# Designed screen tests: excerpt

Four of the 50 tests the tool designed for the Owner screens of Spring PetClinic, copied from its generated file. Each names the rules it covers.

### TC-EPIC-4-ST-1-28 Telephone 60855a on the New Owner form shows the digits-only message and marks the input is-invalid

Priority **high**, type **ui**

Covers:
- R-EPIC-4-ST-1-02: Letters in telephone show an inline digits-only error

Preconditions:
- Login As Owner Admin

Data:

| field | value |
|---|---|
| telephone | 60855a |
| expectedMessages | ["Phone number only accepts digits", "Phone number must be exactly 10 digits"] |

Steps:
- **Given** The New Owner screen at /owners/add is open as OWNER_ADMIN with an untouched form
- **When** 60855a is typed into the telephone field
- **Then** Under the telephone field the message "Phone number only accepts digits" is visible
- **Then** Because 60855a is also only 6 characters, "Phone number must be exactly 10 digits" may show beside it; both are accepted
- **Then** The telephone input has the CSS class is-invalid

Drives: screen `New Owner`

### TC-EPIC-4-ST-2-10 A search with no match shows the not-found message, hides the table and the Add Owner button, and both return after a search that finds owners

Priority **high**, type **ui**, tags: CodeOnly

Covers:
- R-EPIC-4-ST-2-02: Search with no match shows a not-found message
- R-EPIC-4-ST-2-03: Last name search matches by prefix
- R-EPIC-4-ST-2-08: Add Owner button appears only with a loaded, non-empty result
- R-EPIC-4-E-06: With no owner found, the list offers no Add Owner button

Preconditions:
- Login As Owner Admin
- Owners With Last Name Davis Exist
- No Owner Named Nobody
- Open Owners Screen As Owner Admin

Data:

| field | value |
|---|---|
| noMatchTerm | Nobody |
| matchTerm | Davis |
| expectedMessage | No owners with LastName starting with "Nobody" |
| expectedOwners | ["Betty Davis", "Harold Davis"] |

Steps:
- **Given** The OWNER_ADMIN user is on /owners and the unfiltered list has finished loading
- **Then** The owners table and the 'Add Owner' button are visible below it
- **When** The user types 'Nobody' into the Last name field and submits 'Find Owner'
- **Then** The screen shows the text 'No owners with LastName starting with "Nobody"' (the wording says 'starting with')
- **Then** The owners table is not shown
- **Then** The 'Add Owner' button is not shown, so no new owner can be added from this result
- **When** The user replaces the term with 'Davis' and submits 'Find Owner'
- **Then** The owners table is shown with Betty Davis and Harold Davis
- **Then** The not-found message is no longer shown
- **Then** The 'Add Owner' button is shown again below the table

Drives: screen `Owners list (/owners)`

### TC-EPIC-4-ST-1-37 A failed update shows a red error banner on Edit Owner, stays on the screen, and a later successful submit navigates away

Priority **medium**, type **ui**, tags: CodeOnly

Covers:
- R-EPIC-4-ST-1-17: A failed update shows an error banner and stays on the edit screen

Preconditions:
- Login As Owner Admin
- Create Owner Via API
- Open Edit Owner Screen
- The first PUT /api/owners/{id} request from the browser is made to fail with status 500 by network interception; later requests pass through

Data:

| field | value |
|---|---|
| newAddress | 638 Cardinal Ave. |

Steps:
- **Given** The Edit Owner screen of the seeded owner George Franklin is open
- **When** The address is changed to 638 Cardinal Ave. and the submit button is pressed while the first PUT is set to fail
- **Then** A red alert with the error text is shown above the form
- **Then** The browser stays on /owners/{id}/edit and does not navigate to the owner page
- **When** The submit button is pressed again and this PUT reaches the server
- **Then** The browser navigates to the owner page /owners/{id} and shows the address 638 Cardinal Ave.

Drives: screen `Edit Owner`, endpoint `PUT /api/owners/{ownerId}`

### TC-EPIC-4-S3-13 Owner without pets shows the empty-state message and no pet entries

Priority **medium**, type **ui**, tags: CodeOnly

Covers:
- R-EPIC-4-S3-03: Owner without pets shows an empty-state message

Preconditions:
- Login As Owner Admin
- Create Owner

Data:

| field | value |
|---|---|
| owner | {"firstName": "Betty", "lastName": "Davis", "address": "638 Cardinal Ave.", "city": "Sun Prairie", "telephone": "6085551749"} |

Steps:
- **Given** An OWNER_ADMIN session and an owner Betty Davis who has no pets
- **When** The user opens the owner page /owners/{id} of that owner
- **Then** The page shows the text 'This owner has no pets yet.'
- **Then** No pet entry is shown under 'Pets and Visits'

Drives: screen `Owner page (/owners/{id})`
