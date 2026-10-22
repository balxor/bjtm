# Remittance Attack-Path Matrix

## 1. Tujuan

Dokumen ini digunakan untuk memilih, menjalankan, dan menilai attack path pada pengujian remittance. Setiap path memuat prasyarat, kontrol yang diuji, prosedur validasi, expected behavior, evidence, dampak, dan prioritas.

Attack path adalah hipotesis pengujian, bukan pernyataan bahwa kontrol gagal. Gunakan target, akun, role, metode, dan environment dalam scope matrix serta Rules of Engagement (RoE).

## 2. Metode OSCP / bug-bounty untuk API remittance

Gunakan pendekatan manual-first. Otomasi membantu menemukan variasi, tetapi keputusan PASS/FAIL memerlukan pembuktian authorization, state, dan efek bisnis.

### Pass 1: Surface enumeration

Petakan hanya target dalam scope:
- Host, base path, API version, route, HTTP method, authentication type, dan role yang diperlukan.
- OpenAPI/Swagger, Postman collection, integration contract, dan dokumentasi dari pemilik sistem.
- Traffic dari web/mobile test client jika inspeksi diizinkan.
- Endpoint untuk read, create, update, approve, submit, cancel, retry, status inquiry, export, dan administrative action.
- Identifier seperti customer ID, beneficiary ID, transaction ID, quote ID, approval ID, external reference, dan correlation ID.
- Field dari request client dibandingkan dengan field pada response server.
- Error response, pagination, rate-limit response, dan status transition yang terlihat pada alur normal.

Output: endpoint inventory, data-object map, role matrix, dan daftar operasi state-changing.

### Pass 2: Authentication dan authorization matrix

Gunakan matriks berikut untuk menetapkan expected access per role. Isi nilainya berdasarkan requirement dan policy, bukan dugaan.

| Resource / operation | Customer A | Customer B | Maker | Checker | Admin/Support |
|---|---|---|---|---|---|
| Read own beneficiary | Sesuai policy | Sesuai policy | Sesuai policy | Sesuai policy | Sesuai policy |
| Read beneficiary milik user lain | Deny kecuali delegated access | Deny kecuali delegated access | Sesuai policy | Sesuai policy | Sesuai policy |
| Create transaction | Sesuai policy | Sesuai policy | Sesuai policy | Sesuai policy | Sesuai policy |
| Approve transaction | Sesuai policy | Sesuai policy | Deny jika maker tidak boleh approve sendiri | Sesuai policy | Sesuai policy |
| Change approved transaction | Sesuai workflow | Sesuai workflow | Sesuai workflow | Sesuai workflow | Sesuai policy |
| Change limit/configuration | Deny bagi customer biasa | Deny bagi customer biasa | Sesuai policy | Sesuai policy | Dual control jika diwajibkan |

Untuk setiap operasi, uji tiga dimensi:
1. **Function-level authorization:** apakah role dapat memanggil fungsi tersebut?
2. **Object-level authorization:** apakah actor berhak mengakses objek yang ditentukan?
3. **Property-level authorization:** apakah actor boleh membaca atau mengubah setiap field pada operasi tersebut?

### Pass 3: Request diff dan business-logic testing

Untuk satu test case:
1. Rekam satu request baseline yang valid.
2. Salin request ke test tool yang diizinkan.
3. Ubah satu variabel per test, misalnya actor, object identifier sintetis, field nonfinansial, state, atau idempotency reference.
4. Jalankan dengan satu akun dan satu test object yang telah ditetapkan.
5. Bandingkan status code, response body, response headers yang relevan, backend state yang tersedia, dan audit event.
6. Ulangi positive control untuk memastikan baseline tetap berfungsi.
7. Catat hasil dan keterbatasan.

Jangan menyimpulkan vulnerability hanya dari status HTTP. Periksa apakah data benar-benar dikembalikan, state berubah, dan kontrol downstream tetap konsisten.

