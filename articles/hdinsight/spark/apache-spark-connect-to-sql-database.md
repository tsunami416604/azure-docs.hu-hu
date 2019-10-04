---
title: A Apache Spark használata az Azure SQL Database-be való adatolvasásra és-írásra
description: Megtudhatja, hogyan állíthat be kapcsolatot a HDInsight Spark-fürt és egy Azure SQL-adatbázis között az adatolvasáshoz, az adatíráshoz és az adatfolyamok SQL Database-be történő olvasásához
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: 74bff4059442c85cfcde589c5a6cc7ab36472881
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71147043"
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>Az HDInsight Spark-fürt használata az Azure SQL Database-be való adatolvasásra és-írásra

Ismerje meg, hogyan csatlakoztatható Apache Spark-fürt az Azure HDInsight egy Azure SQL Database-adatbázishoz, majd hogyan lehet olvasni, írni és továbbítani az információkat az SQL Database-be. A cikkben szereplő utasítások egy [Jupyter notebook](https://jupyter.org/) használatával futtatják a Scala-kódrészleteket. Létrehozhat azonban egy önálló alkalmazást a Scalaben vagy a Pythonban, és ugyanazokat a feladatokat hajthatja végre.

## <a name="prerequisites"></a>Előfeltételek

* **Azure HDInsight Spark-fürt**.  Kövesse az [Apache Spark-fürt létrehozása a HDInsight-ben](apache-spark-jupyter-spark-sql.md)című témakör utasításait.

* **Azure SQL Database**. Kövesse az [Azure SQL Database létrehozása](../../sql-database/sql-database-get-started-portal.md)című témakör utasításait. Győződjön meg arról, hogy létrehoz egy adatbázist a minta **AdventureWorksLT** sémával és az adattal. Győződjön meg arról is, hogy a kiszolgálói szintű tűzfalszabály létrehozása lehetővé teszi, hogy az ügyfél IP-címe hozzáférjen az SQL-adatbázishoz a kiszolgálón. A tűzfalszabály hozzáadására vonatkozó utasítások ugyanabban a cikkben találhatók. Miután létrehozta az Azure SQL Database-adatbázist, ügyeljen rá, hogy a következő értékeket érdemes megtartania. Szüksége van rájuk, hogy egy Spark-fürtből csatlakozhasson az adatbázishoz.

    * Az Azure SQL Database-t futtató kiszolgáló neve.
    * Azure SQL Database-adatbázis neve.
    * Azure SQL Database-rendszergazda Felhasználónév/jelszó.

* **SQL Server Management Studio**. Kövesse az [SSMS használata a kapcsolódáshoz és az adatlekérdezéshez](../../sql-database/sql-database-connect-query-ssms.md)című témakör utasításait.

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

    ![Adjon nevet a notebooknak](./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png "Adjon nevet a notebooknak")

Most már megkezdheti az alkalmazás létrehozását.
    
## <a name="read-data-from-azure-sql-database"></a>Adatok beolvasása az Azure SQL Database-ből

Ebben a szakaszban egy tábla (például **SalesLT. címe**) adatait olvassa be a AdventureWorks-adatbázisban.

1. Egy új Jupyter jegyzetfüzetben írja be a következő kódrészletet, és cserélje le a helyőrző értékeket az Azure SQL Database értékeire.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    A kódcella futtatásához nyomja le a **SHIFT + ENTER** billentyűparancsot.  

1. Az alábbi kódrészlet használatával létrehozhat egy olyan JDBC URL-címet, amelyet átadhat a Spark dataframe API- `Properties` khoz, létrehoz egy objektumot, amely a paramétereket tartja. Illessze be a kódrészletet egy kód cellájába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt a futtatáshoz.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")         

1. Az alábbi kódrészlet használatával hozzon létre egy dataframe az Azure SQL Database egyik táblájából származó adatokkal. Ebben a kódrészletben a **AdventureWorksLT** -adatbázis részeként elérhető **SalesLT. címünket** használjuk. Illessze be a kódrészletet egy kód cellájába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt a futtatáshoz.

       val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)

1. Mostantól műveleteket hajthat végre a dataframe, például beolvashatja az adatsémát:

       sqlTableDF.printSchema
   
    A következőhöz hasonló kimenet jelenik meg:

    ![séma kimenete](./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png "séma kimenete")

1. Olyan műveleteket is végrehajthat, mint például az első 10 sor beolvasása.

       sqlTableDF.show(10)

1. Vagy lekérhet bizonyos oszlopokat az adatkészletből.

       sqlTableDF.select("AddressLine1", "City").show(10)

## <a name="write-data-into-azure-sql-database"></a>Adatírás az Azure SQL Database-be

Ebben a szakaszban egy CSV-fájlt használunk a fürtön, amely létrehoz egy táblát az Azure SQL Database-ben, és feltölti azokat az adatokkal. A minta CSV-fájl (**HVAC. csv**) az összes HDInsight-fürtön `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv`elérhető.

1. Egy új Jupyter jegyzetfüzetben írja be a következő kódrészletet, és cserélje le a helyőrző értékeket az Azure SQL Database értékeire.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    A kódcella futtatásához nyomja le a **SHIFT + ENTER** billentyűparancsot.  

