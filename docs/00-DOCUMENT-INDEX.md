# LoyalTea Documentation Index
*Complete project documentation for 2-person team development*

## 📋 Documentation Structure

### 📊 **For Business Planning**
Strategic overview and market approach:

1. **[01-BUSINESS-OVERVIEW.md](01-BUSINESS-OVERVIEW.md)** - Complete business strategy
   - Malaysia SME market focus and 3-tier pricing
   - Target customer segments and revenue projections
   - Go-to-market strategy for 2-person team

### 🎯 **For User Experience**
Complete user journeys and specifications:

2. **[02-LOYALTY-CARD-SPECIFICATIONS.md](02-LOYALTY-CARD-SPECIFICATIONS.md)** - Card types specification
   - 8 loyalty card types with Malaysian business examples
   - Universal QR code system and wallet integration

3. **[03-USER-FLOWS.md](03-USER-FLOWS.md)** - Complete user journeys
   - **Web portal primary customer experience** (NEW)
   - OTP-based customer authentication and login
   - Multi-tenant customer flows with branded experiences
   - Campaign-based workflows for all user roles
   - Optional wallet integration (Apple/Google Wallet)
   - Simple domain structure (dashboard.loyaltea.my + app.loyaltea.my)

### 🛠️ **For Development**
Technical implementation for small team:

4. **[04-TECHNICAL-GUIDE.md](04-TECHNICAL-GUIDE.md)** - Development implementation
   - Multi-tenant database architecture
   - **Customer web portal authentication (OTP-based)** (NEW)
   - **Session management with Redis** (NEW)
   - Campaign-based system with optional wallet integration
   - Simple deployment strategy for 2-person team

### 🔐 **For Security & Compliance**
Essential security and legal requirements:

5. **[05-SECURITY-COMPLIANCE.md](05-SECURITY-COMPLIANCE.md)** - Security framework
   - Multi-tenant data protection
   - Malaysian PDPA compliance essentials
   - Basic security measures for small team

---

## 🎯 Quick Navigation by Role

### **Business Strategy & Planning**
- **Start**: [01-BUSINESS-OVERVIEW.md](01-BUSINESS-OVERVIEW.md)
- Market analysis, pricing strategy, and realistic revenue projections
- Malaysian SME focus with 3-tier approach (no enterprise complexity)

### **Development & Implementation**  
- **Start**: [04-TECHNICAL-GUIDE.md](04-TECHNICAL-GUIDE.md)
- Multi-tenant architecture with campaign system
- Practical implementation for 2-person development team

### **User Experience Design**
- **Start**: [03-USER-FLOWS.md](03-USER-FLOWS.md)  
- Simple user journeys across all roles
- Campaign-based loyalty card management

### **Compliance & Security**
- **Start**: [05-SECURITY-COMPLIANCE.md](05-SECURITY-COMPLIANCE.md)
- Essential security measures and Malaysian compliance

---

## 🎯 Team Focus Areas

### **What We're Building (Realistic Scope)**
✅ **Multi-tenant SaaS**: Multiple businesses on 2 simple domains
✅ **Campaign system**: Multiple loyalty cards per business
✅ **Web portal primary**: Customer login and dashboard (OTP-based)
✅ **Optional wallet**: Apple/Google Wallet integration
✅ **3-tier pricing**: Basic (RM99) → Business (RM199) → Multi-Branch (RM299)
✅ **Malaysian focus**: Local payment methods and compliance

### **What We're NOT Building (Out of Scope)**
❌ **White-label solutions**: Too complex for 2-person team  
❌ **Enterprise features**: API access, advanced automation  
❌ **POS integrations**: Third-party system complexity  
❌ **Payment processing**: Focus on manual billing initially  
❌ **Advanced analytics**: Basic reporting sufficient  
❌ **Multiple locations per tier**: Keep it simple

---

## 📊 Document Status

| Document | Status | Purpose |
|----------|--------|---------|
| 01-BUSINESS-OVERVIEW.md | ✅ **Current** | Business strategy for Malaysian SMEs |
| 02-LOYALTY-CARD-SPECIFICATIONS.md | ✅ **Updated Jan 2025** | Card types with web portal access |
| 03-USER-FLOWS.md | ✅ **Updated Jan 2025** | Web-first user journeys with OTP auth |
| 04-TECHNICAL-GUIDE.md | ✅ **Updated Jan 2025** | Includes customer portal authentication |
| 05-SECURITY-COMPLIANCE.md | ✅ **Current** | Essential security measures |
| CHANGELOG-WEB-PORTAL-UPDATE.md | 🆕 **New Jan 2025** | Complete update documentation |

---

## 🚀 Development Priorities

### **Phase 1: Core System (Months 1-2)**
1. Multi-tenant database with campaign support
2. Basic domain routing (dashboard + app domains)
3. Simple campaign creation and management
4. **Customer web portal with OTP authentication** (UPDATED)
5. **Customer registration and login system** (NEW)
6. **Session management with Redis** (NEW)

### **Phase 2: User Interfaces (Months 3-4)**
1. Owner dashboard with campaign management
2. **Customer dashboard with QR code display** (UPDATED)
3. **Customer transaction history and profile** (NEW)
4. Staff QR scanner interface
5. Basic card editor
6. **Optional: Wallet download functionality** (NEW)

### **Phase 3: Malaysian Features (Months 5-6)**
1. Local payment methods (FPX, bank transfer)
2. Basic PDPA compliance features
3. Malaysian business categories
4. Bahasa Malaysia customer interface

---

**This documentation focuses on practical implementation for a 2-person team building a Malaysia-focused SME loyalty platform without over-engineering or complex enterprise features.**