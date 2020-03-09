---
title: A Apache Spark használatával olvashatja és írhatja az adatAzure SQL Database
description: Megtudhatja, hogyan állíthat be kapcsolatot a HDInsight Spark-fürt és a Azure SQL Database között az adatolvasáshoz, az adatíráshoz és az adatfolyamok SQL-adatbázisba való olvasásához.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/05/2020
ms.openlocfilehash: 4e0c1626582297aa7d80cbbd4241b6f81e314f8f
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2020
ms.locfileid: "78927461"
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>Az HDInsight Spark-fürt használata az Azure SQL Databaseba való adatolvasásra és írásra

Megtudhatja, hogyan csatlakoztatható egy Apache Spark-fürt az Azure HDInsight egy Azure SQL Database, majd hogyan lehet olvasni, írni és továbbítani az információkat az SQL Database-be. A cikkben szereplő utasítások egy [Jupyter notebook](https://jupyter.org/) használatával futtatják a Scala-kódrészleteket. Létrehozhat azonban egy önálló alkalmazást a Scalaben vagy a Pythonban, és ugyanazokat a feladatokat hajthatja végre.

## <a name="prerequisites"></a>Előfeltételek

* Azure HDInsight Spark-fürt.  Kövesse az [Apache Spark-fürt létrehozása a HDInsight-ben](apache-spark-jupyter-spark-sql.md)című témakör utasításait.

* Azure SQL Database. Kövesse az [Azure SQL Database létrehozása](../../sql-database/sql-database-get-started-portal.md)című témakör utasításait. Győződjön meg arról, hogy létrehoz egy adatbázist a minta **AdventureWorksLT** sémával és az adattal. Győződjön meg arról is, hogy a kiszolgálói szintű tűzfalszabály létrehozása lehetővé teszi, hogy az ügyfél IP-címe hozzáférjen az SQL-adatbázishoz a kiszolgálón. A tűzfalszabály hozzáadására vonatkozó utasítások ugyanabban a cikkben találhatók. A Azure SQL Database létrehozása után ügyeljen arra, hogy a következő értékeket érdemes megtartania. Szüksége van rájuk, hogy egy Spark-fürtből csatlakozhasson az adatbázishoz.

    * A Azure SQL Database üzemeltető kiszolgáló neve.
    * Azure SQL Database neve.
    * Azure SQL Database rendszergazdai Felhasználónév/jelszó.

* SQL Server Management Studio (SSMS). Kövesse az [SSMS használata a kapcsolódáshoz és az adatlekérdezéshez](../../sql-database/sql-database-connect-query-ssms.md)című témakör utasításait.

## <a name="create-a-jupyter-notebook"></a>Jupyter Notebook létrehozása

Kezdje a Spark-fürthöz társított [Jupyter notebook](https://jupyter.org/) létrehozásával. Ezt a jegyzetfüzetet használja a cikkben használt kódrészletek futtatásához.

1. A [Azure Portal](https://portal.azure.com/)nyissa meg a fürtöt.
1. A jobb oldalon válassza a **Jupyter notebook** alatt a **fürt irányítópultok** elemet.  Ha nem látja a **fürt irányítópultját**, válassza az **Áttekintés** lehetőséget a bal oldali menüben. Ha a rendszer felkéri rá, adja meg a fürthöz tartozó rendszergazdai hitelesítő adatokat.

    ![Jupyter notebook on Apache Spark](./media/apache-spark-connect-to-sql-database/hdinsight-spark-cluster-dashboard-jupyter-notebook.png "Jupyter notebook a Sparkban")

   > [!NOTE]  
   > A Jupyter-jegyzetfüzetet a Spark-fürtön is elérheti, ha megnyitja a következő URL-címet a böngészőben. Cserélje le a **CLUSTERNAME** elemet a fürt nevére:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. A Jupyter jegyzetfüzetben, a jobb felső sarokban kattintson az **új**elemre, majd a **Spark** elemre a Scala notebook létrehozásához. A HDInsight Spark-fürtön található Jupyter-jegyzetfüzetek a Python2-alkalmazások **PySpark** -kernelét, valamint a PySpark3-alkalmazások **Python3** -kernelét is biztosítják. Ebben a cikkben egy Scala-jegyzetfüzetet hozunk létre.

    ![Kernelek Jupyter notebookhoz a Sparkban](./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png "Kernelek Jupyter notebookhoz a Sparkban")

    A kernelekkel kapcsolatos további információkért lásd: [Jupyter notebookkernelek használata Apache Spark-fürtökkel a HDInsightban](apache-spark-jupyter-notebook-kernels.md).

   > [!NOTE]  
   > Ebben a cikkben a Spark (Scala) kernelt használjuk, mert a Sparkból az SQL Database-be továbbított adatok csak a Scala és a Java szolgáltatásban támogatottak. Bár az SQL-re való olvasás és az SQL-be való írás a Python használatával végezhető el, az ebben a cikkben szereplő konzisztencia érdekében mindhárom művelethez a Scalat használjuk.

1. Ekkor megnyílik egy új, alapértelmezett névvel rendelkező jegyzetfüzet, **névtelenül**. Kattintson a jegyzetfüzet nevére, és adjon meg egy tetszőleges nevet.

    ![Adja meg a jegyzetfüzet nevét](./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png "A notebook elnevezése")

Most már megkezdheti az alkalmazás létrehozását.

## <a name="read-data-from-azure-sql-database"></a>Adatok beolvasása a Azure SQL Databaseból

Ebben a szakaszban egy tábla (például **SalesLT. címe**) adatait olvassa be a AdventureWorks-adatbázisban.

1. Egy új Jupyter jegyzetfüzetben írja be a következő kódrészletet, és cserélje le a helyőrző értékeket a Azure SQL Database értékeire.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    A kódcella futtatásához nyomja le a **SHIFT + ENTER** billentyűparancsot.  

1. Az alábbi kódrészlettel hozzon létre egy JDBC URL-címet, amelyet át tud adni a Spark dataframe API-khoz. A kód létrehoz egy `Properties` objektumot a paraméterek tárolásához. Illessze be a kódrészletet egy kód cellájába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt a futtatáshoz.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")         

1. Az alábbi kódrészlet használatával hozzon létre egy dataframe a Azure SQL Database egy táblából származó adatokkal. Ebben a kódrészletben egy `SalesLT.Address` táblázatot használunk, amely a **AdventureWorksLT** -adatbázis részeként érhető el. Illessze be a kódrészletet egy kód cellájába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt a futtatáshoz.

       val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)

1. Mostantól műveleteket hajthat végre a dataframe, például beolvashatja az adatsémát:

       sqlTableDF.printSchema

    A következőhöz hasonló kimenet jelenik meg:

    ![séma kimenete](./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png "séma kimenete")

1. Olyan műveleteket is végrehajthat, mint például az első 10 sor beolvasása.

       sqlTableDF.show(10)

1. Vagy lekérhet bizonyos oszlopokat az adatkészletből.

       sqlTableDF.select("AddressLine1", "City").show(10)

## <a name="write-data-into-azure-sql-database"></a>Adatírás az Azure SQL Databaseba

Ebben a szakaszban egy CSV-fájlt használunk a fürtön, amely létrehoz egy táblázatot a Azure SQL Databaseban, és feltölti azokat az adatokkal. A minta CSV-fájl (**HVAC. csv**) az összes HDInsight-fürtön elérhető a következő helyen: `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv`.

1. Egy új Jupyter jegyzetfüzetben írja be a következő kódrészletet, és cserélje le a helyőrző értékeket a Azure SQL Database értékeire.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    A kódcella futtatásához nyomja le a **SHIFT + ENTER** billentyűparancsot.  

1. Az alábbi kódrészlet egy JDBC URL-címet hoz létre, amelyet átadhat a Spark dataframe API-khoz. A kód létrehoz egy `Properties` objektumot a paraméterek tárolásához. Illessze be a kódrészletet egy kód cellájába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt a futtatáshoz.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")

1. A következő kódrészlettel kinyerheti az adatok sémáját a HVAC. csv fájlban, és a séma használatával betöltheti az adatait a CSV-dataframe, `readDf`. Illessze be a kódrészletet egy kód cellájába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt a futtatáshoz.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

1. Egy ideiglenes tábla létrehozásához használja a `readDf` dataframe `temphvactable`. Ezután az ideiglenes táblázat segítségével hozzon létre egy kaptár-táblázatot, `hvactable_hive`.

       readDf.createOrReplaceTempView("temphvactable")
       spark.sql("create table hvactable_hive as select * from temphvactable")

1. Végül a kaptár táblázat segítségével hozzon létre egy táblázatot a Azure SQL Database. A következő kódrészlet `hvactable` hoz létre a Azure SQL Databaseban.

       spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)

1. Kapcsolódjon a Azure SQL Database a SSMS használatával, és ellenőrizze, hogy megjelenik-e a `dbo.hvactable`.

    a. Indítsa el a SSMS, és kapcsolódjon a Azure SQL Databasehoz a kapcsolat részleteinek megadásával, ahogy az alábbi képernyőképen is látható.

    ![Kapcsolódás az SQL Database-hez a SSMS1 használatával](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png "Kapcsolódás az SQL Database-hez a SSMS1 használatával")

    b. **Object Explorerból**bontsa ki a Azure SQL Database és a tábla csomópontot, hogy megtekintse a **dbo. hvactable** létrejöttét.

    ![Kapcsolódás az SQL Database-hez a SSMS2 használatával](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png "Kapcsolódás az SQL Database-hez a SSMS2 használatával")

1. Futtasson egy lekérdezést a SSMS-ben a tábla oszlopainak megtekintéséhez.

    ```sql
    SELECT * from hvactable
    ```

## <a name="stream-data-into-azure-sql-database"></a>Adatfolyam-továbbítás Azure SQL Databaseba

Ebben a szakaszban az adatátvitelt az előző szakaszban Azure SQL Databaseban már létrehozott `hvactable`ba.

1. Első lépésként győződjön meg arról, hogy nincsenek rekordok a `hvactable`. A SSMS használatával futtassa a következő lekérdezést a táblán.

    ```sql
    TRUNCATE TABLE [dbo].[hvactable]
    ```

1. Hozzon létre egy új Jupyter-jegyzetfüzetet a HDInsight Spark-fürtön. Illessze be a következő kódrészletet a kód cellájába, majd nyomja le a **SHIFT + ENTER**billentyűkombinációt:

       import org.apache.spark.sql._
       import org.apache.spark.sql.types._
       import org.apache.spark.sql.functions._
       import org.apache.spark.sql.streaming._
       import java.sql.{Connection,DriverManager,ResultSet}

1. Az adatok továbbítása a **HVAC. csv** fájlból a `hvactable`ba. A HVAC. csv fájl elérhető a fürtön `/HdiSamples/HdiSamples/SensorSampleData/HVAC/`on. A következő kódrészletben először megkapjuk a továbbított adatmennyiség sémáját. Ezután létrehozunk egy streaming dataframe az adott séma használatával. Illessze be a kódrészletet egy kód cellájába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt a futtatáshoz.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readStreamDf = spark.readStream.schema(userSchema).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
       readStreamDf.printSchema

1. A kimenet a **HVAC. csv**sémáját jeleníti meg. A `hvactable` is ugyanazzal a sémával rendelkezik. A kimenet listázza a tábla oszlopait.

    ![hdinsight Apache Spark séma tábla](./media/apache-spark-connect-to-sql-database/hdinsight-schema-table.png "Tábla sémája")

1. Végül használja az alábbi kódrészletet az adatok beolvasásához a HVAC. csv fájlból, és továbbítsa azt a Azure SQL Database `hvactable`ba. Illessze be a kódrészletet egy cellába, cserélje le a helyőrző értékeket a Azure SQL Database értékeire, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt a futtatáshoz.

       val WriteToSQLQuery  = readStreamDf.writeStream.foreach(new ForeachWriter[Row] {
          var connection:java.sql.Connection = _
          var statement:java.sql.Statement = _
          
          val jdbcUsername = "<SQL DB ADMIN USER>"
          val jdbcPassword = "<SQL DB ADMIN PWD>"
          val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
          val jdbcPort = 1433
          val jdbcDatabase ="<AZURE SQL DB NAME>"
          val driver = "com.microsoft.sqlserver.jdbc.SQLServerDriver"
          val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
  
         def open(partitionId: Long, version: Long):Boolean = {
           Class.forName(driver)
           connection = DriverManager.getConnection(jdbc_url, jdbcUsername, jdbcPassword)
           statement = connection.createStatement
           true
         }
  
         def process(value: Row): Unit = {
           val Date  = value(0)
           val Time = value(1)
           val TargetTemp = value(2)
           val ActualTemp = value(3)
           val System = value(4)
           val SystemAge = value(5)
           val BuildingID = value(6)  
    
           val valueStr = "'" + Date + "'," + "'" + Time + "'," + "'" + TargetTemp + "'," + "'" + ActualTemp + "'," + "'" + System + "'," + "'" + SystemAge + "'," + "'" + BuildingID + "'"
           statement.execute("INSERT INTO " + "dbo.hvactable" + " VALUES (" + valueStr + ")")   
           }

         def close(errorOrNull: Throwable): Unit = {
            connection.close
          }
         })
        
         var streamingQuery = WriteToSQLQuery.start()

1. A következő lekérdezés SQL Server Management Studio (SSMS) futtatásával ellenőrizze, hogy a rendszer a `hvactable` adatfolyamként továbbítja-e az adatátvitelt. A lekérdezés minden futtatásakor a tábla sorainak száma növekszik.

    ```sql
    SELECT COUNT(*) FROM hvactable
    ```

## <a name="next-steps"></a>Következő lépések

* [Az HDInsight Spark-fürt használata Data Lake Storageban lévő adatelemzéshez](apache-spark-use-with-data-lake-store.md)
* [Strukturált adatfolyam-események feldolgozása a EventHub használatával](apache-spark-eventhub-structured-streaming.md)
* [Apache Spark strukturált streaming használata a HDInsight Apache Kafka használatával](../hdinsight-apache-kafka-spark-structured-streaming.md)
