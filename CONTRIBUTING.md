# Contributing to Hospital Patient Management System

Thank you for your interest in contributing! This document provides guidelines and instructions for contributing to this project.

## Code of Conduct

We are committed to providing a welcoming and inspiring community. Please read and follow our code of conduct:

- Be respectful and inclusive
- Focus on what is best for the community
- Show empathy and kindness to other community members
- Be open to constructive feedback

## Getting Started

### Prerequisites

- Node.js v16 or higher
- npm v7 or higher
- IBM DB2 database access
- Git

### Setting Up Development Environment

1. **Fork the Repository**
   ```bash
   Click "Fork" button on GitHub
   ```

2. **Clone Your Fork**
   ```bash
   git clone https://github.com/YOUR_USERNAME/HospitalMangement-main.git
   cd HospitalMangement-main
   ```

3. **Add Upstream Remote**
   ```bash
   git remote add upstream https://github.com/ORIGINAL_OWNER/HospitalMangement-main.git
   ```

4. **Create Development Branch**
   ```bash
   git checkout -b feature/your-feature-name
   ```

5. **Install Dependencies**
   ```bash
   cd backend && npm install
   cd ../frontend && npm install
   ```

6. **Configure Environment**
   ```bash
   cp .env.example .env
   # Update .env with your database credentials
   ```

## Development Workflow

### Branch Naming Conventions

- Feature: `feature/short-description`
- Bug fix: `bugfix/issue-description`
- Documentation: `docs/what-you-changed`
- Testing: `test/what-you-tested`

### Code Style Guidelines

#### JavaScript/React

- Use ES6+ syntax
- Use meaningful variable names
- Follow existing code patterns
- Add comments for complex logic
- Maximum line length: 100 characters

Example:
```javascript
// Good
const getUserName = (user) => {
  if (!user) return 'Anonymous';
  return user.name.trim();
};

// Bad
const gUN = (u) => !u ? 'Anon' : u.n.trim();
```

#### Database Operations

- Use parameterized queries to prevent SQL injection
- Add error handling for all database operations
- Log database errors properly
- Use transactions for related operations

```javascript
// Good
const query = 'SELECT * FROM PATIENT_MASTER WHERE AADHAR_NO = ?';
db.query(query, [aadharNo], (err, result) => {
  if (err) {
    console.error('Database error:', err);
    return res.status(500).json({ error: 'Database query failed' });
  }
  // Process result
});

// Bad
const query = `SELECT * FROM PATIENT_MASTER WHERE AADHAR_NO = '${aadharNo}'`;
```

#### React Components

- Use functional components with hooks
- Keep components small and focused
- Use meaningful prop names
- Document complex props with PropTypes
- Extract reusable logic into custom hooks

```javascript
// Good
const PatientCard = ({ patient, onSelect }) => {
  const { name, aadharNo, department } = patient;
  
  return (
    <div onClick={() => onSelect(aadharNo)}>
      {name} - {department}
    </div>
  );
};

// Bad
const PC = ({ p, o }) => (
  <div onClick={() => o(p.a)}>
    {p.n} - {p.d}
  </div>
);
```

### Commit Message Guidelines

Follow conventional commits format:

```
type(scope): subject

body (optional)

footer (optional)
```

**Types:**
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation changes
- `style`: Code style changes (formatting, etc.)
- `refactor`: Code refactoring
- `test`: Adding or updating tests
- `chore`: Build process, dependencies, etc.

**Examples:**
```bash
git commit -m "feat(queue): add real-time notifications"
git commit -m "fix(upload): resolve CSV parsing error with special characters"
git commit -m "docs(readme): update installation instructions"
git commit -m "refactor(auth): extract validation logic to utils"
```

## Testing

### Running Tests

```bash
# Backend tests
cd backend
npm test

# Frontend tests
cd ../frontend
npm test
```

### Test Coverage

- Aim for at least 80% code coverage
- Write tests for:
  - API endpoints
  - Database operations
  - Utility functions
  - React components

### Types of Tests to Add

1. **Unit Tests**: Test individual functions
2. **Integration Tests**: Test module interactions
3. **E2E Tests**: Test complete workflows

## Making Changes

### 1. Create Feature Branch

```bash
git checkout -b feature/your-feature
```

### 2. Make Changes

