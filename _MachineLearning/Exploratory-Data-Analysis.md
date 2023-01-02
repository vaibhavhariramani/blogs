---
title: "What is Exploratory Data Analysis?"
excerpt: "Exploratory Data Analysis refers to the critical process of performing initial investigations on data."
header:
  image: /assets/images/machine_learning/1-1.jpg
  teaser: assets/images/machine_learning/1.jpg
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

<img alt="Image for post" class="xm xn t u v em aj c"  src="https://miro.medium.com/max/908/1*mW9PgNjfWxHhbve3O2siNA.png" srcset="https://miro.medium.com/max/276/1*mW9PgNjfWxHhbve3O2siNA.png 276w, https://miro.medium.com/max/376/1*mW9PgNjfWxHhbve3O2siNA.png 376w" sizes="450px">  <img alt="Image for post" class="xm xn t u v em aj c"  src="https://miro.medium.com/max/908/1*yY5jSLXWZ4d2H6tVtKRSGg.png" srcset="https://miro.medium.com/max/276/1*yY5jSLXWZ4d2H6tVtKRSGg.png 276w, https://miro.medium.com/max/376/1*yY5jSLXWZ4d2H6tVtKRSGg.png 376w" sizes="450px">

# What is Exploratory Data Analysis?

Exploratory Data Analysis refers to the critical process of performing initial investigations on data so as to discover patterns,to spot anomalies,to test hypothesis and to check assumptions with the help of summary statistics and graphical representations.

## Introduction

Exploratory data analysis is one of the best practices used in data science today. While starting a career in Data Science, people generally don’t know the difference between Data analysis and exploratory data analysis. There is not a very big difference between the two, but both have different purposes.

Exploratory Data Analysis(EDA): Exploratory data analysis is a complement to inferential statistics, which tends to be fairly rigid with rules and formulas. At an advanced level, EDA involves looking at and describing the data set from different angles and then summarizing it.

Data Analysis: Data Analysis is the statistics and probability to figure out trends in the data set. It is used to show historical data by using some analytics tools. It helps in drilling down the information, to transform metrics, facts, and figures into initiatives for improvement.

## Exploratory Data Analysis(EDA)

We will explore a Data set and perform the exploratory data analysis. The major topics to be covered are below:

- Handle Missing value
- Removing duplicates
- Outlier Treatment
- Normalizing and Scaling( Numerical Variables)
- Encoding Categorical variables( Dummy Variables)
- Bivariate Analysis

### Importing Libraries


Python code block:
```python
    import pandas as pd
    import numpy as np
    import matplotlib.pyplot as plt
    import seaborn as sns
    %matplotlib inline
```
### Loading the data set
We will be loading the EDA cars excel file using pandas. For this, we will be using read_excel file.

<img loading="lazy" class="ed ps s t u dp ai c" src="https://miro.medium.com/max/742/1*R2am-Y3sPq5zr0UqV_jn8w.png" alt="Box-plot after removing outliers" width="742" height="252">

# Basic Data Exploration
In this step, we will perform the below operations to check what the data set comprises of. We will check the below things:

- head of the dataset
- the shape of the dataset
- info of the dataset
- summary of the dataset

1. The head function will tell you the top records in the data set. By default, python shows you only the top 5 records.

2. The shape attribute tells us a number of observations and variables we have in the data set. It is used to check the dimension of data. The cars data set has 303 observations and 13 variables in the data set.

<img loading="lazy" class="ed ps s t u dp ai c alignnone" src="https://miro.medium.com/max/730/1*szh0jvlDjUY4GIw6eQPMNw.png" alt="Exploratory Data Analysis - Data Shape" width="730" height="54">

3. info() is used to check the Information about the data and the datatypes of each respective attribute.

<img loading="lazy" class="ed ps s t u dp ai c alignnone" src="https://miro.medium.com/max/610/1*9ro75aHgiFHChuUcMDR-gQ.png" sizes="610px" srcset="https://miro.medium.com/max/276/1*9ro75aHgiFHChuUcMDR-gQ.png 276w, https://miro.medium.com/max/552/1*9ro75aHgiFHChuUcMDR-gQ.png 552w, https://miro.medium.com/max/610/1*9ro75aHgiFHChuUcMDR-gQ.png 610w" alt="Exploratory Data Analysis - Data Information" width="610" height="280">

