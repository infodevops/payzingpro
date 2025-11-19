# 💼 Payroll Manager Pro

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![GitHub Pages](https://img.shields.io/badge/Deployed-GitHub%20Pages-brightgreen)](https://yourusername.github.io/payroll-app/)
[![Firebase](https://img.shields.io/badge/Backend-Firebase-orange)](https://firebase.google.com/)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)

**Enterprise-grade, multi-company payroll management system — completely free and open source.**

[🚀 Live Demo](https://yourusername.github.io/payroll-app/) | [📖 Documentation](#documentation) | [🤝 Contributing](CONTRIBUTING.md) | [🐛 Report Bug](../../issues)

---

## ✨ Features

### 🏢 **Multi-Company Support**
- Manage unlimited companies in one system
- Company-specific branding with logo support
- Complete isolation between company data

### 👥 **4-Tier User Hierarchy**
- **Super Admin**: Full system control across all companies
- **Company Admin**: Manage company settings and employees
- **HR Manager**: Employee and payroll management
- **Employee**: View personal payslips and salary history

### 💰 **Complete Indian Payroll Compliance (2025)**
- Professional payslip generation with all mandatory fields
- HRA, DA, PF, ESI automatic calculations
- TDS support
- LOP (Loss of Pay) calculations
- Form 16 framework ready
- PAN, UAN, ESI number management

### 🔐 **Advanced Security**
- Firebase Authentication integration
- Account lockout after 5 failed login attempts
- Password recovery with email verification
- Optional 2FA for all user levels
- Row-level security with Firestore rules
- Audit trail for all operations

### 📊 **Employee Management**
- Department and designation tracking
- Bank details management
- Complete employee lifecycle (Active/Resigned/Terminated)
- Custom employee ID system
- Bulk operations support

### 📈 **Reports & Analytics**
- Monthly payroll reports
- Company-wise summaries
- Employee salary history
- Export to PDF/CSV

### 💵 **Zero Cost Forever**
- 100% free to use
- Firebase free tier: 200,000+ employees capacity
- GitHub Pages hosting: Free unlimited bandwidth
- No credit card required
- No hidden fees

---

## 🚀 Quick Start (25 Minutes)

### Prerequisites
- Gmail account (for Firebase)
- GitHub account (for hosting)
- Any modern web browser

### Step 1: Firebase Setup (20 minutes)

#### 1.1 Create Firebase Project
```bash
1. Go to https://console.firebase.google.com/
2. Click "Add project"
3. Name: "PayrollManagerPro"
4. Disable Google Analytics
5. Click "Create project"
```

#### 1.2 Enable Authentication
```bash
1. Left sidebar → "Authentication"
2. Click "Get started"
3. Enable "Email/Password"
4. Click "Save"
```

#### 1.3 Create Firestore Database
```bash
1. Left sidebar → "Firestore Database"
2. Click "Create database"
3. Start in "test mode"
4. Choose region: asia-south1 (Mumbai) for India
5. Click "Enable"
```

#### 1.4 Get Firebase Configuration
```bash
1. Click ⚙️ → "Project settings"
2. Scroll to "Your apps"
3. Click web icon (</>)
4. Register app: "PayrollWebApp"
5. Copy the firebaseConfig object
```

#### 1.5 Apply Security Rules
```bash
1. Go to "Firestore Database" → "Rules" tab
2. Copy rules from firestore.rules file in this repo
3. Click "Publish"
```

### Step 2: Deploy Application (5 minutes)

#### 2.1 Clone/Download Repository
```bash
git clone https://github.com/yourusername/payroll-manager-pro.git
cd payroll-manager-pro
```

#### 2.2 Configure Firebase
Open `index.html` and replace the Firebase config (around line 850):

```javascript
const firebaseConfig = {
    apiKey: "YOUR_API_KEY",
    authDomain: "YOUR_PROJECT_ID.firebaseapp.com",
    projectId: "YOUR_PROJECT_ID",
    storageBucket: "YOUR_PROJECT_ID.appspot.com",
    messagingSenderId: "YOUR_MESSAGING_SENDER_ID",
    appId: "YOUR_APP_ID"
};
```

#### 2.3 Deploy to GitHub Pages

**Method 1: GitHub Web Interface (Easiest)**
```bash
1. Create new repository: "payroll-app"
2. Upload index.html
3. Go to Settings → Pages
4. Source: main branch, / (root)
5. Save and wait 1 minute
6. Your app will be live at: https://yourusername.github.io/payroll-app/
```

**Method 2: Git Command Line**
```bash
git add .
git commit -m "Initial deployment"
git push origin main

# Enable GitHub Pages in repository settings
```

**Method 3: GitHub Actions (Automatic)**
```bash
# The .github/workflows/deploy.yml in this repo
# automatically deploys on every push to main
```

---

## 📖 Documentation

### First-Time Setup

#### Create Super Admin Account
1. Open your deployed app URL
2. Click "Create Account"
3. Fill in your details
4. First user automatically becomes Super Admin

#### Add Your First Company
1. Login as Super Admin
2. Go to "Companies" tab
3. Click "+ Add Company"
4. Fill required fields:
   - Company Name
   - Company Admin Name
   - Company Admin Email
   - Company Admin Phone
5. Save and note the generated credentials

#### Add First Employee
1. Select company from dropdown
2. Go to "Employees" tab
3. Click "+ Add Employee"
4. Fill details:
   - Employee ID
   - Name, Email
   - Designation, Department
   - Basic Salary
   - Bank details (optional)
5. Check "Grant Website Access" if employee needs login

#### Generate First Payslip
1. Go to "Employees" tab
2. Click "💰 Payslip" button
3. Select month/year
4. Enter working days and paid days
5. Click "Generate Payslip"
6. Print or download PDF

### User Roles & Permissions

| Feature | Super Admin | Company Admin | HR Manager | Employee |
|---------|-------------|---------------|------------|----------|
| View all companies | ✅ | ❌ | ❌ | ❌ |
| Manage companies | ✅ | Own only | ❌ | ❌ |
| Add users | ✅ | HR only | ❌ | ❌ |
| Manage employees | ✅ | ✅ | ✅ | ❌ |
| Generate payslips | ✅ | ✅ | ✅ | ❌ |
| View own payslips | ✅ | ✅ | ✅ | ✅ |
| View reports | ✅ | Own company | Own company | Own data |

### Payroll Calculations

```javascript
// Automatic calculations performed by the system:

Gross Salary = Basic + HRA + DA + Special Allowances
HRA = Basic × 40%
DA = Basic × 20%
PF Deduction = Basic × 12% (if employee has PF)
ESI Deduction = Gross × 0.75% (if Gross ≤ ₹21,000)
LOP Deduction = (Basic / Working Days) × LOP Days
Net Salary = Gross - (PF + ESI + TDS + LOP)
```

### Security Features

#### Firestore Security Rules
```javascript
// Enforced at database level:
- Users can only read/write their own data
- Company data isolated per company
- Super Admin has global access
- Employees can only read own payslips
- All write operations require authentication
```

#### Account Lockout
```javascript
// Failed login protection:
- Max attempts: 5
- Lockout duration: 30 minutes
- Admin can unlock manually
- Audit log maintained
```

#### Password Requirements
```javascript
// Enforced by Firebase Auth:
- Minimum 6 characters
- Recommended: 8+ chars with numbers and symbols
- Password reset via email
```

---

## 🏗️ Architecture

### Tech Stack
- **Frontend**: Pure HTML5, CSS3, JavaScript (ES6+)
- **Backend**: Firebase (Firestore + Authentication)
- **Hosting**: GitHub Pages
- **Architecture**: Single-page application (SPA)

### Database Schema

```
companies/
  {companyId}/
    - name, pan, tan, pf, esi, address
    - logo, createdAt, createdBy

users/
  {userId}/
    - email, name, role, companyId
    - phoneNumber, createdAt
    - loginAttempts, isLocked

employees/
  {employeeId}/
    - companyId, employeeId, name, email
    - designation, department, basicSalary
    - bankDetails, pfDetails, esiDetails
    - status (Active/Resigned/Terminated)

payslips/
  {payslipId}/
    - companyId, employeeId, month, year
    - earnings, deductions, netSalary
    - generatedBy, generatedAt
```

### File Structure
```
payroll-manager-pro/
├── index.html              # Main application (single file)
├── firestore.rules         # Firestore security rules
├── .github/
│   └── workflows/
│       └── deploy.yml      # CI/CD automation
├── docs/
│   ├── DEPLOYMENT_GUIDE.md # Detailed deployment guide
│   └── API.md              # Firebase API reference
├── README.md               # This file
├── LICENSE                 # MIT License
├── CONTRIBUTING.md         # Contribution guidelines
├── SECURITY.md             # Security policy
└── .gitignore              # Git ignore rules
```

---

## 🤝 Contributing

We welcome contributions! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for details.

### How to Contribute

1. **Fork the repository**
```bash
git clone https://github.com/yourusername/payroll-manager-pro.git
```

2. **Create a feature branch**
```bash
git checkout -b feature/amazing-feature
```

3. **Make your changes**
```bash
# Edit files
git add .
git commit -m "Add amazing feature"
```

4. **Push to your fork**
```bash
git push origin feature/amazing-feature
```

5. **Open a Pull Request**

### Development Setup

```bash
# No build process required! 
# Just open index.html in a browser

# For live development, use any HTTP server:
python -m http.server 8000
# or
npx http-server
```

---

## 🐛 Troubleshooting

### Firebase Configuration Error
```
Error: "Firebase not defined"
Solution: Double-check firebaseConfig values in index.html
Verify: apiKey, projectId, authDomain
```

### Permission Denied
```
Error: "insufficient permissions"
Solution: 
1. Verify Firestore rules are published
2. Check user is logged in
3. Ensure user role has required permissions
```

### Cannot See Company Data
```
Solution:
1. Select company from dropdown (top of page)
2. Verify user is assigned to that company
3. Super Admin can see all companies
```

### Employee Can't Login
```
Solution:
1. Ensure "Grant Website Access" was checked
2. Verify employee status is "Active"
3. Share correct temporary password
```

### Payslip Not Generating
```
Solution:
1. Verify employee has Basic Salary set
2. Check working days ≥ paid days
3. Ensure all required fields filled
```

---

## 📊 System Limits

### Firebase Free Tier (Spark Plan)
| Resource | Limit | Capacity |
|----------|-------|----------|
| Firestore Storage | 1 GB | ~200,000 employees |
| Document Reads | 50,000/day | ~1,600 payslips/day |
| Document Writes | 20,000/day | ~650 new employees/day |
| Authentication | Unlimited | Unlimited users |
| **Cost** | **₹0** | **Forever** |

### GitHub Pages
| Resource | Limit |
|----------|-------|
| Repository Size | 1 GB |
| Bandwidth | 100 GB/month |
| Build Time | 10 min/build |
| **Cost** | **₹0** |

---

## 🔒 Security

### Reporting Vulnerabilities
Please see [SECURITY.md](SECURITY.md) for reporting security issues.

### Security Features
- ✅ Firebase Authentication
- ✅ Firestore Security Rules (row-level security)
- ✅ Account lockout protection
- ✅ Password reset with email verification
- ✅ Optional 2FA for all users
- ✅ Data encryption at rest (Firebase)
- ✅ HTTPS-only communication
- ✅ Audit trail for all operations

---

## 📄 License

This project is licensed under the MIT License - see [LICENSE](LICENSE) file for details.

**TL;DR**: You can use, modify, and distribute this software freely, even commercially, as long as you include the original license.

---

## 🙏 Acknowledgments

- Built with [Firebase](https://firebase.google.com/)
- Hosted on [GitHub Pages](https://pages.github.com/)
- Icons from system emoji library
- Inspired by modern payroll systems

---

## 📞 Support

### Documentation
- [Deployment Guide](docs/DEPLOYMENT_GUIDE.md)
- [API Reference](docs/API.md)
- [User Manual](docs/USER_MANUAL.md)

### Community
- [GitHub Issues](../../issues) - Bug reports and feature requests
- [GitHub Discussions](../../discussions) - Questions and discussions
- [Pull Requests](../../pulls) - Code contributions

### Status
- ✅ Fully functional
- ✅ Production ready
- ✅ Actively maintained
- ✅ Open for contributions

---

## 🗺️ Roadmap

### Version 2.1 (Planned)
- [ ] Bulk payslip generation
- [ ] Email payslips directly to employees
- [ ] Attendance integration
- [ ] Leave management
- [ ] Overtime calculations

### Version 2.2 (Planned)
- [ ] Mobile app (React Native)
- [ ] Advanced reporting with charts
- [ ] Salary revision management
- [ ] Performance appraisal module
- [ ] Multi-language support

### Version 3.0 (Future)
- [ ] AI-powered salary recommendations
- [ ] Compliance automation
- [ ] Integration with accounting software
- [ ] Biometric attendance integration

---

## 💡 Use Cases

- **Small Businesses**: Manage 10-50 employees per company
- **Startups**: Multi-company setup for holding structure
- **Consultancies**: Manage client payrolls
- **HR Agencies**: Service multiple client companies
- **Freelancers**: Personal salary management
- **Educational Projects**: Learn Firebase and web development

---

## 🌟 Star History

[![Star History Chart](https://api.star-history.com/svg?repos=yourusername/payroll-manager-pro&type=Date)](https://star-history.com/#yourusername/payroll-manager-pro&Date)

---

## 📈 Statistics

![GitHub repo size](https://img.shields.io/github/repo-size/yourusername/payroll-manager-pro)
![GitHub code size](https://img.shields.io/github/languages/code-size/yourusername/payroll-manager-pro)
![GitHub commit activity](https://img.shields.io/github/commit-activity/m/yourusername/payroll-manager-pro)
![GitHub last commit](https://img.shields.io/github/last-commit/yourusername/payroll-manager-pro)

---

<div align="center">

**Made with ❤️ for the open-source community**

[⬆ Back to Top](#-payroll-manager-pro)

</div>