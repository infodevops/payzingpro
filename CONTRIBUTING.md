# Contributing to Payroll Manager Pro

Thank you for considering contributing to Payroll Manager Pro! 🎉

This document provides guidelines for contributing to this project. Following these guidelines helps maintain code quality and makes the review process smoother.

## 📋 Table of Contents

- [Code of Conduct](#code-of-conduct)
- [How Can I Contribute?](#how-can-i-contribute)
- [Development Setup](#development-setup)
- [Coding Standards](#coding-standards)
- [Pull Request Process](#pull-request-process)
- [Reporting Bugs](#reporting-bugs)
- [Suggesting Features](#suggesting-features)

---

## 📜 Code of Conduct

### Our Pledge

We are committed to providing a welcoming and inclusive environment for everyone, regardless of:
- Gender, gender identity and expression
- Sexual orientation
- Disability
- Physical appearance
- Race, ethnicity, nationality
- Age, religion

### Our Standards

**Positive behaviors:**
- Using welcoming and inclusive language
- Being respectful of differing viewpoints
- Gracefully accepting constructive criticism
- Focusing on what's best for the community
- Showing empathy towards others

**Unacceptable behaviors:**
- Trolling, insulting/derogatory comments, personal attacks
- Public or private harassment
- Publishing others' private information
- Other conduct which could reasonably be considered inappropriate

---

## 🤝 How Can I Contribute?

### 1. Report Bugs
Found a bug? Please [open an issue](../../issues/new?template=bug_report.md) with:
- Clear, descriptive title
- Steps to reproduce
- Expected vs actual behavior
- Screenshots if applicable
- Your environment (browser, OS)

### 2. Suggest Features
Have an idea? [Open a feature request](../../issues/new?template=feature_request.md) with:
- Use case description
- Proposed solution
- Alternative solutions considered
- Impact on existing functionality

### 3. Improve Documentation
Documentation improvements are always welcome:
- Fix typos or unclear instructions
- Add examples or use cases
- Translate to other languages
- Update outdated information

### 4. Submit Code Changes
Ready to code? See [Development Setup](#development-setup) below.

---

## 🛠️ Development Setup

### Prerequisites
```bash
# No build tools required! This is a pure HTML/CSS/JS project
# You just need:
- A text editor (VS Code, Sublime, Notepad++)
- A modern web browser (Chrome, Firefox, Edge)
- A Firebase account (free)
- A GitHub account (free)
```

### Local Development

#### Step 1: Fork & Clone
```bash
# Fork the repository on GitHub (click Fork button)

# Clone your fork
git clone https://github.com/YOUR_USERNAME/payroll-manager-pro.git
cd payroll-manager-pro
```

#### Step 2: Create Firebase Test Project
```bash
1. Go to https://console.firebase.google.com/
2. Create new project: "PayrollManagerPro-Dev"
3. Enable Authentication (Email/Password)
4. Create Firestore Database (test mode)
5. Copy Firebase config
```

#### Step 3: Configure Local Environment
```bash
# Open index.html in your editor
# Replace Firebase config with your test project config
# (Around line 850)

const firebaseConfig = {
    apiKey: "YOUR_DEV_API_KEY",
    authDomain: "YOUR_DEV_PROJECT.firebaseapp.com",
    // ... rest of config
};
```

#### Step 4: Run Locally
```bash
# Method 1: Simple HTTP server (Python)
python -m http.server 8000
# Open http://localhost:8000

# Method 2: Node.js http-server
npx http-server
# Open http://localhost:8080

# Method 3: VS Code Live Server extension
# Right-click index.html → "Open with Live Server"
```

#### Step 5: Test Your Changes
```bash
1. Create test Super Admin account
2. Add test company
3. Add test employees
4. Generate test payslips
5. Test all affected features
```

---

## 📐 Coding Standards

### HTML
```html
<!-- Use semantic HTML5 elements -->
<main>
  <section class="payroll-section">
    <header>
      <h2>Section Title</h2>
    </header>
    <!-- Content -->
  </section>
</main>

<!-- Proper indentation (2 spaces) -->
<!-- Descriptive IDs and classes -->
<!-- Accessibility attributes (aria-label, alt text) -->
```

### CSS
```css
/* Use CSS variables for colors */
:root {
    --primary-color: #1e40af;
    --text-color: #1f2937;
}

/* BEM naming convention for custom classes */
.payroll-form__input--error {
    border-color: var(--error-color);
}

/* Mobile-first responsive design */
.container {
    /* Mobile styles */
}

@media (min-width: 768px) {
    .container {
        /* Desktop styles */
    }
}
```

### JavaScript
```javascript
// Use ES6+ features
const calculateNetSalary = (basic, allowances, deductions) => {
    return basic + allowances - deductions;
};

// Descriptive variable names
const employeeBasicSalary = 50000; // Good
const x = 50000; // Bad

// Add comments for complex logic
// Calculate HRA as 40% of basic salary (as per Indian tax laws)
const hra = basicSalary * 0.4;

// Error handling
try {
    await saveEmployee(employeeData);
} catch (error) {
    console.error('Failed to save employee:', error);
    showErrorMessage('Unable to save employee. Please try again.');
}

// Async/await over promises
// Good
async function loadEmployees() {
    const snapshot = await db.collection('employees').get();
    return snapshot.docs.map(doc => doc.data());
}

// Avoid
db.collection('employees').get().then(snapshot => {
    // ...
});
```

### Firebase/Firestore
```javascript
// Use subcollections for related data
companies/{companyId}/employees/{employeeId}
companies/{companyId}/payslips/{payslipId}

// Batch writes for multiple operations
const batch = db.batch();
batch.set(ref1, data1);
batch.update(ref2, data2);
await batch.commit();

// Proper error handling
try {
    await db.collection('employees').doc(id).set(data);
} catch (error) {
    if (error.code === 'permission-denied') {
        console.error('Permission denied');
    }
}
```

### Security
```javascript
// Never expose sensitive data in client code
// ❌ BAD
const adminPassword = "admin123";

// ✅ GOOD - Use Firebase Security Rules
// (Firestore rules handle permissions)

// Validate all user inputs
function validateEmail(email) {
    const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
    return emailRegex.test(email);
}

// Sanitize before displaying user input
const safeHTML = DOMPurify.sanitize(userInput);
```

---

## 🔄 Pull Request Process

### Before Submitting

**Checklist:**
- [ ] Code follows project coding standards
- [ ] All tests pass (manual testing)
- [ ] Comments added for complex logic
- [ ] Documentation updated if needed
- [ ] Firestore security rules updated if schema changed
- [ ] No console.log statements left in code
- [ ] Firebase config uses placeholder values (YOUR_API_KEY)
- [ ] Responsive design tested (mobile + desktop)
- [ ] Tested in Chrome, Firefox, and Safari
- [ ] No breaking changes to existing features

### Creating Pull Request

#### Step 1: Create Feature Branch
```bash
# Update main branch
git checkout main
git pull upstream main

# Create feature branch
git checkout -b feature/add-bulk-payslip-generation
# or
git checkout -b fix/payslip-calculation-error
# or
git checkout -b docs/update-deployment-guide
```

#### Step 2: Make Changes
```bash
# Make your changes
# Test thoroughly

# Commit with clear messages
git add .
git commit -m "feat: Add bulk payslip generation feature

- Add batch processing for multiple employees
- Implement progress indicator
- Add error handling for failed generations
- Update UI with bulk action button

Closes #123"
```

#### Step 3: Push & Create PR
```bash
# Push to your fork
git push origin feature/add-bulk-payslip-generation

# Go to GitHub and create Pull Request
# Fill in the PR template
```

### Commit Message Format

Follow [Conventional Commits](https://www.conventionalcommits.org/):

```
<type>(<scope>): <subject>

<body>

<footer>
```

**Types:**
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style changes (formatting, no logic change)
- `refactor`: Code refactoring
- `perf`: Performance improvements
- `test`: Adding/updating tests
- `chore`: Maintenance tasks

**Examples:**
```bash
feat(payslip): Add bulk generation for multiple employees

fix(auth): Resolve login lockout issue after 5 attempts

docs(readme): Update Firebase setup instructions

style(css): Improve button hover effects

refactor(employees): Simplify employee data validation

perf(db): Optimize Firestore query with indexing

chore(deps): Update Firebase SDK to v10.7.0
```

### PR Template

```markdown
## Description
Brief description of changes

## Type of Change
- [ ] Bug fix (non-breaking change fixing an issue)
- [ ] New feature (non-breaking change adding functionality)
- [ ] Breaking change (fix or feature causing existing functionality to not work)
- [ ] Documentation update

## Testing Done
- [ ] Tested on Chrome
- [ ] Tested on Firefox
- [ ] Tested on mobile (responsive)
- [ ] Created test Super Admin account
- [ ] Generated test payslips
- [ ] Verified security rules

## Screenshots (if applicable)
[Add screenshots here]

## Checklist
- [ ] Code follows project style guidelines
- [ ] Self-reviewed my own code
- [ ] Commented complex code sections
- [ ] Updated documentation
- [ ] No console warnings or errors
- [ ] Tested all affected features

## Related Issues
Closes #issue_number
```

### Review Process

1. **Automated Checks**: GitHub Actions will run (if configured)
2. **Code Review**: Maintainer reviews code quality and logic
3. **Testing**: Maintainer tests functionality
4. **Feedback**: Changes requested or approved
5. **Merge**: Once approved, PR is merged to main

**Response Time:**
- We aim to respond to PRs within 48 hours
- Complex PRs may take longer to review
- Be patient and responsive to feedback

---

## 🐛 Reporting Bugs

### Before Reporting

1. **Search existing issues**: Your bug might already be reported
2. **Try latest version**: Bug might be fixed in recent commits
3. **Test in different browser**: Might be browser-specific

### Bug Report Template

```markdown
**Bug Description**
Clear description of the bug

**Steps to Reproduce**
1. Go to '...'
2. Click on '...'
3. Scroll down to '...'
4. See error

**Expected Behavior**
What should happen

**Actual Behavior**
What actually happens

**Screenshots**
Add screenshots if applicable

**Environment**
- Browser: [Chrome 120, Firefox 121, etc.]
- OS: [Windows 11, macOS 14, Ubuntu 22.04]
- Screen size: [Desktop 1920x1080, Mobile 375x667]

**Additional Context**
Any other relevant information

**Console Errors** (if any)
```javascript
// Paste console errors here
```

**Firebase Console Errors** (if any)
// Paste Firestore errors here
```
```

---

## 💡 Suggesting Features

### Feature Request Template

```markdown
**Feature Description**
Clear description of the proposed feature

**Problem/Use Case**
What problem does this solve? Who will benefit?

**Proposed Solution**
Detailed explanation of how it should work

**Alternative Solutions**
Other approaches considered

**Impact Analysis**
- Breaking changes: Yes/No
- Affects: [Auth, Database, UI, etc.]
- Effort estimate: [Small/Medium/Large]

**Mockups/Examples**
Visual examples if applicable

**Additional Context**
Any other relevant information
```

---

## 🎯 Priority Labels

Issues and PRs are labeled by priority:

| Label | Description | Response Time |
|-------|-------------|---------------|
| `critical` | System breaking bugs | 24 hours |
| `high` | Major features/bugs | 2-3 days |
| `medium` | Enhancements | 1 week |
| `low` | Nice-to-have | When available |
| `good-first-issue` | Good for beginners | - |
| `help-wanted` | Community help needed | - |

---

## 🏆 Recognition

Contributors are recognized in:
- **README.md**: Contributors section
- **Release Notes**: Feature/fix credits
- **GitHub**: Contributor badge on profile

Top contributors may be invited as maintainers!

---

## 📞 Getting Help

### Stuck on Something?

1. **Check Documentation**
   - README.md
   - DEPLOYMENT_GUIDE.txt
   - Code comments

2. **Search Issues**
   - Existing discussions
   - Closed issues (might have solutions)

3. **Ask in Discussions**
   - [GitHub Discussions](../../discussions)
   - Tag with appropriate category

4. **Discord/Slack** (if available)
   - Join community chat
   - Ask in #development channel

### Need Maintainer Help?

Tag maintainers in your issue/PR:
```
@maintainer-username Could you review this?
```

---

## 📚 Resources

### Learn More About

**Firebase**
- [Firebase Documentation](https://firebase.google.com/docs)
- [Firestore Data Modeling](https://firebase.google.com/docs/firestore/data-model)
- [Security Rules](https://firebase.google.com/docs/firestore/security/get-started)

**Web Development**
- [MDN Web Docs](https://developer.mozilla.org/)
- [JavaScript.info](https://javascript.info/)
- [CSS Tricks](https://css-tricks.com/)

**Git & GitHub**
- [Git Basics](https://git-scm.com/book/en/v2/Getting-Started-Git-Basics)
- [GitHub Flow](https://guides.github.com/introduction/flow/)
- [Writing Good Commit Messages](https://chris.beams.io/posts/git-commit/)

---

## 🙏 Thank You!

Your contributions make this project better for everyone. Whether you:
- Report a bug
- Suggest a feature
- Write code
- Improve docs
- Help others

**You're awesome! 🌟**

---

## Questions?

Have questions about contributing? Feel free to:
- [Open an issue](../../issues/new?template=question.md)
- [Start a discussion](../../discussions)
- Email: payroll-manager-pro@example.com (if available)

**Happy Contributing! 🚀**