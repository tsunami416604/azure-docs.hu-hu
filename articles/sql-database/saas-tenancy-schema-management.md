---
title: "Azure SQL Database-séma kezelése több-bérlős alkalmazásban | Microsoft Docs"
description: "Több bérlő sémájának kezelése Azure SQL Database-t használó több-bérlős alkalmazásban"
keywords: "sql database-oktatóanyag"
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2017
ms.author: billgib; sstein
ms.openlocfilehash: 701a7296368cd8150eedf8cc50b989fdf6112101
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2017
---
# <a name="manage-schema-for-multiple-tenants-in-a-multi-tenant-application-that-uses-azure-sql-database"></a>Több bérlő sémájának kezelése Azure SQL Database-t használó több-bérlős alkalmazásban

A [első Wingtip jegyek SaaS adatbázis / bérlői oktatóanyag](saas-dbpertenant-get-started-deploy.md) bemutatja, hogyan az alkalmazás egy bérlő adatbázis létesítéséhez és regisztrálja a katalógusban. Bármely alkalmazás, például a Wingtip jegyek SaaS adatbázis / bérlői app verzióinformációk lesz, és esetenként esetén a módosítások adatbázisba. A módosítások a következők lehetnek: új vagy módosított séma, új vagy módosított referenciaadatok és rutin adatbázis-karbantartási feladatok az alkalmazás optimális teljesítményének biztosítása érdekében. A SaaS-alkalmazásokkal ezeket a módosításokat koordinált módon kell üzembe helyezni valószínűleg nagyszámú bérlői adatbázison. A módosítások lehet a jövőben bérlői adatbázisok van szükségük a kiépítési folyamat szóló.

Ez az oktatóanyag két forgatókönyvet ismertet: referenciaadat-frissítések üzembe helyezése az összes bérlőhöz, és index visszaadása a referenciaadatokat tartalmazó táblázathoz. A [rugalmas feladatok](sql-database-elastic-jobs-overview.md) szolgáltatás hajthatók végre ezeket a műveleteket között egyetlen bérlő számára, és a *arany* bérlői adatbázis, amely sablonként szolgál, az új adatbázisokat.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]

