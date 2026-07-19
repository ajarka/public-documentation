# Proposal Kebutuhan & Anggaran SDM (Manpower) — AdopTree World Menuju Production

> 📄 **Dokumen pasangan dari proposal infrastruktur**: [ringkasan-pengajuan.md](ringkasan-pengajuan.md) (1 halaman) · [kapasitas-sistem-staging-production.md](kapasitas-sistem-staging-production.md) (rujukan teknis) · [perhitungan-kapasitas-anggaran-adoptree.xlsx](perhitungan-kapasitas-anggaran-adoptree.xlsx) (model interaktif — **sheet SDM**)
>
> **Status**: Proposal untuk diajukan & disetujui stakeholder
> **Tanggal**: 17 Juli 2026 · **Disusun oleh**: Aditira Jamhuri (CTO)
> **Permintaan founder (CKsan)**: *"hitung manpower untuk menjalankan ini semua — termasuk bila infra on-premise — sekaligus anggaran manpower lainnya untuk membawa AdopTree World ke level production."*
> **Tujuan dokumen**: menjawab *"berapa orang, peran apa, mulai kapan, dan berapa biayanya"* untuk mengoperasikan AdopTree World dari launch → growth → scale (termasuk skenario on-premise tahun ke-3) — dengan struktur yang sama dapat diauditnya seperti proposal infrastruktur: setiap angka adalah **asumsi eksplisit yang bisa diganti** di sheet Excel SDM tanpa merusak formula.

---

## Ringkasan Eksekutif — Yang Diajukan

Platform sudah selesai dibangun (40+ fase produk, PRD v2.9) oleh tim inti yang sangat kecil dengan pola kerja *AI-augmented engineering*. Untuk beroperasi di level production — melayani donor, merchant, korporasi CSR, dan jaringan lapangan sungguhan — dibutuhkan **roster minimum yang tumbuh mengikuti trigger metrik** (prinsip yang sama dengan proposal infrastruktur: penambahan orang mengikuti bukti beban, bukan kalender).

### Tiga keputusan yang diminta

1. **Setujui roster fase Launch (Agustus 2026)** — 4 posisi baru (di luar 3 founder): Fullstack Engineer, Ops & Support Admin, Koordinator Operasi Lapangan, BD/Merchant Onboarding — total biaya SDM fase launch **≈ Rp 82 juta/bulan** (sudah termasuk stipend founder + BPJS + THR).
2. **Setujui plafon anggaran SDM 12 bulan pertama Rp 1,5 miliar** (Agu 2026 – Jul 2027; realisasi proyeksi **≈ Rp 1,35 miliar** dengan penambahan bertahap 2027). Plafon ≈ **88% dari alokasi Team PRD §14.4** untuk periode yang sama — muat di dalam rencana anggaran yang sudah ada, tanpa pos baru.
3. **Setujui prinsip perekrutan berbasis trigger** (§5): posisi 2027–2028 di-list lengkap dengan trigger-nya masing-masing; eksekusi rekrutmen hanya saat trigger terpenuhi (jumlah akun/merchant/tiket support/beban lapangan) — bukan karena jadwal.

### Anggaran SDM per fase (titik tengah realistis)

| Fase | Periode | Headcount total (termasuk 3 founder) | Biaya SDM/bulan |
| --- | --- | --- | --- |
| **Launch** (≤ 2.000 akun, 30 merchant) | Agu – Des 2026 | 7 | **≈ Rp 82 jt** |
| **Growth Y1** (hingga 25 rb akun, 150 merchant) | 2027 (bertahap) | 9 → 14 | **≈ Rp 116 → 163 jt** (rata-rata ≈ Rp 146 jt) |
| **Scale Y2** (hingga 120 rb akun, 400 merchant + gate on-premise) | 2028 | 19 – 21 | **≈ Rp 300–320 jt** |

**Uji kewajaran**: PRD §14.4 mengalokasikan pos **Team (salaries + contractors)** $36.000 (≈ Rp 576 jt) untuk 2026, $120.000 (≈ Rp 1,92 M) untuk 2027, dan $360.000 (≈ Rp 5,76 M) untuk 2028 (kurs Rp 16.000 — asumsi A7 dokumen infrastruktur). Rencana ini terealisasi pada **± 71% (2026) · 91% (2027) · 66% (2028)** dari alokasi tersebut — konsisten dengan pola proposal infrastruktur (realisasi di bawah alokasi, sisa menjadi ruang manuver, bukan komitmen belanja).

