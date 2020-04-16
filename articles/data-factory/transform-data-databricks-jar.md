---
title: Adatok átalakítása a Databricks Jar segítségével
description: Ismerje meg, hogyan dolgozhatja fel vagy alakíthatja át az adatokat egy Databricks Jar futtatásával.
services: data-factory
documentationcenter: ''
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
manager: shwang
ms.date: 03/15/2018
ms.openlocfilehash: 6b010000a674e351051c664dd5eeacd40e802439
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414610"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>Adatok átalakítása Jar-tevékenység futtatásával az Azure Databricks-ben
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Az Azure Databricks Jar tevékenység egy [data factory folyamat](concepts-pipelines-activities.md) egy Spark Jar az Azure Databricks-fürtben fut. Ez a cikk az [adatátalakítási tevékenységek](transform-data.md) cikkre épül, amely általános áttekintést nyújt az adatok átalakításáról és a támogatott átalakítási tevékenységekről.Az Azure Databricks egy felügyelt platform az Apache Spark futtatásához.

Az alábbi videóban a funkció bemutatását és ismertetését tekintheti meg tizenegy percben:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-jar-activity-definition"></a>Databricks Jar tevékenység definíciója

Itt van a minta JSON meghatározása Databricks Jar Tevékenység:

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

## <a name="databricks-jar-activity-properties"></a>Databricks Jar tevékenység tulajdonságai

Az alábbi táblázat a JSON-definícióban használt JSON-tulajdonságokat ismerteti:

|Tulajdonság|Leírás|Kötelező|
|:--|---|:-:|
|név|A folyamatban lévő tevékenység neve.|Igen|
|leírás|A tevékenység tevékenységét leíró szöveg.|Nem|
|type|Databricks Jar tevékenység esetén a tevékenység típusa DatabricksSparkJar.|Igen|
|linkedServiceName|Annak a Databricks csatolt szolgáltatásnak a neve, amelyen a Jar tevékenység fut. A csatolt szolgáltatásról a [Csatolt szolgáltatások számítási cikkében](compute-linked-services.md) olvashat.|Igen|
|mainClassName (mainClassName)|A végrehajtandó fő metódust tartalmazó osztály teljes neve. Ezt az osztályt könyvtárként biztosított JAR-ban kell tartani.|Igen|
|paraméterek|A fő metódusnak átadott paraméterek.  Ez egy sor húr.|Nem|
|Könyvtárak|A feladatot végrehajtó fürtre telepítendő tárak listája. Ez lehet <karakterlánc, objektum>|Igen (legalább egy, amely a mainClassName metódust tartalmazza)|

> [!NOTE]
> **Ismert probléma** – Ha ugyanazt az [interaktív fürtöt](compute-linked-services.md#example---using-existing-interactive-cluster-in-databricks) használja egyidejű Databricks Jar tevékenységek futtatásához (fürt újraindítása nélkül), van egy ismert probléma databricks, ahol az első tevékenység paramétereiben fogja használni a következő tevékenységek is. Ezért helytelen paraméterek et adnak át a következő feladatoknak. Ennek csökkentése érdekében használja a [feladatfürt](compute-linked-services.md#example---using-new-job-cluster-in-databricks) helyett. 

## <a name="supported-libraries-for-databricks-activities"></a>Támogatott kódtárak adatbricktevékenységekhez

A fenti Databricks tevékenységdefinícióban a következő könyvtártípusokat adhatja meg: *jar*, *tojás,* *maven*, *pypi*, *cran.*

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

További részletekért tekintse [databricks dokumentáció](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) könyvtártípusok.

## <a name="how-to-upload-a-library-in-databricks"></a>Könyvtár feltöltése databricks-ben

#### <a name="using-databricks-workspace-ui"></a>[A Databricks munkaterület felhasználói felületének használata](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

A felhasználói felület használatával hozzáadott könyvtár dbfs elérési útjának beszerzéséhez használhatja a [Databricks CLI (telepítés) parancsot.](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli) 

A Jar-kódtárak általában a dbfs:/FileStore/jars alatt tárolódnak a felhasználói felület használata közben. Felsorolhatja az egész CLI: *databricks fs ls dbfs:/FileStore/job-jars* 



#### <a name="copy-library-using-databricks-cli"></a>[Tár másolása a Databricks CLI használatával](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)
Használja a Databricks [CLI-t (telepítési lépések)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Példa - JAR másolása a dbfs-be: *dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar*
