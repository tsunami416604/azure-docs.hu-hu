---
title: "Vész-helyreállítási megoldások – az Azure SQL Database kialakítása |} Microsoft Docs"
description: "Útmutató a felhőalapú megoldás vész-helyreállítási terv válassza ki a megfelelő feladatátvételi mintát."
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: monicar
ms.assetid: 2db99057-0c79-4fb0-a7f1-d1c057ec787f
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 12/13/2017
ms.author: sashan
ms.reviewer: carlrab
ms.workload: Inactive
ms.openlocfilehash: 9d12fb8a7dbd3bb763e42fd0981d7ef18b57248b
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/14/2017
---
# <a name="disaster-recovery-strategies-for-applications-using-sql-database-elastic-pools"></a>Vész helyreállítási stratégiát az SQL Database rugalmas készleteket használó alkalmazások
Az évek azt megtanulhatta, győződjön meg arról, hogy felhőszolgáltatások nem biztos és katasztrofális incidensek olyan esetben fordulhat elő. SQL-adatbázis ezeket az incidensek előfordulásakor az üzletmenet folytonosságát, az alkalmazás így különböző képességeket biztosít. [Rugalmas készletek](sql-database-elastic-pool.md) és az önálló adatbázisok támogatja a vész-helyreállítási funkciók azonos típusú. Ez a cikk ismerteti a több vész-Helyreállítási stratégiát, a rugalmas készletbe, amely kihasználja ezeket az SQL-adatbázis üzleti folytonosságot biztosító szolgáltatásokat.

Ez a cikk a következő kanonikus SaaS ISV-alkalmazás mintát használ:

<i>A modern felhőalapú webalkalmazás minden végfelhasználó látja el egy SQL-adatbázis. Az ISV sok ügyfél rendelkezik, és ezért a sok más néven a bérlői adatbázisok használja. Mivel a bérlői adatbázisok általában előre nem látható tevékenység mintázatok, az ISV költségeket, az adatbázis nagyon kiszámíthatóbbá teszi hosszú időn keresztül rugalmas készlethez használ. A rugalmas készlet is leegyszerűsíti teljesítmény kezelését, ha a felhasználói tevékenység napra. A bérlői adatbázisok mellett az alkalmazás is használó adatbázisok a kezelheti a felhasználói profilokat, biztonsági gyűjtése a használati minták stb. Az egyes bérlők rendelkezésre állását ne befolyásolja az alkalmazás rendelkezésre állásának teljes. Azonban a rendelkezésre állás és a felügyeleti adatbázisok teljesítménye fontos a az alkalmazás függvény, és ha a felügyeleti adatbázisok offline offline állapotban-e a teljes alkalmazás.</i>  

A cikk ismerteti a lehetőségeket rendelkező szigorú rendelkezésre állási követelmények bizalmas indítási alkalmazások költség számos vész-Helyreállítási stratégiát.

## <a name="scenario-1-cost-sensitive-startup"></a>1. forgatókönyv. Költség-és nagybetűket indítása
<i>I indítási üzleti vagyok, és rendkívül vagyok költség-és nagybetűket.  Egyszerűbb telepítés és az alkalmazás felügyelete szempontjából szeretnék, és egy korlátozott SLA-t az egyes ügyfelek is van. Azonban mivel a teljes soha nem offline állapotban, győződjön meg arról, hogy az alkalmazás kívánt.</i>

Az egyszerűség kedvéért követelmény teljesítéséhez összes bérlői adatbázis üzembe helyezés egy rugalmas készlet a kiválasztott Azure-régióban, és felügyeleti adatbázisok önálló adatbázisok georeplikált szerint telepítheti. A bérlő vész-helyreállítási használja a georedundáns helyreállítás, amely minden további költség nélkül. Ahhoz, hogy a felügyeleti adatbázisok rendelkezésre állását, földrajzi-replikálja őket egy másik régióba egy automatikus feladatátvételi csoport (az előzetes verzió) (1. lépés). A teljes költség, a másodlagos adatbázisok a vészhelyreállítási konfigurációban ebben a forgatókönyvben a folyamatban lévő költségét megegyezik. Ez a konfiguráció a következő diagramon látható.

![1. ábra](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-1.png)

