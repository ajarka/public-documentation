# AdopTree — Questioner untuk PRD Investor

> Dokumen ini berisi jawaban dari Pak Choky (CKsan) + hasil eksplorasi teknis dari codebase & staging platform.
> Digunakan sebagai bahan melengkapi PRD investor AdopTree.

---

## A. Tim & Founders

**1. Siapa saja co-founder AdopTree dan peran masing-masing?**

> *Jawaban Pak Choky:*
> - **CKsan** — CEO
> - **Aditira** — CTO
> - **BEKs** — C.Media & Design

---

**2. Apakah ada advisor, mentor, atau investor angel yang sudah on-board?**

> *Jawaban Pak Choky:*
> - **Dewan Pembina / Mentor / Advisor**: Ahmad Junaedi, Udoro KA

---

## B. Status & Traction Saat Ini

**3. Apakah platform sudah live / bisa diakses user? Atau masih beta/staging?**

> *Hasil eksplorasi teknis:*
> Platform **sudah live dan dapat diakses** di `staging.adoptreeworld.com`. Semua fitur inti sudah berjalan:
> - Landing page dengan login/register (email, password, Google SSO)
> - Halaman Jelajahi dengan GIS Map, filter Campaigns / Merchants / Critical Sites / Papua / Mangrove
> - Bot Tira (AI chatbot) aktif di semua halaman (bottom-right button)
> - Checkout dengan multiple payment method
> - My Forest (dashboard donatur)
> - Forum
>
> Status: **Staging production** — deployable ke domain utama. Target public launch H2 2026.

---

**4. Berapa merchant / NGO yang sudah on-board atau punya LOI (Letter of Intent)?**

> *Hasil query DB staging (data per April 2026):*
>
> | Merchant | Lahan | Pohon | Keterangan |
> |----------|-------|-------|------------|
> | Papua Conservation Foundation | 6 | 750 | Sample / demo data |
> | Ajarka | 4 | 3 | Internal test account |
> | **Akademi Buah Nusantara** | **1** | **2** | ✅ **Merchant REAL pertama** |
> | Rawi | 1 | 0 | Sample data |
>
> **Merchant real yang sudah aktif: 1 (Akademi Buah Nusantara).**
> Sisanya adalah sample data untuk keperluan demo & testing platform.

---

**5. Sudah ada transaksi adopsi pohon yang berhasil? Kalau ada, berapa total?**

> *Hasil query DB staging (data per April 2026):*
> - Total adoptions di DB: **35**
> - Active adoptions: **14**
> - Total lands: **12** | Total trees terdaftar: **755**
>
> ⚠️ Catatan: Sebagian besar adalah **data sample/demo**. Transaksi real melalui payment gateway (Midtrans/Solana) perlu dikonfirmasi dari dashboard payment atau tim bisnis.

> *📝 Draft narasi untuk investor:*
> *"Platform telah memiliki 35 adoption records di staging (termasuk data demo). Transaksi berbayar
> pertama melalui Midtrans akan diaktifkan pada public launch H2 2026. Payment gateway Midtrans
> sudah live dan terintegrasi penuh — termasuk bank transfer, e-wallet, QRIS, dan Solana SOL."*

```
Konfirmasi tim (transaksi real yang sudah lewat Midtrans jika ada): ___
```

---

**6. Berapa registered user saat ini?**

> *Hasil query DB staging (data per April 2026):*
> - Total registered users: **16**
>
> ⚠️ Catatan: Ini adalah data staging yang sebagian besar akun test/internal.
> Jumlah real user akan berkembang setelah public launch.

---

## C. Legal & Compliance

**7. Status badan hukum AdopTree saat ini?**
> *(PT sudah terdaftar? Masih proses? Yayasan?)*

> *📝 Draft (konfirmasi ke Pak Choky):*
> Disarankan berbentuk **PT (Perseroan Terbatas)** agar bankable di mata investor dan bisa menerima equity investment secara legal.
> Jika belum terdaftar: proses pendirian PT perlu diselesaikan sebelum atau paralel dengan fundraising.
> Jika sudah: cantumkan nama PT, tahun berdiri, dan domisili.

