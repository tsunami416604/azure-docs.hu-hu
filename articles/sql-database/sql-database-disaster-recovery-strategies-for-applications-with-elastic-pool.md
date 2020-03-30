---
title: Katasztrófa-helyreállítási megoldások tervezése
description: Ismerje meg, hogyan tervezheti meg a vész-helyreállítási felhőalapú megoldását a megfelelő feladatátvételi minta kiválasztásával.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
ms.date: 01/25/2019
ms.openlocfilehash: 4eeaa187142a6d0d97b12f685ebc455f3844606f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73825875"
---
# <a name="disaster-recovery-strategies-for-applications-using-sql-database-elastic-pools"></a>Vész-helyreállítási stratégiák az SQL Database rugalmas készleteit használó alkalmazásokhoz

Az évek során megtanultuk, hogy a felhőszolgáltatások nem üzembiztosak, és katasztrofális események történnek. Az SQL Database számos lehetőséget biztosít az alkalmazás üzletmenet-folytonosságának biztosításához, amikor ezek az incidensek bekövetkeznek. [Rugalmas készletek](sql-database-elastic-pool.md) és egyetlen adatbázisok támogatja az azonos típusú vész-helyreállítási (DR) képességek. Ez a cikk számos DR-stratégiát ismertet a rugalmas készletekhez, amelyek kihasználják ezeket az SQL Database üzletmenet-folytonossági funkciókat.

Ez a cikk a következő kanonikus SaaS ISV alkalmazásmintát használja:

Egy modern felhőalapú webalkalmazás minden végfelhasználó számára egy SQL-adatbázist rendel. A isv-nek sok ügyfele van, ezért számos adatbázist használ, más néven bérlői adatbázisokat. Mivel a bérlői adatbázisok általában előre nem látható tevékenységmintákkal rendelkeznek, az isv egy rugalmas készletet használ, hogy az adatbázis költsége nagyon kiszámítható legyen hosszabb ideig. A rugalmas készlet is leegyszerűsíti a teljesítmény-kezelést, amikor a felhasználói tevékenység kiugrik. A bérlői adatbázisok mellett az alkalmazás több adatbázist is használ a felhasználói profilok, a biztonság, a használati minták gyűjtése stb. Az egyes bérlők rendelkezésre állása nem befolyásolja az alkalmazás teljes rendelkezésre állását. A felügyeleti adatbázisok rendelkezésre állása és teljesítménye azonban kritikus fontosságú az alkalmazás funkciója szempontjából, és ha a felügyeleti adatbázisok offline állapotban vannak, a teljes alkalmazás offline állapotban van.

Ez a cikk ismerteti a DR-stratégiák, amelyek a költségérzékeny indítási alkalmazások a szigorú rendelkezésre állási követelmények kel.

> [!NOTE]
> Prémium szintű vagy üzleti legkritikusabb adatbázisok és rugalmas készletek használata esetén rugalmassá teheti őket a regionális kimaradásokkal szemben, ha zónaredundáns központi telepítési konfigurációvá alakítja őket. Lásd: [Zónaredundáns adatbázisok](sql-database-high-availability.md).

## <a name="scenario-1-cost-sensitive-startup"></a>1. forgatókönyv. Költségérzékeny indítás

Én egy induló vállalkozás, és én vagyok rendkívül költségérzékeny.  Szeretném egyszerűsíteni az alkalmazás telepítését és felügyeletét, és korlátozott SLA-t szeretnék az egyes ügyfelek számára. De azt akarom, hogy az alkalmazás egésze soha nem offline.

Az egyszerűség követelményének kielégítése érdekében telepítse az összes bérlői adatbázist egyetlen rugalmas készletbe az Ön által kiválasztott Azure-régióban, és telepítse a felügyeleti adatbázisokat georeplikált egyetlen adatbázisként. A bérlők vész-helyreállítási használja a geo-visszaállítás, amely jön további költségek nélkül. A felügyeleti adatbázisok rendelkezésre állásának biztosítása érdekében georeplikálja őket egy másik régióba egy automatikus feladatátvételi csoport használatával (1. lépés). A vész-helyreállítási konfiguráció folyamatban lévő költsége ebben a forgatókönyvben megegyezik a másodlagos adatbázisok teljes költsége. Ezt a konfigurációt a következő diagram szemlélteti.

