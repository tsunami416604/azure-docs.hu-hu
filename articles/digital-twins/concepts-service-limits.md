---
title: Az Azure Digital Twins nyilvános előzetes verziójának szolgáltatási korlátai | Microsoft Docs
description: Ismerje meg az Azure Digital Twins nyilvános előzetes verziójának szolgáltatási korlátait.
author: dwalthermsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: dwalther
ms.openlocfilehash: d182a4aea00581b044b5734a5b7d6831f9e55c45
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638478"
---
# <a name="public-preview-service-limits"></a>A szolgáltatás nyilvános előzetes verziójának korlátozásai

A nyilvános előzetes verzióban az Azure Digital Twins a következő ideiglenes előfizetést, példányt és díjszabási korlátot jeleníti meg.

Ezek a megkötések megkönnyítik az új szolgáltatással és számos szolgáltatásával kapcsolatos ismeretek leegyszerűsítését.

> [!NOTE]
> Ezeket a korlátokat az általános elérhetőség (GA) növeli vagy eltávolítja.

## <a name="per-subscription-limits"></a>Előfizetési korlátok

A nyilvános előzetes verzióban minden egyes Azure-előfizetés egyszerre csak egy Azure digitális Twins-példányt tud létrehozni vagy futtatni.

> [!TIP]
> Ha törli a példányt, létrehozhat egy újat.

## <a name="per-instance-limits"></a>Felhasználónkénti korlátok

Az egyes Azure-beli digitális Twins-példányok pedig a következőket tehetik:

- Pontosan egy beágyazott **IoTHub** -erőforrás, amelyet a rendszer automatikusan hozott létre a szolgáltatás kiépítés során.
- Pontosan egy **EventHub** -végpont az esemény típusának **DeviceMessage**.
- Legfeljebb három **EventHub**, **ServiceBus**vagy **EventGrid** végpont, **SensorChange**, **SpaceChange**, **TopologyOperation**vagy **UdfCustom**eseménytípus.

> [!NOTE]
> A nyilvános előzetes verzióban általában a fenti Azure IoT-entitások létrehozásakor meghatározott paraméterek nem szükségesek.
> - A legfrissebb API-specifikációkat a [hencegő dokumentációban találja](./how-to-use-swagger.md) .

## <a name="azure-digital-twins-management-api-limits"></a>Azure digitális Twins felügyeleti API-korlátok

Az Azure Digital Twins felügyeleti API-ra vonatkozó kérelmek díjszabása a következő:

- 100 másodpercenkénti kérelmek az Azure digitális Twins felügyeleti API-hoz.
- Akár 1 000 objektum, amelyet egyetlen Azure digitális ikrek felügyeleti API-lekérdezése adott vissza.

> [!IMPORTANT]
> Ha túllépi az 1 000-Object korlátot, hibaüzenetet kap, és egyszerűsíteni kell a lekérdezést.

## <a name="user-defined-functions-rate-limits"></a>Felhasználó által definiált függvények díjszabási korlátai

A következő korlátok határozzák meg az Azure Digital Twins-példányon végrehajtott összes felhasználó által megadott függvényhívás teljes számát:

- 400 ügyféloldali függvénytár-hívások másodpercenként
- 100 **SendNotification** -hívások másodpercenként

> [!NOTE]
> A következő műveletek a további díjszabási korlátokat is okozhatják ideiglenesen:
> - A topológiai objektum metaadatainak módosításai
> - A felhasználó által definiált függvény definíciójának frissítései
> - Azok az eszközök, amelyek első alkalommal küldenek telemetria

## <a name="device-telemetry-limits"></a>Eszközök telemetria korlátai

Az alábbi korlátok az eszközök által az Azure Digital Twins-példányba küldött összes üzenet teljes száma.

- 100 üzenet másodpercenként

## <a name="next-steps"></a>További lépések

- Egy Azure-beli digitális Twins-minta kipróbálásához lépjen a gyors üzembe helyezési lehetőségre, [és keresse meg az elérhető szobákat](./quickstart-view-occupancy-dotnet.md).