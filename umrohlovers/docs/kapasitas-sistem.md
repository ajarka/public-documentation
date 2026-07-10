# 67 — Proposal Kebutuhan & Kapasitas Infrastruktur Sistem (Staging + Production)

> **Status**: v2.0 — Proposal untuk diajukan & disetujui stakeholder · 7 Juli 2026
> **Pemilik**: Tech Lead (Aditira Jamhuri)
> **Tujuan dokumen**: menjawab *"berapa sebenarnya kebutuhan sistem umrohlovers untuk production, dan berapa biayanya"* — dengan perhitungan yang dapat diaudit baris per baris. Setiap angka punya sumber: **terukur dari server**, **hasil uji beban**, atau **asumsi yang dinyatakan eksplisit** (dan bisa diganti tanpa merusak formula).
> **Prinsip sizing**: *right-sizing* — tidak kekurangan (headroom ≥ 5× beban puncak proyeksi + jalur scale-up < 1 hari), tidak berlebihan (utilisasi steady target 15–40%; upgrade dipicu **metrik**, bukan kalender).
> **Versi ringkas untuk investor**: PRD §10.5 ([PRD-UmrohLovers-Investor.md](../prd/PRD-UmrohLovers-Investor.md))

---

## Ringkasan Eksekutif — Yang Diajukan

### Tiga keputusan yang diminta

1. **Setujui provisioning VPS production dedicated** (4 vCPU / 8 GB, data center Indonesia) pada **Agustus 2026** — sebelum pilot September. Saat ini **production belum ada**; hanya staging yang hidup.
2. **Setujui anggaran infrastruktur 12 bulan pertama ≈ Rp 35–40 juta** (Agu 2026 – Jul 2027, semua layanan, sudah termasuk buffer 10%) — rincian di §8.
3. **Setujui prinsip upgrade berbasis trigger metrik** (§6): kapasitas dinaikkan saat ambang terukur terlampaui, bukan karena jadwal — ini yang menjaga anggaran tidak menggelembung sekaligus tidak pernah kekurangan.

### Anggaran yang diajukan (semua layanan, per bulan)

| Tahap | Periode | Infrastruktur | Biaya/bulan (all-in) |
|---|---|---|---|
| **Pilot** | Agu–Okt 2026 | 1× VPS dedicated 4 vCPU/8 GB + Cloudflare/R2 + monitoring | **Rp 0,6–1,0 jt** |
| **Launch Y1** | Nov 2026–2027 | App node 8 vCPU/16 GB + DB node terpisah + CDN Pro + monitoring | **Rp 2,5–4,5 jt** |
| **Scale Y3** | 2028 | HA penuh: 2× app + LB + managed Postgres HA + replica | **Rp 9–16 jt** |

**Sanity check**: total tahun ketiga (≈ Rp 110–190 jt/tahun) tetap jauh di bawah alokasi budget PRD §14.3 (Rp 300 jt di 2027, Rp 800 jt di 2028) — proposal ini **bukan** batas atas budget, melainkan kebutuhan riil terhitung.

---

## 1. Metodologi — Bagaimana Angka Dihitung

Empat langkah, masing-masing bisa diverifikasi:

| Langkah | Metode | Bagian |
|---|---|---|
| 1. Ukur baseline | Angka riil dari server hari ini (CPU/RAM/disk/DB) — bukan estimasi | §2 |
| 2. Modelkan beban | Formula eksplisit dari target bisnis PRD §13–14; setiap asumsi dinyatakan | §3 |
| 3. Petakan kapasitas komponen | Rule-of-thumb industri yang konservatif + **divalidasi silang dengan uji k6 milik sendiri** | §3.3 |
| 4. Sizing + headroom | Spesifikasi = beban proyeksi × faktor aman; ditolak jika utilisasi proyeksi < 10% (kemahalan) atau > 50% steady (kekurangan headroom) | §5 |

**Dua penjaga kewajaran** yang dipakai di seluruh dokumen:

