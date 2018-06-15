---
title: Erőforrások telepítése az Azure Functions függvény alkalmazások automatizálása |} Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy Azure Resource Manager-sablon, amely a függvény alkalmazást telepíti.
services: Functions
documtationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
keywords: Azure funkciók, a Funkciók, a kiszolgáló nélküli architektúra, a kódot, az azure erőforrás-kezelő infrastruktúra
ms.assetid: d20743e3-aab6-442c-a836-9bcea09bfd32
ms.server: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/25/2017
ms.author: glenga
ms.openlocfilehash: 28b2f5aba69e5c058feb7119eb31352220922998
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937061"
---
# <a name="automate-resource-deployment-for-your-function-app-in-azure-functions"></a>Az Azure Functions függvény alkalmazás erőforrás-telepítés automatizálása

Az Azure Resource Manager-sablon segítségével függvény alkalmazás telepítése. Ez a cikk ismerteti a szükséges erőforrások és a paraméterek úgy. Előfordulhat, hogy kell telepítenie további erőforrások, attól függően, a [eseményindítók és kötések](functions-triggers-bindings.md) az függvény alkalmazásban.

Sablonok létrehozásával kapcsolatos további információkért lásd: [Azure Resource Manager-sablonok készítése](../azure-resource-manager/resource-group-authoring-templates.md).

A minta-sablonok lásd:
- [függvény app a fogyasztás terv]
- [függvény alkalmazást az Azure App Service-csomag]

## <a name="required-resources"></a>Szükséges erőforrások

A függvény az alkalmazás csak ezeket az erőforrásokat:

* Egy [Azure Storage](../storage/index.yml) fiók
* Üzemeltetési terv (fogyasztás terv vagy App Service-csomag)
* Egy függvény alkalmazást 

### <a name="storage-account"></a>Tárfiók

