---
title: Adatok átalakítása a Spark-tevékenység használatával
description: Ismerje meg, hogyan alakíthatja át az adatokat a Spark-programok azure-beli adatfeldolgozó folyamatból való futtatásával a Spark-tevékenység használatával.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 05/31/2018
ms.openlocfilehash: c39575e8ea60a091124c633f8958ec36e8a61885
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418847"
---
# <a name="transform-data-using-spark-activity-in-azure-data-factory"></a>Adatok átalakítása az Azure Data Factory Spark-tevékenységével
> [!div class="op_single_selector" title1="Válassza ki a használt Data Factory szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-spark.md)
> * [Aktuális verzió](transform-data-using-spark.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A Spark-tevékenység egy Data [Factory-folyamat](concepts-pipelines-activities.md) egy Spark-programot hajt végre [a saját](compute-linked-services.md#azure-hdinsight-linked-service) vagy igény szerinti [HDInsight-fürtön.](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Ez a cikk az [adatátalakítási tevékenységek](transform-data.md) cikkre épül, amely általános áttekintést nyújt az adatok átalakításáról és a támogatott átalakítási tevékenységekről. Amikor igény szerinti Spark-alapú szolgáltatást használ, a Data Factory automatikusan létrehoz egy Spark-fürtöt az adatok feldolgozásához, majd törli a fürtöt, amint a feldolgozás befejeződött. 


## <a name="spark-activity-properties"></a>Spark-tevékenység tulajdonságai
Itt van a Spark-tevékenység JSON-definíciója:    

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
        "rootPath": "adfspark",
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

Az alábbi táblázat a JSON-definícióban használt JSON-tulajdonságokat ismerteti:

| Tulajdonság              | Leírás                              | Kötelező |
| --------------------- | ---------------------------------------- | -------- |
| név                  | A folyamatban lévő tevékenység neve.    | Igen      |
| leírás           | A tevékenység tevékenységét leíró szöveg.  | Nem       |
| type                  | A Spark-tevékenység esetén a tevékenység típusa HDInsightSpark. | Igen      |
| linkedServiceName     | Annak a HDInsight-szikracsatolt szolgáltatásnak a neve, amelyen a Spark-program fut. A csatolt szolgáltatásról a [Csatolt szolgáltatások számítási cikkében](compute-linked-services.md) olvashat. | Igen      |
| SparkJobLinkedService | Az Azure Storage-hoz kapcsolódó szolgáltatás, amely rendelkezik a Spark-feladat fájl, függőségek és naplók.  Ha nem ad meg értéket ehhez a tulajdonsághoz, a rendszer a HDInsight-fürthöz társított tárolót használja. A tulajdonság értéke csak egy Azure Storage-kapcsolt szolgáltatás. | Nem       |
| rootPath              | Az Azure Blob-tároló és a Spark-fájlt tartalmazó mappa. A fájlnév nem imitáta a kis- és nagybetűk között. A mappa szerkezetével kapcsolatos részleteket a mappaszerkezettel kapcsolatos részben (következő szakasz) találja. | Igen      |
| entryFilePath         | A Spark-kód/-csomag gyökérmappájának relatív elérési útja. A bejegyzésfájlnak Python vagy .jar fájlnak kell lennie. | Igen      |
| Osztálynév             | Az alkalmazás Java/Spark főosztálya      | Nem       |
| Érvek             | A Spark-program parancssori argumentumainak listája. | Nem       |
| proxyUser             | A Spark-program végrehajtásához megszemélyesített felhasználói fiók | Nem       |
| sparkConfig           | Adja meg a témakörben felsorolt Spark-konfigurációs tulajdonságok értékeit: [Spark-konfiguráció - Alkalmazás tulajdonságai.](https://spark.apache.org/docs/latest/configuration.html#available-properties) | Nem       |
| getDebugInfo          | Itt adható meg, hogy a Spark-naplófájlok másolása a sparkJobLinkedService által megadott HDInsight-fürt (vagy) által használt Azure-tárolóba kerül-e. Engedélyezett értékek: Nincs, Mindig vagy Sikertelen. Alapértelmezett érték: Nincs. | Nem       |

## <a name="folder-structure"></a>Mappastruktúra
Spark-feladatok bővíthetőbbek, mint a Pig/Hive-feladatok. A Spark-feladatok, több függőségek, például jar csomagok (a java CLASSPATH helyezik el), python fájlok (a PYTHONPATH) és bármely más fájlokat.

Hozza létre a következő mappastruktúrát az Azure Blob storage által hivatkozott HDInsight csatolt szolgáltatás. Ezután töltse fel a függő fájlokat a filePath által képviselt gyökérmappában lévő megfelelő **almappákba.** Például töltsön fel python fájlokat a pyFiles almappába és a jar fájlokba a gyökérmappa jars almappájába. Futásidőben a Data Factory szolgáltatás a következő mappastruktúrát várja az Azure Blob storage-ban:     

| Útvonal                  | Leírás                              | Kötelező | Típus   |
| --------------------- | ---------------------------------------- | -------- | ------ |
| `.`(gyökér)            | A Spark-feladat gyökérelérési útja a storage-hoz csatolt szolgáltatásban | Igen      | Mappa |
| &lt;felhasználó által definiált&gt; | A Spark-feladat belépési fájljára mutató elérési út | Igen      | Fájl   |
| ./üvegek                | Az összes fájl ebben a mappában a feltöltött és elhelyezni a java classpath a fürt | Nem       | Mappa |
| ./pyFiles             | Az ebbe a mappába rendszer az összes fájlt feltölti és a fürt PYTHONPATH-jára helyezi | Nem       | Mappa |
| ./fájlok               | A mappa összes fájlja feltöltésre kerül, és a végrehajtó munkakönyvtárába kerül | Nem       | Mappa |
| ./archívumok            | A mappa összes fájlja tömörítetlen | Nem       | Mappa |
| ./naplók                | A Spark-fürtből származó naplókat tartalmazó mappa. | Nem       | Mappa |

Íme egy példa egy tároló, amely két Spark-feladat fájlokat az Azure Blob Storage által hivatkozott HDInsight csatolt szolgáltatás.

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
Az alábbi cikkekben elmagyarázhatja, hogyan alakíthatja át más módon az adatokat: 

* [U-SQL tevékenység](transform-data-using-data-lake-analytics.md)
* [Hive-tevékenység](transform-data-using-hadoop-hive.md)
* [Sertésaktivitás](transform-data-using-hadoop-pig.md)
* [MapReduce tevékenység](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming tevékenység](transform-data-using-hadoop-streaming.md)
* [Szikraaktivitás](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [Gépi tanulási kötegelt végrehajtási tevékenység](transform-data-using-machine-learning.md)
* [Tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)
