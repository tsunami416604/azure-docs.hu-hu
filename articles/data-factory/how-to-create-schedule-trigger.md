---
title: Ütemezési eseményindítók létrehozása az Azure Data Factoryban
description: Ismerje meg, hogyan hozhat létre egy eseményindítót az Azure Data Factoryban, amely ütemezés szerint futtat egy folyamatot.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/23/2018
ms.openlocfilehash: 100f95c20743f70bb4a9f2ac7e74853eab80f3e9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414482"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>Folyamatütemezés szerinti futtatására vonatkozó eseményindító létrehozása
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk az ütemezési eseményindítóval, valamint az ütemezési eseményindító létrehozásának, indításának és figyelésének lépéseivel kapcsolatos információkat tartalmazza. Más típusú eseményindítók, [lásd: Folyamat végrehajtása és eseményindítók.](concepts-pipeline-execution-triggers.md)

Ütemezési eseményindító létrehozásakor meg kell adnia egy ütemezést (kezdési dátum, ismétlődés, befejezési dátum stb.), és társítja a folyamathoz. A folyamatok és az eseményindítók között több-a-többhöz kapcsolat áll fenn. Egyetlen folyamatot több eseményindító is indíthat. Egyetlen eseményindító elindíthat több folyamatot is.

A következő szakaszok különböző módon biztosítják az ütemezési eseményindító létrehozásának lépéseit. 

## <a name="data-factory-ui"></a>A Data Factory felhasználói felülete
Létrehozhat egy **ütemezési eseményindítót,** amely a folyamat időszakos (óránkénti, napi stb.) futtatását ütemezi. 

> [!NOTE]
> A folyamat és az ütemezési eseményindító létrehozásának, az eseményindítónak a folyamathoz való társításával, valamint a folyamat futtatásával és figyelésével készült teljes forgatókönyvről a [Rövid útmutató: Adatgyár létrehozása a Data Factory felhasználói felületének használatával](quickstart-create-data-factory-portal.md)című témakörben található.

1. Váltson a **Szerkesztés** lapra. 

    ![Váltás a Szerkesztés lapra](./media/how-to-create-schedule-trigger/switch-edit-tab.png)
1. Kattintson a menü **Aktiválás** pontjára, majd az **Új/Szerkesztés** elemre. 

    ![Új eseményindító menü](./media/how-to-create-schedule-trigger/new-trigger-menu.png)
2. Az **Eseményindítók hozzáadása** oldalon kattintson az **Eseményindító kiválasztása**, majd pedig az **Új** elemre. 

    ![Eseményindítók hozzáadása – új eseményindító](./media/how-to-create-schedule-trigger/add-trigger-new-button.png)
3. Az **Új eseményindító** lapon tegye a következő lépéseket: 

    1. Annak ellenőrzése, hogy az **Ütemezés** beállítás ki van-e jelölve a **Típus**mezőben. 
    2. Adja meg az eseményindító kezdő dátumát a **kezdő dátumhoz (UTC)**. Alapértelmezés szerint az aktuális dátumidőre van beállítva. 
    3. Adja meg az **ismétlődést** az eseményindítóhoz. Válasszon egyet a legördülő listából (Percenként, Óránkénti, Napi, Heti és Havi). Írja be a szorzót a szövegmezőbe. Ha például azt szeretné, hogy az eseményindító 15 percenként egyszer fusson, jelölje be a **Minden perc**lehetőséget, és írja be a **15** értéket a szövegmezőbe. 
    4. Ha a **Záró mezőben** nem szeretne befejezési dátumot megadni az eseményindítóhoz, válassza a **Nincs befejezés**lehetőséget. A befejezési dátum idő megadásához válassza **a Dátum dátum**lehetőséget, adja meg a befejezési dátumidőt, majd kattintson az Alkalmaz **gombra.** Minden egyes folyamatfuttatás költséggel jár. Ha tesztelés alatt áll, érdemes lehet biztosítani, hogy a folyamat csak néhány szor aktiválódik. Arról is győződjön meg, hogy elegendő idő áll rendelkezésre a folyamat futtatásához a közzététel időpontja és a befejezés időpontja között. Az eseményindító csak a Data Factoryban való közzététel után lesz aktív, a felhasználói felületen történő mentéskor még nem.

        ![Eseményindító-beállítások](./media/how-to-create-schedule-trigger/trigger-settings.png)
4. Az **Új eseményindító** ablakban jelölje be az **Aktivált** lehetőséget, majd kattintson a **Tovább**gombra. Ezzel a jelölőnégyzetből később inaktiválhatja az eseményindítót. 

    ![Eseményindító-beállítások – Tovább gomb](./media/how-to-create-schedule-trigger/trigger-settings-next.png)
