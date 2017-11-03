---
title: "Adatok betöltése az Azure SQL Data Warehouse |} Microsoft Docs"
description: "Ismerje meg, hogy az adatok betöltése az SQL Data Warehouse gyakori forgatókönyvei. Ezek közé tartozik a PolyBase, az Azure blob Storage tárolóban, egybesimított fájlokba és lemez szállítási használatával. Külső eszközöket használhatja."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: 2253bf46-cf72-4de7-85ce-f267494d55fa
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: cakarst;barbkess
ms.openlocfilehash: c4199a387f5cdbd477a5e348e48ba8e8b5900075
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="load-data-into-azure-sql-data-warehouse"></a>Adatok betöltése az Azure SQL Data Warehouse
A forgatókönyv beállítások és adatok betöltése az SQL Data Warehouse javaslatok összegzését.

Az adatok betöltésekor nagyon nehéz részét általában előkészítését végzi, az adatok a terhelést. Azure egyszerűbbé teszi a betöltése az Azure blob storage használatával közös adattárként számos, a szolgáltatások, és Azure Data Factory használatával lehet vezényelni az Azure-szolgáltatások közötti kommunikáció és az adatok mozgása. Ezeket a folyamatokat integrált PolyBase technológia, amely párhuzamosan adatok betöltése az Azure blob-tároló az SQL Data Warehouse nagymértékben párhuzamos feldolgozási (MPP) segítségével. 

Oktatóprogramot kínál, amelyek mintaadatbázisokat betölteni, lásd: [mintaadatbázisokat betölteni][Load sample databases].

## <a name="load-from-azure-blob-storage"></a>Az Azure blob storage betöltése
A leggyorsabban, hogy adatokat importáljon belőlük az SQL Data Warehouse-hoz az Azure blob storage adatok betöltése a PolyBase segítségével. A polybase az SQL Data Warehouse nagymértékben párhuzamos feldolgozási (MPP) tervezési párhuzamosan adatok betöltése az Azure blob storage segítségével. Polybase szolgáltatást akkor használja, használhatja a T-SQL parancsokkal vagy egy Azure Data Factory-folyamathoz.

### <a name="1-use-polybase-and-t-sql"></a>1. A PolyBase és a T-SQL
A betöltési folyamat összegzése:

1. Az adatok áthelyezése az Azure blob-tároló vagy az Azure Data Lake Store, és a szöveges fájlt tárolja.
2. Az SQL Data Warehouse adható meg a hely és az adatok formátumának külső objektumok konfigurálása
3. Adatok betöltése az párhuzamosan egy új adatbázistáblát T-SQL-parancs futtatásával.

<!-- 5. Schedule and run a loading job. --> 

Az oktatóanyagok esetén lásd: [adatok betöltése az Azure blob storage az SQL Data Warehouse-(ba PolyBase)][Load data from Azure blob storage to SQL Data Warehouse (PolyBase)].

### <a name="2-use-azure-data-factory"></a>2. Az Azure Data Factory használata
Leegyszerűsíti a polybase szolgáltatást akkor használja létrehozhat egy Azure Data Factory-folyamathoz, amely az adatok betöltése az Azure blob storage az SQL Data Warehouse PolyBase segítségével. Ez a gyors konfigurálásához, mivel nem kell határoznia a T-SQL-objektumokat. Ha a külső adatok lekérdezése nélkül importálni kell, használja a T-SQL. 

A betöltési folyamat összegzése:

1. Az adatok áthelyezése az Azure blob storage és a szöveges fájlt tárolja. Az Azure Data Factory jelenleg nem támogatja a polybase-zel ADLS-kapcsolat).
2. Hozzon létre egy Azure Data Factory-folyamat az adatok. A PolyBase beállítást használja.
4. Ütemezze, és futtatjuk a folyamatot.

Az oktatóanyagok esetén lásd: [adatok betöltése az Azure blob storage az SQL Data Warehouse (Azure Data Factory)][Load data from Azure blob storage to SQL Data Warehouse (Azure Data Factory)].

## <a name="load-from-sql-server"></a>Betöltése az SQL Server
Adatok betöltése az SQL Server az SQL Data Warehouse az Integration Services (SSIS), a egybesimított fájlok átvitele vagy a Microsoft szállítási lemezek is használhat. Olvasható be a különböző összefoglalását lásd betöltése, folyamatok és oktatóanyagok mutató hivatkozásokat tartalmaz.

Az SQL Data Warehouse az SQL Server teljes adatok áttelepítést végezni, tekintse meg a [áttelepítése – áttekintés][Migration overview]. 

### <a name="use-integration-services-ssis"></a>Használja az Integration Services (SSIS)
Ha már használ való betöltése az SQL Server Integration Services (SSIS) csomag, a csomagok használandó SQL Server a forrás- és az SQL Data Warehouse célhelyként frissítheti. Ez az, hogy gyorsan és könnyen elvégezhető, és akkor hasznos, ha nem kívánt telepítse át a betöltés használandó folyamat adatok már a felhőben. Mi a fontosabb, a betöltés lassabb, mint a PolyBase használatával, mert a SSIS nem végzi el a terhelést párhuzamos lesz.

A betöltési folyamat összegzése:

1. Az Integration Services csomagot az SQL Server-példányt a forrás és a cél az SQL Data Warehouse-adatbázis módosításához.
2. Telepítse át a séma az SQL Data Warehouse még nem létezik.
3. Állítsa a csomagokban megfeleltetését használata csak az SQL Data Warehouse által támogatott adattípusokat.
4. Ütemezze, és futtassa a csomagot.

