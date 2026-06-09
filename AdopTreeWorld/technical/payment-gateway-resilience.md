# AdopTree — Strategi Resilience Payment Gateway

> **Dokumen Resmi · Bahasa Non-Teknis**
> Versi: 1.0
> Tanggal: 9 Juni 2026
> Penyusun: Tim Engineering AdopTree
> Status: Draft Strategi — untuk review tim bisnis & operasional

---

## 1. Ringkasan Eksekutif

Saat ini transaksi pembayaran AdopTree (adopsi pohon, donasi, wakaf, langganan) ditangani sepenuhnya oleh **Midtrans** sebagai *single payment gateway*. Ini berfungsi dengan baik dalam kondisi normal, **namun menempatkan kelangsungan revenue platform sepenuhnya pada satu pihak eksternal**.

Dokumen ini menjelaskan strategi untuk **mengubah arsitektur pembayaran menjadi sistem berlapis (multi-gateway dengan fallback otomatis)**, di mana sistem akan terus menerima pembayaran meskipun salah satu penyedia mengalami gangguan.

Rencana awal:
- **Primary**: Amani Bank (rencana — sedang dalam tahap onboarding)
- **Fallback Tier 1**: Midtrans (yang sekarang sudah berjalan)
- **Fallback Tier 2+**: Slot terbuka untuk gateway tambahan (mis. Xendit, Doku, GoPay Direct) sesuai kebutuhan masa depan

> **Manfaat utama bagi bisnis**: Donor tidak pernah melihat halaman *"pembayaran gagal"* karena masalah di sisi gateway. Setiap rupiah transaksi punya **rute cadangan otomatis**.

---

## 2. Latar Belakang & Konteks Bisnis

### 2.1 Situasi Saat Ini

Setiap transaksi di AdopTree (mulai dari **Donasi Instan $8** hingga **AdopTree NFT $75/5 tahun**) saat ini melalui satu jalur:

```
Donor → Web AdopTree → Midtrans Snap → Bank/E-Wallet Donor → Konfirmasi
```

**Risiko yang dihadapi**:
| Skenario | Dampak | Frekuensi historis |
|---|---|---|
| Midtrans maintenance terjadwal | Semua transaksi tertunda (biasanya 1–4 jam) | Sekitar 2–4× per tahun |
| Midtrans outage tak terduga | Semua transaksi gagal sepenuhnya | Industri: 1–3× per tahun |
| Donor's bank issuer offline | Hanya bank tertentu yang gagal — donor lain tetap bisa | Sering, sporadis |
| Rate limit dari Midtrans di puncak kampanye | Donor mendapat error 429, akhirnya batal donasi | Saat trafik tinggi |

**Konsekuensi bisnis**: kehilangan revenue, donor churn (donor batal dan tidak kembali), dan kredibilitas platform turun saat kejadian outage menjadi publik.

### 2.2 Mengapa Strategi Multi-Gateway?

Dalam industri pembayaran digital di Indonesia, pendekatan **multi-gateway dengan fallback otomatis** sudah menjadi standar di platform berskala (e-commerce, ride-hailing, fintech). Tujuannya bukan untuk mengganti Midtrans, tapi untuk membuat sistem **resilient (tahan banting)**:

> *"Satu gateway boleh down. Sistem tidak boleh down."*

---

## 3. Konsep Inti — Cara Kerja Fallback

### 3.1 Analogi Sederhana

Bayangkan strategi ini seperti **GPS yang menghitung rute alternatif**:

- **Rute utama** (Amani Bank) — cepat, biasa dilewati
- **Rute alternatif 1** (Midtrans) — siap dipakai kalau rute utama macet
- **Rute alternatif 2+** — siap dipakai kalau dua rute pertama bermasalah

Donor (pengguna) tidak perlu tahu rute mana yang sedang dipakai. Mereka tetap melihat tombol **"Bayar Sekarang"** yang sama, dan sistem otomatis memilih rute terbaik yang sedang sehat.

### 3.2 Tingkatan Fallback

| Tingkat | Gateway | Status | Catatan |
|---|---|---|---|
| **0 — Primary** | **Amani Bank** | 🔵 Rencana | Dalam tahap onboarding & integrasi |
| **1 — Fallback** | **Midtrans** | 🟢 Aktif | Sudah berjalan di produksi sekarang |
| **2 — Reserve** | (TBD: Xendit / Doku / GoPay) | ⚪ Slot terbuka | Akan ditambah sesuai kebutuhan |

> **Catatan**: Status "Primary" tidak berarti lebih bagus atau lebih murah secara mutlak. Ini hanya berarti **gateway yang dicoba pertama kali**. Logika pemilihan urutan dapat diubah kapan saja melalui konfigurasi (tidak perlu re-deploy aplikasi).

---

## 4. Alur Pembayaran — Versi Detail

### 4.1 Skenario Normal (Semua Gateway Sehat)

