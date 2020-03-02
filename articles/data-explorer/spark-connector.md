---
title: Az Azure Adatkezelő-összekötő használatával Apache Spark az Azure-Adatkezelő és a Spark-fürtök közötti adatáthelyezést.
description: Ebből a témakörből megtudhatja, hogyan helyezhet át adatáthelyezést az Azure Adatkezelő és Apache Spark-fürtök között.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 1/14/2020
ms.openlocfilehash: b358287664ac6d6a3b641e1ab63073810ceb4c40
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2020
ms.locfileid: "78208597"
---
# <a name="azure-data-explorer-connector-for-apache-spark"></a>Azure Adatkezelő-összekötő a Apache Sparkhoz

A [Apache Spark](https://spark.apache.org/) egy egységes analitikai motor a nagyméretű adatfeldolgozáshoz. Az Azure Adatkezelő egy gyors, teljes körűen felügyelt adatelemzési szolgáltatás, amely nagy mennyiségű adattal kapcsolatos valós idejű elemzést biztosít. 

A Sparkhoz készült Azure Adatkezelő Connector egy [nyílt forráskódú projekt](https://github.com/Azure/azure-kusto-spark) , amely bármely Spark-fürtön futtatható. Az adatforrást és az adatfogadót valósítja meg az Azure-Adatkezelő és a Spark-fürtökön tárolt adatáthelyezéshez. Az Azure Adatkezelő és Apache Spark használatával gyors és méretezhető alkalmazásokat hozhat létre, amelyek az adatvezérelt forgatókönyveket célozzák meg. Például a Machine learning (ML), a Extract-Transform-Load (ETL) és a Log Analytics. Az összekötővel az Azure Adatkezelő a standard Spark forrás és a fogadó művelet (például írás, olvasás és writeStream) esetében érvényes adattár lesz.

Az Azure Adatkezelő akár batch-, akár streaming módban is írhat. Az Azure Adatkezelő beolvasása támogatja az oszlopok metszését és a predikátum pushdown, amely az Azure-Adatkezelőban lévő adatok szűrésére, az átvitt adatok mennyiségének csökkentésére használható.

Ez a témakör ismerteti, hogyan telepítheti és konfigurálhatja az Azure Adatkezelő Spark-összekötőt, és hogyan helyezhet át az Azure Adatkezelő és a Apache Spark-fürtök között.

> [!NOTE]
> Bár az alábbi példák némelyike egy [Azure Databricks](https://docs.azuredatabricks.net/) Spark-fürtre vonatkozik, az Azure adatkezelő Spark-összekötő nem veszi figyelembe a közvetlen függőségeket a Databricks vagy más Spark-disztribúción.

## <a name="prerequisites"></a>Előfeltételek

* [Azure Adatkezelő-fürt és-adatbázis létrehozása](/azure/data-explorer/create-cluster-database-portal) 
* Spark-fürt létrehozása
* Az Azure Adatkezelő Connector könyvtárának telepítése:
    * Előre elkészített kódtárak a [Spark 2,4, a Scala 2,11](https://github.com/Azure/azure-kusto-spark/releases) 
    * [Maven-tárház](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector)
* [Maven 3. x](https://maven.apache.org/download.cgi) telepítve

> [!TIP]
> a 2.3. x verziók szintén támogatottak, de előfordulhat, hogy a Pom. XML függőségeiben módosításokat igényelnek.

## <a name="how-to-build-the-spark-connector"></a>A Spark-összekötő létrehozása

> [!NOTE]
> Ez a lépés nem kötelező. Ha előre elkészített kódtárakat használ, ugorjon a [Spark-fürt beállítása](#spark-cluster-setup)lehetőségre.

### <a name="build-prerequisites"></a>Előfeltételek létrehozása

1. Telepítse a [függőségekben](https://github.com/Azure/azure-kusto-spark#dependencies) felsorolt kódtárakat, például a következő [Kusto Java SDK](/azure/kusto/api/java/kusto-java-client-library) -kódtárakat:
    * [Kusto-alapú adatügyfél](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Kusto-ügyfél betöltése](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)

1. A Spark-összekötő létrehozásához tekintse meg [ezt a forrást](https://github.com/Azure/azure-kusto-spark) .

1. A Maven-projekt definícióit használó Scala/Java-alkalmazásokhoz az alábbi összetevővel kell összekapcsolni az alkalmazást (a legújabb verzió eltérő lehet):
    
    ```Maven
       <dependency>
         <groupId>com.microsoft.azure</groupId>
         <artifactId>spark-kusto-connector</artifactId>
         <version>1.1.0</version>
       </dependency>
    ```

### <a name="build-commands"></a>Összeállítási parancsok

A jar létrehozása és az összes teszt futtatása:

```
mvn clean package
```

A jar létrehozásához futtassa az összes tesztet, és telepítse a jar-t a helyi Maven-tárházba:

```
mvn clean install
```

További információ: [összekötő használata](https://github.com/Azure/azure-kusto-spark#usage).

## <a name="spark-cluster-setup"></a>Spark-fürt beállítása

> [!NOTE]
> A következő lépések végrehajtásakor ajánlott az Azure Adatkezelő Spark-összekötő legújabb kiadásának használata.

1. A következő Spark-fürt beállításainak konfigurálása a Spark 2.4.4 és a Scala 2,11 használatával Azure Databricks fürt alapján:

    ![Databricks-fürt beállításai](media/spark-connector/databricks-cluster.png)
    
1. Telepítse a legújabb Spark-kusto-Connector függvénytárat a Mavenből:
    
    ![importálási könyvtárakat](media/spark-connector/db-libraries-view.png) ![válassza a Spark-Kusto-Connector](media/spark-connector/db-dependencies.png)

1. Ellenőrizze, hogy telepítve van-e az összes szükséges könyvtár:

    ![A telepített könyvtárak ellenőrzése](media/spark-connector/db-libraries-view.png)

1. JAR-fájl használatával történő telepítéshez ellenőrizze, hogy a további függőségek telepítve vannak-e:

    ![Függőségek hozzáadása](media/spark-connector/db-not-maven.png)

## <a name="authentication"></a>Hitelesítés

Az Azure Adatkezelő Spark-összekötő lehetővé teszi a Azure Active Directory (Azure AD) hitelesítését az alábbi módszerek egyikének használatával:
* [Azure ad-alkalmazás](#azure-ad-application-authentication)
* [Azure ad hozzáférési jogkivonat](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token)
* [Eszköz hitelesítése](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (nem éles környezetekben)
* [Azure Key Vault](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault) az Key Vault erőforrás eléréséhez, telepítse az Azure-kulcstartó csomagot, és adja meg az alkalmazás hitelesítő adatait.

### <a name="azure-ad-application-authentication"></a>Azure AD-alkalmazás hitelesítése

Az Azure AD-alkalmazás hitelesítése a legegyszerűbb és leggyakoribb hitelesítési módszer, és az Azure Adatkezelő Spark-összekötő esetében ajánlott.

|Tulajdonságok  |Leírás  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Azure AD-alkalmazás (ügyfél) azonosítója.      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Azure AD-hitelesítési szolgáltató. Azure AD-címtár (bérlő) azonosítója.        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Az ügyfél Azure AD-alkalmazásának kulcsa.     |

### <a name="azure-data-explorer-privileges"></a>Azure Adatkezelő-jogosultságok

Adja meg a következő jogosultságokat egy Azure Adatkezelő-fürtön:

* Az olvasáshoz (adatforráshoz) az Azure AD-identitásnak meg kell adnia a *megjelenítői* jogosultságokat a céladatbázis számára, vagy *rendszergazdai* jogosultságokkal kell rendelkeznie a célként megadott táblán.
* Írás (adatfogadó) esetén az Azure AD-identitásnak betöltési *jogosultsággal kell rendelkeznie* a céladatbázis számára. Emellett a céladatbázis *felhasználói* jogosultságokkal is rendelkeznie kell, hogy új táblákat hozzon létre. Ha a céltábla már létezik, *rendszergazdai* jogosultságokat kell konfigurálnia a cél táblán.
 
Az Azure Adatkezelő rendszerbiztonsági szerepkörrel kapcsolatos további információkért lásd: [szerepköralapú hitelesítés](/azure/kusto/management/access-control/role-based-authorization). A biztonsági szerepkörök kezelésével kapcsolatban lásd: [biztonsági szerepkörök kezelése](/azure/kusto/management/security-roles).

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Spark-fogadó: az Azure Adatkezelőba való írás

1. Fogadó paramétereinek beállítása:

     ```scala
    val KustoSparkTestAppId = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppId")
    val KustoSparkTestAppKey = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppKey")
 
    val appId = KustoSparkTestAppId
    val appKey = KustoSparkTestAppKey
    val authorityId = "72f988bf-86f1-41af-91ab-2d7cd011db47" // Optional - defaults to microsoft.com
    val cluster = "Sparktest.eastus2"
    val database = "TestDb"
    val table = "StringAndIntTable"
    ```

1. Spark-DataFrame írása az Azure Adatkezelő-fürtbe kötegként:

    ```scala
    import com.microsoft.kusto.spark.datasink.KustoSinkOptions
    import org.apache.spark.sql.{SaveMode, SparkSession}

    df.write
      .format("com.microsoft.kusto.spark.datasource")
      .option(KustoSinkOptions.KUSTO_CLUSTER, cluster)
      .option(KustoSinkOptions.KUSTO_DATABASE, database)
      .option(KustoSinkOptions.KUSTO_TABLE, "Demo3_spark")
      .option(KustoSinkOptions.KUSTO_AAD_CLIENT_ID, appId)
      .option(KustoSinkOptions.KUSTO_AAD_CLIENT_PASSWORD, appKey)
      .option(KustoSinkOptions.KUSTO_AAD_AUTHORITY_ID, authorityId)
      .option(KustoSinkOptions.KUSTO_TABLE_CREATE_OPTIONS, "CreateIfNotExist")
      .mode(SaveMode.Append)
      .save()  
    ```
    
   Vagy használja az egyszerűsített szintaxist:
   
    ```scala
         import com.microsoft.kusto.spark.datasink.SparkIngestionProperties
         import com.microsoft.kusto.spark.sql.extension.SparkExtension._
         
         val sparkIngestionProperties = Some(new SparkIngestionProperties()) // Optional, use None if not needed
         df.write.kusto(cluster, database, table, conf, sparkIngestionProperties)
    ```
   
1. Adatfolyamok írása:

    ```scala    
    import org.apache.spark.sql.streaming.Trigger
    import java.util.concurrent.TimeUnit
    import java.util.concurrent.TimeUnit
    import org.apache.spark.sql.streaming.Trigger

    // Set up a checkpoint and disable codeGen. 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
        
    // Write to a Kusto table from a streaming source
    val kustoQ = df
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(conf) 
          .option(KustoSinkOptions.KUSTO_WRITE_ENABLE_ASYNC, "true") // Optional, better for streaming, harder to handle errors
          .trigger(Trigger.ProcessingTime(TimeUnit.SECONDS.toMillis(10))) // Sync this with the ingestionBatching policy of the database
          .start()
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Spark-forrás: olvasás az Azure Adatkezelő

1. [Kis mennyiségű adat](/azure/kusto/concepts/querylimits)olvasásakor adja meg az adat-lekérdezést:

    ```scala
    import com.microsoft.kusto.spark.datasource.KustoSourceOptions
    import org.apache.spark.SparkConf
    import org.apache.spark.sql._
    import com.microsoft.azure.kusto.data.ClientRequestProperties

    val query = s"$table | where (ColB % 1000 == 0) | distinct ColA"
    val conf: Map[String, String] = Map(
          KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
        )

    val df = spark.read.format("com.microsoft.kusto.spark.datasource").
      options(conf).
      option(KustoSourceOptions.KUSTO_QUERY, query).
      option(KustoSourceOptions.KUSTO_DATABASE, database).
      option(KustoSourceOptions.KUSTO_CLUSTER, cluster).
      load()

    // Simplified syntax flavor
    import com.microsoft.kusto.spark.sql.extension.SparkExtension._
    
    val cpr: Option[ClientRequestProperties] = None // Optional
    val df2 = spark.read.kusto(cluster, database, query, conf, cpr)
    display(df2)
    ```

1. Nem kötelező: **Ha megadja** az átmeneti BLOB-tárolót (és nem az Azure-adatkezelő), a Blobok létrehozása a hívó feladata. Ez magában foglalja a tároló üzembe helyezését, a hozzáférési kulcsok elforgatását és az átmeneti összetevők törlését. 
    A KustoBlobStorageUtils modul segítő függvényeket tartalmaz a Blobok törlésére a fiók és a tároló koordinátái és a fiók hitelesítő adatai alapján, vagy egy teljes SAS URL-címet, amely írási, olvasási és listázási engedélyekkel rendelkezik. Ha a megfelelő RDD már nincs rá szükség, az egyes tranzakciók átmeneti blob-összetevőket tárolnak egy külön címtárban. Ez a könyvtár a Spark-illesztőprogram csomópontján jelentett olvasási tranzakciós információs naplók részeként van rögzítve.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    A fenti példában a Key Vault nem érhető el az összekötő felületén keresztül. a Databricks Secrets használata egyszerűbb módszert használ.

1. Olvasás az Azure Adatkezelőról.

    * Ha **Megadja** az átmeneti blob Storage-tárolót, olvassa el az Azure adatkezelő az alábbiak szerint:

        ```scala
         val conf3 = Map(
              KustoSourceOptions.KUSTO_AAD_CLIENT_ID -> appId,
              KustoSourceOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey
              KustoSourceOptions.KUSTO_BLOB_STORAGE_SAS_URL -> storageSas)
        val df2 = spark.read.kusto(cluster, database, "ReallyBigTable", conf3)
        
        val dfFiltered = df2
          .where(df2.col("ColA").startsWith("row-2"))
          .filter("ColB > 12")
          .filter("ColB <= 21")
          .select("ColA")
        
        display(dfFiltered)
        ```

    * Ha az **azure adatkezelő** biztosítja az átmeneti BLOB-tárolót, olvassa el az Azure adatkezelő az alábbi módon:
    
        ```scala
        val dfFiltered = df2
          .where(df2.col("ColA").startsWith("row-2"))
          .filter("ColB > 12")
          .filter("ColB <= 21")
          .select("ColA")
        
        display(dfFiltered)
        ```

## <a name="next-steps"></a>További lépések

* További információ az [Azure adatkezelő Spark-összekötőről](https://github.com/Azure/azure-kusto-spark/tree/master/docs)
* [Mintakód a Javához és a Pythonhoz](https://github.com/Azure/azure-kusto-spark/tree/master/samples/src/main)
