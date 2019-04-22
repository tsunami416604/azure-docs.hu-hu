---
title: Az Azure Functions egy függvényalkalmazáshoz erőforrások üzembe helyezésének automatizálása |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre az Azure Resource Manager-sablon, amely üzembe helyezi a függvényalkalmazást.
services: Functions
documtationcenter: na
author: ggailey777
manager: jeconnoc
keywords: az Azure functions, függvények, kiszolgáló nélküli architektúra, infrastruktúra-kód, az azure resource manager
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.service: azure-functions
ms.server: functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: glenga
ms.openlocfilehash: 5d028768c062ef7df74d48f83ccc4e27a506f1ac
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59270903"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>A függvényalkalmazás az Azure Functions erőforrások üzembe helyezésének automatizálása

Függvényalkalmazás üzembe helyezése Azure Resource Manager-sablon segítségével. Ez a cikk ismerteti a szükséges erőforrásokat és a paraméterek indoklását. Szükség lehet további erőforrásokat telepíteni attól függően, a [eseményindítók és kötések](functions-triggers-bindings.md) a függvényalkalmazásban.

Sablonok létrehozásával kapcsolatos további információkért lásd: [Azure Resource Manager-sablonok készítése](../azure-resource-manager/resource-group-authoring-templates.md).

Mintasablonok lásd:
- [A függvényalkalmazást a Használatalapú csomag]
- [Az Azure App Service-csomag függvényalkalmazás]

> [!NOTE]
> Az Azure Functions szolgáltatási a prémium szintű csomag jelenleg előzetes verzióban érhető el. További információkért lásd: [Azure Functions prémium szintű csomag](functions-premium-plan.md).

## <a name="required-resources"></a>Szükséges erőforrások

Ezeket az erőforrásokat általában tartalmaz egy Azure Functions üzembe helyezési:

