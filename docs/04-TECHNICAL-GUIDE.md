# LoyalTea Technical Implementation Guide
*Practical development guide for 2-person team*

## 🎯 Technical Overview

**Architecture**: Multi-tenant SaaS with campaign-based loyalty system  
**Team Size**: 2 developers (focus on simplicity and maintainability)  
**Technology Stack**: Vue 3 + Node.js + PostgreSQL (familiar, proven stack)  
**Deployment**: Infyra servers with centralized wallet pass generation  
**Domains**: dashboard.loyaltea.my (admin) + app.loyaltea.my (customer/staff)

---

## 🏗️ System Architecture (Simplified)

### Core Components
```
┌─────────────────────────────────────────────────────────┐
│                   Cloudflare                           │
│               (CDN + SSL + Security)                   │
└─────────────────────┬───────────────────────────────────┘
                      │
        ┌─────────────┼─────────────┐
        │             │             │
    ┌───▼────┐   ┌───▼────┐   ┌───▼────┐
    │Dashboard│   │   App  │   │  API   │
    │(Vue 3) │   │(Vue 3) │   │(Node.js)│
    └────────┘   └────────┘   └───┬────┘
                                 │
              ┌──────────────────┼──────────────────┐
              │                  │                  │
       ┌──────▼──────┐   ┌──────▼──────┐   ┌──────▼──────┐
       │ PostgreSQL  │   │    Redis    │   │ File Storage│
       │  Database   │   │   (Cache)   │   │  (Images)   │
       └─────────────┘   └─────────────┘   └─────────────┘
```

### Multi-Tenant Database Structure
```sql
-- Core business/tenant table
CREATE TABLE tenants (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    slug VARCHAR(50) UNIQUE NOT NULL, -- kedaiA, mariospizza
    business_name VARCHAR(200) NOT NULL,
    owner_email VARCHAR(255) NOT NULL,
    subscription_tier VARCHAR(20) DEFAULT 'basic',
    branding JSONB NOT NULL DEFAULT '{}', -- logo_url, colors
    status VARCHAR(20) DEFAULT 'active',
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW()
);

-- Campaign system (multiple cards per business)
CREATE TABLE campaigns (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id UUID NOT NULL REFERENCES tenants(id) ON DELETE CASCADE,
    name VARCHAR(100) NOT NULL, -- "Coffee Stamp Card", "VIP Membership"
    card_type VARCHAR(20) NOT NULL, -- stamp, points, membership, etc.
    card_config JSONB NOT NULL, -- Type-specific settings
    branding JSONB DEFAULT '{}', -- Campaign-specific branding
    is_active BOOLEAN DEFAULT true,
    created_at TIMESTAMP DEFAULT NOW(),
    UNIQUE(tenant_id, name)
);

-- Customer loyalty tracking
CREATE TABLE customer_loyalties (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    customer_id UUID NOT NULL REFERENCES users(id),
    campaign_id UUID NOT NULL REFERENCES campaigns(id),
    tenant_id UUID NOT NULL REFERENCES tenants(id), -- For performance
    progress_data JSONB NOT NULL DEFAULT '{}', -- stamps, points, etc.
    wallet_pass_id VARCHAR(255), -- Apple/Google pass ID
    qr_signature TEXT NOT NULL, -- Signed QR data
    status VARCHAR(20) DEFAULT 'active',
    created_at TIMESTAMP DEFAULT NOW(),
    updated_at TIMESTAMP DEFAULT NOW(),
    UNIQUE(customer_id, campaign_id)
);

-- Simple user table
CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    phone VARCHAR(20) UNIQUE NOT NULL, -- Malaysian format
    email VARCHAR(255),
    full_name VARCHAR(200),
    date_of_birth DATE,
    role VARCHAR(20) DEFAULT 'customer', -- customer, staff, owner, superadmin
    tenant_id UUID REFERENCES tenants(id), -- For staff/owner roles
    created_at TIMESTAMP DEFAULT NOW()
);

-- Transaction log (simple)
CREATE TABLE transactions (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    customer_loyalty_id UUID NOT NULL REFERENCES customer_loyalties(id),
    staff_id UUID REFERENCES users(id),
    transaction_type VARCHAR(50) NOT NULL, -- add_stamp, add_points, redeem_reward
    amount DECIMAL(10,2), -- For points/cashback
    description TEXT,
    created_at TIMESTAMP DEFAULT NOW()
);

-- Essential indexes for performance
CREATE INDEX idx_tenants_slug ON tenants(slug) WHERE status = 'active';
CREATE INDEX idx_campaigns_tenant ON campaigns(tenant_id, is_active);
CREATE INDEX idx_customer_loyalties_customer ON customer_loyalties(customer_id);
CREATE INDEX idx_customer_loyalties_tenant ON customer_loyalties(tenant_id, status);
CREATE INDEX idx_users_phone ON users(phone);
CREATE INDEX idx_users_tenant ON users(tenant_id) WHERE role IN ('staff', 'owner');
```

---

## 🌐 Domain & Routing Implementation

### URL Structure (Path-based Multi-tenancy)
```javascript
// Tenant resolution middleware - simple and effective
const resolveTenant = async (req, res, next) => {
  let tenant = null;
  
  // Method 1: Path-based (app.loyaltea.my/kedaiA/customer)
  const pathSegments = req.path.split('/').filter(Boolean);
  if (pathSegments.length > 0 && pathSegments[0] !== 'login' && pathSegments[0] !== 'staff') {
    tenant = await getTenantBySlug(pathSegments[0]);
    if (tenant) {
      req.tenant = tenant;
      req.path = '/' + pathSegments.slice(1).join('/');
      return next();
    }
  }
  
  // Method 2: Query parameter (app.loyaltea.my/customer?tenant=kedaiA)
  if (req.query.tenant) {
    tenant = await getTenantBySlug(req.query.tenant);
    if (tenant) {
      req.tenant = tenant;
      return next();
    }
  }
  
  // No tenant needed for staff login and other global routes
  next();
};

// Simple tenant lookup with caching
const getTenantBySlug = async (slug) => {
  // Try cache first
  const cached = await redis.get(`tenant:${slug}`);
  if (cached) {
    return JSON.parse(cached);
  }
  
  // Database lookup
  const tenant = await db.query(
    'SELECT * FROM tenants WHERE slug = $1 AND status = $2',
    [slug, 'active']
  );
  
  if (tenant.rows[0]) {
    // Cache for 1 hour
    await redis.setex(`tenant:${slug}`, 3600, JSON.stringify(tenant.rows[0]));
    return tenant.rows[0];
  }
  
  return null;
};
```

