---
layout: home
title: Omnissa Partner Managed Services Ordering API
hide:
  - navigation
  #- toc
  - path
  - title
---
![Workspace ONE UEM](assets/logos/Omnissa_tm_wordmark_CLR-RGB.png){ align=centre }

## What is the Managed Services Ordering API

Partners will be able to create new orders, increase or decrease subscription amounts, cancel the subscription at the time of renewal during the period of change for their subscription and increase subscription amount through add-ons at any time during the term using Cloud Provider Mobility Order API

!!!note
    This documentation uses Postman for all API calls, other options can be used but examples will be based on usage of Postman. [**Postman is free to download**](https://www.postman.com/downloads/) to your PC or you can use the [**web interface**](https://identity.getpostman.com/login?continue=https%3A%2F%2Fgo.postman.co%2Fhome).


[link to postman collection](MSPOrderingAPI.yaml)

## Onboarding to the Managed Services Ordering API

Once you receive your credentials from your Partner Account team or the Omnissa Operations team you can get started with your first authentication call. This will have to be done at the beginning of every session and times out after 7500 seconds. The same authentication scheme `Authorization: Bearer` is required for all API calls.

### Prerequisite

You should already have:

- app-id (ie shared key)
- secret-id (ie secret key)
- Partner ID
- commit contract (ex GEO00000MOBILITY)

!!!note "Tip"
    Any `{placeholder-text}` are environment variables using Postman's environments. Postman environments can hold your app ids and secret keys and also maintain other variables you want to reuse without having to copy+paste them repeatedly such as your Omnissa Partner ID.

### API Bearer Token Generation

In order to access your environment, use the access tokens generated and provided by Omnissa:

1. Create a new **POST** request with the URL [https://apigw.omnissa.com/v1/m0/application/oauth2/token](https://apigw.omnissa.com/v1/m0/application/oauth2/token)
2. Select `Basic Auth` in the **Authorization** tab then fill the username and password with the keys as shown below:
    - Username: `{ app-id }`
    - Password: `{ app-secret }`
3. In the **Params** tab create a Key/Value pairs with the following values:
    - Key: `grant_type`
    - Value: `client_credentials`
4. Set the `Content-Type` header of this **POST** request to none.
5. Select Send to make the API call, upon success your `access_token` will appear in the `Response` portion of the page.

!!!warning "REQUIRED"
    For any API call other than the authorization call, in the Authorization tab set Type as Bearer Token and put your `access_token` into the Token. API calls will not work without this.

## API Call Walkthrough

### Get Subscriptions

This API call will list out all active subscriptions under the Cloud Provider's organization and the commit contracts attached.

```
  GET: https://apigw.omnissa.com/v2/m4/api/sdp/subscriptions
```

1. Create a new HTTP request and select the **GET** method type
2. Paste the url for **GET** Subscriptions API in the url space [https://apigw.omnissa.com/v2/m4/api/sdp/subscriptions](https://apigw.omnissa.com/v2/m4/api/sdp/subscriptions)
3. In the **Authorization** tab set **Type** as `Bearer Token` and put your `access_token` into the Token
4. Create an entry in the **Header** tab with a Key/Value pair with the following values:
    - Key: `partnerId`
    - Value: `{Omnissa Partner ID}`
5. Press **SEND** to view a list of all active subscriptions under the Cloud Provider's Commit Contract

#### Sample response body

``` json
  {
    "subscriptions": [
        {
            "commitContractReference": "AMER00000MOBILITY",
            "serviceInstanceId": "M000000000",
            "customerName": "Company ABC",
            "startDate": "2025-02-16T08:00:00.000+00:00",
            "endDate": "2026-02-15T08:00:00.000+00:00",
            "offers": [
                {
                    "quantity": 500,
                    "sku": "WSU-ASUPSPSSP-12MT0-C1S"
                }
            ]
        },
        {
            "commitContractReference": "AMER00000OBILITY",
            "serviceInstanceId": "M000000000",
            "customerName": "Company EFG",
            "startDate": "2024-03-14T07:00:00.000+00:00",
            "endDate": "2025-03-05T08:00:00.000+00:00",
            "offers": [
                {
                    "quantity": 250,
                    "sku": "WSD-AWOAP-12PT0-C1S"
                },
                {
                    "quantity": 100,
                    "sku": "WSD-AWOSP-12PT0-C1S"
                }
            ]
        },
        {
            "commitContractReference": "AMER00000MOBILITY",
            "serviceInstanceId": "M000000000",
            "customerName": "Company HIJ",
            "startDate": "2024-02-16T08:00:00.000+00:00",
            "endDate": "2025-02-15T08:00:00.000+00:00",
            "offers": [
                {
                    "quantity": 400,
                    "sku": "WSD-AWOSP-12MT0-C2S"
                }
            ]
        },
        {
            "commitContractReference": "AMER00000MOBILITY",
            "serviceInstanceId": "M000000000",
            "customerName": "Company LMN",
            "startDate": "2023-03-30T07:00:00.000+00:00",
            "endDate": "2024-03-29T07:00:00.000+00:00",
            "offers": [
                {
                    "quantity": 500,
                    "sku": "WSU-AWOAB-12MT0-C1S"
                }
            ]
        }
     ]
  }
```

### New Order

After successful API call, the response body will contain a request id to track the status of the order and eventually retrieve the order's associated SIDs. In case of any error, message and traceId will be displayed in the response.

```
  POST: https://apigw.omnissa.com/v2/m4/api/sdp/subscriptions
```

To place an initial order:

1. Create a new HTTP request and select the **POST** method type and paste the URL
2. In the **Authorization** tab set **Type** as `Bearer Token` and put your `access_token` into the **Token**
3. Create an entry in the **Header** tab with a Key/Value pair with the following values:
    - Key: `partnerId`
    - Value: `{Omnissa Partner ID}`
4. Paste the sample request payload in the **Body** tab with raw and JSON selected. Replace the end customer information accordingly, the API Specifications at the bottom of this guide have more details for which fields are required.
   **Note:** More than one product (both primary, secondary add-ons) can be ordered
5. Press **SEND** to submit your order and receive the request id for your order

#### Sample payload

```json
  {
    "commitContractReference": "AMER00000MOBILITY",
    "endCustomerInfo": {
      "customerName": "Customer",
      "firstName": "Jane",
      "lastName": "Smith",
      "email": "XXXXXXX@XXXX.com",
      "country": "United States",
      "zipCode": "94943",
      "address1": "590 E Middlefield Road",
      "city": "Mountain View",
      "county": "United States",
      "state": "California"
    }
    "offers": [
      {
        "sku": "WSD-AWOSP-12PT0-C1S",
        "quantity": 5
      }
    ],
  }
```

Upon success you will get a `requestId`, the `requestId` can be used to check the status of an order using Order Status API call.

#### Sample success response

```json
  {
    "requestId": "bfa000d0-00ae-00d1-00bb-dac0d0f000ee"
  }
```

If there is a validation error like the one below check that the SKU you are using is correct and attached to your commit contract.

#### Sample error response

```json
  {
    "error": "VALIDATION FAILED",
    "message": "Edition Code is required field and cannot be empty",
    "traceId": ""
  }
```

### Order Status

Successful API call will show either **Pending** or **Success** depending on the status of provisioning based on your request id.

```
  GET: https://apigw.omnissa.com/v2/m4/api/sdp/status/{{requestId}}
```

To make a status call:

1. Create a new HTTP request and select the **GET** method type and paste the URL
2. In the **Authorization** tab set **Type** as `Bearer Token` and put your `access_token` into the Token
3. Create an entry in the **Header** tab with a Key/Value pair with the following values:
    - Key: `partnerId`
    - Value: `{Omnissa Partner ID}`
4. Use the `requestId` generated from a new order as the `requestId` in the API call
5. In the **Body** tab set to **none**
6. Once the call is made you will get one of two statuses **PENDING** or **SUCCESS** shown below. When the status is **SUCCESS** a SID will be listed, use the service instance id (SID) to place other orders like add-on (increase in quantity or new secondary add-on), upgrade, renewal or cancellation

#### Pending status response

```json
  {
    "status": "PENDING"
  }
```
#### Success response

```json
  {
	"serviceInstanceId": "M000000000",
    "status": "SUCCESS"
  }
```

### Get Individual SID Information

This API call will retrieve information for a single SID under the Cloud Provider's organization.

```
  GET: https://apigw.omnissa.com/v2/m4/api/sdp/subscriptions/{{SID}}
```

1. Create a new HTTP request and select the **GET** method type
2. In the **Authorization** tab set **Type** as `Bearer Token` and put your `access_token` into the Token
3. Create an entry in the **Header** tab with a Key/Value pair with the following values:
    - Key: `partnerId`
    - Value: `{Omnissa Partner ID}`
4. In the Body make select the **none** radio button
5. Press SEND to view a list of all active subscriptions under the Cloud Provider's Commit Contract

### Add-On Subscription (Quantity Increase)

This API call enables orders that increase the quantity of current subscriptions, use the service instance id (SID) of the initial order to place the add-on order.

```
  POST: https://apigw.omnissa.com/v2/m4/api/sdp/subscriptions/addon
```

To make a add-on quantity increase call:

1. Create a new HTTP request and select the **GET** method type
2. In the **Authorization** tab set **Type** as `Bearer Token` and put your `access_token` into the Token
3. Create an entry in the **Header** tab with a Key/Value pair with the following values:
    - Key: `partnerId`
    - Value: `{Omnissa Partner ID}`
4. Use the SID generated from a new order as the `serviceInstanceId` in the API call, the Body needs to be set to raw and contain
    - `type: "QUANTITY-INCREASE"`
    - the commit contract attached to your org as `commitContractReference: {commit-contract}`
    - Offers that you would like to increase and the quantity from which you are increasing
5. Once the call is made you will get a requestId to track the order status

#### Sample payload

```json
  {
    "type": "QUANTITY-INCREASE",
    "commitContractReference": "AMER00000MOBILITY",
    "serviceInstanceId":"MXXXXXXXXX", //serviceInstanceId: Provisioned SID of the Initial Order
    "offers": [
        {
            "sku": "WSD-AWOSP-12PT0-C1S",
            "fromQuantity": XX, //fromQuantity: Existing QTY for the SKU.
            "toQuantity": XX //toQuantity: Requested QTY for the SKU.
        }
    ]
  }
```

### Add-On Subscription (SECONDARY ADD-ON)

In order to add secondary add-on to the existing primary use below API. Use the service instance id (SID) of the initial order to place the secondary add-on order.

```
  POST: https://apigw.omnissa.com/v2/m4/api/sdp/subscriptions/addon
```

To make a add-on secondary add-on call:

1. Create a new HTTP request and select the **GET** method type
2. In the **Authorization** tab set **Type** as `Bearer Token` and put your `access_token` into the Token
3. Create an entry in the **Header** tab with a Key/Value pair with the following values:
    - Key: `partnerId`
    - Value: `{Omnissa Partner ID}`
4. Use the SID generated from a new order as the `serviceInstanceId` in the API call, the Body needs to be set to raw and contain
   - `type: "SECONDARY-ADDON"`
   - the commit contract attached to your org as `commitContractReference: {commit-contract}`
   - Offers that you would like to add to your existing subscription
5. After successful API call, in response body we receive request id to track the status of the order. In case of any error, message and traceId will be displayed in the response.

#### Sample payload

```json
  {
    "type": "SECONDARY-ADDON",
    "commitContractReference": "AMER00000MOBILITY",
    "serviceInstanceId":"MXXXXXXXX",
    "offers": [
        {
            "sku": "WSD-ABXAP-12PT0-C1S",
            "quantity": "25"
        }
    ]
  }
```

### Upgrade Subscription

This API call allows upgrade of an existing primary subscription using the service instance id (SID) of the primary order. After a successful API call, the response body will include the request id used to track the status of the order. In case of any error, message and traceId will be displayed in the response.

```
  POST: https://apigw.omnissa.com/v2/m4/api/sdp/subscriptions/upgrade
```

To make an Upgrade call:

1. Create a new HTTP request and select the **GET** method type
2. In the **Authorization** tab set **Type** as `Bearer Token` and put your `access_token` into the Token
3. Create an entry in the **Header** tab with a Key/Value pair with the following values:
    - Key: `partnerId`
    - Value: `{Omnissa Partner ID}`
4. Paste the sample request payload in the Body tab with raw and JSON selected. Replace the information accordingly to reflect which SID is being targeted for renewal and what the renewal payload should be prior to sending
5. Upon a successful call a request id will be returned to track the order

#### Sample payload

```json
  {
    "commitContractReference": "AMER00000MOBILITY",
    "serviceInstanceId":"MXXXXXXXX",
    "offers": [
        {
            "fromSku": "WSD-AWOSP-12PT0-C1S", //WS1 Standard 12 month Pre-paid
            "toSku": "WSD-AWOAP-12PT0-C1S", //to WS1 Advanced 12 month Pre-paid
            "quantity": "25"
        }
    ]
  }
```
### Manual Renewal

This API call allows a renewal order to be made within the allowed renewal period. Use the service instance id (SID) of the primary order to place the upgrade order. The updated quantity will take effect upon the next billing cycle. After a successful API call, the response body will include the request id used to track the status of the order. In case of any error, message and traceId will be displayed in the response. Note that if the renewal window is not open then the API call will error out.

Renewal orders can include:

- Increase/Decrease quantity of a single SKUs for a single customer at time of renewal
- Increase/Decrease quantity of multiple SKUs for a single customer at time of renewal
- Change of base SKU
- Addition/removal of add-ons

```
  POST: https://apigw.omnissa.com/v2/m4/api/sdp/subscriptions/renewal
```

To make a Renewal call:

1. Create a new HTTP request and select the **GET** method type
2. In the **Authorization** tab set **Type** as `Bearer Token` and put your `access_token` into the Token
3. Create an entry in the **Header** tab with a Key/Value pair with the following values:
    - Key: `partnerId`
    - Value: `{Omnissa Partner ID}`
4. Paste the sample request payload in the Body tab with raw and JSON selected. Replace the information accordingly to reflect which SID is being targeted for renewal and what the renewal payload should be prior to sending
5. Upon a successful call a request id will be returned to track the order

#### Sample payload

```json
  {
    "commitContractReference": "AMER00000MOBILITY",
    "serviceInstanceId":"MXXXXXXXX",
    "offers": [
        {
            "sku": "WSD-AWOAP-12PT0-C1S",
            "quantity": "2"
        }
    ]
  }
```

If a renewal request is made prior to the renewal window the error message will read:

#### Sample error - 400 Bad request

```json
  {
      "traceId": "0000000000000000",
      "error": "INVALID_REQUEST",
      "message": "Renewal allowed only during last 30 days of active term"
  }
```

### Cancel subscription

Prior to auto renewal, subscriptions can be cancelled within the allowed period using this API call. Note that if the cancellation window is not open then the API call will error out.

```
  POST: https://apigw.omnissa.com/v2/m4/api/sdp/subscriptions/cancel
```

To make a Cancellation call:

1. Create a new HTTP request and select the **POST** method type and paste the URL
2. In the **body**, include the SID of the selected subscription as shown in the sample payload
3. Once the call is made you will get a 202 Accepted for a successful request

#### Sample payload

```json
  {
    "serviceInstanceId":"MXXXXXXXX"
  }
```

#### Sample error - 400 Bad request

If a cancellation request is made prior to the cancellation window the error message will read:

```json
  {
	  "traceId": "0000000000000000",
    "error": "INVALID_REQUEST",
    "message": "Cancellation allowed only during last 30 days of active term"
  }
```

<swagger-ui src="MSPOrderingAPI.yaml"/>
