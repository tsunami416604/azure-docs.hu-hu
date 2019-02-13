---
title: SSIS-csomag futtatása az SSIS-csomag végrehajtása tevékenység – Azure |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan futtathat egy SQL Server Integration Services (SSIS) csomag az Azure Data Factory-folyamatot az SSIS-csomag végrehajtása tevékenységek segítségével.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 02/12/2019
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 2a948a75ce3f6c21d7e92e3e1ccb1ef98dbe2ea0
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56114382"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Futtassa a Azure Data Factory SSIS-csomag végrehajtása tevékenysége egy SSIS-csomag
Ez a cikk ismerteti, hogyan futtathat egy SSIS-csomag az Azure Data Factory (ADF) folyamat az SSIS-csomag végrehajtása tevékenységek segítségével. 

## <a name="prerequisites"></a>Előfeltételek

Hozzon létre egy Azure-SSIS integrációs modul (IR), ha nem rendelkezik a részletes utasításait követve már a [oktatóanyag: SSIS csomagok üzembe helyezése az Azure-bA](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="run-a-package-in-the-azure-portal"></a>A csomag futtatása az Azure Portalon
Ebben a szakaszban használhatja az ADF felhasználói felületének (UI) / alkalmazás létrehozása az ADF, amelyen az SSIS-csomag végrehajtása SSIS-csomag tevékenységgel rendelkező folyamat.

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Az SSIS-csomag végrehajtása tevékenységgel rendelkező folyamat létrehozása
Ebben a lépésben a ADF felhasználói felületén vagy alkalmazás-folyamatok létrehozására használhatja. SSIS-csomag végrehajtása tevékenység hozzáadása a folyamathoz, és konfigurálja úgy, hogy az SSIS-csomag futtatása. 

1. A kezdőlapon ADF áttekintése és az Azure Portalon, kattintson a a **létrehozás és Monitorozás** csempére kattintva indítsa el az ADF felhasználói felületén vagy alkalmazás külön lapon. 

   ![Data factory kezdőlap](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)

   Az a **első lépések** kattintson **folyamat létrehozása**: 

   ![Első lépések lap](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)

2. Az a **tevékenységek** eszközkészletben bontsa ki a **általános**, majd áthúzással egy **SSIS-csomag végrehajtása** tevékenységet a folyamat tervezőfelületére. 

   ![A Tervező felületére húzza az SSIS-csomag végrehajtása tevékenység](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

3. Az a **általános** SSIS-csomag végrehajtása tevékenység lapján, adjon meg egy nevet és leírást a tevékenység. Állítsa be a választható időkorlátja, és ismételje meg a értékeket.

   ![Az Általános lapon tulajdonságainak megadása](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

4. Az a **beállítások** SSIS-csomag végrehajtása tevékenység fülre, válassza ki az Azure-SSIS integrációs modul, amely van társítva az SSISDB-adatbázis, a csomag telepítése. Ha a csomag az 32 bites futtatókörnyezet futtatása van szüksége, ellenőrizze a **32 bites futtatókörnyezet** jelölőnégyzetet. A **naplózási szint**, válassza ki a csomag végrehajtása naplózásának előre meghatározott köre. Ellenőrizze a **testre szabott** jelölőnégyzetet, ha szeretné inkább adja meg a testre szabott naplózási nevét. Az Azure-SSIS integrációs modul futtatásakor, és a **manuális bejegyzések** jelölőnégyzet nincs bejelölve, keresse meg és válassza ki a meglévő mappákat és projektek/csomagok/környezetek SSISDB. Kattintson a **frissítése** beolvasni az SSISDB, az újonnan hozzáadott mappák/projektek/csomagok/környezetek, így elérhetők tallózása és kiválasztása gombra. 

   ![A beállítások lapon - automatikus tulajdonságainak megadása](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

   Ha az Azure-SSIS integrációs modul helyének nem fut, vagy a **manuális bejegyzések** jelölőnégyzet be van jelölve, megadhatja az SSISDB a csomag és a környezet elérési útja a következő formátumok: `<folder name>/<project name>/<package name>.dtsx` és `<folder name>/<environment name>`.

   ![A beállítások lapon - manuális tulajdonságainak beállítása](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings2.png)

5. A a **SSIS paraméterek** lapon SSIS-csomag végrehajtása tevékenységeit, ha az Azure-SSIS integrációs modul fut, és a **manuális bejegyzések** jelölőnégyzetet **beállítások** lapon nincs bejelölve, a meglévő SSIS-paraméterek az SSISDB a kiválasztott projekt/csomag jelenik meg, hogy az értékek rendelhet hozzájuk. Ellenkező esetben adhatja őket egyenként az értéket rendelni őket manuálisan – ellenőrizze, hogy azok léteznek, és pontosan írja be a csomag végrehajtás sikeres. Dinamikus tartalom is értékekre kifejezések, functions, az ADF rendszerváltozók és ADF folyamat paraméterek és változók használatával adhat hozzá.

   ![Az SSIS-paraméterek lapon tulajdonságainak megadása](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-ssis-parameters.png)

6. Az a **kezelők** lapon SSIS-csomag végrehajtása tevékenységeit, ha az Azure-SSIS integrációs modul fut, és a **manuális bejegyzések** jelölőnégyzetet **beállítások** lapon nincs bejelölve, a meglévő kezelők az SSISDB a kiválasztott projekt/csomag jelenik meg, hogy az értékek rendelhet hozzájuk. Ellenkező esetben adhatja őket egyenként az értéket rendelni őket manuálisan – ellenőrizze, hogy azok léteznek, és pontosan írja be a csomag végrehajtás sikeres. Dinamikus tartalom is értékekre kifejezések, functions, az ADF rendszerváltozók és ADF folyamat paraméterek és változók használatával adhat hozzá.

   ![A kezelők lapon tulajdonságainak megadása](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-connection-managers.png)

7. Az a **tulajdonság felülbírálja** lapon SSIS-csomag végrehajtása tevékenységeit, megadhatja az elérési utak a meglévő tulajdonságok a kiválasztott csomag az SSISDB egyenként értéket rendelni őket manuálisan – ellenőrizze, hogy azok léteznek, és vannak, a csomag végrehajtása sikeres, például a helyes megadása a felhasználói változó értékének felülbírálásához, adja meg az elérési út a következő formátumban: `\Package.Variables[User::YourVariableName].Value`. Dinamikus tartalom is értékekre kifejezések, functions, az ADF rendszerváltozók és ADF folyamat paraméterek és változók használatával adhat hozzá.

   ![A tulajdonság felülbírálja lapon tulajdonságainak megadása](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-property-overrides.png)

8. A folyamat konfiguráció érvényesítéséhez kattintson **ellenőrzése** az eszköztáron. A **Folyamatérvényesítési jelentés** bezárásához kattintson a **>>** jelre.

9. A folyamat közzétételére az ADF kattintva **összes közzététele** gombra. 

### <a name="run-the-pipeline"></a>A folyamat futtatása
Ebben a lépésben a folyamat futtatásának aktiválása. 

1. Folyamat futásának aktiválásához kattintson **eseményindító** az eszköztáron, majd kattintson a **Aktiválás most**. 

   ![Aktiválás most](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. A **Folyamatfuttatás** ablakban kattintson a **Befejezés** gombra. 

### <a name="monitor-the-pipeline"></a>A folyamat figyelése

1. Váltson a bal oldali **Monitorozás** lapra. Megjelenik a folyamat futtatása és egyéb információk (például a Futtatás kezdő időpont) és annak állapotát. A nézet frissítéséhez kattintson a **Frissítés** parancsra.

   ![Folyamatfuttatások](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

2. Kattintson az **Actions** (Műveletek) oszlopban található **View Activity Runs** (Tevékenységfuttatások megtekintése) hivatkozásra. Láthatja, hogy csak egy tevékenységfuttatás, a folyamat egyetlen tevékenységet (SSIS-csomag végrehajtása tevékenység) tartalmaz.

   ![Tevékenységfuttatások](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

3. Futtathatja a következő **lekérdezés** szemben az SSISDB adatbázis-az Azure SQL Serveren, ellenőrizze, hogy a csomag végrehajtása. 

   ```sql
   select * from catalog.executions
   ```

   ![Csomag végrehajtásának ellenőrzése](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

4. Az SSISDB végrehajtási azonosító beszerzése a folyamatfuttatás tevékenység kimenetét, és átfogóbb feladatvégrehajtási naplók és hibaüzenetek az ssms-ben az azonosító használatával is.

   ![A végrehajtási azonosító lekérése](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

### <a name="schedule-the-pipeline-with-a-trigger"></a>Ütemezés a folyamat egy eseményindítóval

Is létrehozhat ütemezett eseményindítóként a folyamathoz, hogy a folyamat fut egy ütemezés szerint (óránként, naponta stb.). Egy vonatkozó példáért lásd: [- adat-előállító létrehozása a Data Factory felhasználói felülete](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="run-a-package-with-powershell"></a>A csomag futtatása a PowerShell-lel
Ebben a szakaszban az Azure PowerShell használatával hozhat létre az ADF-folyamatot az SSIS-csomag végrehajtása tevékenység, amelyen az SSIS-csomag. 

A legújabb Azure PowerShell-modulok telepítése a részletes utasításait követve [telepítése és konfigurálása az Azure PowerShell-lel](/powershell/azure/azurerm/install-azurerm-ps).

### <a name="create-an-adf-with-azure-ssis-ir"></a>Azure-SSIS integrációs modul létrehozása az ADF használatával
Használhatja egy meglévő ADF, amely már rendelkezik Azure-SSIS integrációs modul üzembe helyezett vagy hozzon létre egy új ADF Azure-SSIS integrációs modul részletes utasításait a [oktatóanyag: SSIS csomagok üzembe helyezése az Azure Powershellen keresztül](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure-powershell).

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Az SSIS-csomag végrehajtása tevékenységgel rendelkező folyamat létrehozása 
Ebben a lépésben létrehoz egy folyamatot egy SSIS-csomag végrehajtása tevékenységgel. A tevékenység futtatása az SSIS-csomag. 

1. Hozzon létre egy JSON-fájlt **RunSSISPackagePipeline.json** a a **C:\ADF\RunSSISPackage** mappában az alábbi példához hasonló tartalommal:

   > [!IMPORTANT]
   > A fájl mentése előtt cserélje le a kijelölendő objektumok nevét, leírását és az elérési utak tulajdonság és a paraméterértékeket, jelszavakat és más változók értékeinek. 

   ```json
   {
       "name": "RunSSISPackagePipeline",
       "properties": {
           "activities": [{
               "name": "mySSISActivity",
               "description": "My SSIS package/activity description",
               "type": "ExecuteSSISPackage",
               "typeProperties": {
                   "connectVia": {
                       "referenceName": "myAzureSSISIR",
                       "type": "IntegrationRuntimeReference"
                   },
                   "runtime": "x64",
                   "loggingLevel": "Basic",
                   "packageLocation": {
                       "packagePath": "FolderName/ProjectName/PackageName.dtsx"
                   },
                   "environmentPath": "FolderName/EnvironmentName",
                   "projectParameters": {
                       "project_param_1": {
                           "value": "123"
                       }
                   },
                   "packageParameters": {
                       "package_param_1": {
                           "value": "345"
                       }
                   },
                   "projectConnectionManagers": {
                       "MyAdonetCM": {
                           "userName": {
                               "value": "sa"
                           },
                           "passWord": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "abc"
                               }
                           }
                       }
                   },
                   "packageConnectionManagers": {
                       "MyOledbCM": {
                           "userName": {
                               "value": "sa"
                           },
                           "passWord": {
                               "value": {
                                   "type": "SecureString",
                                   "value": "def"
                               }
                           }
                       }
                   },
                   "propertyOverrides": {
                       "\\Package.MaxConcurrentExecutables": {
                           "value": 8,
                           "isSensitive": false
                       }
                   }
               },
               "policy": {
                   "timeout": "0.01:00:00",
                   "retry": 0,
                   "retryIntervalInSeconds": 30
               }
           }]
       }
   }
   ```

2. Az Azure PowerShellben váltson a `C:\ADF\RunSSISPackage` mappát.

3. A folyamat létrehozásához **RunSSISPackagePipeline**futtassa a **Set-AzureRmDataFactoryV2Pipeline** parancsmagot.

   ```powershell
   $DFPipeLine = Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                                  -ResourceGroupName $ResGrp.ResourceGroupName `
                                                  -Name "RunSSISPackagePipeline"
                                                  -DefinitionFile ".\RunSSISPackagePipeline.json"
   ```

   Itt látható a minta kimenete:

   ```
   PipelineName      : Adfv2QuickStartPipeline
   ResourceGroupName : <resourceGroupName>
   DataFactoryName   : <dataFactoryName>
   Activities        : {CopyFromBlobToBlob}
   Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
   ```

### <a name="run-the-pipeline"></a>A folyamat futtatása
Használja a **Invoke-AzureRmDataFactoryV2Pipeline** parancsmagot futtathatja a folyamatot. A parancsmag visszaadja a folyamat futásának azonosítóját a későbbi monitorozás céljából.

```powershell
$RunId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline"></a>A folyamat figyelése

A folyamat futási állapotának folyamatos, az adatok másolásának befejezéséig tartó ellenőrzéséhez futtassa az alábbi PowerShell-szkriptet. Másolja/illessze be az alábbi szkriptet a PowerShell-ablakba, majd nyomja le az Enter billentyűt. 

```powershell
while ($True) {
    $Run = Get-AzureRmDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName `
                                               -DataFactoryName $DataFactory.DataFactoryName `
                                               -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

Is figyelemmel kísérheti a folyamat az Azure portal használatával. Lépésenkénti útmutatásért lásd: [a folyamat figyelése](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="schedule-the-pipeline-with-a-trigger"></a>Ütemezés a folyamat egy eseményindítóval
Az előző lépésben a folyamat igény szerinti futtatta. A folyamatok futtatása ütemezés szerint (óránként, naponta stb.) az ütemezési eseményindító is létrehozhat.

1. Hozzon létre egy JSON-fájlt **MyTrigger.json** a **C:\ADF\RunSSISPackage** mappában az alábbi tartalommal: 

   ```json
   {
       "properties": {
           "name": "MyTrigger",
           "type": "ScheduleTrigger",
           "typeProperties": {
               "recurrence": {
                   "frequency": "Hour",
                   "interval": 1,
                   "startTime": "2017-12-07T00:00:00-08:00",
                   "endTime": "2017-12-08T00:00:00-08:00"
               }
           },
           "pipelines": [{
               "pipelineReference": {
                   "type": "PipelineReference",
                   "referenceName": "RunSSISPackagePipeline"
               },
               "parameters": {}
           }]
       }
   }    
   ```
2. A **Azure PowerShell-lel**, váltson át a **C:\ADF\RunSSISPackage** mappát.
3. Futtassa a **Set-AzureRmDataFactoryV2Trigger** parancsmagot, amely az eseményindítót hoz létre. 

   ```powershell
   Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                   -DataFactoryName $DataFactory.DataFactoryName `
                                   -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
   ```
4. Alapértelmezés szerint az eseményindító leállított állapotban van. Az eseményindító elindításához futtassa a **Start-AzureRmDataFactoryV2Trigger** parancsmagot. 

   ```powershell
   Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                     -DataFactoryName $DataFactory.DataFactoryName `
                                     -Name "MyTrigger" 
   ```
5. Győződjön meg arról, hogy az eseményindító futtatásával el van-e a **Get-AzureRmDataFactoryV2Trigger** parancsmagot. 

   ```powershell
   Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                   -DataFactoryName $DataFactoryName `
                                   -Name "MyTrigger"     
   ```    
6. Futtassa a következő parancsot a következő óra elteltével. Például ha az aktuális idő 3:25-kor (UTC), futtassa a parancsot, 4 Órakor (UTC). 
    
   ```powershell
   Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                      -DataFactoryName $DataFactoryName `
                                      -TriggerName "MyTrigger" `
                                      -TriggerRunStartedAfter "2017-12-06" `
                                      -TriggerRunStartedBefore "2017-12-09"
   ```

   Futtathatja a következő lekérdezés az SSISDB-adatbázison annak ellenőrzéséhez, hogy az Azure SQL Serveren a csomag végrehajtása. 

   ```sql
   select * from catalog.executions
   ```

## <a name="next-steps"></a>További lépések
Tekintse meg a következő blogbejegyzésben található:
-   [Végezzen modernizálást és az ETL/ELT munkafolyamatok SSIS tevékenységek az ADF folyamatok kiterjesztése](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)