5. Az **Új eseményindító** oldalon tekintse át a figyelmeztető üzenetet, majd kattintson a **Befejezés** gombra.

    ![Eseményindító-beállítások – Befejezés gomb](./media/how-to-create-schedule-trigger/new-trigger-finish.png)
6. Kattintson a **Közzététel** elemre a módosítások Data Factoryban való közzétételéhez. Amíg nem teszi közzé a data factory módosításait, az eseményindító nem indul el a folyamat futtatása. 

    ![Közzététel gomb](./media/how-to-create-schedule-trigger/publish-2.png)
8. Váltson a bal oldali **Monitorozás** lapra. A lista frissítéséhez kattintson a **Frissítés** elemre. Láthatja, hogy a folyamat fut által aktivált az ütemezett eseményindító. Figyelje meg az **Aktiválva a következő alapján** oszlop értékeit. Ha az **Eseményindító most** beállítást használja, a manuális eseményindító futtatása látható a listában. 

    ![Aktivált futtatások monitorozása](./media/how-to-create-schedule-trigger/monitor-triggered-runs.png)
9. Kattintson a **Folyamatfuttatások** melletti lefelé mutató nyílra a **Eseményindító-futtatások** nézetre való átváltáshoz. 

    ![Eseményindító-futtatások monitorozása](./media/how-to-create-schedule-trigger/monitor-trigger-runs.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ez a szakasz bemutatja, hogyan használhatja az Azure PowerShellt egy ütemezési eseményindító létrehozásához, indításához és figyeléséhez. Ha látni szeretné, hogy ez a minta működik, először tekintse meg a [rövid útmutató: Hozzon létre egy adat-előállító az Azure PowerShell használatával.](quickstart-create-data-factory-powershell.md) Ezután adja hozzá a következő kódot a fő metódushoz, amely létrehoz és elindít egy 15 percenként futó ütemezési eseményindítót. Az eseményindító egy **Adfv2QuickStartPipeline** nevű folyamathoz van társítva, amelyet a gyorsútmutató részeként hoz létre.

1. Hozzon létre egy **MyTrigger.json** nevű JSON-fájlt a C:\ADFv2QuickStartPSH\ mappában a következő tartalommal:

    > [!IMPORTANT]
    > A JSON-fájl mentése előtt állítsa a **startTime** elem értékét az aktuális UTC-időre. Állítsa be a **endTime** elem értékét egy órával az aktuális UTC-idő után.

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
    - Az eseményindító **típuseleme** "ScheduleTrigger" lesz.
    - A **frekvenciaelem** "Perc", az **intervallumelem** pedig 15. Ezért az eseményindító 15 percenként futtatja a folyamatot a kezdési és befejezési időpontok között.
    - A **endTime** elem egy órával a **startTime** elem értéke után. Ezért az eseményindító fut a folyamat 15 perc, 30 perc és 45 perccel a kezdési időpont után. Ne felejtse el frissíteni a kezdési időt az aktuális UTC-időpontra, és a befejezési időt egy órával a kezdési időpont utánra. 
    - Az eseményindító az **Adfv2QuickStartPipeline** folyamathoz van társítva. Ha több folyamatot szeretne társítani egy eseményindítóhoz, adjon hozzá további **folyamatreferencia** szakaszokat.
    - A rövid útmutatóban lévő folyamat két **paraméterértéket** vesz igénybe: **inputPath** és **outputPath**. Ezért ezeket a paramétereket az eseményindítóból adja át.

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

6.  Az eseményindító futtatása az Azure PowerShellben a **Get-AzDataFactoryV2TriggerRun** parancsmag használatával. Az eseményindító futtatásával kapcsolatos információk leéséhez rendszeresen hajtsa végre a következő parancsot. Frissítse a **TriggerRunStartedAfter** és **a TriggerRunStartedBefore** értékeket, hogy megfeleljenek az eseményindító definíciójának értékeinek:

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
    Az eseményindító-futtatások és a folyamat futtatása az Azure Portalon, [lásd: Folyamatfuttatások figyelése.](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)


## <a name="net-sdk"></a>.NET SDK
Ez a szakasz bemutatja, hogyan hozhat létre, indíthat el és figyelhet egy eseményindítót a .NET SDK segítségével. A minta működéséhez először tekintse át a [rövid útmutatót: Adatgyár létrehozása a .NET SDK használatával.](quickstart-create-data-factory-dot-net.md) Ezután adja hozzá a következő kódot a fő metódushoz, amely létrehoz és elindít egy 15 percenként futó ütemezési eseményindítót. Az eseményindító egy **Adfv2QuickStartPipeline** nevű folyamathoz van társítva, amelyet a gyorsútmutató részeként hoz létre.

15 percenként futó ütemezési eseményindító létrehozásához és elindításához adja hozzá a következő kódot a fő metódushoz:

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

Az eseményindítók futásának figyeléséhez `Console.WriteLine` adja hozzá a következő kódot a mintában szereplő utolsó utasítás előtt:

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

Az eseményindító-futtatások és a folyamat futtatása az Azure Portalon, [lásd: Folyamatfuttatások figyelése.](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)


## <a name="python-sdk"></a>Python SDK
Ez a szakasz bemutatja, hogyan használhatja a Python SDK-t egy eseményindító létrehozásához, indításához és figyeléséhez. Ha látni szeretné, hogy ez a minta működik, először tekintse meg a [rövid útmutató: Hozzon létre egy adatgyárat a Python SDK használatával.](quickstart-create-data-factory-python.md) Ezután adja hozzá a következő kódblokkot a "Pipeline run" kódblokk után a Python-parancsfájlban. Ez a kód létrehoz egy ütemezési eseményindítót, amely 15 percenként fut a megadott kezdési és befejezési időpontok között. Frissítse a **start_time** változót az aktuális UTC-időre, és a **end_time** változót egy órával az aktuális UTC-idő utánra.

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

Az eseményindító-futtatások és a folyamat futtatása az Azure Portalon, [lásd: Folyamatfuttatások figyelése.](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon
Egy Azure Resource Manager sablon használatával hozzon létre egy eseményindítót. Részletes útmutatást az [Azure-adat-előállító létrehozása Erőforrás-kezelő sablon használatával](quickstart-create-data-factory-resource-manager-template.md)című témakörben talál.  

## <a name="pass-the-trigger-start-time-to-a-pipeline"></a>Az eseményindító kezdési idejének áttöltése egy folyamathoz
Az Azure Data Factory 1-es verziója támogatja a particionált adatok olvasását vagy írását a következő rendszerváltozók használatával: **SliceStart**, **SliceEnd**, **WindowStart**és **WindowEnd**. Az Azure Data Factory jelenlegi verziójában ezt a viselkedést egy folyamatparaméter használatával érheti el. Az eseményindító kezdési időpontja és ütemezett időpontja a folyamatparaméter értékeként van beállítva. A következő példában az eseményindító ütemezett időpontja értékként kerül átadásra a folyamat **scheduledRunTime** paraméterének:

```json
"parameters": {
    "scheduledRunTime": "@trigger().scheduledTime"
}
```

## <a name="json-schema"></a>JSON-séma
A következő JSON-definíció bemutatja, hogyan hozhat létre ütemezési és ismétlődési ütemezési eseményindítót:

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
          "weekDays": [<<Monday-Sunday>>],
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
| **Ismétlődés** | Az eseményindítóhoz tartozó ismétlődési szabályokat megadó recurrence objektum. A recurrence objektum a **frequency**, **interval**, **endTime**, **count** és **schedule** elemeket támogatja. Recurrence objektum meghatározásakor a **frequency** elem megadása kötelező. A recurrence objektum többi elemének megadása nem kötelező. |
| **Frekvencia** | Az eseményindító ismétlődésének gyakorisági egysége. A támogatott értékek például: „minute”, „hour”, „day”, „week”, és „month” (percenként, óránként, naponta, hetente és havonta). |
| **interval** | Pozitív egész szám, amely az eseményindító futásának gyakoriságát meghatározó **frequency** érték időközét jelöli. Ha például az **interval** 3, a **frequency** pedig „week”, az eseményindító 3 hetente ismétlődik. |
| **Ütemezése** | Az eseményindító ismétlődési ütemezése. Egy megadott **frequency** értékkel rendelkező eseményindító az ismétlődést az ismétlődési ütemezés alapján módosítja. A **schedule** tulajdonságban az ismétlődéshez tartozó módosítások szerepelnek, amelyek alapjául a percek, órák, a hét napja, a hónap napjai és a hét száma szolgál.


### <a name="schema-defaults-limits-and-examples"></a>Séma alapértékei, korlátai és példái

| JSON-tulajdonság | Típus | Kötelező | Alapértelmezett érték | Érvényes értékek | Példa |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** | Sztring | Igen | None | ISO-8601 dátum-idők | `"startTime" : "2013-01-09T09:30:00-08:00"` |
| **Ismétlődés** | Objektum | Igen | None | Recurrence objektum | `"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **interval** | Szám | Nem | 1 | 1–1000 | `"interval":10` |
| **endTime** | Sztring | Igen | None | Egy jövőbeli időpontot jelölő dátum-idő érték. | `"endTime" : "2013-02-09T09:30:00-08:00"` |
| **Ütemezése** | Objektum | Nem | None | Schedule objektum | `"schedule" : { "minute" : [30], "hour" : [8,17] }` |

### <a name="starttime-property"></a>startTime tulajdonság
Az alábbi táblázatban látható, hogy a **startTime** tulajdonság hogyan irányítja a folyamatfuttatást:

| startTime értéke | Ismétlődés ütemezés nélkül | Ismétlődés ütemezéssel |
|:--- |:--- |:--- |
| Múltbeli kezdési időpont | Kiszámítja a kezdőidőpontot követő első jövőbeli végrehajtási időpontot, és abban az időpontban fut.<br/><br/>Az azt követő végrehajtásokat mindig az utolsó végrehajtási időpont alapján számítja ki.<br/><br/>Lásd a táblázat alatti példát. | Az eseményindító _nem indulhat hamarabb_ a megadott kezdési időpontnál. Az első előfordulás a kezdési időpontból kiszámított ütemezésen alapul.<br/><br/>Az azt követő végrehajtásokat az ismétlődési ütemezés alapján futtatja. |
| Jövőbeli vagy aktuális kezdési időpont | Egyszer fut a megadott kezdési időpontban.<br/><br/>Az azt követő végrehajtásokat mindig az utolsó végrehajtási időpont alapján számítja ki. | Az eseményindító _legkorábban_ a megadott kezdési időpontban indul el. Az első előfordulás a kezdési időpontból kiszámított ütemezésen alapul.<br/><br/>Az azt követő végrehajtásokat az ismétlődési ütemezés alapján futtatja. |

Tekintsünk meg egy példát arról, hogy mi történik, ha a kezdési időpont egy múltbeli időpont, ismétlődéssel, de ütemezés nélkül. Tegyük fel, hogy az aktuális időpont `2017-04-08 13:00`, a kezdési idő `2017-04-07 14:00`, és az eseményindító kétnaponta ismétlődik. (Az **ismétlődési** értéket úgy határozhatja meg, hogy a **gyakoriságtulajdonságot** "nap"-ra, az **intervallumtulajdonságot** pedig 2-re állítja.) Figyelje meg, hogy a **startTime** érték a múltban van, és az aktuális idő előtt következik be.

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
| **Óra** | A nap azon órái, amikor az eseményindító fut. | <ul><li>Egész szám</li><li>Egész számok tömbje</li></ul> |
| **weekDays** | A hét azon napjai, amelyeken az eseményindító fut. Az érték csak heti gyakorisággal adható meg. | <ul><li>Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday (Hétfő, Kedd, Szerda, Csütörtök, Péntek, Szombat, Vasárnap)</li><li>A nap értékek tömbje (a tömb maximális értéke 7)</li><li>A nap értékek nem tesznek különbséget a kis- és nagybetű között</li></ul> |
| **monthlyOccurrences** | A hónap azon napjai, amelyeken az eseményindító fut. Az érték csak havi gyakorisággal adható meg. | <ul><li>Havi **előfordulási** objektumok `{ "day": day,  "occurrence": occurrence }`tömbje: .</li><li>A **day** attribútum a hét azon napja, amelyen az eseményindító fut. Például a `{Sunday}` értékű **day** attribútummal rendelkező **monthlyOccurrences** tulajdonság a hónap minden vasárnapját jelenti. A **day** attribútum megadása kötelező.</li><li>Az **occurrence** attribútum a megadott **day** attribútum előfordulása a hónapban. Például a `{Sunday, -1}` értékű **day** és **occurrence** attribútumokkal rendelkező **monthlyOccurrences** tulajdonság a hónap utolsó vasárnapját jelenti. Az **occurrence** attribútum megadása nem kötelező.</li></ul> |
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
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Óránként fut le. Ez az eseményindító óránként fut le. A perceket a **startTime** értéke szabályozza, ha az érték meg van adva. Ha az érték nincs megadva, akkor a perceket a létrehozás ideje szabályozza. Ha például a kezdés időpontja vagy a létrehozás időpontja (amelyik alkalmazható) 12:25, akkor az eseményindító 00:25-kor, 01:25-kor, 02:25-kor stb. fut le, végül pedig 23:25-kor.<br/><br/>Ez az ütemezés egyenértékű azzal, hogy az eseményindító **gyakorisága** "óra", **1-es intervallumértékkel** és **ütemezés**nélkül.  Ez az ütemezés különböző **frequency** és **interval** értékekkel használható egyéb eseményindítók létrehozásához. Ha például a **gyakorisági** érték "hónap", az ütemezés csak havonta egyszer fut, nem pedig minden nap, amikor a **gyakorisági** érték "nap". |
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
Az eseményindítókról a [Folyamat végrehajtása és az eseményindítók](concepts-pipeline-execution-triggers.md#trigger-execution)című témakörben talál részletes információt.