Nem tervezett kimaradás esetén az elsődleges régióban, a helyreállítási lépéseket kapcsolja a hálózatra az alkalmazás által a következő ábra mutatja.

* A feladatátvételi csoport felügyeleti-adatbázisnak a vész-Helyreállítási régió automatikus feladatátvételt kezdeményez. Az alkalmazás automatikusan újra létrehozza az új elsődleges és minden új fiókok és a bérlői adatbázisok a vész-Helyreállítási régióban jönnek létre. A meglévő ügyfeleknek tekintse meg az adatok átmenetileg nem érhető el.
* A rugalmas készlet létrehozása az eredeti készlet (2) azonos konfigurációjú.
* Georedundáns helyreállítás segítségével készítsen másolatot a bérlő adatbázisok (3). Vegye figyelembe a végfelhasználói kapcsolatok által az egyes visszaállítások váltanak, vagy néhány más alkalmazás-specifikus prioritás sémát használja.


Ezen a ponton az alkalmazás újra online állapotba kerül a vész-Helyreállítási régióban, de néhány ügyfél tapasztalhat a késleltetés, az adatok elérése közben.

![2. ábra](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-2.png)

A leállás ideiglenes volt, akkor lehetséges, hogy az elsődleges régióban helyreállíthatók az Azure-ban, előtt a vész-Helyreállítási régióban összes adatbázis visszaállítása befejeződött. Ebben az esetben levezényelni helyezze át az alkalmazást az elsődleges régióban. A folyamat végrehajtja a következő diagramon ábrázolt műveleteket.

* Összes függőben lévő georedundáns helyreállítás kérelem megszakítása.   
* Feladatok átadása a felügyeleti adatbázisokat az elsődleges régióban (5). A régió helyreállítása után a régi elsődleges automatikusan váltak másodlagos. Most vált a szerepkörök újra. 
* Módosítsa az alkalmazás kapcsolati karakterláncot mutathat vissza az elsődleges régióban. Most már minden új fiókokat és a bérlői adatbázisok az elsődleges régióban jönnek létre. Egyes meglévő ügyfelek tekintse meg az adatok átmenetileg nem érhető el.   
* Állítsa be az összes adatbázis a vész-Helyreállítási készlet csak olvasható annak érdekében, hogy ezeket nem lehet módosítani a vész-Helyreállítási régióban (6). 
* Az egyes adatbázisok a vész-Helyreállítási készlet, amely a helyreállítás óta megváltozott nevezze át vagy törölje a megfelelő adatbázisokat az elsődleges készletben (7). 
* Másolja a frissített adatbázisok a vész-Helyreállítási készlet az elsődleges készlet (8). 
* Törli a vész-Helyreállítási készletet (9)

Ezen a ponton az alkalmazás érhető el az összes bérlői érhető el a készletben lévő adatbázisok elsődleges elsődleges régióban.

![3. ábra](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-3.png)

A kulcs **előnyeit** ezt a stratégiát az adatredundanciát réteg alacsony folyamatban lévő költsége. Biztonsági mentések automatikusan készül az SQL Database szolgáltatás nincs alkalmazás átdolgozás és minden további költség nélkül.  A költsége van szükség, csak akkor, ha a rugalmas adatbázis visszaállítását végzi. A **kompromisszum** , hogy a teljes helyreállítás összes bérlői adatbázis jelentős időt vesz igénybe. Mennyi ideig attól függ, az összes indítja el a vész-Helyreállítási régióban visszaállítások száma és a bérlői adatbázis teljes mérete. Akkor is, ha az egyes bérlők számára visszaállítások keresztül mások helyezi előtérbe, akkor az összes a más visszaállítások kezdeményezett ugyanabban a régióban, a szolgáltatás arbitrates és azelőtt gyorsítja fel a meglévő ügyfeleknek adatbázisok gyakorolt teljes hatás minimalizálása érdekében verseng. Emellett a bérlő adatbázisok helyreállítási nem tudja elindítani, amíg a vész-Helyreállítási régióban új rugalmas készlet jön létre.

