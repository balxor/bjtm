# Metodologi dan Test-Case Design

## Siklus pengujian

1. **Requirement review:** petakan requirement, scope, dan kriteria penerimaan.
2. **Authorization and scope validation:** periksa target, akun, metode, jendela waktu, serta batas transaksi.
3. **Surface and data-flow mapping:** petakan endpoint dan trust boundaries dalam scope.
4. **Test design:** definisikan hipotesis, preconditions, expected behavior, evidence, dan cleanup.
5. **Risk review:** nilai dampak dan tetapkan guardrail serta stop conditions.
6. **Controlled execution:** jalankan test dengan akun uji dan data sintetis.
7. **Evidence review:** periksa timestamp, actor, request/response, state, dan redaction.
8. **Finding triage:** pisahkan observasi faktual, analisis penyebab, dan estimasi dampak.
9. **Remediation and retest:** tetapkan perbaikan dan kriteria uji ulang.
10. **Closeout:** rekonsiliasi objek uji, selesaikan cleanup, dan dokumentasikan sign-off.

## Elemen wajib test case

| Elemen | Penjelasan |
|---|---|
| Test ID | Identifier unik yang dipakai sepanjang siklus |
| Objective | Tujuan kontrol yang diuji |
| Threat/hypothesis | Kondisi yang ingin divalidasi |
| Requirement reference | Acuan requirement atau RoE |
| Preconditions | Kondisi yang harus dipenuhi sebelum test |
| Actor/role | Akun uji dan kewenangan |
| Target | Aset atau endpoint dalam scope |
| Test action | Langkah pengujian yang diizinkan |
| Expected secure behavior | Perilaku yang diharapkan |
| Actual observation | Hasil yang diamati |
| Evidence | Referensi evidence tersanitasi |
| Status | PASS, FAIL, INCONCLUSIVE, BLOCKED, atau NOT TESTED |
| Cleanup/rollback | Prosedur pemulihan state uji |
| Approver | Pihak yang memberi persetujuan jika diperlukan |

## Contoh test case: RT-AUTH-01

- **Tujuan:** memeriksa apakah backend menolak akses akun A terhadap objek sintetis milik akun B.
- **Precondition:** tersedia dua akun uji, dua objek sintetis, dan endpoint dalam scope.
- **Aksi tingkat tinggi:** akses objek akun lain menggunakan sesi akun uji.
- **Expected behavior:** server menolak request dan tidak mengembalikan data objek yang tidak diotorisasi.
- **Evidence:** actor/role, object ID sintetis, timestamp, respons tersanitasi, serta hasil akses oleh pemilik objek.
- **Cleanup:** hapus atau arsipkan objek sintetis sesuai prosedur engagement.
- **Fail condition:** akses lintas objek berhasil atau data terungkap. Hentikan perluasan test dan lakukan eskalasi.

## Contoh test case: RT-INT-01

- **Tujuan:** menilai validasi server-side pada atribut kritis instruksi transaksi.
- **Precondition:** environment uji dengan settlement dinonaktifkan, transaksi sintetis, dan otorisasi untuk skenario tersedia.
- **Aksi tingkat tinggi:** jalankan variasi input yang termasuk test plan untuk menilai validasi server-side.
- **Expected behavior:** perubahan yang tidak diizinkan ditolak dan state transaksi tetap sesuai aturan bisnis.
- **Evidence:** state sebelum/sesudah, input uji tersanitasi, respons, serta log terkait.
- **Cleanup:** batalkan atau bersihkan transaksi sintetis sesuai prosedur.
- **Fail condition:** sistem menerima perubahan atribut kritis yang melanggar aturan. Jangan lanjutkan ke transaksi nyata.

## Status hasil

- **PASS:** perilaku sesuai expected behavior pada cakupan test.
- **FAIL:** evidence menunjukkan kontrol tidak memenuhi kriteria.
- **INCONCLUSIVE:** evidence atau akses tidak cukup untuk menyimpulkan hasil.
- **BLOCKED:** test tidak dapat dijalankan karena prasyarat, izin, atau guardrail.
- **NOT TESTED:** test tidak dijalankan dan alasan dicatat.

Hasil PASS hanya berlaku untuk kondisi, test case, environment, dan waktu yang didokumentasikan.
