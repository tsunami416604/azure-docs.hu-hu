---
title: "Ütemezés eseményindítók létrehozása az Azure Data Factory |} Microsoft Docs"
description: "Megtudhatja, hogyan lehet indítót létrehozni az Azure Data Factoryben, amelyek egy folyamat ütemezés szerint futtatott."
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
ms.date: 01/23/2018
ms.author: shlo
ms.openlocfilehash: 51e2dddbe66ca372d89fc8efeb24bdab9fe6a442
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2018
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>Hozzon létre egy eseményindítót, amelyek egy folyamat ütemezés szerint fut
Ez a cikk tájékoztatást ad azokról az ütemezés eseményindító és létrehozása, indítsa el, és ütemezés eseményindító figyeléséhez szükséges lépésekről. Eseményindítók más típusú, lásd: [csővezeték-végrehajtási és eseményindítók](concepts-pipeline-execution-triggers.md).

Ütemezés eseményindító létrehozása, ha megad egy ütemezés (kezdő dátum, ismétlődési, lejárati dátuma stb.) a eseményindító, és az adatcsatorna társítható. A folyamatok és az eseményindítók között több-a-többhöz kapcsolat áll fenn. Egyetlen folyamatot több eseményindító is indíthat. Egyetlen eseményindító elindíthat több folyamatot is.

