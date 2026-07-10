# Proposal Kebutuhan & Kapasitas Infrastruktur Sistem (Staging + Production)

> **Status**: Proposal untuk diajukan & disetujui stakeholder
> **Tujuan dokumen**: menjawab *"berapa sebenarnya kebutuhan sistem umrohlovers untuk staging & production, dan berapa biayanya"* — dengan perhitungan yang dapat diaudit baris per baris. Setiap angka punya sumber: **terukur dari aplikasi**, **hasil uji beban**, atau **asumsi yang dinyatakan eksplisit** (dan bisa diganti tanpa merusak formula).
> **Prinsip sizing**: *right-sizing* — tidak kekurangan (headroom ≥ 5× beban puncak proyeksi + jalur scale-up < 1 hari), tidak berlebihan (utilisasi steady target 15–40%; upgrade dipicu **metrik**, bukan kalender).

---

## Ringkasan Eksekutif — Yang Diajukan

Platform umrohlovers saat ini **belum memiliki server dedicated** — baik untuk **staging** (environment review internal & UAT stakeholder sebelum rilis) maupun **production** (melayani jamaah sungguhan). Proposal ini mengajukan keduanya, dengan perhitungan kebutuhan yang diturunkan dari data terukur aplikasi dan target bisnis.

### Tiga keputusan yang diminta

1. **Setujui provisioning 2 (dua) VPS dedicated** pada **Agustus 2026** — sebelum pilot September:
   - **Staging** — 2 vCPU / 4 GB: environment review internal + UAT, auto-deploy dari branch `development` via CI/CD
   - **Production** — 4 vCPU / 8 GB, data center Indonesia, terisolasi penuh dari environment uji
2. **Setujui anggaran infrastruktur 12 bulan pertama ≈ Rp 38–42 juta** (Agu 2026 – Jul 2027, staging + production + semua layanan pendukung, sudah termasuk buffer 10%) — rincian di §8.
3. **Setujui prinsip upgrade berbasis trigger metrik** (§6): kapasitas dinaikkan saat ambang terukur terlampaui, bukan karena jadwal — ini yang menjaga anggaran tidak menggelembung sekaligus tidak pernah kekurangan.

### Anggaran yang diajukan (semua layanan, per bulan)

| Tahap               | Periode        | Infrastruktur                                                             | Biaya/bulan (all-in)     |
| ------------------- | -------------- | ------------------------------------------------------------------------- | ------------------------ |
| **Pilot**     | Agu–Okt 2026  | Staging 2 vCPU/4 GB + Production 4 vCPU/8 GB + Cloudflare/R2 + monitoring | **Rp 0,8–1,3 jt** |
| **Launch Y1** | Nov 2026–2027 | Staging + app node 8 vCPU/16 GB + DB node terpisah + CDN Pro + monitoring | **Rp 2,7–5,0 jt** |
| **Scale Y3**  | 2028           | Staging + HA penuh: 2× app + LB + managed Postgres HA + replica          | **Rp 9–16 jt**    |

**Sanity check**: total tahun ketiga (≈ Rp 110–190 jt/tahun) tetap jauh di bawah alokasi budget PRD §14.3 (Rp 300 jt di 2027, Rp 800 jt di 2028) — proposal ini **bukan** batas atas budget, melainkan kebutuhan riil terhitung.

**Cakupan**: proposal ini murni **biaya infrastruktur & layanan pendukungnya** (server, CDN, storage, monitoring, email, peta). Sengaja **di luar cakupan**: (a) SDM ops/engineering — sudah ada di pos tersendiri PRD §14.3; (b) kanal notifikasi berbayar masa depan seperti WhatsApp Business API — diajukan terpisah saat kanalnya diputuskan; (c) backend mobile app — headroom biayanya sudah tersedia di ruang budget yang tersisa, diajukan saat track-nya dimulai. Server fisik (on-premise/colocation) dievaluasi dan **tidak diperlukan** — lihat §5.2 dan catatan backup §4.1.

---

## 1. Metodologi — Bagaimana Angka Dihitung

Empat langkah, masing-masing bisa diverifikasi:

