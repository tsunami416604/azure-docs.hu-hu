---
title: Prémium szintű Azure cache létrehozása fürtözési Redis – Azure CLI
description: Ez az Azure CLI-kódrészlet azt mutatja be, hogyan hozható létre egy 6 GB-os prémium szintű Azure cache a Redis-hez, amely lehetővé teszi a fürtözést és a két szegmenst.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: 68203fe2e054f32ce5764fe4f1b07013b0806104
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75411145"
---
# <a name="create-a-premium-azure-cache-for-redis-with-clustering"></a>Prémium szintű Azure cache létrehozása a fürtözést biztosító Redis

Ebből a forgatókönyvből megtudhatja, hogyan hozhat létre egy 6 GB-os prémium szintű Azure cache-t az Redis-hez és két szegmenshez való fürtözéshez.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-premium-cache-cluster/create-premium-cache-cluster.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja egy erőforráscsoport és egy prémium szintű Azure cache létrehozásához a fürtözési engedélyezéssel rendelkező Redis. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az Redis Create](https://docs.microsoft.com/cli/azure/redis) | Azure cache létrehozása a Redis-példányhoz. |


## <a name="next-steps"></a>Következő lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További Azure cache a Redis CLI-szkriptekhez az [Azure cache Redis dokumentációjában](../cli-samples.md)található.
