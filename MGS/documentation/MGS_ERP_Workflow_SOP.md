# MGS ERP — Workflow & SOP Documentation
## *Alur Kerja Operasional PT. Manata Gawi Sabumi*

> **Disiapkan oleh:** Tim Pengembang ERP  
> **Tanggal:** April 2026  
> **Tujuan:** Mendokumentasikan alur kerja (SOP) existing MGS sebagai basis desain workflow engine pada sistem ERP

---

## Mengapa Dokumen Ini Penting?

Sistem ERP MGS bukan sekadar database — melainkan **mesin penggerak operasional** yang mengikuti cara kerja nyata tim di lapangan. Tanpa SOP yang terdokumentasi dengan baik, modul *progress tracking*, *approval flow*, dan *monitoring real-time* tidak akan mencerminkan kenyataan.

Dokumen ini mencakup:
- Alur kerja **yang sudah diketahui** (dari Smart WFM & informasi MGS)
- **Gap / pertanyaan** yang masih perlu dikonfirmasi oleh tim MGS
- **Usulan desain flow** dalam sistem ERP

---

## Daftar Isi

1. [Modul Project Management — SOP Eksekusi Proyek](#1-modul-project-management--sop-eksekusi-proyek)
2. [Modul Procurement — Alur Pengadaan Barang/Jasa](#2-modul-procurement--alur-pengadaan-barangjasa)
3. [Modul HR & Employment Provider — Alur Penempatan SDM](#3-modul-hr--employment-provider--alur-penempatan-sdm)
4. [Modul Asset Management — Alur Pengelolaan Aset](#4-modul-asset-management--alur-pengelolaan-aset)
5. [Modul Finance & Billing — Alur Keuangan & Invoice](#5-modul-finance--billing--alur-keuangan--invoice)
6. [Cross-Module Integration Flow](#6-cross-module-integration-flow)
7. [Discovery Questions untuk MGS](#7-discovery-questions-untuk-mgs)

---

## 1. Modul Project Management — SOP Eksekusi Proyek

### 1.1 Overview Tipe Proyek MGS

MGS mengerjakan 5 tipe proyek dengan karakteristik yang berbeda:

| Tipe | Deskripsi | Contoh |
|---|---|---|
| **Procurement** | Pengadaan barang/material untuk klien | UPS PLTU Asam-Asam |
| **Maintenance** | Perawatan & perbaikan sistem/aset klien | Overhaul Generator, Panel MV |
| **Construction** | Pekerjaan konstruksi/instalasi | Instalasi panel, kabel, sipil |
| **Labor Supply** | Penyediaan tenaga kerja ke klien | SDM Teknik untuk PLN |
| **Rental** | Penyewaan alat berat/kendaraan | Alat berat ke tambang Adaro |

> Setiap tipe proyek memiliki **stage/tahapan yang berbeda**. SOP di bawah ini adalah pendekatan umum yang perlu dikonfirmasi dan disesuaikan per tipe.

---

### 1.2 Alur Utama Proyek (General Flow)

```mermaid
flowchart TD
    A([🟢 Proyek Masuk\nKontrak / SPK Diterima]) --> B[**STAGE 1**\nAanwijzing / Kickoff\nSurvey lapangan, scope review]
    B --> C{Ada kebutuhan\nmaterial/alat?}
    C -->|Ya| D[**STAGE 2**\nRequest Material\nPR dibuat, dikirim ke logistik]
    C -->|Tidak| E[**STAGE 3**\nMobilisasi Tim\nPenempatan personil]
    D --> PO[Procurement Flow\n→ Lihat Modul 2]
    PO --> E
    E --> F[**STAGE 4**\nPelaksanaan Pekerjaan\nProgress harian + foto]
    F --> G{Pekerjaan\nselesai?}
    G -->|Belum| F
    G -->|Selesai| H[**STAGE 5**\nTesting & Commissioning\nUji fungsi & performa]
    H --> I[**STAGE 6**\nAktualisasi Dokumen\nAs-built, laporan akhir]
    I --> J[**STAGE 7**\nUji Terima / BAST\nSign-off klien]
    J --> K[**STAGE 8**\nInvoicing & Closing\nTagih ke Finance]
    K --> L([🏁 Proyek Closed])

    style A fill:#22c55e,color:#fff
    style L fill:#3b82f6,color:#fff
    style PO fill:#f59e0b,color:#fff
    style D fill:#fef3c7
```

---

### 1.3 Detail Per Stage

#### STAGE 1 — Aanwijzing / Project Kickoff

**Tujuan:** Memastikan semua pihak memahami scope, timeline, dan persyaratan proyek.

| Item | Detail |
|---|---|
| **Trigger** | Kontrak/SPK ditandatangani oleh Direktur |
| **PIC** | Project Manager (Alexander Bobby / Boby / Lina) |
| **Aktivitas** | Survey lapangan, pertemuan kickoff dengan klien, review dokumen kontrak |
| **Output** | Berita Acara Kickoff, Jadwal Kerja, Daftar Kebutuhan Awal |
| **Evidence** | Foto survey lapangan, scan BA Kickoff |
| **Progress %** | 5% |

```
❓ GAP — Konfirmasi ke MGS:
- Apakah selalu ada survey lapangan sebelum mulai?
- Format Berita Acara Kickoff seperti apa yang digunakan?
- Siapa yang hadir dari sisi klien dan MGS?
```

---

#### STAGE 2 — Request Material / Material Planning

**Tujuan:** Mengidentifikasi dan mengajukan kebutuhan material/alat untuk proyek.

| Item | Detail |
|---|---|
| **Trigger** | PM membuat daftar kebutuhan setelah Kickoff |
| **PIC** | PM + Logistik |
| **Aktivitas** | Buat PR (Purchase Request), cek stok aset, tentukan vendor |
| **Output** | Material Request (MR) yang disetujui |
| **Evidence** | Dokumen PR, spesifikasi teknis |
| **Progress %** | 10% |

> **Catatan dari Smart WFM:** Stage ini disebut "Request Material" dan menjadi trigger proses procurement.

---

#### STAGE 3 — Mobilisasi Tim & Aset

**Tujuan:** Menempatkan personil dan aset ke lokasi proyek.

| Item | Detail |
|---|---|
| **Trigger** | Material/alat tersedia, PO sudah approved |
| **PIC** | HRD + Logistik |
| **Aktivitas** | Assign karyawan, dispatch kendaraan/alat, persiapan keberangkatan |
| **Output** | Surat Tugas, bukti keberangkatan |
| **Evidence** | Foto tim di lokasi, check-in GPS (jika ada) |
| **Progress %** | 15% |

```
❓ GAP — Konfirmasi ke MGS:
- Apakah ada Surat Tugas formal untuk setiap penugasan?
- Berapa rata-rata jumlah personil per proyek?
- Apakah kendaraan selalu ikut dikirim atau pakai transportasi lokal?
```

---

#### STAGE 4 — Pelaksanaan Pekerjaan (Daily Progress)

**Tujuan:** Eksekusi pekerjaan di lapangan dengan monitoring progress harian.

| Item | Detail |
|---|---|
| **Trigger** | Tim sudah di lokasi, material tersedia |
| **PIC** | Field Technician + PIC Lapangan |
| **Aktivitas** | Pekerjaan teknis harian, input laporan harian, upload foto |
| **Output** | Laporan Progress Harian, Foto Dokumentasi |
| **Evidence** | Foto before/during/after, laporan tertulis |
| **Progress %** | 20% → 85% (bertahap per hari) |

```mermaid
sequenceDiagram
    participant F as Field Tech
    participant PM as Project Manager
    participant D as Dashboard ERP
    participant K as Klien

    F->>D: Input Progress Harian\n(%, deskripsi, foto)
    D->>PM: Notifikasi update progress
    PM->>D: Review & validasi laporan
    D->>K: Share progress report (opsional)
    
    Note over F,D: Dilakukan setiap hari kerja
    Note over PM,D: PM bisa request foto tambahan jika progress tidak jelas
```

**Komponen Laporan Harian:**
- Tanggal & lokasi
- Deskripsi pekerjaan yang dilakukan
- Persentase progress (kumulatif)
- Kendala yang dihadapi
- Cuaca (untuk pekerjaan outdoor)
- Foto minimum 3 (area, detail pekerjaan, tim)
- Tanda tangan/paraf PIC

```
❓ GAP — Konfirmasi ke MGS:
- Laporan harian saat ini dalam format apa? (WA, Excel, kertas?)
- Siapa yang approve laporan harian — PM atau langsung klien?
- Apakah klien minta laporan mingguan atau bulanan juga?
- Berapa foto minimum yang biasa diupload per laporan?
- Apakah progress % dihitung per task atau per estimasi keseluruhan?
```

---

#### STAGE 5 — Testing & Commissioning

**Tujuan:** Uji fungsi sistem/instalasi sebelum diserahkan ke klien.

| Item | Detail |
|---|---|
| **Trigger** | Pekerjaan instalasi/konstruksi selesai 100% |
| **PIC** | Field Tech + Engineer + Perwakilan Klien |
| **Aktivitas** | Uji fungsi, uji beban, kalibrasi, trial run |
| **Output** | Berita Acara Testing, hasil measurement |
| **Evidence** | Foto hasil test, dokumen measurement sheet |
| **Progress %** | 90% |

> **Dari Smart WFM:** Disebut sebagai stage "Go Live"

---

#### STAGE 6 — Aktualisasi Dokumen

**Tujuan:** Melengkapi dokumentasi akhir proyek sesuai kondisi aktual.

| Item | Detail |
|---|---|
| **Trigger** | Testing berhasil |
| **PIC** | PM + Field Tech |
| **Aktivitas** | Buat as-built drawing, laporan akhir, inventory material sisa |
| **Output** | As-built dokumen, laporan final |
| **Evidence** | Scan dokumen, foto kondisi final |
| **Progress %** | 95% |

---

#### STAGE 7 — Uji Terima / BAST

**Tujuan:** Serah terima formal pekerjaan dari MGS ke klien.

| Item | Detail |
|---|---|
| **Trigger** | Semua dokumen lengkap, klien setuju |
| **PIC** | Direktur / PM + Perwakilan Klien |
| **Aktivitas** | Presentasi final, tanda tangan BAST |
| **Output** | **Berita Acara Serah Terima (BAST)** yang ditandatangani |
| **Evidence** | Scan BAST, foto serah terima |
| **Progress %** | 100% |

> **BAST adalah trigger penagihan invoice ke klien.**

```
❓ GAP — Konfirmasi ke MGS:
- BAST ditandatangani oleh siapa dari sisi MGS? Direktur atau PM?
- Berapa lama biasanya dari BAST sampai invoice dibayar klien?
- Apakah ada tahap retensi (pembayaran sebagian ditahan)?
```

---

#### STAGE 8 — Invoicing & Project Closing

**Tujuan:** Penagihan ke klien dan penutupan proyek secara sistem.

| Item | Detail |
|---|---|
| **Trigger** | BAST ditandatangani |
| **PIC** | Finance / Direktur |
| **Aktivitas** | Buat invoice, kirim ke klien, tracking pembayaran |
| **Output** | Invoice terkirim, konfirmasi pembayaran |
| **Progress %** | Project status → `completed` |

---

### 1.4 Progress Tracking Design

```mermaid
stateDiagram-v2
    [*] --> Draft : Kontrak diterima
    Draft --> Kickoff : PM assign
    Kickoff --> Material_Request : Survey selesai
    Material_Request --> Mobilisasi : MR approved & PO closed
    Mobilisasi --> Pelaksanaan : Tim di lokasi
    Pelaksanaan --> Testing : Progress 85%+
    Testing --> Aktualisasi : Test passed
    Aktualisasi --> BAST : Dok lengkap
    BAST --> Invoicing : BAST signed
    Invoicing --> [*] : Invoice paid

    Pelaksanaan --> Pelaksanaan : Daily update

    note right of Pelaksanaan
        Progress input harian
        Upload foto evidence
        Catat kendala
    end note
```

---

### 1.5 Perbedaan Flow per Tipe Proyek

| Stage | Procurement | Maintenance | Labor Supply | Rental |
|---|---|---|---|---|
| Aanwijzing | ✅ | ✅ | ✅ | ✅ |
| Request Material | ✅ Utama | ✅ Sebagian | ❌ | ❌ |
| Mobilisasi Tim | ✅ | ✅ | ✅ Utama | ❌ |
| Pelaksanaan Harian | ✅ | ✅ | ✅ Attendance | ✅ Logbook |
| Testing & Commissioning | ✅ | ✅ | ❌ | ❌ |
| Aktualisasi Dokumen | ✅ | ✅ | Laporan Bulanan | Laporan Sewa |
| BAST | ✅ | ✅ | Per periode | Per periode |
| Invoice | Per milestone | Satu kali | Bulanan | Bulanan |

---

## 2. Modul Procurement — Alur Pengadaan Barang/Jasa

### 2.1 Overview

Procurement di MGS dipicu oleh kebutuhan proyek — bukan pengadaan stok. Setiap PO harus terhubung ke proyek spesifik.

```mermaid
flowchart LR
    subgraph TRIGGER["📋 Kebutuhan"]
        PM[PM buat\nMaterial Request]
    end

    subgraph REVIEW["🔍 Review"]
        LOG[Logistik cek\nstok & vendor]
        DIR[Direktur / Finance\nApprove PR]
    end

    subgraph EXECUTION["⚙️ Eksekusi"]
        PO[Buat Purchase Order]
        VND[Kirim ke Vendor]
        DEL[Terima barang\n+ Berita Acara]
    end

    subgraph CLOSE["✅ Closing"]
        INV_VND[Invoice masuk\nfrom Vendor]
        PAY[Proses Pembayaran]
    end

    PM --> LOG --> DIR --> PO --> VND --> DEL --> INV_VND --> PAY

    style TRIGGER fill:#dbeafe
    style REVIEW fill:#fef3c7
    style EXECUTION fill:#dcfce7
    style CLOSE fill:#f3e8ff
```

### 2.2 Status Flow Purchase Order

```mermaid
stateDiagram-v2
    [*] --> Draft : PM buat MR
    Draft --> Submitted : Logistik submit
    Submitted --> Approved : Direktur/Finance approve
    Submitted --> Rejected : Ditolak → revisi
    Rejected --> Submitted : Revisi & resubmit
    Approved --> PO_Created : PO dibuat & dikirim vendor
    PO_Created --> Delivered : Barang diterima
    Delivered --> Closed : Invoice vendor dibayar
    Approved --> Cancelled : Dibatalkan
```

### 2.3 Detail Approval Matrix

| Nilai PO | Approver | SLA Approval |
|---|---|---|
| < Rp 5 juta | Logistik | 1 hari |
| Rp 5–50 juta | Finance Manager | 2 hari |
| > Rp 50 juta | Direktur | 3 hari |
| Sangat urgent | Direktur langsung | Same day |

```
❓ GAP — Konfirmasi ke MGS:
- Berapa threshold nilai PO yang butuh approve Direktur?
- Apakah PO harus selalu ada MR sebelumnya, atau bisa PO langsung?
- Siapa yang cek kualitas saat barang datang?
- Apakah ada vendor preferred / blacklist?
- Format PO yang saat ini digunakan seperti apa?
```

---

## 3. Modul HR & Employment Provider — Alur Penempatan SDM

### 3.1 Overview

MGS memiliki 2 tipe bisnis SDM:
1. **Internal HR** — karyawan tetap MGS yang mengerjakan proyek sendiri
2. **Employment Provider** — MGS menyuplai tenaga kerja ke klien (PLN, dll)

### 3.2 Alur Penempatan Karyawan ke Proyek

```mermaid
flowchart TD
    A[Project Won\nKontrak SDM] --> B[Manpower Planning\nBerapa orang, posisi apa]
    B --> C{Tersedia dari\nkaryawan existing?}
    C -->|Ya| D[Assign Internal\nKaryawan MGS]
    C -->|Sebagian| E[Rekrut Tambahan\nContract / Outsource]
    C -->|Tidak| E
    E --> F[Seleksi & Screening]
    F --> G[Approval Direktur]
    G --> H[Penempatan Resmi\nSurat Tugas + Kontrak]
    D --> H
    H --> I[Karyawan di Lokasi\nCheck-in & Attendance]
    I --> J[Monitoring Bulanan\nAbsensi + Kinerja]
    J --> K{Kontrak\nberakhir?}
    K -->|Tidak| J
    K -->|Ya| L[Demobilisasi\nLaporan Akhir SDM]
    L --> M[Invoice ke Klien\nPer periode / bulan]

    style A fill:#22c55e,color:#fff
    style M fill:#3b82f6,color:#fff
```

### 3.3 Attendance & Timesheet Flow

```mermaid
sequenceDiagram
    participant EMP as Karyawan
    participant PIC as PIC Lapangan
    participant HRD as HRD MGS
    participant FIN as Finance

    EMP->>PIC: Check-in harian (absensi)
    PIC->>EMP: Konfirmasi hadir
    
    Note over EMP,PIC: Daily — setiap hari kerja
    
    PIC->>HRD: Rekap absensi mingguan
    HRD->>HRD: Verifikasi & cek izin/sakit
    
    Note over PIC,HRD: Weekly
    
    HRD->>FIN: Timesheet bulanan\n(data untuk payroll)
    FIN->>FIN: Hitung gaji / billing klien
    FIN->>EMP: Transfer gaji
    FIN->>KLI: Invoice penempatan SDM
    
    Note over HRD,FIN: Monthly
```

### 3.4 Data Karyawan yang Dimonitor per Proyek

| Data | Frekuensi Update | PIC |
|---|---|---|
| Status kehadiran (hadir/izin/sakit) | Harian | PIC Lapangan |
| Posisi / penugasan | Per perubahan | HRD |
| Jam kerja / lembur | Harian | PIC Lapangan |
| Incident / kecelakaan | Real-time | PIC Lapangan |
| Evaluasi kinerja | Bulanan | PM / Klien |
| Status kontrak | Per perubahan | HRD |

```
❓ GAP — Konfirmasi ke MGS:
- Absensi saat ini dicatat bagaimana? (kertas, WA, aplikasi?)
- Apakah klien minta laporan absensi bulanan SDM yang ditempatkan?
- Bagaimana sistem perhitungan lembur?
- Apakah ada BPJS TK/Kes yang perlu dilacak per karyawan?
- Format kontrak kerja karyawan outsource seperti apa?
- Apakah ada kategori keterlambatan atau pelanggaran yang dilacak?
```

---

## 4. Modul Asset Management — Alur Pengelolaan Aset

### 4.1 Siklus Hidup Aset MGS

```mermaid
stateDiagram-v2
    [*] --> Available : Aset dibeli / diterima
    Available --> On_Project : Assigned ke proyek
    Available --> On_Rent : Disewakan ke klien
    On_Project --> Available : Proyek selesai, returned
    On_Rent --> Available : Sewa berakhir
    Available --> On_Maintenance : Jadwal servis
    On_Project --> On_Maintenance : Darurat / rusak
    On_Maintenance --> Available : Servis selesai
    On_Maintenance --> Retired : Total loss / tidak layak
    Available --> Retired : Umur ekonomis habis
    Retired --> [*]
```

### 4.2 Alur Penugasan Aset ke Proyek

```mermaid
flowchart LR
    A[PM Request\nAset untuk Proyek] --> B[Cek Ketersediaan\ndi Sistem]
    B --> C{Tersedia?}
    C -->|Ya| D[Buat Asset Assignment\nTanggal deploy]
    C -->|Tidak| E[Alternatif:\nSewa dari luar / tunda]
    D --> F[Dispatch Aset\nke Lokasi]
    F --> G[Penggunaan\ndi Proyek]
    G --> H[Return Aset\n+ Inspeksi Kondisi]
    H --> I{Kondisi OK?}
    I -->|Ya| J[Available kembali]
    I -->|Perlu servis| K[Jadwal Maintenance]
    K --> J
```

### 4.3 Monitoring Kendaraan Operasional

MGS memiliki **14 unit Toyota** (Veloz, Hiace, Hilux, Venturer) yang perlu dimonitor:

| Item Monitor | Frekuensi | Keterangan |
|---|---|---|
| Lokasi / penugasan | Per perubahan | Di proyek mana / siapa driver |
| KM / BBM | Per perjalanan | Logbook perjalanan |
| Servis berkala | Per 5.000 km / 3 bulan | Reminder otomatis |
| STNK / pajak | Tahunan | Alert 1 bulan sebelum jatuh tempo |
| Asuransi | Tahunan | Alert jatuh tempo |
| Kondisi fisik | Bulanan | Foto eksterior & interior |

```
❓ GAP — Konfirmasi ke MGS:
- Apakah ada logbook perjalanan kendaraan saat ini?
- Kendaraan diasuransikan dimana? Kapan jatuh tempo?
- Siapa yang bertanggung jawab atas kerusakan kendaraan di proyek?
- Apakah ada mekanisme booking kendaraan jika banyak proyek butuh bersamaan?
- Alat berat yang saat ini dimiliki MGS ada apa saja?
```

---

## 5. Modul Finance & Billing — Alur Keuangan & Invoice

### 5.1 Overview Alur Keuangan

MGS memiliki 2 arah cash flow:
- **Cash In (Piutang):** Invoice ke klien dari proyek
- **Cash Out (Hutang):** Bayar vendor/supplier dari PO

```mermaid
flowchart TB
    subgraph CASHIN["💰 Cash In — Piutang MGS"]
        direction LR
        BAST2[BAST\nditandatangani] --> INV_OUT[Buat Invoice\nke Klien]
        INV_OUT --> SEND[Kirim Invoice\n+ Dokumen Pendukung]
        SEND --> WAIT[Tunggu Pembayaran\n30-60 hari]
        WAIT --> REC[Payment\nDiterima]
        REC --> DONE_IN[✅ Lunas]
    end

    subgraph CASHOUT["💸 Cash Out — Hutang ke Vendor"]
        direction LR
        DEL2[Barang\nDiterima] --> INV_IN[Invoice\nmasuk dari Vendor]
        INV_IN --> VER[Verifikasi\n& Matching PO]
        VER --> PAY_APP[Approve\nPembayaran]
        PAY_APP --> TRF[Transfer ke\nRekening Vendor]
        TRF --> DONE_OUT[✅ Lunas]
    end

    style CASHIN fill:#dcfce7
    style CASHOUT fill:#fee2e2
```

### 5.2 Invoice Lifecycle — Cash In (Piutang)

```mermaid
stateDiagram-v2
    [*] --> Draft : BAST signed
    Draft --> Sent : Invoice dikirim ke klien
    Sent --> Partial : Pembayaran sebagian (termin)
    Sent --> Paid : Lunas langsung
    Partial --> Paid : Sisa lunas
    Sent --> Overdue : Jatuh tempo terlewat
    Overdue --> Paid : Setelah follow-up
    Draft --> Cancelled : Dibatalkan
    Paid --> [*]
    Cancelled --> [*]
```

### 5.3 Termin Pembayaran

Banyak proyek MGS menggunakan sistem termin (bertahap). Contoh umum:

| Termin | % Bayar | Trigger |
|---|---|---|
| DP (Down Payment) | 20–30% | Kontrak ditandatangani |
| Termin 1 | 30% | Progress 50% |
| Termin 2 | 30% | BAST ditandatangani |
| Retensi | 10% | 3–6 bulan setelah selesai |

```
❓ GAP — Konfirmasi ke MGS:
- Apakah semua proyek pakai sistem termin atau ada yang satu kali bayar?
- Berapa persen DP yang biasa diminta MGS?
- Apakah ada proyek dengan retensi (pembayaran ditahan)?
- Berapa lama payment term yang biasa disepakati? (30, 60, 90 hari?)
- Dokumen apa yang harus dilampirkan saat kirim invoice ke PLN/klien besar?
- Apakah MGS menggunakan faktur pajak (PPN)?
```

### 5.4 Budget vs Actual Tracking

```mermaid
flowchart LR
    A[Contract Value\nNilai Kontrak] -->|dibagi| B[Budget\nAllocation]
    B --> C[Procurement\nBudget]
    B --> D[HR / Labour\nBudget]
    B --> E[Asset / Rental\nBudget]
    B --> F[Overhead\nBudget]

    C -->|actual spent| G[Procurement\nActual]
    D -->|actual spent| H[HR\nActual]
    E -->|actual spent| I[Asset\nActual]
    F -->|actual spent| J[Overhead\nActual]

    G --> K[💹 Budget vs Actual\nper Proyek]
    H --> K
    I --> K
    J --> K
    K --> L[Gross Margin\nper Proyek]
```

---

## 6. Cross-Module Integration Flow

### 6.1 Bagaimana Semua Modul Terhubung

```mermaid
flowchart TD
    subgraph PROJ["📋 Project Management"]
        P1[Project Created]
        P2[Progress Harian]
        P3[BAST Signed]
    end

    subgraph PROC["🛒 Procurement"]
        PR1[Material Request]
        PR2[Purchase Order]
        PR3[Goods Received]
    end

    subgraph HR["👥 HR & Placement"]
        H1[Manpower Plan]
        H2[Attendance Harian]
        H3[Monthly Timesheet]
    end

    subgraph ASSET["🚛 Asset Management"]
        A1[Asset Assignment]
        A2[Usage Log]
        A3[Return & Inspect]
    end

    subgraph FIN["💰 Finance"]
        F1[Budget Plan]
        F2[Vendor Invoice]
        F3[Client Invoice]
        F4[Payment Track]
    end

    P1 --> PR1
    P1 --> H1
    P1 --> A1
    P1 --> F1

    PR1 --> PR2 --> PR3 --> F2
    H1 --> H2 --> H3 --> F3
    A1 --> A2 --> A3

    P2 -->|milestones| F3
    P3 -->|trigger| F3
    F3 --> F4

    style PROJ fill:#dbeafe
    style PROC fill:#fef3c7
    style HR fill:#dcfce7
    style ASSET fill:#f3e8ff
    style FIN fill:#fee2e2
```

### 6.2 Notifikasi & Alert Otomatis

| Event | Notifikasi Ke | Channel |
|---|---|---|
| PO submitted | Finance / Direktur | In-app + Email |
| Progress < target | PM | In-app |
| Invoice jatuh tempo | Finance | In-app + Email |
| Aset harus servis | Logistik | In-app |
| STNK hampir expired | Logistik | In-app + Email |
| Kontrak karyawan habis | HRD | In-app + Email |
| Project deadline approaching | PM | In-app |
| Pembayaran masuk | Finance, Direktur | In-app |

---

## 7. Discovery Questions untuk MGS

> Pertanyaan-pertanyaan berikut perlu dijawab oleh tim MGS sebelum pengembangan dimulai. Jawaban akan menentukan konfigurasi workflow engine pada ERP.

### 7.1 Project Management

- [ ] Tahapan proyek (stages) apa saja yang berlaku di MGS? Apakah sama untuk semua tipe proyek?
- [ ] Laporan progress harian saat ini dalam format apa dan dikirim ke siapa?
- [ ] Berapa foto minimum yang diupload per laporan harian?
- [ ] Siapa yang berhak mengubah status/stage proyek?
- [ ] Apakah ada SLA (target waktu penyelesaian) per stage?
- [ ] Bagaimana cara menghitung persentase progress? (per task, per waktu, manual estimasi?)

### 7.2 Procurement

- [ ] Siapa yang berhak membuat Material Request?
- [ ] Siapa yang approve PR sebelum jadi PO?
- [ ] Berapa nilai PO yang butuh tanda tangan Direktur?
- [ ] Apakah selalu ada 3 penawaran (3 vendor) untuk PO di atas nilai tertentu?
- [ ] Format PO yang ada sekarang seperti apa? Bisa dibagikan contohnya?

### 7.3 HR & Employment Provider

- [ ] Absensi karyawan saat ini dicatat bagaimana?
- [ ] Apakah klien minta laporan kehadiran SDM yang ditempatkan?
- [ ] Berapa hari kerja dalam seminggu untuk proyek lapangan?
- [ ] Apakah ada shift kerja? Jam kerja standar?
- [ ] Bagaimana cara menghitung lembur?
- [ ] Apakah ada uang makan / transport harian yang perlu dicatat?

### 7.4 Asset Management

- [ ] Siapa yang boleh menggunakan/dispatch kendaraan MGS?
- [ ] Apakah ada logbook perjalanan kendaraan yang diisi?
- [ ] Kendaraan diasuransikan? Oleh siapa? Jatuh temponya?
- [ ] Alat berat apa yang dimiliki MGS saat ini (merek, tahun, kapasitas)?
- [ ] Bagaimana prosedur jika kendaraan rusak saat di proyek?

### 7.5 Finance & Billing

- [ ] Apakah semua proyek pakai sistem termin atau ada yang lump sum?
- [ ] Dokumen apa saja yang harus dilampirkan saat penagihan ke PLN?
- [ ] MGS sudah PKP (Pengusaha Kena Pajak)? Apakah terbitkan faktur pajak?
- [ ] Siapa yang tanda tangan invoice keluar?
- [ ] Bagaimana MGS tracking apakah invoice sudah dibayar klien?
- [ ] Apakah ada proyek dengan pembayaran retensi?

---

## Lampiran — Mapping Stage ke Database Field

| Stage Proyek | `projects.status` | `project_tasks.status` | Trigger Notif |
|---|---|---|---|
| Kickoff | `active` | `in_progress` | PM assigned |
| Request Material | `active` | `in_progress` | MR created |
| Mobilisasi | `active` | `in_progress` | Asset assigned |
| Pelaksanaan | `active` | `in_progress` | Daily progress > 0% |
| Testing | `active` | `review` | Progress = 85%+ |
| Aktualisasi | `active` | `review` | Test passed |
| BAST | `active` | `done` | BAST uploaded |
| Invoicing | `active` | `done` | Invoice created |
| Closed | `completed` | `done` | Invoice paid |

---

*Dokumen ini akan terus diperbarui seiring proses discovery bersama tim PT. Manata Gawi Sabumi.*
