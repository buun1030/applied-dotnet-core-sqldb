# Deploy an ASP.NET Core and SQL Database app to App Service

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
az webapp up 
    --name <app-name> \
    --runtime "DOTNET|6.0" \
    --os-type linux --sku F1 \
    --plan <plan-name> \
    --resource-group <rg-name> \
    --location centralus
```
These commands include az group create, az appservice plan create, and az webapp create.
```
You can launch the app at http://<app-name>.azurewebsites.net
```

## 3 - Create the database
Create SQL Server to host the database.
```console
az sql server create \
    --location centralus \
    --resource-group <rg-name> \
    --name <server-name> \
    --admin-user <db-username> \
    --admin-password <db-password>
```
Create a database.
```console
az sql db create \
    --resource-group <rg-name> \
    --server <server-name> \
    --name coreDb
```
Add the following firewall rule to our database server to allow other Azure resources to connect to it.
```console
az sql server firewall-rule create \
    --resource-group <rg-name> \
    --server <server-name> \
    --name AzureAccess \
    --start-ip-address 0.0.0.0 \
    --end-ip-address 0.0.0.0
```

## 4 - Connect the App to the Database
Retrieve the Connection String for our database using the following command.
```console
az sql db show-connection-string \
    --client ado.net \
    --name coreDb \
    --server <your-server-name>
```
Assign the Connection String to our App Service using the command below.
```console
az webapp config connection-string set \
    -g <rg-name> \
    -n <your-app-name> \
    -t SQLServer \
    --settings MyDbConnection=<your-connection-string>
```
MyDbConnection is the name of the Connection String in our appsettings.json file, which means it gets loaded by our app during startup.

## 5 - Generate the Database Schema
To generate our database schema, we need to set up a firewall rule on our Database Server. This rule allows our local computer to connect to Azure.
```console
az sql server firewall-rule create 
    --resource-group msdocs-core-sql \
    --server <yoursqlserver> \
    --name LocalAccess \
    --start-ip-address <your-local-computer-ip> \
    --end-ip-address <your-local-computer-ip>
```
Update the appsettings.json file in our local app code with the Connection String of our Azure SQL Database. The update allows us to run migrations locally against our database hosted in Azure. Replace the username and password placeholders with the values you chose when creating your database.
```json
"ConnectionStrings": {
    "MyDbConnection": "Server=tcp:coredbserver456.database.windows.net,1433;Initial Catalog=coreDb;Persist Security Info=False;User ID=<username>;Password=<password>;Encrypt=True;TrustServerCertificate=False;"
}
```
run the following commands to install the necessary CLI tools for Entity Framework Core. Create an initial database migration file and apply those changes to update the database:
```console
dotnet tool install -g dotnet-ef \
dotnet ef migrations add InitialCreate \
dotnet ef database update
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
