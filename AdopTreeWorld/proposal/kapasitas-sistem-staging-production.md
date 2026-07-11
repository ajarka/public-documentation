# Proposal Kebutuhan & Kapasitas Infrastruktur Sistem AdopTree (Staging + Production)

> 📄 **Untuk pengambilan keputusan cepat, baca versi 1 halaman**: [ringkasan-pengajuan.md](ringkasan-pengajuan.md) — dokumen ini adalah rujukan teknis lengkapnya.
> 📊 **Model perhitungan interaktif (Excel, formula hidup)**: [perhitungan-kapasitas-anggaran.xlsx](perhitungan-kapasitas-anggaran.xlsx) — ubah asumsi di sheet Asumsi, seluruh angka menghitung ulang.
>
> **Status**: Proposal untuk diajukan & disetujui stakeholder
> **Tanggal**: 11 Juli 2026 · **Disusun oleh**: Aditira Jamhuri (CTO)
> **Tujuan dokumen**: menjawab *"berapa sebenarnya kebutuhan sistem AdopTree untuk staging & production, dan berapa biayanya"* — dengan perhitungan yang dapat diaudit baris per baris. Setiap angka punya sumber: **terukur dari aplikasi**, **acuan praktik industri yang dinyatakan**, atau **asumsi eksplisit** (dan bisa diganti tanpa merusak formula).
> **Prinsip sizing**: *right-sizing* — tidak kekurangan (headroom (ruang cadangan kapasitas) ≥ 5× beban puncak proyeksi + jalur scale-up < 1 hari), tidak berlebihan (utilisasi steady target 15–40%; upgrade dipicu **metrik**, bukan kalender).

---

## Ringkasan Eksekutif — Yang Diajukan

Platform AdopTree World sudah selesai dibangun dan teruji end-to-end (web + Android Field App Build 32 + payment Midtrans live), namun **belum memiliki server dedicated untuk beroperasi** — baik untuk **staging** (environment review internal & UAT stakeholder sebelum rilis) maupun **production** (melayani donor, merchant, dan kontributor lapangan sungguhan). Proposal ini mengajukan keduanya, dengan kebutuhan yang diturunkan dari data terukur aplikasi dan target bisnis PRD.

### Tiga keputusan yang diminta

1. **Setujui provisioning 2 (dua) VPS dedicated** pada **Agustus 2026** — sebelum first paying merchant (target Q3 2026) dan public launch H2 2026:
   - **Staging** — 2 vCPU / 4 GB: environment review internal + UAT, auto-deploy dari branch `development` via CI/CD
   - **Production** — 4 vCPU / 8 GB, data center Indonesia, terisolasi penuh dari environment uji
2. **Setujui plafon anggaran infrastruktur 12 bulan pertama Rp 50 juta** (Agu 2026 – Jul 2027, staging + production + **seluruh langganan software** — Mapbox, email, AI, monitoring, CDN, storage, registry, domain — sudah termasuk buffer 10%). Realisasi proyeksi **≈ Rp 40 juta** (upgrade tier mengikuti trigger §6, perkiraan awal 2027); plafon menampung **skenario upgrade dini** — bila lonjakan pasca-public-launch memicu trigger sejak Nov 2026, kebutuhan ≈ Rp 47,5 juta — sehingga momentum launch tidak pernah menunggu persetujuan ulang. Inventaris lengkap di §8.1, rincian di §8.3.
3. **Setujui prinsip upgrade berbasis trigger metrik** (§6): kapasitas dinaikkan saat ambang terukur terlampaui, bukan karena jadwal — ini yang menjaga anggaran tetap terkendali sekaligus kapasitas tidak pernah kekurangan.

### Anggaran yang diajukan (semua layanan, per bulan)

| Tahap | Periode | Infrastruktur | Biaya/bulan (all-in) |
| --- | --- | --- | --- |
| **Launch** (≤ 2.000 akun) | Agu–Des 2026 | Staging 2 vCPU/4 GB + Production 4 vCPU/8 GB + seluruh software langganan | **Rp 0,6–1,7 jt** (realistis ~Rp 1 jt) |
| **Growth Y1** (hingga 25 rb akun) | 2027 | Staging + app node 8 vCPU/16 GB + DB node terpisah + seluruh software langganan | **Rp 2,8–7,5 jt** (realistis ~Rp 4,5 jt) |
| **Scale Y2** (hingga 120 rb akun) | 2028 | Staging + HA penuh: 2× app + LB + managed Postgres HA + replica + node ML + software | **Rp 10–21 jt** (realistis ~Rp 17 jt) |

**Uji kewajaran anggaran**: PRD §14.4 mengalokasikan biaya infrastruktur **$3.600 (≈ Rp 57 jt) untuk 2026** dan **$12.000 (≈ Rp 190 jt) untuk 2027**. Plafon 12 bulan pertama (Rp 50 jt) ≈ **26% alokasi 2027**; realisasi proyeksi (≈ Rp 40 jt) ≈ **21%** — proposal ini **bukan** batas atas budget, melainkan kebutuhan riil terhitung plus ruang skenario upgrade dini; sisa alokasi tetap tersedia tanpa perlu diajukan sekarang.

**Cakupan**: proposal ini murni **biaya infrastruktur & layanan pendukungnya** (server, CDN, storage, peta, email, AI, monitoring). Sengaja **di luar cakupan**: (a) **biaya payment gateway Midtrans** (per transaksi ±2,9% — sudah menjadi pos tersendiri di PRD §14.4, mengikuti volume penjualan, bukan langganan); (b) **biaya program Tukar Poin** (pembelian pulsa/kuota untuk redemption kontributor — biaya program insentif, bukan infrastruktur); (c) **Solana RPC berbayar** untuk pipeline NFT mint — diajukan terpisah bersama sprint mint Q3 2026 (saat ini memakai RPC publik, Rp 0); (d) SDM ops/engineering — pos tersendiri di PRD §14.4. Server fisik (on-premise/colocation) dievaluasi dan **tidak diperlukan** — lihat §5.2 dan catatan backup §4.1.

---

## Rekapitulasi Kebutuhan — Tabel Satu Pandang

Seluruh kebutuhan yang diajukan, dalam satu tabel. Kolom fase menunjukkan biaya per bulan; rincian dan dasar perhitungan setiap baris ada di bagian yang dirujuk.

