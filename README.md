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
  * Optionally the **USERS_NAMES** variable can be used. This must contain a comma separated list
    of mappings from GitHub username to display name in the format `"username" : "Display Name"`. If
    the GitHub username of the user triggering the workflow is listed, the specified display name
    will be used in the test results CSV instead of their username.
  * If the workflow is triggered manually, it will run tests using the installed version (as defined
    in the variable RC_INSTALLED_VERSION) of REDCap only. If triggered by pull request, it will run
    tests on both the installed version and the latest version.
  * Tests must be included in the repository in a *tests* folder. Test files must be pytest files as
    exported by Selenium IDE or a similar tool. Initialisation steps should be placed in a file
    named *init.py* and the actual tests must be named according to pytest conventions.
  * The test environment will be set up with 6 REDCap user accounts: `admin`, `user1`, `user2`,
    `user3`, `user4` and `user5`. Tests will begin logged in to the admin account. You can change to
    other accounts during tests as required. The password for all accounts is `abc123`.
  * If you need to upload a file during your test, use the `send_keys` command, targeted on a file
    upload field. The value passed to the `send_keys` command should be the path to a file within
    your repository. Prefix the path with `REPODIR/` to reference your repository root, for example
    to upload a file *hello.txt* in the *tests* folder, use `REPODIR/tests/hello.txt`.
  * Once tests are complete the results will be stored as CSV file(s) in an artifact and can be
    downloaded. Note that if a failure occurs in the initialisation stage there will be no test
    results. In the event a test fails the exception information, a screenshot and a HTML file
    representing the state of the page at the time of failure are stored in a separate artifact.
* [Release REDCap Module](files/release-redcap-module.yml)
  * This workflow will run when a pull request is merged into the main branch.
  * It will create a release automatically using the version number from the pull request title and
    the pull request description will be used as the release description. The version number must be
    in [semantic versioning](https://semver.org) format.
  * The release will include 2 zip files, the standard download zip and the REDCap module repo
    package. The module repo package is similar to the GitHub auto-generated zip file, but includes
    composer dependencies and excludes testing scripts.
