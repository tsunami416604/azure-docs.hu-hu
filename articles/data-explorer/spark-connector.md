---
title: Az Azure Data Explorer-összekötő az Apache Spark hoz adatok áthelyezése az Azure Data Explorer és a Spark-fürtök között.
description: Ez a témakör bemutatja, hogyan helyezheti át az adatokat az Azure Data Explorer és az Apache Spark-fürtök között.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 1/14/2020
ms.openlocfilehash: b358287664ac6d6a3b641e1ab63073810ceb4c40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208597"
---
# <a name="azure-data-explorer-connector-for-apache-spark"></a>Azure Data Explorer-összekötő az Apache Sparkhoz

[Az Apache Spark](https://spark.apache.org/) egy egységes elemzési motor a nagyméretű adatfeldolgozáshoz. Az Azure Data Explorer egy gyors, teljes körűen felügyelt adatelemzési szolgáltatás, amely nagy mennyiségű adat valós idejű elemzéséhez szolgál. 

Az Azure Data Explorer-összekötő a Spark egy [nyílt forráskódú projekt,](https://github.com/Azure/azure-kusto-spark) amely bármely Spark-fürtön futtatható. Adatforrás- és adatgyűjtőt valósít meg az Azure Data Explorer és a Spark-fürtök közötti adatok áthelyezéséhez. Az Azure Data Explorer és az Apache Spark használatával gyors és méretezhető alkalmazásokat hozhat létre, amelyek adatvezérelt forgatókönyveket céloznak meg. Például a gépi tanulás (ML), Extract-Transform-Load (ETL) és a Log Analytics. Az összekötővel az Azure Data Explorer a szabványos Spark-forrás- és fogadóműveletek, például az írási, olvasási és writestream-műveletek érvényes adattárává válik.

Írhat az Azure Data Explorer vagy kötegelt vagy streamelési módban. Az Azure Data Explorer ből történő olvasás támogatja az oszlopmetszést és az alapleküldéses leküldéses műveleteket, amelyek szűrik az adatokat az Azure Data Explorerben, csökkentve az átvitt adatok mennyiségét.

Ez a témakör ismerteti, hogyan telepítheti és konfigurálhatja az Azure Data Explorer Spark-összekötőt, és hogyan helyezheti át az adatokat az Azure Data Explorer és az Apache Spark-fürtök között.

> [!NOTE]
> Bár az alábbi példák egy [Azure Databricks](https://docs.azuredatabricks.net/) Spark-fürtre hivatkoznak, az Azure Data Explorer Spark-összekötő nem vesz közvetlen függőséget databricks vagy bármely más Spark-disztribúció.

## <a name="prerequisites"></a>Előfeltételek

* [Azure Data Explorer-fürt és-adatbázis létrehozása](/azure/data-explorer/create-cluster-database-portal) 
* Spark-fürt létrehozása
* Az Azure Data Explorer összekötőkönyvtárának telepítése:
    * Előre elkészített könyvtárak a [Spark 2.4, Scala 2.11](https://github.com/Azure/azure-kusto-spark/releases) számára 
    * [Maven-repo](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/spark-kusto-connector)
* [Maven 3.x](https://maven.apache.org/download.cgi) telepítve

> [!TIP]
> A 2.3.x verziók is támogatottak, de szükség lehet a pom.xml függőségek módosítására.

## <a name="how-to-build-the-spark-connector"></a>A Spark-összekötő létrehozása

> [!NOTE]
> Ez a lépés nem kötelező. Ha előre elkészített könyvtárakat használ, nyissa meg a [Spark-fürt telepítőjét.](#spark-cluster-setup)

### <a name="build-prerequisites"></a>Előfeltételek összeállítása

1. Telepítse a függőségekben felsorolt [könyvtárakat,](https://github.com/Azure/azure-kusto-spark#dependencies) beleértve a következő [Kusto Java SDK-tárakat:](/azure/kusto/api/java/kusto-java-client-library)
    * [Kusto adatügyfél](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Kusto Betöltés ügyfél](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)

1. Tekintse meg ezt a [forrást](https://github.com/Azure/azure-kusto-spark) a Spark-összekötő létrehozásához.

1. A Maven projektdefiníciókat használó Scala/Java alkalmazások esetében kapcsolja össze az alkalmazást a következő műtermékkel (a legújabb verzió eltérő lehet):
    
    ```Maven
       <dependency>
         <groupId>com.microsoft.azure</groupId>
         <artifactId>spark-kusto-connector</artifactId>
         <version>1.1.0</version>
       </dependency>
    ```

### <a name="build-commands"></a>Build parancsok

Építeni jar és fuss minden vizsgálatot:

```
mvn clean package
```

Jar készítéséhez futtassa az összes tesztet, és telepítse a jar-t a helyi Maven tárházba:

```
mvn clean install
```

További információt az összekötő használata című témakörben [talál.](https://github.com/Azure/azure-kusto-spark#usage)

## <a name="spark-cluster-setup"></a>Spark-fürt beállítása

> [!NOTE]
> Javasoljuk, hogy a következő lépések végrehajtásakor használja a legújabb Azure Data Explorer Spark-összekötő kiadás.

1. Konfigurálja a következő Spark-fürtbeállításokat az Azure Databricks-fürt alapján a Spark 2.4.4-es és scala 2.11-es használatával:

    ![Databricks fürt beállításai](media/spark-connector/databricks-cluster.png)
    
1. Telepítse a legújabb Spark-kusto-csatlakozó könyvtárat a Maven-től:
    
    ![Könyvtárak](media/spark-connector/db-libraries-view.png) ![importálása A Spark-Kusto-Connector kiválasztása](media/spark-connector/db-dependencies.png)

1. Ellenőrizze, hogy az összes szükséges kódtár telepítve van-e:

    ![Telepített tárak ellenőrzése](media/spark-connector/db-libraries-view.png)

1. JAR-fájl használatával történő telepítés esetén ellenőrizze, hogy további függőségek vannak-e telepítve:

    ![Függőségek hozzáadása](media/spark-connector/db-not-maven.png)

## <a name="authentication"></a>Hitelesítés

Az Azure Data Explorer Spark-összekötő lehetővé teszi az Azure Active Directoryval (Azure AD) való hitelesítést az alábbi módszerek egyikével:
* [Egy Azure AD-alkalmazás](#azure-ad-application-authentication)
* [Egy Azure AD-hozzáférési jogkivonat](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token)
* [Eszközhitelesítés](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (nem éles környezetben)
* Egy [Azure Key Vault](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault) a Key Vault erőforrás eléréséhez telepítse az azure-keyvault csomagot, és adja meg az alkalmazás hitelesítő adatait.

### <a name="azure-ad-application-authentication"></a>Azure AD-alkalmazás hitelesítése

Az Azure AD-alkalmazás hitelesítése a legegyszerűbb és leggyakoribb hitelesítési módszer, és ajánlott az Azure Data Explorer Spark-összekötő.

|Tulajdonságok  |Leírás  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Azure AD alkalmazás (ügyfél) azonosító.      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Azure AD hitelesítési hatóság. Azure AD Könyvtár (bérlő) azonosító.        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Az Ügyfél Azure AD alkalmazáskulcsa.     |

### <a name="azure-data-explorer-privileges"></a>Az Azure Data Explorer jogosultságai

Adja meg a következő jogosultságokat egy Azure Data Explorer-fürtön:

* Olvasás (adatforrás) olvasásához az Azure AD-identitás nak rendelkeznie kell *a* céladatbázis vagy *rendszergazdai* jogosultságok a céltáblán.
* Írás (adatfogadó) az Azure AD-identitás kell *betöltési* jogosultságokat a céladatbázisban. Új táblák létrehozásához *felhasználói* jogosultságokkal is rendelkeznie kell a céladatbázisban. Ha a céltábla már létezik, *rendszergazdai* jogosultságokat kell konfigurálnia a céltáblán.
 
Az Azure Data Explorer főszerepköreiről a [szerepköralapú engedélyezés](/azure/kusto/management/access-control/role-based-authorization)című témakörben talál további információt. A biztonsági szerepkörök kezeléséről a [biztonsági szerepkörek kezelése](/azure/kusto/management/security-roles)című témakörben van.

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Spark-fogadó: írás az Azure Data Explorer be

1. A fogadó paramétereinek beállítása:

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

1. A Spark DataFrame írása az Azure Data Explorer-fürtbe kötegként:

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
   
1. Streamelési adatok írása:

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

## <a name="spark-source-reading-from-azure-data-explorer"></a>Spark-forrás: olvasás az Azure Data Explorerből

1. Kis [mennyiségű adat olvasásakor](/azure/kusto/concepts/querylimits)adja meg az adatlekérdezést:

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

1. Nem kötelező: **Ha** az átmeneti blob storage (és nem az Azure Data Explorer) a blobok jönnek létre a hívó felelőssége. Ez magában foglalja a tároló üzembe, a hozzáférési kulcsok elforgatását és az átmeneti összetevők törlését. 
    A KustoBlobStorageUtils modul súgófüggvényeket tartalmaz a blobok fiók- és tárolókoordináták és fiókhitelesítő adatok alapján történő törléséhez, vagy egy teljes SAS URL-címet írási, olvasási és listaengedélyekkel. Ha a megfelelő RDD-re már nincs szükség, minden tranzakció egy külön könyvtárban tárolja az átmeneti blob-összetevőket. Ez a könyvtár a Spark-illesztőprogram-csomóponton jelentett olvasási tranzakciós információs naplók részeként kerül rögzítésre.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    A fenti példában a Key Vault nem érhető el az összekötő felülethasználatával; a Databricks-titkok használatának egyszerűbb módszerét használják.

1. Olvasson az Azure Data Explorerből.

    * **Ha biztosítja** az átmeneti blobstorage- t, olvassa el az Azure Data Explorer ből az alábbiak szerint:

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

    * Ha az **Azure Data Explorer** biztosítja az átmeneti blob-tárhelyet, olvassa el az Azure Data Explorer ből az alábbiak szerint:
    
        ```scala
        val dfFiltered = df2
          .where(df2.col("ColA").startsWith("row-2"))
          .filter("ColB > 12")
          .filter("ColB <= 21")
          .select("ColA")
        
        display(dfFiltered)
        ```

## <a name="next-steps"></a>További lépések

* További információ az [Azure Data Explorer Spark Connector-ról](https://github.com/Azure/azure-kusto-spark/tree/master/docs)
* [Példakód Java és Python hoz](https://github.com/Azure/azure-kusto-spark/tree/master/samples/src/main)