> [!NOTE]
> Ez a cikk az Azure Data Factory 2-es verziójú, amely jelenleg előzetes verzióban érhetők vonatkozik. Ha szeretne megtudni az Azure Data Factory 1-es verziójú, amely általánosan elérhető (GA), lásd: [Ismerkedés az Azure Data Factory 1-es verziójú](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

A következő szakaszokban különböző módon ütemezés eseményindító létrehozásához szükséges lépéseket. 

## <a name="data-factory-ui"></a>A Data Factory felhasználói felülete
Létrehozhat egy **ütemezés eseményindító** ütemezése egy folyamat rendszeres időközönként futtatandó (óránként, naponta, stb.). 

> [!NOTE]
> A feldolgozási sorban lévő és egy ütemezés eseményindítót az eseményindító társít a folyamatot, és futtatnia vagy felügyelnie a folyamat létrehozásának részletes útmutatást lásd: [gyors üzembe helyezés: hozzon létre egy adat-előállító Data Factory felületen](quickstart-create-data-factory-portal.md).

1. Váltson a **Szerkesztés** lapra. 

    ![Váltás a Szerkesztés lapra](./media/how-to-create-schedule-trigger/switch-edit-tab.png)
1. Kattintson a menü **Aktiválás** pontjára, majd az **Új/Szerkesztés** elemre. 

    ![Új eseményindító menü](./media/how-to-create-schedule-trigger/new-trigger-menu.png)
2. Az **Eseményindítók hozzáadása** oldalon kattintson az **Eseményindító kiválasztása**, majd pedig az **Új** elemre. 

    ![Eseményindítók hozzáadása – új eseményindító](./media/how-to-create-schedule-trigger/add-trigger-new-button.png)
3. Az a **eseményindító** lapon esetén tegye a következőket: 

    1. Ellenőrizze, hogy **ütemezés** van kiválasztva a **típus**. 
    2. Adja meg az eseményindító a kezdő dátum és idő **Start dátuma (UTC)**. Az aktuális dátum és idő alapértelmezés szerint érték. 
    3. Adja meg **ismétlődési** az eseményindító. Válassza ki az értékek (minden perc, óránként, naponta, hetente, és havi) a legördülő listából. A többszöröző a mezőben adja meg. Például, ha azt szeretné, hogy az eseményindító 15 percenként egyszer futtatni, akkor jelölje be **minden perc**, és írja be **15** a szövegmezőben. 
    4. Az a **End** mezőben, ha nem kíván megadni a eseményindító, jelölje be a befejező dátum/idő **nincs záró**. Adja meg egy záró dátum idő **dátuma**, és adja meg a befejező dátum/idő, és kattintson a **alkalmaz**. Minden egyes folyamatfuttatás költséggel jár. Ha teszteli, érdemes lehet győződjön meg arról, hogy a folyamat akkor váltódik ki, hogy csak néhány alkalommal. Arról is győződjön meg, hogy elegendő idő áll rendelkezésre a folyamat futtatásához a közzététel időpontja és a befejezés időpontja között. Az eseményindító csak a Data Factoryban való közzététel után lesz aktív, a felhasználói felületen történő mentéskor még nem.

        ![Eseményindító-beállítások](./media/how-to-create-schedule-trigger/trigger-settings.png)
4. Az a **eseményindító** ablakban ellenőrizze a **aktív** lehetőséget, majd kattintson a **következő**. Az eseményindító később inaktiválása használhatja ezt a jelölőnégyzetet. 

    ![Eseményindító-beállítások – Tovább gomb](./media/how-to-create-schedule-trigger/trigger-settings-next.png)
5. Az **Új eseményindító** oldalon tekintse át a figyelmeztető üzenetet, majd kattintson a **Befejezés** gombra.

    ![Eseményindító-beállítások – Befejezés gomb](./media/how-to-create-schedule-trigger/new-trigger-finish.png)
6. Kattintson a **Közzététel** elemre a módosítások Data Factoryban való közzétételéhez. Adat-előállító változások közzétételére, amíg az eseményindító nem indul el időt. a folyamat fut. 

    ![Közzététel gomb](./media/how-to-create-schedule-trigger/publish-2.png)
8. Váltson a bal oldali **Monitorozás** lapra. A lista frissítéséhez kattintson a **Frissítés** elemre. Megjelenik a feldolgozási sor az ütemezett eseményindító által kiváltott futtatja. Figyelje meg az **Aktiválva a következő alapján** oszlop értékeit. Ha **eseményindító most** beállítást, megjelenik a listában a manuális eseményindító. 

    ![Aktivált futtatások monitorozása](./media/how-to-create-schedule-trigger/monitor-triggered-runs.png)
9. Kattintson a **Folyamatfuttatások** melletti lefelé mutató nyílra a **Eseményindító-futtatások** nézetre való átváltáshoz. 

    ![Eseményindító-futtatások monitorozása](./media/how-to-create-schedule-trigger/monitor-trigger-runs.png)

## <a name="azure-powershell"></a>Azure PowerShell
Ez a szakasz bemutatja, hogyan használhatja az Azure Powershellt létrehozását, indítsa el, és ütemezés eseményindító megfigyelését. Ez a minta működő megtekintéséhez először halad át a [gyors üzembe helyezés: egy adat-előállító létrehozása az Azure PowerShell használatával](quickstart-create-data-factory-powershell.md). Ezt követően adja hozzá a következő kódot a fő metódus, amely a metódus létrehozza és elindítja egy ütemezés eseményindítót, amely 15 percenként fut. Az eseményindító társítva egy nevű folyamatot **Adfv2QuickStartPipeline** a gyors üzembe helyezés részeként létrehozott.

1. Hozzon létre egy JSON fájlt **MyTrigger.json** a C:\ADFv2QuickStartPSH\ mappában, a következő tartalommal:

    > [!IMPORTANT]
    > A JSON-fájl mentése előtt állítsa be az értékét a **startTime** elem a jelenlegi UTC-időre. Állítsa a **endTime** korábbi a jelenlegi UTC-idő egy órával elemet.

    ```json   
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Minute",
                    "interval": 15,
                    "startTime": "2017-12-08T00:00:00",
                    "endTime": "2017-12-08T01:00:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "Adfv2QuickStartPipeline"
                    },
                    "parameters": {
                        "inputPath": "adftutorial/input",
                        "outputPath": "adftutorial/output"
                    }
                }
            ]
        }
    }
    ```

    A JSON kódtöredékre:
    - A **típus** a trigger elem értéke "ScheduleTrigger."
    - A **gyakoriság** elem értéke "Perc" és a **időköz** elem értéke 15. Ezért az eseményindító a feldolgozási sor 15 percenként fut. kezdési és befejezési időpontjai között.
    - A **endTime** eleme egy órával azután az értékét a **startTime** elemet. Ezért az eseményindító futtatja a feldolgozási sor 15 perc, 30 percet, és a kezdési idő utánra 45 percig. Ne felejtse el frissíteni a kezdési időpontot az aktuális UTC idő szerint, és a befejező időpont korábbi a kezdési időpont egy óra. 
    - Az eseményindító társítva van a **Adfv2QuickStartPipeline** folyamat. Több folyamatok társítandó eseményindítót, adja hozzá több **pipelineReference** szakaszok.
    - A kimenetátirányítási mechanizmusával a gyors üzembe helyezési időt vesz igénybe, két **paraméterek** értékek: **inputPath** és **outputPath**. Ezek a paraméterek értékeit, ezért a eseményindítóval át.

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

6.  Az Azure PowerShell használatával futtat az eseményindító Get a **Get-AzureRmDataFactoryV2TriggerRun** parancsmag. Ahhoz, hogy az eseményindító fut információt, rendszeres időközönként hajtsa végre a következő parancsot. Frissítés a **TriggerRunStartedAfter** és **TriggerRunStartedBefore** az eseményindító-definícióban szereplő értékekre értékeket:

    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
    Az eseményindító figyelése fut, és a folyamat fut az Azure portálon, lásd: [figyelő folyamat fut](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).


## <a name="net-sdk"></a>.NET SDK
Ez a szakasz bemutatja, hogyan használhatja a .NET SDK létrehozását, indítsa el, és egy eseményindító megfigyelését. Ez a minta működő megtekintéséhez először halad át a [gyors üzembe helyezés: egy adat-előállító létrehozása a .NET SDK használatával](quickstart-create-data-factory-dot-net.md). Ezt követően adja hozzá a következő kódot a fő metódus, amely a metódus létrehozza és elindítja egy ütemezés eseményindítót, amely 15 percenként fut. Az eseményindító társítva egy nevű folyamatot **Adfv2QuickStartPipeline** a gyors üzembe helyezés részeként létrehozott.

Szeretne létrehozni, és indítsa el, amely 15 percenként fut. ütemezés eseményindítót, adja hozzá a következő kódot a fő metódus:

```csharp
            // Create the trigger
            Console.WriteLine("Creating the trigger");

            // Set the start time to the current UTC time
            DateTime startTime = DateTime.UtcNow;

            // Specify values for the inputPath and outputPath parameters
            Dictionary<string, object> pipelineParameters = new Dictionary<string, object>();
            pipelineParameters.Add("inputPath", "adftutorial/input");
            pipelineParameters.Add("outputPath", "adftutorial/output");

            // Create a schedule trigger
            string triggerName = "MyTrigger";
            ScheduleTrigger myTrigger = new ScheduleTrigger()
            {
                Pipelines = new List<TriggerPipelineReference>()
                {
                    // Associate the Adfv2QuickStartPipeline pipeline with the trigger
                    new TriggerPipelineReference()
                    {
                        PipelineReference = new PipelineReference(pipelineName),
                        Parameters = pipelineParameters,
                    }
                },
                Recurrence = new ScheduleTriggerRecurrence()
                {
                    // Set the start time to the current UTC time and the end time to one hour after the start time
                    StartTime = startTime,
                    TimeZone = "UTC",
                    EndTime = startTime.AddHours(1),
                    Frequency = RecurrenceFrequency.Minute,
                    Interval = 15,
                }
            };

            // Now, create the trigger by invoking the CreateOrUpdate method
            TriggerResource triggerResource = new TriggerResource()
            {
                Properties = myTrigger
            };
            client.Triggers.CreateOrUpdate(resourceGroup, dataFactoryName, triggerName, triggerResource);

            // Start the trigger
            Console.WriteLine("Starting the trigger");
            client.Triggers.Start(resourceGroup, dataFactoryName, triggerName);
```

Futtatás eseményindítót figyelő, vegye fel a következő kódot az utolsó `Console.WriteLine` minta utasítást:

```csharp
            // Check that the trigger runs every 15 minutes
            Console.WriteLine("Trigger runs. You see the output every 15 minutes");

            for (int i = 0; i < 3; i++)
            {
                System.Threading.Thread.Sleep(TimeSpan.FromMinutes(15));
                List<TriggerRun> triggerRuns = client.Triggers.ListRuns(resourceGroup, dataFactoryName, triggerName, DateTime.UtcNow.AddMinutes(-15 * (i + 1)), DateTime.UtcNow.AddMinutes(2)).ToList();
                Console.WriteLine("{0} trigger runs found", triggerRuns.Count);

                foreach (TriggerRun run in triggerRuns)
                {
                    foreach (KeyValuePair<string, string> triggeredPipeline in run.TriggeredPipelines)
                    {
                        PipelineRun triggeredPipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, triggeredPipeline.Value);
                        Console.WriteLine("Pipeline run ID: {0}, Status: {1}", triggeredPipelineRun.RunId, triggeredPipelineRun.Status);
                        List<ActivityRun> runs = client.ActivityRuns.ListByPipelineRun(resourceGroup, dataFactoryName, triggeredPipelineRun.RunId, run.TriggerRunTimestamp.Value, run.TriggerRunTimestamp.Value.AddMinutes(20)).ToList();
                    }
                }
            }
```

Az eseményindító figyelése fut, és a folyamat fut az Azure portálon, lásd: [figyelő folyamat fut](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).


## <a name="python-sdk"></a>Python SDK
Ez a szakasz bemutatja, hogyan használható a Python SDK létrehozása, indítsa el, és egy eseményindító figyelése. Ez a minta működő megtekintéséhez először halad át a [gyors üzembe helyezés: egy adat-előállító létrehozása a Python SDK használatával](quickstart-create-data-factory-python.md). Adja hozzá a következő kódrészletet a "figyelése a Futtatás futószalag" kódblokk, a Python-parancsfájl után. Ez a kód egy eseményindítót hoz létre ütemezés, amely 15 percenként fut. az adott kezdő és befejező időpontok között. Frissítés a **start_time** változó a jelenlegi UTC-időre, és a **end_time** változó korábbi a jelenlegi UTC idő szerint egy óra.

```python
    # Create a trigger
    tr_name = 'mytrigger'
    scheduler_recurrence = ScheduleTriggerRecurrence(frequency='Minute', interval='15',start_time='2017-12-12T04:00:00', end_time='2017-12-12T05:00:00', time_zone='UTC')
    pipeline_parameters = {'inputPath':'adftutorial/input', 'outputPath':'adftutorial/output'}
    pipelines_to_run = []
    pipeline_reference = PipelineReference('copyPipeline')
    pipelines_to_run.append(TriggerPipelineReference(pipeline_reference, pipeline_parameters))
    tr_properties = ScheduleTrigger(description='My scheduler trigger', pipelines = pipelines_to_run, recurrence=scheduler_recurrence)    
    adf_client.triggers.create_or_update(rg_name, df_name, tr_name, tr_properties)

    # Start the trigger
    adf_client.triggers.start(rg_name, df_name, tr_name)
```

Az eseményindító figyelése fut, és a folyamat fut az Azure portálon, lásd: [figyelő folyamat fut](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon
Az Azure Resource Manager-sablon segítségével hozható létre eseményindító. Részletes útmutatásért lásd: [egy az Azure data factory létrehozása egy Resource Manager-sablon használatával](quickstart-create-data-factory-resource-manager-template.md).  

## <a name="pass-the-trigger-start-time-to-a-pipeline"></a>Az eseményindító kezdési ideje átadni egy folyamat
Az Azure Data Factory 1-es verziójú támogatja olvasása vagy a rendszer változók segítségével particionált adatainak írása: **SliceStart**, **SliceEnd**, **WindowStart**, és **WindowEnd**. Az Azure Data Factory 2-es verzióját ez a viselkedés érhet el, hogy egy folyamat paraméterrel. A kezdési ideje és az eseményindítót az ütemezett időpont a feldolgozási sor paraméter értéke legyen. A következő példában az eseményindítót az ütemezett időpontban objektumnak átadott értékként a feldolgozási sor **scheduledRunTime** paraméter:

```json
"parameters": {
    "scheduledRunTime": "@trigger().scheduledTime"
}
```    

További információkért lásd az útmutatást [olvasására vagy írására, hogyan particionálva adatok](how-to-read-write-partitioned-data.md).

## <a name="json-schema"></a>JSON-séma
A következő JSON-definícióból bemutatja, hogyan hozható létre ütemezés eseményindító ütemezés és az ismétlődési:

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Month>>,
        "interval": <<int>>,             // Optional, specifies how often to fire (default to 1)
        "startTime": <<datetime>>,
        "endTime": <<datetime - optional>>,
        "timeZone": "UTC"
        "schedule": {                    // Optional (advanced scheduling specifics)
          "hours": [<<0-23>>],
          "weekDays": : [<<Monday-Sunday>>],
          "minutes": [<<0-59>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurences": [
               {
                    "day": <<Monday-Sunday>>,
                    "occurrence": <<1-5>>
               }
           ]
        }
      }
    },
   "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                        "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
           }
      ]
  }
}
```

> [!IMPORTANT]
>  A **paraméterek** tulajdonság egy kötelező tulajdonság az a **folyamatok** elemet. Ha a folyamat nem helyez el paramétereket, meg kell adni egy üres JSON definíciója a **paraméterek** tulajdonság.


### <a name="schema-overview"></a>Séma – áttekintés
A következő táblázat a fő séma elemei, amelyek kapcsolódnak ismétlődési és ütemezési eseményindító magas szintű áttekintést nyújt:

| JSON-tulajdonság | Leírás |
|:--- |:--- |
| **startTime** | Egy dátum-idő értéknek megfelelően. Az egyszerű ütemezés, értékét a **startTime** tulajdonság első vonatkozik. Az összetett ütemezések a indítsák el a megadott legkorábban **startTime** érték. |
| **Befejezés időpontja** | A befejezési dátum és idő, az eseményindító. Az eseményindító nem hajtja végre a megadott záró dátum és idő után. A tulajdonság értéke nem lehet múltbeli. Ez a tulajdonság nem kötelező. |
| **timeZone** | Az időzónát. Jelenleg csak az UTC időzóna használata támogatott. |
| **recurrence** | Egy ütemezésismétlődési objektum, amely meghatározza az ismétlődési a szabályokat. Az ismétlődési objektum támogatja a **gyakoriság**, **interva**l, **endTime**, **száma**, és **ütemezés**elemek. Ha egy ütemezésismétlődési objektum be van állítva, a **gyakoriság** elemet kell megadni. Az ismétlődési objektum az egyéb elemek egyike sem kötelező. |
| **frequency** | A gyakoriság, amellyel az eseményindító ismét előfordul egység. A támogatott értékek: "minute", "hour", "day", "hét" és "honap." |
| **interval** | Egy pozitív egész szám, amely azt jelzi, az időközt a **gyakoriság** érték, amely megadja, hogy milyen gyakran az eseményindító fut-e. Például ha a **időköz** 3 és a **gyakoriság** "hét," az eseményindító minden 3 hét ismétlődik. |
| **ütemezés** | Az ismétlődés ütemezésének az eseményindító. A megadott eseményindító **gyakoriság** értékét megváltoztatja az ismétlődési a ismétlődési ütemezés szerint. A **ütemezés** tulajdonsága tartalmazza az ismétlődés módosítások, a perc, óra, létrehozását, hónap és a hét számát alapuló.


### <a name="schema-defaults-limits-and-examples"></a>Alapértelmezett séma, korlátok és példák

| JSON-tulajdonság | Típus | Szükséges | Alapértelmezett érték | Érvényes értékek | Példa |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** | Karakterlánc | Igen | Nincs | ISO-8601 dátum-idők | `"startTime" : "2013-01-09T09:30:00-08:00"` |
| **recurrence** | Objektum | Igen | None | Recurrence objektum | `"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **interval** | Szám | Nem | 1 | 1-1 000 | `"interval":10` |
| **Befejezés időpontja** | Karakterlánc | Igen | None | A jövőben egy időpontot jelző dátum-idő érték. | `"endTime" : "2013-02-09T09:30:00-08:00"` |
| **ütemezés** | Objektum | Nem | Nincs | Schedule objektum | `"schedule" : { "minute" : [30], "hour" : [8,17] }` |

