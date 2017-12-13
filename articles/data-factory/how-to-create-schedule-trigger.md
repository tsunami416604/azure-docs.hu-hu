---
title: "Eseményindítók létrehozása az Azure Data Factory |} Microsoft Docs"
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
ms.date: 12/11/2017
ms.author: shlo
ms.openlocfilehash: eed286c01604ab0e9ac2113d56cbce268503668d
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2017
---
# <a name="how-to-create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>Egy eseményindítót, amelyek ütemezés szerint fut, a folyamat létrehozása
Ez a cikk nyújt tájékoztatást létrehozását, indítsa el, és egy eseményindító megfigyelését. Eseményindítók kapcsolatos általános információkért lásd: [csővezeték-végrehajtási és eseményindítók](concepts-pipeline-execution-triggers.md).

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. Ha a Data Factory szolgáltatás általánosan elérhető 1. verzióját használja, lásd a [Data Factory 1. verzió használatának első lépéseit](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


## <a name="use-azure-powershell"></a>Azure PowerShell használatával
Ez a szakasz bemutatja, hogyan használhatja az Azure Powershellt létrehozását, indítsa el, és egy eseményindító megfigyelését. Ha azt szeretné, hogy ez a minta működő, először halad át a [gyors üzembe helyezés: hozzon létre egy adat-előállítót az Azure PowerShell](quickstart-create-data-factory-powershell.md). Ezt követően adja hozzá a következő kódot a fő metódus, amely a metódus létrehozza és elindítja egy ütemezés eseményindítót, amely 15 percenként fut. Az eseményindító társítva egy folyamat (**Adfv2QuickStartPipeline**) az Ön által létrehozott a gyors üzembe helyezés részeként.

1. Hozzon létre egy JSON fájlt MyTrigger.json a C:\ADFv2QuickStartPSH\ mappában, a következő tartalommal: 

    > [!IMPORTANT]
    > Állítsa be **startTime** a jelenlegi UTC-időre és **endTime** egy óra korábbi a jelenlegi UTC-idő a JSON-fájl mentése előtt.

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
    - A **típus** az eseményindító értéke **ScheduleTrigger**. 
    - A **gyakoriság** értéke **perc** és **időköz** értéke **15**. Ezért az eseményindító a feldolgozási sor 15 percenként fut. kezdési és befejezési időpontjai között. 
    - A **endTime** van egy órával azután a **startTime**, így a trigger futtatja a feldolgozási sor 15 perc, 30 percet, és a kezdő időpont után 45 percig. Ne felejtse el frissíteni a kezdő időpont a jelenlegi UTC idő szerint, és az EndTime megadása a startTime elmúlt egy óra.  
    - Az eseményindító társítva van a **Adfv2QuickStartPipeline** folyamat. Több folyamatok társítandó eseményindítót, adja hozzá több **pipelineReference** szakaszok. 
    - A kimenetátirányítási mechanizmusával a gyors üzembe helyezési időt vesz igénybe, két **paraméterek**. Ezek a paraméterek értékeit, ezért a eseményindítóval át. 
2. Egy eseményindító létrehozása a **Set-AzureRmDataFactoryV2Trigger** parancsmag.

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
3. Győződjön meg arról, hogy az állapot az eseményindító **leállítva** használatával a **Get-AzureRmDataFactoryV2Trigger** parancsmag. 

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

## <a name="use-net-sdk"></a>A .NET SDK használata
Ez a szakasz bemutatja, hogyan használhatja a .NET SDK létrehozását, indítsa el, és egy eseményindító megfigyelését. Ha azt szeretné, hogy működik-e ezt a kódot, először halad át a [.NET SDK használatával adat-előállító létrehozása a gyors üzembe helyezés](quickstart-create-data-factory-dot-net.md). Ezt követően adja hozzá a következő kódot a fő metódus, amely a metódus létrehozza és elindítja egy ütemezés eseményindítót, amely 15 percenként fut. Az eseményindító társítva egy folyamat (**Adfv2QuickStartPipeline**) az Ön által létrehozott a gyors üzembe helyezés részeként. 

```csharp
            //create the trigger
            Console.WriteLine("Creating the trigger");

            // set the start time to the current UTC time
            DateTime startTime = DateTime.UtcNow;

            // specify values for the inputPath and outputPath parameters
            Dictionary<string, object> pipelineParameters = new Dictionary<string, object>();
            pipelineParameters.Add("inputPath", "adftutorial/input");
            pipelineParameters.Add("outputPath", "adftutorial/output");

            // create a schedule trigger
            string triggerName = "MyTrigger";
            ScheduleTrigger myTrigger = new ScheduleTrigger()
            {
                Pipelines = new List<TriggerPipelineReference>()
                {
                    // associate the Adfv2QuickStartPipeline pipeline with the trigger
                    new TriggerPipelineReference()
                    {
                        PipelineReference = new PipelineReference(pipelineName),
                        Parameters = pipelineParameters,
                    }
                },
                Recurrence = new ScheduleTriggerRecurrence()
                {
                    // set the start time to current UTC time and the end time to be one hour after.
                    StartTime = startTime,
                    TimeZone = "UTC",
                    EndTime = startTime.AddHours(1),
                    Frequency = RecurrenceFrequency.Minute,
                    Interval = 15,
                }
            };

            // now, create the trigger by invoking CreateOrUpdate method. 
            TriggerResource triggerResource = new TriggerResource()
            {
                Properties = myTrigger
            };
            client.Triggers.CreateOrUpdate(resourceGroup, dataFactoryName, triggerName, triggerResource);

            //start the trigger
            Console.WriteLine("Starting the trigger");
            client.Triggers.Start(resourceGroup, dataFactoryName, triggerName);

```

Futtatás eseményindítót figyelő, vegye fel a következő kódot az utolsó `Console.WriteLine` utasítást: 

```csharp
            // Check the trigger runs every 15 minutes
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

Eseményindító fut/folyamat fut az Azure portálon figyeléséről lásd: [figyelő folyamat fut](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)

## <a name="use-python-sdk"></a>Python SDK használata
Ez a szakasz bemutatja, hogyan használható Python SDK létrehozása, indítsa el, és egy eseményindító figyelése. Ha azt szeretné, hogy működik-e ezt a kódot, először halad át a [Python SDK használatával adat-előállító létrehozása a gyors üzembe helyezés](quickstart-create-data-factory-python.md). Adja hozzá a következő kódrészletet a "figyelése a Futtatás futószalag" kódblokk, a python-parancsfájl után. Ez a kód egy eseményindítót hoz létre ütemezés, amely 15 percenként fut. az adott kezdő és befejező időpontok között. Frissítse a start_time a jelenlegi UTC-idő és a end_time korábbi a jelenlegi UTC idő szerint egy óra. 

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

    # start the trigger
    adf_client.triggers.start(rg_name, df_name, tr_name)
```
Eseményindító fut/folyamat fut az Azure portálon figyeléséről lásd: [figyelő folyamat fut](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)

## <a name="use-resource-manager-template"></a>Használja a Resource Manager-sablon
Az Azure Resource Manager-sablon segítségével hozható létre eseményindító. Részletes útmutatásért lásd: [létrehozása egy Resource Manager-sablon használata az Azure data factory](quickstart-create-data-factory-resource-manager-template.md).  

## <a name="pass-the-trigger-start-time-to-a-pipeline"></a>Az eseményindító kezdési ideje átadni egy folyamat
1-es verziójával Azure Data Factory Olvasás vagy írás a particionált SliceStart/SliceEnd/WindowStart/WindowEnd rendszerváltozók használatával támogatott. A 2-es verzióját ez a viselkedés a paraméter értékének csővezeték paraméter és a trigger elem ütemezett időpont/kezdete segítségével érhet el. A következő példában az eseményindítót az ütemezett időpont kerülnek értékként a feldolgozási sor paraméter scheduledRunTime. 

```json
"parameters": {
    "scheduledRunTime": "@trigger().scheduledTime"
}
```    
További információkért lásd: [olvasására vagy írására, hogyan particionálva adatok](how-to-read-write-partitioned-data.md).

## <a name="next-steps"></a>Következő lépések
Eseményindítók kapcsolatos részletes információkért lásd: [csővezeték-végrehajtási és eseményindítók](concepts-pipeline-execution-triggers.md#triggers).