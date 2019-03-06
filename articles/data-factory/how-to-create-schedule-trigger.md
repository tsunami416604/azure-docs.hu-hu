---
title: Ütemezési eseményindító létrehozása az Azure Data Factoryban |} A Microsoft Docs
description: Megtudhatja, hogyan hozhat létre egy eseményindítót az Azure Data Factoryben, amely egy ütemezés szerint futtatja a folyamatot.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: shlo
ms.openlocfilehash: 62c9a8e6375f6ac7db86ae81cdd4e5c9eb445770
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57432821"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>Hozzon létre egy eseményindítót, amely egy ütemezés szerint futtatja a folyamatot
Ez a cikk az ütemezési eseményindító és létrehozása, indítása és monitorozása az ütemezési eseményindító lépéseit ismerteti. Más típusú eseményindítókat, lásd: [folyamat-végrehajtás és eseményindítók](concepts-pipeline-execution-triggers.md).

Ütemezési eseményindító létrehozásakor megad egy ütemezés (kezdő dátum, ismétlődés, záró dátuma, stb.) a eseményindító és a egy folyamat társítása. A folyamatok és az eseményindítók között több-a-többhöz kapcsolat áll fenn. Egyetlen folyamatot több eseményindító is indíthat. Egyetlen eseményindító elindíthat több folyamatot is.

A következő szakaszok különböző módon ütemezési eseményindító létrehozásához szükséges lépéseket. 

## <a name="data-factory-ui"></a>A Data Factory felhasználói felülete
Létrehozhat egy **ütemezési eseményindító** ütemezni egy folyamat futtatása rendszeres időközönként (például óránként, naponta stb.). 

> [!NOTE]
> Egy folyamat és a egy ütemezési eseményindítót, az eseményindító társítása a folyamat, és, és figyelheti a folyamat létrehozásának teljes leírását lásd: [a rövid útmutató: adat-előállító Data Factory felhasználói felületén hozzon létre](quickstart-create-data-factory-portal.md).

1. Váltson a **Szerkesztés** lapra. 

    ![Váltás a Szerkesztés lapra](./media/how-to-create-schedule-trigger/switch-edit-tab.png)
1. Kattintson a menü **Aktiválás** pontjára, majd az **Új/Szerkesztés** elemre. 

    ![Új eseményindító menü](./media/how-to-create-schedule-trigger/new-trigger-menu.png)
2. Az **Eseményindítók hozzáadása** oldalon kattintson az **Eseményindító kiválasztása**, majd pedig az **Új** elemre. 

    ![Eseményindítók hozzáadása – új eseményindító](./media/how-to-create-schedule-trigger/add-trigger-new-button.png)
3. Az a **új eseményindító** lapon, tegye a következőket: 

    1. Ellenőrizze, hogy **ütemezés** van kiválasztva a **típus**. 
    2. Adja meg a kezdő dátum és idő a trigger **Start dátuma (UTC)**. Időintervallumként az aktuális dátum és idő alapértelmezés szerint. 
    3. Adja meg **ismétlődési** az eseményindító. (Minden percenként, óránként, naponta, hetente, és havonta) a legördülő listából válassza ki egy értéket. A mezőben adja meg a Szorzó. Jelölje be például, ha azt szeretné, hogy az eseményindító esetében 15 percenként egyszer futtatni, akkor **minden perc**, és adja meg **15** a szövegmezőben. 
    4. Az a **záró** mező, ha nem szeretné, hogy adja meg az eseményindító területén válassza a befejező dátum/idő **nem teljes**. Adja meg egy záró dátum idő **dátum**, és adja meg a záró dátum és idő, és kattintson a **alkalmaz**. Minden egyes folyamatfuttatás költséggel jár. Ha teszteli, előfordulhat, hogy szeretne biztosítani, hogy a folyamat akkor aktiválódik, csak néhány alkalommal. Arról is győződjön meg, hogy elegendő idő áll rendelkezésre a folyamat futtatásához a közzététel időpontja és a befejezés időpontja között. Az eseményindító csak a Data Factoryban való közzététel után lesz aktív, a felhasználói felületen történő mentéskor még nem.

        ![Eseményindító-beállítások](./media/how-to-create-schedule-trigger/trigger-settings.png)
