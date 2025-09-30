# LoyalTea Loyalty Card Specifications
*Complete card types for Malaysian SME businesses*

## 🎯 Overview

**8 Card Types Available** - Business owners can create multiple campaigns with different card types
**Campaign-Based System** - Each campaign = one loyalty card type with its own branding and rules  
**Universal QR System** - All card types work with the same QR scanning workflow
**Malaysian Business Focus** - Examples and recommendations tailored for local market

---

## 💳 1. STAMP CARD
*"Collect 5, get 1 free" - Rewards repeat visits*

### Business Use Cases
- **Primary**: Cafés, F&B outlets, salons, car washes, bubble tea shops
- **Model**: Visit-based rewards, habit-building
- **Psychology**: Visual progress, immediate gratification

### How It Works
```
Customer Visit → Staff Adds Stamp → Progress Toward Reward → Full Card = Free Item
```

### Configuration Options (Business Owner Sets)
- **Stamps Required**: 5, 8, 10, 12, 15 stamps
- **Reward Description**: Free coffee, 20% off service, free wash, etc.
- **Card Design**: Business logo + colors
- **Reset Behavior**: Auto-reset after redemption or manual reset

### Data Structure
```
stamps_current: 7        // Current stamp count
stamps_required: 10      // Total needed for reward
stamps_lifetime: 47      // All-time stamps earned
reward_text: "Free Coffee"
last_stamp_date: "2024-01-15"
```

### Customer Experience
- **Wallet Display**: "⭐⭐⭐⭐⭐⭐⭐☆☆☆ (7/10 stamps)"
- **Progress Bar**: Visual fill indicator
- **Redemption**: "REWARD READY! Free Coffee Available"

### Staff Workflow
```
Scan Customer QR → Current Status Shows → Tap "Add Stamp" → Confirm → Updated Instantly
```

---

## 🏆 2. REWARD CARD (Points-Based)
*"Earn 1 point per RM1 spent" - Points redeemable for rewards*

### Business Use Cases
- **Primary**: Retail, automotive, gyms, beauty & aesthetic clinics
- **Model**: Spending-based accumulation
- **Psychology**: Value perception, bigger rewards

### How It Works
```
Customer Spends RM25 → Earns 25 Points → Accumulates → Redeem for Discount/Gift
```

### Configuration Options
- **Points Per Ringgit**: 0.5, 1.0, 1.5, 2.0 points per RM1
- **Reward Tiers**: 
  - 100 points = RM5 discount
  - 200 points = RM12 discount  
  - 500 points = Free service
- **Expiry**: Points valid for 12/24/36 months or no expiry

### Data Structure
```
points_current: 245      // Available points
points_lifetime: 1,247   // All-time points earned
points_redeemed: 1,002   // Points used
rate_per_ringgit: 1.0    // Points earned per RM
reward_tiers: [
  { points: 100, reward: "RM5 discount" },
  { points: 200, reward: "RM12 discount" }
]
```

### Customer Experience
- **Wallet Display**: "245 Points Available"
- **Next Reward**: "55 points until RM12 discount"
- **History**: Track earning and redemption history

### Staff Workflow
```
Scan QR → Enter Purchase Amount → Points Auto-Calculate → Add to Customer → Confirm
```

---

## 👑 3. MEMBERSHIP CARD
*"Silver/Gold/VIP tiers with exclusive perks"*

### Business Use Cases
- **Primary**: Gyms, premium retail, car communities, subscription clubs
- **Model**: Status-based benefits, tier progression
- **Psychology**: Exclusivity, status recognition

### How It Works
```
Customer Joins as Silver → Earns Tier Points → Upgrades to Gold → Access to VIP Benefits
```

### Configuration Options
- **Tier Levels**: 2-5 tiers (Silver, Gold, Platinum, VIP)
- **Upgrade Criteria**: Visit count, spending amount, time-based
- **Benefits Per Tier**: Discount %, exclusive access, priority service
- **Tier Maintenance**: Annual renewal or permanent

