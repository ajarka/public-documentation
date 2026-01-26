# 📍 VinFast Location Status Map - Proposal & Pricing
**Sistem Pemetaan Lokasi VinFast Indonesia dengan Real-time Status**

---

## 📋 Executive Summary

- **Project Name:** VinFast Location Status Map
- **Client:** VinFast Team
- **Document Date:** Januari 2026
- **Prepared By:** Aditira Jamhuri

### Tentang Sistem

Aplikasi web interaktif untuk menampilkan dan mengelola lokasi VinFast (charging stations, showrooms, service centers) di seluruh Indonesia dengan indikator status real-time.

**Tujuan:**
- Memudahkan user menemukan lokasi VinFast terdekat
- Monitoring status lokasi secara real-time
- Management lokasi yang efisien via admin panel
- Meningkatkan user experience dengan informasi akurat

---

## 🎯 Fitur Utama Sistem

### 1. Interactive Map
- Peta Indonesia interaktif dengan marker lokasi VinFast
- Color-coded markers berdasarkan status:
  - 🔵 **Biru** - Available (Tersedia)
  - 🟡 **Kuning** - Maintenance (Dalam perbaikan)
  - 🔴 **Merah** - In Use (Sedang digunakan)
- Auto-zoom berdasarkan lokasi user
- Filter status (show/hide berdasarkan kebutuhan)

### 2. Detailed Location Information
Setiap marker menampilkan informasi lengkap:
- Nama lokasi
- Alamat lengkap (jalan, kota, provinsi, kode pos)
- Status terkini
- **Untuk Charging Stations:**
  - Access hours (e.g., "Available 24/7")
  - Tipe charging (Public/Private)
  - Parking fee status (Free/Not Free)
  - Detail charging ports (jumlah & power, e.g., "1 charging ports of 30kW")
- **Navigation Links:**
  - Google Maps
  - Apple Maps
  - Waze

### 3. Admin Panel
Dashboard management untuk staff VinFast:
- Login dengan autentikasi JWT (secure)
- CRUD Operations (Create, Read, Update, Delete) lokasi
- Quick status update
- Bulk upload via CSV/Excel
- User management (multi-admin support)
- Status history & audit trail

### 4. Real-time Updates
- Automatic status synchronization
- Polling system (30s interval) atau WebSocket (instant)
- Notifikasi untuk admin saat ada perubahan

---

## 💼 Paket Harga & Fitur

### 🥉 PAKET MVP (Minimum Viable Product)

**💰 Harga: Rp 42.000.000,-**
**⏱️ Timeline: 2-3 Minggu**

#### Fitur Included:
✅ **Interactive Map**
- Leaflet map dengan OpenStreetMap tiles
- Marker dengan 3 status warna
- Filter status (toggle show/hide)
- Responsive design (mobile-friendly)

✅ **Location Popup**
- Nama lokasi
- Alamat
- Status badge
- Direction link (Google Maps only)

✅ **Backend API (Golang)**
- GET /locations (semua lokasi)
- GET /locations/:id (detail lokasi)
- GET /locations/nearby (pencarian radius)

✅ **Admin Panel Basic**
- Login authentication (JWT)
- CRUD locations manual
- Manual status update
- Manual refresh (no auto-update)

✅ **Database**
- PostgreSQL 16 + PostGIS 3.4
- Spatial indexing untuk query cepat
- Geographic coordinate system (WGS84)

✅ **Deployment & Support**
- Deployment ke VPS (1x setup)
- Domain + SSL certificate (1 tahun)
- Source code + dokumentasi
- 2 minggu bug support

#### Cocok Untuk:
- Pilot project / proof of concept
- Budget terbatas
- Jumlah lokasi: < 50
- Concurrent users: < 100

---

### 🥈 PAKET STANDARD (Recommended) ⭐

**💰 Harga: Rp 75.000.000,-**
**⏱️ Timeline: 4-5 Minggu**

#### Semua Fitur MVP PLUS:

✅ **Enhanced Location Popup**
- Icon berbeda untuk charging station vs showroom
- Access hours detail ("Available 24/7" atau custom hours)
- Charging type (Public/Private)
- Parking fee indicator (Free/Not Free)
- Charging ports specification (e.g., "1 charging ports of 30kW")
- Multiple direction links:
  - Google Maps
  - Apple Maps
  - Waze

