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
