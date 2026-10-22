# Remittance Test Catalog

## 1. Tujuan

Catalog ini mengubah threat model remittance menjadi kandidat test case dengan precondition, prosedur terkontrol, expected behavior, evidence, dan kriteria hasil. Masukkan hanya skenario yang sesuai scope, arsitektur, dan RoE engagement.

## 2. Label dan test-data model

### Status hasil
- **PASS:** kontrol memenuhi expected behavior pada kondisi pengujian.
- **FAIL:** evidence menunjukkan kontrol tidak memenuhi acceptance criteria.
- **INCONCLUSIVE:** hasil tidak dapat diputuskan dari evidence yang tersedia.
- **BLOCKED:** prasyarat atau izin belum terpenuhi.
- **NOT TESTED:** test tidak dijalankan dan alasannya dicatat.

### Test identity
Gunakan identifier nonproduksi untuk setiap entitas:
- Customer A dan Customer B.
- Maker A dan Checker B.
- Beneficiary A dan Beneficiary B.
- Transaction A, Reference A, dan Correlation ID A.
- Test environment dan waktu eksekusi.

Jangan gunakan identifier rekening atau data pelanggan nyata dalam dataset PoC.

## 3. Test catalog ringkas

| Test ID | Domain | Tujuan | Data/komponen | OWASP Top 10:2025 | Prioritas awal |
|---|---|---|---|---|---|
| RT-AUTH-01 | Object authorization | Membatasi akses per objek | Dua akun, dua beneficiary/transaksi | A01:2025 | P1 |
| RT-AUTH-02 | Function authorization | Membatasi tindakan berdasarkan role | Maker, checker, admin/support bila tersedia | A01:2025, A07:2025 | P1 |
| RT-TXN-01 | Transaction integrity | Memastikan properti finansial divalidasi server-side | Transaksi sintetis | A06:2025, A08:2025 | P1 |
| RT-WF-01 | Workflow integrity | Memastikan state transition dan approval benar | Workflow sandbox | A01:2025, A06:2025, A08:2025 | P1 |
| RT-IDEM-01 | Idempotency / duplicate | Mencegah efek ganda akibat retry | Sandbox connector | A06:2025, A08:2025, A10:2025 | P1 |
| RT-LIM-01 | Limit enforcement | Memastikan limit berlaku atomik | Limit test configuration | A06:2025, A10:2025 | P1 |
| RT-FX-01 | Fee/FX/quote | Menjaga konsistensi nilai dan expiry | Quote/fee fixture | A06:2025, A08:2025 | P2 |
| RT-CB-01 | Callback/webhook | Validasi authenticity dan replay | Callback simulator | A07:2025, A08:2025, A10:2025 | P1 jika berlaku |
| RT-INT-01 | Downstream integration | Menangani timeout, error, dan retry | Mock/sandbox partner | A08:2025, A10:2025 | P1/P2 |
| RT-REC-01 | Reconciliation | Menjaga konsistensi transaction, ledger, dan status | Synthetic transaction lifecycle | A08:2025, A09:2025, A10:2025 | P1/P2 |
| RT-IAM-01 | Token/session | Validasi identity dan privilege lifecycle | Test users/tokens | A07:2025 | P1/P2 |
| RT-DATA-01 | Data exposure | Membatasi properti data yang dikembalikan | API response, export, notification | A01:2025 | P2 |
| RT-LOG-01 | Auditability | Mencatat event keamanan dan transaksi | Audit logs/SIEM | A09:2025 | P2 |
| RT-API-01 | API inventory/configuration | Memeriksa route, version, dan policy | Approved API inventory | A02:2025 | P2 |
| RT-ABUSE-01 | Sensitive business flows | Menguji abuse guardrail pada fungsi sensitif | Test account dan rate limit sandbox | A06:2025, A10:2025 | P2 |
| RT-CFG-01 | Security configuration | Memeriksa konfigurasi, route, debug, dan policy yang terekspos | Gateway/service dalam scope | A02:2025 | P1/P2 |
| RT-SC-01 | Software supply chain | Memeriksa dependency/build/release integrity yang disetujui | Repo, SBOM, pipeline atau artefak jika in scope | A03:2025 | Conditional |
| RT-CRYPTO-01 | Cryptographic controls | Memeriksa perlindungan data dan lifecycle key/secret yang termasuk scope | TLS/config/secret test fixture | A04:2025 | P1/P2 |
| RT-INJ-01 | Injection | Menguji penanganan input pada parser/query/interpreter di endpoint terpilih | Synthetic input dan endpoint yang disetujui | A05:2025 | P1/P2 |

