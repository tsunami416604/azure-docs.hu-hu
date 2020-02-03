---
title: Párhuzamos tömeges adatimportálás SQL partíciós táblák – csoportos adatelemzési folyamat
description: Gyors párhuzamos tömeges importálása az SQL Server-adatbázis az adatokat a particionált táblákat hozhat létre.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 673a801e218d055bf482dc97972e36584cddd402
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721336"
---
# <a name="build-and-optimize-tables-for-fast-parallel-import-of-data-into-a-sql-server-on-an-azure-vm"></a>Hozhat létre, és optimalizálhatja a táblák gyors párhuzamos az adatok importálása be egy SQL Server-beli virtuális gépen

Ez a cikk ismerteti, hogyan hozhat létre gyors párhuzamos tömeges importálása az SQL Server-adatbázis az adatokat a particionált táblákat. Az SQL Database-be való betöltéshez, illetve az adatoknak az SQL-adatbázisba történő importálásához és az azt követő lekérdezésekhez a *particionált táblák és nézetek*használatával javítható Big Data. 

## <a name="create-a-new-database-and-a-set-of-filegroups"></a>Hozzon létre egy új adatbázist és a fájlcsoportok
* [Hozzon létre egy új adatbázist](https://technet.microsoft.com/library/ms176061.aspx), ha az még nem létezik.
* Adja hozzá az adatbázis, amely tárolja a particionált fizikai fájlok adatbázis fájlcsoportokat. 
* Ezt az adatbázis [létrehozása](https://technet.microsoft.com/library/ms176061.aspx) lehetőséggel végezheti el, ha az új vagy az [Alter Database](https://msdn.microsoft.com/library/bb522682.aspx) , ha az adatbázis már létezik.
* Adjon hozzá egy vagy több fájlt (igény szerint) minden egyes adatbázis fájlcsoportba.
  
  > [!NOTE]
  > Adja meg a cél fájlcsoportja, a partíció adatait tartalmazó és a fizikai adatbázis-fájl neve, a fájlcsoport adatok tárolására.
  > 
  > 

Az alábbi példa egy új adatbázist hoz létre három eltérő az elsődleges fájlcsoport és naplófájlcsoportok, az egyes egy fizikai fájlt tartalmazó. Az adatbázisfájlokat az SQL Server-adatok alapértelmezett mappát, az SQL Server-példány szerint jönnek létre. További információ az alapértelmezett fájlok helyeiről: a [SQL Server alapértelmezett és elnevezett példányainak elérési helyei](https://msdn.microsoft.com/library/ms143547.aspx).

    DECLARE @data_path nvarchar(256);
    SET @data_path = (SELECT SUBSTRING(physical_name, 1, CHARINDEX(N'master.mdf', LOWER(physical_name)) - 1)
      FROM master.sys.master_files
      WHERE database_id = 1 AND file_id = 1);

    EXECUTE ('
        CREATE DATABASE <database_name>
         ON  PRIMARY 
        ( NAME = ''Primary'', FILENAME = ''' + @data_path + '<primary_file_name>.mdf'', SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_1] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name_1>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_2] 
        ( NAME = ''FileGroup2'', FILENAME = ''' + @data_path + '<file_name_2>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_3] 
        ( NAME = ''FileGroup3'', FILENAME = ''' + @data_path + '<file_name_3>.ndf'' , SIZE = 102400KB , FILEGROWTH = 10240KB ) 
         LOG ON 
        ( NAME = ''LogFileGroup'', FILENAME = ''' + @data_path + '<log_file_name>.ldf'' , SIZE = 1024KB , FILEGROWTH = 10%)
    ')

## <a name="create-a-partitioned-table"></a>Hozzon létre egy particionált táblához
Megfelelően a sémát, leképezve az előző lépésben létrehozott az adatbázis-fájlcsoport particionált táblák létrehozásához először létre kell hoznia egy partíciós függvény és a rendszer. Amikor adatok tömeges a particionált táblák importált, rögzíti a fájlcsoportokat megfelelően partícióséma, többek között oszlanak meg az alább ismertetett.

### <a name="1-create-a-partition-function"></a>1. partition függvény létrehozása
[Partíciós függvény létrehozása](https://msdn.microsoft.com/library/ms187802.aspx) Ez a függvény határozza meg az egyes partíciós táblákban szerepeltethető értékek/határok tartományát, például a partíciók hónapok szerinti korlátozásához (néhány\_datetime\_mező) a 2013-as évben:
  
        CREATE PARTITION FUNCTION <DatetimeFieldPFN>(<datetime_field>)  
        AS RANGE RIGHT FOR VALUES (
            '20130201', '20130301', '20130401',
            '20130501', '20130601', '20130701', '20130801',
            '20130901', '20131001', '20131101', '20131201' )

### <a name="2-create-a-partition-scheme"></a>2. partíciós séma létrehozása
[Hozzon létre egy particionálási sémát](https://msdn.microsoft.com/library/ms179854.aspx). Ennek a sémának felel meg a partíciós függvény minden egyes partíciótartomány fizikai fájlcsoport, például:
  
        CREATE PARTITION SCHEME <DatetimeFieldPScheme> AS  
        PARTITION <DatetimeFieldPFN> TO (
        <filegroup_1>, <filegroup_2>, <filegroup_3>, <filegroup_4>,
        <filegroup_5>, <filegroup_6>, <filegroup_7>, <filegroup_8>,
        <filegroup_9>, <filegroup_10>, <filegroup_11>, <filegroup_12> )
  
  A tartományok érvényben az egyes partíciókban a függvény séma szerint ellenőrzéséhez futtassa a következő lekérdezést:
  
        SELECT psch.name as PartitionScheme,
            prng.value AS PartitionValue,
            prng.boundary_id AS BoundaryID
        FROM sys.partition_functions AS pfun
        INNER JOIN sys.partition_schemes psch ON pfun.function_id = psch.function_id
        INNER JOIN sys.partition_range_values prng ON prng.function_id=pfun.function_id
        WHERE pfun.name = <DatetimeFieldPFN>

### <a name="3-create-a-partition-table"></a>3. partíciós tábla létrehozása
[Hozzon létre particionált táblát](https://msdn.microsoft.com/library/ms174979.aspx)az Adatséma szerint, és adja meg a táblázat particionálásához használt partíciós sémát és korlátozás mezőt, például:
  
        CREATE TABLE <table_name> ( [include schema definition here] )
        ON <TablePScheme>(<partition_field>)

További információ: [particionált táblák és indexek létrehozása](https://msdn.microsoft.com/library/ms188730.aspx).

## <a name="bulk-import-the-data-for-each-individual-partition-table"></a>Tömeges adatimportálás minden egyes partíción táblához

* A BCP, BULK INSERT vagy más módszerekkel, például [SQL Server áttelepítési varázslóval](https://sqlazuremw.codeplex.com/)is használható. A megadott példa a BCP módszert használja.
* Módosítsa [az adatbázist](https://msdn.microsoft.com/library/bb522682.aspx) úgy, hogy a naplózási séma méretének csökkentése érdekében BULK_LOGGED a tranzakció naplózási sémáját, például:
  
        ALTER DATABASE <database_name> SET RECOVERY BULK_LOGGED
* Gyorsíthatja fel az adatok betöltése, nyissa meg a tömeges importálási műveletek párhuzamosan. A big data SQL Server adatbázisba való tömeges importálásának továbbítására vonatkozó tippekért lásd: [1 TB-os terhelés kevesebb mint 1 óra](https://blogs.msdn.com/b/sqlcat/archive/2006/05/19/602142.aspx).

A következő PowerShell-parancsfájlt, amelyek párhuzamosan adatok betöltése a BCP használatával.

    # Set database name, input data directory, and output log directory
    # This example loads comma-separated input data files
    # The example assumes the partitioned data files are named as <base_file_name>_<partition_number>.csv
    # Assumes the input data files include a header line. Loading starts at line number 2.

    $dbname = "<database_name>"
    $indir  = "<path_to_data_files>"
    $logdir = "<path_to_log_directory>"

    # Select authentication mode
    $sqlauth = 0

    # For SQL authentication, set the server and user credentials
    $sqlusr = "<user@server>"
    $server = "<tcp:serverdns>"
    $pass   = "<password>"

    # Set number of partitions per table - Should match the number of input data files per table
    $numofparts = <number_of_partitions>

    # Set table name to be loaded, basename of input data files, input format file, and number of partitions
    $tbname = "<table_name>"
    $basename = "<base_input_data_filename_no_extension>"
    $fmtfile = "<full_path_to_format_file>"

    # Create log directory if it does not exist
    New-Item -ErrorAction Ignore -ItemType directory -Path $logdir

    # BCP example using Windows authentication
    $ScriptBlock1 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -T -b 2500 -t "," -r \n
    }

    # BCP example using SQL authentication
    $ScriptBlock2 = {
       param($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $num, $sqlusr, $server, $pass)
       bcp ($dbname + ".." + $tbname) in ($indir + "\" + $basename + "_" + $num + ".csv") -o ($logdir + "\" + $tbname + "_" + $num + ".txt") -h "TABLOCK" -F 2 -C "RAW" -f ($fmtfile) -U $sqlusr -S $server -P $pass -b 2500 -t "," -r \n
    }

    # Background processing of all partitions
    for ($i=1; $i -le $numofparts; $i++)
    {
       Write-Output "Submit loading trip and fare partitions # $i"
       if ($sqlauth -eq 0) {
          # Use Windows authentication
          Start-Job -ScriptBlock $ScriptBlock1 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i)
       } 
       else {
          # Use SQL authentication
          Start-Job -ScriptBlock $ScriptBlock2 -Arg ($dbname, $tbname, $basename, $fmtfile, $indir, $logdir, $i, $sqlusr, $server, $pass)
       }
    }

    Get-Job

    # Optional - Wait till all jobs complete and report date and time
    date
    While (Get-Job -State "Running") { Start-Sleep 10 }
    date


## <a name="create-indexes-to-optimize-joins-and-query-performance"></a>Illesztés és a lekérdezési teljesítmény optimalizálása érdekében az indexek létrehozása
* Ha több tábla modellezési adatok kinyerése, indexet hoz létre a join kulcsok illesztési teljesítményének javítása érdekében.
* [Hozzon létre](https://technet.microsoft.com/library/ms188783.aspx) az egyes partíciók azonos fájlcsoportja célzó indexeket (fürtözött vagy nem fürtözött), például:
  
        CREATE CLUSTERED INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
  vagy,
  
        CREATE INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
  
  > [!NOTE]
  > Előfordulhat, hogy szeretne létrehozni az indexek az adatok importálása tömeges előtt. Az Adatbetöltési lelassítja az index-létrehozási tömeges importálás előtt.
  > 
  > 

## <a name="advanced-analytics-process-and-technology-in-action-example"></a>Fejlett analitikai folyamat és technológia művelet példában
Egy teljes körű bemutató példa a csoportos adatelemzési folyamat nyilvános adatkészlettel való használatával kapcsolatban [: csoportos adatelemzési folyamat működés közben: SQL Server használata](sql-walkthrough.md).

