---
title: "Az Azure SQL adatbázis Azure esettanulmány - Umbraco |} Microsoft Docs"
description: "Megtudhatja, hogyan Umbraco használ történő gyors kiépítésére SQL-adatbázis és a bérlők a felhőben akár több ezer méretezési szolgáltatásaival kapcsolatban"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 5243d31e-3241-4cb0-9470-ad488ff28572
ms.service: sql-database
ms.custom: reference
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Inactive
ms.date: 01/10/2017
ms.author: carlrab
ms.openlocfilehash: c25a66daa87da96d4e77c9021a1ceb4366d7a224
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="umbraco-uses-azure-sql-database-to-quickly-provision-and-scale-services-for-thousands-of-tenants-in-the-cloud"></a>Umbraco használja az Azure SQL Database gyors kiépítése és a skála szolgáltatások a felhőben bérlők akár több ezer
![Umbraco embléma](./media/sql-database-implementation-umbraco/umbracologo.png)

Umbraco egy olyan népszerű nyílt forráskódú tartalom-kezelő rendszer (CMS) futtatható semmit kis kampány vagy ismertetőben helyekről összetett alkalmazások Fortune 500 vállalatok és a globális media webhelyek számára. 

> "Van elég nagy közösségi fejlesztőkkel több mint 100 000 fórumainkat, és több mint 350,000 helyeket, amelyek élő, Umbraco fut a rendszer használó fejlesztők számára."
> 
> – Péter sem Christensen, vezető, Umbraco
> 
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-Case-Study-Umbraco/player]
> 
> 

Egyszerűbbé teheti a felhasználói telepítés, Umbraco hozzáadott Umbraco,--szolgáltatás (UaaS): a szoftver,--szolgáltatás (SaaS) ajánlat, amely szükségtelenné teszi a helyszíni központi telepítések, a beépített skálázás, és eltávolítja felügyeleti terhelés, mivel lehetővé teszi a fejlesztők számára a termék megoldás helyett innováció kezelésére. Umbraco el tudja-e az adott előnyök kihasználása érdekében a Microsoft Azure által kínált rugalmas platform,--szolgáltatás (PaaS) modell megtalálható.

UaaS lehetővé teszi, hogy a Szolgáltatottszoftver-ügyfelek számára a Umbraco CMS lehetőségeket, amelyek korábban már azok a reach kívül. Ezen ügyfelek ki van építve a CMS munkakörnyezet, amely tartalmazza az éles adatbázis. Az ügyfelek fejlesztési és tesztelési környezetek, attól függően, hogy a rájuk vonatkozó követelményeket legfeljebb két további adatbázisokat adhat hozzá. Ha egy új környezetre van szükség, egy automatikus folyamat rendeli hozzá, hogy a felhasználói adatbázis automatikusan. Az új adatbázist (másodpercben), készen áll, mert az adatbázis van már előre kiépítve által Umbraco az elérhető adatbázisok (lásd az 1. ábra) Azure rugalmas készletből.

![Umbraco létesítési életciklusa](./media/sql-database-implementation-umbraco/figure1.png)

1. ábra. Üzembe helyezési életciklusa Umbraco szolgáltatásként (UaaS)

## <a name="azure-elastic-pools-and-automation-simplify-deployments"></a>Az Azure rugalmas készletek és automatizálás egyszerűsítése érdekében a központi telepítések
Az Azure SQL Database és más Azure-szolgáltatásokkal Umbraco ügyfelek önálló építhető ki a környezetben, és Umbraco könnyen figyelheti és kezelheti az adatbázisokat egy egyszerűen elsajátítható munkafolyamat részeként:

1. Üzembe helyezés
   
   Umbraco 200 elérhető előtti ponton aktívvá vált-adatbázisok rugalmas készletek kapacitású tart fenn. Egy új ügyfél előfizet UaaS, Umbraco biztosít, ha az ügyfél egy új CMS környezettel közel valós idejű hozzárendelésével egy adatbázist a rendelkezésre állási készletből.
   
   Amikor egy rendelkezésre állási készlet eléri az küszöböt, létrejön egy új rugalmas készletet, és új adatbázisok előtti ponton aktívvá vált, szükség esetén az ügyfelek hozzárendelni.
   
   Megvalósítási teljesen automatizált C# kezelési kódtárakat, és az Azure Service Bus-üzenetsorok használatával.
