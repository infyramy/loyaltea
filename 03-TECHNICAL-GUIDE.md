# LoyalTea Technical Implementation Guide
*Complete MVP Development Specifications*

## 🎯 Technical Overview

**Technology Stack**: jBoilerplate (Vue 3 + TypeScript + Shadcn UI) + Node.js backend
**Architecture**: Mobile-first web applications with wallet integration
**Approach**: MVP-focused development with manual processes
**Timeline**: 4-week core development + 2-week testing/polish

---

## 🏗️ System Architecture

### Core Components Architecture
```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Mobile Web    │    │  Desktop Admin  │    │  Wallet APIs    │
│ (Customer/Staff)│    │ (Owner/SuperAd) │    │ (Apple/Google)  │
└─────────┬───────┘    └─────────┬───────┘    └─────────┬───────┘
          │                      │                      │
          └──────────────────────┼──────────────────────┘
                                 │
                   ┌─────────────┴───────────┐
                   │    API Backend          │
                   │  (Node.js + Express)    │
                   └─────────────┬───────────┘
                                 │
                   ┌─────────────┴───────────┐
                   │    Database            │
                   │   (PostgreSQL)         │
                   └─────────────────────────┘
```

### Technology Stack Details
**Frontend Foundation**:
- **Base**: jBoilerplate (Vue 3 + Composition API + TypeScript)
- **UI Framework**: Shadcn UI components (existing in jBoilerplate)  
- **CSS**: Tailwind CSS (pre-configured in jBoilerplate)
- **Build Tool**: Vite (lightning-fast development)
- **State Management**: Pinia (already configured)

**Backend System**:
- **Runtime**: Node.js 18+ with TypeScript
- **Framework**: Express.js with TypeScript
- **Database**: PostgreSQL (production) / SQLite (development)
- **ORM**: Knex.js (already configured in jBoilerplate)
- **Authentication**: JWT tokens with role-based access

**External Integrations**:
- **Apple Wallet**: PassKit for .pkpass generation
- **Google Wallet**: Google Pay API for loyalty passes
- **QR Generation**: qrcode.js library
- **Push Notifications**: Firebase Cloud Messaging
- **SMS/WhatsApp**: Twilio API (Phase 2)

---

## 🗄️ Database Schema

### Core Tables Structure

