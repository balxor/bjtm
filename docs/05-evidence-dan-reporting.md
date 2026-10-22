# Evidence, Reporting, dan Retest

## Tujuan evidence

Evidence mendukung reproduksi test, peninjauan hasil, penilaian dampak, serta validasi remediasi. Evidence harus terkait dengan test case dan tidak boleh memuat data yang tidak diperlukan.

## Evidence chain minimum

Catat informasi berikut untuk setiap artefak:
- Engagement ID.
- Test case ID dan finding ID, jika ada.
- Timestamp beserta zona waktu.
- Actor/role dan akun uji.
- Target yang diuji.
- ID objek atau transaksi sintetis.
- Kondisi sebelum dan sesudah pengujian.
- Observasi dan expected behavior.
- Nama pengumpul dan reviewer.
- Status redaction dan lokasi penyimpanan.

## Pengumpulan dan penyimpanan

1. Gunakan akun uji dan data sintetis.
2. Redaksi password, token, cookie, API key, PII, dan data finansial.
3. Simpan raw evidence pada evidence store yang memiliki kontrol akses.
4. Buat salinan tersanitasi untuk laporan dan presentasi.
5. Catat hash artefak jika dibutuhkan untuk pemeriksaan integritas.
6. Cantumkan keterbatasan bukti dan kondisi pengujian.
7. Terapkan retensi dan penghapusan sesuai kebijakan engagement.
8. Batasi distribusi evidence berdasarkan kebutuhan pekerjaan.

## Struktur evidence store

- engagement-metadata/
- test-cases/
- raw-evidence-restricted/
- sanitized-evidence/
- findings/
- retest/
- closeout/

Tetapkan owner, kontrol akses, retensi, dan mekanisme penghapusan sebelum evidence dikumpulkan.

## Struktur laporan teknis

1. Cover, klasifikasi, versi, dan distribusi.
2. Executive summary.
3. Tujuan, scope, environment, dan batasan.
4. Metodologi dan aturan pelaksanaan.
5. Ringkasan cakupan pengujian.
6. Temuan berdasarkan severity dan proses bisnis.
7. Detail temuan: deskripsi, evidence, dampak, analisis penyebab, dan rekomendasi.
8. Daftar test PASS, FAIL, INCONCLUSIVE, BLOCKED, dan NOT TESTED.
9. Status retest dan pengecualian risiko.
10. Appendix: test inventory, glossary, dan evidence index.

## Executive dashboard

| Dimensi | Indikator |
|---|---|
| Severity | Jumlah finding per severity |
| Business process | Risiko pada tahapan proses yang diuji |
| Control family | AuthN/AuthZ, transaction integrity, workflow, API, auditability, data exposure |
| Evidence confidence | High/Medium/Low dengan alasan |
| Remediation | Open, in progress, fixed, accepted risk, retest pending |
| Coverage | Planned, tested, blocked, not tested |
| Retest | Pass, fail, inconclusive, tanggal, dan owner |

Jelaskan denominator serta pengecualian saat melaporkan coverage. Jangan menyimpulkan keamanan keseluruhan sistem dari cakupan test yang terbatas.

## Kualitas finding

Setiap finding harus:
- Menjelaskan observasi dan langkah reproduksi yang sesuai scope.
- Menghubungkan dampak dengan kerahasiaan, integritas, ketersediaan, transaksi, fraud exposure, atau auditability.
- Memisahkan penyebab yang didukung evidence dari hipotesis.
- Menetapkan containment, perbaikan teknis, compensating control, dan tindakan jangka panjang bila relevan.
- Menetapkan owner, prioritas, serta acceptance criteria retest.

## Retest criteria

Tentukan kriteria retest saat finding dibuat. Contoh:
- Request tanpa otorisasi ditolak di server dan tidak mengembalikan data objek.
- Atribut transaksi kritis hanya dapat diubah melalui role dan state yang diizinkan.
- Transisi approval yang tidak valid ditolak dan tercatat.
- Aktivitas uji dapat dikorelasikan dengan event log yang relevan.
- Test case awal dan negative-control case menghasilkan output sesuai expected behavior.

Retest dilakukan dalam scope dan environment yang ditetapkan untuk engagement.


## Klasifikasi OWASP dan scoring temuan

Setiap finding mencantumkan kategori utama [OWASP Top 10:2025](17-owasp-top10-2025-mapping.md), serta OWASP API Security Top 10:2023 atau CWE bila relevan. Kategori ditentukan berdasarkan root cause yang didukung evidence, bukan sekadar nama test.

Dalam laporan, pisahkan technical severity/CVSS, dampak bisnis yang diamati, potential impact, attack-path risk score, internal finding-priority score, dan poin evaluasi tender. Nilai dalam [Scoring Temuan untuk Persiapan Evaluasi Bank Jatim](18-scoring-temuan-bank-jatim.md) merupakan simulasi internal dan tidak boleh disampaikan sebagai rubrik resmi Bank Jatim.