Priority P1/P2 merupakan urutan awal untuk perencanaan, bukan severity hasil. Tentukan ulang berdasarkan business impact, exposure, desain aktual, dan kriteria evaluasi. RT-SC-01 hanya berlaku jika repository, dependency, SBOM, pipeline, atau artefak release masuk scope.

## 4. Test cases

### RT-AUTH-01: Object-level authorization

**Tujuan:** memeriksa authorization pada endpoint yang menerima customer ID, beneficiary ID, transaction ID, atau reference.

**Preconditions**
- Dua akun uji dengan ownership terpisah.
- Objek sintetis untuk masing-masing akun.
- Endpoint detail, status, dan perubahan objek tercantum dalam scope.
- Data uji tidak dapat memengaruhi transaksi nyata.

**Prosedur**
1. Lakukan operasi normal atas objek sendiri menggunakan akun A dan catat hasil baseline.
2. Lakukan operasi baca atas objek akun B menggunakan akun A melalui fungsi yang tercantum dalam test plan.
3. Jika operasi perubahan termasuk scope, gunakan objek disposable dan field nonfinansial yang aman.
4. Periksa response, backend state yang disediakan pemilik sistem, serta event audit.
5. Ulangi dengan kontrol positif menggunakan akun pemilik objek.

**Expected behavior**
- Akses akun A terhadap objek milik B ditolak sesuai policy.
- Respons tidak mengungkap informasi sensitif objek B.
- State objek B tidak berubah.
- Kontrol akses dilakukan di server, bukan hanya disembunyikan pada UI.

**Evidence**
- Test ID, actor/role, synthetic object ID, timestamp, request/response tersanitasi, state sebelum/sesudah, correlation ID, audit event.

**FAIL criteria**
- Akun A dapat membaca data yang tidak diizinkan atau melakukan perubahan pada objek B.

**Cleanup**
- Hapus/arsipkan objek uji dan konfirmasi state dengan pemilik sistem.

### RT-AUTH-02: Function-level authorization dan separation of duties

**Tujuan:** menguji apakah fungsi sensitif hanya tersedia bagi role yang berwenang.

**Preconditions**
- Role matrix untuk maker, checker, admin, dan support jika ada.
- Akun uji terpisah per role.
- Operasi approval dan administrasi yang masuk scope telah diidentifikasi.

**Prosedur**
1. Rekam operasi yang sah untuk masing-masing role sebagai baseline.
2. Jalankan negative test terhadap fungsi yang tidak diizinkan untuk role yang dipilih.
3. Verifikasi policy self-approval jika maker dan checker harus berbeda.
4. Uji apakah perubahan kewenangan atau konfigurasi membutuhkan approval terpisah sesuai desain.
5. Periksa audit event untuk operasi yang ditolak maupun diizinkan.

**Expected behavior**
- Fungsi terlarang ditolak oleh backend.
- UI restriction dan API authorization konsisten.
- Separation of duties diberlakukan server-side.
- Operasi administratif dan perubahan konfigurasi mempunyai event audit yang memadai.

**FAIL criteria**
- Role yang tidak berwenang dapat menjalankan fungsi sensitif atau satu actor melewati aturan separation of duties.

### RT-TXN-01: Server-side transaction integrity

**Tujuan:** memastikan atribut transaksi yang memengaruhi hak, nilai, biaya, dan status ditentukan atau divalidasi oleh server.

**Field untuk evaluasi**
- Owner/customer/tenant.
- Beneficiary reference.
- Amount dan currency.
- Fee, FX quote, expiry, total debit.
- Transaction status, approval status, dan screening result.
- Business reference dan idempotency key.

