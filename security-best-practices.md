# 🔒 Security Best Practices for Web Developers

> Based on OWASP Top 10 + enterprise security patterns
> This is how $1M/year developers build secure applications.

## 🎯 Security Mindset

### What Elite Developers Know:
1. **Never trust user input** - Validate, sanitize, escape
2. **Defense in depth** - Multiple layers of security
3. **Principle of least privilege** - Minimum permissions needed
4. **Security by design** - Not an afterthought
5. **Assume breach** - Design for when (not if) security fails
6. **Keep it simple** - Complex security is broken security

## 🛡️ OWASP Top 10 (2023)

### 1. Broken Access Control
**Problem**: Users can access data/functions they shouldn't
**Examples**: 
- IDOR (Insecure Direct Object References)
- Missing function-level access control
- Insecure default permissions

**Fixes**:
```typescript
// ❌ Bad - No authorization check
app.get('/api/users/:id', async (req, res) => {
  const user = await db.users.find(req.params.id);
  res.json(user);
});

// ✅ Good - Check ownership/permissions
app.get('/api/users/:id', async (req, res) => {
  const user = await db.users.find(req.params.id);
  if (!user || user.id !== req.user.id && !req.user.isAdmin) {
    return res.status(403).json({ error: 'Forbidden' });
  }
  res.json(user);
});
```

### 2. Cryptographic Failures
**Problem**: Sensitive data not properly protected
**Examples**:
- Storing passwords in plain text
- Using weak encryption algorithms
- Transmitting data over HTTP

**Fixes**:
```typescript
// ❌ Bad - Plain text password
const user = { password: 'secret123' };

// ✅ Good - Hashed password
import bcrypt from 'bcrypt';
const hashedPassword = await bcrypt.hash('secret123', 12);

// ❌ Bad - HTTP
fetch('http://api.example.com/data');

// ✅ Good - HTTPS
fetch('https://api.example.com/data');
```

### 3. Injection
**Problem**: Untrusted data sent as part of command/query
**Examples**:
- SQL injection
- NoSQL injection
- Command injection
- LDAP injection

**Fixes**:
```typescript
// ❌ Bad - SQL injection vulnerability
const query = `SELECT * FROM users WHERE email = '${email}'`;

// ✅ Good - Parameterized queries
const query = 'SELECT * FROM users WHERE email = ?';
const result = await db.execute(query, [email]);

// ❌ Bad - Command injection
exec(`ls ${userInput}`);

// ✅ Good - Escape input or use safe APIs
exec(`ls ${escapeShellArg(userInput)}`);
```

### 4. Insecure Design
**Problem**: Missing security controls in architecture
**Examples**:
- No rate limiting
- No audit logging
- No input validation
- No error handling

**Fixes**:
- Threat modeling during design
- Security requirements in user stories
- Security testing in CI/CD
- Regular security reviews

### 5. Security Misconfiguration
**Problem**: Insecure default configurations
**Examples**:
- Default passwords
- Unnecessary features enabled
- Verbose error messages
- Missing security headers

**Fixes**:
```typescript
// Security headers
app.use((req, res, next) => {
  res.setHeader('X-Content-Type-Options', 'nosniff');
  res.setHeader('X-Frame-Options', 'DENY');
  res.setHeader('X-XSS-Protection', '1; mode=block');
  res.setHeader('Strict-Transport-Security', 'max-age=31536000');
  res.setHeader('Content-Security-Policy', "default-src 'self'");
  next();
});
```

### 6. Vulnerable and Outdated Components
**Problem**: Using libraries with known vulnerabilities
**Examples**:
- Old versions of dependencies
- Unpatched frameworks
- Abandoned packages

**Fixes**:
- Regular dependency updates
- Automated vulnerability scanning
- Use `npm audit`, `snyk`, `dependabot`
- Monitor security advisories

### 7. Identification and Authentication Failures
**Problem**: Weak authentication mechanisms
**Examples**:
- Weak passwords allowed
- No MFA
- Session fixation
- Credential stuffing

