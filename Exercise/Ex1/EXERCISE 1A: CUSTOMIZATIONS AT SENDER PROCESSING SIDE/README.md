# **EXERCISE 1A: CUSTOMIZATIONS AT SENDER PROCESSING SIDE**

The second exercise is focused on the business transaction activity: Purchase Order Response in where the SAP IDOC ORDRSP.ORDERS05 is the sender interchange payload. It involves determining the corresponding tax identifier from a lookup table in the form of a CSV file and writing it to the segment TAX with the qualifier 7 within the LIN segment group of the UN/EDIFACT ORDRSP outpout. This CSV file looks like following:\

PARTN;IDTNR;Currency;TaxID|
0001000654;MZ-FG-S100;EUR;AAAA-01|
0001000654;MZ-FG-S200;EUR;BBBB-02|
0001000654;MZ-FG-S300;EUR;AAAA-01|
0001000654;MZ-FG-S400;EUR;BBBB-02|
0001000654;MZ-FG-S100;USD;CCCC-03|
0001000654;MZ-FG-S200;USD;CCCC-03|
0001000655;STC-23222;EUR;AAAA-01|
0001000655;TGB19424905;EUR;BBBB-02


In this case, the TaxId can only be determined using three input values (PARTN, IDTNR, Currency). If there is match of the 2 input values with the respective entries in the CSV file, a TaxID from the specific entry will be returned. The input of PARTN (partner number) is to be taken from the sold-to partner (E1EKA1[PARVW = AG]) in the sender IDOC message. The IDTNR originates from the respective line item, especially from the segment E1EDP19[QUALF = 002] "Material number used by vendor" within this line item (E1EDP01). The currency is a value that is maintained in the trading partner profile as a custom parameter, and it has to be handed over accordingly so that this can be used during the lookup in the mapping step. The expected output should be like in the following example in where you can see that each line item (LIN) has an additional TAX segment with the specific TaxID:

