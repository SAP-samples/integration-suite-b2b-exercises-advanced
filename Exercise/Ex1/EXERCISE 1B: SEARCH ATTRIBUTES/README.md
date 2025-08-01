# **Exercise 2B: search attributes**

In the third exercise, you will learn how to configure the B2B monitoring specific search attributes and how to display and use them in B2B monitoring. Search attributes help you to show further values in B2B monitoring which will not provide by the TPM standard parameters. For example, you can than obtain for example business document or purchase order numbers per sender or receiver interchange payload and display them in the B2B monitoring. Furthermore, you can search for B2B interchange entries via these attributes such as business document or purchase order number.

## **Prerequisites**

1.	Exercise 1 must be successfully fulfilled.
2.	Integration flow: Pnnnnnnn . Post-Processing . OrderResponse - Outbound. UN-EDIFACT is deployed.


## **Configuration Steps and Test**

The following steps explain how you can set up search attributes and use these in the B2B Monitoring.

1.	This step is already done by us but check it before!
   
2.	In the “Trading Partner Management” \
(a)	Open the tab “Configuration Manager”\
(b)	In the section “Custom Search Attributes”, click on the menu button with the three points, if “Create” is not visible. \
(c)	Click on the “Create” button.\
(d)	In the opened window “Create Attribute” enter in the field of “Name”: Document Nr.\
(e)	And in the field of “Description”: The number of the business document\
(f)	Click on “Save” button.\

![image](assets/1.png)


3.	In the list of “Custom Search Attributes”
(a)	Now you should see one entry with the name “Document Nr.”.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Remark**: Please consider, currently it is possible to create up to ten different search attributes for the overall tenant. If another user already created a search attribute with the name “Document Nr.”, you could define a new search attribute with another name. You can create search attributes as long as 10 search attributes are not created at your tenant. You can also use one of already existing search attributes for the next steps.

![image](assets/2.png)


4. Go into your TPA: “Procure to Pay – Pnnnnnnnn” and\
(a)	Open the tab “Custom Search Attributes.”\
(b)	Click on “Add” button for the insertion of a search attribute.\
(c)	In the opened window “Add Attribute” select in “Name” the value: “Document Nr.”\
(d)	Select the “Source Type”: XPath\
(e)	Enter the XPath expression: //E1EDK01/BELNR[1]\
(f)	Select the Data Source: Receiver Interchange\
(g)	Select the Business Transaction Activity: Inbound\
(h)	Click on “Save” button.

![image](assets/3.png)

5.	After creating this search attribute, you\
(a)	Should see this search attribute in the list of “Attributes”.\
(b)	Add another search attribute with the parameters:\
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;i) “Name”: “Document Nr.”\
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ii) “Source Type”: XPath\
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;iii)	XPath expression: //E1EDK01/BELNR[1]\
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;iv)	Data Source: Sender Interchange\
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;v)	Business Transaction Activity: Outbound\
(c)	 Save and Click on “Update” button.

![image](assets/4.png)

6.	Now, you can go to your API testing tool and\
(a)	Send for e.g., the IDOC ORDRSP.ORDERS05 - Purchase Order Response message.

![image](assets/5.png)


7.	Go to the B2B Monitoring as described in the exercise 1. If you do an update\
(a)	You might see a new entry in where you should open the details.\
(b)	In the detail panel, click on the tab “Custom Attributes”. \
(c)	You will see the tag “Document Nr.” with the value from the element /ORDERS05/E1EDK01/BELNR[1].

![image](assets/6.png)


8.	You can also add these search attributes in the B2B Monitoring overview list by \
(a)	Clicking on the “Customization” button in the overview list “Interchanges”.\
(b)	You see a window “Columns” in where you can select and deselect the visibility of the columns. Select in here the search attribute “Document Nr.”\
(c)	Click on “OK” button.

![image](assets/7.png)


9.	For searching, you must add the search attributes into the search mask by\
(a)	Clicking on “Go” button.\
(b)	Select the search attribute “Document Nr.” in the opened window “Adapt Filters”, and\
(c)	Click on “OK” button.

![image](assets/8.png)

10.	You will find now the B2B Monitoring list for the search attributes the search field and column “Document Nr.”\
(a)	You can now enter the value of the BELNR into the search field “Document Nr.”\
(b)	Click on “Go” button.\
(c)	And you’ll see just the filtered list of interchanges with the same value in field “Document Nr.”.

![image](assets/9.png)

[Continue with Exercise 2C](Exercise/Ex1/EXERCISE%201C%3A%20CUSTOMIZATIONS%20AT%20RECEIVER%20PROCESSING%20SIDE/README.md)
