---
title: Az Azure Resource Manager sablonfüggvényei - erőforrások |} A Microsoft Docs
description: A funkciók az Azure Resource Manager-sablon használatával lekérheti az erőforrásokra vonatkozó értékeket ismerteti.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/09/2019
ms.author: tomfitz
ms.openlocfilehash: 4d5e6d20cb93c339d75c12ca1c0f56eaa5cc8cdd
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59490758"
---
# <a name="resource-functions-for-azure-resource-manager-templates"></a>Erőforrás-funkciók az Azure Resource Manager-sablonok

Resource Manager az alábbi funkciókat biztosít erőforrás-értékeinek beolvasása:

* [list*](#list)
* [Szolgáltatók](#providers)
* [Hivatkozás](#reference)
* [resourceGroup](#resourcegroup)
* [resourceId](#resourceid)
* [előfizetést](#subscription)

Paraméterek, változókat, vagy a jelenlegi üzemelő példány lekérjük az értékeket, lásd: [központi telepítési érték funkciók](resource-group-template-functions-deployment.md).

<a id="listkeys" />
<a id="list" />

## <a name="list"></a>list*

`list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)`

Ez a függvény szintaxisát a listázási műveletek neve eltérő. Minden egyes végrehajtása az erőforrás típusa, amely támogatja a list művelet értékeit adja vissza. A művelet nevét kell kezdődnie `list`. Néhány gyakori használatra vannak `listKeys` és `listSecrets`. 

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| resourceName vagy resourceIdentifier |Igen |sztring |Az erőforrás egyedi azonosítója. |
| apiVersion |Igen |sztring |API-verzió erőforrás futásidejű állapot. Általában a következő formátumban **éééé-hh-nn**. |
| functionValues |Nem |objektum | A függvény értékekkel rendelkező objektum. Csak adja meg ezt az objektumot az funkciók, amelyek támogatják a paraméterértékeket, rendelkező objektum például fogadása **listAccountSas** a storage-fiók. Az értékek függvény áthaladó példa ebben a cikkben. | 

### <a name="implementations"></a>Megvalósítások

A lehetséges felhasználási lista *, az alábbi táblázatban láthatók.

| Erőforrás típusa | Függvény neve |
| ------------- | ------------- |
| Microsoft.AnalysisServices/servers | [listGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft.Automation/automationAccounts | [listkeys műveletének](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.Batch/batchAccounts | [listkeys műveletének](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft.BatchAI/workspaces/experiments/jobs | [listoutputfiles](/rest/api/batchai/jobs/listoutputfiles) |
| Microsoft.Cache/redis | [listkeys műveletének](/rest/api/redis/redis/listkeys) |
| Microsoft.CognitiveServices/accounts | [listkeys műveletének](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Microsoft.ContainerRegistry/registries | [listBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/getbuildsourceuploadurl) |
| Microsoft.ContainerRegistry/registries | [listCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Microsoft.ContainerRegistry/registries | [ListPolicies végrehajtása](/rest/api/containerregistry/registries/listpolicies) |
| Microsoft.ContainerRegistry/registries | [listUsages](/rest/api/containerregistry/registries/listusages) |
| Microsoft.ContainerRegistry/registries/webhooks | [listEvents](/rest/api/containerregistry/webhooks/listevents) |
| Microsoft.ContainerService/managedClusters | [listClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft.ContainerService/managedClusters | [listClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Microsoft.DataFactory/datafactories/gateways | listauthkeys |
| Microsoft.DataFactory/factories/integrationruntimes | [listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers | [listSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Microsoft.Devices/iotHubs | [listkeys műveletének](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft.Devices/provisioningServices/keys | [listkeys műveletének](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft.Devices/provisioningServices | [listkeys műveletének](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft.DevTestLab/labs | [ListVhds](/rest/api/dtl/labs/listvhds) |
| Microsoft.DevTestLab/labs/schedules | [ListApplicable](/rest/api/dtl/schedules/listapplicable) |
| Microsoft.DevTestLab/labs/users/serviceFabrics | [ListApplicableSchedules](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft.DevTestLab/labs/virtualMachines | [ListApplicableSchedules](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft.DocumentDB/databaseAccounts | [listConnectionStrings](/rest/api/cosmos-db-resource-provider/databaseaccounts/listconnectionstrings) |
| Microsoft.DocumentDB/databaseAccounts | [listkeys műveletének](/rest/api/cosmos-db-resource-provider/databaseaccounts/listkeys) |
| Microsoft.DomainRegistration | [listDomainRecommendations](/rest/api/appservice/domains/listrecommendations) |
| Microsoft.DomainRegistration/topLevelDomains | [listAgreements](/rest/api/appservice/topleveldomains/listagreements) |
| Microsoft.EventGrid/topics | [listkeys műveletének](/rest/api/eventgrid/topics/listsharedaccesskeys) |
| Microsoft.EventHub/namespaces/authorizationRules | [listkeys műveletének](/rest/api/eventhub/namespaces/listkeys) |
| Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys műveletének](/rest/api/eventhub/disasterrecoveryconfigs/listkeys) |
| Microsoft.EventHub/namespaces/eventhubs/authorizationRules | [listkeys műveletének](/rest/api/eventhub/eventhubs/listkeys) |
| Microsoft.ImportExport/jobs | [listBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Microsoft.Logic/integrationAccounts/agreements | [listContentCallbackUrl](/rest/api/logic/agreements/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/assemblies | [listContentCallbackUrl](/rest/api/logic/integrationaccountassemblies/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listCallbackUrl](/rest/api/logic/integrationaccounts/getcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listKeyVaultKeys](/rest/api/logic/integrationaccounts/listkeyvaultkeys) |
| Microsoft.Logic/integrationAccounts/maps | [listContentCallbackUrl](/rest/api/logic/maps/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/partners | [listContentCallbackUrl](/rest/api/logic/partners/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/schemas | [listContentCallbackUrl](/rest/api/logic/schemas/listcontentcallbackurl) |
| Microsoft.Logic/workflows | [listCallbackUrl](/rest/api/logic/workflows/listcallbackurl) |
| Microsoft.Logic/workflows | [listSwagger](/rest/api/logic/workflows/listswagger) |
| Microsoft.MachineLearning/webServices | [listkeys műveletének](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft.MachineLearning/Workspaces | listworkspacekeys |
| Microsoft.MachineLearningServices/workspaces/computes | listkeys műveletének |
| Microsoft.MachineLearningServices/workspaces | listkeys műveletének |
| Microsoft.Maps/accounts | [listkeys műveletének](/rest/api/maps-management/accounts/listkeys) |
| Microsoft.Media/mediaservices/assets | [listContainerSas](/rest/api/media/assets/listcontainersas) |
| Microsoft.Media/mediaservices/assets | [listStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Microsoft.Media/mediaservices/streamingLocators | [listContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft.Media/mediaservices/streamingLocators | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| Microsoft.NotificationHubs/Namespaces/authorizationRules | [listkeys műveletének](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules | [listkeys műveletének](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft.OperationalInsights/workspaces | [listkeys műveletének](/rest/api/loganalytics/workspaces%202015-03-20/listkeys) |
| Microsoft.Relay/namespaces/authorizationRules | [listkeys műveletének](/rest/api/relay/namespaces/listkeys) |
| Microsoft.Relay/namespaces/HybridConnections/authorizationRules | [listkeys műveletének](/rest/api/relay/hybridconnections/listkeys) |
| Microsoft.Relay/namespaces/WcfRelays/authorizationRules | [listkeys műveletének](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft.Search/searchServices | [listAdminKeys](/rest/api/searchmanagement/adminkeys/get) |
| Microsoft.Search/searchServices | [listQueryKeys](/rest/api/searchmanagement/querykeys/listbysearchservice) |
| Microsoft.ServiceBus/namespaces/authorizationRules | [listkeys műveletének](/rest/api/servicebus/namespaces/listkeys) |
| Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys műveletének](/rest/api/servicebus/disasterrecoveryconfigs/listkeys) |
| Microsoft.ServiceBus/namespaces/queues/authorizationRules | [listkeys műveletének](/rest/api/servicebus/queues/listkeys) |
| Microsoft.ServiceBus/namespaces/topics/authorizationRules | [listkeys műveletének](/rest/api/servicebus/topics/listkeys) |
| Microsoft.SignalRService/SignalR | [listkeys műveletének](/rest/api/signalr/signalr/listkeys) |
| Microsoft.Storage/storageAccounts | [listAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft.Storage/storageAccounts | [listkeys műveletének](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft.Storage/storageAccounts | [listServiceSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Microsoft.StorSimple/managers/devices | [listFailoverSets](/rest/api/storsimple/devices/listfailoversets) |
| Microsoft.StorSimple/managers/devices | [listFailoverTargets](/rest/api/storsimple/devices/listfailovertargets) |
| Microsoft.StorSimple/managers | [listActivationKey](/rest/api/storsimple/managers/getactivationkey) |
| Microsoft.StorSimple/managers | [listPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Microsoft.Web/connectionGateways | ListStatus |
| microsoft.web/connections | listconsentlinks |
| Microsoft.Web/customApis | listWsdlInterfaces |
| microsoft.web/locations | listwsdlinterfaces |
| microsoft.web/sites/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| microsoft.web/sites/hybridconnectionnamespaces/relays | [listkeys műveletének](/rest/api/appservice/webapps/listhybridconnectionkeys) |
| microsoft.web/sites | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| microsoft.web/sites/slots/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecretsslot) |

Annak megállapításához, hogy mely erőforrástípusokat list művelettel rendelkezik, a következő lehetőségek állnak rendelkezésére:

* Nézet a [REST API-műveleteket](/rest/api/) erőforrás-szolgáltató, és keresse a műveletek listázása. Például, hogy a storage-fiókok a [listkeys műveletének művelet](/rest/api/storagerp/storageaccounts).
* Használja a [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) PowerShell-parancsmagot. Az alábbi példa lekéri az összes listázási műveletek storage-fiókok:

  ```powershell
  Get-AzProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* A következő Azure CLI-parancs segítségével a listában csak a műveletek szűrése:

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

### <a name="return-value"></a>Vrácená hodnota

A visszaadott objektum eltérő a lista függvényt használja. A storage-fiókok listkeys műveletének például a következő formátumban adja vissza:

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

Más lista függvények, különböző visszaadott formátumokat. Szeretné megtekinteni a függvényt formátumát, foglalja bele a kimeneti szakasz ahogy a példában a sablonban.

### <a name="remarks"></a>Megjegyzések

Az erőforrás neve használatával adja meg az erőforrás vagy a [resourceId függvény](#resourceid). Ha ugyanazt a sablont, amely üzembe helyezi a hivatkozott erőforrás egy lista függvényt használ, használja az erőforrás neve.

Ha egy **lista** függvény olyan erőforrást, amely feltételesen üzembe lett helyezve, a függvény a abban az esetben akkor is, ha az erőforrás nincs telepítve. Ha hibaüzenetet kap a **lista** függvény egy nem létező erőforrás hivatkozik. Használja a **Ha** függvényt, hogy ellenőrizze, hogy a függvény csak akkor értékeli ki, ha az erőforrás létezik. Tekintse meg a [Ha függvény](resource-group-template-functions-logical.md#if) a mintasablon, amely használja, ha és a egy feltételesen üzembe helyezett erőforrás listában.

### <a name="example"></a>Példa

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/listkeys.json) mutatja be az elsődleges és másodlagos kulcsok vissza a kimeneti szakasz egy storage-fiókból. Emellett a tárfiók SAS-jogkivonatát adja vissza. 

A SAS-jogkivonat beszerzéséhez-objektum továbbítása a lejárati ideje. A lejárati időpont a jövőben kell lennie. Ebben a példában a listát funkciók használatát mutatják funkcionalitást. Általában, lenne erőforrás értékét a SAS-jogkivonat használata helyett egy kimeneti értéket, küldje vissza. Kimeneti értékeket az üzembe helyezési előzmények vannak tárolva, és nem biztonságos.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagename": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "southcentralus"
        },
        "accountSasProperties": {
            "type": "object",
            "defaultValue": {
                "signedServices": "b",
                "signedPermission": "r",
                "signedExpiry": "2018-08-20T11:00:00Z",
                "signedResourceTypes": "s"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2018-02-01",
            "name": "[parameters('storagename')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Storage/storageAccounts",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "properties": {
                "supportsHttpsTrafficOnly": false,
                "accessTier": "Hot",
                "encryption": {
                    "services": {
                        "blob": {
                            "enabled": true
                        },
                        "file": {
                            "enabled": true
                        }
                    },
                    "keySource": "Microsoft.Storage"
                }
            },
            "dependsOn": []
        }
    ],
    "outputs": {
        "keys": {
            "type": "object",
            "value": "[listKeys(parameters('storagename'), '2018-02-01')]"
        },
        "accountSAS": {
            "type": "object",
            "value": "[listAccountSas(parameters('storagename'), '2018-02-01', parameters('accountSasProperties'))]"
        }
    }
}
```

## <a name="providers"></a>Szolgáltatók

`providers(providerNamespace, [resourceType])`

Erőforrás-szolgáltató és a támogatott erőforrástípusok kapcsolatos információkat ad vissza. Ha nem ad meg egy erőforrás típusa, a függvény a támogatott típusok az erőforrás-szolgáltató adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| providerNamespace |Igen |sztring |A szolgáltató Namespace |
| resourceType |Nem |sztring |Erőforrás típusa, az adott névtérben. |

### <a name="return-value"></a>Vrácená hodnota

Minden támogatott típus a következő formátumban adja vissza: 

```json
{
    "resourceType": "{name of resource type}",
    "locations": [ all supported locations ],
    "apiVersions": [ all supported API versions ]
}
```

A visszaadott értékekhez tömb rendezése nem garantált.

### <a name="example"></a>Példa

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json) bemutatja, hogyan használja a szolgáltató függvényt:

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

Az a **Microsoft.Web** erőforrás-szolgáltató és **helyek** erőforrás típusa, az előző példában egy objektumot ad vissza a következő formátumban:

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

## <a name="reference"></a>Hivatkozás

`reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])`

Az erőforrások futásidejű állapotot képviselő objektumot adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| resourceName vagy resourceIdentifier |Igen |sztring |Név vagy erőforrás egyedi azonosítója. |
| apiVersion |Nem |sztring |A megadott erőforrás API-verzió. Adja meg az értékét üzembe helyezésekor az erőforrás nem található ugyanazt a sablont. Általában a következő formátumban **éééé-hh-nn**. |
| "Teljes" |Nem |sztring |Érték, amely megadja, hogy a teljes erőforrás-objektumot ad vissza. Ha nem ad meg `'Full'`, csak az erőforrás tulajdonságai objektumot ad vissza. A teljes objektum például az erőforrás-azonosító és a hely értékeket tartalmaz. |

### <a name="return-value"></a>Vrácená hodnota

Minden erőforrástípus a referencia-függvény különböző tulajdonságait adja vissza. A függvény nem ad vissza egy egyetlen, előre definiált formátumot. A visszaadott érték is, a teljes objektum megadott attól függően eltér. Egy erőforrás-típus tulajdonságainak megtekintéséhez a objektumot ad vissza, a kimeneti szakaszban, a példában látható módon.

### <a name="remarks"></a>Megjegyzések

A referencia-függvényt a korábban üzembe helyezett erőforrás vagy a jelenlegi sablon üzembe helyezett erőforrás futási állapotát olvassa be. Ez a cikk bemutatja a példák mindkét forgatókönyvet támogatja. Amikor egy erőforrást az aktuális sablon hivatkozik, adja meg az erőforrásnév csak paraméterként. Amikor egy korábban telepített erőforrásra hivatkozik, adja meg az erőforrás-azonosító és API-verziót az erőforrás. Az erőforrás érvényes API-verziók segítségével meghatározhatja a [sablonreferenciája](/azure/templates/).

A referencia-függvény csak egy erőforrás-definíció tulajdonságainak és a kimeneti szakasz egy sablon vagy telepítési használható.

A referencia-függvény használatával akkor implicit módon deklarálja, hogy egy erőforrás függ-e egy másik erőforrás, ha a hivatkozott erőforrás kiosztása belül ugyanazt a sablont, és a nevét (nem erőforrás-azonosító) az erőforrás hivatkozik. Emellett a dependsOn tulajdonság használatához nincs szükség. A függvény nem kerül kiértékelésre, a hivatkozott erőforrás üzembe helyezési befejeződéséig.

Ha használja a **referencia** függvény olyan erőforrást, amely feltételesen üzembe lett helyezve, a függvény a abban az esetben akkor is, ha az erőforrás nincs telepítve.  Ha hibaüzenetet kap a **referencia** függvény egy nem létező erőforrás hivatkozik. Használja a **Ha** függvényt, hogy ellenőrizze, hogy a függvény csak akkor értékeli ki, ha az erőforrás létezik. Tekintse meg a [Ha függvény](resource-group-template-functions-logical.md#if) használja, ha a minta-sablon és a egy feltételesen üzembe helyezett erőforrás-hivatkozás.

Tekintse meg a nevét és a egy erőforrástípushoz értékeit, hozzon létre egy sablont, amely az objektumot ad vissza, a kimeneti szakaszban. Ha az adott típusú erőforrással rendelkezik, a sablon bármely új erőforrások üzembe helyezése nélkül adja vissza az objektumot. 

Általában használni a **referencia** funkció egy adott érték visszaadása egy objektumot, például a blob-végpont URI-t vagy teljesen minősített tartománynevét.

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

Használat `'Full'` erőforrás értékek, amelyek nem részei a Tulajdonságok séma számíthat. Például a kulcstartó hozzáférési házirendjeinek beállítása, kérje le a tulajdonságok egy virtuális géphez.

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

A fenti sablon teljes példa: [Key vault Windows](https://github.com/rjmax/AzureSaturday/blob/master/Demo02.ManagedServiceIdentity/demo08.msiWindowsToKeyvault.json). Egy hasonló példa érhető el az [Linux](https://github.com/rjmax/AzureSaturday/blob/master/Demo02.ManagedServiceIdentity/demo07.msiLinuxToArm.json).

### <a name="example"></a>Példa

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json) üzembe helyez egy erőforrást, és adott erőforrásra hivatkozik.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Az előző példában a két objektum adja vissza. A Tulajdonságok objektumában a következő formátumban kell megadni:

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

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/reference.json) hivatkozik egy tárfiókot, amelyet az ebben a sablonban nincs telepítve. A tárfiók már létezik ugyanazon az előfizetésen belül.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageResourceGroup": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string"
        }
    },
    "resources": [],
    "outputs": {
        "ExistingStorage": {
            "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]",
            "type": "object"
        }
    }
}
```

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

Az aktuális erőforráscsoport képviselő objektumot adja vissza. 

### <a name="return-value"></a>Vrácená hodnota

A visszaadott objektum a következő formátumban kell megadni:

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

A `resourceGroup()` függvény nem használható sablont [az előfizetési szinten üzembe helyezett](deploy-to-subscription.md). Csak használható az erőforráscsoportban üzembe helyezett szolgáltatásokban.

A resourceGroup függvény egyik gyakori felhasználási hozhat létre erőforrásokat az erőforráscsoport ugyanazon a helyen. Az alábbi példában az erőforráscsoport helyét egy webhely a hely hozzárendeléséhez.

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

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json) az erőforráscsoport tulajdonságait adja vissza.

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

Az előző példában egy objektumot ad vissza a következő formátumban:

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

## <a name="resourceid"></a>resourceId

`resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2]...)`

Adja vissza egy adott erőforrás egyedi azonosítója. A függvény akkor használható, ha az erőforrás neve nem egyértelmű vagy ugyanabban a sablonban nincs kiépítve. 

### <a name="parameters"></a>Paraméterek

| Paraméter | Szükséges | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| subscriptionId |Nem |karakterlánc (a GUID formátumban) |Alapértelmezett érték az aktuális előfizetésben. Adja meg ezt az értéket, amikor szüksége van egy másik előfizetésben egy erőforrás lekérése. |
| resourceGroupName |Nem |sztring |Alapértelmezett érték az aktuális erőforráscsoportban. Adja meg ezt az értéket, amikor szüksége van egy másik erőforráscsoportban található egy erőforrás lekérése. |
| resourceType |Igen |sztring |Felhasznált erőforrás típusa, beleértve az erőforrás-szolgáltató névtere. |
| resourceName1 |Igen |sztring |Erőforrás neve. |
| resourceName2 |Nem |sztring |Következő erőforrás neve szegmens Ha erőforrás van beágyazva. |

### <a name="return-value"></a>Vrácená hodnota

Az azonosító a következő formátumban adja vissza:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Megjegyzések

Együtt használva egy [előfizetés-szintű üzembe helyezési](deploy-to-subscription.md), a `resourceId()` függvény csak kérheti le ezen a szinten üzembe helyezett erőforrásokhoz azonosítója. Például kaphat egy szabályzat-definíció vagy a szerepkör-definíció azonosítója, de nem a storage-fiók Azonosítóját. Egy erőforráscsoport központi telepítésére ennek az ellenkezője is igaz. Az előfizetési szinten üzembe helyezett erőforrások erőforrás-azonosítója nem lehet lekérdezni.

Az erőforrás-e az azonos előfizetésben és erőforráscsoportban tartozik, mint a jelenlegi üzemelő példány a megadott paraméterértékek függenek. Egy tárfiók ugyanabban az előfizetésben és erőforráscsoportban található az erőforrás-azonosító lekéréséhez használja:

```json
"[resourceId('Microsoft.Storage/storageAccounts','examplestorage')]"
```

A tárfiók ugyanahhoz az előfizetéshez, de egy másik erőforráscsoportban található az erőforrás-azonosító lekéréséhez használja:

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

Az erőforrás-azonosítója egy storage-fiók egy másik előfizetésben és erőforráscsoportban, amelyet:

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

Egy adatbázis egy másik erőforráscsoportban található az erőforrás-azonosító lekéréséhez használja:

```json
"[resourceId('otherResourceGroup', 'Microsoft.SQL/servers/databases', parameters('serverName'), parameters('databaseName'))]"
```

Előfizetés-szintű erőforrás erőforrás-Azonosítóját az előfizetési hatókörben való telepítésekor használja:

```json
"[resourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
```

Gyakran kell használatakor ez a függvény egy storage-fiók vagy a virtuális hálózat egy másik erőforráscsoportban. Az alábbi példa bemutatja, hogyan könnyen használható egy erőforrás egy külső erőforrás-csoportból:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
      "subnet1Ref": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks/subnets', parameters('virtualNetworkName'), parameters('subnet1Name'))]"
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

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json) adja vissza az erőforráscsoportot egy storage-fiók erőforrás-azonosító:

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

Az alapértelmezett értékeket az előző példa kimenete a következő:

| Name (Név) | Típus | Érték |
| ---- | ---- | ----- |
| sameRGOutput | Karakterlánc | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | Karakterlánc | /subscriptions/{current-sub-id}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | Karakterlánc | /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | Karakterlánc | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/serverName/databases/databaseName |

## <a name="subscription"></a>előfizetést

`subscription()`

Az előfizetés, a jelenlegi üzemelő példány részleteit adja vissza. 

### <a name="return-value"></a>Vrácená hodnota

A függvény a következő formátumban adja vissza:

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="example"></a>Példa

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json) mutat be az előfizetés függvény meghívta a kimeneti szakasz. 

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

## <a name="next-steps"></a>További lépések

* A szakaszok az Azure Resource Manager-sablon ismertetését lásd: [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md).
* Több sablon egyesíteni, lásd: [kapcsolt sablonok használata az Azure Resource Manager](resource-group-linked-templates.md).
* A megadott számú alkalommal újrafuttathatja egy adott típusú erőforrás létrehozásakor, lásd: [több erőforráspéldány létrehozása az Azure Resource Manager](resource-group-create-multiple.md).
* A létrehozott sablon üzembe helyezése, olvassa el [alkalmazás üzembe helyezése Azure Resource Manager-sablonnal](resource-group-template-deploy.md).

