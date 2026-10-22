# Contributing

Panduan kontribusi ini menjaga konsistensi dokumentasi framework dan kualitas materi teknis.

## Standar penulisan

- Gunakan Bahasa Indonesia teknis dengan istilah keamanan yang lazim.
- Tulis instruksi secara langsung dan spesifik.
- Pisahkan requirement, prosedur, expected behavior, hasil aktual, dan rekomendasi.
- Gunakan status yang konsisten: Proposed, Approved, TBD, Excluded, Blocked, PASS, FAIL, INCONCLUSIVE, dan NOT TESTED.
- Beri ID unik pada test case, finding, dan evidence.
- Sertakan precondition, batas dampak, cleanup, serta kriteria hasil untuk setiap prosedur pengujian.
- Gunakan data sintetis dalam contoh.
- Jangan menggeneralisasi hasil satu test menjadi klaim keamanan seluruh sistem.

## Pengelolaan konten

- Perbarui README saat menambah, menghapus, atau memindahkan dokumen.
- Gunakan relative links untuk dokumentasi di dalam repository.
- Periksa tabel, heading, checklist, dan code fences sebelum commit.
- Perbarui template terkait saat format test case, finding, atau evidence berubah.
- Pastikan contoh sesuai dengan scope pengujian yang dijelaskan pada dokumen terkait.

## Pengamanan artefak

Jangan commit credential, token, private key, raw evidence, data nasabah, data pribadi, transaksi nyata, atau laporan engagement yang dibatasi distribusinya. Simpan artefak tersebut pada evidence store dengan pembatasan akses dan retensi yang sesuai.

## Review checklist

- [ ] Struktur Markdown konsisten.
- [ ] Semua link relatif mengarah ke file yang tersedia.
- [ ] Istilah dan status digunakan secara konsisten.
- [ ] Prosedur memuat prerequisite, expected behavior, dan cleanup.
- [ ] Contoh memakai data sintetis.
- [ ] Tidak ada informasi rahasia atau artefak engagement dalam perubahan.
