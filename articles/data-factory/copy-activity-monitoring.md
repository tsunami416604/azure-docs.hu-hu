---
title: Másolási tevékenység figyelése
description: Megtudhatja, hogyan figyelheti a másolási tevékenység végrehajtását az Azure Data Factoryban.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: jingwang
ms.openlocfilehash: 6494352bf957af83b45488493bf12a094c730c09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79125757"
---
# <a name="monitor-copy-activity"></a>Másolási tevékenység figyelése

Ez a cikk ismerteti, hogyan figyelheti a másolási tevékenység végrehajtását az Azure Data Factoryban. A [másolási tevékenység áttekintése](copy-activity-overview.md) cikkre épül, amely a másolási tevékenység általános áttekintését mutatja be.

## <a name="monitor-visually"></a>Vizuális megfigyelés

Miután létrehozott és közzétett egy folyamatot az Azure Data Factoryban, társíthatja azt egy eseményindítóval, vagy manuálisan elindíthategy ad hoc futtatást. Az Azure Data Factory felhasználói élményében az összes folyamat natív módon figyelheti. Ismerje meg az Azure Data Factory figyelését általában a [Vizuálisan figyelheti az Azure Data Factory-ból.](monitor-visually.md)

A Másolási tevékenység futtatásának figyeléséhez nyissa meg az adatgyár **szerzői & figyelési** felhasználói felületét. A **Figyelő** lapon megjelenik a folyamatfuttatások listája, kattintson a **folyamat névhivatkozására** a folyamatban lévő tevékenységi futtatások listájának eléréséhez.

![Másolási tevékenység futtatásának figyelése](./media/copy-activity-overview/monitor-pipeline-run.png)

Ezen a szinten láthatja a tevékenységbemenet, a kimenet és a hibák másolására mutató hivatkozásokat (ha a másolási tevékenység futtatása sikertelen), valamint statisztikákat, például időtartamot/állapotot. A **másolási** tevékenység neve melletti Részletek gombra (szemüveg) kattintva részletes részleteket talál a másolási tevékenység végrehajtásáról. 

![Másolási tevékenység futtatásának figyelése](./media/copy-activity-overview/monitor-copy-activity-run.png)

