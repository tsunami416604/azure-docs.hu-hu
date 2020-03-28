---
title: Prémium szintű Azure-gyorsítótár létrehozása a Redis számára fürtözéssel - Azure CLI
description: Ez az Azure CLI-kódminta bemutatja, hogyan hozhat létre egy 6 GB-os prémium szintű Azure-gyorsítótárat a Redis fürtözéshez engedélyezett és két szegmensek.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: 68203fe2e054f32ce5764fe4f1b07013b0806104
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75411145"
---
# <a name="create-a-premium-azure-cache-for-redis-with-clustering"></a>Prémium szintű Azure-gyorsítótár létrehozása a Redis számára fürtözéssel

Ebben a forgatókönyvben megtudhatja, hogyan hozhat létre egy 6 GB-os prémium szintű Azure-gyorsítótár redis fürtözés engedélyezve van, és két szegmensek.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-premium-cache-cluster/create-premium-cache-cluster.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a parancsfájl a következő parancsokat használja egy erőforráscsoport és egy prémium szintű Azure-gyorsítótár létrehozása a Fürtözési engedélyezéssel. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az redis létrehozása](https://docs.microsoft.com/cli/azure/redis) | Hozzon létre Azure Cache a Redis-példány. |


## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További Azure Cache for Redis CLI script minták találhatók az [Azure Cache for Redis dokumentációban.](../cli-samples.md)
