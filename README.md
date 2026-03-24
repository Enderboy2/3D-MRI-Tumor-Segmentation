# 3D Brain Tumor Segmentation

![Python](https://img.shields.io/badge/Python-3.10+-blue?logo=python&logoColor=white)
![PyTorch](https://img.shields.io/badge/PyTorch-2.0+-ee4c2c?logo=pytorch&logoColor=white)
![MONAI](https://img.shields.io/badge/MONAI-1.5+-green)
![License](https://img.shields.io/badge/License-MIT-yellow)

<!-- Replace the path below with your actual screenshot after running the notebook -->
![Prediction Example](assets/prediction_example.png)
*Left to right: Raw MRI, Ground truth mask, Model prediction, and a visual overlay.*

---

## What this is

I built a 3D U-Net to find brain tumors in MRI scans. 

Most simple projects slice the brain into 2D images and process them one by one. But tumors are 3D objects, so I built this to process the entire 3D volume at once. It takes a full brain scan combined from four different MRI types and maps out exactly where the tumor is. It even breaks the tumor down into three specific parts: the necrotic core, the edema, and the enhancing tumor.

## The Data

I used the **[BraTS 2021](https://www.synapse.org/#!Synapse:syn25829067/wiki/610865)** dataset. 

It contains multi-parametric MRI scans from 1,251 patients. Each patient has 4 different scans (FLAIR, T1, T1ce, T2). 

To keep the training time reasonable on a free Kaggle GPU, I trained this model on 100 patients. You can find the Kaggle version of the dataset here: [`dschettler8845/brats-2021-task1`](https://www.kaggle.com/datasets/dschettler8845/brats-2021-task1).

## How it works

I wrote this using PyTorch and MONAI. Here’s what happens under the hood:

**The bottleneck problem:** 
3D medical images are massive. If you read from the hard drive during training, the GPU just sits there idle waiting for files to load. So here's how I fixed it: the notebook preloads all the processed patient data straight into RAM before training starts. This way, the GPU stays at 100% capacity and trains much faster.

**Data Augmentation:**
Since I'm only using 100 patients, the model could easily just memorize them. I added random flips, rotations, and brightness shifts during training. This forces the model to actually learn what a tumor looks like.

**The Model:**
It's a 5-level 3D U-Net. I gave it a combined Dice and Cross-Entropy loss function. Basically, this tells the model to ignore all the empty background space and focus strictly on getting the tumor boundaries right.

## Checking the results

I didn't want to just print out a single accuracy number and call it a day. The notebook includes a few different ways to actually see how well the model works on unseen patients:

- **Slice Overlays:** A side-by-side view of the MRI, the ground truth bounds, the prediction bounds, and a final image showing both boundaries overlaid on the brain.
- **3D Rendering:** It uses marching cubes to create an actual 3D mesh of the tumor that you can look at, instead of just flat 2D slices.
- **MIP Views:** Maximum Intensity Projections. This collapses the 3D volume down into 2D so you can see the entire span of the tumor from the top, front, and side.
- **Dice Scores:** It prints out a table of the exact Dice overlap score for every single patient and tumor class.

## Try it yourself

If you want to run this yourself, Kaggle is the easiest way.

1. Make a new Kaggle notebook.
2. Search for the `dschettler8845/brats-2021-task1` dataset and attach it to the notebook.
3. Upload `brain_tumor_segmentation.ipynb`.
4. Go to Settings and turn on the **GPU** and **Internet**.
5. Run all the cells. 

Training 100 patients for 200 epochs will take a few hours.

## Project Files

```
3D_segmentation/
├── brain_tumor_segmentation.ipynb   # The main code
├── assets/
│   └── prediction_example.png       # Toss your result screenshot here
├── requirements.txt                 
├── LICENSE                          
├── .gitignore
└── README.md
```
