---
title: Régiók hozzáadása, feladatátvételi prioritás módosítása, feladatátvétel aktiválása egy Azure Cosmos-fiókhoz
description: Régiók hozzáadása, feladatátvételi prioritás módosítása, feladatátvétel aktiválása egy Azure Cosmos-fiókhoz
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: b7b6be0ce781debcb19b5c0fb7b6a4b0123ef366
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "71275548"
---
# <a name="add-regions-change-failover-priority-trigger-failover-for-an-azure-cosmos-account-using-azure-cli"></a>Régiók hozzáadása, feladatátvételi prioritás módosítása, feladatátvétel i. aktiválása Azure Cosmos-fiókhoz az Azure CLI használatával

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy helyileg telepíti és használja a CLI-t, ez a témakör megköveteli, hogy az Azure CLI 2.0.73-as vagy újabb verzióját futassza. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Példaszkript

Ez a parancsfájl három műveletet mutat be.

- Adjon hozzá egy régiót egy meglévő Azure Cosmos-fiókhoz.
- A területi feladatátvételi prioritás módosítása (az automatikus feladatátvételt használó fiókokra vonatkozik)
- Manuális feladatátvétel indítása az elsődleges és a másodlagos régiók között (manuális feladatátvételi rendszerrel rendelkező számlákra vonatkozik)

> [!NOTE]
> A Cosmos-fiók régióműveleteinek hozzáadása és eltávolítása nem végezhető el más tulajdonságok módosítása közben.

> [!NOTE]
> Ez a minta egy SQL (Core) API-fiók használatával szemlélteti, de ezek a műveletek azonosak a Cosmos DB összes adatbázis API-jában.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/regions.sh "Regional operations for Cosmos DB.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Létrehoz egy Azure Cosmos DB-fiókot. |
| [az cosmosdb update](/cli/azure/cosmosdb#az-cosmosdb-update) | Frissíti az Azure Cosmos DB-fiókját (régió hozzáadása vagy eltávolítása). |
| [az cosmosdb feladatátvétel-prioritás-változás](/cli/azure/cosmosdb#az-cosmosdb-failover-priority-change) | A feladatátvételi prioritás frissítése vagy az Azure Cosmos DB-fiók feladatátvételi aktiválása. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure Cosmos DB CLI-ről az [Azure Cosmos DB CLI dokumentációjában](/cli/azure/cosmosdb)olvashat bővebben.

Az Azure Cosmos DB CLI parancsfájlminták megtalálhatók az [Azure Cosmos DB CLI GitHub-tárházban.](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)
