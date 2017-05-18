---
title: "Séma kezelése több bérlőnél (Azure SQL Database-t használó SaaS-mintaalkalmazás) | Microsoft Docs"
description: "Séma kezelése több bérlőnél az Azure SQL Database-t használó SaaS-alkalmazásban"
keywords: "sql database-oktatóanyag"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 226cda254934fae30410e54148d5cc527e1c7881
ms.contentlocale: hu-hu
ms.lasthandoff: 05/10/2017


---
# <a name="manage-schema-for-multiple-tenants-in-the-wtp-saas-application"></a>Séma kezelése több bérlőnél a WTP SaaS-alkalmazásban

A WTP alkalmazás bemutatása oktatóanyagból megtudhatja, hogyan lehet a WTP alkalmazással bérlői adatbázist üzembe helyezni a kezdeti sémájával, és regisztrálni a katalógusban. Mint minden alkalmazás, idővel a WTP alkalmazás is fejlődik, és alkalmanként szükségessé válik az adatbázis módosítása. A módosítások a következők lehetnek: új vagy módosított séma, új vagy módosított referenciaadatok és rutin adatbázis-karbantartási feladatok az alkalmazás optimális teljesítményének biztosítása érdekében. A SaaS-alkalmazásokkal ezeket a módosításokat koordinált módon kell üzembe helyezni valószínűleg nagyszámú bérlői adatbázison. A módosításokat a későbbi bérlői adatbázisok miatt is be kell építeni az üzembe helyezési folyamatba.

Ez az oktatóanyag két forgatókönyvet ismertet: referenciaadat-frissítések üzembe helyezése az összes bérlőhöz, és index visszaadása a referenciaadatokat tartalmazó táblázathoz. Ezeknek a műveleteknek az összes bérlőre kiterjedő végrehajtására a [Rugalmas feladatok](sql-database-elastic-jobs-overview.md) funkció használatos és egy *arany* bérlői adatbázis, amely az új adatbázisok sablonja.

Ennek az oktatóanyagnak a segítségével megtanulhatja a következőket:

> [!div class="checklist"]

