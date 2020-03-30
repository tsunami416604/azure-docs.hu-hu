---
title: Szilánkos több-bérlős adatbázis teljesítményének figyelése
description: Szilánkos több-bérlős Azure SQL-adatbázis teljesítményének figyelése és kezelése több-bérlős SaaS-alkalmazásban
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
ms.openlocfilehash: 0af476b69f2effd836fe76d62059259076c16f53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214159"
---
# <a name="monitor-and-manage-performance-of-sharded-multi-tenant-azure-sql-database-in-a-multi-tenant-saas-app"></a>Szilánkos több-bérlős Azure SQL-adatbázis teljesítményének figyelése és kezelése több-bérlős SaaS-alkalmazásban

Ebben az oktatóanyagban a rendszer a SaaS-alkalmazásokban használt számos kulcsfontosságú teljesítménykezelési forgatókönyvet vizsgálja meg. Egy terhelésgenerátor segítségével szimulálja a tevékenység szilánkos több-bérlős adatbázisok közötti tevékenység, az SQL Database beépített figyelési és riasztási funkcióit.

A Wingtip jegyek SaaS több-bérlős adatbázis-alkalmazás egy szilánkos több-bérlős adatmodell, ahol a helyszín (bérlő) adatok at bérlői azonosító potenciálisan több adatbázis között. Sok más SaaS-alkalmazáshoz hasonlóan a bérlői számítási feladatok várt mintája kiszámíthatatlan és szórványos. Ez a gyakorlatban azt jelenti, hogy a jegyeladásokra bármikor sor kerülhet. A tipikus adatbázis-használati minta előnyeinek kihasználása érdekében az adatbázisok fel- és leskálázhatók a megoldás költségének optimalizálása érdekében. Az ilyen típusú minta, fontos, hogy az adatbázis-erőforrások használatának figyelése annak érdekében, hogy a terhelések ésszerűen kiegyensúlyozott potenciálisan több adatbázis között. Azt is meg kell győződnie arról, hogy [DTU](sql-database-purchase-models.md#dtu-based-purchasing-model) az egyes adatbázisok megfelelő erőforrásokkal rendelkeznek, és nem ütik meg a DTU-korlátokat. Ez az oktatóanyag az adatbázisok figyelésének és kezelésének módjait, valamint a munkaterhelés változásaira adott korrekciós intézkedéseket ismerteti.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> 
> * A rendszer által biztosított terhelésgenerátor futtatásával szimulálja a használatot egy több-bérlős csoporton lévő adatbázisban
> * Az adatbázis figyelése a terhelés növekedésére adott válaszként
> * Az adatbázis méretének növelése a megnövekedett adatbázis-terhelésre válaszul
> * Bérlő kiépítése egy egybérlős adatbázisba

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip jegyek SaaS több-bérlős adatbázis alkalmazás telepítve van. Ha kevesebb, mint öt perc alatt üzembe szeretne helyezni, [olvassa el a Wingtip Tickets SaaS több-bérlős adatbázis-alkalmazás üzembe helyezése és feltárása című témakört.](saas-multitenantdb-get-started-deploy.md)
* Az Azure PowerShell telepítve van. Részletes információk: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Bevezetés a SaaS teljesítménykezelési mintáiba

Az adatbázisteljesítmény-kezelés a teljesítményadatok fordításából és elemzéséből, majd az adatokra való reagálásból áll. Ez tulajdonképpen a paraméterek módosítását jelenti, miáltal az alkalmazás válaszideje elfogadható szinten marad. 

### <a name="performance-management-strategies"></a>Teljesítménykezelési stratégiák

* A teljesítmény manuális figyelésének elkerülése érdekében a leghatékonyabb, ha olyan **riasztásokat állít be, amelyek akkor aktiválódnak, ha az adatbázisok a normál tartományon kívül rekednek.**
* Az adatbázis számítási méretének rövid távú ingadozásaira való reagálás érdekében a **DTU-szint fel- vagy leskálázható.** Ha ez az ingadozás rendszeres en vagy előre jelezhető módon jelentkezik, **az adatbázis méretezése ütemezhető úgy, hogy automatikusan bekövetkezzen.** Beállítható például a vertikális leskálázás, amikor előre láthatóan kevés lesz a számítási feladat, például éjjelente vagy a hétvégi napokon.
* A hosszabb távú ingadozásokra vagy a bérlők változásaira való reagálásérdekében az **egyes bérlők áthelyezhetők más adatbázisba.**
* Az *egyes* bérlők terhelésének rövid távú növekedésére való válaszul **az egyes bérlők kivehetők az adatbázisból, és egyéni számítási méretet rendelhetnek**hozzá. A terhelés csökkentése után a bérlő ezután visszavezethető a több-bérlős adatbázisba. Ha ez előre ismert, a bérlők megelőzően áthelyezhetők annak érdekében, hogy az adatbázis mindig rendelkezzen a szükséges erőforrásokkal, és hogy elkerülje a több-bérlős adatbázis más bérlőire gyakorolt hatást. Ha ez a szükséglet előre kiszámítható, például ha egy helyszín nagy mennyiségű növekedésre számít a jegyeladásokban egy népszerű esemény miatt, akkor ez a kezelési viselkedés integrálható az alkalmazásba.

Az [Azure Portal](https://portal.azure.com) a legtöbb erőforráshoz beépített figyelési és riasztási lehetőségeket biztosít. Az SQL Database esetében a figyelés és a riasztás elérhető az adatbázisokban. Ez a beépített figyelési és riasztási erőforrás-specifikus, így célszerű használni a kis számú erőforrás, de nem kényelmes, ha sok erőforrással dolgozik.

Nagy mennyiségű forgatókönyvek, ahol dolgozik sok erőforrást, [az Azure Monitor naplók](https://azure.microsoft.com/services/log-analytics/) is használható. Ez egy különálló Azure-szolgáltatás, amely a Log Analytics-munkaterületen összegyűjtött kibocsátott naplók elemzésével szolgál. Az Azure Monitor naplók számos szolgáltatás telemetriai adatokat gyűjthetnek, és riasztások lekérdezésére és beállítására használhatók.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>A Wingtip jegyek SaaS több-bérlős adatbázis-alkalmazás forráskódjának és parancsfájljainak beszereznie

A Wingtip jegyek SaaS több-bérlős adatbázis-parancsfájlok és az alkalmazás forráskód érhető el a [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub tárház. Tekintse meg az [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) a Wingtip Jegyek SaaS-parancsfájlok letöltéséhez és feloldásához szükséges lépésekhez.

## <a name="provision-additional-tenants"></a>További bérlők kiépítése

A teljesítményfigyelés és a felügyelet nagy méretekben való működésének alapos megismerése érdekében ez az oktatóanyag megköveteli, hogy több bérlővel rendelkezik egy horizontálisan skálázott több-bérlős adatbázisban.

Ha már kiépített egy bérlői köteget egy korábbi oktatóanyagban, ugorjon a [Használat szimulálása az összes bérlői adatbázison](#simulate-usage-on-all-tenant-databases) szakaszra.

1. A **PowerShell ISE**, nyitott ... \\Tanulási modulok\\teljesítményfigyelése és kezelése\\*Demo-PerformanceMonitoringAndManagement.ps1*. Tartsa ezt a szkriptet nyitva, mivel az oktatóanyag során több különböző forgatókönyvet is futtatnia kell majd.
1. **$DemoScenario** = **1$DemoScenario**beállítása , _Bérlők kötegének kiépítése_
1. A szkript futtatásához nyomja le az **F5** billentyűt.

A parancsfájl néhány perc alatt 17 bérlőt telepít a több-bérlős adatbázisba. 

Az *új TenantBatch-parancsfájl* új bérlőket hoz létre egyedi bérlői kulcsokkal a szilánkos több-bérlős adatbázisban, és inicializálja őket a bérlő nevével és helyszíntípusával. Ez összhangban van azzal, ahogyan az alkalmazás egy új bérlőt rendel el. 

## <a name="simulate-usage-on-all-tenant-databases"></a>Az összes bérlői adatbázis használatának szimulálása

A *Demo-PerformanceMonitoringAndManagement.ps1* parancsfájl biztosított, amely szimulálja a több-bérlős adatbázis on futó számítási feladatok. A terhelés a rendelkezésre álló betöltési forgatókönyvek egyikével jön létre:

| Bemutató | Forgatókönyv |
|:--|:--|
| 2 | Normál intenzitású terhelés generálása (körülbelül 30 DTU) |
| 3 | Bérlőnkénti hosszabb sorozatok generálása|
| 4 | Terhelés létrehozása bérlőnként magasabb DTU-adatlöketekkel (körülbelül 70 DTU)|
| 5 | Nagy intenzitás (körülbelül 90 DTU) létrehozása egyetlen bérlőn, valamint normál intenzitású terhelés az összes többi bérlőn |

A terhelésgenerátor egy *szintetikus* CPU-terhelést alkalmaz az összes bérlői adatbázison. A generátor minden bérlői adatbázis számára elindít egy feladatot, amely időközönként meghív egy, a terhelést létrehozó tárolt eljárást. A terhelési szintek (DDO-kban), az időtartam és az intervallumok az összes adatbázis között változnak, és előre nem látható bérlői tevékenységet szimulálnak.

1. A **PowerShell ISE**, nyitott ... \\Tanulási modulok\\teljesítményfigyelése és kezelése\\*Demo-PerformanceMonitoringAndManagement.ps1*. Tartsa ezt a szkriptet nyitva, mivel az oktatóanyag során több különböző forgatókönyvet is futtatnia kell majd.
1. Állítsa **be $DemoScenario** = **2**, _Normál intenzitású terhelés generálása_
1. Nyomja meg **az F5** billentyűt a terhelés alkalmazásához az összes bérlőre.

Wingtip jegyek SaaS több-bérlős adatbázis egy SaaS-alkalmazás, és a valós terhelés egy SaaS-alkalmazás általában szórványos és kiszámíthatatlan. Ennek szimulálására a terhelésgenerátor az összes bérlő között elosztott, véletlenszerű terhelést hoz létre. Néhány perc szükséges a terhelési minta kialakulásához, ezért futtassa a terhelésgenerátort 3-5 percig, mielőtt megkísérli a terhelés figyelését a következő szakaszokban.

> [!IMPORTANT]
> A terhelésgenerátor egy új PowerShell-ablakban feladatok sorozataként fut. Ha bezárja a munkamenetet, a terhelésgenerátor leáll. A terhelésgenerátor egy *feladat-meghívási* állapotban marad, ahol terhelést generál minden új bérlőn, amelyek a generátor indítása után vannak kiépítve. A *Ctrl-C billentyűvel* leállíthatja az új feladatok meghívását, és kiléphet a parancsfájlból. A terhelésgenerátor továbbra is fut, de csak a meglévő bérlők.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Erőforrás-használat figyelése az Azure Portal használatával

Az alkalmazott terhelésből eredő erőforrás-használat figyeléséhez nyissa meg a portált a több-bérlős adatbázisban, a **bérlők1,** amely a bérlőket tartalmazza:

1. Nyissa meg az [Azure Portalt,](https://portal.azure.com) és keresse meg a kiszolgáló *&lt;bérlők1-mt- USER&gt;*.
1. Görgessen le, keresse meg az adatbázisokat, és kattintson **a bérlők1**elemre. Ez a szilánkos több-bérlős adatbázis tartalmazza az eddig létrehozott összes bérlőt.

![adatbázis-diagram](./media/saas-multitenantdb-performance-monitoring/multitenantdb.png)

Figyelje **DTU** meg a DTU-diagramot.

## <a name="set-performance-alerts-on-the-database"></a>Teljesítményriasztások beállítása az adatbázisban

Állítson be egy riasztást \>az adatbázison, amely a 75%-os kihasználtságot indítja el az alábbiak szerint:

1. Nyissa meg a *tenants1 adatbázist* (a *tenants1-mt-&lt;USER&gt; * kiszolgálón) az [Azure Portalon.](https://portal.azure.com)
1. Kattintson a **Riasztási szabályok** elemre, majd a **+ Riasztás hozzáadása** gombra:

   ![riasztás hozzáadása](media/saas-multitenantdb-performance-monitoring/add-alert.png)

1. Adjon meg egy nevet, például: **Magas DTU**.
1. Állítsa be a következő értékeket:
   * **Metrikus = DTU százalék**
   * **Feltétel = nagyobb, mint**
   * **Küszöbérték = 75**.
   * **Időszak = Az elmúlt 30 percben**
1. Adjon hozzá egy e-mail címet a *További rendszergazdai e-mail(ek) mezőbe,* és kattintson az **OK**gombra.

   ![riasztás beállítása](media/saas-multitenantdb-performance-monitoring/set-alert.png)

## <a name="scale-up-a-busy-database"></a>Foglalt adatbázis felskálázása

Ha a terhelési szint addig a pontig növekszik az adatbázison, hogy kimaxolja az adatbázist, és eléri a 100%-os DTU-használatot, akkor ez befolyásolja az adatbázis teljesítményét, ami potenciálisan lassítja a lekérdezés válaszidejét.

**Rövid távon**fontolja meg az adatbázis további erőforrások biztosításához való felskálázását, vagy távolítsa el a bérlőket a több-bérlős adatbázisból (a több-bérlős adatbázisból egy önálló adatbázisba való áthelyezése).

**Hosszabb távon**érdemes optimalizálni a lekérdezéseket vagy az indexhasználatot az adatbázis teljesítményének javítása érdekében. Attól függően, hogy az alkalmazás teljesítményérzékenysége problémák az ajánlott eljárás az adatbázis méretezése, mielőtt eléri a 100%-os DTU-használat. Használjon olyan riasztást, amely előre figyelmezteti Önt.

A nagyságú adatbázis szimulálásával a generátor által termelt terhelés növelésével szimulálható. Ami a bérlők burst gyakrabban, és hosszabb ideig, növeli a terhelést a több-bérlős adatbázis az egyes bérlők igényeinek módosítása nélkül. Az adatbázis felskálázása könnyen elvégezhető a portálon vagy a PowerShellben. A gyakorlat során a Portalt használjuk.

1. Állítsa be *$DemoScenario* = **3**, Terhelés létrehozása hosszabb és _gyakoribb sorozatok adatbázisonként_ az adatbázis összesített terhelésének növelése az egyes bérlők által igényelt maximális terhelés módosítása nélkül.
1. Nyomja le az **F5** billentyűt, hogy az összes bérlői adatbázist érje terhelés.
1. Nyissa meg a **bérlők1** adatbázist az Azure Portalon.

A felső diagramon az adatbázis dtu-használatának növekedése. Néhány percet vesz igénybe, amíg az új nagyobb terhelés beindul, de gyorsan látnia kell, hogy az adatbázis elkezdi leadni a maximális kihasználtságot, és ahogy a terhelés az új mintába esik, gyorsan túlterheli az adatbázist.

1. Az adatbázis méretezéséhez kattintson a **Beállítási szint (méretezési DIT)** elemre a beállítások panelen.
1. Állítsa a **DTU** beállítást **100-ra.** 
1. Kattintson az **Alkalmaz** gombra az adatbázis méretezésére vonatkozó kérelem elküldéséhez.

Lépjen vissza a **bérlőkhöz1** > **Áttekintés** a figyelési diagramok megtekintéséhez. Figyelje az adatbázis több erőforrással való ellátásának hatását (bár kevés bérlővel és randomizált terheléssel nem mindig könnyű meggyőzően látni, amíg egy ideig nem fut). Miközben a diagramokat nézi, tartsa szem előtt, hogy a felső diagram 100% -a most 100 DSZ-t jelent, míg az alsó diagramon 100% még mindig 50 DT.

Az adatbázisok a folyamat során végig online állapotban maradnak, és teljes mértékben rendelkezésre állnak. Az alkalmazáskódot mindig meg kell írni az eldobott kapcsolatok újrapróbálkozásához, és így újra csatlakozni fog az adatbázishoz.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Új bérlő kiépítése a saját adatbázisában 

A szilánkos több-bérlős modell lehetővé teszi, hogy válassza ki, hogy egy új bérlő egy több-bérlős adatbázisban más bérlők mellett, vagy a bérlő kiépítése egy saját adatbázisban. A bérlő saját adatbázisában való kiépítésével a külön adatbázisban rejlő elkülönítés előnyeit élvezi, lehetővé téve a bérlő teljesítményét másoktól függetlenül, a bérlő másoktól függetlenül történő visszaállítását stb. Például dönthet úgy, hogy ingyenes próbaverziós vagy rendszeres ügyfelek egy több-bérlős adatbázisban, és a prémium ügyfelek az egyes adatbázisokban.  Ha elkülönített egy-bérlős adatbázisok jönnek létre, továbbra is kezelhetők együttesen egy rugalmas készlet erőforrásköltségek optimalizálása érdekében.

Ha már kiépített egy új bérlőt a saját adatbázisában, hagyja ki a következő néhány lépést.

1. A **PowerShell ISE**, nyitott ... \\Tanulási modulok\\provisiontenants\\*demo-provisiontenants.ps1*. 
1. Módosítsa **$TenantName = "Salix Salsa"** és **$VenueType = "tánc"**
1. **$Scenario** = **2$Scenario**beállítása , _Bérlő kiépítése egy új egybérlős adatbázisban_
1. A szkript futtatásához nyomja le az **F5** billentyűt.

A parancsfájl ezt a bérlőt egy külön adatbázisba építi ki, regisztrálja az adatbázist és a bérlőt a katalógussal, majd megnyitja a bérlő események lapját a böngészőben. Frissítse az Események központ oldalt, és látni fogja, hogy a "Salix Salsa" helyszínként lett hozzáadva.

## <a name="manage-performance-of-an-individual-database"></a>Egyéni adatbázis teljesítményének kezelése

Ha egy több-bérlős adatbázis egyetlen bérlője tartósan nagy terhelést tapasztal, előfordulhat, hogy uralja az adatbázis erőforrásait, és hatással van az adatbázis többi bérlője. Ha a tevékenység valószínűleg egy ideig folytatódik, a bérlő ideiglenesen áthelyezhető az adatbázisból a saját egybérlős adatbázisába. Ez lehetővé teszi, hogy a bérlő rendelkezik a szükséges további erőforrásokat, és teljesen elkülöníti azt a többi bérlőtől.

Ez a gyakorlat szimulálja a hatását Salix Salsa tapasztalható nagy terhelés, amikor jegyek et eladásra egy népszerű esemény.

1. Nyissa meg a ... \\ *Demo-PerformanceMonitoringAndManagement.ps1* parancsfájlt.
1. Állítsa be **$DemoScenario = 5,** _Hozzon létre egy normál terhelést, plusz egy nagy terhelést egyetlen bérlőn (körülbelül 90 DTU)._
1. Set **$SingleTenantName = Salix Salsa**
1. Futtassa a szkriptet az **F5** billentyűvel.

Lépjen a portálra, és keresse meg a **salixsalsa** > **áttekintése** a figyelési diagramok megtekintéséhez. 

## <a name="other-performance-management-patterns"></a>Egyéb teljesítménykezelési minták

**Bérlői önkiszolgáló méretezés**

Mivel a skálázás egy feladat könnyen megnevezhető a felügyeleti API-n keresztül, könnyen hozhat létre a bérlői adatbázisok méretezése a bérlőfelé néző alkalmazásba, és felajánlja, mint a SaaS-szolgáltatás szolgáltatása. Például a bérlők saját maguk adminisztrálhatják a vertikális fel- és leskálázást, ami előfordulhat, hogy közvetlen kapcsolatban áll a számlázásukkal.

**Adatbázis fel- és leméretezése ütemezés szerint a használati mintáknak megfelelően**

Ahol az összesített bérlői használat kiszámítható használati mintákat követ, az Azure Automation használatával fel-le skálázhatja az adatbázist egy ütemezés szerint. Például skálázhatja az adatbázist 18:00 után, majd hétköznap reggel 6 óra előtt, amikor tudja, hogy csökken az erőforrás-igény.

## <a name="next-steps"></a>További lépések

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * A rendszer által biztosított terhelésgenerátor futtatásával szimulálja a használatot egy több-bérlős csoporton lévő adatbázisban
> * Az adatbázis figyelése a terhelés növekedésére adott válaszként
> * Az adatbázis méretének növelése a megnövekedett adatbázis-terhelésre válaszul
> * Bérlő kiépítése egy egybérlős adatbázisba

## <a name="additional-resources"></a>További források

<!--* [Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Azure Automation](../automation/automation-intro.md)