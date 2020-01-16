---
title: SQL Data Warehouse díjak mentése az Azure-ban fenntartott kapacitással
description: Megtudhatja, hogyan csökkentheti az SQL Data Warehouse költségeit fenntartott kapacitásokkal megtakarítás céljából.
services: billing
author: yashesvi
manager: yashar
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: banders
ms.openlocfilehash: b4069c9b18f9591e79d983a1317f00df11cf0611
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75995844"
---
# <a name="save-costs-for-sql-data-warehouse-charges-with-reserved-capacity"></a>Az SQL Data Warehouse költségeinek csökkentése fenntartott kapacitással

Pénzt takaríthat meg az Azure SQL Data Warehouse-zal, ha egy vagy három évre előre lefoglalja a cDWU-használatot. Fenntartott SQL Data Warehouse-kapacitás vásárlásához ki kell választania az Azure-régiót és az időszakot. Ezután hozzá kell adni az SQL Data Warehouse termékváltozatát a kosárhoz, majd ki kell választani a megvásárolni kívánt cDWU-egységek számát.

Foglalás vásárlásakor a foglalás attribútumainak megfelelő SQL Data Warehouse-használatra már nem használatalapú díjszabást alkalmazunk.

A foglalások nem foglalják magukban az SQL Data Warehouse-használat tárolási és hálózatkezelési díjait.

A foglalt kapacitás lejártakor az SQL Data Warehouse-példányok tovább futnak, azonban használatalapú díjszabást alkalmaznak. A foglalások nem újulnak meg automatikusan.

A díjszabással kapcsolatos információkért tekintse meg a [fenntartott SQL Data Warehouse-kapacitáshoz kapcsolódó ajánlatot](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/) ismertető cikket.

Fenntartott Azure SQL Data Warehouse-kapacitást az [Azure Portalon](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) vásárolhat. A foglalásért fizethet [előre vagy havi részletekben](monthly-payments-reservations.md). Fenntartott kapacitás vásárlása:

- Legalább egy nagyvállalati vagy használatalapú fizetéses előfizetés tulajdonosi szerepkörével kell rendelkeznie.
- Nagyvállalati előfizetések esetében engedélyezni kell a **Fenntartott példányok hozzáadása** beállítást az [EA Portalon](https://ea.azure.com/). Ha ez a beállítás le van tiltva, Önnek EA-rendszergazdának kell lennie.
- A felhőszolgáltatói (CSP-) program esetében csak a felügyeleti ügynökök és az értékesítési ügynökök vásárolhatnak fenntartott SQL Data Warehouse-kapacitást.

A nagyvállalati ügyfelek és használatalapú fizetéses ügyfelek által vásárolt foglalások díjával kapcsolatos további információkért tekintse meg [a nagyvállalati regisztráció esetén az Azure-beli foglalás használatát](understand-reserved-instance-usage-ea.md) és [a használatalapú fizetéses előfizetés esetén az Azure-beli foglalás használatát](understand-reserved-instance-usage.md) ismertető cikket.

## <a name="choose-the-right-size-before-purchase"></a>A megfelelő méret kiválasztása vásárlás előtt

Az SQL Data Warehouse-foglalás méretének az összes felhasznált számítási adattárházegységen (cDWU-n) kell alapulnia. A vásárlások 100 cDWU-s növekményekben történnek.

Tegyünk fel például, hogy a teljes SQL Data Warehouse-felhasználás DW3000c. Az egészhez szeretne fenntartott kapacitást vásárolni. Ehhez 30 egységnyi fenntartott cDWU-kapacitást kell vásárolnia.

## <a name="buy-sql-data-warehouse-reserved-capacity"></a>Fenntartott SQL Data Warehouse-kapacitás vásárlása

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Válassza a **Minden szolgáltatás** > **Reservations** lehetőséget.
3. Válasszon előfizetést. Az Előfizetés listából válassza ki a fenntartott kapacitás kifizetéséhez használt előfizetést. Az előfizetésnél megadott fizetési mód szerint lesznek számlázva a fenntartott példány költségei. Az előfizetés típusának nagyvállalati szerződésnek kell lennie (ajánlati számok: MS-AZR-0017P vagy MS-AZR-0148P) vagy utólagos elszámolású (ajánlati számok: MS-AZR-0003P vagy MS-AZR-0023P).
   - Nagyvállalati előfizetésnél a díjak a regisztrációhoz tartozó keretek egyenlegeiből lesznek levonva, illetve túlhasználatként lesznek számlázva.
   - Használatalapú fizetéses előfizetéseknél a díjakat az előfizetéshez tartozó hitelkártyára terheljük vagy a számlafizetési módnak megfelelően számlázzuk.
4. Válassza ki a hatókört. A Hatókör listából válassza ki az előfizetés hatókörét.
   - **Egyetlen erőforráscsoport hatókör** – A foglalási kedvezményt csak a kiválasztott erőforráscsoportban található egyező erőforrásokra alkalmazza.
   - **Egy előfizetésre kiterjedő hatókör** – A foglalási kedvezményt a kiválasztott előfizetésben található, egyező erőforrásokra alkalmazza.
   - **Megosztott hatókör** – A foglalási kedvezményt a számlázási környezet jogosult előfizetéseiben található, egyező erőforrásokra alkalmazza. A Nagyvállalati Szerződéssel rendelkező ügyfelek esetében a számlázási környezet a regisztráció. A használatalapú díjas, egyéni előfizetések esetében a számlázási hatókör a fiókadminisztrátor által létrehozott, jogosult előfizetéseket foglalja magában.
   - A nagyvállalati ügyfelek esetében a számlázási környezet az EA-regisztráció.
   - A használatalapú fizetéses ügyfelek esetében a megosztott hatókör a fiókadminisztrátor által létrehozott, használatalapú fizetéses előfizetéseket foglalja magában.
5. A fenntartott kapacitás által fedezett Azure-régió kiválasztásához válasszon egy régiót.
6. Válasszon ki egy mennyiséget. Adja meg a megvásárolni kívánt mennyiségű 100 adattárházegységet (cDWU-t).    
   Ha például 30-at ad meg mennyiségként, akkor 3000 fenntartott cDWU-kapacitást kap óránként.
7. A **Költségek** szakaszban tekintse át a fenntartott SQL Data Warehouse-kapacitás foglalásának költségét.
8. Válassza a **Beszerzés** lehetőséget.
9. A vásárlás állapotának megtekintéséhez válassza **A foglalás megtekintése** lehetőséget.

## <a name="cancel-exchange-or-refund-reservations"></a>Foglalások lemondása, cseréje vagy visszatérítése

Bizonyos korlátozásokkal lehetősége van a foglalások lemondására, cseréjére és visszatérítésére. További információkért lásd: [Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai](exchange-and-refund-azure-reservations.md).

A rendszer automatikusan alkalmazza a foglalási kedvezményt a fenntartott SQL Data Warehouse-kapacitás hatókörével és régiójával egyező SQL Data Warehouse-példányokra. A fenntartott SQL Data Warehouse-kapacitás hatókörét az [Azure Portal](https://portal.azure.com/), a PowerShell, a parancssori felület és az API használatával frissítheti.

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/).

## <a name="next-steps"></a>Következő lépések

- A foglalási kedvezmények Azure SQL Data Warehouse-ra történő alkalmazásával kapcsolatos további információkért lásd [a foglalási kedvezmények alkalmazását az Azure SQL Data Warehouse-ban](prepay-sql-data-warehouse-charges.md) bemutató cikket.

- Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:
  - [Mi az az Azure Reservations?](save-compute-costs-reservations.md)
  - [Az Azure Reservations kezelése](manage-reserved-vm-instance.md)
  - [Az Azure Reservations-kedvezmény ismertetése](understand-reservation-charges.md)
  - [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](understand-reserved-instance-usage.md)
  - [A foglalási kihasználtság ismertetése vállalati regisztrációnál](understand-reserved-instance-usage-ea.md)
