---
title: Apache Hive használata ETL-eszközként – Azure HDInsight
description: A Apache Hive használatával kinyerheti, átalakíthatja és betöltheti az ETL-adatok az Azure HDInsight-ben.
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.openlocfilehash: 71631cd2394efd6743bc0e80a458fed2678d4be0
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076248"
---
# <a name="use-apache-hive-as-an-extract-transform-and-load-etl-tool"></a>Apache Hive használata kinyerési, átalakítási és betöltési (ETL) eszközként

A beérkező adatbevitelt általában úgy kell megtisztítani és átalakítani, hogy az a megfelelő helyre kerüljön az elemzéshez. A kinyerési, átalakítási és betöltési (ETL) műveletek az adatok előkészítéséhez és egy adatcélhelybe való betöltéséhez használatosak.  A HDInsight Apache Hive a strukturálatlan adatban is olvashatók, szükség szerint feldolgozza az adatfeldolgozást, majd betölti az összes adatát egy kapcsolódó adattárházba a döntési támogatási rendszerek esetében. Ebben a megközelítésben az adatok kinyerése a forrásból történik, és méretezhető tárolóban (például Azure Storage-blobokban vagy Azure Data Lake Storage) tárolódik. A rendszer ezután átalakítja a kaptár-lekérdezések sorát, és végül a kaptáron belül, a cél adattárba való tömeges betöltés előkészítéséhez készíti el azokat.

## <a name="use-case-and-model-overview"></a>Használati eset és modell áttekintése

Az alábbi ábra az ETL Automation használati eseteit és modelljét mutatja be. A bemeneti adatok át lettek alakítva a megfelelő kimenet létrehozásához.  Az átalakítás során az adattípust, az adattípust és a nyelvet is módosíthatja.  Az ETL-folyamatok átalakítják az Imperial-t metrikára, módosíthatja az időzónákat, és javíthatják a pontosságot, hogy megfelelően illeszkedjenek a cél meglévő adataihoz.  Az ETL-folyamatok a meglévő adatokkal is kombinálhatók az új adatokkal, hogy naprakészen tartsák a jelentéskészítést, vagy hogy további információkat szolgáltassanak a meglévő adatokról.  Az olyan alkalmazások, mint például a Jelentéskészítő eszközök és szolgáltatások, a kívánt formátumban használhatják ezeket az eszközöket.

![Apache Hive ETL-architektúra](./media/apache-hadoop-using-apache-hive-as-an-etl-tool/hdinsight-etl-architecture.png)

A Hadoop jellemzően olyan ETL-folyamatokban használják, amelyek nagy mennyiségű szövegfájlt (például CSV) vagy kisebb, de gyakran változó számú szövegfájlt vagy mindkettőt importálnak.  A kaptár egy nagyszerű eszköz, amellyel előkészítheti az adatfeldolgozást, mielőtt betölti azt az adatcélhelybe.  A struktúra lehetővé teszi, hogy létrehoz egy sémát a CSV-fájlon keresztül, és SQL-szerű nyelven hozzon létre MapReduce-programokat, amelyek az adatkezeléssel működnek együtt. 

A Kaptárnak az ETL végrehajtásához való használatának tipikus lépései a következők:

1. Betöltheti az adatAzure Data Lake Storage vagy az Azure Blob Storage.
2. Hozzon létre egy metaadat-tároló adatbázist (a Azure SQL Database használatával) a sémák tárolásához a kaptár használatával.
3. Hozzon létre egy HDInsight-fürtöt, és kapcsolódjon az adattárhoz.
4. Adja meg az adattárban található adatolvasáskor alkalmazandó sémát:

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

