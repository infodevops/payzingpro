# 🚀 Production Deployment Script for Payroll Manager Pro

## Quick Deployment Commands

### One-Line Deployment (Linux/Mac)
```bash
git clone https://github.com/yourusername/payroll-manager-pro.git && cd payroll-manager-pro && chmod +x deploy.sh && ./deploy.sh
```

### Manual Deployment (All Platforms)

#### Step 1: Repository Setup
```bash
# Clone repository
git clone https://github.com/yourusername/payroll-manager-pro.git
cd payroll-manager-pro

# Or if you already have it
cd payroll-manager-pro
git pull origin main
```

#### Step 2: Firebase Configuration

**OPTION A: Environment Variables (Recommended)**
```bash
# Create .env file (not committed to repo)
cat > .env << EOF
FIREBASE_API_KEY=your_api_key_here
FIREBASE_AUTH_DOMAIN=your_project.firebaseapp.com
FIREBASE_PROJECT_ID=your_project_id
FIREBASE_STORAGE_BUCKET=your_project.appspot.com
FIREBASE_MESSAGING_SENDER_ID=123456789012
FIREBASE_APP_ID=1:123456789012:web:abcdef
EOF

# Replace config in index.html
sed -i 's/YOUR_API_KEY/'$FIREBASE_API_KEY'/g' index.html
# ... (repeat for other variables)
```

**OPTION B: Direct Edit (Simpler)**
```bash
# Open index.html and replace Firebase config
nano index.html
# or
vim index.html
# or use any text editor

# Find this section (around line 850):
const firebaseConfig = {
    apiKey: "YOUR_API_KEY",
    authDomain: "YOUR_PROJECT_ID.firebaseapp.com",
    projectId: "YOUR_PROJECT_ID",
    storageBucket: "YOUR_PROJECT_ID.appspot.com",
    messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
    appId: "YOUR_APP_ID"
};

# Replace with your actual values from Firebase Console
```

#### Step 3: Deploy to GitHub Pages

**Using Git**
```bash
# Stage changes
git add index.html

# Commit
git commit -m "chore: Configure Firebase for production deployment"

# Push to main branch
git push origin main

# Enable GitHub Pages (first time only)
# Go to: https://github.com/yourusername/payroll-manager-pro/settings/pages
# Source: Deploy from a branch
# Branch: main, / (root)
# Click Save

# Wait 1-2 minutes
# Your app will be live at:
# https://yourusername.github.io/payroll-manager-pro/
```

**Using GitHub Actions (Automatic)**
```bash
# If .github/workflows/deploy.yml exists, deployment is automatic
# Just push to main branch:
git push origin main

# GitHub Actions will:
# 1. Validate code
# 2. Build deployment
# 3. Deploy to GitHub Pages
# 4. Run post-deployment checks

# Monitor deployment:
# https://github.com/yourusername/payroll-manager-pro/actions
```

---

## 🔐 Security Checklist (Production)

### Pre-Deployment Security

- [ ] **Firestore Security Rules Applied**
  ```bash
  # In Firebase Console:
  # Firestore Database → Rules → Copy from firestore-rules.txt → Publish
  ```

- [ ] **Authentication Settings Configured**
  ```bash
  # In Firebase Console:
  # Authentication → Settings
  # - Authorized domains: Add yourusername.github.io
  # - Email enumeration protection: Enable
  ```

- [ ] **Test Mode Disabled**
  ```bash
  # Verify Firestore is NOT in test mode
  # Rules should start with: rules_version = '2';
  # NOT: allow read, write: if true;
  ```

- [ ] **API Key Restrictions (Optional but Recommended)**
  ```bash
  # In Firebase Console:
  # Project Settings → Service Accounts → API Restrictions
  # Restrict key to: HTTP referrers
  # Add: https://yourusername.github.io/*
  ```

- [ ] **Firebase Config Not Placeholder**
  ```bash
  # Check index.html does NOT contain:
  grep -q "YOUR_API_KEY" index.html && echo "⚠️ Config not updated!" || echo "✅ Config updated"
  ```

### Post-Deployment Security

- [ ] **Test Security Rules**
  ```bash
  # In Firebase Console:
  # Firestore → Rules → Rules Playground
  # Test scenarios from firestore-rules.txt comments
  ```

- [ ] **Enable Audit Logging** (Optional - Paid Feature)
  ```bash
  # In Firebase Console:
  # Project Settings → Usage and billing → Details
  # Enable: Cloud Firestore
  # Enable: Authentication
  ```

- [ ] **Set Up Monitoring**
  ```bash
  # In Firebase Console:
  # Overview → Add monitoring widget
  # Monitor: Authentication, Firestore reads/writes
  ```

---

## 🧪 Testing Production Deployment

