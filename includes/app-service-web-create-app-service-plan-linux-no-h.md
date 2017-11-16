A Cloud Shellben hozzon létre egy App Service-csomagot az [az appservice plan create](/cli/azure/appservice/plan#create) paranccsal.

[!INCLUDE [app-service-plan](app-service-plan-linux.md)]

Az alábbi példa egy `myAppServicePlan` nevű App Service-csomag létrehozását mutatja be a **Standard** tarifacsomagban és Linux-tárolóban:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku S1 --is-linux
```

Az App Service-csomag létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg:

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
```