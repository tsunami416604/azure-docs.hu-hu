---
title: Az Azure digitális Twins nyilvános előzetes verziójú szolgáltatásokra vonatkozó korlátozások |} A Microsoft Docs
description: Megismerheti az Azure digitális Twins nyilvános előzetes verziójú szolgáltatásokra vonatkozó korlátozások
author: dwalthermsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: dwalthermsft
ms.openlocfilehash: 86ae75118dd1311ea2ae92fb718fe4c58b8e5673
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2018
ms.locfileid: "50961755"
---
# <a name="public-preview-service-limits"></a>A szolgáltatás nyilvános előzetes verziójának korlátozásai

A nyilvános előzetes verzióban az Azure digitális Twins rendelkezik, az alábbi ideiglenes előfizetéssel, a példány és a sebességhatár beállításával.

Ezek a korlátozások létezik, így egyszerűsítve az új szolgáltatást és a számos funkciót.

> [!NOTE]
> Ezek a korlátok fog növelhető vagy általánosan (elérhetővé tétel GA) funkcióval.

## <a name="per-subscription-limits"></a>Előfizetésenként korlátok

A nyilvános előzetes során minden Azure-előfizetés létrehozása, vagy egyszerre csak egy Azure digitális Twins példány futtatható.

> [!TIP]
> Ha törli a példányt, létrehozhat egy újat.

## <a name="per-instance-limits"></a>Példány korlátok

Ezután minden egyes Azure digitális Twins példány veheti fel:

- Egy **IoTHub** erőforrás.
- Egy **EventHub** esemény típusú végpont **DeviceMessage**.
- Legfeljebb három **EventHub**, **ServiceBus**, vagy **EventGrid** az esemény típusú végpontok **SensorChange**, **SpaceChange** , **TopologyOperation**, vagy **UdfCustom**.

## <a name="management-api-limits"></a>Felügyeleti API-korlátok

A kérelem sebességhatárok a felügyeleti API-hoz a következők:

- 100 vonatkozó kérelmek másodpercenkénti száma a felügyeleti API-hoz.
- Egyetlen felügyeleti API-lekérdezés által visszaadott legfeljebb 1000 objektumot. 

> [!IMPORTANT]
> Ha 1000-objektum korlátot túllépi, hibaüzenetet kap, és a lekérdezés egyszerűsítenie kell.

## <a name="udf-rate-limits"></a>Sebességhatárok UDF-ben

Az alábbi korlátozások beállítása az Azure digitális Twins-példány összes felhasználó által definiált függvény hívások teljes száma:

- 400 client library hívások / másodperc
- 100 **SendNotification** hívások / másodperc

> [!NOTE]
> Előfordulhat, hogy a következő műveleteket a alkalmazni ideiglenesen további sebességhatárok:
> - A topológia objektum metaadatainak szerkesztése
> - Az UDF-definíció végzett frissítések
> - Az első alkalommal telemetriai adatokat küldő eszközök

## <a name="device-telemetry-limits"></a>Telemetria eszközkorlátok

Az alábbi korlátozások költségplafont, az eszközök küldhet az Azure digitális Twins-példány összes üzenetek teljes száma:

- 100 üzenetek / másodperc

## <a name="next-steps"></a>További lépések

Próbálja ki az Azure digitális Twins minta, nyissa meg [rövid útmutatóban elérhető teremkeresés](./quickstart-view-occupancy-dotnet.md).