Az oktatóanyagok esetén lásd: [adatok betöltése az SQL Server Azure SQL Data Warehouse (SSIS)][Load data from SQL Server to Azure SQL Data Warehouse (SSIS)].

### <a name="use-azcopy-recommended-for--10-tb-data"></a>Használja az AZCopy (< 10 TB-adatok esetén ajánlott)
Ha az adatok mérete < 10 TB, akkor is az adatok exportálása az SQL Serverről egybesimított fájlokba, másolja a fájlokat az Azure blob storage és a PolyBase segítségével az adatok betöltése az SQL Data Warehouse

A betöltési folyamat összegzése:

1. A bcp parancssori segédprogram segítségével exportál adatokat az SQL Serverről egybesimított fájlokba.
2. Az AZCopy parancssori segédprogram segítségével másolja az adatok strukturálatlan fájlból az Azure blob storage.
3. Az SQL Data Warehouse betöltése a PolyBase segítségével.

Az oktatóanyagok esetén lásd: [adatok betöltése az Azure blob storage az SQL Data Warehouse-(ba PolyBase)][Load data from Azure blob storage to SQL Data Warehouse (PolyBase)].

### <a name="use-bcp"></a>Használja a bcp
Ha kis mennyiségű adatokat a bcp segítségével közvetlenül az Azure SQL Data Warehouse betölteni.

A betöltési folyamat összegzése:

1. A bcp parancssori segédprogram segítségével exportál adatokat az SQL Serverről egybesimított fájlokba.
2. Bcp segítségével adatok strukturálatlan fájlból közvetlenül az SQL Data Warehouse betöltése.

Az oktatóanyagok esetén lásd: [adatok betöltése az SQL Serverről az Azure SQL Data Warehouse (bcp)][Load data from SQL Server to Azure SQL Data Warehouse (bcp)].

### <a name="use-importexport-recommended-for--10-tb-data"></a>Használja az Import/Export (> 10 TB-adatok esetén ajánlott)
Ha az adatok mérete > 10 TB-os, és áthelyezi azt az Azure-ba, javasoljuk, hogy használja-e a szolgáltatás szállítási lemez [Import/Export][Import/Export]. 

A betöltési folyamat összegzése

1. A bcp parancssori segédprogram használatával adatok exportálása az SQL Serverről egybesimított fájlokba továbbítható lemezeken.
2. Küldje el a Microsoftnak a lemezeket.
3. A Microsoft az adatok betöltése az SQL Data Warehouse

## <a name="load-from-hdinsight"></a>A HDInsight-ból betöltése
Az SQL Data Warehouse polybase HDInsight az adatok betöltését támogatja. A folyamat megegyezik a adatok betöltése az Azure Blob Storage - PolyBase használatával kapcsolódni HDInsight adatainak betöltése. 

### <a name="1-use-polybase-and-t-sql"></a>1. A PolyBase és a T-SQL
A betöltési folyamat összegzése:

1. Az adatok áthelyezése a HDInsight, és tárolja a szövegfájlok, ORC vagy Parquet formátumban.
2. Külső objektumok konfigurálása az SQL Data Warehouse adható meg a hely és az adatok formátumát.
3. Adatok betöltése az párhuzamosan egy új adatbázistáblát T-SQL-parancs futtatásával.

Az oktatóanyagok esetén lásd: [adatok betöltése az Azure blob storage az SQL Data Warehouse-(ba PolyBase)][Load data from Azure blob storage to SQL Data Warehouse (PolyBase)].

## <a name="recommendations"></a>Javaslatok
Partnereink számos rendelkezik megoldások betöltésekor. További tudnivalókért lásd: listáját a [megoldási partnerek][solution partners]. 

Ha az adatokat a nem relációs forrásból származik, és az SQL Data Warehouse irányítópulttá sorok és oszlopok betöltése előtt kell betöltéséhez. Az átalakított adatokat nem szükséges egy adatbázisban kívánja tárolni, szövegfájlok tárolható.

Statisztika létrehozása az újonnan betöltött adatokról. Az Azure SQL Data Warehouse még nem támogatja a statisztikák automatikus létrehozását és frissítését.  A lekérdezés a legjobb teljesítmény eléréséhez fontos létrehozni statisztikákat a táblák összes oszlopához az első betöltés után, vagy az adatok minden lényeges módosítását fordul elő.  További információkért lásd: [statisztika][Statistics].

## <a name="next-steps"></a>Következő lépések
További fejlesztési tippek, tekintse meg a [fejlesztői áttekintés][development overview].

<!--Image references-->

<!--Article references-->
[Load data from Azure blob storage to SQL Data Warehouse (PolyBase)]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Load data from Azure blob storage to SQL Data Warehouse (Azure Data Factory)]: ./sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md
[Load data from SQL Server to Azure SQL Data Warehouse (SSIS)]: ./sql-data-warehouse-load-from-sql-server-with-integration-services.md
[Load data from SQL Server to Azure SQL Data Warehouse (bcp)]: ./sql-data-warehouse-load-from-sql-server-with-bcp.md
[Load data from SQL Server to Azure SQL Data Warehouse (AZCopy)]: ./sql-data-warehouse-load-from-sql-server-with-azcopy.md

[Load sample databases]: ./sql-data-warehouse-load-sample-databases.md
[Migration overview]: ./sql-data-warehouse-overview-migrate.md
[solution partners]: ./sql-data-warehouse-partner-business-intelligence.md
[development overview]: ./sql-data-warehouse-overview-develop.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->

<!--Other Web references-->
[Import/Export]: https://azure.microsoft.com/documentation/articles/storage-import-export-service/
