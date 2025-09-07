# LoyalTea Security & Compliance
*Essential security measures for 2-person team*

## 🎯 Overview

Essential security measures and Malaysian compliance requirements for LoyalTea multi-tenant loyalty platform, focusing on practical implementation for a small development team.

---

## 🔐 Multi-Tenant Security

### Data Isolation (Essential)
```sql
-- Row-level security for tenant isolation
ALTER TABLE campaigns ENABLE ROW LEVEL SECURITY;
ALTER TABLE customer_loyalties ENABLE ROW LEVEL SECURITY;

-- Policies ensure users only see their tenant's data
CREATE POLICY tenant_isolation ON campaigns
  FOR ALL USING (tenant_id = current_setting('app.current_tenant_id')::uuid);

CREATE POLICY customer_isolation ON customer_loyalties
  FOR ALL USING (tenant_id = current_setting('app.current_tenant_id')::uuid);
```

### Authentication & Authorization
```javascript
// Simple but effective role-based access control
const roles = {
  superadmin: ['*'], // Full access
  owner: ['read:own_tenant', 'write:own_tenant'],
  staff: ['read:customer_loyalty', 'write:transactions'],
  customer: ['read:own_data']
};

// JWT-based authentication
const authenticateUser = (req, res, next) => {
  const token = req.headers.authorization?.replace('Bearer ', '');
  if (!token) return res.status(401).json({ error: 'No token' });
  
  try {
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user = decoded;
    next();
  } catch (error) {
    res.status(401).json({ error: 'Invalid token' });
  }
};
```

### Password & OTP Security
```javascript
// Simple OTP system for Malaysian phone numbers
class OTPService {
  async sendOTP(phone) {
    const otp = Math.floor(100000 + Math.random() * 900000);
    
    // Store OTP with 5-minute expiry
    await redis.setex(`otp:${phone}`, 300, otp);
    
    // Send SMS (Malaysian gateway)
    await this.sendSMS(phone, `Your LoyalTea verification code: ${otp}`);
    
    return true;
  }
  
  async verifyOTP(phone, otp) {
    const storedOTP = await redis.get(`otp:${phone}`);
    if (storedOTP === otp) {
      await redis.del(`otp:${phone}`);
      return true;
    }
    return false;
  }
}
```

---

## 🛡️ Data Protection (PDPA Malaysia)

### Personal Data Collection
```javascript
// Minimal data collection approach
const customerDataFields = {
  required: ['full_name', 'phone'], // Essential for loyalty program
  optional: ['email', 'date_of_birth'], // For enhanced features
  prohibited: ['ic_number', 'address', 'income'] // Not needed, avoid collecting
};

// Clear consent mechanism
const consentForm = {
  loyalty_program: true, // Required for service
  marketing_communications: false, // Opt-in only
  data_analytics: false // Opt-in only
};
```

### Customer Data Rights
```javascript
class DataRightsService {
  // Right to access personal data
  async exportCustomerData(customerId) {
    const customer = await User.findById(customerId);
    const loyalties = await CustomerLoyalty.findByCustomer(customerId);
    const transactions = await Transaction.findByCustomer(customerId);
    
    return {
      personal_data: customer,
      loyalty_cards: loyalties,
      transaction_history: transactions,
      export_date: new Date(),
      retention_period: '24 months from last activity'
    };
  }
  
  // Right to erasure (delete account)
  async deleteCustomerAccount(customerId) {
    // Anonymize instead of hard delete (for business records)
    await User.update(customerId, {
      full_name: 'Deleted User',
      phone: null,
      email: null,
      date_of_birth: null,
      status: 'deleted'
    });
    
    // Keep loyalty history for business analytics (anonymized)
    await CustomerLoyalty.anonymize(customerId);
  }
}
```

### Privacy Policy (Simple & Clear)
```markdown
# LoyalTea Privacy Policy (Malaysian Customers)

## What we collect:
- Your name and phone number (to identify you)
- Email address (to send you updates, optional)
- Birthday (for special offers, optional)
- Your loyalty card activity (stamps, points, rewards)

## How we use it:
- To run your loyalty programs
- To send you rewards notifications
- To help businesses serve you better
- To improve our service

## Your rights:
- See your data: Ask us for a copy anytime
- Fix wrong data: Tell us if something is incorrect
- Delete your data: Ask us to remove your account
- Stop marketing: Unsubscribe from emails/SMS

## Contact us:
Email: privacy@loyaltea.my
Phone: +60 12-345-6789 (WhatsApp)

We follow Malaysian PDPA laws and keep your data secure.
```

---

## 🔒 Technical Security Measures