```mermaid
sequenceDiagram
    autonumber
    actor Donor
    participant FE as Web AdopTree Frontend
    participant BE as Backend AdopTree
    participant Router as Payment Router baru
    participant Amani as Amani Bank
    participant Midtrans as Midtrans
    participant DB as Database

    Donor->>FE: Klik Bayar Sekarang
    FE->>BE: POST /payments order plus amount
    BE->>DB: Catat transaksi status pending
    BE->>Router: Minta link pembayaran
    Router->>Router: Cek health Amani sehat
    Router->>Amani: Buat transaksi
    Amani-->>Router: URL pembayaran
    Router-->>BE: URL pembayaran via Amani
    BE-->>FE: URL pembayaran
    FE-->>Donor: Tampilkan halaman pembayaran Amani

    Note over Donor,Amani: Donor menyelesaikan pembayaran

    Amani->>BE: Webhook pembayaran berhasil
    BE->>DB: Update transaksi status paid
    BE->>BE: Issue tree certificate plus notifikasi
    BE-->>FE: Konfirmasi
    FE-->>Donor: Sertifikat adopsi tersedia
```

### 4.2 Skenario Fallback (Amani Tidak Tersedia)

```mermaid
sequenceDiagram
    autonumber
    actor Donor
    participant FE as Web AdopTree
    participant BE as Backend AdopTree
    participant Router as Payment Router
    participant Amani as Amani Bank
    participant Midtrans as Midtrans
    participant DB as Database

    Donor->>FE: Klik Bayar Sekarang
    FE->>BE: POST /payments
    BE->>DB: Catat transaksi status pending
    BE->>Router: Minta link pembayaran
    Router->>Amani: Cek status dan coba buat transaksi
    Amani--xRouter: Timeout atau Error
    Router->>Router: Catat kegagalan Amani
    Router->>Midtrans: Otomatis pindah ke Midtrans
    Midtrans-->>Router: URL pembayaran berhasil
    Router-->>BE: URL pembayaran via Midtrans
    BE-->>FE: URL pembayaran
    FE-->>Donor: Tampilkan halaman pembayaran Midtrans

    Note over Donor,Midtrans: Donor tidak menyadari ada perpindahan gateway.<br/>Pengalaman tetap mulus.
```

### 4.3 Skenario Kegagalan Total (Semua Gateway Gagal — Sangat Jarang)

Jika semua gateway dalam pool sedang gagal:

1. Sistem **tidak akan menampilkan halaman error generik**.
2. Donor akan mendapat pesan profesional: *"Layanan pembayaran sedang dalam pemeliharaan. Donasi Anda kami simpan, dan kami akan mengirim email saat sudah dapat diproses."*
3. Transaksi tersimpan di database dengan status `pending_retry`.
4. Job otomatis akan **mencoba ulang setiap 5 menit** sampai salah satu gateway pulih.
5. Donor mendapat email konfirmasi otomatis saat pembayaran berhasil dimulai ulang.

---

## 5. Arsitektur Teknis (High-Level)

Berikut gambaran komponen yang akan ditambahkan ke sistem AdopTree:

```mermaid
graph TB
    subgraph Frontend
        UI["Halaman Checkout / Donasi Instan"]
    end

    subgraph Backend["Backend AdopTree"]
        API["Payment API"]
        Router["Payment Router (baru)"]
        Health["Health Check (baru)"]
        Webhook["Webhook Handler"]
        DB[("Database<br/>payments + payment_attempts")]
    end

    subgraph Gateways["Payment Gateways"]
        Amani["Amani Bank API"]
        Midtrans["Midtrans API"]
        Reserve["Reserve Gateway (future)"]
    end

    UI -->|"1. Initiate"| API
    API -->|"2. Request payment link"| Router
    Router -->|"3. Cek status"| Health
    Health -.->|periodik| Amani
    Health -.->|periodik| Midtrans
    Health -.->|periodik| Reserve

    Router -->|"4a. Try Primary"| Amani
    Router -->|"4b. Auto-fallback"| Midtrans
    Router -->|"4c. Auto-fallback"| Reserve

    Amani -->|webhook| Webhook
    Midtrans -->|webhook| Webhook
    Reserve -.->|webhook| Webhook

    Webhook -->|update| DB
    API -->|read| DB

    style Router fill:#a4de34,stroke:#115031,stroke-width:2px,color:#0d3d25
    style Health fill:#a4de34,stroke:#115031,stroke-width:2px,color:#0d3d25
```

### 5.1 Komponen Baru yang Perlu Dibangun

| Komponen | Fungsi | Estimasi effort |
|---|---|---|
| **Payment Router** | Memilih gateway mana yang dipakai per transaksi | Medium |
| **Health Monitor** | Memeriksa kesehatan tiap gateway setiap menit dan menandai mana yang sehat / sakit | Medium |
| **Gateway Adapter** (Amani) | Modul integrasi spesifik Amani Bank | Medium-High |
| **Gateway Adapter** (Midtrans) | Sudah ada, akan di-refactor jadi adapter | Low |
| **Webhook Unifier** | Menerima notifikasi dari multiple gateway dan menstandarkan formatnya | Medium |
| **Retry Worker** | Mencoba ulang transaksi yang pending saat semua gateway awal gagal | Low-Medium |
| **Admin Dashboard panel** | Tim AdopTree dapat melihat gateway mana yang sedang aktif, rate keberhasilan per gateway | Medium |

---

## 6. Diagram Arsitektur & Flow Lengkap

Bagian ini berisi sekumpulan diagram **Mermaid** profesional yang menggambarkan sistem dari berbagai sudut pandang. Setiap diagram dapat di-*render* langsung di GitHub, Notion, atau editor markdown apa pun yang mendukung Mermaid.

