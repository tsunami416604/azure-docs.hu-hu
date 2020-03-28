---
title: Azure-gyorsítótár létrehozása a Redis számára – Azure CLI
description: Ez az Azure CLI-kódminta bemutatja, hogyan hozhat létre egy Azure-gyorsítótárat a Redis-példányhoz az az redis create paranccsal.
author: yegu-ms
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.author: yegu
ms.openlocfilehash: 79b749c0d02a21c1225ee0d046d73ed3fdd98904
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75411322"
---
# <a name="create-an-azure-cache-for-redis"></a>Az Azure Cache for Redis létrehozása

Ebben a forgatókönyvben megtudhatja, hogyan hozhat létre egy Azure-gyorsítótár redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-cache/create-cache.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a parancsfájl a következő parancsokat használja egy erőforráscsoport és egy Azure-gyorsítótár a Redis létrehozásához. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az redis létrehozása](https://docs.microsoft.com/cli/azure/redis) | Hozzon létre Azure Cache a Redis-példány. |


## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További Azure Cache for Redis CLI script minták találhatók az [Azure Cache for Redis dokumentációban.](../cli-samples.md)
