---
title: "Hozza létre, és az adatok gyors párhuzamos importálásakor táblák optimalizálása egy SQL-kiszolgálóra, egy Azure virtuális gépen való |} Microsoft Docs"
description: "Párhuzamos tömeges adatimportálás SQL partíciós táblák használatával"
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: ff90fdb0-5bc7-49e8-aee7-678b54f901c8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2017
ms.author: bradsev
ms.openlocfilehash: 899f20b3642612386f2513c9c8649cd845be826e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="parallel-bulk-data-import-using-sql-partition-tables"></a>Párhuzamos tömeges adatimportálás SQL partíciós táblák használatával
Ez a dokumentum ismerteti, hogyan hozhat létre az SQL Server-adatbázis adatok gyors párhuzamos tömeges importálását a particionált táblákat. A big Data típusú adatok betöltését/átvitel SQL-adatbázishoz, az adatok importálása az SQL-adatbázis és a lekérdezések növelhető a *particionált táblák és nézetek*. 

## <a name="create-a-new-database-and-a-set-of-filegroups"></a>Hozzon létre egy új adatbázist és a fájlcsoport készlete
* [Hozzon létre egy új adatbázist](https://technet.microsoft.com/library/ms176061.aspx), ha már nem létezik.
* Adatbázis fájlcsoport hozzáadása az adatbázisban, ami a particionált fizikai fájlok tárolására. Ezt megteheti a [CREATE DATABASE](https://technet.microsoft.com/library/ms176061.aspx) Ha új vagy [ALTER DATABASE](https://msdn.microsoft.com/library/bb522682.aspx) az adatbázis már létezik.
* Fájlokat adjon hozzá egy vagy több (szükség szerint) minden egyes adatbázis fájlcsoport.
  
  > [!NOTE]
  > Adja meg a cél fájlcsoportban ehhez a partícióhoz adatokat tároló és a fizikai adatbázis fájl vagy fájlcsoport adatok tárolásához.
  > 
  > 

A következő példa egy új adatbázist hoz létre három kívül az elsődleges fájlcsoport és naplófájlcsoportok, az egyes egy fizikai fájlt tartalmazó. Az adatbázisfájlok be az SQL Server-példányt az alapértelmezett SQL Server-adatok mappa jönnek létre. Az alapértelmezett fájlhelyek kapcsolatos további információkért lásd: [fájlhelyek alapértelmezett és az SQL Server példány nevű](https://msdn.microsoft.com/library/ms143547.aspx).

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
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name_2>.ndf'' , SIZE = 4096KB , FILEGROWTH = 1024KB ), 
         FILEGROUP [filegroup_3] 
        ( NAME = ''FileGroup1'', FILENAME = ''' + @data_path + '<file_name>.ndf'' , SIZE = 102400KB , FILEGROWTH = 10240KB ), 
         LOG ON 
        ( NAME = ''LogFileGroup'', FILENAME = ''' + @data_path + '<log_file_name>.ldf'' , SIZE = 1024KB , FILEGROWTH = 10%)
    ')

## <a name="create-a-partitioned-table"></a>Particionált tábla létrehozása
Az adatok séma, a az előző lépésben létrehozott adatbázis fájlcsoportokat leképezve megfelelően particionált táblák létrehozása. Adatok importálása a particionált táblák tömeges esetén rekordok közötti elosztása a fájlcsoportokat megfelelően partícióséma, az alább ismertetett.

**A partíciós tábla létrehozásához kell:**

* [A partíciós függvények létrehozása](https://msdn.microsoft.com/library/ms187802.aspx) amely megadja, hogy az értékek/határok szereplő minden egyes partíciós tábla, pl., partíciók korlátozása havonta tartományán (néhány\_datetime\_mező) 2013-ben:
  
        CREATE PARTITION FUNCTION <DatetimeFieldPFN>(<datetime_field>)  
        AS RANGE RIGHT FOR VALUES (
            '20130201', '20130301', '20130401',
            '20130501', '20130601', '20130701', '20130801',
            '20130901', '20131001', '20131101', '20131201' )
* [Hozzon létre egy partícióséma](https://msdn.microsoft.com/library/ms179854.aspx) amely van leképezve a partíciós függvény minden egyes partíciótartomány fizikai fájlcsoport, pl.:
  
        CREATE PARTITION SCHEME <DatetimeFieldPScheme> AS  
        PARTITION <DatetimeFieldPFN> TO (
        <filegroup_1>, <filegroup_2>, <filegroup_3>, <filegroup_4>,
        <filegroup_5>, <filegroup_6>, <filegroup_7>, <filegroup_8>,
        <filegroup_9>, <filegroup_10>, <filegroup_11>, <filegroup_12> )
  
  Mindegyik partíció, a függvény séma szerint érvényben lévő tartományok ellenőrzéséhez futtassa az alábbi lekérdezést:
  
        SELECT psch.name as PartitionScheme,
            prng.value AS ParitionValue,
            prng.boundary_id AS BoundaryID
        FROM sys.partition_functions AS pfun
        INNER JOIN sys.partition_schemes psch ON pfun.function_id = psch.function_id
        INNER JOIN sys.partition_range_values prng ON prng.function_id=pfun.function_id
        WHERE pfun.name = <DatetimeFieldPFN>
* [Particionált tábla létrehozása](https://msdn.microsoft.com/library/ms174979.aspx)(s) az adatok séma alapján történik, és adja meg a tábla, pl. particionálásához használt partíciós séma és a korlátozás mező:
  
        CREATE TABLE <table_name> ( [include schema definition here] )
        ON <TablePScheme>(<partition_field>)

További információkért lásd: [particionált táblák létrehozása és indexek](https://msdn.microsoft.com/library/ms188730.aspx).

## <a name="bulk-import-the-data-for-each-individual-partition-table"></a>Tömeges importálásához minden egyes partíció tábla adatai
* Előfordulhat, hogy a BCP, TÖMEGES Beszúrás vagy más módszerrel például [SQL Server varázsló](http://sqlazuremw.codeplex.com/). A megadott példa a BCP módszert használja.
* [Az adatbázis módosítására](https://msdn.microsoft.com/library/bb522682.aspx) tranzakció naplózási séma átállítása tömegesen_naplózott naplózás, pl. terhek minimalizálása érdekében:
  
        ALTER DATABASE <database_name> SET RECOVERY BULK_LOGGED
* Adatbetöltési elősegítésére, nyissa meg a tömeges importálási műveletek párhuzamosan. A tömeges meggyorsítása tippek a big Data típusú adatok importálása az SQL Server-adatbázisok, lásd: [1 TB-os betölteni a kevesebb mint 1 óra](http://blogs.msdn.com/b/sqlcat/archive/2006/05/19/602142.aspx).

A következő PowerShell-parancsfájl párhuzamos adatok betöltése a BCP segítségével példája.

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


## <a name="create-indexes-to-optimize-joins-and-query-performance"></a>Illesztések és a lekérdezési teljesítmény optimalizálása érdekében indexek létrehozása
* Ha több tábla rendszer kinyeri az adatokat a modellezési, indexek létrehozása az illesztés kulcsok illesztési teljesítményének javítása.
* [Indexek létrehozása](https://technet.microsoft.com/library/ms188783.aspx) (fürtözött vagy nem fürtözött) célcsoport-kezelési minden partíció esetében azonos fájlcsoport a pl.:
  
        CREATE CLUSTERED INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
  Vagy,
  
        CREATE INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
  
  > [!NOTE]
  > Előfordulhat, hogy az adatok importálása tömeges előtt indexek létrehozása mellett dönt. Az index létrehozása előtt tömeges importálását lelassítja az adatok betöltésekor.
  > 
  > 

## <a name="advanced-analytics-process-and-technology-in-action-example"></a>Speciális elemzésekre folyamat és a technológia művelet példa
A Cortana Analytics folyamat használata a nyilvános dataset végpont forgatókönyv példáért lásd: [Cortana Analytics folyamat működés közben: SQL Server használatával](sql-walkthrough.md).

