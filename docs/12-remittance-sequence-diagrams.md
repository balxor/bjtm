# Remittance Sequence Diagrams dan Control Validation

## 1. Tujuan

Dokumen ini memetakan urutan interaksi antar-komponen dari pembuatan transaksi sampai rekonsiliasi. Sequence adalah arsitektur referensi. Sesuaikan nama endpoint, service, event, state, dan policy dengan desain aktual sebelum menggunakan diagram sebagai test oracle.

Semua test memakai akun dan data sintetis, serta dilakukan pada environment yang tercantum dalam Rules of Engagement (RoE). Alur yang dapat mengirim instruksi ke payment rail harus menggunakan sandbox atau kontrol non-settlement yang disetujui pemilik sistem.

## 2. Sequence A: Create → Validate → Approve → Submit

### Tujuan
Memeriksa apakah kontrol server-side dijalankan sebelum transaksi masuk ke proses pengiriman.

~~~mermaid
sequenceDiagram
    autonumber
    actor Maker as Maker / Customer
    participant API as API Gateway
    participant IAM as IAM / Policy
    participant ORCH as Remittance API
    participant RULE as Limit / Fee / FX
    participant BEN as Beneficiary Service
    participant WF as Approval Workflow
    participant DB as Transaction Store
    participant AUD as Audit Service
    participant ADP as Payment Adapter

    Maker->>API: POST transaction (synthetic input)
    API->>IAM: Validate identity, token, role
    IAM-->>API: Valid identity and claims
    API->>ORCH: Forward authorized request
    ORCH->>BEN: Validate beneficiary ownership and status
    BEN-->>ORCH: Ownership/status result
    ORCH->>RULE: Validate amount, currency, fee, quote, limits
    RULE-->>ORCH: Approved/rejected rule result
    ORCH->>DB: Create transaction in initial state
    DB-->>ORCH: transaction_id, version, state
    ORCH->>AUD: Record create and validation outcome
    ORCH-->>API: Transaction ID and current state
    API-->>Maker: Response

    opt Approval required
        Maker->>API: Submit for approval
        API->>WF: Request transition to PENDING_APPROVAL
        WF->>DB: Check current state and transaction version
        DB-->>WF: State and version
        WF->>AUD: Record maker submission
        WF-->>API: Approval pending
        API-->>Maker: Approval status
    end

    actor Checker as Checker
    Checker->>API: Approve transaction
    API->>IAM: Validate checker identity and role
    IAM-->>API: Checker permitted
    API->>WF: Approve transaction_id and version
    WF->>DB: Verify state, version, SoD, and approval policy
    DB-->>WF: Current state/version
    WF->>DB: Store approval bound to approved version
    WF->>AUD: Record checker, action, version, decision
    WF-->>API: Approved
    API-->>Checker: Approval result

    Maker->>API: Submit approved transaction
    API->>ORCH: Submit transaction_id and idempotency reference
    ORCH->>DB: Verify state, version, approval and submit eligibility
    DB-->>ORCH: Eligible or rejected
    alt Eligible
        ORCH->>ADP: Dispatch to sandbox connector
        ADP-->>ORCH: Accepted / pending reference
        ORCH->>DB: Persist submission and downstream reference
        ORCH->>AUD: Record dispatch result
        ORCH-->>API: Submitted / pending
    else State or approval invalid
        ORCH->>AUD: Record rejected transition
        ORCH-->>API: Reject submission
    end
    API-->>Maker: Submit result
~~~

### Kontrol yang harus diperiksa

- Identity dan function authorization diperiksa pada API serta fungsi sensitif di service.
- Ownership beneficiary diperiksa oleh service yang berwenang, bukan hanya UI.
- Limit, fee, FX, dan atribut transaksi penting divalidasi server-side.
- Setiap state transition memeriksa current state.
- Approval terikat pada transaction version atau digest atribut kritis sesuai desain.
- Submit memeriksa ulang approval, state, limit/rule result yang harus tetap berlaku, serta idempotency reference.
- Audit event mencakup actor, action, object, timestamp, outcome, dan correlation ID.

