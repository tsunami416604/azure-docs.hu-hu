---
title: Azure CLI-példaszkript – Dedikált Batch AI-fürt létrehozása | Microsoft Docs
description: Azure CLI-példaszkript – Dedikált csomópontokból (virtuális gépekből) álló Batch AI-fürt létrehozása és kezelése
services: batch-ai
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch-ai
ms.devlang: azurecli
ms.topic: sample
ms.tgt-pltfrm: multiple
ms.workload: na
ms.date: 07/26/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 643d2a5a600d018912f09d01f2e018100767f147
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53410125"
---
# <a name="cli-example-create-and-manage-a-batch-ai-cluster-of-dedicated-nodes"></a>CLI-példa: Dedikált csomópontok egy Batch AI-fürt létrehozása és kezelése

[!INCLUDE [batch-ai-retiring](../../../includes/batch-ai-retiring.md)]

Ez a szkript olyan Azure CLI-parancsokra mutat példákat, amelyekkel dedikált csomópontokból (virtuális gépekből) álló Batch AI-fürtök hozhatók létre és kezelhetők.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.38-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/batch-ai/create-cluster/create-cluster-dedicated.sh "Create Batch AI cluster - dedicated nodes")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

Az erőforráscsoportok és az azokhoz kapcsolódó erőforrások az alábbi parancsokkal távolíthatók el.

```azurecli-interactive
# Remove resource group for the cluster.
az group delete --name myResourceGroup

# Remove resource group for the auto-storage account.
az group delete --name batchaiautostorage
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az batchai workspace create](/cli/azure/batchai/workspace#az-batchai-workspace-create) | Létrehoz egy Batch AI-munkaterületet. |
| [az batchai cluster create](/cli/azure/batchai/cluster#az-batchai-cluster-create) | Létrehoz egy Batch AI-fürtöt. |
| [az batchai cluster show](/cli/azure/batchai/cluster#az-batchai-cluster-show) | Megjeleníti a Batch AI-fürtre vonatkozó információkat. |
| [az batchai cluster node list](/cli/azure/batchai/cluster/node#az-batchai-cluster-show) | Listázza a Batch AI-fürt csomópontjait. |
| [az batchai cluster resize](/cli/azure/batchai/cluster#az-batchai-cluster-resize) | Átméretezi a Batch AI-fürtöt.  |
| [az group delete](/cli/azure/group#az-group-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).
