# Comparing BERT and RoBERTa for Imbalanced Sentiment Classification of *Black Myth: Wukong* Steam Reviews

Data and code for the paper **"Comparing BERT and RoBERTa for Imbalanced Sentiment Classification of Black Myth: Wukong Steam Reviews"** by Muhammad Raihan Abaid Hasrawi, Fitriyani and Lazuardy Syahrul Darfiansa (Faculty of Informatics, Telkom University).

The study fine-tunes `bert-base-uncased` and `roberta-base` for three-class sentiment classification (positive, negative, neutral) of Steam reviews under four imbalance-handling scenarios: original data, random oversampling, random downsampling and a class-weighted loss. Each configuration is run with five random seeds and compared with TF-IDF baselines using paired bootstrap tests.

## Repository contents

| File | Description |
|---|---|
| `RAW DATA.xlsx` | Raw reviews collected on 10 November 2025 from the game's public Steam review page (8,910 rows; app ID 2358720), covering August 2024 to November 2025. Sorted by Steam's "most helpful" ranking with the English filter. |
| `DATA SENTIMEN.xlsx` | Preprocessed review text (`Text`) and the final manual sentiment label (`Sentimen`: Positif, Negatif, Netral), 6,377 rows before deduplication. |
| `anotasi ulang.xlsx` | Inter-annotator agreement sample: 400 reviews drawn at random from the 5,042 deduplicated reviews and labeled independently by three additional annotators (`Anotasi` sheet). The original labels are in the `Kunci` sheet. |
| `sentiment seri.xlsx` | The 19 reviews from the agreement sample that were tied 2–2 among the four annotators, with the label agreed after discussion. |
| `cek bahasa.xlsx` | Manual check of 100 random reviews flagged as non-English by the `lingua` language detector. |
| `Code.ipynb` | Complete experiment notebook (Google Colab, NVIDIA T4 GPU). |

### Columns of `RAW DATA.xlsx`

| Column | Description |
|---|---|
| `appId` | Steam app ID of *Black Myth: Wukong* |
| `reviewText` | Review text as written by the user |
| `reviewResult` | Recommendation flag (`Recommended` / `Not Recommended`) |
| `postedDate` | Posting date as shown by Steam; dates without a year are from 2025 (data collected on 10 November 2025) |
| `hoursOnRecord` | Reviewer's playtime at the time of the review |
| `helpfullNum`, `rewardNum`, `reviewCommnetCount` | Helpful/funny votes, awards and comment count |

User identifiers (account names, profile URLs, avatars and product counts) were removed before publication.

## Dataset at a glance

| Step | Reviews |
|---|---|
| Collected | 8,910 |
| After removing empty text | 8,884 |
| After text cleaning | 8,545 |
| After language filtering (langdetect) | 6,377 |
| After removing one unlabeled record | 6,376 |
| After deduplication (final dataset) | 5,042 (3,684 positive, 977 neutral, 381 negative) |

The final dataset is split 80:10:10 into training, validation and test sets (stratified, seed 42): 4,033 / 504 / 505 reviews.

**Labels.** All reviews were labeled by one annotator. A review is *positive* if it mainly conveys satisfaction or praise, *negative* if it mainly conveys dissatisfaction or criticism, and *neutral* otherwise (including memes, jokes and off-topic text). Sarcasm is labeled according to its intended meaning. On the random 400-review agreement sample, Fleiss' κ across four annotators is 0.70.

## Running the experiments

1. Open `Code.ipynb` in Google Colab and select a **T4 GPU** runtime.
2. Upload `RAW DATA.xlsx` and `DATA SENTIMEN.xlsx`, or clone this repository, and point `DATA_RAW` and `DATA_LABELED` in the configuration cell to them (for example, `DATA_RAW = '/content/PAPER-IEEE/RAW DATA.xlsx'`).
3. Run all cells. Results are written to Google Drive (`OUT_DIR`), and finished runs are skipped if the session restarts.

Main settings: maximum length 128 tokens, batch size 32, learning rate 2 × 10⁻⁵, AdamW (weight decay 0.01), linear warm-up over 10% of steps, at most 10 epochs with early stopping (patience 2) on validation macro F1, seeds 42–46. The 50 fine-tuning runs (40 main runs and 10 preprocessing-ablation runs) take about 7 hours on a T4.

Required packages: `transformers`, `torch`, `scikit-learn`, `imbalanced-learn`, `langdetect`, `openpyxl`, `sentencepiece`, `scipy`.

## Main results (test set, mean over five seeds)

| Model | Scenario | Macro F1 | Negative recall |
|---|---|---|---|
| BERT | Original | 0.770 | 0.605 |
| BERT | Class-weighted | 0.782 | 0.700 |
| RoBERTa | Original | 0.785 | 0.668 |
| RoBERTa | Class-weighted | 0.787 | 0.758 |
| TF-IDF + SVM (best baseline) | Class-weighted | 0.711 | 0.395 |

See the paper for all scenarios, standard deviations and significance tests.

## Terms of use

The review texts were written by Steam users and are shared here for non-commercial research only. Please cite the paper if you use this data or code.
