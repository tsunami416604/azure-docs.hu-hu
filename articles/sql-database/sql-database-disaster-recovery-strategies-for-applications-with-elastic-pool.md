---
title: A vészhelyreállítási megoldások – Azure SQL-adatbázis megtervezése |} A Microsoft Docs
description: Ismerje meg, hogyan tervezhető a vész-helyreállítási felhőalapú megoldás a megfelelő feladatátvételi minta kiválasztásával.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-poolss
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
manager: craigg
ms.date: 08/27/2018
ms.openlocfilehash: d8614272e60327510c58cf87b70725fc256ed378
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53273002"
---
# <a name="disaster-recovery-strategies-for-applications-using-sql-database-elastic-pools"></a>Vészhelyreállítási stratégiák SQL Database rugalmas készleteket használó alkalmazások

Az évek, hogy megismerte, a cloud services rendszer nem üzembiztos, és a katasztrofális események történik. Az SQL Database ezek az incidensek előfordulásakor az alkalmazás az üzletmenet folytonosságának biztosításához a különböző képességeket biztosít. [Rugalmas készletek](sql-database-elastic-pool.md) és önálló adatbázisokat támogatja a vész-helyreállítási lehetőségei a azonos típusú. Ez a cikk bemutatja több Vészhelyreállítási stratégiák rugalmas adatbáziskészletekhez, amelyek esetében használhatja ezeket az SQL Database üzletmenet-folytonossági funkciókat.

Ez a cikk a következő kanonikus SaaS ISV alkalmazásmintát használ:

A modern felhőalapú webalkalmazások egy SQL-adatbázis kiépítése a végfelhasználók számára. A független Szoftvergyártók számos ügyfél rendelkezik, és ezért a számos adatbázis, más néven bérlői adatbázisokat használ. A bérlői adatbázisok jellemzően kiszámíthatatlan tevékenység mintával rendelkeznek, mert a független Szoftvergyártók rugalmas készlet használ, hogy nagyon előre jelezhető költségek az adatbázis kiterjesztett időszakokra. A rugalmas készlet emellett leegyszerűsíti az alkalmazásteljesítmény-felügyelet, ha a felhasználói tevékenység hirtelen megugró kihasználtság. A bérlői adatbázisok mellett az alkalmazás is használ több adatbázist kezelheti a felhasználói profilok, biztonság, gyűjtése a használati minták stb. Az egyes bérlők rendelkezésre állását ne befolyásolja az alkalmazás rendelkezésre állásának teljes. Azonban a rendelkezésre állás és a felügyeleti adatbázisok teljesítményének fontos a függvény az alkalmazást, és offline állapotban van a felügyeleti adatbázisok esetén a teljes alkalmazás kapcsolat nélküli üzemmódban.

Ez a cikk ismerteti, a költség-és nagybetűket indítási alkalmazások kiépítettektől szigorú rendelkezésre állási követelmények vonatkoznak, a forgatókönyvek széles Vészhelyreállítási stratégiát.

> [!NOTE]
> Ha prémium szintű és az üzletileg kritikus adatbázisok és rugalmas készletek használ, akkor is használhatja őket rugalmas a regionális üzemkimaradások utáni helyreállításon zóna redundáns üzembe helyezési konfiguráció átalakításával. Lásd: [zónaredundáns adatbázisok](sql-database-high-availability.md).

## <a name="scenario-1-cost-sensitive-startup"></a>1. forgatókönyv. Költség-és nagybetűket indítása

Startup-vállalkozással vagyok és vagyok rendkívül költség-és nagybetűket.  Egyszerűbb telepítés és az alkalmazás felügyelete szeretnék, és az egyes ügyfelek is van korlátozva szolgáltatói szerződés. De, egy egész soha nem kapcsolat nélküli üzemmódban, győződjön meg arról, hogy az alkalmazás szeretnék.

Az egyszerűség kedvéért a követelmény teljesítéséhez egy rugalmas készlet tetszőleges Azure-régióban helyezze üzembe az összes bérlői adatbázison, és üzembe helyezése önálló adatbázisok georeplikált felügyeleti adatbázisra. A bérlők vész-helyreállítási használja a geo-visszaállítás, amelyben további költségek nélkül. Ahhoz, hogy a felügyeleti adatbázisok rendelkezésre állását, georeplikáció őket egy másik régióba egy automatikus feladatátvételi csoport (1. lépés). Ebben a forgatókönyvben a vész-helyreállítási konfiguráció folyamatos költsége megegyezik a másodlagos adatbázisok teljes költségét. Ez a konfiguráció a következő diagramon látható.

