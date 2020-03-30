---
title: Adatok másolása a Data Lake Storage Gen1 és az Azure SQL - Sqoop között | Microsoft dokumentumok
description: Adatok másolása az Azure SQL Database és az Azure Data Lake Storage Gen1 között a Sqoop használatával
services: data-lake-store
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: twooley
ms.openlocfilehash: cf3893706afcb4c4cc5b90dd3d2431ecedc71d0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73839060"
---
# <a name="copy-data-between-data-lake-storage-gen1-and-azure-sql-database-using-sqoop"></a>Adatok másolása a Data Lake Storage Gen1 és az Azure SQL Database között a Sqoop használatával

Ismerje meg, hogyan importálhat és exportálhat adatokat az Azure SQL Database és az Azure Data Lake Storage Gen1 között az Apache Sqoop használatával.

## <a name="what-is-sqoop"></a>Milyen helyzetben van a Sqoop?

A big data-alkalmazások természetes választás a strukturálatlan és félig strukturált adatok, például a naplók és a fájlok feldolgozásához. Előfordulhat azonban, hogy a relációs adatbázisokban tárolt strukturált adatokat is fel kell dolgoznia.

[Az Apache Sqoop](https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html) egy olyan eszköz, amely etarol a relációs adatbázisok és egy big data-tárház között, például a Data Lake Storage Gen1 között. Segítségével importálhat adatokat egy relációs adatbázis-kezelő rendszerből (RDBMS), például az Azure SQL Database-ből a Data Lake Storage Gen1-be. Ezután átalakíthatja és elemezheti az adatokat big data-számítási feladatok használatával, majd exportálhatja az adatokat rdbms-be. Ebben a cikkben egy Azure SQL-adatbázist használhat relációs adatbázisként az importáláshoz/exportáláshoz.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, rendelkeznie kell a következőkkel:

* **Azure-előfizetés**. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).
* **Egy Azure Data Lake Storage Gen1 fiók.** A fiók létrehozásáról az [Azure Data Lake Storage Gen1 című témakörben talál útmutatást.](data-lake-store-get-started-portal.md)
* **Azure HDInsight-fürt,** amely hozzáférést biztosít a Data Lake Storage Gen1 fiókhoz. Lásd: [HDInsight-fürt létrehozása a Data Lake Storage Gen1 szolgáltatással című témakört.](data-lake-store-hdinsight-hadoop-use-portal.md) Ez a cikk feltételezi, hogy egy HDInsight Linux-fürt a Data Lake Storage Gen1 hozzáféréssel rendelkezik.
* **Az Azure SQL Database .** Az [azure-beli SQL-adatbázis](../sql-database/sql-database-get-started.md) létrehozásáról további információt az Azure SQL-adatbázis létrehozása című témakörben talál.

## <a name="create-sample-tables-in-the-azure-sql-database"></a>Mintatáblák létrehozása az Azure SQL-adatbázisban

1. A kezdéshez hozzon létre két mintatáblát az Azure SQL-adatbázisban. Az [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) vagy a Visual Studio segítségével csatlakozzon az adatbázishoz, majd futtassa a következő lekérdezéseket.

    **Tábla létrehozása1**

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

    **Tábla2 létrehozása**

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

1. Futtassa a következő parancsot, és adjon hozzá néhány mintaadatot a **Table1 táblázathoz.** Hagyja üresen **a 2.** Később a **Table1-ből** adatokat importál a Data Lake Storage Gen1-be. Ezután a Data Lake Storage Gen1-ből exportálja az adatokat **a Table2 programba.**

       INSERT INTO [dbo].[Table1] VALUES (1,'Neal','Kell'), (2,'Lila','Fulton'), (3, 'Erna','Myers'), (4,'Annette','Simpson');

## <a name="use-sqoop-from-an-hdinsight-cluster-with-access-to-data-lake-storage-gen1"></a>A Sqoop használata a Data Lake Storage Gen1 szolgáltatáshoz hozzáféréssel rendelkező HDInsight-fürtből

Egy HDInsight-fürt már rendelkezik a Sqoop-csomagokkal. Ha úgy állította be a HDInsight-fürtöt, hogy a Data Lake Storage Gen1-et használja további tárként, a Sqoop (konfigurációs módosítások nélkül) használatával importálhatja/exportálhatja az adatokat egy relációs adatbázis, például az Azure SQL Database és a Data Lake Storage Gen1 fiók között.

