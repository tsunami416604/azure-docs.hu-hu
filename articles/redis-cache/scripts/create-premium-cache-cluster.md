---
title: "Az Azure CLI-parancsfájlt minták – hozzon létre egy prémium szintű Azure Redis Cache fürtözési |} Microsoft Docs"
description: "Az Azure CLI-parancsfájlt minta - fürtözési Azure Redis Cache prémium réteg létrehozása"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
tags: azure-service-management
ms.assetid: 07bcceae-2521-4fe3-b88f-ed833104ddd2
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: sdanie
ms.openlocfilehash: 45b2a500751830f6ca19fe8450a7781cb0fcb618
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-premium-azure-redis-cache-with-clustering"></a>Hozzon létre egy prémium szintű Azure Redis Cache fürtözési

Ebben a forgatókönyvben elsajátíthatja a fürtözési engedélyezve van egy 6 GB prémium csomagban Azure Redis Cache létrehozása és a két szilánkok.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-premium-cache-cluster/create-premium-cache-cluster.sh "Azure Redis Cache")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat egy erőforráscsoport és a Premium szint redis gyorsítótár létrehozása a fürtszolgáltatás engedélyezése. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [az csoport létrehozása](https://docs.microsoft.com/cli/azure/group#az_group_create) | Az összes erőforrás tároló erőforrás csoportot hoz létre. |
| [az redis létrehozása](https://docs.microsoft.com/cli/azure/redis#az_redis_create) | Hozzon létre a Redis Cache példányt. |


## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További Azure Redis Cache CLI parancsfájl minták megtalálhatók a [Azure Redis Cache dokumentáció](../cli-samples.md).