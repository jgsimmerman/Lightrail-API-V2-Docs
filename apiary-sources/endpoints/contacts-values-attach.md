### Attach a Contact to a Value [POST /contacts/{id}/values/attach]
Attaching a Value to a Contact will associate the Value with the Contact. The Contact then "has" that Value. In a checkout Transaction, specifying the  `contactId` as a payment source will resolve to all associated Values for the Contact to be considered for the Transaction.

If attaching a Unique Value (`isGenericCode=false`) the Value's `contactId` will be set to the ID of the Contact attached to it. If attaching a Generic Code (`isGenericCode=true`) it depends on whether the Generic Code has `genericCodeOptions.perContact` properties set. If set, attaching will result in a new Value being created using the perContact properties for the new Value's `balance` and `usesRemaining`. If not set, the Generic Code will be shared and the Contact will receive a reference to the Generic Code. You can [list the Value's Contacts](#reference/0/values/list-a-value's-attached-contacts) to see all Contacts attached to the Value.        

+ Parameter
    + id (string) - the ID of the Contact to attach Value to.

+ Request (application/json)
    + Headers
    
            {{header.authorization}}
        
    + Attributes
        + valueId (string, optional) - The `id` of the Value to attach to the Contact.  One of `valueId` or `code` must be specified.
        + code (string, optional) - The `code` of the Value to attach to the Contact.  One of `valueId` or `code` must be specified.

    + Body

            {REQUEST_REPLACEMENT:attachValue1.body}
    
+ Response 200 (application/json)
    
    + Attributes (Value)

    + Body
            
            {REQUEST_REPLACEMENT:attachValue1.response.body}

+ Response 409 (application/json)
    
    A Value with `isGenericCode=true` true and `usesRemaining=0` cannot be attached to any more Contacts.
    
    + Attributes (RestError)
    
    + Body
    
            {
                "statusCode": 409,
                "message": "The Value with id '123abc' cannot be attached because it has a generic code and has 0 usesRemaining."
                "messageCode": "InsufficientUses"
            }

+ Response 409 (application/json)
    
    A Value that is frozen cannot be attached.
    
    + Attributes (RestError)
    
    + Body
    
            {
                "statusCode": 409,
                "message": "The Value cannot be attached because it is frozen."
                "messageCode": "ValueFrozen"
            }

+ Response 409 (application/json)
    
    A Value that is canceled cannot be attached.
    
    + Attributes (RestError)
    
    + Body
    
            {
                "statusCode": 409,
                "message": "The Value cannot be attached because it is canceled."
                "messageCode": "ValueCanceled"
            }
