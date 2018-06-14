---
title: Az Azure CLI-parancsfájlt minták – hozzon létre egy prémium szintű Azure Redis Cache fürtözési |} Microsoft Docs
description: Az Azure CLI-parancsfájlt minta - fürtözési Azure Redis Cache prémium réteg létrehozása
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: 07bcceae-2521-4fe3-b88f-ed833104ddd2
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: fb97d19d50ac9845c2495bd167a9cd30805d464b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
ms.locfileid: "29846298"
---
# <a name="create-a-premium-azure-redis-cache-with-clustering"></a>Hozzon létre egy prémium szintű Azure Redis Cache fürtözési

Ebben a forgatókönyvben elsajátíthatja a fürtözési engedélyezve van egy 6 GB prémium csomagban Azure Redis Cache létrehozása és a két szilánkok.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli[main](../../../cli_scripts/redis-cache/create-premium-cache-cluster/create-premium-cache-cluster.sh "Azure Redis Cache")]

[!INCLUDE [cli-script-clean-up](../../../includes/redis-cli-script-clean-up.md)]

## <a name="script-explanation"></a>Szkript ismertetése

A parancsfájl a következő parancsokat egy erőforráscsoport és a Premium szint redis gyorsítótár létrehozása a fürtszolgáltatás engedélyezése. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#az_group_create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az redis létrehozása](https://docs.microsoft.com/cli/azure/redis#az_redis_create) | Hozzon létre a Redis Cache példányt. |


## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További Azure Redis Cache CLI parancsfájl minták megtalálhatók a [Azure Redis Cache dokumentáció](../cli-samples.md).