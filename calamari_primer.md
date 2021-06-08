---
title: Calamari-Primer
date: 08.06.2021
author: Alexander Winkler
---

# Basis-Funktionalitäten von *calamari* [^1]

*Calamari* ist in `python3` geschrieben und basiert in der Version `v2.1.2` auf `tensorflow v.2.4.0`. 
Bei *Training* und *Prediction* kann durch Verwendung von GPU eine erhebliche Beschleunigung erzielt werden.
Die Erkennung kann parallel durch verschiedene Modelle erfolgen, aus denen mithilfe von Voter-Algorithmen die wahrscheinlichsten Treffer ausgewählt werden.
Die von den Entwicklern angebotenen Standard-Modelle sind v.a. auf historischen Antiqua- und Fraktur-Drucken trainiert und liefern in diesem Kontext beachtliche Erkennungsqualitäten (CER ~ 1-2%).

## Recognition

### Line Snippets

```
calamari-predict \
    --checkpoint "model.ckpt" \
    --data.images "images_files_*.tif"
```

### PageXML

```
calamari-predict \
    --data PageXML \
    --data.text_index 1 \
    --data.images "noscemus_books/*/*.raw.png" \
    --data.xml_files noscemus_books/*/*.xml \
    --checkpoint "calamari_training_output/best.ckpt.json"
```

Folgende Optionen bieten sich für verschiedene Szenarien an:

* Zum Auffangen von Fehlern: `--data.skip_invalid True`
* Zur Reduktion der Rechenlast: `--pipeline.batch_size 4`
* Output von Erkennungswahrscheinlichkeiten auf Zeichenebene: `--extended_prediction_data True`
* `--output_dir <FOLDER>` ????

## Training

### Line Snippets

### PageXML

```
calamari-train \
    --train PageXML \
    --train.images "path/to/images/*/*.raw.png" \
    --trainer.gen SplitTrain \
    --trainer.gen.validation_split_ratio 0.2 \
    --warmstart.model "calamari_models/antiqua_historical/0.ckpt.json" \
    --codec.include {string.digits + string.ascii_letters} \
    --trainer.output_dir "calamari_training_output" \
    --scenario.tensorboard_logger_history_size 50 \
    --device.gpus 0 \
    --n_augmentations 2
```

```
calamari-cross-fold-train \
    --train PageXML \
    --train.images "noscemus_books/*/*.raw.png" \
    --temporary_dir calamari_cf_training_output \
    --keep_temporary_files True \
    --scenario.tensorboard_logger_history_size 50 \
    --device.gpus 0 \
    --codec.include {string.digits + string.ascii_letters} \
    --best_models_dir "calamari_models_noscemus" \
    --weights "calamari_models/antiqua_historical/0.ckpt.json" \
              "calamari_models/antiqua_historical/1.ckpt.json" \
              "calamari_models/antiqua_historical/2.ckpt.json" \
              "calamari_models/antiqua_historical/3.ckpt.json" \
              "calamari_models/antiqua_historical/4.ckpt.json"
#   --n_augmentations=5
```

# Evaluation

## Line snippets

```
calamari-predict-and-eval \
    --checkpoint "/path/to/models/*.ckpt" \
    --data.images "path/to/snippets/*.png" \
    --data.texts "path/to/gt/*.gt.txt" \
    --skip_empty_gt True
```

## PageXML

```
calamari-predict-and-eval \
    --checkpoint  "/path/to/models/*.ckpt.json \
    --data "PageXML" \
    --data.images "path/to/pageimages/*.bin.png \
    --skip_empty_gt True
```
# Vermischte Hinweise

## PageXML von Eynollah

[Eynollah](https://github.com/qurator-spk/eynollah) produziert PageXML-Dateien mit dem Namespace `pc="http://schema.primaresearch.org/PAGE/gts/pagecontent/2019-07-15"`, das *Calamari* derzeit nicht unterstützt.
Um *Eynollah*-PageXML-Dateien zu verarbeiten, muss 
, das *Calamari* derzeit nicht unterstützt.
Um *Eynollah*-PageXML-Dateien zu verarbeiten, muss 

[^1]: Zusammengestellt aus der [*Calamari*-Dokumentation](calamari-ocr.readthedocs.io), verschiedenen [github-Issues](https://github.com/Calamari-OCR/calamari/issues) und dem [Demo-Notebook](https://github.com/andbue/calamari_demo/blob/main/calamari_train.ipynb) von Andreas Büttner.