- **Anti-kekurangan**: setiap tier harus (a) menampung ≥ 5× beban puncak proyeksi tahapnya, (b) punya jalur scale-up < 1 hari kerja, dan (c) memenuhi target RTO/RPO (§7.2) — karena ini platform yang memegang perjalanan ibadah & dana jamaah.
- **Anti-markup**: setiap spesifikasi dibandingkan dengan (a) harga publik ≥ 3 provider (§5.5), (b) opsi satu tingkat lebih murah — dan bila opsi murah ditolak, alasannya ditulis (§5.2), (c) utilisasi proyeksi dicantumkan supaya kelebihan kapasitas terlihat.

---

## 2. Baseline Terukur Hari Ini (7 Juli 2026)

Semua angka **diukur langsung dari server** (`docker stats`, `psql`, `free`, `df`):

### 2.1 Host staging — VPS shared `adoptree` (Indonesia)

| Resource | Kapasitas | Terpakai | Catatan |
|---|---|---|---|
| CPU | 2 vCPU | idle mayoritas waktu | Dibagi **3 project** (AdopTree, Ajarka, umrohlovers) — 14 container termasuk Jenkins, Postgres, Redis, nginx |
| RAM | 7,5 GiB | ~2,3 GiB | 5,2 GiB available |
| Disk | 99 GB | 21 GB (21%) | Docker images 7,2 GB reclaimable — prune rutin dijadwalkan |

### 2.2 Footprint aplikasi umrohlovers (staging)

| Komponen | Terpakai | Limit | Interpretasi |
|---|---|---|---|
| Frontend Next.js 15 | ~101 MiB RAM | 512 MiB | Ringan |
| Backend Go/Echo | **~46 MiB RAM** | 384 MiB | Sangat ringan — karakteristik Go |
| Database PostgreSQL | **21 MB** | shared | Data uji; produksi tumbuh terprediksi (§4.3) |
| CPU keduanya | ~0% idle | — | Beban CPU hanya muncul saat trafik |

### 2.3 Status production: **belum di-provision**

- Pipeline Jenkins production (dengan approval gate), domain `umrohlovers.id` + `api.umrohlovers.id` (Cloudflare Tunnel), dan port (FE `:3201`, BE `:8281`) **sudah siap** — tetapi container production **belum pernah di-deploy**.
- Konsekuensi: keputusan #1 (provision Agustus) adalah prasyarat pilot September.

### 2.4 Bukti kapasitas dari uji nyata (bukan teori)

| Bukti | Hasil | Artinya untuk sizing |
|---|---|---|
| **k6 load test 1.000 virtual users** — dijalankan di box shared 2 vCPU di atas | Lulus; bottleneck teridentifikasi: **pool koneksi DB (25)** | Aplikasi sudah terbukti melewati skala pilot **bahkan pada hardware paling kecil**; artinya spesifikasi Tier 1 (§5.2) punya margin besar, dan tuning pertama yang benar adalah pool DB — bukan beli server besar |
| Redis caching katalog | 18× cache-hit improvement | Mayoritas trafik publik (baca katalog) tidak menyentuh DB |
| Lighthouse Perf 92+ (gzip aktif) | halaman ~1–1,5 MB | Bandwidth per kunjungan terkendali; CDN meng-cache aset |

---

## 3. Model Beban — Asumsi Eksplisit

### 3.1 Formula

```
DAU               = 10% × akun terdaftar           (asumsi engagement platform komunitas)
Concurrent peak   = 10% × DAU                      (jam sibuk malam/akhir pekan)
Request/user      = ~6 request/menit saat aktif    (SPA: navigasi + API) = 0,1 RPS/user
RPS nominal       = concurrent × 0,1
RPS target sizing = RPS nominal × spike 10×        (broadcast WA/radio, campaign, Munas)
```

> Setiap koefisien bisa diperdebatkan — yang penting **dinyatakan**. Bila realita pilot menunjukkan engagement 2× lebih tinggi, formula tinggal dijalankan ulang; §7.1 menunjukkan bahkan meleset 2× pun tidak mengubah spesifikasi tier.

