# LoyalTea User Flows
*Complete user journeys for multi-tenant campaign system*

## 🎯 Overview

**Domain Structure**: 2 domains, multi-tenant architecture  
- `dashboard.loyaltea.my` → Owner + Superadmin (role-based access)
- `app.loyaltea.my` → Customer + Staff (multi-tenant, branded)

**Campaign System**: Each business can create multiple campaigns (multiple loyalty cards)  
**Malaysian Focus**: Practical flows designed for local SME businesses  

---

## 👨‍💼 OWNER FLOW

### Login & Business Dashboard
```
Access: dashboard.loyaltea.my/login
↓
Owner Dashboard Overview:
┌─────────────────────────────────────────────────────────┐
│ 🏪 Kedai Kopi Ah Beng                                  │
│ 📍 Semenyih, Selangor | Plan: Basic (RM99/month)       │
│                                                         │
│ 📊 This Month (January 2024)                          │
│ • Total Customers: 89 (+12 new)                        │
│ • Active Campaigns: 1/1 (Basic tier limit)             │
│ • Transactions: 456 stamps given                       │
│ • Rewards Redeemed: 23                                 │
│                                                         │
│ 🎯 Your Campaign                                       │
│ ☕ Coffee Stamp Card                                   │
│ • 89 active customers                                   │
│ • 456 stamps given this month                          │
│ • 23 rewards redeemed                                  │
│ • [View Details] [Edit Card] [Analytics]               │
│                                                         │
│ [+ Create New Campaign] (Upgrade to Business RM199)    │
│ [Manage Staff] [View QR Codes] [Account Settings]      │
└─────────────────────────────────────────────────────────┘
```

### Campaign Creation Flow
```
[+ Create New Campaign] clicked
↓ (If Basic tier - shows upgrade prompt)
Upgrade Required:
┌─────────────────────────────────────────────────────────┐
│ 🚀 Upgrade to Create More Campaigns                    │
│                                                         │
│ Your current plan (Basic - RM99/month) includes:       │
│ ✅ 1 campaign (currently used)                         │
│                                                         │
│ Upgrade to Business (RM199/month) to get:              │
│ ✅ 3 campaigns (2 more available)                      │
│ ✅ Advanced card types (membership, employee badges)   │
│ ✅ Enhanced customization options                      │
│ ✅ Customer segmentation tools                         │
│                                                         │
│ [Upgrade Now] [Maybe Later]                           │
│                                                         │
│ 💡 Popular combinations for cafes:                     │
│ • Coffee Stamp Card (current)                          │
│ • VIP Membership Card (frequent customers)             │
│ • Staff Badge Card (employee perks)                    │
└─────────────────────────────────────────────────────────┘

↓ (If Business tier - proceed to campaign creation)

Campaign Setup Wizard:
┌─────────────────────────────────────────────────────────┐
│ 🎯 Choose Your Campaign Type                           │
│                                                         │
│ ☕ STAMP CARD                    🏆 POINTS CARD         │
│ Perfect for repeat visits        Perfect for purchase-based │
│ "10 stamps = free coffee"       "1 point per RM1 spent"│
│ [Select]                         [Select]               │
│                                                         │
│ 👑 MEMBERSHIP CARD              🏷️ DISCOUNT CARD        │
│ VIP tiers with benefits          Fixed % off purchases   │
│ "Silver, Gold, VIP levels"      "10% off for members"  │
│ [Select] (Business+ only)        [Select]               │
│                                                         │
│ 👨‍💼 EMPLOYEE BADGE               🎫 EVENT CARD           │
│ Staff ID and perks              Temporary campaigns      │
│ "Staff discounts & access"      "Chinese New Year promo"│
│ [Select] (Business+ only)        [Select]               │
└─────────────────────────────────────────────────────────┘

↓ (Example: Stamp Card selected)

Campaign Configuration:
┌─────────────────────────────────────────────────────────┐
│ ⚙️ Configure Your Stamp Card                           │
│                                                         │
│ Campaign Name: [VIP Coffee Card_______________]         │
│                                                         │
│ How many stamps for reward?                             │
│ ○ 5 stamps  ○ 8 stamps  ● 10 stamps  ○ 12 stamps     │
│                                                         │
│ What's the reward?                                      │
│ [Free premium coffee + pastry__________________]        │
│                                                         │
│ After reward claimed:                                   │
│ ● Start new card automatically  ○ Wait for next visit  │
│                                                         │
│ [Back] [Continue to Design →]                         │
└─────────────────────────────────────────────────────────┘

↓

Card Design Editor:
┌─────────────────────────────────────────────────────────┐
│ 🎨 Design Your Card                                    │
│                                                         │
│  Preview                      │  Customize              │
│  ┌─────────────────────────┐  │                        │
│  │  KEDAI KOPI AH BENG    │  │  🖼️ Logo                │
│  │  [☕ Logo]              │  │  [Upload] [Remove]     │
│  │                         │  │  Current: coffee.png   │
│  │  VIP COFFEE CARD        │  │                        │
│  │                         │  │  🎨 Colors             │
│  │  ⭐⭐⭐⭐⭐☆☆☆☆☆    │  │  Theme: ● Classic      │
│  │  5 of 10 stamps         │  │         ○ Modern       │
│  │                         │  │         ○ Vibrant      │
│  │  "5 more for free       │  │                        │
│  │   premium coffee!"      │  │  📝 Card Title:        │
│  │                         │  │  [VIP Coffee Card]     │
│  │  [QR CODE AREA]         │  │                        │
│  └─────────────────────────┘  │  📱 Preview:           │
│                                │  [iPhone] [Android]    │
│                                │                        │
│                                │  [Save] [Launch]       │
└─────────────────────────────────────────────────────────┘
```

