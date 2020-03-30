---
title: Egy Azure-adat-előállító program programozott figyelése
description: Ismerje meg, hogyan figyelheti a folyamatokat egy adat-előállítókülönböző szoftverfejlesztői készletek (SDK) használatával.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/16/2018
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 43a31d588ff6616d7200d9773883ce5da570b100
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74927361"
---
# <a name="programmatically-monitor-an-azure-data-factory"></a>Egy Azure-adat-előállító program programozott figyelése
Ez a cikk azt ismerteti, hogyan figyelheti a folyamatot egy adat-előállító különböző szoftverfejlesztői készletek (SDK) használatával. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="data-range"></a>Adattartomány

A Data Factory csak 45 napig tárolja a folyamat futtatási adatait. Ha programozott módon lekérdezi a Data Factory folyamatadatainak futtatását `Get-AzDataFactoryV2PipelineRun` – például a PowerShell paranccsal –, nincsenek maximális dátumok a választható `LastUpdatedAfter` és `LastUpdatedBefore` a paraméterekhez. De ha például az elmúlt év adatait kérdezi le, a lekérdezés nem ad vissza hibát, hanem csak az elmúlt 45 nap folyamatfuttatási adatait adja vissza.

Ha 45 napnál tovább szeretné megtartani a folyamatfuttatási adatokat, állítsa be saját diagnosztikai naplózását az [Azure Monitor](monitor-using-azure-monitor.md)segítségével.

## <a name="net"></a>.NET
A .NET SDK használatával létrehozott és figyelt folyamat teljes forgatókönyve: [Adatgyár és folyamat létrehozása a .NET használatával](quickstart-create-data-factory-dot-net.md)című témakörben található.

1. Adja hozzá a következő kódot, hogy folyamatosan ellenőrizze a folyamat állapotát, amíg be nem fejezi az adatok másolását.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Adja hozzá a következő kódot, amely lekéri a másolási tevékenység futtatásának részleteit, például az olvasott/írt adatok méretét.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");
   
    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
    if (pipelineRun.Status == "Succeeded")
        Console.WriteLine(activityRuns.First().Output);
    else
        Console.WriteLine(activityRuns.First().Error);
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

A .NET SDK teljes dokumentációját a [Data Factory .NET SDK hivatkozáscímű témakörben találja.](/dotnet/api/microsoft.azure.management.datafactory?view=azure-dotnet)

## <a name="python"></a>Python
A folyamat Python SDK használatával történő létrehozásának és figyelésének teljes forgatókönyvét az [Adatgyár és folyamat létrehozása python használatával című](quickstart-create-data-factory-python.md)témakörben található.

A folyamat futásának figyeléséhez adja hozzá a következő kódot:

```python
# Monitor the pipeline run
time.sleep(30)
pipeline_run = adf_client.pipeline_runs.get(
    rg_name, df_name, run_response.run_id)
print("\n\tPipeline run status: {}".format(pipeline_run.status))
activity_runs_paged = list(adf_client.activity_runs.list_by_pipeline_run(
    rg_name, df_name, pipeline_run.run_id, datetime.now() - timedelta(1),  datetime.now() + timedelta(1)))
print_activity_run_details(activity_runs_paged[0])
```

A Python SDK teljes dokumentációját a [Data Factory Python SDK-referencia című témakörben találja.](/python/api/overview/azure/datafactory?view=azure-python)

## <a name="rest-api"></a>REST API
A folyamat REST API használatával történő létrehozásának és figyelésének teljes forgatókönyvét az [Adatgyár és folyamat létrehozása REST API használatával című témakörben](quickstart-create-data-factory-rest-api.md)található.
 
1. A folyamat futási állapotának folyamatos, az adatok másolásának befejezéséig tartó ellenőrzéséhez futtassa az alábbi szkriptet.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}?api-version=${apiVersion}"
    while ($True) {
        $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
        Write-Host  "Pipeline run status: " $response.Status -foregroundcolor "Yellow"

        if ($response.Status -eq "InProgress") {
            Start-Sleep -Seconds 15
        }
        else {
            $response | ConvertTo-Json
            break
        }
    }
    ```
2. A másolási tevékenység futtatási részleteinek (például az írt vagy olvasott adatok méretének) lekéréséhez futtassa az alábbi szkriptet.

    ```powershell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}/activityruns?api-version=${apiVersion}&startTime="+(Get-Date).ToString('yyyy-MM-dd')+"&endTime="+(Get-Date).AddDays(1).ToString('yyyy-MM-dd')+"&pipelineName=Adfv2QuickStartPipeline"
    $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
    $response | ConvertTo-Json
    ```

A REST API teljes dokumentációját a [Data Factory REST API referencia című témakörben találja.](/rest/api/datafactory/)

## <a name="powershell"></a>PowerShell
A PowerShell használatával egy folyamat létrehozásának és figyelésének teljes forgatókönyvét az [Adat-előállító és folyamat létrehozása a PowerShell használatával című témakörben található.](quickstart-create-data-factory-powershell.md)

1. A folyamat futási állapotának folyamatos, az adatok másolásának befejezéséig tartó ellenőrzéséhez futtassa az alábbi szkriptet.

    ```powershell
    while ($True) {
        $run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 30
    }
    ```
2. A másolási tevékenység futtatási részleteinek (például az írt vagy olvasott adatok méretének) lekéréséhez futtassa az alábbi szkriptet.

    ```powershell
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```

A PowerShell-parancsmagok teljes dokumentációját a [Data Factory PowerShell-parancsmagok hivatkozási útmutatójában találja.](/powershell/module/az.datafactory)

## <a name="next-steps"></a>További lépések
Tekintse [meg a folyamatok figyelése az Azure Monitor cikkhasználatával,](monitor-using-azure-monitor.md) hogy miként figyelheti az Azure Monitor ta-folyamatait. 

