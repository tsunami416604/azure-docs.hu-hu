---
title: Fizessen elő az Azure SQL Database virtuális magot kapnak, pénzt takaríthat meg |} A Microsoft Docs
description: Ismerje meg, hogyan vásárolhat Azure SQL Database szolgáltatás számára fenntartott kapacitás a számítási költségek mentéséhez.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
manager: craigg
ms.date: 09/20/2018
ms.openlocfilehash: 05c237eb071b48f2373ecfd78eeab6f7bceb8c5c
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2018
ms.locfileid: "52584160"
---
# <a name="prepay-for-sql-database-compute-resources-with-azure-sql-database-reserved-capacity"></a>Fizessen elő az SQL-adatbázis számítási erőforrásokat, hogy az Azure SQL Database szolgáltatás számára fenntartott kapacitás

Pénzt takaríthat meg az Azure SQL Database az Azure SQL Database számítási erőforrások a használatalapú fizetéshez képest előre. Azure SQL Database szolgáltatás számára fenntartott kapacitás esetén módosítani egy előzetes kötelezettségvállalás az SQL Database egy vagy három évig, jelentős árkedvezményeket lekérni a számítási költségek. SQL Database szolgáltatás számára fenntartott kapacitás vásárlása, meg kell adnia az Azure-régió, a központi telepítési típus, a teljesítményszint és a kifejezés. 

