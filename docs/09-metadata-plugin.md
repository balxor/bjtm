# Metadata Plugin Asal

Dokumen ini mencatat cakupan plugin asal yang dokumentasinya dikonversi ke Markdown untuk repository GitHub. Ia bukan file manifest executable dan tidak menggantikan package plugin.

## Identitas

| Field | Nilai |
|---|---|
| Nama paket | bank-jatim-pentest-poc |
| Versi paket asal | 0.1.0 |
| Display name | Bank Jatim Pentest PoC |
| Kategori | Security |
| Tujuan | Persiapan PoC penetration testing yang berizin untuk tender, khususnya kebutuhan remittance bila dikonfirmasi scope |

## Kemampuan dokumentasi

- Persiapan scope dan Rules of Engagement.
- Pemetaan attack surface serta alur bisnis remittance secara konseptual.
- Threat-driven test design.
- Metodologi pengujian dan pencatatan hasil.
- Evidence handling dan reporting.
- Rehearsal/runbook.
- Internal readiness scorecard dan tender differentiation.
- Template test case dan finding.
- Kesiapan klien, tim, dan environment.

## Batasan

- Plugin tidak menyatakan bahwa sistem Bank Jatim tertentu memiliki kerentanan.
- Dokumen undangan satu halaman bukan spesifikasi teknis atau otorisasi pengujian.
- Scope, architecture, akun uji, endpoint, transaction limit, test window, rubric, dan deliverable harus ditetapkan berdasarkan dokumen tender/izin yang sah.
- Repository ini berstatus public; jangan menaruh credential, token, URL rapat privat, raw evidence, internal endpoint yang tidak boleh dipublikasikan, atau data nasabah.

## Pemetaan dokumentasi

| Materi paket asal | Lokasi Markdown GitHub |
|---|---|
| Instruksi utama workflow / SKILL | README dan dokumen di folder docs |
| POC-RUNBOOK.md | docs/06-poc-runbook.md |
| FINDING-TEMPLATE.md | templates/finding-template.md |
| Manifest plugin JSON | Ringkasan metadata ini |

Untuk deployment atau instalasi plugin, gunakan package manifest asal yang tervalidasi pada workflow plugin yang sesuai. Repository dokumentasi ini berfokus pada Markdown untuk dibaca, direview, dan dipelihara melalui GitHub.
