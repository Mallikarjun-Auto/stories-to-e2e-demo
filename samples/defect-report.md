# Defect report: EPIC-1 Owners

Run 14, code at `0515ec8b57e7`. 4 product defect(s): an assertion on behaviour failed in a Then step and no script problem explains it. The tests below were not edited to pass.

## TC-EPIC-1-ST-1-21 Create and update of owners are refused for a user without the OWNER_ADMIN role

**Rules**
- R-EPIC-1-ST-1-10: Create and update of owners require the OWNER_ADMIN role
  - expected: POST /api/owners and PUT /api/owners/{id} are allowed only for users with role OWNER_ADMIN.
  - expected: A user without that role cannot create or change an owner.
  - code: `src/main/java/org/springframework/samples/petclinic/rest/controller/v1/OwnerRestControllerV1.java#L99`
  - code: `src/main/java/org/springframework/samples/petclinic/rest/controller/v1/OwnerRestControllerV1.java#L111`

**Failed step (then)**: The response is refused (403 Forbidden) and no owner is created

**Observed**

```
Unexpected status; body: {"firstName":"George","lastName":"Forbiddenqzx","address":"1 Changed St.","city":"Madison","telephone":"6085551023","pets":[],"id":19}: 201 != 403
```

Last request: `POST http://localhost:9966/petclinic/api/owners`

**Steps to reproduce**
1. Given A user authenticated without role OWNER_ADMIN, and an existing owner with address 110 W. Liberty St.
2. When POST /api/owners with a valid payload and last name Forbiddenqzx
3. Then The response is refused (403 Forbidden) and no owner is created
4. When PUT /api/owners/{id} with address "1 Changed St." and the other fields valid
5. Then The response is refused (403 Forbidden)
6. When As an owner admin, GET /api/owners/{id} and GET /api/owners?lastName=Forbiddenqzx
7. Then The owner's address is still 110 W. Liberty St. and the search returns 404

**Data**

```json
{
  "lastName": "Forbiddenqzx",
  "update": {
    "firstName": "George",
    "lastName": "Franklin",
    "address": "1 Changed St.",
    "city": "Madison",
    "telephone": "6085551023"
  }
}
```

**Screenshot**: none (API test). **Robot log**: `results/EPIC-1/run-14/log.html`

## TC-EPIC-1-ST-2-16 Owner search is allowed for OWNER_ADMIN and refused without that role

**Rules**
- R-EPIC-1-ST-2-05: Owner search requires the OWNER_ADMIN role
  - expected: A user holding the OWNER_ADMIN role can search owners.
  - expected: A user without the OWNER_ADMIN role cannot search owners.
  - code: `src/main/java/org/springframework/samples/petclinic/rest/controller/v1/OwnerRestControllerV1.java#L74`
  - code: `src/main/java/org/springframework/samples/petclinic/rest/controller/v2/OwnerRestControllerV2.java#L32`

**Failed step (then)**: The response status is 403 (access refused) and no owner list is returned

**Observed**

```
Unexpected status; body: [{"firstName":"Betty","lastName":"Davis","address":"638 Cardinal Ave.","city":"Sun Prairie","telephone":"6085551749","pets":[{"name":"Basil","birthDate":"2012-08-06","type":{"name":"hamster","id":6},"id":2,"visits":[],"ownerId":2}],"id":2},{"firstName":"Harold","lastName":"Davis","address":"563 Friendly St.","city":"Windsor","telephone":"6085553198","pets":[{"name":"Iggy","birthDate":"2010-11-30","type":{"name":"lizard","id":3},"id":5,"visits":[],"ownerId":4}],"id":4}]: 200 != 403
```

Last request: `GET http://localhost:9966/petclinic/api/owners?lastName=Davis`

**Steps to reproduce**
1. Given One session holds OWNER_ADMIN and another session holds no OWNER_ADMIN role
2. When GET /api/owners?lastName=Davis is called with the OWNER_ADMIN session
3. Then The response status is 200 and the Davis owners are listed
4. When GET /api/owners?lastName=Davis is called with the session lacking OWNER_ADMIN
5. Then The response status is 403 (access refused) and no owner list is returned

**Data**

```json
{
  "lastName": "Davis"
}
```

**Screenshot**: none (API test). **Robot log**: `results/EPIC-1/run-14/log.html`

## TC-EPIC-1-S3-08 Owner detail is refused without the OWNER_ADMIN role

**Rules**
- R-EPIC-1-S3-04: Owner page requires the OWNER_ADMIN role
  - expected: GET /api/owners/{ownerId} is only available to a user holding the OWNER_ADMIN role.
  - code: `src/main/java/org/springframework/samples/petclinic/rest/controller/v1/OwnerRestControllerV1.java#L89`

**Failed step (then)**: The response status is 403 (401 when the call carries no credentials), not 200

