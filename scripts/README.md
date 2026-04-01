# 🤖 Model Training and Evaluation Pipelines

This directory contains the primary Jupyter Notebooks used to train and evaluate the four distinct Imitation Learning and Vision-Language-Action (VLA) models: **Diffusion Policy**, **Pi0**, **RDT-1B**, and **SmolVLA**. 

Despite their architectural differences, all pipelines adhere to a standardized, unified workflow to ensure methodological consistency and reproducibility across experiments. This document outlines the sequential phases present in each notebook.

---

## 1. Setup and Initialization
The preliminary phase is dedicated to configuring the computational environment.
* **Installations and Authentication:** The initial cells handle the installation of essential libraries (e.g., LeRobot, ManiSkill) and require authentication for Hugging Face (HF) and Weights & Biases (W&B) to enable model checkpointing and metric tracking.
* **Important Notice:** Following the installation of core dependencies, it is strictly required to perform a **session restart** whenever explicitly indicated within the notebook. Failure to do so may result in dependency conflicts or runtime errors.

## 2. Environment Configuration (ENV)
To ensure empirical validity, the simulation environments must perfectly match the conditions under which the training datasets were collected.
* **Environment Overrides:** We apply custom configurations to the baseline ManiSkill environments. This step systematically modifies environment parameters—such as camera observation resolutions and object dimensional properties—to maintain strict coherence with the dataset structure.
* **Visual Inspection:** A dedicated cell is provided to render and visualize both the base camera observations (agent-view) and the render views (human-view), allowing for a qualitative verification of the applied overrides prior to training.

## 3. Dataset Acquisition
Data provisioning is handled autonomously via the Hugging Face Hub integration.
* **Download and Verification:** The specified dataset is downloaded directly into the active runtime. 
* **Compatibility Check:** A structural validation is performed to guarantee that the downloaded dataset format is fully compatible with the currently installed version of the LeRobot framework.

## 4. Model Training
This section encompasses the core optimization loop for the imitation learning policies.
* **Hyperparameter Configuration:** Users can define and adjust training parameters (e.g., training steps, batch size, learning rate).
* **Execution and Deployment:** Upon completion of the training loop, the resulting model checkpoints and optimized weights are automatically pushed to the designated Hugging Face repository for persistent storage and version control.

## 5. Offline Action Accuracy Test
Before deploying the policy in the interactive simulation, a preliminary static evaluation is conducted.
* **Ground Truth Comparison:** The selected model checkpoint is evaluated against an arbitrary subset of samples extracted from the offline dataset.
* **Metric Computation:** The system computes and returns accuracy metrics detailing the quantitative variance between the policy's predicted actions and the ground-truth expert actions.

## 6. Online Evaluation (Inference in ManiSkill)
The final phase consists of an active, closed-loop evaluation within the customized ManiSkill virtual environments to assess the policy's real-time manipulation capabilities.
* **Evaluation Configuration:** Users can configure test parameters, including environment seeds, specific model checkpoints to load, maximum episode steps, and the total number of evaluation trials. 
* **Language Prompting:** There is an explicit option to alter the natural language instructions provided to the VLA models, allowing for zero-shot semantic generalization tests.
* **Initialization and Bridging:** The notebook initializes the vectorized environments, loads the trained policy, and establishes the essential helper functions required to translate generic LeRobot actions into ManiSkill-compatible control signals.
* **Inference Loop:** The policy acts autonomously within the environment over the designated number of steps.
* **Results and Visualization:** Upon conclusion of the inference loop, comprehensive success metrics are outputted directly in the notebook, accompanied by generated MP4 video renderings of the evaluation trials for qualitative analysis.

---

## Model-Specific Extensions: RDT-1B (`rdt1b.ipynb`)

While adhering to the overarching workflow described above, the pipeline for the **Robotics Diffusion Transformer (RDT-1B)** incorporates several model-specific architectural and data-processing deviations. When executing the `rdt1b.ipynb` notebook, users should note the following specialized phases:

### Dataset Processing Extensions
The data provisioning phase for RDT-1B introduces supplementary procedures necessitated by the model's multimodal architecture:
1. **Embeddings Retrieval:** The pipeline requires the integration of pre-computed natural language embeddings (generated prior via the `k_embeddings.ipynb` utility script).
2. **HDF5 Conversion:** The standard LeRobot dataset format is structurally incompatible with the native RDT dataloader. Consequently, the notebook executes an automated conversion of the dataset into the **HDF5 format**, during which the pre-computed language embeddings are injected directly into the data hierarchy.

### Encoders and Weights Initialization
This section manages the independent acquisition of the model's foundational encoders:
* **Visual Encoder:** The "SigLIP" visual encoder weights are downloaded and initialized separately.
* **Textual Encoder Omission:** Crucially, the pipeline intentionally bypasses the downloading and instantiation of the Google T5-XXL textual encoder. This architectural truncation is a deliberate optimization necessary to prevent Out-Of-Memory (OOM) exceptions, as loading the full T5-XXL model concurrently with RDT-1B exceeds the 40GB VRAM limitations of a standard NVIDIA A100 GPU.

### Fine-Tuning Setup
This segment executes specific configurational prerequisites and environmental setups mandated by the native [RDT-1B framework](https://github.com/thu-ml/RoboticsDiffusionTransformer). These steps are deeply integrated with the original repository's logic. For a comprehensive understanding of these underlying mechanisms, or for instructions on how to modify these initialization parameters, users are highly encouraged to consult the official RDT documentation.

*(Note: Beyond these specific initialization and data formatting steps, the remainder of the RDT-1B training, offline testing, and online evaluation workflows align identically with the standardized pipeline detailed in sections 4 through 6).*
