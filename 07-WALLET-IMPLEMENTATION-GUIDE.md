# 📱 Apple Wallet & Google Wallet Implementation Guide
*Developer's step-by-step implementation guide for LoyalTea wallet integration*

---

## 🎯 What This Guide Covers

This guide explains **exactly what developers need to do** to implement Apple Wallet and Google Wallet integration in LoyalTea - not the code itself, but **the process, requirements, and steps** to make it work.

**What You'll Learn:**
- What accounts and certificates you need to set up
- Step-by-step implementation process
- How the wallet systems actually work
- What files and data structures are required
- Testing procedures and deployment steps
- Troubleshooting common issues

**What This Is NOT:**
- Code examples (those are in 03-TECHNICAL-GUIDE.md)
- Business explanations (those are in other docs)
- This is purely the **implementation roadmap for developers**

---

## 🍎 Apple Wallet Implementation Process

### Step 1: Apple Developer Account Setup (Required First!)

**What You Need:**
1. **Apple Developer Account** - $99/year subscription
2. **Apple ID** associated with your business
3. **Business verification** documents

**How to Set Up:**
1. Go to [developer.apple.com](https://developer.apple.com)
2. Click "Account" and sign in with Apple ID
3. Select "Join the Apple Developer Program"
4. Choose "Company" account type (not Individual)
5. Pay $99 annual fee
6. Wait 1-2 business days for approval

**What You Get:**
- Access to PassKit APIs
- Ability to create Pass Type IDs
- Certificate generation tools
- Testing and distribution rights

### Step 2: Pass Type ID Registration

**What This Is:**
A unique identifier for your loyalty card passes (like `pass.com.loyaltea.loyalty`)

**How to Create:**
1. Log into [developer.apple.com](https://developer.apple.com)
2. Go to "Certificates, Identifiers & Profiles"
3. Select "Identifiers" → "Pass Type IDs"
4. Click "+" to create new Pass Type ID
5. Enter description: "LoyalTea Loyalty Cards"
6. Enter identifier: `pass.com.loyaltea.loyalty`
7. Click "Register"

**Important:** Write down this Pass Type ID - you'll need it in your code!

### Step 3: Certificate Generation

**You Need 2 Certificates:**

**Certificate 1: Pass Type ID Certificate**
1. In Apple Developer portal, go to "Certificates"
2. Click "+" to create new certificate
3. Select "Pass Type ID Certificate"
4. Choose your Pass Type ID from dropdown
5. Upload Certificate Signing Request (CSR) file
6. Download the certificate (.cer file)
7. Install it in your Keychain (double-click on Mac)

**Certificate 2: WWDR Certificate**
1. Go to [Apple PKI page](https://www.apple.com/certificateauthority/)
2. Download "Worldwide Developer Relations - G4" certificate
3. Install it in your Keychain

**Export Private Keys:**
1. Open Keychain Access on Mac
2. Find your Pass Type ID certificate
3. Right-click → Export certificate with private key
4. Save as .p12 file with password
5. Convert to PEM format using OpenSSL (or online converter)

### Step 4: Understanding Pass Bundle Structure

**What Apple Wallet Needs:**
A `.pkpass` file which is actually a ZIP file containing:

```
loyalty-pass.pkpass (ZIP file containing):
├── pass.json          (Pass data and styling)
├── manifest.json      (File checksums)
├── signature          (Cryptographic signature)
├── logo.png          (Business logo - 29x29 points)
├── logo@2x.png       (High-res logo - 58x58 pixels)
├── logo@3x.png       (Super high-res - 87x87 pixels)
└── background.png     (Optional background image)
```

**pass.json Structure (What Your Code Must Generate):**
```json
{
  "formatVersion": 1,
  "passTypeIdentifier": "pass.com.loyaltea.loyalty",
  "serialNumber": "customer_12345",
  "teamIdentifier": "YOUR_TEAM_ID",
  "organizationName": "LoyalTea",
  "description": "Mario's Pizza Loyalty Card",
  "logoText": "Mario's Pizza",
  "foregroundColor": "rgb(255, 255, 255)",
  "backgroundColor": "rgb(60, 65, 76)",
  "storeCard": {
    "primaryFields": [
      {
        "key": "balance",
        "label": "Stamps",
        "value": "7 of 10"
      }
    ],
    "secondaryFields": [
      {
        "key": "reward",
        "label": "Next Reward",
        "value": "Free Coffee"
      }
    ]
  },
  "barcode": {
    "format": "PKBarcodeFormatQR",
    "message": "loyaltea://customer/12345",
    "messageEncoding": "iso-8859-1"
  }
}
```

### Step 5: Pass Generation Process (What Your Code Does)

**When Customer Registers:**
1. **Create pass.json** with customer data
2. **Add business branding** (logo files, colors)
3. **Generate QR code** with customer ID
4. **Calculate manifest.json** (SHA1 hashes of all files)
5. **Sign the manifest** using your certificates
6. **ZIP everything** into .pkpass file
7. **Serve file** with correct MIME type

**Key Implementation Points:**
- Use PassKit library (like `passkit-generator` for Node.js)
- Store certificates securely in environment variables
- Generate unique serial numbers for each customer
- Handle logo resizing automatically
- Implement proper error handling for certificate issues

---

## 📱 Google Wallet Implementation Process

### Step 1: Google Cloud Console Setup

**What You Need:**
1. **Google Cloud Account** (free to start)
2. **Project in Google Cloud Console**
3. **Wallet API enabled**

**Setup Steps:**
1. Go to [console.cloud.google.com](https://console.cloud.google.com)
2. Create new project: "LoyalTea Wallet Integration"
3. Enable Google Wallet API:
   - Go to "APIs & Services" → "Library"
   - Search for "Google Wallet API"
   - Click "Enable"

### Step 2: Service Account Creation

**What This Is:**
A special account that your server uses to authenticate with Google

**How to Create:**
1. In Google Cloud Console, go to "IAM & Admin" → "Service Accounts"
2. Click "Create Service Account"
3. Name: "loyaltea-wallet-service"
4. Description: "Service account for wallet pass generation"
5. Click "Create and Continue"
6. Grant role: "Wallet Objects Admin"
7. Click "Done"

**Download Credentials:**
1. Click on your new service account
2. Go to "Keys" tab
3. Click "Add Key" → "Create New Key"
4. Choose "JSON" format
5. Download the .json file
6. **Keep this file secure!** - It's your authentication

### Step 3: Issuer Account Registration

**What This Is:**
Your business must be registered as a "pass issuer" with Google

**How to Register:**
1. Go to [pay.google.com/business/console](https://pay.google.com/business/console)
2. Sign in with Google account
3. Click "Get Started" under Google Wallet API
4. Fill out business information:
   - Business name: "LoyalTea"
   - Business model: "Loyalty/Rewards"
   - Website: Your business website
5. Submit for review (usually approved within 1-2 days)

**Get Your Issuer ID:**
After approval, you'll get an Issuer ID (like `3388000000022471234`) - save this!

### Step 4: Understanding Google Wallet Structure

**Google Wallet Uses Two Components:**

**1. Class Definition (defines the card template):**
```json
{
  "id": "3388000000022471234.loyaltea_loyalty_class",
  "classTemplateInfo": {
    "cardTemplateOverride": {
      "cardRowTemplateInfos": [
        {
          "oneItem": {
            "item": {
              "firstValue": {
                "fields": [
                  {
                    "fieldPath": "object.textModulesData['points']"
                  }
                ]
              }
            }
          }
        }
      ]
    }
  },
  "issuerName": "LoyalTea",
  "reviewStatus": "UNDER_REVIEW"
}
```

**2. Object Instance (individual customer card):**
```json
{
  "id": "3388000000022471234.customer_12345",
  "classId": "3388000000022471234.loyaltea_loyalty_class",
  "state": "ACTIVE",
  "barcode": {
    "type": "QR_CODE",
    "value": "loyaltea://customer/12345"
  },
  "textModulesData": [
    {
      "id": "points",
      "header": "Current Balance",
      "body": "245 Points"
    }
  ],
  "heroImage": {
    "sourceUri": {
      "uri": "https://yourdomain.com/logos/mario_pizza.png"
    }
  }
}
```

### Step 5: Implementation Process (What Your Code Does)

**One-Time Setup (in your application):**
1. **Create Class Definition** using Google Wallet API
2. **Upload business logos** to your CDN/server
3. **Store class ID** in your database

**For Each Customer Registration:**
1. **Generate Object Instance** with customer data
2. **Create JWT token** containing the object
3. **Generate "Add to Google Wallet" link**
4. **Return link to customer's browser**

**JWT Token Structure:**
```json
{
  "iss": "your-service-account-email@your-project.iam.gserviceaccount.com",
  "aud": "google",
  "typ": "savetowallet",
  "iat": 1234567890,
  "exp": 1234567890,
  "payload": {
    "genericObjects": [
      {
        // Your object data here
      }
    ]
  }
}
```

**Key Implementation Points:**
- Use Google Auth Library for JWT signing
- Sign JWTs with your service account private key
- Handle image uploads and URL generation
- Implement proper error handling for API failures
- Use batch operations for better performance

---

## 🔄 Universal QR Code System Implementation

### Understanding the QR Code Flow

**What Makes This "Universal":**
- One QR code format works for all 8 card types
- Same scanning process whether customer uses Apple Wallet, Google Wallet, or mobile web
- Staff interface automatically detects card type and customer

### QR Code Data Structure

**What the QR Code Contains:**
```
loyaltea://customer/12345/business/67890?sig=abc123&ts=1234567890
```

**Breakdown:**
- `loyaltea://` - Custom protocol scheme
- `customer/12345` - Unique customer ID
- `business/67890` - Business ID
- `sig=abc123` - Cryptographic signature (prevents tampering)
- `ts=1234567890` - Timestamp (prevents replay attacks)

### Implementation Steps

**Step 1: QR Code Generation (When Customer Registers)**
1. **Generate unique customer ID** (UUID)
2. **Get business ID** from current business context
3. **Create timestamp** (current Unix timestamp)
4. **Generate signature** using HMAC-SHA256 with secret key
5. **Construct QR data string**
6. **Generate QR code image** using QR library
7. **Embed in both wallet passes and mobile web**

**Step 2: QR Code Scanning (Staff Interface)**
1. **Staff opens scanning interface** (big scan button)
2. **Camera captures QR code**
3. **Parse QR data string**
4. **Validate signature** (security check)
5. **Check timestamp** (not too old - prevent replay)
6. **Look up customer and business data** from database
7. **Display appropriate interface** based on card type
8. **Show transaction options** (add stamp, points, etc.)

**Step 3: Transaction Processing**
1. **Staff selects action** (Add Stamp, Add Points, Apply Discount)
2. **Update customer loyalty data** in database
3. **Trigger wallet pass updates** (Apple & Google push notifications)
4. **Log transaction** for business analytics
5. **Show confirmation** to staff and customer

---

## 🔧 Development Environment Setup

### Required Development Tools

**For Apple Wallet:**
- **macOS computer** (required for certificate management)
- **Xcode or Xcode Command Line Tools** (for certificate operations)
- **OpenSSL** (for certificate conversion)
- **Node.js with PassKit library** (or equivalent in your language)

**For Google Wallet:**
- **Any development environment** (Windows, Mac, Linux)
- **Google Cloud SDK** (optional, for CLI operations)
- **JWT library** for your programming language
- **HTTP client library** for API calls

### Environment Variables You Need

**Apple Wallet Configuration:**
```env
APPLE_TEAM_ID=YOUR_TEAM_ID
APPLE_PASS_TYPE_ID=pass.com.loyaltea.loyalty
APPLE_WWDR_CERT_PATH=/path/to/wwdr.pem
APPLE_SIGNER_CERT_PATH=/path/to/signer.pem
APPLE_SIGNER_KEY_PATH=/path/to/signer.key
APPLE_SIGNER_KEY_PASSPHRASE=your_passphrase
```

**Google Wallet Configuration:**
```env
GOOGLE_SERVICE_ACCOUNT_KEY_PATH=/path/to/service-account.json
GOOGLE_ISSUER_ID=3388000000022471234
GOOGLE_APPLICATION_NAME=LoyalTea
```

**Universal Configuration:**
```env
QR_CODE_SECRET_KEY=your_secret_key_for_signing
BUSINESS_LOGO_CDN_URL=https://your-cdn.com/logos/
WALLET_PASS_BASE_URL=https://your-api.com/wallet/
```

### File Organization Structure

```
src/
├── services/
│   ├── apple-wallet.service.js    (Apple pass generation)
│   ├── google-wallet.service.js   (Google pass generation)
│   ├── qr-code.service.js         (QR generation/validation)
│   └── transaction.service.js     (Business logic)
├── certificates/
│   ├── apple/
│   │   ├── wwdr.pem              (Apple WWDR certificate)
│   │   ├── signer.pem            (Your signing certificate)
│   │   └── signer.key            (Your private key)
│   └── google/
│       └── service-account.json   (Google credentials)
├── templates/
│   ├── apple-pass-template/       (Apple pass bundle template)
│   └── google-class-template.json (Google class definition)
└── public/
    └── logos/                     (Business logo storage)
```

---

## 🧪 Testing Procedures

### Apple Wallet Testing

**Phase 1: Certificate Testing**
1. **Verify certificates installed** correctly in Keychain
2. **Test certificate export** and PEM conversion
3. **Generate test pass** with dummy data
4. **Validate pass structure** using Apple's validator tools

**Phase 2: Pass Generation Testing**
1. **Create test business** with logo and branding
2. **Generate passes** for different card types
3. **Test pass installation** on physical iOS devices
4. **Verify pass appearance** matches design expectations

**Phase 3: Update Testing**
1. **Simulate transactions** (add stamps, points, etc.)
2. **Send push notifications** to update passes
3. **Verify passes update** correctly on devices
4. **Test edge cases** (network issues, expired passes)

### Google Wallet Testing

**Phase 1: API Authentication Testing**
1. **Verify service account** can authenticate with Google
2. **Test API permissions** for wallet operations
3. **Create test class definitions**
4. **Validate JWT token generation** and signing

**Phase 2: Pass Creation Testing**
1. **Create test objects** for different card types
2. **Generate "Add to Wallet" links**
3. **Test pass installation** on Android devices
4. **Verify pass appearance** and functionality

**Phase 3: Update Testing**
1. **Update pass objects** via API
2. **Test real-time synchronization**
3. **Verify passes update** correctly on devices
4. **Test bulk operations** for performance

### QR Code System Testing

**Phase 1: Generation Testing**
1. **Generate QR codes** for test customers
2. **Verify QR data structure** is correct
3. **Test signature validation** algorithm
4. **Ensure QR codes scan** properly with different cameras

**Phase 2: Scanning Testing**
1. **Test staff scanning interface** on various devices
2. **Verify customer lookup** works correctly
3. **Test transaction processing** for all card types
4. **Validate security checks** (signature, timestamp)

**Phase 3: Integration Testing**
1. **Test complete customer journey** (registration to transaction)
2. **Verify wallet updates** after QR transactions
3. **Test error handling** (invalid QR codes, network issues)
4. **Performance test** with multiple simultaneous scans

---

## 🚀 Deployment Process

### Pre-Deployment Checklist

**Apple Wallet Ready:**
- [ ] Apple Developer Account active and paid
- [ ] Pass Type ID registered and active
- [ ] Certificates generated and exported properly
- [ ] Test passes work on physical iOS devices
- [ ] Push notification setup configured

**Google Wallet Ready:**
- [ ] Google Cloud project created and configured
- [ ] Wallet API enabled and tested
- [ ] Service account created with proper permissions
- [ ] Issuer account approved by Google
- [ ] Test passes work on Android devices

**Infrastructure Ready:**
- [ ] Secure certificate storage implemented
- [ ] CDN configured for logo hosting
- [ ] Database schema supports wallet pass IDs
- [ ] API endpoints for wallet operations created
- [ ] Error logging and monitoring configured

### Deployment Steps

**Step 1: Production Environment Setup**
1. **Deploy certificates** to secure environment variables
2. **Configure CDN** with business logo uploads
3. **Set up database** with proper indexes for performance
4. **Deploy API endpoints** for pass generation and updates
5. **Configure monitoring** for wallet operations

**Step 2: Business Onboarding Process**
1. **Create business setup wizard** for logo upload
2. **Implement card type selection** interface
3. **Build pass preview system** for businesses
4. **Test complete business configuration** flow

**Step 3: Customer Registration System**
1. **Deploy QR code generation** system
2. **Implement registration forms** with wallet integration
3. **Set up pass distribution** endpoints
4. **Configure automatic pass updates**

**Step 4: Staff Interface Deployment**
1. **Deploy scanning interface** with large UI buttons
2. **Implement transaction processing** system
3. **Set up real-time pass updates** after transactions
4. **Configure staff authentication** and permissions

### Monitoring and Maintenance

**What to Monitor:**
- **Pass generation success rates** (should be >95%)
- **Wallet update delivery** (Apple & Google push notifications)
- **API error rates** and response times
- **Certificate expiration dates** (set alerts 30 days before)
- **Customer adoption rates** by wallet type

**Regular Maintenance Tasks:**
- **Certificate renewal** (Apple certificates expire annually)
- **API quota monitoring** (Google Wallet usage limits)
- **Performance optimization** based on usage patterns
- **Security updates** for libraries and dependencies

---

## ❓ Common Implementation Questions

### "Which Wallet Should I Implement First?"

**Recommendation: Start with Apple Wallet**
- Higher customer engagement rates
- More reliable push notification delivery
- Better visual design consistency
- Premium brand association

**Then Add Google Wallet:**
- Covers Android users (larger market share in Malaysia)
- Free to implement and test
- Faster approval process
- Good for businesses targeting younger demographics

### "Do I Need a Mac Computer?"

**For Apple Wallet: Yes (for initial setup)**
- Certificate management requires macOS
- One-time setup on Mac, then deploy certificates to production
- Can use cloud Mac services if you don't own one
- After initial setup, production runs on any server

**For Google Wallet: No**
- Works on Windows, Mac, or Linux
- All operations via web APIs
- No special development tools required

### "How Long Does Implementation Take?"

**Apple Wallet Timeline:**
- Week 1: Developer account setup and approval
- Week 2: Certificate generation and testing
- Week 3: Pass generation implementation
- Week 4: Testing and deployment

**Google Wallet Timeline:**
- Day 1: Google Cloud setup and API enabling
- Day 2: Service account creation and testing
- Day 3-5: Issuer registration and approval
- Week 2: Implementation and testing

**Universal QR System:**
- Week 1: QR code generation and validation
- Week 2: Staff scanning interface
- Week 3: Transaction processing system
- Week 4: End-to-end testing

### "What If Something Goes Wrong?"

**Common Issues and Solutions:**

**Apple Certificate Problems:**
- Keep certificates backed up securely
- Set calendar reminders for annual renewal
- Test certificate validity monthly
- Have alternative authentication methods ready

**Google API Issues:**
- Monitor API quotas and usage
- Implement exponential backoff for rate limiting
- Use batch operations for better performance
- Cache frequent API calls

**QR Code Security:**
- Use strong cryptographic signatures
- Implement timestamp validation
- Log all QR scanning attempts
- Have fraud detection algorithms

### "How Do I Handle Updates?"

**Wallet Pass Updates:**
- Apple: Push notifications via APNs
- Google: Direct API calls for instant updates
- Fallback: Customer re-scans QR if push fails
- Batch updates for better performance

**Business Branding Changes:**
- Logo updates require new pass generation
- Color changes can be applied to existing passes
- Test changes on small customer group first
- Gradual rollout to all customers

---

## 🎯 Success Metrics and KPIs

### Technical Metrics
- **Pass Generation Success Rate**: >95% (Apple and Google)
- **QR Code Scan Success Rate**: >98% first attempt
- **Pass Update Delivery Rate**: >90% within 5 seconds
- **API Response Time**: <500ms average
- **System Uptime**: >99.5% availability

### Business Metrics
- **Wallet Adoption Rate**: Target 80% within 6 months
- **Customer Engagement**: 70% increase in visit frequency
- **Transaction Speed**: 50% reduction in processing time
- **Staff Satisfaction**: Easy interface, minimal training needed
- **Customer Satisfaction**: >90% positive feedback on convenience

### Financial Metrics
- **Setup Cost**: $99 Apple + development time
- **Ongoing Costs**: Minimal (Google free tier, Apple no per-transaction)
- **ROI Timeline**: Break-even within 3 months
- **Cost Savings**: Reduced plastic card printing, staff training time
- **Revenue Increase**: Higher customer retention and visit frequency

---

## 🚀 Quick Start Implementation Plan

### Phase 1: Foundation (Week 1-2)
**Day 1-3: Account Setup**
- [ ] Create Apple Developer account ($99)
- [ ] Set up Google Cloud project (free)
- [ ] Enable Google Wallet API
- [ ] Create service accounts and credentials

**Day 4-7: Certificate Management**
- [ ] Generate Apple Pass Type ID
- [ ] Create Apple certificates (requires Mac)
- [ ] Export and convert certificates to PEM format
- [ ] Test certificate validity

**Week 2: Basic Implementation**
- [ ] Set up development environment
- [ ] Install required libraries and dependencies
- [ ] Configure environment variables
- [ ] Create basic pass generation functions

### Phase 2: Core Features (Week 3-4)
**Week 3: Pass Generation**
- [ ] Implement Apple pass generation (.pkpass files)
- [ ] Implement Google pass generation (JWT tokens)
- [ ] Add business logo and branding system
- [ ] Create QR code generation with signatures

**Week 4: Integration**
- [ ] Build customer registration flow
- [ ] Create "Add to Wallet" buttons and links
- [ ] Implement staff scanning interface
- [ ] Add transaction processing system

### Phase 3: Testing & Deployment (Week 5-6)
**Week 5: Comprehensive Testing**
- [ ] Test passes on physical iOS and Android devices
- [ ] Verify pass updates work correctly
- [ ] Test QR code scanning with different cameras
- [ ] Load test with multiple simultaneous operations

**Week 6: Production Deployment**
- [ ] Deploy to production environment
- [ ] Configure monitoring and alerts
- [ ] Train first business customers
- [ ] Monitor initial customer adoption

### Phase 4: Optimization (Ongoing)
**Month 2-3: Performance Optimization**
- [ ] Monitor pass generation speeds
- [ ] Optimize API calls and database queries
- [ ] Implement caching where appropriate
- [ ] Scale infrastructure based on usage

**Month 3-6: Feature Enhancement**
- [ ] Add advanced branding options
- [ ] Implement analytics and reporting
- [ ] Add bulk operations for large businesses
- [ ] Create admin dashboard for monitoring

---

## 🎯 Final Developer Checklist

### Before You Start Coding
- [ ] **Understand the business requirements** - Review all LoyalTea documentation
- [ ] **Set up development accounts** - Apple Developer ($99) and Google Cloud (free)
- [ ] **Prepare development environment** - Mac for Apple certificates, any OS for Google
- [ ] **Install required tools** - Xcode tools, OpenSSL, Node.js or your preferred language

### During Implementation
- [ ] **Follow security best practices** - Secure certificate storage, encrypted communications
- [ ] **Implement proper error handling** - Graceful failures, retry mechanisms
- [ ] **Add comprehensive logging** - Track pass generation, updates, and failures
- [ ] **Test on real devices** - Both iOS and Android physical devices

### Before Launch
- [ ] **Complete end-to-end testing** - Full customer journey from registration to transaction
- [ ] **Load test the system** - Ensure it handles expected traffic
- [ ] **Set up monitoring** - Track success rates, performance, errors
- [ ] **Prepare rollback plan** - What to do if something goes wrong

### After Launch
- [ ] **Monitor key metrics** - Pass generation success, update delivery, customer adoption
- [ ] **Collect user feedback** - From both businesses and customers
- [ ] **Plan regular maintenance** - Certificate renewals, API updates, performance optimization
- [ ] **Scale infrastructure** - Based on actual usage patterns

---

## 🏢 Multi-Business Implementation Strategy

### Understanding the Multi-Business Architecture

**LoyalTea serves multiple businesses simultaneously**, each with their own:
- Custom branding (logos, colors)
- Card type selection (stamps, points, membership, etc.)
- Reward configurations
- Customer base
- Staff access

**Key Implementation Challenges:**
- Each business needs unique wallet passes
- Staff can only access their business data
- Customers can have cards from multiple businesses
- Universal QR system must identify business context

### Database Schema for Multi-Business Support

**Business Table Structure:**
```sql
CREATE TABLE businesses (
  id UUID PRIMARY KEY,
  name VARCHAR(255) NOT NULL,
  email VARCHAR(255) UNIQUE,
  logo_url VARCHAR(500),
  brand_colors JSONB, -- {primary: "#667eea", secondary: "#764ba2"}
  card_type VARCHAR(50) NOT NULL, -- 'stamp', 'points', 'membership', etc.
  card_config JSONB, -- Card-specific configuration
  wallet_config JSONB, -- Apple/Google wallet settings
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);
```

**Customer Loyalty Multi-Business:**
```sql
CREATE TABLE customer_loyalty (
  id UUID PRIMARY KEY,
  customer_id UUID NOT NULL,
  business_id UUID NOT NULL REFERENCES businesses(id),
  card_type VARCHAR(50) NOT NULL,
  current_data JSONB, -- Card-specific progress data
  wallet_pass_id VARCHAR(255), -- Apple/Google pass identifier
  qr_code_data VARCHAR(500), -- Signed QR data string
  status VARCHAR(20) DEFAULT 'active',
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW(),
  UNIQUE(customer_id, business_id) -- One card per customer per business
);
```

**Staff Access Control:**
```sql
CREATE TABLE staff_access (
  id UUID PRIMARY KEY,
  user_id UUID NOT NULL,
  business_id UUID NOT NULL REFERENCES businesses(id),
  role VARCHAR(50) DEFAULT 'staff', -- 'owner', 'manager', 'staff'
  permissions JSONB, -- Role-specific permissions
  created_at TIMESTAMP DEFAULT NOW(),
  UNIQUE(user_id, business_id)
);
```

### Apple Wallet Multi-Business Implementation

**Pass Type ID Strategy:**
Since Apple requires one Pass Type ID per developer account, you need to handle multiple businesses within a single pass structure.

**Option 1: Single Pass Type ID (Recommended)**
```
Pass Type ID: pass.com.loyaltea.loyalty
Serial Number Format: {business_id}_{customer_id}
Example: pass.com.loyaltea.loyalty with serial "business_123_customer_456"
```

**pass.json Template for Any Business:**
```json
{
  "formatVersion": 1,
  "passTypeIdentifier": "pass.com.loyaltea.loyalty",
  "serialNumber": "{business_id}_{customer_id}",
  "teamIdentifier": "YOUR_TEAM_ID",
  "organizationName": "LoyalTea",
  "description": "{business_name} Loyalty Card",
  "logoText": "{business_name}",
  "foregroundColor": "{business_colors.text}",
  "backgroundColor": "{business_colors.primary}",
  "storeCard": {
    "primaryFields": [
      // Dynamic based on card_type
    ],
    "secondaryFields": [
      // Dynamic based on card_type
    ]
  },
  "barcode": {
    "format": "PKBarcodeFormatQR",
    "message": "loyaltea://customer/{customer_id}/business/{business_id}?sig={signature}&ts={timestamp}",
    "messageEncoding": "iso-8859-1"
  },
  "locations": [
    {
      "latitude": {business_latitude},
      "longitude": {business_longitude},
      "relevantText": "Show loyalty card"
    }
  ]
}
```

**Dynamic Pass Generation Service:**
```javascript
class AppleWalletService {
  async generateBusinessPass(customerId, businessId, loyaltyData) {
    const business = await Business.findById(businessId);
    const customer = await Customer.findById(customerId);
    
    // Get card-specific template
    const passTemplate = this.getCardTemplate(business.card_type);
    
    // Populate business branding
    const passData = {
      ...passTemplate,
      serialNumber: `${businessId}_${customerId}`,
      description: `${business.name} Loyalty Card`,
      logoText: business.name,
      foregroundColor: business.brand_colors.text,
      backgroundColor: business.brand_colors.primary,
      storeCard: this.buildCardFields(business.card_type, loyaltyData, business.card_config)
    };
    
    // Generate pass with business-specific assets
    return this.createPkPassFile(passData, business.logo_url, businessId);
  }
  
  getCardTemplate(cardType) {
    const templates = {
      stamp: {
        primaryFields: [
          {
            key: "progress",
            label: "Progress",
            value: "{stamps_collected} of {stamps_required}"
          }
        ],
        secondaryFields: [
          {
            key: "reward",
            label: "Reward",
            value: "{reward_description}"
          }
        ]
      },
      points: {
        primaryFields: [
          {
            key: "balance",
            label: "Points",
            value: "{current_points}"
          }
        ],
        secondaryFields: [
          {
            key: "next_reward",
            label: "Next Reward",
            value: "{points_needed} points until {reward_name}"
          }
        ]
      },
      membership: {
        primaryFields: [
          {
            key: "tier",
            label: "Status",
            value: "{membership_tier}"
          }
        ],
        secondaryFields: [
          {
            key: "benefits",
            label: "Benefits",
            value: "{tier_benefits}"
          }
        ]
      }
      // ... templates for all 8 card types
    };
    return templates[cardType];
  }
}
```

### Google Wallet Multi-Business Implementation

**Class Definition Strategy:**
Google Wallet allows multiple classes per issuer, so create one class per business for better organization.

**Class ID Format:**
```
Class ID: {issuer_id}.{business_id}_loyalty_class
Object ID: {issuer_id}.{business_id}_{customer_id}
```

**Dynamic Class Creation:**
```javascript
class GoogleWalletService {
  async createBusinessClass(businessId) {
    const business = await Business.findById(businessId);
    
    const classDefinition = {
      id: `${process.env.GOOGLE_ISSUER_ID}.${businessId}_loyalty_class`,
      issuerName: "LoyalTea",
      reviewStatus: "UNDER_REVIEW",
      classTemplateInfo: {
        cardTemplateOverride: {
          cardRowTemplateInfos: this.getCardRowTemplate(business.card_type)
        }
      },
      heroImage: {
        sourceUri: {
          uri: business.logo_url
        }
      },
      hexBackgroundColor: business.brand_colors.primary.replace('#', ''),
      localizedIssuerName: {
        defaultValue: {
          language: "en-US",
          value: business.name
        }
      }
    };
    
    // Create class via Google Wallet API
    return await this.walletClient.genericClass().insert({
      requestBody: classDefinition
    });
  }
  
  async generateCustomerObject(customerId, businessId, loyaltyData) {
    const business = await Business.findById(businessId);
    const classId = `${process.env.GOOGLE_ISSUER_ID}.${businessId}_loyalty_class`;
    
    const objectInstance = {
      id: `${process.env.GOOGLE_ISSUER_ID}.${businessId}_${customerId}`,
      classId: classId,
      state: "ACTIVE",
      barcode: {
        type: "QR_CODE",
        value: this.generateQRData(customerId, businessId)
      },
      textModulesData: this.buildTextModules(business.card_type, loyaltyData, business.card_config),
      heroImage: {
        sourceUri: {
          uri: business.logo_url
        }
      }
    };
    
    return await this.walletClient.genericObject().insert({
      requestBody: objectInstance
    });
  }
}
```

### Universal QR Code System for Multi-Business

**Enhanced QR Data Structure:**
```
loyaltea://customer/{customer_id}/business/{business_id}/card/{card_type}?sig={signature}&ts={timestamp}&v=1
```

**QR Code Generation with Business Context:**
```javascript
class QRCodeService {
  generateBusinessQR(customerId, businessId, cardType) {
    const timestamp = Math.floor(Date.now() / 1000);
    const baseData = `loyaltea://customer/${customerId}/business/${businessId}/card/${cardType}`;
    const dataToSign = `${baseData}&ts=${timestamp}`;
    
    // Sign with business-specific salt for extra security
    const signature = this.generateSignature(dataToSign, businessId);
    
    return `${baseData}?sig=${signature}&ts=${timestamp}&v=1`;
  }
  
  validateQR(qrData, scanningBusinessId) {
    const parsed = new URL(qrData);
    const customerId = parsed.pathname.split('/')[2];
    const businessId = parsed.pathname.split('/')[4];
    const cardType = parsed.pathname.split('/')[6];
    const signature = parsed.searchParams.get('sig');
    const timestamp = parsed.searchParams.get('ts');
    
    // Security validations
    if (!this.validateSignature(qrData, signature, businessId)) {
      throw new Error('Invalid QR code signature');
    }
    
    if (this.isExpired(timestamp)) {
      throw new Error('QR code expired');
    }
    
    // Business context validation
    if (scanningBusinessId !== businessId) {
      throw new Error('QR code not valid for this business');
    }
    
    return { customerId, businessId, cardType };
  }
}
```

### Staff Interface for Multi-Business

**Business Context Detection:**
```javascript
class StaffScannerService {
  async processScan(qrData, staffUserId) {
    // Get staff's business context
    const staffAccess = await StaffAccess.findOne({ user_id: staffUserId });
    const businessId = staffAccess.business_id;
    
    // Validate QR code against business context
    const qrInfo = this.qrCodeService.validateQR(qrData, businessId);
    
    // Load customer loyalty data for this business
    const customerLoyalty = await CustomerLoyalty.findOne({
      customer_id: qrInfo.customerId,
      business_id: businessId
    });
    
    if (!customerLoyalty) {
      throw new Error('Customer not found in this business');
    }
    
    // Return business-specific interface
    return this.buildTransactionInterface(customerLoyalty, staffAccess.permissions);
  }
  
  buildTransactionInterface(customerLoyalty, permissions) {
    const business = customerLoyalty.business;
    const cardType = business.card_type;
    
    // Card-specific interfaces
    const interfaces = {
      stamp: {
        displayData: {
          current: `${customerLoyalty.current_data.stamps} of ${business.card_config.stamps_required}`,
          next: `${business.card_config.stamps_required - customerLoyalty.current_data.stamps} more for ${business.card_config.reward_name}`
        },
        actions: [
          { id: 'add_stamp', label: 'Add Stamp', enabled: permissions.includes('add_stamps') }
        ]
      },
      points: {
        displayData: {
          current: `${customerLoyalty.current_data.points} points`,
          next: `${this.getNextRewardThreshold(customerLoyalty.current_data.points, business.card_config.rewards)} points until next reward`
        },
        actions: [
          { id: 'add_points', label: 'Add Points', enabled: permissions.includes('add_points') },
          { id: 'redeem_points', label: 'Redeem Points', enabled: permissions.includes('redeem_points') }
        ]
      }
      // ... interfaces for all 8 card types
    };
    
    return interfaces[cardType];
  }
}
```

### 8 Card Types Implementation Details

**Card Type Configuration System:**
```javascript
const CARD_TYPE_CONFIGS = {
  stamp: {
    required_fields: ['stamps_required', 'reward_name'],
    optional_fields: ['auto_reset', 'expiry_days'],
    wallet_template: 'stamp_template',
    transaction_types: ['add_stamp', 'redeem_reward']
  },
  
  points: {
    required_fields: ['points_per_rm', 'reward_tiers'],
    optional_fields: ['points_expiry', 'bonus_multipliers'],
    wallet_template: 'points_template',
    transaction_types: ['add_points', 'redeem_points', 'transfer_points']
  },
  
  membership: {
    required_fields: ['tiers', 'tier_benefits'],
    optional_fields: ['tier_requirements', 'tier_expiry'],
    wallet_template: 'membership_template',
    transaction_types: ['upgrade_tier', 'add_points', 'apply_benefit']
  },
  
  discount: {
    required_fields: ['discount_percentage'],
    optional_fields: ['category_discounts', 'special_occasions'],
    wallet_template: 'discount_template',
    transaction_types: ['apply_discount', 'special_discount']
  },
  
  cashback: {
    required_fields: ['cashback_rate', 'minimum_redemption'],
    optional_fields: ['category_rates', 'bonus_periods'],
    wallet_template: 'cashback_template',
    transaction_types: ['add_cashback', 'redeem_cashback']
  },
  
  multipass: {
    required_fields: ['package_options', 'validity_period'],
    optional_fields: ['package_discounts', 'transfer_allowed'],
    wallet_template: 'multipass_template',
    transaction_types: ['use_pass', 'extend_validity']
  },
  
  coupon: {
    required_fields: ['coupon_types', 'distribution_method'],
    optional_fields: ['usage_limits', 'stacking_rules'],
    wallet_template: 'coupon_template',
    transaction_types: ['issue_coupon', 'redeem_coupon']
  },
  
  gift: {
    required_fields: ['denominations', 'transfer_enabled'],
    optional_fields: ['reload_allowed', 'expiry_period'],
    wallet_template: 'gift_template',
    transaction_types: ['add_value', 'use_value', 'transfer_gift']
  }
};
```

**Transaction Processing for Each Card Type:**
```javascript
class TransactionProcessor {
  async processTransaction(loyaltyId, transactionType, transactionData, staffId) {
    const loyalty = await CustomerLoyalty.findById(loyaltyId);
    const business = await Business.findById(loyalty.business_id);
    
    // Get card-type specific processor
    const processor = this.getProcessor(business.card_type);
    
    // Process transaction
    const result = await processor.process(
      loyalty.current_data,
      business.card_config,
      transactionType,
      transactionData
    );
    
    // Update database
    await CustomerLoyalty.updateOne(
      { _id: loyaltyId },
      {
        current_data: result.newData,
        updated_at: new Date()
      }
    );
    
    // Update wallet passes
    await this.updateWalletPasses(loyaltyId, result.newData);
    
    // Log transaction
    await this.logTransaction(loyaltyId, staffId, transactionType, result);
    
    return result;
  }
  
  getProcessor(cardType) {
    const processors = {
      stamp: new StampCardProcessor(),
      points: new PointsCardProcessor(),
      membership: new MembershipCardProcessor(),
      discount: new DiscountCardProcessor(),
      cashback: new CashbackCardProcessor(),
      multipass: new MultipassCardProcessor(),
      coupon: new CouponCardProcessor(),
      gift: new GiftCardProcessor()
    };
    
    return processors[cardType];
  }
}

// Example: Stamp Card Processor
class StampCardProcessor {
  process(currentData, cardConfig, transactionType, transactionData) {
    switch (transactionType) {
      case 'add_stamp':
        return this.addStamp(currentData, cardConfig);
      case 'redeem_reward':
        return this.redeemReward(currentData, cardConfig);
      default:
        throw new Error(`Invalid transaction type: ${transactionType}`);
    }
  }
  
  addStamp(currentData, cardConfig) {
    const newStamps = (currentData.stamps || 0) + 1;
    const stampsRequired = cardConfig.stamps_required;
    
    let newData = { ...currentData, stamps: newStamps };
    let rewardEarned = false;
    
    if (newStamps >= stampsRequired) {
      // Reward earned!
      rewardEarned = true;
      if (cardConfig.auto_reset) {
        newData.stamps = newStamps - stampsRequired; // Carry over extra stamps
      }
      newData.rewards_earned = (currentData.rewards_earned || 0) + 1;
    }
    
    return {
      newData,
      changes: {
        stamps_added: 1,
        reward_earned: rewardEarned,
        message: rewardEarned ? 
          `Congratulations! You've earned a ${cardConfig.reward_name}!` : 
          `${stampsRequired - newData.stamps} more stamps until ${cardConfig.reward_name}`
      }
    };
  }
}

// Example: Points Card Processor
class PointsCardProcessor {
  process(currentData, cardConfig, transactionType, transactionData) {
    switch (transactionType) {
      case 'add_points':
        return this.addPoints(currentData, cardConfig, transactionData);
      case 'redeem_points':
        return this.redeemPoints(currentData, cardConfig, transactionData);
      default:
        throw new Error(`Invalid transaction type: ${transactionType}`);
    }
  }
  
  addPoints(currentData, cardConfig, { purchase_amount }) {
    const pointsToAdd = Math.floor(purchase_amount * cardConfig.points_per_rm);
    const newPoints = (currentData.points || 0) + pointsToAdd;
    
    // Check for tier upgrades or bonus points
    const bonusPoints = this.calculateBonusPoints(newPoints, cardConfig);
    const finalPoints = newPoints + bonusPoints;
    
    return {
      newData: {
        ...currentData,
        points: finalPoints,
        lifetime_points: (currentData.lifetime_points || 0) + pointsToAdd + bonusPoints
      },
      changes: {
        points_added: pointsToAdd,
        bonus_points: bonusPoints,
        message: `Earned ${pointsToAdd} points${bonusPoints > 0 ? ` + ${bonusPoints} bonus` : ''}!`
      }
    };
  }
}
```

### Business Onboarding Flow

**Step-by-Step Business Setup:**
```javascript
class BusinessOnboardingService {
  async createBusiness(businessData) {
    // Step 1: Create business record
    const business = await Business.create({
      name: businessData.name,
      email: businessData.email,
      card_type: businessData.card_type,
      card_config: this.getDefaultCardConfig(businessData.card_type)
    });
    
    // Step 2: Upload and process logo
    const logoUrl = await this.uploadBusinessLogo(businessData.logo_file, business.id);
    await Business.updateOne(
      { _id: business.id },
      { logo_url: logoUrl }
    );
    
    // Step 3: Create Google Wallet class
    await this.googleWalletService.createBusinessClass(business.id);
    
    // Step 4: Generate business QR code for customer registration
    const registrationQR = this.generateRegistrationQR(business.id);
    
    // Step 5: Create owner staff access
    await StaffAccess.create({
      user_id: businessData.owner_user_id,
      business_id: business.id,
      role: 'owner',
      permissions: ['all']
    });
    
    return {
      business,
      registration_qr: registrationQR,
      dashboard_url: `${process.env.APP_URL}/dashboard/${business.id}`
    };
  }
  
  getDefaultCardConfig(cardType) {
    const defaults = {
      stamp: {
        stamps_required: 10,
        reward_name: "Free Item",
        auto_reset: true
      },
      points: {
        points_per_rm: 1,
        reward_tiers: [
          { points: 100, reward: "RM5 Discount" },
          { points: 200, reward: "RM12 Discount" },
          { points: 500, reward: "RM35 Discount" }
        ]
      },
      membership: {
        tiers: [
          { name: "Silver", requirements: { visits: 5 }, benefits: ["5% discount"] },
          { name: "Gold", requirements: { visits: 15 }, benefits: ["10% discount", "Priority service"] },
          { name: "VIP", requirements: { visits: 30 }, benefits: ["15% discount", "Free delivery"] }
        ]
      }
      // ... defaults for all 8 card types
    };
    
    return defaults[cardType] || {};
  }
}
```

### Advanced Features Implementation

**Multi-Location Business Support:**
```javascript
// For businesses with multiple locations
CREATE TABLE business_locations (
  id UUID PRIMARY KEY,
  business_id UUID NOT NULL REFERENCES businesses(id),
  location_name VARCHAR(255),
  address TEXT,
  latitude DECIMAL(10, 8),
  longitude DECIMAL(11, 8),
  qr_code_data TEXT, -- Location-specific QR
  staff_access JSONB -- Location-specific staff
);
```

**Cross-Business Analytics:**
```javascript
class AnalyticsService {
  async getPlatformMetrics() {
    return {
      total_businesses: await Business.count(),
      total_customers: await Customer.count(),
      total_transactions: await Transaction.count(),
      card_type_distribution: await this.getCardTypeDistribution(),
      wallet_adoption_rates: await this.getWalletAdoptionRates(),
      average_customer_lifetime_value: await this.getAverageCLV()
    };
  }
  
  async getBusinessBenchmarks(businessId) {
    const business = await Business.findById(businessId);
    const industryMetrics = await this.getIndustryMetrics(business.industry);
    
    return {
      your_adoption_rate: await this.getAdoptionRate(businessId),
      industry_average: industryMetrics.average_adoption_rate,
      your_transaction_frequency: await this.getTransactionFrequency(businessId),
      industry_benchmark: industryMetrics.average_frequency,
      recommendations: this.generateRecommendations(business, industryMetrics)
    };
  }
}
```

**Fraud Detection and Security:**
```javascript
class SecurityService {
  async validateTransaction(customerId, businessId, transactionType, amount, location) {
    const riskFactors = [];
    
    // Check for suspicious patterns
    if (await this.isHighFrequencyScanning(customerId, businessId)) {
      riskFactors.push('high_frequency');
    }
    
    if (await this.isUnusualLocation(customerId, businessId, location)) {
      riskFactors.push('unusual_location');
    }
    
    if (await this.isUnusualAmount(customerId, businessId, amount)) {
      riskFactors.push('unusual_amount');
    }
    
    const riskScore = this.calculateRiskScore(riskFactors);
    
    if (riskScore > 0.8) {
      // Block transaction and require additional verification
      return { approved: false, reason: 'high_risk', verification_required: true };
    } else if (riskScore > 0.5) {
      // Allow but flag for review
      return { approved: true, flagged: true, risk_score: riskScore };
    }
    
    return { approved: true, risk_score: riskScore };
  }
}
```

### Scalability Considerations

**Database Sharding Strategy:**
```javascript
// Shard by business_id for better performance
const getShardKey = (businessId) => {
  return businessId.substring(0, 2); // Use first 2 characters
};

// Connection routing
const getDBConnection = (shardKey) => {
  return connectionPool[`shard_${shardKey}`];
};
```

**Caching Strategy:**
```javascript
class CacheService {
  // Cache business configurations (rarely change)
  async getBusinessConfig(businessId) {
    const cacheKey = `business_config:${businessId}`;
    let config = await this.redis.get(cacheKey);
    
    if (!config) {
      config = await Business.findById(businessId);
      await this.redis.setex(cacheKey, 3600, JSON.stringify(config)); // 1 hour cache
    }
    
    return JSON.parse(config);
  }
  
  // Cache frequently accessed customer data
  async getCustomerLoyalty(customerId, businessId) {
    const cacheKey = `loyalty:${customerId}:${businessId}`;
    let loyalty = await this.redis.get(cacheKey);
    
    if (!loyalty) {
      loyalty = await CustomerLoyalty.findOne({ customer_id: customerId, business_id: businessId });
      await this.redis.setex(cacheKey, 300, JSON.stringify(loyalty)); // 5 minute cache
    }
    
    return JSON.parse(loyalty);
  }
}
```

---

**This comprehensive implementation guide covers every aspect of building a multi-business, multi-card-type wallet system that scales to support thousands of businesses while maintaining security, performance, and user experience across all 8 loyalty card types.**