```
Konfirmasi tim: [ ] Sudah PT — nama: _______ | [ ] Masih proses | [ ] Bentuk lain: _______
```

---

**8. Untuk fitur Wakaf — apakah sudah ada sertifikasi atau endorsement dari lembaga resmi (MUI, BWI, dll)?**

> *Catatan teknis:* Tier Wakaf sudah diimplementasikan di platform (perpetual ownership, Shariah-compliant label).

> *📝 Draft (konfirmasi ke Pak Choky):*
> Untuk tahap awal, endorsement formal dari MUI atau BWI (Badan Wakaf Indonesia) belum wajib —
> tapi **sangat direkomendasikan** sebagai trust signal ke donor Muslim dan investor syariah.
> Sementara belum ada sertifikasi, bisa dicantumkan: *"Fitur Wakaf dirancang sesuai prinsip syariah;
> proses endorsement MUI/BWI sedang dijajaki sebagai bagian dari roadmap compliance Q3 2026."*

```
Konfirmasi tim: [ ] Sudah ada endorsement — dari: _______ | [ ] Sedang proses | [ ] Belum, targetkan: _______
```

---

**9. Carbon credit framework — apakah sudah ada standar yang diikuti (VCS, Gold Standard, dll)?**

> *Hasil eksplorasi teknis:*
> Carbon credit sudah **diimplementasikan secara teknis** — kalkulasi real-time berbasis CO2 absorption
> per spesies pohon (kg/year), dilacak per adopsi, dan ditampilkan di dashboard user.
> Platform bisa menghitung total CO2 tersekap dalam kg dan ton secara live.

> *📝 Draft (konfirmasi ke Pak Choky):*
> Untuk investor, standar yang paling dikenal dan bankable adalah:
> - **VCS (Verified Carbon Standard / Verra)** — paling umum untuk reforestasi
> - **Gold Standard** — lebih prestisius, sering diminta untuk ESG reporting korporasi
>
> Rekomendasi narasi: *"Kalkulasi karbon menggunakan metodologi berbasis IPCC tier 2
> (CO₂ absorption per spesies). Sertifikasi VCS ditargetkan pada 2027 seiring pertumbuhan
> luas lahan dan volume pohon yang terverifikasi."*
> Redemption & trading kredit karbon masuk roadmap setelah sertifikasi selesai.

```
Konfirmasi tim: [ ] Sudah pakai standar: _______ | [ ] Target standar: VCS / Gold Standard | [ ] Lainnya: _______
```

---

## D. Investment Ask

**10. Berapa total funding yang dicari di round ini?**

> *📝 Draft — Rekomendasi berdasarkan tahap platform & proyeksi PRD:*
>
> Berdasarkan benchmarking startup greentech/agritech seed round di Indonesia & SEA (2024–2025),
> dan melihat status AdopTree (working product, staging live, 1 merchant real, target launch H2 2026):
>
> **Rekomendasi: USD $300,000 – $500,000 (Seed Round)**
>
> - **Minimum (soft close)**: $300,000
> - **Target (full round)**: $500,000
> - **Hard cap**: $750,000 (jika ada over-subscribe)
>
> Angka ini cukup untuk 18 bulan runway menuju break-even (Q3 2027 per proyeksi PRD),
> dan tidak terlalu besar sehingga valuasi tidak terlalu dilute di tahap awal.

```
Konfirmasi Pak Choky: ___
```

---

**11. Equity yang ditawarkan ke investor?**

> *📝 Draft — Rekomendasi:*
>
> Untuk seed round di Indonesia pada valuasi awal (pre-revenue, working product):
>
> **Rekomendasi: 15–20% equity untuk keseluruhan round**
>
> - Jika raise $300K → valuasi pre-money: ~$1.5M (20%) atau ~$2M (15%)
> - Jika raise $500K → valuasi pre-money: ~$2.5M (20%) atau ~$3.3M (15%)
>
> Narasi ke investor: *"Kami menawarkan 15% equity di valuasi pre-money $3.3M,
> didukung working product dengan infrastruktur tech yang sudah production-ready."*

```
Konfirmasi Pak Choky: ___
```

