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

Here we import required libraries, then define the bucket name, defining the paths where our training and testing data will be. I have already uploaded the diabetes data in our S3 bucket. Then we import the data from our S3, then split it into training and validation data. We then saves these train and test data and writing back to s3 model in training and testing paths respectively. Then we import a docker image/ container for our xgboost algorithm from aws itself. We define an estimator which contains our container, total number of ianstances used for training (=1), type of the isntance (=ml.m4.xlarge), our output(model)path and our job name. Set the hyperparameters (max_depth=7, num_round = 1000, objective: binary logistic).Once the training is done, in AWS Sagemaker console go to training jobs section and you will see the list of all training jobs that has been done till now. You can also the see the hyperparameter tuning jobs , here i have seperately run in my local machine and got the hyparameters values and directly fed to to our model to save the AWS cost. Deploying the model as an endpoint ( with number of instance =1, type = ml.m4.xlarge. Now if you want to handle many requests , then you can increase the number of instances and also upgrade the type of EC2 instance. 

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
here you define what kind of service (diabetesPrediction), provider (define python version (python 3.8), region (us-east-1), name of the endpoint (diabetes-pred), endpoint resource (link cicled in the belwo pic), action means what you want your resource to do (we want to invoke the endpoint)) and function (what function will it be execute that is defined in handler.py file)

![4](https://user-images.githubusercontent.com/36281158/92412562-f5727300-f169-11ea-85c3-130f79d62f69.PNG)

Handler.py file  (also known as lambda function) 

This function will get invoked when the client through AWS API GATEWAY will request 

The function will take the input paramters (numerical values), convert it into the list of of str type and then will be given to the endpoint and return type will be json format. 

For additional information, go to https://docs.aws.amazon.com/sagemaker/latest/dg/cdf-inference.html

Now the two files have been edited, save it and run the following command in the powershell promt. 

serverless deploy --force

![5](https://user-images.githubusercontent.com/36281158/92414454-e5f72800-f171-11ea-927b-351e695cf606.PNG)

Here we will get the url (highlighted one in the above pic ) which will be provided to the client. It s a post url means client will give the input and get the required result . For web interface, HTML and CSS can be used to get the proper UI format .

d) Checking if the REST API is working or not 

Give the values in the format "pregnancies=1&glucose=12&BP=180&SkinThickness=1&Insulin=23&BMI=34&DiabetesPedigreeFunction=1&Age=76" by going to API gateway and under the POST section, click on test and underquery strings section paste it and it will generate "0" (no diabetes) under the response body.

![6](https://user-images.githubusercontent.com/36281158/92414623-abda5600-f172-11ea-957e-38514359efd7.PNG)

How the lambda function and API GATEWAY works ?

![7](https://user-images.githubusercontent.com/36281158/92414820-96196080-f173-11ea-905f-e6a162e8b577.PNG)

 Starting from the client side, a client script calls an Amazon API Gateway API action and passes parameter values. API Gateway is a layer that provides API to the client. In addition, it seals the backend so that AWS Lambda stays and executes in a protected private network. API Gateway passes the parameter values to the Lambda function. The Lambda function parses the value and sends it to the SageMaker model endpoint. The model performs the prediction and returns the predicted value to AWS Lambda. The Lambda function parses the returned value and sends it back to API Gateway. API Gateway responds to the client with that value
 
 For more info , visit this link https://aws.amazon.com/blogs/machine-learning/call-an-amazon-sagemaker-model-endpoint-using-amazon-api-gateway-and-aws-lambda/
 
 PS: 







