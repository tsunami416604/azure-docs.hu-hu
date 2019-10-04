---
title: A fiók kulcsainak és a kapcsolatok karakterláncának használata Azure Cosmos-fiókhoz
description: A fiók kulcsainak és a kapcsolatok karakterláncának használata Azure Cosmos-fiókhoz
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: e766f52d729a4f916eefd2b148d926d929b4f540
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71275555"
---
# <a name="work-with-account-keys-and-connection-strings-for-an-azure-cosmos-account-using-azure-cli"></a>A fiók kulcsainak és a kapcsolatok karakterláncának használata Azure Cosmos-fiókhoz az Azure CLI használatával

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a témakörhöz az Azure CLI 2.0.73 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Példaszkript

Ez a szkript négy műveletet mutat be.

- Az összes fiók kulcsainak listázása
- Csak olvasható fiókok kulcsainak listázása
- Kapcsolatok karakterláncok listázása
- Fiók kulcsainak újragenerálása

> [!NOTE]
> Ez a példa egy SQL-(Core-) API-fiók használatát mutatja be, de a fiók kulcsa és a kapcsolatok karakterlánca azonos a Cosmos DB összes adatbázis-API-jával.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/keys.sh "Keys and connection string operations for Cosmos DB.")]

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
| [az cosmosdb Keys List](/cli/azure/cosmosdb/keys#az-cosmosdb-keys-list) | Egy Azure Cosmos DB fiók kulcsainak listázása. |
| [az cosmosdb List-Read-Only-Keys](/cli/azure/cosmosdb#az-cosmosdb-list-read-only-keys) | Egy Azure Cosmos DB fiók írásvédett kulcsainak listázása. |
| [az cosmosdb list-connection-strings](/cli/azure/cosmosdb#az-cosmosdb-list-connection-strings) | Egy Azure Cosmos DB fiókhoz tartozó kapcsolatok karakterláncok listázása. |
| [az cosmosdb regenerate-key](/cli/azure/cosmosdb#az-cosmosdb-regenerate-key) | Kulcsok újragenerálása egy Azure Cosmos DB-fiókhoz. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

A Azure Cosmos DB CLI-vel kapcsolatos további információkért lásd: [Azure Cosmos db parancssori felület dokumentációja](/cli/azure/cosmosdb).

A CLI-szkriptek összes Azure Cosmos DB a [Azure Cosmos db CLI GitHub-tárházban](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)található.
