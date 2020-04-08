---
title: Sablonfüggvények - erőforrások
description: Az Azure Resource Manager-sablonban az erőforrásokkal kapcsolatos értékek lekéréséhez használandó függvények ismertetése.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 90cee78c29c26c88d808cdef798e74a2184a5fcf
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804758"
---
# <a name="resource-functions-for-arm-templates"></a>Erőforrás-függvények ARM-sablonokhoz

Az Erőforrás-kezelő a következő függvényeket biztosítja az erőforrásértékek beszerzéséhez az Azure Resource Manager (ARM) sablonban:

* [extensionResourceId](#extensionresourceid)
* [lista*](#list)
* [Szolgáltatók](#providers)
* [Hivatkozás](#reference)
* [erőforrásCsoport](#resourcegroup)
* [resourceId](#resourceid)
* [Előfizetés](#subscription)
* [előfizetésResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

A paraméterekből, változókból vagy az aktuális központi telepítésből származó értékek beolvassa a [Telepítési érték függvények című témakört.](template-functions-deployment.md)

## <a name="extensionresourceid"></a>extensionResourceId

```json
extensionResourceId(resourceId, resourceType, resourceName1, [resourceName2], ...)
```

Egy [bővítményerőforrás](../management/extension-resource-types.md)erőforrásazonosítóját adja eredményül, amely egy másik erőforrásra alkalmazott erőforrástípus, amelyet a képességekhez szeretne hozzáadni.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| resourceId |Igen |sztring |Annak az erőforrásnak az erőforrásazonosítója, amelyre a bővítményerőforrás vonatkozik. |
| resourceType |Igen |sztring |Erőforrás típusa, beleértve az erőforrás-szolgáltató névterét. |
| erőforrásnév1 |Igen |sztring |Az erőforrás neve. |
| erőforrásNév2 |Nem |sztring |Szükség esetén következő erőforrásnév-szegmens. |

Folytassa az erőforrásnevek paraméterként való hozzáadását, ha az erőforrástípus több szegmenst tartalmaz.

### <a name="return-value"></a>Visszatérítési érték

A függvény által visszaadott erőforrás-azonosító alapvető formátuma:

```json
{scope}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

A hatókörszegmens a kiterjesztett erőforrástól függően változik.

Ha a bővítményerőforrást egy **erőforrásra**alkalmazza, az erőforrás-azonosító a következő formátumban jelenik meg:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{baseResourceProviderNamespace}/{baseResourceType}/{baseResourceName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Ha a bővítményerőforrást **erőforráscsoportra**alkalmazza, a formátum a következő:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Amikor a bővítményerőforrást **egy előfizetésre**alkalmazza, a formátum a következő:

```json
/subscriptions/{subscriptionId}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

Ha a bővítményerőforrást **egy felügyeleti csoportra**alkalmazza, a formátum a következő:

```json
/providers/Microsoft.Management/managementGroups/{managementGroupName}/providers/{extensionResourceProviderNamespace}/{extensionResourceType}/{extensionResourceName}
```

### <a name="extensionresourceid-example"></a>extensionResourceId példa

A következő példa egy erőforráscsoport zárolásának erőforrásazonosítóját adja vissza.

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

## <a name="list"></a>lista*

```json
list{Value}(resourceName or resourceIdentifier, apiVersion, functionValues)
```

A függvény szintaxisa a listaműveletek neve szerint változik. Minden implementáció a listaműveletet támogató erőforrástípus értékeit adja vissza. A művelet nevének `list`a- val kell kezdődnie. Néhány gyakori használat `listKeys` `listSecrets`a és a.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| resourceName vagy resourceIdentifier |Igen |sztring |Az erőforrás egyedi azonosítója |
| apiVersion |Igen |sztring |Az erőforrás-futásidejű állapot API-verziója. Jellemzően, a formátum, **yyyy-mm-dd**. |
| függvényértékek |Nem |objektum | Olyan objektum, amely a függvény értékeit tartalmazja. Csak olyan függvények számára adja meg ezt az objektumot, amelyek támogatják a paraméteres értékekkel rendelkező objektumok fogadását, például **a storage-fióklistAccountSas-értékét.** Ebben a cikkben látható egy példa a függvényértékek átadására. |

### <a name="valid-uses"></a>Érvényes felhasználások

A listafüggvények csak az erőforrás-definíció tulajdonságaiban és a sablon vagy központi telepítés kimenetek szakaszában használhatók. Ha [tulajdonságiterséggel](copy-properties.md)használja, használhatja a `input` listafüggvényeket, mert a kifejezés az erőforrás tulajdonsághoz van rendelve. Nem használhatja őket, `count` mert a számlálót meg kell határozni a listafüggvény feloldása előtt.

### <a name="implementations"></a>Megvalósítások

A lista* lehetséges felhasználási területe az alábbi táblázatban látható.

| Erőforrás típusa | Függvény neve |
| ------------- | ------------- |
| Microsoft.AnalysisServices/kiszolgálók | [listGatewayStatus](/rest/api/analysisservices/servers/listgatewaystatus) |
| Microsoft.AppConfiguration/configurationStores | Listabillentyűk |
| Microsoft.Automation/automationAccounts | [listabillentyűk](/rest/api/automation/keys/listbyautomationaccount) |
| Microsoft.Batch/batchAccounts | [listabillentyűk](/rest/api/batchmanagement/batchaccount/getkeys) |
| Microsoft.BatchAI/munkaterületek/kísérletek/feladatok | [listoutputfájlok](/rest/api/batchai/jobs/listoutputfiles) |
| Microsoft.Blockchain/blockchainMembers | [listApiKeys billentyűk](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys) |
| Microsoft.Blockchain/blockchainMembers/transactionNodes | [listApiKeys billentyűk](/rest/api/blockchain/2019-06-01-preview/transactionnodes/listapikeys) |
| Microsoft.Cache/redis | [listabillentyűk](/rest/api/redis/redis/listkeys) |
| Microsoft.CognitiveServices/fiókok | [listabillentyűk](/rest/api/cognitiveservices/accountmanagement/accounts/listkeys) |
| Microsoft.ContainerRegistry/regisztika | [listBuildSourceUploadUrl](/rest/api/containerregistry/registries%20(tasks)/getbuildsourceuploadurl) |
| Microsoft.ContainerRegistry/regisztika | [listCredentials](/rest/api/containerregistry/registries/listcredentials) |
| Microsoft.ContainerRegistry/regisztika | [listUsages](/rest/api/containerregistry/registries/listusages) |
| Microsoft.ContainerRegistry/regisztika/webhookok | [listEvents (események)](/rest/api/containerregistry/webhooks/listevents) |
| Microsoft.ContainerRegistry/regisztika/futtatás | [listLogSasUrl](/rest/api/containerregistry/runs/getlogsasurl) |
| Microsoft.ContainerRegistry/regisztika/feladatok | [listDetails (List-](/rest/api/containerregistry/tasks/getdetails) |
| Microsoft.ContainerService/managedClusters | [listClusterAdminCredential](/rest/api/aks/managedclusters/listclusteradmincredentials) |
| Microsoft.ContainerService/managedClusters | [listClusterUserCredential](/rest/api/aks/managedclusters/listclusterusercredentials) |
| Microsoft.ContainerService/managedClusters/accessProfiles | [listCredential](/rest/api/aks/managedclusters/getaccessprofile) |
| Microsoft.DataBox/jobs | listCredentials |
| Microsoft.DataFactory/datafactories/gateways | listauthkeys |
| Microsoft.DataFactory/gyárak/integrationruntimes | [listauthkeys](/rest/api/datafactory/integrationruntimes/listauthkeys) |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Containers | [listSasTokens](/rest/api/datalakeanalytics/storageaccounts/listsastokens) |
| Microsoft.DataShare/fiókok/megosztások | [listaSzinkronizálások](/rest/api/datashare/shares/listsynchronizations) |
| Microsoft.DataShare/accounts/shareSubscriptions | [listSourceSharesynchronizationBeállítások](/rest/api/datashare/sharesubscriptions/listsourcesharesynchronizationsettings) |
| Microsoft.DataShare/accounts/shareSubscriptions | [listSzinkronizálásrészletek](/rest/api/datashare/sharesubscriptions/listsynchronizationdetails) |
| Microsoft.DataShare/accounts/shareSubscriptions | [listaSzinkronizálások](/rest/api/datashare/sharesubscriptions/listsynchronizations) |
| Microsoft.Devices/iotHubs | [listabillentyűk](/rest/api/iothub/iothubresource/listkeys) |
| Microsoft.Devices/iotHubs/iotHubKeys | [listabillentyűk](/rest/api/iothub/iothubresource/getkeysforkeyname) |
| Microsoft.Devices/provisioningServices/keys | [listabillentyűk](/rest/api/iot-dps/iotdpsresource/listkeysforkeyname) |
| Microsoft.Devices/provisioningServices | [listabillentyűk](/rest/api/iot-dps/iotdpsresource/listkeys) |
| Microsoft.DevTestLab/labs | [ListVhds](/rest/api/dtl/labs/listvhds) |
| Microsoft.DevTestLab/labs/schedules | [ListApplicable](/rest/api/dtl/schedules/listapplicable) |
| Microsoft.DevTestLab/labs/users/serviceFabrics | [ListApplicableSchedules](/rest/api/dtl/servicefabrics/listapplicableschedules) |
| Microsoft.DevTestLab/labs/virtualMachines | [ListApplicableSchedules](/rest/api/dtl/virtualmachines/listapplicableschedules) |
| Microsoft.DocumentDB/databaseAccounts | [listConnectionStrings](/rest/api/cosmos-db-resource-provider/databaseaccounts/listconnectionstrings) |
| Microsoft.DocumentDB/databaseAccounts | [listabillentyűk](/rest/api/cosmos-db-resource-provider/databaseaccounts/listkeys) |
| Microsoft.DomainRegistration | [listDomainRecommendations](/rest/api/appservice/domains/listrecommendations) |
| Microsoft.DomainRegistration/topLevelDomains | [listAgreements (Megállapodások)](/rest/api/appservice/topleveldomains/listagreements) |
| Microsoft.EventGrid/tartományok | [listabillentyűk](/rest/api/eventgrid/version2019-06-01/domains/listsharedaccesskeys) |
| Microsoft.EventGrid/témakörök | [listabillentyűk](/rest/api/eventgrid/version2019-06-01/topics/listsharedaccesskeys) |
| Microsoft.EventHub/namespaces/authorizationRules | [listabillentyűk](/rest/api/eventhub/namespaces/listkeys) |
| Microsoft.EventHub/namespaces/disasterRecoveryConfigs/authorizationRules | [listabillentyűk](/rest/api/eventhub/disasterrecoveryconfigs/listkeys) |
| Microsoft.EventHub/namespaces/eventhubs/authorizationRules | [listabillentyűk](/rest/api/eventhub/eventhubs/listkeys) |
| Microsoft.ImportExport/feladatok | [listBitLockerKeys](/rest/api/storageimportexport/bitlockerkeys/list) |
| Microsoft.Kusto/Clusters/Databases | [ListPrincipals (Listaigazgatók)](/rest/api/azurerekusto/databases/listprincipals) |
| Microsoft.LabServices/users | [ListEnvironments (Környezetek listázása)](/rest/api/labservices/globalusers/listenvironments) |
| Microsoft.LabServices/users | [ListLabs (ListLabs)](/rest/api/labservices/globalusers/listlabs) |
| Microsoft.Logic/integrationAccounts/agreements | [listContentCallbackUrl](/rest/api/logic/agreements/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/assemblies | [listContentCallbackUrl](/rest/api/logic/integrationaccountassemblies/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listCallbackUrl](/rest/api/logic/integrationaccounts/getcallbackurl) |
| Microsoft.Logic/integrationAccounts | [listKeyVaultKeys](/rest/api/logic/integrationaccounts/listkeyvaultkeys) |
| Microsoft.Logic/integrationAccounts/maps | [listContentCallbackUrl](/rest/api/logic/maps/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/partners | [listContentCallbackUrl](/rest/api/logic/partners/listcontentcallbackurl) |
| Microsoft.Logic/integrationAccounts/sémák | [listContentCallbackUrl](/rest/api/logic/schemas/listcontentcallbackurl) |
| Microsoft.Logic/munkafolyamatok | [listCallbackUrl](/rest/api/logic/workflows/listcallbackurl) |
| Microsoft.Logic/munkafolyamatok | [listSwagger között](/rest/api/logic/workflows/listswagger) |
| Microsoft.Logic/workflows/runs/actions | [listExpressionTraces](/rest/api/logic/workflowrunactions/listexpressiontraces) |
| Microsoft.Logic/workflows/runs/actions/reszussza | [listExpressionTraces](/rest/api/logic/workflowrunactionrepetitions/listexpressiontraces) |
| Microsoft.Logic/workflows/triggers | [listCallbackUrl](/rest/api/logic/workflowtriggers/listcallbackurl) |
| Microsoft.Logic/workflows/versions/triggers | [listCallbackUrl](/rest/api/logic/workflowversions/listcallbackurl) |
| Microsoft.MachineLearning/webServices | [listabillentyűk](/rest/api/machinelearning/webservices/listkeys) |
| Microsoft.MachineLearning/Workspaces | listworkspacekeys |
| Microsoft.MachineLearningServices/munkaterületek/számítás | [listabillentyűk](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listkeys) |
| Microsoft.MachineLearningServices/munkaterületek/számítás | [listNodes](/rest/api/azureml/workspacesandcomputes/machinelearningcompute/listnodes) |
| Microsoft.MachineLearningServices/munkaterületek | [listabillentyűk](/rest/api/azureml/workspacesandcomputes/workspaces/listkeys) |
| Microsoft.Maps/fiókok | [listabillentyűk](/rest/api/maps-management/accounts/listkeys) |
| Microsoft.Media/mediaservices/assets | [listContainerSas](/rest/api/media/assets/listcontainersas) |
| Microsoft.Media/mediaservices/assets | [listStreamingLocators (StreamingLocators)](/rest/api/media/assets/liststreaminglocators) |
| Microsoft.Media/mediaservices/streamingLocators | [listContentKeys](/rest/api/media/streaminglocators/listcontentkeys) |
| Microsoft.Media/mediaservices/streamingLocators | [listPaths (Görbék)](/rest/api/media/streaminglocators/listpaths) |
| Microsoft.Network/applicationSecurityGroups | listIpKonfigurációk |
| Microsoft.NotificationHubs/Namespaces/authorizationRules | [listabillentyűk](/rest/api/notificationhubs/namespaces/listkeys) |
| Microsoft.NotificationHubs/Namespaces/NotificationHubs/authorizationRules | [listabillentyűk](/rest/api/notificationhubs/notificationhubs/listkeys) |
| Microsoft.OperationalInsights/munkaterületek | [listabillentyűk](/rest/api/loganalytics/workspaces%202015-03-20/listkeys) |
| Microsoft.PolicyInsights/remediations | [listDeployments](/rest/api/policy-insights/remediations/listdeploymentsatresourcegroup) |
| Microsoft.Relay/namespaces/authorizationRules | [listabillentyűk](/rest/api/relay/namespaces/listkeys) |
| Microsoft.Relay/namespaces/disasterRecoveryConfigs/authorizationRules | listabillentyűk |
| Microsoft.Relay/namespaces/HybridConnections/authorizationRules | [listabillentyűk](/rest/api/relay/hybridconnections/listkeys) |
| Microsoft.Relay/namespaces/WcfRelays/authorizationRules | [listabillentyűk](/rest/api/relay/wcfrelays/listkeys) |
| Microsoft.Search/searchServices | [listAdminKeys](/rest/api/searchmanagement/adminkeys/get) |
| Microsoft.Search/searchServices | [listQueryKeys](/rest/api/searchmanagement/querykeys/listbysearchservice) |
| Microsoft.ServiceBus/namespaces/authorizationRules | [listabillentyűk](/rest/api/servicebus/namespaces/listkeys) |
| Microsoft.ServiceBus/namespaces/disasterRecoveryConfigs/authorizationRules | [listabillentyűk](/rest/api/servicebus/disasterrecoveryconfigs/listkeys) |
| Microsoft.ServiceBus/namespaces/queues/authorizationRules | [listabillentyűk](/rest/api/servicebus/queues/listkeys) |
| Microsoft.ServiceBus/namespaces/topics/authorizationRules | [listabillentyűk](/rest/api/servicebus/topics/listkeys) |
| Microsoft.SignalRService/SignalR | [listabillentyűk](/rest/api/signalr/signalr/listkeys) |
| Microsoft.Storage/storageAccounts | [listAccountSas](/rest/api/storagerp/storageaccounts/listaccountsas) |
| Microsoft.Storage/storageAccounts | [listabillentyűk](/rest/api/storagerp/storageaccounts/listkeys) |
| Microsoft.Storage/storageAccounts | [listServiceSas](/rest/api/storagerp/storageaccounts/listservicesas) |
| Microsoft.StorSimple/managers/devices | [listFailoverSets](/rest/api/storsimple/devices/listfailoversets) |
| Microsoft.StorSimple/managers/devices | [listFailoverTargets](/rest/api/storsimple/devices/listfailovertargets) |
| Microsoft.StorSimple/managers | [listActivationKey](/rest/api/storsimple/managers/getactivationkey) |
| Microsoft.StorSimple/managers | [listPublicEncryptionKey](/rest/api/storsimple/managers/getpublicencryptionkey) |
| Microsoft.Web/connectionGateways | ListStatus (Listaállapota) |
| microsoft.web/kapcsolatok | listconsentlinks |
| Microsoft.Web/customApis | listWsdlInterfaces (WsdlInterfaces) |
| microsoft.web/locations | listwsdlinters |
| microsoft.web/apimanagementaccounts/apis/connections | listconnectionkeys |
| microsoft.web/apimanagementaccounts/apis/connections | listsecrets (listatitok) |
| microsoft.web/sites/backups | [lista](/rest/api/appservice/webapps/listbackups) |
| Microsoft.Web/sites/config | [lista](/rest/api/appservice/webapps/listconfigurations) |
| microsoft.web/sites/functions | [listabillentyűk](/rest/api/appservice/webapps/listfunctionkeys)
| microsoft.web/sites/functions | [listsecrets (listatitok)](/rest/api/appservice/webapps/listfunctionsecrets) |
| microsoft.web/sites/hybridconnectionnamespaces/relays | [listabillentyűk](/rest/api/appservice/appserviceplans/listhybridconnectionkeys) |
| microsoft.web/webhelyek | [listsyncfunctiontriggerstatus](/rest/api/appservice/webapps/listsyncfunctiontriggers) |
| microsoft.web/sites/slots/functions | [listsecrets (listatitok)](/rest/api/appservice/webapps/listfunctionsecretsslot) |
| microsoft.web/sites/slots/backups | [lista](/rest/api/appservice/webapps/listbackupsslot) |
| Microsoft.Web/sites/slots/config | [lista](/rest/api/appservice/webapps/listconfigurationsslot) |
| microsoft.web/sites/slots/functions | [listsecrets (listatitok)](/rest/api/appservice/webapps/listfunctionsecretsslot) |

Annak meghatározásához, hogy mely erőforrástípusok rendelkeznek listaművelettel, a következő lehetőségek közül választhat:

* Tekintse meg egy erőforrás-szolgáltató [REST API-műveleteit,](/rest/api/) és keresse meg a listaműveleteket. A tárfiókok például a [listKeys művelettel](/rest/api/storagerp/storageaccounts)rendelkeznek.
* Használja a [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) PowerShell parancsmag. A következő példa lekéri a tárfiókok összes listaműveletét:

  ```powershell
  Get-AzProviderOperation -OperationSearchString "Microsoft.Storage/*" | where {$_.Operation -like "*list*"} | FT Operation
  ```
* A következő Azure CLI-paranccsal csak a listaműveleteket szűrheti:

  ```azurecli
  az provider operation show --namespace Microsoft.Storage --query "resourceTypes[?name=='storageAccounts'].operations[].name | [?contains(@, 'list')]"
  ```

### <a name="return-value"></a>Visszatérítési érték

A visszaadott objektum a használt listafüggvénytől függ. A tárfiók keys listKeys list keys(kulcsok) listája például a következő formátumot adja vissza:

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

Más listafüggvények különböző visszatérési formátumúak. A függvény formátumának megtekintéséhez adja meg a kimenetek szakaszban a példasablonban látható módon.

### <a name="remarks"></a>Megjegyzések

Adja meg az erőforrást az erőforrás nevével vagy az [erőforrásazonosító függvénnyel.](#resourceid) Ha listafüggvényt használ ugyanabban a sablonban, amely a hivatkozott erőforrást telepíti, használja az erőforrás nevét.

Ha egy **listafüggvényt** használ egy feltételesen üzembe helyezett erőforrásban, a függvény akkor is kiértékelésre kerül, ha az erőforrás nincs telepítve. Hibaüzenet et kap, ha a **listafüggvény** nem létező erőforrásra hivatkozik. Az **if** függvény segítségével győződjön meg arról, hogy a függvény csak az erőforrás üzembe helyezésekor kerül kiértékelésre. Tekintse meg a [ha függvényt](template-functions-logical.md#if) egy mintasablonhoz, amely feltételesen telepített erőforrással használja a ha és a listát.

### <a name="list-example"></a>Példa lista

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/listkeys.json) bemutatja, hogyan adja vissza az elsődleges és másodlagos kulcsokat egy tárfiókból a kimenetek szakaszban. Azt is visszaadja a SAS-jogkivonatot a tárfiókhoz.

A SAS-jogkivonat lekéréséhez adja át egy objektumot a lejárati idő. A lejárati időnek a jövőben kell lennie. Ez a példa a listafüggvények használatának bemutatására szolgál. Általában a SAS-jogkivonatot egy erőforrásértékben kell használni, ahelyett, hogy kimeneti értékként adja volna vissza. A kimeneti értékek a központi telepítési előzményekben tárolódnak, és nem biztonságosak.

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

Információt ad vissza egy erőforrás-szolgáltatóról és annak támogatott erőforrástípusairól. Ha nem ad meg erőforrástípust, a függvény az erőforrás-szolgáltató összes támogatott típusát adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| providerNamespace között |Igen |sztring |A szolgáltató névtere |
| resourceType |Nem |sztring |A megadott névtérben lévő erőforrás típusa. |

### <a name="return-value"></a>Visszatérítési érték

Minden támogatott típus a következő formátumban jelenik meg:

```json
{
    "resourceType": "{name of resource type}",
    "locations": [ all supported locations ],
    "apiVersions": [ all supported API versions ]
}
```

A visszaadott értékek tömbsorrendje nem garantált.

### <a name="providers-example"></a>Példa szolgáltatókra

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/providers.json) bemutatja a szolgáltatói függvény használatát:

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

A **Microsoft.Web** erőforrás-szolgáltató és **webhelyerőforrás-típus** esetében az előző példa a következő formátumú objektumot adja vissza:

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

Egy erőforrás futásidejű állapotát képviselő objektumot ad vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| resourceName vagy resourceIdentifier |Igen |sztring |Egy erőforrás neve vagy egyedi azonosítója. Amikor az aktuális sablonban hivatkozik egy erőforrásra, csak az erőforrás nevét adja meg paraméterként. Ha egy korábban üzembe helyezett erőforrásra hivatkozik, vagy ha az erőforrás neve nem egyértelmű, adja meg az erőforrás-azonosítót. |
| apiVersion |Nem |sztring |A megadott erőforrás API-verziója. **Erre a paraméterre akkor van szükség, ha az erőforrás nincs kiépítve ugyanazon a sablonon belül.** Jellemzően, a formátum, **yyyy-mm-dd**. Az erőforrás érvényes API-verzióiról a [sablon hivatkozása oldalon](/azure/templates/)olvashat. |
| "Teljes" |Nem |sztring |Érték, amely meghatározza, hogy a teljes erőforrásobjektumot adja-e vissza. Ha nem adja `'Full'`meg, csak az erőforrás tulajdonságobjektumát adja vissza. A teljes objektum olyan értékeket tartalmaz, mint az erőforrás-azonosító és a hely. |

### <a name="return-value"></a>Visszatérítési érték

Minden erőforrástípus különböző tulajdonságokat ad vissza a referenciafüggvényhez. A függvény nem ad vissza egyetlen, előre definiált formátumot. A visszaadott érték az `'Full'` argumentum értékététől függően is eltérő. Egy erőforrástípus tulajdonságainak megtekintéséhez adja vissza az objektumot a kimenetek szakaszban a példában látható módon.

### <a name="remarks"></a>Megjegyzések

A referenciafüggvény lekéri egy korábban üzembe helyezett erőforrás vagy az aktuális sablonban telepített erőforrás futásidejű állapotát. Ez a cikk mindkét forgatókönyvre vonatkozó példákat mutat be.

A **hivatkozási** függvény t általában egy adott értéket ad vissza egy objektumból, például a blob végpont URI-ból vagy a teljesen minősített tartománynévből.

```json
"outputs": {
    "BlobUri": {
        "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName'))).primaryEndpoints.blob]",
        "type" : "string"
    },
    "FQDN": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName'))).dnsSettings.fqdn]",
        "type" : "string"
    }
}
```

Akkor `'Full'` használja, ha olyan erőforrásértékekre van szüksége, amelyek nem részei a tulajdonságsémának. Például kulcstartó hozzáférési szabályzatok beállításához egy virtuális gép identitástulajdonságainak lekérni.

```json
{
  "type": "Microsoft.KeyVault/vaults",
  "properties": {
    "tenantId": "[subscription().tenantId]",
    "accessPolicies": [
      {
        "tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.tenantId]",
        "objectId": "[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2019-03-01', 'Full').identity.principalId]",
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

### <a name="valid-uses"></a>Érvényes felhasználások

A referenciafüggvény csak az erőforrás-definíció és a sablon vagy központi telepítés kimeneti szakaszában használható. Ha [tulajdonságiterséggel](copy-properties.md)használja, használhatja a `input` hivatkozási függvényt, mert a kifejezés az erőforrás tulajdonsághoz van rendelve.

A hivatkozási függvénnyel nem állíthatja `count` be a tulajdonság értékét egy másolási ciklusban. Más tulajdonságokat is beállíthat a ciklusban. A hivatkozás le van tiltva a count tulajdonsághoz, mert ezt a tulajdonságot meg kell határozni a referenciafüggvény feloldása előtt.

A beágyazott [sablon](linked-templates.md#nested-template) kimeneteiben a hivatkozási függvény nem használható a beágyazott sablonban üzembe helyezett erőforrás visszaadására. Ehelyett használjon [csatolt sablont](linked-templates.md#linked-template).

Ha a **referencia** függvényt egy feltételesen üzembe helyezett erőforrásban használja, a függvény akkor is kiértékelésre kerül, ha az erőforrás nincs telepítve.  Hibaüzenetet kap, ha a **hivatkozási** függvény nem létező erőforrásra hivatkozik. Az **if** függvény segítségével győződjön meg arról, hogy a függvény csak az erőforrás üzembe helyezésekor kerül kiértékelésre. Tekintse meg a [ha függvényt](template-functions-logical.md#if) egy mintasablonhoz, amely feltételesen üzembe helyezett erőforrással használja a ha és a hivatkozás funkciót.

### <a name="implicit-dependency"></a>Implicit függőség

A referencia függvény használatával implicit módon deklarálhatja, hogy az egyik erőforrás egy másik erőforrástól függ, ha a hivatkozott erőforrás ugyanabban a sablonban van kiépítve, és az erőforrásra a neve alapján hivatkozik (nem erőforrásazonosító). Nem kell a dependsOn tulajdonságot is használnia. A függvény nem kiértékelve, amíg a hivatkozott erőforrás befejeződött a központi telepítés.

### <a name="resource-name-or-identifier"></a>Erőforrás neve vagy azonosítója

Ha ugyanabban a sablonban üzembe helyezett erőforrásra hivatkozik, adja meg az erőforrás nevét.

```json
"value": "[reference(parameters('storageAccountName'))]"
```

Ha olyan erőforrásra hivatkozik, amely nincs ugyanabban a sablonban `apiVersion`telepítve, adja meg az erőforrás-azonosítót és a.

```json
"value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2018-07-01')]"
```

Annak elkerülése érdekében, hogy ne legyen egyértelmű, hogy melyik erőforrásra hivatkozik, megadhat egy teljesen minősített erőforrás-azonosítót.

```json
"value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', parameters('ipAddressName')))]"
```

Egy erőforrásra mutató teljesen minősített hivatkozás összeállításakor a szegmensek típusból és névből való egyesítésének sorrendje nem egyszerűen a kettő összefűzése. Ehelyett a névtér után használjon *típus-/névpárok* sorozatát a legkevésbé specifikustól a legkonkrétabbig:

**{resource-provider-namespace}/{parent-resource-type}/{parent-resource-name}[/{child-resource-type}/{child-resource-name}]**

Például:

`Microsoft.Compute/virtualMachines/myVM/extensions/myExt`helyes, `Microsoft.Compute/virtualMachines/extensions/myVM/myExt` nem helyes

Az erőforrás-azonosítók létrehozásának egyszerűsítése érdekében a függvény `concat()` helyett a `resourceId()` jelen dokumentumban leírt függvényeket használja.

### <a name="get-managed-identity"></a>Felügyelt identitás beszereznie

[Az Azure-erőforrások felügyelt identitások](../../active-directory/managed-identities-azure-resources/overview.md) [olyan bővítményerőforrás-típusok,](../management/extension-resource-types.md) amelyek implicit módon jönnek létre egyes erőforrásokhoz. Mivel a felügyelt identitás nincs explicit módon definiálva a sablonban, hivatkoznia kell arra az erőforrásra, amelyre az identitás vonatkozik. Az `Full` összes tulajdonság lefoglalásához használható, beleértve az implicit módon létrehozott identitást is.

Például a bérlői azonosító lekérni egy felügyelt identitás, amely egy virtuálisgép méretezési csoport, használja:

```json
"tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), '2019-03-01', 'Full').Identity.tenantId]"
```

### <a name="reference-example"></a>Példa hivatkozásra

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/referencewithstorage.json) telepít egy erőforrást, és hivatkozik az erőforrásra.

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

Az előző példa a két objektumot adja vissza. A tulajdonságobjektum formátuma a következő:

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

A teljes objektum formátuma a következő:

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

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/reference.json) olyan tárfiókra hivatkozik, amely nincs telepítve ebben a sablonban. A tárfiók már létezik ugyanazon az előfizetésen belül.

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

Az aktuális erőforráscsoportot reprezentativó objektumot adja vissza.

### <a name="return-value"></a>Visszatérítési érték

A visszaadott objektum formátuma a következő:

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

A **managedBy** tulajdonság csak olyan erőforráscsoportok hoz vissza, amelyek egy másik szolgáltatás által kezelt erőforrásokat tartalmaznak. Felügyelt alkalmazások, Databricks és AKS esetén a tulajdonság értéke a kezelő erőforrás erőforrásazonosítója.

### <a name="remarks"></a>Megjegyzések

A `resourceGroup()` függvény nem használható olyan sablonban, amely [az előfizetés szintjén van telepítve.](deploy-to-subscription.md) Csak olyan sablonokban használható, amelyek egy erőforráscsoportra vannak telepítve. A `resourceGroup()` függvényt egy [összekapcsolt vagy beágyazott sablonban (belső hatókörrel)](linked-templates.md) használhatja, amely egy erőforráscsoportot céloz meg, még akkor is, ha a szülősablon telepítve van az előfizetésben. Ebben az esetben a csatolt vagy beágyazott sablon az erőforráscsoport szintjén van telepítve. Az erőforráscsoportok előfizetési szintű telepítésben történő célzásáról az [Azure-erőforrások telepítése több előfizetéshez vagy erőforráscsoporthoz című](cross-resource-group-deployment.md)témakörben talál további információt.

Az erőforráscsoport funkció közös használata, ha az erőforráscsoporttal azonos helyen hoz létre erőforrásokat. A következő példa az erőforráscsoport helyét használja egy alapértelmezett paraméterértékhez.

```json
"parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
}
```

Az erőforráscsoport függvénnyel címkéket alkalmazhat az erőforráscsoportból egy erőforrásra. További információ: [Címkék alkalmazása erőforráscsoportból](../management/tag-resources.md#apply-tags-from-resource-group).

Ha beágyazott sablonokat használ több erőforráscsoportra való üzembe helyezéshez, megadhatja az erőforráscsoport függvény kiértékelésének hatókörét. További információ: [Azure-erőforrások telepítése több előfizetésre vagy erőforráscsoportra.](cross-resource-group-deployment.md)

### <a name="resource-group-example"></a>Példa erőforráscsoportra

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourcegroup.json) az erőforráscsoport tulajdonságait adja vissza.

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

Az előző példa a következő formátumú objektumot adja vissza:

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

Egy erőforrás egyedi azonosítóját adja vissza. Ezt a függvényt akkor használja, ha az erőforrás neve nem egyértelmű, vagy nem ugyanabban a sablonban van kiépítve. A visszaadott azonosító formátuma attól függően változik, hogy a központi telepítés egy erőforráscsoport, előfizetés, felügyeleti csoport vagy bérlő hatókörén történik.The format of the returned identifier varies on whether the deployment happens at the scope of a resource group, subscription, management group, or tenant.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| subscriptionId |Nem |karakterlánc (GUID formátumban) |Az alapértelmezett érték az aktuális előfizetés. Adja meg ezt az értéket, ha egy másik előfizetésben lévő erőforrást kell beolvasnia. Csak akkor adja meg ezt az értéket, ha egy erőforráscsoport vagy előfizetés hatókörén üzembe helyezi. |
| resourceGroupName |Nem |sztring |Az alapértelmezett érték az aktuális erőforráscsoport. Akkor adja meg ezt az értéket, ha egy másik erőforráscsoportban lévő erőforrást kell beolvasnia. Csak akkor adja meg ezt az értéket, ha egy erőforráscsoport hatókörén telepíti. |
| resourceType |Igen |sztring |Erőforrás típusa, beleértve az erőforrás-szolgáltató névterét. |
| erőforrásnév1 |Igen |sztring |Az erőforrás neve. |
| erőforrásNév2 |Nem |sztring |Szükség esetén következő erőforrásnév-szegmens. |

Folytassa az erőforrásnevek paraméterként való hozzáadását, ha az erőforrástípus több szegmenst tartalmaz.

### <a name="return-value"></a>Visszatérítési érték

Amikor a sablon t egy erőforráscsoport hatókörén van telepítve, az erőforrás-azonosító a következő formátumban jelenik meg:

```json
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

[Ha előfizetési szintű telepítésben](deploy-to-subscription.md)használja, az erőforrás-azonosító a következő formátumban jelenik meg:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Ha egy [felügyeleti csoportszintű központi telepítésben](deploy-to-management-group.md) vagy bérlői szintű telepítésben használatos, az erőforrás-azonosító a következő formátumban jelenik meg:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

Az azonosító más formátumban való lekért:

* [extensionResourceId](#extensionresourceid)
* [előfizetésResourceId](#subscriptionresourceid)
* [tenantResourceId](#tenantresourceid)

### <a name="remarks"></a>Megjegyzések

A megadott paraméterek száma attól függően változik, hogy az erőforrás szülő vagy gyermek erőforrás,,-e az erőforrás ugyanabban az előfizetésben vagy erőforráscsoportban.

Ha ugyanabban az előfizetésben és erőforráscsoportban lévő szülőerőforrás-azonosítót szeretné beszerezni, adja meg az erőforrás típusát és nevét.

```json
"[resourceId('Microsoft.ServiceBus/namespaces', 'namespace1')]"
```

Egy gyermekerőforrás erőforrás-azonosítójának lekérnie, figyeljen az erőforrástípus szegmenseinek számára. Erőforrásnév megadásához az erőforrástípus minden szegmenséhez. A szegmens neve megegyezik a hierarchia adott részéhez létező erőforrással.

```json
"[resourceId('Microsoft.ServiceBus/namespaces/queues/authorizationRules', 'namespace1', 'queue1', 'auth1')]"
```

Ha ugyanabban az előfizetésben, de eltérő erőforráscsoportban lévő erőforrás erőforrásazonosítóját szeretné lekérni, adja meg az erőforráscsoport nevét.

```json
"[resourceId('otherResourceGroup', 'Microsoft.Storage/storageAccounts', 'examplestorage')]"
```

Egy másik előfizetésben és erőforráscsoportban lévő erőforrás-azonosító lekérni, adja meg az előfizetés azonosítóját és az erőforráscsoport nevét.

```json
"[resourceId('xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', 'otherResourceGroup', 'Microsoft.Storage/storageAccounts','examplestorage')]"
```

Gyakran kell használni ezt a funkciót, ha egy tárfiókot vagy virtuális hálózatot használ egy másik erőforráscsoportban. A következő példa bemutatja, hogyan használható egy külső erőforráscsoportból származó erőforrás:

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

### <a name="resource-id-example"></a>Példa erőforrásazonosítóra

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/resourceid.json) az erőforráscsoport egyik tárfiókjának erőforrásazonosítóját adja vissza:

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

Az előző példa kimenete az alapértelmezett értékekkel a következő:

| Név | Típus | Érték |
| ---- | ---- | ----- |
| sameRGOutput | Sztring | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| eltérőRGOutput | Sztring | /subscriptions/{current-sub-id}/resourceGroups/otherResourceGroup/providers/Microsoft.StorageAccounts/examplestorage |
| differentSubOutput | Sztring | /subscriptions/11111111-1111-1111-111111111111/resourceGroups/otherResourceGroup/providers/Microsoft.Storage/storageAccounts/examplestorage |
| beágyazotterőforráskimenet | Sztring | /subscriptions/{current-sub-id}/resourceGroups/examplegroup/providers/Microsoft.SQL/servers/serverName/databases/databaseName |

## <a name="subscription"></a>előfizetést

```json
subscription()
```

Az aktuális központi telepítés előfizetésének részleteit adja vissza.

### <a name="return-value"></a>Visszatérítési érték

A függvény a következő formátumot adja eredményül:

```json
{
    "id": "/subscriptions/{subscription-id}",
    "subscriptionId": "{subscription-id}",
    "tenantId": "{tenant-id}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="remarks"></a>Megjegyzések

Ha beágyazott sablonokat használ több előfizetésre való üzembe helyezéshez, megadhatja az előfizetési függvény kiértékelésének hatókörét. További információ: [Azure-erőforrások telepítése több előfizetésre vagy erőforráscsoportra.](cross-resource-group-deployment.md)

### <a name="subscription-example"></a>Példa előfizetésre

A következő [példasablon](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/subscription.json) a kimenetek szakaszban hívott előfizetési függvényt mutatja be.

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

## <a name="subscriptionresourceid"></a>előfizetésResourceId

```json
subscriptionResourceId([subscriptionId], resourceType, resourceName1, [resourceName2], ...)
```

Az előfizetés szintjén üzembe helyezett erőforrás egyedi azonosítóját adja vissza.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Leírás |
|:--- |:--- |:--- |:--- |
| subscriptionId |Nem |karakterlánc (GUID formátumban) |Az alapértelmezett érték az aktuális előfizetés. Adja meg ezt az értéket, ha egy másik előfizetésben lévő erőforrást kell beolvasnia. |
| resourceType |Igen |sztring |Erőforrás típusa, beleértve az erőforrás-szolgáltató névterét. |
| erőforrásnév1 |Igen |sztring |Az erőforrás neve. |
| erőforrásNév2 |Nem |sztring |Szükség esetén következő erőforrásnév-szegmens. |

Folytassa az erőforrásnevek paraméterként való hozzáadását, ha az erőforrástípus több szegmenst tartalmaz.

### <a name="return-value"></a>Visszatérítési érték

Az azonosító t a következő formátumban adja vissza:

```json
/subscriptions/{subscriptionId}/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Megjegyzések

Ezzel a függvénnyel lekéri az [erőforrás-azonosítót az erőforráscsoport](deploy-to-subscription.md) helyett az előfizetésbe üzembe helyezett erőforrásokhoz. A visszaadott azonosító az [erőforrásazonosító](#resourceid) függvény által visszaadott értéktől tér el, ha nem tartalmazza az erőforráscsoport értékét.

### <a name="subscriptionresourceid-example"></a>például subscriptionResourceID

A következő sablon egy beépített szerepkört rendel hozzá. Telepítheti erőforráscsoportba vagy előfizetésbe. Az előfizetésResourceId függvényt használja a beépített szerepkörök erőforrás-azonosítójának lekérni.

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
| resourceType |Igen |sztring |Erőforrás típusa, beleértve az erőforrás-szolgáltató névterét. |
| erőforrásnév1 |Igen |sztring |Az erőforrás neve. |
| erőforrásNév2 |Nem |sztring |Szükség esetén következő erőforrásnév-szegmens. |

Folytassa az erőforrásnevek paraméterként való hozzáadását, ha az erőforrástípus több szegmenst tartalmaz.

### <a name="return-value"></a>Visszatérítési érték

Az azonosító t a következő formátumban adja vissza:

```json
/providers/{resourceProviderNamespace}/{resourceType}/{resourceName}
```

### <a name="remarks"></a>Megjegyzések

Ezzel a függvénnyel lekéri a bérlőre üzembe helyezett erőforrás erőforrás-azonosítójának lekérni. A visszaadott azonosító az erőforráscsoport vagy az előfizetési értékek nem tartalmazza az erőforráscsoport vagy az előfizetési értékeket.

## <a name="next-steps"></a>További lépések

* Az Azure Resource Manager-sablon szakaszainak leírását az [Azure Resource Manager-sablonok készítése című témakörben találja.](template-syntax.md)
* Több sablon egyesítéséhez olvassa el [a Csatolt sablonok használata az Azure Resource Manager rel.](linked-templates.md)
* Ha egy erőforrástípus létrehozásakor meghatározott számú alkalommal szeretne meghaladni, olvassa [el az Erőforrások több példányának létrehozása az Azure Resource Manager ben című témakört.](copy-resources.md)
* A létrehozott sablon központi telepítéséről az Alkalmazás üzembe helyezése az [Azure Resource Manager sablonnal című témakörben](deploy-powershell.md)olvashat.

