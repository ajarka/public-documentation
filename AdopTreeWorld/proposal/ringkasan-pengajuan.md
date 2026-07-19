# Ringkasan Pengajuan — Infrastruktur AdopTree World

> **Tanggal**: 11 Juli 2026 (revisi v1.6 — 18 Juli 2026: + ArcGIS · AccuWeather · DeepSeek · AI internal engineering, keputusan founder) · **Diajukan oleh**: Aditira Jamhuri (CTO)
> **Dokumen rujukan** (perhitungan lengkap & dapat diaudit): [kapasitas-sistem-staging-production.md](kapasitas-sistem-staging-production.md)
> **Model perhitungan interaktif** (Excel, formula hidup): [perhitungan-kapasitas-anggaran-adoptree.xlsx](perhitungan-kapasitas-anggaran-adoptree.xlsx)
> **Dokumen pasangan — SDM/manpower** (roster + anggaran menuju production, termasuk skenario on-premise): [anggaran-sdm-manpower.md](anggaran-sdm-manpower.md)

---

## Yang Diajukan

Platform AdopTree World sudah selesai dibangun dan teruji end-to-end — web, aplikasi Android lapangan (Build 32), dan pembayaran Midtrans — namun **belum memiliki server untuk beroperasi**. Diajukan pengadaan dua server dedicated + langganan software pendukung, provisioning **Agustus 2026** — prasyarat first paying merchant (Q3) dan public launch H2 2026:

1. **Server Staging** — environment review internal & UAT: setiap fitur diperiksa stakeholder sebelum dirilis
2. **Server Production** — melayani donor, merchant/NGO, dan korporasi CSR sungguhan di `adoptreeworld.com`, terisolasi penuh dari environment uji, dengan backup harian dan komitmen pemulihan tertulis

---

## Berapa Biayanya

| Fase | Periode | Biaya per bulan |
| --- | --- | --- |
| **Launch** (hingga 2.000 akun) | Agustus – Desember 2026 | **Rp 6,7 juta** |
| **Growth** (hingga 25 rb akun) | Januari – Juli 2027 | **Rp 10,1 juta** |

<br>

> ## Persetujuan yang diminta: **plafon Rp 125 juta** untuk 12 bulan pertama
>
> Realisasi diproyeksikan **≈ Rp 114 juta**. Biaya setup/one-time: **Rp 0** (dikerjakan internal; lisensi ArcGIS dibayar tahunan di muka ≈ Rp 11–19 jt dan sudah termasuk plafon). Angka plafon sudah mencakup kedua server, storage **seluruh konten media** (foto bukti lapangan, video, dokumen — dengan formula volume per item), seluruh langganan software — peta GIS web (Mapbox), **analisis GIS profesional (ArcGIS)**, **data cuaca per lahan (AccuWeather)**, **AI komersial Tira (DeepSeek — dipilih karena paling ekonomis) + AI internal engineering (Claude Code Max $200/bln — rekomendasi CTO)**, email, **Google Workspace (mailbox domain tim — seat mengikuti roster SDM)**, monitoring, CDN, domain — buffer 10%, **dan ruang skenario upgrade dini** (≈ Rp 121 juta) sehingga upgrade berjalan tanpa menunggu persetujuan ulang di tengah momentum. Empat langganan strategis tersebut adalah keputusan founder 18 Juli 2026 (revisi v1.6.2 — sebelumnya plafon Rp 50 juta; AI internal ditetapkan Claude Max $200/bln; + Google Workspace seat roster). Pemisahan server tahap lanjut (aplikasi ↔ database ↔ backup) sudah dipetakan per fase di dokumen rujukan — bukan biaya baru yang menyusul.

---

## Arah 3 Tahun (keputusan founder: cloud dulu → on-premise saat skala terbukti)

| Periode | Skema | Anggaran |
| --- | --- | --- |
| Tahun 1 | Cloud (proposal ini, termasuk langganan strategis) | **Plafon Rp 125 jt** |
| Tahun 2 | Cloud, naik mengikuti trigger metrik | ≈ Rp 245 jt |
| Tahun 3 | Transisi **on-premise**: CapEx server ≈ Rp 230 jt + migrasi | ≈ Rp 550 jt |
| Tahun 4+ | On-premise steady — **lebih hemat dari tetap cloud** | ≈ Rp 300 jt/th |