### 3.2 Hasil per tahap (anchor volume = target bisnis PRD §13–14)

| Tahap | Akun | DAU | Concurrent peak | RPS nominal | **RPS target sizing (×10)** |
|---|---|---|---|---|---|
| **Pilot** (Sep–Nov 2026) | 500–2.000 | 50–200 | 20–40 | 2–4 | **~50** |
| **Launch Y1** (Nov 2026–2027) | 20–50 rb | 2–5 rb | 200–500 | 20–50 | **250–500** |
| **Scale Y3** (2028) | 100–150 rb | 10–15 rb | 1.000–1.500 | 100–150 | **1.000–1.500** |

### 3.3 Kapasitas komponen (rule-of-thumb konservatif, tervalidasi k6)

| Komponen | Kapasitas aman per unit | Yang membatasi |
|---|---|---|
| **Go/Echo BE** | ≥ 2.000 RPS/core (JSON + query ringan) | CPU — praktis bukan bottleneck sampai Y3 |
| **Next.js SSR** | 50–150 RPS/core halaman SSR | Komponen terboros CPU → mitigasi: **CDN Cloudflare men-cache halaman publik** (asumsi konservatif 70% offload) |
| **PostgreSQL 16** (4 vCPU NVMe) | 500–1.000 tx tulis/s; puluhan ribu read/s | Pool koneksi + IOPS; read sudah diserap Redis |
| **Redis 7** | 50.000+ ops/s | Bukan bottleneck di skala manapun rencana ini |

**Kesimpulan §3**: throughput **bukan** alasan membeli server besar — Go menangani ribuan RPS/core. Pendorong biaya yang sebenarnya adalah **availability** (uptime + keamanan data dana jamaah): isolasi, backup, replika. Maka struktur tier di §5 dibangun di atas kebutuhan availability, dengan throughput sebagai syarat minimum yang mudah terpenuhi.

---

## 4. Kebutuhan Storage & Bandwidth

### 4.1 Object storage — Cloudflare R2 (egress gratis)

| Item | Basis | Pilot (2 rb akun) | Y1 (50 rb) | Y3 (150 rb) |
|---|---|---|---|---|
| KYC 4 dokumen/akun | ~6 MB/akun | 12 GB | 300 GB | 900 GB |
| Cover paket, foto item mitra, avatar | — | ~2 GB | ~30 GB | ~100 GB |
| e-Visa PDF | ~0,5 MB/jamaah berangkat | ~0,3 GB | ~6 GB | ~25 GB |
| Backup DB (retensi 30 hari) | §4.3 | ~3 GB | ~50 GB | ~200 GB |
| **Total** | | **~17 GB** | **~390 GB** | **~1,2 TB** |
| **Biaya** ($0,015/GB/bln) | | **< Rp 10 rb/bln** | **~Rp 100 rb/bln** | **~Rp 300 rb/bln** |

R2 bukan faktor biaya di skala manapun — dan egress gratis berarti lonjakan trafik unduhan tidak menimbulkan tagihan kejutan.

### 4.2 Database

- Baseline 21 MB (data uji). Pertumbuhan: ~10–20 MB / 1.000 booking + **audit log UU PDP sebagai penyumbang terbesar** (~100 KB/booking efektif).
- Proyeksi ukuran: Pilot **< 1 GB** · Y1 **≤ 10 GB** · Y3 **≤ 60 GB** (dengan partisi + arsip `audit_logs` tahunan mulai Y1 akhir).
- Implikasi: kapasitas disk bukan masalah; yang dianggarkan adalah **IOPS NVMe + disiplin backup** (§7.2).

### 4.3 Bandwidth

- Y1: 5.000 DAU × 5 halaman × 1,5 MB ≈ 37 GB/hari ≈ 1,1 TB/bln **di edge** — mayoritas diserap cache Cloudflare (gratis); **origin cukup 100–300 GB/bln**. Pilih paket VPS dengan kuota ≥ 2 TB / unmetered lokal — tersedia standar di provider Indonesia.

---

