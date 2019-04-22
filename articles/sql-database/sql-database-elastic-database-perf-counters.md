---
title: Teljesítményszámlálók a szilánkleképezés-kezelővel teljesítményének nyomon követéséhez létrehozása
description: ShardMapManager osztály- és a függő útválasztási teljesítményszámlálók
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: 5c6c923c86ea0c5968079188c87ec3988ec30142
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59494918"
---
# <a name="create-performance-counters-to-track-performance-of-shard-map-manager"></a>Teljesítményszámlálók a szilánkleképezés-kezelővel teljesítményének nyomon követéséhez létrehozása

Teljesítményszámlálók teljesítményének nyomon követésére használt [Adatfüggő útválasztás](sql-database-elastic-scale-data-dependent-routing.md) műveleteket. Ezek a számlálók alatt érhetők el a Teljesítményfigyelőben, a "Elastic Database: Szilánkkezelési"kategóriában.

Teljesítményének rögzítése egy [szilánkleképezés-kezelővel](sql-database-elastic-scale-shard-map-management.md), különösen akkor használ, [Adatfüggő útválasztás](sql-database-elastic-scale-data-dependent-routing.md). Számlálók Microsoft.Azure.SqlDatabase.ElasticScale.Client osztály-módszerekkel együtt jönnek létre.  


**A legújabb verziója:** Lépjen a [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Lásd még: [alkalmazás használatához a legújabb rugalmas adatbázis ügyfélkönyvtárának frissítése](sql-database-elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Előfeltételek

* A teljesítmény kategória és a számlálók létrehozásához a felhasználónak kell lennie a helyi részét **rendszergazdák** csoporthoz az alkalmazást futtató gépen.  
* Hozzon létre egy teljesítményszámláló-példány, és frissíti a számlálókat, a felhasználó tagja kell lennie a **rendszergazdák** vagy **Teljesítményfigyelő felhasználók** csoport.

## <a name="create-performance-category-and-counters"></a>Teljesítmény kategória és -számlálók létrehozása

A számlálók létrehozásához hívja CreatePerformanceCategoryAndCounters módszer a [ShardMapManagementFactory osztály](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory). Csak a rendszergazda hajthat végre a metódus:

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

Is [ez](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) PowerShell-parancsfájl a metódus végrehajtásához.
A metódus létrehozza a következő teljesítményszámlálókkal:  

* **Gyorsítótárazott leképezések**: A gyorsítótárban a szegmenstérkép hozzárendelések száma.
* **Adatfelderítési rekordok művelet/mp**: Az útválasztási függő műveletek esetében a szegmenstérkép sebessége. Ez a számláló akkor frissül, amikor a hívás [OpenConnectionForKey()](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey) a cél szegmenshez a sikeres kapcsolat eredményez.
* **Keresési gyorsítótár-találatok száma/mp-leképezés**: A horizontális skálázási térképet leképezések sikeres gyorsítótár keresési műveletek száma.
* **Keresési gyorsítótár-tévesztések száma/mp-leképezés**: A horizontális skálázási térképet leképezések sikertelen gyorsítótár keresési műveletek száma.
* **Leképezések, vagy a gyorsítótár/mp-ben frissített**: A sebesség, mely leképezések vannak a hozzáadása vagy frissítése a szegmenstérkép-gyorsítótárban.
* **Hozzárendelések eltávolítva a gyorsítótár/mp**: A sebesség, ahol leképezéseket el a szegmenstérkép-gyorsítótárból.

Teljesítményszámlálók minden gyorsítótárazott szegmenstérkép folyamatonként jön létre.  

## <a name="notes"></a>Megjegyzések

A következő események aktiválja a teljesítményszámlálók létrehozása:  

* Inicializálása a [ShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) a [eager betöltése](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy), ha a ShardMapManager bármely szegmenstérképet tartalmaz. Ezek közé tartozik a [GetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager) és a [TryGetSqlShardMapManager](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager) módszereket.
* Horizontálispartíció-térkép, sikerült keresési (használatával [GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx) vagy [GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx)).
* Horizontális skálázási térképet használ CreateShardMap() sikeres létrejöttéről.

A teljesítményszámlálók frissíti a szegmenstérkép és leképezések végrehajtott összes gyorsítótár-műveleteiről. A szegmenstérkép használatával DeleteShardMap() sikeres eltávolítását a teljesítmény-számlálók példány törlését eredményezi.  

## <a name="best-practices"></a>Ajánlott eljárások

* A teljesítmény kategória és a számlálók létrehozása csak egyszer ShardMapManager objektum létrehozása előtt kell elvégezni. Minden, a parancs végrehajtása a CreatePerformanceCategoryAndCounters() törli az előző számlálók (összes példány által jelentett adatok elvesztése), és újakat hoz létre.  
* Teljesítmény számlálópéldányt folyamatonként jönnek létre. Bármely alkalmazás összeomlása vagy eltávolítása a gyorsítótárból horizontálispartíció-térkép eredményez az teljesítmény Számláló példányainak törlése.  

### <a name="see-also"></a>Lásd még

[Az Elastic Database szolgáltatásainak áttekintése](sql-database-elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
