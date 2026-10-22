# Remittance Threat Model

## Tujuan

Threat model ini membantu menyusun pengujian keamanan pada sistem remittance. Sesuaikan aktor, komponen, alur, dan kontrol dengan arsitektur serta proses bisnis pada engagement.

## Alur bisnis konseptual

1. Pengguna melakukan autentikasi melalui channel.
2. Pengguna membuat atau memilih beneficiary.
3. Sistem memvalidasi data penerima, limit, biaya, nilai tukar, dan aturan bisnis yang berlaku.
4. Instruksi transaksi dibuat dan dicatat pada status awal.
5. Approval dilakukan sesuai pemisahan kewenangan, termasuk maker-checker jika diterapkan.
6. Instruksi diteruskan ke integrasi atau payment rail yang digunakan.
7. Status transaksi, notifikasi, audit log, dan rekonsiliasi diperbarui.
8. Exception, reversal, atau dispute ditangani sesuai workflow operasional.

Verifikasi setiap langkah terhadap proses bisnis yang masuk scope sebelum menyusun test case.

## Trust boundaries

Petakan komponen dan aliran data pada batas berikut:
- Client/browser/mobile app ke channel dan backend.
- Channel/API gateway ke service layer.
- Service remittance ke core banking, payment service, atau integration layer.
- Identity/session management ke fungsi transaksi.
- Maker, checker, administrator, dan support.
- Transaction processing ke logging, monitoring, dan reconciliation.
- Vendor atau third-party integration yang termasuk scope.

## Aset dan tujuan keamanan

| Aset | Tujuan keamanan | Fokus pengujian |
|---|---|---|
| Identitas dan session | Kerahasiaan dan autentisitas | Lifecycle token, session binding, expiry, dan privilege |
| Customer dan beneficiary | Kerahasiaan dan integritas | Akses berdasarkan owner dan role |
| Instruksi transaksi | Integritas | Validasi amount, currency, fee, beneficiary, reference, dan state |
| Approval | Otorisasi dan akuntabilitas | Pemisahan tugas serta approval pada state yang benar |
| Transaction status | Integritas dan konsistensi | Transisi status dan sinkronisasi antarservice |
| Audit trail | Akuntabilitas | Kelengkapan dan korelasi event |
| Integration messages | Integritas dan autentisitas | Validasi pesan, trust boundary, serta penanganan error |

## Kelompok hipotesis pengujian

Skenario berikut merupakan kandidat test. Sertakan dalam rencana hanya jika sesuai scope dan RoE.

| ID | Hipotesis | Dampak yang dinilai | Metode validasi |
|---|---|---|---|
| RT-AUTH-01 | Otorisasi object-level tidak diterapkan secara konsisten | Akses lintas pengguna ke transaksi atau beneficiary | Gunakan dua akun uji dan objek sintetis |
| RT-ROLE-01 | Pemisahan maker/checker/admin tidak diterapkan secara memadai | Approval atau administrasi di luar kewenangan | Gunakan akun per-role pada environment uji |
| RT-INT-01 | Atribut kritis transaksi tidak divalidasi di server | Instruksi transaksi dapat berubah tanpa kontrol yang semestinya | Gunakan transaksi sintetis dan jalur tanpa settlement |
| RT-WF-01 | Transisi workflow tidak dibatasi menurut state | Bypass approval atau perubahan status yang tidak sah | Validasi transisi pada environment uji |
| RT-REPLAY-01 | Proteksi duplicate/replay tidak memadai | Pemrosesan ganda atau status tidak konsisten | Gunakan harness/sandbox dan batas pengujian yang ditetapkan |
| RT-API-01 | Enforcement kontrol API berbeda antarendpoint | Akses atau aksi tanpa otorisasi | Uji kombinasi role dan resource uji |
| RT-LOG-01 | Event penting tidak dicatat secara lengkap | Kesenjangan monitoring dan audit | Korelasikan aktivitas uji dengan log yang tersedia |
| RT-DATA-01 | Respons atau export mengembalikan data berlebih | Paparan PII atau data finansial | Gunakan dataset sintetis dan evaluasi minimisasi data |

## Prioritisasi

Nilai skenario berdasarkan dampak bisnis, kemungkinan penyalahgunaan, relevansi dengan requirement, kelayakan pengujian, risiko operasional, kualitas evidence, serta kemampuan melakukan retest.

Keberadaan hipotesis di threat model tidak berarti kelemahan telah ditemukan. Hasil hanya dapat ditetapkan setelah test case dijalankan dan evidence ditinjau.
