---
title: Azure SQL Database-séma kezelése több-bérlős alkalmazásban | Microsoft Docs
description: Több bérlő sémájának kezelése Azure SQL Database-t használó több-bérlős alkalmazásban
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib
manager: craigg
ms.date: 09/19/2018
ms.openlocfilehash: 14183475fcca0e12c56f009f105e77aaf11b0c98
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53315214"
---
# <a name="manage-schema-in-a-saas-application-that-uses-sharded-multi-tenant-sql-databases"></a>Egy több-bérlős SQL szilánkokra osztott adatbázisok használó SaaS-alkalmazásban séma kezelése

Ez az oktatóanyag a legnagyobb kihívás a szoftver (saas biztosított) alkalmazás-adatbázisok egy járműflotta karbantartása megvizsgálja. Megoldások a sémamódosítások ki az adatbázisok repülőkről szellőztető találja meg.

Mint minden alkalmazás a Wingtip Tickets SaaS-alkalmazás idővel fejlődik, és az adatbázis-módosítások szükségesek. Változások a hatással van a séma vagy a referencia-adatok, vagy adatbázis-karbantartási feladatokat a alkalmazni. A mintában a bérlői adatbázis SaaS-alkalmazásokkal a módosítások valószínűleg nagyszámú bérlői adatbázison keresztül kell koordinált. Ezenkívül ezek a változások kell tartalmaznia az adatbázisba a kiépítési folyamat végrehajtásával biztosítható szerepelnek az új adatbázisok azok létrehozásakor.

#### <a name="two-scenarios"></a>Két forgatókönyv

Ez az oktatóanyag bemutatja, a következő két esetben:
- Frissítések telepítése egyetlen bérlő számára.
- A tábla a referenciaadatokat tartalmazó index újraépítése.

A [Elastic Jobs](sql-database-elastic-jobs-overview.md) az Azure SQL Database szolgáltatás hajthatók végre ezeket a műveleteket a több bérlős adatbázisban. A feladatok is működnek a "sablon" bérlői adatbázis. A Wingtip Tickets mintaalkalmazás Ez a sablon az adatbázis új bérlői adatbázis kiépítése másolja.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Hozzon létre egy feladatot az ügynök.
> * A T-SQL-lekérdezést végrehajtani a több bérlős adatbázisok.
> * Az összes bérlői adatbázis referenciaadatok frissítése.
> * Index létrehozása a táblához az összes bérlői adatbázison.

## <a name="prerequisites"></a>Előfeltételek

- A Wingtip Tickets database több-bérlős alkalmazás már telepítve kell:
    - Útmutatásért lásd: az első oktatóanyaga, amely a Wingtip Tickets SaaS database több-bérlős alkalmazást mutatja be:<br />[Üzembe helyezése és megismerése, hogy az Azure SQL Database több-bérlős alkalmazás horizontálisan skálázott](saas-multitenantdb-get-started-deploy.md).
        - Az üzembe helyezési folyamatnak a kevesebb mint öt perc alatt fut.
    - Rendelkeznie kell a *horizontálisan skálázott több-bérlős* Wingtip telepített verzióját. A verziók *önálló* és *bérlőnkénti adatbázis* nem támogatják az ebben az oktatóanyagban.

- A legújabb verziót az SQL Server Management Studio (SSMS) telepítve kell lennie. [Töltse le és telepítse az SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

- Az Azure PowerShell telepítve kell lennie. További információkért lásd: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps).

