[RULES]

## Vocabulary

Dto is data that must be verified because it comes from outside the system.
Entity is an object that can act within the system.
Primitive is an object, array, string, number or boolean.
Noun is an entity, dto, or primative
Verb is a word that describes an act
Outcome is a field that holds a state, for example color for an apple.

## Requirements

• A requirement only exists to be broken down
• formatted as Noun.Verb->Outcome
| requirement | comment |
| -------------------------------- | -------------------------------- |
| apple.look->color | look at the apple, determine its color |
| recording.audit->compliant | recording meets compliance rules or not |
| user.create->created | new user exists |
| user.deactivate->status | user can no longer active |
| user.authenticate->authenticated | identity was proven |
| request.authorize->approved | access was granted |
| application.submit->created | application exists |
| application.review->status | application failed review |

## Steps

• Break each requirement into steps.  
• formatted as (verb.noun) as a signature; example - get.id(item): item-id
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

## Story Points

• Use one form only: given an (entity), it should (outcome)
• You can shorten it to (entity) >> (verb)
• Belongs to one step, has one Noun, and asserts one Outcome
• Do not use the words `and` or `or`

| story point           | statement                             |
| --------------------- | ------------------------------------- |
| user >> log-in        | given a user it should log in         |
| session >> expire     | given a session it should expire      |
| payment >> settle     | given a payment it should settle      |
| recording >> audit    | given a recording it should audit     |
| notification >> send  | given a notification it should send   |
| job >> execute        | given a job it should execute         |
| document >> upload    | given a document it should upload     |
| application >> submit | given an application it should submit |
| user >> deactivate    | given a user it should deactivate     |
| invoice >> generate   | given an invoice it should generate   |
