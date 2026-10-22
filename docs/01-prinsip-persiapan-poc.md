# Prinsip Persiapan PoC

## Tujuan

Framework ini menetapkan proses persiapan, pelaksanaan, evaluasi, dan penutupan penetration testing Proof of Concept (PoC). Setiap tahap menghasilkan artefak yang dapat ditinjau dan digunakan untuk keputusan teknis.

## Prinsip kerja

1. **Otorisasi tertulis.** Pengujian aktif hanya dilakukan setelah pemilik kewenangan menyetujui scope dan Rules of Engagement (RoE).
2. **Scope yang jelas.** Catat aset, akun, environment, metode, waktu, batas beban, serta pengecualian.
3. **Berbasis risiko.** Prioritaskan skenario menurut dampak bisnis, peluang penyalahgunaan, dan tujuan pengujian.
4. **Dampak minimum.** Gunakan akun uji dan data sintetis. Hindari pemrosesan transaksi atau perubahan data nyata.
5. **Hasil yang dapat diulang.** Setiap test case mempunyai precondition, langkah, expected behavior, hasil aktual, evidence, dan cleanup.
6. **Evidence mendukung kesimpulan.** Pisahkan observasi, interpretasi, penyebab yang didukung bukti, serta perkiraan dampak.
7. **Remediasi terukur.** Setiap finding mempunyai rekomendasi, prioritas, owner, dan acceptance criteria untuk retest.
8. **Perubahan tercatat.** Perubahan scope, metode, jadwal, dan limit memerlukan persetujuan serta catatan perubahan.

## Status requirement dan test

Gunakan status berikut secara konsisten.

| Status | Arti | Tindakan |
|---|---|---|
| Draft | Belum melalui review | Lengkapi dan ajukan review |
| Approved | Sudah mendapat persetujuan yang diperlukan | Dapat digunakan sesuai batas persetujuan |
| TBD | Informasi belum tersedia | Tetapkan owner dan target penyelesaian |
| Excluded | Dikecualikan dari scope | Jangan lakukan pengujian |
| Blocked | Ada prasyarat atau izin yang belum terpenuhi | Hentikan sampai hambatan diselesaikan |
| PASS | Hasil memenuhi expected behavior | Catat kondisi dan batas uji |
| FAIL | Hasil tidak memenuhi expected behavior | Simpan evidence dan lakukan triage |
| INCONCLUSIVE | Evidence tidak cukup untuk keputusan | Catat batasan dan tentukan tindak lanjut |
| NOT TESTED | Test tidak dijalankan | Catat alasan |

## Alur persiapan

1. Kumpulkan requirement proyek dan kriteria penerimaan.
2. Tetapkan scope, exclusions, RoE, RACI, serta jalur eskalasi.
3. Pahami alur bisnis, aset, trust boundaries, dan kontrol keamanan.
4. Susun threat model dan daftar test case.
5. Siapkan akun uji, data sintetis, environment, evidence store, dan cleanup procedure.
6. Lakukan review risiko serta readiness assessment.
7. Rehearsal demonstrasi dalam environment yang sesuai.
8. Jalankan pengujian sesuai RoE dan catat hasil.
9. Lakukan review temuan, pelaporan, retest, dan closeout.

## Quality gate

PoC siap dijalankan jika:
- Otorisasi, scope, dan RoE telah disetujui.
- Setiap test case merujuk pada aset dan metode yang termasuk scope.
- Akun uji, data sintetis, environment, serta batas beban tersedia.
- Stop conditions dan kontak eskalasi telah ditetapkan.
- Evidence workflow dan penyuntingan data sudah diuji.
- Cleanup dan rekonsiliasi objek uji memiliki owner.
- Tim memahami prosedur penghentian dan komunikasi insiden.
