# Imitation Learning baselines for Robotic Manipulation in Virtual Environment
[![Hugging Face](https://img.shields.io/badge/%F0%9F%A4%97%20Model%20%26%20Data-FFD21E?style=flat&logoColor=white)](https://huggingface.co/mimirmimir) [![Thesis PDF](https://img.shields.io/badge/%F0%9F%93%9C%20Thesis-2ea44f?style=flat)](thesis.pdf)
[![Diffusion Policy](https://img.shields.io/badge/Diffusion%20Policy-525252?logo=googlecolab)](https://colab.research.google.com/drive/1Y7WWHbDcby_Tj6Lr2pvNwursgn2f9B1A) [![SmolVLA](https://img.shields.io/badge/SmolVLA-525252?logo=googlecolab)](https://colab.research.google.com/drive/1wf0hqzjLR6yL-vlTbdkqXctZP2GqUC3R) [![Pi0](https://img.shields.io/badge/Pi0-525252?logo=googlecolab)](https://colab.research.google.com/drive/1BVCzvt2PfhSahik43obpyKKV_AB7xxAZ) [![RDT-1B](https://img.shields.io/badge/RDT--1B-525252?logo=googlecolab)](https://colab.research.google.com/drive/1dT5BuTBwXS1d3Sf5lcwipcJDJLhjJFDz)

This repository provides standalone Jupyter Notebooks and utility scripts to set up, train, and evaluate different Imitation Learning and Vision-Language-Action (VLA) models for robotic manipulation on the [ManiSkill 3](https://github.com/haosulab/ManiSkill) simulation environment.
These scripts are designed to be run on Google Colab (or a similar Jupyter environment with GPU support) and serve as a practical working pipeline for future students and researchers working on robotic manipulation.

## 📁 Repository Structure

The project is organized into the main `scripts/` directory, which contains the core training pipelines and supplementary folders for data processing and extra experiments:

* **`scripts/`**:
  * `dp_84.ipynb` and `dp_224.ipynb`: Pipelines for Diffusion Policy (different resolutions).
  * `pi0.ipynb`: Pipeline for Physical Intelligence Zero (Pi0).
  * `rdt1b.ipynb`: Pipeline for Robotics Diffusion Transformer (RDT-1B).
  * `smolvla.ipynb`: Pipeline for SmolVLA.
  
  * **`utils/`** (Data preparation and processing scripts):
    * `k_dataset.ipynb`: Used to generate the original demonstrations, create the dataset, and convert it into the required training formats.
    * `k_embeddings.ipynb`: Dedicated script to pre-compute the language embeddings specifically required for the RDT-1B model.
  
  * **`eval exp/`** (Ablation studies and experiments):
    * Contains alternative evaluation loops used to introduce disturbances and to conduct extra experiments.

Inside each subfolder is present an additional readme file that details how to use each code.

## 🛠️ Prerequisites

Before running any notebook, ensure you have:
1.  **Google Colab**: A Google Colab PRO account to run the environments, A100 40GB GPU is a minimum requirement to train the models. 
2.  **Hugging Face Account**: A Write-access Token is required to download datasets and upload model weights.
3.  **Weights & Biases (W&B)**: Necessary for monitoring and tracking metrics during training.

## 📚 Resources & Acknowledgements

The training pipelines for **Diffusion Policy**, **SmolVLA**, and **Pi0** in this repository are built heavily upon the [LeRobot](https://github.com/huggingface/lerobot) framework by Hugging Face. 

For deeper insights into the specific model architectures and their original implementations, please refer to the official repositories:

* **Diffusion Policy** by Real Stanford: [real-stanford/diffusion_policy](https://github.com/real-stanford/diffusion_policy)
* **Pi0** by Physical Intelligence (OpenPI): [Physical-Intelligence/openpi](https://github.com/Physical-Intelligence/openpi)
* **RDT-1B** by THU-ML (Robotics Diffusion Transformer): [thu-ml/RoboticsDiffusionTransformer](https://github.com/thu-ml/RoboticsDiffusionTransformer)

---

## 🚀 How to Run the Pipelines (Step-by-Step)

All the pipelines are extremely similar to each other and the script cells are already in the corect execution order so that the whole notebook can automatically run. RDT-1B contains some additional passages compared to the other models.

### Phase 1: Data Preparation (Initial Setup)
If you are starting from scratch and need to generate or process the datasets yourself, use the scripts in the `utils/` folder:
1. Run **`k_dataset.ipynb`** to gather the demonstrations, compile them, and convert them into the correct formats.
2. If you intend to train the **RDT-1B** model, you must first run **`k_embeddings.ipynb`** to pre-compute the textual embeddings. This will save significant compute time during the main training phase. The results can be automatically copied to your Google Drive space.

Additional information on how to use these utils scripts are provided in the readme of the respective folder.

### Phase 2: Training and Standard Evaluation
Open your chosen model's notebook (e.g., `smolvla.ipynb` or `rdt1b.ipynb`) in Google Colab:
1. **Hardware Check**: Go to `Runtime` > `Change runtime type` and ensure the Hardware accelerator is set to **GPU** (Select A100 or better).
2. **Authentication**: Execute the initial setup cells. You will be prompted to enter your W&B API key and Hugging Face token. *(For RDT-1B, you must also authorize access to Google Drive).*
3. **Execution**: Run the cells sequentially. The script will autonomously install dependencies, register customized simulation environments, download the processed dataset, execute the training loop, and finally perform an evaluation, saving demonstration videos in MP4 format.

Additional information on how to use these utils scripts are provided in the readme of the respective folder.

### Phase 3: Ablation Studies and Extra Experiments
To test the trained policies in different scenarios or reproduce the ablation studies presented in the thesis:
* Navigate to the **`eval exp/`** folder.
* Use the scripts provided to launch alternative evaluation loops, in which you can introduce gaussian noise and random occlusions to the observations.
* Simply load the weights (checkpoints) of the models you previously trained and saved to observe their behavior under the new experimental conditions.

## ⚙️ Customizing the Execution

The entire pipeline is designed to be highly modular, allowing for intuitive modifications across all its subcomponents. To fully understand what parameters can be adjusted and how to implement these changes, please refer to the dedicated `README.md` files located within the respective subdirectories, as well as the detailed inline comments accompanying the specific functions within each Jupyter Notebook.

Broadly speaking, the primary elements that can be practically customized include:

* **Tasks and Virtual Environments:** You can seamlessly switch between different simulation tasks (e.g., from `PickCube-v1` to `StackCube-v1`). Furthermore, the physical and visual properties of these environments can be overridden. This includes adjusting camera resolution, frames per second (FPS), natural language prompts, controller types, and the robotic agent itself.
* **Training Parameters:** Key hyperparameters—such as `training_steps`, batch sizes, and learning rates—can be easily modified to suit different computational constraints or experimental setups (e.g., lowering the training steps for a quick compilation test).
* **Evaluation Settings:** The evaluation loops can be tailored by adjusting `num_episodes` (the number of generated test videos), specifying random seeds, or altering the natural language instructions provided to the models to test their semantic generalization capabilities.

## 📥 Accessing the Results
* **Training Metrics**: Tracked live via the W&B link generated in the Colab output.
* **Model Checkpoints**: Saved in the `outputs/` folder in Colab and automatically pushed to your Hugging Face Hub.
* **Videos**: Standard evaluation `.mp4` files are saved in the Colab file explorer (usually under `eval_videos/` or `episodes/`).