### Frontend Routing (Vue 3)
```javascript
// Dashboard routes (dashboard.loyaltea.my)
const dashboardRoutes = [
  {
    path: '/login',
    component: () => import('@/views/auth/Login.vue')
  },
  {
    path: '/superadmin',
    component: () => import('@/views/superadmin/Layout.vue'),
    meta: { requiresAuth: true, role: 'superadmin' },
    children: [
      { path: 'businesses', component: () => import('@/views/superadmin/Businesses.vue') },
      { path: 'billing', component: () => import('@/views/superadmin/Billing.vue') }
    ]
  },
  {
    path: '/dashboard',
    component: () => import('@/views/owner/Layout.vue'),
    meta: { requiresAuth: true, role: 'owner' },
    children: [
      { path: '', component: () => import('@/views/owner/Dashboard.vue') },
      { path: 'campaigns', component: () => import('@/views/owner/Campaigns.vue') },
      { path: 'campaigns/create', component: () => import('@/views/owner/CampaignCreate.vue') },
      { path: 'campaigns/:id/edit', component: () => import('@/views/owner/CampaignEdit.vue') },
      { path: 'staff', component: () => import('@/views/owner/Staff.vue') }
    ]
  }
];

// App routes (app.loyaltea.my)
const appRoutes = [
  {
    // Customer login - universal entry point
    path: '/login',
    component: () => import('@/views/customer/Login.vue')
  },
  {
    // Customer dashboard - requires authentication
    path: '/dashboard',
    component: () => import('@/views/customer/Dashboard.vue'),
    meta: { requiresAuth: true, role: 'customer' }
  },
  {
    // Customer profile management
    path: '/profile',
    component: () => import('@/views/customer/Profile.vue'),
    meta: { requiresAuth: true, role: 'customer' }
  },
  {
    // Multi-business view for customers
    path: '/cards',
    component: () => import('@/views/customer/AllCards.vue'),
    meta: { requiresAuth: true, role: 'customer' }
  },
  {
    // Staff login
    path: '/staff/login',
    component: () => import('@/views/staff/Login.vue')
  },
  {
    // Staff scanner
    path: '/staff/scanner',
    component: () => import('@/views/staff/Scanner.vue'),
    meta: { requiresAuth: true, role: 'staff' }
  },
  {
    // Dynamic tenant routes (for joining/registration)
    path: '/:tenant(\\w+)',
    component: () => import('@/views/customer/TenantWrapper.vue'),
    children: [
      { path: 'join', component: () => import('@/views/customer/Landing.vue') },
      { path: 'register', component: () => import('@/views/customer/Register.vue') }
    ]
  }
];
```

---

## 🔐 Customer Web Portal Authentication

### OTP-Based Authentication System
```javascript
class CustomerAuthService {
  constructor() {
    this.otpStore = new Map(); // In production, use Redis
    this.sessionStore = new Map(); // In production, use Redis with TTL
  }

  // Step 1: Generate and send OTP
  async sendOTP(phoneNumber) {
    // Validate Malaysian phone format
    if (!this.isValidMalaysianPhone(phoneNumber)) {
      throw new Error('Invalid phone number format');
    }

    // Generate 6-digit OTP
    const otp = Math.floor(100000 + Math.random() * 900000).toString();
    const expiresAt = Date.now() + (5 * 60 * 1000); // 5 minutes

    // Store OTP temporarily
    this.otpStore.set(phoneNumber, {
      code: otp,
      expiresAt,
      attempts: 0
    });

    // Send via SMS provider
    await this.sendSMS(phoneNumber, `Your LoyalTea verification code is: ${otp}. Valid for 5 minutes.`);

    return { success: true, expiresIn: 300 };
  }

  // Step 2: Verify OTP and create session
  async verifyOTP(phoneNumber, otpCode) {
    const stored = this.otpStore.get(phoneNumber);

    if (!stored) {
      throw new Error('OTP not found or expired');
    }

    // Check expiration
    if (Date.now() > stored.expiresAt) {
      this.otpStore.delete(phoneNumber);
      throw new Error('OTP expired');
    }

    // Check attempts (prevent brute force)
    if (stored.attempts >= 3) {
      this.otpStore.delete(phoneNumber);
      throw new Error('Too many failed attempts');
    }

    // Verify OTP
    if (stored.code !== otpCode) {
      stored.attempts++;
      throw new Error('Invalid OTP');
    }

    // OTP valid - clean up and get/create user
    this.otpStore.delete(phoneNumber);
    const user = await this.getUserByPhone(phoneNumber);

    if (!user) {
      throw new Error('User not found. Please register first.');
    }

    // Create session
    const session = await this.createSession(user);

    return {
      success: true,
      user: {
        id: user.id,
        phone: user.phone,
        name: user.full_name,
        email: user.email
      },
      token: session.token,
      expiresAt: session.expiresAt
    };
  }

  // Create secure session
  async createSession(user) {
    const sessionId = this.generateSecureToken();
    const expiresAt = Date.now() + (30 * 24 * 60 * 60 * 1000); // 30 days

    const session = {
      sessionId,
      userId: user.id,
      phone: user.phone,
      role: 'customer',
      createdAt: Date.now(),
      expiresAt,
      lastActivity: Date.now()
    };

    // Store session in Redis
    await redis.setex(
      `session:${sessionId}`,
      30 * 24 * 60 * 60, // 30 days in seconds
      JSON.stringify(session)
    );

    // Also store user sessions list for multi-device management
    await redis.sadd(`user:${user.id}:sessions`, sessionId);

    return {
      token: sessionId,
      expiresAt
    };
  }

  // Validate session middleware
  async validateSession(req, res, next) {
    const token = req.headers.authorization?.replace('Bearer ', '') || req.cookies.session_token;

    if (!token) {
      return res.status(401).json({ error: 'No session token provided' });
    }

    // Get session from Redis
    const sessionData = await redis.get(`session:${token}`);

    if (!sessionData) {
      return res.status(401).json({ error: 'Invalid or expired session' });
    }

    const session = JSON.parse(sessionData);

    // Check if expired
    if (Date.now() > session.expiresAt) {
      await this.destroySession(token);
      return res.status(401).json({ error: 'Session expired' });
    }

    // Update last activity
    session.lastActivity = Date.now();
    await redis.setex(`session:${token}`, 30 * 24 * 60 * 60, JSON.stringify(session));

    // Attach user to request
    req.user = {
      id: session.userId,
      phone: session.phone,
      role: session.role
    };

    next();
  }

  // Logout - destroy session
  async logout(sessionToken) {
    const sessionData = await redis.get(`session:${sessionToken}`);

    if (sessionData) {
      const session = JSON.parse(sessionData);

      // Remove from user's sessions list
      await redis.srem(`user:${session.userId}:sessions`, sessionToken);

      // Delete session
      await redis.del(`session:${sessionToken}`);
    }

    return { success: true };
  }

  // Utility functions
  isValidMalaysianPhone(phone) {
    // Malaysian phone: 01X-XXXX-XXXX or 01XXXXXXXX
    const regex = /^(01[0-9])[0-9]{7,8}$/;
    return regex.test(phone.replace(/[-\s]/g, ''));
  }

  generateSecureToken() {
    const crypto = require('crypto');
    return crypto.randomBytes(32).toString('hex');
  }

  async getUserByPhone(phone) {
    const result = await db.query(
      'SELECT * FROM users WHERE phone = $1 AND role = $2',
      [phone, 'customer']
    );
    return result.rows[0] || null;
  }

  async sendSMS(phone, message) {
    // Integration with Malaysian SMS provider
    // Example: local SMS gateway, Twilio, etc.
    console.log(`SMS to ${phone}: ${message}`);
    return true;
  }
}
```

### Customer Registration Flow
```javascript
class CustomerRegistrationService {
  async registerCustomer(registrationData, campaignId) {
    const { phone, fullName, email, birthday, tenantId } = registrationData;

    // Check if customer already exists
    const existingUser = await db.query(
      'SELECT id FROM users WHERE phone = $1',
      [phone]
    );

    let customerId;

    if (existingUser.rows.length > 0) {
      // Customer exists, just add to this campaign
      customerId = existingUser.rows[0].id;
    } else {
      // Create new customer
      const newUser = await db.query(`
        INSERT INTO users (phone, full_name, email, date_of_birth, role)
        VALUES ($1, $2, $3, $4, 'customer')
        RETURNING id
      `, [phone, fullName, email, birthday]);

      customerId = newUser.rows[0].id;
    }

    // Check if already enrolled in this campaign
    const existingLoyalty = await db.query(
      'SELECT id FROM customer_loyalties WHERE customer_id = $1 AND campaign_id = $2',
      [customerId, campaignId]
    );

    if (existingLoyalty.rows.length > 0) {
      throw new Error('Already enrolled in this campaign');
    }

    // Create loyalty card for this campaign
    const qrData = this.generateCustomerQR(customerId, campaignId, tenantId);

    const loyalty = await db.query(`
      INSERT INTO customer_loyalties (
        customer_id, campaign_id, tenant_id,
        progress_data, qr_signature, status
      )
      VALUES ($1, $2, $3, $4, $5, 'active')
      RETURNING id
    `, [
      customerId,
      campaignId,
      tenantId,
      JSON.stringify({ stamps: 0, points: 0 }),
      qrData.signature
    ]);

    return {
      customerId,
      loyaltyId: loyalty.rows[0].id,
      qrCode: qrData.qrCode
    };
  }

  generateCustomerQR(customerId, campaignId, tenantId) {
    const timestamp = Date.now();
    const data = `${customerId}|${campaignId}|${tenantId}|${timestamp}`;

    const crypto = require('crypto');
    const hmac = crypto.createHmac('sha256', process.env.QR_SECRET_KEY);
    hmac.update(data);
    const signature = hmac.digest('hex');

    return {
      qrCode: `loyaltea://scan?d=${Buffer.from(data).toString('base64')}&s=${signature}`,
      signature
    };
  }
}
```

### Session Management in Frontend
```javascript
// Vue 3 Pinia Store for customer authentication
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'