### Data Structure
```
current_tier: "Gold"
tier_points: 847         // Points toward next tier
tier_benefits: [
  "15% discount on all services",
  "Priority booking",
  "Free parking"
]
next_tier: "Platinum"
next_tier_requirement: 1000  // Points needed
membership_since: "2023-06-01"
```

### Customer Experience
- **Wallet Display**: "Gold Member - 15% Discount"
- **Progress**: "153 points until Platinum"
- **Benefits**: Clear list of current tier perks

### Staff Workflow
```
Scan QR → See Member Tier → Apply Appropriate Benefits → Log Visit/Purchase
```

---

## 💰 4. DISCOUNT CARD
*"10% off all purchases" - Ongoing savings for cardholders*

### Business Use Cases
- **Primary**: Fashion, beauty salons, restaurants, home & lifestyle stores
- **Model**: Immediate discount on every purchase
- **Psychology**: Always saving money, loyalty through savings

### How It Works
```
Customer Shows Card → Staff Applies Discount → Customer Saves Money Every Time
```

### Configuration Options
- **Discount Type**: Fixed percentage (5%, 10%, 15%, 20%)
- **Discount Scope**: All items, specific categories, minimum purchase
- **Special Days**: Double discount weekends, birthday bonus
- **Expiry**: Annual renewal, lifetime, subscription-based

### Data Structure
```
discount_percentage: 15     // Standard discount
discount_scope: "all"      // All items or specific categories  
total_savings: 247.50      // Lifetime savings amount
special_offers: [
  { type: "birthday", discount: 25, valid_days: 7 }
]
membership_valid_until: "2024-12-31"
```

### Customer Experience
- **Wallet Display**: "15% OFF All Purchases"
- **Savings Tracker**: "Saved RM247 this year"
- **Special Offers**: Birthday discounts, seasonal bonuses

### Staff Workflow
```
Scan QR → See Discount Rate → Apply at Checkout → Confirm Savings Amount
```

---

## 💳 5. CASHBACK CARD
*"Get 5% cashback on purchases" - Earn money back to spend*

### Business Use Cases  
- **Primary**: Supermarkets, petrol stations, retail chains, restaurants
- **Model**: Percentage return as spendable credit
- **Psychology**: Getting money back, compound savings

### How It Works
```
Customer Spends RM100 → Earns RM5 Cashback → Accumulates Credit → Uses for Future Purchases
```

### Configuration Options
- **Cashback Rate**: 2%, 3%, 5%, 7% of purchase amount
- **Minimum Redemption**: RM5, RM10, RM20 minimum to use
- **Maximum Cashback**: Monthly/annual caps if needed
- **Expiry**: 6/12/24 months or no expiry

### Data Structure
```
cashback_balance: 42.75    // Available cashback credit
cashback_rate: 5.0         // Percentage earned
cashback_lifetime: 347.25  // Total ever earned
min_redemption: 10.00      // Minimum to redeem
last_earned: "2024-01-10"
```

### Customer Experience
- **Wallet Display**: "Cashback Balance: RM42.75"
- **Earning Rate**: "Earn 5% back on all purchases"
- **Usage**: "Available to spend on your next visit"

### Staff Workflow
```
Scan QR → See Available Cashback → Customer Chooses to Use/Save → Process Payment
```

---

## 🎫 6. MULTIPASS CARD  
*"10-visit gym pass" - Prepaid sessions/visits*

### Business Use Cases
- **Primary**: Fitness studios, car wash packages, beauty treatments, massage
- **Model**: Prepaid package consumption
- **Psychology**: Bulk purchase savings, commitment

### How It Works
```
Customer Buys 10-Visit Pass → Each Visit Uses 1 Credit → Track Remaining → Renew When Empty
```