### Campaign Launch & QR Generation
```
Card Launch Success:
┌─────────────────────────────────────────────────────────┐
│ 🎉 VIP Coffee Card is Live!                           │
│                                                         │
│ Your campaign is ready for customers:                   │
│                                                         │
│ 📱 Customer Registration:                              │
│ [QR Code Image] ← Print this for your counter          │
│ "Scan to join VIP Coffee Card"                        │
│                                                         │
│ 🔗 Registration Link:                                  │
│ app.loyaltea.my/kedaiA/customer?campaign=vip-coffee    │
│                                                         │
│ 👨‍💼 Staff Access:                                      │
│ Your staff can scan customer cards at:                 │
│ app.loyaltea.my/login (using their login)             │
│                                                         │
│ 📧 Next Steps:                                         │
│ ✅ QR code sent to your email                          │
│ ✅ Staff login details sent via WhatsApp               │
│ ✅ Setup guide emailed to you                          │
│                                                         │
│ [Download QR Codes] [Staff Training] [View Analytics]  │
└─────────────────────────────────────────────────────────┘
```

### Staff Management
```
dashboard.loyaltea.my/staff
↓
Staff Management:
┌─────────────────────────────────────────────────────────┐
│ 👥 Staff Management                                     │
│                                                         │
│ Current Staff (2):                                      │
│                                                         │
│ 👤 Ahmad Rahman - Manager                               │
│ • Phone: 019-123-4567                                  │
│ • Can access: All campaigns                             │
│ • Last used scanner: 1 hour ago                        │
│ • [Edit Access] [Reset Password]                       │
│                                                         │
│ 👤 Siti Aminah - Cashier                              │
│ • Phone: 012-987-6543                                  │
│ • Can access: Coffee Stamp Card only                   │
│ • Last used scanner: Active now ✅                      │
│ • [Edit Access] [Reset Password]                       │
│                                                         │
│ [+ Add New Staff Member]                               │
│                                                         │
│ 📱 Staff Login Instructions:                           │
│ 1. Go to app.loyaltea.my/login                         │
│ 2. Enter phone number + password                       │
│ 3. Use scanner to add stamps/points                    │
│                                                         │
│ Need help training staff? [WhatsApp Support]           │
└─────────────────────────────────────────────────────────┘
```

---

## 👨‍💼 STAFF FLOW