Egy Azure storage-fiók egy függvény app szükség. Általános célú fiók, amely támogatja a BLOB, táblák, üzenetsorok és fájlok szükséges. További információkért lásd: [Azure Functions tárolási fiókra vonatkozó követelmények](functions-create-function-app-portal.md#storage-account-requirements).

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

Emellett a tulajdonság `AzureWebJobsStorage` Alkalmazásbeállítás a hely konfigurációban kell megadni. Ha a függvény alkalmazás nem használja az Application Insights figyelési, azt szintén meg kell adni `AzureWebJobsDashboard` egy alkalmazás-beállításként.

Az Azure Functions futtatókörnyezettel használja a `AzureWebJobsStorage` kapcsolati karakterlánc belső várólisták létrehozásához.  Az Application Insights nincs engedélyezve, a futtatókörnyezet használja a `AzureWebJobsDashboard` jelentkezzenek be Azure Table storage és a tápkábelek kapcsolati karakterláncot a **figyelő** lapon a portálon.

Ezek a tulajdonságok vannak megadva a `appSettings` gyűjtemény a `siteConfig` objektum:

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

### <a name="hosting-plan"></a>Üzemeltetési terv

Az üzemeltetési terv meghatározásának függ, hogy használ-e a felhasználási vagy App Service-csomag. Lásd: [a fogyasztás terven függvény alkalmazás telepítése](#consumption) és [függvény alkalmazás az App Service-csomag telepítése](#app-service-plan).

### <a name="function-app"></a>Függvényalkalmazás

A függvény app erőforrás van definiálva típusú erőforrások használatával **Microsoft.Web/Site** és fajta **functionapp**:

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

## <a name="deploy-a-function-app-on-the-consumption-plan"></a>A felhasználási terven függvény alkalmazás telepítése

Két különböző módban is futtathatja egy függvény alkalmazás: a fogyasztás terv és az App Service-csomag. A felhasználási terv automatikusan osztja ki a számítási teljesítményt, a kódja fut, kimenő terhelés kezelésére, szükség szerint arányosan, és majd arányosan csökken, amikor a kód nem fut. Igen nem kell a tétlen virtuális gépek után kell fizetnie, és nem kell előzetesen tartalékkapacitás. Üzemeltetési tervek kapcsolatos további információkért lásd: [Azure Functions használat és az App Service csomagokban](functions-scale.md).

Lásd: a minta Azure Resource Manager sablon [függvény app a fogyasztás terv].

### <a name="create-a-consumption-plan"></a>Felhasználás terv létrehozása

A felhasználási terv "kiszolgálófarm" erőforrás különleges típusú. Használatával megadja a `Dynamic` értékét a `computeMode` és `sku` tulajdonságok:

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

Emellett a fogyasztás terv kell két további beállítások a webhely konfigurációs: `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` és `WEBSITE_CONTENTSHARE`. Ezek a tulajdonságok konfigurálása a tárolási fiók és a fájl elérési útja az alkalmazáskódot függvény és a konfiguráció tárolásához.

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

## <a name="deploy-a-function-app-on-the-app-service-plan"></a>Egy függvény alkalmazás az App Service-csomag telepítése

Az App Service-csomag a függvény alkalmazás fut, a Basic, Standard és Premium termékváltozat, a webalkalmazások hasonló dedikált virtuális gépeken. Az App Service-csomag működésével kapcsolatos részletekért lásd: a [Azure App Service-csomagok részletes áttekintése](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md). 

Lásd: a minta Azure Resource Manager sablon [függvény alkalmazást az Azure App Service-csomag].

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

Miután kijelölt egy beállítást, a függvény-alkalmazás létrehozása. Az alkalmazás a tároló, amely a függvények.

A függvény az alkalmazás rendelkezik sok gyermekszintű erőforrása, amely a központi telepítésre, beleértve az alkalmazásbeállítások és az adatforrás beállításokat is használhatja. Előfordulhat, hogy meg távolítsa el a **sourcecontrols** gyermek-erőforrás és a, valamint egy másik [rendszerbe állítási beállításának](functions-continuous-deployment.md) helyette.

> [!IMPORTANT]
> Az alkalmazás sikeres közzétételéhez Azure Resource Manager használatával, fontos megérteni, hogyan vannak telepítve az erőforrások az Azure-ban. A következő példában a legfelső szintű konfigurációk használatával alkalmazhatók **siteConfig**. Fontos állítsa be ezeket a konfigurációkat a legfelső szintű, mert ezek a funkciók futási és telepítési motor információáramlás. Legfelső szintű adatokat a gyermek előtt meg kell adni **sourcecontrols vagy webes** erőforrás vonatkozik. Bár ezek a beállítások konfigurálására a gyermekszintű lehetséges **config/appSettings** erőforrás, a függvény app telepítenie kell bizonyos esetekben *előtt* **config/appSettings**  vonatkozik. Például használata esetén a funkciók [Logic Apps](../logic-apps/index.yml), a függvények egy másik erőforrás függősége.

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
> Ez a sablon által a [projekt](https://github.com/projectkudu/kudu/wiki/Customizing-deployments#using-app-settings-instead-of-a-deployment-file) app beállítások értéket, amely beállítja a alapkönyvtárának, amelyben a funkciók telepítési motor (Kudu) megkeresi telepíthető. A funkciók vannak a tárházban almappájában a **src** mappát. Igen, az előző példában azt állítsa be az alkalmazás beállítások értéket `src`. Ha a funkciók a tárház gyökérkönyvtárában, vagy ha nem telepíti a verziókövetésből, eltávolíthatja az alkalmazás beállításainak érték.

## <a name="deploy-your-template"></a>A sablon telepítése

A sablon telepítéséhez az alábbi módokon használhatja:

* [PowerShell](../azure-resource-manager/resource-group-template-deploy.md)
* [Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)
* [Azure Portal](../azure-resource-manager/resource-group-template-deploy-portal.md)
* [REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)

### <a name="deploy-to-azure-button"></a>Telepítse az Azure gomb

Cserélje le ```<url-encoded-path-to-azuredeploy-json>``` rendelkező egy [URL-kódolású](https://www.bing.com/search?q=url+encode) nyers elérési útja verzióját a `azuredeploy.json` fájlt a Githubon.

Íme egy példa a markdown használó:

```markdown
[![Deploy to Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>)
```

Íme egy példa a HTML formátumú:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/<url-encoded-path-to-azuredeploy-json>" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"></a>
```

## <a name="next-steps"></a>További lépések

Fejleszthet, és az Azure Functions konfigurálásával kapcsolatos további tudnivalókért.

* [Az Azure Functions fejlesztői segédanyagai](functions-reference.md)
* [Az Azure-függvény Alkalmazásbeállítások konfigurálása](functions-how-to-use-azure-function-app-settings.md)
* [Az első Azure-függvény létrehozása](functions-create-first-azure-function.md)

<!-- LINKS -->

[függvény app a fogyasztás terv]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dynamic/azuredeploy.json
[függvény alkalmazást az Azure App Service-csomag]: https://github.com/Azure/azure-quickstart-templates/blob/master/101-function-app-create-dedicated/azuredeploy.json
