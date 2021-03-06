---
title: Az adatátalakítás a Databricks Pythonral
description: Megtudhatja, hogyan dolgozhat fel és alakíthat át egy Databricks Python használatával.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/15/2018
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
manager: anandsub
ms.custom: devx-track-python
ms.openlocfilehash: 7e80fad02a186173868a6aa78aedeac0801f199a
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2020
ms.locfileid: "96496891"
---
# <a name="transform-data-by-running-a-python-activity-in-azure-databricks"></a>Az adatátalakítást egy Python-tevékenység futtatásával Azure Databricks
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


A [Data Factory folyamat](concepts-pipelines-activities.md) Azure Databricks Python-tevékenysége egy Python-fájlt futtat a Azure Databricks-fürtben. Ez a cikk az Adatátalakítási [tevékenységekről](transform-data.md) szóló cikket ismerteti, amely általános áttekintést nyújt az adatátalakításról és a támogatott átalakítási tevékenységekről. A Azure Databricks felügyelt platform a Apache Spark futtatásához.

Az alábbi videóban a funkció bemutatását és ismertetését tekintheti meg tizenegy percben:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-python-activity-definition"></a>Databricks Python-tevékenység definíciója

Itt látható a Databricks Python-tevékenység JSON-definíciója:

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

A következő táblázat a JSON-definícióban használt JSON-tulajdonságokat ismerteti:

|Tulajdonság|Leírás|Kötelező|
|---|---|---|
|name|A folyamatban szereplő tevékenység neve.|Igen|
|leírás|A tevékenység működését leíró szöveg|Nem|
|típus|A Databricks Python-tevékenység esetén a tevékenység típusa DatabricksSparkPython.|Igen|
|linkedServiceName|Annak a Databricks társított szolgáltatásnak a neve, amelyen a Python-tevékenység fut. A társított szolgáltatással kapcsolatos további információkért lásd: [számítási társított szolgáltatások](compute-linked-services.md) cikk.|Igen|
|pythonFile|A végrehajtandó Python-fájl URI-ja. Csak DBFS elérési utak támogatottak.|Igen|
|parameters|A Python-fájlnak átadandó parancssori paraméterek. Ez a karakterláncok tömbje.|Nem|
|kódtárak|Azoknak a táraknak a listája, amelyek a feladatot végrehajtó fürtön lesznek telepítve. <sztring, objektum> tömbje lehet.|Nem|

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

#### <a name="using-databricks-workspace-ui"></a>[Databricks-munkaterület felhasználói felületének használata](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

A felhasználói felület használatával hozzáadott könyvtár dbfs elérési útjának beszerzéséhez használhatja a [DATABRICKS CLI-t (telepítés)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

A jar-kódtárak általában a dbfs:/FileStore/tégelyek alatt tárolódnak, miközben a felhasználói felületet használják. A CLI: *databricks FS ls dbfs:/FileStore/tégelyek* segítségével listázhatja az összeset 



#### <a name="copy-library-using-databricks-cli"></a>[Könyvtár másolása a Databricks CLI használatával](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Például: *databricks FS CP sparkpi-Assembly-0,1. jar dbfs:/FileStore/tégelyek*
