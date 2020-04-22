---
title: Adatok olvasása és írása az Azure SQL Database-be az Apache Spark használatával
description: Ismerje meg, hogyan állíthat be kapcsolatot a HDInsight Spark-fürt és egy Azure SQL-adatbázis között. Adatok olvasása, adatok írása és adatfolyamként való streamelése SQL-adatbázisba
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/20/2020
ms.openlocfilehash: 4e783a233bd35e012c02fbbbdc7f4223552fc734
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686847"
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>Adatok olvasása és írása az Azure SQL Database-be HDInsight Spark-fürthasználatával

Ismerje meg, hogyan csatlakoztathat egy Apache Spark-fürtöt az Azure HDInsightban egy Azure SQL-adatbázissal. Ezután olvassa el, írja és streamelje az adatokat az SQL-adatbázisba. A cikkben található utasítások egy Jupyter-jegyzetfüzetet használnak a Scala-kódkódrészletek futtatásához. Azonban létrehozhat egy önálló alkalmazást a Scala vagy a Python, és ugyanazokat a feladatokat.

## <a name="prerequisites"></a>Előfeltételek

* Azure HDInsight Spark-fürt.  Kövesse az [Apache Spark-fürt létrehozása a HDInsightban](apache-spark-jupyter-spark-sql.md)című útmutatóutasításait.

* az Azure SQL Database-szel szemben. Kövesse az [Azure SQL-adatbázis létrehozása című útmutatóutasításait.](../../sql-database/sql-database-get-started-portal.md) Győződjön meg arról, hogy hozzon létre egy adatbázist a minta **AdventureWorksLT** séma és az adatok. Győződjön meg arról is, hogy hozzon létre egy kiszolgálószintű tűzfalszabályt, amely lehetővé teszi, hogy az ügyfél IP-címe hozzáférjen a kiszolgálósql-adatbázisához. A tűzfalszabály hozzáadására vonatkozó utasítások ugyanebben a cikkben találhatók. Miután létrehozta az Azure SQL-adatbázist, győződjön meg róla, hogy a következő értékek et hasznos. Szüksége van rájuk, hogy csatlakozzon az adatbázishoz egy Spark-fürtből.

    * Az Azure SQL-adatbázist tároló kiszolgálónév.
    * Az Azure SQL Database neve.
    * Azure SQL Database rendszergazdai felhasználónév / jelszó.

* SQL Server Management Studio (SSMS). Az adatok [csatlakoztatásához és lekérdezéséhez](../../sql-database/sql-database-connect-query-ssms.md)kövesse az SSMS használata című útmutató utasításait.

## <a name="create-a-jupyter-notebook"></a>Jupyter-notebook létrehozása

Első a Spark-fürthöz társított Jupyter-jegyzetfüzet létrehozásával. Ezzel a jegyzetfüzettel futtathatja a cikkben használt kódrészleteket.

