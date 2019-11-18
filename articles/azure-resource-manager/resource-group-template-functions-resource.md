---
title: Sablon functions – erőforrások
description: A funkciók az Azure Resource Manager-sablon használatával lekérheti az erőforrásokra vonatkozó értékeket ismerteti.
ms.topic: conceptual
ms.date: 10/26/2019
ms.openlocfilehash: 6457bafeeb0b241171311dc3dcea30b7b6993791
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150684"
---
# <a name="resource-functions-for-azure-resource-manager-templates"></a>Erőforrás-funkciók az Azure Resource Manager-sablonok

Resource Manager az alábbi funkciókat biztosít erőforrás-értékeinek beolvasása:

* [extensionResourceId](#extensionresourceid)
* [lista *](#list)
* [Szolgáltatók](#providers)
* [reference](#reference)
* [resourceGroup](#resourcegroup)
* [resourceId](#resourceid)
* [előfizetést](#subscription)
* [subscriptionResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

Paraméterek, változókat, vagy a jelenlegi üzemelő példány lekérjük az értékeket, lásd: [központi telepítési érték funkciók](resource-group-template-functions-deployment.md).

## <a name="extensionresourceid"></a>extensionResourceId

```json
extensionResourceId(resourceId, resourceType, resourceName1, [resourceName2], ...)
```

A [bővítmény erőforrásának](extension-resource-types.md)erőforrás-azonosítóját adja vissza, amely egy másik erőforrásra alkalmazott erőforrástípus, amely a képességeihez való hozzáadásra szolgál.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| resourceId |Igen |sztring |Annak az erőforrásnak az erőforrás-azonosítója, amelyre a bővítmény erőforrása vonatkozik. |
| resourceType |Igen |sztring |Felhasznált erőforrás típusa, beleértve az erőforrás-szolgáltató névtere. |
| resourceName1 |Igen |sztring |Erőforrás neve. |
| resourceName2 |Nem |sztring |A következő erőforrás neve szegmens, ha szükséges. |

Ha az erőforrás típusa több szegmenst tartalmaz, folytassa a paraméterek hozzáadását paraméterként.

### <a name="return-value"></a>Vrácená hodnota

A függvény által visszaadott erőforrás-azonosító alapszintű formátuma a következő:

```json
{scope}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

A hatókör szegmense a kibővített erőforrástól függ.

Ha a bővítmény erőforrása egy **erőforrásra**vonatkozik, a rendszer az erőforrás-azonosítót a következő formátumban adja vissza:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{baseResourceProviderNamespace}/{baseResourceType}/{baseResourceName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Ha a bővítmény erőforrását egy **erőforráscsoport**alkalmazza, a formátum a következő:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Ha a bővítmény erőforrása egy **előfizetésre**van alkalmazva, a formátum a következő:

```json
/subscriptions/{subscriptionId}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Ha a bővítmény erőforrását egy **felügyeleti csoportra**alkalmazza, a formátum a következő:

```json
/providers/Microsoft.Management/managementGroups/{managementGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

### <a name="extensionresourceid-example"></a>extensionResourceId példa

A következő példa egy erőforráscsoport-zárolás erőforrás-AZONOSÍTÓját adja vissza.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "lockName":{
            "type": "string"
        }
    },
    "variables": {},
    "resources": [],
    "outputs": {
        "lockResourceId": {
            "type": "string",
            "value": "[extensionResourceId(resourceGroup().Id , 'Microsoft.Authorization/locks', parameters('lockName'))]"
        }
    }
}
```

<a id="listkeys" />
<a id="list" />

## <a name="list"></a>list*

```json
list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)
```

A függvény szintaxisa a lista műveleteinek nevével változik. Minden implementáció a lista műveletét támogató erőforrástípus értékeit adja vissza. A művelet nevének a `list`vel kell kezdődnie. Néhány gyakori használat `listKeys` és `listSecrets`. 

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| resourceName vagy resourceIdentifier |Igen |sztring |Az erőforrás egyedi azonosítója. |
| apiVersion |Igen |sztring |API-verzió erőforrás futásidejű állapot. Általában a következő formátumban **éééé-hh-nn**. |
| functionValues |Nem |objektum | A függvény értékekkel rendelkező objektum. Csak adja meg ezt az objektumot az funkciók, amelyek támogatják a paraméterértékeket, rendelkező objektum például fogadása **listAccountSas** a storage-fiók. Ebben a cikkben látható egy példa a függvény értékének átadására. | 

### <a name="valid-uses"></a>Érvényes használati módok

A List függvények csak az erőforrás-definíció tulajdonságaiban és a sablon vagy központi telepítés kimenetek szakaszában használhatók. Ha tulajdonság- [iterációt](resource-group-create-multiple.md#property-iteration)használ, használhatja a `input` listázása funkciót, mert a kifejezés hozzá van rendelve az erőforrás-tulajdonsághoz. Ezeket nem használhatja `count`, mert a Count meghatározása előtt meg kell határozni a darabszámot.

### <a name="implementations"></a>Megvalósítások

A (z) * lista lehetséges felhasználási módjai a következő táblázatban láthatók.

| Erőforrás típusa | Függvény neve |
| ------------- | ------------- |
| Microsoft.AnalysisServices/servers | [listGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft.AppConfiguration/configurationStores | Listkeys műveletének beolvasása |
| Microsoft.Automation/automationAccounts | [listkeys műveletének](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.Batch/batchAccounts | [listkeys műveletének beolvasása](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft. BatchAI/munkaterületek/kísérletek/feladatok | [listoutputfiles](/rest/api/batchai/jobs/listoutputfiles) |
| Microsoft. Blockchain/blockchainMembers | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Microsoft.Blockchain/blockchainMembers/transactionNodes | [listApiKeys](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Microsoft. BotService/botServices/csatornák | listChannelWithKeys |
| Microsoft.Cache/redis | [listkeys műveletének](/rest/api/redis/redis/listkeys) |
| Microsoft.CognitiveServices/accounts | [listkeys műveletének](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Microsoft.ContainerRegistry/registries | [listBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/getbuildsourceuploadurl) |
| Microsoft.ContainerRegistry/registries | [listCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Microsoft.ContainerRegistry/registries | [listUsages](/rest/api/containerregistry/registries/listusages) |
| Microsoft. ContainerRegistry/nyilvántartók/webhookok | [listEvents](/rest/api/containerregistry/webhooks/listevents) |
| Microsoft.ContainerRegistry/registries/runs | [listLogSasUrl](/rest/api/containerregistry/runs/getlogsasurl) |
| Microsoft.ContainerRegistry/registries/tasks | [listDetails](/rest/api/containerregistry/tasks/getdetails) |
| Microsoft.ContainerService/managedClusters | [listClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft.ContainerService/managedClusters | [listClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Microsoft.ContainerService/managedClusters/accessProfiles | [listCredential](/rest/api/aks/managedclusters/getaccessprofile) |
| Microsoft.DataBox/jobs | listCredentials |
| Microsoft.DataFactory/datafactories/gateways | listauthkeys |
| Microsoft.DataFactory/factories/integrationruntimes | [listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers | [listSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Microsoft.Devices/iotHubs | [listkeys műveletének beolvasása](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft.Devices/provisioningServices/keys | [listkeys műveletének beolvasása](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft.Devices/provisioningServices | [listkeys műveletének beolvasása](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft.DevTestLab/labs | [ListVhds](/rest/api/dtl/labs/listvhds) |
| Microsoft.DevTestLab/labs/schedules | [ListApplicable](/rest/api/dtl/schedules/listapplicable) |
| Microsoft.DevTestLab/labs/users/serviceFabrics | [ListApplicableSchedules](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft.DevTestLab/labs/virtualMachines | [ListApplicableSchedules](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft.DocumentDB/databaseAccounts | [listConnectionStrings](/rest/api/cosmos-db-resource-provider/databaseaccounts/listconnectionstrings) |
| Microsoft.DocumentDB/databaseAccounts | [listkeys műveletének](/rest/api/cosmos-db-resource-provider/databaseaccounts/listkeys) |
| Microsoft.DomainRegistration | [listDomainRecommendations](/rest/api/appservice/domains/listrecommendations) |
| Microsoft.DomainRegistration/topLevelDomains | [listAgreements](/rest/api/appservice/topleveldomains/listagreements) |
| Microsoft.EventGrid/domains | [listkeys műveletének](/rest/api/eventgrid/domains/listsharedaccesskeys) |
| Microsoft.EventGrid/topics | [listkeys műveletének](/rest/api/eventgrid/topics/listsharedaccesskeys) |
| Microsoft.EventHub/namespaces/authorizationRules | [listkeys műveletének beolvasása](/rest/api/eventhub/namespaces/listkeys) |
| Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys műveletének beolvasása](/rest/api/eventhub/disasterrecoveryconfigs/listkeys) |
| Microsoft. EventHub/névterek/eventhubs/engedélyezési szabályok | [listkeys műveletének beolvasása](/rest/api/eventhub/eventhubs/listkeys) |
| Microsoft. ImportExport/feladatok | [listBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Microsoft. LabServices/felhasználók | [ListEnvironments](/rest/api/labservices/globalusers/listenvironments) |
| Microsoft. LabServices/felhasználók | [ListLabs](/rest/api/labservices/globalusers/listlabs) |
| Microsoft.Logic/integrationAccounts/agreements | [listContentCallbackUrl](/rest/api/logic/agreements/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/assemblies | [listContentCallbackUrl](/rest/api/logic/integrationaccountassemblies/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listCallbackUrl](/rest/api/logic/integrationaccounts/getcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listKeyVaultKeys](/rest/api/logic/integrationaccounts/listkeyvaultkeys) |
| Microsoft.Logic/integrationAccounts/maps | [listContentCallbackUrl](/rest/api/logic/maps/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/partners | [listContentCallbackUrl](/rest/api/logic/partners/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/schemas | [listContentCallbackUrl](/rest/api/logic/schemas/listcontentcallbackurl) |
| Microsoft.Logic/workflows | [listCallbackUrl](/rest/api/logic/workflows/listcallbackurl) |
| Microsoft.Logic/workflows | [listSwagger](/rest/api/logic/workflows/listswagger) |
| Microsoft. Logic/munkafolyamatok/triggerek | [listCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Microsoft. Logic/munkafolyamatok/verziók/eseményindítók | [listCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Microsoft.MachineLearning/webServices | [listkeys műveletének beolvasása](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft. MachineLearning/munkaterületek | listworkspacekeys |
| Microsoft.MachineLearningServices/workspaces/computes | Listkeys műveletének beolvasása |
| Microsoft. MachineLearningServices/munkaterületek | Listkeys műveletének beolvasása |
| Microsoft. Maps/fiókok | [listkeys műveletének](/rest/api/maps-management/accounts/listkeys) |
| Microsoft. Media/Mediaservices/eszközök | [listContainerSas](/rest/api/media/assets/listcontainersas) |
| Microsoft. Media/Mediaservices/eszközök | [listStreamingLocators](/rest/api/media/assets/liststreaminglocators) |
| Microsoft.Media/mediaservices/streamingLocators | [listContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft.Media/mediaservices/streamingLocators | [listPaths](/rest/api/media/streaminglocators/listpaths) |
| Microsoft.Network/applicationSecurityGroups | listIpConfigurations |
| Microsoft. NotificationHubs/névterek/engedélyezési szabályok | [listkeys műveletének beolvasása](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft. NotificationHubs/névterek/NotificationHubs/engedélyezési szabályok | [listkeys műveletének beolvasása](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft.OperationalInsights/workspaces | [listkeys műveletének](/rest/api/loganalytics/workspaces%202015-03-20/listkeys) |
| Microsoft. Relay/névterek/engedélyezési szabályok | [listkeys műveletének beolvasása](/rest/api/relay/namespaces/listkeys) |
| Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules | listkeys műveletének beolvasása |
| Microsoft. Relay/névterek/HybridConnections/engedélyezési szabályok | [listkeys műveletének beolvasása](/rest/api/relay/hybridconnections/listkeys) |
| Microsoft. Relay/névterek/WcfRelays/engedélyezési szabályok | [listkeys műveletének beolvasása](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft.Search/searchServices | [listAdminKeys](/rest/api/searchmanagement/adminkeys/get) |
| Microsoft.Search/searchServices | [listQueryKeys](/rest/api/searchmanagement/querykeys/listbysearchservice) |
| Microsoft.ServiceBus/namespaces/authorizationRules | [listkeys műveletének beolvasása](/rest/api/servicebus/namespaces/listkeys) |
| Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules | [listkeys műveletének beolvasása](/rest/api/servicebus/disasterrecoveryconfigs/listkeys) |
| Microsoft.ServiceBus/namespaces/queues/authorizationRules | [listkeys műveletének beolvasása](/rest/api/servicebus/queues/listkeys) |
| Microsoft.ServiceBus/namespaces/topics/authorizationRules | [listkeys műveletének beolvasása](/rest/api/servicebus/topics/listkeys) |
| Microsoft.SignalRService/SignalR | [listkeys műveletének beolvasása](/rest/api/signalr/signalr/listkeys) |
| Microsoft.Storage/storageAccounts | [listAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft.Storage/storageAccounts | [listkeys műveletének beolvasása](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft.Storage/storageAccounts | [listServiceSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Microsoft.StorSimple/managers/devices | [listFailoverSets](/rest/api/storsimple/devices/listfailoversets) |
| Microsoft.StorSimple/managers/devices | [listFailoverTargets](/rest/api/storsimple/devices/listfailovertargets) |
| Microsoft.StorSimple/managers | [listActivationKey](/rest/api/storsimple/managers/getactivationkey) |
| Microsoft.StorSimple/managers | [listPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Microsoft. Web/connectionGateways | ListStatus |
| microsoft.web/connections | listconsentlinks |
| Microsoft.Web/customApis | listWsdlInterfaces |
| microsoft.web/locations | listwsdlinterfaces |
| microsoft.web/apimanagementaccounts/apis/connections | listconnectionkeys |
| microsoft.web/apimanagementaccounts/apis/connections | listsecrets |
| microsoft.web/sites/functions | [listsecrets](/rest/api/appservice/webapps/listfunctionsecrets) |
| Microsoft. Web/Sites/hybridconnectionnamespaces/Relays | [listkeys műveletének beolvasása](/rest/api/appservice/webapps/listhybridconnectionkeys) |
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

A visszaadott objektum a használt lista függvénytől függ. A Storage-fiók Listkeys műveletének beolvasása például a következő formátumot adja vissza:

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

Az erőforrás neve használatával adja meg az erőforrás vagy a [resourceId függvény](#resourceid). Ha egy, a hivatkozott erőforrást telepítő sablonban egy list függvényt használ, használja az erőforrás nevét.

Ha feltételesen telepített erőforrásban használ egy **List** függvényt, akkor a függvény akkor is kiértékelésre kerül, ha az erőforrás nincs telepítve. Hibaüzenet jelenik meg, ha a **lista** függvény olyan erőforrásra hivatkozik, amely nem létezik. Az **IF** függvény használatával győződjön meg arról, hogy a függvény csak az erőforrás telepítésekor lesz kiértékelve. Tekintse meg a if [függvényt](resource-group-template-functions-logical.md#if) egy olyan minta sablon esetében, amely a IF és a listát feltételesen telepített erőforrást használja.

### <a name="list-example"></a>Példa a listára

A következő [példasablonja](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/listkeys.json) mutatja be az elsődleges és másodlagos kulcsok vissza a kimeneti szakasz egy storage-fiókból. Emellett a tárfiók SAS-jogkivonatát adja vissza. 

Az SAS-jogkivonat lekéréséhez továbbítson egy objektumot a lejárati időpontra. A lejárati időnek a jövőben kell lennie. Ebben a példában a listát funkciók használatát mutatják funkcionalitást. Általában, lenne erőforrás értékét a SAS-jogkivonat használata helyett egy kimeneti értéket, küldje vissza. Kimeneti értékeket az üzembe helyezési előzmények vannak tárolva, és nem biztonságos.

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

```json
providers(providerNamespace, [resourceType])
```

Erőforrás-szolgáltató és a támogatott erőforrástípusok kapcsolatos információkat ad vissza. Ha nem ad meg egy erőforrás típusa, a függvény a támogatott típusok az erőforrás-szolgáltató adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
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

### <a name="providers-example"></a>Szolgáltatók – példa

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

```json
reference(resourceName or resourceIdentifier, [apiVersion], ['Full'])
```

Az erőforrások futásidejű állapotot képviselő objektumot adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| resourceName vagy resourceIdentifier |Igen |sztring |Név vagy erőforrás egyedi azonosítója. Amikor egy erőforrást az aktuális sablon hivatkozik, adja meg az erőforrásnév csak paraméterként. Egy korábban központilag telepített erőforrásra hivatkozva adja meg az erőforrás-azonosítót. |
| apiVersion |Nem |sztring |A megadott erőforrás API-verzió. Adja meg az értékét üzembe helyezésekor az erőforrás nem található ugyanazt a sablont. Általában a következő formátumban **éééé-hh-nn**. Az erőforrás érvényes API-verzióihoz lásd: [sablon-hivatkozás](/azure/templates/). |
| "Teljes" |Nem |sztring |Érték, amely megadja, hogy a teljes erőforrás-objektumot ad vissza. Ha nem ad meg `'Full'`, csak az erőforrás tulajdonságai objektumot ad vissza. A teljes objektum például az erőforrás-azonosító és a hely értékeket tartalmaz. |

### <a name="return-value"></a>Vrácená hodnota

Minden erőforrástípus a referencia-függvény különböző tulajdonságait adja vissza. A függvény nem ad vissza egy egyetlen, előre definiált formátumot. A visszaadott érték is, a teljes objektum megadott attól függően eltér. Egy erőforrás-típus tulajdonságainak megtekintéséhez a objektumot ad vissza, a kimeneti szakaszban, a példában látható módon.

### <a name="remarks"></a>Megjegyzések

A referencia-függvényt a korábban üzembe helyezett erőforrás vagy a jelenlegi sablon üzembe helyezett erőforrás futási állapotát olvassa be. Ez a cikk bemutatja a példák mindkét forgatókönyvet támogatja.

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

### <a name="valid-uses"></a>Érvényes használati módok

A referencia-függvény csak egy erőforrás-definíció tulajdonságainak és a kimeneti szakasz egy sablon vagy telepítési használható. Ha tulajdonság- [iterációt](resource-group-create-multiple.md#property-iteration)használ, használhatja a `input` hivatkozási függvényét, mert a kifejezés hozzá van rendelve az erőforrás tulajdonsághoz. A `count` nem használható, mert a számnak meg kell határoznia a hivatkozási függvény feloldása előtt.

A [beágyazott](resource-group-linked-templates.md#nested-template) sablon kimenetében lévő Reference függvény nem használható a beágyazott sablonban üzembe helyezett erőforrások visszaküldéséhez. Ehelyett használjon [csatolt sablont](resource-group-linked-templates.md#external-template).

Ha a **hivatkozási** függvényt egy feltételesen üzembe helyezett erőforrásban használja, akkor a függvény akkor is ki lesz értékelve, ha az erőforrás nincs telepítve.  Hibaüzenet jelenik meg, ha a **hivatkozási** függvény olyan erőforrásra hivatkozik, amely nem létezik. Az **IF** függvény használatával győződjön meg arról, hogy a függvény csak az erőforrás telepítésekor lesz kiértékelve. Tekintse meg az IF [függvényt](resource-group-template-functions-logical.md#if) egy olyan sablon esetében, amely a IF és a hivatkozást egy feltételesen telepített erőforrással használja.

### <a name="implicit-dependency"></a>Implicit függőség

A referencia-függvény használatával akkor implicit módon deklarálja, hogy egy erőforrás függ-e egy másik erőforrás, ha a hivatkozott erőforrás kiosztása belül ugyanazt a sablont, és a nevét (nem erőforrás-azonosító) az erőforrás hivatkozik. Emellett a dependsOn tulajdonság használatához nincs szükség. A függvény nem kerül kiértékelésre, a hivatkozott erőforrás üzembe helyezési befejeződéséig.

### <a name="resource-name-or-identifier"></a>Erőforrás neve vagy azonosítója

Ha egy olyan erőforrásra hivatkozik, amely ugyanabban a sablonban van telepítve, adja meg az erőforrás nevét.

```json
"value": "[reference(parameters('storageAccountName'))]"
```

Ha olyan erőforrásra hivatkozik, amely nem ugyanabban a sablonban van telepítve, adja meg az erőforrás-azonosítót.

```json
"value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

Annak elkerülése érdekében, hogy a rendszer melyik erőforrásra hivatkozik, megadhat egy teljesen minősített erőforrás nevét.

```json
"value": "[reference(concat('Microsoft.Network/publicIPAddresses/', parameters('ipAddressName')))]"
```

Egy erőforrásra vonatkozó teljes körű hivatkozás létrehozásakor a szegmensek és a név összevonásának sorrendje nem csupán a kettő összefűzése. Ehelyett a névtér után olyan *típusú/név* párokat használjon, amelyek legalább a legpontosabbak:

**{erőforrás-szolgáltató-névtér}/{Parent-Resource-Type}/{Parent-Resource-Name} [/{Child-Resource-Type}/{Child-Resource-name}]**

Például:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt` helyes `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` helytelen

### <a name="reference-example"></a>Példa hivatkozásra

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

```json
resourceGroup()
```

Az aktuális erőforráscsoport képviselő objektumot adja vissza. 

### <a name="return-value"></a>Vrácená hodnota

A visszaadott objektum a következő formátumban kell megadni:

```json
{
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "name": "{resourceGroupName}",
  "type":"Microsoft.Resources/resourceGroups",
  "location": "{resourceGroupLocation}",
  "managedBy": "{identifier-of-managing-resource}",
  "tags": {
  },
  "properties": {
    "provisioningState": "{status}"
  }
}
```

A **többé** tulajdonság csak olyan erőforráscsoportok esetében lesz visszaadva, amelyek egy másik szolgáltatás által felügyelt erőforrásokat tartalmaznak. A felügyelt alkalmazások, a Databricks és az AK esetében a tulajdonság értéke a kezelő erőforrás erőforrás-azonosítója.

### <a name="remarks"></a>Megjegyzések

Az `resourceGroup()` függvény nem használható [az előfizetés szintjén üzembe helyezett](deploy-to-subscription.md)sablonban. Csak az erőforráscsoporthoz központilag telepített sablonokban használható.

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

A resourceGroup függvény használatával címkéket is alkalmazhat az erőforráscsoporthoz egy erőforráshoz. További információ: [címkék alkalmazása az erőforráscsoporthoz](resource-group-using-tags.md#apply-tags-from-resource-group).

### <a name="resource-group-example"></a>Erőforráscsoport – példa

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
  "type":"Microsoft.Resources/resourceGroups",
  "location": "southcentralus",
  "properties": {
    "provisioningState": "Succeeded"
  }
}
```

## <a name="resourceid"></a>resourceId

```json
resourceId([subscriptionId], [resourceGroupName], resourceType, resourceName1, [resourceName2], ...)
```

Adja vissza egy adott erőforrás egyedi azonosítója. A függvény akkor használható, ha az erőforrás neve nem egyértelmű vagy ugyanabban a sablonban nincs kiépítve. 

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| subscriptionId |Nem |karakterlánc (a GUID formátumban) |Alapértelmezett érték az aktuális előfizetésben. Adja meg ezt az értéket, amikor szüksége van egy másik előfizetésben egy erőforrás lekérése. |
| resourceGroupName |Nem |sztring |Alapértelmezett érték az aktuális erőforráscsoportban. Adja meg ezt az értéket, amikor szüksége van egy másik erőforráscsoportban található egy erőforrás lekérése. |
| resourceType |Igen |sztring |Felhasznált erőforrás típusa, beleértve az erőforrás-szolgáltató névtere. |
| resourceName1 |Igen |sztring |Erőforrás neve. |
| resourceName2 |Nem |sztring |A következő erőforrás neve szegmens, ha szükséges. |

Ha az erőforrás típusa több szegmenst tartalmaz, folytassa a paraméterek hozzáadását paraméterként.

### <a name="return-value"></a>Vrácená hodnota

Az erőforrás-azonosítót a következő formátumban adja vissza:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Ha [előfizetési szintű központi telepítésben](deploy-to-subscription.md)használja, az erőforrás-azonosítót a következő formátumban adja vissza a rendszer:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Az azonosító más formátumokban való lekéréséhez lásd:

* [extensionResourceId](#extensionresourceid)
* [subscriptionResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

### <a name="remarks"></a>Megjegyzések

Az Ön által megadott paraméterek száma attól függően változik, hogy az erőforrás szülő-vagy gyermek erőforrás-e, és hogy az erőforrás ugyanabban az előfizetésben vagy erőforráscsoporthoz van-e.

Az azonos előfizetésben és erőforráscsoporthoz tartozó szülő erőforráshoz tartozó erőforrás-azonosító lekéréséhez adja meg az erőforrás típusát és nevét.

```json
"[resourceId('Microsoft.ServiceBus/namespaces', 'namespace1')]"
```

Egy alárendelt erőforrás erőforrás-AZONOSÍTÓjának lekéréséhez figyeljen az erőforrástípus szegmensek számára. Adja meg az erőforrástípus minden szegmensének erőforrás-nevét. A szegmens neve a hierarchia adott részéhez tartozó erőforrásnak felel meg.

```json
"[resourceId('Microsoft.ServiceBus/namespaces/queues/authorizationRules', 'namespace1', 'queue1', 'auth1')]"
```

Ha egy erőforrás erőforrás-AZONOSÍTÓját szeretné lekérni ugyanabba az előfizetésbe, de más erőforráscsoporthoz, adja meg az erőforráscsoport nevét.

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts', 'examplestorage')]"
```

Egy másik előfizetéshez és erőforráscsoporthoz tartozó erőforrás-azonosító lekéréséhez adja meg az előfizetés AZONOSÍTÓját és az erőforráscsoport nevét.

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
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

### <a name="resource-id-example"></a>Példa erőforrás-AZONOSÍTÓra

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
| sameRGOutput | Sztring | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentRGOutput | Sztring | /subscriptions/{current-sub-id}/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| differentSubOutput | Sztring | /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| nestedResourceOutput | Sztring | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/serverName/databases/databaseName |

## <a name="subscription"></a>előfizetést

```json
subscription()
```

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

### <a name="subscription-example"></a>Előfizetés – példa

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

## <a name="subscriptionresourceid"></a>subscriptionResourceId

```json
subscriptionResourceId([subscriptionId], resourceType, resourceName1, [resourceName2], ...)
```

Az előfizetési szinten üzembe helyezett erőforrás egyedi azonosítóját adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| subscriptionId |Nem |karakterlánc (GUID formátumban) |Alapértelmezett érték az aktuális előfizetésben. Adja meg ezt az értéket, amikor szüksége van egy másik előfizetésben egy erőforrás lekérése. |
| resourceType |Igen |sztring |Felhasznált erőforrás típusa, beleértve az erőforrás-szolgáltató névtere. |
| resourceName1 |Igen |sztring |Erőforrás neve. |
| resourceName2 |Nem |sztring |A következő erőforrás neve szegmens, ha szükséges. |

Ha az erőforrás típusa több szegmenst tartalmaz, folytassa a paraméterek hozzáadását paraméterként.

### <a name="return-value"></a>Vrácená hodnota

Az azonosító a következő formátumban adja vissza:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Megjegyzések

Ezzel a függvénnyel lekérheti az erőforrás-AZONOSÍTÓt az [előfizetésre az erőforráscsoport helyett központilag telepített](deploy-to-subscription.md) erőforrásokhoz. A visszaadott azonosító eltér a [resourceId](#resourceid) függvény által visszaadott értéktől, mert nem tartalmazza az erőforráscsoport értékét.

### <a name="subscriptionresourceid-example"></a>subscriptionResourceID példa

A következő sablon egy beépített szerepkört rendel hozzá. Egy erőforráscsoport vagy előfizetés számára is telepíthető. A subscriptionResourceId függvény használatával beolvassa a beépített szerepkörök erőforrás-AZONOSÍTÓját.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        },
        "builtInRoleType": {
            "type": "string",
            "allowedValues": [
                "Owner",
                "Contributor",
                "Reader"
            ],
            "metadata": {
                "description": "Built-in role to assign"
            }
        },
        "roleNameGuid": {
            "type": "string",
            "defaultValue": "[newGuid()]",
            "metadata": {
                "description": "A new GUID used to identify the role assignment"
            }
        }
    },
    "variables": {
        "Owner": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
        "Contributor": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Reader": "[subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[parameters('roleNameGuid')]",
            "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

## <a name="tenantresourceid"></a>tenantResourceId

```json
tenantResourceId(resourceType, resourceName1, [resourceName2], ...)
```

A bérlői szinten üzembe helyezett erőforrás egyedi azonosítóját adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| resourceType |Igen |sztring |Felhasznált erőforrás típusa, beleértve az erőforrás-szolgáltató névtere. |
| resourceName1 |Igen |sztring |Erőforrás neve. |
| resourceName2 |Nem |sztring |A következő erőforrás neve szegmens, ha szükséges. |

Ha az erőforrás típusa több szegmenst tartalmaz, folytassa a paraméterek hozzáadását paraméterként.

### <a name="return-value"></a>Vrácená hodnota

Az azonosító a következő formátumban adja vissza:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Megjegyzések

Ezzel a függvénnyel lekérheti a bérlőhöz központilag telepített erőforrások erőforrás-AZONOSÍTÓját. A visszaadott azonosító eltér a más erőforrás-azonosító függvények által visszaadott értékektől, ha nem tartalmazza az erőforráscsoport vagy az előfizetés értékét.

## <a name="next-steps"></a>További lépések

* A szakaszok az Azure Resource Manager-sablon ismertetését lásd: [Azure Resource Manager-sablonok készítése](resource-group-authoring-templates.md).
* Több sablon egyesíteni, lásd: [kapcsolt sablonok használata az Azure Resource Manager](resource-group-linked-templates.md).
* A megadott számú alkalommal újrafuttathatja egy adott típusú erőforrás létrehozásakor, lásd: [több erőforráspéldány létrehozása az Azure Resource Manager](resource-group-create-multiple.md).
* A létrehozott sablon üzembe helyezése, olvassa el [alkalmazás üzembe helyezése Azure Resource Manager-sablonnal](resource-group-template-deploy.md).

