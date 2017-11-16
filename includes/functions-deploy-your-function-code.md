## <a name="deploy-your-function-code"></a>A függvénykód létrehozása  

A függvénykódot többféleképpen is elkészítheti az új függvényalkalmazásban. Ez a témakör GitHub mintatárházához kapcsolódik. Az eddigiekhez hasonlóan a `<app_name>` helyőrzőt cserélje le a létrehozott függvényalkalmazás nevére. 

```azurecli-interactive
az functionapp deployment source config --name <app_name> --resource-group myResourceGroup --branch master \
--repo-url https://github.com/Azure-Samples/functions-quickstart \
--manual-integration 
```
A központi telepítés forrásának beállítása után az Azure parancssori felület információkat jeleníti meg a következőhöz hasonló (az olvashatóság eltávolított null értékeket):

```json
{
  "branch": "master",
  "deploymentRollbackEnabled": false,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/...",
  "isManualIntegration": true,
  "isMercurial": false,
  "kind": null,
  "name": "quickstart",
  "repoUrl": "https://github.com/Azure-Samples/functions-quickstart",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.Web/sites/sourcecontrols"
}
```
