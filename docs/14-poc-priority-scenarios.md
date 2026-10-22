# PoC Priority Scenarios: Remittance API

## 1. Tujuan

Dokumen ini menetapkan lima skenario kandidat utama untuk demonstrasi penetration testing pada remittance API. Setiap skenario dirancang agar dapat dieksekusi secara terukur, diulang, dibuktikan dengan evidence, dan dihentikan sebelum timbul efek finansial nyata.

Sebelum eksekusi, sesuaikan nama service, API route, role, transaction state, serta expected behavior dengan API contract, requirement, dan Rules of Engagement (RoE). Contoh endpoint pada dokumen ini menggunakan domain placeholder dan bukan target operasional.

## 2. Pemilihan skenario

| Urutan | Scenario ID | Attack path | Alasan dipilih | Risiko utama | Prasyarat wajib |
|---:|---|---|---|---|---|
| 1 | POC-01 | Object-level authorization | Membuktikan pemisahan data antar-owner | Akses atau perubahan objek tanpa entitlement | Dua akun dan objek sintetis |
| 2 | POC-02 | Transaction property integrity | Menguji otoritas server atas field bisnis dan finansial | Instruksi berubah di luar policy | Sandbox non-settlement dan field contract |
| 3 | POC-03 | Approval binding / state machine | Memeriksa maker-checker dan approval terhadap versi transaksi | Submit setelah perubahan kritis tanpa approval yang valid | Dua role dan workflow sandbox |
| 4 | POC-04 | Idempotency / duplicate submit | Menguji timeout dan retry terhadap satu logical transaction | Dispatch atau posting ganda | Mock/payment sandbox dan retry ceiling |
| 5 | POC-05 | Callback integrity / reconciliation | Menguji status downstream serta rekonsiliasi | Status tidak sah, duplikat, atau mismatch tidak tertangani | Callback simulator dan referensi sintetis |

Urutan akhir mempertimbangkan scope, ketersediaan environment, waktu demonstrasi, serta acceptance criteria tender. Kelima skenario ini bukan pernyataan bahwa vulnerability ditemukan pada sistem target.

## 3. POC-01: Object-level authorization

### Tujuan
Memastikan setiap operasi membaca atau mengubah beneficiary/transaksi memeriksa owner, tenant, delegation, dan role pada server.

### Setup
- Account A dan Account B dengan hak akses berbeda.
- Object A dimiliki Account A; Object B dimiliki Account B.
- Gunakan dua transaksi/beneficiary sintetis yang dibuat khusus untuk test.
- Identifikasi endpoint read, detail, status, dan operasi update yang masuk scope.
- Siapkan akses read-only ke state/log test jika disediakan pemilik sistem.

### Test procedure
1. Login sebagai Account A lalu akses Object A. Rekam baseline.
2. Login sebagai Account B lalu akses Object B. Rekam positive control.
3. Dengan token Account A, lakukan request terhadap Object B pada endpoint yang sama, sesuai scope.
4. Bandingkan HTTP status, response body, metadata sensitif, correlation ID, dan audit event.
5. Periksa apakah state Object B berubah. Untuk write test, gunakan objek disposable dan field nonfinansial yang secara eksplisit diizinkan.
6. Hentikan variasi saat akses lintas-owner terbukti. Jangan enumerasi identifier lain.

Contoh struktur request untuk test environment. Ganti host, token, dan ID hanya dengan nilai yang diberikan untuk engagement:

~~~http
GET /api/v1/transactions/<SYNTHETIC_TRANSACTION_ID_B> HTTP/1.1
Host: <APPROVED_TEST_HOST>
Authorization: Bearer <TOKEN_FOR_TEST_ACCOUNT_A>
Accept: application/json
X-Test-Case: POC-01
~~~

Request ini merupakan template dokumentasi. Gunakan Repeater atau API client hanya terhadap target dalam scope.

