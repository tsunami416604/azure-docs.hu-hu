---
title: Adatok másolása az Azure Data Lake Storage Gen1 és a Sqoop használata Azure SQL database között |} A Microsoft Docs
description: Adatok másolása az Azure SQL Database és az Azure Data Lake Storage Gen1 között a Sqoop használatával
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 3f914b2a-83cc-4950-b3f7-69c921851683
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 7d3283b03d15278d1f7fd42a72b154dab1a442b4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58878527"
---
# <a name="copy-data-between-azure-data-lake-storage-gen1-and-azure-sql-database-using-sqoop"></a>Adatok másolása az Azure Data Lake Storage Gen1 és a Sqoop használata Azure SQL database között
Ismerje meg, hogyan Apache Sqoop használatával Azure SQL Database és az Azure Data Lake Storage Gen1 közötti adatok importálása és exportálása.

## <a name="what-is-sqoop"></a>Mit jelent a sqoop használatával?
Big data-alkalmazások használata természetes választás strukturálatlan és részben strukturált adatok, például naplók és a fájlok feldolgozására. Azonban előfordulhat, hogy is van szükség, amely a relációs adatbázisokban tárolt strukturált adatok feldolgozása.

[Az Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) eszközt úgy tervezték, relációs adatbázisok és a egy big Data típusú adatok adattár, például a Data Lake Storage Gen1 közötti adatátvitelhez. Adatokat importálhat egy relációs adatbázis-kezelő rendszer (RDBMS) például az Azure SQL Database Data Lake Storage Gen1, használhatja azt. Majd átalakíthatja és elemezheti az adatokat, használja a big data számítási feladatok és majd exportálja az adatokat egy RDBMS be újra. Ebben az oktatóanyagban az Azure SQL Database mint segítségével a relációs adatbázis az importálási és exportálási.

## <a name="prerequisites"></a>Előfeltételek
A cikk elkezdéséhez az alábbiakkal kell rendelkeznie:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Az Azure Data Lake Storage Gen1 fiók**. Létrehozásával kapcsolatos utasításokért lásd: [Ismerkedés az Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Az Azure HDInsight-fürt** hozzáférést egy Data Lake Storage Gen1 fiókot. Lásd: [egy HDInsight-fürt létrehozása a Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Ez a cikk feltételezi, hogy a Data Lake Storage Gen1 hozzáféréssel rendelkező HDInsight Linux-fürt.
* **Azure SQL Database** Létrehozásával kapcsolatos utasításokért lásd: [hozzon létre egy Azure SQL database](../sql-database/sql-database-get-started.md)

## <a name="do-you-learn-fast-with-videos"></a>Gyorsan tanul videók segítségével?
[Ebben a videóban](https://mix.office.com/watch/1butcdjxmu114) hogyan másolhat adatokat az Azure Storage-Blobok és a Data Lake Storage Gen1 között a DistCp használata.

## <a name="create-sample-tables-in-the-azure-sql-database"></a>Az Azure SQL Database mintául szolgáló tábla létrehozásához
1. Első lépésként hozzon létre két minta táblák az Azure SQL Database-ben. Használat [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) vagy a Visual Studio az Azure SQL Database-adatbázishoz csatlakozhat, és futtassa a következő lekérdezéseket.

    **Tábla1 létrehozása**

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
2. A **tábla1**, adjon hozzá néhány adatot. Hagyja **Table2** üres. Importáljuk az adatokat **tábla1** Data Lake Storage Gen1 be. Majd, hogy exportálja az adatokat a Data Lake Storage Gen1 be **Table2**. Futtassa az alábbi kódrészletet.

        INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');


## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-storage-gen1"></a>Hozzáférés egy HDInsight-fürtöt a Sqoop használatával Data Lake Storage Gen1
Egy HDInsight-fürt már elérhető a Sqoop-csomagokat. Ha konfigurálta a HDInsight-fürt használata a Data Lake Storage Gen1 egy kiegészítő tárolóként, segítségével Sqoop (nélkül végrehajtott bármilyen konfigurációs módosításokat) között (az ebben a példában az Azure SQL Database) a relációs adatok importálása és exportálása és a egy Data Lake Tárfiók Gen1.

1. Ebben az oktatóanyagban azt feltételeztük, létrehozott egy Linux-fürt, úgy csatlakozhat a fürthöz SSH kell használnia. Lásd: [csatlakozás Linux-alapú HDInsight-fürthöz](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).
2. Győződjön meg arról, hogy a Data Lake Storage Gen1 fiók hozzáférhet a fürtből. Futtassa a következő parancsot a SSH használatával:

        hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net/

    Ez a Data Lake Storage Gen1 fiókban lévő fájlok és mappák listáját kell biztosítania.

### <a name="import-data-from-azure-sql-database-into-data-lake-storage-gen1"></a>Adatokat importálhat az Azure SQL Database, Data Lake Storage Gen1
1. Navigáljon ahhoz a könyvtárhoz, ahol a Sqoop csomagok érhetők el. Általában ez lesz a `/usr/hdp/<version>/sqoop/bin`.
2. Az adatimportálás **tábla1** a Data Lake Storage Gen1 figyelembe. Az alábbi szintaxissal:

        sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

    Vegye figyelembe, hogy **sql-adatbázis – kiszolgálónév** helyőrző a hol futnak az Azure SQL database-kiszolgáló nevét jelenti. **SQL-adatbázis-name** helyőrző tényleges adatbázis nevét jelöli.

    Például:


        sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=twooley@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1

1. Győződjön meg arról, hogy az adatokat a Data Lake Storage Gen1 fiók át lett adva. Futtassa az alábbi parancsot:

        hdfs dfs -ls adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/

    A következő kimenetnek kell megjelennie.


        -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
        -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
        -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
        -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
        -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

    Minden egyes **. rész – m -*** fájl megfelel egy sort a forrástábla **Table1**. A rész - m - tartalmát is megtekintheti * fájlok ellenőrzése.


### <a name="export-data-from-data-lake-storage-gen1-into-azure-sql-database"></a>Data Lake Storage Gen1 adatok exportálása az Azure SQL Database-be
1. Exportálja az adatokat a Data Lake Storage Gen1 fiókból a üres tábla **Table2**, az Azure SQL Database-ben. Az alábbi szintaxissal.

        sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

    Például:


        sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=twooley@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

1. Győződjön meg arról, hogy az adatok feltöltése az SQL-adatbázistáblába. Használat [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) vagy a Visual Studio az Azure SQL Database-adatbázishoz csatlakozhat, és a következő lekérdezés futtatásával.

        SELECT * FROM TABLE2

    Ez a következő kimenetet kell rendelkeznie.

         ID  FName   LName
        ------------------
        1    Neal    Kell
        2    Lila    Fulton
        3    Erna    Myers
        4    Annette    Simpson

## <a name="performance-considerations-while-using-sqoop"></a>Teljesítménnyel kapcsolatos megfontolások a Sqoop használata közben

Teljesítmény-finomhangolási Sqoop feladat másolja az adatokat a Data Lake Storage Gen1, lásd: [Sqoop teljesítmény dokumentum](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/).

## <a name="see-also"></a>Lásd még
* [Adatok másolása az Azure Storage-Blobokból a Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)
* [Az Azure Data Lake Analytics használata a Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata a Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
