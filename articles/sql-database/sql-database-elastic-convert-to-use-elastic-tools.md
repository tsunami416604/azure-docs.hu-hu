---
title: Horizontális felskálázás meglévő adatbázisok migrálása |} A Microsoft Docs
description: Elastic database-eszközök használatához hozzon létre egy szegmens kezelő szilánkokra osztott adatbázisok konvertálása
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
ms.date: 04/01/2018
ms.openlocfilehash: 03e1974a91a8c3cceacab777e28e8e4a01ccb313
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51251593"
---
# <a name="migrate-existing-databases-to-scale-out"></a>Horizontális felskálázás meglévő adatbázisok migrálása
Könnyedén kezelheti a meglévő horizontálisan skálázott horizontálisan felskálázott adatbázisok Azure SQL Database-adatbázis eszközök segítségével (mint például a [Elastic Database-ügyfélkódtár](sql-database-elastic-database-client-library.md)). Egy meglévő készlethez adatbázisok használatához először konvertálja a [szilánkleképezés-kezelővel](sql-database-elastic-scale-shard-map-management.md). 

## <a name="overview"></a>Áttekintés
Egy meglévő, szilánkokra osztott adatbázis áttelepítése: 

1. Készítse elő a [szegmenstérkép-kezelő adatbázis](sql-database-elastic-scale-shard-map-management.md).
2. Hozzon létre a horizontális skálázási térképet.
3. Készítse elő az egyes szegmensekben.  
4. Leképezések hozzáadása a horizontális skálázási térképet.

Ezek a technológiák segítségével valósítható meg vagy a [.NET-keretrendszer ügyféloldali kódtár](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/), vagy a PowerShell-parancsfájlok címen található [Azure SQL Database – rugalmas adatbázis eszközök parancsfájlok](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db). Az itt szereplő példák a PowerShell-parancsfájlokat használja.

A ShardMapManager kapcsolatos további információkért lásd: [Szilánkleképezés-kezelés](sql-database-elastic-scale-shard-map-management.md). A rugalmas Adatbáziseszközök áttekintését lásd: [Elastic Database funkcióit áttekintő](sql-database-elastic-scale-introduction.md).

## <a name="prepare-the-shard-map-manager-database"></a>Készítse elő a szegmenstérkép-kezelő adatbázis
A szilánkleképezés-kezelő egy speciális, horizontálisan felskálázott adatbázisok kezelése az adatokat tartalmazó adatbázis. Egy meglévő adatbázis használatát, vagy hozzon létre egy új adatbázist. Szilánkleképezés-kezelővel átjáróként adatbázis nem lehet azonos adatbázist használnak egy szegmens. A PowerShell-parancsprogram nem hoz létre az adatbázis az Ön számára. 

## <a name="step-1-create-a-shard-map-manager"></a>1. lépés: hozzon létre egy szegmens kezelő
    # Create a shard map manager. 
    New-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 
    #<server_name> and <smm_db_name> are the server name and database name 
    # for the new or existing database that should be used for storing 
    # tenant-database mapping information.

### <a name="to-retrieve-the-shard-map-manager"></a>A szilánkleképezés-kezelővel lekéréséhez
A létrehozás után a szilánkleképezés-kezelővel ezzel a parancsmaggal kérheti le. Ez a lépés akkor szükséges, minden alkalommal kell használnia a ShardMapManager objektum.

    # Try to get a reference to the Shard Map Manager  
    $ShardMapManager = Get-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 


## <a name="step-2-create-the-shard-map"></a>2. lépés: a szegmenstérkép létrehozása
Válassza ki a szegmenstérkép hozhat létre. A választás attól függ, hogy az adatbázis-architektúra: 

1. Az adatbázisonkénti egyetlen bérlőt (feltételei, lásd: a [szószedet](sql-database-elastic-scale-glossary.md).) 
2. Több bérlő adatbázisonként (két típusa):
   1. Lista leképezés
   2. Tartomány leképezése

Hozzon létre egy egybérlős modellbe a **lista leképezés** horizontális skálázási térképet. Az egybérlős adatbázismodell bérlőnként egy adatbázis rendeli hozzá. Ez a lehetőség SaaS-fejlesztők számára modellje egyszerűbbé téve a felügyelet.

![Lista leképezés][1]

A több-bérlős modell egy önálló adatbázis rendel hozzá a több bérlő (és a bérlők csoportok juttathatja el több adatbázis között). Ezt a modellt használja, ha várhatóan minden bérlő, kis méretű adatokat kell rendelkeznie. Ebben a modellben bérlők számos hozzárendelése egy adatbázis használatával **tartomány hozzárendelési**. 

![Tartomány leképezése][2]

Megvalósíthat egy több-bérlős adatbázis modellt használja, vagy egy *lista leképezés* több bérlő hozzárendelése egy önálló adatbázis. Ha például DB1 bérlői azonosító 1 és 5 kapcsolatos információk tárolására szolgál, és DB2 7 bérlői és bérlői 10 adatait tárolja. 

![Több bérlő egyetlen DB][3] 

**A választott alapján, válassza ki az alábbi lehetőségek egyikét:**