| Erőforrás                                                                           | Követelmény | Szintaxist és a Tulajdonságok referencia                                                         |   |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|---|
| Függvényalkalmazás                                                                     | Szükséges    | [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)                             |   |
| Egy [Azure Storage](../storage/index.yml) fiók                                   | Szükséges    | [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |   |
| Egy [Application Insights](../azure-monitor/app/app-insights-overview.md) összetevő | Optional    | [Microsoft.Insights/components](/azure/templates/microsoft.insights/components)         |   |
| A [szolgáltatási csomag](./functions-scale.md)                                             | Nem kötelező<sup>1</sup>    | [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)                 |   |

<sup>1</sup>egy szolgáltatási csomagot csak akkor szükséges, ha úgy dönt, a függvényalkalmazás futtatásához egy [prémium szintű csomag](./functions-premium-plan.md) (az előzetes verzió) vagy a egy [App Service-csomag](../app-service/overview-hosting-plans.md).

> [!TIP]
> Bár nem kötelező, erősen ajánlott, hogy az Application Insights beállítása az alkalmazáshoz.

<a name="storage"></a>
### <a name="storage-account"></a>Tárfiók

Azure-tárfiókra szükség egy függvényalkalmazást. Egy általános célú fiók, amely támogatja a blobok, táblák, üzenetsorok és fájlok szükséges. További információkért lásd: [Azure Functions storage-fiókra vonatkozó követelmények](functions-create-function-app-portal.md#storage-account-requirements).

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2018-07-01",
    "location": "[resourceGroup().location]",
    "kind": "StorageV2",
    "properties": {
        "accountType": "[parameters('storageAccountType')]"
    }
}
```

Emellett a tulajdonság `AzureWebJobsStorage` kell megadni, a hely konfigurációban egy alkalmazásbeállításhoz. Ha a függvényalkalmazás nem használja az Application Insights figyelési, azt szintén meg kell adni `AzureWebJobsDashboard` , egy alkalmazásbeállításhoz.

Az Azure Functions runtime használja a `AzureWebJobsStorage` kapcsolati karakterlánc belső várólisták létrehozásához.  Az Application Insights nincs engedélyezve, ha a modul használja a `AzureWebJobsDashboard` kapcsolati karakterlánc jelentkezhetnek az Azure Table storage és a power a **figyelő** lapot a portálon.

Ezek a tulajdonságok vannak megadva a `appSettings` gyűjteményt a `siteConfig` objektum:

```json
"appSettings": [
    {
        "name": "AzureWebJobsStorage",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    },
    {
        "name": "AzureWebJobsDashboard",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
    }
]
```

### <a name="application-insights"></a>Application Insights

Az Application Insights figyelési a függvényalkalmazások használata ajánlott. Application Insights-erőforrás van definiálva, a típus **Microsoft.Insights/components** és az altípus **webes**:

```json
        {
            "apiVersion": "2015-05-01",
            "name": "[variables('appInsightsName')]",
            "type": "Microsoft.Insights/components",
            "kind": "web",
            "location": "[resourceGroup().location]",
            "tags": {
                "[concat('hidden-link:', resourceGroup().id, '/providers/Microsoft.Web/sites/', variables('functionAppName'))]": "Resource"
            },
            "properties": {
                "Application_Type": "web",
                "ApplicationId": "[variables('functionAppName')]"
            }
        },
```

Emellett a kialakítási kulcsot kell megadni, a függvény használatával a `APPINSIGHTS_INSTRUMENTATIONKEY` nastavení aplikace. Ez a tulajdonság megadott a `appSettings` gyűjteményt a `siteConfig` objektum:

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>Szolgáltatási csomag

A definíció, a üzemeltetési terv változik, és a következők egyike lehet:
* [Használatalapú csomag](#consumption) (alapértelmezett)
* [Prémium szintű csomag](#premium) (az előzetes verzió)
* [App Service-csomag](#app-service-plan)

### <a name="function-app"></a>Függvényalkalmazás

A függvény alkalmazás erőforrás van definiálva típusú erőforrások használatával **Microsoft.Web/sites** és altípus **functionapp**:

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Insights/components', variables('appInsightsName'))]"
    ]
```

> [!IMPORTANT]
> Ha explicit módon egy szolgáltatási csomagot, egy további elem lenne szükséges a dependsOn tömb: `"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

Ezeket az alkalmazásbeállításokat a függvényalkalmazáshoz tartalmaznia kell:

| Beállítás neve                 | Leírás                                                                               | Példaértékek                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | Egy kapcsolati karakterláncot egy Storage-fiók, amely a Functions runtime belső várólista | Lásd: [Storage-fiók](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | Az Azure Functions futtatókörnyezet verziója                                                | `~2`                                  |
| FUNCTIONS_WORKER_RUNTIME     | A nyelvi modult kell használni az alkalmazásban található függvények                                   | `dotnet`, `node`, `java`, vagy `python` |
| WEBSITE_NODE_DEFAULT_VERSION | Csak akkor szükséges, ha használja a `node` nyelvi modult, verziót kell használni, adja meg.              | `10.14.1`                             |

Ezek a tulajdonságok vannak megadva a `appSettings` gyűjteményt a `siteConfig` tulajdonság:

```json
"properties": {
    "siteConfig": {
        "appSettings": [
            {
                "name": "AzureWebJobsStorage",
                "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
            },
            {
                "name": "FUNCTIONS_WORKER_RUNTIME",
                "value": "node"
            },
            {
                "name": "WEBSITE_NODE_DEFAULT_VERSION",
                "value": "10.14.1"
            },
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~2"
            }
        ]
    }
}
```

<a name="consumption"></a>

## <a name="deploy-on-consumption-plan"></a>A Használatalapú csomag üzembe helyezése

A Használatalapú csomag automatikusan foglalja le a számítási teljesítményt, amikor a kód fut, elvégzi a horizontális felskálázást a terhelés kezelése érdekében szükség szerint, és ezután méretezhető, amikor a kód nem fut. Nem kell fizetnie a tétlen virtuális gépeket, és nem kell foglalhat le előre a kapacitás. További tudnivalókért lásd: [Azure Functions méretezése és üzemeltetése](functions-scale.md#consumption-plan).

Egy minta Azure Resource Manager-sablon, lásd: [A függvényalkalmazást a Használatalapú csomag].

### <a name="create-a-consumption-plan"></a>Hozzon létre egy Használatalapú csomag

A Használatalapú csomag nem kell definiálni. Az egyik automatikusan létrejön vagy kiválasztott régiók szerinti történik, a függvény alkalmazás erőforrásán létrehozásakor.

A Használatalapú csomag az "kiszolgálófarm" erőforrás egy speciális típusa. Windows, megadhatja azt a a `Dynamic` értékét a `computeMode` és `sku` tulajdonságai:

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "computeMode": "Dynamic",
        "sku": "Dynamic"
    }
}
```

