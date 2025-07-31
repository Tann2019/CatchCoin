# Solana Crypto Token Creation Guide

## Overview
This guide will help you create your own cryptocurrency token on the Solana blockchain using Windows with WSL (Windows Subsystem for Linux).

## Prerequisites
- Windows 10/11 with WSL installed
- Basic understanding of command line operations
- Some SOL tokens for transaction fees (can get from devnet faucet)

## Table of Contents
1. [WSL Setup](#wsl-setup)
2. [Install Solana Development Tools](#install-solana-development-tools)
3. [Basic Solana Configuration](#basic-solana-configuration)
4. [Create Your Token](#create-your-token)
5. [Token Management](#token-management)
6. [Advanced Features](#advanced-features)
7. [Deployment to Mainnet](#deployment-to-mainnet)

---

## WSL Setup

### Install WSL (if not already installed)
Open PowerShell as Administrator and run:
```powershell
wsl --install
```

After installation, restart your computer and open Ubuntu from the Start menu.

---

## Install Solana Development Tools

### For WSL with Package Issues (Recommended for WSL1)
If you're experiencing systemd-related package errors in WSL, use this direct installation approach:

```bash
# Skip problematic system packages and install Solana directly
# First, hold the problematic packages
sudo apt-mark hold systemd systemd-timesyncd udev libpam-systemd systemd-resolved systemd-sysv libnss-systemd

# Install Rust (essential for Solana)
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
source ~/.cargo/env

# Install Solana CLI directly
sh -c "$(curl -sSfL https://release.anza.xyz/stable/install)"

# Add Solana to PATH
echo 'export PATH="$HOME/.local/share/solana/install/active_release/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc

# Install SPL Token CLI
cargo install spl-token-cli
```

### Quick Installation (Recommended for WSL2)
Open your WSL terminal and run this single command that installs all dependencies:

```bash
curl --proto '=https' --tlsv1.2 -sSfL https://solana-install.solana.workers.dev | bash
```

This installs:
- Rust programming language
- Solana CLI
- Anchor CLI (framework for Solana programs)
- Node.js and Yarn

### Manual Installation (if quick install fails)

#### 1. Install Rust
```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
. "$HOME/.cargo/env"
```

Verify installation:
```bash
rustc --version
```

#### 2. Install Solana CLI
```bash
sh -c "$(curl -sSfL https://release.anza.xyz/stable/install)"
```

Add to PATH (check your shell with `echo $SHELL`):

For Bash:
```bash
echo 'export PATH="$HOME/.local/share/solana/install/active_release/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

For Zsh:
```bash
echo 'export PATH="$HOME/.local/share/solana/install/active_release/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

Verify installation:
```bash
solana --version
```

#### 3. Install Anchor CLI
```bash
cargo install --git https://github.com/solana-foundation/anchor avm --force
avm install latest
avm use latest
```

Verify installation:
```bash
anchor --version
```

#### 4. Install SPL Token CLI
```bash
cargo install spl-token-cli
```

---

## Basic Solana Configuration

### 1. Check Current Configuration
```bash
solana config get
```

### 2. Set Network to Devnet (for testing)
```bash
solana config set --url devnet
```

Network options:
- `devnet` - For testing (recommended to start)
- `mainnet-beta` - Live network (costs real money)
- `testnet` - Test network
- `localhost` - Local validator

### 3. Create a Wallet
```bash
solana-keygen new
```

**IMPORTANT**: Save the seed phrase securely! This is your wallet recovery phrase.

### 4. Check Your Wallet Address
```bash
solana address
```

### 5. Get Some Devnet SOL (for testing)
```bash
solana airdrop 2
```

If you get rate limit errors, try these alternatives:

**Option 1: Try smaller amounts**
```bash
solana airdrop 1
# Wait a few minutes, then try again
solana airdrop 1
```

**Option 2: Use the Web Faucet**
Visit https://faucet.solana.com/ and enter your wallet address

**Option 3: Try different amounts with delays**
```bash
solana airdrop 0.5
sleep 60
solana airdrop 0.5
sleep 60
solana airdrop 1
```

Check balance:
```bash
solana balance
```

**Note**: You need at least 0.01 SOL to create a token. If you keep getting rate limited, wait 10-15 minutes and try again.

---

## Create Your Token

### 1. Create a New Token (Mint Account)
```bash
spl-token create-token
```

This will output something like:
```
Creating token 99zqUzQGohamfYxyo8ykTEbi91iom3CLmwCA75FK5zTg
Address:  99zqUzQGohamfYxyo8ykTEbi91iom3CLmwCA75FK5zTg
Decimals: 9
```

**Save this address - this is your token's unique identifier!**

### 2. Create a Token Account to Hold Your Tokens
```bash
spl-token create-account <YOUR_TOKEN_ADDRESS>
```

Example:
```bash
spl-token create-account 99zqUzQGohamfYxyo8ykTEbi91iom3CLmwCA75FK5zTg
```

### 3. Mint Some Tokens
```bash
spl-token mint <YOUR_TOKEN_ADDRESS> <AMOUNT>
```

Example (mints 1000 tokens):
```bash
spl-token mint 99zqUzQGohamfYxyo8ykTEbi91iom3CLmwCA75FK5zTg 1000
```

### 4. Check Token Supply
```bash
spl-token supply <YOUR_TOKEN_ADDRESS>
```

### 5. Check Your Token Balance
```bash
spl-token balance <YOUR_TOKEN_ADDRESS>
```

### 6. View Your Token on Solana Explorer
You can view your token on the Solana blockchain explorer:

**Devnet Explorer:**
- Go to: https://explorer.solana.com/?cluster=devnet
- Search for your token address: `<YOUR_TOKEN_ADDRESS>`
- Or use direct link: `https://explorer.solana.com/address/<YOUR_TOKEN_ADDRESS>?cluster=devnet`

**Check all your tokens:**
```bash
spl-token accounts
```

**Get detailed token information:**
```bash
spl-token display <YOUR_TOKEN_ADDRESS>
```

---

## Token Management

### Transfer Tokens
```bash
spl-token transfer <TOKEN_ADDRESS> <AMOUNT> <RECIPIENT_ADDRESS>
```

### Create Token Account for Someone Else
```bash
spl-token create-account --owner <OWNER_ADDRESS> <TOKEN_ADDRESS>
```

### Disable Minting (Make Fixed Supply)
```bash
spl-token authorize <TOKEN_ADDRESS> mint --disable
```

### Set Freeze Authority
```bash
spl-token authorize <TOKEN_ADDRESS> freeze <NEW_FREEZE_AUTHORITY>
```

---

## Advanced Features

### Add Professional Metadata to Your Token

#### Step 1: Create Your Token Logo
**Logo Requirements:**
- Format: PNG or JPG
- Size: 512x512 pixels (recommended)
- Background: Transparent or solid color
- Style: Professional, clear, recognizable

**Free Logo Creation Tools:**
- Canva: https://canva.com
- Logo Maker: https://logomakr.com
- GIMP (free): https://gimp.org

#### Step 2: Create Metadata JSON File
Create a file called `catchcoin-metadata.json`:

```json
{
  "name": "CatchCoin",
  "symbol": "CATCH",
  "description": "CatchCoin - A revolutionary cryptocurrency token designed for the future of digital transactions",
  "image": "https://your-domain.com/catchcoin-logo.png",
  "external_url": "https://catchcoin.com",
  "properties": {
    "category": "token"
  },
  "attributes": [
    {
      "trait_type": "Type",
      "value": "Utility Token"
    },
    {
      "trait_type": "Network",
      "value": "Solana"
    },
    {
      "trait_type": "Standard",
      "value": "SPL Token"
    }
  ]
}
```

#### Step 3: Host Your Logo and Metadata
**Free Hosting Options:**
1. **GitHub Pages** (Recommended for developers):
   - Create GitHub repo
   - Upload logo and JSON
   - Enable GitHub Pages
   - Access via: `https://yourusername.github.io/catchcoin/`

2. **IPFS** (Decentralized):
   - Upload to Pinata.cloud or Web3.storage
   - Get permanent IPFS link

3. **Simple Website**:
   - Use Netlify, Vercel, or GitHub Pages
   - Create basic landing page

#### Step 4: Create Token with Metadata on Devnet (Test First)

```bash
# Create token with metadata extension
spl-token create-token --program-id TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb --enable-metadata

# Initialize metadata (replace with your URLs)
spl-token initialize-metadata <TOKEN_ADDRESS> "CatchCoin" "CATCH" "https://your-domain.com/catchcoin-metadata.json"
```

#### Step 5: Basic CatchCoin Website Template

Create `index.html`:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>CatchCoin - The Future of Digital Currency</title>
    <style>
        body { font-family: Arial, sans-serif; margin: 0; background: #f0f8ff; }
        .hero { background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); color: white; padding: 100px 20px; text-align: center; }
        .container { max-width: 800px; margin: 0 auto; padding: 20px; }
        .logo { width: 150px; height: 150px; margin: 20px auto; }
        .token-info { background: white; padding: 30px; border-radius: 10px; margin: 20px 0; box-shadow: 0 4px 6px rgba(0,0,0,0.1); }
    </style>
</head>
<body>
    <div class="hero">
        <div class="container">
            <img src="catchcoin-logo.png" alt="CatchCoin Logo" class="logo">
            <h1>CatchCoin (CATCH)</h1>
            <p>The Revolutionary Cryptocurrency Token</p>
        </div>
    </div>
    
    <div class="container">
        <div class="token-info">
            <h2>Token Information</h2>
            <p><strong>Token Address:</strong> <code>BjAWEumvr3gp7KCKCpBneFNfgt78X7Sck7CTpsPXTVHy</code></p>
            <p><strong>Network:</strong> Solana</p>
            <p><strong>Symbol:</strong> CATCH</p>
            <p><strong>Decimals:</strong> 9</p>
            
            <h3>About CatchCoin</h3>
            <p>CatchCoin represents the future of digital transactions, built on the fast and efficient Solana blockchain.</p>
            
            <p><a href="https://explorer.solana.com/address/BjAWEumvr3gp7KCKCpBneFNfgt78X7Sck7CTpsPXTVHy?cluster=devnet" target="_blank">View on Solana Explorer</a></p>
        </div>
    </div>
</body>
</html>
```

### Create Token with Metadata (Token Extensions)
This stores token name, symbol, and image directly on-chain:

```bash
spl-token create-token --program-id TokenzQdBNbLqP5VEhdkAS6EPFLC1PHnBqCXEpPxuEb --enable-metadata
```

Then initialize metadata:
```bash
spl-token initialize-metadata <TOKEN_ADDRESS> "<TOKEN_NAME>" "<TOKEN_SYMBOL>" "<TOKEN_URI>"
```

Example:
```bash
spl-token initialize-metadata BdhzpzhTD1MFqBiwNdrRy4jFo2FHFufw3n9e8sVjJczP "CatchCoin" "CATCH" "https://your-website.com/metadata.json"
```

### Metadata JSON Format
Create a JSON file with your token metadata:
```json
{
  "name": "CatchCoin",
  "symbol": "CATCH",
  "description": "A revolutionary cryptocurrency token",
  "image": "https://your-website.com/logo.png",
  "external_url": "https://your-website.com",
  "attributes": [
    {
      "trait_type": "Type",
      "value": "Utility Token"
    }
  ]
}
```

---

## Deployment to Mainnet

### 1. Mainnet Costs Breakdown
Before deploying to mainnet, here are the typical costs:

**Basic Token Creation:**
- Create token mint: ~0.00144 SOL (~$0.20-0.40)
- Create token account: ~0.00204 SOL (~$0.30-0.60)
- Mint tokens: ~0.000005 SOL per transaction (~$0.001)
- **Total for basic token: ~0.004 SOL (~$0.60-1.20)**

**Token with Metadata:**
- Create token with metadata: ~0.0055 SOL (~$0.80-1.60)
- Initialize metadata: ~0.0015 SOL (~$0.20-0.40)
- **Total with metadata: ~0.007 SOL (~$1.00-2.00)**

**Additional Costs:**
- Transaction fees: ~0.000005 SOL per transaction
- Account rent: Some accounts require rent deposits (usually recoverable)

**Current SOL Price Check:**
```bash
# Check current SOL price (approximate)
curl -s "https://api.coingecko.com/api/v3/simple/price?ids=solana&vs_currencies=usd"
```

### 2. Switch to Mainnet
```bash
solana config set --url mainnet-beta
```

### 3. Fund Your Wallet with Real SOL
You'll need to buy SOL from an exchange and transfer it to your wallet address.

**Recommended amount for token creation: 0.1 SOL** (covers all costs plus buffer)

**Where to buy SOL:**
- Coinbase, Binance, Kraken, FTX
- Send to your wallet: `3EjxLQzYNmhBZWaqjexw5p4GapZgeTs5F6e33riyT3xR`

### 4. Create Your Token on Mainnet
Follow the same steps as devnet, but on mainnet (costs real SOL).

---

## Important Security Notes

1. **Never share your private key or seed phrase**
2. **Test everything on devnet first**
3. **Backup your wallet files**
4. **Use a hardware wallet for large amounts**
5. **Verify all addresses before sending transactions**

---

## Useful Commands Reference

```bash
# Check all available spl-token commands
spl-token --help

# Check Solana CLI commands
solana --help

# Update Solana CLI
agave-install update

# Start local validator (for local development)
solana-test-validator

# Set local network
solana config set --url localhost
```

---

## Token Economics Considerations

1. **Total Supply**: Decide on maximum number of tokens
2. **Decimals**: Usually 6-9 decimals (9 is default)
3. **Distribution**: How will you distribute tokens?
4. **Utility**: What purpose does your token serve?
5. **Governance**: Will token holders have voting rights?

---

## Next Steps for Your CatchCoin Project

1. **Define Token Economics**: Total supply, distribution mechanism
2. **Create Metadata**: Logo, description, website
3. **Test on Devnet**: Create and test all functionality
4. **Deploy on Mainnet**: When ready for production
5. **Create Frontend**: Web interface for users to interact with your token
6. **Add Liquidity**: List on decentralized exchanges
7. **Marketing**: Community building and promotion

---

## Common Issues and Solutions

### Systemd Package Errors in WSL
If you encounter errors like "systemd", "systemd-timesyncd", "udev" errors when running `sudo apt update` or installing packages:

```bash
# Fix broken packages
sudo dpkg --configure -a

# Clean package cache
sudo apt clean
sudo apt autoclean

# Update package lists
sudo apt update --fix-missing

# Force fix broken dependencies
sudo apt --fix-broken install

# If still having issues, try:
sudo apt update && sudo apt upgrade -y
```

Alternative approach if above doesn't work:
```bash
# Reset package manager
sudo rm /var/lib/apt/lists/lock
sudo rm /var/cache/apt/archives/lock
sudo rm /var/lib/dpkg/lock*

# Reconfigure packages
sudo dpkg --configure -a
sudo apt update
```

### "Permission denied" error on Linux/WSL
Install build dependencies:
```bash
sudo apt update
sudo apt install build-essential
```

### AVM installation issues
Make sure Rust and Cargo are properly installed first.

### Network connection issues
Try switching to a different RPC endpoint:
```bash
solana config set --url https://api.devnet.solana.com
```

### Airdrop Rate Limit Issues
If you get "airdrop request failed" due to rate limits:

1. **Wait and retry**: Rate limits reset every few minutes
2. **Use web faucet**: Visit https://faucet.solana.com/
3. **Try smaller amounts**: `solana airdrop 0.5` instead of 2
4. **Check current balance**: `solana balance` to see if previous airdrops worked

### "AccountNotFound" Error When Creating Tokens
This error usually means insufficient SOL balance:

```bash
# Check your balance first
solana balance

# If balance is 0, get SOL from web faucet or try airdrop again
# You need at least 0.01 SOL for transactions

# Alternative: try switching to a different devnet RPC
solana config set --url https://devnet.helius-rpc.com
```

---

## Resources

- [Solana Documentation](https://solana.com/docs)
- [SPL Token Documentation](https://www.solana-program.com/docs/token)
- [Solana Cookbook](https://solana.com/developers/cookbook)
- [Solana Explorer](https://explorer.solana.com/)
- [Token Extensions Guide](https://solana.com/developers/guides/token-extensions)

---

*This guide was created to help you launch CatchCoin on the Solana blockchain. Good luck with your crypto project!*

