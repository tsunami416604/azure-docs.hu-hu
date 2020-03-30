---
title: A függvényalkalmazás-erőforrások azure-beli telepítésének automatizálása
description: Megtudhatja, hogyan hozhat létre egy Azure Resource Manager-sablont, amely telepíti a függvényalkalmazást.
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.topic: conceptual
ms.date: 04/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 48d98d6fef896f9288be88824a62fa1c8179217f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276893"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Erőforrás-telepítés automatizálása a függvényalkalmazáshoz az Azure Functionsben

Egy Azure Resource Manager-sablon használatával telepítheti a függvényalkalmazást. Ez a cikk ismerteti a szükséges erőforrásokat és paramétereket ehhez. Előfordulhat, hogy további erőforrásokat kell üzembe helyeznie, attól függően, hogy a függvényalkalmazásban az [eseményindítók és a kötések.](functions-triggers-bindings.md)

A sablonok létrehozásáról további információkat az [Authoring Azure Resource Manager templates](../azure-resource-manager/templates/template-syntax.md) (Azure Resource Manager-sablonok készítése) című témakörben talál.

Mintasablonokról:
- [Függvényalkalmazás a felhasználási tervben]
- [Függvényalkalmazás az Azure App Service-csomagban]

## <a name="required-resources"></a>Szükséges erőforrások

Az Azure Functions központi telepítése általában a következő erőforrásokból áll:

| Erőforrás                                                                           | Követelmény | Szintaxis és tulajdonságok hivatkozása                                                         |   |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|---|
| Függvényalkalmazás                                                                     | Kötelező    | [Microsoft.Web/webhelyek](/azure/templates/microsoft.web/sites)                             |   |
| [Egy Azure Storage-fiók](../storage/index.yml)                                   | Kötelező    | [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |   |
| [Az Application Insights](../azure-monitor/app/app-insights-overview.md) egyik összetevője | Optional    | [Microsoft.Insights/components](/azure/templates/microsoft.insights/components)         |   |
| A [tárhely terv](./functions-scale.md)                                             | Választható<sup>1</sup>    | [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)                 |   |

<sup>1 1</sup> A tárhelycsomagcsak akkor szükséges, ha úgy dönt, hogy a függvényalkalmazást [prémium csomagon](./functions-premium-plan.md) (előzetes verzióban) vagy [Egy App Service-csomagon futtatja.](../app-service/overview-hosting-plans.md)

> [!TIP]
> Bár nem szükséges, erősen ajánlott az Application Insights konfigurálása az alkalmazáshoz.

<a name="storage"></a>
### <a name="storage-account"></a>Tárfiók

Egy függvényalkalmazáshoz azure-tárfiók szükséges. Szüksége van egy általános célú fiók, amely támogatja a blobok, táblák, várólisták és fájlok. További információ: [Azure Functions storage-fiók követelményeinek](storage-considerations.md#storage-account-requirements).

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2019-04-01",
    "location": "[resourceGroup().location]",
    "kind": "StorageV2",
    "sku": {
        "name": "[parameters('storageAccountType')]"
    }
}
```

Ezenkívül a `AzureWebJobsStorage` tulajdonságot alkalmazásbeállításként kell megadni a helykonfigurációban. Ha a függvényalkalmazás nem használja az Application Insights `AzureWebJobsDashboard` figyeléséhez, azt is meg kell adnia egy alkalmazás beállítás.

Az Azure Functions futásidejű a kapcsolati karakterlánc ot használja a `AzureWebJobsStorage` belső várólisták létrehozásához.  Ha az Application Insights nincs engedélyezve, a futásidejű a kapcsolati karakterlánc ot használja az `AzureWebJobsDashboard` Azure Table storage-ba való bejelentkezéshez, és a **figyelő** lap betöltéséhez a portálon.

Ezek a tulajdonságok az `appSettings` `siteConfig` objektum gyűjteményében vannak megadva:

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

Az Application Insights ajánlott a függvényalkalmazások figyeléséhez. Az Application Insights erőforrás a **Microsoft.Insights/components** és a kind **web**típussal van definiálva:

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
                "ApplicationId": "[variables('appInsightsName')]"
            }
        },
```

Emellett a műszerezési kulcsot az alkalmazásbeállítás használatával `APPINSIGHTS_INSTRUMENTATIONKEY` kell biztosítani a függvényalkalmazásnak. Ez a tulajdonság az `appSettings` `siteConfig` objektum gyűjteményében van megadva:

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>Tárhelyterv

A tárhelycsomag meghatározása változó, és a következők egyike lehet:
* [Felhasználási terv](#consumption) (alapértelmezett)
* [Prémium csomag](#premium) (előzetes verzióban)
* [App Szolgáltatási csomag](#app-service-plan)

### <a name="function-app"></a>Függvényalkalmazás

A függvényalkalmazás-erőforrást **egy Microsoft.Web/sites** és kind **functionapp**típusú erőforrás határozza meg:

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
}
```

> [!IMPORTANT]
> Ha kifejezetten egy tárhelytervet határoz meg, egy további elemre lenne szükség a dependsOn tömbben:`"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

A függvényalkalmazásnak tartalmaznia kell a következő alkalmazásbeállításokat:

| Beállítás neve                 | Leírás                                                                               | Példaértékek                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | Kapcsolati karakterlánc egy tárfiókhoz, amelyet a Functions futásidejű a belső várólistára használ | Lásd: [Tárfiók](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | Az Azure Functions futásidejű verziójá                                                | `~2`                                  |
| FUNCTIONS_WORKER_RUNTIME     | Az alkalmazás funkcióihoz használandó nyelvi verem                                   | `dotnet`, `node` `java`, `python`, vagy`powershell` |
| WEBSITE_NODE_DEFAULT_VERSION | Csak akkor szükséges, ha a `node` nyelvi verem, meghatározza a verziót kell használni              | `10.14.1`                             |

Ezek a tulajdonságok a `appSettings` `siteConfig` tulajdonság gyűjteményében vannak megadva:

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

## <a name="deploy-on-consumption-plan"></a>Üzembe helyezés a felhasználási tervben

A felhasználási terv automatikusan lefoglalja a számítási teljesítményt, amikor a kód fut, szükség szerint skálázódik a terhelés kezeléséhez, és majd a méretezések, ha a kód nem fut. Nem kell fizetnie az alapjárati virtuális gépekért, és nem kell előre lefoglalnia a kapacitást. További információ: [Azure Functions scale and hosting](functions-scale.md#consumption-plan).

Az Azure Resource Manager mintasablonról a [Függvényalkalmazás a felhasználási tervben.]

### <a name="create-a-consumption-plan"></a>Felhasználási terv létrehozása

A felhasználási tervet nem kell definiálni. Az egyik automatikusan létrejön, vagy kiválasztott régiónként, amikor létrehozza a függvény alkalmazás erőforrás magát.

A felhasználási terv a "serverfarm" erőforrás egy speciális típusa. A Windows rendszerben a `Dynamic` következő értékek `computeMode` `sku` használatával adhatja meg:

```json
{  
   "type":"Microsoft.Web/serverfarms",
   "apiVersion":"2016-09-01",
   "name":"[variables('hostingPlanName')]",
   "location":"[resourceGroup().location]",
   "properties":{  
      "name":"[variables('hostingPlanName')]",
      "computeMode":"Dynamic"
   },
   "sku":{  
      "name":"Y1",
      "tier":"Dynamic",
      "size":"Y1",
      "family":"Y",
      "capacity":0
   }
}
```

> [!NOTE]
> A felhasználási terv nem határozható meg kifejezetten Linuxhoz. Ez automatikusan létrejön.

Ha kifejezetten meghatározza a felhasználási tervet, be `serverFarmId` kell állítania a tulajdonságot az alkalmazásban, hogy az a terv erőforrás-azonosítójára mutatjon. Győződjön meg arról, `dependsOn` hogy a függvényalkalmazás rendelkezik a terv beállításával is.

### <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

#### <a name="windows"></a>Windows

A Windows rendszerben a felhasználási csomag hoz `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` két `WEBSITE_CONTENTSHARE`további beállítást a hely konfigurációjában: és . Ezek a tulajdonságok konfigurálják a tárfiókot és a fájl elérési útját, ahol a függvényalkalmazás-kód és konfiguráció tárolják.

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

Linux on a függvényalkalmazásnak `kind` a `functionapp,linux`beállításának a `reserved` beállítással `true`kell rendelkeznie, és a tulajdonságnak a következőre kell, hogy legyen állítva:

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

## <a name="deploy-on-premium-plan"></a>Üzembe helyezés a Prémium csomagra

A prémium csomag ugyanazt a skálázást kínálja, mint a felhasználási csomag, de dedikált erőforrásokat és további képességeket tartalmaz. További információ: [Azure Functions Premium Plan](./functions-premium-plan.md).

### <a name="create-a-premium-plan"></a>Prémium csomag létrehozása

A prémium csomag a "serverfarm" erőforrás egy speciális típusa. Megadhatja `EP1`a `EP2`leírás [objektum](https://docs.microsoft.com/azure/templates/microsoft.web/2018-02-01/serverfarms#skudescription-object)tulajdonságértékét `Name` vagy `sku` `EP3` tulajdonságértékét.

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[parameters('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "properties": {
        "name": "[parameters('hostingPlanName')]",
        "workerSize": "[parameters('workerSize')]",
        "workerSizeId": "[parameters('workerSizeId')]",
        "numberOfWorkers": "[parameters('numberOfWorkers')]",
        "hostingEnvironment": "[parameters('hostingEnvironment')]",
        "maximumElasticWorkerCount": "20"
    },
    "sku": {
        "Tier": "ElasticPremium",
        "Name": "EP1"
    }
}
```

### <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

A prémium csomagon lévő függvényalkalmazásnak rendelkeznie kell a `serverFarmId` korábban létrehozott terv erőforrás-azonosítójára beállított tulajdonsággal. Ezenkívül a Prémium csomag hoz két további `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` beállítást `WEBSITE_CONTENTSHARE`a hely konfigurációjában: és . Ezek a tulajdonságok konfigurálják a tárfiókot és a fájl elérési útját, ahol a függvényalkalmazás-kód és konfiguráció tárolják.

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

## <a name="deploy-on-app-service-plan"></a>Üzembe helyezés az App Service-csomagon

Az App Service-csomagban a függvényalkalmazás az alapszintű, standard és prémium szintű termékkészletekdedikált virtuális gépeken fut, hasonlóan a webalkalmazásokhoz. Az App Service-csomag működéséről az [Azure App Service-csomagok részletes áttekintésében olvashat részletesen.](../app-service/overview-hosting-plans.md)

A minta Azure Resource Manager sablon, lásd: [Függvényalkalmazás az Azure App Service-csomag.]

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

Az App Service-csomagot egy "kiszolgálófarm" erőforrás határozza meg.

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

Az alkalmazás Linuxon való futtatásához `kind` a `Linux`következőt is be kell állítania:

```json
{
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2018-02-01",
    "name": "[variables('hostingPlanName')]",
    "location": "[resourceGroup().location]",
    "kind": "Linux",
    "sku": {
        "name": "S1",
        "tier": "Standard",
        "size": "S1",
        "family": "S",
        "capacity": 1
    }
}
```

### <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

Egy App Service-csomagon lévő `serverFarmId` függvényalkalmazásnak rendelkeznie kell a korábban létrehozott terv erőforrás-azonosítójára beállított tulajdonsággal.

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

A Linux-alkalmazásoknak `linuxFxVersion` a `siteConfig`területen is szerepelniük kell egy tulajdonsággal. Ha csak a kódot telepíti, ennek értékét a kívánt futásidejű verem határozza meg:

| Verem            | Példaérték                                         |
|------------------|-------------------------------------------------------|
| Python           | `DOCKER|microsoft/azure-functions-python3.6:2.0`      |
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

Ha [egyéni tárolólemezképet telepít,](./functions-create-function-linux-custom-image.md)meg kell `linuxFxVersion` adnia azt, és tartalmaznia kell olyan konfigurációt, amely lehetővé teszi a lemezkép lehúzását, mint a [Web App tárolókhoz](/azure/app-service/containers). Emellett a `WEBSITES_ENABLE_APP_SERVICE_STORAGE` `false`beállítás a beállításra is be van állítva, mivel az alkalmazás tartalma magában a tárolóban található:

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

Egy függvényalkalmazás számos gyermekerőforrással rendelkezik, amelyeket használhat a központi telepítésben, beleértve az alkalmazásbeállításokat és a forrásvezérlési beállításokat. Azt is választhatja, hogy távolítsa el a **sourcecontrols** gyermek erőforrás, és használja egy másik [telepítési lehetőség](functions-continuous-deployment.md) helyett.

> [!IMPORTANT]
> Az azure Resource Manager használatával az alkalmazás sikeres üzembe helyezéséhez fontos megérteni, hogyan vannak üzembe helyezve az erőforrások az Azure-ban. A következő példában a legfelső szintű konfigurációk at a **siteConfig**használatával alkalmazzák. Fontos, hogy ezeket a konfigurációkat egy legfelső szinten állítsa be, mert információkat továbbítanak a Functions futásidejű és üzembe helyezési motornak. A **gyermekforrásvezérlők/webes** erőforrás alkalmazása előtt felső szintű adatokra van szükség. Bár ezeket a beállításokat a gyermekszintű **konfigurációs/appSettings** erőforrásban lehet konfigurálni, bizonyos esetekben a függvényalkalmazást telepíteni kell a **config/appSettings** alkalmazása *előtt.* Ha például függvényeket használ [a Logic Apps](../logic-apps/index.yml)használatával, a függvények egy másik erőforrás függőségét képezik.

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
> Ez a sablon a [Project](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) alkalmazás beállítási értékét használja, amely beállítja azt az alapkönyvtárat, amelyben a Functions központi telepítési motorja (Kudu) telepítve lévő kódot keres. A mi adattár, a funkciók egy almappában az **src** mappát. Így az előző példában az alkalmazás beállításainak értékét a értékre `src`állítjuk. Ha a függvények a tárház gyökerében vannak, vagy ha nem a forrásvezérlőből telepít, eltávolíthatja ezt az alkalmazásbeállítási értéket.

## <a name="deploy-your-template"></a>A sablon telepítése

A sablon üzembe helyezésének az alábbi módjai közül választhat:

* [Powershell](../azure-resource-manager/templates/deploy-powershell.md)
* [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)
* [Azure-portál](../azure-resource-manager/templates/deploy-portal.md)
* [REST API](../azure-resource-manager/templates/deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Üzembe helyezés az Azure-ban gomb

Cserélje ```<url-encoded-path-to-azuredeploy-json>``` le a `azuredeploy.json` fájl nyers elérési útjának [URL-kódolású](https://www.bing.com/search?q=url+encode) verziójára a GitHubon.

Íme egy példa, amely a markdownt használja:

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

Íme egy példa, amely HTML-t használ:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

### <a name="deploy-using-powershell"></a>Üzembe helyezés a PowerShell használatával

A következő PowerShell-parancsok létrehoznak egy erőforráscsoportot, és olyan sablont telepítenek, amely a szükséges erőforrásokkal rendelkező függvényalkalmazást hoz létre. A helyi futtatáshoz telepítve kell lennie [az Azure PowerShellnek.](/powershell/azure/install-az-ps) Fusson [`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount) a bejelentkezéshez.

```powershell
# Register Resource Providers if they're not already registered
Register-AzResourceProvider -ProviderNamespace "microsoft.web"
Register-AzResourceProvider -ProviderNamespace "microsoft.storage"

# Create a resource group for the function app
New-AzResourceGroup -Name "MyResourceGroup" -Location 'West Europe'

# Create the parameters for the file, which for this template is the function app name.
$TemplateParams = @{"appName" = "<function-app-name>"}

# Deploy the template
New-AzResourceGroupDeployment -ResourceGroupName "MyResourceGroup" -TemplateFile template.json -TemplateParameterObject $TemplateParams -Verbose
```

A központi telepítés teszteléséhez használhatja az [ehhez hasonló sablont,](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json) amely egy függvényalkalmazást hoz létre a Windows rendszeren egy használati csomagban. Cserélje `<function-app-name>` le a függvényalkalmazás egyedi nevére.

## <a name="next-steps"></a>További lépések

További információ az Azure Functions fejlesztéséről és konfigurálásáról.

* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)
* [Az Azure függvényalkalmazás-beállítások konfigurálása](functions-how-to-use-azure-function-app-settings.md)
* [Az első Azure-függvény létrehozása](functions-create-first-azure-function.md)

<!-- LINKS -->

[Függvényalkalmazás a felhasználási tervben]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[Függvényalkalmazás az Azure App Service-csomagban]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json
