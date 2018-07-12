---
title: Redis Cache gyorsítótár üzembe helyezése az Azure Resource Manager |} A Microsoft Docs
description: Az Azure Resource Manager-sablon használatával helyezhet üzembe egy Azure Redis Cache.
services: app-service
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: ce6f5372-7038-4655-b1c5-108f7c148282
ms.service: cache
ms.workload: web
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: wesmc
ms.openlocfilehash: b26116b974abbfe410b0a6ebc0186d73f4eea1bf
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38452583"
---
# <a name="create-a-redis-cache-using-a-template"></a>Redis Cache létrehozása sablon használatával
Ebben a témakörben elsajátíthatja, hogyan hozhat létre egy Azure Resource Manager-sablon, amely üzembe helyezi az Azure Redis Cache. A gyorsítótár egy meglévő tárfiókot a diagnosztikai adatok is használható. Emellett megismerjük, hogyan határozza meg, mely erőforrások vannak telepítve, és a megadott paramétereket definiálása az üzembe helyezés végrehajtása esetén. Ez a sablont használhatja a saját környezeteiben, vagy testre is szabhatja a saját követelményeinek megfelelően.

Diagnosztikai beállítások jelenleg az összes gyorsítótárakhoz ugyanabban a régióban egy előfizetéshez vannak megosztva. Más régióban található összes gyorsítótárak frissítése a régióban egy gyorsítótár van hatással.

Sablonok létrehozásával kapcsolatos további információkért lásd: [Azure Resource Manager-sablonok készítése](../azure-resource-manager/resource-group-authoring-templates.md).

A teljes sablonját, lásd: [Redis Cache sablon](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json).

> [!NOTE]
> Az új Resource Manager-sablonok [prémium szintű](cache-premium-tier-intro.md) érhetők el. 
> 
> * [Prémium Redis gyorsítótár létrehozása a fürtözési](https://azure.microsoft.com/documentation/templates/201-redis-premium-cluster-diagnostics/)
> * [Prémium Redis gyorsítótár létrehozása a adatmegőrzési funkciójával](https://azure.microsoft.com/documentation/templates/201-redis-premium-persistence/)
> * [Prémium Redis gyorsítótár létrehozása a virtuális hálózat és a választható fürtszolgáltatás](https://azure.microsoft.com/documentation/templates/201-redis-premium-vnet-cluster-diagnostics/)
> 
> A legújabb sablonokért megtekintéséhez [Azure gyorsindítási sablonok](https://azure.microsoft.com/documentation/templates/) és keressen rá a `Redis Cache`.
> 
> 

## <a name="what-you-will-deploy"></a>Mit fog üzembe helyezni
Ez a sablon telepíti az Azure Redis Cache, amely egy meglévő tárfiókot a diagnosztikai adatok.

Az automatikus üzembe helyezéshez kattintson az alábbi gombra:

[![Üzembe helyezés az Azure-ban](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="parameters"></a>Paraméterek
Az Azure Resource Managerrel meghatározhatja a sablon üzembe helyezésekor megadandó értékek paramétereit. A sablon tartalmaz, amely tartalmazza az összes paraméter értékét nevű paraméterek szakaszban.
Azokhoz az értékekhez adjon meg paramétert, amelyek az üzembe helyezendő projekt vagy az üzembe helyezési környezet alapján változhatnak. Ne adjon meg olyan paramétereket olyan értékhez, amelyek nem változnak. A sablonban minden egyes paraméterérték az üzembe helyezendő erőforrások megadásához lesz felhasználva. 

[!INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### <a name="rediscachelocation"></a>redisCacheLocation
A Redis gyorsítótár helyét. A legjobb teljesítmény érdekében ugyanazt a helyet használja, az alkalmazás a gyorsítótár használható.

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
### <a name="redis-cache"></a>Redis Cache
Az Azure Redis Cache hoz létre.

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
          "apiVersion": "2015-07-01",
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


