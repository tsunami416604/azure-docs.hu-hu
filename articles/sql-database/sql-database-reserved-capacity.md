---
title: Fizessen elő az Azure SQL Database virtuális magot kapnak, pénzt takaríthat meg |} A Microsoft Docs
description: Ismerje meg, hogyan vásárolhat Azure SQL Database szolgáltatás számára fenntartott kapacitás a számítási költségek mentéséhez.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
manager: craigg
ms.date: 02/08/2019
ms.openlocfilehash: ec9bd3ee106571484c513c2d005a374a90c1d17e
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59359712"
---
# <a name="prepay-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>Fizessen elő az SQL-adatbázis számítási erőforrásokat, hogy az Azure SQL Database szolgáltatás számára fenntartott kapacitás

Pénzt takaríthat meg az Azure SQL Database előre a számítási erőforrások a használatalapú fizetéshez képest. Azure SQL Database szolgáltatás számára fenntartott kapacitás esetén módosítani egy előzetes kötelezettségvállalás az SQL Database egy vagy három évig, jelentős árkedvezményeket lekérni a számítási költségek. SQL Database szolgáltatás számára fenntartott kapacitás vásárlása, meg kell adnia az Azure-régió, a központi telepítési típus, a teljesítményszint és a kifejezés.

Nem kell a Foglalás hozzárendelése meghatározott SQL Database-példányok (önálló adatbázisok, rugalmas készletek vagy felügyelt példányok). SQL Database-példány, amelyeken már fut, vagy azokat, amelyeket újonnan telepített, automatikusan megkapja a juttatás megfelelő. A Foglalás megvásárlásával Ön előre fizetés a számítási költségek egy vagy három éves időszakra. Amint vásárol egy foglalást, a számítási díjakat, amelyek megfelelnek a Foglalás attribútumok már nem számoljuk el a használatalapú-as-, SQL Database díjszabása nyissa meg. A foglalás nem fedi le szoftvereket, hálózati vagy tárolási díjak az SQL Database-példányhoz társított. A Foglalás időtartamára végén a számlázási ellátás lejár, és az SQL-adatbázisok számlázása a használatalapú – mint-akkor lépjen díj ellenében. Foglalások nem automatikus megújítási. Díjszabási információkért tekintse meg a [SQL Database szolgáltatás számára fenntartott kapacitás ajánlat](https://azure.microsoft.com/pricing/details/sql-database/managed/).

Azure SQL Database szolgáltatás számára fenntartott kapacitás megvásárolhatja a [az Azure portal](https://portal.azure.com). Az SQL Database szolgáltatás számára fenntartott kapacitás vásárlása:

- A tulajdonos szerepkör legalább egy vállalati vagy használatalapú fizetéses előfizetésre kell lennie.
- Vállalati előfizetés esetén **fenntartott példányok hozzáadása** engedélyezve kell lennie a [a nagyvállalati szerződések portáljának](https://ea.azure.com). Vagy, ha ez a beállítás le van tiltva, az előfizetés egy nagyvállalati szerződés rendszergazdájának kell lennie.
- A Cloud Solution Provider (CSP) program keretében csak a felügyeleti ügynökök vagy értékesítési ügynökök vásárolhatja meg az SQL Database szolgáltatás számára fenntartott kapacitás.

Hogyan vállalati ügyfelek és a használatalapú fizetéses ügyfelek számára számlázzuk foglalás beszerzésekhez részleteiért lásd: [Azure foglalás használati adatai a nagyvállalati beléptetés](../billing/billing-understand-reserved-instance-usage-ea.md) és [Azure foglalás ismertetése használat a használatalapú fizetéses előfizetést](../billing/billing-understand-reserved-instance-usage.md).

## <a name="determine-the-right-sql-size-before-purchase"></a>Határozza meg a megfelelő SQL-méretet vásárlás előtt

Foglalási mérete a meglévő vagy hamarosan –--telepíteni önálló adatbázisok, rugalmas készletek vagy egy adott régióban és a teljesítmény szint és a hardver-generation használatával felügyelt példányok által használt számítási teljes mennyisége alapján.

Például tegyük fel, hogy futtatja egy általános célú, Gen5 – 16 virtuális mag rugalmas készletet, és két üzletileg kritikus, Gen5 – 4 virtuális mag önálló adatbázisok. További nézzük lehet, hogy szeretné-e a következő hónap belüli üzembe helyezett egy további általános célú, a Gen5 – 16 virtuális mag rugalmas készletet, és a egy üzletileg kritikus, Gen5 – 32 virtuális mag a rugalmas készlet. Ezenkívül tegyük fel, hogy ismeri, hogy ezeket az erőforrásokat kell legalább 1 évig. Ebben az esetben meg kell vásárolnia egy 32 mag (2 x 16), 1 év foglalás egyetlen adatbázishoz és rugalmas készlet általános célú - Gen5 és a egy 40 (2 x 4 + 32) a tárolókészlet üzletileg kritikus - Gen5 virtuális mag 1 év foglalás egyetlen adatbázishoz és rugalmas.

## <a name="buy-sql-database-reserved-capacity"></a>Az SQL Database szolgáltatás számára fenntartott kapacitás vásárlása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **minden szolgáltatás** > **foglalások**.
3. Válassza ki **Hozzáadás** és a termék típusának kiválasztása panelen válassza ki **SQL Database** , egy új foglalást vásárolhat SQL Database-hez.
4. Adja meg a kötelező mezőket. Meglévő vagy új önálló adatbázisok, rugalmas készletek vagy a kiválasztott attribútumok felügyelt példányokat beolvasni a fenntartott kapacitás kedvezményes jogosultak. SQL Database-példány, amely a kedvezmény a tényleges száma attól függ, a hatókör és a kiválasztott mennyiség.

   ![Képernyőkép, mielőtt beküldi az SQL Database szolgáltatás számára fenntartott kapacitás vásárlás](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

    | Mező      | Leírás|
    |:------------|:--------------|
    |Name (Név)        |A Foglalás neve.|
    |Előfizetés|Az SQL Database szolgáltatás számára fenntartott kapacitás foglalási díjfizetéséhez használt előfizetés. A fizetési módot, az előfizetés fel van töltve az SQL Database szolgáltatás számára fenntartott kapacitás foglalás az előzetes költségek. Az előfizetés típusúnak kell lennie a nagyvállalati szerződés (számok kínálnak: MS-AZR-0017P vagy MS-AZR - 0148 P) vagy a használatalapú fizetés (számok kínálnak: MS-AZR-0003P vagy MS-AZR - 0023 P). Nagyvállalati előfizetésnél a díjak a regisztrációhoz tartozó keretek egyenlegeiből lesznek levonva, illetve túlhasználatként lesznek számlázva. Használatalapú fizetéses előfizetéseknél a díjakat az előfizetéshez tartozó hitelkártyára terheljük vagy a számlafizetési módnak megfelelően számlázzuk.|
    |Hatókör       |A virtuális mag foglalás hatóköre egy előfizetés vagy több előfizetés (megosztott hatókör) is foglalkozik. Ha ki: <br/><br/>**Egy előfizetés** -rendszer alkalmazza a virtuális mag foglalási kedvezményt SQL Database-példány ebben az előfizetésben. <br/><br/>**Előfizetés megosztott** – a virtuális mag foglalási kedvezményt a számlázási környezetben előfizetésekkel példányai az SQL Database vonatkozik. A vállalati ügyfelek a megosztott hatókörrel a regisztráció és a regisztráció belüli összes előfizetésre tartalmazza. Használatalapú fizetéses ügyfelek számára a megosztott hatókörrel a fiók rendszergazdája által létrehozott összes utólagos elszámolású előfizetések.|
    |Régió      |Az Azure-régióban, az SQL Database által fenntartott kapacitás foglalás.|
    |Központi telepítés típusa|Az SQL erőforrás típusa, amely szeretné megvásárolni a foglalást.|
    |Teljesítményszint|Az SQL Database-példány szolgáltatási szintjei.
    |Időtartam        |Egy vagy három év.|
    |Mennyiség    |Az SQL Database megvásárolt példányainak számát fenntartott kapacitás foglalás. A mennyiség a futó kérheti le a számlázási kedvezményt SQL Database-példányok számát. Például ha 10 SQL Database-példányt futtat az USA keleti régiójában, majd kell megadni mennyiség 10 az összes futó gépek juttatása maximalizálása érdekében. |
    |||

5. Tekintse át az SQL-adatbázis költsége a szolgáltatás számára fenntartott kapacitás foglalása az **költségek** szakaszban.
6. Válassza a **Beszerzés** lehetőséget.
7. Válassza ki **megtekintése a Foglalás** a vásárlás állapotának megjelenítéséhez.

## <a name="cancellations-and-exchanges"></a>Lemondás és cseréje

Ha megszakítja az SQL Database szolgáltatás számára fenntartott kapacitás foglalás, előfordulhat, hogy a 12 %-os korai lemondási díj levonása után. A visszatérítést a rendszer a vételár vagy a foglalás jelenlegi ára közül az alacsonyabb összeg alapján számolja. A visszatérítések felső korlátja évi 50 000 dollár. A kapott visszatérítés tehát a fennmaradó, arányosított egyenleg és a 12%-os korai felmondási díj különbsége lesz. Visszavonást, nyissa meg az Azure Portalon, és válassza a Foglalás **visszatérítés** támogatási kérelmet szeretne létrehozni.

Ha a fenntartott Azure SQL Database-kapacitás lefoglalását egy másik régióra, üzembe helyezési típusra, teljesítményszintre vagy eltérő feltételekre kell módosítania, akkor kicserélheti a foglalást egy azonos vagy magasabb értékűre. Az új foglalási időszak kezdő dátuma nem kerül át a lecserélt foglalásból. Az 1 vagy 3 éves időszak az új foglalás létrehozásakor indul. Egy exchange kérést, nyissa meg a foglalást az Azure Portalon, és válassza **Exchange** támogatási kérelmet szeretne létrehozni.

Exchange-hez vagy a visszatérítés foglalások kapcsolatos további információkért lásd: [foglalás cseréje és a visszatérítéseket](../billing/billing-azure-reservations-self-service-exchange-and-refund.md).

## <a name="vcore-size-flexibility"></a>virtuális mag mérete rugalmasság

virtuális mag mérete rugalmasan segít kisebbre vagy nagyobbra méretezhetők a teljesítményszint és a régiót, a lefoglalt kapacitás juttatás elvesztése nélkül. Az SQL Database szolgáltatás számára fenntartott kapacitás emellett lehetőséget biztosít átmenetileg áthelyezheti a gyakori elérésű adatbázisok készletek és az önálló adatbázisok közötti (belül az azonos régióban és a teljesítmény szint) a normál működés részeként a lefoglalt kapacitás elvesztése nélkül előnyeit. Egy nem alkalmazott puffer tartja a foglalás, hatékonyan kezelheti a teljesítmény-használatnak a költségvetés túllépése nélkül.

## <a name="next-steps"></a>További lépések

A virtuális mag foglalási kedvezményt automatikusan alkalmazni, amelyek megfelelnek az SQL Database szolgáltatás számára fenntartott kapacitás foglalás hatóköre és attribútumok az SQL Database-példányok száma. Frissítheti a keresztül az SQL Database szolgáltatás számára fenntartott kapacitás foglalási hatókört [az Azure portal](https://portal.azure.com), PowerShell, CLI és az API-n keresztül.

Megtudhatja, hogyan kezelheti az SQL Database szolgáltatás számára fenntartott kapacitás foglalás, lásd: [SQL Database szolgáltatás számára fenntartott kapacitás kezelése](../billing/billing-manage-reserved-vm-instance.md).

Azure-foglalások kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mi az az Azure Reservations?](../billing/billing-save-compute-costs-reservations.md)
- [Az Azure-fenntartások kezelése](../billing/billing-manage-reserved-vm-instance.md)
- [Megismerheti a kedvezményes Azure-foglalások](../billing/billing-understand-reservation-charges.md)
- [A használatalapú fizetéses előfizetést foglalás használati adatai](../billing/billing-understand-reserved-instance-usage.md)
- [A nagyvállalati beléptetés foglalás használati adatai](../billing/billing-understand-reserved-instance-usage-ea.md)
- [A Partner Center Felhőszolgáltató (CSP) program Azure foglalások](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Segítség Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).
