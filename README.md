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

Here we import required libraries, then define the bucket name, defining the paths where our training and testing data will be. I have already uploaded the diabetes data in our S3 bucket. Then we import the data from our S3, then split it into training and validation data. We then saves these train and test data and writing back to s3 model in training and testing paths respectively. Then we import a docker image/ container for our xgboost algorithm from aws itself. We define an estimator which contains our container, total number of ianstances used for training (=1), type of the isntance (=ml.m4.xlarge), our output(model)path and our job name. Set the hyperparameters (max_depth=7, num_round = 1000, objective: binary logistic).Once the training is done, in AWS Sagemaker console go to training jobs section and you will see the list of all training jobs that has been done till now. You can also the see the hyperparameter tuning jobs , here i have seperately run in my local machine and got the hyparameters values and directly fed to to our model to save the AWS cost.  

![training_job](https://user-images.githubusercontent.com/36281158/92396633-f6dc7500-f142-11ea-82b2-af2aa7e6230a.PNG)


6. Endpoint creation

Endpoint is basically where our clients will hitting up the URL to get the result whether the person has diabetes or not by inputting the numerical values of the independent variables. 

To create the endpoint, go to endpoints tab and click on create endpoint. Create with new configurationa, add name, click on "add model" below which we created above and create. 

![endpoint_final](https://user-images.githubusercontent.com/36281158/92411083-13d57000-f164-11ea-9a3e-6650f4837c72.PNG)

#### 7. Integration With AWS sagemaker using serverless.yml and handler.py files    

After the endpoint creation, install serverless framework from the internet. Download node.js from the link https://nodejs.org/en/download/. It will install all the requirements. After installing, do the following steps in order to connect serverless with the aws 

a) Adding configurations. Create a new user under IAM (Identity and Access Management) section. Give administrative access initailly, after creating the user, you will get two keys; Access Key Id and Secret Access Key. Then type the following command in Windows powershell (make  a new folder and then going into that directory)

serverless config --provider aws --key Access Key Id --secret Secret Access Key 

b) Then type the below command to get the template files (handler.py and serverless.yml) which we will edit

serverless create --template aws-python --name diabetes_Pred

c) Description and coding of two files 

serverless.yml file
here you define what kind of service (diabetesPrediction), provider (define python version (python 3.8), region (us-east-1), endpoint resource (link cicled in the belwo pic), action means what you want your resource to do (we want to invoke the endpoint)) and function (what function will it be execute that is defined in handler.py file)\

![4](https://user-images.githubusercontent.com/36281158/92412562-f5727300-f169-11ea-85c3-130f79d62f69.PNG)

Handler.py file 






