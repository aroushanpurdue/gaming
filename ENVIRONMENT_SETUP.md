# Development Environment Setup

Complete guide to set up your development environment for the Universal Wallet System.

---

## Prerequisites

### Required Software

**Node.js & npm:**
```bash
# Install Node.js 18+ LTS
# Download from: https://nodejs.org/

# Verify installation
node --version  # Should be v18.x or higher
npm --version   # Should be 9.x or higher
```

**Unity:**
```
Version: Unity 2021.3 LTS or higher
Download: https://unity.com/download

Required Modules:
- Android Build Support
- iOS Build Support (if targeting iOS)
```

**Solidity Development:**
```bash
# Install Hardhat globally
npm install -g hardhat

# Verify
hardhat --version
```

**Rust (for Solana):**
```bash
# Install Rust
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

# Install Solana CLI
sh -c "$(curl -sSfL https://release.solana.com/stable/install)"

# Install Anchor
cargo install --git https://github.com/coral-xyz/anchor avm --locked --force
avm install latest
avm use latest

# Verify
solana --version
anchor --version
```

**MongoDB:**
```bash
# Option 1: MongoDB Atlas (Cloud - Recommended)
# Sign up at: https://www.mongodb.com/cloud/atlas

# Option 2: Local Installation
# Download from: https://www.mongodb.com/try/download/community
```

**Git:**
```bash
# Verify Git is installed
git --version

# Clone the project
git clone <repository-url>
cd UniversalWalletSystem
```

---

## Day 1 Setup Checklist

### ✅ 1. Unity Gaming Services Account

**Steps:**
1. Go to https://dashboard.unity3d.com/
2. Sign in or create Unity ID
3. Create new project: "Universal Wallet System"
4. Enable Authentication service
5. Configure sign-in methods:
   - Enable Google Play Games
   - Enable Email/Password
   - Enable Facebook (optional)
   - Enable Anonymous
6. Get Project ID (save for later)

**Save These:**
```
Unity Project ID: ________________
Unity Organization ID: ________________
```

### ✅ 2. Blockchain Testnet Accounts

**Somnia Testnet:**
1. Visit https://testnet.somnia.network/
2. Get testnet STT from faucet
3. Create wallet for gas pool:
   ```
   Address: ________________
   Private Key: ________________ (KEEP SECURE)
   ```

**Sei Testnet (Arctic-1):**
1. Visit https://app.sei.io/faucet
2. Get testnet SEI
3. Create wallet for gas pool:
   ```
   Address: ________________
   Private Key: ________________ (KEEP SECURE)
   ```

**Solana Devnet:**
```bash
# Generate new keypair
solana-keygen new --outfile ~/.config/solana/devnet-wallet.json

# Get your address
solana address

# Get devnet SOL (airdrop)
solana airdrop 2

# Save:
# Pubkey: ________________
# Keypair location: ~/.config/solana/devnet-wallet.json
```

### ✅ 3. MongoDB Setup

**Option A: MongoDB Atlas (Recommended)**
1. Create account at https://www.mongodb.com/cloud/atlas
2. Create free cluster
3. Create database user
4. Whitelist your IP (or 0.0.0.0/0 for dev)
5. Get connection string:
   ```
   mongodb+srv://username:password@cluster.mongodb.net/
   ```

**Option B: Local MongoDB**
```bash
# Start MongoDB service
mongod --dbpath /path/to/data

# Connection string:
mongodb://localhost:27017/universal_wallet
```

### ✅ 4. RPC Providers (Optional - for production)

**For better reliability, sign up for:**

**Somnia:**
- Use public RPC for now: https://rpc.somnia.network

**Sei:**
- Use public RPC: https://evm-rpc.sei-apis.com

**Solana:**
- QuickNode: https://www.quicknode.com/
- Alchemy: https://www.alchemy.com/
- Or use public: https://api.mainnet-beta.solana.com

### ✅ 5. Dune Analytics Account

1. Sign up at https://dune.com/
2. Create account (free tier is fine)
3. Verify email
4. Save credentials:
   ```
   Dune Username: ________________
   ```

---

## Project Installation

### 1. Install Smart Contract Dependencies

```bash
cd SmartContracts
npm init -y
npm install --save-dev hardhat @nomicfoundation/hardhat-toolbox
npm install @openzeppelin/contracts dotenv

# Initialize Hardhat
npx hardhat init
# Select: "Create a TypeScript project"
```

### 2. Install Solana Program Dependencies

```bash
cd ../SolanaProgram
anchor init game_wallet
cd game_wallet

# Dependencies will be in Cargo.toml
```

### 3. Install Backend Dependencies

```bash
cd ../../Backend
npm init -y
npm install express mongoose dotenv cors
npm install @solana/web3.js ethers
npm install bull redis
npm install --save-dev @types/node @types/express typescript nodemon
```

### 4. Setup Unity Project

```bash
# Open Unity Hub
# Create New Project
# Name: UniversalWalletTest
# Template: 3D Core
# Version: 2021.3 LTS

# Install Unity packages via Package Manager:
# 1. Window -> Package Manager
# 2. Add package from git URL:
#    - com.unity.services.core
#    - com.unity.services.authentication
#    - com.unity.services.cloudsave
```

