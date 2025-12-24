You are tasked with converting the given functional design document into a technical design document in JSON format. The document should include the following key sections:
            1. **Document Title **: Title of the document.
            2. **Purpose **: A brief description of the purpose of the solution(maximum 100 words).
            3. **Solution Overview **: A high-level summary of the solution, including key components and their interactions(maximum 100 words).
            4. **Assumptions**: List 2 to 4 key assumptions based on the given Function Design Document content.            
            5. **Technical Decisions **: Key technical decisions made during the solution design(maximum 100 words).
            6. **All Classes**: Find list of class from the given content with class name, class description and X++ Code as described below.
                a. **Class Name **: Name of the class where updates to business logic or batch jobs occur.
                b. **Class Description**: A brief description of the updated class and its responsibilities(maximum 100 words).
                c. **X++ Code**: Please provide an ** X++ code** demonstrating:
                    - Basic constructs such as loops, conditionals, and data manipulation.
                    - Interaction with tables(if applicable).
                    - Strictly Do not start with **`**. Ensure that the code formatting is maintained with proper line breaks similar to the below given sample.
                    - Code should be practical, well-commented, and real-world oriented for Dynamics AX/365 development. Below is the sample X++ code for class. NOTE: This is only the sample code. Do not return the same code.
                        class GPDiscountFloorValidator
                        {
                            public static void validateDiscountFloor(DiscountTable discountTable)
                            {
                                ItemsWithPriceViolationTable violationTable;
                                ProductTable product;
                                real syntheticCost, currentGP, newGP, discountAmount, finalPrice;
                        
                                // Iterate over products in the discount configuration
                                while select product
                                    join TradeAgreement from TradeAgreementCollection
                                        where TradeAgreement.Product == product.ProductID
                                        && TradeAgreement.DiscountID == discountTable.DiscountId
                                {
                                    // Calculate synthetic cost
                                    syntheticCost = product.PurchasePrice * product.SyntheticCostPercentage;
                        
                                    // Calculate current GP%
                                    currentGP = (TradeAgreement.CurrentUnitPrice - syntheticCost) / TradeAgreement.CurrentUnitPrice;
                        
                                    // Apply discount to calculate final price
                                    discountAmount = TradeAgreement.CurrentUnitPrice * discountTable.DiscountPercentage / 100;
                                    finalPrice = TradeAgreement.CurrentUnitPrice - discountAmount;
                        
                                    // Calculate new GP%
                                    newGP = (finalPrice - syntheticCost) / finalPrice;
                        
                                    // Check for GP% floor violation
                                    if (TradeAgreement.ChannelType == ChannelType::B2B && newGP < discountTable.B2BGPFloor ||
                                        TradeAgreement.ChannelType == ChannelType::B2C && newGP < discountTable.B2CGPFloor)
                                    {
                                        violationTable.ChannelType       = TradeAgreement.ChannelType;
                                        violationTable.ItemNumber        = product.ProductID;
                                        violationTable.NewGP             = newGP;
                                        violationTable.DiscountId        = discountTable.DiscountId;
                                        violationTable.FinalPrice        = finalPrice;
                                        violationTable.B2BGPFloor        = discountTable.B2BGPFloor;
                                        violationTable.B2CGPFloor        = discountTable.B2CGPFloor;
                                        violationTable.SyntheticCost     = syntheticCost;
                                        violationTable.CurrentUnitPrice  = TradeAgreement.CurrentUnitPrice;
                        
                                        violationTable.insert();
                                    }
                                }
                            }
                        }
            7. **All Extension Classes**: Find list of  extension class from the given content with extension class name ,extension class description and X++ Code as described below.
                a. **Extension Class Name**: Name of the Extension class where updates to business logic or batch jobs occur.
                b. **Extension Class Description**: A brief description of the updated extension class and its responsibilities(maximum 100 words).
                c. **X++ Code for Extension Class**: Please provide an ** X++ code** demonstrating:
                    - Basic constructs such as loops, conditionals, and data manipulation.
                    - Interaction with tables(if applicable).Strictly Do not start with **`**. Ensure that the code formatting is maintained with proper line breaks similar to the below given sample.
                    - Code should be practical, well-commented, and real-world oriented for Dynamics AX/365 development. Below is the sample X++ code for extension class. NOTE: This is only the sample code. Do not return the same code.
                        [ExtensionOf(classStr(PurchPurchaseOrderDP))]
                        final class WWIPurchPurchaseOrderDP_Extension
                        {
                            protected PurchPurchaseOrderHeader initializePurchaseOrderHeader(VendPurchOrderJour _vendPurchOrderJour)
                            {
                                PurchPurchaseOrderHeader purchPurchaseOrderHeader;
                                PurchTable purchTable;
                        
                                purchPurchaseOrderHeader = next initializePurchaseOrderHeader(_vendPurchOrderJour);
                        
                                purchTable = PurchTable::find(_vendPurchOrderJour.PurchId);
                        
                                purchPurchaseOrderHeader.WWIDlvModeId = purchTable.DlvMode;
                                purchPurchaseOrderHeader.WWIProjID = purchTable.ProjId;
                                purchPurchaseOrderHeader.WWIVendorID = purchTable.OrderAccount;
                                purchPurchaseOrderHeader.WWIConfirmedDlvDate = purchTable.ConfirmedDlv;
                                purchPurchaseOrderHeader.WWINamedPlace = purchTable.WWINamedPlace;
                        
                                return purchPurchaseOrderHeader;
                            }
                        }
            8. **All Unit Test Cases**: Identify possible unit test cases for the given content. Provide result with Test Case Description, Test Case Expected Result, X++ code for the identified unit Test cases. Consider edge cases, invalid inputs, and possible exceptions while writing the test cases.
                a. **Test Case Name**: An appropriate name to the Test Case Scenario(maximum 50 letters).
                b. **Test Case Description**: A brief description of the test case scenario(maximum 50 words).
                c. **Test Case Expected Result**: The expected result for Test Case 1 (maximum 50 words).
                d. **Test Case X++ Code**: The X++ function takes in [input data types] and returns [expected output].
                    - Strictly Do not start with **`**. Ensure that the code formatting is maintained with proper line breaks similar to the below given sample.
                    Below is the sample X++ code for unit test case. NOTE: This is only the sample code. Do not return the same code
                    [SysTestCase]
                    class ValidateDiscountFloorTest extends SysTestCase
                    {
                        public void validateDiscountFloor()
                        {
                            RetailDiscount discount = RetailDiscount::find(""TCOR-000101"");
                            RetailChannel channel = RetailChannel::find(""003-Compton"");
                            RetailProduct product = RetailProduct::find(""PXA5161-295550"");
                            Percent20 b2bFloor = 10;
                            Percent20 b2cFloor = 12;
                            Percent20 currentGP = calcGrossProfit(product.SyntheticCost, product.CurrentUnitPrice);
                            Percent20 newGP = calcGrossProfit(product.SyntheticCost, product.CurrentUnitPrice - discount.DiscountAmount);
                            if (newGP < b2bFloor || newGP < b2cFloor || product.FinalPrice < product.UMAP)
                            {
                                ItemsWithPriceViolation violationRecord;
                                violationRecord.insert();
                            }
                        }
                        private Percent20 calcGrossProfit(Amount syntheticCost, Amount price)
                        {
                            return ((price - syntheticCost) / price) * 100;
                        }
                    }
            9.**All Tables**: Consider the below instructions points:
                    a. Identify form names from the content.
                    b. Classify the identified form names as Standard or Custom D365 FO forms.
                    c. If Standard form, list out the related D365 FO data sources and their fields if existing or new fields based on the given requirement along with data types (Note: Name the form name with suffix '_Extended' for standard forms).
                    d. If Custom form, suggest data sources and their fields that can be added as new objects in D365 FO based on the given requirement along with data types.
                    e. List all the identified data sources as tables with the below details which can be extracted using your D365FO knowledge:
                        i. **Table Name**: Name of the table.
                        ii. **Table Purpose**: Explain the purpose of the table.
                        iii. **Label**: Table Label
                        iv. **Fields**: List of fields with the below details:
                            1. **FieldName**: Name of the field.
                            2. **DataType**: Data type of the field. Note: Data types should be related to X++ language (eg: anytype, boolean, date, enum, guid, int, int64, real, str, timeOfDay, utcdatetime).
                            3. **Allow Edit**
                            4. **Allow Edit on Create**
                            5. **Mandatory**
    
            Finally, **Avoid providing N/A responses** wherever possible.
            The JSON format output should be structured as follows:
            ```json
            {
              'Document Title': 'Title',
              'Purpose': 'Purpose description',
              'Solution Overview': 'Overview description',
              'Technical Decisions': 'Summary of technical decisions',
              'Assumptions': ['Assumption 1', 'Assumption 2'],
              'All Classes':[{
                'Class Name': 'Class Name',
                'Class Description': 'Class Description',                               
                'X++ Code': 'Place the entire well-structured X++ code for classes here. Strictly Do not start with **`**. Ensure that the code formatting is maintained with proper line breaks.'
              }],
              'All Extension Classes':[ {                                                          
                'Class Name': 'Extension Class Name',
                'Class Description': 'Description of the extension class',
                'X++ Code': 'Place the entire well-structured X++ code for classes here. Strictly Do not start with **`**. Ensure that the code formatting is maintained with proper line breaks.'
              }],
              'All Unit Test Cases':[ {                                                          
                'Test Case Name': 'Unit Test Case Name',
                'Test Case Description': 'Description of the unit test case scenario',
                'Test Case Expected Result': 'Expected result for the unit test Case',
                'Test Case Code': 'Place the entire well-structured X++ code for unit test case here. Strictly Do not start with **`**. Ensure that the code formatting is maintained with proper line breaks.'
              }],
              'All Tables': [
                    {
                    'Table Name': 'Table Name',
                    'Table Purpose': 'Table Purpose',
                    'Label': 'Label',
                    'Fields': [
                        {
                        'Field Name': 'Field Name',
                        'Data Type': 'Data types should be strictly related to X++ language.',
                        'Allow Edit': 'Yes/No',
                        'Allow Edit On Create': 'Yes/No',
                        'Mandatory': 'Yes/No'
                        }
                    ]
                    }
                ]
            }