**Preconditions**
- Transaksi sintetis.
- Test endpoint atau harness telah disetujui.
- Settlement dana nyata dinonaktifkan atau dijamin tidak dapat terjadi melalui mekanisme yang disetujui.

**Prosedur**
1. Buat transaksi melalui alur normal dan simpan nilai request serta hasil server.
2. Identifikasi field yang dapat diedit oleh pengguna berdasarkan kontrak API.
3. Untuk field yang seharusnya server-controlled, jalankan negative test menggunakan transaksi disposable.
4. Bandingkan response, state transaksi, nilai hasil perhitungan, dan event audit.
5. Periksa apakah perubahan memerlukan validasi ulang atau approval ulang sesuai policy.

**Expected behavior**
- Field server-controlled tidak dapat diubah hanya dengan mengubah request client.
- Nilai finansial dan status divalidasi terhadap data serta aturan server-side.
- Penolakan tidak mengubah state transaksi.
- Audit trail mencatat perubahan dan hasil validasi.

**FAIL criteria**
- Field kritis yang seharusnya terlindungi diterima atau status transaksi berubah tanpa aturan yang diwajibkan.

### RT-WF-01: State transition dan approval binding

**Tujuan:** memastikan lifecycle transaksi tidak melewati validasi atau approval.

**Preconditions**
- State machine dan transisi yang diizinkan.
- Maker/checker yang berbeda bila diperlukan.
- Transaksi sintetis dengan jalur downstream non-settlement.

**Prosedur**
1. Jalankan satu transaksi melalui alur normal untuk memperoleh state baseline.
2. Dari interface uji yang disetujui, verifikasi respons sistem terhadap transisi yang tidak tersedia bagi current actor atau current state.
3. Uji perubahan atribut transaksi setelah approval pada transaksi disposable.
4. Periksa apakah approval lama tetap berlaku atau sistem meminta approval baru sesuai policy.
5. Bandingkan state orchestrator, workflow, connector, dan audit event jika aksesnya disediakan.

**Expected behavior**
- Backend menolak transisi yang tidak valid.
- Perubahan atribut kritis membatalkan approval lama atau memicu approval ulang sesuai aturan.
- Transaksi tidak dikirim ke downstream sebelum seluruh prasyarat dipenuhi.

**FAIL criteria**
- Actor dapat melompati state, menggunakan approval yang tidak lagi valid, atau menyebabkan dispatch ketika prasyarat belum dipenuhi.

### RT-IDEM-01: Idempotency dan duplicate handling

**Tujuan:** memeriksa bahwa retry atau request duplikat tidak membuat lebih dari satu efek bisnis untuk satu instruksi transaksi.

**Preconditions**
- Payment adapter sandbox atau mock.
- Satu logical transaction sintetis.
- Idempotency behavior dan transaction ceiling tertulis pada RoE.
- Jumlah retry dibatasi secara eksplisit.

**Prosedur**
1. Submit satu transaksi sintetis dan catat transaction ID, downstream reference, dan hasilnya.
2. Lakukan retry terbatas pada instruksi yang sama sesuai jumlah yang disetujui.
3. Jika concurrency masuk scope, jalankan dengan concurrency rendah yang ditetapkan pada RoE.
4. Bandingkan jumlah internal record, outbound dispatch, posting pada sandbox, dan audit event.
5. Pastikan status final serta reference konsisten.

**Expected behavior**
- Retry ditangani sesuai kontrak API dan tidak menghasilkan posting/dispatch kedua.
- Sistem menyediakan hasil yang konsisten, misalnya mengembalikan hasil transaksi yang sudah ada atau menolak duplicate.
- Duplicate attempt tercatat untuk audit.

**FAIL criteria**
- Satu logical transaction menghasilkan lebih dari satu financial effect atau status internal tidak dapat direkonsiliasi.

**Guardrail**
- Jangan menjalankan replay atau concurrency test terhadap payment rail produksi.

### RT-LIM-01: Limit enforcement dan atomicity

**Tujuan:** memeriksa penerapan limit transaksi dan limit agregat pada kondisi normal serta kondisi request yang bersamaan bila disetujui.

