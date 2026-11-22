# UI Testing Lab (`ui-lab`)

Automated UI tests for Selenium's web form and QuickPizza using **Part 3: Robot Framework** and **Part 4: Cypress**.

## Tool Parts

### `robot/` (Part 3: Robot Framework)
Holds the Robot Framework test suites (`first.robot`, `web_form.robot`, `pizza_scenario.robot`), the QuickPizza keyword resource file (`resources/quickpizza.resource`), `requirements.txt`, and evidence from two headless runs in `results/run1/` and `results/run2/`.

Run all Robot Framework tests (from `robot/` with `CHROMEDRIVER` set and `.venv` active):
```bash
robot -v BROWSER:headlesschrome .
```

### `cypress/` (Part 4: Cypress)
Holds the Cypress configuration (`cypress.config.js`), specs (`cypress/e2e/web-form.cy.js`, `cypress/e2e/pizza-scenario.cy.js`), custom admin login command (`cypress/support/commands.js`), `package.json`, `package-lock.json`, and run evidence in `cypress/screenshots/` and `cypress/videos/`.

Run all Cypress tests (from `cypress/` after `npm install`):
```bash
npx cypress run
```
