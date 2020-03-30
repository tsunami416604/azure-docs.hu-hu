---
title: 'Saas alkalmazás: Számos adatbázis teljesítményének figyelése'
description: Az Azure SQL-adatbázisok és -készletek teljesítményének figyelése és kezelése több-bérlős SaaS-alkalmazásban
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 34c50795567615637e31446ad3dc51a5e1b355f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214466"
---
# <a name="monitor-and-manage-performance-of-azure-sql-databases-and-pools-in-a-multi-tenant-saas-app"></a>Az Azure SQL-adatbázisok és -készletek teljesítményének figyelése és kezelése több-bérlős SaaS-alkalmazásban

Ebben az oktatóanyagban a rendszer a SaaS-alkalmazásokban használt számos kulcsfontosságú teljesítménykezelési forgatókönyvet vizsgálja meg. Egy terhelésgenerátor használatával szimulálja a tevékenység az összes bérlői adatbázisok, a beépített figyelési és riasztási funkciók az SQL Database és a rugalmas készletek jelennek meg.

A Wingtip jegyek SaaS-adatbázis bérlőnként alkalmazás egy egybérlős adatmodellt használ, ahol minden helyszín (bérlő) saját adatbázissal rendelkezik. Sok más SaaS-alkalmazáshoz hasonlóan a bérlői számítási feladatok várt mintája kiszámíthatatlan és szórványos. Ez a gyakorlatban azt jelenti, hogy a jegyeladásokra bármikor sor kerülhet. A tipikus adatbázis-használati minta kihasználása érdekében a bérlői adatbázisok rugalmas készletekbe vannak telepítve. A rugalmas készletek optimalizálják a megoldások költségeit azáltal, hogy számos adatbázis között osztják meg az erőforrásokat. Ennél a típusú mintánál fontos az adatbázis és a készleterőforrások felhasználásának figyelése annak biztosítása érdekében, hogy a terhelések egyenletesen oszoljanak meg a készletek közt. Emellett azt is biztosítani kell, hogy az egyes adatbázisok elengedő mennyiségű erőforrással rendelkezzenek, és hogy a készletek ne érjék el a maximális [eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model)-korlátot. Ez az oktatóanyag különböző módszereket ismertet az adatbázisok és készletek figyelésére és kezelésére, valamint a számítási feladatok változásaira adott korrekciós műveletek elvégzésére.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> 
> * A bérlői adatbázisok használatának szimulálása egy adott terhelésgenerátor futtatásával
> * A bérlői adatbázisok megnövekedett terhelésre adott válaszának megfigyelése
> * A rugalmas készlet vertikális felskálázása az adatbázis megnövekedett terhelésére adott válaszul
> * Egy második rugalmas készlet kiépítése az adatbázis-tevékenységek terhelésének kiegyenlítésére


Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip jegyek SaaS-adatbázis bérlőnként alkalmazás telepítve van. Ha kevesebb, mint öt perc alatt üzembe szeretne helyezni, [olvassa el a Wingtip Tickets SaaS-adatbázis bérlőnkénti telepítése és feltárása című témakört.](saas-dbpertenant-get-started-deploy.md)
* Az Azure PowerShell telepítve van. Részletes információk: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Bevezetés a SaaS teljesítménykezelési mintáiba

Az adatbázisteljesítmény-kezelés a teljesítményadatok fordításából és elemzéséből, majd az adatokra való reagálásból áll. Ez tulajdonképpen a paraméterek módosítását jelenti, miáltal az alkalmazás válaszideje elfogadható szinten marad. Több bérlő üzemeltetése esetén rugalmas készletek költséghatékony módja annak, hogy erőforrásokat biztosítson és kezeljenek egy előre nem látható számítási feladatokkal rendelkező adatbáziscsoport számára. Bizonyos számításifeladat-mintáknál már akár két S3-adatbázist is előnyös lehet készletben kezelni.

![alkalmazásdiagram](./media/saas-dbpertenant-performance-monitoring/app-diagram.png)

