# Drivable Area and Lane segmentation

This project is about detecting the Drivable area and detecting lanes on the road. This project is mainly about finetuning the model with the dataset we generated using stable diffusion

## Dataset

- This dataset contains images for Drivable Area segmentation and Lane detection. All the images are generated using Stable diffusion in Google Colaboratory. This dataset is around 90 Megabytes. The project we are working on has two label outputs for each sample. And these outputs are overlayed on the original image.
- We've used stable diffusion to generate images for finetuning the model. click on the below badge to see how we worked with stable diffusion. The model we used is CompVis's stable-diffusion-v1-4 which can run on T4 GPU provided without any cost by google.

The Custom Dataset Link is "https://drive.usercontent.google.com/download?id=1BI8xGxoWWjDZYkTLBt4qOki1vfcG29lP&export=download&authuser=0" 

## Annotation

- The images are annotated using labelme tool. Which is an opensource tool used to annotate image data. Each image is annotated twice one is for drivable area segmentation and another is for lane detection.

### Label Studio Annotation Tool


![label1](https://github.com/pijush2022/Lane_detection/assets/104580397/3cc4d73e-f86c-4557-85ca-e99ff64b9ea3)

### Annotation:
• Our project leverages Label Studio, an image annotation tool, to meticulously label road lines and drivable areas within our image dataset.
• Link: https://labelstud.io/guide

#### Original Image and Annotation for Drivable area segmentation


![label2](https://github.com/pijush2022/Lane_detection/assets/104580397/83967bd9-4f76-4310-909f-9e6afb961698)

#### Annotation for Lane detecton


![label3](https://github.com/pijush2022/Lane_detection/assets/104580397/11bf1be4-102f-4273-abf7-99354da0bd78)

### Example of our custome data from the above code

![label4](https://github.com/pijush2022/Lane_detection/assets/104580397/8b60e5a9-efa8-4a9b-a983-8a5493c63eb3)


## Partitioning

The dataset is structured into three distinct partitions: Train, Test, and Validation. The Train split comprises 80% of the dataset, containing both the input images and their corresponding labels. Meanwhile, the Test and Validation splits each contain 10% of the data, with a similar structure, consisting of image data and label information.
Within each of these splits, there are three folders:

- Images: This folder contains the original images, serving as the raw input data for the task at hand.

- Segments: Here, you can access the labels specifically designed for Drivable Area Segmentation, crucial for understanding road structure and drivable areas.

- Lane: This folder contains labels dedicated to Lane Detection, assisting in identifying and marking lanes on the road.

## Accessing the Dataset

The dataset that we annotated is available in this repository in the datasets folder as datasets.zip. And also the dataset is pushed to huggingface datasets and you can access the dataset like shown below



```python
from datasets import load_dataset
Download the dataset from "https://drive.usercontent.google.com/download?id=1BI8xGxoWWjDZYkTLBt4qOki1vfcG29lP&export=download&authuser=0"
dataset = load_dataset("drive/dataset")
```


## Training
https://github.com/pijush2022/Lane_detection/blob/master/Submission/Update%202/Update_2.ipynb

### Transformation while Training

#### Random Perspective Transformation:
This transformation simulates changes in the camera's perspective, including rotation, scaling, shearing, and translation. It is applied with random parameters:

  - degrees: Random rotation in the range of -10 to 10 degrees.
  - translate: Random translation in the range of -0.1 to 0.1 times the image dimensions.
  - scale: Random scaling in the range of 0.9 to 1.1 times the original size.
  - shear: Random shearing in the range of -10 to 10 degrees.
  - perspective: A slight random perspective distortion.
  
#### HSV Color Augmentation:
  
  - This changes the hue, saturation, and value of the image.
  - Random gains for hue, saturation, and value are applied.
  - The hue is modified by rotating the color wheel.
  - The saturation and value are adjusted by multiplying with random factors.
  - This helps to make the model invariant to changes in lighting and color variations.
  
#### Image Resizing:

  - If the Images are not in the specified size, the images are resized to a fixed size (640x360) using cv2.resize.
  
#### Label Preprocessing:
  
  - The labels (segmentation masks) are thresholded to create binary masks. This means that pixel values are set to 0 or 255 based on a threshold (usually 1 in this case).
  - The binary masks are also inverted to create a binary mask for the background.
  - These binary masks are converted to PyTorch tensors for use in training the semantic segmentation model.

### Loss

- Tversky loss and Focal loss are used here. Total loss = Focal Loss + Tversky Loss

### Optimization

- In this setup, an Adam optimizer with a dynamically decreasing learning rate is employed. This adaptive learning rate is regulated using a Polynomial Learning Rate Scheduler, which gradually reduces the learning rate as the training progresses.

## Deployment

- The model is deployed on Github Action with CI/CD. click [here](https://github.com/pijush2022/Lane_detection.git) to go there.
- You can deploy the model locally as well. There are three methods to run the model they are listed below.
  

#### Python Virtual Environment
- `cd` into deployment directory. and run `python -m venv .venv` to create a virtual environment.
- run `pip install -r requirements.txt`
- run `python app.py`
- open `http://localhost:7860/` in you browser to see the app

## References

[1] [TwinLiteNet: An Efficient and Lightweight Model for Driveable Area and Lane Segmentation in Self-Driving Cars](https://arxiv.org/abs/2307.10705), **Authors**: Quang Huy Che, Dinh Phuc Nguyen, Minh Quan Pham, Duc Khai Lam, **Year**: 2023. Click [here](https://github.com/chequanghuy/TwinLiteNet) to go the TwinLiteNet Repository

[2] The Stable Diffusion code is taken from here. click [here](https://colab.research.google.com/github/huggingface/notebooks/blob/main/diffusers/stable_diffusion.ipynb)
