# Universal Wallet System - Technical Architecture

**Version:** 1.0  
**Last Updated:** 2025-04-05  
**Build Timeline:** 8 days

---

## 🎯 System Overview

A cross-game wallet system for Unity games supporting:
- **Authentication:** Unity Gaming Services (Google Play, Email, Facebook, Anonymous)
- **EVM Chains:** Somnia Network, Sei Network Mainnet
- **Non-EVM:** Solana
- **Gas Abstraction:** Meta-transactions (EVM) + Fee Payer (Solana)
- **Analytics:** Dune dashboards for all chains

---

## 🏗️ High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                     Unity Game Client                        │
│  ┌──────────────────────────────────────────────────────┐   │
│  │          Unity Gaming Services (UGS)                  │   │
│  │  - Google Play Sign-In                                │   │
│  │  - Email/Password Auth                                │   │
│  │  - Facebook OAuth                                     │   │
│  │  - Anonymous Sign-In                                  │   │
│  └──────────────────────────────────────────────────────┘   │
│                          ↓                                   │
│  ┌──────────────────────────────────────────────────────┐   │
│  │         Universal Wallet SDK                          │   │
│  │  - WalletDerivationManager (UGS ID → Wallets)         │   │
│  │  - MultiChainManager (Somnia/Sei/Solana)              │   │
│  │  - TransactionManagers (EVM + Solana)                 │   │
│  └──────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│                    Backend Relay Service                     │
│  ┌──────────────┬──────────────┬──────────────────────┐     │
│  │ EVM Relay    │ Solana Relay │ UGS Verification     │     │
│  │ (Meta-tx)    │ (Fee Payer)  │ (Player ID Auth)     │     │
│  └──────────────┴──────────────┴──────────────────────┘     │
│  ┌──────────────────────────────────────────────────────┐   │
│  │         Gas Pool Management                           │   │
│  │  - Monitor balances (Somnia, Sei, Solana)             │   │
│  │  - Auto-refill alerts                                 │   │
│  │  - Transaction queue & retry logic                    │   │
│  └──────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│                      Blockchain Layer                        │
│  ┌──────────────┬──────────────┬──────────────────────┐     │
│  │  Somnia      │  Sei         │  Solana              │     │
│  │  Network     │  Network     │  Mainnet             │     │
│  ├──────────────┼──────────────┼──────────────────────┤     │
│  │ GameWallet   │ GameWallet   │ game_wallet          │     │
│  │ .sol         │ .sol         │ program (.rs)        │     │
│  └──────────────┴──────────────┴──────────────────────┘     │
└─────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────┐
│                    Analytics Layer                           │
│  ┌──────────────────────────────────────────────────────┐   │
│  │              Dune Analytics                           │   │
│  │  - Multi-chain dashboards                             │   │
│  │  - Real-time metrics (DAU, transactions, gas)         │   │
│  │  - UGS sign-in method analytics                       │   │
│  └──────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

---

## 🔐 Authentication Flow (Unity Gaming Services)

```
User Opens Game
      ↓
Show Login Options:
  - Continue with Google Play
  - Sign in with Email
  - Continue with Facebook
  - Play as Guest (Anonymous)
      ↓
User Selects Method
      ↓
UGS Authenticates User
      ↓
Returns: UGS Player ID (unique identifier)
      ↓
Wallet Derivation:
  HMAC-SHA256(UGS_Player_ID + Salt)
      ↓
  ├─→ EVM Private Key → 0x742d35... (Somnia & Sei)
  └─→ Solana Keypair → 5Gk7d9X... (Solana)
      ↓
Wallets Stored Encrypted in:
  - Unity Cloud Save (backup)
  - Local Encrypted PlayerPrefs
      ↓
User Can Now Transact on All Chains
```

---

## 🌐 Multi-Chain Support

### EVM Chains (Shared Contract Logic)

