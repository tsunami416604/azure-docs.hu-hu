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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "73720334"
---
### <a name="retrieve-host-name-ports-and-access-keys-from-the-azure-portal"></a>Állomásnév, portok és hozzáférési kulcsok beolvasása a Azure Portal

Ahhoz, hogy Redis-példányhoz kapcsolódjon egy Azure cache-hez, a gyorsítótár-ügyfeleknek az állomásnévre, a portokra és a gyorsítótár kulcsára van szükségük. Egyes ügyfelek különböző neveken hivatkozhatnak ezekre az elemekre. Az állomásnév, a portok és a kulcsok beszerezhetők a [Azure Portalból](https://portal.azure.com).

- A hozzáférési kulcsok lekéréséhez a gyorsítótár bal oldali navigációs sávján válassza a **hozzáférési kulcsok**elemet. 
  
  ![Azure cache a Redis kulcsaihoz](media/redis-cache-access-keys/redis-cache-keys.png)

- Az állomásnév és a portok beszerzéséhez a gyorsítótár bal oldali navigációs sávján válassza a **Tulajdonságok**lehetőséget. Az állomásnév az űrlap * \<DNS-neve>. Redis.cache.Windows.net*.

  ![Azure cache a Redis tulajdonságaihoz](media/redis-cache-access-keys/redis-cache-hostname-ports.png)

