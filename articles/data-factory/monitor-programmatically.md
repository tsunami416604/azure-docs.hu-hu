---
title: Programozott módon figyelheti az Azure data factory |} A Microsoft Docs
description: Megismerkedhet az adat-előállító folyamat monitorozása a különböző szoftverfejlesztői készletek (SDK-k) használatával.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/16/2018
author: gauravmalhot
ms.author: gamal
manager: craigg
ms.openlocfilehash: 035e12da67d28e8e3fb46ac295717dd6b579922c
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58486613"
---
# <a name="programmatically-monitor-an-azure-data-factory"></a>Programozott módon az Azure data factory monitorozása
Ez a cikk ismerteti az adat-előállító folyamat monitorozása a különböző szoftverfejlesztői készletek (SDK-k) használatával. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="data-range"></a>Tartomány

A Data Factory csak 45 nap folyamatfuttatás adatokat tárolja. Amikor lekérdezheti, ha programozott módon a Data Factory a folyamatfuttatások - adatait például a PowerShell-paranccsal `Get-AzDataFactoryV2PipelineRun` – nincsenek a választható maximális dátumok `LastUpdatedAfter` és `LastUpdatedBefore` paramétereket. De ha lekérdezési adatok az elmúlt év, például a lekérdezés nem ad vissza hibát, de csak akkor adja folyamat futtatási adatait az elmúlt 45 nap.

Ha szeretne folyamatfuttatás adatok legalább 45 napig maradnak, állítsa be a saját a diagnosztikai naplózás [Azure Monitor](monitor-using-azure-monitor.md).

## <a name="net"></a>.NET
Létrehozás és figyelés .NET SDK használatával egy folyamat részletes útmutatást lásd: [és folyamat adat-előállító létrehozása .NET használatával](quickstart-create-data-factory-dot-net.md).

1. Adja hozzá a következő kódot a folyamat az adatok másolásának befejezéséig tartó futási állapotának folyamatos ellenőrzéséhez.

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

2. Adja hozzá a következő kódot, hogy beolvassa másolási tevékenység futtatási részleteit, például az írt vagy olvasott adatok méretét.

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

A .NET SDK teljes dokumentációját lásd: [Data Factory .NET SDK-referenciában](/dotnet/api/microsoft.azure.management.datafactory?view=azure-dotnet).

## <a name="python"></a>Python
Létrehozásával és a egy folyamatot a Python SDK használatával figyelési leírását lásd: [hozzon létre egy adat-előállító és folyamat használatával Python](quickstart-create-data-factory-python.md).

A folyamatfuttatás monitorozásához adja hozzá a következő kódot:

```python
#Monitor the pipeline run
time.sleep(30)
pipeline_run = adf_client.pipeline_runs.get(rg_name, df_name, run_response.run_id)
print("\n\tPipeline run status: {}".format(pipeline_run.status))
activity_runs_paged = list(adf_client.activity_runs.list_by_pipeline_run(rg_name, df_name, pipeline_run.run_id, datetime.now() - timedelta(1),  datetime.now() + timedelta(1)))
print_activity_run_details(activity_runs_paged[0])
```

A Python SDK teljes dokumentációját lásd: [Data Factory Python SDK-leírás](/python/api/overview/azure/datafactory?view=azure-python).

## <a name="rest-api"></a>REST API
Létrehozásával és a egy folyamatot a REST API használatával figyelési leírását lásd: [és folyamat létrehozása a data factory REST API használatával](quickstart-create-data-factory-rest-api.md).
 
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

A REST API-t teljes dokumentációjáért lásd: [Data Factory REST API-referencia](/rest/api/datafactory/).

## <a name="powershell"></a>PowerShell
Létrehozásának és a egy PowerShell-lel folyamat figyelését részletes útmutatást lásd: [és folyamat adat-előállító létrehozása PowerShell használatával](quickstart-create-data-factory-powershell.md).

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

A PowerShell-parancsmagok teljes dokumentációjáért lásd: [Data Factory PowerShell-parancsmagok leírása](/powershell/module/az.datafactory).

## <a name="next-steps"></a>További lépések
Lásd: [-folyamatok figyelése az Azure Monitor használatával](monitor-using-azure-monitor.md) a cikkben megismerheti az Azure Monitor használatával a Data Factory-folyamatok figyelése. 