![1. ábra](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-1.png)

Ha kimaradás történik az elsődleges régióban, az alkalmazás online állapotba hozásának helyreállítási lépéseit a következő diagram szemlélteti.

* A feladatátvételi csoport elindítja a felügyeleti adatbázis automatikus feladatátvételt a VÉSZ-régióba. Az alkalmazás automatikusan újracsatlakozik az új elsődleges és az összes új fiókok és bérlői adatbázisok jönnek létre a VÉSZ-régióban. A meglévő ügyfelek átmenetileg nem érhetők el az adataik.
* Hozza létre a rugalmas készletet ugyanazzal a konfigurációval, mint az eredeti készlet (2).
* A geo-visszaállítás segítségével másolatot készíthet a bérlői adatbázisokról (3). Az egyes visszaállítások a végfelhasználói kapcsolatok által, vagy más alkalmazás-specifikus prioritási séma.

Ezen a ponton az alkalmazás újra online állapotba kerül a VÉSZ-régióban, de egyes ügyfelek késést tapasztalnak az adataik elérésekor.

![2. ábra](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-2.png)

Ha a szolgáltatáskimaradás ideiglenes volt, lehetséges, hogy az elsődleges régió az Azure által helyreállítja, mielőtt az összes adatbázis-visszaállítás befejeződik a VÉSZ-régióban. Ebben az esetben vezényli az alkalmazás áthelyezése vissza az elsődleges régióba. A folyamat a következő ábrán látható lépéseket veszi.

* Az összes függőben lévő geo-visszaállítási kérelem visszavonása.
* Feladatátvétel a felügyeleti adatbázisok az elsődleges régióba (5). A régió helyreállítása után a régi előválasztás automatikusan másodlagossá vált. Most újra szerepet cserélnek.
* Módosítsa az alkalmazás kapcsolati karakterláncát úgy, hogy visszamutasson az elsődleges régióra. Most minden új fiókok és bérlői adatbázisok jönnek létre az elsődleges régióban. Egyes meglévő ügyfelek átmenetileg nem érhetők el az adataik.
* Állítsa be a VÉSZ-készlet összes adatbázisát írásvédettre, hogy a VÉSZ-régióban (6) ne lehessen őket módosítani.
* A helyreállítási helyreállítás óta megváltozott dr-készlet minden egyes adatbázisához nevezze át vagy törölje a megfelelő adatbázisokat az elsődleges készletben (7).
* Másolja a frissített adatbázisokat a VÉSZ-készletből az elsődleges készletbe (8).
* A VÉSZ-készlet törlése (9)

Ezen a ponton az alkalmazás online állapotban van az elsődleges régióban az összes bérlői adatbázis érhető el az elsődleges készletben.

![3. ábra](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-3.png)

A stratégia fő **előnye** az adatréteg-redundancia alacsony folyamatos költsége. A biztonsági másolatokat az SQL Database szolgáltatás automatikusan, alkalmazás-újraírás nélkül és további költségek nélkül készíti el.  A költség csak akkor merül fel, ha a rugalmas adatbázisok visszaállítása. A **kompromisszum** az, hogy az összes bérlői adatbázis teljes helyreállítása jelentős időt vesz igénybe. Az időtartam a vész-helyreállítások teljes számától és a bérlői adatbázisok teljes méretétől függ. Még akkor is, ha egyes bérlők visszaállítása a többivel szemben, akkor is versenyben van az összes többi visszaállítások, amelyek ugyanabban a régióban kezdeményezett, mint a szolgáltatás dönt, és a szabályozások, hogy minimálisra csökkentsék a meglévő ügyfelek adatbázisaira gyakorolt általános hatás minimalizálása érdekében. Emellett a bérlői adatbázisok helyreállítása nem indulhat el, amíg a VÉSZ-régióban létre nem jön az új rugalmas készlet.

## <a name="scenario-2-mature-application-with-tiered-service"></a>2. forgatókönyv. Érett alkalmazás többszintű szolgáltatással