#### Users Table
```sql
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email VARCHAR(255) UNIQUE NOT NULL,
    phone VARCHAR(20) UNIQUE,
    password_hash VARCHAR(255), -- NULL for customers (no login)
    role ENUM('super_admin', 'business_owner', 'staff', 'customer') NOT NULL,
    first_name VARCHAR(100),
    last_name VARCHAR(100),
    date_of_birth DATE, -- For customers only
    is_active BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

#### Businesses Table
```sql
CREATE TABLE businesses (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    owner_id UUID NOT NULL REFERENCES users(id),
    business_name VARCHAR(200) NOT NULL,
    business_type VARCHAR(100),
    description TEXT,
    logo_url VARCHAR(500),
    primary_color VARCHAR(7) DEFAULT '#000000',
    secondary_color VARCHAR(7) DEFAULT '#ffffff',
    phone VARCHAR(20),
    email VARCHAR(255),
    address TEXT,
    -- Manual billing fields
    subscription_status ENUM('trial', 'active', 'cancelled') DEFAULT 'trial',
    subscription_start DATE,
    last_payment_date DATE,
    next_payment_due DATE,
    -- Loyalty program configuration
    card_type ENUM('stamp', 'points', 'membership', 'discount', 'cashback', 'multipass', 'coupon', 'gift') NOT NULL,
    card_config JSONB, -- Card-specific configuration
    is_active BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

#### Customer Loyalty Table
```sql
CREATE TABLE customer_loyalties (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    customer_id UUID NOT NULL REFERENCES users(id),
    business_id UUID NOT NULL REFERENCES businesses(id),
    -- Universal fields for all card types
    qr_code VARCHAR(255) UNIQUE NOT NULL,
    wallet_pass_id VARCHAR(255), -- Apple/Google pass ID
    -- Card type specific data
    current_data JSONB, -- Current balance/status (points, stamps, etc.)
    lifetime_data JSONB, -- Historical totals
    join_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    last_activity TIMESTAMP,
    is_active BOOLEAN DEFAULT true,
    UNIQUE(customer_id, business_id)
);
```

#### Transactions Table
```sql
CREATE TABLE transactions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    customer_loyalty_id UUID NOT NULL REFERENCES customer_loyalties(id),
    staff_id UUID REFERENCES users(id),
    transaction_type VARCHAR(50) NOT NULL, -- 'earn', 'redeem', 'adjust'
    description TEXT,
    -- Transaction data
    amount DECIMAL(10,2), -- Purchase amount if applicable
    change_data JSONB, -- What changed (points added, stamps, etc.)
    previous_data JSONB, -- Previous state
    new_data JSONB, -- New state after transaction
    metadata JSONB, -- Additional transaction info
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

#### Staff Assignments Table
```sql
CREATE TABLE staff_assignments (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    staff_id UUID NOT NULL REFERENCES users(id),
    business_id UUID NOT NULL REFERENCES businesses(id),
    role VARCHAR(50) DEFAULT 'staff',
    permissions JSONB,
    is_active BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    UNIQUE(staff_id, business_id)
);
```

### Card Type Configuration Examples
**Stored in businesses.card_config JSONB field**:

```json
// STAMP CARD
{
  "type": "stamp",
  "stamps_required": 10,
  "reward_description": "Free coffee",
  "auto_reset": true
}

// POINTS CARD  
{
  "type": "points",
  "points_per_ringgit": 1.0,
  "reward_tiers": [
    {"points": 100, "reward": "RM5 discount"},
    {"points": 200, "reward": "RM12 discount"}
  ]
}

// DISCOUNT CARD
{
  "type": "discount",
  "discount_percentage": 15,
  "minimum_purchase": 0,
  "valid_until": "2024-12-31"
}
```

---

## 📱 Frontend Architecture

### Application Structure (Extending jBoilerplate)
```
src/
├── components/
│   ├── ui/ (from jBoilerplate - Shadcn components)
│   ├── loyalty/
│   │   ├── QRScanner.vue
│   │   ├── LoyaltyCard.vue
│   │   ├── StampDisplay.vue
│   │   ├── PointsDisplay.vue
│   │   └── WalletButton.vue
│   ├── business/
│   │   ├── CardTypeSelector.vue
│   │   ├── CustomerList.vue
│   │   └── BasicAnalytics.vue
│   └── mobile/
│       ├── MobileCard.vue
│       ├── MobileScanner.vue
│       └── MobileLayout.vue
├── pages/
│   ├── auth/ (from jBoilerplate)
│   ├── super-admin/
│   │   ├── businesses.vue
│   │   ├── create-business.vue
│   │   └── platform-overview.vue
│   ├── business-owner/
│   │   ├── setup-wizard.vue
│   │   ├── customers.vue
│   │   ├── staff.vue
│   │   └── analytics.vue
│   ├── staff/
│   │   ├── scanner.vue
│   │   └── history.vue
│   ├── customer/
│   │   ├── register.vue
│   │   ├── card-view.vue (Phase 2)
│   │   └── otp-login.vue (Phase 2)
│   └── public/
│       ├── qr/[code].vue
│       └── wallet-redirect.vue
├── composables/
│   ├── useAuth.ts (from jBoilerplate)
│   ├── useLoyalty.ts
│   ├── useQRCode.ts
│   ├── useWallet.ts
│   └── useNotifications.ts
├── services/
│   ├── api.ts (from jBoilerplate)
│   ├── loyaltyService.ts
│   ├── walletService.ts
│   ├── qrService.ts
│   └── notificationService.ts
└── types/
    ├── auth.ts (from jBoilerplate)
    ├── business.ts
    ├── loyalty.ts
    └── wallet.ts
```

### Interface Design Strategy

#### Desktop Admin Interfaces (Business Owner + Super Admin)
**Technology**: Full jBoilerplate admin components
- Use existing Shadcn UI component library
- Responsive design (works on tablet/mobile)
- Data tables for customer/transaction lists
- Form wizards for setup processes
- Dashboard widgets for key metrics

#### Mobile Web Interfaces (Customer + Staff)  
**Technology**: Custom Vue 3 components optimized for mobile
- **Customer**: Full-screen card design, wallet-like appearance
- **Staff**: Scanner-first interface, large buttons, minimal navigation
- **Design System**: Custom CSS optimized for thumb navigation
- **Performance**: <2 second load times, works on 3G connections

---

## 🔍 QR Code System

### Universal QR Code Architecture
**One QR per customer per business - works with all 8 card types**

#### QR Code Data Structure
```typescript
interface QRCodeData {
  customer_id: string;
  business_id: string;
  loyalty_id: string; // customer_loyalties.id
  security_hash: string; // Prevents tampering
  timestamp: number; // Prevents replay attacks
  version: string; // For future compatibility
}
```

#### QR Code Generation
```typescript
// services/qrService.ts
class QRService {
  generateCustomerQR(customerId: string, businessId: string): string {
    const qrData: QRCodeData = {
      customer_id: customerId,
      business_id: businessId,
      loyalty_id: loyalty.id,
      security_hash: this.generateHash(customerId, businessId),
      timestamp: Date.now(),
      version: '1.0'
    };
    
    const qrString = `loyaltea://scan?data=${encodeURIComponent(JSON.stringify(qrData))}`;
    return QRCode.toDataURL(qrString, {
      width: 300,
      margin: 2,
      color: { dark: '#000000', light: '#FFFFFF' }
    });
  }

  validateQR(qrData: string): QRScanResult {
    // Validate structure, security hash, timestamp
    // Return customer info and card type for processing
  }
}
```

### Staff Scanner Implementation
**Universal scanner that works with all card types**

```vue
<!-- components/loyalty/QRScanner.vue -->
<template>
  <div class="qr-scanner">
    <!-- Large scan button (70% of screen) -->
    <button @click="startScanning" class="scan-button">
      <Camera class="w-16 h-16" />
      <span class="text-xl font-bold">SCAN CUSTOMER</span>
    </button>
    
    <!-- Camera view when active -->
    <div v-if="scanning" class="camera-container">
      <video ref="videoElement" autoplay playsinline></video>
      <div class="scan-overlay">
        <div class="scan-box"></div>
      </div>
    </div>

    <!-- Customer info after successful scan -->
    <div v-if="scannedCustomer" class="customer-info">
      <h3>{{ scannedCustomer.name }}</h3>
      <div class="card-status">
        {{ formatCardStatus(scannedCustomer.cardType, scannedCustomer.currentData) }}
      </div>
      
      <!-- Dynamic action buttons based on card type -->
      <div class="action-buttons">
        <component 
          :is="getCardActions(scannedCustomer.cardType)"
          :customer="scannedCustomer"
          @transaction="processTransaction"
        />
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue'
import { BrowserQRCodeReader } from '@zxing/library'

// Dynamic card type components
const cardActionComponents = {
  stamp: StampCardActions,
  points: PointsCardActions, 
  membership: MembershipCardActions,
  discount: DiscountCardActions,
  cashback: CashbackCardActions,
  multipass: MultipassCardActions,
  coupon: CouponCardActions,
  gift: GiftCardActions
}

const getCardActions = (cardType: string) => {
  return cardActionComponents[cardType] || DefaultCardActions
}
</script>
```

---

## 💳 Wallet Integration

### Apple Wallet Integration
**Generate .pkpass files with business branding and real-time updates**

```typescript
// services/walletService.ts
import { PKPass } from 'passkit-generator'

class AppleWalletService {
  async generateLoyaltyPass(
    customer: Customer, 
    business: Business, 
    loyalty: CustomerLoyalty
  ): Promise<Buffer> {
    
    const pass = new PKPass({
      model: './wallet-templates/loyalty-card',
      certificates: {
        wwdr: process.env.APPLE_WWDR_CERT,
        signerCert: process.env.APPLE_SIGNER_CERT,
        signerKey: process.env.APPLE_SIGNER_KEY,
        signerKeyPassphrase: process.env.APPLE_KEY_PASSPHRASE
      }
    });

    // Set business branding
    pass.primaryFields.push({
      key: 'balance',
      label: this.getBalanceLabel(business.cardType),
      value: this.formatBalance(business.cardType, loyalty.currentData)
    });

    // Add progress information
    if (business.cardType === 'stamp' || business.cardType === 'points') {
      pass.auxiliaryFields.push({
        key: 'progress',
        label: 'Progress',
        value: this.getProgressText(business.cardConfig, loyalty.currentData)
      });
    }

    // Business info on back
    pass.backFields.push(
      { key: 'business', label: 'Business', value: business.businessName },
      { key: 'phone', label: 'Phone', value: business.phone },
      { key: 'address', label: 'Address', value: business.address }
    );

    // QR code for scanning
    pass.setBarcodes({
      format: 'PKBarcodeFormatQR',
      message: loyalty.qrCode,
      messageEncoding: 'iso-8859-1'
    });

    // Colors and branding
    pass.setColors({
      backgroundColor: business.primaryColor,
      foregroundColor: business.secondaryColor,
      labelColor: business.secondaryColor
    });

    return pass.generate();
  }

  async updatePass(passId: string, newData: any): Promise<void> {
    // Send push notification to update pass
    const pushNotification = {
      aps: { alert: 'Your loyalty card has been updated' }
    };
    
    await this.sendPushNotification(passId, pushNotification);
  }
}
```

### Google Wallet Integration  
**Create loyalty objects for Google Pay**

```typescript
class GoogleWalletService {
  async generateLoyaltyPass(
    customer: Customer,
    business: Business, 
    loyalty: CustomerLoyalty
  ) {
    const loyaltyObject = {
      id: `${business.id}_${customer.id}`,
      classId: `${process.env.GOOGLE_ISSUER_ID}.loyalty_${business.id}`,
      
      loyaltyPoints: {
        balance: {
          string: this.formatBalance(business.cardType, loyalty.currentData)
        }
      },
      
      accountId: customer.email,
      accountName: `${customer.firstName} ${customer.lastName}`,
      
      barcode: {
        type: 'QR_CODE',
        value: loyalty.qrCode
      },
      
      heroImage: {
        sourceUri: { uri: business.logoUrl }
      },

      hexBackgroundColor: business.primaryColor,
      
      textModulesData: [{
        header: 'Progress',
        body: this.getProgressText(business.cardConfig, loyalty.currentData)
      }]
    };

    return this.createJWT(loyaltyObject);
  }

  private createJWT(payload: any): string {
    return jwt.sign(payload, process.env.GOOGLE_WALLET_PRIVATE_KEY, {
      algorithm: 'RS256',
      audience: 'google',
      issuer: process.env.GOOGLE_WALLET_ISSUER_EMAIL
    });
  }
}
```

### Real-time Pass Updates
**Update wallet cards instantly when transactions occur**

```typescript
// When transaction is processed
async function updateWalletCards(loyaltyId: string, newData: any) {
  const loyalty = await CustomerLoyalty.findById(loyaltyId);
  
  if (loyalty.walletPassId) {
    // Determine wallet type from pass ID format
    if (loyalty.walletPassId.startsWith('apple_')) {
      await appleWalletService.updatePass(loyalty.walletPassId, newData);
    } else if (loyalty.walletPassId.startsWith('google_')) {
      await googleWalletService.updatePass(loyalty.walletPassId, newData);
    }
  }
}
```

### Developer Resources & Official Documentation

#### Apple Wallet (PassKit) Implementation

**Required Setup**:
1. **Apple Developer Account** ($99/year) - Required for certificates
2. **Pass Type ID** - Register your pass type identifier
3. **Certificates** - Download signing certificates and WWDR certificate  
4. **Team ID** - Your Apple Developer Team identifier

**Essential Documentation Links**:
- **PassKit Framework**: [developer.apple.com/documentation/passkit](https://developer.apple.com/documentation/passkit)
- **Wallet Developer Guide**: [developer.apple.com/library/archive/documentation/UserExperience/Conceptual/PassKit_PG/](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/PassKit_PG/)
- **Pass Design Guidelines**: [developer.apple.com/design/human-interface-guidelines/wallet](https://developer.apple.com/design/human-interface-guidelines/wallet)
- **Certificate Setup Guide**: [developer.apple.com/documentation/walletpasses/building_a_pass](https://developer.apple.com/documentation/walletpasses/building_a_pass)

**Key Implementation Steps**:
1. **Certificate Management**: Set up Pass Type ID and download certificates
2. **Pass Bundle Creation**: Create .pkpass files with manifest and signatures
3. **Web Service Integration**: Implement update/registration endpoints  
4. **Push Notification Setup**: Configure APNs for pass updates

**Testing Procedures**:
- Test passes on physical iOS devices (simulator has limitations)
- Validate pass bundle structure and signatures
- Test push notification updates
- Verify pass appearance across different iOS versions

#### Google Wallet Implementation  

**Required Setup**:
1. **Google Cloud Console** - Enable Google Wallet API
2. **Service Account** - Create service account with Wallet permissions
3. **Issuer Account** - Register as Google Wallet issuer
4. **OAuth 2.0** - Set up authentication for API calls

**Essential Documentation Links**:
- **Google Wallet API**: [developers.google.com/wallet](https://developers.google.com/wallet)
- **Generic Pass Guide**: [developers.google.com/wallet/generic](https://developers.google.com/wallet/generic)
- **REST API Reference**: [developers.google.com/wallet/generic/rest](https://developers.google.com/wallet/generic/rest)
- **Integration Checklist**: [developers.google.com/wallet/generic/android/prerequisites](https://developers.google.com/wallet/generic/android/prerequisites)

**Key Implementation Steps**:
1. **API Authentication**: Set up service account and JWT signing
2. **Class Definition**: Define loyalty card class structure
3. **Object Creation**: Create individual loyalty card objects
4. **Save to Wallet**: Generate "Add to Google Wallet" links
5. **Update Management**: Push updates via REST API

**Testing Procedures**:
- Test on Android devices with Google Wallet installed
- Verify JWT token generation and signing
- Test API endpoints with Postman or similar tools
- Validate pass rendering across Android versions

#### Logo & Image Integration Technical Details

**Apple Wallet Logo Requirements**:
```
logo.png (29x29 points) - Standard resolution
logo@2x.png (58x58 pixels) - Retina resolution  
logo@3x.png (87x87 pixels) - Super Retina resolution

background.png (180x220 points) - Standard background
background@2x.png (360x440 pixels) - Retina background
background@3x.png (540x660 pixels) - Super Retina background
```

**Google Wallet Image Requirements**:
```
Logo Image: 660x660 pixels (square, PNG/JPG)
Hero Image: 1032x336 pixels (landscape banner)
Card Background: Solid colors via hexColor property
```

**Image Processing Pipeline**:
```typescript
// Image optimization service
class ImageOptimizationService {
  async optimizeForWallet(
    originalImage: Buffer,
    targetType: 'apple' | 'google',
    imageType: 'logo' | 'background'
  ): Promise<ProcessedImages> {
    
    // Auto-resize for wallet requirements
    const sizes = this.getRequiredSizes(targetType, imageType);
    const optimizedImages = {};
    
    for (const [name, dimensions] of Object.entries(sizes)) {
      optimizedImages[name] = await sharp(originalImage)
        .resize(dimensions.width, dimensions.height, {
          fit: 'cover',
          background: { r: 255, g: 255, b: 255, alpha: 0 }
        })
        .png({ quality: 90, compressionLevel: 9 })
        .toBuffer();
    }
    
    return optimizedImages;
  }
}
```

#### Common Integration Issues & Solutions

**Apple Wallet Troubleshooting**:
- **Certificate Errors**: Ensure WWDR certificate is current and properly installed
- **Pass Validation**: Use Apple's pass validator tool before deployment
- **Push Notifications**: Verify APNs certificates and endpoint configuration
- **Signature Issues**: Check that all files in manifest are properly signed

**Google Wallet Troubleshooting**:
- **JWT Authentication**: Verify service account key and scopes
- **API Quotas**: Monitor API usage and request limit increases if needed
- **Object Updates**: Use PATCH requests for partial updates to avoid conflicts
- **Regional Availability**: Check Google Wallet availability in target regions

**Universal Issues**:
- **Image Optimization**: Ensure images meet size and format requirements
- **QR Code Generation**: Test QR codes with multiple scanning applications
- **Database Performance**: Index frequently queried fields (wallet_pass_id, customer_id)
- **Error Handling**: Implement proper retry logic for API failures

#### Development Environment Setup

**Required Dependencies**:
```bash
# Apple Wallet
npm install passkit-generator
npm install node-forge  # For certificate handling

# Google Wallet  
npm install google-auth-library
npm install googleapis

# Image Processing
npm install sharp
npm install qrcode

# Database
npm install prisma @prisma/client
```

**Environment Variables**:
```env
# Apple Wallet
APPLE_TEAM_ID=your_team_id
APPLE_PASS_TYPE_ID=pass.com.yourcompany.loyalty
APPLE_WWDR_CERT=path/to/wwdr.pem
APPLE_SIGNER_CERT=path/to/signer.pem
APPLE_SIGNER_KEY=path/to/signer.key
APPLE_KEY_PASSPHRASE=your_passphrase

# Google Wallet
GOOGLE_SERVICE_ACCOUNT_KEY=path/to/service-account.json
GOOGLE_ISSUER_ID=your_issuer_id
GOOGLE_APPLICATION_NAME=LoyalTea
```

---

## 🔄 Transaction Processing

### Universal Transaction Handler
**Single transaction processor that works with all 8 card types**

```typescript
// services/transactionService.ts
class TransactionService {
  async processTransaction(
    loyaltyId: string,
    transactionType: string,
    transactionData: any,
    staffId?: string
  ): Promise<TransactionResult> {
    
    const loyalty = await CustomerLoyalty.findById(loyaltyId);
    const business = await Business.findById(loyalty.businessId);
    
    // Get card type specific processor
    const processor = this.getCardProcessor(business.cardType);
    
    // Calculate new state
    const result = processor.process(
      loyalty.currentData,
      business.cardConfig,
      transactionType,
      transactionData
    );
    
    // Update database
    await this.updateLoyalty(loyaltyId, result.newData);
    
    // Log transaction
    await this.logTransaction({
      customer_loyalty_id: loyaltyId,
      staff_id: staffId,
      transaction_type: transactionType,
      change_data: result.changes,
      previous_data: loyalty.currentData,
      new_data: result.newData
    });
    
    // Update wallet pass
    await this.updateWalletCards(loyaltyId, result.newData);
    
    return result;
  }

  private getCardProcessor(cardType: string): CardProcessor {
    switch (cardType) {
      case 'stamp': return new StampCardProcessor();
      case 'points': return new PointsCardProcessor();
      case 'membership': return new MembershipCardProcessor();
      case 'discount': return new DiscountCardProcessor();
      case 'cashback': return new CashbackCardProcessor();
      case 'multipass': return new MultipassCardProcessor();
      case 'coupon': return new CouponCardProcessor();
      case 'gift': return new GiftCardProcessor();
      default: throw new Error(`Unknown card type: ${cardType}`);
    }
  }
}
```

### Card Type Processors
**Specific logic for each loyalty card type**

```typescript
// processors/StampCardProcessor.ts
class StampCardProcessor implements CardProcessor {
  process(currentData: any, config: any, type: string, data: any): ProcessorResult {
    switch (type) {
      case 'add_stamp':
        return {
          newData: {
            ...currentData,
            stamps_current: (currentData.stamps_current || 0) + 1,
            stamps_lifetime: (currentData.stamps_lifetime || 0) + 1
          },
          changes: { stamps_added: 1 },
          rewardEligible: (currentData.stamps_current || 0) + 1 >= config.stamps_required
        };
        
      case 'redeem_reward':
        return {
          newData: {
            ...currentData,
            stamps_current: 0, // Reset after redemption
            rewards_redeemed: (currentData.rewards_redeemed || 0) + 1
          },
          changes: { reward_redeemed: true, stamps_reset: true }
        };
    }
  }
}

// processors/PointsCardProcessor.ts  
class PointsCardProcessor implements CardProcessor {
  process(currentData: any, config: any, type: string, data: any): ProcessorResult {
    switch (type) {
      case 'add_points':
        const pointsToAdd = data.amount * config.points_per_ringgit;
        return {
          newData: {
            ...currentData,
            points_current: (currentData.points_current || 0) + pointsToAdd,
            points_lifetime: (currentData.points_lifetime || 0) + pointsToAdd
          },
          changes: { points_added: pointsToAdd, purchase_amount: data.amount }
        };
        
      case 'redeem_points':
        return {
          newData: {
            ...currentData,
            points_current: currentData.points_current - data.points,
            points_redeemed: (currentData.points_redeemed || 0) + data.points
          },
          changes: { points_redeemed: data.points, reward: data.reward }
        };
    }
  }
}
```

---

## 🔐 Authentication & Security

### Role-Based Access Control
**Simple but secure permission system**

```typescript
// middleware/auth.ts
interface UserPermissions {
  role: 'super_admin' | 'business_owner' | 'staff' | 'customer';
  businessId?: string; // For business_owner and staff
  permissions: string[];
}

const rolePermissions = {
  super_admin: ['*'], // Full access
  business_owner: [
    'business:read', 'business:update',
    'customers:read', 'customers:update',
    'staff:create', 'staff:read', 'staff:update', 'staff:delete',
    'transactions:read', 'analytics:read'
  ],
  staff: [
    'customers:read', 'customers:scan',
    'transactions:create', 'transactions:read'
  ],
  customer: [
    'profile:read', 'profile:update',
    'loyalty:read' // Only their own
  ]
};

function authorizeAccess(userRole: string, businessId: string, resource: string, action: string) {
  const permissions = rolePermissions[userRole];
  return permissions.includes('*') || 
         permissions.includes(`${resource}:${action}`) ||
         permissions.includes(`${resource}:*`);
}
```

### Security Measures
**Essential security without over-engineering**

```typescript
// Security configuration
const securityConfig = {
  // JWT tokens
  jwtSecret: process.env.JWT_SECRET,
  jwtExpiration: '24h',
  
  // Rate limiting
  rateLimits: {
    api: { windowMs: 15 * 60 * 1000, max: 100 }, // 100 requests per 15 minutes
    scanner: { windowMs: 60 * 1000, max: 30 },   // 30 scans per minute
  },
  
  // QR code security
  qrCodeExpiration: 5 * 60 * 1000, // 5 minutes
  maxQRCodeAge: 10 * 60 * 1000,    // 10 minutes
  
  // Password requirements (for admin users only)
  passwordMinLength: 8,
  passwordRequireNumbers: true,
  passwordRequireSymbols: false
};
```

---

## 📊 Analytics & Reporting

### Basic Analytics (MVP Scope)
**Simple but useful metrics for business owners**

```typescript
// services/analyticsService.ts
class AnalyticsService {
  async getBusinessMetrics(businessId: string, period: string): Promise<BusinessMetrics> {
    const timeframe = this.getTimeframe(period);
    
    const [
      customerCount,
      transactionCount,
      activeCustomers,
      topCustomers
    ] = await Promise.all([
      this.getCustomerCount(businessId, timeframe),
      this.getTransactionCount(businessId, timeframe),
      this.getActiveCustomers(businessId, timeframe),
      this.getTopCustomers(businessId, timeframe, 5)
    ]);

    return {
      period,
      totalCustomers: customerCount.total,
      newCustomers: customerCount.new,
      activeCustomers: activeCustomers.count,
      totalTransactions: transactionCount.total,
      averageTransactionsPerCustomer: Math.round(transactionCount.total / customerCount.total),
      topCustomers,
      growthRate: this.calculateGrowthRate(customerCount, period)
    };
  }

  private async getCustomerCount(businessId: string, timeframe: any) {
    // Simple customer counting queries
    return {
      total: await this.db('customer_loyalties')
        .where('business_id', businessId)
        .count('* as count')
        .first(),
      new: await this.db('customer_loyalties')
        .where('business_id', businessId)
        .where('created_at', '>=', timeframe.start)
        .count('* as count')
        .first()
    };
  }
}
```

---

## 🚀 Development Process

### Phase 1: Core Development (4 weeks)

#### Week 1-2: Foundation & QR System
**Day 1-3: jBoilerplate Setup**
- Clone and configure jBoilerplate for LoyalTea
- Set up loyalty-specific database tables
- Configure development environment with Docker

**Day 4-7: Universal QR System**
- QR code generation service
- QR code validation and security
- Basic customer registration flow

**Day 8-14: Core Loyalty Logic**
- Transaction processing system
- Card type processors (all 8 types)
- Basic mobile interfaces

#### Week 3-4: Wallet Integration
**Day 15-21: Apple Wallet**
- PassKit certificate setup
- .pkpass generation for all card types
- Real-time pass updates via push notifications

**Day 22-28: Google Wallet**  
- Google Pay API integration
- JWT-based pass creation
- Cross-platform testing

### Phase 2: Polish & Testing (2 weeks)

#### Week 5: Interface Completion
- Staff mobile scanner optimization
- Business owner admin panel completion  
- Customer mobile web card designs
- Super admin platform tools

#### Week 6: Beta Preparation
- End-to-end testing all card types
- Performance optimization (<2 second load times)
- Security validation and penetration testing
- First business onboarding preparation

---

## 🔧 Deployment & Operations

### Development Environment
```yaml
# docker-compose.dev.yml
version: '3.8'
services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=development
      - DATABASE_URL=postgresql://loyaltea:password@postgres:5432/loyaltea_dev
    volumes:
      - .:/app
      - /app/node_modules

  postgres:
    image: postgres:14-alpine
    environment:
      POSTGRES_USER: loyaltea
      POSTGRES_PASSWORD: password
      POSTGRES_DB: loyaltea_dev
    volumes:
      - postgres_data:/var/lib/postgresql/data
    ports:
      - "5432:5432"

volumes:
  postgres_data:
```

### Production Deployment
**Simple, reliable deployment strategy**

```dockerfile
# Dockerfile
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
RUN npm run build

FROM node:18-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY package.json ./
EXPOSE 3000
CMD ["npm", "run", "start:prod"]
```

### Essential Monitoring
**Key metrics to track**:
- API response times (<500ms target)
- QR scan success rates (>95% target)
- Wallet update success (>98% target)
- Database query performance
- User error rates
- System uptime (>99.5% target)

---

## 📋 Testing Strategy

### Automated Testing
```typescript
// tests/loyalty/transaction.test.ts
describe('Loyalty Transaction Processing', () => {
  test('Stamp card: Add stamp increases count', async () => {
    const loyalty = await createTestLoyalty('stamp', { stamps_current: 5 });
    const result = await transactionService.processTransaction(
      loyalty.id, 'add_stamp', {}
    );
    expect(result.newData.stamps_current).toBe(6);
  });

  test('Points card: Purchase adds correct points', async () => {
    const loyalty = await createTestLoyalty('points', { points_current: 100 });
    const result = await transactionService.processTransaction(
      loyalty.id, 'add_points', { amount: 25 }
    );
    expect(result.newData.points_current).toBe(125); // 1 point per RM1
  });
});
```

### Manual Testing Checklist
**Pre-launch verification**:
- [ ] All 8 card types work correctly
- [ ] QR scanning works on iOS and Android devices  
- [ ] Apple Wallet integration generates and updates passes
- [ ] Google Wallet integration works on Android devices
- [ ] Staff scanner interface is intuitive and fast
- [ ] Business owner can set up program in <5 minutes
- [ ] Customer registration completes in <30 seconds
- [ ] System handles offline/poor connectivity gracefully

---

## 🔒 Security & Compliance Framework

### Data Protection
**PDPA (Malaysia) Compliance**:
- Customer consent for data collection
- Clear privacy policy and terms of service
- Data retention policies (delete inactive customers after 2 years)
- Right to data export and deletion
- Secure data storage and transmission (HTTPS, encrypted database)

### Payment Security
**Manual Billing Security**:
- No payment card data stored (manual bank transfers)
- Invoice generation with unique reference numbers
- Payment confirmation tracking
- Secure communication via WhatsApp Business API

### System Security
**Essential Security Measures**:
- API rate limiting (prevent abuse)
- JWT token expiration and rotation
- QR code time-based validation (prevent replay attacks)
- Input validation and sanitization
- SQL injection prevention (parameterized queries)
- XSS protection (Content Security Policy headers)

---

## 🎯 Success Metrics

### Technical KPIs
- **System Uptime**: >99.5% (maximum 3.6 hours downtime/month)
- **API Response Time**: <500ms (95th percentile)
- **Mobile Page Load**: <2 seconds on 3G connection
- **QR Scan Success**: >95% (scans work on first attempt)
- **Wallet Update Speed**: <3 seconds (scan to wallet refresh)

### User Experience KPIs  
- **Customer Registration**: <30 seconds (QR scan to wallet card)
- **Staff Training Time**: <30 minutes (from login to competent)
- **Business Setup**: <5 minutes (card type selection to go-live)
- **Transaction Processing**: <10 seconds (scan to completion)

---

## 💻 Development Environment Setup

### Quick Start Commands
```bash
# Clone jBoilerplate as foundation
git clone <jboilerplate-repo> loyaltea
cd loyaltea

# Install dependencies
npm install

# Set up environment
cp .env.example .env
# Edit .env with loyalty-specific configuration

# Set up database
npm run migrate:latest
npm run seed:run

# Start development
npm run dev
```

### Required Environment Variables
```env
# Database
DATABASE_URL=postgresql://loyaltea:password@localhost:5432/loyaltea_dev

# JWT
JWT_SECRET=your-super-secret-jwt-key

# Apple Wallet
APPLE_PASS_TYPE_ID=pass.com.loyaltea.loyalty
APPLE_TEAM_ID=YOUR_APPLE_TEAM_ID
APPLE_WWDR_CERT=path/to/wwdr.pem
APPLE_SIGNER_CERT=path/to/signer.pem
APPLE_SIGNER_KEY=path/to/signer.key
APPLE_KEY_PASSPHRASE=your_passphrase

# Google Wallet  
GOOGLE_WALLET_ISSUER_ID=your_issuer_id
GOOGLE_WALLET_PRIVATE_KEY=-----BEGIN PRIVATE KEY-----...
GOOGLE_WALLET_ISSUER_EMAIL=service-account@project.iam.gserviceaccount.com

# Push Notifications
FIREBASE_PROJECT_ID=loyaltea-app
FIREBASE_PRIVATE_KEY=-----BEGIN PRIVATE KEY-----...
FIREBASE_CLIENT_EMAIL=firebase-adminsdk-xxx@loyaltea-app.iam.gserviceaccount.com
```

---

**This technical guide provides complete specifications for implementing LoyalTea MVP using jBoilerplate foundation with focus on wallet integration, universal QR system, and mobile-first experience.**