| Kategori | # | Kebutuhan | Spesifikasi / Paket | Launch (Agu–Des 2026) | Growth Y1 (2027) | Scale Y2 (2028) | Rincian |
| --- | --- | --- | --- | --- | --- | --- | --- |
| **Server** | 1 | VPS Staging dedicated — review internal & UAT + CI/CD | 2 vCPU / 4 GB / 80 GB NVMe | Rp 150–350 rb | Rp 150–350 rb | Rp 250–500 rb | §5.1 |
| **Server** | 2 | VPS Production | Launch: 4 vCPU / 8 GB / 160 GB NVMe (Indonesia) → Y1: app 8 vCPU/16 GB + DB node 4 vCPU/8 GB → Y2: HA (2× app + LB + managed Postgres + replica + node ML) | Rp 400–800 rb | Rp 1,3–2,6 jt | Rp 5–10 jt | §5.2–5.4 |
| **Software** | 3 | Cloudflare — CDN, WAF, Tunnel, DNS | Free → Pro ($20/bln) | Rp 0 | ~Rp 330 rb | Rp 330 rb–1 jt | §8.1 |
| **Software** | 4 | Cloudflare R2 — SELURUH konten media (foto inspeksi, **video**, photo sphere 360°, galeri, sertifikat, APK, backup DB) | $0,015/GB/bln, egress gratis | < Rp 25 rb | ~Rp 75 rb | ~Rp 350 rb | §4.1, §8.1 |
| **Software** | 5 | Mapbox — peta GIS web + Static API (watermark mobile) + geocoding | Free tier besar → ~$5/1.000 loads | Rp 0 | Rp 0–800 rb | Rp 1–5 jt (dgn mitigasi §8.1) | §8.1 |
| **Software** | 6 | Tira AI (OpenAI/Gemini, multi-provider) | Usage-based, **cap harian fail-closed built-in** | Rp 100–300 rb | Rp 0,5–2 jt | Rp 2–6 jt | §8.1 |
| **Software** | 7 | Email transaksional — Resend → AWS SES saat volume tinggi | Free 3 rb → Pro $20 → Scale $90 → SES $0,10/1.000 | Rp 0 | Rp 330 rb–1,5 jt | Rp 0,8–2 jt | §8.1 |
| **Software** | 8 | Error monitoring (Sentry) + uptime monitor | Free → Team ~$26 (+ ~$25 Y2) | Rp 0 | ~Rp 420 rb | ~Rp 0,8–1,3 jt | §8.1 |
| **Software** | 9 | Push notifikasi — FCM (mobile) + Web Push VAPID (browser) | Gratis (FCM) / standar terbuka (VAPID) | Rp 0 | Rp 0 | Rp 0 | §8.1 |
| **Software** | 10 | Domain `adoptreeworld.com` | ~Rp 300 rb/tahun | ~Rp 25 rb | ~Rp 25 rb | ~Rp 25 rb | §8.1 |
| **Software** | 11 | Jenkins (CI/CD) · GitHub · Google OAuth · Instagram Graph API · Meilisearch · Redis · PostgreSQL+PostGIS · ML Tree Service (self-host) · citra Sentinel-2 (Copernicus) · seluruh library open-source | Self-host / free plan | Rp 0 | Rp 0 | Rp 0 | §8.1 |
| **TOTAL** | | **Per bulan (rentang penuh)** | | **Rp 0,6–1,7 jt** | **Rp 2,8–7,5 jt** | **Rp 10–21 jt** | §8.2 |
| **TOTAL** | | **Per bulan (titik tengah realistis)** | | **~Rp 1,0 jt** | **~Rp 4,5 jt** | **~Rp 17 jt** | §8.2 |

| Ringkasan akhir | Nilai |
| --- | --- |
| Biaya one-time (setup, hardening, uji restore, uji beban) | **Rp 0** — jam kerja internal, tanpa lisensi |
| **Plafon 12 bulan pertama** (Agu 2026 – Jul 2027, termasuk buffer 10%) | **Rp 50 juta** — realisasi proyeksi ≈ Rp 40 jt; skenario upgrade dini ≈ Rp 47,5 jt (§8.3) |
| Proyeksi tahun 2028 (skala penuh) | ≈ Rp 120–250 jt/tahun (realistis ~Rp 200 jt) |
| Perbandingan terhadap alokasi budget PRD §14.4 | Plafon ≈ 26% alokasi 2027 ($12.000); realisasi proyeksi ≈ 21%; proyeksi 2028 < 40% alokasi 2028 ($36.000) |

---

## 1. Metodologi — Bagaimana Angka Dihitung

Empat langkah, masing-masing bisa diverifikasi:

| Langkah | Metode | Bagian |
| --- | --- | --- |
| 1. Ukur baseline | Footprint riil aplikasi (RAM/CPU/DB/disk) diukur langsung dari environment berjalan — bukan estimasi | §2 |
| 2. Modelkan beban | Formula eksplisit dari target bisnis PRD §14.2; setiap asumsi dinyatakan | §3 |
| 3. Petakan kapasitas komponen | Acuan praktik industri yang konservatif; uji beban k6 terhadap server production dijadwalkan sebagai **gate go-live** (§9) | §3.3 |
| 4. Sizing + headroom | Spesifikasi = beban proyeksi × faktor aman; ditolak jika utilisasi proyeksi < 10% (kapasitas berlebih) atau > 50% steady (kekurangan headroom) | §5 |

**Dua penjaga kewajaran** yang dipakai di seluruh dokumen:

- **Jaminan kecukupan kapasitas**: setiap tier harus (a) menampung ≥ 5× beban puncak proyeksi tahapnya, (b) punya jalur scale-up < 1 hari kerja, dan (c) memenuhi target RTO/RPO (§7.2) — karena platform ini memegang **dana donor dalam escrow bertahap** dan **data pribadi sensitif** (NIK 16 digit + foto KTP pengelola lahan, data donor).
- **Jaminan efisiensi biaya**: setiap spesifikasi dibandingkan dengan (a) harga publik ≥ 3 provider (§5.5), (b) opsi satu tingkat lebih hemat — dan bila opsi tersebut tidak dipilih, alasannya ditulis (§5.1–5.2), (c) utilisasi proyeksi dicantumkan supaya kelebihan kapasitas terlihat.

---

## 2. Baseline Terukur (11 Juli 2026)

### 2.1 Footprint aplikasi — diukur langsung, bukan estimasi

Angka dari environment uji internal (`docker stats`, `psql`, `df`):

| Komponen | RAM terpakai | Interpretasi |
| --- | --- | --- |
| Frontend Next.js | ~105 MiB | Ringan |
| Backend Rust/Axum | **~46 MiB** | Sangat ringan — karakteristik Rust (PRD §10.2) |
| PostgreSQL + PostGIS | ~163 MiB | Termasuk ekstensi geospasial |
| Redis | ~18 MiB | Cache + sesi |
| Meilisearch | ~67 MiB | Full-text search |
| nginx + cloudflared | ~95 MiB | Reverse proxy + tunnel |
| **Total stack aplikasi** | **≈ 0,5 GB** | |
| Jenkins CI/CD (self-host) | ~0,8 GB | Ditempatkan di staging, bukan production |
| CPU seluruh stack | ~0–4% saat idle | Beban CPU hanya muncul mengikuti trafik |

**Interpretasi**: total footprint stack aplikasi ≈ 0,5 GB RAM — platform ini sangat efisien (backend Rust 46 MiB). Kebutuhan server ditentukan oleh **headroom untuk lonjakan trafik, database geospasial, layanan ML estimasi pohon, dan keamanan data** — bukan oleh aplikasinya sendiri.

### 2.2 Database & storage — terukur

| Item | Nilai terukur | Catatan |
| --- | --- | --- |
| Ukuran database | **35 MB** (data uji) | Termasuk ~13 MB baseline tetap PostGIS + batas provinsi Kepmendagri |
| Isi data uji | 28 akun · 777 pohon · 238 adopsi | Merchant demonstrasi end-to-end |
| Disk terpakai server uji | 20 GB dari 99 GB (21%) | Termasuk image Docker ~7 GB |
| Media (foto inspeksi, sertifikat, APK) | Di object storage (R2), bukan di disk server | APK Field App ±117 MB per build — egress R2 gratis |

### 2.3 Status environment: belum ada server dedicated

| Environment | Fungsi | Status hari ini |
| --- | --- | --- |
| **Staging** | Review internal stakeholder, UAT fitur sebelum rilis, tempat uji beban — auto-deploy setiap push ke branch `development` | 📋 **Diajukan** — belum ada server dedicated |
| **Production** | Melayani donor, merchant/NGO, korporasi CSR, dan kontributor lapangan sungguhan di `adoptreeworld.com` | 📋 **Diajukan** — belum pernah di-provision |

Yang **sudah siap** dan hanya perlu diarahkan (tanpa biaya tambahan): pipeline CI/CD Jenkins (staging auto-deploy; production dengan approval gate), domain `adoptreeworld.com` + subdomain staging (Cloudflare Tunnel), image Docker FE/BE yang sudah teruji, dan distribusi APK via R2 (`/download/releases`, 32 build terrilis).