✅ **Real-time Updates**
- Auto-polling setiap 30 detik
- Status berubah otomatis tanpa refresh manual
- Background sync

✅ **CSV Bulk Upload**
- Admin UI untuk upload file CSV/Excel
- Data validation & error reporting
- Progress indicator
- Support untuk ratusan lokasi sekaligus

✅ **Advanced Admin Panel**
- Location management table dengan:
  - Search by name/address
  - Filter by status/type/city
  - Sort by various fields
  - Pagination untuk banyak data
- Quick status update (tanpa full edit)
- Status history log (audit trail)
- Bulk status update (multiple locations sekaligus)
- User management (multiple admin dengan roles)

✅ **Enhanced Map Features**
- Auto-fit bounds based on locations
- Search location by name/address
- Click marker untuk quick info preview
- Location clustering (untuk banyak marker berdekatan)
- Custom marker icons

✅ **Performance Optimization**
- API caching (Redis/in-memory)
- Database query optimization
- Lazy loading components
- CDN untuk static assets

✅ **Deployment & Monitoring**
- VPS deployment dengan optimization
- Nginx reverse proxy + SSL
- Basic monitoring scripts
- Auto-restart on failure
- Rate limiting (DDoS protection)
- Health check automation

✅ **Extended Support**
- 1 bulan bug fixes & minor adjustments
- Training & handover session
- Complete documentation (technical + user guide)

#### Cocok Untuk:
- Production-ready application
- Public-facing website
- Jumlah lokasi: 50-500
- Concurrent users: 100-1,000
- Professional business use

---

### 🥇 PAKET PREMIUM (Enterprise)

**💰 Harga: Rp 110.000.000,-**
**⏱️ Timeline: 6-7 Minggu**

#### Semua Fitur STANDARD PLUS:

✅ **WebSocket Real-time Updates**
- Instant status updates (no delay)
- Bidirectional communication
- Connection management & auto-reconnect
- Better UX untuk collaborative admin

✅ **Advanced Analytics Dashboard**
- Statistik lokasi per status (charts)
- Usage heatmap visualization
- Historical data analytics
- Geographic distribution reports
- Export reports (PDF/Excel)
- Custom date range filtering

✅ **Public API dengan API Keys**
- RESTful API untuk integrasi eksternal
- API key generation & management
- Rate limiting per API key
- Access control & permissions
- API documentation (Swagger/OpenAPI)
- Webhook support

✅ **Multi-language Support**
- Bahasa Indonesia
- English
- Easy to add more languages
- Language switcher UI

✅ **Advanced Map Features**
- Heatmap visualization (density)
- Route planning (multi-location directions)
- Custom map styles/themes (dark mode, satellite, etc.)
- Geofencing (area boundaries)
- Drawing tools untuk admin

✅ **Notification System**
- Email notifications untuk status changes
- Webhook support untuk integrasi external
- Admin alert system (push notifications)
- Scheduled reports

✅ **Enhanced Security**
- Two-factor authentication (2FA)
- IP whitelisting untuk admin panel
- Advanced rate limiting
- Security audit + penetration testing report
- Encrypted sensitive data
- Audit logging

✅ **Load Balancing & Scalability**
- Multi-server setup support
- Database replication (read replicas)
- CDN integration (CloudFlare/AWS)
- Auto-scaling configuration

✅ **Mobile-Optimized PWA**
- Progressive Web App
- Offline mode support
- Add to home screen
- Push notifications
- Mobile-first design

✅ **Premium Support**
- 3 bulan support & maintenance
- 2x training session untuk team client
- Priority bug fixes
- 1-2 custom feature requests
- Monthly performance reports

#### Cocok Untuk:
- Enterprise-level deployment
- High traffic website
- Jumlah lokasi: 500+
- Concurrent users: 1,000+
- Integration dengan sistem existing (ERP/CRM)
- Long-term scalability needs

---

## 📦 Add-ons (Optional)

Fitur tambahan yang bisa ditambahkan ke paket manapun:

