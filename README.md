# Optimizing an ML Pipeline in Azure

## Overview
This project is part of the Udacity Azure ML Nanodegree.
In this project, we build and optimize an Azure ML pipeline using the Python SDK and a provided Scikit-learn model.
This model is then compared to an Azure AutoML run.

## Summary
**As a part of this project, we have used Bank Marketing Data Set which contains information about campaigns. The goal of the solution is to predict if the client will subscribe to the services or not.**

**We will be using scikit-learn logistic regression and find the best output through HyperDrive. The best performing model has the accuracy of .**

## Scikit-learn Pipeline
The pipeline follows the normal ML Engineering steps: 

![image](https://user-images.githubusercontent.com/25560357/121797745-d98f8680-cc3f-11eb-9ad6-8fef9db5df18.png)


1. Data Preparation

In this step, the data is loaded using TabularDatasetFactory. 
Once loaded, the data is cleansed through the train.py scripts. The null values are dropped and one-hot encoding is performed. This also splits the data into train and test datasets using the SkLearn split function. 

This training data is fit into LogisticRegression model provided by Scikit-Learn. 

SRC : https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv

Few details : 
- Total Rows : 32951
- Total Columns : 20 Feature Columns & 1 Target Column
- Target Column : Y to predict if the users will subscribe to the services. 
- Potential Issues : Data Imbalance

2. Hyper Parameter Tuning: 
The hyper drive configuration is done to tune the input arguments - C and number of iterations. We have chosen randomly c [0.03,0.3,3,10,30] and max_iterations as [25,50,75,100].

3. The best model : 
The best model : 
 - ID :  HD_03d54f0f-57ed-46ba-8575-1f8a5eddff2a_9
 - Metrics :  {'Regularization Strength:': 0.3, 'Max iterations:': 75, 'Accuracy': 0.9152250885179565}

**What are the benefits of the parameter sampler you chose?**
I have chosen Random Parameter Sampler. Since this supports both discrete and continueous hyperparamters. This also avoids wastage by early terminating the low performing runs. 

**What are the benefits of the early stopping policy you chose?**
I have chosen Bandit Policy as it utilizes the slack factor and evaluation interval. In case the primary metric is not within specific slack factor, the policy terminates the execution. 

## AutoML
AutoML tried more models and the best model generated by AutoML is VotingEnsemble with an accuracy of 0.917. 

VotingEnsemble is an ensemble model that predicts based on the weighted average of predicted class probabilities (for classification tasks) or predicted regression targets (for regression tasks). More information can be found here : https://docs.microsoft.com/en-us/azure/machine-learning/concept-automated-ml#ensemble


The best model suggested by AutoML 
![image](https://user-images.githubusercontent.com/25560357/121783016-2fc1e280-cbca-11eb-9e8b-2d046700b291.png)

The details about the best model : 
![image](https://user-images.githubusercontent.com/25560357/121783078-5f70ea80-cbca-11eb-89de-ef24a3b3d69c.png)

![image](https://user-images.githubusercontent.com/25560357/121783121-8f1ff280-cbca-11eb-81cf-418f95af4eee.png)

![image](https://user-images.githubusercontent.com/25560357/121785295-a369ec80-cbd6-11eb-93ae-1de606c3e617.png)


## Pipeline comparison
AutoML tried multiple models as compared to Logistic Reagression by HyperDrive. The accuracy achieved through AutoML Best model ( VotingEnsemble ) is 0.917 as compared to 0.915 achived through HyperDrive. 
Even though the Hyperdrive and AutoML prepare the data in similar way (AutoML being slightly better through data guardrails) , they handle the configurations and execution in different way. For the HyperDrive setup, our ML Model is same but it iterates though multiple set of hyper parameters to get the best results. AutoML on other hand, tries different algorithms / models with optimal hyper parameter values to get the best results. 

Given the higher processing time of different models, AutoML takes much higher time than HyperDrive. While Auto ML can provide the best model for a resident data scientists, the hyperdrive would be used by more mature problem statements where we are clear about the data science models to be used. 

## Future work
1. Rebalance Data. The automl gave a warning on the data imbalance
![image](https://user-images.githubusercontent.com/25560357/121785230-2b9bc200-cbd6-11eb-99b4-a592e2166664.png)

As described in the documentation : https://docs.microsoft.com/en-us/azure/machine-learning/concept-manage-ml-pitfalls
The data imbalance can lead to a falsely perceived positive effect of a model's accuracy, because the input data has bias towards one class, which results in the trained model to mimic that bias.

In order to avoid the same, as given in the suggestion we can try : 
- Change the final metric from Accuracy to something which better handles the imbalances. e.g. AUC_weighted
- Resampling to even the class imbalance, either by up-sampling the smaller classes or down-sampling the larger classes. These methods require expertise to process and analyze.

There are other options which Automl can use (weighted columns) to avoid the impact of imbalance.


## Proof of cluster clean up
**If you did not delete your compute cluster in the code, please complete this section. Otherwise, delete this section.**
**Image of cluster marked for deletion**

![image](https://user-images.githubusercontent.com/25560357/121782979-f6897280-cbc9-11eb-9517-b46c8f9da825.png)

