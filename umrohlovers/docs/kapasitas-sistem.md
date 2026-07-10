# 67 — Kapasitas Sistem & Kebutuhan Infrastruktur (Staging + Production)

> **Status**: Living document · v1.0 — 7 Juli 2026
> **Pemilik**: Tech Lead (Aditira)
> **Ringkasan satu kalimat**: Staging hari ini sudah cukup di VPS shared; production **belum di-provision** — dokumen ini menghitung kebutuhan per tahap pertumbuhan (Pilot → Launch → Scale) dengan asumsi eksplisit, plus checklist go-live.
> **Versi high-level untuk investor**: PRD §10.5 ([PRD-UmrohLovers-Investor.md](../prd/PRD-UmrohLovers-Investor.md))

---

## 1. Kondisi Terukur Hari Ini (baseline, 7 Juli 2026)

Semua angka di bawah **diukur langsung** dari server (bukan estimasi):

### 1.1 Host — VPS shared `adoptree` (Indonesia)

| Resource | Kapasitas | Terpakai | Catatan |
|---|---|---|---|
| CPU | **2 vCPU** | idle mayoritas waktu | Dibagi 3 project (AdopTree, Ajarka, umrohlovers) — total 14 container |
| RAM | **7,5 GiB** | ~2,3 GiB used (5,2 GiB available) | Termasuk Jenkins, Postgres, Redis, Meilisearch, nginx, cloudflared |
| Disk | **99 GB** | 21 GB (21%) | Docker images 7,2 GB (98% reclaimable — perlu prune rutin) |

### 1.2 Footprint container umrohlovers (staging)

| Container | RAM terpakai | RAM limit | CPU idle |
|---|---|---|---|
| `kopsimari-haji-frontend-staging` (Next.js 15) | ~101 MiB | 512 MiB | ~0% |
| `kopsimari-haji-backend-staging` (Go/Echo) | ~46 MiB | 384 MiB | ~0% |
| Database `kopsimari_haji_staging` | **21 MB** | (shared `adoptree-postgres`, PG16) | — |
| Redis | (shared `adoptree-redis`, redis:7-alpine) | — | — |

**Interpretasi**: footprint aplikasi sangat kecil (Go BE hanya ~46 MB RAM). Yang membatasi bukan aplikasi kita, melainkan (a) 2 vCPU dibagi 3 project, dan (b) Postgres/Redis shared.

### 1.3 Production: **BELUM ADA**

- Port sudah direservasi (FE `:3201`, BE `:8281`), compose dir production sudah dipetakan (`/opt/adoptree/docker/production/kopsimari-haji`), Jenkins pipeline production (approval gate) sudah jalan — **tapi container production belum pernah di-deploy**.
- Domain `umrohlovers.id` + `api.umrohlovers.id` sudah aktif di Cloudflare (Tunnel), tinggal diarahkan.
- **Keputusan yang dibutuhkan**: apakah production ikut menumpang VPS shared (murah, risiko noisy-neighbor) atau VPS dedicated (rekomendasi — lihat §4).

### 1.4 Bukti beban yang sudah ada (hasil uji, bukan teori)

| Bukti | Hasil | Implikasi |
|---|---|---|
| k6 load test **1.000 VUs** (Mei 2026, box shared 2 vCPU) | Lulus; bottleneck teridentifikasi di **DB pool 25 koneksi** | Aplikasi sanggup melewati skala pilot bahkan di box sekarang; DB pool adalah tuning pertama saat naik kelas |
| Redis caching endpoint katalog | **18× cache hit improvement** | Beban baca katalog (mayoritas trafik publik) murah — bisa dilayani tanpa menyentuh DB |
| Lighthouse | Perf 92+ / A11y 96 / BP 99 / SEO 100 | Halaman publik ringan; bandwidth per pageview terkendali (gzip aktif) |

---

## 2. Model Beban — Asumsi Eksplisit

Anchor volume dari target bisnis (PRD §13–14). Semua asumsi bisa diganti — formula tetap.

### 2.1 Formula

