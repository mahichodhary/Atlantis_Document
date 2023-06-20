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
    
    b. sudo unzip path/to/terraform_*.zip -d /usr/local/bin

2. Download Atlantis

    https://github.com/runatlantis/atlantis/releases/tag/v0.24.2

3. For Local server:- Download Ngrok

    a. Go to https://ngrok.com/download, download ngrok and unzip it.
    
    b. Start ngrok on port 4141 and take note of the hostname it gives you:
    
    c. ngrok http 4141
    
    d. URL="https://{YOUR_HOSTNAME}.ngrok.io"
    
4. Else if EC2 Server You can use its public ip.

    URL="http://public_ip:4141"
    
5. Create a random string 

    a. echo $RANDOM | md5sum | head -c 20; echo;
    
    b. SECRET="{YOUR_RANDOM_STRING}"

6. Add Webhook on Github

   a. Go to your repo's settings
   
   b. Select Webhooks or Hooks in the sidebar and Click Add webhook.
   
   c. set Payload URL with /events at the end. https://c5004d84.ngrok.io/events or http://ip:4141/events
   
   d. set Content type to application/json & Secret to your random string
   
   e. select Let me select individual events & check the boxes:
   
       Pull request reviews
       Pushes
       Issue comments
       Pull requests
   d. leave Active checked
   
   f. click Add webhook
  
8. GitHub or GitHub Enterprise Access Token

   a. Create a Personal Access Token
   
   b. create a token with repo scope
   
   c. set the token as an environment variable
   
   d. TOKEN="{YOUR_TOKEN}"
   
   e. USERNAME="{the username of your GitHub, GitLab or Bitbucket user}"
  
   f. REPO_ALLOWLIST="$YOUR_GIT_HOST/$YOUR_USERNAME/$YOUR_REPO"
   
   #For other enterprise access token you can visit https://www.runatlantis.io/guide/testing-locally.html#create-an-access-token-for-atlantis 
   
9.Now you can start Atlantis by using GitHub Command:
   
      ./atlantis server \
      --atlantis-url="$URL" \
      --gh-user="$USERNAME" \
      --gh-token="$TOKEN" \
      --gh-webhook-secret="$SECRET" \
      --repo-allowlist="$REPO_ALLOWLIST"
      
   #To start atlantis server for other enterprise you can visit https://www.runatlantis.io/guide/testing-locally.html#start-atlantis    
 
_________________________________________________________________________________________________________________________________    

Workflow Diagram:      
        
![Atlantis_Workflow](https://github.com/mahichodhary/Atlantis_Document/assets/130489002/bf4ca3f5-31eb-4af7-8253-50cfc20c6a69)


__________________________________________________________________________________________________________________________________


Server Side Config:

A Server-Side Config file is used for more groups of server config that can't reasonably be expressed through flags.

You do not need a server-side repo config file unless you want to customize some aspect of Atlantis on a per-repo basis.

To use server side repo config create a config file, ex. repos.yaml, and pass it to the atlantis server command via the --repo-config.
      
      repos:
      - id: /.*/  
        branch: /.*/ 
        repo_config_file: path/to/atlantis.yaml
        plan_requirements: [approved]
        apply_requirements: [approved]
        allowed_overrides: [apply_requirements, workflow, delete_source_branch_on_merge, repo_locking]
-id:
   #### id can either be an exact repo ID or a regex.
   #### using a regex, it must start and end with a slash.
   #### Repo ID's are of the form {VCS hostname}/{org}/{repo name}, ex. github.com/runatlantis/atlantis.
  
-branch:
  #### branch is an regex matching pull requests by base branch (using this you can specify particular branch)
  #### (the branch the pull request is getting merged into).
  #### By default, all branches are matched
  
repo_config_file:
  #### repo_config_file specifies which repo config file to use for this repo.
  #### By default, atlantis.yaml is used.
  
plan_requirements:
  #### plan_requirements sets the Plan Requirements for all repos that match.

apply_requirements:
  #### apply_requirements sets the Apply Requirements for all repos that match.
  
allowed_overrides specifies:
#### allowed_overrides specifies which keys can be overridden by this repo in its atlantis.yaml file.

__________________________________________________________________________________________________________________________________
  
## Multiple Atlantis Servers Handle The Same Repository
Running multiple Atlantis servers to handle the same repository can be done to separate permissions for each Atlantis server. In this case, a different atlantis.yaml repository config file can be used by using different repos.yaml files.

For example, consider a situation where a separate production-server atlantis uses repo config atlantis-production.yaml and staging-server atlantis uses repo config atlantis-staging.yaml.

Each server has different permissions and a different repos.yaml file.

The repos.yaml contains repo_config_file key to specify the repository atlantis config file 

    ## repos.yaml 
      repos: 
      - id: /.*/ 
        # for production-server
        repo_config_file: atlantis-production.yaml
        # for staging-server
        # repo_config_file: atlantis-staging.yaml
        
Then, create atlantis-production.yaml and atlantis-staging.yaml files in the repository .

__________________________________________________________________________________________________________________________________

### Repo Level atlantis.yaml Config
An atlantis.yaml file specified at the root of a Terraform repo allows you to instruct Atlantis on the structure of your repo and set custom workflows.
 
#### For Module atlantis.yaml:

    .
    ├── modules
    │   └── module1
    │       ├── main.tf
    │       ├── outputs.tf
    │       └── submodule
    │           ├── main.tf
    │           └── outputs.tf
    └── project1
         └── main.tf
  
If you want Atlantis to plan project1/ whenever any .tf files under module1/ change or any .tf or .tfvars files under project1/ change you could use the following configuration for atlantis.yaml:

    version: 3
    projects:
    - dir: project1 
      autoplan:
        when_modified: ["../modules/**/*.tf", "*.tf*",]
________________________________________________________________________________________________________________________________________________________        
#### For Custom Workflow
Custom workflows can be specified in the Server-Side Repo Config or in the Repo-Level atlantis.yaml files.

         .
    └── project1
        ├── main.tf
        ├── production.tfvars
        └── staging.tfvars

    # repos.yaml or atlantis.yaml
    workflows:
      staging:
        plan:
          steps:
          - init
          - plan:
              extra_args: ["-var-file", "staging.tfvars"]
        apply:
          steps:
            - apply:
                extra_args: ["-var-file", "production.tfvars"]      
      production:
        plan:
          steps:
          - init
          - plan:
              extra_args: ["-var-file", "production.tfvars"]
        apply:
          steps:
            - apply:
                extra_args: ["-var-file", "production.tfvars"]
Commands for workspace: 
                           
    atlantis plan -p project1-staging  
    atlantis plan -p project1-production
    
     
##### If multiple atlantis.yaml file with different configuration in same repository you want to use a particular atlantis.yaml give path of the file at server-side in repos.yaml (repo_config_file: path/to/atlantis.yaml) 


##### When module and workspace are used together:

          version: 3
          projects:
          - dir: project1
            workspace: staging
          - dir: project1
            workspace: production
            
With the above config, when Atlantis determines that the configuration for the project1 dir has changed, it will run plan for both the staging and production workspaces.

If you want to run plan or apply for a specific workspace using directory you can use

    atlantis plan -w staging -d project1
    atlantis plan -w production -d project1



