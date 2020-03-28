---
title: Az állomásnév, portok, kulcsok beolvasása – Azure Cache for Redis - Azure CLI
description: Ez az Azure CLI-kódminta bemutatja, hogyan szerezheti be a állomásnevet, a portokat és a redis-példányok Azure-gyorsítótárának kulcsait.
author: yegu-ms
ms.author: yegu
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.openlocfilehash: 199c5707e9ecd887af64e271184e151ce0966745
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75411300"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-cache-for-redis"></a>Az állomásnév, a portok és a redis-i Azure-gyorsítótár lekéri a gazdagépnevét, portjait és kulcsait

Ebben a forgatókönyvben megtudhatja, hogyan kérheti be a állomásnév, portok és kulcsok, amelyek egy Azure-gyorsítótár redis-példányhoz való csatlakozáshoz használt.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Cache for Redis")]


## <a name="script-explanation"></a>Szkript ismertetése

Ez a parancsfájl a következő parancsokat használja egy Azure-gyorsítótár redis-példány állomásnevének, kulcsainak és portjainak lekéréséhez. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis) | A Redis-példány Azure-gyorsítótárának részleteinek beolvasása. |
| [az redis listabillentyűk](https://docs.microsoft.com/cli/azure/redis) | Hozzáférés-kulcsok lekérése egy Azure-gyorsítótár redis-példányhoz. |


## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További Azure Cache for Redis CLI script minták találhatók az [Azure Cache for Redis dokumentációban.](../cli-samples.md)
