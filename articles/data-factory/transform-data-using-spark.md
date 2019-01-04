---
title: Adatok átalakítása a Spark-tevékenység használatával az Azure Data Factoryban |} A Microsoft Docs
description: 'Útmutató: adatok átalakítása a Spark program való futtatásával egy, a Spark-tevékenység használatával az Azure data factory-folyamatot.'
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/31/2018
ms.author: douglasl
ms.openlocfilehash: a25505a976be9d9ae38f562591d86ca9b56b8859
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54025612"
---
# <a name="transform-data-using-spark-activity-in-azure-data-factory"></a>Adatok átalakítása a Spark-tevékenység használatával az Azure Data Factoryban
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1-es verzió](v1/data-factory-spark.md)
> * [Aktuális verzió](transform-data-using-spark.md)

A Spark-tevékenység az adat-előállító [folyamat](concepts-pipelines-activities.md) lefut egy Spark-programot a [saját](compute-linked-services.md#azure-hdinsight-linked-service) vagy [igény szerinti](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight-fürt. Ez a cikk épül, amely a [adat-átalakítási tevékenységeket](transform-data.md) című cikket, amely megadja az adatok átalakítását és a támogatott Adatátalakítási tevékenységek általános áttekintése. Egy igény szerinti Spark társított szolgáltatás használata esetén a Data Factory automatikusan létrehoz egy Spark-fürtöt,-igény az adatok feldolgozása a, és ezután törli a fürtöt, a feldolgozás befejeződése után. 

> [!IMPORTANT]
> Spark-tevékenység nem támogatja a HDInsight Spark-fürtökön, amely egy Azure Data Lake Store elsődleges tárolóként használnia.

## <a name="spark-activity-properties"></a>Spark-tevékenység tulajdonságai
Itt látható a minta egy Spark-tevékenység JSON-definíciót:    

```json
{
    "name": "Spark Activity",
    "description": "Description",
    "type": "HDInsightSpark",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "sparkJobLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "rootPath": "adfspark\\pyFiles",
        "entryFilePath": "test.py",
        "sparkConfig": {
            "ConfigItem1": "Value"
        },
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ]
    }
}
```

A következő táblázat ismerteti a JSON-definíciójában használt JSON-tulajdonságokat:

| Tulajdonság              | Leírás                              | Szükséges |
| --------------------- | ---------------------------------------- | -------- |
| név                  | A folyamat a tevékenység neve.    | Igen      |
| leírás           | A tevékenység leírása leíró szöveg.  | Nem       |
| type                  | A Spark-tevékenység a tevékenység típus HDInsightSpark. | Igen      |
| linkedServiceName     | A HDInsight Spark társított szolgáltatás, amelyen futtatja a Spark-program neve. Ezt a társított szolgáltatást kapcsolatos további információkért lásd: [társított szolgáltatások számítása](compute-linked-services.md) cikk. | Igen      |
| SparkJobLinkedService | Az Azure Storage társított szolgáltatás, amely tartalmazza a Spark, feladat-fájlt, a függőségeket és a naplókat.  Ha nem ad meg egy értéket ehhez a tulajdonsághoz, a HDInsight-fürthöz társított tárolót használja. Ez a tulajdonság értéke csak lehet az Azure Storage társított szolgáltatása. | Nem       |
| rootPath              | Az Azure Blob-tároló és a Spark-fájlt tartalmazó mappát. A fájlnév formátuma a kis-és nagybetűket. Gyökérmappa-szerkezetében hivatkoznak (a következő szakaszban) szakaszban Ez a mappa szerkezete részleteit. | Igen      |
| entryFilePath         | A gyökérmappában található azon a Spark kódcsomag relatív elérési útja. A bejegyzés fájlnak kell lennie, vagy egy Python-fájlt, vagy a .jar-fájl. | Igen      |
| Osztálynév             | Alkalmazás Java/Spark main osztály      | Nem       |
| argumentumok             | A Spark-program parancssori argumentumokat listája. | Nem       |
| proxyUser             | A felhasználói fiók megszemélyesítése a Spark-program végrehajtása | Nem       |
| sparkConfig           | Adja meg a témakörben felsorolt Spark konfigurációs tulajdonságok értékeit: [Spark-konfiguráció – alkalmazástulajdonságok](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Nem       |
| getDebugInfo          | Itt adhatja meg, ha a Spark naplófájlok másolja az Azure Storage HDInsight-fürt által használt (vagy) sparkJobLinkedService által megadott. Megengedett értékek: Nincs, mindig, vagy sikertelen. Alapértelmezett érték: Nincs. | Nem       |

## <a name="folder-structure"></a>gyökérmappa-szerkezetében
Spark-feladatok olyan, mint a Pig/Hive-feladatok több bővíthető. A Spark-feladatok, megadhat több függőség például jar (a java OSZTÁLYÚTVONAL helyezett) csomagok, a python-fájlok (a PYTHONPATH helyezett) és a többi fájlt.

A következő gyökérmappa-szerkezetében létrehozása az Azure Blob storage a HDInsight társított szolgáltatás által hivatkozott. Ezután töltse fel a függő fájl a gyökérmappában által képviselt megfelelő sub mappákhoz **entryFilePath**. Például töltse fel a pyFiles almappába python-fájlok és a jar-fájlok a JAR-fájlok kivételével a legfelső szintű mappa almappája. Futásidőben a Data Factory szolgáltatás a következő mappastruktúra vár az Azure Blob storage-ban:     

| Útvonal                  | Leírás                              | Szükséges | Típus   |
| --------------------- | ---------------------------------------- | -------- | ------ |
| `.` (root)            | A meghajtógyökér elérési útja a Spark-feladat, a storage-beli társított szolgáltatás | Igen      | Mappa |
| &lt;felhasználó által definiált &gt; | Az elérési a bejegyzés fájlt a Spark-feladat | Igen      | Fájl   |
| . / jars                | Ebben a mappában található összes fájlt fel és a fürt a java osztályútvonal helyezni | Nem       | Mappa |
| ./pyFiles             | Ebben a mappában található összes fájlt fel és a fürt a PYTHONPATH helyezni | Nem       | Mappa |
| . / fájlok               | Ebben a mappában található összes fájlt feltölteni, és elhelyezett végrehajtó munkakönyvtár | Nem       | Mappa |
| . / archívum            | Ebben a mappában található összes fájlt nincs tömörítve | Nem       | Mappa |
| . / naplók                | A Spark-fürt naplóit tartalmazó mappát. | Nem       | Mappa |

Íme egy példa a Spark az Azure Blob Storage a HDInsight társított szolgáltatás által hivatkozott két feladat fájlt tartalmazó tároló.

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs
```
## <a name="next-steps"></a>További lépések
Tekintse meg a következő cikkek, amelyek bemutatják, hogyan alakíthat át adatokat, egyéb módon: 

* [U-SQL-tevékenység](transform-data-using-data-lake-analytics.md)
* [Hive-tevékenység](transform-data-using-hadoop-hive.md)
* [Pig-tevékenység](transform-data-using-hadoop-pig.md)
* [MapReduce-tevékenység](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streamelési tevékenységben](transform-data-using-hadoop-streaming.md)
* [Spark-tevékenység](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning kötegelt végrehajtási tevékenység](transform-data-using-machine-learning.md)
* [Tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)