> [!NOTE]
> A Használatalapú csomag Linux explicit módon nem lehet definiálni. Automatikusan létrejön.

Ha explicit módon határozhatja meg a használatalapú csomag, kell beállítani a `serverFarmId` tulajdonság az alkalmazást úgy, hogy az erőforrás-azonosító, a terv mutat. Győződjön meg arról, hogy rendelkezik-e a függvényalkalmazás egy `dependsOn` beállítás, valamint a csomag számára.

### <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

#### <a name="windows"></a>Windows

Használatalapú Windows, a hely a konfigurációban két további beállítás szükséges: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` és `WEBSITE_CONTENTSHARE`. Ezek a tulajdonságok konfigurálása a fiók- és elérési útján a függvény kódját és konfigurációs tárolására.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTSHARE",
                    "value": "[toLower(variables('functionAppName'))]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```

#### <a name="linux"></a>Linux

Linuxon, rendelkeznie kell a függvényalkalmazást a `kind` beállítása `functionapp,linux`, és tartalmaznia kell a `reserved` tulajdonság `true`:

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp,linux",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountName'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        },
        "reserved": true
    }
}
```



<a name="premium"></a>

## <a name="deploy-on-premium-plan"></a>A prémium szintű csomag üzembe helyezése

A prémium szintű csomag ugyanezt a skálázást, mint a használatalapú csomag kínál, de dedikált erőforrásokat és további képességeket is tartalmaz. További tudnivalókért lásd: [Azure Functions prémium szintű csomag (előzetes verzió)](./functions-premium-plan.md).

### <a name="create-a-premium-plan"></a>Hozzon létre egy prémium szintű csomag

Egy prémium szintű csomag az "kiszolgálófarm" erőforrás egy speciális típusa. Megadhatja a `EP1`, `EP2`, vagy `EP3` számára a `sku` tulajdonság értéke.

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "EP1"
    }
}
```

### <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

Rendelkeznie kell egy prémium szintű csomag a függvényalkalmazás a `serverFarmId` tulajdonsága az erőforrás-Azonosítóját a korábban létrehozott csomagot. Emellett egy prémium szintű csomag igényli-e a hely a konfigurációban két további beállítás: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` és `WEBSITE_CONTENTSHARE`. Ezek a tulajdonságok konfigurálása a fiók- és elérési útján a függvény kódját és konfigurációs tárolására.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",            
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "WEBSITE_CONTENTSHARE",
                    "value": "[toLower(variables('functionAppName'))]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```


<a name="app-service-plan"></a> 

## <a name="deploy-on-app-service-plan"></a>Az App Service-csomag telepítése

Az App Service-csomag esetében a függvényalkalmazás alap, Standard és prémium szintű termékváltozatok, webes alkalmazásokhoz hasonlóan a dedikált virtuális gépeken futtatja. Az App Service-csomag működésével kapcsolatos részletekért lásd: a [Azure App Service díjcsomagjainak részletes áttekintése](../app-service/overview-hosting-plans.md).

