# Apple of the World

**Cross-market transmission intelligence.** Input a hypothetical equity action — "buy 1,000 AAPL from a Chinese broker, what happens to Germany?" — and the system traces 2nd / 3rd-order impacts across global markets in real time. Powered by a 4-agent reasoning ensemble with full chain-of-thought transparency and verifiable on-chain prediction receipts.

**Status**: 📋 PLANNING (Phase 0 — pre-build design)
**Genesis**: spinoff of [Glass-Box Alpha](https://github.com/Alexander-Sorrell-IT/glass-box-alpha) (Mantle Turing Test 2026 entry). ~70% architecture carries over.

---

## The thesis

A trader presses "what if I buy 1,000 AAPL shares from a Chinese broker?" The system shows:

- **Supply chain ripple**: AAPL demand → Foxconn ramp signal → TSMC up → German auto-supplier impact (Infineon, Aptiv, Continental) → DAX move
- **Currency ripple**: HKD pressure → USD/CNH widens → EUR/USD reaction → Bund yields shift
- **Sentiment cascade**: HK tech open → European tech sentiment → US futures
- **Correlation hop**: AAPL up + USD up → oil weak → energy stocks down → MSCI World rebalance

Output: probabilistic transmission graph + full reasoning chain + a cryptographic timestamp proving the prediction was made BEFORE the market reacted.

**Bloomberg Terminal costs $24K/year and gives you the data.** This gives you reasoning + verifiable predictions at $99/mo. ~5% of Bloomberg's price.

---

## Data sources — free tier first, paid later

### Free (Phase 1 MVP — sufficient for closed beta)
| Source | What | Limit | Cost |
|---|---|---|---|
| **Yahoo Finance** (`yfinance` Python lib) | US/EU/Asia equities, 15-min delayed, ~7 years history | Unlimited via scraping | $0 |
| **Alpha Vantage** | US equities, FX, crypto, technicals | 25 calls/day | $0 |
| **FRED API** (US Federal Reserve) | Macro indicators, rates, GDP, employment | Unlimited | $0 |
| **ECB Statistical Data Warehouse** | EU macro, ECB rates, EUR series | Unlimited | $0 |
| **World Bank API** | Country-level economic data | Unlimited | $0 |
| **SEC EDGAR API** | 10-K, 10-Q filings (supply chain mining) | Unlimited | $0 |
| **GDELT 2.0** | Global news events, real-time, 100+ languages | Unlimited | $0 |
| **Reddit API** | Sentiment proxy (WSB, r/stocks, r/investing) | 60 req/min | $0 |
| **OpenCorporates** | Corporate structure / subsidiaries | 50 lookups/day | $0 |
| **DeepSeek LLM** | Agent reasoning (deepseek-reasoner native CoT) | Pay-per-token | ~$0.14-0.55 per 1M tokens |
| **Groq** (backup LLM) | Llama 3 70B, fast | 30 req/min free | $0 |
| **Vercel** | Frontend hosting | Hobby tier | $0 |
| **Neon Postgres** | Database | 0.5GB free | $0 |
| **Upstash Redis** | Caching | 10K commands/day | $0 |

### Paid (Phase 2+ — institutional tier features)
| Source | What | Cost when needed |
|---|---|---|
| **Polygon.io Starter** | Delayed US equity API | $29/mo |
| **Polygon.io Developer** | Real-time US equity | $199/mo |
| **Tiingo** | Equity fundamentals + news | $30/mo |
| **X API Basic** | Real-time sentiment firehose | $200/mo |
| **NewsAPI** | Curated news headlines (5K req/day) | $449/mo |
| **OANDA** | Real-time FX | $20/mo |
| **RavenPack** (institutional only) | Pre-labeled sentiment events | $5-50K/mo |

### Brokerage / execution (if we add live trading later)
| Broker | Note | Cost |
|---|---|---|
| **Alpaca** | Free paper trading, free real US equity trading | $0 |
| **Interactive Brokers TWS API** | Global equities, $0 commission options available | $0 + per-trade fees |

---

## Architecture (~70% reuse from Glass-Box Alpha)

```
apple-of-the-world/
├── api/                              # FastAPI backend
│   ├── data_sources/
│   │   ├── yahoo.py                  # yfinance wrapper
│   │   ├── alpha_vantage.py
│   │   ├── fred.py                   # macro
│   │   ├── gdelt.py                  # news events
│   │   ├── sec_edgar.py              # 10-K supply chain extraction
│   │   ├── reddit.py                 # sentiment
│   │   └── x_api.py                  # Phase 2
│   ├── agents/                       # reuse Glass-Box Alpha pattern
│   │   ├── chronos.py                # historical analog mining for equities
│   │   ├── web.py                    # supply chain graph traversal
│   │   ├── mood.py                   # cross-region sentiment cascade
│   │   ├── devils_advocate.py        # counter-hypothesis: intervention, sanctions, guidance shifts
│   │   └── orchestrator.py           # Fold ensemble (same math)
│   ├── graph/
│   │   ├── supply_chain.py           # cached company-relationship graph
│   │   └── extractor.py              # LLM-powered 10-K → graph
│   ├── attestation/
│   │   └── mantle_anchor.py          # Mantle on-chain reasoning-hash commit (carry-over from Glass-Box)
│   └── main.py                       # FastAPI app
├── frontend/                         # Next.js — query input + transmission map viz
│   ├── app/
│   ├── components/
│   │   ├── TransmissionGraph.tsx     # D3 force-directed cascade visualization
│   │   ├── ReasoningStream.tsx       # carries over from Glass-Box
│   │   └── ScenarioInput.tsx
│   └── lib/
├── data/
│   └── supply_chains_seed.json       # curated initial graph (~500 major companies)
└── README.md
```

### What carries over from Glass-Box Alpha (~70%)
- 4-agent ensemble pattern
- Fold ensemble math
- Reasoning chain UI / live streaming
- DeepSeek backend (deepseek-reasoner)
- **On-chain reasoning attestation via Mantle** — the unique moat: every prediction's reasoning hash committed BEFORE outcome unfolds, providing cryptographically verifiable predictions
- Risk-management / circuit-breaker patterns (if we add execution)

### What's net-new (~30%)
- Equity data adapters (Yahoo/AV/FRED/etc instead of Nansen/Elfa)
- Supply chain graph data + LLM extraction pipeline
- Currency/FX integration
- Cross-region sentiment aggregation (GDELT, Reddit, X later)
- Transmission cascade visualization (D3 force graph, not just leaderboard)

---

## Cost estimates

### MVP (Phase 1 — first 90 days, 0 paying users)
| Item | Cost |
|---|---|
| All data (Yahoo + AV + FRED + GDELT + SEC + Reddit) | $0 |
| DeepSeek (moderate dev use) | ~$10/mo |
| Vercel + Neon + Upstash | $0 (free tiers) |
| Domain | ~$10/yr |
| **TOTAL** | **~$10-15/mo** |

3-month MVP burn: **~$50 total** for initial build + testing.

### Phase 2 (closed beta, 50-200 free + ~50 paying users)
| Item | Cost |
|---|---|
| Polygon.io Starter (delayed real-time) | $29/mo |
| Tiingo (news + fundamentals) | $30/mo |
| DeepSeek (scaled usage) | ~$50/mo |
| Vercel Pro | $20/mo |
| Neon Pro | $19/mo |
| **TOTAL** | **~$148/mo** |

Revenue at 50 paying × $99 = $4,950/mo. **97% gross margin.**

### Phase 3 (1,000 paying users — institutional ready)
| Item | Cost |
|---|---|
| Polygon.io Developer (real-time) | $199/mo |
| Tiingo | $30/mo |
| X API Basic | $200/mo |
| NewsAPI | $449/mo |
| DeepSeek at scale | ~$300/mo |
| Vercel + Neon + Upstash scaled | ~$200/mo |
| Vector DB (Pinecone Serverless) | ~$70/mo |
| **TOTAL** | **~$1,450/mo** |

Revenue at 1,000 paying × $99 = $99K/mo. **98.5% gross margin.**

### Phase 4 (institutional tier, $2-5K/mo customers)
Real-time NYSE + NASDAQ + LSE + HKEX licensing kicks in: ~$15-30K/mo total infra. Still ~95% gross margin at $100K+ ARR.

---

## Roadmap

| Phase | When | Effort | Outcome |
|---|---|---|---|
| **Phase 0 — Design** | Now (this doc) | 1 hour | This file |
| **Phase 1 — MVP scaffold** | Late June 2026 (after Glass-Box Alpha submits Jun 15 + Mantle awards Jul 10) | 2-3 weeks | Working backend + frontend with free data sources, 1 worked example end-to-end |
| **Phase 2 — Closed beta** | August–September 2026 | 6-8 weeks | 50-200 free users, 50-100 paying retail at $99/mo |
| **Phase 3 — Institutional pilot** | October–December 2026 | 8-12 weeks | 2-3 paying institutional pilots at $2-5K/mo |
| **Phase 4 — Real revenue** | Q1 2027 | ongoing | $50-100K MRR target |

**Conservative year-1 revenue target**: 500 retail × $99 + 10 institutional × $3K = **$80K MRR ≈ $1M ARR**.

---

## The unique angle

**On-chain attestation as a regulatory + credibility moat.** Every prediction's reasoning chain gets hashed on Mantle BEFORE the outcome unfolds. Anyone can verify the prediction wasn't backfit. No Bloomberg / Refinitiv / Koyfin / Seeking Alpha has this. For institutional users this is auditable timestamping. For retail it's marketing ("watch the AI think out loud, then watch its receipts settle on-chain").

The pitch: **"the first equity research tool whose predictions are cryptographically verifiable as having been made before the outcome."**

---

## Next action

After Glass-Box Alpha ships and the Mantle Turing Test concludes (Jul 10, 2026), revisit this doc, kick off Phase 1.

Until then: **DO NOT pivot focus**. Glass-Box Alpha is the priority through June 15.
