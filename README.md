<div align="center">

<h1>FinSeer</h1>

<p><b>Retrieval-augmented Large Language Models for Financial Time Series Forecasting</b></p>

[![arXiv](https://img.shields.io/badge/arXiv-2502.05878-b31b1b.svg)](https://arxiv.org/abs/2502.05878)
[![Hugging Face Collection](https://img.shields.io/badge/%F0%9F%A4%97%20Hugging%20Face-Collection-yellow?logo=huggingface)](https://huggingface.co/collections/TheFinAI/finseer-retrieval-augmented-time-series-forecasting-6ac7153f1e8bbfa51e7bd031)
[![Model: FinSeer](https://img.shields.io/badge/%F0%9F%A4%97%20Model-FinSeer-yellow?logo=huggingface)](https://huggingface.co/TheFinAI/FinSeer)
[![Model: StockLLM](https://img.shields.io/badge/%F0%9F%A4%97%20Model-StockLLM-yellow?logo=huggingface)](https://huggingface.co/TheFinAI/StockLLM)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

<a href="https://arxiv.org/abs/2502.05878">Paper</a> ·
<a href="https://huggingface.co/collections/TheFinAI/finseer-retrieval-augmented-time-series-forecasting-6ac7153f1e8bbfa51e7bd031">Models</a>

</div>

## Overview

This repository contains the code for **FinSeer**, a domain-specific retriever for financial time-series forecasting, used in a retrieval-augmented generation (RAG) framework for stock movement prediction. FinSeer is trained with candidate selection refined by LLM feedback and a similarity-driven objective, and the retrieved sequences are fed into **StockLLM**, an LLM fine-tuned for stock movement prediction. The code covers indicator calculation, retriever training, and stock movement prediction with and without retrieval.

## Resources on Hugging Face

| Resource | Type | Description |
|---|---|---|
| [FinSeer collection](https://huggingface.co/collections/TheFinAI/finseer-retrieval-augmented-time-series-forecasting-6ac7153f1e8bbfa51e7bd031) | Collection | All FinSeer resources |
| [TheFinAI/FinSeer](https://huggingface.co/TheFinAI/FinSeer) | Model | Our retriever |
| [TheFinAI/StockLLM](https://huggingface.co/TheFinAI/StockLLM) | Model | Our fine-tuned stock LLM |

## Repository Structure

```
src/
├── 1_calulate_indicators/          # financial indicator calculation
├── 2_train_retriever/              # LLM feedback scoring and candidate selection
└── 3_stock_movement_prediction/    # embeddings, similarity, and RAG prediction
```

## Installation

```shell
# for baseline RAG models and retriever training
pip install InstructorEmbedding
pip install -U FlagEmbedding
pip install sentence-transformers==2.2.2
pip install protobuf==3.20.0
pip install yahoo-finance
python -m pip install -U angle-emb
pip install transformers==4.33.2  # UAE
```

## Train the Retriever

**Step 1. Get LLM feedback scores** (`src/2_train_retriever/get_llm_feedback_scores.py`)

- `dataset`: `acl18`, `bigdata22`, `stock23`
- `target`: the file to save, a JSON with LLM probability scores

```python
parser = argparse.ArgumentParser(description='test')
parser.add_argument('--dataset', default='acl18', type=str)
parser.add_argument('--target', default='acl18.scored.json', type=str)
args = parser.parse_args()
get_all_scores(llm='StockLLM')  # llama family are all supported for this code
```

**Step 2. Select positive and negative candidates** (`src/2_train_retriever/select_positive_and_combine.py`)

Before this step, you should have generated `acl18.scored.json`, `bigdata22.scored.json` and `stock23.scored.json`.

This step selects candidates for all three datasets and generates a combined `train.scored.json` file.

Then, follow the steps in [this link](https://github.com/FlagOpen/FlagEmbedding/tree/master/examples/finetune/embedder) to fine-tune your own FinSeer using the `train.scored.json` data.

## Predict Stock Movement with the RAG Model

**Step 1. Get embeddings of queries and candidates** (`src/3_stock_movement_prediction/get_embeddings.py`)

- `q_or_c`: query or candidate; we generate the embeddings of query sequences and candidate sequences separately

```python
parser = argparse.ArgumentParser(description='test')
parser.add_argument('--test_dataset', default='bigdata22', type=str)
parser.add_argument('--embedding_model', default='e5',
                    choices=['instructor', 'uae', 'bge', 'llm_embedder', 'e5', 'FinSeer'])
parser.add_argument('--q_or_c', default='candidate')
args = parser.parse_args()
```

**Step 2. Calculate the similarity of queries and qualified candidates** (and get the top-5 related candidates).

**Step 3. Predict stock movement with or without retrieval.** This is what our three files do:

- `1_no_retrieval.py`
- `2_random_retrieval.py`
- `3_similarity_retrieval.py`

## Citation

If you find FinSeer useful, please cite:

```bibtex
@misc{xiao2025retrievalaugmented,
  title={Retrieval-augmented Large Language Models for Financial Time Series Forecasting},
  author={Mengxi Xiao and Zihao Jiang and Lingfei Qian and Zhengyu Chen and Yueru He and Yijing Xu and Yuecheng Jiang and Dong Li and Ruey-Ling Weng and Min Peng and Jimin Huang and Sophia Ananiadou and Qianqian Xie},
  year={2025},
  eprint={2502.05878},
  archivePrefix={arXiv},
  primaryClass={cs.CL}
}
```

## License

The code in this repository is released under the [MIT License](LICENSE). Datasets and models on Hugging Face keep their own licenses, stated on each card.

---

<div align="center">

Built by [The Fin AI](https://thefin.ai) · [Hugging Face](https://huggingface.co/TheFinAI) · [GitHub](https://github.com/The-FinAI)

</div>
