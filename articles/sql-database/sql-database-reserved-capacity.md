---
title: Költségmegtakarítás
description: Ismerje meg, hogyan vásárolhat azure SQL Database fenntartott kapacitást, hogy mentse a számítási költségeket.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein, carlrab
ms.date: 08/29/2019
ms.openlocfilehash: 0713e1ed98cc87b95cad1f84672148cd062e0b58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979994"
---
# <a name="save-costs-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>Az Azure SQL Database által lefoglalt kapacitással megtakaríthatja az SQL Database számítási erőforrásainak költségeit

Az Azure SQL Database segítségével pénzt takaríthat meg, ha lefoglal egy számítási erőforrásokra vonatkozó foglalást a használatalapú árakhoz képest. Az Azure SQL Database számára fenntartott kapacitással egy vagy három évig kötelezettséget vállal az SQL Database használatára, hogy jelentős kedvezményt kapjon a számítási költségekből. Az SQL Database számára fenntartott kapacitás megvásárlásához meg kell adnia az Azure-régiót, a központi telepítés típusát, a teljesítményszintet és a kifejezést.


A foglalást nem kell hozzárendelnie adott SQL Database-példányokhoz (egyetlen adatbázishoz, rugalmas készletekhez vagy felügyelt példányhoz). A már futó vagy újonnan üzembe helyezett SQL Database-példányok automatikusan megkapják az előnyt. A foglalás megvásárlásával egy vagy három évig a számítási költségek használatát vállalja. Amint megvásárol egy foglalást, az SQL Database a foglalási attribútumokkal megegyező díjakat már nem számítja fel a használatalapú díjszabás szerint. A foglalás nem terjed ki az SQL Database-példányhoz kapcsolódó szoftverekre, hálózatokra vagy tárolási költségekre. A foglalási időszak végén a számlázási juttatás lejár, és az SQL-adatbázisok számlázása a felosztó-kirovó áron történik. A foglalások nem újulnak meg automatikusan. Az árképzésről az [SQL Database fenntartott kapacitásajánlatában](https://azure.microsoft.com/pricing/details/sql-database/managed/)talál.

Az Azure SQL Database számára fenntartott kapacitást az [Azure Portalon](https://portal.azure.com)vásárolhatja meg. A foglalásért fizethet [előre vagy havi részletekben](../cost-management-billing/reservations/monthly-payments-reservations.md). SQL Database lefoglalt kapacitásának megvásárlása:

- Legalább egy vállalati vagy egyéni előfizetés tulajdonosi szerepkörben kell lennie, ha felosztó-ki-felárak vannak.
- Nagyvállalati előfizetések esetében engedélyezni kell a **Fenntartott példányok hozzáadása** beállítást az [EA Portalon](https://ea.azure.com). Ha ez a beállítás le van tiltva, akkor az előfizetésben eA-rendszergazdának kell lennie.
- A Felhőszolgáltató (CSP) program esetében csak a rendszergazdai ügynökök vagy az értékesítési ügynökök vásárolhatnak SQL Database fenntartott kapacitást.

A nagyvállalati ügyfelek és az használatalapú fizetéses ügyfelek foglalási vásárlásokért való felszámításának részletei, az [Azure-foglalások használatának ismertetése a nagyvállalati regisztrációhoz,](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) valamint [az Azure-foglalás használatának megismerése az használatalapú fizetéses előfizetéshez.](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

## <a name="determine-the-right-sql-size-before-purchase"></a>A megfelelő SQL-méret meghatározása vásárlás előtt

A foglalás méretének a meglévő vagy hamarosan üzembe helyezendő egyetlen adatbázisok, rugalmas készletek vagy felügyelt példányok által egy adott régióban használt számítási teljes mennyiségen kell alapulnia, és ugyanazt a teljesítményszintet és hardvergenerálást kell használnia.

Tegyük fel például, hogy egy általános célt futtat, a Gen5 – 16 virtuálismagos rugalmas készletet, és két üzleti szempontból kritikus fontosságú, Gen5 – 4 virtuális magos egyetlen adatbázist. Továbbá tegyük fel, hogy a következő hónapban egy további általános célt, a Gen5 – 16 virtuálismagos rugalmas készletet és egy üzleti szempontból kritikus fontosságú, a Gen5 – 32 virtuálismagos rugalmas készletet szeretne telepíteni. Is, tegyük fel, hogy tudod, hogy szüksége lesz ezekre az erőforrásokra legalább 1 évig. Ebben az esetben meg kell vásárolnia egy 32 (2x16) virtuális mag, 1 éves foglalás egyetlen adatbázis/rugalmas készlet általános célja - Gen5 és egy 40 (2x4 + 32) virtuális mag 1 éves foglalás egyetlen adatbázis/rugalmas készlet üzleti kritikus - Gen5.

## <a name="buy-sql-database-reserved-capacity"></a>Fenntartott SQL Database-kapacitás vásárlása

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Válassza a **Minden szolgáltatás** > **foglalása lehetőséget.**
3. Válassza a **Hozzáadás** lehetőséget, majd a Beszerzési foglalások ablaktáblán válassza az **SQL Database** lehetőséget az SQL Database új foglalásának megvásárlásához.
4. Töltse ki a szükséges mezőket. Meglévő vagy új egyetlen adatbázisok, rugalmas készletek, vagy felügyelt példányok, amelyek megfelelnek a kiválasztott attribútumok jogosultak a fenntartott kapacitás kedvezmény. Az engedményt kapó SQL Database-példányok tényleges száma a kiválasztott hatókörtől és mennyiségtől függ.
    ![Képernyőkép az SQL Database lefoglalt kapacitásvásárlásának elküldése előtt](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

Az alábbi táblázat a szükséges mezőket ismerteti.

| Mező      | Leírás|
|------------|--------------|
|Előfizetés|Az SQL Database lefoglalt kapacitásfoglalásához használt előfizetés. Az előfizetés fizetési módja az SQL Database lefoglalt kapacitásfoglalásának előzetes költségeit számítja fel. Az előfizetéstípusnak nagyvállalati szerződésnek (ajánlatszámok: MS-AZR-0017P vagy MS-AZR-0148P) vagy egyedi megállapodásnak kell lennie a felosztó-kiosztó díjszabással (ajánlatszámok: MS-AZR-0003P vagy MS-AZR-0023P). Nagyvállalati előfizetésnél a díjak a regisztrációhoz tartozó keretek egyenlegeiből lesznek levonva, illetve túlhasználatként lesznek számlázva. A felosztó-kiosztó díjszabással rendelkező egyéni előfizetések esetén a díjakat az előfizetés hitelkártyájára vagy számlafizetési módjára terheljük.|
|Hatókör       |A virtuálismag-foglalás hatóköre egy előfizetésre vagy több előfizetésre (megosztott hatókörre) terjedhet ki. Ha a következőket választja: <br/><br/>**Megosztott**, a virtuálismag-foglalási kedvezmény a számlázási környezetben lévő bármely előfizetésben futó SQL Database-példányokra vonatkozik. A vállalati ügyfelek számára a megosztott hatókör a regisztráció, és a regisztráción belüli összes előfizetést tartalmazza. A használatalapú fizetéses ügyfelek esetében a megosztott hatókör a fiókadminisztrátor által létrehozott, használatalapú fizetéses előfizetéseket foglalja magában.<br/><br/>**Egyetlen előfizetés**, a virtuálismag-foglalási kedvezmény az ebben az előfizetésben lévő SQL Database-példányokra vonatkozik. <br/><br/>**Egyetlen erőforráscsoport**, a foglalási engedmény a kiválasztott előfizetésben lévő SQL Database-példányokra és az adott előfizetésen belül kiválasztott erőforráscsoportra vonatkozik.|
|Régió      |Az Sql Database által lefoglalt kapacitásfoglalás által lefedett Azure-régió.|
|Központi telepítés típusa|Az SQL erőforrástípus, amelyhez meg szeretné vásárolni a foglalást.|
|Teljesítményszint|Az SQL Database-példányok szolgáltatási szintje.
|Időtartam        |Egy vagy három éve.|
|Mennyiség    |Az SQL-adatbázis lefoglalt kapacitásfoglalásán belül vásárolt számítási erőforrások mennyisége. A mennyiség a kiválasztott Azure-régióban és a Teljesítmény szintben lévő virtuális magok száma, amelyek le vannak foglalva, és a számlázási engedményt kapják. Ha például az USA keleti régiójában a Gen5 16 virtuálismagok teljes számítási kapacitásával rendelkező SQL Database-példányokat futtatja vagy tervezi futtatni, akkor a mennyiséget 16-ként kell megadnia, hogy maximalizálja az összes példány előnyét. |

1. Tekintse át az SQL Database lefoglalt kapacitásfoglalásának költségét a **Költségek** szakaszban.
1. Válassza a **Beszerzés** lehetőséget.
1. Válassza **a Foglalás megtekintése** lehetőséget a vásárlás állapotának megtekintéséhez.

## <a name="cancel-exchange-or-refund-reservations"></a>Foglalások lemondása, cseréje vagy visszatérítése

Bizonyos korlátozásokkal lehetősége van a foglalások lemondására, cseréjére és visszatérítésére. További információkért lásd: [Az Azure Reservations önkiszolgáló csere- és visszatérítési szolgáltatásai](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

## <a name="vcore-size-flexibility"></a>virtuálismag-méret rugalmassága

A virtuális mag méretének rugalmassága segít a teljesítményszinten és a régión belül fel- vagy leskálázhatóként, a fenntartott kapacitáselőnyeinek elvesztése nélkül. Az SQL Database fenntartott kapacitása azt a rugalmasságot is biztosítja, hogy a fenntartott kapacitás elvesztése nélkül ideiglenesen áthelyezheti a gyakori elérésű adatbázisokat a készletek és az egyes adatbázisok között a normál műveletek részeként (ugyanazon a régión és teljesítményszinten). Előny. Ha egy nem alkalmazott puffert tart a foglalásban, hatékonyan kezelheti a teljesítménycsúcsokat anélkül, hogy túllépné a költségkeretet.

## <a name="limitation"></a>Korlátozás

DTU-alapú (alapszintű, szabványos vagy prémium) SQL-adatbázisok nem foglalhatók le.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>További lépések

A virtuálismag-foglalási kedvezmény automatikusan vonatkozik az SQL Database által fenntartott kapacitásfoglalási hatókörnek és attribútumnak megfelelő SQL Database-példányok számára. Frissítheti az SQL Database fenntartott kapacitásfoglalás hatókörét az [Azure Portalon](https://portal.azure.com), a PowerShellen, a CLI-n vagy az API-n keresztül.

Az SQL Database lefoglalt kapacitásfoglalásának kezeléséről az [SQL Database számára fenntartott kapacitás kezelése](../cost-management-billing/reservations/manage-reserved-vm-instance.md)című témakörben olvashat.

Az Azure Reservationszel kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mik azok az Azure-foglalások?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Azure-foglalások kezelése](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Az Azure Reservations-kedvezmény ismertetése](../cost-management-billing/reservations/understand-reservation-charges.md)
- [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [A foglalási kihasználtság ismertetése vállalati regisztrációnál](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [Azure Reservations a Partner Center felhőszolgáltatói (CSP) programjában](https://docs.microsoft.com/partner-center/azure-reservations)
