# Evaluator Demo Run Sheet: Remittance PoC

## 1. Tujuan

Run sheet ini digunakan oleh presenter, test operator, evidence lead, dan engagement lead untuk menjalankan demonstrasi PoC yang konsisten. Agenda dapat disesuaikan dengan waktu dan rubric evaluasi tender.

## 2. Peran

| Role | Tanggung jawab saat demo |
|---|---|
| Presenter / engagement lead | Menjelaskan objective, scope, hasil, impact, remediation, dan limitation |
| Test operator | Menjalankan request pada target dan akun yang disetujui |
| Evidence lead | Mencatat timestamp, test ID, correlation ID, before/after state, dan evidence reference |
| Safety controller | Memantau batas RoE serta memerintahkan stop jika ada risiko |
| Client system observer | Membantu memeriksa state/log yang diizinkan dan mengonfirmasi cleanup |

Satu personel dapat memegang beberapa peran jika kontrol keselamatan serta quality review tetap berjalan.

## 3. Agenda demonstrasi 30 menit

| Waktu | Bagian | Artefak yang ditampilkan | Exit criteria |
|---|---|---|---|
| 00:00-03:00 | Objective dan test boundaries | Scope excerpt, actor matrix, stop conditions | Semua pihak memahami batas test |
| 03:00-08:00 | POC-01 Authorization | Baseline dan negative-test comparison, object state | Verdict didukung response dan state |
| 08:00-14:00 | POC-02 Transaction integrity | Property matrix, expected vs actual server-side value | Validasi atribut bisa diperiksa |
| 14:00-19:00 | POC-03 Approval binding | Transaction version, approval record, submit decision | Approval terikat ke state/versi yang sesuai |
| 19:00-24:00 | POC-04 Idempotency | Retry record, dispatch count, sandbox result | Hasil satu logical transaction bisa direkonsiliasi |
| 24:00-28:00 | POC-05 Callback/reconciliation | Callback fixture, state, ledger sandbox/reference, audit | Event dan state konsisten |
| 28:00-30:00 | Ringkasan dan Q&A | Finding register, remediation, retest criteria | Evaluator menerima kesimpulan terukur |

Jika rubric resmi menentukan urutan lain, susun ulang agenda dan catat alasannya.

## 4. Script pembuka

Presenter harus menyampaikan:
1. Business process dan kontrol yang akan diuji.
2. Scope serta exclusions untuk demonstrasi.
3. Environment yang digunakan dan jenis data uji.
4. Akun/role yang akan dipakai.
5. Batas request, retry, serta transaksi.
6. Stop conditions dan penanganan jika terjadi kondisi tak terduga.
7. Cara membedakan live evidence, test fixture, dan ilustrasi arsitektur.

Jangan menyatakan bahwa test dilakukan live jika yang ditampilkan merupakan fixture atau evidence dari rehearsal sebelumnya.

## 5. Checklist sebelum demo

### Environment
- [ ] Host, route, API version, dan environment cocok dengan scope.
- [ ] Login dan akun uji diverifikasi.
- [ ] Test data serta state awal tersedia.
- [ ] Test connector/mock tidak dapat menyebabkan settlement dana nyata.
- [ ] Akses ke read-only state view atau audit log berfungsi.
- [ ] Waktu sistem dan zona waktu dicatat.
- [ ] Batas request dan jumlah transaksi siap dipantau.

### Evidence
- [ ] Test ID dan Path ID dibuat sebelum demonstrasi.
- [ ] Capture workflow diuji.
- [ ] Secret serta data pribadi otomatis/manual disunting.
- [ ] Before/after state dapat dibuktikan.
- [ ] Evidence store mempunyai akses terbatas.
- [ ] Finding template dan demo scorecard siap.
- [ ] Evidence dari rehearsal diberi label sebagai rehearsal evidence, bukan live result.

### Safety
- [ ] Safety controller ditetapkan.
- [ ] Emergency contact tersedia.
- [ ] Stop phrase dan jalur eskalasi diketahui tim.
- [ ] Cleanup dan rekonsiliasi telah disiapkan.
- [ ] Pemilik sistem dapat mengonfirmasi bahwa tidak ada payment rail produksi yang terlibat.

## 6. Run sheet per skenario

### POC-01: Authorization boundary

**Presenter:** jelaskan actor A, owner objek, dan expected policy.

**Operator:**
1. Jalankan positive control dengan owner objek.
2. Jalankan negative test sesuai test case dan RoE.
3. Tampilkan request/response comparison.

**Evidence lead:** tampilkan actor, object owner, status response, object state, timestamp, dan audit event bila ada.

**Verdict:** PASS jika akses lintas-owner ditolak serta state tidak berubah. FAIL jika data nonpublik diakses atau objek berubah tanpa entitlement.

**Stop:** hentikan variasi setelah bukti akses lintas-owner cukup.

### POC-02: Transaction property integrity

**Presenter:** jelaskan properti yang seharusnya divalidasi server-side.

**Operator:**
1. Tampilkan transaksi baseline.
2. Tampilkan satu negative test dari test fixture/sandbox untuk field yang diizinkan.
3. Periksa nilai tersimpan melalui read-only view.