![1. ábra](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-1.png)

Egy kimaradás során az elsődleges régióban, a helyreállítási lépéseket ahhoz, hogy az alkalmazás online által a következő ábra mutatja be.

* A feladatátvételi csoport felügyeleti-adatbázisnak a Vészhelyreállítási régióban automatikus feladatátvételt kezdeményez. Az alkalmazás automatikusan újrakapcsolódik az új elsődleges és minden új fiókok számára, és a bérlői adatbázisok jönnek létre a Vészhelyreállítás régióban található. A meglévő ügyfelek tekintheti meg saját átmenetileg nem érhető el.
* A rugalmas készlet létrehozása az eredeti készlet (2) azonos konfigurációjú.
* A geo-visszaállítás használatával másolatokat készíteni a a bérlői adatbázisok (3). Fontolja meg az egyes visszaállítások elindítása a végfelhasználói kapcsolatok által, vagy néhány más alkalmazás-specifikus prioritású előtaggal.

Ezen a ponton az alkalmazás újra online állapotba kerül a Vészhelyreállítás régióban található, de az egyes ügyfelek késleltetést tapasztal, az adatok elérése közben.

![2. ábra](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-2.png)

Ha a szolgáltatáskimaradás elhárítása után ideiglenes volt, lehetséges, hogy az elsődleges régió helyreállítása az Azure-ban, előtt a Vészhelyreállítás régióban található összes adatbázis-visszaállítás befejeződött. Ebben az esetben koordinálása áthelyezése az alkalmazás az elsődleges régióba. A folyamat a következő diagramon bemutatott lépéseket vesz igénybe.

* Összes függőben lévő geo-visszaállítás kérelem megszakítása
* Az elsődleges régió (5) a felügyeleti adatbázisok feladatátvételét. A régió helyreállítása után a régi elsődleges automatikusan váltak másodlagos példány hozható létre. Most már térnek szerepkörök újra.
* Módosítsa az alkalmazás kapcsolati karakterláncában, mutasson az elsődleges régióba. Most már minden új fiókokat és a bérlői adatbázisok jönnek létre az elsődleges régióba. Egyes meglévő ügyfelek tekintheti meg saját átmenetileg nem érhető el.
* Minden adatbázis csak olvasható, győződjön meg arról, nem módosítható a DR régióban (6) a DR készletet állítja be.
* Minden egyes adatbázishoz a DR-készlet, a helyreállítás óta megváltozott nevezze át vagy törölje a megfelelő készleten belül lévő adatbázisok az elsődleges (7).
* Másolja a frissített adatbázisok a DR-készletből az elsődleges készlet (8).
* Törölni a DR-készletet (9)

Ezen a ponton az alkalmazás érhető el az összes bérlő érhető el a készletben található adatbázisok elsődleges az elsődleges régióban.

![3. ábra](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-3.png)

A kulcs **előnyeit** ebben a stratégiában az adatredundancia szint alacsony folyamatos költségeket. Biztonsági mentés automatikusan megnyílik az SQL Database szolgáltatás nincs alkalmazás átírás és további költségek nélkül.  A költsége akkor lesz felszámítva, csak akkor, ha a rugalmas adatbázisok visszaállítását végzi. A **kompromisszum** , hogy az összes bérlői adatbázis teljes helyreállítás jelentős ideig tart. Mennyi ideig attól függ, a teljes száma a visszaállítások kezdeményezheti a Vészhelyreállítás régióban található, és a bérlői adatbázisok teljes méretét. Akkor is, ha az egyes bérlők számára visszaállítások keresztül másoknak, rangsorolja, minden a többi visszaállítások ugyanabban a régióban, a szolgáltatás arbitrates, és a meglévő ügyfelek adatbázisok teljes gyakorolt hatás minimalizálása érdekében a szabályozza felé indított vannak versenyben. Emellett a bérlői adatbázisok helyreállítását nem lehet elindítani, mindaddig, amíg az új rugalmas készlet a DR régióban jön létre.

