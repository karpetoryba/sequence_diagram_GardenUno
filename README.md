# 🔐 User Authentication Flow - Sequence Diagram

[![Mermaid](https://img.shields.io/badge/Mermaid-Sequence%20Diagram-ff6b6b)](https://mermaid-js.github.io/mermaid/)
[![VS Code](https://img.shields.io/badge/VS%20Code-Ready-007ACC)](https://code.visualstudio.com/)

This project contains a comprehensive Mermaid-based sequence diagram that models the complete user authentication and registration flow. The diagram covers session management, country verification, email-based authentication, and intelligent user detection.

## 🏗️ Architecture Overview

The system implements a modern, secure authentication flow with the following key features:

- **Session-based authentication** with automatic session checking
- **Country verification** with localization support
- **Unified email authentication** (handles both login and registration)
- **CAPTCHA protection** against automated attacks
- **Code verification** with expiration handling
- **Intelligent user detection** (automatic login vs registration)

## 👥 System Participants

| Component    | Responsibility                                      |
| ------------ | --------------------------------------------------- |
| **User**     | End user interacting with the application           |
| **Frontend** | React/Vue/Angular client handling UI and state      |
| **Backend**  | REST API server managing business logic             |
| **Database** | PostgreSQL/MySQL storing users, sessions, and codes |

## 🔄 Complete Authentication Flow

### 1. **Application Startup & Session Check**

- Automatic session validation on app launch
- Redirects to dashboard if valid session exists
- Prompts for authentication if session expired

### 2. **Country Verification & Localization**

- User selects their country
- System validates country availability
- Interface language automatically adjusted (e.g., French for France)

### 3. **Email Authentication Process**

- User enters email address and completes CAPTCHA
- System validates email format and CAPTCHA response
- Generates verification code (login or registration)

### 4. **Code Verification**

- User enters received verification code
- System validates code and checks expiration
- Handles invalid/expired codes with retry options

### 5. **Smart User Detection**

- **Existing User**: Immediate login with session creation
- **New User**: Account creation + welcome email + session creation
- Both paths lead to authenticated dashboard access

## 🛠️ VS Code Setup Instructions

### Prerequisites

```bash
# Install VS Code extensions
code --install-extension bierner.markdown-mermaid
```

### 📦 Required Extensions

1. **Markdown Preview Mermaid Support**

   - Extension ID: `bierner.markdown-mermaid`
   - Enables Mermaid rendering in markdown preview

2. **Mermaid Markdown Syntax Highlighting** (Optional)
   - Extension ID: `bpruitt-goddard.mermaid-markdown-syntax-highlighting`
   - Provides syntax highlighting for Mermaid code blocks

### 👁️ Preview Methods

#### Method 1: Markdown Preview

1. Open `auth-flow.md` or any file containing Mermaid diagrams
2. Press `Ctrl+Shift+V` (Windows/Linux) or `Cmd+Shift+V` (Mac)
3. The diagram will render automatically in the preview pane

#### Method 2: Side-by-Side Preview

1. Right-click on the markdown file
2. Select **"Open Preview to the Side"**
3. Edit and preview simultaneously

#### Method 3: Command Palette

1. Press `Ctrl+Shift+P` (Windows/Linux) or `Cmd+Shift+P` (Mac)
2. Type "Markdown: Open Preview"
3. Select the appropriate option

## 📋 API Endpoints

The diagram references the following REST API endpoints:

```http
GET    /api/session              # Check existing session
POST   /api/verify-country       # Validate country access
POST   /api/user                 # Email & CAPTCHA validation
POST   /api/auth/check-code      # Verify authentication code
POST   /api/auth/verify-user     # Check user existence
```

## 🔧 Technical Implementation

### Database Schema (Simplified)

```sql
-- Users table
users (id, email, active, created_at)

-- Sessions table
sessions (id, user_id, session_token, expires_at)

-- Verification codes
login_codes (id, email, code, expires_at)
registration_codes (id, email, code, expires_at)
```

### Frontend State Management

```javascript
// Session token handling
localStorage.setItem("sessionToken", token);
axios.defaults.headers.common["Authorization"] = `Bearer ${token}`;
```

## 🚀 Features Highlights

- ✅ **Zero-friction UX**: Single email input for both login and registration
- ✅ **Security-first**: CAPTCHA, code expiration, session management
- ✅ **Internationalization**: Dynamic language switching based on country
- ✅ **Error handling**: Comprehensive error states and recovery options
- ✅ **Production-ready**: Proper session lifecycle and database design

## 📊 Diagram Benefits

- **Visual Documentation**: Clear understanding of the entire flow
- **Developer Onboarding**: New team members can quickly grasp the system
- **API Contract**: Defines exact request/response structures
- **Testing Guide**: Identifies all test scenarios and edge cases
- **Architecture Review**: Enables system design discussions

## 🔗 Related Documentation

- [API Documentation](./docs/api-endpoints.md)
- [Frontend Implementation Guide](./docs/frontend-setup.md)
- [Database Schema](./docs/database-schema.md)
- [Security Considerations](./docs/security.md)

## 📚 Mermaid Resources

- [Mermaid Official Documentation](https://mermaid-js.github.io/mermaid/)
- [Sequence Diagram Syntax](https://mermaid-js.github.io/mermaid/#/sequenceDiagram)
- [Mermaid Live Editor](https://mermaid.live/)

---

**Note**: This sequence diagram represents a production-ready authentication system with modern security practices and user experience considerations.
