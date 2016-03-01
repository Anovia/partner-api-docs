# partner-api-docs v0.1.0
Documentation for Anovia's REST API for partner lead submission, on boarding, and reporting.

---
## Index

[Introduction](#introduction)

[Versioning](#versioning)

[Terms](#glossary-of-terms)

[Authentication](#authentication)

[Endpoints](#endpoints)

- [Leads](#leads)
- [Merchants](#merchants)
- [Residuals](#residuals)
- [Statements](#statements)
- [Fees](#fees)
- [Deposits](#deposits)
- [Batches](#batches)
- [Transactions](#transactions)

[Query Syntax](#query-syntax)

---
## Introduction

Anovia's Partner API is designed to provide a simple way for parters to access data surrounding their merchant portfolio. The target audience for this document is technical teams of partners who want to consume merchant processing data for use in their own applications.

### Production Environment

Accessing your data is done via a partner specific subdomain on anoviapayments.com. Your relationship manager will provide you with your subdomain prior to launch.

    <partnerName>.anoviapayments.com/api/v1

### Development Environment

For development, we also provide a developer sandbox that can be accessed with your dev API keypair. Please see the section on Authentication for more details.

    apiTest.anoviapayments.com/api/v1

---
## Versioning

The Anovia Partner API uses [semantic versioning](http://semver.org).

Version numbers can be interpreted as MAJOR.MINOR.PATCH. 

- MAJOR version is incremented for incompatible API changes.
- MINOR version is incremented for additional functionality or fields that are backwards-compatible. __NOTE:__ The introduction of new fields is regarded as a backwards-compatible change.
- PATCH version is incremented for backwards-compatible bug fixes.

---
## Glossary of Terms

__Lead__: A referral submitted to Anovia by a partner. A lead is still in the sales cycle, and is being actively worked by Anovia Sales. A lead can have multiple merchants.

__Merchant__: A merchant record is created when a business owner signs a merchant services agreement.

__Residual__: A monthly summary of the revenue a merchant generates for you, the partner.

__Statement__: A monthly summary of a merchant's fees, deposits, and batches.

__Fee__: A monthly amount charged for a particular transaction type, product, or service.

__Deposit__: A daily summary of funds depositied to a merchant's bank account for their processing revenue.

__Batch__: A group of authorized transactions that are submitted to the acquirer for settlement. Batches are usually settled daily, but this can also occur multiple times in a day, or every few days.

__Transaction__: A settled transaction between cardholder and merchant. Please note that only transactions that have been settled in a batch are reported at this time.

---
## Authentication

> NOTE: For security purposes, all requests to Anovia's API must be made via HTTPS

When you sign up with Anovia, you will be provided an API keypair for our TEST environment. This keypair consists of an apiKeyId and apiKeySecret. Upon completing your integration, you will be provided with a new keypair for our PRODUCTION environment.

Authentication to the Anovia Partner API is performed in one of two ways:

### Basic Auth

Create an Basic Authorization header, whose value is your API keypair, separated by a colon.

    Authorization: Basic <Base64UrlSafe(apiKeyId:apiKeySecret)>

### Bearer Auth

For an additional layer of security, you can also use tokens via a Bearer Authorization header. To receive a token, POST to {{host}}/oauth/token, with a Basic Auth header set as described above. You will receive a token that can be used in a Bearer Auth header:

    Authorization: Bearer <token>

Tokens are valid for 1 hour. If you make a request with an invalid token, you will receive a 401 response, at which point you would use your API key to request a new token.

To test authentication, you can submit a GET request to {{host}}/api. If you receive a 200 reponse code, you're doing it right.

---
## Endpoints

A list of available endpoints, and schemas for all available objects is provided here.

For sample requests, responses, and code, please check out our Postman collection:

[![Run in Postman](https://run.pstmn.io/button.png)](https://www.getpostman.com/run-collection/7b0ee3c91ba0a64a8c59)

## Leads

### Lead Routes

    GET /leads

    GET /leads/:id

    GET /leads/:id/merchants


### Lead Schema

Name                                    |Type                |Allow Null  |Required    |Description
----------------------------------------|--------------------|------------|------------|-------------
id                                      |string(13)          |False       |-           |Anovia's unique identifier for lead records
externalId                              |string(36)          |True        |False       |Your own unique identifier for leads submitted to Anovia
businessName                            |string(140)         |True        |True        |The name of the business you are referring
contactName                             |string(140)         |True        |True        |The first and last name of the decision maker for the business
contactPhone                            |string(10)          |True        |True        |The decision maker's phone number
contactPhoneExtension                   |string(8)           |True        |True        |The decision maker's phone number extension
contactEmail                            |string(140)         |True        |True        |The decision maker's email address
bestTimeToContact                       |string(255)         |True        |False       |The best time and/or day to contact the decision maker
submitterId                             |string(36)          |True        |False       |An id you provide for tracking your agent/employee who submitted the lead 
submitterName                           |string(140)         |True        |False       |The name of the agent/employee who submitted the lead
submitterPhone                          |string(10)          |True        |False       |The phone number of the agent/employee who submitted the lead
submitterPhoneExtension                 |string(8)           |True        |False       |The phone number extension of the agent/employee who submitted the lead
submitterEmail                          |string(140)         |True        |False       |The email address of the agent/employee who submitted the lead
channel                                 |string(36)          |False       |True        |The sales channel to which this lead should be added. Possible values will be provided to you by your relationship manager
tags                                    |array(tags)         |True        |False       |Any tags you wish to add to a lead for reporting purposes
status                                  |string(36)          |True        |-           |The current status of the lead (see below for values)
receivedDate                            |date                |False       |-           |(YYYY-MM-DD) The date the lead was received 
signedDate                              |date                |True        |-           |(YYYY-MM-DD) The date the lead signed their processing agreement
lostDate                                |date                |True        |-           |(YYYY-MM-DD) THe date the lead was lost (hopefully this is null!)

## Lead Statuses

A lead's status should give you a general idea of where the lead is in the sales process. Please keep in mind that the status of a lead is fluid. 
It can change rapidly, and changes are not always linear. For instance, a lead can be statused as 'Lost', and then return to 'Contact in Progress'
should the merchant call back and wish to resume the sales process. For this reason, we do not recommend driving automation in your systems based on 
the status of a lead.

Status                |Description
----------------------|-------------
New                   |Lead received, not yet touched
Qualifying            |First contact attempted
Contact in Progress   |Decision maker reached via phone or email
Proposal Presented    |A pricing proposal has been provided to the merchant
Waiting for Info      |Anovia Account Executive is waiting on additional information from the merchant
Out for Signature     |A processing agreemtn has been provided to the merchant and is awaiting their signature
Signed                |The merchant has signed the agreement, deal has been submitted to underwriting
Lost                  |The merchant has decided not to process payments with Anovia


## Merchants

### Merchant Routes

    GET /merchants

    GET /merchants/:id

    GET /merchants/:id/residuals

    GET /merchants/:id/statements

    GET /merchants/:id/fees

    GET /merchants/:id/deposits

    GET /merchants/:id/batches

    GET /merchants/:id/transactions

### Merchant Schema

Name                                    |Type                |Allow Null  |Description
----------------------------------------|--------------------|------------|-------------
id                                      |string(13)          |False       |Anovia's unique identifier for merchant records
mid                                     |string(20)          |True        |Processing platform's ID for this merchant account
lead                                    |string(13)          |False       |The id of the related lead
processor                               |string(20)          |True        |The name of the processing platform
externalId                              |string(36)          |True        |The identifier provided when you submitted the related Lead
dbaName                                 |string(140)         |True        |The Doing Business As name for this merchant
submitterId                             |string(36)          |True        |The id you provided for tracking your agent/employee who submitted the merchant's Lead 
countryCode                             |string(2)           |False       |The two letter country code where the merchant transacts business
channel                                 |string(36)          |False       |The sales channel you provided for the merchant's Lead
tags                                    |array(tags)         |True        |Any tags you provided when creating the merchant's Lead
status                                  |string(36)          |True        |The current status of the merchant (see below for values)
createdDate                             |date                |False       |(YYYY-MM-DD) The date the merchant record was created 
signedDate                              |date                |True        |(YYYY-MM-DD) The date the merchant's application was signed
approvedDate                            |date                |True        |(YYYY-MM-DD) The date the merchant was approved by underwriting
activatedDate                           |date                |True        |(YYYY-MM-DD) The date the merchant processed their first transaction
deactivatedDate                         |date                |True        |(YYYY-MM-DD) The date the merchant closed their account (hopefully this is null!)

## Merchant Statuses

Status                |Description
----------------------|----------
QA                    |Merchant has signed agreement, data being reviewed for accuracy
Underwriting          |Performing KYC checks, decisioning account
Boarding              |Account approved, configuring platform
Product               |Equipment/Services being configured/shipped/deployed
Active                |Merchant has processed their first transaction
Closed                |Merchant account has been closed and/or deactivated

## Residuals

Residual records are a snapshot of a merchant's processing volume and fees for one month, along with your residual income for that period.

### Residual Routes

    GET /residuals

    GET /residuals/:id

### Residual Schema

Name                                    |Type                |Allow Null  |Description
----------------------------------------|--------------------|------------|-------------
id                                      |string(13)          |False       |Anovia's unique identifier for residual records
mid                                     |string(20)          |False       |Processing platform's ID for this merchant account
merchant                                |string(13)          |False       |The id of the related merchant
processor                               |string(20)          |False       |The name of the processing platform
externalId                              |string(36)          |True        |The identifier provided when you submitted the related Lead
mid                                     |string(20)          |False       |The processor issued merchant id for the merchant
dbaName                                 |string(140)         |False       |The Doing Business As name for this merchant
submitterId                             |string(36)          |True        |The id you provided for tracking your agent/employee who submitted the merchant's Lead
signedVolume                            |decimal             |False       |The monthly volume the merchant projected when they signed their processing agreement
volume                                  |decimal             |False       |The merchant's actual volume for the period
totalFees                               |decimal             |False       |The total fees due from the merchant for the period
collectedFees                           |decimal             |False       |The total fees collected from the merchant for the period
nonProcessingFees                       |decimal             |False       |Fees assessed to the merchant but excluded from residual calculation
adjustments                             |decimal             |False       |Adjustments made to correct errors in prior residual statements
netRevenue                              |decimal             |False       |The net revenue to Anovia from the merchant for the period
residualPercentage                      |decimal             |False       |The percentage of fees that you receive per your referral agreement for this merchant
residual                                |decimal             |False       |Your residual payment from this merchant for this period
channel                                 |string(36)          |False       |The sales channel you provided for the merchant's Lead
period                                  |int(6)              |False       |(YYYYMM) The year and month this residual pertains to

## Statements

### Statement Routes

    GET /statements

    GET /statements/:id

    GET /statements/:id/fees

    GET /statements/:id/deposits

    GET /statements/:id/batches

### Statement Schema

Name                                    |Type                |Allow Null  |Description
----------------------------------------|--------------------|------------|-------------
id                                      |string(13)          |False       |Anovia's unique identifier for statement records
mid                                     |string(20)          |True        |Processing platform's ID for this merchant account
merchant                                |string(13)          |False       |The id of the related merchant
processor                               |string(20)          |False       |The name of the processing platform
startDate                               |date                |False       |(YYYY-MM-DD) The beginning date of the statement period 
endDate                                 |date                |False       |(YYYY-MM-DD) The ending date of the statement period 
totalTransactionAmount                  |decimal             |False       |The sum of transactions in this statement period
totalTransactionCount                   |int                 |False       |The sum of transactions in this statement period
totalFeeAmount                          |decimal             |False       |Total dollar value of fees
totalFeeCount                           |int                 |False       |Total number of fee items 
totalDepositAmount                      |decimal             |False       |The total amount of sales deposited in this statement period
totalDepositCount                       |int                 |False       |The total count of deposits in this statement period
totalBatchAmount                        |decimal             |False       |The total amount of sales batched in this statement period
totalBatchCount                         |int                 |False       |The total count of batches in this statement period
period                                  |int                 |False       |(YYYYMM) The year and month this statement pertains to 

## Fees

### Fee Routes

    GET /fees

    GET /fees/:id

### Fee Schema

Name                                    |Type                |Allow Null  |Description
----------------------------------------|--------------------|------------|-------------
id                                      |string(13)          |False       |Anovia's unique identifier for fee records
mid                                     |string(20)          |False       |Processing platform's ID for this merchant account
merchant                                |string(13)          |False       |The id of the related merchant
statement                               |string(13)          |False       |The id of the related statement
processor                               |string(20)          |False       |The name of the processing platform
feeTitle                                |string(140)         |False       |The title of the specific fee item being assessed, ex. '1099 Fee', 'PCI Non Compliance Fee', 'Visa Assessments'
feeType                                 |string(20)          |False       |Type of fee being assessed, ex. 'Authorization Fees', 'Card Brand Fees', 'Transaction Fees'
transactionAmount                       |decimal             |False       |The dollar amount of the transactions
transactionCount                        |int            	   |False       |The number of transactions in this rate category
rate                                    |decimal             |False       |The dollar value of the rate assessed
rateType                                |string(20)          |False       |The type of rate being assessed, either 'percentage' or 'per item'
paid                                    |decimal             |False       |Value of fees collected
due                                     |decimal             |False       |Dollar value of total fees due
total                                   |decimal             |False       |Net value of fees paid vs. fees due

## Deposits

### Deposit Routes

    GET /deposits

    GET /deposits/:id

    GET /deposits/:id/batches

### Schema

Name                                    |Type                |Allow Null  |Description
----------------------------------------|--------------------|------------|-------------
id                                      |string(13)          |False       |Anovia's unique identifier for deposit records
mid                                     |string(20)          |False       |Processing platform's ID for this merchant account
merchant                                |string(13)          |False       |The id of the related merchant
statement                               |string(13)          |False       |The id of the related statement
processor                               |string(20)          |False       |The name of the processing platform
depositDate                             |date                |False       |(YYYY-MM-DD) Business date the merchant should receive deposit.
routingNumber                           |string(9)           |True        |ABA number of merchants designated depository institution.
accountNumber                           |string(40)          |True        |Settlement account designated by merchant at the depository institution.
depositAmount                           |decimal             |False       |Dollar value of the merchant deposit.

## Batches

### Batch Routes

    GET /batches

    GET /batches/:id

    GET /batches/:id/transactions

### Batch Schema

Name                                    |Type                |Allow Null  |Description
----------------------------------------|--------------------|------------|-------------
id                                      |string(13)          |False       |Anovia's unique identifier for batch records
mid                                     |string(20)          |True        |Processing platform's ID for this merchant account
merchant                                |string(13)          |False       |The id of the related merchant
statement                               |string(13)          |False       |The id of the related statement
processor                               |string(20)          |False       |The name of the processing platform
batchDate                               |date                |False       |(YYYY-MM-DD) Date batch was processed
totalTransactionAmount                  |decimal             |False       |Total value of processed transactions
totalTransactionCount                   |int                 |False       |Number of settled transactions processed in the batch
nonSettledTransactionAmount             |decimal             |True        |The amount of sales that were processed in the batch but not settled
settledTransactionAmount                |decimal             |True        |The amount of sales that were processed in the batch and settled
terminalIdentifier                      |string(20)          |True        |The id of the terminal used to process the transaction 

## Transactions

### Routes

    GET /transactions

    GET /transactions/:id

### Transaction Schema

Name                                    |Type                |Allow Null  |Description
----------------------------------------|--------------------|------------|-------------
id                                      |string(13)          |False       |Anovia's unique identifier for transaction records
mid                                     |string(20)          |True        |Processing platform's ID for this merchant account
merchant                                |string(13)          |False       |The id of the related merchant
batch                                   |string(13)          |False       |The id of the related batch
processor                               |string(20)          |False       |The name of the processing platform
accountNumber                           |string(16)          |False       |The account number associated with the customer's form of payment. For payment card transactions, this will be the masked account number. ex: 123456XXXXXX1234
transactionDate                         |date                |False       |Date the transaction was initiated  
paymentType                             |int                 |False       |Form of payment used by the customer/cardholder 
transactionAmount                       |decimal             |False       |Original transaction amount
authorizationAmount                     |decimal             |False       |Authorized transaction amount
salesTaxAmount                          |decimal             |False       |Dollar value of the sales tax related to transactions
authorizationCode                       |string(6)           |True        |Authorization code assigned to the transaction
terminalIdentifier                      |string(20)          |True        |The id of the terminal used to process the transaction
merchantIdentifier                      |string(128)         |True        |An identifier for this transaction from the merchant's POS system

---
## Query Syntax

The following query params are supported on all routes that return data as a collection of objects:

__page[limit]__ - restrict the number of results returned for a colleciton query. Accepts an integer value.

    Example:
    // return no more than 20 records
    ?page[limit]=20
    
    Default: 20

__page[offset]__ - skip the first X results returned in a collection query. Most helpful when used in conjuction with __sort__. Accepts an integer value.

    Example:
    // skip the first 20 records
    ?page[offset]=20
    
    Default: 0
    
__sort__ - specify field(s) by which to sort a collection query's results. Accepts a comma separated list of field names.

    Examples:
    // sort by ASC status, then ASC name
    ?sort=status,name
    
    // sort by ASC status, then DESC name
    ?sort=status,-name
    
    // sort by DESC status, then DESC name
    ?sort=-status,-name
        
    Default: None
    
__include__ - specify which parent relationships to include in the response. In order to include a related object, the collection you are querying must have a foreign key that relates it to the object you want to include. Accepts a comma separated list of parent relationship names

    Example:
    // return an array of children and include their parent
    /children?include=parent
        
    Default: None
    
__filter[field]__ - specify criteria by which to filter the results of a collection query. Accepts operators ( wrapped in {curly braces} ) and dates, in YYYY-MM-DD format.

    Examples:
    // Date created = 01/15/2016
    ?filter[created]=2016-01-15
    
    // Date modified > 01/15/2016
    ?filter[modified]={gt}2016-01-15
    
    // Date signed >= 01/15/2016
    ?filter[signed]={gte}2016-01-15
    
    // Date approved < 01/15/2016
    ?filter[approved]={lt}2016-01-15
    
    // Date activated <= 01/15/2016
    ?filter[activated]={lte}2016-01-15
    
    // Date deactivated != 01/15/2016
    ?filter[deactivated]={ne}2016-01-15
    
    // Date signed between 01/01/2016 and 02/01/2016
    ?filter[signed]={gte}2016-01-01,{lt}2016-02-01
        
    Default: None
    

&copy; 2015 - Anovia Payments. All rights reserved. 
