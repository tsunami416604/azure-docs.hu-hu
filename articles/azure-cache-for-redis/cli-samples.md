---
title: Azure CLI Azure-gyorsítótár Redis-mintákhoz
description: 'Azure CLI-minták az Azure Cache for Redis számára: Gyorsítótár létrehozása, gyorsítótár törlése, gyorsítótár részleteinek beolvasása, állomásnév, portok és kulcsok, webalkalmazás csatlakoztatása.'
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 04/14/2017
ms.openlocfilehash: c43e23b4bf46258cc91b06a0912d03e85a5c7a14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75411348"
---
# <a name="azure-cli-samples-for-azure-cache-for-redis"></a>Azure CLI-minták az Azure Cache for Redis-hez

A következő táblázat az Azure CLI használatával létrehozott bash parancsfájlokra mutató hivatkozásokat tartalmaz.

| | |
|---|---|
|**Gyorsítótár létrehozása**||
| [Gyorsítótár létrehozása](./scripts/create-cache.md) | Létrehoz egy erőforráscsoportot és egy alapszintű Azure-gyorsítótárat a Redis számára. |
| [Prémium szintű gyorsítótár létrehozása fürtözéssel](./scripts/create-premium-cache-cluster.md) | Erőforráscsoportot és prémium szintű gyorsítótárat hoz létre fürtözés engedélyezésével.|
| [Gyorsítótár részleteinek beolvasása](./scripts/show-cache.md) | A Redis-példány Azure-gyorsítótárának részletei, beleértve a kiépítési állapotot is. |
| [Az állomásnév, a portok és a kulcsok beszerezni](./scripts/cache-keys-ports.md) | Lekéri a állomásnevet, portokat és kulcsokat egy Azure-gyorsítótár redis-példányhoz. |
|**Webalkalmazás és gyorsítótár**||
| [Webalkalmazás csatlakoztatása a Redis Azure-gyorsítótárához](./../app-service/scripts/cli-connect-to-redis.md) | Létrehoz egy Azure-webalkalmazást és egy Azure-gyorsítótárat a Redis számára, majd hozzáadja a redis-kapcsolat részleteit az alkalmazásbeállításokhoz. |
|**Gyorsítótár törlése**||
| [Gyorsítótár törlése](./scripts/delete-cache.md) | Egy Azure-gyorsítótár törlése a Redis-példányhoz  |
| | |

Az Azure CLI-ről az [Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) és az Azure [CLI – első lépések](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)című témakörben talál további információt.
