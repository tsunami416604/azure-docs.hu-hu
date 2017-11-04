---
title: "Az Azure CLI parancsfájl minta - Get az állomásnevet, portokat és tartozó Azure Redis Cache |} Microsoft Docs"
description: "Az Azure CLI parancsfájl minta - Get az állomásnév portok, és az Azure Redis Cache példány tartozó"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
tags: azure-service-management
ms.assetid: 761eb24e-2ba7-418d-8fc3-431153e69a90
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: sdanie
ms.openlocfilehash: aee24e5c478c4453655952cc626d7d6c857e7962
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="get-the-hostname-ports-and-keys-for-azure-redis-cache"></a>Azure Redis Cache az állomásnév, portokat és kulcsok beszerzése

Ebben a forgatókönyvben megismerheti, hogyan lehet lekérni az állomásnév, portokat és az Azure Redis Cache példány való kapcsolódáshoz használt kulcsok.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Mintaparancsfájl

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Redis Cache")]


## <a name="script-explanation"></a>Parancsfájl ismertetése

A parancsfájl a következő parancsokat az állomásnév, a kulcsok és a portok az Azure Redis Cache példány beolvasása. Minden egyes parancsa a tábla-parancs adott dokumentációjára mutató hivatkozásokat.

| Parancs | Megjegyzések |
|---|---|
| [az redis megjelenítése](https://docs.microsoft.com/cli/azure/redis#az_redis_show) | Beolvasni az Azure Redis Cache példány részleteit. |
| [az-redis-listázása](https://docs.microsoft.com/cli/azure/redis#az_redis_list_keys) | Azure Redis Cache példány elérési kulcsainak beolvasása. |


## <a name="next-steps"></a>Következő lépések

További információ az Azure parancssori felület: [Azure CLI dokumentáció](https://docs.microsoft.com/cli/azure/overview).

További Azure Redis Cache CLI parancsfájl minták megtalálhatók a [Azure Redis Cache dokumentáció](../cli-samples.md).