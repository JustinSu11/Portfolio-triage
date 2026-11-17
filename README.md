# Portfolio Triage

A news triage tool for an investing portfolio.
It does NOT predict prices. It answers one question:
**"What should I read first about the things I own?"**

Analogy: an ER triage nurse. She doesn't cure anyone.
She decides who gets seen first.

## Scope guardrails (re-read when drifting)

- Output is information, not financial advice.
- Rank by relevance + severity. No price prediction.
- Trained model is the main project. An LLM/NLP, if used,
  is only a data tool (confirm with professor first).
- Finish the baseline before anything fancy.

## Pipeline

1. **Holdings in:** ticker + portfolio weight (e.g. NVDA 12%).
2. **News in:** FNSPID slice (ticker-tagged, CC BY 4.0).
3. **Relevance:**
   - Direct: article names the holding.
   - Indirect: article hits a related company/sector
     (TSMC, AMD, export rules). A company graph spreads
     relevance outward, decaying per hop.
4. **Direction:** good / bad / neutral for the holding.
5. **Magnitude:** high / medium / low.
6. **Score:** `relevance x magnitude x portfolio weight`.
7. **Output:** top 5 stories per day.

Example output: `HIGH | negative | NVDA (12%) | <headline>`

## Models

| Role | Model |
|---|---|
| Baseline | Logistic regression; "keyword match, newest first" |
| Main | Gradient-boosted trees (hop distance, source, sentiment, cluster size, weight) |
| Unsupervised | Cluster duplicate stories so one event doesn't flood the digest |

## Labels and metrics

- Weak labels: abnormal return (stock minus SPY) the day
  after the article. Noisy proxy for magnitude only.
- Ground truth: ~200 hand-labeled articles.
- Metrics: macro-F1 (classes), NDCG@5 (ranking).

## Milestones

- [ ] 1. Download FNSPID slice (~5 tickers), inspect columns
- [ ] 2. Hand-build ~20-node company graph
- [ ] 3. Hand-label 50 articles, check labeling consistency
- [ ] 4. Confirm LLM/NLP scope with professor
- [ ] 5. Baselines + metrics
- [ ] 6. Feature engineering + GBT
- [ ] 7. Duplicate-story clustering
- [ ] 8. Daily top-5 digest demo
- [ ] 9. Write-up (limits: noisy labels, not advice)

## Known risks

- Price-reaction labels are noisy. Say so in the write-up.
- FNSPID sentiment labels are undocumented. Inspect first.
- Dataset terms: verify license before use.

## Setup

TBD once code exists. Keep secrets in `.env` (git-ignored).

## Resources

- FNSPID dataset (news + prices, ticker-tagged):
  https://github.com/Zdong104/FNSPID_Financial_News_Dataset
- FNSPID paper: https://arxiv.org/abs/2402.06698v1
