---
title: Az Apache Hive használata ETL-eszközként – Azure HDInsight
description: Az Apache Hive használatával kinyerheti, átalakíthatja és betöltheti (ETL) adatait az Azure HDInsightban.
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: be331f36a6305b05ce83a2b2d5fdfb73a154ce3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623115"
---
# <a name="use-apache-hive-as-an-extract-transform-and-load-etl-tool"></a>Az Apache Hive használata kivonat, átalakítás és betöltés (ETL) eszközként

Általában meg kell tisztítania és át kell alakítania a bejövő adatokat, mielőtt betöltené azokat egy elemzésre alkalmas célhelyre. A kinyerési, átalakítási és betöltési (ETL) műveletek az adatok előkészítésére és az adatcélba való betöltésére szolgálnak.  A HDInsight-on futó Apache Hive képes olvasni strukturálatlan adatokban, szükség szerint feldolgozni az adatokat, majd betölteni az adatokat egy relációs adattárházba a döntéstámogató rendszerek hez. Ebben a megközelítésben az adatok kinyerése a forrásból, és méretezhető tároló, például az Azure Storage blobok vagy az Azure Data Lake Storage tárolja. Az adatok ezután átalakulnak a Hive-lekérdezések sorozata, és végül a Hive-n belül előkészítve a cél adattárba való tömeges betöltése.

## <a name="use-case-and-model-overview"></a>Használati eset és modell – áttekintés

Az alábbi ábra áttekintést nyújt az ETL-automatizálás használati esetéről és modelljéről. A bemeneti adatok a megfelelő kimenet létrehozásához alakulnak át.  Az átalakítás során az adatok megváltoztathatják az alakot, az adattípust és akár a nyelvet is.  Az ETL-folyamatok metrikussá alakíthatják az Imperialt, módosíthatják az időzónákat, és javíthatják a pontosságot, hogy megfelelően igazodjanak a célban meglévő adatokhoz.  Az ETL-folyamatok az új adatokat a meglévő adatokkal is kombinálhatják, hogy naprakészen tartsák a jelentéseket, vagy hogy további betekintést nyújtsanak a meglévő adatokba.  Az alkalmazások, például a jelentéskészítő eszközök és szolgáltatások ezután a kívánt formátumban felhasználhatók.

![Apache Hive mint ETL architektúra](./media/apache-hadoop-using-apache-hive-as-an-etl-tool/hdinsight-etl-architecture.png)

A Hadoop-ot általában olyan ETL folyamatokban használják, amelyek nagyszámú szöveges fájlt (például CSV-t) vagy kisebb, de gyakran változó számú szöveges fájlt importálnak, vagy mindkettőt.  Hive egy nagyszerű eszköz, amelynek segítségével előkészíti az adatokat betöltése előtt az adatok cél.  Hive lehetővé teszi, hogy hozzon létre egy sémát a CSV felett, és egy SQL-szerű nyelvet hozhat létre MapReduce programok, amelyek kölcsönhatásba lépnek az adatokkal.

A Hive-nek az ETL végrehajtásához való használatának tipikus lépései a következők:

1. Adatok betöltése az Azure Data Lake Storage vagy az Azure Blob Storage.
2. Hozzon létre egy Metadata Store-adatbázist (az Azure SQL Database használatával) a Hive által a sémák tárolásához.
3. Hozzon létre egy HDInsight-fürtöt, és csatlakoztassa az adattárból.
4. Adja meg azt a sémát, amelyet olvasáskor szeretne alkalmazni az adattárban lévő adatokkal szemben:

    ```
    DROP TABLE IF EXISTS hvac;

    --create the hvac table on comma-separated sensor data stored in Azure Storage blobs

    CREATE EXTERNAL TABLE hvac(`date` STRING, time STRING, targettemp BIGINT,
        actualtemp BIGINT,
        system BIGINT,
        systemage BIGINT,
        buildingid BIGINT)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    STORED AS TEXTFILE LOCATION 'wasbs://{container}@{storageaccount}.blob.core.windows.net/HdiSamples/SensorSampleData/hvac/';
    ```

