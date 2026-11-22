# Defect report: EPIC-4 Owner screens

Run 9, code: Spring PetClinic REST with its Angular front-end. 3 product defect(s): an assertion on behaviour failed in a Then step and no script problem explains it. The tests below were not edited to pass.

## TC-EPIC-4-ST-1-08 OWNER_ADMIN role gates listing, searching, adding and updating owners

**Rules**
- R-EPIC-4-E-04: One OWNER_ADMIN role gates adding, editing, listing and searching owners
  - expected: A caller without the OWNER_ADMIN role can neither list or search owners nor add or update one; a caller with the role can do all of these.
  - code: `src/main/java/org/springframework/samples/petclinic/rest/controller/v1/OwnerRestControllerV1.java#L74`
  - code: `src/main/java/org/springframework/samples/petclinic/rest/controller/v1/OwnerRestControllerV1.java#L99`
  - code: `src/main/java/org/springframework/samples/petclinic/rest/controller/v1/OwnerRestControllerV1.java#L111`

**Failed step (then)**: Each of the four calls is refused with 401 or 403 and none returns 2xx

**Observed**

```
Call answered 200, expected 401 or 403; all statuses: [200, 200, 201, 204]
```

Last request: `PUT http://localhost:9966/petclinic/api/owners/48`

**Steps to reproduce**
1. Given The seeded owner George Franklin exists and a caller without the OWNER_ADMIN role (no credentials or a user lacking the role) is used first
2. When That caller sends GET /api/owners, a search by last name Franklin, POST /api/owners with the new owner and PUT /api/owners/{id} with the update body
3. Then Each of the four calls is refused with 401 or 403 and none returns 2xx
4. Then Verified afterwards as OWNER_ADMIN: no owner with last name Gatekeeper exists and the seeded owner still has address 110 W. Liberty St.
5. When A caller with the OWNER_ADMIN role repeats the same four calls
6. Then The list and the search return 200 with the owner, the POST succeeds and creates Nora Gatekeeper, and the PUT succeeds and the owner then has address 1 Gate St.

**Data**

```json
{
  "seeded": {
    "firstName": "George",
    "lastName": "Franklin",
    "address": "110 W. Liberty St.",
    "city": "Madison",
    "telephone": "6085551023"
  },
  "newOwner": {
    "firstName": "Nora",
    "lastName": "Gatekeeper",
    "address": "1 Gate St.",
    "city": "Madison",
    "telephone": "6085550001"
  },
  "updateBody": {
    "firstName": "George",
    "lastName": "Franklin",
    "address": "1 Gate St.",
    "city": "Madison",
    "telephone": "6085551023"
  }
}
```

**Screenshot**: none (API test). **Robot log**: `results/EPIC-4/run-9/log.html`

## TC-EPIC-4-ST-1-39 POST and PUT owner calls without the OWNER_ADMIN role are refused and save nothing

**Rules**
- R-EPIC-4-ST-1-21: Adding and updating an owner require the OWNER_ADMIN role
  - expected: POST /api/owners and PUT /api/owners/{id} are guarded by hasRole OWNER_ADMIN; a caller without that role is not allowed to save.
  - code: `src/main/java/org/springframework/samples/petclinic/rest/controller/v1/OwnerRestControllerV1.java#L99`
  - code: `src/main/java/org/springframework/samples/petclinic/rest/controller/v1/OwnerRestControllerV1.java#L111`

**Failed step (then)**: The response status is 403 and no owner Betty Davis was created

**Observed**

```
Unexpected status; body: {"firstName":"Betty","lastName":"Davis","address":"638 Cardinal Ave.","city":"Sun Prairie","telephone":"6085551749","pets":[],"id":57}: 201 != 403
```

Last request: `POST http://localhost:9966/petclinic/api/owners`

