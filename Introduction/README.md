# **Introduction**

These are now the second part of the exercises in where you will learn more about the customizations using pre- and post-processing, and B2B simulations. 

In the [first exercise](https://github.com/SAP-samples/integration-suite-b2b-exercises-basic/tree/main/exercises/Ex01) you will set the B2B simulation that is based on a custom sender and receiver communication flow. This B2B simulation is very useful for simulating the configuration and mapping business transaction activities and you’ll especially see how the other customizations from next exercise will behave. Starting with this exercise, you especially need an API testing tool. This API testing tool is necessary for submitting data via to the communication sender channel flows.

With the second exercise, you will now learn how you can customize the business transaction activities in trading partner agreements at sender side by using:

+ Custom pre-processing using a customized pre-processing integration flow, and
+ Custom parameters also in combination with the mapping

The third exercise cover the definition and use of B2B Monitoring related search attributes. This is very useful, if you want to display and search via values which are deeply in business messages and not a part of the headers such as business document identifiers.

Through the fourth exercise, you will learn how and for what customized post-processing can be created, configured, and used as custom post-processing in the TPA.
In the fifth exercise, you will learn how to create and configure a customized receiver communication.
 
# **PREPARATION: COPY AND DEPLOY INTEGRATION FLOWS**

For the following 5 exercises, it is necessary to copy several integrations flows to your own integration package and finally these copied integration flows should also be configured and deployed.

**Keep in your mind to change the IDs (e.g P123456 will be then User13 – 13 is your id). Or in other words replace “P” with “User” and the “123456” or “nnnnnn” with your id “13”.**


**Explanation:**

1. Go into your Integration Suite tenant which you used for the [exercise 1](https://github.com/SAP-samples/integration-suite-b2b-exercises-basic/tree/main/exercises/Ex01)
   1. Choose Design -> Integration and APIs
    2.Open the integration package: **OpenSAP BTP4 Week3 Exercises - Templates**

![image](assets/1.png)


1. Copy all the 6 integration flows of this integration package into your already created integration package **openSAP-BTP4-P12345678** by the following steps:
    1.	Click per integration flow at Action button -> Copy.
    2.	In the window “Copy …” change the prefix “Pnnnnnnnn” with your user id such as “User13” 
    3.	Delete the suffix “_copy.”
    4.	Select your integration package such as **openSAP-BTP4-User13**
    5.	Click on button “Copy.” 
    6.	In the message window click on button “Cancel” and do the same with the next integration flow

Remark: You may have mistakenly copied the integration flow to the same template integration package. Delete this integration flow from this package, please.

![image](assets/2.png)

1. Go into your package openSAP-BTP4-User13 -> Artifacts, and
    1.	Select all 6 copied integration flows which start with your user id.
    2.	Click on Actions -> Configure

![image](assets/3.png)

1. In the window “Configure Selected Artifacts”
    1.	Change in the configuration menus of all selected integration flows. 
    2.	The placeholder “Pnnnnnn” into user id such as UserXX (e.g. TP_User13)
    3.	Once you changed user id in all configuration menus, click on button “Deploy all”.
    4.	Click on “Save” button in the message window “Confirmation”

![image](assets/4.png)

[Start with Exercise 2](../Exercise%202/README.md)

