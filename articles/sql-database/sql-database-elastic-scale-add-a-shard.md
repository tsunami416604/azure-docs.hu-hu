---
title: Rugalmas Adatbáziseszközök használatáról szilánk hozzáadása |} A Microsoft Docs
description: Állítsa be a rugalmas, méretezhető API-k használata új szegmensek hozzáadása egy szegmens.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-scale
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: 3be9723d0beddc6bfcd6b6a34b56f4b8fd80aa31
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50239933"
---
# <a name="adding-a-shard-using-elastic-database-tools"></a>Rugalmas Adatbáziseszközök használatáról szilánk hozzáadása
## <a name="to-add-a-shard-for-a-new-range-or-key"></a>Egy új tartományt vagy a kulcs szilánk hozzáadása
Alkalmazások gyakran kell hozzáadni az új kulcsok vagy kulcstartományokkal horizontálispartíció-térkép, amely már létezik a várt adatok kezelésének új szegmensekre. Például előfordulhat, hogy egy új szegmensen üzembe helyezhet egy új bérlőt kell szilánkokra osztott alkalmazás bérlői azonosító alapján, vagy adatok horizontálisan skálázott havi szükség lehet egy új szegmensen üzembe helyezett minden egyes új hónap kezdete előtt. 

Ha a kulcs értékeit az új tartomány még nem részei egy létező hozzárendelés, egyszerűen adja hozzá az új szegmensen, és társítsa az új kulcs vagy a tartományt a többi. 

### <a name="example--adding-a-shard-and-its-range-to-an-existing-shard-map"></a>Példa: szegmensek és a tartomány hozzáadása egy meglévő szegmenstérkép
Ebben a példában a TryGetShard ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._shard_map.trygetshard), [.NET](https://msdn.microsoft.com/library/azure/dn823929.aspx)) a CreateShard ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map._shard_map.createshard), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard.aspx)), CreateRangeMapping ([ Java](/java/api/com.microsoft.azure.elasticdb.shard.map._range_shard_map.createrangemapping), [.NET](https://msdn.microsoft.com/library/azure/dn807221.aspx#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.RangeShardMap`1.CreateRangeMapping\(Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.RangeMappingCreationInfo{`0}\))) módszereket, és létrehoz egy példányt a ShardLocation ([Java](/java/api/com.microsoft.azure.elasticdb.shard.base._shard_location), [.NET](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardlocation.shardlocation.aspx#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardLocation.)) osztály. A minta az alábbi nevű adatbázis **sample_shard_2** és alapjait, az összes szükséges sémaobjektumok elkészültek, amely tárolja a napnak a [300, 400-as).  

```csharp
// sm is a RangeShardMap object.
// Add a new shard to hold the range being added. 
Shard shard2 = null; 

if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
{ 
    shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
} 

// Create the mapping and associate it with the new shard 
sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                            (new Range<long>(300, 400), shard2, MappingStatus.Online)); 
```

A .NET-verzióhoz is használhatja PowerShell alternatívájaként hozhat létre egy új Szilánkleképezés-kezelővel. Példa érhető el [Itt](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

## <a name="to-add-a-shard-for-an-empty-part-of-an-existing-range"></a>A meglévő tartomány egyik üres része horizontális partíció hozzáadása
Bizonyos körülmények között előfordulhat, hogy már számos rendelve szegmensek és részlegesen adatokkal feltöltött, de közelgő adatokat a rendszer a másik adatszilánkba író most szeretnénk. Például, szegmens szerint naptartomány és már van lefoglalt 50 napja van egy szegmensre, de napja 24, a kívánt jövőbeli adatkezelési eltérő szegmensben kerül. A rugalmas adatbázis [felosztási-egyesítési eszközének](sql-database-elastic-scale-overview-split-and-merge.md) hajthatják végre ezt a műveletet, de ha adatáthelyezés nem szükséges (például adatokat a különböző, [25, 50 nap), azaz, naponta 25 kizárólagos, 50 között lehet még nem létezik) Ez a szegmens térkép felügyeleti API-k teljes egészében közvetlenül használatával.

### <a name="example-splitting-a-range-and-assigning-the-empty-portion-to-a-newly-added-shard"></a>Példa: egy tartományt felosztása, és az üres részét hozzárendelése egy újonnan hozzáadott szegmensek
"Sample_shard_2" és az összes szükséges séma objektum belül, egy adatbázist létrehozni.  

```csharp
// sm is a RangeShardMap object.
// Add a new shard to hold the range we will move 
Shard shard2 = null; 

if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
{ 
    shard2 = sm.CreateShard(new ShardLocation(shardServer,"sample_shard_2"));  
} 

// Split the Range holding Key 25 
sm.SplitMapping(sm.GetMappingForKey(25), 25); 

// Map new range holding [25-50) to different shard: 
// first take existing mapping offline 
sm.MarkMappingOffline(sm.GetMappingForKey(25)); 

// now map while offline to a different shard and take online 
RangeMappingUpdate upd = new RangeMappingUpdate(); 
upd.Shard = shard2; 
sm.MarkMappingOnline(sm.UpdateMapping(sm.GetMappingForKey(25), upd)); 
```

**Fontos**: ezt a módszert használja, csak ha biztos abban, hogy tartományát üres-e a frissített leképezést.  A fenti módszerek nem ellenőrzi a tartomány áthelyezése esetén az adatok, így érdemes ellenőrzéseket tartalmazzák a kódban.  Ha a sorok léteznek, a tartomány áthelyezése folyamatban van, a tényleges adatok terjesztési nem egyeznek meg a frissített szegmenstérkép. Használja a [felosztási-egyesítési eszközének](sql-database-elastic-scale-overview-split-and-merge.md) ezekben az esetekben ehelyett a művelet végrehajtására.  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

