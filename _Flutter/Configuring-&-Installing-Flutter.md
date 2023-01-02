---
title: "Configuring & Installing VS code for Flutter and Dart"
excerpt: "Guide to install flutter"
category: Flutter
header:
  image: /assets/images/flutter/1.png
  teaser: /assets/images/flutter/2.png
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

Flutter is a great framework built by google to create hybrid apps for mobile and other platforms. flutter has some really good documentation that you can follow as well. which I would be following as well, the link is given below. The thing you need to have installed on your PC: VS Code or Android Studio, Git.
<br>
Link: [https://flutter.dev/docs/get-started/install/windows]( https://flutter.dev/docs/get-started/install/windows)
<img src="/assets/images/flutter/3.jpg">
This article introduces the basics of flutter and dart theory, laying down the common concepts and techniques involved. This post is intended for the people starting with flutter development, making it easy to follow the core concepts and get comfortable with flutter basics.

## Why Flutter?

### Fast development

Flutter's hot reload helps you quickly and easily experiment, build UIs, add features, and fix bugs faster. Experience sub-second reload times without losing state on emulators, simulators, and hardware.
<img src="/assets/images/flutter/4.jpg">
<img src="/assets/images/flutter/5.jpg">

### Expressive, beautiful UIs
Delight your users with Flutter's built-in beautiful Material Design and Cupertino (iOS-flavor) widgets, rich motion APIs, smooth natural scrolling, and platform awareness.

### Native Performance
Flutter’s widgets incorporate all critical platform differences such as scrolling, navigation, icons and fonts to provide full native performance on both iOS and Android.

<center>
It’s free and open source.
<h2> Get started </h2>
</center>

### Step 1: Installing Flutter

Link: [https://flutter.dev/docs/get-started/install/windows](https://flutter.dev/docs/get-started/install/windows)

- the first step is to go the link provided above and to download the flutter SDK you can do that in two ways. you need to have git installed for flutter to work.
- Download the file and place it in your C:/ directory in a newly created folder titled src, extract the file. The path would look something like this C:/src/flutter and extract it there
- If you have git installed you can just clone the repository in the newly created folder src in your C:/ directory.

```python
    git clone https://github.com/flutter/flutter.git -b stable
```
### Step 2: Updating your Path

Now, why we need to do this step is that we can use the flutter SDK in our windows command prompt or terminal from any directory we want in our OS. go to the Start search bar and enter the keyword env, which would give you the settings for “Edit your System Environment variable”, select it to have a new window opened as shown below and click on the Environment variables to add a new path for flutter

<img src="/assets/images/flutter/3.png">

- After you’ve clicked on the button, you would see a new window, click on the path and click on the edit button just like I’ve done in the image below and that would open another window( I know too much work but the rest is really easy)

<img src="/assets/images/flutter/4.png">

well, in the new window just click on the new button and add the path to the bin folder in the new flutter SDK in your C: drive. Your path would be C:/src/flutter/bin. After you’ve done all the hard part, Close all of these windows by clicking on the ok button
<img src="/assets/images/flutter/5.png">

### Step 3: Run Flutter Doctor

now we need to run the command “flutter doctor” in our command prompt. and let flutter do its thing. but just to be on the safe side I’m going in the flutter directory (C:/src/flutter) to run this command and it might take some time.

<img src="/assets/images/flutter/6.png">

Well, I have some good news and some bad news, the good news is that we’re done with this step and the bad news is that we have some more steps to follow. like in the image above we need to have an android studio, flutter extensions for vs code, and an emulator or a real android device to run our apps to run on.

### Step 4: Installing Android Studio.
okay, so we don’t need to write code on an android studio we can do it on Vs code with the help of a few extensions but it’s crucial for creating apps. just go the official website of an android studio and begin your installation and after you’re done with it. we’ll move on to the other steps. The link to their site is below [https://developer.android.com/studio](https://developer.android.com/studio)
after installing an android studio if you run the flutter doctor -v you would see some more things that needed to be taken care of in the image below.

<img src="/assets/images/flutter/7.png">

to resolve the android toolchain issue with android licenses not accepted. just run the command flutter doctor — android-licenses and keep on accepting the licenses.

### Step 5: Installing Flutter plugins for Android Studio

Now, if you look at the image above you could see that the terminal is telling us that the dart plugin and flutter plugin are not installed in the Android studio. So open your android studio. when you install the flutter plugin it will automatically install the dart plugin as well.
<img src="/assets/images/flutter/8.png">

### Step 6: Installing flutter packages in VS Code
well, just open your vs code editor and look for packages in the packages section in the left sidebar menu. you’ll need to have two packages installed. but if you install only the flutter package it will automatically install the dart package as well. just like in step 5.
<hr>
- Flutter
- Awesome Flutter Snippets
<img src="/assets/images/flutter/9.png">

### Step 7: Set up your Android Device
Now, if you run the command flutter doctor -v in your terminal you would see that the only thing left is that you don’t have a connected device to your machine. we could download an emulator from our android studio but it would mean more work for your PC or laptop. if you’re comfortable with it then yeah go for it. But we’ll be attaching a real device to your machine. Download an opensource project on Github called scrcpy by Genymobile to mirror your mobile on laptop. you can follow the instructions there to mirror your android device and here is the link [https://github.com/Genymobile/scrcpy](https://github.com/Genymobile/scrcpy)

### Step 8: Running your first flutter Application

if everything has gone according to the plan than we would see green ticks on all of the mentioned tasks. if you run the flutter doctor -v command in the terminal. no issues found.

<img src="/assets/images/flutter/10.png">

Let’s create our very first flutter application. you can go to any folder you like to open up the terminal and run the following command flutter create firstApp to make your first flutter application and now to run your application go to the newly created directory by cd firstApp to run the project by the command flutter run. and it might take some time to run your first project
<img src="/assets/images/flutter/11.png">

<img src="/assets/images/flutter/12.png">

Thanks for reading through the article and if you find any issues or errors while installing the application. do let me know in the comments and I’ll try to help you as well.

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
  