### Basic Security Implementation
```javascript
// Essential security middleware
const securityMiddleware = {
  // Rate limiting (prevent abuse)
  rateLimit: rateLimit({
    windowMs: 15 * 60 * 1000, // 15 minutes
    max: 100, // Limit each IP to 100 requests per windowMs
    message: 'Too many requests'
  }),
  
  // Basic input validation
  validateInput: (req, res, next) => {
    const { body } = req;
    
    // Sanitize strings
    for (const key in body) {
      if (typeof body[key] === 'string') {
        body[key] = body[key].trim().slice(0, 1000); // Max length
      }
    }
    
    next();
  },
  
  // CORS for multi-domain setup
  cors: cors({
    origin: [
      'https://dashboard.loyaltea.my',
      'https://app.loyaltea.my'
    ],
    credentials: true
  })
};
```

### Database Security
```sql
-- Basic database security
-- Create limited user accounts (not root access)
CREATE USER loyaltea_app WITH PASSWORD 'secure_password';
GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA public TO loyaltea_app;

-- Sensitive data encryption in application layer
CREATE TABLE users (
  id UUID PRIMARY KEY,
  phone VARCHAR(20) NOT NULL, -- Encrypted in app
  email VARCHAR(255), -- Encrypted in app  
  full_name VARCHAR(200), -- Encrypted in app
  created_at TIMESTAMP DEFAULT NOW()
);

-- Regular backups
-- pg_dump loyaltea > backup_$(date +%Y%m%d).sql
-- Store backups securely off-site
```

### File Upload Security
```javascript
// Secure file upload for business logos
const multer = require('multer');
const path = require('path');

const upload = multer({
  dest: '/app/uploads/',
  limits: {
    fileSize: 2 * 1024 * 1024 // 2MB limit
  },
  fileFilter: (req, file, cb) => {
    // Only allow images
    const allowedTypes = ['image/jpeg', 'image/png', 'image/gif'];
    if (allowedTypes.includes(file.mimetype)) {
      cb(null, true);
    } else {
      cb(new Error('Invalid file type'), false);
    }
  }
});

// Virus scanning (optional, for when budget allows)
const scanFile = async (filePath) => {
  // Use simple file type validation initially
  // Upgrade to proper antivirus scanning later
  return true;
};
```

---

## 🏢 Business Compliance (Malaysian Law)

### Business Registration Requirements
```markdown
## LoyalTea Sdn Bhd Registration Checklist:

### Company Setup:
- [ ] Register with SSM (Companies Commission of Malaysia)
- [ ] Obtain business license from local council
- [ ] Register for tax (income tax, SST if applicable)
- [ ] Open Malaysian business bank account

### Data Protection:
- [ ] Register as data user under PDPA (if >RM3M revenue)
- [ ] Implement data protection policies
- [ ] Train staff on customer data handling
- [ ] Maintain data processing records

### Consumer Protection:
- [ ] Clear pricing display (no hidden fees)
- [ ] Proper terms and conditions
- [ ] Customer complaint handling process
- [ ] Refund policy for subscription services
```

### Customer Business Support
```javascript
// Help customers with their compliance
class ComplianceService {
  getBusinessGuidance(businessType) {
    const guides = {
      fnb: {
        licenses: ['Business License', 'Food Handler Certificate', 'Halal Certification (if applicable)'],
        tax: 'Register for income tax, SST if revenue >RM500k',
        health: 'Follow MOH food safety guidelines'
      },
      retail: {
        licenses: ['Business License', 'Import License (if importing goods)'],
        tax: 'Register for income tax, SST if revenue >RM500k', 
        consumer: 'Follow Consumer Protection Act requirements'
      },
      services: {
        licenses: ['Business License', 'Professional License (if applicable)'],
        tax: 'Register for income tax, SST if revenue >RM500k',
        data: 'PDPA compliance if collecting customer data'
      }
    };
    
    return guides[businessType] || guides.services;
  }
}
```

---

## 🚨 Incident Response (Simplified)