Egy minta Azure Resource Manager-sablon, lásd: [Az Azure App Service-csomag függvényalkalmazás].

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

"Kiszolgálófarm:" erőforrás App Service-csomag határozza meg.

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "[parameters('sku')]",
        "workerSize": "[parameters('workerSize')]",
        "hostingEnvironment": "",
        "numberOfWorkers": 1
    }
}
```

Az alkalmazás futtatását Linux rendszeren, meg kell adnia a `kind` való `Linux`:

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2015-04-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "kind": "Linux",
    "properties": {
        "name": "[variables('hostingPlanName')]",
        "sku": "[parameters('sku')]",
        "workerSize": "[parameters('workerSize')]",
        "hostingEnvironment": "",
        "numberOfWorkers": 1
    }
}
```

### <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása 

Rendelkeznie kell egy függvényalkalmazást az App Service-csomag a `serverFarmId` tulajdonsága az erőforrás-Azonosítóját a korábban létrehozott csomagot.

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ]
        }
    }
}
```

A Linux-alkalmazások is tartalmaznia kell egy `linuxFxVersion` tulajdonság alatt `siteConfig`. Kód üzembe csupán, ha ez az érték határozza meg a kívánt futtatókörnyezeti verem:

| Verem            | Példaérték                                         |
|------------------|-------------------------------------------------------|
| Python (előzetes verzió) | `DOCKER|microsoft/azure-functions-python3.6:2.0`      |
| JavaScript       | `DOCKER|microsoft/azure-functions-node8:2.0`          |
| .NET             | `DOCKER|microsoft/azure-functions-dotnet-core2.0:2.0` |

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                }
            ],
            "linuxFxVersion": "DOCKER|microsoft/azure-functions-node8:2.0"
        }
    }
}
```

Ha Ön [egy egyéni tároló rendszerképének üzembe helyezéséhez](./functions-create-function-linux-custom-image.md), meg kell adnia azt az `linuxFxVersion` és konfigurációt, amely lehetővé teszi, hogy a rendszerkép le kell kérnie, részeként [Web App for Containers](/azure/app-service/containers). Ezenkívül állítsa `WEBSITES_ENABLE_APP_SERVICE_STORAGE` való `false`, mivel az alkalmazás tartalmának biztosított magának a tárolóban:

```json
{
    "apiVersion": "2016-03-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ],
    "properties": {
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "siteConfig": {
            "appSettings": [
                {
                    "name": "AzureWebJobsStorage",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
                {
                    "name": "FUNCTIONS_WORKER_RUNTIME",
                    "value": "node"
                },
                {
                    "name": "WEBSITE_NODE_DEFAULT_VERSION",
                    "value": "10.14.1"
                },
                {
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~2"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_URL",
                    "value": "[parameters('dockerRegistryUrl')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_USERNAME",
                    "value": "[parameters('dockerRegistryUsername')]"
                },
                {
                    "name": "DOCKER_REGISTRY_SERVER_PASSWORD",
                    "value": "[parameters('dockerRegistryPassword')]"
                },
                {
                    "name": "WEBSITES_ENABLE_APP_SERVICE_STORAGE",
                    "value": "false"
                }
            ],
            "linuxFxVersion": "DOCKER|myacr.azurecr.io/myimage:mytag"
        }
    }
}
```

## <a name="customizing-a-deployment"></a>Központi telepítés testreszabása

Egy függvényalkalmazást, amelyet a központi telepítésben, beleértve az alkalmazásbeállítások és a Forrásvezérlő használhat számos gyermekerőforrásait rendelkezik. Is választhatja azt is távolítsa el a **sourcecontrols** gyermek-erőforrás, és a egy másik használni [üzembe helyezési lehetőség](functions-continuous-deployment.md) helyette.

