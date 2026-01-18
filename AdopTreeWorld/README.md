# AdopTree World - Documentation

## Overview

Welcome to the AdopTree World documentation! This comprehensive documentation suite covers all aspects of the tree adoption platform, from user guides to testing procedures.

**AdopTree World** is a tree adoption platform connecting donors with conservation lands to combat climate change through reforestation. The platform supports individual donors, land providers (merchants), corporate sustainability programs, and platform administrators.

---

## 📚 Documentation Index

### [Feature Status Report](FEATURE-STATUS.md)
**Complete platform feature inventory and implementation status**

View the full breakdown of implemented features across backend and frontend:
- **Backend**: 98% complete (92 out of 94 features)
- **Frontend**: 95% complete
- Database-ready features (NFT tracking, Games/Quests)
- Technology stack summary
- Production vs. development status

**Read this first** to understand what's built and what's planned.

---

### [Donor Guide](DONOR-GUIDE.md)
**Complete user journey for tree adopters**

Everything donors need to know about:
- Registration (4 methods: Email, Google OAuth, Solana Wallet, Guest)
- Browsing conservation lands
- Shopping cart and checkout (3-step process)
- Payment methods (6 options including crypto)
- Dashboard features (Journey, Certificates, Activity)
- Profile management and security
- Carbon credits and environmental impact tracking

**For**: Individual users who want to adopt trees

---

### [Merchant Guide](MERCHANT-GUIDE.md)
**Land provider registration and management**

Complete merchant workflow:
- Becoming a merchant (application and verification)
- Land management (creation, boundaries with GeoJSON)
- Tree management (individual and bulk CSV import)
- GPS coordinate mapping
- Earnings tracking and payout requests
- Best practices for photos and data accuracy
- Mermaid diagrams for workflows

**For**: Conservation land owners, reforestation organizations

---

### [Admin Guide](ADMIN-GUIDE.md)
**Platform administration and management**

Administrative functions:
- Admin dashboard and statistics
- Merchant approval workflow (4 stages)
- User management (ban, unban, role changes)
- Transaction monitoring and manual verification
- System health monitoring
- Content moderation (reviews)
- Admin best practices and security

**For**: Platform administrators and super admins

---

### [Corporate Guide](CORPORATE-GUIDE.md)
**Corporate/B2B features and bulk adoption**

Enterprise functionality:
- Corporate account overview (3 subscription tiers)
- Team management (4 roles: Owner, Admin, Member, Viewer)
- Bulk adoptions with employee gifting
- ESG reporting (Environmental, Social, Governance)
- API integration and credentials
- Webhook configuration
- Corporate workflows with Mermaid diagrams

**For**: Companies, CSR departments, enterprise users

---

### [Testing Guide](TESTING-GUIDE.md)
**End-to-end testing scenarios and quality assurance**

Comprehensive testing documentation:
- Testing environment setup
- Super admin access credentials
- Donor test scenarios (9 scenarios)
- Merchant test scenarios (4 scenarios)
- Admin test scenarios (4 scenarios)
- Corporate test scenarios (2 scenarios)
- Edge case testing (10 tests)
- Performance and security testing
- Pre-launch checklist (80+ items)

**For**: QA testers, developers, product managers

---

## 🚀 Quick Start by User Type

### I'm a Donor
1. Read: [Donor Guide](DONOR-GUIDE.md)
2. Go to: https://staging.adoptreeworld.com/
3. Register and start adopting trees
4. Download your certificates

### I'm a Land Provider
1. Read: [Merchant Guide](MERCHANT-GUIDE.md)
2. Apply: https://staging.adoptreeworld.com/merchant/register
3. Wait for approval (3-5 business days)
4. Add your conservation lands and trees

### I'm an Administrator
1. Read: [Admin Guide](ADMIN-GUIDE.md)
2. Login: https://staging.adoptreeworld.com/auth/login
   - Email: `admin@adoptreeworld.com`
   - Password: `Admin123!`
3. Access admin dashboard
4. Approve merchants, monitor platform

### I'm from a Company
1. Read: [Corporate Guide](CORPORATE-GUIDE.md)
2. Apply: https://staging.adoptreeworld.com/corporate/register
3. Setup team and bulk adoptions
4. Generate ESG reports

### I'm a Tester/Developer
1. Read: [Testing Guide](TESTING-GUIDE.md)
2. Check: [Feature Status Report](FEATURE-STATUS.md)
3. Use staging environment for testing
4. Follow test scenarios

---

## 🌍 Environment Information

### Staging Environment
**URL**: https://staging.adoptreeworld.com/

**Purpose**: Safe testing environment separate from production
- Test all features without affecting real users
- Midtrans payment test mode (no real charges)
- Data can be reset periodically
- Separate database and services

**Super Admin Access** (Staging Only):
```
Email: admin@adoptreeworld.com
Password: Admin123!
Role: SuperAdmin (full platform access)
```

