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


