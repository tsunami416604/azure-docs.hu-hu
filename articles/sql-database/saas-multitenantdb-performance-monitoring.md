---
title: Szilánkokra osztott több-bérlős Azure SQL-adatbázis egy több-bérlős SaaS-alkalmazás teljesítményének figyelése |} A Microsoft Docs
description: Szilánkokra osztott több-bérlős egy több-bérlős SaaS-alkalmazás az Azure SQL adatbázis teljesítményének figyelése és kezelése
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: be7dbe35800bbe911bc56d1883462534a16499a0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58083181"
---
# <a name="monitor-and-manage-performance-of-sharded-multi-tenant-azure-sql-database-in-a-multi-tenant-saas-app"></a>Szilánkokra osztott több-bérlős egy több-bérlős SaaS-alkalmazás az Azure SQL adatbázis teljesítményének figyelése és kezelése

Ebben az oktatóanyagban használt SaaS-alkalmazásokban több fő felügyeleti forgatókönyvek teljesítménykezelési. A terhelésgenerátor tevékenység szimulálása között több-bérlős szilánkokra osztott adatbázisok használatával, a beépített figyelési és riasztási funkcióit az SQL Database találja meg.

A Wingtip Tickets SaaS több-bérlős adatbázis app egy több-bérlős horizontálisan skálázott adatok modellt használ, ahol helyszín (bérlő) adatok van-e terjesztve Bérlőazonosító potenciálisan több adatbázis között. Sok más SaaS-alkalmazáshoz hasonlóan a bérlői számítási feladatok várt mintája kiszámíthatatlan és szórványos. Ez a gyakorlatban azt jelenti, hogy a jegyeladásokra bármikor sor kerülhet. Ezen tipikus adatbázis-felhasználási minta kihasználásához adatbázisok méretezhetők felfelé és lefelé optimalizálhatja a megoldások költségeit. Az ebben a típusú mintánál fontos győződjön meg arról, hogy terhelések egyenletesen oszoljanak esetleg több adatbázisban az adatbázis erőforrás-használat figyelése. Győződjön meg arról, hogy az egyes adatbázisok erőforrással rendelkezzenek, és nem találkozik kell azok [DTU](sql-database-purchase-models.md#dtu-based-purchasing-model) korlátok. Ez az oktatóanyag bemutatja, figyelése és felügyelete az adatbázisokat, és hogyan lehet a számítási feladatok változásaira adott korrekciós művelet végrehajtása módjai.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> 
> * Egy több-bérlős szilánkokra osztott adatbázis használatának szimulálása egy adott terhelésgenerátor futtatásával
> * Reagáljon a megnövekedett az adatbázis figyelése
> * Vertikális felskálázása az adatbázis az adatbázis megnövekedett terhelésére adott válaszul
> * Új bérlő kiépítéséhez egy egybérlős adatbázisba

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip Tickets SaaS több-bérlős adatbázis-alkalmazás telepítve van. Kevesebb mint öt perc alatt üzembe helyezéséhez lásd: [üzembe helyezés a Wingtip Tickets SaaS több-bérlős adatbázis-alkalmazás és megismerése](saas-multitenantdb-get-started-deploy.md)
* Az Azure PowerShell telepítve van. A részletekért lásd: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Teljesítménykezelési minták SaaS bemutatása

Az adatbázisteljesítmény-kezelés a teljesítményadatok fordításából és elemzéséből, majd az adatokra való reagálásból áll. Ez tulajdonképpen a paraméterek módosítását jelenti, miáltal az alkalmazás válaszideje elfogadható szinten marad. 

### <a name="performance-management-strategies"></a>Teljesítménykezelési stratégiák

* Ne kelljen manuálisan a teljesítmény figyelésére, a leghatékonyabb **riasztások beállítását, melyek indítható el, ha az adatbázisok kilépnek az engedélyezett tartományból teljesítményfigyelésnél**.
* Az adatbázis, a számítási méret rövidtávú ingadozásaira a **DTU-szintje vertikálisan fel, felfelé vagy lefelé**. Ha az ingadozás rendszeres vagy Kiszámítható alapon **automatikus skálázás az adatbázis ütemezhető**. Beállítható például a vertikális leskálázás, amikor előre láthatóan kevés lesz a számítási feladat, például éjjelente vagy a hétvégi napokon.
* Válaszoljon ingadozásokra vagy a bérlők változásai **az egyes bérlők is áthelyezhetők, másik adatbázisba**.
* A rövid távú növekedése válaszolni *egyes* bérlői terhelés, **az egyes bérlők kívül egy adatbázist, és egy adott számítási méret hozzárendelt**. Ha a terhelés csökkenésével a bérlő majd adhatók vissza a több-bérlős adatbázishoz. Ha ez az előzetesen ismert, bérlők áthelyezhetők jegyértékesítésről az adatbázis mindig legyen az ehhez szükséges erőforrásokat, és a több bérlős adatbázisban található többi bérlő gyakorolt hatás elkerülése érdekében. Ha ez a szükséglet előre kiszámítható, például ha egy helyszín nagy mennyiségű növekedésre számít a jegyeladásokban egy népszerű esemény miatt, akkor ez a kezelési viselkedés integrálható az alkalmazásba.

Az [Azure Portal](https://portal.azure.com) a legtöbb erőforráshoz beépített figyelési és riasztási lehetőségeket biztosít. SQL-adatbázis monitorozási és riasztási érhető el az adatbázisok. A beépített figyelés és riasztás erőforrás-specifikus, így azt a kis számú használni, de nem kényelmes, sok erőforrás használatakor.

Nagy mennyiségű forgatókönyvekhez, ahol sok erőforrásokkal dolgozik, [naplózza az Azure Monitor](https://azure.microsoft.com/services/log-analytics/) is használható. Ez az egy külön Azure-szolgáltatás, amely biztosít elemzési kibocsátott diagnosztikai naplók és a Log Analytics-munkaterületen gyűjtött telemetria. Az Azure Monitor naplóira számos szolgáltatásból képes telemetriai adatok gyűjtésére, és lekérdezheti, és riasztásokat állíthat be.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>A Wingtip Tickets SaaS több-bérlős adatbázis forráskódjához és parancsfájlok

A Wingtip Tickets SaaS több-bérlős adatbázis parancsfájlok és az alkalmazás forráskódjának érhető el a [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub-adattárban. Tekintse meg a [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) töltse le és a Wingtip Tickets SaaS-parancsfájlok feloldása.

## <a name="provision-additional-tenants"></a>További bérlők kiépítése

Az alkalmazásteljesítmény-figyelés és kezelés működését ipari méretekben kellőképpen szemléltetni lehessen, az oktatóanyaghoz, hogy egy több-bérlős szilánkokra osztott adatbázis több bérlővel rendelkezik.

Ha egy korábbi oktatóanyagban már kiépített bérlők egy kötegét, ugorjon a [összes bérlői adatbázis használatának szimulálása](#simulate-usage-on-all-tenant-databases) szakaszban.

1. Az a **PowerShell ISE-ben**, nyissa meg... \\Tanulási modulok\\alkalmazásteljesítmény-figyelési és felügyeleti\\*Demo-PerformanceMonitoringAndManagement.ps1*. Tartsa ezt a szkriptet nyitva, mivel az oktatóanyag során több különböző forgatókönyvet is futtatnia kell majd.
1. Válassza a **$DemoScenario** = **1**, _Bérlők kötegelt kiépítése_ lehetőséget.
1. A szkriptek futtatásához nyomja le az **F5** billentyűt.

A szkript üzembe helyezi, néhány perc alatt 17 bérlőt a több-bérlős adatbázisba. 

A *New-TenantBatch* parancsfájl új bérlők egyedi bérlővel-kulcsokat hoz létre a horizontálisan skálázott több-bérlős adatbázisban és inicializálja őket a bérlő nevével és a helyszíntípussal típusát. Ez az alkalmazás kiosztja az új bérlő módja összhangban. 

## <a name="simulate-usage-on-all-tenant-databases"></a>Az összes bérlői adatbázis használatának szimulálása

A *Demo-PerformanceMonitoringAndManagement.ps1* parancsfájl, amely kiható terhelési a több-bérlős adatbázisban kerül. A terhelés a véleménypontszámok generálásáról az egyik rendelkezésre álló betöltése:

| Bemutató | Forgatókönyv |
|:--|:--|
| 2 | Normál intenzitású terhelés (nagyjából 30 DTU) |
| 3 | Bérlőnként hosszabb adatlöketekkel terhelés létrehozása|
| 4 | Magasabb DTU-löketekkel (KB 70 DTU) bérlőnként terhelés létrehozása|
| 5 | A magas intenzitása (körülbelül 90 DTU) az egyetlen új bérlő, valamint az összes többi bérlő normál intenzitású terhelés létrehozása |

A terhelésgenerátor egy *szintetikus* CPU-terhelést alkalmaz az összes bérlői adatbázison. A generátor minden bérlői adatbázis számára elindít egy feladatot, amely időközönként meghív egy, a terhelést létrehozó tárolt eljárást. A terhelések szintje (a dtu-k), időtartama és időközönként sokfélék minden adatbázis szimulálva a kiszámíthatatlan bérlői aktivitást.

1. Az a **PowerShell ISE-ben**, nyissa meg... \\Tanulási modulok\\alkalmazásteljesítmény-figyelési és felügyeleti\\*Demo-PerformanceMonitoringAndManagement.ps1*. Tartsa ezt a szkriptet nyitva, mivel az oktatóanyag során több különböző forgatókönyvet is futtatnia kell majd.
1. Állítsa be **$DemoScenario** = **2**, _normál intenzitású terhelés létrehozása_
1. Nyomja meg **F5** betöltés alkalmazni a bérlők számára.

A Wingtip Tickets SaaS több bérlős adatbázisban egy SaaS-alkalmazás, és a való életből vett terhelés egy SaaS-alkalmazás általában szórványosak és kiszámíthatatlanok. Ennek szimulálására a terhelésgenerátor az összes bérlő között elosztott, véletlenszerű terhelést hoz létre. Több perc szükséges a terhelés minta bontakozik ki, ezért futtassa a terhelésgenerátort 3 – 5 percet, mielőtt megkísérelné a következő szakaszok a terheléseket figyelni.

> [!IMPORTANT]
> A terhelésgenerátor egy új PowerShell-ablakban feladatok sorozataként fut. Ha bezárja a munkamenetet, a terhelésgenerátor leáll. A terhelésgenerátor marad egy *feladat meghívása* állapot, ahol minden olyan új bérlők számára, miután elindult a generátor felhasznált terhelése állít elő. Használat *Ctrl-C* meghívása új feladatok leállítása és lépjen ki a parancsprogramot. A terhelésgenerátor továbbra is futtatja, de csak a meglévő bérlők számára.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Az Azure portal használatával erőforrás-használat figyelése

Az eredmények közül a terhelések erőforrás-használat figyeléséhez, nyissa meg a portálon a több-bérlős adatbázissal **tenants1**, amely tartalmazza a bérlők számára:

1. Nyissa meg a [az Azure portal](https://portal.azure.com) , és keresse meg a kiszolgáló *tenants1-mt -&lt;felhasználói&gt;*.
1. Görgessen lefelé, és keresse meg az adatbázisok, és kattintson a **tenants1**. A több-bérlős szilánkokra osztott adatbázis eddig létrehozott összes bérlőre vonatkozóan tartalmazza.

![Adatbázis-diagram](./media/saas-multitenantdb-performance-monitoring/multitenantdb.png)

Figyelje meg a **DTU** diagram.

## <a name="set-performance-alerts-on-the-database"></a>Teljesítményriasztások beállítása az adatbázis

Riasztások beállítása az adatbázis, amely elindítja a \>75 %-os kihasználtságot, az alábbiak szerint:

1. Nyissa meg a *tenants1* database (a a *tenants1-mt -&lt;felhasználói&gt;*  kiszolgáló) a a [az Azure portal](https://portal.azure.com).
1. Kattintson a **Riasztási szabályok** elemre, majd a **+ Riasztás hozzáadása** gombra:

   ![riasztás hozzáadása](media/saas-multitenantdb-performance-monitoring/add-alert.png)

1. Adjon meg egy nevet, például: **Magas DTU**.
1. Állítsa be a következő értékeket:
   * **Metrika = DTU százalékos értéke**
   * **Feltétel = nagyobb, mint**
   * **Küszöbérték = 75**.
   * **Időtartam = az elmúlt 30 percben**
1. Az e-mail cím hozzáadása a *további rendszergazdai email(s)* mezőbe, majd kattintson a **OK**.

   ![riasztás beállítása](media/saas-multitenantdb-performance-monitoring/set-alert.png)

## <a name="scale-up-a-busy-database"></a>Vertikális felskálázás foglalt adatbázis

Ha a terhelés szintje nő, a pont, hogy ki az adatbázis lefoglalja és eléri a 100 %-os DTU-használatát egy adatbázison, majd adatbázis teljesítménye érintett, lelassíthatja a lekérdezések válaszidejét.

**Rövid távon**, amennyiben további erőforrás az adatbázis vertikális felskálázásával, vagy távolítsa el a bérlők a több-bérlős adatbázisból (a több-bérlős adatbázisból történő áthelyezése egy önálló adatbázis).

**Hosszabb távon**, fontolja meg a lekérdezések optimalizálását vagy index használatát az adatbázis-teljesítmény javítása. Az alkalmazás teljesítményingadozásokra való függően problémák az ajánlott eljárás egy adatbázis vertikális felskálázás, mielőtt az eléri a 100 %-os DTU-használatát. Használjon olyan riasztást, amely előre figyelmezteti Önt.

Foglalt adatbázis a generátor által létrehozott terhelés növelésével szimulálhat. Okozza a bérlők gyakrabban, valamint a továbbiakban, folyamatosan növekvő a több-bérlős adatbázis terhelését az egyes bérlők követelményeinek megváltoztatása nélkül. Az adatbázis vertikális felskálázása könnyedén elvégezhető a portálon vagy a PowerShell használatával. A gyakorlat során a Portalt használjuk.

1. Állítsa be *$DemoScenario* = **3**, _terhelés létrehozása adatbázisonkénti hosszabb és gyakoribb adatlöketekkel_ összesített terhelési intenzitását növelik a az adatbázis minden bérlő csúcsterhelési módosítása nélkül.
1. Nyomja le az **F5** billentyűt, hogy az összes bérlői adatbázist érje terhelés.
1. Nyissa meg a **tenants1** adatbázis az Azure Portalon.

Figyelheti az adatbázis megnövekedett DTU-használatát a felső diagramon. Az új magasabb terhelés, néhány percig tart, de hamarosan látni az adatbázis maximális kihasználtsága elkezdi, és a terhelés stabilan be az új minta, ahogy azt beáll az adatbázis.

1. Az adatbázis vertikális felskálázásához kattintson **tarifacsomag (dtu-k)** a beállítások panelen.
1. Módosítsa a **DTU** beállítást **100**. 
1. Kattintson a **alkalmaz** az adatbázis méretezése a kérelem elküldéséhez.

Lépjen vissza a **tenants1** > **áttekintése** megtekintése a figyelési diagramjait. Figyelje meg, így az adatbázis több erőforrást (habár néhány bérlők és véletlenszerű terhelés nem mindig könnyen von látható, amíg nem futtat egy kis ideig). Közben diagramok megtekintése vegye figyelembe, hogy 100 % felső diagramon most már jelöli 100 dtu-k, a pedig az alsó diagramon látható 100 % továbbra is 50 dtu-k.

Az adatbázisok a folyamat során végig online állapotban maradnak, és teljes mértékben rendelkezésre állnak. Alkalmazáskód kell mindig írni újrapróbálja a megszakított kapcsolatokat, és így újra csatlakozni fog az adatbázishoz.

## <a name="provision-a-new-tenant-in-its-own-database"></a>A saját adatbázisát egy új bérlő kiépítése 

A horizontálisan skálázott több-bérlős modell lehetővé teszi, hogy válassza ki, hogy egy több-bérlős adatbázisban mellett más bérlők új bérlő kiépítése, vagy saját egy adatbázisban az új bérlő kiépítéséhez. A saját adatbázisát egy bérlő kiépítésével előnyös az önálló adatbázis, így kezelheti a bérlőt, mások függetlenül teljesítményét, illetve mások számára, függetlenül a bérlő visszaállítása rejlő elkülönítési stb. Például választhatja a különálló adatbázisok ingyenes próbaidőszakot, vagy rendszeres ügyfelek egy több-bérlős adatbázisban, és a prémium szintű ügyfelek helyezi.  Egybérlős elszigetelt adatbázisok jönnek létre, ha azok továbbra is kezelhető együttesen egy rugalmas készletben erőforrás a költségek optimalizálása érdekében.

Ha már kiépített egy új bérlőt a saját adatbázis, a következő néhány lépés kihagyható.

1. Az a **PowerShell ISE-ben**, nyissa meg... \\Tanulási modulok\\ProvisionTenants\\*Demo-ProvisionTenants.ps1*. 
1. Módosítsa **$TenantName = "Fűzfa milyen"** és **$VenueType = "helyettünk"**
1. Állítsa be **$Scenario** = **2**, _az egybérlős adatbázis új bérlő kiépítéséhez_
1. A szkriptek futtatásához nyomja le az **F5** billentyűt.

A parancsfájl egy önálló adatbázisban az új bérlő kiépítéséhez, az adatbázis és a bérlő regisztrálásához a katalógusban, és nyissa meg a bérlő események lapot a böngészőben. Frissítse az Eseményközpontot oldalt, és látni fogja a hozzáadott "Fűzfa milyen" egy helyszín.

## <a name="manage-performance-of-an-individual-database"></a>Az egyes adatbázisok teljesítményének kezelése

Ha egy több-bérlős adatbázison belül egyetlen bérlő tartósan magas terhelés, akkor előfordulhat, hogy általában uralja az adatbázis-erőforrások és befolyásolja a többi bérlő ugyanabban az adatbázisban. A tevékenység várhatóan huzamosabb ideig, a bérlő ideiglenesen áthelyezhető-e saját egybérlős adatbázis és az adatbázisból. Ez lehetővé teszi a bérlő számára a további erőforrások van szüksége, és teljes mértékben elkülöníti a többi bérlőtől.

Ebben a gyakorlatban a fűzfa milyen magasabb terhelés tapasztal, amikor a jegyek árusítása egy népszerű esemény hatását szimulálja.

1. Nyissa meg a... \\ *Demo-PerformanceMonitoringAndManagement.ps1* parancsfájlt.
1. Állítsa be **$DemoScenario = 5**, _normál terhelés, valamint magasabb terhelés létrehozása egyetlen bérlő (körülbelül 90 dtu-k) létrehozása._
1. Állítsa be **$SingleTenantName fűzfa milyen =**
1. Futtassa a szkriptet az **F5** billentyűvel.

Ugrás a portálra, és navigáljon a **salixsalsa** > **áttekintése** megtekintése a figyelési diagramjait. 

## <a name="other-performance-management-patterns"></a>Egyéb teljesítménykezelési minták

**Bérlői önkiszolgáló skálázás**

Mivel a skálázási feladat könnyen nevű a felügyeleti API-n keresztül, egyszerűen a bérlőoldali alkalmazásba bérlői adatbázisok méretezésének lehetősége, és felkínálható az SaaS-szolgáltatás egy funkciójaként. Például a bérlők saját maguk adminisztrálhatják a vertikális fel- és leskálázást, ami előfordulhat, hogy közvetlen kapcsolatban áll a számlázásukkal.

**Egy adatbázis felfelé és lefelé skálázás használati mintákhoz ütemezés szerint**

Összesített bérlői használat kiszámítható mintákat követ, ahol használhatja az Azure Automation vertikális felfelé és lefelé ütemezés szerint. Például méretezése Este 6 után egy adatbázist, és nincs az erőforrásigény legördülő hétköznapokon, ha ismeri reggel 6 előtt fel.

## <a name="next-steps"></a>További lépések

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Egy több-bérlős szilánkokra osztott adatbázis használatának szimulálása egy adott terhelésgenerátor futtatásával
> * Reagáljon a megnövekedett az adatbázis figyelése
> * Vertikális felskálázása az adatbázis az adatbázis megnövekedett terhelésére adott válaszul
> * Új bérlő kiépítéséhez egy egybérlős adatbázisba

## <a name="additional-resources"></a>További források

<!--* [Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Azure Automation](../automation/automation-intro.md)