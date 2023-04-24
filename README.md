# IR final project: Peptents 🧬

## TERMS DETECTION

### [Step 1](https://github.com/Dorin-D/IR-peptents/blob/main/terms_detection/step1_finetune_roberta/train_maccrobat.ipynb): Finetune `RoBERTa-large-PM-M3-Voc`

We modified the code from [here](https://github.com/huggingface/notebooks/blob/main/examples/token_classification.ipynb) to use Roberta-large with Pubmed vocabulary as our pretrained model. We then fine-tune this model on NER task on the [Maccrobat dataset](https://huggingface.co/datasets/ktgiahieu/maccrobat2018_2020) for 20 epochs, reaching 93% accuracy.

### [Step 2](https://github.com/Dorin-D/IR-peptents/blob/main/terms_detection/step2_prodigy_fix_label/prodigy_label.ipynb): Correct the predicted terms with Prodigy 🦄

We modify prodigy `ner.manual` to load the Huggingface model in order to help with annotations.

### [Step 3.1](https://github.com/Dorin-D/IR-peptents/blob/main/terms_detection/step3_1_train_spacy/train_spacy.ipynb): Train SpaCy with the corrected terms

### [Step 3.2](https://github.com/Dorin-D/IR-peptents/blob/main/terms_detection/step3_2_train_huggingface/train_gold.ipynb): Train Huggingface model with the corrected terms

We finetune `RoBERTa-large-PM-M3-Voc##` again with the newly annotated data, then upload the model to Huggingface Hub. You can try to use our model using this [notebook](https://github.com/Dorin-D/IR-peptents/blob/main/terms_detection/step3_2_train_huggingface/inference_huggingface.ipynb).

### TLDR

Our journey of getting to the final product:

- Our patents have no numbering -> we choose Huggingface alvaroalon2/biobert_chemical_ner as starting point
- But Huggingface `alvaroalon2/biobert_chemical_ner` only recognize "Chemical", which is not what we are looking for.
- We found `d4data/biomedical-ner-all`, which have pretty good terms (Diagnostics procedure, Medication, Lab value, etc) but bad model. We utilize its dataset **MACCROBAT2018** and **MACCROBAT2020**
- We fine tune `bert-for-patents` on **MACCROBAT2018** and **MACCROBAT2020**
- We modify prodigy `ner.manual` to load the Huggingface model in order to help with annotations. In the process, we found that the vocabulary of English is not sufficient for chemical.
- We then fine tune `RoBERTa-large-PM-M3-Voc` because it has **PubMed** vocabulary, then the result is much better and helps a lot with annotations
- We manually annotated ~ 200 samples. Then fine tune spacy and Huggingface models (not using MACCROBAT) on our gold dataset. Resulting in:
  0.7 accuracy in SpaCy
  0.88 accuracy in our final model [RoBERTa-large-PM-M3-Voc](ktgiahieu/RoBERTa-large-PM-M3-Voc-hf-finetuned-ner-combine-filtered)
