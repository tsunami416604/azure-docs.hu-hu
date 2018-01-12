---
title: "Apache Hive használja, mint az ETL eszköz – az Azure HDInsight |} Microsoft Docs"
description: "Használja a Apache Hive kinyerési, átalakítási és betöltési (ETL) adatokat az Azure HDInsight."
services: hdinsight
documentationcenter: 
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: 1ccbfe23e9c887a98a0dbfa8031078a15c6e41b6
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/12/2018
---
# <a name="use-apache-hive-as-an-extract-transform-and-load-etl-tool"></a>Apache Hive használata a kinyerési, átalakítási és betöltési (ETL) eszköz

Általában kell tisztítani és a bejövő adatok átalakítása előtt a megfelelő-e elemzés célt betöltése során. Kinyerési, átalakítási és betölteni (ETL) művelet készítse elő az adatokat, és töltse be adatok célt szolgálnak.  A HDInsight Hive strukturálatlan adatok olvasása, feldolgozni az adatokat szükség szerint, és majd adatok betöltése az egy relációs adatraktár döntési támogatási rendszerekhez. Ezt a módszert használja, az adatok a forrás kinyert és méretezhető tárolókhoz, például az Azure Storage blobs szolgáltatásban vagy az Azure Data Lake Store tárolja. Az adatok majd alakította Hive-lekérdezések sorozatát használatával, és végül előkészített belül struktúra betöltése a cél adattárba tömeges előkészítésekor.

## <a name="use-case-and-model-overview"></a>Használja a kis- és modell áttekintése

Az alábbi ábra a használati eset és az ETL automation típusú áttekintését tartalmazza. Bemeneti adat átalakítására kerül a megfelelő kimenet előállításához.  Átalakítás során az adatok alakzat, adattípus és még akkor is, nyelvi módosíthatja.  ETL-folyamatokkal birodalmi átalakítása metrika, időzónák módosítható, és megfelelően igazodnak a meglévő adatok a cél a pontosság javítása.  ETL-folyamatokkal kombinálhatja is naprakész állapotban tartása reporting, illetve további nyújt betekintést az adatokba meglévő meglévő adatok új adatokat.  Alkalmazások, mint az eszközök és szolgáltatások reporting képes felhasználni ezeket az adatokat a kívánt formátumban.

![Apache Hive ETL](./media/apache-hadoop-using-apache-hive-as-an-etl-tool/hdinsight-etl-architecture.png)

Hadoop importálása vagy egy nagy száma szövegfájlok (például a CSV-k) vagy egy kisebb, de gyakran a szövegfájlok, vagy mindkettőt számának módosítása ETL-folyamatok általában használatos.  Hive egy remek eszköz, amellyel készítheti elő az adatok előtt a adatok célhelyre történő betöltése során.  Hive lehetővé teszi egy séma létrehozása a CSV-n keresztül, és egy SQL-szerű nyelv használatával készítése a MapReduce programok, amelyek az adatok kezeléséhez. 

A jellemzően előforduló lépéseket a Hive eszközzel ETL végrehajtásához a következők:

1. Adatok betöltése az Azure Data Lake Store- vagy Azure Blob Storage tárolóban.
2. (Az Azure SQL Database használata) metaadat-tároló-adatbázis létrehozása a Hive használata a sémák tárolása.
3. HDInsight-fürtök létrehozása, és csatlakozzon az adattárban.
4. Adja meg a séma alkalmazandó olvasási időpontban adatok a tárolóban:

    ```
    DROP TABLE IF EXISTS hvac;

    --create the hvac table on comma-separated sensor data stored in Azure Storage blobs
    
    CREATE EXTERNAL TABLE hvac(`date` STRING, time STRING, targettemp BIGINT,
        actualtemp BIGINT, 
        system BIGINT, 
        systemage BIGINT, 
        buildingid BIGINT)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' 
    STORED AS TEXTFILE LOCATION 'wasb://{container}@{storageaccount}.blob.core.windows.net/HdiSamples/SensorSampleData/hvac/';
    ```