```
DAU               = 10% × akun terdaftar aktif
Concurrent peak   = 10% × DAU  (jam sibuk)
Request rate/user = ~6 request/menit saat aktif (SPA + API + polling ringan) = 0,1 RPS/user
RPS nominal       = concurrent × 0,1
RPS target sizing = RPS nominal × faktor spike 5–10×  (campaign, Munas, broadcast WA/radio)
```

### 2.2 Hasil per tahap

| Tahap | Periode | Akun | DAU | Concurrent peak | RPS nominal | **RPS target sizing** |
|---|---|---|---|---|---|---|
| **Pilot** | Sep–Nov 2026 | 500–2.000 | 50–200 | 20–40 | 2–4 | **50** |
| **Launch (Y1)** | Nov 2026–2027 | 20.000–50.000 | 2.000–5.000 | 200–500 | 20–50 | **250–500** |
| **Scale (Y3)** | 2028–2029 | 100.000–150.000 | 10.000–15.000 | 1.000–1.500 | 100–150 | **1.000–1.500** |

### 2.3 Kapasitas per komponen (rule-of-thumb konservatif)

| Komponen | Kapasitas per unit | Sumber batasan |
|---|---|---|
| **Go/Echo BE** | 2.000–5.000 RPS/core (JSON + query ringan); ratusan RPS/core bahkan untuk endpoint berat | CPU-bound; Go sangat efisien — BE **bukan** bottleneck sampai Y3 |
| **Next.js SSR** | 50–150 RPS/core untuk halaman SSR | Komponen paling lapar CPU; mitigasi: halaman publik di-cache CDN Cloudflare + static/ISR |
| **PostgreSQL 16** (4 vCPU NVMe) | 500–1.000 tx tulis/s; puluhan ribu read/s dengan index benar | Pool koneksi (sekarang 25) + disk IOPS; mayoritas read sudah diserap Redis |
| **Redis 7** | 50.000+ ops/s single instance | Praktis tidak akan jadi bottleneck di skala ini |

**Kesimpulan model**: sampai **Launch Y1**, satu node aplikasi + satu node DB yang benar sudah lebih dari cukup. Kebutuhan menjadi arsitektural (HA, replica, LB) baru di **Y3** — dan itu pun didorong oleh *availability* (uang jamaah = uptime kritis), bukan oleh throughput.

---

## 3. Kebutuhan Storage & Bandwidth

### 3.1 Object storage (Cloudflare R2)

| Item | Ukuran satuan | Pilot (2k akun) | Y1 (50k akun) | Y3 (150k akun) |
|---|---|---|---|---|
| KYC 4 dokumen/akun (KTP, KK, paspor, selfie) | ~6 MB/akun | 12 GB | 300 GB | 900 GB |
| Cover paket + foto item mitra + avatar | — | ~2 GB | ~30 GB | ~100 GB |
| e-Visa PDF (per jamaah berangkat) | ~0,5 MB | ~0,3 GB | ~6 GB | ~25 GB |
| Backup DB harian (retensi 30 hari) | — | ~3 GB | ~50 GB | ~200 GB |
| **Total R2** | | **~17 GB** | **~390 GB** | **~1,2 TB** |
| **Biaya R2** ($0,015/GB/bln, egress **gratis**) | | ~$0,3/bln | ~$6/bln | ~$18/bln |

R2 praktis gratis di semua skala — bukan faktor biaya.

### 3.2 Database (PostgreSQL)

- Baseline sekarang: 21 MB (data uji).
- Estimasi pertumbuhan: ~10–20 MB per 1.000 booking (bookings + jamaah + escrow + payments) + **audit log UU PDP sebagai penyumbang terbesar** (~100 KB/booking efektif).
- Proyeksi: Pilot **< 1 GB** · Y1 **≤ 10 GB** · Y3 **≤ 60 GB** (dengan archive/partisi `audit_logs` tahunan).
- Semua muat nyaman di NVMe; yang penting adalah **IOPS & backup**, bukan kapasitas.

### 3.3 Bandwidth

- Halaman publik ~1–1,5 MB gzip; aset berat (Mapbox tile, foto R2) **tidak lewat origin**.
- Y1: 5.000 DAU × 5 halaman × 1,5 MB ≈ 37 GB/hari ≈ **1,1 TB/bln di edge** — mayoritas diserap cache Cloudflare (gratis); origin cukup **100–300 GB/bln**. Kuota bandwidth VPS Indonesia umumnya cukup; pilih paket unmetered/≥2 TB untuk aman di Y1+.

