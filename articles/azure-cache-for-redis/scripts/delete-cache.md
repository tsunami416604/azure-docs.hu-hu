---
title: Azure-gyorsítótár törlése a Redis-hez – Azure CLI
description: Ez az Azure CLI-kódminta bemutatja, hogyan törölheti az Azure Cache for Redis példány az az redis delete paranccsal.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: e5178ab4069cc5ffa8607c5feea6ffac86284a5b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75411075"
---
# <a name="delete-an-azure-cache-for-redis"></a>A Redis Azure-gyorsítótárának törlése

Ebben a forgatókönyvben megtudhatja, hogyan törölheti az Azure-gyorsítótár redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli[main](../../../cli_scripts/redis-cache/delete-cache/delete-cache.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a parancsfájl a következő parancsokat használja a Redis-példány Azure-gyorsítótárának törléséhez. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az redis törlés](https://docs.microsoft.com/cli/azure/redis) | Törölje az Azure Cache a Redis-példány. |


## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További Azure Cache for Redis CLI script minták találhatók az [Azure Cache for Redis dokumentációban.](../cli-samples.md)
