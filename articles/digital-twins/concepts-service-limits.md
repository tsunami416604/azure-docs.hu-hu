---
title: Nyilvános előzetes verziós szolgáltatási korlátok - Azure Digital Twins | Microsoft dokumentumok
description: Ismerje meg az Azure Digital Twins nyilvános előzetes verziójú szolgáltatásra, előfizetésre, példányra és díjkorlátra vonatkozó korlátjait.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 5e323d8faa19ceb0712aa6183df17740ce2a0a1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370379"
---
# <a name="public-preview-service-limits"></a>A szolgáltatás nyilvános előzetes verziójának korlátozásai

[!INCLUDE [digital-twins-preview-limit-alert](../../includes/digital-twins-preview-limit-alert.md)]

A nyilvános előzetes verzió során az Azure Digital Twins a következő ideiglenes előfizetéssel, például és díjkorlátokkal rendelkezik a meglévő ügyfelek számára. Ezek a korlátok azért léteznek, hogy egyszerűsítsék az új szolgáltatás és számos szolgáltatásának megismerését, és az általános rendelkezésre állás (GA) növeli vagy eltávolítja őket.

## <a name="per-subscription-limits"></a>Előfizetésenkénti korlátok

A nyilvános előzetes verzió során minden Azure-előfizetés egyszerre csak egy Azure Digital Twins-példányt hozhat létre vagy futtathat. Ha törli a példányt, létrehozhat egy újat.

## <a name="per-instance-limits"></a>Példányonkénti korlátok

Viszont minden Azure Digital Twins példány rendelkezhet:

- Pontosan egy beágyazott **IoTHub-erőforrás,** amely automatikusan jön létre a szolgáltatás kiépítése során.
- Pontosan egy **EventHub-végpont** a **DeviceMessage**eseménytípushoz.
- Legfeljebb három **EventHub**, **ServiceBus**vagy **EventGrid** végpont a **SensorChange**, **SpaceChange**, **TopologyOperation**vagy **UdfCustom**eseménytípusból.

> [!NOTE]
> A fenti Azure IoT-entitások létrehozása során általában definiált paraméterek nem szükségesek a nyilvános előzetes verzió során.
> - Tekintse meg a [Swagger referencia dokumentációját](./how-to-use-swagger.md) a legújabb API-specifikációk.

## <a name="azure-digital-twins-management-api-limits"></a>Az Azure Digital Twins Management API korlátai

Az Azure Digital Twins Management API kérelemsebesség-korlátai a következők:

- Másodpercenként 100 kérelem az Azure Digital Twins Management API-hoz.
- Egyetlen Azure Digital Twins Management API-lekérdezés által visszaadott legfeljebb 1000 objektum.

> [!IMPORTANT]
> Ha túllépi az 1000 objektumra vonatkozó korlátot, hibaüzenetet kap, és egyszerűsítenie kell a lekérdezést.

## <a name="user-defined-functions-rate-limits"></a>Felhasználó által definiált függvények sebességkorlátai

A következő korlátok határozzák meg az Azure Digital Twins-példányra irányuló, felhasználó által definiált függvényhívások teljes számát:

- 400 ügyfélkönyvtár-hívás másodpercenként
- 100 **SendNotification** hívások másodpercenként

> [!NOTE]
> A következő műveletek miatt ideiglenesen további díjkorlátok at alkalmazhatnak:
> - A topológiaobjektum metaadatainak szerkesztése
> - A felhasználó által definiált függvénydefiníció frissítései
> - Telemetriai adatokat első alkalommal küldő eszközök

## <a name="device-telemetry-limits"></a>Eszköztelemetriai korlátok

A következő korlátok korlátozzák az eszközök által az Azure Digital Twins-példánynak küldhető összes üzenet teljes számát:

- 100 üzenet másodpercenként az összes eszközön
-    Eszközönként 25 üzenet másodpercenként

## <a name="next-steps"></a>További lépések

- Az Azure Digital Twins-minta kipróbálásához keresse meg [a rövid útmutatót, és keresse meg az elérhető szobákat.](./quickstart-view-occupancy-dotnet.md)