Én egy érett SaaS alkalmazás többszintű szolgáltatási ajánlatok és a különböző SLA a próba-ügyfelek és a fizető ügyfelek számára. A próba ügyfelek, azt kell csökkenteni a költségeket, amennyire csak lehetséges. A próbaverziós ügyfelek leállást tarthatnak, de csökkenteni szeretném annak valószínűségét. A fizető ügyfelek számára minden állásidő repülési kockázatot jelent. Ezért biztos akarok lenni abban, hogy a fizető ügyfelek mindig hozzáférhetnek az adataikhoz.

A forgatókönyv támogatása érdekében válassza el a próba-bérlők a fizetős bérlők külön rugalmas készletek elhelyezésével. A próbaverziós ügyfelek bérlőnként alacsonyabb eDTU-val vagy virtuális magokkal rendelkeznek, és hosszabb helyreállítási idővel alacsonyabb SLA-val rendelkeznek. A fizető ügyfelek egy bérlőnként magasabb eDTU-val vagy virtuális magokkal rendelkező készletben vannak, és magasabb SLA-val rendelkeznek. A legalacsonyabb helyreállítási idő biztosítása érdekében a fizető ügyfelek bérlői adatbázisai georeplikált. Ezt a konfigurációt a következő diagram szemlélteti.

![4.](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-4.png)

Az első forgatókönyvhöz hasonlóan a felügyeleti adatbázisok is elég aktívak, így egyetlen georeplikált adatbázist kell használni a használatához (1). Ez biztosítja az új ügyfél-előfizetések, profilfrissítések és egyéb felügyeleti műveletek kiszámítható teljesítményét. A régió, amelyben az elsődleges az irányítási adatbázisok laknak az elsődleges régió és a régió, amelyben a másodlagos a kezelési adatbázisok a VÉSZ-régióban.

A fizető ügyfelek bérlői adatbázisai aktív adatbázisokkal rendelkeznek az elsődleges régióban kiépített "fizetős" készletben. A vész-és a dr régióban egy másodlagos készlet azonos nevű. Minden bérlő georeplikált a másodlagos készlet (2). Ez lehetővé teszi az összes bérlői adatbázis gyors helyreállítását feladatátvétel használatával.

Ha az elsődleges régióban kimaradás történik, az alkalmazás online állapotba hozásának helyreállítási lépéseit a következő ábra szemlélteti:

![5. ábra](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-5.png)

* Azonnal feladatátvétela a felügyeleti adatbázisok a VÉSZ-régió (3).
* Módosítsa az alkalmazás kapcsolati karakterláncát úgy, hogy a VÉSZ-régióra mutasson. Most minden új fiókok és a bérlői adatbázisok jönnek létre a VÉSZ-régióban. A meglévő próbaverziós ügyfelek átmenetileg nem érhetők el az adataik.
* A vész-érték-helyreállításhoz a fizetős bérlő adatbázisai a VÉSZ-régióban lévő készletre való átvétele a rendelkezésre állás azonnali visszaállításához (4). Mivel a feladatátvétel egy gyors metaadatszint-változás, fontolja meg egy optimalizálás, ahol az egyes feladatátvételek aktiválódik igény szerint a végfelhasználói kapcsolatok.
* Ha a másodlagos készlet eDTU-mérete vagy virtuálismag-értéke alacsonyabb volt, mint az elsődleges, mert a másodlagos adatbázisok csak a másodlagos adatok nál uk ként szükséges a változásnaplók feldolgozásához szükséges kapacitást, azonnal növelje a készlet kapacitását most a teljes munkaterhelés befogadásához az összes bérlő (5).
* Hozza létre az új rugalmas készlet et ugyanazzal a névvel és a vész-és konfigurálása a vész-és konfigurálás a próbaverziós ügyfelek adatbázisok (6).
* A próbaverziós ügyfelek készletének létrehozása után a geo-visszaállítás segítségével állítsa vissza az egyes próba-bérlői adatbázisokat az új készletbe (7). Fontolja meg az egyes visszaállítások indítását a végfelhasználói kapcsolatok on keresztül, vagy használjon más alkalmazásspecifikus prioritási sémát.

