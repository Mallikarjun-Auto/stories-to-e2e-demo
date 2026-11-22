# Questions raised: excerpt

Where the written story and the code disagree, the tool does not guess: it asks. Three of the 15 questions it raised for the Owners Epic. In this demo the answers were proposed by Claude from what the code does; a product owner would confirm or change them.

## Q-EPIC-1-01 (disagreement)

The criterion expects the message "Telephone must be 10 digits". The code has the entity message "Phone number must be exactly 10 digits" (Owner.java#L47), and the REST layer returns a schema validation error on field telephone, not either text. Which text must the tests assert?

Options:
- Assert "Telephone must be 10 digits" on the screen (screen code not in slice)
- Assert "Phone number must be exactly 10 digits" as the code has it
- Assert only the 400 status and the field name telephone

## Q-EPIC-1-02 (disagreement)

The OpenAPI contract for updateOwner lists 200 with the Owner body as the success response. The code returns 204 (OwnerRestControllerV1.java#L124) and the test expects 204. Which status is correct for a successful update?

Options:
- 204 as the code has it
- 200 with the owner body as in the OpenAPI contract

## Q-EPIC-1-05 (disagreement)

S2.2 says the message "has not been found" is shown. The code slice only returns HTTP 404 with an empty body from GET /api/owners. Where is the message text produced, and should the test check the API 404 or the screen message?

Options:
- Check the screen message "has not been found" (UI layer, outside this slice)
- Check only the API 404 with an empty body
- Both: 404 at the API and the message on the screen