---

**12. Apakah ada minimum check size per investor?**

> *📝 Draft — Rekomendasi:*
>
> **Minimum check size: $25,000 per investor**
>
> Ini umum untuk seed round di Indonesia — cukup besar agar investor serius,
> cukup kecil untuk membuka peluang ke beberapa angel investor sekaligus
> (diversified cap table lebih baik untuk tahap awal).

```
Konfirmasi Pak Choky: ___
```

---

**13. Alokasi penggunaan dana (perkiraan persentase)?**

> *📝 Draft — Rekomendasi alokasi untuk $500K raise:*
>
> | Kategori | % | Estimasi USD | Keterangan |
> |----------|---|-------------|------------|
> | Sales & Business Development | 35% | $175,000 | Akuisisi merchant, BD corporate CSR, partnership |
> | Product & Technology | 25% | $125,000 | Solana NFT minting live, surveillance system, mobile app |
> | Operations & Tim | 25% | $125,000 | Gaji tim, infrastruktur server, tool |
> | Legal & Compliance | 10% | $50,000 | Pendirian PT, sertifikasi Wakaf, VCS carbon standard |
> | Marketing & Community | 5% | $25,000 | Brand awareness, social media, komunitas donatur |
>
> Prioritas utama penggunaan dana: **merchant acquisition** — karena revenue model bergantung
> pada jumlah merchant yang on-board (platform fee per adopsi).

```
Konfirmasi Pak Choky: ___
```

---

**14. Target runway dari dana ini berapa bulan?**

> *📝 Draft — Rekomendasi:*
>
> **Target runway: 18 bulan (hingga ~Q4 2027)**
>
> Ini selaras dengan proyeksi PRD:
> - H2 2026: Launch production → $23K revenue
> - 2027: Scale → $498K revenue
> - Break-even diproyeksikan Q3 2027
>
> Dengan runway 18 bulan, dana cukup untuk mencapai break-even sebelum perlu raise lagi.
> Jika pertumbuhan lebih cepat, bisa raise Series A di Q2–Q3 2027 dari posisi yang kuat.

---

## E. Partnerships & Pipeline

**15. Apakah sudah ada kerjasama aktif dengan pemilik lahan / merchant tertentu?**

> *Hasil eksplorasi teknis + DB staging:*
> Fitur land partnership sudah live. Dari data DB:
>
> **✅ Merchant real yang aktif: Akademi Buah Nusantara** — 1 lahan, 2 pohon terdaftar.
> Ini adalah proof-of-concept merchant pertama yang menunjukkan platform dapat dioperasikan end-to-end.

> *📝 Draft narasi untuk investor:*
> *"AdopTree telah memiliki merchant pertama yang aktif (Akademi Buah Nusantara) dan sedang dalam
> penjajakan dengan beberapa NGO serta pemilik lahan di Jawa dan Papua. Pipeline merchant aktif
> menjadi prioritas utama penggunaan dana fundraising ini."*

```
Konfirmasi Pak Choky (nama merchant/NGO lain yang sedang dijajaki): ___
```

---

**16. Apakah ada dukungan atau MOU dengan instansi pemerintah (KLHK, Dinas Lingkungan, dll)?**

> *📝 Draft narasi untuk investor:*
> Untuk tahap awal (pre-MOU), ini adalah narasi yang wajar dan jujur:
> *"AdopTree beroperasi dalam ekosistem reforestasi yang sejalan dengan program nasional
> FOLU Net Sink 2030 (KLHK). Kami sedang menjajaki keterlibatan formal dengan dinas terkait
> sebagai bagian dari roadmap compliance dan legitimasi platform di 2026."*
>
> Jika ada koneksi ke KLHK, Dinas Kehutanan, atau BRIN yang sudah ada — cantumkan di sini
> karena ini akan sangat menaikkan kredibilitas di mata investor.

```
Konfirmasi Pak Choky: [ ] Sudah ada MOU/dukungan — dari: _______ | [ ] Sedang penjajakan | [ ] Belum ada
```

---

**17. Ada corporate / perusahaan yang sudah tertarik untuk CSR bulk adoption?**

