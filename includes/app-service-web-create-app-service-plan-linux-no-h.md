A felhő rendszerhéj az erőforráscsoport egy App Service-csomag létrehozása a [az App Service-csomagot hozzon létre](/cli/azure/appservice/plan#create) parancsot.

<!-- [!INCLUDE [app-service-plan](app-service-plan-linux.md)] -->

Az alábbi példakód létrehozza az App Service-csomag nevű `myAppServicePlan` a a **szabványos** tarifacsomag (`--sku S1`) és a Linux-tárolóban (`--is-linux`).

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