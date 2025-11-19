# Security Policy

## 🔒 Reporting a Vulnerability

We take the security of Payroll Manager Pro seriously. If you discover a security vulnerability, please follow responsible disclosure practices.

### How to Report

**DO NOT** open a public GitHub issue for security vulnerabilities.

Instead:

1. **Email**: Send details to [your-email@example.com]
   - Subject: "SECURITY: [Brief Description]"
   - Include: Detailed description, steps to reproduce, impact assessment

2. **GitHub Security Advisory** (Preferred):
   - Go to: https://github.com/yourusername/payroll-manager-pro/security/advisories
   - Click "Report a vulnerability"
   - Fill in the private advisory form

### What to Include

Please provide:
- **Description**: Clear explanation of the vulnerability
- **Impact**: What could an attacker achieve?
- **Steps to Reproduce**: Detailed reproduction steps
- **Affected Versions**: Which versions are vulnerable?
- **Proof of Concept**: Code/screenshots demonstrating the issue
- **Suggested Fix**: If you have recommendations

### Response Timeline

| Stage | Timeline |
|-------|----------|
| Initial Response | Within 48 hours |
| Vulnerability Confirmation | Within 7 days |
| Fix Development | Depends on severity |
| Patch Release | Within 30 days (critical issues: 7 days) |
| Public Disclosure | After patch is released |

---

## 🛡️ Security Measures

### Current Protections

#### 1. Authentication & Authorization
```
✅ Firebase Authentication (industry-standard)
✅ Email/password authentication
✅ Password reset with email verification
✅ Account lockout after 5 failed attempts
✅ Optional 2FA for all user levels
✅ Session management by Firebase
```

#### 2. Data Security
```
✅ Firestore Security Rules (row-level access control)
✅ Data encryption at rest (Firebase managed)
✅ HTTPS-only communication (enforced by Firebase/GitHub Pages)
✅ Company data isolation (cannot cross-access)
✅ Role-based access control (RBAC)
```

#### 3. Client-Side Security
```
✅ Input validation on all forms
✅ XSS prevention (no innerHTML for user input)
✅ CSRF protection via Firebase Auth tokens
✅ No sensitive data in localStorage
✅ Secure random password generation
```

#### 4. Audit & Monitoring
```
✅ All operations logged with timestamps
✅ User actions tracked (who, what, when)
✅ Failed login attempt monitoring
✅ Account lockout logs
```

---

## 🚨 Known Security Considerations

### Firebase Configuration Exposure

**Status**: Expected behavior
**Risk**: Low
**Explanation**:
```javascript
// Firebase config in client code is NORMAL and SAFE
const firebaseConfig = {
    apiKey: "AIzaSy...",  // This is a public identifier, not a secret
    authDomain: "...",
    projectId: "...",
    // ...
};
```

**Why It's Safe**:
- Firebase API keys are public identifiers, not secret keys
- Security is enforced by Firestore Security Rules
- Rules run on Firebase servers, not client-side
- Even with API key, attackers cannot bypass security rules

**Protection**: Firestore Security Rules (firestore.rules)

### Client-Side Code

**Status**: Inherent limitation
**Risk**: Low (with proper security rules)
**Explanation**:
- All HTML/CSS/JS is viewable in browser
- Business logic is visible to users
- Salary calculations can be seen

**Why It's Safe**:
- Calculations are for display only
- All data writes are protected by security rules
- Users can only modify their authorized data
- Firebase validates on server-side

**Mitigation**:
- Critical validation in Firestore Rules
- Server-side functions for sensitive operations (if needed)
- Audit logs for all modifications

---

## 🔐 Firestore Security Rules