**Evidence lead:** bandingkan expected property, request, persisted value, validation result, dan state.

**Verdict:** PASS jika server mempertahankan policy; FAIL jika field server-controlled memengaruhi state tanpa kontrol yang diwajibkan.

**Stop:** jangan lanjutkan ke adapter/payment rail untuk negative test.

### POC-03: Approval binding

**Presenter:** jelaskan maker, checker, transaction version, dan approval policy.

**Operator:**
1. Tampilkan transaction version saat dibuat.
2. Tampilkan approval record.
3. Tampilkan hasil uji approved transaction pada fixture non-settlement.
4. Tampilkan submit eligibility decision.

**Evidence lead:** korelasikan version, approval record, role, dan audit event.

**Verdict:** PASS jika approval terikat pada instruksi yang benar; FAIL jika workflow memperbolehkan submit dengan approval yang tidak valid menurut policy.

### POC-04: Idempotency

**Presenter:** jelaskan risiko timeout dan retry pada satu logical transaction.

**Operator:**
1. Jalankan satu transaksi sintetis melalui mock/sandbox.
2. Tampilkan timeout fixture jika digunakan.
3. Jalankan retry terbatas.
4. Tampilkan dispatch count, logical transaction reference, dan sandbox result.

**Evidence lead:** pastikan semua count dan reference sesuai hasil yang diklaim.

**Verdict:** PASS jika satu logical instruction menghasilkan maksimal satu efek bisnis; FAIL jika duplicate effect dibuktikan.

### POC-05: Callback dan reconciliation

**Presenter:** jelaskan event authenticity, deduplication, state transition, dan reconciliation.

**Operator:**
1. Tampilkan callback valid dari simulator.
2. Tampilkan fixture invalid atau duplicate yang sudah disetujui.
3. Periksa state transaction, external reference, reconciliation outcome, dan audit event.

**Evidence lead:** buat satu baris timeline dengan timestamp dan correlation ID.

**Verdict:** PASS jika event diproses sesuai policy dan mismatch ditangani; FAIL jika event tidak valid mengubah state atau menghasilkan efek yang tidak sesuai.

## 7. Template live evidence timeline

| Timestamp | Test ID | Actor/role | Action | Expected | Observed | State reference | Evidence ID |
|---|---|---|---|---|---|---|---|
| TBD | POC-01 | ACT-CUST-A | Read Object A | Allow | TBD | TBD | TBD |
| TBD | POC-01 | ACT-CUST-A | Read Object B | Deny | TBD | TBD | TBD |
| TBD | POC-02 | ACT-MAKER-A | Property validation test | Policy-based | TBD | TBD | TBD |
| TBD | POC-03 | ACT-CHECKER-B | Approve transaction version | Policy-based | TBD | TBD | TBD |
| TBD | POC-04 | ACT-MAKER-A | Controlled retry | Idempotent result | TBD | TBD | TBD |
| TBD | POC-05 | SVC-CALLBACK-SIM | Callback fixture | Policy-based | TBD | TBD | TBD |

Isi kolom Observed dan State reference hanya setelah aktivitas dijalankan. Jangan mengisi expected result sebagai hasil aktual.

## 8. Fallback jika live demo gagal

Jika koneksi, akun, mock service, atau environment gagal:

1. Hentikan pengujian aktif sesuai stop procedure.
2. Catat waktu, kondisi, pesan error, dan komponen yang bermasalah.
3. Jangan mengulang request berisiko untuk memaksa hasil.
4. Tunjukkan diagram, test case, expected behavior, dan bukti rehearsal yang memiliki label jelas.
5. Jelaskan bagian yang belum dapat diuji serta dampaknya pada kesimpulan.
6. Jadwalkan pengujian ulang hanya setelah prasyarat dan izin siap.

Rehearsal evidence dapat menunjukkan kesiapan metode, tetapi tidak boleh disajikan sebagai hasil pengujian live pada sistem target.

## 9. Closeout checklist

- [ ] Seluruh test diberi status dan rationale.
- [ ] Evidence ditinjau dan disunting.
- [ ] Objek serta transaksi sintetis direkonsiliasi.
- [ ] Tidak ada dispatch/posting yang tidak direncanakan.
- [ ] Test yang blocked atau tidak dijalankan tercatat.
- [ ] Finding memiliki impact, severity rationale, remediation, dan retest criteria.
- [ ] Raw evidence tersimpan sesuai access control dan retensi.
- [ ] Pemilik sistem menerima ringkasan hasil dan cleanup status.

## 10. Dokumen pendukung

- [PoC Priority Scenarios](14-poc-priority-scenarios.md)
- [Synthetic Remittance Test Data Model](15-synthetic-remittance-test-data.md)
- [Remittance Threat Model](03-remittance-threat-model.md)
- [Remittance Test Catalog](10-remittance-test-catalog.md)
- [Attack-Path Matrix](11-remittance-attack-path-matrix.md)
- [Sequence Diagrams](12-remittance-sequence-diagrams.md)
- [API Security Testing Field Playbook](13-api-security-testing-field-playbook.md)
