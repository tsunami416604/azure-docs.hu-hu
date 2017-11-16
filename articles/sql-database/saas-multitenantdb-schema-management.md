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
ms.date: 11/14/2017
ms.author: billgib
ms.openlocfilehash: 346177be29ec196464f4f441858222ac5d5eb8c3
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="manage-schema-for-multiple-tenants-in-a-multi-tenant-application-that-uses-azure-sql-database"></a>Az Azure SQL Database egy több-bérlős alkalmazás több bérlő a séma kezelése

A [első Wingtip jegyek SaaS több-bérlős adatbázis-oktatóanyag](saas-multitenantdb-get-started-deploy.md) bemutatja, hogyan az alkalmazás egy több-bérlős szilánkos adatbázis létesítéséhez és regisztrálja a katalógusban. Bármely alkalmazás, például a Wingtip jegyek SaaS-alkalmazás verzióinformációk lesz, és esetenként esetén a módosítások adatbázisba. A módosítások a következők lehetnek: új vagy módosított séma, új vagy módosított referenciaadatok és rutin adatbázis-karbantartási feladatok az alkalmazás optimális teljesítményének biztosítása érdekében. A SaaS-alkalmazásokkal ezeket a módosításokat koordinált módon kell üzembe helyezni valószínűleg nagyszámú bérlői adatbázison. A módosítások lehet a jövőben bérlői adatbázisok van szükségük a kiépítési folyamat szóló.

Ez az oktatóanyag két forgatókönyvet ismertet: referenciaadat-frissítések üzembe helyezése az összes bérlőhöz, és index visszaadása a referenciaadatokat tartalmazó táblázathoz. A [rugalmas feladatok](sql-database-elastic-jobs-overview.md) szolgáltatás hajthatók végre ezeket a műveleteket között bérlői adatbázisra, és a *arany* bérlői adatbázis, amely sablonként szolgál, az új adatbázisokat.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]