### Current Rules (Summary)

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    
    // Super Admin: Access everything
    function isSuperAdmin() {
      return get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'Super Admin';
    }
    
    // Company Admin: Access own company
    function isCompanyAdmin(companyId) {
      return get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'Company Admin'
        && get(/databases/$(database)/documents/users/$(request.auth.uid)).data.companyId == companyId;
    }
    
    // HR: Access own company employees
    function isHR(companyId) {
      return get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'HR Manager'
        && get(/databases/$(database)/documents/users/$(request.auth.uid)).data.companyId == companyId;
    }
    
    // Employee: Access own data only
    function isEmployee(employeeId) {
      return request.auth.uid == employeeId;
    }
    
    // Users collection
    match /users/{userId} {
      allow read: if request.auth != null && (
        isSuperAdmin() || 
        request.auth.uid == userId
      );
      allow write: if request.auth != null && isSuperAdmin();
    }
    
    // Companies collection
    match /companies/{companyId} {
      allow read: if request.auth != null && (
        isSuperAdmin() || 
        isCompanyAdmin(companyId) || 
        isHR(companyId)
      );
      allow write: if request.auth != null && (
        isSuperAdmin() || 
        isCompanyAdmin(companyId)
      );
    }
    
    // Employees collection
    match /employees/{employeeId} {
      allow read: if request.auth != null && (
        isSuperAdmin() || 
        isCompanyAdmin(resource.data.companyId) || 
        isHR(resource.data.companyId) ||
        isEmployee(employeeId)
      );
      allow write: if request.auth != null && (
        isSuperAdmin() || 
        isCompanyAdmin(request.resource.data.companyId) || 
        isHR(request.resource.data.companyId)
      );
    }
    
    // Payslips collection
    match /payslips/{payslipId} {
      allow read: if request.auth != null && (
        isSuperAdmin() || 
        isCompanyAdmin(resource.data.companyId) || 
        isHR(resource.data.companyId) ||
        isEmployee(resource.data.employeeId)
      );
      allow write: if request.auth != null && (
        isSuperAdmin() || 
        isCompanyAdmin(request.resource.data.companyId) || 
        isHR(request.resource.data.companyId)
      );
    }
  }
}
```

### Testing Security Rules

```bash
# In Firebase Console:
1. Go to Firestore Database → Rules tab
2. Click "Rules Playground"
3. Test different user roles and operations
4. Verify read/write permissions work correctly
```

---

## ⚠️ Potential Vulnerabilities & Mitigations

### 1. Mass Assignment

**Risk**: User could try to modify unauthorized fields
**Example**: Employee trying to change their own salary

**Mitigation**:
```javascript
// Validate allowed fields in security rules
allow update: if request.auth != null 
  && request.resource.data.diff(resource.data).affectedKeys()
    .hasOnly(['phoneNumber', 'address', 'profilePicture'])
  && isEmployee(employeeId);
```

### 2. Privilege Escalation

**Risk**: User trying to change their role
**Example**: HR Manager trying to become Company Admin

**Mitigation**:
```javascript
// Prevent role changes by non-Super Admins
allow update: if request.auth != null
  && (isSuperAdmin() || request.resource.data.role == resource.data.role);
```

### 3. Data Leakage via Enumeration

**Risk**: Attacker trying to enumerate all employee IDs
**Example**: Looping through employee IDs to access data

**Mitigation**:
- Security rules prevent unauthorized reads
- Firebase rate limits prevent brute force
- Failed attempts logged and monitored

### 4. XSS (Cross-Site Scripting)

**Risk**: Malicious scripts in employee names/data
**Example**: Employee name: `<script>alert('XSS')</script>`

**Mitigation**:
```javascript
// Use textContent, not innerHTML
element.textContent = employeeName; // Safe
element.innerHTML = employeeName;   // Dangerous

// Validate input format
function isValidName(name) {
  return /^[a-zA-Z\s]+$/.test(name);
}
```

### 5. CSRF (Cross-Site Request Forgery)

**Risk**: Unauthorized actions from malicious sites
**Example**: Malicious site triggering payslip generation

**Mitigation**:
- Firebase Auth tokens required for all requests
- Tokens automatically validated by Firebase
- Origin validation by Firebase Security Rules

---

## 🔧 Security Best Practices for Deployments

### Production Deployment Checklist

- [ ] **Firestore Security Rules Published**: Verify rules are active
- [ ] **Test Mode Disabled**: Ensure not in test mode
- [ ] **Firebase API Restrictions** (Optional): Restrict API key to your domains
- [ ] **HTTPS Only**: GitHub Pages enforces this automatically
- [ ] **Environment Variables**: No secrets in client code
- [ ] **Audit Logs Enabled**: Monitor Firebase console regularly
- [ ] **Regular Backups**: Enable Firestore backup (paid feature)
- [ ] **Access Control Review**: Regularly audit user roles
- [ ] **Dependency Updates**: Keep Firebase SDK updated

### Firebase Console Security Settings

```bash
1. Authentication → Settings:
   - Authorized domains: Add your domain
   - Email enumeration protection: Enable
   
