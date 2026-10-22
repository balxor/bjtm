# Metodologi dan Test-Case Design

## Siklus pengujian

1. **Requirement review** — ekstrak persyaratan TOR/RFP/SoW dan tandai sumber setiap requirement.
2. **Authorization and scope validation** — cek aset, akun, metode, jendela, serta batas transaksi sesuai RoE.
3. **Surface and data-flow mapping** — identifikasi endpoint dan trust boundaries yang dikonfirmasi.
4. **Test design** — tulis hipotesis, preconditions, expected secure behavior, evidence, dan cleanup.
5. **Risk review** — review dampak dan tentukan guardrail serta stop conditions.
6. **Controlled execution** — lakukan test minimal-impact dengan akun dan data sintetis.
7. **Evidence validation** — verifikasi timestamp, actor, request/response, state, dan redaction.
8. **Finding triage** — pisahkan fakta observasi dari root-cause hypothesis dan estimasi impact.
9. **Remediation and retest** — definisikan perbaikan, kondisi retest, serta bukti closure.
10. **Closeout** — rekonsiliasi data uji, pastikan cleanup, lalu dapatkan sign-off.

## Elemen wajib test case

| Elemen | Penjelasan |
|---|---|
| Test ID | Unik, konsisten, dan tidak berubah selama siklus |
| Objective | Tujuan kontrol yang diuji |
| Threat/hypothesis | Kelemahan yang ingin dibuktikan atau ditolak |
| Requirement/source | Referensi TOR/RFP/SoW atau persetujuan scope |
| Preconditions | Kondisi aman yang harus benar sebelum mulai |
| Actor/role | Akun uji dan kewenangan yang diuji |
| Target | Aset/endpoint/object yang dikonfirmasi masuk scope |
| Test action | Langkah yang dapat direplikasi tanpa dampak berlebihan |
| Expected secure behavior | Perilaku yang seharusnya terjadi |
| Actual observation | Hasil faktual, termasuk hasil negatif |
| Evidence | Referensi ke bukti tersanitasi |
| Pass/fail/inconclusive | Status dan alasannya |
| Cleanup/rollback | Cara mengembalikan state uji |
| Approver | Persetujuan tambahan bila diperlukan |

## Contoh test case tingkat desain

Contoh ini adalah **template skenario**, bukan instruksi untuk target nyata.

### RT-AUTH-01 — Batas otorisasi objek

- **Tujuan:** memverifikasi backend menolak akses akun A terhadap objek sintetis yang menjadi milik akun B.
- **Precondition:** dua akun uji, dua objek sintetis, kontrol scope dan endpoint telah disetujui.
- **Aksi tingkat tinggi:** akses objek yang ditetapkan untuk akun yang tidak berwenang dengan sesi akun uji tersebut.
- **Expected secure behavior:** server menolak permintaan tanpa membocorkan data objek.
- **Evidence:** role/actor, object ID sintetis, timestamp, respons yang sudah disunting, dan hasil dari akun pemilik.
- **Cleanup:** hapus/arsipkan objek sintetis sesuai prosedur.
- **Fail condition:** akses lintas objek berhasil atau informasi sensitif terungkap; hentikan perluasan test dan eskalasi.

### RT-INT-01 — Integritas instruksi transaksi

- **Tujuan:** memastikan backend memvalidasi atribut transaksi kritis dan tidak hanya mempercayai kontrol client.
- **Precondition:** environment sandbox atau test harness dengan settlement dinonaktifkan, transaksi sintetis, persetujuan khusus.
- **Aksi tingkat tinggi:** gunakan variasi test yang disetujui untuk menilai validasi server-side.
- **Expected secure behavior:** perubahan yang tidak diizinkan ditolak; aturan bisnis dan audit trail tetap konsisten.
- **Evidence:** state sebelum/sesudah, input uji tersanitasi, respons, serta log dari pemilik sistem.
- **Cleanup:** batalkan/bersihkan transaksi sintetis sesuai prosedur yang disepakati.
- **Fail condition:** perubahan kritis diterima di luar aturan; jangan lanjutkan ke pemrosesan transaksi nyata.

## Pengelolaan hasil

Gunakan status yang konsisten:
- **PASS** — kontrol berperilaku sesuai ekspektasi pada cakupan test.
- **FAIL** — bukti menunjukkan kontrol tidak memenuhi kriteria.
- **INCONCLUSIVE** — data/akses/bukti tidak cukup untuk kesimpulan.
- **BLOCKED** — tidak dapat dilakukan karena izin, dependency, atau guardrail.
- **NOT TESTED** — tidak dilakukan, dengan alasan.

Hasil PASS bukan bukti keamanan absolut; ia hanya merujuk pada test, kondisi, dan waktu yang tercatat.
