# Genesis Zero

NFTs on Kaspa, but actually on-chain.

The image, traits, ownership, and trade settlement all live inside Kaspa transactions. No IPFS. No server. No contracts. No backend. No trust assumptions. The DAG is the database.

**Live on testnet:** [kasgenesiszero.com](https://kasgenesiszero.com)
**Mainnet:** soon.

---

## What it does

- **Deploy** a collection of any size. Images are inscribed directly into transaction payloads, chunked if needed, with parallel workers up to 40x for large drops.
- **Mint** randomly from any deployed collection. The mint is a real on-chain transaction with the full image data.
- **Trade** through covenant-locked listings. The buyer's payment is enforced by the script — they cannot underpay, cannot redirect funds, cannot fake the transaction.
- **Cancel** with a Schnorr signature on the IF-branch. Free, no self-payment, no protocol fee.
- **Send** with a transfer payload. Indexer-enforced ownership; cheating transfers during an active listing are ignored.

---

## How it works

**Inscriptions.** Every NFT is a real Kaspa transaction whose payload contains the image bytes (or chunk references), MIME type, name, traits, creator, and ownership. Anyone with a Kaspa node can decode and verify.

**Collections.** A collection is a manifest transaction that lists all the item template TX IDs. For collections over 80 items, the manifest is split into part transactions. No central registry.

**Marketplace.** Listings use a KIP-10 covenant P2SH script with two branches:

- **ELSE branch (buy):** spendable by anyone, but the script forces output 0 to pay the seller's address with at least the listed price. `OP_TXOUTPUTSPK` + `OP_EQUALVERIFY` and `OP_TXOUTPUTAMOUNT` + `OP_GREATERTHANOREQUAL` are consensus-enforced. The buyer cannot underpay or redirect.
- **IF branch (cancel):** spendable only with the seller's signature via `OP_CHECKSIG`. Free cancel, no payment required.

**Ownership resolver.** The client walks chain history for each NFT and replays events through a state machine that:

- Honors V2 listings as locks (xfers during active listings are ignored as dead data).
- Rejects fake delists by validating the delister against the original lister.
- Rejects fake legacy buys by requiring the seller to actually be the current owner.

This is the same model as KRC-20 and Ordinals on the ownership side, with chain-enforced payment on top.

---

## How to use it

1. Open [kasgenesiszero.com](https://kasgenesiszero.com).
2. Set a password. The site generates a wallet and encrypts your private key with **AES-256-GCM** + **PBKDF2 1,000,000 iterations**. The key never leaves your browser.
3. Send some testnet KAS to your wallet address (faucet on Kaspa Discord).
4. **Explore** existing collections, **Deploy** your own, **Mint** from open ones, or visit **Market** to trade.
5. Click any NFT in **My NFTs** for details, send, list, or cancel.

Auto-locks after 10 minutes of inactivity. Backup your password — there is no recovery.

---

## Security model

**Covenant-enforced payment + indexer-enforced NFT ownership.**

- Buyer cannot underpay: covenant `>=` price check.
- Buyer cannot redirect payment: covenant `==` seller SPK check.
- Seller cannot get paid without releasing the listing: covenant single-spend.
- Seller cannot list-then-send to cheat the buyer: state machine ignores cheating xfers.
- Only seller can cancel for free: signature gate on the IF branch.
- Wallet keys are AES-256-GCM encrypted, never logged, wiped on auto-lock.

**Honest limit:** NFT ownership is not native UTXO custody. It is resolved by replaying on-chain events through the state machine, the same way KRC-20 balances are resolved. This is the strongest model possible until Kaspa ships native NFT UTXO locking.

---

## Audits

Two independent AI audit passes during the testnet hardening sprint. All critical and high-severity findings remediated. Zero red items at ship time.

Patch trail: KIE-3 (resolver state machine) → KIE-4 (canonical state) → KIE-4.1 (fail-closed send) → KIE-4.2 (cache invalidation) → KIE-4.3 (resolver hardening) → KIE-4.4 (image pipeline) → KIE-4.5 (gallery render).

---

## Disclaimer

This is open source software provided **as is, without warranty of any kind**, express or implied, including but not limited to merchantability, fitness for a particular purpose, and non-infringement.

- **Testnet first.** Use testnet to learn the flows before touching real KAS on mainnet.
- **Not your keys, not your coins.** Your private key is encrypted in your browser. Backup your password. There is no recovery, no support email, no admin override.
- **No financial advice.** NFTs and crypto are volatile and may lose all value.
- **No guarantees of liquidity, value, or future support.** This is a protocol and a reference client, not a service.
- **Use at your own risk.** The authors and contributors are not liable for any loss of funds, data, or NFTs.

By using this software you accept full responsibility for your transactions.

---

## Open source

MIT License. Fork it. Run it. Improve it. Anyone can host their own client — the protocol is permissionless.

Built on Kaspa. Powered by the BlockDAG. No middlemen.

---

## Links

- Site: [kasgenesiszero.com](https://kasgenesiszero.com)
- Twitter: [@Kas_Ranks](https://twitter.com/Kas_Ranks)
- Companion projects:
  - [KasProof](https://kasproof.com) — file timestamping on Kaspa
  - [Kassword](https://kassword.com) — encrypted password storage on Kaspa

---

This was step three.