> **Catatan untuk pembaca non-teknis**: Tidak perlu memahami semua diagram sekaligus. Setiap diagram berdiri sendiri dan menjawab satu pertanyaan spesifik. Lewatkan saja yang tidak relevan dengan peran Anda.

---

### 6.1 System Topology — Pemandangan Lengkap (Bird's-Eye View)

> **Pertanyaan yang dijawab**: "Apa saja komponen sistem dan bagaimana mereka terhubung?"

```mermaid
graph TB
    subgraph DonorLayer["Donor Layer"]
        Browser["Browser / Mobile App<br/>adoptreeworld.com"]
    end

    subgraph EdgeLayer["Edge Layer"]
        CDN["Cloudflare CDN<br/>DDoS Protection + SSL"]
    end

    subgraph AppLayer["Application Layer"]
        FE["Frontend Next.js<br/>Checkout + Donasi Instan"]
        BE["Backend API Rust/Axum<br/>Payment Endpoints"]
    end

    subgraph OrchLayer["Payment Orchestration (NEW)"]
        Router["Payment Router<br/>Memilih gateway per transaksi"]
        Health["Health Monitor<br/>Cek gateway tiap 60 detik"]
        Retry["Retry Worker<br/>Retry queue tiap 5 menit"]
        Webhook["Webhook Unifier<br/>Terima notif dari semua gateway"]
    end

    subgraph AdapterLayer["Gateway Adapter Layer"]
        AmaniAdapter["Amani Adapter"]
        MidtransAdapter["Midtrans Adapter"]
        ReserveAdapter["Reserve Adapter (future)"]
    end

    subgraph ExtGateways["External Payment Gateways"]
        Amani["Amani Bank<br/>Corporate Banking API"]
        Midtrans["Midtrans Snap<br/>Multi-channel"]
        Reserve["Reserve Gateway<br/>Xendit / Doku / dll"]
    end

    subgraph DataLayer["Data Layer"]
        DB[("PostgreSQL<br/>payments + attempts + health")]
        Cache[("Redis Cache<br/>health status + routing")]
    end

    subgraph Observability["Observability"]
        Logs["Tracing Logs"]
        Metrics["Metrics Dashboard"]
        Alerts["PagerDuty / Telegram"]
    end

    Browser --> CDN
    CDN --> FE
    FE -->|"REST API"| BE
    BE -->|"Initiate payment"| Router
    Router -->|"Read health"| Cache
    Health -->|"Write health"| Cache
    Health -.->|"Probe periodic"| AmaniAdapter
    Health -.->|"Probe periodic"| MidtransAdapter

    Router -->|"Try primary"| AmaniAdapter
    Router -->|Fallback| MidtransAdapter
    Router -->|Reserve| ReserveAdapter

    AmaniAdapter --> Amani
    MidtransAdapter --> Midtrans
    ReserveAdapter -.-> Reserve

    Amani -->|Webhook| Webhook
    Midtrans -->|Webhook| Webhook
    Reserve -.->|Webhook| Webhook

    Webhook --> DB
    BE --> DB
    Router --> DB
    Retry --> DB
    Retry --> Router

    BE --> Logs
    Router --> Logs
    Webhook --> Logs
    Logs --> Metrics
    Metrics --> Alerts

    classDef newComponent fill:#a4de34,stroke:#115031,stroke-width:2px,color:#0d3d25
    classDef external fill:#fef3c7,stroke:#d97706,stroke-width:1.5px,color:#78350f
    classDef storage fill:#dbeafe,stroke:#1d4ed8,stroke-width:1.5px,color:#1e3a8a

    class Router,Health,Retry,Webhook,AmaniAdapter,MidtransAdapter,ReserveAdapter newComponent
    class Amani,Midtrans,Reserve external
    class DB,Cache storage
```

**Cara membaca**:
- Komponen **hijau lime** = baru, akan dibangun
- Komponen **kuning** = pihak eksternal (di luar kendali kami)
- Komponen **biru** = penyimpanan data
- Garis tegas = jalur aktif setiap transaksi
- Garis putus-putus = jalur monitoring/health (latar belakang)

---

### 6.2 Transaction State Machine — Siklus Hidup Satu Transaksi

> **Pertanyaan yang dijawab**: "Sebuah transaksi melewati state apa saja dari klik bayar sampai sertifikat terbit?"

```mermaid
stateDiagram-v2
    [*] --> Created: Donor klik Bayar

    Created --> RoutingAttempted: Router memilih gateway

    RoutingAttempted --> PendingPayment: Gateway primary OK
    RoutingAttempted --> Fallback: Gateway primary gagal
    Fallback --> PendingPayment: Gateway fallback OK
    Fallback --> PendingRetry: Semua gateway gagal

    PendingRetry --> RoutingAttempted: Retry worker fire tiap 5 menit
    PendingRetry --> Abandoned: TTL habis 24 jam

    PendingPayment --> Paid: Webhook settlement diterima
    PendingPayment --> Failed: Webhook deny atau cancel
    PendingPayment --> Expired: TTL gateway habis
    PendingPayment --> Abandoned: Donor close browser lebih dari 24 jam

    Paid --> CertificateIssued: Issue sertifikat + NFT
    CertificateIssued --> [*]

    Failed --> [*]
    Expired --> [*]
    Abandoned --> [*]

    note right of RoutingAttempted
        Setiap percobaan dicatat
        di tabel payment_attempts
        dengan timestamp + gateway
    end note

    note right of PendingRetry
        State ini hanya muncul
        saat outage total
        sangat jarang
    end note
```

