# Hybrid post-quantum key exchange in production TLS 1.3

[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.24456092.svg)](https://doi.org/10.5281/zenodo.24456092)

Code and data for the manuscript *Hybrid Post-Quantum Key Exchange in Production TLS 1.3: A Reproducible Measurement of Overhead and Its Implications for Migration* (Judith Ann Sarjeant, submitted to the ITU Journal on Future and Evolving Technologies, special issue on quantum-safe communication, 2026).

The study compares classical **X25519** with hybrid **X25519MLKEM768** (X25519 + ML-KEM-768, FIPS 203; RFC 10024) in real TLS 1.3 handshakes against three public endpoints: cloudflare.com, pq.cloudflareresearch.com and www.google.com. All measurements were made with stock OpenSSL 3.5.4 from one cloud vantage (qBraid Lab, served by Cloudflare's IAD data centre in Ashburn, Virginia).

**Results dashboard:** https://sarjeant.github.io/pqc-hybrid-tls-handshakes/ (interactive charts and tables; source in `docs/index.html`)

**Dataset:** 7 sessions, 1,260 handshakes (180 per session: 30 rounds × 3 endpoints × 2 groups, in shuffled order within each round). Every handshake succeeded and negotiated the requested group.

## Contents

| Path | What it is |
|---|---|
| `pqc_hybrid_handshake_qbraid.ipynb` | Measurement and analysis notebook (setup, sanity check, sessions, ClientHello capture, paired analysis) |
| `results_qBraid_cloud.csv` | Session 1, 1 October 2026 (180 handshakes) |
| `sessions/` | Sessions 2–7, 21–23 November 2026 UTC (180 handshakes each) |
| `sessions_excluded/` | One run started 45 s before session 2 that duplicated it; excluded before analysis and kept for transparency |
| `hello_sizes.csv` | ClientHello and ServerHello lengths per endpoint and group (21 November 2026) |
| `hello_sizes_raw.txt` | Full `openssl s_client -msg` output behind `hello_sizes.csv` |
| `analysis_outputs/` | Outputs of notebook section 6: `paired_summary.csv`, `per_session_summary.csv`, `fig_paired.png` |
| `.zenodo.json` | Metadata Zenodo uses when archiving each release |
| `docs/index.html` | Results dashboard, published with GitHub Pages. Self-contained: the summary data it shows are embedded in the page |

## Sessions

| Session | Start (UTC) | File |
|---|---|---|
| 1 | 2026-10-01 20:19 | `results_qBraid_cloud.csv` |
| 2 | 2026-11-21 17:37 | `sessions/session_qBraid_cloud_20261121T173729Z.csv` |
| 3 | 2026-11-21 23:06 | `sessions/session_qBraid_cloud_20261121T230614Z.csv` |
| 4 | 2026-11-22 05:57 | `sessions/session_qBraid_cloud_20261122T055726Z.csv` |
| 5 | 2026-11-22 17:05 | `sessions/session_qBraid_cloud_20261122T170517Z.csv` |
| 6 | 2026-11-23 00:13 | `sessions/session_qBraid_cloud_20261123T001312Z.csv` |
| 7 | 2026-11-23 04:55 | `sessions/session_qBraid_cloud_20261123T045556Z.csv` |

## Columns

One row per handshake attempt.

| Column | Meaning |
|---|---|
| `vantage` | Measurement location label (`qBraid cloud` throughout) |
| `utc_start` | Session start time |
| `session_id` | Session identifier (UTC timestamp). Sessions 2–7 only; for session 1 it is derived from `utc_start` |
| `session_note` | Free-text label given at run time. Sessions 2–7 only |
| `round` | Round number within the session (0–29) |
| `order_in_round` | Position of the attempt within its shuffled round (0–5). Sessions 2–7 only |
| `host`, `group` | Endpoint and requested key-exchange group |
| `ok` | Handshake completed |
| `negotiated` | Group reported by OpenSSL |
| `total_ms` | Wall-clock duration of the `s_client` process |
| `handshake_ms` | `total_ms` minus `overhead_ms` (startup-adjusted handshake time) |
| `tcp_rtt_ms` | Time for a separate TCP connection made just before the handshake (includes name resolution) |
| `dns_ms` | Name-resolution time measured on its own. Sessions 2–7 only |
| `bytes_sent`, `bytes_recv` | OpenSSL's handshake byte counters ("written" and "read") |
| `overhead_ms` | OpenSSL start-up time for the session (median of 15 `openssl version` runs) |
| `cf_colo` | Cloudflare data centre serving the session |
| `openssl` | OpenSSL version string. Sessions 2–7 only |

All times are in milliseconds.

## Reproducing the analysis

The analysis runs from this folder with Python 3, pandas, numpy, matplotlib and scipy:

1. Open `pqc_hybrid_handshake_qbraid.ipynb` in Jupyter from the repository root.
2. Run section 0 (settings), then section 6 (paired and multi-session analysis).

Section 6 reads `results_qBraid_cloud.csv` and `sessions/`, ignores `sessions_excluded/`, and uses fixed seed 20261119. It writes `paired_summary.csv`, `per_session_summary.csv` and `fig_paired.png` to the repository root; compare them with `analysis_outputs/`.

## Running new measurements

Sections 1–5 need a Linux environment with outbound HTTPS. Section 1 installs the Python packages and builds OpenSSL 3.5.4 in your home folder if no OpenSSL 3.5+ is found; nothing system-wide is changed. For a new session run sections 0, 1 and 2, then section 4; run section 5 once. Results depend on the network path, so new sessions from other locations will differ from these.

## Licence

- Code (the notebook): MIT licence, see `LICENSE`.
- Data (CSV and text files): Creative Commons Attribution 4.0 International, see `LICENSE-DATA.md`.

## Citation

Please cite the archived release:

> Sarjeant, J. A. (2026). *Hybrid post-quantum key exchange in production TLS 1.3: measurement code and data* (version 1.0.1) [Data set]. Zenodo. https://doi.org/10.5281/zenodo.24456092

GitHub's "Cite this repository" button (from `CITATION.cff`) gives the same reference in other formats.

## Author

Judith Ann Sarjeant · ORCID [0009-0004-5012-9694](https://orcid.org/0009-0004-5012-9694)