- Keep changes focused and minimal
- Update related documentation
- Add tests for new features

### 3. Test Your Changes

```bash
# Backend
cd backend
npm test
npm start

# Frontend (in another terminal)
cd frontend
npm test
npm run dev
```

### 4. Commit Changes

```bash
git add .
git commit -m "feat(feature): add new functionality"
```

### 5. Push to Your Fork

```bash
git push origin feature/your-feature
```

### 6. Create Pull Request

- Go to GitHub and create a PR
- Write clear description of changes
- Reference related issues
- Include before/after screenshots if UI changes
- Wait for review and CI checks to pass

## Pull Request Process

### PR Title Format

```
[Type] Brief description

Examples:
[Feature] Add real-time queue notifications
[Fix] Correct Aadhar validation regex
[Docs] Update database schema documentation
```

### PR Description Template

```markdown
## Description
Brief description of changes

## Type of Change
- [ ] New feature
- [ ] Bug fix
- [ ] Documentation update
- [ ] Performance improvement

## Related Issue
Closes #123

## Testing
Describe how you tested changes:
- [ ] Tested locally
- [ ] Added unit tests
- [ ] Tested database operations

## Screenshots (if applicable)
Add before/after screenshots

## Checklist
- [ ] Code follows style guidelines
- [ ] Comments added for complex logic
- [ ] Documentation updated
- [ ] Tests added/updated
- [ ] No breaking changes
```

### Review Process

1. **Code Review**: At least 1 maintainer review required
2. **Tests**: All tests must pass
3. **Linting**: No lint errors
4. **Conflicts**: Must be resolved before merging

### Common Review Feedback

- **Code Style**: Ensure consistent with project style
- **Performance**: Avoid inefficient database queries
- **Security**: Check for SQL injection, XSS vulnerabilities
- **Testing**: Ensure adequate test coverage
- **Documentation**: Update relevant docs

## Issues

### Reporting Bugs

Use the bug report template:

```markdown
## Description
Clear description of the bug

## Steps to Reproduce
1. Do this
2. Then this
3. Error occurs

## Expected Behavior
What should happen

## Actual Behavior
What actually happened

## Screenshots
If applicable

## Environment
- OS: Windows/Linux/Mac
- Node.js version: 16.x
- Browser: Chrome/Firefox
```

### Feature Requests

Use the feature request template:

```markdown
## Description
Clear description of requested feature

## Use Case
Why is this feature needed?

## Proposed Solution
How should it work?

## Alternative Solutions
Other approaches considered
```

## Documentation

### Updating README

When adding features:
1. Update API documentation
2. Add new endpoints/features to TOC
3. Include code examples
4. Update troubleshooting if needed

### Adding New Pages/Components

Create documentation in comments:

```javascript
/**
 * PatientCard Component
 * 
 * Displays a patient's basic information
 * 
 * @param {Object} patient - Patient data object
 * @param {string} patient.name - Patient full name
 * @param {string} patient.aadharNo - 12-digit Aadhar number
 * @param {string} patient.department - Department visited
 * @param {Function} onSelect - Callback when card is clicked
 */
const PatientCard = ({ patient, onSelect }) => {
  // Component code
};
```

## Performance Considerations

- Avoid N+1 database queries
- Use database indexes for frequently searched fields
- Implement pagination for large datasets
- Optimize React components (useMemo, useCallback)
- Use lazy loading for images/components

## Security Checklist

Before submitting:
- [ ] No hardcoded credentials
- [ ] Input validation implemented
- [ ] SQL injection prevented
- [ ] XSS prevention in place
- [ ] CORS properly configured
- [ ] Error messages don't expose sensitive info

## Getting Help

- **Questions**: Open a discussion in Issues
- **Bug Reports**: Use bug report template
- **General Help**: Check existing documentation
- **Contact**: Mention @maintainer in issues

## Recognition

Contributors will be recognized in:
- CONTRIBUTORS.md file
- GitHub contributors page
- Release notes for major contributions

## Legal

By submitting a pull request, you agree that:
1. Your code can be used under the project's license
2. You have the right to submit the code
3. The code doesn't violate any laws or existing copyrights

## Thank You

Thank you for contributing to make this project better! Your efforts help improve healthcare management systems for everyone.

---

**Questions?** Feel free to open an issue or contact the maintainers.
