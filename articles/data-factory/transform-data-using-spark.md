---
title: Adatátalakítás a Spark-tevékenységgel
description: Megtudhatja, hogyan alakíthatja át az adatait Spark-programok futtatásával egy Azure-beli adatfeldolgozó-folyamatból a Spark-tevékenység használatával.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/31/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 27dea39a1ebd2be56c86e4327218c62c5378002d
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893756"
---
# <a name="transform-data-using-spark-activity-in-azure-data-factory"></a>Adatátalakítás a Spark-tevékenységgel Azure Data Factory
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-spark.md)
> * [Aktuális verzió](transform-data-using-spark.md)

Egy Data Factory [folyamat](concepts-pipelines-activities.md) Spark-tevékenysége egy Spark-programot hajt végre a [saját](compute-linked-services.md#azure-hdinsight-linked-service) vagy [igény szerinti HDInsight-](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) fürtön. Ez a cikk az Adatátalakítási [tevékenységekről](transform-data.md) szóló cikket ismerteti, amely általános áttekintést nyújt az adatátalakításról és a támogatott átalakítási tevékenységekről. Ha igény szerinti Spark társított szolgáltatást használ, Data Factory automatikusan létrehoz egy Spark-fürtöt az adatainak feldolgozásához, majd a feldolgozás befejeződése után törli a fürtöt. 


## <a name="spark-activity-properties"></a>Spark-tevékenység tulajdonságai
Itt látható a Spark-tevékenység JSON-definíciója:    

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

A következő táblázat a JSON-definícióban használt JSON-tulajdonságokat ismerteti:

| Tulajdonság              | Leírás                              | Szükséges |
| --------------------- | ---------------------------------------- | -------- |
| név                  | A folyamatban szereplő tevékenység neve.    | Igen      |
| leírás           | A tevékenység működését leíró szöveg  | Nem       |
| type                  | Spark-tevékenység esetén a tevékenység típusa HDInsightSpark. | Igen      |
| linkedServiceName     | Azon HDInsight Spark társított szolgáltatás neve, amelyen a Spark-program fut. A társított szolgáltatással kapcsolatos további információkért lásd: [számítási társított szolgáltatások](compute-linked-services.md) cikk. | Igen      |
| sparkJobLinkedService | Az Azure Storage társított szolgáltatása, amely tartalmazza a Spark-feladatot tartalmazó fájlt, a függőségeket és a naplókat.  Ha nem ad meg értéket ehhez a tulajdonsághoz, a rendszer a HDInsight-fürthöz társított tárolót használja. Ennek a tulajdonságnak az értéke csak Azure Storage társított szolgáltatás lehet. | Nem       |
| rootPath              | Az Azure Blob-tároló és a Spark-fájlt tartalmazó mappa. A fájl neve megkülönbözteti a kis-és nagybetűket. A mappa struktúrájával kapcsolatos részletekért tekintse meg a mappa szerkezete szakaszt (következő szakasz). | Igen      |
| entryFilePath         | A Spark-kód/csomag gyökérkönyvtárának relatív elérési útja. A beviteli fájlnak Python-vagy. jar-fájlnak kell lennie. | Igen      |
| className             | Alkalmazás Java/Spark fő osztálya      | Nem       |
| argumentumok             | A Spark programhoz tartozó parancssori argumentumok listája. | Nem       |
| proxyUser             | A Spark-program végrehajtásához megszemélyesíteni kívánt felhasználói fiók | Nem       |
| sparkConfig           | A Spark-konfiguráció tulajdonságainak megadása a következő témakörben: [Spark konfiguráció-alkalmazás tulajdonságai](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Nem       |
| getDebugInfo          | Megadja, hogy a rendszer mikor másolja a Spark-naplófájlokat a sparkJobLinkedService által meghatározott HDInsight-fürt (vagy) által használt Azure-tárolóba. Megengedett értékek: nincs, mindig vagy sikertelen. Alapértelmezett érték: nincs. | Nem       |

## <a name="folder-structure"></a>Mappa szerkezete
A Spark-feladatok sokkal bővíthetők, mint a Pig/kaptár feladatok. A Spark-feladatok esetében több függőséget is megadhat, például a jar-csomagokat (a Java OSZTÁLYÚTVONAL helyezik el), a Python-fájlokat (a PYTHONPATH) és minden más fájlt.

Hozza létre a következő mappastruktúrát a HDInsight társított szolgáltatás által hivatkozott Azure Blob Storage-tárolóban. Ezután töltse fel a függő fájlokat a **entryFilePath**által jelzett gyökérmappa megfelelő almappáiba. Töltse fel például a Python-fájlokat a pyFiles almappában és a jar-fájlokba a gyökérmappa tégelyek almappájába. Futásidőben a Data Factory szolgáltatás a következő mappastruktúrát várja az Azure Blob Storage-ban:     

| Útvonal                  | Leírás                              | Szükséges | Type (Típus)   |
| --------------------- | ---------------------------------------- | -------- | ------ |
| `.` (root)            | A Spark-feladatokhoz tartozó gyökér elérési útja a Storage társított szolgáltatásban | Igen      | Mappa |
| &lt;felhasználó által definiált &gt; | A Spark-feladathoz tartozó belépési fájlra mutató elérési út | Igen      | File   |
| ./jars                | A rendszer feltölti és elhelyezi a mappa összes fájlját a fürt Java-osztályútvonal. | Nem       | Mappa |
| ./pyFiles             | A mappa összes fájlja fel van töltve és a fürt PYTHONPATH kerül. | Nem       | Mappa |
| ./files               | A mappa összes fájlja fel van töltve, és a végrehajtó munkakönyvtárba kerül. | Nem       | Mappa |
| ./archives            | A mappa összes fájlja nem tömörített | Nem       | Mappa |
| ./logs                | A Spark-fürtből származó naplókat tartalmazó mappa. | Nem       | Mappa |

Íme egy példa egy olyan tárolóra, amely két Spark-feladatot tartalmaz a HDInsight társított szolgáltatás által hivatkozott Azure-Blob Storage.

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
A következő cikkekből megtudhatja, hogyan alakíthat át más módon az adatátalakítást: 

* [U-SQL-tevékenység](transform-data-using-data-lake-analytics.md)
* [Struktúra tevékenysége](transform-data-using-hadoop-hive.md)
* [Pig-tevékenység](transform-data-using-hadoop-pig.md)
* [MapReduce tevékenység](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming-tevékenység](transform-data-using-hadoop-streaming.md)
* [Spark-tevékenység](transform-data-using-spark.md)
* [.NET egyéni tevékenység](transform-data-using-dotnet-custom-activity.md)
* [Batch-végrehajtási tevékenység Machine Learning](transform-data-using-machine-learning.md)
* [Tárolt eljárási tevékenység](transform-data-using-stored-procedure.md)
