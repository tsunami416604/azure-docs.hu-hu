---
title: Séma kezelése több-bérlős alkalmazásokban
description: Több bérlő sémájának kezelése Azure SQL Database-t használó több-bérlős alkalmazásban
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, sstein
ms.date: 12/18/2018
ms.openlocfilehash: 6f660426c41b37dd27438c28cbf603bdbf1e58b3
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79269197"
---
# <a name="manage-schema-in-a-saas-application-that-uses-sharded-multi-tenant-sql-databases"></a>Séma kezelése egy több-bérlős SQL-adatbázist használó SaaS-alkalmazásban

Ez az oktatóanyag azt vizsgálja, hogy milyen kihívásokat kell fenntartani a szoftveres (SaaS-) alkalmazásban található adatbázisok flottájának fenntartásához. A megoldások bemutatják, hogy Fanning a séma változásait az adatbázisok flottáján belül.

Mint bármely alkalmazás, a Wingtip jegyek SaaS-alkalmazás az idő múlásával fejlődik, és szükség lesz az adatbázis módosítására. A változtatások befolyásolhatják a séma vagy a hivatkozási adatok hatását, vagy alkalmazhatják az adatbázis-karbantartási feladatokat. Ha egy SaaS-alkalmazás a bérlői minta alapján egy adatbázist használ, a módosításokat össze kell hangolni a bérlői adatbázisok potenciálisan nagy flottáján. Emellett ezeket a módosításokat be kell építenie az adatbázis-létesítési folyamatba, hogy az új adatbázisokban is szerepeljenek a létrehozásuk során.

#### <a name="two-scenarios"></a>Két forgatókönyv

Ez az oktatóanyag a következő két forgatókönyvet vizsgálja:
- Az összes bérlőre vonatkozó hivatkozási adatfrissítések üzembe helyezése.
- Hozzon létre egy indexet a táblázatban, amely tartalmazza a hivatkozási adatkészletet.

A Azure SQL Database [rugalmas feladatok](elastic-jobs-overview.md) funkciójának használatával végrehajthatja ezeket a műveleteket a bérlői adatbázisok között. A feladatok a sablon bérlői adatbázisán is működnek. Az Wingtip tickets minta alkalmazásban ez a sablon-adatbázis egy új bérlői adatbázis kiépítésére lett másolva.

Ennek az oktatóanyagnak a segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Hozzon létre egy feladatot tartalmazó ügynököt.
> * T-SQL-lekérdezés végrehajtása több bérlős adatbázison.
> * Frissítse a hivatkozási adatforrásokat az összes bérlői adatbázisban.
> * Index létrehozása egy táblához az összes bérlői adatbázisban.

## <a name="prerequisites"></a>Előfeltételek

- A Wingtip jegyek több-bérlős adatbázis-alkalmazást már üzembe kell helyezni:
    - Útmutatásért tekintse meg az első oktatóanyagot, amely bemutatja a Wingtip tickets SaaS több-bérlős adatbázis-alkalmazást:<br />[Helyezzen üzembe és vizsgáljon meg egy több-bérlős, Azure SQL Database-t használó több-bérlős alkalmazást](saas-multitenantdb-get-started-deploy.md).
        - Az üzembe helyezési folyamat öt percnél kevesebb ideig fut.
    - A Wingtip *több-bérlős, több-bérlős* verzióját kell telepíteni. Az *önálló* és az *adatbázis-bérlői* verziók nem támogatják ezt az oktatóanyagot.

