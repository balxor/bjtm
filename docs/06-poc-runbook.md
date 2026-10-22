# PoC Runbook

## 0. Authorization gate

- [ ] Otorisasi tertulis diterima dan valid.
- [ ] Scope dan exclusions disetujui.
- [ ] RoE, batas waktu, request, dan transaksi disepakati.
- [ ] Environment dipastikan dan dicatat.
- [ ] Prohibited actions dan stop conditions disepakati.
- [ ] Emergency contacts dan jalur eskalasi telah diuji.
- [ ] Akses evidence repository serta aturan retensi dikonfirmasi.

## 1. Environment readiness

- [ ] Architecture/data-flow tersedia atau gap dicatat.
- [ ] Daftar URL/IP/API berasal dari pemilik sistem dan berada dalam scope.
- [ ] Akun uji serta role diverifikasi.
- [ ] Dataset dan beneficiary sintetis tersedia.
- [ ] Jalur settlement dinonaktifkan atau telah dibatasi sesuai desain sandbox.
- [ ] Sinkronisasi waktu diperiksa.
- [ ] Evidence naming dan redaction diuji.
- [ ] Prosedur cleanup/rollback diketahui tim dan pemilik sistem.

## 2. Demo sequence

Untuk setiap skenario:

1. Jelaskan proses bisnis dan aset yang sedang diuji.
2. Nyatakan hipotesis serta kontrol yang diharapkan.
3. Tegaskan preconditions, akun uji, dan batas dampak.
4. Lakukan validasi minimum yang telah disetujui.
5. Tampilkan evidence tersanitasi dan hasil sebelum/sesudah.
6. Hubungkan observasi ke dampak bisnis yang masuk akal.
7. Bedakan root cause terverifikasi dari hipotesis.
8. Tunjukkan remediasi yang dapat dilaksanakan.
9. Jelaskan kriteria retest dan cara mengukur penutupan.
10. Konfirmasi tidak ada efek samping dan lengkapi catatan cleanup.

## 3. Rencana waktu (isi setelah durasi PoC dikonfirmasi)

| Tahap | Durasi | Owner | Exit criteria |
|---|---|---|---|
| Kickoff dan safety briefing | TBD | Engagement lead + scope owner | Go/no-go disetujui |
| Validasi environment | TBD | Pentester + system owner | Akun, data, dan target tervalidasi |
| Demonstrasi skenario 1 | TBD | Test lead | Evidence dan hasil dicatat |
| Demonstrasi skenario 2 | TBD | Test lead | Evidence dan hasil dicatat |
| Skenario tambahan (bila disetujui) | TBD | Test lead | Sesuai slot dan batas RoE |
| Ringkasan hasil | TBD | Reporting lead | Temuan awal ditinjau |
| Cleanup dan closeout | TBD | Tim + system owner | Rekonsiliasi dan sign-off |

## 4. Evidence naming convention

Format yang direkomendasikan:

    <ENGAGEMENT-ID>_<TEST-ID>_<TIMESTAMP>_<EVIDENCE-TYPE>

Contoh sintetis:
- BJ-POC-01_RT-AUTH-01_20261022T103000_request-redacted.txt
- BJ-POC-01_RT-AUTH-01_20261022T103000_response-redacted.txt
- BJ-POC-01_RT-AUTH-01_20261022T103000_state-before-after.png

Gunakan timestamp dan zona waktu yang konsisten. Jangan menyertakan nama nasabah, nomor rekening, token, atau data finansial pada nama file.

## 5. Stop conditions

Hentikan aktivitas terkait jika:
- Transaksi nyata berpotensi diproses atau dana bergerak.
- Data/objek di luar test population terlihat atau berubah.
- Sistem menjadi tidak stabil atau kontrol operasional terganggu.
- Batas waktu, request, atau transaksi tercapai.
- Secret atau data sensitif terpapar.
- Pemilik sistem menginstruksikan berhenti.

Setelah stop:
- Jangan mencoba variasi lanjutan.
- Catat waktu, test ID, dan kondisi.
- Beri tahu emergency contact.
- Amankan bukti yang dibutuhkan dengan cara yang disetujui.
- Lanjutkan hanya setelah izin eksplisit dan mitigasi disetujui.

## 6. Closeout

- [ ] Semua test case ditandai status dan hasilnya.
- [ ] Transaksi/objek uji direkonsiliasi dengan pemilik sistem.
- [ ] Cleanup/rollback selesai.
- [ ] Evidence tervalidasi dan tersanitasi.
- [ ] Temuan awal melalui quality review.
- [ ] Gap dan test yang tidak dilakukan dicatat.
- [ ] Debrief dengan client selesai.
- [ ] Retensi/penghapusan artefak mengikuti persetujuan.
