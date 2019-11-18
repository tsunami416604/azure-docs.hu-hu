---
title: Az Azure CLI-Példaszkript – törlés Azure Cache redis
description: Az Azure CLI-Példaszkript – törlés Azure Cache redis
author: yegu-ms
tags: azure-service-management
ms.service: cache
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/30/2017
ms.author: yegu
ms.openlocfilehash: a2884fd326b6091680b8d81a905f3ee3320a2740
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74121201"
---
# <a name="delete-an-azure-cache-for-redis"></a>A Redis Azure Cache törlése

Ebben a forgatókönyvben további törlése az Azure Cache redis.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli[main](../../../cli_scripts/redis-cache/delete-cache/delete-cache.sh "Azure Cache for Redis")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja egy Azure Cache Redis-példány törlése. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az redis delete](https://docs.microsoft.com/cli/azure/redis) | Azure Cache Redis-példány törlése. |


## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További Azure Cache a Redis CLI-példaszkripteket megtalálható a [Azure Cache Redis dokumentációt](../cli-samples.md).
