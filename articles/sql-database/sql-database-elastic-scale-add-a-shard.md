---
title: "A rugalmas adatbázis-eszközökkel szilánkcímtárban hozzáadása |} Microsoft Docs"
description: "Állítsa be a rugalmas, méretezhető API-k használata új szilánkok hozzáadása a szilánkcímtárban."
services: sql-database
documentationcenter: 
manager: jhubbard
author: ddove
editor: 
ms.assetid: 62a349db-bebe-406f-a120-2f1986f2b286
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: ddove
ms.openlocfilehash: 7432bf00aa4d97d8dbc4b92a6a836698ee2b84d7
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="adding-a-shard-using-elastic-database-tools"></a>A rugalmas adatbázis-eszközökkel szilánkcímtárban hozzáadása
## <a name="to-add-a-shard-for-a-new-range-or-key"></a>A szilánkok egy új tartományt vagy a kulcs hozzáadása
Alkalmazások egyszerűen adja hozzá az új kulcsokat vagy egy már létező shard térkép kulcstartományokkal várt adatok kezelésének új szilánkok gyakran kell. Például egy bérlő-azonosító szerint szilánkos alkalmazás egy új shard létrehozni egy új bérlőt kell, vagy adatok szilánkos havi esetleg egy új shard kiépített új havonta megkezdése előtt. 

Az új kulcs értékek tartományán már nem része egy meglévő leképezést, akkor adja hozzá az új shard, és rendelje hozzá az új kulcs vagy az és, hogy a shard nagyon egyszerű. 

### <a name="example--adding-a-shard-and-its-range-to-an-existing-shard-map"></a>Példa: egy shard és a tartomány hozzáadása meglévő shard térkép
Ebben a példában a [TryGetShard](https://msdn.microsoft.com/library/azure/dn823929.aspx) a [CreateShard](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard.aspx), [CreateRangeMapping](https://msdn.microsoft.com/library/azure/dn807221.aspx#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.RangeShardMap`1.CreateRangeMapping\(Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.RangeMappingCreationInfo{`0}\)) módszerek, és létrehoz egy példányát a [ShardLocation](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardlocation.shardlocation.aspx#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardLocation.)osztály. Néven adatbázis az alábbi minta **sample_shard_2** , minden szükséges sémaobjektumok saját létrejöttek ahhoz, hogy a tartomány [300, 400).  

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


Alternatív megoldásként a Powershell segítségével hozzon létre egy új Shard térkép Manager. Példa: elérhető [Itt](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

## <a name="to-add-a-shard-for-an-empty-part-of-an-existing-range"></a>Egy meglévő tartomány üres részét képezik a szilánkcímtárban hozzáadása
Bizonyos körülmények között előfordulhat, hogy már van leképezve a shard széles és részben megtelt adatokkal, de az kívánja most átirányítja egy másik shard jövőbeli adatokat. Például, hogy a shard naptartomány által, és már lefoglalt 50 nap kell egy shard, de 24 napon szeretné megnyílik egy másik shard a jövőbeli adatokat. A rugalmas adatbázis [vegyes egyesítéses eszköz](sql-database-elastic-scale-overview-split-and-merge.md) hajthatják végre a műveletet, de ha az adatmozgatás nem szükséges (például a tartományhoz [25, 50 napnyi adat), azaz, napi 25 kizárólagos, 50 között lehet még nem létezik) Ez végezheti el a szilánkok térkép felügyeleti API-k teljes mértékben közvetlenül használatával.

### <a name="example-splitting-a-range-and-assigning-the-empty-portion-to-a-newly-added-shard"></a>Példa: egy tartományt a felosztás, és az üres rész hozzárendelése egy újonnan hozzáadott shard
Létrehozott egy "sample_shard_2" és az összes szükséges séma objektum saját nevű adatbázis.  

    // sm is a RangeShardMap object.
    // Add a new shard to hold the range we will move 
    Shard shard2 = null; 

    if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
    { 

        shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
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

**Fontos**: Ezzel a technikával használja, csak ha biztos abban, hogy a tartományon a frissített leképezése nem üres.  A fenti módszerek nem ellenőrzi adatokat át, a tartományhoz, így legjobb ellenőrzések szerepeljenek a kódot.  Ha a sorok léteznek éppen áthelyezik a tartományban, a tényleges adatok terjesztési nem fog egyezni a frissített shard leképezés. Használja a [vegyes egyesítéses eszköz](sql-database-elastic-scale-overview-split-and-merge.md) ezekben az esetekben inkább a művelet végrehajtására.  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

