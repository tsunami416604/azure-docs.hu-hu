---
title: Séma kezelése több-bérlős alkalmazásban
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269197"
---
# <a name="manage-schema-in-a-saas-application-that-uses-sharded-multi-tenant-sql-databases"></a>Séma kezelése egy saas-alkalmazásokban, amely szilánkos több-bérlős SQL-adatbázisokat használ

Ez az oktatóanyag a Szoftver szolgáltatásként (SaaS) alkalmazások adatbázis-flottájának karbantartásával kapcsolatos kihívásokat vizsgálja. Megoldások at bizonyított an legyező ki séma változások az egész flotta adatbázisok.

Mint minden alkalmazás, a Wingtip Jegyek SaaS alkalmazás idővel fejlődik, és az adatbázis módosítására lesz szükség. A módosítások hatással lehetnek a sémára vagy a referenciaadatokra, vagy adatbázis-karbantartási feladatokat alkalmazhatnak. Egy SaaS-alkalmazás bérlői mintánként egy adatbázis használatával, a módosításokat össze kell hangolni egy potenciálisan hatalmas flotta bérlői adatbázisok között. Ezenkívül ezeket a módosításokat be kell építenie az adatbázis-létesítési folyamatba annak érdekében, hogy azok létrehozásukkor bekerüljenek az új adatbázisokba.

#### <a name="two-scenarios"></a>Két forgatókönyv

Ez az oktatóanyag a következő két forgatókönyvet vizsgálja:
- Referencia-adatfrissítések üzembe helyezése az összes bérlő számára.
- Építse újra a táblázatban a referenciaadatokat tartalmazó indexet.

Az Azure SQL Database [rugalmas feladatok](elastic-jobs-overview.md) szolgáltatás a bérlői adatbázisok közötti műveletek végrehajtásához használható. A feladatok is működnek a "sablon" bérlői adatbázis. A Wingtip jegyek mintaalkalmazásban ez a sablonadatbázis egy új bérlői adatbázis kiépítéséhez kerül.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Feladatügynök létrehozása.
> * T-SQL-lekérdezés végrehajtása több bérlői adatbázison.
> * A referenciaadatok frissítése az összes bérlői adatbázisban.
> * Hozzon létre egy indexet egy táblán az összes bérlői adatbázisban.

## <a name="prerequisites"></a>Előfeltételek

- A Wingtip Jegyek több-bérlős adatbázis-alkalmazást már telepíteni kell:
    - További információt az első oktatóanyagban talál, amely bemutatja a Wingtip Tickets SaaS több-bérlős adatbázis-alkalmazást:<br />[Az Azure SQL Database szolgáltatást használó, több-bérlős szilánkos alkalmazást helyezhet üzembe és vizsgálja meg.](saas-multitenantdb-get-started-deploy.md)
        - A központi telepítés folyamata kevesebb, mint öt percig fut.
    - A Wingtip *szilánkos több-bérlős* verziójának telepítve kell lennie. *Az önálló* és *az adatbázis bérlőnkénti* verziói nem támogatják ezt az oktatóanyagot.