Ezen a ponton az alkalmazás újra online állapotba kerül a VÉSZ-régióban. Minden fizető ügyfél hozzáférhet az adataihoz, miközben a próbaverziós ügyfelek késleltetést tapasztalnak az adataik elérésekor.

Ha az elsődleges régió tasza vissza az *Azure-ban, miután* visszaállította az alkalmazást a VÉSZ-régióban, folytathatja az alkalmazás futtatását az adott régióban, vagy dönthet úgy, hogy visszaadja az elsődleges régióba. Ha az elsődleges régió a feladatátvételi folyamat befejezése *előtt* helyreáll, fontolja meg a visszavétel azonnal. A feladat-visszavétel a következő ábrán bemutatott lépéseket teszi:

![6. ábra](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-6.png)

* Az összes függőben lévő geo-visszaállítási kérelem visszavonása.
* A felügyeleti adatbázisok feladatátvétele (8). A régió helyreállítása után a régi elsődleges automatikusan másodlagosvá válik. Most újra ez lesz az előválasztás.  
* Feladatátvétel a fizetős bérlői adatbázisok (9). Hasonlóképpen, a régió helyreállítása után a régi előválasztás automatikusan másodlagosvá válik. Most újra ők lesznek az előválasztás.
* Állítsa be a visszaállított próba-adatbázisok, amelyek megváltoztak a VÉSZ régióban írásvédett (10).
* A próbaverziós ügyfelek DR-készletében a helyreállítás óta megváltozott minden adatbázis esetében nevezze át vagy törölje a megfelelő adatbázist a próbaverziós ügyfelek elsődleges készletében (11).
* Másolja a frissített adatbázisokat a VÉSZ-készletből az elsődleges készletbe (12).
* Törölje a VÉSZ-készletet (13).

> [!NOTE]
> A feladatátvételi művelet aszinkron. A helyreállítási idő minimalizálása érdekében fontos, hogy legalább 20 adatbázis kötegeiben hajtsa végre a bérlői adatbázisok feladatátvételi parancsát.

Ennek a stratégiának a legfontosabb **előnye,** hogy a legmagasabb SLA-t biztosítja a fizető ügyfelek számára. Azt is garantálja, hogy az új kísérletek feloldása, amint a próba-DR készlet jön létre. A kompromisszum az, hogy ez a beállítás növeli a bérlői adatbázisok teljes költségét a fizetős ügyfelek másodlagos **VÉSZ-készletének** költségével. Emellett ha a másodlagos készlet más méretű, a fizető ügyfelek a feladatátvétel után alacsonyabb teljesítményt tapasztalnak, amíg a készlet frissítése a VÉSZ-régióban befejeződik.

## <a name="scenario-3-geographically-distributed-application-with-tiered-service"></a>3. forgatókönyv. Földrajzilag elosztott alkalmazás többszintű szolgáltatással

Nekem van egy érett SaaS alkalmazás többszintű szolgáltatási ajánlatok. Azt akarom, hogy felajánl egy nagyon agresszív SLA az én fizetett ügyfelek és minimálisra csökkenti a hatás kockázatát, ha kimaradások fordulnak elő, mert még rövid megszakítás okozhat az ügyfelek elégedetlenségét. Rendkívül fontos, hogy a fizető ügyfelek mindig hozzáférhessenek az adataikhoz. A próbaverziók ingyenesek, és a próbaidőszak alatt nem kínálnak SLA-t.

A forgatókönyv támogatásához használjon három különálló rugalmas készletet. Két egyenlő méretű készlet kiépítése magas eDT-k vagy virtuális magok adatbázisonként két különböző régióban, hogy tartalmazza a fizetős ügyfelek bérlői adatbázisok. A próba-bérlőket tartalmazó harmadik készlet adatbázisonként alacsonyabb eDT-ekkel vagy virtuális magokkal rendelkezhet, és a két régió egyikében kiépíthető.