Ebben a grafikus figyelési nézetben az Azure Data Factory bemutatja a másolási tevékenység végrehajtási adatait, beleértve az adatok olvasási/írásbeli kötetét, a forrásból a fogadóba másolt fájlok/adatsorok számát, az átviteli csatornát, a másolási forgatókönyvhöz alkalmazott konfigurációkat, a másolási tevékenység lépéseit a megfelelő időtartamokkal és részletekkel, és így tovább. Tekintse meg ezt a [táblázatot](#monitor-programmatically) az egyes lehetséges metrikákról és annak részletes leírásáról. 

Bizonyos esetekben, amikor egy másolási tevékenység et futtat a Data Factory, a **"Teljesítményhangolási tippek"** a másolási tevékenység figyelési nézet tetején jelenik meg, amint az a példában látható. A tippek megmondja, hogy az ADF által az adott példány futtatása kor azonosított szűk keresztmetszetet, valamint javaslatot tesz arra, hogy mit kell módosítani a másolási átviteli szint növelése érdekében. További információ az [automatikus teljesítményhangolási tippekről.](copy-activity-performance-troubleshooting.md#performance-tuning-tips)

Az alsó **végrehajtási részletek és időtartamok** ismerteti a legfontosabb lépéseket a másolási tevékenység megy keresztül, ami különösen hasznos a másolási teljesítmény hibaelhárításához. A másolási futtatás szűk keresztmetszete a leghosszabb időtartamú. Tekintse meg [a másolási tevékenység teljesítményének hibaelhárítása](copy-activity-performance-troubleshooting.md) az egyes fázisok által képviselt információkat és a részletes hibaelhárítási útmutatót.

**Példa: Másolás az Amazon S3-ról az Azure Data Lake Storage Gen2 szolgáltatásba**

![A másolási tevékenység futtatásának részleteinek figyelése](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="monitor-programmatically"></a>Monitor programozott módon

Másolási tevékenység végrehajtási részleteket és a teljesítmény jellemzőit is visszaadja a **Másolási tevékenység futtatási eredmény** > **kimenete** szakaszban, amely a felhasználói felület figyelési nézet megjelenítésére szolgál. A következőkben a visszaadható tulajdonságok teljes listája látható. Csak a másolási forgatókönyvhöz megfelelő tulajdonságok jelennek meg. A tevékenységfuttatások programozott módon való figyeléséről az [Azure-adatgyár programozott figyelése](monitor-programmatically.md)című témakörben talál további információt.

| Tulajdonság neve  | Leírás | Egység a kimenetben |
|:--- |:--- |:--- |
| dataRead | A forrásból beolvasott adatok tényleges mennyisége. | Int64 érték bájtban |
| dataWritten (adatírás | A fogadóba írt/véglegesített adatok tényleges csatlakoztatása. A méret eltérhet a mérettől, `dataRead` mivel az egyes adattárak adatainak tárolására vonatkozik. | Int64 érték bájtban |
| fájlokOlvassa | A fájlalapú forrásból beolvasott fájlok száma. | Int64 érték (egység nélkül) |
| fájlokMegírt | A fájlalapú fogadóba írt/véglegesített fájlok száma. | Int64 érték (egység nélkül) |
| sourcePeakConnections | A forrásadattárhoz a Másolás tevékenység futtatása során létrehozott egyidejű kapcsolatok maximális száma. | Int64 érték (egység nélkül) |
| sinkPeakConnections | A fogadó adattárához a Másolás tevékenység futtatása során létrehozott egyidejű kapcsolatok maximális száma. | Int64 érték (egység nélkül) |
| sorokOlvasás | A forrásból beolvasott sorok száma (bináris másolat esetén nem alkalmazható). | Int64 érték (egység nélkül) |
| sorokmásolása | A fogadóba másolt sorok száma (bináris másolás esetén nem alkalmazható). | Int64 érték (egység nélkül) |
| rowsSkiped (Kihagyott) sor | A kihagyott inkompatibilis sorok száma. A nem kompatibilis sorok kihagyását `enableSkipIncompatibleRow` a true beállítással engedélyezheti. | Int64 érték (egység nélkül) |
| copyDuration | A másolási futtatás időtartama. | Int32 érték másodpercben |
| korlátozások | Adatátviteli sebesség. | Lebegőpontos szám, KBps-ben |
| sourcePeakConnections | A forrásadattárhoz a Másolás tevékenység futtatása során létrehozott egyidejű kapcsolatok maximális száma. | Int32 érték (egység nélkül) |
| sinkPeakConnections| A fogadó adattárához a Másolás tevékenység futtatása során létrehozott egyidejű kapcsolatok maximális száma.| Int32 érték (egység nélkül) |
| sqlDwPolyBase | Azt jelzi, hogy a polybase-t használja-e a rendszer az SQL Data Warehouse-ba történő másoláskor. | Logikai |
| redshiftUnload | Azt jelzi, hogy a rendszer a rendszer az IRAK OT használja-e az adatok Redshift ből történő másolásakor. | Logikai |
| hdfsDistcp | Azt jelzi, hogy a DistCp-t használja-e a rendszer, amikor adatokat másol a HDFS fájlrendszerből. | Logikai |
| effectiveIntegrationRuntime | A tevékenység futtatásához használt integrációs futásidejű (IR) vagy `<IR name> (<region if it's Azure IR>)`futásidők formátumban. | Szöveg (karakterlánc) |
| usedDataIntegrationUnits | A hatékony adatintegrációs egységek a másolás során. | Int32 érték |
| használtParallelCopies | A hatékony párhuzamosMásolatok másolás közben. | Int32 érték |
| átirányításRowPath | A `redirectIncompatibleRowSettings` tulajdonságban konfigurált blobstorage kihagyott inkompatibilis sorainaplójának elérési útja. Lásd [Hibatűrés](copy-activity-overview.md#fault-tolerance). | Szöveg (karakterlánc) |
| executionDetails | További részletek a másolási tevékenység fázisairól, valamint a megfelelő lépésekről, időtartamokról, konfigurációkról stb. Nem javasoljuk, hogy elemezd ezt a szakaszt, mert változhat. A másolási teljesítmény megértésének és hibaelhárításának jobb megértéséhez és hibaelhárításához olvassa el a Vizuális figyelés című [szakaszt.](#monitor-visually) | Tömb |
| perfRecommendation | Teljesítményhangolási tippek másolása. A részletekért olvassa el a [Teljesítményhangolási tippeket.](copy-activity-performance-troubleshooting.md#performance-tuning-tips) | Tömb |

**Példa:**

```json
"output": {
    "dataRead": 1180089300500,
    "dataWritten": 1180089300500,
    "filesRead": 110,
    "filesWritten": 110,
    "sourcePeakConnections": 640,
    "sinkPeakConnections": 1024,
    "copyDuration": 388,
    "throughput": 2970183,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
    "usedDataIntegrationUnits": 128,
    "billingReference": "{\"activityType\":\"DataMovement\",\"billableDuration\":[{\"Managed\":11.733333333333336}]}",
    "usedParallelCopies": 64,
    "executionDetails": [
        {
            "source": {
                "type": "AmazonS3"
            },
            "sink": {
                "type": "AzureBlobFS",
                "region": "East US",
                "throttlingErrors": 6
            },
            "status": "Succeeded",
            "start": "2020-03-04T02:13:25.1454206Z",
            "duration": 388,
            "usedDataIntegrationUnits": 128,
            "usedParallelCopies": 64,
            "profile": {
                "queue": {
                    "status": "Completed",
                    "duration": 2
                },
                "transfer": {
                    "status": "Completed",
                    "duration": 386,
                    "details": {
                        "listingSource": {
                            "type": "AmazonS3",
                            "workingDuration": 0
                        },
                        "readingFromSource": {
                            "type": "AmazonS3",
                            "workingDuration": 301
                        },
                        "writingToSink": {
                            "type": "AzureBlobFS",
                            "workingDuration": 335
                        }
                    }
                }
            },
            "detailedDurations": {
                "queuingDuration": 2,
                "transferDuration": 386
            }
        }
    ],
    "perfRecommendation": [
        {
            "Tip": "6 write operations were throttled by the sink data store. To achieve better performance, you are suggested to check and increase the allowed request rate for Azure Data Lake Storage Gen2, or reduce the number of concurrent copy runs and other data access, or reduce the DIU or parallel copy.",
            "ReferUrl": "https://go.microsoft.com/fwlink/?linkid=2102534 ",
            "RuleName": "ReduceThrottlingErrorPerfRecommendationRule"
        }
    ],
    "durationInQueue": {
        "integrationRuntimeQueue": 0
    }
}
```

## <a name="next-steps"></a>További lépések
Lásd a többi Másolási tevékenység cikkeket:

\-[Tevékenység másolása – áttekintés](copy-activity-overview.md)

\-[Tevékenységteljesítmény másolása](copy-activity-performance.md)