### <a name="starttime-property"></a>startTime tulajdonság
Az alábbi táblázat bemutatja, hogyan a **startTime** a tulajdonság szabja meg futtatni egy eseményindító:

| startTime értéke | Ismétlődés ütemezés nélkül | Ismétlődés ütemezéssel |
|:--- |:--- |:--- |
| Múltbeli kezdési időpont | Az első jövőbeli végrehajtási idő kiszámítja a kezdési idő utánra, és adott időpontban lefut.<br/><br/>A későbbi végrehajtások során a legutóbbi végrehajtásának időpontja kiszámítása alapján futtatja.<br/><br/>Lásd a táblázat alatti példát. | A indítsák el _nem sooner mint_ a megadott kezdési időponttal. A első előfordulása az ütemezés kezdési idejét alapján számított alapul.<br/><br/>A későbbi végrehajtások során ismétlődési ütemezés szerint futtatja. |
| Jövőbeli vagy aktuális kezdési időpont | Egyszer, a megadott kezdési időpontban futtatja.<br/><br/>A későbbi végrehajtások során a legutóbbi végrehajtásának időpontja kiszámítása alapján futtatja. | A indítsák el _egyes_ mint a megadott kezdési időpont. A első előfordulása az ütemezés kezdési idejét alapján számított alapul.<br/><br/>A későbbi végrehajtások során ismétlődési ütemezés szerint futtatja. |