### Staff Login & Scanner Interface
```
Access: app.loyaltea.my/login
↓
Staff Login:
┌─────────────────────────────────────────────────────────┐
│ 👨‍💼 Staff Login                                          │
│                                                         │
│ Phone Number: [019-123-4567______________]              │
│ Password: [••••••••••]                                  │
│                                                         │
│ [Login]                                                │
│                                                         │
│ Forgot password? WhatsApp your manager                 │
│ Need help? Call: 019-123-4567 (Manager)               │
└─────────────────────────────────────────────────────────┘

↓ (Login successful, business context auto-detected)

Staff Scanner Dashboard:
┌─────────────────────────────────────────────────────────┐
│ 👋 Hi Ahmad! - Kedai Kopi Ah Beng                     │
│                                                         │
│                                                         │
│              📱 SCAN CUSTOMER QR                       │
│             [Large Scan Button]                        │
│                                                         │
│                                                         │
│ 📊 Your Activity Today:                               │
│ • Customers helped: 12                                  │
│ • Stamps given: 15                                     │
│ • Rewards redeemed: 2                                  │
│                                                         │
│ 🕐 Recent Activity:                                    │
│ • Siti A. - +1 stamp (3 min ago)                      │
│ • John L. - +1 stamp (8 min ago)                      │
│ • Mary T. - FREE COFFEE redeemed (15 min ago)         │
│                                                         │
│ [Manual Search] [Help] [Logout]                       │
└─────────────────────────────────────────────────────────┘
```

### Customer QR Scan Process
```
[📱 SCAN CUSTOMER QR] tapped
↓
Camera scanning interface opens
↓ (QR Code successfully scanned)

Customer Profile Loaded:
┌─────────────────────────────────────────────────────────┐
│ 👤 Aminah Salleh (VIP Member)                          │
│ 📱 019-234-5678                                        │
│                                                         │
│ ☕ VIP Coffee Card                                     │
│ ⭐⭐⭐⭐⭐⭐⭐☆☆☆                              │
│ 7 of 10 stamps collected                               │
│ 🎯 3 more stamps for FREE PREMIUM COFFEE!              │
│                                                         │
│ 📅 Member since: Dec 2023                             │
│ 🏆 Total stamps earned: 31                            │
│ 🎁 Rewards claimed: 3                                 │
│ 📍 Last visit: 2 days ago                             │
│                                                         │
│              [+ ADD STAMP]                             │
│                                                         │
│ [View History] [Done] [Scan Next Customer]             │
└─────────────────────────────────────────────────────────┘

↓ [+ ADD STAMP] tapped

Confirmation:
┌─────────────────────────────────────────────────────────┐
│ ✅ Stamp Added!                                        │
│                                                         │
│ Customer: Aminah Salleh                                │
│ Progress: 8 of 10 stamps                               │
│ Message: "2 more stamps for free coffee!"              │
│                                                         │
│ 📱 Customer's wallet updated automatically             │
│                                                         │
│           [Scan Next Customer]                         │
│                                                         │
│ ⏱️ Total time: 6 seconds                              │
└─────────────────────────────────────────────────────────┘
```

### Multi-Campaign Staff Interface (Business Tier)
```
For businesses with multiple campaigns:

Customer Profile (Multiple Cards):
┌─────────────────────────────────────────────────────────┐
│ 👤 Ahmad Rahman                                         │
│ 📱 012-345-6789                                         │
│                                                         │
│ Select which card to update:                            │
│                                                         │
│ ☕ Coffee Stamp Card                                   │
│ 7/10 stamps • [+ ADD STAMP]                           │
│                                                         │
│ 👑 VIP Membership                                      │
│ Gold Member • [VIEW BENEFITS] [UPGRADE TIER]          │
│                                                         │
│ 🎫 CNY Special Promotion                               │
│ 2 coupons available • [REDEEM COUPON]                 │
│                                                         │
│ [Customer History] [Done]                              │
└─────────────────────────────────────────────────────────┘
```

---

## 👥 CUSTOMER FLOW

### QR Code Scan & Landing Page
```
Customer scans QR code at store counter
↓
QR redirects to: app.loyaltea.my/kedaiA/join
↓
Branded Landing Page:
┌─────────────────────────────────────────────────────────┐
│ [☕ KEDAI KOPI AH BENG LOGO]                           │
│                                                         │
│ Welcome to Our Loyalty Program! 🎉                     │
│                                                         │
│ ☕ Join Our VIP Coffee Card                            │
│ Collect 10 stamps, get 1 FREE premium coffee!         │
│                                                         │
│ ✅ Access via web or phone wallet                      │
│ 📱 No app download needed                              │
│ ⚡ Quick 30-second signup                              │
│                                                         │
│ Already have an account?                                │
│ [🔑 LOGIN]                                             │
│                                                         │
│           [🚀 JOIN NOW - IT'S FREE!]                   │
│                                                         │
│ 📞 Questions? WhatsApp: 019-123-4567                   │
└─────────────────────────────────────────────────────────┘
```

