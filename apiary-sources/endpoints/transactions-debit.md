### Debit [POST /transactions/debit]

Debit (remove an amount from) a Lightrail payment source.  Debiting is simpler and less powerful than checkout.  It does not apply the promotion logic of `balanceRules`, or calculate discounts or taxes.

+ Request (application/json)

    + Headers
    
            {{header.authorization}}
        
    + Attributes
        + id (string, required) - {{transaction.id}}  {{transaction.idPurpose}}
        + source (LightrailTransactionParty, required) - The payment rail to debit.  Only `lightrail` rails that refer to a specific Value are supported.
        + currency (string, required) - {{currency.code}}
        + amount (number) - The amount to debit.  Must be > 0 if specified.  One of `amount` or `uses` must be specified.
        + uses (number) - The number of `usesRemaining` to debit.  Defaults to 0.  Must be > 0 if specified.  One of `amount` or `uses` must be specified.
        + simulate (boolean, optional) - {{transaction.simulate}}
        + allowRemainder (boolean, optional) - {{transaction.allowRemainder}}
        + pending (boolean, optional) - {{transaction.pending}}
        + metadata (object, optional) - {{transaction.metadata}}

    + Body

            {REQUEST_REPLACEMENT:debitTransaction1.body}
    
+ Response 201 (application/json)

    + Attributes (Transaction)

    + Body

            {REQUEST_REPLACEMENT:debitTransaction1.response.body}

+ Response 409 (application/json)

    You cannot debit a Value by more balance than is available (if `allowRemainder` is not `true`).

    + Attributes (RestError)

    + Body

            {
                "statusCode": 409,
                "message": "Insufficient balance for the transaction.",
                "messageCode": "InsufficientBalance"
            }
