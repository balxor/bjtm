# Penetration Testing PoC Framework

Framework ini membantu tim menyiapkan, menjalankan, dan mengevaluasi penetration testing Proof of Concept (PoC). Dokumen mencakup tata kelola, pemodelan risiko, desain test case, eksekusi, evidence, pelaporan, dan retest.

## Tujuan

- Menghubungkan risiko bisnis dengan skenario pengujian yang terukur.
- Menetapkan scope, peran, otorisasi, dan batas operasional sebelum pengujian.
- Menyusun test case yang repeatable dengan expected behavior dan kriteria hasil.
- Mengumpulkan evidence yang relevan, aman, dan dapat ditelusuri.
- Menjelaskan dampak teknis dan bisnis serta rekomendasi remediasi.
- Mengukur hasil retest menggunakan acceptance criteria.

## Struktur dokumentasi

### Tata kelola dan delivery

- **[Prinsip persiapan PoC](docs/01-prinsip-persiapan-poc.md)**: requirement, status kesiapan, dan quality gate.
- **[Scope, governance, dan RoE](docs/02-scope-governance-roe.md)**: otorisasi, target, peran, eskalasi, dan batas pengujian.
- **[Remittance threat model](docs/03-remittance-threat-model.md)**: alur transaksi, trust boundary, dan kelompok risiko.
- **[Metodologi dan test case](docs/04-metodologi-test-case.md)**: siklus pengujian dan pencatatan hasil.
- **[Evidence dan reporting](docs/05-evidence-dan-reporting.md)**: evidence chain, laporan, dashboard, dan retest.
- **[PoC runbook](docs/06-poc-runbook.md)**: readiness, urutan demo, stop procedure, dan closeout.
- **[Tender differentiation dan scorecard](docs/07-differentiation-scorecard.md)**: kualitas teknis, demo, dan penilaian kesiapan tim.
- **[Kesiapan klien dan tim](docs/08-kesiapan-klien-dan-tim.md)**: kebutuhan proyek, peran, dan persiapan teknis.
- **[Operating model](docs/09-operating-model.md)**: workstream, deliverable, dan quality gate.

### Metodologi dan skenario remittance

- **[Remittance test catalog](docs/10-remittance-test-catalog.md)**: test case, preconditions, expected result, evidence, dan kriteria hasil.
- **[Attack-path matrix](docs/11-remittance-attack-path-matrix.md)**: likelihood × impact, prioritas, dan jalur pengujian.
- **[Sequence diagrams](docs/12-remittance-sequence-diagrams.md)**: Create-to-Submit, Approval Binding, Retry, Callback, dan Reconciliation.
- **[API Security Testing Field Playbook](docs/13-api-security-testing-field-playbook.md)**: enumerasi API, authorization, request diff, workflow, dan validasi temuan.
- **[PoC Priority Scenarios](docs/14-poc-priority-scenarios.md)**: lima skenario, prosedur, evidence, dan demo 30 menit.
- **[Synthetic Test Data Model](docs/15-synthetic-remittance-test-data.md)**: actor/role, objek, field, dan state uji.
- **[Evaluator Demo Run Sheet](docs/16-evaluator-demo-run-sheet.md)**: agenda, peran, checklist, timeline evidence, dan fallback.

### OWASP 2025 dan penilaian

- **[OWASP Top 10:2025 Mapping](docs/17-owasp-top10-2025-mapping.md)**: pemetaan kategori OWASP 2025 ke test remittance.
- **[Scoring Temuan Bank Jatim](docs/18-scoring-temuan-bank-jatim.md)**: simulasi internal 0–100 untuk triage dan prioritas demo. Bukan rubrik poin resmi Bank Jatim.

### Acuan PoC Bank Jatim

- **[Objective dan Kriteria Evaluasi PoC Bank Jatim](docs/19-objective-dan-kriteria-poc-bank-jatim.md)**: objective, urutan metodologi, deliverable, enam aspek evaluasi, RoE, dan konfirmasi jadwal.

### Template kerja

- **[Test-case template](templates/test-case-template.md)**: perencanaan dan pelaksanaan test.
- **[Finding template](templates/finding-template.md)**: dokumentasi temuan dan rekomendasi.
- **[Evidence-log template](templates/evidence-log-template.md)**: indeks evidence dan kontrol penyimpanan.

## Prinsip pelaksanaan

1. **Authorization first.** Mulai pengujian setelah otorisasi tertulis, scope, dan Rules of Engagement (RoE) disetujui.
2. **Scope control.** Setiap test case harus mengacu pada aset, akun, environment, dan metode yang diizinkan.
3. **Risk-led testing.** Prioritaskan skenario berdasarkan dampak bisnis, kemungkinan penyalahgunaan, dan relevansi kontrol.
4. **Minimum impact.** Gunakan data sintetis dan environment yang sesuai. Hindari transaksi dana nyata.
5. **Evidence-based findings.** Catat perilaku aktual, expected behavior, keterbatasan pengujian, dan evidence pendukung.
6. **Remediation and retest.** Setiap finding harus memiliki rekomendasi, owner, prioritas, dan kriteria retest.

## Alur kerja

1. Kumpulkan requirement, scope, dan kriteria penerimaan.
2. Susun scope matrix, RoE, RACI, serta jalur eskalasi.
3. Petakan alur bisnis, aset, trust boundaries, dan kontrol keamanan.
4. Buat threat model dan prioritaskan test case.
5. Siapkan akun uji, data sintetis, environment, evidence store, serta prosedur cleanup.
6. Lakukan review risiko dan readiness gate.
7. Jalankan skenario sesuai urutan PoC.
8. Dokumentasikan hasil dan susun finding register.
9. Presentasikan dampak, rekomendasi, dan retest criteria.
10. Selesaikan cleanup, rekonsiliasi objek uji, dan closeout.

## Deliverable

- Scope matrix dan Rules of Engagement.
- Threat model dan test scenario matrix.
- Test case beserta status dan evidence reference.
- PoC runbook dan agenda demonstrasi.
- Finding register dan laporan teknis.
- Executive summary dan daftar prioritas remediasi.
- Retest plan serta closeout checklist.

## Pengelolaan artefak

Simpan raw evidence, data uji, hasil scanning, request/response, dan laporan engagement pada penyimpanan yang memiliki kontrol akses, retensi, serta mekanisme penghapusan yang sesuai. Dokumentasi yang dibagikan hanya memuat data yang diperlukan dan telah disunting. Jangan menyertakan credential, token, data pribadi, atau data transaksi nyata dalam dokumen publik.