| Langkah                       | Metode                                                                                                                                 | Bagian |
| ----------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------ |
| 1. Ukur baseline              | Footprint riil aplikasi (RAM/CPU/DB) dari environment uji — bukan estimasi                                                            | §2    |
| 2. Modelkan beban             | Formula eksplisit dari target bisnis PRD §13–14; setiap asumsi dinyatakan                                                            | §3    |
| 3. Petakan kapasitas komponen | Rule-of-thumb industri yang konservatif +**divalidasi silang dengan uji k6 milik sendiri**                                       | §3.3  |
| 4. Sizing + headroom          | Spesifikasi = beban proyeksi × faktor aman; ditolak jika utilisasi proyeksi < 10% (kemahalan) atau > 50% steady (kekurangan headroom) | §5    |

**Dua penjaga kewajaran** yang dipakai di seluruh dokumen:

- **Anti-kekurangan**: setiap tier harus (a) menampung ≥ 5× beban puncak proyeksi tahapnya, (b) punya jalur scale-up < 1 hari kerja, dan (c) memenuhi target RTO/RPO (§7.2) — karena ini platform yang memegang perjalanan ibadah & dana jamaah.
- **Anti-markup**: setiap spesifikasi dibandingkan dengan (a) harga publik ≥ 3 provider (§5.5), (b) opsi satu tingkat lebih murah — dan bila opsi murah ditolak, alasannya ditulis (§5.1–5.2), (c) utilisasi proyeksi dicantumkan supaya kelebihan kapasitas terlihat.

---

## 2. Baseline Terukur (7 Juli 2026)

### 2.1 Footprint aplikasi — diukur langsung, bukan estimasi

Angka dari environment uji internal (`docker stats`, `psql`):

| Komponen               | RAM terpakai               | Interpretasi                             |
| ---------------------- | -------------------------- | ---------------------------------------- |
| Frontend Next.js 15    | ~101 MiB                   | Ringan                                   |
| Backend Go/Echo        | **~46 MiB**          | Sangat ringan — karakteristik Go        |
| Database PostgreSQL 16 | **21 MB** (data uji) | Pertumbuhan produksi terprediksi (§4.2) |
| CPU (FE + BE)          | ~0% saat idle              | Beban CPU hanya muncul mengikuti trafik  |

**Interpretasi**: total footprint aplikasi < 200 MB RAM. Platform ini sangat efisien — kebutuhan server ditentukan oleh **headroom untuk lonjakan trafik, database, dan keamanan data**, bukan oleh aplikasinya sendiri.

### 2.2 Status environment: belum ada server dedicated

| Environment          | Fungsi                                                                                                                     | Status hari ini                                    |
| -------------------- | -------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------- |
| **Staging**    | Review internal stakeholder, UAT fitur sebelum rilis, tempat uji beban — auto-deploy setiap push ke branch`development` | 📋**Diajukan** — belum ada server dedicated |
| **Production** | Melayani jamaah & mitra sungguhan di`umrohlovers.id`                                                                     | 📋**Diajukan** — belum pernah di-provision  |

Yang **sudah siap** dan tinggal diarahkan (tanpa biaya tambahan): pipeline CI/CD Jenkins (staging auto-deploy; production dengan approval gate), domain `umrohlovers.id` + `api.umrohlovers.id` + `staging.umrohlovers.id` (Cloudflare Tunnel), dan image Docker FE/BE yang sudah teruji.

### 2.3 Bukti kapasitas dari uji nyata (bukan teori)

| Bukti                                                                          | Hasil                                                            | Artinya untuk sizing                                                                                                                                                                                         |
| ------------------------------------------------------------------------------ | ---------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **k6 load test 1.000 virtual users** — dijalankan pada mesin uji 2 vCPU | Lulus; bottleneck teridentifikasi:**pool koneksi DB (25)** | Aplikasi terbukti melewati skala pilot**bahkan pada hardware minimum**; spesifikasi yang diajukan (§5) punya margin besar, dan tuning pertama yang benar adalah pool DB — bukan membeli server besar |
| Redis caching katalog                                                          | 18× cache-hit improvement                                       | Mayoritas trafik publik (baca katalog) tidak menyentuh DB                                                                                                                                                    |
| Lighthouse Perf 92+ (gzip aktif)                                               | halaman ~1–1,5 MB                                               | Bandwidth per kunjungan terkendali; CDN meng-cache aset                                                                                                                                                      |

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