**Preconditions**
- Limit policy dan nilai uji yang ditetapkan pemilik sistem.
- Test account dan transaksi sintetis.
- Persetujuan khusus jika concurrency test dijalankan.

**Prosedur**
1. Jalankan transaksi sintetis di bawah limit untuk memperoleh baseline.
2. Jalankan satu negative test pada batas limit yang telah ditetapkan.
3. Jika concurrency test disetujui, lakukan sejumlah kecil request yang telah ditentukan untuk memeriksa atomicity limit reservation dan commit.
4. Bandingkan hasil API dengan limit service serta transaction store.
5. Hentikan uji setelah mencapai batas request atau transaksi yang disetujui.

**Expected behavior**
- Transaksi yang melampaui kebijakan ditolak sebelum dispatch atau posting.
- Limit tidak terlewati karena dua request dievaluasi secara bersamaan.
- Perhitungan limit konsisten untuk channel dan role yang termasuk scope.

**FAIL criteria**
- Limit dapat dilampaui pada hasil yang tercatat atau akibat request bersamaan di environment test.

### RT-FX-01: Fee, FX quote, expiry, dan rounding

**Tujuan:** memvalidasi konsistensi nilai antara quote, konfirmasi, approval, submission, dan record akhir.

**Preconditions**
- Aturan fee/FX/rounding dan masa berlaku quote tersedia.
- Test quote atau data fixture sintetis.

**Prosedur**
1. Buat quote menggunakan alur normal dan simpan quote ID, versi, expiry, rate, fee, dan total.
2. Bandingkan nilai quote dengan detail transaksi sebelum approval dan sebelum submit.
3. Jalankan skenario quote expired atau update rate menggunakan test fixture yang disediakan.
4. Evaluasi hasil rounding dan precision untuk kasus yang telah ditentukan.
5. Bandingkan nilai final yang direkam sistem dengan aturan bisnis.

**Expected behavior**
- Quote expired ditolak atau diperbarui sesuai policy.
- Nilai fee/FX/total konsisten serta dihitung pada sisi server.
- Approval mengikat ke nilai yang disetujui.
- Tidak ada perubahan nilai yang tidak diinformasikan melalui mekanisme yang berlaku.

**FAIL criteria**
- Nilai final tidak sesuai aturan quote, pembulatan, expiry, atau approval yang berlaku.

### RT-CB-01: Callback/webhook authenticity dan duplicate event

**Tujuan:** memverifikasi authenticity, integrity, replay handling, dan state transition pada status dari downstream.

**Preconditions**
- Callback simulator atau sandbox endpoint yang dikendalikan.
- Mekanisme authentication/signature dan event ID diketahui.
- Transaksi sintetis dengan status yang aman.

**Prosedur**
1. Kirim callback valid menggunakan simulator dan catat perubahan state.
2. Uji event negatif dengan signature atau authentication metadata yang tidak valid melalui simulator.
3. Ulangi event test yang sama untuk menilai duplicate handling.
4. Uji event out-of-order hanya jika tersedia fixture yang aman dan desain memiliki aturan ordering.
5. Bandingkan response, state transaction, downstream reference, dan audit trail.

**Expected behavior**
- Event dengan authenticity gagal ditolak tanpa mengubah state.
- Event duplikat tidak menghasilkan efek ganda.
- Status hanya bergerak melalui transisi yang diizinkan.
- Event diterima atau ditolak memiliki audit record yang dapat ditelusuri.

**FAIL criteria**
- Event tidak valid mengubah transaksi atau event duplikat/out-of-order menimbulkan state yang melanggar aturan.

**Guardrail**
- Jangan mengirim forged callback ke sistem partner atau endpoint production.

### RT-INT-01: Downstream timeout, error, dan retry handling

**Tujuan:** memeriksa penanganan ketidakpastian ketika partner atau payment service tidak merespons secara normal.

**Preconditions**
- Mock downstream atau sandbox.
- Respons uji yang telah ditetapkan: success, timeout, error, pending.
- Transaction reference sintetis.

