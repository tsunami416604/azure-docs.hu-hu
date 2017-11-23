A Cloud Shellben az [az webapp create](/cli/azure/webapp#create) paranccsal hozzon létre egy [webalkalmazást](../articles/app-service/app-service-web-overview.md) a `myAppServicePlan` App Service-csomagban. 

Az alábbi példában cserélje le  *\<alkalmazás_neve >* egy globálisan egyedi alkalmazásnévvel rendelkező (érvényes karakterek: `a-z`, `0-9`, és `-`). 

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

Tallózással keresse meg az újonnan létrehozott webalkalmazáshoz.

```bash
http://<app_name>.azurewebsites.net
```