## 5. Spesifikasi yang Diajukan per Tier

### 5.0 Tabel keputusan

| Env | Kapan | Spesifikasi | VPS/bln | All-in/bln | Status |
|---|---|---|---|---|---|
| **Staging** | tetap | Menumpang shared box existing | Rp 0 | Rp 0 | ✅ Cukup |
| **Prod Tier 1 — Pilot** | **Agustus 2026** | 1× VPS dedicated **4 vCPU / 8 GB / 160 GB NVMe**, Indonesia | Rp 400–800 rb | **Rp 0,6–1,0 jt** | 📋 Diajukan |
| **Prod Tier 2 — Launch Y1** | saat trigger §6 terpicu (perkiraan Okt–Nov 2026, pra-Munas) | App **8 vCPU / 16 GB** + **DB node terpisah 4 vCPU / 8 GB NVMe** | Rp 1,3–2,6 jt | **Rp 2,5–4,5 jt** | 📋 |
| **Prod Tier 3 — Scale Y3** | saat trigger terpicu (perkiraan 2027 akhir–2028) | 2× app node + LB + **managed Postgres HA + read replica** + Redis dedicated | Rp 5–10 jt | **Rp 9–16 jt** | 📋 |

### 5.1 Staging — tidak minta apa-apa

Footprint total ~150 MB RAM dan DB 21 MB — shared box existing cukup permanen. Biaya marginal **Rp 0**. Tindakan minor tanpa biaya: prune Docker terjadwal; pool DB dinaikkan saat uji beban ulang.

### 5.2 Production Tier 1 — Pilot (Agustus 2026)

**Diajukan: 1× VPS dedicated 4 vCPU / 8 GB / 160 GB NVMe, data center Indonesia** (UU PDP data residency), semua container milik sendiri:

| Container | RAM limit | Konfigurasi kunci |
|---|---|---|
| FE Next.js | 1 GB | Halaman publik → cache CDN Cloudflare |
| BE Go (+ worker H-30/H-14) | 512 MB | **Pool DB 25 → 40** (tuning hasil k6) |
| PostgreSQL 16 dedicated | 2 GB | `shared_buffers` 512 MB; **WAL archiving ke R2 tiap 15 menit + basebackup harian** |
| Redis 7 dedicated | 256 MB | `maxmemory` + `allkeys-lru` |
| nginx + cloudflared | 128 MB | Pola staging yang sudah terbukti |
| Headroom OS + spike | ~4 GB | |

**Bukti tidak kekurangan** — proyeksi utilisasi terhadap beban pilot:

| Metrik | Beban pilot (puncak, spike ×10) | Kapasitas box | Utilisasi proyeksi |
|---|---|---|---|
| RPS total | ~50 | ribuan (BE) / ratusan pasca-CDN (FE) | **< 15% CPU** |
| RAM | ~2,5 GB teralokasi | 8 GB | ~50% (dengan limit) |
| DB tx/s | < 5 tulis/s | ratusan | **< 5%** |
| Pembanding empiris | — | k6 1.000 VUs lulus di box **setengah spec ini yang dibagi 3 project** | margin sangat besar |

Box ini bahkan sanggup menampung beban **awal Y1** — upgrade ke Tier 2 dipicu metrik, bukan tanggal.

**Bukti tidak berlebihan** — opsi yang dipertimbangkan dan alasannya:

| Opsi | Biaya/bln | Keputusan | Alasan |
|---|---|---|---|
| Menumpang shared box existing (Rp 0) | Rp 0 | ❌ Ditolak | Uang jamaah riil: noisy-neighbor dengan 2 project lain, blast-radius insiden lintas project, audit UU PDP/PP 38 tidak bersih. Penghematan Rp 600 rb tidak sebanding |
| VPS 2 vCPU / 4 GB (Rp 250–350 rb) | −Rp 300 rb | ❌ Ditolak | Postgres dedicated 2 GB + stack + OS = RAM mepet tanpa headroom spike; gagal syarat anti-kekurangan (a) |
| **VPS 4 vCPU / 8 GB** | **Rp 400–800 rb** | ✅ **Diajukan** | Titik seimbang: isolasi penuh + headroom ≥ 5× + masih murah |
| VPS 8 vCPU / 16 GB (Rp 0,9–1,3 jt) | +Rp 500 rb | ❌ Belum | Utilisasi proyeksi < 8% = bayar kapasitas nganggur; resize vertical < 1 jam tersedia kapan pun trigger terpicu |
| Cloud managed penuh / Kubernetes | ≥ Rp 3–5 jt | ❌ Belum | Kompleksitas + biaya tidak sepadan untuk 1 box; dipertimbangkan lagi di Tier 3 |

