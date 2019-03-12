---
title: Adatok átalakítása a Databricks-jegyzetfüzet – Azure |} A Microsoft Docs
description: Ismerje meg a feldolgozása vagy átalakíthatja azokat egy Databricks-jegyzetfüzet futtatásával.
services: data-factory
documentationcenter: ''
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/15/2018
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: 8036a8694bb8c8d0db236eba831f13dc2bf47d0a
ms.sourcegitcommit: 30a0007f8e584692fe03c0023fe0337f842a7070
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57576820"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Adatok átalakítása a Databricks-jegyzetfüzet futtatása

Az Azure Databricks-jegyzetfüzet tevékenységeivel az egy [Data Factory-folyamatot](concepts-pipelines-activities.md) Databricks-jegyzetfüzet futtatása az Azure Databricks-munkaterületen. Ez a cikk épül, amely a [adat-átalakítási tevékenységeket](transform-data.md) című cikket, amely megadja az adatok átalakítását és a támogatott Adatátalakítási tevékenységek általános áttekintése. Az Azure Databricks egy Apache Spark rendszert futtató felügyelt platform.

## <a name="databricks-notebook-activity-definition"></a>Databricks-jegyzetfüzet tevékenységet definíciója

Itt látható a minta JSON-definícióját egy Databricks-jegyzetfüzetek tevékenységeit:

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

## <a name="databricks-notebook-activity-properties"></a>Databricks-jegyzetfüzet tevékenység tulajdonságai

A következő táblázat ismerteti a JSON-definíciójában használt JSON-tulajdonságokat:

|Tulajdonság|Leírás|Szükséges|
|---|---|---|
|név|A folyamat a tevékenység neve.|Igen|
|leírás|A tevékenység leírása leíró szöveg.|Nem|
|type|Databricks-jegyzetfüzetek tevékenységeit tevékenység típus DatabricksNotebook.|Igen|
|linkedServiceName|Amelyen futtatja a Databricks-jegyzetfüzetek Databricks társított szolgáltatás neve. Ezt a társított szolgáltatást kapcsolatos további információkért lásd: [társított szolgáltatások számítása](compute-linked-services.md) cikk.|Igen|
|notebookPath|A jegyzetfüzet futtatása a Databricks-munkaterület az abszolút elérési útja. Az elérési út perjellel kell kezdődnie.|Igen|
|baseParameters|Kulcs-érték párokból álló tömb. Alap paramétereket az egyes tevékenységek futtatásához használható. Ha a notebook tart egy paramétert, amely nincs megadva, az alapértelmezett érték a notebookból használható. További információ: a paraméterek [Databricks-jegyzetfüzeteket](https://docs.databricks.com/api/latest/jobs.html#jobsparampair).|Nem|
|Kódtárak|Telepíteni a fürt, amely végrehajtja a feladat a könyvtárak listáját. Álló tömb lehet \<karakterlánc, objektum >.|Nem|


## <a name="supported-libraries-for-databricks-activities"></a>Databricks-tevékenységek támogatott kódtárak

A fenti Databricks tevékenység meghatározásában, adja meg, ezek a szalagtár-típusok: *jar*, *tojás*, *maven*, *pypi*,  *cran*.

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

További részletekért tekintse meg a [Databricks dokumentációja](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) függvénytár típusaihoz.

## <a name="how-to-upload-a-library-in-databricks"></a>Hogyan tölthet fel egy gyűjteményt a Databricks

#### <a name="using-databricks-workspace-uihttpsdocsazuredatabricksnetuser-guidelibrarieshtmlcreate-a-library"></a>[Databricks-munkaterület felhasználói felület használatával](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

A könyvtár hozzáadása a felhasználói felület használatával dbfs elérési beszerzéséhez használja a [a Databricks parancssori felület (telepítés)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Általában a Jar-kódtárakat dbfs tárolt: / {{FileStore/jars a felhasználói felület használata közben. A parancssori felületén keresztül az összes listázhatja: *databricks fs ls dbfs: / {{FileStore/JAR-fájlok vagy kivételével*.



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Másolási szalagtár Databricks parancssori felület használatával](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Példa: *databricks fs cp SparkPi-szerelvény-0.1.jar dbfs: / {{FileStore/JAR-fájlok vagy kivételével*