### Test oracle

**PASS:** transaksi mengikuti state machine yang ditetapkan; checker tidak dapat approve jika role/policy melarang; perubahan atribut kritis tidak dapat menggunakan approval lama ketika approval ulang diwajibkan.

**FAIL:** service menerima transition yang tidak valid, menggunakan approval untuk versi transaksi berbeda, atau melakukan dispatch meskipun prasyarat wajib tidak terpenuhi.

## 3. Sequence B: Perubahan atribut setelah approval

### Risiko
Approval dapat tidak lagi mewakili instruksi yang dikirim jika perubahan pada amount, currency, beneficiary, fee, atau tujuan transaksi tidak menginvalidasi approval sesuai aturan.

~~~mermaid
sequenceDiagram
    autonumber
    actor M as Maker
    actor C as Checker
    participant API as Remittance API
    participant WF as Approval Service
    participant DB as Transaction Store
    participant ADP as Payment Adapter
    participant AUD as Audit Service

    M->>API: Create synthetic transaction T
    API->>DB: Persist version v1 and critical fields
    DB-->>API: T, v1, DRAFT
    M->>API: Submit T for approval
    API->>WF: Request approval for T/v1
    WF->>DB: Read T/v1 and current state
    DB-->>WF: T/v1, PENDING_APPROVAL
    C->>WF: Approve T/v1
    WF->>DB: Store approval bound to v1
    WF->>AUD: Record approval(T, v1, checker)

    Note over M,DB: Execute only in non-settlement test environment
    M->>API: Use approved test flow to update a critical field
    API->>DB: Validate update policy and persist new version if allowed
    DB-->>API: T/v2 or update rejected
    API->>AUD: Record update attempt and outcome

    M->>API: Submit T
    API->>WF: Validate approval binding against current version
    WF->>DB: Read current version and approval record
    DB-->>WF: Current version v2, approval for v1
    alt Re-approval required
        WF-->>API: Reject or require new approval
        API->>AUD: Record stale approval rejection
        API-->>M: Not eligible for dispatch
    else Policy permits the unchanged approved version
        WF-->>API: Approval still valid by policy
        API->>ADP: Dispatch only if every submit condition passes
        API->>AUD: Record submit decision
    end
~~~

### Prosedur validasi

1. Buat transaksi sintetis melalui workflow normal.
2. Simpan versi transaksi dan nilai atribut kritis sebelum approval.
3. Lakukan approval dengan account checker yang terpisah.
4. Pada test transaction yang tidak dapat settlement, lakukan perubahan melalui fungsi uji yang disetujui.
5. Periksa apakah perubahan ditolak, versi berubah dan approval diinvalidasi, atau approval ulang diwajibkan sesuai policy.
6. Jalankan submit hanya pada sandbox dan hanya untuk memverifikasi keputusan eligibility.
7. Periksa audit event serta bukti bahwa tidak terjadi dispatch ketika approval sudah tidak valid.

Jangan menggunakan perubahan ke ledger atau payment rail produksi untuk menguji skenario ini.

## 4. Sequence C: Timeout, retry, dan idempotency

### Risiko
Client atau service dapat melakukan retry setelah timeout tanpa mengetahui apakah downstream telah memproses request. Kontrol harus mencegah satu logical instruction menghasilkan lebih dari satu efek bisnis.

