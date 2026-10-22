# Evidence Log Template

> Gunakan log ini sebagai indeks. Artefak mentah harus disimpan pada evidence repository yang disetujui dan dibatasi aksesnya, bukan di repository GitHub publik.

| Field | Nilai |
|---|---|
| Engagement ID | TBD |
| Test Case ID | TBD |
| Finding ID (jika ada) | TBD |
| Evidence ID | TBD |
| Timestamp dan timezone | TBD |
| Actor / role uji | TBD |
| Target yang disetujui | TBD |
| Synthetic object / transaction ID | TBD |
| Evidence type | Request / response / screenshot / log / state snapshot / lainnya |
| File reference di secure vault | TBD |
| Sanitization status | Pending / Complete |
| Reviewer | TBD |
| Integrity/hash reference (opsional) | TBD |
| Access classification | TBD |
| Retention / deletion date | TBD |
| Notes / limitation | TBD |

## Review checklist

- [ ] Evidence berkaitan dengan test case dan finding yang benar.
- [ ] Timestamp dan zona waktu konsisten.
- [ ] Identifier menggunakan data sintetis jika memungkinkan.
- [ ] Secret, credential, PII, dan financial data telah di-redact.
- [ ] Evidence menunjukkan observed behavior dan bukan kesimpulan tanpa konteks.
- [ ] Raw evidence disimpan sesuai kontrol akses dan retensi.
- [ ] Salinan untuk laporan tidak mengungkap data yang tidak perlu.
- [ ] Hash/integrity reference dicatat bila proses membutuhkan.
- [ ] Access serta disposal mengikuti kebijakan engagement.

## Contoh ID artefak

- BJ-POC-01_RT-AUTH-01_20261022T103000_request-redacted.txt
- BJ-POC-01_RT-AUTH-01_20261022T103000_response-redacted.txt
- BJ-POC-01_RT-AUTH-01_20261022T103000_state-before-after.png

Contoh hanya ilustratif; sesuaikan engagement ID dan convention dengan aturan klien.