2. Firestore → Rules:
   - Review and test rules
   - Set rules version: rules_version = '2'
   
3. Project Settings → Service Accounts:
   - Review service accounts
   - Revoke unused keys
   
4. IAM & Admin (Firebase Console):
   - Review project members
   - Remove unnecessary access
```

---

## 📊 Security Monitoring

### What to Monitor

1. **Failed Login Attempts**
   - Track in `users` collection: `loginAttempts` field
   - Alert if > 3 attempts in 5 minutes

2. **Unusual Data Access Patterns**
   - Firebase Console → Usage & Billing
   - Sudden spike in reads/writes
   - Access from unusual locations

3. **Rule Violations**
   - Firebase Console → Firestore → Usage
   - "Permission Denied" errors
   - May indicate attack attempt

4. **Account Lockouts**
   - Track `isLocked` and `lockedUntil` fields
   - High lockout rate may indicate brute force

### Monitoring Tools

```bash
# Firebase Console Monitoring:
1. Overview → Health checks
2. Firestore → Usage
3. Authentication → Usage
4. Project Settings → Usage and billing

# External Monitoring (Optional):
- Google Cloud Monitoring (paid)
- Third-party security scanners
- Uptime monitoring services
```

---

## 🚀 Reporting Endpoint Issues

### GitHub Pages Issues

If you discover issues specific to deployment:
- [ ] HTTPS not enforcing
- [ ] CORS errors
- [ ] Content Security Policy issues
- [ ] Resource loading failures

Report via: [GitHub Issues](../../issues) with tag `deployment`

### Firebase Issues

For Firebase-specific problems:
- [ ] Authentication failures
- [ ] Firestore permission errors
- [ ] API quota exceeded
- [ ] Service outages

Check: [Firebase Status](https://status.firebase.google.com/)

---

## 🎓 Security Resources

### Learn More

**OWASP Top 10**
- [OWASP Web Application Security](https://owasp.org/www-project-top-ten/)
- [OWASP Cheat Sheets](https://cheatsheetseries.owasp.org/)

**Firebase Security**
- [Firebase Security Rules Guide](https://firebase.google.com/docs/rules)
- [Firebase Security Best Practices](https://firebase.google.com/docs/rules/best-practices)
- [Firestore Security Rules Cookbook](https://firebase.google.com/docs/firestore/security/rules-conditions)

**Web Security**
- [MDN Web Security](https://developer.mozilla.org/en-US/docs/Web/Security)
- [Content Security Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)

---

## 🏅 Security Hall of Fame

We recognize security researchers who responsibly disclose vulnerabilities:

| Researcher | Vulnerability | Date | Severity |
|------------|---------------|------|----------|
| _Your name here_ | _Description_ | _Date_ | _High/Medium/Low_ |

---

## 📋 Security Checklist for Contributors

Before submitting code:

- [ ] No hardcoded credentials
- [ ] Input validation implemented
- [ ] XSS prevention (use textContent)
- [ ] CSRF tokens not needed (Firebase handles)
- [ ] Security rules updated if schema changed
- [ ] No sensitive data in console.log
- [ ] No SQL injection risk (using Firestore)
- [ ] Authentication required for sensitive operations
- [ ] Role-based access respected

---

## 📞 Contact

**Security Team Email**: security@payroll-manager-pro.example.com

**PGP Key**: [Public key if available]

**Response Hours**: Monday-Friday, 9 AM - 5 PM IST

---

## 📜 Disclosure Policy

### Coordinated Disclosure

We follow responsible disclosure:

1. **Report received**: Acknowledged within 48 hours
2. **Investigation**: 7 days to confirm and assess
3. **Fix developed**: Timeline depends on severity
4. **Patch released**: Deployed to production
5. **Public disclosure**: 
   - After patch deployed
   - Coordinated with reporter
   - Credit given to researcher (if desired)

### Public Recognition

With your permission, we will:
- Credit you in release notes
- Add you to Security Hall of Fame
- Link to your GitHub/website
- Share on social media (optional)

---

**Thank you for helping keep Payroll Manager Pro secure! 🔒**