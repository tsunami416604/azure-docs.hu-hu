---
title: Adatfolyam-tevékenység
description: Adatfolyamatok végrehajtása adatfeldolgozó folyamatból.
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: makromer
ms.date: 03/16/2020
ms.openlocfilehash: 115cb3e499117457629e130b6432a1cbc2224edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79463050"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Adatfolyam-tevékenység az Azure Data Factoryban

Az adatfolyam-tevékenység segítségével átalakíthatja és áthelyezi az adatokat az adatfolyamok leképezésével. Ha még nem ért hozzá az adatfolyamok, olvassa el [az Adatfolyam leképezése – áttekintés című témakört.](concepts-data-flow-overview.md)

## <a name="syntax"></a>Szintaxis

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "MyDataFlow",
         "type": "DataFlowReference"
      },
      "compute": {
         "coreCount": 8,
         "computeType": "General"
      },
      "staging": {
          "linkedService": {
              "referenceName": "MyStagingLinkedService",
              "type": "LinkedServiceReference"
          },
          "folderPath": "my-container/my-folder"
      },
      "integrationRuntime": {
          "referenceName": "MyDataFlowIntegrationRuntime",
          "type": "IntegrationRuntimeReference"
      }
}

```

## <a name="type-properties"></a>Típus tulajdonságai

Tulajdonság | Leírás | Megengedett értékek | Kötelező
-------- | ----------- | -------------- | --------
adatfolyam | A végrehajtás alatt lévő adatfolyamra mutató hivatkozás | DataFlowReference | Igen
integrációRuntime | Az adatfolyam által futtatott számítási környezet. Ha nincs megadva, az automatikus feloldási Azure-integrációs runtime lesz használva | IntegrationRuntimeReference | Nem
compute.coreCount | A spark-fürtben használt magok száma. Csak akkor adható meg, ha az automatikus feloldási Azure-integrációs runtime-ot használja | 8, 16, 32, 48, 80, 144, 272 | Nem
compute.computeType típus | A spark-fürtben használt számítási típus. Csak akkor adható meg, ha az automatikus feloldási Azure-integrációs runtime-ot használja | "Általános", "ComputeOptimized", "MemoryOptimized" | Nem
staging.linkedService | SQL DW-forrás vagy -fogadó használata esetén a PolyBase átmeneti | LinkedServiceReference | Csak akkor, ha az adatfolyam beolvassa vagy írja az SQL DW
staging.folderPath | SQL DW-forrás vagy -fogadó használata esetén a Blob storage-fiókban lévő mappaelérési út a PolyBase átmeneti | Sztring | Csak akkor, ha az adatfolyam beolvassa vagy írja az SQL DW

![Adatfolyam végrehajtása](media/data-flow/activity-data-flow.png "Adatfolyam végrehajtása")

### <a name="dynamically-size-data-flow-compute-at-runtime"></a>Dinamikusan méretező adatfolyam-számítás futásidőben

A Core Count és a Compute Type tulajdonságok dinamikusan beállíthatók a bejövő forrásadatok méretéhez való igazításhoz futásidőben. A forrásadatkészlet-adatok méretének megkereséséhez használjon folyamattevékenységeket, például a Keresés vagy a Metaadatok bekésése. Ezután használja a Dinamikus tartalom hozzáadása az adatfolyam-tevékenység tulajdonságaiban.

![Dinamikus adatfolyam](media/data-flow/dyna1.png "Dinamikus adatfolyam")

[Itt van egy rövid videó bemutató elmagyarázza ezt a technikát](https://www.youtube.com/watch?v=jWSkJdtiJNM)

### <a name="data-flow-integration-runtime"></a>Adatfolyam-integráció futásideje

Válassza ki, hogy melyik integrációs futásidejűt használja az adatfolyam-tevékenység végrehajtásához. Alapértelmezés szerint a Data Factory fogja használni az automatikus feloldása Azure-integrációs futásidő négy feldolgozó magok és nincs ideje az élő (TTL). Ez az infravörös rendszer általános célú számítási típussal rendelkezik, és ugyanabban a régióban fut, mint a gyár. Létrehozhat saját Azure-integrációs runtimes, amelyek meghatározzák az adott régiók, számítási típus, alapvető száma, és a TTL az adatfolyam-tevékenység végrehajtása.

A folyamatvégrehajtások esetében a fürt egy feladatfürt, amely a végrehajtás megkezdése előtt néhány percet vesz igénybe. Ha nincs megadva TTL, akkor ez az indítási idő minden folyamat futtatásakor szükséges. Ha ttl-t ad meg, a meleg fürtkészlet az utolsó végrehajtás után megadott ideig aktív marad, ami rövidebb indítási időket eredményez. Ha például a TTL 60 perc, és óránként egyszer fut egy adatfolyamot, a fürtkészlet aktív marad. További információ: [Azure-integrációs runtime](concepts-integration-runtime.md).

![Azure-integrációs futásidejű](media/data-flow/ir-new.png "Azure-integrációs futásidejű")

> [!NOTE]
> Az integrációs futásidejű kiválasztása az adatfolyam-tevékenység ben csak a folyamat *aktivált végrehajtásai* vonatkozik. A folyamat adatfolyamokkal történő hibakeresése a hibakeresési munkamenetben megadott fürtön fut.

### <a name="polybase"></a>PolyBase

Ha egy Azure SQL Data Warehouse-t használ fogadóként vagy forrásként, ki kell választania egy átmeneti helyet a PolyBase kötegelt terheléshez. A PolyBase lehetővé teszi a kötegelt betöltést ömlesztve az adatok soronkénti betöltése helyett. A PolyBase drasztikusan csökkenti a betöltési időt az SQL DW-be.

## <a name="parameterizing-data-flows"></a>Adatfolyamok paraméterezése

### <a name="parameterized-datasets"></a>Paraméterezett adatkészletek

Ha az adatfolyam paraméterezett adatkészleteket használ, állítsa be a paraméterértékeket a **Beállítások** lapon.

![Adatfolyam-paraméterek végrehajtása](media/data-flow/params.png "Paraméterek")

### <a name="parameterized-data-flows"></a>Paraméterezett adatfolyamok

Ha az adatfolyam paraméterezett, állítsa be az adatfolyam paramétereinek dinamikus értékeit a **Paraméterek** lapon. Az ADF-folyamat kifejezés nyelvének (csak karakterlánctípusoknál) vagy az adatfolyam-kifejezés nyelvének használatával dinamikus vagy literális paraméterértékeket rendelhet hozzá. További információt az [Adatfolyam-paraméterek című témakörben talál.](parameters-data-flow.md)

![Példa az adatfolyam-paraméter végrehajtására](media/data-flow/parameter-example.png "Példa paraméterre")

### <a name="parameterized-compute-properties"></a>Paraméterezett számítási tulajdonságok.

Paraméterezheti a magszámát vagy a számítási típusát, ha az Azure-integráció automatikus feloldását használja, és értékeket ad meg a compute.coreCount és compute.computeType számára.

![Példa az adatfolyam-paraméter végrehajtására](media/data-flow/parameterize-compute.png "Példa paraméterre")

## <a name="pipeline-debug-of-data-flow-activity"></a>Az adatfolyam-tevékenység folyamatának hibakeresése

Ha adatfolyam-tevékenységgel futtatott hibakeresési folyamatot szeretne végrehajtani, a felső **sávadatfolyam-hibakeresési** csúszkáján keresztül be kell kapcsolnia az adatfolyam-hibakeresési módot. A hibakeresési mód lehetővé teszi az adatfolyam futtatását egy aktív Spark-fürtön. További információt a [Hibakeresési mód című témakörben talál.](concepts-data-flow-debug-mode.md)

![Hibakeresés gomb](media/data-flow/debugbutton.png "Hibakeresés gomb")

A hibakeresési folyamat az aktív hibakeresési fürtön fut, nem az adatfolyam-tevékenység beállításaiban megadott integrációs futásidejű környezetben. A hibakeresési mód indításakor kiválaszthatja a hibakeresési számítási környezetet.

## <a name="monitoring-the-data-flow-activity"></a>Az adatfolyam-tevékenység figyelése

Az adatfolyam-tevékenység egy speciális figyelési élményt, ahol megtekintheti a particionálás, a szakasz idő és az adatvonal-információk. Nyissa meg a figyelési ablaktáblát a szemüveg ikonon, a **Műveletek csoportban.** További információ: [Monitoring Data Flows](concepts-data-flow-monitoring.md).

### <a name="use-data-flow-activity-results-in-a-subsequent-activity"></a>Az adatfolyam-tevékenység eredményeinek használata egy későbbi tevékenységben

Az adatfolyam-tevékenység az egyes fogadókba írt sorok számát és az egyes forrásból beolvasott sorok számát mutató metrikákat ad ki. Ezek az eredmények `output` a tevékenység futtatási eredményének szakaszában kerülnek visszaadásra. A visszaadott mutatók az alábbi json formátumúak.

``` json
{
    "runStatus": {
        "metrics": {
            "<your sink name1>": {
                "rowsWritten": <number of rows written>,
                "sinkProcessingTime": <sink processing time in ms>,
                "sources": {
                    "<your source name1>": {
                        "rowsRead": <number of rows read>
                    },
                    "<your source name2>": {
                        "rowsRead": <number of rows read>
                    },
                    ...
                }
            },
            "<your sink name2>": {
                ...
            },
            ...
        }
    }
}
```

Ha például a "dataflowActivity" nevű tevékenység "sink1" nevű fogadóba írt `@activity('dataflowActivity').output.runStatus.metrics.sink1.rowsWritten`sorok számát szeretné megkapni, használja a használatát.

A fogadóban használt "source1" nevű forrásból beolvasott sorok számának leolvasásához használja a használatát. `@activity('dataflowActivity').output.runStatus.metrics.sink1.sources.source1.rowsRead`

> [!NOTE]
> Ha egy fogadó nulla sorokat írt, nem jelenik meg a metrikákban. A létezés a `contains` funkció segítségével ellenőrizhető. Például `contains(activity('dataflowActivity').output.runStatus.metrics, 'sink1')` ellenőrzi, hogy a sorok at sink1.For example, will check whether any rows were written to sink1.

## <a name="next-steps"></a>További lépések

Lásd: A Data Factory által támogatott vezérlési folyamat tevékenységek: 

- [If Condition tevékenység](control-flow-if-condition-activity.md)
- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden egyes tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresstevékenységet](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
- [Until tevékenység](control-flow-until-activity.md)