### Customer Registration
```
[🚀 JOIN NOW] tapped
↓
Registration Form:
┌─────────────────────────────────────────────────────────┐
│ 📝 Join VIP Coffee Card                                │
│ 🏪 Kedai Kopi Ah Beng                                 │
│                                                         │
│ Full Name: [_________________________]                  │
│ Phone: [019-_______________] 🇲🇾                        │
│ Email: [_______________________@___]                    │
│ Birthday: [DD/MM] (for special birthday offers)        │
│                                                         │
│ ☑️ I want to receive special offers via WhatsApp      │
│ ☑️ I agree to the terms and conditions                │
│                                                         │
│           [CREATE MY CARD]                             │
│                                                         │
│ 🔒 Your info is safe and secure                       │
│ 📱 Takes 30 seconds, works immediately                 │
└─────────────────────────────────────────────────────────┘

↓ (Registration processing - redirects to customer dashboard)

Customer Dashboard (Web Portal):
┌─────────────────────────────────────────────────────────┐
│ 🎉 Welcome to Kedai Kopi Ah Beng VIP! 🎉              │
│                                                         │
│ ✨ Your VIP Coffee Card is Ready! ✨                   │
│                                                         │
│ ☕ Your VIP Coffee Card                                │
│ [QR Code Display - For Staff Scanning]                 │
│ ⭐☆☆☆☆☆☆☆☆☆                                      │
│ 0 of 10 stamps collected                               │
│ 🎯 Collect 10 stamps for FREE COFFEE!                 │
│                                                         │
│ 📊 Your Progress:                                      │
│ • Member since: Today                                  │
│ • Total visits: 0                                      │
│ • Stamps earned: 0                                     │
│ • Rewards earned: 0                                    │
│                                                         │
│ 💡 How it works:                                       │
│ 1️⃣ Show this QR code when ordering                    │
│ 2️⃣ Staff scans your QR code                           │
│ 3️⃣ Stamps automatically added                          │
│ 4️⃣ Free coffee after 10 stamps!                       │
│                                                         │
│ 📱 Optional: Add to Phone Wallet                       │
│ [Add to Apple Wallet] [Add to Google Wallet]          │
│ Access your card without internet connection           │
│                                                         │
│ [View Full History] [Profile Settings] [Logout]        │
└─────────────────────────────────────────────────────────┘
```

### Customer Login (Returning Customers)
```
[🔑 LOGIN] tapped (from landing page)
↓
Login Form:
┌─────────────────────────────────────────────────────────┐
│ 📱 Login to Your Account                               │
│ 🏪 Kedai Kopi Ah Beng                                 │
│                                                         │
│ Phone Number: [019-234-5678__________] 🇲🇾             │
│                                                         │
│ [SEND OTP CODE]                                        │
│                                                         │
│ We'll send a 6-digit code to your phone                │
│                                                         │
│ Don't have account? [Register Now]                     │
└─────────────────────────────────────────────────────────┘

↓ (OTP sent via SMS)

OTP Verification:
┌─────────────────────────────────────────────────────────┐
│ 🔢 Enter Your Code                                     │
│                                                         │
│ Code sent to: 019-234-5678                            │
│                                                         │
│ Enter code: [□][□][□][□][□][□]                        │
│                                                         │
│ [VERIFY] [Resend Code]                                 │
│                                                         │
│ Code expires in: 4:52                                  │
└─────────────────────────────────────────────────────────┘

↓ (OTP verified - creates session)

Customer Dashboard (Web Portal):
┌─────────────────────────────────────────────────────────┐
│ 👋 Welcome back, Aminah!                               │
│ 🏪 Kedai Kopi Ah Beng                                 │
│                                                         │
│ ☕ Your VIP Coffee Card                                │
│ [QR Code Display - Tap to Enlarge]                     │
│ ⭐⭐⭐⭐⭐⭐⭐⭐☆☆                              │
│ 8 of 10 stamps collected                               │
│ 🎯 Just 2 more stamps for FREE COFFEE! 🎉             │
│                                                         │
│ 📊 Your Progress:                                      │
│ • Member since: Dec 2023                              │
│ • Total visits: 28                                     │
│ • Stamps earned: 38                                    │
│ • Free coffees enjoyed: 3                             │
│                                                         │
│ 🕐 Recent Activity:                                    │
│ • +1 stamp - Today, 9:30 AM                           │
│ • +1 stamp - 2 days ago                               │
│ • FREE COFFEE claimed - 1 week ago                     │
│                                                         │
│ 📱 Optional: Save to Phone Wallet                      │
│ [Add to Apple Wallet] [Add to Google Wallet]          │
│ Access your card offline anytime                       │
│                                                         │
│ [View Full History] [Profile Settings] [Logout]        │
└─────────────────────────────────────────────────────────┘
```

