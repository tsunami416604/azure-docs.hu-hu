---
title: Előre fizetett számítási fenntartott kapacitással - Azure Database for MySQL
description: A MySQL fenntartott kapacitású Azure Database előre fizetése
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 78c8750de7189bad33e9bbc766a3d7543a646f6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159353"
---
# <a name="prepay-for-azure-database-for-mysql-compute-resources-with-reserved-capacity"></a>A MySQL fenntartott kapacitású Azure Database előre fizetése

Az Azure Database for MySQL mostantól segít pénzt megtakarítani azáltal, hogy előre kifizeti a számítási erőforrásokat a használatalapú árakhoz képest. Az Azure Database for MySQL fenntartott kapacitással egy vagy három évre előzetes kötelezettséget vállal a MySQL-kiszolgálón, hogy jelentős kedvezményt kapjon a számítási költségekből. Az Azure Database for MySQL fenntartott kapacitás megvásárlásához meg kell adnia az Azure-régiót, a központi telepítés típusát, a teljesítményszintet és a kifejezést. </br>

Nem kell hozzárendelnie a foglalást a MySQL-kiszolgálók adott Azure-adatbázisához. Egy már futó Azure Database for MySQL vagy az újonnan telepített, automatikusan megkapja a fenntartott díjszabás előnyeit. A foglalás megvásárlásával ön egy vagy három évig előre megfizeti a számítási költségeket. Amint megvásárol egy foglalást, a MySQL-alapú Azure-adatbázis a foglalási attribútumokkal megegyező számítási díjakat már nem számítja fel a használatalapú díjszabásszerint. A foglalás nem terjed ki a MySQL adatbázis-kiszolgálóhoz kapcsolódó szoftverekre, hálózatokra vagy tárolási díjakra. A foglalási időszak végén a számlázási juttatás lejár, és az Azure Database for MySQL számlázása a felosztó-kirovó áron. A foglalások nem újulnak meg automatikusan. Díjszabási információkért tekintse meg az [Azure Database for MySQL fenntartott kapacitásajánlat](https://azure.microsoft.com/pricing/details/mysql/). </br>

Az Azure Database for MySQL fenntartott kapacitása az [Azure Portalon](https://portal.azure.com/)vásárolhat. A lefoglalt kapacitás megvásárlása:

* Legalább egy vállalati vagy egyéni előfizetés tulajdonosi szerepkörben kell lennie, ha felosztó-ki-felárak vannak.
* Nagyvállalati előfizetések esetében engedélyezni kell a **Fenntartott példányok hozzáadása** beállítást az [EA Portalon](https://ea.azure.com/). Ha ez a beállítás le van tiltva, akkor az előfizetésben eA-rendszergazdának kell lennie.
* A Felhőszolgáltató (CSP) program esetében csak a rendszergazdai ügynökök vagy az értékesítési ügynökök vásárolhatnak Azure Database-t a MySQL számára fenntartott kapacitáshoz. </br>

A nagyvállalati ügyfelek és az használatalapú fizetéses ügyfelek foglalási vásárlásokért való felszámításának részletei, az [Azure-foglalások használatának ismertetése a nagyvállalati regisztrációhoz,](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea) valamint [az Azure-foglalás használatának megismerése az használatalapú fizetéses előfizetéshez.](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage)


## <a name="determine-the-right-database-size-before-purchase"></a>Az adatbázis megfelelő méretének meghatározása vásárlás előtt

A foglalás méretének a meglévő vagy hamarosan üzembe helyezendő kiszolgáló által egy adott régión belül használt számítási mennyiségen kell alapulnia, és ugyanazt a teljesítményszintet és hardvergenerálást kell használnia.</br>

Tegyük fel például, hogy egy általános célt futtat, a Gen5 – 32 virtuálismagos MySQL adatbázist, és két memóriaoptimalizált memóriát, a Gen5 – 16 virtuális magos MySQL-adatbázisokat. Továbbá, azt feltételezzük, hogy azt tervezi, hogy telepíteni a következő hónapban egy további általános célja, Gen5 – 32 virtuális magadatbázis-kiszolgáló, és egy memória optimalizált, Gen5 – 16 vCore adatbázis-kiszolgáló. Tegyük fel, hogy tudja, hogy szüksége lesz ezekre az erőforrásokra legalább 1 évig. Ebben az esetben meg kell vásárolnia egy 64 (2x32) virtuális mag, 1 éves foglalás egyetlen adatbázis általános célja - Gen5 és egy 48 (2x16 + 16) virtuális mag 1 év foglalás egyetlen adatbázis memória optimalizált - Gen5


## <a name="buy-azure-database-for-mysql-reserved-capacity"></a>Azure-adatbázis vásárlása a MySQL számára fenntartott kapacitáshoz

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
2. Válassza a **Minden szolgáltatás** > **foglalása lehetőséget.**
3. Válassza **a Hozzáadás lehetőséget,** majd a Vásárlás foglalások ablaktáblán válassza az **Azure Database for MySQL** lehetőséget a MySQL-adatbázisok új foglalásának megvásárlásához.
4. Töltse ki a szükséges mezőket. A meglévő vagy új adatbázisok, amelyek megfelelnek a kiválasztott attribútumok jogosultak a fenntartott kapacitáskedvezmény. Az Azure-adatbázis tényleges száma a MySQL-kiszolgálókhoz, amelyek a kedvezményt kapnak, a kiválasztott hatókörtől és mennyiségtől függ.


![A fenntartott árak áttekintése](media/concepts-reserved-pricing/mysql-reserved-price.png)


Az alábbi táblázat a szükséges mezőket ismerteti.

| Mező | Leírás |
| :------------ | :------- |
| Előfizetés   | Az Azure Database for MySQL fenntartott kapacitásfoglalásához használt előfizetés. Az előfizetés fizetési módja az Azure Database kezdeti költségeit számítja fel a MySQL számára fenntartott kapacitásfoglaláshoz. Az előfizetéstípusnak nagyvállalati szerződésnek (ajánlatszámok: MS-AZR-0017P vagy MS-AZR-0148P) vagy egyedi megállapodásnak kell lennie a felosztó-kiosztó díjszabással (ajánlatszámok: MS-AZR-0003P vagy MS-AZR-0023P). Nagyvállalati előfizetésnél a díjak a regisztrációhoz tartozó keretek egyenlegeiből lesznek levonva, illetve túlhasználatként lesznek számlázva. A felosztó-kiosztó díjszabással rendelkező egyéni előfizetések esetén a díjakat az előfizetés hitelkártyájára vagy számlafizetési módjára terheljük.
| Hatókör | A virtuálismag-foglalás hatóköre egy előfizetésre vagy több előfizetésre (megosztott hatókörre) terjedhet ki. Ha a következőket választja: </br></br> **Megosztott**, a virtuálismag-foglalási kedvezmény az Azure Database for MySQL-kiszolgálók ra vonatkozik a számlázási környezetben futó bármely előfizetésben. A vállalati ügyfelek számára a megosztott hatókör a regisztráció, és a regisztráción belüli összes előfizetést tartalmazza. A használatalapú fizetéses ügyfelek esetében a megosztott hatókör a fiókadminisztrátor által létrehozott, használatalapú fizetéses előfizetéseket foglalja magában.</br></br> **Egyetlen előfizetés**, a virtuálismag-foglalási kedvezmény az Azure Database for MySQL-kiszolgálók ebben az előfizetésben vonatkozik. </br></br> **Egyetlen erőforráscsoport,** a foglalási kedvezmény a kiválasztott előfizetésben és az adott előfizetésen belül a kiválasztott erőforráscsoportban a MySQL-kiszolgálók Azure Database for MySQL-kiszolgálókra vonatkozik.
| Régió | Az Azure-régió, amely az Azure Database for MySQL fenntartott kapacitásfoglalás által lefedett.
| Központi telepítés típusa | Az Azure Database for MySQL erőforrástípus, amely a foglalást szeretné megvásárolni.
| Teljesítményszint | A MySQL-kiszolgálók Azure-adatbázisának szolgáltatási szintje.
| Időtartam | Egy év
| Mennyiség | Az Azure Database for MySQL fenntartott kapacitásfoglalása az Azure Database-ben vásárolt számítási erőforrások mennyisége. A mennyiség a kiválasztott Azure-régióban és a Teljesítmény szintben lévő virtuális magok száma, amelyek le vannak foglalva, és a számlázási engedményt kapják. Ha például egy Azure Database for MySQL-kiszolgálók hoz egy Azure Database for MySQL-kiszolgálót futtat vagy szeretne futtatni, amelynek teljes számítási kapacitása Gen5 16 virtuális mag az USA keleti régiójában, akkor a mennyiséget 16-ként kell megadnia, hogy maximalizálja az összes kiszolgáló előnyét.

## <a name="cancel-exchange-or-refund-reservations"></a>Foglalások lemondása, cseréje vagy visszatérítése

Bizonyos korlátozásokkal lehetősége van a foglalások lemondására, cseréjére és visszatérítésére. További információkért lásd: [Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai](https://docs.microsoft.com/azure/billing/billing-azure-reservations-self-service-exchange-and-refund).

## <a name="vcore-size-flexibility"></a>virtuálismag-méret rugalmassága

A virtuális mag méretének rugalmassága segít a teljesítményszinten és a régión belül fel- vagy leskálázhatóként, a fenntartott kapacitáselőnyeinek elvesztése nélkül. 

## <a name="need-help--contact-us"></a>Segítségre van szüksége? Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

A virtuálismag-foglalási kedvezmény automatikusan vonatkozik az Azure Database for MySQL-kiszolgálók, amelyek megfelelnek az Azure Database for MySQL fenntartott kapacitásfoglaláshatókör és attribútumok automatikusan alkalmazza. Frissítheti az Azure-adatbázis hatókörét a MySQL számára fenntartott kapacitásfoglaláshoz az Azure Portalon, a PowerShellen, a CLI-n vagy az API-n keresztül. </br></br>
Az Azure Database for MySQL fenntartott kapacitás ának kezeléséről az Azure Database mySQL számára fenntartott kapacitás kezelése című témakörben olvashat.

Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Mik azok az Azure-foglalások?](https://docs.microsoft.com/azure/billing/billing-save-compute-costs-reservations)
* [Azure-foglalások kezelése](https://docs.microsoft.com/azure/billing/billing-manage-reserved-vm-instance)
* [Az Azure Reservations-kedvezmény ismertetése](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges)
* [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](https://docs.microsoft.com/azure/billing/billing-understand-reservation-charges-mysql)
* [A foglalási kihasználtság ismertetése vállalati regisztrációnál](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)
* [Azure Reservations a Partner Center felhőszolgáltatói (CSP) programjában](https://docs.microsoft.com/partner-center/azure-reservations)