Nézzük meg, mi történik, ha a kezdési időpont ismétlődése, de nincs ütemezés a múltban példát. Feltételezik, hogy az aktuális idő `2017-04-08 13:00`, a kezdési időpont `2017-04-07 14:00`, és az Ismétlődési gyakoriság két nap. (A **ismétlődési** beállításával értéke meg van adva a **gyakoriság** tulajdonság "Day" és a **időköz** tulajdonság 2.) Figyelje meg, hogy a **startTime** érték a múltban van, és az aktuális időpont előtt következik be.

Ezen feltételek mellett az első végrehajtási időpont `2017-04-09 at 14:00`. Az ütemezőmotor a kezdési időpont alapján kiszámítja a végrehajtási alkalmakat. A múltbéli időpontokat a rendszer elveti. A motor az első jövőbeli alkalmat használja. Ebben a forgatókönyvben a kezdési időpont van `2017-04-07 at 2:00pm`, így a következő példány kezdve, amely két nap `2017-04-09 at 2:00pm`.

Az első végrehajtási ideje a azonos még akkor is, ha a **startTime** értéke `2017-04-05 14:00` vagy `2017-04-01 14:00`. A későbbi végrehajtások során az első végrehajtása után az ütemezés segítségével számított. Ezért a későbbi végrehajtások során erővel `2017-04-11 at 2:00pm`, majd `2017-04-13 at 2:00pm`, majd `2017-04-15 at 2:00pm`, és így tovább.

