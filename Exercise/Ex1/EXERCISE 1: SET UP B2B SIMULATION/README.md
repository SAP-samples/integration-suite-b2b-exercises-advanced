# **EXERCISE 2: SET UP B2B SIMULATION**

In the first exercise, we set up a B2B simulation so that you can use an API test tool to send the interchange payloads to the Integration Suite - B2B system via an HTTP request. The received result via the synchronous HTTP response will be displayed in the same session of the API test tool like shown in the following figure.

#**How it works?**

This B2B simulation is based on 2 integration flows which are connected to the B2B generic flows via ProcessDirect. 

These are:
•	Pnnnnnnnn - Step 1a - B2B Simulation.Sender – This integration flow is responsible for passing the received interchange payload from the API test tool to the step 1b flow for further extraction and processing. Furthermore, this integration flow waits for a few seconds and then obtains the result from the data store, which was stored in here by the Pnnnnnnnn - Step 3b - B2B Simulation.Receiver flow. It returns this resulting interchange payload back to the API test tool using the synchronous HTTP response. The correlation between the sender and receiver interchange payload takes place by using the unique message identification or interchange control number, which is returned via the ProcessDirect call to step 1b flow.

•	Pnnnnnnnn - Step 3b - B2B Simulation.Receiver - This flow is responsible for passing the final assembled and converted interchange payload to the sender integration flow “Pnnnnnnnn - Step 1a - B2B Simulation.Sender”.  To do this, it stores the resulting interchange payload into the data store together with interchange control number or the message id of the sender interchange payload that is used as correlation identification. This integration flow will be called by Step 3 flow. If the interchange payloads are EDIFACT or ASC X12 interchanges and based on this syntax, line feeds are inserted at the end of each segment if this is desired. 
 

Pnnnnnnnn - Step 1a - B2B Simulation.Sender


Pnnnnnnnn - Step 3b - B2B Simulation.Receiver


**Prerequisites**

1.	The exercise 1 must be successfully fulfilled.
2.	Pnnnnnnnn - Step 1a - B2B Simulation.Sender must be deployed.
3.	Pnnnnnnnn - Step 3b - B2B Simulation.Receiver must be deployed.
4.	An API test tool such as Insomnia is successfully installed on your computer.


**Exercise Steps**

The following steps explain to you how you can set up and test the API testing tool based B2B simulation using the 2 already prepared custom communication integration flows:
•	Pnnnnnnnn - Step 1a - B2B Simulation.Sender
•	Pnnnnnnnn - Step 3b - B2B Simulation.Receiver