**Fixes**:
```typescript
// Password requirements
const passwordPolicy = {
  minLength: 12,
  requireUppercase: true,
  requireLowercase: true,
  requireNumbers: true,
  requireSpecialChars: true,
  checkBreachedPasswords: true // Have I Been Pwned API
};

// Session security
app.use(session({
  secret: process.env.SESSION_SECRET,
  resave: false,
  saveUninitialized: false,
  cookie: {
    secure: true, // HTTPS only
    httpOnly: true, // No JS access
    sameSite: 'strict', // CSRF protection
    maxAge: 24 * 60 * 60 * 1000 // 24 hours
  }
}));
```

### 8. Software and Data Integrity Failures
**Problem**: Code/data integrity not verified
**Examples**:
- Unsigned updates
- Insecure deserialization
- CI/CD pipeline compromise

**Fixes**:
- Code signing
- Verify checksums
- Secure CI/CD pipelines
- Input validation for deserialization

### 9. Security Logging and Monitoring Failures
**Problem**: Insufficient logging/monitoring
**Examples**:
- No audit logs
- No alerting on security events
- Logs not monitored

**Fixes**:
```typescript
// Security logging
const securityLogger = winston.createLogger({
  level: 'info',
  format: winston.format.json(),
  transports: [
    new winston.transports.File({ filename: 'security.log' })
  ]
});

// Log security events
securityLogger.info('Login attempt', {
  userId: user.id,
  ip: req.ip,
  timestamp: new Date().toISOString(),
  success: true
});
```

### 10. Server-Side Request Forgery (SSRF)
**Problem**: Server makes requests to unintended locations
**Examples**:
- User-controlled URLs in requests
- Internal service exposure
- Cloud metadata access

**Fixes**:
```typescript
// ❌ Bad - User controls URL
const response = await fetch(req.body.url);

// ✅ Good - Validate and restrict URLs
const allowedDomains = ['api.example.com', 'cdn.example.com'];
const url = new URL(req.body.url);
if (!allowedDomains.includes(url.hostname)) {
  throw new Error('Invalid URL domain');
}
const response = await fetch(url.toString());
```

## 🔐 Authentication & Authorization

### JWT Best Practices:
```typescript
// Token creation
const token = jwt.sign(
  { userId: user.id, role: user.role },
  process.env.JWT_SECRET,
  {
    expiresIn: '15m', // Short-lived
    issuer: 'your-app',
    audience: 'your-app-users'
  }
);

// Token verification
try {
  const decoded = jwt.verify(token, process.env.JWT_SECRET, {
    issuer: 'your-app',
    audience: 'your-app-users',
    algorithms: ['HS256'] // Explicit algorithm
  });
} catch (err) {
  // Invalid token
}
```

### OAuth 2.0 Flow:
1. User clicks "Login with X"
2. Redirect to provider authorization
3. User approves
4. Provider redirects back with code
5. Exchange code for access token
6. Use token to access user data

## 🛡️ Input Validation

### Validation Rules:
```typescript
// Email validation
const emailSchema = z.string().email().max(255);

// Password validation
const passwordSchema = z.string()
  .min(12)
  .regex(/[A-Z]/, 'Must contain uppercase')
  .regex(/[a-z]/, 'Must contain lowercase')
  .regex(/[0-9]/, 'Must contain number')
  .regex(/[^A-Za-z0-9]/, 'Must contain special char');

// Sanitize HTML input
import DOMPurify from 'dompurify';
const sanitized = DOMPurify.sanitize(userInput);

// Validate file uploads
const fileSchema = z.object({
  mimetype: z.enum(['image/jpeg', 'image/png', 'image/gif']),
  size: z.number().max(5 * 1024 * 1024) // 5MB max
});
```

## 📊 Security Checklist

### Pre-Deployment:
- [ ] Input validation on all endpoints
- [ ] Authentication on protected routes
- [ ] Authorization checks on all data access
- [ ] HTTPS enabled
- [ ] Security headers configured
- [ ] Dependencies up to date
- [ ] Environment variables secured
- [ ] Error messages don't leak info
- [ ] Rate limiting enabled
- [ ] Logging configured

### Post-Deployment:
- [ ] Monitor logs for security events
- [ ] Regular vulnerability scans
- [ ] Dependency updates
- [ ] Penetration testing
- [ ] Incident response plan
- [ ] Backup strategy
- [ ] Disaster recovery testing

---

*Last updated: 2026-05-16*
*Source: OWASP Top 10 + enterprise security patterns*
