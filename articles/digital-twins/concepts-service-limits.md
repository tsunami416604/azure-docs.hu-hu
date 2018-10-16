---
title: Az Azure digitális Twins nyilvános előzetes verziójú szolgáltatásokra vonatkozó korlátozások |} A Microsoft Docs
description: Ismertetése az Azure digitális Twins nyilvános előzetes verziójú szolgáltatásokra vonatkozó korlátozások
author: dwalthermsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: dwalthermsft
ms.openlocfilehash: aa5f6053bf1c98d2b84c02617da30f5d856ed3fc
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324111"
---
# <a name="public-preview-service-limits"></a>Nyilvános előzetes verziójú szolgáltatásokra vonatkozó korlátozások

A nyilvános előzetes verzióban az Azure digitális Twins lesz ideiglenes előfizetés, a példány- és sebességkorlátok, amelyek az alábbiakban tekintheti át.

Ezek a korlátozások létezik, így egyszerűsítve az új szolgáltatást és a számos funkciót.

> [!NOTE]
> Ezek a korlátok fogja növelni, illetve által általánosan elérhető (GA) eltávolítva.

## <a name="per-subscription-limits"></a>Előfizetésenként korlátok

A nyilvános előzetes verzióban minden egyes Azure-előfizetés létrehozása, vagy is pontosan egy Azure digitális Twins-példány fut egyszerre.

> [!TIP]
> Törli a példány lehetővé teszi, hogy hozzon létre egy újat.

## <a name="per-instance-limits"></a>Példány korlátok

Ezután minden egyes Azure digitális Twins példány veheti fel:

- Egy `IoTHub` erőforrás
- Egy `EventHub` eseménytípus DeviceMessage végpontja
- Legfeljebb három `EventHub`, `ServiceBus`, vagy `EventGrid` esemény típusú végpontok `SensorChange`, `SpaceChange`, `TopologyOperation`, vagy `UdfCustom`

## <a name="management-api-limits"></a>Felügyeleti API-korlátok

A kérelem sebességhatárok a felügyeleti API-hoz a következők:

- 100 vonatkozó kérelmek másodpercenkénti száma a felügyeleti API
- Egyetlen felügyeleti API-lekérdezést adhat vissza legfeljebb 1000 objektumot

> [!IMPORTANT]
> Ha túllépi a 1000 objektumkorlát, hibaüzenetet kap, és egyszerűsítse a lekérdezést kell.

## <a name="udf-rate-limits"></a>Sebességhatárok UDF-ben

Az alábbi korlátozások beállítása az Azure digitális Twins-példány összes felhasználó által definiált függvény hívások teljes száma:

- 400 client library hívások / másodperc
- 100 SendNotification hívások / másodperc

> [!NOTE]
> A következő műveleteket okozhat a alkalmazni ideiglenesen további sebességhatárok:
> - Topológia objektum metaadatait módosítások
> - Az UDF definíciófrissítéseket.
> - Eszközök telemetriai adatokat küldenek az első alkalommal

## <a name="device-telemetry-limits"></a>Telemetria eszközkorlátok

A korlátok alábbi cap üzenetek teljes száma az eszközök küldheti az Azure digitális Twins-példány:

- 100 üzenetek / másodperc

## <a name="next-steps"></a>További lépések

Próbálja ki az Azure digitális Twins minta, nyissa meg [rövid útmutatóban elérhető teremkeresés](./quickstart-view-occupancy-dotnet.md).