## <a name="scenario-2-mature-application-with-tiered-service"></a>2. forgatókönyv. Érett alkalmazás rétegzett szolgáltatással

Én vagyok a rétegzett szolgáltatási ajánlatok és a különböző SLA-k próbaverziós ügyfelek és az ügyfelek és a egy érett SaaS-alkalmazáshoz. A próbaverziós felhasználók rendelkezem a lehető legnagyobb mértékben költségek csökkentése érdekében. Próbaverziós felhasználók is igénybe vehet az állásidő, de szeretnék csökkenteni annak a valószínűségét. A fizető ügyfelek a leállás annak a flight kockázata. Győződjön meg arról, hogy már fizetőssé szeretném ügyfelek képesek mindig hozzáférhetnek az adataikhoz.

Ez a forgatókönyv támogatása érdekében külön a próbabérlőket fizetős bérlőtől azáltal, külön rugalmas készletekbe. A próbaverzió ügyfélnél alacsonyabb edtu-k vagy virtuális magok / bérlő és a egy hosszabb helyreállítási idő az alacsonyabb SLA. A fizető ügyfeleket egy készletet a magasabb edtu-k vagy virtuális magok / bérlő és a egy magasabb SLA találhatók. A legalacsonyabb helyreállítási idő biztosításához a fizető ügyfeleket bérlői adatbázisok georeplikált. Ez a konfiguráció a következő diagramon látható.

![4. ábra](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-4.png)

Az első forgatókönyv, mint a felügyeleti adatbázisok aktívak meglehetősen így egy georeplikált adatbázis használhatja, (1). Ez biztosítja, hogy a kiszámítható teljesítményt az új ügyfél-előfizetések, profil-frissítések és egyéb felügyeleti műveleteket. A régió, amelyben az elsődleges felügyeleti adatbázisok találhatók az elsődleges régióban, és a régiót, amelyben a másodlagos felügyeleti adatbázisok találhatók a Vészhelyreállítási régióban.

A fizető ügyfeleket bérlői adatbázisok aktív adatbázisok rendelkezik az elsődleges régióban üzembe helyezett "fizetős" készletben. Egy másodlagos készletet a DR régióban ugyanazzal a névvel. Minden egyes bérlőhöz georeplikált másodlagos készletéhez (2). Ez lehetővé teszi az összes bérlői adatbázis feladatátvételi gyors helyreállítás.

Egy kimaradás során az elsődleges régióban, a helyreállítási lépéseket ahhoz, hogy az alkalmazás online kapcsolatait mutatja be a következő diagram:

![5. ábra](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-5.png)

* A Vészhelyreállítási régióban (3) a felügyeleti adatbázisok azonnali feladatátvételt.
* Módosítsa az alkalmazás kapcsolati karakterláncában, hogy a Vészhelyreállítási régióban mutasson. Most már minden új fiókokat és a bérlői adatbázisok jönnek létre a Vészhelyreállítás régióban található. A meglévő próbaverzió ügyfelek tekintheti meg saját átmenetileg nem érhető el.
* A készlethez a DR régióban azonnal visszaállítani a rendelkezésre állásuk (4) a fizetős bérlői adatbázisok feladatátvételét. Mivel a feladatátvétel egy gyors metaadatok szint módosítása, fontolja meg az optimalizálás, ahol az egyes feladatátvételeket igény szerinti által aktivált a végfelhasználói kapcsolatokat.
* Ha a másodlagos készlet edtu-k méretének vagy virtuális mag értéke alacsonyabb, mint az elsődleges volt, mert a másodlagos adatbázisok csak a módosítási naplója feldolgozása, miközben a másodlagos példány hozható létre voltak a kapacitás szükséges, azonnal készlet kapacitásának növelése most már a teljes terhelés befogadásához az összes bérlő (5).
* Az új rugalmas készlet létrehozása ugyanazzal a névvel, és ugyanazt a konfigurációt a próbaverziós felhasználók adatbázisok (6) a Vészhelyreállítás régióban található.
* A próbaverziós felhasználók készlet létrehozása után a geo-visszaállítás segítségével az egyes próbaverziós bérlői adatbázisok visszaállítása az új készletbe (7). Fontolja meg a végfelhasználói kapcsolatok által az egyes visszaállítások elindítása vagy néhány más alkalmazás-specifikus prioritású előtaggal.

Ezen a ponton az alkalmazás újra online állapotba kerül a Vészhelyreállítás régióban található. Az összes fizető ügyfeleket férhetnek hozzá az adataikhoz kell számolnia, amíg a próbaverziós felhasználók késleltetést tapasztal, az adatok elérése közben.

Amikor helyreállítja az elsődleges régió Azure *után* visszaállította a DR régióban, az alkalmazás futtatása az adott régióban továbbra is az alkalmazást, vagy beállíthatja úgy, hogy a feladat-visszavételhez az elsődleges régióba. Ha az elsődleges régió hasznosítják *előtt* a feladatátvétel befejeződött, azonnal érdemes lehet feladat-visszavétel. A feladat-visszavételi fogadja a következő ábra szemlélteti a lépéseket:

![6. ábra.](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-6.png)

* Összes függőben lévő geo-visszaállítás kérelem megszakítása
* Átadja a feladatokat a felügyeleti adatbázisok (8). A régió helyreállítása után a régi elsődleges automatikusan a másodlagos válnak. Most újra lesz az elsődleges.  
* Átadja a feladatokat a fizetős bérlői adatbázisok (9). Hasonlóképpen a régió helyreállítása után a régi elsődleges automatikusan válnak a másodlagos példány hozható létre. Most ismét válnak eredményezi.
* Állítsa be a visszaállított próbaverziós adatbázisok, amelyek megváltoztak a DR régióban csak olvasható (10).
* Minden egyes adatbázishoz a próbaverziós felhasználók DR-készlet, amely a helyreállítás óta nevezze át vagy törölje a megfelelő adatbázist a próbaverziós felhasználók elsődleges készletben (11).
* Másolja a frissített adatbázisok a DR-készletből az elsődleges készlet (12).
* A DR-készlet (13) törlése.

> [!NOTE]
> A feladatátvételi művelet aszinkron. A helyreállítási idő minimalizálása érdekében fontos, hogy legalább 20 adatbázis kötegekben hajtsa végre a bérlői adatbázisok feladatátvételi parancsot.

A kulcs **előnyeit** Ez a stratégia az, hogy a fizető ügyfeleket biztosítja a legmagasabb szintű SLA-t. Emellett biztosítja azt, hogy az új kísérletek fel oldva, amint a próbaverziós DR-készlet jön létre. A **kompromisszum** fizetős ügyfeleknek, hogy a telepítő növeli a teljes költség a bérlői adatbázisok esetében a másodlagos Vészhelyreállítási készlet költségei. Emellett ha a másodlagos készlet egy másik méretet, a fizető ügyfeleket élmény kisebb teljesítményt a feladatátvételt követően a Vészhelyreállítás régióban található a készlet frissítés befejeződéséig.

## <a name="scenario-3-geographically-distributed-application-with-tiered-service"></a>3. forgatókönyv. Földrajzilag elosztott alkalmazás rétegzett szolgáltatással

Van egy rétegzett szolgáltatási ajánlatok az érett SaaS-alkalmazáshoz. Szeretnék saját fizetős ügyfeleknek kínált rendkívül agresszív szolgáltatásszint-szerződéssel és minimálisra gyakorolt hatás kockázatok, amikor a leállások esetén, mert még rövid megszakítás ügyfél elégedetlenségét okozhatnak. Rendkívül fontos, hogy a fizető ügyfeleket mindig elérheti az adatokat. Az ingyenes próbaverziók és SLA-t a próbaidőszak alatt nem érhető el.

A forgatókönyv támogatása érdekében használja a három különálló rugalmas készleteket. Két azonos méretűek készletek nagy edtu-k vagy a virtuális magok adatbázisonkénti a fizetős ügyfelek bérlői adatbázisokat tartalmazó két különböző régióban üzembe helyezheti. A próbabérlőket tartalmazó harmadik készlet lehet alacsonyabb edtu-k vagy virtuális magok adatbázisonkénti és építhető ki egy két régiót.

Garantálja a legalacsonyabb helyreállítási idő leállások során, a fizető ügyfeleket bérlői adatbázisok, georeplikált 50 %-a két régió az elsődleges adatbázisok. Ehhez hasonlóan az egyes régiókban 50 %-a másodlagos adatbázisok rendelkezik. Így ha egy régió offline állapotban van, csak 50 %-a fizetős ügyfelei adatbázisainak érintett és kell átadja a feladatokat. Az egyéb adatbázisok változatlanok maradnak. Ez a konfiguráció a következő ábra mutatja be:

![4. ábra](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-7.png)

Mivel az előző esetben a felügyeleti adatbázisok még aktív így őket, egyetlen georeplikált-adatbázisok konfigurálása (1). Ez biztosítja az új ügyfél kiszámítható teljesítményt, előfizetések, a profil frissítéseket és az egyéb felügyeleti műveleteket. Terület A az elsődleges régióban, a felügyeleti adatbázisok és a régiót a B felügyeleti adatbázisok helyreállítási szolgál.

A fizető ügyfeleket bérlői adatbázisok georeplikált is, de az elsődleges és másodlagos példány hozható létre felosztása terület A és B (2) régió között. Ezzel a módszerrel negatív hatással a szolgáltatáskiesés megszüntetése után a bérlői elsődleges adatbázisokat is átirányíthatja a más régióba, és elérhetővé válik. A másik fele a bérlői adatbázisokat nem lehet minden érintett.

A következő ábra szemlélteti a régióban. a szolgáltatáskimaradás esetén elvégzendő lépések

![5. ábra](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-8.png)

* Azonnal átadja a feladatokat a felügyeleti adatbázisok régióba B (3).
* Módosítsa az alkalmazás kapcsolati karakterláncában, ellenőrizze, hogy az új fiókokat és a bérlői adatbázisok jönnek létre a régióban B és a meglévő bérlői adatbázisok találhatók van, valamint a felügyeleti adatbázisok régió módosítása b felügyeleti adatbázishoz mutasson. A meglévő próbaverzió ügyfelek tekintheti meg saját átmenetileg nem érhető el.
* Készlethez azonnal a rendelkezésre állásuk (4) visszaállítása a B régióban 2 a fizetős bérlői adatbázisok feladatátvételét. Mivel a feladatátvétel egy gyors metaadatok szint módosítása, akkor fontolja meg az optimalizálás, ahol az egyes feladatátvételeket igény szerinti által aktivált a végfelhasználói kapcsolatokat.
* Most óta a készletben 2 csak az elsődleges adatbázisok, a készlet nő a teljes terhelés tartalmaz, és azonnal edtu-k méretének (5) vagy virtuális magok számának növeléséhez.
* Az új rugalmas készlet létrehozása ugyanazzal a névvel, és ugyanazt a konfigurációt a próbaverziós felhasználók adatbázisok (6) a B a régióban.
* A készlet létrehozása után a geo-visszaállítás segítségével állíthatja vissza az egyes próbaverziós bérlői adatbázis (7) a készletbe. Fontolja meg az egyes visszaállítások elindítása a végfelhasználói kapcsolatok által, vagy néhány más alkalmazás-specifikus prioritású előtaggal.

> [!NOTE]
> A feladatátvételi művelet aszinkron. A helyreállítási idő minimalizálása érdekében fontos, hogy legalább 20 adatbázis kötegekben hajtsa végre a bérlői adatbázisok feladatátvételi parancsot.

Ezen a ponton az alkalmazás újra online állapotba kerül régióban B. Az összes fizető ügyfeleket férhetnek hozzá az adataikhoz kell számolnia, amíg a próbaverziós felhasználók késleltetést tapasztal, az adatok elérése közben.

Terület A állítható helyre, ha el kell döntenie, hogy szeretné-e régió B használandó próbaverziós ügyfelek vagy próbaverziós ügyfelek tárolókészletet használja. a régióban történő feladat-visszavétel Egy adott feltétel lehet a % próbaverziós bérlői adatbázisok a helyreállítás óta módosítva. Függetlenül attól, hogy döntést kell újra egyensúlyba a fizetős bérlők két készletek között. a következő ábra a folyamatát mutatja be, amikor a próbaverziós bérlői adatbázisok a feladat-visszavételhez régió A.  

![6. ábra.](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-9.png)

