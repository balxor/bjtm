# Kesiapan Klien dan Tim

## Input proyek

| Input | Kegunaan | Status |
|---|---|---|
| TOR/RFP/SoW | Scope, deliverable, dan kriteria penerimaan | TBD |
| Evaluation rubric | Bobot dan metode penilaian | TBD |
| Arsitektur dan data-flow | Komponen dan trust boundary | TBD |
| Inventory aplikasi/API/aset | Daftar target yang diizinkan | TBD |
| Environment dan test window | Jadwal dan batas pelaksanaan | TBD |
| Akun dan role uji | Validasi authorization serta separation of duties | TBD |
| Data dan beneficiary sintetis | Pelaksanaan test tanpa data transaksi nyata | TBD |
| Transaction/rate limits | Batas pengujian | TBD |
| Kontak incident/emergency | Eskalasi dan penghentian aktivitas | TBD |
| Evidence handling dan retensi | Penyimpanan, akses, serta penghapusan evidence | TBD |
| Daftar kontrol yang diuji | Pemetaan test case ke kontrol keamanan | TBD |
| Format deliverable | Laporan, demo, presentasi, dan matriks | TBD |

## Peran tim

| Peran | Tanggung jawab |
|---|---|
| Engagement/PoC lead | Scope, komunikasi, go/no-go, dan eskalasi |
| Application/API pentester | Authentication, authorization, API, dan business logic |
| Banking/process specialist | Akurasi alur bisnis yang menjadi scope |
| Infrastructure/cloud specialist | Pengujian infrastruktur jika termasuk scope |
| Evidence/reporting lead | Evidence, quality review, finding register, dan laporan |
| Client liaison | Jadwal, akun uji, klarifikasi, dan koordinasi persetujuan |

Satu anggota tim dapat memegang beberapa peran jika kapasitas dan review tetap memadai.

## Persiapan teknis

- Sediakan workspace pengujian dengan kontrol akses.
- Gunakan tools yang diizinkan dalam RoE.
- Catat versi tool dan konfigurasi yang relevan.
- Uji evidence workflow sebelum PoC.
- Uji sinkronisasi waktu.
- Periksa akun dan hak akses tanpa merekam secret di log.
- Siapkan data sintetis dan cleanup procedure.
- Pastikan komunikasi dengan pemilik sistem dan tim operasi tersedia.
- Uji prosedur rollback dan stop-test.
- Siapkan template laporan serta finding register.

## Pertanyaan klarifikasi proyek

1. Apa saja aset dan proses bisnis yang termasuk scope?
2. Environment mana yang digunakan untuk pengujian?
3. Apakah semua test menggunakan akun dan data sintetis?
4. Teknik apa yang dilarang atau membutuhkan persetujuan tambahan?
5. Berapa batas request, transaksi, waktu, dan concurrency?
6. Deliverable apa yang dinilai dan bagaimana formatnya?
7. Siapa yang berwenang menyetujui perubahan scope dan penghentian test?
8. Di mana evidence disimpan, siapa yang dapat mengaksesnya, dan berapa lama retensinya?

## Definition of Ready

PoC siap direhearsal saat scope dan rubric tersedia, test case telah direview, environment dan akun uji siap, jalur transaksi nyata dibatasi sesuai desain, evidence workflow berfungsi, dan seluruh peserta memahami stop conditions.
