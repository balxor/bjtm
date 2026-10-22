# Operating Model

## Workstream

| ID | Workstream | Aktivitas | Output |
|---|---|---|---|
| WS-01 | Governance and scope | Otorisasi, scope, RoE, RACI, eskalasi | Scope matrix dan RoE |
| WS-02 | Business and architecture mapping | Alur transaksi, komponen, trust boundary | Data-flow dan asset map |
| WS-03 | Threat modeling | Risiko, kontrol, attack hypothesis | Threat model dan scenario matrix |
| WS-04 | Test design | Preconditions, steps, expected behavior, cleanup | Test-case register |
| WS-05 | Environment readiness | Akun, data sintetis, tooling, evidence pipeline | Readiness checklist |
| WS-06 | PoC execution | Eksekusi terkontrol dan pencatatan hasil | Test log dan evidence index |
| WS-07 | Reporting and triage | Severity, impact, rekomendasi, quality review | Finding register dan laporan |
| WS-08 | Retest and closeout | Verifikasi perbaikan, cleanup, sign-off | Retest report dan closeout record |

## Quality gate per tahap

| Gate | Kriteria masuk | Kriteria keluar |
|---|---|---|
| G1: Scope approved | Sponsor, scope owner, dan target teridentifikasi | Scope, exclusions, RoE, serta escalation path disetujui |
| G2: Test design ready | Requirement dan alur bisnis tersedia | Test case direview; expected behavior dan cleanup ditetapkan |
| G3: Environment ready | Target, akun, dan data uji disediakan | Akun dan data diuji; stop conditions serta evidence workflow siap |
| G4: Execution complete | Go/no-go disetujui | Status test, evidence, dan cleanup dicatat |
| G5: Reporting complete | Evidence tersedia untuk triage | Finding direview, dampak dijelaskan, dan rekomendasi disusun |
| G6: Closeout complete | Remediation dan retest status tersedia | Objek uji direkonsiliasi, artefak ditangani, dan sign-off dicatat |

## Register deliverable

| Deliverable | Owner | Reviewer | Format | Status |
|---|---|---|---|---|
| Scope matrix | Engagement lead | Scope owner | Markdown/XLSX | TBD |
| Rules of Engagement | Engagement lead | Scope owner/legal | DOCX/PDF | TBD |
| Threat model | Test lead | Technical lead | Markdown/diagram | TBD |
| Test-case register | Pentest lead | Technical reviewer | Markdown/XLSX | TBD |
| Evidence index | Evidence lead | Reporting lead | Markdown/CSV | TBD |
| Finding register | Reporting lead | Technical lead | Markdown/XLSX | TBD |
| Technical report | Reporting lead | Engagement lead | DOCX/PDF | TBD |
| Retest report | Test lead | Scope owner | DOCX/PDF | TBD |
| Closeout record | Engagement lead | Scope owner | Markdown/PDF | TBD |

## Change control

Setiap perubahan scope, target, teknik, jadwal, batas volume, atau treatment data harus:
1. Dicatat pada change log.
2. Memuat alasan dan dampak.
3. Menetapkan pihak pemberi persetujuan.
4. Memperbarui scope matrix, RoE, test case, dan jadwal bila relevan.
5. Disampaikan kepada tim sebelum eksekusi perubahan.

## Status proyek

Gunakan status berikut pada workstream dan deliverable:
- **Not started**
- **In progress**
- **Blocked**
- **In review**
- **Approved**
- **Complete**

Setiap status harus disertai owner dan next action bila pekerjaan belum selesai.