**Penting**:
- Setiap transisi state ter-audit di database (siapa, kapan, gateway mana).
- State `Paid` adalah **final** — tidak bisa kembali ke `PendingPayment` meskipun ada webhook duplikat (idempotency guard).

---

### 6.3 Routing Decision Tree — Logika Pemilihan Gateway

> **Pertanyaan yang dijawab**: "Bagaimana sistem memutuskan gateway mana yang dipakai untuk transaksi ini?"

```mermaid
flowchart TD
    Start(["Transaksi baru masuk"]) --> ReadConfig{"Baca konfigurasi<br/>routing aktif"}

    ReadConfig -->|"priority"| Priority["Urutkan gateway<br/>by priority"]
    ReadConfig -->|"weighted"| Weighted["Pilih gateway by weighted random<br/>contoh: 70 persen Amani / 30 persen Midtrans"]
    ReadConfig -->|"cheapest"| Cheapest["Pilih gateway dengan<br/>fee terendah untuk amount ini"]

    Priority --> CheckHealth1
    Weighted --> CheckHealth1
    Cheapest --> CheckHealth1

    CheckHealth1{"Gateway #1 sehat?<br/>cek Redis cache"}
    CheckHealth1 -->|"Ya"| TryGW1
    CheckHealth1 -->|"Tidak"| CheckHealth2

    TryGW1["Panggil API Gateway #1<br/>timeout 5 detik"]
    TryGW1 -->|"Success"| LogSuccess1["Catat di payment_attempts<br/>gateway #1 status success"]
    TryGW1 -->|"Timeout / Error"| MarkUnhealthy1["Tandai gateway #1<br/>unhealthy 60 detik"]
    MarkUnhealthy1 --> CheckHealth2

    CheckHealth2{"Gateway #2 sehat?"}
    CheckHealth2 -->|"Ya"| TryGW2
    CheckHealth2 -->|"Tidak"| CheckHealth3

    TryGW2["Panggil API Gateway #2"]
    TryGW2 -->|"Success"| LogSuccess2["Catat gateway #2 success"]
    TryGW2 -->|"Error"| MarkUnhealthy2["Tandai #2 unhealthy"]
    MarkUnhealthy2 --> CheckHealth3

    CheckHealth3{"Ada gateway #3+?"}
    CheckHealth3 -->|"Ya"| TryGW3["Coba gateway berikutnya"]
    CheckHealth3 -->|"Tidak"| EnqueueRetry["Masuk antrian retry<br/>status PendingRetry"]

    TryGW3 -->|"Success"| LogSuccess3["Catat sukses"]
    TryGW3 -->|"Gagal"| EnqueueRetry

    LogSuccess1 --> ReturnUrl(["Return payment URL"])
    LogSuccess2 --> ReturnUrl
    LogSuccess3 --> ReturnUrl

    EnqueueRetry --> NotifyDonor(["Tampilkan pesan halus + email"])

    classDef startNode fill:#a4de34,stroke:#115031,color:#0d3d25
    classDef successNode fill:#dcfce7,stroke:#16a34a,color:#14532d
    classDef warningNode fill:#fef3c7,stroke:#d97706,color:#78350f
    classDef terminalNode fill:#fee2e2,stroke:#dc2626,color:#7f1d1d

    class Start startNode
    class LogSuccess1,LogSuccess2,LogSuccess3,ReturnUrl successNode
    class MarkUnhealthy1,MarkUnhealthy2 warningNode
    class EnqueueRetry,NotifyDonor terminalNode
```

**Konfigurasi routing strategy** dapat diubah kapan saja melalui admin dashboard tanpa re-deploy aplikasi.

---

### 6.4 Webhook Convergence — Multi-Source Notification Handling

> **Pertanyaan yang dijawab**: "Setiap gateway kirim notifikasi dengan format berbeda — bagaimana kita menanganinya?"

```mermaid
sequenceDiagram
    autonumber
    participant Amani as Amani Bank
    participant Midtrans as Midtrans
    participant Reserve as Reserve GW
    participant Endpoint as Webhook Endpoint
    participant Unifier as Webhook Unifier
    participant Validator as Signature Validator
    participant Repo as Payment Repository
    participant Issuer as Certificate Issuer

    Note over Amani,Reserve: Tiga gateway dengan 3 format webhook berbeda

    Amani->>+Endpoint: POST /webhooks/amani dengan payload format Amani
    Midtrans->>+Endpoint: POST /webhooks/midtrans dengan payload format Midtrans
    Reserve-->>+Endpoint: POST /webhooks/reserve dengan payload format Reserve

    Endpoint->>+Unifier: Normalisasi payload

    Unifier->>+Validator: Verifikasi signature
    Validator->>Validator: Cek HMAC atau Bearer atau SHA512
    Validator-->>-Unifier: Valid atau Invalid

    alt Signature invalid
        Unifier-->>Endpoint: 401 Unauthorized
        Endpoint-->>Amani: Reject
    end

    Unifier->>Unifier: Mapping ke format kanonik order_id gateway status txn_ref

    Unifier->>+Repo: Cari payment by order_id

    alt Payment tidak ditemukan
        Repo-->>Unifier: 404
        Unifier-->>Endpoint: 200 OK silent
        Note over Endpoint: Hindari retry loop dari gateway
    end

    Repo-->>-Unifier: payment record

    Unifier->>Unifier: Cek idempotency sudah pernah diproses

    alt Sudah Paid sebelumnya
        Unifier-->>Endpoint: 200 OK no-op
        Note over Unifier: Idempotent webhook duplikat aman
    end

    Unifier->>+Repo: Update status Paid
    Repo->>Repo: Log di payment_attempts gateway konfirmer
    Repo-->>-Unifier: OK

    Unifier->>+Issuer: Trigger sertifikat
    Issuer->>Issuer: Generate PDF dan NFT jika tier AdopTree
    Issuer-->>-Unifier: OK

    Unifier-->>-Endpoint: 200 OK
    Endpoint-->>-Amani: 200 OK
```