## <a name="scenario-2-mature-application-with-tiered-service"></a>2. forgatókönyv. Érett alkalmazás rétegzett szolgáltatással
<i>Én vagyok a rétegzett szolgáltatási ajánlatok és különböző SLA-k próba felhasználók és az ügyfelek fizető érett SaaS-alkalmazáshoz. A próba ügyfelek van lehetőség szerint a költségek csökkentése. Próbaverziós felhasználók is igénybe vehet az állásidőt, de annak valószínűsége csökkenteni szeretnék. A fizető vevők leállási esetén a felhőszolgáltató közötti átviteléhez kockázata. Győződjön meg arról, hogy fizető kívánt, az ügyfelek képesek mindig hozzáférjenek az adataikhoz.</i> 

Ez a forgatókönyv támogatása érdekében a próbabérlőket eltérő, külön fizetős bérlők által külön rugalmas készletek üzembe őket. A próba alacsonyabb eDTU-bérlők és az alacsonyabb SLA-t és a hosszabb helyreállítási idő rendelkeznek. A fizető vevők egy magasabb eDTU-bérlő és a magasabb SLA-t a készletben vannak. A legalacsonyabb helyreállításkor biztosításához a fizető vevők bérlői adatbázisok a következők georeplikált. Ez a konfiguráció a következő diagramon látható. 

![4. ábra](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-4.png)

Az első forgatókönyv, mint a felügyeleti adatbázisok aktívak üzembe helyezését, hogy használni egy georeplikált adatbázist (1) az. Ez biztosítja a kiszámítható teljesítmény új ügyfél-előfizetések, a profil frissítések és az egyéb felügyeleti műveleteket. A régió, amelyben eredményezi a felügyeleti adatbázis található az elsődleges régióban, a régiót, amelyben a másodlagos felügyeleti adatbázis található pedig a vész-Helyreállítási régióban.

A fizető vevők bérlői adatbázisok aktív adatbázisok van a "fizetős" tárolókészletben az elsődleges régióban kiépítve. Ezzel a névvel, a vész-Helyreállítási régióban másodlagos készlet kiépítéséhez. Mindegyik bérlő georeplikált másodlagos készletéhez (2). Ez lehetővé teszi, hogy a gyors helyreállításának összes bérlői adatbázis feladatátvétel használatával. 

Nem tervezett kimaradás esetén az elsődleges régióban, az alkalmazás online állapotba a helyreállítási lépéseket mutatja be a következő diagram:

![5. ábra](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-5.png)

* A vész-Helyreállítási régióban (3) a felügyeleti adatbázisok azonnal feladatátvételt.
* Módosítsa az alkalmazás kapcsolati karakterláncot a vész-Helyreállítási régió mutassanak. Most már minden új fiókokat és a bérlői adatbázisok a vész-Helyreállítási régióban jönnek létre. A meglévő próba felhasználók tekintse meg az adatok átmenetileg nem érhető el.
* Feladatok átadása a fizetős bérlő adatbázisokat a készlethez, a vész-Helyreállítási régióban azonnal visszaállítása a rendelkezésre állásuk (4). Mivel a feladatátvételt a gyors metaadatok szint módosítása, fontolja meg az optimalizálás, ahol az egyes feladatátvételek lépésüket az igény szerinti a végfelhasználói kapcsolatok. 
* Ha a másodlagos készlet edtu-k mérete alacsonyabb, mint az elsődleges volt, mert a másodlagos adatbázisok csak a módosítási napló feldolgozása, miközben a másodlagos adatbázisok voltak kapacitás szükséges, azonnal növelje a készlet kapacitásának most már egyetlen bérlő számára [5], a teljes munkaterhelés befogadásához. 
* Hozzon létre új rugalmas készlet ugyanazt a nevet és a próbaverziós felhasználók adatbázisok (6) a vész-Helyreállítási régióban azonos konfigurációval. 
* A próba felhasználók készlet létrehozása után a georedundáns helyreállítás használatával állítsa vissza az egyes próbabérlet adatbázisokat az új készletbe (7). Vegye figyelembe, a végfelhasználói kapcsolatok által az egyes visszaállítások váltanak, vagy néhány más alkalmazás-specifikus prioritás sémát használja.

