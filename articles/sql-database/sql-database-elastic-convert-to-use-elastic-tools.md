---
title: Meglévő adatbázisok migrálása a vertikális felskálázáshoz | Microsoft Docs
description: Többrétegű adatbázisok átalakítása rugalmas adatbázis-eszközök használatára egy szegmens Térkép-kezelő létrehozásával
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
ms.openlocfilehash: 2d6d5c51cb381c089633ba010a1d64c8486ddcd8
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568721"
---
# <a name="migrate-existing-databases-to-scale-out"></a>Meglévő adatbázisok migrálása a vertikális felskálázáshoz
A meglévő horizontális felskálázású adatbázisok egyszerűen kezelhetők Azure SQL Database adatbázis-eszközökkel (például a [Elastic Database ügyféloldali kódtár](sql-database-elastic-database-client-library.md)használatával). Először alakítsa át egy meglévő adatbázis-készletet a szegmenses [Térkép kezelőjének](sql-database-elastic-scale-shard-map-management.md)használatára. 

## <a name="overview"></a>Áttekintés
Meglévő, horizontálisan használt adatbázis migrálása: 

1. Készítse elő a szegmens [map Manager-adatbázist](sql-database-elastic-scale-shard-map-management.md).
2. Hozza létre a szegmenses térképet.
3. Készítse elő az egyes szegmenseket.  
4. Leképezések hozzáadása a szegmenses térképhez

Ezek a technikák a [.NET-keretrendszer ügyféloldali függvénytárával](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)vagy az [Azure SQL db-Elastic Database eszközök parancsfájljaiban](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db)található PowerShell-parancsfájlok használatával valósíthatók meg. Az itt található példák a PowerShell-szkripteket használják.

A ShardMapManager kapcsolatos további információkért lásd: a szegmenses [hozzárendelések kezelése](sql-database-elastic-scale-shard-map-management.md). A rugalmas adatbázis-eszközök áttekintését lásd: [Elastic Database funkciók áttekintése](sql-database-elastic-scale-introduction.md).

## <a name="prepare-the-shard-map-manager-database"></a>A szegmens Map Manager adatbázisának előkészítése
A szegmenses Térkép-kezelő egy olyan speciális adatbázis, amely tartalmazza a kibővített adatbázisok kezelésére szolgáló adatmennyiséget. Használhat meglévő adatbázist, vagy létrehozhat egy új adatbázist. A szegmenses Térkép-kezelőként működő adatbázis nem lehet ugyanaz az adatbázis, mint a Szilánk. A PowerShell-parancsfájl nem hozza létre az adatbázist. 

## <a name="step-1-create-a-shard-map-manager"></a>1\. lépés: a szegmenses Térkép kezelőjének létrehozása
    # Create a shard map manager. 
    New-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 
    #<server_name> and <smm_db_name> are the server name and database name 
    # for the new or existing database that should be used for storing 
    # tenant-database mapping information.

### <a name="to-retrieve-the-shard-map-manager"></a>A szegmens Térkép kezelőjének beolvasása
A létrehozást követően lekérheti a szegmens Map Managert ezzel a parancsmaggal. Erre a lépésre minden alkalommal szükség van, amikor a ShardMapManager objektumot kell használnia.

    # Try to get a reference to the Shard Map Manager  
    $ShardMapManager = Get-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 


## <a name="step-2-create-the-shard-map"></a>2\. lépés: a szegmenses Térkép létrehozása
Válassza ki a létrehozandó szegmens-hozzárendelés típusát. A választás az adatbázis-architektúrától függ: 

1. Egyetlen bérlő/adatbázis (a használati feltételekhez lásd [](sql-database-elastic-scale-glossary.md)a szószedetet). 
2. Több bérlő/adatbázis (két típus):
   1. Lista megfeleltetése
   2. Tartomány-hozzárendelés

Egybérlős modell esetén hozzon létre egy **lista** -hozzárendelési szegmens leképezést. Az egybérlős modell bérlőként egy adatbázist rendel hozzá. Ez egy hatékony modell az SaaS-fejlesztők számára, mivel leegyszerűsíti a felügyeletet.

![Lista megfeleltetése][1]

A több-bérlős modell több bérlőt rendel egy adott adatbázishoz (és több adatbázisból is terjesztheti a bérlők csoportjait). Akkor használja ezt a modellt, ha várható, hogy az egyes bérlők kis mennyiségű adatra van szükségük. Ebben a modellben a **tartomány-hozzárendelés**használatával rendeljen hozzá bérlőket egy adatbázishoz. 

![Tartomány-hozzárendelés][2]

Akár több-bérlős adatbázis-modellt is alkalmazhat egy *lista* -hozzárendeléssel, hogy több bérlőt rendeljen hozzá egy adott adatbázishoz. A DB1 például az 1. és az 5. bérlői azonosító adatainak tárolására szolgál, a DB2 pedig a 7. Bérlő és a bérlő 10 adatait tárolja. 

![Több bérlő egyetlen ADATBÁZISon][3] 

**Válasszon egyet az alábbi lehetőségek közül:**

