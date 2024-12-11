# NCTU Github Workflows

This repository contains GitHub Workflows which you can add to your repository. These workflows can
be triggered on pull request to ensure that the code passes checks before being merged into the main
branch.

The workflow files that you want to use will need to be placed into the `.github/workflows`
directory within your repository. The workflow files listed below call reusable workflows in this
repository, which allows for easy centralised maintenance of workflow code.

Please take note of the variables and secrets that need to be configured in order to run the
workflows.<br>
These are configured in the repository or organization settings:
*Secrets and Variables -> Actions*.<br><br>


* [REDCap External Module Security Scan](files/redcap-security-scan.yml)
  * This workflow requires the following variables and secrets:
    * Variable **RC_COMMUNITY_USERNAME** - Your username for the REDCap community
    * Secret **RC_COMMUNITY_PASSWORD** - Your password for the REDCap community
  * If your module uses system hooks, you will need to uncomment the line to set the RC_SYSTEM_HOOKS
    input to true. This will prevent the security scan from failing due to warning about the system
    hooks.
  * The security scan is always run using the latest version of REDCap.
* [REDCap External Module Tests](files/redcap-module-tests.yml)
  * This workflow requires the following variables and secrets:
    * Variable **RC_INSTALLED_VERSION** - The version of REDCap you are using
    * Variable **RC_COMMUNITY_USERNAME** - Your username for the REDCap community
    * Secret **RC_COMMUNITY_PASSWORD** - Your password for the REDCap community
  * If the workflow is triggered manually, it will run tests using the installed version (as defined
    in the variable RC_INSTALLED_VERSION) of REDCap only. If triggered by pull request, it will run
    tests on both the installed version and the latest version.
  * Tests must be included in the repository in a *tests* folder. Test files must be pytest files as
    exported by Selenium IDE or a similar tool. Initialisation steps should be placed in a file
    named *init.py* and the actual tests must be named according to pytest conventions.
  * If your pytest files reference the browser, using `webdriver.Chrome()` or `webdriver.Firefox()`,
    this must be replaced with `self.selectedBrowser`. Tests will be run on both Chrome and Firefox
    in parallel and must pass on both browsers to be considered a passing test.
  * The test environment will be set up with 4 REDCap user accounts: `admin`, `user`, `user2` and
    `user3`. Tests will begin logged in to the admin account. You can change to other accounts
    during tests as required. The password for all accounts is `abc123`.
* [Release REDCap Module](files/release-redcap-module.yml)
  * This workflow will run when a pull request is merged into the main branch.
  * It will create a release automatically using the version number from the pull request title and
    the pull request description will be used as the release description.
  * The release will include 2 zip files, the standard download zip and the REDCap module repo
    package. The module repo package is similar to the GitHub auto-generated zip file, but includes
    composer dependencies and excludes testing scripts.
