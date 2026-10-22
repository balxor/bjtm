# Kesiapan Klien dan Tim

## Input yang diperlukan dari klien

| Input | Mengapa diperlukan | Status |
|---|---|---|
| TOR/RFP/SoW terkini | Sumber scope, deliverable, dan kriteria penerimaan | TBD |
| PoC evaluation rubric | Agar prioritas sesuai penilaian resmi | TBD |
| Arsitektur dan data-flow | Memahami komponen dan trust boundary aktual | TBD |
| Inventory aplikasi/API/aset | Menghindari target di luar scope | TBD |
| Environment dan test window | Menentukan batas eksekusi | TBD |
| Akun serta role uji | Menguji authorization dan separation of duties | TBD |
| Data dan beneficiary sintetis | Mencegah penggunaan data nasabah nyata | TBD |
| Transaction/rate limits | Memastikan validasi punya batas eksplisit | TBD |
| Kontak incident/emergency | Eskalasi dan penghentian aktivitas | TBD |
| Aturan evidence/retensi | Menjaga kerahasiaan dan integritas bukti | TBD |
| Existing controls to validate | Menyelaraskan test case dengan kontrol yang diharapkan | TBD |
| Format deliverable | Memastikan laporan, presentasi, dan matriks sesuai tender | TBD |

## Peran tim yang disarankan

| Peran | Akuntabilitas |
|---|---|
| Engagement / PoC lead | Scope, komunikasi, go/no-go, dan eskalasi |
| Application/API pentester | AuthN/AuthZ, API, dan validasi business logic |
| Banking process specialist | Memastikan skenario mencerminkan proses yang dikonfirmasi |
| Infrastructure/cloud specialist | Hanya bila infrastruktur/cloud masuk scope |
| Evidence and reporting lead | Bukti, quality review, finding register, dan laporan |
| Client liaison | Koordinasi akun uji, jadwal, pertanyaan, serta persetujuan |

Satu orang dapat menjalankan beberapa peran jika kemampuan dan independensi review tetap memadai.

## Persiapan teknis

- Workspace pengujian terpisah dan dikendalikan aksesnya.
- Tools proxy/API/browser/scanner hanya yang diizinkan RoE.
- Versi tool dan konfigurasi dicatat.
- Evidence workflow diuji sebelum PoC.
- Sinkronisasi waktu terverifikasi.
- Akun dan hak akses diverifikasi tanpa mengekspos secret di log.
- Data sintetis dan cleanup procedure disiapkan.
- Jalur komunikasi dengan pemilik sistem dan tim operasi aktif.
- Prosedur rollback/stop-test telah dipahami semua anggota.
- Laporan template dan finding register tersedia sebelum eksekusi.

## Pertanyaan klarifikasi untuk panitia/pemilik sistem

1. Apakah panitia dapat memberikan TOR/RFP/SoW terbaru dan rubric evaluasi PoC?
2. Komponen remittance mana saja yang termasuk scope, dan environment apa yang digunakan?
3. Apakah seluruh test berjalan pada sandbox/UAT dengan data serta beneficiary sintetis?
4. Skenario atau teknik apa yang secara eksplisit dilarang?
5. Apa batas request, transaksi, dan waktu pengujian serta kriteria stop-test?
6. Format deliverable apa yang dinilai: demo langsung, laporan teknis, presentasi, atau kombinasi?
7. Bagaimana proses persetujuan perubahan scope dan pengamanan evidence?

## Definition of Ready

PoC siap direhearsal apabila scope dan rubric diketahui, test cases sudah direview, environment serta akun uji tersedia, demonstrasi tidak dapat memindahkan dana nyata, evidence pipeline berfungsi, dan semua peserta memahami stop conditions.
