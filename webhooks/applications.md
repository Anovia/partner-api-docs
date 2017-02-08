## Application Webhook Samples

Example Application Status Webhook Registration Request:

    {
      data: {
        type: 'webhooks',
        attributes: {
          model: 'applications',
          event: 'status',
          uri: 'https://webhooks.yourdomain.com/anoviaApplicationStatus',
          value: 'New'
        }
      }
    }

Example Application Status Webhook Registration Response:

    {
      data: {
        id: 'LLKFWIJLKDF2342039',
        type: 'webhooks',
        attributes: {
          token: 'RAM2342JEIOFEDK2393',
          model: 'applications',
          event: 'status',
          uri: 'https://webhooks.yourdomain.com/anoviaApplicationStatus',
          value: 'New'
        }
      }
    }

Example Application Status Webhook Message:

    {
      meta: {
        token: 'RAM2342JEIOFEDK2393'
      },
      data: {
        id: '3NQKBQ',
        type: 'applications',
        attributes: {
          "lead": "2YWF",
          "legalName": "Contact Name",
          "processorName": "TSYS",
          "federalTaxId": "5551231234",
          "ownershipType": "Sole Proprietor",
          "acceptTermsAndConditions": true,
          "externalIdentifier": "1234567",
          "submitterIdentifier": "JOE IBO",
          "countryCode": "US",
          "channelCode": "ODASI",
          "status": "New",
          "signedDate": null,
          "approvedDate": null,
          "declinedDate": null,
          "withdrawnDate": null,
          "tags": {
            "key1": "some value",
            "key2": "another value",
            "key3": "yet another value",
            "organization_id": "testOrgId"
          },
          "legalAddress": {
            "address1": "123 Main St",
            "address2": "Apt 100",
            "city": "Irving",
            "state": "TX",
            "zip": "75039",
            "countryCode": "US"
          },
          "applicants": [
            {
              "firstName": "Charlie",
              "lastName": "Berard",
              "ownershipPercentage": 100,
              "dob": "1980-07-28",
              "ssn": "123123123",
              "email": "charlieb@anoviapayments.com",
              "phone": "5551231231",
              "homeAddress": {
                "address1": "123 Main St",
                "address2": "Apt 100",
                "city": "Irving",
                "state": "TX",
                "zip": "75039",
                "countryCode": "US"
              }
            }
          ],
          "bankAccounts": [
            {
              "bankName": "Bank of America",
              "routingNumber": "123123123",
              "accountNumber": "321321321",
              "accountType": "Checking",
              "accountName": "Testy McTesterson"
            }
          ],
          "locations": [
            {
              "dbaName": "SOME DBA",
              "dbaAddress": {
                "address1": "123 Main St",
                "address2": "Apt 100",
                "city": "Irving",
                "state": "TX",
                "zip": "75039",
                "countryCode": "US"
              },
              "products": []
            }
          ],
          "attachments": [],
          "modifiedDate": "2017-02-08T18:16:06.712Z",
          "createdDate": "2017-02-08T18:16:06.712Z"
        }
      },
      jsonapi: {
        version: '1.0'
      }
    }