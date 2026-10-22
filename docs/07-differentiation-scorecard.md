# Tender Differentiation dan Internal Scorecard

## Kriteria diferensiasi teknis

Tunjukkan kualitas metode dan pelaksanaan melalui:
- Pengujian yang mengikuti risiko dan proses bisnis.
- Pengujian authentication, authorization, workflow, serta transaction integrity.
- Validasi API dan business logic pada sisi server.
- Batas eksekusi yang melindungi transaksi dan data.
- Evidence yang terstruktur dan dapat direproduksi.
- Triage yang memisahkan observasi dari analisis.
- Rekomendasi remediasi dengan acceptance criteria.
- Retest, knowledge transfer, dan komunikasi teknis yang jelas.
- Metrik pekerjaan yang dapat dihitung dan diaudit.

Hindari klaim absolut seperti “100% secure”, “zero risk”, atau jaminan bahwa semua kerentanan akan ditemukan.

## Internal readiness scorecard

Dokumen jadwal Bank Jatim menyebut enam aspek evaluasi tanpa bobot angka. Bobot di bawah adalah **bobot rehearsal internal untuk melatih tim pada keenam aspek tersebut**, bukan bobot resmi tender. Rubrik resmi menjadi acuan penilaian tender. Rincian indikator dan deliverable yang diminta Bank tersedia di [Objective dan Kriteria Evaluasi PoC Bank Jatim](19-objective-dan-kriteria-poc-bank-jatim.md).

| Area | Bobot rehearsal internal | Pertanyaan reviewer |
|---|---:|---|
| Metodologi dan kepatuhan RoE | 20% | Apakah pendekatan, urutan kerja, cakupan, dan batas pengujian sesuai? |
| Pengalaman proyek relevan | 15% | Apakah tim dapat menunjukkan pengalaman VAPT sejenis dengan bukti pendukung? |
| Kompetensi tim | 15% | Apakah peran, kemampuan teknis, dan penguasaan personel jelas? |
| Tools dan interpretasi hasil | 10% | Apakah tools relevan dan hasilnya divalidasi secara kritis? |
| Kualitas laporan dan rekomendasi | 25% | Apakah evidence lengkap, temuan dapat diulang, risk rating beralasan, dampak jelas, dan rekomendasi dapat diterapkan? |
| Presentasi dan komunikasi teknis | 15% | Apakah hasil disampaikan dengan jelas dan tim mampu menjawab pertanyaan teknis? |
| **Total** | **100%** | |

## Skala penilaian internal

- **1: Tidak siap.** Artefak atau penjelasan tidak tersedia.
- **2: Perlu perbaikan besar.** Ada pendekatan, tetapi terdapat gap kritis.
- **3: Memadai.** Requirement utama ditangani dengan beberapa gap.
- **4: Kuat.** Metode dan evidence jelas; gap bersifat minor.
- **5: Unggul.** Metode repeatable, terukur, dan mendukung keputusan teknis.

Skor berbobot = nilai area / 5 × bobot area. Sertakan evidence pendukung setiap nilai.

## Skor finding dan skor kesiapan tim adalah dua hal berbeda

Internal readiness scorecard di atas mengukur kesiapan tim dan kualitas pelaksanaan. Skor itu tidak sama dengan severity kerentanan, attack-path risk score, atau nilai tender yang ditetapkan evaluator.

Untuk memilih temuan yang akan dibawa ke demo, gunakan [Scoring Temuan untuk Persiapan Evaluasi Bank Jatim](18-scoring-temuan-bank-jatim.md). Model 0–100 pada dokumen itu merupakan simulasi internal sampai tim memperoleh rubric resmi. OWASP Top 10:2025 menjadi klasifikasi risiko, bukan tabel poin bug.

## Demo storyboard

### 1. Business objective
Jelaskan proses bisnis dan aset yang menjadi target test.

### 2. Security hypothesis
Jelaskan kontrol yang diuji dan alasan pemilihan skenario.

### 3. Controlled test
Laksanakan langkah yang sudah dicantumkan pada test case.

### 4. Evidence
Tampilkan hasil aktual, expected behavior, dan state sebelum/sesudah.

### 5. Impact analysis
Jelaskan dampak yang didukung hasil pengujian serta keterbatasan analisis.

### 6. Remediation
Jabarkan perubahan kontrol, prioritas, dan owner yang diperlukan.

### 7. Retest
Tentukan kondisi lulus dan evidence untuk membuktikan perbaikan.

## Persiapan tanya jawab evaluator

Tim harus dapat menjelaskan:
- Dasar prioritas setiap skenario.
- Evidence yang mendukung temuan dan batas analisis.
- Kontrol untuk mencegah transaksi atau perubahan data nyata.
- Cara membedakan kegagalan kontrol dari kesalahan konfigurasi test.
- Batas cakupan PoC dan test yang tidak dijalankan.
- Metode verifikasi remediasi dan closure.
- Perlindungan serta retensi data pengujian.