**Somnia Network:**
- **Type:** EVM-compatible Layer 1
- **Speed:** ~400ms block time
- **TPS:** 400,000+
- **Gas Cost:** ~$0.0001 per transaction
- **Use Case:** Ultra-fast gaming transactions
- **RPC:** https://rpc.somnia.network
- **Explorer:** https://explorer.somnia.network

**Sei Network Mainnet:**
- **Type:** EVM-compatible (Twin Turbo)
- **Speed:** Sub-second finality
- **TPS:** 28,300+ (parallelized)
- **Gas Cost:** ~$0.0008 per transaction
- **Use Case:** DeFi + Gaming hybrid
- **RPC:** https://evm-rpc.sei-apis.com
- **Explorer:** https://seitrace.com

### Non-EVM Chain

**Solana:**
- **Type:** High-performance blockchain
- **Speed:** 400ms slot time
- **TPS:** 65,000+
- **Gas Cost:** ~$0.00025 per transaction
- **Use Case:** NFT minting, SPL tokens
- **RPC:** https://api.mainnet-beta.solana.com
- **Explorer:** https://solscan.io

---

## 💼 Wallet Derivation (Deterministic)

### Security Model

```javascript
// Input: UGS Player ID
const ugsPlayerId = "abc123xyz789"; // From Unity Gaming Services

// Step 1: Create seed with salt
const salt = process.env.WALLET_DERIVATION_SALT; // Stored securely
const seed = HMAC_SHA256(ugsPlayerId, salt);

// Step 2: Derive EVM wallet (for Somnia & Sei)
const evmPrivateKey = derive_evm_key(seed);
const evmAddress = privateKeyToAddress(evmPrivateKey);
// Result: 0x742d35Cc6634C0532925a3b844Bc454e4438f44e

// Step 3: Derive Solana wallet
const solanaKeypair = Keypair.fromSeed(seed.slice(0, 32));
const solanaPubkey = solanaKeypair.publicKey.toString();
// Result: 5Gk7d9XqJPWz3qKh8QqzKJZvJ3q7v8YqJ9qJ5qJ6qJ7q

// Step 4: Store encrypted backup
const encrypted = encrypt({
  evmPrivateKey,
  solanaKeypair,
  ugsPlayerId
}, userPassword);

// Save to Unity Cloud Save
await CloudSaveService.SaveData("wallet_backup", encrypted);
```

**Benefits:**
- ✅ Same UGS ID = Same wallets across all games
- ✅ No seed phrases for users to manage
- ✅ Cloud backup available
- ✅ Deterministic = Recoverable from UGS ID

---

## 🔄 Transaction Flow

### EVM Chains (Somnia & Sei) - Meta-Transactions

```
1. User Action in Unity
   "Mint NFT" button clicked
        ↓
2. Unity SDK builds transaction data
   const txData = {
     to: contractAddress,
     data: encodeFunctionCall("mintNFT", [tokenId])
   }
        ↓
3. User signs message (offline, free)
   const signature = await wallet.signMessage(txData);
        ↓
4. Send to Backend
   POST /relay-transaction/evm
   {
     chain: "somnia",
     userAddress: "0x742d35...",
     signature: "0xabc123...",
     txData: {...}
   }
        ↓
5. Backend verifies signature
   const isValid = verifySignature(signature, txData, userAddress);
        ↓
6. Backend executes via Smart Contract
   const tx = await GameWallet.executeMetaTx(
     userAddress,
     signature,
     txData
   );
   // Contract pays gas from its balance
        ↓
7. Transaction confirmed on Somnia/Sei
   Event emitted → Indexed by Dune
        ↓
8. Unity receives confirmation
   Show success message to user
```

### Solana - Fee Payer Transactions

