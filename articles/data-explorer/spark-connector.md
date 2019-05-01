---
title: Az Azure Data Explorer Apache Spark-összekötő használatával adatok áthelyezése az Azure Data Explorer és a Spark-fürtök között.
description: Ez a témakör bemutatja, hogyan adatok áthelyezése Azure adatkezelő és az Apache Spark-fürtök között.
author: orspod
ms.author: orspodek
ms.reviewer: michazag
ms.service: data-explorer
ms.topic: conceptual
ms.date: 4/29/2019
ms.openlocfilehash: 8cb1489a0663556f9dd9e6026a036df2468d656d
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928332"
---
# <a name="azure-data-explorer-connector-for-apache-spark-preview"></a>Az Azure Data Explorer (előzetes verzió) az Apache Spark-összekötő

[Az Apache Spark](https://spark.apache.org/) egy egységes elemzési motor az adatfeldolgozás nagy méretű. Az Azure Data Explorer egy gyors, teljes körűen felügyelt elemzési szolgáltatás nagy mennyiségű adat valós idejű elemzés céljából. 

A Spark for Azure adatkezelő összekötő adatforrás és az adatok áthelyezése Azure adatkezelő és a Spark-fürtök mind azok képességeinek használata között adatfogadó valósítja meg. Használja az adatkezelőt az Azure és az Apache Spark, adatvezérelt forgatókönyvek, például a machine learning (gépi tanulás), a kinyerési, átalakítási-betöltési (ETL) és a Log Analytics célzó gyors, skálázható alkalmazásokat hozhat létre. Írás az Adatkezelőbe az Azure batch- és a folyamatos átviteli mód végezhető.
Azure Data Explorer olvasásakor oszlop tárolókarbantartási és predikátum legördülő lista, ami csökkenti az átvitt adatok mennyisége Azure adatkezelővel kiszűrésével támogatja.

Az Azure Data Explorer Spark-összekötő egy [nyílt forráskódú projekt](https://github.com/Azure/azure-kusto-spark) futtatható bármely Spark-fürtöt.

> [!NOTE]
> Bár az alábbi példák némelyike hivatkozik egy [Azure Databricks](https://docs.azuredatabricks.net/) Spark-fürt az Azure Data Explorer Spark-összekötő nem teszi meg a Databricks vagy bármely más Spark terjesztési közvetlen függőségek.

## <a name="prerequisites"></a>Előfeltételek

* [Az Azure Data Explorer fürt és adatbázis létrehozása](/azure/data-explorer/create-cluster-database-portal) 
* Spark-fürt létrehozása
* Telepítse az Azure Data Explorer-összekötő könyvtár és szereplő könyvtárak [függőségek](https://github.com/Azure/azure-kusto-spark#dependencies) többek között az alábbiakat [Kusto Java SDK](/azure/kusto/api/java/kusto-java-client-library) könyvtárak:
    * [Kusto-adatok ügyfél](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-data)
    * [Kusto betöltési ügyfél](https://mvnrepository.com/artifact/com.microsoft.azure.kusto/kusto-ingest)
* Beépített kódtárak a [Spark 2.4, Scala 2.11-et](https://github.com/Azure/azure-kusto-spark/releases)

## <a name="how-to-build-the-spark-connector"></a>A Spark-összekötő létrehozása

A Spark-összekötő felépíthető [források](https://github.com/Azure/azure-kusto-spark) alább részletesen.

> [!NOTE]
> Ez a lépés nem kötelező. Előre elkészített tárak használatakor lépjen a [Spark-fürt beállítása](#spark-cluster-setup).

### <a name="build-prerequisites"></a>Előfeltételek létrehozása

* A Java 1.8-as SDK telepítve van
* [Maven 3.x](https://maven.apache.org/download.cgi) telepítve
* Az Apache Spark-verzió 2.4.0 vagy újabb

> [!TIP]
> 2.3.x verziók is támogatottak, de szükség lehet néhány módosítást a pom.xml függőségek.

A használata a Maven-projektet definíciókat, Scala és Java-alkalmazások összekapcsolása az alkalmazás a következő összetevő (a legújabb verzió eltérhetnek):

```Maven
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>spark-kusto-connector</artifactId>
     <version>1.0.0-Beta-02</version>
   </dependency>
```

### <a name="build-commands"></a>Parancsok létrehozása

Jar hozhat létre, és az összes vizsgálat futtatása:

```
mvn clean package
```

Jar hozhat létre, az összes vizsgálat futtatása, és telepítse a jar a helyi Maven tárházra:

```
mvn clean install
```

További információkért lásd: [összekötő használati](https://github.com/Azure/azure-kusto-spark#usage).

## <a name="spark-cluster-setup"></a>Spark-fürt beállítása

> [!NOTE]
> Javasoljuk, hogy a legújabb Azure Data Explorer Spark-összekötő kiadást használja, ha a következő lépésekkel:

1. Állítsa be a következő Spark fürtbeállítások Spark 2.4 és Scala 2.11-et használó Azure Databricks-fürt alapján: 

    ![Databricks-fürt megadása](media/spark-connector/databricks-cluster.png)

1. Importálja az Azure Data Explorer-összekötő könyvtár:

    ![Importálás az Azure Data Explorer könyvtár](media/spark-connector/db-create-library.png)

1. Adjon hozzá további függőségeket:

    ![Függőségek hozzáadása](media/spark-connector/db-dependencies.png)

    > [!TIP]
    > A megfelelő java-verzió minden Spark kiadott található [Itt](https://github.com/Azure/azure-kusto-spark#dependencies).

1. Győződjön meg arról, hogy minden szükséges függvénytárak telepítve vannak:

    ![Ellenőrizze a telepített könyvtárak](media/spark-connector/db-libraries-view.png)

## <a name="authentication"></a>Hitelesítés

Az Azure Data Explorer Spark-összekötő lehetővé teszi, hogy az Azure Active Directory (Azure AD) használatával hitelesítést egy [Azure AD-alkalmazás](#azure-ad-application-authentication), [Azure AD hozzáférési jogkivonatot](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#direct-authentication-with-access-token), [eszközhitelesítés ](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#device-authentication) (a nem éles környezethez), vagy [Azure Key Vault](https://github.com/Azure/azure-kusto-spark/blob/dev/docs/Authentication.md#key-vault). A felhasználó kell telepítse az azure-keyvault-csomagot és alkalmazást hitelesítő adatok a Key Vault-erőforrás eléréséhez.

### <a name="azure-ad-application-authentication"></a>Az Azure AD alkalmazás-hitelesítés

A legtöbb egyszerű és a gyakori hitelesítési módszert. Ez a módszer az Azure Data Explorer Spark-összekötő használatának ajánlott.

|Tulajdonságok  |Leírás  |
|---------|---------|
|**KUSTO_AAD_CLIENT_ID**     |   Az Azure AD-alkalmazás (ügyfél) azonosítója.      |
|**KUSTO_AAD_AUTHORITY_ID**     |  Az Azure AD-hitelesítési szolgáltatót. Az Azure AD-címtár (bérlő) azonosítója.        |
|**KUSTO_AAD_CLIENT_PASSWORD**    |    Az ügyfél az Azure AD alkalmazás kulcsa.     |

### <a name="azure-data-explorer-privileges"></a>Az Azure Data Explorer jogosultsággal

Az Azure Data Explorer-fürtön a következő jogosultságokat kell biztosítani:

* (Adatforrás) olvasásához, rendelkeznie kell az Azure AD-alkalmazás *megjelenítő* jogosultságokkal azon a céladatbázis vagy *rendszergazdai* jogosultságokkal a céltábla.
* A (adatokat fogadó) írására, rendelkeznie kell az Azure AD-alkalmazás *módon eredményesen dolgozható* jogosultságokkal a céladatbázison. Is rendelkeznie kell *felhasználói* jogosultságokkal új táblák létrehozása a céladatbázison. Ha már létezik a céloldali tábla, *rendszergazdai* jogosultságokkal a céltábla konfigurálható.
 
Azure adatkezelő egyszerű szerepkörökkel kapcsolatos további információkért lásd: [szerepkör-alapú hitelesítést](/azure/kusto/management/access-control/role-based-authorization). Biztonsági szerepkörök kezeléséhhez lásd: [biztonsági szerepkörök kezelése](/azure/kusto/management/security-roles).

## <a name="spark-sink-writing-to-azure-data-explorer"></a>A Spark fogadó: Az Azure Data Explorer írása

1. Fogadóparaméterek beállítása:

     ```scala
    val KustoSparkTestAppId = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppId")
    val KustoSparkTestAppKey = dbutils.secrets.get(scope = "KustoDemos", key = "KustoSparkTestAppKey")
 
    val appId = KustoSparkTestAppId
    val appKey = KustoSparkTestAppKey
    val authorityId = "72f988bf-86f1-41af-91ab-2d7cd011db47"
    val cluster = "Sparktest.eastus2"
    val database = "TestDb"
    val table = "StringAndIntTable"
    ```

1. Spark DataFrame írni az Azure Data Explorer fürt Batch:

    ```scala
    df.write
      .format("com.microsoft.kusto.spark.datasource")
      .option(KustoOptions.KUSTO_CLUSTER, cluster)
      .option(KustoOptions.KUSTO_DATABASE, database)
      .option(KustoOptions.KUSTO_TABLE, table)
      .option(KustoOptions.KUSTO_AAD_CLIENT_ID, appId)
      .option(KustoOptions.KUSTO_AAD_CLIENT_PASSWORD, appKey) 
      .option(KustoOptions.KUSTO_AAD_AUTHORITY_ID, authorityId)
      .save()
    ```

1. Írási streamelési adatok:

    ```scala    
    import org.apache.spark.sql.streaming.Trigger
    import java.util.concurrent.TimeUnit
    
    // Set up a checkpoint and disable codeGen. Set up a checkpoint and disable codeGen as a workaround for an known issue 
    spark.conf.set("spark.sql.streaming.checkpointLocation", "/FileStore/temp/checkpoint")
    spark.conf.set("spark.sql.codegen.wholeStage","false")
    
    // Write to a Kusto table fro streaming source
    val kustoQ = csvDf
          .writeStream
          .format("com.microsoft.kusto.spark.datasink.KustoSinkProvider")
          .options(Map(
            KustoOptions.KUSTO_CLUSTER -> cluster,
            KustoOptions.KUSTO_TABLE -> table,
            KustoOptions.KUSTO_DATABASE -> database,
            KustoOptions.KUSTO_AAD_CLIENT_ID -> appId,
            KustoOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey,
            KustoOptions.KUSTO_AAD_AUTHORITY_ID -> authorityId))
          .trigger(Trigger.Once)
    
    kustoQ.start().awaitTermination(TimeUnit.MINUTES.toMillis(8))
    ```

## <a name="spark-source-reading-from-azure-data-explorer"></a>Külső forrás: Az Azure Data Explorer olvasásakor

1. Kis mennyiségű adatot olvasásakor adja meg a lekérdezés:

    ```scala
    val conf: Map[String, String] = Map(
          KustoOptions.KUSTO_AAD_CLIENT_ID -> appId,
          KustoOptions.KUSTO_AAD_CLIENT_PASSWORD -> appKey,
          KustoOptions.KUSTO_QUERY -> s"$table | where (ColB % 1000 == 0) | distinct ColA"      
        )
    
    // Simplified syntax flavor
    import org.apache.spark.sql._
    import com.microsoft.kusto.spark.sql.extension.SparkExtension._
    import org.apache.spark.SparkConf
    
    val df = spark.read.kusto(cluster, database, "", conf)
    display(df)
    ```

1. Nagy mennyiségű adat olvasásakor átmeneti blob storage-bA kell adni. Adja meg a storage tároló SAS-kulcsot, vagy a tárfiók neve, a fiókkulcsra és a tároló neve. 

    ```scala
    // Use either container/account-key/account name, or container SaS
    val container = dbutils.secrets.get(scope = "KustoDemos", key = "blobContainer")
    val storageAccountKey = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountKey")
    val storageAccountName = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageAccountName")
    // val storageSas = dbutils.secrets.get(scope = "KustoDemos", key = "blobStorageSasUrl")
    ```

    A fenti példában hogy nem érhető el a Key Vault a connector felhasználói felületének használatával. Másik lehetőségként használjuk egy egyszerűbb módszert a Databricks titkos kulcsok használatával.

1. Olvassa el az Azure Data Explorer:

    ```scala
    val df2 = spark.read.kusto(cluster, database, "ReallyBigTable", conf3)
    
    val dfFiltered = df2
      .where(df2.col("ColA").startsWith("row-2"))
      .filter("ColB > 12")
      .filter("ColB <= 21")
      .select("ColA")
    
    display(dfFiltered)
    ```
