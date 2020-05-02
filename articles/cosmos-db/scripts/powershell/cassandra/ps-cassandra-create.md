---
title: PowerShell-parancsfájl Azure Cosmos DB létrehozásához Cassandra API szóköz és tábla
description: Azure PowerShell script-Azure Cosmos DB Cassandra API alapterület és tábla létrehozása
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: c81c2962ef7868394f70bc3ca5f3a147bb4f268b
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82653084"
---
# <a name="create-a-keyspace-and-table-for-azure-cosmos-db---cassandra-api"></a>Térköz és tábla létrehozása a Azure Cosmos DB-Cassandra API

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-create.ps1 "Create a keyspace and table for Cassandra API")]

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
| [Új – AzCosmosDBAccount](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Új Cosmos DB-fiók létrehozása. |
| [Set-AzCosmosDBCassandraKeyspace](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbcassandrakeyspace) | Létrehoz vagy frissít egy Cosmos DB Cassandra API lemezterületet. |
| [Új – AzCosmosDBCassandraClusterKey](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbcassandraclusterkey) | Létrehoz egy új CosmosDB Cassandra-fürtöt. |
| [Új – AzCosmosDBCassandraColumn](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbcassandracolumn) | Létrehoz egy új CosmosDB Cassandra oszlopot. |
| [Új – AzCosmosDBCassandraSchema](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbcassandraschema) | Új CosmosDB Cassandra-sémát hoz létre. |
| [Set-AzCosmosDBCassandraTable](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbcassandratable) | Egy Cosmos DB Cassandra API tábla létrehozása vagy frissítése. |
|**Azure-erőforráscsoportok**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Cosmos DB PowerShell-példaszkripteket az [Azure Cosmos DB PowerShell-szkriptek között](../../../powershell-samples.md) találhat.