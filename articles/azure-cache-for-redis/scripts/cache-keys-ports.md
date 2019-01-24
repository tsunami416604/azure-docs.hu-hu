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
ms.openlocfilehash: f9a963ec81b78cfcc6ded7d8f35f4066f931e53e
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54847316"
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
| [az redis list-keys](https://docs.microsoft.com/cli/azure/redis#az_redis_list_keys) | Hozzáférési kulcsok lekérése az Azure Cache a Redis-példányt. |


## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információért lásd az [Azure CLI dokumentációját](https://docs.microsoft.com/cli/azure).

További Azure Cache a Redis CLI-példaszkripteket megtalálható a [Azure Cache Redis dokumentációt](../cli-samples.md).
