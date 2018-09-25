---
title: Azure Cosmos DB-fiókok több főkiszolgálós engedélyezése
description: Ez a cikk ismerteti, hogyan lehet engedélyezni az Azure portal, PowerShell, CLI és az Azure Resource Manager-sablonok az Azure Cosmos DB-fiók létrehozása során több főkiszolgálós támogatást.
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 71579e28f389d91498ef5f37c5d43dc9d5140234
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46963219"
---
# <a name="enable-multi-master-for-azure-cosmos-db-accounts"></a>Azure Cosmos DB-fiókok több főkiszolgálós engedélyezése

Több főkiszolgálós támogatást engedélyezve van az Azure Cosmos DB-fiók létrehozása során. Az Azure Cosmos DB-fiókot az Azure portal, PowerShell, CLI vagy az Azure Resource Manager-sablon segítségével hozható létre.

> [!IMPORTANT]
> Jelenleg több főkiszolgálós támogatást az új Azure Cosmos DB-fiókok csak engedélyezhető. Meglévő Azure Cosmos DB-fiókokhoz nem használhatja a szolgáltatást. A Microsoft támogatást nyújt a meglévő fiókok dolgozik, és ez a támogatás jelentjük be, ha elérhetővé.

Miután több főkiszolgálós támogatással rendelkező Azure Cosmos DB-fiókot hoz létre, adatbázisok, a tárolók létrehozása, dokumentumok feltöltése és ütközés feloldása szabályzatokat rendelhet hozzájuk. Több főkiszolgálós és kód mintában ütközésfeloldás, lásd: [több főkiszolgálós Kódminták](multi-master-conflict-resolution.md#code-samples) cikk.

## <a name="enable-multi-master-using-azure-portal"></a>Több főkiszolgálós engedélyezése az Azure portal használatával

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/)

2. Kattintson a Kiválasztás elemre **erőforrás létrehozása > adatbázisok > Azure Cosmos DB**.

3. Az a **új fiók** panelen adjon meg egy új Azure Cosmos DB-fiókot a következő beállításokat:

   |**Beállítás**  |**Ajánlott érték** |**Leírás**|
   |---------|---------|---------|
   |Előfizetés   | {Subscription}  |Válassza ki az Azure-előfizetés az Azure Cosmos DB-fiókhoz használni.  |
   |Erőforráscsoport  |   {Az erőforráscsoport neve}    |  Válasszon ki egy meglévő erőforráscsoportot, vagy válasszon **hozzon létre új**, majd adja meg a fiók új erőforráscsoport nevét. |
   |Fiók neve | {a fiók neve}   |  Adjon meg egy egyedi nevet az Azure Cosmos DB-fiók azonosításához.        |
   |API  |   Bármelyik   |  Válassza ki az API-típust.   |
   |Hely  | Válassza ki bármelyik régióban   | Válassza ki azt a földrajzi helyet, ahol az Azure Cosmos DB-fiókot üzemeltetni fogja. Bármelyik régiót választhatja, mivel ez a fiók több régióban.  |
   |Georedundancia engedélyezése   |  Bekapcsolás  |  Válassza ki ahhoz, hogy többszörös főkiszolgáló alábbi ki kell jelölni.   |
   |A több főkiszolgálós működés engedélyezése | Bekapcsolás  | Válassza ki ahhoz, hogy többszörös főkiszolgáló ehhez a fiókhoz. |


## <a name="using-multi-master-in-sdks"></a>Több főkiszolgálós SDK-k használatával

Több főkiszolgálós fiókkal az alkalmazások belül, kihasználhatja több mastering a ConnectionPolicy kihasználva, ahogy az alábbi.

```csharp
ConnectionPolicy policy = new ConnectionPolicy
{
   ConnectionMode = ConnectionMode.Direct,
   ConnectionProtocol = Protocol.Tcp,
   UseMultipleWriteLocations = true,
};
policy.PreferredLocations.Add("West US");
policy.PreferredLocations.Add("North Europe");
policy.PreferredLocations.Add("Southeast Asia");
```

## <a name="enable-multi-master-using-powershell"></a>Engedélyezze a több főkiszolgálós PowerShell-lel

Több főkiszolgálós engedélyezett Cosmos DB-fiókok beállításával is létrehozhat a `enableMultipleWriteLocations` paraméternek "true". Az Azure Cosmos DB-fiók létrehozásához több főkiszolgálós engedélyezve van, nyisson meg egy PowerShell-ablakot, és futtassa a következő szkriptet:

```azurepowershell-interactive
$locations = @(@{"locationName"="East US"; "failoverPriority"=0},
             @{"locationName"="West US"; "failoverPriority"=1})

$iprangefilter = "<ip-range-filter>"

$consistencyPolicy = @{"defaultConsistencyLevel"="Session";
                       "maxIntervalInSeconds"= "10";
                       "maxStalenessPrefix"="200"}

$CosmosDBProperties = @{"databaseAccountOfferType"="Standard";
                        "locations"=$locations;
                        "consistencyPolicy"=$consistencyPolicy;
                        "ipRangeFilter"=$iprangefilter;
                        "enableMultipleWriteLocations"="true"}

New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
  -ApiVersion "2015-04-08" `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -Name "myCosmosDbAccount" `
  -Properties $CosmosDBProperties
```

## <a name="enable-multi-master-using-cli"></a>Engedélyezze a több főkiszolgálós parancssori felület használatával

Több főkiszolgálós "true" engedélyezése – több-írás – helyek paraméterének beállításával engedélyezheti. Több főkiszolgálós hoz létre az Azure Cosmos DB-fiókot, nyissa meg az Azure CLI vagy a cloud shell, és futtassa a következő parancsot:

```azurecli-interactive
az cosmosdb create \
   –-name "myCosmosDbAccount" \
   --resource-group "myResourceGroup" \
   --default-consistency-level "Session" \
   --enable-automatic-failover "true" \
   --locations "EastUS=0" "WestUS=1" \
   --enable-multiple-write-locations true \
```

## <a name="enable-multi-master-using-resource-manager-template"></a>Több főkiszolgálós használatával engedélyezése Resource Manager-sablon

A következő JSON-kódot egy példa egy Resource Manager-sablon üzembe helyezése az Azure Cosmos DB-fiókot használó. Resource Manager-sablon formátuma és a szintaxissal kapcsolatos további információkért lásd: [Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) dokumentációját. Figyelje meg, hogy ez a sablon a fő paraméter "enableMultipleWriteLocations": true.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String"
        },
        "locationName": {
            "type": "String"
        },
        "defaultExperience": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2015-04-08",
            "location": "[parameters('location')]",
            "tags": {
                "defaultExperience": "[parameters('defaultExperience')]"
            },
            "properties": {
                "databaseAccountOfferType": "Standard",
                "locations": [
                    {
                        "id": "[concat(parameters('name'), '-', parameters('location'))]",
                        "failoverPriority": 0,
                        "locationName": "[parameters('locationName')]"
                    }
                ],
                "isVirtualNetworkFilterEnabled": false,
                "enableMultipleWriteLocations": true,
                "virtualNetworkRules": [],
                "dependsOn": []
            }
        }
    ]
}
```

## <a name="next-steps"></a>További lépések

Ebben a cikkben megismerkedhetett az Azure Cosmos DB-fiókok több főkiszolgálós támogatásának engedélyezése. Ezután keresse meg az alábbi forrásanyagokat:

* [több főkiszolgálós használata a nyílt forráskódú NoSQL-adatbázisok](multi-master-oss-nosql.md)

* [Az Azure Cosmos DB ütközésfeloldás ismertetése](multi-master-conflict-resolution.md)
