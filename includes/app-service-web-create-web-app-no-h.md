A Cloud Shellben az [az webapp create](/cli/azure/webapp#create) paranccsal hozzon létre egy [webalkalmazást](../articles/app-service/app-service-web-overview.md) a `myAppServicePlan` App Service-csomagban. 

A webalkalmazás üzemeltetési tárterületet biztosít a kódhoz, valamint megadja az üzembe helyezett alkalmazás megtekintéséhez szükséges URL-címet.

Az alábbi parancsban cserélje ki az *\<app_name>* nevet egy egyedi névre (érvényes karakterek: `a-z`, `0-9` és `-`). Ha az `<app_name>` nem egyedi, a következő hibaüzenet jelenik meg: „A megadott <alkalmazás_neve> névvel már létezik webhely.” A webalkalmazás alapértelmezett URL-címe `https://<app_name>.azurewebsites.net`. 

```azurecli-interactive
az webapp create --name <app_name> --resource-group myResourceGroup --plan myAppServicePlan --deployment-local-git
```

A webalkalmazás létrehozása után az Azure CLI az alábbi példához hasonló információkat jelenít meg:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

Egy üres web app alkalmazásban létrehozott git telepítés engedélyezve van.

> [!NOTE]
> A Git távoli URL-CÍMÉT is megjelennek a `deploymentLocalGitUrl` tulajdonság formátumban `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`. Az URL-cím akkor menteni, mert később szüksége.
>

Az újonnan létrehozott webapp megtekintéséhez tallózással keresse meg a helyet.

```bash
http://<app_name>.azurewebsites.net
```
