<properties 
    pageTitle="使用 SQL 資料分割資料表平行處理大量資料匯入 | Microsoft Azure" 
    description="使用 SQL 資料分割資料表平行處理大量資料匯入" 
    services="machine-learning" 
    documentationCenter="" 
    authors="bradsev"
    manager="paulettm" 
    editor="cgronlun" />

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/11/2015" 
    ms.author="bradsev" /> 


# 使用 SQL 資料分割資料表平行處理大量資料匯入

若要將巨量資料載入/傳輸至 SQL Database，可使用「資料分割資料表和檢視」__，來改善將資料匯入 SQL DB 的程序和後續查詢。 本文件說明如何建置資料分割資料表，以快速的平行處理方式將大量資料匯入 SQL Server 資料庫。


## 建立新的資料庫和一組檔案群組

- [建立新的資料庫](https://technet.microsoft.com/library/ms176061.aspx) (如果不存在)
- 將資料庫檔案群組新增至將用來保留資料分割實體檔案的資料庫
- 注意: 這可以使用 [CREATE DATABASE](https://technet.microsoft.com/library/ms176061.aspx) 如果是新或 [ALTER DATABASE](https://msdn.microsoft.com/library/bb522682.aspx) 如果資料庫已經存在

- 將一或多個檔案 (視需要) 新增至每個資料庫檔案群組
 > [AZURE.NOTE] 指定將保留這個資料分割之資料的目標檔案群組，以及將儲存檔案群組資料的實體資料庫檔案名稱。

下列範例會建立含有三個檔案群組的新資料庫，這三個檔案群組不包括主要和記錄群組，且每個檔案群組中都會包含一個實體檔案。 資料庫檔案建立於預設的 SQL Server [資料] 資料夾中，如 SQL Server 執行個體中所設定。 如需預設檔案位置的詳細資訊，請參閱 [名為 SQL Server 的執行個體和預設的檔案位置](https://msdn.microsoft.com/library/ms143547.aspx)。

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

## 建立資料分割資料表

根據資料結構描述來建立資料分割資料表，其會對應到上一個步驟中建立的資料庫檔案群組。 將資料大量匯入資料分割資料表時，記錄將根據資料分割配置分佈於檔案群組中，如下所述。

**若要建立資料分割資料表，您需要：**

- [建立資料分割函數](https://msdn.microsoft.com/library/ms187802.aspx) 以定義的值/界限包含在每個個別資料分割資料表，例如，2013 年 some\_datetime\_field 限制資料分割範圍:

        CREATE PARTITION FUNCTION <DatetimeFieldPFN>(<datetime_field>)  
        AS RANGE RIGHT FOR VALUES (
            '20130201', '20130301', '20130401',
            '20130501', '20130601', '20130701', '20130801',
            '20130901', '20131001', '20131101', '20131201' )

- [建立資料分割配置](https://msdn.microsoft.com/library/ms179854.aspx) ，將對應的資料分割函數中的每個資料分割範圍到實體檔案群組，例如:

        CREATE PARTITION SCHEME <DatetimeFieldPScheme> AS  
        PARTITION <DatetimeFieldPFN> TO (
        <filegroup_1>, <filegroup_2>, <filegroup_3>, <filegroup_4>,
        <filegroup_5>, <filegroup_6>, <filegroup_7>, <filegroup_8>,
        <filegroup_9>, <filegroup_10>, <filegroup_11>, <filegroup_12> )

- 提示：若要根據函數/配置確認範圍會在每個資料分割中生效，請執行下列查詢：

        SELECT psch.name as PartitionScheme,
            prng.value AS ParitionValue,
            prng.boundary_id AS BoundaryID
        FROM sys.partition_functions AS pfun
        INNER JOIN sys.partition_schemes psch ON pfun.function_id = psch.function_id
        INNER JOIN sys.partition_range_values prng ON prng.function_id=pfun.function_id
        WHERE pfun.name = <DatetimeFieldPFN>

- [建立分割的資料表](https://msdn.microsoft.com/library/ms174979.aspx)(s) 根據您的資料結構描述，並指定資料分割配置和條件約束欄位用來分割資料表，例如:

        CREATE TABLE <table_name> ( [include schema definition here] )
        ON <TablePScheme>(<partition_field>)

- 如需詳細資訊，請參閱 [建立資料分割資料表和索引](https://msdn.microsoft.com/library/ms188730.aspx)。

## 大量匯入每個個別資料分割資料表的資料

- 您可以使用 BCP、 BULK INSERT 或其他方法例如 [SQL Server 移轉精靈](http://sqlazuremw.codeplex.com/)。 所提供的範例會使用 BCP 方法。

- [改變資料庫](https://msdn.microsoft.com/library/bb522682.aspx) 交易記錄配置變更為 BULK_LOGGED，以便記錄，例如額外負荷降至最低:

        ALTER DATABASE <database_name> SET RECOVERY BULK_LOGGED

- 若要加速資料載入，可以平行方式啟動大量匯入作業。 如需大量的秘訣巨量資料的匯入 SQL Server 資料庫，請參閱 [載入 1 TB 的時間少於 1 小時](http://blogs.msdn.com/b/sqlcat/archive/2006/05/19/602142.aspx)。

下列 PowerShell 指令碼是使用 BCP 平行載入資料的範例。

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

## 建立索引以將聯結和查詢效能最佳化

- 如果您會從多個資料表擷取資料來進行模型化，請在聯結索引鍵上建立索引來提升聯結效能。

- [建立索引](https://technet.microsoft.com/library/ms188783.aspx) (叢集或非叢集) 的目標設定相同的檔案群組，每個資料分割，例如:

        CREATE CLUSTERED INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)

或者，

        CREATE INDEX <table_idx> ON <table_name>( [include index columns here] )
        ON <TablePScheme>(<partition)field>)

 > [AZURE.NOTE] 您可以選擇在大量匯入資料之前建立索引。 在大量匯入之前建立索引，將讓資料載入速度變慢。

## 進階分析程序和技術實務範例

如需 Cortana 分析程序使用公用資料集的端對端逐步解說範例，請參閱 [作用中的 Cortana 分析程序: 使用 SQL Server](machine-learning-data-science-process-sql-walkthrough.md)。