### Expected secure behavior
- Akses tidak sah ditolak sesuai kebijakan API.
- Response tidak membocorkan data objek.
- State tidak berubah.
- API melakukan authorization per object, tidak hanya memeriksa apakah session valid.

### Evidence dan verdict
**Evidence minimum:** request/response tersanitasi, actor/role, owner dan ID sintetis, timestamp, correlation ID, state before/after, audit event bila tersedia.

- **PASS:** Account A tidak dapat membaca atau mengubah Object B, sementara positive control Account B berhasil sesuai policy.
- **FAIL:** Account A dapat membaca data nonpublik Object B atau mengubah objek tanpa entitlement.
- **INCONCLUSIVE:** objek tidak dapat dibedakan, policy tidak tersedia, atau backend state tidak dapat diperiksa.
- **BLOCKED:** dua akun/objek uji atau otorisasi write test tidak tersedia.

### Dampak dan remediation
Dampak potensial meliputi akses lintas customer, perubahan beneficiary/transaksi tanpa hak, serta paparan data. Remediasi umumnya mencakup authorization check di setiap operasi server-side, ownership/tenant validation, policy deny-by-default, serta negative regression test.

### Cleanup
Pastikan tidak ada perubahan pada Object B. Hapus atau arsipkan data sintetis sesuai prosedur engagement.

## 4. POC-02: Transaction property integrity

### Tujuan
Menentukan apakah atribut yang menentukan instruksi remittance dihitung atau divalidasi oleh server, bukan dipercaya dari request client.

### Property matrix

| Property | Sumber otoritatif yang diharapkan | Pertanyaan uji |
|---|---|---|
| Owner/customer/tenant | Authenticated context dan data server | Apakah caller dapat mengganti ownership melalui request? |
| Beneficiary ID | Beneficiary service dan authorization policy | Apakah beneficiary harus dimiliki/dapat diakses actor? |
| Amount/currency | Transaction rules dan product policy | Apakah nilai divalidasi terhadap range, currency, precision, dan limit? |
| Fee/FX quote ID/expiry | Quote/rules service | Apakah fee dan FX konsisten dengan quote yang berlaku? |
| Approval/status | Workflow/state machine | Apakah field ini hanya berubah melalui operasi workflow yang sah? |
| Screening/risk outcome | Risk/compliance service jika digunakan | Apakah caller dapat memaksa nilai outcome melalui input? |
| Business/idempotency reference | Transaction orchestration | Apakah referensi unik dan konsisten pada retry? |

### Setup
- Satu transaksi sintetis dalam status draft.
- Dokumentasi field yang editable serta field yang server-controlled.
- Sandbox dengan settlement dinonaktifkan atau pengamanan non-settlement yang disetujui.
- Read-only state view atau test log untuk membandingkan request dengan state tersimpan.

### Test procedure
1. Buat transaksi menggunakan fungsi normal dan simpan nilai input, response, transaction ID, dan state.
2. Pilih satu field per test. Mulai dari field berdampak rendah dan eksplisit boleh diuji.
3. Untuk field server-controlled, lakukan negative test hanya pada harness atau transaksi sandbox yang tidak dapat mencapai connector.
4. Bandingkan response dengan nilai tersimpan, hasil fee/FX/limit, approval record, dan audit event yang tersedia.
5. Ulangi baseline untuk memastikan test tidak mengganggu fungsi normal.
6. Jangan meneruskan transaksi ke payment rail.

### Expected secure behavior
- Server menolak atau mengabaikan field yang tidak boleh dikendalikan client sesuai API contract.
- Atribut finansial divalidasi terhadap aturan yang berlaku.
- Owner, approval status, screening result, dan transaction status tidak dapat ditentukan dari input client yang tidak dipercaya.
- Penolakan tidak menghasilkan perubahan state yang tidak konsisten.

### Evidence dan verdict
Rekam field yang diuji, request sebelum/sesudah redaction, expected value, actual persisted value, validation result, state transition, timestamp, dan event log.

