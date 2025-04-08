# **EXERCISE 1C: CUSTOMIZATIONS AT RECEIVER PROCESSING SIDE**


In the this exercise, you will learn how to do a further sorting of the mapping output through an integration flow “Pnnnnnnn . Post-Processing . OrderResponse - Outbound. UN-EDIFACT” that is called by custom post-processing.  In this case, the line items should be sorted according to the Tax Identifiers from Exercise 3. It means the line items with the same Tax Identifiers should be grouped together. 

You will also learn, how you can calculate a summary of line items. This should be provided via an additional MOA (Monetary Amount) segment in the trailer part of the message. 

You will also learn, how you can set of ordinal numbers per line item in the data element 1082 (Line item identifier) of the LIN segment. Like in the following example shown, a further part of this exercise is the insertion of further values that are not supported by the standard TPA configuration such as the value EDI_ORDRPS in the data element 0032 – Communications agreement into the UNB interchange envelope segment and the insertion the UNA (Service String Advice) segment.
The expected output should be:


# **How it works?**

The integration flow of the custom post-processing “Pnnnnnnn . Post-Processing . OrderResponse - Outbound. UN-EDIFACT” is very simple. It just calls a XSLT script, which is responsible for:\

a)	Sorting of line items – This is realized by the XSLT function “for-each-group” in where each tax identifier (./G_SG36[1]/S_TAX[1]/D_3446[1]) will be the group criteria for the grouping. This group criteria is provided by the attribute “group-by”.  \

b)	Summary in MOA segment – This function needs an already generated data element D_5004 (Monetary amount) in the generated MOA summary segment. If there is a match to this data element (xsl:template match="D_5004[../D_5025 = '128']"), the XSLT calculates the sum and returns this sum in specific output format by the function format-number(sum(preceding::D_5118), '#.00')\

c)	Set ordinal number in LIN segment – This function also needs an already generated data element D_1082. If there is a match to this data element (xsl:template match="D_1082"), the XSLT calculates for this data element the ordinal number by the function count(preceding::G_SG26) + 1 and inserts to it.\

XSLT Script:

# **Prerequisites**

1.	[Exercise 1](https://github.com/SAP-samples/integration-suite-b2b-exercises-basic/tree/main/exercises/Ex01) must be successfully fulfilled.
2.	Integration flow: Pnnnnnnn . Post-Processing . OrderResponse - Outbound. UN-EDIFACT must be deployed.

# **Configuration Steps and Test   **

The following steps explain how you can set up a custom post-processing, which will manipulate the target payload after the mapping step.

1.	You need now further extensions in the target MIG and MAG. Therefore, open your TPA “Procure to Pay – Pnnnnnnn”.\
(a)	Select the business transaction activity “Purcchase Order Response” the step “Mapping”, and\
(b)	Open the MAG by clicking on the version in the opened callout.

2.	Once the MAG is opened\

(a)	Enable the MAG in edit mode by clicking on “Edit” button, and\
(b)	Open the target MIG by clicking on the link on top of the target structure.

3.	Switch to the edit mode. Once you did it, add following group and leaf nodes according to the instructions of creating a MIG (Chapter 2) from [Exercise 1](https://github.com/SAP-samples/integration-suite-b2b-exercises-basic/tree/main/exercises/Ex01). \

(a)	Data element 1082 (Line item identifier) in SG26  LIN\
(b)	Segment group “MOA” (Monetary Amount) underneath the segment UNS. This MOA segment should be qualified with the qualifier value “128” (Total amount) and the following child nodes should be selected:\
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;i)	C516 – Monetary Amount\
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ii)	5025 – Monetary amount type code qualifier\
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;iii)	5004 – Monetary amount\
(c)	Click on “Save” button and may “Cancel” button for unlocking the MIG.


4.	Now you can add the additional mapping elements into the MAG. Therefore, go back into the opened “MAG” and click on refresh button (F5) so that you can see the extensions made in the target MIG. Once the MAG in the edit mode, you should do following steps:\

(a)	If there is a mapping to the node 1082 delete this (right click on the line and press delete)\
(b)	Add in the leaf node (data element) 1082 - Line item identifier underneath SG26  LIN a constant by clicking on action button, and\
(c)	Add the constant value “###” into it so that this leaf node will be created in target payload.\
(d)	Click on “OK” button.\
(e)	Crate a mapping element from source group node “E1EDS01” to the target group node “MOA[5025 = 128] so that this group node will be created in the target payload. \(**Remark**: This group node will be otherwise not created, because it is optional)\
(f)	Go to the leaf node (data element) 5004 – Monetary among and click on action button.\
(g)	So that you can enter the constant “1” (it must be an integer).\
(h)	Click on button “OK”.\
(i)	Click on “Save” button and may “Cancel” button for unlocking the MAG.


5. Once you added the extensions into the MAG, go back into your TPA “Procure to Pay - P12345678” and add further activity parameters which inserts additional values into the envelope header (UNB segment) by \
(a)	Open the tab “Activity Parameters”\
(b)	Click on Add Parameters -> Create activity parameters  -> Outbound\
(c)	Add the parameter key “SAP_EDI_REC_Interchange _Agreement_ID” which refers to the data element 0032 – Communications agreement ID in the UNB segment\
(d)	Enter the value “EDI_ORDRSP”.\
(e)	Click on “Save” button.


6.	Once the new activity parameter is saved, you \
(a)	Should see the new entry in the list of “Activity parameters”. \
(b)	Should than go to the receiver activity step “Interchange”. In here you should enter the address for pointing to the custom post-processing.

7.	This address can be taken from the custom post-processing integration flow. Therefore, open in another web browser tab the integration flow: “Pnnnnnnnn . Post-Processing . OrderResponse - Outbound. UN-EDIFACT” and \
(a)	Do a double click on the adapter line “ProcessDirect” so that you can see the details of the “ProcessDirect”. \
(b)	Take in tab “Connection” the address: /TP_Pnnnnnnnn/tpm/post-processing/orderResponse-outbound/un-edifact and copy into a notepad (Please consider Pnnnnnnn should be your user id)

8.	Open the windows browser tab  in where you see the TPA “Procure to Pay - P12345678” including the already selected receiver interchange activity. If the TPA is in edit mode, you should:\
(a)	Select the check box of “Customized Post-Processing” and enter the copied address /TP_nnnnnnnn/tpm/post-processing/orderResponse-outbound/un-edifact 
into the field of “Process Direct Address”.\
(b)	For inserting the UNA segment into the receiver interchange payload, select the check box “Use Custom Separators” and select following values:\
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;i)	Segment Terminator: Apostrophe (‘)\
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;ii)	Composite Terminator: Colon (:)\
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;iii)	Data Element Separator Plus (+)\
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;iv)	Escape Character: Question Mark (?)\
(c)	Click on “Save” button.

9.	Once you finished and saved the configuration\
(a)	Click on “Update” button so that these changes will be written into the Partner Directory.

10.	Once the updated business transaction is updated successfully, \
(a)	You can now send the IDOC ORDRSP.ORDERS05 (Purchase Order Response) message via your API testing tool.\
(b)	After a short while you’ll find the response with the added UNA segment\
(c)	With an added value in the data element “0032 – Communications agreement ID” in the UNB segment\
(d)	With an ordinal number per line item in the data element 1082 (Line item identifier)\
(e)	With a resorted line items according to the values of data element (3446 - Party tax identifier) in segment group SG32 -> TAX\
(f)	And with the summary of all amounts from the PRI segments of the line items in the segment MOA with the qualifier 128.

