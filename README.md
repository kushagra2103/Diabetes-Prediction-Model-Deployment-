# Diabetes-Prediction-Model-Deployment-Using-AWS Sagemaker


## Model Building 

### Problem Statement

Here we going to make a model which will predict whether a person has diabetes or not based on 8 features. We will implement xgboost algorithm. Then we will see how deployment as a REST API on AWS sagemaker is done.

### Dataset 

The data is taken form https://www.kaggle.com/uciml/pima-indians-diabetes-database. It has 768 rows and 9 coulmns

Features are as follows 

Independent Variables 
1. Pregnancies	

2. Glucose

3. BloodPressure

4. SkinThickness

5. Insulin

6. BMI	

7. DiabetesPedigreeFunction

8. Age

Dependent Varaible 

Outcome: It has values o and 1. O refers to No diabetes and 1 refers to diabetes 

Independent Varibales are not much corrleated to each as seen from the correlation matrix

![1](https://user-images.githubusercontent.com/36281158/92333493-83375b00-f0a3-11ea-9801-44433a076f77.PNG)

Now seeing the pair plot of the variables, there is no trend observable or regions of diabletes to non diabetes region is higly overlapping. Therefore our alorithm has to perform very well.

![2](https://user-images.githubusercontent.com/36281158/92333617-94cd3280-f0a4-11ea-9aa1-443e92c48ad1.png)

Missing Values: In this dataset, there are some values are 0 in some columns which dosen't makes any sense, as they are basically the NULL values. So they are replaced by the mean values of that column respectively .


### Training and Accuracy 

#### XGBoost Algorithm 











## Model Deployment in AWS Sagemaker 


### Steps :

1. First make an account on AWS.

2. Then to your AWS sagemaker and on the left, under the "Notebook" click on Notebook instances.

3. Create a new notebook instance using "ml.t2.medium" EC2 instance in the conda-python3 environment.

![3](https://user-images.githubusercontent.com/36281158/92393611-c6dea300-f13d-11ea-9b22-930f19f1ecbf.PNG)

4. Open the new notebook and do the coding part.

5. Notebook Coding :

Here we import required libraries, then define the bucket name, defining the paths where our training and testing data will be. I have already uploaded the diabetes data in our S3 bucket. Then we import the data from our S3, then split it into training and validation data. We then saves these train and test data and writing back to s3 model in training and testing paths respectively. Then we import a docker image/ container for our xgboost algorithm from aws itself. We define an estimator which contains our container, total number of ianstances used for training (=1), type of the isntance (=ml.m4.xlarge), our output(model)path and our job name. Set the hyperparameters (max_depth=7, num_round = 500, objective: binary logistic).Once the training is done, in AWS Sagemaker console go to training jobs section and you will see the list of all training jobs that has been done till now. You can also the see the hyperparameter tuning jobs , here i have seperately run in my local machine and got the hyparameters values and directly fed to to our model to save the AWS cost.  

![training_job](https://user-images.githubusercontent.com/36281158/92396633-f6dc7500-f142-11ea-82b2-af2aa7e6230a.PNG)


6. Endpoint creation

Endpoint is basically where our clients will 