- **PASS:** server-controlled attributes tetap sesuai sumber otoritatif.
- **FAIL:** input yang tidak berwenang mengubah nilai persisted atau memengaruhi state/approval.
- **INCONCLUSIVE:** aturan field atau hasil backend tidak tersedia.
- **BLOCKED:** sandbox/harness non-settlement tidak tersedia.

### Dampak dan remediation
Risiko meliputi instruksi finansial yang tidak sesuai persetujuan, perubahan beneficiary, nilai fee/FX yang salah, atau kontrol approval yang dilewati. Remediasi dapat mencakup schema allowlist, separation antara client DTO dan internal model, server-side calculation, property-level authorization, serta revalidation sebelum submit.

## 5. POC-03: Approval binding dan state machine

### Tujuan
Memastikan approval berlaku pada transaksi dan versi atribut kritis yang benar, serta backend menegakkan state transition.

### Setup
- Akun Maker dan Checker berbeda.
- Satu transaksi sintetis.
- State machine, rule self-approval, dan field yang memerlukan approval ulang terdokumentasi.
- Sandbox non-settlement dan pemeriksaan state tersedia.

### Test procedure
1. Maker membuat transaksi dan mencatat transaction ID, version, beneficiary, amount, currency, fee/FX reference, serta state.
2. Maker mengirim transaksi ke approval.
3. Checker melakukan approval melalui workflow normal. Simpan approval record serta versi transaksi.
4. Pada transaksi test yang tidak dapat settlement, gunakan alur update yang secara eksplisit disetujui untuk mengevaluasi perubahan atribut kritis.
5. Periksa apakah update ditolak, approval diinvalidasi, atau approval ulang diwajibkan sesuai policy.
6. Coba submit hanya pada sandbox untuk memeriksa eligibility. Jangan dispatch ke rail produksi.
7. Cocokkan current transaction version, approval version, submit decision, dan audit event.

### Expected secure behavior
- Maker tidak dapat menyetujui transaksinya sendiri jika separation of duties diwajibkan.
- Checker hanya dapat approve transaksi dalam state yang diizinkan.
- Perubahan atribut kritis membuat approval lama tidak berlaku jika policy mensyaratkannya.
- Submit ditolak ketika state, approval, limit, atau rule result yang diwajibkan tidak valid.

### Evidence dan verdict
**Evidence minimum:** maker/checker identity, transaction ID, version sebelum/sesudah, approval record, perubahan atribut uji, submit eligibility result, audit log.

- **PASS:** setiap transition dan approval mengikuti workflow.
- **FAIL:** actor melompati state atau transaksi yang berubah dapat disubmit menggunakan approval yang semestinya sudah tidak valid.
- **INCONCLUSIVE:** version/approval binding tidak dapat ditelusuri.
- **BLOCKED:** akun terpisah atau sandbox tidak tersedia.

### Dampak dan remediation
Dampak potensial berupa transaksi disubmit tanpa review yang diwajibkan. Perbaikan umumnya meliputi server-side state machine, separation of duties, approval binding ke transaction version atau digest atribut kritis, serta revalidation saat submit.

## 6. POC-04: Idempotency dan duplicate handling

### Tujuan
Menilai apakah retry setelah timeout dapat menyebabkan satu logical transaction menghasilkan lebih dari satu dispatch atau posting.

### Setup
- Mock connector atau payment sandbox.
- Satu logical transaction sintetis dengan business reference dan idempotency key.
- Simulasi timeout yang terkendali.
- Retry count, concurrency, dan transaction ceiling ditentukan dalam RoE.

### Test procedure
1. Submit satu transaksi sintetis melalui alur normal dan catat logical transaction ID serta reference downstream.
2. Gunakan mock untuk mensimulasikan timeout setelah request diterima downstream, sesuai fixture yang disediakan.
3. Lakukan retry terbatas sesuai kontrak API dan batas RoE.
4. Bandingkan jumlah logical transaction, outbound dispatch, sandbox posting, response, dan audit event.
5. Jika concurrency test disetujui, jalankan sebagai test case terpisah dengan limit yang sudah ditentukan.
6. Hentikan setelah batas request yang disepakati. Jangan mencari batas kegagalan dengan menambah volume.