### Automated Testing Script
```bash
#!/bin/bash

echo "🧪 Testing Production Deployment..."
echo ""

# Test 1: Check if site is accessible
URL="https://yourusername.github.io/payroll-manager-pro/"
echo "1️⃣ Checking site accessibility..."
STATUS=$(curl -s -o /dev/null -w "%{http_code}" $URL)
if [ "$STATUS" = "200" ]; then
    echo "   ✅ Site is live (HTTP $STATUS)"
else
    echo "   ❌ Site returned HTTP $STATUS"
    exit 1
fi

# Test 2: Check if Firebase config is present
echo "2️⃣ Checking Firebase configuration..."
if curl -s $URL | grep -q "firebase.google.com"; then
    echo "   ✅ Firebase SDK loaded"
else
    echo "   ❌ Firebase SDK not found"
    exit 1
fi

# Test 3: Check if placeholder values are present
echo "3️⃣ Checking for placeholder values..."
if curl -s $URL | grep -q "YOUR_API_KEY"; then
    echo "   ❌ Placeholder Firebase config detected!"
    exit 1
else
    echo "   ✅ No placeholder values found"
fi

# Test 4: Check HTTPS
echo "4️⃣ Checking HTTPS enforcement..."
if curl -s -I -L "http://yourusername.github.io/payroll-manager-pro/" | grep -q "https://"; then
    echo "   ✅ HTTPS redirect working"
else
    echo "   ⚠️ HTTPS redirect might not be working"
fi

echo ""
echo "✅ All production tests passed!"
echo "🎉 Your Payroll Manager Pro is production-ready!"
```

### Manual Testing Checklist

```bash
# Visit your deployed app
open https://yourusername.github.io/payroll-manager-pro/

# Test these features:
```

- [ ] **Page Loads Successfully**
  - No console errors
  - Firebase initializes correctly
  - UI renders properly

- [ ] **Create Account (First Super Admin)**
  - Sign up form works
  - Email validation works
  - Account created successfully
  - Automatically gets Super Admin role

- [ ] **Login/Logout**
  - Login with created credentials
  - Session persists on refresh
  - Logout works
  - Cannot access after logout

- [ ] **Add Company**
  - Super Admin can add company
  - Company admin credentials generated
  - Company appears in dropdown

- [ ] **Add Employee**
  - Select company
  - Fill employee details
  - Save successfully
  - Employee appears in list

- [ ] **Generate Payslip**
  - Click payslip button for employee
  - Fill pay period details
  - Calculations correct (HRA, DA, PF, etc.)
  - Payslip displays properly
  - PDF download works

- [ ] **Role-Based Access**
  - Login as Company Admin (different browser/incognito)
  - Can only see own company
  - Cannot access other companies
  - HR and Employee roles work similarly

- [ ] **Security Features**
  - Failed login attempts tracked
  - Account locks after 5 attempts
  - Password reset works
  - 2FA setup works (if enabled)

---

## 📊 Production Monitoring

### Firebase Console Monitoring

```bash
# Daily Checks (5 minutes)
1. Go to: https://console.firebase.google.com/
2. Select your project
3. Check:
   - Authentication → Usage (# of users, sign-ins)
   - Firestore → Usage (reads, writes, storage)
   - Overview → Health (any errors?)

# Weekly Checks (10 minutes)
1. Review failed authentication attempts
2. Check Firestore security rule violations
3. Review storage usage trends
4. Check for unusual activity patterns
```

### Uptime Monitoring (Optional)

```bash
# Use free services like:
# - UptimeRobot (https://uptimerobot.com/)
# - StatusCake (https://www.statuscake.com/)
# - Pingdom (https://www.pingdom.com/)

# Configure:
# URL to monitor: https://yourusername.github.io/payroll-manager-pro/
# Check interval: 5 minutes
# Alert email: your@email.com
```

### GitHub Actions Monitoring

```bash
# View deployment history:
https://github.com/yourusername/payroll-manager-pro/actions

# Set up notifications:
# GitHub Settings → Notifications → Actions
# Enable: "Email" for workflow failures
```

---

## 🔄 Updating Production

### Hotfix Deployment (Critical Bugs)
```bash
# 1. Create hotfix branch
git checkout -b hotfix/critical-payslip-calculation
git pull origin main

# 2. Make fix in index.html
nano index.html
# Fix the bug

# 3. Test locally
python -m http.server 8000
# Test at http://localhost:8000

# 4. Deploy immediately
git add index.html
git commit -m "hotfix: Fix payslip calculation error"
git push origin hotfix/critical-payslip-calculation

# 5. Create Pull Request and merge
# OR merge directly to main (emergency only):
git checkout main
git merge hotfix/critical-payslip-calculation
git push origin main

# 6. Monitor deployment
# GitHub Actions will auto-deploy in 2-3 minutes
```

### Regular Updates (Features/Improvements)
```bash
# 1. Create feature branch
git checkout -b feature/bulk-payslip-generation
git pull origin main

# 2. Make changes
nano index.html
# Add new feature

# 3. Test thoroughly locally
python -m http.server 8000

# 4. Commit and push
git add index.html
git commit -m "feat: Add bulk payslip generation"
git push origin feature/bulk-payslip-generation

# 5. Create Pull Request
# Go to GitHub → Pull Requests → New PR
# Add description, reviewers

# 6. After review and approval, merge to main
# Deployment happens automatically via GitHub Actions
```

