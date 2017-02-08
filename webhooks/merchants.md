## Merchant Webhook Samples

Example Merchant Status Webhook Registration Request:

    {
      data: {
        type: 'webhooks',
        attributes: {
          model: 'merchants',
          event: 'status',
          uri: 'https://webhooks.yourdomain.com/anoviaMerchantStatus',
          value: 'New'
        }
      }
    }

Example Merchant Status Webhook Registration Response:

    {
      data: {
        id: 'LLKFWIJLKDF2342039',
        type: 'webhooks',
        attributes: {
          token: 'RAM2342JEIOFEDK2393',
          model: 'merchants',
          event: 'status',
          uri: 'https://webhooks.yourdomain.com/anoviaMerchantStatus',
          value: 'New'
        }
      }
    }

Example Merchant Status Webhook Message:

    {
      meta: {
        token: 'RAM2342JEIOFEDK2393'
      },
      data: {
        "type": "merchants",
        "id": "5T5",
        "links": {
          "self": "/api/v1/merchants/5T5"
        },
        "attributes": {
          "mid": "8546123412341234",
          "lead": "270M",
          "application": "349A",
          "dbaName": "SOME TEST BUSINESS",
          "processorName": "TSYS",
          "externalIdentifier": "123123",
          "submitterIdentifier": "Lead Page",
          "countryCode": "US",
          "channelCode": "ABCDE",
          "status": "Active",
          "signedDate": "2016-03-02T15:43:19.000Z",
          "approvedDate": "2016-03-02T19:40:16.000Z",
          "activatedDate": "2016-03-03T00:00:00.000Z",
          "boardedDate": "2016-03-02T21:22:02.000Z",
          "deactivatedDate": null,
          "tags": null,
          "products": [
            {
              "name": "VX 520",
              "identifier": "12345678",
                "configuration": {
                  "username": "someusername",
                  "password": "somepassword",
                  "key": "1234567789"
                }
            }
          ],
          "dbaAddress": {
            "address1": "123 Main St.",
            "address2": null,
            "city": "Austin",
            "state": "TX",
            "zip": "77777",
            "countryCode": "US"
          },
          "legalAddress": null,
          "principals": [
            {
              "firstName": "Charlie",
              "lastName": "Berard",
              "ownershipPercentage": 100,
              "dob": "1980-08-01",
              "ssn": "*****1234",
              "email": "info@somebusiness.com",
              "phone": "5551231234",
              "homeAddress": {
                "address1": "123 Main St.",
                "address2": null,
                "city": "Austin",
                "state": "TX",
                "zip": "77777",
                "countryCode": "US"
              }
            }
          ],
          "bankAccounts": [
            {
              "id": "48V",
              "bankName": "Bank of America",
              "routingNumber": "*****1234",
              "accountNumber": "**********99",
              "accountType": "Checking",
              "accountName": "SOME TEST BUSINESS"
            }
          ],
          "modifiedDate": "2016-12-29T22:55:06.010Z",
          "createdDate": "2016-07-12T09:59:37.633Z",
          "statements": null,
          "residuals": null,
          "fees": null,
          "deposits": null,
          "disbursements": null,
          "batches": null,
          "transactions": null
        }
      },
      jsonapi: {
        version: '1.0'
      }
    }