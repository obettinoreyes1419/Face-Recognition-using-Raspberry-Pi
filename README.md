# Face-Recognition-using-Raspberry-Pi
## Description 
A small project which does face detection using OpenCV library on RaspberryPi.
## Content
1. [What's Raspberry pi?](#rpi)
2. [Face Recognition?](#facerecognition)
3. [OpenCV Library?](#opencv)
4. [Project](#project)
   * [Requirements](#requirements)
   * [Procedure](#procedure)
<a name="rpi"> </a>   
## What's Raspberry pi?
> Raspberry pi is a mini computer(credit card sized), which is capable of doing various tasks but with less computation power as it doesn't have powerful resources. The Raspberry Pi is slower than a modern laptop or desktop but is still a complete Linux computer and can provide all the expected abilities that implies, at a low-power consumption level.  
[Explore more](https://www.raspberrypi.org/)
<a name="facerecognition"> </a>
## what is Face Recognition?
> Face Recognition/Facial Recognition is a category of biometric software which identifies people by their faces. Face is captured by digital camera and the system is trained and then it is capable of identifying the person.
<a name="opencv"></a>
## OpenCV Library
> OpenCV (Open Source Computer Vision Library) is an open source computer vision and machine learning software library. OpenCV was built to provide a common infrastructure for computer vision applications and to accelerate the use of machine perception in the commercial products. It has C++, Python and Java interfaces and supports Windows, Linux, Mac OS, iOS and Android.  
[Read more](https://opencv.org)
## Project
> In this project we will learn how to detect real-time faces on Picam.
<a name="requirements"> </a>
### Stuff used in this projects
1. [Raspberry Pi 3 Model-B](https://www.raspberrypi.org/products/raspberry-pi-3-model-b/)
2. [Raspberry Pi camera module](https://www.amazon.in/gp/product/B00L1FOIIS/ref=oh_aui_detailpage_o07_s00?ie=UTF8&psc=1)
### Softwares
1. [OpenCV Library](https://opencv.org)
2. [Python3](https://www.python.org)
3. [TextEditor](https://atom.io)

## 1] Introduction :
>This project was done with "Open Source Computer Vision Library", the OpenCV. In this project, we will be using Raspberry Pi (so, Raspbian as OS) and Python.OpenCV was designed for computational efficiency and with a strong focus on real-time applications. So, it's perfect for real-time face recognition using a camera.

## 2] Project Structure :
1. Face Detection and Data Gathering.
2. Train the Sysytem.
3. Face Recognition.
<a name="procedure"></a>
## 3] Procedure :
**Step 1: Installing OpenCV library**
I am using a Raspberry Pi V3 updated to the last version of Raspbian (Stretch), so the best way to have OpenCV installed, is to follow the awesome tutorial developed by Adrian Rosebrock: [Raspbian Stretch: Install OpenCV 3 + Python on your Raspberry Pi](https://www.pyimagesearch.com/2017/09/04/raspbian-stretch-install-opencv-3-python-on-your-raspberry-pi/)

Once you finished the tutorial, you should have an OpenCV virtual environment ready to run our experiments on your Pi.
Let's go to our virtual environment and confirm that OpenCV 3 is correctly installed.

Run the command `source` each time you open up a new terminal to ensure your system variables have been set up correctly.  
`source ~/.profile` 

Next, let's enter on our virtual environment:  
`workon cv`

If you see the text (cv) preceding your prompt, then you are in the cv virtual environment:  
`(cv) pi@raspberry:~$`

Now, enter in your Python interpreter :  
`python`

then import the OpenCV library :  
`>>import cv2`

If no error messages appear, the OpenCV is correctly installed ON YOUR PYTHON VIRTUAL ENVIRONMENT.

You can also check the OpenCV version installed:  
`cv2.__version__`

**Step 2: Testing Camera**
Once you have OpenCV installed in your Raspberry Pi, let's test to confirm that your camera is working properly.

I am assuming that you have a PiCam already installed on your Raspberry Pi.

You must have the camera enabled when you ran through Adrian's tutorial, otherwise, the drivers will not be installed correctly.
In case you get an error like: OpenCV Error: Assertion failed , you can try solve the issue, using the command:  
`sudo modprobe bcm2835-v4l2`

Once you have all drivers correctly installed, enter the below Python code on your IDE or any Text Editor :  
```python
import numpy as np
import cv2
cap = cv2.VideoCapture(0)
cap.set(3,640) # set Width
cap.set(4,480) # set Height
while(True):
    ret, frame = cap.read()
    if ret:
        gray = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)
        cv2.imshow('frame', frame)
        cv2.imshow('gray', gray)

        k = cv2.waitKey(30) & 0xff
    if k == 27: # press 'ESC' to quit
        break
cap.release()
cv2.destroyAllWindows()
```
The above code will capture the video stream that will be generated by your PiCam, displaying both, in BGR color and Gray mode.
You can download the code [CamTest.py](CamTest.py) from above as well.
Now, Run the program :  
`python CamTest.py`

Some may found issues when trying to open the camera ( "Assertion failed" error messages). That could happen if the camera was not enabled during OpenCv installation and so, camera drivers did not install correctly. To correct, use the command:  
`sudo modprobe bcm2835-v4l2`

**Step 3: Face Detection**
The most basic task on Face Recognition is of course, "Face Detecting". Before anything, you must "capture" a face in order to recognize it, when compared with a new face captured on future.

The most common way to detect a face (or any objects), is using the "Haar Cascade classifier"

Object Detection using Haar feature-based cascade classifiers is an effective object detection method proposed by Paul Viola and Michael Jones in their paper, "Rapid Object Detection using a Boosted Cascade of Simple Features" in 2001. It is a machine learning based approach where a cascade function is trained from a lot of positive and negative images. It is then used to detect objects in other images.

Here we will work with face detection. Initially, the algorithm needs a lot of positive images (images of faces) and negative images (images without faces) to train the classifier. Then we need to extract features from it. The good news is that OpenCV comes with a trainer as well as a detector. If you want to train your own classifier for any object like car, planes etc. you can use OpenCV to create one. Its full details are given here: [Cascade Classifier Training.](https://docs.opencv.org/3.3.0/dc/d88/tutorial_traincascade.html)

Enough theory, let's create a face detector with OpenCV!

Download the file: [faceDetection.py](faceDetection.py)

```python
import numpy as np
import cv2
faceCascade = cv2.CascadeClassifier('/home/pi/opencv-3.4.1/data/haarcascades/haarcascade_frontalface_default.xml')
cap = cv2.VideoCapture(0)
cap.set(3,640) # set Width
cap.set(4,480) # set Height
while True:
    ret, img = cap.read()
    gray = cv2.cvtColor(img, cv2.COLOR_BGR2GRAY)
    faces = faceCascade.detectMultiScale(
        gray,
        scaleFactor=1.2,
        minNeighbors=5,
        minSize=(20,20)
    )
    for (x,y,w,h) in faces:
        cv2.rectangle(img,(x,y),(x+w,y+h),(255,0,0),2)
        roi_gray = gray[y:y+h, x:x+w]
        roi_color = img[y:y+h, x:x+w]
    cv2.imshow('video',img)
    k = cv2.waitKey(30) & 0xff
    if k == 27: # press 'ESC' to quit
        break
cap.release()
cv2.destroyAllWindows()
```

When you compare with the last code used to test the camera, you will realize that few parts were added to it. Note the line below:  
`faceCascade = cv2.CascadeClassifier('Cascades/haarcascade_frontalface_default.xml')`  
This is the line that loads the "classifier" (that must be in a directory named "Cascades/", under your project directory).

Then, we will set our camera and inside the loop, load our input video in grayscale mode (same we saw before).

Now we must call our classifier function, passing it some very important parameters, as scale factor, number of neighbors and minimum size of the detected face.

```python
faces = faceCascade.detectMultiScale(
        gray,     
        scaleFactor=1.2,
        minNeighbors=5,     
        minSize=(20, 20)
    )
```

Where,

`* gray` is the input grayscale image.  
`* scaleFactor` is the parameter specifying how much the image size is reduced at each image scale. It is used to create the scale pyramid.  
`* minNeighbors` is a parameter specifying how many neighbors each candidate rectangle should have, to retain it. A higher number gives lower false positives.  
`* minSize` is the minimum rectangle size to be considered a face.  

The function will detect faces on the image. Next, we must "mark" the faces in the image, using, for example, a blue rectangle. This is done with this portion of the code:  
```python
for (x,y,w,h) in faces:
    cv2.rectangle(img,(x,y),(x+w,y+h),(255,0,0),2)
    roi_gray = gray[y:y+h, x:x+w]
    roi_color = img[y:y+h, x:x+w]
```
If faces are found, it returns the positions of detected faces as a rectangle with the left up corner (x,y) and having "w" as its Width and "h" as its Height ==> (x,y,w,h).
