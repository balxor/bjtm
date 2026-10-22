# Remittance Threat Model

## Tujuan dan batasan

Dokumen ini adalah model ancaman konseptual untuk merancang pengujian pada sistem remittance. Ini **bukan representasi arsitektur Bank Jatim**. Sesuaikan alur, aktor, sistem, serta kontrol setelah memperoleh diagram dan requirement yang disetujui.

## Alur bisnis konseptual

1. User mengakses channel dan melakukan autentikasi.
2. User membuat atau memilih penerima/beneficiary.
3. Sistem memvalidasi data, limit, fee/FX, serta aturan yang relevan.
4. Transaksi dibuat dan disimpan sebagai status awal.
5. Approval dijalankan sesuai model kewenangan, misalnya maker-checker jika memang diterapkan.
6. Transaksi dikirim melalui integrasi/payment rail yang disetujui.
7. Status, notifikasi, audit log, dan rekonsiliasi diperbarui.
8. Exception, reversal, atau dispute ditangani melalui proses yang terdefinisi.

Setiap langkah harus diverifikasi terhadap proses aktual sebelum test plan disahkan.

## Trust boundaries yang perlu dipetakan

- Client/browser/mobile app ke channel/backend.
- Channel/API gateway ke service layer.
- Service remittance ke sistem core/payment/integration.
- Identitas dan session management ke fungsi transaksi.
- Maker/checker/admin/support serta batas antar-role.
- Transaction processing ke logging, monitoring, dan reconciliation.
- Vendor/third-party integration jika tercantum dalam scope.

## Aset dan tujuan keamanan

| Aset | Tujuan keamanan | Pertanyaan pengujian |
|---|---|---|
| Identitas/session | Kerahasiaan dan autentisitas | Apakah session/token ditangani dan dibatasi dengan benar? |
| Customer/beneficiary | Kerahasiaan dan integritas | Apakah akses dibatasi pada objek yang diotorisasi? |
| Instruksi transaksi | Integritas | Apakah amount, currency, fee, beneficiary, reference, dan status divalidasi server-side? |
| Approval | Otorisasi dan non-repudiation | Apakah approval hanya dilakukan role berwenang dan pada state yang benar? |
| Transaction status | Integritas dan konsistensi | Apakah transisi status dibatasi dan konsisten di seluruh komponen? |
| Audit trail | Akuntabilitas | Apakah tindakan penting dicatat dan dapat ditelusuri? |
| Integration messages | Integritas dan autentisitas | Apakah trust boundary dan validasi pesan dijaga? |

## Keluarga hipotesis pengujian

Semua skenario berikut bersifat usulan dan hanya berlaku jika disetujui dalam scope.

| ID | Hipotesis | Dampak yang hendak dinilai | Validasi aman |
|---|---|---|---|
| RT-AUTH-01 | Otorisasi object-level mungkin tidak konsisten | Akses lintas pengguna terhadap objek transaksi/beneficiary | Gunakan dua akun uji dan objek sintetis yang telah ditetapkan |
| RT-ROLE-01 | Pemisahan maker/checker/admin mungkin lemah | Approval atau administrasi di luar kewenangan | Gunakan akun per-role; jangan memproses dana nyata |
| RT-INT-01 | Atribut kritis transaksi mungkin tidak divalidasi server-side | Perubahan instruksi tanpa kontrol yang semestinya | Gunakan transaksi sintetis dan pastikan jalur tidak dapat settlement |
| RT-WF-01 | State transition mungkin tidak dibatasi | Bypass workflow atau approval | Validasi transisi state pada environment aman |
| RT-REPLAY-01 | Duplicate/replay protection mungkin tidak memadai | Pemrosesan ganda atau status tidak konsisten | Hanya pada harness/transaction sandbox dengan batas yang disetujui |
| RT-API-01 | Kontrol API mungkin tidak sama kuat pada semua endpoint | Akses atau aksi tak sah | Bandingkan perilaku terhadap role dan resource uji |
| RT-LOG-01 | Event penting mungkin tidak tercatat lengkap | Kesenjangan deteksi dan audit | Cocokkan aktivitas uji dengan audit/SIEM yang disetujui |
| RT-DATA-01 | Respons atau export mungkin mengekspos data berlebih | Paparan PII/financial data | Gunakan dataset sintetis; cek redaction dan minimisasi |

## Prioritisasi

Nilai skenario berdasarkan:
- Dampak bisnis/transaksi jika hipotesis terbukti.
- Relevansi dengan scope dan kriteria tender.
- Kelayakan demonstrasi di environment yang tersedia.
- Risiko operasional selama pengujian.
- Kualitas bukti yang dapat dikumpulkan.
- Relevansi remediasi dan retest.

Jangan menyimpulkan suatu kelemahan ada hanya karena masuk threat model. Status awal setiap hipotesis adalah **belum diuji**.
