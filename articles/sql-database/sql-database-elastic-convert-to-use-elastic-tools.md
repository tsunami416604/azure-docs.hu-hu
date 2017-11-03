---
title: "Telepítse át a meglévő adatbázisok horizontális |} Microsoft Docs"
description: "Horizontálisan skálázott adatbázisokat rugalmas adatbáziseszközöket használhat hozzon létre egy shard térkép manager átalakítása"
services: sql-database
documentationcenter: 
author: ddove
manager: jhubbard
editor: 
ms.assetid: 8c851d8e-8fd5-4327-89c1-9178b20ddd69
ms.service: sql-database
ms.custom: scale out apps
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Inactive
ms.date: 10/24/2016
ms.author: ddove
ms.openlocfilehash: 356c4223ff3ae844552b7bee40aa3ffc6aad7ea0
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="migrate-existing-databases-to-scale-out"></a>Telepítse át a meglévő adatbázisok kívánt réteget
Könnyedén felügyelheti a meglévő szilánkos kiterjesztett adatbázisok Azure SQL Database-adatbázis eszközökkel (például a [Elastic Database ügyféloldali kódtárának](sql-database-elastic-database-client-library.md)). Először alakítsa át az adatbázisokat kell használni, a készlet a [shard térkép manager](sql-database-elastic-scale-shard-map-management.md). 

## <a name="overview"></a>Áttekintés
Meglévő szilánkos adatbázis áttelepítéséhez: 

1. Készítse elő a [shard manager adatbázist](sql-database-elastic-scale-shard-map-management.md).
2. A szilánkok térkép létrehozásához.
3. Az egyes szilánkok előkészítése.  
4. Adja hozzá a shard térkép leképezéseket.

Ezek a technológiák segítségével kell végrehajtani a [.NET-keretrendszer ügyféloldali kódtár](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/), vagy a PowerShell-parancsfájlok a következő címen található [Azure SQL Database - rugalmas adatbázis eszközök parancsfájlok](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db). Itt a példák a PowerShell-parancsfájlok használata.

A ShardMapManager kapcsolatos további információkért lásd: [Shard térkép felügyeleti](sql-database-elastic-scale-shard-map-management.md). A rugalmas adatbáziseszközöket áttekintését lásd: [rugalmas adatbázis-szolgáltatások áttekintése](sql-database-elastic-scale-introduction.md).

## <a name="prepare-the-shard-map-manager-database"></a>A szilánkok térkép manager-adatbázis előkészítése
A szilánkok térkép manager különleges adatbázis kiterjesztett adatbázisok kezelése az adatokat tartalmazza. Használjon egy meglévő adatbázist, vagy hozzon létre egy új adatbázist. A shard térkép manager működött adatbázis nem lehet azonos adatbázist használnak a szilánkcímtárban. A PowerShell-parancsfájl nem hoz létre az adatbázis meg. 

## <a name="step-1-create-a-shard-map-manager"></a>1. lépés: hozzon létre egy shard térkép manager
    # Create a shard map manager. 
    New-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 
    #<server_name> and <smm_db_name> are the server name and database name 
    # for the new or existing database that should be used for storing 
    # tenant-database mapping information.

### <a name="to-retrieve-the-shard-map-manager"></a>A szilánkok térkép manager beolvasása
A létrehozás után a shard térkép manager ezzel a parancsmaggal kérheti le. Ez a lépés akkor szükséges, minden alkalommal, amikor kell használnia a ShardMapManager objektum.

    # Try to get a reference to the Shard Map Manager  
    $ShardMapManager = Get-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 


## <a name="step-2-create-the-shard-map"></a>2. lépés: a shard térkép létrehozásához.
Válassza ki a shard térkép létrehozásához a típusát. A választott attól függ, hogy az adatbázis-architektúra: 

1. Adatbázisonként egybérlős (feltételeit, tekintse meg a [szószedet](sql-database-elastic-scale-glossary.md).) 
2. Több bérlő adatbázisonként (két típus):
   1. Lista leképezése
   2. Tartomány leképezése

Single-bérlő modell, hozzon létre egy **lista leképezési** shard leképezés. A single-bérlős modell bérlőnként egy adatbázis rendeli hozzá. Ez megegyezik egy hatékony modell Szolgáltatottszoftver-fejlesztőknek egyszerűbbé teszi a felügyeleti.

![Lista leképezése][1]

A több-bérlős modell több bérlő rendel hozzá egy adatbázist (és a bérlő csoportok szét több adatbázis). Ezt a modellt használja, ha várhatóan mindegyik bérlő kisméretű kell rendelkeznie. Ebben a modellben bérlők számos hozzárendelése egy adatbázis használatával **tartomány leképezése**. 

![Tartomány leképezése][2]

Megvalósíthat egy adatbázis több-bérlős modell használatával vagy egy *lista leképezési* több bérlő hozzárendelése egy adatbázist. Például a D1 bérlői azonosító 1 és 5 információ tárolására szolgál, és DB2 eltárolja 7 bérlői és bérlői 10. 

![Az egyetlen DB több bérlő][3] 

**A kiválasztott alapján, válassza ki az alábbi lehetőségek egyikét:**