Nem kell a Foglalás hozzárendelése az SQL Database-példányokat. SQL Database-példány, amelyeken már fut, vagy azokat, amelyeket újonnan telepített, automatikusan megkapja a juttatás megfelelő. A Foglalás megvásárlásával Ön előre fizetés a számítási költségek a SQL Database-példányok esetében egy vagy három évig. Amint vásárol egy foglalást, a számítási díjakat, amelyek megfelelnek a Foglalás attribútumok már nem számoljuk el a használatalapú-as-, SQL Database díjszabása nyissa meg. A foglalás nem fedi le szoftvereket, hálózati vagy tárolási díjak az SQL Database-példányhoz társított. A Foglalás időtartamára végén a számlázási ellátás lejár, és az SQL-adatbázisok számlázása a használatalapú – mint-akkor lépjen díj ellenében. Foglalások nem automatikus megújítási. Díjszabási információkért tekintse meg a [SQL Database szolgáltatás számára fenntartott kapacitás ajánlat](https://azure.microsoft.com/pricing/details/sql-database/managed/).

Azure SQL Database szolgáltatás számára fenntartott kapacitás megvásárolhatja a [az Azure portal](https://portal.azure.com). Az SQL Database szolgáltatás számára fenntartott kapacitás vásárlása:
- A tulajdonos szerepkör legalább egy vállalati vagy használatalapú fizetéses előfizetésre kell lennie.
- Az Enterprise-előfizetések, a foglalást az Azure-beli vásárlásokra engedélyezve kell lennie a [a nagyvállalati szerződések portáljának](https://ea.azure.com).
-  A Cloud Solution Provider (CSP) program keretében csak a felügyeleti ügynökök vagy értékesítési ügynökök vásárolhatja meg az SQL Database szolgáltatás számára fenntartott kapacitás.

A részleteket, hogyan vállalati ügyfelek és a használatalapú fizetéses ügyfelek számára számlázzuk foglalás vásárlásokra, az [ismertetése Azure foglalás használatát a nagyvállalati beléptetés](../billing/billing-understand-reserved-instance-usage-ea.md) és [megismerheti az Azure-foglalás használat a használatalapú fizetéses előfizetést](../billing/billing-understand-reserved-instance-usage.md).

## <a name="determine-the-right-sql-size-before-purchase"></a>Határozza meg a megfelelő SQL-méretet vásárlás előtt

Foglalási mérete alapján kell a meglévő vagy hamarosan –--telepíteni az SQL által használt számítási teljes mennyisége önálló adatbázisok és/vagy egy adott régión belül a rugalmas készletek és a teljesítmény szint és a hardver-generation használatával. 

Például tegyük fel, hogy futtatja egy általános célú, Gen5 – 16 virtuális mag rugalmas készletet, és két üzletileg kritikus, Gen5 – 4 virtuális mag önálló adatbázisok. További nézzük lehet, hogy szeretné-e a következő hónap belüli üzembe helyezett egy további általános célú, a Gen5 – 16 virtuális mag rugalmas készletet, és a egy üzletileg kritikus, Gen5 – 32 virtuális mag a rugalmas készlet. Ezenkívül tegyük fel, hogy ismeri, hogy ezeket az erőforrásokat kell legalább 1 évig. Ebben az esetben meg kell vásárolnia egy 32 mag (2 x 16), SQL Database egyetlen/rugalmas készletbeli általános célú - Compute, Gen5 és a egy 40 (2 x 4 + 32) 1 év foglalás az SQL Database egyetlen/rugalmas készletbeli üzletileg kritikus - Compute, Gen5 virtuális mag 1 év foglalás.

## <a name="buy-sql-database-reserved-capacity"></a>Az SQL Database szolgáltatás számára fenntartott kapacitás vásárlása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **minden szolgáltatás** > **foglalások**.
3. Válassza ki **Hozzáadás** és a termék típusának kiválasztása panelen válassza ki **SQL Database** , egy új foglalást vásárolhat SQL Database-hez.
4. Adja meg a kötelező mezőket. Meglévő vagy új önálló adatbázisokat vagy rugalmas készletek a kiválasztott attribútumok minősítéséhez beolvasni a fenntartott kapacitás kedvezményes megfelelő. SQL Database-példány, amely a kedvezmény a tényleges száma attól függ, a hatókör és a kiválasztott mennyiség.

   ![Képernyőkép, mielőtt beküldi az SQL Database szolgáltatás számára fenntartott kapacitás vásárlás](./media/sql-database-reserved-vcores/sql-reserved-vcores-purchase.png)

    | Mező      | Leírás|
    |:------------|:--------------|
    |Name (Név)        |A Foglalás neve.| 
    |Előfizetés|Az SQL Database szolgáltatás számára fenntartott kapacitás foglalási díjfizetéséhez használt előfizetés. A fizetési módot, az előfizetés fel van töltve az SQL Database szolgáltatás számára fenntartott kapacitás foglalás az előzetes költségek. Az előfizetés típusa kétféle lehet: nagyvállalati szerződés (ajánlatszám: MS-AZR-0017P) vagy használatalapú fizetéses (ajánlatszám: MS-AZR-0003P). Nagyvállalati előfizetésnél a díjak a regisztrációhoz tartozó keretek egyenlegeiből lesznek levonva, illetve túlhasználatként lesznek számlázva. Használatalapú fizetéses előfizetéseknél a díjakat az előfizetéshez tartozó hitelkártyára terheljük vagy a számlafizetési módnak megfelelően számlázzuk.|    
    |Hatókör       |A virtuális mag foglalás hatóköre egy előfizetés vagy több előfizetés (megosztott hatókör) is foglalkozik. Ha ki: <ul><li>Egyetlen előfizetés – a virtuális mag foglalási kedvezményt alkalmazza a rendszer SQL Database-példány ebben az előfizetésben. </li><li>Közös – a virtuális mag van alkalmazza a foglalási kedvezményt olyan előfizetéseket, a számlázási környezetben futó SQL Database-példányok. A vállalati ügyfelek a megosztott hatókörrel a regisztráció és előfizetéseken belül a regisztráció (kivéve a fejlesztési és tesztelési előfizetések) magában foglalja. Használatalapú fizetéses ügyfelek számára a megosztott hatókörrel a fiók rendszergazdája által létrehozott összes utólagos elszámolású előfizetések.</li></ul>|
    |Régió      |Az Azure-régióban, az SQL Database által fenntartott kapacitás foglalás.|    
    |Központi telepítés típusa|Az SQL erőforrás típusa, amely szeretné megvásárolni a foglalást.|
    |Teljesítményszint|Az SQL Database-példány szolgáltatási szintjei.
    |Időtartam        |Egy vagy három év.|
    |Mennyiség    |Az SQL Database megvásárolt példányainak számát fenntartott kapacitás foglalás. A mennyiség a futó kérheti le a számlázási kedvezményt SQL Database-példányok számát. Például ha 10 SQL Database-példányt futtat az USA keleti régiójában, majd kell megadni mennyiség 10 az összes futó gépek juttatása maximalizálása érdekében. |

5. Tekintse át az SQL-adatbázis költsége a szolgáltatás számára fenntartott kapacitás foglalása az **költségek** szakaszban.
6. Válassza a **Beszerzés** lehetőséget.
7. Válassza ki **megtekintése a Foglalás** a vásárlás állapotának megjelenítéséhez.

## <a name="cancellations-and-exchanges"></a>Lemondás és cseréje

Ha megszakítja az SQL Database szolgáltatás számára fenntartott kapacitás foglalás, előfordulhat, hogy a 12 %-os korai lemondási díj levonása után. A visszatérítést a rendszer a vételár vagy a foglalás jelenlegi ára közül az alacsonyabb összeg alapján számolja. A visszatérítések felső korlátja évi 50 000 dollár. A kapott visszatérítés tehát a fennmaradó, arányosított egyenleg és a 12%-os korai felmondási díj különbsége lesz. Visszavonást, nyissa meg az Azure Portalon, és válassza a Foglalás **visszatérítés** támogatási kérelmet szeretne létrehozni.

Az SQL Database szolgáltatás számára fenntartott kapacitás foglalás váltson egy másik régióba, központi telepítési típus, a teljesítményszint vagy kifejezés kell, ha egy másik, amely egyenlő vagy nagyobb értékű foglalás tudjon cserélni. Az új foglalási időszak kezdő dátuma nem kerül át a lecserélt foglalásból. Az 1 vagy 3 éves időszak az új foglalás létrehozásakor indul. Egy exchange kérést, nyissa meg a foglalást az Azure Portalon, és válassza **Exchange** támogatási kérelmet szeretne létrehozni.

## <a name="vcore-size-flexibility"></a>virtuális mag mérete rugalmasság

virtuális mag mérete rugalmasan segít kisebbre vagy nagyobbra méretezhetők a teljesítményszint és a régiót, a lefoglalt kapacitás juttatás elvesztése nélkül. Az SQL Database szolgáltatás számára fenntartott kapacitás emellett lehetőséget biztosít átmenetileg áthelyezheti a gyakori elérésű adatbázisok készletek és az önálló adatbázisok közötti (belül az azonos régióban és a teljesítmény szint) a normál működés részeként a lefoglalt kapacitás elvesztése nélkül előnyeit. Egy nem alkalmazott puffer tartja a foglalás, hatékonyan kezelheti a teljesítmény-használatnak a költségvetés túllépése nélkül.

## <a name="next-steps"></a>További lépések

A virtuális mag foglalási kedvezményt automatikusan alkalmazni, amelyek megfelelnek az SQL Database szolgáltatás számára fenntartott kapacitás foglalás hatóköre és attribútumok az SQL Database-példányok száma. Frissítheti a keresztül az SQL Database szolgáltatás számára fenntartott kapacitás foglalási hatókört [az Azure portal](https://portal.azure.com), PowerShell, CLI és az API-n keresztül. 

Megtudhatja, hogyan kezelheti az SQL Database szolgáltatás számára fenntartott kapacitás foglalás, lásd: [felügyelete az SQL Database szolgáltatás számára fenntartott kapacitás](../billing/billing-manage-reserved-vm-instance.md).

Azure-foglalások kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Mik az Azure-foglalásokat?](../billing/billing-save-compute-costs-reservations.md)
- [Az Azure Reservations kezelése](../billing/billing-manage-reserved-vm-instance.md)
- [Megismerheti a kedvezményes Azure-foglalások](../billing/billing-understand-reservation-charges.md)
- [A használatalapú fizetéses előfizetést foglalás használati adatai](../billing/billing-understand-reserved-instance-usage.md)
- [A nagyvállalati beléptetés foglalás használati adatai](../billing/billing-understand-reserved-instance-usage-ea.md)
- [A Partner Center Felhőszolgáltató (CSP) program Azure foglalások](https://docs.microsoft.com/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Segítség Kapcsolatfelvétel.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

