---
title: Az Azure Adatkezelő-összekötő használatával Apache Spark az Azure-Adatkezelő és a Spark-fürtök közötti adatáthelyezést.
description: Ebből a témakörből megtudhatja, hogyan helyezhet át adatáthelyezést az Azure Adatkezelő és Apache Spark-fürtök között.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 4/29/2019
ms.openlocfilehash: 0fe81926327bcccac56718cc0d06e336e1af17fe
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70165091"
---
# <a name="azure-data-explorer-connector-for-apache-spark-preview"></a>Azure Adatkezelő-összekötő a Apache Sparkhoz (előzetes verzió)

A [Apache Spark](https://spark.apache.org/) egy egységes analitikai motor a nagyméretű adatfeldolgozáshoz. Az Azure Adatkezelő egy gyors, teljes körűen felügyelt adatelemzési szolgáltatás, amely nagy mennyiségű adattal kapcsolatos valós idejű elemzést biztosít. 

Az Azure Adatkezelő-összekötő a Sparkhoz olyan adatforrást és adatfogadót valósít meg, amely az Azure Adatkezelő és a Spark-fürtök közötti adatáthelyezést is lehetővé tenné. Az Azure Adatkezelő és Apache Spark használatával gyors és méretezhető alkalmazásokat hozhat létre az adatvezérelt forgatókönyvek, például a gépi tanulás (ML), a kinyerés átalakítása-Load (ETL) és a Log Analytics számára. Az Azure Adatkezelőba való írás kötegelt és streaming módban is elvégezhető.
Az Azure Adatkezelő-ból való olvasás támogatja az oszlopok metszését és a predikátum pushdown, ami csökkenti az átvitt adatok mennyiségét az Azure-Adatkezelőban lévő adatok kiszűrésével.

Az Azure Adatkezelő Spark-összekötő egy [nyílt forráskódú projekt](https://github.com/Azure/azure-kusto-spark) , amely bármely Spark-fürtön futtatható.

> [!NOTE]
> Bár az alábbi példák némelyike egy [Azure Databricks](https://docs.azuredatabricks.net/) Spark-fürtre vonatkozik, az Azure adatkezelő Spark-összekötő nem veszi figyelembe a közvetlen függőségeket a Databricks vagy más Spark-disztribúción.

## <a name="prerequisites"></a>Előfeltételek

* [Azure Adatkezelő-fürt és-adatbázis létrehozása](/azure/data-explorer/create-cluster-database-portal) 
* Spark-fürt létrehozása
* Telepítse az Azure Adatkezelő Connector kódtárat és a [függőségekben](https://github.com/Azure/azure-kusto-spark#dependencies) felsorolt könyvtárakat, beleértve a következő [Kusto Java SDK](/azure/kusto/api/java/kusto-java-client-library) -kódtárakat:
    * [Kusto-alapú adatügyfél](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Kusto-ügyfél betöltése](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)
* Előre elkészített kódtárak a [Spark 2,4, a Scala 2,11](https://github.com/Azure/azure-kusto-spark/releases)

## <a name="how-to-build-the-spark-connector"></a>A Spark-összekötő létrehozása

A Spark-összekötő az alábbiakban [](https://github.com/Azure/azure-kusto-spark) részletezett forrásokból is kiépíthető.

> [!NOTE]
> Ez a lépés nem kötelező. Ha előre elkészített kódtárakat használ, ugorjon a [Spark-fürt beállítása](#spark-cluster-setup)lehetőségre.

### <a name="build-prerequisites"></a>Előfeltételek létrehozása

* Java 1,8 SDK telepítve
* [Maven 3. x](https://maven.apache.org/download.cgi) telepítve
* Apache Spark 2.4.0 vagy újabb verzió

> [!TIP]
> a 2.3. x verziók szintén támogatottak, de előfordulhat, hogy a Pom. XML függőségeiben módosításokat igényelnek.

A Maven-projekt definícióit használó Scala/Java-alkalmazásokhoz az alábbi összetevővel kell összekapcsolni az alkalmazást (a legújabb verzió eltérő lehet):

```Maven
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>spark-kusto-connector</artifactId>
     <version>1.0.0-Beta-02</version>
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
> A következő lépések végrehajtásakor ajánlott a legújabb Azure Adatkezelő Spark-összekötő kiadásának használata:

1. Állítsa be a következő Spark-fürt beállításait Azure Databricks fürt alapján, a Spark 2,4 és a Scala 2,11 használatával: 

    ![Databricks-fürt beállításai](media/spark-connector/databricks-cluster.png)

1. Az Azure Adatkezelő Connector-függvénytár importálása:

    ![Azure Adatkezelő-függvénytár importálása](media/spark-connector/db-create-library.png)

1. További függőségek hozzáadása (a Maven használata esetén nem szükséges):

    ![Függőségek hozzáadása](media/spark-connector/db-dependencies.png)

    > [!TIP]
    > Az egyes Spark-kiadásokhoz tartozó Java-kiadás megfelelő verziója [itt](https://github.com/Azure/azure-kusto-spark#dependencies)található.

1. Ellenőrizze, hogy telepítve van-e az összes szükséges könyvtár:

    ![A telepített könyvtárak ellenőrzése](media/spark-connector/db-libraries-view.png)

## <a name="authentication"></a>Authentication

Az Azure Adatkezelő Spark Connector lehetővé teszi, hogy az Azure ad-alkalmazással, az [](#azure-ad-application-authentication) [Azure ad hozzáférési](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token)jogkivonatával, az [eszköz](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) -hitelesítéssel (nem éles környezetekben) vagy az [Azure-kulccsal hitelesítse Azure Active Directory (Azure ad-val). ](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault)Tároló. A felhasználónak telepítenie kell az Azure-kulcstartó csomagot, és meg kell adnia az alkalmazás hitelesítő adatait a Key Vault erőforrás eléréséhez.

### <a name="azure-ad-application-authentication"></a>Azure AD-alkalmazás hitelesítése

A legegyszerűbb és gyakori hitelesítési módszer. Ez a módszer az Azure Adatkezelő Spark-összekötő használata esetén ajánlott.

|properties  |Leírás  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Azure AD-alkalmazás (ügyfél) azonosítója.      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Azure AD-hitelesítési szolgáltató. Azure AD-címtár (bérlő) azonosítója.        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Az ügyfél Azure AD-alkalmazásának kulcsa.     |

### <a name="azure-data-explorer-privileges"></a>Azure Adatkezelő-jogosultságok

Az Azure Adatkezelő-fürtön a következő jogosultságokat kell megadni:

* Az olvasáshoz (adatforráshoz) az Azure AD- alkalmazásnak meg kell adni a megjelenítői jogosultságokat a céladatbázis számára, vagy *rendszergazdai* jogosultságokat kell megadnia a cél táblában.
* Íráshoz (adatfogadó) az Azure AD-alkalmazásnak betöltési jogosultságokkal kell rendelkeznie a céladatbázis számára. Emellett a céladatbázis *felhasználói* jogosultságokkal is rendelkeznie kell, hogy új táblákat hozzon létre. Ha a céltábla már létezik, akkor konfigurálható a *rendszergazdai* jogosultságok a céltábla számára.
 
Az Azure Adatkezelő rendszerbiztonsági szerepkörrel kapcsolatos további információkért lásd: [szerepköralapú hitelesítés](/azure/kusto/management/access-control/role-based-authorization). A biztonsági szerepkörök kezelésével kapcsolatban lásd: [biztonsági szerepkörök kezelése](/azure/kusto/management/security-roles).

## <a name="spark-sink-writing-to-azure-data-explorer"></a>Spark-fogadó: Írás az Azure Adatkezelőba

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
    val conf = Map(
            KustoSinkOptions.KUSTO_CLUSTER -> cluster,
            KustoSinkOptions.KUSTO_TABLE -> table,
            KustoSinkOptions.KUSTO_DATABASE -> database,
            KustoSinkOptions.KUSTO_AAD_CLIENT_ID -> appId,
            KustoSinkOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey,
            KustoSinkOptions.KUSTO_AAD_AUTHORITY_ID -> authorityId)
    
    df.write
      .format("com.microsoft.kusto.spark.datasource")
      .options(conf)
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

    // Set up a checkpoint and disable codeGen. Set up a checkpoint and disable codeGen as a workaround for an known issue 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
    spark.conf.set("spark.sql.codegen.wholeStage","false") // Use in case a NullPointerException is thrown inside codegen iterator
    
    // Write to a Kusto table from a streaming source
    val kustoQ = df
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(conf) 
          .option(KustoSinkOptions.KUSTO_WRITE_ENABLE_ASYNC, "true") // Optional, better for streaming, harder to handle errors
          .trigger(Trigger.ProcessingTime(TimeUnit.SECONDS.toMillis(10))) // Sync this with the ingestionBatching policy of the database
          .start()
    
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Spark-forrás: Olvasás az Azure Adatkezelő

1. Kis mennyiségű adat olvasásakor adja meg az adat-lekérdezést:

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

1. Nagy mennyiségű információ olvasásakor meg kell adni az átmeneti blob Storage-t. Adja meg a Storage-tároló SAS-kulcsát vagy a Storage-fiók nevét, a fiók kulcsát és a tároló nevét. Ez a lépés csak a Spark-összekötő aktuális előzetes kiadásához szükséges.

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    A fenti példában nem fér hozzá a Key Vault az összekötő felületén keresztül. Azt is megteheti, hogy a Databricks Secrets használatával egyszerűbb módszert használ.

1. Olvasás az Azure Adatkezelőról:

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
