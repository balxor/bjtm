# Synthetic Remittance Test Data Model

## 1. Tujuan

Model ini menyediakan entitas uji yang konsisten untuk rehearsal, eksekusi test case, evidence capture, dan retest. Seluruh nama dan identifier merupakan contoh sintetis. Jangan menggantinya dengan data rekening, pelanggan, atau transaksi nyata.

## 2. Actor dan role

| Actor ID | Role | Kepemilikan / privilege | Penggunaan |
|---|---|---|---|
| ACT-CUST-A | Customer A | Pemilik objek A | Positive control authorization |
| ACT-CUST-B | Customer B | Pemilik objek B | Pembanding ownership |
| ACT-MAKER-A | Maker | Membuat transaksi sesuai policy | Workflow initiation |
| ACT-CHECKER-B | Checker | Approval sesuai policy | Separation of duties |
| ACT-OPS-READ | Operations read-only | Inquiry/reconciliation read-only | Pengujian akses baca |
| ACT-OPS-ADMIN | Operations admin | Hanya fungsi yang disediakan khusus untuk test | Administrative authorization, bila in-scope |
| SVC-ORCH-TEST | Service identity | Akses antar-service test | Correlation dan service identity |
| SVC-CALLBACK-SIM | Callback simulator | Mengirim event uji ke simulator endpoint | Callback validation |

Sederhanakan daftar ini jika sistem tidak mendukung role terkait. Jangan menambahkan akun atau role privileged ke sistem target tanpa persetujuan tertulis.

## 3. Objek sintetis

| Object ID | Owner | State | Tujuan |
|---|---|---|---|
| BEN-A-001 | ACT-CUST-A | Active | Positive control beneficiary |
| BEN-B-001 | ACT-CUST-B | Active | Cross-owner negative test |
| TX-A-001 | ACT-CUST-A / ACT-MAKER-A | DRAFT | Baseline create/update |
| TX-A-002 | ACT-CUST-A / ACT-MAKER-A | PENDING_APPROVAL | Approval workflow |
| TX-A-003 | ACT-CUST-A / ACT-MAKER-A | APPROVED | Approval-binding test |
| TX-A-004 | ACT-CUST-A / ACT-MAKER-A | SUBMITTED/PENDING pada sandbox | Idempotency/retry |
| TX-A-005 | ACT-CUST-A / ACT-MAKER-A | State ditentukan simulator | Callback/reconciliation |
| QUOTE-TST-001 | Test quote service | Valid | Fee/FX baseline |
| QUOTE-TST-002 | Test quote service | Expired fixture | Quote expiry negative test |

Identifier ini adalah label logis. Jika API memerlukan UUID atau format tertentu, gunakan identifier yang dibuat environment uji.

## 4. Transaction fixtures

| Fixture ID | Kondisi | Tujuan | Expected outcome |
|---|---|---|---|
| FIX-TXN-BASE | Amount, currency, beneficiary, dan rule set valid | Alur normal | Diterima sesuai policy |
| FIX-TXN-OWNER-MISMATCH | Beneficiary bukan milik actor dan tidak ada delegation | Object authorization | Ditolak tanpa state change |
| FIX-TXN-INVALID-PROPERTY | Field server-controlled diuji melalui test fixture | Property authorization | Ditolak/diabaikan sesuai kontrak |
| FIX-TXN-STALE-APPROVAL | Versi transaksi berbeda dari approved version | Approval binding | Submit ditolak atau re-approval diwajibkan |
| FIX-TXN-RETRY | Logical transaction dengan retry terkendali | Idempotency | Maksimal satu efek bisnis |
| FIX-CB-DUPLICATE | Callback event ID sama | Duplicate event handling | Tidak ada efek baru |
| FIX-CB-INVALID-AUTH | Callback simulator dengan metadata autentikasi tidak valid | Callback authenticity | Ditolak tanpa state change |
| FIX-CB-OUT-OF-ORDER | Event tidak sesuai state saat ini | State transition | Ditolak atau masuk exception sesuai policy |
| FIX-DOWNSTREAM-TIMEOUT | Mock partner tidak memberi hasil final | Timeout recovery | Pending/unknown dan reconciliation sesuai desain |
| FIX-RECON-MISMATCH | Record internal dan downstream berbeda | Reconciliation | Exception dibuat dan dapat ditelusuri |

Fixture negatif hanya dibuat pada simulator, test harness, atau environment uji yang dikendalikan. Jangan mengedit record transaksi produksi untuk menghasilkan skenario negatif.

## 5. Data dictionary atribut transaksi

| Field | Kategori | Sumber otoritatif yang diharapkan | Perilaku validasi |
|---|---|---|---|
| transactionId | Identifier | Transaction service | Bukan satu-satunya dasar authorization |
| customerId / tenantId | Ownership | Identity context dan record server | Caller tidak dapat mengganti ownership tanpa policy |
| beneficiaryId | Reference | Beneficiary service | Ownership, status, dan product rule divalidasi |
| amount | Finansial | Input dengan validasi server | Range, precision, limit, dan product rule |
| currency | Finansial | Product/rule policy | Hanya currency yang didukung untuk flow terkait |
| fee | Finansial | Fee/rule service | Nilai dihitung atau divalidasi server-side |
| fxQuoteId / quoteVersion | Quote | Quote service | Expiry dan versi diperiksa |
| transactionStatus | State | Workflow/orchestrator | Tidak diperbarui sebagai properti bebas |
| approvalStatus | State/authorization | Workflow service | Actor dan transition mengikuti policy |
| screeningResult | Kontrol | Risk/compliance service jika digunakan | Caller tidak dapat memaksakan outcome |
| idempotencyKey | Request identity | Client atau kontrak API, diproses server-side | Scope, uniqueness, dan payload binding dijaga |
| businessReference | Business reference | Transaction service | Konsistensi dan duplicate handling |
| externalReference | Downstream | Connector/payment simulator | Divalidasi dan dikorelasikan |
| correlationId | Observability | Gateway/orchestrator | Diteruskan konsisten, bukan faktor authorization |
| createdBy / approvedBy | Audit | Identity/workflow service | Diisi dari actor terautentikasi |

