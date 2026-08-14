# Backend — Gas Provider

Fastify API for Flare Summer Signal: deposit intents, FTSO quotes, FDC attestation (best-effort), and treasury gas distribution.

## Run

```bash
cp .env.example .env
npm install
npx prisma migrate deploy
npm run dev   # :3000
```

Health: `GET /health`

## Production

Deploy with Railway (`railway.json` + Dockerfile).

**Production:** https://backend-production-6f62.up.railway.app  
Point frontend `VITE_API_URL` at that URL (already set on Vercel Production).

## Key services

- `ftso` / `priceCalculator` — FTSO-backed pricing  
- `fdc` — deposit attestation  
- `treasuryDistribution` — native gas payouts  
- `eventProcessor` — deposit → intent pipeline  

See [../docs/ENVIRONMENT_VARIABLES.md](../docs/ENVIRONMENT_VARIABLES.md).
