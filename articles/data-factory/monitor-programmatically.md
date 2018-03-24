---
title: Programozott módon figyelheti az Azure data factory |} Microsoft Docs
description: Ismerje meg, hogy egy adat-előállítóban folyamat figyelése különböző software development Kit (SDK-k) használatával.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: douglasl
ms.openlocfilehash: 87e69349245c5f67e23022e3a45ed798400e6a2c
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="programmatically-monitor-an-azure-data-factory"></a>Egy Azure data factory programozott módon figyelése
Ez a cikk ismerteti az adat-előállító adatcsatorna figyelése különböző software development Kit (SDK-k) használatával. 

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. A Data Factory szolgáltatásnak, amely általánosan elérhető (GA), 1 verziójának használatakor lásd: [figyelése és kezelése a Data Factory version1 adatcsatornák](v1/data-factory-monitor-manage-pipelines.md).

## <a name="data-range"></a>Az adattartománynak

Adat-előállító csak a folyamat futtatása az adatok 45 napig tárolja. Ha kérdezze le programozott módon fut a Data Factory csővezeték - adatokat például a PowerShell-paranccsal `Get-AzureRmDataFactoryV2PipelineRun` -nincsenek a választható maximális dátumok `LastUpdatedAfter` és `LastUpdatedBefore` paraméterek. De ha a lekérdezés az adatok az elmúlt év, például a lekérdezés nem ad vissza a hiba, de csak ad vissza a következő feldolgozási sorban az elmúlt 45 nap adatait.

Ha szeretné megőrizni a csővezeték adatok futtassa a több mint 45 nap, a saját diagnosztikai naplózás beállítása [Azure figyelő](monitor-using-azure-monitor.md).

## <a name="net"></a>.NET
A létrehozás és a .NET SDK használatával folyamat figyelése részletes útmutatást lásd: [tartalmazó data factory létrehozása és használata a .NET-feldolgozási folyamat](quickstart-create-data-factory-dot-net.md).

1. Adja hozzá a következő kódot a folyamatosan fut, amíg az adatok másolásának befejezné a folyamat állapotának ellenőrzéséhez.

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

2. Adja hozzá a következő kódot, hogy beolvassa másolási tevékenység részletek, például futtassa az adatok olvasása/írása.

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

A .NET SDK teljes dokumentációjáért lásd: [Data Factory .NET SDK-referencia](/dotnet/api/microsoft.azure.management.datafactory?view=azure-dotnet).

## <a name="python"></a>Python
A létrehozás és Python SDK használatával folyamat figyelése részletes útmutatást lásd: [tartalmazó data factory létrehozása és használata a Python-feldolgozási folyamat](quickstart-create-data-factory-python.md).

Figyelheti a folyamatot futtató, adja hozzá a következő kódot:

```python
#Monitor the pipeline run
time.sleep(30)
pipeline_run = adf_client.pipeline_runs.get(rg_name, df_name, run_response.run_id)
print("\n\tPipeline run status: {}".format(pipeline_run.status))
activity_runs_paged = list(adf_client.activity_runs.list_by_pipeline_run(rg_name, df_name, pipeline_run.run_id, datetime.now() - timedelta(1),  datetime.now() + timedelta(1)))
print_activity_run_details(activity_runs_paged[0])
```

Az a Python SDK teljes dokumentációjáért lásd: [Data Factory Python SDK hivatkozás](/python/api/overview/azure/datafactory?view=azure-python).

## <a name="rest-api"></a>REST API
A létrehozás és a REST API használatával folyamat figyelése részletes útmutatást lásd: [tartalmazó data factory létrehozása és a REST API használatával-feldolgozási folyamat](quickstart-create-data-factory-rest-api.md).
 
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

    ```PowerShell
    $request = "https://management.azure.com/subscriptions/${subsId}/resourceGroups/${resourceGroup}/providers/Microsoft.DataFactory/factories/${dataFactoryName}/pipelineruns/${runId}/activityruns?api-version=${apiVersion}&startTime="+(Get-Date).ToString('yyyy-MM-dd')+"&endTime="+(Get-Date).AddDays(1).ToString('yyyy-MM-dd')+"&pipelineName=Adfv2QuickStartPipeline"
    $response = Invoke-RestMethod -Method GET -Uri $request -Header $authHeader
    $response | ConvertTo-Json
    ```

Az REST API-t a teljes dokumentációjáért lásd: [Data Factory REST API-referenciában](/rest/api/datafactory/).

## <a name="powershell"></a>PowerShell
A létrehozás és a PowerShell használatával folyamat figyelése részletes útmutatást lásd: [tartalmazó data factory létrehozása és a PowerShell használatával-feldolgozási folyamat](quickstart-create-data-factory-powershell.md).

1. A folyamat futási állapotának folyamatos, az adatok másolásának befejezéséig tartó ellenőrzéséhez futtassa az alábbi szkriptet.

    ```powershell
    while ($True) {
        $run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

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
    $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result
    
    Write-Host "Activity 'Output' section:" -foregroundcolor "Yellow"
    $result.Output -join "`r`n"
    
    Write-Host "\nActivity 'Error' section:" -foregroundcolor "Yellow"
    $result.Error -join "`r`n"
    ```

A PowerShell-parancsmagok teljes dokumentációjáért lásd: [Data Factory PowerShell parancsmag-referencia](/powershell/module/azurerm.datafactoryv2/?view=azurermps-4.4.1).

## <a name="next-steps"></a>További lépések
Lásd: [figyelő folyamatok Azure-figyelővel](monitor-using-azure-monitor.md) cikkben tájékozódhat az adat-előállító adatcsatornák figyeléséhez használt Azure-figyelő. 