---

## 4. Rekomendasi Sizing per Tier

### 4.0 Ringkasan (tabel keputusan)

| Env | Kapan | Spec | Est. biaya/bln | Status |
|---|---|---|---|---|
| **Staging** (tetap) | sekarang | Menumpang VPS shared 2 vCPU/8 GB (existing) | **Rp 0 marginal** | ✅ Berjalan, cukup |
| **Production — Pilot** | provision **Agustus 2026** (sebelum pilot Sep) | VPS dedicated 4 vCPU / 8 GB / 160 GB NVMe, Indonesia | **Rp 400–800 rb** | 📋 Belum ada |
| **Production — Launch Y1** | upgrade Okt–Nov 2026 (pra-Munas) | App node 8 vCPU / 16 GB + DB dipisah 4 vCPU / 8 GB NVMe | **Rp 1,5–3,5 jt** | 📋 |
| **Production — Scale Y3** | 2027 akhir → 2028 | 2× app node + LB, managed Postgres HA + replica, Redis dedicated, PgBouncer | **Rp 8–15 jt** | 📋 |

> Provider kandidat (Indonesia, UU PDP data residency): IDCloudHost, Biznet Gio, CloudKilat; alternatif regional DO/Vultr Singapore bila latency dapat diterima — **data PII tetap direkomendasikan on-shore**.

### 4.1 Staging — tetap seperti sekarang

- Shared box **cukup** untuk staging selamanya (footprint 150 MB RAM total, DB 21 MB).
- Tindakan minor: (a) `docker system prune` terjadwal (7,2 GB image reclaimable), (b) naikkan DB pool BE staging bila uji beban ulang.

### 4.2 Production Tier 1 — Pilot (Sep–Nov 2026, ≤ 2.000 akun)

**VPS dedicated 4 vCPU / 8 GB / 160 GB NVMe** — satu box, semua container milik sendiri (tidak berbagi dengan AdopTree):

| Container | RAM limit | Catatan konfigurasi |
|---|---|---|
| FE Next.js | 1 GB | Output standalone (sudah); halaman publik → cache CDN |
| BE Go | 512 MB | **DB pool 25 → 40**; worker H-30/H-14 include |
| PostgreSQL 16 dedicated | 2 GB | `shared_buffers` 512 MB, `max_connections` 100; **WAL archiving + backup harian ke R2 + PITR-ready** |
| Redis 7 dedicated | 256 MB | `maxmemory` + `allkeys-lru` |
| nginx + cloudflared | 128 MB | Pola sama dengan staging |
| **Headroom OS/spike** | ~4 GB | |

Kenapa dedicated meski trafik pilot kecil: **ini uang jamaah sungguhan** — isolasi dari noisy-neighbor, blast radius insiden, dan kebersihan audit (UU PDP/PP 38) lebih penting daripada selisih ~Rp 500 rb/bln.

### 4.3 Production Tier 2 — Launch Y1 (Nov 2026–2027, 20–50 rb akun)

- **Pisahkan DB ke node sendiri** (4 vCPU / 8 GB NVMe) atau managed Postgres — langkah tunggal paling berdampak (bottleneck k6 = DB).
- App node naik ke 8 vCPU / 16 GB; FE bisa di-scale `docker compose --scale` di belakang nginx bila SSR menjadi berat.
- Tambahan wajib tier ini: PgBouncer (atau pool per-service), Cloudflare Pro (WAF + cache analytics, $20), uptime monitoring eksternal + alerting (Sentry sudah ada), **drill restore backup** terjadwal bulanan.
- Kapasitas hasil: nyaman menampung 250–500 RPS spike Munas dengan cache hangat.

### 4.4 Production Tier 3 — Scale Y3 (2028, 100–150 rb akun)

