---
title: Az Azure digitális Twins nyilvános előzetes verziójú szolgáltatásokra vonatkozó korlátozások |} A Microsoft Docs
description: Ismerje meg az Azure digitális Twins nyilvános előzetes verziójú szolgáltatásokra vonatkozó korlátozások.
author: dwalthermsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2019
ms.author: dwalthermsft
ms.openlocfilehash: 1e7b6ed7c401bed741142dfc02efc7990cf67f6e
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54118814"
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

- Pontosan egy **IoTHub** erőforrás.
- Pontosan egy **EventHub** esemény típusú végpont **DeviceMessage**.
- Legfeljebb három **EventHub**, **ServiceBus**, vagy **EventGrid** az esemény típusú végpontok **SensorChange**, **SpaceChange** , **TopologyOperation**, vagy **UdfCustom**.

> [!NOTE]
> Egyes általában a fenti Azure IoT-entitások létrehozásakor meghatározott paraméterek nem szükségesek a nyilvános előzetes verzió ideje alatt.
> - Tekintse át a [Swagger dokumentációja](./how-to-use-swagger.md) számára a legújabb API-specifikációkat.

## <a name="azure-digital-twins-management-api-limits"></a>Az Azure digitális Twins felügyeleti API-korlátok

Az Azure digitális Twins felügyeleti API a kérelem sebességhatárt a következők:

- 100 vonatkozó kérelmek másodpercenkénti száma az Azure digitális Twins felügyeleti API-hoz.
- Egyetlen Azure digitális Twins felügyeleti API-lekérdezés által visszaadott legfeljebb 1000 objektumot.

> [!IMPORTANT]
> Ha 1000-objektum korlátot túllépi, hibaüzenetet kap, és a lekérdezés egyszerűsítenie kell.

## <a name="user-defined-functions-rate-limits"></a>Felhasználó által definiált függvények sebességhatárok

Az alábbi korlátozások beállítása az Azure digitális Twins-példány összes felhasználó által definiált függvény hívások teljes száma:

- 400 client library hívások / másodperc
- 100 **SendNotification** hívások / másodperc

> [!NOTE]
> Előfordulhat, hogy a következő műveleteket a alkalmazni ideiglenesen további sebességhatárok:
> - A topológia objektum metaadatainak szerkesztése
> - A felhasználó által definiált függvény definíciója végzett frissítések
> - Az első alkalommal telemetriai adatokat küldő eszközök

## <a name="device-telemetry-limits"></a>Telemetria eszközkorlátok

Az alábbi korlátozások költségplafont, az eszközök küldhet az Azure digitális Twins-példány összes üzenetek teljes száma:

- 100 üzenetek / másodperc

## <a name="next-steps"></a>További lépések

- Próbálja ki az Azure digitális Twins minta, nyissa meg [rövid útmutatóban elérhető teremkeresés](./quickstart-view-occupancy-dotnet.md).