A készleteket és a készletekben lévő adatbázisokat figyelni kell annak biztosítása érdekében, hogy elfogadható teljesítménytartományokon belül maradjanak. Állítsa be a készlet konfigurációját az összes adatbázis összesített munkaterhelésének igényeinek megfelelően, biztosítva, hogy a készlet eDT-k megfelelőek legyenek a teljes munkaterheléshez. Állítsa be az adatbázisonkénti minimális és maximális eDTU-értékeket az alkalmazás specifikus igényei szerint.

### <a name="performance-management-strategies"></a>Teljesítménykezelési stratégiák

* A teljesítmény manuális figyelésének elkerülése érdekében a leghatékonyabb, ha olyan **riasztásokat állít be, amelyek akkor aktiválódnak, ha az adatbázisok vagy készletek a normál tartományon kívülre esnek.**
* A készlet összesített számítási méretének rövid távú ingadozásaira való reagálás érdekében a **készlet eDTU-szintje fel- vagy leskálázható.** Ha az ingadozás rendszeres vagy kiszámítható, akkor **a készlet beállítható úgy, hogy a skálázás automatikusan ütemezve legyen**. Beállítható például a vertikális leskálázás, amikor előre láthatóan kevés lesz a számítási feladat, például éjjelente vagy a hétvégi napokon.
* A hosszabb távú ingadozásokra vagy az adatbázisok számának változására válaszul **az egyes adatbázisok áthelyezhetők másik készletekbe**.
* Az *egyes* adatbázisok betöltésének rövid távú növekedésére adott **válaszként az egyes adatbázisok kivehetők egy készletből, és egyedi számítási méretet rendelhetnek**hozzá. A terhelés csökkenésével az adatbázis visszahelyezhető a készletbe. Ha ez előre ismert, az adatbázisok megelőzően áthelyezhetők annak érdekében, hogy az adatbázis mindig rendelkezzen a szükséges erőforrásokkal, és hogy elkerülje a készlet más adatbázisaira gyakorolt hatást. Ha ez a szükséglet előre kiszámítható, például ha egy helyszín nagy mennyiségű növekedésre számít a jegyeladásokban egy népszerű esemény miatt, akkor ez a kezelési viselkedés integrálható az alkalmazásba.

