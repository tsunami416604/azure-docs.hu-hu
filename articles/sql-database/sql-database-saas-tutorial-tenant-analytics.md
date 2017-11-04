---
title: "Elemzési lekérdezések futtatása több Azure SQL-adatbázisban | Microsoft Docs"
description: "Adatok kinyerése a bérlő adatbázis kapcsolat nélküli elemzéshez analytics adatbázisba"
keywords: "sql database-oktatóanyag"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: billgib; sstein
ms.openlocfilehash: 4a96efb15268c56e3625832b0b4d6dd8f6a78614
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="extract-data-from-tenant-databases-into-an-analytics-database-for-offline-analysis"></a>Adatok kinyerése a bérlő adatbázis kapcsolat nélküli elemzéshez analytics adatbázisba

Ebben az oktatóanyagban egy rugalmas feladat minden egyes bérlői adatbázisához lekérdezéseinek futtatásához használja. A feladat jegy értékesítési adatait kinyeri, és betölti az elemzési adatbázis (vagy az adatraktár) elemzés céljából. Az elemzési adatbázis majd le kell kérdezni insights kibontani a napi működésiadat egyetlen bérlő számára.


Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Bérlői analitikai adatbázis létrehozása
> * Ütemezett feladat létrehozása az adatok kinyerésére és az analitikai adatbázis feltöltésére

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip SaaS-alkalmazás telepítve van. Kevesebb mint öt perc alatt telepítéséhez lásd: [központi telepítése és vizsgálja meg a Wingtip SaaS-alkalmazáshoz](sql-database-saas-tutorial.md)
* Az Azure PowerShell telepítve van. A részletekért lásd: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Telepítve van az SQL Server Management Studio (SSMS) legújabb verziója. [Az SSMS letöltése és telepítése](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

## <a name="tenant-operational-analytics-pattern"></a>Bérlői operatív analitikai mintázat

Az SaaS-alkalmazásokban rejlő nagy lehetőségek egyike a felhőben tárolt részletes bérlői adatok felhasználása. Ezen adatok alapján tájékozódhat az alkalmazása felhasználásáról és működéséről, valamint a bérlőiről. Az adatok segíthetik az alkalmazás és a platform funkcióinak fejlesztését, használhatóságuk javítását és más befektetéseket. Ezeknek az adatoknak egyetlen több bérlős adatbázisban történő elérése könnyű, de nem olyan egyszerű, ha méretezve akár több ezer adatbázis között vannak elosztva. Az adatok elérésének egyik módja a feladat futtatása során kapott lekérdezési eredmények tárolását egy kimeneti adatbázisban és táblában tárolni képes rugalmas feladatok használata.

## <a name="get-the-wingtip-application-scripts"></a>A Wingtip alkalmazásszkriptek beolvasása

A Wingtip Szolgáltatottszoftver-parancsfájlok és az alkalmazás forráskódjához érhetők el a [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) github-tárház. [Töltse le a Wingtip Szolgáltatottszoftver-parancsfájlok lépéseket](sql-database-wtp-overview.md#download-and-unblock-the-wingtip-saas-scripts).

## <a name="deploy-a-database-for-tenant-analytics-results"></a>A bérlői analitikai eredményeket tároló adatbázis kialakítása

Ehhez az oktatóanyaghoz szükség van egy adatbázisra, amely tárolja a lekérdezéseket tartalmazó, feladatként futó parancsprogramok által visszaadott eredményeket. Hozzunk létre egy tenantanalytics nevű adatbázist erre a célra.

1. Nyissa meg a ...\\Tanulási modulok\\Működési elemzések\\Bérlői analitikák\\*Demo-TenantAnalytics.ps1* fájlt a *PowerShell ISE* alkalmazásban, és állítsa be a következő értékeket:
   * **$DemoScenario** = **2***Működési elemzés adatbázis telepítése*
1. Az **F5** lenyomásával futtassa le a bérlői analitikai adatbázist létrehozó bemutató szkriptet (amely meghívja a *Deploy-TenantAnalyticsDB.ps1* szkriptet).

## <a name="create-some-data-for-the-demo"></a>Adatok létrehozása a bemutatóhoz

1. Nyissa meg a ...\\Tanulási modulok\\Működési elemzések\\Bérlői analitikák\\*Demo-TenantAnalytics.ps1* fájlt a *PowerShell ISE* alkalmazásban, és állítsa be a következő értékeket:
   * **$DemoScenario** = **1***Jegyek beszerzése minden helyszínen*
1. Az **F5** lenyomásával futtassa le a szkriptet, és hozza létre a jegyvásárlások naplóját.


## <a name="create-a-scheduled-job-to-retrieve-tenant-analytics-about-ticket-purchases"></a>Ütemezett feladat létrehozása a jegyvásárlásokkal kapcsolatos bérlői analitikák kigyűjtésére

Ez a szkript létrehoz egy feladatot, amely minden bérlőtől beszerzi a jegyvásárlásokkal kapcsolatos adatokat. Ezeket egyetlen táblában összesítve részletes képet kaphat a bérlők körében elterjedt jegyvásárlási mintázatokról.

1. Nyissa meg az SSMS-t, és kapcsolódjon a catalog-&lt;felhasználó&gt;.database.windows.net kiszolgálóhoz
1. Nyissa meg a ...\\Tanulási modulok\\Működési elemzések\\Bérlői analitikák\\*TicketPurchasesfromAllTenants.sql* fájlt
1. Módosítsa &lt;felhasználói&gt;, használja a Wingtip SaaS-alkalmazás a parancsfájl elején telepítésekor használt felhasználónevét **sp\_hozzáadása\_cél\_csoport\_tag** és **sp\_hozzáadása\_feladatlépés használja**
1. Kattintson a jobb gombbal, válassza ki **kapcsolat**, és kapcsolódjon a katalógus -&lt;felhasználói&gt;. database.windows.net kiszolgáló, ha még nincs csatlakoztatva
1. Ellenőrizze, hogy kapcsolódik-e a **jobaccount** adatbázishoz, és nyomja le az **F5** billentyűt a parancsprogram futtatásához

* Az **sp\_add\_target\_group** létrehozza a *TenantGroup* nevű célcsoportot, most hozzá kell adnunk a céltagokat.
* **SP\_hozzáadása\_cél\_csoport\_tag** ad hozzá egy *server* céltípust tag, amely úgy ítéli meg, hogy a kiszolgálón belüli összes adatbázis (Megjegyzés: Ez a customer1 -&lt;felhasználói&gt; a bérlő adatbázisokat tartalmazó kiszolgáló) időpontban feladat végrehajtása a feladat kell foglalni.
* Az **sp\_add\_job** létrehoz egy új, hetenként ütemezett feladatot „Jegyvásárlások az összes bérlőnél” néven
* **sp\_add\_jobstep** létrehozza a T-SQL-parancsszöveget tartalmazó feladatlépést, amely kinyeri az összes bérlő jegyvásárlási információit és a kapott eredményhalmazt bemásolja egy *AllTicketsPurchasesfromAllTenants* nevű táblába
* A szkript fennmaradó nézetei megjelenítik, hogy léteznek-e az objektumok, és figyelik a feladat-végrehajtást. Az állapot figyeléséhez tekintse át az állapot értékét az **életciklus** oszlopban. A sikeres végrehajtás azt jelenti, hogy a feladat sikeresen befejeződött az összes bérlői adatbázison és a két további, referenciatáblát tartalmazó adatbázison.

A szkript sikeres lefutása az alábbihoz hasonló eredményt mutat:

![eredmények](media/sql-database-saas-tutorial-tenant-analytics/ticket-purchases-job.png)

## <a name="create-a-job-to-retrieve-a-summary-count-of-ticket-purchases-from-all-tenants"></a>Feladat létrehozása az összes bérlő jegyvásárlásainak összesítéséhez

Ez a szkript létrehoz egy feladatot, amely megadja az összes bérlő összes jegyvásárlásának összegét.

1. Nyissa meg az SSMS-t, és kapcsolódjon a *catalog-&lt;felhasználó&gt;.database.windows.net* kiszolgálóhoz
1. Nyissa meg a ...\\Tanulási modulok\\Kiépítés és katalogizálás\\Működési elemzések\\Bérlői analitikák\\*Results-TicketPurchasesfromAllTenants.sql* fájlt
1. Módosítsa &lt;felhasználói&gt;, a Wingtip Szolgáltatottszoftver-alkalmazás a parancsfájl a telepítésekor használt felhasználónév használata a **sp\_hozzáadása\_feladatlépés használja** tárolt eljárás
1. Kattintson a jobb gombbal, válassza ki **kapcsolat**, és kapcsolódjon a katalógus -&lt;felhasználói&gt;. database.windows.net kiszolgáló, ha még nincs csatlakoztatva
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

* További [oktatóprogramot kínál, amelyek a Wingtip SaaS-alkalmazás épül](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [Rugalmas feladatok](sql-database-elastic-jobs-overview.md)
