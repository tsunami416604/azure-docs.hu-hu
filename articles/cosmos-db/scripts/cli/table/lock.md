---
title: Azure Cosmos DB Table API tábla erőforrás-zárolásának létrehozása
description: Azure Cosmos DB Table API tábla erőforrás-zárolásának létrehozása
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 06/03/2020
ms.openlocfilehash: bb66f215241755ae0fbcef3c95f5ea67a686a4fd
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87092575"
---
# <a name="create-resource-lock-for-a-azure-cosmos-db-table-api-table-using-azure-cli"></a>Erőforrás-zárolás létrehozása Azure Cosmos DB Table API táblázathoz az Azure CLI használatával

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a témakörhöz az Azure CLI 2.6.0 vagy újabb verziójának kell futnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

> [!IMPORTANT]
> Az erőforrás-zárolások nem működnek a Cosmos DB Table SDK-t, az Azure Storage Table SDK-t, a fiók kulcsain keresztül csatlakozó eszközöket, illetve az Azure Portalt, kivéve, ha a Cosmos DB fiókot a rendszer először zárolja az `disableKeyBasedMetadataWriteAccess` engedélyezett tulajdonsággal. Ha többet szeretne megtudni a tulajdonság engedélyezéséről, tekintse meg az SDK-k [módosításainak megelőzését](../../../role-based-access-control.md#prevent-sdk-changes)ismertető témakört.

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/table/lock.sh "Create a resource lock for an Azure Cosmos DB Table API table.")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az Lock Create](/cli/azure/lock#az-lock-create) | Zárolást hoz létre. |
| [az Lock List](/cli/azure/lock#az-lock-list) | Zárolási információk listázása. |
| [az Lock show](/cli/azure/lock#az-lock-show) | Zárolás tulajdonságainak megjelenítése. |
| [az Lock delete](/cli/azure/lock#az-lock-delete) | Törli a zárolást. |

## <a name="next-steps"></a>További lépések

-[Erőforrások zárolása a váratlan módosítások megelőzése érdekében](../../../../azure-resource-manager/management/lock-resources.md)

-[Azure Cosmos db parancssori felület dokumentációja](/cli/azure/cosmosdb).

-[Azure Cosmos db CLI GitHub-tárház](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