**Prosedur**
1. Jalankan transaksi pada kondisi downstream normal.
2. Gunakan mock/sandbox untuk mensimulasikan timeout atau error sesuai test plan.
3. Catat status transaksi yang dilaporkan kepada pengguna dan status internal.
4. Periksa strategi retry, idempotency key, serta batas retry.
5. Pastikan pemulihan tidak menyebabkan dispatch atau posting ganda.

**Expected behavior**
- Status ambiguous direpresentasikan secara eksplisit, misalnya pending/unknown jika sesuai desain.
- Retry bounded dan konsisten terhadap idempotency contract.
- Transaksi masuk ke reconciliation/exception handling bila status tidak dapat dipastikan.
- Tidak ada false success atau duplicate side effect.

### RT-REC-01: Reconciliation dan ledger consistency

**Tujuan:** memastikan transaksi dapat ditelusuri dari instruction sampai downstream status, ledger posting, dan reconciliation record.

**Preconditions**
- Sandbox transaction/ledger atau test fixture yang mewakili state tersebut.
- Akses read-only ke record yang diperlukan.
- Transaction ID dan correlation ID sintetis.

**Prosedur**
1. Jalankan satu transaksi sintetis dengan alur sandbox normal.
2. Kumpulkan transaction ID, downstream reference, ledger reference, status, dan correlation ID.
3. Gunakan fixture untuk status completed, pending, timeout, atau exception.
4. Cocokkan catatan orchestrator, connector, downstream, ledger, dan reconciliation.
5. Periksa exception queue dan approval untuk koreksi/reversal jika fungsi itu masuk scope.

**Expected behavior**
- Mismatch status atau posting menghasilkan exception yang dapat ditelusuri.
- Koreksi, reprocessing, dan reversal dibatasi oleh role yang sesuai.
- Tidak ada perubahan ledger langsung dari role yang tidak berwenang.
- Audit record menghubungkan keputusan dan hasil koreksi.

### RT-IAM-01: Token, session, dan service identity

**Tujuan:** memvalidasi lifecycle token/session serta privilege yang terikat pada user dan service identity.

**Preconditions**
- Akun uji serta alur login/logout/revocation yang tersedia.
- Policy mengenai expiry, scope, audience, issuer, dan role.

**Prosedur**
1. Jalankan fungsi normal dengan token valid sebagai baseline.
2. Uji request menggunakan token expired, revoked, malformed, atau scope/role yang tidak sesuai, menggunakan test harness yang disetujui.
3. Verifikasi perilaku setelah logout dan perubahan privilege sesuai kemampuan sistem.
4. Review identitas service-to-service, privilege minimum, dan penyimpanan secret yang dapat diperiksa dalam scope.
5. Periksa logging untuk memastikan token dan secret tidak dicatat.

**Expected behavior**
- Token hanya berlaku untuk identitas, audience, waktu, dan privilege yang benar.
- Token revoked atau expired tidak mengakses fungsi terlindungi.
- Service identity memiliki akses minimum yang diperlukan.
- Secret tidak muncul dalam log atau response error.

### RT-DATA-01: Data exposure pada response, export, notification, dan error

**Tujuan:** memeriksa minimisasi data dan pembatasan akses per role.

**Preconditions**
- Akun uji dengan role berbeda.
- Data customer dan transaksi sintetis.
- Endpoint/report/export yang termasuk scope.

**Prosedur**
1. Jalankan operasi baca normal bagi tiap role.
2. Periksa properti respons dan data export dibandingkan kebutuhan role.
3. Periksa error handling pada input negatif yang diizinkan.
4. Tinjau data pada notification dan log sesuai akses yang diizinkan.
5. Pastikan output tidak menyertakan secret atau data pribadi yang tidak diperlukan.

**Expected behavior**
- API mengembalikan properti minimum yang dibutuhkan.
- Data transaksi dan report dibatasi berdasarkan authorization.
- Pesan error tidak memaparkan detail internal atau secret.
- Data sensitif disunting pada log dan notification sesuai policy.

### RT-LOG-01: Audit event completeness dan correlation

**Tujuan:** menilai apakah aktivitas sensitif dapat ditelusuri dan mendukung investigasi.

