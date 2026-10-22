# Penetration Testing PoC Framework

Framework ini digunakan untuk merencanakan dan melaksanakan Proof of Concept (PoC) penetration testing. Cakupan meliputi tata kelola pengujian, pemodelan risiko, desain test case, eksekusi terkontrol, pengelolaan evidence, pelaporan, serta retest.

## Tujuan

- Menghubungkan risiko bisnis dengan skenario pengujian yang terukur.
- Menetapkan scope, peran, otorisasi, dan batas operasional sebelum pengujian.
- Menyusun test case yang repeatable dengan expected behavior dan kriteria hasil.
- Mengumpulkan evidence yang relevan, aman, dan dapat ditelusuri.
- Menjelaskan dampak teknis dan bisnis serta rekomendasi remediasi.
- Mengukur hasil retest menggunakan acceptance criteria.

## Struktur dokumentasi

| Dokumen | Cakupan |
|---|---|
| [Prinsip persiapan PoC](docs/01-prinsip-persiapan-poc.md) | Prinsip kerja, status requirement, input, dan quality gate |
| [Scope, governance, dan RoE](docs/02-scope-governance-roe.md) | Otorisasi, scope, peran, eskalasi, dan stop conditions |
| [Remittance threat model](docs/03-remittance-threat-model.md) | Alur transaksi, trust boundaries, serta kelompok risiko remittance |
| [Metodologi dan test case](docs/04-metodologi-test-case.md) | Siklus kerja, test-case design, dan pencatatan hasil |
| [Evidence dan reporting](docs/05-evidence-dan-reporting.md) | Evidence chain, format laporan, dashboard, dan retest |
| [PoC runbook](docs/06-poc-runbook.md) | Readiness, urutan demonstrasi, stop procedure, dan closeout |
| [Tender differentiation dan scorecard](docs/07-differentiation-scorecard.md) | Kualitas teknis, demonstrasi, dan penilaian kesiapan internal |
| [Kesiapan klien dan tim](docs/08-kesiapan-klien-dan-tim.md) | Input proyek, peran tim, dan persiapan teknis |
| [Operating model](docs/09-operating-model.md) | Workstream, deliverable, dan quality gate |
| [Test-case template](templates/test-case-template.md) | Format desain dan pelaksanaan test |
| [Finding template](templates/finding-template.md) | Format temuan dan rekomendasi |
| [Evidence-log template](templates/evidence-log-template.md) | Indeks evidence dan kontrol penyimpanan |

## Prinsip pelaksanaan

1. **Authorization first.** Mulai pengujian setelah otorisasi tertulis, scope, dan Rules of Engagement (RoE) disetujui.
2. **Scope control.** Setiap test case harus mengacu pada aset, akun, environment, dan metode yang diizinkan.
3. **Risk-led testing.** Prioritaskan skenario berdasarkan dampak bisnis, kemungkinan penyalahgunaan, dan relevansi kontrol.
4. **Minimum impact.** Gunakan data sintetis dan environment yang sesuai. Hindari transaksi dana nyata.
5. **Evidence-based findings.** Catat perilaku aktual, expected behavior, keterbatasan pengujian, dan evidence pendukung.
6. **Remediation and retest.** Setiap finding harus memiliki rekomendasi, owner, prioritas, dan kriteria retest.

## Alur kerja

1. Kumpulkan requirement, scope, dan kriteria penerimaan.
2. Susun scope matrix, RoE, RACI, serta jalur eskalasi.
3. Petakan alur bisnis, aset, trust boundaries, dan kontrol keamanan.
4. Buat threat model dan prioritaskan test case.
5. Siapkan akun uji, data sintetis, environment, evidence store, serta prosedur cleanup.
6. Lakukan review risiko dan readiness gate.
7. Jalankan skenario sesuai urutan PoC.
8. Dokumentasikan hasil dan susun finding register.
9. Presentasikan dampak, rekomendasi, dan retest criteria.
10. Selesaikan cleanup, rekonsiliasi objek uji, dan closeout.

## Deliverable

- Scope matrix dan Rules of Engagement.
- Threat model dan test scenario matrix.
- Test case beserta status dan evidence reference.
- PoC runbook dan agenda demonstrasi.
- Finding register dan laporan teknis.
- Executive summary dan daftar prioritas remediasi.
- Retest plan serta closeout checklist.

## Pengelolaan artefak

Simpan raw evidence, data uji, hasil scanning, request/response, dan laporan engagement pada penyimpanan yang memiliki kontrol akses, retensi, serta mekanisme penghapusan yang sesuai. Dokumentasi yang dibagikan hanya memuat data yang diperlukan dan telah disunting. Jangan menyertakan credential, token, data pribadi, atau data transaksi nyata dalam dokumen publik.
