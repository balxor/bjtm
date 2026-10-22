# Remittance Threat Model

## 1. Tujuan dan cakupan teknis

Dokumen ini digunakan untuk memetakan risiko keamanan pada sistem remittance, menentukan lokasi kontrol yang perlu dievaluasi, serta menyusun test case untuk PoC penetration testing.

Model mencakup channel, API, identity, beneficiary management, transaction orchestration, limit dan fee calculation, approval workflow, fraud/compliance services bila digunakan, payment connector, callback/webhook, core banking atau ledger, reconciliation, notification, dan audit logging.

Gunakan model ini hanya jika aplikasi yang ditetapkan Bank Jatim terkait remittance. Dokumen jadwal PoC VAPT menyebut satu aplikasi uji, tetapi belum mengidentifikasi nama aplikasi atau arsitektur teknisnya. Aset dan komponen aktual harus mengikuti scope yang diberikan Bank.

Gunakan komponen yang memang ada dalam arsitektur engagement. Tandai komponen lain sebagai not applicable. Pemilihan test case harus mengikuti scope, Rules of Engagement (RoE), environment, dan batas transaksi yang telah ditetapkan.

## 2. Arsitektur remittance referensi

Diagram berikut menunjukkan arsitektur logis generik untuk remittance. Sistem aktual dapat menggabungkan, memisahkan, atau mengganti komponen sesuai desain implementasi.

~~~mermaid
flowchart LR
    C[Customer / Corporate User] --> CH[Web / Mobile / Branch Channel]
    CH --> EDGE[WAF / API Gateway]
    EDGE --> IAM[Identity / Session / Policy]
    IAM --> ORCH[Remittance API / Orchestrator]

    ORCH --> BEN[Customer & Beneficiary Service]
    ORCH --> RULE[Limit / Fee / FX Rules]
    ORCH --> RISK[Fraud / Compliance Screening]
    ORCH --> WF[Approval Workflow]
    ORCH --> TX[(Transaction State Store)]

    WF --> TX
    WF --> CORE[Core Banking / Ledger]
    ORCH --> CONN[Payment Connector / Adapter]
    CONN --> RAIL[Payment Network / Remittance Partner]
    RAIL --> CB[Inbound Callback / Status API]
    CB --> ORCH

    CORE --> REC[Reconciliation / Exception Queue]
    CONN --> REC
    ORCH --> EVT[Audit Events / Security Logs]
    IAM --> EVT
    WF --> EVT
    CONN --> EVT
    CB --> EVT
    EVT --> SIEM[SIEM / Monitoring]
    ORCH --> NOTIF[Notification Service]
    NOTIF --> C

    classDef boundary fill:#eef5ff,stroke:#4778b8,color:#111;
    classDef data fill:#f3f4f6,stroke:#555,color:#111;
    class EDGE,IAM,ORCH,WF,CONN,CB boundary;
    class TX,CORE,REC,EVT data;
~~~

### 2.1 Fungsi komponen dan area pemeriksaan