**Cakupan**: murni **kompensasi SDM** (gaji + beban pemberi kerja + stipend founder). Sengaja **di luar cakupan** (pos anggaran lain yang sudah ada): (a) **insentif kontributor lapangan** per tugas (Tukar Poin — pos program insentif; model crowdsource dijelaskan §6, bukan hubungan kerja); (b) **komisi penjualan BD** (pos Marketing & Sales PRD §14.4); (c) **jasa profesional legal/audit** (pos Legal & Compliance PRD §14.4); (d) perangkat kerja & operasional kantor (dianggarkan terpisah bila dibutuhkan — tim remote-first).

---

## 1. Prinsip Organisasi — Mengapa Roster Ini Bisa Kecil

Empat karakteristik platform yang menekan kebutuhan headcount secara struktural — masing-masing sudah terbukti berjalan, bukan rencana:

| # | Karakteristik | Dampak ke SDM |
| --- | --- | --- |
| 1 | **AI-augmented engineering** — platform 40+ fase dibangun end-to-end oleh 1 CTO dengan co-engineering AI (PRD §2) | Kebutuhan engineer ≈ ⅓–¼ dari tim konvensional untuk cakupan yang sama; engineer baru difokuskan ke *bus factor* & on-call, bukan kapasitas fitur |
| 2 | **Tira-First Support 24/7** (PRD §6.9) — AI menjawab dengan data live lebih dulu; hanya kasus tak terselesaikan menjadi tiket | Deflection tiket menekan kebutuhan CS: 1 admin ops cukup untuk fase launch; CS kedua baru masuk saat volume tiket riil melewati ambang (§5) |
| 3 | **Kontributor lapangan crowdsource** (PRD §6.5) — inspeksi & penanaman oleh jaringan kontributor ber-tier dengan insentif per tugas + kendali mutu berlapis (watermark kamera, review queue, QR, evidence evaluator) | Lapangan **tidak digaji bulanan** — biaya mengikuti volume tugas (pos program). Yang dibutuhkan sebagai karyawan hanya **koordinator** (pelatihan, jadwal, QA bukti) |
| 4 | **Otomasi operasional built-in** — review queue, escrow milestone dengan double-gate, maker-checker keuangan, notifikasi menyeluruh, audit log | Proses back-office berjalan di sistem; admin ops bekerja *by exception* (antrian yang butuh keputusan manusia), bukan input data manual |

**Konsekuensi kebijakan**: penambahan orang diperlakukan seperti penambahan server — **berbasis trigger terukur** (§5), dengan daftar posisi + trigger disetujui di muka supaya eksekusi rekrutmen tidak menunggu persetujuan ulang.

---

## 2. Struktur Biaya per Karyawan — Basis Perhitungan

Semua angka gaji adalah **asumsi eksplisit** (benchmark pasar Indonesia 2026, mid-level, remote-first — sel kuning di sheet SDM Excel; ganti angkanya, seluruh dokumen menghitung ulang):

| Komponen beban pemberi kerja | Basis | Nilai |
| --- | --- | --- |
| BPJS Kesehatan (porsi pemberi kerja) | 4% dari gaji | +4% |
| BPJS Ketenagakerjaan (JKK 0,24% + JKM 0,3% + JHT 3,7% + JP 2%) | dari gaji | +6,24% |
| THR (Tunjangan Hari Raya) | 1 bulan gaji / 12 | +8,33% |
| Pembulatan cadangan (kenaikan tahunan, penggantian sakit/resign) | | ~+1,4% |
| **Faktor pengali total ("loaded cost")** | | **× 1,20** |

> Seluruh tabel biaya di dokumen ini sudah dalam **loaded cost** (gaji × 1,20), sehingga angka yang tampil = beban kas bulanan sesungguhnya, bukan gaji pokok.

**Stipend founder** (3 orang — CEO, CTO, C.Media): selama fase launch & growth diasumsikan **Rp 10 jt/bulan/founder** (jauh di bawah nilai pasar peran masing-masing — selisihnya adalah sweat equity), naik ke **Rp 20 jt mulai fase Scale 2028** — mengikuti pendapatan yang sudah terbukti (PRD memproyeksikan net revenue positif sejak 2027; kenaikan ditahan satu fase sebagai konservatisme kas). Ini **keputusan stakeholder** — angkanya sel kuning di Excel, bukan ketetapan dokumen ini.

---

## 3. Fase Launch (Agustus – Desember 2026) — Roster Minimum Production