```
1. User Action in Unity
   "Transfer SPL Token" clicked
        ↓
2. Unity SDK builds Solana instruction
   const instruction = SystemProgram.transfer({
     fromPubkey: userWallet.publicKey,
     toPubkey: recipientPubkey,
     lamports: amount
   });
        ↓
3. User signs instruction (offline)
   const signature = await userWallet.signTransaction(tx);
        ↓
4. Send to Backend
   POST /relay-transaction/solana
   {
     userPubkey: "5Gk7d9X...",
     signature: "3j2k1m...",
     instruction: {...}
   }
        ↓
5. Backend adds fee payer signature
   tx.partialSign(userKeypair);       // Already signed
   tx.partialSign(feePayerKeypair);   // Backend adds
        ↓
6. Submit to Solana
   const txId = await connection.sendRawTransaction(tx.serialize());
        ↓
7. Confirmation
   Event logged → Indexed by Dune
        ↓
8. Unity receives confirmation
```

---

## 📊 Event Schema for Dune Analytics

### EVM Contracts (Somnia & Sei)

```solidity
event UserRegistered(
    address indexed userWallet,
    string ugsPlayerId,
    string signInMethod,  // "google_play", "email", "facebook", "anonymous"
    uint256 timestamp
);

event TransactionExecuted(
    address indexed user,
    string indexed actionType,  // "mint", "transfer", "upgrade"
    string chain,               // "somnia" or "sei"
    uint256 gasUsed,
    bool success,
    uint256 timestamp
);

event ChainSwitched(
    address indexed user,
    string fromChain,
    string toChain,
    uint256 timestamp
);

event DailySnapshot(
    uint256 indexed date,
    uint256 activeUsers,
    uint256 totalTransactions,
    uint256 totalGasUsed
);
```

### Solana Program

```rust
#[event]
pub struct UserRegistered {
    pub wallet: Pubkey,
    pub ugs_player_id: String,
    pub sign_in_method: String,
    pub timestamp: i64,
}

#[event]
pub struct TransactionExecuted {
    pub user: Pubkey,
    pub action_type: String,
    pub fee_paid: u64,
    pub success: bool,
    pub timestamp: i64,
}
```

---

## 🗄️ Database Schema

### MongoDB Collections

**users:**
```javascript
{
  _id: ObjectId,
  ugsPlayerId: String,          // Unity Gaming Services Player ID
  signInMethod: String,         // "google_play", "email", "facebook", "anonymous"
  evmWallet: String,            // Derived EVM address (0x...)
  solanaWallet: String,         // Derived Solana pubkey (base58)
  encryptedSeed: String,        // Encrypted backup
  cloudSaveBackup: Boolean,     // If backed up to UGS Cloud Save
  createdAt: Date,
  lastActiveAt: Date,
  totalTransactions: Number,
  preferredChain: String        // Last used chain
}
```

**transactions:**
```javascript
{
  _id: ObjectId,
  txId: String,                 // Blockchain transaction hash
  ugsPlayerId: String,
  chain: String,                // "somnia", "sei", "solana"
  chainType: String,            // "evm" or "solana"
  userWallet: String,           // User's wallet address
  action: String,               // "mint", "transfer", "upgrade"
  gasUsed: Number,              // For EVM
  feePaid: Number,              // For Solana (in lamports)
  status: String,               // "pending", "confirmed", "failed"
  blockNumber: Number,
  timestamp: Date,
  metadata: Object              // Additional action-specific data
}
```

**gasPool:**
```javascript
{
  _id: ObjectId,
  chain: String,                // "somnia", "sei", "solana"
  chainType: String,            // "evm" or "solana"
  walletAddress: String,        // Gas pool wallet address
  currentBalance: Number,       // In native currency
  balanceUSD: Number,           // USD equivalent
  totalSpent: Number,           // Lifetime spent
  totalTransactions: Number,    // Lifetime tx count
  lastRefill: Date,
  alertThreshold: Number,       // Alert when below this
  autoRefill: Boolean
}
```