### 5.3 Production Tier 2 — Launch Y1

Perubahan tunggal paling berdampak: **pisahkan database ke node sendiri** (menjawab bottleneck k6) — app node naik ke 8 vCPU/16 GB, DB node 4 vCPU/8 GB NVMe. Tambahan wajib tier ini: PgBouncer, Cloudflare Pro (~Rp 330 rb — WAF + cache analytics), uptime monitor eksternal, dan **drill restore backup bulanan** (backup yang tidak pernah diuji = tidak ada backup).

Kapasitas hasil: spike Munas 250–500 RPS tertampung dengan cache hangat pada utilisasi ~30–40% — lolos syarat headroom.

### 5.4 Production Tier 3 — Scale Y3

Pendorongnya **availability**, bukan throughput: 2× app node + load balancer, **managed PostgreSQL HA + 1 read replica** (leaderboard/laporan pindah ke replica), Redis node sendiri, observability penuh (APM + log terpusat), SLO 99,9%. Mulai tier ini managed DB dibayar karena failover otomatis + PITR yang dikelola vendor lebih murah daripada 1 SRE.

### 5.5 Pembanding harga pasar (bukti bebas markup)

Harga publik per Juli 2026 (⚠️ verifikasi ulang saat pengadaan — harga cloud berubah):

| Provider | Spec setara Tier 1 (4 vCPU/8 GB) | Harga/bln | Catatan |
|---|---|---|---|
| IDCloudHost (ID) | Cloud VPS pay-as-you-go | ≈ Rp 400–600 rb | Ekstrapolasi dari harga publik 2 vCPU/2 GB Rp 87–149 rb; lokasi Indonesia ✓ |
| Biznet Gio (ID) | NEO Lite/VPS setara | ≈ Rp 400–600 rb | Lokasi Indonesia ✓ |
| DigitalOcean (SGP) | Basic 4 vCPU/8 GB $48 · GP dedicated $63 | ≈ Rp 780 rb–1 jt | Kualitas & tooling bagus; data PII keluar RI → hanya cadangan |
| Vultr (SGP) | 4 vCPU/8 GB | ≈ $40–48 (Rp 650–780 rb) | idem |

Rentang proposal **Rp 400–800 rb** = harga pasar apa adanya, tanpa margin. Preferensi: provider Indonesia (data residency UU PDP + latency).

---

## 6. Trigger Upgrade Berbasis Metrik (pengendali anggaran)

Kapasitas **tidak** dinaikkan karena jadwal — hanya saat ambang berikut terlampaui (dipantau via Sentry + uptime monitor + `docker stats`/node exporter):

| Metrik | Ambang | Aksi |
|---|---|---|
| CPU host | > 60% sustained 30 menit (di luar deploy/backup) | Resize vertical → evaluasi tier berikutnya |
| RAM host | > 75% steady | Resize |
| Latensi API p95 | > 400 ms selama 1 jam | Investigasi (cache/query) → scale bila struktural |
| Pool DB | wait event / pemakaian > 70% | Naikkan pool → PgBouncer → DB node terpisah |
| Disk | > 70% | Expand volume |
| Error rate 5xx | > 0,5% berkelanjutan | Insiden — runbook §9 |

Dua konsekuensi: anggaran tidak menggelembung mendahului kebutuhan, dan keputusan upgrade selalu punya bukti tertulis (metrik) — bukan perasaan.