| Tahap                                | Akun        | DAU       | Concurrent peak | RPS nominal | **RPS target sizing (×10)** |
| ------------------------------------ | ----------- | --------- | --------------- | ----------- | ---------------------------------- |
| **Pilot** (Sep–Nov 2026)      | 500–2.000  | 50–200   | 20–40          | 2–4        | **~50**                      |
| **Launch Y1** (Nov 2026–2027) | 20–50 rb   | 2–5 rb   | 200–500        | 20–50      | **250–500**                 |
| **Scale Y3** (2028)            | 100–150 rb | 10–15 rb | 1.000–1.500    | 100–150    | **1.000–1.500**             |

### 3.3 Kapasitas komponen (rule-of-thumb konservatif, tervalidasi k6)

| Komponen                              | Kapasitas aman per unit                    | Yang membatasi                                                                                                       |
| ------------------------------------- | ------------------------------------------ | -------------------------------------------------------------------------------------------------------------------- |
| **Go/Echo BE**                  | ≥ 2.000 RPS/core (JSON + query ringan)    | CPU — praktis bukan bottleneck sampai Y3                                                                            |
| **Next.js SSR**                 | 50–150 RPS/core halaman SSR               | Komponen terboros CPU → mitigasi:**CDN Cloudflare men-cache halaman publik** (asumsi konservatif 70% offload) |
| **PostgreSQL 16** (4 vCPU NVMe) | 500–1.000 tx tulis/s; puluhan ribu read/s | Pool koneksi + IOPS; read sudah diserap Redis                                                                        |
| **Redis 7**                     | 50.000+ ops/s                              | Bukan bottleneck di skala manapun rencana ini                                                                        |

**Kesimpulan §3**: throughput **bukan** alasan membeli server besar — Go menangani ribuan RPS/core. Pendorong biaya yang sebenarnya adalah **availability** (uptime + keamanan data dana jamaah): isolasi, backup, replika. Maka struktur tier di §5 dibangun di atas kebutuhan availability, dengan throughput sebagai syarat minimum yang mudah terpenuhi.

---

## 4. Kebutuhan Storage & Bandwidth

### 4.1 Object storage — Cloudflare R2 (egress gratis)

| Item                                 | Basis                    | Pilot (2 rb akun)        | Y1 (50 rb)               | Y3 (150 rb)              |
| ------------------------------------ | ------------------------ | ------------------------ | ------------------------ | ------------------------ |
| KYC 4 dokumen/akun                   | ~6 MB/akun               | 12 GB                    | 300 GB                   | 900 GB                   |
| Cover paket, foto item mitra, avatar | —                       | ~2 GB                    | ~30 GB                   | ~100 GB                  |
| e-Visa PDF                           | ~0,5 MB/jamaah berangkat | ~0,3 GB                  | ~6 GB                    | ~25 GB                   |
| Backup DB (retensi 30 hari)          | §4.2                    | ~3 GB                    | ~50 GB                   | ~200 GB                  |
| **Total**                      |                          | **~17 GB**         | **~390 GB**        | **~1,2 TB**        |
| **Biaya** ($0,015/GB/bln)      |                          | **< Rp 10 rb/bln** | **~Rp 100 rb/bln** | **~Rp 300 rb/bln** |

R2 bukan faktor biaya di skala manapun — dan egress gratis berarti lonjakan trafik unduhan tidak menimbulkan tagihan kejutan.

> **Catatan backup**: R2 sekaligus menjadi tujuan backup database (WAL + basebackup, §7.2) — object storage yang *off-site* dan tergeoreplikasi secara bawaan. **Server fisik khusus backup tidak diperlukan** dan justru lebih buruk: satu lokasi (risiko bencana lokal), perlu perawatan hardware, tanpa jaminan durability setara object storage.

### 4.2 Database

- Baseline 21 MB (data uji). Pertumbuhan: ~10–20 MB / 1.000 booking + **audit log UU PDP sebagai penyumbang terbesar** (~100 KB/booking efektif).
- Proyeksi ukuran: Pilot **< 1 GB** · Y1 **≤ 10 GB** · Y3 **≤ 60 GB** (dengan partisi + arsip `audit_logs` tahunan mulai Y1 akhir).
- Implikasi: kapasitas disk bukan masalah; yang dianggarkan adalah **IOPS NVMe + disiplin backup** (§7.2).

### 4.3 Bandwidth

