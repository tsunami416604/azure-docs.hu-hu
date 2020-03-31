---
title: Fenntartott számítási díjszabás - Azure Database for PostgreSQL - Single Server
description: A fenntartott kapacitású PostgreSQL számítási erőforrásokhoz szükséges Azure Database előre fizetése
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 66d7228e78f03196da0b26249e7f1f86e79d79fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159013"
---
# <a name="prepay-for-azure-database-for-postgresql-compute-resources-with-reserved-capacity"></a>A fenntartott kapacitású PostgreSQL számítási erőforrásokhoz szükséges Azure Database előre fizetése

A PostgreSQL Azure Database for PostgreSQL mostantól segít pénzt megtakarítani azáltal, hogy előre kifizeti a számítási erőforrásokat a használatalapú árakhoz képest. A PostgreSQL-hez készült Azure Database használatával egy vagy három évre előzetes kötelezettséget vállal a PostgreSQL-kiszolgálón, hogy jelentős kedvezményt kapjon a számítási költségekből. Az Azure Database for PostgreSQL fenntartott kapacitás megvásárlásához meg kell adnia az Azure-régiót, a központi telepítés típusát, a teljesítményszintet és a kifejezést. </br>

Nem kell hozzárendelnie a foglalást adott Azure Database for PostgreSQL-kiszolgálókhoz. Egy már futó Azure Database for PostgreSQL vagy az újonnan telepített, automatikusan megkapja a fenntartott díjszabás előnyeit. A foglalás megvásárlásával ön egy vagy három évig előre megfizeti a számítási költségeket. Amint megvásárol egy foglalást, a PostgreSQL-alapú Azure-adatbázis a foglalási attribútumokkal megegyező díjakat már nem számítfel a használatalapú díjszabás szerint. A foglalás nem terjed ki a PostgreSQL Adatbázis-kiszolgálókhoz kapcsolódó szoftverekre, hálózatokra vagy tárolási díjakra. A foglalási időszak végén a számlázási juttatás lejár, és az Azure Database for PostgreSQL a díjfizetés sel a felosztó-kirovó áron kerül számlázásra. A foglalások nem újulnak meg automatikusan. Díjszabási információkért tekintse meg az [Azure Database for PostgreSQL fenntartott kapacitásajánlat](https://azure.microsoft.com/pricing/details/postgresql/). </br>

> [!IMPORTANT]
> A fenntartott kapacitásdíjszabás csak a PostgreSQL [egykiszolgálós](https://docs.microsoft.com/azure/postgresql/overview#azure-database-for-postgresql---single-server) üzembe helyezéshez az Azure Database számára érhető el, [a nagykapacitású Citus](https://docs.microsoft.com/azure/postgresql/overview#azure-database-for-postgresql---hyperscale-citus) központi telepítése esetén nem.

A PostgreSQL számára fenntartott kapacitásazure-adatbázist az [Azure Portalon vásárolhatja](https://portal.azure.com/)meg. A lefoglalt kapacitás megvásárlása:

* Legalább egy vállalati vagy egyéni előfizetés tulajdonosi szerepkörben kell lennie, ha felosztó-ki-felárak vannak.
* Nagyvállalati előfizetések esetében engedélyezni kell a **Fenntartott példányok hozzáadása** beállítást az [EA Portalon](https://ea.azure.com/). Ha ez a beállítás le van tiltva, akkor az előfizetésben eA-rendszergazdának kell lennie.
* A Felhőszolgáltató (CSP) program esetében csak a rendszergazdai ügynökök vagy az értékesítési ügynökök vásárolhatnak Azure Database for PostgreSQL fenntartott kapacitást. </br>

A nagyvállalati ügyfelek és az használatalapú fizetéses ügyfelek foglalási vásárlásokért való felszámításának részletei, az [Azure-foglalások használatának ismertetése a nagyvállalati regisztrációhoz,](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) valamint [az Azure-foglalás használatának megismerése az használatalapú fizetéses előfizetéshez.](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)


## <a name="determine-the-right-server-size-before-purchase"></a>A megfelelő kiszolgálóméret meghatározása vásárlás előtt

A foglalás méretének az adott régión belül meglévő vagy hamarosan üzembe helyezett kiszolgálók által használt számítási mennyiség teljes mennyiségén kell alapulnia, és ugyanazt a teljesítményszintet és hardvergenerálást kell használnia.</br>

Tegyük fel például, hogy egy általános célt futtat, a Gen5 – 32 virtuális magos PostgreSQL adatbázist, és két memóriaoptimalizált memóriát, a Gen5 – 16 virtuális magos PostgreSQL-adatbázisokat. Továbbá, azt feltételezzük, hogy azt tervezi, hogy telepíteni a következő hónapban egy további általános célja, Gen5 – 32 virtuális magadatbázis-kiszolgáló, és egy memória optimalizált, Gen5 – 16 vCore adatbázis-kiszolgáló. Tegyük fel, hogy tudja, hogy szüksége lesz ezekre az erőforrásokra legalább 1 évig. Ebben az esetben meg kell vásárolnia egy 64 (2x32) virtuális mag, 1 éves foglalás egyetlen adatbázis általános célja - Gen5 és egy 48 (2x16 + 16) virtuális mag 1 év foglalás egyetlen adatbázis memória optimalizált - Gen5


## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>Azure-adatbázis vásárlása a PostgreSQL számára fenntartott kapacitáshoz

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. Válassza a **Minden szolgáltatás** > **foglalása lehetőséget.**
3. Válassza **a Hozzáadás lehetőséget,** majd a Beszerzési foglalások ablaktáblán válassza az **Azure Database for PostgreSQL** lehetőséget a PostgreSQL-adatbázisok új foglalásának megvásárlásához.
4. Töltse ki a szükséges mezőket. A meglévő vagy új adatbázisok, amelyek megfelelnek a kiválasztott attribútumok jogosultak a fenntartott kapacitáskedvezmény. A postgreSQL-kiszolgálókhoz készült Azure-adatbázis tényleges száma a kiválasztott hatókörtől és mennyiségtől függ.


![A fenntartott árak áttekintése](media/concepts-reserved-pricing/postgresql-reserved-price.png)


Az alábbi táblázat a szükséges mezőket ismerteti.

| Mező | Leírás |
| :------------ | :------- |
| Előfizetés   | Az Azure Database postgreSQL-alapú fenntartott kapacitásfoglaláshoz használt előfizetés. Az előfizetés fizetési módja a PostgreSQL számára fenntartott kapacitásfoglalásazure-adatbázisának előzetes költségeit számítja fel. Az előfizetéstípusnak nagyvállalati szerződésnek (ajánlatszámok: MS-AZR-0017P vagy MS-AZR-0148P) vagy egyedi megállapodásnak kell lennie a felosztó-kiosztó díjszabással (ajánlatszámok: MS-AZR-0003P vagy MS-AZR-0023P). Nagyvállalati előfizetésnél a díjak a regisztrációhoz tartozó keretek egyenlegeiből lesznek levonva, illetve túlhasználatként lesznek számlázva. A felosztó-kiosztó díjszabással rendelkező egyéni előfizetések esetén a díjakat az előfizetés hitelkártyájára vagy számlafizetési módjára terheljük.
| Hatókör | A virtuálismag-foglalás hatóköre egy előfizetésre vagy több előfizetésre (megosztott hatókörre) terjedhet ki. Ha a következőket választja: </br></br> **Megosztott**, a virtuálismag-foglalási kedvezmény az Azure Database for PostgreSQL-kiszolgálókra vonatkozik, amelyek a számlázási környezetben bármilyen előfizetésben futnak. A vállalati ügyfelek számára a megosztott hatókör a regisztráció, és a regisztráción belüli összes előfizetést tartalmazza. A használatalapú fizetéses ügyfelek esetében a megosztott hatókör a fiókadminisztrátor által létrehozott, használatalapú fizetéses előfizetéseket foglalja magában.</br></br> **Egyetlen előfizetés**, a virtuálismag-foglalási kedvezmény az Azure Database for PostgreSQL-kiszolgálókra vonatkozik ebben az előfizetésben. </br></br> **Egyetlen erőforráscsoport**, a foglalási kedvezmény a kiválasztott előfizetésben és az adott előfizetésen belül a kijelölt erőforráscsoportban a PostgreSQL-kiszolgálók Azure Database for PostgreSQL-kiszolgálókra vonatkozik.
| Régió | Az Azure-régió, amely az Azure Database for PostgreSQL fenntartott kapacitásfoglalás által lefedett.
| Központi telepítés típusa | Az Azure Database for PostgreSQL erőforrástípus, amely a foglalást meg szeretné vásárolni.
| Teljesítményszint | A PostgreSQL-kiszolgálók Azure-adatbázisának szolgáltatási szintje.
| Időtartam | Egy év
| Mennyiség | Az Azure Database for PostgreSQL fenntartott kapacitásfoglaláshoz vásárolt számítási erőforrások mennyisége. A mennyiség a kiválasztott Azure-régióban és a Teljesítmény szintben lévő virtuális magok száma, amelyek le vannak foglalva, és a számlázási engedményt kapják. Ha például egy Azure Database for PostgreSQL-kiszolgálók teljes számítási kapacitásával rendelkező, Az USA keleti régiójában gen5 16 virtuális mag teljes számítási kapacitással fut vagy szeretne futtatni, akkor a mennyiséget 16-ként kell megadnia, hogy maximalizálja az összes kiszolgáló előnyét.

## <a name="cancel-exchange-or-refund-reservations"></a>Foglalások lemondása, cseréje vagy visszatérítése

Bizonyos korlátozásokkal lehetősége van a foglalások lemondására, cseréjére és visszatérítésére. További információkért lásd: [Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="vcore-size-flexibility"></a>virtuálismag-méret rugalmassága

A virtuális mag méretének rugalmassága segít a teljesítményszinten és a régión belül fel- vagy leskálázhatóként, a fenntartott kapacitáselőnyeinek elvesztése nélkül. 

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

A virtuálismag-foglalási kedvezmény automatikusan vonatkozik az Azure Database for PostgreSQL-kiszolgálók, amelyek megfelelnek az Azure Database for PostgreSQL fenntartott kapacitásfoglalás hatóköre és attribútumai automatikusan alkalmazza. Frissítheti az Azure-adatbázis hatókörét a PostgreSQL számára fenntartott kapacitásfoglaláshoz az Azure Portalon, a PowerShellen, a CLI-n vagy az API-n keresztül. </br></br>
Az Azure Database for PostgreSQL fenntartott kapacitás kezeléséről az Azure Database for PostgreSQL fenntartott kapacitás kezelése című témakörben olvashat.

Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Mik azok az Azure-foglalások?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
* [Azure-foglalások kezelése](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [Az Azure Reservations-kedvezmény ismertetése](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-postgresql)
* [A foglalási kihasználtság ismertetése vállalati regisztrációnál](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [Azure Reservations a Partner Center felhőszolgáltatói (CSP) programjában](https://docs.microsoft.com/partner-center/azure-reservations)