### Pass 4: Controlled confirmation dan evidence

- Reproduksi observasi dengan objek uji yang sama dan kondisi yang sama.
- Hindari pengulangan jika dampak sudah terbukti.
- Gunakan correlation ID untuk menghubungkan API, workflow, connector, audit event, dan sandbox ledger jika tersedia.
- Rekam state sebelum/sesudah tanpa menyimpan data yang tidak diperlukan.
- Tetapkan impact berdasarkan evidence yang tersedia.
- Siapkan acceptance criteria untuk retest.

### Tool workflow

Gunakan tools sesuai RoE:
- Intercepting proxy untuk inspeksi request dan response pada test environment.
- Repeater atau manual HTTP client untuk mengubah satu variabel per test.
- Comparer untuk membandingkan response antarrole, owner objek, atau state.
- API client untuk menjalankan alur normal dan negative test.
- Browser/mobile test client untuk pemetaan operasi aplikasi jika diizinkan.
- Scanner untuk discovery yang disetujui; validasi hasilnya secara manual.

Hindari mass enumeration identifier, brute force, load testing, atau automation pada fungsi finansial tanpa izin spesifik dan batas volume tertulis.

## 3. Skala likelihood dan impact

### Likelihood (L)

| Nilai | Definisi |
|---:|---|
| 1 | Tidak umum; memerlukan kondisi khusus dan beberapa kontrol harus gagal |
| 2 | Rendah; prasyarat sulit dipenuhi atau kontrol kompensasi kuat |
| 3 | Mungkin; dapat dicapai oleh actor yang memenuhi prasyarat akses |
| 4 | Tinggi; jalur mudah diulang dan kontrol pembatas lemah |
| 5 | Sangat tinggi; jalur langsung, mudah diulang, dan prasyarat minimal |

Likelihood adalah estimasi awal. Perbarui setelah memahami authentication requirement, exposure, role yang dibutuhkan, kontrol deteksi, dan mitigasi yang berlaku.

### Impact (I)

| Nilai | Dampak transaksi/finansial dan operasional |
|---:|---|
| 1 | Dampak terbatas pada data uji; tidak ada perubahan state bisnis |
| 2 | Paparan terbatas atau gangguan kecil dengan pemulihan langsung |
| 3 | Paparan data/state terbatas, investigasi manual, atau koreksi objek uji |
| 4 | Potensi perubahan instruksi finansial, pemrosesan ganda, akses lintas customer, atau gangguan proses bisnis yang material |
| 5 | Potensi unauthorized financial effect, perubahan ledger, dampak lintas customer dalam skala luas, atau kegagalan kontrol pembayaran serius |

Impact bukan estimasi nominal kerugian. Perhitungan nominal hanya boleh menggunakan data volume, limit, dan parameter bisnis dari pemilik sistem.

### Inherent risk score

~~~text
Risk score = Likelihood (1-5) x Impact (1-5)

1-4    Low
5-9    Moderate
10-16  High
17-25  Critical
~~~

Skala ini merupakan model prioritisasi internal. Gunakan rubric severity dan risk acceptance klien bila tersedia. Bedakan inherent risk, residual risk, dan severity hasil pengujian.

## 4. Attack-path matrix

Nilai berikut merupakan baseline untuk merencanakan PoC, bukan hasil penilaian sistem aktual. Sesuaikan setelah scope, topology, access model, dan kontrol diketahui.

