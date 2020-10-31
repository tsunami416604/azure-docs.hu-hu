---
title: PowerShell-szkript Azure Cosmos DB létrehozásához Cassandra API a térköz és a tábla autoscale használatával
description: Azure PowerShell script-Azure Cosmos DB Cassandra API alapterület és tábla létrehozása az autoscale paranccsal
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 07/30/2020
ms.author: mjbrown
ms.openlocfilehash: 631f689348f4fa99bc15209f81436a718df163b4
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93074644"
---
# <a name="create-a-keyspace-and-table-with-autoscale-for-azure-cosmos-db---cassandra-api"></a>Hozzon létre egy alapterületet és egy táblázatot az Azure Cosmos DB-Cassandra API
[!INCLUDE[appliesto-cassandra-api](../../../includes/appliesto-cassandra-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-autoscale.ps1 "Create a keyspace and table with autoscale for Cassandra API")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
|**Azure Cosmos DB**| |
| [Új – AzCosmosDBAccount](/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Létrehoz egy Cosmos DB fiókot. |
| [Új – AzCosmosDBCassandraKeyspace](/powershell/module/az.cosmosdb/new-azcosmosdbcassandrakeyspace) | Létrehoz egy Cosmos DB Cassandra API-tárhelyet. |
| [Új – AzCosmosDBCassandraClusterKey](/powershell/module/az.cosmosdb/new-azcosmosdbcassandraclusterkey) | Létrehoz egy Cosmos DB Cassandra API-fürtöt. |
| [Új – AzCosmosDBCassandraColumn](/powershell/module/az.cosmosdb/new-azcosmosdbcassandracolumn) | Létrehoz egy Cosmos DB Cassandra API oszlopot. |
| [Új – AzCosmosDBCassandraSchema](/powershell/module/az.cosmosdb/new-azcosmosdbcassandraschema) | Létrehoz egy Cosmos DB Cassandra API sémát. |
| [Új – AzCosmosDBCassandraTable](/powershell/module/az.cosmosdb/new-azcosmosdbcassandratable) | Létrehoz egy Cosmos DB Cassandra API táblázatot. |
|**Azure-erőforráscsoportok**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>Következő lépések

További információ a Azure PowerShellről: [Azure PowerShell dokumentációja](/powershell/)