~~~mermaid
sequenceDiagram
    autonumber
    participant ORCH as Remittance Orchestrator
    participant IDEM as Idempotency / Transaction Store
    participant ADP as Payment Adapter
    participant MOCK as Payment Sandbox / Mock
    participant AUD as Audit Service

    ORCH->>IDEM: Reserve idempotency key K for logical transaction T
    IDEM-->>ORCH: New key / existing result
    alt Key is new
        ORCH->>ADP: Submit T with business reference R
        ADP->>MOCK: Dispatch T/R
        MOCK-->>ADP: Accepted but response times out
        ADP-->>ORCH: Timeout / outcome unknown
        ORCH->>IDEM: Persist pending/unknown outcome and retry policy
        ORCH->>AUD: Record timeout, correlation ID, attempt number
        ORCH->>IDEM: Controlled retry for key K
        IDEM-->>ORCH: Existing transaction T and retry eligibility
        ORCH->>ADP: Retry same logical instruction under policy
        ADP->>MOCK: Retry with stable reference / idempotency identity
        MOCK-->>ADP: Existing result or deduplicated result
        ADP-->>ORCH: Consistent downstream result
        ORCH->>IDEM: Persist single logical outcome
        ORCH->>AUD: Record retry and deduplication
    else Key already exists
        IDEM-->>ORCH: Return stored status/result
        ORCH->>AUD: Record duplicate submission
    end
~~~

### Prosedur validasi

1. Gunakan payment mock/sandbox dengan satu transaksi sintetis.
2. Tetapkan idempotency key dan business reference.
3. Simulasikan timeout setelah downstream menerima request menggunakan fixture yang disediakan, tanpa memutus layanan produksi.
4. Jalankan retry terbatas sesuai RoE.
5. Bandingkan logical transaction count, dispatch count, sandbox posting, state, dan audit record.
6. Periksa respons terhadap duplicate submission serta perilaku ketika idempotency key telah digunakan.

### Test oracle

**PASS:** satu logical transaction menghasilkan maksimal satu financial effect; retry menghasilkan hasil konsisten atau status pending yang eksplisit.

**FAIL:** satu instruksi menyebabkan dispatch/posting ganda, atau sistem melaporkan success padahal hasil downstream tidak diketahui tanpa mekanisme rekonsiliasi.

## 5. Sequence D: Callback, status update, dan reconciliation

~~~mermaid
sequenceDiagram
    autonumber
    participant PARTNER as Payment Partner Simulator
    participant CB as Callback Gateway
    participant VAL as Signature / Event Validator
    participant ORCH as Remittance Orchestrator
    participant DB as Transaction Store
    participant LEDGER as Ledger Sandbox
    participant REC as Reconciliation
    participant AUD as Audit / SIEM

    PARTNER->>CB: Send status event E for synthetic transaction T
    CB->>VAL: Validate peer, signature, timestamp, event ID, schema
    alt Authentic event and valid transition
        VAL->>ORCH: Validated event E/T
        ORCH->>DB: Check current state, event dedupe, correlation
        DB-->>ORCH: Current state and event status
        alt Event is new and transition allowed
            ORCH->>DB: Persist new transaction status
            ORCH->>LEDGER: Apply or verify sandbox posting if required by design
            LEDGER-->>ORCH: Posting reference / status
            ORCH->>REC: Submit status and references for reconciliation
            REC-->>ORCH: Matched or exception
            ORCH->>AUD: Record event, state transition, and correlation IDs
        else Duplicate or out-of-order event
            ORCH->>AUD: Record duplicate/rejected event
        end
    else Invalid signature, peer, schema, or freshness
        VAL->>AUD: Record callback validation failure
        VAL-->>CB: Reject event
    end
~~~

### Prosedur validasi

1. Gunakan callback simulator dan transaksi sintetis.
2. Rekam baseline event yang valid beserta state sebelum/sesudah.
3. Jalankan event negatif dari simulator untuk signature/authentication metadata yang invalid.
4. Kirim ulang event yang sama untuk menguji deduplication.
5. Uji event out-of-order hanya bila tersedia fixture dan state aman.
6. Cocokkan state API, transaction store, ledger sandbox, reconciliation record, dan audit event.

### Test oracle

**PASS:** event tidak valid ditolak; duplicate tidak menimbulkan efek tambahan; event out-of-order ditangani sesuai state machine; status dan posting dapat direkonsiliasi.