### 2.4 Bukti kesiapan dari pemakaian nyata

| Bukti | Hasil | Artinya untuk sizing |
| --- | --- | --- |
| 35+ fase produk dirilis Apr–Jul 2026 via pipeline yang sama | Deploy harian tanpa insiden kapasitas | Pola operasional (build → deploy → verifikasi) sudah teruji; environment baru tinggal diarahkan |
| 32 build APK Field App terdistribusi via R2 | Unduhan APK (±117 MB/build) tidak membebani server | Egress R2 gratis — lonjakan unduhan tidak menimbulkan biaya tak terduga |
| Payment Midtrans + escrow ledger berjalan end-to-end di environment uji | Alur uang teruji | Kebutuhan production adalah **isolasi + disiplin backup**, bukan kapasitas komputasi besar |
| Uji beban k6 formal | 📋 Dijadwalkan sebagai **gate go-live** terhadap server production (§9) | Spesifikasi §5 memakai acuan industri konservatif; hasil k6 menjadi validasi silang sebelum menerima trafik nyata |

---

## 3. Model Beban — Asumsi Eksplisit

### 3.1 Formula

```
DAU               = 10% × akun terdaftar           (asumsi engagement platform komunitas)
Concurrent peak   = 10% × DAU                      (jam sibuk malam/akhir pekan)
Request/user      = ~6 request/menit saat aktif    (SPA: navigasi + API) = 0,1 RPS/user
RPS nominal       = concurrent × 0,1
RPS target sizing = RPS nominal × faktor lonjakan 10×   (kampanye viral, liputan media, program CSR serentak)
```

> Setiap koefisien bisa diperdebatkan — yang penting **dinyatakan**. Bila realita menunjukkan engagement 2× lebih tinggi, formula cukup dijalankan ulang; §7.1 menunjukkan bahkan meleset 2× pun tidak mengubah spesifikasi tier.

### 3.2 Hasil per tahap (anchor volume = target bisnis PRD §14.2)

| Tahap | Akun terdaftar | DAU | Concurrent peak | RPS nominal | **RPS target sizing (×10)** |
| --- | --- | --- | --- | --- | --- |
| **Launch** (H2 2026) | 2.000 | 200 | 20 | 2 | **~20–50** |
| **Growth Y1** (2027) | 25.000 | 2.500 | 250 | 25 | **~250** |
| **Scale Y2** (2028) | 120.000 | 12.000 | 1.200 | 120 | **~1.200** |

Beban khas AdopTree yang ikut diperhitungkan: sinkronisasi batch Field App dari lapangan (idempotent, dapat di-buffer — bukan beban puncak), WebSocket chat & telemetri GPS kontributor (koneksi ringan berumur panjang), dan inferensi ML estimasi pohon (berjalan on-demand, bukan per-request publik).

### 3.3 Kapasitas komponen (acuan praktik industri — konservatif)

| Komponen | Kapasitas aman per unit | Yang membatasi |
| --- | --- | --- |
| **Rust/Axum BE** | ≥ 2.000 RPS/core (JSON + query ringan) — karakteristik lebih efisien dari Go/Node (PRD §10.2) | CPU — praktis bukan bottleneck sampai Y2 |
| **Next.js SSR** | 50–150 RPS/core halaman SSR | Komponen terboros CPU → mitigasi: **CDN Cloudflare men-cache halaman publik** (asumsi konservatif 70% offload) |
| **PostgreSQL 16 + PostGIS** (4 vCPU NVMe) | 500–1.000 tx tulis/s; puluhan ribu read/s | Pool koneksi + IOPS; query geospasial berat (polygon overlap) hanya di alur registrasi lahan — jarang |
| **Redis 7** | 50.000+ ops/s | Bukan bottleneck di skala manapun rencana ini |
| **Meilisearch** | Ribuan query/s pada indeks ≤ ratusan ribu dokumen | RAM indeks — masih kecil (ratusan MB di Y2) |
| **ML Tree Service (Python)** | 1 inferensi Sentinel-2/DeepForest per beberapa detik | RAM saat inferensi (~1–2 GB) — on-demand, di-queue, bukan jalur trafik publik |

**Kesimpulan §3**: throughput **bukan** alasan membeli server besar — Rust menangani ribuan RPS/core. Pendorong biaya yang sebenarnya adalah **availability dan keamanan data** (dana escrow donor + PII KTP → isolasi, backup teruji, enkripsi) plus **RAM untuk layanan pendukung** (PostGIS, Meilisearch, ML service). Struktur tier di §5 dibangun di atas kebutuhan itu, dengan throughput sebagai syarat minimum yang mudah terpenuhi.

---

## 4. Kebutuhan Storage & Bandwidth

### 4.1 Object storage — Cloudflare R2 (egress gratis) — SELURUH konten media platform

Seluruh konten yang diunggah dan dihasilkan platform — **file, gambar, DAN video** — berada di object storage, bukan di disk server. Basis per item dinyatakan agar dapat diaudit: foto inspeksi ber-watermark ~1,5 MB/foto × 3 foto/inspeksi; video profil/galeri lahan ~75 MB/video; photo sphere 360° ~15 MB; foto KTP pengelola ~1 MB/lahan; sertifikat PDF ~0,5 MB/dokumen; APK ±117 MB/build.

| Item | Basis | Launch (2 rb akun · 500 pohon · 30 merchant) | Y1 (25 rb · 8 rb pohon · 150 merchant) | Y2 (120 rb · 30 rb pohon · 400 merchant) |
| --- | --- | --- | --- | --- |
| Foto inspeksi lapangan (jejak audit ESG) | ~5 MB/inspeksi × ≥2 inspeksi/pohon/tahun | ~5 GB | ~80 GB | ~400 GB |
| **Video** — profil lahan, galeri, posts merchant, landing | ~75 MB/video × 1–2 video/lahan aktif + konten kampanye (A12) | ~5 GB | ~60 GB | ~250 GB |
| Photo sphere 360° (pengalaman pohon imersif) | ~15 MB/sphere | ~2 GB | ~15 GB | ~50 GB |
| Galeri foto lahan, avatar, media forum | — | ~3 GB | ~25 GB | ~100 GB |
| Sertifikat PDF + laporan ESG | ~0,5 MB/dokumen | ~0,5 GB | ~5 GB | ~20 GB |
| APK releases (retensi seluruh build) | ±117 MB/build | ~5 GB | ~10 GB | ~15 GB |
| Backup DB (retensi 30 hari) | §4.2 | ~3 GB | ~40 GB | ~150 GB |
| **Total** | | **~25 GB** | **~235–300 GB** | **~1,0–1,5 TB** |
| **Biaya** ($0,015/GB/bln) | | **< Rp 25 rb/bln** | **~Rp 75 rb/bln** | **~Rp 250–360 rb/bln** |

**Dua sifat R2 yang membuat konten berat tetap terkendali secara anggaran:**

1. **Egress gratis** — pemutaran video, unduhan APK, dan penayangan foto **tidak menimbulkan biaya bandwidth sama sekali**, berapa pun trafiknya. Pada object storage konvensional (mis. S3: egress ~$0,09/GB), justru egress video — bukan penyimpanannya — yang menjadi sumber biaya tak terduga; arsitektur ini menghilangkan kategori risiko tersebut sejak desain.
2. **Biaya linear dan kecil terhadap skala** — bahkan pada skenario media **5× proyeksi** (≈ 7,5 TB di Y2, misal adopsi video jauh lebih agresif), biaya R2 ≈ $112 ≈ **Rp 1,8 jt/bln** — tetap < 15% anggaran bulanan Y2 dan tercakup rentang §8.2 (analisis di §7.1).