### <a name="option-1-create-a-shard-map-for-a-list-mapping"></a>1. lehetőség: hozzon létre egy listát a leképezés horizontálispartíció-térkép
Hozzon létre egy horizontális skálázási térképet a ShardMapManager objektummal. 

    # $ShardMapManager is the shard map manager object. 
    $ShardMap = New-ListShardMap -KeyType $([int]) 
    -ListShardMapName 'ListShardMap' 
    -ShardMapManager $ShardMapManager 


### <a name="option-2-create-a-shard-map-for-a-range-mapping"></a>2. lehetőség: horizontálispartíció-térkép, a tartomány-hozzárendelés létrehozása
Ezt a hozzárendelést mintát használja, a bérlői azonosító értéket kell lennie a folyamatos tartományokat, és elfogadott, hogy rendelkezik a tartományokat a tartalma alapján a rendszer kihagyja a tartomány az adatbázis létrehozásakor.

    # $ShardMapManager is the shard map manager object 
    # 'RangeShardMap' is the unique identifier for the range shard map.  
    $ShardMap = New-RangeShardMap 
    -KeyType $([int]) 
    -RangeShardMapName 'RangeShardMap' 
    -ShardMapManager $ShardMapManager 

### <a name="option-3-list-mappings-on-a-single-database"></a>3. lehetőség: Lista leképezése egy önálló adatbázis
Ezt a mintát beállítása van szükség egy lista térkép létrehozása is, ahogyan a 2. lépéshez, 1. lehetőség.

## <a name="step-3-prepare-individual-shards"></a>3. lépés: Készítse elő az egyes szegmensek
Adja hozzá a szilánkleképezés-kezelő mindegyik szegmens (adatbázis). Ez előkészíti az egyes adatbázisok leképezés információk tárolására. Ez a módszer minden egyes szegmens hajtható végre.

    Add-Shard 
    -ShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>'
    # The $ShardMap is the shard map created in step 2.


## <a name="step-4-add-mappings"></a>4. lépés: A leképezések hozzáadása
Leképezések hozzáadása létrehozott szegmenstérkép típusától függ. Ha létrehozott egy lista térképet, lista leképezések adja hozzá. Ha tartományt térképén hozta létre, tartomány leképezések ad hozzá.

### <a name="option-1-map-the-data-for-a-list-mapping"></a>1. lehetőség: az adatok egy lista a leképezés leképezése
Képezze le az adatokat egy lista leképezés hozzáadásával az egyes bérlők számára.  

    # Create the mappings and associate it with the new shards 
    Add-ListMapping 
    -KeyType $([int]) 
    -ListPoint '<tenant_id>' 
    -ListShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 

### <a name="option-2-map-the-data-for-a-range-mapping"></a>2. lehetőség: az adatok a tartomány-hozzárendelés leképezése
Adja hozzá a tartomány leképezések minden bérlői azonosító tartomány - adatbázis-társítások:

    # Create the mappings and associate it with the new shards 
    Add-RangeMapping 
    -KeyType $([int]) 
    -RangeHigh '5' 
    -RangeLow '1' 
    -RangeShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 


### <a name="step-4-option-3-map-the-data-for-multiple-tenants-on-a-single-database"></a>4. lépés: 3. lehetőség: az adatok leképezése egy önálló adatbázisban több bérlő számára
Minden egyes bérlőhöz futtassa az Add-ListMapping (1. lehetőség). 

## <a name="checking-the-mappings"></a>A leképezések ellenőrzése
A meglévő szegmensek és a hozzájuk társított leképezések kapcsolatos információk lekérdezhetők, használja a következő parancsokat:  

    # List the shards and mappings 
    Get-Shards -ShardMap $ShardMap 
    Get-Mappings -ShardMap $ShardMap 

## <a name="summary"></a>Összegzés
A telepítés befejezése után elkezdheti használni az Elastic Database ügyfélkódtár. Is [Adatfüggő útválasztásnak](sql-database-elastic-scale-data-dependent-routing.md) és [többszegmenses lekérdezés](sql-database-elastic-scale-multishard-querying.md).

## <a name="next-steps"></a>További lépések
A PowerShell-parancsprogramok, az első [Azure SQL DB-Elastic Database-eszközök parancsfájlok](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

Az eszközök emellett a Githubon érhetők el: [Azure/rugalmas-db-tools-](https://github.com/Azure/elastic-db-tools).

A felosztási-egyesítési eszközének használatával adatok áthelyezése, illetve a több-bérlős modell egy egybérlős modellbe. Lásd: [felosztási-egyesítési eszközének](sql-database-elastic-scale-get-started.md).

## <a name="additional-resources"></a>További források
A több bérlős szoftverszolgáltatás (SaaS) típusú adatbázis-alkalmazások általános adatarchitektúra-mintázataival kapcsolatos információk: [Tervminták több-bérlős SaaS-alkalmazásokhoz Azure SQL Database esetén](sql-database-design-patterns-multi-tenancy-saas-applications.md).

## <a name="questions-and-feature-requests"></a>Kérdések és a Funkciókérések
Ha kérdése van, használja a [SQL Database fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) és a funkciókérések, adja hozzá őket a [SQL Database-visszajelzési fórumon](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/sql-database-elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/sql-database-elastic-convert-to-use-elastic-tools/multipleonsingledb.png

