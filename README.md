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
Each conversation was generated using the dialog inpainting method detailed in the paper using the `Inpaint-OQ` inpainter model, a T5-XXL model that was fine-tuned on `OR-QuAC` and `QReCC` using a dialog reconstruction loss. For a detailed summary of the dataset, please refer to the [data card](WikiDialog-OQ_Data_Card.pdf).

The passages in the dataset come from the `OR-QuAC` retrieval corpus and share passage ids.
You can download the `OR-QuAC` dataset and find more details about it [here](https://github.com/prdwb/orconvqa-release).

## Download the raw JSON format data.

The dataset can be downloaded in (gzipped) JSON format from Google Cloud using the following commands:

```bash
# Download validation data (72Mb)
wget https://storage.googleapis.com/gresearch/dialog-inpainting/WikiDialog_OQ/data_validation.jsonl.gz
# Download training data (100 shards, about 72Mb each)
wget $(seq -f "https://storage.googleapis.com/gresearch/dialog-inpainting/WikiDialog_OQ/data_train.jsonl-%05g-of-00099.gz" 0 99)
```

Each line contains a single conversation serialized as a JSON object, for example:
```json
{
 "pid": "894686@1",
 "title": "Mother Mary Alphonsa",
 "passage": "Two years after Nathaniel's death in 1864, Rose was enrolled at a boarding school run by Diocletian Lewis in nearby Lexington, Massachusetts; she disliked the experience. After Nathaniel's death, the family moved to Germany and then to England. Sophia and Una died there in 1871 and 1877, respectively. Rose married author George Parsons Lathrop in 1871. Prior to the marriage, Lathrop had shown romantic interest in Rose's sister Una. Their brother...",
 "sentences": [
   "Two years after Nathaniel's death in 1864, Rose was enrolled at a boarding school run by Diocletian Lewis in nearby Lexington, Massachusetts; she disliked the experience.",
   "After Nathaniel's death, the family moved to Germany and then to England.",
   "Sophia and Una died there in 1871 and 1877, respectively.",
   "Rose married author George Parsons Lathrop in 1871.",
   "Prior to the marriage, Lathrop had shown romantic interest in Rose's sister Una.",
   "..."], 
 "utterances": [
    "Hi, I'm your automated assistant. I can answer your questions about Mother Mary Alphonsa.", 
    "What was Mother Mary Alphonsa's first education?",
     "Two years after Nathaniel's death in 1864, Rose was enrolled at a boarding school run by Diocletian Lewis in nearby Lexington, Massachusetts; she disliked the experience.", 
    "Did she stay in the USA?", 
    "After Nathaniel's death, the family moved to Germany and then to England.", 
    "Why did they move?", 
    "Sophia and Una died there in 1871 and 1877, respectively.",
    "..."],
   "author_num": [0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0, 1, 0]
}
```

The fields are:
* `pid (string)`: a unique identifier of the passage that corresponds to the passage ids in the public OR-QuAC dataset.
* `title (string)`:  Title of the source Wikipedia page for `passage`
* `passage (string)`: A passage from English Wikipedia
* `sentences (list of strings)`: A list of all the sentences that were segmented from `passage`.
* `utterances (list of strings)`: A synthetic dialog generated from `passage` by our Dialog Inpainter model. The list contains alternating utterances from each speaker (`[utterance_1, utterance_2, …, utterance_n]`). In this dataset, the first utterance is a "prompt" that was provided to the model, and every alternating utterance is a sentence from the passage.
* `author_num (list of ints)`: a list of integers indicating the author number in `text`. `[utterance_1_author, utterance_2_author, …, utterance_n_author]`. Author numbers are either 0 or 1.  

Note that the dialog in `utterances` only uses the first 6 sentences of the passage; the remaining sentences are provided in the `sentences` field and can be used to extend the dialog.

## Download the processed dataset via [TFDS](https://www.tensorflow.org/datasets/catalog/wiki_dialog).

First, install the [`tfds-nightly`](https://www.tensorflow.org/datasets/overview#installation) package and other dependencies.

```bash
pip install -q tfds-nightly tensorflow apache_beam
```

After installation, load the `WikiDialog-OQ` dataset using the following snippet:
```python
>>> import tensorflow_datasets as tfds
>>> dataset, info = tfds.load('wiki_dialog/OQ', with_info=True)
>>> info
```

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
