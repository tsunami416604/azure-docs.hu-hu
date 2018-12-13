---
title: Teljesítményszámlálók a szilánkleképezés-kezelőhöz
description: ShardMapManager osztály- és a függő útválasztási teljesítményszámlálók
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 03/31/2018
ms.openlocfilehash: f98c09a7e51fa729ef4a940e5f3c03de55d8dfd2
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52875280"
---
# <a name="performance-counters-for-shard-map-manager"></a>Teljesítményszámlálók a szilánkleképezés-kezelőhöz
Teljesítményének rögzítése egy [szilánkleképezés-kezelővel](sql-database-elastic-scale-shard-map-management.md), különösen akkor használ, [Adatfüggő útválasztás](sql-database-elastic-scale-data-dependent-routing.md). Számlálók Microsoft.Azure.SqlDatabase.ElasticScale.Client osztály-módszerekkel együtt jönnek létre.  

Számlálók teljesítményének nyomon követésére használt [Adatfüggő útválasztás](sql-database-elastic-scale-data-dependent-routing.md) műveleteket. Ezek a számlálók a Teljesítményfigyelőben, a "Elastic Database: Szilánkkezelési" kategória alatt érhetők el.

**A legújabb verzió:** Ugrás [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Lásd még: [alkalmazás használatához a legújabb rugalmas adatbázis ügyfélkönyvtárának frissítése](sql-database-elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Előfeltételek
* A teljesítmény kategória és a számlálók létrehozásához a felhasználónak kell lennie a helyi részét **rendszergazdák** csoporthoz az alkalmazást futtató gépen.  
* Hozzon létre egy teljesítményszámláló-példány, és frissíti a számlálókat, a felhasználó tagja kell lennie a **rendszergazdák** vagy **Teljesítményfigyelő felhasználók** csoport. 

## <a name="create-performance-category-and-counters"></a>Teljesítmény kategória és -számlálók létrehozása
A számlálók létrehozásához hívja CreatePeformanceCategoryAndCounters módszer a [ShardMapManagementFactory osztály](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.aspx). Csak a rendszergazda hajthat végre a metódus: 

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

Is [ez](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) PowerShell-parancsfájl a metódus végrehajtásához. A metódus létrehozza a következő teljesítményszámlálókkal:  

* **Gyorsítótárazott leképezések**: gyorsítótárazza a szegmenstérkép hozzárendelések száma.
* **Adatfelderítési rekordok művelet/mp**: függő útválasztási műveletek adatokat horizontális skálázási térképet. Ez a számláló akkor frissül, amikor a hívás [OpenConnectionForKey()](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx) a cél szegmenshez a sikeres kapcsolat eredményez. 
* **Keresési gyorsítótár-találatok száma/mp-leképezés**: sikeres gyorsítótár-keresési műveletek leképezések a horizontális skálázási térképet. 
* **Keresési gyorsítótár-tévesztések száma/mp-leképezés**: leképezések a horizontális skálázási térképet gyorsítótár sikertelen keresési műveletek száma.
* **Leképezések, vagy a gyorsítótár/mp-ben frissített**: mely leképezések lettek hozzáadva vagy frissítve az a szegmenstérkép-gyorsítótár számolva. 
* **Hozzárendelések eltávolítva a gyorsítótár/mp**: sebesség, amellyel hozzárendelések el a szegmenstérkép-gyorsítótárból. 

Teljesítményszámlálók minden gyorsítótárazott szegmenstérkép folyamatonként jön létre.  

## <a name="notes"></a>Megjegyzések
A következő események aktiválja a teljesítményszámlálók létrehozása:  

* Inicializálása a [ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) a [eager betöltése](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy.aspx), ha a ShardMapManager bármely szegmenstérképet tartalmaz. Ezek közé tartozik a [GetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx?f=255&MSPPError=-2147217396#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerFactory.GetSqlShardMapManager%28System.String,Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerLoadPolicy%29) és a [TryGetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx) módszereket.
* Horizontálispartíció-térkép, sikerült keresési (használatával [GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx) vagy [GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx)). 
* Horizontális skálázási térképet használ CreateShardMap() sikeres létrejöttéről.

A teljesítményszámlálók frissíti a szegmenstérkép és leképezések végrehajtott összes gyorsítótár-műveleteiről. A horizontális skálázási térképet DeleteShardMap () reults használata a teljesítmény-számlálói példány törlése sikeres eltávolítása.  

## <a name="best-practices"></a>Ajánlott eljárások
* A teljesítmény kategória és a számlálók létrehozása csak egyszer ShardMapManager objektum létrehozása előtt kell elvégezni. Minden, a parancs végrehajtása a CreatePerformanceCategoryAndCounters() törli az előző számlálók (összes példány által jelentett adatok elvesztése), és újakat hoz létre.  
* Teljesítmény számlálópéldányt folyamatonként jönnek létre. Bármely alkalmazás összeomlása vagy eltávolítása a gyorsítótárból horizontálispartíció-térkép eredményez az teljesítmény Számláló példányainak törlése.  

### <a name="see-also"></a>Lásd még
[Az Elastic Database szolgáltatásainak áttekintése](sql-database-elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->

