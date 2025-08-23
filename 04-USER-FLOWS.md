# LoyalTea User Flows
*Complete User Journeys for All Roles*

## 🎯 Overview

**Universal QR System**: One QR code per customer per business works with all 8 card types
**Mobile-First Design**: Customers and staff use optimized mobile web interfaces
**Wallet Integration**: Primary customer experience through Apple/Google Wallet
**Manual Onboarding**: Business creation handled personally by super admin

---

## 👥 User Interface Allocation

### 🖥️ **Desktop Admin Panels** (jBoilerplate Interface)
- **Super Admin**: Platform management, business creation, billing oversight
- **Business Owner**: Loyalty program setup, customer management, analytics

### 📱 **Mobile Web Applications** (Custom Interface)
- **Customer**: Wallet integration + mobile web card fallback  
- **Staff**: Universal QR scanner with card-specific actions

---

## 📱 Understanding Digital Wallet Integration

### What is Apple Wallet & Google Wallet?

**Simple Explanation**: Digital wallets are like having a physical wallet on your phone, but for storing cards, tickets, and passes digitally. When customers get a loyalty card from your business, it goes directly into their phone's wallet app - the same place where they store boarding passes, movie tickets, and credit cards.

**Why Customers Love Wallet Cards**:
- **Always Available**: No need to remember to bring physical cards
- **Never Lost**: Cards sync across all their Apple/Google devices  
- **Instant Access**: Shows on lock screen when at business location
- **No Apps Required**: Works without downloading your business app
- **Works Offline**: Cards function even without internet connection

### How Wallet Integration Benefits Your Business

**Professional Appearance**:
- Your logo and branding displayed prominently on each card
- Customers see your business every time they open their wallet
- Professional design builds trust and credibility

**Higher Customer Engagement**:
- 70% higher usage rates compared to plastic cards or apps
- Lock screen notifications remind customers they have rewards
- Push notifications for special offers or expiry warnings

**No Technical Complexity for Staff**:
- Same QR scanning process for all card types
- Universal scanner works with wallet and non-wallet customers
- No staff training on different systems needed

### The Wallet Integration Process (Simplified)

**For Business Owners**:
```
1. Business Setup
   ├── Upload your logo and choose brand colors
   ├── Select card type (stamps, points, discounts, etc.)  
   └── System automatically creates wallet-ready cards

2. Customer Registration  
   ├── Customer scans QR code with phone camera
   ├── Fills simple form (name, phone, email, birthday)
   └── Gets "Add to Wallet" button

3. Wallet Card Added
   ├── Customer taps "Add to Apple/Google Wallet"  
   ├── Card instantly appears in their wallet app
   └── Card shows your logo, business name, progress

4. Daily Usage
   ├── Customer visits your business
   ├── Staff scans customer's QR code from wallet
   └── Points/stamps automatically added
```

**Visual Flow**:
```
📱 Customer Phone        →  🏪 Your Business        → 📱 Customer Wallet
┌─────────────────┐        ┌─────────────────┐        ┌─────────────────┐
│ Camera scans QR │   →    │ Staff processes │   →    │ Card updated    │
│ Registration    │        │ transaction     │        │ with new stamps │
│ form appears    │        │ via QR scan     │        │ or points       │
└─────────────────┘        └─────────────────┘        └─────────────────┘
```

### Common Questions About Wallet Integration

**Q: Do customers need to download an app?**
A: No! Apple Wallet and Google Wallet are built into every iPhone and Android phone. Customers just tap "Add to Wallet" and the card is ready.

**Q: What if customers don't have smartphones?**
A: The system works both ways - customers can still use QR codes from the mobile website without adding to wallet. Same scanning process for staff.

**Q: Can customers lose their digital cards?**
A: Digital wallet cards automatically sync across all customer devices (iPhone, iPad, Apple Watch). If they get a new phone, all cards transfer automatically.

**Q: Do wallet cards work without internet?**
A: Yes! Once added to wallet, cards work offline. QR codes display even without internet connection.

**Q: How do customers find their loyalty cards quickly?**
A: Wallet apps automatically show relevant cards based on location. When customers arrive at your business, your loyalty card appears on their lock screen.

### Technical Implementation Overview (For Developers)

