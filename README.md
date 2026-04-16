# VOC Split EDA and Group Localisation Pipeline

This repository is the shared workspace for a group object localisation project on VOC-style data.

The project is designed to prevent data leakage by forcing all members to use the exact same train/val/test image IDs while allowing each member to train a different model.

## Project goal

Train four different object localisation models on identical splits, then ensemble their predictions:

1. DETR
2. YOLO
3. Faster R-CNN
4. Neural Cellular Automata (NCA)

The final output is one ensemble prediction set with a common evaluation pipeline.

## Repository structure

Top-level notebooks:

1. [VOC_Main.ipynb](VOC_Main.ipynb): shared starter notebook for split logic, parsing, EDA, and baseline dataset utilities.
2. [VOC_Member_DETR_Template.ipynb](VOC_Member_DETR_Template.ipynb): DETR template pipeline.
3. [VOC_Member_YOLO_Template.ipynb](VOC_Member_YOLO_Template.ipynb): YOLO template pipeline.
4. [VOC_Member_FasterRCNN_Template.ipynb](VOC_Member_FasterRCNN_Template.ipynb): Faster R-CNN template pipeline.
5. [VOC_Member_NCA_Template.ipynb](VOC_Member_NCA_Template.ipynb): NCA template pipeline.
6. [VOC_Ensemble_Evaluation.ipynb](VOC_Ensemble_Evaluation.ipynb): combines model predictions and evaluates ensemble performance.

Data folders:

1. [trainval](trainval): VOC2012 train and val source images/annotations.
2. [test](test): VOC2012 test source images/annotations.

Artifacts folder:

1. [artifacts/class_to_id.csv](artifacts/class_to_id.csv): class mapping table.
2. [artifacts/images_index.csv](artifacts/images_index.csv): image-level index.
3. [artifacts/objects_index.csv](artifacts/objects_index.csv): object-level index.
4. [artifacts/train_objects_no_difficult.csv](artifacts/train_objects_no_difficult.csv): training objects with difficult instances removed.
5. [artifacts/group_split](artifacts/group_split): locked split IDs and split manifest.
6. [artifacts/predictions](artifacts/predictions): per-model and ensemble CSV predictions.

## Leakage prevention contract

All team members must follow these rules:

1. Do not modify split assignment logic.
2. Always use IDs from [artifacts/group_split/train_ids.txt](artifacts/group_split/train_ids.txt), [artifacts/group_split/val_ids.txt](artifacts/group_split/val_ids.txt), and [artifacts/group_split/test_ids.txt](artifacts/group_split/test_ids.txt).
3. Validate split integrity with zero overlap between train, val, and test.
4. Keep preprocessing and split boundaries consistent across all model notebooks.

Split metadata is recorded in [artifacts/group_split/split_manifest.json](artifacts/group_split/split_manifest.json), including expected counts and file hashes.

## Standard prediction format

Every model notebook must export predictions as CSV with the same columns:

1. image_id
2. split
3. source_model
4. class_id
5. cls
6. xmin
7. ymin
8. xmax
9. ymax
10. score

All model prediction files are saved under [artifacts/predictions](artifacts/predictions).

Recommended filenames:

1. detr_val_predictions.csv
2. detr_test_predictions.csv
3. yolo_val_predictions.csv
4. yolo_test_predictions.csv
5. fasterrcnn_val_predictions.csv
6. fasterrcnn_test_predictions.csv
7. nca_val_predictions.csv
8. nca_test_predictions.csv

The ensemble notebook writes:

1. ensemble_test_predictions.csv

## End-to-end workflow

1. Run [VOC_Main.ipynb](VOC_Main.ipynb) to verify paths, parse annotations, inspect data quality, and confirm split integrity.
2. Each member runs their model template notebook and trains on shared train IDs.
3. Each member evaluates on shared val IDs and exports val/test CSV predictions.
4. Run [VOC_Ensemble_Evaluation.ipynb](VOC_Ensemble_Evaluation.ipynb).

Inside the ensemble notebook, the pipeline will:

1. Load all model CSV prediction files.
2. Combine predictions using class-wise NMS and weighted box fusion-like aggregation.
3. Evaluate ensemble mAP@0.5 on validation.
4. Generate final ensemble test predictions.

## Reproducibility checklist

Before merging final results:

1. Confirm all members used the same split files from [artifacts/group_split](artifacts/group_split).
2. Confirm required prediction CSV files exist in [artifacts/predictions](artifacts/predictions).
3. Confirm each CSV uses the standard schema and valid class IDs.
4. Confirm no train/val/test overlap appears in any notebook outputs.
5. Record package versions and random seeds used in each model notebook.

## Notes

1. Template notebooks are intentionally scaffolded: each member should replace model-specific training/inference stubs while preserving the shared split and output contract.
2. The mAP implementation in templates is lightweight and intended for consistent internal comparison; you can optionally add COCO-style metrics as a secondary evaluation.
