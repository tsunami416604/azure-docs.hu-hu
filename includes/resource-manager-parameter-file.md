A paraméterfájl használata paraméterértékek felelt meg a telepítés során, ha szüksége hozzon létre egy JSON-fájl formátuma nem a következőhöz hasonló:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "webSiteName": {
            "value": "ExampleSite"
        },
        "webSiteHostingPlanName": {
            "value": "DefaultPlan"
        },
        "webSiteLocation": {
            "value": "West US"
        },
        "adminPassword": {
            "reference": {
               "keyVault": {
                  "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
               }, 
               "secretName": "sqlAdminPassword" 
            }   
        }
   }
}
```

A paraméter-fájl mérete nem lehet több mint 64 KB.

Meg kell adnia a kényes értéket a paraméterhez (például jelszót), ha hozzá kulcstároló ezt az értéket. Olvashatók be a key vault üzembe helyezése során, mert az előző példában látható módon. További információkért lásd: [biztonságos értéket átadni a telepítés során](../articles/azure-resource-manager/resource-manager-keyvault-parameter.md). 

