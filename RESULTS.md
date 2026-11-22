# UI lab results

Tool parts that I chose: Part 3 (Robot Framework) and Part 4 (Cypress)

## Part 0
- Step 0.1, Chrome version: `Google Chrome 158.0.8127.36 (Official Build) (arm64)`
- Step 0.3, ChromeDriver version: `ChromeDriver 158.0.8127.36`
- Step 0.4, locator of Pizza, Please!: `button[name="pizza-please"]` (1 match)
- Step 0.4, locator of Excluded tools: `select` (1 match)
- Step 0.4, locator of Must be vegetarian: `#default-checkbox` (1 match)
- Step 0.4, locator of Custom Pizza Name: `input#pizza-name` (1 match)
- Step 0.5, pizza name, tool and calories of the run by hand: `Lab Pizza 20261122-200058`, tool `Scissors`, calories per slice `500`

| Step | Actual result | Pass or fail |
|------|---------------|--------------|
| 1 | Heading `Looking to break out of your pizza routine?` appeared | Pass |
| 2 | Max Calories per Slice, Excluded tools, Must be vegetarian, and Custom Pizza Name appeared | Pass |
| 3 | Fields held `600`, `Knife`, vegetarian ticked (`True`), and `Lab Pizza 20261122-200058` | Pass |
| 4 | Recommendation showed `Lab Pizza 20261122-200058`, `Tool: Scissors`, `Calories per slice: 500` | Pass |
| 5 | `Please log in first.` appeared | Pass |
| 6 | Signed in on `/admin` and `Latest pizza recommendations` appeared | Pass |
| 7 | `Lab Pizza 20261122-200058 (tool=Scissors, ingredients_number=7) (newest)` appeared | Pass |

## Part 3: Robot Framework
- `robot --version`: `Robot Framework 7.5.1 (Python 3.12.10 on darwin)`
- Step 3.2, failure message for the wrong title: `Title should have been 'Web Form' but was 'Web form'.`
- Step 3.8, run 1, result, elapsed time and pizza name: `PASS` (`1 test, 1 passed, 0 failed`), elapsed `6.632317` s, pizza name `Lab Pizza 20261122-200315`
- Step 3.8, run 2, result, elapsed time and pizza name: `PASS` (`1 test, 1 passed, 0 failed`), elapsed `6.580864` s, pizza name `Lab Pizza 20261122-200337`

## Part 4: Cypress
- `node --version`, `npm --version` and `npx cypress version`: `v24.18.0`, `12.0.1`, `16.2.1`
- Step 4.3, failure message for the wrong title: `Timed out retrying after 10000ms` (`-'Web form'` vs `+'Web Form'`)
- Step 4.6, tool and calories in the answer of `POST /api/pizza`: `Pizza cutter`, `450` calories per slice
- Step 4.7, the line of the admin list that shows your pizza: `Lab Pizza 20261122-201021 (tool=Pizza cutter, ingredients_number=6)` (appeared second below another user's `(newest)` pizza in run 2)
- Step 4.8, run 1, result, duration and pizza name: `All specs passed!` (`2 passing`), total `00:12` (`pizza-scenario.cy.js` `00:10`, `web-form.cy.js` `00:02`), screenshots replaced by run 2
- Step 4.8, run 2, result, duration and pizza name: `All specs passed!` (`2 passing`), total `00:27` (`pizza-scenario.cy.js` `00:10`, `web-form.cy.js` `00:16`), pizza name `Lab Pizza 20261122-201021`
- Step 4.9 (optional), browser and result: Ran full multi-spec suite in Electron 146 (`2 passing`); Chrome 158 passed `pizza-scenario.cy.js` (`7s`) and `web-form.cy.js` (`9s`) individually, but hit Cypress 16 CDP `Error: * is not a valid URL` when switching origins across specs in one Chrome 158 run.

## Part B
1. All three number boxes on QuickPizza share the same `id="floating_filled"`, so `#floating_filled` matched 3 elements in Step 0.4 instead of 1. Because of that, the tests locate `Max Calories per Slice` from its visible `<label>` text and step to the sibling `<input>`. Giving each input its own unique `id` (such as `id="max-calories"`) would let a test target the box directly by `id`, which stays stable even if the label wording or DOM structure changes.
2. Step 7 uses a **consistency check** (comparing what two parts of the system report for the same pizza). It would catch a fault where QuickPizza displays one tool in the customer recommendation UI but saves or renders a different tool in the backend/admin list (for example, ignoring `excludedTools` or mixing up tool IDs between the recommendation response and the stored record).
3. QP-01 could check that every ingredient in the recommendation list is vegetarian. Because the rendered HTML `<li>` items under `Ingredients:` only show ingredient names and not a `vegetarian` flag, the test would need either the `POST /api/pizza` JSON response (which Cypress already captures with `cy.intercept('POST', '/api/pizza')` and includes `ingredients[].vegetarian === true` and `vegetarian: true`) or a list of known non-vegetarian ingredients to assert against.
4. Robot Framework required explicit `Wait Until ...` keywords (such as `Wait Until Element Is Visible css:h1` and `Wait Until Page Contains Element id:svelte-announcer`) with `timeout=10s`, whereas Cypress automatically retried every `cy.get()`, `cy.contains()`, and `.should()` for up to `defaultCommandTimeout: 10000`. Both tools let me build QP-01 quickly once the locators were known, though Cypress was faster to inspect because `cy.intercept` let me assert on the `/api/pizza` payload directly. Robot Framework produced a detailed step-by-step HTML `log.html` and `report.html` (`~6.6s` per run) with inline screenshots, while Cypress produced `.mp4` videos and full-page PNGs (`~10s` for `pizza-scenario.cy.js`).
5. I would choose **Cypress** for the automated UI tests of Project 2. First, its built-in retryability (`defaultCommandTimeout`) eliminates manual `Wait Until ...` boilerplate for dynamic pages, which keeps tests shorter and less flaky. Second, `cy.intercept()` lets a UI test verify both the browser UI and the underlying API request/response in the same test, and `video: true` records full-run video evidence automatically.
6. (Part 3) If QuickPizza changed the locator of the calories box, I would update the single `${MAX CALORIES}` variable in `robot/resources/quickpizza.resource`. Because `pizza_scenario.robot` only calls high-level keywords like `Fill In The Pizza Request` and never contains raw CSS or XPath locators, the test file itself would not need any edits.
7. (Part 4) Cypress clears cookies before every `it()` block (`testIsolation`), and QuickPizza stores the `AWSALB` sticky-session cookie so that `/admin` hits the same backend instance that created the customer's pizza. If steps 6 and 7 were placed in a separate `it()` test, Cypress would clear the `AWSALB` cookie first, so `/admin` could be routed to a different backend server that does not have `Lab Pizza ...` in its list and step 7 would fail.
