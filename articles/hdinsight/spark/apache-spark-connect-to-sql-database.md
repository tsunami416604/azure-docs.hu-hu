---
title: Apache Spark segítségével olvasása és írása az adatokat az Azure SQL database |} Microsoft Docs
description: Útmutató egy HDInsight Spark-fürt és -adatok olvasása, írása, és az adatfolyam adatok egy SQL-adatbázisba Azure SQL-adatbázis közötti kapcsolat
services: hdinsight
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: nitinme
ms.openlocfilehash: 6ef0b1ce589bd19693d45a9e4f579ef260530a40
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>Olvasási és írási adatokat az Azure SQL database HDInsight Spark-fürt használatával

Útmutató: Azure HDInsight az Apache Spark-fürt csatlakozzon az Azure SQL-adatbázis és majd olvasási, írási és az SQL-adatbázisba adatok folyamatos átviteléhez. A jelen cikkben lévő utasítások Jupyter notebook használatával futtassa a Scala kódrészleteket. Azonban egy önálló alkalmazás létrehozása a Scala vagy Python, és hajtsa végre a ugyanazokhoz a feladatokhoz. 

## <a name="prerequisites"></a>Előfeltételek

* **Az Azure HDInsight Spark-fürt**.  Kövesse az utasításokat, [Apache Spark-fürt létrehozása hdinsight](apache-spark-jupyter-spark-sql.md).

* **Az Azure SQL adatbázis**. Kövesse az utasításokat, [hozzon létre egy Azure SQL-adatbázis](../../sql-database/sql-database-get-started-portal.md). Ellenőrizze, hogy az adatbázis létrehozása a mintával **AdventureWorksLT** séma- és adatokat. Ellenőrizze azt is, létrehozhat egy kiszolgálószintű tűzfalszabály engedélyezése az ügyfél IP-címét a kiszolgálón az SQL-adatbázis eléréséhez. Az utasításokat a tűzfalszabály ugyanabban a cikkben érhető el. Egyszer hozott létre az Azure SQL-adatbázis, ellenőrizze, hogy praktikus tartsa a következő értékeket. Már szükség a Spark-fürt csatlakozni az adatbázishoz.

    * Az Azure SQL-adatbázist futtató kiszolgáló neve
    * Az Azure SQL-adatbázis neve
    * Az Azure SQL adatbázis rendszergazda felhasználónév / jelszó

* **SQL Server Management Studio**. Kövesse az utasításokat, [használata SSMS való kapcsolódás és lekérdezés az adatok](../../sql-database/sql-database-connect-query-ssms.md).

## <a name="create-a-jupyter-notebook"></a>Jupyter notebook létrehozása

Először hozzon létre a Spark-fürt társított Jupyter notebook. A jegyzetfüzet segítségével futtassa a kódrészleteket a cikk ezt használja. 