### Expected secure behavior
- Retry menggunakan idempotency identity yang konsisten.
- Hasil duplicate konsisten atau ditolak sesuai kontrak.
- Satu logical instruction menghasilkan maksimal satu efek bisnis.
- Status pending/unknown ditangani melalui recovery/reconciliation dan tidak diklaim berhasil tanpa bukti.

### Evidence dan verdict
Catat idempotency key tersamarkan, business reference, logical transaction count, dispatch count, posting count, state, retry attempt, timestamp, serta correlation ID.

- **PASS:** tidak ada efek ganda; status dapat dijelaskan dan direkonsiliasi.
- **FAIL:** satu instruksi menghasilkan lebih dari satu dispatch/posting atau status yang tidak dapat direkonsiliasi.
- **INCONCLUSIVE:** sandbox tidak menyediakan observasi outbound/posting.
- **BLOCKED:** mock connector, timeout fixture, atau batas retry tidak tersedia.

### Dampak dan remediation
Risiko dapat berupa pemrosesan ganda atau status finansial ambigu. Kontrol terkait mencakup penyimpanan idempotency atomik, unique business reference, deduplication downstream, bounded retry, explicit pending state, serta rekonsiliasi.

## 7. POC-05: Callback integrity dan reconciliation

### Tujuan
Memastikan status dari downstream divalidasi sebelum mengubah state dan setiap transaksi sintetis dapat direkonsiliasi.

### Setup
- Callback simulator atau test endpoint yang dikendalikan.
- Satu transaksi sintetis dengan correlation ID.
- Mekanisme autentikasi/signature, event ID, timestamp, state transition, dan expected audit event diketahui.
- Akses pada status internal serta record reconciliation/ledger sandbox tersedia.

### Test procedure
1. Kirim callback valid melalui simulator dan catat state serta audit event.
2. Kirim event negatif melalui simulator untuk menguji signature/authentication metadata yang invalid.
3. Ulangi event ID yang sama untuk menguji deduplication.
4. Uji event out-of-order hanya jika tersedia fixture yang aman dan behavior-nya didefinisikan dalam sistem.
5. Cocokkan state transaction store, payment adapter, ledger sandbox, external reference, reconciliation queue, dan audit event.
6. Jika terjadi mismatch, periksa apakah sistem membuat exception dan membatasi koreksi pada role yang berwenang.

### Expected secure behavior
- Event invalid ditolak tanpa mengubah state.
- Duplicate event tidak membuat efek baru.
- Event out-of-order mengikuti policy state machine.
- Mismatch status/posting dapat diidentifikasi, dilog, dan diarahkan ke exception handling.
- Koreksi/reversal mengikuti authorization dan audit requirements.

### Evidence dan verdict
Catat event ID, sumber simulator, validation result, transaction ID, external reference, state sebelum/sesudah, ledger reference, exception ID, correlation ID, dan audit event.

- **PASS:** callback validation, state transition, dan reconciliation mengikuti policy.
- **FAIL:** event tidak valid mengubah state, duplicate event menghasilkan efek kedua, atau mismatch tidak diproses sesuai control requirement.
- **INCONCLUSIVE:** tidak ada visibility ke status downstream atau reconciliation.
- **BLOCKED:** callback simulator atau ledger sandbox tidak tersedia.

### Dampak dan remediation
Risiko meliputi status transaksi yang tidak valid, mismatch yang tidak terdeteksi, atau efek duplicate. Kontrol yang relevan mencakup peer authentication, signature/timestamp validation, event deduplication, state validation, correlation ID end-to-end, exception management, dan controlled reconciliation.

## 8. Evidence pack untuk evaluator