### <a name="option-1-create-a-shard-map-for-a-list-mapping"></a>1\. lehetőség: szegmenses Térkép létrehozása lista-hozzárendeléshez
Hozzon létre egy szegmenses térképet a ShardMapManager objektum használatával. 

    # $ShardMapManager is the shard map manager object. 
    $ShardMap = New-ListShardMap -KeyType $([int]) 
    -ListShardMapName 'ListShardMap' 
    -ShardMapManager $ShardMapManager 


### <a name="option-2-create-a-shard-map-for-a-range-mapping"></a>2\. lehetőség: szegmens leképezés létrehozása tartomány-hozzárendeléshez
A leképezési minta kihasználása érdekében a bérlői azonosító értékének folytonos tartománynak kell lennie, és elfogadható, hogy az adatbázisok létrehozásakor kihagyja a tartományt a tartományokban.

    # $ShardMapManager is the shard map manager object 
    # 'RangeShardMap' is the unique identifier for the range shard map.  
    $ShardMap = New-RangeShardMap 
    -KeyType $([int]) 
    -RangeShardMapName 'RangeShardMap' 
    -ShardMapManager $ShardMapManager 

### <a name="option-3-list-mappings-on-an-individual-database"></a>3\. lehetőség: Egy adott adatbázishoz tartozó hozzárendelések listázása
A minta beállításához szükség van egy lista-hozzárendelés létrehozására is, ahogy azt a 2. lépés, 1. lehetőség mutatja.

## <a name="step-3-prepare-individual-shards"></a>3\. lépés: Egyedi szegmensek előkészítése
Adja hozzá az egyes szegmenseket (adatbázisokat) a szegmenses Térkép kezelőjéhez. Ezzel előkészíti az egyes adatbázisokat a leképezési adatok tárolásához. Hajtsa végre ezt a metódust az egyes szegmenseken.

    Add-Shard 
    -ShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>'
    # The $ShardMap is the shard map created in step 2.


## <a name="step-4-add-mappings"></a>4\. lépés: Leképezések hozzáadása
A leképezések hozzáadása a létrehozott szegmens-leképezés típusától függ. Ha létrehozta a listához tartozó térképet, a lista-hozzárendeléseket is hozzáadhatja. Ha tartományhoz tartozó térképet hozott létre, tartomány-hozzárendeléseket adhat hozzá.

### <a name="option-1-map-the-data-for-a-list-mapping"></a>1\. lehetőség: a lista megfeleltetéséhez tartozó adatleképezés
Rendelje hozzá az adatleképezést az egyes bérlők listájának hozzárendelésével.  

    # Create the mappings and associate it with the new shards 
    Add-ListMapping 
    -KeyType $([int]) 
    -ListPoint '<tenant_id>' 
    -ListShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 

### <a name="option-2-map-the-data-for-a-range-mapping"></a>2\. lehetőség: az adattartomány-hozzárendelések leképezése
Adja hozzá a tartomány-hozzárendeléseket az összes bérlői azonosító tartományhoz – adatbázis-társítások:

    # Create the mappings and associate it with the new shards 
    Add-RangeMapping 
    -KeyType $([int]) 
    -RangeHigh '5' 
    -RangeLow '1' 
    -RangeShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 


### <a name="step-4-option-3-map-the-data-for-multiple-tenants-on-an-individual-database"></a>3\. lépés: a több bérlőre vonatkozó adatleképezés egy adott adatbázisban
Az egyes bérlők esetében futtassa az Add-ListMapping (1. lehetőség). 

## <a name="checking-the-mappings"></a>A leképezések ellenőrzése
A meglévő szegmensekkel és a hozzájuk társított leképezésekkel kapcsolatos információkat a következő parancsokkal kérdezheti le:  

    # List the shards and mappings 
    Get-Shards -ShardMap $ShardMap 
    Get-Mappings -ShardMap $ShardMap 

## <a name="summary"></a>Összegzés
A telepítés befejezése után megkezdheti az Elastic Database ügyféloldali függvénytár használatát. Az Adatfüggő [útválasztást](sql-database-elastic-scale-data-dependent-routing.md) és a [több szegmensre](sql-database-elastic-scale-multishard-querying.md)kiterjedő lekérdezést is használhatja.

## <a name="next-steps"></a>További lépések
A PowerShell-parancsfájlok beszerzése az [Azure SQL db-Elastic Database eszközök parancsfájljaiból](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

Az eszközök a GitHubon is elérhetők: [Azure/rugalmas-db-Tools](https://github.com/Azure/elastic-db-tools).

A Split-Merge eszköz használatával több-bérlős modellbe helyezheti át az adatait egyetlen bérlős modellbe. Lásd: [felosztás egyesítése eszköz](sql-database-elastic-scale-get-started.md).

## <a name="additional-resources"></a>További források
A több bérlős szoftverszolgáltatás (SaaS) típusú adatbázis-alkalmazások általános adatarchitektúra-mintázataival kapcsolatos információk: [Tervminták több-bérlős SaaS-alkalmazásokhoz Azure SQL Database esetén](sql-database-design-patterns-multi-tenancy-saas-applications.md).

## <a name="questions-and-feature-requests"></a>Kérdések és szolgáltatások kérései
Ha kérdése van, használja a [SQL Database fórumot](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) és a szolgáltatásra vonatkozó kérelmeket, vegye fel őket a [SQL Database visszajelzési fórumba](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/sql-database-elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/sql-database-elastic-convert-to-use-elastic-tools/multipleonsingledb.png

