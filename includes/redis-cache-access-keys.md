---
title: fájl belefoglalása
description: fájl belefoglalása
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 11/05/2019
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: a737e130d616a67bab28c7c96c0372216a6707af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "73720334"
---
### <a name="retrieve-host-name-ports-and-access-keys-from-the-azure-portal"></a>Állomásnév, portok és hozzáférési kulcsok beolvasása az Azure Portalról

A Redis-példány Azure-gyorsítótárához való csatlakozáshoz a gyorsítótár-ügyfeleknek szükségük van az állomásnévre, a portokra és a gyorsítótár kulcsára. Egyes ügyfelek különböző neveken hivatkozhatnak ezekre az elemekre. Az állomásnevét, portjait és kulcsait az [Azure Portalon](https://portal.azure.com)szerezheti be.

- A hozzáférési kulcsok beolvasásához a rejtett navigációt a gyorsítótárbal való navigáláskor válassza az **Access-kulcsok**lehetőséget. 
  
  ![Azure-gyorsítótár a Redis-kulcsokhoz](media/redis-cache-access-keys/redis-cache-keys.png)

- Az állomásnév és a portok leolvasásához válassza a rejtett navigációt a gyorsítótár bal oldali navigációs sávján, és válassza **a Tulajdonságok lehetőséget.** Az állomásnév * \<>.redis.cache.windows.net típusú DNS-név.*

  ![Azure-gyorsítótár a Redis-tulajdonságokhoz](media/redis-cache-access-keys/redis-cache-hostname-ports.png)

