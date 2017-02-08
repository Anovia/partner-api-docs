## Lead Webhook Samples

Example Lead Status Webhook Registration Request:

    {
      data: {
        type: 'webhooks',
        attributes: {
          model: 'leads',
          event: 'status',
          uri: 'https://webhooks.yourdomain.com/anoviaLeadStatus',
          value: 'New'
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
          model: 'leads',
          event: 'status',
          uri: 'https://webhooks.yourdomain.com/anoviaLeadStatus',
          value: 'New'
        }
      }
    }

Example Lead Status Webhook Message:

    {
      meta: {
        token: 'RAM2342JEIOFEDK2393'
      },
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
          channelCode: 'SOMECHANNEL',
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
        }
      },
      jsonapi: {
        version: '1.0'
      }
    }