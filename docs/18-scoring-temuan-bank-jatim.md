# Scoring Temuan untuk Persiapan Evaluasi Bank Jatim

## 1. Status rubrik dan tujuan

Dokumen jadwal PoC Bank Jatim menyebut enam aspek evaluasi: metodologi, pengalaman, kompetensi tim, tools, kualitas laporan, dan presentasi hasil. Dokumen tersebut tidak memuat bobot angka atau tabel poin resmi untuk tiap jenis bug. Karena itu, angka dalam dokumen ini adalah **model simulasi internal untuk triage dan memilih skenario demonstrasi**, bukan skor resmi Bank Jatim atau prediksi nilai tender.

Sebelum PoC, minta atau cocokkan dengan TOR/SoW, acceptance criteria, dan lembar penilaian evaluator. Jika dokumen resmi memuat formula atau bobot, formula resmi menggantikan simulasi ini. Gunakan enam aspek dan deliverable dalam [Objective dan Kriteria Evaluasi PoC Bank Jatim](19-objective-dan-kriteria-poc-bank-jatim.md) sebagai dasar persiapan, tanpa menambahkan bobot yang tidak diberikan Bank.

**Tidak ada nilai baku OWASP “A01 sekian poin” atau “A08 sekian poin”.** OWASP Top 10:2025 dipakai untuk klasifikasi risiko. Skor suatu finding bergantung pada dampak, kemungkinan eksploitasi, cakupan, kualitas bukti, dan kontrol yang gagal.

## 2. Pisahkan empat jenis penilaian

| Penilaian | Yang diukur | Cara pakai |
|---|---|---|
| OWASP Top 10:2025 | Kategori risiko aplikasi | Klasifikasi penyebab/area risiko; tidak memberi poin per bug |
| Technical severity / CVSS | Keparahan teknis berdasarkan metrik yang sesuai | Nilai teknis pendukung, bukan satu-satunya ukuran risiko bisnis |
| Internal finding-priority score | Nilai praktis sebuah temuan untuk triage dan pemilihan demo | Gunakan rumus simulasi pada dokumen ini |
| Nilai evaluasi tender | Nilai yang diberikan evaluator kepada metode, bukti, dan demonstrasi tim | Hanya dapat dipastikan dari rubric resmi Bank Jatim |

Bank menyatakan penilaian tidak berfokus pada jumlah temuan saja. Temuan berisiko tinggi harus didukung evidence valid, metodologi yang sesuai, reproduktibilitas, risk rating beralasan, dan rekomendasi yang dapat ditindaklanjuti. Model 0–100 di dokumen ini hanya membantu tim menentukan urutan triage dan demo; model ini tidak memprediksi poin evaluasi tender.

## 3. Rumus simulasi internal: 0–100

Beri nilai setiap dimensi dari 0 sampai 5, lalu hitung. Nilai 0 berarti hasil uji tidak menunjukkan kontribusi risiko pada dimensi tersebut. Jika evidence tidak cukup untuk menilai suatu dimensi, jangan memberi nilai 0; gunakan INCONCLUSIVE sampai tersedia dasar penilaian. Nilai 2 dan 4 adalah tingkat di antara nilai acuan.

`Skor = Σ (Bobot dimensi × Nilai dimensi / 5)`

| Dimensi | Bobot | Nilai 1 | Nilai 3 | Nilai 5 |
|---|---:|---|---|---|
| Dampak bisnis dan criticality proses | 30 | Gangguan terbatas pada fungsi nonkritis | Mengganggu proses penting dan membutuhkan investigasi/koreksi | Mengancam proses remittance inti atau kontrol pembayaran |
| Integritas transaksi / fraud exposure | 25 | Tidak menyentuh instruksi atau state bisnis | Memengaruhi atribut/state atau kontrol yang relevan | Membuka jalur perubahan instruksi, bypass approval, pemrosesan ganda, atau efek finansial tanpa hak |
| Exploitability dan prasyarat | 15 | Kondisi sulit, hak tinggi atau beberapa prasyarat khusus | Memerlukan akun/kondisi tertentu | Dapat dilakukan dengan langkah sederhana oleh actor realistis dengan akses yang wajar |
| Blast radius / cakupan | 10 | Satu objek uji terbatas | Kelompok objek/role atau satu proses | Dapat melintasi customer/tenant atau memengaruhi banyak transaksi/flow |
| Kualitas evidence dan reproduktibilitas | 10 | Observasi belum kuat atau sulit diulang | Evidence memadai dengan beberapa batasan | Positive/negative control, state, request/response, timestamp, dan korelasi mendukung reproduksi |
| Kegagalan deteksi, audit, atau control monitoring | 10 | Ada event dan deteksi yang bekerja | Ada gap pada sebagian logging/deteksi | Aktivitas kritis sulit dideteksi atau tidak bisa ditelusuri sesuai kebutuhan |

