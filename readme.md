# Template for Full-stack Blazor + Web API

Serve a Web API and a Blazor project from the same ASP.NET Core project.

## Try it out - publish

Using bash / zsh:

```
$ dotnet tool install --global dotnet-ef
$ dotnet publish -c Release 
$ cd server
$ dotnet ef database update
$ mkdir bin/Release/net6.0/publish/bin
$ cp bin/database.db* bin/Release/net6.0/publish/bin
$ cd bin/Release/net6.0/publish
$ ./TodoApi
```

Explanation of the above commands:
- First install the Entity Framework cli
- Publish a release build of the project
- Go to server folder
- Create the database contents from migrations using the ef cli
- Create a folder for the database (sqlite)
- Copy database files to new folder
- Go to program folder
- Start the executable (TodoApi)

## GitHub Action workflow example

```yaml
# Docs for the Azure Web Apps Deploy action: https://github.com/Azure/webapps-deploy
# More GitHub Actions for Azure: https://github.com/Azure/actions

name: Build and deploy ASP.Net Core app to Azure Web App - krdo-todo-app

on:
  push:
    branches:
      - master
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v2

      - name: Set up .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: '6.0.x'
          include-prerelease: true
    
      - name: Update database
        run: |
          dotnet tool install --global dotnet-ef
          cd server
          dotnet tool restore
          dotnet ef database update

      - name: Build with dotnet
        run: | 
          dotnet build --configuration Release

      - name: dotnet publish
        run: dotnet publish -c Release -o ${{env.DOTNET_ROOT}}/myapp

      - name: Copy database
        run: |
          mkdir ${{env.DOTNET_ROOT}}/myapp/bin
          cd server
          cp bin/database.db* ${{env.DOTNET_ROOT}}/myapp/bin

      - name: Upload artifact for deployment job
        uses: actions/upload-artifact@v2
        with:
          name: .net-app
          path: ${{env.DOTNET_ROOT}}/myapp

  deploy:
    runs-on: ubuntu-latest
    needs: build
    environment:
      name: 'Production'
      url: ${{ steps.deploy-to-webapp.outputs.webapp-url }}

    steps:
      - name: Download artifact from build job
        uses: actions/download-artifact@v2
        with:
          name: .net-app

      - name: Deploy to Azure Web App
        id: deploy-to-webapp
        uses: azure/webapps-deploy@v2
        with:
          app-name: 'krdo-todo-app'
          slot-name: 'Production'
          publish-profile: ${{ secrets.AZUREAPPSERVICE_PUBLISHPROFILE_CAE8313A832C41A29B592AB00DEE15EE }}
          package: .
```