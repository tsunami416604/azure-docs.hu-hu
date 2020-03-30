---
title: Az Azure Cache for Redis üzembe helyezése az Azure Resource Manager rel
description: Ismerje meg, hogyan azure Resource Manager-sablon használatával telepítheti az Azure Cache for Redis erőforrást. A sablonok gyakori forgatókönyvekhez vannak megadva.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 01/23/2017
ms.openlocfilehash: 787edf662aa3a34e167db61b0a89dfc5c2944219
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75412402"
---
# <a name="create-an-azure-cache-for-redis-using-a-template"></a>Azure-gyorsítótár létrehozása a Redis számára sablon használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ebben a témakörben megtudhatja, hogyan hozhat létre egy Azure Resource Manager-sablont, amely telepíti az Azure-gyorsítótárat a Redis számára. A gyorsítótár egy meglévő tárfiókkal használható a diagnosztikai adatok megőrzéséhez. Azt is megtudhatja, hogyan határozhatja meg, hogy mely erőforrások vannak telepítve, és hogyan határozhatja meg a központi telepítés végrehajtásakor megadott paramétereket. Ez a sablont használhatja a saját környezeteiben, vagy testre is szabhatja a saját követelményeinek megfelelően.

Jelenleg a diagnosztikai beállítások meg vannak osztva az összes gyorsítótárak ugyanabban a régióban egy előfizetés. Egy gyorsítótár frissítése a régióban hatással van a régió összes többi gyorsítótárára.

A sablonok létrehozásáról további információt az [Azure Resource Manager-sablonok készítése című témakörben talál.](../azure-resource-manager/templates/template-syntax.md) A JSON szintaxisáról és a gyorsítótár-erőforrástípusok tulajdonságairól a [Microsoft.Cache erőforrástípusok](/azure/templates/microsoft.cache/allversions)című témakörben olvashat.

A teljes sablonról az [Azure Cache for Redis sablonban](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json)olvashat.

> [!NOTE]
> Az új [prémium szint](cache-premium-tier-intro.md) erőforrás-kezelősablonjai elérhetők. 
> 
> * [Prémium szintű Azure-gyorsítótár létrehozása a Redis számára fürtözéssel](https://azure.microsoft.com/resources/templates/201-redis-premium-cluster-diagnostics/)
> * [Prémium szintű Azure-gyorsítótár létrehozása a Redis számára adatmegőrzéssel](https://azure.microsoft.com/resources/templates/201-redis-premium-persistence/)
> * [Virtuális hálózatba telepített prémium szintű redis-gyorsítótár létrehozása](https://azure.microsoft.com/resources/templates/201-redis-premium-vnet/)
> 
> A legújabb sablonok kereséséhez olvassa el az Azure `Azure Cache for Redis` [gyorsindítási sablonjait,](https://azure.microsoft.com/documentation/templates/) és keressen rá.
> 
> 

## <a name="what-you-will-deploy"></a>Mit fog telepíteni
Ebben a sablonban egy Azure-gyorsítótárat telepít a Redisszámára, amely egy meglévő tárfiókot használ a diagnosztikai adatokhoz.

Az automatikus üzembe helyezéshez kattintson az alábbi gombra:

[![Üzembe helyezés az Azure-ban](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="parameters"></a>Paraméterek
Az Azure Resource Managerrel meghatározhatja a sablon üzembe helyezésekor megadandó értékek paramétereit. A sablon tartalmaz egy Parameters nevű szakaszt, amely az összes paraméterértéket tartalmazza.
Azokhoz az értékekhez adjon meg paramétert, amelyek az üzembe helyezendő projekt vagy az üzembe helyezési környezet alapján változhatnak. Ne adjon meg olyan paramétereket olyan értékhez, amelyek nem változnak. A sablonban minden egyes paraméterérték az üzembe helyezendő erőforrások megadásához lesz felhasználva. 

[!INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### <a name="rediscachelocation"></a>redisCacheLocation
Az Azure Cache a Redis helye. A legjobb teljesítmény érdekében használja ugyanazt a helyet, mint a gyorsítótárral használandó alkalmazás.

    "redisCacheLocation": {
      "type": "string"
    }

### <a name="existingdiagnosticsstorageaccountname"></a>existingDiagnosticsStorageAccountName
A diagnosztikai célokra használandó meglévő tárfiók neve. 

    "existingDiagnosticsStorageAccountName": {
      "type": "string"
    }

### <a name="enablenonsslport"></a>enableNonSslPort
Logikai érték, amely azt jelzi, hogy engedélyezi-e a hozzáférést a nem SSL-portokon keresztül.

    "enableNonSslPort": {
      "type": "bool"
    }

### <a name="diagnosticsstatus"></a>diagnosticsStatus (diagnosztikaÁllapot
Olyan érték, amely azt jelzi, hogy engedélyezve van-e a diagnosztika. Használja a BE vagy a OFF-ot.

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
Létrehozza az Azure cache redis.

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

    New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache

### <a name="azure-cli"></a>Azure CLI
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup
