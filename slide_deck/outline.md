## 1. Introduction (2 slides)
- Motivation + two central questions
- Dataset facts (9,725 features, 36 time points, 25-min intervals)

## 2. Data Processing (1 slide)
- All three preprocessing steps with the arcsinh formula written out; explain the assumption behind per-chip median normalization

## 3. Phase I: Period Discovery (3 slides)
- The clustering-over-time-points strategy
- Figure slide using rough-cell-expression-cycles.png and rough-gmm-classes.png
- Summary: $T = 12 \times 25 = 300$ min, confirmed across all DR methods

## 4. Phase II: Gene Analysis (5 slides)
- Permutation test on lag-12 ACF → 5,243/9,725 genes periodic
- PCA figure (periodic vs. non-periodic)
- Seasonal decomposition math + GMM cluster selection (BIC → $k=8$, 7 real clusters)
- Seasonal cluster figure
- ARMA and CDHMM slides


## 5. Semantic Annotation via LDA (3 slides)
- LDA motivation (when → why)
- Table of all 10 Claude-labeled topics
- Heatmap slide (placeholder for the exported figure from semantic_2.ipynb) + key biological findings

## 6. Conclusions

## Backup
- GMM selection, permutation test math, LDA generative model