**FAIL:** callback yang tidak valid mengubah state, event duplikat menimbulkan efek baru, atau mismatch tidak masuk ke exception handling sesuai policy.

## 6. Sequence E: End-to-end reconciliation

~~~mermaid
sequenceDiagram
    autonumber
    participant API as Remittance API
    participant TX as Transaction Store
    participant ADP as Payment Adapter
    participant EXT as Partner Simulator
    participant LEDGER as Ledger Sandbox
    participant REC as Reconciliation
    participant OPS as Exception Operator
    participant AUD as Audit Service

    API->>TX: Persist transaction T with correlation C
    API->>ADP: Submit T/C
    ADP->>EXT: Dispatch T with external reference R
    EXT-->>ADP: Status and R
    ADP->>TX: Record downstream status and R
    ADP->>LEDGER: Post/verify according to integration contract
    LEDGER-->>ADP: Ledger reference L
    ADP->>REC: Send T, R, L, amount/currency and status
    REC->>TX: Read internal state
    REC->>LEDGER: Read sandbox posting
    alt All references and values match
        REC->>AUD: Record reconciliation match
    else Mismatch / pending / missing record
        REC->>AUD: Record discrepancy and correlation
        REC-->>OPS: Create exception item
        OPS->>REC: Review using authorized role
        OPS->>AUD: Record review and approved disposition
    end
~~~

### Hal yang harus cocok

- Internal transaction ID.
- External partner reference.
- Ledger posting reference jika sistem menggunakan ledger terpisah.
- Amount, currency, fee, dan total sesuai tahap proses.
- Status dan timestamp.
- Correlation ID.
- Hasil approval, submit, callback, dan reconciliation.
- Identitas operator serta approval untuk koreksi atau reversal.

## 7. Checklist review sequence

Gunakan checklist saat membandingkan diagram dengan implementasi.

- [ ] API dan service memvalidasi identity, role, dan object ownership.
- [ ] Critical properties tidak dipercaya hanya karena berasal dari client.
- [ ] Validation, limits, dan business rules terjadi sebelum submit.
- [ ] Approval terikat pada versi transaksi yang disetujui.
- [ ] State transition memeriksa current state dan prasyarat.
- [ ] Idempotency key dan business reference tetap konsisten pada retry.
- [ ] Adapter menangani timeout tanpa mengasumsikan success atau failure secara keliru.
- [ ] Callback divalidasi autentisitas, freshness, schema, event ID, dan state.
- [ ] Duplicate dan out-of-order event ditangani secara eksplisit.
- [ ] Ledger dan reconciliation dapat mengidentifikasi mismatch.
- [ ] Audit event mencatat actor, action, object, outcome, timestamp, correlation ID.
- [ ] Notification hanya menyampaikan status yang konsisten dengan sumber status yang otoritatif.
- [ ] Cleanup dan retest criteria ditentukan.

## 8. Mapping ke attack-path matrix

| Sequence | Attack-path mapping | Test catalog |
|---|---|---|
| Create → Validate → Approve → Submit | AP-02, AP-03, AP-07, AP-11 | RT-TXN-01, RT-AUTH-02, RT-WF-01, RT-FX-01 |
| Change after approval | AP-03 | RT-WF-01, RT-TXN-01 |
| Timeout → Retry → Idempotency | AP-04, AP-08 | RT-IDEM-01, RT-INT-01 |
| Callback → State update | AP-05 | RT-CB-01 |
| Status → Ledger → Reconciliation | AP-08, AP-12 | RT-REC-01, RT-LOG-01 |

## 9. Referensi teknis

- [OWASP API Security Top 10 2023](https://api-security.owasp.org/editions/2023/en/0x11-t10/)
- [OWASP Web Security Testing Guide: Business Logic Testing](https://wstg.org/stable/4-Web_Application_Security_Testing/10-Business_Logic_Testing/)
- [OWASP ASVS 5.0.0](https://asvs.owasp.org/)

Catat requirement identifier dan acceptance criteria yang dipakai pada test-case register.