> *Catatan teknis:* Fitur Corporate CSR sudah fully implemented — modul khusus dengan role
> CorporateOwner / Admin / Viewer, dashboard korporasi, dan sistem undangan tim.

> *📝 Draft narasi untuk investor:*
> Modul Corporate CSR sudah production-ready. Untuk memperkuat bagian ini di depan investor:
> - Jika ada perusahaan yang sudah pernah dihubungi / express interest → sebutkan (tanpa perlu MOU formal)
> - Narasi: *"AdopTree memiliki modul CSR siap pakai untuk perusahaan dengan kebutuhan ESG reporting.
>   Pipeline awal mencakup [nama industri/perusahaan jika ada], dengan target 2 corporate partner
>   di H2 2026."*

```
Konfirmasi Pak Choky (ada nama perusahaan yang sudah tertarik?): ___
```

---

## F. Teknologi

**18. Fitur NFT + Solana — sudah live atau masih di roadmap?**

> *Hasil eksplorasi teknis (dari codebase):*
>
> **Sudah diimplementasikan:**
> - Backend service NFT lengkap: metadata storage, transaction history, owner tracking
> - Model DB: `nft_metadata`, `nft_transactions`, `wallet_nonce`
> - Wallet authentication via Solana (wallet connect + nonce signing)
> - Pembayaran menggunakan **SOL (Solana)** tersedia di checkout sebagai metode pembayaran
> - Frontend: komponen `solana-payment.tsx` aktif di halaman checkout
>
> **Masih roadmap (belum live):**
> - On-chain **minting** NFT di Solana blockchain — kode ada tapi fungsinya masih stub
>   (komentar di kode: *"In a real implementation, we would: 1. Fetch on-chain account data, 2. Fetch metadata from URI..."*)
> - Data layer dan API endpoint NFT siap; tinggal integrasi actual minting ke Solana mainnet/devnet
>
> **Ringkasan**: Infrastruktur NFT = ready. Pembayaran SOL = live. Actual minting on-chain = roadmap Q3 2026.

---

**19. GIS monitoring + foto pohon — sudah bisa diakses user atau masih tahap awal?**

> *Hasil eksplorasi teknis (dari staging + codebase):*
>
> **Sudah live:**
> - **Interactive GIS Map** berbasis Mapbox GL dengan satellite imagery — live di halaman `/explore` dan `/map`
> - Setiap pohon memiliki koordinat GIS (latitude/longitude) — ditampilkan sebagai dots di peta
> - Polygon lahan divisualisasikan di peta
> - **My Forest** — donatur bisa melihat pohon-pohon yang mereka adopsi di peta pribadi
> - **Tree health tracking** — model `tree_health` ada di DB, termasuk foto, kondisi, dan surveillance log
> - Foto pohon bisa diupload dan ditampilkan per pohon
>
> **Dalam pengembangan:**
> - Periodic surveillance & laporan berkala (fitur Gold/Platinum tier) — infrastruktur ada, jadwal rutin belum diaktifkan
> - 360° view pohon (disebutkan di tier AdopTree premium)

---

## G. Differensiasi & Kompetisi

**20. Menurut kamu, apa 1–2 hal yang paling membuat AdopTree berbeda dari platform sejenis?**

> *Jawaban Pak Choky — Visi & Misi AdopTree:*
>
> a. AdopTree adalah platform terpercaya dalam ekosistem gerakan go green internasional sebagai pusat daur ulang karbon dunia dalam reforestasi dan pertanian.
>
> b. AdopTree adalah **multiverse platform** — tempat bertemunya donor (individu/korporasi/pemerintah/NGO/koperasi) dengan *"tree/green/soil eco caretaker"* melalui Social Media, Market Place, dan Green Forum.
>
> c. Platform yang **terpercaya dan transparan**, mendekatkan donor ke lahan dengan monitoring dan periodic tracking/surveillance updates.
>
> d. **Monitoring platform untuk donasi** — memastikan donasi benar-benar sampai dan terpakai di tempat yang tepat.
>
> e. **Crowd Funding umbrella platform** untuk semua entitas legal (GO/NGO/individual/korporasi/koperasi) dengan lahan dan komponen yang LEGAL.

