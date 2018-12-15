---
title: Azure CLI-példaszkript – Batch AI-fürt létrehozása konfigurációs fájllal | Microsoft Docs
description: Azure CLI-példaszkript – Batch AI-fürt létrehozása JSON-fájlban megadott konfigurációs beállításokkal.
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
ms.date: 08/16/2018
ms.author: danlep
ROBOTS: NOINDEX
ms.openlocfilehash: 41a3a801214ff00c01397034e26fde6946ab97f0
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53407813"
---
# <a name="cli-example-create-a-batch-ai-cluster-using-a-cluster-configuration-file"></a>CLI-példa: Hozzon létre egy Batch AI-fürtöt egy fürt konfigurációs fájl használatával

[!INCLUDE [batch-ai-retiring](../../../includes/batch-ai-retiring.md)]

Ez a példaszkript bemutatja, hogyan adhatja meg egy Batch AI-fürt beállításait konfigurációs JSON-fájllal. Adja meg ezeket a beállításokat az `az batchai cluster create` parancs megfelelő parancssori paramétereiként. A konfigurációs fájlok akkor lehetnek hasznosak, ha több fájlrendszert kell csatlakoztatnia a fürtcsomópontokhoz, vagy ha több fürtön is ugyanazt a konfigurációt szeretné alkalmazni.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez az útmutatóhoz az Azure CLI 2.0.38-as vagy újabb verziójára lesz szükség. Futtassa az `az --version` parancsot a verzió meghatározásához. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli). 

## <a name="example-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../cli_scripts/batch-ai/create-cluster/create-cluster-config-file.sh "Create Batch AI cluster - configuration file")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

Az erőforráscsoportok és az azokhoz kapcsolódó erőforrások az alábbi parancsokkal távolíthatók el.

```azurecli-interactive
# Remove resource group for the cluster.
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Létrehoz egy tárfiókot. |
| [az storage share create](/cli/azure/storage/share#az-storage-share-create) | Létrehoz egy fájlmegosztást a tárfiókban. |
| [az batchai workspace create](/cli/azure/batchai/workspace#az-batchai-workspace-create) | Létrehoz egy Batch AI-munkaterületet. |
| [az batchai cluster create](/cli/azure/batchai/cluster#az-batchai-cluster-create) | Létrehoz egy Batch AI-fürtöt. |
| [az batchai cluster show](/cli/azure/batchai/cluster#az-batchai-cluster-show) | Megjeleníti a Batch AI-fürtre vonatkozó információkat. |
| [az group delete](/cli/azure/group#az-group-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).