- Y1: 5.000 DAU × 5 halaman × 1,5 MB ≈ 37 GB/hari ≈ 1,1 TB/bln **di edge** — mayoritas diserap cache Cloudflare (gratis); **origin cukup 100–300 GB/bln**. Pilih paket VPS dengan kuota ≥ 2 TB / unmetered lokal — tersedia standar di provider Indonesia.

---

## 5. Spesifikasi yang Diajukan per Tier

### 5.0 Tabel keputusan

| Env                                | Kapan                                                         | Spesifikasi                                                                       | VPS/bln        | Status      |
| ---------------------------------- | ------------------------------------------------------------- | --------------------------------------------------------------------------------- | -------------- | ----------- |
| **Staging dedicated**        | **Agustus 2026**                                        | 1× VPS**2 vCPU / 4 GB / 80 GB NVMe**                                       | Rp 150–350 rb | 📋 Diajukan |
| **Prod Tier 1 — Pilot**     | **Agustus 2026**                                        | 1× VPS dedicated**4 vCPU / 8 GB / 160 GB NVMe**, Indonesia                 | Rp 400–800 rb | 📋 Diajukan |
| **Prod Tier 2 — Launch Y1** | saat trigger §6 terpicu (perkiraan Okt–Nov 2026, pra-Munas) | App**8 vCPU / 16 GB** + **DB node terpisah 4 vCPU / 8 GB NVMe**       | Rp 1,3–2,6 jt | 📋          |
| **Prod Tier 3 — Scale Y3**  | saat trigger terpicu (perkiraan 2027 akhir–2028)             | 2× app node + LB +**managed Postgres HA + read replica** + Redis dedicated | Rp 5–10 jt    | 📋          |

### 5.1 Staging dedicated — environment review internal & UAT

**Diajukan: 1× VPS 2 vCPU / 4 GB / 80 GB NVMe** menjalankan stack lengkap milik sendiri (FE + BE + PostgreSQL + Redis + nginx/tunnel).

**Kenapa staging dedicated dibutuhkan (bukan kemewahan):**

| Fungsi                                  | Nilai bisnisnya                                                                                                                                     |
| --------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Review internal stakeholder**   | Setiap fitur dicek di`staging.umrohlovers.id` sebelum dirilis — siklus feedback stakeholder yang selama ini terbukti efektif berjalan terus      |
| **UAT & gate rilis**              | Tidak ada perubahan masuk production tanpa lolos staging (pipeline:`development` → auto-deploy staging → review → approval gate → production) |
| **Tempat uji beban & eksperimen** | k6/uji destruktif dijalankan di staging**tanpa risiko menyentuh data jamaah**                                                                 |
| **Isolasi data**                  | Data uji dan data pribadi jamaah tidak pernah berada di mesin yang sama — kebersihan audit UU PDP/PP 38                                            |

**Bukti tidak berlebihan**: footprint aplikasi hanya ~150 MB (§2.1); box 2 vCPU/4 GB memuat stack lengkap + Postgres + ruang build dengan nyaman. Opsi lebih murah (1 vCPU/1–2 GB, Rp 50–100 rb) **ditolak** karena tidak memuat Postgres + Redis + stack dengan topologi yang sama seperti production — dan staging yang topologinya berbeda membuat hasil review/UAT tidak valid. Opsi lebih mahal tidak diperlukan: staging tidak butuh headroom spike (penggunanya internal, belasan orang).

### 5.2 Production Tier 1 — Pilot (Agustus 2026)

**Diajukan: 1× VPS dedicated 4 vCPU / 8 GB / 160 GB NVMe, data center Indonesia** (UU PDP data residency), semua container milik sendiri:

| Container                  | RAM limit | Konfigurasi kunci                                                                          |
| -------------------------- | --------- | ------------------------------------------------------------------------------------------ |
| FE Next.js                 | 1 GB      | Halaman publik → cache CDN Cloudflare                                                     |
| BE Go (+ worker H-30/H-14) | 512 MB    | **Pool DB 25 → 40** (tuning hasil k6)                                               |
| PostgreSQL 16 dedicated    | 2 GB      | `shared_buffers` 512 MB; **WAL archiving ke R2 tiap 15 menit + basebackup harian** |
| Redis 7 dedicated          | 256 MB    | `maxmemory` + `allkeys-lru`                                                            |
| nginx + cloudflared        | 128 MB    | Pola yang sudah terbukti di pipeline CI/CD                                                 |
| Headroom OS + spike        | ~4 GB     |                                                                                            |

