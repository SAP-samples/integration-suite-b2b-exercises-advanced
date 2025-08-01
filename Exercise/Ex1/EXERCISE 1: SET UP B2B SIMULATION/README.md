# **Exercise 2: set up B2B Simulation**

In the first exercise, we set up a B2B simulation so that you can use an API test tool to send the interchange payloads to the Integration Suite - B2B system via an HTTP request. The received result via the synchronous HTTP response will be displayed in the same session of the API test tool like shown in the following figure.

![image](assets/1.png)


## **How it works?**

This B2B simulation is based on 2 integration flows which are connected to the B2B generic flows via ProcessDirect. 

These are:\
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;•	Pnnnnnnnn - Step 1a - B2B Simulation.Sender – This integration flow is responsible for passing the received interchange payload from the API test tool to the step 1b flow for further extraction and processing. Furthermore, this integration flow waits for a few seconds and then obtains the result from the data store, which was stored in here by the Pnnnnnnnn - Step 3b - B2B Simulation.Receiver flow. It returns this resulting interchange payload back to the API test tool using the synchronous HTTP response. The correlation between the sender and receiver interchange payload takes place by using the unique message identification or interchange control number, which is returned via the ProcessDirect call to step 1b flow.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;•	Pnnnnnnnn - Step 3b - B2B Simulation.Receiver - This flow is responsible for passing the final assembled and converted interchange payload to the sender integration flow “Pnnnnnnnn - Step 1a - B2B Simulation.Sender”.  To do this, it stores the resulting interchange payload into the data store together with interchange control number or the message id of the sender interchange payload that is used as correlation identification. This integration flow will be called by Step 3 flow. If the interchange payloads are EDIFACT or ASC X12 interchanges and based on this syntax, line feeds are inserted at the end of each segment if this is desired. 
 

Pnnnnnnnn - Step 1a - B2B Simulation.Sender

![image](assets/2.png)




Pnnnnnnnn - Step 3b - B2B Simulation.Receiver

![image](assets/3.png)



## **Prerequisites**

1.	The exercise 1 must be successfully fulfilled.
2.	Pnnnnnnnn - Step 1a - B2B Simulation.Sender must be deployed.
3.	Pnnnnnnnn - Step 3b - B2B Simulation.Receiver must be deployed.
4.	An API test tool such as Insomnia is successfully installed on your computer.


## **Exercise Steps**

The following steps explain to you how you can set up and test the API testing tool based B2B simulation using the 2 already prepared custom communication integration flows:\
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;•	Pnnnnnnnn - Step 1a - B2B Simulation.Sender\
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;•	Pnnnnnnnn - Step 3b - B2B Simulation.Receiver


1.	Create the B2B receiver simulation communication channel by the following steps:\
(a)	Go to B2B Scenarios -> Your Trading Partner (P12345678)  -> Systems  -> Communications\
(b)	Click on “Create” button\
(c)	Enter the Name: “B2B-Simulation.Receiver”\
(d)	Enter the Alias: “P12345678_B2B-Simulation.Receiver” whereby P12345678 is your user id\
(e)	Enter the same value as Description\
(f)	Select the Direction: Receiver\
(g)	Select the Adapter “Process_Direct”\
(h)	Enter the Address: /TP_P12345678/tpm/b2b-simulation/receiver whereby P12345678 is your user id address\
(i)	Click on “Save” button

![image](assets/4.png)




2.	Stay in the in the “Communication Channel” and create the B2B sender simulation communication channel by the following steps:\
(a)	Click on “Create” button\
(b)	Enter the Name: “B2B-Simulation.Sender”\
(c)	Enter the Alias: “P12345678_B2B-Simulation.Sender” whereby P12345678 is your user id\
(d)	Enter the same value as Description\
(e)	Select the Direction: Sender\
(f)	Select the Adapter “Process_Direct”\
(g)	Click on “Save” button

![image](assets/5.png)



3.	Open your trading partner agreement “Procure to Pay - P12345678” that you created in exercise 1, and\
(a)	Deactivate it, and\
(b)	Click on “Edit” button

![image](assets/6.png)


4.	Change the communication channels accordingly:\
(a)	Purchase Order Request – Sender Communication: B2B-Simulation.Sender\
(b)	Purchase Order Request – Receiver Communication: B2B-Simulation.Receiver\
(c)	Purchase Order Response – Sender Communication: B2B-Simulation.Sender\
(d)	Purchase Order Response – Receiver Communication: B2B-Simulation.Receiver\
(e)	Click on “Save” button

![image](assets/7.png)


5. Write the changed configuration into the Partner Directory by \
(a)	Clicking on “Activate” button

![image](assets/8.png)


6.	Take your API test tool of your choice. We prepared the content for Insomnia. If you don’t have installed an API test tool, download and install it for one of the following download sites:\
Insomnia: https://insomnia.rest/download

![image](assets/9.png)


7.	Your Tenant is opensapeu01


8.	If you go for Insomnia\
(a)	Click on “Import” and\
(b)	Import the provided collection “OpenSAP BTP4 - B2B Simulation - Insomnia Collection” from the discovery page

![image](assets/10.png)


9.	Once you successfully imported the collection\
(a)	You should see the collection of the prepared HTTP Get Requests\
(b)	Click on button ( ) for managing environments\
(c)	In the window “Management Environments” select your tenant (openSAPEU01)\
(d)	Subsitute the user id Pnnnnnnnnn with your user id such as P12345678 (or e.g. User13) and click on close button.

![image](assets/11.png)


10.	Back in the main view\
(a)	Select the environment of your booked tenant name that you identified in step 7

![image](assets/12.png)


10.	If you are now in the Insomnia “Collection” view:\
(a)	Go to the Purchase Order Request  UN/EDIFACT ORDERS entry.\
(b)	Click on “Headers” tab for checking the header attributes.


![image](assets/13.png)


11.	In the “Header” attributes view you should see:\
(a)	communication_protocol: Process_Direct\
Which is in this case the sender communication channel type\
(b)	message_content_type: application/EDIFACT\
Which is the sending MIME Type content type.\
(c)	type_system: UN-EDIFACT\
Which is the sending type system\
(d)	insert_carriage_return: true 

true means, that a carriage return will be inserted after each segment.\
If you set “false”, you’ll get the receiver UN/EDIFACT interchange payload without carriage return after each segment.

![image](assets/14.png)


12.	Now you can simulate the end-to-end flow\
(a)	Click on “Send” button, and\
(b)	You’ll see the result in the HTTP response part after view seconds, if everything was processed sufficiently.

![image](assets/15.png)

[Continue with Exercise 2A](Exercise/Ex1/EXERCISE%201A%3A%20CUSTOMIZATIONS%20AT%20SENDER%20PROCESSING%20SIDE/README.md)