**Preconditions**
- Daftar event wajib dan akses ke log uji.
- Satu atau lebih transaksi sintetis.
- Sinkronisasi waktu dan correlation ID.

**Prosedur**
1. Jalankan create, update, approval, submit, status update, serta operasi yang ditolak pada akun uji.
2. Cari event terkait berdasarkan test ID, transaction ID, actor, atau correlation ID.
3. Cocokkan timestamp, actor, object, action, outcome, serta state change.
4. Periksa akses administratif ke log dan perubahan konfigurasi logging jika tercakup.
5. Bandingkan event aplikasi dengan event pada monitoring/SIEM yang tersedia.

**Expected behavior**
- Event penting tercatat dengan field minimum yang diperlukan.
- Waktu dan reference dapat dikorelasikan.
- Log terlindungi dari akses atau perubahan oleh role yang tidak berwenang.
- Secret tidak dicatat dalam event.

### RT-API-01: API inventory, version, dan configuration

**Tujuan:** mengetahui apakah endpoint yang aktif tercatat dan menerapkan policy yang ditetapkan.

**Preconditions**
- Inventory API atau daftar endpoint yang diserahkan pemilik sistem.
- Daftar versi aktif, route, role, dan policy yang berlaku.

**Prosedur**
1. Bandingkan route yang digunakan pada PoC dengan inventory yang disediakan.
2. Periksa authentication, authorization, TLS, schema validation, error handling, dan rate limit sesuai checklist.
3. Evaluasi versi atau route yang dinyatakan deprecated hanya jika ada dalam scope.
4. Periksa apakah endpoint internal memerlukan identity dan policy yang sesuai.
5. Dokumentasikan selisih inventory dan observasi tanpa memindai di luar scope.

**Expected behavior**
- Endpoint aktif memiliki owner, versi, authentication, authorization, dan policy yang sesuai.
- Route yang tidak lagi digunakan dinonaktifkan menurut prosedur perubahan.
- Konfigurasi konsisten pada gateway dan service.

### RT-ABUSE-01: Sensitive business-flow abuse controls

**Tujuan:** memeriksa kontrol penggunaan fungsi sensitif yang bisa menimbulkan beban operasional, biaya, atau transaksi tidak diinginkan jika diotomasi.

**Contoh flow**
- Membuat quote atau transaksi.
- Menambahkan atau mengubah beneficiary.
- Mengirim ulang instruksi.
- Melakukan inquiry status berulang.
- Memicu notifikasi atau verifikasi yang berbiaya.

**Prosedur**
1. Tentukan flow yang sensitif berdasarkan requirement dan model risiko.
2. Minta nilai rate/volume test yang disetujui.
3. Jalankan sedikit request sintetis dalam rentang uji.
4. Periksa throttling, quota, fraud/control signals, dan response saat threshold tercapai.
5. Pastikan limit tidak memblokir operasi normal yang tidak terkait serta event abuse dapat ditelusuri.

**Expected behavior**
- Kontrol beroperasi sesuai rate/volume policy.
- Response saat limit tercapai konsisten dan tidak menimbulkan transaksi parsial.
- Deteksi dan audit event tersedia jika flow dipakai berlebihan.

Jangan menjalankan load test atau meningkatkan request rate di luar nilai tertulis pada RoE.

### RT-CFG-01: Security misconfiguration

**Tujuan:** memeriksa konfigurasi aplikasi, gateway, atau service yang dapat membuka akses atau mengurangi kontrol keamanan.

**Preconditions**
- Daftar host, route, environment, dan komponen dalam scope.
- Baseline konfigurasi/hardening yang disetujui atau acceptance criteria yang jelas.
- Akses yang diperlukan untuk memeriksa respons atau konfigurasi test yang disediakan.

**Prosedur**
1. Bandingkan route dan service yang terlihat dengan inventory serta scope.
2. Periksa secara terbatas apakah debug/test endpoint, directory listing, default page, error trace, atau management route terekspos.
3. Tinjau kebijakan authentication, CORS, security headers, transport security, dan konfigurasi gateway/service yang disediakan.
4. Bandingkan deny policy antar-gateway dan service jika akses tersedia.
5. Jangan mengubah konfigurasi atau mengakses route administrasi tanpa otorisasi khusus.

