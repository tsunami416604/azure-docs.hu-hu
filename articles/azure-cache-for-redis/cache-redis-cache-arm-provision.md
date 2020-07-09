---
title: Az Azure cache üzembe helyezése a Redis Azure Resource Manager
description: Megtudhatja, hogyan helyezhet üzembe egy Azure cache-t a Redis-erőforráshoz Azure Resource Manager sablonnal. A sablonok a gyakori forgatókönyvek esetében érhetők el.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 01/23/2017
ms.openlocfilehash: 0dbcbd173ce0a7c4c6a123f0644b870aa3cec2f5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85829890"
---
# <a name="create-an-azure-cache-for-redis-using-a-template"></a>Azure cache létrehozása Redis sablon használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ebben a témakörben megtudhatja, hogyan hozhat létre olyan Azure Resource Manager-sablont, amely üzembe helyez egy Azure-gyorsítótárat a Redis. A gyorsítótárat meglévő Storage-fiókkal is használhatja a diagnosztikai adatkezelés érdekében. Azt is megtudhatja, hogyan határozhatja meg, hogy mely erőforrások legyenek telepítve, és hogyan határozhatja meg a központi telepítés végrehajtásakor megadott paramétereket. Ez a sablont használhatja a saját környezeteiben, vagy testre is szabhatja a saját követelményeinek megfelelően.

Jelenleg a diagnosztikai beállítások az adott régióban lévő összes gyorsítótárhoz meg vannak osztva egy előfizetéshez. A régió egyik gyorsítótárának frissítése hatással van a régió összes többi gyorsítótárára.

További információ a sablonok létrehozásáról: [Azure Resource Manager-sablonok](../azure-resource-manager/templates/template-syntax.md)készítése. A gyorsítótár-erőforrástípusok JSON-szintaxisának és-tulajdonságainak megismeréséhez tekintse meg a [Microsoft. cache típusú erőforrástípusok](/azure/templates/microsoft.cache/allversions)című témakört.

A teljes sablonhoz lásd: az [Azure cache for Redis sablonja](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json).

> [!NOTE]
> Az új [prémium szinthez](cache-premium-tier-intro.md) Resource Manager-sablonok érhetők el. 
> 
> * [Prémium szintű Azure cache létrehozása a fürtözést biztosító Redis](https://azure.microsoft.com/resources/templates/201-redis-premium-cluster-diagnostics/)
> * [Prémium szintű Azure cache létrehozása a Redis adatmegőrzési szolgáltatásával](https://azure.microsoft.com/resources/templates/201-redis-premium-persistence/)
> * [Virtual Network üzembe helyezett prémium szintű Redis Cache létrehozása](https://azure.microsoft.com/resources/templates/201-redis-premium-vnet/)
> 
> A legújabb sablonok kereséséhez tekintse meg az [Azure Gyorsindítás sablonjait](https://azure.microsoft.com/documentation/templates/) , és keressen rá `Azure Cache for Redis` .
> 
> 

## <a name="what-you-will-deploy"></a>Üzembe helyezés
Ebben a sablonban egy Azure cache-t fog telepíteni a Redis, amely egy meglévő Storage-fiókot használ a diagnosztikai szolgáltatásokhoz.

Az automatikus üzembe helyezéshez kattintson az alábbi gombra:

[![Üzembe helyezés az Azure-ban](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="parameters"></a>Paraméterek
Az Azure Resource Managerrel meghatározhatja a sablon üzembe helyezésekor megadandó értékek paramétereit. A sablon tartalmaz egy paramétert, amely az összes paraméter értékét tartalmazza.
Azokhoz az értékekhez adjon meg paramétert, amelyek az üzembe helyezendő projekt vagy az üzembe helyezési környezet alapján változhatnak. Ne adjon meg olyan paramétereket olyan értékhez, amelyek nem változnak. A sablonban minden egyes paraméterérték az üzembe helyezendő erőforrások megadásához lesz felhasználva. 

[!INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### <a name="rediscachelocation"></a>redisCacheLocation
A Redis tartozó Azure cache helye. A legjobb teljesítmény érdekében használja ugyanazt a helyet, mint amelyet a gyorsítótárhoz használni kíván.

```json
  "redisCacheLocation": {
    "type": "string"
  }
```

### <a name="existingdiagnosticsstorageaccountname"></a>existingDiagnosticsStorageAccountName
A diagnosztikai célra használandó meglévő Storage-fiók neve. 

```json
  "existingDiagnosticsStorageAccountName": {
    "type": "string"
  }
```

### <a name="enablenonsslport"></a>enableNonSslPort
Logikai érték, amely azt jelzi, hogy engedélyezi-e a hozzáférést a nem SSL-portokon keresztül.

```json
  "enableNonSslPort": {
    "type": "bool"
  }
```

### <a name="diagnosticsstatus"></a>diagnosticsStatus
Egy érték, amely jelzi, hogy engedélyezve van-e a diagnosztika. Használat be-és kikapcsolása.

```json
  "diagnosticsStatus": {
    "type": "string",
    "defaultValue": "ON",
    "allowedValues": [
          "ON",
          "OFF"
      ]
  }
```

## <a name="resources-to-deploy"></a>Üzembe helyezendő erőforrások
### <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Létrehozza az Azure cache-t a Redis számára.

```json
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
```

## <a name="commands-to-run-deployment"></a>Az üzembe helyezést futtató parancsok
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```azurepowershell
    New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup
```
