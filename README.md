# Banco de Portugal classification about companies being late in reporting tasks
<div align="justify">This project was done on the context of a classe in my master's where a private Kaggle competition was created by the professor. The goal of the project was to use an anonymized dataset of
information about several companies and if they submitted the reports late or not.</div></br>

## Table of Contents
1. [Problem Description](#problem-description)
2. [Preprocessing Methods](#preprocessing-methods)
3. [Predictive Models](#predictive-models)
4. [Accuracy Measures](#accuracy-measures)
5. [Explainability](#Explainability)

## Problem Descriptiomn
<div align="justify">Banco de Portugal has been observing that some companies often lag behind when submitting their information reports. Thus, the purpose of this project is to find whether it is possible to classify 
the companies as late in the reporting tasks or not, based on financial and economical aspects concerning those companies, and also to acknowledge the attributes that contribute to this situation.</div></br>

For this, two datasets were made available: a train set and a test set. Both comprising 55 variables:

- an identifying variable, i;
- forty continuous variables, x1 to x40;
- twelve nominal categorical variables, c1 to c12;
- two ordinal categorical variables, o1 and o2.

<div align="justify">The train set also contains information about the target variable, y, which is binary (taking the value 0 if the company is on time in the submission of the report or 1 if the company
is late in the submission). Whilst the train set has 5832 observations, the test set includes 5843 observations.</div></br>

## Preprocessing Methods
<div align="justify">First, it was found that the target variable is quite unbalanced, having approximately 64% of its values equal to 0 and 36% equal to 1. This means that most companies deliver their
reports on time, making it harder to predict when they are going to submit them late.</div></br>

<div align="justify">The analysis of the variables was done first by studying the missing values of the data sets. In the train set, 22 of the 55 variables mentioned above present missing values. It is
important to note that the variables that present the most missing values are x13 to x18 (more than 1000 missing values in each of these variables), being these variables responsible for
approximately 84% of the total of missing values in the train set. The same happens in the test set.</div></br>

<div align="justify">The first preprocessing method tried was to remove the variables x13 to x18, but it didn’t resulted in a better score in the Kaggle platform, when compared to using no preprocessing
at all.</div></br>

<div align="justify">The second method applied was the MICE (Multivariate Imputation by Chained Equations) package of R. It was used in both the train and test sets. MICE imputes missing
values with plausible data values and each variable has its own imputation model. The random forest method was chosen to draw these plausible values. Even though it was expected that
this method would improve the results in the Kaggle platform, it didn’t.</div></br>

<div align="justify">After this, an assessment of the correlation among the continuous variables and the target variable within the train set was made, as seen on the figure below. Several pairs of highly
correlated variables were found, which suggested great redundancy among the train dataset and the need to proceed to feature selection.</div></br>

![image](https://github.com/user-attachments/assets/20c6616e-dd93-40cc-ab04-37a7a35a30e1)

<div align="justify">The first approach to feature selection was made using the DALEX package in R, to find the most important features in one of the initial models that were developed. The function
model_part disclosed the root mean square error loss after permutations within each variable.</div></br>

<div align="justify">The variables c12, c7, x35, x6, c4, x22 emerged from the plot with the greatest RMSE loss, and were selected to rerun the same predictive model, removing all the remaining. However, this
preprocessing step didn’t lead to better results in the Kaggle leaderboard and it was discarded.</div></br>

<div align="justify">Taking into consideration the correlations between the numerical variables, another preprocessing method was tried. From the pairs of variables that presented a correlation of
0,7 or more, one of the variables was removed.</div></br>

<div align="justify">The obtained training dataset consisted in the following variables: the ordinal and categorical variables, x3, x7, x11, x13, x15, x17, x21, x25, x26, x29, x33, x34, x36, x37, x39 and x40.</div></br>
After the conducted experiments it was possible to see that this method did not improve the Kaggle score.

<div align="justify">Next, i tried to do the feature selection on knime to find the set of features that could give us the best accuracy for the predictions.</div></br>

![image](https://github.com/user-attachments/assets/ae152059-4a60-4bdc-b603-2283b1e590a3)

<div align="justify">I ran the feature selection loop on a Gradient Boosted Trees model, using the Genetic Algorithm as the feature selection strategy. After the tuning of the parameters of the
genetic algorithm that allowed us to get the best accuracy i set the advanced settings as it is below.</div></br>

![image](https://github.com/user-attachments/assets/5943bb0d-68db-4f64-ad0e-5343e5b40594)

<div align="justify">After running this loop and analyzing the scorer node, i got an accuracy of 0.692, the confusion matrix and the other metrics for the model used are the ones below.</div></br>

![image](https://github.com/user-attachments/assets/611d8349-2d59-4632-b796-b2aef1675a22)

<div align="justify">Analyzing the results given by the feature selection loop, i got 61 different results with the number of features going from 18 on the smallest set to 35 on the larger. The accuracy went
from 0.653 to 0.709 on the most accurate set of features and was the one with the best accuracy that was chosen to proceed with the predictions for the test dataset.</div></br>

<div align="justify">With these results, i could conclude that making the predictions with feature selection was the way to go to get the best score possible.</div></br>

<div align="justify">The best set of features is the one below with 26 features, 20 from the continuous variables, 6 from the categorical variables and none from the ordinal variables.</div></br>

![image](https://github.com/user-attachments/assets/5ae01e34-d744-455b-a078-84b4908ddf5e)

## Predictive Models
<div align="justify">The first predictive model tried was a decision tree from the Rpart package of R, with the default parameters and the method equal to “class”.</div></br>

<div align="justify">This model was first trained with the train set without preprocessing, resulting in a Kaggle score of 67,94%. This was the starting point of the work proposed.</div></br>

<div align="justify">This model was also trained with the train set without the variables x13 to x18 (the ones responsible for most of the missing values) and with the train set with the missing values
imputed by MICE. In both these experiences, the Kaggle score did not improve. It was concluded that the decision tree model from Rpart is not suited for this problem.</div></br>

<div align="justify">The second predictive model tried was a decision tree from the C5.0 package of R, with noGlobalPruning = TRUE (in order to simplify the tree) and minCases = 10 (minCases = 50 was also tried but it produced worser results in the predictions).</div></br>

<div align="justify">This model was trained with the train set without preprocessing, resulting in a Kaggle score of 68,356%. Those predictions were better than the ones produced by the Rpart package.</div></br>

<div align="justify">The model was also trained with the train set with the missing values imputed by MICE, but it resulted in worse predictions (even worser than the ones from Rpart).</div></br>

<div align="justify">The C5.0 model was also trained with only 6 variables (x6, x22, x35, c4, c7 and c12). These variables were the ones considered to be the most important by the DALEX library,
which was referred above in the preprocessing methods part. This resulted in worse predictions than the ones produced by the same model trained with the train set without any
preprocessing.</div></br>

<div align="justify">After these experiments, several models were trained using autoML in KNIME. It is important to note that the autoML node also automates the preprocessing of the data. Models
such as Decision Trees, Random Forests, Logistic Regression, Neural Networks, XGBoost Trees and Gradient Boosted Trees were considered.</div></br>

<div align="justify">During these experiments, the previously referred preprocessing methods, except the feature selection, were also tried. Despite this, it was possible to see that the model that produced the better results 
(considering the score presented in Kaggle) was the Gradient Boosted Trees model without any preprocessing.</div></br>

<div align="justify">The previous conclusion led to a more focused experimentation of the Gradient Boosted Trees model and to the application of a different preprocessing method - feature selection.</div></br>

<div align="justify">Making the feature selection on knime explained before, we proceeded for the prediction using only the set of features with the best accuracy. Filtering the test and train datasets we were able to learn the model on the filtered train
dataset and predict with the filtered test dataset and after submitting the results of the predictions, we got our best score on the competition of 0,69104.</div></br>

![image](https://github.com/user-attachments/assets/09fbf02c-b12b-4a5a-bf16-13eb83c7be32)

## Accuracy Measures
In this part of the report, the accuracy measures for the two best experimented models will be shown and analysed. 

<div align="justify">Considering the model 1 the Gradient Boosted Trees with feature selection and the model 2 the Gradient Boosted Trees model trained with autoML. These measures were computed considering a partitioning of the training data into 80/20.</div></br>

<div align="justify">Note that the positive class refers to the cases where y=1, when the company is late in the submission of the report. In the context of this problem, it is safe to assume that obtaining
false negatives is worse than obtaining false positives. When a company is predicted to be late in their submission and happens to deliver on time is a better situation then when a
company is unexpectedly late in their submission.</div></br>

<div align="justify">Taking this into consideration, along with the accuracy measure, the sensitivity will also be analyzed since it is assumed the model must be reliable when predicting that a company will not be late (what happens when this last
measure is high).</div></br>

|         | Accuracy | Sensitivity |
| ------- |:--------:| :----------:|
| Model 1 | 0,688    | 0,247       |
| Model 2 | 0,688    | 0,726       |

<div align="justify">Note that the two analyzed models present the same accuracy but when considering the sensitivity measure it is possible to see that the second model presents a considerably higher value.</div></br>

<div align="justify">Taking that into consideration and making the assumptions that were explained above, it is possible to say that in the context of the problem it would be more advantageous to use the second model 
to predict which companies are going to be late in their report submission.</div></br>

## Explainability
<div align="justify">In this part of the report, the explainability of the best model experiment (regarding the score presented in Kaggle’s public leaderboard) will be explored.</div></br>

<div align="justify">The Gradient Boosted Trees algorithm can be considered as a black-box model. It is also important to note that the Dalex library, previously referred to, constitutes a form of explainable machine learning, 
but in this specific problem it was primarily used as a form of feature selection.</div></br>

<div align="justify">In this section, different explainability techniques will be applied, such as, feature relevance (SHAP), explanations by simplification (LIME) and visual explanations (Partial Dependence Plots).</div></br>

<div align="justify">Firstly, considering the first observation of the test set. The model predicted that the class assigned to this observation is the positive class (y=1). With the use of the XAI view node in
knime it was possible to obtain a local explanation for this specific observation.</div></br>

![image](https://github.com/user-attachments/assets/eff9b287-5bd1-424f-bd1f-786c57e8056d)

<div align="justify">Analyzing the image above it is possible to see that the features that most contributed positively for the classification of this instance were x2, x16, x23, x32. Whereas feature c1
was the one that contributed negatively for the classification of the instance.</div></br>

<div align="justify">Considering now the first 100 observations of the test set (maximum number to be handled by the node used). Note that it was assumed that these 100 observations can represent the globality 
of the observations present in the test set.</div></br>

![image](https://github.com/user-attachments/assets/a63c8759-64b9-49c3-b56b-faf1388082ed)

<div align="justify">The image above presents the explanations feature violins. When the shap value is the same for different instances, the wider the violin will be for that SHAP value. Analyzing the
image above will provide a global explanation.</div></br>

<div align="justify">It is possible to see that when considering the most dense part of the violins (larger number of instances considered) the SHAP values are not very different from zero. Despite
this, note that in some features this wider part of the violin is clearly located either in the positive or negative part of the graphic, which indicates that the value of that feature
contributed respectively, positively or negatively for the classification of the test instance as the positive class (y=1).</div></br>

<div align="justify">It is important to note that with this graph it is also possible to see the appearance of some extreme SHAP values. In those specific instances the value of that had a very large
predictive power in the model, thus had a very large contribution in the classification of the test instance.</div></br>

© 2024 Victor Malheiro
