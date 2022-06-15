# Starbucks Capstone Challenge

## Introduction

sends offers to customers using its mobile application, these offers can be sent through the web or via email, these offers range from discounts for future purchases to promotions such as buy one get one free (BOGO), however, no all customers receive these offers, additionally within the customers who receive them not all see and use them and some even make their transactions without even using the offers.

The task is to combine transaction, demographic and offer data to determine which demographic groups respond best to which offer type. This data set is a simplified version of the real Starbucks app because the underlying simulator only has one product whereas Starbucks actually sells dozens of products.

## Problem

In this project my objective was to find those characteristics of a client and their transactions that managed to make it possible to complete an offer successfully, since with the aim of offering a more personalized attention to clients, it is important to know what strategies work. more for each population segment and at the same time lead to increased sales.


## Data

In order to carry out the respective analyses, the following three sets of data are available and described below.

* **portfolio.json** : containing offer ids and meta data about each offer (duration, type, etc.)
* **profile.json** : demographic data for each customer
* **transcript.json** : records for transactions, offers received, offers viewed, and offers completed

Each variable in the files is described next:

#### **portfolio.json**

- id (string) - offer id
- offer_type (string) - type of offer ie BOGO, discount, informational
- difficulty (int) - minimum required spend to complete an offer
- reward (int) - reward given for completing an offer
- duration (int) - time for offer to be open, in days
- channels (list of strings)

#### **profile.json**

- age (int) - age of the customer
- became_member_on (int) - date when customer created an app account
- gender (str) - gender of the customer (note some entries contain 'O' for other rather than M or F)
- id (str) - customer id
- income (float) - customer's income

#### **transcript.json**

- event (str) - record description (ie transaction, offer received, offer viewed, etc.)
- person (str) - customer id
- time (int) - time in hours since start of test. The data begins at time t=0
- value - (dict of strings) - either an offer id or transaction amount depending on the record

## Exploratory Data Analysis and Data Preprocessing 

1. **portfolio :**  The data frame contains information for 10 different offers, we found three different types of offer like: BOGO (buy one get one), Discount, and Informational. Aditionally has data about the diferent chanels where the offer are sended to a client and the duration of the offert to can use it.

For the cleaning of this database, a coding was created in dumiees variables for the types of offer, the information of the data string of the channel variable is extracted to be able to convert them into dumiees variables.

