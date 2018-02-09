A Cloud Shellben az [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) paranccsal hozzon létre egy [webalkalmazást](../articles/app-service/app-service-web-overview.md) a `myAppServicePlan` App Service-csomagban. 

A következő példában cserélje ki az *\<app_name>* nevet egy globálisan egyedi névre (érvényes karakterek: `a-z`, `0-9` és `-`). 

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

Ezzel létrehozott egy üres webalkalmazást, engedélyezett Git üzemelő példánnyal.

> [!NOTE]
> A távoli Git URL-címe a `deploymentLocalGitUrl` tulajdonságban látható, a következő formátumban: `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`. Mentse el ezt az URL-t, később még szüksége lesz rá.
>

Tallózással keresse meg az újonnan létrehozott webalkalmazását.

```bash
http://<app_name>.azurewebsites.net
```
