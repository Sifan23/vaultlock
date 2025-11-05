# 🔐 VaultLock — Multi-Signature Token Vault on Solana

**VaultLock** is a decentralized multi-signature vault built on the **Solana blockchain**.  
It allows teams to pool SPL tokens and manage withdrawals using programmable dual-authorization rules — eliminating single points of failure and ensuring transparent on-chain fund governance.

---

## 🚀 Overview

| Layer | Description |
|--------|-------------|
| **Backend (Anchor)** | Solana smart contract managing vaults, proposals, and approvals |
| **Frontend (Next.js)** | React-based DApp for vault creation, deposits, and withdrawals |
| **Token Standard** | SPL Tokens |
| **Blockchain** | Solana Devnet |
| **Deployment** | Anchor CLI (backend), Vercel (frontend) |

---

## 🧠 Architecture

```

VaultLock (Monorepo or Two-Repo Setup)
│
├── vaultlock-anchor-backend/      # Solana smart contract (Anchor)
│   ├── programs/
│   │   └── vaultlock/
│   │       ├── src/
│   │       │   ├── lib.rs
│   │       │   ├── instructions/
│   │       │   │   ├── create_vault.rs
│   │       │   │   ├── deposit.rs
│   │       │   │   ├── propose_withdrawal.rs
│   │       │   │   ├── approve_withdrawal.rs
│   │       │   │   └── execute_withdrawal.rs
│   │       │   └── state.rs
│   │       └── Cargo.toml
│   ├── tests/
│   │   └── vaultlock.test.ts
│   ├── Anchor.toml
│   ├── Cargo.toml
│   └── README.md
│
└── vaultlock-frontend-dapp/       # Web application (Next.js + Anchor)
├── .env.local
├── package.json
├── next.config.js
├── tsconfig.json
├── public/
│   └── logo.svg
└── src/
├── lib/
│   ├── idl/
│   │   └── vaultlock.json
│   ├── anchorClient.ts
│   ├── constants.ts
│   └── pda.ts
├── components/
│   ├── Header.tsx
│   ├── VaultCard.tsx
│   └── ProposalList.tsx
├── pages/
│   ├── index.tsx
│   ├── create-vault.tsx
│   ├── deposit.tsx
│   ├── proposals.tsx
│   └── execute.tsx
├── hooks/
│   ├── useVault.ts
│   ├── useWallet.ts
│   └── useProgram.ts
├── context/
│   └── VaultContext.tsx
├── styles/
│   └── globals.css
└── tests/
└── vaultFlow.test.tsx

````

---

## 🦀 Backend — **vaultlock-anchor-backend**

### 📜 Description
The on-chain **Anchor program** handles:
- Vault creation (with two authority keys)
- Token deposits
- Withdrawal proposals
- Dual approval logic
- Secure token transfers (SPL)

### ⚙️ Setup
```bash
cd vaultlock-anchor-backend

# Install Anchor
cargo install --git https://github.com/coral-xyz/anchor avm --locked --force
avm install latest && avm use latest

# Build and test
anchor build
anchor test

# Deploy to Devnet
solana config set --url https://api.devnet.solana.com
anchor deploy
````

After deploying, note:

* 🧩 **Program ID** (printed on deploy)
* 📄 **IDL JSON** (`target/idl/vaultlock.json`)

Send both to the frontend team.

### 🧪 Testing

```bash
anchor test
```

Validates:

* Vault initialization & PDA correctness
* Deposit and withdrawal proposals
* Dual approval enforcement
* Unauthorized access rejection

---

## 💻 Frontend — **vaultlock-frontend-dapp**

### 📜 Description

A **React + Next.js DApp** for interacting with the VaultLock program.
Users can:

* Create new vaults
* Deposit tokens
* Propose withdrawals
* Approve and execute transfers

### ⚙️ Setup

```bash
cd vaultlock-frontend-dapp
npm install
```

Create `.env.local`:

```env
NEXT_PUBLIC_PROGRAM_ID=<YourProgramID>
NEXT_PUBLIC_SOLANA_CLUSTER=https://api.devnet.solana.com
```

Copy the backend’s `vaultlock.json` file:

```
vaultlock-anchor-backend/target/idl/vaultlock.json
→ vaultlock-frontend-dapp/src/lib/idl/vaultlock.json
```

Start development:

```bash
npm run dev
```

Visit [http://localhost:3000](http://localhost:3000)

---

## 🧩 How Backend and Frontend Connect

| Component           | Location                     | Description                               |
| ------------------- | ---------------------------- | ----------------------------------------- |
| **Program ID**      | `.env.local`                 | Solana on-chain program address           |
| **IDL JSON**        | `src/lib/idl/vaultlock.json` | Describes program interface               |
| **Anchor Provider** | `src/lib/anchorClient.ts`    | Configures connection and wallet provider |
| **RPC**             | Solana JSON RPC              | Used by frontend to send instructions     |

---

## 🧱 Folder Structure Explained

### 🔹 Backend (`vaultlock-anchor-backend`)

| Folder/File         | Purpose                                                       |
| ------------------- | ------------------------------------------------------------- |
| `src/instructions/` | Each on-chain instruction (create, deposit, approve, execute) |
| `src/state.rs`      | Defines account structs and PDA logic                         |
| `tests/`            | Anchor + TypeScript tests                                     |
| `migrations/`       | Optional deploy scripts                                       |
| `target/idl/`       | Generated IDL for frontend integration                        |

---

### 🔹 Frontend (`vaultlock-frontend-dapp`)

| Folder/File       | Purpose                                     |
| ----------------- | ------------------------------------------- |
| `src/lib/`        | Core blockchain logic, IDL, constants, PDAs |
| `src/components/` | Reusable React UI components                |
| `src/pages/`      | Next.js pages for each app route            |
| `src/hooks/`      | Custom hooks (wallets, vault data, etc.)    |
| `src/context/`    | Global state management (React Context)     |
| `src/styles/`     | TailwindCSS and global styles               |
| `src/tests/`      | UI and integration tests                    |

---

## 🔄 Team Workflow

| Step                           | Backend Dev                      | Frontend Team                            |
| ------------------------------ | -------------------------------- | ---------------------------------------- |
| **1.** Develop Smart Contract  | Implement & test logic in Anchor | —                                        |
| **2.** Deploy Program          | Deploy to Solana Devnet          | —                                        |
| **3.** Export IDL & Program ID | Send to frontend                 | Receive files                            |
| **4.** Integrate IDL           | —                                | Import into `/src/lib/idl/`              |
| **5.** Build & Test UI         | —                                | Use wallets to create vaults & proposals |
| **6.** Demo & Iterate          | Deploy new versions as needed    | Sync via updated IDL                     |

> 💡 Optional: Host both repos in one GitHub org with issues linked across backend & frontend.

---

## 🧪 Testing and CI/CD

### Backend

* `anchor test` on local validator
* Lint + Cargo checks on CI

### Frontend

* `npm run lint && npm run test`
* Automated Vercel preview builds

### Combined

* End-to-end tests with mock wallets (Jest + @solana/web3.js)
* Devnet deployment verification before final demo

---

## 🧭 Deployment

| Layer        | Platform      | Command         |
| ------------ | ------------- | --------------- |
| **Backend**  | Solana Devnet | `anchor deploy` |
| **Frontend** | Vercel        | `npm run build` |

Set environment variables in Vercel:

```
NEXT_PUBLIC_PROGRAM_ID=<ProgramID>
NEXT_PUBLIC_SOLANA_CLUSTER=https://api.devnet.solana.com
```

---

