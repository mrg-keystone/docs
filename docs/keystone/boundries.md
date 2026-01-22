# Boundries

## Logic

Logic is classified into two different camps based on testablilty

1. Business -- this is the actual application logic. This cannot have imports from
   outside of the Dto folder.
1. Data -- this is impure functionality when extracting this from prototypes we
   keep it as thin as possible, cannot have imports outside of the Dto folder, this
   is the "outbound boundry" of the application.
   3.Coordinators -- these join business and data, they are composed of an inner function
   and an outer function the outer function defines (data) the setup, getting data etc...
   the inner function defines the pure business logic. It is structured as a sandwich
   get data -> pass data in business -> do stuff with the return, persist, http calls etc...

## Crossing Boundries

1. Dto -- These are the boundries, they use the class-validator and class
   transformer libraries to enforce runtime typesafety as well as provide types for the
   ide.
1. Entrypoints -- This is where the user interacts with the application or the
   "inbound boundry". this can be controllers, routes, or exports

## Barrel Exports

Only to be done on polymorphic <feature-name>.mod.ts and "bootstrap.ts"
