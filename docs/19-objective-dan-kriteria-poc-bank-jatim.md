# Objective dan Kriteria Evaluasi PoC VAPT Bank Jatim 2026

## 1. Dasar pelaksanaan

Acuan kerja ini adalah dokumen **Jadwal PoC Pengadaan Konsultan Vulnerability Assessment & Penetration Testing (VAPT), 26–30 Oktober 2026** yang diunggah untuk persiapan tim. Dokumen tersebut menetapkan evaluasi kompetensi teknis calon penyedia melalui pengujian pada aplikasi yang sama, dengan scope dan aturan yang seragam.

Dokumen jadwal belum menyebut nama aplikasi, host, API, environment, akun, atau arsitektur teknis target. Tim harus mengambil detail tersebut dari Bank Jatim saat kick-off dan memasukkannya ke scope matrix serta Rules of Engagement (RoE).

## 2. Objective PoC

Dokumen Bank Jatim menyebut tiga tujuan utama:

1. Menilai kemampuan pelaksanaan VAPT.
2. Menilai kualitas analisis dan rekomendasi.
3. Menilai mutu laporan dan komunikasi teknis.

Semua peserta menggunakan aplikasi uji yang sama, mengikuti scope dan durasi yang ditetapkan Bank, menerapkan **OWASP Top 10:2025**, serta menjalankan pengujian **non-destruktif**.

## 3. Metodologi yang diminta

Urutan kerja pada dokumen Bank Jatim terdiri dari lima tahap:

1. **Information Gathering:** memetakan scope, teknologi, endpoint, alur aplikasi, dan attack surface yang diizinkan.
2. **Vulnerability Assessment:** mengidentifikasi potensi kerentanan melalui pemeriksaan manual dan/atau tools yang sesuai.
3. **Penetration Testing:** memvalidasi kerentanan secara terkendali untuk menilai kemungkinan eksploitasi dan dampaknya.
4. **Reporting:** mendokumentasikan evidence, tingkat risiko, dampak, dan rekomendasi perbaikan.
5. **Final Presentation:** menyampaikan ringkasan hasil, menjelaskan temuan utama, dan menjawab pertanyaan evaluator.

Semua langkah harus mengikuti scope dan RoE yang disetujui. Tim perlu menjelaskan alasan pemilihan teknik, hasil validasi, keterbatasan, dan hubungan antara evidence dengan kesimpulan.

## 4. Deliverable yang wajib disiapkan

| Deliverable | Isi minimum |
|---|---|
| Executive Summary | Scope, hasil utama, risiko prioritas, dan kesimpulan |
| Technical Report | Metodologi, cakupan, langkah validasi, detail temuan, bukti, dampak, dan batasan pengujian |
| Vulnerability Matrix | Aset/endpoint, kategori, status validasi, severity/prioritas, dan referensi temuan |
| Risk Rating | Tingkat risiko setiap temuan beserta dasar penilaian dan asumsi |
| Remediation Recommendation | Rekomendasi yang spesifik, dapat ditindaklanjuti, dan diprioritaskan berdasarkan risiko |

Detail satu temuan harus mendukung pembacaan dan reproduksi: kondisi awal, prasyarat, langkah uji, expected versus observed behavior, evidence, dampak, akar masalah, remediasi, dan kriteria retest.

## 5. Kriteria evaluasi yang tercantum di dokumen

Dokumen Bank Jatim menyebut enam aspek. Dokumen yang tersedia tidak menetapkan bobot angka atau poin resmi per jenis bug.

| Aspek evaluasi | Indikator yang dinilai | Bukti yang perlu disiapkan tim |
|---|---|---|
| Metodologi | Kesesuaian pendekatan, urutan kerja, cakupan pengujian, dan kepatuhan RoE | Rencana uji, scope matrix, test catalog, catatan pelaksanaan, alasan pemilihan skenario |
| Pengalaman | Relevansi proyek VAPT sejenis dan dokumen pendukung | Daftar proyek relevan dan bukti pengalaman yang diizinkan untuk disampaikan |
| Kompetensi tim | Pengalaman, peran, pemahaman teknis, dan kemampuan personel | Pembagian peran, pengalaman dan keahlian relevan, penguasaan metode dan temuan |
| Tools yang digunakan | Relevansi tools, kombinasi metode manual/otomatis, dan interpretasi kritis | Daftar tools, tujuan penggunaan, hasil yang telah divalidasi manual, serta batasan tools |
| Kualitas laporan | Kelengkapan bukti, reproduktibilitas, risk rating, dampak, dan rekomendasi | Executive summary, technical report, vulnerability matrix, risk rating, remediation plan, evidence index |
| Presentasi hasil | Kejelasan penyampaian, penguasaan materi, dan jawaban teknis | Alur presentasi, demonstrasi sesuai scope, penguasaan detail temuan, dan Q&A |