Target fase (PRD §13): production live · first paying merchant (Q3) · 30 merchant · ≤ 2.000 akun · pilot CSR.

| # | Peran | Jml | Fungsi kritis (mengapa harus ada sejak launch) | Gaji/bln (asumsi) | Loaded/bln |
| --- | --- | --- | --- | --- | --- |
| F1–F3 | **3 Founder** (CEO · CTO · C.Media) | 3 | Strategi/BD/fundraise · arsitektur+delivery+DevOps · brand+konten | 3 × Rp 10 jt (stipend) | Rp 36,0 jt |
| L1 | **Fullstack Engineer** (mid) | 1 | *Bus factor* CTO (hari ini seluruh sistem bergantung 1 orang — risiko yang tidak boleh dibawa ke production); rilis, bugfix, on-call bergantian | Rp 14 jt | Rp 16,8 jt |
| L2 | **Ops & Support Admin** | 1 | Eskalasi Tira-first support, review queue bukti lapangan, administrasi escrow/payout (approval antrian keuangan), verifikasi KYC pengelola lahan | Rp 7 jt | Rp 8,4 jt |
| L3 | **Koordinator Operasi Lapangan** | 1 | Membangun & melatih jaringan kontributor (Field App), menjadwalkan inspeksi MRV, QA bukti — kredibilitas "verified reforestation" berdiri di peran ini | Rp 8 jt | Rp 9,6 jt |
| L4 | **BD / Merchant Onboarding** | 1 | Target 30 merchant + pilot CSR; pendampingan onboarding lahan (dibantu Tira Co-Pilot) | Rp 9 jt (+komisi dari pos S&M) | Rp 10,8 jt |
| | **Total fase Launch** | **7** | | | **≈ Rp 81,6 jt/bln** |

**Total 5 bulan (Agu–Des 2026): ≈ Rp 408 jt** — ≈ 71% alokasi Team PRD §14.4 tahun 2026 (Rp 576 jt), dengan catatan Jan–Jul 2026 tim berjalan tanpa pengeluaran gaji (sweat equity founder — nilai yang sudah "terbayar" dalam bentuk platform jadi).

**Yang sengaja TIDAK di-hire di fase launch** (dan mengapa): DevOps khusus (CTO + AI tooling masih menangani; masuk 2027 sebagai persiapan on-premise), CS kedua (menunggu data volume tiket riil), Finance khusus (volume transaksi launch masih tertangani admin ops + maker-checker), QA khusus (review AI + E2E otomatis masih memadai untuk kecepatan rilis fase ini).

---

## 4. Fase Growth 2027 — Penambahan Bertahap (9 → 14 orang)

Penambahan dieksekusi per kuartal **hanya saat trigger-nya terpenuhi** (§5). Urutan di bawah adalah proyeksi realistis; sheet SDM Excel menghitung ulang bila urutan digeser.

| # | Peran | Masuk (proyeksi) | Trigger perekrutan | Gaji/bln | Loaded/bln |
| --- | --- | --- | --- | --- | --- |
| G1 | **DevOps/SRE Engineer** | Q1 2027 | Production Tier 2 aktif (app+DB node terpisah) — juga **prasyarat jalur on-premise** §7: orang ini harus sudah 1,5–2 tahun mengoperasikan sistem sebelum memegang hardware sendiri | Rp 15 jt | Rp 18,0 jt |
| G2 | **Mobile Engineer (Flutter)** | Q1 2027 | Field App > 500 pengguna lapangan aktif / backlog rilis mobile > 1 sprint | Rp 14 jt | Rp 16,8 jt |
| G3 | **Customer Support** | Q2 2027 | Tiket pasca-deflection Tira > 15/hari sustained 1 bulan | Rp 6,5 jt | Rp 7,8 jt |
| G4 | **Koordinator Lapangan Regional #2** | Q2 2027 | > 60 lahan aktif / > 3 provinsi jarak jauh | Rp 8 jt | Rp 9,6 jt |
| G5 | **Finance & Admin** | Q2 2027 | Volume payout escrow > 100 transaksi/bulan (rekonsiliasi, pajak, laporan) | Rp 8 jt | Rp 9,6 jt |
| G6 | **BD #2** | Q3 2027 | Pipeline merchant > kapasitas BD #1 (> 10 onboarding aktif paralel) | Rp 9 jt | Rp 10,8 jt |
| G7 | **Content & Community** | Q3 2027 | Kampanye B2C penuh + forum/IG aktif melewati kapasitas C.Media | Rp 7 jt | Rp 8,4 jt |

