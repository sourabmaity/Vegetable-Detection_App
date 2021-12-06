# Vegetable-Detection_App

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

<img src="https://github.com/SOURAB-BAPPA/Vegetable-Detection_App/blob/main/images/front_page.jpg" alt="App Example Page" width="300" height="520"/>


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

Once you have collected all the data and created your classes, you will train your model. Click the "Train Model" button. You do not need to change any of the values under Advanced (you can keep the number of epochs, batch size, and learning rate as the default values). 

In the preview window, turn the input on and test out your model. Under the output, your model should be able to distinguish between the objects. If you would like to add more image samples, you will need to go through the process again to re-train your model.

<br/>

## Build and run app

### Step 3: Export machine learning model

Now that you've trained your model, you'll need to export it. As the model will be used in an Android app, click on the Tensorflow Lite tab and download both the floating point and quantized models. When you download your floating point model, the folder should include two files: labels.txt and model_unquant.tflite. Similarly, in your quantized model folder, you should have labels.txt and model.tflite. 

Now, get the project from [Github](https://github.com/tensorflow/examples). Click the green code button and download ZIP. You will need to click through a number of folders (``examples-master/lite/examples/image_classification/android/app/src/main/assets``). In assets, copy the ``labels.txt``, ``model.tflite``, and ``model_unquant.tflite`` files (you only need to keep one ``labels.txt`` file) into assets. 


### Step 4: Incorporate model into an Android app

Now, copy the file path for the android folder. Open Android Studio and click "Open an existing Android Studio project." A window should pop up that says "Open File or Project." At the top of the window, copy paste the file path and click OK. 

Your screen should look like the one below. Double click on CameraActivity (found under ``app/java/org.tensorflow.lite.examples.classification``), find (using Ctrl+f on Windows) the line of code that says 

```
private Model model = Model.QUANTIZED_EFFICIENTNET;
``` 

and change QUANTIZED to FLOAT so that the line of code now reads 

```
private Model model = Model.FLOAT_EFFICIENTNET;
```

Now, inside the tflite package under ``org.tensorflow.lite.examples.classification`` folder, double click on the ClassifierFloatEfficientNet.java class and find the function getModelPath(). Inside this function, there should be a line that says 

```
return "efficientnet-lite0-fp32.tflite";
```

Change the file so that the code now reads

```
return "model_unquant.tflite";
```

In the same class, find getLabelPath() and change the code to return ``"labels.txt"`` instead of ``"labels_without_background.txt"``. 

Now, open the ClassiferFloatMobileNet.java class. Again, change the return statement in the function getModelPath() to ``return "model.unquant.tflite"`` instead of the existing line of code.

Next, open the ClassifierQuantizedEfficientNet.java class and find the function getModelPath(). This time, we will change ``return "efficientnet-lite0-int8.tflite";`` to ``return "model.tflite";`` and change the return value in ``getLabelPath()`` to ``return "labels.txt";``.

Do the same for ClassifierQuantizedMobileNet.java. 


### Step 6: Build the Android Studio project

Select `Build -> Make Project` and check that the project builds successfully. You will need Android SDK configured in the settings. You'll need at least SDK
version 23. The `build.gradle` file will prompt you to download any missing libraries.

The file `download.gradle` directs gradle to download the two models used in the example, placing them into `assets`.

<aside class="note"><b>Note:</b><p>`build.gradle` is configured to use TensorFlow Lite's nightly build.</p><p>If you see a build error related to compatibility with Tensorflow Lite's Java API (for example, `method X is undefined for type Interpreter`), there has likely been a backwards compatible change to the API. You will need to run `git pull` in the examples repo to obtain a version that is compatible with the nightly build.</p></aside>

<img src="images/build-app-1.png?raw=true" alt="App Example Page" width="427" height="204"/>

<img src="images/build-app-2.png?raw=true" alt="App Example Page" width="732" height="204"/>


### Step 7: Turn on Android phone developer mode

In our example, we have used a Motorola Moto E4 phone. Go to _Settings_ and scroll to _About phone_. Scroll down to _Build number_ and click it seven times. After a few taps, the steps should count down until you unlock the developer options. Then, back in _Settings_, scroll to _Developer options_ and turn Developer mode on. Once developer options are activated, you will see a message that reads, You are now a developer!


### Step 8: Install and run the app

Connect the Android device to the computer and be sure to approve any ADB permission prompts that appear on your phone. Select `Run -> Run app.` Select the deployment target in the connected devices to the device on which the app will be installed. This will install the app on the device. The app should automatically open and it should be able to recognize the objects you trained the model on. If the labels are not showing up, make sure the ``labels.txt`` file is still in your assets folder.

<img src="images/run-app.png?raw=true" alt="App Example Page" width="223" height="152"/>


## Assets folder
_Do not delete the assets folder content_. If you explicitly deleted the files, choose `Build -> Rebuild` to re-download the deleted model files into the assets folder.


## Sources

[Google's Teachable Machine 2.0](https://teachablemachine.withgoogle.com/)

[TensorFlow Reference Code](https://github.com/tensorflow/examples)

[Dinesh Raturi's Fruits Prediction Android App Youtube Tutorial](https://www.youtube.com/watch?v=fNbxSXi0OkA)

[How-To Geek: How to Access Developer Options and Enable USB Debugging on Android](https://www.howtogeek.com/129728/how-to-access-the-developer-options-menu-and-enable-usb-debugging-on-android-4.2/)

## Contact

For any questions, suggestions, or concerns, please contact me at alexiswu@princeton.edu.

