# Atlantis_Document
Atlantis

Atlantis is an application for automating Terraform via pull requests. It is deployed as a standalone application into your infrastructure. No third-party has access to your credentials.

Atlantis listens for GitHub, GitLab or Bitbucket webhooks about Terraform pull requests. It then runs terraform plan and comments with the output back on the pull request.

__________________________________________________________________________________________________________________________________
 Why Atlantis??

1. Increased visibility

   When everyone is executing Terraform on their own computers, it's hard to know the current state of your infrastructure:
  
   a. Is what's in main branch deployed?
   
   b. Did someone forget to create a pull request for that latest change?
  
   c. What was the output from that last terraform apply?
  
   With Atlantis,everything is visible on the pull request.You can view the history of everything that was done to your 
   infrastructure
 2. Enable collaboration with everyone
  
     You probably don't want to distribute Terraform credentials to everyone in your engineering organization, but now  
     anyone can open up a Terraform pull request.
   
     You can require approval before the pull request is applied so nothing happens accidentally.
  
 3. Review Terraform pull requests better
  
     You can't fully review a Terraform change without seeing the output of terraform plan. Now that output is added 
     to the pull request automatically.

 4. Standardize your workflows
  
     Atlantis locks a directory/workspace until the pull request is merged or the lock is manually deleted. This 
     ensures that changes are applied in the order expected.
   __________________________________________________________________________________________________________________________________
Setup Of Atlantis:

1. Install Terraform
    a. Download from https://developer.hashicorp.com/terraform/downloads 
    b. unzip path/to/terraform_*.zip -d /usr/local/bin

2. Download Atlantis
    https://github.com/runatlantis/atlantis/release

3. For Local server:- Download Ngrok
    a. Go to https://ngrok.com/download, download ngrok and unzip it.
    b. Start ngrok on port 4141 and take note of the hostname it gives you:
    c. ngrok http 4141
    d. URL="https://{YOUR_HOSTNAME}.ngrok.io"
    
4. Else if EC2 Server You can use its public ip.
    URL="http://public_ip:4141/events"
    
5. Create a random string 
    a. echo $RANDOM | md5sum | head -c 20; echo;
    b. SECRET="{YOUR_RANDOM_STRING}"

6. Add Webhook on Github
   a. Go to your repo's settings
   b. Select Webhooks or Hooks in the sidebar and Click Add webhook.
   c. set Payload URL with /events at the end. https://c5004d84.ngrok.io/events or http://ip:4141/events
   d. set Content type to application/json & Secret to your random string
   e. select Let me select individual events & check the boxes
       Pull request reviews
       Pushes
       Issue comments
       Pull requests
       leave Active checked
       click Add webhook

