# Prinsip Sumber dan Batasan

## Yang didukung dokumen sumber

Dokumen PDF yang tersedia adalah surat undangan rapat koordinasi persiapan pelaksanaan Proof of Concept (PoC) pengadaan jasa konsultan penetration testing tahun 2026. Dokumen menyebut rapat pada 21 Oktober 2026 pukul 10.00 WIB melalui pertemuan daring.

Dokumen tersebut **bukan TOR/RFP/SoW teknis**. Dokumen tidak menetapkan rincian teknis berikut:
- Sistem, aplikasi, API, host, jaringan, atau environment dalam scope.
- Arsitektur remittance dan data-flow aktual.
- Akun/role uji, dataset, serta beneficiary sintetis.
- Metode pengujian yang diizinkan atau dilarang.
- Jendela pengujian dan batas transaksi/rate.
- Kriteria evaluasi teknis, deliverable, atau bobot penilaian.

## Cara menangani informasi yang belum tersedia

Gunakan status berikut pada artefak proyek:

| Status | Makna | Perlakuan |
|---|---|---|
| Confirmed | Dinyatakan pada dokumen tender atau dikonfirmasi tertulis oleh pemilik sistem | Dapat menjadi dasar perencanaan |
| Proposed | Rekomendasi dari tim penguji | Minta persetujuan sebelum eksekusi |
| TBD | Belum diketahui atau belum disetujui | Tidak boleh diasumsikan |
| Out of scope | Dikecualikan secara eksplisit | Jangan diuji |
| Blocked | Bergantung pada persetujuan atau input yang belum diterima | Tidak dieksekusi |

## Aturan keterlacakan

Untuk setiap requirement:
1. Catat sumber, halaman/bagian, pemilik, dan status.
2. Pisahkan kutipan atau persyaratan sumber dari interpretasi tim.
3. Catat pertanyaan klarifikasi dan keputusan yang disetujui.
4. Jika ada perubahan scope, rekam persetujuan dan versi dokumen.
5. Jangan menyatakan bahwa sistem Bank Jatim memiliki kerentanan tanpa bukti hasil pengujian yang sah.

## Checklist sebelum rencana dianggap baseline

- [ ] TOR/RFP/SoW versi berlaku diterima.
- [ ] Kriteria penilaian PoC dikonfirmasi.
- [ ] Diagram arsitektur/data-flow aktual diterima atau gap dicatat.
- [ ] Daftar aset dan environment disetujui.
- [ ] RoE ditandatangani pemilik kewenangan.
- [ ] Batas penggunaan data dan bukti disepakati.

> Jangan menambahkan meeting URL, meeting ID/passcode, kredensial, atau detail internal ke repository publik.
