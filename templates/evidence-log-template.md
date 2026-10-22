# Evidence Log Template

Gunakan log ini sebagai indeks evidence. Simpan artefak mentah pada evidence store dengan kontrol akses, klasifikasi, dan retensi sesuai kebijakan engagement.

| Field | Nilai |
|---|---|
| Engagement ID | TBD |
| Test Case ID | TBD |
| Finding ID (jika ada) | TBD |
| Evidence ID | TBD |
| Timestamp dan timezone | TBD |
| Actor / role uji | TBD |
| Target | TBD |
| Synthetic object / transaction ID | TBD |
| Evidence type | Request / response / screenshot / log / state snapshot / lainnya |
| File reference | TBD |
| Sanitization status | Pending / Complete |
| Reviewer | TBD |
| Integrity/hash reference (opsional) | TBD |
| Access classification | TBD |
| Retention / deletion date | TBD |
| Notes / limitation | TBD |

## Review checklist

- [ ] Evidence terkait dengan test case dan finding yang tepat.
- [ ] Timestamp dan zona waktu konsisten.
- [ ] Identifier memakai data sintetis jika memungkinkan.
- [ ] Secret, credential, data pribadi, dan informasi finansial telah disunting.
- [ ] Evidence menunjukkan hasil aktual dan expected behavior.
- [ ] Artefak mentah disimpan sesuai kontrol akses dan retensi.
- [ ] Salinan laporan hanya memuat data yang diperlukan.
- [ ] Hash/integrity reference dicatat bila dibutuhkan.
- [ ] Akses serta penghapusan mengikuti kebijakan engagement.

## Contoh ID artefak

- ENG-POC-01_RT-AUTH-01_20261022T103000_request-redacted.txt
- ENG-POC-01_RT-AUTH-01_20261022T103000_response-redacted.txt
- ENG-POC-01_RT-AUTH-01_20261022T103000_state-before-after.png

Gunakan engagement ID dan format penamaan yang ditetapkan dalam RoE.
