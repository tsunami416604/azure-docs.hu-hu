---
title: Egy egybérlős alkalmazást az Azure SQL Database-séma kezelése |} A Microsoft Docs
description: Séma kezelése több bérlőnél egy egybérlős alkalmazást, amely az Azure SQL Database
keywords: sql database-oktatóanyag
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 09/19/2018
ms.author: sstein
ms.reviewer: billgib
ms.openlocfilehash: 6a37bdd6e8b7893da5df1113f5f3f153148cb1b0
ms.sourcegitcommit: 8b694bf803806b2f237494cd3b69f13751de9926
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2018
ms.locfileid: "46498079"
---
# <a name="manage-schema-in-a-saas-application-using-the-database-per-tenant-pattern-with-azure-sql-database"></a>A bérlőnkénti adatbázis minta használatával az Azure SQL Database SaaS-alkalmazásokban séma kezelése
 
Egy adatbázis-alkalmazások fejlődésének elkerülhetetlenül módosításokat kell az adatbázis-séma vagy a hivatkozás adatai számára kell tenni.  Adatbázis-karbantartási feladatok is szükségesek a rendszeres időközönként. Bérlői mintában az adatbázist használó alkalmazás kezelése igényel a alkalmazni ezeket megváltozik, vagy a karbantartási feladatok között egy valószínűleg nagyszámú bérlői adatbázison.

Ez az oktatóanyag bemutatja, két forgatókönyv - frissítések telepítése az összes bérlőhöz, és a referenciaadatokat tartalmazó táblázathoz az index újraépítése. A [rugalmas feladatok](sql-database-elastic-jobs-overview.md) funkció hajthatók végre ezeket a műveleteket, az összes bérlői adatbázison és az új bérlői adatbázisok létrehozására használt sablon adatbázis.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]

> * Hozzon létre egy feladatot az ügynök
> * Hatására az összes bérlői adatbázis futtatásához a T-SQL-feladatok
> * Az összes bérlői adatbázis referenciaadatok frissítése
> * Index létrehozása a táblához az összes bérlői adatbázisban


Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip Tickets SaaS adatbázis Per bérlői alkalmazás telepítve van. Kevesebb mint öt perc alatt üzembe helyezéséhez lásd: [üzembe helyezése és felfedezése a Wingtip Tickets SaaS adatbázis bérlői alkalmazásonként](saas-dbpertenant-get-started-deploy.md)
* Az Azure PowerShell telepítve van. A részletekért lásd: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Telepítve van az SQL Server Management Studio (SSMS) legújabb verziója. [Az SSMS letöltése és telepítése](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

> [!NOTE]
> Ez az oktatóanyag az SQL Database szolgáltatás, amely egy korlátozott előzetes verzió (rugalmas adatbázis-feladatok) funkcióit használja. Ha szeretné elvégezni ezt az oktatóanyagot, adja meg az előfizetés-Azonosítóját az SaaSFeedback@microsoft.com a subject = Elastic Jobs Preview szöveget. Miután megkapta a megerősítést az előfizetésének engedélyezéséről, [töltse le és telepítse a legújabb kiadás előtti feladatok parancsmagjait](https://github.com/jaredmoo/azure-powershell/releases). Ebben az előzetes verzióban korlátozva, ezért forduljon SaaSFeedback@microsoft.com kapcsolatos kérdésekre, vagy támogatást.

## <a name="introduction-to-saas-schema-management-patterns"></a>SaaS-séma kezelése minták bemutatása

Bérlő mintában az adatbázis elkülöníti a bérlői adatok hatékony, de növeljük a számot, az adatbázisok kezelésére és karbantartására. [Rugalmas feladatok](sql-database-elastic-jobs-overview.md) elősegíti a felügyeletét és SQL Database-adatbázisok kezelését. A feladatok lehetővé teszik, hogy biztonságosan és megbízhatóan, futtassa a feladatok (T-SQL-parancsfájlok) adatbázisok csoportjain. Feladatok üzembe sémát és a közös referenciaadat-változások az alkalmazások összes bérlői adatbázison. Rugalmas feladatok is használható fenntartani egy *sablon* új bérlők számára, hogy mindig létrehozására használt adatbázis rendelkezik a legújabb sémával és referenciaadatokkal.

![képernyő](media/saas-tenancy-schema-management/schema-management-dpt.png)


## <a name="elastic-jobs-limited-preview"></a>Az Elastic Jobs korlátozott előzetes verziója

Nincs, amely mostantól az Azure SQL Database integrált funkciója Elastic Jobs új verziója. Ez Elastic Jobs-nak ez az új verziója jelenleg korlátozott előzetes verzió. A korlátozott előzetes verzió jelenleg támogatja, hozzon létre egy feladatot az ügynök, és T-SQL feladatok létrehozása és kezelése a PowerShell segítségével.

> [!NOTE]
> Ez az oktatóanyag az SQL Database szolgáltatás, amely egy korlátozott előzetes verzió (rugalmas adatbázis-feladatok) funkcióit használja. Ha szeretné elvégezni ezt az oktatóanyagot, adja meg az előfizetés-Azonosítóját az SaaSFeedback@microsoft.com a subject = Elastic Jobs Preview szöveget. Miután megkapta a megerősítést az előfizetésének engedélyezéséről, [töltse le és telepítse a legújabb kiadás előtti feladatok parancsmagjait](https://github.com/jaredmoo/azure-powershell/releases). Ebben az előzetes verzióban korlátozva, ezért forduljon SaaSFeedback@microsoft.com kapcsolatos kérdésekre, vagy támogatást.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>A Wingtip Tickets SaaS adatbázis / bérlő alkalmazásszkriptek beolvasása

Az alkalmazás forrás-kód és a felügyeleti parancsprogramokat érhetők el a [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-adattárban. Tekintse meg a [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) töltse le és a Wingtip Tickets SaaS-parancsfájlok feloldása.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Hozzon létre egy feladatot az ügynök, adatbázis és az új feladat ügynök

Az oktatóanyaghoz a PowerShell használatával hozzon létre egy feladatot az ügynök és a biztonsági feladat ügynök adatbázisában. A feladat adatbázisa tárolja a feladatdefiníciók, feladatállapotok és -előzményeket. A feladat-ügynök és a kapcsolódó adatbázis létrehozása után hozzon létre, és közvetlenül figyelheti a feladatokat.

1. **A PowerShell ISE-ben**, nyissa meg... \\Tanulási modulok\\Sémakezelés\\*Demo-SchemaManagement.ps1*.
1. A szkriptek futtatásához nyomja le az **F5** billentyűt.

A *Demo-SchemaManagement.ps1* szkript a *Deploy-SchemaManagement.ps1* nevű SQL-adatbázis létrehozása-szkript *osagent* a katalóguskiszolgálón. Ezután a feladat ügynök, az adatbázis, mint egy paraméter használatával hoz létre.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Feladat létrehozása új referenciaadatok bevezetéséhez az összes bérlőn

A Wingtip Tickets alkalmazás minden bérlői adatbázis tartalmaz egy támogatott helyszíntípuskészleteket tartalmaznak. Minden egyes helyszín, egy adott helyszín típusának felhasználásával, amely meghatározza, milyen típusú eseményeket, amelyek lehet üzemeltetni, és meghatározza, hogy az alkalmazásban használt háttérkép van. Az alkalmazás támogatásához új típusú eseményeket ezek a referenciaadatok frissítése és az új helyszíntípusokat hozzá kell lennie.  Ebben a gyakorlatban frissítést helyezhet üzembe az összes bérlői adatbázison két további helyszíntípus hozzáadásához: *Motorkerékpár-verseny* és *Úszóklub*.

Először tekintse át az egyes bérlői adatbázisok szerepelnek a helyszíntípusok. A bérlői adatbázisokat az SQL Server Management Studio (SSMS) egyik csatlakozhat, és vizsgálja meg a VenueTypes tábla.  Is lekérdezheti, ha ezt a táblázatot a Lekérdezésszerkesztő az Azure Portalon érhető el, amely az adatbázis oldal. 

1. Nyissa meg az ssms-ben és a bérlői kiszolgálóhoz csatlakozni: *tenants1-dpt -&lt;felhasználói&gt;. database.windows.net*
1. Ellenőrizze, hogy a *motorkerékpár-verseny* és *Úszóklub* **nem** jelenleg foglalt, keresse meg a _contosoconcerthall_ az adatbázis a *tenants1-dpt -&lt;felhasználói&gt;*  kiszolgáló és a lekérdezés a *VenueTypes* tábla.

Most hozzunk létre egy feladatot, amely frissíti a *VenueTypes* táblát az összes bérlői adatbázis hozzáadása az új helyszíntípusokat.

Egy új feladat létrehozásához használni létrehozott tárolt eljárásokat, a feladatok rendszer a _jobagent_ adatbázis-a feladat ügynök létrehozásakor.

1. Az ssms-ben, a kiszolgáló csatlakozni: *katalógus-dpt -&lt;felhasználói&gt;. database.windows.net* kiszolgáló 
1. Az ssms-ben a fájl megnyitása... \\Tanulási modulok\\Sémakezelés\\DeployReferenceData.sql
1. Az utasítás módosítása: állítsa be @wtpUser = &lt;felhasználói&gt; , és illessze be a felhasználó értéket a Wingtip Tickets SaaS adatbázis Per bérlői alkalmazás üzembe helyezésekor használt
1. Győződjön meg arról, hogy csatlakozik az _jobagent_ adatbázist, majd nyomja le **F5** a parancsfájl futtatása

Figyelje meg a következő elemeket az a *DeployReferenceData.sql* parancsfájlt:
* **SP\_hozzáadása\_cél\_csoport** létrehozza a DemoServerGroup nevű.
* **SP\_hozzáadása\_cél\_csoport\_tag** céladatbázis készletét meghatározására szolgál.  Először a _tenants1-dpt -&lt;felhasználói&gt;_  kiszolgáló megjelenik-e.  A kiszolgálón a kiszolgáló hozzáadása a célként hatására az adatbázisok a feladat végrehajtása a feladat foglalandó idején. Ezután a _basetenantdb_ adatbázis és a *adhocreporting* (egy újabb oktatóanyagban használt) adatbázis célként kerülnek.
* **SP\_hozzáadása\_feladat** nevű feladatot hoz létre _referenciaadat-telepítés_.
* **SP\_hozzáadása\_jobstep** a a VenueTypes referenciatáblára való frissítéséhez a T-SQL-parancsszöveget tartalmazó feladatlépést hoz létre.
* A szkript fennmaradó nézetei megjelenítik, hogy léteznek-e az objektumok, és figyelik a feladat-végrehajtást. Ezen lekérdezések használatához, tekintse át az állapot értékét az a **életciklus** határozza meg, ha a feladat befejeződött a céladatbázisok oszlopot.

A szkript befejezése után ellenőrizheti a referenciaadatok frissítve lett.  Az ssms-ben, keresse meg a *contosoconcerthall* adatbázist a a *tenants1-dpt -&lt;felhasználói&gt;*  kiszolgáló és a lekérdezés a *VenueTypes* tábla.  Ellenőrizze, hogy *motorkerékpár-verseny* és *Úszóklub* **vannak** most már jelen.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Feladat létrehozása a referenciatábla indexének kezeléséhez

Ebben a gyakorlatban egy feladatot a referenciatábla elsődleges kulcsához tartozó index újraépítéséhez.  Ez a tipikus adatbázis-karbantartási művelet, amely nagy mennyiségű adat betöltése után előfordulhat, hogy végezhető.

Hozzon létre egy feladatot ugyanannak a feladatnak a „system” által tárolt eljárásait használva.

1. Nyissa meg az ssms-ben, és csatlakozzon a _katalógus-dpt -&lt;felhasználói&gt;. database.windows.net_ kiszolgáló
1. Nyissa meg a fájlt _... \\Tanulási modulok\\Sémakezelés\\.SQL_
1. Kattintson a jobb gombbal, válassza ki a kapcsolat, és csatlakozzon a _katalógus-dpt -&lt;felhasználói&gt;. database.windows.net_ kiszolgálóján, ha még nem kapcsolódott
1. Győződjön meg arról, hogy csatlakozik az _jobagent_ adatbázist, majd nyomja le **F5** a parancsfájl futtatása

Figyelje meg a következő elemeket az a _.SQL_ parancsfájlt:
* **SP\_hozzáadása\_feladat** létrehoz egy új feladatot, úgynevezett "Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885"
* **SP\_hozzáadása\_jobstep** hoz létre a frissítéséhez az index a T-SQL-parancsszöveget tartalmazó feladatlépést
* A szkript fennmaradó nézetei figyelheti a feladat végrehajtása. Ezen lekérdezések használatához, tekintse át az állapot értékét az a **életciklus** oszlop határozza meg, ha a feladat sikeresen befejeződött az összes cél csoport tagjai.



## <a name="next-steps"></a>További lépések

Ennek az oktatóanyagnak a segítségével megtanulta a következőket:

> [!div class="checklist"]

> * Hozzon létre egy feladatot az ügynök több adatbázis között a T-SQL-feladatok futtatása
> * Az összes bérlői adatbázis referenciaadatok frissítése
> * Index létrehozása a táblához az összes bérlői adatbázisban

Ezt követően próbálja meg a [alkalmi jelentéskészítő oktatóanyag](saas-tenancy-cross-tenant-reporting.md) elosztott lekérdezések futtatását több bérlői adatbázisok megismerése.


## <a name="additional-resources"></a>További források

* [A Wingtip Tickets SaaS adatbázis Per bérlői alkalmazástelepítés útmutatóra további oktatóanyagok](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Kiterjesztett felhőalapú adatbázisok kezelése](sql-database-elastic-jobs-overview.md)
* [Horizontálisan felskálázott felhőalapú adatbázisok létrehozása és kezelése](sql-database-elastic-jobs-create-and-manage.md)