---
title: Teljesítményszámlálók a shard térképkezelő nyomon követéséhez
description: ShardMapManager osztály- és adatfüggő útválasztási teljesítményszámlálók
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seoapril2019, seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/07/2019
ms.openlocfilehash: de481dad9dd39b301a21142c67b1baf2209f76e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823906"
---
# <a name="create-performance-counters-to-track-performance-of-shard-map-manager"></a>Teljesítményszámlálók létrehozása a szegmenstérkép-kezelő teljesítményének nyomon követéséhez

A teljesítményszámlálók az [adatfüggő útválasztási](sql-database-elastic-scale-data-dependent-routing.md) műveletek teljesítményének nyomon követésére szolgálnak. Ezek a számlálók érhetők el a Teljesítményfigyelő, a "Rugalmas adatbázis: Shard Management" kategóriában érhető el.

A [szegmenstérkép-kezelő](sql-database-elastic-scale-shard-map-management.md)teljesítményét rögzítheti, különösen [adatfüggő útválasztás](sql-database-elastic-scale-data-dependent-routing.md)használata esetén. Számlálók jönnek létre a microsoft.Azure.SqlDatabase.ElasticScale.Client osztály metódusai.  


**A legújabb verzió:** Látogasson el a [Microsoft.Azure.SqlDatabase.ElasticScale.Client elemre.](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) Lásd [még: Egy alkalmazás frissítése a legújabb rugalmas adatbázis-ügyfélkódtár használatához.](sql-database-elastic-scale-upgrade-client-library.md)

## <a name="prerequisites"></a>Előfeltételek

* A teljesítménykategória és -számlálók létrehozásához a felhasználónak az alkalmazást üzemeltető gépen a helyi **Rendszergazdák** csoport tagja kell, hogy legyen.  
* Teljesítményszámláló-példány létrehozásához és a számlálók frissítéséhez a felhasználónak a **Rendszergazdák** vagy a **Teljesítményfigyelő felhasználói** csoportjának tagjának kell lennie.

## <a name="create-performance-category-and-counters"></a>Teljesítménykategória és -számlálók létrehozása

A számlálók létrehozásához hívja meg a [ShardMapManagementFactory osztály](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory)CreatePerformanceCategoryAndCounters metódusát. A metódust csak rendszergazda hajthatja végre:

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

Ezt [a](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) PowerShell-parancsfájlt is használhatja a metódus végrehajtásához.
A metódus a következő teljesítményszámlálókat hozza létre:  

* **Gyorsítótárazott leképezések:** A szegmensleképezés gyorsítótárazott leképezéseinek száma.
* **DDR-műveletek/mp**: A szegmenstérkép adatfüggő útválasztási műveleteinek mértéke. Ez a számláló frissül, ha az [OpenConnectionForKey()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey) hívása sikeres kapcsolatot eredményez a célszegmenshez.
* **Leképezési gyorsítótár-lekérések másodpercenként:** A shard-térkép leképezéseinek sikeres gyorsítótár-felolvasási műveleteinek aránya.
* **Leképezési keresése gyorsítótár a másodpercenként:** A szegmenstérkép leképezéseinek sikertelen gyorsítótár-keresései aránya.
* **Gyorsítótár-/mp-ben hozzáadott vagy frissített leképezések:** A leképezések hozzáadásának vagy frissítésének mértéke a shard leképezés gyorsítótárában.
* **A gyorsítótárból/mp-ből eltávolított leképezések:** A leképezések eltávolításának mértéke a szegmensleképezés gyorsítótárából.

Teljesítményszámlálók jönnek létre az egyes gyorsítótárazott shard térkép folyamatonként.  

## <a name="notes"></a>Megjegyzések

A következő események a teljesítményszámlálók létrehozását indítják el:  

* Inicializálása a [ShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) [a lelkes betöltése,](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy)ha a ShardMapManager tartalmaz shard maps. Ezek közé tartozik a [GetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager) és a [TryGetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) metódusok.
* A shard térkép sikeres felkeresni [(a GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [a GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx) vagy a [GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx)használatával.
* A shard térkép sikeres létrehozása a CreateShardMap() használatával.

A teljesítményszámlálók at a szegmenstérképen és a leképezéseken végrehajtott összes gyorsítótár-művelet frissíti. A shard térkép deleteShardMap() használatával sikeres eltávolítása a teljesítményszámlálók példányának törlését eredményezi.  

## <a name="best-practices"></a>Ajánlott eljárások

* A teljesítménykategória és -számlálók létrehozását csak egyszer kell végrehajtani a ShardMapManager objektum létrehozása előtt. A CreatePerformanceCategoryAndCounters() parancs minden végrehajtása törli a korábbi számlálókat (az összes példány által jelentett adatok elvesztése), és újakat hoz létre.  
* A teljesítményszámláló-példányok folyamatonként jönnek létre. Minden alkalmazás összeomlása vagy eltávolítása a shard térkép a gyorsítótárból a teljesítményszámlálók példányainak törlését eredményezi.  

### <a name="see-also"></a>Lásd még

[Az Elastic Database szolgáltatásainak áttekintése](sql-database-elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
