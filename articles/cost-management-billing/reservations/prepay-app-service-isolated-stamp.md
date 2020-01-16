---
title: Mentés Azure App Service számára fenntartott kapacitással
description: Megtudhatja, hogyan csökkentheti az Azure App Service-beli izolált bélyegek díjait fenntartott kapacitással.
services: billing
author: yashesvi
manager: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: banders
ms.openlocfilehash: 8161b169e8801fae4a9aa81290cf29ec28abf858
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75995883"
---
# <a name="save-costs-for-azure-app-service-isolated-stamp-fee-with-reserved-capacity"></a>Az Azure App Service-beli izolált bélyegek díjainak csökkentése fenntartott kapacitással

Pénzt takaríthat meg az Azure App Service-beli izolált bélyegek díjából, ha három évre előre kötelezettséget vállal egy bélyeghasználat foglalására. Fenntartott kapacitás izolált bélyegdíjhoz történő vásárlásához ki kell választania a bélyeg üzembe helyezésének helyeként szolgáló Azure-régiót és a megvásárolni kívánt bélyegek számát.

Foglalás vásárlásakor a foglalás jellemzőivel egyező izolált bélyegdíj használatáért a továbbiakban nem használatalapú díjakat kell fizetnie. A rendszer automatikusan alkalmazza a foglalást a fenntartott kapacitás hatókörével és régiójával egyező izolált bélyegekre. Az izolált bélyegekhez nem kell foglalást rendelni. A foglalás nem érvényes a feldolgozókra, így a bélyeghez társított egyéb erőforrásokért külön kell fizetnie.

A fenntartott kapacitás lejáratakor az izolált bélyegek tovább futnak, azonban használatalapú díjjal lesznek számlázva. A foglalások nem újulnak meg automatikusan.

## <a name="determine-the-right-reservation-to-purchase"></a>A megfelelő foglalás kiválasztása

A foglalás megvásárlásával kötelezettséget vállal a lefoglalt mennyiségek használatára a következő három évre. Tekintse meg a használati adatokat annak meghatározásához, hány olyan izolált bélyeggel rendelkezik az App Service-ben, amelyeket konzisztensen használ, és esetlegesen használni fog a jövőben.

Emellett tekintse át, hogy az izolált bélyegek használata hogyan jelenik meg a linuxos vagy windowsos fogyasztásmérőkön.

- Alapértelmezés szerint egy üres izolált bélyeg használata a windowsos bélyeg-fogyasztásmérőn jelenik meg. Ha például nincsenek üzembe helyezve feldolgozók. Ha a bélyegen üzembe vannak helyezve windowsos feldolgozók, akkor a bélyeghasználat továbbra is az adott fogyasztásmérőn jelenik meg.
- Ha üzembe helyez egy Linux-feldolgozót, akkor a mérő a Linux-tranzakciómérőszámra vált át.
- Ha linuxos és windowsos feldolgozók is üzembe vannak helyezve, a bélyeghasználat a windowsos fogyasztásmérőn jelenik meg.

A bélyeg-fogyasztásmérő tehát váltani tud a Windows és a Linux rendszer között a bélyeg élettartama során.

Ha egy vagy több windowsos feldolgozóval rendelkezik a bélyegen, akkor Windows rendszerű bélyegfoglalásokat vásároljon. Linux rendszerű bélyegfoglalást csak akkor érdemes vásárolnia, ha a bélyegen _kizárólag_ linuxos feldolgozókat kíván használni.

## <a name="buy-isolated-stamp-reserved-capacity"></a>Fenntartott kapacitás vásárlása izolált bélyeghez

Izolált bélyeghez az [Azure Portalon](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D) vásárolhat fenntartott kapacitást. A foglalásért fizethet [előre vagy havi részletekben](monthly-payments-reservations.md). Fenntartott kapacitás vásárlásához legalább egy nagyvállalati vagy egy használatalapú fizetéses, egyéni előfizetés tulajdonosi szerepkörével kell rendelkeznie.