![image](https://github.com/SAP-samples/integration-suite-b2b-exercises-advanced/blob/main/Exercise/Ex1/EXERCISE%201A%3A%20CUSTOMIZATIONS%20AT%20SENDER%20PROCESSING%20SIDE/assets/1.png)


# **How it works?**

The mentioned CSV file must exist in the data store: BTP4_CVM_Table, which emulates the external persistence. In a case of processing of the business transaction activity “Purchase Order Response”, the pre-processing step is calling the integration flow Pnnnnnnnn . Pre-Processing . OrderResponse - Outbound . SAP IDoc, which picks the appropriate entry from the data store and hands over as parameter to the next activity step, the mapping step. In order find the specific entry, a trading partner related an entry ID “Data_Store_Entry” must be maintained in the Trading Partner Profile(TPP) and referred in the corresponding Trading Partner Agreement(TPA). 

Pnnnnnnnn . Pre-Processing . OrderResponse - Outbound . SAP IDoc

![image](https://github.com/SAP-samples/integration-suite-b2b-exercises-advanced/blob/main/Exercise/Ex1/EXERCISE%201A%3A%20CUSTOMIZATIONS%20AT%20SENDER%20PROCESSING%20SIDE/assets/2.png)


The mapping step gets the result of the BTP4_CVM_Table from pre-processing integration flow via the parameter “CVM_TaxCode” and the currency code via a further parameter Trading_Partner_Currency which is also maintained in the TPP and referred in the corresponding TPA.
Based on the mapped source elements:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;•	Partner number from the sold-to partner (E1EKA1[PARVW = AG]/PARTN)\
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;•	Material number used by vendor (E1EDP01/E1EDP19[QUALF = 002]/IDTNR)

And the Trading_Partner_Currency from the Trading Partner Profile, the following mapping functions returns the corresponding TaxID from the list CVM_TaxCode:

<xsl:variable name="vPartnerID" select="$nodes_in/PARTN"/>
<xsl:variable name="vIdtNr" select="$nodes_in/IDTNR"/>
<xsl:variable name="vCurrency" select="$Trading_Partner_Currency"/>
<xsl:variable name="vLookupTable" select="tokenize($CVM_TaxCode, '\|')"/>
<xsl:value-of select="for $i in $vLookupTable return if(tokenize($i, ';')[1] = $vPartnerID and tokenize($i, ';')[2] = $vIdtNr and tokenize($i, ';')[3] = $vCurrency) then tokenize($i, ';')[4] else ()"/>

Especially the function for $i in $vLookupTable return if … loops across each entry, compares the three input values and returns the TaxID (fourth value) if there is a match.


# **Prerequisites**

1.	Exercise 1 is successfully fulfilled.
2.	Integration flow: Pnnnnnnnn . Pre-Processing . OrderResponse - Outbound . SAP IDoc is deployed
3.	Integration flow: P12345678 - Insert entry into lookup table BTP4_CVM_Table once so that you can find in the data store OpenSAP-BTP4_CVM_Table and entry with the ID: EDI_Pnnnnnnnn in where Pnnnnnnnn is your user id. See: Monitor  Integrations and APIs  -> Data Stores  -> OpenSAP-BTP4_CVM_Table

# **Exercise Steps**

The following steps explain to you, how you can integrate integration flow that runs as custom pre-processing in front of the mapping step. It also explains how you can inject the results of the custom pre-processing into the mapping step itself.


1.	Go to your trading partner profile (TPP) such as P12345678 -> Parameters and enter the trading partner related parameters according to the following steps:\
(a)	Click on “Create” button\
(b)	Enter the Parameter Key: Trading_Partner_Currency, and\
(c)	Enter the Value: EUR\
(d)	Click on save button

![image](https://github.com/SAP-samples/integration-suite-b2b-exercises-advanced/blob/main/Exercise/Ex1/EXERCISE%201A%3A%20CUSTOMIZATIONS%20AT%20SENDER%20PROCESSING%20SIDE/assets/3.png)


2.	Create a second custom parameter with the values:\
(a)	Key: Data_Store_Entry\
(b)	Value: EDI_Pnnnnnnn whereby Pnnnnnnnn is your user id.

![image](https://github.com/SAP-samples/integration-suite-b2b-exercises-advanced/blob/main/Exercise/Ex1/EXERCISE%201A%3A%20CUSTOMIZATIONS%20AT%20SENDER%20PROCESSING%20SIDE/assets/4.png)



3.	Go to your trading partner agreement (Procure to Pay – Pnnnnnnnn). -> B2B Scenarios and open it in “Edit” mode. \
(a)	Open the “Activity Parameters”\
(b)	Select Add Parameters -> Extend from TP -> Outbound

![image](https://github.com/SAP-samples/integration-suite-b2b-exercises-advanced/blob/main/Exercise/Ex1/EXERCISE%201A%3A%20CUSTOMIZATIONS%20AT%20SENDER%20PROCESSING%20SIDE/assets/5.png)


4.	In the window “Trading Partner Parameters”, select\
(a)	Trading_Partner_Currency\
(b)	Data_Store_Entry\
(c)	Click on “Save” button

![image](https://github.com/SAP-samples/integration-suite-b2b-exercises-advanced/blob/main/Exercise/Ex1/EXERCISE%201A%3A%20CUSTOMIZATIONS%20AT%20SENDER%20PROCESSING%20SIDE/assets/6.png)


5.	You should see the two trading partner related custom parameters in the tab “Activity Parameters”.

   
   ![image](https://github.com/SAP-samples/integration-suite-b2b-exercises-advanced/blob/main/Exercise/Ex1/EXERCISE%201A%3A%20CUSTOMIZATIONS%20AT%20SENDER%20PROCESSING%20SIDE/assets/7.png)


6.	The next step is to point in the sender interchange to prepared custom pre-processing flow which should be already available in your integration package: “openSAP-BTP4-Pnnnnnnn”. For getting this “pointing” address, \
(a)	Open the integration flow “Pnnnnnnnn . Pre-Processing . OrderResponse - Outbound . SAP IDoc” in your package\
(b)	Do a double click on the “ProcessDirect” adapter\
(c)	Go to tab “Connection” and copy the address “/TP_Pnnnnnnnn/tpm/pre-processing/orderResponse-outbound/sap_idoc” into the clipboard

![image](https://github.com/SAP-samples/integration-suite-b2b-exercises-advanced/blob/main/Exercise/Ex1/EXERCISE%201A%3A%20CUSTOMIZATIONS%20AT%20SENDER%20PROCESSING%20SIDE/assets/8.png)


7.	Go back into the TPA “Procure to Pay – Pnnnnnnn” -> B2B Scenario \ 
(a)	Select the sender interchange step at the purchase order responding activity  \
(b)	Enable the “Customized Pre-Processing” and \
(c)	Insert the copied address “/TP_Pnnnnnnnn/tpm/pre-processing/orderResponse-outbound/sap_idoc” into the field of “Process Direct Address”.

![image](https://github.com/SAP-samples/integration-suite-b2b-exercises-advanced/blob/main/Exercise/Ex1/EXERCISE%201A%3A%20CUSTOMIZATIONS%20AT%20SENDER%20PROCESSING%20SIDE/assets/9.png)


8.	To modify the mapping guideline so that this will do a correct tax output by using the result of this customized pre-processing you should do now the changes in this mapping guideline by \ 
(a)	 Click on the business transaction activity step “Mapping”, and  \ 
(b)	Click on the version of the mapping guideline so that you will get to the Mapping Guideline itself.

**Remark: Take care that here you already work on your own Mapping Guideline which you created in [exercise 1](https://github.com/SAP-samples/integration-suite-b2b-exercises-basic/tree/main/exercises/Ex01)!**

![image](https://github.com/SAP-samples/integration-suite-b2b-exercises-advanced/blob/main/Exercise/Ex1/EXERCISE%201A%3A%20CUSTOMIZATIONS%20AT%20SENDER%20PROCESSING%20SIDE/assets/10.png)


9.	But before you can do the changes in the mapping guideline, you should add the necessary segment group for the “Tax” into the target MIG that is based on the EDIFACT ORDRSP. For this purpose, you should  \
(a)	Enable the edit mode, and \
(b)	Confirm the message for creating a new draft version in case you had an active status of the MAG \
(c)	Click on the link of the target MIG above of the structure of the target MIG. \

**Remark: Take care that here you already work on your own MIG which you created in [exercise 1](https://github.com/SAP-samples/integration-suite-b2b-exercises-basic/tree/main/exercises/Ex01)!** 

![image](https://github.com/SAP-samples/integration-suite-b2b-exercises-advanced/blob/main/Exercise/Ex1/EXERCISE%201A%3A%20CUSTOMIZATIONS%20AT%20SENDER%20PROCESSING%20SIDE/assets/11.png)



10.	Enable the edit mode of the MIG by: \
(a)	Clicking on “Edit” button. \
(b)	Confirm the message for creating a new draft version in case you had an active status of the MIG 

**Remark: Take that you work on your own MIG which you have created in the exercise in [exercise 1](https://github.com/SAP-samples/integration-suite-b2b-exercises-basic/tree/main/exercises/Ex01)!**

![image](https://github.com/SAP-samples/integration-suite-b2b-exercises-advanced/blob/main/Exercise/Ex1/EXERCISE%201A%3A%20CUSTOMIZATIONS%20AT%20SENDER%20PROCESSING%20SIDE/assets/12.png)


11.	If the opened target MIG is in “Edit” mode, you should do the following steps:\
(a)	Insert the segment group 36 with the trigger segment “TAX” as you learned by the “Exercise 1”. In this trigger segment, you should just select the data elements: 
5283 (Duty or fax or fee function)\
3446 (Party tax identifier)\
This segment group should be qualified by the qualifier value “7” (Tax) from the data element 5283.\
(b)	Once you created the new qualified segment group, you should save the update MIG and may click on the button “Cancel” so this MIG is not locked.

![image](https://github.com/SAP-samples/integration-suite-b2b-exercises-advanced/blob/main/Exercise/Ex1/EXERCISE%201A%3A%20CUSTOMIZATIONS%20AT%20SENDER%20PROCESSING%20SIDE/assets/13.png)


12.	Now you can update the MAG so that the tax information will be written into the EDIFACT target. The result of the tax will be provided by a camel exchange property. This can be obtained by a “Global Parameter” in the MAG. Therefore, go back into the MAG and\
(a)	Click on tab “Global Parameters”\
(b)	Create a new “Global Parameter” by clicking on button “Create”\
(c)	In the window “Create New Global Parameter” insert in Parameter Name the name: “CVM_TaxCode”\
(d)	And a short text in the field “Summary” such as: “Tax code list data store lookup table”\
(e)	Click on “Create”.

![image](https://github.com/SAP-samples/integration-suite-b2b-exercises-advanced/blob/main/Exercise/Ex1/EXERCISE%201A%3A%20CUSTOMIZATIONS%20AT%20SENDER%20PROCESSING%20SIDE/assets/14.png)


13.	Once you created this parameter, you should\
(a)	See this new parameter in the list of “Global Parameters”\
(b)	Add the second parameter “Trading_Partner_Currency” using the same procedure.\
(c)	Click on “Create” button.
(d)	Save the MAG!

![image](https://github.com/SAP-samples/integration-suite-b2b-exercises-advanced/blob/main/Exercise/Ex1/EXERCISE%201A%3A%20CUSTOMIZATIONS%20AT%20SENDER%20PROCESSING%20SIDE/assets/15.png)


14.	Now you can extend the MAG with the new mapping element. For this purpose, you should refresh the MAG by using the F5 key so that you can see the extension made in the target MIG. Once the MAG is refreshed\
(a)	Go to tab “Mapping”\
(b)	In the source MIG, search for “PARVW = AG “ or scroll down to the node “E1EDKA1[PARVW = AG]”\
(c)	In the target MIG, search for the node 5283 or scroll down to the node 5283 under the Parent node SG36[5283 = 7], and\
(d)	If constant (#) is not available, set the constant by clicking on button “…”\
(e)	Click on context menu item “Set Constant”\
(f)	You should see the value “7”. If not enter it.\
(g)	Click on “OK” button.\
(h)	Create a mapping element from source node “PARTN” (Partner number) to target node “3446” (Party tax identifier).

![image](https://github.com/SAP-samples/integration-suite-b2b-exercises-advanced/blob/main/Exercise/Ex1/EXERCISE%201A%3A%20CUSTOMIZATIONS%20AT%20SENDER%20PROCESSING%20SIDE/assets/16.png)


15.	Once you created this mapping element, you must insert a second source node and a function into this mapping element by\
(a)	In the source MIG, scroll down or search for “E1EDP19[QUALF = 002]\
(b)	Create a mapping line from source leaf node IDTNR (IDOC material ID) to the target leaf node 3446 (Party tax identifier)\
(c)	Insert the following function into XSLT code: \
<xsl:variable name="vPartnerID" select="$nodes_in/PARTN"/>
<xsl:variable name="vIdtNr" select="$nodes_in/IDTNR"/>
<xsl:variable name="vCurrency" select="$Trading_Partner_Currency"/>
<xsl:variable name="vLookupTable" select="tokenize($CVM_TaxCode, '\|')"/>
<xsl:value-of select="for $i in $vLookupTable return if(tokenize($i, ';')[1] = $vPartnerID and tokenize($i, ';')[2] = $vIdtNr and tokenize($i, ';')[3] = $vCurrency) then tokenize($i, ';')[4] else ()"/>

**Maybe you can copy this xslt code at first to a ascii editor to be sure no unnecessary line breaks are in.**

Press the Validate Button to make sure the xslt is correct\
  (d)	Click on “Save” button.

![image](https://github.com/SAP-samples/integration-suite-b2b-exercises-advanced/blob/main/Exercise/Ex1/EXERCISE%201A%3A%20CUSTOMIZATIONS%20AT%20SENDER%20PROCESSING%20SIDE/assets/17.png)


16.	Once you have made all the changes and in case you created a new version of your MAG, you must change the version in the Mapping step by updating to the latest version by the following steps, \
(a)	If the TPA is in “Edit” mode\
(b)	Click on the mapping step at the “Purchase Order Response” activity\
(c)	Click on button \
(d)	In the window “Select MAG” open the dropdown\
(e)	Select the latest version.\
(f)	Click on button “Choose.”

**Remark: The versions of the corresponding source and target MIGs will be updated, automatically.**

![image](https://github.com/SAP-samples/integration-suite-b2b-exercises-advanced/blob/main/Exercise/Ex1/EXERCISE%201A%3A%20CUSTOMIZATIONS%20AT%20SENDER%20PROCESSING%20SIDE/assets/18.png)


17.	If the MAG is now in the latest version, you can update the business transaction by going back to the TPA “Procure to Pay – Pnnnnnnn” and\
(a)	Click on “Update” button, if this button is enabled.\
**Remark: If the “Update” button is not enabled, you can also do a deactivation or activation, or you can click on “Edit” and “Save” so that this “Update” button will get enabled.**\
(b)	Select the business transaction “Purchase Order Request Response”, and\
(c)	Click on “Update” button.

![image](https://github.com/SAP-samples/integration-suite-b2b-exercises-advanced/blob/main/Exercise/Ex1/EXERCISE%201A%3A%20CUSTOMIZATIONS%20AT%20SENDER%20PROCESSING%20SIDE/assets/19.png)


18.	Finally, you can simulate the changed business transaction by using B2B simulation via the API test tool. Hereby, you should send the corresponding SAP IDoc ORDRSP.ORDERS05 (purchase order response) \
(a)	By pressing the "Send" button

19.	In the response you’ll see the additional qualified TAX segment per line item in where you will also find the “Party tax identification” value which is coming from the provided lookup table.


![image](https://github.com/SAP-samples/integration-suite-b2b-exercises-advanced/blob/main/Exercise/Ex1/EXERCISE%201A%3A%20CUSTOMIZATIONS%20AT%20SENDER%20PROCESSING%20SIDE/assets/20.png)



