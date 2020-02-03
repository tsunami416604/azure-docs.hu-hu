---
title: Adatok áthelyezése az SQL Server virtuális gép – csoportos adatelemzési folyamat
description: Adatok áthelyezése vagy egy helyszíni SQL Serverről egybesimított fájlokba, az SQL Server Azure virtuális gépen.
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
ms.openlocfilehash: b8a01b5f2f5ec64fea014468356408220f9c4f1a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721370"
---
# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Adatok áthelyezés SQL Server-kiszolgálóra Azure-beli virtuális gépeken

Ez a cikk ismerteti a lehetőség egyszerű fájlok (CSV- vagy TSV formátumok) vagy egy helyszíni SQL Serverről az SQL Server-beli virtuális gépen. Adatok áthelyezése a felhőbe ezeket a feladatokat a csoportos adatelemzési folyamat részét képezik.

Egy olyan témakörben, amely az adatáthelyezési beállításokat ismerteti Machine Learning Azure SQL Databasere, tekintse meg az [adatáthelyezés egy Azure SQL Databasere Azure Machine learning](move-sql-azure.md)számára című témakört.

A következő táblázat összefoglalja a lehetőség az SQL Server-beli virtuális gépen.

| <b>FORRÁS</b> | <b>CÉL: SQL Server Azure-beli virtuális gépen</b> |
| --- | --- |
| <b>Sima fájl</b> |1. <a href="#insert-tables-bcp">parancssori tömeges másolási segédprogram (BCP)</a><br> 2. <a href="#insert-tables-bulkquery">tömeges beszúrási SQL-lekérdezés</a><br> 3. <a href="#sql-builtin-utilities">a SQL Server grafikus beépített segédprogramjai</a> |
| <b>Helyszíni SQL Server</b> |1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">SQL Server-adatbázis üzembe helyezése egy Microsoft Azure VM-varázslóban</a><br> 2. <a href="#export-flat-file">Exportálás egy egyszerű fájlba</a><br> 3. <a href="#sql-migration">SQL Database áttelepítési varázsló</a> <br> 4. <a href="#sql-backup">az adatbázis biztonsági mentése és visszaállítása</a><br> |

Ez a dokumentum azt feltételezi, hogy az SQL-parancsok végrehajtása SQL Server Management Studio vagy Visual Studio Adatbázis-böngészőból történik.

> [!TIP]
> Alternatív megoldásként a [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) használatával létrehozhat és ütemezhet egy olyan folyamatot, amely az Azure-beli SQL Server VMba helyezi át az adatcsatornákat. További információ: [adatok másolása Azure Data Factorysal (másolási tevékenység)](../../data-factory/copy-activity-overview.md).
>
>

## <a name="prereqs"></a>Előfeltételek
Ez az oktatóanyag feltételezi, hogy:

* Egy **Azure-előfizetés**. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/).
* Egy **Azure Storage-fiók**. Ez az oktatóanyag az adatok tárolása Azure storage-fiók használandó. Ha nem rendelkezik Azure Storage-fiókkal, tekintse meg a [Storage-fiók létrehozása](../../storage/common/storage-account-create.md) című cikket. Miután létrehozta a tárfiókot, szüksége lesz a a tárterület elérésére használt fiók kulcs beszerzése. Lásd: a [Storage-fiók elérési kulcsainak kezelése](../../storage/common/storage-account-keys-manage.md).
* Kiépített **SQL Server egy Azure-beli virtuális gépen**. Útmutatásért lásd: [Azure SQL Server virtuális gép beállítása IPython notebook-kiszolgálóként a speciális elemzésekhez](../data-science-virtual-machine/setup-sql-server-virtual-machine.md).
* **Azure PowerShell** helyileg telepítve és konfigurálva. Útmutatásért lásd: [Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview).

## <a name="filesource_to_sqlonazurevm"></a>Adatok áthelyezése egy egyszerű fájlból a SQL Serverba egy Azure-beli virtuális gépen
Ha az adatok egy egybesimított fájlt, (rendezett sor/oszlop formátumban), azt helyezheti át SQL Server rendszerű virtuális gép az Azure-on keresztül az alábbi módszerek:

1. [Parancssori tömeges másolási segédprogram (BCP)](#insert-tables-bcp)
2. [SQL-lekérdezés tömeges beszúrása](#insert-tables-bulkquery)
3. [Grafikus beépített segédprogramok a SQL Serverban (Importálás/exportálás, SSIS)](#sql-builtin-utilities)

### <a name="insert-tables-bcp"></a>Parancssori tömeges másolási segédprogram (BCP)
BCP parancssori segédprogram az SQL Server telepítve, és egyik adatok áthelyezése a leggyorsabb módja. Mindhárom SQL Server változatban működik (helyszíni SQL Server, SQL Azure és SQL Server VM az Azure-ban).

> [!NOTE]
> **Hol kell az adataim a BCP-hez?**  
> Bár nem kötelező, és a cél SQL Server ugyanazon a számítógépen található adatforrás-adatokat tartalmazó fájlok lehetővé, hogy a gyorsabb adatátvitel (hálózati sebesség és helyi lemez i/o-sebesség). Áthelyezheti az adatok tárolására szolgáló sima fájlokat a gépre, ahol a SQL Server a különböző fájlmásolás-eszközök, például a [AZCopy](../../storage/common/storage-use-azcopy.md), a [Azure Storage Explorer](https://storageexplorer.com/) vagy a Windows másolási/beillesztési RDP protokoll (RDP) használatával telepíthetők.
>
>

1. Győződjön meg arról, hogy az adatbázis és a táblázatok jönnek létre a cél SQL Server-adatbázist. Íme egy példa arra, hogyan végezheti el a `Create Database` és `Create Table` parancsok használatát:

    ```sql
    CREATE DATABASE <database_name>
    
    CREATE TABLE <tablename>
    (
        <columnname1> <datatype> <constraint>,
        <columnname2> <datatype> <constraint>,
        <columnname3> <datatype> <constraint>
    )
    ```

1. Létrehozhatja a táblázat sémáját leíró formátumú fájlt a következő parancs kiadásával azon a számítógépen, ahol a BCP telepítve van.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`
1. Szúrja be az adatait az adatbázisba a BCP paranccsal, amelynek a parancssorból kell működnie, ha SQL Server ugyanarra a gépre van telepítve:

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attempt -t \t -r \n`

> **BCP-beszúrások optimalizálása** A lapkák optimalizálásához tekintse meg a következő cikket ["a tömeges importálás optimalizálásához"](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx) című cikkben.
>
>

### <a name="insert-tables-bulkquery-parallel"></a>Tetszés lapkák a gyorsabb adatáthelyezéshez
Ha az áthelyezett adatmennyiség nagy, akkor felgyorsíthatja a dolgokat, ha párhuzamosan hajt végre több BCP-parancsot egy PowerShell-parancsfájlban.

> [!NOTE]
> **Big adatfeldolgozás** A nagyméretű és nagy méretű adatkészletek betöltésének optimalizálása érdekében particionálja a logikai és fizikai adatbázis-táblákat több fájlcsoportok és partíciós tábla használatával. További információ a partíciós táblázatok létrehozásáról és az adatok betöltéséről: [párhuzamos terhelésű SQL-partíciós táblák](parallel-load-sql-partitioned-tables.md).
>
>

A következő PowerShell-parancsfájl bemutatja a párhuzamos lapkákat a BCP használatával:

```powershell
$NO_OF_PARALLEL_JOBS=2

Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
# Define what each job does
$ScriptBlock = {
    param($partitionnumber)

    #Explicitly using SQL username password
    bcp database..tablename in datafile_path.csv -F 2 -f format_file_path.xml -U username@servername -S tcp:servername -P password -b block_size_to_move_in_single_attempt -t "," -r \n -o path_to_outputfile.$partitionnumber.txt

    #Trusted connection w.o username password (if you are using windows auth and are signed in with that credentials)
    #bcp database..tablename in datafile_path.csv -o path_to_outputfile.$partitionnumber.txt -h "TABLOCK" -F 2 -f format_file_path.xml  -T -b block_size_to_move_in_single_attempt -t "," -r \n
}


# Background processing of all partitions
for ($i=1; $i -le $NO_OF_PARALLEL_JOBS; $i++)
{
    Write-Debug "Submit loading partition # $i"
    Start-Job $ScriptBlock -Arg $i      
}


# Wait for it all to complete
While (Get-Job -State "Running")
{
    Start-Sleep 10
    Get-Job
}

# Getting the information back from the jobs
Get-Job | Receive-Job
Set-ExecutionPolicy Restricted #reset the execution policy
```

### <a name="insert-tables-bulkquery"></a>SQL-lekérdezés tömeges beszúrása
A [tömeges beszúrási SQL-lekérdezéssel](https://msdn.microsoft.com/library/ms188365) adatok importálhatók az adatbázisba a sor/oszlop alapú fájlok alapján (a támogatott típusok az[adatok előkészítése tömeges exportálásra vagy importálásra (SQL Server)](https://msdn.microsoft.com/library/ms188609)) című témakörben találhatók.

Az alábbiakban néhány Példaparancsok a tömeges beszúrás vannak, az alábbi:  

1. Az adatok elemzéséhez, és állítsa be az egyéni beállításokat, győződjön meg arról, hogy az SQL Server-adatbázis feltételezi, hogy ugyanazt a formátumot semmilyen különleges mezők, például a dátumok az importálás előtt. A következő példa bemutatja, hogyan állítsa be a dátumformátum, év, hónap-nap (ha az adatok év, hónap-nap formátumban):

    ```sql
    SET DATEFORMAT ymd;
    ```
2. Adatok importálása használatával tömeges importálási utasításokat:

    ```sql
    BULK INSERT <tablename>
    FROM
    '<datafilename>'
    WITH
    (
        FirstRow = 2,
        FIELDTERMINATOR = ',', --this should be column separator in your data
        ROWTERMINATOR = '\n'   --this should be the row separator in your data
    )
    ```

### <a name="sql-builtin-utilities"></a>Beépített segédprogramok a SQL Server
A SQL Server Integration Services (SSIS) használatával adatok importálhatók az Azure-beli SQL Server VMba egy sima fájlból.
SSIS két studio környezetekben érhető el. Részletekért lásd: [Integration Services (SSIS) és Studio-környezetek](https://technet.microsoft.com/library/ms140028.aspx):

* A SQL Server Data Tools részleteiért lásd: [Microsoft SQL Server Adateszközök](https://msdn.microsoft.com/data/tools.aspx)  
* Az importálási/exportálási varázsló részleteiért lásd: [SQL Server Importálás és exportálás varázsló](https://msdn.microsoft.com/library/ms141209.aspx) .

## <a name="sqlonprem_to_sqlonazurevm"></a>Adatok áthelyezése helyszíni SQL Serverból egy Azure-beli virtuális gépen SQL Server
Az alábbi migrálási stratégiák is használhatja:

1. [SQL Server-adatbázis üzembe helyezése Microsoft Azure virtuális gép varázslóban](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Exportálás az egyszerű fájlba](#export-flat-file)
3. [SQL Database áttelepítési varázsló](#sql-migration)
4. [Adatbázis biztonsági mentése és visszaállítása](#sql-backup)

Az alábbi lehetőségeket ismertetjük:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>SQL Server-adatbázis üzembe helyezése a Microsoft Azure virtuális gép varázsló
Az **SQL Server-adatbázis központi telepítése Microsoft Azure virtuális gépre varázsló** egyszerű és ajánlott módszer az adatok áthelyezésére egy helyszíni SQL Server-példányról egy Azure-beli virtuális gépen SQL Server. A részletes lépések, valamint a további alternatívák megvitatására lásd: [adatbázisok Áttelepítésének SQL Server Azure-beli virtuális gépen](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md).

### <a name="export-flat-file"></a>Exportálás az egyszerű fájlba
Számos módszer használható a helyszíni SQL Server adatainak tömeges exportálására az [adatok tömeges importálása és exportálása (SQL Server)](https://msdn.microsoft.com/library/ms175937.aspx) című témakörben leírtak szerint. Ez a dokumentum a tömeges másolási Program (BCP) azzal foglalkozik, példaként. Adatok exportálása egy egybesimított fájlt, miután importálható egy másik SQL server tömeges importálás használatával.

1. Az adatok helyszíni SQL Serverről exportálja egy fájlba, a bcp segédprogram használatával az alábbiak szerint

    `bcp dbname..tablename out datafile.tsv -S    servername\sqlinstancename -T -t \t -t \n -c`
2. Hozza létre az adatbázist és a táblázatot SQL Server VM az Azure-ban az 1. lépésben exportált Table séma `create database` és `create table` használatával.
3. Hozzon létre egy formátumfájlt, az adatok exportálása/importálása folyamatban a következő tábla sémáját leírásához. A formátumú fájl részletes ismertetését lásd: [Format fájl létrehozása (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx).

    Formázza a fájl létrehozása az SQL Server-gépen futtatásakor a BCP használatával

        bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n

    Fájl létrehozása formázása BCP Amikor távolról futtatott SQL-kiszolgáló

        bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
4. Az [adatok beolvasása a fájl forrása](#filesource_to_sqlonazurevm) című részben leírt módszerek bármelyikével áthelyezheti az adatok egy SQL Serverba.

### <a name="sql-migration"></a>SQL Database áttelepítési varázsló
[SQL Server adatbázis-áttelepítési varázsló](https://sqlazuremw.codeplex.com/) felhasználóbarát módon helyezi át az adatátvitelt két SQL Server-példány között. Lehetővé teszi a felhasználó leképezése az adatséma források és a cél táblák között, válassza a oszloptípusainak és számos egyéb funkciókat. A tömeges másolási (BCP) valójában használ. Képernyőkép: az SQL Database áttelepítése varázsló az üdvözlőképernyőn alább látható.  

![Az SQL Server Migrálási varázsló][2]

### <a name="sql-backup"></a>Adatbázis biztonsági mentése és visszaállítása
Az SQL Server támogatja:

1. Az [adatbázis biztonsági mentése és visszaállítása funkció](https://msdn.microsoft.com/library/ms187048.aspx) (mindkettő egy helyi fájlra, vagy a blobba történő exportálás bacpac) és [az adatcsomag-alkalmazásokra](https://msdn.microsoft.com/library/ee210546.aspx) (a bacpac használatával).
2. Képes közvetlenül létrehozni az SQL Server virtuális gépek az Azure-ban másolt adatbázishoz vagy egy meglévő SQL Azure adatbázis másolás. További információ: [az adatbázis másolása varázsló használata](https://msdn.microsoft.com/library/ms188664.aspx).

Képernyőkép: az adatbázis biztonsági mentése/visszaállítás az SQL Server Management Studio lehetőségeit, az alábbiakban látható.

![Az SQL Server Importálás eszköz][1]

## <a name="resources"></a>Erőforrások
[Adatbázis migrálása SQL Server Azure-beli virtuális gépen](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md)

[Az SQL Server használata Azure virtuális gépeken – áttekintés](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)

[1]: ./media/move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/move-sql-server-virtual-machine/database_migration_wizard.png