**Lintasan biaya 2027**: awal tahun ≈ Rp 116 jt/bln (roster launch + G1–G2) → akhir tahun ≈ Rp 163 jt/bln (roster penuh 14 orang; stipend founder tetap Rp 10 jt — §2). **Total 2027 ≈ Rp 1,75 M ≈ 91% alokasi Team PRD** (Rp 1,92 M).

---

## 5. Prinsip Perekrutan Berbasis Trigger

Cermin dari §6 dokumen infrastruktur — kapasitas (orang maupun server) dinaikkan saat ambang terukur terlampaui:

| Sinyal terukur | Sumber data | Aksi |
| --- | --- | --- |
| Tiket support pasca-Tira > 15/hari (1 bulan) | Dashboard support inbox | Hire CS berikutnya |
| Antrian review bukti lapangan > 48 jam berulang | Review queue metrics | Hire/naikkan kapasitas koordinator lapangan |
| Backlog rilis > 1 sprint selama 2 sprint berturut | Papan sprint | Hire engineer berikutnya |
| Payout escrow > 100/bln | Konsol Keuangan | Hire Finance & Admin |
| > 10 onboarding merchant paralel per BD | Pipeline CRM | Hire BD berikutnya |
| Belanja cloud sustained > Rp 20 jt/bln 2 kuartal (gate §10.4 infra) | Tagihan bulanan | Aktifkan rencana SDM on-premise (§7) |

Setiap posisi di §4 dan §6 sudah membawa trigger-nya — sehingga persetujuan dokumen ini = persetujuan *daftar dan harganya*, sementara *waktunya* dikendalikan data.

---

## 6. Model Operasi Lapangan — Karyawan vs Kontributor (penting untuk memahami biaya)

Kredibilitas MRV AdopTree dibangun di lapangan, tetapi **lapangan bukan pos gaji**:

| Lapisan | Status | Kompensasi | Kendali mutu |
| --- | --- | --- | --- |
| **Koordinator Operasi Lapangan** (L3, G4, S…) | Karyawan | Gaji bulanan (dokumen ini) | KPI: cakupan inspeksi, SLA antrian bukti, kualitas jaringan |
| **Kontributor lapangan** (inspektor ber-tier, penanam) | Mitra crowdsource — **bukan** hubungan kerja | **Insentif per tugas** (Tukar Poin → pulsa/kuota; pos program insentif, di luar dokumen ini) | Watermark kamera anti-manipulasi · review queue · QR identitas pohon · evidence evaluator · tier system |
| **Pengelola lahan (PIC merchant)** | Personel merchant | Ditanggung merchant | KTP-verified (wajib), draft-until-complete |

Model ini yang membuat biaya lapangan **naik-turun mengikuti volume tugas** (elastis, seperti cloud) — bukan beban gaji tetap. Estimasi pos insentif kontributor dianggarkan bersama program Tukar Poin (di luar cakupan, §Cakupan) supaya tidak dobel hitung.

---

## 7. SDM Infrastruktur On-Premise (Tahun ke-3) — Selaras §10 Dokumen Infrastruktur

Permintaan founder secara spesifik: manpower **bila infra on-premise**. Jawabannya dirancang supaya **konsisten dengan §10.2 dokumen infrastruktur** (yang sudah memuat pos "SDM operasional/on-call 24/7 ±0,5 FTE = Rp 5–8 jt/bln") — **tanpa dobel hitung**:

