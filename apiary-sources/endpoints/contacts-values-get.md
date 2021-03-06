### List a Contact's Values [GET /contacts/{id}/values{?limit}{?programId}{?currency}{?balance}{?usesRemaining}{?discount}{?active}{?frozen}{?canceled}{?pretax}{?startDate}{?endDate}{?createdDate}{?updatedDate}]

+ Parameter
    + id (string) - The ID of the Contact to get the Values of.
    + limit (number, optional) - {{pagination.limit}}
    + programId (string, optional) - {{filter.programId}}  {{filter.ops.in}}
    + currency (string, optional) - {{filter.currency}}  {{filter.ops.in}}
    + balance (number, optional) - {{filter.balance}}  {{filter.ops.number}}
    + usesRemaining (number, optional) - {{filter.usesRemaining}}  {{filter.ops.number}}
    + discount (boolean, optional) - {{filter.discount}}
    + active (boolean, optional) - {{filter.active}}
    + frozen (boolean, optional) - {{filter.frozen}}
    + canceled (boolean, optional) - {{filter.canceled}}
    + pretax (boolean, optional) - {{filter.pretax}}
    + startDate (string, optional) - {{filter.startDate}}  {{filter.ops.date}}
    + endDate (string, optional) - {{filter.endDate}}  {{filter.ops.date}}
    + createdDate (string, optional) - {{filter.createdDate}}  {{filter.ops.date}}
    + updatedDate (string, optional) - {{filter.updatedDate}}  {{filter.ops.date}}

+ Request (application/json)
    + Headers

            {{header.authorization}}

+ Response 200 (application/json)
    + Headers

            Limit: 100
            MaxLimit: 1000
            Link: <URL>; rel="first", <URL>; rel="prev", <URL>; rel="next", <URL>; rel="last"

    + Attributes (array[Value])

    + Body

            {REQUEST_REPLACEMENT:listContactsValue1.response.body}