A legalacsonyabb helyreállítási idő biztosítása a kimaradások során, a fizető ügyfelek bérlői adatbázisok georeplikált 50%-a az elsődleges adatbázisok a két régiómindegyikében. Hasonlóképpen minden régió rendelkezik a másodlagos adatbázisok 50%-ával. Ily módon, ha egy régió offline állapotban van, a fizetős ügyfelek adatbázisainak csak 50%-a érintett, és át kell adnia a feladatátvételt. A többi adatbázis érintetlen marad. Ezt a konfigurációt az alábbi ábra szemlélteti:

![4.](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-7.png)

Az előző forgatókönyvekhez hasonló módon a felügyeleti adatbázisok meglehetősen aktívak, ezért konfigurálja őket egyetlen georeplikált adatbázisként (1). Ez biztosítja az új ügyfél-előfizetések, profilfrissítések és egyéb felügyeleti műveletek kiszámítható teljesítményét. Az A régió a felügyeleti adatbázisok elsődleges régiója, és a B régió t használják a felügyeleti adatbázisok helyreállításához.

A fizető ügyfelek bérlői adatbázisai is georeplikált, de az elsődleges és másodlagos felosztása az A régió és a B régió (B) (2). Ily módon a bérlő elsődleges adatbázisok által érintett kimaradás átveheti a másik régióban, és elérhetővé válnak. A bérlői adatbázisok másik fele egyáltalán nem érintett.

A következő ábra bemutatja a helyreállítási lépéseket kell végrehajtani, ha egy kimaradás történik az A régióban.

![5. ábra](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-8.png)

* Azonnal feladatátvétela a felügyeleti adatbázisok a B régió (3).
* Módosítsa az alkalmazás kapcsolati karakterláncát úgy, hogy a B régió felügyeleti adatbázisaira mutasson. A meglévő próbaverziós ügyfelek átmenetileg nem érhetők el az adataik.
* A b régióban lévő 2-es készletre a fizetős bérlő adatbázisai feladatátvételt kapnak, hogy azonnal visszaállítsák a rendelkezésre állásukat (4). Mivel a feladatátvétel egy gyors metaadatszint-változás, érdemes lehet egy optimalizálás, ahol az egyes feladatátvételek aktiválódik igény szerint a végfelhasználói kapcsolatok.
* Mivel a 2-es készlet csak elsődleges adatbázisokat tartalmaz, a készlet teljes munkaterhelése nő, és azonnal növelheti az eDTU méretét (5) vagy a virtuális magok számát.
* Hozza létre az új rugalmas készlet et ugyanazzal a névvel és azonos konfigurációval a B régióban a próbaverziós ügyfelek adatbázisaihoz (6).
* A készlet létrehozása után használja a geo-visszaállítás t az egyes próba-bérlői adatbázis visszaállításához a készletbe (7). Az egyes visszaállítások a végfelhasználói kapcsolatok által, vagy más alkalmazás-specifikus prioritási séma.

> [!NOTE]
> A feladatátvételi művelet aszinkron. A helyreállítási idő minimalizálása érdekében fontos, hogy legalább 20 adatbázis kötegeiben hajtsa végre a bérlői adatbázisok feladatátvételi parancsát.

Ezen a ponton az alkalmazás újra online a B régióban. Minden fizető ügyfél hozzáférhet az adataihoz, miközben a próbaverziós ügyfelek késleltetést tapasztalnak az adataik elérésekor.

Az A régió helyreakerülésekor el kell döntenie, hogy a B régiót kívánja-e használni a próbaverziós ügyfelek számára, vagy a próbaügyfelek készletét szeretné használni az A régióban. Az egyik feltétel lehet a próba-bérlői adatbázisok %, amelyet a helyreállítás óta módosítottak. Ettől a döntéstől függetlenül újra ki kell egyensúlyoznia a fizetős bérlőket két készlet között. a következő ábra azt a folyamatot mutatja be, amikor a próba-bérlői adatbázisok visszaadják a következő régióba az A régióba.  

![6. ábra](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-9.png)