**Bukti tidak kekurangan** — proyeksi utilisasi terhadap beban pilot:

| Metrik             | Beban pilot (puncak, spike ×10) | Kapasitas box                                                | Utilisasi proyeksi  |
| ------------------ | -------------------------------- | ------------------------------------------------------------ | ------------------- |
| RPS total          | ~50                              | ribuan (BE) / ratusan pasca-CDN (FE)                         | **< 15% CPU** |
| RAM                | ~2,5 GB teralokasi               | 8 GB                                                         | ~50% (dengan limit) |
| DB tx/s            | < 5 tulis/s                      | ratusan                                                      | **< 5%**      |
| Pembanding empiris | —                               | k6 1.000 VUs lulus pada mesin uji 2 vCPU (setengah spec ini) | margin sangat besar |

Box ini bahkan sanggup menampung beban **awal Y1** — upgrade ke Tier 2 dipicu metrik, bukan tanggal.

**Bukti tidak berlebihan** — opsi yang dipertimbangkan dan alasannya:

| Opsi                                          | Biaya/bln                | Keputusan            | Alasan                                                                                                                                                                                      |
| --------------------------------------------- | ------------------------ | -------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1 VPS digabung staging + production           | −Rp 250 rb              | ❌ Ditolak           | Uang jamaah riil tidak boleh satu mesin dengan environment uji: uji beban/eksperimen bisa menjatuhkan production, dan audit UU PDP/PP 38 menuntut pemisahan data uji vs data pribadi jamaah |
| VPS production 2 vCPU / 4 GB (Rp 250–350 rb) | −Rp 300 rb              | ❌ Ditolak           | Postgres dedicated 2 GB + stack + OS = RAM mepet tanpa headroom spike; gagal syarat anti-kekurangan (a)                                                                                     |
| **VPS 4 vCPU / 8 GB**                   | **Rp 400–800 rb** | ✅**Diajukan** | Titik seimbang: isolasi penuh + headroom ≥ 5× + masih murah                                                                                                                               |
| VPS 8 vCPU / 16 GB (Rp 0,9–1,3 jt)           | +Rp 500 rb               | ❌ Belum             | Utilisasi proyeksi < 8% = bayar kapasitas nganggur; resize vertical < 1 jam tersedia kapan pun trigger terpicu                                                                              |
| Cloud managed penuh / Kubernetes              | ≥ Rp 3–5 jt            | ❌ Belum             | Kompleksitas + biaya tidak sepadan untuk 1 box; dipertimbangkan lagi di Tier 3                                                                                                              |
| Server fisik (colocation / on-premise) — termasuk untuk backup | CapEx Rp 30–80 jt + rak DC Rp 1–2 jt/bln | ❌ Ditolak | CapEx besar di muka tidak cocok untuk fase pre-revenue; pengadaan hardware berminggu-minggu bertentangan dengan prinsip upgrade-by-trigger (resize VPS < 1 jam); maintenance hardware jadi tanggungan sendiri; UU PDP data residency sudah terpenuhi via DC Indonesia bersertifikat (ISO 27001) — dan kebutuhan backup sudah terjawab lebih baik oleh R2 off-site (§4.1, §7.2). Dana jamaah dipegang bank partner, bukan platform — tidak ada tuntutan regulasi kontrol fisik di sisi kita |

### 5.3 Production Tier 2 — Launch Y1

Perubahan tunggal paling berdampak: **pisahkan database ke node sendiri** (menjawab bottleneck k6) — app node naik ke 8 vCPU/16 GB, DB node 4 vCPU/8 GB NVMe. Tambahan wajib tier ini: PgBouncer, Cloudflare Pro (~Rp 330 rb — WAF + cache analytics), uptime monitor eksternal, dan **drill restore backup bulanan** (backup yang tidak pernah diuji = tidak ada backup). Staging dedicated tetap berjalan apa adanya.

Kapasitas hasil: spike Munas 250–500 RPS tertampung dengan cache hangat pada utilisasi ~30–40% — lolos syarat headroom.

### 5.4 Production Tier 3 — Scale Y3

