# Genesis Zero

NFTs on Kaspa. Actually in the DAG.

The image, traits, ownership, and trade settlement all live inside Kaspa transactions. No IPFS. No server. No contracts. No backend. No trust assumptions. The DAG is the database.

**Live on mainnet:** [kasgenesiszero.com](https://kasgenesiszero.com)

---

## What it does

- **Deploy** a collection of any size. Images are inscribed directly into transaction payloads, chunked if needed, with parallel workers up to 250x for large drops.
- **Mint** randomly from any deployed collection. The mint is a real transaction in the DAG with the full image data.
- **Trade** through covenant-locked listings. The buyer's payment is enforced by the script. They cannot underpay, cannot redirect funds, cannot fake the transaction.
- **Cancel** with a Schnorr signature on the IF-branch. Free, no self-payment, no protocol fee.
- **Send** with a transfer payload. Ownership-enforced by consensus-visible event replay. Cheating transfers during an active listing are ignored.

---

## How it works

**Inscriptions.** Every NFT is a real Kaspa transaction whose payload carries the image bytes (or chunk references), MIME type, name, traits, creator, and ownership. Anyone with a Kaspa node can decode and verify.

**Collections.** A collection is a manifest transaction that lists all item template TX IDs. For collections over 80 items, the manifest is split into part transactions. No central registry.

**Marketplace.** Listings use a KIP-10 covenant P2SH script with two branches:

- **ELSE branch (buy):** spendable by anyone, but the script forces output 0 to pay the seller's address with at least the listed price. `OP_TXOUTPUTSPK` + `OP_EQUALVERIFY` and `OP_TXOUTPUTAMOUNT` + `OP_GREATERTHANOREQUAL` are consensus-enforced. The buyer cannot underpay or redirect.
- **IF branch (cancel):** spendable only with the seller's signature via `OP_CHECKSIG`. Free cancel, no payment required.

**Ownership resolver.** The client walks chain history for each NFT and replays events through a state machine that:

- Honors V2 listings as locks (transfers during an active listing are dead data).
- Rejects forged delists by validating the delister against the original lister.
- Rejects forged legacy buys by requiring the seller to actually be the current owner.
- Walks forward through buy and transfer chains to arrive at the current canonical owner, regardless of how many hands the NFT has passed through.

Same settlement model as KRC-20 and Ordinals on the ownership side, with chain-enforced payment on top.

---

## How to use it

1. Open [kasgenesiszero.com](https://kasgenesiszero.com).
2. Set a password. The site generates a wallet and encrypts your private key with **AES-256-GCM** + **PBKDF2 1,000,000 iterations**. The key never leaves your browser.
3. Fund your wallet with KAS.
4. **Explore** existing collections, **Deploy** your own, **Mint** from open ones, or visit **Market** to trade.
5. Click any NFT in **My NFTs** for details, send, list, or cancel.

Auto-locks after 10 minutes of inactivity. Back up your password. There is no recovery.

---

## Fees

Deploy fees are tiered by collection size:

| Collection size     | Total  | Miners (80%) | Protocol (20%) | Max image size |
|---------------------|--------|--------------|----------------|----------------|
| 1 to 1,000 items    | 250 KAS | 200 KAS      | 50 KAS         | 1.5 MB         |
| 1,001 to 5,000 items| 500 KAS | 400 KAS      | 100 KAS        | 0.5 MB         |

Per-transaction fees:

| Action  | Miners    | Protocol (maker)  | Total on top of price |
|---------|-----------|-------------------|-----------------------|
| Mint    | 2.5 KAS   | 2.5 KAS           | 5 KAS                 |
| Buy     | 2.5 KAS   | 2.5 KAS           | 5 KAS                 |
| Cancel  | ~0 KAS    | 0 KAS             | ~0 KAS                |

Mint price on top of the fee goes to the collection creator. Buy price on top of the fee goes to the seller.

---

## Security model

**Covenant-enforced payment + consensus-visible ownership.**

- Buyer cannot underpay: covenant enforces `>=` listed price.
- Buyer cannot redirect payment: covenant enforces output SPK equals seller SPK.
- Seller cannot get paid without releasing the listing: covenant is a single-spend UTXO.
- Seller cannot list-then-send to cheat the buyer: resolver ignores cheating transfers during active listings.
- Only seller can cancel for free: signature gate on the IF branch.
- Wallet keys are AES-256-GCM encrypted, never logged, wiped on auto-lock.
- Counterfeit shield hides NFTs that claim membership in collections they do not belong to, and hides items beyond a collection's declared supply.

**Honest limit.** NFT ownership is not native UTXO custody. It is resolved by replaying events in the DAG through the state machine, the same way KRC-20 balances are resolved. This is the strongest model possible until Kaspa ships native NFT UTXO locking in a future hardfork.

---

## Disclaimer

This is open source software provided **as is, without warranty of any kind**, express or implied, including but not limited to merchantability, fitness for a particular purpose, and non-infringement.

- **Not your keys, not your coins.** Your private key is encrypted in your browser. Back up your password. There is no recovery, no support email, no admin override.
- **No financial advice.** NFTs and crypto are volatile and may lose all value.
- **No guarantees of liquidity, value, or future support.** This is a protocol and a reference client, not a service.
- **Use at your own risk.** The authors and contributors are not liable for any loss of funds, data, or NFTs.

By using this software you accept full responsibility for your transactions.

---

## Open source

MIT License. Fork it. Run it. Improve it. Anyone can host their own client, the protocol is permissionless.

Built on Kaspa. Powered by the BlockDAG. No middlemen.

---

## Links

- Site: [kasgenesiszero.com](https://kasgenesiszero.com)
- Twitter: [@Kas_Ranks](https://twitter.com/Kas_Ranks)
- Companion projects:
  - [KasProof](https://kasproof.com), file timestamping on Kaspa
  - [Kassword](https://kassword.com), encrypted password storage on Kaspa

---

The first genesis0 collection deployed on Kaspa mainnet lives at transaction `e154964b049cdc8660e3b58a4cd5c9fcfdbc2316bfbdcf6688e6a82f9be213c6`. That is Genesis Zero.
