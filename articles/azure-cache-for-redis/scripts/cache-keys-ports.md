---
title: Azure CLI-szkript minták – a gazdagépnév, portok és kulcsok lekérése az Azure Cache Redis |} A Microsoft Docs
description: Azure CLI-szkript minták – a gazdagépnév, portok és kulcsok lekérése az Azure Cache Redis-példány
services: azure-cache-for-redis
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
ms.openlocfilehash: 820161b5ea5c1d071292897cab30f921f1256cf3
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53019633"
---
# <a name="get-the-hostname-ports-and-keys-for-azure-cache-for-redis"></a>A gazdagépnév, portok és kulcsok lekérése az Azure Cache Redis

Ebben a forgatókönyvben, megtudhatja, hogyan kérheti le a gazdagépnév, portok és egy Redis-példányt az Azure Cache való kapcsolódáshoz használt kulcsokat.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

## <a name="sample-script"></a>Példaszkript

[!code-azurecli[main](../../../cli_scripts/redis-cache/cache-keys-ports/cache-keys-ports.sh "Azure Azure Cache for Redis")]


## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja beolvasni az állomásnevet, a kulcsok és a egy Redis-példányt az Azure gyorsítótár-porton. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az redis show](https://docs.microsoft.com/cli/azure/redis#az_redis_show) | Kérje le az Azure Cache Redis-példány adatait. |
| [az redis list-keys](https://docs.microsoft.com/cli/azure/redis#az_redis_list_keys) | Hozzáférési kulcsok lekérése az Azure Cache a Redis-példányt. |


## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További Azure Cache a Redis CLI-példaszkripteket megtalálható a [Azure Cache Redis dokumentációt](../cli-samples.md).