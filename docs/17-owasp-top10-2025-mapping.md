# OWASP Top 10:2025 Mapping untuk Pengujian Remittance

## 1. Acuan utama

Metodologi pengujian aplikasi pada framework ini menggunakan **OWASP Top 10:2025** sebagai klasifikasi risiko aplikasi web. Daftar ini membantu mengelompokkan risiko dan menentukan area test; OWASP Top 10 bukan tabel poin untuk setiap bug dan bukan pengganti penilaian risiko bisnis.

Acuan resmi: [OWASP Top 10:2025](https://top10.owasp.org/2025/).

Untuk risiko yang khusus pada API, gunakan [OWASP API Security Top 10:2023](https://api-security.owasp.org/editions/2023/en/0x11-t10/) sebagai referensi pelengkap. Jangan menyebut API Security Top 10:2023 sebagai edisi 2025.

## 2. Daftar kategori OWASP Top 10:2025

| Kode | Kategori resmi | Fokus untuk remittance | Test case yang terkait |
|---|---|---|---|
| A01:2025 | Broken Access Control | Akses lintas customer/tenant, fungsi role, object/property authorization, approval dan pembatasan operasi | RT-AUTH-01, RT-AUTH-02, RT-DATA-01 |
| A02:2025 | Security Misconfiguration | Konfigurasi gateway/service, route terbuka, debug/error detail, CORS, default configuration, serta policy yang tidak konsisten | RT-API-01, RT-CFG-01 |
| A03:2025 | Software Supply Chain Failures | Dependency dan package, sumber artefak, build/release pipeline, hak akses CI/CD, signing/provenance | RT-SC-01 bila source code, pipeline, atau dependency masuk scope |
| A04:2025 | Cryptographic Failures | Perlindungan data sensitif saat transit/tersimpan, pengelolaan key/secret, token dan material kriptografi | RT-CRYPTO-01 |
| A05:2025 | Injection | Input yang mencapai query, interpreter, template, command, atau parser tanpa penanganan yang aman | RT-INJ-01 pada komponen yang diizinkan |
| A06:2025 | Insecure Design | Kelemahan business logic, aturan limit, separation of duties, approval binding, dan penyalahgunaan flow sensitif | RT-TXN-01, RT-WF-01, RT-IDEM-01, RT-LIM-01, RT-FX-01, RT-ABUSE-01 |
| A07:2025 | Authentication Failures | Login/session lifecycle, token validation, identity antarservice, autentikasi callback, dan pemulihan akun sesuai scope | RT-IAM-01, RT-AUTH-02, RT-CB-01 |
| A08:2025 | Software or Data Integrity Failures | Perubahan state tidak sah, data atau event yang tidak tepercaya, duplicate processing, integritas callback, dan konsistensi transaksi | RT-TXN-01, RT-WF-01, RT-IDEM-01, RT-CB-01, RT-INT-01, RT-REC-01 |
| A09:2025 | Security Logging and Alerting Failures | Audit event tidak lengkap, tidak ada korelasi, tidak ada alert yang diwajibkan, atau aktivitas kritis sulit ditelusuri | RT-LOG-01, RT-REC-01 |
| A10:2025 | Mishandling of Exceptional Conditions | Timeout, partial failure, retry, exception, rollback, event out-of-order, dan state yang tidak konsisten | RT-IDEM-01, RT-LIM-01, RT-INT-01, RT-CB-01, RT-REC-01 |

Pemetaan di atas merupakan hubungan kerja yang relevan untuk pengujian remittance, bukan klaim bahwa setiap area sudah diuji atau ditemukan bermasalah. Status test tetap mengikuti evidence, scope, dan kondisi environment.

## 3. Pemetaan ringkas katalog ke OWASP

| Test ID | Area uji | OWASP Top 10:2025 utama | API Security Top 10:2023 pelengkap |
|---|---|---|---|
| RT-AUTH-01 | Object-level authorization | A01 | API1:2023, API3:2023 |
| RT-AUTH-02 | Function authorization / separation of duties | A01, A07 | API2:2023, API5:2023 |
| RT-TXN-01 | Integritas atribut transaksi | A06, A08 | API3:2023, API6:2023 |
| RT-WF-01 | State transition / approval binding | A01, A06, A08 | API5:2023, API6:2023 |
| RT-IDEM-01 | Idempotency / duplicate handling | A06, A08, A10 | API4:2023, API6:2023 |
| RT-LIM-01 | Limit / atomicity | A06, A10 | API4:2023, API6:2023 |
| RT-FX-01 | Fee, FX quote, expiry, rounding | A06, A08 | API6:2023 |
| RT-CB-01 | Callback authenticity / duplicate event | A07, A08, A10 | API2:2023, API8:2023, API10:2023 |
| RT-INT-01 | Downstream timeout / retry | A08, A10 | API10:2023 |
| RT-REC-01 | Reconciliation / ledger consistency | A08, A09, A10 | API10:2023 |
| RT-IAM-01 | Token, session, service identity | A07 | API2:2023, API8:2023 |
| RT-DATA-01 | Data exposure | A01 | API3:2023 |
| RT-LOG-01 | Audit event / correlation | A09 | API10:2023 |
| RT-API-01 | Inventory / version / configuration | A02 | API8:2023, API9:2023 |
| RT-ABUSE-01 | Sensitive business-flow abuse | A06, A10 | API4:2023, API6:2023 |
| RT-CFG-01 | Security configuration | A02 | API8:2023 |
| RT-SC-01 | Software supply chain (conditional) | A03 | API8:2023 |
| RT-CRYPTO-01 | Cryptographic controls | A04 | API2:2023, API8:2023 |
| RT-INJ-01 | Injection handling | A05 | API8:2023 |

Kategori dapat lebih dari satu bila temuan menunjukkan beberapa akar masalah. Pilih kategori utama berdasarkan root cause yang didukung evidence, lalu tambahkan kategori sekunder jika perlu. Jangan mengklasifikasikan temuan hanya dari nama test case.

## 4. Scope dan status cakupan

Katalog mencakup test case khusus untuk security configuration (RT-CFG-01), supply chain (RT-SC-01), cryptographic controls (RT-CRYPTO-01), serta injection handling (RT-INJ-01). Pelaksanaan tiap test tetap bergantung pada akses, environment, serta scope tertulis:

1. **A03 Software Supply Chain Failures:** RT-SC-01 hanya dijalankan bila repository, SBOM, pipeline, release artifact, atau dependency memang termasuk scope. Untuk black-box-only PoC, area ini bisa berstatus OUT OF SCOPE.
2. **A04 Cryptographic Failures:** RT-CRYPTO-01 memerlukan requirement atau konfigurasi yang bisa diperiksa. Jangan mencoba memecahkan key atau mengakses data nasabah.
3. **A05 Injection:** RT-INJ-01 menggunakan payload minimal dan non-destruktif pada endpoint yang diizinkan.
4. **A02 Security Misconfiguration:** RT-CFG-01 memeriksa konfigurasi dan route yang berada dalam scope.
5. **A09 Security Logging & Alerting Failures:** RT-LOG-01 mencakup audit/correlation; alerting diuji hanya jika log/SIEM/alert mechanism termasuk scope.

Jangan menyatakan kategori sudah tercakup hanya karena tercantum pada tabel. Beri status **TESTED**, **NOT TESTED**, **BLOCKED**, atau **OUT OF SCOPE**, dengan alasan dan evidence reference.

## 5. Aturan klasifikasi temuan

- Cantumkan kode dan nama OWASP Top 10:2025 pada finding.
- Bila relevan, cantumkan API Security Top 10:2023, CWE, dan CVSS sebagai referensi tambahan.
- Pisahkan klasifikasi OWASP, technical severity, business risk, dan skor kesiapan vendor. Keempatnya memiliki tujuan berbeda.
- Tetapkan severity berdasarkan exploitability, access required, affected data/process, business impact, dan evidence yang ada.
- Jangan menyimpulkan dampak transaksi finansial aktual dari test yang hanya dijalankan pada synthetic data atau sandbox.
- Bila kontrol bekerja sesuai expected behavior, rekam hasil PASS beserta positive/negative control yang relevan.

## 6. Referensi

- [OWASP Top 10:2025](https://top10.owasp.org/2025/)
- [OWASP Top 10:2025 Introduction](https://top10.owasp.org/2025/0x00_2025-Introduction/)
- [OWASP API Security Top 10:2023](https://api-security.owasp.org/editions/2023/en/0x11-t10/)
- [OWASP ASVS](https://asvs.owasp.org/)
- [OWASP Web Security Testing Guide](https://wstg.org/)
- [Remittance Test Catalog](10-remittance-test-catalog.md)
- [Remittance Attack-Path Matrix](11-remittance-attack-path-matrix.md)
- [Scoring Temuan untuk Persiapan Evaluasi Bank Jatim](18-scoring-temuan-bank-jatim.md)
