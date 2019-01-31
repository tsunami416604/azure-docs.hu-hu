---
title: Adatok áthelyezése az SQL Server virtuális gép – csoportos adatelemzési folyamat
description: Adatok áthelyezése vagy egy helyszíni SQL Serverről egybesimított fájlokba, az SQL Server Azure virtuális gépen.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 7c87a0f478b6efbe7ae9ff07def8b4d0d730b111
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55478491"
---
# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Adatok áthelyezés SQL Server-kiszolgálóra Azure-beli virtuális gépeken

Ez a cikk ismerteti a lehetőség egyszerű fájlok (CSV- vagy TSV formátumok) vagy egy helyszíni SQL Serverről az SQL Server-beli virtuális gépen. Adatok áthelyezése a felhőbe ezeket a feladatokat a csoportos adatelemzési folyamat részét képezik.

Ez a témakör ismerteti az adatok áthelyezése az Azure SQL Database, Machine Learning a beállításokat, lásd: [adatok áthelyezése az Azure SQL Database az Azure Machine Learning](move-sql-azure.md).

A következő táblázat összefoglalja a lehetőség az SQL Server-beli virtuális gépen.

| <b>FORRÁS</b> | <b>CÉL: Az SQL Server Azure virtuális gépen</b> |
| --- | --- |
| <b>Egybesimított fájl</b> |1. <a href="#insert-tables-bcp">Parancssori tömeges másolás eszköz (BCP) </a><br> 2. <a href="#insert-tables-bulkquery">Tömeges beszúrás SQL-lekérdezés </a><br> 3. <a href="#sql-builtin-utilities">Az SQL Server grafikus beépített segédprogramok</a> |
| <b>A helyszíni SQL Server</b> |1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">SQL Server-adatbázis üzembe helyezése a Microsoft Azure virtuális gép varázsló</a><br> 2. <a href="#export-flat-file">Egybesimított fájl exportálása </a><br> 3. <a href="#sql-migration">Az SQL Database áttelepítése varázsló </a> <br> 4. <a href="#sql-backup">Adatbázis biztonsági mentése és visszaállítása </a><br> |

Vegye figyelembe, hogy jelen dokumentum céljából feltételezzük, hogy az SQL Server Management Studio vagy Visual Studio-adatbázis Explorer tevékenységében SQL parancsok.

> [!TIP]
> Alternatív megoldásként használható [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) hozhat létre és ütemezhet egy folyamatot, amely áthelyezi adatait egy SQL Server virtuális Gépet az Azure-ban. További információkért lásd: [adatmásolás az Azure Data Factory (másolási tevékenységgel) rendelkező](../../data-factory/copy-activity-overview.md).
>
>

## <a name="prereqs"></a>Előfeltételek
Ez az oktatóanyag feltételezi, hogy:

* Egy **Azure-előfizetés**. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/).
* Egy **Azure storage-fiók**. Ez az oktatóanyag az adatok tárolása Azure storage-fiók használandó. Ha nem rendelkezik Azure storage-fiókkal, tekintse meg a [hozzon létre egy tárfiókot](../../storage/common/storage-quickstart-create-account.md) cikk. Miután létrehozta a tárfiókot, szüksége lesz a a tárterület elérésére használt fiók kulcs beszerzése. Lásd: [a tárelérési kulcsok kezelése](../../storage/common/storage-account-manage.md#access-keys).
* Üzembe helyezett **egy Azure virtuális Gépen futó SQL Server**. Útmutatásért lásd: [egy Azure SQL Server virtuális gép beállításához-IPython Notebook-kiszolgálóként a speciális analitikai](../data-science-virtual-machine/setup-sql-server-virtual-machine.md).
* Telepített és konfigurált **Azure PowerShell-lel** helyileg. Útmutatásért lásd: [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/overview).

## <a name="filesource_to_sqlonazurevm"></a> Egybesimított fájl forrás helyez át adatokat egy Azure virtuális Gépen futó SQL Serverre
Ha az adatok egy egybesimított fájlt, (rendezett sor/oszlop formátumban), azt helyezheti át SQL Server rendszerű virtuális gép az Azure-on keresztül az alábbi módszerek:

1. [Parancssori tömeges másolás eszköz (BCP)](#insert-tables-bcp)
2. [Tömeges beszúrás SQL-lekérdezés ](#insert-tables-bulkquery)
3. [Az SQL Server (Import/Export, SSIS) grafikus beépített segédprogramok](#sql-builtin-utilities)

### <a name="insert-tables-bcp"></a>Parancssori tömeges másolás eszköz (BCP)
BCP parancssori segédprogram az SQL Server telepítve, és egyik adatok áthelyezése a leggyorsabb módja. Az összes három SQL Server változat (a helyszíni SQL Server, SQL Azure és az SQL Server rendszerű virtuális gép az Azure-ban) működik.

> [!NOTE]
> **Az adatok hol kell lennie a BCP használatával?**  
> Bár nem kötelező, és a cél SQL Server ugyanazon a számítógépen található adatforrás-adatokat tartalmazó fájlok lehetővé, hogy a gyorsabb adatátvitel (hálózati sebesség és helyi lemez i/o-sebesség). Áthelyezheti az adatokat a gép tartalmazó egybesimított fájlok, SQL Server telepítve van a használatával különféle fájlok másolása eszközök, mint például [AZCopy](../../storage/common/storage-use-azcopy.md), [Azure Storage Explorer](http://storageexplorer.com/) vagy a windows másolási és beillesztési távoli asztalon keresztül Protokoll (RDP).
>
>

1. Győződjön meg arról, hogy az adatbázis és a táblázatok jönnek létre a cél SQL Server-adatbázist. Íme egy példa, hogy az módjáról a `Create Database` és `Create Table` parancsokat:

```sql
CREATE DATABASE <database_name>

CREATE TABLE <tablename>
(
    <columnname1> <datatype> <constraint>,
    <columnname2> <datatype> <constraint>,
    <columnname3> <datatype> <constraint>
)
```

2. Hozza létre a formátumú fájlt, amely ismerteti a tábla sémája a következő parancsot a parancssorból a gép amelyen telepítve van a BCP használatával.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`
3. Az adatok beszúrása az adatbázisba, ha a bcp-parancs használatával az alábbiak szerint. Ez használható a parancssorból feltételezve, hogy az SQL Server ugyanezen a gépen telepítve van:

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attempt -t \t -r \n`

> **Szúrja be a BCP optimalizálása** tekintse meg a következő cikkben ["Tömeges importálás optimalizálása irányelvek"](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx) ilyen Beszúrások optimalizálása érdekében.
>
>

### <a name="insert-tables-bulkquery-parallel"></a>A gyorsabb adatátvitel párhuzamosan futtatni a beszúrások
Ha az adatokat helyez át nagy, felgyorsíthatja dolgot egyszerre több BCP bevezetett parancsok végrehajtásával egy PowerShell-parancsfájl párhuzamosan.

> [!NOTE]
> **A big data-feldolgozó** nagy és nagyon nagy adatkészletekhez Adatbetöltési optimalizálása érdekében particionálja a logikai és fizikai adatbázistáblák több fájl csoportok használatával, és a Táblák particionálása. Létrehozásával és az adatok betöltése a táblák partíció kapcsolatos további információkért lásd: [párhuzamos betöltés SQL partíciós táblák](parallel-load-sql-partitioned-tables.md).
>
>

A következő PowerShell-parancsfájlt mutat be a párhuzamos Beszúrás a bcp használatával:

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

### <a name="insert-tables-bulkquery"></a>Tömeges beszúrás SQL-lekérdezés
[Tömeges beszúrás SQL-lekérdezés](https://msdn.microsoft.com/library/ms188365) adatok importálása az adatbázisban a sorhoz/oszlophoz alapú fájlok segítségével (ismerkedhet meg a támogatott típusok a[előkészítése adatok tömeges exportálása és importálása (SQL Server)](https://msdn.microsoft.com/library/ms188609)) témakör.

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

### <a name="sql-builtin-utilities"></a>Az SQL Server beépített segédprogramok
Az SQL Server Integrációk Services (SSIS) segítségével az adatok importálása az Azure-ban egy egybesimított fájlból az SQL Server rendszerű virtuális gép.
SSIS két studio környezetekben érhető el. További információkért lásd: [Integration Services (SSIS) és a Studio környezetek](https://technet.microsoft.com/library/ms140028.aspx):

* További információ az SQL Server Data Tools: [Microsoft SQL Server Data Tools](https://msdn.microsoft.com/data/tools.aspx)  
* További információ az importálási/exportálási varázslóban: [SQL Server importálása és exportálása varázsló](https://msdn.microsoft.com/library/ms141209.aspx)

## <a name="sqlonprem_to_sqlonazurevm"></a>Adatok áthelyezése a helyszíni SQL Serverről az SQL Server egy Azure virtuális gépen
Az alábbi migrálási stratégiák is használhatja:

1. [SQL Server-adatbázis üzembe helyezése a Microsoft Azure virtuális gép varázsló](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Egybesimított fájl exportálása](#export-flat-file)
3. [Az SQL Database áttelepítése varázsló](#sql-migration)
4. [Adatbázis biztonsági mentése és visszaállítása](#sql-backup)

Azt ismertetik ezeket az alábbi:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>SQL Server-adatbázis üzembe helyezése a Microsoft Azure virtuális gép varázsló
A **üzembe helyezése az SQL Server-adatbázis a Microsoft Azure virtuális gép varázslóban** egy egyszerű és ajánlott módja az adatok áthelyezése a helyszíni SQL Server-példány az SQL Server-beli virtuális gépen. Részletes lépéseit, valamint egyéb alternatívák hatásának a megbeszélését lásd [adatbázis áttelepítése az SQL Server-beli virtuális gépen](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md).

### <a name="export-flat-file"></a>Egybesimított fájl exportálása
Különböző módszereket is használható az adatok exportálása egy helyszíni SQL Serverről tömeges leírtak szerint a [tömeges adatok importálása és exportálása az (SQL Server)](https://msdn.microsoft.com/library/ms175937.aspx) témakör. Ez a dokumentum a tömeges másolási Program (BCP) azzal foglalkozik, példaként. Adatok exportálása egy egybesimított fájlt, miután importálható egy másik SQL server tömeges importálás használatával.

1. Az adatok helyszíni SQL Serverről exportálja egy fájlba, a bcp segédprogram használatával az alábbiak szerint

    `bcp dbname..tablename out datafile.tsv -S    servername\sqlinstancename -T -t \t -t \n -c`
2. Az adatbázis és tábla az SQL Server rendszerű virtuális gép létrehozása az Azure-ban a `create database` és `create table` az 1. lépésben exportált a következő tábla sémáját.
3. Hozzon létre egy formátumfájlt, az adatok exportálása/importálása folyamatban a következő tábla sémáját leírásához. A formátumfájl részleteit ismerteti [hozzon létre egy Formátumfájlt (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx).

    Formázza a fájl létrehozása az SQL Server-gépen futtatásakor a BCP használatával

        bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n

    Fájl létrehozása formázása BCP Amikor távolról futtatott SQL-kiszolgáló

        bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
4. A szakaszban ismertetett módszerek bármelyikét használhatja [fájl forrásból származó adatokat](#filesource_to_sqlonazurevm) az adatok áthelyezéséhez az egybesimított fájlok SQL Serverre.

### <a name="sql-migration"></a>Az SQL Database áttelepítése varázsló
[SQL Server adatbázis-Migrálási varázsló](http://sqlazuremw.codeplex.com/) felhasználóbarát biztosítja az adatok áthelyezése két SQL server-példányok között. Lehetővé teszi a felhasználó leképezése az adatséma források és a cél táblák között, válassza a oszloptípusainak és számos egyéb funkciókat. A tömeges másolási (BCP) valójában használ. Képernyőkép: az SQL Database áttelepítése varázsló az üdvözlőképernyőn alább látható.  

![Az SQL Server Migrálási varázsló][2]

### <a name="sql-backup"></a>Adatbázis biztonsági mentése és visszaállítása
Az SQL Server támogatja:

1. [Adatbázis biztonsági mentése és visszaállítása funkciók](https://msdn.microsoft.com/library/ms187048.aspx) (mindkettő egy helyi fájl vagy a bacpac exportálása blob) és [szintű alkalmazások](https://msdn.microsoft.com/library/ee210546.aspx) (bacpac használatával).
2. Képes közvetlenül létrehozni az SQL Server virtuális gépek az Azure-ban másolt adatbázishoz vagy egy meglévő SQL Azure adatbázis másolás. További részletekért lásd: [adatbázis másolása varázslóval](https://msdn.microsoft.com/library/ms188664.aspx).

Képernyőkép: az adatbázis biztonsági mentése/visszaállítás az SQL Server Management Studio lehetőségeit, az alábbiakban látható.

![Az SQL Server Importálás eszköz][1]

## <a name="resources"></a>További források
[Egy Azure virtuális Gépen futó SQL Server-adatbázis áttelepítése](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md)

[Az SQL Server használata Azure virtuális gépeken – áttekintés](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)

[1]: ./media/move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/move-sql-server-virtual-machine/database_migration_wizard.png