### Configuration Options
- **Pass Types**: 5/10/15/20 visit packages
- **Package Pricing**: Bulk discount pricing
- **Validity**: 30/60/90 days or no expiry
- **Service Scope**: All services or specific treatments

### Data Structure
```
passes_total: 10          // Original package size
passes_used: 6            // Sessions consumed
passes_remaining: 4       // Available sessions
package_type: "10-visit premium"
purchase_date: "2024-01-01"
expiry_date: "2024-04-01"
valid_services: ["all"]
```

### Customer Experience
- **Wallet Display**: "4 visits remaining (of 10)"
- **Progress Bar**: Visual consumption indicator
- **Expiry**: "Valid until April 1, 2024"

### Staff Workflow
```
Scan QR → See Available Visits → Deduct 1 Visit → Confirm Service → Update Balance
```

---

## 🎟️ 7. COUPON CARD
*"Special offers & flash deals" - Limited-time vouchers*

### Business Use Cases
- **Primary**: eCommerce, F&B promos, retail campaigns, event marketing
- **Model**: Time-sensitive offers and promotions
- **Psychology**: Urgency, exclusive deals, surprise rewards

### How It Works
```
Business Creates Coupon → Sends to Customers → Customer Uses Before Expiry → One-time Redemption
```

### Configuration Options
- **Coupon Types**: Fixed discount, percentage off, buy-one-get-one
- **Distribution**: All customers, segments, individuals, birthdays
- **Validity**: 1-30 days, specific dates, special occasions
- **Usage**: Single-use, limited quantity, minimum purchase

### Data Structure
```
active_coupons: [
  {
    id: "BDAY2024",
    type: "birthday_special", 
    discount: "Free dessert",
    expires: "2024-01-25",
    used: false
  }
]
coupon_history: [...]      // Past coupons redeemed
notifications_enabled: true
```

### Customer Experience
- **Wallet Display**: "3 Active Offers Available"
- **Coupon List**: "Birthday Special: Free Dessert (expires in 3 days)"
- **Notifications**: Push alerts for new coupons

### Staff Workflow
```
Scan QR → See Available Coupons → Customer Chooses → Validate/Redeem → Mark as Used
```

---

## 🎁 8. GIFT CARD
*"RM100 prepaid value" - Prepaid spending balance*

### Business Use Cases
- **Primary**: Retail stores, cafés, automotive services, holiday campaigns
- **Model**: Prepaid monetary value, gifting
- **Psychology**: Gift giving, prepaid convenience

### How It Works
```
Purchase/Receive Gift Card → Has Monetary Value → Spend Down Balance → Reload or Replace
```

### Configuration Options
- **Denominations**: RM25, RM50, RM100, RM200, custom amounts
- **Gifting**: Can be transferred to others via QR/email
- **Reload**: Top-up capability or single-use
- **Expiry**: 12/24/36 months or no expiry

### Data Structure
```
balance_current: 67.50     // Available balance
balance_original: 100.00   // Starting amount
transaction_history: [
  { date: "2024-01-15", amount: -32.50, description: "Coffee & pastry" }
]
gifted_by: "sarah@email.com"  // If received as gift
expires: "2025-01-01"
reloadable: false
```

### Customer Experience
- **Wallet Display**: "Gift Card Balance: RM67.50"
- **Usage History**: Track spending
- **Gifting**: "Send this gift card to someone"

### Staff Workflow
```
Scan QR → See Available Balance → Enter Purchase Amount → Deduct from Balance → Confirm
```

---

## 🔄 Universal QR Code System

### Single QR Code Per Customer Per Business
**Every card type uses the same QR code system:**

```
QR Code Contains:
- Customer ID
- Business ID  
- Card Type
- Security Hash
- Timestamp
```

