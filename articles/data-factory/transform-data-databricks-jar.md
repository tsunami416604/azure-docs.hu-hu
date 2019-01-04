---
title: Adatok átalakítása a Databricks Jar – Azure |} A Microsoft Docs
description: Ismerje meg a feldolgozása vagy átalakíthatja azokat egy Databricks Jar futtatásával.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/15/2018
ms.author: douglasl
ms.openlocfilehash: 8a271359f09ca63e1a0c3a143994739ee7db8aab
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54014182"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>Adatok átalakítása egy Jar tevékenység futtatja az Azure Databricksben

Az Azure Databricks Jar tevékenységének egy [Data Factory-folyamatot](concepts-pipelines-activities.md) egy Spark-Jar fut az Azure Databricks-fürt. Ez a cikk épül, amely a [adat-átalakítási tevékenységeket](transform-data.md) című cikket, amely megadja az adatok átalakítását és a támogatott Adatátalakítási tevékenységek általános áttekintése. Az Azure Databricks egy Apache Spark rendszert futtató felügyelt platform.

Az alábbi videóban a funkció bemutatását és ismertetését tekintheti meg tizenegy percben:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-jar-activity-definition"></a>Databricks Jar tevékenységdefinícióban

Itt látható a minta egy Databricks-Jar tevékenység JSON-definíciót:

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

## <a name="databricks-jar-activity-properties"></a>Databricks Jar-tevékenység tulajdonságai

A következő táblázat ismerteti a JSON-definíciójában használt JSON-tulajdonságokat:

|Tulajdonság|Leírás|Szükséges|
|:--|---|:-:|
|név|A folyamat a tevékenység neve.|Igen|
|leírás|A tevékenység leírása leíró szöveg.|Nem|
|type|Databricks Jar tevékenységhez a tevékenység típus DatabricksSparkJar.|Igen|
|linkedServiceName|A Jar-tevékenység fut, amelyen a Databricks társított szolgáltatás neve. Ezt a társított szolgáltatást kapcsolatos további információkért lásd: [társított szolgáltatások számítása](compute-linked-services.md) cikk.|Igen|
|mainClassName|Az osztály, amely tartalmazza a fő módszert hajtható végre teljes neve. Ez az osztály egy JAR kódtárként megadott tartalmaznia kell.|Igen|
|paraméterek|A fő metódusnak átadott paraméterek.  Ez a karakterláncok tömbje.|Nem|
|Kódtárak|Telepíteni a fürt, amely végrehajtja a feladat a könyvtárak listáját. Álló tömb lehet < karakterlánc, objektum >|Igen (legalább egy tartalmazó mainClassName módszer)|

## <a name="supported-libraries-for-databricks-activities"></a>Databricks-tevékenységek támogatott kódtárak

A fenti Databricks tevékenységdefinícióban, adja meg könyvtár típusaival: *jar*, *tojás*, *maven*, *pypi*,  *cran*.

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
                "repo": "http://cran.us.r-project.org"
            }
        }
    ]
}

```

További részletekért tekintse meg [Databricks dokumentációja](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) függvénytár típusaihoz.

## <a name="how-to-upload-a-library-in-databricks"></a>Hogyan tölthet fel egy gyűjteményt a Databricks

#### <a name="using-databricks-workspace-uihttpsdocsazuredatabricksnetuser-guidelibrarieshtmlcreate-a-library"></a>[Databricks-munkaterület felhasználói felület használatával](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

A könyvtár hozzáadása a felhasználói felület használatával dbfs elérési beszerzéséhez használja [a Databricks parancssori felület (telepítés)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Általában a Jar-kódtárakat dbfs tárolt: / {{FileStore/jars a felhasználói felület használata közben. A parancssori felületén keresztül az összes listázhatja: *databricks fs ls dbfs: / {{FileStore/feladat-JAR-fájlok kivételével* 



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Másolási szalagtár Databricks parancssori felület használatával](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)
Databricks parancssori felület használata [(telepítési lépéseket)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Példa – másolás JAR dbfs: *dbfs cp SparkPi-szerelvény-0.1.jar dbfs:/docs/sparkpi.jar*