Pendorongnya **availability**, bukan throughput: 2× app node + load balancer, **managed PostgreSQL HA + 1 read replica** (leaderboard/laporan pindah ke replica), Redis node sendiri, observability penuh (APM + log terpusat), SLO 99,9%. Mulai tier ini managed DB dibayar karena failover otomatis + PITR yang dikelola vendor lebih murah daripada 1 SRE.

### 5.5 Pembanding harga pasar (bukti bebas markup)

Harga publik per Juli 2026 (⚠️ verifikasi ulang saat pengadaan — harga cloud berubah):

| Provider           | Spec staging (2 vCPU/4 GB)                           | Spec prod Tier 1 (4 vCPU/8 GB)                                 | Catatan                                                                       |
| ------------------ | ---------------------------------------------------- | -------------------------------------------------------------- | ----------------------------------------------------------------------------- |
| IDCloudHost (ID)   | ≈ Rp 175–300 rb                                    | ≈ Rp 400–600 rb                                              | Ekstrapolasi dari harga publik 2 vCPU/2 GB Rp 87–149 rb; lokasi Indonesia ✓ |
| Biznet Gio (ID)    | ≈ Rp 200–300 rb                                    | ≈ Rp 400–600 rb                                              | Lokasi Indonesia ✓                                                           |
| DigitalOcean (SGP) | $24–32 (Rp 390–520 rb) | $48–63 (Rp 780 rb–1 jt) | Kualitas & tooling bagus; data PII keluar RI → hanya cadangan |                                                                               |
| Vultr (SGP)        | $20–24 (Rp 330–390 rb) | $40–48 (Rp 650–780 rb)  | idem                                                           |                                                                               |

Rentang proposal (staging **Rp 150–350 rb**, production **Rp 400–800 rb**) = harga pasar apa adanya, tanpa margin. Preferensi: provider Indonesia (data residency UU PDP + latency).

---

## 6. Trigger Upgrade Berbasis Metrik (pengendali anggaran)

Kapasitas **tidak** dinaikkan karena jadwal — hanya saat ambang berikut terlampaui (dipantau via Sentry + uptime monitor + node metrics):

| Metrik          | Ambang                                           | Aksi                                               |
| --------------- | ------------------------------------------------ | -------------------------------------------------- |
| CPU host        | > 60% sustained 30 menit (di luar deploy/backup) | Resize vertical → evaluasi tier berikutnya        |
| RAM host        | > 75% steady                                     | Resize                                             |
| Latensi API p95 | > 400 ms selama 1 jam                            | Investigasi (cache/query) → scale bila struktural |
| Pool DB         | wait event / pemakaian > 70%                     | Naikkan pool → PgBouncer → DB node terpisah      |
| Disk            | > 70%                                            | Expand volume                                      |
| Error rate 5xx  | > 0,5% berkelanjutan                             | Insiden — runbook §9                             |

Dua konsekuensi: anggaran tidak menggelembung mendahului kebutuhan, dan keputusan upgrade selalu punya bukti tertulis (metrik) — bukan perasaan.

---

## 7. Uji Ketahanan Rencana (what-if)

### 7.1 Bagaimana kalau estimasi meleset?

| Skenario                                                       | Dampak            | Jawaban                                                                                                                                                                                                                          |
| -------------------------------------------------------------- | ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Engagement 2× asumsi (DAU 20%)                                | RPS proyeksi ×2  | Masih < 30% utilisasi Tier 1; tidak mengubah spesifikasi                                                                                                                                                                         |
| **Spike Munas 20×** nominal (≈ 1.000 RPS sesaat di Y1) | Puncak ekstrem    | Cloudflare men-cache katalog publik (mayoritas trafik) → origin hanya menerima API dinamis ~200–300 RPS → BE Go sanggup; DB terlindungi Redis. Lapis kedua: rate-limit per-IP di nginx. Lapis ketiga: resize vertical < 1 jam |
| Pertumbuhan akun 2× lebih cepat                               | Storage & DB naik | R2 & disk punya margin ≥ 3×; trigger §6 menaikkan tier lebih awal — anggaran naik mengikuti*revenue yang juga naik*                                                                                                        |
| Pilot mundur                                                   | Biaya idle        | Total staging + production pilot hanya Rp 0,8–1,3 jt/bln; provisioning bisa ditunda (lead time 1–2 hari kerja)                                                                                                                 |

