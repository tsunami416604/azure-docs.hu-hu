---
title: Shard hozzáadása rugalmas adatbázis-eszközökkel
description: Rugalmas méretezési API-k használatával új szegmensek hozzáadása a szegmenskészlethez.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 4043fd374a314735173a1f07f46c8394592b81e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823714"
---
# <a name="adding-a-shard-using-elastic-database-tools"></a>Szegmens hozzáadása rugalmas adatbázis-eszközökkel

## <a name="to-add-a-shard-for-a-new-range-or-key"></a>Szegmens hozzáadása új tartományhoz vagy kulcshoz

Az alkalmazásoknak gyakran új szegmenseket kell hozzáadniuk az új kulcsoktól vagy kulcstartományoktól elvárt adatok kezeléséhez, egy már létező szegmensleképezéshez. Például egy bérlői azonosító által skálázott alkalmazás előfordulhat, hogy egy új shard egy új bérlő, vagy a havonta skálázott adatok szükség lehet egy új shard kiépített kezdete előtt minden új hónap.

Ha a kulcsértékek új tartománya még nem része egy meglévő leképezésnek, egyszerűen hozzáadhatja az új szegmenst, és társíthatja az új kulcsot vagy tartományt a szegmenshez.

### <a name="example--adding-a-shard-and-its-range-to-an-existing-shard-map"></a>Példa: egy szegmens és a tartomány hozzáadása egy meglévő shard térképhez

Ez a minta a TryGetShard ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.trygetshard), [.NET](https://docs.microsoft.com/previous-versions/azure/dn823929(v=azure.100))) CreateShard ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.createshard), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard)), CreateRangeMapping ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.createrangemapping), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1) metódusok) segítségével jelenik meg, és létrehozza a ShardLocation ([Java](/java/api/com.microsoft.azure.elasticdb.shard.base.shardlocation), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardlocation)) osztály egy példányát. Az alábbi mintában egy **sample_shard_2** nevű adatbázis és az összes benne lévő szükséges sémaobjektum a [300, 400) tartomány tárolására készült.  

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

A .NET verzió, a PowerShell is használhatja alternatívaként hozzon létre egy új Shard Map Manager. Egy példa [itt](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db)érhető el .

## <a name="to-add-a-shard-for-an-empty-part-of-an-existing-range"></a>Szegmens hozzáadása egy meglévő tartomány üres részéhez

Bizonyos körülmények között előfordulhat, hogy már leképezett egy tartományt egy szegmenshez, és részben adatokkal töltötte fel, de most azt szeretné, hogy a közelgő adatok at egy másik szegmensbe irányítsa. Például a shard a nap tartományban, és már kiosztott 50 nap egy shard, de a 24. A rugalmas [adatbázis-felosztott egyesítési eszköz](sql-database-elastic-scale-overview-split-and-merge.md) elvégezheti ezt a műveletet, de ha az adatok áthelyezése nem szükséges (például a [25, 50) napok tartományára vonatkozó adatok, azaz a 25. napon, 50 kizárólagos, még nem létezik), akkor ezt közvetlenül a Shard Map Management API-k használatával végezheti el.

### <a name="example-splitting-a-range-and-assigning-the-empty-portion-to-a-newly-added-shard"></a>Példa: egy tartomány felosztása és az üres rész hozzárendelése egy újonnan hozzáadott szegmenshez

A "sample_shard_2" nevű adatbázis és az összes benne lévő szükséges sémaobjektum létrejött.  

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

**Fontos:** Csak akkor használja ezt a technikát, ha biztos abban, hogy a frissített leképezés tartománya üres.  Az előző módszerek nem ellenőrzik az áthelyezett tartomány adatait, ezért a legjobb, ha az ellenőrzéseket belefoglalja a kódba.  Ha sorok vannak az áthelyezett tartományban, a tényleges adateloszlás nem felel meg a frissített shard térképnek. Ezekben az esetekben a [felosztásos egyesítési eszközzel](sql-database-elastic-scale-overview-split-and-merge.md) hajthatja végre a műveletet.  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
