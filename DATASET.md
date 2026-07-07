# ClearTrace — DEX Aggregator Execution Quality Dataset

On-chain execution-quality metrics for Ethereum DEX aggregators, sliced by the
**originating frontend** — the dimension most public DEX datasets collapse. Built
by [ClearTrace](https://cleartracedata.com), a cross-frontend DEX attribution
engine that identifies hidden frontends and institutional routing from on-chain
trace data.

**Data snapshot:** see `EXPORTED_AT.txt` (refreshed periodically). Chains: Ethereum, Base, Arbitrum, Optimism.

---

## Why this dataset is different

Most DEX analytics aggregate by *protocol* or *router contract*. ClearTrace
attributes each swap to the **frontend that originated it** (a wallet's embedded
swap, an aggregator API call, a named UI, or an unattributed router) using four
attribution vectors: calldata-suffix trapping, proxy-router hunting (tx_to
mismatch), multi-hop origin tracing, and fee-recipient attribution.

That lets you compare execution quality across *who actually routed the trade*,
not just which contract settled it.

---

## Files

### `execution_slippage.csv`
Median on-chain execution slippage by aggregator project, per chain (plus an
`all`-chains roll-up). Slippage is in basis points (bps); lower is better.

| Column | Description |
|--------|-------------|
| `project` | Aggregator/DEX project name (Dune labeling) |
| `category` | `Aggregator`, or `DEX` for the Uniswap baseline |
| `blockchain` | `all`, `ethereum`, `base`, `arbitrum`, `optimism` |
| `avg_slippage_bps` | Median execution slippage, basis points |
| `toxicity_score` | Internal 0–100 toxicity proxy (≈ slippage-derived) |
| `total_trades` | Trade count in the cell |

Includes Aggregator rows plus the Uniswap DEX-venue baseline (the implicit
"default" everyone is compared against). Cells are significance-gated.

### `revert_rates.csv`
On-chain router revert rate across **all** routing transactions (not a sample),
after a sender-level bot filter (methodology v5, 2026-07-05) that excludes
competing solver/keeper bots: senders with zero successful txs in the window,
senders reverting ≥50% of 5+ txs, and senders with 100+ reverts at a ≥10%
personal rate. Their failed fill races are not user execution failures — before
this filter they inflated solver-heavy routers' headline rates up to 20×.
A reverted trade costs gas and delivers nothing — a real, often-ignored cost.
Known residual: the Odos/Ethereum cell remains inflated by address-rotating
spam (genuine user rate ≈0.2%); exports before 2026-07-05 used the looser v4
filter.

| Column | Description |
|--------|-------------|
| `project` | Aggregator/router project |
| `blockchain` | Chain |
| `revert_rate_pct` | Reverted ÷ total routing txs, percent |
| `reverted_txs` | Count of reverted routing txs |
| `total_routing_txs` | Total routing txs observed |

### `execution_by_frontend.csv`
The slice unique to ClearTrace: median slippage by **originating frontend**, with
an attribution-confidence tier. Cells require ≥300 trades.

| Column | Description |
|--------|-------------|
| `blockchain` | Chain (`all` = pooled) |
| `bucket` | Taxonomy: `named_frontend`, `aggregator_api_direct`, `wallet_embedded`, `unattributed` |
| `originating_frontend` | Resolved frontend (e.g. `uniswap: UniversalRouter`, `metamask: MetaSwap`); blank when unattributed |
| `confidence` | Attribution confidence: `high` / `medium` / `low` / `none` |
| `total_trades` | Trade count in the cell |
| `median_slippage_bps` | Median slippage, basis points |
| `execution_quality_score` | 0–100 (higher = better execution) |

### `leaderboard.json`
The joined board: on-chain execution + revert (Component A, high-volume) merged
with forward-captured quote-accuracy samples (Component B). Each metric comes
from where it is credible.

- `onchain_slippage_bps`, `onchain_revert_rate_pct`, `onchain_trades`,
  `onchain_routing_txs` — on-chain, all trades.
- `quote_median_gap_bps`, `quote_realized_samples`, `quote_confidence` — a small,
  still-accruing fork-simulation sample. **`quote_confidence: preliminary`** means
  the quote-gap figures are characterization-grade, not yet statistically rated.

---

## A few things the data shows

- **Reverts vary widely across aggregators.** On Ethereum they range from CoW
  Protocol near 0.04% to several percent for solver-heavy routers. (An earlier
  version of this note cited Odos at 19.86% — a sender-concentration audit on
  2026-07-05 showed that figure was dominated by address-rotating solver bots,
  not failing user transactions; Odos's genuine user rate is ≈0.2%. Methodology
  v5 filters these senders.)
- **Slippage is not uniform.** Ethereum median slippage runs from bitget_dex_aggregator
  at 2.32 bps to tokenlon at 64.48 bps.
- **Frontend matters.** A Uniswap UniversalRouter-originated trade on Ethereum
  shows 2.54 bps median slippage (high confidence), while the large `unattributed`
  / `none`-confidence pool sits at 12.83 bps — the spread the frontend dimension
  surfaces.

These are observations from one snapshot, not advice. Reproduce them from the
CSVs before citing.

---

## Methodology & caveats

- **Source:** Dune Analytics trace data, aggregated by ClearTrace's attribution
  kernel; quote accuracy via Alchemy `eth_simulateV1` fork-simulation.
- **Slippage** is median realized vs. expected output at fill grain, in bps.
- **Significance gating:** per-frontend cells require ≥300 trades; sparse cells
  are dropped, not shown thin.
- **`unattributed` is honest, not a guess.** When the vectors don't resolve a
  frontend, the trade stays `unattributed` rather than being assigned a likely-
  wrong label. The large `none`-confidence pool is the genuinely unresolved tail.
- **Quote accuracy is preliminary** (small sample, fork-sim) — treat
  `quote_median_gap_bps` as directional until `quote_confidence` reads `rated`.
- **One snapshot.** See `EXPORTED_AT.txt` for the capture date; aggregator behavior shifts over time.

## License

Released under **CC BY 4.0**. Use it freely, including commercially — just credit
**ClearTrace (cleartracedata.com)** and link back where practical.

## Links

- Live dashboard & API: https://cleartracedata.com
- Methodology write-ups: https://cleartracedata.com/research
- Also on Kaggle: https://www.kaggle.com/datasets/andrewmaury/cleartrace-dex-execution-quality
- Also on Hugging Face: https://huggingface.co/datasets/andrewmaury/cleartrace-dex-execution-quality
- Also on GitHub: https://github.com/RantumBits/cleartrace-dex-execution-quality
- Citable DOI (Zenodo): https://doi.org/10.5281/zenodo.21089929
