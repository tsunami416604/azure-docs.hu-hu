---
title: Adatfolyam-tevékenység
description: Az adatfolyamatok végrehajtása egy adatfeldolgozó-folyamaton belül.
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: makromer
ms.date: 04/30/2020
ms.openlocfilehash: 1004f7fcc8ff93a170b724a6d8b1c2216b9c39b8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84726973"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Adatfolyam-tevékenység Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Az adatfolyam tevékenységgel átalakíthatja és áthelyezheti az adatait a leképezési adatfolyamatok használatával. Ha még nem ismeri az adatfolyamatokat, tekintse meg az [adatáramlás leképezése – áttekintés](concepts-data-flow-overview.md) című témakört.

## <a name="syntax"></a>Syntax

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
adatfolyam | A végrehajtandó adatfolyamra mutató hivatkozás | DataFlowReference | Yes
integrationRuntime | Az a számítási környezet, amelyen az adatfolyam fut. Ha nincs megadva, a rendszer az Azure Integration Runtime automatikus feloldását fogja használni. | IntegrationRuntimeReference | No
számítás. coreCount | A Spark-fürtben használt magok száma. Csak akkor adható meg, ha az Azure Integration Runtime automatikus feloldása használatban van | 8, 16, 32, 48, 80, 144, 272 | No
számítás. computeType | A Spark-fürtben használt számítási típus. Csak akkor adható meg, ha az Azure Integration Runtime automatikus feloldása használatban van | "Általános", "ComputeOptimized", "MemoryOptimized" | No
előkészítés. linkedService | Ha SQL DW-forrást vagy-fogadót használ, a rendszer a alapszintű előkészítéshez használt Storage-fiókot használja. | Linkedservicereference sématulajdonsággal | Csak akkor, ha az adatfolyam beolvas vagy ír egy SQL DW-t
előkészítés. folderPath | Ha SQL DW-forrást vagy-fogadót használ, a mappa elérési útja a blob Storage-fiókban | Sztring | Csak akkor, ha az adatfolyam beolvas vagy ír egy SQL DW-t

![Adatfolyam végrehajtása](media/data-flow/activity-data-flow.png "Adatfolyam végrehajtása")

### <a name="dynamically-size-data-flow-compute-at-runtime"></a>Adatáramlási számítások dinamikusan méretezése futásidőben

Az alapszám és a számítási típus tulajdonságainak beállítása dinamikusan beállítható úgy, hogy a bejövő forrásadatok mérete futásidőben is módosítható legyen. A forrás adatkészlet-adatok méretének megkereséséhez használjon olyan folyamat-tevékenységeket, mint a keresés vagy a metaadatok beolvasása. Ezután használja a dinamikus tartalom hozzáadása részt az adatfolyam tevékenység tulajdonságainál.

![Dinamikus adatfolyam](media/data-flow/dyna1.png "Dinamikus adatfolyam")