> [!NOTE]
> Ez az oktatóanyag az Azure SQL Database szolgáltatás, amely egy korlátozott előzetes verzió funkcióit használja ([rugalmas adatbázis-feladatok](sql-database-elastic-database-client-library.md)). Ha szeretné elvégezni ezt az oktatóanyagot, adja meg az előfizetés-Azonosítóját az *SaaSFeedback@microsoft.com* a subject = Elastic Jobs Preview szöveget. Miután megkapta a megerősítést az előfizetésének engedélyezéséről, [töltse le és telepítse a legújabb kiadás előtti feladatok parancsmagjait](https://github.com/jaredmoo/azure-powershell/releases). Ebben az előzetes verzióban korlátozva, ezért forduljon *SaaSFeedback@microsoft.com* kapcsolatos kérdésekre, vagy támogatást.

## <a name="introduction-to-saas-schema-management-patterns"></a>SaaS-séma kezelése minták bemutatása

A több-bérlős szilánkokra osztott adatbázis modellt a jelen példában használt lehetővé teszi a bérlők számára az adatbázis tartalmaz egy vagy több tenant. Ez a minta bemutatja a használatára egy több-bérlős és a egy-bérlői adatbázissal, amely lehetővé teszi a lehetséges egy *hibrid* bérlő felügyeleti modellt. Módosítások és az ilyen adatbázisok kezelése bonyolult feladatnak bizonyulhat. [Rugalmas feladatok](sql-database-elastic-jobs-overview.md) elősegíti a felügyeletét és kezelését nagy számú adatbázis. A feladatok lehetővé teszik, hogy biztonságosan és megbízhatóan Transact-SQL-parancsprogramokat futtató tevékenységek bérlői adatbázisok csoportjain. A feladatok olyan felhasználói interakciótól vagy beviteltől független. Ez a módszer a séma vagy a közös referenciaadatokkal, az alkalmazások az összes bérlőre kiterjedő módosítások üzembe helyezéséhez használható. Rugalmas feladatok is használható az adatbázis hamisított Kerberos-sablon másolatának fenntartásához. A sablon új bérlők számára, mindig biztosítva a legújabb séma létrehozására szolgál, és a referenciaadatok használatban vannak.

![képernyő](media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>Az Elastic Jobs korlátozott előzetes verziója

Nincs, amely mostantól az Azure SQL Database integrált funkciója Elastic Jobs új verziója. Ez Elastic Jobs-nak ez az új verziója jelenleg korlátozott előzetes verzió. A korlátozott előzetes jelenleg támogatja hozhat létre egy feladatot az ügynök és a T-SQL feladatok létrehozása és kezelése a PowerShell használatával.
> [!NOTE]
> Ez az oktatóanyag az SQL Database szolgáltatás, amely egy korlátozott előzetes verzió (rugalmas adatbázis-feladatok) funkcióit használja. Ha szeretné elvégezni ezt az oktatóanyagot, adja meg az előfizetés-Azonosítóját az SaaSFeedback@microsoft.com a subject = Elastic Jobs Preview szöveget. Miután megkapta a megerősítést az előfizetés engedélyezve van, töltse le és telepítse a legújabb kiadás előtti feladatok parancsmagjait. Ebben az előzetes verzióban korlátozva, ezért forduljon SaaSFeedback@microsoft.com kapcsolatos kérdésekre, vagy támogatást.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>A Wingtip Tickets SaaS több-bérlős adatbázis forráskódjához és parancsfájlok

A Wingtip Tickets SaaS több-bérlős adatbázis parancsfájlok és az alkalmazás forráskódjának érhető el a [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) tárházban a Githubon. Tekintse meg a [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) töltse le és a Wingtip Tickets SaaS-parancsfájlok feloldása.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Hozzon létre egy feladatot az ügynök, adatbázis és az új feladat ügynök

Ehhez az oktatóanyaghoz, PowerShell használatával hozza létre a feladat adatbázisának és feladat-ügynök. Az MSDB adatbázis SQL-ügynök által használt, például egy feladat ügynök segítségével egy Azure SQL database feladatdefiníciók, feladatállapotok és -előzmények tárolására. A feladat ügynök létrehozása után hozzon létre, és közvetlenül figyelheti a feladatokat.

1. A **PowerShell ISE-ben**, nyissa meg *... \\Tanulási modulok\\Sémakezelés\\Demo-SchemaManagement.ps1*.
2. A szkriptek futtatásához nyomja le az **F5** billentyűt.

A *Demo-SchemaManagement.ps1* szkript a *Deploy-SchemaManagement.ps1* parancsfájl nevű adatbázis létrehozásához _jobagent_ a katalóguskiszolgálón. A parancsfájl létrehozza a feladat ügynököt, és átadja a _jobagent_ adatbázis paraméterként.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Feladat létrehozása új referenciaadatok bevezetéséhez az összes bérlőn

#### <a name="prepare"></a>Előkészítés

Minden bérlői adatbázis helyszíntípuskészleteket tartalmaznak a készletét tartalmazza a **VenueTypes** tábla. Minden helyszín típusának felhasználásával határozza meg, hogy milyen típusú eseményeket, amelyek üzemeltethető a helyszínen. Ezek a helyszíntípusok megfelelnek a háttérben képeket a bérlő események alkalmazásban látható.  Ebben a gyakorlatban, frissítést helyezhet üzembe az összes adatbázis két további helyszíntípus hozzáadásához: *Motorkerékpár-verseny* és *Úszóklub*.

Először tekintse át az egyes bérlői adatbázisok szerepelnek a helyszíntípusok. A bérlői adatbázisokat az SQL Server Management Studio (SSMS) egyik csatlakozhat, és vizsgálja meg a VenueTypes tábla.  Is lekérdezheti, ha ezt a táblázatot a Lekérdezésszerkesztő az Azure Portalon érhető el, amely az adatbázis oldal.

1. Nyissa meg az ssms-ben és a bérlői kiszolgálóhoz csatlakozni: *tenants1-dpt -&lt;felhasználói&gt;. database.windows.net*
1. Ellenőrizze, hogy a *motorkerékpár-verseny* és *Úszóklub* **nem** jelenleg foglalt, keresse meg a *contosoconcerthall* az adatbázis a *tenants1-dpt -&lt;felhasználói&gt;*  kiszolgáló és a lekérdezés a *VenueTypes* tábla.



#### <a name="steps"></a>Lépések

Most létrehozhat egy feladatot, amely frissíti a **VenueTypes** tábla az egyes bérlők adatbázisokban, a két új helyszíntípusokat hozzáadásával.

Hozzon létre egy új feladatot, az alkalmazásban létrehozott feladatok rendszerszintű tárolt eljárásokra készletét használja a _jobagent_ adatbázis. A tárolt eljárások jöttek létre, amikor a feladat ügynök lett létrehozva.

1. Az ssms-ben, a bérlői kiszolgálóhoz csatlakozni: tenants1-mt -&lt;felhasználói&gt;. database.windows.net

2. Keresse meg a *tenants1* adatbázis.

3. Lekérdezés a *VenueTypes* táblát annak a megerősítéséhez, hogy *motorkerékpár-verseny* és *Úszóklub* még nem az eredménylistában.

4. A kiszolgáló, amely csatlakozni *katalógus-mt -&lt;felhasználói&gt;. database.windows.net*.

5. Csatlakozás a _jobagent_ -adatbázis a kiszolgáló.

6. Az ssms-ben nyissa meg a fájlt *... \\Tanulási modulok\\Sémakezelés\\DeployReferenceData.sql*.

7. Az utasítás módosítása: állítsa be @User = &lt;felhasználói&gt; , és illessze be a felhasználó értéket a Wingtip Tickets SaaS több-bérlős adatbázis-alkalmazás üzembe helyezésekor alkalmazott.

8. A szkriptek futtatásához nyomja le az **F5** billentyűt.

#### <a name="observe"></a>Megfigyelés

Figyelje meg a következő elemeket az *DeployReferenceData.sql* parancsfájlt:

- **SP\_hozzáadása\_cél\_csoport** hoz létre a célcsoport neve *DemoServerGroup*, és a csoporthoz hozzáadott céltagokat.

- **SP\_hozzáadása\_cél\_csoport\_tag** ad hozzá a következő elemek:
    - A *kiszolgáló* céltagtípust.
        - Ez a *tenants1-mt -&lt;felhasználói&gt;*  a bérlői adatbázisokat tartalmazó kiszolgálóra.
        - A bérlői adatbázisokat, amelyeket az az idő a feladat végrehajtja a kiszolgálót is magában foglalja.
    - A *adatbázis* céltagtípust a sablon-adatbázis (*basetenantdb*) helyezkedik el, amely *katalógus-mt -&lt;felhasználói&gt;*  kiszolgálón
    - A *adatbázis* céltagtípust tartalmazza a *adhocreporting* egy újabb oktatóanyagban használt adatbázist.

- **SP\_hozzáadása\_feladat** nevű feladatot hoz létre *referenciaadat-telepítés*.

- **SP\_hozzáadása\_jobstep** a a VenueTypes referenciatáblára való frissítéséhez a T-SQL-parancsszöveget tartalmazó feladatlépést hoz létre.

- A szkript fennmaradó nézetei megjelenítik, hogy léteznek-e az objektumok, és figyelik a feladat-végrehajtást. Ezen lekérdezések használatához, tekintse át az állapot értékét az a **életciklus** oszlop határozza meg, ha a feladat befejeződött. A feladat frissíti a bérlők adatbázist, és a két további adatbázisok, amelyek tartalmazzák a referenciatábla frissíti.

Az ssms-ben, keresse meg a bérlői adatbázis a a *tenants1-mt -&lt;felhasználói&gt;*  kiszolgáló. Lekérdezés a *VenueTypes* táblát annak a megerősítéséhez, hogy *motorkerékpár-verseny* és *Úszóklub* most megjelennek a táblában. A számuk helyszíntípuskészleteket két kell nőtt.

## <a name="create-a-job-to-manage-the-reference-table-index"></a>Feladat létrehozása a referenciatábla indexének kezeléséhez

Ebben a gyakorlatban létrehoz egy feladatot a referenciatábla elsődleges kulcsához a bérlői adatbázisok tartozó index újraépítéséhez. Egy index-újraépítési állapota egy tipikus adatbázis-kezelési művelete, amelyek futhatnak a rendszergazda egy nagy mennyiségű adatok betöltése, a teljesítmény javítása betöltése után.

1. Csatlakozás az ssms-ben, _jobagent_ adatbázist *katalógus-mt -&lt;felhasználói&gt;. database.windows.net* kiszolgáló.

2. Nyissa meg az ssms-ben, *... \\Tanulási modulok\\Sémakezelés\\.SQL*.

3. A szkriptek futtatásához nyomja le az **F5** billentyűt.

#### <a name="observe"></a>Megfigyelés

Figyelje meg a következő elemeket az *.SQL* parancsfájlt:

* **SP\_hozzáadása\_feladat** létrehoz egy új feladatot nevű *Online újraindexelése PK\_\_VenueTyp\_\_265E44FD7FD4C885*.

* **SP\_hozzáadása\_jobstep** a frissítéséhez az index a T-SQL-parancsszöveget tartalmazó feladatlépést hoz létre.

* A szkript fennmaradó nézetei figyelheti a feladat végrehajtása. Ezen lekérdezések használatához, tekintse át az állapot értékét az a **életciklus** oszlop határozza meg, ha a feladat sikeresen befejeződött az összes cél csoport tagjai.

## <a name="additional-resources"></a>További források

<!-- TODO: Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->
* [Kiterjesztett felhőalapú adatbázisok kezelése](sql-database-elastic-jobs-overview.md)
* [Horizontálisan felskálázott felhőalapú adatbázisok létrehozása és kezelése](sql-database-elastic-jobs-create-and-manage.md)

## <a name="next-steps"></a>További lépések

Ennek az oktatóanyagnak a segítségével megtanulta a következőket:

> [!div class="checklist"]
> * Hozzon létre egy feladatot ügynököt a T-SQL-feladatok futtatása több adatbázisban
> * Az összes bérlői adatbázis referenciaadatok frissítése
> * Index létrehozása a táblához az összes bérlői adatbázisban

Ezt követően próbálja meg a [alkalmi jelentéskészítő oktatóanyag](saas-multitenantdb-adhoc-reporting.md) elosztott lekérdezések futtatását több bérlői adatbázisok megismerése.