**⚠️ CRITICAL**: Only test on staging, never on production!

---

### Production Environment
**URL**: https://adoptreeworld.com/

**⚠️ DO NOT TEST ON PRODUCTION**
- Live users and real money
- No test accounts allowed
- Only for actual users

---

## 🎯 Platform Features Overview

### User Roles
- **Guest**: Browse and adopt without account
- **Donor**: Adopt trees, track impact, download certificates
- **Merchant**: Manage lands and trees, earn from adoptions
- **MerchantStaff**: Assist with merchant operations
- **CorporateAdmin**: Manage corporate sustainability programs
- **CorporateMember**: Create adoptions for company
- **Admin**: Platform moderation and merchant approval
- **SuperAdmin**: Full platform control

### Authentication Methods
1. **Email/Password**: Traditional registration with Argon2 hashing
2. **Google OAuth**: One-click signup with Google account
3. **Solana Wallet**: Web3 authentication (SIWS protocol)
4. **Guest Checkout**: Adopt without registration (upgradeable later)

### Adoption Tiers
| Tier | Price | Duration | Features |
|------|-------|----------|----------|
| **Donation** | $8 | 1 year | Basic support, certificate |
| **Wakaf** | $10 | Perpetual | Permanent adoption, certificate |
| **GreenSociety** | $12 | 1 year | Community tier, certificate |
| **AdoptTree** | $75 | 5 years | Premium long-term, priority updates |

### Payment Methods
1. **Midtrans Snap**: All-in-one payment gateway
2. **Bank Transfer**: Virtual account (multiple banks)
3. **E-Wallet**: GoPay, OVO, LinkAja, DANA
4. **QRIS**: Scan QR code payment
5. **Solana**: Cryptocurrency payment
6. **Credit Card**: Coming soon

### Core Features
- ✅ Tree adoption with 4 product tiers
- ✅ Certificate generation with QR codes
- ✅ Carbon credit calculation and tracking
- ✅ Land management with GPS boundaries
- ✅ Bulk tree import via CSV
- ✅ Corporate bulk adoptions with employee gifting
- ✅ ESG report generation
- ✅ API integration with webhooks
- ✅ Multi-language support (planned)
- ⚠️ NFT tracking (database ready, UI pending)
- ⚠️ Gamification (database ready, UI pending)

---

## 🛠️ Technology Stack

### Backend
- **Language**: Rust
- **Framework**: Axum
- **Database**: PostgreSQL with SQLx
- **Cache**: Redis
- **Authentication**: JWT (access + refresh tokens)
- **Password**: Argon2 hashing
- **Payment**: Midtrans integration
- **Email**: SMTP (Lettre)
- **Storage**: S3 + local filesystem
- **API**: RESTful JSON API

### Frontend
- **Framework**: Next.js 14+ (App Router)
- **UI Library**: React 18+
- **Styling**: Tailwind CSS + Shadcn/ui
- **Forms**: React Hook Form + Zod validation
- **State**: Zustand
- **Maps**: Leaflet + Turf.js
- **HTTP**: Axios

### DevOps
- **CI/CD**: Jenkins
- **Containers**: Docker multi-stage builds
- **Auto-Update**: Watchtower
- **Reverse Proxy**: Nginx
- **Monitoring**: System health dashboard
- **Notifications**: Discord webhooks

---

## 📊 Platform Statistics (as of documentation)

**Implementation Status:**
- Backend Features: **98% Complete** (92/94)
- Frontend Features: **95% Complete**
- Total Lines of Code: ~50,000+
- Documentation Pages: 6 comprehensive guides
- Test Scenarios: 19+ end-to-end scenarios
- Supported Roles: 8 user roles
- Payment Methods: 6 (5 live, 1 coming soon)
- Authentication Methods: 4

**Feature Breakdown:**
- Authentication: 4 methods
- User Management: 8 roles with RBAC
- Tree Management: Full CRUD + bulk import + QR
- Adoption System: 4 tiers with full lifecycle
- Payment Integration: Midtrans with 6 methods
- Land Management: GeoJSON boundaries
- Merchant System: Verification, earnings, payouts
- Corporate/B2B: Team, bulk, ESG, API
- Certificates: PDF generation + QR verification
- Carbon Credits: Real-time calculation + projections
- Reviews & Ratings: CRUD + moderation
- Notifications: Email/Push/SMS with preferences
- Admin System: Approvals, statistics, monitoring

---

## 🔄 Documentation Updates

This documentation is maintained alongside the codebase. When features change:

1. **Feature Status Report**: Update implementation percentages
2. **User Guides**: Add new features, update screenshots
3. **Testing Guide**: Add test scenarios for new features
4. **API Documentation**: Update endpoints and examples

**Last Updated**: January 2025
**Documentation Version**: 1.0
**Platform Version**: Beta (Pre-Production)

---

## 📝 Contributing to Documentation

