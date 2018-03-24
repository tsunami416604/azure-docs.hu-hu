---
title: Adatok átalakítása a Databricks Notebook - Azure |} Microsoft Docs
description: Ismerje meg, hogyan kell feldolgozni vagy átalakítási adatok Databricks jegyzetfüzet futtatásával.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2018
ms.author: douglasl
ms.openlocfilehash: d4a57e45d5ddf55906fcf575df39135a227418ec
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Adatok átalakítása Databricks jegyzetfüzet futtatásával

Az Azure Databricks Notebook tevékenység egy [Data Factory-folyamathoz](concepts-pipelines-activities.md) Databricks jegyzetfüzet futtatja az Azure Databricks munkaterületen. Ez a cikk épít, a [adatok átalakítása tevékenységek](transform-data.md) cikk, amelynek során az adatok átalakítása és a támogatott átalakítása tevékenységek általános áttekintést. Az Azure Databricks egy felügyelt platformon futó Apache Spark.

## <a name="databricks-notebook-activity-definition"></a>Databricks Notebook activity definíció

Ez a minta JSON-definícióból Databricks Notebook tevékenység:

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
            }
        }
    }
}
```

## <a name="databricks-notebook-activity-properties"></a>Databricks Notebook tevékenység tulajdonságai

A következő táblázat a JSON-definícióból használt JSON-tulajdonságok:

|Tulajdonság|Leírás|Szükséges|
|---|---|---|
|név|A feldolgozási tevékenység nevét.|Igen|
|leírás|A tevékenység mit leíró szöveg.|Nem|
|type|Databricks Notebook tevékenység a tevékenységtípus DatabricksNotebook.|Igen|
|linkedServiceName|Amikor a Databricks notebook fut. Databricks társított szolgáltatás neve. A csatolt szolgáltatással kapcsolatos további tudnivalókért lásd: [összekapcsolt szolgáltatások számítási](compute-linked-services.md) cikk.|Igen|
|notebookPath|A Databricks munkaterületen futtatásához a notebook abszolút elérési útja. Ennek az elérési útnak törtvonallal kell kezdődnie.|Igen|
|baseParameters|Kulcs-érték párokból álló tömb. Alap paraméterek használhatók az egyes tevékenységek futtatásához. Ha a notebook paramétert nincs megadva, az alapértelmezett értéket a notebook használható. További információ a paraméterek [Databricks notebookok](https://docs.databricks.com/api/latest/jobs.html#jobsparampair).|Nem|
