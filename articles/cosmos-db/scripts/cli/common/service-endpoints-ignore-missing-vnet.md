---
title: Meglévő Azure Cosmos-fiók csatlakoztatása virtuális hálózati szolgáltatásvégpontokkal
description: Meglévő Azure Cosmos-fiók csatlakoztatása virtuális hálózati szolgáltatásvégpontokkal
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: 46e93e864034c451e1da1848a318ab176a292b6e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "71275562"
---
# <a name="connect-an-existing-azure-cosmos-account-with-virtual-network-service-endpoints-using-azure-cli"></a>Meglévő Azure Cosmos-fiók csatlakoztatása virtuális hálózati szolgáltatásvégpontokkal az Azure CLI használatával

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy helyileg telepíti és használja a CLI-t, ez a témakör megköveteli, hogy az Azure CLI 2.0.73-as vagy újabb verzióját futassza. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Példaszkript

Ez a minta azt mutatja be, hogyan csatlakoztathat egy meglévő Azure Cosmos-fiókot egy meglévő új `ignore-missing-vnet-service-endpoint` virtuális hálózathoz, ahol az alhálózat még nincs konfigurálva a szolgáltatásvégpontokhoz a paraméter használatával. Ez lehetővé teszi, hogy a Cosmos-fiók konfigurációja hiba nélkül befejeződjen, mielőtt a virtuális hálózat alhálózatának konfigurációja befejeződne. Az alhálózati konfiguráció befejezése után a Cosmos-fiók ezután elérhető lesz a konfigurált alhálózaton keresztül.

> [!NOTE]
> Ez a minta egy SQL (Core) API-fiók használatával szemlélteti. Ha ezt a mintát más API-khoz szeretné használni, alkalmazza az alábbi parancsfájlban lévő paramétereket `enable-virtual-network` `virtual-network-rules` az API-specifikus parancsfájlra.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/service-endpoints-ignore-missing-vnet.sh "Create an Azure Cosmos account with service endpoints.")]

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
| [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) | Létrehoz egy Azure virtuális hálózatot. |
| [az network vnet subnet create](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-create) | Alhálózatot hoz létre egy Azure virtuális hálózathoz. |
| [az hálózati virtuális hálózat alhálózata](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-show) | Egy Azure virtuális hálózat alhálózatát adja vissza. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Létrehoz egy Azure Cosmos DB-fiókot. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet#az-network-vnet-subnet-update) | Egy Azure virtuális hálózat alhálózatának frissítése. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure Cosmos DB CLI-ről az [Azure Cosmos DB CLI dokumentációjában](/cli/azure/cosmosdb)olvashat bővebben.

Az Azure Cosmos DB CLI parancsfájlminták megtalálhatók az [Azure Cosmos DB CLI GitHub-tárházban.](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)