export const useCustomerAuthStore = defineStore('customerAuth', () => {
  const user = ref(null)
  const token = ref(localStorage.getItem('session_token'))
  const isAuthenticated = computed(() => !!user.value && !!token.value)

  // Send OTP
  async function sendOTP(phoneNumber) {
    const response = await fetch('/api/customer/auth/send-otp', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ phone: phoneNumber })
    })

    if (!response.ok) {
      throw new Error('Failed to send OTP')
    }

    return await response.json()
  }

  // Verify OTP and login
  async function verifyOTP(phoneNumber, otpCode) {
    const response = await fetch('/api/customer/auth/verify-otp', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ phone: phoneNumber, otp: otpCode })
    })

    if (!response.ok) {
      throw new Error('Invalid OTP')
    }

    const data = await response.json()

    // Store session
    token.value = data.token
    user.value = data.user
    localStorage.setItem('session_token', data.token)
    localStorage.setItem('user_data', JSON.stringify(data.user))

    return data
  }

  // Restore session from localStorage
  function restoreSession() {
    const savedToken = localStorage.getItem('session_token')
    const savedUser = localStorage.getItem('user_data')

    if (savedToken && savedUser) {
      token.value = savedToken
      user.value = JSON.parse(savedUser)
      return true
    }

    return false
  }

  // Logout
  async function logout() {
    if (token.value) {
      await fetch('/api/customer/auth/logout', {
        method: 'POST',
        headers: { 'Authorization': `Bearer ${token.value}` }
      })
    }

    user.value = null
    token.value = null
    localStorage.removeItem('session_token')
    localStorage.removeItem('user_data')
  }

  // Get auth header
  function getAuthHeader() {
    return token.value ? { 'Authorization': `Bearer ${token.value}` } : {}
  }

  return {
    user,
    token,
    isAuthenticated,
    sendOTP,
    verifyOTP,
    restoreSession,
    logout,
    getAuthHeader
  }
})
```

### API Endpoints for Customer Portal
```javascript
// Express routes for customer authentication and portal
const express = require('express');
const router = express.Router();
const customerAuth = new CustomerAuthService();
const customerReg = new CustomerRegistrationService();

// Send OTP
router.post('/auth/send-otp', async (req, res) => {
  try {
    const { phone } = req.body;
    const result = await customerAuth.sendOTP(phone);
    res.json(result);
  } catch (error) {
    res.status(400).json({ error: error.message });
  }
});

// Verify OTP and login
router.post('/auth/verify-otp', async (req, res) => {
  try {
    const { phone, otp } = req.body;
    const result = await customerAuth.verifyOTP(phone, otp);
    res.json(result);
  } catch (error) {
    res.status(401).json({ error: error.message });
  }
});

// Logout
router.post('/auth/logout', async (req, res) => {
  try {
    const token = req.headers.authorization?.replace('Bearer ', '');
    await customerAuth.logout(token);
    res.json({ success: true });
  } catch (error) {
    res.status(400).json({ error: error.message });
  }
});

