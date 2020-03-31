---
title: Adatok átalakítása a Databricks Pythonnal
description: Ismerje meg, hogyan dolgozhatja fel vagy alakíthatja át az adatokat egy Databricks Python futtatásával.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/15/2018
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: anandsub
ms.openlocfilehash: be2e389a0f103983a566a3f74d201e5589d84586
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74926724"
---
# <a name="transform-data-by-running-a-python-activity-in-azure-databricks"></a>Adatok átalakítása Python-tevékenység futtatásával az Azure Databricks-ben

Az Azure Databricks Python-tevékenység egy [Data Factory-folyamat](concepts-pipelines-activities.md) egy Python-fájlt futtat az Azure Databricks-fürtben. Ez a cikk az [adatátalakítási tevékenységek](transform-data.md) cikkre épül, amely általános áttekintést nyújt az adatok átalakításáról és a támogatott átalakítási tevékenységekről.Az Azure Databricks egy felügyelt platform az Apache Spark futtatásához.

Az alábbi videóban a funkció bemutatását és ismertetését tekintheti meg tizenegy percben:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-python-activity-definition"></a>Databricks Python-tevékenység definíciója

Itt van a minta JSON meghatározása Databricks Python tevékenység:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksSparkPython",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "pythonFile": "dbfs:/docs/pi.py",
            "parameters": [
                "10"
            ],
            "libraries": [
                {
                    "pypi": {
                        "package": "tensorflow"
                    }
                }
            ]
        }
    }
}
```

## <a name="databricks-python-activity-properties"></a>Databricks Python-tevékenység tulajdonságai

Az alábbi táblázat a JSON-definícióban használt JSON-tulajdonságokat ismerteti:

|Tulajdonság|Leírás|Kötelező|
|---|---|---|
|név|A folyamatban lévő tevékenység neve.|Igen|
|leírás|A tevékenység tevékenységét leíró szöveg.|Nem|
|type|Databricks Python-tevékenység esetén a tevékenység típusa DatabricksSparkPython.|Igen|
|linkedServiceName|A Databricks csatolt szolgáltatás neve, amelyen a Python-tevékenység fut. A csatolt szolgáltatásról a [Csatolt szolgáltatások számítási cikkében](compute-linked-services.md) olvashat.|Igen|
|pythonFile fájl|A végrehajtandó Python-fájl URI-ja. Csak a DBFS-elérési utak támogatottak.|Igen|
|paraméterek|A Python-fájlnak átadott parancssori paraméterek. Ez egy sor húr.|Nem|
|Könyvtárak|A feladatot végrehajtó fürtre telepítendő tárak listája. Ez lehet <karakterlánc, objektum>|Nem|

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

A Jar-kódtárak általában a dbfs:/FileStore/jars alatt tárolódnak a felhasználói felület használata közben. Felsorolhatja az egész CLI: *databricks fs ls dbfs:/FileStore/jars* 



#### <a name="copy-library-using-databricks-cli"></a>[Tár másolása a Databricks CLI használatával](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Példa: *databricks fs cp SparkPi-assembly-0.1.jar dbfs:/FileStore/jars*