**Steps to reproduce**
1. Given An owner George Franklin, 110 W. Liberty St. exists with a known id, and the caller is logged in without OWNER_ADMIN
2. When POST /api/owners is sent with the Betty Davis body
3. Then The response status is 403 and no owner Betty Davis was created
4. When PUT /api/owners/{id} is sent for the seeded owner with address 1 Changed St.
5. Then The response status is 403
6. Then Read back as OWNER_ADMIN, the seeded owner still has address 110 W. Liberty St.

**Data**

```json
{
  "postBody": {
    "firstName": "Betty",
    "lastName": "Davis",
    "address": "638 Cardinal Ave.",
    "city": "Sun Prairie",
    "telephone": "6085551749"
  },
  "putBody": {
    "firstName": "George",
    "lastName": "Franklin",
    "address": "1 Changed St.",
    "city": "Madison",
    "telephone": "6085551023"
  }
}
```

**Screenshot**: none (API test). **Robot log**: `results/EPIC-4/run-9/log.html`

## TC-EPIC-4-ST-2-14 Without the OWNER_ADMIN role owners cannot be listed, searched, added or updated; with the role all four work

**Rules**
- R-EPIC-4-ST-2-07: Owner list API requires the OWNER_ADMIN role
  - expected: Listing or searching owners is allowed only for users with the OWNER_ADMIN role.
  - code: `src/main/java/org/springframework/samples/petclinic/rest/controller/v1/OwnerRestControllerV1.java#L74`
- R-EPIC-4-E-04: One OWNER_ADMIN role gates adding, editing, listing and searching owners
  - expected: A caller without the OWNER_ADMIN role can neither list or search owners nor add or update one; a caller with the role can do all of these.
  - code: `src/main/java/org/springframework/samples/petclinic/rest/controller/v1/OwnerRestControllerV1.java#L74`
  - code: `src/main/java/org/springframework/samples/petclinic/rest/controller/v1/OwnerRestControllerV1.java#L99`
  - code: `src/main/java/org/springframework/samples/petclinic/rest/controller/v1/OwnerRestControllerV1.java#L111`

**Failed step (then)**: Both requests are rejected as unauthorized or forbidden (status 401 or 403) and no owner data is returned

**Observed**

```
GET /owners answered 200, expected 401 or 403
```

Last request: `GET http://localhost:9966/petclinic/api/owners?lastName=Davis`

**Steps to reproduce**
1. Given The caller has no OWNER_ADMIN role (not authenticated, or a user without that role)
2. When GET /api/owners and GET /api/owners?lastName=Davis are called
3. Then Both requests are rejected as unauthorized or forbidden (status 401 or 403) and no owner data is returned
4. When POST /api/owners is called with the newOwner data
5. Then The request is rejected as unauthorized or forbidden (status 401 or 403) and Eva Barrow is not created
6. When PUT /api/owners/{id} is called for the seeded owner with address '111 W. Liberty St.'
7. Then The request is rejected as unauthorized or forbidden (status 401 or 403) and the owner's address is unchanged
8. Given The caller is authenticated with the OWNER_ADMIN role
9. When GET /api/owners, GET /api/owners?lastName=Davis, POST /api/owners with newOwner, and PUT /api/owners/{id} with the new address are called
10. Then The two GET requests answer 200 with owners, the POST creates Eva Barrow, and the PUT updates the seeded owner's address to '111 W. Liberty St.'

**Data**

```json
{
  "seededOwner": {
    "firstName": "George",
    "lastName": "Franklin",
    "address": "110 W. Liberty St.",
    "city": "Madison",
    "telephone": "6085551023"
  },
  "newOwner": {
    "firstName": "Eva",
    "lastName": "Barrow",
    "address": "7 Elm St.",
    "city": "Madison",
    "telephone": "6085550111"
  },
  "updatedAddress": "111 W. Liberty St."
}
```

**Screenshot**: none (API test). **Robot log**: `results/EPIC-4/run-9/log.html`
