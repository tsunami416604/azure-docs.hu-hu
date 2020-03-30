---
title: Bukdácsoló ablakeseményindítók létrehozása az Azure Data Factoryban
description: Ismerje meg, hogyan hozhat létre egy eseményindítót az Azure Data Factoryban, amely egy folyamatot futtat egy bukdácsoló ablakon.
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
ms.openlocfilehash: f9e31b8f0fce1af8408b80afb1049dae8c8ecf1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73673716"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Átfedésmentes ablakban folyamatot futtató trigger létrehozása
Ez a cikk a bukdácsoló ablakeseményindító létrehozásának, indításának és figyelésének lépéseit ismerteti. Az eseményindítókról és a támogatott típusokról a [Folyamat végrehajtása és az eseményindítók](concepts-pipeline-execution-triggers.md)című témakörben talál általános információkat.

Az átfedésmentes ablakos eseményindítók olyan eseményindítók, amelyek rendszeres időközönként aktiválódnak a megadott kezdési időponttól kezdve, az állapot megőrzése mellett. Az átfedésmentes ablakok rögzített méretű, egymást nem fedő és összefüggő időintervallumok. A bukdácsoló ablakeseményindító egy-az-egyhez kapcsolatban áll egy folyamattal, és csak egy egyes számú folyamatra hivatkozhat.

## <a name="data-factory-ui"></a>A Data Factory felhasználói felülete

1. Ha a Data Factory felhasználói felületén bukdácsoló ablakeseményindítót szeretne létrehozni, válassza az **Eseményindítók** lapot, majd az **Új**lehetőséget. 
1. Az eseményindító konfigurációs ablaktábla megnyitása után válassza a **Tumbling ablak**lehetőséget, majd adja meg a bukdácsoló ablak eseményindító-tulajdonságait. 
1. Amikor elkészült, válassza a **Mentés** lehetőséget.

![Tumbling ablakeseményindító létrehozása az Azure Portalon](media/how-to-create-tumbling-window-trigger/create-tumbling-window-trigger.png)

## <a name="tumbling-window-trigger-type-properties"></a>Az ablakeseményindító típusának bukdácsolása

A bukdácsoló ablak a következő eseményindító típustulajdonságokkal rendelkezik:

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
            "maxConcurrency": <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                "intervalInSeconds": <<int>>,
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

Az alábbi táblázat magas szintű áttekintést nyújt a főbb JSON-elemekről, amelyek egy bukdácsoló ablakesemény ismétlődéséhez és ütemezéséhez kapcsolódnak:

