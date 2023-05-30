# Atlantis_Document
Atlantis

Atlantis is an application for automating Terraform via pull requests. It is deployed as a standalone application into your infrastructure. No third-party has access to your credentials.

Atlantis listens for GitHub, GitLab or Bitbucket webhooks about Terraform pull requests. It then runs terraform plan and comments with the output back on the pull request.

Why Atlantis??

1. Increased visibility

When everyone is executing Terraform on their own computers, it's hard to know the current state of your infrastructure:
Is what's in main branch deployed?
Did someone forget to create a pull request for that latest change?
What was the output from that last terraform apply?
With Atlantis, everything is visible on the pull request. You can view the history of everything that was done to your infrastructure.