> **Jalur eskalasi video (dinyatakan sekarang, dibeli nanti)**: hari ini video disajikan langsung dari R2 via CDN — memadai untuk video profil/galeri berdurasi pendek. Bila platform kelak membutuhkan *adaptive streaming* (video panjang, kualitas menyesuaikan koneksi), jalurnya adalah Cloudflare Stream (~$5/1.000 menit tersimpan + $1/1.000 menit tayang) — **dipicu trigger pemakaian dan diajukan sebagai revisi anggaran**, bukan dibeli di muka. Prinsip §6 berlaku juga untuk storage.

> **Catatan backup**: R2 sekaligus menjadi tujuan backup database (WAL + basebackup, §7.2) — object storage yang *off-site* dan tergeoreplikasi secara bawaan. **Server fisik khusus backup tidak diperlukan** dan justru lebih buruk: satu lokasi (risiko bencana lokal), perlu perawatan hardware, tanpa jaminan durability setara object storage. Bukti foto inspeksi adalah **jejak audit ESG** — retensinya mengikuti umur kontrak adopsi, bukan kebijakan hemat storage.

### 4.2 Database

- Baseline 35 MB (data uji; ~13 MB di antaranya baseline tetap PostGIS + batas provinsi).
- Pertumbuhan didorong: pohon + inspeksi + **escrow ledger append-only** (setiap peristiwa dana = baris permanen) + notifikasi + audit log super-admin.
- Proyeksi ukuran: Launch **< 1 GB** · Y1 **≤ 15 GB** · Y2 **≤ 80 GB** (dengan partisi + arsip tabel notifikasi/log tahunan mulai akhir Y1).
- Implikasi: kapasitas disk bukan masalah; yang dianggarkan adalah **IOPS NVMe + disiplin backup** (§7.2) — ledger escrow tidak boleh kehilangan lebih dari beberapa menit data.

### 4.3 Bandwidth

- Y1: 2.500 DAU × 5 halaman × ~1,5 MB ≈ 19 GB/hari ≈ 0,6 TB/bln **di edge** — mayoritas diserap cache Cloudflare (gratis); **origin cukup 100–200 GB/bln**. Unduhan APK & media sepenuhnya dari R2 (tidak menyentuh origin). Pilih paket VPS dengan kuota ≥ 2 TB / unmetered lokal — tersedia standar di provider Indonesia.

---

## 5. Spesifikasi yang Diajukan per Tier

### 5.0 Tabel keputusan

| Env | Kapan | Spesifikasi | VPS/bln | Status |
| --- | --- | --- | --- | --- |
| **Staging dedicated** | **Agustus 2026** | 1× VPS **2 vCPU / 4 GB / 80 GB NVMe** | Rp 150–350 rb | 📋 Diajukan |
| **Prod Tier 1 — Launch** | **Agustus 2026** | 1× VPS dedicated **4 vCPU / 8 GB / 160 GB NVMe**, Indonesia | Rp 400–800 rb | 📋 Diajukan |
| **Prod Tier 2 — Growth Y1** | saat trigger §6 terpicu (perkiraan awal 2027) | App **8 vCPU / 16 GB** + **DB node terpisah 4 vCPU / 8 GB NVMe** | Rp 1,3–2,6 jt | 📋 |
| **Prod Tier 3 — Scale Y2** | saat trigger terpicu (perkiraan akhir 2027–2028) | 2× app node + LB + **managed Postgres HA + read replica** + Redis dedicated + node ML terpisah | Rp 5–10 jt | 📋 |

### 5.0b Pemisahan peran server — Builder · Aplikasi · Database · Backup

Empat peran infrastruktur **dipisahkan sejak desain**, dan tingkat pemisahan fisiknya naik per tier — masing-masing sudah masuk dalam anggaran fase yang bersangkutan (tidak ada biaya tersembunyi yang menyusul):

| Peran | Launch (2026) | Growth Y1 (2027) | Scale Y2 (2028) | Dianggarkan di |
| --- | --- | --- | --- | --- |
| **Builder / CI-CD** (Jenkins, build image Docker) | Di **VPS staging** — terpisah dari production **sejak hari pertama** | Tetap di staging | Tetap di staging (naik spec bila antrean build memicu trigger §6) | Baris #1 rekapitulasi |
| **Aplikasi** (FE + BE + Redis + Meilisearch + ML) | VPS production (bersama DB — SPOF diterima sadar, §7.2) | **App node sendiri** 8 vCPU/16 GB | **2× app node + load balancer** + node ML terpisah | Baris #2 |
| **Database** (PostgreSQL + PostGIS) | Container dedicated di VPS production, RAM limit sendiri | **DB node fisik terpisah** 4 vCPU/8 GB NVMe | **Managed PostgreSQL HA + read replica** (failover otomatis + PITR) | Baris #2 |
| **Backup** | **R2 off-site** (WAL 15-menit + basebackup harian) — bukan VM | R2 + **drill restore bulanan** | R2 + PITR managed DB | Baris #4 |

**Dua keputusan pemisahan yang dinyatakan eksplisit:**

1. **Builder tidak pernah menumpang server production.** Proses build (compile Rust + bundle Next.js + build image Docker) adalah beban CPU/RAM yang datang mendadak — bila menumpang production, setiap deploy berarti degradasi layanan bagi pengguna; memisahkannya juga menjaga kebersihan rantai pasok (server production tidak memegang toolchain build, hanya menjalankan image yang sudah jadi). Ini sebabnya Jenkins ditempatkan di VPS staging sejak Tier 1 — pemisahan builder↔production **tidak menunggu fase growth**.
2. **Backup bukan VM dan tidak akan pernah menjadi VM.** Tujuan backup adalah bertahan saat server utamanya mati — menaruhnya di VM lain di data center yang sama mengulang risiko yang sama. Object storage off-site tergeoreplikasi (R2) memberi durability yang tidak bisa ditandingi VM backup, dengan biaya lebih kecil (§4.1). Kelayakannya dibuktikan dengan **uji restore** — gate go-live §9, lalu drill bulanan mulai Y1.

Dengan peta ini, pertanyaan "kapan multiple VM?" punya jawaban terukur: **pemisahan DB → app terjadi di Tier 2** (dipicu trigger §6, dianggarkan Rp 1,3–2,6 jt/bln), **redundansi app + HA database terjadi di Tier 3** (Rp 5–10 jt/bln) — keduanya sudah tercantum di anggaran per fase (§8.2), bukan biaya baru yang muncul belakangan.

### 5.1 Staging dedicated — environment review internal & UAT

**Diajukan: 1× VPS 2 vCPU / 4 GB / 80 GB NVMe** menjalankan stack lengkap milik sendiri (FE + BE + PostgreSQL/PostGIS + Redis + Meilisearch + nginx/tunnel) **plus Jenkins CI/CD self-host**.

**Kenapa staging dedicated dibutuhkan (kebutuhan operasional, bukan fasilitas tambahan):**

| Fungsi | Nilai bisnisnya |
| --- | --- |
| **Review internal stakeholder** | Setiap fitur dicek di staging sebelum dirilis — siklus feedback yang selama pengembangan terbukti efektif (35+ fase dirilis dengan pola ini) berjalan terus |
| **UAT & gate rilis** | Tidak ada perubahan masuk production tanpa lolos staging (pipeline: `development` → auto-deploy staging → review → approval gate → production) |
| **Tempat uji beban & eksperimen** | k6/uji destruktif dijalankan di staging **tanpa risiko menyentuh dana donor atau data KTP** |
| **Isolasi data** | Data uji dan data pribadi pengguna tidak pernah berada di mesin yang sama — kebersihan audit UU PDP |