### Interpretasi skor

| Skor | Kelompok internal | Keputusan kerja |
|---:|---|---|
| 85–100 | A: Prioritas demo tertinggi | Kandidat utama jika scope, safety, dan evidence siap |
| 70–84 | B: Prioritas tinggi | Kandidat demo atau pembuktian lanjutan |
| 50–69 | C: Prioritas menengah | Masuk laporan; demo jika relevan dengan waktu dan rubric |
| 30–49 | D: Prioritas rendah | Catat dan kelompokkan; jangan menggeser kasus berisiko tinggi tanpa alasan |
| 0–29 | E: Informasional / sangat rendah | Catat bila berguna, biasanya bukan skenario demo utama |

Gunakan faktor sesuai evidence aktual. Skor tidak boleh dinaikkan dengan mengasumsikan jumlah customer, nilai transaksi, atau kerugian yang tidak dibuktikan. Temuan yang hanya mengungkap banner/version atau error detail tanpa data sensitif, bypass kontrol, atau jalur serangan yang terbukti dibatasi maksimal **29/100** dalam simulasi ini.

## 4. Contoh: bug apa yang mendapat skor lebih tinggi?

Angka berikut adalah contoh latihan berdasarkan rumus internal di atas. Angka tersebut **bukan poin resmi yang diberikan Bank Jatim**.

| Contoh temuan hipotetis | OWASP Top 10:2025 terkait | Nilai dimensi (Bisnis / Integritas / Exploitability / Cakupan / Evidence / Deteksi) | Skor simulasi | Prioritas |
|---|---|---|---:|---|
| Mengubah beneficiary atau amount setelah approval tetap berlaku, dibuktikan pada sandbox non-settlement | A01, A06, A08 | 5 / 5 / 4 / 4 / 5 / 4 | 93 | A |
| Satu logical transaction menyebabkan dua dispatch/posting dalam mock connector setelah retry | A06, A08, A10 | 5 / 5 / 3 / 3 / 5 / 4 | 88 | A |
| Actor yang tidak berwenang dapat melewati fungsi approval pada workflow uji | A01, A06, A07 | 5 / 5 / 3 / 3 / 5 / 4 | 88 | A |
| Callback yang tidak lolos pemeriksaan integritas mengubah status transaksi menjadi sukses pada simulator | A07, A08, A10 | 5 / 4 / 3 / 3 / 5 / 4 | 83 | B |
| Akun A dapat membaca detail transaksi sensitif sintetis milik akun B | A01 | 4 / 3 / 4 / 4 / 5 / 3 | 75 | B |
| Event perubahan transaksi inti tidak memiliki audit trail atau alert yang disyaratkan | A09 | 3 / 3 / 3 / 3 / 4 / 5 | 66 | C |
| Informasi banner atau error detail tanpa data sensitif dan tanpa jalur eksploitasi lanjutan | A02 atau A10, sesuai root cause | 1 / 1 / 5 / 2 / 4 / 2 | 29 (dibatasi) | E |

Skor tabel dihitung dari bobot pada Bagian 3. Nilai contoh harus disesuaikan saat prasyarat, konfigurasi, evidence, atau dampak aktual berbeda.

## 5. Faktor yang membuat sebuah temuan menjadi kandidat skor tertinggi

Utamakan temuan yang membuktikan kegagalan kontrol penting dan berdampak pada proses bisnis:

1. **Transaction integrity:** beneficiary, amount, currency, fee/FX, status, approval, atau instruksi berubah di luar policy server-side.
2. **Authorization dan separation of duties:** akses lintas customer/tenant, fungsi approval yang dapat dilakukan oleh role yang salah, atau perubahan transaksi setelah approval tanpa approval ulang.
3. **Duplicate processing dan exception handling:** retry atau timeout menyebabkan efek bisnis ganda, status yang salah, atau transaksi yang tidak dapat direkonsiliasi.
4. **Trust dan data integrity:** callback/event tidak tepercaya dapat mengubah state, atau respons dari downstream dipakai tanpa validasi yang diwajibkan.
5. **Confidentiality berskala luas:** data transaksi/customer yang sensitif dapat dibaca lintas owner atau role.
6. **Evidence yang kuat:** hasil memiliki baseline, positive/negative control, request/response tersanitasi, state before/after, correlation ID, audit reference, serta retest criteria.
7. **Dampak dan akar masalah yang jelas:** temuan menjelaskan kontrol yang gagal, prasyarat eksploitasi, batas dampak, dan perbaikan yang dapat diterapkan.