### 7.2 Bagaimana kalau server mati? (RTO/RPO — komitmen pemulihan)

| Env/Tier        | RPO (maks data hilang) | RTO (maks waktu pulih) | Mekanisme                                                                                                                                    |
| --------------- | ---------------------- | ---------------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| Staging         | best-effort            | ≤ 1 hari              | Redeploy dari CI/CD + seed; tidak ada data jamaah                                                                                            |
| Prod 1 — Pilot | **≤ 15 menit**  | **≤ 4 jam**     | WAL archiving ke R2 tiap 15 mnt + basebackup harian; rebuild dari compose + restore —**diuji sebelum go-live, bukan setelah insiden** |
| Prod 2 — Y1    | ≤ 5 menit             | ≤ 1 jam               | DB node terpisah + streaming archive; app stateless tinggal redeploy                                                                         |
| Prod 3 — Y3    | ≈ 0                   | ≤ 15 menit            | Managed HA failover otomatis + replica                                                                                                       |

Target SLO production: 99,5% (pilot) → 99,8% (Y1) → 99,9% (Y3). Single point of failure di Tier 1 **diterima secara sadar** untuk fase pilot karena RPO/RTO di atas memadai untuk volume 500–2.000 akun — dan dihilangkan bertahap justru saat volumenya membenarkan biayanya.

---

## 8. Anggaran Diajukan — Rincian 12 Bulan Pertama

### 8.1 Biaya bulanan per komponen

| Komponen                         | Pilot (Agu–Okt)         | Launch Y1 (Nov→)        | Scale Y3 (2028)       |
| -------------------------------- | ------------------------ | ------------------------ | --------------------- |
| **VPS staging dedicated**  | Rp 150–350 rb           | Rp 150–350 rb           | Rp 250–500 rb        |
| **VPS production**         | Rp 400–800 rb           | Rp 1,3–2,6 jt           | Rp 5–10 jt           |
| Cloudflare (Tunnel/CDN/WAF) + R2 | ~Rp 30 rb                | ~Rp 450 rb (Pro + R2)    | ~Rp 700 rb            |
| Monitoring (Sentry + uptime)     | Rp 0 (free tier)         | ~Rp 400 rb               | ~Rp 1 jt              |
| Email (Resend) + Mapbox          | Rp 0–150 rb             | ~Rp 300–800 rb          | ~Rp 1–2 jt           |
| **Total/bulan**            | **Rp 0,8–1,3 jt** | **Rp 2,7–5,0 jt** | **Rp 9–16 jt** |

### 8.2 Proyeksi 12 bulan (Agustus 2026 – Juli 2027)

| Pos                                                               | Perhitungan                                   | Jumlah                    |
| ----------------------------------------------------------------- | --------------------------------------------- | ------------------------- |
| Staging dedicated, 12 bulan                                       | 12 × ~Rp 250 rb (titik tengah)               | Rp 3,0 jt                 |
| Production pilot, 3 bulan (Agu–Okt)                              | 3 × ~Rp 800 rb (titik tengah, VPS + layanan) | Rp 2,4 jt                 |
| Production launch Y1, 9 bulan (Nov–Jul)                          | 9 × ~Rp 3,4 jt (titik tengah)                | Rp 30,6 jt                |
| One-time: hardening, drill restore, k6 ulang production           | jam kerja internal + Rp 0 lisensi             | Rp 0                      |
| Buffer 10% (fluktuasi kurs/harga)                                 |                                               | Rp 3,6 jt                 |
| **Total 12 bulan pertama (staging + production + layanan)** |                                               | **≈ Rp 38–42 jt** |

> **Konteks**: ≈ 1–2 booking umroh (AOV Rp 30 jt) menutup infrastruktur setahun penuh — **sudah termasuk environment review internal (staging)**. Dibanding alokasi PRD §14.3 (infra 2026: Rp 50 jt; 2027: Rp 300 jt), proposal ini memakai **< 15% ruang budget 2027** — sisanya tetap tersedia untuk mobile app, environment tambahan, dan kebutuhan tak terduga, **tanpa perlu diajukan sekarang**.

---

## 9. Checklist Provisioning & Go-Live (target Agustus 2026)

### Staging dedicated

