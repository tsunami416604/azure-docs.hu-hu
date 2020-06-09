---
title: Erőforrás-zárolás létrehozása Gremlin-adatbázishoz és gráf a Azure Cosmos DBhoz
description: Erőforrás-zárolás létrehozása Gremlin-adatbázishoz és gráf a Azure Cosmos DBhoz
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 06/03/2020
ms.openlocfilehash: e4aba05df23ce4048ba711680086407cb764a24a
ms.sourcegitcommit: 5504d5a88896c692303b9c676a7d2860f36394c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2020
ms.locfileid: "84514723"
---
# <a name="create-a-resource-lock-for-azure-cosmos-gremlin-api-database-and-graph-using-azure-cli"></a>Erőforrás-zárolás létrehozása az Azure Cosmos Gremlin API-adatbázishoz és-gráfhoz az Azure CLI használatával

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a témakörhöz az Azure CLI 2.6.0 vagy újabb verziójának kell futnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

> [!IMPORTANT]
> Az erőforrás-zárolások nem működnek a Gremlin SDK-val vagy az Azure Portalon keresztül csatlakozó felhasználók által végzett változtatások esetén, kivéve, ha a Cosmos DB fiókot először zárolták az `disableKeyBasedMetadataWriteAccess` engedélyezett tulajdonsággal. Ha többet szeretne megtudni a tulajdonság engedélyezéséről, tekintse meg az SDK-k [módosításainak megelőzését](../../../role-based-access-control.md#preventing-changes-from-cosmos-sdk)ismertető témakört.

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/gremlin/lock.sh "Create a resource lock for an Azure Cosmos DB Gremlin API database and graph.")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az Lock Create](/cli/azure/lock#az-lock-create) | Zárolást hoz létre. |
| [az Lock List](/cli/azure/lock#az-lock-list) | Zárolási információk listázása. |
| [az Lock show](/cli/azure/lock#az-lock-show) | Zárolás tulajdonságainak megjelenítése. |
| [az Lock delete](/cli/azure/lock#az-lock-delete) | Törli a zárolást. |

## <a name="next-steps"></a>Következő lépések

-[Erőforrások zárolása a váratlan módosítások megelőzése érdekében](../../../../azure-resource-manager/management/lock-resources.md)

-[Azure Cosmos db parancssori felület dokumentációja](/cli/azure/cosmosdb).

-[Azure Cosmos db CLI GitHub-tárház](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