---

## 7. Uji Ketahanan Rencana (what-if)

### 7.1 Bagaimana kalau estimasi meleset?

| Skenario | Dampak | Jawaban |
|---|---|---|
| Engagement 2× asumsi (DAU 20%) | RPS proyeksi ×2 | Masih < 30% utilisasi Tier 1; tidak mengubah spesifikasi |
| **Spike Munas 20×** nominal (≈ 1.000 RPS sesaat di Y1) | Puncak ekstrem | Cloudflare men-cache katalog publik (mayoritas trafik) → origin hanya menerima API dinamis ~200–300 RPS → BE Go sanggup; DB terlindungi Redis. Lapis kedua: rate-limit per-IP di nginx. Lapis ketiga: resize vertical < 1 jam |
| Pertumbuhan akun 2× lebih cepat | Storage & DB naik | R2 & disk punya margin ≥ 3×; trigger §6 menaikkan tier lebih awal — anggaran naik mengikuti *revenue yang juga naik* |
| Pilot mundur | Biaya idle | Tier 1 hanya Rp 0,6–1 jt/bln; bisa ditunda provisioning-nya (lead time 1–2 hari kerja) |

### 7.2 Bagaimana kalau server mati? (RTO/RPO — komitmen pemulihan)

| Tier | RPO (maks data hilang) | RTO (maks waktu pulih) | Mekanisme |
|---|---|---|---|
| 1 — Pilot | **≤ 15 menit** | **≤ 4 jam** | WAL archiving ke R2 tiap 15 mnt + basebackup harian; rebuild dari compose + restore — **diuji sebelum go-live, bukan setelah insiden** |
| 2 — Y1 | ≤ 5 menit | ≤ 1 jam | DB node terpisah + streaming archive; app stateless tinggal redeploy |
| 3 — Y3 | ≈ 0 | ≤ 15 menit | Managed HA failover otomatis + replica |

Target SLO: 99,5% (pilot) → 99,8% (Y1) → 99,9% (Y3). Single point of failure di Tier 1 **diterima secara sadar** untuk fase pilot karena RPO/RTO di atas memadai untuk volume 500–2.000 akun — dan dihilangkan bertahap justru saat volumenya membenarkan biayanya.

---

## 8. Anggaran Diajukan — Rincian 12 Bulan Pertama

### 8.1 Biaya bulanan per komponen

| Komponen | Pilot (Agu–Okt) | Launch Y1 (Nov→) | Scale Y3 (2028) |
|---|---|---|---|
| VPS production | Rp 400–800 rb | Rp 1,3–2,6 jt | Rp 5–10 jt |
| Staging | Rp 0 (existing) | Rp 0 | Rp 0–500 rb (pisah bila perlu) |
| Cloudflare (Tunnel/CDN/WAF) + R2 | ~Rp 30 rb | ~Rp 450 rb (Pro + R2) | ~Rp 700 rb |
| Monitoring (Sentry + uptime) | Rp 0 (free tier) | ~Rp 400 rb | ~Rp 1 jt |
| Email (Resend) + Mapbox | Rp 0–150 rb | ~Rp 300–800 rb | ~Rp 1–2 jt |
| **Total/bulan** | **Rp 0,6–1,0 jt** | **Rp 2,5–4,5 jt** | **Rp 9–16 jt** |

### 8.2 Proyeksi 12 bulan (Agustus 2026 – Juli 2027)

| Pos | Perhitungan | Jumlah |
|---|---|---|
| Pilot, 3 bulan (Agu–Okt) | 3 × ~Rp 800 rb (titik tengah) | Rp 2,4 jt |
| Launch Y1, 9 bulan (Nov–Jul) | 9 × ~Rp 3,3 jt (titik tengah) | Rp 29,7 jt |
| One-time: hardening, drill restore, k6 ulang production | jam kerja internal + Rp 0 lisensi | Rp 0 |
| Buffer 10% (fluktuasi kurs/harga) | | Rp 3,2 jt |
| **Total 12 bulan pertama** | | **≈ Rp 35–40 jt** |

