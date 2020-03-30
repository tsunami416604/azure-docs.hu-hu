---
title: Meglévő adatbázisok áttelepítése horizontális felskálázáshoz | Microsoft dokumentumok
description: Szilánkos adatbázisok konvertálása rugalmas adatbázis-eszközök használatára shard map manager létrehozásával
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: c776f4ac09626f0abd1eb754cde391a1c5447627
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74421222"
---
# <a name="migrate-existing-databases-to-scale-out"></a>Meglévő adatbázisok áttelepítése horizontális felskálázáshoz

Az Azure SQL Database adatbázis-eszközei (például a [rugalmas adatbázis-ügyfélkódtár)](sql-database-elastic-database-client-library.md)segítségével egyszerűen kezelheti a meglévő kibővített horizontális annektált adatbázisokat. Először konvertáljon egy meglévő adatbáziskészletet a [shard térképkezelő használatára.](sql-database-elastic-scale-shard-map-management.md)

## <a name="overview"></a>Áttekintés

Meglévő szilánkos adatbázis áttelepítése:

1. Készítse elő a [shard map manager adatbázist](sql-database-elastic-scale-shard-map-management.md).
2. Hozza létre a shard térképet.
3. Készítse elő az egyes szilánkokat.  
4. Leképezések hozzáadása a shard térképhez.

Ezek a technikák a [.](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/) [Azure SQL DB - Elastic Database tools scripts](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db) Az itt látható példák a PowerShell-parancsfájlokat használják.

A ShardMapManager szolgáltatásról további információt a [Shard térképkezelés című](sql-database-elastic-scale-shard-map-management.md)témakörben talál. A rugalmas adatbázis-eszközök áttekintését a [Rugalmas adatbázis-szolgáltatások – áttekintés című témakörben találja.](sql-database-elastic-scale-introduction.md)

## <a name="prepare-the-shard-map-manager-database"></a>A szegmenstérkép-kezelő adatbázisának előkészítése

A shard map manager egy speciális adatbázis, amely tartalmazza a kibővített adatbázisok kezeléséhez szükséges adatokat. Használhat egy meglévő adatbázist, vagy létrehozhat új adatbázist. A szegmenstérkép-kezelőként működő adatbázis nem lehet ugyanaz az adatbázis, mint a szegmens. A PowerShell-parancsfájl nem hozza létre az adatbázist.

## <a name="step-1-create-a-shard-map-manager"></a>1. lépés: shard térképkezelő létrehozása

```powershell
# Create a shard map manager
New-ShardMapManager -UserName '<user_name>' -Password '<password>' -SqlServerName '<server_name>' -SqlDatabaseName '<smm_db_name>'
#<server_name> and <smm_db_name> are the server name and database name
# for the new or existing database that should be used for storing
# tenant-database mapping information.
```

### <a name="to-retrieve-the-shard-map-manager"></a>A shard térképkezelő beolvasása

A létrehozás után ezzel a parancsmaggal lekérheti a shard térképkezelőt. Erre a lépésre minden alkalommal szükség van a ShardMapManager objektum használatához.

```powershell
# Try to get a reference to the Shard Map Manager  
$ShardMapManager = Get-ShardMapManager -UserName '<user_name>' -Password '<password>' -SqlServerName '<server_name>' -SqlDatabaseName '<smm_db_name>'
```

## <a name="step-2-create-the-shard-map"></a>2. lépés: a shard térkép létrehozása

Válassza ki a létrehozni kívánt szegmenstérkép típusát. A választás az adatbázis architektúrájáttól függ:

1. Adatbázisonként egyetlen bérlő (a kifejezésekért lásd a [szószedetet.)](sql-database-elastic-scale-glossary.md)
2. Adatbázisonként több bérlő (két típus):
   1. Lista leképezése
   2. Tartomány leképezése

Egy bérlős modell létrehozása **listaleképezés shard** leképezési. Az egybérlős modell bérlőnként egy adatbázist rendel hozzá. Ez egy hatékony modell a SaaS-fejlesztők számára, mivel leegyszerűsíti a felügyeletet.

![Lista leképezése][1]

A több-bérlős modell több bérlőt rendel egy adott adatbázishoz (és a bérlők csoportjait több adatbázis között is eloszthatja). Akkor használja ezt a modellt, ha azt várja, hogy minden bérlőnek kis adatigénye legyen. Ebben a modellben rendeljen bérlői tartományt egy adatbázishoz **tartományleképezés**használatával.

![Tartomány leképezése][2]

Vagy valósíthat meg egy több-bérlős adatbázis-modell egy *lista leképezés* segítségével több bérlő hozzárendelése egy adott adatbázishoz. A DB1 például az 1-es és 5-ös bérlői azonosító adatainak tárolására szolgál, a DB2 pedig a 7-es és a 10-es bérlő adatait tárolja.

![Több bérlő egyetlen adatbázison][3]

**A választás alapján válasszon az alábbi lehetőségek közül:**

### <a name="option-1-create-a-shard-map-for-a-list-mapping"></a>1. lehetőség: shard térkép létrehozása listaleképezéshez

