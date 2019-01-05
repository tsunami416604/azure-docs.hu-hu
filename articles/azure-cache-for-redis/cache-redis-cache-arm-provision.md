---
title: A Redis az Azure Resource Manager üzembe helyezhet egy Azure Cache |} A Microsoft Docs
description: Az Azure Resource Manager-sablon segítségével üzembe helyezése az Azure Cache redis.
services: app-service
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: ce6f5372-7038-4655-b1c5-108f7c148282
ms.service: cache
ms.workload: web
ms.tgt_pltfrm: azure-cache-for-redis
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: wesmc
ms.openlocfilehash: c8aadcf7b37e28035e936a9f722ee832127e928d
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54038585"
---
# <a name="create-an-azure-cache-for-redis-using-a-template"></a>Az Azure Cache létrehozása sablon használatával redis
Ebben a témakörben elsajátíthatja, hogyan hozhat létre egy Azure Resource Manager-sablon üzembe helyez egy Azure Cache redis. A gyorsítótár egy meglévő tárfiókot a diagnosztikai adatok is használható. Emellett megismerjük, hogyan határozza meg, mely erőforrások vannak telepítve, és a megadott paramétereket definiálása az üzembe helyezés végrehajtása esetén. Ez a sablont használhatja a saját környezeteiben, vagy testre is szabhatja a saját követelményeinek megfelelően.

Diagnosztikai beállítások jelenleg az összes gyorsítótárakhoz ugyanabban a régióban egy előfizetéshez vannak megosztva. Más régióban található összes gyorsítótárak frissítése a régióban egy gyorsítótár van hatással.

Sablonok létrehozásával kapcsolatos további információkért lásd: [Azure Resource Manager-sablonok készítése](../azure-resource-manager/resource-group-authoring-templates.md). A JSON-szintaxist és a gyorsítótár erőforrástípusok tulajdonságaival kapcsolatos tudnivalókért lásd: [a Microsoft.Cache erőforrás-típus](/azure/templates/microsoft.cache/allversions).

A teljes sablonját, lásd: [Azure Cache Redis-sablon](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json).

> [!NOTE]
> Az új Resource Manager-sablonok [prémium szintű](cache-premium-tier-intro.md) érhetők el. 
> 
> * [Hozzon létre egy prémium szintű Azure Cache redis klaszterezéssel](https://azure.microsoft.com/documentation/templates/201-redis-premium-cluster-diagnostics/)
> * [Prémium szintű Azure Cache létrehozása redis-adatmegőrzés](https://azure.microsoft.com/documentation/templates/201-redis-premium-persistence/)
> * [A virtuális hálózat és a választható fürtszolgáltatás redis prémium szintű Azure Cache létrehozása](https://azure.microsoft.com/documentation/templates/201-redis-premium-vnet-cluster-diagnostics/)
> 
> A legújabb sablonokért megtekintéséhez [Azure gyorsindítási sablonok](https://azure.microsoft.com/documentation/templates/) és keressen rá a `Azure Cache for Redis`.
> 
> 

## <a name="what-you-will-deploy"></a>Mit fog üzembe helyezni
Ez a sablon telepíti az Azure Cache a Redis által egy meglévő tárfiókot a diagnosztikai adatok.

Az automatikus üzembe helyezéshez kattintson az alábbi gombra:

[![Üzembe helyezés az Azure-ban](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="parameters"></a>Paraméterek
Az Azure Resource Managerrel meghatározhatja a sablon üzembe helyezésekor megadandó értékek paramétereit. A sablon tartalmaz, amely tartalmazza az összes paraméter értékét nevű paraméterek szakaszban.
Azokhoz az értékekhez adjon meg paramétert, amelyek az üzembe helyezendő projekt vagy az üzembe helyezési környezet alapján változhatnak. Ne adjon meg olyan paramétereket olyan értékhez, amelyek nem változnak. A sablonban minden egyes paraméterérték az üzembe helyezendő erőforrások megadásához lesz felhasználva. 

[!INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### <a name="rediscachelocation"></a>redisCacheLocation
Az Azure Cache redis helye. A legjobb teljesítmény érdekében ugyanazt a helyet használja, az alkalmazás a gyorsítótár használható.

    "redisCacheLocation": {
      "type": "string"
    }

### <a name="existingdiagnosticsstorageaccountname"></a>existingDiagnosticsStorageAccountName
A meglévő tárfiókot a diagnosztikai használandó neve. 

    "existingDiagnosticsStorageAccountName": {
      "type": "string"
    }

### <a name="enablenonsslport"></a>enableNonSslPort
Logikai érték, amely azt jelzi, hogy engedélyezi a hozzáférést a nem SSL portok keresztül.

    "enableNonSslPort": {
      "type": "bool"
    }

### <a name="diagnosticsstatus"></a>diagnosticsStatus
Egy érték, amely azt jelzi, hogy engedélyezve van-e a diagnosztika. Használat be vagy ki BEÁLLÍTÁST.

    "diagnosticsStatus": {
      "type": "string",
      "defaultValue": "ON",
      "allowedValues": [
            "ON",
            "OFF"
        ]
    }

## <a name="resources-to-deploy"></a>Üzembe helyezendő erőforrások
### <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Az Azure Cache Redis hoz létre.

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('redisCacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[parameters('redisCacheLocation')]",
      "properties": {
        "enableNonSslPort": "[parameters('enableNonSslPort')]",
        "sku": {
          "capacity": "[parameters('redisCacheCapacity')]",
          "family": "[parameters('redisCacheFamily')]",
          "name": "[parameters('redisCacheSKU')]"
        }
      },
      "resources": [
        {
          "apiVersion": "2017-05-01-preview",
          "type": "Microsoft.Cache/redis/providers/diagnosticsettings",
          "name": "[concat(parameters('redisCacheName'), '/Microsoft.Insights/service')]",
          "location": "[parameters('redisCacheLocation')]",
          "dependsOn": [
            "[concat('Microsoft.Cache/Redis/', parameters('redisCacheName'))]"
          ],
          "properties": {
            "status": "[parameters('diagnosticsStatus')]",
            "storageAccountName": "[parameters('existingDiagnosticsStorageAccountName')]"
          }
        }
      ]
    }



## <a name="commands-to-run-deployment"></a>Az üzembe helyezést futtató parancsok
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell
    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache

### <a name="azure-cli"></a>Azure CLI
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup


