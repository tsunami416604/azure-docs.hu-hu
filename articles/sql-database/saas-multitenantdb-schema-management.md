---
title: Azure SQL Database-séma kezelése több-bérlős alkalmazásban | Microsoft Docs
description: Több bérlő sémájának kezelése Azure SQL Database-t használó több-bérlős alkalmazásban
keywords: sql database-oktatóanyag
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 01/03/2018
ms.reviewers: billgib
ms.author: genemi
ms.openlocfilehash: 816cde31e84eeda8110c042f4e0640f12fb4cc53
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34645990"
---
# <a name="manage-schema-in-a-saas-application-that-uses-sharded-multi-tenant-sql-databases"></a>Horizontálisan skálázott több-bérlős SQL-adatbázisok használó SaaS-alkalmazás a séma kezelése

Ez az oktatóanyag egy járműflotta egy szoftverfrissítési szolgáltatott szoftverként (SaaS) alkalmazás-adatbázisok karbantartása kihívás megvizsgálja. A különböző adatbázisok járműflotta kimenő sémamódosítások szellőztető egy megoldások.

Bármely alkalmazás, például a Wingtip jegyek SaaS-alkalmazás verzióinformációk lesz, és a szükséges a módosítások adatbázisba. Módosítása előfordulhat, hogy séma vagy a hivatkozás, vagy alkalmazza az adatbázis-karbantartási feladatokat. Egy SaaS-alkalmazáshoz típusú bérlői mintában adatbázist használ, a módosítások kell koordinált, egy potenciálisan nagy járműflotta bérlői adatbázisok között. Ezenkívül ezek a változások kell tartalmaznia kiépítési folyamat végrehajtásával biztosítható, hogy azok szerepelnek új adatbázisok létrehozásához szükségesek az adatbázisba.

#### <a name="two-scenarios"></a>Két esetben

Ez az oktatóanyag ismerteti, a következő két példa:
- Hivatkozás adatok frissítéseket telepítheti az összes bérlőre vonatkozó.
- A táblázat a referencia-adatokat tartalmazó index újraépítése.

A [rugalmas feladatok](sql-database-elastic-jobs-overview.md) az Azure SQL-adatbázis szolgáltatása hajthatók végre ezeket a műveleteket a bérlő az adatbázisok közötti. A feladatok is működik a "sablon" bérlői adatbázissal. A Wingtip jegyek PéldaAlkalmazás esetén ez a sablon adatbázis lehet kiépíteni egy új bérlő adatbázist a rendszer átmásolja.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Hozzon létre egy feladat ügynök.
> * T-SQL-lekérdezést végrehajtani a több bérlő adatbázis.
> * Frissíti az összes bérlői adatbázisok hivatkozási adatait.
> * Egy tábla összes bérlői adatbázisokban indexet létrehozni.

## <a name="prerequisites"></a>Előfeltételek

- A több-bérlős adatbázis alkalmazás már esetlegesen telepített Wingtip jegyek:
    - Útmutatásért lásd: első oktatóanyaga, amely bemutatja a Wingtip jegyek SaaS több-bérlős adatbázis app:<br />[Központi telepítése, és vizsgálja meg a szilánkos több-bérlős alkalmazás által használt Azure SQL Database](saas-multitenantdb-get-started-deploy.md).
        - A telepítés folyamata a kevesebb mint öt perc alatt fut.
    - Rendelkeznie kell a *szilánkos több-bérlős* Wingtip telepített verzióját. A verziók *önálló* és *bérlőnként adatbázis* nem támogatják az oktatóanyag.

- SQL Server Management Studio (SSMS) legfrissebb telepítve kell lennie. [Töltse le és telepítse az SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

- Az Azure PowerShell telepítve kell lennie. További információkért lásd: [Ismerkedés az Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

> [!NOTE]
> Ez az oktatóanyag az Azure SQL Database szolgáltatás egy korlátozott előzetes verziójú funkciókat használ ([rugalmas adatbázis-feladatok](sql-database-elastic-database-client-library.md)). Ha ez az oktatóanyag elvégzéséhez, adja meg az előfizetés-Azonosítóval történő *SaaSFeedback@microsoft.com* témát = a rugalmas feladatok megtekintése. Miután megkapta a megerősítést az előfizetésének engedélyezéséről, [töltse le és telepítse a legújabb kiadás előtti feladatok parancsmagjait](https://github.com/jaredmoo/azure-powershell/releases). Ez az előnézet korlátozva, így forduljon *SaaSFeedback@microsoft.com* kapcsolatos kérdésekre, vagy a támogatási szolgálathoz.

## <a name="introduction-to-saas-schema-management-patterns"></a>Bevezetés a Szolgáltatottszoftver-séma felügyeleti minták

Horizontálisan skálázott több-bérlős adatbázismodell a mintában használt lehetővé teszi, hogy a bérlők számára az adatbázis tartalmaz egy vagy több tenant. Ez a minta ismerteti a lehetőségeket kínál a több-bérlős és egy bérlői adatbázisok, engedélyezése vegyesen használni egy *hibrid* bérlői felügyeleti modellt. Ezek az adatbázisok módosításai kezelése bonyolult lehet. [Rugalmas feladat](sql-database-elastic-jobs-overview.md) elősegíti a felügyeleti és a nagy számú adatbázis kezelésére. Feladatok lehetővé teszik a biztonságosan és megbízhatóan Transact-SQL-parancsprogramokat futtató feladatok bérlői adatbázisok csoportja ellen. A feladatok függetlenek felhasználói interakcióktól vagy bemenettől. Ez a módszer használható központi telepítése a módosítások séma vagy közös referenciaadatok teljes egyetlen bérlő számára az alkalmazásban. Rugalmas feladat karbantartása arany sablon az adatbázis másolatát is használható. A sablon használatával hozzon létre új bérlők, mindig a legújabb séma biztosítása és referenciaadatok használatban van.

![képernyő](media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>Az Elastic Jobs korlátozott előzetes verziója

Rugalmas feladat, amelyik most már egy beépített szolgáltatás Azure SQL adatbázis új verziója van telepítve. Ez Elastic Jobs-nak ez az új verziója jelenleg korlátozott előzetes verzió. A korlátozott előzetes jelenleg egy feladat ügynököt, és T-SQL feladatok létrehozásához és kezeléséhez létrehozása a PowerShell használatával támogatja.
> [!NOTE] 
> Ez az oktatóanyag az SQL Database szolgáltatás, amely egy korlátozott előzetes verziójú (a rugalmas adatbázis-feladatok) funkcióit használja. Ha ez az oktatóanyag elvégzéséhez, adja meg az előfizetés-Azonosítóval történő SaaSFeedback@microsoft.com témát = a rugalmas feladatok megtekintése. Miután megkapta megerősítése, hogy az előfizetés engedélyezve van-e, töltse le és telepítse a legújabb kiadás előtti feladatok parancsmagokat. Ez az előnézet korlátozva, így forduljon SaaSFeedback@microsoft.com kapcsolatos kérdésekre, vagy a támogatási szolgálathoz.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Az alkalmazás forráskódjához Wingtip jegyek SaaS több-bérlős adatbázis és a parancsfájlok

A Wingtip jegyek SaaS több-bérlős adatbázis parancsfájlok és az alkalmazás forráskódjához érhetők el a [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) Github tárházából. Tekintse meg a [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) töltse le és feloldása a Wingtip jegyek Szolgáltatottszoftver-parancsfájlok lépéseit. 

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Hozzon létre egy feladat ügynök adatbázis és az új feladat ügynök

Ez az oktatóanyag a feladat ügynök adatbázis és a feladat ügynök létrehozásához PowerShell használatát igényli. Az MSDB adatbázis SQL-ügynök által használt, például egy feladat ügynök segítségével Azure SQL-adatbázis tárolja a definíciók, feladat állapota és előzményei. A feladat ügynök létrehozása után hozzon létre, és figyelheti a feladat azonnal.

1. A **PowerShell ISE**, nyissa meg *... \\Tanulási modulok\\séma felügyeleti\\bemutató-SchemaManagement.ps1*.
2. A szkriptek futtatásához nyomja le az **F5** billentyűt.

A *bemutató-SchemaManagement.ps1* parancsfájl-hívások a *telepítés-SchemaManagement.ps1* parancsfájl nevű adatbázis létrehozásához _jobagent_ a katalógus-kiszolgálón. A parancsfájl létrehozza a feladat ügynök, hogy a _jobagent_ adatbázis paraméterként.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Feladat létrehozása új referenciaadatok bevezetéséhez az összes bérlőn

#### <a name="prepare"></a>Előkészítés

Mindegyik bérlő adatbázis helyszínére típusainak készletét tartalmazza a **VenueTypes** tábla. Minden egyes helyszínére típus határozza meg, hogy milyen típusú eseményeket, amelyek a egy helyszínére lehet üzemeltetni. A bérlői események alkalmazás látható háttérképeket helyszínére típusaival felelnek meg.  Ebben a gyakorlatban frissítést telepít két további helyszínére típusok hozzáadása az összes adatbázisra: *Motorkerékpárja Racing* és *úszó Club*. 

Először tekintse át az egyes bérlői adatbázisban szereplő helyszínére típusok. Csatlakozzon egy bérlő adatbázis az SQL Server Management Studio (SSMS), és vizsgálja meg a VenueTypes tábla.  Ezt a táblázatot az Azure portálon, a Lekérdezésszerkesztő is lekérdezheti az adatbázissal kapcsolatos beállításainak lapja érhető el. 

1. Nyissa meg a szolgáltatáshoz az SSMS és a bérlői kiszolgálóhoz csatlakozni: *tenants1-dpt -&lt;felhasználói&gt;. database.windows.net*
1. Annak ellenőrzésére, hogy *Motorkerékpárja Racing* és *úszó Club* **nem** jelenleg része, keresse meg a *contosoconcerthall* az adatbázis a *tenants1-dpt -&lt;felhasználói&gt;*  kiszolgáló és a lekérdezés a *VenueTypes* tábla.



#### <a name="steps"></a>Lépések

Most frissíteni feladatot hoz létre a **VenueTypes** tábla minden egyes bérlők adatbázis, a két új helyszínére típus hozzáadásával.

Hozzon létre egy új feladatot, hogy a létrehozott feladatok rendszerszintű tárolt eljárásokra készletének használata a _jobagent_ adatbázis. A tárolt eljárások a feladat ügynök létrehozásakor lettek létrehozva.

1. Az SSMS, a bérlő kiszolgálóhoz csatlakozni: tenants1-mt -&lt;felhasználói&gt;. database.windows.net

2. Keresse meg a *tenants1* adatbázis.

3. Lekérdezés a *VenueTypes* annak ellenőrzésére, hogy a tábla *Motorkerékpárja Racing* és *úszó Club* még nem az eredménylistában.

4. A kiszolgáló, amely csatlakozni *katalógus-mt -&lt;felhasználói&gt;. database.windows.net*.

5. Csatlakozás a _jobagent_ a kiszolgáló az adatbázis.

6. Az SSMS, nyissa meg a fájl *... \\Tanulási modulok\\séma felügyeleti\\DeployReferenceData.sql*.

7. Az utasítás módosítása: beállítása @User = &lt;felhasználói&gt; és a Wingtip jegyek SaaS több-bérlős adatbázis-alkalmazás telepítésekor használt felhasználói helyettesítésére.

8. A szkriptek futtatásához nyomja le az **F5** billentyűt.

#### <a name="observe"></a>Megfigyelés

Figyelje meg a következő elemeket a *DeployReferenceData.sql* parancsfájlt:

- **SP\_hozzáadása\_cél\_csoport** hoz létre a célcsoport neve *DemoServerGroup*, és a célként megadott tagokat vesz fel a csoportba.

- **SP\_hozzáadása\_cél\_csoport\_tag** a következő elemek hozzáadása:
    - A *server* céloz tag típusa.
        - Ez a *tenants1-mt -&lt;felhasználói&gt;*  kiszolgálót, amely tartalmazza a bérlők adatbázisok.
        - A feladat végrehajtása időpontjában bérlői adatbázisok többek között a kiszolgáló tartalmazza.
    - A *adatbázis* céloz meg a sablon adatbázis tagtípus (*basetenantdb*), amely a található *katalógus-mt -&lt;felhasználói&gt;*  kiszolgáló,
    - A *adatbázis* céloz tagtípus tartalmazza a *adhocreporting* egy újabb oktatóanyagban használt adatbázist.

- **SP\_hozzáadása\_feladat** létrehoz egy feladatot nevű *hivatkozás adatok telepítési*.

- **SP\_hozzáadása\_feladatlépés használja** hoz létre a T-SQL parancs szövege frissíteni a referenciatábla VenueTypes tartalmazó feladatlépéshez.

- A szkript fennmaradó nézetei megjelenítik, hogy léteznek-e az objektumok, és figyelik a feladat-végrehajtást. Tekintse át a értéket használja ezeket a lekérdezéseket a **életciklus** oszlop határozza meg, ha a feladat befejeződött. A feladat frissíti a bérlők adatbázist, és frissíti a referenciatábla tartalmazó két további adatbázisok.

A szolgáltatáshoz az SSMS, keresse meg azt a bérlő adatbázis a a *tenants1-mt -&lt;felhasználói&gt;*  kiszolgáló. Lekérdezés a *VenueTypes* annak ellenőrzésére, hogy a tábla *Motorkerékpárja Racing* és *úszó Club* most hozzáadódnak a táblában. A számuk helyszínére típusú két kell növekedett.

## <a name="create-a-job-to-manage-the-reference-table-index"></a>Feladat létrehozása a referenciatábla indexének kezeléséhez

Ebben a gyakorlatban létrehoz egy feladatot, a hivatkozás tábla elsődleges kulcsa a bérlő adatbázisok az index újraépítése. Az index rebuild egy tipikus adatbázis felügyeleti műveletet, amelyek a rendszergazda egy nagy mennyiségű adatok betöltését, a teljesítmény javítása érdekében betöltése után.

1. Az SSMS, csatlakozzon a _jobagent_ adatbázis *katalógus-mt -&lt;felhasználói&gt;. database.windows.net* kiszolgáló.

2. Nyissa meg a szolgáltatáshoz az ssms, *... \\Tanulási modulok\\séma felügyeleti\\OnlineReindex.sql*.

3. A szkriptek futtatásához nyomja le az **F5** billentyűt.

#### <a name="observe"></a>Megfigyelés

Figyelje meg a következő elemeket a *OnlineReindex.sql* parancsfájlt:

* **SP\_hozzáadása\_feladat** létrehoz egy új feladatot nevű *Online újraindexelése PK\_\_VenueTyp\_\_265E44FD7FD4C885*.

* **SP\_hozzáadása\_feladatlépés használja** hoz létre az index frissítésének T-SQL-parancs szöveget tartalmazó feladatlépéshez.

* A parancsfájl-figyelő feladat végrehajtása a fennmaradó nézeteket. Tekintse át a értéket használja ezeket a lekérdezéseket a **életciklus** oszlop határozza meg, ha a feladat sikeresen befejeződött a cél-csoport minden tagjára.

## <a name="additional-resources"></a>További források

<!-- TODO: Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->
* [Kiterjesztett felhőalapú adatbázisok kezelése](sql-database-elastic-jobs-overview.md)
* [Horizontálisan felskálázott felhőalapú adatbázisok létrehozása és kezelése](sql-database-elastic-jobs-create-and-manage.md)

## <a name="next-steps"></a>További lépések

Ennek az oktatóanyagnak a segítségével megtanulta a következőket:

> [!div class="checklist"]
.
> * Hozzon létre egy T-SQL feladatok futtatása több adatbázis közötti feladat-ügynök
> * Az összes bérlői adatbázis referenciaadatok frissítése
> * Index létrehozása a táblához az összes bérlői adatbázisban

Ezt követően próbálja meg a [alkalmi jelentéskészítési oktatóanyag](saas-multitenantdb-adhoc-reporting.md) eltérések felfedezése, elosztott lekérdezések futtatásáért bérlői adatbázisok.