Looking at the data in the head function and in info, we know that the variable Income and travel time are of float data type instead of the object. So we will convert it into the float. Also, there are some invalid values like @@ and ‘*’ in the data which we will be treating as missing values.

<img loading="lazy" class="ed ps s t u dp ai c alignnone" src="https://miro.medium.com/max/778/1*WenFumTUD7-UuWuMoYfGAg.png" alt="Exploratory Data Analysis - Data Type" width="778" height="480">

## Handling missing value

<img loading="lazy" class="ed ps s t u dp ai c alignnone" src="https://miro.medium.com/max/694/1*TrqxavMLxmX6woTiyYRFuA.png" sizes="694px" srcset="https://miro.medium.com/max/276/1*TrqxavMLxmX6woTiyYRFuA.png 276w, https://miro.medium.com/max/552/1*TrqxavMLxmX6woTiyYRFuA.png 552w, https://miro.medium.com/max/640/1*TrqxavMLxmX6woTiyYRFuA.png 640w, https://miro.medium.com/max/694/1*TrqxavMLxmX6woTiyYRFuA.png 694w" alt="Exploratory Data Analysis - Sum" width="694" height="240">

We can see that we have various missing values in the respective columns. There are various ways of treating your missing values in the data set. And which technique to use when is actually dependent on the type of data you are dealing with.

- Drop the missing values: In this case, we drop the missing values from those variables. In case there are very few missing values you can drop those values.
- Impute with mean value: For the numerical column, you can replace the missing values with mean values. Before replacing with mean value, it is advisable to check that the variable shouldn’t have extreme values .i.e. outliers.
- Impute with median value: For the numerical column, you can also replace the missing values with median values. In case you have extreme values such as outliers it is advisable to use the median approach.
- Impute with mode value: For the categorical column, you can replace the missing values with mode values i.e the frequent ones.
In this exercise, we will replace the numerical columns with median values and for categorical columns, we will drop the missing values.

<img loading="lazy" class="ed ps s t u dp ai c alignnone" src="https://miro.medium.com/max/793/1*fl-T0Jz44Ut4pue_lcvSMg.png" alt="Image for postExploratory Data Analysis - Impute Missing values" width="793" height="174">
<img loading="lazy" class="ed ps s t u dp ai c alignnone" src="https://miro.medium.com/max/703/1*Fywb0gMNHCAnrZ88SI1ZFA.png" alt="Exploratory Data Analysis - Impute Missing Values" width="703" height="238">
<img loading="lazy" class="ed ps s t u dp ai c" src="https://miro.medium.com/max/717/1*adetm64ZyfCgCs9Vr62qow.png" sizes="700px" srcset="https://miro.medium.com/max/276/1*adetm64ZyfCgCs9Vr62qow.png 276w, https://miro.medium.com/max/552/1*adetm64ZyfCgCs9Vr62qow.png 552w, https://miro.medium.com/max/640/1*adetm64ZyfCgCs9Vr62qow.png 640w, https://miro.medium.com/max/700/1*adetm64ZyfCgCs9Vr62qow.png 700w" alt="Image for post" width="717" height="235">

## Handling Duplicate records

<img loading="lazy" class="ed ps s t u dp ai c" src="https://miro.medium.com/max/802/1*bEVBr5aefKcPvNYVmN7OAQ.png" sizes="700px" srcset="https://miro.medium.com/max/276/1*bEVBr5aefKcPvNYVmN7OAQ.png 276w, https://miro.medium.com/max/552/1*bEVBr5aefKcPvNYVmN7OAQ.png 552w, https://miro.medium.com/max/640/1*bEVBr5aefKcPvNYVmN7OAQ.png 640w, https://miro.medium.com/max/700/1*bEVBr5aefKcPvNYVmN7OAQ.png 700w" alt="Image for post" width="802" height="554">

Since we have 14 duplicate records in the data, we will remove this from the data set so that we get only distinct records. Post removing the duplicate, we will check whether the duplicates have been removed from the data set or not.