5. Alakítsa át az adatokat, és töltse be a célhelyre.  A Hive-ot az átalakítás és a betöltés során többféleképpen is használhatja:

    * Adatok lekérdezése és előkészítése a Hive használatával, és mentse csv-ként az Azure Data Lake Storage vagy az Azure blob storage használatával.  Ezután az SQL Server Integration Services (SSIS) eszközhöz hasonló eszközzel szerezze be ezeket a csv-ket, és töltse be az adatokat egy célrelációs adatbázisba, például az SQL Serverbe.
    * Az adatok lekérdezése közvetlenül az Excel vagy a C# a Hive ODBC illesztőprogram használatával.
    * Az [Apache Sqoop](apache-hadoop-use-sqoop-mac-linux.md) segítségével olvassa el az előkészített lapos CSV-fájlokat, és töltse be őket a célrelációs adatbázisba.

## <a name="data-sources"></a>Adatforrások

Az adatforrások általában olyan külső adatok, amelyek megfelelhetnek az adattárban lévő adatoknak, például:

* Közösségi médiaadatok, naplófájlok, érzékelők és adatfájlokat generáló alkalmazások.
* Adatszolgáltatóktól, például időjárási statisztikáktól vagy szállítói értékesítési számoktól kapott adatkészletek.
* A megfelelő eszközön vagy keretrendszeren keresztül rögzített, szűrt és feldolgozott adatfolyamok.

<!-- TODO: (see Collecting and loading data into HDInsight). -->

## <a name="output-targets"></a>Kimeneti célok

A Hive segítségével különböző célokhoz adhat ki adatokat, többek között a következőkre:

* Relációs adatbázis, például SQL Server vagy Azure SQL Database.
* Egy adattárház, például az Azure SQL Data Warehouse.
* Excel.
* Azure-tábla és blob storage.
* Olyan alkalmazások vagy szolgáltatások, amelyek nél az adatokat meghatározott formátumokba vagy meghatározott típusú információstruktúrát tartalmazó fájlokká kell feldolgozni.
* A JSON document store, mint az [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

## <a name="considerations"></a>Megfontolandó szempontok

Az ETL modell általában akkor használatos, ha a következőket szeretné:

* Adatfolyam-adatok vagy nagy mennyiségű, külső forrásokból származó félig strukturált vagy strukturálatlan adat betöltése egy meglévő adatbázisba vagy információs rendszerbe.
* Tisztítsa meg, alakítsa át és érvényesítse az adatokat betöltése előtt, esetleg egynél több átalakítási áthaladás sal a fürtön keresztül.
* Rendszeresen frissített jelentések és vizualizációk létrehozása. Ha például a jelentés létrehozása túl sokáig tart a nap folyamán, ütemezheti a jelentés éjszakai futtatását. A Hive-lekérdezés automatikus futtatásához [használhatja az Azure Logic Apps](../../logic-apps/logic-apps-overview.md) és a PowerShell.

Ha az adatok célja nem adatbázis, a lekérdezésen belül a megfelelő formátumú fájlt hozhat létre, például csv-t. Ez a fájl ezután importálható az Excelbe vagy a Power BI-ba.

Ha az ETL-folyamat részeként több műveletet kell végrehajtania az adatokon, fontolja meg, hogyan kezeli őket. Ha a műveleteket egy külső program vezérli, nem pedig a megoldáson belüli munkafolyamat, el kell döntenie, hogy egyes műveletek párhuzamosan hajthatók-e végre, és észlelnie kell, ha az egyes feladatbefejeződik. A hadoopon belüli oozie-hoz hasonló munkafolyamat-mechanizmus használata egyszerűbb lehet, mint külső parancsfájlok vagy egyéni programok használatával műveletek sorozatának vezénylése. Az Oozie-ról további információt a [Munkafolyamat és a feladatvezényezés című témakörben](https://msdn.microsoft.com/library/dn749829.aspx)talál.

## <a name="next-steps"></a>További lépések

* [ETL méretarányban](apache-hadoop-etl-at-scale.md)
* [Adatfolyamat üzembe építése](../hdinsight-operationalize-data-pipeline.md)

<!-- * [ETL Deep Dive](../hdinsight-etl-deep-dive.md) -->