**Bukti tidak berlebihan**: footprint stack aplikasi hanya ~0,5 GB (§2.1) + Jenkins ~0,8 GB; server 2 vCPU/4 GB memuat semuanya + ruang build dengan margin aman. Opsi lebih rendah (1 vCPU/1–2 GB, Rp 50–100 rb) **ditolak** karena tidak memuat PostGIS + Meilisearch + Jenkins + build Docker dengan topologi yang sama seperti production — dan staging yang topologinya berbeda membuat hasil review/UAT tidak valid. Spesifikasi lebih tinggi belum diperlukan: staging tidak memerlukan headroom lonjakan trafik (penggunanya internal, belasan orang).

### 5.2 Production Tier 1 — Launch (Agustus 2026)

**Diajukan: 1× VPS dedicated 4 vCPU / 8 GB / 160 GB NVMe, data center Indonesia** (UU PDP data residency untuk NIK/KTP + data donor), semua container milik sendiri:

| Container | RAM limit | Konfigurasi kunci |
| --- | --- | --- |
| FE Next.js | 1 GB | Halaman publik → cache CDN Cloudflare |
| BE Rust/Axum | 512 MB | Terukur hanya 46 MiB — limit memberi ruang lonjakan + WebSocket |
| PostgreSQL 16 + PostGIS dedicated | 2 GB | `shared_buffers` 512 MB; **WAL archiving ke R2 tiap 15 menit + basebackup harian** |
| Redis 7 dedicated | 256 MB | `maxmemory` + `allkeys-lru` |
| Meilisearch | 512 MB | Indeks lahan/pohon/merchant |
| ML Tree Service (on-demand) | 1,5 GB | Antrean inferensi — tidak berjalan permanen; dimatikan otomatis saat idle |
| nginx + cloudflared | 192 MB | Pola yang sudah terbukti di pipeline CI/CD |
| Headroom OS + lonjakan trafik | ~2 GB | |

**Bukti tidak kekurangan** — proyeksi utilisasi terhadap beban launch:

| Metrik | Beban launch (puncak, lonjakan ×10) | Kapasitas server | Utilisasi proyeksi |
| --- | --- | --- | --- |
| RPS total | ~20–50 | ribuan (BE Rust) / ratusan pasca-CDN (FE) | **< 10% CPU** |
| RAM | ~6 GB teralokasi (dengan limit) | 8 GB | ~75% teralokasi, ~30% terpakai riil (limit ≫ pemakaian terukur §2.1) |
| DB tx/s | < 5 tulis/s | ratusan | **< 5%** |
| Sinkronisasi Field App | batch idempotent, dapat antre | — | bukan beban puncak |

Server ini bahkan mampu menampung beban **awal Y1** — upgrade ke Tier 2 dipicu metrik, bukan tanggal.

**Bukti tidak berlebihan** — opsi yang dipertimbangkan dan alasannya:

| Opsi | Biaya/bln | Keputusan | Alasan |
| --- | --- | --- | --- |
| 1 VPS digabung staging + production | −Rp 250 rb | ❌ Ditolak | Dana escrow donor riil dan data KTP tidak boleh satu mesin dengan environment uji: uji beban/eksperimen bisa menjatuhkan production, dan audit UU PDP menuntut pemisahan data uji vs data pribadi |
| VPS production 2 vCPU / 4 GB (Rp 250–350 rb) | −Rp 300 rb | ❌ Ditolak | PostGIS 2 GB + Meilisearch + ML service + stack menyisakan RAM sangat terbatas tanpa headroom lonjakan; gagal syarat jaminan kecukupan (a) |
| **VPS 4 vCPU / 8 GB** | **Rp 400–800 rb** | ✅ **Diajukan** | Titik seimbang: isolasi penuh + headroom ≥ 5× + biaya tetap efisien |
| VPS 8 vCPU / 16 GB (Rp 0,9–1,3 jt) | +Rp 500 rb | ❌ Belum | Utilisasi proyeksi < 8% = membayar kapasitas yang tidak terpakai; resize vertical < 1 jam tersedia kapan pun trigger terpicu |
| Cloud managed penuh / Kubernetes | ≥ Rp 3–5 jt | ❌ Belum | Kompleksitas + biaya tidak sepadan untuk satu server; dipertimbangkan lagi di Tier 3 |
| Server fisik (colocation / on-premise) — termasuk untuk backup | CapEx Rp 30–80 jt + rak DC Rp 1–2 jt/bln | ❌ Ditolak | CapEx besar di muka tidak cocok untuk fase pre-revenue; pengadaan hardware berminggu-minggu bertentangan dengan prinsip upgrade-by-trigger (resize VPS < 1 jam); maintenance hardware jadi tanggungan sendiri; UU PDP data residency sudah terpenuhi via DC Indonesia bersertifikat (ISO 27001) — dan kebutuhan backup sudah terjawab lebih baik oleh R2 off-site (§4.1, §7.2). Dana pembayaran diproses Midtrans (gateway berlisensi), bukan disimpan platform — tidak ada tuntutan regulasi kontrol fisik di sisi kita |

### 5.3 Production Tier 2 — Growth Y1

Perubahan tunggal paling berdampak: **pisahkan database ke node sendiri** — app node naik ke 8 vCPU/16 GB, DB node 4 vCPU/8 GB NVMe. Tambahan wajib tier ini: PgBouncer (pool koneksi), Cloudflare Pro (~Rp 330 rb — WAF + cache analytics), Sentry Team + uptime monitor eksternal, dan **drill restore backup bulanan** (backup yang tidak pernah diuji = tidak ada backup). Staging dedicated tetap berjalan apa adanya.

Kapasitas hasil: lonjakan kampanye 250 RPS tertampung dengan cache hangat pada utilisasi ~30–40% — lolos syarat headroom.

### 5.4 Production Tier 3 — Scale Y2

Pendorongnya **availability**, bukan throughput: 2× app node + load balancer, **managed PostgreSQL HA + 1 read replica** (laporan ESG/leaderboard pindah ke replica), Redis node sendiri, **node ML terpisah** (inferensi Sentinel-2/DeepForest tidak lagi berbagi RAM dengan app), observability penuh (APM + log terpusat), SLO 99,9%. Mulai tier ini managed DB dibayar karena failover otomatis + PITR (point-in-time recovery) yang dikelola vendor lebih ekonomis dibanding merekrut satu engineer SRE.

### 5.5 Pembanding harga pasar (transparansi harga)

Harga publik per Juli 2026 (⚠️ verifikasi ulang saat pengadaan — harga cloud berubah):

| Provider | Spec staging (2 vCPU/4 GB) | Spec prod Tier 1 (4 vCPU/8 GB) | Catatan |
| --- | --- | --- | --- |
| IDCloudHost (ID) | ≈ Rp 175–300 rb | ≈ Rp 400–600 rb | Pay-as-you-go, ekstrapolasi dari harga publik (2 vCPU/2 GB mulai Rp 87–149 rb); DC Indonesia ✓ |
| Biznet Gio NEO Lite (ID) | ≈ Rp 200–300 rb | ≈ Rp 400–600 rb | NVMe, free bandwidth; DC Indonesia ✓ |
| DigitalOcean (SGP) | $24–32 (Rp 390–520 rb) | $48–63 (Rp 780 rb–1 jt) | Tooling bagus; data PII keluar RI → hanya cadangan |
| Vultr (SGP) | $20–24 (Rp 330–390 rb) | $40–48 (Rp 650–780 rb) | idem |