<img loading="lazy" class="ed ps s t u dp ai c" src="https://miro.medium.com/max/721/1*Ssw-F5X7Dv5Vs4PTGEtHRg.png" sizes="700px" srcset="https://miro.medium.com/max/276/1*Ssw-F5X7Dv5Vs4PTGEtHRg.png 276w, https://miro.medium.com/max/552/1*Ssw-F5X7Dv5Vs4PTGEtHRg.png 552w, https://miro.medium.com/max/640/1*Ssw-F5X7Dv5Vs4PTGEtHRg.png 640w, https://miro.medium.com/max/700/1*Ssw-F5X7Dv5Vs4PTGEtHRg.png 700w" alt="Image for post" width="721" height="43">

<img loading="lazy" class="ed ps s t u dp ai c" src="https://miro.medium.com/max/734/1*SC8rIGiXtehv2K-k3_dCxg.png" sizes="700px" srcset="https://miro.medium.com/max/276/1*SC8rIGiXtehv2K-k3_dCxg.png 276w, https://miro.medium.com/max/552/1*SC8rIGiXtehv2K-k3_dCxg.png 552w, https://miro.medium.com/max/640/1*SC8rIGiXtehv2K-k3_dCxg.png 640w, https://miro.medium.com/max/700/1*SC8rIGiXtehv2K-k3_dCxg.png 700w" alt="Image for post" width="734" height="87">

## Handling Outlier

Outliers, being the most extreme observations, may include the sample maximum or sample minimum, or both, depending on whether they are extremely high or low. However, the sample maximum and minimum are not always outliers because they may not be unusually far from other observations.

We Generally identify outliers with the help of boxplot, so here box plot shows some of the data points outside the range of the data.

<img loading="lazy" class="ed ps s t u dp ai c" src="https://miro.medium.com/max/702/1*3DAHUOTEOmzYrLEl2E_GbA.png" alt="Image for post" width="702" height="271">

Looking at the box plot, it seems that the variables INCOME, have outlier present in the variables. These outliers value needs to be teated and there are several ways of treating them:

- Drop the outlier value
- Replace the outlier value using the IQR

<img loading="lazy" class="ed ps s t u dp ai c" src="https://miro.medium.com/max/790/1*21yw90gA0DFXlZdZs6f_cA.png" sizes="700px" srcset="https://miro.medium.com/max/276/1*21yw90gA0DFXlZdZs6f_cA.png 276w, https://miro.medium.com/max/552/1*21yw90gA0DFXlZdZs6f_cA.png 552w, https://miro.medium.com/max/640/1*21yw90gA0DFXlZdZs6f_cA.png 640w, https://miro.medium.com/max/700/1*21yw90gA0DFXlZdZs6f_cA.png 700w" alt="Image for post" width="790" height="199">

*#Boxplot After removing outlier*
<img loading="lazy" class="ed ps s t u dp ai c" src="https://miro.medium.com/max/742/1*R2am-Y3sPq5zr0UqV_jn8w.png" sizes="700px" srcset="https://miro.medium.com/max/276/1*R2am-Y3sPq5zr0UqV_jn8w.png 276w, https://miro.medium.com/max/552/1*R2am-Y3sPq5zr0UqV_jn8w.png 552w, https://miro.medium.com/max/640/1*R2am-Y3sPq5zr0UqV_jn8w.png 640w, https://miro.medium.com/max/700/1*R2am-Y3sPq5zr0UqV_jn8w.png 700w" alt="Box-plot after removing outliers" width="742" height="252">

## Bivariate Analysis
When we talk about bivariate analysis, it means analyzing 2 variables. Since we know there are numerical and categorical variables, there is a way of analyzing these variables as shown below:

### Numerical vs. Numerical
1. Scatterplot
2. Line plot
3. Heatmap for correlation
4. Joint plot

### Categorical vs. Numerical
1. Bar chart
2. Violin plot
3. Categorical box plot
4.Swarm plot

### Two Categorical Variables
1. Bar chart
2. Grouped bar chart
3. Point plot

If we need to find the correlation-
<img loading="lazy" class="ed ps s t u dp ai c" src="https://miro.medium.com/max/729/1*aQFnkEA591TrjFPOM6SC_g.png" alt="Image for post" width="729" height="186">