Végül a órában vagy percben nincsenek beállítva az ütemezésben eseményindító, ha az óra vagy percek száma, az első végrehajtása használatosak az alapértelmezett beállításokat.

### <a name="schedule-property"></a>ütemezés tulajdonság
Egyrészről a ütemezés használata korlátozhatja a eseményindító végrehajtások száma. Például ha egy eseményindító havi gyakorisággal ütemezett csak a 31 nap, az eseményindító futtatja csak azokat a 31 napos hónapokban.

Ezzel szemben az ütemezés növelheti is az eseményindító végrehajtásának számát. Például egy eseményindítót, amely a hónap napjain 1 és 2, ütemezett havi gyakorisággal havonta, hanem a hónap 1. és 2. napon futtatja.

Ha több **ütemezés** elemek vannak megadva, a kiértékelés sorrendjét legnagyobbaktól a legkisebb ütemezési beállítás. A kiértékelés hét számát, valamint majd hónap és nap, hétköznap, óra, kezdődik, és végül perc.

A következő táblázat ismerteti a **ütemezés** elemek részletesen:


| JSON-elem | Leírás | Érvényes értékek |
|:--- |:--- |:--- |
| **perc** | Az óra azon perce, amikor az eseményindító fut. | <ul><li>Egész szám</li><li>Egész számok tömbje</li></ul>
| **üzemideje (óra)** | A nap azon órái, amikor az eseményindító fut. | <ul><li>Egész szám</li><li>Egész számok tömbje</li></ul> |
| **hétköznapokon** | Napokat a hét, amelyen fut az eseményindító. Az érték csak a heti gyakorisággal adható meg. | <ul><li>Hétfő, kedd, szerda, csütörtök, péntek, szombat, vasárnap</li><li>Tömböt nap (maximális tömb mérete 7)</li><li>Értékek nap-és nagybetűk nem</li></ul> |
| **monthlyOccurrences** | Amikor az eseményindító fut. hónap napjai. Az érték csak a havi gyakorisággal adható meg. | <ul><li>A tömb **monthlyOccurence** objektumok: `{ "day": day,  "occurrence": occurence }`.</li><li>A **nap** attribútuma, amikor az eseményindító fut. a hét napját. Például egy **monthlyOccurrences** tulajdonság egy **nap** értékének `{Sunday}` azt jelenti, hogy a hónap minden vasárnap. A **nap** attribútumot kell megadni.</li><li>A **előfordulási** attribútum értéke a megadott előfordulása **nap** a hónap során. Például egy **monthlyOccurrences** tulajdonság **nap** és **előfordulási** értékének `{Sunday, -1}` azt jelenti, hogy a hónap utolsó vasárnap. A **előfordulási** attribútum megadása nem kötelező.</li></ul> |
| **monthDays** | Napot a hónapban, amelyen fut az eseményindító. Az érték csak a havi gyakorisággal adható meg. | <ul><li>Bármilyen érték -1 és -31 között</li><li>Bármilyen érték 1 és 31 között</li><li>Tömböt</li></ul> |