1. Ebben a cikkben feltételezzük, hogy létrehozott egy Linux-fürtöt, így az SSH használatával kell csatlakoznia a fürthöz. Lásd: [Csatlakozás Linux alapú HDInsight-fürthöz](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

1. Ellenőrizze, hogy a fürtből hozzá tud-e férni a Data Lake Storage Gen1 fiókhoz. Futtassa a következő parancsot az SSH parancssorból:

       hdfs dfs -ls adl://<data_lake_storage_gen1_account>.azuredatalakestore.net/

   Ez a parancs a Data Lake Storage Gen1 fiókban lévő fájlok/mappák listáját tartalmazza.

### <a name="import-data-from-azure-sql-database-into-data-lake-storage-gen1"></a>Adatok importálása az Azure SQL Database-ből a Data Lake Storage Gen1 szolgáltatásba

1. Keresse meg azt a könyvtárat, ahol a Sqoop csomagok elérhetők. Ez a hely `/usr/hdp/<version>/sqoop/bin`általában a .

1. Importálja az adatokat a **Table1-ből** a Data Lake Storage Gen1 fiókba. Használja a következő szintaxist:

       sqoop-import --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table1 --target-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1

   Az **sql-database-server-name** helyőrző annak a kiszolgálónak a nevét jelöli, amelyen az Azure SQL-adatbázis fut. **az sql-database-name** helyőrző a tényleges adatbázisnevet jelöli.

   Például:

       sqoop-import --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=twooley@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table1 --target-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1

1. Ellenőrizze, hogy az adatok átvitele megtörtént-e a Data Lake Storage Gen1 fiókba. Futtassa az alábbi parancsot:

       hdfs dfs -ls adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/

   A következő kimenetnek kell megjelennie.

       -rwxrwxrwx   0 sshuser hdfs          0 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/_SUCCESS
       -rwxrwxrwx   0 sshuser hdfs         12 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00000
       -rwxrwxrwx   0 sshuser hdfs         14 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00001
       -rwxrwxrwx   0 sshuser hdfs         13 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00002
       -rwxrwxrwx   0 sshuser hdfs         18 2016-02-26 21:09 adl://hdiadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1/part-m-00003

   Minden **egyes rész-m-*** fájl a forrástábla **Table1**. Megtekintheti az ellenőrizendő rész-m-* fájlok tartalmát.

### <a name="export-data-from-data-lake-storage-gen1-into-azure-sql-database"></a>Adatok exportálása a Data Lake Storage Gen1 szolgáltatásból az Azure SQL Database szolgáltatásba

1. Exportálja az adatokat a Data Lake Storage Gen1 fiókból az Üres **table Table2**táblába az Azure SQL Database-ben. Használja az alábbi szintaxist.

       sqoop-export --connect "jdbc:sqlserver://<sql-database-server-name>.database.windows.net:1433;username=<username>@<sql-database-server-name>;password=<password>;database=<sql-database-name>" --table Table2 --export-dir adl://<data-lake-storage-gen1-name>.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

   Például:

       sqoop-export --connect "jdbc:sqlserver://mysqoopserver.database.windows.net:1433;username=twooley@mysqoopserver;password=<password>;database=mysqoopdatabase" --table Table2 --export-dir adl://myadlsg1store.azuredatalakestore.net/Sqoop/SqoopImportTable1 --input-fields-terminated-by ","

1. Ellenőrizze, hogy az adatok at az SQL Database táblába töltötték-e fel. Az [SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md) vagy a Visual Studio segítségével csatlakozzon az Azure SQL-adatbázishoz, majd futtassa a következő lekérdezést.

       SELECT * FROM TABLE2

   Ennek a parancsnak a következő kimenettel kell rendelkeznie.

        ID  FName    LName
       -------------------
       1    Neal     Kell
       2    Lila     Fulton
       3    Erna     Myers
       4    Annette  Simpson

## <a name="performance-considerations-while-using-sqoop"></a>Teljesítménybeli szempontok a Sqoop használata közben

A Sqoop-feladat data-adatok Data Lake Storage Gen1-be történő másolásához szükséges teljesítményhangolásról a [Sqoop teljesítményblogbejegyzésében talál.](https://blogs.msdn.microsoft.com/bigdatasupport/2015/02/17/sqoop-job-performance-tuning-in-hdinsight-hadoop/)

## <a name="next-steps"></a>További lépések

* [Adatok másolása az Azure Storage Blobs szolgáltatásból a Data Lake Storage Gen1 szolgáltatásba](data-lake-store-copy-data-azure-storage-blob.md)
* [Az adatok védelme az 1. generációs Data Lake Storage-ban](data-lake-store-secure-data.md)
* [Az Azure Data Lake Analytics használata a Data Lake Storage Gen1 szolgáltatással](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Az Azure HDInsight használata a Data Lake Storage Gen1 szolgáltatással](data-lake-store-hdinsight-hadoop-use-portal.md)