Kategori OWASP yang sama dapat menghasilkan skor sangat berbeda. Contoh A01 yang hanya melewati pembatasan pada data non-sensitif tidak setara dengan A01 yang membuka data transaksi lintas customer atau memungkinkan perubahan instruksi. Penentuan nilai mengikuti bukti dan dampak, bukan kode OWASP semata.

## 6. Urutan kandidat untuk PoC remittance

Urutan awal berikut dipakai sebelum scope, arsitektur, dan rubric evaluator tersedia:

| Urutan | Kandidat | Alasan bisnis |
|---:|---|---|
| 1 | Approval binding / perubahan atribut kritis setelah approval | Menguji integritas instruksi dan pemisahan kewenangan |
| 2 | Idempotency / duplicate processing | Menguji apakah retry dapat menciptakan efek bisnis ganda |
| 3 | Object-level dan function-level authorization | Menguji batas akses customer, role, dan fungsi sensitif |
| 4 | Callback authenticity dan state transition | Menguji trust boundary serta kebenaran status transaksi |
| 5 | Limit, fee/FX, dan reconciliation | Menguji aturan transaksi serta konsistensi antarservice |

Perbarui urutan berdasarkan API yang benar-benar berada dalam scope, akses test account, kontrol yang tersedia, waktu demonstrasi, dan rubric resmi. Skenario dengan nilai bisnis tinggi tidak dijalankan bila test environment atau pengamanan non-settlement tidak memadai.

## 7. Aturan penilaian saat PoC berlangsung

- **Bukti kontrol gagal:** tetapkan FAIL, hentikan variasi yang tidak diperlukan, dokumentasikan dampak yang terbukti, dan lakukan eskalasi sesuai RoE.
- **Kontrol bekerja:** tetapkan PASS bila positive/negative control mendukung expected behavior.
- **Bukti tidak cukup:** gunakan INCONCLUSIVE; jangan mengubah dugaan menjadi finding.
- **Test tidak bisa dijalankan:** gunakan BLOCKED atau NOT TESTED dan catat dependency.
- **Dampak hanya potensial:** labeli sebagai potential impact, terpisah dari observed impact.
- **Tidak ada dana nyata:** seluruh validasi transaksi memakai data sintetis dan sandbox/mock yang tidak dapat menimbulkan settlement aktual.
- **Jangan mengejar skor:** tidak melakukan test di luar scope, memperluas akses data, meningkatkan volume tanpa batas, atau menimbulkan transaksi nyata demi membuat temuan terlihat lebih berat.

## 8. Input untuk mengonfirmasi skor resmi Bank Jatim

Minta dokumen atau penjelasan tertulis berikut sebelum angka simulasi dipakai sebagai prediksi tender:

- Rubrik evaluasi PoC/technical evaluation beserta bobot per kriteria.
- Apakah evaluator memberi nilai pada jumlah temuan, severity, relevansi business impact, teknik demonstrasi, evidence, atau gabungannya.
- Apakah ada skala poin khusus untuk Critical/High/Medium/Low atau jenis flaw tertentu.
- Apakah ada daftar skenario wajib, batas waktu demo, atau acceptance criteria yang menjadi syarat penilaian.
- Apakah hasil dari sandbox, mock connector, dan synthetic data diterima sebagai bukti PoC.

Setelah rubric resmi tersedia, petakan setiap test scenario dan evidence ke baris penilaian yang bersesuaian. Jangan menyampaikan model internal sebagai formula Bank Jatim sebelum ada dasar tertulis.

## 9. Referensi

- [OWASP Top 10:2025](https://top10.owasp.org/2025/)
- [OWASP API Security Top 10:2023](https://api-security.owasp.org/editions/2023/en/0x11-t10/)
- [Remittance Test Catalog](10-remittance-test-catalog.md)
- [Remittance Attack-Path Matrix](11-remittance-attack-path-matrix.md)
- [Tender Differentiation dan Internal Scorecard](07-differentiation-scorecard.md)
- [Objective dan Kriteria Evaluasi PoC Bank Jatim](19-objective-dan-kriteria-poc-bank-jatim.md)
