---
title: Fenntartott számítási díjszabás – Azure Database for PostgreSQL – nagy kapacitású (Citus)
description: Előre fizetés a Azure Database for PostgreSQL-nagy kapacitású (Citus) számítási erőforrások számára fenntartott kapacitással
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: dcf38a3bcd41ba41bb7cf3b16d022a7a45734c4a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85218033"
---
# <a name="prepay-for-azure-database-for-postgresql---hyperscale-citus-compute-resources-with-reserved-capacity"></a>Előre fizetés a Azure Database for PostgreSQL-nagy kapacitású (Citus) számítási erőforrások számára fenntartott kapacitással

Azure Database for PostgreSQL – nagy kapacitású (Citus) mostantól lehetővé teszi a pénz megtakarítását a számítási erőforrások előre fizetett költségeivel, az utólagos elszámolású árakhoz képest. A nagy kapacitású (Citus) fenntartott kapacitással egy-vagy hároméves időszakra vonatkozó előzetes kötelezettségvállalást hozhat a nagy kapacitású-(Citus-) kiszolgálócsoport számára, hogy jelentős kedvezményt kapjon a számítási költségekkel kapcsolatban. A nagy kapacitású (Citus) számára fenntartott kapacitás megvásárlásához meg kell adnia az Azure-régiót, a foglalási időszakot és a számlázási gyakoriságot.

> [!IMPORTANT]
> Ez az oldal a Azure Database for PostgreSQL – nagy kapacitású (Citus) számára fenntartott kapacitásról szól. A Azure Database for PostgreSQL – egyetlen kiszolgáló számára fenntartott kapacitással kapcsolatos információkért tekintse meg [ezt az oldalt](/azure/postgresql/concept-reserved-pricing) .

A foglalást nem kell hozzárendelni adott nagy kapacitású-(Citus-) kiszolgálócsoportokhöz. Egy már futó nagy kapacitású-(Citus-) kiszolgálócsoport vagy újonnan üzembe helyezett szolgáltatás automatikusan megkapja a fenntartott díjszabás előnyeit. A foglalás megvásárlásával előre fizet a számítási költségekre egy vagy három év alatt. A foglalás megvásárlása után a foglalási attribútumoknak megfelelő nagy kapacitású-(Citus-) számítási díjakra az utólagos elszámolású díjszabásban már nem számítunk fel díjat. A foglalások nem fedik le a nagy kapacitású-(Citus-) kiszolgálócsoportokhöz kapcsolódó szoftver-, hálózatkezelési és tárolási díjakat. A foglalási időszak végén a számlázási juttatás lejár, és a nagy kapacitású (Citus) kiszolgálói csoportok díjait az utólagos elszámolású díjszabás szerint számoljuk el. A foglalások nem újítják meg az előfizetést. A díjszabással kapcsolatos információkért tekintse meg a [Azure Database for PostgreSQL – nagy kapacitású (Citus) fenntartott kapacitás ajánlatát](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/).

