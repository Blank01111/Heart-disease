# Heart-disease

## Introduction

In this analysis, we will look at a data set from Kaggle which is a combination of 5 data sets from Cleveland, Hungarian, Switzerland, Long Beach VA, and Statlog (Heart) Data Set. The link to this dataset can be found in the reference section. Its variables are age (in years), the sex of the patient (categorical), chest pain type (categorical), resting systolic blood pressure (mmHg), serum cholesterol (mg/dl), fasting blood sugar (categorical), resting electrocardiogram results (categorical), maximum heart rate achieved (bpm), exercise induced angina, old peak ST (depression), the scope of the peak exercise ST segment (categorical), and target (categorical variable of whether the patient has heart disease or not). Altogether, there are 1190 observations. 

The following questions are of interest. How does the patient's cholesterol affect whether they have heart disease? What are the interactions between heart disease and age? How is the patient's sex and whether they have heart disease related? What else affects whether the patient has heart disease? How does fasting blood sugar affect heart disease? 

## Data preprocessing

### Categorical labelled variables

The sex of the patients is given the labels “Female” and “Male”. Chest pain type has labels “Typical angina”, “Atypical angina”, “Non-anginal”, and “Asymptomatic”. Fasting blood sugar labels are “Greater than 120”, and “Less than 120”. Resting ecg “Normal”, “Abnormality”, “Left Ventricular hypertrophy”. Exercise angina “Yes” or “No”. ST slope “Upsloping”, “Flat”, and “Downsloping”. Target labels “Normal” or “Heart disease”. Each of these were categorized as numbers in the original data set. I just put each category into words. 

### Continuous and discrete variables

To have a manageable number of categories for age whilst keeping as much variability as possible, we split by decades. “20s” to “70s”. For blood pressure we used the information from (Heart Research Institute, 2024), to categorize this variable as follows. Blood pressure between 90 and 120 is “Optimal”, 120 to 129 is “Normal”, 129 to 139 is “High”, and greater than 139 is “Very high”. Before categorizing cholesterol, we converted the measurements from mg/dl to mmol/L, for convenience, by multiplying by 0.02586. Using information available from (NHS, 2022) to split it to be “Normal” if less than or equal to 5 and “High” otherwise. 

