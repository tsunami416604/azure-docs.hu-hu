---
title: "Azure SQL Database-séma kezelése több-bérlős alkalmazásban | Microsoft Docs"
description: "Több bérlő sémájának kezelése Azure SQL Database-t használó több-bérlős alkalmazásban"
keywords: "sql database-oktatóanyag"
services: sql-database
documentationcenter: 
author: MightyPen
manager: craigg
editor: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.reviewers: billgib
ms.author: genemi
ms.openlocfilehash: 135764a7d89dcf711ff7fe9416850f1af9329479
ms.sourcegitcommit: df4ddc55b42b593f165d56531f591fdb1e689686
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2018
---
# <a name="manage-schema-for-multiple-tenants-in-a-multi-tenant-application-that-uses-azure-sql-database"></a>Az Azure SQL Database egy több-bérlős alkalmazás több bérlő a séma kezelése

Ez az oktatóanyag megvizsgálja a kihívás az adatbázisok egy szoftverfrissítési potenciálisan nagy járműflotta a felhőben szolgáltatott szoftverként (SaaS) alkalmazás karbantartása. Megoldások egy kezelésére kifejlesztett és az alkalmazások élettartama során végrehajtott séma fejlesztései.

Bármely alkalmazás fejlődésének módosítások akkor fordulhat elő, a táblázat oszlopainak vagy más sémamódosítási, illetve a referenciaadatok, vagy a teljesítmény kapcsolódó elemeket. Egy SaaS-alkalmazáshoz ezek a változások kell telepíteni összehangolt módon számos meglévő bérlő adatbázisok között. És a jövőbeli bérlői adatbázisok, amelyek nem kerülnek be az alkalmazás ezeket a módosításokat kell szerepelnie. Ezért a módosításokat is kell kell építeni a folyamat, amely az új adatbázisok kiépítését.

#### <a name="two-scenarios"></a>Két esetben

Ez az oktatóanyag ismerteti, a következő két példa:
- Hivatkozás adatok frissítéseket telepítheti az összes bérlőre vonatkozó.
- A táblázat a referencia-adatokat tartalmazó index retuning.

A [rugalmas feladatok](sql-database-elastic-jobs-overview.md) az Azure SQL-adatbázis szolgáltatása hajthatók végre ezeket a műveleteket a bérlő az adatbázisok közötti. A feladatok is működnek az arany sablon bérlői adatbázis. Ez a sablon új adatbázisokat kiosztásakor szolgál.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Feladat-fiók létrehozása.
> * Több bérlő átfogó lekérdezése.
> * Frissíti az összes bérlői adatbázis adatait.
> * Egy tábla összes bérlői adatbázisokban indexet létrehozni.

## <a name="prerequisites"></a>Előfeltételek

- A Wingtip jegyek alkalmazás már telepítve kell:
    - Útmutatásért lásd: első oktatóanyaga, amely bemutatja a *Wingtip jegyek* SaaS több-bérlős adatbázis alkalmazást:<br />[Központi telepítése, és vizsgálja meg a szilánkos több-bérlős alkalmazás által használt Azure SQL Database](saas-multitenantdb-get-started-deploy.md).
        - A telepítés folyamata a kevesebb mint öt perc alatt fut.
    - Rendelkeznie kell a *szilánkos több-bérlős* Wingtip telepített verzióját. A verziók *önálló* és *bérlőnként adatbázis* nem támogatják a jelen oktatóanyag.