### <a name="option-1-create-a-shard-map-for-a-list-mapping"></a>1. lehetőség: a lista hozzárendelés shard térkép létrehozásához.
A ShardMapManager objektummal shard térkép létrehozásához. 

    # $ShardMapManager is the shard map manager object. 
    $ShardMap = New-ListShardMap -KeyType $([int]) 
    -ListShardMapName 'ListShardMap' 
    -ShardMapManager $ShardMapManager 


### <a name="option-2-create-a-shard-map-for-a-range-mapping"></a>2. lehetőség: a tartomány hozzárendelés shard térkép létrehozásához.
A leképezési mintát használják, a bérlői azonosító értékekkel kell lennie a folyamatos tartományokat, és elfogadható tartományok résnek rendelkezik a tartomány átugrásával, ha az adatbázisok létrehozásához.

    # $ShardMapManager is the shard map manager object 
    # 'RangeShardMap' is the unique identifier for the range shard map.  
    $ShardMap = New-RangeShardMap 
    -KeyType $([int]) 
    -RangeShardMapName 'RangeShardMap' 
    -ShardMapManager $ShardMapManager 

### <a name="option-3-list-mappings-on-a-single-database"></a>3. lehetőség: Lista leképezése egy önálló adatbázis
A lista leképezés létrehozása is ebben a mintában beállítása igényel, ahogy az 1. lehetőség 2. lépést.

## <a name="step-3-prepare-individual-shards"></a>3. lépés: Felkészülés az egyes szilánkok
Adja hozzá a shard térkép manager minden shard (adatbázis). Ez az egyes adatbázisok előkészítése leképezési információk tárolására. Ez a módszer minden shard hajtható végre.

    Add-Shard 
    -ShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>'
    # The $ShardMap is the shard map created in step 2.


## <a name="step-4-add-mappings"></a>4. lépés: Hozzárendelések hozzáadása
A leképezések hozzáadását létrehozott shard térkép fajtájától függ. Ha létrehozott egy lista térkép, hozzá kell adnia hozzárendelések listáját. Ha létrehozott egy tartomány-társítást, hozzá kell adnia tartomány leképezéseket.

### <a name="option-1-map-the-data-for-a-list-mapping"></a>1. lehetőség: az adatok a lista hozzárendelés leképezése
Az adatok leképezése egy lista leképezése hozzáadásával az egyes bérlők számára.  

    # Create the mappings and associate it with the new shards 
    Add-ListMapping 
    -KeyType $([int]) 
    -ListPoint '<tenant_id>' 
    -ListShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 

### <a name="option-2-map-the-data-for-a-range-mapping"></a>2. lehetőség: az adatok a tartományon a leképezés leképezése
Adja hozzá az összes a bérlői azonosító tartomány - adatbázis társítások tartomány leképezéseit:

    # Create the mappings and associate it with the new shards 
    Add-RangeMapping 
    -KeyType $([int]) 
    -RangeHigh '5' 
    -RangeLow '1' 
    -RangeShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 


### <a name="step-4-option-3-map-the-data-for-multiple-tenants-on-a-single-database"></a>4. lépés: 3. lehetőség: egy adatbázist a több bérlő számára az adatok leképezése
Az egyes bérlők, futtassa az Add-ListMapping (1. lehetőség). 

## <a name="checking-the-mappings"></a>A leképezések ellenőrzése
A meglévő szilánkok és a hozzájuk társított leképezések kapcsolatos információk lekérdezhetők, következő parancsok használatával:  

    # List the shards and mappings 
    Get-Shards -ShardMap $ShardMap 
    Get-Mappings -ShardMap $ShardMap 

## <a name="summary"></a>Összefoglalás
A telepítés befejezése után megkezdheti az Elastic Database ügyféloldali kódtár használata. Is [adatok függő útválasztási](sql-database-elastic-scale-data-dependent-routing.md) és [több shard lekérdezés](sql-database-elastic-scale-multishard-querying.md).

## <a name="next-steps"></a>Következő lépések
A PowerShell-parancsfájlok az beszerzése [Azure SQL DB-rugalmas adatbázis eszközök parancsfájlok](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

Az eszközök megtalálhatók a Githubon: [Azure/rugalmas-db-eszközök](https://github.com/Azure/elastic-db-tools).

A felosztott egyesítéses eszközzel áthelyezéséhez adatok vagy a több-bérlős modell egybérlős modellre. Lásd: [vegyes egyesítő eszköz](sql-database-elastic-scale-get-started.md).

## <a name="additional-resources"></a>További források
A több bérlős szoftverszolgáltatás (SaaS) típusú adatbázis-alkalmazások általános adatarchitektúra-mintázataival kapcsolatos információk: [Tervminták több-bérlős SaaS-alkalmazásokhoz Azure SQL Database esetén](sql-database-design-patterns-multi-tenancy-saas-applications.md).

## <a name="questions-and-feature-requests"></a>Kérdések és funkciókra vonatkozó kérések
Kérdéseit, használja a [SQL-adatbázis fórum](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) , a szolgáltatás kéréseket, hozzáadhatja őket a a [SQL adatbázis-visszajelzési fórumon](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/sql-database-elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/sql-database-elastic-convert-to-use-elastic-tools/multipleonsingledb.png