| ID | Komponen | Tanggung jawab utama | Kontrol yang perlu diperiksa |
|---|---|---|---|
| C-01 | Channel | Mengumpulkan instruksi dari nasabah atau corporate user | Session handling, anti-CSRF bila relevan, validasi input client, tidak mengandalkan validasi client sebagai kontrol keamanan |
| C-02 | WAF / API gateway | Routing, TLS termination, API policy, request limits | Route inventory, authentication enforcement, size/rate limits, header normalization, pembatasan endpoint administratif |
| C-03 | IAM / policy | Login, token, session, role, policy | Token validation, issuer/audience/expiry, revocation, role and tenant boundaries, service identity |
| C-04 | Customer / beneficiary service | Data penerima dan relasi pemilik | Object-level authorization, ownership validation, perubahan rekening penerima, audit perubahan |
| C-05 | Remittance API / orchestrator | Membuat transaksi dan mengatur urutan proses | Server-side authorization, schema validation, state machine, idempotency, correlation ID |
| C-06 | Limit / fee / FX rules | Menghitung biaya, nilai tukar, limit, dan total | Sumber nilai otoritatif, konsistensi quote dan commit, rounding, expiry, atomic limit enforcement |
| C-07 | Fraud / compliance screening | Menjalankan pemeriksaan sesuai konfigurasi produk | Fail-open/fail-closed policy, keterkaitan hasil screening dengan transaksi, penanganan timeout dan exception |
| C-08 | Approval workflow | Maker-checker atau proses approval lain | Separation of duties, binding approval ke detail transaksi, role enforcement, state transition |
| C-09 | Transaction state store | Menyimpan instruksi dan status transaksi | Integritas referensi, concurrency control, constraint unik, transaction consistency, akses database |
| C-10 | Payment connector / adapter | Mengirim instruksi ke partner atau payment rail | Service authentication, message validation, timeout/retry design, idempotency, secret handling |
| C-11 | Callback / status API | Menerima status dari partner atau downstream service | Authenticity, signature validation, replay protection, event ordering, schema validation |
| C-12 | Core banking / ledger | Mencatat posting dan posisi finansial | Authorization, atomic posting, reference uniqueness, reversal controls, reconciliation |
| C-13 | Reconciliation / exception queue | Membandingkan state internal dengan status downstream | Deteksi mismatch, exception handling, controlled repair, maker-checker untuk koreksi |
| C-14 | Notification | Menyampaikan status kepada pengguna | Data minimization, recipient binding, escaping, tidak menjadikan notifikasi sebagai sumber status otoritatif |
| C-15 | Audit / SIEM | Merekam tindakan serta mendeteksi perilaku yang relevan | Actor, action, object ID, timestamp, correlation ID, hasil kontrol, akses dan retensi log |

## 3. Trust boundaries

| Boundary | Arus yang melewati boundary | Risiko utama | Pengujian yang disarankan |
|---|---|---|---|
| TB-01: Client ke edge | Browser/mobile/branch ke WAF dan gateway | Manipulasi request, session abuse, API exposure | Bandingkan validasi client dan server, periksa enforcement gateway |
| TB-02: Edge ke identity dan API | Token, claims, headers, API calls | Token validation lemah, role confusion, route yang tidak dilindungi | Uji token invalid/expired/revoked dan matriks role per endpoint |
| TB-03: Antar-service domain | Customer, beneficiary, rule engine, orchestration | Trust terhadap caller, object ownership hilang, property assignment tidak aman | Periksa identity service-to-service dan authorization pada setiap operasi |
| TB-04: Transaction ke approval | Instruksi transaksi ke workflow approval | Self-approval, approval pada detail yang berubah, transisi state tidak sah | Gunakan akun maker/checker terpisah dan transaksi sintetis |
| TB-05: Internal ke payment partner | Instruksi pembayaran keluar dan respons downstream | Message tampering, retry ganda, credential service terbuka | Gunakan sandbox connector dan test fixture yang disetujui |
| TB-06: Partner ke callback | Callback/webhook/status update masuk | Callback palsu, replay, event terlambat atau duplikat | Gunakan simulator callback yang dikendalikan tim atau client |
| TB-07: Application ke ledger dan reconciliation | Posting, status, reversal, exception | Status tidak konsisten, posting ganda, koreksi tanpa kontrol | Rekonsiliasi transaction ID sintetis pada seluruh komponen terkait |
| TB-08: Application ke log dan export | Event, log, report, file export | Data sensitif di log, log tidak lengkap, akses report berlebihan | Periksa event, akses log/export, redaction, serta korelasi end-to-end |

## 4. Invariant keamanan dan integritas transaksi

Pengujian harus memeriksa apakah aturan berikut ditegakkan pada sisi server dan antarkomponen.

