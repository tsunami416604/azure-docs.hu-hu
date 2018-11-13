---
title: Azure CLI-szkript – Azure Cosmos DB Table API-fiók, -adatbázis és -táblázat létrehozása | Microsoft Docs
description: Azure CLI-példaszkript – Azure Cosmos DB Table API-fiók, -adatbázis és -táblázat létrehozása
author: markjbrown
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.topic: sample
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: 178ecfe6971a47009781d8866986d936f8e3a5d2
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51019556"
---
# <a name="azure-cosmos-db-create-a-table-api-account-using-azure-cli"></a>Azure Cosmos DB: Table API-fiók létrehozása az Azure CLI használatával

Ez a CLI-példaszkript létrehoz egy Azure Cosmos DB Table API-fiókot, -adatbázist és -táblázatot.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure parancssori felületének 2.0-s vagy annál újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/cosmosdb/create-cosmosdb-table-account/create-cosmosdb-table-account.sh "Create an Azure Cosmos DB Table API account, database, and table.")]

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
| [az cosmosdb database create](/cli/azure/cosmosdb/database#az-cosmosdb-database-create) | Létrehoz egy Azure Cosmos DB-adatbázist. |
| [az cosmosdb collection create](/cli/azure/cosmosdb/collection#az-cosmosdb-collection-create) | Létrehoz egy Azure Cosmos DB-táblázatot. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](/cli/azure).

További Azure Cosmos DB CLI-példaszkripteket az [Azure Cosmos DB CLI dokumentációjában](../cli-samples.md) találhat.