### Rollback (If Something Goes Wrong)
```bash
# Option 1: Revert last commit
git revert HEAD
git push origin main
# Deployment automatically reverts

# Option 2: Rollback to specific commit
git log --oneline  # Find good commit hash
git revert abc1234  # Replace with actual hash
git push origin main

# Option 3: Force rollback (DANGER - only if necessary)
git reset --hard abc1234  # Good commit hash
git push origin main --force
# Warning: This rewrites history!
```

---

## 💾 Backup Strategy

### Database Backups (Firestore)

```bash
# AUTOMATIC BACKUPS (Paid Firebase Plan)
# In Firebase Console:
# Firestore → Backups
# Schedule: Daily at 2 AM IST
# Retention: 7 days

# MANUAL EXPORT (Free - do monthly)
# 1. Install Firebase CLI
npm install -g firebase-tools

# 2. Login
firebase login

# 3. Export data
firebase firestore:export gs://your-project-bucket/backups/$(date +%Y%m%d)

# 4. Download locally (optional)
gsutil -m cp -r gs://your-project-bucket/backups/20250120 ./local-backup/
```

### Application Backup

```bash
# Git already backs up your code
# Additional backup to local:
git clone --mirror https://github.com/yourusername/payroll-manager-pro.git backup-$(date +%Y%m%d)

# Or archive current production:
tar -czf payroll-backup-$(date +%Y%m%d).tar.gz index.html firestore-rules.txt README.md
```

---

## 📈 Scaling Considerations

### When to Upgrade Firebase Plan

| Metric | Free Tier Limit | When to Upgrade |
|--------|-----------------|-----------------|
| Storage | 1 GB | > 800 MB (80% full) |
| Reads | 50,000/day | > 40,000/day consistent |
| Writes | 20,000/day | > 16,000/day consistent |
| Users | Unlimited | N/A (always free) |

### Performance Optimization

```javascript
// If app gets slow, optimize Firestore queries:

// Before (slow - reads entire collection)
db.collection('employees').get();

// After (fast - uses indexes)
db.collection('employees')
  .where('companyId', '==', currentCompanyId)
  .where('status', '==', 'Active')
  .limit(50)
  .get();

// Create composite indexes in Firebase Console
// Firestore → Indexes → Add index
```

### GitHub Pages Bandwidth

```bash
# Free tier: 100 GB/month
# Typical usage: ~1 MB per page load
# Supports: ~100,000 page views/month
# If exceeded: GitHub may send warning
# Solution: Migrate to Netlify, Vercel (also free)
```

---

## 🆘 Troubleshooting Production

### Site Not Loading

```bash
# Check 1: Is GitHub Pages enabled?
# Settings → Pages → Source should be "Deploy from branch: main"

# Check 2: Is index.html in root?
ls -la  # Should see index.html

# Check 3: Check deployment status
# Actions tab → Should show successful deployment

# Check 4: Clear browser cache
Ctrl+Shift+R (Windows) or Cmd+Shift+R (Mac)

# Check 5: Wait longer
# First deployment can take up to 10 minutes
```

### Firebase Errors in Console

```bash
# Error: "Firebase: Error (auth/configuration-not-found)"
# Fix: Check Firebase config values are correct

# Error: "Missing or insufficient permissions"
# Fix: Firestore security rules not applied correctly
# Re-apply from firestore-rules.txt

# Error: "Quota exceeded"
# Fix: Upgrade Firebase plan or optimize queries
```

### Authentication Not Working

```bash
# Issue: Cannot create account
# Check: Firebase Authentication enabled
# Console → Authentication → Sign-in method → Email/Password

# Issue: Password reset not working
# Check: Authorized domains includes yourusername.github.io
# Console → Authentication → Settings → Authorized domains
```

---

## 📞 Support & Resources

### Official Documentation
- Firebase: https://firebase.google.com/docs
- GitHub Pages: https://docs.github.com/en/pages
- Project README: https://github.com/yourusername/payroll-manager-pro

### Community Support
- GitHub Issues: https://github.com/yourusername/payroll-manager-pro/issues
- GitHub Discussions: https://github.com/yourusername/payroll-manager-pro/discussions

### Status Pages
- Firebase Status: https://status.firebase.google.com/
- GitHub Status: https://www.githubstatus.com/

---

## ✅ Production Deployment Complete!

Your Payroll Manager Pro is now live at:
**https://yourusername.github.io/payroll-manager-pro/**

Next steps:
1. ✅ Verify all features work
2. ✅ Create your Super Admin account
3. ✅ Add your first company
4. ✅ Invite company admins
5. ✅ Start managing payroll!

**Cost: ₹0 forever** 🎉