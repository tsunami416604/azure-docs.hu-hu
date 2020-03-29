---
title: Adatok áthelyezése SQL Server virtuális gépre – Csapatadatelemzési folyamat
description: Adatok áthelyezése egysíkú fájlokból vagy egy helyszíni SQL Serverről az SQL Server kiszolgálóra az Azure VM-en.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721370"
---
# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Adatok áthelyezés SQL Server-kiszolgálóra Azure-beli virtuális gépeken

Ez a cikk ismerteti az adatok áthelyezése egy síkfájlokból (CSV vagy TSV-formátumok) vagy egy helyszíni SQL Server egy Azure virtuális gépen az SQL Server között. Ezek a feladatok az adatok áthelyezése a felhőbe részét képezik a csapat adatelemzési folyamat.

Az Azure SQL Database for Machine Learning szolgáltatásba való átköltözésének lehetőségeit ismertető témakör az [Adatok áthelyezése Azure-beli Azure Machine Learninghez](move-sql-azure.md)című témakörben olvashat.

Az alábbi táblázat összefoglalja az adatok áthelyezése az SQL Server egy Azure virtuális gépen.

| <b>Forrás</b> | <b>DESTINATION: SQL Server az Azure VM-en</b> |
| --- | --- |
| <b>Egybesimított fájl</b> |1. <a href="#insert-tables-bcp">Parancssori tömeges másolási segédprogram (BCP)</a><br> 2. <a href="#insert-tables-bulkquery">SQL-lekérdezés tömeges beszúrása</a><br> 3. <a href="#sql-builtin-utilities">Grafikus beépített segédprogramok az SQL Server ben</a> |
| <b>Helyszíni SQL Server</b> |1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">SQL Server-adatbázis telepítése Microsoft Azure virtuális gép varázslóra</a><br> 2. <a href="#export-flat-file">Exportálás egy sima fájlba</a><br> 3. <a href="#sql-migration">SQL-adatbázis áttelepítése varázsló</a> <br> 4. <a href="#sql-backup">Adatbázis biztonsági mentése és visszaállítása</a><br> |

Ez a dokumentum feltételezi, hogy az SQL-parancsok végrehajtása az SQL Server Management Studio vagy a Visual Studio Adatbázis-kezelő szolgáltatásból történik.

> [!TIP]
> Alternatív megoldásként az [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) segítségével létrehozhat és ütemezhet egy folyamatot, amely áthelyezi az adatokat egy SQL Server virtuális gépre az Azure-ban. További információ: [Adatok másolása az Azure Data Factory (Copy Activity)](../../data-factory/copy-activity-overview.md)használatával című témakörben.
>
>

## <a name="prerequisites"></a><a name="prereqs"></a>Előfeltételek
Ez az oktatóanyag feltételezi, hogy:

* **Egy Azure-előfizetés**. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/).
* Egy **Azure-tárfiók.** Ebben az oktatóanyagban az adatok tárolásához egy Azure storage-fiókot fog használni. Ha nem rendelkezik Azure-tárfiókkal, tekintse meg a [tárfiók létrehozása](../../storage/common/storage-account-create.md) cikket. Miután létrehozta a tárfiókot, be kell szereznie a tároló eléréséhez használt fiókkulcsot. Lásd: [Tárfiók hozzáférési kulcsainak kezelése.](../../storage/common/storage-account-keys-manage.md)
* Kiépített **SQL Server egy Azure virtuális gép.** További információt az [Azure SQL Server virtuális gép beállítása IPython notebook-kiszolgálóként című](../data-science-virtual-machine/setup-sql-server-virtual-machine.md)témakörben talál a speciális elemzésekhez.
* Helyileg telepített és konfigurált **Azure PowerShell.** További információt az [Azure PowerShell telepítése és konfigurálása című témakörben talál.](/powershell/azure/overview)

## <a name="moving-data-from-a-flat-file-source-to-sql-server-on-an-azure-vm"></a><a name="filesource_to_sqlonazurevm"></a>Adatok áthelyezése egy sima fájlforrásból az SQL Serverbe egy Azure virtuális gépen
Ha az adatok egy sima fájlban vannak (sor-/oszlop formátumban rendezve), az alábbi módszerekkel áthelyezhetők az SQL Server Virtuális gépre az Azure-ban:

1. [Parancssori tömeges másolási segédprogram (BCP)](#insert-tables-bcp)
2. [SQL-lekérdezés tömeges beszúrása](#insert-tables-bulkquery)
3. [Grafikus beépített segédprogramok az SQL Server ben (importálás/exportálás, SSIS)](#sql-builtin-utilities)

### <a name="command-line-bulk-copy-utility-bcp"></a><a name="insert-tables-bcp"></a>Parancssori tömeges másolási segédprogram (BCP)
A BCP az SQL Server kiszolgálóval telepített parancssori segédprogram, amely az adatok áthelyezésének egyik leggyorsabb módja. Mindhárom SQL Server-változatban működik (a helyszíni SQL Server, az SQL Azure és az SQL Server virtuális gép az Azure-ban).

> [!NOTE]
> **Hol legyenek az adataim a BCP-hez?**  
> Bár ez nem szükséges, miután a fájlokat tartalmazó forrásadatok at ugyanazon a gépen, mint a cél SQL Server lehetővé teszi a gyorsabb adatátvitelt (hálózati sebesség vs helyi lemez IO sebesség). Az adatokat tartalmazó fájlokat áthelyezheti arra a számítógépre, ahol az SQL Server telepítve van különböző fájlmásoló eszközökkel, például az [AZCopy,](../../storage/common/storage-use-azcopy.md) [az Azure Storage Explorer](https://storageexplorer.com/) vagy a Windows copy/paste segítségével a Remote Desktop Protocol (RDP) protokollon keresztül.
>
>

1. Győződjön meg arról, hogy az adatbázis és a táblák a cél SQL Server adatbázisban vannak létrehozva. Íme egy példa arra, hogyan `Create Database` kell `Create Table` ezt megtenni a és a parancsokkal:

    ```sql
    CREATE DATABASE <database_name>
    
    CREATE TABLE <tablename>
    (
        <columnname1> <datatype> <constraint>,
        <columnname2> <datatype> <constraint>,
        <columnname3> <datatype> <constraint>
    )
    ```

1. Hozza létre azt a formátumfájlt, amely leírja a tábla sémáját, és adja ki a következő parancsot annak a gépnek a parancssorából, amelyen a bcp telepítve van.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`
1. Szúrja be az adatokat az adatbázisba az bcp paranccsal, amelynek a parancssorból kell működnie, ha az SQL Server ugyanazon a számítógépen van telepítve:

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attempt -t \t -r \n`

> **BCP-beszúrások optimalizálása** Kérjük, olvassa el a ["Tömeges importálás optimalizálásának irányelvei"](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx) című cikket az ilyen lapkák optimalizálásához.
>
>

### <a name="parallelizing-inserts-for-faster-data-movement"></a><a name="insert-tables-bulkquery-parallel"></a>A beszúrások párhuzamosítása a gyorsabb adatmozgás érdekében
Ha az áthelyezett adatok nagyok, felgyorsíthatja a dolgokat, ha egyidejűleg több BCP-parancsot hajt végre párhuzamosan egy PowerShell-parancsfájlban.

> [!NOTE]
> **Big data betöltése** Az adatok nagy és nagyon nagy adatkészletek betöltésének optimalizálásához particionálja a logikai és fizikai adatbázistáblákat több fájlcsoport és partíciós tábla használatával. Az adatok partíciós táblákba való létrehozásáról és betöltéséről további információt az [SQL partíciós táblák párhuzamos betöltése című témakörben talál.](parallel-load-sql-partitioned-tables.md)
>
>

A következő PowerShell-parancsfájl-minta a bcp használatával párhuzamos beszúrásokat mutatja be:

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

### <a name="bulk-insert-sql-query"></a><a name="insert-tables-bulkquery"></a>SQL-lekérdezés tömeges beszúrása
[A Tömeges beszúrássql query](https://msdn.microsoft.com/library/ms188365) segítségével adatokat importálhat az adatbázisba sor- és oszlopalapú fájlokból (a támogatott típusokat az[Adatok előkészítése tömeges exportálásra vagy importálásra (SQL Server) című](https://msdn.microsoft.com/library/ms188609)témakör ismerteti).

Íme néhány mintaparancs a Tömeges beszúráshoz az alábbi módon:  

1. Az importálás előtt elemezze az adatokat, és adja meg az egyéni beállításokat, és győződjön meg arról, hogy az SQL Server-adatbázis ugyanazt a formátumot veszi fel minden speciális mezőben, például a dátumokban. Íme egy példa arra, hogyan állíthatja be a dátumformátumot év-hónap-nap (ha az adatok tartalmazzák a dátumot év-hónap-nap formátumban):

    ```sql
    SET DATEFORMAT ymd;
    ```
2. Adatok importálása tömeges importálási utasításokkal:

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

### <a name="built-in-utilities-in-sql-server"></a><a name="sql-builtin-utilities"></a>Beépített segédprogramok az SQL Server ben
Az SQL Server Integration Services (SSIS) segítségével adatokat importálhat az Azure-beli SQL Server virtuális gépbe egy sima fájlból.
Az SSIS két stúdiókörnyezetben érhető el. További részletek: [Integration Services (SSIS) és Studio Environments](https://technet.microsoft.com/library/ms140028.aspx):

* Az SQL Server Adateszközökről a [Microsoft SQL Server Data Tools című](https://msdn.microsoft.com/data/tools.aspx) témakörben talál részleteket.  
* Az Importálás/exportálás varázslóval kapcsolatos részletek az [SQL Server importálása és exportálása varázslóban](https://msdn.microsoft.com/library/ms141209.aspx)

## <a name="moving-data-from-on-premises-sql-server-to-sql-server-on-an-azure-vm"></a><a name="sqlonprem_to_sqlonazurevm"></a>Adatok áthelyezése a helyszíni SQL Server kiszolgálóról az SQL Server kiszolgálóra egy Azure virtuális gépen
A következő áttelepítési stratégiákat is használhatja:

1. [SQL Server-adatbázis telepítése Microsoft Azure virtuális gép varázslóra](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Exportálás egyfájlba](#export-flat-file)
3. [SQL-adatbázis áttelepítése varázsló](#sql-migration)
4. [Adatbázis biztonsági mentése és visszaállítása](#sql-backup)

Az alábbi lehetőségek mindegyikét leírjuk:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>SQL Server-adatbázis telepítése Microsoft Azure virtuális gép varázslóra
Az **SQL Server-adatbázis központi telepítése egy Microsoft Azure virtuális gép varázsló** egy egyszerű és ajánlott módja az adatok áthelyezésének egy helyszíni SQL Server-példányból az SQL Server egy Azure virtuális gép. A részletes lépésekről, valamint az egyéb alternatívákról szóló témaköraz [Adatbázis áttelepítése az SQL Serverkiszolgálóra Azure-beli virtuális gépről](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md)című témakörben található.

### <a name="export-to-flat-file"></a><a name="export-flat-file"></a>Exportálás egyfájlba
Különböző módszerek kelhetők fel az adatok tömeges exportálására egy helyszíni SQL Server kiszolgálóról az [SQL Server tömeges importálása és exportálása (SQL Server)](https://msdn.microsoft.com/library/ms175937.aspx) témakörben ismertetett módon. Ez a dokumentum példaként a Tömeges másolási programra (BCP) vonatkozik. Miután az adatokat egy sima fájlba exportálta, tömeges importálással importálható egy másik SQL-kiszolgálóra.

1. Adatok exportálása a helyszíni SQL Server kiszolgálóról fájlba a bcp segédprogram mal az alábbiak szerint

    `bcp dbname..tablename out datafile.tsv -S    servername\sqlinstancename -T -t \t -t \n -c`
2. Hozza létre az adatbázist és a táblát `create database` az `create table` SQL Server virtuális gép en az Azure-ban az 1.
3. Hozzon létre egy formátumfájlt az exportált/importált adatok táblasémájának leírására. A formátumfájl részleteit a [Formátumfájl létrehozása (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx)című részben ismerteti.

    Fájlgenerálás formázása BCP futtatásakor az SQL Server-gépről

        bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n

    Fájlgenerálás formázása a BCP távoli futtatásakor SQL Server kiszolgálón

        bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
4. Az [Adatok áthelyezése a fájlforrásból](#filesource_to_sqlonazurevm) című szakaszban ismertetett módszerek bármelyikével áthelyezheti az adatokat egy SQL Server kiszolgálóra.

### <a name="sql-database-migration-wizard"></a><a name="sql-migration"></a>SQL-adatbázis áttelepítése varázsló
[Az SQL Server Adatbázis-áttelepítés varázsló](https://sqlazuremw.codeplex.com/) felhasználóbarát módon mozgatja az adatokat két SQL-kiszolgálópéldány között. Lehetővé teszi a felhasználó számára az adatséma leképezése a források és a céltáblák között, oszloptípusok és egyéb funkciók kiválasztásához. Ez használ tömeges másolat (BCP) a borítók alatt. Az SQL Database Migration varázsló üdvözlőképernyőjén látható képernyő az alábbiakban látható.  

![SQL Server Áttelepítés varázsló][2]

### <a name="database-back-up-and-restore"></a><a name="sql-backup"></a>Adatbázis biztonsági mentése és visszaállítása
Az SQL Server a következőket támogatja:

1. [Adatbázis biztonsági másolatot, és visszaállítása funkciók](https://msdn.microsoft.com/library/ms187048.aspx) (mind a helyi fájl vagy bacpac export blob) és [data tier alkalmazások](https://msdn.microsoft.com/library/ee210546.aspx) (bacpac használatával).
2. Képes közvetlenül létrehozni az SQL Server virtuális gépeket az Azure-ban egy másolt adatbázissal, vagy egy meglévő SQL Azure-adatbázisba másolni. További információt [az Adatbázis másolása varázsló használata című témakörben talál.](https://msdn.microsoft.com/library/ms188664.aspx)

Az sql Server Management Studio adatbázis-biztonsági mentése/visszaállítási beállításainak képernyőképe alább látható.

![SQL Server importálási eszköz][1]

## <a name="resources"></a>Források
[Adatbázis áttelepítése az SQL Server kiszolgálóra egy Azure virtuális gépen](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md)

[Az SQL Server használata az Azure Virtual Machinesben – áttekintés](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)

[1]: ./media/move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/move-sql-server-virtual-machine/database_migration_wizard.png