## Normalizing and Scaling
Often the variables of the data set are of different scales i.e. one variable is in millions and others in only 100. For e.g. in our data set Income is having values in thousands and age in just two digits. Since the data in these variables are of different scales, it is tough to compare these variables.

Feature scaling (also known as data normalization) is the method used to standardize the range of features of data. Since the range of values of data may vary widely, it becomes a necessary step in data preprocessing while using machine learning algorithms.

In this method, we convert variables with different scales of measurements into a single scale. StandardScaler normalizes the data using the formula (x-mean)/standard deviation. We will be doing this only for the numerical variables.

<img loading="lazy" class="ed ps s t u dp ai c" src="https://miro.medium.com/max/789/1*l_MD5ImWOIIpm7eU3FEoPg.png" sizes="700px" srcset="https://miro.medium.com/max/276/1*l_MD5ImWOIIpm7eU3FEoPg.png 276w, https://miro.medium.com/max/552/1*l_MD5ImWOIIpm7eU3FEoPg.png 552w, https://miro.medium.com/max/640/1*l_MD5ImWOIIpm7eU3FEoPg.png 640w, https://miro.medium.com/max/700/1*l_MD5ImWOIIpm7eU3FEoPg.png 700w" alt="Image for post" width="789" height="205">
<img loading="lazy" class="ed ps s t u dp ai c" src="https://miro.medium.com/max/802/1*1bIYZV2s6rZpeMep6jcmOg.png" sizes="700px" srcset="https://miro.medium.com/max/276/1*1bIYZV2s6rZpeMep6jcmOg.png 276w, https://miro.medium.com/max/552/1*1bIYZV2s6rZpeMep6jcmOg.png 552w, https://miro.medium.com/max/640/1*1bIYZV2s6rZpeMep6jcmOg.png 640w, https://miro.medium.com/max/700/1*1bIYZV2s6rZpeMep6jcmOg.png 700w" alt="Image for post" width="802" height="208">

## ENCODING
One-Hot-Encoding is used to create dummy variables to replace the categories in a categorical variable into features of each category and represent it using 1 or 0 based on the presence or absence of the categorical value in the record.

This is required to do since the machine learning algorithms only work on the numerical data. That is why there is a need to convert the categorical column into a numerical one.

get_dummies is the method that creates a dummy variable for each categorical variable.

<img loading="lazy" class="ed ps s t u dp ai c" src="https://miro.medium.com/max/793/1*7pezD8fTPk_t7kZSNJOzyA.png" sizes="700px" srcset="https://miro.medium.com/max/276/1*7pezD8fTPk_t7kZSNJOzyA.png 276w, https://miro.medium.com/max/552/1*7pezD8fTPk_t7kZSNJOzyA.png 552w, https://miro.medium.com/max/640/1*7pezD8fTPk_t7kZSNJOzyA.png 640w, https://miro.medium.com/max/700/1*7pezD8fTPk_t7kZSNJOzyA.png 700w" alt="Image for post" width="793" height="53">

<img loading="lazy" class="ed ps s t u dp ai c" src="https://miro.medium.com/max/799/1*RN-cYRzYnHh6gqhxs48Njg.png" sizes="700px" srcset="https://miro.medium.com/max/276/1*RN-cYRzYnHh6gqhxs48Njg.png 276w, https://miro.medium.com/max/552/1*RN-cYRzYnHh6gqhxs48Njg.png 552w, https://miro.medium.com/max/640/1*RN-cYRzYnHh6gqhxs48Njg.png 640w, https://miro.medium.com/max/700/1*RN-cYRzYnHh6gqhxs48Njg.png 700w" alt="Image for post" width="799" height="182">

<img loading="lazy" class="ed ps s t u dp ai c" src="https://miro.medium.com/max/799/1*LzL75bYXK6k-jJx4LTBZWw.png" sizes="700px" srcset="https://miro.medium.com/max/276/1*LzL75bYXK6k-jJx4LTBZWw.png 276w, https://miro.medium.com/max/552/1*LzL75bYXK6k-jJx4LTBZWw.png 552w, https://miro.medium.com/max/640/1*LzL75bYXK6k-jJx4LTBZWw.png 640w, https://miro.medium.com/max/700/1*LzL75bYXK6k-jJx4LTBZWw.png 700w" alt="Image for post" width="799" height="260">

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
  
