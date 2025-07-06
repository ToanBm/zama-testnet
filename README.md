# Zama Protocol Technical Guides
## Install Dependecies
#### Packages:
```
sudo apt update && sudo apt upgrade -y
sudo apt install curl build-essential git wget jq make gcc nano tmux unzip libssl-dev pkg-config -y
```
#### Install Nodejs, npm, yarn
```
sudo apt update
sudo curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
sudo apt install -y nodejs
node -v
sudo npm install -g yarn
yarn -v
```
#### Install Hardhat
```
sudo npm install -g hardhat
```
## Deploy FHECounter contract
### Clone Repo
```
git clone https://github.com/zama-ai/fhevm-hardhat-template
cd fhevm-hardhat-template
```
### Install:
```
npm install
```
### Replace hardhat.config.ts file:
```
rm hardhat.config.ts && nano hardhat.config.ts
```
```
import "@fhevm/hardhat-plugin";
import "@nomicfoundation/hardhat-chai-matchers";
import "@nomicfoundation/hardhat-ethers";
import "@nomicfoundation/hardhat-verify";
import "@typechain/hardhat";
import "hardhat-deploy";
import "hardhat-gas-reporter";
import type { HardhatUserConfig } from "hardhat/config";
import "solidity-coverage";
import "dotenv/config";

import "./tasks/accounts";
import "./tasks/FHECounter";

// Load environment variables
const SEPOLIA_RPC_URL: string = process.env.SEPOLIA_RPC_URL || "";
const PRIVATE_KEY: string = process.env.PRIVATE_KEY || "";
const ETHERSCAN_API_KEY: string = process.env.ETHERSCAN_API_KEY || "";

// Validate required environment variables
if (!SEPOLIA_RPC_URL) {
  throw new Error("SEPOLIA_RPC_URL is not set in .env file");
}
if (!PRIVATE_KEY) {
  throw new Error("PRIVATE_KEY is not set in .env file");
}

const config: HardhatUserConfig = {
  defaultNetwork: "hardhat",
  namedAccounts: {
    deployer: 0,
  },
  etherscan: {
    apiKey: {
      sepolia: ETHERSCAN_API_KEY,
    },
  },
  gasReporter: {
    currency: "USD",
    enabled: process.env.REPORT_GAS ? true : false,
    excludeContracts: [],
  },
  networks: {
    hardhat: {
      chainId: 31337,
    },
    anvil: {
      accounts: [`0x${PRIVATE_KEY}`],
      chainId: 31337,
      url: "http://localhost:8545",
    },
    sepolia: {
      accounts: [`0x${PRIVATE_KEY}`],
      chainId: 11155111,
      url: SEPOLIA_RPC_URL,
    },
  },
  paths: {
    artifacts: "./artifacts",
    cache: "./cache",
    sources: "./contracts",
    tests: "./test",
  },
  solidity: {
    version: "0.8.24",
    settings: {
      metadata: {
        bytecodeHash: "none",
      },
      optimizer: {
        enabled: true,
        runs: 800,
      },
      evmVersion: "cancun",
    },
  },
  typechain: {
    outDir: "types",
    target: "ethers-v6",
  },
};

export default config;
```
### Set .env file
```
PRIVATE_KEY=your-private-key
SEPOLIA_RPC_URL=https://ethereum-sepolia-rpc.publicnode.com
ETHERSCAN_API_KEY=your-api-key
ZAMA_RPC_URL=https://fhevm-testnet.zama.ai
```
### Verify your wallet
```
npx hardhat accounts --network sepolia
```
#### Compile
```
npx hardhat compile
```
### Deploy
```
npx hardhat deploy --network sepolia
```