Susun satu folder internal yang dibatasi akses untuk setiap scenario ID:

~~~text
POC-01-authorization/
  00-test-plan.md
  01-baseline-request-redacted.txt
  02-negative-test-request-redacted.txt
  03-response-comparison.md
  04-state-before-after.md
  05-audit-event-redacted.json
  06-verdict-and-impact.md
  07-remediation-and-retest.md

POC-02-transaction-integrity/
POC-03-approval-binding/
POC-04-idempotency/
POC-05-callback-reconciliation/
~~~

Folder di atas merupakan contoh struktur. Simpan evidence mentah di evidence store yang dikendalikan engagement, bukan pada repository dokumentasi publik.

Setiap evidence pack harus memuat:
- Test ID dan Path ID.
- Timestamp dan timezone.
- Actor/role dan objek sintetis.
- Expected versus observed behavior.
- State proof atau log reference.
- Redaction status dan reviewer.
- Verdict dan alasan.
- Cleanup status.
- Retest criteria.

## 9. Rencana demo evaluator: 30 menit

| Menit | Aktivitas | Output evaluator |
|---:|---|---|
| 00-03 | Jelaskan business flow dan scope test | Scope, roles, non-settlement guardrail |
| 03-08 | POC-01: authorization boundary | Bukti object ownership di-enforce |
| 08-14 | POC-02: transaction property integrity | Bukti validasi server-side |
| 14-19 | POC-03: approval binding | Bukti approval terkait versi/state |
| 19-24 | POC-04: idempotency/retry | Bukti satu logical transaction, satu efek bisnis |
| 24-28 | POC-05: callback/reconciliation | Bukti status dan audit/reconciliation konsisten |
| 28-30 | Ringkasan, limitations, remediation dan retest | Finding register dan next steps |

Atur urutan menurut waktu serta rubric tender yang resmi. Jika skenario gagal direproduksi, jangan memaksakan efek berisiko; tampilkan test method, batasan, dan hasil yang dapat dibuktikan.

## 10. Kesiapan dan penanganan hasil tanpa vulnerability

PoC yang berkualitas juga harus menghasilkan hasil yang jelas saat kontrol bekerja sesuai harapan.

- Jika kontrol lulus, tunjukkan negative test ditolak, positive control berhasil, state tetap konsisten, dan event tercatat.
- Jika hasil tidak konklusif, jelaskan data atau akses yang kurang.
- Jika test diblokir oleh scope atau environment, dokumentasikan dependency dan dampak terhadap coverage.
- Jika vulnerability terbukti, hentikan variasi yang tidak diperlukan, amankan evidence, lakukan cleanup, dan laporkan sesuai jalur eskalasi.
- Jangan menaikkan impact dari sandbox ke dugaan kerugian finansial aktual tanpa bukti.

## 11. Acceptance criteria keseluruhan

PoC dinyatakan selesai bila:
- Kelima skenario memiliki status dan rationale.
- Semua request memakai akun, data, dan endpoint dalam scope.
- Tidak ada dana nyata yang bergerak akibat pengujian.
- State transaksi sintetis telah direkonsiliasi.
- Evidence pack telah ditinjau dan disunting.
- Finding memiliki impact, recommendation, owner, dan retest criteria.
- Limitations dan test yang tidak dijalankan dicatat.
- Cleanup dan closeout mendapat sign-off.

## 12. Relasi dengan dokumentasi lain

- [Remittance Threat Model](03-remittance-threat-model.md)
- [Remittance Test Catalog](10-remittance-test-catalog.md)
- [Attack-Path Matrix](11-remittance-attack-path-matrix.md)
- [Sequence Diagrams](12-remittance-sequence-diagrams.md)
- [API Security Testing Field Playbook](13-api-security-testing-field-playbook.md)
- [Test Case Template](../templates/test-case-template.md)
- [Finding Template](../templates/finding-template.md)
- [Evidence Log Template](../templates/evidence-log-template.md)
