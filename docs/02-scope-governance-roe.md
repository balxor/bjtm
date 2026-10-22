# Scope, Governance, dan Rules of Engagement

## Tujuan

Menetapkan batas legal, operasional, dan teknis sebelum pengujian dimulai. Otorisasi harus tertulis dan diberikan oleh pihak yang berwenang atas aset yang diuji.

Dokumen jadwal PoC Bank Jatim menetapkan satu aplikasi uji yang sama untuk seluruh peserta, dengan scope dan aturan yang seragam. Nama aplikasi dan rincian teknis belum tercantum dalam jadwal yang tersedia. Objective, deliverable, dan enam aspek evaluasi dirangkum pada [Objective dan Kriteria Evaluasi PoC Bank Jatim](19-objective-dan-kriteria-poc-bank-jatim.md). Catat detail dari Bank saat kick-off sebelum menyusun endpoint inventory atau menetapkan arsitektur.

## Scope matrix

Lengkapi matriks dan simpan referensi persetujuannya pada dokumentasi engagement.

| Field | Nilai | Status |
|---|---|---|
| Engagement / PoC ID | TBD | Draft |
| Sponsor dan scope owner | TBD | TBD |
| Environment | TBD | TBD |
| Aplikasi, API, host, atau jaringan yang diizinkan | TBD | TBD |
| Akun dan role uji | TBD | TBD |
| Metode pengujian yang diizinkan | TBD | TBD |
| Exclusions | TBD | TBD |
| Jendela pengujian dan zona waktu | TBD | TBD |
| Rate, volume, dan transaction limit | TBD | TBD |
| Data uji dan beneficiary sintetis | TBD | TBD |
| Kontak eskalasi dan emergency contact | TBD | TBD |
| Lokasi, retensi, dan penghapusan evidence | TBD | TBD |

## Isi minimum RoE

RoE harus menetapkan:
- Pihak pemberi otorisasi dan pihak yang berhak menyetujui perubahan scope.
- Aset, akun, role, environment, aplikasi, API, dan rentang jaringan yang termasuk atau dikecualikan.
- Teknik yang diizinkan, teknik terlarang, serta batas proof-of-exploitation.
- Waktu mulai dan berakhir, request rate, concurrency, volume, serta transaction ceiling.
- Penggunaan environment produksi atau nonproduksi dan kontrol tambahan untuk masing-masing.
- Penggunaan data sintetis serta aturan akses data sensitif.
- Kontak eskalasi, tingkat keparahan insiden, dan prosedur stop-test.
- Pengumpulan, penyuntingan, akses, retensi, dan penghapusan evidence.
- Prosedur perubahan scope, cleanup, rollback, dan closeout.

## Stop conditions

Hentikan test terkait jika:
- Transaksi nyata berpotensi diproses, diselesaikan, atau memindahkan dana.
- Data atau objek di luar populasi uji terlihat atau berpotensi berubah.
- Sistem tidak stabil, latency/error meningkat secara tidak wajar, atau operasi terdampak.
- Batas rate, volume, waktu, atau transaksi tercapai.
- Credential, token, atau secret terpapar di lokasi yang tidak diizinkan.
- Pemilik sistem atau emergency contact memerintahkan penghentian.

Catat waktu, test ID, kondisi, tindakan mitigasi, pihak yang diberi tahu, dan keputusan untuk melanjutkan. Pengujian hanya boleh dilanjutkan setelah pihak berwenang menyetujui tindakan mitigasi dan kelanjutan test.

## RACI

| Aktivitas | Engagement lead | Pentester | Scope owner | SOC/ops | Evidence/report lead |
|---|---|---|---|---|---|
| Persetujuan scope/RoE | R | C | A | C | I |
| Persiapan test case | A | R | C | C | C |
| Go/no-go eksekusi | R | C | A | C | I |
| Eksekusi pengujian | A | R | C | C | C |
| Stop-test operasional | R | R | A | R | I |
| Review temuan | A | R | C | C | R |
| Retest dan closeout | R | R | A | C | R |

R = Responsible, A = Accountable, C = Consulted, I = Informed. Sesuaikan penetapan peran dengan tata kelola engagement.

## Go/no-go gate

Jangan memulai pengujian aktif sampai:
- Otorisasi dan scope tertulis tersedia.
- RoE dan stop conditions mendapat persetujuan.
- Akun/role, dataset sintetis, target, dan environment telah diuji untuk kesiapan.
- Kontak darurat tersedia.
- Evidence handling dan cleanup procedure siap.
- Skenario telah melalui review risiko.
