# TradingAppStoreExamples-TradeStation
## Description
TradingApp.Store offers a comprehensive software suite enabling vendors to verify user permissions for their products. The suite comprises digitally signed Dynamic Link Libraries (DLLs) containing API functions accessible via EasyLanguage scripts integrated into your software.

## Setup
Go to vendors.tradingapp.store, create a vendor account, and click the 'Create Listing' button at the top right. Procede to fill out the Product Listing Form:

Basic Information:

Product Name
Fill out a Product Name at the top left and a SKU will be automatically produced at the bottom of the page (this will identify your product on our servers). 

Product Description
Fill out a product description that fully explains your product, benefits, and how it works.  Give as much detail as possible.  


Specifications

Listing Types:
Check all that apply to this product.  Your product may fit into more than one Listing Type.  

Subscription Options
Choose the type of billing scheme to use. (Such as Lifetime, Annual, Monthly, Free Trial + Monthly, etc)

Upload Software Here:
Skip this step temporarily because you first need to integrate the TAS subscription verification DLL into your custom software by following the directions below.  This will be the final step before deployment.


Post-Purchase Details

Webhook Link:
If you have a realtime listening application that works with Webhooks, paste the link to it here to be notified when a purchase for this product is made.

Purchase Email:
If you would like email notifications upon purchases, place the receiving address here.


Software Details

Target Platform:
Choose TradeStation




target platform (TradeStation), and an optional username that will be embedded into the license. This is useful if you'd like to offload machine authorization to a third party, like if you have your own username/password login system that you'd like to use. If no username, put "none". After filling out the details, click "Generate MSI" and proceed to install the downloaded MSI file. You can then launch the TradingApp.Store license manager application to see the generated license.  

![TradingApp.Store License Manager](licensemanager_screenshot.png)  

Your system is now ready for seamless integration with our platform.

## How it works
The DLL will automatically detect a license in the TradingAppStore/licenses folder and then will determine if the user has permission. If the license is expired, or a newer version of the license is required, then the DLL will automatically update the license to contain the new information. Consequently, users need only execute the installer once to access any trading apps or software included with their current or future purchases.
The TradingAppStore DLL also offers a hardware authorization option that only allows a certain number of devices to access one instance of your product. This adds an additional layer of security by preventing copies of a DLL / product from gaining permission.
You may download the installer for TradingAppStore from the vendor portal whenever you are in the process of creating a listing. All licenses created from the vendor portal are tagged with a “Debug” flag, so they will not have any functionality in release mode. Thus, BE SURE TO CHANGE THE DEBUG FLAG TO FALSE AFTER COMPLETION OF TESTING PHASES.

## Implementation
Below is an EasyLanguage implementation that calls the UserHasPermission function of the TradingAppStore DLL:
```pascal
DefineDLLFunc: "C:\ProgramData\TradingAppStore\x86\TASlicense.dll",  int, "UserHasPermission", lpstr, lpstr, bool, bool;
vars:
	string _TS_num("TradeStation-" + "__GET_TradeStation_NUM__"),
	string _productId("__INSERT_PRODUCT_SKU__")
bool debug =  true
bool tasAUTH = false;

print(numToStr(UserHasPermission(_TS_num,_productId, debug, tasAUTH), 0));

```

## DLL Inputs
The DLL must have 4 input values:
* string customerID :   username of the user
* string productID :    SKU of the product to be checked.
* bool debug :          set to True if you are testing to use Debug licenses distributed by the vendor portal. SET TO FALSE FOR RELEASE OR ELSE ANYONE WILL HAVE ACCESS TO YOUR PRODUCT
* bool TASauth :        Enable this to use our system for user authorization via hardware identifiers. Otherwise, you can use another system like Username / Password

## DLL Return Values
The DLL will return various error values based on numerous factors. It is up to your application how to handle them.
```
0 - no error
1 - expired
2 - wrong customerId
3 - cannot use Debug license in Release Mode
4 - invalid productId
5 - Too many user instances. Only for TAS Authorization. Contact support@tradingapp.store
6 - billing attempt not found... likely expired
8 - File Error
9 - other error
```
## Other Notes
If you are planning on using other apps sold from TradingApp.Store, you must first uninstall the vendor installation and delete the TradingAppStore folder located at C:/ProgramData/ . This will insure that there will be no conflict between the license generated whenever you buy a real product and the debug license used for testing.

## Further Help
If you need assistance in implementation, you may email support@tradingapp.store and we will respond as quickly as possible.