| Aspek | Rencana |
| --- | --- |
| **Siapa yang memegang** | **G1 (DevOps/SRE, hire Q1 2027)** — dengan sengaja direkrut **2 tahun sebelum** gate on-premise supaya sudah hafal seluruh sistem di cloud sebelum memegang hardware sendiri; + **S1 (Infra/Platform Engineer #2, hire 2028)** untuk rotasi on-call yang manusiawi (SLO 99,9% tidak boleh bergantung 1 orang) |
| **Beban kerja on-premise** | Failover & penggantian hardware, patch OS/firmware, kapasitas & suku cadang, akses fisik DC colocation, uji pemulihan backup berkala — pekerjaan yang selama fase cloud dikerjakan vendor |
| **Alokasi FTE** | ± 0,5 FTE efektif dari gabungan G1+S1 (sisanya tetap mengerjakan CI/CD, monitoring, staging — pekerjaannya tidak hilang saat pindah on-premise) |
| **Akuntansi biaya** | Pos "SDM on-call Rp 5–8 jt/bln" pada §10.2 OpEx on-premise **= porsi 0,5 FTE dari gaji G1/S1 di dokumen ini** — saat konsolidasi anggaran tahunan, hitung **sekali** (di pos SDM). TCO on-premise §10.3 tidak berubah |
| **Kriteria §10.4 butir 2** ("tim memiliki kapasitas ops min. 0,5 FTE khusus") | **Terpenuhi by design** oleh roster ini — bukan rekrutmen mendadak saat migrasi |
| **Yang TIDAK dibutuhkan** | Teknisi jaga di lokasi 24/7 (DC colocation ISO 27001 menyediakan remote-hands), tim NOC terpisah (monitoring otomatis + rotasi on-call 2 orang memadai di skala ini) |

**Kesimpulan on-premise**: transisi tahun ke-3 **tidak menciptakan pos SDM baru** — ia dipikul oleh 2 engineer infra yang memang sudah ada di roster 2027–2028, dengan tambahan beban yang sudah dihargai di §10.2. Inilah alasan G1 masuk Q1 2027, bukan saat migrasi.

---

## 8. Fase Scale 2028 — Roster Lengkap (19–21 orang)

| # | Peran | Jml | Catatan | Gaji/bln | Loaded/bln |
| --- | --- | --- | --- | --- | --- |
| | Roster 2027 (14 orang, tanpa stipend) | 11 + 3F | dari §3–§4 | | Rp 126,6 jt |
| | Stipend founder (naik mulai fase Scale — §2) | 3 | 3 × Rp 20 jt | | Rp 72,0 jt |
| S1 | **Infra/Platform Engineer #2** | 1 | Rotasi on-call + eksekusi on-premise (§7) | Rp 15 jt | Rp 18,0 jt |
| S2 | **Backend Engineer** | 1 | Skala 120 rb akun; modul karbon/registri (arah SRUK/SPE-GRK) | Rp 16 jt | Rp 19,2 jt |
| S3 | **QA Engineer** | 1 | Regression suite formal menggantikan sebagian QA berbasis AI | Rp 10 jt | Rp 12,0 jt |
| S4 | **Corporate Account Manager** | 1 | Kelola akun Adop Lahan / ESG B2B (retensi & upsell CSR) | Rp 12 jt | Rp 14,4 jt |
| S5 | **CS #2** + **Koordinator Lapangan #3–4** | 3 | Mengikuti trigger volume (§5) | Rp 6,5 + 2×8 jt | Rp 27,0 jt |
| S6 | **Finance #2 / payroll** | 1 | Volume payout & kepatuhan pajak skala penuh | Rp 8 jt | Rp 9,6 jt |
| S7 | *(Opsional — trigger)* Data/ML Engineer | 0–1 | Bila estimasi ML & analitik karbon menjadi lini produk (SRUK/registri) | Rp 16 jt | Rp 19,2 jt |
| | **Total fase Scale** | **19–21** | | | **≈ Rp 300–320 jt/bln** |

**Total 2028 ≈ Rp 3,8 M ≈ 66% alokasi Team PRD** (Rp 5,76 M) — ruang tersisa untuk koreksi pasar gaji, insentif kinerja, atau percepatan yang diputuskan stakeholder.

---

## 9. Plafon & Plot Dana SDM 3 Tahun

| Periode | Roster | Anggaran (realistis) | % alokasi Team PRD §14.4 |
| --- | --- | --- | --- |
| **Agu–Des 2026** (Launch) | 7 | **≈ Rp 408 jt** | 71% dari alokasi 2026 |
| **2027** (Growth, bertahap 9→14) | 9 → 14 | **≈ Rp 1,75 M** | 91% dari alokasi 2027 |
| **2028** (Scale + gate on-premise) | 19–21 | **≈ Rp 3,8 M** | 66% dari alokasi 2028 |
| **Total 3 tahun** | | **≈ Rp 6,0 M** | **≈ 72% dari total alokasi Team 3 tahun (≈ Rp 8,26 M)** |

> ## Persetujuan yang diminta hari ini: **plafon SDM 12 bulan pertama Rp 1,5 miliar** (Agu 2026 – Jul 2027)
>
> Realisasi diproyeksikan **≈ Rp 1,35 miliar** (roster launch 5 bulan + penambahan bertahap semester pertama 2027 sesuai trigger). Plafon memuat buffer ≈ 10% untuk koreksi gaji pasar saat rekrutmen riil dan percepatan trigger. Angka tahun ke-2 dan ke-3 disajikan untuk kelengkapan plot dana proposal pendanaan (paralel dengan Plot Dana 3 Tahun infrastruktur §10.5) — **bukan** komitmen yang dimintakan tanda tangan hari ini.

**Gabungan plot dana 3 tahun (SDM + infrastruktur)** — untuk proposal pendanaan yang di-plot di awal (permintaan founder 12 Jul):

| Pos | Th-1 (Agu 26–Jul 27) | Th-2 (Agu 27–Jul 28) | Th-3 (Agu 28–Jul 29) | Total 3 th |
| --- | --- | --- | --- | --- |
| SDM (dokumen ini)* | ≈ Rp 1,35 M (plafon 1,5 M) | ≈ Rp 3,05 M | ≈ Rp 3,85 M | **≈ Rp 8,25 M** |
| Infrastruktur (§10.5 dok. infra, rev v1.6.3 — termasuk langganan strategis + VPS AI dedicated) | Rp 140 jt (plafon) | ≈ Rp 250 jt | ≈ Rp 550 jt | **≈ Rp 940 jt** |
| **Total SDM + Infra** | **≈ Rp 1,49 M** | **≈ Rp 3,30 M** | **≈ Rp 4,40 M** | **≈ Rp 9,19 M** |

<sub>\* Baris ini memakai periode tahun-berjalan proposal (Agu–Jul) agar sejalan §10.5 infra — karenanya totalnya (Rp 8,25 M) lebih besar dari total kalender 2026–2028 (Rp 6,0 M, tabel di atas): periode berjalan mencakup 7 bulan tambahan pada 2029 dengan roster skala penuh. Untuk **uji kewajaran vs PRD §14.4** dipakai angka kalender (408 jt · 1,75 M · 3,8 M = 72% alokasi), karena PRD berbasis tahun kalender. Rekonsiliasi keduanya ada di sheet SDM Excel.</sub>

---

## 10. Uji Kewajaran & Pembanding

| | |
| --- | --- |
| **Muat dalam alokasi yang ada** | Seluruh rencana ≈ 71–91% dari pos Team PRD §14.4 per tahun — tidak menambah pos anggaran, tidak melampaui rencana yang sudah dilihat investor |
| **Rasio SDM : pendapatan sehat** | PRD §14.4 memproyeksikan pendapatan 2027 $276 rb (net positif) — biaya Team 2027 (realisasi ≈ $109 rb) ≈ 40% revenue; 2028 (≈ $237 rb) ≈ 12% — lintasan yang menguat, bukan membengkak |
| **Benchmark gaji wajar** | Asumsi gaji mid-level pasar Indonesia 2026 remote-first; setiap angka sel kuning di Excel — bila pasar bergerak, ganti angkanya dan plafon menghitung ulang; buffer 10% menampung koreksi normal |
| **Headcount per skala** | 14 orang melayani 25 rb akun (2027) ≈ 1.800 akun/karyawan; 21 orang melayani 120 rb (2028) ≈ 5.700 akun/karyawan — rasio membaik karena otomasi (Tira, review queue, escrow) menyerap pertumbuhan |
| **On-premise tanpa pos baru** | Kriteria SDM §10.4 dokumen infra terpenuhi dari roster ini (§7) — migrasi tahun ke-3 tidak memicu rekrutmen darurat |

---

## Persetujuan

| Peran | Nama | Tanggal | Tanda Tangan / Persetujuan |
| --- | --- | --- | --- |
| Founder & CEO | Sandhy Krisnamurthi | | |
| CTO (pengaju) | Aditira Jamhuri | 17 Juli 2026 | ✓ |

---

## Changelog

| Versi | Tanggal | Perubahan |
| --- | --- | --- |
| v1.0 | 2026-07-17 | Dokumen pertama — permintaan founder (hitung manpower termasuk skenario on-premise + anggaran SDM menuju production): roster 3 fase berbasis trigger, struktur loaded cost (BPJS+THR ×1,20), model lapangan crowdsource vs karyawan, SDM on-premise selaras §10 dok. infrastruktur (tanpa dobel hitung), plafon 12 bulan Rp 1,5 M, plot dana 3 tahun gabungan SDM+infra ≈ Rp 8,0 M, uji kewajaran vs pos Team PRD §14.4; sheet **SDM** ditambahkan ke Excel |
