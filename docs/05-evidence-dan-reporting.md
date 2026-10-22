# Evidence, Reporting, dan Retest

## Tujuan evidence

Evidence harus memungkinkan evaluator atau pemilik sistem memahami apa yang diuji, perilaku yang diamati, dampaknya, dan cara memverifikasinya tanpa mengulang aktivitas berisiko.

## Evidence chain minimum

Setiap artefak evidence harus terkait dengan:
- Engagement / PoC ID.
- Test case ID dan finding ID (jika ada).
- Timestamp dengan zona waktu yang konsisten.
- Actor/role dan test account yang digunakan.
- Target yang memang telah disetujui.
- Object/transaction ID sintetis.
- Kondisi sebelum dan sesudah pengujian.
- Hasil aktual dan expected secure behavior.
- Nama pengumpul bukti dan reviewer.
- Redaction status dan lokasi penyimpanan yang disetujui.

## Praktik pengumpulan yang aman

1. Gunakan data dan akun sintetis.
2. Redaksi password, token, session cookie, API key, PII, dan informasi finansial.
3. Jangan memasukkan raw evidence ke repository GitHub publik.
4. Pertahankan versi bukti asli pada evidence vault dengan akses terbatas jika kebijakan klien mengizinkan.
5. Buat salinan tersanitasi untuk laporan dan presentasi.
6. Catat hash artefak bila diperlukan untuk membantu pemeriksaan integritas.
7. Jangan membuat screenshot yang menampilkan data nasabah lain.
8. Rekam keterbatasan dan kondisi pengujian agar hasil tidak disalahartikan.

## Model folder evidence

Gunakan struktur pada repository internal yang disetujui klien. Jangan buat folder evidence private seolah-olah aman di repository publik.

Contoh struktur konseptual:
- engagement-metadata/
- test-cases/
- raw-evidence-restricted/
- sanitized-evidence/
- findings/
- retest/
- closeout/

Raw evidence harus dibatasi aksesnya, memiliki retensi terdefinisi, dan dihapus sesuai persetujuan engagement.

## Struktur laporan teknis

1. Cover, klasifikasi, versi, dan kontrol distribusi.
2. Executive summary.
3. Tujuan, scope, environment, dan batasan.
4. Metodologi dan aturan pelaksanaan.
5. Ringkasan cakupan pengujian.
6. Ringkasan temuan menurut severity dan business process.
7. Detail setiap temuan: deskripsi, bukti, dampak, akar masalah, rekomendasi.
8. Test yang PASS, FAIL, INCONCLUSIVE, BLOCKED, dan NOT TESTED.
9. Retest status dan pengecualian risiko.
10. Appendix: test inventory, glossary, dan evidence index.

## Executive dashboard

| Dimensi | Indikator |
|---|---|
| Severity | Jumlah temuan per severity setelah proses triage |
| Business process | Risiko menurut tahap remittance yang benar-benar berada dalam scope |
| Control family | AuthN/AuthZ, transaction integrity, workflow, API, auditability, data exposure |
| Evidence confidence | High/Medium/Low dengan alasan yang terdefinisi |
| Remediation | Open, in progress, fixed, accepted risk, retest pending |
| Coverage | Test planned vs tested vs blocked vs not tested |
| Retest | Pass, fail, inconclusive, dan tanggal verifikasi |

Jangan mengklaim coverage 100% kecuali definisi scope, denominator, pengecualian, serta metode coverage dijelaskan.

## Kualitas temuan

Sebuah finding yang baik:
- Menjelaskan fakta observasi dengan bahasa yang dapat direproduksi.
- Memetakan dampak potensial pada transaksi, fraud exposure, confidentiality, integrity, availability, atau auditability yang relevan.
- Memisahkan root cause yang terverifikasi dari dugaan.
- Menetapkan rekomendasi berurutan: containment, perbaikan teknis, compensating control, serta perbaikan jangka panjang.
- Menyebutkan retest criteria yang terukur.

## Retest criteria

Kriteria retest ditetapkan saat temuan ditulis, bukan setelah perbaikan selesai. Contoh:
- Request tanpa kewenangan selalu ditolak server-side dan tidak membocorkan isi objek.
- Atribut transaksi kritis hanya dapat diubah melalui state/role yang diizinkan.
- Transisi approval yang tidak valid ditolak serta meninggalkan audit trail yang konsisten.
- Event keamanan dan transaksi uji dapat dikorelasikan pada log yang ditetapkan.
- Test case awal dan negative-control case menghasilkan hasil sesuai ekspektasi.

Retest hanya pada scope dan environment yang disetujui ulang bila perubahan memerlukannya.