> **Konteks**: ≈ 1–2 booking umroh (AOV Rp 30 jt) menutup infrastruktur setahun penuh. Dibanding alokasi PRD §14.3 (infra 2026: Rp 50 jt; 2027: Rp 300 jt), proposal ini memakai **< 15% ruang budget 2027** — sisanya tetap tersedia untuk mobile app, environment tambahan, dan kebutuhan tak terduga, **tanpa perlu diajukan sekarang**.

---

## 9. Checklist Go-Live Production (Tier 1, target Agustus 2026)

- [ ] Provision VPS 4 vCPU/8 GB Indonesia + hardening (SSH key-only, ufw, fail2ban, unattended security update)
- [ ] Compose production (`kopsimari-haji-prod`) + **secrets baru** (JWT, DB password, HMAC — tidak reuse staging)
- [ ] Postgres dedicated: migrasi goose dari nol + seed minimal (settings, zona, BPS)
- [ ] Cloudflare Tunnel production → `umrohlovers.id`, `api.umrohlovers.id`
- [ ] Jenkins job production diarahkan ke host baru (approval gate tetap)
- [ ] WAL archiving 15-menit + basebackup harian ke R2 + **uji restore penuh** (gate go-live — bukan opsional)
- [ ] Sentry env `production` + uptime monitor eksternal + alert Discord
- [ ] k6 ulang **terhadap box production**: gate hijau 500 VUs, pool 40
- [ ] DNS cutover + smoke test E2E (auth, KYC, booking mock, akad PDF)
- [ ] Runbook insiden 1 halaman: restart, rollback image, restore DB, kontak eskalasi

---

## Appendix A — Daftar Asumsi (untuk diaudit/diganti)

| # | Asumsi | Nilai | Sensitivitas bila meleset |
|---|---|---|---|
| A1 | DAU = 10% akun | 10% | ×2 → tetap < 30% utilisasi Tier 1 (§7.1) |
| A2 | Concurrent = 10% DAU | 10% | idem |
| A3 | 6 req/menit/user aktif | 0,1 RPS | idem |
| A4 | Faktor spike | 10× | Munas 20× dianalisis terpisah (§7.1) |
| A5 | CDN offload halaman publik | 70% | Konservatif; realita biasanya > 85% untuk katalog |
| A6 | KYC 6 MB/akun | 4 dok | ×2 → R2 Y3 tetap < Rp 600 rb/bln |
| A7 | Kurs USD | Rp 16.000 | Buffer 10% di §8.2 |
| A8 | Harga provider | per Jul 2026 | **Wajib verifikasi ulang saat pengadaan** |

## Appendix B — Sumber Harga Publik

- IDCloudHost — halaman pricing publik (Cloud VPS pay-as-you-go; 2 vCPU/2 GB Rp 87–149 rb/bln sebagai anchor ekstrapolasi): https://idcloudhost.com/pricing/
- DigitalOcean — Droplet pricing (Basic 4 vCPU/8 GB ≈ $48/bln; General Purpose mulai $63/bln): https://www.digitalocean.com/pricing/droplets
- Cloudflare R2 ($0,015/GB/bln, egress $0) & Cloudflare Pro ($20/bln): halaman pricing publik Cloudflare
- Sentry Team (~$26/bln), Resend Pro (~$20/bln): halaman pricing publik masing-masing

---

## Changelog

| Versi | Tanggal | Perubahan |
|---|---|---|
| v2.0 | 2026-07-07 | Ditulis ulang sebagai proposal-grade: ringkasan eksekutif dengan 3 keputusan + anggaran 12 bulan, metodologi auditable, bukti anti-kekurangan (utilisasi proyeksi + RTO/RPO/SLO) & anti-markup (tabel opsi ditolak + pembanding harga 4 provider), trigger upgrade berbasis metrik, analisis what-if, appendix asumsi & sensitivitas |
| v1.0 | 2026-07-07 | Dokumen awal — baseline terukur, model beban, sizing 3 tier, checklist go-live |