- Telepíteni kell a SQL Server Management Studio (SSMS) legújabb verzióját. [Töltse le és telepítse a SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

- Azure PowerShell kell telepíteni. Részletekért lásd: [a Azure PowerShell első lépései](https://docs.microsoft.com/powershell/azure/get-started-azureps).

> [!NOTE]
> Ez az oktatóanyag egy korlátozott előzetes verzióban ([rugalmas adatbázis-feladatok](sql-database-elastic-database-client-library.md)) található Azure SQL Database szolgáltatás funkcióit használja. Ha ezt az oktatóanyagot szeretné elvégezni, adja meg az előfizetés-AZONOSÍTÓját a *SaaSFeedback\@Microsoft.com* a subject = rugalmas feladatok előzetes verziójával. Miután megkapta a megerősítést az előfizetésének engedélyezéséről, [töltse le és telepítse a legújabb kiadás előtti feladatok parancsmagjait](https://github.com/jaredmoo/azure-powershell/releases). Ez az előzetes verzió korlátozott, ezért forduljon a *SaaSFeedback\@Microsoft.com* kapcsolódó kérdésekhez vagy támogatáshoz.

## <a name="introduction-to-saas-schema-management-patterns"></a>Az SaaS-séma felügyeleti mintáinak bemutatása

A mintában használt többbérlős adatbázis-modell lehetővé teszi, hogy egy bérlői adatbázis egy vagy több bérlőt tartalmazzon. Ez a példa feltárja a több-bérlős és egy bérlős adatbázisok kombinációjának használatát, amely lehetővé teszi a *hibrid* bérlői felügyeleti modell használatát. Az adatbázisok változásainak kezelése bonyolult lehet. A [rugalmas feladatok](elastic-jobs-overview.md) megkönnyítik a nagy számú adatbázis felügyeletét és kezelését. A feladatok segítségével biztonságosan és megbízhatóan futtathat Transact-SQL-parancsfájlokat a bérlői adatbázisok egy csoportján. A feladatok függetlenek a felhasználói interakciótól vagy bemenettől. Ezzel a módszerrel a séma módosításait vagy a közös referenciákat az alkalmazás összes bérlője számára is telepítheti. A rugalmas feladatok is használhatók az adatbázis arany sablonos példányának karbantartásához. A sablon új bérlők létrehozására szolgál, és mindig gondoskodik a legújabb séma-és hivatkozási adatszolgáltatások használatáról.

![képernyő](media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>Az Elastic Jobs korlátozott előzetes verziója

A rugalmas feladatok új verziója már a Azure SQL Database integrált szolgáltatása. Ez Elastic Jobs-nak ez az új verziója jelenleg korlátozott előzetes verzió. A korlátozott előzetes verzió jelenleg a PowerShell használatával teszi lehetővé a feladatok ügynökének létrehozását és a T-SQL-T a feladatok létrehozásához és kezeléséhez.
> [!NOTE]
> Ez az oktatóanyag egy korlátozott előzetes verzióban (rugalmas adatbázis-feladatok) található SQL Database szolgáltatás funkcióit használja. Ha ezt az oktatóanyagot szeretné elvégezni, adja meg az előfizetési azonosítót SaaSFeedback@microsoft.com tulajdonos = rugalmas feladatok előzetes verziójával. Miután megkapta a megerősítést, hogy az előfizetése engedélyezve lett, töltse le és telepítse a legújabb kiadás előtti feladatok parancsmagokat. Ez az előzetes verzió korlátozott, ezért lépjen kapcsolatba SaaSFeedback@microsoft.com a kapcsolódó kérdésekkel vagy támogatással kapcsolatban.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>A Wingtip tickets SaaS több-bérlős adatbázis-alkalmazás forráskódjának és parancsfájljainak beszerzése

A Wingtip jegyek SaaS több-bérlős adatbázis-szkriptek és az alkalmazás forráskódja elérhető a GitHub [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) adattárában. A Wingtip tickets SaaS-parancsfájlok letöltéséhez és feloldásához szükséges lépéseket lásd: [általános útmutató](saas-tenancy-wingtip-app-guidance-tips.md) .

## <a name="create-a-job-agent-database-and-new-job-agent"></a>A Job Agent-adatbázis és az új feladatkártya létrehozása

Ez az oktatóanyag megköveteli, hogy a PowerShell-lel hozza létre a Job Agent-adatbázist és a feladatot ügynökét. Az SQL Agent által használt MSDB-adatbázishoz hasonlóan a feladatok ügynöke egy Azure SQL Database-adatbázist használ a feladatdefiníciók, a feladatok állapotának és a korábbi előzményeknek a tárolására. A feladat-ügynök létrehozása után azonnal létrehozhatja és figyelheti a feladatokat.

1. A **POWERSHELL ISE**-ben nyissa meg a *...\\learning-modulokat\\Schema Management\\demo-SchemaManagement. ps1*.
2. A szkriptek futtatásához nyomja le az **F5** billentyűt.

A *demo-SchemaManagement. ps1* parancsfájl meghívja a *Deploy-SchemaManagement. ps1* parancsfájlt, hogy létrehozzon egy _jobagent_ nevű adatbázist a katalógus-kiszolgálón. A szkript ezután létrehozza a feladatot, és paraméterként átadja a _jobagent_ -adatbázist.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Feladat létrehozása új referenciaadatok bevezetéséhez az összes bérlőn

#### <a name="prepare"></a>Előkészítés

Az egyes bérlői adatbázisok a **VenueTypes** táblában tartalmazzák a helyszín típusait. Minden egyes helyszín típusa határozza meg, hogy milyen típusú eseményeket lehet üzemeltetni a helyszínen. Ezek a helyszín típusok a bérlői események alkalmazásban megjelenő háttérképnek felelnek meg.  Ebben a gyakorlatban egy frissítést telepít az összes adatbázisra, hogy két további típusú helyszínt adjon hozzá: *motorkerékpár-verseny* és az *úszó Club*.

Először tekintse át az egyes bérlői adatbázisokban található helyszín típusait. Kapcsolódjon SQL Server Management Studio (SSMS) egyik bérlői adatbázisához, és vizsgálja meg a VenueTypes táblát.  Ezt a táblázatot a Azure Portal lekérdezés-szerkesztőjében is lekérdezheti, amely az adatbázis lapról érhető el.

1. Nyissa meg a SSMS, és kapcsolódjon a bérlői kiszolgálóhoz: *tenants1-DPT-&lt;user&gt;. database.Windows.net*
1. Annak ellenőrzéséhez, hogy a *motorkerékpár-verseny* és **az** *úszás Club* jelenleg nem szerepel, keresse meg a *contosoconcerthall* -adatbázist a *tenants1-DPT-&lt;felhasználói&gt;* kiszolgálón, és kérdezze le a *VenueTypes* táblát.



#### <a name="steps"></a>Lépések

Most hozzon létre egy feladatot a **VenueTypes** tábla minden bérlői adatbázisban való frissítéséhez a két új helyszín típusának hozzáadásával.

Új feladat létrehozásához használja a _jobagent_ -adatbázisban létrehozott feladatok rendszer tárolt eljárásait. A rendszer a feladatok ügynökének létrehozásakor hozta létre a tárolt eljárásokat.

1. A SSMS-ben kapcsolódjon a bérlői kiszolgálóhoz: tenants1-MT-&lt;User&gt;. database.windows.net

2. Keresse meg a *tenants1* -adatbázist.

3. A *VenueTypes* tábla lekérdezése annak megerősítéséhez, hogy a *motorkerékpár-verseny* és az *úszó klub* még nem szerepel az eredmények listáján.

4. Kapcsolódjon a Catalog-kiszolgálóhoz, amely a *Catalog-MT-&lt;felhasználó&gt;. database.Windows.net*.

5. Kapcsolódjon a _jobagent_ -adatbázishoz a katalógus-kiszolgálón.

6. A SSMS-ben nyissa meg a fájlt *...\\learning-modulok\\Schema Management\\DeployReferenceData. SQL*.

7. Módosítsa az utasítást: állítsa be @User = &lt;felhasználói&gt; és cserélje le a Wingtip tickets SaaS több-bérlős adatbázis-alkalmazás üzembe helyezésekor használt felhasználói értéket.

8. A szkriptek futtatásához nyomja le az **F5** billentyűt.

#### <a name="observe"></a>Figyelje meg

Figyelje meg a következő elemeket a *DeployReferenceData. SQL* parancsfájlban:

- **sp\_hozzáadás\_cél\_csoport** hozza létre a célcsoport nevét, és hozzáadja a *DemoServerGroup*a csoporthoz.

- **sp\_\_cél\_csoport hozzáadása\_tag** hozzáadja a következő elemeket:
    - Egy *kiszolgáló* -megcélzott tag típusa.
        - Ez a *tenants1-MT-&lt;user&gt;* Server, amely tartalmazza a bérlői adatbázisokat.
        - A kiszolgáló belefoglalása tartalmazza a bérlői adatbázisokat is, amelyek a feladatok végrehajtásának időpontjában léteznek.
    - A (z)*basetenantdb*( *katalógus – MT –&lt;felhasználói&gt;* kiszolgálón található) *adatbázis* -célcsoport típusa
    - Egy *adatbázis* -megcélzott tag típusa, amely tartalmazza a *adhocreporting* -adatbázist, amelyet egy későbbi oktatóanyagban használtak.

- az **sp\_add\_Job** létrehoz egy *hivatkozási adatok központi telepítés*nevű feladatot.

- **sp\_add\_jobstep** létrehozza a t-SQL-parancs szövegét tartalmazó feladatot, amely frissíti a VenueTypes.

- A szkript fennmaradó nézetei megjelenítik, hogy léteznek-e az objektumok, és figyelik a feladat-végrehajtást. Ezekkel a lekérdezésekkel áttekintheti az állapot értékét az **életciklus** oszlopban annak megállapításához, hogy a feladatok befejeződtek-e. A feladatok frissítik a bérlői adatbázist, és frissítik a hivatkozási táblázatot tartalmazó két további adatbázist.

A SSMS-ben keresse meg a bérlői adatbázist a *tenants1-MT-&lt;felhasználói&gt;* kiszolgálón. A *VenueTypes* tábla lekérdezésével erősítse meg, hogy a *motorkerékpár-verseny* és az *úszó klub* már hozzá van adva a táblához. A helyszínek típusának teljes számát kettővel kell növelni.

## <a name="create-a-job-to-manage-the-reference-table-index"></a>Feladat létrehozása a referenciatábla indexének kezeléséhez

Ezzel a gyakorlattal létrejön egy feladat, amely az indexet újraépíti a hivatkozási tábla elsődleges kulcsához az összes bérlői adatbázison. Az indexek újraépítése egy tipikus adatbázis-kezelési művelet, amelyet a rendszergazda a nagy mennyiségű adat betöltését követően futtathat a teljesítmény javítása érdekében.

1. A SSMS-ben kapcsolódjon a _jobagent_ -adatbázishoz a *Catalog-MT-&lt;felhasználói&gt;. database.Windows.net-* kiszolgálón.

2. A SSMS-ben nyissa meg a *...\\learning-modulokat\\Schema Management\\Sémakezelés. SQL*.

3. A szkriptek futtatásához nyomja le az **F5** billentyűt.

#### <a name="observe"></a>Figyelje meg

Figyelje meg a következő elemeket a *Sémakezelés. SQL* parancsfájlban:

* az **sp\_add\_Job** nevű új feladatot hoz létre az *online reindex PK\_\_VENUETYP\_\_265E44FD7FD4C885*.

* az **sp\_add\_jobstep** létrehozza a t-SQL-parancs szövegét tartalmazó feladatot, amely frissíti az indexet.

* A parancsfájl-figyelő feladatok végrehajtásának hátralévő nézetei. Ezekkel a lekérdezésekkel áttekintheti az állapot értékét az **életciklus** oszlopban annak megállapításához, hogy a feladatok sikeresen befejeződtek-e az összes célcsoport-tagon.

## <a name="additional-resources"></a>További források

<!-- TODO: Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->
* [Kiterjesztett felhőalapú adatbázisok kezelése](elastic-jobs-overview.md)

## <a name="next-steps"></a>Következő lépések

Ennek az oktatóanyagnak a segítségével megtanulta a következőket:

> [!div class="checklist"]
> * Feladat-ügynök létrehozása a T-SQL-feladatok több adatbázison való futtatásához
> * Az összes bérlői adatbázisban lévő hivatkozási érték frissítése
> * Index létrehozása a táblához az összes bérlői adatbázisban

Ezután próbálja ki az [ad hoc jelentéskészítési oktatóanyagot](saas-multitenantdb-adhoc-reporting.md) , és vizsgálja meg a bérlői adatbázisok közötti elosztott lekérdezések futtatását.

