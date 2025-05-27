# 🧩 Sequence Diagram – User Registration Flow

This project contains a Mermaid-based sequence diagram that models the full user registration flow. The diagram includes various scenarios such as language and country selection, email or Google registration, CAPTCHA validation, and code verification.

## 👥 Participants:

- **User** – The end user interacting with the system.
- **Frontend** – The UI where users input data and receive feedback.
- **Backend** – Handles business logic, validation, and coordination.
- **Database** – Stores user data and verification codes.

## 🔁 Flow Covered:

1. Language and country selection (for foreign users).
2. Choosing registration method (Email or Google).
3. Handling Google registration errors and fallback.
4. Email-based registration:
   - Filling in and validating email
   - CAPTCHA validation
   - Submitting registration data
5. Sending registration or login code depending on user status.
6. Code verification:
   - Handling expired or invalid codes
   - Sending welcome message on success
   - Allowing the user to retry or change method
7. Final login after successful registration.

---

## ▶️ How to Preview the Diagram in VS Code

To view the Mermaid sequence diagram directly in VS Code:

### 1. 📦 Install Extension

Install the **“Markdown Preview Mermaid Support”** extension from the VS Code Marketplace:

- Open Extensions panel (`Ctrl+Shift+X`)
- Search: `Markdown Preview Mermaid Support`
- Click **Install**

### 2. 📝 Open the Diagram File

- Create or open a file named `diagram.md` (or similar)
- Make sure your Mermaid code is enclosed in:

  \`\`\`mermaid  
   sequenceDiagram  
   ...your diagram...  
   \`\`\`

### 3. 👁️ Preview It

- Press `Ctrl+Shift+V` (or `Cmd+Shift+V` on Mac) to open the **Markdown preview**
- Or right-click the file and choose **“Open Preview to the Side”**

You should now see the fully rendered sequence diagram in real time!

---

## 📎 Notes

- This approach is lightweight and requires no external tools or build steps.
- The Mermaid syntax can be easily extended with other diagram types (flowcharts, class diagrams, etc.).
