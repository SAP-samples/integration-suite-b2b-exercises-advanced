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



# **How it works?**

The mentioned CSV file must exist in the data store: BTP4_CVM_Table, which emulates the external persistence. In a case of processing of the business transaction activity “Purchase Order Response”, the pre-processing step is calling the integration flow Pnnnnnnnn . Pre-Processing . OrderResponse - Outbound . SAP IDoc, which picks the appropriate entry from the data store and hands over as parameter to the next activity step, the mapping step. In order find the specific entry, a trading partner related an entry ID “Data_Store_Entry” must be maintained in the Trading Partner Profile(TPP) and referred in the corresponding Trading Partner Agreement(TPA). 

Pnnnnnnnn . Pre-Processing . OrderResponse - Outbound . SAP IDoc

The mapping step gets the result of the BTP4_CVM_Table from pre-processing integration flow via the parameter “CVM_TaxCode” and the currency code via a further parameter Trading_Partner_Currency which is also maintained in the TPP and referred in the corresponding TPA.
Based on the mapped source elements:\

•	Partner number from the sold-to partner (E1EKA1[PARVW = AG]/PARTN)\
•	Material number used by vendor (E1EDP01/E1EDP19[QUALF = 002]/IDTNR)

And the Trading_Partner_Currency from the Trading Partner Profile, the following mapping functions returns the corresponding TaxID from the list CVM_TaxCode:

<xsl:variable name="vPartnerID" select="$nodes_in/PARTN"/>
<xsl:variable name="vIdtNr" select="$nodes_in/IDTNR"/>
<xsl:variable name="vCurrency" select="$Trading_Partner_Currency"/>
<xsl:variable name="vLookupTable" select="tokenize($CVM_TaxCode, '\|')"/>
<xsl:value-of select="for $i in $vLookupTable return if(tokenize($i, ';')[1] = $vPartnerID and tokenize($i, ';')[2] = $vIdtNr and tokenize($i, ';')[3] = $vCurrency) then tokenize($i, ';')[4] else ()"/>

Especially the function for $i in $vLookupTable return if … loops across each entry, compares the three input values and returns the TaxID (fourth value) if there is a match.