**Kunci desain**:
- Setiap gateway punya **endpoint webhook terpisah** untuk signature verification yang tepat.
- Format yang berbeda di-normalisasi ke **canonical schema** sebelum diproses.
- **Idempotency** dijaga via `order_id` unik — webhook duplikat tidak menyebabkan transaksi ganda.

---

### 6.5 Health Monitor Loop — Operational View

> **Pertanyaan yang dijawab**: "Bagaimana sistem tahu gateway mana yang sedang sehat?"

```mermaid
flowchart LR
    subgraph Loop["Continuous Loop (tiap 60 detik)"]
        Tick(["Tick"]) --> ProbeAll["Probe semua gateway<br/>secara paralel"]
    end

    ProbeAll --> ProbeAmani{"Probe Amani"}
    ProbeAll --> ProbeMidtrans{"Probe Midtrans"}
    ProbeAll --> ProbeReserve{"Probe Reserve"}

    subgraph AmaniHealth["Amani Health"]
        ProbeAmani -->|"GET /healthz"| AmaniResult["Latency 240ms<br/>Status 200 OK"]
        AmaniResult --> ScoreAmani["Hitung score<br/>uptime persen + latency"]
    end

    subgraph MidtransHealth["Midtrans Health"]
        ProbeMidtrans -->|"GET /v2/ping"| MidtransResult["Latency 180ms<br/>Status 200 OK"]
        MidtransResult --> ScoreMidtrans["Hitung score"]
    end

    subgraph ReserveHealth["Reserve Health"]
        ProbeReserve --> ReserveResult["Latency timeout"]
        ReserveResult --> ScoreReserve["Score 0<br/>Tandai unhealthy"]
    end

    ScoreAmani --> UpdateCache[("Update Redis<br/>key: gateway_health")]
    ScoreMidtrans --> UpdateCache
    ScoreReserve --> UpdateCache

    UpdateCache --> CheckThreshold{"Gagal 3x<br/>berturut-turut?"}

    CheckThreshold -->|"Ya"| TriggerAlert["Kirim alert<br/>Telegram + PagerDuty"]
    CheckThreshold -->|"Tidak"| Wait["Tunggu tick berikutnya"]

    TriggerAlert --> Wait
    Wait --> Tick

    classDef alertNode fill:#fee2e2,stroke:#dc2626,color:#7f1d1d
    classDef okNode fill:#dcfce7,stroke:#16a34a,color:#14532d
    classDef warningNode fill:#fef3c7,stroke:#d97706,color:#78350f

    class TriggerAlert alertNode
    class AmaniResult,MidtransResult okNode
    class ReserveResult warningNode
```

**Kebijakan operasional**:
- **Probe interval**: 60 detik (dapat diubah)
- **Threshold unhealthy**: 3 kali gagal berturut-turut
- **Cooldown**: setelah ditandai unhealthy, gateway tetap di-skip 60 detik sebelum di-probe ulang
- **Alert escalation**: alert otomatis ke tim ops via Telegram + PagerDuty (untuk insiden production)

---

### 6.6 Data Model — Entity Relationship Diagram

> **Pertanyaan yang dijawab**: "Tabel apa saja yang dibutuhkan untuk menyimpan jejak transaksi multi-gateway?"

```mermaid
erDiagram
    PAYMENTS ||--o{ PAYMENT_ATTEMPTS : has
    PAYMENTS ||--o{ PAYMENT_WEBHOOK_EVENTS : receives
    PAYMENTS }o--|| ADOPTIONS : "settles"
    PAYMENT_ATTEMPTS }o--|| GATEWAYS : "via"
    PAYMENT_WEBHOOK_EVENTS }o--|| GATEWAYS : "from"
    GATEWAYS ||--o{ GATEWAY_HEALTH_HISTORY : "tracks"

    PAYMENTS {
        uuid id PK
        string order_id UK "ATW prefix timestamp suffix"
        uuid adoption_id FK
        decimal amount_usd
        decimal amount_idr
        string status "Created Pending Paid Failed Expired"
        timestamp created_at
        timestamp expires_at
        timestamp paid_at
        string paid_via_gateway "amani midtrans reserve"
    }

    PAYMENT_ATTEMPTS {
        uuid id PK
        uuid payment_id FK
        string gateway "amani midtrans reserve"
        int attempt_number "1 2 3 dst"
        string outcome "success timeout error fallback"
        int latency_ms
        text error_detail
        timestamp attempted_at
    }

    PAYMENT_WEBHOOK_EVENTS {
        uuid id PK
        uuid payment_id FK
        string gateway
        string event_type "settlement deny cancel refund"
        jsonb raw_payload "Original webhook body"
        boolean signature_valid
        boolean processed
        timestamp received_at
    }

    GATEWAYS {
        string name PK "amani midtrans reserve"
        int priority "1 primary 2 fallback dst"
        boolean is_enabled
        decimal weight "0.0 sampai 1.0 untuk weighted routing"
        jsonb credentials_ref "Vault key reference"
        timestamp last_modified
    }

    GATEWAY_HEALTH_HISTORY {
        uuid id PK
        string gateway FK
        int latency_ms
        int status_code
        boolean is_healthy
        timestamp probed_at
    }

    ADOPTIONS {
        uuid id PK
        uuid user_id
        uuid tree_id
        string tier "donation wakaf green_society adoptree"
    }
```

