---
title: "Egy Redis gyorsítótárhoz Azure Resource Manager használatával kiépítése |} Microsoft Docs"
description: "Az Azure Redis Cache telepítéséhez használható Azure Resource Manager-sablon."
services: app-service
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: ce6f5372-7038-4655-b1c5-108f7c148282
ms.service: cache
ms.workload: web
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: sdanie
ms.openlocfilehash: cce5d63e8bad2dd066cb4c28e2a8a9cb16c47953
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-redis-cache-using-a-template"></a>Redis Cache létrehozása sablon használatával
Ebben a témakörben elsajátíthatja, hogyan hozzon létre egy Azure Resource Manager-sablon, amely egy Azure Redis Cache telepít. A gyorsítótár segítségével a meglévő tárfiók diagnosztikai adatok megőrzéséhez. Azt is megtudhatja, hogyan határozza meg, mely erőforrásokat központilag telepíti, és hogyan adhat meg a paramétereket, amelyek megadott, amikor a központi telepítés végrehajtása. Ez a sablont használhatja a saját környezeteiben, vagy testre is szabhatja a saját követelményeinek megfelelően.

Jelenleg a diagnosztikai beállítások megosztott ugyanabban a régióban-előfizetéshez tartozó összes gyorsítótárak esetében. Minden más régióban gyorsítótárak régióban egy gyorsítótár frissítése hatással van.

Sablonok létrehozásával kapcsolatos további információkért lásd: [Azure Resource Manager sablonok készítése](../azure-resource-manager/resource-group-authoring-templates.md).

Tekintse meg a teljes sablon [Redis Cache sablon](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json).

> [!NOTE]
> Az új Resource Manager-sablonok [prémium csomagban](cache-premium-tier-intro.md) érhetők el. 
> 
> * [Prémium szintű Redis gyorsítótárat létrehozni fürtözési](https://azure.microsoft.com/documentation/templates/201-redis-premium-cluster-diagnostics/)
> * [Hozzon létre Redis Cache prémium adatmegőrzés](https://azure.microsoft.com/documentation/templates/201-redis-premium-persistence/)
> * [A virtuális hálózat és a választható csoportosítási Redis Cache prémium létrehozása](https://azure.microsoft.com/documentation/templates/201-redis-premium-vnet-cluster-diagnostics/)
> 
> A legutóbbi sablonok ellenőrzéséhez tekintse meg a [Azure gyors üzembe helyezési sablonokat](https://azure.microsoft.com/documentation/templates/) keresse meg a `Redis Cache`.
> 
> 

## <a name="what-you-will-deploy"></a>Mit fog üzembe helyezni
Ez a sablon telepíteni fogja az Azure Redis Cache, amely diagnosztikai adatok meglévő tárfiókot használ.

Az automatikus üzembe helyezéshez kattintson az alábbi gombra:

[![Üzembe helyezés az Azure-ban](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="parameters"></a>Paraméterek
Az Azure Resource Managerrel meghatározhatja a sablon üzembe helyezésekor megadandó értékek paramétereit. A sablon nevű paraméterek szakaszban, amely tartalmazza az összes paraméter értékét tartalmazza.
Azokhoz az értékekhez adjon meg paramétert, amelyek az üzembe helyezendő projekt vagy az üzembe helyezési környezet alapján változhatnak. Ne adjon meg olyan paramétereket olyan értékhez, amelyek nem változnak. A sablonban minden egyes paraméterérték az üzembe helyezendő erőforrások megadásához lesz felhasználva. 

[!INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### <a name="rediscachelocation"></a>redisCacheLocation
A Redis gyorsítótár helyét. A legjobb teljesítmény érdekében ugyanazt a helyet használja a alkalmazásként a gyorsítótár használható.

    "redisCacheLocation": {
      "type": "string"
    }

### <a name="existingdiagnosticsstorageaccountname"></a>existingDiagnosticsStorageAccountName
A meglévő használt diagnosztikai tárfiók neve. 

    "existingDiagnosticsStorageAccountName": {
      "type": "string"
    }

### <a name="enablenonsslport"></a>enableNonSslPort
Egy logikai érték, amely azt jelzi, hogy engedélyezi a hozzáférést a nem SSL portok keresztül.

    "enableNonSslPort": {
      "type": "bool"
    }

### <a name="diagnosticsstatus"></a>diagnosticsStatus
Egy érték, amely azt jelzi, hogy engedélyezve van-e a diagnosztika. Használjon ON vagy OFF.

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