1. **Identity invariant:** setiap operasi dijalankan oleh identity yang valid dengan audience, scope, role, dan session state yang sesuai.
2. **Object ownership invariant:** pengguna hanya dapat membaca atau mengubah objek yang diizinkan untuk dirinya, tenant, atau perannya.
3. **Property invariant:** field yang mengendalikan owner, status, approval, fee, limit, atau hasil screening tidak dapat diubah melalui input yang tidak berwenang.
4. **Transaction binding invariant:** approval berlaku untuk versi instruksi yang disetujui, termasuk atribut kritis seperti beneficiary, amount, currency, fee, dan tujuan pembayaran.
5. **State transition invariant:** status transaksi hanya berubah melalui transisi yang diizinkan.
6. **Idempotency invariant:** pengulangan request yang mewakili instruksi bisnis sama tidak menghasilkan efek finansial kedua.
7. **Limit invariant:** limit individual, harian, per-beneficiary, dan total diterapkan secara konsisten serta aman terhadap request bersamaan.
8. **Callback invariant:** event downstream hanya diterima dari sumber yang terautentikasi, divalidasi, dan diproses sesuai aturan duplikasi serta urutan event.
9. **Ledger consistency invariant:** posting, status transaksi, callback, dan rekonsiliasi dapat dikorelasikan dengan identifier yang konsisten.
10. **Audit invariant:** tindakan penting memiliki actor, timestamp, object reference, correlation ID, outcome, dan sumber event yang memadai.

## 5. Prioritas hipotesis vulnerability

Prioritas di bawah adalah prioritas awal berdasarkan dampak potensial pada sistem remittance. Sesuaikan dengan desain, eksposur, kontrol kompensasi, dan kriteria severity dalam engagement.

| ID | Prioritas awal | Lokasi | Hipotesis flaw | Dampak yang dinilai |
|---|---|---|---|---|
| RM-AUTH-01 | P1 | Beneficiary, transaction, status/detail API | Broken Object Level Authorization (BOLA/IDOR) | Akses atau perubahan objek milik pengguna lain |
| RM-AUTH-02 | P1 | Approval API dan admin/support functions | Broken Function Level Authorization (BFLA) atau pemisahan tugas lemah | Operasi approval/admin dilakukan oleh role yang tidak sesuai |
| RM-TXN-01 | P1 | Create/update transaction API | Properti kritis diterima dari client tanpa validasi otoritatif di server | Perubahan beneficiary, amount, currency, fee, owner, atau status |
| RM-WF-01 | P1 | Transaction state machine dan approval | Transisi status tidak valid atau approval tidak terikat pada detail transaksi | Transaksi melewati validasi atau approval yang diwajibkan |
| RM-REPLAY-01 | P1 | Submit endpoint dan payment connector | Idempotency atau duplicate handling tidak konsisten | Instruksi yang sama menghasilkan pemrosesan lebih dari sekali |
| RM-CB-01 | P1 | Callback/webhook/status ingestion | Autentisitas sumber, proteksi replay, atau aturan urutan event lemah | Status transaksi dapat dipengaruhi event yang tidak valid |
| RM-LIM-01 | P1/P2 | Limit engine dan transaction commit | Limit tidak diterapkan atomik pada request bersamaan atau lintas channel | Akumulasi transaksi dapat melewati kebijakan limit |
| RM-FX-01 | P2 | Quote, fee, FX, transaction confirmation | Nilai quote, rounding, expiry, fee, atau total berbeda antar tahap | Nilai transaksi tidak sesuai persetujuan pengguna atau aturan |
| RM-IAM-01 | P1/P2 | IAM, token, session, service identity | Token/session tidak divalidasi lengkap atau privilege tidak dicabut | Akses setelah expiry/revocation atau akses lintas role |
| RM-INT-01 | P1/P2 | Payment adapter dan downstream integrations | Retry, timeout, trust, atau message validation tidak konsisten | Status ambigu, instruksi ganda, atau respons yang diproses tanpa validasi |
| RM-REC-01 | P1/P2 | Ledger, reconciliation, exception repair | Mismatch status atau posting tidak terdeteksi; koreksi tidak memiliki dual control | Ketidaksesuaian catatan transaksi dan pembukuan |
| RM-DATA-01 | P2 | API response, export, notification, error | Data property berlebih, PII pada log, atau akses report berlebihan | Paparan data pelanggan atau instruksi transaksi |
| RM-LOG-01 | P2 | Audit event, application log, SIEM | Peristiwa penting tidak lengkap atau tidak dapat dikorelasikan | Deteksi, investigasi, dan audit transaksi terganggu |
| RM-API-01 | P2 | Gateway, public/internal API inventory | Endpoint lama, konfigurasi lemah, atau route tidak dilindungi | Surface yang tidak tercakup oleh policy yang seharusnya |
| RM-ABUSE-01 | P2 | Sensitive business flows | Tidak ada pembatasan penggunaan fungsi sensitif yang sesuai risiko | Enumerasi, automation abuse, atau peningkatan biaya/operasi |

P1 berarti kandidat prioritas PoC yang perlu dievaluasi lebih awal; ini bukan severity finding. Severity final ditentukan berdasarkan hasil test, impact, exploit preconditions, exposure, dan rubric engagement.

## 6. Metode pengujian per skenario

Semua pengujian di bagian ini dilakukan hanya terhadap aset dan akun yang tercantum dalam scope. Gunakan environment nonproduksi, akun uji terpisah, serta data transaksi sintetis. Pengujian yang menyentuh posting atau payment rail harus memakai jalur sandbox dengan settlement dana nyata dinonaktifkan atau pengamanan lain yang secara tertulis disetujui pemilik sistem.

### RM-AUTH-01: Object-level authorization

**Objektif:** memastikan kontrol ownership diterapkan pada setiap operasi yang membaca atau mengubah objek remittance.

**Persiapan**
- Buat dua akun uji dengan ownership berbeda.
- Buat beneficiary dan transaksi sintetis untuk masing-masing akun.
- Tentukan operasi baca, update, dan status yang termasuk scope.

**Prosedur**
1. Jalankan operasi normal sebagai pemilik objek untuk mendapatkan baseline.
2. Ulangi operasi baca terhadap objek akun lain menggunakan akun uji yang tidak memiliki akses.
3. Untuk operasi perubahan, gunakan field nonfinansial atau objek disposable sesuai RoE.
4. Periksa respons aplikasi dan pastikan tidak ada perubahan pada state objek.
5. Periksa log akses yang tersedia untuk memastikan actor dan hasil penolakan tercatat.

**Expected secure behavior**
- Permintaan ditolak dengan respons yang tidak membocorkan data objek.
- State objek tidak berubah.
- Pemeriksaan ownership berjalan di server untuk setiap operasi, termasuk operasi yang memakai identifier dari request.

**Evidence**
- Test ID, role akun, ID sintetis, timestamp, respons tersanitasi, state sebelum/sesudah, dan log terkait.

### RM-AUTH-02: Function authorization dan maker-checker

**Objektif:** memastikan fungsi privileged dan approval hanya dapat dilakukan oleh role yang ditetapkan.

**Persiapan**
- Identifikasi role maker, checker, administrator, dan support jika tersedia.
- Siapkan akun uji terpisah dan transaksi sintetis.
- Tetapkan aturan self-approval serta batas fungsi per role dari requirement sistem.

**Prosedur**
1. Catat operasi yang diizinkan untuk tiap role sebagai baseline.
2. Uji akses fungsi approval atau administrasi memakai role yang tidak seharusnya memiliki fungsi tersebut.
3. Uji aturan self-approval dengan akun yang sama hanya jika policy produk mensyaratkan maker dan checker terpisah.
4. Periksa apakah approval tetap valid setelah detail transaksi kritis berubah melalui alur uji yang disetujui.
5. Tinjau hasil dan audit trail dari akun masing-masing.

**Expected secure behavior**
- API dan service menolak fungsi yang tidak diizinkan meskipun request dapat dibentuk dari client.
- Pemisahan tugas dipaksakan di server.
- Approval terikat ke versi dan detail transaksi yang telah disetujui.

### RM-TXN-01: Validasi properti dan integritas instruksi

**Objektif:** memastikan server menghitung atau memvalidasi sendiri atribut yang memengaruhi hak, nilai, biaya, dan status transaksi.

**Field untuk ditinjau**
- Owner/customer/tenant reference.
- Beneficiary reference.
- Amount dan currency.
- Fee, FX quote ID, expiry, dan total debit.
- Transaction status dan approval state.
- Limit, risk decision, atau screening result.
- Idempotency/business reference.

