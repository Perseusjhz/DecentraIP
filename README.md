# DecentraIP: A Decentralized Intellectual Property Registry on Ethereum

**SC6106 Blockchain Development Fundamentals — Project Summary**  
JIAO HAOZHE · TAN LINHAO · SONG RUOCHANG

---

## Overview

DecentraIP is a decentralized application (DApp) that enables creators to register, verify, and transfer ownership of intellectual property (IP) on the Ethereum blockchain. The system replaces the slow and costly process of traditional IP registration with an instant, tamper-proof, and independently verifiable on-chain record.

The project was implemented as a full-stack DApp comprising a Solidity smart contract and a browser-based frontend, deployed on the Remix JavaScript VM for demonstration purposes.

---

## Problem and Motivation

Traditional intellectual property registration suffers from three fundamental shortcomings:

- **Slow**: Patent applications routinely take 18–36 months to process.
- **Hard to dispute**: Priority of creation is difficult to prove without expensive third-party notarisation.
- **Centralised**: Copyright databases represent single points of failure and can be tampered with or taken offline.

Blockchain directly addresses each of these issues. Once a record is written to the chain, it is immutable and timestamped by consensus rather than by any single authority. Ownership can be queried by anyone without requiring trust in an intermediary.

---

## Technical Design

The system is structured in three layers:

| Layer | Components |
|---|---|
| Frontend | Single HTML file · Web Crypto API · client-side SHA-256 hashing |
| Integration | ethers.js v6 · MetaMask wallet signing |
| On-chain | Solidity 0.8.20 · `IPRegistry.sol` · Remix VM |

> The file itself never leaves the user's device — only the 32-byte hash is sent to the blockchain.

The contract exposes three external functions:

- **`registerIP`** — writes a new `IPRecord` struct (owner address, block timestamp, existence flag) to the mapping and emits an `IPRegistered` event.
- **`verifyIP`** — a `view` function that returns ownership data at zero gas cost to the caller.
- **`transferOwnership`** — reassigns ownership after validating the caller's identity via the `onlyIPOwner` modifier, emitting an `OwnershipTransferred` event.

---

## Security and Gas Optimisation

**Security measures:**

| Threat | Defence |
|---|---|
| Duplicate registration | Existence flag checked before every write |
| Unauthorised transfer | `onlyIPOwner` modifier compares `msg.sender` to stored owner |
| Empty hash attack | `validHash` modifier rejects `bytes32(0)` |
| Ownership burn | Explicit check against `address(0)` |
| Reentrancy | Structurally impossible — no external calls made |

**Gas optimisations:**

- `bytes32` instead of `string` for the hash key — single EVM storage slot, no dynamic memory allocation.
- Struct fields ordered for slot packing — `address` (20 bytes) and `bool` (1 byte) share one 32-byte slot.
- `verifyIP` marked as `view` — read operations are free for off-chain callers.

---

## Limitations

- Only the hash is stored on-chain; the original file must be preserved separately.
- Block timestamps carry a ~15-second tolerance and can be marginally manipulated by miners.
- No access control on registration — any address can submit arbitrary hashes.
- No on-chain dispute arbitration mechanism.

---

## Future Enhancements

- **IPFS integration** — store file content in a decentralised way alongside the on-chain hash.
- **ERC-721 tokenisation** — turn each IP record into a tradeable NFT.
- **Multi-signature** — require multiple signers for institutional IP assets.
- **Layer 2 deployment** — Polygon or Arbitrum to cut gas costs by 90%+.

---

## Project Structure

```
DecentraIP/
├── contracts/
│   └── IPRegistry.sol       # Solidity 0.8.20 smart contract
└── frontend/
    └── index.html           # Single-file DApp frontend
```

## Tech Stack

| Component | Technology |
|---|---|
| Smart contract | Solidity 0.8.20 |
| Development / deployment | Remix IDE |
| Frontend | HTML5 + Web Crypto API |
| Web3 integration | ethers.js v6 |
| Wallet | MetaMask |
| Hash algorithm | SHA-256 |
