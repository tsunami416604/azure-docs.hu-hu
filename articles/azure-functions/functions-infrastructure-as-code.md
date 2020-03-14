---
title: Function app-erőforrás üzembe helyezésének automatizálása az Azure-ban
description: Megtudhatja, hogyan hozhat létre olyan Azure Resource Manager sablont, amely üzembe helyezi a Function alkalmazást.
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.topic: conceptual
ms.date: 04/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 48d98d6fef896f9288be88824a62fa1c8179217f
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79276893"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>A Function alkalmazás erőforrás-telepítésének automatizálása Azure Functions

Egy Azure Resource Manager sablon használatával üzembe helyezhet egy Function alkalmazást. Ez a cikk a szükséges erőforrásokat és paramétereket ismerteti. Előfordulhat, hogy további erőforrásokat kell telepítenie a Function alkalmazásban lévő [Eseményindítók és kötések](functions-triggers-bindings.md) függvényében.

További információ a sablonok létrehozásáról: [Azure Resource Manager-sablonok](../azure-resource-manager/templates/template-syntax.md)készítése.

A példákat lásd:
- [Function alkalmazás a használati tervben]
- [Function alkalmazás Azure App Service csomagon]

## <a name="required-resources"></a>Szükséges erőforrások

Az Azure Functions üzemelő példányok általában az alábbi erőforrásokból állnak:

| Erőforrás                                                                           | Követelmény | Szintaxis és tulajdonságok – hivatkozás                                                         |   |
|------------------------------------------------------------------------------------|-------------|-----------------------------------------------------------------------------------------|---|
| Function-alkalmazás                                                                     | Kötelező    | [Microsoft. Web/Sites](/azure/templates/microsoft.web/sites)                             |   |
| [Azure Storage](../storage/index.yml) -fiók                                   | Kötelező    | [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |   |
| [Application Insights](../azure-monitor/app/app-insights-overview.md) összetevő | Optional    | [Microsoft. bepillantások/összetevők](/azure/templates/microsoft.insights/components)         |   |
| [Üzemeltetési csomag](./functions-scale.md)                                             | Opcionális<sup>1</sup>    | [Microsoft. Web/kiszolgálófarmok](/azure/templates/microsoft.web/serverfarms)                 |   |

<sup>1</sup> A szolgáltatási csomagra csak akkor van szükség, ha úgy dönt, hogy a Function alkalmazást egy [prémium szintű csomagra](./functions-premium-plan.md) (előzetes verzióban) vagy egy [app Service csomagra](../app-service/overview-hosting-plans.md)szeretné futtatni.

> [!TIP]
> Habár nem kötelező, erősen ajánlott az alkalmazáshoz Application Insights konfigurálni.

<a name="storage"></a>
### <a name="storage-account"></a>Tárfiók

Egy Function alkalmazáshoz Azure Storage-fiók szükséges. Olyan általános célú fiókra van szüksége, amely támogatja a blobokat, a táblákat, a várólistákat és a fájlokat. További információ: [Azure functions Storage-fiókra vonatkozó követelmények](storage-considerations.md#storage-account-requirements).

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

Emellett az `AzureWebJobsStorage` tulajdonságot is meg kell adni az alkalmazás beállításaként a hely konfigurációjában. Ha a Function alkalmazás nem használja Application Insights a figyeléshez, akkor azt is meg kell adnia, hogy az alkalmazás beállítása `AzureWebJobsDashboard` legyen.

A Azure Functions futtatókörnyezet a `AzureWebJobsStorage` kapcsolódási karakterláncot használja belső várólisták létrehozásához.  Ha Application Insights nincs engedélyezve, a futtatókörnyezet a `AzureWebJobsDashboard` kapcsolódási karakterláncot használja az Azure Table Storage-ba való bejelentkezéshez és a portál **figyelés** lapjának bekapcsolásához.

Ezek a tulajdonságok a `siteConfig` objektum `appSettings` gyűjteményében vannak megadva:

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

A Application Insights a Function apps figyeléséhez ajánlott. A Application Insights erőforrás a **Microsoft. ininsights/Components** és a Kind **web**típussal van definiálva:

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

Emellett a kialakítási kulcsot is meg kell adni a Function alkalmazásnak a `APPINSIGHTS_INSTRUMENTATIONKEY` Alkalmazásbeállítások használatával. Ez a tulajdonság a `siteConfig` objektum `appSettings` gyűjteményében van megadva:

```json
"appSettings": [
    {
        "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
        "value": "[reference(resourceId('microsoft.insights/components/', variables('appInsightsName')), '2015-05-01').InstrumentationKey]"
    }
]
```

### <a name="hosting-plan"></a>Üzemeltetési csomag

A üzemeltetési csomag definíciója változó, és a következők egyike lehet:
* [Felhasználási terv](#consumption) (alapértelmezett)
* [Prémium csomag](#premium) (előzetes verzió)
* [App Service-csomag](#app-service-plan)

### <a name="function-app"></a>Függvényalkalmazás

A Function app erőforrás a **Microsoft. Web/Sites** és Kind **functionapp**típusú erőforrás használatával van definiálva:

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
> Ha explicit módon definiál egy üzemeltetési csomagot, egy további elemre lesz szükség a dependsOn tömbben: `"[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"`

A Function alkalmazásnak tartalmaznia kell ezeket az Alkalmazásbeállítások:

| Beállítás neve                 | Leírás                                                                               | Példaértékek                        |
|------------------------------|-------------------------------------------------------------------------------------------|---------------------------------------|
| AzureWebJobsStorage          | Egy olyan Storage-fiókhoz tartozó kapcsolódási karakterlánc, amelyet a functions futtatókörnyezet a belső üzenetsor-kezelést használ | Lásd: [Storage-fiók](#storage)       |
| FUNCTIONS_EXTENSION_VERSION  | Az Azure Functions futtatókörnyezet verziója                                                | `~2`                                  |
| FUNCTIONS_WORKER_RUNTIME     | Az alkalmazásban a függvényekhez használandó nyelvi verem                                   | `dotnet`, `node`, `java`, `python`vagy `powershell` |
| WEBSITE_NODE_DEFAULT_VERSION | Csak akkor szükséges, ha a `node` nyelvi verem használatával adja meg a használni kívánt verziót              | `10.14.1`                             |

Ezek a tulajdonságok a `siteConfig` tulajdonság `appSettings` gyűjteményében vannak megadva:

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

## <a name="deploy-on-consumption-plan"></a>Üzembe helyezés a használati tervben

A használati terv automatikusan lefoglalja a számítási teljesítményt, amikor a kód fut, szükség szerint méretezi a terhelést, majd méretezi a értéket, ha a kód nem fut. A tétlen virtuális gépekért nem kell fizetnie, és nem kell előre lefoglalnia a kapacitást. További információ: [Azure functions skálázás és üzemeltetés](functions-scale.md#consumption-plan).

Minta Azure Resource Manager sablon esetében lásd: [Function alkalmazás a használati tervben].

### <a name="create-a-consumption-plan"></a>Felhasználási terv létrehozása

Nem szükséges a használati terv meghatározása. A Function app-erőforrás létrehozásakor a rendszer automatikusan létrehozza vagy kiválasztja az egyiket a régió alapján.

A felhasználási terv egy speciális "kiszolgálófarm" típusú erőforrás. A Windows esetében a `computeMode` és `sku` tulajdonságok `Dynamic` értékének használatával adhatja meg:

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
> A használati tervet nem lehet explicit módon definiálni a Linux rendszerhez. A rendszer automatikusan létrehozza.

Ha explicit módon meghatározza a használati tervet, be kell állítania a `serverFarmId` tulajdonságot az alkalmazásban, hogy az a csomag erőforrás-AZONOSÍTÓJÁRA mutasson. Győződjön meg arról, hogy a Function alkalmazásnak van egy `dependsOn` beállítása is a tervhez.

### <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

#### <a name="windows"></a>Windows

A Windowsban a felhasználási terv két további beállítást igényel a hely konfigurációjában: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` és `WEBSITE_CONTENTSHARE`. Ezek a tulajdonságok konfigurálhatják a Storage-fiókot és a fájl elérési útját, ahol a Function app-kód és a konfiguráció tárolva van.

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

Linux rendszeren a Function alkalmazásnak rendelkeznie kell a `kind` `functionapp,linux`re, és a `reserved` tulajdonságot `true`értékre kell állítani:

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

## <a name="deploy-on-premium-plan"></a>Üzembe helyezés prémium csomaggal

A Prémium csomag ugyanazt a skálázást kínálja, mint a használati terv, de dedikált erőforrásokat és további képességeket is tartalmaz. További információ: [Azure functions Premium csomag](./functions-premium-plan.md).

### <a name="create-a-premium-plan"></a>Prémium csomag létrehozása

A Prémium csomag egy speciális "kiszolgálófarm" típusú erőforrás. Megadhatja azt `EP1`, `EP2`vagy `EP3` használatával a `sku` [description objektum](https://docs.microsoft.com/azure/templates/microsoft.web/2018-02-01/serverfarms#skudescription-object)`Name` tulajdonságának értékeként.

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

A prémium szintű csomagban lévő Function alkalmazásnak a korábban létrehozott csomag erőforrás-AZONOSÍTÓJÁHOZ kell beállítania a `serverFarmId` tulajdonságot. A Prémium csomag emellett két további beállítást is igényel a hely konfigurációjában: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` és `WEBSITE_CONTENTSHARE`. Ezek a tulajdonságok konfigurálhatják a Storage-fiókot és a fájl elérési útját, ahol a Function app-kód és a konfiguráció tárolva van.

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

## <a name="deploy-on-app-service-plan"></a>Üzembe helyezés App Service tervben

A App Service tervben a Function app a webalkalmazásokhoz hasonló, az alapszintű, a standard és a prémium SKU-ra épülő dedikált virtuális gépeken fut. Az App Service-csomag működésével kapcsolatos részletekért tekintse [meg a Azure app Service tervek részletes áttekintését](../app-service/overview-hosting-plans.md).

Példa Azure Resource Manager sablonra: [Function alkalmazás Azure App Service csomagon].

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

Az alkalmazás Linux rendszeren való futtatásához a `kind`t is be kell állítania `Linux`:

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

Egy App Service-csomagban lévő Function alkalmazásnak rendelkeznie kell a korábban létrehozott csomag erőforrás-AZONOSÍTÓJÁHOZ beállított `serverFarmId` tulajdonsággal.

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

A Linux-alkalmazásoknak tartalmaznia kell egy `linuxFxVersion` tulajdonságot is `siteConfig`alatt. Ha csak a kód üzembe helyezését végzi, az értéket a kívánt futtatókörnyezeti verem határozza meg:

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

Ha [Egyéni tároló lemezképet telepít](./functions-create-function-linux-custom-image.md), meg kell adnia a `linuxFxVersion`, és olyan konfigurációt kell tartalmaznia, amely lehetővé teszi a lemezkép leválasztását, ahogy az [Web App for containers](/azure/app-service/containers). Azt is beállíthatja, hogy a `WEBSITES_ENABLE_APP_SERVICE_STORAGE` `false`, mert az alkalmazás tartalma a tárolóban van megadva:

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

A Function app számos alárendelt erőforrással rendelkezik, amelyek használhatók a központi telepítésben, beleértve az Alkalmazásbeállítások és a verziókövetés beállításait is. Dönthet úgy is, hogy eltávolítja a **sourcecontrols** -gyermek erőforrást, és egy másik [központi telepítési lehetőséget](functions-continuous-deployment.md) használ.

> [!IMPORTANT]
> Az alkalmazás Azure Resource Manager használatával történő sikeres üzembe helyezéséhez fontos megérteni, hogyan történik az erőforrások üzembe helyezése az Azure-ban. A következő példában a legfelső szintű konfigurációk a **siteConfig**használatával lesznek alkalmazva. Fontos, hogy legfelső szinten állítsa be ezeket a konfigurációkat, mert információt továbbítanak a functions futtatókörnyezet és a telepítési motor számára. A gyermek **sourcecontrols/webes** erőforrás alkalmazása előtt legfelső szintű információra van szükség. Habár ezeket a beállításokat a gyermek szintű **konfiguráció/appSettings** erőforrásban is konfigurálhatja, bizonyos esetekben a Function alkalmazást telepíteni kell a **config/appSettings** alkalmazása *előtt* . Ha például az [Logic apps](../logic-apps/index.yml)függvényt használja, a függvények egy másik erőforrástól függenek.

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
> Ez a sablon a [Project](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) app Settings (projekt alkalmazás beállításai) értéket használja, amely azt az alapkönyvtárat állítja be, amelyben a functions Deployment Engine (kudu) megkeresi a telepíthető kódot. A tárházban a függvények a **src** mappa almappájában találhatók. Így az előző példában az Alkalmazásbeállítások értékét `src`értékre állítjuk. Ha a függvények a tárház gyökerében találhatók, vagy ha nem a verziókövetés alapján végzik el a telepítést, akkor eltávolíthatja ezt az Alkalmazásbeállítások értékét.

## <a name="deploy-your-template"></a>A sablon telepítése

A következő módokon végezheti el a sablon üzembe helyezését:

* [PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
* [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)
* [Azure Portalra](../azure-resource-manager/templates/deploy-portal.md)
* [REST API](../azure-resource-manager/templates/deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Üzembe helyezés az Azure-ban gomb

Cserélje le a ```<url-encoded-path-to-azuredeploy-json>```t a GitHubon lévő `azuredeploy.json` fájl nyers elérési útjának [URL-kódolású](https://www.bing.com/search?q=url+encode) verziójára.

Íme egy példa, amely az Markdown-t használja:

```markdown
[![Deploy to Azure](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

Az alábbi példa HTML-t használ:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="https://azuredeploy.net/deploybutton.png"></a>
```

### <a name="deploy-using-powershell"></a>Üzembe helyezés a PowerShell használatával

A következő PowerShell-parancsok létrehoznak egy erőforráscsoportot, és telepítenek egy olyan sablont, amely létrehozza a szükséges erőforrásokkal rendelkező Function alkalmazást. A helyi futtatáshoz [Azure PowerShell](/powershell/azure/install-az-ps) telepítve kell lennie. [`Connect-AzAccount`](/powershell/module/az.accounts/connect-azaccount) futtatásával jelentkezzen be.

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

A központi telepítés teszteléséhez használhat egy olyan [sablont](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-function-app-create-dynamic/azuredeploy.json) , amely a Windowsban egy használati alkalmazást hoz létre egy felhasználási tervben. Cserélje le a `<function-app-name>`t a Function alkalmazás egyedi nevére.

## <a name="next-steps"></a>Következő lépések

További információ a Azure Functions fejlesztéséről és konfigurálásáról.

* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)
* [Az Azure Function app beállításainak konfigurálása](functions-how-to-use-azure-function-app-settings.md)
* [Az első Azure-függvény létrehozása](functions-create-first-azure-function.md)

<!-- LINKS -->

[Function alkalmazás a használati tervben]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[Function alkalmazás Azure App Service csomagon]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json