| Path ID | Rangkaian kontrol yang diuji | Prasyarat umum | L | I | Score | Prioritas PoC | Bukti hasil utama |
|---|---|---|---:|---:|---:|---|---|
| AP-01 | Authenticated user → object ID → beneficiary/transaction authorization | Akun dan owner objek terpisah | 3 | 4 | 12 | P1 | Request antar-owner, response, state, audit event |
| AP-02 | Draft update → property validation → persisted transaction | Transaksi sintetis non-settlement | 3 | 5 | 15 | P1 | Field sebelum/sesudah, response, state, audit |
| AP-03 | Maker create → checker approve → version changes → submit | Dua role uji dan state machine | 3 | 5 | 15 | P1 | Versi saat approval, approval status, submit decision |
| AP-04 | Submit → timeout/retry → idempotency/duplicate control | Sandbox atau mock connector | 3 | 5 | 15 | P1 | Logical transaction count, dispatch count, sandbox posting |
| AP-05 | Callback ingress → authenticity/replay checks → state update | Callback simulator yang dikendalikan | 3 | 5 | 15 | P1 jika in-scope | Valid/invalid/duplicate event dan perubahan state |
| AP-06 | Concurrent limit requests → rule evaluation → atomic commit | Sandbox dan batas concurrency tertulis | 2 | 4 | 8 | P2 | Request count, accepted/rejected state, limit record |
| AP-07 | Low-privileged role → privileged endpoint/function | Role matrix dan test accounts | 3 | 4 | 12 | P1 | Role, route/method, response, audit event |
| AP-08 | Downstream pending/timeout → retry/status recovery → reconciliation | Mock/sandbox status fixture | 3 | 4 | 12 | P1/P2 | Internal/external status, ledger reference, exception |
| AP-09 | Read/export/error response → object/property filtering | Synthetic data dan role terpisah | 3 | 3 | 9 | P2 | Data returned dibanding kebutuhan role |
| AP-10 | API inventory → undocumented/stale route → policy comparison | Inventory dan route discovery dalam scope | 2 | 4 | 8 | P2 | Route/version, authentication policy, owner |
| AP-11 | Quote → confirmation → approval → dispatch | Fee/FX rules dan quote fixture | 2 | 4 | 8 | P2 | Quote version, fee/FX values, expiry, final state |
| AP-12 | Transaction action → audit generation → SIEM/reconciliation | Akses ke log pengujian | 3 | 3 | 9 | P2 | Event completeness, timestamps, actor, correlation ID |

### Interpretasi score

- **P1:** kandidat utama PoC jika berada dalam scope dan dapat diuji dengan aman.
- **P2:** dikerjakan setelah P1 atau jika sesuai fokus evaluasi dan environment mendukung.
- Score sama tidak selalu menghasilkan urutan yang sama. Pertimbangkan exposure, kontrol kompensasi, waktu PoC, dan kualitas evidence.

## 5. Detail jalur pengujian

### AP-01: Object-level authorization

**Tujuan:** memeriksa apakah authorization melekat pada objek, bukan hanya pada endpoint atau session.

**Setup**
- Account A owns Object A.
- Account B owns Object B.
- Kedua objek dibuat untuk pengujian dan tidak berkaitan dengan pelanggan nyata.

**Prosedur**
1. Gunakan Account A untuk membaca Object A sebagai baseline.
2. Gunakan Account A untuk meminta Object B pada endpoint baca yang sama dan tercantum dalam scope.
3. Jalankan operasi yang sama dengan Account B sebagai positive control.
4. Untuk operasi update, gunakan objek disposable dan field nonfinansial.
5. Periksa response, object state, dan audit event.

**PASS:** akses lintas-owner ditolak; tidak ada data sensitif atau perubahan objek.  
**FAIL:** Account A membaca data nonpublik Object B atau mengubahnya tanpa entitlement.  
**Stop condition:** hentikan variasi setelah akses lintas-owner terbukti. Jangan melakukan enumerasi identifier pengguna lain.

### AP-02: Property authorization dan transaction integrity

**Tujuan:** mengevaluasi apakah API menerima field yang seharusnya dikendalikan server.

**Setup**
- Satu transaksi sintetis berstatus draft.
- Kontrak API atau field model untuk menentukan editable dan server-controlled attributes.
- Sandbox yang mencegah settlement finansial.

