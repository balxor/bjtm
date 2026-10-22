# API Security Testing Field Playbook untuk Remittance

## 1. Tujuan dan metode

Dokumen ini memberikan metode manual-first untuk mengubah endpoint inventory menjadi pengujian API dan business logic yang bisa direproduksi. Urutan kerja terinspirasi dari penetration testing hands-on: reconnaissance dalam scope, enumeration, hypothesis-driven testing, controlled validation, evidence capture, dan reporting.

Pada sistem remittance, pembuktian dibatasi pada akses dan perubahan terhadap objek sintetis. Tidak dilakukan post-exploitation di luar kebutuhan pembuktian, tidak dilakukan pivot ke sistem lain, dan tidak dilakukan akses data nasabah nyata.

## 2. Tahap 0: Rules of Engagement dan safety gate

Sebelum mengirim request pengujian, catat:
- Scope ID dan pemilik sistem.
- Host, path, endpoint, API version, dan environment.
- Test account, role, customer/tenant, beneficiary, dan transaction IDs sintetis.
- Metode dan jenis pengujian yang diizinkan.
- Batas request, concurrency, transaction count, dan test window.
- Sistem downstream yang dapat menerima instruksi.
- Batas non-settlement dan cara memastikan hal tersebut.
- Stop condition serta kontak eskalasi.
- Lokasi penyimpanan evidence, redaction, dan retensi.

Status yang belum jelas berarti test terkait belum siap dijalankan.

## 3. Tahap 1: Reconnaissance dan endpoint enumeration

### Sumber yang digunakan

Urutan prioritas:
1. OpenAPI/Swagger dan API contract dari pemilik sistem.
2. Web/mobile traffic yang dihasilkan oleh akun uji.
3. Postman collection atau dokumentasi integrasi yang masuk scope.
4. API gateway inventory dan owner mapping.
5. Manual request review pada fitur yang diperbolehkan.

Gunakan passive/manual discovery terlebih dahulu. Active scan hanya dilakukan bila dicantumkan atau diizinkan dalam RoE.

### Endpoint inventory

| Field | Contoh nilai |
|---|---|
| API ID | API-REM-001 |
| Host/environment | `remittance-test.example.invalid` |
| Method/path | `GET /api/v1/transactions/{transactionId}` |
| Business function | Read transaction |
| Actor/role required | Customer / maker / checker |
| Object identifiers | customerId, transactionId |
| State-changing | Yes / No |
| Sensitive properties | amount, currency, beneficiary, status |
| Authentication type | Session / OAuth token / service identity |
| Authorization control | Function / object / property |
| Downstream effect | None / workflow / connector / ledger sandbox |
| Evidence reference | TBD |

Contoh host dan route di atas adalah placeholder dokumentasi. Ganti hanya dengan endpoint yang diberikan pemilik sistem atau ditemukan melalui aktivitas dalam scope.

### Output reconnaissance

- Route/method inventory.
- Data-object map.
- Role and entitlement matrix.
- Daftar field berdasarkan ownership dan editable status.
- State-changing operation list.
- Trust-boundary map.
- Daftar endpoint yang perlu klarifikasi scope.

## 4. Tahap 2: Proxy dan manual request workflow

### Alur operasional

1. Login dengan akun uji yang disediakan.
2. Jalankan satu fungsi normal dari UI atau API client.
3. Capture request dan response pada proxy.
4. Catat actor/role, object ID sintetis, state awal, dan correlation ID.
5. Kirim baseline request menggunakan Repeater atau HTTP client yang disetujui.
6. Simpan baseline sebagai evidence tersanitasi.
7. Duplikasi request dan ubah satu variabel saja untuk negative test yang direncanakan.
8. Bandingkan hasil dengan baseline.
9. Gunakan fungsi read-only atau log yang diizinkan untuk memeriksa state setelah request.
10. Hentikan variasi segera setelah kelemahan terbukti atau batas pengujian tercapai.

Jangan mengubah banyak field sekaligus. Perubahan multi-parameter membuat root cause sulit ditentukan.

### Request comparison worksheet

| Request | Actor | Object owner | Variable changed | Expected result | Actual result | State changed? |
|---|---|---|---|---|---|---|
| Baseline | Account A | Account A | None | Policy permits | TBD | TBD |
| Negative test | Account A | Account B | Object identifier | Deny unless delegated | TBD | TBD |
| Positive control | Account B | Account B | None | Policy permits | TBD | TBD |

Semua Account A/B dan object identifiers harus dibuat untuk pengujian. Jangan mencari identifier pengguna nyata melalui enumeration.

## 5. Tahap 3: Authorization testing

### BOLA / IDOR

