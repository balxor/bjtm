# Tender Differentiation dan Internal Scorecard

## Prinsip diferensiasi

Diferensiasi sebaiknya ditunjukkan lewat kualitas penalaran, keselamatan eksekusi, reproducibility, dan remediasi—bukan jumlah tool atau klaim bahwa semua kerentanan dapat ditemukan.

Poin yang perlu dibuktikan saat PoC:
- Threat-led testing yang berangkat dari risiko bisnis remittance.
- Pemahaman authentication, authorization, workflow, dan transaction integrity.
- Pengujian API serta business logic dengan validasi server-side.
- Guardrail yang mencegah dana atau data nyata terdampak.
- Evidence yang terstruktur, tersanitasi, dan dapat direproduksi.
- Triage yang membedakan observasi dari asumsi.
- Rekomendasi remediasi teknis yang memiliki acceptance/retest criteria.
- Disiplin retest, knowledge transfer, serta komunikasi eksekutif.
- Metrik layanan yang realistis dan dapat diperiksa.

Hindari klaim absolut seperti “100% secure”, “zero risk”, atau jaminan menemukan seluruh kerentanan.

## Internal readiness scorecard

Gunakan model berikut hanya untuk latihan internal apabila panitia belum memberikan rubric. Jika rubric tender resmi tersedia, gunakan rubric resmi itu; jangan mencampurkan bobot tanpa penjelasan.

| Area | Bobot internal | Pertanyaan reviewer |
|---|---:|---|
| Scope & RoE readiness | 15% | Apakah tim tahu apa yang boleh/tidak boleh diuji? |
| Business-risk understanding | 15% | Apakah risiko transaksi dan proses dijelaskan dengan benar? |
| Attack hypothesis quality | 15% | Apakah hipotesis dapat diuji dan memiliki expected behavior jelas? |
| Technical depth | 20% | Apakah validasi mencapai kontrol server-side dan alur bisnis? |
| Evidence quality/reproducibility | 15% | Dapatkah orang lain memahami dan memverifikasi hasilnya? |
| Risk/remediation quality | 10% | Apakah mitigasi layak dan retest terukur? |
| Presentation/evaluator communication | 10% | Apakah demo jelas, aman, dan tepat sasaran? |
| **Total** | **100%** | |

## Skala penilaian internal

Beri nilai setiap area dengan skala 1–5:
- **1 — Tidak siap:** artefak/penjelasan tidak ada atau tidak konsisten.
- **2 — Lemah:** ada pendekatan, tetapi banyak gap kritis.
- **3 — Memadai:** requirement utama ditangani dengan beberapa gap.
- **4 — Kuat:** bukti dan metode solid, gap minor.
- **5 — Unggul:** terukur, repeatable, berorientasi bisnis, dan siap dievaluasi.

Skor berbobot = nilai area / 5 × bobot area. Cantumkan evidence pendukung nilai, bukan penilaian subjektif saja.

## Demo storyboard

### 1. Business problem
Paparkan proses dan aset yang benar-benar dikonfirmasi oleh klien.

### 2. Security control hypothesis
Nyatakan kontrol yang sedang diuji dan alasan relevansinya.

### 3. Controlled proof
Laksanakan langkah minimum di environment dan akun yang disetujui.

### 4. Evidence
Tunjukkan perilaku yang diharapkan dibanding perilaku aktual dengan data tersanitasi.

### 5. Business impact
Jelaskan dampak berdasarkan observasi dan skenario yang dapat dipertanggungjawabkan.

### 6. Remediation
Tunjukkan kontrol yang harus diperbaiki, owner yang relevan, dan prioritas.

### 7. Retest measurement
Definisikan kondisi lulus yang membuktikan kontrol bekerja setelah perbaikan.

## Persiapan tanya jawab evaluator

Tim harus mampu menjawab:
- Mengapa skenario diprioritaskan?
- Apa yang menjadi bukti dan apa yang masih hipotesis?
- Bagaimana tim menjamin test tidak memindahkan dana nyata?
- Bagaimana tim mengetahui tindakan dihentikan/ditolak oleh kontrol yang benar?
- Apa batas coverage dari demo ini?
- Bagaimana rekomendasi diuji ulang dan status penutupan dibuktikan?
- Bagaimana data dan evidence dilindungi selama engagement?
