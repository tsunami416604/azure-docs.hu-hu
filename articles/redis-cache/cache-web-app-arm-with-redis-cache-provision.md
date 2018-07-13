---
title: Webalkalmazás üzembe helyezése a Redis Cache használatával
description: Az Azure Resource Manager-sablon használatával a Redis Cache-webalkalmazás üzembe helyezéséhez.
services: app-service
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 6e99c71f-ef8e-4570-a307-e4c059e60c35
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.author: wesmc
ms.openlocfilehash: b787b3f2caaff5535557c03c45b058e1bbb0c77d
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38671401"
---
# <a name="create-a-web-app-plus-redis-cache-using-a-template"></a>Hozzon létre egy webalkalmazás és Redis Cache-sablon használatával
Ebben a témakörben megtudhatja, hogyan hozhat létre egy Azure Resource Manager-sablon üzembe helyez egy Azure-webalkalmazást a Redis cache használatával. Megtudhatja, hogyan határozza meg, mely erőforrások vannak telepítve, és a megadott paramétereket definiálása az üzembe helyezés végrehajtása esetén. Ez a sablont használhatja a saját környezeteiben, vagy testre is szabhatja a saját követelményeinek megfelelően.

Sablonok létrehozásával kapcsolatos további információkért lásd: [Azure Resource Manager-sablonok készítése](../azure-resource-manager/resource-group-authoring-templates.md).

A teljes sablonját, lásd: [webalkalmazás létrehozása Redis Cache sablon](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-with-redis-cache/azuredeploy.json).

## <a name="what-you-will-deploy"></a>Mit fog üzembe helyezni
Ez a sablon telepíti:

* Azure Web App
* Azure Redis Cache.

Az automatikus üzembe helyezéshez kattintson az alábbi gombra:

[![Üzembe helyezés az Azure-ban](./media/cache-web-app-arm-with-redis-cache-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-with-redis-cache%2Fazuredeploy.json)

## <a name="parameters-to-specify"></a>Adja meg a paramétereket
[!INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

[!INCLUDE [cache-deploy-parameters](../../includes/cache-deploy-parameters.md)]

## <a name="variables-for-names"></a>Nevek változói
Ez a sablon változók használatával hozhat létre az erőforrások nevét. Használja a [uniqueString](../azure-resource-manager/resource-group-template-functions-string.md#uniquestring) függvény létrehozására egy értéket az erőforráscsoport azonosítója alapján.

    "variables": {
      "hostingPlanName": "[concat('hostingplan', uniqueString(resourceGroup().id))]",
      "webSiteName": "[concat('webSite', uniqueString(resourceGroup().id))]",
      "cacheName": "[concat('cache', uniqueString(resourceGroup().id))]"
    },


## <a name="resources-to-deploy"></a>Üzembe helyezendő erőforrások
[!INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### <a name="redis-cache"></a>Redis Cache
Az Azure Redis Cache a webalkalmazáshoz használt hoz létre. A gyorsítótár neve szerepel a **cacheName** változó.

A sablon létrehozza a gyorsítótár megegyezik az erőforráscsoport ugyanazon a helyen.

    {
      "name": "[variables('cacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[resourceGroup().location]",
      "apiVersion": "2015-08-01",
      "dependsOn": [ ],
      "tags": {
        "displayName": "cache"
      },
      "properties": {
        "sku": {
          "name": "[parameters('cacheSKUName')]",
          "family": "[parameters('cacheSKUFamily')]",
          "capacity": "[parameters('cacheSKUCapacity')]"
        }
      }
    }


### <a name="web-app"></a>Webalkalmazás
Létrehozza a webalkalmazást a megadott név a **Webhelynév helyére írja be** változó.

Figyelje meg, hogy a web app alkalmazás beállítás tulajdonságokkal, amelyek lehetővé teszik, hogy a Redis Cache használata van konfigurálva. Beállítások dinamikusan jön létre az alkalmazás üzembe helyezése során megadott értékek alapján.

    {
      "apiVersion": "2015-08-01",
      "name": "[variables('webSiteName')]",
      "type": "Microsoft.Web/sites",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.Web/serverFarms/', variables('hostingPlanName'))]",
        "[concat('Microsoft.Cache/Redis/', variables('cacheName'))]"
      ],
      "tags": {
        "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', variables('hostingPlanName'))]": "empty",
        "displayName": "Website"
      },
      "properties": {
        "name": "[variables('webSiteName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
      },
      "resources": [
        {
          "apiVersion": "2015-08-01",
          "type": "config",
          "name": "appsettings",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', variables('webSiteName'))]",
            "[concat('Microsoft.Cache/Redis/', variables('cacheName'))]"
          ],
          "properties": {
            "CacheConnection": "[concat(variables('cacheName'),'.redis.cache.windows.net,abortConnect=false,ssl=true,password=', listKeys(resourceId('Microsoft.Cache/Redis', variables('cacheName')), '2015-08-01').primaryKey)]"
          }
        }
      ]
    }

## <a name="commands-to-run-deployment"></a>Az üzembe helyezést futtató parancsok
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell
    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-with-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup

### <a name="azure-cli"></a>Azure CLI
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-with-redis-cache/azuredeploy.json -g ExampleDeployGroup