**Apple Wallet Integration**:
- Uses PassKit framework and .pkpass file format
- Business certificates required from Apple Developer Account
- Official Documentation: [developer.apple.com/documentation/walletpasses](https://developer.apple.com/documentation/walletpasses)
- Design Guidelines: [developer.apple.com/design/human-interface-guidelines/wallet](https://developer.apple.com/design/human-interface-guidelines/wallet)

**Google Wallet Integration**:
- Uses Google Wallet API and JWT-based passes
- Google Cloud Console setup required
- Official Documentation: [developers.google.com/wallet](https://developers.google.com/wallet)
- Design Specifications: [developers.google.com/wallet/generic/rest/v1/genericobject](https://developers.google.com/wallet/generic/rest/v1/genericobject)

**Key Technical Components**:
1. **Pass Generation**: Automated creation of wallet files with business branding
2. **Certificate Management**: Secure handling of Apple/Google certificates  
3. **Push Notifications**: Update cards when points/stamps change
4. **QR Code Generation**: Universal QR system that works for all card types

**Developer Resources**:
- Complete implementation examples in 03-TECHNICAL-GUIDE.md
- Step-by-step integration procedures
- Testing and validation procedures
- Troubleshooting common integration issues

### Success Indicators

**Customer Adoption Metrics**:
- 80% target adoption rate for wallet integration
- 70% higher engagement vs. traditional cards
- 90% customer satisfaction with wallet convenience

**Business Benefits Measured**:
- Increased repeat visit frequency
- Higher reward redemption rates  
- Improved brand recognition and recall
- Reduced support queries about lost cards

---

## 🛒 CUSTOMER JOURNEY: "Getting & Using My Digital Loyalty Card"

### Primary Path: Wallet Integration (80% Target Adoption)

#### Initial Registration & Wallet Setup
```
Customer visits participating business
↓
Notices QR code displayed at counter/table/entrance
↓
Opens phone camera app (native camera)
↓
Points camera at QR code → Camera recognizes and shows notification
↓
Taps notification → Mobile web page opens
↓
Page displays: "[Business Name] Loyalty Program"
├── Business logo prominently displayed
├── Card type clearly explained ("Collect 10 stamps, get free coffee")
└── "Join now and get your digital loyalty card" message
↓
Simple registration form (4 fields only):
┌─────────────────────────────────┐
│ Full Name: [________________]   │
│ Phone: [___________________]    │  
│ Email: [___________________]    │
│ Birthday: [MM/DD/YYYY]          │
│                                 │
│        [JOIN NOW]               │
└─────────────────────────────────┘
↓
Customer fills form and taps "JOIN NOW"
↓
System processes registration and detects device:
├── iOS Device → Shows "Add to Apple Wallet" button with Apple logo
└── Android Device → Shows "Add to Google Wallet" button with Google logo  
↓
Customer taps wallet button
↓
Wallet app opens automatically showing loyalty card preview:
├── Business branding (logo, colors)
├── Card type clearly displayed ("Stamp Card")
├── Current status ("0/10 stamps - Start earning!")
├── QR code embedded for staff scanning
└── Business contact information on back
↓
Customer taps "Add" in wallet app
↓
SUCCESS: Digital loyalty card now lives in customer's wallet
├── Card appears in wallet alongside credit cards
├── Accessible from lock screen
├── Available offline
└── Ready for first transaction
↓
Customer receives confirmation message:
"Welcome! Your [Business Name] loyalty card is ready. 
Show this to staff on your next visit to start earning."
```

#### Ongoing Usage: Earning & Redeeming
```
Customer returns to business for purchase/service
↓
Customer opens wallet app on phone
↓
Scrolls to find business loyalty card
├── Card displays current status (e.g., "7/10 stamps")
├── Shows progress toward next reward
└── QR code clearly visible
↓
Customer shows phone screen to staff member
↓
Staff scans QR code with their mobile scanner
↓
Staff processes transaction based on card type:

FOR STAMP CARD:
Staff sees: "John Doe - 7/10 stamps"
Staff taps: [+ ADD STAMP]
Result: Customer earns 1 stamp

FOR POINTS CARD:  
Staff sees: "Sarah Lee - 245 points"
Staff enters purchase amount: RM25
Staff taps: [+ ADD POINTS]
Result: Customer earns 25 points

FOR DISCOUNT CARD:
Staff sees: "Ahmad Ali - 15% member discount" 
Staff applies 15% discount at checkout
Result: Customer saves money immediately

(Similar flows for other 5 card types)
↓
Transaction processed instantly
├── Database updated
├── Wallet card receives push notification
└── Card automatically refreshes with new balance
↓
Customer sees updated card immediately:
├── New balance displayed ("8/10 stamps")
├── Progress toward reward updated
├── Push notification: "You earned 1 stamp! 2 more for free coffee"
└── Ready for next visit
```

#### Reward Redemption Flow
```
Customer reaches reward eligibility (e.g., 10/10 stamps)
↓
Wallet card shows: "REWARD READY! Free Coffee Available"
↓
Customer visits business to redeem
↓
Shows wallet card to staff
↓
Staff scans QR code
↓
Staff sees: "John Doe - REWARD AVAILABLE: Free Coffee"
↓
Staff taps: [REDEEM REWARD]
↓
Staff gives customer their free coffee
↓
Card resets automatically:
├── Stamp count back to 0/10
├── Customer ready to start earning again
├── Wallet card updates immediately
└── Transaction logged for business analytics
```

### Fallback Path: Mobile Web Card (20% Target)

#### When Wallet Integration Fails
```
Customer completes registration form
↓
Wallet integration fails OR customer declines wallet option
↓
System automatically shows mobile web card option:
"Your digital loyalty card is ready!
Save this page to access your card anytime."
├── [📱 Add to Home Screen] button
├── [🔖 Bookmark This Page] button  
└── [📧 Email Me the Link] button
↓
Customer chooses preferred save method
↓
Mobile web page displays full-screen digital loyalty card:
┌─────────────────────────────────┐
│  [Business Logo]                │
│                                 │
│        STAMP CARD               │
│                                 │
│    ⭐⭐⭐☆☆☆☆☆☆☆        │
│      3 of 10 stamps             │
│                                 │
│  "7 more stamps for free coffee" │
│                                 │
│         [QR CODE]               │
│                                 │
│    Show this to staff           │
└─────────────────────────────────┘
↓
Next visit: Customer opens saved page/bookmark
↓
Shows mobile screen to staff (same scanning process)
↓
Staff processes transaction identically to wallet cards
↓
Customer refreshes page to see updated balance
```

### Phase 2: Customer Web Access (Optional Login)
```
Existing customer wants to access card on different device
OR
Customer lost their phone/needs to view card elsewhere
↓
Visits: [business-name].loyaltea.com/card
↓
Simple login page:
┌─────────────────────────────────┐
│     Access Your Loyalty Card    │
│                                 │
│ Phone Number: [+60____________] │
│                                 │
│        [SEND CODE]              │
└─────────────────────────────────┘
↓
Enters phone number, taps "SEND CODE"
↓
Receives SMS or WhatsApp message:
"Your LoyalTea access code: 123456
Valid for 10 minutes."
↓
Enters 6-digit code:
┌─────────────────────────────────┐
│    Enter the code sent to your  │
│         phone number            │
│                                 │
│      [1][2][3][4][5][6]        │
│                                 │
│         [VERIFY]                │
└─────────────────────────────────┘
↓
Code verified → Single page showing their loyalty cards:
┌─────────────────────────────────┐
│         Your Loyalty Cards      │
│                                 │
│ 🍕 Mario's Pizza                │
│    7/10 stamps                  │
│                                 │
│ ☕ Coffee Bean Café             │
│    245 points                   │
│                                 │
│ 💄 Beauty Salon                 │
│    Gold Member - 15% off        │
│                                 │
│  [VIEW TRANSACTION HISTORY]     │
└─────────────────────────────────┘
↓
Customer can view cards and history but primary experience remains wallet-based
```

---

## 💼 STAFF JOURNEY: "Processing Customer Transactions"

### Daily Staff Setup & Login
```
Staff member arrives at work
↓
Takes out phone or tablet (any device with camera)
↓
Opens mobile browser (Chrome, Safari, etc.)
↓
Navigates to: staff.loyaltea.com
↓
Simple login screen:
┌─────────────────────────────────┐
│         LoyalTea Staff          │
│                                 │
│ Username: [________________]    │
│ Password: [________________]    │
│                                 │
│        [LOG IN]                 │
│                                 │
│ 📞 Need help? Call: 03-XXXXXXX  │
└─────────────────────────────────┘
↓
Enters credentials provided by business owner
↓
Mobile staff interface loads:
┌─────────────────────────────────┐
│    Welcome back, Ahmad!         │
│    [Business Name] Staff        │
│                                 │
│                                 │
│         📷 SCAN                 │
│      CUSTOMER QR                │
│                                 │
│                                 │
│                                 │
│ Recent Scans:                   │
│ • John D. - 2 stamps (+1)       │
│ • Sarah L. - 15 points (+10)    │
│                                 │
│ [HISTORY] [SEARCH] [PROFILE]    │
└─────────────────────────────────┘
```

### Universal Customer Processing (All 8 Card Types)
```
Customer approaches counter with purchase/service
↓
Customer shows loyalty card:
├── Wallet app on phone screen
├── Mobile web page with QR code
├── Screenshot of their card
└── Even printed QR code (if available)
↓
Staff taps large "SCAN CUSTOMER QR" button (70% of screen)
↓
Phone camera opens in browser with scanning interface:
┌─────────────────────────────────┐
│           SCANNING              │
│  ┌─────────────────────────┐   │
│  │                         │   │
│  │    [QR CODE FRAME]      │   │
│  │   Point camera at       │   │
│  │   customer's QR code    │   │
│  │                         │   │
│  └─────────────────────────┘   │
│                                 │
│    [MANUAL SEARCH] [CANCEL]     │
└─────────────────────────────────┘
↓
Staff points camera at customer's QR code
↓
QR code detected and scanned successfully
↓
System automatically identifies customer and card type
↓
Interface adapts to show card-specific actions:

┏━━━━━ STAMP CARD ━━━━━━━━━━━━━━━┓
┃ 👤 John Doe                  ┃
┃ ⭐ Current: 7/10 stamps       ┃
┃ 🎯 Reward: Free coffee        ┃
┃ 📅 Last visit: 3 days ago    ┃
┃                              ┃
┃    [+ ADD STAMP] [HISTORY]   ┃
┃                              ┃
┃ ⚠️ 3 more stamps for reward!  ┃
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛

┏━━━━━ POINTS CARD ━━━━━━━━━━━━━━┓
┃ 👤 Sarah Lee                 ┃
┃ 💎 Balance: 245 points       ┃
┃ 💰 Purchase: RM[____]        ┃  
┃ 📅 Last visit: 1 week ago    ┃
┃                              ┃
┃ [+ ADD POINTS] [- REDEEM]    ┃
┃    [HISTORY]                 ┃
┃                              ┃
┃ 💡 55 points until next reward┃
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛

┏━━━━ DISCOUNT CARD ━━━━━━━━━━━━┓
┃ 👤 Ahmad Ali                 ┃
┃ 🏷️ Member Discount: 15%      ┃
┃ 💳 Always active             ┃
┃ 📅 Member since: Jan 2024    ┃
┃                              ┃
┃   [✓ APPLY DISCOUNT]         ┃
┃      [HISTORY]               ┃
┃                              ┃
┃ 💰 Total saved: RM247 in 2024┃
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛

(Similar optimized interfaces for other 5 card types)
↓
Staff selects appropriate action:
├── Taps [+ ADD STAMP] for stamp cards
├── Enters amount and taps [+ ADD POINTS] for points
├── Taps [✓ APPLY DISCOUNT] for discount cards
├── Enters service count for multipass cards
└── Processes according to card type
↓
Staff confirms the transaction:
┌─────────────────────────────────┐
│        Confirm Transaction      │
│                                 │
│ Customer: John Doe              │
│ Action: Add 1 stamp             │
│ New Balance: 8/10 stamps        │
│                                 │
│     [✓ CONFIRM] [✗ CANCEL]     │
└─────────────────────────────────┘
↓
Staff taps [✓ CONFIRM]
↓
Transaction processed instantly:
├── Database updated immediately
├── Customer's wallet card refreshed via push notification  
├── Success confirmation shown to staff
└── Ready for next customer
↓
Staff sees success message:
┌─────────────────────────────────┐
│            SUCCESS! ✓           │
│                                 │
│    Added 1 stamp to John Doe    │
│     New balance: 8/10 stamps    │
│                                 │
│   Customer's wallet updated!    │
│                                 │
│        [SCAN NEXT CUSTOMER]     │
└─────────────────────────────────┘
↓
Staff ready to serve next customer (entire process takes <10 seconds)
```

### Problem Resolution Workflows

#### QR Code Won't Scan
```
Staff tries to scan but QR code not working:
├── Poor lighting
├── Damaged/blurry QR code  
├── Camera issues
└── Customer phone screen too dim
↓
Staff taps [MANUAL SEARCH] button
↓
Manual customer lookup interface:
┌─────────────────────────────────┐
│        Find Customer            │
│                                 │
│ Phone: [+60_______________]     │
│    OR                           │
│ Email: [__________________]     │
│                                 │
│        [SEARCH]                 │
│                                 │
│ Recent Customers:               │
│ • John Doe - 019-1234567        │
│ • Sarah Lee - sarah@email.com   │  
│ • Ahmad Ali - 012-9876543       │
└─────────────────────────────────┘
↓
Staff types customer phone number or selects from recent list
↓
Customer found → Same interface as successful QR scan
↓
Staff processes transaction normally
```

#### Customer Not Found in System
```
Staff searches for customer but no results found
↓
Interface shows:
┌─────────────────────────────────┐
│      Customer Not Found         │
│                                 │
│ This customer hasn't joined     │
│ our loyalty program yet.        │
│                                 │
│    [REGISTER NEW CUSTOMER]      │
│         [SKIP FOR NOW]          │
└─────────────────────────────────┘
↓
Staff taps [REGISTER NEW CUSTOMER]
↓
Quick registration form:
┌─────────────────────────────────┐
│       New Customer Signup       │
│                                 │
│ Name: [___________________]     │
│ Phone: [+60_______________]     │
│ Email: [__________________]     │
│ Birthday: [DD/MM/YYYY]          │
│                                 │
│ [✓ CREATE ACCOUNT & PROCESS]    │
└─────────────────────────────────┘
↓  
Staff helps customer fill form
↓
Account created instantly
↓
Customer gets wallet card immediately (staff shows QR for wallet)
↓
Staff can now process transaction right away
```

#### System/Connection Issues
```
Internet connection problems or system temporarily down
↓
Staff interface shows:
┌─────────────────────────────────┐
│       Connection Issue          │
│                                 │
│ ⚠️ Cannot connect to server     │
│                                 │
│    [RETRY] [OFFLINE MODE]       │
└─────────────────────────────────┘
↓
Staff taps [OFFLINE MODE]
↓
Simple offline transaction logging:
┌─────────────────────────────────┐
│        Offline Transactions     │
│                                 │
│ Customer: [________________]    │
│ Action: [Stamp] [Points] [Disc] │  
│ Amount: [_____] (if applicable) │
│ Time: 14:25                     │
│                                 │
│      [LOG TRANSACTION]          │
│                                 │
│ Pending Sync: 3 transactions    │
└─────────────────────────────────┘
↓
Staff logs transactions manually
↓
When connection restored, transactions automatically sync:
├── All offline transactions processed
├── Customer wallet cards updated
├── Database brought up to date
└── Staff notified of successful sync
```

---

## 🏢 BUSINESS OWNER JOURNEY: "Setting Up & Managing My Loyalty Program"

### Manual Business Creation (Done by Super Admin)
```
Potential business owner contacts LoyalTea:
├── Phone call to published number
├── WhatsApp message to business line
├── Email to info@loyaltea.com
└── In-person visit (Semenyih area)
↓
LoyalTea team (super admin) responds within 2 hours
↓
Personal meeting/demo scheduled:
├── Face-to-face meeting at business location (preferred)
├── Video call demonstration
└── Phone demonstration with screen sharing
↓
Super admin demonstrates working system:
├── Shows customer registration process on phone
├── Demonstrates staff scanning and transaction processing
├── Shows wallet integration working in real time
├── Explains benefits and pricing (RM99/month)
└── Answers all questions and concerns
↓
Business owner decides to proceed
↓
Super admin creates business account on the spot:
┌─────────────────────────────────┐
│      Create New Business        │
│                                 │
│ Business Name: [______________] │
│ Owner Name: [_________________] │
│ Phone: [+60___________________] │
│ Email: [____________________]  │
│ Address: [____________________] │
│ Business Type: [______________] │
│                                 │
│    [CREATE ACCOUNT]             │
└─────────────────────────────────┘
↓
Account created → Super admin emails login credentials to owner
```

### Owner Program Configuration Wizard
```
Business owner receives email with login details
↓
Logs into desktop admin panel at: owner.loyaltea.com
↓
Welcome wizard launches automatically:
┌─────────────────────────────────┐
│     Welcome to LoyalTea! 🎉     │
│                                 │
│ Let's set up your loyalty       │
│ program in just 3 steps:        │
│                                 │
│ ✓ Step 1: Choose card type      │
│ ○ Step 2: Configure program     │  
│ ○ Step 3: Upload branding       │
│                                 │
│          [GET STARTED]          │
└─────────────────────────────────┘
↓
Step 1: Card Type Selection
┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃                            Choose Your Loyalty Card Type                     ┃
┃                                                                             ┃
┃ ☕ STAMP CARD              🏆 POINTS CARD              👑 MEMBERSHIP CARD    ┃  
┃ "Collect 10, get 1 free"   "Earn points per RM spent"  "VIP tiers & perks"  ┃
┃ Perfect for: Cafés, F&B    Perfect for: Retail, Gyms   Perfect for: Premium ┃
┃      [SELECT]                   [SELECT]                    [SELECT]        ┃
┃                                                                             ┃
┃ 💰 DISCOUNT CARD          💳 CASHBACK CARD            🎫 MULTIPASS CARD     ┃
┃ "15% off for members"      "5% cashback earned"       "10-visit packages"   ┃
┃ Perfect for: Fashion       Perfect for: Supermarkets   Perfect for: Services ┃
┃      [SELECT]                   [SELECT]                    [SELECT]        ┃
┃                                                                             ┃
┃ 🎟️ COUPON CARD            🎁 GIFT CARD                                     ┃
┃ "Special offers & deals"   "Prepaid value cards"                           ┃
┃ Perfect for: Promotions    Perfect for: Gifting                            ┃
┃      [SELECT]                   [SELECT]                                   ┃
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛
↓
Owner clicks on preferred card type (e.g., STAMP CARD)
↓
Step 2: Program Configuration (specific to chosen card type)
┌─────────────────────────────────┐
│      Configure Stamp Card       │
│                                 │
│ How many stamps for reward?     │
│ ○ 5 stamps  ○ 8 stamps         │
│ ● 10 stamps ○ 12 stamps        │
│ ○ 15 stamps                     │
│                                 │
│ What's the reward?              │
│ [Free coffee_____________]      │
│                                 │
│ Auto-reset after redemption?    │
│ ● Yes ○ No                      │
│                                 │
│         [CONTINUE]              │
└─────────────────────────────────┘
↓  
Owner configures program settings
↓
Step 3: Business Branding
┌─────────────────────────────────┐
│        Upload Your Branding     │
│                                 │
│ Business Logo:                  │
│ [📁 Choose File] logo.png       │
│                                 │
│ Primary Color:                  │
│ [🎨] #2563eb (Blue)            │
│                                 │
│ Secondary Color:                │  
│ [🎨] #ffffff (White)           │
│                                 │
│ Preview:                        │
│ [🔄 Loading preview...]         │
│                                 │
│    [SAVE & LAUNCH PROGRAM]      │
└─────────────────────────────────┘
↓
Owner uploads logo and chooses colors
↓
System generates everything automatically:
├── Customer registration QR codes (multiple sizes for printing)
├── Staff account credentials  
├── Wallet pass templates with business branding
├── Mobile web card designs
└── Admin panel fully configured
↓
Completion screen:
┌─────────────────────────────────┐
│          🎉 You're Live! 🎉     │
│                                 │
│ Your loyalty program is now     │
│ active and ready for customers! │
│                                 │
│ ✓ QR codes generated            │
│ ✓ Staff accounts created        │
│ ✓ Wallet integration ready      │
│ ✓ Mobile cards designed         │
│                                 │
│    [DOWNLOAD QR CODES]          │
│    [TRAIN YOUR STAFF]           │
│    [VIEW DASHBOARD]             │
└─────────────────────────────────┘
```

### Daily Management Dashboard
```
Owner logs into admin panel for daily management
↓
Dashboard shows key metrics:
┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃                            Mario's Pizza - Dashboard                        ┃
┃                                                                             ┃
┃ 📊 This Week                    👥 Total Customers: 127                    ┃
┃ • New customers: 8              🔄 Active this week: 23                     ┃
┃ • Total transactions: 45        ⭐ Rewards redeemed: 5                     ┃
┃ • Average per day: 6.4          📈 Growth: +12% vs last week               ┃
┃                                                                             ┃
┃ 🌟 Top Customers This Week      📱 Recent Transactions                     ┃
┃ 1. Sarah L. - 3 visits          • John D. - Added stamp (2 min ago)       ┃
┃ 2. Ahmad A. - 2 visits          • Lisa M. - Redeemed reward (1 hr ago)     ┃
┃ 3. John D. - 2 visits           • Sarah L. - Added stamp (2 hr ago)        ┃
┃                                                                             ┃
┃ [VIEW ALL CUSTOMERS] [MANAGE STAFF] [DOWNLOAD REPORTS] [QR CODES]          ┃
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛
↓
Owner can access different management sections:
├── All Customers (view profiles, transaction history, manual adjustments)
├── Staff Management (create accounts, view activity, set permissions)
├── Program Settings (modify card configuration, update branding)
├── QR Code Downloads (regenerate codes, different sizes for printing)
├── Basic Reports (weekly/monthly summaries, customer export)
└── Business Profile (update contact info, hours, location)
```

### Staff Account Creation
```
Owner needs to create staff account for new employee
↓
Navigates to "Manage Staff" section
↓
Staff management interface:
┌─────────────────────────────────┐
│           Staff Accounts        │
│                                 │
│ Active Staff:                   │
│ • Ahmad Rahman (Manager)        │  
│   Login: ahmad_mario            │
│   Last active: 2 hours ago     │
│                                 │
│ • Lisa Wong (Cashier)           │
│   Login: lisa_mario             │
│   Last active: Yesterday       │
│                                 │
│    [+ ADD NEW STAFF MEMBER]     │
└─────────────────────────────────┘
↓
Owner clicks [+ ADD NEW STAFF MEMBER]
↓
Simple staff creation form:
┌─────────────────────────────────┐
│         Add New Staff           │
│                                 │
│ Staff Name: [_________________] │
│ Username: [___________________] │  
│ Password: [___________________] │
│ Role: [Cashier ▼]               │
│                                 │
│ Permissions:                    │
│ ☑️ Scan customer QR codes       │
│ ☑️ Add points/stamps            │
│ ☑️ Process redemptions          │
│ ☑️ View transaction history     │
│ ☐ Manual customer adjustments  │
│                                 │
│      [CREATE STAFF ACCOUNT]     │
└─────────────────────────────────┘
↓
Owner fills details and creates account
↓
Staff credentials generated and displayed for owner to share with employee
```

---

## ⚙️ SUPER ADMIN JOURNEY: "Platform Management & Business Onboarding"

### Business Onboarding Workflow
```
New business inquiry received via:
├── Phone call to LoyalTea main number
├── WhatsApp message to business line  
├── Email to info@loyaltea.com
├── Referral from existing business
└── In-person visit (Semenyih area)
↓
Super admin logs into platform dashboard
↓
Creates new lead in system:
┌─────────────────────────────────┐
│           New Business Lead     │
│                                 │
│ Business Name: [______________] │
│ Owner Name: [_________________] │
│ Contact Phone: [+60___________] │
│ Contact Email: [______________] │
│ Business Type: [______________] │  
│ Location: [___________________] │
│ Source: [Phone call ▼]         │
│ Status: [Initial contact ▼]    │
│ Notes: [_____________________] │
│                                 │
│        [SAVE LEAD]              │
└─────────────────────────────────┘
↓
Super admin schedules demo meeting
↓
Demo conducted (see Business Owner journey for demo process)
↓
If business owner agrees to proceed:
┌─────────────────────────────────┐
│      Convert Lead to Business   │
│                                 │
│ ✓ Demo completed successfully   │
│ ✓ Owner agreed to RM99/month    │
│ ✓ Ready for account creation    │
│                                 │
│ Subscription Start: [Today ▼]   │
│ First Payment Due: [Next month] │
│ Trial Period: [1 month free]    │
│                                 │
│    [CREATE BUSINESS ACCOUNT]    │
└─────────────────────────────────┘
↓
Business account created in system
↓
Super admin sends welcome email with login credentials
↓
Personal onboarding session scheduled (2 hours)
```

### Platform Administration Dashboard
```
Super admin logs into platform control panel
↓
Platform overview dashboard:
┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃                           LoyalTea Platform Dashboard                        ┃
┃                                                                             ┃
┃ 📊 Platform Statistics         💰 Revenue Overview                         ┃
┃ • Total Businesses: 23         • This Month: RM2,277                       ┃
┃ • Active Customers: 1,247      • Last Month: RM1,980                       ┃
┃ • Transactions Today: 156      • Growth: +15.0%                            ┃
┃ • System Uptime: 99.8%         • Outstanding: RM198                        ┃
┃                                                                             ┃
┃ 🏢 Recent Business Activity    ⚠️ Attention Required                       ┃
┃ • Mario's Pizza - 45 trans.    • 2 businesses need payment follow-up       ┃
┃ • Coffee Bean - 23 trans.      • 1 technical support request               ┃
┃ • Beauty Salon - 18 trans.     • System backup due tomorrow               ┃
┃                                                                             ┃
┃ [MANAGE BUSINESSES] [BILLING] [SUPPORT] [SYSTEM] [REPORTS]                 ┃
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛
↓
Super admin can manage all aspects:
├── All Businesses (view, edit, activate/deactivate)
├── Manual Billing (generate invoices, track payments)
├── Customer Support (help businesses with issues)
├── System Administration (backups, updates, monitoring)
└── Platform Reports (growth metrics, usage analytics)
```

### Manual Billing Management
```
Super admin handles monthly billing for all businesses
↓
Accesses billing management:
┌─────────────────────────────────┐
│          Monthly Billing        │
│                                 │
│ February 2024 - 23 Businesses   │
│                                 │
│ ✅ Mario's Pizza - Paid RM99    │
│ ✅ Coffee Bean - Paid RM99      │
│ ✅ Beauty Salon - Paid RM99     │
│ ⏳ Car Wash Pro - Due in 3 days │
│ ⚠️ Mini Market - Overdue 2 days │
│                                 │
│ Total Expected: RM2,277         │
│ Total Collected: RM2,079        │  
│ Outstanding: RM198              │
│                                 │
│ [SEND REMINDERS] [MARK PAID]    │
│ [GENERATE INVOICES]             │
└─────────────────────────────────┘
↓
For overdue accounts, super admin makes personal follow-up:
├── WhatsApp message to business owner
├── Phone call to understand any payment issues  
├── Offer payment plan if needed
├── Maintain relationship (not aggressive collection)
└── Work with business to resolve payment
```

### Customer Support Management
```
Business owner contacts support via WhatsApp or phone
↓
Super admin receives support request:
┌─────────────────────────────────┐
│           Support Ticket        │
│                                 │
│ From: Mario's Pizza (Ahmad)     │
│ Contact: +60-12-3456789         │
│ Issue: Staff can't scan QR codes│
│ Priority: High                  │
│ Created: 10:30 AM today         │
│                                 │
│ Description:                    │
│ "Camera not working on scanner  │
│ page. Customers waiting."       │
│                                 │
│ [CALL NOW] [REMOTE HELP]        │
│ [VISIT ON-SITE] [UPDATE]        │
└─────────────────────────────────┘
↓
Super admin provides immediate personal support:
├── Phone call within 30 minutes
├── Screen sharing to diagnose issue
├── On-site visit if needed (Semenyih area)
├── Follow-up to ensure resolution
└── Document solution for future reference
```

---

## 🔄 TRANSACTION PROCESSING FLOWS

### Universal Transaction Architecture
**Same process works for all 8 card types - system adapts automatically**

```
Staff scans customer QR code
↓
System extracts data from QR:
├── Customer ID
├── Business ID  
├── Loyalty record ID
├── Security validation
└── Timestamp check
↓
System retrieves customer loyalty record
↓
System identifies card type from business configuration
↓
System loads appropriate card processor:
├── StampCardProcessor for stamp cards
├── PointsCardProcessor for points cards
├── DiscountCardProcessor for discount cards
├── (etc. for all 8 card types)
└── Displays card type-specific interface to staff
↓
Staff performs action based on card type
↓
Transaction processor calculates new state:
├── Previous balance/status
├── Transaction details (amount, action type)
├── Business rules (points per RM, stamps required, etc.)
└── New balance/status after transaction
↓
Database updated atomically:
├── Customer loyalty record updated
├── Transaction log created  
├── Business analytics incremented
└── All changes committed together
↓
Wallet pass update triggered:
├── Generate new pass data
├── Send push notification to customer device
├── Pass automatically refreshes in wallet
└── Customer sees new balance immediately
↓
Staff receives confirmation:
├── Success message displayed
├── New customer balance shown
├── Ready for next customer
└── Entire process completes in <3 seconds
```

### Card Type-Specific Transaction Examples

#### Stamp Card Transaction
```
BEFORE: Customer has 7/10 stamps
↓
Staff scans QR → System shows "John Doe - 7/10 stamps"
↓  
Staff taps [+ ADD STAMP]
↓
System processes:
├── stamps_current: 7 → 8
├── stamps_lifetime: 47 → 48  
├── last_activity: updated to now
└── Check if reward eligible (8 < 10, so not yet)
↓
AFTER: Customer has 8/10 stamps
↓
Wallet card updates: "8 of 10 stamps - 2 more for free coffee!"
```

#### Points Card Transaction
```
BEFORE: Customer has 245 points, makes RM25 purchase
↓
Staff scans QR → System shows "Sarah Lee - 245 points"
↓
Staff enters purchase amount: RM25
↓
Staff taps [+ ADD POINTS]
↓
System calculates:
├── Points to add: RM25 × 1.0 rate = 25 points
├── points_current: 245 → 270
├── points_lifetime: 1,247 → 1,272
└── purchase_amount: RM25 logged
↓
AFTER: Customer has 270 points
↓
Wallet card updates: "270 points available - 30 points until next reward"
```

#### Reward Redemption Flow
```
Customer has completed stamp card (10/10 stamps)
↓
Staff scans QR → System shows "REWARD READY: Free Coffee"
↓
Staff taps [REDEEM REWARD]
↓
System processes redemption:
├── stamps_current: 10 → 0 (reset)
├── rewards_redeemed: 2 → 3 (increment)
├── last_redemption: updated to now
└── Transaction logged as "reward_redemption"
↓
Staff gives customer free coffee
↓
Wallet card resets: "0 of 10 stamps - Start earning your next free coffee!"
```

---

## 📱 Mobile Interface Design Principles

### Customer Mobile Experience
**Full-screen wallet-style card design**

```
┌─────────────────────────────────┐ ← Phone screen (mobile browser)
│  [Business Logo]                │
│                                 │
│        STAMP CARD               │ ← Card type clearly displayed
│                                 │
│    ⭐⭐⭐⭐⭐⭐⭐☆☆☆        │ ← Visual progress indicator
│      7 of 10 stamps             │ ← Current status large text
│                                 │
│  "3 more stamps for free coffee" │ ← Motivation message
│                                 │
│         [QR CODE]               │ ← QR code for staff scanning
│         ████████                │
│         ████████                │
│         ████████                │
│                                 │
│    Show this to staff           │ ← Clear instruction
└─────────────────────────────────┘

Design Principles:
• 70% of screen for card visual
• High contrast for visibility in bright light
• Thumb-friendly (no precise tapping needed)
• Works in portrait orientation
• Loads in <2 seconds on 3G
```

### Staff Mobile Interface
**Scanner-first design optimized for speed**

```
┌─────────────────────────────────┐ ← Phone/tablet screen
│    Welcome back, Ahmad!         │ ← Personal greeting
│    Mario's Pizza Staff          │ ← Business context
│                                 │
│                                 │
│         📷 SCAN                 │ ← Giant scan button (70% screen)
│      CUSTOMER QR                │
│                                 │ 
│                                 │
│                                 │
│ Recent Activity:                │ ← Context from recent scans
│ • John D. - 1 stamp (+1) 2m ago │
│ • Sarah L. - 15 pts (+10) 5m ago │
│                                 │
│ [HISTORY] [SEARCH] [HELP]       │ ← Secondary actions
└─────────────────────────────────┘

Design Principles:
• One-handed operation
• Large touch targets (minimum 44px)  
• High contrast colors
• Fast camera activation (<1 second)
• Works in bright restaurant lighting
```

### After Scanning - Card-Specific Interface
```
┌─────────────────────────────────┐ ← Adapts based on scanned card type
│ 👤 John Doe                     │
│ ⭐ Stamp Card: 7/10 stamps      │ ← Card type & current status
│ 🎯 Reward: Free coffee          │ ← Reward clearly displayed
│ 📅 Last visit: 3 days ago       │ ← Helpful context
│                                 │
│    [+ ADD STAMP] [HISTORY]      │ ← Primary actions for this card type
│                                 │
│ 💡 3 more stamps for reward!    │ ← Encouraging message
│                                 │
│        [DONE] [SCAN NEXT]       │ ← Navigation options
└─────────────────────────────────┘

Adapts for each card type:
• Stamp cards → [+ ADD STAMP]
• Points cards → [+ ADD POINTS] with amount entry
• Discount cards → [✓ APPLY DISCOUNT] 
• Multipass cards → [- USE VISIT]
• etc.
```

---

## ⚡ Performance & Speed Optimizations

### Customer Registration Speed Target: <30 seconds
```
QR scan → Page load (1s) → Form fill (15s) → Processing (2s) → Wallet add (10s) = 28s total
```

### Staff Transaction Speed Target: <10 seconds  
```
Scan QR (2s) → Customer load (1s) → Staff action (3s) → Processing (1s) → Confirmation (3s) = 10s total
```

### System Response Time Targets
- **QR Code Generation**: <1 second
- **Customer Lookup**: <500ms
- **Transaction Processing**: <1 second  
- **Wallet Pass Update**: <3 seconds
- **Mobile Page Load**: <2 seconds on 3G
- **API Response**: <300ms average

### Offline Handling
**Staff Interface Offline Mode**:
- Cache customer data for recent customers
- Log transactions locally when connection fails
- Sync automatically when connection restored
- Show clear offline status indicator
- Allow basic operations to continue

---

## 🔐 Security & Privacy Considerations

### QR Code Security
- **Time-based validation**: QR codes expire after 10 minutes
- **Cryptographic signatures**: Prevent QR code tampering
- **Rate limiting**: Maximum 30 scans per minute per staff
- **Replay protection**: Each scan includes timestamp validation

### Customer Data Protection  
- **Minimal data collection**: Only 4 fields required (name, phone, email, birthday)
- **Secure transmission**: All data sent over HTTPS
- **Database encryption**: Customer data encrypted at rest
- **Access logging**: All data access logged for audit

### Staff Access Control
- **Role-based permissions**: Staff can only access assigned business data
- **Session management**: Automatic logout after 8 hours inactivity  
- **Failed login protection**: Account locked after 5 failed attempts
- **Audit trail**: All staff actions logged with timestamps

---

## 📊 Analytics & Insights Available

### For Business Owners
**Simple but useful metrics**:
- Total customers and growth rate
- Active customers (visited in last 30 days)
- Transaction volume and trends
- Top customers by activity
- Reward redemption rates
- Peak hours and days
- Customer retention metrics

### For Super Admin
**Platform-level insights**:
- Total businesses and growth
- Overall customer base growth
- Transaction volume across platform
- Business performance comparisons
- System health and uptime
- Revenue and billing status
- Support request patterns

### For Staff
**Operational context**:
- Recent customer interactions
- Daily transaction summary
- Shift activity overview
- Common issues and solutions

---

**This comprehensive user flows document covers every aspect of user interaction with the LoyalTea loyalty system, ensuring smooth experiences for all roles while maintaining the "simplest loyalty program" philosophy.**