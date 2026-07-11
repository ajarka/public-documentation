# Ringkasan Pengajuan — Infrastruktur umrohlovers

> **Tanggal**: 7 Juli 2026 · **Diajukan oleh**: Aditira Jamhuri (CTO)
> **Dokumen rujukan** (perhitungan lengkap & dapat diaudit): [kapasitas-sistem-staging-production.md](kapasitas-sistem-staging-production.md) · **Model perhitungan Excel** (formula interaktif): [perhitungan-kapasitas-anggaran.xlsx](perhitungan-kapasitas-anggaran.xlsx)

---

## Yang Diajukan

Platform umrohlovers sudah selesai dibangun dan teruji, namun **belum memiliki server untuk beroperasi**. Diajukan pengadaan dua server dedicated + langganan software pendukung, provisioning **Agustus 2026** — prasyarat pilot September:

1. **Server Staging** — environment review internal & UAT: setiap fitur diperiksa stakeholder di `staging.umrohlovers.id` sebelum dirilis
2. **Server Production** — melayani jamaah & mitra sungguhan di `umrohlovers.id`, terisolasi penuh dari environment uji, dengan backup harian dan komitmen pemulihan tertulis

---

## Berapa Biayanya

| Fase | Periode | Biaya per bulan |
| --- | --- | --- |
| **Pilot** (50–500 jamaah) | Agustus – Oktober 2026 | **Rp 1 juta** |
| **Launch** (pasca-Munas, hingga 50 rb akun) | November 2026 – Juli 2027 | **Rp 3,6 juta** |

<br>

> ## Persetujuan yang diminta: **plafon Rp 45 juta** untuk 12 bulan pertama
>
> Realisasi diproyeksikan **≈ Rp 40 juta**. Biaya setup/one-time: **Rp 0** (dikerjakan internal). Angka plafon sudah mencakup kedua server, seluruh langganan software (peta, email, monitoring, CDN, storage), dan buffer 10%.

---

## Mengapa Angka Ini Wajar

| | |
| --- | --- |
| **Setara 1–2 booking umroh** | Satu tahun infrastruktur penuh ≈ nilai 1–2 transaksi (AOV Rp 30 juta) |
| **≈ 15% dari alokasi anggaran** | Rencana anggaran (PRD §14.3) menyediakan Rp 300 juta untuk infrastruktur 2027 — pengajuan ini memakai sekitar 15%-nya |
| **Harga pasar, tanpa margin** | Spesifikasi dibandingkan dengan harga publik 4 provider (Indonesia & regional) — tabel pembanding ada di dokumen rujukan |
| **Kenaikan hanya berdasarkan bukti** | Kapasitas dinaikkan hanya saat metrik pemakaian melewati ambang tertulis — bukan berdasarkan jadwal, sehingga belanja tidak pernah mendahului kebutuhan |
| **Sudah teruji sebelum diajukan** | Aplikasi lulus uji beban 1.000 pengguna simultan; kebutuhan server dihitung dari data terukur, bukan perkiraan |
| **Jaga-jaga sudah termasuk** | Plafon memuat buffer 10% (fluktuasi kurs/harga/volume) dan kapasitas server disiapkan ≥ 5× beban puncak proyeksi; bila realisasi mendekati plafon, pengeluaran berikutnya kembali dimintakan persetujuan — anggaran tidak dapat terlampaui tanpa sepengetahuan stakeholder |

---

## Timeline

| Kapan | Apa |
| --- | --- |
| Minggu pertama Agustus | Persetujuan → provisioning kedua server (1–2 hari kerja) |
| Agustus | Setup, pengamanan, backup + uji pemulihan, uji beban ulang |
| September | **Pilot wave 1 (50 jamaah) berjalan di production** |
| November | Launch publik — Munas SI Surabaya |

---

## Jika Ditunda

Pilot September **tidak memiliki environment production** untuk berjalan, dan review stakeholder kehilangan environment staging dedicated. Provisioning hanya butuh 1–2 hari kerja, namun setup + uji pemulihan + uji beban membutuhkan sisa bulan Agustus — persetujuan di awal Agustus menjaga seluruh rangkaian menuju Munas November tetap pada jadwal.

---

## Persetujuan

| Peran | Nama | Tanggal | Tanda Tangan / Persetujuan |
| --- | --- | --- | --- |
| Founder & Chairman/CEO | Lukman | | |
| Founder & President | Sandhy Krisnamurthi | | |
| CTO (pengaju) | Aditira Jamhuri | 7 Juli 2026 | ✓ |

---

*Rincian teknis — spesifikasi server, inventaris software per layanan, metodologi perhitungan, komitmen pemulihan (RTO/RPO), pembanding harga provider, dan analisis skenario — tersedia di dokumen rujukan untuk keperluan verifikasi.*
