# Scope, Governance, dan Rules of Engagement

## Tujuan

Menetapkan batas legal, operasional, dan teknis sebelum pengujian. Persetujuan tender atau undangan rapat saja tidak otomatis menjadi otorisasi untuk menguji sistem.

## Scope matrix

Isi matriks ini bersama pemilik sistem dan lampirkan referensi persetujuan. Gunakan placeholder sampai data dikonfirmasi.

| Field | Nilai | Status |
|---|---|---|
| Engagement / PoC ID | TBD | TBD |
| Sponsor dan scope owner | TBD | TBD |
| Environment (PoC/UAT/staging/production) | TBD | TBD |
| Aplikasi/API/host yang diizinkan | TBD | TBD |
| Akun dan role uji | TBD | TBD |
| Jenis pengujian yang diizinkan | TBD | TBD |
| Explicit exclusions | TBD | TBD |
| Jendela waktu dan zona waktu | TBD | TBD |
| Rate/volume/transaction limit | TBD | TBD |
| Test data dan beneficiary sintetis | TBD | TBD |
| Jalur eskalasi dan emergency contact | TBD | TBD |
| Retensi dan penghapusan evidence | TBD | TBD |

## Minimum isi RoE

RoE setidaknya harus mendefinisikan:
- Otorisasi tertulis dan pihak yang dapat menyetujui perubahan scope.
- Aset, akun, role, alamat, aplikasi, API, serta environment yang masuk/keluar scope.
- Teknik yang diizinkan, teknik yang dilarang, dan batas eksploitasi.
- Waktu mulai/berakhir, batas request, concurrency, serta transaction ceiling.
- Apakah pengujian menyentuh sistem production; default yang direkomendasikan adalah environment terisolasi.
- Penggunaan data sintetis dan larangan akses/pengambilan data nasabah nyata kecuali disetujui eksplisit.
- Kontak eskalasi, tingkat keparahan insiden, dan mekanisme stop-test.
- Pengamanan evidence, redaksi data, akses, retensi, dan pemusnahan.
- Prosedur perubahan scope, cleanup, rollback, serta sign-off akhir.

## Stop conditions

Hentikan test terkait segera jika:
- Ada indikasi transaksi nyata dapat diproses, settled, atau memindahkan dana.
- Objek atau data di luar test population terlihat atau berpotensi berubah.
- Sistem tidak stabil, latency/error meningkat tak wajar, atau kontrol fraud dapat terganggu.
- Batas rate, volume, atau waktu terlampaui.
- Credential/token/secrets terpapar di tempat yang tidak disetujui.
- Pemilik sistem atau emergency contact memerintahkan penghentian.

Catat waktu, test ID, kondisi, tindakan mitigasi, siapa yang diberi tahu, dan persetujuan untuk melanjutkan.

## RACI praktis

| Aktivitas | Engagement lead | Pentester | Bank scope owner | SOC/ops | Evidence/report lead |
|---|---|---|---|---|---|
| Persetujuan scope/RoE | R | C | A | C | I |
| Persiapan test case | A | R | C | C | C |
| Go/no-go eksekusi | R | C | A | C | I |
| Eksekusi pengujian | A | R | C | C | C |
| Stop-test operasional | R | R | A | R | I |
| Validasi bukti/temuan | A | R | C | C | R |
| Retest dan closeout | R | R | A | C | R |

R = Responsible, A = Accountable, C = Consulted, I = Informed. Peran final harus disesuaikan dengan tata kelola engagement.

## Go/no-go gate

Jangan memulai pengujian aktif sebelum seluruh hal kritis berikut terpenuhi:
- Otorisasi dan scope tertulis.
- RoE dan stop conditions disetujui.
- Akun/role, dataset sintetis, test endpoint, dan environment tervalidasi.
- Kontak darurat tersedia.
- Mekanisme pengamanan evidence dan cleanup siap.
- Skenario telah direview untuk risiko dampak operasional.