| JSON-elem | Leírás | Típus | Megengedett értékek | Kötelező |
|:--- |:--- |:--- |:--- |:--- |
| **Típus** | A ravasz típusa. A típus a "TumblingWindowTrigger" rögzített érték. | Sztring | "TumblingWindowTrigger" | Igen |
| **runtimeState** | Az eseményindító futási idejének aktuális állapota.<br/>**Megjegyzés:** Ez \<az elem olvashatóCsak>. | Sztring | "Elindítva", "Leállítva", "Letiltva" | Igen |
| **frequency** | Olyan karakterlánc, amely azt a frekvenciaegységet (perc vagy óra) jelöli, amelynél az eseményindító ismétlődik. Ha a **kezdőidő** dátumértékei részletesebbek, mint a **gyakorisági** érték, a program figyelembe veszi a **kezdési idő** dátumokat az ablakhatárok kiszámításakor. Ha például a **gyakorisági** érték óránkénti, és a **startTime** érték 2017-09-01T10:10:10Z, az első ablak a következő (2017-09-01T10:10:10Z, 2017-09-01T11:10:10Z). | Sztring | "perc", "óra"  | Igen |
| **interval** | Pozitív egész szám, amely az eseményindító futásának gyakoriságát meghatározó **frequency** érték időközét jelöli. Ha például az **intervallum** 3, a **gyakoriságpedig** "óra", az eseményindító 3 óránként ismétlődik. <br/>**Megjegyzés:** A minimális ablakintervallum 15 perc. | Egész szám | Pozitív egész szám. | Igen |
| **startTime**| Az első előfordulás, amely lehet a múltban. Az első eseményindító időköz (**startTime**, **startTime** + **interval**). | DateTime | DateTime érték. | Igen |
| **endTime**| Az utolsó előfordulás, ami lehet a múltban. | DateTime | DateTime érték. | Igen |
| **Késleltetés** | Az ablak adatfeldolgozásának elhalasztására szánt idő. A folyamat futtatása a várt végrehajtási idő és a **késleltetés**mértéke után indul el. A **késleltetés** határozza meg, hogy az eseményindító mennyi ideig várjon a megfelelő idő után, mielőtt új futtatást indítana. A **késleltetés** nem változtatja meg az ablak **startTime**. A 00:10:00 **késleltetési** érték például 10 perces késést jelent. | Időtartomány<br/>(óó:pp:ss)  | Olyan időfesztávolságérték, amelyben az alapértelmezett érték 00:00:00. | Nem |
| **maxEgyidejű pénznem** | A kész ablakokhoz indított egyidejű eseményindító-futtatások száma. Például a tegnapi töltési idő höz való visszatöltés 24 ablakot eredményez. Ha **maxConcurrency** = 10, az eseményindító események csak az első 10 ablakban (00:00-01:00 - 09:00-10:00) aktiválódnak. Miután az első 10 aktivált folyamat fut befejeződött, eseményindító fut a következő 10 ablakok (10:00-11:00 - 19:00-20:00). Folytatva ezt a példát **maxConcurrency** = 10, ha 10 ablak kész, van 10 teljes folyamat fut. Ha csak egy ablak áll készen, akkor csak egy folyamat fut. | Egész szám | 1 és 50 közötti egész szám. | Igen |
| **retryPolicy: Count** | A folyamat futtatása előtt az újrapróbálkozások száma "Sikertelen" jelöléssel van ellátva.  | Egész szám | Egész szám, ahol az alapértelmezett érték 0 (nincs újrapróbálkozás). | Nem |
| **retryPolicy: intervalInSeconds** | Az újrapróbálkozási kísérletek közötti késleltetés másodpercben. | Egész szám | Másodpercek száma, ahol az alapértelmezett érték 30. | Nem |
| **dependsOn: típus** | A TumblingWindowTriggerReference típusa. Szükség van, ha a függőség van beállítva. | Sztring |  "TumblingWindowTriggerDependencyReference", "SelfDependencyTumblingWindowTriggerReference" | Nem |
| **dependsOn: méret** | A függőségi zuhanás ablakának mérete. | Időtartomány<br/>(óó:pp:ss)  | Pozitív időfesztávolságérték, ahol az alapértelmezett érték a gyermekeseményindító ablakmérete  | Nem |
| **dependsOn: eltolás** | A függőségi eseményindító eltolása. | Időtartomány<br/>(óó:pp:ss) |  Olyan timespan érték, amelynek negatívnak kell lennie egy önfüggőségben. Ha nincs megadva érték, az ablak megegyezik magával az eseményindítóval. | Ön-függőség: Igen<br/>Egyéb: Nem  |

### <a name="windowstart-and-windowend-system-variables"></a>WindowStart és WindowEnd rendszerváltozók

Használhatja a **WindowStart** és **WindowEnd** rendszer változók a bukdácsoló ablak eseményindító a **folyamat** definíciójában (azaz egy lekérdezés része). Adja át a rendszerváltozók paraméterekként a folyamat az **eseményindító** definíciójában. A következő példa bemutatja, hogyan adhatja át ezeket a változókat paraméterként:

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

A **WindowStart** és a **WindowEnd** rendszerváltozó-értékek használata a folyamatdefinícióban a "MyWindowStart" és a "MyWindowEnd" paraméterek használatával ennek megfelelően.

