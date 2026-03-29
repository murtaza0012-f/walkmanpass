🔐 WalkmanPass

A zero-knowledge password manager built with the MERN stack — AES-256-GCM encryption, Argon2id hashing, and JWT authentication. Built during the AFEC Hackathon 2026.
📌 What is WalkmanPass?
WalkmanPass is a full-stack password manager where your data is never visible to the server. Every password is encrypted client-side using a key derived from your master password — which we never store or transmit in plain text.
Built by Jacqueline, Murtaza and Julien at the AFEC La Rochelle Hackathon 2026.

✨ Features
🔒 AES-256-GCM encryption — every password encrypted with a unique IV
🧠 Zero-knowledge architecture — the server never sees your passwords in plain text
🔑 Argon2id password hashing — the most secure hashing algorithm available
🪙 JWT authentication — stateless, secure sessions (24h expiry)
🛡️ PBKDF2 key derivation — 100,000 iterations, SHA-256
🔁 Recovery key system — reset your master password without losing your data
📧 Email notifications — welcome email + password reset confirmation
🗂️ Password categories — social, banking, email, shopping, work, other
🔍 Search and filter — instant search across your vault
📋 One-click copy — copy passwords securely without exposing them on screen
🎲 Password generator — generate strong random passwords
✏️ Full CRUD — create, view, edit, delete password entries

🏗️ Architecture
walkmanpass/
├── client/                    # React frontend (Vite)
│   └── src/
│       ├── components/
│       │   ├── PasswordRow.jsx           # Validation password chars 
│       ├── hooks/
│       │   ├── useDashboard.jsx           # To handle passwords
│       ├── pages/
│       │   ├── HomePage.jsx           # Landing page
│       │   ├── AuthPage.jsx           # Login + Register
│       │   ├── DashboardPage.jsx      # Password vault
│       │   └── ForgotPasswordPage.jsx # Recovery flow
│       └── App.jsx
│       └── main.jsx
│       └── index.css
│
└── server/                    # Node.js + Express backend
    ├── config/
    │   └── database.js      # connecting to database
    ├── controllers/
    │   ├── AuthController.js          # Register, Login, Reset
    │   └── PasswordController.js      # CRUD for passwords
    ├── models/
    │   ├── User.js                    # User schema (Mongoose)
    │   └── Password.js                # Password schema (Mongoose)
    ├── middleware/
    │   └── auth.js                    # JWT middleware
    ├── routes/
    │   └── auth.js                    # routes for login
    │   └── passwords.js               # routes for passwords
    └── services/
        ├── cryptoService.js           # AES-256-GCM + PBKDF2
        └── emailService.js            # Nodemailer (Gmail)
    └── server.js                      # server manager


🔐 Security Architecture — How It Works
Understanding the security model is important before deploying.
1. Registration

User chooses a master password
A random encryption salt is generated (32 bytes)
A master key is derived: PBKDF2(masterPassword, encryptionSalt, 100000, 32, SHA-256)
The master password is hashed with Argon2id before storing in the database
A recovery key (64 hex chars) is generated and shown once to the user
The master key is encrypted with the recovery key and stored (AES-256-GCM)

2. Saving a Password

User provides their master password
The encryption key is re-derived: PBKDF2(masterPassword, encryptionSalt, ...)
The password is encrypted: AES-256-GCM(plaintext, derivedKey, randomIV)
The database stores: encryptedPassword + IV + authTag — never the plain text

3. Decrypting a Password

User must re-enter their master password to view any stored password
The key is re-derived, then used to decrypt on-the-fly
The plain text is never persisted anywhere

4. Password Reset (Recovery Key Flow)

User provides their email + recovery key + new master password
The recovery key decrypts the stored master key
All stored passwords are re-encrypted with the new master key
A new recovery key is generated and shown to the user

user


🛠️ Tech Stack
LayerTechnologyFrontendReact 18, Vite, React Router, Tailwind CSSBackendNode.js, Express.jsDatabaseMongoDB AtlasODMMongooseAuthenticationJWT (jsonwebtoken)Password HashingArgon2idEncryptionAES-256-GCM (Node.js crypto)Key DerivationPBKDF2 — SHA-256, 100k iterationsEmailNodemailer + Gmail SMTP

🚀 Local Setup
Prerequisites

Node.js 18+
npm or yarn
A MongoDB Atlas account (free) — see deployment guide below
A Gmail account for email sending
1. Clone the repository
git https://github.com/murtaza0012-f/walkmanpass.git
cd walkmanpass

2. Install dependencies
# Backend
cd server
npm install

# Frontend
cd ../client
npm install

3. Configure environment variables
Create a .env file in the server/ directory:
env# MongoDB
MONGODB_URI=mongodb+srv://<user>:<password>@cluster.mongodb.net/walkmanpass

# JWT
JWT_SECRET=your_very_long_random_secret_here_minimum_32_chars

# Email (Gmail)
EMAIL_USER=yourgmail@gmail.com
EMAIL_PASS=your_gmail_app_password
EMAIL_FROM=WalkmanPass <yourgmail@gmail.com>

# Client URL (for email links)
CLIENT_URL=http://localhost:5173

# Server port
PORT=5000
Create a .env file in the client/ directory:
envVITE_API_URL=http://localhost:5000/api
4. Run the project
# Terminal 1 — Backend
cd server
npm run dev

# Terminal 2 — Frontend
cd client
npm run dev