**Catatan untuk tim**:
- `payment_attempts` adalah **audit trail lengkap** — setiap percobaan ke gateway dicatat, sukses maupun gagal. Berguna untuk analitik dan dispute resolution.
- `gateway_health_history` di-prune otomatis (retensi 30 hari) untuk mencegah tabel membesar.
- Konfigurasi `gateways.priority` dan `gateways.weight` adalah **hot-reloadable** — perubahan diterapkan dalam <60 detik tanpa restart.

---

### 6.7 Retry Recovery Flow — Penanganan Failure Total

> **Pertanyaan yang dijawab**: "Apa yang terjadi pada transaksi saat *semua* gateway sedang down (skenario sangat langka)?"

```mermaid
sequenceDiagram
    autonumber
    actor Donor
    participant FE as Frontend
    participant BE as Backend
    participant Router as Payment Router
    participant Queue as Retry Queue
    participant Worker as Retry Worker cron 5 menit
    participant Email as Email Service

    Donor->>FE: Klik Bayar
    FE->>BE: POST /payments
    BE->>Router: Request payment URL

    Router--xRouter: Semua gateway down

    Router->>Queue: Enqueue payment_id retry_count 0
    Router-->>BE: Status PendingRetry
    BE-->>FE: Sedang diproses kami kirim email setelah siap
    FE-->>Donor: Pesan profesional ditampilkan

    BE->>Email: Send email Donasi tertunda
    Email-->>Donor: Email konfirmasi

    Note over Worker: 5 menit kemudian
    Worker->>Queue: Pull pending retries
    Queue-->>Worker: list payment_id retry_count
    Worker->>Router: Re-attempt

    alt Gateway pulih
        Router-->>Worker: Payment URL berhasil dibuat
        Worker->>Email: Send email Donasi siap dibayar
        Email-->>Donor: Link pembayaran via email
        Donor->>FE: Klik link dan selesaikan pembayaran
    else Masih gagal
        Worker->>Queue: Re-enqueue retry_count 1
        Note over Worker: Exponential backoff 5 menit ke 15 menit ke 1 jam ke 4 jam
    end

    Note over Worker,Queue: TTL maksimum 24 jam<br/>Setelah itu mark Abandoned<br/>plus refund jika sudah ada partial charge
```

**Kebijakan retry**:
- **Backoff exponential**: 5 menit → 15 menit → 1 jam → 4 jam → mark Abandoned
- **Donor diberitahu** di setiap milestone via email (tidak silent failure)
- **TTL maksimum**: 24 jam — setelah itu transaksi resmi `Abandoned`, donor diminta retry manual

---

### 6.8 Production Deployment Topology — Infrastructure View

> **Pertanyaan yang dijawab**: "Di production, komponen ini berjalan di mana saja secara fisik?"