**Observed**

```
Unexpected status; body: {"firstName":"George","lastName":"Franklin","address":"110 W. Liberty St.","city":"Madison","telephone":"6085551023","pets":[{"name":"Leo","birthDate":"2010-09-07","type":{"name":"cat","id":1},"id":1,"visits":[],"ownerId":1}],"id":1}: 200 != 401
```

Last request: `GET http://localhost:9966/petclinic/api/owners/1`

**Steps to reproduce**
1. Given The seeded owner 1 exists and the caller does not hold the OWNER_ADMIN role
2. When GET /api/owners/1 is called as that user
3. Then The response status is 403 (401 when the call carries no credentials), not 200
4. Then The body holds no owner data (no firstName, lastName, pets)
5. Given The same owner and a session with the OWNER_ADMIN role (Authenticated as owner admin)
6. When GET /api/owners/1 is called as that user
7. Then The response status is 200 and the body holds owner 1 (George Franklin)

**Data**

```json
{
  "ownerId": 1
}
```

**Screenshot**: none (API test). **Robot log**: `results/EPIC-1/run-14/log.html`

## TC-EPIC-1-E-16 A user without OWNER_ADMIN cannot create, update, search or open owners

**Rules**
- R-EPIC-1-ST-1-10: Create and update of owners require the OWNER_ADMIN role
  - expected: POST /api/owners and PUT /api/owners/{id} are allowed only for users with role OWNER_ADMIN.
  - expected: A user without that role cannot create or change an owner.
  - code: `src/main/java/org/springframework/samples/petclinic/rest/controller/v1/OwnerRestControllerV1.java#L99`
  - code: `src/main/java/org/springframework/samples/petclinic/rest/controller/v1/OwnerRestControllerV1.java#L111`
- R-EPIC-1-ST-2-05: Owner search requires the OWNER_ADMIN role
  - expected: A user holding the OWNER_ADMIN role can search owners.
  - expected: A user without the OWNER_ADMIN role cannot search owners.
  - code: `src/main/java/org/springframework/samples/petclinic/rest/controller/v1/OwnerRestControllerV1.java#L74`
  - code: `src/main/java/org/springframework/samples/petclinic/rest/controller/v2/OwnerRestControllerV2.java#L32`
- R-EPIC-1-S3-04: Owner page requires the OWNER_ADMIN role
  - expected: GET /api/owners/{ownerId} is only available to a user holding the OWNER_ADMIN role.
  - code: `src/main/java/org/springframework/samples/petclinic/rest/controller/v1/OwnerRestControllerV1.java#L89`
- R-EPIC-1-ST-2-02: Search with no match returns not found
  - expected: When lastName=Nobody matches no owner, the response status is 404 with no body.
  - expected: No owner list is returned for the unmatched search.
  - code: `src/main/java/org/springframework/samples/petclinic/rest/controller/v1/OwnerRestControllerV1.java#L83`
  - code: `src/main/java/org/springframework/samples/petclinic/rest/controller/v1/OwnerRestControllerV1.java#L84`
  - code: `src/test/java/org/springframework/samples/petclinic/rest/controller/OwnerRestControllerV1Tests.java#L195`

**Failed step (then)**: The request is refused (401 or 403), not 201

**Observed**

```
Expected the request to be refused with 401 or 403, got 201; body: {"firstName":"George","lastName":"Zqegjnumak","address":"110 W. Liberty St.","city":"Madison","telephone":"6085551023","pets":[],"id":34}
```

Last request: `POST http://localhost:9966/petclinic/api/owners`

**Steps to reproduce**
1. Given Authenticated as a user without role OWNER_ADMIN
2. When POST /api/owners is sent with the valid payload
3. Then The request is refused (401 or 403), not 201
4. When PUT /api/owners/1 is sent with the update payload
5. Then The request is refused (401 or 403), not 204
6. When GET /api/owners?lastName=Franklin is called
7. Then The request is refused (401 or 403), no owner list is returned
8. When GET /api/owners/1 is called
9. Then The request is refused (401 or 403), no owner is returned
10. When As owner admin, GET /api/owners?lastName=<uniqueLast> and GET /api/owners/1 are called
11. Then The search returns 404 with no body (nothing was created) and owner 1 still has address 110 W. Liberty St., city Madison and telephone 6085551023

**Data**

```json
{
  "payload": {
    "firstName": "George",
    "lastName": "<uniqueLast>",
    "address": "110 W. Liberty St.",
    "city": "Madison",
    "telephone": "6085551023"
  },
  "updatePayload": {
    "firstName": "George",
    "lastName": "Franklin",
    "address": "1 Changed St.",
    "city": "Elsewhere",
    "telephone": "6085550000"
  }
}
```

**Screenshot**: none (API test). **Robot log**: `results/EPIC-1/run-14/log.html`