// Get customer dashboard data (requires auth)
router.get('/dashboard', customerAuth.validateSession, async (req, res) => {
  try {
    const customerId = req.user.id;

    // Get all loyalty cards for this customer
    const loyalties = await db.query(`
      SELECT
        cl.*,
        c.name as campaign_name,
        c.card_type,
        c.card_config,
        t.business_name,
        t.slug as tenant_slug,
        t.branding
      FROM customer_loyalties cl
      JOIN campaigns c ON cl.campaign_id = c.id
      JOIN tenants t ON cl.tenant_id = t.id
      WHERE cl.customer_id = $1 AND cl.status = 'active'
      ORDER BY cl.updated_at DESC
    `, [customerId]);

    res.json({ loyalties: loyalties.rows });
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});

// Get specific loyalty card details
router.get('/card/:loyaltyId', customerAuth.validateSession, async (req, res) => {
  try {
    const { loyaltyId } = req.params;
    const customerId = req.user.id;

    const loyalty = await db.query(`
      SELECT
        cl.*,
        c.name as campaign_name,
        c.card_type,
        c.card_config,
        t.business_name,
        t.branding
      FROM customer_loyalties cl
      JOIN campaigns c ON cl.campaign_id = c.id
      JOIN tenants t ON cl.tenant_id = t.id
      WHERE cl.id = $1 AND cl.customer_id = $2
    `, [loyaltyId, customerId]);

    if (loyalty.rows.length === 0) {
      return res.status(404).json({ error: 'Card not found' });
    }

    // Get transaction history
    const transactions = await db.query(`
      SELECT * FROM transactions
      WHERE customer_loyalty_id = $1
      ORDER BY created_at DESC
      LIMIT 50
    `, [loyaltyId]);

    res.json({
      loyalty: loyalty.rows[0],
      transactions: transactions.rows
    });
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});

// Customer registration
router.post('/register', async (req, res) => {
  try {
    const { phone, fullName, email, birthday, tenantSlug, campaignId } = req.body;

    // Get tenant
    const tenant = await db.query('SELECT id FROM tenants WHERE slug = $1', [tenantSlug]);
    if (tenant.rows.length === 0) {
      return res.status(404).json({ error: 'Business not found' });
    }

    const result = await customerReg.registerCustomer({
      phone,
      fullName,
      email,
      birthday,
      tenantId: tenant.rows[0].id
    }, campaignId);

    res.json(result);
  } catch (error) {
    res.status(400).json({ error: error.message });
  }
});

module.exports = router;
```

---

## 💳 Campaign System Implementation

### Campaign Management Service
```javascript
class CampaignService {
  async createCampaign(tenantId, campaignData) {
    // Check tier limits
    const tenant = await this.getTenant(tenantId);
    const campaignCount = await this.getCampaignCount(tenantId);
    
    const tierLimits = {
      basic: 1,
      business: 3,
      multi_branch: 999 // Unlimited campaigns, limited branches
    };
    
    if (campaignCount >= tierLimits[tenant.subscription_tier]) {
      throw new Error(`Campaign limit reached for ${tenant.subscription_tier} tier`);
    }
    
    // Create campaign
    const campaign = await db.query(`
      INSERT INTO campaigns (tenant_id, name, card_type, card_config, branding)
      VALUES ($1, $2, $3, $4, $5)
      RETURNING *
    `, [
      tenantId,
      campaignData.name,
      campaignData.card_type,
      this.getDefaultConfig(campaignData.card_type),
      campaignData.branding || {}
    ]);
    
    return campaign.rows[0];
  }
  
  getDefaultConfig(cardType) {
    const defaults = {
      stamp: {
        stamps_required: 10,
        reward_description: 'Free item',
        auto_reset: true
      },
      points: {
        points_per_rm: 1,
        reward_tiers: [
          { points: 100, reward: 'RM5 discount' },
          { points: 200, reward: 'RM12 discount' }
        ]
      },
      membership: {
        tiers: [
          { name: 'Silver', requirements: { visits: 5 }, benefits: ['5% discount'] },
          { name: 'Gold', requirements: { visits: 15 }, benefits: ['10% discount'] }
        ]
      },
      discount: {
        discount_percentage: 10,
        minimum_purchase: 0
      }
    };
    
    return defaults[cardType] || {};
  }
  
  async updateCampaignBranding(campaignId, branding) {
    const result = await db.query(`
      UPDATE campaigns 
      SET branding = $2, updated_at = NOW()
      WHERE id = $1
      RETURNING *
    `, [campaignId, branding]);
    
    // Invalidate cache
    await redis.del(`campaign:${campaignId}`);
    
    return result.rows[0];
  }
}
```

### Transaction Processing (Simplified)
```javascript
class TransactionService {
  async processStampTransaction(customerLoyaltyId, staffId, action = 'add_stamp') {
    const loyalty = await this.getCustomerLoyalty(customerLoyaltyId);
    const campaign = await this.getCampaign(loyalty.campaign_id);
    
    let newProgress = { ...loyalty.progress_data };
    let rewardEarned = false;
    
    if (action === 'add_stamp') {
      newProgress.stamps = (newProgress.stamps || 0) + 1;
      
      // Check if reward earned
      if (newProgress.stamps >= campaign.card_config.stamps_required) {
        rewardEarned = true;
        if (campaign.card_config.auto_reset) {
          newProgress.stamps = 0;
        }
        newProgress.rewards_earned = (newProgress.rewards_earned || 0) + 1;
      }
    }
    
    // Update database
    await db.query(`
      UPDATE customer_loyalties 
      SET progress_data = $2, updated_at = NOW()
      WHERE id = $1
    `, [customerLoyaltyId, newProgress]);
    
    // Log transaction
    await db.query(`
      INSERT INTO transactions (customer_loyalty_id, staff_id, transaction_type, description)
      VALUES ($1, $2, $3, $4)
    `, [
      customerLoyaltyId,
      staffId,
      action,
      rewardEarned ? 'Reward earned!' : 'Stamp added'
    ]);
    
    // Update wallet pass (if exists)
    if (loyalty.wallet_pass_id) {
      await this.updateWalletPass(loyalty.wallet_pass_id, newProgress, campaign);
    }
    
    return {
      newProgress,
      rewardEarned,
      message: rewardEarned ? 
        `Congratulations! You earned ${campaign.card_config.reward_description}!` :
        `${campaign.card_config.stamps_required - newProgress.stamps} more stamps until reward`
    };
  }
  
  async processPointsTransaction(customerLoyaltyId, staffId, purchaseAmount) {
    const loyalty = await this.getCustomerLoyalty(customerLoyaltyId);
    const campaign = await this.getCampaign(loyalty.campaign_id);
    
    const pointsToAdd = Math.floor(purchaseAmount * campaign.card_config.points_per_rm);
    
    let newProgress = { ...loyalty.progress_data };
    newProgress.points = (newProgress.points || 0) + pointsToAdd;
    newProgress.points_lifetime = (newProgress.points_lifetime || 0) + pointsToAdd;
    
    // Update database
    await db.query(`
      UPDATE customer_loyalties 
      SET progress_data = $2, updated_at = NOW()
      WHERE id = $1
    `, [customerLoyaltyId, newProgress]);
    
    // Log transaction
    await db.query(`
      INSERT INTO transactions (customer_loyalty_id, staff_id, transaction_type, amount, description)
      VALUES ($1, $2, $3, $4, $5)
    `, [
      customerLoyaltyId,
      staffId,
      'add_points',
      purchaseAmount,
      `Added ${pointsToAdd} points`
    ]);
    
    // Update wallet pass
    if (loyalty.wallet_pass_id) {
      await this.updateWalletPass(loyalty.wallet_pass_id, newProgress, campaign);
    }
    
    return {
      newProgress,
      pointsAdded: pointsToAdd,
      message: `Added ${pointsToAdd} points! Total: ${newProgress.points} points`
    };
  }
}
```

---

## 📱 Wallet Integration (Optional Feature)

### Overview - Wallet as Enhancement, Not Requirement

**Primary Access**: Web portal (app.loyaltea.my/login)
**Optional Enhancement**: Apple Wallet / Google Wallet download

The wallet integration provides an optional offline experience for customers who want quick access to their loyalty cards. However, **all core functionality works perfectly through the web portal alone**.

### Wallet Pass Generation (On-Demand)
```javascript
class WalletService {
  constructor() {
    this.appleCredentials = {
      teamId: process.env.INFYRA_APPLE_TEAM_ID,
      passTypeId: 'pass.com.infyra.loyaltea',
      signerCert: process.env.INFYRA_APPLE_CERT,
      signerKey: process.env.INFYRA_APPLE_KEY
    };
    
    this.googleCredentials = {
      issuerId: process.env.INFYRA_GOOGLE_ISSUER_ID,
      serviceAccount: JSON.parse(process.env.INFYRA_GOOGLE_SERVICE_ACCOUNT)
    };
  }
  
  // Customer requests wallet pass (optional download)
  async generateWalletPass(customerId, loyaltyId) {
    const customer = await this.getCustomer(customerId);
    const loyalty = await this.getCustomerLoyalty(loyaltyId);
    const campaign = await this.getCampaign(loyalty.campaign_id);
    const tenant = await this.getTenant(campaign.tenant_id);

    // Check if pass already exists
    if (loyalty.wallet_pass_id) {
      return {
        applePassUrl: `/api/wallet/apple/${loyalty.wallet_pass_id}.pkpass`,
        googlePassUrl: `/api/wallet/google/${loyalty.wallet_pass_id}`
      };
    }

    // Generate Apple Wallet pass
    const applePass = await this.generateApplePass({
      customer,
      campaign,
      tenant,
      loyalty
    });

    // Generate Google Wallet pass
    const googlePass = await this.generateGooglePass({
      customer,
      campaign,
      tenant,
      loyalty
    });

    // Store pass IDs (optional - only if customer downloads)
    await db.query(`
      UPDATE customer_loyalties
      SET wallet_pass_id = $2
      WHERE id = $1
    `, [loyalty.id, applePass.serialNumber]);

    return {
      applePassUrl: applePass.url,
      googlePassUrl: googlePass.url
    };
  }
  
  async generateApplePass({ customer, campaign, tenant, loyalty }) {
    const serialNumber = `${tenant.slug}_${customer.id}_${campaign.id}`;
    
    const passData = {
      formatVersion: 1,
      passTypeIdentifier: this.appleCredentials.passTypeId,
      serialNumber: serialNumber,
      teamIdentifier: this.appleCredentials.teamId,
      organizationName: 'LoyalTea',
      description: `${tenant.business_name} - ${campaign.name}`,
      logoText: tenant.business_name,
      
      // Tenant branding
      foregroundColor: tenant.branding.colors?.text || '#ffffff',
      backgroundColor: tenant.branding.colors?.primary || '#3b82f6',
      
      // Card fields based on type
      storeCard: this.buildCardFields(campaign, loyalty),
      
      // QR code for staff scanning
      barcode: {
        format: 'PKBarcodeFormatQR',
        message: this.generateQRData(customer.id, campaign.id, tenant.id),
        messageEncoding: 'iso-8859-1'
      }
    };
    
    // Generate .pkpass file using existing Infyra infrastructure
    const passUrl = await this.createApplePassFile(passData, tenant.branding.logo_url);
    
    return { serialNumber, url: passUrl };
  }
  
  buildCardFields(campaign, loyalty) {
    const progress = loyalty.progress_data;
    
    if (campaign.card_type === 'stamp') {
      return {
        primaryFields: [{
          key: 'stamps',
          label: 'Stamps',
          value: `${progress.stamps || 0} of ${campaign.card_config.stamps_required}`
        }],
        secondaryFields: [{
          key: 'reward',
          label: 'Reward',
          value: campaign.card_config.reward_description
        }]
      };
    }
    
    if (campaign.card_type === 'points') {
      return {
        primaryFields: [{
          key: 'points',
          label: 'Points',
          value: progress.points || 0
        }],
        secondaryFields: [{
          key: 'next_reward',
          label: 'Next Reward',
          value: this.getNextReward(progress.points, campaign.card_config.reward_tiers)
        }]
      };
    }
    
    // Default fallback
    return {
      primaryFields: [{
        key: 'status',
        label: 'Status',
        value: 'Active Member'
      }]
    };
  }
  
  generateQRData(customerId, campaignId, tenantId) {
    const timestamp = Math.floor(Date.now() / 1000);
    const data = `${customerId}|${campaignId}|${tenantId}|${timestamp}`;
    const signature = this.signData(data);
    
    return `loyaltea://scan?data=${encodeURIComponent(data)}&sig=${signature}`;
  }
  
  signData(data) {
    const crypto = require('crypto');
    const hmac = crypto.createHmac('sha256', process.env.QR_SECRET_KEY);
    hmac.update(data);
    return hmac.digest('hex');
  }
}
```

---

## 🔐 Security Implementation (Multi-Tenant)

### Authentication & Authorization
```javascript
class AuthService {
  async authenticate(phone, otp) {
    // Verify OTP
    const isValidOTP = await this.verifyOTP(phone, otp);
    if (!isValidOTP) {
      throw new Error('Invalid OTP');
    }
    
    // Get user
    const user = await db.query('SELECT * FROM users WHERE phone = $1', [phone]);
    if (user.rows.length === 0) {
      throw new Error('User not found');
    }
    
    const userData = user.rows[0];
    
    // Generate JWT token
    const token = jwt.sign(
      { 
        userId: userData.id,
        role: userData.role,
        tenantId: userData.tenant_id 
      },
      process.env.JWT_SECRET,
      { expiresIn: '24h' }
    );
    
    return { token, user: userData };
  }
  
  requireAuth(requiredRole = null) {
    return async (req, res, next) => {
      const token = req.headers.authorization?.replace('Bearer ', '');
      
      if (!token) {
        return res.status(401).json({ error: 'Authentication required' });
      }
      
      try {
        const decoded = jwt.verify(token, process.env.JWT_SECRET);
        req.user = decoded;
        
        // Check role if specified
        if (requiredRole && req.user.role !== requiredRole) {
          return res.status(403).json({ error: 'Insufficient permissions' });
        }
        
        next();
      } catch (error) {
        return res.status(401).json({ error: 'Invalid token' });
      }
    };
  }
  
  requireTenantAccess() {
    return (req, res, next) => {
      // Super admin can access all tenants
      if (req.user.role === 'superadmin') {
        return next();
      }
      
      // Check if user has access to requested tenant
      const requestedTenantId = req.tenant?.id;
      const userTenantId = req.user.tenantId;
      
      if (requestedTenantId && requestedTenantId !== userTenantId) {
        return res.status(403).json({ error: 'Access denied to this tenant' });
      }
      
      next();
    };
  }
}
```

### Data Isolation (Row Level Security)
```sql
-- Enable RLS on multi-tenant tables
ALTER TABLE campaigns ENABLE ROW LEVEL SECURITY;
ALTER TABLE customer_loyalties ENABLE ROW LEVEL SECURITY;
ALTER TABLE transactions ENABLE ROW LEVEL SECURITY;

-- Create policies for tenant isolation
CREATE POLICY tenant_isolation_campaigns ON campaigns
  FOR ALL
  USING (tenant_id = current_setting('app.current_tenant_id')::uuid);

CREATE POLICY tenant_isolation_loyalties ON customer_loyalties
  FOR ALL
  USING (tenant_id = current_setting('app.current_tenant_id')::uuid);

CREATE POLICY tenant_isolation_transactions ON transactions
  FOR ALL
  USING (
    customer_loyalty_id IN (
      SELECT id FROM customer_loyalties 
      WHERE tenant_id = current_setting('app.current_tenant_id')::uuid
    )
  );

-- Function to set tenant context
CREATE OR REPLACE FUNCTION set_tenant_context(tenant_uuid UUID)
RETURNS void AS $$
BEGIN
  PERFORM set_config('app.current_tenant_id', tenant_uuid::text, true);
END;
$$ LANGUAGE plpgsql;
```

---

## 🚀 Deployment Strategy (2-Person Team)

### Simple Docker Setup
```dockerfile
# Multi-stage build for production
FROM node:18-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production
COPY . .
RUN npm run build

FROM node:18-alpine AS production
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/node_modules ./node_modules
COPY --from=builder /app/package.json ./package.json

# Copy Infyra certificates
COPY --from=builder /app/certs ./certs

EXPOSE 3000
HEALTHCHECK --interval=30s --timeout=3s CMD curl -f http://localhost:3000/health || exit 1
CMD ["npm", "run", "start:prod"]
```

### Environment Configuration
```bash
# Core Database
DATABASE_URL=postgresql://loyaltea:password@db:5432/loyaltea
REDIS_URL=redis://redis:6379

# Domains
DASHBOARD_DOMAIN=dashboard.loyaltea.my
APP_DOMAIN=app.loyaltea.my

# Authentication
JWT_SECRET=your-super-secure-jwt-secret-here
QR_SECRET_KEY=your-qr-signing-key-here

# Infyra Wallet Integration (provided by Infyra)
INFYRA_APPLE_TEAM_ID=ABC123DEF4
INFYRA_APPLE_CERT=/app/certs/apple-cert.pem
INFYRA_APPLE_KEY=/app/certs/apple-key.pem
INFYRA_GOOGLE_ISSUER_ID=1234567890123456789
INFYRA_GOOGLE_SERVICE_ACCOUNT=/app/certs/google-service.json

# File Storage (simple local storage initially)
UPLOAD_PATH=/app/uploads
STATIC_URL=https://assets.loyaltea.my

# SMS OTP (Malaysian provider)
SMS_PROVIDER=local_sms_gateway
SMS_API_KEY=your-sms-api-key
```

### Production Deployment (docker-compose.yml)
```yaml
version: '3.8'

services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - DATABASE_URL=${DATABASE_URL}
      - REDIS_URL=redis://redis:6379
    volumes:
      - ./certs:/app/certs:ro
      - ./uploads:/app/uploads
    depends_on:
      - postgres
      - redis
    restart: unless-stopped
    
  postgres:
    image: postgres:15-alpine
    environment:
      - POSTGRES_DB=loyaltea
      - POSTGRES_USER=loyaltea
      - POSTGRES_PASSWORD=${DB_PASSWORD}
    volumes:
      - postgres_data:/var/lib/postgresql/data
    restart: unless-stopped
    
  redis:
    image: redis:7-alpine
    command: redis-server --appendonly yes
    volumes:
      - redis_data:/data
    restart: unless-stopped
    
  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
      - ./ssl:/etc/nginx/ssl
      - ./uploads:/var/www/uploads
    depends_on:
      - app
    restart: unless-stopped

volumes:
  postgres_data:
  redis_data:
```

---

## 📊 Performance Optimization (Essential Only)

### Database Query Optimization
```sql
-- Essential indexes only (avoid over-indexing)
CREATE INDEX CONCURRENTLY idx_campaigns_tenant_active 
ON campaigns(tenant_id) WHERE is_active = true;

CREATE INDEX CONCURRENTLY idx_customer_loyalties_customer_campaign
ON customer_loyalties(customer_id, campaign_id);

CREATE INDEX CONCURRENTLY idx_transactions_loyalty_recent
ON transactions(customer_loyalty_id, created_at DESC);

-- Simple query optimization
EXPLAIN ANALYZE SELECT c.*, t.business_name 
FROM campaigns c
JOIN tenants t ON c.tenant_id = t.id  
WHERE t.slug = 'kedaiA' AND c.is_active = true;
```

### Basic Caching Strategy
```javascript
class CacheService {
  constructor() {
    this.redis = new Redis(process.env.REDIS_URL);
    this.defaultTTL = 3600; // 1 hour
  }
  
  // Cache tenant data (rarely changes)
  async getTenant(slug) {
    const cacheKey = `tenant:${slug}`;
    const cached = await this.redis.get(cacheKey);
    
    if (cached) {
      return JSON.parse(cached);
    }
    
    const tenant = await db.query(
      'SELECT * FROM tenants WHERE slug = $1 AND status = $2',
      [slug, 'active']
    );
    
    if (tenant.rows[0]) {
      await this.redis.setex(cacheKey, this.defaultTTL, JSON.stringify(tenant.rows[0]));
      return tenant.rows[0];
    }
    
    return null;
  }
  
  // Cache campaign config (changes occasionally)
  async getCampaign(campaignId) {
    const cacheKey = `campaign:${campaignId}`;
    const cached = await this.redis.get(cacheKey);
    
    if (cached) {
      return JSON.parse(cached);
    }
    
    const campaign = await db.query('SELECT * FROM campaigns WHERE id = $1', [campaignId]);
    
    if (campaign.rows[0]) {
      await this.redis.setex(cacheKey, 1800, JSON.stringify(campaign.rows[0])); // 30 minutes
      return campaign.rows[0];
    }
    
    return null;
  }
  
  // Invalidate cache when data changes
  async invalidateTenant(slug) {
    await this.redis.del(`tenant:${slug}`);
  }
  
  async invalidateCampaign(campaignId) {
    await this.redis.del(`campaign:${campaignId}`);
  }
}
```

---

## 📱 Frontend Implementation (Vue 3)

### Tenant Branding System
```vue
<!-- TenantWrapper.vue - Applies tenant branding to customer interface -->
<template>
  <div class="tenant-app" :style="tenantStyles">
    <header class="tenant-header">
      <img v-if="tenant.branding.logo_url" :src="tenant.branding.logo_url" :alt="tenant.business_name" />
      <h1>{{ tenant.business_name }}</h1>
    </header>
    
    <main>
      <router-view :tenant="tenant" />
    </main>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import { useRoute } from 'vue-router'
import { useTenantStore } from '@/stores/tenant'

const route = useRoute()
const tenantStore = useTenantStore()
const tenant = ref(null)

const tenantStyles = computed(() => {
  if (!tenant.value?.branding) return {}
  
  return {
    '--primary-color': tenant.value.branding.colors?.primary || '#3b82f6',
    '--secondary-color': tenant.value.branding.colors?.secondary || '#1e40af',
    '--text-color': tenant.value.branding.colors?.text || '#ffffff'
  }
})

onMounted(async () => {
  const tenantSlug = route.params.tenant
  if (tenantSlug) {
    tenant.value = await tenantStore.loadTenant(tenantSlug)
  }
})
</script>

<style scoped>
.tenant-app {
  background-color: var(--primary-color);
  color: var(--text-color);
  min-height: 100vh;
}

.tenant-header {
  padding: 1rem;
  text-align: center;
  background: linear-gradient(135deg, var(--primary-color), var(--secondary-color));
}

.tenant-header img {
  width: 80px;
  height: 80px;
  border-radius: 50%;
  margin-bottom: 0.5rem;
}
</style>
```

### Campaign Management Interface
```vue
<!-- CampaignCreate.vue - Simple campaign creation -->
<template>
  <div class="campaign-create">
    <h2>Create New Campaign</h2>
    
    <!-- Tier check -->
    <div v-if="exceedsLimit" class="upgrade-notice">
      <h3>🚀 Upgrade Required</h3>
      <p>Your current plan ({{ currentTier }}) includes {{ tierLimits[currentTier] }} campaign(s).</p>
      <p>You currently have {{ existingCampaigns }} active campaign(s).</p>
      <button @click="showUpgradeModal = true" class="btn-primary">Upgrade Plan</button>
    </div>
    
    <!-- Campaign creation form -->
    <form v-else @submit.prevent="createCampaign" class="campaign-form">
      <div class="form-group">
        <label>Campaign Name</label>
        <input v-model="form.name" type="text" required placeholder="e.g., VIP Coffee Card" />
      </div>
      
      <div class="form-group">
        <label>Card Type</label>
        <div class="card-type-grid">
          <div v-for="type in availableCardTypes" :key="type.id" 
               :class="['card-type-option', { selected: form.card_type === type.id }]"
               @click="form.card_type = type.id">
            <div class="card-icon">{{ type.icon }}</div>
            <h4>{{ type.name }}</h4>
            <p>{{ type.description }}</p>
          </div>
        </div>
      </div>
      
      <!-- Card type specific configuration -->
      <div v-if="form.card_type === 'stamp'" class="config-section">
        <h3>Stamp Card Configuration</h3>
        <div class="form-group">
          <label>Stamps Required</label>
          <select v-model="form.config.stamps_required">
            <option value="5">5 stamps</option>
            <option value="8">8 stamps</option>
            <option value="10">10 stamps</option>
            <option value="12">12 stamps</option>
          </select>
        </div>
        
        <div class="form-group">
          <label>Reward</label>
          <input v-model="form.config.reward_description" 
                 type="text" required 
                 placeholder="e.g., Free premium coffee" />
        </div>
      </div>
      
      <button type="submit" class="btn-primary" :disabled="loading">
        {{ loading ? 'Creating...' : 'Create Campaign' }}
      </button>
    </form>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import { useCampaignStore } from '@/stores/campaign'
import { useAuthStore } from '@/stores/auth'

const campaignStore = useCampaignStore()
const authStore = useAuthStore()

const loading = ref(false)
const showUpgradeModal = ref(false)
const existingCampaigns = ref(0)
const form = ref({
  name: '',
  card_type: '',
  config: {}
})

const tierLimits = {
  basic: 1,
  business: 3,
  multi_branch: 999
}

const currentTier = computed(() => authStore.user.tenant.subscription_tier)
const exceedsLimit = computed(() => existingCampaigns.value >= tierLimits[currentTier.value])

const availableCardTypes = computed(() => {
  const basic = [
    { id: 'stamp', name: 'Stamp Card', icon: '☕', description: 'Collect stamps, get rewards' },
    { id: 'points', name: 'Points Card', icon: '🏆', description: 'Earn points from purchases' },
    { id: 'discount', name: 'Discount Card', icon: '🏷️', description: 'Fixed % discount for members' }
  ]
  
  const business = [
    ...basic,
    { id: 'membership', name: 'Membership Card', icon: '👑', description: 'VIP tiers with benefits' },
    { id: 'employee', name: 'Employee Badge', icon: '👨‍💼', description: 'Staff ID and perks' },
    { id: 'event', name: 'Event Card', icon: '🎫', description: 'Temporary campaigns' }
  ]
  
  return currentTier.value === 'basic' ? basic : business
})

const createCampaign = async () => {
  loading.value = true
  try {
    await campaignStore.createCampaign(form.value)
    // Redirect to campaign list or show success message
  } catch (error) {
    // Show error message
  } finally {
    loading.value = false
  }
}

onMounted(async () => {
  existingCampaigns.value = await campaignStore.getActiveCampaignCount()
})
</script>
```

---

**This technical guide provides practical implementation details for a 2-person team to build and deploy the LoyalTea platform efficiently while maintaining code quality and system reliability.**