**Expected behavior:** route tidak digunakan tidak terekspos, debug detail tidak membocorkan data internal, service sensitif membutuhkan kontrol akses, dan policy berlaku konsisten.

**Evidence:** URL/path dalam scope, konfigurasi baseline, request/response tersanitasi, waktu, environment, serta hasil perbandingan.

**FAIL criteria:** konfigurasi aktual melanggar requirement atau mengekspos fungsi/informasi yang tidak boleh diakses.

### RT-SC-01: Software supply-chain controls (conditional)

**Tujuan:** menilai kontrol dependency, build, dan release hanya jika source repository, CI/CD, SBOM, infrastructure-as-code, atau artifact supply chain secara eksplisit masuk scope.

**Preconditions**
- Izin tertulis untuk repository/pipeline/artifact yang ditinjau.
- Read-only access bila memadai, SBOM atau dependency lockfile, serta kebijakan release yang berlaku.
- Reviewer dari pemilik aplikasi atau DevSecOps.

**Prosedur**
1. Cocokkan dependency inventory/SBOM dengan lockfile serta release artifact yang dipakai untuk test environment.
2. Tinjau pinning versi, sumber package, dukungan dependency, dan pemeriksaan dependency yang berjalan sesuai kebijakan.
3. Tinjau kontrol perubahan pipeline, permission service account, approval release, secret exposure, dan integritas artefak yang dapat diperiksa dari bukti yang disediakan.
4. Dokumentasikan kelemahan kontrol atau gap bukti tanpa mengubah source, memasukkan package, atau memicu build/deploy yang tidak diotorisasi.
5. Jika repository/pipeline tidak masuk scope, tetapkan OUT OF SCOPE atau NOT TESTED; jangan menyimpulkan kontrol supply-chain aman.

**Expected behavior:** dependency dan artifact dapat ditelusuri, pipeline memakai prinsip least privilege, release dikendalikan, dan secret tidak terekspos dalam artefak atau log.

**Evidence:** commit/release reference, manifest atau SBOM yang diizinkan, setting relevan, hasil policy check, dan scope reference.

### RT-CRYPTO-01: Cryptographic controls

**Tujuan:** memeriksa kontrol kriptografi yang terkait data remittance, sesi, integrasi, dan secret sesuai scope.

**Preconditions**
- Aset dan jalur data yang diizinkan telah dipetakan.
- Test account dan test data sintetis.
- Baseline atau requirement kriptografi, konfigurasi TLS, serta key/secret handling yang tersedia bagi tim.

**Prosedur**
1. Periksa transport security pada endpoint yang disetujui menggunakan pemeriksaan non-destruktif.
2. Tinjau konfigurasi kriptografi dan alur perlindungan data at rest hanya bila tersedia akses resmi.
3. Periksa apakah secret, token, key material, atau data sensitif masuk ke response/error/log yang dapat diakses tidak berwenang.
4. Tinjau kontrol akses dan proses rotasi/revocation key atau secret dari artefak yang diizinkan.
5. Jangan mencoba memecahkan key, men-decrypt data nasabah, atau mengekspor secret aktual untuk membuktikan dampak.

**Expected behavior:** konfigurasi mengikuti baseline, material kriptografi terlindungi, dan akses data/secret dibatasi sesuai policy.

**Evidence:** konfigurasi tersanitasi, hasil test TLS yang relevan, data sintetis, dan referensi requirement.

### RT-INJ-01: Injection handling

**Tujuan:** memeriksa apakah input pada endpoint yang diizinkan ditangani secara aman ketika diproses oleh query, parser, template, atau interpreter.

**Preconditions**
- Endpoint dan jenis uji tercantum dalam scope.
- Test environment dan data sintetis.
- Payload minimal yang tidak memodifikasi data atau menjalankan tindakan destruktif.