Az [Azure Portal](https://portal.azure.com) a legtöbb erőforráshoz beépített figyelési és riasztási lehetőségeket biztosít. Az SQL Database esetén figyelési és riasztási lehetőségeket biztosít az adatbázisokhoz és készletekhez. Ez a beépített figyelésés és riasztás erőforrás-specifikus, ezért célszerű kis számú erőforráshoz használni, de nem túl kényelmes, ha sok erőforrással dolgozik.

Nagy mennyiségű forgatókönyvek, ahol dolgozik sok erőforrást, [az Azure Monitor naplók](saas-dbpertenant-log-analytics.md) is használható. Ez egy különálló Azure-szolgáltatás, amely a Log Analytics-munkaterületen összegyűjtött kibocsátott naplók elemzésével szolgál. Az Azure Monitor naplók számos szolgáltatás telemetriai adatokat gyűjthetnek, és riasztások lekérdezésére és beállítására használhatók.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>A Wingtip jegyek SaaS-adatbázis bérlőnkénti alkalmazásparancsfájljainak beszereznie

A Wingtip jegyek SaaS több-bérlős adatbázis-parancsfájlok és az alkalmazás forráskód érhető el a [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub tárház. Tekintse meg az [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) a Wingtip Jegyek SaaS-parancsfájlok letöltéséhez és feloldásához szükséges lépésekhez.

## <a name="provision-additional-tenants"></a>További bérlők kiépítése

Noha a készletek használata már két S3-adatbázis esetén is költséghatékony lehet, minél több adatbázis található egy készletben, a költséghatékonyság mértéke annál jobban nő. Annak érdekében, hogy a teljesítményfigyelés és -kezelés nagy léptékben való használatát kellőképpen szemléltetni lehessen, jelen oktatóanyaghoz legalább 20 üzembe helyezett adatbázis szükséges.

Ha már kiépített egy bérlői köteget egy korábbi oktatóanyagban, ugorjon a [Használat szimulálása az összes bérlői adatbázisban](#simulate-usage-on-all-tenant-databases) szakaszra.

1. A **PowerShell ISE**, nyitott ... \\Tanulási modulok\\teljesítményfigyelése és kezelése\\*Demo-PerformanceMonitoringAndManagement.ps1*. Tartsa ezt a szkriptet nyitva, mivel az oktatóanyag során több különböző forgatókönyvet is futtatnia kell majd.
1. **$DemoScenario** = **1$DemoScenario**beállítása , **Bérlők kötegének kiépítése**
1. A szkript futtatásához nyomja le az **F5** billentyűt.

A szkript kevesebb mint öt perc alatt 17 bérlőt helyez üzembe.

Az *Új-TenantBatch* parancsfájl [az Erőforrás-kezelő](../azure-resource-manager/index.yml) sablonjainak beágyazott vagy csatolt készletét használja, amelyek bérlők kötegét hozzák létre, amely alapértelmezés szerint átmásolja az adatbázis **basetenantdb-jét** a katalóguskiszolgálón az új bérlői adatbázisok létrehozásához, majd regisztrálja ezeket a katalógusban, és végül inicializálja őket a bérlő nevével és helyszíntípusával. Ez összhangban van azzal, ahogyan az alkalmazás egy új bérlőt rendel el. A *basetenantdb-n* végrehajtott módosítások at minden ezt követően kiépített új bérlőre alkalmazza a rendszer. Tekintse meg a [Sémakezelése oktatóanyagból,](saas-tenancy-schema-management.md) hogy hogyan módosíthatja a sémát a *meglévő* bérlői adatbázisokon (beleértve az *basetenantdb* adatbázist is).

## <a name="simulate-usage-on-all-tenant-databases"></a>Az összes bérlői adatbázis használatának szimulálása

A *Demo-PerformanceMonitoringAndManagement.ps1* parancsfájl elérhetővé téve az összes bérlői adatbázison futó számítási feladatot. A terhelés a rendelkezésre álló betöltési forgatókönyvek egyikével jön létre:

| Bemutató | Forgatókönyv |
|:--|:--|
| 2 | Normál intenzitási terhelés generálása (körülbelül 40 DTU) |
| 3 | Terhelés létrehozása adatbázisonkénti hosszabb és gyakoribb adatlöketekkel|
| 4 | Terhelés létrehozása adatbázisonként nagyobb DTU-adatlöketekkel (körülbelül 80 DTU)|
| 5 | Normál terhelés és nagy terhelés létrehozása egyetlen bérlőn (körülbelül 95 DTU)|
| 6 | Kiegyensúlyozatlan terhelés létrehozása több készlet számára|

A terhelésgenerátor egy *szintetikus* CPU-terhelést alkalmaz az összes bérlői adatbázison. A generátor minden bérlői adatbázis számára elindít egy feladatot, amely időközönként meghív egy, a terhelést létrehozó tárolt eljárást. A terhelések szintje (eDTU-ban mérve), időtartama és időköze minden adatbázis esetén más és más, ezzel szimulálva a kiszámíthatatlan bérlői aktivitást.

1. A **PowerShell ISE**, nyitott ... \\Tanulási modulok\\teljesítményfigyelése és kezelése\\*Demo-PerformanceMonitoringAndManagement.ps1*. Tartsa ezt a szkriptet nyitva, mivel az oktatóanyag során több különböző forgatókönyvet is futtatnia kell majd.
1. Állítsa **be $DemoScenario** = **2**, *Normál intenzitású terhelés létrehozása*.
1. Nyomja le az **F5** billentyűt, hogy az összes bérlői adatbázist érje terhelés.

Wingtip jegyek SaaS-adatbázis bérlőnként egy SaaS-alkalmazás, és a valós terhelés egy SaaS-alkalmazás általában szórványos és kiszámíthatatlan. Ennek szimulálására a terhelésgenerátor az összes bérlő között elosztott, véletlenszerű terhelést hoz létre. Néhány perc szükséges a terhelési minta kialakulásához, ezért futtassa a terhelésgenerátort 3-5 percig, mielőtt megkísérli a terhelés figyelését a következő szakaszokban.

> [!IMPORTANT]
> A terhelésgenerátor feladatok sorozataként fut a helyi PowerShell-munkamenetben. Hagyja nyitva a *Demo-PerformanceMonitoringAndManagement.ps1* lapot! Ha bezárja a lapot vagy felfüggeszti a gép működését, a terhelésgenerátor leáll. A terhelésgenerátor egy *feladat-meghívási* állapotban marad, ahol terhelést generál minden új bérlőn, amelyek a generátor indítása után vannak kiépítve. A *Ctrl-C billentyűvel* leállíthatja az új feladatok meghívását, és kiléphet a parancsfájlból. A terhelésgenerátor továbbra is fut, de csak a meglévő bérlők.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Erőforrás-használat figyelése az Azure Portal használatával

Az alkalmazott terhelésből eredő erőforrás-használat figyeléséhez nyissa meg a portált a bérlői adatbázisokat tartalmazó készlethez:

1. Nyissa meg az [Azure Portalt,](https://portal.azure.com) és keresse meg a *&lt;bérlők1-dpt- USER&gt; * kiszolgálót.
1. Görgessen lefelé, keresse meg a rugalmas készleteket, és kattintson a **Pool1** készletre. Ez a készlet tartalmazza az összes eddig létrehozott bérlői adatbázist.

Figyelje meg a **rugalmas készlet figyelése** és **rugalmas adatbázis figyelési** diagramok.

A készlet erőforrás-kihasználtsága a készlet összes adatbázis-kihasználtsága. Az adatbázis-diagram az öt legforróbb adatbázist mutatja:

![adatbázis-diagram](./media/saas-dbpertenant-performance-monitoring/pool1.png)

Mivel az első öt en túl további adatbázisok is vannak a készletben, a készlet kihasználtsága olyan tevékenységet jelenít meg, amely nem jelenik meg az első öt adatbázis-diagramban. További részletekért kattintson **az Adatbázis-erőforrások kihasználtsága gombra:**

![adatbázis-erőforrás kihasználtsága](./media/saas-dbpertenant-performance-monitoring/database-utilization.png)


## <a name="set-performance-alerts-on-the-pool"></a>Teljesítményriasztások beállítása a készletben

Állítson be egy riasztást \>a készletre, amely a 75%-os kihasználtságot indítja el az alábbiak szerint:

1. Nyissa *meg a Készlet1-et* (a *tenants1-dpt- user\<\> server-en)* az Azure [Portalon.](https://portal.azure.com)
1. Kattintson a **Riasztási szabályok** elemre, majd a **+ Riasztás hozzáadása** gombra:

   ![riasztás hozzáadása](media/saas-dbpertenant-performance-monitoring/add-alert.png)

1. Adjon meg egy nevet, például: **Magas DTU**.
1. Állítsa be a következő értékeket:
   * **Metrika = eDTU százalékos értéke**
   * **Feltétel = nagyobb, mint**
   * **Küszöbérték = 75**
   * **Időszak = Az elmúlt 30 percben**
1. Adjon hozzá egy e-mail címet a *További rendszergazdai e-mail(ek) mezőbe,* és kattintson az **OK**gombra.

   ![riasztás beállítása](media/saas-dbpertenant-performance-monitoring/alert-rule.png)


## <a name="scale-up-a-busy-pool"></a>Foglalt készlet vertikális felskálázása

Ha egy készlet összesített terhelési szintje addig növekszik, hogy teljesen lefoglalja a készletet és 100%-os eDTU-használatot ér el, az hatással van az adatbázisok egyéni teljesítményére, és lelassíthatja a lekérdezések válaszidejét a készletben található összes adatbázisban.

**Rövid távon**fontolja meg a készlet további erőforrások biztosításához történő bővítését, vagy távolítsa el az adatbázisokat a készletből (áthelyezi őket más készletekbe, vagy a készletből egy önálló szolgáltatási szintre).

**Hosszabb távon**érdemes optimalizálni a lekérdezéseket vagy az indexhasználatot az adatbázis teljesítményének javítása érdekében. Az alkalmazás teljesítményingadozásokra való érzékenységétől függően az ajánlott eljárás a készlet vertikális felskálázása még a 100%-os eDTU-használat elérése előtt. Használjon olyan riasztást, amely előre figyelmezteti Önt.

Foglalt készletet a generátor által létrehozott terhelés növelésével szimulálhat. Az adatbázisok gyakoribb és hosszabb ideig történő felszakadása miatt a készlet összesített terhelése az egyes adatbázisok követelményeinek módosítása nélkül. A készlet vertikális felskálázása könnyedén elvégezhető a portálon vagy a PowerShellben. A gyakorlat során a Portalt használjuk.

1. Állítsa be *$DemoScenario* = **3**, Terhelés létrehozása hosszabb és _gyakoribb sorozatok adatbázisonként,_ hogy növelje az összesített terhelés intenzitását a készlet módosítása nélkül a maximális terhelés szükséges az egyes adatbázisok.
1. Nyomja le az **F5** billentyűt, hogy az összes bérlői adatbázist érje terhelés.

1. Nyissa meg a **Készlet1** lapot az Azure Portalon.

Figyelje a megnövekedett készlet eDTU használatát a felső diagramon. Néhány percet vesz igénybe, amíg az új nagyobb terhelés beindul, de gyorsan látnia kell, hogy a készlet elkezd idoszakot találni a maximális kihasználtsághoz, és ahogy a terhelés az új mintába esik, gyorsan túlterheli a készletet.

1. A készlet méretezéséhez kattintson a **Készlet1** lap tetején a **Készlet konfigurálása** elemre.
1. Állítsa a **Pool eDTU** beállítást **100-ra.** A készlet eDTU-értékének módosítása nem módosítja az adatbázisonkénti beállításokat (ami továbbra is adatbázisonként legfeljebb 50 eDTU). Az adatbázisonkénti beállítások a **Készlet konfigurálása** lap jobb oldalán láthatók.
1. Kattintson a **Mentés** gombra a készlet méretezésére vonatkozó kérelem elküldéséhez.

Lépjen vissza a **Készlet1** > **áttekintése lapra** a figyelési diagramok megtekintéséhez. Figyelje a több erőforrást biztosító készlet hatását (bár kevés adatbázissal és randomizált terheléssel nem mindig könnyű meggyőzően látni, amíg egy ideig nem fut). A diagramok megtekintése közben vegye figyelembe, hogy a felső diagramon látható 100% most 100 eDTU-t jelent, míg az alsó diagramon látható 100% továbbra is 50 eDTU-t, mivel az adatbázisonkénti maximum változatlanul 50 eDTU.

Az adatbázisok a folyamat során végig online állapotban maradnak, és teljes mértékben rendelkezésre állnak. Abban a pillanatban, hogy minden adatbázis készen áll a készlet új eDTU-értékével való engedélyezésre, minden aktív kapcsolat megszakad. Az alkalmazás kódját mindig úgy kell megírni, hogy újrapróbálja a megszakított kapcsolatokat, így újra csatlakozni fog az adatbázishoz a felskálázott készletben.

## <a name="load-balance-between-pools"></a>A készletek közötti terhelési egyensúly

A készlet felskálázása mellett másik lehetőségként létrehozhat egy második készletet és áthelyezhet abba adatbázisokat, hogy kiegyenlítse a két készlet terhelését. Ehhez az új készletet ugyanazon a kiszolgálón kell létrehozni, amelyen az első is megtalálható.

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg a **tenants1-dpt-&lt;USER&gt; ** kiszolgálót.
1. Kattintson **a + Új készlet** elemre, ha az aktuális kiszolgálón szeretne készletet létrehozni.
1. A **rugalmas** készletsablonon:

   1. Állítsa **a név készlet2** *beállítását.*
   1. A tarifacsomagnál hagyja meg a **Standard készlet** beállítást.
   1. Kattintson **a Készlet konfigurálása**gombra, majd a
   1. Állítsa **a pool eDTU-t** *50 eDTU-ra.*
   1. Kattintson az **Adatbázisok hozzáadása** gombra a kiszolgálón lévő, a *2.*
   1. Jelölje ki bármelyik 10 adatbázist, ha ezeket át szeretné helyezni az új készletbe, majd kattintson a **Kijelölés gombra.** Ha már fut a terhelésgenerátor, a szolgáltatás már tudja, hogy a teljesítményprofil igényel nagyobb készlet, mint az alapértelmezett 50 eDTU-méret, és azt javasolja, kezdve a 100 eDTU-beállítás.

      ![Ajánlás](media/saas-dbpertenant-performance-monitoring/configure-pool.png)

   1. Ebben az oktatóanyagban hagyja az alapértelmezett értéket 50 eDT-ben, és kattintson ismét a **Kijelölés** gombra.
   1. Válassza az **OK gombot** az új készlet létrehozásához és a kijelölt adatbázisok áthelyezéséhez.

A készlet létrehozása és az adatbázisok áthelyezése néhány percet vesz igénybe. Az adatbázisok áthelyezése során az utolsó pillanatig online állapotban maradnak, és teljes mértékben hozzáférhetők maradnak, amikor is a nyitott kapcsolatok lezárulnak. Mindaddig, amíg van néhány újrapróbálkozási logika, az ügyfelek majd csatlakozni az adatbázishoz az új készletben.

Tallózással keresse meg a **Pool2** (a *bérlők1-dpt-\<felhasználói\> * kiszolgáló) nyissa meg a készletet, és figyelje a teljesítményét. Ha nem látja, várja meg, amíg az új készlet kiépítése befejeződik.

Most már láthatja, hogy az erőforrás-használat a *Készlet1-en* csökkent, és hogy *a Pool2* most hasonlóan töltődik be.

## <a name="manage-performance-of-an-individual-database"></a>Egyéni adatbázis teljesítményének kezelése

Ha egy készletben egy adott adatbázis tartósan nagy terhelést tapasztal, a készlet konfigurációjától függően, előfordulhat, hogy uralja a készlet erőforrásait, és hatással van más adatbázisokra. Ha a tevékenység valószínűleg egy ideig folytatódik, az adatbázis ideiglenesen áthelyezhető a készletből. Ez lehetővé teszi, hogy az adatbázis rendelkezik a szükséges további erőforrásokkal, és elkülöníti a többi adatbázistól.

Ez a gyakorlat a Contoso Concert Hall magas terhelésének a hatását szimulálja, amikor megkezdődik a jegyek árusítása egy népszerű koncertre.

1. A **PowerShell ISE-ben**nyissa meg a ... \\ *Demo-PerformanceMonitoringAndManagement.ps1* parancsfájlt.
1. Állítsa be **$DemoScenario = 5, Normál terhelés létrehozása plusz egy nagy terhelés egyetlen bérlőn (körülbelül 95 DTU).**
1. Állítsa be a **$SingleTenantDatabaseName = contosoconcerthall**értéket.
1. Futtassa a szkriptet az **F5** billentyűvel.


1. Az [Azure Portalon](https://portal.azure.com)keresse meg az adatbázisok listáját a *tenants1-dpt-\<felhasználói\> * kiszolgálón. 
1. Kattintson a **contosoconcerthall** adatbázis.
1. Kattintson a medencére, ahol **contosoconcerthall** van. Keresse meg a készletet a **rugalmas készlet** szakaszban.

1. Vizsgálja meg a **rugalmas készlet figyelési** diagramot, és keresse meg a megnövekedett készlet eDTU-használat. Egy-két perc után jelentkezik a magas terhelés, aminek következtében a készlet eléri a 100%-os kihasználtságot.
2. Vizsgálja meg a **rugalmas adatbázis figyelési** kijelző, amely az elmúlt óra legforróbb adatbázisokat jeleníti meg. A *contosoconcerthall* adatbázis hamarosan az öt legmenőbb adatbázis egyikeként jelenik meg.
3. **Kattintson a rugalmas adatbázis figyelési** **diagramra,** és megnyitja az **Adatbázis-erőforrás-kihasználtság** lapot, ahol az adatbázisok bármelyikét figyelheti. Ez lehetővé teszi a *contosoconcerthall* adatbázis megjelenítésének elkülönítését.
4. Az adatbázisok listájából kattintson a **contosoconcerthall**lehetőségre.
5. Kattintson **a Tarifaszint (méretezési DTO-k)** gombra a **Teljesítmény konfigurálása** lap megnyitásához, ahol önálló számítási méretet állíthat be az adatbázishoz.
6. Kattintson a **Standard** lapra a Standard csomag skálázási beállításainak megnyitásához.
7. A **100** DTU-kód kijelöléséhez csúsztassa jobbra a **DTU csúszkát.** Megjegyzés: ez megfelel az **S3**szolgáltatási célkitűzésnek.
8. Kattintson az **Alkalmaz gombra,** ha az adatbázist ki szeretné helyezni a készletből, és *szabványos S3-adatbázissá* szeretné tenni.
9. A skálázás befejezése után figyelje a contosoconcerthall adatbázisra és a Pool1-re gyakorolt hatást a rugalmas készletre és az adatbázis-pengékre.

Miután a contosoconcerthall adatbázis nagy terhelése megszűnik, azonnal vissza kell küldenie a készletbe a költségek csökkentése érdekében. Ha nem világos, hogy ez mikor fog megtörténni, beállíthatja az adatbázisban, amely akkor aktiválódik, ha a DTU-használat a készlet adatbázisonkénti maximális értékalá csökken. Egy adatbázis készletbe történő áthelyezésének menetét az 5. gyakorlat írja le.

## <a name="other-performance-management-patterns"></a>Egyéb teljesítménykezelési minták

**Megelőző méretezés** A fenti gyakorlatban, ahol felderítette, hogyan kell egy elszigetelt adatbázis méretezése, tudta, hogy melyik adatbázist kell keresni. Ha a Contoso Concert Hall vezetősége tájékoztatta volna a Wingtips-et a közelgő jegyértékesítésről, az adatbázist megelőzően kilehetett volna helyezni a készletből. Máskülönben valószínűleg egy riasztást kellett volna beállítani a készleten vagy az adatbázison ahhoz, hogy észre lehessen venni, mi történik. Nem jó, ha az ilyen eseményekről úgy szerez tudomást, hogy a készletben található többi bérlő csökkenő teljesítményről panaszkodik. Ha a bérlő meg tudja jósolni, hogy milyen hosszan lesz szüksége további erőforrásokra, beállítható egy Azure Automation-runbook, amely pontosan ütemezi az adatbázis kivételét, majd visszahelyezését a készletbe.

**Bérlők általi önkiszolgáló skálázás** Mivel a skálázási feladat könnyen meghívható a felügyeleti API-n keresztül, így a bérlői adatbázisok méretezésének lehetősége könnyen beépíthető a bérlőoldali alkalmazásba, és felkínálható az SaaS-szolgáltatás egy funkciójaként. Például a bérlők saját maguk adminisztrálhatják a vertikális fel- és leskálázást, ami előfordulhat, hogy közvetlen kapcsolatban áll a számlázásukkal.

**Készletek használati mintákhoz igazított, ütemezett vertikális fel- és leskálázása**

Ha az összesített bérlői használat kiszámítható mintákat követ, az Azure Automationnel ütemezni tudja a készletek vertikális fel- és leskálázását. Például egy készletet hétköznapokon este 6 után leskálázhat, reggel 6 előtt pedig felskálázhat, ha tudja, hogy a két időpont között csökken az erőforrásigény.



## <a name="next-steps"></a>További lépések

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * A bérlői adatbázisok használatának szimulálása egy adott terhelésgenerátor futtatásával
> * A bérlői adatbázisok megnövekedett terhelésre adott válaszának megfigyelése
> * A rugalmas készlet vertikális felskálázása az adatbázis megnövekedett terhelésére adott válaszul
> * Egy második rugalmas készlet kiépítése az adatbázis-tevékenységek terhelésének kiegyenlítésére

[Egyetlen bérlő visszaállítása – oktatóanyag](saas-dbpertenant-restore-single-tenant.md)


## <a name="additional-resources"></a>További források

* További [oktatóanyagok, amelyek a Wingtip jegyek SaaS-adatbázis bérlőnkénti alkalmazásonkénti üzembe helyezésére épülnek](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [SQL rugalmas készletek](sql-database-elastic-pool.md)
* [Azure Automation](../automation/automation-intro.md)
* [Azure Monitor-naplók](saas-dbpertenant-log-analytics.md) – Az Azure Monitor-naplók oktatóanyagának beállítása és használata