- SQL Server Management Studio (SSMS) legfrissebb telepítve kell lennie. [Töltse le és telepítse az SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

- Az Azure PowerShell telepítve kell lennie. További információkért lásd: [Ismerkedés az Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

> [!NOTE]
> Ez az oktatóanyag az Azure SQL Database szolgáltatás egy korlátozott előzetes verziójú funkciókat használ ([rugalmas adatbázis-feladatok](sql-database-elastic-database-client-library.md)). Ha ez az oktatóanyag elvégzéséhez, adja meg az előfizetés-Azonosítóval történő  *SaaSFeedback@microsoft.com*  témát = a rugalmas feladatok megtekintése. Miután megkapta a megerősítést az előfizetésének engedélyezéséről, [töltse le és telepítse a legújabb kiadás előtti feladatok parancsmagjait](https://github.com/jaredmoo/azure-powershell/releases). Ez az előnézet korlátozva, így forduljon  *SaaSFeedback@microsoft.com*  kapcsolatos kérdésekre, vagy a támogatási szolgálathoz.

## <a name="introduction-to-saas-schema-management-patterns"></a>Bevezetés a Szolgáltatottszoftver-séma felügyeleti minták

Horizontálisan skálázott több-bérlős adatbázismodell a mintában használt lehetővé teszi, hogy a bérlők számára az adatbázis tartalmaz egy vagy több tenant. Ez a minta ismerteti a lehetőségeket kínál a több-bérlős és egy bérlői adatbázisok, engedélyezése vegyesen használni egy *hibrid* bérlői felügyeleti modellt. Ezek az adatbázisok kezelése bonyolult. [Rugalmas feladatok](sql-database-elastic-jobs-overview.md) elősegíti az SQL-adatréteg felügyeletét és kezelését. Feladatok lehetővé teszik a biztonságosan és megbízhatóan Transact-SQL-parancsprogramokat futtató feladatok bérlői adatbázisok csoportja ellen. A feladatok függetlenek felhasználói interakcióktól vagy bemenettől. Ez a módszer használható központi telepítése a módosítások séma vagy közös referenciaadatok teljes egyetlen bérlő számára az alkalmazásban. Rugalmas feladat karbantartása arany sablon az adatbázis másolatát is használható. A sablon használatával hozzon létre új bérlők, mindig a legújabb séma biztosítása és referenciaadatok használatban van.

![képernyő](media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>Az Elastic Jobs korlátozott előzetes verziója

Rugalmas feladat, amelyik most már egy beépített szolgáltatás Azure SQL adatbázis új verziója van telepítve. Integrált által jelenleg olyan nincs további szolgáltatásokért és összetevőkért igényel. Ez Elastic Jobs-nak ez az új verziója jelenleg korlátozott előzetes verzió. A korlátozott előzetes jelenleg PowerShell feladat fiókokat hozhat létre, és T-SQL, a feladatok létrehozásához és kezeléséhez.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Az alkalmazás forráskódjához Wingtip jegyek SaaS több-bérlős adatbázis és a parancsfájlok

A Wingtip jegyek SaaS több-bérlős adatbázis parancsfájlok és az alkalmazás forráskódjához érhetők el a [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) Github tárházából. Tekintse meg a [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) töltse le és feloldása a Wingtip jegyek Szolgáltatottszoftver-parancsfájlok lépéseit. 

## <a name="create-a-job-account-database-and-new-job-account"></a>Feladatfiók-adatbázis és új feladatfiók létrehozása

Ez az oktatóanyag a feladat fiók adatbázis és a feladat-fiók létrehozása a PowerShell használatát igényli. Például az MSDB adatbázis SQL-ügynök által használt a rugalmas feladatok segítségével Azure SQL-adatbázis tárolja a definíciók, feladat állapota és előzményei. A feladat-fiók létrehozása után hozzon létre, és figyelheti a feladat azonnal.

1. A **PowerShell ISE**, nyissa meg *... \\Tanulási modulok\\séma felügyeleti\\bemutató-SchemaManagement.ps1*.
2. A szkriptek futtatásához nyomja le az **F5** billentyűt.

A *bemutató-SchemaManagement.ps1* parancsfájl-hívások a *telepítés-SchemaManagement.ps1* réteg létrehozása a parancsfájl *S2* nevű adatbázis **jobaccount** a katalógus-kiszolgálón. A parancsfájl létrehozza a feladat fiókot, a jobaccount adatbázis paraméterként átadni a feladat fiók létrehozási híváshoz.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Feladat létrehozása új referenciaadatok bevezetéséhez az összes bérlőn

#### <a name="prepare"></a>Előkészítés

Az egyes bérlők adatbázisok helyszínére típusainak készletét tartalmazza a **VenueTypes** tábla. A helyszínére típusok határozzák meg, hogy milyen típusú eseményeket, amelyek egy helyszínére, üzemeltetett. Ebben a gyakorlatban frissítést telepít két további helyszínére típusok hozzáadása az összes adatbázisra: *Motorkerékpárja Racing* és *úszó Club*. Ezek a helyszíntípusok megfelelnek a bérlő eseményalkalmazásában látható háttérképnek.

Központi telepítéséhez az új referenciaadatok vegye figyelembe a már meglévő helyszínére-típusok száma, amelyek esetleg 10. A Wingtip webes felhasználói felülete az alábbi hivatkozásra kattintva, majd hajtsa végre a megfelelő Megjegyzés a **helyszínére típus** legördülő menüben:
- http://events.Wingtip-MT.<USER>. trafficmanager.net

Most már tudja száma eredeti helyszínére-típusok száma. Különösen, vegye figyelembe, hogy sem *Motorkerékpárja Racing* sem *úszó Club* még léteznek.

#### <a name="steps"></a>Lépések

Most frissíteni feladatot hoz létre a **VenueTypes** tábla minden egyes bérlők adatbázis, a két új helyszínére típus hozzáadásával.

Hozzon létre egy új feladatot, hogy a létrehozott feladatok rendszerszintű tárolt eljárásokra készletének használata a *jobaccount* adatbázis. Az eljárások a feladat-fiók létrehozásakor lettek létrehozva.

1. Az SSMS, a bérlő kiszolgálóhoz csatlakozni: tenants1-mt -\<felhasználói\>. database.windows.net

2. Keresse meg a *tenants1* adatbázisról a a *tenants1-mt -\<felhasználói\>. database.windows.net* kiszolgáló.

3. Lekérdezés a *VenueTypes* annak ellenőrzésére, hogy a tábla *Motorkerékpárja Racing* és *úszó Club* még nem az eredménylistában.

4. A kiszolgáló, amely csatlakozni *katalógus-mt -\<felhasználói\>. database.windows.net*.

5. Csatlakozás a *jobaccount* a kiszolgáló az adatbázis.

6. Az SSMS, nyissa meg a fájl *... \\Tanulási modulok\\séma felügyeleti\\DeployReferenceData.sql*.

7. Az utasítás módosítása: beállítása @User = &lt;felhasználói&gt; és a Wingtip jegyek SaaS több-bérlős adatbázis-alkalmazás telepítésekor használt felhasználói helyettesítésére.

8. A szkriptek futtatásához nyomja le az **F5** billentyűt.

#### <a name="observe"></a>Megfigyelés

Figyelje meg a következő elemeket a *DeployReferenceData.sql* parancsfájlt:

- **SP\_hozzáadása\_cél\_csoport** hoz létre a célcsoport neve *DemoServerGroup*, és a célként megadott tagokat vesz fel a csoportba.

- **SP\_hozzáadása\_cél\_csoport\_tag** a következő elemek hozzáadása:
    - A *server* céloz tag típusa.
        - Ez a *tenants1-mt -&lt;felhasználói&gt;*  kiszolgálót, amely tartalmazza a bérlők adatbázisok.
        - Így a kiszolgálón lévő összes adatbázis szerepelnek a feladat végrehajtásakor a szabály a feladatot.
    - A *adatbázis* az arany adatbázis tag céltípust (*basetenantdb*), amely a található *katalógus-mt -&lt;felhasználói&gt;*  kiszolgáló,
    - A *adatbázis* céloz tagtípus tartalmazza a *adhocreporting* egy újabb oktatóanyagban használt adatbázist.

- **SP\_hozzáadása\_feladat** létrehoz egy feladatot nevű *hivatkozás adatok telepítési*.

- **SP\_hozzáadása\_feladatlépés használja** hoz létre a T-SQL parancs szövege frissíteni a referenciatábla VenueTypes tartalmazó feladatlépéshez.

- A szkript fennmaradó nézetei megjelenítik, hogy léteznek-e az objektumok, és figyelik a feladat-végrehajtást. Tekintse át a értéket használja ezeket a lekérdezéseket a **életciklus** oszlop határozza meg, ha a feladat sikeresen befejeződött. A feladat frissíti a bérlők adatbázist, és frissíti a referenciatábla tartalmazó két további adatbázisok.

A szolgáltatáshoz az SSMS, keresse meg azt a bérlő adatbázis a a *tenants1-mt -&lt;felhasználói&gt;*  kiszolgáló. Lekérdezés a *VenueTypes* annak ellenőrzésére, hogy a tábla *Motorkerékpárja Racing* és *úszó Club* most hozzáadódnak a táblában. A számuk helyszínére típusú két kell növekedett.

## <a name="create-a-job-to-manage-the-reference-table-index"></a>Feladat létrehozása a referenciatábla indexének kezeléséhez

Ebben a gyakorlatban az előző gyakorlat hasonlít. Ebben a gyakorlatban létrehoz egy feladatot, hogy a referencia-tábla elsődleges kulcs indexe. Az index rebuild, amelyek a rendszergazda egy nagy méretű adatok betöltése után egy táblába tipikus adatbázis felügyeleti művelet teljesítmény javítása érdekében.

1. Az SSMS, csatlakozzon a *jobaccount* adatbázis *katalógus-mt -&lt;felhasználói&gt;. database.windows.net* kiszolgáló.

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
> - A több bérlő átfogó lekérdezése feladat-fiók létrehozása.
> - Frissíti az összes bérlői adatbázis adatait.
> - Egy tábla összes bérlői adatbázisokban indexet létrehozni.

Ezt követően próbálja meg a [alkalmi jelentéskészítési oktatóanyag](saas-multitenantdb-adhoc-reporting.md).

