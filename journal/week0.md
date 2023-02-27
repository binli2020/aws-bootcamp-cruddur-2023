# Week 0 — Billing and Architecture

## pre and on 14 Feb 2023
### Team Related
* Created an Organization for our team (AWS_ANZ) https://github.com/ANZ-AWS/
* Created the team shared repo in the above organization. https://github.com/ANZ-AWS/aws-bootcamp-cruddur-2023
* Invited some of the team members to the organization

### Some Prerequisite work
* Created a GitPod account using my GitHub account. Added the GitPod Chromn extension. Tried from inside a GitHub repo to open an IDE on GitPod, it worked.
* Created a GitHub Codespace Account
* Althoug I already have a free-tier AWS account, I watched the video to review the process.
* Created a free-tier Lucid Chart account
* Created a HoneyComb account using my GitHub account
* Created a Rollbar account using my GitHub account

### Week 0 tasks
* Reviewed Week 0 - Live Streamed Video
* Watched Chirag's Week 0 - Spend Considerations
* Created a Billing Alarm & Budget
* Created an AWS admin user
* Used AWS CloudShell


### Getting the AWS CLI Working

We'll be using the AWS CLI often in this bootcamp,
so we'll proceed to installing this account.


#### Install AWS CLI

- We are going to install the AWS CLI when our Gitpod enviroment lanuches.
- We are are going to set AWS CLI to use partial autoprompt mode to make it easier to debug CLI commands.
- The bash commands we are using are the same as the [AWS CLI Install Instructions]https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html


Update our `.gitpod.yml` to include the following task.

```sh
tasks:
  - name: aws-cli
    env:
      AWS_CLI_AUTO_PROMPT: on-partial
    init: |
      cd /workspace
      curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
      unzip awscliv2.zip
      sudo ./aws/install
      cd $THEIA_WORKSPACE_ROOT
```

We'll also run these commands indivually to perform the install manually

#### Set Env Vars

We will set these credentials for the current bash terminal
```
export AWS_ACCESS_KEY_ID=<access_key_id>    (note there is no double quotes surronding the key)
export AWS_SECRET_ACCESS_KEY=<secret_access_key>    (note there is no double quotes surronding the key)
export AWS_DEFAULT_REGION=us-east-1
```

We'll tell Gitpod to remember these credentials if we relaunch our workspaces
```
gp env AWS_ACCESS_KEY_ID=""
gp env AWS_SECRET_ACCESS_KEY=""
gp env AWS_DEFAULT_REGION=us-east-1
```

#### Check that the AWS CLI is working and you are the expected user

```sh
aws sts get-caller-identity
```

You should see something like this:
```json
{
    "UserId": "AIFBZRJIQN2ONP4ET4EK4",
    "Account": "655602346534",
    "Arn": "arn:aws:iam::655602346534:user/andrewcloudcamp"
}
```

### Continue Week 0 tasks
* Finished watching security related YT video and quiz. (27/2)
* Finished watching Lucid related YT video and drawing the [logical architecture diagram](https://lucid.app/lucidchart/be914bd1-dfc7-47be-9874-40595505c521/edit?viewport_loc=-976%2C-112%2C2255%2C1031%2C0_0&invitationId=inv_14c871df-0c35-494e-b9b3-c025ee06bbbf) (28/2)
![Screenshot of the Logical Architecture Diagram](https://user-images.githubusercontent.com/71969513/221689458-7595652a-34f4-4719-952b-8b516c159c40.png)

* 
