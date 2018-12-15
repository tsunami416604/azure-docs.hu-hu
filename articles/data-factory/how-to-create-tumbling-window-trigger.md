---
title: Átfedésmentes ablakos eseményindítók létrehozása az Azure Data Factoryban |} A Microsoft Docs
description: Megtudhatja, hogyan hozhat létre egy eseményindítót az Azure Data Factory-folyamat futó átfedésmentes ablak.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: shlo
ms.openlocfilehash: 4f124be9ef2247ab91d1e968b4533297ee8dba02
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53437248"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Hozzon létre egy eseményindítót, amely futtatja a folyamatot egy átfedésmentes ablak
Ez a cikk létrehozása, indítása és monitorozása az átfedésmentes ablakos eseményindító lépéseit ismerteti. Eseményindítók és a támogatott típusok kapcsolatos általános információkért lásd: [folyamat-végrehajtás és eseményindítók](concepts-pipeline-execution-triggers.md).

Az átfedésmentes ablakos eseményindítók olyan eseményindítók, amelyek rendszeres időközönként aktiválódnak a megadott kezdési időponttól kezdve, az állapot megőrzése mellett. Az átfedésmentes ablakok rögzített méretű, egymást nem fedő és összefüggő időintervallumok. Átfedésmentes ablakos eseményindító-az-egyhez kapcsolatban áll egy folyamat, és csak egyetlen folyamat hivatkozhat.

## <a name="data-factory-ui"></a>A Data Factory felhasználói felülete

Átfedésmentes ablakos eseményindító létrehozása az Azure Portalon, válassza ki a **eseményindító > Átfedésmentes ablak > Tovább**, majd konfigurálja a tulajdonságait, amelyek meghatározzák az átfedésmentes ablak.

![Átfedésmentes ablakos eseményindító létrehozása az Azure Portalon](media/how-to-create-tumbling-window-trigger/create-tumbling-window-trigger.png)

## <a name="tumbling-window-trigger-type-properties"></a>Átfedésmentes ablak eseményindító-típus tulajdonságait
Átfedésmentes ablak rendelkezik a következő eseményindító tulajdonságait:

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

Az alábbi táblázat a fő JSON-elemek, amelyek kapcsolatos ismétlődés és ütemezés átfedésmentes ablakos eseményindító magas szintű áttekintést nyújt:

| JSON-elem | Leírás | Típus | Megengedett értékek | Szükséges |
|:--- |:--- |:--- |:--- |:--- |
| **type** | A trigger típusa. A típus a rögzített érték "TumblingWindowTrigger." | Karakterlánc | "TumblingWindowTrigger" | Igen |
| **runtimeState** | Az eseményindító-futtatás ideje aktuális állapotát.<br/>**Megjegyzés:**: Ez az elem \<readOnly >. | Karakterlánc | "Elindítva", "leállított," "Letiltva" | Igen |
| **frequency** | Az eseményindító ismétlődésének gyakorisági gyakoriság egysége (percek vagy órák) jelölő karakterláncot. Ha a **startTime** dátum értékek a következők részletesebben, mint a **gyakorisága** érték, a **startTime** dátumok számítanak, amikor számítja ki az ablak határok. Például ha a **gyakorisága** értéke óránként és a **startTime** értéke 2017-09-01T10:10:10Z, az első ablak van (2017-09-01T10:10:10Z, 2017-09-01T11:10:10Z). | Karakterlánc | "minute", "hour"  | Igen |
| **interval** | Pozitív egész szám, amely az eseményindító futásának gyakoriságát meghatározó **frequency** érték időközét jelöli. Például ha a **időköz** 3 és a **gyakorisága** "hour", akkor az eseményindító 3 óránként ismétlődik. | Egész szám | Pozitív egész szám. | Igen |
| **startTime**| Az első előfordulás, amely lehet múltbeli. Az első eseményindító időköz (**startTime**, **startTime** + **időköz**). | DateTime | Egy dátum/idő érték. | Igen |
| **endTime**| Az utolsó előfordulás, amely lehet múltbeli. | DateTime | Egy dátum/idő érték. | Igen |
| **delay** | Az adatfeldolgozási időszak kezdete késleltetés időtartama. A folyamat futtatása után a várt végrehajtási idő plusz a elindult **késleltetés**. A **késleltetés** határozza meg, mennyi ideig vár, a trigger új futtatását elindítása előtt az esedékes időn túli. A **késleltetés** nem módosítható az ablak **startTime**. Ha például egy **késleltetés** 00:10:00 érték azt jelenti, egy 10 perces késleltetést. | Időtartomány<br/>(ÓÓ)  | Egy timespan értékre, ahol az alapértelmezett beállítás 00:00:00. | Nem |
| **maxConcurrency** | A windows-készen áll a kiváltó egyidejű eseményindító-futtatások száma. Például a Háttérkitöltés óránként fut 24 windows tegnap eredményez. Ha **maxConcurrency** = 10, az eseményindító események csak az első 10 windows elindulása esetén (00:00-01:00 - 09:00-10:00). Az első 10 aktivált folyamatfuttatást befejezése után, az eseményindító-futtatások vannak aktivált a következő 10 Windows (10:00 – 11:00 – 19:00-20:00). Ebben a példában a folytatása **maxConcurrency** = 10, készen áll, nincsenek 10 teljes folyamatfuttatások a windows 10 esetén. Ha csak 1 ablak készen áll, nincs csak 1 folyamatfuttatás. | Egész szám | Egy egész számot 1 és 50 között. | Igen |
| **retryPolicy: Száma** | A folyamat futásának előtti újrapróbálkozások száma "Nem sikerült." van megjelölve.  | Egész szám | Egy egész számot, ahol az alapértelmezett érték a 0 (nincs újrapróbálkozás). | Nem |
| **retryPolicy: intervalInSeconds** | A másodpercben megadott újrapróbálkozási kísérletek közötti késleltetést. | Egész szám | A másodperc, ahol az alapértelmezett beállítás 30 száma. | Nem |

