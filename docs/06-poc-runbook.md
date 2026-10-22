# PoC Runbook

## 0. Authorization gate

- [ ] Otorisasi tertulis tersedia.
- [ ] Scope dan exclusions ditetapkan.
- [ ] RoE, batas waktu, request, dan transaksi disetujui.
- [ ] Environment dicatat.
- [ ] Teknik terlarang dan stop conditions diketahui tim.
- [ ] Emergency contact dan jalur eskalasi tersedia.
- [ ] Evidence store dan aturan retensi ditetapkan.

## 1. Environment readiness

- [ ] Diagram arsitektur dan data-flow tersedia.
- [ ] Daftar URL/IP/API sesuai scope.
- [ ] Akun uji dan role telah diuji.
- [ ] Dataset dan beneficiary sintetis tersedia.
- [ ] Jalur settlement dinonaktifkan atau dibatasi sesuai desain sandbox.
- [ ] Sinkronisasi waktu diuji.
- [ ] Evidence naming dan redaction telah dicoba.
- [ ] Cleanup dan rollback procedure tersedia.

## 1A. Klasifikasi finding dan prioritas demo

Sebelum menetapkan urutan demonstrasi, petakan test ke [OWASP Top 10:2025](17-owasp-top10-2025-mapping.md), nilai dampak berdasarkan evidence, dan periksa kesiapan environment serta guardrail. Untuk latihan prioritas, rujuk [Scoring Temuan untuk Persiapan Evaluasi Bank Jatim](18-scoring-temuan-bank-jatim.md); skor tersebut adalah simulasi internal, bukan nilai resmi tender.

## 2. Urutan demonstrasi

Untuk setiap skenario:

1. Jelaskan proses bisnis dan aset yang diuji.
2. Nyatakan hipotesis serta kontrol yang diharapkan.
3. Tampilkan preconditions, akun uji, dan batas dampak.
4. Jalankan langkah yang tercantum pada test case.
5. Tampilkan evidence dan state sebelum/sesudah.
6. Hubungkan observasi dengan dampak bisnis yang dapat dijelaskan.
7. Pisahkan penyebab yang didukung bukti dari hipotesis.
8. Uraikan perbaikan yang direkomendasikan.
9. Jelaskan acceptance criteria untuk retest.
10. Periksa efek samping dan selesaikan cleanup.

## 3. Rencana waktu

Lengkapi durasi, owner, dan exit criteria sesuai alokasi PoC.

| Tahap | Durasi | Owner | Exit criteria |
|---|---|---|---|
| Kickoff dan safety briefing | TBD | Engagement lead + scope owner | Keputusan go/no-go dicatat |
| Validasi environment | TBD | Pentester + system owner | Akun, data, dan target siap |
| Demonstrasi skenario 1 | TBD | Test lead | Evidence dan hasil dicatat |
| Demonstrasi skenario 2 | TBD | Test lead | Evidence dan hasil dicatat |
| Skenario tambahan | TBD | Test lead | Selesai sesuai jadwal dan RoE |
| Ringkasan hasil | TBD | Reporting lead | Temuan awal ditinjau |
| Cleanup dan closeout | TBD | Tim + system owner | Rekonsiliasi dan sign-off |

## 4. Evidence naming convention

Format:

    <ENGAGEMENT-ID>_<TEST-ID>_<TIMESTAMP>_<EVIDENCE-TYPE>

Contoh:
- ENG-POC-01_RT-AUTH-01_20261022T103000_request-redacted.txt
- ENG-POC-01_RT-AUTH-01_20261022T103000_response-redacted.txt
- ENG-POC-01_RT-AUTH-01_20261022T103000_state-before-after.png

Gunakan timestamp dan zona waktu yang konsisten. Hindari nama nasabah, nomor rekening, token, dan data finansial pada nama file.

## 5. Stop procedure

Hentikan aktivitas terkait jika:
- Transaksi nyata berpotensi diproses atau dana berpindah.
- Data atau objek di luar test population terlihat atau berubah.
- Sistem tidak stabil atau operasi terdampak.
- Batas waktu, request, volume, atau transaksi tercapai.
- Secret atau data sensitif terpapar.
- Pemilik sistem memerintahkan penghentian.

Setelah stop:
- Hentikan variasi test terkait.
- Catat waktu, test ID, dan kondisi.
- Hubungi emergency contact.
- Amankan evidence sesuai prosedur.
- Lanjutkan hanya setelah mitigasi dan izin untuk melanjutkan disetujui.

## 6. Closeout

- [ ] Semua test case memiliki status dan hasil.
- [ ] Transaksi/objek uji direkonsiliasi dengan pemilik sistem.
- [ ] Cleanup/rollback selesai.
- [ ] Evidence ditinjau dan disunting.
- [ ] Finding melalui quality review.
- [ ] Gap dan test yang tidak dijalankan dicatat.
- [ ] Debrief selesai.
- [ ] Retensi dan penghapusan artefak mengikuti kebijakan engagement.