### Staff Scanner Workflow (Same for All Card Types)
```
1. Staff opens mobile scanner
2. Scans customer QR code
3. System identifies card type automatically
4. Displays appropriate interface:
   - Stamp Card: Add/remove stamps
   - Points Card: Add points (enter amount)
   - Membership: Apply tier benefits  
   - Discount: Show discount rate
   - Cashback: Show balance, redeem option
   - Multipass: Deduct visit, show remaining
   - Coupon: Show active offers
   - Gift Card: Show balance, deduct amount
5. Staff takes action
6. Customer's wallet updates instantly
```

---

## 📱 Customer Access & Wallet Integration

### Primary Access: Web Portal ✅
**All card types accessible via web browser:**
- Login via OTP (SMS verification)
- View QR code for staff scanning
- Real-time balance updates
- Transaction history
- Profile management
- Works on any device
- No installation required

### Optional: Wallet Integration
**Customers can optionally download to Apple/Google Wallet:**

#### Apple Wallet Pass Structure
**All card types can generate .pkpass files with:**
- Business branding (logo, colors)
- Card type-specific display fields
- QR code for scanning
- Real-time balance updates
- Location-based notifications (optional)
- Offline access

#### Google Wallet Pass Structure
**All card types can create Google Pay loyalty objects with:**
- Business branding
- Card type-specific layouts
- QR/barcode for scanning
- Automatic balance synchronization
- Push notification updates
- Quick home screen access

### Universal Updates (Web + Wallet)
**When staff processes transaction:**
1. Database updates instantly
2. Web portal reflects new balance immediately
3. If wallet pass exists: Push notification sent to device
4. Wallet pass refreshes automatically
5. Customer sees new balance in both web and wallet

---

## 🎨 Mobile Design Standards

### Customer Wallet Card Display
**Consistent across all card types:**
- Business logo prominently displayed
- Card type clearly identified
- Current status/balance large and clear
- QR code easily accessible
- Progress indicators where applicable
- Business contact info/location

### Staff Mobile Interface
**Unified scanning interface:**
- Large "SCAN" button (camera activation)
- Customer info display after scan
- Card type-specific action buttons
- Transaction history access
- Manual customer search option
- Simple, thumb-friendly design

---

## 🚀 Business Owner Setup Process

### Card Type Selection Wizard
```
1. Business owner logs into admin panel
2. Clicks "Setup Loyalty Program"
3. Sees 8 card type options with descriptions
4. Clicks preferred card type
5. Configuration form appears (specific to card type)
6. Sets parameters (stamps needed, points rate, discounts, etc.)
7. Uploads logo, chooses colors
8. Reviews and confirms
9. System generates QR codes and staff accounts
10. Program goes live immediately
```

### Configuration Simplicity
**Each card type has maximum 5 configuration fields:**
- Primary setting (stamps needed, discount rate, etc.)
- Reward/benefit description
- Business branding (logo, colors)
- Expiry/validity rules
- Special conditions

---

## 🎨 Wallet Card Visual Customization

### Business Logo & Branding Requirements

Every loyalty card in Apple Wallet and Google Wallet can be fully customized with business branding to maintain professional appearance and brand consistency.

#### Logo Upload System
**Supported Formats**: PNG, JPG, SVG (PNG recommended for best quality)
**Required Sizes**:
- **Logo**: 60×60px minimum, 300×300px maximum (square format preferred)
- **Background Header**: 375×144px for Apple Wallet
- **Brand Strip**: 375×32px for Google Wallet pass header

#### Visual Customization Options

**Business Logo Placement**:
- Primary logo displayed on card front
- Auto-scaling for different wallet views
- Maintains aspect ratio across all devices

**Background Images** (Optional):
- Header background for enhanced branding
- Subtle patterns or gradients
- Must not interfere with text readability

**Color Schemes**:
- Primary brand color (used for card theme)
- Secondary color (for accents and highlights)
- Text color auto-adjusted for contrast
- Supports hex codes or color picker

**Text Customization**:
- Business name (automatically from setup)
- Card descriptions and labels
- Custom terms and conditions
- Multiple language support

#### Admin Panel Customization Interface

