---
title: ETL eszköz – Azure HDInsight az Apache Hive használatával
description: Az Apache Hive használata kinyerési, átalakítási és betöltési (ETL) adatokat az Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
editor: jasonwhowell
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.openlocfilehash: 2bdfe35c7ce705966904487c3de6691e05c09098
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2018
ms.locfileid: "42055732"
---
# <a name="use-apache-hive-as-an-extract-transform-and-load-etl-tool"></a>Az Apache Hive használata kinyerési, átalakítási és betöltési (ETL) eszközként

Általában kell tisztítása és átalakítása a bejövő adatok egy cél megfelelő az analytics-be való betöltés előtt. A kinyerési, átalakítási és Load (ETL) operations előkészíti az adatokat, és töltse be az adatok cél szolgálnak.  A HDInsight Hive olvassa el a teljes strukturálatlan adatmennyiséget tárolni, az adatok feldolgozására, igény szerint, és majd betölteni az adatokat relációs adattárházba döntési támogatási rendszereken. Ebben a megközelítésben adatok ki kell olvasni a forrás- és méretezhető tárhelyre, mint az Azure Storage blobból vagy az Azure Data Lake Store tárolja. Az adatok ezután alakította sorozata, a Hive-lekérdezések használatával, és végül előkészített Hive belül a célként megadott adattárba való betöltésének tömeges előkészítésekor.

## <a name="use-case-and-model-overview"></a>Használja a kis- és a model áttekintése

Az alábbi ábra a használati és ETL-automatizálási modelljét áttekintése látható. A bemeneti adatok átalakítják a megfelelő kimenet előállítása.  Az átalakítás során az adatok formázása, adattípus és még akkor is, nyelvi módosíthatja.  ETL-folyamatok birodalmi átalakítása metrika, módosítsa az időzónát, és megfelelően igazodnak a meglévő adatokat a rendeltetési a pontosság javítása.  ETL-folyamatok a meglévő adatokat naprakészen tartsa reporting, vagy további meglévő adatok betekintést nyújt az új adatok is kombinálhatók.  Alkalmazások, például eszközök és szolgáltatások reporting tudják felhasználni ezeket az adatokat a kívánt formátumban.

![Az Apache Hive ETL](./media/apache-hadoop-using-apache-hive-as-an-etl-tool/hdinsight-etl-architecture.png)

Hadoop ETL-folyamatok importálni vagy nagy mennyiségben szöveges fájlok (például a CSV-k) vagy egy kisebb, de gyakran a szöveges fájlokat, vagy mindkettőt számának módosítása általában használatos.  Hive egy remek eszköz használatával az adatok előkészítése az adat célhelyét-be való betöltés előtt.  Hive lehetővé teszi, hogy hozzon létre egy sémát keresztül a fürt megosztott kötetei szolgáltatás és a egy SQL-szerű nyelv használatával létrehozhat egy MapReduce-programok, amelyek az adatok kezeléséhez. 

A jellemzően előforduló lépéseket a Hive használatával egy ETL végrehajtásához a következők:

1. Adatok betöltése az Azure Data Lake Store vagy Azure Blob Storage-bA.
2. A sémák tárolása Hive általi használatra (Azure SQL Database használatával) metaadat-Store adatbázist létrehozni.
3. Hozzon létre egy HDInsight-fürtöt, és csatlakozzon az adattárban.
4. Adja meg a alkalmazni olvasási időpontjában az adattárban lévő adatok séma:

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

5. Az adatok átalakításához, és töltse be a célra.  Többféleképpen is használható a Hive átalakítása és betöltése során:

    * Lekérdezés, és előkészíti az adatokat a Hive használatával, és az Azure Data Lake Store vagy Azure blob storage-bA egy CSV-fájlként mentheti.  Egy eszköz, például az SQL Server Integration Services (SSIS) segítségével beszerezni ezeket CSV-k, és az adatok betöltése az SQL Serverhez hasonló cél relációs adatbázis.
    * A lekérdezést közvetlenül a Excel vagy C# a Hive ODBC illesztőprogram segítségével.
    * Használat [Apache Sqoop](apache-hadoop-use-sqoop-mac-linux.md) , olvassa el a előkészített egybesimított CSV-fájlokat, és betöltheti azokat a cél relációs adatbázis.

## <a name="data-sources"></a>Adatforrások

Az adatforrások azok, amelyek például összekapcsolhatók az adattároló-ben meglévő adatok általában külső adatokat:

* Közösségi adatok, naplófájlok, érzékelők és az alkalmazásokat, amelyek az adatfájlokat létrehozni.
* -Adatszolgáltatókkal, például az időjárás statisztika vagy szállító eladási számok beszerezni adathalmazok.
* Streamelési adatok rögzített, szűrt, és a megfelelő eszköz vagy keretrendszer keresztül.

<!-- TODO: (see Collecting and loading data into HDInsight). -->

## <a name="output-targets"></a>Kimeneti célok

Hive segítségével számos különböző célokat, beleértve a kimeneti adatokat:

* Például az SQL Server vagy az Azure SQL Database egy relációs adatbázis.
* Egy adattárházat, például az Azure SQL Data warehouse-bA.
* Excel.
* Az Azure tábla- és blob storage.
* Alkalmazások vagy szolgáltatások, amelyek adott formátumokba feldolgozandó adatokat igényelnek, vagy, fájlok, amelyek tartalmazzák az adott típusú adatok struktúrája.
* Például egy JSON-dokumentum Store <a href="https://azure.microsoft.com/services/cosmos-db/">CosmosDB</a>.

## <a name="considerations"></a>Megfontolandó szempontok

Az ETL-modell általában használatos, ha meg szeretné:

* Betöltése egy meglévő adatbázis vagy a rendszer az adatok streamelése vagy részben strukturált vagy strukturálatlan adatokon nagy mennyiségű külső forrásból.
* Tiszta, átalakíthatja és adatok érvényesítése, mielőtt betöltené azokat a, akár egynél több átalakítási használatával továbbítja a fürt.
* Hozzon létre jelentéseket és vizualizációkat, amelyek rendszeresen frissülnek.  Például ha a jelentés készítése a nap folyamán túl sokáig tart, ütemezhet éjszaka futtatni a jelentést.  Azure Scheduler és a PowerShell segítségével automatikusan futtasson egy Hive-lekérdezést.

Ha a cél az adatok nem egy adatbázist, létrehozhat egy fájlt a lekérdezés, például a fürt megosztott kötetei szolgáltatás a megfelelő formátumban. Ez a fájl importálható az Excel vagy a Power bi-ban.

Ha hajthat végre különböző műveleteket az adatok az ETL-folyamat részeként van szüksége, fontolja meg, hogyan kezeli azokat. Ha a művelet egy külső program által vezérelt, helyett a megoldáson belül munkafolyamatként kell döntse el, hogy néhány művelet hajtható végre párhuzamosan, és annak észlelése, ha minden egyes feladat befejeződik. Egy munkafolyamat mechanizmust, például a Hadoop belül az Oozie használata könnyebb, mint a külső parancsfájlokkal vagy egyéni programok műveletek sorozata összehangolhatja próbál lehet. Az Oozie kapcsolatos további információkért lásd: [vezénylési munkafolyamat és a feladat](https://msdn.microsoft.com/library/dn749829.aspx).

## <a name="next-steps"></a>További lépések

* [ETL ipari méretekben](apache-hadoop-etl-at-scale.md)
* [Egy adatfolyamat üzembe helyezése](../hdinsight-operationalize-data-pipeline.md)

<!-- * [ETL Deep Dive](../hdinsight-etl-deep-dive.md) -->
