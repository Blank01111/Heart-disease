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



### Notes on outliers

171 observations of the patients had 0 cholesterol and 1 had a resting systolic blood pressure of 0. These values are impossible and so, with much less information to go on, we removed these observations. Several of the observations had very large values for their cholesterol and blood pressure. Treating observations with blood pressure greater than 170 and cholesterol greater than 9.73629 were considered possible outliers. We also consider systolic blood pressure less than 90 an outlier which is especially small for this data set. We will fit and interpret 2 Bayesian networks, one with all the observations and another without possible outliers. Here, 61 observations are treated as possible outliers whilst 956 are not. 