Hozzon létre egy shard térképet a ShardMapManager objektum használatával.

```powershell
# $ShardMapManager is the shard map manager object
$ShardMap = New-ListShardMap -KeyType $([int]) -ListShardMapName 'ListShardMap' -ShardMapManager $ShardMapManager
```

### <a name="option-2-create-a-shard-map-for-a-range-mapping"></a>2. lehetőség: shard térkép létrehozása tartományleképezéshez

A leképezési minta használatához a bérlői azonosító értékeknek folyamatos tartományoknak kell lenniük, és elfogadható, hogy az adatbázisok létrehozásakor a tartomány kihagyásával rés legyen a tartományokban.

```powershell
# $ShardMapManager is the shard map manager object
# 'RangeShardMap' is the unique identifier for the range shard map.  
$ShardMap = New-RangeShardMap -KeyType $([int]) -RangeShardMapName 'RangeShardMap' -ShardMapManager $ShardMapManager
```

### <a name="option-3-list-mappings-on-an-individual-database"></a>3. lehetőség: Egyedi adatbázis leképezései

A minta beállításához egy listatérkép létrehozására is szükség van a 2.

## <a name="step-3-prepare-individual-shards"></a>3. lépés: Az egyes szilánkok előkészítése

Adja hozzá az egyes szegmensek (adatbázis) a shard térkép kezelő. Ez előkészíti az egyes adatbázisokat a leképezési adatok tárolására. Ezt a metódust minden szegmensen hajtsa végre.

```powershell
Add-Shard -ShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
# The $ShardMap is the shard map created in step 2.
```

## <a name="step-4-add-mappings"></a>4. lépés: Leképezések hozzáadása

A leképezések hozzáadása a létrehozott shard térkép től függ. Ha létrehozott egy listaleképezést, listaleképezéseket ad hozzá. Ha létrehozott egy tartományleképezést, tartományleképezéseket ad hozzá.

### <a name="option-1-map-the-data-for-a-list-mapping"></a>1. lehetőség: az adatok leképezése listaleképezéshez

Az adatok leképezése az egyes bérlők listájának hozzárendelésével.  

```powershell
# Create the mappings and associate it with the new shards
Add-ListMapping -KeyType $([int]) -ListPoint '<tenant_id>' -ListShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
```

### <a name="option-2-map-the-data-for-a-range-mapping"></a>2. lehetőség: az adatok leképezése tartományleképezéshez

Adja hozzá az összes bérlői azonosítótartomány tartomány- adatbázis-társítások tartományleképezéseit:

```powershell
# Create the mappings and associate it with the new shards
Add-RangeMapping -KeyType $([int]) -RangeHigh '5' -RangeLow '1' -RangeShardMap $ShardMap -SqlServerName '<shard_server_name>' -SqlDatabaseName '<shard_database_name>'
```

### <a name="step-4-option-3-map-the-data-for-multiple-tenants-on-an-individual-database"></a>4. lépés 3.

Minden bérlő, futtassa az Add-ListMapping (1. lehetőség).

## <a name="checking-the-mappings"></a>A leképezések ellenőrzése

A meglévő szegmensekkel és a hozzájuk társított leképezésekkel kapcsolatos információk a következő parancsokkal kérdezhetők le:  

```powershell
# List the shards and mappings
Get-Shards -ShardMap $ShardMap
Get-Mappings -ShardMap $ShardMap
```

## <a name="summary"></a>Összefoglalás

Miután befejezte a telepítést, megkezdheti a rugalmas adatbázis-ügyfélkódtár használatát. [Adatfüggő útválasztást](sql-database-elastic-scale-data-dependent-routing.md) és [többshard lekérdezést](sql-database-elastic-scale-multishard-querying.md)is használhat.

## <a name="next-steps"></a>További lépések

A PowerShell-parancsfájlok beszerezhető az [Azure SQL DB-Elastic Database-eszközök parancsfájljaiból.](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db)

Az eszközök a GitHubon is találhatók: [Azure/elastic-db-tools.](https://github.com/Azure/elastic-db-tools)

A felosztásos egyesítési eszközzel adatokat helyezhet át egy több-bérlős modellbe vagy onnan egyetlen bérlői modellbe. Lásd: [Egyesítési eszköz felosztása](sql-database-elastic-scale-get-started.md).

## <a name="additional-resources"></a>További források

A több bérlős szoftverszolgáltatás (SaaS) típusú adatbázis-alkalmazások általános adatarchitektúra-mintázataival kapcsolatos információk: [Tervminták több-bérlős SaaS-alkalmazásokhoz Azure SQL Database esetén](sql-database-design-patterns-multi-tenancy-saas-applications.md).

## <a name="questions-and-feature-requests"></a>Kérdések és szolgáltatáskérések

Ha kérdése van, használja az [SQL Database fórumot,](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) és a szolgáltatáskérésekhez adja hozzá őket az [SQL Database visszajelzési fórumához.](https://feedback.azure.com/forums/217321-sql-database/)

<!--Image references-->
[1]: ./media/sql-database-elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/sql-database-elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/sql-database-elastic-convert-to-use-elastic-tools/multipleonsingledb.png