2. Használata
   
   Az ügyfelek a saját adatbázis egy-három környezetekben (a termelési, átmeneti, és/vagy fejlesztői), minden egyes használja. Felhasználói adatbázisokat rugalmas készletek, amely lehetővé teszi a számára biztosítanak a hatékony méretezést anélkül, hogy túlzott kiépítéséhez Umbraco szerepelnek.
   
   ![Umbraco projekt áttekintése](./media/sql-database-implementation-umbraco/figure2.png)
   
   ![Umbraco projekt részletei](./media/sql-database-implementation-umbraco/figure3.png)
   
   2. ábra Projekt áttekintése és a részleteket megjelenítő Umbraco,--szolgáltatás (UaaS) ügyfél-webhely
   
   Az Azure SQL-adatbázis adatbázis-tranzakciós egységek (dtu-k) használ a relatív power valós adatbázis-tranzakció szükséges. A UaaS ügyfelek adatbázisok általában körülbelül 10 dtu-k működnek, de mindegyik rendelkezik-e a rugalmasságát méretezhetőség az igény szerinti. Ez azt jelenti, hogy UaaS is biztosítja, hogy az ügyfelek mindig szükséges erőforrásokat, akár csúcsidőben. Például a legutóbbi vasárnap sport események esetén egy UaaS tapasztalt ügyféladatbázis csúcsaira legfeljebb 100 dtu-k a game időtartama. Az Azure rugalmas készletek végzett a teljesítmény csökkenése nélkül, hogy a nagy érdeklődés támogatásához Umbraco.
3. Figyelés
   
   Umbraco figyelők adatbázis-tevékenység az Azure portálon, és egyéni e-mailes riasztásokhoz irányítópultok használata.
4. Vészhelyreállítás
   
   Azure két vészhelyreállítás (DR) lehetőségeket is kínál: aktív georeplikáció és georedundáns helyreállítás. A vész-Helyreállítási lehetőség, amely egy vállalati ki kell jelölni függ a [üzleti folytonosságot biztosító célok](sql-database-business-continuity.md).
   
   aktív georeplikáció biztosít a leggyorsabb válasz leállás esetén. Aktív georeplikációt használ, a különböző régiókban található kiszolgálók négy olvasható másodlagos adatbázis hozható létre, és feladatátvételt a másodlagos adatbázis meghibásodása majd kezdeményezheti.
   
   Umbraco a georeplikáció nem igényel, de azt előnyeit Azure georedundáns helyreállítás minimális állásidő érdekében a nem tervezett kimaradás esetén. georedundáns helyreállítás adatbázis a biztonsági másolatok georedundáns Azure storage támaszkodik. Amely lehetővé teszi a felhasználók számára egy biztonsági másolatból állítsa vissza, ha kimaradás az elsődleges régióban található.
5. Deaktiválás kiépítése
   
   A projekt környezetekben a törölt a rendszer eltávolítja a társított adatbázisokban (fejlesztési, előkészítési vagy élő), Azure Service Bus várólista törlése során. Az automatikus folyamat visszaállítja a nem használt adatbázisok Umbraco a rugalmas adatbázis-rendelkezésre állási készletbe, így maximális kihasználtsági megőrzésével jövőbeli kialakítási elérhetővé válnak.

## <a name="elastic-pools-allow-uaas-to-scale-with-ease"></a>Rugalmas készletek méretezése a könnyű UaaS engedélyezése
Azure rugalmas készletek kihasználva, Umbraco is teljesítményének optimalizálása ügyfeleinek over - vagy hiány rendelkezés nélkül. Umbraco van majdnem 3000 olyan adatbázisok segítségével könnyen méretezését, a szükséges, a meglévő 325,000 ügyfelek vagy az új felhasználók, akiket a felhőben a CMS üzembe helyezésére befogadásához 19 rugalmas készletek között.

Valójában megfelelően Péter sem Christensen műszaki vezethet, Umbraco, "UaaS most tapasztalható növekedése körülbelül 30 új ügyfelek naponta. Az ügyfelek a kényelmi célokat szolgál, hogy az új projektek kiépítése másodpercben, azonnal közzéteheti a élő webhelyek "Egykattintásos központi telepítéssel" fejlesztési környezetből, amely nem nagyon, és ugyanúgy, mint gyorsan módosításokat, ha találnak hibák."

Ha az ügyfél nem igényel többé egy második, illetve harmadik környezetben, egyszerűen távolíthat el ezeket a környezetben. Amely szabaddá válnak más ügyfelek számára a Umbraco rugalmas adatbázis-rendelkezésre állási készlet részeként használható erőforrásokat.

![Umbraco üzembe helyezési architektúrája](./media/sql-database-implementation-umbraco/figure4.png)

3. ábra. A Microsoft Azure UaaS üzembe helyezési architektúrája

## <a name="the-path-from-datacenter-to-cloud"></a>A felhőalapú adatközpont elérési útja
Ha a Umbraco fejlesztők kezdetben a Szolgáltatottszoftver-modell váltana, azok tudta, hogy a szolgáltatás felé költséghatékony, méretezhető úgy kell.

> "a rugalmas készletek az SaaS ajánlott, mert azt felfelé és lefelé igény szerint tárcsázza kapacitás tökéletes. Kiépítés egyszerű, és a telepítés részeként, azt is megtarthatják az kihasználtsági legfeljebb."
> 
> – Péter sem Christensen, vezető, Umbraco
> 
> 

"Akartunk az időt a felhasználók problémák megoldásával kapcsolatos nem kezelheti az infrastruktúrát. A legtöbb érték, a felhasználók könnyen meg akartunk"szerint Niels Hartvig, Umbraco a alapító. "Kezdeti minősül a kiszolgálók ragozott formáival üzemeltető, de a kapacitástervezés lett volna a nightmare." Helyezi Umbraco nem alkalmaz minden adatbázis-rendszergazdák UaaS használatának legfontosabb értékajánlatához aláhúzásjeleket tartalmazhatnak, amelyek.

Egy fontos célja a Umbraco fejlesztők számára lehetővé teszik a UaaS ügyfelek környezetek kiépítéséhez, gyorsan és a kapacitás korlátozások nélkül lett. De Umbraco adatközpontokban dedikált üzemeltetett szolgáltatást biztosító lenne szükség nagy mennyiségű többletkapacitással felszakadásáig feldolgozás kezelésére. Amely akkor kell azt jelentette, hogy jelentős számítási infrastruktúrát használ, rendszeresen túl hozzáadása.

Emellett a Umbraco fejlesztői csapat kívánta olyan megoldás, amely lehetővé tenné a lehető legtöbb lehető a meglévő kódot ismét felhasználni azokat. Mikkel Madsen állapota Umbraco fejlesztőjeként, "volt elégedett a Microsoft fejlesztői eszközök, amelyeket már ismeri a, például a Microsoft SQL Server, Microsoft Azure SQL-adatbázis, az ASP.net és az Internet Information Services (IIS). Az infrastruktúra-szolgáltatási vagy egy PaaS erőforrásokba történő beruházások előtt a felhő megoldás, győződjön meg arról, hogy az volna támogatja a Microsoft-eszközöket és a platformok, így azt nem kell bekövetkezett nagymértékű változások alap kódban, győződjön meg akartunk. "

A feltételek mindegyikének megfelelő, Umbraco következő megfelel a cloud partner keresést végrehajtani:

* Elegendő kapacitása és a megbízhatóság
* Támogatja a Microsoft fejlesztői eszközök, Umbraco mérnökök volna nem kényszeríthető, saját fejlesztői környezetükben teljesen reinvent
* Jelen az összes a földrajzi piacok, amelyben UaaS sávszélességen (Győződjön meg arról, hogy azok is hozzáférjenek az adataikhoz gyorsan és, hogy azok adatai egy olyan helyre, a regionális szabályozási követelményeknek megfelelő vállalatok szükség)

## <a name="why-umbraco-chose-azure-for-uaas"></a>Miért Umbraco Azure számára választott UaaS
Szerinti Péter sem Christensen "után annak eldöntéséhez, hogy a beállítások, jelenleg kijelölt Azure azt minden el a feltételeket, kezelhetőségi és a méretezhetőség ismeretét, és a költséghatékonyság telepítve. Azure virtuális gépeken környezetek beállítása, és minden környezet rendelkezik a saját Azure SQL Database-példányt, a rugalmas készletek példányainak. Adatbázisok közötti fejlesztési, átmeneti és élő környezetben elválasztva robusztus teljesítmény-elszigetelés érdekében egyeztetni kell a méretezési felhasználóknak is kínált – hatalmas win. "

Péter sem szűnik meg, "előtt, le kellett manuálisan telepíteni a kiszolgálókra webes adatbázisokhoz. Most azt nem kell foglalkoznia. Minden automatizált – a kiépítéstől a tisztítás. "

Péter sem az is problémamentesen működjön együtt az Azure által biztosított méretezési képességeket. "a rugalmas készletek az SaaS ajánlott, mert azt felfelé és lefelé igény szerint tárcsázza kapacitás tökéletes. Kiépítés egyszerű, és a telepítés részeként, azt is megtarthatják az kihasználtsági legfeljebb." Péter sem állapotok, "az egyszerű, a rugalmas készletek szolgáltatási réteg-alapú dtu-k, benne együtt kínálja nekünk a az igény szerinti új erőforráskészletek kiépítéséhez. Nagyobb ügyfeleink egyik nemrégiben, 100 dtu-k az élő környezetben való csúcsos. A rugalmas készletek megadott Azure használatával, az erőforrásokat, amelyek valós időben szükség rájuk anélkül, hogy előre jelezni DTU követelményei a felhasználói adatbázisokat. Egyszerűen csak put, ügyfeleink beolvasása a válaszidő, amely arra számítanak, és azt megfelel a teljesítmény szolgáltatásiszint-szerződések."

Mikkel Madsen összegzi azt: "Azt korábban elfogadott a hatékony Azure algoritmus, amely egy általános Szolgáltatottszoftver-forgatókönyv (bevezetése az új ügyfelek léptékű valós időben) az alkalmazás típusnak (adatbázisok, mindkét fejlesztési előzetes kiépítése és működés közbeni) felett az alapul szolgáló technológiát (Azure Service Bus-üzenetsorok használata az Azure SQL Database szolgáltatással együtt)."

## <a name="with-azure-uaas-is-exceeding-customer-expectations"></a>Az Azure-UaaS meghaladja ügyfél verziójával kapcsolatos elvárások
Hálózatiadapter-Azure-bA a felhő partnerrel, Umbraco óta kell biztosítania a UaaS ügyfelek optimalizált Tartalomkezelés teljesítménnyel, anélkül, hogy az IT-erőforrás befektetés egy önálló üzemeltetett megoldás szükséges. Péter sem szerint, mert "azt meghallgatnánk a fejlesztők kényelme és a méretezhetőséget, hogy Azure biztosítanak számunkra, és az ügyfelek thrilled a szolgáltatások és a megbízhatóság. A teljes lett egy nagyszerű win nekünk!"

## <a name="more-information"></a>További információ
* Az Azure rugalmas készletek kapcsolatos további információkért lásd: [rugalmas készletek](sql-database-elastic-pool.md).
* Azure Service Bus kapcsolatos további információkért lásd: [Azure Service Bus](https://azure.microsoft.com/services/service-bus/).
* Virtuális hálózat kapcsolatos további információkért lásd: [virtuális hálózat](https://azure.microsoft.com/documentation/services/virtual-network/).    
* Biztonsági mentési és helyreállítási kapcsolatos további információkért lásd: [az üzletmenet folytonossága](sql-database-business-continuity.md).    
* Ppols figyelésével kapcsolatos további tudnivalókért lásd: [figyelése erőforráskészleteket](sql-database-elastic-pool-manage-portal.md).    
* Szolgáltatásként Umbraco kapcsolatos további információkért lásd: [Umbraco](https://umbraco.com/cloud).