**Upload Process**:
1. **Logo Manager**: Drag-and-drop logo upload with instant preview
2. **Image Optimizer**: Automatic resizing and format conversion
3. **Preview Mode**: See exactly how card appears in Apple/Google Wallet
4. **Device Testing**: Preview on different phone sizes and orientations

**Quality Validation**:
- Automatic image compression for optimal file size
- Resolution check ensures wallet display quality
- Format conversion (JPG → PNG for transparency)
- File size optimization (keeps under wallet limits)

**Live Preview System**:
```
┌─────────────────────────────────┐
│ [Business Logo]    LoyalTea     │ ← Header with logo
│ ────────────────────────────    │
│ STAMP CARD                      │ ← Card type
│ ⭐⭐⭐⭐⭐ ○○○○○              │ ← Progress
│ 5/10 stamps collected           │
│ Next reward: Free Coffee        │
└─────────────────────────────────┘
```

#### Technical Implementation for Developers

**Apple Wallet (.pkpass) Logo Integration**:
- Logo files stored in pass bundle as `logo.png`, `logo@2x.png`
- Background images as `background.png`, `background@2x.png`
- Color definitions in `pass.json` style section

**Google Wallet Pass Logo Integration**:
- Logo images uploaded via Google Wallet API
- Image URLs stored in pass object heroImage/logoImage fields
- Color schemes defined in hexColor properties

**Storage & Delivery**:
- Business logos stored in secure cloud storage
- CDN delivery for fast wallet loading
- Automatic image optimization pipeline
- Version control for logo updates

#### Business Benefits

**Professional Appearance**:
- Consistent brand recognition in customer wallets
- Premium feel increases customer trust
- Professional design encourages usage

**Marketing Value**:
- Brand visibility every time customer opens wallet
- Logo reinforcement builds brand recall
- Professional cards encourage word-of-mouth

**Customer Experience**:
- Easy identification among multiple loyalty cards
- Visual appeal increases engagement
- Professional appearance builds confidence

#### Setup Wizard Integration

**Step 3: Branding Setup** (Enhanced from existing business setup):
1. Upload business logo (required)
2. Choose color scheme (optional, defaults to blue)
3. Preview wallet card appearance
4. Test on sample card types
5. Approve final design

**Ongoing Management**:
- Update logo anytime from business dashboard
- Seasonal branding changes
- A/B test different color schemes
- Monitor engagement with different designs

---

## ✅ Success Metrics Per Card Type

### Stamp Cards
- **Completion Rate**: % of customers who complete stamp cards
- **Return Frequency**: Days between visits
- **Redemption Rate**: % of completed cards redeemed

### Points Cards  
- **Average Points Per Customer**: Engagement level
- **Redemption Ratio**: Points redeemed vs earned
- **Purchase Frequency**: Visits per month

### Membership Cards
- **Tier Progression**: % customers advancing tiers
- **Benefit Usage**: Which perks used most
- **Retention Rate**: Monthly active members

### Discount Cards
- **Usage Frequency**: Discounts applied per month
- **Savings Amount**: Total customer savings
- **Purchase Increase**: Spend lift with discount

### Cashback Cards
- **Cashback Accumulation**: Average balance per customer
- **Redemption Frequency**: How often cashback used
- **Purchase Behavior**: Spending patterns

### Multipass Cards
- **Package Completion**: % of passes fully used
- **Usage Pattern**: Visit frequency within validity
- **Renewal Rate**: Customers buying new packages

### Coupon Cards
- **Open Rate**: % customers viewing coupons
- **Redemption Rate**: % coupons actually used
- **Response Time**: Days to redeem after receiving

### Gift Cards
- **Utilization Rate**: % of gift card value used
- **Breakage**: Unused gift card value
- **Viral Coefficient**: Gift cards sent to new customers

---

**This specification covers all 8 loyalty card types with complete technical, business, and user experience details for the LoyalTea "Simplest Loyalty Program" system.**