# Liver-Histopathology-Fibrosis-Ultrasound-Image-Classification
This project achieves state-of-the-art (SOTA) performance for liver fibrosis classification using heterogeneous ultrasound images, supported by Grad-CAM visualizations for interpretability.

---
### Dataset
The **Liver Histopathology (Fibrosis) Ultrasound Images** dataset from Kaggle is used in this study.

- **Dataset:** https://www.kaggle.com/datasets/vibhingupta028/liver-histopathology-fibrosis-ultrasound-images

This dataset contains **five fibrosis stages (F0–F4)**:

| Class | Stage Name | # Images | Description |
|------|------------|---------:|------------|
| F0 | No Fibrosis | 2114 | Healthy liver tissue with no signs of fibrosis. |
| F1 | Portal Fibrosis | 861 | Fibrosis around the portal areas of the liver. |
| F2 | Periportal Fibrosis | 793 | Fibrosis around the edges of the portal regions. |
| F3 | Septal Fibrosis | 857 | Fibrosis forming septa (bands) across the liver tissue. |
| F4 | Cirrhosis | 1698 | Advanced fibrosis leading to cirrhosis and loss of normal liver structure. |

---

### Related Work
Two recent papers have directly used this dataset:
- **Paper1:** https://www.nature.com/articles/s41598-025-33544-z
- **Paper2:** https://www.mdpi.com/2075-4418/15/24/3226

---

### Key Insight
From this project, I found that a high-end model is not always required to achieve SOTA performance. Instead, carefully designed data augmentation and proper hyperparameter tuning can significantly improve results, even using only ResNet-18.

---

### Data Preprocessing

### Train / Val / Test Split
The dataset was sorted by filename and split into **80% train / 10% val / 10% test** using **stratified sampling** to preserve class distribution.  

We verified that there is **no filename overlap** between the training, validation, and test splits to avoid direct data leakage.

However, the ideal approach for medical imaging is a **patient-level split** (to ensure images from the same patient do not appear in multiple splits).  
Unfortunately, this dataset does not provide **patient identifiers or metadata**, so patient-level leakage cannot be fully ruled out.

### Normalization
Dataset-specific **mean** and **std** were computed from the **training set only** (Resize 256 → CenterCrop 224 → ToTensor) and used for normalization.

### Data Augmentation
**Training transforms:**
- Resize (256×256)
- RandomResizedCrop (224)
- HorizontalFlip (p=0.5)
- RandomAffine (rotation/translation/scale/shear)
- GaussianBlur (p=0.1)
- Normalize (mean, std)

**Validation/Test transforms:**
- Resize (224×224)
- ToTensor
- Normalize (mean, std)

---

### Model
Only **ResNet-18** was used in this study, and the network was trained **from scratch** (no pretrained weights).

---

### Training Setup


**Loss Function**
- CrossEntropyLoss

**Optimizer**
- AdamW  
- Learning rate: `1e-4`  
- Weight decay: `1e-4`

**Learning Rate Scheduler**
- CosineAnnealingLR  
- `T_max = num_epochs`, `eta_min = 1e-6`

**Class Imbalance Handling**
To handle class imbalance, a **WeightedRandomSampler** was used in the **training DataLoader only**.  
Sample weights were computed using **inverse class frequency**, ensuring balanced sampling across fibrosis stages.

**Regularization**
- **MixUp** was applied during training:
  - `alpha = 0.1`
  - `mixup_prob = 0.15`

---

### Model Selection
- The best model was saved based on the **lowest validation loss**.

---

###  Results (Test Set)

**Overall Performance**
| Metric | Score |
|--------|------:|
| Accuracy | **99.84%** |
| Macro Precision | **99.77%** |
| Macro Recall | **99.75%** |
| Macro F1-score | **99.76%** |
| Macro Specificity | **99.96%** |
| Macro AUC | **100%** |
| Weighted Precision | **99.84%** |
| Weighted Recall | **99.84%** |
| Weighted F1-score | **99.84%** |
| Weighted Specificity | **99.98%** |
| Weighted AUC | **100%** |

---

**Per-Class Performance**
| Class | Precision | Recall | F1-score | Support |
|------|----------:|-------:|---------:|--------:|
| F0 | 1.0000 | 1.0000 | 1.0000 | 212 |
| F1 | 0.9885 | 1.0000 | 0.9942 | 86 |
| F2 | 1.0000 | 0.9873 | 0.9936 | 79 |
| F3 | 1.0000 | 1.0000 | 1.0000 | 86 |
| F4 | 1.0000 | 1.0000 | 1.0000 | 170 |

---

### Confusion Matrix (Test Set)

<img width="2177" height="2280" alt="cm" src="https://github.com/user-attachments/assets/2ad5941b-8512-4070-8e0b-71e340f394b3" />

---

### Grad-CAM visualization

<img width="1580" height="2639" alt="grad_cam" src="https://github.com/user-attachments/assets/4ec13e99-d171-4191-b8ad-66f5350f6722" />


