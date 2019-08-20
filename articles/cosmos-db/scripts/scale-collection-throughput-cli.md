---
title: Azure CLI-szkript – az Azure Cosmos-tároló átviteli sebességének méretezése | Microsoft Docs
description: Azure CLI-parancsfájl – példa – az Azure Cosmos-tároló átviteli sebességének méretezése
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/26/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: a7ae4b015ba476ca1ffdb4b4510334490a6f6fee
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616697"
---
# <a name="scale-azure-cosmos-container-throughput-using-the-azure-cli"></a>Az Azure Cosmos-tároló átviteli sebességének méretezése az Azure CLI használatával

Ez a minta a tároló átviteli sebességét méretezi bármilyen Azure Cosmos-tárolóra.  

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure parancssori felületének 2.0-s vagy annál újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/scale-cosmosdb-throughput/scale-cosmosdb-throughput.sh "Scale Azure Cosmos DB throughput")]

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
| [az cosmosdb database create](/cli/azure/cosmosdb/database#az-cosmosdb-database-create) | Létrehoz egy Azure Cosmos-adatbázist. |
| [az cosmosdb collection create](/cli/azure/cosmosdb/collection#az-cosmosdb-collection-create) | Létrehoz egy Azure Cosmos-tárolót. |
| [az cosmosdb collection update](/cli/azure/cosmosdb/collection#az-cosmosdb-collection-update) | Frissít egy Azure Cosmos-tárolót. |
| [az group delete](/cli/azure/group#az-group-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További Azure Cosmos DB CLI-példaszkripteket az [Azure Cosmos DB CLI dokumentációjában](../cli-samples.md) találhat.
