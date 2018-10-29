# BZKY API #
Changelog
Added mining confirmation.
Section 5 is modified.
Added section 6-8.
Added supported currencies and countries (IDR|IDN)


###### Public parameters and error code description


Public parameter(compulsory)

Parameter | Description
---|---
timestamp | Timestamp of request
signature | Data signature for this request
appid | AppID credentials assigned by Bizkey
m | Method used (see each sections for more information)

Definition of signature(parameter mentioned above):

    All GET parameters (except signature) are first sorted ascendingly according to the ASCII, and the key pairs are then concatenated with comma (e.g. key1=value1,key2=value2...)with appsecret appended at the back. The result is then hashed with MD5 to generate the signature)
    
        signature = md5('key1=value1,key2=value2,key3=value3…appsecret');


   
Interface address：

    https://trade-api.bizkey.io/index.php
   

Example of return

    {"status": true, "code":"00000" }
    
Type of method

    POST   
    
Process:
    
    Merchant:
        1.Register an account to obtain user_id
        2.Submit information to register as merchant
    Consumer：
        1.Register an account to obtain user_id
        2.Submit membership information
        
    Note：
    After getting user_id on Bizkey platform, merchant will need to manually link their ID to Bizkey user_id.
    It is optional for consumer to register/create an account 

Others：    
````
1.The settlement will be processed at 0000 GMT+8 everyday.
2.The conversion rate will be based on BOC's OTC rate at 10am everyday.
````


Error code

Error code | Description
---|---
10000 | Missing field (time)
10001 | Timeout (time difference exceeds 30 seconds)
10002 | Missing field (signature)
10003 | The signature is incorrect
10004 | System error
10005 | Missing field (mainly AppID)
10006 | AppID doesn't exist
10007 | The AppID is disabled
10008 | The app's encryption key is empty
10010 | Missing field(Shop category)
40001 | User does not exist
30001 | Failed to create account. (internal error, need to troubleshoot)
40002 | Failed to create a new store entry (internal error)
50001 | Store does not exist
50002 | Payment time is incorrect
50003 | Failed to generate order
50004 | Customer user_id does not exist
60001 | Failed to generate access token
---

#### 1. Interface to create user ID 
Parameter | Description
  ---|---
m | reg
remark | User remark(optional)


- Return example:
```
    {
    	"status": true,
    	"code": 0,
    	"data": {
    		"user_id": "bzky_5b696cccb1b985b696cccb1be7",
    		"eth_wallet": "0x69C8925CD6BD2CA0EFd0a5D426a9a825eC89702b"
    	}
    }
```

#### 2. Interface to create merchants 

Parameter | Mandatory | Description
  ---|---|---
m | Y | createStore
user_id | Y |  User's User_id
store_name | Y  |  Merchant's store name
sellername |  Y |  Real Name
mobile |  Y |  Phone number(no restrictions, under 20 characters)
category_id | Y | Category ID，(Please refer to category list)
country | Y | Country(Abbreviation, please refer the complete list) CHN、USA、SGP
address | Y | Full address
acreage | N | Area of shop (Must be integer, in sqm^2)
lat | N | Latitidue
lng | N | Longitude
idcard_sn | N | National ID number
idcard_image_front | N | Front of ID document (Image upload interface will return the document name)
idcard_image_back | N | Back of ID document (Image upload interface will return the document name)
idcard_image_in_hand | N | Photo with ID held on hand (Image upload interface will return the document name)
photo_shop_front | N | Photo of storefront (Image upload interface will return the document name)
photo_shop_inside | N | Interior (Image upload interface will return the document name)
bankcard_sn | N | Bank card number
bankcard_image | N | Front of bank card (Image upload interface will return the document name)
license_sn | N | Business License Number
license_image | N | Photo of business license (Image upload interface will return the document name)

- Return example
```
    {
    	"status": "true",
    	"code": 0
    }
```

#### 3. Interface to upload photo 

Parameter | Mandatory  | Description
  ---|---|---
  m |Y|uploadImg 	 
fileimage|Y |File type according to HTTP list's FILETYPE(?) ，not encrypted
- Example
```
    {
    	"status": true,
    	"code": 0,
    	"image_name": "2018083117002312323454.jpg"
    }
```


#### 4.Interface to create member

Parameter | Mandatory  | Description
  ---|---|---
