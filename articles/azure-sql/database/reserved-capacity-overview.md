---
title: Számítási költségek mentése fenntartott kapacitással
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Megtudhatja, hogyan vásárolhat Azure SQL Database és az SQL felügyelt példányának fenntartott kapacitását a számítási költségek mentéséhez.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 08/29/2019
ms.openlocfilehash: c02368b5713c0743cdca764275f76dda0e0926d2
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84119093"
---
# <a name="save-costs-for-resources-with-reserved-capacity---azure-sql-database--sql-managed-instance"></a>Fenntartott kapacitással rendelkező erőforrások költségeinek mentése – Azure SQL Database & SQL felügyelt példánya
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)] 

A Azure SQL Database és az SQL felügyelt példányával pénzt takaríthat meg, ha kifizeti a számítási erőforrások foglalását az utólagos elszámolású árakhoz képest. A fenntartott kapacitással a SQL Database és/vagy az SQL felügyelt példányainak egy vagy három éves időszakra vonatkozó kötelezettségvállalását kell elvégeznie, hogy a számítási költségek jelentős kedvezményt kapjanak. A fenntartott kapacitás megvásárlásához meg kell adnia az Azure-régiót, a központi telepítési típust, a teljesítménnyel kapcsolatos szintet és a kifejezést.

