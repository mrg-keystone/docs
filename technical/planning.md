[rules]

## vocabulary

Dto is data that must be verified because it comes from outside the system.
Entity is an object that can act within the system.
Value is an object, array, string, number or boolean.
Noun is an entity, dto, or value.
Verb is a word that describes an act.
State is what the Noun becomes after the Verb
Fault is a defective state (error)
Test is an executable specification formatted as: Noun.Verb → State | Fault.

## Requirements

• A requirement describes an e2e test
• Can be broken down into steps

| requirement                      | comment                                   |
| -------------------------------- | ----------------------------------------- |
| apple.look->identified           | look at the apple and determine its color |
| recording.audit->compliant       | recording meets compliance rules or not   |
| user.create->created             | new user exists                           |
| user.deactivate->deactivated     | user can no longer be active              |
| user.authenticate->authenticated | identity was proven                       |
| request.authorize->approved      | access was granted                        |
| application.submit->created      | application exists                        |
| application.review->reviewed     | application has been reviewed             |

## Steps

• Are derrived from requirements
• formatted as (Noun.Verb) as a signature; example - id.get(item): item-id
• suffix DTOs with dto; leave internal types unsuffixed.
| step | comment |
| ---------------------------------------------------- | ----------------------- |
| get.id(item): item-id | extract identifier |
| get.range(items, start, end): item-range | slice items |
| set.status(order, status): updated-order | update state |
| create.user(user-dto, profile-dto): user | create user and profile |
| authenticate.user(creds-dto, device-id): auth-result | verify identity |
| authorize.request(request, policy): approval | evaluate access |
| submit.application(app-dto, user-id): application | submit on behalf |

> **REVIEW:** These examples are all standalone. Add one traced example showing
> how a single requirement (e.g., `user.authenticate->authenticated`) decomposes
> into its steps. That's the missing link between this section and the one above.

## Assertions

• Are derived from steps
• Describes a unit test
• Do not use the words `and` or `or`

| ~~story point~~ assertion | statement                             |
| ------------------------- | ------------------------------------- |
| user >> log-in            | given a user it should log in         |
| session >> expire         | given a session it should expire      |
| payment >> settle         | given a payment it should settle      |
| ~~recording >> audit~~    | ~~given a recording it should audit~~ |