**Prosedur**
1. Catat field yang dapat diedit melalui fungsi bisnis normal.
2. Gunakan test request untuk satu field per pengujian.
3. Pilih field berdampak rendah terlebih dahulu. Owner, amount, currency, beneficiary, fee, approval status, dan screening decision hanya diuji memakai test fixture di sandbox.
4. Periksa response dan nilai tersimpan menggunakan read-only view atau log.
5. Pastikan transaksi tidak dikirim ke connector selama negative test.

**PASS:** field server-controlled ditolak/diabaikan sesuai kontrak dan tidak memengaruhi state.  
**FAIL:** field terlindungi diterima dan nilai persisted berubah tanpa kontrol yang diwajibkan.

### AP-03: Approval binding dan perubahan setelah approval

**Tujuan:** memastikan approval berlaku bagi detail transaksi tertentu.

**Setup**
- Maker dan checker adalah akun berbeda.
- Satu transaksi sintetis.
- Transaction version, approval record, dan status tersedia untuk pemeriksaan.

**Prosedur**
1. Maker membuat transaksi dan mencatat versi serta atribut kritis.
2. Checker melakukan approval melalui workflow resmi.
3. Pada test transaction yang tidak dapat settlement, lakukan perubahan melalui test harness atau jalur update yang disetujui.
4. Lanjutkan hanya melalui alur submit normal dalam sandbox.
5. Periksa apakah sistem menolak, menginvalidasi approval, atau meminta approval ulang.
6. Bandingkan approval record, version, dan audit event.

**PASS:** approval hanya valid untuk versi/detail yang disetujui.  
**FAIL:** perubahan atribut kritis tetap dapat disubmit memakai approval lama ketika policy mewajibkan approval ulang.  
**Stop condition:** jangan lanjutkan ke payment connector atau ledger produksi.

### AP-04: Idempotency dan duplicate processing

**Tujuan:** memeriksa hubungan antara API retry, idempotency record, outbound dispatch, dan posting sandbox.

**Setup**
- Mock/sandbox connector.
- Satu logical transaction dan reference unik.
- Retry count serta transaction ceiling disetujui.

**Prosedur**
1. Submit satu logical transaction dan catat ID serta status.
2. Ulangi request secara terbatas sesuai desain retry dan RoE.
3. Periksa apakah response mengembalikan hasil yang sama atau menolak duplicate sesuai kontrak.
4. Cocokkan transaction record, outbound dispatch, ledger sandbox, dan audit event.
5. Jika concurrency test disetujui, lakukan secara terpisah dengan jumlah kecil.

**PASS:** satu logical instruction menghasilkan maksimal satu efek bisnis.  
**FAIL:** terjadi lebih dari satu dispatch/posting untuk instruksi yang sama atau state tidak dapat direkonsiliasi.

### AP-05: Callback authenticity, replay, dan ordering

**Tujuan:** memeriksa apakah callback memengaruhi status hanya setelah validasi sumber dan state.

**Setup**
- Callback simulator yang dikendalikan.
- Transaksi sintetis.
- Kontrak signature/authentication, event ID, timestamp, dan urutan status.

**Prosedur**
1. Kirim event valid melalui simulator dan rekam state setelah proses.
2. Gunakan simulator untuk mengirim event dengan signature atau metadata autentikasi tidak valid.
3. Ulangi satu event valid untuk menilai duplicate handling.
4. Uji event out-of-order hanya dengan fixture yang aman dan jika ordering termasuk desain.
5. Periksa state transaksi, outbound action, audit, dan reconciliation.

**PASS:** event tidak valid ditolak, duplikat tidak menimbulkan efek kedua, dan status mengikuti transisi yang diizinkan.  
**FAIL:** event tidak valid mengubah state atau event duplikat/out-of-order menghasilkan state yang tidak sesuai.  
**Stop condition:** jangan mengirim callback palsu ke endpoint production atau partner aktual.

### AP-06: Limit enforcement dalam kondisi bersamaan

**Tujuan:** menilai apakah limit reservation dan commit tetap konsisten ketika request hampir bersamaan.