[Íme egy rövid videós oktatóanyag, amely leírja ezt a technikát](https://www.youtube.com/watch?v=jWSkJdtiJNM)

### <a name="data-flow-integration-runtime"></a>Adatfolyam-integrációs modul

Válassza ki, hogy melyik Integration Runtime szeretné használni az adatfolyam tevékenységének végrehajtásához. Alapértelmezés szerint a Data Factory az Azure Integration Runtime automatikus feloldását fogja használni négy munkavégző maggal, és nincs élettartam (TTL). Ez az IR általános célú számítási típussal rendelkezik, és ugyanabban a régióban fut, mint a gyár. Létrehozhat saját Azure-integrációs modulokat, amelyek meghatározott régiókat, számítási típusokat, alapszámokat és ÉLETTARTAMot határoznak meg az adatfolyam tevékenységének végrehajtásához.

A folyamatok végrehajtásához a fürt egy olyan fürt, amely a végrehajtás megkezdése előtt több percet vesz igénybe. Ha nem ad meg TTL-értéket, a rendszer ezt az indítási időt igényli minden folyamat futtatásához. Ha a TTL értéket adja meg, a meleg fürt a legutóbbi végrehajtás után megadott időre aktív marad, ami rövidebb indítási időt eredményez. Ha például 60 perc ÉLETTARTAMa van, és óránként egyszer futtat egy adatfolyamot, a fürtön marad aktív. További információ: [Azure Integration Runtime](concepts-integration-runtime.md).

![Azure Integration Runtime](media/data-flow/ir-new.png "Azure Integration Runtime")

> [!IMPORTANT]
> Az adatfolyam-tevékenység Integration Runtime kiválasztása csak a folyamat *aktivált végrehajtására* vonatkozik. A folyamat hibakeresése az adatfolyamatokkal a hibakeresési munkamenetben megadott fürtön fut.

### <a name="polybase"></a>PolyBase

Ha egy Azure SQL Data Warehouse fogadóként vagy forrásként használ, ki kell választania egy átmeneti helyet a Batch-köteg betöltéséhez. A Base lehetővé teszi a kötegek tömeges betöltését az adatsorok egymásba helyezése helyett. A Base drasztikusan csökkenti a betöltési időt az SQL DW-ben.

## <a name="parameterizing-data-flows"></a>Parameterizing-adatfolyamok

### <a name="parameterized-datasets"></a>Paraméteres adatkészletek

Ha az adatfolyam paraméteres adatkészleteket használ, állítsa be a paraméterek értékét a **Beállítások** lapon.

![Adatfolyam paramétereinek végrehajtása](media/data-flow/params.png "Paraméterek")

### <a name="parameterized-data-flows"></a>Paraméteres adatfolyamatok

Ha az adatfolyam paraméteres, állítsa be az adatfolyam paramétereinek dinamikus értékeit a **Parameters (paraméterek** ) lapon. A dinamikus vagy literális paraméterérték kiosztásához használhatja az ADF-folyamat kifejezésének nyelvét vagy az adatfolyam kifejezésének nyelvét is. További információ: adatfolyam- [Paraméterek](parameters-data-flow.md).

### <a name="parameterized-compute-properties"></a>Paraméteres számítási tulajdonságok.

Ha az Azure Integration Runtime automatikus feloldása és a számítási. coreCount és számítások értékeit adja meg, parametrizálja az alapvető darabszámot vagy a számítási típust.

![Példa az adatforgalom paraméterének végrehajtására](media/data-flow/parameterize-compute.png "Példa paraméterre")

## <a name="pipeline-debug-of-data-flow-activity"></a>Adatáramlási tevékenység folyamatának hibakeresése

Egy adatáramlási tevékenységgel futtatott hibakeresési folyamat végrehajtásához az adatfolyam-hibakeresési módot a felső sávon található **adatfolyam-hibakeresési** csúszka használatával kell bekapcsolni. A hibakeresési mód lehetővé teszi az adatfolyamok aktív Spark-fürtön való futtatását. További információ: [hibakeresési mód](concepts-data-flow-debug-mode.md).

![Hibakeresés gomb](media/data-flow/debugbutton.png "Hibakeresés gomb")

A hibakeresési folyamat az aktív hibakeresési fürtön fut, nem az adatáramlási tevékenység beállításaiban megadott integrációs futtatókörnyezeti környezettel. A hibakeresési mód indításakor kiválaszthatja a hibakeresés számítási környezetét.

## <a name="monitoring-the-data-flow-activity"></a>Az adatfolyam tevékenységének figyelése

Az adatfolyam-tevékenység speciális figyelési felülettel rendelkezik, ahol megtekintheti a particionálást, a szakasz időpontját és az adatvonal-információkat. Nyissa meg a figyelés panelt a **műveletek**területen található szemüvegek ikon használatával. További információ: [az adatfolyamatok figyelése](concepts-data-flow-monitoring.md).

### <a name="use-data-flow-activity-results-in-a-subsequent-activity"></a>Adatfolyam-tevékenységek eredményeinek használata egy későbbi tevékenységben

Az adatfolyam tevékenység az egyes fogadók számára írt sorok számát és az egyes forrásokból beolvasott sorokra vonatkozó mérőszámokat jeleníti meg. Ezek az eredmények a `output` tevékenység futtatási eredményének szakaszában lesznek visszaadva. A visszaadott metrikák az alábbi JSON formátumban jelennek meg.

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

Ha például a "sink1" nevű fogadóba írt sorok számát szeretné lekérni egy "dataflowActivity" nevű tevékenységben, használja a következőt: `@activity('dataflowActivity').output.runStatus.metrics.sink1.rowsWritten` .

A (z) "source1" nevű forrásból beolvasott sorok számának lekéréséhez használja a következőt: `@activity('dataflowActivity').output.runStatus.metrics.sink1.sources.source1.rowsRead` .

> [!NOTE]
> Ha a fogadó nulla sorból áll, akkor nem jelenik meg a mérőszámokban. A létezés ellenőrizhető a függvény használatával `contains` . Például `contains(activity('dataflowActivity').output.runStatus.metrics, 'sink1')` megvizsgálhatja, hogy a sorok sink1-e.

## <a name="next-steps"></a>További lépések

Lásd: Data Factory által támogatott vezérlési flow-tevékenységek: 

- [If Condition tevékenység](control-flow-if-condition-activity.md)
- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden egyes tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
- [Until tevékenység](control-flow-until-activity.md)
