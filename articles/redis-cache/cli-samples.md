---
title: Parancssori felület a Redis cache-minták az Azure |} A Microsoft Docs
description: Az Azure CLI-minták az Azure Redis Cache.
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 8d2de145-50c0-4f76-bf8f-fdf679f03698
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.date: 04/14/2017
ms.author: wesmc
ms.openlocfilehash: 01773e1ec24e6ab7d1899df6774230b7ce5b5676
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46957897"
---
# <a name="azure-cli-samples-for-azure-redis-cache"></a>Az Azure CLI-minták az Azure Redis Cache

A következő táblázat az Azure CLI használatával létrehozott bash parancsfájlokra mutató hivatkozásokat tartalmaz.

| | |
|---|---|
|**Gyorsítótár létrehozása**||
| [Gyorsítótár létrehozása](./scripts/create-cache.md) | Létrehoz egy erőforráscsoportot és a egy alapszintű csomag az Azure Redis Cache. |
| [Hozzon létre egy prémium szintű gyorsítótár klaszterezéssel](./scripts/create-premium-cache-cluster.md) | Létrehoz egy erőforráscsoportot és a prémium szintű gyorsítótár fürtözés engedélyezve van.|
| [Gyorsítótár-részletek](./scripts/show-cache.md) | Lekérdezi az Azure Redis Cache-példányból, beleértve a kiépítési állapot részleteit. |
| [A gazdagépnév, portok és kulcsok beolvasása](./scripts/cache-keys-ports.md) | A gazdagépnév, portok és kulcsok az Azure Redis Cache-példány beolvasása. |
|**Webalkalmazás és a gyorsítótár**||
| [Webalkalmazás csatlakoztatása redis Cache-gyorsítótárhoz](./../app-service/scripts/app-service-cli-app-service-redis.md) | Létrehoz egy Azure-webalkalmazást és egy redis Cache gyorsítótárhoz, majd hozzáadja az alkalmazásbeállításokat a redis-kapcsolat adatai. |
|**Gyorsítótár törlése**||
| [A gyorsítótár törlése](./scripts/delete-cache.md) | Az Azure Redis Cache-példány törlése  |
| | |

Az Azure CLI-vel kapcsolatos további információkért lásd: [az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) és [Azure CLI használatának első lépései](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).