* Minden nyitott geo-visszaállítás kérelemhez próbakészletben DR megszakítása
* Átadja a feladatokat a felügyeleti adatbázisban (8). A régió helyreállítása után a régi elsődleges automatikusan a másodlagos vált. Most újra lesz az elsődleges.  
* Válassza ki, melyik fizetős bérlői adatbázisok a feladat-visszavételhez készlet 1 – kezdeményezési feladatátvételt a másodlagos példány hozható létre (9). A régió helyreállítása után 1 készletben található összes adatbázis automatikusan lett másodlagos példány hozható létre. Most már 50 %-át őket elsődleges ismét válik.
* Az eredeti eDTU (10) vagy a virtuális magok száma 2 készlet méretének csökkentésére.
* A készlet összes csak olvasható (11) B régióban próbaverziós adatbázisok visszaállítva.
* A helyreállítás óta megváltozott DR próbakészletben lévő egyes adatbázisokhoz nevezze át vagy törölje a megfelelő adatbázist az elsődleges próbakészletben (12).
* Másolja a frissített adatbázisok a DR-készletből az elsődleges készlet (13).
* A DR-készlet (14) törlése.

A kulcs **előnyöket** ezen stratégia vannak:

* A legtöbb agresszív SLA támogatja a fizető ügyfelek számára, biztos lehet benne, hogy nem egy szolgáltatáskimaradás hatással az több mint 50 %-a bérlői adatbázisok.
* Ez garantálja, hogy az új kísérletek fel oldva, amint az eljáráshoz DR-készlet jön létre a helyreállítás során.
* Lehetővé teszi a hatékonyabb használat 50 %-a másodlagos adatbázisok készletben 1 tárolókészlet-kapacitást, és a készlet 2 garantáltan kevésbé aktív, mint az elsődleges adatbázisok.

A fő **kompromisszummal** vannak:

* Szemben a management adatbázisok CRUD-műveletek alacsonyabb késéssel rendelkeznek, a B régió csatlakozik, azok végrehajtásakor az elsődleges felügyeleti adatbázisok ellen a végfelhasználók szolgáltatáshoz kapcsolódnak a régióban, mint A végfelhasználók számára.
* A felügyeleti adatbázisban összetettebb kialakításától van szükség. Például minden bérlői rekord, egy helyen címke, amely a feladatátvétel és feladat-visszavétel során módosítani kell.  
* A fizető ügyfeleket a szokásosnál alacsonyabb teljesítményt tapasztalhat, mindaddig, amíg a régió B készlet frissítése befejeződött.

## <a name="summary"></a>Összegzés

Ez a cikk az adatbázisszint SaaS ISV több-bérlős alkalmazás által használt vész-helyreállítási stratégiákról összpontosít. A választott stratégia az alkalmazások, például az üzleti modellben, az SLA-t szeretné az ügyfelek számára kínálnak, költségvetés-megkötés stb igényeit alapul. Egyes ismertetett stratégiák előnyeit és kompromisszum ismerteti, így sikerült tájékozott döntést. Az adott alkalmazás valószínűleg is más Azure-összetevőket. Ezért tekintse át az üzleti folytonossági útmutatást és előkészíthető a velük az adatbázisszint helyreállítása. Helyreállítás az adatbázis-alkalmazások az Azure-beli kezelésével kapcsolatos további tudnivalókért tekintse meg [tervezése felhőalapú megoldások vész-helyreállítási](sql-database-designing-cloud-solutions-for-disaster-recovery.md).  

## <a name="next-steps"></a>További lépések

* További információ az Azure SQL Database automatikus biztonsági mentések, lásd: [SQL-adatbázis automatikus biztonsági mentések](sql-database-automated-backups.md).
* Egy üzleti folytonosság – áttekintés és forgatókönyvek: [üzleti folytonosság – áttekintés](sql-database-business-continuity.md).
* Az automatikus biztonsági másolatokból helyreállítási kapcsolatos további információkért lásd: [adatbázis visszaállítása a szolgáltatás által létrehozott biztonsági másolatokból](sql-database-recovery-using-backups.md).
* Gyorsabb helyreállítási beállítások kapcsolatos további információkért lásd: [aktív georeplikáció](sql-database-active-geo-replication.md) és [automatikus feladatátvételi csoportok](sql-database-auto-failover-group.md).
* Az automatikus biztonsági másolatokból archiválásra kapcsolatos további információkért lásd: [adatbázis másolása](sql-database-copy.md).
