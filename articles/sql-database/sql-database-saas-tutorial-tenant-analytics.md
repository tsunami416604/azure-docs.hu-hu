---
title: "Több bérlőre kiterjedő analitikai lekérdezések futtatása (Azure SQL Database-t használó SaaS-mintaalkalmazás) | Microsoft Docs"
description: "Több bérlőre kiterjedő analitikai lekérdezések futtatása"
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
ms.openlocfilehash: b512e2f7833be1947ef7674d6e0266879789ac5a
ms.contentlocale: hu-hu
ms.lasthandoff: 05/10/2017


---
# <a name="run-analytics-queries-against-multiple-tenants"></a>Több bérlőre kiterjedő analitikai lekérdezések futtatása

Ebben az oktatóanyagban a katalógusban szereplő összes bérlőre vonatkozó analitikai lekérdezéseket futtat majd. A lekérdezések futtatásához egy rugalmas feladatot fogunk használni. A feladat kigyűjti és a katalóguskiszolgálón létrehozott külön analitikai adatbázisba tölti be az adatokat. Ennek az adatbázisnak a lekérdezésével feltárhatók az összes bérlő nap mint nap használt adatai között rejlő összefüggések. A feladat kimeneteként az eredményt visszaadó lekérdezések alapján létrejön egy tábla a bérlő analitikai adatbázisában.


Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Bérlői analitikai adatbázis létrehozása
> * Ütemezett feladat létrehozása az adatok kinyerésére és az analitikai adatbázis feltöltésére

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A WTP alkalmazás üzembe van helyezve. A kevesebb mint öt perc alatti üzembe helyezéshez lásd: [A WTP SaaS-alkalmazás üzembe helyezése és megismerése](sql-database-saas-tutorial.md)
* Az Azure PowerShell telepítve van. A részletekért lásd: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Telepítve van az SQL Server Management Studio (SSMS) legújabb verziója. [Az SSMS letöltése és telepítése](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

## <a name="tenant-operational-analytics-pattern"></a>Bérlői operatív analitikai mintázat

Az SaaS-alkalmazásokban rejlő nagy lehetőségek egyike a felhőben tárolt részletes bérlői adatok felhasználása. Ezen adatok alapján tájékozódhat az alkalmazása felhasználásáról és működéséről, valamint a bérlőiről. Az adatok segíthetik az alkalmazás és a platform funkcióinak fejlesztését, használhatóságuk javítását és más befektetéseket. Ezeknek az adatoknak egyetlen több bérlős adatbázisban történő elérése könnyű, de nem olyan egyszerű, ha méretezve akár több ezer adatbázis között vannak elosztva. Az adatok elérésének egyik módja a feladat futtatása során kapott lekérdezési eredmények tárolását egy kimeneti adatbázisban és táblában tárolni képes rugalmas feladatok használata.

## <a name="get-the-wingtip-application-scripts"></a>A Wingtip alkalmazásszkriptek beolvasása

A Wingtip Tickets szkriptjei és alkalmazás-forráskódja a [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) GitHub-tárházban érhetők el. A parancsfájlok a [Tanulási modulok mappában](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules) találhatók. Töltse le a **Tanulási modulok** mappát a helyi számítógépére, a mappaszerkezetének megőrzésével.

## <a name="deploy-a-database-for-tenant-analytics-results"></a>A bérlői analitikai eredményeket tároló adatbázis kialakítása

Ehhez az oktatóanyaghoz szükség van egy adatbázisra, amely tárolja a lekérdezéseket tartalmazó, feladatként futó parancsprogramok által visszaadott eredményeket. Hozzunk létre egy tenantanalytics nevű adatbázist erre a célra.

1. Nyissa meg a ...\\Tanulási modulok\\Működési elemzések\\Bérlői analitikák\\*Demo-TenantAnalytics.ps1* fájlt a *PowerShell ISE* alkalmazásban, és állítsa be a következő értékeket:
   * **$DemoScenario** = **2** *Működési elemzés adatbázis telepítése*
1. Az **F5** lenyomásával futtassa le a bérlői analitikai adatbázist létrehozó bemutató szkriptet (amely meghívja a *Deploy-TenantAnalyticsDB.ps1* szkriptet).

## <a name="create-some-data-for-the-demo"></a>Adatok létrehozása a bemutatóhoz

1. Nyissa meg a ...\\Tanulási modulok\\Működési elemzések\\Bérlői analitikák\\*Demo-TenantAnalytics.ps1* fájlt a *PowerShell ISE* alkalmazásban, és állítsa be a következő értékeket:
   * **$DemoScenario** = **1** *Jegyek beszerzése minden helyszínen*
1. Az **F5** lenyomásával futtassa le a szkriptet, és hozza létre a jegyvásárlások naplóját.


## <a name="create-a-scheduled-job-to-retrieve-tenant-analytics-about-ticket-purchases"></a>Ütemezett feladat létrehozása a jegyvásárlásokkal kapcsolatos bérlői analitikák kigyűjtésére

Ez a szkript létrehoz egy feladatot, amely minden bérlőtől beszerzi a jegyvásárlásokkal kapcsolatos adatokat. Ezeket egyetlen táblában összesítve részletes képet kaphat a bérlők körében elterjedt jegyvásárlási mintázatokról.

1. Nyissa meg az SSMS-t, és kapcsolódjon a catalog-\<felhasználó\>.database.windows.net kiszolgálóhoz
1. Nyissa meg a ...\\Tanulási modulok\\Működési elemzések\\Bérlői analitikák\\*TicketPurchasesfromAllTenants.sql* fájlt
1. Módosítsa a \<WtpUser\> felhasználót a WTP alkalmazás telepítésekor az **sp\_add\_target\_group\_member** és az **sp\_add\_jobstep** szkript elején használt felhasználónévvel
1. Kattintson a jobb gombbal, válassza a **Kapcsolat** lehetőséget, és ha még nem kapcsolódott, akkor kapcsolódjon a catalog-\<WtpUser\>.database.windows.net kiszolgálóhoz
1. Ellenőrizze, hogy kapcsolódik-e a **jobaccount** adatbázishoz, és nyomja le az **F5** billentyűt a parancsprogram futtatásához

* Az **sp\_add\_target\_group** létrehozza a *TenantGroup* nevű célcsoportot, most hozzá kell adnunk a céltagokat.
* Az **sp\_add\_target\_group\_member** hozzáad egy *kiszolgáló* céltagtípust, amely feltételezi, hogy a kiszolgálón (ne feledje, ez a bérlői adatbázisokat tartalmazó customer1-&lt;WtpUser&gt; kiszolgáló) belüli összes adatbázist feladat-végrehajtáskor bele kell foglalni a feladatba.
* Az **sp\_add\_job** létrehoz egy új, hetenként ütemezett feladatot „Jegyvásárlások az összes bérlőnél” néven
* **sp\_add\_jobstep** létrehozza a T-SQL-parancsszöveget tartalmazó feladatlépést, amely kinyeri az összes bérlő jegyvásárlási információit és a kapott eredményhalmazt bemásolja egy *AllTicketsPurchasesfromAllTenants* nevű táblába
* A szkript fennmaradó nézetei megjelenítik, hogy léteznek-e az objektumok, és figyelik a feladat-végrehajtást. Az állapot figyeléséhez tekintse át az állapot értékét az **életciklus** oszlopban. A sikeres végrehajtás azt jelenti, hogy a feladat sikeresen befejeződött az összes bérlői adatbázison és a két további, referenciatáblát tartalmazó adatbázison.

A szkript sikeres lefutása az alábbihoz hasonló eredményt mutat:

![eredmények](media/sql-database-saas-tutorial-tenant-analytics/ticket-purchases-job.png)

## <a name="create-a-job-to-retrieve-a-summary-count-of-ticket-purchases-from-all-tenants"></a>Feladat létrehozása az összes bérlő jegyvásárlásainak összesítéséhez

Ez a szkript létrehoz egy feladatot, amely megadja az összes bérlő összes jegyvásárlásának összegét.

1. Nyissa meg az SSMS-t, és kapcsolódjon a *catalog-&lt;felhasználó&gt;.database.windows.net* kiszolgálóhoz
1. Nyissa meg a ...\\Tanulási modulok\\Kiépítés és katalogizálás\\Működési elemzések\\Bérlői analitikák\\*Results-TicketPurchasesfromAllTenants.sql* fájlt
1. Módosítsa a &lt;WtpUser&gt; felhasználót a WTP alkalmazás telepítésekor a szkriptben, az **sp\_add\_jobstep** tárolt alkalmazásban használt felhasználónévvel
1. Kattintson a jobb gombbal, válassza a **Kapcsolat** lehetőséget, és ha még nem kapcsolódott, akkor kapcsolódjon a catalog-\<WtpUser\>.database.windows.net kiszolgálóhoz
1. Ellenőrizze, hogy kapcsolódik-e a **tenantanalytics** adatbázishoz, és nyomja le az **F5** billentyűt a parancsprogram futtatásához

A szkript sikeres lefutása az alábbihoz hasonló eredményt mutat:

![eredmények](media/sql-database-saas-tutorial-tenant-analytics/total-sales.png)



* Az **sp\_add\_job** létrehoz egy új, hetenként ütemezett feladatot „Jegyrendelési eredmények” néven

* Az **sp\_add\_jobstep** létrehozza a T-SQL-parancsszöveget tartalmazó feladatlépést, amely kinyeri az összes bérlő jegyvásárlási információit és a kapott eredményhalmazt bemásolja egy CountofTicketOrders nevű táblába

* A szkript fennmaradó nézetei megjelenítik, hogy léteznek-e az objektumok, és figyelik a feladat-végrehajtást. Az állapot figyeléséhez tekintse át az állapot értékét az **életciklus** oszlopban. A sikeres végrehajtás azt jelenti, hogy a feladat sikeresen befejeződött az összes bérlői adatbázison és a két további, referenciatáblát tartalmazó adatbázison.


## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Bérlői analitikai adatbázis létrehozása
> * Az összes bérlőről analitikai adatokat kinyerő ütemezett feladat létrehozása

Gratulálunk!

## <a name="additional-resources"></a>További források

* [A Wingtip Tickets Platform (WTP) alkalmazás kezdeti üzembe helyezésére épülő további oktatóanyagok](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials)
* [Rugalmas feladatok](sql-database-elastic-jobs-overview.md)