5. Átalakíthatja az adatokat, és töltse be a cél.  Többféleképpen is lehet a Hive használata a átalakítás és betöltés során:

    * Lekérdezi és készítse elő az adatokat a Hive eszközzel, és az Azure Data Lake Store vagy az Azure blob-tároló CSV-ként mentse.  Majd eszköz segítségével például SQL Server Integration Services (SSIS) adott CSV-k megszerzésére és az adatok betöltése az például az SQL Server cél relációs adatbázis.
    * A lekérdezést közvetlenül az Excel vagy C# Hive ODBC-illesztőprogram használatával.
    * Használjon [Apache Sqoop](apache-hadoop-use-sqoop-mac-linux.md) az előkészített egyszerű CSV-fájlok olvasása és betöltve helyezheti relációs adatbázist.

## <a name="data-sources"></a>Adatforrások

Az adatforrások azok rendelhető hozzá meglévő adatok a tárolóban, például általában külső adatokat:

* Közösségi adatok, naplófájlok, érzékelőket és adatfájlok alkalmazásokat.
* Adatszolgáltató, például időjárási statisztika vagy szállító értékesítési számok nyert adatkészletek.
* Streamelési adatok rögzített szűrt és a megfelelő eszköz vagy a keretrendszer keresztül.

<!-- TODO: (see Collecting and loading data into HDInsight). -->

## <a name="output-targets"></a>Kimeneti célok

Kimeneti adatok körének cél, beleértve a Hive segítségével:

* Egy relációs adatbázisban, például az SQL Server vagy az Azure SQL Database.
* Adatraktár, például az Azure SQL Data warehouse-bA.
* Excel.
* Az Azure tábla és a blob storage.
* Alkalmazások vagy szolgáltatások adott formátumokba feldolgozandó adatokat igénylő, vagy, fájlok, amelyek adatszerkezet adott típusú tartalmaznak.
* Például egy JSON-dokumentum tároló <a href="https://azure.microsoft.com/services/cosmos-db/">CosmosDB</a>.

## <a name="considerations"></a>Megfontolandó szempontok

Az ETL-modell általában akkor használják, ha meg szeretné:

* Betöltése egy meglévő adatbázis vagy a rendszer az adatfolyam adatok vagy a nagy mennyiségű strukturálatlan és félig strukturált adatok külső forrásból.
* Tiszta, átalakítására, és az adatok érvényesítése előtt betöltése során, lehet, hogy egynél több átalakítása használatával továbbítja a fürt.
* Jelentések és képi megjelenítéseket, rendszeresen frissített létrehozása.  Például ha a jelentés készítése a nap folyamán túl sokáig tart, ütemezheti éjszaka futtatni a jelentést.  Azure-ütemező és a PowerShell segítségével automatikusan a Hive-lekérdezések futtatása.

Az adatok célja nem egy adatbázist, ha a fájl a megfelelő formátumú a lekérdezésben, például a fürt megosztott Kötetei is létrehozhat. Ez a fájl majd importálható az Excel vagy a Power bi-ban.

Ha az adatok több műveletek végrehajtása az ETL-folyamat részeként van szüksége, fontolja meg, hogyan kezelheti azokat. Ha a művelet egy külső program által vezérelt, helyett a megoldáson munkafolyamatként kell döntse el, hogy bizonyos műveleteket hajtható végre párhuzamosan, illetve annak észlelése, ha minden feladat befejeződik. Egy munkafolyamat mechanizmussal, például a Hadoop belül Oozie egyszerűbb, mint az egyéni programok és a külső parancsfájlok használatával műveletek sorozata vezénylését tett kísérlet lehet. Oozie kapcsolatos további információkért lásd: [munkafolyamat és a feladat vezénylési](https://msdn.microsoft.com/library/dn749829.aspx).

<!-- ## Next steps -->
<!-- * [ETL at scale](../hdinsight-etl-at-scale.md): Learn more about performing ETL at scale. -->
<!-- * [Operationalize Data Pipelines with Oozie](hdinsight-operationalize-data-pipeline.md): Learn how to build a data pipeline that uses Hive to summarize CSV flight delay data, stage the prepared data in Azure Storage blobs, and then use Sqoop to load the summarized data into Azure SQL Database. -->
<!-- * [ETL Deep Dive](../hdinsight-etl-deep-dive.md): Walk through an end-to-end ETL pipeline.  -->
