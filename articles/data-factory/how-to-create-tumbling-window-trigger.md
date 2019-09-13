---
title: Kieséses ablakos eseményindítók létrehozása a Azure Data Factoryban | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre olyan triggert a Azure Data Factoryban, amely egy folyamatot futtat egy kieséses ablakban.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 7600398d213748bdea9da5a483a8c10d486a8048
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/11/2019
ms.locfileid: "70915551"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Folyamatokat futtató trigger létrehozása egy kieséses ablakban
Ez a cikk a bukdácsoló ablakos triggerek létrehozásának, indításának és figyelésének lépéseit ismerteti. Az eseményindítókkal és a támogatott típusokkal kapcsolatos általános információkért lásd: [folyamat-végrehajtás és eseményindítók](concepts-pipeline-execution-triggers.md).

Az átfedésmentes ablakos eseményindítók olyan eseményindítók, amelyek rendszeres időközönként aktiválódnak a megadott kezdési időponttól kezdve, az állapot megőrzése mellett. Az átfedésmentes ablakok rögzített méretű, egymást nem fedő és összefüggő időintervallumok. A bukdácsoló ablakos trigger egy-az-egyhez kapcsolattal rendelkezik egy folyamattal, és csak egy külön folyamatra hivatkozhat.

## <a name="data-factory-ui"></a>A Data Factory felhasználói felülete

1. Ha a Data Factory felhasználói felületén létre szeretne hozni egy kieséses ablak-eseményindítót, válassza az **Eseményindítók** fület, majd válassza az **új**lehetőséget. 
1. Miután megnyitotta az aktiválási konfiguráció ablaktáblát, válassza a **kiesési ablak**lehetőséget, majd adja meg a kikapcsolt ablak triggerének tulajdonságait. 
1. Amikor elkészült, válassza a **Mentés** lehetőséget.

![Kieséses ablakos trigger létrehozása a Azure Portalban](media/how-to-create-tumbling-window-trigger/create-tumbling-window-trigger.png)

## <a name="tumbling-window-trigger-type-properties"></a>Kiváltott ablak típusú trigger tulajdonságai

A kiesési ablak a következő típusú trigger-tulajdonságokkal rendelkezik:

```
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": "<<Started/Stopped/Disabled - readonly>>",
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": "<<datetime>>",
            "endTime: <<datetime – optional>>,
            "delay": <<timespan – optional>>,
            “maxConcurrency”: <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                “intervalInSeconds”: <<int>>,
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan – optional>>,
                    "referenceTrigger": {
                        "referenceName": "MyTumblingWindowDependency1",
                        "type": "TriggerReference"
                    }
                },
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan>>
                }
            ]
        },
        "pipeline": {
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

Az alábbi táblázat áttekintést nyújt azokról a fő JSON-elemekről, amelyek a kieséses ablak eseményindítójának ismétlődésével és ütemezésével kapcsolatosak:

| JSON-elem | Leírás | Type | Megengedett értékek | Kötelező |
|:--- |:--- |:--- |:--- |:--- |
| **type** | Az trigger típusa. A típus a "TumblingWindowTrigger" rögzített érték. | String | "TumblingWindowTrigger" | Igen |
| **runtimeState** | Az trigger futási idejének jelenlegi állapota.<br/>**Megjegyzés**: Ez az elem \<ReadOnly >. | String | "Started", "leállítva", "Letiltva" | Igen |
| **frequency** | Az a gyakorisági egység (perc vagy óra) jelölő sztring, amelynél az trigger ismétlődik. Ha a **kezdő** időpontok értékének részletessége nagyobb, mint a **gyakoriság** értéke, a rendszer a **kezdő** időpontokat veszi figyelembe az ablak határainak kiszámításakor. Ha például a **gyakoriság** értéke óránként, a **kezdő időpont** pedig 2017-09-01T10:10:10Z, az első ablak a következő: (2017-09-01T10:10:10Z, 2017-09-01T11:10:10Z). | String | "minute", "Hour"  | Igen |
| **interval** | Pozitív egész szám, amely az eseményindító futásának gyakoriságát meghatározó **frequency** érték időközét jelöli. Ha például az **intervallum** 3, és a **gyakoriság** értéke "Hour", az trigger 3 óránként ismétlődik. <br/>**Megjegyzés**: A minimális ablak időköze 15 perc. | Integer | Pozitív egész szám. | Igen |
| **startTime**| Az első előfordulás, amely múltbeli lehet. Az első trigger időköze akövetkező: (Kezdés, **kezdő** + **időköz**). | DateTime | Egy DateTime érték. | Igen |
| **endTime**| A legutóbbi előfordulás, amely múltbeli lehet. | DateTime | Egy DateTime érték. | Igen |
| **delay** | Az ablak adatfeldolgozásának megkezdését késleltető idő. A folyamat futtatása a várt végrehajtási idő és a **késleltetés**mennyisége után indul el. A **késleltetés** határozza meg, hogy mennyi ideig várakozik az indítás a határidő lejártakor az új Futtatás elindítása előtt. A **késleltetés** nem változtatja meg az ablak **kezdő**időkeretét. Például az 00:10:00- as késleltetési érték 10 percet vesz igénybe. | Timespan<br/>(óó: PP: SS)  | Egy TimeSpan érték, amely az alapértelmezett 00:00:00. | Nem |
| **maxConcurrency** | Azon egyidejű trigger-futtatások száma, amelyek készen állnak a Windows rendszerre. Például a tegnapi értékre való kitöltés óránkénti futtatásához 24 Windowson. Ha a **maxConcurrency** = 10, az aktiválási események csak az első 10 Windows rendszerre (00:00-01:00-09:00-10:00) vannak kirúgva. Az első 10 aktivált folyamat befejezése után a trigger-futtatások a következő 10 Windows rendszerre (10:00-11:00-19:00-20:00) lesznek kirúgva. Ha a **maxConcurrency** = 10 esetében ebben a példában 10 Windows áll rendelkezésre, 10 teljes folyamat fut. Ha csak 1 ablak áll készen, csak 1 folyamat fut. | Integer | 1 és 50 közötti egész szám. | Igen |
| **RetryPolicy Száma** | Az újrapróbálkozások száma, mielőtt a folyamat futása "sikertelen" állapotúként van megjelölve.  | Integer | Egész szám, ahol az alapértelmezett érték 0 (nincs újrapróbálkozás). | Nem |
| **retryPolicy: intervalInSeconds** | A másodpercben megadott újrapróbálkozási kísérletek közötti késleltetés. | Integer | Azon másodpercek száma, amelyekben az alapértelmezett érték 30. | Nem |
| **dependsOn: típus** | A TumblingWindowTriggerReference típusa. Kötelező, ha függőség van beállítva. | String |  "TumblingWindowTriggerDependencyReference", "SelfDependencyTumblingWindowTriggerReference" | Nem |
| **dependsOn: méret** | A függőséget jelző ablak mérete | Timespan<br/>(óó: PP: SS)  | Pozitív TimeSpan érték, amelynél az alapértelmezett érték a gyermek trigger ablakának mérete  | Nem |
| **dependsOn: eltolás** | A függőségi trigger eltolása. | Timespan<br/>(óó: PP: SS) |  Egy TimeSpan érték, amelynek negatívnak kell lennie az önfüggőségben. Ha nincs megadva érték, az ablak ugyanaz, mint maga az trigger. | Önálló függőség: Igen<br/>Más Nem  |

### <a name="windowstart-and-windowend-system-variables"></a>WindowStart és WindowEnd rendszerváltozók

Használhatja a **WindowStart** és a **WindowEnd** rendszerváltozóit a **folyamat** definíciójában (azaz egy lekérdezés egy részénél). A rendszerváltozókat paraméterként adja át a folyamatnak az **trigger** definíciójában. Az alábbi példa bemutatja, hogyan továbbíthatja ezeket a változókat paraméterekként:

```
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
            ...
        "pipeline": {
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

Ha a **WindowStart** és a **WindowEnd** rendszerváltozó értékét szeretné használni a folyamat definíciójában, használja a "MyWindowStart" és a "MyWindowEnd" paramétereket ennek megfelelően.

### <a name="execution-order-of-windows-in-a-backfill-scenario"></a>A Windows végrehajtási sorrendje backfill-forgatókönyvben
Ha több Windows-telepítés van folyamatban (különösen backfill-forgatókönyvben), a Windows-végrehajtás sorrendje determinisztikus, a legrégebbitől a legújabbig terjedő intervallumig. Ez a viselkedés jelenleg nem módosítható.

### <a name="existing-triggerresource-elements"></a>Meglévő TriggerResource elemek
A következő pontok érvényesek a meglévő **TriggerResource** elemekre:

* Ha az trigger változásának **gyakorisági** elemének (vagy ablakának mérete) értéke megváltozik, a már feldolgozott Windows-állapot *nem* áll alaphelyzetbe. Az trigger továbbra is az új ablak méretének használatával folytatja a Windowst az utolsó ablakból.
* Ha az indítás **utolsó** eleme elemének értéke (hozzáadott vagy frissített), a már feldolgozott Windows-állapot *nem* áll alaphelyzetbe. Az trigger tiszteletben tartja az új **utolsó** értéket. Ha az új **befejezési** érték a már végrehajtott Windows előtt van, az trigger leáll. Ellenkező esetben az aktiválás leáll, amikor az új befejezési érték észlelhető.

### <a name="tumbling-window-trigger-dependency"></a>Kiesési ablak trigger-függősége

Ha azt szeretné, hogy a rendszer csak akkor hajtson végre egy késleltetésű ablakos triggert, ha egy másik, az adatgyárban lévő kiesést kiváltó ablak-trigger sikeres végrehajtását követően [létrehoz egy ablakos trigger-függőséget](tumbling-window-trigger-dependency.md). 

## <a name="sample-for-azure-powershell"></a>Példa Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ez a szakasz bemutatja, hogyan használható a Azure PowerShell egy trigger létrehozásához, elindításához és figyeléséhez.

1. Hozzon létre egy **MyTrigger. JSON** nevű JSON-fájlt a C:\ADFv2QuickStartPSH\ mappában a következő tartalommal:

    > [!IMPORTANT]
    > A JSON-fájl mentése előtt állítsa be az időponthoz tartozó elem értékét az aktuális UTC-időre. Állítsa a befejezési elem értékét egy órára az aktuális UTC időpontnál.

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

2. Hozzon létre egy triggert a **set-AzDataFactoryV2Trigger** parancsmag használatával:

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
    
3. Győződjön meg arról, hogy az trigger állapota **leállt** a **Get-AzDataFactoryV2Trigger** parancsmag használatával:

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Indítsa el a triggert a **Start-AzDataFactoryV2Trigger** parancsmag használatával:

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Győződjön meg arról, hogy az trigger állapota a **Get-AzDataFactoryV2Trigger** parancsmag használatával **indul el** :

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6. A Get **-AzDataFactoryV2TriggerRun** parancsmag használatával lekérheti a trigger futtatását Azure PowerShellban. Az trigger futtatásával kapcsolatos információk lekéréséhez futtassa rendszeresen a következő parancsot. Frissítse a **TriggerRunStartedAfter** és a **TriggerRunStartedBefore** értékeket az trigger definíciójában szereplő értékeknek megfelelően:

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
Az trigger-futtatások és a folyamat-futtatások figyeléséhez a Azure Portalban lásd: [folyamatok futtatásának figyelése](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="next-steps"></a>További lépések

* Az eseményindítókkal kapcsolatos részletes információkért lásd: [folyamat-végrehajtás és eseményindítók](concepts-pipeline-execution-triggers.md#triggers).
* [Kieséses ablak trigger-függőségének létrehozása](tumbling-window-trigger-dependency.md)