1. Az alábbi kódrészlet egy olyan JDBC URL-címet hoz létre, amelyet átadhat a Spark dataframe `Properties` API-khoz, és létrehoz egy objektumot, amely a paramétereket fogja tárolni. Illessze be a kódrészletet egy kód cellájába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt a futtatáshoz.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")

1. A következő kódrészlettel bontsa ki az adatok sémáját a HVAC. csv fájlban, és a séma használatával töltse be a CSV-fájlból a dataframe `readDf`. Illessze be a kódrészletet egy kód cellájába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt a futtatáshoz.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

1. Hozzon `readDf` létre egy ideiglenes `temphvactable`táblát a dataframe használatával. Ezután az ideiglenes táblázat segítségével hozzon létre egy kaptár- `hvactable_hive`táblázatot.

       readDf.createOrReplaceTempView("temphvactable")
       spark.sql("create table hvactable_hive as select * from temphvactable")

1. Végül a kaptár táblázat segítségével hozzon létre egy táblát az Azure SQL Database-ben. Az alábbi kódrészlet az `hvactable` Azure SQL Database-ben jön létre.

       spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)

1. Kapcsolódjon az Azure SQL Database-hez az SSMS használatával, és ellenőrizze `dbo.hvactable` , hogy látható-e.

    a. Indítsa el a SSMS, és kapcsolódjon az Azure SQL Database-hez a kapcsolat részleteinek megadásával, ahogy az alábbi képernyőképen is látható.

    ![Kapcsolódás az SQL Database-hez a SSMS1 használatával](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png "Kapcsolódás az SQL Database-hez a SSMS1 használatával")

    b. A Object Explorer bontsa ki az Azure SQL Database és a Table csomópontot, hogy megtekintse a **dbo. hvactable** létrejöttét.

    ![Kapcsolódás az SQL Database-hez a SSMS2 használatával](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png "Kapcsolódás az SQL Database-hez a SSMS2 használatával")

1. Futtasson egy lekérdezést a SSMS-ben a tábla oszlopainak megtekintéséhez.

        SELECT * from hvactable

## <a name="stream-data-into-azure-sql-database"></a>Adatfolyamok beküldése az Azure SQL Database-be

Ebben a szakaszban az előző szakaszban az Azure SQL Database-ben már létrehozott **hvactable** továbbítjuk az adatátvitelt.

1. Első lépésként győződjön meg arról, hogy nincsenek rekordok a **hvactable**. A SSMS használatával futtassa a következő lekérdezést a táblán.

       TRUNCATE TABLE [dbo].[hvactable]

1. Hozzon létre egy új Jupyter-jegyzetfüzetet a HDInsight Spark-fürtön. Illessze be a következő kódrészletet a kód cellájába, majd nyomja le a **SHIFT + ENTER**billentyűkombinációt:

       import org.apache.spark.sql._
       import org.apache.spark.sql.types._
       import org.apache.spark.sql.functions._
       import org.apache.spark.sql.streaming._
       import java.sql.{Connection,DriverManager,ResultSet}

1. Az adatok továbbítása a **HVAC. csv** fájlból a hvactable. A HVAC. csv fájl a következő helyen érhető el `/HdiSamples/HdiSamples/SensorSampleData/HVAC/`a fürtön:. A következő kódrészletben először megkapjuk a továbbított adatmennyiség sémáját. Ezután létrehozunk egy streaming dataframe az adott séma használatával. Illessze be a kódrészletet egy kód cellájába, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt a futtatáshoz.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readStreamDf = spark.readStream.schema(userSchema).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
       readStreamDf.printSchema

1. A kimenet a **HVAC. csv**sémáját jeleníti meg. A **hvactable** ugyanazzal a sémával is rendelkezik. A kimenet listázza a tábla oszlopait.

    ![hdinsight Apache Spark séma tábla](./media/apache-spark-connect-to-sql-database/hdinsight-schema-table.png "Tábla sémája")

1. Végül használja az alábbi kódrészletet az adatok beolvasásához a HVAC. csv fájlból, és továbbítsa azt a **hvactable** az Azure SQL Database-ben. Illessze be a kódrészletet egy kód cellájába, cserélje le a helyőrző értékeket az Azure SQL Database-adatbázis értékeire, majd nyomja le a **SHIFT + ENTER** billentyűkombinációt a futtatáshoz.

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

1. A következő lekérdezés futtatásával ellenőrizze, hogy a rendszer a **hvactable** továbbítja-e az adatátvitelt a SQL Server Management Studio (SSMS). A lekérdezés minden futtatásakor a tábla sorainak száma növekszik.

        SELECT COUNT(*) FROM hvactable

## <a name="next-steps"></a>További lépések

* [Az HDInsight Spark-fürt használata Data Lake Storageban lévő adatelemzéshez](apache-spark-use-with-data-lake-store.md)
* [Strukturált adatfolyam-események feldolgozása a EventHub használatával](apache-spark-eventhub-structured-streaming.md)
* [Apache Spark strukturált streaming használata a HDInsight Apache Kafka használatával](../hdinsight-apache-kafka-spark-structured-streaming.md)
