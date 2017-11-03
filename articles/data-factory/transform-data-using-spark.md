---
title: "A Spark-tevékenység használata az Azure Data Factory adatok átalakítása |} Microsoft Docs"
description: "Útmutató adatok átalakítása futtatásával Spark programokat egy, a Spark-tevékenység használata az Azure data factory-folyamathoz."
services: data-factory
documentationcenter: 
author: shengcmsft
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: shengc
ms.openlocfilehash: f1548c6ad397a7154482fa73e992aef9201c5752
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="transform-data-using-spark-activity-in-azure-data-factory"></a>A Spark-tevékenység használata az Azure Data Factory adatok átalakítása
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [1. verzió – Általánosan elérhető](v1/data-factory-spark.md)
> * [2. verzió – Előzetes verzió](transform-data-using-spark.md)

A Spark-tevékenység egy adat-előállítóban [csővezeték](concepts-pipelines-activities.md) végrehajtása a Spark program a [saját](compute-linked-services.md#azure-hdinsight-linked-service) vagy [igény szerinti](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight-fürthöz. Ez a cikk épít, a [adatok átalakítása tevékenységek](transform-data.md) cikk, amelynek során az adatok átalakítása és a támogatott átalakítása tevékenységek általános áttekintést. Egy igény szerinti Spark kapcsolódó szolgáltatás használatakor a Data Factory automatikusan létrehozza a Spark-fürt, közvetlenül az időponthoz kötött feldolgozni az adatokat, és majd törli a fürtöt, a feldolgozás befejezése után. 

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd [V1 Spark tevékenység](v1/data-factory-spark.md).

> [!IMPORTANT]
> Spark tevékenység nem támogatja a HDInsight Spark-fürtjei használja az Azure Data Lake Store elsődleges tárolására.

## <a name="spark-activity-properties"></a>A Spark-tevékenység tulajdonságai
Ez a minta egy Spark tevékenység JSON-definícióból:    

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
        "arguments": [ "arg1", "arg2" ],
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

A következő táblázat a JSON-definícióból használt JSON-tulajdonságok:

| Tulajdonság              | Leírás                              | Szükséges |
| --------------------- | ---------------------------------------- | -------- |
| név                  | A feldolgozási tevékenység nevét.    | Igen      |
| leírás           | A tevékenység mit leíró szöveg.  | Nem       |
| type                  | Spark tevékenység tevékenység típus HDInsightSpark. | Igen      |
| linkedServiceName     | A HDInsight Spark csatolt szolgáltatás a Spark program fut. neve. A csatolt szolgáltatással kapcsolatos további tudnivalókért lásd: [összekapcsolt szolgáltatások számítási](compute-linked-services.md) cikk. | Igen      |
| sparkJobLinkedService | Az Azure Storage társított szolgáltatás, amely tárolja a Spark feladat fájl, a függőségeket és a naplókat.  Ha nem ad meg egy értéket ehhez a tulajdonsághoz, a HDInsight-fürthöz társított tárolót használja a rendszer. | Nem       |
| rootPath              | Az Azure Blob-tároló és a Spark-fájlt tartalmazó mappát. A fájlnév pedig kis-és nagybetűket. Tekintse meg a gyökérmappa-szerkezetében (a következő szakaszban) szakasz ebben a mappában szerkezete vonatkozó további információért. | Igen      |
| entryFilePath         | A gyökérmappában található azon a Spark kódcsomag relatív elérési útja. | Igen      |
| Osztálynév             | Az alkalmazás Java/Spark fő osztály      | Nem       |
| Argumentumok             | A Spark program parancssori argumentumokat listáját. | Nem       |
| proxyUser             | A Spark program végrehajtásának megszemélyesíteni a felhasználói fiók | Nem       |
| sparkConfig           | Adja meg a témakörben ismertetett Spark konfigurációs tulajdonságok értékeit: [Spark konfigurációs - alkalmazás tulajdonságainak](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Nem       |
| getDebugInfo          | Itt adhatja meg, amikor a Spark naplófájlok kerülnek a HDInsight-fürt által használt Azure storage (vagy) leírt módon sparkJobLinkedService. Megengedett értékek: None, mindig, vagy sikertelen. Alapértelmezett érték: nincs. | Nem       |

## <a name="folder-structure"></a>Mappaszerkezet
Spark feladatok olyan több bővíthető, mint a Pig vagy Hive-feladatokat. A Spark-feladatok biztosíthat több függőség például jar (a java CLASSPATH helyezett) csomagok, python-fájlok (a PYTHONPATH helyezve) és egyéb fájlokat.

A következő mappaszerkezet létrehozása az Azure Blob Storage HDInsight kapcsolódó szolgáltatás hivatkozik. Ezután a megfelelő mappákat a gyökérmappában által képviselt függő fájlok feltöltése **entryFilePath**. Python pyFiles almappájába és jar-fájlok például a legfelső szintű JAR-fájlok kivételével almappája feltöltése. Futásidőben a Data Factory szolgáltatásnak a következő gyökérmappa-szerkezetében vár az Azure Blob Storage:     

| Útvonal                  | Leírás                              | Szükséges | Típus   |
| --------------------- | ---------------------------------------- | -------- | ------ |
| `.`(root)            | A Spark feladatot a tárolás társított szolgáltatásának elérési útjának gyökeréhez | Igen      | Mappa |
| &lt;felhasználó által definiált&gt; | Az elérési út a Spark feladat bejegyzés fájlra hivatkozik | Igen      | Fájl   |
| . / jars                | Ebben a mappában található összes fájl feltöltése, és a fürt a java classpath helyezve | Nem       | Mappa |
| . / pyFiles             | Ebben a mappában található összes fájl feltöltése, és a fürt PYTHONPATH helyezve | Nem       | Mappa |
| . / fájlok               | Ebben a mappában található összes fájl feltöltése és végrehajtó munkakönyvtár helyezve | Nem       | Mappa |
| . / archiválja            | Ebben a mappában található összes fájl tömörítetlen. | Nem       | Mappa |
| . / naplói                | A Spark-fürtön a naplókat tartalmazó mappa. | Nem       | Mappa |

Íme egy példa egy tárolás az Azure Blob Storage a HDInsight kapcsolódó szolgáltatás által hivatkozott két Spark feladat fájlt tartalmazza.

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
## <a name="next-steps"></a>Következő lépések
Tekintse meg a következő cikkekben talál, amely ismerteti az adatok más módon: 

* [U-SQL-tevékenység](transform-data-using-data-lake-analytics.md)
* [Hive-tevékenység](transform-data-using-hadoop-hive.md)
* [A Pig-tevékenység](transform-data-using-hadoop-pig.md)
* [MapReduce művelethez](transform-data-using-hadoop-map-reduce.md)
* [Hadoop Streamelési tevékenységben](transform-data-using-hadoop-streaming.md)
* [A Spark-tevékenység](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [Machine Learning kötegelt végrehajtási tevékenység](transform-data-using-machine-learning.md)
* [A tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)