Ezen a ponton az alkalmazás újra online állapotba a vész-Helyreállítási régióban. Minden fizető vevők férhetnek hozzá az adataikhoz rendelkezik, amíg a próba ügyfél tapasztalhat késleltetés az adatok elérése közben.

Ha az elsődleges régióban Azure hasznosítják *után* visszaállította az alkalmazás a vész-Helyreállítási régióban folytathatja az alkalmazás futtatása az adott régióban, vagy beállíthatja úgy, hogy visszaadják feladataikat az elsődleges régióban. Ha az elsődleges régióban állítja helyre a rendszer *előtt* a feladatátvétel befejeződött, javasoljuk, hogy visszavétele azonnal. A feladat-visszavétel fogadja el a következő ábra szemlélteti a lépéseket: 

![6. ábra](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-6.png)

* Összes függőben lévő georedundáns helyreállítás kérelem megszakítása.   
* Feladatok átadása a felügyeleti adatbázisok (8). A régió helyreállítása után a régi elsődleges automatikusan a másodlagos válik. Most újra lesz az elsődleges.  
* Feladatok átadása a fizetős bérlői adatbázisok (9). Hasonlóképpen a régió helyreállítása után a régi elsődleges automatikusan vált a másodlagos adatbázis. Most ismét válnának eredményezi. 
* Állítsa be a visszaállított próba adatbázisok, amely, a vész-Helyreállítási régióban csak olvasható (10).
* Az egyes adatbázisok helyreállítási óta megváltoztak a próbaverziós felhasználók DR-készletben nevezze át vagy törölje a megfelelő adatbázist a próbaverziós felhasználók elsődleges készletben (11). 
* Másolja a frissített adatbázisok a vész-Helyreállítási készlet az elsődleges készlet (12). 
* [13] DR-készlet törlése 

> [!NOTE]
> A feladatátvételi művelet aszinkron. A helyreállítási idő minimalizálása érdekében fontos, hogy a bérlő adatbázisok feladatátvételi parancs legalább 20 adatbázisok kötegekben hajtható végre. 
> 
> 

A kulcs **előnyeit** ezt a stratégiát arra, hogy a fizető vevők biztosít a legmagasabb szolgáltatásiszint-szerződést. Emellett biztosítja azt, hogy az új kísérletek feloldja-e, amint a vész-Helyreállítási próbakészletben jön létre. A **kompromisszum** ügyfelek fizetős, hogy a telepítő növeli a teljes költség, a bérlő adatbázisok által a másodlagos DR-készletben költségét. Ezenkívül-e a másodlagos készlet különböző méretű, a fizető vevők alacsonyabb teljesítmény tapasztalható feladatátvételt követően a készlet frissítése a vész-Helyreállítási régióban befejeződéséig. 

## <a name="scenario-3-geographically-distributed-application-with-tiered-service"></a>3. forgatókönyv. Földrajzilag elosztott alkalmazás rétegzett szolgáltatással
<i>A rétegzett szolgáltatási ajánlatok érett SaaS-alkalmazás van. Szeretnék rendkívül agresszív SLA nyújtsanak a fizetős ügyfeleknek, és minimálisra csökkenthetők a járulékos hatással lehet az, ha valamilyen okból kimaradás fordul elő, mert még rövid megszakítás ügyfél kapcsolatos okozhat. Nagyon fontos, hogy a fizető vevők mindig is hozzáférjenek az adataikhoz. A próbaverzió szabad és a próbaidőszak alatt nem ajánlott a szolgáltatásiszint-szerződésben garantált.</i> 

A forgatókönyv támogatása érdekében használjon három különálló rugalmas készletek. A magas edtu-k adatbázisonkénti magában foglalja a fizetős ügyfelek bérlői adatbázisok két különböző régiókban két egyenlő méretű rendelkezik kiépítéséhez. A próbabérlőket tartalmazó harmadik készlet lehet alacsonyabb edtu-k adatbázisonkénti és építhető ki a két régiók egyikéhez sem.

A legalacsonyabb helyreállítás ideje alatt kimaradások biztosításához a fizető vevők bérlői adatbázisok a következők georeplikált 50 %-a két régió elsődleges adatbázis. Hasonlóképpen minden egyes régió van 50 %-a másodlagos adatbázisok. Ezzel a módszerrel a régió nem érhető el, ha a fizetős ügyfelek adatbázisok csak 50 %-át érintett és kell feladatátadáshoz használhat. Az egyéb adatbázisok változatlanok maradnak. Ez a konfiguráció a következő ábrán látható:

