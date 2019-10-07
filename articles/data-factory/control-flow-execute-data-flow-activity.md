---
title: Adatfolyam-tevékenység a Azure Data Factoryban | Microsoft Docs
description: Az adatfolyamatok végrehajtása egy adatfeldolgozó-folyamaton belül.
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: makromer
ms.openlocfilehash: 7db410e97046b6d251eb73e754e40eab09a2ee64
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981807"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Adatfolyam-tevékenység Azure Data Factory

Az adatfolyam tevékenységgel átalakíthatja és áthelyezheti az adatait a leképezési adatfolyamatok használatával. Ha még nem ismeri az adatfolyamatokat, tekintse meg az [adatáramlás leképezése – áttekintés](concepts-data-flow-overview.md) című témakört.

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

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

Tulajdonság | Leírás | Megengedett értékek | Szükséges
-------- | ----------- | -------------- | --------
adatfolyam | A végrehajtandó adatfolyamra mutató hivatkozás | DataFlowReference | Igen
integrationRuntime | Az a számítási környezet, amelyen az adatfolyam fut | IntegrationRuntimeReference | Igen
előkészítés. linkedService | Ha SQL DW-forrást vagy-fogadót használ, a rendszer a alapszintű előkészítéshez használt Storage-fiókot használja. | Linkedservicereference sématulajdonsággal | Csak akkor, ha az adatfolyam beolvas vagy ír egy SQL DW-t
előkészítés. folderPath | Ha SQL DW-forrást vagy-fogadót használ, a mappa elérési útja a blob Storage-fiókban | Sztring | Csak akkor, ha az adatfolyam beolvas vagy ír egy SQL DW-t

Adatfolyam-(media/data-flow/activity-data-flow.png "végrehajtási") ![folyamat végrehajtása]

### <a name="data-flow-integration-runtime"></a>Adatfolyam-integrációs modul

Válassza ki, hogy melyik Integration Runtime szeretné használni az adatfolyam tevékenységének végrehajtásához. Alapértelmezés szerint a Data Factory az Azure Integration Runtime és a négy munkavégző mag együttes használatát fogja használni, és nincs élettartam (TTL). Ez az IR általános célú számítási típussal rendelkezik, és ugyanabban a régióban fut, mint a gyár. Létrehozhat saját Azure-integrációs modulokat, amelyek meghatározott régiókat, számítási típusokat, alapszámokat és ÉLETTARTAMot határoznak meg az adatfolyam tevékenységének végrehajtásához.

A folyamatok végrehajtásához a fürt egy olyan fürt, amely a végrehajtás megkezdése előtt több percet vesz igénybe. Ha nem ad meg TTL-értéket, a rendszer ezt az indítási időt igényli minden folyamat futtatásához. Ha a TTL értéket adja meg, a meleg fürt a legutóbbi végrehajtás után megadott időre aktív marad, ami rövidebb indítási időt eredményez. Ha például 60 perc ÉLETTARTAMa van, és óránként egyszer futtat egy adatfolyamot, a fürtön marad aktív. További információ: [Azure Integration Runtime](concepts-integration-runtime.md).

![Azure Integration Runtime](media/data-flow/ir-new.png "Azure Integration Runtime")

> [!NOTE]
> Az adatfolyam-tevékenység Integration Runtime kiválasztása csak a folyamat *aktivált végrehajtására* vonatkozik. A folyamat hibakeresése az adatfolyamatokkal a hibakeresési munkamenetben megadott fürtön fut.

### <a name="polybase"></a>PolyBase

Ha egy Azure SQL Data Warehouse fogadóként vagy forrásként használ, ki kell választania egy átmeneti helyet a Batch-köteg betöltéséhez. A Base lehetővé teszi a kötegek tömeges betöltését az adatsorok egymásba helyezése helyett. A Base drasztikusan csökkenti a betöltési időt az SQL DW-ben.

## <a name="parameterizing-data-flows"></a>Parameterizing-adatfolyamok

### <a name="parameterized-datasets"></a>Paraméteres adatkészletek

Ha az adatfolyam paraméteres adatkészleteket használ, állítsa be a paraméterek értékét a **Beállítások** lapon.

![Az adatfolyam paramétereinek](media/data-flow/params.png "paramétereinek") végrehajtása

### <a name="parameterized-data-flows"></a>Paraméteres adatfolyamatok

Ha az adatfolyam paraméteres, állítsa be az adatfolyam paramétereinek dinamikus értékeit a **Parameters (paraméterek** ) lapon. Az ADF-folyamat kifejezésének nyelvét (csak karakterlánc-típusok esetében) vagy az adatfolyam kifejezésének nyelvét használhatja dinamikus vagy literális paraméterérték hozzárendeléséhez. További információ: adatfolyam- [Paraméterek](parameters-data-flow.md).

Példa ![az adatforgalom paraméterének](media/data-flow/parameter-example.png "paraméterére") példa

## <a name="pipeline-debug-of-data-flow-activity"></a>Adatáramlási tevékenység folyamatának hibakeresése

Egy adatáramlási tevékenységgel futtatott hibakeresési folyamat végrehajtásához az adatfolyam-hibakeresési módot a felső sávon található **adatfolyam-hibakeresési** csúszka használatával kell bekapcsolni. A hibakeresési mód lehetővé teszi az adatfolyamok aktív Spark-fürtön való futtatását. További információ: [hibakeresési mód](concepts-data-flow-debug-mode.md).

![Hibakeresés gomb](media/data-flow/debugbutton.png "hibakeresése gomb")

A hibakeresési folyamat az aktív hibakeresési fürtön fut, nem az adatáramlási tevékenység beállításaiban megadott integrációs futtatókörnyezeti környezettel. A hibakeresési mód indításakor kiválaszthatja a hibakeresés számítási környezetét.

## <a name="monitoring-the-data-flow-activity"></a>Az adatfolyam tevékenységének figyelése

Az adatfolyam-tevékenység speciális figyelési felülettel rendelkezik, ahol megtekintheti a particionálást, a szakasz időpontját és az adatvonal-információkat. Nyissa meg a figyelés panelt a **műveletek**területen található szemüvegek ikon használatával. További információ: [az adatfolyamatok figyelése](concepts-data-flow-monitoring.md).

## <a name="next-steps"></a>További lépések

Lásd: Data Factory által támogatott vezérlési flow-tevékenységek: 

- [If Condition tevékenység](control-flow-if-condition-activity.md)
- [Folyamat végrehajtása tevékenység](control-flow-execute-pipeline-activity.md)
- [Minden tevékenységhez](control-flow-for-each-activity.md)
- [Metaadatok beolvasása tevékenység](control-flow-get-metadata-activity.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [Webes tevékenység](control-flow-web-activity.md)
- [Until tevékenység](control-flow-until-activity.md)
