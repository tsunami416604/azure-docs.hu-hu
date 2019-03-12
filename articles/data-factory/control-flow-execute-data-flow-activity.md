---
title: Data flow tevékenység végrehajtása az Azure Data Factoryban |} A Microsoft Docs
description: A végrehajtási adatok folyamat tevékenységeit futtatja adatfolyam-gyűjteményre.
services: data-factory
documentationcenter: ''
author: kromerm
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: makromer
ms.openlocfilehash: 4e661f838cc0b8e52ff72e4e44a43d611fa3355f
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2019
ms.locfileid: "57727382"
---
# <a name="execute-data-flow-activity-in-azure-data-factory"></a>Data flow tevékenység végrehajtása az Azure Data Factoryban
A végrehajtási adatok folyamat tevékenység használatával futtassa az ADF adatfolyama folyamatfuttatások hibakeresési (védőfal), és az aktivált folyamatfuttatások.

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="syntax"></a>Szintaxis

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "dataflow1",
         "type": "DataFlowReference"
      },
        "compute": {
          "computeType": "General",
          "coreCount": 8,
      }
}

```

## <a name="type-properties"></a>Tulajdonságok

* ```dataflow``` a végrehajtani kívánt adatok folyamat entitás neve
* ```compute``` a Spark-végrehajtási környezetben ismerteti
* ```coreCount``` a tevékenység végrehajtása az adatfolyam hozzárendelése magok száma

![Hajtsa végre az adatfolyam](media/data-flow/activity-data-flow.png "végrehajtása az adatfolyam")

### <a name="run-on"></a>Futtatás a következőn:

Válassza ki a számítási környezetet Ez az adatfolyam végrehajtásához. Az alapértelmezett érték az Azure automatikus feloldása alapértelmezett integrációs modul. Ez a választás végrehajtja az adatok az adatfolyam a Spark-környezetben az adat-előállító ugyanabban a régióban. A számítási típus lesz, ami azt jelenti, hogy a számítási környezet több percet vesz igénybe az indítási feladat fürt.

### <a name="compute-type"></a>Számítási típus

Választhat, általános célú, Memóriaoptimalizált Compute vagy az optimalizált memóriájú, attól függően, hogy az adatfolyama követelményeinek.

### <a name="core-count"></a>Magok száma

Válassza ki, hány magunk szeretne hozzárendelni a feladathoz. A kisebb feladatokat kevesebb maggal jobban működnek.

### <a name="staging-area"></a>Átmeneti terület

Ha az adatok Azure Data Warehouse-ba is elhelyezés, választania kell a Polybase kötegelt betöltés előkészítési helyet.

## <a name="parameterized-datasets"></a>A paraméteres adatkészletek

Paraméteres adatkészleteket használja, ha mindenképpen állítsa be a paraméterértékeket.

![Hajtsa végre a Flow Adatparaméterek](media/data-flow/params.png "paraméterek")

### <a name="debugging-parameterized-data-flows"></a>Hibakeresési paraméteres adatfolyamok

Csak a paraméteres adatkészletekkel való futtatásához a végrehajtási adatok folyamat tevékenységgel folyamat hibakeresési adatfolyamok is hibakeresési. Az ADF adatfolyam interaktív hibakeresési munkamenetek jelenleg nem működik a paraméteres adatkészletek. Folyamat-végrehajtás és hibakeresési futtatások paraméterekkel együtt fog működni.

## <a name="next-steps"></a>További lépések
Tekintse meg a többi Data Factory által támogatott átvitelvezérlési tevékenységek: 

- [If Condition tevékenység](control-flow-if-condition-activity.md)
- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden egyes tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
- [Until tevékenység](control-flow-until-activity.md)