```mermaid
graph TB
    subgraph Internet["Internet"]
        Users["Users Web + Mobile"]
        AmaniAPI["Amani Bank API<br/>sandbox + production"]
        MidtransAPI["Midtrans API<br/>sandbox + production"]
    end

    subgraph CloudflareGroup["Cloudflare"]
        CFEdge["Edge CDN<br/>DDoS + Bot Protection"]
        CFWebhook["Webhook Endpoint<br/>SSL termination"]
    end

    subgraph VPS["VPS Production"]
        subgraph DockerNet["Docker Network adoptree-prod"]
            Nginx["nginx-proxy port 443"]
            FEContainer["frontend Next.js<br/>3 replicas"]
            BEContainer["backend Rust/Axum<br/>3 replicas"]
            HealthContainer["health-monitor<br/>1 replica"]
            RetryContainer["retry-worker<br/>1 replica"]
        end

        subgraph DataStack["Data Stack"]
            Postgres[("PostgreSQL 16<br/>Primary")]
            PostgresReplica[("PostgreSQL<br/>Read Replica")]
            RedisCache[("Redis 7<br/>Health + Sessions")]
        end
    end

    subgraph ObjectStorage["Object Storage"]
        R2["Cloudflare R2<br/>Certificates + Tree photos"]
    end

    subgraph Obs["Observability"]
        Grafana["Grafana Dashboard"]
        Loki["Loki Log Aggregator"]
        AlertManager["Alert Manager<br/>Telegram + PagerDuty"]
    end

    Users -->|"HTTPS"| CFEdge
    CFEdge -->|"HTTPS"| Nginx
    Nginx -->|"HTTP"| FEContainer
    Nginx -->|"HTTP"| BEContainer

    FEContainer -->|"REST"| BEContainer
    BEContainer -->|"SQL"| Postgres
    BEContainer -->|"Read-only"| PostgresReplica
    BEContainer -->|"Cache"| RedisCache

    BEContainer -->|"HTTPS"| AmaniAPI
    BEContainer -->|"HTTPS"| MidtransAPI

    AmaniAPI -.->|"Webhook"| CFWebhook
    MidtransAPI -.->|"Webhook"| CFWebhook
    CFWebhook -->|"HTTPS"| Nginx

    HealthContainer -->|"Probe"| AmaniAPI
    HealthContainer -->|"Probe"| MidtransAPI
    HealthContainer -->|"Write status"| RedisCache

    RetryContainer -->|"Poll queue"| Postgres
    RetryContainer -->|"Re-attempt"| BEContainer

    BEContainer -->|"Upload PDF"| R2

    BEContainer -.->|"Logs"| Loki
    HealthContainer -.->|"Logs"| Loki
    RetryContainer -.->|"Logs"| Loki
    Loki --> Grafana
    Grafana --> AlertManager

    classDef newNode fill:#a4de34,stroke:#115031,color:#0d3d25
    classDef extNode fill:#fef3c7,stroke:#d97706,color:#78350f
    classDef storageNode fill:#dbeafe,stroke:#1d4ed8,color:#1e3a8a

    class HealthContainer,RetryContainer newNode
    class AmaniAPI,MidtransAPI extNode
    class Postgres,PostgresReplica,RedisCache,R2 storageNode
```

**Highlight infrastruktur**:
- **Stateless containers** (frontend, backend) dapat di-scale horizontal sesuai kebutuhan.
- **Health monitor** dan **retry worker** adalah **single-instance** (1 replica) untuk menghindari double-probe / double-retry.
- Webhook melalui **Cloudflare** untuk SSL termination + DDoS protection.
- Database dipisah **primary** (write) dan **read replica** untuk performance + redundancy.

---

## 7. Manfaat Bisnis (Business Impact)

### 7.1 Manfaat Langsung

| Manfaat | Sebelum | Sesudah |
|---|---|---|
| **Uptime pembayaran** | ~99.5% (mengikuti Midtrans) | ~99.95%+ (kombinasi 2 gateway) |
| **Resiko revenue loss saat outage** | Tinggi — semua transaksi mati | Rendah — auto-fallback transparan |
| **Negosiasi fee gateway** | Lemah — satu vendor | Kuat — vendor tahu ada alternatif |
| **Geografis coverage** | Terbatas pada channel Midtrans | Lebih luas (Amani Bank corporate + Midtrans consumer) |
| **Donor confidence** | Donor sensitif terhadap incident | Donor jarang tahu ada gangguan |

### 7.2 Manfaat Strategis Jangka Panjang

1. **Kemandirian dari satu vendor** — leverage komersial saat negosiasi rate / fitur baru.
2. **Foundation untuk multi-currency** — beberapa gateway native USD/SGD/multi-mata uang, memudahkan rencana ekspansi regional.
3. **Compliance flexibility** — beberapa segmen donor (institusi, foundation, perbankan syariah) mungkin punya preferensi gateway tertentu. Multi-gateway memungkinkan mengarahkan transaksi sesuai segmen.
4. **A/B testing** — bisa membandingkan conversion rate Amani vs Midtrans dan mengarahkan trafik ke yang lebih baik.

---

## 8. Pengalaman Donor (UX Promise)

**Komitmen kami kepada donor**: pengalaman pembayaran **tidak boleh terganggu** oleh kerumitan teknis di belakang layar.

### 8.1 Yang Dilihat Donor

- ✅ Halaman checkout yang sama, tombol yang sama, alur yang sama.
- ✅ Tidak ada pilihan "Pilih gateway pembayaran" — sistem yang memilih, bukan donor.
- ✅ Konfirmasi pembayaran tetap melalui email dan aplikasi seperti biasa.

### 8.2 Yang Tidak Dilihat Donor

- ❌ Notifikasi "Gateway X sedang down".
- ❌ Halaman error teknis.
- ❌ Logika routing internal kami.

> **Filosofi**: Resilience adalah pekerjaan engineering yang baik **bila pengguna tidak merasakannya**.

---

## 9. Roadmap & Tahapan Implementasi

```mermaid
gantt
    title Roadmap Payment Gateway Resilience
    dateFormat YYYY-MM-DD
    section Fase 1 Foundation
    Refactor Midtrans jadi adapter      :a1, 2026-06-15, 2w
    Bangun Payment Router core          :a2, after a1, 2w
    Bangun Health Monitor               :a3, after a1, 2w
    section Fase 2 Amani Integration
    Onboarding teknis Amani Bank        :b1, 2026-07-01, 3w
    Integrasi Amani Adapter             :b2, after b1, 3w
    Sandbox testing                     :b3, after b2, 2w
    section Fase 3 Production
    Soft launch 1 persen ke Amani       :c1, after b3, 1w
    Gradual rollout 10 ke 50 ke 100     :c2, after c1, 3w
    Monitoring dan tuning               :c3, after c2, 2w
    section Fase 4 Extension
    Slot gateway ketiga TBD             :d1, after c3, 4w
```