**chainConfig:**
```javascript
{
  _id: ObjectId,
  chainName: String,            // "somnia", "sei", "solana"
  chainType: String,            // "evm" or "solana"
  chainId: Number,              // For EVM chains
  rpcUrl: String,               // Primary RPC
  fallbackRpcUrls: [String],    // Backup RPCs
  contractAddress: String,      // EVM contract or Solana program ID
  explorerUrl: String,
  nativeCurrency: {
    symbol: String,
    decimals: Number
  },
  enabled: Boolean,
  avgGasCost: Number,           // Rolling average
  updatedAt: Date
}
```

---

## 🔒 Security Architecture

### Authentication Security
- **UGS Token Storage:** Encrypted in PlayerPrefs
- **Token Refresh:** Automatic rotation
- **Session Management:** 24-hour timeout, refresh on activity
- **Anonymous Protection:** Prompt to convert before uninstall

### Wallet Security
- **Derivation Salt:** Stored in backend environment variables only
- **Private Keys:** Never transmitted or logged
- **Encryption:** AES-256 for all stored wallet data
- **Cloud Save:** Double encryption (Unity + our layer)

### Transaction Security
- **Signature Verification:** All transactions verified before relay
- **Rate Limiting:** 10 tx/minute per user
- **Gas Limits:** Max spend per transaction
- **Suspicious Activity:** Auto-pause on unusual patterns
- **Replay Protection:** Nonce tracking (EVM) / Recent blockhash (Solana)

### Backend Security
- **API Authentication:** JWT tokens from UGS verification
- **Input Validation:** All params sanitized
- **SQL Injection:** Using ORMs (Mongoose)
- **CORS:** Whitelist Unity app origins only
- **DDoS Protection:** Rate limiting + Cloudflare

---

## 📈 Scalability Plan

### Current Architecture (MVP)
- Handles: 10,000 users, 100,000 tx/day
- Backend: Single server
- Database: MongoDB single instance
- Cost: ~$100/month

### Scale to 100k Users
- Backend: Load balancer + 3-5 servers
- Database: MongoDB replica set
- RPC: Dedicated nodes or premium providers
- Cost: ~$500-1000/month

### Scale to 1M Users
- Backend: Auto-scaling (10-50 servers)
- Database: MongoDB sharding
- RPC: Own nodes + GeoDB routing
- CDN: For static assets
- Cost: ~$5000-10000/month

---

## 🎯 Success Metrics

**User Experience:**
- Login success rate: >99%
- Transaction success rate: >99%
- Average transaction time: <5 seconds
- SDK integration time: <30 minutes

**Cost Efficiency:**
- Somnia avg cost: <$0.0001/tx
- Sei avg cost: <$0.0008/tx
- Solana avg cost: <$0.00025/tx
- Gas pool refill: <1/week per chain

**Adoption:**
- Same wallet across 3+ games
- User retention D7: >40%
- Cloud Save usage: >60%
- Preferred chain: Track by game type

---

## 🚀 Deployment Strategy

### Phase 1: Testnet (Days 1-6)
- Deploy contracts to testnets
- Full integration testing
- Stress testing

### Phase 2: Mainnet Soft Launch (Day 7)
- Deploy to mainnet
- Limited rollout (1 game, 100 users)
- Monitor closely

### Phase 3: Full Launch (Day 8)
- Open to all games
- Public Dune dashboard
- Documentation published

---

## 📚 Technology Stack Summary

**Smart Contracts:**
- Solidity 0.8.20+ (EVM)
- Anchor Framework 0.29+ (Solana)
- Hardhat (deployment & testing)

**Unity:**
- Unity 2021.3 LTS+
- Unity Gaming Services SDK
- Nethereum 4.x (EVM Web3)
- Solnet 6.x (Solana Web3)

**Backend:**
- Node.js 18+ / TypeScript
- Express.js
- MongoDB 6+
- Bull (job queues)
- Web3.js / ethers.js
- @solana/web3.js

**Infrastructure:**
- Somnia RPC
- Sei RPC
- Solana RPC (QuickNode/Alchemy)
- MongoDB Atlas
- AWS/DigitalOcean
- Dune Analytics

---

**Next Steps:** Proceed to Smart Contract Development (Day 2)