> * Feladat-fiók létrehozása
> * Több bérlő átfogó lekérdezése
> * Az összes bérlői adatbázis adatainak frissítése
> * Index létrehozása a táblához az összes bérlői adatbázisban


Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip jegyek SaaS adatbázis / bérlői alkalmazás telepítve van. Kevesebb mint öt perc alatt telepítéséhez lásd: [központi telepítése és vizsgálja meg a Wingtip jegyek SaaS adatbázis / bérlői alkalmazás](saas-dbpertenant-get-started-deploy.md)
* Az Azure PowerShell telepítve van. A részletekért lásd: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Telepítve van az SQL Server Management Studio (SSMS) legújabb verziója. [Az SSMS letöltése és telepítése](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

*Ez az oktatóanyag az SQL Database szolgáltatás egy korlátozott előzetes verziójának funkcióit használja (Elastic Database-feladatok). Ha szeretné elvégezni ezt az oktatóanyagot, küldje el az előfizetés-azonosítóját az SaaSFeedback@microsoft.com címre, tárgyként megadva a subject=Elastic Jobs Preview szöveget. Miután megkapta a megerősítést az előfizetésének engedélyezéséről, [töltse le és telepítse a legújabb kiadás előtti feladatok parancsmagjait](https://github.com/jaredmoo/azure-powershell/releases). Ez az előnézet korlátozva, így forduljon SaaSFeedback@microsoft.com kapcsolatos kérdésekre, vagy a támogatási szolgálathoz.*


## <a name="introduction-to-saas-schema-management-patterns"></a>Az SaaS-sémakezelési minták bemutatása

Az adatbázisonkénti egyetlen bérlőt alkalmazó SaaS-mintából fakadó adatelkülönítésnek számos előnye van, ugyanakkor a sok adatbázis karbantartása és kezelése miatt tovább növeli a bonyolultságot. [Rugalmas feladatok](sql-database-elastic-jobs-overview.md) elősegíti az SQL-adatréteg felügyeletét és kezelését. A feladatok lehetővé teszik a feladatok (T-SQL-szkriptek) adatbáziscsoportokon történő, felhasználói interakciótól vagy beviteltől független biztonságos és megbízható futtatását. Ez a módszer használható a séma és a közös referenciaadat-változások üzembe helyezésére egy alkalmazás összes bérlőjében. Az Elastic Jobs feladatok az új bérlők létrehozására használt adatbázis *arany* másolatának karbantartására is használhatók, hogy mindig a legújabb sémával és referenciaadatokkal rendelkezzen.

![képernyő](media/saas-tenancy-schema-management/schema-management.png)


## <a name="elastic-jobs-limited-preview"></a>Az Elastic Jobs korlátozott előzetes verziója

Megjelent az Elastic Jobs új verziója, amely most az Azure SQL Database integrált funkciója (nincs szükség hozzá további szolgáltatásokra vagy összetevőkre). Ez Elastic Jobs-nak ez az új verziója jelenleg korlátozott előzetes verzió. Ez a korlátozott előzetes verzió jelenleg a PowerShellt támogatja feladatfiókok létrehozásához és a T-SQL-t a feladatok létrehozásához és kezeléséhez.

> [!NOTE]
> *Ez az oktatóanyag az SQL Database szolgáltatás egy korlátozott előzetes verziójának funkcióit használja (Elastic Database-feladatok). Ha szeretné elvégezni ezt az oktatóanyagot, küldje el az előfizetés-azonosítóját az SaaSFeedback@microsoft.com címre, tárgyként megadva a subject=Elastic Jobs Preview szöveget. Miután megkapta a megerősítést az előfizetésének engedélyezéséről, [töltse le és telepítse a legújabb kiadás előtti feladatok parancsmagjait](https://github.com/jaredmoo/azure-powershell/releases). Ez az előnézet korlátozva, így forduljon SaaSFeedback@microsoft.com kapcsolatos kérdésekre, vagy a támogatási szolgálathoz.*

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>A Wingtip jegyek SaaS adatbázis / bérlői alkalmazás parancsfájlok beolvasása

A Wingtip jegyek SaaS adatbázis / bérlői parancsfájlok és az alkalmazás forráskódjához érhetők el a [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) github-tárház. [Töltse le a Wingtip jegyek SaaS adatbázis / bérlői parancsfájlok lépéseket](saas-dbpertenant-wingtip-app-guidance-tips.md#download-and-unblock-the-wingtip-tickets-saas-database-per-tenant-scripts).

## <a name="create-a-job-account-database-and-new-job-account"></a>Feladatfiók-adatbázis és új feladatfiók létrehozása

Ennek az oktatóanyagnak a használatához a PowerShell-lel létre kell hoznia a feladatfiók-adatbázis és a feladatfiókot. Az Elastic Jobs az MSDB-hez és a SQL-ügynökhöz hasonlóan az Azure SQL Database-t használja a feladatdefiníciók, feladatállapotok és -előzmények tárolására. A feladatfiók létrehozása után azonnal létrehozhatja és figyelheti a feladatokat.

1. **A PowerShell ISE**, nyissa meg... \\Tanulási modulok\\séma felügyeleti\\*bemutató-SchemaManagement.ps1*.
1. A szkript futtatásához nyomja le az **F5** billentyűt.

A *Demo-SchemaManagement.ps1* szkript a *Deploy-SchemaManagement.ps1* szkriptet hívja a **jobaccount** nevű *S2* adatbázis katalóguskiszolgálón történő létrehozásához. Ezután létrehozza a feladatfiókot, paraméterként átadva a feladatfiók nevű adatbázist a feladatfiók-létrehozási hívásnak.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Feladat létrehozása új referenciaadatok bevezetéséhez az összes bérlőn

Az egyes bérlői adatbázisok helyszíntípuskészleteket tartalmaznak, amelyek a helyszínen üzemeltetett eseménytípusokat határozzák meg. Ebben a gyakorlatban frissítést helyezhet üzembe az összes bérlői adatbázison két további helyszíntípus hozzáadásához: *Motorkerékpár-verseny* és *Úszóklub*. Ezek a helyszíntípusok megfelelnek a bérlő eseményalkalmazásában látható háttérképnek.

Kattintson a Helyszíntípus legördülő menüre, és ellenőrizze, hogy csak 10 helyszíntípus-lehetőség legyen elérhető, és hogy a „Motorkerékpár-verseny” és az „Úszóklub” ne szerepeljen a listán.

Most hozzon létre egy feladatot, amely frissíti a *VenueTypes* táblát az összes bérlői adatbázisban, és adja hozzá az új helyszíntípusokat.

Új feladat létrehozásához a feladatfiók létrehozásakor a feladatfiók adatbázisban létrehozott feladatkészletek rendszer által tárolt folyamatait használjuk.

1. Nyissa meg a szolgáltatáshoz az SSMS és a kiszolgáló csatlakozzon: katalógus-dpt -\<felhasználói\>. database.windows.net kiszolgáló
1. A bérlői kiszolgáló is kapcsolódhat: tenants1-dpt -\<felhasználói\>. database.windows.net
1. Keresse meg a *contosoconcerthall* adatbázisról a a *tenants1-dpt -\<felhasználói\>*  kiszolgáló és a lekérdezés a *VenueTypes* annak ellenőrzésére, hogy a tábla *Motorkerékpárja Racing* és *úszó Club* **nem** az eredménylistában.
1. A szolgáltatáshoz az SSMS nyissa meg a fájlt... \\Tanulási modulok\\séma felügyeleti\\DeployReferenceData.sql
1. Az utasítás módosítása: beállítása @wtpUser = &lt;felhasználói&gt; és a Wingtip jegyek SaaS adatbázis / bérlői alkalmazás telepítésekor használt felhasználói helyettesítésére
1. Ellenőrizze, hogy kapcsolódik-e a feladatfiók adatbázishoz, és nyomja le az **F5** billentyűt a szkript futtatásához

* Az **sp\_add\_target\_group** létrehozza a DemoServerGroup nevű célcsoportot, most hozzá kell adnunk a céltagokat.
* **SP\_hozzáadása\_cél\_csoport\_tag** ad hozzá egy *server* céloz tag típusa, amely úgy ítéli meg, hogy a kiszolgálón belüli összes adatbázis (vegye figyelembe, ez a tenants1 - dpt - &lt;Felhasználói&gt; a bérlő adatbázisokat tartalmazó kiszolgáló) időpontban feladat végrehajtási szerepelnie kell a feladatot, a második hozzáadásával egy *adatbázis* céloz tagtípus, kifejezetten a "golden" adatbázis (basetenantdb) lévő katalógus-dpt -&lt;felhasználói&gt; kiszolgáló, és végül egy másik *adatbázis* csoport tagja céltípust a adhocanalytics adatbázis, amely használatban van egy újabb tartalmazza az oktatóanyag.
* Az **sp\_add\_job** létrehoz egy „Referenciaadat-telepítés” nevű feladatot
* **SP\_hozzáadása\_feladatlépés használja** hoz létre a T-SQL parancs szövege a referenciatábla VenueTypes frissítése tartalmazó feladat lépésének
* A szkript fennmaradó nézetei megjelenítik, hogy léteznek-e az objektumok, és figyelik a feladat-végrehajtást. Tekintse át a értéket használja ezeket a lekérdezéseket a **életciklus** határozza meg, ha a feladat sikeresen befejeződött az összes bérlői és a két további adatbázisait a referenciatábla tartalmazó oszlop.

1. A szolgáltatáshoz az SSMS, keresse meg a a *contosoconcerthall* adatbázisról a a *tenants1-dpt -\<felhasználói\>*  kiszolgáló és a lekérdezés a *VenueTypes* tábla a Ellenőrizze, hogy *Motorkerékpárja Racing* és *úszó Club* **vannak** most az eredmények listájában.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Feladat létrehozása a referenciatábla indexének kezeléséhez

Az előző gyakorlathoz hasonlóan, ebben a gyakorlatban létrehoz egy feladatot a referenciatábla elsődleges kulcsához tartozó index újraépítéséhez, amely a rendszergazda gyakori adatbázis-kezelési művelete, miután nagy mennyiségű adat töltődött be a táblába.

Hozzon létre egy feladatot ugyanannak a feladatnak a „system” által tárolt eljárásait használva.

1. Nyissa meg a szolgáltatáshoz az SSMS, és kapcsolódjon a katalógus-dpt -&lt;felhasználói&gt;. database.windows.net kiszolgáló
1. Nyissa meg a ...\\Tanulási modulok\\Sémakezelés\\.sql fájlt
1. Kattintson a jobb gombbal, válassza ki a kapcsolat, a katalógushoz való kapcsolódás-dpt -&lt;felhasználói&gt;. database.windows.net kiszolgáló, ha még nincs csatlakoztatva
1. Ellenőrizze, hogy kapcsolódik-e a jobaccount adatbázishoz, és nyomja le az F5 billentyűt a parancsprogram futtatásához

* Az sp\_add\_job létrehoz egy „Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885” nevű új feladatot
* Az sp\_add\_jobstep létrehozza a T-SQL-parancsszöveget tartalmazó feladatlépést az index frissítéséhez




## <a name="next-steps"></a>Következő lépések

Ennek az oktatóanyagnak a segítségével megtanulta a következőket:

> [!div class="checklist"]

> * Feladatfiók létrehozása több bérlőben történő lekérdezéshez
> * Az összes bérlői adatbázis adatainak frissítése
> * Index létrehozása a táblához az összes bérlői adatbázisban

[Oktatóanyag alkalmi elemzéshez](saas-tenancy-adhoc-analytics.md)


## <a name="additional-resources"></a>További források

* [További oktatóprogramot kínál, amelyek a Wingtip jegyek SaaS adatbázis / bérlői alkalmazástelepítés épül](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Kiterjesztett felhőalapú adatbázisok kezelése](sql-database-elastic-jobs-overview.md)
* [Horizontálisan felskálázott felhőalapú adatbázisok létrehozása és kezelése](sql-database-elastic-jobs-create-and-manage.md)