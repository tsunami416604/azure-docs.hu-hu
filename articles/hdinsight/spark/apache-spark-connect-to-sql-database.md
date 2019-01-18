---
title: Az Apache Spark használata olvasása és írása az adatok Azure SQL Database-adatbázishoz
description: Ismerje meg, hogyan állítható be egy HDInsight Spark-fürt és az adatok olvasása, írása, és az adatfolyam adatok egy SQL database-be egy Azure SQL database közötti kapcsolat
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/15/2018
ms.openlocfilehash: 2b818350c19d1d9ff34bcdac0e438def0f859e40
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54381902"
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>HDInsight Spark-fürt használatával olvasása és írása az adatok Azure SQL Database-adatbázishoz

Útmutató Apache Spark-fürt az Azure HDInsight összekapcsolása egy Azure SQL database és olvasni, írni és streamelhet adatokat az SQL database-be. Az utasításokat a jelen cikk használatát egy [Jupyter Notebook](https://jupyter.org/) Scala kódrészletek végrehajtásához. Azonban önálló alkalmazás létrehozása a Scala-vagy Python, és hajtsa végre a feladatot. 

## <a name="prerequisites"></a>Előfeltételek

* **Az Azure HDInsight Spark-fürt**.  Kövesse az utasításokat, [Apache Spark-fürt létrehozása HDInsight](apache-spark-jupyter-spark-sql.md).

* **Az Azure SQL database**. Kövesse az utasításokat, [hozzon létre egy Azure SQL database](../../sql-database/sql-database-get-started-portal.md). Ellenőrizze, hogy egy adatbázist hoz létre a mintával **AdventureWorksLT** séma és adatok. Ügyeljen arra, hogy létrehozott egy kiszolgálószintű tűzfalszabályt, hogy az ügyfél IP-címet a kiszolgálón az SQL-adatbázis eléréséhez. Ugyanebben a cikkben található utasításokat a tűzfalszabály hozzáadása érhető el. Miután létrehozta az Azure SQL database, ellenőrizze, hogy praktikus tartsa a következő értékeket. Szükség van rájuk a Spark-fürt kapcsolódni az adatbázishoz.

    * Az Azure SQL-adatbázist futtató kiszolgáló nevét.
    * Az Azure SQL-adatbázis neve.
    * Az Azure SQL database rendszergazda felhasználónév / jelszó.

* **SQL Server Management Studio**. Kövesse az utasításokat, [csatlakozás és adatok lekérdezése az SSMS használatával](../../sql-database/sql-database-connect-query-ssms.md).

## <a name="create-a-jupyter-notebook"></a>Jupyter Notebook létrehozása 

Először hozzon létre egy [Jupyter Notebook](https://jupyter.org/) a Spark-fürthöz társított. Az ebben a cikkben használt kódrészleteket futtatni a notebookot fog használni. 

1. Az a [az Azure portal](https://portal.azure.com/), nyissa meg a fürtöt.
1. Válassza ki **Jupyter notebook** alá **fürt irányítópultjai** jobb oldalán.  Ha nem lát **fürt irányítópultjai**, kattintson a **áttekintése** a panelen a bal oldali menüből. Ha a rendszer felkéri rá, adja meg a fürthöz tartozó rendszergazdai hitelesítő adatokat.

    ![A Jupyter notebook Spark](./media/apache-spark-connect-to-sql-database/hdinsight-spark-cluster-dashboard-jupyter-notebook.png "Spark a Jupyter notebook")
   
   > [!NOTE]  
   > A Jupyter notebook Spark-fürtön nyissa meg a következő URL-címet a böngészőben is hozzáférhet. Cserélje le a **CLUSTERNAME** elemet a fürt nevére:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. Kattintson a jobb felső sarokban, a Jupyter notebook **új**, és kattintson a **Spark** Scala notebook létrehozásához. Jupyter notebookok a HDInsight Spark-fürt is biztosítanak a **PySpark** kernel Python2-alkalmazások, és a **PySpark3** kernel Python3-alkalmazásokhoz. Ebben a cikkben létrehozunk egy Scala-jegyzetfüzetek.
   
    ![Kernelek Jupyter notebookokhoz Spark](./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png "kernelek Jupyter notebookokhoz Spark")

    A kernelekkel kapcsolatos további információkért lásd: [Jupyter notebookkernelek használata Apache Spark-fürtökkel a HDInsightban](apache-spark-jupyter-notebook-kernels.md).

   > [!NOTE]  
   > Ebben a cikkben használjuk egy Spark (Scala) kernel mert az SQL database-be a Spark streamelési adatok csak akkor támogatott a Scala és Java jelenleg. Annak ellenére, hogy olvasása és írása az SQL-be végezhető konzisztencia ebben a cikkben használja Python, Scala a használjuk mindhárom művelet.

1. Ekkor megnyílik egy új jegyzetfüzetet, egy alapértelmezett névvel rendelkező **névtelen**. A notebook nevére kattint, és adjon meg egy tetszőleges nevet.

    ![Adjon nevet a notebooknak](./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png "Adjon nevet a notebooknak")

Most már megkezdheti az alkalmazás létrehozásának.
    
## <a name="read-data-from-azure-sql-database"></a>Olvassa el az adatok Azure SQL database-ből

Ebben a szakaszban adatokat olvasni a táblában (például **SalesLT.Address**), amely az AdventureWorks adatbázisban van.

1. Új Jupyter notebook kódcellába illessze be az alábbi kódrészletet, és a helyőrző értékeket cserélje le az Azure SQL database értékeit.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    A kódcella futtatásához nyomja le a **SHIFT + ENTER** billentyűparancsot.  

1. Használja az alábbi kódrészlet egy JDBC URL-cím összeállítását adhat át az API-kat hoz létre a Spark dataframe- `Properties` objektum, amely tárolja a paramétereket. Illessze be a kódtöredéket a kódcellába, majd nyomja le **SHIFT + ENTER** futtatásához.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")         

1. Az alábbi kódrészlet használatával hozzon létre dataframe-az adatokat az Azure SQL database egyik táblájába. Ebben a kódrészletben használjuk egy **SalesLT.Address** táblát, amely elérhető része a **AdventureWorksLT** adatbázis. Illessze be a kódtöredéket a kódcellába, majd nyomja le **SHIFT + ENTER** futtatásához.

       val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)

1. Most már az adathalmaz, például az első az adatséma műveleteket végezheti el:

       sqlTableDF.printSchema
   
    Egy a következőhöz hasonló kimenet jelenik meg:

    ![Adjon nevet a notebooknak](./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png "Adjon nevet a notebooknak")

1. Is elvégezheti műveletekre – például olvashatók be az első 10 sort.

       sqlTableDF.show(10)

1. Másik lehetőségként bizonyos oszlopokat kérjen le az adatkészletből.

       sqlTableDF.select("AddressLine1", "City").show(10)

## <a name="write-data-into-azure-sql-database"></a>Adatok írása az Azure SQL database-be

Ebben a szakaszban használjuk egy CSV-minta elérhető a fürtön az Azure SQL database tábla létrehozása és adatokkal való feltöltéséhez. A CSV-mintafájlt (**HVAC.csv**) érhető el, az összes HDInsight-fürtök `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv`.

1. Új Jupyter notebook kódcellába illessze be az alábbi kódrészletet, és a helyőrző értékeket cserélje le az Azure SQL database értékeit.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    A kódcella futtatásához nyomja le a **SHIFT + ENTER** billentyűparancsot.  

1. Az alábbi kódrészlet egy JDBC URL-címet, amely az API-kat hoz létre a Spark dataframe átadható összeállít egy `Properties` objektum, amely tárolja a paramétereket. Illessze be a kódtöredéket a kódcellába, majd nyomja le **SHIFT + ENTER** futtatásához.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")

1. A következő kódrészletet használja a séma HVAC.csv az adatok kinyerése és a séma segítségével az adatok betöltéséhez az dataframe, a fürt megosztott kötetei szolgáltatás `readDf`. Illessze be a kódtöredéket a kódcellába, majd nyomja le **SHIFT + ENTER** futtatásához.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

1. Használja a `readDf` dataframe egy ideiglenes tábla létrehozásához `temphvactable`. Az ideiglenes tábla használatával hive-tábla létrehozása `hvactable_hive`.

       readDf.createOrReplaceTempView("temphvactable")
       spark.sql("create table hvactable_hive as select * from temphvactable")

1. Végül a hive-tábla használatával hozzon létre egy táblát az Azure SQL database-ben. A következő kódrészlet létrehozza `hvactable` Azure SQL database-ben.

       spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)

1. Az Azure SQL database, az SSMS használatával csatlakozhat, és győződjön meg arról, hogy megjelenik egy `dbo.hvactable` van.

    a. Indítsa el az ssms-ben, és csatlakozzon az Azure SQL database kapcsolati adatok megadásával, az alábbi képernyőképen látható módon.

    ![Csatlakozás SQL Database-adatbázishoz az SSMS használatával](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png "Csatlakozás SQL Database-adatbázishoz az SSMS használatával")

    b. Az Object Explorer ablaktáblában bontsa ki a az Azure SQL database és a tábla csomópont megtekintéséhez a **dbo.hvactable** létrehozott.

    ![Csatlakozás SQL Database-adatbázishoz az SSMS használatával](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png "Csatlakozás SQL Database-adatbázishoz az SSMS használatával")

1. Futtassa a lekérdezés az ssms-ben, tekintse meg a tábla oszlopai.

        SELECT * from hvactable

## <a name="stream-data-into-azure-sql-database"></a>Stream-adatokat az Azure SQL database-be

Ebben a szakaszban azt streamelése adattárházba a **hvactable** , hogy már létrehozott Azure SQL Database, az előző szakaszban.

1. Első lépésként ellenőrizze, hogy nincsenek a rekordok a **hvactable**. SSMS használatával futtassa a következő lekérdezést a tábla.

       DELETE FROM [dbo].[hvactable]

1. Új Jupyter notebook létrehozása a HDInsight Spark-fürtön. Kódcellába, illessze be az alábbi kódrészletet, és nyomja le az **SHIFT + ENTER**:

       import org.apache.spark.sql._
       import org.apache.spark.sql.types._
       import org.apache.spark.sql.functions._
       import org.apache.spark.sql.streaming._
       import java.sql.{Connection,DriverManager,ResultSet}

1. Hogy az adatok streamelése a **HVAC.csv** a hvactable be. HVAC.csv fájl érhető el, a fürt */HdiSamples/HdiSamples/SensorSampleDataHVAC/*. Az alábbi kódrészletben először lekérjük az adatséma is streamelhetők. Ezután létrehozunk egy streamelési dataframe, hogy a séma használatával. Illessze be a kódtöredéket a kódcellába, majd nyomja le **SHIFT + ENTER** futtatásához.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readStreamDf = spark.readStream.schema(userSchema).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
       readStreamDf.printSchema

1. A kimenet mutatja sémája **HVAC.csv**. A **hvactable** ugyanazt a sémát is. A kimenet az oszlopok a tábla sorolja fel.

    ![Tábla sémája](./media/apache-spark-connect-to-sql-database/schema-of-table.png "tábla sémája")

1. Végül használja az alábbi kódrészlet adatokat olvasni az HVAC.csv, illetve streamelni be azt a **hvactable** Azure SQL database-ben. Illessze be a kódtöredéket a kódcellába, cserélje le a helyőrző értékeket az Azure SQL database értékeit, majd nyomja le **SHIFT + ENTER** futtatásához.

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

1. Győződjön meg arról, hogy az adatok folyamatos be a **hvactable** az SQL Server Management Studio (SSMS) a következő lekérdezés futtatásával. A lekérdezés minden futtatásakor a tábla növekvő mutatja a sorok száma.

        SELECT COUNT(*) FROM hvactable

## <a name="next-steps"></a>További lépések

* [HDInsight Spark-fürt használata a Data Lake Storage lévő adatok elemzéséhez](apache-spark-use-with-data-lake-store.md)
* [Strukturált streamelési események feldolgozása az EventHub használatával](apache-spark-eventhub-structured-streaming.md)
* [Az Apache Spark strukturált Stream használata a Apache Kafka on HDInsight használata](../hdinsight-apache-kafka-spark-structured-streaming.md)
