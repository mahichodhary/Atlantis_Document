# Atlantis_Document
Atlantis
Atlantis is an application for automating Terraform via pull requests. It is deployed as a standalone application into your infrastructure. No third-party has access to your credentials.

Atlantis listens for GitHub, GitLab or Bitbucket webhooks about Terraform pull requests. It then runs terraform plan and comments with the output back on the pull request.