Petakan nama field ke schema aplikasi sebelum eksekusi.

## 6. State fixture

| State | Makna | Operasi relevan | Fokus test |
|---|---|---|---|
| DRAFT | Instruksi belum final | Create/update/validate | Ownership dan property validation |
| VALIDATED | Validasi awal lulus | Request approval atau proceed | Limit/rule binding |
| PENDING_APPROVAL | Menunggu checker | Approve/reject | Role dan separation of duties |
| APPROVED | Approval selesai | Submit jika syarat terpenuhi | Approval binding |
| SUBMITTED | Instruksi diserahkan ke adapter | Query/recovery | Retry/idempotency |
| PENDING / UNKNOWN | Hasil downstream belum dipastikan | Inquiry/reconciliation | Timeout recovery |
| PROCESSING | Downstream memproses | Status update | Callback ordering |
| COMPLETED | Selesai menurut definisi produk | Inquiry/reconciliation | Final state dan audit |
| FAILED / REJECTED | Gagal atau ditolak | Inquiry, permitted retry/recreate | Retry policy |
| RETURNED / REVERSED | Return/reversal sesuai produk | Exception/reconciliation | Dual control dan audit |

Daftar ini bersifat generik. Gunakan state machine aktual saat menentukan expected behavior.

## 7. Expected state untuk lima skenario utama

| Scenario | State awal | Operasi uji | Expected result |
|---|---|---|---|
| POC-01 Object authorization | BEN-A-001 dan BEN-B-001 aktif | ACT-CUST-A meminta objek milik ACT-CUST-B | Ditolak; state kedua objek tidak berubah |
| POC-02 Property integrity | TX-A-001 pada DRAFT | Negative test field server-controlled melalui sandbox | Field ditolak/diabaikan; tidak ada dispatch |
| POC-03 Approval binding | TX-A-003 pada APPROVED | Versi atribut kritis berubah melalui fixture yang disetujui | Approval invalid atau re-approval sesuai policy |
| POC-04 Idempotency | TX-A-004 pada state sandbox | Retry terbatas dengan logical transaction yang sama | Satu logical outcome dan maksimal satu efek |
| POC-05 Callback/reconciliation | TX-A-005 pada state simulator | Event valid, invalid, duplicate, atau mismatch | State mengikuti policy dan mismatch ditangani |

## 8. Checklist data uji

Sebelum test:
- [ ] Semua actor adalah akun uji.
- [ ] Semua beneficiary dan transaksi dibuat khusus untuk PoC.
- [ ] Tidak ada identifier atau data nasabah nyata.
- [ ] Role matrix telah ditentukan.
- [ ] Field editable dan server-controlled telah dipetakan.
- [ ] State awal dan expected transition dicatat.
- [ ] Sandbox/mock mencegah settlement dana nyata.
- [ ] Batas request, retry, dan concurrency tercantum dalam RoE.
- [ ] Evidence store tersedia.

Setelah test:
- [ ] Semua object ID ditelusuri.
- [ ] State transaksi uji diperiksa dan direkonsiliasi.
- [ ] Tidak ada dispatch/posting yang tidak direncanakan.
- [ ] Event uji dapat dikorelasikan dengan evidence.
- [ ] Cleanup, expiry, atau disposal selesai.
- [ ] Status test dan alasannya dicatat.

## 9. Identifier dan evidence naming convention

~~~text
<ENGAGEMENT-ID>_<SCENARIO-ID>_<TEST-ID>_<UTC-TIMESTAMP>_<ARTIFACT-TYPE>
~~~

Contoh:
~~~text
ENG-POC-01_POC-01_RT-AUTH-01_20261022T030000Z_request-redacted.txt
ENG-POC-01_POC-01_RT-AUTH-01_20261022T030000Z_response-redacted.txt
ENG-POC-01_POC-03_RT-WF-01_20261022T031500Z_state-before-after.md
~~~

Samakan zona waktu di seluruh log. Simpan raw request, token, dan evidence sensitif pada evidence store engagement. Jangan commit artefak tersebut ke repository dokumentasi.

## 10. Relasi dokumen

- [PoC Priority Scenarios](14-poc-priority-scenarios.md)
- [Remittance Test Catalog](10-remittance-test-catalog.md)
- [Attack-Path Matrix](11-remittance-attack-path-matrix.md)
- [Sequence Diagrams](12-remittance-sequence-diagrams.md)
- [OSCP / Bug-Hunter Field Playbook](13-oscp-bughunter-field-playbook.md)
- [Test Case Template](../templates/test-case-template.md)
- [Evidence Log Template](../templates/evidence-log-template.md)