1. Jalankan operasi yang sah terhadap objek milik Account A.
2. Catat request, identifier objek, dan baseline response.
3. Gunakan Account A untuk meminta objek sintetis milik Account B yang dibuat untuk test.
4. Periksa response body, metadata, dan state object.
5. Ulangi dengan Account B untuk positive control.
6. Uji operasi update hanya dengan field disposable/nonfinansial jika perubahan state diizinkan.
7. Hentikan setelah ditemukan akses lintas-owner.

**Bug condition:** object-level check tidak diterapkan atau tidak sesuai policy, sehingga actor dapat membaca atau mengubah objek tanpa entitlement.

**Bukan bukti yang cukup:** status HTTP saja tanpa pemeriksaan content, state, dan expected policy.

### BFLA / privilege boundary

Untuk tiap fungsi sensitif:
- Tetapkan expected role dari policy.
- Jalankan baseline dengan role yang sah.
- Ulangi dengan role uji yang tidak memiliki privilege tersebut.
- Periksa response dan audit event.
- Hindari tindakan yang mengeksekusi pembayaran atau mengubah konfigurasi produksi.

Fokus fungsi dapat mencakup approval, beneficiary administration, transaction cancellation, exception handling, report export, limit configuration, atau user/role management bila masuk scope.

### Property-level authorization

Buat field matrix sebelum pengujian:

| Property | Sumber nilai yang diharapkan | Dapat diedit user? | Test method | Expected behavior |
|---|---|---|---|---|
| beneficiaryId | Beneficiary service / user selection | Sesuai flow | Ownership and business validation | Ditolak jika tidak memiliki akses |
| amount | User input dengan server validation | Sesuai produk | Test value pada transaksi sintetis | Limit dan business rule diterapkan |
| currency | Product/transaction rules | Sesuai produk | Uji nilai yang didukung pada sandbox | Hanya nilai yang diizinkan |
| fee / FX quote ID | Quote/rule service | Umumnya server-governed | Uji quote version/expiry fixture | Nilai sesuai quote policy |
| transaction status | State machine | Tidak langsung melalui client | Negative test pada test harness | Perubahan hanya melalui transisi yang diizinkan |
| approval status | Workflow service | Tidak melalui properti bebas | Approval flow test | Perubahan memerlukan actor dan state yang sah |
| owner/tenant ID | Identity/business context | Tidak boleh dipercaya dari client saja | Test pada objek sintetis | Ownership server-side tetap berlaku |

Jangan menggunakan field finansial atau kontrol privileged pada sistem yang dapat settlement dana nyata.

## 6. Tahap 4: Workflow and state-machine testing

Minta daftar state dan transisi yang diizinkan. Contoh abstrak:

| Current state | Operation | Actor | Expected next state | Kondisi |
|---|---|---|---|---|
| DRAFT | Validate | Maker/system | VALIDATED | Input lolos business rule |
| VALIDATED | Request approval | Maker | PENDING_APPROVAL | Approval required |
| PENDING_APPROVAL | Approve | Checker | APPROVED | Role dan SoD valid |
| APPROVED | Submit | Authorized actor/system | SUBMITTED/PENDING | Approval masih valid |
| SUBMITTED | Receive status | Adapter/event processor | PROCESSING/COMPLETED/FAILED | Event authentic dan transition valid |
| Any terminal state | Resubmit | Actor sesuai policy | Deny / idempotent result | Tidak membuat efek baru |

Langkah:
1. Uji alur normal untuk memperoleh baseline.
2. Identifikasi operasi yang mengubah state.
3. Dengan transaksi sintetis pada sandbox, uji satu transisi yang tidak diizinkan atau tidak sesuai urutan.
4. Periksa response dan state yang tersimpan.
5. Periksa apakah approval tetap berlaku ketika transaction version atau atribut kritis berubah.
6. Verifikasi tidak ada connector dispatch saat precondition tidak terpenuhi.

Bukti utama adalah state sebelum/sesudah dan keputusan workflow, bukan hanya response endpoint.

## 7. Tahap 5: Idempotency, retries, callback, dan limit checks

### Idempotency test matrix

| Case | Request pattern | Expected secure behavior |
|---|---|---|
| IDEM-01 | Same key, same payload, retry terbatas | Mengembalikan hasil konsisten; tidak membuat efek kedua |
| IDEM-02 | Same key, different payload dalam test fixture | Ditolak atau conflict sesuai kontrak; payload baru tidak mengganti instruksi awal |
| IDEM-03 | New request key tetapi business reference sama | Duplicate business transaction ditangani sesuai desain |
| IDEM-04 | Timeout dengan outcome downstream belum diketahui | Status pending/unknown atau rekonsiliasi; retry tidak menimbulkan dispatch ganda |
| IDEM-05 | Duplicate event dari simulator | Event diproses idempotently |

