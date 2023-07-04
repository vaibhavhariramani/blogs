---
title: "FACE DETECTION Using Haar cascade in C.V."
excerpt: "FACE DETECTION Using Haar cascade classifier in PYTHON using OpenCV"
header:
  image: /assets/images/computer_vision/face-detect.png
  teaser: /assets/images/computer_vision/face-detect.png
sidebar:
  - title: "About the Author"
    image: https://vaibhavhariramani.github.io/blogs/images/bio-pic-2.jpg
    image_alt: "logo"
    text: "I am a Machine Learning enthausiast by profession and a Blogger by passion. I have been working on machine learning projects for more than 2 years."
  - title: "Other Blogs"
    text: " [Here](https://vaibhavhariramani.github.io/blogs/) you will find articles on “Machine Learning, Statistics, Deep Learning, NLP and Artificial Intelligence”."
gallery:
  - url: /assets/images/unsplash-gallery-image-1.jpg
    image_path: assets/images/unsplash-gallery-image-1-th.jpg
    alt: "placeholder image 1"
  - url: /assets/images/unsplash-gallery-image-2.jpg
    image_path: assets/images/unsplash-gallery-image-2-th.jpg
    alt: "placeholder image 2"
  - url: /assets/images/unsplash-gallery-image-3.jpg
    image_path: assets/images/unsplash-gallery-image-3-th.jpg
    alt: "placeholder image 3"
---

