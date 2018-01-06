---
title: "Átfedésmentes ablak eseményindítók létrehozása az Azure Data Factory |} Microsoft Docs"
description: "Megtudhatja, hogyan lehet indítót létrehozni az Azure Data Factoryben, amely egy folyamat fut egy átfedésmentes ablak."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: shlo
ms.openlocfilehash: a3b056ae4bb4eda26fec58ca3b6bed7f0744e36e
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/05/2018
---
# <a name="how-to-create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Egy folyamat futó átfedésmentes ablak eseményindító létrehozása
Ez a cikk lépéseit létrehozása, indítsa el, és figyelheti a átfedésmentes ablak eseményindítót. Eseményindítók és a támogatott típusok kapcsolatos általános információkért lásd: [csővezeték-végrehajtási és eseményindítók](concepts-pipeline-execution-triggers.md).

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory szolgáltatás általánosan elérhető 1. verzióját használja, lásd a [Data Factory 1. verzió használatának első lépéseit](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Átfedésmentes ablak eseményindítók olyan eseményindító, amely a megadott kezdési időponttal, állapot, megtartja a rendszeres időközönként következik be. Átfedésmentes windows rendszer rögzített méretű, nem átfedő és összefüggő időközök sorozata. Átfedésmentes ablak eseményindító 1:1 kapcsolatban áll egy folyamatot, és csak egy szinguláris folyamat hivatkozhat.

## <a name="tumbling-window-trigger-type-properties"></a>Átfedésmentes ablak indítási típus tulajdonságokat.
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

A következő táblázat a fő elemet átfedésmentes ablak eseményindító ütemezését és az ismétlődési kapcsolódnak magas szintű áttekintést nyújt.

| **JSON-név** | **Leírás** | **Megengedett értékek** | **Szükséges** |
|:--- |:--- |:--- |:--- |
| **típusa** | Az eseményindító-típusa. A rögzített "TumblingWindowTrigger." | Karakterlánc | Igen |
| **runtimeState** | <readOnly>A lehetséges értékek: Indítása, leállítása, le van tiltva | Karakterlánc | Igen, csak olvasható |
| **gyakoriság** |A *gyakoriság* karakterlánc, amely a gyakoriság egység az eseményindító ismét előfordul. Támogatott értékei a "legújabb" és "óra." A kezdési időpont részekből dátum, amelyek részletesebben, mint a gyakorisága, ha azok fog figyelembe kell venni az ablak határok kiszámításához. Az ex: Ha a gyakoriság nem óránkénti, és a kezdési időpont 2016-04-01T10:10:10Z, az első ablakban van (2017-09-01T10:10:10Z, 2017-09-01T11:10:10Z.)  | Karakterlánc. Támogatott típusok "minute", "hour" | Igen |
| **időköz** |A *időköz* pozitív egész szám, és azt jelzi, az időközt a *gyakoriság* , amely meghatározza, hogy milyen gyakran fusson az eseményindító. Például ha *időköz* 3 és *gyakoriság* "hour", akkor az eseményindító 3 óránként ismétlődik. | Egész szám | Igen |
| **Kezdő időpont**|*startTime* egy dátum-idő. *startTime* első és az elmúlt lehet. Az első eseményindító időköz (startTime, startTime + időköz) legyen. | DateTime | Igen |
| **Befejezés időpontja**|*endTime* egy dátum-idő. *endTime* utolsó és az elmúlt lehet. | DateTime | Igen |
| **késleltetés** | Adja meg a késleltetés az adatok feldolgozása a időszak kezdete előtt. A folyamat futtatása után a várt végrehajtási ideje + késleltetés el van indítva. Késleltetés a hogyan határozza meg az eseményindító meddig lejárt időt ahhoz, hogy kiváltsa új futtassa. Nem módosítható, az ablak indítási időpontja. | TimeSpan érték (Példa: 00:10:00, magában foglalja a késleltetést a 10 perc) |  Nem. Alapértelmezett érték "00: 00:00" |
| **maximális feldolgozási** | Egyidejű eseményindító fut, amely készen áll a Windows kiváltó száma. Példa: Ha keressük visszatöltési óránkénti a tegnap, amely lehet 24 windows. Ha a feldolgozási = eseményindító események elindulása esetén csak az első 10 windows 10 (00:00-01:00 - 09:00 – 10:00). Az első 10 indított folyamat fut befejezését követően elindulása eseményindító futtatása esetén a következő 10 (10:00 – 11:00 – 19:00 – 20:00). Párhuzamossági példát folytatva = 10, készen áll, nem lesznek 10 folyamat fut a windows 10 esetén. Ha csak 1 ablak készen áll, a csak lesz 1 folyamat futtatása. | Egész szám | Igen. A lehetséges értékek: 1-50 |
| **a retryPolicy: száma** | A folyamat futtatása előtt az újrapróbálkozások száma "Sikertelen" van megjelölve.  | Egész szám |  Nem. Alapértelmezett érték a 0-újrapróbálkozások |
| **a retryPolicy: intervalInSeconds** | Az újrapróbálkozási kísérletek közötti késleltetés, másodpercben | Egész szám |  Nem. Alapértelmezett érték 30 másodperc |

### <a name="using-system-variables-windowstart-and-windowend"></a>Rendszer változókkal: WindowStart és WindowEnd

Ha szeretné használni WindowStart és az átfedésmentes ablak eseményindító WindowEnd a **csővezeték** definition (azaz a rész a lekérdezések), át kell adnia a változók paraméterként a folyamat a a **eseményindító**definícióját, például így:
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

Ezt követően a csővezeték-definícióban használja az WindowStart és WindowEnd, használja a paraméterek ennek megfelelően "MyWindowStart" és "MyWindowEnd"

### <a name="notes-on-backfill"></a>Tudnivalók a visszatöltési
Ha több windows feliratkozott végrehajtási (esp. visszatöltési forgatókönyvben), Windows végrehajtás sorrendje determinisztikus, és a legújabb legrégebbitől időközönként. Nincs mód mostantól a működés megváltoztatásához.

### <a name="updating-an-existing-triggerresource"></a>Egy meglévő TriggerResource frissítése
* Gyakoriság (vagy az ablak mérete) az eseményindító módosul, ha windows állapotát feldolgozása már lesz *nem* alaphelyzetbe kell állítani. Az eseményindító indítási legutóbb hajtotta végre az új ablak mérete használata a Windows továbbra is.
* A trigger változik (hozzáadott vagy frissített) windows már állapotát befejezési időpontja feldolgozása lesz *nem* alaphelyzetbe kell állítani. Az eseményindító egyszerűen vesszük új befejezésének. Ha a befejező időpont előtt a windows már végre, az eseményindító leáll. Ellenkező esetben azt leáll, ha az új befejezési időpont a rendszer észlelt.

## <a name="sample-using-azure-powershell"></a>A minta Azure PowerShell használatával
Ez a szakasz bemutatja, hogyan használhatja az Azure Powershellt létrehozását, indítsa el, és egy eseményindító megfigyelését.

1. Hozzon létre egy JSON fájlt MyTrigger.json a C:\ADFv2QuickStartPSH\ mappában, a következő tartalommal:

   > [!IMPORTANT]
   > Állítsa be **startTime** a jelenlegi UTC-időre és **endTime** egy óra korábbi a jelenlegi UTC-idő a JSON-fájl mentése előtt.

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
2. Egy eseményindító létrehozása a **Set-AzureRmDataFactoryV2Trigger** parancsmag.

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    
3. Confirm that the status of the trigger is **Stopped** by using the **Get-AzureRmDataFactoryV2Trigger** cmdlet.

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
4. Az eseményindító használatával indítsa el a **Start-AzureRmDataFactoryV2Trigger** parancsmagot:

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
5. Győződjön meg arról, hogy az állapot az eseményindító **elindítva** használatával a **Get-AzureRmDataFactoryV2Trigger** parancsmag.

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
6.  Első eseményindító fut, a PowerShell használatával a **Get-AzureRmDataFactoryV2TriggerRun** parancsmag. Ahhoz, hogy az információkat az eseményindító fut, a következő parancsot rendszeresen: frissítés **TriggerRunStartedAfter** és **TriggerRunStartedBefore** az eseményindító-definícióban szereplő értékekre értékek .

    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
Eseményindító fut/folyamat fut az Azure portálon figyeléséről lásd: [figyelő folyamat fut](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)

## <a name="next-steps"></a>További lépések
Eseményindítók kapcsolatos részletes információkért lásd: [csővezeték-végrehajtási és eseményindítók](concepts-pipeline-execution-triggers.md#triggers).
