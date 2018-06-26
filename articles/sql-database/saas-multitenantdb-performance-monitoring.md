---
title: A több-bérlős SaaS-alkalmazás egy szilánkos több-bérlős Azure SQL adatbázis teljesítményének figyelése |} Microsoft Docs
description: Megfigyelés és kezelés szilánkos több-bérlős egy több-bérlős SaaS-alkalmazás az Azure SQL adatbázis teljesítménye
keywords: sql database-oktatóanyag
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 75431715b5948525e92c99b778842d26a684da82
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36753445"
---
# <a name="monitor-and-manage-performance-of-sharded-multi-tenant-azure-sql-database-in-a-multi-tenant-saas-app"></a>Megfigyelés és kezelés szilánkos több-bérlős egy több-bérlős SaaS-alkalmazás az Azure SQL adatbázis teljesítménye

Ebben az oktatóanyagban használt SaaS-alkalmazásokhoz több alapvető teljesítményt felügyeleti lehetőségeket írja. A betöltési generátor használatával tevékenység szimulálása szilánkos több-bérlős az adatbázisok közötti, beépített figyelési és riasztási szolgáltatásokat az SQL-adatbázis egy.

A Wingtip jegyek SaaS több-bérlős adatbázis alkalmazás több-bérlős horizontálisan skálázott adatok modellt használ, ahol helyszínére (bérlői) adatok között van elosztva Bérlőazonosító által esetlegesen több adatbázis. Sok más SaaS-alkalmazáshoz hasonlóan a bérlői számítási feladatok várt mintája kiszámíthatatlan és szórványos. Ez a gyakorlatban azt jelenti, hogy a jegyeladásokra bármikor sor kerülhet. Az adatbázisok tipikus használati szokásokkal előnyeit, adatbázisok is méretezhető felfelé és lefelé optimalizálhatja a költség, a megoldás. Az ilyen típusú mintát fontos annak érdekében, hogy a terhelés ésszerűen kiegyensúlyozott potenciálisan több adatbázis közötti adatbázis-erőforrás felhasználásának figyelésére. Is biztosítania kell, hogy az egyes adatbázisok rendelkezik megfelelő erőforrásokkal, és nem elérte-e a [DTU](sql-database-service-tiers.md#what-are-database-transaction-units-dtus) korlátok. Ez az oktatóanyag ismerteti, felügyelhetik és kezelhetik az adatbázisok és a változások a terhelési válaszul intézkedéseket módon.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]

> * A megadott terheléselosztási generátor futtatásával szilánkos több-bérlős adatbázis-használata szimulálása
> * Az adatbázis figyelésére, reagáljon a megnövekedett terhelés
> * A nagyobb adatbázis-terhelésnek válaszul adatbázis méretezése
> * A bérlő kiépíteni egy bérlői adatbázis

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip jegyek SaaS több-bérlős adatbázis alkalmazás telepítve van. Kevesebb mint öt perc alatt telepítéséhez lásd: [központi telepítése és vizsgálja meg a Wingtip jegyek SaaS több-bérlős adatbázis-alkalmazás](saas-multitenantdb-get-started-deploy.md)
* Az Azure PowerShell telepítve van. A részletekért lásd: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Bevezetés a Szolgáltatottszoftver-teljesítmény felügyeleti minták

Az adatbázisteljesítmény-kezelés a teljesítményadatok fordításából és elemzéséből, majd az adatokra való reagálásból áll. Ez tulajdonképpen a paraméterek módosítását jelenti, miáltal az alkalmazás válaszideje elfogadható szinten marad. 

### <a name="performance-management-strategies"></a>Teljesítménykezelési stratégiák

* Ne kelljen manuálisan figyelemmel kísérni a teljesítményét, hogy a leghatékonyabb **riasztások beállítását, amelyek indul el, ha az adatbázisok kóbor kívül normál tartományok**.
* Egy adatbázis teljesítményszintjét a rövid távú ingadozását válaszolni a **DTU szint is méretezhető felfelé vagy lefelé**. Rendszeres vagy előre jelezhető alapon történik a értékingadozásait **automatikus ütemezése az adatbázis skálázás**. Beállítható például a vertikális leskálázás, amikor előre láthatóan kevés lesz a számítási feladat, például éjjelente vagy a hétvégi napokon.
* Válaszoljon a hosszabb távú ingadozását, vagy a bérlők a **az egyes bérlők anélkül áthelyezhetők más adatbázisba**.
* A rövid távú növekedése válaszolni *egyedi* bérlős terheléselosztóra, **az egyes bérlők adatbázis veszi, és az egyes teljesítményszintet hozzárendelt**. Csökken a terhelés, ha a bérlő majd adhatók vissza a több-bérlős adatbázishoz. Ha ez ismert előre, bérlők áthelyezhető pre-emptively ellenőrizze az adatbázis mindig rendelkezik-e a szükséges erőforrásokat, és a több-bérlős adatbázis más bérlők gyakorolt hatás elkerülése érdekében. Ha ez a szükséglet előre kiszámítható, például ha egy helyszín nagy mennyiségű növekedésre számít a jegyeladásokban egy népszerű esemény miatt, akkor ez a kezelési viselkedés integrálható az alkalmazásba.

Az [Azure Portal](https://portal.azure.com) a legtöbb erőforráshoz beépített figyelési és riasztási lehetőségeket biztosít. SQL-adatbázis figyelés és riasztás érhető el az adatbázisok. A beépített figyelés és riasztás érték erőforrás-specifikus, ezért célszerű a kis mennyiségű erőforrást használ, de nem nem megfelelő, ha sok erőforrást.

Nagy mennyiségű forgatókönyvek, ahol sok erőforrással rendelkező dolgozunk, [Naplóelemzési](https://azure.microsoft.com/services/log-analytics/) is használható. Ez a külön Azure szolgáltatás, amely analytics kibocsátott diagnosztikai naplók és a naplóelemzési munkaterület összegyűjtött telemetrikus keresztül. A Naplóelemzési sok szolgáltatásokból telemetriai adatokat gyűjthet, és lekérdezése, és állítson be riasztásokat használható.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Az alkalmazás forráskódjához Wingtip jegyek SaaS több-bérlős adatbázis és a parancsfájlok

A Wingtip jegyek SaaS több-bérlős adatbázis parancsfájlok és az alkalmazás forráskódjához érhetők el a [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub-tárház. Tekintse meg a [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) töltse le és feloldása a Wingtip jegyek Szolgáltatottszoftver-parancsfájlok lépéseit.

## <a name="provision-additional-tenants"></a>További bérlők kiépítése

Ez az oktatóanyag beható ismerete léptékű Teljesítményfigyelő és felügyeleti működése, megköveteli, hogy a több bérlő több-bérlős szilánkos adatbázisban.

Ha egy korábbi oktatóanyag már ellátta a bérlő köteg, ugorjon a [szimulálás használati összes bérlői adatbázisok](#simulate-usage-on-all-tenant-databases) szakasz.

1. Az a **PowerShell ISE**, nyissa meg... \\Tanulási modulok\\Teljesítményfigyelő és felügyeleti\\*bemutató-PerformanceMonitoringAndManagement.ps1*. Tartsa ezt a szkriptet nyitva, mivel az oktatóanyag során több különböző forgatókönyvet is futtatnia kell majd.
1. Válassza a **$DemoScenario** = **1**, _Bérlők kötegelt kiépítése_ lehetőséget.
1. A szkriptek futtatásához nyomja le az **F5** billentyűt.

A parancsfájl a több-bérlős adatbázisba 17 bérlők telepít néhány perc múlva. 

A *New-TenantBatch* parancsfájl új bérlők hoz létre egyedi bérlői kulcsok a szilánkos több-bérlős adatbázison belül, és azokat a bérlő neve és helyszínére típusú inicializál. Ez a módszer az alkalmazás látja el egy új bérlőt összhangban. 

## <a name="simulate-usage-on-all-tenant-databases"></a>Az összes bérlői adatbázis használatának szimulálása

A *bemutató-PerformanceMonitoringAndManagement.ps1* parancsfájl, feltéve, hogy egy több-bérlős rajta futó munkaterhelések szimulálja. A terhelés jön létre a rendelkezésre álló terheléselosztási forgatókönyveket egyikének használatával:

| Bemutató | Forgatókönyv |
|:--|:--|
| 2 | Normál intenzitásának terhelés készítése (körülbelül 30 DTU) |
| 3 | Bérlőnként hosszabb felszakadásáig terheléssel készítése|
| 4 | Bérlőnként magasabb DTU felszakadásáig terheléssel készítése (hozzávetőleges 70 DTU)|
| 5 | Egy magas intenzitásának készítése (körülbelül 90 DTU) a többi bérlő terhelése egyetlen bérlő és a normál intenzitásának |

A terhelésgenerátor egy *szintetikus* CPU-terhelést alkalmaz az összes bérlői adatbázison. A generátor minden bérlői adatbázis számára elindít egy feladatot, amely időközönként meghív egy, a terhelést létrehozó tárolt eljárást. A terhelési szintben (a dtu-k) időtartama és intervallumok változnak, előre nem látható bérlői tevékenységeket szimulálva összes adatbázis között.

1. Az a **PowerShell ISE**, nyissa meg... \\Tanulási modulok\\Teljesítményfigyelő és felügyeleti\\*bemutató-PerformanceMonitoringAndManagement.ps1*. Tartsa ezt a szkriptet nyitva, mivel az oktatóanyag során több különböző forgatókönyvet is futtatnia kell majd.
1. Állítsa be **$DemoScenario** = **2**, _Generate normál intenzitásának betöltése_
1. Nyomja le az **F5** alkalmazni a terhelést a bérlők számára.

Wingtip jegyek SaaS több-bérlős adatbázis egy SaaS-alkalmazás, pedig egy SaaS-alkalmazás valós terhelése jellemzően szórványos és előre nem látható. Ennek szimulálására a terhelésgenerátor az összes bérlő között elosztott, véletlenszerű terhelést hoz létre. Több percig merülnek fel, a betöltés minta van szükség, ezért futtassa a terhelés generátor 3-5 percet, mielőtt megpróbálja a terhelés, az alábbi szakaszok a figyelheti.

> [!IMPORTANT]
> A betöltési generátor fut egy új PowerShell-ablakban feladatok sorozataként. Ha bezárja a munkamenetet, a betöltés generátor leáll. A betöltési generátor marad a *feladat meghívása* állapotba, ahol hoz létre a kódgenerátor végrehajtásának megkezdése után törlődnek új tenantokat terhelése. Használjon *Ctrl-C* leállítására, hívja az új feladatokat, és lépjen ki a parancsfájl. A betöltési generátor továbbra is fut, de csak a meglévő bérlők számára.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>A figyelő erőforrás-használata az Azure portál használatával

Figyelheti az erőforrás-felhasználás által alkalmazott a terhelés, nyissa meg a több-bérlős adatbázisba, a portál **tenants1**, amely tartalmazza a bérlők:

1. Nyissa meg a [Azure-portálon](https://portal.azure.com) , és keresse meg a kiszolgáló *tenants1-mt -&lt;felhasználói&gt;*.
1. Görgessen lefelé, és keresse meg az adatbázisok, és kattintson a **tenants1**. A szilánkos több-bérlős adatbázis eddig létrehozott összes bérlők tartalmazza.

![Adatbázis-diagram](./media/saas-multitenantdb-performance-monitoring/multitenantdb.png)

Figyelje meg a **DTU** diagram.

## <a name="set-performance-alerts-on-the-database"></a>Az adatbázis teljesítményével kapcsolatos riasztások beállítása

Az adatbázis, amely elindítja a riasztást állíthat be \>75 %-os kihasználtsága az alábbiak szerint:

1. Nyissa meg a *tenants1* adatbázis (a a *tenants1-mt -&lt;felhasználói&gt;*  kiszolgáló) a a [Azure-portálon](https://portal.azure.com).
1. Kattintson a **Riasztási szabályok** elemre, majd a **+ Riasztás hozzáadása** gombra:

   ![riasztás hozzáadása](media/saas-multitenantdb-performance-monitoring/add-alert.png)

1. Adjon meg egy nevet, például: **Magas DTU**.
1. Állítsa be a következő értékeket:
   * **A metrika = DTU százalékos aránya**
   * **Feltétel = nagyobb, mint**
   * **Küszöbérték = 75**.
   * **Az elmúlt 30 perc idő =**
1. E-mail cím hozzáadása a *további rendszergazda email(s)* mezőbe, majd kattintson a **OK**.

   ![riasztás beállítása](media/saas-multitenantdb-performance-monitoring/set-alert.png)

## <a name="scale-up-a-busy-database"></a>Egy foglalt adatbázis méretezése

Ha a terhelés növekszik a ponthoz, hogy az adatbázis kimenő maxes, és eléri a 100 %-os DTU-használatát egy adatbázison, majd adatbázis teljesítménye érintett, potenciálisan lelassulnak, ami lekérdezési válaszidőt.

**Rövid távú**, amennyiben további erőforrás az adatbázis vertikális felskálázásával, vagy a bérlők távolítsa el a több-bérlős adatbázisból (a több-bérlős adatbázisból történő áthelyezése egy önálló adatbázis).

**Hosszabb távon**, a lekérdezések optimalizálja, vagy index használati adatbázis teljesítményének javítása érdekében. Attól függően, hogy az alkalmazás érzékenységi teljesítmény állít ki az ajánlott eljárás egy adatbázis növelheti előtt 100 %-os DTU-használatát. Használjon olyan riasztást, amely előre figyelmezteti Önt.

A terhelés a készítő által előállított növelésével szimulálhatja foglalt adatbázis. Ez a bérlők számára kapacitásnövelés gyakrabban, valamint a továbbiakban, a több-bérlős adatbázis terhelését növelése az egyes bérlők követelményeinek módosítása nélkül. Az adatbázis vertikális felskálázásával könnyen történik, a portál vagy a PowerShell. A gyakorlat során a Portalt használjuk.

1. Állítsa be *$DemoScenario* = **3**, _hosszabb és gyakoribb felszakadásáig adatbázisonként Generate terheléssel_ összesített terhelést intenzitásának növelik a az adatbázis az egyes bérlők által igényelt csúcsterhelés módosítása nélkül.
1. Nyomja le az **F5** billentyűt, hogy az összes bérlői adatbázist érje terhelés.
1. Lépjen a **tenants1** adatbázis az Azure portálon.

Figyeli a megnövekedett adatbázis DTU-használatát a felső diagramra. Az új magasabb terhelés, indítsa néhány percet vesz igénybe, de gyorsan megtekintheti az az adatbázis elérte a maximális használat megkezdése, és a betöltés steadies be az új mintát, mivel gyorsan tömbparaméter-típust használ az adatbázis.

1. Az adatbázis növelheti, kattintson a **tarifacsomag (skála dtu-k)** a beállítások panelen.
1. Módosítsa a **DTU** beállítást **100**. 
1. Kattintson a **alkalmaz** az adatbázis méretezése a kérelmet.

Lépjen vissza a **tenants1** > **áttekintése** a figyelési diagramok megtekintéséhez. Figyeli a hatását, hogy az adatbázis biztosítson több erőforrást (bár néhány bérlők és a véletlenszerű betöltése nem mindig könnyen kétséget látható, amíg nem futtat egy kis ideig). Amíg nézi, szem előtt a diagramokon szerepel a 100 %-os a felső diagram most pedig 100 dtu-k, a pedig a kisebb diagram 100 %-os továbbra is 50 dtu-inak száma.

Az adatbázisok a folyamat során végig online állapotban maradnak, és teljes mértékben rendelkezésre állnak. Alkalmazáskód mindig írható, majd ismételje meg a kapcsolatok megszakadnak, és úgy fog újra az adatbázishoz.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Egy új bérlőt a saját adatbázis kiépítése 

A szilánkos több-bérlős modell lehetővé teszi annak meghatározását, hogy egy több-bérlős adatbázis mellett más bérlők egy új bérlő kiépítésének, vagy saját adatbázisban a bérlő kiépítésének. Által használt dinamikus kiosztásnál a bérlők a saját adatbázis előnyeivel szerves része a másik adatbázist, így mások függetlenül bérlőre teljesítményének kezeléséhez, állítsa vissza a bérlőre, függetlenül a elkülönítési stb. Például előfordulhat, hogy válasszon egy több-bérlős adatbázisban ingyenes vagy rendszeres ügyfeleket és a prémium szintű ügyfelek be az egyes adatbázisokat.  Elkülönített bérlői egyetlen adatbázisok jönnek létre, ha ezek továbbra is kezelhetők együttesen egy rugalmas készletben erőforrás költségek optimalizálása érdekében.

Ha már megtörtént egy új bérlőt a saját adatbázisában, ugorjon a következő néhány lépést.

1. Az a **PowerShell ISE**, nyissa meg... \\Tanulási modulok\\ProvisionTenants\\*bemutató-ProvisionTenants.ps1*. 
1. Módosítsa **$TenantName = "Fűzfa milyen"** és **$VenueType = "dance"**
1. Állítsa be **$Scenario** = **2**, _kiépíteni a bérlő az egyetlen-bérlő adatbázis_
1. A szkriptek futtatásához nyomja le az **F5** billentyűt.

A parancsfájl kiépíteni a bérlő külön adatbázisban, regisztrálja az adatbázis és a bérlői a katalógusban, és a bérlői események lapot a böngészőben nyissa meg. Frissítse az események Hub oldalt, és látni fogja, mint egy helyszínére "Fűzfa milyen" hozzá lett adva.

## <a name="manage-performance-of-a-single-database"></a>Egy önálló adatbázis teljesítményének kezelése

Ha egy több-bérlős adatbázisban lévő egyetlen bérlő a tartós nagy terhelés, akkor előfordulhat, hogy az egyes dominálja az adatbázis-erőforrásokat, és hatással lehet más bérlők ugyanabban az adatbázisban. Ha a tevékenység valószínű, hogy továbbra is egy kis ideig, a bérlő ideiglenesen áthelyezhetők a adatbázisból, és a saját single-bérlő adatbázisba. Ez lehetővé teszi, hogy a bérlő számára a további erőforrások van szüksége, és teljesen elkülöníti a a többi bérlőtől.

Ebben a gyakorlatban a nagy terhelés tapasztal, amikor jegyek keresse fel a népszerű esemény pénztári fűzfa milyen hatása szimulálja.

1. Nyissa meg a... \\ *Bemutató-PerformanceMonitoringAndManagement.ps1* parancsfájl.
1. Állítsa be **$DemoScenario = 5**, _szokásos terhelés és a nagy terhelés egyetlen bérlő létrehozása (körülbelül 90 DTU)._
1. Állítsa be **$SingleTenantName fűzfa milyen =**
1. Futtassa a szkriptet az **F5** billentyűvel.

Navigáljon a portálon, és keresse meg **salixsalsa** > **áttekintése** a figyelési diagramok megtekintéséhez. 

## <a name="other-performance-management-patterns"></a>Más teljesítmény felügyeleti minták

**Bérlői önkiszolgáló skálázás**

Mert skálázás feladat könnyen az API management szolgáltatáson keresztül nevű könnyen létrehozhatja a bérlő számára is elérhető alkalmazásba a bérlő adatbázisok szolgáltatás, és a Szolgáltatottszoftver-szolgáltatásban szolgáltatásként ajánlja. Például a bérlők saját maguk adminisztrálhatják a vertikális fel- és leskálázást, ami előfordulhat, hogy közvetlen kapcsolatban áll a számlázásukkal.

**Egy adatbázis felfelé és lefelé skálázás használati minták megfelelő ütemezés szerint**

Összesített bérlők követi, kiszámítható használati minták, ahol használhatja Azure Automation adatbázis felfelé és lefelé méretezési ütemezés szerint. Például csökkentheti adatbázis 18: 00 után, és nincs erőforrásigények csökkenését létrehozását, ha ismeri a reggel 6 óra, mielőtt újra fel.

## <a name="next-steps"></a>További lépések

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * A megadott terheléselosztási generátor futtatásával szilánkos több-bérlős adatbázis-használata szimulálása
> * Az adatbázis figyelésére, reagáljon a megnövekedett terhelés
> * A nagyobb adatbázis-terhelésnek válaszul adatbázis méretezése
> * A bérlő kiépíteni egy bérlői adatbázis

## <a name="additional-resources"></a>További források

<!--* [Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Azure Automation](../automation/automation-intro.md)