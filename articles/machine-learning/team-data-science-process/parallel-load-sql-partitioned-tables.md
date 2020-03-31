---
title: Párhuzamos tömeges adatok importálása SQL partíciós táblákban – Csapatadat-elemzési folyamat
description: Particionált táblák at hozhat létre az adatok SQL Server-adatbázisba történő gyors párhuzamos tömeges importálásához.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721336"
---
# <a name="build-and-optimize-tables-for-fast-parallel-import-of-data-into-a-sql-server-on-an-azure-vm"></a>Táblák létrehozása és optimalizálása az adatok gyors párhuzamos importálásához egy SQL Serverbe egy Azure virtuális gépen

Ez a cikk azt ismerteti, hogy miként hozhat létre particionált táblákat az adatok SQL Server-adatbázisba történő gyors párhuzamos tömeges importálásához. Az SQL-adatbázisba történő big data be- és átviteléhez az adatok importálása az SQL ADATBÁZISba és az azt követő lekérdezések a *particionált táblák és nézetek*használatával javíthatók. 

## <a name="create-a-new-database-and-a-set-of-filegroups"></a>Új adatbázis és fájlcsoportok létrehozása
* [Hozzon létre egy új adatbázist,](https://technet.microsoft.com/library/ms176061.aspx)ha még nem létezik.
* Adja hozzá az adatbázisfájlcsoportokat a particionált fizikai fájlokat tartalmazó adatbázishoz. 
* Ezt akkor teheti meg [a CREATE DATABASE segítségével,](https://technet.microsoft.com/library/ms176061.aspx) ha az új vagy [az ALTER ADATBÁZIS,](https://msdn.microsoft.com/library/bb522682.aspx) ha az adatbázis már létezik.
* Adjon hozzá egy vagy több fájlt (szükség szerint) minden adatbázisfájlcsoporthoz.
  
  > [!NOTE]
  > Adja meg a partíció adatait tároló célfájlcsoportot és a fájlcsoport adatait tároló fizikai adatbázisfájl-név(eke)t.
  > 
  > 

A következő példa új adatbázist hoz létre az elsődleges és a naplócsoportoktól eltérő három fájlcsoporttal, amelyek mindegyikben egy fizikai fájlt tartalmaznak. Az adatbázisfájlok az sql server-példányban konfigurált alapértelmezett SQL Server Data mappában jönnek létre. Az alapértelmezett fájlhelyekről további információt az [SQL Server alapértelmezett és elnevezett példányainak fájlhelyei című témakörben talál.](https://msdn.microsoft.com/library/ms143547.aspx)

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

## <a name="create-a-partitioned-table"></a>Particionált tábla létrehozása
Ha az előző lépésben létrehozott adatbázis-fájlcsoportokhoz leképezve particionált táblát (séma) szeretne létrehozni, először létre kell hoznia egy partíciófüggvényt és sémát. Ha az adatok tömegesen importálhatók a particionált táblá(ka)ra, a rekordok a fájlcsoportok között partícióséma szerint oszlanak meg, az alábbiakban leírtak szerint.

### <a name="1-create-a-partition-function"></a>1. Partíciófüggvény létrehozása
[Partíciófüggvény létrehozása](https://msdn.microsoft.com/library/ms187802.aspx) Ez a függvény határozza meg az egyes partíciók tábláiban szerepeltetni fogandó értékek/határok\_tartományát, például a partíciók hónap (néhány datetime\_mező) általi korlátozása a 2013-as évben:
  
        CREATE PARTITION FUNCTION <DatetimeFieldPFN>(<datetime_field>)  
        AS RANGE RIGHT FOR VALUES (
            '20130201', '20130301', '20130401',
            '20130501', '20130601', '20130701', '20130801',
            '20130901', '20131001', '20131101', '20131201' )

### <a name="2-create-a-partition-scheme"></a>2. Partícióséma létrehozása
[Hozzon létre egy partíciósémát](https://msdn.microsoft.com/library/ms179854.aspx). Ez a séma a partíciófüggvény minden partíciótartományát egy fizikai fájlcsoporthoz rendeli, például:
  
        CREATE PARTITION SCHEME <DatetimeFieldPScheme> AS  
        PARTITION <DatetimeFieldPFN> TO (
        <filegroup_1>, <filegroup_2>, <filegroup_3>, <filegroup_4>,
        <filegroup_5>, <filegroup_6>, <filegroup_7>, <filegroup_8>,
        <filegroup_9>, <filegroup_10>, <filegroup_11>, <filegroup_12> )
  
  Ha ellenőrizni szeretné az egyes partíciókban a függvény/séma szerinti tartományokat, futtassa a következő lekérdezést:
  
        SELECT psch.name as PartitionScheme,
            prng.value AS PartitionValue,
            prng.boundary_id AS BoundaryID
        FROM sys.partition_functions AS pfun
        INNER JOIN sys.partition_schemes psch ON pfun.function_id = psch.function_id
        INNER JOIN sys.partition_range_values prng ON prng.function_id=pfun.function_id
        WHERE pfun.name = <DatetimeFieldPFN>

### <a name="3-create-a-partition-table"></a>3. Partíciós tábla létrehozása
[Particionált tábla](https://msdn.microsoft.com/library/ms174979.aspx)(táblák) létrehozása az adatséma szerint, és adja meg a tábla particionálásához használt partíciósémát és megkötési mezőt, például:
  
        CREATE TABLE <table_name> ( [include schema definition here] )
        ON <TablePScheme>(<partition_field>)

További információt a [Particionált táblák és indexek létrehozása című témakörben talál.](https://msdn.microsoft.com/library/ms188730.aspx)

## <a name="bulk-import-the-data-for-each-individual-partition-table"></a>Az egyes partíciók adatainak tömeges importálása

* Használhatja a BCP, BULK INSERT vagy más módszereket, például az [SQL Server Áttelepítés varázslót.](https://sqlazuremw.codeplex.com/) A megadott példa a BCP metódust használja.
* [Módosítsa az adatbázist a](https://msdn.microsoft.com/library/bb522682.aspx) tranzakciónaplózási séma módosításához BULK_LOGGED a naplózás terhelésének minimalizálása érdekében, például:
  
        ALTER DATABASE <database_name> SET RECOVERY BULK_LOGGED
* Az adatok betöltésének felgyorsításához indítsa el a tömeges importálási műveleteket párhuzamosan. A big data tömeges importálásának SQL Server-adatbázisokba történő tömeges importálásával kapcsolatos tippek az [1 TB betöltése kevesebb mint 1 óra alatt](https://blogs.msdn.com/b/sqlcat/archive/2006/05/19/602142.aspx)című témakörben olvashatnak.

A következő PowerShell-parancsfájl egy példa a BCP használatával történő párhuzamos adatbetöltésre.

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


## <a name="create-indexes-to-optimize-joins-and-query-performance"></a>Indexek létrehozása az illesztések és a lekérdezési teljesítmény optimalizálásához
* Ha több táblából nyer ki adatokat modellezéshez, hozzon létre indexeket az illesztési kulcsokon az illesztési teljesítmény javítása érdekében.
* [Indexek](https://technet.microsoft.com/library/ms188783.aspx) (fürtözött vagy nem fürtözött) létrehozása, amelyek ugyanazt a fájlcsoportot célozzák meg az egyes partíciókhoz, például:
  
        CREATE CLUSTERED INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
  Vagy
  
        CREATE INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)
  
  > [!NOTE]
  > Dönthet úgy, hogy az indexeket az adatok tömeges importálása előtt hozza létre. Az index létrehozása a tömeges importálás előtt lelassítja az adatok betöltését.
  > 
  > 

## <a name="advanced-analytics-process-and-technology-in-action-example"></a>Speciális elemzési folyamat és technológia működés közben – példa
A csapatadat-elemzési folyamat nyilvános adatkészlettel történő használatával egy teljes körű forgatókönyv-példát a [Team Data Science Process in Action: using Action című témakörben ismer.](sql-walkthrough.md)