**Prosedur**
1. Tetapkan satu endpoint dan satu input dari alur normal sebagai baseline.
2. Kirim variasi input minimal dan non-destruktif yang sesuai konteks parser/query yang digunakan.
3. Amati perbedaan response, error, timing, dan bukti server-side yang telah disediakan.
4. Bila indikasi ditemukan, gunakan proof minimal untuk konfirmasi tanpa membaca data nyata atau menjalankan command di luar kebutuhan.
5. Hentikan setelah ada bukti yang memadai; jangan melakukan ekstraksi, persistence, atau modifikasi data bisnis.

**Expected behavior:** input divalidasi/di-parameterisasi sesuai konteks, error tidak membocorkan detail sensitif, dan payload tidak memicu operasi yang tidak diotorisasi.

**Evidence:** baseline dan variasi request tersanitasi, actual versus expected behavior, error/correlation ID yang tersedia, serta bukti minimal yang mendukung root cause.

## 5. Evidence register

Setiap test case menghasilkan baris evidence dengan field berikut:

| Field | Deskripsi |
|---|---|
| Engagement ID / Test ID | Identifikasi proyek dan test |
| Actor / role | Akun uji dan role |
| Object / transaction ID | Identifier sintetis |
| Timestamp / timezone | Waktu test |
| Correlation ID | Referensi korelasi lintas-service |
| Request/response | Salinan tersanitasi |
| Before/after state | Perubahan state yang diamati |
| Audit / SIEM reference | Event terkait bila tersedia |
| Test result | PASS / FAIL / INCONCLUSIVE / BLOCKED / NOT TESTED |
| Cleanup status | Complete / Pending / Exception |
| Reviewer | Orang yang mengecek evidence |

## 6. Template perencanaan kasus PoC

Sebelum test dilaksanakan, isi kolom berikut:

| Field | Isi |
|---|---|
| Test ID | |
| Control objective | |
| Requirement reference | |
| In-scope component / endpoint | |
| Test account / role | |
| Synthetic data | |
| Preconditions | |
| Test action | |
| Expected secure behavior | |
| Evidence to capture | |
| Pass / fail criteria | |
| Rate/transaction boundary | |
| Stop condition | |
| Cleanup / rollback | |
| Approver | |

## 7. Keterkaitan dengan standar

Gunakan OWASP Top 10:2025 untuk klasifikasi risiko aplikasi web. Gunakan OWASP API Security Top 10:2023 sebagai referensi pelengkap untuk risiko khusus API. Terapkan ASVS dan WSTG sesuai teknologi, requirement, dan scope engagement.

| Area | Referensi |
|---|---|
| Klasifikasi risiko aplikasi web | [OWASP Top 10:2025](https://top10.owasp.org/2025/) |
| Risiko khusus API | [OWASP API Security Top 10:2023](https://api-security.owasp.org/editions/2023/en/0x11-t10/) |
| Requirement verifikasi aplikasi | [OWASP ASVS](https://asvs.owasp.org/) |
| Pengujian business logic dan workflow | [OWASP Web Security Testing Guide](https://wstg.org/) |
| Pemetaan test case dan kategori | [OWASP Top 10:2025 Mapping](17-owasp-top10-2025-mapping.md) |
| Prioritas temuan untuk latihan internal | [Scoring Temuan Bank Jatim](18-scoring-temuan-bank-jatim.md) |

Catat requirement dan test identifier yang digunakan pada test-case register. Tetapkan acceptance criteria sebelum test dijalankan. Kategori OWASP mengklasifikasikan risiko; kategori tersebut tidak menentukan skor tender.


## OWASP Top 10:2025 alignment

Kolom pada ringkasan menunjukkan kategori utama yang relevan untuk tiap test case. Pemetaan lengkap dan kategori tambahan tercantum pada [OWASP Top 10:2025 Mapping](17-owasp-top10-2025-mapping.md). Kategori tersebut adalah panduan klasifikasi, bukan kesimpulan bahwa kerentanan sudah ditemukan.

OWASP Top 10:2025 tidak menetapkan nilai poin per bug. Untuk penentuan technical severity dan prioritas demo, gunakan evidence aktual dan [model scoring internal](18-scoring-temuan-bank-jatim.md). Model internal harus diganti bila rubric resmi Bank Jatim tersedia.
