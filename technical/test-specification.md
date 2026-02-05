[test-specification]

## Test

- Is an executable specification
- Formatted as: Noun.Verb -> State / Fault
- Reads as: `${Noun} should be ${State}` / `${Noun} should be ${Fault}`
- Applied as a requirement (e2e test) or assertion (unit test)
- Fault is omitted in requirements, required in assertions
- Faults originate at the step level; assertions inherit them
- Fault names should describe why something didn't succeed, not just that it didn't

| notation                                    | english                         |
| ------------------------------------------- | ------------------------------- |
| user.authenticate -> authenticated          | user should be authenticated    |
| session.create -> created                   | session should be created       |
| credentials.validate -> validated / invalid | credentials should be validated |
| credentials.validate -> validated / invalid | credentials should be invalid   |
| user.find -> found / not-found              | user should be found            |
| user.find -> found / not-found              | user should not be found        |

## Requirements

- Are applied as e2e tests
- Happy path only; faults are defined in steps
- Are broken down into steps

| requirement                        | comment                       |
| ---------------------------------- | ----------------------------- |
| recording.audit -> audited         | recording has been evaluated  |
| user.create -> created             | new user exists               |
| user.deactivate -> deactivated     | user can no longer be active  |
| user.authenticate -> authenticated | identity was proven           |
| request.authorize -> approved      | access was granted            |
| application.submit -> submitted    | application accepted          |
| application.review -> reviewed     | application has been reviewed |

## Steps

- Are implementation details, not tests
- Are derived from requirements
- Formatted as Noun.Verb(args): return-type / fault
- Fault is optional; omit for steps that cannot fail
- Suffix DTOs (external input that must be validated) with dto; leave internal types unsuffixed.

| step                                                            | comment                 |
| --------------------------------------------------------------- | ----------------------- |
| id.get(item): item-id                                           | extract identifier      |
| range.get(items, start, end): item-range                        | slice items             |
| status.set(order, status): updated-order / not-found            | update state            |
| user.create(user-dto, profile-dto): user / invalid              | create user and profile |
| user.authenticate(creds-dto, device-id): auth-result / rejected | verify identity         |
| request.authorize(request, policy): approval / denied           | evaluate access         |
| application.submit(app-dto, user-id): application / invalid     | submit on behalf        |

## Assertions

- Are applied as unit tests
- Are derived from steps
- Fault is required; each assertion tests one outcome
- Do not use the words `and` or `or`

| assertion                                   | statement                                  |
| ------------------------------------------- | ------------------------------------------ |
| credentials.validate -> validated / invalid | given credentials they should be validated |
| user.find -> found / not-found              | given a user it should be found            |
| password.verify -> verified / mismatched    | given a password it should be verified     |
| session.create -> created / unavailable     | given a session it should be created       |
| session.expire -> expired / not-found       | given a session it should expire           |
| payment.settle -> settled / declined        | given a payment it should settle           |
| recording.audit -> audited / flagged        | given a recording it should be audited     |
| user.deactivate -> deactivated / not-found  | given a user it should be deactivated      |
| invoice.generate -> generated / incomplete  | given an invoice it should be generated    |

## Traced example

`user.authenticate -> authenticated` breaks down into:

| step                                                         | comment                    |
| ------------------------------------------------------------ | -------------------------- |
| credentials.validate(creds-dto): credentials / invalid       | check input format         |
| user.find(credentials): user / not-found                     | look up user by identifier |
| password.verify(user, credentials): auth-result / mismatched | compare password hash      |
| session.create(user, device-id): session / unavailable       | issue session on success   |

Each step produces an assertion:

| assertion                                   | statement                                  |
| ------------------------------------------- | ------------------------------------------ |
| credentials.validate -> validated / invalid | given credentials they should be validated |
| credentials.validate -> validated / invalid | given credentials they should be invalid   |
| user.find -> found / not-found              | given a user it should be found            |
| user.find -> found / not-found              | given a user it should not be found        |
| password.verify -> verified / mismatched    | given a password it should be verified     |
| password.verify -> verified / mismatched    | given a password it should be mismatched   |
| session.create -> created / unavailable     | given a session it should be created       |
| session.create -> created / unavailable     | given a session it should be unavailable   |
