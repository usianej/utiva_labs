# Azure App Service CI/CD Workflow with git Actions

## Local Environment setup

- Install git, and Azure Cli

> git can be installed by downloading and installing gitbash.

*https://learn.microsoft.com/en-us/cli/azure/install-azure-cli-windows?tabs=azure-cli*

> AzureCLI can be installed 

*https://learn.microsoft.com/en-us/cli/azure/install-azure-cli-windows?tabs=azure-cli*

## Git and GitHub Set Up

> git config --global user.name "FIRST_NAME LAST_NAME"

> git config --global user.email "your_email@example.com"

----
## Web App Creation 

- Create webapp in Azure 
- note the version and the type of webapp

## Git Hub

- Create a git hub repository 

    Make the repository public, add a git ignore file, add licence apache licence 2.0

- clone repositories into a new folder

    git clone ssh://john@example.com/path/to/my-project.git 
    cd my-project 

    
### For your secret (Generate deployment credentials)

> The recommended way to authenticate with Azure App Services for GitHub Actions is with a publish profile.

**If linux is being used, you have to set WEBSITE_WEBDEPLOY_USE_SCM**

Set WEBSITE_WEBDEPLOY_USE_SCM = true in Azure Management Portal settings (under Website → Configuration → Application Settings). Make sure false is in the value field.

1. Go to your app service in the Azure portal.

2. On the Overview page, select Get Publish profile.

3. Save the downloaded file. You'll use the contents of the file to create a GitHub secret

### Add secret to github

- set a secret on the github repository 

    In GitHub, browse your repository. Select Settings > Security > Secrets and variables > Actions > New repository secret.

    To use app-level credentials, paste the contents of the downloaded publish profile file into the secret's value field. Name the secret AZURE_WEBAPP_PUBLISH_PROFILE.


## Local App Creation 

> clone your git repository to your local workstation (copy the http link from your github) 

    git clone <link>

> On that new folder cloned fron github, cd into the folder and create your webapp

  dotnet new webapp

> create a new file on the new folder .github/workflows/main.yml

  The .yml file will be used by github actions 

> Get the version of your dotnet application and add it to your .yml file 

    dotnet --version 


---

## .yml file 

The .yml file is the deployment file.

Find the sample below.

----

```YAML

name: .NET Core CI

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app-name    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '3.1.x'           # set this to the dot net version to use

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # Checkout the repo
      - uses: actions/checkout@main
      
      # Setup .NET Core SDK
      - name: Setup .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ env.DOTNET_VERSION }} 
      
      # Run dotnet build and publish
      - name: dotnet build and publish
        run: |
          dotnet restore
          dotnet build --configuration Release
          dotnet publish -c Release --property:PublishDir='${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
          
      # Deploy to Azure Web apps
      - name: 'Run Azure webapp deploy action using publish profile credentials'
        uses: azure/webapps-deploy@v2
        with: 
          app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
          publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE  }} # Define secret variable in repository settings as per action documentation
          package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp'
```

## Git Commands 

To effectively push your changes to git, you need to enable workfows on the Personal Access Token being used. You could create a new PAT or add the workflow access to on that is being used.

> Select your profile, go to Settings -> Developer Settings > Personal Access Token > Token (classic)

> On git push, A git Action will be triggerd 

    git add .
    git commit -m "initial commit"
    git push