**Prosedur**
1. Gunakan fungsi normal untuk membuat transaksi sintetis dan catat nilai yang dihasilkan server.
2. Uji property yang boleh diedit berdasarkan kontrak API.
3. Untuk field yang dikelola server, lakukan negative test pada test harness atau transaksi sintetis yang tidak dapat diselesaikan ke payment rail.
4. Bandingkan respons, state di backend, nilai perhitungan, dan audit event.
5. Pastikan input tidak sah tidak mengubah atribut terlindungi.

**Expected secure behavior**
- Field yang tidak dapat diedit ditolak atau diabaikan dengan perilaku terdokumentasi.
- Amount, currency, fee, owner, dan status dihitung atau divalidasi terhadap sumber otoritatif.
- Penolakan meninggalkan state transaksi tetap konsisten.

### RM-WF-01: State machine dan approval binding

**Objektif:** memastikan setiap transisi status mengikuti workflow bisnis.

**Prosedur**
1. Dapatkan daftar status transaksi dan transisi yang diizinkan.
2. Jalankan alur normal dengan satu transaksi sintetis untuk mencatat state baseline.
3. Pada transaksi disposable, uji operasi transisi yang berada di luar urutan yang diizinkan melalui interface uji yang disetujui.
4. Uji apakah perubahan pada beneficiary, amount, currency, atau fee setelah approval memerlukan approval ulang.
5. Periksa state di orchestrator, workflow, dan ledger/sandbox sesuai akses yang tersedia.

**Expected secure behavior**
- Transisi yang tidak sah ditolak oleh server.
- Perubahan atribut kritis membatalkan approval lama atau memicu ulang approval sesuai policy.
- State tidak meloncat ke submitted, settled, atau status final tanpa prasyarat yang diwajibkan.
- Tidak terjadi efek eksternal bila transisi ditolak.

### RM-REPLAY-01: Idempotency dan duplicate transaction

**Objektif:** menilai perlakuan sistem terhadap pengulangan request atau retry untuk instruksi bisnis yang sama.

**Persiapan**
- Gunakan simulator atau sandbox payment connector.
- Tentukan idempotency key, business reference, dan kriteria transaksi dianggap duplikat.
- Tetapkan jumlah request minimum dan batas concurrency dalam RoE.

**Prosedur**
1. Kirim satu instruksi sintetis melalui alur normal dan simpan reference hasil.
2. Ulangi request yang sama menggunakan mekanisme retry yang disepakati.
3. Jika diizinkan, uji request bersamaan dalam jumlah kecil pada sandbox.
4. Bandingkan jumlah transaction record, outbound dispatch, ledger posting, dan audit event.
5. Pastikan retry tidak menimbulkan efek eksternal kedua.

**Expected secure behavior**
- Request duplikat menghasilkan respons yang konsisten, conflict, atau referensi ke transaksi yang sudah ada sesuai desain.
- Hanya satu instruksi bisnis diproses untuk idempotency scope yang sama.
- Retry dan duplicate detection tercatat.

### RM-LIM-01: Limit enforcement dan kondisi bersamaan

**Objektif:** memeriksa apakah limit diterapkan secara konsisten dan atomik.

**Prosedur**
1. Minta konfigurasi limit uji dan batas transaksi yang diizinkan.
2. Jalankan transaksi sintetis di bawah limit sebagai baseline.
3. Jalankan satu kasus batas yang disepakati menggunakan konfigurasi sandbox.
4. Jika ada persetujuan concurrency test, jalankan jumlah request kecil yang ditetapkan untuk mengevaluasi kondisi bersamaan.
5. Bandingkan hasil dengan limit engine dan transaction store.

**Expected secure behavior**
- Transaksi yang melampaui kebijakan ditolak sebelum dispatch atau posting.
- Evaluasi limit dan commit transaksi tidak menimbulkan kondisi race.
- Limit diterapkan konsisten lintas channel sesuai aturan produk.

Jangan melakukan stress test atau meningkatkan volume melampaui nilai yang secara eksplisit ditetapkan.

### RM-FX-01: Konsistensi quote, fee, FX, dan total

**Objektif:** menilai konsistensi nilai antara kalkulasi, konfirmasi, approval, dispatch, dan catatan akhir.