> * Feladat-fiók létrehozása
> * Több bérlő átfogó lekérdezése
> * Az összes bérlői adatbázis adatainak frissítése
> * Index létrehozása a táblához az összes bérlői adatbázisban


Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip jegyek SaaS több-bérlős adatbázis alkalmazás központi telepítése. Kevesebb mint öt perc alatt telepítéséhez lásd: [központi telepítése és vizsgálja meg a Wingtip jegyek SaaS több-bérlős adatbázis-alkalmazás](saas-multitenantdb-get-started-deploy.md)
* Az Azure PowerShell telepítve van. A részletekért lásd: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Telepítve van az SQL Server Management Studio (SSMS) legújabb verziója. [Az SSMS letöltése és telepítése](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

> [!NOTE]
> *Ez az oktatóanyag az SQL Database szolgáltatás egy korlátozott előzetes verziójának funkcióit használja (Elastic Database-feladatok). Ha szeretné elvégezni ezt az oktatóanyagot, küldje el az előfizetés-azonosítóját az SaaSFeedback@microsoft.com címre, tárgyként megadva a subject=Elastic Jobs Preview szöveget. Miután megkapta a megerősítést az előfizetésének engedélyezéséről, [töltse le és telepítse a legújabb kiadás előtti feladatok parancsmagjait](https://github.com/jaredmoo/azure-powershell/releases). Ez az előnézet korlátozva, így forduljon SaaSFeedback@microsoft.com kapcsolatos kérdésekre, vagy a támogatási szolgálathoz.*


## <a name="introduction-to-saas-schema-management-patterns"></a>Az SaaS-sémakezelési minták bemutatása

Horizontálisan skálázott több-bérlős adatbázismodell a mintában használt lehetővé teszi, hogy a bérlők számára az adatbázis tartalmazza a bérlők tetszőleges számú. Ez a minta ismerteti a lehetőségeket kínál a több-bérlős és a bérlői egyetlen adatbázisok, a "hibrid" bérlői kezelési modell engedélyezése vegyesen használni. Karbantartása ezeket az adatbázisokat kezelése pedig összetett. [Rugalmas feladatok](sql-database-elastic-jobs-overview.md) elősegíti az SQL-adatréteg felügyeletét és kezelését. A feladatok lehetővé teszik a feladatok (T-SQL-szkriptek) adatbáziscsoportokon történő, felhasználói interakciótól vagy beviteltől független biztonságos és megbízható futtatását. Ez a módszer használható a séma és a közös referenciaadat-változások üzembe helyezésére egy alkalmazás összes bérlőjében. Az Elastic Jobs feladatok az új bérlők létrehozására használt adatbázis *arany* másolatának karbantartására is használhatók, hogy mindig a legújabb sémával és referenciaadatokkal rendelkezzen.

![képernyő](media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>Az Elastic Jobs korlátozott előzetes verziója

Megjelent az Elastic Jobs új verziója, amely most az Azure SQL Database integrált funkciója (nincs szükség hozzá további szolgáltatásokra vagy összetevőkre). Ez Elastic Jobs-nak ez az új verziója jelenleg korlátozott előzetes verzió. Ez a korlátozott előzetes verzió jelenleg a PowerShellt támogatja feladatfiókok létrehozásához és a T-SQL-t a feladatok létrehozásához és kezeléséhez.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-scripts"></a>A Wingtip jegyek SaaS több-bérlős adatbázis alkalmazás parancsfájlok beolvasása

A Wingtip jegyek SaaS több-bérlős adatbázis parancsfájlok és az alkalmazás forráskódjához érhetők el a [WingtipTicketsSaaS-MultiTenantDB](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub-tárházban. <!-- [Steps to download the Wingtip Tickets SaaS Multi-tenant Database scripts](saas-multitenantdb-wingtip-app-guidance-tips.md#download-and-unblock-the-wingtip-saas-scripts)-->

## <a name="create-a-job-account-database-and-new-job-account"></a>Feladatfiók-adatbázis és új feladatfiók létrehozása

Ennek az oktatóanyagnak a használatához a PowerShell-lel létre kell hoznia a feladatfiók-adatbázis és a feladatfiókot. Az Elastic Jobs az MSDB-hez és a SQL-ügynökhöz hasonlóan az Azure SQL Database-t használja a feladatdefiníciók, feladatállapotok és -előzmények tárolására. A feladatfiók létrehozása után azonnal létrehozhatja és figyelheti a feladatokat.

1. A **PowerShell ISE**, nyissa meg *... \\Tanulási modulok\\séma felügyeleti\\bemutató-SchemaManagement.ps1*.
1. A szkript futtatásához nyomja le az **F5** billentyűt.

A *Demo-SchemaManagement.ps1* szkript a *Deploy-SchemaManagement.ps1* szkriptet hívja a **jobaccount** nevű *S2* adatbázis katalóguskiszolgálón történő létrehozásához. Ezután létrehozza a feladatfiókot, paraméterként átadva a feladatfiók nevű adatbázist a feladatfiók-létrehozási hívásnak.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Feladat létrehozása új referenciaadatok bevezetéséhez az összes bérlőn

Az egyes bérlők adatbázisok helyszínére típusok készletét tartalmazza a tábla **VenueTypes** , megadhatja, hogy az eseményeket, amelyek egy helyszínére, üzemeltetett típusú. Ebben a gyakorlatban frissítést telepít két további helyszínére típusok hozzáadása az adatbázishoz: *Motorkerékpárja Racing* és *úszó Club*. Ezek a helyszíntípusok megfelelnek a bérlő eseményalkalmazásában látható háttérképnek.

Kattintson a Helyszíntípus legördülő menüre, és ellenőrizze, hogy csak 10 helyszíntípus-lehetőség legyen elérhető, és hogy a „Motorkerékpár-verseny” és az „Úszóklub” ne szerepeljen a listán.

Most hozzon létre egy feladatot, amely frissíti a **VenueTypes** az bérlők adatbázis(ok) tábla, és adja hozzá az új helyszínére típusokat.

Új feladat létrehozásához a feladatfiók létrehozásakor a feladatfiók adatbázisban létrehozott feladatkészletek rendszer által tárolt folyamatait használjuk.

1. Az SSMS, a bérlő kiszolgálóhoz csatlakozni: tenants1-mt -\<felhasználói\>. database.windows.net
2. Keresse meg a *tenants1* adatbázisról a a *tenants1-mt -\<felhasználói\>. database.windows.net* kiszolgáló és a lekérdezés a *VenueTypes* tábla a Ellenőrizze, hogy *Motorkerékpárja Racing* és *úszó Club* vannak **nem** az eredménylistában.
3. A kiszolgáló csatlakozni: katalógus-mt -\<felhasználói\>. database.windows.net
4. A kiszolgáló a jobaccount adatbázishoz csatlakozni.
5. A szolgáltatáshoz az SSMS nyissa meg a fájlt... \\Tanulási modulok\\séma felügyeleti\\DeployReferenceData.sql
6. Az utasítás módosítása: beállítása @User = &lt;felhasználói&gt; és a Wingtip jegyek SaaS több-bérlős adatbázis-alkalmazás telepítésekor használt felhasználói helyettesítésére.
7. A szkript futtatásához nyomja le az **F5** billentyűt.

    * **SP\_hozzáadása\_cél\_csoport** hoz létre a célcsoport neve DemoServerGroup, most cél tagok hozzáadása a csoporthoz.
    * **SP\_hozzáadása\_cél\_csoport\_tag** ad hozzá egy *kiszolgáló* céloz tag típusa, amely úgy ítéli meg, hogy a kiszolgálón belüli összes adatbázis (vegye figyelembe, ez a tenants1 - mt - &lt;felhasználói&gt; a bérlők adatbázist tartalmazó kiszolgáló) a feladat végrehajtása a feladat szereplő időpontjában egy *adatbázis* céloz tag típusa, az "arany" adatbázis (basetenantdb) lévő katalógus-mt -&lt;felhasználói&gt; kiszolgáló, és végül a *adatbázis* tag céltípust a adhocreporting adatbázis, amely használatban van egy újabb útmutató tartalmazza.
    * **SP\_hozzáadása\_feladat** létrehoz egy feladatot "Hivatkozási adatokat telepítés" nevezik.
    * **SP\_hozzáadása\_feladatlépés használja** hoz létre a T-SQL parancs szövege frissíteni a referenciatábla VenueTypes tartalmazó feladatlépéshez.
    * A szkript fennmaradó nézetei megjelenítik, hogy léteznek-e az objektumok, és figyelik a feladat-végrehajtást. Tekintse át a értéket használja ezeket a lekérdezéseket a **életciklus** határozza meg, ha a feladat sikeresen befejeződött a bérlők adatbázis és a két további adatbázis, a referenciatábla tartalmazó oszlop.

1. A szolgáltatáshoz az SSMS, keresse meg azt a bérlő adatbázis a a *tenants1-mt -&lt;felhasználói&gt;*  kiszolgáló és a lekérdezés a *VenueTypes* annak ellenőrzésére, hogy a tábla *Motorkerékpárja Racing* és *úszó Club* most már **hozzáadott* táblához.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Feladat létrehozása a referenciatábla indexének kezeléséhez

Az előző gyakorlathoz hasonlóan, ebben a gyakorlatban létrehoz egy feladatot a referenciatábla elsődleges kulcsához tartozó index újraépítéséhez, amely a rendszergazda gyakori adatbázis-kezelési művelete, miután nagy mennyiségű adat töltődött be a táblába.


1. A szolgáltatáshoz az SSMS, csatlakozzon a katalógusban jobaccount adatbázis-mt -&lt;felhasználói&gt;. database.windows.net kiszolgáló.
2. Az SSMS nyissa meg a... \\Tanulási modulok\\séma felügyeleti\\OnlineReindex.sql.
3. Nyomja le az **F5** a parancsfájl futtatása

    * **SP\_hozzáadása\_feladat** létrehoz egy új feladatot nevű "Online ismételt indexelése PK\_\_VenueTyp\_\_265E44FD7FD4C885".
    * **SP\_hozzáadása\_feladatlépés használja** hoz létre az index frissítésének T-SQL-parancs szöveget tartalmazó feladatlépéshez.
    * A parancsfájl-figyelő feladat végrehajtása a fennmaradó nézeteket. Tekintse át a értéket használja ezeket a lekérdezéseket a **életciklus** oszlop határozza meg, ha a feladat sikeresen befejeződött a cél-csoport minden tagjára.

## <a name="next-steps"></a>Következő lépések

Ennek az oktatóanyagnak a segítségével megtanulta a következőket:

> [!div class="checklist"]

> * Feladatfiók létrehozása több bérlőben történő lekérdezéshez
> * Az összes bérlői adatbázis adatainak frissítése
> * Index létrehozása a táblához az összes bérlői adatbázisban

[Oktatóanyag alkalmi elemzéshez](saas-multitenantdb-adhoc-reporting.md)


## <a name="additional-resources"></a>További források

<!--* Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Kiterjesztett felhőalapú adatbázisok kezelése](sql-database-elastic-jobs-overview.md)
* [Horizontálisan felskálázott felhőalapú adatbázisok létrehozása és kezelése](sql-database-elastic-jobs-create-and-manage.md)
