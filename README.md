# Hash-anchored NIDS ledger demo

Static site. No build step, no dependencies. Alerts stay off-chain; each block anchors only a Merkle root (SHA-256, proof-of-work in the browser).

## Deploy on Vercel
Option A (CLI): `npm i -g vercel`, then run `vercel --prod` in this folder. Accept the defaults (Framework: Other, no build command, output directory: `.`).

Option B (Git): push this folder to a GitHub repo, import it at vercel.com/new, keep Framework Preset "Other", leave Build Command and Output Directory empty, click Deploy.

## Run locally
`npx serve .` then open the printed localhost URL (Web Crypto needs HTTPS or localhost).
