# nids-ledger-demo

A small browser demo for one idea from my blockchain + ML paper on network intrusion detection: if a blockchain is going to protect a NIDS, it should not store the alerts. It should store a short fingerprint of them.

The page runs a toy ledger. You capture a few fake IDS alerts, mine a block, and the block records only a Merkle root of that batch. The alerts themselves stay in a separate off-chain store. Then you try to cheat, and the ledger shows you where.

## Why this exists

Putting a blockchain behind an IDS makes the logs tamper-evident, but it also adds storage and verification cost, and the cost grows with the log volume. Papers on lightweight blockchains (SLIM is the one I leaned on) attack this with sharding, stateless verification and offloading. The simplest lever is earlier than any of that: don't put the logs on-chain at all. This demo makes that lever visible, and shows that you keep the tamper detection anyway.

It is a concept demo. It is not an experiment, and none of its numbers should be quoted as results.

## What happens when you click things

Capture alerts: generates five random alerts (source IP, attack type, severity) and holds them in a pending pool.

Mine block: takes the pending alerts, builds a Merkle tree over their SHA-256 hashes, and searches for a nonce until the block hash starts with the chosen number of zeros. The alerts move to the off-chain store. Only the header goes on the chain.

Edit an off-chain alert: flips the severity on one stored alert. The chain is untouched, but the Merkle root you get from the stored alerts no longer matches the one anchored in the block.

Edit an on-chain Merkle root: overwrites the root inside a block header. The block's stored hash no longer matches its contents, so it fails, and the link from the next block breaks.

Verify chain: recomputes everything and marks each block valid or says what went wrong.

## Block header

Each header is counted as 84 bytes. This is my own layout, chosen to be simple, not copied from a real chain.

| field | bytes |
|---|---|
| index | 4 |
| previous hash | 32 |
| Merkle root | 32 |
| timestamp | 8 |
| nonce | 8 |

The "on-chain" and "off-chain" figures on the page come from this layout and from the JSON length of the stored alerts. On a real chain, transaction formats and fees will change them.

## What it leaves out

There is one node, so there is no network, no consensus between peers, and no forks. Proof-of-work runs at toy difficulty. Whoever controls the browser tab controls the off-chain store, so there is no real access control. There is also no federated learning, encryption or zero-knowledge proof. SecureFLACF has those pieces, and I only borrowed its idea of anchoring records on-chain with a local detector next to them.

## Running it

It is one HTML file with no build step and no dependencies.

    npx serve .

Then open the localhost address it prints. The page uses the Web Crypto API, which only works on HTTPS or localhost, so opening index.html straight from the file system may not work in every browser.

## Deploying to Vercel

From this folder:

    npm i -g vercel
    vercel --prod

Or push the folder to GitHub, import the repo at vercel.com/new, set the framework preset to Other, and leave the build command and output directory empty. vercel.json only adds a few response headers.

## Files

    index.html    the whole demo (markup, styles, script)
    vercel.json   headers and clean URLs
    package.json  only there so `npm run dev` works
