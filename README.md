
# Sign in with NFT: Account NFT Tapps - The Future of Decentralized Authentication

## Introduction

**Sign in with NFT** is an open-source solution that enables any website to implement decentralized user authentication using NFTs. Just like "Sign in with Google" or "Sign in with Facebook," this project provides a frictionless login experience but with **user-owned** credentials in the form of NFTs. This empowers users with privacy, true ownership, and decentralized identity across the web.

By adopting "Sign in with NFT," we aim to drive mass adoption of **Ethereum** and **web3 technologies**. It allows websites to seamlessly integrate decentralized login mechanisms while giving users control over their own identity and credentials.

## Features

- **NFT-based Authentication**: Websites can implement an easy and secure way for users to log in using their NFTs as credentials.
- **Universal Integration SDK**: A simple JavaScript SDK for any website to quickly add the "Sign in with NFT" functionality.
- **NFT Claiming for Sign-Up**: If users don’t have the required NFT, they can mint or claim the NFT directly during the sign-up process, making the onboarding experience seamless.
- **ERC-5169/ERC-7738 Integration**: Supports loading TokenScript for NFTs to act as tapps (portable mini-applications), [Account NFT tapp](https://github.com/zhangzhongnan928/Sign-in-with-NFT-Tapp/blob/main/account_nft_tapp_use_cases.md).
- **Multi-chain Support**: Initially supports Ethereum and Polygon, with plans to expand to other EVM-compatible chains.
- **Customizable Login Logic**: Websites can set login conditions based on specific NFT attributes or collections.
- **Fully Open-Source**: The project is a public good aimed at driving mass Ethereum adoption, with contributions welcome from developers worldwide.

## Benefits

### For Users
- **Ownership**: Users own their digital identity(asset) in the form of NFTs, providing them with more control over their data and privacy.
- **Seamless Experience**: Sign in across different websites and platforms with a single NFT, without needing to remember multiple usernames and passwords.
- **Privacy-first**: Each platform issues its own NFT, ensuring user privacy is maintained across different platforms.
- **Exclusive Perks**: Users may unlock exclusive content, features, or benefits by holding specific NFTs tied to a platform or ecosystem.

### For Websites
- **Onboard Crypto Users**: Tap into the fast-growing crypto-native audience by offering NFT-based login. This positions your website to attract a tech-savvy, high-value user group.
- **No Centralized Account Management**: Eliminate the need to manage user credentials or store sensitive information, reducing liability.
- **Easy Integration**: With the SDK, websites can easily implement NFT-based authentication without complex setup.
- **Several Steps Closer to Payment**: Each user connects to your website with their NFT and wallet. Since the wallet already holds funds, you're just one transaction away from offering seamless payments.
- **Increased Engagement**: NFTs allow for unique, platform-specific rewards or access, encouraging users to engage and participate more with the website.
- **Viral Marketing**: Users can showcase their NFTs across social platforms, driving organic growth and visibility for the website.

### For Ethereum
- **Mass Adoption**: This project drives more users and developers to Ethereum by making NFTs a central part of decentralized identity and authentication.
- **Increased Usage**: As more websites adopt "Sign in with NFT," Ethereum becomes the backbone of digital identity(asset) management, increasing network activity and transaction volume.
- **Interoperability**: The project showcases Ethereumâs capability to act as a bridge across multiple dApps and ecosystems, leveraging its strength as an open, decentralized platform.

## Table of Contents

- [How It Works](#how-it-works)
- [Getting Started](#getting-started)
- [SDK Integration](#sdk-integration)
- [NFT Verification Backend](#nft-verification-backend)
- [Contributing](#contributing)
- [License](#license)

## How It Works

### User Flow

1. **Connect or Create Wallet**: Users click "Sign in with NFT" on a website. If they have a wallet (e.g., MetaMask or Coinbase Wallet), they can connect it directly. If they don’t have a wallet, they can create one on the spot using **Coinbase Smart Wallet** or **Joy.ID**.
   
2. **Claim or Mint NFT**: If users don’t own the required NFT, they are prompted to claim or mint the NFT during the sign-up process, making the onboarding experience frictionless.

3. **Challenge Message**: The website backend generates a challenge message that is sent to the user for signing.

4. **Verify NFT Ownership**: After the user signs the message, the backend verifies that the user holds the required NFT and checks its validity (e.g., attributes, collection).

5. **Authentication**: Once verified, the user is authenticated and logged in to the website.

### Supported Chains
Currently, "Sign in with NFT" supports:
- Ethereum
- Base

More EVM-compatible chains like Optimism, Arbitrum, Polygon and others will be added based on community demand.

## Getting Started

### Prerequisites
To get started with integrating **Sign in with NFT**, you will need:
- A **MetaMask** wallet or other Ethereum-compatible wallets.
- Basic knowledge of **JavaScript** and **web3**.
- A web platform where you want to enable decentralized login.

### Installation
First, clone this repository and install the necessary dependencies.

```bash
git clone https://github.com/your-repo/sign-in-with-nft.git
cd sign-in-with-nft
npm install
```

Next, you can add the **Sign in with NFT SDK** to your website.

## SDK Integration

To integrate "Sign in with NFT" on your website, follow these steps:

1. Install the SDK in your project:
   ```bash
   npm install sign-in-with-nft-sdk
   ```

2. Add the following code to your login page:

```javascript
import { connectWallet, signMessage, verifyNFT } from 'sign-in-with-nft-sdk';

// Triggered when the user clicks "Sign in with NFT"
async function signInWithNFT() {
  const wallet = await connectWallet();
  
  // Generate a challenge message
  const challenge = await fetch('/api/generate-challenge');
  
  // Sign the challenge message with the user's wallet
  const signature = await signMessage(wallet, challenge);
  
  // Verify the signed message and NFT ownership
  const isValid = await verifyNFT(wallet, signature);
  
  if (isValid) {
    // Successful login, redirect the user
    window.location.href = '/dashboard';
  } else {
    alert("Invalid NFT or login attempt failed.");
  }
}
```

3. Configure your backend to handle challenge message generation and NFT verification. (See the [NFT Verification Backend](#nft-verification-backend) section for more details.)

### Example Code
Check the `/examples` directory for fully working integration examples.

## NFT Verification Backend

To verify NFT ownership, you will need a backend that handles the challenge generation and NFT validation. Hereâs a basic implementation:

1. **Generate a Challenge**: This API endpoint generates a challenge for the user to sign.
```javascript
app.get('/api/generate-challenge', (req, res) => {
  const challenge = `Sign this message to authenticate: ${Date.now()}`;
  res.json({ challenge });
});
```

2. **Verify Signature and NFT**: This API endpoint verifies the signed message and checks NFT ownership.
```javascript
app.post('/api/verify-nft', async (req, res) => {
  const { walletAddress, signature } = req.body;

  // Recover the signed message
  const signedMessage = web3.eth.accounts.recover(challenge, signature);
  if (signedMessage.toLowerCase() !== walletAddress.toLowerCase()) {
    return res.status(401).json({ message: 'Invalid signature' });
  }

  // Check NFT ownership
  const contract = new web3.eth.Contract(ERC721_ABI, NFT_CONTRACT_ADDRESS);
  const owner = await contract.methods.ownerOf(TOKEN_ID).call();
  
  if (owner.toLowerCase() === walletAddress.toLowerCase()) {
    return res.json({ valid: true });
  } else {
    return res.status(401).json({ message: 'NFT not owned' });
  }
});
```

More advanced backend setups (e.g., multi-chain, custom logic for NFT attributes) can be added as necessary.

## Contributing

We welcome contributions from the community! Hereâs how you can get involved:
1. Fork the repo and create your own branch.
2. Add your changes (new features, bug fixes, etc.).
3. Submit a pull request with a description of what youâve done.

### Roadmap
- Add support for more wallets (e.g., Coinbase Wallet).
- Expand chain compatibility (Base, Optimism, Arbitrum).
- Add TokenScript-based customizable login flows.
- Build a user interface for easier NFT verification management.

### Issues
If you encounter any issues or have feature requests, please submit them in the [Issues](https://github.com/your-repo/sign-in-with-nft/issues) section of this repository.

## License

This project is open source and licensed under the [MIT License](./LICENSE).

---

Made with ð by the Ethereum community. Let's build the decentralized web together!