### Multi-Campaign Customer Experience
```
For customers with multiple loyalty cards from same business:

Customer Dashboard (Multiple Cards):
┌─────────────────────────────────────────────────────────┐
│ 👋 Hi Ahmad! - Kedai Kopi Ah Beng                     │
│                                                         │
│ Your Loyalty Cards (3):                                 │
│                                                         │
│ ☕ VIP Coffee Card                                     │
│ ⭐⭐⭐⭐⭐⭐⭐☆☆☆ (7/10 stamps)               │
│ [Show QR] [Details]                                    │
│                                                         │
│ 👑 Gold Membership                                     │
│ Gold Tier - 15% Discount                               │
│ [Show QR] [Details]                                    │
│                                                         │
│ 🎫 CNY Special Campaign                                │
│ 2 coupons available - Expires Feb 28                   │
│ [Show QR] [Details]                                    │
│                                                         │
│ 📱 Add All Cards to Wallet                             │
│ [Add to Apple Wallet] [Add to Google Wallet]          │
│                                                         │
│ [Profile Settings] [Logout]                            │
└─────────────────────────────────────────────────────────┘
```

### Web Portal Features

**Customer Can Access:**
1. **Digital QR Code**: Always available for staff scanning
2. **Live Balance**: Real-time stamps/points/rewards status
3. **Transaction History**: Complete activity log
4. **Profile Management**: Update phone, email, birthday
5. **Wallet Integration**: Optional download to Apple/Google Wallet
6. **Multi-Business Cards**: View all loyalty programs from one login
7. **Notifications**: Special offers, birthday rewards, campaign updates

**Authentication & Sessions:**
- OTP-based login (SMS verification)
- Session persists for 30 days
- Auto-login on return visits (if session active)
- Secure logout option
- Multi-device access (phone, tablet, desktop)

