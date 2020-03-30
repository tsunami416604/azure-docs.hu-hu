---
title: Webalkalmazás kiépítése a Redis Azure-gyorsítótárral
description: Az Azure Resource Manager-sablonnal webalkalmazást telepíthet az Azure Cache for Redis alkalmazással.
services: app-service
author: yegu-ms
ms.service: app-service
ms.topic: conceptual
ms.date: 01/06/2017
ms.author: yegu
ms.openlocfilehash: 11c854491ab030394eb61964979cb04a5a4b489b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75433378"
---
# <a name="create-a-web-app-plus-azure-cache-for-redis-using-a-template"></a>Webapp és Azure-gyorsítótár létrehozása a Redis számára sablon használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ebben a témakörben megtudhatja, hogyan hozhat létre egy Azure Resource Manager-sablont, amely telepíti az Azure Web App az Azure Cache for Redis. Megtudhatja, hogyan határozhatja meg, hogy mely erőforrások vannak telepítve, és hogyan határozhatja meg a központi telepítés végrehajtásakor megadott paramétereket. Ez a sablont használhatja a saját környezeteiben, vagy testre is szabhatja a saját követelményeinek megfelelően.

A sablonok létrehozásáról további információt az [Azure Resource Manager-sablonok készítése című témakörben talál.](../azure-resource-manager/templates/template-syntax.md) A JSON szintaxisáról és a gyorsítótár-erőforrástípusok tulajdonságairól a [Microsoft.Cache erőforrástípusok](/azure/templates/microsoft.cache/allversions)című témakörben olvashat.

A teljes sablonról a [Web App az Azure Cache for Redis sablonban olvashat.](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-with-redis-cache/azuredeploy.json)

## <a name="what-you-will-deploy"></a>Mit fog telepíteni
Ebben a sablonban a következőket telepíti:

* Azure-webalkalmazás
* Azure Cache for Redis

Az automatikus üzembe helyezéshez kattintson az alábbi gombra:

[![Üzembe helyezés az Azure-ban](./media/cache-web-app-arm-with-redis-cache-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-with-redis-cache%2Fazuredeploy.json)

## <a name="parameters-to-specify"></a>Megadandó paraméterek
[!INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

[!INCLUDE [cache-deploy-parameters](../../includes/cache-deploy-parameters.md)]

## <a name="variables-for-names"></a>A nevek változói
Ez a sablon változókat használ az erőforrások nevének létrehozásához. Az [egyedi Karakterlánc](../azure-resource-manager/templates/template-functions-string.md#uniquestring) függvényt használja az erőforráscsoport-azonosítón alapuló érték létrehozásához.

    "variables": {
      "hostingPlanName": "[concat('hostingplan', uniqueString(resourceGroup().id))]",
      "webSiteName": "[concat('webSite', uniqueString(resourceGroup().id))]",
      "cacheName": "[concat('cache', uniqueString(resourceGroup().id))]"
    },


## <a name="resources-to-deploy"></a>Üzembe helyezendő erőforrások
[!INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Létrehozza az Azure Cache for Redis, amely a webalkalmazással használt. A gyorsítótár neve meg van adva a **cacheName** változóban.

A sablon ugyanazon a helyen hozza létre a gyorsítótárat, mint az erőforráscsoport.

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
A **websitename** változóban megadott névvel rendelkező webalkalmazást hozza létre.

Figyelje meg, hogy a webalkalmazás olyan alkalmazásbeállítási tulajdonságokkal van konfigurálva, amelyek lehetővé teszik, hogy működjön együtt az Azure Cache for Redis használatával. Ezek az alkalmazásbeállítások dinamikusan jönnek létre a központi telepítés során megadott értékek alapján.

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
    New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-with-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup

### <a name="azure-cli"></a>Azure CLI
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-with-redis-cache/azuredeploy.json -g ExampleDeployGroup
