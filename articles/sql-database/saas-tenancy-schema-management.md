---
title: Azure SQL Database-séma kezelése több-bérlős alkalmazásban | Microsoft Docs
description: Több bérlő sémájának kezelése Azure SQL Database-t használó több-bérlős alkalmazásban
keywords: sql database-oktatóanyag
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.reviewer: billgib
ms.openlocfilehash: 2e4af3e3e1ef1d9da7c66b929885e3ec749b462f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34646272"
---
# <a name="manage-schema-in-a-saas-application-using-the-database-per-tenant-pattern-with-azure-sql-database"></a>Az adatbázis-/-bérlő minta használatával az Azure SQL Database SaaS-alkalmazás a séma kezelése

Egy adatbázis-alkalmazás fejlődésének módosítások mindenképpen szükség, az adatbázis-séma vagy a hivatkozás adataihoz.  Adatbázis-karbantartási feladatok is szükségesek a rendszeres időközönként. Az adatbázis típusú bérlői mintában használó alkalmazások kezelése igényel alkalmazni a módosításokat vagy karbantartási feladatok egy járműflotta bérlői adatbázisok között.

Ez az oktatóanyag ismerteti, két forgatókönyv - hivatkozás adatok frissítéseinek telepítéséhez az összes bérlőre vonatkozó és a referencia-adatokat tartalmazó tábla az index újraépítése. A [rugalmas feladatok](sql-database-elastic-jobs-overview.md) szolgáltatás hajthatók végre ezeket a műveleteket, minden bérlői adatbázisok, és az új bérlő adatbázisok létrehozásához használt sablon adatbázis.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]

