# LoyalTea Project Documentation
*Complete documentation for Malaysian SME loyalty platform*

## 📋 Quick Start

**For Business Strategy**: Start with [01-BUSINESS-OVERVIEW.md](01-BUSINESS-OVERVIEW.md)  
**For Development**: Start with [04-TECHNICAL-GUIDE.md](04-TECHNICAL-GUIDE.md)  
**For User Experience**: Start with [03-USER-FLOWS.md](03-USER-FLOWS.md)  

---

## 📚 Complete Documentation Structure

| Document | Purpose | Target Audience |
|----------|---------|----------------|
| [00-DOCUMENT-INDEX.md](00-DOCUMENT-INDEX.md) | Navigation guide | All team members |
| [01-BUSINESS-OVERVIEW.md](01-BUSINESS-OVERVIEW.md) | Business strategy & Malaysian market | Business planning |
| [02-LOYALTY-CARD-SPECIFICATIONS.md](02-LOYALTY-CARD-SPECIFICATIONS.md) | Card types & specifications | Product design |
| [03-USER-FLOWS.md](03-USER-FLOWS.md) | Complete user journeys | UX design & development |
| [04-TECHNICAL-GUIDE.md](04-TECHNICAL-GUIDE.md) | Implementation guide | Development team |
| [05-SECURITY-COMPLIANCE.md](05-SECURITY-COMPLIANCE.md) | Security & Malaysian compliance | Development & legal |

---

## 🎯 Project Overview

**What**: Multi-tenant SaaS loyalty platform for Malaysian SMEs  
**Who**: 2-person development team  
**Where**: Malaysia (Klang Valley focus initially)  
**When**: 6-month development timeline  

### Key Architecture Decisions
- **Domains**: dashboard.loyaltea.my (admin) + app.loyaltea.my (customer/staff)
- **Multi-tenancy**: Path-based routing (app.loyaltea.my/kedaiA/customer)
- **Campaign System**: Multiple loyalty cards per business
- **Wallet Integration**: Apple & Google Wallet via Infyra infrastructure
- **Technology Stack**: Vue 3 + Node.js + PostgreSQL

### Simplified Pricing Strategy
- **Basic (RM99)**: 1 campaign, 500 customers, single location
- **Business (RM199)**: 3 campaigns, 1,500 customers, enhanced features  
- **Multi-Branch (RM299)**: 3 locations, unlimited campaigns, 2K customers/branch

---

## 🚀 What We're Building (Scope)

### ✅ **Core Features (Essential)**
- Multi-tenant loyalty platform with 8 card types
- Campaign-based system (multiple cards per business)
- Apple & Google Wallet integration
- Malaysian payment methods (FPX, bank transfer)
- Basic PDPA compliance and security
- Simple owner dashboard and staff scanner

### ❌ **Out of Scope (Too Complex for 2-Person Team)**
- White-label solutions or custom branding
- Enterprise features (API access, advanced automation)
- POS system integrations
- Real-time payment processing
- Advanced analytics and reporting
- Multiple locations per tier (except Multi-Branch)

---

## 🇲🇾 Malaysian Market Focus

### Target Businesses
- **Kedai Kopi & Traditional Cafes** (Basic tier)
- **Restaurants & Modern F&B** (Business tier)
- **Beauty Salons & Service Providers** (Business tier)
- **Small Retail Chains** (Multi-Branch tier)

### Local Considerations
- Malaysian phone number format (+60)
- Ringgit Malaysia (RM) pricing
- Local payment methods (FPX, bank transfer)
- Malaysian business categories and examples
- PDPA compliance requirements
- WhatsApp/phone support culture

---

## 📈 Development Timeline

### Phase 1: Foundation (Months 1-2)
- Multi-tenant database and authentication
- Basic campaign creation and management
- Simple domain routing and tenant resolution
- Customer registration with wallet integration

### Phase 2: Core Features (Months 3-4)
- Owner dashboard with campaign management
- Customer landing pages with branding
- Staff QR scanner interface
- Card editor with tier-based features

### Phase 3: Malaysian Features (Months 5-6)
- Local payment integration (FPX)
- PDPA compliance features
- Bahasa Malaysia customer interface
- Business onboarding and support tools

---

## 🛠️ Technical Highlights

### Architecture Simplifications
- Single database with row-level security
- Path-based multi-tenancy (no subdomain complexity)
- Centralized wallet pass generation via Infyra
- Simple Redis caching strategy
- Docker-based deployment

### Security & Compliance
- JWT authentication with role-based access
- Database encryption and backup procedures
- Malaysian PDPA compliance built-in
- Basic incident response procedures
- Regular security monitoring

---

## 📞 Support & Development

**Technical Questions**: Review [04-TECHNICAL-GUIDE.md](04-TECHNICAL-GUIDE.md)  
**Business Strategy**: Review [01-BUSINESS-OVERVIEW.md](01-BUSINESS-OVERVIEW.md)  
**User Experience**: Review [03-USER-FLOWS.md](03-USER-FLOWS.md)  
**Security Concerns**: Review [05-SECURITY-COMPLIANCE.md](05-SECURITY-COMPLIANCE.md)  

---

## ✅ Documentation Status

**Last Updated**: September 2024  
**Status**: Complete and ready for development  
**Team Review**: Approved for 2-person team implementation  
**Next Review**: After Phase 1 completion (Month 2)  

---

**This documentation provides a complete, practical roadmap for building a Malaysia-focused SME loyalty platform without over-engineering or unrealistic enterprise complexity.**