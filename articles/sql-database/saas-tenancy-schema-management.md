---
title: Séma kezelése egybérlős alkalmazásban
description: Több bérlő sémájának kezelése az Azure SQL Database-t használó egybérlős alkalmazásban
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 09/19/2018
ms.openlocfilehash: b6802d97b964b8863f6c2fce0cebfe16782b46fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269210"
---
# <a name="manage-schema-in-a-saas-application-using-the-database-per-tenant-pattern-with-azure-sql-database"></a>Séma kezelése egy SaaS-alkalmazásban a bérlőnkénti adatbázis-minta használatával az Azure SQL Database használatával
 
Az adatbázis-alkalmazások fejlődésével elkerülhetetlenül módosítani kell az adatbázissémát vagy a referenciaadatokat.  Az adatbázis-karbantartási feladatokra is rendszeres időközönként szükség van. Egy olyan alkalmazás kezelése, amely bérlői mintánként használja az adatbázist, megköveteli ezeket a módosításokat vagy karbantartási feladatokat a bérlői adatbázisok flottájában.

Ez az oktatóanyag két forgatókönyvet tár fel: referencia-adatfrissítések üzembe helyezése az összes bérlő számára, és a referenciaadatokat tartalmazó index újraépítése a táblázatban. A [rugalmas feladatok](elastic-jobs-overview.md) szolgáltatás hajtja végre ezeket a műveleteket az összes bérlői adatbázisok, és a sablon adatbázis új bérlői adatbázisok létrehozásához használt sablon adatbázis.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> 
> * Feladatügynök létrehozása
> * A T-SQL-feladatok futtatása az összes bérlői adatbázisban
> * Referenciaadatok frissítése az összes bérlői adatbázisban
> * Index létrehozása a táblához az összes bérlői adatbázisban


Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip jegyek SaaS-adatbázis bérlőnként alkalmazás telepítve van. Ha kevesebb, mint öt perc alatt üzembe szeretne helyezni, [olvassa el a Wingtip Tickets SaaS-adatbázis üzembe helyezése és feltárása bérlői alkalmazásonként című témakört.](saas-dbpertenant-get-started-deploy.md)
* Az Azure PowerShell telepítve van. Részletes információk: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Telepítve van az SQL Server Management Studio (SSMS) legújabb verziója. [Az SSMS letöltése és telepítése](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

> [!NOTE]
> Ez az oktatóanyag az SQL Database szolgáltatás egy korlátozott előzetes verziójának funkcióit használja (Elastic Database-feladatok). Ha szeretné ezt az oktatóanyagot, adja SaaSFeedback@microsoft.com meg az előfizetés-azonosítóját a subject=Elastic Jobs Preview használatával. Miután megkapta a megerősítést az előfizetésének engedélyezéséről, [töltse le és telepítse a legújabb kiadás előtti feladatok parancsmagjait](https://github.com/jaredmoo/azure-powershell/releases). Ez az előnézet korlátozott, ezért forduljon SaaSFeedback@microsoft.com a kapcsolódó kérdésekhez vagy támogatáshoz.

## <a name="introduction-to-saas-schema-management-patterns"></a>Bevezetés a SaaS-sémakezelési minták ba

Az adatbázis bérlői minta elkülöníti a bérlői adatokhatékonyan, de növeli az adatbázisok kezelésére és karbantartására. [Rugalmas feladatok](elastic-jobs-overview.md) megkönnyíti az SQL-adatbázisok felügyeletét és felügyeletét. A feladatok lehetővé teszik a feladatok (T-SQL-parancsfájlok) biztonságos és megbízható futtatását adatbázisok egy csoportja ellen. A feladatok séma- és gyakori referenciaadat-módosításokat telepíthetnek az alkalmazás összes bérlői adatbázisában. Rugalmas feladatok is használható egy *sablon* adatbázis új bérlők létrehozásához használt, biztosítva, hogy mindig a legújabb séma és a referencia adatokat.

![képernyő](media/saas-tenancy-schema-management/schema-management-dpt.png)


## <a name="elastic-jobs-limited-preview"></a>Az Elastic Jobs korlátozott előzetes verziója

Van egy új verziója rugalmas feladatok, amely most az Azure SQL Database integrált szolgáltatása. Ez Elastic Jobs-nak ez az új verziója jelenleg korlátozott előzetes verzió. Ez a korlátozott előzetes verzió jelenleg támogatja a PowerShell használatával egy feladat-ügynök létrehozásához, és a T-SQL feladatok létrehozásához és kezeléséhez.

> [!NOTE]
> Ez az oktatóanyag az SQL Database szolgáltatás egy korlátozott előzetes verziójának funkcióit használja (Elastic Database-feladatok). Ha szeretné ezt az oktatóanyagot, adja SaaSFeedback@microsoft.com meg az előfizetés-azonosítóját a subject=Elastic Jobs Preview használatával. Miután megkapta a megerősítést az előfizetésének engedélyezéséről, [töltse le és telepítse a legújabb kiadás előtti feladatok parancsmagjait](https://github.com/jaredmoo/azure-powershell/releases). Ez az előnézet korlátozott, ezért forduljon SaaSFeedback@microsoft.com a kapcsolódó kérdésekhez vagy támogatáshoz.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>A Wingtip jegyek SaaS-adatbázis beszereznie bérlői alkalmazásparancsfájlokonként

Az alkalmazás forráskódja és felügyeleti parancsfájljai a [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-tárházban érhetők el. Tekintse meg az [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) a Wingtip Jegyek SaaS-parancsfájlok letöltéséhez és feloldásához szükséges lépésekhez.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Feladatügynök-adatbázis és új feladatügynök létrehozása

Ez az oktatóanyag megköveteli, hogy a PowerShell használatával hozzon létre egy feladat-ügynök és a háttérfeladat-ügynök adatbázis. A feladatügynök-adatbázis feladatdefiníciókat, feladatállapotot és előzményeket tartalmaz. A feladatügynök és az adatbázis létrehozása után azonnal létrehozhat és figyelhet feladatokat.

1. **A PowerShell ISE**, nyissa ... \\Tanulási modulok\\sémakezelése\\*dema-SchemaManagement.ps1*.
1. A szkript futtatásához nyomja le az **F5** billentyűt.

A *Demo-SchemaManagement.ps1* parancsfájl meghívja a *Deploy-SchemaManagement.ps1* parancsfájlt, hogy hozzon létre egy *osagent* nevű SQL-adatbázist a katalóguskiszolgálón. Ezután létrehozza a feladat-ügynök, az adatbázis taszinkaként.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Feladat létrehozása új referenciaadatok bevezetéséhez az összes bérlőn

A Wingtip Jegyek alkalmazásban minden bérlői adatbázis tartalmaz egy támogatott helyszíntípusokat. Minden helyszín egy adott helyszíntípussal rendelkezik, amely meghatározza a tárolható események típusát, és meghatározza az alkalmazásban használt háttérképet. Ahhoz, hogy az alkalmazás támogassa az új típusú eseményeket, ezeket a referenciaadatokat frissíteni kell, és új helyszíntípusokat kell hozzáadni.  Ebben a gyakorlatban frissítést helyezhet üzembe az összes bérlői adatbázison két további helyszíntípus hozzáadásához: *Motorkerékpár-verseny* és *Úszóklub*.

Először tekintse át az egyes bérlői adatbázisokban szereplő helyszíntípusokat. Csatlakozzon az SQL Server Management Studio (SSMS) egyik bérlői adatbázisához, és vizsgálja meg a VenueTypes táblát.  Ezt a táblát az Azure Portal lekérdezésszerkesztőjében is lekérdezheti, amely az adatbázislapról érhető el. 

1. Nyissa meg az SSMS-t, és csatlakozzon a bérlői kiszolgálóhoz: *tenants1-dpt-&lt;user&gt;.database.windows.net*
1. Annak ellenőrzéséhez, hogy *a Motorkerékpár-verseny-* és *Úszóklub* jelenleg **nem** szerepel a csomagban, keresse meg a _contosoconcerthall_ adatbázist a *&lt;tenants1-dpt- felhasználói&gt; * kiszolgálón, és kérdezze le a *VenueTypes* táblát.

Most hozzon létre egy feladatot a *VenueTypes* tábla frissítésére az összes bérlői adatbázisban az új helyszíntípusok hozzáadásához.

Új feladat létrehozásához a feladatügynök létrehozásakor a _jobagent_ adatbázisban létrehozott feladatok rendszertárolt eljárásait használja.

1. Az SSMS-ben csatlakozzon a katalóguskiszolgálóhoz: *katalógus-dpt-&lt;&gt;user .database.windows.net* server 
1. Az SSMS-ben nyissa meg a fájlt ... \\Tanulási modulok\\sémakezelése\\DeployReferenceData.sql
1. Az utasítás módosítása: &lt;&gt; SET @wtpUser = felhasználó és a Wingtip Tickets SaaS-adatbázis bérlőnkénti telepítésekor használt felhasználói érték helyettesítése
1. Győződjön meg arról, hogy csatlakozik a _jobagent_ adatbázishoz, és nyomja le az **F5** billentyűt a parancsfájl futtatásához

Figyelje meg a következő elemeket a *DeployReferenceData.sql* parancsfájlban:
* **Sp\_\_add\_target group** létrehozza a célcsoport nevét DemoServerGroup.
* **Az\_\_sp\_\_add target group tag** a céladatbázisok készletének meghatározására szolgál.  Először a _&lt;tenants1-dpt- felhasználói&gt; _ kiszolgáló kerül hozzáadásra.  Ha a kiszolgálót célként adja hozzá, akkor a feladat végrehajtásakor a kiszolgáló adatbázisai is szerepelni fognak a feladatban. Ezután a _basetenantdb_ adatbázis és az *adhocreporting* adatbázis (egy későbbi oktatóanyagban használt) tárolóként kerül hozzáadásra.
* **sp\_\_add job** létrehoz egy feladat nevű _referencia adatok telepítése_.
* **Sp\_\_add jobstep** létrehozza a feladat lépés, amely tartalmazza a T-SQL parancs szövegét, hogy frissítse a referenciatábla, VenueTypes.
* A szkript fennmaradó nézetei megjelenítik, hogy léteznek-e az objektumok, és figyelik a feladat-végrehajtást. Ezekkel a lekérdezésekkel tekintse át az **életciklus-oszlop** állapotértékét, és állapítsa meg, hogy a feladat befejeződött-e az összes céladatbázison.

Miután a parancsfájl befejeződött, ellenőrizheti, hogy a referenciaadatok frissültek-e.  Az SSMS-ben keresse meg a *contosoconcerthall* adatbázist a *tenants1-dpt-&lt;user&gt; * kiszolgálón, és kérdezze le a *VenueTypes* táblát.  Ellenőrizze, hogy *a Motorkerékpár Racing* and Swimming *Club* már jelen **van.**


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Feladat létrehozása a referenciatábla indexének kezeléséhez

Ez a gyakorlat egy feladat segítségével újraépíti az indexet a referenciatábla elsődleges kulcsán.  Ez egy tipikus adatbázis-karbantartási művelet, amely nagy mennyiségű adat betöltése után végezhető el.

Hozzon létre egy feladatot ugyanannak a feladatnak a „system” által tárolt eljárásait használva.

1. Nyissa meg az SSMS-t, és csatlakozzon a _katalógus-dpt-&lt;&gt;felhasználó .database.windows.net_ szerverhez
1. A fájl megnyitása _... Tanulási modulok\\sémakezelése\\OnlineReindex.sql \\_
1. Kattintson a jobb gombbal, válassza a Kapcsolat lehetőséget, és csatlakozzon a _&lt;katalógusfelhasználó&gt;.database.windows.net_ kiszolgálóhoz, ha még nem csatlakozott
1. Győződjön meg arról, hogy csatlakozik a _jobagent_ adatbázishoz, és nyomja le az **F5** billentyűt a parancsfájl futtatásához

Figyelje meg az _OnlineReindex.sql_ parancsfájl következő elemeit:
* **sp\_\_add munkát** létrehoz egy új feladatot\_\_az úgynevezett "Online Reindex PK VenueTyp\_\_265E44FD7FD4C885"
* **sp\_\_add jobstep** létrehozza a Feladat lépés, amely tartalmazza a T-SQL parancs szövegét, hogy frissítse a tárgymutató
* A parancsfájl fennmaradó nézetei figyelik a feladat végrehajtását. Ezekkel a lekérdezésekkel áttekintheti az **életciklus-oszlop** állapotértékét, és megállapíthatja, hogy a feladat sikeresen befejeződött-e az összes célcsoport-tagon.



## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> 
> * Feladatügynök létrehozása több adatbázis t-SQL-feladatainak futtatásához
> * Referenciaadatok frissítése az összes bérlői adatbázisban
> * Index létrehozása a táblához az összes bérlői adatbázisban

Ezután próbálja meg az [Ad hoc jelentési oktatóanyag](saas-tenancy-cross-tenant-reporting.md) ot az elosztott lekérdezések bérlői adatbázisok közötti futtatásának feltárásához.


## <a name="additional-resources"></a>További források

* [További oktatóanyagok, amelyek a Wingtip jegyek SaaS-adatbázis bérlőnkénti alkalmazásonkénti üzembe helyezésére épülnek](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Kiterjesztett felhőalapú adatbázisok kezelése](elastic-jobs-overview.md)