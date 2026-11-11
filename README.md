# Heart-disease

## Introduction

In this analysis, we will look at a data set from Kaggle which is a combination of 5 data sets from Cleveland, Hungarian, Switzerland, Long Beach VA, and Statlog (Heart) Data Set. The link to this dataset can be found in the reference section. Its variables are age (in years), the sex of the patient (categorical), chest pain type (categorical), resting systolic blood pressure (mmHg), serum cholesterol (mg/dl), fasting blood sugar (categorical), resting electrocardiogram results (categorical), maximum heart rate achieved (bpm), exercise induced angina, old peak ST (depression), the scope of the peak exercise ST segment (categorical), and target (categorical variable of whether the patient has heart disease or not). Altogether, there are 1190 observations. 

The following questions are of interest. How does the patient's cholesterol affect whether they have heart disease? What are the interactions between heart disease and age? How is the patient's sex and whether they have heart disease related? What else affects whether the patient has heart disease? How does fasting blood sugar affect heart disease? 

## Data preprocessing

As this analysis uses Bayesian Networks, it is much easier to move forward with this analysis if all the variables are discrete. This is due to the way the probabilities are defined. It also means we can use the data to learn the structure of the network (which variables are conditionally related to each) which is treated as unknown in this analysis. A large part of the data pre-processing then was categorizing our continuous variables. 

### Categorical labelled variables

The sex of the patients is given the labels “Female” and “Male”. Chest pain type has labels “Typical angina”, “Atypical angina”, “Non-anginal”, and “Asymptomatic”. Fasting blood sugar labels are “Greater than 120”, and “Less than 120”. Resting ecg “Normal”, “Abnormality”, “Left Ventricular hypertrophy”. Exercise angina “Yes” or “No”. ST slope “Upsloping”, “Flat”, and “Downsloping”. Target labels “Normal” or “Heart disease”. Each of these were categorized as numbers in the original data set. I just put each category into words. 

### Continuous and discrete variables

To have a manageable number of categories for age whilst keeping as much variability as possible, we split by decades. “20s” to “70s”. For blood pressure we used the information from (Heart Research Institute, 2024), to categorize this variable as follows. Blood pressure between 90 and 120 is “Optimal”, 120 to 129 is “Normal”, 129 to 139 is “High”, and greater than 139 is “Very high”. Before categorizing cholesterol, we converted the measurements from mg/dl to mmol/L, for convenience, by multiplying by 0.02586. Using information available from (NHS, 2022) to split it to be “Normal” if less than or equal to 5 and “High” otherwise. 

When dealing with maximum heart rate, we used information from (American Heart Association, 2024). Here we found that for vigorous activity, your maximum heart rate should be between 70% and 80% of 220 - age. We then calculated 220 – age for each patient. The maximum heart rate observed is then “Low” if less than 70% of 200 – age, “High” if above 85% and “Normal” otherwise. For the variable old peak, we used information from (Kashou, Basit and Malik, 2023). We found that if an individual is in their 20s or 30s their old peak depression should be 0.3 or below. If older, it should be 0.25 or below. From there we categorized this variable as “High” and “Normal” this way. 

### Spread of categories

The age category has very few patients who are in their 20s and 70s with 6 and 26 observations respectively. There are more people in their 30s (90 observations), 40s (273), 60s, (206) with the most by far being in their 50s (417).  

<img width="863" height="542" alt="Screenshot From 2026-11-11 17-12-53" src="https://github.com/user-attachments/assets/8b122b4c-f585-47a1-b3bb-f736a5226fc4" />

There are also a lot more males (748) than females in this data set (270). Chest pain type has an even spread of observations with atypical angina (209) and non-angina pain (249). Much less observations have a category of typical angina whilst many more have asymptomatic chest pain type (499). Resting blood pressure has an even spread of patients in the optimal (317) and very high (363) categories with fewer in the normal (96) and high (242) categories. As for cholesterol, many more patients had high cholesterol (876) than normal cholesterol (142). Likewise, many more patients had a fasting blood sugar of less than 120 (853) than greater than it (165). Most patients had a normal resting ecg (577) whilst fewer had an abnormality (128) and left ventricular hypertrophy (313). As for maximum heart rate, most had a high one (549) whilst slightly less had a normal one (324) and much less had a low one (145). Many more patients had an exercise angina (641) than not (377). The balance between the patient's old peaks is pretty much even as is the target categories with slightly more patients not having heart disease (541 compared to 476). Finally, many more patients had an upsloping (480) and flat (476) ST slope with few having a downsloping one (61).

### A note on outliers

There were a total of 171 patients having a total cholesterol of 0 with one having a resting systolic blood pressure of 0. These values are impossible, so, with very little information to go on, we will just remove these variables. Looking at the boxplots of cholesterol and systolic blood pressure, there are a few very unusually high values which could have an impact on our analysis (the highest observed was 15.59358). We will then treat all resting systolic blood pressures greater than 170 and cholesterols greater than 9.73629 as potential outliers. These ranges where decided by the range boxplots used to determine potential outliers $Lower quartile + 1.5 \times Interquartile range$

. We will then fit a Bayesian network with the full data set and another one without the potential outliers and compare. 