### <a name="windowstart-and-windowend-system-variables"></a>WindowStart és WindowEnd rendszerváltozók

Használhatja a **WindowStart** és **WindowEnd** átfedésmentes ablak eseményindítója esetén a rendszer változókat a **folyamat** definition (azt jelenti, a lekérdezés részeként). A rendszerváltozók paraméterek átadása a folyamat a **eseményindító** definíciója. Az alábbi példa bemutatja, hogyan adhatók át ezeket a változókat, paraméterek:

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

Használatához a **WindowStart** és **WindowEnd** rendszer változó értékeit a folyamat meghatározásának, használja a "MyWindowStart" és "MyWindowEnd" paramétereket, ennek megfelelően.

### <a name="execution-order-of-windows-in-a-backfill-scenario"></a>Visszatöltési forgatókönyvek esetében a Windows végrehajtásának sorrendje
Ha több windows regisztrálásához végrehajtási (különösen a visszatöltési forgatókönyvek esetében), a Windows végrehajtási sorrendje determinisztikus, a legrégebbitől a legújabbig időközönként. Jelenleg ez a viselkedés nem módosítható.

### <a name="existing-triggerresource-elements"></a>Meglévő TriggerResource elemek
A következő szempontokat alkalmazni a meglévő **TriggerResource** elemek:

* Ha az érték a **gyakorisága** elemet (vagy ablakméret) a trigger módosításokat, a windows már feldolgozott állapota *nem* alaphelyzetbe állítása. Az eseményindító aktiválódik a Windows-, az új ablak méretének használatával végrehajtott utolsó ablakból továbbra is.
* Ha az érték a **endTime** elem eseményindító változásokat (hozzáadott vagy frissített), a windows állapotának feldolgozása már *nem* alaphelyzetbe állítása. Az eseményindító figyelembe veszi az új **endTime** értéket. Ha az új **endTime** értéke előtt a windows-már tevékenységében, a trigger leáll. Ellenkező esetben az eseményindító leáll, ha az új **endTime** értéket észlelt.

## <a name="sample-for-azure-powershell"></a>Az Azure PowerShell-minta
Ez a szakasz bemutatja, hogyan hozhat létre, indítsa el, és a egy eseményindító figyelni az Azure PowerShell használatával.

1. Hozzon létre egy JSON-fájlt **MyTrigger.json** a C:\ADFv2QuickStartPSH\ mappában az alábbi tartalommal:

   > [!IMPORTANT]
   > A JSON-fájl mentése előtt az értékét állítsa be a **startTime** elem az aktuális UTC időpontig. Az értékét állítsa be a **endTime** elem az aktuális UTC időpontig elmúlt egy óra.

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

2. Az eseményindító létrehozása a **Set-AzureRmDataFactoryV2Trigger** parancsmagot:

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
    
3. Győződjön meg arról, hogy az eseményindító állapotának **leállítva** használatával a **Get-AzureRmDataFactoryV2Trigger** parancsmagot:

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Indítsa el az eseményindítót a használatával a **Start-AzureRmDataFactoryV2Trigger** parancsmagot:

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Győződjön meg arról, hogy az eseményindító állapotának **elindítva** használatával a **Get-AzureRmDataFactoryV2Trigger** parancsmagot:

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6. Az eseményindító fut. az Azure PowerShell használatával Get a **Get-AzureRmDataFactoryV2TriggerRun** parancsmagot. Információt szeretne kapni az eseményindító-futtatások, rendszeres időközönként hajtsa végre a következő parancsot. Frissítés a **TriggerRunStartedAfter** és **TriggerRunStartedBefore** értékeit, hogy megfeleljenek az eseményindító definíciójában szereplő értékeket:

    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
Eseményindító-futtatások és a folyamat figyelése fut, az Azure Portalon, tekintse meg [folyamatfuttatások monitorozása](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="next-steps"></a>További lépések
Részletes információkat lásd: [folyamat-végrehajtás és eseményindítók](concepts-pipeline-execution-triggers.md#triggers).