4. Az a **új eseményindító** ellenőrzése ablakban a **aktiválva** lehetőséget, majd kattintson a **tovább**. A jelölőnégyzet segítségével később inaktiválhatja az eseményindítót. 

    ![Eseményindító-beállítások – Tovább gomb](./media/how-to-create-schedule-trigger/trigger-settings-next.png)
5. Az **Új eseményindító** oldalon tekintse át a figyelmeztető üzenetet, majd kattintson a **Befejezés** gombra.

    ![Eseményindító-beállítások – Befejezés gomb](./media/how-to-create-schedule-trigger/new-trigger-finish.png)
6. Kattintson a **Közzététel** elemre a módosítások Data Factoryban való közzétételéhez. Mindaddig, amíg a módosítások közzététele a Data Factory, az eseményindító nem indul el a folyamat futásának indítása. 

    ![Közzététel gomb](./media/how-to-create-schedule-trigger/publish-2.png)
8. Váltson a bal oldali **Monitorozás** lapra. A lista frissítéséhez kattintson a **Frissítés** elemre. A folyamat az ütemezett eseményindító által aktivált fog látni. Figyelje meg az **Aktiválva a következő alapján** oszlop értékeit. Ha **Aktiválás most** beállítást, megjelenik a manuális eseményindító-Futtatás a listában. 

    ![Aktivált futtatások monitorozása](./media/how-to-create-schedule-trigger/monitor-triggered-runs.png)
9. Kattintson a **Folyamatfuttatások** melletti lefelé mutató nyílra a **Eseményindító-futtatások** nézetre való átváltáshoz. 

    ![Eseményindító-futtatások monitorozása](./media/how-to-create-schedule-trigger/monitor-trigger-runs.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ez a szakasz bemutatja, hogyan hozhat létre, indítsa el, és az ütemezési eseményindító figyelni az Azure PowerShell használatával. Minta működés, először lépjen a [a rövid útmutató: Adat-előállító létrehozása az Azure PowerShell-lel](quickstart-create-data-factory-powershell.md). Ezt követően adja a következő kódot a fő metódus létrehozza és elindítja egy ütemezési eseményindítót, amely 15 percenként fut le. Nevű folyamatot az eseményindító társítva **Adfv2QuickStartPipeline** a rövid útmutató során létrehozott.

1. Hozzon létre egy JSON-fájlt **MyTrigger.json** a C:\ADFv2QuickStartPSH\ mappában az alábbi tartalommal:

    > [!IMPORTANT]
    > A JSON-fájl mentése előtt az értékét állítsa be a **startTime** elem az aktuális UTC időpontig. Az értékét állítsa be a **endTime** elem az aktuális UTC időpontig elmúlt egy óra.

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

    A JSON-kódrészletben:
    - A **típus** az eseményindító elem értéke "ScheduleTrigger."
    - A **gyakorisága** elem "Percre" van beállítva, és a **időköz** elem 15-re van állítva. Ezért az eseményindító a folyamat 15 percenként fut a kezdő és záró időpont között.
    - A **endTime** elem értéke után egy órán keresztül a **startTime** elemet. Ezért az eseményindító fut. a folyamat 15 perc, 30 perc és a kezdő időpont utáni 45 percig. Ne felejtse el frissíteni a kezdési időpontot az aktuális UTC-idő és a befejezési idő korábbi a kezdési időpont egy óra. 
    - Az eseményindító társítva van a **Adfv2QuickStartPipeline** folyamat. Eseményindító több folyamatot társítható, adjon hozzá további **pipelineReference** szakaszokat.
    - A rövid útmutatóban a folyamat vesz igénybe, két **paraméterek** értékek: **inputPath** és **outputPath**. Ezért adja át a paraméterek értékei a trigger által.

2. Az eseményindító létrehozása a **Set-AzDataFactoryV2Trigger** parancsmagot:

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```

3. Győződjön meg arról, hogy az eseményindító állapotának **leállítva** használatával a **Get-AzDataFactoryV2Trigger** parancsmagot:

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Indítsa el az eseményindítót a használatával a **Start-AzDataFactoryV2Trigger** parancsmagot:

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Győződjön meg arról, hogy az eseményindító állapotának **elindítva** használatával a **Get-AzDataFactoryV2Trigger** parancsmagot:

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6.  Az eseményindító fut. az Azure PowerShell használatával Get a **Get-AzDataFactoryV2TriggerRun** parancsmagot. Az eseményindító-futtatások kapcsolatos információk lekérése, rendszeres időközönként hajtsa végre a következő parancsot. Frissítés a **TriggerRunStartedAfter** és **TriggerRunStartedBefore** értékeit, hogy megfeleljenek az eseményindító definíciójában szereplő értékeket:

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
    Az eseményindító figyelése fut, és az Azure Portalon folyamatfuttatások, lásd: [folyamatfuttatások monitorozása](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).


## <a name="net-sdk"></a>.NET SDK
Ez a szakasz bemutatja, hogyan hozhat létre, indítsa el, és a egy eseményindító figyelni a .NET SDK használata. Minta működés, először lépjen a [a rövid útmutató: Adat-előállító létrehozása a .NET SDK-val](quickstart-create-data-factory-dot-net.md). Ezt követően adja a következő kódot a fő metódus létrehozza és elindítja egy ütemezési eseményindítót, amely 15 percenként fut le. Nevű folyamatot az eseményindító társítva **Adfv2QuickStartPipeline** a rövid útmutató során létrehozott.

Hozhat létre, és indítsa el egy ütemezési eseményindítót, amely 15 percenként fut le, adja hozzá a következő kódot a fő metódushoz:

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

Eseményindító-Futtatás monitorozásához adja hozzá a következő kódot az utolsó előtti `Console.WriteLine` utasítás a minta:

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

Az eseményindító figyelése fut, és az Azure Portalon folyamatfuttatások, lásd: [folyamatfuttatások monitorozása](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).


## <a name="python-sdk"></a>Python SDK
Ez a szakasz bemutatja, hogyan használható a Python SDK-t hozhat létre, indítsa el, és a egy eseményindító figyelni. Minta működés, először lépjen a [a rövid útmutató: Adat-előállító létrehozása a Python SDK-val](quickstart-create-data-factory-python.md). Adja hozzá az alábbi kódblokkot az "a folyamat futásának monitorozása" kódblokkot, a Python-szkript után. Ez a kód létrehoz egy ütemezési eseményindítót, amely a megadott kezdő és befejező időpontok közötti 15 percenként fut le. Frissítés a **start_time** változó az aktuális UTC időpontig, és a **end_time** változó az aktuális UTC időpontig elmúlt egy óra.

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

Az eseményindító figyelése fut, és az Azure Portalon folyamatfuttatások, lásd: [folyamatfuttatások monitorozása](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon
Eseményindító létrehozása Azure Resource Manager-sablon segítségével. Lépésenkénti útmutatásért lásd: [egy Azure-beli adat-előállító létrehozása Resource Manager-sablon használatával](quickstart-create-data-factory-resource-manager-template.md).  

## <a name="pass-the-trigger-start-time-to-a-pipeline"></a>A trigger kezdő időpontja át egy folyamat
Az Azure Data Factory 1. verzió támogatja a rendszerváltozók használatával particionált adatok írása és olvasása: **SliceStart**, **SliceEnd**, **WindowStart**, és **WindowEnd**. A jelenlegi verzióban az Azure Data Factory a folyamat paraméter használatával érheti el ezt a viselkedést. A kezdő és az eseményindító ütemezett időpontja a folyamat paraméter értékeként állítsa be. A következő példában az eseményindítót az ütemezett időpontban a folyamat értékként átadott **scheduledRunTime** paramétert:

```json
"parameters": {
    "scheduledRunTime": "@trigger().scheduledTime"
}
```    

További információkért tekintse meg a található [olvasására vagy írására hogyan particionált adatok](how-to-read-write-partitioned-data.md).

## <a name="json-schema"></a>JSON-sémája
A következő JSON-definíciót bemutatja, hogyan hozhat létre egy ütemezési eseményindító ütemezési és ismétlődési:

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
          "monthlyOccurrences": [
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
>  A **paraméterek** tulajdonság a **folyamatok** elem kötelező tulajdonsága. Ha a folyamat nem használ paramétert, akkor is vegyen fel egy üres JSON-definíciót a **paraméterek** tulajdonságba.


### <a name="schema-overview"></a>Séma áttekintése
Az alábbi táblázat magas szintű áttekintést nyújt az eseményindítóval kapcsolatos ismétlődés és ütemezés fő sémaelemeiről:

| JSON-tulajdonság | Leírás |
|:--- |:--- |
| **startTime** | Dátum-idő érték. Egyszerű ütemezések esetében a **startTime** tulajdonság értéke az első előfordulásra vonatkozik. Összetett ütemezéseknél az eseményindító nem indul korábban a megadott **startTime** értéknél. |
| **endTime** | Az eseményindító záró dátuma és időpontja. Az eseményindító nem lesz végrehajtva a megadott záró dátum és idő után. A tulajdonság értéke nem lehet múltbéli időpont. Ez a tulajdonság nem kötelező. |
| **timeZone** | Az időzóna. Jelenleg csak az UTC időzóna támogatott. |
| **recurrence** | Az eseményindítóhoz tartozó ismétlődési szabályokat megadó recurrence objektum. A recurrence objektum a **frequency**, **interval**, **endTime**, **count** és **schedule** elemeket támogatja. Recurrence objektum meghatározásakor a **frequency** elem megadása kötelező. A recurrence objektum többi elemének megadása nem kötelező. |
| **frequency** | Az eseményindító ismétlődésének gyakorisági egysége. A támogatott értékek például: „minute”, „hour”, „day”, „week”, és „month” (percenként, óránként, naponta, hetente és havonta). |
| **interval** | Pozitív egész szám, amely az eseményindító futásának gyakoriságát meghatározó **frequency** érték időközét jelöli. Ha például az **interval** 3, a **frequency** pedig „week”, az eseményindító 3 hetente ismétlődik. |
| **schedule** | Az eseményindító ismétlődési ütemezése. Egy megadott **frequency** értékkel rendelkező eseményindító az ismétlődést az ismétlődési ütemezés alapján módosítja. A **schedule** tulajdonságban az ismétlődéshez tartozó módosítások szerepelnek, amelyek alapjául a percek, órák, a hét napja, a hónap napjai és a hét száma szolgál.


### <a name="schema-defaults-limits-and-examples"></a>Séma alapértékei, korlátai és példái

| JSON-tulajdonság | Typo | Szükséges | Alapértelmezett érték | Érvényes értékek | Példa |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** | String | Igen | None | ISO-8601 dátum-idők | `"startTime" : "2013-01-09T09:30:00-08:00"` |
| **recurrence** | Objektum | Igen | None | Recurrence objektum | `"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **interval** | Szám | Nem | 1 | 1–1000 | `"interval":10` |
| **endTime** | String | Igen | None | Egy jövőbeli időpontot jelölő dátum-idő érték. | `"endTime" : "2013-02-09T09:30:00-08:00"` |
| **schedule** | Objektum | Nem | None | Schedule objektum | `"schedule" : { "minute" : [30], "hour" : [8,17] }` |

### <a name="starttime-property"></a>startTime tulajdonság
Az alábbi táblázatban látható, hogy a **startTime** tulajdonság hogyan irányítja a folyamatfuttatást:

| startTime értéke | Ismétlődés ütemezés nélkül | Ismétlődés ütemezéssel |
|:--- |:--- |:--- |
| Múltbeli kezdési időpont | Kiszámítja a kezdőidőpontot követő első jövőbeli végrehajtási időpontot, és abban az időpontban fut.<br/><br/>Az azt követő végrehajtásokat mindig az utolsó végrehajtási időpont alapján számítja ki.<br/><br/>Lásd a táblázat alatti példát. | Az eseményindító _nem indulhat hamarabb_ a megadott kezdési időpontnál. Az első előfordulás a kezdési időpontból kiszámított ütemezésen alapul.<br/><br/>Az azt követő végrehajtásokat az ismétlődési ütemezés alapján futtatja. |
| Jövőbeli vagy aktuális kezdési időpont | Egyszer fut a megadott kezdési időpontban.<br/><br/>Az azt követő végrehajtásokat mindig az utolsó végrehajtási időpont alapján számítja ki. | Az eseményindító _nem indulhat hamarabb_ a megadott kezdési időpontnál. Az első előfordulás a kezdési időpontból kiszámított ütemezésen alapul.<br/><br/>Az azt követő végrehajtásokat az ismétlődési ütemezés alapján futtatja. |

Tekintsünk meg egy példát arról, hogy mi történik, ha a kezdési időpont egy múltbeli időpont, ismétlődéssel, de ütemezés nélkül. Tegyük fel, hogy az aktuális időpont `2017-04-08 13:00`, a kezdési idő `2017-04-07 14:00`, és az eseményindító kétnaponta ismétlődik. (A **recurrence** értéke a **frequency** tulajdonság „day” értékre és az **interval** tulajdonság 2 értékre történő állításával adható meg.) Figyelje meg, hogy a **startTime** értéke múltbeli időpont, és az aktuális időpont elé esik.

Ezen feltételek mellett az első végrehajtási időpont `2017-04-09 at 14:00`. Az ütemezőmotor a kezdési időpont alapján kiszámítja a végrehajtási alkalmakat. A múltbéli időpontokat a rendszer elveti. A motor az első jövőbeli alkalmat használja. Tehát ebben a forgatókönyvben a kezdési időpont `2017-04-07 at 2:00pm`, így a következő alkalom két napra esik ettől, azaz `2017-04-09 at 2:00pm`.

Az első végrehajtási időpont akkor is ez, ha a **startTime** értéke `2017-04-05 14:00` vagy `2017-04-01 14:00`. Az első végrehajtás után a rendszer a következő végrehajtási időpontokat az ütemezés alapján számítja ki. Ezért a következő végrehajtások a következő időpontokban történnek: `2017-04-11 at 2:00pm`, `2017-04-13 at 2:00pm` majd `2017-04-15 at 2:00pm` és így tovább.

Végül, ha az eseményindító ütemezésében nincs megadva az óra vagy perc, a rendszer alapértelmezés szerint az első végrehajtás óra vagy perc értékeit használja.

### <a name="schedule-property"></a>schedule tulajdonság
A schedule használata egyrészt korlátozhatja a eseményindító végrehajtásainak számát. Ha például egy havi gyakoriságú eseményindító csak a 31. napon való futásra van ütemezve, akkor az eseményindító csak a 31 napos hónapokban fut.

Ezzel szemben az ütemezés növelheti is az eseményindító végrehajtásának számát. Ha például egy havi gyakoriságú eseményindító a hónap 1. és 2. napján való futásra van ütemezve, akkor az eseményindító minden elsején és másodikán is fut, nem csak egyszer egy hónapban.

Ha több **schedule** elem is meg van adva, a kiértékelési sorrend a legnagyobb ütemezési beállítástól halad a legkisebb felé. A kiértékelés a hét számával kezdődik, majd ezt követi a hónap napja, a hét napja, az óra és végül a perc.

A következő táblázat részletesen ismerteti a **schedule** elemeit:


| JSON-elem | Leírás | Érvényes értékek |
|:--- |:--- |:--- |
| **minutes** | Az óra azon perce, amikor az eseményindító fut. | <ul><li>Egész szám</li><li>Egész számok tömbje</li></ul>
| **hours** | A nap azon órái, amikor az eseményindító fut. | <ul><li>Egész szám</li><li>Egész számok tömbje</li></ul> |
| **weekDays** | A hét azon napjai, amelyeken az eseményindító fut. Az érték csak heti gyakorisággal adható meg. | <ul><li>Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday (Hétfő, Kedd, Szerda, Csütörtök, Péntek, Szombat, Vasárnap)</li><li>A nap értékek tömbje (a tömb maximális értéke 7)</li><li>A nap értékek nem tesznek különbséget a kis- és nagybetű között</li></ul> |
| **monthlyOccurrences** | A hónap azon napjai, amelyeken az eseményindító fut. Az érték csak havi gyakorisággal adható meg. | <ul><li>A tömb **monthlyOccurrence** objektumok: `{ "day": day,  "occurrence": occurrence }`.</li><li>A **day** attribútum a hét azon napja, amelyen az eseményindító fut. Például a `{Sunday}` értékű **day** attribútummal rendelkező **monthlyOccurrences** tulajdonság a hónap minden vasárnapját jelenti. A **day** attribútum megadása kötelező.</li><li>Az **occurrence** attribútum a megadott **day** attribútum előfordulása a hónapban. Például a `{Sunday, -1}` értékű **day** és **occurrence** attribútumokkal rendelkező **monthlyOccurrences** tulajdonság a hónap utolsó vasárnapját jelenti. Az **occurrence** attribútum megadása nem kötelező.</li></ul> |
| **monthDays** | A hónap azon napja, amelyen az eseményindító lefut. Az érték csak havi gyakorisággal adható meg. | <ul><li>Bármilyen érték -1 és -31 között</li><li>Bármilyen érték 1 és 31 között</li><li>Értékek tömbje</li></ul> |


## <a name="examples-of-trigger-recurrence-schedules"></a>Eseményindító-ismétlődési ütemezések példái
Ez a szakasz az ismétlődésütemezésekre mutat példákat, és a **schedule** objektumra és annak elemeire koncentrál.

Ez a példa azt feltételezi, hogy az **interval** értéke 1, és hogy a **frequency** értéke helyes, az ütemezés meghatározása szerint. Nem lehet például a **frequency** értéke „day”, ha a **schedule** objektum „monthDays” értékre lett módosítva. Ez és az ehhez hasonló korlátozások az előző szakaszban található táblázatban szerepelnek.

| Példa | Leírás |
|:--- |:--- |
| `{"hours":[5]}` | Minden nap 05:00-kor fut le. |
| `{"minutes":[15], "hours":[5]}` | Minden nap 05:15-kor fut le. |
| `{"minutes":[15], "hours":[5,17]}` | Minden nap 05:15-kor és 17:15-kor fut le. |
| `{"minutes":[15,45], "hours":[5,17]}` | Minden nap 05:15-kor, 05:45-kor, 17:15-kor és 17:45-kor fut le. |
| `{"minutes":[0,15,30,45]}` | 15 percenként fut le. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Óránként fut le. Ez az eseményindító óránként fut le. A perceket a **startTime** értéke szabályozza, ha az érték meg van adva. Ha az érték nincs megadva, akkor a perceket a létrehozás ideje szabályozza. Ha például a kezdés időpontja vagy a létrehozás időpontja (amelyik alkalmazható) 12:25, akkor az eseményindító 00:25-kor, 01:25-kor, 02:25-kor stb. fut le, végül pedig 23:25-kor.<br/><br/>Ez az ütemezés egyenértékű azzal, mintha egy „hour” értékű **frequency** attribútummal rendelkező eseményindító lenne, 1-es **interval** értékkel, **schedule** nélkül.  Ez az ütemezés különböző **frequency** és **interval** értékekkel használható egyéb eseményindítók létrehozásához. Ha például a **frequency** értéke „month”, az ütemezés egy hónapban csak egyszer fut le, nem pedig minden nap, amikor a **frequency** értéke „day”. |
| `{"minutes":[0]}` | Minden óra kezdetén fut le. Ez az eseményindító minden óra kezdetén fut le, 00:00-kor, 01:00-kor, 02:00-kor és így tovább.<br/><br/>Ez az ütemezés megegyezik egy olyan eseményindítóval, amely „hour” **frequency** értékkel és nulla perc **startTime** értékkel rendelkezik, vagy nincs **schedule** érték, de a **frequency** értéke „day”. Ha a **frequency** értéke „week” vagy „month”, az ütemezés csak egy héten egyszer vagy egy hónapban egyszer fut le. |
| `{"minutes":[15]}` | Minden óra 15. percében fut le. Ez az eseményindító minden óra 15. percében fut le, 00:15-kor, 01:15-kor, 02:15-kor és így tovább, egészen 23:15-ig. |
| `{"hours":[17], "weekDays":["saturday"]}` | Minden héten szombaton, 17:00-kor fut le. |
| `{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Minden héten hétfőn, szerdán és pénteken, 17:00-kor fut le. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Minden héten hétfőn, szerdán és pénteken, 17:15-kor és 17:45-kor fut le. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Hétköznapokon 15 percenként fut le. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Hétköznapokon 15 percenként fut le 09:00 és 16:45 között. |
| `{"weekDays":["tuesday", "thursday"]}` | Keddenként és csütörtökönként, a megadott kezdési időpontban fut le. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` | Minden hónap 28. napján, 06:00-kor fut le (azt feltételezi, hogy a **frequency** „month” értékre van állítva). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` | Minden hónap utolsó napján, 06:00-kor fut le. Ha az adott eseményindítót a hónap utolsó napján kívánja futtatni, a 28., 29., 30. vagy 31. nap megadása helyett használjon -1-et. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | Minden hónap első és utolsó napján, 06:00-kor fut le. |
| `{monthDays":[1,14]}` | Minden hónap első és 14. napján, a megadott kezdési időpontban fut le. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Minden hónap első péntekjén, 05:00-kor fut le. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Minden hónap első péntekjén, a megadott kezdési időpontban fut le. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | A hónap végétől számított harmadik pénteken, minden hónapban, a megadott kezdési időpontban fut le. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Minden hónap első és utolsó péntekjén, 05:15-kor fut le. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Minden hónap első és utolsó péntekjén, a megadott kezdési időpontban fut le. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | Minden hónap ötödik péntekjén, a megadott kezdési időpontban fut le. Ha az adott hónapban nincs ötödik péntek, a folyamat nem fut le, mivel kizárólag a minden ötödik pénteken történő futásra van ütemezve. Az eseményindító a hónap utolsó péntekjén való futtatásához az **occurrence** értékénél használjon -1-et 5 helyett. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | Minden hónap utolsó péntekjén, 15 percenként fut le. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | Minden hónap harmadik szerdáján, 05:15-kor, 05:45-kor, 17:15-kor és 17:45-kor fut le. |


## <a name="next-steps"></a>További lépések
Részletes információkat lásd: [folyamat-végrehajtás és eseményindítók](concepts-pipeline-execution-triggers.md#triggers).
