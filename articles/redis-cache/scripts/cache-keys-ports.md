---
title: Az Azure CLI parancsfájl minta - Get az állomásnevet, portokat és tartozó Azure Redis Cache |} Microsoft Docs
description: Az Azure CLI parancsfájl minta - Get az állomásnév portok, és az Azure Redis Cache példány tartozó
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
tags: azure-service-management
ms.assetid: 761eb24e-2ba7-418d-8fc3-431153e69a90
ms.service: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 08/30/2017
ms.author: wesmc
ms.openlocfilehash: 9a2a6a7ed4bb56f93c965a5bfd24b21368117c7b
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
ms.locfileid: "29846451"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-redis-cache"></a>Azure Redis Cache az állomásnév, portokat és kulcsok beszerzése

Ebben a forgatókönyvben megismerheti, hogyan lehet lekérni az állomásnév, portokat és az Azure Redis Cache példány való kapcsolódáshoz használt kulcsok.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Redis Cache")]


## <a name="script-explanation"></a>Szkript ismertetése

A parancsfájl a következő parancsokat az állomásnév, a kulcsok és a portok az Azure Redis Cache példány beolvasása. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az redis megjelenítése](https://docs.microsoft.com/cli/azure/redis#az_redis_show) | Beolvasni az Azure Redis Cache példány részleteit. |
| [az-redis-listázása](https://docs.microsoft.com/cli/azure/redis#az_redis_list_keys) | Azure Redis Cache példány elérési kulcsainak beolvasása. |


## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További Azure Redis Cache CLI parancsfájl minták megtalálhatók a [Azure Redis Cache dokumentáció](../cli-samples.md).