![image](https://user-images.githubusercontent.com/100497288/173728282-b04cd6ef-8785-411b-9f3e-a331d9a97059.png)

In the case of the distribution of the gender, more than 8000 clients are male, about 6000 are female and the rest of the clients are others.

![image](https://user-images.githubusercontent.com/100497288/173730345-3aeef8e8-4f5c-4d8f-91d6-907ab79fb150.png)

When we compare de income with the gender it s posible to see that the male are concentrate below of 8000 of income, while the women has a more wide distibution and concentrates their income between 50000 and 100000, the other genders has income between 50000 and 70000.

![image](https://user-images.githubusercontent.com/100497288/173730570-1ac33a79-bb38-4ad9-a036-f0f9b912acab.png)


2. **profile:** There are information of 17000 customer profiles available for the analysis. in this case there is information of 5 variables age
became_member, gender, go and income, we found 2175 null data in two variables gender and income, as we saw the nulls coincide at the time in the two variables, additionally we found an atypical value in the age variable that corresponds to the value of 118.

For cleaning, dummy variables were created on the gender variable and I extracted the information of the month and year in which the client linked to the application

![image](https://user-images.githubusercontent.com/100497288/173729596-9e99f235-e13a-4292-bf1d-6aef33dc1ec3.png)

in the posible values of the age varible there are an atipic value this is the age of 118 which can be an error in the information because is not a typical value in this variable, and appears to be related with the null values in the data set

![image](https://user-images.githubusercontent.com/100497288/173730740-7f981f7a-067d-4ab1-9e0a-17be5eb72bb5.png)

On this part I change the datatype of the variable became member on in a datatime to extract the year and see the distribution of the variable and found that 2017 was the year when we more clients become a member

![image](https://user-images.githubusercontent.com/100497288/173730841-92587015-6dbe-42ad-a9d8-5aaf4536bd95.png)


3. **transcript:** There are information of 17000 customer profiles available for the analysis. in this case there is information of 5 variables age
became_member, gender, go and income, we found 2175 null data in two variables gender and income, as we saw the nulls coincide at the time in the two variables, additionally we found an atypical value in the age variable that corresponds to the value of 118.

Contains information on customer transactions, as well as whether or not they saw the offers that were sent to them and if they used them, for cleaning, dummy variables are created for the categories of the event variable

![image](https://user-images.githubusercontent.com/100497288/173730039-ce39ddba-3faa-480d-9a25-da094d7e9ccd.png)

we see that there are transactions that no necesary are related with offert sends, actually some clients doesn't even received an offert but still buy products, in the group of clientes who received an ofert the 75% view the offert and from these 58% use the ofert, these is the behaviour we see in the graph below.

![image](https://user-images.githubusercontent.com/100497288/173730997-424c0758-19b3-4d63-871e-1a4ecb6e3931.png)


## Model

the target variable was created taking into account as success those customers who saw the offer and additionally completed the transaction, for this a temporary variable was created to check the existence of an offer prior to purchase.

```markdown
Syntax highlighted code block

def create_model(df, target, target_names, model):
    '''
    Builds classification model
    
    INPUT:
    df - the dataframe to be analyzed
    target - the outcome variable
    model - the model to evaluate
    
    OUTPUT:
    model - the classification model
    
    '''
    df = df.dropna(how='any', axis=0)
    
    
    X = df.drop(columns=['Client_id', 'Offer_id', 'time', 'became_member_on',
                        'offer completed', 'offer received','offer viewed', 'event',
                        'gender','success'])
    
    y = df.pop(target)
    
    X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.3, random_state=123)
    
    model = model

    model.fit(X_train, y_train)
    
    
    model_predictions = model.predict(X_test)
    print(classification_report(y_test, model_predictions, target_names=target_names))
    
    print("Total model accuracy:\t {}".format(accuracy_score(y_test, model_predictions)))
    
    train_probs = model.predict_proba(X_train)
    model_probs = model.predict_proba(X_test)
    print("Train ROC AUC score:\t {}".format(roc_auc_score(y_train, train_probs[:, 1])))
    print("Test ROC AUC score:\t {}".format(roc_auc_score(y_test, model_probs[:, 1])))
    
        
    return model

```
I decide to use the ROC, precision, recal, f1-score as metrics to compare the models, because are the more frequently metrics used to studie the performance of an classification model, in the analysis we see the value in the ROC for the model applied to the total data set, the train data and the test data, the idea is that the value of the metric has to be the more similar in the three set to has better performance and avoid problems like overfitting.

![image](https://user-images.githubusercontent.com/100497288/173732226-607d0dfb-4e46-4e40-b316-6d045522f4ac.png)

According with the results I found that te Adaboost has better metrics, more stable in the groups. Thats why I decide to use this model to predict my target variable. 

Once the model was selected, a search was made for the best parameters for the model, then an analysis was carried out to determine which variables influence the target variable.

![image](https://user-images.githubusercontent.com/100497288/173732507-2d457487-e6b1-427a-af9c-43498842bee7.png)

## Reflection

With some analisys of the data I found that contrasting the offert type with the gender, in the case of the male the offer lead to more succesfull cases in both offer type, but the bogo type is slightly more efficient with 45% of succes than the discount type with 45% of success, while in the case of the famale, the success in both type are less than in the case of male and aditionally there is no significant diferences in the type of offer they recived

In this project, I have the goal of predict offer success based on the available information from the clients and the offerts. I built three classification model to predict the success, a random forest,Gradient Boosting , AdaBoost and found based on the metrics from the total model, the train and test results that the AdaBoostClassifier was the best option for may case and the way I work with the data.

The model selected can be used to predict whether an offer is going to be successfully or not. The final model has an accuracy of 70%, not a bad value but can be improved whit a more exacutive work.

The most important variables to determinate aw offer success result to be:

- Age
- Income
- difficulty
- start_month
- num_offers

we also see that the offerts are a litlle more efective with famale than male and that more income leads to more succes.

## Improvement
Explore better prepocesing for the data, a more wide exploratory analysis and better modeling techniques.
