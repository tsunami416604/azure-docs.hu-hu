---
title: fájlbefoglalás
description: fájlbefoglalás
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 11/05/2019
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: a737e130d616a67bab28c7c96c0372216a6707af
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "73720334"
---
### <a name="retrieve-host-name-ports-and-access-keys-from-the-azure-portal"></a>Állomásnév, portok és hozzáférési kulcsok beolvasása a Azure Portal

Ahhoz, hogy Redis-példányhoz kapcsolódjon egy Azure cache-hez, a gyorsítótár-ügyfeleknek az állomásnévre, a portokra és a gyorsítótár kulcsára van szükségük. Egyes ügyfelek különböző neveken hivatkozhatnak ezekre az elemekre. Az állomásnév, a portok és a kulcsok beszerezhetők a [Azure Portalból](https://portal.azure.com).

- A hozzáférési kulcsok lekéréséhez a gyorsítótár bal oldali navigációs sávján válassza a **hozzáférési kulcsok**elemet. 
  
  ![Azure cache a Redis kulcsaihoz](media/redis-cache-access-keys/redis-cache-keys.png)

- Az állomásnév és a portok beszerzéséhez a gyorsítótár bal oldali navigációs sávján válassza a **Tulajdonságok**lehetőséget. Az állomásnév a Form * \<DNS name> . Redis.cache.Windows.net*.

  ![Azure cache a Redis tulajdonságaihoz](media/redis-cache-access-keys/redis-cache-hostname-ports.png)

