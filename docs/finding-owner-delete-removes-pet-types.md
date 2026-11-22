# A finding from the demo application: deleting an owner deletes the pet types

While making the screen tests repeatable, the generated suite started failing for a reason that had nothing to do
with the screens: the pet types `cat` and `dog` had disappeared from the application, and so had the seeded cat.
Tracing it back gave a short, repeatable sequence against Spring PetClinic REST (the commit the demo uses).

## Reproduce it

Start the application with its sample data, then:

```
GET    /petclinic/api/pettypes                      -> bird, cat, dog, hamster, lizard, snake
POST   /petclinic/api/owners                        -> a new owner (any valid body)
POST   /petclinic/api/owners/{id}/pets              -> a pet of type cat (id 1)
POST   /petclinic/api/owners/{id}/pets              -> a pet of type dog (id 2)
DELETE /petclinic/api/owners/{id}                   -> 204 No Content
GET    /petclinic/api/pettypes                      -> bird, hamster, lizard, snake      (cat and dog are gone)
```

## What goes wrong

- Deleting **one owner** who still has pets removes the **pet types those pets use**.
- A pet type is shared reference data. With it, every other pet of that type disappears too, including the
  application's own sample cat.
- Deleting a pet on its own, or an owner after the pets were deleted first, does not do this.

## How it was found, and what it is not

- The generated tests created owners with pets and cleaned up by deleting the owner. After a few tests, later tests
  that needed `cat` or `dog` failed.
- The cause was diagnosed by hand, with the steps above. The tool reported the symptoms (failing tests and missing
  data); it did not name this defect on its own.
- It has **not** been reported to the Spring PetClinic project. Check it against the current upstream version before
  describing it as a defect of the released product.
- The demo suites avoid triggering it: every test that creates a pet deletes the pet before its owner.
