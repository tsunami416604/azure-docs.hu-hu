A Cloud Shellben az [az webapp create](/cli/azure/webapp#az_webapp_create) paranccsal hozzon létre egy [webalkalmazást](../articles/app-service/containers/app-service-linux-intro.md) a `myAppServicePlan` App Service-csomagban. 

Az alábbi példában cserélje le `<app_name>` egy globálisan egyedi alkalmazásnévvel rendelkező (érvényes karakterek: `a-z`, `0-9`, és `-`). A futtatókörnyezet értéke `dotnetcore|1.1`. Minden támogatott futtatókörnyezetek megtekintéséhez futtassa [az webalkalmazás lista-futtatókörnyezetek](/cli/azure/webapp#az_webapp_list_runtimes). 

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "dotnetcore|1.1" --deployment-local-git
```

Ha a webalkalmazás létrejött, az Azure parancssori felület kimenetét mutatja be az alábbi példához hasonló:

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

Létrehozott egy üres webalkalmazás Linux tároló, a git-telepítés engedélyezve van.

> [!NOTE]
> A Git távoli URL-CÍMÉT is megjelennek a `deploymentLocalGitUrl` tulajdonság formátumban `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`. Az URL-cím akkor menteni, mert később szüksége.
>