5. Alakítsa át az adatszerkezetet, és töltse be a célhelyre.  A struktúra több módon is használható az átalakítás és a betöltés során:

    * Megkérdezheti és előkészítheti az adatstruktúrákat a kaptár használatával, és mentheti CSV-ként Azure Data Lake Storage vagy Azure Blob Storage-tárolóban.  Ezután használjon olyan eszközt, mint a SQL Server Integration Services (SSIS), hogy megszerezze ezeket a CSV, és betöltse az adattárolót egy célként szolgáló, például SQL Serverba.
    * Az adatok lekérdezése közvetlenül az Excelből vagy C# a kaptár ODBC-illesztő használatával.
    * Az [Apache Sqoop](apache-hadoop-use-sqoop-mac-linux.md) használatával olvassa el az előkészített CSV-fájlokat, és töltse be azokat a cél-viszonyítási adatbázisba.

## <a name="data-sources"></a>Adatforrások

Az adatforrások jellemzően olyan külső információk, amelyek az adattár meglévő adataihoz is illeszkednek, például:

* Az adatfájlokat létrehozó közösségi adathordozók, naplófájlok, érzékelők és alkalmazások.
* Adatszolgáltatókból beszerzett adatkészletek, például időjárási statisztikák vagy szállítói értékesítési számok.
* Egy megfelelő eszközön vagy keretrendszeren keresztül rögzített, szűrt és feldolgozott adatátviteli adatcsatorna.

<!-- TODO: (see Collecting and loading data into HDInsight). -->

## <a name="output-targets"></a>Kimeneti célok

A kaptár használatával több célra is elvégezheti az adatokat, többek között a következőket:

* Egy rokon adatbázis, például SQL Server vagy Azure SQL Database.
* Egy adattárház, például Azure SQL Data Warehouse.
* Excel.
* Azure Table és blob Storage.
* Azok az alkalmazások vagy szolgáltatások, amelyeknek az adatok meghatározott formátumokba való feldolgozására, vagy adott típusú adatszerkezetet tartalmazó fájlokra van szükségük.
* Egy JSON-dokumentum tárolója, például <a href="https://azure.microsoft.com/services/cosmos-db/">CosmosDB</a>.

## <a name="considerations"></a>Megfontolandó szempontok

Az ETL modellt általában a következő esetekben használja a rendszer:

* Egy meglévő adatbázisba vagy információs rendszerbe betöltheti a külső forrásokból származó, részben strukturált vagy strukturálatlan adatok adatfolyamait vagy nagy mennyiségű részét.
* Megtisztíthatja, átalakíthatja és érvényesítheti az adataikat a betöltés előtt, például a fürtön keresztül egynél több transzformációs folyamat használatával.
* Jelentéseket és vizualizációkat készíthet, amelyek rendszeresen frissülnek.  Ha például a jelentés túl hosszú időt vesz igénybe a nap folyamán, a jelentést a következő időpontban is futtathatja:.  Az Azure Scheduler és a PowerShell használatával automatikusan futtathat egy kaptár-lekérdezést.

Ha az adatelérési cél nem adatbázis, akkor a lekérdezésen belül, például egy CSV-fájlban is létrehozhatja a megfelelő formátumú fájlt. Ez a fájl ezután importálható az Excelbe vagy a Power BIba.

Ha több műveletet kell végrehajtania az adatokon az ETL-folyamat részeként, gondolja át, hogyan kezeli őket. Ha a műveleteket egy külső program vezérli, nem pedig a megoldáson belüli munkafolyamatként, el kell döntenie, hogy vannak-e párhuzamosan végrehajtható műveletek, valamint az egyes feladatok befejezésének észlelése. A munkafolyamati mechanizmusok, például a Hadoop belüli Oozie könnyebben megoldhatók, mint a külső parancsfájlok vagy egyéni programok használatával végzett műveletek sorrendjének kipróbálása. További információ a Oozie: [munkafolyamatok és feladatok](https://msdn.microsoft.com/library/dn749829.aspx)összehangolása.

## <a name="next-steps"></a>További lépések

* [ETL-skála](apache-hadoop-etl-at-scale.md)
* [Adatfolyamatok működővé tenni](../hdinsight-operationalize-data-pipeline.md)

<!-- * [ETL Deep Dive](../hdinsight-etl-deep-dive.md) -->