- Az SQL Server Management Studio (SSMS) legújabb verzióját telepíteni kell. [Töltse le és telepítse az SSMS-t.](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

- Az Azure PowerShellt telepíteni kell. További információt az [Azure PowerShell – első lépések.](https://docs.microsoft.com/powershell/azure/get-started-azureps)

> [!NOTE]
> Ez az oktatóanyag az Azure SQL Database szolgáltatás korlátozott előzetes verziójú ([rugalmas adatbázis-feladatok)](sql-database-elastic-database-client-library.md)szolgáltatásait használja. Ha szeretné ezt az oktatóanyagot, adja meg az előfizetési azonosítót *a\@SaaSFeedback microsoft.com* a subject=Elastic Jobs Preview segítségével. Miután megkapta a megerősítést az előfizetésének engedélyezéséről, [töltse le és telepítse a legújabb kiadás előtti feladatok parancsmagjait](https://github.com/jaredmoo/azure-powershell/releases). Ez az előzetes verzió korlátozott, ezért forduljon a *\@SaaSFeedback microsoft.com* kapcsolódó kérdésekért vagy támogatásért.

## <a name="introduction-to-saas-schema-management-patterns"></a>Bevezetés a SaaS-sémakezelési minták ba

A mintában használt szilánkos több-bérlős adatbázismodell lehetővé teszi, hogy a bérlők adatbázisa egy vagy több bérlőt tartalmazzon. Ez a minta feltárja a több-bérlős és egy-bérlős adatbázisok keverékének használatával való lehetséges, amely lehetővé teszi a *hibrid* bérlői felügyeleti modell használatát. Az adatbázisok módosításai bonyolultak lehetnek. [Rugalmas jobs](elastic-jobs-overview.md) megkönnyíti felügyelete és kezelése nagy számú adatbázis. A feladatok lehetővé teszik a Transact-SQL parancsfájlok feladatként való biztonságos és megbízható futtatását bérlői adatbázisok egy csoportja ellen. A feladatok függetlenek a felhasználói beavatkozástól vagy a beviteltől. Ez a módszer a séma vagy a közös referenciaadatok módosításainak üzembe helyezésére használható az alkalmazás összes bérlője számára. Rugalmas feladatok is használható az adatbázis aranysablon-példányának karbantartásához. A sablon új bérlők létrehozására szolgál, mindig biztosítva a legújabb séma és referenciaadatok használatban van.

![képernyő](media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>Az Elastic Jobs korlátozott előzetes verziója

Van egy új verziója rugalmas feladatok, amely most egy integrált funkció az Azure SQL Database. Ez Elastic Jobs-nak ez az új verziója jelenleg korlátozott előzetes verzió. A korlátozott előzetes verzió jelenleg támogatja a PowerShell használatával egy feladat-ügynök létrehozásához, és a T-SQL feladatok létrehozásához és kezeléséhez.
> [!NOTE]
> Ez az oktatóanyag az SQL Database szolgáltatás egy korlátozott előzetes verziójának funkcióit használja (Elastic Database-feladatok). Ha szeretné ezt az oktatóanyagot, adja SaaSFeedback@microsoft.com meg az előfizetés-azonosítóját a subject=Elastic Jobs Preview használatával. Miután megkapta a megerősítést az előfizetésének engedélyezéséről, töltse le és telepítse a legújabb kiadás előtti feladatok parancsmagjait. Ez az előnézet korlátozott, ezért forduljon SaaSFeedback@microsoft.com a kapcsolódó kérdésekhez vagy támogatáshoz.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>A Wingtip jegyek SaaS több-bérlős adatbázis-alkalmazás forráskódjának és parancsfájljainak beszereznie

A Wingtip jegyek SaaS több-bérlős adatbázis-parancsfájlok és az alkalmazás forráskód érhető el a [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) tárházban a GitHubon. Tekintse meg az [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) a Wingtip Tickets SaaS-parancsfájlok letöltéséhez és feloldásához.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Feladatügynök-adatbázis és új feladatügynök létrehozása

Ez az oktatóanyag megköveteli, hogy a PowerShell használatával hozza létre a feladatügynök-adatbázis és a feladatügynök. Az SQL Agent által használt MSDB-adatbázishoz hasonlóan egy feladatügynök is egy Azure SQL-adatbázist használ a feladatdefiníciók, a feladatállapot és az előzmények tárolására. A feladatügynök létrehozása után azonnal létrehozhat és figyelhet feladatokat.

1. A **PowerShell ISE**, *nyissa ... Tanulási modulok\\sémakezelése\\dema-SchemaManagement.ps1 . \\*
2. A szkript futtatásához nyomja le az **F5** billentyűt.

A *Demo-SchemaManagement.ps1* parancsfájl meghívja a *Deploy-SchemaManagement.ps1* parancsfájlt, hogy hozzon létre egy _jobagent_ nevű adatbázist a katalóguskiszolgálón. A parancsfájl ezután létrehozza a _jobagent_ feladatügynököt, és paraméterként adja át a jobagent-adatbázist.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Feladat létrehozása új referenciaadatok bevezetéséhez az összes bérlőn

#### <a name="prepare"></a>Előkészítés

Minden bérlő adatbázisa tartalmazza a **VenueTypes** táblában a helyszíntípusok készletét. Minden helyszíntípus határozza meg, hogy milyen típusú eseményeket lehet üzemeltetni egy helyszínen. Ezek a helyszíntípusok megfelelnek a bérlői események alkalmazásban látható háttérképeknek.  Ebben a gyakorlatban az összes adatbázis frissítését két további helyszíntípus hozzáadásához: *Motorkerékpár-verseny* és *úszóklub*.

Először tekintse át az egyes bérlői adatbázisokban szereplő helyszíntípusokat. Csatlakozzon az SQL Server Management Studio (SSMS) egyik bérlői adatbázisához, és vizsgálja meg a VenueTypes táblát.  Ezt a táblát az Azure Portal lekérdezésszerkesztőjében is lekérdezheti, amely az adatbázislapról érhető el.

1. Nyissa meg az SSMS-t, és csatlakozzon a bérlői kiszolgálóhoz: *tenants1-dpt-&lt;user&gt;.database.windows.net*
1. Annak ellenőrzéséhez, hogy *a Motorkerékpár-verseny-* és *Úszóklub* jelenleg **nem** szerepel a csomagban, keresse meg a *contosoconcerthall* adatbázist a *&lt;tenants1-dpt- felhasználói&gt; * kiszolgálón, és kérdezze le a *VenueTypes* táblát.



#### <a name="steps"></a>Lépések

Most hozzon létre egy feladatot a **VenueTypes** tábla frissítésére minden bérlői adatbázisban, a két új helyszíntípus hozzáadásával.

Új feladat létrehozásához a _jobagent_ adatbázisban létrehozott feladatok rendszer tárolt eljárásait használja. A tárolt eljárások a feladatügynök létrehozásakor jöttek létre.

1. Az SSMS-ben csatlakozzon a bérlői kiszolgálóhoz: tenants1-mt-&lt;user&gt;.database.windows.net

2. Tallózással keresse meg a *bérlők1 adatbázist.*

3. A *VenueTypes* tábla lekérdezése annak megerősítéséhez, hogy *a Motorkerékpár-verseny-* és *Úszóklub* még nem szerepel az eredménylistában.

4. Csatlakozzon a katalóguskiszolgálóhoz, amely *katalógus-mt-&lt;&gt;felhasználó .database.windows.net*.

5. Csatlakozás a _jobagent_ katalóguskiszolgáló munkaügynök-adatbázisához.

6. Az SSMS-ben nyissa meg a fájlt *... Tanulási modulok\\sémakezelése\\DeployReferenceData.sql . \\*

7. Módosítsa a kimutatást: set @User = &lt;user,&gt; és helyettesítse a Wingtip Tickets SaaS több-bérlős adatbázis-alkalmazás telepítésekor használt felhasználói értéket.

8. A szkript futtatásához nyomja le az **F5** billentyűt.

#### <a name="observe"></a>Megfigyelni

Figyelje meg a következő elemeket a *DeployReferenceData.sql* parancsfájlban:

- **Sp\_\_add\_target group** létrehozza a célcsoport neve *DemoServerGroup*, és hozzáadja a céltagok a csoporthoz.

- **sp\_\_add\_\_target group member** adds the following items:
    - *Kiszolgálói* céltag típusa.
        - Ez a *bérlők1&lt;mt-felhasználói&gt; * kiszolgáló, amely tartalmazza a bérlők adatbázisait.
        - Beleértve a kiszolgálótartalmazza a bérlői adatbázisokat, amelyek a feladat végrehajtásakor léteznek.
    - A *&lt;&gt; katalógus-mt-felhasználói* kiszolgálón található sablonadatbázis *(basetenantdb)* *adatbázis-céltag* típusa,
    - Egy *database* adatbázis-céltag-típus, amely egy későbbi oktatóanyagban használt *adhocreporting* adatbázist tartalmazza.

- **sp\_\_add job** létrehoz egy feladatot nevű *referencia adatok telepítése*.

- **Sp\_\_add jobstep** létrehozza a feladat lépés, amely tartalmazza a T-SQL parancs szövegét, hogy frissítse a referenciatábla, VenueTypes.

- A szkript fennmaradó nézetei megjelenítik, hogy léteznek-e az objektumok, és figyelik a feladat-végrehajtást. Ezekkel a lekérdezésekkel tekintse át az **életciklus-oszlop** állapotértékét, és állapítsa meg, hogy a feladat mikor fejeződött be. A feladat frissíti a bérlői adatbázist, és frissíti a két további adatbázist, amely tartalmazza a referenciatáblát.

Az SSMS-ben keresse meg a *bérlői adatbázist&lt;&gt; a bérlők1-mt-felhasználói* kiszolgálón. A *VenueTypes* tábla lekérdezése annak megerősítéséhez, hogy *a Motorkerékpár-verseny-* és *Úszóklub* most már hozzá van adva a táblához. A helyszíntípusok teljes számát kettővel kellett volna növelni.

## <a name="create-a-job-to-manage-the-reference-table-index"></a>Feladat létrehozása a referenciatábla indexének kezeléséhez

Ez a gyakorlat létrehoz egy feladatot a referenciatábla elsődleges kulcsának indexének újraépítéséhez az összes bérlői adatbázison. Az index-újraépítés egy tipikus adatbázis-kezelési művelet, amelyet a rendszergazda nagy mennyiségű adatbetöltés betöltése után futtathat a teljesítmény javítása érdekében.

1. Az SSMS-ben csatlakozzon _a jobagent_ adatbázishoz a *&lt;katalógus-mt- User&gt;.database.windows.net* kiszolgálón.

2. Az SSMS, nyitott *... Tanulási modulok\\sémakezelése\\OnlineReindex.sql . \\*

3. A szkript futtatásához nyomja le az **F5** billentyűt.

#### <a name="observe"></a>Megfigyelni

Figyelje meg az *OnlineReindex.sql* parancsfájl következő elemeit:

* **sp\_\_add munkát** hoz létre egy új feladatot hívott *Online Reindex PK\_\_\_\_VenueTyp 265E44FD7FD4C885*.

* **Sp\_\_add jobstep** létrehozza a feladat lépés, amely tartalmazza a T-SQL parancs szövegét, hogy frissítse az indexet.

* A parancsfájl fennmaradó nézetei figyelik a feladat végrehajtását. Ezekkel a lekérdezésekkel áttekintheti az **életciklus-oszlop** állapotértékét, és megállapíthatja, hogy a feladat sikeresen befejeződött-e az összes célcsoport-tagon.

## <a name="additional-resources"></a>További források

<!-- TODO: Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->
* [Kiterjesztett felhőalapú adatbázisok kezelése](elastic-jobs-overview.md)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Feladatügynök létrehozása a T-SQL-feladatok több adatbázisban való futtatásához
> * Referenciaadatok frissítése az összes bérlői adatbázisban
> * Index létrehozása a táblához az összes bérlői adatbázisban

Ezután próbálja meg az [Ad hoc jelentési oktatóanyag](saas-multitenantdb-adhoc-reporting.md) ot az elosztott lekérdezések bérlői adatbázisok közötti futtatásának feltárásához.

