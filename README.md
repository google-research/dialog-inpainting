# Dialog Inpainting: Turning Documents into Dialogs

## Abstract
Many important questions (e.g. "How to eat healthier?") require conversation to establish context and explore in depth.
However, conversational question answering (ConvQA) systems have long been stymied by scarce training data that is expensive to collect.
To address this problem, we propose a new technique for synthetically generating diverse and high-quality dialog data: *dialog inpainting*.
Our approach takes the text of any document and transforms it into a two-person dialog between the writer and an imagined reader:
we treat sentences from the article as utterances spoken by the writer, and then use a dialog inpainter to predict what the imagined reader asked or said in between each of the writer's utterances.
By applying this approach to passages from Wikipedia and the web, we produce `WikiDialog` and `WebDialog`, two datasets totalling 19 million diverse information-seeking dialogs---1,000x larger than the largest existing ConvQA dataset.
Furthermore, human raters judge the *answer adequacy* and *conversationality* of `WikiDialog` to be as good or better than existing manually-collected datasets.
Using our inpainted data to pre-train ConvQA retrieval systems, we significantly advance state-of-the-art across three benchmarks (`QReCC`, `OR-QuAC`, `TREC CaST`) yielding up to 40\% relative gains on standard evaluation metrics.

## Disclaimer
This is not an officially supported Google product.

# `WikiDialog-OQ`

We are making `WikiDialog-OQ`, a dataset containing 11M information-seeking conversations from passages in English Wikipedia, publicly available.
Each conversation was generated using the dialog inpainting method detailed in the paper using the `Inpaint-OQ` inpainter model, a T5-XXL model that was fine-tuned on `OR-QuAC` and `QReCC` using a dialog reconstruction loss.
The passages come from the `OR-QuAC` retrieval corpus and share passage ids.
You can download the `OR-QuAC` dataset and find more details about it [here](https://github.com/prdwb/orconvqa-release).

## Accessing the dataset

The dataset can be downloaded in raw JSON format from [Google Cloud](), or accessed via [TFDS](https://www.tensorflow.org/datasets/catalog/wiki_dialog) using the following snippet:

```
>>> import tensorflow_datasets as tfds
>>> ds = tfds.load('wiki_dialog/oq')
>>> # Print the first example from the dataset
>>> print(next(iter(ds)))
```

Note: This may require [the nightly build of TFDS](https://www.tensorflow.org/datasets/overview#installation).

## Data card (TODO)

## Citing WikiDialog

```
@inproceedings{dai2022dialoginpainting,
  title={Dialog Inpainting: Turning Documents to Dialogs},
  author={Dai, Zhuyun and Chaganty, Arun Tejasvi and Zhao, Vincent and Amini, Aida and Green, Mike and Rashid, Qazi and Guu, Kelvin},
  booktitle={International Conference on Machine Learning (ICML)},
  year={2022},
  organization={PMLR}
}
```
