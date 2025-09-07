# LoyalTea Demo Suite v2.0
*Interactive HTML demonstrations based on final architecture*

## 🎯 Overview

Complete demo suite showcasing the LoyalTea multi-tenant loyalty platform based on the **FINAL architecture** documented in the `/docs` folder. These demos reflect the simplified 2-domain approach, campaign-based system, and Malaysian SME focus.

## 📁 Demo Files

### 1. **index.html** - Complete Customer Journey
**Multi-tenant customer experience with campaign system**
- Landing page with tenant branding (app.loyaltea.my/kedaiA/customer)
- Registration flow with Malaysian phone format
- Multiple campaign selection (for Business tier customers)
- Apple/Google Wallet integration demonstration
- Customer dashboard with loyalty progress

### 2. **owner-dashboard.html** - Business Owner Interface  
**Complete owner experience for campaign management**
- Dashboard overview with tier-based limits
- Campaign creation wizard (Basic → Business tier upgrade flow)
- Card editor with tier-based customization options
- Staff management interface
- Analytics and QR code generation

### 3. **staff-scanner.html** - Staff Interface
**Mobile-optimized QR scanner for loyalty transactions**
- Staff login interface (app.loyaltea.my/login)
- QR code scanner with camera simulation
- Customer profile display with multiple campaigns
- Transaction processing (stamps, points, rewards)
- Real-time wallet updates

### 4. **superadmin-dashboard.html** - Platform Management
**Superadmin interface for multi-tenant platform oversight**
- Platform metrics and business overview
- Tenant management with tier limitations
- Billing and subscription management
- Support ticket handling

### 5. **card-types-showcase.html** - All 8 Card Types
**Complete loyalty card specifications with Malaysian examples**
- All 8 card types with local business use cases
- Campaign-based implementation examples
- Wallet pass previews for each type
- Business tier recommendations

## 🎯 Key Features Demonstrated

### Architecture Updates (v2.0)
- ✅ **2-domain structure**: dashboard.loyaltea.my + app.loyaltea.my
- ✅ **Multi-tenant routing**: Path-based tenant resolution
- ✅ **Campaign system**: Multiple cards per business
- ✅ **3-tier pricing**: Basic (RM99) → Business (RM199) → Multi-Branch (RM299)
- ✅ **Malaysian focus**: Local business examples and pricing

### User Flow Improvements
- ✅ **Simplified navigation**: No complex subdomain management
- ✅ **Tier-based features**: Clear upgrade paths and limitations
- ✅ **Malaysian UX**: WhatsApp support, local payment methods
- ✅ **Mobile-first design**: Optimized for Malaysian smartphone usage

## 🚀 How to Use

1. **Start with Customer Journey**: Open `index.html` to see complete customer experience
2. **Explore Business Management**: Open `owner-dashboard.html` for business owner interface
3. **Test Staff Operations**: Open `staff-scanner.html` for daily staff workflows  
4. **Review Platform Management**: Open `superadmin-dashboard.html` for platform oversight
5. **Understand Card Types**: Open `card-types-showcase.html` for complete specifications

## 📱 Mobile Testing

All demos are mobile-responsive and optimized for:
- **iPhone Safari** (Primary target for wallet integration)
- **Android Chrome** (Google Wallet support)
- **Malaysian smartphones** (Various screen sizes and connectivity)

## 🎨 Visual Design

**Consistent with Documentation**:
- Malaysian business branding examples
- Clean, simple interfaces suitable for SME owners
- Mobile-first responsive design
- Clear navigation and user feedback

**Interactive Elements**:
- Clickable buttons with realistic workflows
- Form validation and error handling
- Loading states and success animations
- Modal dialogs and confirmation screens

---

**These demos provide a complete visual representation of the LoyalTea platform as documented in `/docs`, ready for developer implementation and stakeholder review.**