- [ ] Provision VPS 2 vCPU/4 GB + hardening dasar (SSH key-only, ufw, auto security update)
- [ ] Deploy stack lengkap (FE, BE, Postgres, Redis, nginx/tunnel) + seed data uji
- [ ] Arahkan `staging.umrohlovers.id` + pipeline Jenkins auto-deploy branch `development`
- [ ] Smoke test E2E + akses review untuk stakeholder

### Production (Tier 1)

- [ ] Provision VPS 4 vCPU/8 GB Indonesia + hardening (SSH key-only, ufw, fail2ban, unattended security update)
- [ ] Compose production + **secrets baru** (JWT, DB password, HMAC — tidak reuse staging)
- [ ] Postgres dedicated: migrasi goose dari nol + seed minimal (settings, zona, BPS)
- [ ] Cloudflare Tunnel production → `umrohlovers.id`, `api.umrohlovers.id`
- [ ] Jenkins job production diarahkan ke host baru (approval gate tetap)
- [ ] WAL archiving 15-menit + basebackup harian ke R2 + **uji restore penuh** (gate go-live — bukan opsional)
- [ ] Sentry env `production` + uptime monitor eksternal + alert
- [ ] k6 ulang **terhadap box production**: gate hijau 500 VUs, pool 40
- [ ] DNS cutover + smoke test E2E (auth, KYC, booking, akad PDF)
- [ ] Runbook insiden 1 halaman: restart, rollback image, restore DB, kontak eskalasi

---

## Appendix A — Daftar Asumsi (untuk diaudit/diganti)

| #  | Asumsi                     | Nilai        | Sensitivitas bila meleset                         |
| -- | -------------------------- | ------------ | ------------------------------------------------- |
| A1 | DAU = 10% akun             | 10%          | ×2 → tetap < 30% utilisasi Tier 1 (§7.1)       |
| A2 | Concurrent = 10% DAU       | 10%          | idem                                              |
| A3 | 6 req/menit/user aktif     | 0,1 RPS      | idem                                              |
| A4 | Faktor spike               | 10×         | Munas 20× dianalisis terpisah (§7.1)            |
| A5 | CDN offload halaman publik | 70%          | Konservatif; realita biasanya > 85% untuk katalog |
| A6 | KYC 6 MB/akun              | 4 dok        | ×2 → R2 Y3 tetap < Rp 600 rb/bln                |
| A7 | Kurs USD                   | Rp 16.000    | Buffer 10% di §8.2                               |
| A8 | Harga provider             | per Jul 2026 | **Wajib verifikasi ulang saat pengadaan**   |

## Appendix B — Sumber Harga Publik

- IDCloudHost — halaman pricing publik (Cloud VPS pay-as-you-go; 2 vCPU/2 GB Rp 87–149 rb/bln sebagai anchor ekstrapolasi): https://idcloudhost.com/pricing/
- DigitalOcean — Droplet pricing (Basic 4 vCPU/8 GB ≈ $48/bln; General Purpose mulai $63/bln): https://www.digitalocean.com/pricing/droplets
- Cloudflare R2 ($0,015/GB/bln, egress $0) & Cloudflare Pro ($20/bln): halaman pricing publik Cloudflare
- Sentry Team (~$26/bln), Resend Pro (~$20/bln): halaman pricing publik masing-masing

---

## Changelog

| Versi | Tanggal    | Perubahan                                                                                                                                                                                                                                                                                                                     |
| ----- | ---------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| v2.1  | 2026-07-07 | Scope diperluas: proposal kini mengajukan**staging dedicated** (environment review internal/UAT) + **production** sebagai dua server baru; anggaran 12 bulan ≈ Rp 38–42 jt; justifikasi staging, RTO/RPO staging, dan checklist provisioning staging ditambahkan; dokumen dipindah ke `umrohlovers/proposal/` |
| v2.0  | 2026-07-07 | Proposal-grade: ringkasan eksekutif dengan keputusan + anggaran 12 bulan, metodologi auditable, bukti anti-kekurangan (utilisasi proyeksi + RTO/RPO/SLO) & anti-markup (tabel opsi ditolak + pembanding harga 4 provider), trigger upgrade berbasis metrik, analisis what-if, appendix asumsi & sensitivitas                  |
| v1.0  | 2026-07-07 | Dokumen awal — baseline terukur, model beban, sizing 3 tier, checklist go-live                                                                                                                                                                                                                                               |
