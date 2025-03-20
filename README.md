# Interactly Docs

Documentation for interactly.ai

## Steps to run mintlify

```shell
npm i -g mintlify

pm2 delete documentation || true

pm2 start --name "documentation" --silent --namespace docs "mintlify dev --port 8400"

```