- 2× app node + load balancer (HA aplikasi), **managed PostgreSQL HA + 1 read replica** (laporan/leaderboard/analytics pindah ke replica), Redis dedicated node, object storage tetap R2.
- Observability penuh: Grafana/Prometheus atau vendor APM, log terpusat, SLO uptime ≥ 99,9%.
- Mulai evaluasi: partisi `audit_logs`, arsip booking selesai > 2 tahun, dan (bila mobile app rilis) rate-limit/API gateway.
- Estimasi Rp 8–15 jt/bln **konsisten** dengan line item PRD §14.3 (infrastruktur Rp 0,8 M/tahun 2028 sudah mencakup Sentry, R2, Mapbox, email, dan multi-env).

---

## 5. Bottleneck yang Sudah Diketahui & Mitigasi Bertahap

| # | Bottleneck | Bukti | Mitigasi (urutan eksekusi) |
|---|---|---|---|
| 1 | **DB pool 25** | k6 1.000 VUs | Tier 1: naikkan ke 40 → Tier 2: PgBouncer + DB node terpisah |
| 2 | **2 vCPU shared** dengan 2 project lain | terukur | Tier 1: pindah production ke VPS dedicated |
| 3 | **Next.js SSR CPU-bound** | karakteristik framework | Cache CDN halaman publik (Cloudflare) + ISR; scale-out FE horizontal Tier 2+ |
| 4 | **Audit log growth** (UU PDP) | desain | Partisi + archive tahunan mulai Tier 2 |
| 5 | **Single point of failure** (1 box) | arsitektur Tier 1 | Diterima untuk pilot (dengan backup harian + PITR); dihilangkan bertahap Tier 2 (DB terpisah) → Tier 3 (HA penuh) |

---

## 6. Checklist Go-Live Production (Tier 1)

- [ ] Provision VPS 4 vCPU/8 GB Indonesia + hardening (SSH key-only, ufw, fail2ban, auto security update)
- [ ] Compose production (`kopsimari-haji-prod`) + secrets baru (JWT, DB password, HMAC webhook — **jangan reuse staging**)
- [ ] Postgres dedicated + migrasi goose dari nol + seed minimal (settings, zona, BPS)
- [ ] Cloudflare Tunnel baru diarahkan ke box production (`umrohlovers.id`, `api.umrohlovers.id`)
- [ ] Jenkins deploy-production job diarahkan ke host baru (approval gate tetap)
- [ ] Backup harian DB → R2 + **uji restore** sebelum go-live (bukan hanya backup)
- [ ] Sentry env `production` + uptime monitor eksternal + alert Discord
- [ ] k6 ulang **terhadap box production** (target: 500 VUs hijau, pool 40)
- [ ] DNS cutover + smoke test E2E (auth, KYC, booking mock, akad PDF)
- [ ] Dokumen runbook insiden (restart, rollback image, restore DB)

---

## 7. Ringkasan Biaya Infrastruktur per Bulan (semua layanan)

| Komponen | Pilot | Launch Y1 | Scale Y3 |
|---|---|---|---|
| VPS production | Rp 400–800 rb | Rp 1,5–3 jt | Rp 6–12 jt |
| Staging (shared, existing) | Rp 0 | Rp 0 | Rp 0–500 rb (pisah bila perlu) |
| Cloudflare (Tunnel/CDN/R2) | ~Rp 20 rb | ~Rp 400 rb (Pro + R2) | ~Rp 700 rb |
| Sentry / monitoring | Rp 0 (free tier) | ~Rp 400 rb | ~Rp 1 jt |
| Email (Resend) + Mapbox | Rp 0–150 rb | ~Rp 300–800 rb | ~Rp 1–2 jt |
| **Total** | **≈ Rp 0,5–1 jt/bln** | **≈ Rp 2,5–4,5 jt/bln** | **≈ Rp 9–16 jt/bln** |

> Setahun penuh di Y3 ≈ Rp 110–190 jt — masih jauh di bawah alokasi PRD §14.3 (Rp 0,8 M/2028), menyisakan ruang untuk mobile app backend, environment tambahan, dan jasa managed.

---

## Changelog

| Versi | Tanggal | Perubahan |
|---|---|---|
| v1.0 | 2026-07-07 | Dokumen awal — baseline terukur, model beban, sizing 3 tier, checklist go-live. Ditulis sebagai lampiran detail PRD §10.5 |
