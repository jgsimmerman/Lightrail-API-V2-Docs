# Checkout Integration

<p class= "intro">The Checkout endpoint unifies payment. It allows you to charge multiple sources, including credit cards, in a single step.</p>

Accepting multiple payment methods is complicated because it requires logic to ensure that each payment source is charged in the correct order, for the right amount, and with transactional consistency. The Checkout endpoint calculates the subtotal, applies any relevant customer value (e.g. promotion, gift card), and charges the customer's credit card, if applicable, all in a single request. 

### Where does the Checkout API fit in? 

When a customer of yours wants to make a purchase, you need to accept payment. It's at this step that you would make a Checkout API Request, sending details of the items they are purchasing along with any payment methods for the purchase. Checkout can also be used to simulate a purchase, allowing you to display to your customer what customer value applies to their transaction before you process the transaction. 

### How does the Checkout API work? 

Checkout can be broken into two primary components:

- line items: the customer's shopping cart
- payment sources: the customer's methods of payment

#### Line Items
Line items represent a shopping cart and contain all of the information required for Lightrail to calculate the transaction. Below is an example shopping cart with two line items. Note, by including the `taxRate`, Lightrail automatically calculates and charges for any tax that must be collected.
```
    "lineItems":[
          {
             "productId": "shoes-pid-133241",
             "unitPrice":4999,
             "taxRate":0.05
          },
          {
             "productId": "hat-pid-43545",
             "unitPrice":1799,
             "taxRate":0.05
          }
    ]
```

#### Payment Sources
The `sources` property in the `checkout` endpoint contains a list of payment sources. 
A `source` consists of an object with a `rail` identifier along with some additional data. Rail refers to "payment rail" and is used to differentiate where a particular source must be charged. 

Promotions, gift cards, and loyalty points you've created within Lightrail are all accessed via `"rail": "lightrail"`. Credit card processing is done via Stripe through `"rail": "stripe"`. There's one more `"rail": "internal"` which represents value that is internal to your system. It means you're tracking the collection of funds, outside of Lightrail and Stripe. For example, this allows you to migrate off a legacy gift card system you might have.

See the [full endpoint reference](https://lightrailapi.docs.apiary.io/#reference/0/transactions/checkout) for details. 

#### Rail: Lightrail
With `"rail": "lightrail"` you'll supply an identifier of an object in Lightrail. The following is an example of supplying a Lightrail payment source.
```json
    { 
        "rail": "lightrail",
        "contactId": "contact-123456"
    }
```

There are 3 possible variations for the Lightrail rail: 

- contactId: The ID of a Contact. This causes all Values attached to that Contact to be considered for the Checkout Transaction.
- valueId: The ID of a Value. Uniquely identifies a single value in Lightrail.
- code: The code of a Value in Lightrail. Often represents a gift card code or some promotion code that the customer enters during checkout. 

#### Rail: Stripe
The Stripe rail is used when payment from a credit card is required for the transaction. 
You must OAuth your Stripe account with Lightrail so that Lightrail can charge the credit card on your behalf using [Stripe Connect](https://stripe.com/connect).

Usage:
```json
    {
        "rail": "stripe",
        "source": "tok_12345"
    }
```

The `source` is a tokenized credit card created from Stripe Elements.

### Checkout Example

Suppose a customer has a promotion attached to their Lightrail Contact record and provides their credit card to cover what's outstanding. The following is an example of the Checkout request and response. 

Request: `POST /transactions/checkout`
```json
    {
       "id": "sample-transaction-123",
       "currency": "USD",
       "sources":[
          {
             "rail": "lightrail",
             "contactId": "sample-contact-id-123"
          },
          {
             "rail": "stripe",
             "source": "tok_visa"
          }
       ],
       "lineItems":[
          {
             "productId": "shoes-pid-133241",
             "unitPrice":4999,
             "taxRate":0.05
          },
          {
             "productId": "hat-pid-43545",
             "unitPrice":1799,
             "taxRate":0.05
          }
       ],
       "simulate":true
    }
```

Response:
```json
    {
       "id": "sample-transaction-123",
       "transactionType": "checkout",
       "currency": "USD",
       "createdDate": "2019-12-20T21:34:13.000Z",
       "tax":{
          "roundingMode": "HALF_EVEN"
       },
       "paymentSources":[
          {
             "rail": "lightrail",
             "contactId": "sample-contact-id-123"
          },
          {
             "rail": "stripe",
             "source": "tok_visa"
          }
       ],
       "totals":{
          "subtotal":6798,
          "tax":272,
          "discount":1360,
          "payable":5710,
          "remainder":0,
          "forgiven":0,
          "discountLightrail":1360,
          "paidLightrail":0,
          "paidStripe":5710,
          "paidInternal":0
       },
       "lineItems":[
          {
             "productId": "shoes-pid-133241",
             "unitPrice":4999,
             "taxRate":0.05,
             "quantity":1,
             "lineTotal":{
                "subtotal":4999,
                "taxable":3999,
                "tax":200,
                "discount":1000,
                "remainder":0,
                "payable":4199
             }
          },
          {
             "productId": "hat-pid-43545",
             "unitPrice":1799,
             "taxRate":0.05,
             "quantity":1,
             "lineTotal":{
                "subtotal":1799,
                "taxable":1439,
                "tax":72,
                "discount":360,
                "remainder":0,
                "payable":1511
             }
          }
       ],
       "steps":[
          {
             "rail": "lightrail",
             "valueId": "sample-value-id-123",
             "contactId": "sample-contact-id-123",
             "code":null,
             "balanceBefore":null,
             "balanceChange":-1360,
             "balanceAfter":null,
             "usesRemainingBefore":1,
             "usesRemainingChange":-1,
             "usesRemainingAfter":0
          },
          {
             "rail": "stripe",
             "chargeId":null,
             "charge":null,
             "amount":-5710
          }
       ],
       "pending":false,
       "metadata":null,
       "createdBy": "user-5022fccf827647ee9cfb63b779d62193-TEST"
    }
```

As you can see, Lightrail handles the complexity of applying the promotion, calculating tax, and charging the various payment sources. In this example the customer was discounted $13.60 and paid the remaining $57.60 via credit card. Lightrail returns a summary and detailed information of the transaction so that it's easy to display a breakdown to the customer. 