![4. ábra](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-7.png)

Mivel az előző esetben a felügyeleti adatbázisok aktívak meglehetősen úgy állítsa be ezeket georeplikált az önálló adatbázisok (1). Ez biztosítja az új ügyfél kiszámítható teljesítmény, előfizetések, a profil frissítések és az egyéb felügyeleti műveleteket. A régió az elsődleges régióban, a felügyeleti adatbázisok és a régió B használt felügyeleti adatbázisok helyreállításához.

A fizető vevők bérlői adatbázisok georeplikált is, de az elsődleges és másodlagos elosztva terület A és B (2) a régióban. Ezzel a módszerrel a bérlő elsődleges adatbázisok, a kimaradást okozó negatív hatással is átveheti a többi régiónál és elérhetővé válnak. A másik fele a bérlő adatbázisok nem kell minden érintett. 

A következő diagram azt ábrázolja, a helyreállítási lépésekkel régióban A. nem tervezett kimaradás esetén

![5. ábra](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-8.png)

* Azonnal feladatátvételt a B régióban (3) a felügyeleti adatbázisok.
* Módosítsa az alkalmazás kapcsolati karakterláncot, hogy az új fiókokat és a bérlői adatbázisok B régióban jönnek létre, és a meglévő bérlő adatbázisok találhatók van, valamint a felügyeleti adatbázisok régióban B. Módosítsa a felügyeleti adatbázisok mutasson. A meglévő próba felhasználók tekintse meg az adatok átmenetileg nem érhető el.
* Feladatok átadása a fizetős bérlő adatbázisok készlethez régióban B azonnal visszaállítása a rendelkezésre állásuk (4) 2. Mivel a feladatátvételt a gyors metaadatok szint módosítása, akkor fontolja meg az optimalizálás, ahol az egyes feladatátvételek lépésüket az igény szerinti a végfelhasználói kapcsolatok. 
* Most óta készlet 2 csak az elsődleges adatbázist, az alkalmazáskészlet növeli a teljes munkaterhelés tartalmaz, és azonnal növelheti a eDTU méretét (5). 
* Hozzon létre új rugalmas készlet neve és a próbaverziós felhasználók adatbázisok (6) a B régióban azonos konfigurációval. 
* A készlet létrehozása után a georedundáns helyreállítás használatával állítsa vissza az egyes próbabérlet adatbázist (7) készletbe. Vegye figyelembe a végfelhasználói kapcsolatok által az egyes visszaállítások váltanak, vagy néhány más alkalmazás-specifikus prioritás sémát használja.

> [!NOTE]
> A feladatátvételi művelet aszinkron. A helyreállítási idő minimalizálása érdekében fontos, hogy a bérlő adatbázisok feladatátvételi parancs legalább 20 adatbázisok kötegekben hajtható végre. 
> 

Ezen a ponton az alkalmazás újra online állapotba kerül régióban a b kiszolgálóra. Minden fizető vevők férhetnek hozzá az adataikhoz rendelkezik, amíg a próba ügyfél tapasztalhat késleltetés az adatok elérése közben.

A régió helyreállításakor el kell döntenie, ha a próbaverziós felhasználók vagy a feladat-visszavétel használja a próbaverziós felhasználók régióban A. régió B használni kívánt Egy feltétel lehet módosítható, mert a helyreállítási próbabérlet-adatbázisok %. Függetlenül attól, hogy döntést kell újra egyensúlyba kell hoznia a fizetős bérlők között két rendelkezik. a következő ábra a folyamatát mutatja be, amikor a próbabérlet adatbázisok visszaadják feladataikat A. régió  

![6. ábra](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-9.png)

* Megszakítja a vész-Helyreállítási próbakészletbe tartozó összes függőben lévő georedundáns helyreállítás kérelem.   
* Feladatok átadása a felügyeleti adatbázisban (8). A régió helyreállítása után a régi elsődleges automatikusan a másodlagos inaktívvá vált. Most újra lesz az elsődleges.  
* Válassza ki, melyik fizetős bérlői adatbázisok visszaadják feladataikat készlet 1 – kezdeményezési feladatátvételt a másodlagos adatbázis (9). A régió helyreállítását követően 1 alkalmazáskészletben lévő összes adatbázis automatikusan másodlagos inaktívvá vált. Most 50 %-át őket elsődleges ismét válik. 
* Az eredeti edtu-k (10) 2-készlet méretének csökkentése.
* Készlet összes B régióban próba adatbázisok visszaállítani, csak olvasható (11).
* Az egyes adatbázisok a próba DR-készletben a helyreállítási óta megváltozott nevezze át vagy törölje a megfelelő adatbázist az elsődleges próbakészletben (12). 
* Másolja a frissített adatbázisok a vész-Helyreállítási készlet az elsődleges készlet (13). 
* [14] DR-készlet törlése 

A kulcs **előnyöket** e stratégia vannak:

* Támogatja a legtöbb agresszív SLA-t a fizető vevők, biztos lehet benne, hogy nem tervezett kimaradás nem befolyásolja a több mint 50 %-a bérlői adatbázisok. 
* Ez garantálja, hogy az új kísérletek feloldja-e, amint az eljárást vész-Helyreállítási készlet jön létre, a helyreállítás során. 
* Ez lehetővé teszi, hogy a tárolókészlet-kapacitást, 50 %-készlet 1 másodlagos adatbázisok hatékonyabb kihasználását, és készlet 2 garantáltan kevésbé aktív, mint az elsődleges adatbázis.

A fő **kompromisszumot** vannak:

* A CRUD műveletek az felügyeleti adatbázisokhoz kisebb késést biztosít azon végfelhasználók számára, a végfelhasználók számára, az elsődleges felügyeleti adatbázis elleni végrehajtás régió B csatlakozik a régióban, mint A kapcsolat van.
* A felügyeleti adatbázisban összetettebb kialakítása igényel. Például minden bérlő rekord tartalmazza-e a location kódcímke, módosítani kell a feladatátvételi és a feladat-visszavétel során.  
* A fizető vevők szokásosnál alacsonyabb teljesítményt tapasztalhat, a készlet frissítés régióban B befejeződéséig. 

## <a name="summary"></a>Összefoglalás
Ez a cikk foglalkozik a több-bérlős Szolgáltatottszoftver-ISV alkalmazás által használt adatbázis-rétegből vész-helyreállítási stratégiái. Stratégia van annak megfelelően kell elvégezni az alkalmazás, például az üzleti modell, az SLA-t szeretne nyújtsanak az ügyfeleknek, a költségvetési megkötés stb. Minden ismertetett stratégia előnyei és kompromisszum összegzi, így sikerült tájékozott döntést. Valószínűleg az adott alkalmazás emellett egyéb Azure összetevőket tartalmazza. Ezért tekintse át az üzleti folytonossági útmutatást és levezényelni a visszaállítást az adatbázis-rétegből velük. Helyreállítás az adatbázis-alkalmazások az Azure-ban kezelésével kapcsolatos további tudnivalókért tekintse meg [Designing vész-helyreállítási megoldások](sql-database-designing-cloud-solutions-for-disaster-recovery.md).  

## <a name="next-steps"></a>Következő lépések
* További tudnivalók az Azure SQL adatbázis automatikus biztonsági mentés című [SQL-adatbázis biztonsági mentések automatikus](sql-database-automated-backups.md).
* Egy üzleti folytonosság – áttekintés és forgatókönyvek: [üzleti folytonosság – áttekintés](sql-database-business-continuity.md).
* A helyreállítás automatikus biztonsági mentés használatával kapcsolatos további tudnivalókért lásd: [adatbázis visszaállítása a szolgáltatás által kezdeményezett biztonsági másolatból](sql-database-recovery-using-backups.md).
* Gyorsabb helyreállítási beállításokkal kapcsolatos további tudnivalókért lásd: [aktív georeplikáció](sql-database-geo-replication-overview.md).
* Automatikus biztonsági mentés az Archiválás használatával kapcsolatos további tudnivalókért lásd: [adatbázis másolása](sql-database-copy.md).