1. Az a [Azure-portálon](https://portal.azure.com/), nyissa meg a fürt. 

2. Az a **Gyorshivatkozások** területén kattintson **irányítópultok fürt** megnyitásához a **irányítópultok fürt** nézet.  Ha nem lát **Gyorshivatkozások**, kattintson a **áttekintése** a panel bal oldali menüjében.

    ![Fürt irányítópultja a Spark](./media/apache-spark-connect-to-sql-database/hdinsight-cluster-dashboard-on-spark.png "Spark a fürt-irányítópult") 

3. Kattintson a **Jupyter Notebook**. Ha a rendszer felkéri rá, adja meg a fürthöz tartozó rendszergazdai hitelesítő adatokat.

    ![A Spark Jupyter notebook](./media/apache-spark-connect-to-sql-database/hdinsight-jupyter-notebook-on-spark.png "Spark a Jupyter notebook")
   
   > [!NOTE]
   > A Jupyter notebook Spark-fürtön a böngészőben nyissa meg a következő URL-címet is elérhető. Cserélje le a **CLUSTERNAME** elemet a fürt nevére:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   > 
   > 

4. Kattintson a jobb felső sarokban a Jupyter notebook **új**, és kattintson a **Spark** Scala jegyzetfüzet létrehozásához. A HDInsight Spark-fürt Jupyter notebookok is megadhatja a **PySpark** kernel Python2 alkalmazásokhoz, és a **PySpark3** kernel Python3 alkalmazások. Ez a cikk azt egy Scala notebook létrehozása.
   
    ![Jupyter notebookokhoz a Spark mag](./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png "Jupyter notebookokhoz a Spark mag")

    A kernelekkel kapcsolatos további információkért lásd: [Jupyter notebookkernelek használata Apache Spark-fürtökkel a HDInsightban](apache-spark-jupyter-notebook-kernels.md).

   > [!NOTE]
   > Ebben a cikkben használjuk a Spark (Scala) kernel, mert az SQL-adatbázisba származó Spark streamelési adatok csak akkor támogatott a Scala és Java jelenleg. Annak ellenére, hogy az olvasott és írt SQL teheti a Python, az ebben a cikkben konzisztenciáját, azt használatával Scala mindhárom műveletben.
   >

5. Alapértelmezett néven, megnyílik egy új notebook **névtelen**. Kattintson a notebook neve, és adjon meg egy nevet az Ön által választott.

    ![Adjon nevet a notebooknak](./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png "Adjon nevet a notebooknak")

Most elindíthatja az alkalmazás létrehozása.
    
## <a name="read-data-from-azure-sql-database"></a>Az Azure SQL adatbázis-adatok olvasása

Ebben a szakaszban adatokat olvasni a tábla (például **SalesLT.Address**), amely az AdventureWorks adatbázisban van.

1. Új Jupyter notebook kód cellába illessze be a következő kódrészletet, és cserélje le a helyőrző értékeket az Azure SQL-adatbázis értékeit.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    A kódcella futtatásához nyomja le a **SHIFT + ENTER** billentyűparancsot.  

2. Az alábbi kódrészletben összeállít egy JDBC URL-címet, amely képes továbbadni a Spark dataframe API-kat hoz létre egy `Properties` ahhoz, hogy a paraméterek objektum. Illessze be a kódrészletet a kód cellába, majd nyomja le az **SHIFT + ENTER** futtatásához.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")         

3. Az alábbi részlet egy dataframe az adatokat az Azure SQL adatbázis egyik táblája hoz létre. Ezt a kódrészletet használjuk a **SalesLT.Address** táblázatot, amely elérhető egy részének a **AdventureWorksLT** adatbázis. Illessze be a kódrészletet a kód cellába, majd nyomja le az **SHIFT + ENTER** futtatásához.

       val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)

4. Most már a dataframe, például a Adatséma első műveleteket végezheti el:

       sqlTableDF.printSchema
   
    A kimenet az alábbihoz hasonló jelenik meg:

    ![Adjon nevet a notebooknak](./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png "Adjon nevet a notebooknak")

5. Is műveleteket hajthat végre:, kérje le a legelső 10 sor.

       sqlTableDF.show(10)

6. Vagy az adatkészlet egyes oszlopok le.

       sqlTableDF.select("AddressLine1", "City").show(10)

## <a name="write-data-into-azure-sql-database"></a>Adatokat írni az Azure SQL-adatbázis

Ebben a szakaszban használatával egy CSV-mintafájlt érhető el a fürt létrehoz egy táblát az Azure SQL-adatbázist, és feltöltheti olyan adatokat. A CSV-mintafájlt (**HVAC.csv**) érhető el, az összes HDInsight-fürtök `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv`.

1. Új Jupyter notebook kód cellába illessze be a következő kódrészletet, és cserélje le a helyőrző értékeket az Azure SQL-adatbázis értékeit.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    A kódcella futtatásához nyomja le a **SHIFT + ENTER** billentyűparancsot.  

2. Az alábbi kódrészletben összeállít egy JDBC URL-címet, amely képes továbbadni a Spark dataframe API-kat hoz létre egy `Properties` ahhoz, hogy a paraméterek objektum. Illessze be a kódrészletet a kód cellába, majd nyomja le az **SHIFT + ENTER** futtatásához.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")

3. Az alábbi kódrészletben kibontja a séma HVAC.csv adatainak és a séma segítségével az adatok betöltése az egy dataframe, a fürt megosztott kötetei szolgáltatás `readDf`. Illessze be a kódrészletet a kód cellába, majd nyomja le az **SHIFT + ENTER** futtatásához.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

