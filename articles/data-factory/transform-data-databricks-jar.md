---
title: Az adatátalakítás a Databricks jar-Azure-ban
description: Megtudhatja, hogyan dolgozhat fel és alakíthat át adatátalakítást egy Databricks jar futtatásával.
services: data-factory
documentationcenter: ''
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/15/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: 982f00b5de9fd3e84233e5fe3b68e22fa6f7fe2a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683956"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>Az adatátalakításhoz jar-tevékenység fut Azure Databricks

A [Data Factory folyamat](concepts-pipelines-activities.md) Azure Databricks jar-tevékenysége egy Spark jar-t futtat a Azure Databricks-fürtben. Ez a cikk az Adatátalakítási [tevékenységekre](transform-data.md) cikkre épül, amely általános áttekintést nyújt az adatátalakításról és a támogatott átalakítási tevékenységekről. A Azure Databricks felügyelt platform a Apache Spark futtatásához.

Az alábbi videóban a funkció bemutatását és ismertetését tekintheti meg tizenegy percben:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-jar-activity-definition"></a>Databricks jar-tevékenység definíciója

Itt látható a Databricks jar-tevékenység JSON-definíciója:

```json
{
    "name": "SparkJarActivity",
    "type": "DatabricksSparkJar",
    "linkedServiceName": {
        "referenceName": "AzureDatabricks",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mainClassName": "org.apache.spark.examples.SparkPi",
        "parameters": [ "10" ],
        "libraries": [
            {
                "jar": "dbfs:/docs/sparkpi.jar"
            }
        ]
    }
}

```

## <a name="databricks-jar-activity-properties"></a>Databricks jar-tevékenység tulajdonságai

A következő táblázat a JSON-definícióban használt JSON-tulajdonságokat ismerteti:

|Tulajdonság|Leírás|Kötelező|
|:--|---|:-:|
|név|A folyamatban szereplő tevékenység neve.|Igen|
|leírás|A tevékenység működését leíró szöveg|Nem|
|type|A Databricks jar tevékenység esetén a tevékenység típusa DatabricksSparkJar.|Igen|
|linkedServiceName|Annak a Databricks társított szolgáltatásnak a neve, amelyen a jar-tevékenység fut. A társított szolgáltatásról a következő témakörben talál további információt: [számítási társított szolgáltatások](compute-linked-services.md) cikk.|Igen|
|mainClassName|A végrehajtandó fő metódust tartalmazó osztály teljes neve. Ennek az osztálynak szerepelnie kell egy könyvtárként megadott JAR-fájlban.|Igen|
|paraméterek|A Main metódusnak átadott paraméterek.  Ez a karakterláncok tömbje.|Nem|
|szalagtárak|Azoknak a táraknak a listája, amelyek a feladatot végrehajtó fürtön lesznek telepítve. < Sztring, objektum > tömbje lehet.|Igen (legalább egy mainClassName metódust tartalmaz)|

> [!NOTE]
> **Ismert probléma** – ha ugyanazt az [interaktív fürtöt](compute-linked-services.md#example---using-existing-interactive-cluster-in-databricks) használja az egyidejű Databricks jar-tevékenységek futtatásához (a fürt újraindítása nélkül), akkor ismert probléma van a Databricks, ahol az első tevékenység paramétereit a következő tevékenységek fogják használni továbbá. Emiatt a rendszer helytelen paramétereket ad át a következő feladatoknak. Ezzel a megoldással csökkentheti a [feladatok fürtjét](compute-linked-services.md#example---using-new-job-cluster-in-databricks) . 

## <a name="supported-libraries-for-databricks-activities"></a>Támogatott kódtárak a databricks-tevékenységekhez

A fenti Databricks-tevékenység definíciójában a következő típustár-típusokat adhatja meg: *jar*, *Egg*, *Maven*, *PyPI*, *Cran*.

```json
{
    "libraries": [
        {
            "jar": "dbfs:/mnt/libraries/library.jar"
        },
        {
            "egg": "dbfs:/mnt/libraries/library.egg"
        },
        {
            "maven": {
                "coordinates": "org.jsoup:jsoup:1.7.2",
                "exclusions": [ "slf4j:slf4j" ]
            }
        },
        {
            "pypi": {
                "package": "simplejson",
                "repo": "http://my-pypi-mirror.com"
            }
        },
        {
            "cran": {
                "package": "ada",
                "repo": "https://cran.us.r-project.org"
            }
        }
    ]
}

```

További részletekért tekintse meg a [Databricks dokumentációját](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) .

## <a name="how-to-upload-a-library-in-databricks"></a>Könyvtár feltöltése a Databricks-ben

#### <a name="using-databricks-workspace-uihttpsdocsazuredatabricksnetuser-guidelibrarieshtmlcreate-a-library"></a>[Databricks-munkaterület felhasználói felületének használata](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

A felhasználói felület használatával hozzáadott könyvtár dbfs elérési útjának beszerzéséhez használhatja a [DATABRICKS CLI-t (telepítés)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

A jar-kódtárak általában a dbfs:/FileStore/tégelyek alatt tárolódnak, miközben a felhasználói felületet használják. A CLI-n keresztül listázhatja az összeset: *databricks FS ls dbfs:/FileStore/Job-jars* 



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Könyvtár másolása a Databricks CLI használatával](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)
Használja a Databricks CLI [-t (telepítési lépések)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Példa – a JAR másolása a dbfs: *dbfs CP sparkpi-Assembly-0.1. jar dbfs:/docs/SparkPi. jar*
