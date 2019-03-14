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
ms.date: 05/25/2017
ms.author: glenga
ms.openlocfilehash: 9fc55e2b3ebb1e932a991e0da2c78a980abbc953
ms.sourcegitcommit: d89b679d20ad45d224fd7d010496c52345f10c96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57792497"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>A függvényalkalmazás az Azure Functions erőforrások üzembe helyezésének automatizálása

Függvényalkalmazás üzembe helyezése Azure Resource Manager-sablon segítségével. Ez a cikk ismerteti a szükséges erőforrásokat és a paraméterek indoklását. Szükség lehet további erőforrásokat telepíteni attól függően, a [eseményindítók és kötések](functions-triggers-bindings.md) a függvényalkalmazásban.

Sablonok létrehozásával kapcsolatos további információkért lásd: [Azure Resource Manager-sablonok készítése](../azure-resource-manager/resource-group-authoring-templates.md).

Mintasablonok lásd:
- [A függvényalkalmazást a Használatalapú csomag]
- [Az Azure App Service-csomag függvényalkalmazás]

## <a name="required-resources"></a>Szükséges erőforrások

Függvényalkalmazás ezeket az erőforrásokat igényel:

* Egy [Azure Storage](../storage/index.yml) fiók
* Egy szolgáltatási csomagot (használatalapú és App Service-csomag)
* Függvényalkalmazás 

JSON-szintaxist, és ezek az erőforrások tulajdonságait lásd:

* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)
* [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)

### <a name="storage-account"></a>Tárfiók

Azure-tárfiókra szükség egy függvényalkalmazást. Egy általános célú fiók, amely támogatja a blobok, táblák, üzenetsorok és fájlok szükséges. További információkért lásd: [Azure Functions storage-fiókra vonatkozó követelmények](functions-create-function-app-portal.md#storage-account-requirements).

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
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
```    

### <a name="hosting-plan"></a>Szolgáltatási csomag

A definíció, a üzemeltetési terv változik, ha egy Használatalapú és App Service-csomag használja. Lásd: [üzembe helyezése egy függvényalkalmazást a Használatalapú díjcsomag](#consumption) és [függvényalkalmazás üzembe helyezése az App Service-csomag](#app-service-plan).

### <a name="function-app"></a>Függvényalkalmazás

A függvény alkalmazás erőforrás van definiálva típusú erőforrások használatával **Microsoft.Web/Site** és altípus **functionapp**:

```json
{
    "apiVersion": "2015-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('functionAppName')]",
    "location": "[resourceGroup().location]",
    "kind": "functionapp",            
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
    ]
```

<a name="consumption"></a>

## <a name="deploy-a-function-app-on-the-consumption-plan"></a>A Használatalapú csomag a függvényalkalmazás üzembe helyezése

Két különböző módban is futtathatja egy függvény alkalmazást: a Használatalapú és App Service-csomag. A Használatalapú csomag automatikusan foglalja le a számítási teljesítményt, amikor a kód fut, elvégzi a horizontális felskálázást a terhelés kezelése érdekében szükség szerint, és ezután méretezhető, amikor a kód nem fut. Így nem kell fizetnie a tétlen virtuális gépeket, és nem kell foglalhat le előre a kapacitás. Futtatási tervek kapcsolatos további információkért lásd: [Azure Functions Használatalapú és App Service-csomagok](functions-scale.md).

Egy minta Azure Resource Manager-sablon, lásd: [A függvényalkalmazást a Használatalapú csomag].

### <a name="create-a-consumption-plan"></a>Hozzon létre egy Használatalapú csomag

Használatalapú egy olyan speciális típusú "kiszolgálófarm:" erőforrás. Használatával megadja azt a `Dynamic` értékét a `computeMode` és `sku` tulajdonságai:

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

### <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása

Emellett a használatalapú a hely a konfigurációban két további beállítás szükséges: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` és `WEBSITE_CONTENTSHARE`. Ezek a tulajdonságok konfigurálása a fiók- és elérési útján a függvény kódját és konfigurációs tárolására.

```json
{
    "apiVersion": "2015-08-01",
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
                    "name": "AzureWebJobsDashboard",
                    "value": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
                },
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
                    "name": "FUNCTIONS_EXTENSION_VERSION",
                    "value": "~1"
                }
            ]
        }
    }
}
```                    

<a name="app-service-plan"></a> 

## <a name="deploy-a-function-app-on-the-app-service-plan"></a>Függvényalkalmazás üzembe helyezése az App Service-csomag

Az App Service-csomag esetében a függvényalkalmazás alap, Standard és prémium szintű termékváltozatok, webes alkalmazásokhoz hasonlóan a dedikált virtuális gépeken futtatja. Az App Service-csomag működésével kapcsolatos részletekért lásd: a [Azure App Service díjcsomagjainak részletes áttekintése](../app-service/overview-hosting-plans.md). 

Egy minta Azure Resource Manager-sablon, lásd: [Az Azure App Service-csomag függvényalkalmazás].

### <a name="create-an-app-service-plan"></a>App Service-csomag létrehozása

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

### <a name="create-a-function-app"></a>Függvényalkalmazás létrehozása 

Miután egy méretezési lehetőséget választotta, hozzon létre egy függvényalkalmazást. Az alkalmazás nem az összes függvényt tartalmazó tároló.

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
            { "name": "FUNCTIONS_EXTENSION_VERSION", "value": "~1" },
            { "name": "Project", "value": "src" }
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
          "AzureWebJobsDashboard": "[concat('DefaultEndpointsProtocol=https;AccountName=', variables('storageAccountName'), ';AccountKey=', listKeys(variables('storageAccountid'),'2015-05-01-preview').key1)]"
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
