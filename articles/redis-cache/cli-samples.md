---
title: "Az Azure CLI Redis gyorsítótár minták |} Microsoft Docs"
description: "Az Azure CLI-példák Azure Redis Cache."
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 8d2de145-50c0-4f76-bf8f-fdf679f03698
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: azurecli
ms.topic: article
ms.date: 04/14/2017
ms.author: sdanie
ms.openlocfilehash: a0f3b294f2a655a5ff891d4fd1be9137080349a6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cli-samples-for-azure-redis-cache"></a>Az Azure CLI-példák Azure Redis gyorsítótár

A következő táblázat a bash parancsfájlok az Azure parancssori felület használatával készített mutató hivatkozásokat tartalmaz.

| | |
|---|---|
|**Gyorsítótár létrehozása**||
| [A gyorsítótár létrehozása](./scripts/create-cache.md) | Létrehoz egy erőforráscsoport és az Azure Redis Cache alapvető réteget. |
| [Prémium szintű gyorsítótár létrehozásához fürtözési](./scripts/create-premium-cache-cluster.md) | Hoz létre egy erőforráscsoportot és a premium szint gyorsítótár fürtözési engedélyezve van.|
| [Gyorsítótár-részletek](./scripts/show-cache.md) | Lekérdezi az Azure Redis Cache példány, beleértve a kiépítési állapot részleteit. |
| [Az állomásnév, portokat és kulcsok beszerzése](./scripts/cache-keys-ports.md) | Lekérdezi az állomásnév, portokat és a kulcsok Azure Redis Cache példányt. |
|**A webalkalmazás és a gyorsítótár**||
| [Egy webes alkalmazás csatlakoztatása a redis gyorsítótár](./../app-service/scripts/app-service-cli-app-service-redis.md) | Azure-webalkalmazás és a redis gyorsítótár hoz létre, majd hozzáadja a redis kapcsolódási adatait az alkalmazás beállításaiban. |
|**Gyorsítótár törlése**||
| [A gyorsítótár törlése](./scripts/delete-cache.md) | Törli az Azure Redis Cache példányt  |
| | |

Azure CLI 2.0 kapcsolatos további információkért lásd: [Azure CLI 2.0 telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) és [Ismerkedés az Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli).