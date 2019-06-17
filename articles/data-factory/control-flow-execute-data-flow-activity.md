---
title: Data flow tevékenység végrehajtása az Azure Data Factoryban |} A Microsoft Docs
description: Hogyan hajtható végre az adatok jut el a data factory-folyamatok belül.
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: makromer
ms.openlocfilehash: e75c6290474d876ca22b5888d06b1fc0e4c8cd05
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077322"
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

### <a name="debugging-pipelines-with-data-flows"></a>Hibakeresési adatfolyam-gyűjteményre folyamatok

![Hibakeresési gomb](media/data-flow/debugbutton.png "hibakeresési gomb")

A Data Flow Debug használatával az adatfolyamok egy folyamat hibakeresési futtassa interaktív módon tesztelése egy fűtéssel fürtöt használják. A folyamat Debug lehetőséget használva belül egy folyamatot az adatok folyamatok tesztelése.

### <a name="run-on"></a>Futtatás

Ez a mező kitöltése kötelező, amely meghatározza, mely az adatfolyam tevékenység-végrehajtási használandó integrációs modul. Alapértelmezés szerint a Data Factory az alapértelmezett automatikus feloldása Azure integrációs modul fogja használni. A saját Azure integrációs modulok, amelyek adott régiók definiálása, típusát, a magszámot, és a TTL számítási az adatokat a folyamat tevékenységek végrehajtását a is létrehozhat.

Az adatfolyam-végrehajtások alapértelmezett érték 60 perc TTL általános számítási 8 maggal.

Válassza ki a számítási környezetet Ez az adatfolyam végrehajtásához. Az alapértelmezett érték az Azure automatikus feloldása alapértelmezett integrációs modul. Ez a választás végrehajtja az adatok az adatfolyam a Spark-környezetben az adat-előállító ugyanabban a régióban. A számítási típus lesz, ami azt jelenti, hogy a számítási környezet több percet vesz igénybe az indítási feladat fürt.

Az adatfolyam-tevékenységek a Spark-végrehajtási környezet felett van. Az a [Azure integrációs modul](concepts-integration-runtime.md) (általános célú, memóriahasználatra optimalizált és számításra optimalizált) számítási típus beállítása a feldolgozó magok száma, és a time-to-live megfelelően a végrehajtó motor az adatfolyam-számítási beállítások követelmények. Emellett TTL beállítása lehetővé teszi azonnal elérhetővé válik a feladatvégrehajtások meleg fürtben fenntartását.

![Azure integrációs modul](media/data-flow/ir-new.png "Azure integrációs modul")

> [!NOTE]
> Az Integration Runtime kijelölés adatfolyam tevékenységbe csak érvényes *végrehajtások aktivált* a folyamat. Hibakeresés a folyamatot az adatok elkezdenek beérkezni a hibakeresési lesz az alapértelmezett 8 magos Spark-fürtön hajtja végre.

### <a name="staging-area"></a>Átmeneti terület

Ha az adatok Azure Data Warehouse-ba is elhelyezés, választania kell a Polybase kötegelt betöltés előkészítési helyet. Az előkészítési beállításokat, amelyek csak az Azure Data Warehouse számítási feladatok alkalmazhatók.

## <a name="parameterized-datasets"></a>A paraméteres adatkészletek

Paraméteres adatkészleteket használja, ha mindenképpen állítsa be a paraméterértékeket.

![Hajtsa végre a Flow Adatparaméterek](media/data-flow/params.png "paraméterek")

### <a name="debugging-parameterized-data-flows"></a>Hibakeresési paraméteres adatfolyamok

Csak a paraméteres adatkészletekkel való futtatásához a végrehajtási adatok folyamat tevékenységgel folyamat hibakeresési adatfolyamok is hibakeresési. Az ADF adatfolyam interaktív hibakeresési munkamenetek jelenleg nem működik a paraméteres adatkészletek. Folyamat-végrehajtás és hibakeresési futtatások paraméterekkel együtt fog működni.

Bevált gyakorlat, hogy hozhat létre a statikus adatkészlet adatfolyamait, hogy teljes oszlop propagálás elérhető tervezés időpontjában. Ezután cserélje le a statikus adatkészlet dinamikus paraméteres adatkészlet, a flow adatfolyamat üzembe helyezése során.

## <a name="next-steps"></a>További lépések
Tekintse meg a többi Data Factory által támogatott átvitelvezérlési tevékenységek: 

- [If Condition tevékenység](control-flow-if-condition-activity.md)
- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden egyes tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
- [Until tevékenység](control-flow-until-activity.md)