4. Használja a `readDf` egy ideiglenes tábla létrehozásához dataframe `temphvactable`. Az ideiglenes tábla használatával hozzon létre egy hive táblát `hvactable_hive`.

       readDf.createOrReplaceTempView("temphvactable")
       spark.sql("create table hvactable_hive as select * from temphvactable")

5. Végezetül táblázattal a hive tábla létrehozása az Azure SQL-adatbázis. A következő kódrészletet hoz létre `hvactable` az Azure SQL-adatbázis.

       spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)

6. Az Azure SQL adatbázisban az SSMS használatával csatlakozhat, és győződjön meg arról, hogy megjelenik egy `dbo.hvactable` van.

    a. Indítsa el a szolgáltatáshoz az SSMS, és csatlakozzon az Azure SQL-adatbázis, az alábbi képernyőfelvételen látható módon adja meg a kapcsolat adatai.

    ![Csatlakozás az SQL database szolgáltatáshoz az SSMS használatával](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png "Csatlakozás SQL Database szolgáltatáshoz az SSMS használatával")

    b. A az Object Explorer ablaktáblában bontsa ki az az Azure SQL-adatbázis és a tábla csomópont megjelenítéséhez a **dbo.hvactable** létrehozni.

    ![Csatlakozás az SQL database szolgáltatáshoz az SSMS használatával](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png "Csatlakozás SQL Database szolgáltatáshoz az SSMS használatával")

## <a name="stream-data-into-azure-sql-database"></a>Az adatfolyam adatok az Azure SQL-adatbázisba

Ez a szakasz azt adatok folyamatos átviteléhez azokat a **hvactable** már létrehozott Azure SQL-adatbázis az előző szakaszban.

1. Első lépésként, ellenőrizze, hogy nincsenek a rekordok a **hvactable**. A következő lekérdezés SSMS használatával, futtassa a táblában.

       DELETE FROM [dbo].[hvactable]

2. Új Jupyter notebook létrehozása a HDInsight Spark-fürtön. A kód a cella illessze be a következő kódrészletet, és nyomja le az **SHIFT + ENTER**:

       import org.apache.spark.sql._
       import org.apache.spark.sql.types._
       import org.apache.spark.sql.functions._
       import org.apache.spark.sql.streaming._
       import java.sql.{Connection,DriverManager,ResultSet}

3. Azt az adatok folyamatos átviteléhez a **HVAC.csv** a hvactable be. HVAC.csv fájl érhető el a fürthöz */HdiSamples/HdiSamples/SensorSampleData/HVAC/*. A következő kódrészletet a azt először beolvasni a közzétett adatok a sémát. Ezután azt hozzon létre egy adatfolyam-továbbítási dataframe adott sémáját használja. Illessze be a kódrészletet a kód cellába, majd nyomja le az **SHIFT + ENTER** futtatásához.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readStreamDf = spark.readStream.schema(userSchema1).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
       readStreamDf.printSchema

4. A kimenet látható sémája **HVAC.csv**. A **hvactable** , valamint az ugyanazon séma van. A kimenet a tábla oszlopai sorolja fel.

    ![Tábla sémája](./media/apache-spark-connect-to-sql-database/schema-of-table.png "tábla sémája")

5. Végezetül a következő kódrészletet használja adatokat olvasni az HVAC.csv és adatfolyamként küldje be azt a **hvactable** az Azure SQL-adatbázis. Illessze be a kódrészletet a kód cellában, cserélje le a helyőrző értékeket az Azure SQL-adatbázis értékeinek, és nyomja le az **SHIFT + ENTER** futtatásához.

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

6. Győződjön meg arról, hogy az adatok adatfolyamként történő a **hvactable** az SQL Server Management Studio (SSMS) a következő lekérdezés futtatásával. A lekérdezés futtatása Everytime, azt a sorok számát jeleníti meg a tábla növekvő.

        SELECT COUNT(*) FROM hvactable

## <a name="next-steps"></a>További lépések

* [A Data Lake Store adatok elemzése a HDInsight Spark-fürt használatával](apache-spark-use-with-data-lake-store.md)
* [Folyamat strukturált esemény streamelését EventHub használatával](apache-spark-eventhub-structured-streaming.md)
* [Válassza a Spark strukturált hdinsight Kafka Stream továbbítása](../hdinsight-apache-kafka-spark-structured-streaming.md)