---

## Environment Variables Setup

### Backend .env File

Create `Backend/.env`:
```env
# Node Environment
NODE_ENV=development
PORT=3000

# MongoDB
MONGODB_URI=mongodb+srv://username:password@cluster.mongodb.net/universal_wallet

# Blockchain RPCs
SOMNIA_RPC_URL=https://rpc.testnet.somnia.network
SEI_RPC_URL=https://evm-rpc-testnet.sei-apis.com
SOLANA_RPC_URL=https://api.devnet.solana.com

# Gas Pool Wallets (Testnet)
SOMNIA_GAS_POOL_PRIVATE_KEY=your_private_key_here
SEI_GAS_POOL_PRIVATE_KEY=your_private_key_here
SOLANA_FEE_PAYER_KEYPAIR_PATH=/path/to/devnet-wallet.json

# Wallet Derivation (KEEP SECRET!)
WALLET_DERIVATION_SALT=your_random_256_bit_hex_string_here

# Unity Gaming Services
UGS_PROJECT_ID=your_unity_project_id
UGS_ENVIRONMENT=production

# Security
JWT_SECRET=your_jwt_secret_here
ENCRYPTION_KEY=your_32_byte_hex_key_here

# Rate Limiting
MAX_TX_PER_MINUTE=10
MAX_TX_PER_HOUR=100

# Monitoring (Optional)
SENTRY_DSN=your_sentry_dsn_here
```

### Smart Contracts .env File

Create `SmartContracts/.env`:
```env
# Testnet Private Keys
SOMNIA_TESTNET_PRIVATE_KEY=your_private_key_here
SEI_TESTNET_PRIVATE_KEY=your_private_key_here

# RPC URLs
SOMNIA_TESTNET_RPC=https://rpc.testnet.somnia.network
SEI_TESTNET_RPC=https://evm-rpc-testnet.sei-apis.com

# Mainnet (for Day 8)
SOMNIA_MAINNET_PRIVATE_KEY=your_mainnet_private_key_here
SEI_MAINNET_PRIVATE_KEY=your_mainnet_private_key_here
SOMNIA_MAINNET_RPC=https://rpc.somnia.network
SEI_MAINNET_RPC=https://evm-rpc.sei-apis.com

# Block Explorers (for verification)
SOMNIA_EXPLORER_API_KEY=your_api_key_if_available
SEI_EXPLORER_API_KEY=your_api_key_if_available
```

### Unity Project Settings

In Unity Editor:
1. Edit -> Project Settings -> Services
2. Link project to UGS Project ID
3. Configure Authentication providers

---

## Verification

### Test Everything Works

**Test Smart Contract Setup:**
```bash
cd SmartContracts
npx hardhat compile
# Should compile successfully
```

**Test Solana Setup:**
```bash
cd ../SolanaProgram/game_wallet
anchor build
# Should build successfully
```

**Test Backend:**
```bash
cd ../../Backend
npm run dev
# Should start server on port 3000
```

**Test Unity:**
- Open Unity project
- Window -> General -> Services
- Should show linked to UGS
- Authentication tab should show configured methods

**Test Database Connection:**
```bash
cd Backend
node -e "require('mongoose').connect(process.env.MONGODB_URI).then(() => console.log('DB Connected!')).catch(e => console.error(e))"
# Should print "DB Connected!"
```

---

## Troubleshooting

**Issue: Hardhat not compiling**
```bash
# Clear cache
npx hardhat clean
rm -rf artifacts cache
npx hardhat compile
```

**Issue: Anchor build fails**
```bash
# Update Anchor
avm install latest
avm use latest

# Clean and rebuild
anchor clean
anchor build
```

**Issue: MongoDB connection fails**
- Check IP whitelist in MongoDB Atlas
- Verify credentials in .env
- Test connection string

**Issue: Unity UGS not linking**
- Verify Project ID is correct
- Check internet connection
- Try unlinking and re-linking in Project Settings

**Issue: Solana airdrop fails (rate limit)**
```bash
# Try different RPC
solana config set --url https://api.devnet.solana.com

# Or request from faucet website:
# https://faucet.solana.com/
```

---

## Security Checklist

**Before proceeding:**
- [ ] `.env` files added to `.gitignore`
- [ ] Private keys never committed to git
- [ ] Wallet derivation salt is random & secure
- [ ] MongoDB has authentication enabled
- [ ] Testnet accounts only (no mainnet keys yet)

**Create `.gitignore`:**
```
# .gitignore
.env
.env.*
*.key
**/node_modules/
.DS_Store
*.log
artifacts/
cache/
target/
.anchor/
```

---

## Next Steps

Once environment is set up:
1. ✅ Verify all checklist items above
2. ✅ Run all verification tests
3. ✅ Save all credentials securely
4. 🚀 Proceed to Day 2: Smart Contract Development

---

**Environment Setup Version:** 1.0  
**Last Updated:** April 5, 2025

