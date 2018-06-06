# hello-cloud-app-service-docker

Uses the Spotify plugin to build and push a Docker image.

Then the azure-webapp-maven-plugin is used to deploy the image from ACR to App Service.

Create a registry
```
az acr create --admin-enabled --resource-group HelloCloud --location westus --name hellocloudregistry --sku Basic
```

Get credential for registry 
```
az acr credential show --name hellocloudregistry --query passwords[0]
```

Add the registry and credentials in POM.XML

      <server>
           <id>YOUR_REGISTRY_NAME.azurecr.io</id>
           <username>YOUR_REGISTRY_NAME</username>
           <password>PASSWORD_FROM ABOVE</password>
       </server>
     
       