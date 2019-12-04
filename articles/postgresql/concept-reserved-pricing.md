---
title: Fenntartott számítási díjszabás – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Előre fizetés Azure Database for PostgreSQL számítási erőforrások számára fenntartott kapacitással
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 634ab645ea49c29bb5624afd82e549dfbc8d0fdf
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769897"
---
# <a name="prepay-for-azure-database-for-postgresql-compute-resources-with-reserved-capacity"></a>Előre fizetés Azure Database for PostgreSQL számítási erőforrások számára fenntartott kapacitással

A Azure Database for PostgreSQL mostantól segít pénzt megtakarítani a számítási erőforrások előfizetésével, az utólagos elszámolású árakhoz képest. Azure Database for PostgreSQL fenntartott kapacitással a PostgreSQL-kiszolgáló előzetes kötelezettségvállalása egy évig érvényes, és a számítási költségek jelentős kedvezményt kapnak. Azure Database for PostgreSQL fenntartott kapacitás megvásárlásához meg kell adnia az Azure-régiót, a központi telepítési típust, a teljesítménnyel kapcsolatos szintet és a kifejezést. </br>

A foglalást nem kell hozzárendelni adott Azure Database for PostgreSQL kiszolgálókhoz. Egy már futó Azure Database for PostgreSQL vagy újonnan üzembe helyezett szolgáltatás automatikusan megkapja a fenntartott díjszabás előnyeit. A foglalás megvásárlásával egy évig előre fizet a számítási költségekért. A foglalás megvásárlása után az Azure Database for PostgreSQL számítási díjait, amelyek megfelelnek a foglalási attribútumoknak, már nem számítjuk fel az utólagos elszámolású díjszabást. A foglalás nem vonatkozik a PostgreSQL-adatbázis-kiszolgálókhoz kapcsolódó szoftverekre, hálózatokra vagy tárolási díjakra. A foglalási időszak végén a számlázási juttatás lejár, a Azure Database for PostgreSQL pedig az utólagos elszámolású díjszabás szerint számoljuk el. A foglalások nem automatikus megújítást végeznek. A díjszabással kapcsolatos információkért tekintse meg a [Azure Database for PostgreSQL fenntartott kapacitás ajánlatát](https://azure.microsoft.com/pricing/details/postgresql/). </br>

> [!IMPORTANT]
> A fenntartott kapacitás díjszabása csak a Azure Database for PostgreSQL [egykiszolgálós](https://docs.microsoft.com/azure/postgresql/overview#azure-database-for-postgresql---single-server) telepítés esetén érhető el, és nem a [nagy kapacitású Citus](https://docs.microsoft.com/azure/postgresql/overview#azure-database-for-postgresql---hyperscale-citus) telepítéséhez.

A [Azure Portal](https://portal.azure.com/)Azure Database for PostgreSQL fenntartott kapacitást is vásárolhat. A fenntartott kapacitás megvásárlása:

* Legalább egy Nagyvállalati vagy egyéni előfizetéshez tulajdonosi szerepkörrel kell rendelkeznie, utólagos elszámolású díjszabással.
* Nagyvállalati előfizetések esetében engedélyezni kell a **Fenntartott példányok hozzáadása** beállítást az [EA Portalon](https://ea.azure.com/). Ha ez a beállítás le van tiltva, akkor az előfizetés egyik nagyvállalati rendszergazdájának kell lennie.
* A Cloud Solution Provider (CSP) program esetében csak a felügyeleti ügynökök vagy értékesítési ügynökök vásárolhatják meg Azure Database for PostgreSQL fenntartott kapacitást. </br>

Az ügyfelek és az utólagos elszámolású ügyfelek díjszabása a foglalások beszerzése után: az [Azure foglalási szolgáltatás használatának megismerése a nagyvállalati beléptetéshez](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) , valamint az [Azure-foglalás használatának](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)ismertetése az utólagos elszámolású előfizetéshez.


## <a name="determine-the-right-server-size-before-purchase"></a>A kiszolgáló megfelelő méretének meghatározása a vásárlás előtt

A foglalás méretének a meglévő vagy hamarosan üzembe helyezett, adott régión belüli kiszolgálók által használt számítási mennyiségnek kell alapulnia, és ugyanazt a teljesítményszint és a hardver generációját kell használnia.</br>

Tegyük fel például, hogy egy általános célt futtat, Gen5 – 32 virtuális mag PostgreSQL-adatbázist, és két memóriát optimalizált, Gen5 – 16 virtuális mag PostgreSQL-adatbázist. Továbbá Tételezzük fel, hogy a következő hónapban szeretne üzembe helyezni egy további általános célt, a Gen5 – 32 virtuális mag adatbázis-kiszolgálót és egy, a Gen5 – 16 virtuális mag adatbázis-kiszolgálót. Tegyük fel, hogy tudnia kell, hogy legalább 1 évig szüksége lesz ezekre az erőforrásokra. Ebben az esetben vásárolnia kell egy 64 (2x32) virtuális mag, 1 éves foglalást az Egyadatbázisos általános célú Gen5 és a 48 (2x16 + 16) virtuális mag 1 év foglalása egyetlen adatbázishoz optimalizált memória esetén – Gen5


## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>Azure Database for PostgreSQL fenntartott kapacitás vásárlása

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).
2. Válassza a **Minden szolgáltatás** > **Reservations** lehetőséget.
3. Válassza a **Hozzáadás** lehetőséget, majd a foglalások ablaktáblán válassza a **Azure Database for PostgreSQL** lehetőséget a PostgreSQL-adatbázisok új foglalásának megvásárlásához.
4. Töltse ki a kötelező mezőket. Meglévő vagy új adatbázisok, amelyek megfelelnek a kiválasztott attribútumoknak, a fenntartott kapacitás kedvezményét kapják meg. A kedvezményt megkapó Azure Database for PostgreSQL kiszolgálók tényleges száma függ a hatókörtől és a kiválasztott mennyiségtől.


![A fenntartott díjszabás áttekintése](media/concepts-reserved-pricing/postgresql-reserved-price.png)


A következő táblázat a kötelező mezőket ismerteti.

| Mező | Leírás |
| :------------ | :------- |
| Előfizetés   | A Azure Database for PostgreSQL fenntartott kapacitás foglalásának megfizetéséhez használt előfizetés. Az előfizetéshez tartozó fizetési módot a Azure Database for PostgreSQL fenntartott kapacitás foglalásának előzetes költségei alapján számítjuk fel. Az előfizetés típusának nagyvállalati szerződésnek kell lennie (ajánlati számok: MS-AZR-0017P vagy MS-AZR-0148P), vagy az utólagos elszámolású díjszabással rendelkező egyéni szerződés (ajánlati számok: MS-AZR-0003P vagy MS-AZR-0023P). Nagyvállalati előfizetésnél a díjak a regisztrációhoz tartozó keretek egyenlegeiből lesznek levonva, illetve túlhasználatként lesznek számlázva. Az utólagos elszámolású előfizetések esetében az előfizetés díjait a bankkártya vagy a számla fizetési módja alapján számítjuk fel.
| Hatókör | A virtuális mag foglalási hatóköre egyetlen előfizetésre vagy több előfizetésre (megosztott hatókörre) is vonatkozhat. Ha a következőket választja: </br></br> **Megosztva**a virtuális mag foglalási kedvezményt a számlázási környezetben lévő előfizetésekben futó Azure Database for PostgreSQL-kiszolgálókra alkalmazza a rendszer. A vállalati ügyfelek esetében a közös hatókör a regisztráció, és a regisztráción belüli összes előfizetés szerepel. A használatalapú fizetéses ügyfelek esetében a megosztott hatókör a fiókadminisztrátor által létrehozott, használatalapú fizetéses előfizetéseket foglalja magában.</br></br> **Egyszeri előfizetés**esetén a virtuális mag foglalási kedvezményt az előfizetésben lévő Azure Database for PostgreSQL-kiszolgálókra alkalmazza a rendszer. </br></br> **Egy erőforráscsoport**, a foglalási kedvezményt a rendszer a kijelölt előfizetésben található Azure Database for PostgreSQL-kiszolgálókra és az adott előfizetéshez tartozó kiválasztott erőforráscsoporthoz alkalmazza.
| Region (Régió) | Az Azure Database for PostgreSQL fenntartott kapacitás foglalása által érintett Azure-régió.
| Központi telepítés típusa | Az a Azure Database for PostgreSQL erőforrástípus, amelyre a foglalást megvásárolni szeretné.
| Teljesítményszint | A Azure Database for PostgreSQL-kiszolgálók szolgáltatási szintje.
| Időtartam | Egy év
| Mennyiség | A Azure Database for PostgreSQL fenntartott kapacitás foglalása keretében megvásárolt számítási erőforrások mennyisége. A mennyiség a kiválasztott Azure-régióban és a lefoglalt teljesítményszint virtuális mag, és a számlázási kedvezményt kapja. Ha például olyan Azure Database for PostgreSQL-kiszolgálókat futtat, amelyeken az USA keleti régiójában az Gen5 16 virtuális mag teljes számítási kapacitása fut, akkor az összes kiszolgáló előnyének maximalizálása érdekében a 16-as mennyiséget kell megadnia.

## <a name="cancel-exchange-or-refund-reservations"></a>Foglalások lemondása, cseréje vagy visszatérítése

Bizonyos korlátozásokkal lehetősége van a foglalások lemondására, cseréjére és visszatérítésére. További információkért lásd: [Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="vcore-size-flexibility"></a>Virtuális mag méretének rugalmassága

a virtuális mag méretének rugalmassága segít a teljesítmény-és a régión belüli vertikális felskálázásban, a fenntartott kapacitási juttatás elvesztése nélkül. 

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Következő lépések

A virtuális mag foglalási kedvezmény automatikusan érvényes azon Azure Database for PostgreSQL-kiszolgálók számára, amelyek megfelelnek az Azure Database for PostgreSQL fenntartott kapacitás foglalási hatókörének és attribútumainak. A PostgreSQL-hez készült Azure-adatbázis hatókörét a Azure Portal, a PowerShell, a CLI vagy az API segítségével frissítheti. </br></br>
A Azure Database for PostgreSQL fenntartott kapacitás kezelésével kapcsolatos információkért lásd: Azure Database for PostgreSQL fenntartott kapacitás kezelése.

Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Mi a Azure Reservations](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)?
* [Az Azure Reservations kezelése](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [Az Azure Reservations-kedvezmény ismertetése](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-postgresql)
* [A foglalási kihasználtság ismertetése vállalati regisztrációnál](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [Azure Reservations a Partner Center felhőszolgáltatói (CSP) programjában](https://docs.microsoft.com/partner-center/azure-reservations)
