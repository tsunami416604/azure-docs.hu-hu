---
title: Azure CLI Azure cache Redis-mintákhoz
description: 'Azure CLI-minták az Azure cache-hez a Redis-hez: gyorsítótár létrehozása, gyorsítótár törlése, gyorsítótár adatainak beolvasása, állomásnév, portok és kulcsok, webalkalmazás csatlakoztatása.'
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 04/14/2017
ms.openlocfilehash: c43e23b4bf46258cc91b06a0912d03e85a5c7a14
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75411348"
---
# <a name="azure-cli-samples-for-azure-cache-for-redis"></a>Azure CLI-minták az Azure cache Redis-hez

A következő táblázat az Azure CLI használatával létrehozott bash parancsfájlokra mutató hivatkozásokat tartalmaz.

| | |
|---|---|
|**Gyorsítótár létrehozása**||
| [Gyorsítótár létrehozása](./scripts/create-cache.md) | Létrehoz egy erőforráscsoportot és egy alapszintű Azure cache-gyorsítótárat a Redis számára. |
| [Prémium szintű gyorsítótár létrehozása fürtözéssel](./scripts/create-premium-cache-cluster.md) | Létrehoz egy erőforráscsoportot és egy prémium szintű gyorsítótárat, amely lehetővé teszi a fürtözést.|
| [Gyorsítótár adatainak beolvasása](./scripts/show-cache.md) | Beolvassa a Redis-példányhoz tartozó Azure cache adatait, beleértve a létesítési állapotot is. |
| [Állomásnév, portok és kulcsok lekérése](./scripts/cache-keys-ports.md) | Lekéri az Redis-példányhoz tartozó Azure-gyorsítótár állomásneve, portjai és kulcsai. |
|**Webalkalmazás és gyorsítótár**||
| [Webalkalmazás összekötése egy Azure cache-Redis](./../app-service/scripts/cli-connect-to-redis.md) | Létrehoz egy Azure-webalkalmazást és egy Azure-gyorsítótárat a Redis, majd hozzáadja a Redis kapcsolati adatait az alkalmazás beállításaihoz. |
|**Gyorsítótár törlése**||
| [Gyorsítótár törlése](./scripts/delete-cache.md) | Egy Azure cache törlése a Redis-példányhoz  |
| | |

Az Azure CLI-vel kapcsolatos további információkért lásd: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) és az [Azure CLI első lépései](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).
