# Playwright

Docs:
- <https://playwright.dev>
- <https://github.com/microsoft/playwright>

Why:

- Support for all browsers
- Fast and reliable execution
- Powerful automation capabilities
- Integrates with your workflow

# Comparison to Cypress

Playwright is simialar to Cypress in it's API and if you know Cypress, you can easily learn Playwright.

Some benefirts of Playwright over Cypress:

- Speed, this issue https://github.com/cypress-io/cypress/issues/22868 killed our test suites - now it has been fixed (I think) but I haven't tested it (because we already ported our test suite to Playwright)
- TypeScript support has always been better in Playwright, I believe it still is
- Playwrights explicit async is better than Cypress implicit async which has caused pain here and there
- Playwrights Locator makes it possible to do user centric testing without using cypress-testing-library (https://playwright.dev/docs/locators)
- Playwrights dev tools are ok and do the job, I liked Cypress test runner more though
- Configuring and extending Playwright has been easier than Cypress
- Playwrights multi browser support has always been better, although Cypress has improved it lately
- Video / trace recording is easy and fast
- CI/CD integration is easy
- VSCode plugin is available and tests can be run from the editor
- Playwrights second mover advantage, it has always done the same things as Cypress but better

- Cypress also has evolved and e.g. there's new methods to clear cookies now