## <a name="examples-of-trigger-recurrence-schedules"></a>Példák eseményindító ismétlődési ütemezés
Ez a szakasz ismétlődés ütemezésének példákat tartalmaz, és elsősorban a **ütemezés** objektum és az elemei.

A példák azt feltételezik, hogy a **időköz** értéke 1 és, hogy a **gyakoriság** érték szerint az ütemezés definíciója helyes. Nem lehet például egy **gyakoriság** "day" értékének és a "monthDays" módosítását is a **ütemezés** objektum. Ezek a korlátozások a táblázatban az előző szakaszban szerepelnek.

| Példa | Leírás |
|:--- |:--- |
| `{"hours":[5]}` | 5:00 órakor naponta fut. |
| `{"minutes":[15], "hours":[5]}` | 5:15 óra naponta futtatni. |
| `{"minutes":[15], "hours":[5,17]}` | 5 15 Perckor és délután 5:15 óra naponta futtatni. |
| `{"minutes":[15,45], "hours":[5,17]}` | Futtatás éjfélre (5:15), 5 óra 45 Perckor, 5:15 előtti, és 5:45 PM minden nap. |
| `{"minutes":[0,15,30,45]}` | 15 percenként fut. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Minden órában futtatva. Ez az eseményindító óránként fut le. A perc szabályozzák a **startTime** értéket, ha meg van adva. Ha nincs megadva, a perc szabályozzák a létrehozásának ideje. Például ha a kezdési ideje vagy létrehozásának idejét (amelyik vonatkozik) 12:25 PM, az eseményindító fut 00:25, 01:25, 02:25,..., és 23:25.<br/><br/>Az ütemezés értéke megegyezik a eseményindító rendelkezik egy **gyakorisága** "órás," értékre egy **időköz** értékének 1 és nem **ütemezés**.  Ezt az ütemezést is használható különböző **gyakoriság** és **időköz** értékek más eseményindítókat létrehozni. Például, ha a **gyakoriság** értéke "honap", az ütemezés futtatása csak havonta ahelyett, hogy minden nap, amikor a **gyakoriság** érték: "day". |
| `{"minutes":[0]}` | Minden órában futtatva egész. Ehhez az eseményindítóhoz óránként fut az Óra kezdő pozíció: 12:00-kor, 1:00-kor, 2:00-kor, és így tovább.<br/><br/>Ezt az ütemezést a eseményindító megegyezik egy **gyakoriság** "hour" értékének és egy **startTime** nulla perc vagy a nem érték **ütemezés** , de egy **gyakorisága**  értéke "day". Ha a **gyakoriság** értéke "hét" vagy "honap", az ütemezés csak, illetve hajt végre egy napját a heti vagy havi egy nap. |
| `{"minutes":[15]}` | 15 perc óránként túlra futtatni. Ehhez az eseményindítóhoz óránként fut kezdő pozíció: 00:15 AM, 1:15 AM, 2:15 AM, 15 perc, és így tovább, és 11:15 előtti végű. |
| `{"hours":[17], "weekDays":["saturday"]}` | Minden héten, 5:00 PM szombaton futtassa. |
| `{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Minden héten, 5:00 PM hétfőn, szerdán és pénteken futtassa. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Minden héten, 5:15 előtti és délután 5:45 óra hétfőn, szerdán és pénteken futtassa. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | 15 percenként futtatható létrehozását. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | 9:00-kor és du. 4:45 közötti napok vannak 15 percenként fut. |
| `{"weekDays":["tuesday", "thursday"]}` | Futtassa a keddi és csütörtöki napokon a megadott kezdési időpontban. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` | Minden hónap 28 napján 6:00 órakor futtassa (feltéve, hogy egy **gyakoriság** "honap", érték). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` | Futtatás a hónap utolsó napján 6:00 órakor. Egy eseményindító Futtatás a hónap utolsó napján, használja a -1 helyett 28, 29, 30 és 31 nap. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | Minden hónap első és utolsó napján 6:00 órakor futtassa. |
| `{monthDays":[1,14]}` | Futtassa a megadott kezdési időpontban minden hónap első és 14. napon. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Futtassa az első péntekén havonta, 5:00-kor. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Futtassa a megadott kezdési időpontban minden hónap első péntekén. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | Futtatás a hónap minden hónapban a megadott kezdési időpontban végén harmadik péntekén. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Futtassa az első és utolsó péntekén legyen havonta Reggel 5:15. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Futtassa az első és utolsó péntekén legyen minden hónapban a megadott kezdési időpontban. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | Futtassa a megadott kezdési időpontban havonta ötödik péntekén. Esetén nem ötödik pénteken egy hónap, a folyamat nem fut, mivel csak ötödik péntekenként futtatásra ütemezve van. Az eseményindító futtatásához utolsó péntekén legyen a hónap előforduló fontolja meg 5,-1 érték helyett a **előfordulási** érték. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | A hónap utolsó péntekén 15 percenként fut. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | Futtatás éjfélre (5:15), 5 óra 45 Perckor, 5:15 előtti, és délután 5:45 óra minden hónap harmadik szerdáján. |


## <a name="next-steps"></a>További lépések
Eseményindítók kapcsolatos részletes információkért lásd: [csővezeték-végrehajtási és eseményindítók](concepts-pipeline-execution-triggers.md#triggers).
