## GenericCodeOptions (object)
+ perContact (PerContact, required) - Properties determining what the Value is worth to each Contact. If set, the generic code cannot be used anonymously: it must be attached to a Contact first or a `contactId` must be provided with the `code` in checkout.

## PerContact (object)
+ balance (number, optional) - The balance, as an integer in the smallest unit of currency, available to each Contact that the code is attached to. If an overall balance is set on the Value, the `perContact.balance` will be subtracted from the overall `balance` each time the Value is attached. This allows the Value's `balance` to be used as a liability control and represents the total amount that can be spent in checkout.
+ usesRemaining (number, optional) - The usesRemaining available to each Contact that the code is attached to. If an overall usesRemaining is set on the Value, the perContact.usesRemaining will be subtracted from the overall usesRemaining each time the Value is attached. This allows the Value's `usesRemaining` to be used as a liability control and represents how many times the generic code can be used in total.