| Add-on Feature | Harga | Timeline | Keterangan |
|----------------|-------|----------|------------|
| **Mobile App (React Native)** | Rp 45.000.000,- | 4-5 minggu | iOS + Android native app dengan offline mode |
| **Extended Support** | Rp 5.000.000,-/bulan | Ongoing | Bug fixes, minor updates, priority support |
| **Managed Hosting** | Rp 3.500.000,-/bulan | Ongoing | VPS management, monitoring, backups, updates |
| **Data Migration Service** | Rp 8.000.000,- | 1-2 minggu | Migrate data dari existing system/database |
| **Custom Integration** | Rp 15.000.000,- | 2-3 minggu | Integrasi dengan ERP/CRM/third-party systems |
| **Advanced Reporting Module** | Rp 12.000.000,- | 2 minggu | Custom dashboards, BI tools, advanced analytics |
| **WhatsApp Bot Integration** | Rp 18.000.000,- | 2-3 minggu | Status updates & notifications via WhatsApp |
| **Reservation System** | Rp 20.000.000,- | 3 minggu | Booking system untuk charging slots |
| **Multi-tenant Support** | Rp 25.000.000,- | 3-4 minggu | Support multiple organizations/regions |

---

## 📊 Perbandingan Paket

| Feature | MVP | STANDARD | PREMIUM |
|---------|-----|----------|---------|
| **💰 Harga** | Rp 42 jt | Rp 75 jt | Rp 110 jt |
| **⏱️ Timeline** | 2-3 minggu | 4-5 minggu | 6-7 minggu |
| **Interactive Map** | ✅ Basic | ✅ Enhanced | ✅ Advanced |
| **Location Popup** | Basic | Detail ✅ | Detail ✅ |
| **Admin Panel** | Basic | Advanced ✅ | Advanced ✅ |
| **Real-time Updates** | ❌ Manual | ✅ Polling (30s) | ✅ WebSocket (instant) |
| **CSV Bulk Upload** | ❌ | ✅ | ✅ |
| **Search & Filter** | ❌ | ✅ | ✅ |
| **Analytics Dashboard** | ❌ | ❌ | ✅ |
| **Public API** | ❌ | ❌ | ✅ |
| **Multi-language** | ❌ | ❌ | ✅ |
| **Mobile PWA** | ❌ | ❌ | ✅ |
| **Notification System** | ❌ | ❌ | ✅ |
| **2FA Security** | ❌ | ❌ | ✅ |
| **Support Duration** | 2 minggu | 1 bulan | 3 bulan |
| **Training Session** | ❌ | 1x | 2x |
| **Max Locations** | < 50 | 50-500 | 500+ |
| **Max Concurrent Users** | < 100 | 100-1,000 | 1,000+ |
| **Custom Features** | ❌ | ❌ | 1-2 requests |

---

## 💻 Teknologi yang Digunakan

### Backend
- **Language:** Golang 1.22+
- **Framework:** Echo / Fiber (high performance)
- **Database:** PostgreSQL 16 + PostGIS 3.4
- **Authentication:** JWT (JSON Web Tokens)
- **API Style:** RESTful API
- **WebSocket:** gorilla/websocket (Premium tier)

**Kenapa Golang?**
- ✅ High performance & low resource usage
- ✅ Excellent concurrency handling (goroutines)
- ✅ Fast compilation & deployment
- ✅ Native PostGIS support via pgx driver
- ✅ Small binary size (~10-15MB)

### Frontend
- **Framework:** Next.js 14+ (React)
- **Language:** TypeScript
- **UI Library:** Ant Design / Shadcn UI
- **Map Library:** React Leaflet / Google Maps API
- **State Management:** React Context / Zustand
- **Styling:** Tailwind CSS

**Kenapa Next.js?**
- ✅ SEO-friendly (Server-Side Rendering)
- ✅ Excellent performance
- ✅ Rich component ecosystem
- ✅ Easy deployment (Vercel/Netlify)
- ✅ Built-in optimization

### Database
- **PostgreSQL 16** - Industry-standard RDBMS
- **PostGIS 3.4** - Spatial extension untuk geographic queries
- **GIST Indexes** - Fast spatial queries

**Kenapa PostGIS?**
- ✅ Native geographic data types
- ✅ Spatial queries (radius search, nearest location, etc.)
- ✅ Used by Uber, Lyft, Grab untuk location services
- ✅ Open-source & mature

