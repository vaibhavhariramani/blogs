---
title: "FACE DETECTION Using OpenCV"
excerpt: "Learn how to develop a face identification system Using Haar cascade classifier in PYTHON using OpenCV"
header:
  image: /assets/images/computer_vision/haar.jpg
  teaser: /assets/images/computer_vision/haar.jpg
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

Today We will Learn how to develop a face identification system using haar cascade classifier.



![Image for post](https://miro.medium.com/max/1373/1*pB-quHdy-X-EvdCDqZNlaA.png)

A **facial identification system** is a technology capable of identifying a face of a person from a digital image or a video frame from a video source.

**Cascade classifier**, or namely *cascade of boosted classifiers working with haar-like features*, is a special case of **ensemble learning**, called **boosting**. It typically relies on [**Adaboost**](https://www.google.com/url?q=https%3A%2F%2Fmaelfabien.github.io%2Fmachinelearning%2Fadaboost&sa=D&sntz=1&usg=AFQjCNFeQbwPdev7PoN6X7Mhb9A-3Bh0iQ) classifiers (and other models such as Real Adaboost, Gentle Adaboost or Logitboost).

Cascade classifiers are trained on a few hundred sample images of image that contain the object we want to detect, and other images that do not contain those images.

# How can we detect if a face is there or not ?

There is an algorithm, called **Viola–Jones object detection framework**, that includes all the steps required for live face detection :

*   **Haar Feature Selection**, features derived from Haar wavelets
*   **Create integral image**
*   **Adaboost Training**
*   **Cascading Classifiers**



![Image for post](https://miro.medium.com/max/992/1*MDkpzf_3JidDLHhR09yawA.png)

> **Haar Cascade classifier** is based on the Haar Wavelet technique to analyze pixels in the image into squares by function. This uses “integral image” concepts to compute the “features” detected.

**Haar cascade classifier** is based on the **Viola-Jones detection algorithm** which is trained in given some input faces and non-faces and training a classifier that identifies a face.

# I.1.a. Haar Feature Selection

## There are some common features that we find on most common human faces :

*   *a dark eye region compared to upper-cheeks*
*   *a bright nose bridge region compared to the eyes*
*   *some specific location of eyes, mouth, nose…*

The characteristics are called **Haar Features**. The feature extraction process will look like this :

![Image for post](https://miro.medium.com/max/850/1*-evey1MrqLmlaVeSAX7vZw.png)

Haar Features


![Image for post](https://miro.medium.com/max/1194/1*gl4JHntNPHQt1G7txpiRMA.png)

Haar features are similar to these **convolution kernels** which are used to detect the presence of that feature in the given image.

> *Detect edges using convolution kernels:*

Given an input image and convolution kernel, we place kernel to a corner and do convolution multiplication shifting the kernels.


![Image for post](https://miro.medium.com/max/390/0*Ms94ZOyysSZ7c6A8.gif)

This method is used to detect different types of edges using different kernels.

> A Haar-Feature is just like a kernel in CNN, except that in a CNN, the values of the kernel are determined by training, while a Haar-Feature is manually determined.



![Image for post](https://miro.medium.com/max/491/0*g_FpO-ZYVFWiedDF.png)

Here are some Haar-Features. The first two are “**edge features**”, used to detect edges. The third is a “line feature”, while the fourth is a “four rectangle feature”, most likely used to detect a slanted line.

***When haar features are applied to image of a girl.***

Each feature results in a single value which is calculated by subtracting the sum of pixels under a white rectangle from the sum of pixels under the black rectangle.


![Image for post](https://miro.medium.com/max/373/0*cWZ_lbufqZ2p-kX3.png)

Every haar feature has some sort of resemblance to identify a part of the face.

Viola-Jones uses 24\*24 as base window size and calculates the above features all over the image shifting by 1 PX.

If we consider all possible parameters of the haar features like position, scale, and type we end up calculating about 160,000+ features. So we need to evaluate a huge set of features for every 24\*24 PX.

So to avoid this we have an idea to avoid redundant features and pick only those features which are very useful for us. This can be done using **AdaBoost**.

# I.1.b. The integral image

Computing the rectangle features in a convolutional kernel style can be long, very long. For this reason, the authors, Viola and Jones, proposed an intermediate representation for the image : the integral image. The role of the integral image is to allow any rectangular sum to be computed simply, using only four values. We’ll see how it works !

Suppose we want to determine the rectangle features at a given pixel with coordinates (x,y). Then, the integral image of the pixel in the sum of the pixels above and to the left of the given pixel.


![Image for post](https://miro.medium.com/max/1280/1*BUF_hZ4wPTT9Ewg8Hogfow.png)

where ii(x,y) is the integral image and i(x,y) is the original image.

When you compute the whole integral image, there is a form a recurrence which requires only one pass over the original image. Indeed, we can define the following pair of recurrences :


![Image for post](https://miro.medium.com/max/1038/1*Zvv5XU_USrtrzRY7dvJKtw.png)

where s(x,y) is the cumulative row sum and and s(x−1)=0, ii(−1,y)=0.

How can that be useful ? Well, consider a region D for which we would like to estimate the sum of the pixels. We have defined 3 other regions : A, B and C.

*   The value of the integral image at point 1 is the sum of the pixels in rectangle A.
*   The value at point 2 is A + B
*   The value at point 3 is A + C
*   The value at point 4 is A + B + C + D.

Therefore, the sum of pixels in region D can simply be computed as : 4+1−(2+3).

And over a single pass, we have computed the value inside a rectangle using only 4 array references.


![Image for post](https://miro.medium.com/max/1280/1*IQFQ0wVMegmYzRczxEop-w.png)

# I.1c. Learning the classification function with Adaboost

As stated previously there can be approximately 160,000 + feature values within a detector at 24\*24 base resolution which need to be calculated. But it is to be understood that only a few set of features will be useful among all these features to identify a face.

> **AdaBoost is used to remove redundant features and choose only relevant features.**


![Image for post](https://miro.medium.com/max/296/1*7iHDSDp5ttlEJqJPrzYHcQ.jpeg)

**For example, feature detecting a vertical edge is useful detecting a nose but irrelevant detecting a lip.**

Given a set of labeled training images (positive or negative), Adaboost is used to :

*   select a small set of features
*   and train the classifier

> AdaBoost is used to get the best features among all these 160,000+ features. These features are also called as weak classifiers. After these features are found a weighted combination of all these features in use in evaluating and deciding any given window has a face or not.

# I.1.d. Cascading Classifier
Although the above processes described above is quite efficient, a major issue remains. In an image, most of the image is a non-face region. Giving equal importance to each region of the image makes no sense, since we should mainly focus on the regions that are most likely to contain a picture. Viola and Jones achieved an increased detection rate while reducing computation time using Cascading Classifiers.

The key idea is to reject sub-windows that do not contain faces while identifying regions that do. Since the task is to identify properly the face, we want to minimize the false negative rate, i.e the sub-windows that contain a face and have not been identified as such.

A series of classifiers are applied to every sub-window. These classifiers are simple decision trees :

*   if the first classifier is positive, we move on to the second
*   if the second classifier is positive, we move on to the third

Any negative result at some point leads to a rejection of the sub-window as potentially containing a face. The initial classifier eliminates most negative examples at a low computational cost, and the following classifiers eliminate additional negative examples but require more computational effort.

![Image for post](https://miro.medium.com/max/622/1*U8LBlqSa0A6Tmrbhf7FDhA.png)

The classifiers are trained using Adaboost and adjusting the threshold to minimize the false rate. When training such model, the variables are the following :

*   the number of classifier stages
*   the number of features in each stage
*   the threshold of each stage

> *The job of each stage is used to determine whether a given sub window is definitely not a face or may be a face. A given sub window is immediately discarded as not a face if it fails in any of the stage.*

Luckily in **OpenCV**, this whole model is already **pre-trained** for face detection.

The **viola-Jones face detection algorithm** is trained and weights are stored in the disk. All we do is take the features from the file and apply to our image, if the face is present in the image we get the face location.


![Image for post](https://miro.medium.com/max/1050/1*3BLPNeN44I7joSmc_DLlPw.png)

cat face detected using **viola-Jones face detection algorithm in openCV.**

You can find the face detection implementation by OpenCV “*haarcascade\_frontalface\_default.xml*” file [here](https://github.com/vaibhavhariramani/Computer-Vision-Tutorial/tree/main/Haarcascades).

# Further Use

After identifying a face or multiple faces from an image we can implement a deep learning face recognition system that can classify a given face-recognizing a person.

To See the implementation of **Haar cascade classifier** and **face recognition** using OpenCV in python Checkout this blog **here**.

# Results

I will made a quick [YouTube] illustration of the face detection algorithm.

<iframe width="665" height="382" src="https://www.youtube.com/embed/ONB8NLMiY30" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

# Drawbacks of Haar Cascade Classifiers

Althrough , While cascade methods are extremely fast, they leave much to be desired. If you’ve ever used the Haar cascade classifiers provided by OpenCV (i.e. the Viola-Jones detectors) to detect faces you’ll know exactly what I’m talking about.

![Image for post](https://miro.medium.com/max/400/1*v2DMzQlqpp_pGAxDWhbJWQ.png)

**Figure :** Example of falsely detecting a face in an image. This is a common problem when using Haar cascade classifiers.

> In order to detect faces/humans/objects/whatever in OpenCV (and remove the false positives), you’ll spend a lot of time tuning the cv2.detectMultiScale parameters. And again, there is no guarantee that the exact same parameters will work from image-to-image. This makes batch-processing large datasets for face detection a tedious task since you’ll be very concerned with either (1) falsely detecting faces or (2) missing faces entirely, simply due to poor parameter choices on a per image basis.

To troubleshoot this issue we have **Histogram of Oriented Gradients** approach about which we will discuss in new blog untill then stay tuned..

Here is The original [paper](https://www.cs.cmu.edu/~efros/courses/LBMV07/Papers/viola-cvpr-01.pdf) was published in 2001 by Viola & Jones.


This article was originally published on my personal blog : [https://vaibhavhariramani.blogspot.com/2020/04/a-full-guide-to-face-detection.html](https://www.google.com/url?q=https%3A%2F%2Fvaibhavhariramani.blogspot.com%2F2020%2F04%2Fa-full-guide-to-face-detection.html&sa=D&sntz=1&usg=AFQjCNEyokBkr5ZnYnY3T_zlhxzk6Nm1qw)



## geeky baba

### In this tutorial, we'll see how to create and launch a face detection algorithm in Python using OpenCV. We'll also add…

#### [vaibhavhariramani.blogspot.com](https://vaibhavhariramani.blogspot.com/2020/04/a-full-guide-to-face-detection.html)

The Github repository of this article (and all the others from my blog) can be found here :

# vaibhavhariramani / FaceDetection

## vaibhavhariramani/FaceDetection

### Face detection is a computer vision technology that helps to locate/visualize human faces in digital images. This…

 [github.com](https://github.com/vaibhavhariramani/FaceDetection)

## [This repo contains exercises, code, tutorials and articles of my personal blog — geekyBawa/Computer\_Vision](https://www.google.com/url?q=https%3A%2F%2Fgithub.com%2Fvaibhavhariramani%2FFaceDetection&sa=D&sntz=1&usg=AFQjCNHpXIUpF4Paju36SqMd1hZeLUf6kQ)

# Do***Checkout My other Blogs***

## GEEKY TRAVELLER - Face Detection

### Then, we apply this rectangle as a convolutional kernel, over our whole image. In order to be exhaustive, we should…

[sites.google.com](https://sites.google.com/view/geeky-traveller/computer-vision/face-detection)


## Vaibhav's Blog

### A site for Machine Learning Tutorials and Articles

[vaibhavhariramani.github.io](https://vaibhavhariramani.github.io/blogs)

# Made with ❤️by Vaibhav Hariramani

*   *If you enjoyed this,* [*follow me on Medium*](https://medium.com/@vaibhav.hariramani01) *for more*
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
  