**Setup**
- Limit test dari pemilik sistem.
- Akun dan transaksi sintetis.
- Sandbox non-settlement.
- Batas concurrency tertulis pada RoE.

**Prosedur**
1. Jalankan transaksi sintetis di bawah limit sebagai baseline.
2. Jalankan satu test boundary value yang disetujui.
3. Hanya jika diizinkan, jalankan concurrency test terbatas dengan dua request uji yang telah ditentukan.
4. Bandingkan hasil dengan limit policy dan transaction store.
5. Hentikan setelah test count yang disetujui selesai.

**PASS:** hasil memenuhi limit setelah validasi dan commit.  
**FAIL:** limit terlampaui akibat race condition.  
**Stop condition:** jangan meningkatkan concurrency atau volume untuk mencari ambang kegagalan.

## 6. Artefak untuk setiap attack path

| Artefak | Isi |
|---|---|
| Path ID | AP-01, AP-02, dan seterusnya |
| Preconditions | Access, role, data, state, environment |
| Sequence | Urutan komponen |
| Test cases | Test ID dan variasi yang dijalankan |
| Evidence bundle | Request/response tersanitasi, timestamp, correlation ID |
| State proof | State sebelum/sesudah dari view/log yang diizinkan |
| Risk score | L, I, score, rationale |
| Result | PASS, FAIL, INCONCLUSIVE, BLOCKED, NOT TESTED |
| Remediation | Kontrol teknis yang perlu diperbaiki |
| Retest | Acceptance criteria dan hasil verifikasi |

## 7. Severity finding versus attack-path score

Attack-path score digunakan untuk memilih prioritas pengujian. Severity finding ditetapkan setelah hasil diverifikasi menggunakan rubric klien jika tersedia.

Saat memberi severity, pertimbangkan:
- Apakah akses memerlukan autentikasi atau role khusus.
- Apakah path hanya membaca data atau mengubah state.
- Apakah financial effect terjadi, dicegah kontrol downstream, atau hanya menjadi risiko potensial.
- Lingkup objek yang dapat terdampak.
- Prasyarat, kemungkinan pengulangan, dan kontrol kompensasi.
- Kualitas evidence serta reproducibility.
- Kemampuan deteksi, investigasi, dan recovery.

Jangan menyatakan kerugian finansial aktual jika test hanya menunjukkan perubahan pada objek sintetis di sandbox.

## 8. Remediation mapping

| Domain | Kontrol teknis yang relevan |
|---|---|
| BOLA | Authorization per object dan owner/tenant check di server |
| BFLA | Policy terpusat, deny-by-default, role-function matrix |
| Property authorization | DTO/schema allowlist, field server-owned, authoritative calculation |
| Approval binding | Approval terikat pada transaction version atau digest atribut kritis |
| Idempotency | Idempotency store atomik, unique business reference, downstream deduplication |
| Limit race | Atomic reserve/commit, transaksi database, bounded concurrency testing |
| Callback | Authentication/signature validation, event ID, replay protection, state validation |
| Fee/FX | Quote version, expiry enforcement, server-side calculation, precision policy |
| Reconciliation | Correlation ID end-to-end, exception queue, controlled repair/reversal |
| Audit | Event schema, log access control, timestamp consistency, correlation |
| API inventory | Versioned inventory, route owner, removal/deprecation process |
| Sensitive data | Data minimization, output schema, RBAC, log redaction, retention |

## 9. Referensi teknis

- [OWASP API Security Top 10 2023](https://api-security.owasp.org/editions/2023/en/0x11-t10/)
- [OWASP Web Security Testing Guide: Business Logic Testing](https://wstg.owasp.org/stable/4-Web_Application_Security_Testing/10-Business_Logic_Testing/)
- [OWASP ASVS 5.0.0](https://asvs.owasp.org/)

Catat requirement dan test identifier dari standar yang relevan pada test-case register, lalu tetapkan acceptance criteria untuk setiap requirement dalam scope.
