# Azure App Service CI/CD Workflow with git Actions

## Local Environment setup

- Install git, and Azure Cli

> git can be installed by downloading and installing gitbash.

*https://learn.microsoft.com/en-us/cli/azure/install-azure-cli-windows?tabs=azure-cli*

> AzureCLI can be installed 

*https://learn.microsoft.com/en-us/cli/azure/install-azure-cli-windows?tabs=azure-cli*

## Git and GitHub Set Up

git config --global user.name "FIRST_NAME LAST_NAME"

git config --global user.email "your_email@example.com"

----
## Web App Creation 

- Create webapp in Azure 

## Git Hub

- Create a git hub repository 

    Make the repository public, add a git ignore file, add licence apache licence 2.0

- clone repositories into a new folder

    git clone ssh://john@example.com/path/to/my-project.git 
    cd my-project 

    
### For your secret, you use a service principle 

For this go to cloud shell (bash)

> After the Installation of cloud-cli, you will be able to run Azure CLI commands

To login to azure cli 

- To login and access your resource groups run the folowing commands

    az login 

    az group list 

> get the resource group that you need to permit 

simillar to this:

output: /subscriptions/f2eca35d-0a61-4937-a650-59e8910a8fb0/resourceGroups/demoapp

> az ad sp create-for-rbac --name {myApp} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{MyResourceGroup} --sdk-auth

Example:

    az ad sp create-for-rbac -n newwebapp021 --role contributor --scope "/subscriptions/e77db16d-5ea4-4932-b1f7-066315e7b399/resourceGroups/classdemo5" --sdk-auth

Deployment credentials can also be gotten with other methods because the sdk-auth is depracated 

> https://learn.microsoft.com/en-us/azure/app-service/deploy-github-actions?tabs=userlevel#generate-deployment-credentials


> Add secret to git hub

- set a secret on the github repository 

  setting - secrets - Actions - New repository  Secret 

## Local App Creation 

> On that new folder create your webapp

  dotnet new webapp

> create a new file on the new folder .github/workflows/main.yml

  The .yml file will be used by github actions 

> dotnet --version 

this is used to check the specific version 

---

## .yml file 

The .yml file is the deployment file.

Find the sample below.

----

    name: Deploy ASP.NET CORE

    on:
    push:
        branches: 
        - main

    env:
    AZURE_WEBAPP_NAME: newwebapp021
    AZURE_WEBAPP_PATH: '.'
    DOTNET_VERSION: '7.0.100'

    jobs:
    build-and-deploy:
        runs-on: ubuntu-latest
        steps:
        
        - uses: actions/checkout@main

        - name: Login via Azure CLI
            uses: azure/login@v1
            with:
            creds: ${{ secrets.azure_credentials }}

        - name: SETUP .NET CORE
            uses: actions/setup-dotnet@v1
            with:
            dotnet-version: ${{ env.DOTNET_VERSION }}

        - name: dotnet build + publish
            run: |
            dotnet build --configuration release
            dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PATH }}/myapp'

        - name: Deploy to App Service 
            uses: azure/webapps-deploy@v2
            with:
            app-name: ${{ env.AZURE_WEBAPP_NAME }}
            package : '${{ env.AZURE_WEBAPP_PATH }}/myapp'

## Git Commands 

> On git push, A git Action will be triggerd 

    git add .
    git commit -m "initial commit"
    git push