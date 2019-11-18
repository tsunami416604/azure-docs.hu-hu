---
title: 'Azure CLI-szkript – példa: az Azure cache-hez tartozó állomásnév, portok és kulcsok beolvasása a Redis'
description: Azure CLI-szkript minták – a gazdagépnév, portok és kulcsok lekérése az Azure Cache Redis-példány
author: yegu-ms
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.author: yegu
ms.openlocfilehash: 604ec6de3b95a4bc289176d54d9c7b0a6c42eae6
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122516"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-cache-for-redis"></a>A gazdagépnév, portok és kulcsok lekérése az Azure Cache Redis

Ebben a forgatókönyvben, megtudhatja, hogyan kérheti le a gazdagépnév, portok és egy Redis-példányt az Azure Cache való kapcsolódáshoz használt kulcsokat.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Cache for Redis")]


## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja beolvasni az állomásnevet, a kulcsok és a egy Redis-példányt az Azure gyorsítótár-porton. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis) | Kérje le az Azure Cache Redis-példány adatait. |
| [az redis list-keys](https://docs.microsoft.com/cli/azure/redis) | Hozzáférési kulcsok lekérése az Azure Cache a Redis-példányt. |


## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További Azure Cache a Redis CLI-példaszkripteket megtalálható a [Azure Cache Redis dokumentációt](../cli-samples.md).
