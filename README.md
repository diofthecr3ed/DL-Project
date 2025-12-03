# DL-Project

# Pedestrian Intention Detection in the Indian Context  
Deep Learning Project – AI3001 | IIT Delhi

### Team Members  
- Aadi Jain (U20230006)  
- Devansh Bartwal (U20230007)  
- Muskaan Sahni (U20230161)  
- Preesha Katial (U20230057)

---

## Abstract
Indian roads are highly unstructured, chaotic, and dense, leading to frequent failures of Western pedestrian intent prediction systems. We propose a lightweight frame-based model that predicts whether a pedestrian intends to cross the ego vehicle’s path, using both visual posture cues and contextual road semantics. Our MobileNetV2 + Spatial Attention model combined with a 12-dimensional context feature vector achieved improved recall for the safety-critical Crossing class while preserving real-time feasibility.

---

## 1. Introduction
Pedestrian fatalities in India exceed 30,000 annually, driven by unpredictable behavior, limited visibility, and confusing infrastructure. Autonomous driving stacks must infer a pedestrian’s intention quickly and reliably.  
This project aims to enable active safety systems such as emergency braking in Indian traffic conditions.

---

## 2. Problem Definition
Given:
- An RGB frame from an ego-vehicle camera  
- Bounding box around a detected pedestrian  

Predict the binary crossing intention:

\[
P(y = \text{crossing} \mid I, C)
\]

Where:  
- I = Pedestrian image crop  
- C = Context features extracted from annotations  

The model must remain robust in crowded and ambiguous traffic environments.

---

## 3. Dataset: IDD-PeD  
Indian Driving Dataset subset with annotated pedestrian behavior.

| Property | Value |
|---------|------|
| Total pedestrian annotations | 10,000 |
| Crossing | 2,596 |
| Not Crossing | 7,404 |
| Crossing frames | 2,391 |

Image Placeholder: Dataset Examples  
> Insert: Few real images from dataset showing Indian traffic scenes

Image Placeholder: Class Distribution Bar Graph  
> Insert: Training data imbalance visualization

IDD-PeD highlights domain shift challenges compared to Western datasets like JAAD or PIE.

---

## 4. Proposed Solution

Frame-based binary classification using:

1. MobileNetV2 backbone for posture features  
2. Spatial Attention to focus on head and legs  
3. 12-Dim context input encoding risk and environment cues  

### Model Architecture Diagram

Image Placeholder: Full Model Architecture  
> Insert: Block diagram showing MobileNetV2 → Attention → Dense → +Context → Binary output

---

## 5. Context Feature Design

| Category | Example Attributes |
|----------|------------------|
| Awareness | head orientation |
| Visibility | occlusion levels |
| Vulnerability | age group |
| Social dynamics | group or solo |
| Infrastructure | signals, crosswalk |
| Location | divider or roadside |
| Ego-Vehicle Interaction | direct attention to vehicle |

Image Placeholder: Feature Importance Chart  
> Insert: Bar plot showing relative weight of each context feature

---

## 6. Data Pipeline
- Pedestrian extraction using bounding boxes  
- Resize to 224x224  
- Basic augmentations (light rotation, flips)  
- Frame skipping to avoid redundancy  
- Focal Loss to address class imbalance  

\[
FL = -\alpha (1-p)^\gamma \log(p)
\]

---

## 7. Training Details

| Component | Value |
|----------|------|
| Backbone | MobileNetV2 |
| Optimizer | Adam |
| Learning Rate | 1e-4 (decay schedule) |
| Epochs | 30 |
| Batch Size | 32 |
| Hardware | NVIDIA RTX 3050 |

---

## 8. Evaluation Metrics
Accuracy is misleading for unbalanced classes.  
We evaluate using:
- Precision  
- Recall  
- F1-Score  
- ROC-AUC  
- Confusion Matrix  

---

## 9. Experimental Results

We evaluated the model on the test set with a classification threshold of 0.5. The results indicate a strong ability to distinguish between crossing and non-crossing pedestrians, though class imbalance remains a challenge for the minority class.

### 9.1 Classification Report

| Class | Precision | Recall | F1-Score |
| :--- | :---: | :---: | :---: |
| **Not Crossing** | 0.92 | 0.94 | 0.93 |
| **Crossing** (Critical) | 0.69 | 0.60 | 0.64 |
| | | | |
| **Accuracy** | | | **0.89** |
| **Macro Avg** | 0.80 | 0.77 | 0.79 |
| **Weighted Avg** | 0.88 | 0.89 | 0.88 |

**ROC-AUC Score:** $0.8435$

### 9.2 Confusion Matrix Analysis
The confusion matrix highlights the trade-off between safety and convenience in the Indian context.

| Metric | Count | Interpretation |
| :--- | :---: | :--- |
| **True Negatives** | 2929 | **Correctly Ignored:** Pedestrian stayed on the side; vehicle continued. |
| **False Positives** | 175 | **False Alarm:** Vehicle braked unnecessarily (Safety preferred). |
| **False Negatives** | 253 | **Missed Pedestrian:** Critical safety failure; pedestrian crossed but model missed it. |
| **True Positives** | 382 | **Correct Stop:** Model correctly identified crossing intent. |

### 9.3 Performance Interpretation
* **High Accuracy (89%):** Driven largely by the "Not Crossing" majority class.
* **Recall on Crossing (0.60):** The model correctly identified 60% of pedestrians intending to cross. The 253 "Missed Pedestrians" represent the most significant area for future improvement.
* **Precision on Crossing (0.69):** When the model predicts "Crossing," it is correct 69% of the time, resulting in a manageable number of "False Alarms" (175) relative to the dataset size.

---

## 10. Ablation Analysis

| Model Variation | F1 (Crossing) | Notes |
|----------------|--------------|------|
| ResNet50 + limited features | 0.23 | Overfits; slow |
| EfficientNet + limited features | 0.35 | High compute cost |
| MobileNetV3 + 8 features | 0.56 | Lightweight but noisy |
| MobileNetV2 + Attention + 15 features | 0.60 | Good recall |
| MobileNetV2 + Attention + 12 features (Final) | 0.64 | Best tradeoff |

---

## 11. Key Learnings

| Challenge | Resolution |
|----------|------------|
| High class imbalance | Focal Loss + Balanced sampling |
| Unpredictable motion | Context outperformed motion cues |
| Heavy models failed | Mobile-friendly model succeeded |
| Data leakage risk | Strict temporal split usage |

Intent prediction does not always require temporal data in unstructured traffic.  
Scene semantics often overpower motion cues.

---

## 12. Future Work
- Multi-frame temporal reasoning  
- Pose keypoints for fine-grained intention  
- Camera + LiDAR fusion  
- Real-world testing on ADAS hardware  
- Cross-dataset adaptation (JAAD → IDD generalization)

---

## References
1. Bokkasam, R. et al., 2025. Pedestrian Intention and Trajectory Prediction in Unstructured Traffic Using IDD-PeD.  
2. Rasouli, A. et al., 2019. PIE Dataset for Pedestrian Intention Estimation.  
3. Kotseruba, I. et al., 2021. Winter Conference on Applications of Computer Vision.

---

## Appendix

Image Placeholder: Additional training graphs  
> Insert: Loss curves, accuracy curves

Image Placeholder: Baseline Architecture Diagrams  
> Insert: ResNet, EfficientNet, MobileNet comparisons
