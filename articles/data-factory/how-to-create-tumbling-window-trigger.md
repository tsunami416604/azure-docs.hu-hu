---
title: Az Azure Data Factory átfedésmentes ablak eseményindítók létrehozása |} Microsoft Docs
description: Megtudhatja, hogyan lehet indítót létrehozni az Azure Data Factoryben, amely egy folyamat fut egy átfedésmentes ablak.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: shlo
ms.openlocfilehash: 312072a5de21ff1c6b602fed93b77c564b15a9f1
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Hozzon létre egy eseményindítót, amely egy folyamat fut egy átfedésmentes ablak
Ez a cikk lépéseit létrehozása, indítsa el, és figyelheti a átfedésmentes ablak eseményindítót. Eseményindítók és a támogatott típusok kapcsolatos általános információkért lásd: [csővezeték-végrehajtási és eseményindítók](concepts-pipeline-execution-triggers.md).

> [!NOTE]
> Ez a cikk az Azure Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha szeretne megtudni az Azure Data Factory 1-es verziójú, amely általánosan elérhető (GA), lásd: [Ismerkedés az Azure Data Factory 1-es verziójú](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Az átfedésmentes ablakos eseményindítók olyan eseményindítók, amelyek rendszeres időközönként aktiválódnak a megadott kezdési időponttól kezdve, az állapot megőrzése mellett. Az átfedésmentes ablakok rögzített méretű, egymást nem fedő és összefüggő időintervallumok. Átfedésmentes ablak eseményindító egy folyamatot egy az egyhez típusú kapcsolattal rendelkezik, és csak egy szinguláris folyamat hivatkozhat.

## <a name="tumbling-window-trigger-type-properties"></a>Átfedésmentes ablak indítási típus tulajdonságokat.
Egy átfedésmentes ablak a következő indítási típus tulajdonságokkal rendelkeznek:

```  
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": "<<Started/Stopped/Disabled - readonly>>",
        "typeProperties": {
            "frequency": "<<Minute/Hour>>",
            "interval": <<int>>,
            "startTime": "<<datetime>>",
            "endTime: "<<datetime – optional>>"",
            "delay": "<<timespan – optional>>",
            “maxConcurrency”: <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count":  <<int - optional, default: 0>>,
                “intervalInSeconds”: <<int>>,
            }
        },
        "pipeline":
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "MyPipelineName"
                },
                "parameters": {
                    "parameter1": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    },
                    "parameter2": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    },
                    "parameter3": "https://mydemo.azurewebsites.net/api/demoapi"
                }
            }
      }    
}
```  

A következő táblázat a fő JSON-elemek szerepelnek, amelyek kapcsolódnak ismétlődési és ütemezési átfedésmentes ablak eseményindító magas szintű áttekintést nyújt:

| JSON-elem | Leírás | Típus | Megengedett értékek | Szükséges |
|:--- |:--- |:--- |:--- |:--- |
| **Típusa** | Az eseményindító típusa. A típus a rögzített érték "TumblingWindowTrigger." | Karakterlánc | "TumblingWindowTrigger" | Igen |
| **runtimeState** | A jelenlegi állapotában az eseményindító futási időt.<br/>**Megjegyzés:**: Ez az elem \<readOnly >. | Karakterlánc | "Lépések", "leállt," "Letiltva" | Igen |
| **frequency** | A gyakoriság egységet (perc vagy óra), az eseményindító ismét előfordul jelölő karakterlánccá. Ha a **startTime** dátum értékei részletesebben, mint a **gyakoriság** érték, a **startTime** dátumok minősülnek, ha az ablak határok arra az esetre vonatkoznak. Például ha a **gyakoriság** értéke óránkénti és a **startTime** értéke 2016-04-01T10:10:10Z, az első ablakban van (2017-09-01T10:10:10Z, 2017-09-01T11:10:10Z). | Karakterlánc | "minute", "hour"  | Igen |
| **interval** | Pozitív egész szám, amely az eseményindító futásának gyakoriságát meghatározó **frequency** érték időközét jelöli. Például ha a **időköz** 3 és a **gyakoriság** "óra," az eseményindító 3 óránként ismétlődik. | Egész szám | Egy pozitív egész szám. | Igen |
| **startTime**| A első előfordulása, amely az elmúlt lehet. Az első eseményindító időköz (**startTime**, **startTime** + **időköz**). | DateTime | Egy DateTime értéket. | Igen |
| **endTime**| A utolsó előfordulása, amely ezelőtti lehet. | DateTime | Egy DateTime értéket. | Igen |
| **delay** | Adatok feldolgozása a időszak kezdete késleltetés mennyisége. A folyamat futtatása után a várt végrehajtási ideje és mennyisége elindult **késleltetés**. A **késleltetés** határozza meg, hogy az eseményindító meddig ahhoz, hogy kiváltsa egy új futtató esedékes időn túli. A **késedelem** nem módosítható a az ablak **startTime**. Például egy **késleltetés** 00:10:00 érték azt jelenti, 10 percig várnia. | Időtartomány  | A time értéknek, ahol az alapértelmezett érték 00:00:00. | Nem |
| **maxConcurrency** | Egyidejű eseményindító fut, amely készen áll a Windows kiváltó száma. Például biztonsági kitöltés óránként fut 24 windows tegnap eredményez. Ha **maxConcurrency** = eseményindító események elindulása esetén csak az első 10 windows 10 (00:00-01:00 - 09:00 – 10:00). Az első 10 indított folyamat fut befejezését követően elindulása eseményindító futtatása esetén a következő 10 Windows (10:00 – 11:00 – 19:00 – 20:00). Ebben a példában a folytatása **maxConcurrency** = 10, készen áll, nincsenek 10 teljes folyamat fut a windows 10 esetén. Ha csak 1 ablak készen áll, nincs csak 1 folyamatot futtató. | Egész szám | Egy egész számot 1 és 50 között. | Igen |
| **a retryPolicy: száma** | A folyamat futtatása előtt az újrapróbálkozások száma van megjelölve, "Sikertelen".  | Egész szám | Egy egész számot, ahol az alapértelmezett érték a 0 (nincs újrapróbálás). | Nem |
| **a retryPolicy: intervalInSeconds** | A késleltetés másodpercben megadott újrapróbálkozási kísérletek között. | Egész szám | Hány másodpercig, ahol az alapértelmezett érték 30. | Nem |

### <a name="windowstart-and-windowend-system-variables"></a>WindowStart és WindowEnd rendszerváltozók

Használhatja a **WindowStart** és **WindowEnd** rendszerváltozók az átfedésmentes ablak eseményindító a **csővezeték** definition (Ez azt jelenti, hogy a lekérdezés rész). A rendszer változók paraméterekként átadása a kimenetátirányítási mechanizmusával a **eseményindító** definíciója. A következő példa bemutatja, hogyan változókhoz továbbítsa paraméterként:

```  
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
            ...
        "pipeline":
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "MyPipelineName"
                },
                "parameters": {
                    "MyWindowStart": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    },
                    "MyWindowEnd": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    }
                }
            }
      }    
}
```  

Használatához a **WindowStart** és **WindowEnd** rendszer változó értékeit az adatcsatorna-definícióban, használja a "MyWindowStart" és "MyWindowEnd" paramétereket, ennek megfelelően.

### <a name="execution-order-of-windows-in-a-backfill-scenario"></a>Windows visszatöltési esetén végrehajtási sorrendje
Ha több windows feliratkozott végrehajtási (különösen a visszatöltési esetén), a végrehajtás sorrendje a windows nincs determinisztikus, a legújabb legrégebbi időszakok közül. Jelenleg ez a viselkedés nem módosítható.

### <a name="existing-triggerresource-elements"></a>Meglévő TriggerResource elemek
A következők vonatkoznak meglévő **TriggerResource** elemei:

* Ha az érték a **gyakoriság** elemet (vagy ablakméret) eseményindító módosításait, a windows már feldolgozott állapota *nem* alaphelyzetbe állítása. Az eseményindító továbbra is fennáll, az érvényesítést a Windows új ablak mérete használatával végrehajtott utolsó ablakból.
* Ha értéke a **endTime** eseményindító változtatás (hozzáadott vagy frissített), a windows állapotát már feldolgozott elem *nem* alaphelyzetbe állítása. Az eseményindító eleget tegyen az új **endTime** érték. Ha az új **endTime** értéke előtt a windows, amely már végre az eseményindító leáll. Ellenkező esetben az eseményindító leáll, ha az új **endTime** értéket a rendszer észlelt.

## <a name="sample-for-azure-powershell"></a>Az Azure PowerShell minta
Ez a szakasz bemutatja, hogyan használhatja az Azure Powershellt létrehozását, indítsa el, és egy eseményindító megfigyelését.

1. Hozzon létre egy JSON fájlt **MyTrigger.json** a C:\ADFv2QuickStartPSH\ mappában, a következő tartalommal:

   > [!IMPORTANT]
   > A JSON-fájl mentése előtt állítsa be az értékét a **startTime** elem a jelenlegi UTC-időre. Állítsa a **endTime** korábbi a jelenlegi UTC-idő egy órával elemet.

    ```json   
    {
      "name": "PerfTWTrigger",
      "properties": {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
          "frequency": "Minute",
          "interval": "15",
          "startTime": "2017-09-08T05:30:00Z",
          "delay": "00:00:01",
          "retryPolicy": {
            "count": 2,
            "intervalInSeconds": 30
          },
          "maxConcurrency": 50
        },
        "pipeline": {
          "pipelineReference": {
            "type": "PipelineReference",
            "referenceName": "DynamicsToBlobPerfPipeline"
          },
          "parameters": {
            "windowStart": "@trigger().outputs.windowStartTime",
            "windowEnd": "@trigger().outputs.windowEndTime"
          }
        },
        "runtimeState": "Started"
      }
    }
    ```  

2. Egy eseményindító létrehozása a **Set-AzureRmDataFactoryV2Trigger** parancsmagot:

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
    
3. Győződjön meg arról, hogy az állapot az eseményindító **leállítva** használatával a **Get-AzureRmDataFactoryV2Trigger** parancsmagot:

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Az eseményindító használatával indítsa el a **Start-AzureRmDataFactoryV2Trigger** parancsmagot:

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Győződjön meg arról, hogy az állapot az eseményindító **elindítva** használatával a **Get-AzureRmDataFactoryV2Trigger** parancsmagot:

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6. Az Azure PowerShell használatával futtat az eseményindító Get a **Get-AzureRmDataFactoryV2TriggerRun** parancsmag. Lekérése az eseményindító fut, rendszeres időközönként hajtsa végre a következő parancsot. Frissítés a **TriggerRunStartedAfter** és **TriggerRunStartedBefore** az eseményindító-definícióban szereplő értékekre értékeket:

    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
Az Azure portálon futó eseményindító futtatása és a folyamat figyelése című [figyelő folyamat fut](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="next-steps"></a>További lépések
Eseményindítók kapcsolatos részletes információkért lásd: [csővezeték-végrehajtási és eseményindítók](concepts-pipeline-execution-triggers.md#triggers).