When updating documentation:

1. **Maintain Consistency**
   - Use same markdown formatting
   - Follow existing structure
   - Use clear, concise language
   - Include examples and screenshots

2. **Update All Relevant Files**
   - If feature changes, update guide
   - If UI changes, update screenshots
   - If API changes, update examples

3. **Test Documentation**
   - Follow your own instructions
   - Verify links work
   - Test code examples
   - Check Mermaid diagrams render

4. **Keep Professional Tone**
   - Medium-level detail (not too technical, not too high-level)
   - Active voice ("Click the button" not "The button should be clicked")
   - Clear expected results
   - Troubleshooting sections

---

## 🆘 Getting Help

### For Users
- **Email Support**: support@adoptreeworld.com
- **Documentation**: This repository
- **Staging Testing**: https://staging.adoptreeworld.com/

### For Developers
- **Technical Issues**: Check GitHub issues
- **API Questions**: api-support@adoptreeworld.com
- **Code Review**: See CONTRIBUTING.md (if exists)

### For Testers
- **Test Scenarios**: [Testing Guide](TESTING-GUIDE.md)
- **Bug Reports**: Document with template in Testing Guide
- **Staging Access**: Use super admin credentials

### For Admins
- **Admin Guide**: [Admin Guide](ADMIN-GUIDE.md)
- **Super Admin**: admin@adoptreeworld.com / Admin123! (staging)
- **Admin Training**: Available upon request

---

## 🔐 Security and Privacy

**Important Security Notes:**

1. **Credentials in Documentation**
   - Super admin credentials are for **staging only**
   - Production credentials are **never** in documentation
   - Rotate staging credentials periodically

2. **Testing Safety**
   - Always test on staging environment
   - Never use real payment details on staging
   - Test data can be reset without notice

3. **Data Privacy**
   - User data is encrypted at rest
   - Passwords hashed with Argon2
   - GDPR compliant (data export/deletion)
   - No sensitive data in logs

4. **Reporting Vulnerabilities**
   - Email: security@adoptreeworld.com
   - Do not disclose publicly until patched
   - Responsible disclosure policy

---

## 🎉 Success Stories

**What You Can Do With AdopTree World:**

### As a Donor
- Adopt trees in conservation areas worldwide
- Track your environmental impact (CO2, carbon credits)
- Receive beautiful certificates with QR verification
- Gift adoptions to friends and family
- View your trees on interactive maps

### As a Merchant
- Register your conservation land
- Earn revenue from tree adoptions
- Manage trees with GPS tracking
- Bulk import trees via CSV
- Track earnings and request payouts

### As a Company
- Run corporate sustainability programs
- Adopt trees in bulk for employee gifting
- Generate ESG reports for compliance
- Integrate via API with your systems
- Manage team access with role-based permissions

### As an Administrator
- Oversee platform health
- Approve new merchants
- Monitor transactions
- Moderate content
- View comprehensive statistics

---

## 📞 Contact Information

**General Inquiries**: info@adoptreeworld.com
**Support**: support@adoptreeworld.com
**Sales (Corporate)**: sales@adoptreeworld.com
**Technical**: api-support@adoptreeworld.com
**Security**: security@adoptreeworld.com

**Staging Environment**: https://staging.adoptreeworld.com/
**Production**: https://adoptreeworld.com/ (DO NOT TEST)

---

## 📄 License and Legal

**Platform License**: Proprietary
**Documentation License**: Internal Use Only
**Last Updated**: January 2025

**Terms of Service**: https://adoptreeworld.com/terms
**Privacy Policy**: https://adoptreeworld.com/privacy
**Cookie Policy**: https://adoptreeworld.com/cookies

---

## 🌳 About AdopTree World

**Mission**: Combat climate change through accessible reforestation and transparent tree adoption.

**Vision**: A world where everyone can contribute to environmental conservation through verified, impactful tree adoptions.

**Values**:
- **Transparency**: Every adoption verified with QR codes
- **Impact**: Real trees, real conservation, real impact
- **Accessibility**: Multiple payment methods, multiple languages
- **Community**: Connect donors, land providers, and corporations

**Environmental Impact Goal**: 1 million trees adopted by 2026

---

## 🗺️ Documentation Roadmap

**Upcoming Documentation:**

- [ ] API Reference Documentation (Swagger/OpenAPI)
- [ ] Mobile App User Guide (iOS/Android)
- [ ] Merchant Onboarding Video Tutorials
- [ ] Corporate Integration Examples
- [ ] Multi-language Documentation (ID, ES, FR)
- [ ] Advanced Admin Procedures
- [ ] Database Schema Documentation
- [ ] Architecture Decision Records (ADRs)

---

**🌍 Thank you for being part of the AdopTree World community! Together, we're making the planet greener, one tree at a time. 🌳**

---

*Documentation maintained by the AdopTree World team. For questions or corrections, contact the documentation team.*
