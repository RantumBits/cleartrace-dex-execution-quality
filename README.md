# ClearTrace — Open DEX Execution-Quality Dataset

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.21089929.svg)](https://doi.org/10.5281/zenodo.21089929)
[![License: CC BY 4.0](https://img.shields.io/badge/License-CC%20BY%204.0-blue.svg)](https://creativecommons.org/licenses/by/4.0/)

Cross-frontend DEX execution-quality metrics from on-chain trace data, for
Ethereum, Base, Arbitrum, and Optimism. Median slippage, router revert rates,
and a slice by **originating frontend** — the dimension most public DEX datasets
collapse.

Built by [ClearTrace](https://cleartracedata.com), a cross-frontend DEX
attribution engine. This repo is the canonical open-data mirror; the same data
is on [Kaggle](https://www.kaggle.com/datasets/andrewmaury/cleartrace-dex-execution-quality)
and [Hugging Face](https://huggingface.co/datasets/andrewmaury/cleartrace-dex-execution-quality).

**Snapshot:** see [`EXPORTED_AT.txt`](EXPORTED_AT.txt). Refreshed periodically.

## Files

| File | What it is |
|------|-----------|
| [`execution_slippage.csv`](execution_slippage.csv) | Median on-chain execution slippage (bps) by aggregator project, per chain |
| [`revert_rates.csv`](revert_rates.csv) | On-chain router revert rate across **all** routing txs, per chain |
| [`execution_by_frontend.csv`](execution_by_frontend.csv) | Median slippage by originating frontend + attribution-confidence tier (the ClearTrace-unique slice) |
| [`leaderboard.json`](leaderboard.json) | Joined board: on-chain execution + revert + preliminary quote-accuracy samples |

Full column docs, methodology, and caveats are in [`DATASET.md`](DATASET.md).

## What makes it different

Most DEX analytics aggregate by *protocol* or *router contract*. ClearTrace
attributes each swap to the **frontend that originated it** using four vectors
(calldata-suffix trapping, proxy-router hunting, multi-hop origin tracing,
fee-recipient attribution) — so you can compare execution quality across *who
actually routed the trade*, not just which contract settled it.

Unresolved trades stay `unattributed` rather than getting a likely-wrong label.

## License

[CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) — free to use, including
commercially; just credit **ClearTrace (cleartracedata.com)**.

## Citation

Archived on Zenodo with a citable DOI. Cite the **concept DOI** (always resolves
to the latest version):

> ClearTrace. *ClearTrace — Open DEX Execution-Quality Dataset.* Zenodo.
> https://doi.org/10.5281/zenodo.21089929

```bibtex
@dataset{cleartrace_dex_execution_quality,
  author    = {ClearTrace},
  title     = {ClearTrace --- Open DEX Execution-Quality Dataset},
  publisher = {Zenodo},
  doi       = {10.5281/zenodo.21089929},
  url       = {https://doi.org/10.5281/zenodo.21089929}
}
```

## Links

- Live dashboard & API: https://cleartracedata.com
- Leaderboard: https://cleartracedata.com/leaderboard
- Methodology write-ups: https://cleartracedata.com/research