Rentang proposal (staging **Rp 150–350 rb**, production **Rp 400–800 rb**) = harga pasar apa adanya, tanpa margin. Preferensi: provider Indonesia (data residency UU PDP untuk NIK/KTP + latency pengguna domestik).

---

## 6. Trigger Upgrade Berbasis Metrik (pengendali anggaran)

Kapasitas **tidak** dinaikkan karena jadwal — hanya saat ambang berikut terlampaui (dipantau via Sentry + uptime monitor + node metrics):

| Metrik | Ambang | Aksi |
| --- | --- | --- |
| CPU host | > 60% sustained 30 menit (di luar deploy/backup) | Resize vertical → evaluasi tier berikutnya |
| RAM host | > 75% steady | Resize |
| Latensi API p95 | > 400 ms selama 1 jam | Investigasi (cache/query) → scale bila struktural |
| Pool DB | wait event / pemakaian > 70% | Naikkan pool → PgBouncer → DB node terpisah |
| Disk | > 70% | Expand volume |
| Error rate 5xx | > 0,5% berkelanjutan | Insiden — runbook §9 |
| Antrean ML service | > 10 menit menunggu inferensi berulang | Node ML terpisah (komponen Tier 3 bisa maju sendiri) |

Dua konsekuensi: belanja tidak pernah mendahului kebutuhan, dan setiap keputusan upgrade selalu punya bukti tertulis (metrik) — bukan perkiraan subjektif.

---

## 7. Uji Ketahanan Rencana (Analisis Skenario)

### 7.1 Bagaimana kalau estimasi meleset?

| Skenario | Dampak | Jawaban |
| --- | --- | --- |
| Engagement 2× asumsi (DAU 20%) | RPS proyeksi ×2 | Masih < 20% utilisasi Tier 1; tidak mengubah spesifikasi |
| **Lonjakan kampanye/PR 20×** nominal (≈ 500 RPS sesaat di Y1) | Puncak ekstrem | Cloudflare men-cache halaman publik (explore/landing — mayoritas trafik) → origin hanya menerima API dinamis → BE Rust menanganinya dengan mudah; DB terlindungi Redis. Lapis kedua: rate-limit per-IP (sudah terpasang di aplikasi). Lapis ketiga: resize vertical < 1 jam |
| Pertumbuhan akun 2× lebih cepat | Storage & DB naik | R2 & disk punya margin ≥ 3×; trigger §6 menaikkan tier lebih awal — anggaran naik mengikuti *volume adopsi yang juga naik* |
| **Volume konten media 5× proyeksi** (adopsi video sangat agresif) | Storage Y2 ~7,5 TB | Biaya R2 ≈ Rp 1,8 jt/bln — tetap < 15% anggaran bulanan Y2 dan tertampung rentang §8.2; egress tetap Rp 0 berapa pun penayangan; bila butuh adaptive streaming → jalur Cloudflare Stream diajukan sebagai revisi anggaran (§4.1) |
| Biaya AI Tira melampaui perkiraan | Biaya variabel naik | **Cap harian fail-closed sudah built-in** (bila cache budget mati, permintaan mahal ditolak — bukan berjalan tanpa batas); cap dan model dapat disetel admin tanpa deploy; multi-provider = pindah ke model termurah yang memadai |
| Trigger Tier 2 terpicu lebih awal (lonjakan pasca-public-launch, Nov 2026) | Biaya 12 bulan naik ke ≈ Rp 47,5 jt | **Sudah tercakup plafon Rp 50 jt** — upgrade berjalan tanpa proses persetujuan ulang di tengah momentum launch; tetap dilaporkan ke stakeholder dengan bukti metrik |
| Launch mundur | Biaya idle | Total staging + production launch hanya ~Rp 1 jt/bln; provisioning bisa ditunda (lead time 1–2 hari kerja) |

### 7.2 Bagaimana kalau server mati? (RTO/RPO — komitmen pemulihan)

RTO = waktu maksimum pemulihan layanan; RPO = maksimum data yang boleh hilang.

| Env/Tier | RPO | RTO | Mekanisme |
| --- | --- | --- | --- |
| Staging | best-effort | ≤ 1 hari | Redeploy dari CI/CD + seed; tidak ada data pengguna nyata |
| Prod 1 — Launch | **≤ 15 menit** | **≤ 4 jam** | WAL archiving ke R2 tiap 15 mnt + basebackup harian; rebuild dari compose + restore — **diuji sebelum go-live, bukan setelah insiden** |
| Prod 2 — Y1 | ≤ 5 menit | ≤ 1 jam | DB node terpisah + streaming archive; aplikasi stateless cukup di-redeploy |
| Prod 3 — Y2 | ≈ 0 | ≤ 15 menit | Managed HA failover otomatis + replica |

Target SLO production: 99,5% (launch) → 99,8% (Y1) → 99,9% (Y2). Single point of failure di Tier 1 **diterima secara sadar** untuk fase launch karena RPO/RTO di atas memadai untuk volume ≤ 2.000 akun — dan dihilangkan bertahap justru saat volumenya membenarkan biayanya. Catatan khusus AdopTree: **escrow ledger dan bukti foto inspeksi adalah data fidusia** (komitmen pencairan dana donor + jejak audit ESG) — keduanya dicakup RPO di atas (ledger di DB, foto langsung ke R2 tergeoreplikasi).

---

## 8. Anggaran Diajukan — Rincian 12 Bulan Pertama

### 8.1 Inventaris langganan software (LENGKAP — semua yang platform pakai)

Setiap layanan pihak ketiga yang disentuh platform tercantum di sini — termasuk yang **Rp 0**, supaya terlihat tidak ada yang lupa dihitung. Basis perhitungan dicantumkan agar angka bisa diaudit; harga per Juli 2026 (verifikasi saat pengadaan).

