# How it works

A general description of the approach. It deliberately leaves out prompts, settings and internal details.

## The idea

Teams write acceptance criteria, developers implement them, and testers write end-to-end tests by hand weeks later.
Three things tend to go wrong:

- the criteria and the code drift apart, so behaviour that exists only in the code is never tested;
- one big request to an AI over a whole Epic produces vague tests;
- nobody can say how much of the Epic the tests actually cover.

The approach here addresses each of these.

## The stages

**1. Cut the Epic into small pieces.** Each Story is read together with the part of the code that implements it, so
the AI works on a focused amount of material instead of the whole Epic at once.

**2. Rules before tests.** For each piece the tool first extracts *rules*: single, testable statements of behaviour.
Every rule records where it came from: the written criteria, the code, or both. Behaviour found only in the code is
marked so a person can decide whether it is intended. Requirements the code does not seem to implement are marked
too.

**3. Ask, never guess.** When the criteria and the code disagree, the tool tests what the code does and raises a
question for a person, with the options. Answers go back in through a spreadsheet, and only the rules and tests that
an answer touches are redesigned; everything else keeps its identity.

**4. Design the tests.** Tests are written in Given/When/Then form and each one names the rules it covers. A coverage
check makes sure no rule is left without a test, unless it is explicitly marked as not testable end to end, with a
reason.

**5. Generate the suite.** The designed tests become a Robot Framework suite, one reusable keyword per distinct step.
The suite is ordinary, readable Robot Framework that a team can own and edit.

**5b. Screens as well as the API.** When the application has screens, the tool reads their templates together with the back end, so the rules and tests can cover what the user sees: messages, buttons, navigation. Screen tests use one small, neutral set of actions (open a page, click, type, read text), and a single setting chooses the browser library underneath, Playwright or Selenium. The tests themselves do not change, so a client can keep the library they already use. Data for a test is prepared and removed through the API, which keeps screen tests fast and repeatable.

**6. Run and sort the failures.** The suite is run against the live application. Every failure is sorted:

- the environment could not be reached;
- a script mistake (wrong data, path or keyword);
- a product defect, meaning an assertion about behaviour failed.

**7. Repair script mistakes, never assertions.** Script mistakes are repaired automatically. A repair may not change
or remove an assertion. A product defect is never "fixed" by editing the test: it is reported.

**8. Report.** Product defects go into a defect report with the rules involved, what was expected, what was
observed, the steps to reproduce and the data used.

## Why this design

- **Rules first** makes coverage measurable and makes requirement-versus-code disagreements visible.
- **A person stays in the loop** at the one place where judgement is needed: the questions.
- **Assertions are protected**, so the automatic repair cannot hide a real bug.
- **Standard output** (Robot Framework, Markdown, JSON, a spreadsheet) means no lock-in.
