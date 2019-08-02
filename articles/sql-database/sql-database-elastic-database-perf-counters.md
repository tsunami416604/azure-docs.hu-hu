---
title: Teljesítményszámlálók létrehozása a szegmenses Térkép kezelőjé teljesítményének nyomon követéséhez
description: ShardMapManager osztály és az Adatfüggő útválasztási teljesítményszámlálók
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/07/2019
ms.openlocfilehash: ae7666113bd3a4bdb595a8312fdb25007d4ed2c3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568679"
---
# <a name="create-performance-counters-to-track-performance-of-shard-map-manager"></a>Teljesítményszámlálók létrehozása a szegmenses Térkép kezelőjé teljesítményének nyomon követéséhez

A teljesítményszámlálók segítségével nyomon követheti az Adatfüggő [útválasztási](sql-database-elastic-scale-data-dependent-routing.md) műveletek teljesítményét. Ezek a teljesítményszámlálók elérhetők a Teljesítményfigyelőben a "Elastic Database: Szegmens kezelése kategória.

Rögzítheti egy szegmenses [Térkép kezelőjének](sql-database-elastic-scale-shard-map-management.md)teljesítményét, különösen az Adatfüggő [Útválasztás](sql-database-elastic-scale-data-dependent-routing.md)használata esetén. A számlálók a Microsoft. Azure. SqlDatabase. ElasticScale. Client osztály metódusával jönnek létre.  


**A legújabb verzióhoz:** Lépjen a [Microsoft. Azure. SqlDatabase. ElasticScale. Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)webhelyre. Lásd még [az alkalmazás frissítése a legújabb rugalmas adatbázis-ügyféloldali kódtár használatára](sql-database-elastic-scale-upgrade-client-library.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

* A teljesítmény kategóriájának és számlálóinak létrehozásához a felhasználónak a helyi **rendszergazdák** csoport tagjának kell lennie az alkalmazást üzemeltető gépen.  
* A teljesítményszámláló-példányok létrehozásához és a számlálók frissítéséhez a felhasználónak a **rendszergazdák** vagy a **Teljesítményfigyelő felhasználói** csoport tagjának kell lennie.

## <a name="create-performance-category-and-counters"></a>Teljesítmény kategória és számlálók létrehozása

A számlálók létrehozásához hívja meg a [ShardMapManagementFactory osztály](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory)CreatePerformanceCategoryAndCounters metódusát. Csak egy rendszergazda hajthatja végre a metódust:

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

[Ezt](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) a PowerShell-szkriptet használhatja a metódus végrehajtásához is.
A metódus a következő teljesítményszámlálókat hozza létre:  

* **Gyorsítótárazott leképezések**: A szegmens térképhez gyorsítótárazott leképezések száma
* **DDR-művelet/mp**: A szegmenses térképhez kapcsolódó adattovábbítási műveletek sebessége. Ez a számláló akkor frissül, ha a [OpenConnectionForKey ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey) hívása sikeres csatlakozást eredményez a cél szegmenshez.
* **Keresési gyorsítótár látogatottságának leképezése másodpercenként**: A gyorsítótár-keresési műveletek gyakorisága a szegmenses térképen való hozzárendelésekhez.
* **Hozzárendelési keresési gyorsítótárbeli elhagyott érték/mp**: Sikertelen gyorsítótár-keresési műveletek gyakorisága a szegmenses térképen való leképezésekhez.
* **A gyorsítótárban/mp-ben hozzáadott vagy frissített leképezések**: A skálázási Térkép gyorsítótárban való hozzáadásának és frissítésének gyakorisága.
* **Hozzárendelések eltávolítva a gyorsítótárból/MP-ből**: A szegmensek közötti Térkép gyorsítótárból való eltávolításának gyakorisága.

A teljesítményszámlálók minden egyes gyorsítótárazott szegmens-leképezéshez jönnek létre.  

## <a name="notes"></a>Megjegyzések

A következő események indítják el a teljesítményszámlálók létrehozását:  

* A [ShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) inicializálása a [lelkes](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy)betöltéssel, ha a ShardMapManager tartalmaz bármely szegmenses térképet. Ezek közé tartoznak a [GetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager) és a [TryGetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) metódusok.
* Egy szegmenses Térkép sikeres keresése ( [GetShardMap ()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap ()](https://msdn.microsoft.com/library/azure/dn824212.aspx) vagy [GetRangeShardMap ()](https://msdn.microsoft.com/library/azure/dn824173.aspx)használatával).
* A CreateShardMap () használatával sikerült létrehozni a szegmenses leképezést.

A teljesítményszámlálókat a rendszer az összes gyorsítótárazási művelettel frissíti a szegmens térképen és leképezéseken. A szegmens-hozzárendelés sikeres eltávolítása a DeleteShardMap () használatával a teljesítményszámlálók példányának törlését eredményezi.  

## <a name="best-practices"></a>Ajánlott eljárások

* A teljesítmény kategóriájának és számlálóinak létrehozását csak egyszer kell végrehajtani a ShardMapManager objektum létrehozása előtt. A CreatePerformanceCategoryAndCounters () parancs minden végrehajtása törli az előző számlálókat (az összes példány által jelentett adatvesztést), és újakat hoz létre.  
* A teljesítményszámláló példányai folyamat alapján jönnek létre. A rendszer a gyorsítótárból a szegmensek összes összeomlását vagy eltávolítását eredményezi, és törli a teljesítményszámlálók példányainak törlését.  

### <a name="see-also"></a>Lásd még

[Az Elastic Database szolgáltatásainak áttekintése](sql-database-elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
