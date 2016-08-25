# partner-api-docs v0.8.0
Documentation for Anovia's REST API for partner lead submission, on boarding, and reporting.

---
## Index

[Introduction](#introduction)

[Versioning](#versioning)

[Terms](#glossary-of-terms)

[Authentication](#authentication)

[API Samples](#api-samples)

[Errors](#errors)

[Schemas](#schemas)

- [Leads](#leads)
- [Merchants](#merchants)
- [Residuals](#residuals)
- [Statements](#statements)
- [Fees](#fees)
- [Deposits](#deposits)
- [Deposit Totals](#deposit-totals)
- [Batches](#batches)
- [Transactions](#transactions)

[Sub-Doc Schemas](#sub-doc-schemas)

- [Product Schema](#product-schema)
- [Bank Account Schema](#bank-account-schema)
- [Principal Schema](#principal-schema)
- [Address Schema](#address-schema)

[Webhooks](#webhooks)

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
- MINOR version is incremented for additional functionality or fields that are backwards-compatible.
- PATCH version is incremented for backwards-compatible bug fixes.

> NOTE: The introduction of new fields is regarded as a backwards-compatible change, and thus only requires incrementing the MINOR version.

---
## Glossary of Terms

__Lead__: A referral submitted to Anovia by a partner. A lead is still in the sales cycle, and is being actively worked by Anovia Sales. A lead can have multiple merchants.

__Merchant__: A merchant record is created when a business owner signs a merchant services agreement.

__Residual__: A monthly summary of the revenue a merchant generates for you, the partner.

__Statement__: A monthly summary of a merchant's fees, deposits, and batches.

__Fee__: A monthly amount charged for a particular transaction type, product, or service.

__Deposit__: A daily summary of funds deposited to a merchant's bank account for their processing revenue.

__Deposit Totals__: A daily breakdown of where funds for a particular deposit were routed. 

__Batch__: A group of authorized transactions that are submitted to the acquirer for settlement. Batches are usually settled daily, but this can also occur multiple times in a day, or every few days.

__Transaction__: A captured transaction between cardholder and merchant. Please note that only transactions that have been settled in a batch are reported at this time.

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

### API Samples

The Anovia API uses [JSONAPI](http://jsonapi.org) to format responses, as described below. Sample responses for each endpoint, as well as sample code for a variety of languages, are available via Postman.

[![Run in Postman](https://run.pstmn.io/button.svg)](https://app.getpostman.com/run-collection/7b0ee3c91ba0a64a8c59#?env%5BAnovia%20API%20Test.template%5D=W3sia2V5IjoiaG9zdCIsInZhbHVlIjoiIiwidHlwZSI6InRleHQiLCJlbmFibGVkIjp0cnVlfSx7ImtleSI6ImFwaV9rZXlfaWQiLCJ2YWx1ZSI6IiIsInR5cGUiOiJ0ZXh0IiwiZW5hYmxlZCI6dHJ1ZX0seyJrZXkiOiJhcGlfa2V5X3NlY3JldCIsInZhbHVlIjoiIiwidHlwZSI6InRleHQiLCJlbmFibGVkIjp0cnVlfSx7ImtleSI6ImJhc2U2NF9rZXlfcGFpciIsInZhbHVlIjoiIiwidHlwZSI6InRleHQiLCJlbmFibGVkIjp0cnVlfSx7ImtleSI6ImFjY2Vzc190b2tlbiIsInZhbHVlIjoiIiwidHlwZSI6InRleHQiLCJlbmFibGVkIjp0cnVlfSx7ImtleSI6ImxlYWRfaWQiLCJ2YWx1ZSI6IiIsInR5cGUiOiJ0ZXh0IiwiZW5hYmxlZCI6dHJ1ZX0seyJrZXkiOiJtZXJjaGFudF9pZCIsInZhbHVlIjoiIiwidHlwZSI6InRleHQiLCJlbmFibGVkIjp0cnVlfSx7ImtleSI6InJlc2lkdWFsX2lkIiwidmFsdWUiOiIiLCJ0eXBlIjoidGV4dCIsImVuYWJsZWQiOnRydWV9LHsia2V5Ijoic3RhdGVtZW50X2lkIiwidmFsdWUiOiIiLCJ0eXBlIjoidGV4dCIsImVuYWJsZWQiOnRydWV9LHsia2V5IjoiZGVwb3NpdF9pZCIsInZhbHVlIjoiIiwidHlwZSI6InRleHQiLCJlbmFibGVkIjp0cnVlfSx7ImtleSI6ImZlZV9pZCIsInZhbHVlIjoiIiwidHlwZSI6InRleHQiLCJlbmFibGVkIjp0cnVlfSx7ImtleSI6ImJhdGNoX2lkIiwidmFsdWUiOiIiLCJ0eXBlIjoidGV4dCIsImVuYWJsZWQiOnRydWV9LHsia2V5IjoidHJhbnNhY3Rpb25faWQiLCJ2YWx1ZSI6IiIsInR5cGUiOiJ0ZXh0IiwiZW5hYmxlZCI6dHJ1ZX1d)



Request an object by id:

    Request: GET /api/v1/merchants/123abc456def7
    
    Response:
    {
      "data": {                               // for individual resources fetched by :id, the top-level data element is an object
        "type": "merchants",                  // the type of object being returned
        "id": "123abc456def7",                // the :id of the object
        "attributes": {                       // all data fields for the object are placed in 'attributes'
          "mid": "8546123412341234",
          "dbaName": "Charlie's Chocolates"
          ...
        }
      },
      "links": {
        "self": "/api/v1/merchants/123abc456def7"
      }
    }
    
Request a collection of objects
    
    Request: GET /api/v1/merchants/
    
    Response:
    {
      "data": [                               // for collections of resources, the top-level data element is an array of objects
        {
          "type": "merchants",
          "id": "123abc456def7",
          "attributes": {
            "mid": "8546123412341234",
            "dbaName": "Charlie's Chocolates"
            ...
          }
        }, {
          "type": "merchants",
          "id": "456def789ghi0",
          "attributes": {
            "mid": "8546567856785678",
            "dbaName": "Joe's Diner"
            ...
          }
        }
      ],
      "links": {
        "self": "/api/v1/merchants/?include=page[limit]=100&page[offset]=0",     // the current query
        "first": "/api/v1/merchants/?include=page[limit]=100&page[offset]=0",    // the first page
        "last": "/api/v1/merchants/?include=page[limit]=100&page[offset]=100",   // the last page
        "prev": null,                                                            // the previous page
        "next": "/api/v1/merchants/?include=page[limit]=100&page[offset]=100"    // the next page
      },
      "meta": {
        "count": 115,      // the total number of records matching your query
        "limit": 100,      // the max number of records that could be returned
        "offset": 0        // the number of records skipped
      }
    }

Use the 'include' query param to retreive related parent records. Related resources will be returned in the top-level 'included' element.
A 'relationships' element will be added to the resource object so that you can link the two.

    Request: GET /api/v1/merchants/123abc456def7?include=lead
    
    Response:
    
    {
      "data": {
        "type": "merchants",
        "id": "123abc456def7",
        "attributes": {
          "mid": "8546123412341234",
          "dbaName": "Charlie's Chocolates"
          ...
        },
        "relationships": {                    // the resource now contains a 'relationships' attribute
          "lead": {                           // the name of the relationship will match the value of your 'include' query param
            "data": {
              "type": "leads",                // relationships are linked to included records
              "id": "789ghi012jkl3"           // by unique combination of 'type' & 'id'
            }
          }
        }
      },
      "included": [                           // included is always an array
        {
          "type": "leads",
          "id": "789ghi012jkl3",
          "attributes": {
            "externalIdentifier": "your-custom-identifier",
            "businessName": "Chuck's Chocs"
            ...
          }
        }
      ],
      "links": {
        "self": "/api/v1/merchants/?include=lead&page[limit]=100&page[offset]=0",     // the current query
        "first": "/api/v1/merchants/?include=lead&page[limit]=100&page[offset]=0",    // the first page
        "last": "/api/v1/merchants/?include=lead&page[limit]=100&page[offset]=100",   // the last page
        "prev": null,                                                                 // the previous page
        "next": "/api/v1/merchants/?include=lead&page[limit]=100&page[offset]=100"    // the next page
      },
      "meta": {
        "count": 115,      // the total number of records matching your query
        "limit": 100,      // the max number of records that could be returned
        "offset": 0        // the number of records skipped
      }
    }

## Errors

Errors also adhere to the JSONAPI spec. Errors are returned as an array of JSON objects.

    Request: /api/v1/merchantsss
    
    HTTP Status Code: 404
    Response Body:
    {
      "errors": [
        {
          "status": "404",
          "title": "Not Found"
        }
      ]
    }

The following HTTP Status Codes are utilized by the Anovia API:

Status Code      |Description                  |Details
-----------------|-----------------------------|-------------
200              |OK                           |Successful request
201              |Created                      |Record created
400              |Bad Request                  |Error parsing request
401              |Unauthorized                 |Error authenticating with your API credentials
403              |Forbidden                    |You do not have permission for this resource
404              |Not Found                    |Resource not found
422              |Unprocessable Entity         |Request well-formed, but errors in syntax (check query string)
500              |Internal Server Error        |Oops, we broke something
501              |Not Implemented              |Endpoint exists, but is not yet implemented

## Schemas

## Leads

### Lead Routes

Method  |Route                                  |Description
--------|---------------------------------------|-------------
POST    |/leads                                 |Create a new lead
GET     |/leads                                 |Returns a collection of leads
GET     |/leads/:id                             |Returns a lead by it's id
GET     |/leads/:id/tags                        |Returns a lead's tags object
POST    |/leads/:id/tags                        |Add tags to an existing lead. Tags with new keys will be added, tags with existing keys will be ignored.
PUT     |/leads/:id/tags                        |Update a lead's tags. <br> **NOTE** PUT'ing tags will replace all tags for a lead, so when using PUT, send ALL tags that should exist for that lead. <br> All attributes outside of tags object will be ignored. When using PUT, existing tags will be updated, new tags will be added. Updates to merchant/lead tags are distinct operations, so if you want to update a lead's tags and it's merchants, you would need to make those calls separately.
GET     |/leads/:id/merchants                   |Returns all merchants for a specific lead

### Lead Schema

Name                                    |Type                |Allow Null  |Required    |Description
----------------------------------------|--------------------|------------|------------|-------------
id                                      |string(13)          |False       |-           |Anovia's unique identifier for lead records
externalIdentifier                      |string(36)          |True        |False       |Your own unique identifier for leads submitted to Anovia
businessName                            |string(140)         |True        |True        |The name of the business you are referring
contactName                             |string(140)         |True        |True        |The first and last name of the decision maker for the business
contactPhone                            |string(10)          |True        |True        |The decision maker's phone number
contactPhoneExtension                   |string(8)           |True        |False       |The decision maker's phone number extension
contactEmail                            |string(140)         |True        |True        |The decision maker's email address
bestTimeToContact                       |string(255)         |True        |False       |The best time and/or day to contact the decision maker
submitterIdentifier                     |string(36)          |True        |False       |An id you provide for tracking your agent/employee who submitted the lead 
submitterName                           |string(140)         |True        |False       |The name of the agent/employee who submitted the lead
submitterPhone                          |string(10)          |True        |False       |The phone number of the agent/employee who submitted the lead
submitterPhoneExtension                 |string(8)           |True        |False       |The phone number extension of the agent/employee who submitted the lead
submitterEmail                          |string(140)         |True        |False       |The email address of the agent/employee who submitted the lead
channelName                             |string(36)          |False       |True        |The sales channel to which this lead should be added. Possible values will be provided to you by your relationship manager
tags                                    |object              |True        |False       |JSON object containing key:value pairs for customizing your reporting. Tags will be copied to related merchants ONLY on creation of merchant record.
tags.key                                |string(36)          |True        |False       |Key by which a tag can be referenced. Keys must be unique.
tags.value                              |string(36)          |True        |False       |Value of an individual tag
status                                  |string(36)          |True        |-           |The current status of the lead (see below for values)
receivedDate                            |date                |False       |-           |(YYYY-MM-DD) The date the lead was received 
signedDate                              |date                |True        |-           |(YYYY-MM-DD) The date the lead signed their processing agreement
lostDate                                |date                |True        |-           |(YYYY-MM-DD) THe date the lead was lost (hopefully this is null!)
countryCode                             |string(2)           |False       |True        |The country the business is located in. <br>Values: <br>US<br>CA

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
Out for Signature     |A processing agreement has been provided to the merchant and is awaiting their signature
Signed                |The merchant has signed the agreement, deal has been submitted to underwriting
Lost                  |The merchant has decided not to process payments with Anovia
eSig Exceptions       |A processing agreement was sent to the merchant for signature, but there is currently an error during the signing process


## Merchants

> Note: creating new merchants is reserved for parters on our Payment Facilitation platform. Unlike traditional merchant accounts, these accounts (elsewhere referred to as 'sub-merchants',
have pre-determined pricing and product configuration, as well as an executed agreement.)

### Merchant Routes

Method  |Route                                  |Description
--------|---------------------------------------|-------------
POST    |/merchants                             |Create a new merchant, for ProPay sub-merchants only.
GET     |/merchants                             |Returns a collection of merchants
GET     |/merchants/:id                         |Returns a merchant by it's id
GET     |/merchants/:id/tags                    |Returns an merchant's tags object
POST    |/merchants/:id/tags                    |Add tags to an existing merchant. Tags with new keys will be added, tags with existing keys will be ignored.
PUT     |/merchants/:id/tags                    |Update a merchant's tags. <br> **NOTE** PUT'ing tags will replace all tags for a merchant, so when using PUT, send ALL tags that should exist for that merchant. <br> All attributes outside of tags object will be ignored. When using PUT, existing tags will be updated, new tags will be added. Updates to merchant/lead tags are distinct operations, so if you want to update a lead's tags and it's merchants, you would need to make those calls separately.
GET     |/merchants/:id/residuals               |Returns all residuals for a specific merchant
GET     |/merchants/:id/statements              |Returns all statements for a specific merchant
GET     |/merchants/:id/fees                    |Returns all fees for a specific merchant
GET     |/merchants/:id/deposits                |Returns all deposits for a specific merchant
GET     |/merchants/:id/batches                 |Returns all batches for a specific merchant
GET     |/merchants/:id/transactions            |Returns all transactions for a specific merchant

### Merchant Schema

Name                                    |Type                |Allow Null  |Required     |Description
----------------------------------------|--------------------|------------|-------------|------------
id                                      |string(13)          |False       |False        |Anovia's unique identifier for merchant records
mid                                     |string(20)          |True        |False        |Processing platform's ID for this merchant account
lead                                    |string(13)          |False       |False        |The id of the related lead
dbaName                                 |string(140)         |False       |True         |The Doing Business As name for this merchant
legalName                               |string(140)         |False       |True         |The name of the legal entity for this business, as it appears on tax filing documents
federalTaxId                            |string(9)           |False       |True         |The Federal Tax ID for this business. If ownership type is Sole Proprietor, this will be the Sole Proprietor's Social Security Number
ownershipType                           |string(40)          |False       |True         |The ownership type that describes how the business is registred <br/> Values: <br> Sole Proprietor <br>
acceptTermsAndConditions                |bool                |False       |True         |This value must be set to true, or merchant cannot be added. During the process of signing up for an account, a merchant must explicitly accept the Terms & Conditions of their processing agreement
processorName                           |string(20)          |True        |True         |The name of the processing platform (Either 'TSYS' or 'ProPay')
externalIdentifier                      |string(36)          |True        |False        |The identifier provided when you submitted the related Lead
submitterIdentifier                     |string(36)          |True        |False        |The id you provided for tracking your agent/employee who submitted the merchant's Lead 
countryCode                             |string(2)           |False       |True         |The two letter country code where the merchant transacts business<br>Values: <br>US<br>CA
channelName                             |string(36)          |False       |True         |The sales channel you provided for the merchant's Lead
tags                                    |object              |True        |False        |Object containing key:value pairs for customizing your reporting. Inherited from lead record.
tags.key                                |string(36)          |True        |False        |Key by which a tag can be referenced
tags.value                              |string(36)          |True        |False        |Value of an individual tag
status                                  |string(36)          |True        |False        |The current status of the merchant (see below for values)
createdDate                             |date                |False       |False        |(YYYY-MM-DD) The date the merchant record was created 
signedDate                              |date                |True        |False        |(YYYY-MM-DD) The date the merchant's application was signed
approvedDate                            |date                |True        |False        |(YYYY-MM-DD) The date the merchant was approved by underwriting
boardedDate                             |date                |True        |False        |(YYYY-MM-DD) The date the merchant configuration was sent to the processing platform
activatedDate                           |date                |True        |False        |(YYYY-MM-DD) The date the merchant processed their first transaction
deactivatedDate                         |date                |True        |False        |(YYYY-MM-DD) The date the merchant closed their account (hopefully this is null!)
products                                |array               |True        |False        |List of products the merchant is using for accepting payemnts (terminals, gateways, etc). See Product Schema for details
principals                              |array               |False       |True         |List of owners for the business. See Principal Schema for details
bankAccount                             |bank account object |False       |True         |The bank account where funds will be deposited, and fees will be withdrawn
dbaAddress                              |address object      |False       |True         |The physical address of the business
legalAddress                            |address object      |False       |True         |The registered legal address of the business entity

## Merchant Statuses

Status                |Description
----------------------|----------
QA                    |Merchant has signed agreement, data being reviewed for accuracy
Underwriting          |Performing KYC checks, decisioning account
Boarding              |Account approved, configuring platform
Product               |Equipment/Services being configured/shipped/deployed
Install               |Contacting merchant to confirm receipt of hardware and run test transaction
Active                |Merchant has processed their first transaction
Closed                |Merchant account has been closed and/or deactivated

## Sub-Doc Schemas

### Product Schema
Name                                    |Type                |Allow Null  |Description
----------------------------------------|--------------------|------------|-------------
name                                    |string(50)          |False       |The name of the product being used, (Ex: 'VX520', 'BridgePay')
identifier                              |string(50)          |True        |Processing platform's ID for this product
configuration                           |object              |True        |Configuration credentials, usually for a gateway or 3rd party product
configuration.username                  |string(50)          |True        |The username to be used in configuration settings
configuration.password                  |string(50)          |True        |The password or other secondary key to be used in configuration settings
configuration.key                       |string(50)          |True        |The API Key or other tertiary key to be used in configuration settings

### Bank Account Schema
Name                                    |Type                |Allow Null  |Required     |Description
----------------------------------------|--------------------|------------|-------------|-------------
bankName                                |string(50)          |True        |True         |The name of the banking institution that issued this bank account
routingNumber                           |string(9)           |True        |True         |The 9 digit routing number
accountNumber                           |object              |True        |True         |The bank account number
accountType                             |string(50)          |True        |True         |Either 'Checking' or 'Savings'
accountName                             |string(50)          |True        |True         |The name of the account holder (either the business name or individual's name that owns the account)

### Principal Schema
Name                                    |Type                |Allow Null  |Required     |Description
----------------------------------------|--------------------|------------|-------------|-------------
firstName                               |string(50)          |True        |True         |The first name of the principal
lastName                                |string(50)          |True        |True         |The last name of the principal
ownershipPercentage                     |int                 |True        |True         |The percentage of the business this principal owns. 60% ownership would be passed as 60
dob                                     |string(10)          |True        |True         |YYYY-MM-DD date of birth
ssn                                     |string(50)          |True        |True         |The social security number of the principal
email                                   |string(50)          |True        |True         |The personal email address of the principal
phone                                   |string(10)          |True        |True         |The home phone number of the principal (numeric only)
homeAddress                             |address object      |True        |True         |The home address of the principal

### Address Schema
Name                                    |Type                |Allow Null  |Required     |Description
----------------------------------------|--------------------|------------|-------------|-------------
address1                                |string(50)          |True        |True         |The street number and street (Ex: 123 Main St.)
address2                                |string(50)          |True        |True         |The suite #, apartment #, etc (Ex: Suite 100 or Apt. #4321)
city                                    |string(50)          |True        |True         |
state                                   |string(50)          |True        |True         |The two letter state/province abbreviation
zip                                     |string(50)          |True        |True         |The postal code
country                                 |string(50)          |True        |True         |The two letter country code (Either 'US' or 'CA')

## Residuals

Residual records are a snapshot of a merchant's processing volume and fees for one month, along with your residual income for that period.

### Residual Routes

Method  |Route                                  |Description
--------|---------------------------------------|-------------
GET     |/residuals                             |Returns a collection of residuals
GET     |/residuals/:id                         |Returns a residual by it's id

### Residual Schema

Name                                    |Type                |Allow Null  |Description
----------------------------------------|--------------------|------------|-------------
id                                      |string(13)          |False       |Anovia's unique identifier for residual records
mid                                     |string(20)          |False       |Processing platform's ID for this merchant account
merchant                                |string(13)          |False       |The id of the related merchant
processorName                           |string(20)          |False       |The name of the processing platform
externalIdentifier                      |string(36)          |True        |The identifier provided when you submitted the related Lead
dbaName                                 |string(140)         |False       |The Doing Business As name for this merchant
submitterIdentifier                     |string(36)          |True        |The id you provided for tracking your agent/employee who submitted the merchant's Lead
count                                   |int                 |False       |The number of items
volume                                  |decimal             |False       |The merchant's volume for the period
expense                                 |decimal             |False       |The total expenses for the period
income                                  |decimal             |False       |The total income for the period
adjustments                             |decimal             |False       |Adjustments made to correct errors in prior residual statements
profit                                  |decimal             |False       |Income minus Expenses
residualAmount                          |decimal             |False       |Net residual amount calculated based on agreed upon residual percentages/amounts
channelName                             |string(36)          |False       |The sales channel you provided for the merchant's Lead
period                                  |int                 |False       |(YYYYMM) The year and month this residual pertains to

## Statements

### Statement Routes

Method  |Route                                  |Description
--------|---------------------------------------|-------------
GET     |/statements                            |Returns a collection of statements
GET     |/statements/:id                        |Returns a statement by it's id
GET     |/statements/:id/fees                   |Returns all fees for a specific statement
GET     |/statements/:id/deposits               |Returns all deposits for a specific statement
GET     |/statements/:id/batches                |Returns all batches for a specific statement

### Statement Schema

Name                                    |Type                |Allow Null  |Description
----------------------------------------|--------------------|------------|-------------
id                                      |string(13)          |False       |Anovia's unique identifier for statement records
mid                                     |string(20)          |True        |Processing platform's ID for this merchant account
merchant                                |string(13)          |False       |The id of the related merchant
processorName                           |string(20)          |False       |The name of the processing platform
startDate                               |date                |False       |(YYYY-MM-DD) The beginning date of the statement period 
endDate                                 |date                |False       |(YYYY-MM-DD) The ending date of the statement period 
totalTransactionAmount                  |decimal             |False       |The sum of transactions in this statement period
totalTransactionCount                   |int                 |False       |The count of transactions in this statement period
totalFeeAmount                          |decimal             |False       |Total dollar value of fees
totalFeeCount                           |int                 |False       |Total number of fee items 
totalDepositAmount                      |decimal             |False       |The total amount of sales deposited in this statement period
totalDepositCount                       |int                 |False       |The total count of deposits in this statement period
totalBatchAmount                        |decimal             |False       |The total amount of sales batched in this statement period
totalBatchCount                         |int                 |False       |The total count of batches in this statement period
period                                  |int                 |False       |(YYYYMM) The year and month this statement pertains to 

## Fees

### Fee Routes

Method  |Route                                  |Description
--------|---------------------------------------|-------------
GET     |/fees                                  |Returns a collection of fees
GET     |/fees/:id                              |Returns a fee by it's id

### Fee Schema

Name                                    |Type                |Allow Null  |Description
----------------------------------------|--------------------|------------|-------------
id                                      |string(13)          |False       |Anovia's unique identifier for fee records
mid                                     |string(20)          |False       |Processing platform's ID for this merchant account
merchant                                |string(13)          |False       |The id of the related merchant
statement                               |string(13)          |False       |The id of the related statement
processorName                           |string(20)          |False       |The name of the processing platform
feeTitle                                |string(140)         |False       |The title of the specific fee item being assessed, ex. '1099 Fee', 'PCI Non Compliance Fee', 'Visa Assessments'
feeCategory                             |string(20)          |False       |Values: <br>Authorization<br>Data Capture<br>Exceptions<br>Debit Network<br>EBT<br>MISC Fees<br>Interchange<br>Individual Plan<br>Discount **<br>Card Brand Fees
feeVolume                               |decimal             |False       |The sum of transactions to which this fee was applied
feeCount                                |int            	   |False       |The number of transactions to which this fee was applied
perItemRate                             |decimal             |True        |The amount of fees assessed per each item in feeCount
percentageRate                          |decimal             |True        |The percentage of the feeVolume that is charged for this fee
feeAmount                               |decimal             |False       |Sum of (feeVolume x percentageRate) + (feeCount x perItemRate)
feePaid                                 |decimal             |False       |Value of fees alread paid (ex: if a merchant's fees are billed daily vs monthly)
feeDue                                  |decimal             |False       |feeAmount - feePaid. This amount will be deducted from the merchant's bank account at month end 
period                                  |int                 |False       |(YYYYMM) The year and month this fee was billed

## Deposits

### Deposit Routes

Method  |Route                                  |Description
--------|---------------------------------------|-------------
GET     |/deposits                              |Returns a collection of deposits
GET     |/deposits/:id                          |Returns a deposit by it's id

### Schema

Name                                    |Type                |Allow Null  |Description
----------------------------------------|--------------------|------------|-------------
id                                      |string(13)          |False       |Anovia's unique identifier for deposit records
mid                                     |string(20)          |False       |Processing platform's ID for this merchant account
merchant                                |string(13)          |False       |The id of the related merchant
statement                               |string(13)          |False       |The id of the related statement
processorName                           |string(20)          |False       |The name of the processing platform
depositDate                             |date                |False       |(YYYY-MM-DD) Business date the merchant should receive deposit.
routingNumber                           |string(9)           |True        |ABA number of merchants designated depository institution.
accountNumber                           |string(40)          |True        |Settlement account designated by merchant at the depository institution.
depositAmount                           |decimal             |False       |Dollar value of the merchant deposit.
nonSettledAmount                        |decimal             |True        |The sum of sales that were processed in this deposit period, but not settled to the merchant's bank account. Example: AMEX ESA, 100% Reserve, MRA
period                                  |int                 |False       |(YYYYMM) The year and month this deposit occurred

## Deposit Totals

### Deposit Total Routes

Method  |Route                                  |Description
--------|---------------------------------------|-------------
GET     |/depositTotals                        |Returns a collection of deposit totals
GET     |/depositTotals/:id                    |Returns a collection of deposit totals for a specific date (YYYY/MM/DD)

### Schema

Name                                    |Type                |Allow Null  |Description
----------------------------------------|--------------------|------------|-------------
id                                      |string(13)          |False       |Anovia's unique identifier for depositTotal records
mid                                     |string(20)          |False       |Processing platform's ID for this merchant account
merchant                                |string(13)          |False       |The id of the related merchant
processorName                           |string(20)          |False       |The name of the processing platform
depositDate                             |date                |False       |Business date the merchant should receive deposit.
netDepositAmount                        |decimal             |False       |Amount after chargebacks, credits, and daily fees are removed but prior to other withheld amounts.
actualDepositAmount                     |decimal             |False       |Amount actually deposited to the merchant account.
settledAmount                           |decimal             |False       |Amount settled/paid to the merchant.
nonSettledAmount                        |decimal             |True        |Amount not paid to the merchant.  Activity in the batch that is not qualified for ACH.
dailyFeeAmount                          |decimal             |True        |Amount of daily fees deducted from the merchant.
disqualifiedAmount                      |decimal             |True        |Amount that initially qualified for ACH during processing, but then determined to not qualify for ACH at the time of ACH run.
suspenseFundingHeldAmount               |decimal             |True        |Amount withheld from the merchant due to suspense funding settings
suspenseFundingReleasedAmount           |decimal             |True        |Amount released back to merchant from suspense funding.
reserveFundingHeldAmount                |decimal             |True        |Amount withheld from the merchant due to reserve funding settings.
reserveFundingReleasedAmount            |decimal             |True        |Amount released back to merchant from reserve funding.
cashAdvanceHeldAmount                   |decimal             |True        |Any amount withheld for cash advance provider processes.
federalBackupWithholdingAmount          |decimal             |True        |Amount withheld for IRS backup withholding.
stateBackupWitholdingAmount             |decimal             |True        |Amount withheld for state backup withholding.

## Batches

### Batch Routes

Method  |Route                                  |Description
--------|---------------------------------------|-------------
GET     |/batches                               |Returns a collection of batches
GET     |/batches/:id                           |Returns a batch by it's id
GET     |/batches/:id/transactions              |Returns all transactions for a specific batch

### Batch Schema

Name                                    |Type                |Allow Null  |Description
----------------------------------------|--------------------|------------|-------------
id                                      |string(13)          |False       |Anovia's unique identifier for batch records
mid                                     |string(20)          |True        |Processing platform's ID for this merchant account
merchant                                |string(13)          |False       |The id of the related merchant
statement                               |string(13)          |False       |The id of the related statement
processorName                           |string(20)          |False       |The name of the processing platform
batchDate                               |date                |False       |(YYYY-MM-DD) Date batch was processed
totalTransactionAmount                  |decimal             |False       |Total value of processed transactions
totalTransactionCount                   |int                 |False       |Number of settled transactions processed in the batch
terminalName                            |string(20)          |True        |A short descriptor of the product being used. Ex: 'VX520', 'BridgePay', 'Auth.net'
terminalIdentifier                      |string(20)          |True        |The id of the terminal used to process the transaction
period                                  |int                 |False       |(YYYYMM) The year and month this batch was processed

## Transactions

### Routes

Method  |Route                                  |Description
--------|---------------------------------------|-------------
GET     |/transactions                          |Returns a collection of transactions
GET     |/transactions/:id                      |Returns a transaction by it's id

### Transaction Schema

Name                                    |Type                |Allow Null  |Description
----------------------------------------|--------------------|------------|-------------
id                                      |string(13)          |False       |Anovia's unique identifier for transaction records
mid                                     |string(20)          |True        |Processing platform's ID for this merchant account
merchant                                |string(13)          |False       |The id of the related merchant
batch                                   |string(13)          |False       |The id of the related batch
processorName                           |string(20)          |False       |The name of the processing platform
accountNumber                           |string(16)          |False       |The account number associated with the customer's form of payment. For payment card transactions, this will be the masked account number. ex: 123456XXXXXX1234
transactionDate                         |date                |False       |(YYYY-MM-DD) Date the transaction was initiated  
paymentType                             |string(20)          |False       |Form of payment used by the customer/cardholder 
transactionAmount                       |decimal             |False       |Captured transaction amount
authorizationAmount                     |decimal             |False       |Authorized transaction amount
authorizationCode                       |string(20)          |True        |Authorization code assigned to the transaction
terminalName                            |string(20)          |True        |A short descriptor of the product being used. Ex: 'VX520', 'BridgePay', 'Auth.net'
terminalIdentifier                      |string(20)          |True        |The id of the terminal used to process the transaction
merchantIdentifier                      |string(128)         |True        |An identifier for this transaction from the merchant's POS system

---
## Webhooks

Anovia's API also provides the option to receive notifications when a lead or merchant's status changes. You can register your webhook endpoints by POST'ing
to the URLs provided below. Webhook messages will be POST'ed to your endpoint, and will contain a JSON body.

Method  |Route                                  |Description
--------|---------------------------------------|-------------
POST    |/webhooks                              |register to receive a new webhook
GET     |/webhooks                              |get a list of all webhooks currently registered for your organization
GET     |/webhooks/:id                          |get a specific webhook by id
POST    |/webhooks/:id                          |update a webhook's uri
POST    |/webhooks/:id/actions/regenerateToken  |generate a new token for the webhooks specified by the id parameter
DELETE  |/webhooks/:id                          |remove a webhook subscription

### Webhook Schema

Name                                    |Type                |Allow Null  |Description
----------------------------------------|--------------------|------------|-------------
id                                      |string(13)          |False       |The unique identifier of the webhook connection
token                                   |string(30)          |False       |A token you can use to verify that the webhook message came from Anovia
hook                                    |string(20)          |False       |The event you are subscribing to. Available Webhooks: <br/> leadStatus - receive a notification when a lead's status changes <br/> merchantStatus - recieve a notification when a merchant's status changes
uri                                     |string(100)         |False       |The fully qualified URI on your server where webhook messages should be sent.

> **NOTE:** For security purposes, all webhook uri's must be HTTPS

Example Lead Status Webhook Registration Request:

    {
      data: {
        type: 'webhooks',
        attributes: {
          hook: 'leadStatus',
          uri: 'https://webhooks.yourdomain.com/anoviaLeadStatus'
        }
      }
    }

Example Lead Status Webhook Registration Response:

    {
      data: {
        id: 'LLKFWIJLKDF2342039',
        type: 'webhooks',
        attributes: {
          token: 'RAM2342JEIOFEDK2393',
          hook: 'leadStatus',
          uri: 'https://webhooks.yourdomain.com/anoviaLeadStatus'
        }
      }
    }

Example Lead Status Webhook Message:

    {
      meta: {
        token: 'RAM2342JEIOFEDK2393'
      },
      links: {
        self: 'api/v1/leads/3NQKBQ'
      }
      data: {
        id: '3NQKBQ',
        type: 'leads',
        attributes: {
          bestTimeToContact: 'mornings',
          businessName: 'Test Merchant Creation 3',
          contactName: 'Contact Name',
          contactPhone: '5551231231',
          contactEmail: 'testme@email.com',
          countryCode: 'US',
          channelName: 'SOMECHANNEL',
          externalIdentifier: 'YOUREXTID',
          receivedDate: '2016-08-21T21:31:20.907Z',
          signedDate: '2016-08-25T21:31:20.907Z',
          lostDate: null,
          status: 'Signed',
          submitterIdentifier: '123901',
          submitterName: 'Russell Westbrook',
          submitterPhone: '5551234567',
          submitterEmail: 'russell@okc.com',
          tags: {
            tag1: 'val1',
            tag2: 'val2',
            tag3: 'val3'
          }
        },
        links: {
          self: 'api/v1/leads/3NQKBQ'
        }
      },
      jsonapi: {
        version: '1.0'
      }
    }

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
    

&copy; 2016 - Anovia Payments. All rights reserved. 
