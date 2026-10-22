# Willa (Flask edition)

IST 303 Team Project: Fall 2026

## Team


| Member     | Roles     |
| ---------- | --------- |
| Bobby Cruz | Developer |


## Concept

Willa is a privacy-first legal assistant that helps people bring their own claims in South Africa's Small Claims Court, without a lawyer. It takes a claimant from "someone owes me money" to a correct letter of demand. It checks whether the court can hear the claim, applies the law in code rather than leaving it to guesswork, and bases every document on the Small Claims Courts Act and its Rules. Nothing the claimant types is stored or sent anywhere.

The bigger question behind Willa: how much of the small-claims process can run with as few people involved as the law allows? Willa keeps a person exactly where the law requires one: the sworn affidavit, the clerk issuing the summons, and the hearing.

This course project rebuilds Willa in Flask, one tested module at a time. Milestone 1.0 covers the first steps a claimant takes:

1. **Check eligibility** against the Small Claims Courts Act (sections 7, 15 and 16), with the reason and the fix for every refusal.
2. **Find the right court** in a database of Small Claims Courts.
3. **See the deadline**: the day the 14-day demand period ends.
4. **Draft the letter of demand** (Form J993) from the claimant's answers.



### Data store

The app uses an SQLite database, accessed through Python's `sqlite3` module. It holds public reference data only, so claimants' details never reach it.


| Table    | Holds                                                  | Source                                                                                   |
| -------- | ------------------------------------------------------ | ---------------------------------------------------------------------------------------- |
| `courts` | Court name, also known as, places of sitting, province | Department of Justice lower-courts list (justice.gov.za), with the date it was collected |




## Stakeholders


| Stakeholder                     | Interest in Willa                                                               |
| ------------------------------- | ------------------------------------------------------------------------------- |
| Claimant (plaintiff)            | Primary user: a person owed up to R30,000 who wants to claim without a lawyer   |
| Defendant                       | Receives the letter of demand, which needs to be clear and correct              |
| Clerk of the Small Claims Court | Helps claimants and issues the summons; benefits when claimants arrive prepared |
| Commissioner                    | Hears the case and relies on correct paperwork                                  |
| Developer (Bobby Cruz)          | Builds, tests and maintains the app                                             |




## Milestone 1.0

One milestone made of two 3-week iterations. Milestone 1.0 is demonstrated in class on **12 November 2026**.


| Iteration   | Dates           | Goal                                                                                    |
| ----------- | --------------- | --------------------------------------------------------------------------------------- |
| Iteration 1 | 1 Oct – 22 Oct  | Set up the project and write the eligibility rules, with tests                          |
| Iteration 2 | 23 Oct – 12 Nov | Eligibility form, court finder (database), deadline, letter of demand, privacy test, CI |




## User stories and tasks

Every user story is written from the claimant's side. Priority: **Must** is needed for Milestone 1.0; **Should** is planned but the first to drop if time runs short. Each story's estimate is the sum of its tasks, in hours.

### US1: Check eligibility

*As a claimant, I want Willa to tell me whether the Small Claims Court can hear my claim, so I don't prepare papers for the wrong court.*

Priority: Must · Estimate: 10.25 h · Iterations 1 and 2

**Acceptance criteria:** Willa refuses when the claimant is not a natural person (s7(1)), the amount is over R30,000 (s15), or the matter is excluded (s16). Every refusal names the section and the correction the claimant can make. All 26 statutory test cases pass.

The first four tasks set up the project, because the eligibility check needs an app to run in.


| Task | Description                                                                                   | Est (h) | Iteration | Owner      |
| ---- | --------------------------------------------------------------------------------------------- | ------- | --------- | ---------- |
| 1.1  | Create GitHub repo `ist-303-audit`; add instructor as collaborator                      | 0.25    | 1         | Bobby Cruz |
| 1.2  | Python 3.12 virtual environment, `requirements.txt` (flask, pytest, pytest-cov), `.gitignore` | 0.5     | 1         | Bobby Cruz |
| 1.3  | App factory `create_app()` and a home page                                                    | 1       | 1         | Bobby Cruz |
| 1.4  | pytest setup and first test (home page returns 200)                                           | 0.5     | 1         | Bobby Cruz |
| 1.5  | Write the jurisdiction rules as pure Python functions in `willa/rules.py`                     | 2       | 1         | Bobby Cruz |
| 1.6  | Write the 26 statutory test cases in `tests/test_rules.py`                                    | 1.5     | 1         | Bobby Cruz |
| 1.7  | Claim form (Jinja template), POST route and result page                                       | 3       | 2         | Bobby Cruz |
| 1.8  | Route tests with Flask's test client                                                          | 1.5     | 2         | Bobby Cruz |




### US2: Find my court

*As a claimant, I want to find the Small Claims Court for my area, so I know where to lodge my claim.*

Priority: Must · Estimate: 8.5 h · Iteration 2

**Acceptance criteria:** searching by town or court name returns matching courts with province and places of sitting, read from the database. The page shows where the data came from and when it was collected. Searches are not stored.


