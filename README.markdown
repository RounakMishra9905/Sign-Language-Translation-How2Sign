# PoseStich-SLT: Linguistically Inspired Pose-Stitching for End-to-End Sign Language Translation

This repository contains the code, datasets, and resources for the paper on Linguistically Inspired Pose-Stitching for End-to-End Sign Language
Translation focusing on the creation and evaluation of **BPCC-ISL** and **BPCC-ASL** datasets, along with linguistically generated datasets **BLIMP-ISL** and **BLIMP-ASL**. The datasets are designed to enhance SLT systems for Indian Sign Language (ISL) and American Sign Language (ASL) by leveraging pose stitching, vocabulary matching, and linguistic templates.

## Table of Contents
- [Overview](#overview)
- [Datasets](#datasets)
- [Data Processing](#data-processing)
- [Model Architecture](#model-architecture)
- [Training Details](#training-details)
- [Results](#results)
- [Installation](#installation)
- [Usage](#usage)
- [Directory Structure](#directory-structure)
- [Contact](#contact)
- [Cite Paper](#cite-paper)

## Overview
This project focuses on creating large-scale SLT datasets by combining existing datasets like **iSign**, **How2Sign**, **CISLR**, **WLASL**, and **BLIMP**. The datasets are processed to match vocabulary, sentence length distributions, and frame rates, followed by pose stitching to generate coherent sign pose-text datasets. Two datasets, **BPCC-ISL** and **BPCC-ASL**, are created, along with linguistically generated **BLIMP-ISL** and **BLIMP-ASL** datasets covering 12 linguistic phenomena and 67 paradigms.
## Overall Architecture
![alt text](./images/{7C4A7553-9D4E-4A8F-AC69-25EDA6385220}.png)
**Figure 1: Overview of the dataset creation pipeline**  
![alt text](./images/{7C4A7553-9D4E-4A8F-AC69-25EDA6385220}.png)

## Datasets
The following datasets are used or generated in this project:

| Dataset Name       | Number of Sentences |
|--------------------|---------------------|
| iSign Train        | 99,923             |
| iSign Test         | 6,069              |
| iSign Val          | 5,653              |
| How2Sign Train     | 31,092             |
| How2Sign Test      | 2,349              |
| How2Sign Val       | 1,739              |
| BPCC-ISL           | 1,640,469          |
| BPCC-ASL           | 1,173,536          |
| BLIMP-ISL          | 22,219,407         |
| BLIMP-ASL          | 2,880,008          |

### Vocabulary Distribution
The datasets share common vocabularies to ensure compatibility. Below are the unique word counts and their overlaps:

| Dataset Combination                     | Unique Words |
|-----------------------------------------|--------------|
| iSign Train                             | 15,093       |
| CISLR                                   | 4,764        |
| BPCC-ISL                                | 6,700        |
| iSign Train ∩ CISLR                     | 2,450        |
| iSign Train ∩ BPCC-ISL                  | 4,976        |
| CISLR ∩ BPCC-ISL                        | 3,152        |
| WLASL                                   | 2,000        |
| How2Sign Train                          | 7,430        |
| BPCC-ASL                                | 4,613        |
| WLASL ∩ How2Sign Train                  | 1,501        |
| WLASL ∩ BPCC-ASL                        | 1,943        |
| How2Sign Train ∩ BPCC-ASL               | 2,882        |

**Figure 2: Vocabulary distribution between datasets**  
![alt text](./images/{63F12F97-0616-4F19-B8CA-A563AAF270B7}.png)

## Data Processing
### Sentence Length Matching
The sentence length distributions of **BPCC-ISL** and **BPCC-ASL** are aligned with **iSign** and **How2Sign**, respectively, to ensure compatibility.

**Figure 3: Sentence length distribution (before and after matching)**  
![alt text](./images/{7FE879A8-3A81-49FD-984C-236556627A4B}.png)

### Frame Rate Matching
To align frame rates, the mean frame count of the source datasets (e.g., iSign, How2Sign) is matched with the generated datasets by sampling every \(x\)-th frame, where \(x\) is the sampling rate (\(x = \text{mean}_{\text{generated}} / \text{mean}_{\text{source}}\)).

**Figure 4: Frame distribution before and after matching**  
![alt text](./images/{10C65582-B812-48B8-86D9-4DD0B878EFF8}.png)

### Pose Processing
Pose data is extracted using the **MediaPipe** library, focusing on 76 key points (21 left hand, 21 right hand, 11 body pose, 23 facial keypoints). Keypoints with confidence below 0.8 are filled using the nearest frame with higher confidence.

### Pose Stitching
Two strategies are used:
1. **Same Word Order (SWO)**: Poses are stitched in the same order as the sentence.
2. **Random Word Order (RWO)**: Poses are stitched in random order to improve generalization.

## Model Architecture
The SLT system uses an encoder-decoder architecture:
- **Encoder**: BERT model with 4 layers, 512 hidden size, 8 attention heads.
- **Decoder**: GPT-2 model with 4 layers, 512 hidden size, 8 attention heads.
- **Tokenizer**: BPE tokenizer trained on combined datasets with a vocabulary size of 15,000.
- **Optimizer**: AdamW with a learning rate of 3e-4 (iSign) or 1e-4 (How2Sign).
- **Input**: 152-dimensional vectors (76 keypoints × (x, y) coordinates).

## Training Details
- **Pretraining Strategy**: Linear annealing from 0% to 85% of original dataset (iSign/How2Sign) over 60,000 steps.
- **Hyperparameters**:

| Hyperparameter             | iSign   | How2Sign |
|----------------------------|---------|----------|
| Learning Rate              | 3e-4    | 1e-4     |
| Encoder Layers             | 4       | 2        |
| Decoder Layers             | 4       | 2        |
| Hidden Size                | 512     | 512      |
| Attention Heads            | 8       | 4        |
| Dropout                    | 0.1     | 0.3      |
| Batch Size                 | 16      | 16       |
| Sampling Steps             | 4       | 3        |

## Results
### Performance Gains
Pretraining with pose-stitched datasets significantly improves performance:
BLEU Score:
![alt text](./images/{EDA1CB27-65F9-4BC0-85CD-60C304BBD363}.png)
ROUGE Score:
![alt text](./images/{C5EDA331-8979-4AE7-8E73-AE0A68E9F4C4}.png)


### Qualitative Results
The model outperforms baselines like GloFE, capturing main ideas more accurately (see Table 10 in the paper).



## Installation
1. Clone the repository:
   ```bash
   git clone https://github.com/Exploration-Lab/Linguistic-Based-ISL.git
   ```
2. Install dependencies:
   ```bash
   install dependency environment.yaml
   ```
3. Install MediaPipe for pose extraction:
   ```bash
   pip install mediapipe
   ```

## Directory Structure
```
slt-datasets/
├── Preprocessing_Data_generation/                     # Preprocessing , Data generation and Processed datasets (BPCC-ISL, BPCC-ASL, BLimp-ASL, Blimip-ASL etc.)
├── images/                   # Figures and visualizations
├── IncrementalPT_linear_sch.py                 # Preprocessing and training scripts 
├── environment.yaml          # Dependencies
└── README.md                 # This file
└── helpers                   # Contains helper files for training
```

## Contact
For questions or issues, please open an issue on GitHub or contact [vaibhavmulti@gmail.com](mailto:vaibhavmulti@gmail.com), [sanjeet2601@gmail.com](mailto:sanjeet2601@gmail.com).

## Cite Paper
