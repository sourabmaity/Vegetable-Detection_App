<h1 style="font-size:10vw"> Vegetable-Detection_App-<img src="https://github.com/SOURAB-BAPPA/Vegetable-Detection_App/blob/main/images/vegetable.gif" width=70 height=70 />  <img src="https://user-images.githubusercontent.com/73097560/115834477-dbab4500-a447-11eb-908a-139a6edaec5c.gif"></h1>

## Overview

This tutorial provides step-by-step instructions on how to create an Android app using YOLO V5 .pt model and Android Studio. By following the tutorial, you will be able to use your Android app to detect objects through supervised machine learning. 

This is an example application for [PyTorch](https://pytorch.org/mobile/android/#more-pytorch-android-demo-apps) on Android. It uses image classification to continuously classify objects it sees from the device's back camera. Inference is performed using the PyTorch Lite Java API. The demo app classifies frames in real-time, displaying the top most probable classifications. 

### Tutorial Overview

1. Data collection
2. Train the model using collected image data
3. Export machine learning model
4. Incorporate model into an Android app

### Requirements

*   [Android Studio Arctic Fox | 2020.3.1 Patch 3](https://developer.android.com/studio) (installed on a Mac, Linux, or Windows machine) or above
*   Android device in [developer mode](https://developer.android.com/studio/debug/dev-options) with USB debugging enabled
*   USB cable (to connect Android device to your computer)

<b>Icon-</b><img src="https://github.com/SOURAB-BAPPA/Vegetable-Detection_App/blob/main/images/icon4.png" width=70 height=70 />

<img src="https://github.com/SOURAB-BAPPA/Vegetable-Detection_App/blob/main/images/front_page.jpg" alt="App Example Page" width="300" height="520"/><img src="https://github.com/SOURAB-BAPPA/Vegetable-Detection_App/blob/main/images/2.jpg" alt="App Example Page" width="300" height="520"/>

<img src="https://github.com/SOURAB-BAPPA/Vegetable-Detection_App/blob/main/images/4.jpg" alt="App Example Page" width="300" height="520"/><img src="https://github.com/SOURAB-BAPPA/Vegetable-Detection_App/blob/main/images/3.jpg" alt="App Example Page" width="300" height="520"/>


## Create and train model

### Step 1: Data collection

To train the machine learning model, we'll be using [YOLO V5](https://github.com/ultralytics/yolov5). 

Begin by deciding which objects you plan to train your model on. Then, you can collect your data in two ways: 

1. Take photos of each object using your phone camera , and label them for each class. 
2. Take photos of multiple object in a single frame using your phone camera , and label them individually for each class.

### Notes
*   Be sure to only include one object for each class (i.e. make sure there are no tometo images in the potato class and vice versa). 
*   It is recommended that you take at least 100 image samples for each class and have at least 3 classes. 
*   Your image samples should also ideally be from different angles.

### Step 2: Train the model using collected image data

Run the script below to generate a custom model `best.torchscript.pt` located in `runs/train/exp/weights`:

```
python train.py --img 640 --batch 16 --epochs 3 --data  data.yaml  --weights yolov5s.pt
```

The precision of the model with the epochs set as 3 is very low - less than 0.01 actually; with a tool such as [Weights and Biases](https://wandb.ai), which can be set up in a few minutes and has been integrated with YOLOv5, you can find that with `--epochs` set as 80, the precision gets to be 0.95. But on a CPU machine, you can quickly train a custom model using the command above, then test it in the Android demo app. Below is a sample wandb metrics from 3, 30, and 100 epochs of training:

![](https://github.com/SOURAB-BAPPA/Vegetable-Detection_App/blob/main/ObjectDetection/metrics.png)

### Step 3. Convert the custom model to lite version

With the `export.py` modified in step 1 `Prepare the model` of the section `Quick Start`, you can convert the new custom model to its TorchScript lite version:

```
python export.py --weights runs/train/exp/weights/best.pt --include torchscript
```

The resulting `best.torchscript.ptl` is located in `runs/train/exp/weights`, which needs to be copied to the Android demo app's assets folder.


<br/>

## Build and run app

### Step 4: Export machine learning model

Now that you've trained your model, you'll need to export it. Download `best.torchscript.ptl` is located in `runs/train/exp/weights` and create a `label.txt`.

Now, get the project from [Github](https://github.com/SOURAB-BAPPA/Vegetable-Detection_App). Click the green code button and download ZIP. You will need to click through a number of folders (``Vegetable-Detection_App/ObjectDetection/app/src/main/assets/``). In assets, copy the ``label.txt``, and ``best.torchscript.ptl`` files (you only need to keep one ``label.txt`` file) into assets. 



### Step 5: Build the Android Studio project

Now, copy the file path for the android folder. Open Android Studio and click "Open an existing Android Studio project." A window should pop up that says "Open File or Project." At the top of the window, copy paste the file path and click OK. 

Then in `PrePostProcessor.java`, change line `private static int mOutputColumn = 9;` to `private static int mOutputColumn = no_of_labels+5;`.(label.txt defines the 4 custom class names)

Select `Build -> Make Project` and check that the project builds successfully. You will need Android SDK configured in the settings. You'll need at least SDK
version 23. The `build.gradle` file will prompt you to download any missing libraries.


### Step 6: Turn on Android phone developer mode

In our example, we have used a Motorola Moto E4 phone. Go to _Settings_ and scroll to _About phone_. Scroll down to _Build number_ and click it seven times. After a few taps, the steps should count down until you unlock the developer options. Then, back in _Settings_, scroll to _Developer options_ and turn Developer mode on. Once developer options are activated, you will see a message that reads, You are now a developer!


### Step 7: Install and run the app

Connect the Android device to the computer and be sure to approve any ADB permission prompts that appear on your phone. Select `Run -> Run app.` Select the deployment target in the connected devices to the device on which the app will be installed. This will install the app on the device. The app should automatically open and it should be able to recognize the objects you trained the model on. If the labels are not showing up, make sure the ``label.txt`` file is still in your assets folder.


## Assets folder
_Do not delete the assets folder content_. If you explicitly deleted the files, choose `Build -> Rebuild` to re-download the deleted model files into the assets folder.


## Sources

[PyTorch](https://pytorch.org/mobile/android/)
[The model version must be between 3 and 5But the model version is 7](https://github.com/pytorch/android-demo-app/issues/202)

## Contact

For any questions, suggestions, or concerns, please contact me at maitysourab@gmail.com.