In [previous blog](https://medium.com/geeky-bawa/face-identification-using-haar-cascade-classifier-af3468a44814) we discussed how [Face Identification using Haar cascade classifier](https://medium.com/geeky-bawa/face-identification-using-haar-cascade-classifier-af3468a44814) works(theory) . In today’s blog we will implement FACE DETECTION Using Haar cascade classifier in PYTHON using OpenCV library and haarcascade\_frontalface\_default.xml.



![Image for post](https://miro.medium.com/max/483/1*kLF2LdYe5vQcEtzauJ0dhw.png)

# **PREREQUISITES**

> 1.) Download [*haarcascade\_frontalface\_default.xml*](https://github.com/vaibhavhariramani/Computer-Vision-Tutorial/blob/main/Haarcascades/haarcascade_frontalface_default.xml) *\[from repo link given below\]*

## 2.) pip install opencv-python

[link to github Repo of my Computer Vision Tutorial](https://github.com/vaibhavhariramani/Computer-Vision-Tutorial/archive/main.zip)



## vaibhavhariramani/FaceDetection

### Face detection is a computer vision technology that helps to locate/visualize human faces in digital images. This…

[github.com](https://github.com/vaibhavhariramani/FaceDetection)



## vaibhavhariramani/Computer-Vision-Tutorial

### You can't perform that action at this time. You signed in with another tab or window. You signed out in another tab or…

[github.com](https://github.com/vaibhavhariramani/Computer-Vision-Tutorial)

# I.) CODE with explanation

# I.1. Importing libraries
```python
**import cv2**
```

# I.2. Importing cascade files

The second step simply is to locate the pre-trained weights(cascade file). We will be using default pre-trained models to detect face, eyes and mouth. Depending on your version of Python, the files should be located in :

```
python3.7/site-packages/cv2/data
```

otherwise you can get all cascade files from our repo [here:](https://github.com/vaibhavhariramani/Computer-Vision-Tutorial/tree/main/Haarcascades)

Once identified, we’ll declare Cascade classifiers this way :

```python
cascPath = "haarcascade/haarcascade\_frontalface\_default.xml"  
eyePath = "haarcascade/haarcascade\_eye.xml"  
smilePath = "haarcascade/haarcascade\_smile.xml" faceCascade = cv2.CascadeClassifier(cascPath)  
eyeCascade = cv2.CascadeClassifier(eyePath)  
smileCascade = cv2.CascadeClassifier(smilePath)
```

# I.3. **Load the image**

Before implementing the real time face detection algorithm, let’s try a simple version on an image. We can start by loading a test image :

```python
# Load the image
gray = cv2.imread(‘face\_detect\_test.jpeg’, 0)plt.figure(figsize=(12,8))
plt.imshow(gray, cmap=’gray’)
plt.show()
```


![Image for post](https://miro.medium.com/max/483/1*GWcYlyBJQWVd182idz8pxQ.png)

**Test image**

# I.3. Detect face on an image

Now, we detect the face and we add a rectangle around it :

```python
# Detect faces
faces = faceCascade.detectMultiScale(
  gray,
  scaleFactor=1.1,
  minNeighbors=5,
  flags=cv2.CASCADE\_SCALE\_IMAGE
)# For each face
for (x, y, w, h) in faces:
  # Draw rectangle around the face
  cv2.rectangle(gray, (x, y), (x+w, y+h), (255, 255, 255), 3)
 ```

Here is a list of the most common parameters of the detectMultiScale function :

*   **scaleFactor** : Parameter specifying how much the image size is reduced at each image scale.
*   **minNeighbors** : Parameter specifying how many neighbors each candidate rectangle should have to retain it.
*   **minSize** : Minimum possible object size. Objects smaller than that are ignored.
*   **maxSize** : Maximum possible object size. Objects larger than that are ignored.

Finally, display the result :

```python
plt.figure(figsize=(12,8))
plt.imshow(gray, cmap=’gray’)
plt.show()
```


![Image for post](https://miro.medium.com/max/483/1*-DCtfAFyu-NA-Eg2cA9P2g.png)

Face detection works well on our test image. Let’s move on to real time now !

# I.4. Real time face detection

Let’s move on to the Python implementation of the live facial detection. The first step is to launch the camera, and capture the video. Then, we’ll transform the image to a gray scale image. This is used to reduce the dimension of the input image. Indeed, instead of 3 points per pixel describing Red, Green, Blue, we apply a simple linear transformation :


![Image for post](https://miro.medium.com/max/1124/1*5Vxys2IJ7D5tk7VPQcIuvw.png)

This is implemented by default in OpenCV.

```python
video_capture = cv2.VideoCapture(0)while True:
  # Capture frame-by-frame
  ret, frame = video\_capture.read()
  gray = cv2.cvtColor(frame, cv2.COLOR\_BGR2GRAY)
  ```

Now, we’ll use the faceCascade variable define above, which contains a pre-trained algorithm, and apply it to the gray scale image.
```python
faces = faceCascade.detectMultiScale(
  gray,
  scaleFactor=1.1,
  minNeighbors=5,
  minSize=(30, 30),
  flags=cv2.CASCADE\_SCALE\_IMAGE
)
```

For each face detected, we’ll draw a rectangle around the face :

```python
for (x, y, w, h) in faces:
  if w > 250 :
    cv2.rectangle(frame, (x, y), (x+w, y+h), (255, 0, 0), 3)
    roi_gray = gray[y:y+h, x:x+w]
    roi_color = frame[y:y+h, x:x+w]
```

For each mouth detected, draw a rectangle around it :

```python
smile = smileCascade.detectMultiScale(
  roi_gray,
  scaleFactor= 1.16,
  minNeighbors=35,
  minSize=(25, 25),
  flags=cv2.CASCADE_SCALE_IMAGE
)
for (sx, sy, sw, sh) in smile:
  cv2.rectangle(roi_color, (sh, sy), (sx+sw, sy+sh), (255, 0, 0), 2)
  cv2.putText(frame,’Smile’,(x + sx,y + sy), 1, 1, (0, 255, 0), 1)
```

For each eye detected, draw a rectangle around it :

```python
eyes = eyeCascade.detectMultiScale(roi_gray)
for (ex,ey,ew,eh) in eyes:
  cv2.rectangle(roi_color,(ex,ey),(ex+ew,ey+eh),(0,255,0),2)
  cv2.putText(frame,’Eye’,(x + ex,y + ey), 1, 1, (0, 255, 0), 1)
```

**Then, count the total number of faces, and display the overall image :**

```python
cv2.putText(frame,’Number of Faces : ‘ + str(len(faces)),(40, 40), font, 1,(255,0,0),2)
# Display the resulting frame
cv2.imshow(‘Video’, frame)
```

And implement an exit option when we want to stop the camera by pressing q :

```python
if cv2.waitKey(1) & 0xFF == ord(‘q’):
  break
```

Finally, when everything is done, release the capture and destroy all windows. There are some troubles killing windows on Mac which might require killing Python from the Activity Manager later on.


```python
video_capture.release()
cv2.destroyAllWindows()
```

# I.5. Wrapping it up

<script src="https://gist.github.com/vaibhavhariramani/1332ec1242139614592c6a5a47462584.js"></script>


# I.6. Results

I will made a quick [YouTube](https://www.youtube.com/watch?v=ONB8NLMiY30)[](http://savefrom.net/?url=https%3A%2F%2Fwww.youtube.com%2Fwatch%3Fv%3DONB8NLMiY30&utm_source=userjs-chrome&utm_medium=extensions&utm_campaign=link_modifier "Get a direct link") illustration of the face detection algorithm.

<iframe width="1191" height="670" src="https://www.youtube.com/embed/ONB8NLMiY30" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>



###### [GEEKY BAWA](https://medium.com/geeky-bawa?source=post_sidebar--------------------------post_sidebar-----------)

### just a silly Geek who love to seek out new technologies and experience cool stuff.

# Do Checkout My other Blogs



## GEEKY TRAVELLER - Face Detection

### Then, we apply this rectangle as a convolutional kernel, over our whole image. In order to be exhaustive, we should…

[face-detection-using-haar-cascade-theory/](../face-detection-using-haar-cascade-theory/)


## Vaibhav's Blog

### A site for Machine Learning Tutorials and Articles

[vaibhavhariramani.github.io](https://vaibhavhariramani.github.io/blogs/)

# Do Checkout My Youtube channel



## GHUMMAKKAD BAWA

### Hi, There i am Vaibhav Hariramani a Tech and Travel Blogger always in search of cheap food with great taste and great…

[www.youtube.com](https://www.youtube.com/channel/UCXxjFKZ8-25Qn3DzvMJ_arA)



## GEEKY BAWA

### Hi there! I am VAIBHAV HARIRAMANI (AKA GEEKY BAWA) Tech blogger who love to seek out new technologies and experience…

[www.youtube.com](https://www.youtube.com/channel/UCy7amUpLnsRLEMIaJGGBYog)

# Made with ❤️by Vaibhav Hariramani

*   *If you enjoyed this,* [*follow me on Medium*](https://medium.com/@vaibhavji) *for more*
*   *Follow me on* [*Kaggle*](https://www.kaggle.com/vaibhavhariramani) *for more content!*
*   *Let’s connect on* [*LinkedIn*](https://www.linkedin.com/in/vaibhav-hariramani-087488186/)
*   [*Interested in collaborating?*](https://api.whatsapp.com/send?phone=+917790991077&text=Hi,%20I%20contacted%20you%20Through%20your%20website.) 
*   *Check out my* [*website*](https://vaibhavhariramani.github.io/)*.*
*   *Check out my* [*website*](https://sites.google.com/view/geeky-traveller/home)*.*

### Don't forget to tag us

if you use this repo in  your project don't forget to mention us as Contributer in it . And Don't forget to tag us [Linkedin](https://www.linkedin.com/in/vaibhav-hariramani-087488186/),[ instagram](https://www.instagram.com/geeky_baba_/?hl=en),[ facebook](https://www.facebook.com/jayesh.hariramani.3) ,[ twitter](https://www.linkedin.com/in/vaibhav-hariramani-087488186/), [ Github](https://github.com/vaibhavhariramani) 


# More Resources 

To learn more about these Resources you can Refer to some of these articles written by Me:-

- [Medium](https://medium.com/geeky-bawa)
- [geeky Traveller](https://sites.google.com/view/geeky-traveller/)
- [Blogs](https://github.com/vaibhavhariramani/blogs)
- [Youtube](https://www.youtube.com/channel/UCy7amUpLnsRLEMIaJGGBYog)[![Youtube Badge](https://img.shields.io/badge/-Geeky_Bawa-1ca0f1?style=flat-circle&labelColor=d54b3d&logo=youtube&logoColor=white&link=https://www.youtube.com/channel/UCy7amUpLnsRLEMIaJGGBYog)](https://www.youtube.com/channel/UCy7amUpLnsRLEMIaJGGBYog)
- Email: [vaibhav.hariramani01@gmail.com](mailto:vaibhav.hariramani01@gmail.com)


# Download [THE VAIBHAV HARIRAMANI APP](https://play.google.com/store/apps/details?id=com.geeky.developer)

# [![image](/images/gh-bannner-light.jpg)](https://play.google.com/store/apps/details?id=com.geeky.developer) 


[<img width="95" align='center' src="https://archive.org/download/download-button-png/download-button-png.png">The Vaibhav Hariramani App (Latest Version) ](https://play.google.com/store/apps/details?id=com.geeky.developer)

Download [THE VAIBHAV HARIRAMANI APP](https://play.google.com/store/apps/details?id=com.geeky.developer) consist of Tutorials,Projects,Blogs and Vlogs of our Site developed Using Android Studio with Web View try installing it in your android device.

### Follow me
  
[![Linkedin Badge](https://img.shields.io/badge/-VaibhavHariramani-blue?style=flat-circle&logo=Linkedin&logoColor=white&link=https://www.linkedin.com/in/vaibhav-hariramani-087488186/)](https://www.linkedin.com/in/vaibhav-hariramani-087488186/) [![Instagram Badge](https://img.shields.io/badge/-VaibhavHariramani-e02c73?style=flat-circle&labelColor=e02c73&logo=Instagram&logoColor=white&link=https://www.instagram.com/vaibhav.hariramani/?hl=en)](https://www.instagram.com/vaibhav.hariramani/?hl=en) [![Twitter Badge](https://img.shields.io/badge/-VaibhavHariramani-1ca0f1?style=flat-circle&labelColor=1ca0f1&logo=twitter&logoColor=white&link=https://twitter.com/vaibhavhariram2)](https://twitter.com/vaibhavhariram2) [![GitHub Badge](https://img.shields.io/badge/-@vaibhavhariramani-24292e?style=flat-circle&labelColor=24292e&logo=github&logoColor=white&link=https://github.com/vaibhavhariramani)](https://github.com/vaibhavhariramani) [![Gmail Badge](https://img.shields.io/badge/-VaibhavHariramani-d54b3d?style=flat-circle&labelColor=d54b3d&logo=gmail&logoColor=white&link=mailto:vaibhav.hariramani01@gmail.com)](mailto:vaibhav.hariramani01@gmail.com) [![Medium Badge](https://img.shields.io/badge/-VaibhavHariramani-d54b3d?style=flat-circle&labelColor=d54b3d&logo=medium&logoColor=white&link=https://medium.com/geeky-bawa)](https://medium.com/geeky-bawa) 

Happy coding ❤️ .
  