**Prosedur**
1. Minta aturan fee, FX, precision, rounding, quote expiry, dan total debit.
2. Buat transaksi sintetis dengan nilai yang disetujui.
3. Bandingkan quote pada saat dibuat dengan nilai saat konfirmasi dan state yang tersimpan.
4. Uji quote yang kedaluwarsa atau berubah menggunakan test fixture yang tersedia.
5. Bandingkan amount, currency, fee, FX rate, total, dan versi quote pada seluruh catatan uji.

**Expected secure behavior**
- Nilai final sesuai quote yang berlaku dan policy yang ditetapkan.
- Quote kedaluwarsa memerlukan refresh atau ditolak sesuai desain.
- Rounding dan fee dihitung secara konsisten serta tidak bergantung pada nilai client yang tidak dipercaya.

### RM-CB-01: Callback/webhook authenticity dan replay

**Objektif:** memastikan status dari partner hanya diterima setelah validasi sumber dan event.

**Persiapan**
- Gunakan callback simulator atau test endpoint yang dikendalikan client.
- Tetapkan mekanisme signature, mutual TLS, timestamp, nonce, event ID, dan aturan urutan event yang digunakan produk.

**Prosedur**
1. Catat perilaku callback valid sebagai baseline.
2. Kirim event negatif dari simulator untuk kasus signature tidak valid atau metadata autentikasi tidak lengkap.
3. Kirim event test yang sama sekali lagi untuk mengevaluasi duplicate handling.
4. Gunakan event berurutan berbeda hanya jika test fixture menyediakan state yang aman untuk itu.
5. Periksa perubahan status, outbound action, dan audit log.

**Expected secure behavior**
- Event tidak valid ditolak tanpa mengubah state transaksi.
- Event duplikat tidak menimbulkan pemrosesan berulang.
- Event yang datang di luar urutan ditangani sesuai state machine.
- Status final hanya berubah setelah pemeriksaan yang diwajibkan selesai.

Jangan memalsukan callback ke sistem partner produksi atau mengirim event ke endpoint yang tidak masuk scope.

### RM-INT-01: Connector, retry, dan timeout

**Objektif:** mengevaluasi autentikasi service-to-service, validasi pesan, serta konsistensi saat downstream mengalami timeout atau error.

**Prosedur**
1. Review kontrak API, identitas service, metode autentikasi, dan secret storage yang digunakan.
2. Gunakan sandbox connector atau mock server untuk mensimulasikan respons sukses, timeout, dan error.
3. Amati keputusan retry, timeout, circuit breaker, serta penanganan status transaksi.
4. Periksa apakah retry memakai idempotency/reference yang sesuai.
5. Pastikan kegagalan downstream tidak menyebabkan status final yang salah atau dispatch kedua.

**Expected secure behavior**
- Service hanya mempercayai identitas dan pesan dari peer yang diizinkan.
- Timeout menghasilkan status yang eksplisit dan dapat direkonsiliasi.
- Retry bersifat bounded dan tidak menyebabkan dispatch atau posting duplikat.
- Kegagalan validasi tidak ditangani dengan fallback yang membuka akses tanpa kontrol.

### RM-REC-01: Reconciliation dan ledger consistency

**Objektif:** memastikan transaksi dapat dilacak dari instruksi sampai status downstream, posting, dan rekonsiliasi.

**Prosedur**
1. Buat satu transaksi sintetis melalui alur sandbox normal.
2. Catat transaction ID, reference downstream, ledger reference, correlation ID, dan status.
3. Gunakan simulator untuk skenario sukses, pending, timeout, atau exception yang disediakan.
4. Bandingkan state dari orchestrator, connector, callback, ledger/sandbox, dan reconciliation record.
5. Periksa mekanisme exception queue dan otorisasi untuk koreksi atau reversal.

**Expected secure behavior**
- Status yang belum pasti direpresentasikan secara eksplisit.
- Mismatch masuk ke exception handling dan dapat ditelusuri.
- Reprocessing, reversal, dan koreksi memiliki kontrol otorisasi dan audit trail.
- Tidak ada perubahan ledger langsung oleh akun yang tidak berwenang.

### RM-IAM-01: Token, session, dan service identity