### 9.1 Milestone Utama

| Fase | Output | Target |
|---|---|---|
| **Fase 1 — Foundation** | Payment Router siap, Midtrans tetap berjalan tanpa downtime | Akhir Juli 2026 |
| **Fase 2 — Amani Integration** | Amani Bank ter-integrasi penuh di sandbox | Akhir Agustus 2026 |
| **Fase 3 — Production** | Amani Bank menerima 100% trafik primer, Midtrans sebagai fallback | Akhir September 2026 |
| **Fase 4 — Extension** | Gateway ketiga sebagai reserve, sesuai kebutuhan | Q4 2026 |

> Target dapat disesuaikan setelah review teknis dan finalisasi kontrak Amani Bank.

---

## 10. Risiko & Mitigasi

| Risiko | Probabilitas | Dampak | Mitigasi |
|---|---|---|---|
| Integrasi Amani Bank lebih lama dari estimasi | Sedang | Sedang — geser timeline | Buffer 2 minggu di Fase 2, Midtrans tetap jadi primary sampai Amani siap |
| Donor mengalami transaksi ganda saat fallback | Rendah | Tinggi (refund manual) | Idempotency key dengan order_id unik, audit log setiap percobaan gateway |
| Webhook dari dua gateway konflik untuk order yang sama | Rendah | Sedang | First-confirmation-wins logic + audit trail di tabel `payment_attempts` |
| Fee gateway baru (Amani) lebih tinggi dari Midtrans | Sedang | Rendah-Sedang | Routing logic dapat dikonfigurasi: bisa "Amani primary" atau "biaya termurah primary" |
| Compliance issue saat migrasi data transaksi | Rendah | Tinggi | Data Midtrans existing tidak dimigrasi — tetap di Midtrans. Sistem baru hanya untuk transaksi baru sejak go-live |

---

## 11. Pertanyaan Yang Sering Ditanya

**Q: Apakah donor perlu melakukan apa-apa saat rollout?**
A: Tidak. Pengalaman donor tidak berubah sama sekali — tombol bayar, alur checkout, email konfirmasi tetap sama.

**Q: Apakah donor yang sudah pakai Midtrans akan dipaksa pindah ke Amani?**
A: Tidak. Sistem yang memilih gateway secara dinamis per transaksi. Donor tidak pernah "terikat" dengan gateway tertentu.

**Q: Kalau Amani lebih mahal dari Midtrans, kenapa dijadikan primary?**
A: Pemilihan "primary" tidak hanya tentang fee — juga tentang strategic partnership, reliability profile, dan support level. Selain itu, **logic routing dapat dikonfigurasi** untuk mengarahkan trafik ke gateway termurah atau dengan success rate terbaik secara real-time.

**Q: Berapa lama untuk implementasi penuh?**
A: Sekitar 3–4 bulan dari kickoff teknis, dengan asumsi Amani Bank sudah memberikan akses sandbox. Detail di [Roadmap](#9-roadmap--tahapan-implementasi).

**Q: Apakah ini akan mempengaruhi sertifikat NFT (AdopTree tier)?**
A: Tidak. Layer NFT issuance tetap independen dari payment gateway — yang berubah hanya cara duit masuk, bukan cara sertifikat diterbitkan.

**Q: Apakah aman menambahkan gateway baru di masa depan?**
A: Ya. Arsitektur dirancang dengan **adapter pattern** — gateway baru tinggal dibuatkan adapter sesuai interface standar, dan otomatis bisa masuk ke pool routing tanpa mengubah business logic.

---

## 12. Glosarium

| Istilah | Penjelasan |
|---|---|
| **Payment Gateway** | Pihak ketiga yang memproses pembayaran (Midtrans, Amani Bank, dll.) |
| **Primary Gateway** | Gateway yang dicoba pertama kali oleh sistem |
| **Fallback Gateway** | Gateway cadangan yang otomatis dipakai jika primary gagal |
| **Adapter Pattern** | Pola desain software di mana setiap gateway diberi "pembungkus" agar terlihat sama dari sisi business logic |
| **Health Check** | Pemeriksaan otomatis berkala untuk memastikan gateway sedang sehat dan bisa menerima transaksi |
| **Webhook** | Notifikasi otomatis dari gateway ke server AdopTree saat status pembayaran berubah |
| **Idempotency** | Properti yang memastikan bahwa percobaan ulang tidak menyebabkan transaksi ganda |
| **Routing Logic** | Aturan internal yang menentukan gateway mana yang dipakai per transaksi |
| **Sandbox** | Lingkungan testing yang menyerupai produksi tapi tanpa transaksi uang sungguhan |

---

## 13. Persetujuan & Kontak

Dokumen ini disusun untuk:
- 🟢 **Disetujui oleh**: CEO / CTO / Head of Product
- 🟡 **Direview oleh**: Tim Engineering, Tim Finance, Tim Legal & Compliance
- 🔵 **Diketahui oleh**: Tim Operations, Tim Customer Support

**Pertanyaan & feedback**: hello@adoptreeworld.com

---

*Dokumen ini akan diperbarui seiring dengan progres implementasi. Versi terbaru selalu tersedia di repository internal.*