### Security Incident Handling
```javascript
class IncidentResponse {
  async handleSecurityIncident(incident) {
    const severity = this.assessSeverity(incident);
    
    if (severity === 'HIGH') {
      // Customer data potentially compromised
      await this.notifyAffectedCustomers(incident);
      await this.reportToAuthorities(incident);
      await this.implementImmediateFix(incident);
    } else if (severity === 'MEDIUM') {
      // System security issue
      await this.implementFix(incident);
      await this.documentIncident(incident);
    } else {
      // Low severity - log and monitor
      await this.logIncident(incident);
    }
  }
  
  assessSeverity(incident) {
    if (incident.type === 'data_breach') return 'HIGH';
    if (incident.type === 'unauthorized_access') return 'MEDIUM';
    return 'LOW';
  }
  
  async notifyAffectedCustomers(incident) {
    // Simple email/SMS notification
    const message = `
      Important Security Notice:
      
      We detected and resolved a security issue that may have affected your account.
      Your data is now secure. As a precaution, please:
      
      1. Log in to check your account
      2. Report any suspicious activity
      3. Contact us if you have questions
      
      We sincerely apologize for this incident.
      
      LoyalTea Team
      privacy@loyaltea.my
    `;
    
    // Send via SMS/email to affected customers
  }
}
```

### Backup & Recovery
```bash
#!/bin/bash
# Simple backup script for 2-person team

# Database backup
pg_dump -h localhost -U loyaltea loyaltea > "backup_db_$(date +%Y%m%d_%H%M).sql"

# File backup (logos, uploads)
tar -czf "backup_files_$(date +%Y%m%d_%H%M).tar.gz" /app/uploads/

# Upload to cloud storage (simple approach)
# rclone copy backup_*.sql remote:loyaltea-backups/
# rclone copy backup_*.tar.gz remote:loyaltea-backups/

# Keep only last 30 days of backups
find . -name "backup_*" -type f -mtime +30 -delete

echo "Backup completed: $(date)"
```

---

## 📊 Security Monitoring (Basic)

### Essential Monitoring
```javascript
// Simple security monitoring for 2-person team
class SecurityMonitor {
  constructor() {
    this.alertThresholds = {
      failed_logins: 10, // per hour per IP
      api_errors: 50, // per hour
      new_registrations: 20 // per hour (spam protection)
    };
  }
  
  async checkSecurityMetrics() {
    const metrics = await this.getHourlyMetrics();
    
    if (metrics.failed_logins > this.alertThresholds.failed_logins) {
      await this.sendAlert('High number of failed logins detected');
    }
    
    if (metrics.api_errors > this.alertThresholds.api_errors) {
      await this.sendAlert('High API error rate detected');
    }
    
    if (metrics.new_registrations > this.alertThresholds.new_registrations) {
      await this.sendAlert('Possible spam registration activity');
    }
  }
  
  async sendAlert(message) {
    // Simple WhatsApp/email alert to team
    console.log(`SECURITY ALERT: ${message}`);
    // Send to development team via WhatsApp/email
  }
}

// Run security check every hour
setInterval(() => {
  new SecurityMonitor().checkSecurityMetrics();
}, 60 * 60 * 1000);
```

---

## ✅ Security Checklist for 2-Person Team

### Pre-Launch Security (Essential)
```markdown
## Before Going Live:

### Technical Security:
- [ ] HTTPS enabled on both domains (Cloudflare SSL)
- [ ] Database passwords are strong and unique
- [ ] JWT secret key is cryptographically secure
- [ ] Rate limiting implemented on all APIs
- [ ] Input validation on all user inputs
- [ ] File upload restrictions (size, type)
- [ ] Basic SQL injection prevention (parameterized queries)
- [ ] XSS prevention (input sanitization)

### Data Protection:
- [ ] Customer data encryption in database
- [ ] Minimal data collection (only what's needed)
- [ ] Clear privacy policy published
- [ ] Customer consent mechanism implemented
- [ ] Data deletion capability built
- [ ] Backup system working and tested

### Business Compliance:
- [ ] Business registered in Malaysia (SSM)
- [ ] Business bank account opened
- [ ] Basic terms of service published
- [ ] Customer support contact info available
- [ ] PDPA compliance documentation ready

### Incident Response:
- [ ] Basic monitoring alerts set up
- [ ] Backup and recovery procedures tested
- [ ] Team contact info for emergencies
- [ ] Simple incident response plan documented
```

### Monthly Security Tasks
```markdown
## Regular Security Maintenance:

### Weekly:
- [ ] Check system logs for errors/anomalies
- [ ] Verify backup system is working
- [ ] Monitor customer support for security issues

### Monthly:
- [ ] Review user access (remove old staff accounts)
- [ ] Check for software updates (Node.js, PostgreSQL)
- [ ] Test backup restoration process
- [ ] Review security monitoring alerts

### Quarterly:
- [ ] Update dependencies and security patches
- [ ] Review privacy policy and terms of service
- [ ] Assess security measures effectiveness
- [ ] Plan security improvements for next quarter
```

---

**This security and compliance guide provides practical, implementable security measures for a 2-person team while meeting Malaysian regulatory requirements and protecting customer data effectively.**