---
title: Azure cache kezelése az Azure CLI-vel való Redis
description: 'Azure CLI-minták az Azure cache Redis való kezeléséhez: gyorsítótár létrehozása, gyorsítótár törlése, gyorsítótár részleteinek, állomásnév, portok és kulcsok összekapcsolása, webalkalmazások csatlakoztatása.'
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 04/14/2017
ms.openlocfilehash: 345fd0272e0cfe9b4d7329f004e628ea7820c2d9
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87421240"
---
# <a name="manage-azure-cache-for-redis-with-azure-cli"></a>Azure cache kezelése az Azure CLI-vel való Redis

A következő táblázat az Azure CLI használatával létrehozott bash parancsfájlokra mutató hivatkozásokat tartalmaz.

| Gyorsítótár létrehozása | Description |
| ------------ | ----------- |
| [Gyorsítótár létrehozása](./scripts/create-cache.md) | Létrehoz egy erőforráscsoportot és egy alapszintű Azure cache-gyorsítótárat a Redis számára. |
| [Prémium szintű gyorsítótár létrehozása fürtözéssel](./scripts/create-premium-cache-cluster.md) | Létrehoz egy erőforráscsoportot és egy prémium szintű gyorsítótárat, amely lehetővé teszi a fürtözést.|
| [Gyorsítótár adatainak beolvasása](./scripts/show-cache.md) | Beolvassa a Redis-példányhoz tartozó Azure cache adatait, beleértve a létesítési állapotot is. |
| [Állomásnév, portok és kulcsok lekérése](./scripts/cache-keys-ports.md) | Lekéri az Redis-példányhoz tartozó Azure-gyorsítótár állomásneve, portjai és kulcsai. |
|**Webalkalmazás és gyorsítótár**| **Leírás**|
| [Webalkalmazás összekötése egy Azure cache-Redis](./../app-service/scripts/cli-connect-to-redis.md) | Létrehoz egy Azure-webalkalmazást és egy Azure-gyorsítótárat a Redis, majd hozzáadja a Redis kapcsolati adatait az alkalmazás beállításaihoz. |
|**Gyorsítótár törlése**| **Leírás** |
| [Gyorsítótár törlése](./scripts/delete-cache.md) | Egy Azure cache törlése a Redis-példányhoz  |

Az Azure CLI-vel kapcsolatos további információkért lásd: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) és az [Azure CLI első lépései](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).
