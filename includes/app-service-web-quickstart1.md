## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

 Az [Azure-erőforráscsoport](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups) egy logikai tároló, amelyben erőforrások, például webalkalmazások és adatbázisok kezelhetők. Az erőforráscsoportban található erőforrásokat üzembe helyezheti, frissítheti és törölheti.

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#create) paranccsal.

```azurecli
az group create --name myResourceGroup --location westeurope
```

Az elérhető helyek megjelenítéséhez használja az `az appservice list-locations` parancsot. Az erőforrásokat általában a közelében található régiókban hozhatja létre.

## <a name="create-an-azure-app-service-plan"></a>Azure App Service-csomag létrehozása

Hozzon létre egy „INGYENES” [App Service-csomagot](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) az [az appservice plan create](/cli/azure/appservice/plan#create) paranccsal.

[!INCLUDE [app-service-plan](app-service-plan.md)]

Az alábbi parancs egy `quickStartPlan` nevű App Service-csomagot hoz létre az **Ingyenes** tarifacsomag használatával.

```azurecli
az appservice plan create --name quickStartPlan --resource-group myResourceGroup --sku FREE
```

Az App Service-csomag létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg:

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "quickStartPlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/quickStartPlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "quickStartPlan",
  < JSON data removed for brevity. >
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
```

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása