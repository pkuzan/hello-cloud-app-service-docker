# hello-cloud-app-service-docker

Uses the Spotify plugin to build and push a Docker image.

Then the azure-webapp-maven-plugin is used to deploy the image from ACR to App Service.


## Setup
Create a registry
```
az login
az acr create --admin-enabled --resource-group HelloCloud --location westus --name hellocloudregistry --sku Basic
```

Get credential for registry 
```
az acr credential show --name hellocloudregistry --query passwords[0]
```

Add the registry and credentials in settings.xml

      <server>
           <id>YOUR_REGISTRY_NAME.azurecr.io</id>
           <username>YOUR_REGISTRY_NAME</username>
           <password>PASSWORD_FROM ABOVE</password>
       </server>
     
The Azure maven plugin needs to authenticate with Azure, a Service Principal is used for this purpose. 
```
az account show --query "{subscriptionId:id, tenantId:tenantId}"
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/YOUR_SUBSCRIPTION_ID;"
```

It will respond :
```
{
  "appId": "APP_ID",
  "displayName": "NAME",
  "name": "http://NAME",
  "password": "PASSWORD",
  "tenant": "TENNANT_ID"
}
```

In your Maven settings.xml add the following, replacing the placeholders with your values, this 
will be used to authenticate the plugin.

```
<servers> 
   <server>
     <id>azure-auth</id>
      <configuration>
         <client>APP_ID</client>
         <tenant>TENNANT_ID</tenant>
         <key>PASSWORD</key>
         <environment>AZURE</environment>
      </configuration>
   </server>
</servers> 
```     
You'll end up with 2 servers defined in setteings.xml, one for the Spotify plugin
the other for the Azure plugin.

## Workflow

* Perform setup as above
* dockerfile:build
    * Build the Docker image
* dockerfile:push
    * Push the image to ACR
* azure-webapp:deploy
    * Deploys the image from ACR to App Service     
       