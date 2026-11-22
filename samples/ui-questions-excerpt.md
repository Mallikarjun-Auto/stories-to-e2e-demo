# Questions raised on the screens: excerpt

The written requirement and the real screens disagree in several places. The tool asks instead of guessing. Three of the 12 questions for the Owner screens. In this demo the answers were proposed by Claude from what the screens do; a product owner would confirm or change them.

## Q-EPIC-4-03 (disagreement)

The criteria say "when Save is clicked". The buttons are labelled "Add Owner" and "Update Owner", and the button is disabled while the form is invalid (owner-add.component.html#L120), so with a telephone containing letters the click cannot submit.

Options:
- Treat "Save" as the Add Owner/Update Owner button, and for S1.2 assert the button is disabled plus the inline error.
- Keep the button enabled so that a click on an invalid form shows errors, and change the code.

## Q-EPIC-4-04 (disagreement)

S2.2 says the message "has not been found" is shown. The screen shows 'No owners with LastName starting with "<term>"'. The phrase 'has not been found' does not appear in the code slice.

Options:
- The code is right: test for 'No owners with LastName starting with "Nobody"' and update the criterion.
- The criterion is right: the screen text must be changed to a message containing 'has not been found'.

## Q-EPIC-4-09 (disagreement)

S1.2 expects the inline error "Telephone must be 10 digits". The screen has no such text. Letters give "Phone number only accepts digits", wrong length gives "Phone number must be exactly 10 digits", and several messages can show at once (e.g. 60855a has both pattern and length errors).

Options:
- Assert the messages the code shows.
- Change the screen so a single message "Telephone must be 10 digits" is shown for any invalid telephone.
