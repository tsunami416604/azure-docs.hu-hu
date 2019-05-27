---
title: Adatok átalakítása a Databricks Pythonnal – Azure |} A Microsoft Docs
description: Ismerje meg a feldolgozása vagy átalakíthatja azokat egy Databricks Python futtatásával.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/15/2018
author: gauravmalhot
ms.author: gamal
ms.reviewer: maghan
manager: craigg
ms.openlocfilehash: 3ab3ec5380fbc90dffd4f258073ad8b477e2318a
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002834"
---
# <a name="transform-data-by-running-a-python-activity-in-azure-databricks"></a>Adatok átalakítása egy Python-tevékenység fut az Azure Databricksben

Az Azure Databricks Python tevékenység egy [Data Factory-folyamatot](concepts-pipelines-activities.md) az Azure Databricks-fürtön fut egy Python-fájlt. Ez a cikk épül, amely a [adat-átalakítási tevékenységeket](transform-data.md) című cikket, amely megadja az adatok átalakítását és a támogatott Adatátalakítási tevékenységek általános áttekintése. Az Azure Databricks egy Apache Spark rendszert futtató felügyelt platform.

Az alábbi videóban a funkció bemutatását és ismertetését tekintheti meg tizenegy percben:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-python-activity-definition"></a>Databricks Python tevékenységdefinícióban

Itt látható a minta egy Databricks Python tevékenység JSON-definíciót:

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

A következő táblázat ismerteti a JSON-definíciójában használt JSON-tulajdonságokat:

|Tulajdonság|Leírás|Szükséges|
|---|---|---|
|név|A folyamat a tevékenység neve.|Igen|
|description|A tevékenység leírása leíró szöveg.|Nem|
|típus|Databricks Python tevékenységhez a tevékenység esetében DatabricksSparkPython.|Igen|
|linkedServiceName|A Python-tevékenység fut, amelyen a Databricks társított szolgáltatás neve. Ezt a társított szolgáltatást kapcsolatos további információkért lásd: [társított szolgáltatások számítása](compute-linked-services.md) cikk.|Igen|
|pythonFile|URI-ját a Python-fájlt kell végrehajtani. Csak a DBFS elérési utak támogatottak.|Igen|
|paraméterek|Parancssori paraméterek, amelyek a rendszer átad a Python-fájlt. Ez a karakterláncok tömbje.|Nem|
|Kódtárak|Telepíteni a fürt, amely végrehajtja a feladat a könyvtárak listáját. Álló tömb lehet < karakterlánc, objektum >|Nem|

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
                "repo": "https://cran.us.r-project.org"
            }
        }
    ]
}

```

További részletekért tekintse meg [Databricks dokumentációja](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) függvénytár típusaihoz.

## <a name="how-to-upload-a-library-in-databricks"></a>Hogyan tölthet fel egy gyűjteményt a Databricks

#### <a name="using-databricks-workspace-uihttpsdocsazuredatabricksnetuser-guidelibrarieshtmlcreate-a-library"></a>[Databricks-munkaterület felhasználói felület használatával](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

A könyvtár hozzáadása a felhasználói felület használatával dbfs elérési beszerzéséhez használja [a Databricks parancssori felület (telepítés)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Általában a Jar-kódtárakat dbfs tárolt: / {{FileStore/jars a felhasználói felület használata közben. A parancssori felületén keresztül az összes listázhatja: *databricks fs ls dbfs: / {{FileStore/JAR-fájlok vagy kivételével* 



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Másolási szalagtár Databricks parancssori felület használatával](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Példa: *databricks fs cp SparkPi-szerelvény-0.1.jar dbfs: / {{FileStore/JAR-fájlok vagy kivételével*