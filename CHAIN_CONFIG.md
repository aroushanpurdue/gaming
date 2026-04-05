# Chain Configuration

This file defines all supported blockchain networks and their configurations.

---

## EVM Chains

### Somnia Network

**Network Details:**
```json
{
  "chainName": "Somnia Network",
  "chainId": 50311,
  "chainType": "evm",
  "rpcUrl": "https://rpc.somnia.network",
  "fallbackRpcUrls": [
    "https://rpc-backup.somnia.network"
  ],
  "explorerUrl": "https://explorer.somnia.network",
  "nativeCurrency": {
    "name": "Somnia",
    "symbol": "STT",
    "decimals": 18
  },
  "testnet": {
    "chainId": 50312,
    "rpcUrl": "https://rpc.testnet.somnia.network",
    "explorerUrl": "https://explorer.testnet.somnia.network"
  }
}
```

**Characteristics:**
- Block Time: ~400ms
- TPS: 400,000+
- Avg Gas Cost: ~$0.0001 per transaction
- Best For: Ultra-fast gaming transactions, real-time gameplay

**Contract Addresses:**
```
Testnet:
  GameWallet: TBD (deploy on Day 2)
  GasTank: TBD (deploy on Day 2)
  
Mainnet:
  GameWallet: TBD (deploy on Day 8)
  GasTank: TBD (deploy on Day 8)
```

---

### Sei Network Mainnet

**Network Details:**
```json
{
  "chainName": "Sei Network",
  "chainId": 1329,
  "chainType": "evm",
  "rpcUrl": "https://evm-rpc.sei-apis.com",
  "fallbackRpcUrls": [
    "https://evm-rpc-arctic-1.sei-apis.com"
  ],
  "explorerUrl": "https://seitrace.com",
  "nativeCurrency": {
    "name": "Sei",
    "symbol": "SEI",
    "decimals": 18
  },
  "testnet": {
    "chainId": 713715,
    "rpcUrl": "https://evm-rpc-testnet.sei-apis.com",
    "explorerUrl": "https://seitrace.com/?chain=arctic-1"
  }
}
```

**Characteristics:**
- Block Time: ~390ms
- TPS: 28,300+ (parallelized)
- Avg Gas Cost: ~$0.0008 per transaction
- Best For: DeFi + Gaming hybrid, NFT marketplaces

**Contract Addresses:**
```
Testnet (Arctic-1):
  GameWallet: TBD (deploy on Day 2)
  GasTank: TBD (deploy on Day 2)
  
Mainnet:
  GameWallet: TBD (deploy on Day 8)
  GasTank: TBD (deploy on Day 8)
```

---

## Non-EVM Chains

### Solana

**Network Details:**
```json
{
  "chainName": "Solana",
  "chainType": "solana",
  "rpcUrl": "https://api.mainnet-beta.solana.com",
  "fallbackRpcUrls": [
    "https://solana-api.projectserum.com",
    "https://rpc.ankr.com/solana"
  ],
  "explorerUrl": "https://solscan.io",
  "nativeCurrency": {
    "name": "Solana",
    "symbol": "SOL",
    "decimals": 9
  },
  "testnet": {
    "rpcUrl": "https://api.devnet.solana.com",
    "explorerUrl": "https://solscan.io/?cluster=devnet"
  }
}
```

**Characteristics:**
- Slot Time: ~400ms
- TPS: 65,000+
- Avg Transaction Fee: ~$0.00025
- Best For: NFT minting, SPL tokens, high-frequency actions

**Program Addresses:**
```
Devnet:
  game_wallet: TBD (deploy on Day 2)
  
Mainnet-Beta:
  game_wallet: TBD (deploy on Day 8)
```

---

## Gas Pool Configuration

**Initial Funding (Testnet):**
- Somnia Testnet: 1000 STT (from faucet)
- Sei Testnet: 100 SEI (from faucet)
- Solana Devnet: 10 SOL (from faucet)

**Initial Funding (Mainnet - Day 8):**
- Somnia: $200 worth (~200,000 STT estimated)
- Sei: $150 worth (~1,000 SEI estimated)
- Solana: $150 worth (~0.75 SOL estimated)

**Auto-Refill Thresholds:**
- Alert when balance < $50 USD equivalent
- Critical alert when balance < $20 USD equivalent

---

## Unity Gaming Services Configuration

**Authentication Methods:**
```json
{
  "providers": [
    {
      "name": "Google Play Games",
      "platform": "Android",
      "enabled": true,
      "priority": 1
    },
    {
      "name": "Email/Password",
      "platform": "All",
      "enabled": true,
      "priority": 2
    },
    {
      "name": "Facebook",
      "platform": "All",
      "enabled": true,
      "priority": 3
    },
    {
      "name": "Anonymous",
      "platform": "All",
      "enabled": true,
      "priority": 4
    }
  ]
}
```

**UGS Project Setup:**
```
Project ID: TBD (create on Day 1)
Environment: Production
Services Enabled:
  - Authentication
  - Cloud Save
  - Analytics (optional)
```

---

## Security Configuration

**Wallet Derivation:**
```
Algorithm: HMAC-SHA256
Salt: (stored in backend .env)
Iterations: 100,000
Key Length: 32 bytes
```

**Encryption:**
```
Algorithm: AES-256-GCM
IV: Random 12 bytes per encryption
Auth Tag: 16 bytes
```

**Rate Limiting:**
```
Per User:
  - Max 10 transactions per minute
  - Max 100 transactions per hour
  - Max 500 transactions per day

Per IP:
  - Max 100 requests per minute
  - Max 1000 requests per hour
```

---

## Development vs Production

**Development (Days 1-7):**
- Use testnets only
- Mock UGS authentication for testing
- Unlimited rate limits
- Verbose logging

**Production (Day 8+):**
- Mainnets only
- Real UGS authentication
- Strict rate limits
- Error logging only

---

**Configuration Template Version:** 1.0  
**Last Updated:** April 5, 2025