> * Feladatfiók létrehozása több bérlőben történő lekérdezéshez
> * Az összes bérlői adatbázis adatainak frissítése
> * Index létrehozása a táblához az összes bérlői adatbázisban


Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A WTP alkalmazás üzembe van helyezve. A kevesebb, mint öt perc alatti üzembe helyezéshez lásd: [A WTP SaaS-alkalmazás üzembe helyezése és felfedezése](sql-database-saas-tutorial.md)
* Az Azure PowerShell telepítve van. A részletekért lásd: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Telepítve van az SQL Server Management Studio (SSMS) legújabb verziója. [Az SSMS letöltése és telepítése](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

*Ez az oktatóanyag az SQL Database szolgáltatás egy korlátozott előzetes verziójának funkcióit használja (Elastic Database-feladatok). Ha szeretné elvégezni ezt az oktatóanyagot, küldje el az előfizetés-azonosítóját az SaaSFeedback@microsoft.com címre, tárgyként megadva a subject=Elastic Jobs Preview szöveget. Miután megkapta a megerősítést az előfizetésének engedélyezéséről, [töltse le és telepítse a legújabb kiadás előtti feladatok parancsmagjait](https://github.com/jaredmoo/azure-powershell/releases). Mivel ez egy korlátozott előzetes verzió, az ezzel kapcsolatos kérdésekkel és támogatásért vegye fel a kapcsolatot az SaaSFeedback@microsoft.com címen.*


## <a name="introduction-to-saas-schema-management-patterns"></a>Az SaaS-sémakezelési minták bemutatása

Az adatbázisonkénti egyetlen bérlőt alkalmazó SaaS-mintából fakadó adatelkülönítésnek számos előnye van, ugyanakkor a sok adatbázis karbantartása és kezelése miatt tovább növeli a bonyolultságot. [Rugalmas feladatok](sql-database-elastic-jobs-overview.md) elősegíti az SQL-adatréteg felügyeletét és kezelését. A feladatok lehetővé teszik a feladatok (T-SQL-szkriptek) adatbáziscsoportokon történő, felhasználói interakciótól vagy beviteltől független biztonságos és megbízható futtatását. Ez a módszer használható a séma és a közös referenciaadat-változások üzembe helyezésére egy alkalmazás összes bérlőjében. Az Elastic Jobs feladatok az új bérlők létrehozására használt adatbázis *arany* másolatának karbantartására is használhatók, hogy mindig a legújabb sémával és referenciaadatokkal rendelkezzen.

![képernyő](media/sql-database-saas-tutorial-schema-management/schema-management.png)


## <a name="elastic-jobs-limited-preview"></a>Az Elastic Jobs korlátozott előzetes verziója

Megjelent az Elastic Jobs új verziója, amely most az Azure SQL Database integrált funkciója (nincs szükség hozzá további szolgáltatásokra vagy összetevőkre). Ez Elastic Jobs-nak ez az új verziója jelenleg korlátozott előzetes verzió. Ez a korlátozott előzetes verzió jelenleg a PowerShellt támogatja feladatfiókok létrehozásához és a T-SQL-t a feladatok létrehozásához és kezeléséhez.

> [!NOTE]
> *Ez az oktatóanyag az SQL Database szolgáltatás egy korlátozott előzetes verziójának funkcióit használja (Elastic Database-feladatok). Ha szeretné elvégezni ezt az oktatóanyagot, küldje el az előfizetés-azonosítóját az SaaSFeedback@microsoft.com címre, tárgyként megadva a subject=Elastic Jobs Preview szöveget. Miután megkapta a megerősítést az előfizetésének engedélyezéséről, [töltse le és telepítse a legújabb kiadás előtti feladatok parancsmagjait](https://github.com/jaredmoo/azure-powershell/releases). Mivel ez egy korlátozott előzetes verzió, az ezzel kapcsolatos kérdésekkel és támogatásért vegye fel a kapcsolatot az SaaSFeedback@microsoft.com címen.*

## <a name="get-the-wingtip-application-scripts"></a>A Wingtip alkalmazásszkriptek beolvasása

A Wingtip Tickets szkriptjei és alkalmazás-forráskódja a [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) GitHub-tárban érhetők el. A parancsfájlok a [Tanulási modulok mappában](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules) találhatók. Töltse le a **Tanulási modulok** mappát a helyi számítógépére, a mappaszerkezetének megőrzésével.

## <a name="create-a-job-account-database-and-new-job-account"></a>Feladatfiók-adatbázis és új feladatfiók létrehozása

Ennek az oktatóanyagnak a használatához a PowerShell-lel létre kell hoznia a feladatfiók-adatbázis és a feladatfiókot. Az Elastic Jobs az MSDB-hez és a SQL-ügynökhöz hasonlóan az Azure SQL Database-t használja a feladatdefiníciók, feladatállapotok és -előzmények tárolására. A feladatfiók létrehozása után azonnal létrehozhatja és figyelheti a feladatokat.

1. Nyissa meg a ...\\Tanulási modulok\\Sémakezelés\\*Demo-SchemaManagement.ps1* fájlt a **PowerShell ISE** parancssorában.
1. A szkriptek futtatásához nyomja le az **F5** billentyűt.

A *Demo-SchemaManagement.ps1* szkript a *Deploy-SchemaManagement.ps1* szkriptet hívja a **jobaccount** nevű *S2* adatbázis katalóguskiszolgálón történő létrehozásához. Ezután létrehozza a feladatfiókot, paraméterként átadva a feladatfiók nevű adatbázist a feladatfiók-létrehozási hívásnak.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Feladat létrehozása új referenciaadatok bevezetéséhez az összes bérlőn

Az egyes bérlői adatbázisok helyszíntípuskészleteket tartalmaznak, amelyek a helyszínen üzemeltetett eseménytípusokat határozzák meg. Ebben a gyakorlatban frissítést helyezhet üzembe az összes bérlői adatbázison két további helyszíntípus hozzáadásához: *Motorkerékpár-verseny* és *Úszóklub*. Ezek a helyszíntípusok megfelelnek a bérlő eseményalkalmazásában látható háttérképnek.

Kattintson a Helyszíntípus legördülő menüre, és ellenőrizze, hogy csak 10 helyszíntípus-lehetőség legyen elérhető, és hogy a „Motorkerékpár-verseny” és az „Úszóklub” ne szerepeljen a listán.

Most hozzon létre egy feladatot, amely frissíti a *VenueTypes* táblát az összes bérlői adatbázisban, és adja hozzá az új helyszíntípusokat.

Új feladat létrehozásához a feladatfiók létrehozásakor a feladatfiók adatbázisban létrehozott feladatkészletek rendszer által tárolt folyamatait használjuk.

1. Nyissa meg az SSMS-t, és kapcsolódjon a katalóguskiszolgálóhoz: catalog-\<user\>.database.windows.net kiszolgáló
1. Kapcsolódjon a bérlői kiszolgálóhoz is: tenants1-\<user\>.database.windows.net
1. Keresse meg a *contosoconcerthall* adatbázist a *tenants1* kiszolgálón, és kérdezze le a *VenueTypes* táblát annak a megerősítéséhez, hogy a *Motorkerékpár-verseny* és az *Úszóklub* **nem szerepel** az eredménylistán.
1. Nyissa meg a ... \\Tanulási modulok\\Sémakezelés\\DeployReferenceData.sql fájlt
1. Módosítsa a \<user\> értékét, használja a WTP alkalmazás üzembe helyezésekor alkalmazott felhasználónevet a szkriptben mind a 3 helyen
1. Ellenőrizze, hogy kapcsolódik-e a feladatfiók adatbázishoz, és nyomja le az **F5** billentyűt a szkript futtatásához

* Az **sp\_add\_target\_group** létrehozza a DemoServerGroup nevű célcsoportot, most hozzá kell adnunk a céltagokat.
* Az **sp\_add\_target\_group\_member** hozzáad egy *kiszolgáló* céltagtípust, amely feltételezi, hogy a kiszolgálón (ne feledje, ez a bérlői adatbázisokat tartalmazó customer1-&lt;WtpUser&gt; kiszolgáló) belüli összes adatbázist feladat-végrehajtáskor bele kell foglalni a feladatba, a második egy *adatbázis* céltagtípust ad hozzá, konkrétan a baseTenantDB „arany” adatbázist, amely a catalog-&lt;WtpUser&gt; kiszolgálón található, végül pedig egy másik *adatbázis* célcsoporttagtípust a későbbi oktatóanyagban használt adhocanalytics adatbázis belefoglalásához.
* Az **sp\_add\_job** létrehoz egy „Referenciaadat-telepítés” nevű feladatot
* Az **sp\_add\_jobstep** létrehozza a T-SQL-parancsszöveget tartalmazó feladatlépést a VenueTypes referenciatáblára való frissítéséhez
* A szkript fennmaradó nézetei megjelenítik, hogy léteznek-e az objektumok, és figyelik a feladat-végrehajtást. Tekintse át az állapot értékét az **életciklus** oszlopban. A feladat sikeresen befejeződött az összes bérlői adatbázison és a két további, referenciatáblát tartalmazó adatbázison.

1. Keresse meg az SSMS-ben a *contosoconcerthall* adatbázist a *tenants1* kiszolgálón, és kérdezze le a *VenueTypes* táblát annak a megerősítéséhez, hogy a *Motorkerékpár-verseny* és az *Úszóklub* **most már szerepel** az eredménylistán.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Feladat létrehozása a referenciatábla indexének kezeléséhez

Az előző gyakorlathoz hasonlóan, ebben a gyakorlatban létrehoz egy feladatot a referenciatábla elsődleges kulcsához tartozó index újraépítéséhez, amely a rendszergazda gyakori adatbázis-kezelési művelete, miután nagy mennyiségű adat töltődött be a táblába.

Hozzon létre egy feladatot ugyanannak a feladatnak a „system” által tárolt eljárásait használva.

1. Nyissa meg az SSMS-t, és kapcsolódjon a catalog-&lt;WtpUser&gt;.database.windows.net kiszolgálóhoz
1. Nyissa meg a ...\\Tanulási modulok\\Sémakezelés\\.sql fájlt
1. Kattintson a jobb gombbal, válassza a Kapcsolat lehetőséget, és ha még nem kapcsolódott, akkor kapcsolódjon a catalog-&lt;WtpUser&gt;.database.windows.net kiszolgálóhoz
1. Ellenőrizze, hogy kapcsolódik-e a jobaccount adatbázishoz, és nyomja le az F5 billentyűt a parancsprogram futtatásához

* Az sp\_add\_job létrehoz egy „Online Reindex PK\_\_VenueTyp\_\_265E44FD7FD4C885” nevű új feladatot
* Az sp\_add\_jobstep létrehozza a T-SQL-parancsszöveget tartalmazó feladatlépést az index frissítéséhez




## <a name="next-steps"></a>Következő lépések

Ennek az oktatóanyagnak a segítségével megtanulta a következőket:

> [!div class="checklist"]

> * Feladatfiók létrehozása több bérlőben történő lekérdezéshez
> * Az összes bérlői adatbázis adatainak frissítése
> * Index létrehozása a táblához az összes bérlői adatbázisban

[Oktatóanyag alkalmi elemzéshez](sql-database-saas-tutorial-adhoc-analytics.md)


## <a name="additional-resources"></a>További források

* [A Wingtip Tickets Platform (WTP) alkalmazás kezdeti üzembe helyezésére épülő további oktatóanyagok](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* [Kiterjesztett felhőalapú adatbázisok kezelése](sql-database-elastic-jobs-overview.md)
* [Horizontálisan felskálázott felhőalapú adatbázisok létrehozása és kezelése](sql-database-elastic-jobs-create-and-manage.md)