Jalankan hanya pada connector mock/sandbox. Batasi retry dan concurrency sesuai RoE.

### Callback test matrix

| Case | Fixture | Expected secure behavior |
|---|---|---|
| CB-01 | Signature/authentication valid | Diproses jika state transition valid |
| CB-02 | Signature/authentication invalid | Ditolak tanpa mengubah transaction state |
| CB-03 | Event ID duplikat | Tidak menimbulkan efek kedua |
| CB-04 | Timestamp/freshness tidak memenuhi policy | Ditolak atau ditangani sesuai protocol |
| CB-05 | Event out-of-order | Ditolak atau masuk exception handling menurut state machine |
| CB-06 | Unknown synthetic transaction reference | Tidak mengubah transaksi lain |

Gunakan callback simulator. Jangan mengirim event buatan ke payment partner aktual atau endpoint production.

### Limit test matrix

- Nilai normal di bawah limit.
- Boundary value yang secara eksplisit ditetapkan untuk test.
- Nilai di atas limit pada sandbox jika diizinkan.
- Dua request terkontrol untuk memeriksa atomicity jika concurrency test disetujui.
- Limit lintas channel atau actor hanya jika memang termasuk scope.

Catat nilai yang diuji, policy version, accepted/rejected result, serta state. Jangan melakukan load testing atau mencari batas maksimum dengan volume yang meningkat terus-menerus.

## 8. Tahap 6: Validasi temuan dan pelaporan

Sebelum menyatakan finding:
1. Reproduksi sekali dengan kondisi yang sama.
2. Pastikan test dijalankan pada aset yang benar dan masuk scope.
3. Pastikan expected behavior didukung policy, requirement, atau role matrix.
4. Tunjukkan observed behavior dan state yang relevan.
5. Catat attack preconditions dan batas akses yang diperlukan.
6. Hitung impact berdasarkan bukti, bukan asumsi worst-case saja.
7. Hindari mengulang tindakan ketika efek sudah terbukti.
8. Siapkan langkah retest yang menguji kontrol yang sama setelah remediasi.

### Bug report structure

- **Title:** nama flaw dan komponen.
- **Summary:** satu paragraf tentang kontrol yang gagal.
- **Affected endpoint/component:** hanya aset dalam scope.
- **Prerequisites:** role, session, test fixture, state awal.
- **Steps to reproduce:** langkah ringkas dan repeatable.
- **Expected vs actual:** perilaku policy versus observasi.
- **Evidence:** request/response tersanitasi, state, timestamp, correlation ID.
- **Impact:** dampak yang dibuktikan dan risiko potensial yang dipisahkan.
- **Severity rationale:** exposure, privilege, business impact, control compensation.
- **Remediation:** perbaikan teknis yang relevan dengan root cause.
- **Retest criteria:** kondisi lulus yang spesifik.

### Severity sanity check

Pertanyaan sebelum memberi severity:
- Apakah bukti menunjukkan read, write, approval, dispatch, atau financial effect?
- Apakah dampak terjadi pada objek sintetis atau ada bukti dampak lain?
- Apakah actor membutuhkan role tertentu?
- Apakah alur lain mencegah efek setelah kontrol pertama gagal?
- Apakah ada audit/detection dan mekanisme rollback?
- Apakah finding reproducible dan tidak bergantung pada asumsi?

Attack-path score digunakan untuk prioritas test. Severity finding mengikuti rubric engagement.

## 9. Pelaporan dan evidence checklist

Setiap test record minimal berisi:
- Test ID dan Path ID.
- Endpoint/method yang disetujui.
- Role, test account, dan object owner.
- State sebelum dan sesudah.
- Request/response yang telah disunting.
- Timestamp dan correlation ID.
- Expected behavior dan actual behavior.
- Result: PASS, FAIL, INCONCLUSIVE, BLOCKED, atau NOT TESTED.
- Business impact dan batas analisis.
- Cleanup status dan reviewer.

Raw evidence disimpan di evidence store engagement. Repository dokumentasi hanya memuat contoh sintetis dan template.

## 10. Referensi teknis

- [OWASP API Security Top 10 2023](https://api-security.owasp.org/editions/2023/en/0x11-t10/)
- [OWASP Web Security Testing Guide: Business Logic Testing](https://wstg.org/stable/4-Web_Application_Security_Testing/10-Business_Logic_Testing/)
- [OWASP ASVS 5.0.0](https://asvs.owasp.org/)
- [Remittance Threat Model](03-remittance-threat-model.md)
- [Remittance Test Catalog](10-remittance-test-catalog.md)
- [Attack-Path Matrix](11-remittance-attack-path-matrix.md)
- [Sequence Diagrams](12-remittance-sequence-diagrams.md)
