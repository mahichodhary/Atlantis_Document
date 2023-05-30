# Atlantis_Document
Atlantis

Atlantis is an application for automating Terraform via pull requests. It is deployed as a standalone application into your infrastructure. No third-party has access to your credentials.

Atlantis listens for GitHub, GitLab or Bitbucket webhooks about Terraform pull requests. It then runs terraform plan and comments with the output back on the pull request.


 Why Atlantis??

 1.Increased visibility

  When everyone is executing Terraform on their own computers, it's hard to know the current state of your infrastructure:
  
  a. Is what's in main branch deployed?
  
  b. Did someone forget to create a pull request for that latest change?
  
  c. What was the output from that last terraform apply?
  
  With Atlantis,everything is visible on the pull request.You can view the history of everything that was done to your infrastructure
  
  2.Enable collaboration with everyone
  
  You probably don't want to distribute Terraform credentials to everyone in your engineering organization, but now anyone can open up a Terraform pull request.
  You can require approval before the pull request is applied so nothing happens accidentally.
  
  3.Review Terraform pull requests better
  
  You can't fully review a Terraform change without seeing the output of terraform plan. Now that output is added to the pull request automatically.

  4.Standardize your workflows
  
  Atlantis locks a directory/workspace until the pull request is merged or the lock is manually deleted. This ensures that changes are applied in the order expected.