- Nagyvállalati előfizetések esetében engedélyezni kell a **Fenntartott példányok hozzáadása** beállítást az [EA Portalon](https://ea.azure.com/). Illetve ha ez a beállítás le van tiltva, akkor Önnek EA-rendszergazdának kell lennie.
- A felhőszolgáltatói (CSP-) program esetében csak a felügyeleti ügynökök és az értékesítési ügynökök vásárolhatnak fenntartott SQL Data Warehouse-kapacitást.

**A vásárláshoz:**

1. Nyissa meg az [Azure Portalt](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D).
1. Válasszon előfizetést. Az **Előfizetés** listából válassza ki a fenntartott kapacitás kifizetéséhez használt előfizetést. Az előfizetésnél megadott fizetési mód szerint lesznek számlázva a fenntartott példány költségei. Az előfizetés típusának nagyvállalati szerződésnek kell lennie (ajánlati számok: MS-AZR-0017P vagy MS-AZR-0148P) vagy utólagos elszámolású (ajánlati számok: MS-AZR-0003P vagy MS-AZR-0023P) vagy CSP-előfizetés.
    - Nagyvállalati előfizetésnél a díjak a regisztrációhoz tartozó keretek egyenlegeiből lesznek levonva, illetve túlhasználatként lesznek számlázva.
    - Használatalapú fizetéses előfizetéseknél a díjakat az előfizetéshez tartozó hitelkártyára terheljük vagy a számlafizetési módnak megfelelően számlázzuk.
1. Az előfizetés hatókörének kiválasztásához válasszon ki egy **Hatókört**.
    - **Egyetlen erőforráscsoport hatókör** – A foglalási kedvezményt csak a kiválasztott erőforráscsoportban található egyező erőforrásokra alkalmazza.
    - **Egy előfizetésre kiterjedő hatókör** – A foglalási kedvezményt a kiválasztott előfizetésben található, egyező erőforrásokra alkalmazza.
    - **Megosztott hatókör** – A foglalási kedvezményt a számlázási környezet jogosult előfizetéseiben található, egyező erőforrásokra alkalmazza. A Nagyvállalati Szerződéssel rendelkező ügyfelek esetében a számlázási környezet a regisztráció. A használatalapú díjas, egyéni előfizetések esetében a számlázási hatókör a fiókadminisztrátor által létrehozott, jogosult előfizetéseket foglalja magában.
1. A fenntartott kapacitás által fedezett Azure-régió kiválasztásához válasszon ki egy **Régiót**, és adja hozzá a foglalást a kosárhoz.
1. Válassza ki az izolált csomagok egyik típusát, majd kattintson a **Kiválasztás** lehetőségre.  
    ![Példa ](./media/prepay-app-service-isolated-stamp/app-service-isolated-stamp-select.png)
1. Adja meg az App Service-beli izolált bélyegek lefoglalni kívánt mennyiségét. Ha például hármat ad meg mennyiségként, akkor három lefoglalt bélyeget kap a régióban. Kattintson a **Tovább gombra: felülvizsgálat + vásárlás**.
1. Tekintse át a beállításokat, és kattintson a **Vásárlás** lehetőségre.

A vásárlás után a [Foglalások](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) terület megnyitásával bármikor megtekintheti és monitorozhatja a vásárlás állapotát.

## <a name="cancel-exchange-or-refund-reservations"></a>Foglalások lemondása, cseréje vagy visszatérítése

Bizonyos korlátozásokkal lehetősége van a foglalások lemondására, cseréjére és visszatérítésére. További információkért lásd: [Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai](exchange-and-refund-azure-reservations.md).

## <a name="discount-application-shown-in-usage-data"></a>A kedvezmény alkalmazása a használati adatokban

A használati adatok ára nulla azon használat esetében, amelyre érvényes a foglalási kedvezmény. A használati adatok megjelenítik az egyes foglalásokban található bélyegpéldányokra vonatkozó foglalási kedvezményt.

A foglalási kedvezmény a használati adatokban történő megjelenésével kapcsolatos további információkért tekintse meg [a Nagyvállalati Szerződés foglalási költségeit és használati adatait](understand-reserved-instance-usage-ea.md) ismertető cikket, ha Ön Nagyvállalati Szerződéssel rendelkező (EA-) ügyfél. Minden más esetben tekintse meg a [használatalapú fizetéses egyedi előfizetés Azure-beli foglalási használatát](understand-reserved-instance-usage.md) ismertető cikket.

## <a name="next-steps"></a>Következő lépések

- Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:
  - [Mi az az Azure Reservations?](save-compute-costs-reservations.md)
  - [Az Azure App Service-beli izolált bélyegekre érvényes foglalási kedvezmény alkalmazása](reservation-discount-app-service-isolated-stamp.md)
  - [A foglalási kihasználtság ismertetése vállalati regisztrációnál](understand-reserved-instance-usage-ea.md)
