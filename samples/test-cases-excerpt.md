# Designed tests: excerpt

Four of the 37 tests the tool designed for the Owners Epic of Spring PetClinic REST, copied from its generated file. Each names the rules it covers.

### TC-EPIC-1-ST-1-12 Create a valid owner: 201 with id and Location, same values on GET by id, and found by last-name search

Priority **high**, type **api**

Covers:
- R-EPIC-1-ST-1-01: Create owner with valid fields returns the new owner
- R-EPIC-1-E-01: An owner created in ST-1 is found by the ST-2 search; a rejected one is not

Preconditions:
- Authenticated as owner admin
- Valid owner payload with a letters-only last name that is unique per run (the lastName pattern rejects digits), e.g. Franklinqzx

Data:

| field | value |
|---|---|
| payload | {"firstName": "George", "lastName": "Franklinqzx", "address": "110 W. Liberty St.", "city": "Madison", "telephone": "6085551023"} |

Steps:
- **Given** Signed-in owner admin; GET /api/owners?lastName=Franklinqzx returns 404 (count of owners with that last name is 0)
- **When** POST /api/owners with the valid payload
- **Then** The response is 201, the body contains the five submitted values and a server-generated id (integer, minimum 0), and the Location header is /api/owners/{id} for that id
- **When** GET /api/owners/{id} using the id from the response
- **Then** The response is 200 and firstName, lastName, address, city and telephone equal the submitted values
- **When** GET /api/owners?lastName=Franklinqzx
- **Then** The response is 200 and the list contains exactly one owner with that id, city Madison and telephone 6085551023 (count grew from 0 to 1)

Drives: endpoint `POST /api/owners`, endpoint `GET /api/owners/{ownerId}`, endpoint `GET /api/owners?lastName={lastName}`

### TC-EPIC-1-ST-1-16 Changing the address of an existing owner returns 204 and is visible on GET

Priority **high**, type **api**

Covers:
- R-EPIC-1-ST-1-05: Changing the address of an existing owner is saved and visible

Preconditions:
- Authenticated as owner admin
- Existing owner created through POST /api/owners

Data:

| field | value |
|---|---|
| newAddress | 638 Cardinal Ave. |

Steps:
- **Given** An existing owner George Franklin, address 110 W. Liberty St., city Madison, telephone 6085551023
- **When** PUT /api/owners/{id} with address "638 Cardinal Ave." and the other four fields unchanged and valid
- **Then** The response is 204 (as the code has it, per answer to Q-EPIC-1-02)
- **When** GET /api/owners/{id}
- **Then** The response is 200 and address is "638 Cardinal Ave."

Drives: endpoint `PUT /api/owners/{ownerId}`, endpoint `GET /api/owners/{ownerId}`

### TC-EPIC-1-ST-1-21 Create and update of owners are refused for a user without the OWNER_ADMIN role

Priority **high**, type **api**, tags: CodeOnly

Covers:
- R-EPIC-1-ST-1-10: Create and update of owners require the OWNER_ADMIN role

Preconditions:
- A signed-in user who does not hold the OWNER_ADMIN role
- Existing owner George Franklin (seeded id 1 or created by an owner admin)

Data:

| field | value |
|---|---|
| lastName | Forbiddenqzx |
| update | {"firstName": "George", "lastName": "Franklin", "address": "1 Changed St.", "city": "Madison", "telephone": "6085551023"} |

Steps:
- **Given** A user authenticated without role OWNER_ADMIN, and an existing owner with address 110 W. Liberty St.
- **When** POST /api/owners with a valid payload and last name Forbiddenqzx
- **Then** The response is refused (403 Forbidden) and no owner is created
- **When** PUT /api/owners/{id} with address "1 Changed St." and the other fields valid
- **Then** The response is refused (403 Forbidden)
- **When** As an owner admin, GET /api/owners/{id} and GET /api/owners?lastName=Forbiddenqzx
- **Then** The owner's address is still 110 W. Liberty St. and the search returns 404

Drives: endpoint `POST /api/owners`, endpoint `PUT /api/owners/{ownerId}`, endpoint `GET /api/owners/{ownerId}`

### TC-EPIC-1-S3-09 A single pet is returned only through the owner it belongs to

Priority **medium**, type **api**, tags: CodeOnly

Covers:
- R-EPIC-1-S3-05: Single pet of an owner is returned only when it belongs to that owner

Preconditions:
- Authenticated as owner admin
- Owner with two pets

Data:

| field | value |
|---|---|
| ownerId | 6 |
| ownPetId | 7 |
| otherOwnersPetId | 1 |
| unknownOwnerId | 999999 |
| note | Pet 7 (Samantha) belongs to owner 6; pet 1 (Leo) belongs to owner 1. Confirm both in the seed script, or take ids from the created fixture. |

Steps:
- **Given** Owner 6 owns pet 7, and pet 1 belongs to a different owner
- **When** GET /api/owners/6/pets/7 is called
- **Then** The response status is 200 and the body is the pet with id 7, its name, birthDate, type and visits array
- **When** GET /api/owners/6/pets/1 is called (a pet that exists but is not one of owner 6's pets)
- **Then** The response status is 404 and no pet data is returned
- **When** GET /api/owners/999999/pets/7 is called (owner does not exist)
- **Then** The response status is 404 and no pet data is returned

Drives: endpoint `GET /api/owners/{ownerId}/pets/{petId}`
