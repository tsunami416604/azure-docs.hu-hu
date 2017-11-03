---
title: "Adatok áthelyezése az SQL Server Azure virtuális géphez |} Microsoft Docs"
description: "Helyezze át adatok strukturálatlan fájlból, vagy egy helyi SQL Server-kiszolgáló SQL Server Azure virtuális gépen."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 2c9ef1d3-4f5c-4b1f-bf06-223646c8af06
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
ms.openlocfilehash: bd9289ea528c3a80f63380daf52161d2477a6771
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="move-data-to-sql-server-on-an-azure-virtual-machine"></a>Adatok áthelyezés SQL Server-kiszolgálóra Azure-beli virtuális gépeken
Ez a témakör bemutatja a beállítások áthelyezése adatok strukturálatlan fájlból (CSV vagy TSV formátumban), vagy egy helyi SQL Server-kiszolgáló SQL Server Azure virtuális géphez. Ezeket a feladatokat az adatok áthelyezését a felhőbe az Team tudományos folyamat részét képezik.

Ez a témakör ismerteti az adatok áthelyezése a Machine Learning Azure SQL adatbázis beállításait, lásd: [adatok áthelyezése az Azure SQL Database az Azure Machine Learning](move-sql-azure.md).

A **menü** alatti leíró betöltik az adatokat más cél környezetekben, ahol az adatok is tárolhatók és feldolgozhatók, a csapat adatok tudományos folyamat (TDSP) során témakörökre mutató hivatkozásokat tartalmaz.

[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

A következő táblázat összefoglalja a beállítások érhetők el adatok áthelyezése az SQL Server Azure virtuális géphez.

| <b>FORRÁS</b> | <b>CÉL: SQL Server Azure virtuális gépen</b> |
| --- | --- |
| <b>Egybesimított fájl</b> |1. <a href="#insert-tables-bcp">Parancssori tömeges másolási segédprogram (BCP)</a><br> 2. <a href="#insert-tables-bulkquery">A tömeges beszúrás SQL-lekérdezés</a><br> 3. <a href="#sql-builtin-utilities">Az SQL Server grafikus beépített segédprogramok</a> |
| <b>A helyszíni SQL Server</b> |1. <a href="#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard">SQL Server-adatbázis telepítése a Microsoft Azure virtuális gép varázsló</a><br> 2. <a href="#export-flat-file">Egybesimított fájl exportálása</a><br> 3. <a href="#sql-migration">SQL-adatbázis áttelepítése varázsló</a> <br> 4. <a href="#sql-backup">Adatbázis biztonsági mentése és visszaállítása</a><br> |

Figyelje meg, hogy a jelen dokumentum céljából feltételezzük, hogy az SQL-parancsok végrehajtott SQL Server Management Studio vagy Visual Studio adatbázis-kezelő.

> [!TIP]
> Alternatív megoldásként használható [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) lehet létrehozni és ütemezni egy folyamatot, amely az Azure data áthelyezi egy SQL Server virtuális Gépet. További információkért lásd: [Azure Data Factory (másolási tevékenység) adatok másolása](../../data-factory/v1/data-factory-data-movement-activities.md).
>
>

## <a name="prereqs"></a>Előfeltételek
Ez az oktatóanyag feltételezi, hogy rendelkezik:

* Egy **Azure-előfizetés**. Ha nem rendelkezik előfizetéssel, regisztrálhat egy [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial/).
* Egy **Azure storage-fiók**. Szüksége lesz egy Azure storage-fiók ebben az oktatóanyagban az adatok tárolásához. Ha egy Azure storage-fiók nem rendelkezik, tekintse meg a [hozzon létre egy tárfiókot](../../storage/common/storage-create-storage-account.md#create-a-storage-account) cikk. Miután létrehozta a tárfiókot, szüksége lesz a tároló elérésére használt fiókot kulcs beszerzése. Lásd: [a tárelérési kulcsok kezelése](../../storage/common/storage-create-storage-account.md#manage-your-storage-access-keys).
* Kiépített **egy Azure virtuális Gépen található SQL-kiszolgáló**. Útmutatásért lásd: [állítsa be az Azure SQL Server virtuális gép speciális elemzésekre IPython Notebook kiszolgálóként](../data-science-virtual-machine/setup-sql-server-virtual-machine.md).
* Telepített és konfigurált **Azure PowerShell** helyileg. Útmutatásért lásd: [telepítése és konfigurálása az Azure PowerShell](/powershell/azure/overview).

## <a name="filesource_to_sqlonazurevm"></a>Adatok áthelyezése egy egybesimított fájl forrásból SQL Server egy Azure virtuális gépen
Ha az adatok egy egyszerű fájlban (rendezett sorhoz/oszlophoz formátumban), akkor helyezheti át SQL Server rendszerű virtuális az Azure-on keresztül az alábbi módszerek:

1. [Parancssori tömeges másolási segédprogram (BCP)](#insert-tables-bcp)
2. [A tömeges beszúrás SQL-lekérdezés](#insert-tables-bulkquery)
3. [Az SQL Server (importálási/exportálási, SSIS) grafikus beépített segédprogramok](#sql-builtin-utilities)

### <a name="insert-tables-bcp"></a>Parancssori tömeges másolási segédprogram (BCP)
BCP parancssori segédprogram SQL Server telepített, és egyik áthelyezni az adatokat a leggyorsabb módja. Minden három SQL Server változat (a helyszíni SQL Server, SQL Azure és SQL Server Azure virtuális gép) is használható.

> [!NOTE]
> **Az adatok hol kell lennie a BCP?**  
> Bár ez nem kötelező, és a cél az SQL Server ugyanazon a számítógépen található forrás adatokat tartalmazó fájlok segítségével gyorsabb átvitelek (hálózati sebesség és a helyi lemez IO sebessége). Áthelyezheti a egybesimított fájlokba, a gép adatokat tartalmazó adott SQL Server használatával van telepítve, mint eszközök különböző fájlok másolása [AZCopy](../../storage/common/storage-use-azcopy.md), [Azure Tártallózó](http://storageexplorer.com/) vagy a windows távoli asztalon keresztül, másolási és beillesztési Protokoll (RDP).
>
>

1. Győződjön meg arról, hogy a célként megadott SQL Server-adatbázis az adatbázis és a táblázatok jönnek létre. Nem adott használatával például a `Create Database` és `Create Table` parancsokat:

        CREATE DATABASE <database_name>

        CREATE TABLE <tablename>
        (
            <columnname1> <datatype> <constraint>,
            <columnname2> <datatype> <constraint>,
            <columnname3> <datatype> <constraint>
        )
2. Hozza létre a formátumú fájlt, amely leírja a séma a következő táblázatban a következő parancsot a parancssorból a gép bcp futtató.

    `bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n`
3. Helyezze be az adatokat az adatbázisba, az alábbiak szerint a bcp paranccsal. Feltételezve, hogy az SQL Server ugyanezen a gépen telepítve van a kell működnie parancsot a parancssorból:

    `bcp dbname..tablename in datafilename.tsv -f exportformatfilename.xml -S servername\sqlinstancename -U username -P password -b block_size_to_move_in_single_attemp -t \t -r \n`

> **Optimalizálja a BCP beszúrása** tekintse meg a következő cikk ["Tömeges importálással optimalizálása útmutatás"](https://technet.microsoft.com/library/ms177445%28v=sql.105%29.aspx) ilyen Beszúrások optimalizálása érdekében.
>
>

### <a name="insert-tables-bulkquery-parallel"></a>Gyorsabb adatátvitelt jelölik a Beszúrás parallelizing
Ha az adatokat helyez át nagy, felgyorsíthatja dolgot egy PowerShell-parancsfájlt párhuzamosan több BCP parancsok egyidejűleg végrehajtásával.

> [!NOTE]
> **Big Data típusú adatok adatfeldolgozást** nagy és nagyon nagy adatkészletek betöltése adatok optimalizálása érdekében partícióazonosító a logikai és fizikai adatbázis táblák fájlcsoportok és a partíció táblákat. Létrehozásával és a partíciós táblákba adatok betöltése kapcsolatos további információkért lásd: [párhuzamos terhelés SQL partíciós táblákba](parallel-load-sql-partitioned-tables.md).
>
>

A PowerShell-parancsfájlpélda az alábbi párhuzamos Beszúrás a bcp bemutatása:

    $NO_OF_PARALLEL_JOBS=2

     Set-ExecutionPolicy RemoteSigned #set execution policy for the script to execute
     # Define what each job does
       $ScriptBlock = {
           param($partitionnumber)

           #Explictly using SQL username password
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


### <a name="insert-tables-bulkquery"></a>A tömeges beszúrás SQL-lekérdezés
[Tömeges beszúrás SQL-lekérdezés](https://msdn.microsoft.com/library/ms188365) is használható, hogy adatokat importáljon belőlük az adatbázist a sorhoz/oszlophoz alapú fájlok (lásd a támogatott típusok: a[tömeges exportálása és importálása (SQL Server) előkészítése adatait](https://msdn.microsoft.com/library/ms188609)) témakör.

Az alábbiakban néhány Példaparancsok tömeges Beszúrás az alábbiak szerint vannak:  

1. Az adatok elemzéséhez, és győződjön meg arról, hogy az SQL Server-adatbázis azt feltételezi, hogy ugyanazt a formátumot dátumok például különleges mezőket az importálás előtt minden egyéni beállítások megadása. Íme egy példa bemutatja, hogyan beállítani a dátumformátum év, hónap-nap (ha az adatok a dátum az év, hónap-nap formátumban):

        SET DATEFORMAT ymd;    
2. Importálja az adatokat, és tömeges importálási utasítást:

        BULK INSERT <tablename>
        FROM    
        '<datafilename>'
        WITH
        (
        FirstRow=2,
        FIELDTERMINATOR =',', --this should be column separator in your data
        ROWTERMINATOR ='\n'   --this should be the row separator in your data
        )

### <a name="sql-builtin-utilities"></a>Az SQL Server beépített segédprogramok
SQL Server integrációja Services (SSIS) segítségével az adatok importálása egy egybesimított fájlból Azure SQL Server virtuális gép.
SSIS két studio környezetekben érhető el. További információkért lásd: [Integration Services (SSIS) és a Studio környezetek](https://technet.microsoft.com/library/ms140028.aspx):

* További részletek az SQL Server Data Tools: [Microsoft SQL Server Data Tools](https://msdn.microsoft.com/data/tools.aspx)  
* További részletek az Import/Export varázsló: [SQL Server importálása és exportálása varázsló](https://msdn.microsoft.com/library/ms141209.aspx)

## <a name="sqlonprem_to_sqlonazurevm"></a>Adatok áthelyezése a helyszíni SQL Server SQL Server egy Azure virtuális gépen
A következő áttelepítési stratégia is használhatja:

1. [SQL Server-adatbázis telepítése a Microsoft Azure virtuális gép varázsló](#deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard)
2. [Egybesimított fájl exportálása](#export-flat-file)
3. [SQL-adatbázis áttelepítése varázsló](#sql-migration)
4. [Adatbázis biztonsági mentése és visszaállítása](#sql-backup)

Azt mutatják be ezen az alábbi:

### <a name="deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>SQL Server-adatbázis telepítése a Microsoft Azure virtuális gép varázsló
A **központi telepítése az SQL Server-adatbázis a Microsoft Azure virtuális gép varázsló** egy egyszerű és ajánlott módja a tárolt adatok mozgatása a helyszíni SQL Server-példány SQL Server egy Azure virtuális gépen. Részletes lépéseit, valamint az egyéb alternatívák döntéseken, lásd: [adatbázis áttelepítése SQL Server egy Azure virtuális gépen](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md).

### <a name="export-flat-file"></a>Egybesimított fájl exportálása
Különböző módszer használható tömeges adatok exportálása egy helyszíni SQL Server, ahogy a [tömeges adatok importálása és exportálása a (SQL Server)](https://msdn.microsoft.com/library/ms175937.aspx) témakör. Ez a dokumentum a tömeges másolási Program (BCP) szakaszában példaként. Amennyiben az adatok strukturálatlan fájlba exportálása, hogy importálni lehessen egy másik SQL Server tömeges importálással.

1. A helyszíni SQL Server a bcp segédprogram használatával az alábbiak szerint fájlba az adatok exportálása

    `bcp dbname..tablename out datafile.tsv -S    servername\sqlinstancename -T -t \t -t \n -c`
2. Az adatbázis és a tábla az SQL Server virtuális gép létrehozása az Azure használatával a `create database` és `create table` a következő tábla sémáját exportálja a rendszer az 1. lépésben.
3. Hozzon létre egy formátumfájlt keresztül mutatja az adatok exportálása/importálása folyamatban a következő tábla sémáját. A formátumfájl részleteit ismerteti a [hozzon létre egy Formátumfájlt (SQL Server)](https://msdn.microsoft.com/library/ms191516.aspx).

    Formázza a fájl létrehozása a jelentés futtatásakor BCP az SQL Server-számítógépről

        bcp dbname..tablename format nul -c -x -f exportformatfilename.xml -S servername\sqlinstance -T -t \t -r \n

    Formázza a fájl létrehozása a jelentés futtatásakor BCP távoli SQL-kiszolgálón

        bcp dbname..tablename format nul -c -x -f  exportformatfilename.xml  -U username@servername.database.windows.net -S tcp:servername -P password  --t \t -r \n
4. A szakaszban ismertetett módszerekkel [áthelyezése adatforrásból származó fájl](#filesource_to_sqlonazurevm) az adatok áthelyezése egy SQL Server a egybesimított fájlokba.

### <a name="sql-migration"></a>SQL-adatbázis áttelepítése varázsló
[SQL Server adatbázis áttelepítése varázsló](http://sqlazuremw.codeplex.com/) felhasználóbarát biztosítja az adatok áthelyezése két SQL server példányai között. A felhasználó a képezi le a forrás és a céltábla közötti adatkulcsokat típusú oszlopokat és a különböző egyéb funkciók válasszon. A tömeges másolási (BCP) a színfalak használ. Az alábbiakban látható egy Képernyőkép az SQL-adatbázis áttelepítése varázsló az üdvözlőképernyő.  

![SQL Server varázsló][2]

### <a name="sql-backup"></a>Adatbázis biztonsági mentése és visszaállítása
SQL Server támogatja:

1. [Adatbázis biztonsági mentése és visszaállítása funkció](https://msdn.microsoft.com/library/ms187048.aspx) (mind a helyi fájl vagy bacpac exportálása blob) és [adatok rétegből álló alkalmazások](https://msdn.microsoft.com/library/ee210546.aspx) (bacpac használatával).
2. Képes közvetlenül létrehozása a másolt adatbázis vagy egy meglévő SQL Azure Database másolása az Azure SQL Server virtuális gépen. További részletekért lásd: [adatbázis másolása varázsló használatával](https://msdn.microsoft.com/library/ms188664.aspx).

Fel/helyreállítani a képernyőképen az adatbázis biztonsági beállításokat az SQL Server Management Studio alább láthatók.

![SQL Server Import eszközt][1]

## <a name="resources"></a>Erőforrások
[Egy Azure virtuális Gépen lévő SQL Server adatbázis áttelepítése](../../virtual-machines/windows/sql/virtual-machines-windows-migrate-sql.md)

[Az SQL Server használata Azure virtuális gépeken – áttekintés](../../virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)

[1]: ./media/move-sql-server-virtual-machine/sqlserver_builtin_utilities.png
[2]: ./media/move-sql-server-virtual-machine/database_migration_wizard.png