**Objektif:** memeriksa lifecycle authentication dan enforcement privilege.

**Prosedur**
1. Uji login dan operasi normal menggunakan akun uji.
2. Uji request dengan token expired, revoked, malformed, atau scope yang tidak memadai sesuai test plan.
3. Periksa perubahan role, logout, atau revocation sesuai fitur yang tersedia.
4. Review pembatasan service identity dan secret storage untuk koneksi internal.
5. Periksa apakah request ditolak di gateway dan service yang mengelola fungsi sensitif.

**Expected secure behavior**
- Token divalidasi terhadap issuer, audience, expiry, signature, dan policy yang berlaku.
- Token yang tidak valid atau dicabut tidak dapat menjalankan fungsi terlindungi.
- Service identity menggunakan privilege minimum dan secret tidak dicatat pada log.

### RM-DATA-01 dan RM-LOG-01: Data exposure dan auditability

**Objektif:** memeriksa minimisasi informasi pada response/log serta kelengkapan catatan aktivitas penting.

**Prosedur**
1. Gunakan akun dan transaksi sintetis untuk memicu operasi normal dan operasi yang ditolak.
2. Periksa response API, error, report, export, notification, dan log yang tersedia dalam scope.
3. Pastikan hanya role yang tepat dapat mengakses data transaksi dan report.
4. Cocokkan aksi, actor, object ID, timestamp, correlation ID, perubahan state, dan outcome pada audit events.
5. Periksa apakah token, credential, atau data sensitif masuk ke log atau pesan error.

**Expected secure behavior**
- Respons hanya mengembalikan properti yang dibutuhkan oleh role pemanggil.
- Error tidak mengekspos secret, data pribadi, query, atau detail internal yang tidak perlu.
- Event transaksi dan security event dapat dikorelasikan tanpa menyimpan secret.
- Hak akses dan retensi log mengikuti kebijakan yang ditetapkan.

## 7. Matriks hasil, evidence, dan kriteria pass/fail

| Field | Isi minimum |
|---|---|
| Test ID | Identifier unik dan tetap |
| Hypothesis | Kondisi kontrol yang diuji |
| Preconditions | Akun, data sintetis, environment, dan state awal |
| Test action | Langkah yang dilaksanakan sesuai RoE |
| Expected behavior | Respons atau state yang diharapkan |
| Actual observation | Perilaku yang diamati |
| State integrity | State sebelum/sesudah dan referensi transaksi |
| Evidence | Request/response tersanitasi, audit event, timestamp, correlation ID |
| Result | PASS / FAIL / INCONCLUSIVE / BLOCKED / NOT TESTED |
| Business impact | Dampak yang didukung observasi |
| Cleanup | Pemulihan objek dan state uji |
| Retest criteria | Kondisi untuk menilai perbaikan |

**PASS:** kontrol berperilaku sesuai kriteria pada kondisi uji.  
**FAIL:** evidence menunjukkan kontrol tidak memenuhi kriteria.  
**INCONCLUSIVE:** evidence atau akses tidak cukup untuk menyimpulkan hasil.  
**BLOCKED:** prasyarat atau izin belum tersedia.  
**NOT TESTED:** test tidak dijalankan dan alasannya dicatat.

Setiap hasil harus memuat batasan test. Kegagalan satu skenario tidak boleh digeneralisasi ke seluruh sistem tanpa pengujian tambahan.

## 8. Kandidat skenario untuk demonstrasi PoC

Pilih tiga sampai lima skenario sesuai waktu dan scope. Kombinasi yang biasanya memberi cakupan risiko transaksi yang baik adalah:

1. **Authorization boundary:** akses objek antar-akun atau antar-role menggunakan data sintetis.
2. **Transaction integrity:** validasi properti dan perhitungan server-side.
3. **Approval/state machine:** pemisahan tugas dan binding approval ke instruksi transaksi.
4. **Idempotency/replay:** satu instruksi sintetis tidak menimbulkan efek ganda pada connector sandbox.
5. **Callback/reconciliation:** event downstream, status transaksi, ledger, dan audit trail konsisten.