Gunakan enam aspek ini sebagai struktur latihan evaluator. Bobot pada internal scorecard, bila dipakai, hanya untuk rehearsal tim dan tidak boleh disebut sebagai bobot resmi Bank Jatim.

## 6. Rules of Engagement dan pengamanan

Dokumen Bank Jatim menyatakan aktivitas berikut dapat dilakukan sesuai scope:

- Information gathering dan scanning terkendali.
- Validasi manual atas temuan.
- Proof of Concept yang tidak merusak.
- Penggunaan akun uji dari Bank.

Dokumen tersebut melarang aktivitas berikut kecuali Bank menyetujuinya secara tertulis:

- DoS/DDoS, stress testing, atau tindakan yang mengganggu layanan.
- Pengujian di luar domain/IP yang ditetapkan.
- Penghapusan atau perubahan data maupun transaksi nyata.
- Malware, persistence, dan eksfiltrasi data.

Tim wajib mematuhi komitmen kerahasiaan, menggunakan environment uji dan data dummy, serta menghentikan dan mengeskalasi aktivitas bila melihat indikasi dampak operasional. Peserta tidak boleh melihat atau memanfaatkan hasil kerja peserta lain. Jika pengujian berlangsung serentak, gunakan akun terpisah dan koordinasikan aktivitas untuk mencegah interferensi.

## 7. Jadwal dan hal yang perlu dikonfirmasi

Dokumen jadwal menyebut periode **26–30 Oktober 2026**, jam pengujian 08.00–17.00 WIB, serta presentasi **90 menit per peserta**. Namun, beberapa bagian dokumen memberikan pembagian hari yang berbeda:

| Bagian dokumen | Jadwal yang dicantumkan |
|---|---|
| Ikhtisar, halaman 2 | Persiapan dan kick-off 26 Oktober; pengujian 27–29 Oktober; presentasi hari ke-4 |
| Gantt chart, halaman 5 | Jam pengujian 26–28 Oktober; presentasi 29–30 Oktober |
| Agenda harian, halaman 6 | Persiapan & VA/PT 26 Oktober; VA/PT dan reporting 27–28 Oktober; presentasi 29 Oktober 08.00–17.00 dan 30 Oktober 08.00–11.30 |

Sebelum mengunci workplan, minta konfirmasi tertulis dari Bank mengenai tanggal pengujian, tanggal presentasi, slot per peserta, serta tenggat penyerahan laporan. Jangan menetapkan jadwal internal berdasarkan salah satu pembagian tersebut sebelum dikonfirmasi.

## 8. Implikasi untuk dokumentasi tim

- Gunakan OWASP Top 10:2025 sebagai klasifikasi risiko aplikasi, sesuai instruksi Bank.
- Anggap hanya aplikasi, endpoint, akun, dan environment yang ditetapkan Bank sebagai target PoC.
- Gunakan katalog pengujian sebagai kandidat test; pilih test setelah scope dan teknologi diketahui.
- Gunakan threat model remittance pada repository hanya jika aplikasi yang diberikan Bank memang terkait remittance. Jangan menyajikannya sebagai arsitektur aktual sebelum Bank mengonfirmasi.
- Bedakan hasil aktual, expected behavior, dan potensi dampak.
- Nilai internal 0–100 pada dokumen scoring hanya membantu triage dan pemilihan skenario. Dokumen Bank yang tersedia tidak menetapkan poin untuk setiap jenis bug, bobot enam kriteria, atau formula skor resmi.
- Pastikan laporan akhir berisi lima deliverable yang disebut di Bagian 4 dan mendukung enam aspek evaluasi di Bagian 5.

## 9. Dokumen acuan dan hubungan

- [Remittance Threat Model](03-remittance-threat-model.md), jika relevan dengan aplikasi yang ditetapkan Bank.
- [Metodologi dan Test Case](04-metodologi-test-case.md).
- [Evidence, Reporting, dan Retest](05-evidence-dan-reporting.md).
- [PoC Runbook](06-poc-runbook.md).
- [Tender Differentiation dan Internal Scorecard](07-differentiation-scorecard.md).
- [Remittance Test Catalog](10-remittance-test-catalog.md), sebagai katalog kandidat yang diseleksi setelah scope diketahui.
- [OWASP Top 10:2025 Mapping](17-owasp-top10-2025-mapping.md).
- [Scoring Temuan untuk Persiapan Evaluasi Bank Jatim](18-scoring-temuan-bank-jatim.md).