> [!IMPORTANT]
> Sikeresen üzembe helyezése az Azure Resource Manager használatával, fontos tudni, hogyan vannak telepítve az erőforrásokat az Azure-ban. A következő példában a legfelső szintű konfigurációk használatával alkalmazhatók **siteConfig**. Fontos beállítani ezeket a konfigurációkat a legfelső szinten, mert azok a Functions-futtatókörnyezet és a központi telepítési motor információ közlésére. Legfelső szintű az adatok szükségesek a gyermek előtt **sourcecontrols/webes** erőforrás van alkalmazva. Bár ezek a beállítások konfigurálása a gyermekszintű a **config/appSettings** erőforrás, bizonyos esetekben kell üzembe helyezni a függvényalkalmazás *előtt* **config/appSettings**  van alkalmazva. Például ha használja a functions és a [Logic Apps](../logic-apps/index.yml), a függvények egy másik erőforrás függőség.

```json
{
  "apiVersion": "2015-08-01",
  "name": "[parameters('appName')]",
  "type": "Microsoft.Web/sites",
  "kind": "functionapp",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Web/serverfarms', parameters('appName'))]"
  ],
  "properties": {
     "serverFarmId": "[variables('appServicePlanName')]",
     "siteConfig": {
        "alwaysOn": true,
        "appSettings": [
            {
                "name": "FUNCTIONS_EXTENSION_VERSION",
                "value": "~2"
            },
            {
                "name": "Project",
                "value": "src"
            }
        ]
     }
  },
  "resources": [
     {
        "apiVersion": "2015-08-01",
        "name": "appsettings",
        "type": "config",
        "dependsOn": [
          "[resourceId('Microsoft.Web/Sites', parameters('appName'))]",
          "[resourceId('Microsoft.Web/Sites/sourcecontrols', parameters('appName'), 'web')]",
          "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
        ],
        "properties": {
          "AzureWebJobsStorage": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]",
          "FUNCTIONS_EXTENSION_VERSION": "~2",
          "FUNCTIONS_WORKER_RUNTIME": "dotnet",
          "Project": "src"
        }
     },
     {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites/', parameters('appName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('sourceCodeRepositoryURL')]",
            "branch": "[parameters('sourceCodeBranch')]",
            "IsManualIntegration": "[parameters('sourceCodeManualIntegration')]"
          }
     }
  ]
}
```
> [!TIP]
> Ez a sablon által a [projekt](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) alkalmazás beállítások érték, amely beállítja a alapkönyvtárának, amelyben a Functions üzembe helyezési motorban (Kudu) megkeresi üzembe helyezhető. A tárházban, a funkciók vannak egy almappájában a **src** mappát. Így az előző példában azt adja meg az alkalmazás beállításainak értéket `src`. Ha az funkciók a tárház gyökérkönyvtárában található, vagy ha nem telepíti a forráskezelőből, eltávolíthatja az alkalmazás beállításainak érték.

## <a name="deploy-your-template"></a>A sablon telepítése

A sablon üzembe helyezéséhez az alábbi módokon használhatja:

* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)
* [Azure Portal](../azure-resource-manager/resource-group-template-deploy-portal.md)
* [REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Deploy to Azure gombbal

Cserélje le ```<url-encoded-path-to-azuredeploy-json>``` együtt egy [URL-kódolású](https://www.bing.com/search?q=url+encode) verzióját a nyers elérési útját a `azuredeploy.json` fájl a Githubban.

Íme egy példa markdown használó:

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

Íme egy példa, HTML formátumú:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

## <a name="next-steps"></a>További lépések

További információ a fejlesztés, és az Azure Functions konfigurálása.

* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)
* [Hogyan lehet Azure-függvényalkalmazás beállításainak konfigurálása](functions-how-to-use-azure-function-app-settings.md)
* [Az első Azure-függvény létrehozása](functions-create-first-azure-function.md)

<!-- LINKS -->

[A függvényalkalmazást a Használatalapú csomag]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[Az Azure App Service-csomag függvényalkalmazás]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json