Untuk setiap skenario, demonstrasi perlu menunjukkan tujuan bisnis, kontrol yang diuji, precondition, test action, expected behavior, hasil aktual, evidence, dampak, rekomendasi, dan retest criteria.

## 9. Remediation map

| Temuan potensial | Kontrol teknis yang biasanya relevan |
|---|---|
| BOLA / IDOR | Server-side object authorization, ownership check per operasi, negative tests lintas akun |
| BFLA / role bypass | Deny-by-default policy, centralized authorization, server-side role matrix |
| Property tampering | DTO/schema allowlist, server-owned field protection, authoritative calculation |
| Approval bypass | State machine eksplisit, maker-checker enforcement, approval binding ke transaction version |
| Duplicate/replay | Idempotency key yang memiliki scope, unique business reference, atomic processing, downstream deduplication |
| Race pada limit | Atomic reservation/commit, transaksi database yang sesuai, concurrency tests yang bounded |
| Callback spoof/replay | Peer authentication, signature verification, timestamp/nonce/event ID validation, idempotent processing |
| Quote/fee mismatch | Quote version, expiry validation, server-side fee/FX calculation, decimal precision policy |
| Reconciliation mismatch | Correlation ID end-to-end, exception queue, dual control untuk repair/reversal, reconciliation alerts |
| Sensitive log/data | Data minimization, redaction, response schema, RBAC dan retensi pada log/export |

Remediasi final harus mengikuti hasil root-cause analysis dan desain aplikasi.

## 10. Guardrail pengujian

- Gunakan aset, endpoint, akun, role, dan metode yang tercantum dalam RoE.
- Prioritaskan sandbox/UAT dengan synthetic customer, beneficiary, dan transaction data.
- Jangan mengirim instruksi ke payment rail produksi untuk menguji duplikasi, replay, state transition, atau limit.
- Hindari stress testing; concurrency test harus mempunyai batas kecil yang disetujui.
- Jangan mengubah ledger, database produksi, atau status finansial secara langsung.
- Hentikan test jika objek di luar populasi uji terdampak, state menjadi tidak konsisten, atau ada risiko pemindahan dana.
- Simpan evidence dengan kontrol akses, redaction, dan retensi yang ditentukan engagement.
- Catat setiap test yang dibatasi oleh izin, keterbatasan environment, atau ketiadaan simulator.

## 11. Referensi teknis

Gunakan OWASP Top 10:2025 untuk klasifikasi risiko aplikasi web, OWASP API Security Top 10:2023 sebagai acuan pelengkap khusus API, serta standar verifikasi dan testing sesuai teknologi dan scope engagement.

- [OWASP Top 10:2025](https://top10.owasp.org/2025/): klasifikasi risiko aplikasi web.
- [OWASP API Security Top 10:2023](https://api-security.owasp.org/editions/2023/en/0x11-t10/): authorization, authentication, property-level controls, resource consumption, sensitive business flows, API inventory, dan unsafe consumption of APIs.
- [OWASP Application Security Verification Standard (ASVS) 5.0.0](https://asvs.owasp.org/): requirement verifikasi keamanan aplikasi web dan service.
- [OWASP Web Security Testing Guide 4.2: Business Logic Testing](https://wstg.owasp.org/stable/4-Web_Application_Security_Testing/10-Business_Logic_Testing/): data validation, request forgery, integrity checks, process timing, limits, workflow circumvention, dan payment functionality.

Untuk objective dan kriteria PoC Bank Jatim, lihat [Objective dan Kriteria Evaluasi PoC Bank Jatim](19-objective-dan-kriteria-poc-bank-jatim.md). Untuk detail test case remittance, gunakan [Remittance Test Catalog](10-remittance-test-catalog.md), [OWASP Top 10:2025 Mapping](17-owasp-top10-2025-mapping.md), [Attack-Path Matrix](11-remittance-attack-path-matrix.md), dan [Sequence Diagrams](12-remittance-sequence-diagrams.md). Gunakan [Scoring Temuan untuk Persiapan Evaluasi Bank Jatim](18-scoring-temuan-bank-jatim.md) hanya sebagai model internal sampai rubric resmi diperoleh. Catat identifier requirement dan acceptance criteria pada test-case register.
