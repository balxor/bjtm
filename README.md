# Bank Jatim Penetration Testing PoC Playbook

Panduan persiapan Proof of Concept (PoC) penetration testing yang **berizin, terukur, aman, dan berbasis bukti** untuk proses tender. Dokumen ini menekankan pembuktian kontrol keamanan pada alur remittance tanpa mengasumsikan arsitektur atau ruang lingkup Bank Jatim yang belum dikonfirmasi.

> **Status sumber:** dokumen undangan yang tersedia hanya mengonfirmasi adanya rapat koordinasi persiapan PoC. Dokumen tersebut tidak memuat TOR/RFP/SoW teknis, daftar aset, arsitektur remittance, akun uji, jendela pengujian, maupun kriteria penerimaan. Semua hal tersebut harus dikonfirmasi kepada panitia/pemilik sistem sebelum eksekusi.

## Tujuan

- Menghubungkan risiko bisnis dan transaksi ke hipotesis pengujian yang dapat divalidasi.
- Menjalankan pengujian di bawah otorisasi tertulis dan Rules of Engagement (RoE).
- Menghasilkan evidence yang reproducible, dapat diaudit, dan mendukung remediasi.
- Menunjukkan kematangan delivery, bukan sekadar jumlah tools atau temuan.
- Menjaga integritas transaksi dan menghindari dampak pada dana atau data nasabah nyata.

## Navigasi dokumentasi

| Dokumen | Isi |
|---|---|
| [Prinsip sumber dan batasan](docs/01-prinsip-sumber-dan-batasan.md) | Fakta dari dokumen sumber, asumsi, dan data yang belum tersedia |
| [Scope, governance, dan RoE](docs/02-scope-governance-roe.md) | Otorisasi, cakupan, peran, eskalasi, dan stop conditions |
| [Remittance threat model](docs/03-remittance-threat-model.md) | Alur bisnis konseptual dan keluarga hipotesis pengujian |
| [Metodologi dan test-case design](docs/04-metodologi-test-case.md) | Siklus pengujian dan kriteria lulus/gagal |
| [Evidence dan reporting](docs/05-evidence-dan-reporting.md) | Evidence chain, temuan, dashboard, dan retest |
| [PoC runbook](docs/06-poc-runbook.md) | Checklist readiness, urutan demo, dan closeout |
| [Differentiation dan scorecard](docs/07-differentiation-scorecard.md) | Cara menunjukkan keunggulan teknis dan model penilaian internal |
| [Kesiapan klien dan tim](docs/08-kesiapan-klien-dan-tim.md) | Input wajib, RACI praktis, dan persiapan teknis |
| [Metadata plugin asal](docs/09-metadata-plugin.md) | Identitas dan cakupan dokumentasi yang dikonversi |
| [Template test case](templates/test-case-template.md) | Format test case yang dapat direplikasi |
| [Template finding](templates/finding-template.md) | Format temuan teknis dan risiko bisnis |
| [Template evidence log](templates/evidence-log-template.md) | Pencatatan evidence dan integritas artefak |

## Prinsip kerja

1. **Authorization first.** Tidak ada pengujian aktif sebelum pemilik sistem menyetujui scope, metode, waktu, akun, serta batas dampaknya secara tertulis.
2. **Source-grounded.** Istilah dan kriteria tender mengikuti TOR/RFP/SoW. Detail yang belum tersedia diberi status TBD.
3. **Risk-led.** Prioritaskan skenario yang membuktikan risiko terhadap confidentiality, integrity, availability, transaction integrity, fraud exposure, dan auditability.
4. **Minimum impact.** Gunakan data sintetis, akun uji, dan validasi sekecil mungkin. Jangan melakukan settlement atau memindahkan dana nyata.
5. **Evidence before claims.** Pisahkan fakta yang teramati dari hipotesis penyebab dan estimasi dampak.
6. **Remediation and retest.** Setiap temuan harus memiliki perbaikan yang dapat ditindaklanjuti serta kriteria retest yang terukur.

## Alur kerja yang disarankan

1. Terima dan ekstrak persyaratan tender.
2. Tutup gap scope dan otorisasi.
3. Petakan alur bisnis dan trust boundaries yang benar-benar dikonfirmasi.
4. Pilih 3–5 skenario bernilai tinggi.
5. Siapkan test account, data sintetis, rollback, dan evidence repository.
6. Rehearsal demo pada lingkungan yang disetujui.
7. Jalankan uji minimal-impact sesuai RoE.
8. Presentasikan bukti, dampak, akar masalah, remediasi, dan retest.
9. Rekonsiliasi transaksi uji dan lakukan secure closeout.

## Kriteria sukses PoC

- Scope dan RoE disepakati.
- Tiap skenario menguji kontrol yang jelas.
- Pengujian dapat dihentikan dan dipulihkan dengan aman.
- Bukti dapat ditelusuri ke test ID, waktu, actor, serta objek uji.
- Temuan menjelaskan dampak bisnis tanpa klaim yang tidak terbukti.
- Rekomendasi implementable dan mempunyai kriteria retest.
- Presentasi menunjukkan pemahaman proses bisnis serta trade-off keamanan.

## Keamanan repository

Repository ini berstatus public. Jangan commit credential, token, endpoint internal yang belum boleh dipublikasikan, data nasabah, payload sensitif, hasil scan, bukti PoC mentah, atau dokumen tender yang bersifat rahasia. Simpan artefak engagement di lokasi yang disetujui klien dengan kontrol akses dan retensi yang sesuai.

## Catatan

Dokumen ini adalah playbook persiapan. Ini bukan bukti bahwa sistem tertentu Bank Jatim memiliki kerentanan, dan bukan otorisasi untuk menguji sistem. Kegiatan aktual hanya dapat dilakukan sesuai persetujuan tertulis dan scope yang disahkan.