---

## H. Target & Timeline

**21. Target production launch ke publik: kapan?**

> *Jawaban Pak Choky:*
> **2026**

---

**22. Target pasar pertama: Indonesia saja, atau ada rencana ekspansi ke negara lain?**

> *Jawaban Pak Choky:*
> **International** — platform sudah dirancang multi-bahasa (Indonesia, English, Arabic) dan multi-currency.

---

**23. Ada hal lain yang penting untuk investor tahu yang belum tercakup di atas?**

> *Jawaban Pak Choky — Business Projection, Milestone, Target & Strategy:*
>
> - **3-year commercial target**: 100K Ha land occupancy
>   ~ 5.000 pohon/Ha × 100.000 Ha = **500.000.000 pohon** (reserved/under management)
> - **Strategy**: Networks, Donations, and CSR

---

## Ringkasan Teknis Platform (dari Codebase & Staging)

| Fitur | Status |
|-------|--------|
| Landing page & auth (email + Google SSO) | ✅ Live |
| Jelajahi lahan (GIS map + filter) | ✅ Live |
| Adopsi pohon + checkout | ✅ Live |
| Payment: Midtrans (bank, e-wallet, QRIS, kartu kredit) | ✅ Live |
| Payment: Solana (SOL crypto) | ✅ Live |
| My Forest dashboard (donatur) | ✅ Live |
| Bot Tira (AI chatbot — Gemini powered) | ✅ Live |
| Forum komunitas | ✅ Live |
| Merchant dashboard + manajemen lahan | ✅ Live |
| Land owner portal + partnership invite | ✅ Live |
| Corporate CSR module | ✅ Live |
| Carbon credit tracking & kalkulasi | ✅ Live |
| GIS tree tracking & peta adopsi | ✅ Live |
| Tree health monitoring | ✅ Live (infrastruktur) |
| Admin panel | ✅ Live |
| Service tier (Donasi/Wakaf × Silver/Gold/Platinum) | ✅ Live |
| NFT data layer & wallet auth | ✅ Live |
| On-chain Solana NFT minting | 🔄 Roadmap Q3 2026 |
| Carbon credit redemption/trading | 🔄 Roadmap |
| Periodic surveillance reports (Gold/Platinum) | 🔄 Roadmap |

---

## Draft Pesan untuk Konfirmasi ke Pak Choky (CKsan)

> Kirim ini via chat sebelum meeting investor Rabu. Cukup jawab singkat — angka & keputusan finalnya.

---

Bro, mau finalisasi PRD investor sebelum meeting. Ada beberapa poin yang butuh konfirmasi kamu — jawab singkat aja ya:

**Legal:**
1. AdopTree sudah berbentuk PT belum? Kalau sudah, nama PTnya apa?
2. Untuk fitur Wakaf — sudah ada endorsement dari MUI/BWI atau masih planned?

**Investment Ask** *(ini yang paling penting untuk investor deck):*

3. Berapa funding yang mau di-raise? Draft gue: **USD $300K–$500K** — setuju atau ada angka lain?
4. Equity yang ditawarkan? Draft gue: **15–20%** dari total round
5. Ada minimum check size per investor? Draft gue: **$25,000**
6. Alokasi dana (draft gue): 35% BD/Sales, 25% Tech, 25% Ops, 10% Legal, 5% Marketing — OK?

**Partnerships:**

7. Selain Akademi Buah Nusantara, ada NGO/merchant lain yang lagi dijajaki?
8. Ada koneksi ke KLHK atau dinas pemerintah yang bisa disebutkan?
9. Ada perusahaan yang sudah nunjukin interest buat CSR adoption?

Kalau semua confirmed, PRD investor langsung final dan siap dipresentasikan! 🌳

---

_Dokumen ini dikompilasi dari: jawaban Pak Choky (CKsan) + eksplorasi staging `staging.adoptreeworld.com` + analisis codebase AdopTree World._
_Semua bagian bertanda `📝 Draft` adalah estimasi/rekomendasi yang perlu dikonfirmasi oleh tim founder sebelum PRD dianggap final._