| Task | Description                                                                               | Est (h) | Iteration | Owner      |
| ---- | ----------------------------------------------------------------------------------------- | ------- | --------- | ---------- |
| 2.1  | `schema.sql`, `db.py` (`get_db`, `close_db`) and an `init-db` command                     | 2       | 2         | Bobby Cruz |
| 2.2  | Build `data/courts.csv` from the justice.gov.za lower-courts list; record source and date | 3       | 2         | Bobby Cruz |
| 2.3  | Court search route and template                                                           | 2       | 2         | Bobby Cruz |
| 2.4  | Tests using a temporary database                                                          | 1.5     | 2         | Bobby Cruz |




### US3: Know my deadline

*As a claimant, I want to see the date my 14-day demand period ends, so I know when I may go to the clerk.*

Priority: Must · Estimate: 4.5 h · Iteration 2

**Acceptance criteria:** given the date the defendant received the demand, Willa shows the end date using the counting rule confirmed by the legal advisor. Month-end, weekend and public-holiday cases are tested.


| Task | Description                                          | Est (h) | Iteration | Owner      |
| ---- | ---------------------------------------------------- | ------- | --------- | ---------- |
| 3.1  | Confirm the day-counting rule with the legal advisor | 0.5     | 2         | Bobby Cruz |
| 3.2  | `willa/deadlines.py`: demand period end date         | 1.5     | 2         | Bobby Cruz |
| 3.3  | Edge-case tests                                      | 1.5     | 2         | Bobby Cruz |
| 3.4  | Show the deadline on the result page                 | 1       | 2         | Bobby Cruz |




### US4: Draft my letter of demand

*As a claimant, I want Willa to fill in my letter of demand (Form J993), so I can send a correct demand.*

Priority: Should · Estimate: 5.5 h · Iteration 2

**Acceptance criteria:** the letter is filled in from the claimant's answers, with no AI involved. Missing fields are listed, never invented. The page prints cleanly.


| Task | Description                                       | Est (h) | Iteration | Owner      |
| ---- | ------------------------------------------------- | ------- | --------- | ---------- |
| 4.1  | J993 template; fill it in and list missing fields | 2.5     | 2         | Bobby Cruz |
| 4.2  | Tests: every field lands; missing fields reported | 1.5     | 2         | Bobby Cruz |
| 4.3  | Print-friendly letter page                        | 1.5     | 2         | Bobby Cruz |




### US5: Nothing I type is kept

*As a claimant, I want nothing I type to be kept, so using Willa doesn't expose my dispute.*

Priority: Must · Estimate: 2.5 h · Iteration 2

**Acceptance criteria:** a test runs the whole flow with unique "canary" values, then searches the database and log files. None of the values appear.


| Task | Description                                                 | Est (h) | Iteration | Owner      |
| ---- | ----------------------------------------------------------- | ------- | --------- | ---------- |
| 5.1  | Canary test across database and logs                        | 2       | 2         | Bobby Cruz |
| 5.2  | Confirm no request bodies are logged; note it in the README | 0.5     | 2         | Bobby Cruz |


**Stretch goals (Milestone 2.0):** affidavit of delivery (Form J994); AI-drafted particulars of claim through a local model; translation into South African languages; hosting behind Cloudflare Tunnel and Access; summons preparation sheet; sheriff lookup.

## Technical tasks

Work that supports every story rather than one.


| Task | Description                                                     | Est (h) | Iteration | Owner      |
| ---- | --------------------------------------------------------------- | ------- | --------- | ---------- |
| T.1  | GitHub Actions workflow: run `pytest --cov=willa` on every push | 1.5     | 2         | Bobby Cruz |
| T.2  | README: how to run the app, run the tests and report coverage   | 0.5     | 2         | Bobby Cruz |




## Course deliverables


| Task | Description                                                                        | Est (h) | Iteration | Owner      |
| ---- | ---------------------------------------------------------------------------------- | ------- | --------- | ---------- |
| C.1  | Part A: this README                                                                | 1.5     | 1         | Bobby Cruz |
| C.2  | Part B: burndown, stand-ups, retrospective, planned vs completed, Iteration 2 plan | 1.5     | 1         | Bobby Cruz |
| C.3  | Part C: slides and live demo script in `Part C/`                                   | 3       | 2         | Bobby Cruz |
| C.4  | Part C: `MILESTONE_2.0.md`                                                         | 1       | 2         | Bobby Cruz |




## Summary


| Work                          | Priority | Estimate (h) | Iteration 1 (h) | Iteration 2 (h) |
| ----------------------------- | -------- | ------------ | --------------- | --------------- |
| US1 Check eligibility         | Must     | 10.25        | 5.75            | 4.5             |
| US2 Find my court             | Must     | 8.5          | 0               | 8.5             |
| US3 Know my deadline          | Must     | 4.5          | 0               | 4.5             |
| US4 Draft my letter of demand | Should   | 5.5          | 0               | 5.5             |
| US5 Nothing I type is kept    | Must     | 2.5          | 0               | 2.5             |
| Technical tasks               | Must     | 2            | 0               | 2               |
| Course deliverables           | Must     | 7            | 3               | 4               |
| **Total**                     |          | **40.25**    | **8.75**        | **31.5**        |




## Iteration 1 plan

**Features in Iteration 1:** the first part of US1. That means setting up the project (tasks 1.1 to 1.4) and writing the eligibility rules with their tests (1.5 and 1.6), so the logic is proven before any web form is built. Course deliverables C.1 and C.2 also fall in this iteration.

**Velocity.** This is the first iteration, so there's no measured velocity yet.

## Task allocation

All tasks are allocated to myself as I'm audting the course.