| Layanan | Fungsi | Basis perhitungan | Launch | Y1 (2027) | Y2 (2028) |
| --- | --- | --- | --- | --- | --- |
| **Cloudflare** (plan) | CDN, WAF, Tunnel, DNS | Free → Pro $20/bln mulai Y1 (WAF + cache analytics) | Rp 0 | ~Rp 330 rb | Rp 330 rb–1 jt |
| **Cloudflare R2** | SELURUH konten media: foto inspeksi ber-watermark, **video** (profil/galeri lahan, posts, landing), photo sphere 360°, galeri, sertifikat PDF, laporan ESG, APK releases, backup DB | $0,015/GB/bln, egress gratis (§4.1); skenario media 5× tetap ≈ Rp 1,8 jt/bln (§7.1) | < Rp 25 rb | ~Rp 75 rb | ~Rp 350 rb |
| **Mapbox** | Peta GIS web (explore, detail lahan, 3D tracker) + Static API (watermark kamera mobile) + geocoding | Free tier: 50 rb web loads + 50 rb static + 25 rb mobile MAU + 100 rb geocoding/bln; lalu ~$5/1.000 loads; loads ≈ DAU × 30 sesi × 40–60% sesi membuka peta (A9) | Rp 0 (jauh di bawah free tier) | Rp 0–800 rb (~38 rb loads — umumnya masih free) | Rp 1–5 jt (~180 rb loads) — **mitigasi wajib**: thumbnail statis + lazy-load peta + satu style (A9) |
| **Tira AI** (OpenAI/Gemini — multi-provider, runtime-switchable) | Co-pilot merchant (create land/campaign), support 24/7, normalize-geo, species autofill | Usage-based per token; **cap harian fail-closed + per-identitas sudah built-in**; biaya ≈ percakapan × panjang konteks (A11) | Rp 100–300 rb | Rp 0,5–2 jt | Rp 2–6 jt |
| **Resend** | Email transaksional (verifikasi, reset, notifikasi, kuitansi) | Free 3 rb/bln → Pro $20 (50 rb) → Scale $90 (100 rb); volume ≈ 2–6 email/akun aktif/bln (A10) | Rp 0 | Rp 330 rb–1,5 jt | — (migrasi SES di bawah) |
| **AWS SES** (pengganti Resend saat volume > 100 rb/bln) | idem | $0,10/1.000 email | — | — | Rp 0,8–2 jt |
| **Sentry** | Error monitoring FE + BE | Developer free → Team ~$26 → Business ~$80 | Rp 0 | ~Rp 420 rb | ~Rp 0,4–1,3 jt |
| **Uptime monitor** (+ APM di Y2) | Alert eksternal saat situs down | UptimeRobot free → Better Stack ~$25 | Rp 0 | Rp 0–400 rb | Rp 400 rb–1 jt |
| **Firebase FCM** | Push notification mobile (Field App) | Gratis tanpa batas volume | Rp 0 | Rp 0 | Rp 0 |
| **Web Push (VAPID)** | Push notification browser — standar terbuka, keypair milik sendiri | Rp 0 by design (tanpa vendor) | Rp 0 | Rp 0 | Rp 0 |
| **Solana RPC** | Wallet auth (SIWS) hari ini; NFT mint Q3 2026 | RPC publik gratis; RPC berbayar diajukan **terpisah** bersama sprint mint | Rp 0 | (proposal terpisah) | (proposal terpisah) |
| **Container registry** | Image Docker FE/BE privat | GHCR (gratis) / Docker Hub Pro ~$11 | Rp 0–180 rb | Rp 0–180 rb | Rp 0–180 rb |
| **Domain** `adoptreeworld.com` | — | ~Rp 300 rb/tahun ÷ 12 | ~Rp 25 rb | ~Rp 25 rb | ~Rp 25 rb |
| **Jenkins** (CI/CD) | Build + deploy otomatis | Open-source, self-host di VPS staging | Rp 0 | Rp 0 | Rp 0 |
| **GitHub** | Repo kode privat | Free plan mencukupi (CI di Jenkins, bukan Actions) | Rp 0 | Rp 0 | Rp 0 |
| **Google OAuth** · **Instagram Graph API** · citra **Sentinel-2** (program Copernicus) · Meilisearch · Redis · PostgreSQL+PostGIS · ML Tree Service (self-host) · seluruh library OSS (Next.js, Rust/Axum, Flutter, dsb.) | Login, cross-post forum, citra satelit, search, cache, DB, estimasi pohon, seluruh stack | Gratis / open-source / self-host | Rp 0 | Rp 0 | Rp 0 |
| **Subtotal software** | | | **Rp 0,1–0,8 jt** | **Rp 1,3–4,7 jt** | **Rp 4,7–11 jt** |

> Batas atas subtotal mengandaikan **semua** layanan menyentuh paket berbayar maksimalnya bersamaan — jarang terjadi. Nilai realistis (dipakai untuk proyeksi §8.3): Launch ~Rp 200 rb · Y1 ~Rp 1,8 jt · Y2 ~Rp 6,5 jt.
>
> **Di luar inventaris (bukan langganan infrastruktur)**: biaya transaksi Midtrans (±2,9% per transaksi — pos payment gateway PRD §14.4, mengikuti penjualan) dan biaya program Tukar Poin (pembelian pulsa/kuota redemption — biaya program insentif kontributor).

### 8.2 Ringkasan biaya bulanan (server + software)

| Komponen | Launch (Agu–Des 2026) | Growth Y1 (2027) | Scale Y2 (2028) |
| --- | --- | --- | --- |
| **VPS staging dedicated** | Rp 150–350 rb | Rp 150–350 rb | Rp 250–500 rb |
| **VPS production** | Rp 400–800 rb | Rp 1,3–2,6 jt | Rp 5–10 jt |
| **Subtotal software** (§8.1) | Rp 0,1–0,8 jt | Rp 1,3–4,7 jt | Rp 4,7–11 jt |
| **Total/bulan (rentang penuh)** | **Rp 0,6–1,7 jt** | **Rp 2,8–7,5 jt** | **Rp 10–21 jt** |
| **Titik tengah realistis** | **~Rp 1,0 jt** | **~Rp 4,5 jt** | **~Rp 17 jt** |

### 8.3 Proyeksi 12 bulan (Agustus 2026 – Juli 2027)

| Pos | Perhitungan | Jumlah |
| --- | --- | --- |
| Staging dedicated, 12 bulan | 12 × ~Rp 250 rb | Rp 3,0 jt |
| Production launch + software, 5 bulan (Agu–Des 2026) | 5 × ~Rp 720 rb (Prod Tier 1 + software Launch) | Rp 3,6 jt |
| Production growth Y1 + software, 7 bulan (Jan–Jul 2027) | 7 × ~Rp 4,2 jt (Prod Tier 2 + software Y1) | Rp 29,6 jt |
| One-time: hardening, drill restore, k6 gate go-live | jam kerja internal + Rp 0 lisensi | Rp 0 |
| Buffer 10% (fluktuasi kurs/harga/volume) | | Rp 3,6 jt |
| **Total proyeksi realisasi (base — upgrade mengikuti trigger, perkiraan Jan 2027)** | | **≈ Rp 39,8 jt** |
| **Skenario upgrade dini** — trigger terpicu Nov 2026 seiring lonjakan pasca-public-launch (3 bln launch + 9 bln growth) | 12×0,25 + 3×0,72 + 9×4,2 + buffer 10% | **≈ Rp 47,5 jt** |
| **PLAFON DIAJUKAN** (menampung base maupun skenario dini) | | **Rp 50 jt** |

> **Konteks**: satu paket CSR korporasi rata-rata di PRD §7.2 bernilai $10.000 (≈ Rp 160 jt) — plafon infrastruktur setahun penuh ≈ **sepertiga dari satu deal CSR**, sudah termasuk environment review internal (staging) dan seluruh langganan software. Dibanding alokasi PRD §14.4 (infra 2026: $3.600 ≈ Rp 57 jt; 2027: $12.000 ≈ Rp 190 jt), plafon ini ≈ **26% ruang 2027** (realisasi proyeksi ≈ 21%) — sisanya tetap tersedia untuk sprint NFT (RPC berbayar), lonjakan tak terduga, dan environment tambahan, **tanpa perlu diajukan sekarang**. Prinsip pengendalian tetap berlaku: realisasi mengikuti trigger §6, dan bila mendekati plafon, pengeluaran berikutnya kembali dimintakan persetujuan.

---

## 9. Checklist Provisioning & Go-Live (target Agustus 2026)

### Staging dedicated

- [ ] Provision VPS 2 vCPU/4 GB + hardening dasar (SSH key-only, ufw, auto security update)
- [ ] Deploy stack lengkap (FE, BE, Postgres/PostGIS, Redis, Meilisearch, nginx/tunnel) + Jenkins + seed data uji
- [ ] Arahkan subdomain staging + pipeline Jenkins auto-deploy branch `development`
- [ ] Smoke test E2E + akses review untuk stakeholder

### Production (Tier 1)