A foglalást nem kell hozzárendelni adott SQL Database vagy SQL felügyelt példányhoz. A már futó vagy újonnan üzembe helyezett meglévő központi telepítések automatikusan megkapják a kedvezményt. A foglalás megvásárlásával véglegesíti a számítási költségek felhasználását egy vagy három év alatt. A foglalás megvásárlása után a foglalási attribútumoknak megfelelő számítási díjakra már nem számítunk fel díjat az utólagos elszámolású díjszabásban. A foglalás nem vonatkozik a szolgáltatáshoz kapcsolódó szoftverekre, hálózatokra vagy tárolási díjakra. A foglalási időszak végén a számlázási juttatás lejár, és a SQL Database vagy az SQL felügyelt példány díját az utólagos elszámolású díjszabás szerint számoljuk el. A foglalások nem automatikus megújítást végeznek. A díjszabással kapcsolatos információkért tekintse meg a [fenntartott kapacitás ajánlatát](https://azure.microsoft.com/pricing/details/sql-database/managed/).

Fenntartott kapacitást a [Azure Portal](https://portal.azure.com)is vásárolhat. A foglalásért fizethet [előre vagy havi részletekben](../../cost-management-billing/reservations/prepare-buy-reservation.md). Fenntartott kapacitás vásárlása:

- Legalább egy Nagyvállalati vagy egyéni előfizetéshez tulajdonosi szerepkörrel kell rendelkeznie, utólagos elszámolású díjszabással.
- Nagyvállalati előfizetések esetében engedélyezni kell a **Fenntartott példányok hozzáadása** beállítást az [EA Portalon](https://ea.azure.com). Ha ez a beállítás le van tiltva, akkor az előfizetés egyik nagyvállalati rendszergazdájának kell lennie. fenntartott kapacitás.

Az ügyfelek és az utólagos elszámolású ügyfelek díjszabása a foglalások beszerzése után: az [Azure foglalási szolgáltatás használatának megismerése a nagyvállalati beléptetéshez](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) , valamint az [Azure-foglalás használatának](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)ismertetése az utólagos elszámolású előfizetéshez.

## <a name="determine-correct-size-before-purchase"></a>A megfelelő méret meghatározása a vásárlás előtt

A foglalások méretének a meglévő vagy hamarosan üzembe helyezett SQL Database vagy SQL felügyelt példány által a megadott régión belüli, illetve az azonos teljesítményszint és hardveres generáció használatával felhasznált teljes számítási mennyiség alapján kell alapulnia.

Tegyük fel például, hogy egy általános célt futtat, Gen5 – 16 virtuális mag rugalmas készletet, és két üzleti szempontból kritikus, Gen5 – 4 virtuális mag önálló adatbázist használ. Továbbá Tételezzük fel, hogy a következő hónapban szeretne üzembe helyezni egy további általános célú, Gen5 – 16 virtuális mag rugalmas készletet és egy üzleti szempontból kritikus Gen5 – 32 virtuális mag rugalmas készletet. Azt is tegyük fel, hogy tudnia kell, hogy legalább 1 évig szüksége lesz ezekre az erőforrásokra. Ebben az esetben vásárolnia kell egy 32 (2x16) virtuális mag, 1 éves foglalást az önálló adatbázisok/rugalmas készlet általános célú Gen5 és a 40 (2x4 + 32) virtuális mag 1 éves foglalás a különálló adatbázis/rugalmas készlet üzleti szempontból kritikus fontosságú – Gen5.

## <a name="buy-reserved-capacity"></a>Fenntartott kapacitás vásárlása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza a **Minden szolgáltatás** > **Reservations** lehetőséget.
3. Válassza a **Hozzáadás** lehetőséget, majd a **foglalások** ablaktáblán válassza a **SQL Database** lehetőséget a SQL Database új foglalásának megvásárlásához.
4. Töltse ki a kötelező mezőket. Meglévő SQL-adatbázisok (egyazon vagy készletezett) vagy SQL felügyelt példányok, amelyek megfelelnek a kiválasztott attribútumoknak, a fenntartott kapacitás kedvezményének beszerzéséhez. A kedvezményt megkapó SQL-adatbázisok vagy SQL felügyelt példányok tényleges száma a kiválasztott hatókörtől és mennyiségtől függ.
    ![A fenntartott kapacitás vásárlásának elküldése előtt képernyőkép](./media/reserved-capacity-overview/sql-reserved-vcores-purchase.png)

A következő táblázat a kötelező mezőket ismerteti.

| Mező      | Description|
|------------|--------------|
|Előfizetés|A kapacitás foglalásának megfizetéséhez használt előfizetés. Az előfizetéshez tartozó fizetési módot a foglalás előzetes költségei alapján számítjuk fel. Az előfizetés típusának nagyvállalati szerződésnek kell lennie (ajánlati számok: MS-AZR-0017P vagy MS-AZR-0148P), vagy az utólagos elszámolású díjszabással rendelkező egyéni szerződés (ajánlati számok: MS-AZR-0003P vagy MS-AZR-0023P). Nagyvállalati előfizetésnél a díjak a regisztrációhoz tartozó keretek egyenlegeiből lesznek levonva, illetve túlhasználatként lesznek számlázva. Az utólagos elszámolású előfizetések esetében az előfizetés díjait a bankkártya vagy a számla fizetési módja alapján számítjuk fel.|
|Hatókör       |A virtuális mag foglalási hatóköre egyetlen előfizetésre vagy több előfizetésre (megosztott hatókörre) is vonatkozhat. Ha a következőket választja: <br/><br/>**Megosztott**, a virtuális mag foglalási kedvezményt a rendszer a számlázási környezetben lévő előfizetésekben futó SQL Database vagy SQL felügyelt példányra alkalmazza. A vállalati ügyfelek esetében a közös hatókör a regisztráció, és a regisztráción belüli összes előfizetés szerepel. A használatalapú fizetéses ügyfelek esetében a megosztott hatókör a fiókadminisztrátor által létrehozott, használatalapú fizetéses előfizetéseket foglalja magában.<br/><br/>**Egyszeri előfizetés**esetén a virtuális mag foglalási kedvezményt az előfizetéshez tartozó SQL-adatbázisokra vagy SQL-felügyelt példányokra alkalmazza a rendszer. <br/><br/>**Egy erőforráscsoport**, a foglalási kedvezmény a kijelölt előfizetésben található SQL-adatbázisokra vagy SQL-felügyelt példányokra, valamint az adott előfizetéshez tartozó kiválasztott erőforráscsoporthoz lesz alkalmazva.|
|Régió      |Az Azure-régió, amelyre a kapacitás foglalása vonatkozik.|
|Központi telepítés típusa|Az az SQL-erőforrástípus, amelyre a foglalást megvásárolni szeretné.|
|Teljesítményszint|Az SQL-adatbázisok vagy a felügyelt SQL-példányok szolgáltatási szintje. |
|Kifejezés        |Egy vagy három év.|
|Mennyiség    |A kapacitás-foglalás keretében megvásárolt számítási erőforrások mennyisége. A mennyiség a kiválasztott Azure-régióban és a lefoglalt teljesítményszint virtuális mag, és a számlázási kedvezményt kapja. Ha például több SQL-adatbázist futtat vagy tervez futtatni az USA keleti régiójában lévő Gen5 16 virtuális mag teljes számítási kapacitásával, akkor az összes adatbázisra vonatkozó előny maximalizálása érdekében a 16. mennyiséget adja meg. |

1. Tekintse át a kapacitás foglalásának költségeit a costs ( **költségek** ) szakaszban.
1. Válassza a **Beszerzés** lehetőséget.
1. Válassza a **foglalás megtekintése** lehetőséget a vásárlás állapotának megtekintéséhez.

## <a name="cancel-exchange-or-refund-reservations"></a>Foglalások lemondása, cseréje vagy visszatérítése

Bizonyos korlátozásokkal lehetősége van a foglalások lemondására, cseréjére és visszatérítésére. További információkért lásd: [Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>Virtuális mag méretének rugalmassága

a virtuális mag méretének rugalmassága segít a teljesítmény-és a régión belüli vertikális felskálázásban, a fenntartott kapacitási juttatás elvesztése nélkül. A fenntartott kapacitással rugalmasan helyezheti át a gyakran használt SQL-adatbázisokat a rugalmas készletekbe (ugyanabban a régióban és teljesítményszint) a szokásos műveletek részeként anélkül, hogy elveszítené a fenntartott kapacitást. Ha nem alkalmazott puffert tart fenn a foglalásban, a teljesítménybeli tüskéket hatékonyan kezelheti a költségkeret túllépése nélkül.

## <a name="limitation"></a>Korlátozás

SQL Databaseban nem foglalhat le kiszolgáló nélküli vagy DTU (alapszintű, standard vagy prémium) adatbázisokat.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

A virtuális mag foglalási kedvezményt a rendszer automatikusan alkalmazza azon SQL-adatbázisok vagy SQL-felügyelt példányok számára, amelyek megfelelnek a kapacitás foglalási hatókörének és attribútumainak. A kapacitás foglalásának hatókörét a [Azure Portal](https://portal.azure.com), a PowerShell, a CLI vagy az API használatával frissítheti.

A kapacitás-foglalás kezelésével kapcsolatos további információkért lásd a [fenntartott kapacitások kezelése](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)című témakört.

Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mi az az Azure Reservations?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Az Azure Reservations kezelése](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Az Azure Reservations-kedvezmény ismertetése](../../cost-management-billing/reservations/understand-reservation-charges.md)
- [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [A foglalási kihasználtság ismertetése vállalati regisztrációnál](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [Azure Reservations a Partner Center felhőszolgáltatói (CSP) programjában](https://docs.microsoft.com/partner-center/azure-reservations)