Total 3 tahun ≈ **Rp 920 jt** — ≈ 112% dari alokasi infrastruktur PRD §14.4 (≈ Rp 825 jt; selisih ≈ Rp 95 jt/3 th perlu revisi alokasi PRD +$6.000 atau ditutup dari sisa pos lain, mis. pos Team yang realisasinya 66–91% — diputuskan stakeholder); kenaikan dari 79% (v1.5) sepenuhnya berasal dari langganan strategis keputusan founder (Claude Max · ArcGIS · Google Workspace · AccuWeather) yang berjalan di semua skema. Kalkulasi lengkap (spesifikasi hardware, TCO, titik impas, kriteria eksekusi) di dokumen rujukan §10. **Yang dimintakan persetujuan hari ini tetap hanya plafon tahun pertama**; CapEx on-premise diputuskan pada gate evaluasi akhir tahun ke-2 dengan data pemakaian riil.

---

## Mengapa Angka Ini Wajar

| | |
| --- | --- |
| **≈ ⅘ dari satu deal CSR** | Satu tahun infrastruktur + seluruh langganan software (ArcGIS, AccuWeather, AI, Workspace) ≈ empat perlima nilai satu paket CSR korporasi rata-rata (PRD §7.2: $10.000 ≈ Rp 160 juta) |
| **≈ 66% dari alokasi anggaran** | Rencana anggaran (PRD §14.4) menyediakan $12.000 ≈ Rp 190 juta untuk infrastruktur 2027 — plafon ini ≈ 66%-nya (realisasi proyeksi ≈ 60%) |
| **Harga pasar, tanpa margin** | Spesifikasi dibandingkan dengan harga publik 4 provider (Indonesia & regional) — tabel pembanding ada di dokumen rujukan |
| **Kenaikan hanya berdasarkan bukti** | Kapasitas dinaikkan hanya saat metrik pemakaian melewati ambang tertulis — bukan berdasarkan jadwal, sehingga belanja tidak pernah mendahului kebutuhan |
| **Dihitung dari data terukur** | Kebutuhan server dihitung dari footprint aplikasi yang diukur langsung (backend Rust hanya 46 MB RAM), bukan perkiraan; biaya AI dibatasi cap harian; storage konten (foto/video) dianggarkan dengan formula volume — bahkan skenario media 5× tetap < 15% anggaran bulanan |
| **Jaga-jaga sudah termasuk** | Plafon memuat buffer 10% (fluktuasi kurs/harga/volume) dan kapasitas server disiapkan ≥ 5× beban puncak proyeksi; bila realisasi mendekati plafon, pengeluaran berikutnya kembali dimintakan persetujuan — anggaran tidak dapat terlampaui tanpa sepengetahuan stakeholder |

---

## Timeline

| Kapan | Apa |
| --- | --- |
| Minggu pertama Agustus | Persetujuan → provisioning kedua server (1–2 hari kerja) |
| Agustus | Setup, pengamanan, backup + uji pemulihan, uji beban terhadap server production |
| September | **Platform production siap menerima first paying merchant** |
| H2 2026 | Public launch — kampanye B2C + pilot CSR berjalan di production |

---

## Jika Ditunda

First paying merchant (target Q3 2026) **tidak memiliki environment production** untuk bertransaksi, dan review stakeholder kehilangan environment staging dedicated. Provisioning hanya butuh 1–2 hari kerja, namun pengamanan + uji pemulihan backup + uji beban membutuhkan sisa bulan Agustus — persetujuan di awal Agustus menjaga seluruh rangkaian menuju public launch H2 2026 tetap pada jadwal. Setiap bulan penundaan juga menunda bukti transaksi nyata yang dibutuhkan penggalangan dana (PRD §16).

---

## Persetujuan

| Peran | Nama | Tanggal | Tanda Tangan / Persetujuan |
| --- | --- | --- | --- |
| Founder & CEO | Sandhy Krisnamurthi | | |
| CTO (pengaju) | Aditira Jamhuri | 11 Juli 2026 | ✓ |

---

*Rincian teknis — spesifikasi server, inventaris software per layanan (termasuk semua yang Rp 0), metodologi perhitungan, komitmen pemulihan (RTO/RPO), pembanding harga provider, dan analisis skenario — tersedia di [dokumen rujukan](kapasitas-sistem-staging-production.md) untuk keperluan verifikasi.*