A [Azure Portal](https://portal.azure.com/)foglalt nagy kapacitású (Citus) fenntartott kapacitást is megvásárolhatja. A foglalásért fizethet [előre vagy havi részletekben](https://docs.microsoft.com/azure/cost-management-billing/reservations/monthly-payments-reservations). A fenntartott kapacitás megvásárlása:

* Legalább egy Nagyvállalati vagy egyéni előfizetéshez tulajdonosi szerepkörrel kell rendelkeznie, utólagos elszámolású díjszabással.
* Nagyvállalati előfizetések esetében engedélyezni kell a **Fenntartott példányok hozzáadása** beállítást az [EA Portalon](https://ea.azure.com/). Ha ez a beállítás le van tiltva, akkor az előfizetés egyik nagyvállalati rendszergazdájának kell lennie.
* A Cloud Solution Provider (CSP) program esetében csak a felügyeleti ügynökök vagy értékesítési ügynökök vásárolhatják meg a nagy kapacitású (Citus) fenntartott kapacitást.

Ha szeretné megtudni, hogy a vállalati ügyfelek és az utólagos elszámolású ügyfelek milyen díjat számítanak fel a foglalások vásárlására, tekintse meg az [Azure foglalás használatának ismertetése a nagyvállalati beléptetéssel](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) és az [Azure foglalás használatának](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)ismertetése az utólagos elszámolású előfizetéshez.

## <a name="determine-the-right-server-group-size-before-purchase"></a>A kiszolgáló csoport megfelelő méretének meghatározása a vásárlás előtt

A foglalások méretének az adott régióban található nagy kapacitású-(Citus-) kiszolgálócsoportok meglévő vagy hamarosan üzembe helyezett koordinátora és munkavégző csomópontjai által használt teljes számítási mennyiség alapján kell alapulnia.

Tegyük fel például, hogy egy nagy kapacitású-(Citus-) kiszolgáló csoportot futtat 16 virtuális mag-koordinátorral és három 8 virtuális mag feldolgozó csomóponttal. Továbbá tegyük fel, hogy a következő hónapban szeretne üzembe helyezni egy további nagy kapacitású (Citus), amely egy 32 virtuális mag-koordinátorral és két 4 virtuális mag feldolgozó csomóponttal rendelkezik. Tegyük fel, hogy legalább egy évig szüksége lesz ezekre az erőforrásokra.

Ebben az esetben egy éves foglalást kell vásárolnia a következőhöz:

* Összesen 16 virtuális mag + 32 virtuális mag = 48 virtuális mag a koordinátori csomópontokhoz
* Összesen három csomópont x 8 virtuális mag + 2 csomópont x 4 virtuális mag = 24 + 8 = 32 virtuális mag a munkavégző csomópontok számára

## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>Azure Database for PostgreSQL fenntartott kapacitás vásárlása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza a **Minden szolgáltatás** > **Reservations** lehetőséget.
3. Válassza a **Hozzáadás** lehetőséget, majd a foglalások ablaktáblán válassza a **Azure Database for PostgreSQL** lehetőséget a PostgreSQL-adatbázisok új foglalásának megvásárlásához.
4. Válassza a nagy kapacitású (Citus) számítási típust a vásárláshoz, és kattintson a **kiválasztás** gombra.
5. Tekintse át a kiválasztott számítási típus mennyiségét a **Products (termékek** ) lapon.
4. A vásárlás befejezéséhez folytassa a **vásárlás + áttekintés** lapon.

A következő táblázat a kötelező mezőket ismerteti.

| Mező        | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Előfizetés | A Azure Database for PostgreSQL fenntartott kapacitás foglalásának megfizetéséhez használt előfizetés. Az előfizetéshez tartozó fizetési módot a Azure Database for PostgreSQL fenntartott kapacitás foglalásának előzetes költségei alapján számítjuk fel. Az előfizetés típusának nagyvállalati szerződésnek kell lennie (ajánlati számok: MS-AZR-0017P vagy MS-AZR-0148P), vagy az utólagos elszámolású díjszabással rendelkező egyéni szerződés (ajánlati számok: MS-AZR-0003P vagy MS-AZR-0023P). A nagyvállalati előfizetések esetében a díjakat a beléptetés pénzügyi kötelezettségvállalásának egyenlege vagy a felszámított díj alapján kell levonni. Az utólagos elszámolású előfizetések esetében az előfizetés díjait a bankkártya vagy a számla fizetési módja alapján számítjuk fel.                                                                                  |
| Hatókör        | A virtuális mag foglalási hatóköre egyetlen előfizetésre vagy több előfizetésre (megosztott hatókörre) is vonatkozhat. Ha a következőket választja: <br><br> **Megosztott,** a virtuális mag foglalási kedvezmény a számlázási környezetben lévő előfizetésekben futó nagy kapacitású-(Citus-) kiszolgálócsoportok esetében érvényes. A vállalati ügyfelek esetében a közös hatókör a regisztráció, és a regisztráción belüli összes előfizetés szerepel.  A használatalapú fizetéses ügyfelek esetében a megosztott hatókör a fiókadminisztrátor által létrehozott, használatalapú fizetéses előfizetéseket foglalja magában. <br><br> **Egyszeri előfizetés esetén** a virtuális mag foglalási kedvezményt a rendszer az előfizetésben lévő nagy kapacitású (Citus) kiszolgálócsoportok esetében alkalmazza. <br><br> **Egy erőforráscsoport,** a foglalási kedvezményt a rendszer a kijelölt előfizetésben található nagy kapacitású (Citus), a kiválasztott erőforráscsoporthoz pedig az adott előfizetésen belül alkalmazza. |
| Régió       | Az Azure-régió, amelyre a Azure Database for PostgreSQL – nagy kapacitású (Citus) foglalt kapacitás foglalása vonatkozik.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Kifejezés         | Egy vagy három év.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Mennyiség     | A nagy kapacitású (Citus) foglalt kapacitás foglalása során megvásárolt számítási erőforrások mennyisége. Különösen a kijelölt Azure-régió koordinátorának vagy munkavégző csomópontjának a virtuális mag száma, amely a számlázási kedvezményt kapja. Ha például futtatja (vagy futtatni kívánja) a nagy kapacitású (Citus) kiszolgálói csoportokat az USA keleti régiójában lévő 64 koordinátori csomópont virtuális mag és 32 munkavégző csomópont-virtuális mag teljes számítási kapacitásával, akkor az összes kiszolgáló előnyének maximalizálása érdekében adja meg 64 és 32 értéket a koordinátor és a munkavégző csomópontok számára.                                                                                                                                                                                                                                                     |



## <a name="cancel-exchange-or-refund-reservations"></a>Foglalások lemondása, cseréje vagy visszatérítése

Bizonyos korlátozásokkal lehetősége van a foglalások lemondására, cseréjére és visszatérítésére. További információkért lásd: [Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="vcore-size-flexibility"></a>Virtuális mag méretének rugalmassága

a virtuális mag méretének rugalmassága segítséget nyújt a koordinátorok és a munkavégző csomópontok egy adott régión belüli vertikális felskálázásához, a fenntartott kapacitási juttatás elvesztése nélkül.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

A virtuális mag foglalási kedvezményt a rendszer automatikusan alkalmazza azon nagy kapacitású-(Citus-) kiszolgálócsoportok számára, amelyek megfelelnek a Azure Database for PostgreSQL fenntartott kapacitás foglalási hatókörének és attribútumainak. Az Azure Database for PostgreSQL – nagy kapacitású (Citus) fenntartott kapacitás foglalásának hatókörét a Azure Portal, a PowerShell, a CLI vagy az API segítségével frissítheti.

Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Mi az az Azure Reservations?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
* [Az Azure Reservations kezelése](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [Az Azure Reservations-kedvezmény ismertetése](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-postgresql)
* [A foglalási kihasználtság ismertetése vállalati regisztrációnál](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [Azure Reservations a Partner Center felhőszolgáltatói (CSP) programjában](https://docs.microsoft.com/partner-center/azure-reservations)
