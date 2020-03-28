---
title: Az Azure Cache for Redis részletei – Azure CLI
description: Ez az Azure CLI-kódminta bemutatja, hogyan kérheti le a Redis-példány Azure-gyorsítótárának részleteit, beleértve a kiépítési állapotát is.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: 54920017f4a0598a74a54114e3d1e0a5392d7d2a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75411054"
---
# <a name="get-details-of-an-azure-cache-for-redis"></a>A Redis Azure-gyorsítótárának részletei

Ebben a forgatókönyvben megtudhatja, hogyan kérheti le az Azure-gyorsítótár a Redis-példány részleteit, beleértve a kiépítési állapotát is.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli[main](../../../cli_scripts/redis-cache/show-cache/show-cache.sh "Azure Cache for Redis")]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a parancsfájl a következő parancsokat használja egy Azure-gyorsítótár redis-példány részleteinek lekéréséhez. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis) | A Redis-példány Azure-gyorsítótárának részleteinek beolvasása. |


## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További Azure Cache for Redis CLI script minták találhatók az [Azure Cache for Redis dokumentációban.](../cli-samples.md)
