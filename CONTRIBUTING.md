# Contributing to FollowUpHub

**Project Code:** FUH-2025  
**Version:** 1.0.0  
**Timestamp:** 2025-10-18 22:15:41 UTC

---

## Copyright Notice

© 2025 GURDIAN-X - Costantine George Mpanda. All Rights Reserved.

---

## Welcome! 🎉

Thank you for your interest in contributing to FollowUpHub! This document provides guidelines and instructions for contributing to this project. By participating, you agree to abide by our code of conduct and follow our #ComplianceKwanza philosophy.

---

## Table of Contents

1. [Code of Conduct](#code-of-conduct)
2. [Getting Started](#getting-started)
3. [Development Setup](#development-setup)
4. [How to Contribute](#how-to-contribute)
5. [Coding Standards](#coding-standards)
6. [Testing Requirements](#testing-requirements)
7. [Pull Request Process](#pull-request-process)
8. [Issue Reporting](#issue-reporting)
9. [Documentation](#documentation)
10. [License](#license)

---

## Code of Conduct

### Our Pledge

We are committed to providing a welcoming and inclusive environment for all contributors, regardless of:
- Experience level
- Gender identity and expression
- Sexual orientation
- Disability
- Personal appearance
- Body size
- Race, ethnicity, or nationality
- Age
- Religion or lack thereof

### Expected Behavior

✅ Be respectful and professional  
✅ Use welcoming and inclusive language  
✅ Accept constructive criticism gracefully  
✅ Focus on what's best for the project  
✅ Show empathy towards others  
✅ Respect differing viewpoints and experiences  

### Unacceptable Behavior

❌ Harassment, discrimination, or trolling  
❌ Personal attacks or insults  
❌ Publishing others' private information  
❌ Sexual attention or advances  
❌ Other unprofessional conduct  

### Enforcement

Violations may result in temporary or permanent ban from the project. Report violations to: kapipocostantine@gmail.com

---

## Getting Started

### Prerequisites

Before contributing, ensure you have:
- ✅ Git installed and configured
- ✅ Node.js 18+ and npm 10+
- ✅ PostgreSQL 14+ (for backend development)
- ✅ Code editor (VS Code recommended)
- ✅ Basic understanding of:
  - JavaScript/React
  - REST APIs
  - SQL/PostgreSQL
  - Git workflow

### Project Structure

```
FollowUpHub/
├── backend/               # Node.js/Express API
│   ├── src/
│   │   ├── config/       # Configuration files
│   │   ├── controllers/  # Route handlers
│   │   ├── middleware/   # Express middleware
│   │   ├── routes/       # API routes
│   │   ├── services/     # Business logic
│   │   ├── utils/        # Utility functions
│   │   └── prisma/       # Database schema & migrations
│   ├── tests/            # Test files
│   └── package.json
├── frontend/             # React application
│   ├── src/
│   │   ├── components/   # React components
│   │   ├── pages/        # Page components
│   │   ├── hooks/        # Custom hooks
│   │   ├── services/     # API services
│   │   ├── store/        # State management
│   │   └── locales/      # Translations (SW/EN)
│   └── package.json
├── docs/                 # Additional documentation
├── .cursorrules          # AI assistant guidelines
├── PROJECT_CHARTER.md    # Strategic framework
├── TECHNICAL_SPECS.md    # Technical architecture
├── DATABASE_SCHEMA.md    # Database design
├── API_DOCUMENTATION.md  # API reference
├── SECURITY_POLICY.md    # Security policies
├── CONTRIBUTING.md       # This file
├── CHANGELOG.md          # Version history
├── LICENSE               # License information
├── .gitignore            # Git ignore rules
└── README.md             # Project overview
```

---

## Development Setup

### 1. Fork and Clone

```bash
# Fork the repository on GitHub
# Then clone your fork
git clone https://github.com/YOUR_USERNAME/FollowUpHub.git
cd FollowUpHub

# Add upstream remote
git remote add upstream https://github.com/GURDIAN-X/FollowUpHub.git
```

### 2. Install Dependencies

```bash
# Backend
cd backend
npm install

# Frontend
cd ../frontend
npm install
```

### 3. Environment Configuration

```bash
# Backend
cd backend
cp .env.example .env
# Edit .env with your local configuration

# Frontend
cd ../frontend
cp .env.example .env
# Edit .env with your local configuration
```

### 4. Database Setup

```bash
cd backend

# Create database
createdb followuphub

# Run migrations
npx prisma migrate dev

# Seed database (optional)
npx prisma db seed
```

### 5. Start Development Servers

```bash
# Backend (terminal 1)
cd backend
npm run dev

# Frontend (terminal 2)
cd frontend
npm run dev
```

**Access the application:**
- Frontend: http://localhost:5173
- Backend API: http://localhost:3000/api/v1

---

## How to Contribute

### Types of Contributions

We welcome various types of contributions:

1. **🐛 Bug Fixes:** Fix issues and improve stability
2. **✨ New Features:** Add new functionality (discuss first)
3. **📝 Documentation:** Improve or add documentation
4. **🧪 Tests:** Add or improve test coverage
5. **🎨 UI/UX:** Enhance user interface and experience
6. **🔒 Security:** Security improvements and fixes
7. **♿ Accessibility:** Improve WCAG 2.1 AA compliance
8. **🌍 Translations:** Add or improve Swahili/English translations
9. **⚡ Performance:** Optimize code and queries

### Contribution Workflow

1. **Check existing issues** or create a new one to discuss your idea
2. **Create a branch** from `develop` for your work
3. **Make changes** following our coding standards
4. **Write tests** for your changes (≥80% coverage)
5. **Run linters and tests** to ensure quality
6. **Commit changes** using conventional commits
7. **Push to your fork** and create a pull request
8. **Address review feedback** and iterate
9. **Celebrate** when your PR is merged! 🎉

### Branch Naming Convention

Use descriptive branch names:

```
feature/add-user-notifications
fix/resolve-login-timeout
docs/update-api-documentation
refactor/improve-query-performance
test/add-resolution-tests
security/fix-xss-vulnerability
```

---

## Coding Standards

### JavaScript/React Style

**ESLint Configuration:**
- Airbnb JavaScript Style Guide
- React best practices
- No unused variables
- Consistent formatting

**Run linter:**
```bash
npm run lint
npm run lint:fix
```

### Code Formatting

**Prettier Configuration:**
- Single quotes
- Semicolons required
- 2-space indentation
- Line length: 100 characters
- Trailing commas (ES5)

**Format code:**
```bash
npm run format
```

### JSDoc Comments

All public functions must have JSDoc comments:

```javascript
/**
 * Create a new resolution
 * @param {Object} data - Resolution data
 * @param {string} data.description - Resolution description
 * @param {string} data.type - Resolution type (ILIELEKEZA/ILISHAURIWA)
 * @param {string} data.organizationId - Organization ID
 * @returns {Promise<Object>} Created resolution
 * @throws {ValidationError} If data is invalid
 * @throws {AuthorizationError} If user lacks permissions
 * @audit Logs CREATE_RESOLUTION action
 */
async function createResolution(data) {
  // Implementation
}
```

### Naming Conventions

**Variables and Functions:**
```javascript
// camelCase
const userName = 'John Doe';
function getUserById(id) { }
```

**Constants:**
```javascript
// UPPER_SNAKE_CASE
const MAX_LOGIN_ATTEMPTS = 5;
const JWT_SECRET = process.env.JWT_SECRET;
```

**React Components:**
```javascript
// PascalCase
function ResolutionList() { }
function UserProfile() { }
```

**Files:**
```
// Components: PascalCase
UserProfile.jsx
ResolutionList.jsx

// Utilities/Services: camelCase
authService.js
dateUtils.js

// Constants: lowercase
constants.js
config.js
```

### Security Requirements

**ALWAYS:**
- ✅ Validate all user inputs (server-side)
- ✅ Use parameterized queries (Prisma ORM)
- ✅ Implement authentication/authorization checks
- ✅ Add audit logging for sensitive operations
- ✅ Handle errors properly (no sensitive info in errors)
- ✅ Use environment variables for secrets
- ✅ Follow OWASP Top 10 guidelines

**NEVER:**
- ❌ Commit secrets to version control
- ❌ Concatenate user input into SQL queries
- ❌ Skip input validation
- ❌ Expose sensitive data in API responses
- ❌ Use HTTP for sensitive data (always HTTPS)

### Accessibility (WCAG 2.1 AA)

**Requirements:**
- ✅ Semantic HTML (use correct elements)
- ✅ ARIA labels where needed
- ✅ Keyboard navigation support
- ✅ Color contrast ratios (≥4.5:1)
- ✅ Focus indicators visible
- ✅ Form labels and error messages
- ✅ Screen reader compatibility

### Bilingual Support (SW/EN)

**All user-facing text must support both languages:**

```javascript
// translations/en.json
{
  "common": {
    "save": "Save",
    "cancel": "Cancel"
  }
}

// translations/sw.json
{
  "common": {
    "save": "Hifadhi",
    "cancel": "Ghairi"
  }
}

// Usage in React
import { useTranslation } from 'react-i18next';

function MyComponent() {
  const { t } = useTranslation();
  return <button>{t('common.save')}</button>;
}
```

---

## Testing Requirements

### Test Coverage

**Minimum Requirements:**
- Unit tests: ≥80% coverage
- Integration tests: Critical paths covered
- E2E tests: Main user workflows

### Running Tests

```bash
# Backend
cd backend
npm test                  # Run all tests
npm run test:watch       # Watch mode
npm run test:coverage    # Coverage report

# Frontend
cd frontend
npm test
npm run test:watch
npm run test:coverage
```

### Writing Tests

**Unit Test Example (Jest):**
```javascript
describe('createResolution', () => {
  it('should create resolution with valid data', async () => {
    const data = {
      description: 'Test resolution',
      type: 'ILIELEKEZA',
      organizationId: 'org-uuid'
    };
    
    const result = await createResolution(data);
    
    expect(result).toHaveProperty('id');
    expect(result.description).toBe(data.description);
  });

  it('should throw error with invalid data', async () => {
    const data = { description: '' }; // Invalid
    
    await expect(createResolution(data)).rejects.toThrow(ValidationError);
  });
});
```

**Integration Test Example (Supertest):**
```javascript
describe('POST /api/v1/resolutions', () => {
  it('should create resolution when authenticated', async () => {
    const response = await request(app)
      .post('/api/v1/resolutions')
      .set('Authorization', `Bearer ${validToken}`)
      .send(validResolutionData);
    
    expect(response.status).toBe(201);
    expect(response.body.success).toBe(true);
  });
});
```

**Component Test Example (React Testing Library):**
```javascript
import { render, screen, fireEvent } from '@testing-library/react';
import ResolutionForm from './ResolutionForm';

describe('ResolutionForm', () => {
  it('should submit form with valid data', async () => {
    const onSubmit = jest.fn();
    render(<ResolutionForm onSubmit={onSubmit} />);
    
    fireEvent.change(screen.getByLabelText('Description'), {
      target: { value: 'Test resolution' }
    });
    
    fireEvent.click(screen.getByText('Submit'));
    
    expect(onSubmit).toHaveBeenCalledWith({
      description: 'Test resolution'
    });
  });
});
```

---

## Pull Request Process

### Before Creating a PR

**Checklist:**
- [ ] Code follows style guidelines (ESLint + Prettier)
- [ ] All tests pass (`npm test`)
- [ ] Test coverage ≥80% for new code
- [ ] JSDoc comments added for public functions
- [ ] Security considerations reviewed
- [ ] Accessibility verified (if UI changes)
- [ ] Bilingual support implemented (if applicable)
- [ ] Documentation updated (if needed)
- [ ] No merge conflicts with `develop` branch
- [ ] Commits follow conventional commit format

### Creating a PR

1. **Push to your fork:**
```bash
git push origin feature/your-feature-name
```

2. **Create pull request on GitHub:**
   - Base: `develop` (not `main`)
   - Title: Clear, descriptive title
   - Description: Use the PR template

3. **PR Description Template:**
```markdown
## Description
Brief description of changes.

## Type of Change
- [ ] Bug fix
- [ ] New feature
- [ ] Documentation update
- [ ] Refactoring
- [ ] Security fix

## Changes Made
- Change 1
- Change 2
- Change 3

## Testing
How were these changes tested?

## Screenshots (if applicable)
Add screenshots for UI changes.

## Checklist
- [ ] Code follows style guidelines
- [ ] Tests added/updated
- [ ] Documentation updated
- [ ] Security reviewed
- [ ] Accessibility verified

## Related Issues
Closes #issue_number
```

### Code Review Process

**What reviewers look for:**
1. **Functionality:** Does it work as intended?
2. **Security:** Any security vulnerabilities?
3. **Performance:** Any performance concerns?
4. **Code Quality:** Clean, maintainable code?
5. **Tests:** Adequate test coverage?
6. **Documentation:** Clear comments and docs?
7. **Accessibility:** WCAG 2.1 AA compliance?
8. **Compliance:** Follows #ComplianceKwanza?

**Review Timeline:**
- Initial review: Within 48 hours
- Follow-up: Within 24 hours after updates

**Approval Requirements:**
- At least 1 approval from maintainers
- All CI checks passing
- No merge conflicts
- No requested changes

### After Approval

1. Maintainer will merge your PR
2. Your contribution will be added to CHANGELOG.md
3. You'll be credited in release notes (if significant)

---

## Issue Reporting

### Bug Reports

**Template:**
```markdown
## Bug Description
Clear description of the bug.

## Steps to Reproduce
1. Step 1
2. Step 2
3. Step 3

## Expected Behavior
What should happen?

## Actual Behavior
What actually happens?

## Environment
- OS: [e.g., Ubuntu 22.04]
- Browser: [e.g., Chrome 120]
- Node version: [e.g., 18.17.0]
- Package version: [e.g., 1.0.0]

## Screenshots
If applicable.

## Additional Context
Any other information.
```

### Feature Requests

**Template:**
```markdown
## Feature Description
Clear description of the feature.

## Problem it Solves
What problem does this feature address?

## Proposed Solution
How should this feature work?

## Alternatives Considered
What other approaches did you consider?

## Additional Context
Any other information.
```

### Security Vulnerabilities

**DO NOT** create public issues for security vulnerabilities.

**Instead:**
- Email: security@gurdianx.com or kapipocostantine@gmail.com
- Follow responsible disclosure policy
- See [SECURITY_POLICY.md](./SECURITY_POLICY.md)

---

## Documentation

### Documentation Updates

When making changes that affect:
- **API:** Update API_DOCUMENTATION.md
- **Database:** Update DATABASE_SCHEMA.md
- **Security:** Update SECURITY_POLICY.md
- **Architecture:** Update TECHNICAL_SPECS.md
- **User features:** Update README.md

### Documentation Style

**Markdown Guidelines:**
- Use headers hierarchically (##, ###, ####)
- Add code blocks with language specifiers
- Use tables for structured data
- Include examples where helpful
- Add links to related documentation

---

## License

By contributing to FollowUpHub, you agree that your contributions will be licensed under the same terms as the project (see [LICENSE](./LICENSE)).

**Copyright:** © 2025 GURDIAN-X - Costantine George Mpanda. All Rights Reserved.

---

## Recognition

Contributors will be recognized in:
- GitHub contributors page
- Release notes (significant contributions)
- CHANGELOG.md (feature additions)

---

## Contact

**Questions or Need Help?**

**Project Owner:** Costantine George Mpanda (GURDIAN-X)  
**Institution:** University of Dodoma (UDOM)  
**Location:** Dodoma, Tanzania 🇹🇿

**Emails:**
- kapipocostantine@gmail.com
- costantine.mpanda@udom.ac.tz
- contact@gurdianx.com

**Phones:**
- +255714755686
- +255752999417

**Repository:** https://github.com/GURDIAN-X/FollowUpHub

---

## Related Documentation

📄 [PROJECT_CHARTER.md](./PROJECT_CHARTER.md) - Strategic framework  
📄 [TECHNICAL_SPECS.md](./TECHNICAL_SPECS.md) - Technical architecture  
📄 [DATABASE_SCHEMA.md](./DATABASE_SCHEMA.md) - Database design  
📄 [API_DOCUMENTATION.md](./API_DOCUMENTATION.md) - API reference  
📄 [SECURITY_POLICY.md](./SECURITY_POLICY.md) - Security policies  
📄 [CHANGELOG.md](./CHANGELOG.md) - Version history  
📄 [README.md](./README.md) - Project overview  

---

**Thank you for contributing to FollowUpHub!** 🙏

**#ComplianceKwanza** 🛡️ | **Made in Tanzania** 🇹🇿

**Timestamp:** 2025-10-18 22:15:41 UTC  
**Version:** 1.0.0

© 2025 GURDIAN-X - Costantine George Mpanda. All Rights Reserved.