### Infrastructure
- **Web Server:** Nginx (reverse proxy, SSL termination)
- **VPS:** Vultr / DigitalOcean / Hetzner
- **SSL:** Let's Encrypt (free, auto-renewal)
- **CDN:** Cloudflare (free tier)
- **Monitoring:** Custom scripts + health checks

---

## 💳 Terms & Conditions

### Metode Pembayaran

Pembayaran dilakukan dalam 3 tahap:

1. **Down Payment 40%** - Setelah approval proposal & kickoff meeting
2. **Progress Payment 30%** - Setelah MVP/core features selesai (demo & review)
3. **Final Payment 30%** - Setelah deployment & UAT (User Acceptance Testing) approval

**Metode Transfer:**
- Bank BCA / Mandiri / BNI
- Invoice akan dikirim sebelum setiap pembayaran

### Yang Termasuk dalam Harga

✅ **Source Code & Ownership**
- Full source code ownership (unlimited license)
- No recurring license fees
- Complete project files (backend + frontend)

✅ **Deployment**
- VPS setup & configuration (1x)
- Nginx reverse proxy setup
- SSL certificate installation (Let's Encrypt)
- Database migration & seeding

✅ **Domain & SSL**
- Domain registration (1 tahun pertama)
- SSL certificate (free, via Let's Encrypt)

✅ **Documentation**
- Technical documentation (architecture, API specs)
- Database schema documentation
- Deployment guide (step-by-step)
- Admin user guide (how to manage locations)
- API documentation (if applicable)

✅ **Training & Handover**
- Training session untuk admin (1-2x sesuai paket)
- Knowledge transfer session
- Q&A support

### Yang TIDAK Termasuk

❌ **Recurring Costs**
- Biaya VPS/hosting bulanan (~$12-30/bulan atau Rp 180k-450k/bulan)
- Domain renewal setelah tahun pertama (~Rp 200k/tahun)

❌ **Content & Data**
- Data entry lokasi (jika belum ada data)
- Foto/gambar lokasi
- Content creation

❌ **Major Changes**
- Perubahan major features setelah UAT approval
- Redesign UI/UX setelah finalisasi
- Additional features di luar scope paket

❌ **Third-party Services**
- Google Maps API key (jika pakai Google Maps - optional)
- Email service (SMTP) untuk notifications
- SMS gateway untuk notifications

---

## ⚙️ Development Process

### 1. Kickoff Meeting (Week 0)
- Requirement gathering & clarification
- Review mockups/wireframes
- Finalize tech stack & architecture
- Setup project management tools
- DP 40% payment

### 2. Development Phase (Week 1-4/5/7)

**Sprint 1 (Week 1-2): Core MVP**
- Database setup (PostgreSQL + PostGIS)
- Backend API development (CRUD endpoints)
- Frontend map integration
- Basic admin panel
- Initial deployment

**Sprint 2 (Week 3): Enhanced Features**
- Detailed location popup
- CSV bulk upload
- Advanced admin panel
- Real-time updates (polling/WebSocket)
- Performance optimization

**Sprint 3 (Week 4): Polish & Testing** (STANDARD/PREMIUM)
- UI/UX improvements
- Bug fixes
- Load testing
- Security audit
- Documentation

**Sprint 4 (Week 5-7): Premium Features** (PREMIUM only)
- Analytics dashboard
- Public API
- Multi-language
- PWA optimization
- Advanced security

### 3. Testing & UAT (Last week)
- Internal testing (unit, integration, E2E)
- UAT with client
- Bug fixes & adjustments
- Progress payment 30%

### 4. Deployment & Handover
- Production deployment
- Training session
- Documentation delivery
- Final payment 30%
- Warranty period starts

### 5. Support Period
- Bug fixes & minor updates (sesuai paket)
- Email/WhatsApp support
- Monthly check-ins (PREMIUM)

---

## 🎨 Portfolio & Past Projects

Berikut adalah beberapa project mapping & geospatial yang pernah kami kerjakan:

### 1. Project Detail MAP - Hybrid & Satellite Views
![Project Detail MAP Hybrid](./assets/images/porfolio/Project%20Detail%20MAP%20Hybrid.png)
![Project Detail MAP Satelit](./assets/images/porfolio/Project%20Detail%20MAP%20Satelit.png)

**Features:**
- Multiple map layer support (Hybrid, Satellite, Streets)
- Custom markers & popups
- Location detail panel

---

### 2. KML Tagging & Map Preview
![Tagging KML MAP Preview](./assets/images/porfolio/Tagging%20KML%20MAP%20Preview.png)
![Tagging KML MAP Streets Preview](./assets/images/porfolio/Tagging%20KML%20MAP%20Streets%20Preview.png)
![Project Detail KML](./assets/images/porfolio/Project%20Detail%20KML.png)

**Features:**
- KML file import & visualization
- Geographic tagging system
- Multi-layer map support
- Custom styling per region

---

### 3. Point Info Data - Right Panel
![Point Info Data Right Panel](./assets/images/porfolio/point-info-data-right-panel.png)

**Features:**
- Detailed location information panel
- Data-rich popup displays
- Clean & modern UI design

---

### 4. Technician Tracking System
![Technician Tracking](./assets/images/porfolio/technician-tracking.png)

**Features:**
- Real-time location tracking
- Status monitoring
- Route optimization
- Similar use case to VinFast project

---

### 5. Additional Dashboard & Map Interfaces
![Dashboard 1](./assets/images/porfolio/photo_2025-10-31_21-10-45.jpg)
![Screenshot 1](./assets/images/porfolio/Screenshot%202025-10-31%20212244.png)
![Screenshot 2](./assets/images/porfolio/Screenshot%202025-10-31%20212423.png)
![Dashboard 2](./assets/images/porfolio/image.png)

**Features:**
- Complex data visualization
- Multi-panel dashboards
- Real-time data updates
- Professional UI/UX design

---

### Client Testimonials

> *"Professional, responsive, dan hasil sesuai ekspektasi. Sistem mapping yang dibangun sangat membantu operasional kami."*
> — **Previous Client, Logistics Company**

> *"Technical expertise yang solid, terutama dalam handling geographic data dan real-time updates."*
> — **Previous Client, Field Service Management**

---

## 🔒 Quality Assurance & Guarantees

### Quality Standards

✅ **Code Quality**
- Clean, well-documented code
- Consistent coding standards
- Comprehensive comments
- Modular architecture

✅ **Security**
- OWASP Top 10 compliance
- SQL injection prevention (parameterized queries)
- XSS prevention (input sanitization)
- CSRF protection (JWT tokens)
- Password hashing (bcrypt)
- HTTPS enforcement
- Rate limiting & DDoS protection

✅ **Performance**
- API response time < 200ms (p95)
- Database query optimization
- Proper indexing (especially spatial indexes)
- Caching strategies
- Lazy loading & code splitting

✅ **Compatibility**
- Cross-browser support (Chrome, Firefox, Safari, Edge)
- Mobile responsive (iOS & Android)
- Tablet optimization
- Desktop support (Windows, macOS, Linux)

✅ **Testing**
- Unit tests (backend logic)
- Integration tests (API endpoints)
- E2E tests (user flows)
- Load testing (stress test)
- Security testing

### Deployment Guarantees

✅ **Infrastructure**
- VPS properly configured & secured
- SSL/HTTPS configured correctly
- Firewall rules (UFW: ports 22, 80, 443 only)
- Auto-restart on failure (systemd)
- Health check automation

✅ **Monitoring**
- Basic monitoring scripts included
- Health check endpoint (/health)
- Error logging (structured logs)
- Database backup automation (optional add-on)

✅ **Documentation**
- Complete README with setup instructions
- API documentation (endpoints, params, responses)
- Database schema documentation (ERD)
- Deployment guide (step-by-step)
- Admin user guide (screenshots)

---

## 📞 Contact & Next Steps

### Interested in This Project?

Kami siap untuk discuss lebih detail tentang project VinFast Location Status Map ini.

**Contact Information:**
- **Email:** [your-email@example.com]
- **WhatsApp:** [+62-xxx-xxxx-xxxx]
- **Portfolio:** [your-portfolio-website.com]
- **GitHub:** [github.com/your-username]

### Next Steps:

1. **Schedule Call** - Mari kita schedule call/meeting untuk discuss requirements lebih detail
2. **Review Proposal** - Review proposal ini dan tentukan paket yang sesuai
3. **Q&A Session** - Tanya jawab tentang technical approach & features
4. **Finalize Scope** - Finalisasi scope of work & timeline
5. **Kickoff** - Sign agreement & kickoff project!

### Availability:

Saya available untuk meeting/call:
- **Weekdays:** 19:00 - 23:00 WIB
- **Video Call:** Google Meet / Zoom
- **In-person:** Jakarta (jika diperlukan)

**Response Time:** < 24 jam untuk WhatsApp

---

## 📅 Project Timeline Estimate

### PAKET MVP (2-3 Minggu)
```
Week 1: [████████████████████] Backend + Database (80%)
Week 2: [████████████████████] Frontend + Admin (80%)
Week 3: [██████████          ] Testing + Deployment (40%)
```

### PAKET STANDARD (4-5 Minggu)
```
Week 1: [████████████████████] Backend Core (100%)
Week 2: [████████████████████] Frontend Map (100%)
Week 3: [████████████████████] Admin Panel (100%)
Week 4: [████████████████████] Features + Polish (100%)
Week 5: [██████████          ] Testing + Deploy (50%)
```

### PAKET PREMIUM (6-7 Minggu)
```
Week 1-2: [████████████████████] Core Development (100%)
Week 3-4: [████████████████████] Advanced Features (100%)
Week 5:   [████████████████████] Analytics + API (100%)
Week 6:   [████████████████████] Premium Features (100%)
Week 7:   [██████████          ] Testing + Deploy (50%)
```

---

## ❓ FAQ (Frequently Asked Questions)

### 1. Apakah harga bisa nego?
Ya, harga dapat disesuaikan tergantung scope dan requirements. Kontak kami untuk discuss lebih lanjut.

### 2. Apakah ada garansi jika ada bug?
Ya, setiap paket include bug fixes selama periode warranty (2 minggu - 3 bulan sesuai paket).

### 3. Apakah bisa custom features di luar paket?
Ya, bisa. Custom features dapat ditambahkan dengan additional cost. Lihat section Add-ons.

### 4. Apakah source code diserahkan?
Ya, full source code ownership diserahkan ke client setelah final payment.

### 5. Berapa biaya hosting per bulan?
Estimasi $12-30/bulan (~Rp 180k-450k/bulan) untuk VPS. Kami bisa bantu setup atau provide managed hosting.

### 6. Apakah support Google Maps API?
Ya, bisa pakai Google Maps API (client perlu provide API key sendiri) atau OpenStreetMap (free).

### 7. Berapa lama waktu deployment?
Deployment ke production server memakan waktu 1-2 hari setelah code final approval.

### 8. Apakah bisa integrate dengan sistem existing?
Ya, bisa. Lihat add-on "Custom Integration" untuk details.

### 9. Apakah ada recurring fees?
Tidak ada recurring license fees. Hanya biaya hosting/domain renewal yang bersifat ongoing.

### 10. Apakah bisa dibuat mobile app?
Ya, bisa. Lihat add-on "Mobile App (React Native)" untuk native iOS + Android app.

---

## 📄 Appendix

### A. Technical Specifications

**System Requirements:**
- VPS: 2 vCPU, 2GB RAM minimum
- Storage: 20GB SSD minimum
- Bandwidth: 1TB/month minimum
- OS: Ubuntu 22.04 LTS or newer

**Browser Support:**
- Chrome 90+
- Firefox 88+
- Safari 14+
- Edge 90+

**Mobile Support:**
- iOS 13+
- Android 8.0+

### B. Service Level Agreement (SLA)

**Uptime:** 99.5% (STANDARD & PREMIUM packages)
**Response Time:** < 24 hours for support requests
**Bug Fix Time:** < 72 hours for critical bugs (during warranty period)

### C. Data Privacy & Security

- Data stored di server Indonesia (optional)
- GDPR-compliant data handling
- Regular security updates
- Encrypted data transmission (HTTPS)
- Secure authentication (JWT)

---

**Document Version:** 1.0
**Last Updated:** Januari 2026
**Valid Until:** Maret 2026

---

<div align="center">

### 🚀 Ready to Build Your VinFast Location Map?

**Let's discuss your requirements and get started!**

---

*This proposal is confidential and intended solely for the use of VinFast team.*

</div>
