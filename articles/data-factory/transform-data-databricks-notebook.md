---
title: Adatok átalakítása a Databricks notebookkal
description: Ismerje meg, hogyan dolgozhatja fel vagy alakíthatja át az adatokat egy Databricks-jegyzetfüzet futtatásával.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: nabhishek
ms.author: abnarain
manager: shwang
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 6d3c9f0df0d834ffe75d0b56e3c80a432c27ea38
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419017"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Adatok átalakítása Databricks-jegyzetfüzet futtatásával
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Az Azure Databricks notebook tevékenység egy [data factory folyamat](concepts-pipelines-activities.md) fut egy Databricks notebook az Azure Databricks munkaterületen. Ez a cikk az [adatátalakítási tevékenységek](transform-data.md) cikkre épül, amely általános áttekintést nyújt az adatok átalakításáról és a támogatott átalakítási tevékenységekről.Az Azure Databricks egy felügyelt platform az Apache Spark futtatásához.

## <a name="databricks-notebook-activity-definition"></a>Databricks notebook tevékenységdefiníciója

Itt van a minta JSON meghatározása Databricks Notebook tevékenység:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksNotebook",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "notebookPath": "/Users/user@example.com/ScalaExampleNotebook",
            "baseParameters": {
                "inputpath": "input/folder1/",
                "outputpath": "output/"
            },
            "libraries": [
                {
                "jar": "dbfs:/docs/library.jar"
                }
            ]
        }
    }
}
```

## <a name="databricks-notebook-activity-properties"></a>Databricks notebook tevékenység tulajdonságai

Az alábbi táblázat a JSON-definícióban használt JSON-tulajdonságokat ismerteti:

|Tulajdonság|Leírás|Kötelező|
|---|---|---|
|név|A folyamatban lévő tevékenység neve.|Igen|
|leírás|A tevékenység tevékenységét leíró szöveg.|Nem|
|type|A Databricks notebook tevékenység, a tevékenység típusa DatabricksNotebook.|Igen|
|linkedServiceName|Annak a Databricks csatolt szolgáltatásnak a neve, amelyen a Databricks-jegyzetfüzet fut. A csatolt szolgáltatásról a [Csatolt szolgáltatások számítási cikkében](compute-linked-services.md) olvashat.|Igen|
|notebookPath|A Databricks-munkaterületen futtatandó jegyzetfüzet abszolút elérési útja. Ennek az elérési útnak perjellel kell kezdődnie.|Igen|
|baseParameters|Kulcs-érték párok tömbje. Az alapparaméterek minden tevékenységfuttatáshoz használhatók. Ha a jegyzetfüzet olyan paramétert vesz igénybe, amely nincs megadva, a rendszer a jegyzetfüzet alapértelmezett értékét használja. További információ a [Databricks notebookok paramétereiről.](https://docs.databricks.com/api/latest/jobs.html#jobsparampair)|Nem|
|Könyvtárak|A feladatot végrehajtó fürtre telepítendő tárak listája. Ez lehet \<egy tömb a húr, objektum>.|Nem|


## <a name="supported-libraries-for-databricks-activities"></a>Támogatott könyvtárak Databricks tevékenységekhez

A fenti Databricks tevékenységdefinícióban a következő könyvtártípusokat adhatja meg: *jar*, *tojás,* *whl*, *maven*, *pypi*, *cran.*

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
            "whl": "dbfs:/mnt/libraries/mlflow-0.0.1.dev0-py2-none-any.whl"
        },
        {
            "whl": "dbfs:/mnt/libraries/wheel-libraries.wheelhouse.zip"
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

További részletekért tekintse meg a [Databricks dokumentációt](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) a függvénytár-típusok.

## <a name="passing-parameters-between-notebooks-and-data-factory"></a>Paraméterek átadása a jegyzetfüzetek és a Data Factory között

Adatgyári paramétereket adhat át a jegyzetfüzetek használatával *baseParameters* tulajdonság databricks tevékenység. 

Bizonyos esetekben előfordulhat, hogy vissza kell adnia bizonyos értékeket a jegyzetfüzetből az adat-előállítóba, amely az adat-előállítóban használható(feltételes ellenőrzések) vagy a későbbi tevékenységek által felhasználható (a méretkorlát 2 MB). 

1. A notebook, meghívhat [dbutils.notebook.exit("returnValue")](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-workflows.html#notebook-workflows-exit) és a megfelelő "returnValue" visszakerül az adat-előállító.

2. A kimenetet az adat-előállítóban `'@activity('databricks notebook activity name').output.runOutput'`is felhasználhatja a kifejezés használatával, például . 

   > [!IMPORTANT]
   > Ha JSON-objektumot ad át, az értékeket tulajdonságnevek hozzáfűzésével kérheti le. Például: `'@activity('databricks notebook activity name').output.runOutput.PropertyName'`

## <a name="how-to-upload-a-library-in-databricks"></a>Könyvtár feltöltése databricks-ben

#### <a name="using-databricks-workspace-ui"></a>[A Databricks munkaterület felhasználói felületének használata](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

A felhasználói felület használatával hozzáadott könyvtár dbfs elérési útjának beszerzéséhez használhatja a [Databricks CLI (telepítés) parancsot.](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli) 

A Jar-kódtárak általában a dbfs:/FileStore/jars alatt tárolódnak a felhasználói felület használata közben. Felsorolhatja az egész CLI: *databricks fs ls dbfs:/FileStore/jars*.



#### <a name="copy-library-using-databricks-cli"></a>[Tár másolása a Databricks CLI használatával](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Példa: *databricks fs cp SparkPi-assembly-0.1.jar dbfs:/FileStore/jars*