* Törölje az összes függőben lévő geo-visszaállítási kérelmek próba-DR készlet.
* Feladatátvétel a felügyeleti adatbázis felett (8). A régió helyreállítása után a régi elsődleges automatikusan másodlagosvá vált. Most újra ez lesz az előválasztás.  
* Válassza ki, mely fizetős bérlői adatbázisok feladat-visszaad készlet 1 és kezdeményezi a feladatátvételt a másodlagos (9). A régió helyreállítása után az 1-es készletben lévő összes adatbázis automatikusan másodlagossá vált. Most 50%-uk újra előválasztáslesz.
* Csökkentse a 2-es készlet méretét az eredeti eDTU-ra (10) vagy a virtuális magok számára.
* Állítsa be az összes visszaállított próba-adatbázist a B régióban írásvédettre (11).
* A próba-DR-készlet minden olyan adatbázisához, amely a helyreállítás óta megváltozott, nevezze át vagy törölje a megfelelő adatbázist a próbaelsődleges készletben (12).
* Másolja a frissített adatbázisokat a VÉSZ-készletből az elsődleges készletbe (13).
* Törölje a VÉSZ-készletet (14).

E stratégia legfontosabb **előnyei** a következők:

* Támogatja a legagresszívebb SLA a fizető ügyfelek számára, mert biztosítja, hogy a kimaradás nem befolyásolhatja a bérlői adatbázisok több mint 50%-át.
* Garantálja, hogy az új kísérletek feloldása, amint a nyomvonal DR-készlet jön létre a helyreállítás során.
* Lehetővé teszi a készlet kapacitásának hatékonyabb kihasználását, mivel az 1-es és 2-es készletmásodlagos adatbázisainak 50%-a garantáltan kevésbé aktív, mint az elsődleges adatbázisok.

A fő **kompromisszumok** a következők:

* A felügyeleti adatbázisok on-mail-műveletek a felügyeleti adatbázisok kisebb késést a végfelhasználók az A régióhoz kapcsolódó, mint a B régióhoz kapcsolódó végfelhasználók számára, ahogy azok a felügyeleti adatbázisok elsődleges en hajtják végre.
* A felügyeleti adatbázis összetettebb tervezését igényli. Például minden bérlői rekord rendelkezik egy helycímkével, amelyet meg kell változtatni a feladatátvétel és a feladat-visszavétel során.  
* A fizető ügyfelek a szokásosnál gyengébb teljesítményt tapasztalhatnak, amíg a B régióban a készlet frissítése be nem fejeződik.

## <a name="summary"></a>Összefoglalás

Ez a cikk a vész-helyreállítási stratégiák a SaaS ISV több-bérlős alkalmazás által használt adatbázis-szint. A választott stratégia az alkalmazás igényein alapul, például az üzleti modellen, az ügyfeleknek nyújtani kívánt SLA-n, a költségvetési megkötésen stb. Minden leírt stratégia felvázolja az előnyöket és a kompromisszumot, így megalapozott döntést hozhat. Az adott alkalmazás valószínűleg más Azure-összetevőket is tartalmaz. Így tekintse át az üzletmenet-folytonossági útmutatást, és vezényli velük az adatbázis-szint helyreállítását. Ha többet szeretne megtudni az azure-beli adatbázis-alkalmazások helyreállításának kezeléséről, olvassa el [a Felhőalapú megoldások tervezése a vészhelyreállításhoz](sql-database-designing-cloud-solutions-for-disaster-recovery.md)című részét.  

## <a name="next-steps"></a>További lépések

* Az Azure SQL Database automatikus biztonsági másolatairól az [SQL Database automatikus biztonsági mentései](sql-database-automated-backups.md)című témakörben olvashat.
* Az üzletmenet folytonosságának áttekintését és forgatókönyveit az [Üzletmenet folytonosságának áttekintése](sql-database-business-continuity.md)című témakörben találja.
* Az automatikus biztonsági mentések helyreállítási használatáról az [adatbázis visszaállítása a szolgáltatás által kezdeményezett biztonsági mentések ből](sql-database-recovery-using-backups.md)című témakörben olvashat.
* A gyorsabb helyreállítási lehetőségekről az [Aktív georeplikáció](sql-database-active-geo-replication.md) és [az automatikus feladatátvételi csoportok](sql-database-auto-failover-group.md)című témakörben olvashat.
* Az automatikus biztonsági mentések archiváláshoz való használatáról az [adatbázis másodpéldányán](sql-database-copy.md)olvashat.
