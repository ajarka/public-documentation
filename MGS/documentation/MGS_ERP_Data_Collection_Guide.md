# Panduan Pengisian Data — MGS ERP System
## *Untuk Tim PT. Manata Gawi Sabumi*

> **Disiapkan oleh:** Tim Pengembang ERP  
> **Tanggal:** April 2026  
> **Tujuan:** Panduan pengisian template data awal untuk sistem ERP MGS

---

## Apa Ini?

Dokumen ini adalah panduan untuk membantu tim PT. MGS mengisi data awal yang dibutuhkan sebelum sistem ERP dibangun. Data yang Anda isi akan menjadi **database pertama** sistem ERP MGS dan sangat penting untuk kelancaran implementasi.

> **Tidak perlu khawatir soal teknis!** Cukup buka file `.csv` dengan **Microsoft Excel** atau **Google Sheets**, isi datanya, dan kirimkan kembali kepada kami.

---

## File Template yang Perlu Diisi

| No | File | Isi | Prioritas | Estimasi Waktu |
|---|---|---|---|---|
| 1 | `MGS_Data_Template_Projects.csv` | Daftar semua proyek (aktif & historis) | 🔴 **Wajib** | 1-2 jam |
| 2 | `MGS_Data_Template_Employees.csv` | Data seluruh karyawan & tenaga kerja | 🔴 **Wajib** | 2-3 jam |
| 3 | `MGS_Data_Template_Vendors.csv` | Daftar supplier & vendor rekanan | 🟡 **Penting** | 1 jam |
| 4 | `MGS_Data_Template_Assets.csv` | Daftar aset (kendaraan, alat berat, dll) | 🟡 **Penting** | 1 jam |

---

## Cara Membuka File CSV

### Dengan Microsoft Excel:
1. Buka Microsoft Excel
2. Klik **File → Open**
3. Pilih file `.csv` yang ingin dibuka
4. Jika muncul dialog "Text Import Wizard", pilih **Delimited → Next → Comma → Finish**
5. Isi data sesuai kolom yang ada

### Dengan Google Sheets:
1. Buka [sheets.google.com](https://sheets.google.com)
2. Klik **File → Import**
3. Upload file `.csv`
4. Pilih **Comma** sebagai separator
5. Isi data

---

## Panduan Per Template

### 1. Template Proyek (`MGS_Data_Template_Projects.csv`)

Isi **semua proyek** yang pernah dikerjakan MGS, baik yang sudah selesai maupun yang masih berjalan.

**Pertanyaan panduan:**
- Proyek apa saja yang sedang berjalan saat ini?
- Proyek apa saja yang sudah selesai dalam 2-3 tahun terakhir?
- Siapa klien utama MGS? (PLTU Asam-Asam, PLN, dll)
- Berapa nilai kontrak rata-rata per proyek?

**Kolom paling penting:**
- `NAMA PROYEK` — Nama proyek lengkap
- `NAMA KLIEN` — Nama perusahaan klien
- `TIPE PROYEK` — procurement / maintenance / construction / labor_supply / rental
- `STATUS` — active / completed / on_hold
- `NILAI KONTRAK` — Nilai dalam Rupiah (tanpa titik/koma)

---

### 2. Template Karyawan (`MGS_Data_Template_Employees.csv`)

Isi **semua karyawan** — baik karyawan tetap MGS maupun tenaga kerja yang disuplai ke klien.

**Pertanyaan panduan:**
- Berapa total karyawan MGS saat ini?
- Ada berapa tenaga kerja yang sedang ditempatkan di proyek klien?
- Posisi/jabatan apa saja yang ada di MGS?

**Kolom paling penting:**
- `NAMA LENGKAP` — Nama sesuai KTP
- `JABATAN` — Posisi/jabatan saat ini
- `TIPE KARYAWAN` — permanent / contract / outsource / freelance
- `STATUS` — active / on_project / resigned
- `TGL BERGABUNG` — Format YYYY-MM-DD

---

### 3. Template Vendor (`MGS_Data_Template_Vendors.csv`)

Isi daftar **semua supplier dan vendor** yang pernah atau sering digunakan MGS untuk pengadaan barang/jasa.

**Pertanyaan panduan:**
- Supplier material apa saja yang biasa digunakan?
- Vendor jasa apa saja (servis, overhaul, rental)?
- Ada berapa vendor/supplier aktif saat ini?

**Kolom paling penting:**
- `NAMA PERUSAHAAN` — Nama vendor/supplier
- `KATEGORI` — material / equipment / service / labor / vehicle
- `NAMA KONTAK` — Nama PIC di vendor
- `NO HP` — Nomor yang bisa dihubungi

---

### 4. Template Aset (`MGS_Data_Template_Assets.csv`)

Isi daftar **semua aset** milik MGS: kendaraan operasional, alat berat, peralatan teknis, dll.

> **Info:** Kami sudah mengisi 14 unit Toyota (dari serah terima November 2020) sebagai contoh. Mohon lengkapi nomor polisi dan detail lainnya.

**Pertanyaan panduan:**
- Berapa total kendaraan yang dimiliki MGS?
- Alat berat apa saja yang dimiliki atau dikelola MGS?
- Peralatan teknis apa saja yang digunakan di lapangan?

**Kolom paling penting:**
- `NAMA ASET` — Nama aset (contoh: Toyota Hiace 2020)
- `KATEGORI` — vehicle / heavy_equipment / tools
- `NO POLISI` — Untuk kendaraan bermotor
- `STATUS` — available / on_project / on_rent

---

## Yang Tidak Perlu Diisi (Opsional)

Kolom-kolom berikut **boleh dikosongkan** jika datanya tidak tersedia atau dirahasiakan:
- Gaji karyawan
- Nomor KTP / BPJS
- Nilai kontrak / budget (bisa diisi perkiraan)
- Rating vendor

Data ini bisa dilengkapi kemudian setelah sistem berjalan.

---

## Format Data Penting

| Data | Format | Contoh |
|---|---|---|
| Tanggal | YYYY-MM-DD | 2025-01-15 |
| Uang (Rupiah) | Angka tanpa titik/koma | 320000000 |
| Status | Huruf kecil, tanpa spasi | active |
| Rating | Angka 1-5 | 4 |

---

## Setelah Selesai

Setelah mengisi template, silakan:

1. **Simpan** file dalam format `.csv` atau `.xlsx`
2. **Kirimkan** ke tim pengembang via email atau WhatsApp
3. **Konfirmasi** via WhatsApp bahwa file sudah dikirim

Tim kami akan memverifikasi data dan menghubungi jika ada pertanyaan.

---

## Butuh Bantuan?

Jika ada kolom yang membingungkan atau pertanyaan seputar pengisian data, hubungi tim pengembang. Kami siap mendampingi proses pengisian data ini melalui **sesi video call atau kunjungan langsung** jika diperlukan.

---

*Terima kasih atas kepercayaan PT. Manata Gawi Sabumi. Data yang lengkap akan menghasilkan sistem ERP yang lebih akurat dan bermanfaat!*