- [ ] Provision VPS 4 vCPU/8 GB Indonesia + hardening (SSH key-only, ufw, fail2ban, unattended security update)
- [ ] Compose production + **secrets baru** (JWT, DB password, kunci enkripsi 2FA, HMAC QR, VAPID web push — tidak reuse staging)
- [ ] Postgres dedicated: migrasi dari nol + seed minimal (settings, katalog spesies, batas provinsi)
- [ ] Cloudflare Tunnel production → `adoptreeworld.com` + subdomain API
- [ ] Jenkins job production diarahkan ke host baru (approval gate tetap)
- [ ] WAL archiving 15-menit + basebackup harian ke R2 + **uji restore penuh** (gate go-live — bukan opsional; ledger escrow adalah data fidusia)
- [ ] Sentry env `production` + uptime monitor eksternal + alert
- [ ] **k6 terhadap server production**: gate hijau untuk profil beban launch (§3.2) sebelum menerima trafik nyata
- [ ] DNS cutover + smoke test E2E (auth + 2FA, adopsi + checkout Midtrans sandbox→live, escrow ledger, sertifikat PDF, notifikasi web push + FCM)
- [ ] Arahkan distribusi APK (`/download/releases`) + verifikasi unduhan `latest.apk` via CDN
- [ ] Runbook insiden 1 halaman: restart, rollback image, restore DB, kontak eskalasi

---

## Appendix A — Daftar Asumsi (untuk diaudit/diganti)

| # | Asumsi | Nilai | Sensitivitas bila meleset |
| --- | --- | --- | --- |
| A1 | DAU = 10% akun | 10% | ×2 → tetap < 20% utilisasi Tier 1 (§7.1) |
| A2 | Concurrent = 10% DAU | 10% | idem |
| A3 | 6 req/menit/user aktif | 0,1 RPS | idem |
| A4 | Faktor lonjakan trafik | 10× | Lonjakan 20× dianalisis terpisah (§7.1) |
| A5 | CDN offload halaman publik | 70% | Konservatif; realita biasanya > 85% untuk halaman explore/landing |
| A6 | Foto inspeksi ~5 MB per inspeksi, ≥ 2 inspeksi/pohon/tahun | 3 foto × 1,5 MB | ×2 → R2 Y2 tetap < Rp 500 rb/bln |
| A12 | Video ~75 MB/video, 1–2 video per lahan aktif + konten kampanye | per §4.1 | Media total 5× → R2 Y2 ≈ Rp 1,8 jt/bln (< 15% anggaran bulanan Y2); kebutuhan adaptive streaming → Cloudflare Stream via revisi anggaran |
| A7 | Kurs USD | Rp 16.000 | Buffer 10% di §8.3 |
| A8 | Harga provider & software | per Jul 2026 | **Wajib verifikasi ulang saat pengadaan** |
| A9 | % sesi yang membuka peta (basis biaya Mapbox) | 40–60% (peta = fitur inti AdopTree) | Y1 tetap dekat free tier; Y2 dimitigasi thumbnail statis + lazy-load + konsolidasi style — tanpa mitigasi batas atas Y2 naik ~2× |
| A10 | Email per akun aktif per bulan | 2–6 | Volume > 100 rb/bln → migrasi Resend → AWS SES ($0,10/1.000) memangkas biaya email ~80% |
| A11 | Biaya AI per percakapan Tira | Rp 50–300 (model hemat, konteks terkendali) | Cap harian fail-closed membatasi eksposur maksimum per definisi; admin dapat menurunkan cap/model tanpa deploy |

## Appendix B — Sumber Harga Publik

- IDCloudHost — halaman pricing publik (Cloud VPS pay-as-you-go; mulai Rp 87–149 rb untuk 2 vCPU/2 GB sebagai anchor ekstrapolasi): https://idcloudhost.com/pricing/
- Biznet Gio — NEO Lite / NEO Lite Pro (NVMe, free bandwidth): https://www.biznetgio.com/pricelist
- DigitalOcean — Droplet pricing (Basic 4 vCPU/8 GB ≈ $48/bln; General Purpose mulai $63/bln): https://www.digitalocean.com/pricing/droplets
- Vultr — Cloud Compute (4 vCPU/8 GB ≈ $40–48/bln): halaman pricing publik Vultr
- Cloudflare R2 ($0,015/GB/bln, egress $0) & Cloudflare Pro ($20/bln): halaman pricing publik Cloudflare
- Mapbox — free tier 50.000 web map loads + 50.000 Static API + 25.000 mobile MAU + 100.000 geocoding per bulan, lalu ~$5/1.000 loads: https://www.mapbox.com/pricing
- Resend — Free 3 rb → Pro $20 (50 rb) → Scale $90 (100 rb email/bln): https://resend.com/pricing · AWS SES $0,10/1.000 email (jalur migrasi volume tinggi)
- Sentry — Developer free → Team ~$26 → Business ~$80/bln: halaman pricing publik Sentry
- OpenAI / Google Gemini — harga per token publik masing-masing provider (multi-provider: model termurah yang memadai yang dipakai)
- Docker Hub Pro ~$11/bln vs GitHub Container Registry (gratis untuk kebutuhan kita): halaman pricing publik masing-masing

⚠️ Seluruh harga adalah harga publik per Juli 2026 dan **wajib diverifikasi ulang saat pengadaan**.

---

## Changelog

| Versi | Tanggal | Perubahan |
| --- | --- | --- |
| v1.3 | 2026-07-11 | **Plafon direvisi Rp 40 jt → Rp 50 jt berbasis skenario** (menjawab telaah stakeholder): biaya bulanan fase growth AdopTree lebih tinggi dari platform sejenis (~Rp 4,5 jt vs ~Rp 3,6 jt — Tira AI + konten video + stack geospasial/ML), dan plafon lama hanya menampung asumsi upgrade Jan 2027; skenario upgrade dini Nov 2026 (lonjakan pasca-public-launch) ≈ Rp 47,5 jt kini tercakup plafon sehingga momentum launch tidak menunggu persetujuan ulang. Titik tengah realistis diselaraskan dengan model Excel (Y1 ~Rp 4,5 jt; Y2 ~Rp 17 jt); realisasi proyeksi base ≈ Rp 39,8 jt tidak berubah |
| v1.2 | 2026-07-11 | Model perhitungan Excel ([perhitungan-kapasitas-anggaran.xlsx](perhitungan-kapasitas-anggaran.xlsx)) ditambahkan: 8 sheet (Ringkasan · Asumsi · Beban · StorageR2 · Software · Server · Anggaran12Bln · Pembanding) dengan formula hidup — seluruh asumsi A1–A12 + anchor volume menjadi sel input; total 12 bulan terhitung ≈ Rp 39,8 jt ≤ plafon Rp 40 jt |
| v1.1 | 2026-07-11 | **Anggaran konten media dieksplisitkan** (§4.1): baris video + photo sphere 360° dengan basis volume per item, skenario media 5× (tetap < 15% anggaran Y2 — egress R2 gratis), jalur eskalasi Cloudflare Stream via revisi anggaran; asumsi A12 baru. **§5.0b Pemisahan peran server** ditambahkan: peta Builder · Aplikasi · Database · Backup per tier + dua keputusan eksplisit (builder tidak pernah menumpang production; backup = object storage off-site, bukan VM) — menegaskan pemisahan multi-VM di fase growth sudah tercantum dalam anggaran per fase |
| v1.0 | 2026-07-11 | Dokumen awal — dua lapisan ([ringkasan-pengajuan.md](ringkasan-pengajuan.md) 1 halaman + rujukan teknis ini): baseline terukur dari aplikasi berjalan (docker stats/psql/df), model beban dari target PRD §14.2, inventaris software lengkap (termasuk Tira AI usage-based dengan cap fail-closed, Mapbox dengan formula + mitigasi, item Rp 0), jaminan kecukupan (utilisasi proyeksi + RTO/RPO/SLO) & efisiensi biaya (tabel opsi ditolak + pembanding 4 provider), trigger upgrade berbasis metrik, analisis skenario, anggaran 12 bulan ≈ Rp 35–40 jt dengan uji kewajaran terhadap PRD §14.4, checklist provisioning, appendix asumsi A1–A11 + sumber harga |
