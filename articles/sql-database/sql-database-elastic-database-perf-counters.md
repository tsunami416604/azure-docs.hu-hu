---
title: Teljesítményszámlálók a szilánkleképezés-kezelőhöz
description: ShardMapManager osztály- és a függő útválasztási teljesítményszámlálói
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: 9c134ee96f7749529ab665df041cfc51c979acde
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34647323"
---
# <a name="performance-counters-for-shard-map-manager"></a>Teljesítményszámlálók a szilánkleképezés-kezelőhöz
Rögzítheti a teljesítményét egy [shard térkép manager](sql-database-elastic-scale-shard-map-management.md), különösen akkor, ha használatával [adatok függő útválasztási](sql-database-elastic-scale-data-dependent-routing.md). Számlálók Microsoft.Azure.SqlDatabase.ElasticScale.Client osztály metódusával jönnek létre.  

Teljesítményszámlálók segítségével nyomon követhető a teljesítményét [adatok függő útválasztási](sql-database-elastic-scale-data-dependent-routing.md) műveletek. Ezek a számlálók a Teljesítményfigyelőben, a "Rugalmas adatbázis: a Shard kezelése" kategória alatt érhetők el.

**A legújabb verzió:** Ugrás [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Lásd még: [frissítése a legújabb elastic database ügyféloldali kódtár használata alkalmazások](sql-database-elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Előfeltételek
* A teljesítménykategória és számlálók létrehozásához a felhasználónak kell lennie a helyi részét **rendszergazdák** csoportot az alkalmazást futtató számítógépen.  
* A teljesítményszámláló-példány létrehozásához, és a számlálók frissítéséhez, a felhasználó tagja sem kell lennie a **rendszergazdák** vagy **Teljesítményfigyelő felhasználók** csoport. 

## <a name="create-performance-category-and-counters"></a>Hozzon létre teljesítménykategória és -számlálók
A számlálók létrehozásához hívja a CreatePeformanceCategoryAndCounters a [ShardMapManagmentFactory osztály](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.aspx). Csak a rendszergazda a metódus segítségével hajtható végre: 

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

Is [ez](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) PowerShell-parancsfájl a metódus végrehajtásához. A metódus létrehozza a következő teljesítményszámlálókkal:  

* **Gyorsítótárazott hozzárendelések**: a shard térkép gyorsítótárazott hozzárendelések száma.
* **DDR művelet/mp**: a shard térkép útválasztási műveletek függő adatokat. Ez a számláló akkor frissül, amikor hívása [OpenConnectionForKey()](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx) létre sikeres kapcsolatot a cél shard eredményez. 
* **Keresési találatok száma gyorsítótárban másodpercenként leképezési**: sikeres gyorsítótár keresési műveletek a shard térkép leképezések. 
* **Keresési gyorsítótár-tévesztései/másodperc leképezési**: gyorsítótár sikertelen keresési műveletek a shard térkép leképezések.
* **Hozzárendelések, vagy a gyorsítótár másodpercenkénti frissített**: mely hozzárendelések közé éppen felvett vagy frissített, a gyorsítótár a shard térkép aránya. 
* **Hozzárendelések eltávolítja a gyorsítótár másodpercenkénti**: sebesség, amellyel hozzárendelések el a shard térkép-gyorsítótárból. 

Teljesítményszámlálók minden gyorsítótárazott shard leképezés folyamatonként jön létre.  

## <a name="notes"></a>Megjegyzések
A következő események elindítani a teljesítményszámlálók létrehozását:  

* Inicializálása a [ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) rendelkező [különösen betöltése](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy.aspx), ha a ShardMapManager tartalmazza a szilánkcímtárban. Ezek közé tartozik a [GetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx?f=255&MSPPError=-2147217396#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerFactory.GetSqlShardMapManager%28System.String,Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerLoadPolicy%29) és a [TryGetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx) módszerek.
* A szilánkok leképezés sikeres keresési (használatával [GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx) vagy [GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx)). 
* A sikeres létrehozást shard térkép CreateShardMap() használatával.

A teljesítményszámlálók frissíti a shard térkép és hozzárendelések végrehajtott összes gyorsítótár-műveletekhez. Sikeres Eltávolítás a shard térkép DeleteShardMap () reults használatát a teljesítménypéldány számlálók törlése.  

## <a name="best-practices"></a>Ajánlott eljárások
* A teljesítménykategória és számlálók létrehozását csak egyszer ShardMapManager objektum létrehozása előtt kell elvégezni. Minden, a parancs végrehajtása a CreatePerformanceCategoryAndCounters() törli az előző számlálók (összes példány által küldött adatok elvesztése), és újakat hoz létre.  
* Teljesítmény számlálópéldány folyamatonként jönnek létre. Bármely alkalmazás összeomlása és -eltávolítási szilánkok leképezés a gyorsítótárból eredményez a teljesítmény számlálók példányok törlése.  

### <a name="see-also"></a>Lásd még
[Az Elastic Database szolgáltatásainak áttekintése](sql-database-elastic-scale-introduction.md)  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->