m | Y | createMember
user_id | Y |  User's User_id
real_name | Y  |  Real name
mobile | Y |  hone number(no restrictions, under 20 characters)
gender | N |  Gender( 0=Unknown;1=Male;2=Female )
birthday | N | Birth date（yyyy-mm-dd）

- Return example
```
    {
	"status": true,
	"code": 0
}
```



#### 5.Interface to upload order

Parameter | Mandatory  | Description
  ---|---|---
m | Y |  neworder
seller_id | Y |  Seller's user_id
buyer_id | N |  Buyer's user_id
order_sn | Y |  Order ID
order_amount | Y |  Order amount
new_user | N |  Is it new user,[1=Yes,0=No(default)]
out_sales | N |   Is it foreign customer[1=Yes,0=No(default)]
pay_time | Y |  Time of payment(time stamp)
currency_code | Y |  Currency used(USD、CNY)
payment_id | N | Payment method
consignee | N | Consignee
phone | N | Phone number of receiver
goodslist | N | Order Item List (JSON string)

Example and description of goodslist format:
    
    Each goods object must contain 4 properties:
    bar_code,goods_name,price,quantity

    [
        {
            "bar_code": "332457424641",
            "goods_name": "Cookie500Gram",
            "price": "15.5",
            "quantity": 2
        },
        {
            "bar_code": "323374553243",
            "goods_name": "LiptonGreenTea",
            "price": "3.5",
            "quantity": 1
        }
    ]

- Return Example
```
    {
	"status": true,
	"code": 0
	```
"data":{
            "confirm_url":"https://trade-api.bizkey.io/index.php?c=page&m=confirm_order&order_sn=5bb0e57a2b07a2b0235bb0e57a2b"
```
}

confirm_url is the link to confirm the order, it needs to be pushed to user for mining to be completed. 
```


#### 6.Generating token for accessing H5(website) wallet

Parameter | Mandatory  | Description
  ---|---|---
m |Y |authToken
 user_id| Y | User's User_id (the one generated in 1.)

- Return Example
```
    {
	"status": true,
	"code": 0,
	"data": {
		"token": "K$G3DyTDY5&%EPhmgUDc1O!^B5xLB=Za",
		"expire_time": 1534131626,
		"user_id": "bzky_5b5e8797dd0af5b5e8797dd0fc"
	}
}

#### 7. H5(website) Bizkey wallet
Development in progress, stay tuned.

#### 8. Generating token for accessing H5(website) wallet

Parameter | Mandatory  | Description
  ---|---|---
user_id |Y |Merchants' User_id
token| Y | token generated in 6.
appid| Y | Assigned AppId.


```
Section 5 (Upload New Order interface) returns the Confirm_url. Concatenate it with merchant's user_id, token, appID and let the merchant to confirm the transaction to initiate the mining.

It is recommended to redirect to your own address to get the token before using http302 to redirect to the mining confirmation page.




# Appendix：

 #### Currency code

Currency | Code
-- | --
Canadian Dollar | CAD
Chinese Yuan | CNY
Japanese Yen | JPY
Korean Won | KRW
Malaysian Ringgit| MYR
Singapore Dollar | SGD
Thai Bhat | THB
United States Dollar | USD
Indonesian Rupiah | IDR


 #### Payment method

payment id | Payment method
-- | --
1 | Wechat
2 | Alipay
3 | Paypal
4 | BankCard
5 | Cash
6 | BTC
7 | ETH
8 | BZKY
9 | Others

#### Country ID

ID | Country
-- | --
CAN | Canada
CHN | China
JPN | Japan
KOR | Korea
MAS | Malaysia
SGP | Singapore
THA | Thailand
USA | United States of America
IDN | Indonesia


#### Shop Category

Category ID | Category
-- | --
1 | Food and beverage
2 | Massage & SPA
3 | Supermarket / Departmental store
4 | Pet services
5 | Apparel (Clothes, shoes, bags)
6 | Household cleaning
7 | Education and training
8 | Hair/nail saloon
9 | Beauty/Makeup Saloon
10 | Mom and baby essentials
48 | Car Service
49 | Photography services
50 | Digital Home Appliances
51 | Home maintenance
52 | Fresh fruit
53 | Laundry service
54 | Flower/Wedding Planning
55 | Snacks
56 | Tobacco, wine, sugar, tea
57 | Medical services
58 | Videography/Books
59 | Sports and Fitness
60 | Jewellery/ Accessories
61 | Leisure and entertainment
62 | Courier /Errand Services