### <a name="execution-order-of-windows-in-a-backfill-scenario"></a>Az ablakok végrehajtási sorrendje háttérkitöltési forgatókönyvben
Ha több ablak van végrehajtásra (különösen egy háttérkitöltési forgatókönyvesetén), az ablakok végrehajtási sorrendje determinisztikus, a legrégebbitől a legújabb intervallumokig. Ez a viselkedés jelenleg nem módosítható.

### <a name="existing-triggerresource-elements"></a>Meglévő TriggerResource-elemek
A következő pontok vonatkoznak a meglévő **TriggerResource** elemekre:

* Ha az eseményindító **gyakorisági** elemének (vagy ablakméretének) értéke megváltozik, a már feldolgozott ablakok állapota *nem* lesz alaphelyzetbe állítva. Az eseményindító továbbra is az új ablakméret használatával végrehajtott ablakok ablakait váltja ki.
* Ha az eseményindító **endTime** elemének értéke megváltozik (hozzáadva vagy frissítve), a már feldolgozott ablakok állapota *nem* lesz alaphelyzetbe állítva. Az eseményindító tiszteletben tartja az új **endTime** értéket. Ha az új **endTime** érték a már végrehajtott ablakok előtt van, az eseményindító leáll. Ellenkező esetben az eseményindító leáll, amikor az új **endTime** érték találkozik.

### <a name="tumbling-window-trigger-dependency"></a>Az ablak aktiválásának zuhanása függőség

Ha meg szeretne győződni arról, hogy egy bukdácsoló ablak eseményindítója csak egy másik bukdácsoló ablakeseményindító sikeres végrehajtása után kerül végrehajtásra az adat-előállítóban, [hozzon létre egy bukdácsoló ablak eseményindító függőséget.](tumbling-window-trigger-dependency.md) 

## <a name="sample-for-azure-powershell"></a>Minta az Azure PowerShellhez

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ez a szakasz bemutatja, hogyan használhatja az Azure PowerShellt egy eseményindító létrehozásához, indításához és figyeléséhez.

1. Hozzon létre egy **MyTrigger.json** nevű JSON-fájlt a C:\ADFv2QuickStartPSH\ mappában a következő tartalommal:

    > [!IMPORTANT]
    > A JSON-fájl mentése előtt állítsa a **startTime** elem értékét az aktuális UTC-időre. Állítsa be a **endTime** elem értékét egy órával az aktuális UTC-idő után.

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

2. Hozzon létre egy eseményindítót a **Set-AzDataFactoryV2Trigger** parancsmag használatával:

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
    
3. A **Get-AzDataFactoryV2Trigger** parancsmag használatával ellenőrizze, hogy az eseményindító állapota **Le van-e állítva:**

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Indítsa el az eseményindítót a **Start-AzDataFactoryV2Trigger** parancsmag használatával:

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Ellenőrizze, hogy az eseményindító állapota **Elindult-e** a **Get-AzDataFactoryV2Trigger** parancsmag használatával:

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6. Az eseményindító futtatása az Azure PowerShellben a **Get-AzDataFactoryV2TriggerRun** parancsmag használatával. Az eseményindító futtatásairól a következő parancsot rendszeresen végrehajthatja. Frissítse a **TriggerRunStartedAfter** és **a TriggerRunStartedBefore** értékeket, hogy megfeleljenek az eseményindító definíciójának értékeinek:

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
Az eseményindítók és a folyamatfuttatások figyelése az Azure Portalon, [lásd: Folyamatfuttatások figyelése.](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)

## <a name="next-steps"></a>További lépések

* Az eseményindítókról a [Folyamat végrehajtása és az eseményindítók](concepts-pipeline-execution-triggers.md#triggers)című témakörben talál részletes információt.
* [Függőség létrehozása átfedésmentes ablak eseményindítójához](tumbling-window-trigger-dependency.md)
