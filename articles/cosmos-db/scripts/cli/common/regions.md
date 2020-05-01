---
title: Régiók hozzáadása, feladatátvételi prioritás módosítása, feladatátvétel indítása Azure Cosmos-fiókhoz
description: Régiók hozzáadása, feladatátvételi prioritás módosítása, feladatátvétel indítása Azure Cosmos-fiókhoz
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: b7b6be0ce781debcb19b5c0fb7b6a4b0123ef366
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "71275548"
---
# <a name="add-regions-change-failover-priority-trigger-failover-for-an-azure-cosmos-account-using-azure-cli"></a>Régiók hozzáadása, feladatátvételi prioritás módosítása, feladatátvétel indítása Azure Cosmos-fiókhoz az Azure CLI használatával

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a témakörhöz az Azure CLI 2.0.73 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Példaszkript

Ez a szkript három műveletet mutat be.

- Régió hozzáadása egy meglévő Azure Cosmos-fiókhoz.
- Regionális feladatátvételi prioritás módosítása (az automatikus feladatátvételt használó fiókokra vonatkozik)
- Manuális feladatátvétel elindítása az elsődlegesről a másodlagos régiókba (a manuális feladatátvételsel rendelkező fiókokra vonatkozik)

> [!NOTE]
> Területi műveletek hozzáadása és eltávolítása Cosmos-fiókban nem végezhető el más tulajdonságok módosításakor.

> [!NOTE]
> Ez a példa egy SQL-(Core-) API-fiók használatát mutatja be, de ezek a műveletek megegyeznek az összes Cosmos DB adatbázis-API-ban.

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
| [az cosmosdb update](/cli/azure/cosmosdb#az-cosmosdb-update) | Egy Azure Cosmos DB fiók frissítése (régió hozzáadása vagy eltávolítása). |
| [az cosmosdb feladatátvétel – prioritás – módosítás](/cli/azure/cosmosdb#az-cosmosdb-failover-priority-change) | Frissítse a feladatátvételi prioritást, vagy indítsa el a feladatátvételt egy Azure Cosmos DB fiókon. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

A Azure Cosmos DB CLI-vel kapcsolatos további információkért lásd: [Azure Cosmos db parancssori felület dokumentációja](/cli/azure/cosmosdb).

A CLI-szkriptek összes Azure Cosmos DB a [Azure Cosmos db CLI GitHub-tárházban](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)található.