> * Hozzon létre egy feladat ügynök
> * Az összes bérlői adatbázis futtatásához a T-SQL feladatok eredményezheti
> * Az összes bérlői adatbázis referenciaadatok frissítése
> * Index létrehozása a táblához az összes bérlői adatbázisban


Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip jegyek SaaS adatbázis / bérlői alkalmazás telepítve van. Kevesebb mint öt perc alatt telepítéséhez lásd: [központi telepítése és vizsgálja meg a bérlői alkalmazásonként Wingtip jegyek Szolgáltatottszoftver-adatbázis](saas-dbpertenant-get-started-deploy.md)
* Az Azure PowerShell telepítve van. A részletekért lásd: [Ismerkedés az Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Telepítve van az SQL Server Management Studio (SSMS) legújabb verziója. [Az SSMS letöltése és telepítése](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

> [!NOTE]
> Ez az oktatóanyag az SQL Database szolgáltatás, amely egy korlátozott előzetes verziójú (a rugalmas adatbázis-feladatok) funkcióit használja. Ha ez az oktatóanyag elvégzéséhez, adja meg az előfizetés-Azonosítóval történő SaaSFeedback@microsoft.com témát = a rugalmas feladatok megtekintése. Miután megkapta a megerősítést az előfizetésének engedélyezéséről, [töltse le és telepítse a legújabb kiadás előtti feladatok parancsmagjait](https://github.com/jaredmoo/azure-powershell/releases). Ez az előnézet korlátozva, így forduljon SaaSFeedback@microsoft.com kapcsolatos kérdésekre, vagy a támogatási szolgálathoz.

## <a name="introduction-to-saas-schema-management-patterns"></a>Bevezetés a Szolgáltatottszoftver-séma felügyeleti minták

Az adatbázis típusú bérlői mintában bérlői adatokat elkülöníti hatékonyan, de kezeli és tartja karban adatbázisok száma. [Rugalmas feladat](sql-database-elastic-jobs-overview.md) elősegíti a adminisztrációs és kezelőkonzol az SQL-adatbázisok. Feladatok lehetővé teszik a biztonságosan és megbízhatóan, futtatni a feladatok (T-SQL-parancsfájlok) adatbázisok csoportja. Feladatok séma és a közös hivatkozás adatváltozásokat telepítheti egy alkalmazás az összes bérlői adatbázisok közötti. Rugalmas feladat is használható karbantartása egy *sablon* mindig biztosítása az új bérlők létrehozásához használt adatbázis a legutóbbi séma- és referenciainformációkat adatokat tartalmaz.

![képernyő](media/saas-tenancy-schema-management/schema-management-dpt.png)


## <a name="elastic-jobs-limited-preview"></a>Az Elastic Jobs korlátozott előzetes verziója

Rugalmas feladat, amelyik most már egy beépített szolgáltatás Azure SQL adatbázis új verziója van telepítve. Ez Elastic Jobs-nak ez az új verziója jelenleg korlátozott előzetes verzió. A korlátozott előzetes verziója jelenleg támogatja a PowerShell használatával létrehozni a feladat ügynököt, és T-SQL feladatok létrehozásához és kezeléséhez.

> [!NOTE]
> Ez az oktatóanyag az SQL Database szolgáltatás, amely egy korlátozott előzetes verziójú (a rugalmas adatbázis-feladatok) funkcióit használja. Ha ez az oktatóanyag elvégzéséhez, adja meg az előfizetés-Azonosítóval történő SaaSFeedback@microsoft.com témát = a rugalmas feladatok megtekintése. Miután megkapta a megerősítést az előfizetésének engedélyezéséről, [töltse le és telepítse a legújabb kiadás előtti feladatok parancsmagjait](https://github.com/jaredmoo/azure-powershell/releases). Ez az előnézet korlátozva, így forduljon SaaSFeedback@microsoft.com kapcsolatos kérdésekre, vagy a támogatási szolgálathoz.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>A bérlői alkalmazás parancsfájlok Wingtip jegyek SaaS adatbázishoz beolvasása

Az alkalmazás forrás kód és a felügyeleti parancsfájlok érhetők el a [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub-tárház. Tekintse meg a [általános útmutatást](saas-tenancy-wingtip-app-guidance-tips.md) töltse le és feloldása a Wingtip jegyek Szolgáltatottszoftver-parancsfájlok lépéseit.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Hozzon létre egy feladat ügynök adatbázis és az új feladat ügynök

Ebben az oktatóanyagban a PowerShell segítségével hozzon létre egy feladat ügynök és a kapcsolódó biztonsági feladat ügynök adatbázis szükséges. A feladat ügynök adatbázis tárolja a definíciók, feladat állapota és előzményei. A feladat ügynök és a kapcsolódó adatbázis létrehozása után hozzon létre, és figyelheti a feladat azonnal.

1. **A PowerShell ISE**, nyissa meg... \\Tanulási modulok\\séma felügyeleti\\*bemutató-SchemaManagement.ps1*.
1. A szkriptek futtatásához nyomja le az **F5** billentyűt.

A *bemutató-SchemaManagement.ps1* parancsfájl-hívások a *telepítés-SchemaManagement.ps1* parancsfájl nevű SQL-adatbázis létrehozásához *osagent* a katalógus-kiszolgálón. Ezután a feladat ügynök, az adatbázis használata paramétereként hoz létre.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Feladat létrehozása új referenciaadatok bevezetéséhez az összes bérlőn

A Wingtip jegyeket alkalmazás egyes bérlői adatbázis támogatott helyszínére típusok készletét tartalmazza. Egyes helyszínekkel van egy adott helyszínére típusú, amely meghatározza, milyen típusú eseményeket lehet üzemeltetni, amely meghatározza, hogy az alkalmazásban használt háttérkép. Az alkalmazás támogatja az új típusú eseményeket a referenciaadatok hozzáadott új és frissített helyszínére típusok kell lennie.  Ebben a gyakorlatban frissítést helyezhet üzembe az összes bérlői adatbázison két további helyszíntípus hozzáadásához: *Motorkerékpár-verseny* és *Úszóklub*.

Először tekintse át az egyes bérlői adatbázisban szereplő helyszínére típusok. Csatlakozzon egy bérlő adatbázis az SQL Server Management Studio (SSMS), és vizsgálja meg a VenueTypes tábla.  Ezt a táblázatot az Azure portálon, a Lekérdezésszerkesztő is lekérdezheti az adatbázissal kapcsolatos beállításainak lapja érhető el. 

1. Nyissa meg a szolgáltatáshoz az SSMS és a bérlői kiszolgálóhoz csatlakozni: *tenants1-dpt -&lt;felhasználói&gt;. database.windows.net*
1. Annak ellenőrzésére, hogy *Motorkerékpárja Racing* és *úszó Club* **nem** jelenleg része, keresse meg a _contosoconcerthall_ az adatbázis a *tenants1-dpt -&lt;felhasználói&gt;*  kiszolgáló és a lekérdezés a *VenueTypes* tábla.

Most hozzon létre egy feladatot, amely frissíti a *VenueTypes* az új helyszínére hozzáadáshoz összes bérlői adatbázis táblájában.

Új feladat létrehozása, a tárolt eljárások létre feladatok rendszer készletének használata a _jobagent_ adatbázis, a feladat ügynök létrehozásakor.

1. Az SSMS, a kiszolgáló csatlakoznak: *katalógus-dpt -&lt;felhasználói&gt;. database.windows.net* kiszolgáló 
1. A szolgáltatáshoz az SSMS nyissa meg a fájlt... \\Tanulási modulok\\séma felügyeleti\\DeployReferenceData.sql
1. Az utasítás módosítása: beállítása @wtpUser = &lt;felhasználói&gt; és a Wingtip jegyek SaaS adatbázis / bérlői alkalmazás telepítésekor használt felhasználói helyettesítésére
1. Biztosan csatlakozni tudjanak a _jobagent_ adatbázis, és nyomja le az **F5** a parancsfájl futtatása

Figyelje meg a következő elemeket a *DeployReferenceData.sql* parancsfájlt:
* **SP\_hozzáadása\_cél\_csoport** hoz létre a célcsoport neve DemoServerGroup.
* **SP\_hozzáadása\_cél\_csoport\_tag** szolgál céladatbázisokhoz a kulcstulajdonságokat határozza meg.  Első a _tenants1-dpt -&lt;felhasználói&gt;_  kiszolgáló hozzá van adva.  Kiszolgálón a kiszolgáló hozzáadása a célként hatására az adatbázisokat a feladat végrehajtása a feladat szereplő idején. Ezt követően a _basetenantdb_ adatbázis és a *adhocreporting* (egy újabb oktatóanyagban használt) adatbázis célként kerülnek.
* **SP\_hozzáadása\_feladat** létrehoz egy feladatot nevű _hivatkozás adatok telepítési_.
* **SP\_hozzáadása\_feladatlépés használja** hoz létre a T-SQL parancs szövege frissíteni a referenciatábla VenueTypes tartalmazó feladatlépéshez.
* A szkript fennmaradó nézetei megjelenítik, hogy léteznek-e az objektumok, és figyelik a feladat-végrehajtást. Tekintse át a értéket használja ezeket a lekérdezéseket a **életciklus** határozza meg, ha a feladat befejeződött a céladatbázisokhoz alapját képező oszlop.

A parancsfájl befejezése után ellenőrizheti a referenciaadatok frissítve lett.  A szolgáltatáshoz az SSMS, keresse meg a a *contosoconcerthall* adatbázisról a a *tenants1-dpt -&lt;felhasználói&gt;*  kiszolgáló és a lekérdezés a *VenueTypes* tábla.  Ellenőrizze, hogy *Motorkerékpárja Racing* és *úszó Club* **vannak** most már létezik.


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Feladat létrehozása a referenciatábla indexének kezeléséhez

Ebben a gyakorlatban egy feladat használatával építse újra a hivatkozási tábla elsődleges kulcs indexe.  Ez az egy tipikus adatbázis-karbantartási művelet nagy mennyiségű adatok betöltése után előfordulhat, hogy létrehozhatók.

Hozzon létre egy feladatot ugyanannak a feladatnak a „system” által tárolt eljárásait használva.

1. Nyissa meg a szolgáltatáshoz az SSMS és kapcsolódjon a _katalógus-dpt -&lt;felhasználói&gt;. database.windows.net_ kiszolgáló
1. Nyissa meg a fájlt _... \\Tanulási modulok\\séma felügyeleti\\OnlineReindex.sql_
1. Kattintson a jobb gombbal, válassza ki a kapcsolat, és kapcsolódjon a _katalógus-dpt -&lt;felhasználói&gt;. database.windows.net_ kiszolgáló, ha még nincs csatlakoztatva
1. Biztosan csatlakozni tudjanak a _jobagent_ adatbázis, és nyomja le az **F5** a parancsfájl futtatása

Figyelje meg a következő elemeket a _OnlineReindex.sql_ parancsfájlt:
* **SP\_hozzáadása\_feladat** létrehoz egy új feladatot nevű "Online ismételt indexelése PK\_\_VenueTyp\_\_265E44FD7FD4C885"
* **SP\_hozzáadása\_feladatlépés használja** hoz létre az index frissítésének T-SQL-parancs szöveget tartalmazó feladat lépésének
* A parancsfájl-figyelő feladat végrehajtása a fennmaradó nézeteket. Tekintse át a értéket használja ezeket a lekérdezéseket a **életciklus** oszlop határozza meg, ha a feladat sikeresen befejeződött a cél-csoport minden tagjára.



## <a name="next-steps"></a>További lépések

Ennek az oktatóanyagnak a segítségével megtanulta a következőket:

> [!div class="checklist"]

> * Hozzon létre egy feladat ügynök több adatbázis közötti T-SQL feladatok futtatásához
> * Az összes bérlői adatbázis referenciaadatok frissítése
> * Index létrehozása a táblához az összes bérlői adatbázisban

Ezt követően próbálja meg a [alkalmi jelentéskészítési oktatóanyag](saas-tenancy-cross-tenant-reporting.md) eltérések felfedezése, elosztott lekérdezések futtatásáért bérlői adatbázisok.


## <a name="additional-resources"></a>További források

* [További oktatóprogramot kínál, amelyek a Wingtip jegyek SaaS adatbázis / bérlői alkalmazástelepítés épül](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Kiterjesztett felhőalapú adatbázisok kezelése](sql-database-elastic-jobs-overview.md)
* [Horizontálisan felskálázott felhőalapú adatbázisok létrehozása és kezelése](sql-database-elastic-jobs-create-and-manage.md)