---
title: Data Lake Store és a Sqoop használatával Azure SQL-adatbázis közötti adatok másolása |} Microsoft Docs
description: Másolja az adatokat az Azure SQL Database és a Data Lake Store között a Sqoop használatával
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 3f914b2a-83cc-4950-b3f7-69c921851683
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 2e68f74976c25a5c2db32867dabca147f5c30e80
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="copy-data-between-data-lake-store-and-azure-sql-database-using-sqoop"></a>Data Lake Store és a Sqoop használatával Azure SQL-adatbázis közötti adatok másolása
Útmutató Apache Sqoop használatával importálhat és exportálhat adatokat az Azure SQL Database és a Data Lake Store között.

## <a name="what-is-sqoop"></a>Mi az a Sqoop?
Big data-alkalmazások olyan természetes téve a strukturálatlan és félig strukturált adatok, például a naplókat, valamint a fájlok feldolgozása. Azonban is lehet a relációs adatbázisok tárolt strukturált adatok feldolgozása érdekében.

[Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) eszköz a relációs adatbázisok és a big Data típusú adatok tára, például a Data Lake Store között továbbított adatok. Adatokat importálhat egy relációs adatbázis-kezelő rendszer (RDBMS) például az Azure SQL Database Data Lake Store az használhatja. Majd átalakíthatja és elemezheti az adatokat, használja a big data-számítási feladatok és exportálhatja az adatokat egy RDBMS programba. Ebben az oktatóanyagban az Azure SQL Database mint segítségével a relációs adatbázis az importálási/exportálási.

## <a name="prerequisites"></a>Előfeltételek
A cikk elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Egy Azure Data Lake Store-fiók**. Hogyan hozhat létre ilyet, lásd: [Ismerkedés az Azure Data Lake Store](data-lake-store-get-started-portal.md)
* **Az Azure HDInsight-fürt** a Data Lake Store-fiók eléréséhez. Lásd: [HDInsight-fürtök létrehozása a Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Ez a cikk feltételezi, hogy rendelkezik-e egy Data Lake Store-hozzáféréssel rendelkező HDInsight Linux-fürt.
* **Azure SQL Database** Hogyan hozhat létre ilyet, lásd: [Azure SQL-adatbázis létrehozása](../sql-database/sql-database-get-started.md)

## <a name="do-you-learn-fast-with-videos"></a>Gyorsan tanul videók segítségével?
[A videó](https://mix.office.com/watch/1butcdjxmu114) az adatok Azure Storage Blobs és a Data Lake Store használatának ból a DistCp közötti másolása.

## <a name="create-sample-tables-in-the-azure-sql-database"></a>A minta-táblázatok létrehozása az Azure SQL-adatbázis
1. Először hozzon létre két minta tábla az Azure SQL Database. Használjon [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) vagy a Visual Studio használatával csatlakozzon az Azure SQL Database, és futtassa az alábbi lekérdezéseket.

    **Table1 létrehozása**

        CREATE TABLE [dbo].[Table1](
        [ID] [int] NOT NULL,
        [FName] [nvarchar](50) NOT NULL,
        [LName] [nvarchar](50) NOT NULL,
         CONSTRAINT [PK_Table_1] PRIMARY KEY CLUSTERED
            (
                   [ID] ASC
            )
        ) ON [PRIMARY]
        GO

    **Table2 létrehozása**

        CREATE TABLE [dbo].[Table2](
        [ID] [int] NOT NULL,
        [FName] [nvarchar](50) NOT NULL,
        [LName] [nvarchar](50) NOT NULL,
         CONSTRAINT [PK_Table_2] PRIMARY KEY CLUSTERED
            (
                   [ID] ASC
            )
        ) ON [PRIMARY]
        GO
2. A **Table1**, vegye fel néhány adatot. Hagyja **Table2** üres. Az adatok fogunk importálni **Table1** a Data Lake Store. Majd, hogy exportálja az adatokat a Data Lake Store az **Table2**. Futtassa a következő kódrészletet.

        INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');


## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-store"></a>HDInsight-fürtök hozzáféréssel rendelkező Sqoop használatával Data Lake Store
HDInsight-fürtök már elérhető a Sqoop csomagokat. Ha konfigurálta a HDInsight-fürt használata a Data Lake Store további tárterületként, segítségével Sqoop (nélkül végrehajtott bármilyen konfigurációs módosításokat) importálási/exportálási között (a példában az Azure SQL Database) a relációs adatok és a Data Lake Store-fiók.

1. Ebben az oktatóanyagban feltételezzük, hogy létrehozott egy Linux-fürt, csatlakozzon a fürthöz SSH kell használnia. Lásd: [csatlakozás egy Linux-alapú HDInsight-fürt](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).
2. Győződjön meg arról, hogy a Data Lake Store-fiók hozzáférhet a fürtből. A következő parancsot az SSH-parancssorból:

        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net/

    Ez biztosítania kell a fájlok és mappák a Data Lake Store-fiókban listáját.

### <a name="import-data-from-azure-sql-database-into-data-lake-store"></a>Adatokat importálhat az Azure SQL Database Data Lake Store
1. Navigáljon ahhoz a könyvtárhoz, ahol a Sqoop csomagok érhetők el. Általában ez lesz `/usr/hdp/<version>/sqoop/bin`.
2. Az adatimportálás **Table1** be a Data Lake Store-fiókba. A következő szintaxissal:

        sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

    Vegye figyelembe, hogy **sql-adatbázis-kiszolgálónév** helyőrző az Azure SQL-adatbázist futtató kiszolgáló nevét. **SQL-adatbázis-neve** helyőrző az aktuális adatbázis nevét.

    Például:


        sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1

1. Győződjön meg arról, hogy az adatok átvitele volt-e a Data Lake Store-fiók. Futtassa az alábbi parancsot:

        hdfs dfs -ls adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/

    A következő kimenetet kell megjelennie.


        -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
        -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
        -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
        -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
        -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

    Minden egyes **rész-m -*** fájl megfelel-e a forrástábla sorában **Table1**. Megtekintheti az - m - elem tartalmának * fájlok ellenőrzése.


### <a name="export-data-from-data-lake-store-into-azure-sql-database"></a>Adatok exportálása az Azure SQL Database Data Lake Store-ból
1. Exportálja az adatokat a Data Lake Store-fiókot az üres táblázat **Table2**, az Azure SQL-adatbázisban. A következő szintaxissal.

        sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-store-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

    Például:


        sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=nitinme@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlstore.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

1. Győződjön meg arról, hogy az SQL-adatbázis táblája feltöltött adatokat. Használjon [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) vagy a Visual Studio használatával csatlakozzon az Azure SQL Database, és futtassa a következő lekérdezés.

        SELECT * FROM TABLE2

    Ez a következő kimenetet kell rendelkeznie.

         ID  FName   LName
        ------------------
        1    Neal    Kell
        2    Lila    Fulton
        3    Erna    Myers
        4    Annette    Simpson

## <a name="performance-considerations-while-using-sqoop"></a>Sqoop használatakor a teljesítménnyel kapcsolatos szempontok

Másolja az adatokat a Data Lake Store a Sqoop feladat teljesítményhangolás, lásd: [Sqoop teljesítmény dokumentum](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/).

## <a name="see-also"></a>Lásd még
* [Adatok másolása az Azure Storage Blobs Data Lake Store-bA](data-lake-store-copy-data-azure-storage-blob.md)
* [Biztonságos adattárolás a Data Lake Store-ban](data-lake-store-secure-data.md)
* [Az Azure Data Lake Analytics használata a Data Lake Store-ral](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata a Data Lake Store-ral](data-lake-store-hdinsight-hadoop-use-portal.md)
