---
title: "Az Azure Resource Manager sablonfüggvényei - erőforrások |} Microsoft Docs"
description: "Az Azure Resource Manager-sablonok segítségével erőforrásokra vonatkozó értékek lekérését funkcióit ismerteti."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/22/2018
ms.author: tomfitz
ms.openlocfilehash: f92afd27540e935ed901151d980377b9b34ea8f5
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2018
---
# <a name="resource-functions-for-azure-resource-manager-templates"></a>Az Azure Resource Manager sablonokhoz erőforrás-funkciók

Erőforrás-kezelő a következő funkciókat biztosít erőforrás értékek beolvasása:

* [listKeys és a {Value} lista](#listkeys)
* [szolgáltatók](#providers)
* [reference](#reference)
* [resourceGroup](#resourcegroup)
* [resourceId](#resourceid)
* [előfizetést](#subscription)

Ahhoz, hogy az értékeket a paraméterek, változók vagy a jelenlegi üzemelő példány, lásd: [központi telepítési érték funkciók](resource-group-template-functions-deployment.md).

<a id="listkeys" />
<a id="list" />

## <a name="listkeys-and-listvalue"></a>listKeys és a {Value} lista
`listKeys(resourceName or resourceIdentifier, apiVersion)`

`list{Value}(resourceName or resourceIdentifier, apiVersion)`

Minden erőforrástípus, amely támogatja a list művelet értékeket adja vissza. A leggyakoribb használata `listKeys`. 

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| resourceName vagy resourceIdentifier |Igen |karakterlánc |Az erőforrás egyedi azonosítója. |
| apiVersion |Igen |karakterlánc |API-verzió erőforrás futásidejű állapot. Általában a következő formátumban **éééé-hh-nn**. |

### <a name="return-value"></a>Visszatérési érték

A visszaadott objektumot listKeys formátuma a következő:

```json
{
  "keys": [
    {
      "keyName": "key1",
      "permissions": "Full",
      "value": "{value}"
    },
    {
      "keyName": "key2",
      "permissions": "Full",
      "value": "{value}"
    }
  ]
}
```

Más lista függvények visszatérési formátumuk eltérő. Egy függvény formátum megtekintéséhez foglalja bele a kimenetek szakaszban látható módon a példa sablont. 

### <a name="remarks"></a>Megjegyzések

Bármely művelet kezdetű **lista** használható legyen a sablon egy függvényt. A rendelkezésre álló műveletek közé tartozik, nem csak listKeys, de az is, például műveletek `list`, `listAdminKeys`, és `listStatus`. Azonban nem használható **lista** a kérelem törzsében szereplő értékeket igénylő műveletekhez. Például a [lista fiók SAS](/rest/api/storagerp/storageaccounts#StorageAccounts_ListAccountSAS) kérelemtörzs-paraméterrel, például a művelethez *signedExpiry*, így a sablonon belül nem használható.

Annak meghatározásához, mely rendelkezik a list művelet, a következő lehetőségei vannak:

* Nézet a [REST API-műveleteket](/rest/api/) az erőforrás-szolgáltató és listázási műveletei keressen. Például, storage-fiókok vannak a [listKeys művelet](/rest/api/storagerp/storageaccounts#StorageAccounts_ListKeys).
* Használja a [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) PowerShell-parancsmagot. Az alábbi példa lekérdezi valamennyi listázási műveletei storage-fiókok:

  ```powershell
  Get-AzureRmProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* A következő Azure CLI paranccsal csak a lista műveletek szűrése:

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

Adja meg az erőforrás használatával vagy a [resourceId függvény](#resourceid), vagy a formátum `{providerNamespace}/{resourceType}/{resourceName}`.

### <a name="example"></a>Példa

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/listkeys.json) visszaállítása az elsődleges és másodlagos kulcsok a tárfiók a kimenetek szakaszban látható.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageAccountName": { 
          "type": "string"
      }
  },
  "resources": [
    {
      "name": "[parameters('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {
      "referenceOutput": {
          "type": "object",
          "value": "[listKeys(parameters('storageAccountName'), '2016-12-01')]"
      }
    }
}
``` 

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/listkeys.json --parameters storageAccountName=<your-storage-account>
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/listkeys.json -storageAccountName <your-storage-account>
```

<a id="providers" />

## <a name="providers"></a>szolgáltatók
`providers(providerNamespace, [resourceType])`

Egy erőforrás-szolgáltató és a támogatott erőforrástípusai információt ad vissza. Erőforrástípus nincs megadva, a függvény a támogatott típusok esetében az erőforrás-szolgáltató.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| providerNamespace |Igen |karakterlánc |A szolgáltató Namespace |
| resourceType |Nem |karakterlánc |A típusú erőforrás a megadott névtérben. |

### <a name="return-value"></a>Visszatérési érték

Minden támogatott típust ad vissza a következő formátumban: 

```json
{
    "resourceType": "{name of resource type}",
    "locations": [ all supported locations ],
    "apiVersions": [ all supported API versions ]
}
```

Tömb által visszaadott érték sorrendje nem garantált.

### <a name="example"></a>Példa

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json) a szolgáltató funkció használatát ismerteti:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "providerNamespace": {
            "type": "string"
        },
        "resourceType": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "providerOutput": {
            "value": "[providers(parameters('providerNamespace'), parameters('resourceType'))]",
            "type" : "object"
        }
    }
}
```

Az a **Microsoft.Web** erőforrás-szolgáltató és **helyek** erőforrástípus, az előző példában objektum beállítása/beolvasása a következő formátumban:

```json
{
  "resourceType": "sites",
  "locations": [
    "South Central US",
    "North Europe",
    "West Europe",
    "Southeast Asia",
    ...
  ],
  "apiVersions": [
    "2016-08-01",
    "2016-03-01",
    "2015-08-01-preview",
    "2015-08-01",
    ...
  ]
}
```

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/providers.json --parameters providerNamespace=Microsoft.Web resourceType=sites
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/providers.json -providerNamespace Microsoft.Web -resourceType sites
```

<a id="reference" />

## <a name="reference"></a>Hivatkozás
`reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])`

Az erőforrás futásidejű állapot képviselő objektum beállítása/beolvasása.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| resourceName vagy resourceIdentifier |Igen |karakterlánc |Név vagy egy erőforrás egyedi azonosítója. |
| apiVersion |Nem |karakterlánc |A megadott erőforrás API-verzió. Ez a paraméter tartalmazza, amikor az erőforrás nincs kiépítve belül ugyanazt a sablont. Általában a következő formátumban **éééé-hh-nn**. |
| "Teljes" |Nem |karakterlánc |Érték, amely megadja, hogy térjen vissza a teljes erőforrás-objektum. Ha nincs megadva `'Full'`, csak az erőforrás tulajdonságai objektumot ad vissza. A teljes objektum tartalmaz értékeket, például az erőforrás-azonosító és helyét. |

### <a name="return-value"></a>Visszatérési érték

Minden erőforrástípus adja vissza a hivatkozás függvény különböző tulajdonságai. A függvény nem ad vissza egy egyetlen, előre meghatározott formátumban. Emellett a visszaadott érték eltér a teljes objektum megadott e. Erőforrástípus tulajdonságainak megtekintéséhez a kimenetek szakaszában, a példában látható módon adja vissza az objektum.

### <a name="remarks"></a>Megjegyzések

A hivatkozás függvény az értékét a futásidejű állapot osztályból származik, és ezért nem használható a változók szakaszban. A sablon kimenetének részében használható vagy [csatolt sablon](resource-group-linked-templates.md#link-or-nest-a-template). A kimenetek szakaszában nem használható egy [beágyazott sablon](resource-group-linked-templates.md#link-or-nest-a-template). Egy beágyazott sablon üzembe helyezett erőforrás értékek visszaállításához a beágyazott sablon átalakítása csatolt sablont. 

A hivatkozás függvény használatával, akkor implicit módon deklarálja, hogy egy erőforrás függ-e egy másik erőforrás, ha a hivatkozott erőforrás ugyanazt a sablont belül lett beállítva. Nem kell a dependsOn tulajdonság is használhatja. A függvény a rendszer nem értékeli ki, a hivatkozott erőforrás telepítés befejeződéséig.

Lásd: a tulajdonság és erőforrástípus tartozó értékek, hozzon létre egy sablont, amely a kimenetek szakaszban adja vissza az objektum. Ha az adott típusú erőforrással rendelkezik, a sablon bármely új erőforrások telepítése nélkül a objektum beállítása/beolvasása. 

Általában akkor használják a **hivatkozás** működnek, mint egy adott értéket visszaadásának egy objektumot, például a blob-végpont URI vagy teljesen minősített tartománynevét.

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01').primaryEndpoints.blob]",
        "type" : "string"
    },
    "FQDN": {
        "value": "[reference(concat('Microsoft.Network/publicIPAddresses/', parameters('ipAddressName')), '2016-03-30').dnsSettings.fqdn]",
        "type" : "string"
    }
}
```

Használjon `'Full'` Ha erőforrás értékek, amelyek nem szerepelnek a Tulajdonságok séma van szükség. Például kulcstároló hozzáférési szabályzatainak beállításához identitás tulajdonságainak beolvasása a virtuális gép.

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "properties": {
    "tenantId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.tenantId]",
    "accessPolicies": [
      {
        "tenantId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.tenantId]",
        "objectId": "[reference(concat('Microsoft.Compute/virtualMachines/', variables('vmName')), '2017-03-30', 'Full').identity.principalId]",
        "permissions": {
          "keys": [
            "all"
          ],
          "secrets": [
            "all"
          ]
        }
      }
    ],
    ...
```

A fenti sablon átfogó példát talál [Key Vault a Windows](https://github.com/rjmax/AzureSaturday/blob/master/Demo02.ManagedServiceIdentity/demo08.msiWindowsToKeyvault.json). Egy hasonló példa érhető el [Linux](https://github.com/rjmax/AzureSaturday/blob/master/Demo02.ManagedServiceIdentity/demo07.msiLinuxToArm.json).

### <a name="example"></a>Példa

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json) erőforrás telepíti, és hivatkozik arra, hogy az erőforrás.

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageAccountName": { 
          "type": "string"
      }
  },
  "resources": [
    {
      "name": "[parameters('storageAccountName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-12-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {
      "referenceOutput": {
          "type": "object",
          "value": "[reference(parameters('storageAccountName'))]"
      },
      "fullReferenceOutput": {
        "type": "object",
        "value": "[reference(parameters('storageAccountName'), '2016-12-01', 'Full')]"
      }
    }
}
``` 

Az előző példában a két objektum adja vissza. A Tulajdonságok objektum a következő formátumban kell megadni:

```json
{
   "creationTime": "2017-10-09T18:55:40.5863736Z",
   "primaryEndpoints": {
     "blob": "https://examplestorage.blob.core.windows.net/",
     "file": "https://examplestorage.file.core.windows.net/",
     "queue": "https://examplestorage.queue.core.windows.net/",
     "table": "https://examplestorage.table.core.windows.net/"
   },
   "primaryLocation": "southcentralus",
   "provisioningState": "Succeeded",
   "statusOfPrimary": "available",
   "supportsHttpsTrafficOnly": false
}
```

A teljes objektum a következő formátumban kell megadni:

```json
{
  "apiVersion":"2016-12-01",
  "location":"southcentralus",
  "sku": {
    "name":"Standard_LRS",
    "tier":"Standard"
  },
  "tags":{},
  "kind":"Storage",
  "properties": {
    "creationTime":"2017-10-09T18:55:40.5863736Z",
    "primaryEndpoints": {
      "blob":"https://examplestorage.blob.core.windows.net/",
      "file":"https://examplestorage.file.core.windows.net/",
      "queue":"https://examplestorage.queue.core.windows.net/",
      "table":"https://examplestorage.table.core.windows.net/"
    },
    "primaryLocation":"southcentralus",
    "provisioningState":"Succeeded",
    "statusOfPrimary":"available",
    "supportsHttpsTrafficOnly":false
  },
  "subscriptionId":"<subscription-id>",
  "resourceGroupName":"functionexamplegroup",
  "resourceId":"Microsoft.Storage/storageAccounts/examplestorage",
  "referenceApiVersion":"2016-12-01",
  "condition":true,
  "isConditionTrue":true,
  "isTemplateResource":false,
  "isAction":false,
  "provisioningOperation":"Read"
}
```

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/referencewithstorage.json --parameters storageAccountName=<your-storage-account>
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/referencewithstorage.json -storageAccountName <your-storage-account>
```

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/reference.json) egy tárfiókot, amely nincs telepítve Ez a sablon hivatkozik. A tárfiók már létezik az ugyanazon erőforráscsoporton belül.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountName": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "ExistingStorage": {
            "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')), '2016-01-01')]",
            "type" : "object"
        }
    }
}
```

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/reference.json --parameters storageAccountName=<your-storage-account>
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/reference.json -storageAccountName <your-storage-account>
```

<a id="resourcegroup" />

## <a name="resourcegroup"></a>resourceGroup
`resourceGroup()`

A jelenlegi erőforráscsoportban képviselő objektumot adja vissza. 

### <a name="return-value"></a>Visszatérési érték

A visszaadott objektumot a következő formátumban kell megadni:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

### <a name="remarks"></a>Megjegyzések

A közös a resourceGroup függvény használata erőforrások létrehozásához és az erőforráscsoport ugyanazon a helyen. Az alábbi példában az erőforráscsoport helye rendelje hozzá a webhelyhez tartozó hely.

```json
"resources": [
   {
      "apiVersion": "2016-08-01",
      "type": "Microsoft.Web/sites",
      "name": "[parameters('siteName')]",
      "location": "[resourceGroup().location]",
      ...
   }
]
```

### <a name="example"></a>Példa

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json) az erőforráscsoport tulajdonságainak adja vissza.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "resourceGroupOutput": {
            "value": "[resourceGroup()]",
            "type" : "object"
        }
    }
}
```

A fenti példában egy objektum a következő formátumban adja vissza:

```json
{
  "id": "/subscriptions/{subscription-id}/resourceGroups/examplegroup",
  "name": "examplegroup",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/resourcegroup.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/resourcegroup.json 
```

<a id="resourceid" />

## <a name="resourceid"></a>resourceId
`resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2]...)`

Az erőforrás egyedi azonosítójának beolvasása. Ezt a funkciót használja, ha az erőforrás neve nem egyértelmű, vagy nem kiépített ugyanabban a sablonban. 

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| subscriptionId |Nem |karakterlánc (a GUID formátumban) |Alapértelmezett érték az aktuális előfizetésben. Adja meg ezt az értéket, ha szüksége van egy másik előfizetésben található erőforrás lekérése. |
| resourceGroupName |Nem |karakterlánc |Alapértelmezett érték: a jelenlegi erőforráscsoportban. Adja meg ezt az értéket, ha erőforrást egy másik erőforráscsoportban van szüksége. |
| resourceType |Igen |karakterlánc |Beleértve az erőforrás-szolgáltató névtere erőforrás típusát. |
| resourceName1 |Igen |karakterlánc |Erőforrás neve. |
| resourceName2 |Nem |karakterlánc |Következő neve erőforrásszegmensre. Ha az erőforrás van beágyazva. |

### <a name="return-value"></a>Visszatérési érték

Az azonosító eredmény abban az esetben a következő formátumban:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Megjegyzések

A megadott paraméterértékek függnek, hogy az erőforrás azonos előfizetésbe és erőforráscsoportba tartozik, mint a jelenlegi üzemelő példány.

Az erőforrás-azonosítója egy tárfiók ugyanahhoz az előfizetéshez és erőforráscsoport megtekintéséhez használja:

```json
"[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]"
```

Az erőforrás-azonosítója egy tárfiók ugyanahhoz az előfizetéshez, de egy másik erőforráscsoportban található, amelyet:

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

Az erőforrás-azonosítója egy tárfiók egy másik előfizetésben és erőforráscsoportban használatához:

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

Az erőforrás-azonosító egy másik erőforráscsoportban található adatbázis használatához:

```json
"[resourceId('otherResourceGroup', 'Microsoft.SQL/servers/databases', parameters('serverName'), parameters('databaseName'))]"
```

Gyakran kell használnia a függvény egy tárfiókhoz vagy a virtuális hálózat használata egy másik erőforráscsoportban. A következő példa bemutatja, hogyan könnyen használható egy külső erőforráscsoportból erőforrás:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "virtualNetworkName": {
          "type": "string"
      },
      "virtualNetworkResourceGroup": {
          "type": "string"
      },
      "subnet1Name": {
          "type": "string"
      },
      "nicName": {
          "type": "string"
      }
  },
  "variables": {
      "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
      "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
  },
  "resources": [
  {
      "apiVersion": "2015-05-01-preview",
      "type": "Microsoft.Network/networkInterfaces",
      "name": "[parameters('nicName')]",
      "location": "[parameters('location')]",
      "properties": {
          "ipConfigurations": [{
              "name": "ipconfig1",
              "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "subnet": {
                      "id": "[variables('subnet1Ref')]"
                  }
              }
          }]
       }
  }]
}
```

### <a name="example"></a>Példa

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json) adja vissza az erőforrás-azonosító tárfiókok esetén az erőforráscsoport:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "sameRGOutput": {
            "value": "[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentRGOutput": {
            "value": "[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "differentSubOutput": {
            "value": "[resourceId('11111111-1111-1111-1111-111111111111', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]",
            "type" : "string"
        },
        "nestedResourceOutput": {
            "value": "[resourceId('Microsoft.SQL/servers/databases', 'serverName', 'databaseName')]",
            "type" : "string"
        }
    }
}
```

Az alapértelmezett értékeit az előző példából kimenete:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| sameRGOutput | Karakterlánc | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | Karakterlánc | /subscriptions/{current-sub-id}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | Karakterlánc | /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | Karakterlánc | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/serverName/databases/databaseName |

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/resourceid.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/resourceid.json 
```

<a id="subscription" />

## <a name="subscription"></a>előfizetést
`subscription()`

Az előfizetés, a jelenlegi üzemelő példány részleteit adja vissza. 

### <a name="return-value"></a>Visszatérési érték

A függvény a következő formátumban:

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="example"></a>Példa

A következő [példa sablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json) az előfizetés függvény hívása a kimenetek szakaszban látható. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "subscriptionOutput": {
            "value": "[subscription()]",
            "type" : "object"
        }
    }
}
```

A példa sablon Azure parancssori felülettel történő üzembe helyezéséhez használja:

```azurecli-interactive
az group deployment create -g functionexamplegroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/subscription.json
```

A példa sablon PowerShell használatával történő üzembe helyezéséhez használja:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName functionexamplegroup -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/functions/subscription.json 
```

## <a name="next-steps"></a>További lépések
* A szakaszok az Azure Resource Manager-sablon ismertetését lásd: [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md).
* Több sablon egyesíteni, lásd: [kapcsolt sablonok használata az Azure Resource Manager](resource-group-linked-templates.md).
* Megadott számú alkalommal felépítésének egy adott típusú erőforrás létrehozása esetén lásd: [erőforrások több példánya létrehozása az Azure Resource Manager](resource-group-create-multiple.md).
* A sablon létrehozott központi telepítéséről, olvassa el [Azure Resource Manager-sablon az alkalmazás központi telepítését](resource-group-template-deploy.md).

