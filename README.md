# applied-dotnet-core-sqldb
Apply ASP.NET Core with SQL DB tutorial by myself from azure docs

## 1 - Set up the simple application
```console
git clone https://github.com/Azure-Samples/msdocs-app-service-sqldb-dotnetcore.git
cd msdocs-app-service-sqldb-dotnetcore
```

## 2 - Publish your web app
```console
az login
```

Create a webapp and deploy code from a local workspace to the app. The command is required to run from the folder where the code is present.
```console
az webapp up --name <app-name> --runtime "DOTNET|6.0" --os-type linux --sku F1 --plan <plan-name> --resource-group <rg-name> --location centralus
```
These commands include az group create, az appservice plan create, and az webapp create.

```console
You can launch the app at http://<app-name>.azurewebsites.net
```

## 3 - Create the database
```console
```

## 4 - Connect the App to the Database
```console
```

## 5 - Generate the Database Schema
```console
```

## (Optional) Update the app and redeploy
Example: In the local directory, open the Index.cshtml file:
```html
<div class="jumbotron">
    <h1>Welcome 💜</h1>
    <p class="lead">Example .NET app to Azure App Service.</p>
</div>
```
Save your changes, then redeploy the app
```console
az webapp up --os-type linux
```
This command uses values that are cached locally in the .azure/config file, including the app name, resource group, and App Service plan.

## (Optional) Clean up resources
```console
az group delete --name <rg-name>
```