1. Az [Azure Portalon](https://portal.azure.com/)nyissa meg a fürtöt.
1. Válassza a **Jupyter-jegyzetfüzet** lehetőséget a jobb oldali **Fürtirányítópultok** alatt.  Ha nem látható **a Fürtirányítópultok,** válassza a bal oldali menü **Áttekintés parancsát.** Ha a rendszer felkéri rá, adja meg a fürthöz tartozó rendszergazdai hitelesítő adatokat.

    ![Jupyter notebook az Apache Sparkon](./media/apache-spark-connect-to-sql-database/hdinsight-spark-cluster-dashboard-jupyter-notebook.png "Jupyter notebook a Sparkon")

   > [!NOTE]  
   > A Jupyter-jegyzetfüzetet a Spark-fürtön is elérheti a következő URL-cím megnyitásával a böngészőben. Cserélje le a **CLUSTERNAME-t** a fürt nevére:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

1. A Jupyter-jegyzetfüzetjobb felső sarkában kattintson az **Új**gombra, majd a **Spark** elemre a Scala-jegyzetfüzet létrehozásához. Jupyter notebookok HDInsight Spark-fürtön is biztosítja a **PySpark** kernel Python2-alkalmazások, és a **PySpark3** kernel Python3-alkalmazások. Ebben a cikkben hozzon létre egy Scala notebook.

    ![Kernelek a Jupyter-jegyzetfüzethez a Sparkon](./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png "Kernelek a Jupyter-jegyzetfüzethez a Sparkon")

    A kernelekkel kapcsolatos további információkért lásd: [Jupyter notebookkernelek használata Apache Spark-fürtökkel a HDInsightban](apache-spark-jupyter-notebook-kernels.md).

   > [!NOTE]  
   > Ebben a cikkben egy Spark (Scala) kernelt használunk, mert a Sparkból az SQL-adatbázisba történő adatfolyam-továbbítás jelenleg csak a Scala és a Java támogatja. Annak ellenére, hogy az SQL-ből való olvasás és az SQL-be írás python használatával elvégezhető, a jelen cikkben szereplő konzisztencia érdekében a Scala-t mindhárom művelethez használjuk.

1. Megnyílik egy új jegyzetfüzet, amelynek alapértelmezett neve **Untitled**. Kattintson a jegyzetfüzet nevére, és adjon meg egy választott nevet.

    ![A notebook elnevezése](./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png "A notebook elnevezése")

Most már elkezdheti az alkalmazás létrehozását.

## <a name="read-data-from-azure-sql-database"></a>Adatok olvasása az Azure SQL Database-ből

Ebben a szakaszban az AdventureWorks adatbázisban található táblából (például **SalesLT.Address)** olvasható.

1. Egy új Jupyter-jegyzetfüzetben egy kódcellába illessze be a következő kódrészletet, és cserélje le a helyőrző értékeket az Azure SQL Database értékeire.

    ```scala
    // Declare the values for your Azure SQL database

    val jdbcUsername = "<SQL DB ADMIN USER>"
    val jdbcPassword = "<SQL DB ADMIN PWD>"
    val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
    val jdbcPort = 1433
    val jdbcDatabase ="<AZURE SQL DB NAME>"
    ```

    A kódcella futtatásához nyomja le a **SHIFT + ENTER** billentyűparancsot.  

1. Az alábbi kódrészlet segítségével hozzon létre egy JDBC-URL-címet, amelyet átadhat a Spark dataframe API-knak. A kód `Properties` létrehoz egy objektumot a paraméterek tárolására. Illessze be a kódrészletet egy kódcellába, és a futtatáshoz nyomja le a **SHIFT + ENTER billentyűkombinációt.**

    ```scala
    import java.util.Properties

    val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
    val connectionProperties = new Properties()
    connectionProperties.put("user", s"${jdbcUsername}")
    connectionProperties.put("password", s"${jdbcPassword}")
    ```

1. Az alábbi kódrészlet segítségével hozzon létre egy dataframe az adatokat egy tábla az Azure SQL Database.Use the kódrészlet below to create a dataframe with the datadata from a table in your Azure SQL Database. Ebben a kódrészletben egy `SalesLT.Address` olyan táblát használunk, amely az **AdventureWorksLT** adatbázis részeként érhető el. Illessze be a kódrészletet egy kódcellába, és a futtatáshoz nyomja le a **SHIFT + ENTER billentyűkombinációt.**

    ```scala
    val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)
    ```

1. Most már műveleteket az adatkereten, például az adatséma beszerzése:

    ```scala
    sqlTableDF.printSchema
    ```

    Az alábbihoz hasonló kimenet jelenik meg:

    ![séma kimenete](./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png "séma kimenete")

1. Olyan műveleteket is elvégezhet, mint a felső 10 sor beolvasása.

    ```scala
    sqlTableDF.show(10)
    ```

1. Vagy lekérheti az adott oszlopokat az adatkészletből.

    ```scala
    sqlTableDF.select("AddressLine1", "City").show(10)
    ```

## <a name="write-data-into-azure-sql-database"></a>Adatok írása az Azure SQL Database-be

Ebben a szakaszban egy minta CSV-fájlt használunk a fürtön elérhető egy tábla létrehozásához az Azure SQL Database-ben, és feltölti azt az adatokkal. A minta CSV fájl (**HVAC.csv**) minden `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv`HDInsight-fürtön elérhető a .

1. Egy új Jupyter-jegyzetfüzetben egy kódcellába illessze be a következő kódrészletet, és cserélje le a helyőrző értékeket az Azure SQL Database értékeire.

    ```scala
    // Declare the values for your Azure SQL database

    val jdbcUsername = "<SQL DB ADMIN USER>"
    val jdbcPassword = "<SQL DB ADMIN PWD>"
    val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
    val jdbcPort = 1433
    val jdbcDatabase ="<AZURE SQL DB NAME>"
    ```

    A kódcella futtatásához nyomja le a **SHIFT + ENTER** billentyűparancsot.  

1. A következő kódrészlet egy JDBC-URL-címet hoz létre, amelyet átadhat a Spark dataframe API-knak. A kód `Properties` létrehoz egy objektumot a paraméterek tárolására. Illessze be a kódrészletet egy kódcellába, és a futtatáshoz nyomja le a **SHIFT + ENTER billentyűkombinációt.**

    ```scala
    import java.util.Properties

    val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
    val connectionProperties = new Properties()
    connectionProperties.put("user", s"${jdbcUsername}")
    connectionProperties.put("password", s"${jdbcPassword}")
    ```

1. Az alábbi kódrészlet segítségével bontsa ki a HVAC.csv-ben lévő adatok sémáját, és a sémával töltse be az adatokat a CSV-ből egy adatkeretbe. `readDf` Illessze be a kódrészletet egy kódcellába, és a futtatáshoz nyomja le a **SHIFT + ENTER billentyűkombinációt.**

    ```scala
    val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
    val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

1. Az `readDf` adatkeret segítségével hozzon `temphvactable`létre egy ideiglenes táblát, . Ezután az ideiglenes tábla segítségével hozzon létre egy kaptártáblát. `hvactable_hive`

    ```scala
    readDf.createOrReplaceTempView("temphvactable")
    spark.sql("create table hvactable_hive as select * from temphvactable")
    ```

1. Végül a struktúratábla segítségével hozzon létre egy táblát az Azure SQL Database-ben. A következő kódrészlet `hvactable` et hoz létre az Azure SQL Database-ben.

    ```scala
    spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)
    ```

1. Csatlakozzon az Azure SQL-adatbázishoz az SSMS használatával, és ellenőrizze, hogy ott lát-e egy. `dbo.hvactable`

    a. Indítsa el az SSMS-t, és csatlakozzon az Azure SQL-adatbázishoz a kapcsolat részleteinek megadásával, ahogy az az alábbi képernyőképen látható.

    ![Csatlakozás SQL-adatbázishoz ssms1 használatával](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png "Csatlakozás SQL-adatbázishoz ssms1 használatával")

    b. Az **Objektumkezelőből**bontsa ki az Azure SQL-adatbázist és a táblacsomópontot a létrehozott **dbo.hvactable** megtekintéséhez.

    ![Csatlakozás SQL-adatbázishoz SSMS2 használatával](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png "Csatlakozás SQL-adatbázishoz SSMS2 használatával")

1. Futtasson lekérdezést az SSMS-ben a tábla oszlopainak megtekintéséhez.

    ```sql
    SELECT * from hvactable
    ```

## <a name="stream-data-into-azure-sql-database"></a>Adatok streamelése az Azure SQL Database-be

Ebben a szakaszban az `hvactable` adatokat az Azure SQL Database az előző szakaszban már létrehozott adatokat.

1. Első lépésként győződjön meg arról, `hvactable`hogy nincsenek rekordok a ban. Az SSMS használatával futtassa a következő lekérdezést a táblán.

    ```sql
    TRUNCATE TABLE [dbo].[hvactable]
    ```

1. Hozzon létre egy új Jupyter-jegyzetfüzetet a HDInsight Spark-fürtön. A kódcellába illessze be a következő részletet, majd nyomja le a **SHIFT + ENTER billentyűkombinációt:**

    ```scala
    import org.apache.spark.sql._
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    import org.apache.spark.sql.streaming._
    import java.sql.{Connection,DriverManager,ResultSet}
    ```

1. A **HVAC.csv-ből** adatokat `hvactable`továbbítunk a . A HVAC.csv fájl a `/HdiSamples/HdiSamples/SensorSampleData/HVAC/`fürtön érhető el a (. A következő kódrészletben először az adatfolyamként továbbítandó adatok sémáját kapjuk meg. Ezután létrehozunk egy streamelési adatkeretet a séma használatával. Illessze be a kódrészletet egy kódcellába, és a futtatáshoz nyomja le a **SHIFT + ENTER billentyűkombinációt.**

    ```scala
    val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
    val readStreamDf = spark.readStream.schema(userSchema).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
    readStreamDf.printSchema
    ```

1. A kimenet a **HVAC.csv**sémáját mutatja. A `hvactable` ugyanaz a séma is. A kimenet a táblázat oszlopait sorolja fel.

    !["hdinsight Apache Spark sématábla"](./media/apache-spark-connect-to-sql-database/hdinsight-schema-table.png "Tábla sémája")

1. Végül a következő kódrészlet segítségével olvassa be az adatokat a HVAC.csv fájlból, és továbbítsa azokat az `hvactable` Azure SQL Database-be. Illessze be a kódrészletet egy kódcellába, cserélje le a helyőrző értékeket az Azure SQL Database értékeire, majd nyomja le a **SHIFT + ENTER billentyűkombinációt** a futtatáshoz.

    ```scala
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
    ```

1. Ellenőrizze, hogy az adatok streamelése az SQL Server Management Studio (SSMS) következő lekérdezésének futtatásával történik.Verify that the data is streamed into the `hvactable` by running the following query in SQL Server Management Studio (SSMS). A lekérdezés minden futtatásakor a tábla sorainak száma növekszik.

    ```sql
    SELECT COUNT(*) FROM hvactable
    ```

## <a name="next-steps"></a>További lépések

* [Adatok elemzése a Data Lake Storage-ban a HDInsight Spark-fürthasználatával](apache-spark-use-with-data-lake-store.md)
* [Adatok betöltése és lekérdezések futtatása Apache Spark-fürtön az Azure HDInsightban](apache-spark-load-data-run-query.md)
* [Az Apache Spark strukturált streamelésének használata az Apache Kafkával a HDInsighton](../hdinsight-apache-kafka-spark-structured-streaming.md)
