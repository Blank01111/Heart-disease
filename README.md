# Heart-disease

## Introduction

In this analysis, we will look at a data set from Kaggle which is a combination of 5 data sets from Cleveland, Hungarian, Switzerland, Long Beach VA, and Statlog (Heart) Data Set. The link to this dataset can be found in the reference section. Its variables are age (in years), the sex of the patient (categorical), chest pain type (categorical), resting systolic blood pressure (mmHg), serum cholesterol (mg/dl), fasting blood sugar (categorical), resting electrocardiogram results (categorical), maximum heart rate achieved (bpm), exercise induced angina, old peak ST (depression), the scope of the peak exercise ST segment (categorical), and target (categorical variable of whether the patient has heart disease or not). Altogether, there are 1190 observations. 

The following questions are of interest. Which variables could possibly affect other variables? How does the patient's cholesterol affect whether they have heart disease? What are the interactions between heart disease and age? How is the patient's sex and whether they have heart disease related? What else affects heart disease?

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

There were a total of 171 patients having a total cholesterol of 0 with one having a resting systolic blood pressure of 0. These values are impossible, so, with very little information to go on, we will just remove these variables. Looking at the boxplots of cholesterol and systolic blood pressure, there are a few very unusually high values which could have an impact on our analysis (the highest observed was 15.59358). We will then treat all resting systolic blood pressures greater than 170 and cholesterols greater than 9.73629 as potential outliers. These ranges where decided by the range boxplots used to determine potential outliers $Lower quartile+1.5*Interquartile range$. We will then fit a Bayesian network with the full data set and another one without the potential outliers and compare. 

<img width="863" height="542" alt="Screenshot From 2026-11-11 17-56-24" src="https://github.com/user-attachments/assets/733f7c3f-1ca0-4244-97e5-e8eec77a0509" />

## Methods

### The modelling technique

The model we will be using is a Bayesian network which displays conditional dependencies for the variables. If an arrow points from cholesterol to age say we say that given the patients age, we can predict a probability for their cholesterol. Here we say age is the parent node of cholesterol. If one variable (or node) is not dependent on another, we say they are independent. Note that even if one node doesn’t have an edge directly to it, they are not automatically independent. One nodes parent could be dependent on the other node for example. There are several reasons why we will use a Bayesian network. First, it allows us to see relationships between the variables. We can also understand their relationships. Thirdly, we can make predictions. 

### Score functions and algorithms

We will treat the structure and resulting conditional probabilities as unknown in this model. To discover the structure of the network, we will use an algorithm called hill climbing (HC). We start with a network structure, then for each iteration, we randomly make a new connection or remove one scoring the new network by a chosen score function asking, “is this better or worse?”. This algorithm also checks if the connections form any closed loops which are not allowed in Bayesian networks.  

The score functions we will use are the AIC and BIC ones. Both measure how closely they match what we observe in the data but also penalize more complicated models. BIC penalizes more heavily. Both will be used to avoid overfitting whilst trying to be as “accurate” as possible. Both are used as AIC may not penalize heavily enough, which is where we will look at the model from the BIC. 

There are some limitations to this approach. HC is a greedy algorithm meaning it will not always choose the “best” structure possible. To combat this, we will use 10-fold cross validation (splitting the data 10 times fitting 90% to fit the model the rest used to test it) repeated 5 times. We will use the average log-likelihood to determine the fit. This will help determine just how well the models truly are. Log likelihood is used due to its easy interpretation and as a simple way to judge the models. 

When predicting the probabilities, we will use a Bayesian approach. We will assume that each probability of the interactions has equal probabilities, letting the data adjust to this assumption. Using the R function bayes, we set iss = 2. The higher the value, the more the prior assumption has an impact. Choosing such a low value is due to having a reasonably large sample size whilst letting the prior have some effect.  

## Results

### Full dataset

The AIC model had an average log-likelihood of 9.270508, making it marginally better than the BIC model with 9.270383. The AIC model is much more complicated than the BIC network, meaning interpretation would be much more difficult. We will then use the BIC network. 

<img width="788" height="553" alt="Screenshot From 2026-11-12 09-14-05" src="https://github.com/user-attachments/assets/c1125d9a-13c4-4170-af1b-8cc01b558b7d" />

 Above we can see, under this model, cholesterol is independent of all the other variables. The fasting blood sugar, and the old peak of the patient seem to determine the probabilities of the patient’s age. ST slope and the exercise angina categories seem to affect the patient's old peak. The ST slope of the patient seems to be directly related to whether the patient has heart disease or not. Whether the patient has heart disease directly influences the patients' age, has exercise angina, and the patient's chest pain type. Fasting blood sugar levels seem to be directly related to the patients resting systolic blood pressure. Whether the patient has exercise angina directly influences the max heart rate and chest pain type of the patient. Finally, the resting ecg of the patient seems to be conditionally related to their maximum heart rate. 

 From this model the probability the patient has heart disease is 0.468106. Conditioning on the age, this probability changes a lot.

<img width="902" height="553" alt="Screenshot From 2026-11-12 09-36-43" src="https://github.com/user-attachments/assets/40c7921a-fc9e-48a1-b040-3a280b3509e3" />

 It seems here, under this model, patients in their 60s are most likely to have heart disease rising a bit higher than their baseline moving up to 0.533492. Patients in their 20s are much less likely to have heart disease with a probability of 0.2929629.

The probability of a patient in this data set being female or male was 0.265947 and 0.734053 respectively. What happens when we include whether they have heart disease or not in the equation?

<img width="902" height="553" alt="Screenshot From 2026-11-12 09-55-13" src="https://github.com/user-attachments/assets/1f744ed8-c070-4f9c-9b14-b9e5c783da90" />