**QR Code Display:**
- Large, scannable QR code front and center
- Tap to fullscreen for easy staff scanning
- Works with or without internet (cached after first load)
- Same QR code works for wallet and web portal
```

---

## 👨‍💼 SUPERADMIN FLOW

### Platform Management
```
Access: dashboard.loyaltea.my/login (superadmin role)
↓
Platform Overview:
┌─────────────────────────────────────────────────────────┐
│ 📊 LoyalTea Platform Dashboard                         │
│                                                         │
│ 🏢 Platform Stats (January 2024)                      │
│ • Total Businesses: 23                                 │
│ • Active Campaigns: 31                                 │
│ • Total Customers: 2,456                              │
│ • Monthly Revenue: RM3,470                             │
│                                                         │
│ 📈 Tier Distribution:                                  │
│ • Basic (RM99): 15 businesses                          │
│ • Business (RM199): 6 businesses                       │
│ • Multi-Branch (RM299): 2 businesses                  │
│                                                         │
│ 🚨 Action Required:                                    │
│ • 2 payment overdue                                    │
│ • 1 support ticket pending                             │
│ • 3 new signups awaiting approval                      │
│                                                         │
│ [Manage Businesses] [Handle Payments] [Support Queue]  │
└─────────────────────────────────────────────────────────┘
```

### Business Management
```
dashboard.loyaltea.my/superadmin/businesses
↓
Business Management:
┌─────────────────────────────────────────────────────────┐
│ 🏪 Active Businesses (23)                             │
│                                                         │
│ kedaiA - Kedai Kopi Ah Beng                           │
│ • Plan: Basic (RM99/month) • Status: ✅ Active         │
│ • Campaigns: 1/1 • Customers: 89                      │
│ • Last payment: 15 Jan 2024                           │
│ • [View Details] [Contact Owner] [Billing]            │
│                                                         │
│ mariospizza - Mario's Italian Restaurant               │
│ • Plan: Business (RM199/month) • Status: ✅ Active     │
│ • Campaigns: 2/3 • Customers: 234                     │
│ • Last payment: 22 Jan 2024                           │
│ • [View Details] [Contact Owner] [Billing]            │
│                                                         │
│ warungmak - Warung Mak Kiah                           │
│ • Plan: Basic (RM99/month) • Status: ⚠️ Payment Due   │
│ • Campaigns: 1/1 • Customers: 45                      │
│ • Payment due: 25 Jan 2024 (3 days overdue)          │
│ • [Send Reminder] [Contact Owner] [Suspend?]          │
│                                                         │
│ [+ Add New Business] [Export Report] [Send Broadcast]  │
└─────────────────────────────────────────────────────────┘
```

---

## 🌐 Customer Access Methods

### Three Ways to Access Loyalty Card

**1. Web Portal (Primary Method)** ✅ Recommended
```
• Access via: app.loyaltea.my/login
• Login with phone number + OTP
• View QR code, balance, history
• Works on any device with browser
• Session persists for 30 days
• No installation required
```

**2. Apple Wallet (Optional)**
```
• Download from web portal
• One-time setup, always accessible
• Works offline
• Automatic updates when staff scans
• Lock screen access
• Location-based notifications
```

**3. Google Wallet (Optional)**
```
• Download from web portal
• One-time setup, always accessible
• Works offline
• Real-time balance updates
• Quick access from home screen
• Location-based reminders
```

### Universal Customer Journey
```
Customer Registration:
1. Scan QR at store → Register → Web portal access
2. Optional: Download to Apple/Google Wallet
3. Show QR (web or wallet) when visiting store
4. Staff scans → Balance updates everywhere

Returning Customer:
1. Open web portal (or wallet if downloaded)
2. Show QR code to staff
3. Staff scans → Updates reflected immediately
4. View updated balance in web portal or wallet
```

---

## 🔄 Universal QR Code System

### Single QR Implementation
```
QR Code Format:
loyaltea://customer/{customer-id}/business/{business-id}?sig={signature}&ts={timestamp}

Example:
loyaltea://customer/abc123/business/kedaiA?sig=xyz789&ts=1704067200

What happens when scanned:
1. System identifies customer and business
2. Loads customer's loyalty cards for that business  
3. Shows appropriate interface based on user role:
   • Customer → Registration or dashboard
   • Staff → Transaction interface with customer profile
```

### Multi-Campaign Support
```
For businesses with multiple campaigns:

Customer has multiple active cards:
• Coffee Stamp Card (8/10 stamps)
• VIP Membership (Gold tier)  
• CNY Promotion (2 coupons available)

Staff Interface shows all cards:
• Select which campaign to update
• Process transaction for chosen campaign
• Customer wallet updates the specific card
```

---

## 📱 Malaysian User Experience Considerations

### Language & Cultural Elements
```
Customer Interface:
• English primary, simple Malaysian English
• WhatsApp contact prominently displayed
• Phone number format: 01X-XXX-XXXX (Malaysian format)
• Currency: RM (Ringgit Malaysia)
• Cultural holidays: Chinese New Year, Hari Raya campaigns

Business Interface:  
• English (business owners comfortable with English)
• Local business examples and categories
• Malaysian business hour support (9 AM - 6 PM MYT)
• Local contact methods (WhatsApp, phone calls)
```

### Payment & Billing Flows
```
Manual Payment Process (Current):
1. Owner receives monthly invoice via email
2. Bank transfer to LoyalTea account
3. Reference number: Business name + invoice number
4. Payment confirmation within 24 hours
5. Service continues uninterrupted

Future: FPX Integration
• Direct online banking payment
• Automatic receipt generation
• Instant payment confirmation
```

---

**These user flows provide practical, Malaysian-focused experiences that work for 2-person team development while serving local SME businesses effectively.**