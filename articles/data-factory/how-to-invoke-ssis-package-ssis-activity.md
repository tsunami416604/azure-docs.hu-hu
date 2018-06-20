---
title: SSIS-csomag futtatás végrehajtása SSIS csomag tevékenység - Azure |} Microsoft Docs
description: Ez a cikk ismerteti, hogyan egy SQL Server Integration Services (SSIS) csomag futtatásához egy Azure Data Factory-folyamathoz SSIS-csomag hajtható végre tevékenységgel.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 05/25/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: ffe65260e73aa2daa4ab63840a00076000f8e6a8
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264905"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>Futtassa a végrehajtás SSIS csomag tevékenység Azure Data Factory egy SSIS-csomagot
Ez a cikk ismerteti, hogyan egy SSIS-csomag futtatása az Azure Data Factory-folyamat a tevékenység végrehajtása SSIS-csomag használatával. 

> [!NOTE]
> Ez a cikk a Data Factory 2. verziójára vonatkozik, amely jelenleg előzetes verzióban érhető el. SSIS-csomag hajtható végre tevékenység nem érhető el a Data Factory szolgáltatásnak, amely általánosan elérhető (GA) 1 verzióját. Egy alternatív módszert egy SSIS-csomagot a Data Factory szolgáltatásnak 1 verziójával, lásd: [futtatása SSIS-csomagok használata tárolt eljárási tevékenység az 1-es](v1/how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>Előfeltételek

### <a name="azure-sql-database"></a>Azure SQL Database 
Ez a cikk a forgatókönyv, amelyen az SSIS-katalógus Azure SQL-adatbázis. Egy Azure SQL felügyelt példányát (előzetes verzió) is használhatja.

## <a name="create-an-azure-ssis-integration-runtime"></a>Azure SSIS integrációs modul létrehozása
Ha még nem rendelkezik a részletes utasításokat a következő, hozzon létre egy Azure-SSIS-integrációs futásidejű a [oktatóanyag: telepítése SSIS-csomagok](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="data-factory-ui-azure-portal"></a>Data Factory felhasználói felület (Azure-portál)
Ebben a szakaszban a Data Factory felhasználói felület létrehozása a Data Factory-folyamat fut egy SSIS-csomag hajtható végre SSIS-csomag tevékenység használhatja.

### <a name="create-a-data-factory"></a>Data factory létrehozása
Első lépés az adat-előállító létrehozása az Azure-portál használatával. 

1. Indítsa el a **Microsoft Edge** vagy a **Google Chrome** böngészőt. A Data Factory felhasználói felületének használata jelenleg csak a Microsoft Edge-ben és a Google Chrome-ban támogatott.
2. Lépjen az [Azure Portalra](https://portal.azure.com). 
3. Kattintson az **Új** elemre, majd az **Adatok + analitika**, végül a **Data Factory** elemre. 
   
   ![New (Új)->DataFactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. Az **Új adat-előállító** lapon, a **Név** mezőben adja meg a következőt: **ADFTutorialDataFactory**. 
      
     ![Új adat-előállító lap](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   Az Azure data factory nevének **globálisan egyedinek** kell lennie. Ha a Név mezőnél az alábbi hiba jelenik meg, módosítsa az adat-előállító nevét (például a következőre: sajátneveADFTutorialDataFactory). A Data Factory-összetevők részleteit a [Data Factory elnevezési szabályait](naming-rules.md) ismertető cikkben találja.
  
     ![A név nem érhető el – hiba](./media/how-to-invoke-ssis-package-stored-procedure-activity/name-not-available-error.png)
3. Válassza ki azt az **Azure-előfizetést**, amelyben az adat-előállítót létre szeretné hozni. 
4. Az **erőforráscsoportban** hajtsa végre a következő lépések egyikét:
     
      - Kattintson a **Meglévő használata** elemre, majd a legördülő listából válasszon egy meglévő erőforráscsoportot. 
      - Kattintson az **Új létrehozása** elemre, és adja meg az erőforráscsoport nevét.   
         
    Az erőforráscsoportokkal kapcsolatos információkért tekintse meg a [Using resource groups to manage your Azure resources](../azure-resource-manager/resource-group-overview.md) (Erőforráscsoportok használata az Azure-erőforrások kezeléséhez) című cikket.  
4. Válassza a **V2 (előzetes verzió)** értéket a **verzió** esetén.
5. Válassza ki a Data Factory **helyét**. A legördülő listában csak a Data Factory által támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más helyeken is lehetnek.
6. Válassza a **Rögzítés az irányítópulton** lehetőséget.     
7. Kattintson a **Create** (Létrehozás) gombra.
8. Az irányítópulton megjelenő csempén a következő állapotleírás látható: **Adat-előállító üzembe helyezése**. 

    ![adat-előállító üzembe helyezése csempe](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. A létrehozás befejezése után a **Data Factory** lap a képen látható módon jelenik meg.
   
    ![Data factory kezdőlap](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Az Azure Data Factory felhasználói felület (UI) alkalmazás külön lapon történő elindításához kattintson az **Létrehozás és monitorozás** csempére. 

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Hozzon létre egy folyamatot tevékenység végrehajtása SSIS-csomag
Ebben a lépésben használatával a Data Factory felhasználói felületén hozzon létre egy folyamatot. A feldolgozási sor végrehajtása SSIS-csomag tevékenység hozzáadása, és konfigurálja a SSIS-csomag futtatásához. 

1. A get elindított oldalon kattintson **létrehozási folyamat**: 

    ![Első lépések lap](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. Az a **tevékenységek** eszközkészlet, bontsa ki a **általános**, és húzással a **SSIS-csomag hajtható végre** tevékenység a csővezeték-Tervező felületére. 

   ![A Tervező felületére húzza a SSIS-tevékenység](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

3. Az a **általános** lap végrehajtása SSIS-csomag tevékenység tulajdonságainak, adjon meg nevet és a tevékenység leírását. Nem kötelező időtúllépés állítsa, majd próbálja megismételni a értékeket.

    ![Az Általános lapon tulajdonságainak beállítása](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

4. Az a **beállítások** SSIS-csomag hajtható végre tevékenység, jelölje be az Azure-SSIS-integrációs futásidejű társított tulajdonságok lapján a `SSISDB` adatbázis, ahol a csomag üzembe lesz helyezve. Adjon meg a csomag elérési utat a `SSISDB` formátumú adatbázis `<folder name>/<project name>/<package name>.dtsx`. Lehetősége van a adja meg a 32 bites végrehajtásához, és egy előre definiált vagy egyéni naplózási szint, és adjon meg egy környezetben elérési utat a formátum `<folder name>/<environment name>`.

    ![A beállítások lapon tulajdonságainak beállítása](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

5. Az adatcsatorna konfiguráció érvényesítése céljából kattintson **ellenőrzése** az eszköztáron. A **Folyamatérvényesítési jelentés** bezárásához kattintson a **>>** jelre.

6. A feldolgozási sor közzétételére az adat-előállító kattintva **összes** gombra. 

### <a name="optionally-parameterize-the-activity"></a>Másik lehetőségként parametrizálja a tevékenység

Szükség esetén rendelje hozzá az értékeket, kifejezés vagy függvények, amelyek a Data Factory rendszerváltozók, a projekt vagy a csomag paraméterekhez JSON formátumban jelentheti a **speciális** fülre. Például hozzárendelheti adat-előállító adatcsatorna paraméterek SSIS-projektjéhez vagy a csomag paraméterek az alábbi képernyőfelvételen látható módon:

![Paraméterek hozzáadása a végrehajtás SSIS-csomag tevékenységhez](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-parameters.png)

### <a name="run-and-monitor-the-pipeline"></a>Futtathatja és figyelheti a folyamatot
Ebben a szakaszban indít folyamatot futtató, és megfigyeli azt. 

1. Futtassa egy folyamat indításához kattintson **eseményindító** az eszköztáron, és kattintson **aktiválhatja most**. 

    ![Aktiválás most](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-trigger.png)

2. A **Folyamatfuttatás** ablakban kattintson a **Befejezés** gombra. 

3. Váltson a bal oldali **Monitorozás** lapra. A Futtatás futószalag és egyéb információk (például futtassa Kezdés időpontja) mellett állapota látható. A nézet frissítéséhez kattintson a **Frissítés** parancsra.

    ![Folyamatfuttatások](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

4. Kattintson az **Actions** (Műveletek) oszlopban található **View Activity Runs** (Tevékenységfuttatások megtekintése) hivatkozásra. Futtassa az adatcsatorna esetében van (a végrehajtás SSIS-csomag tevékenység) csak egy tevékenység csak egy tevékenység megjelenik.

    ![Tevékenységfuttatások](./media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-runs.png)

5. Futtathatja a következő **lekérdezés** szemben az SSISDB adatbázis annak ellenőrzésére, hogy az Azure SQL Server a csomag végrehajtása. 

    ```sql
    select * from catalog.executions
    ```

    ![Ellenőrizze a csomag végrehajtások](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)

6. Az SSISDB végrehajtási azonosító beszerzése a feldolgozási sor tevékenység Futtatás kimenetét, és az Azonosítót használva átfogóbb feladatvégrehajtási naplók és a hibaüzenetek szolgáltatáshoz az ssms is.

    ![Beolvasni a végrehajtása.](media/how-to-invoke-ssis-package-ssis-activity/get-execution-id.png)

> [!NOTE]
> Is létrehozhat egy ütemezett eseményindító a tölcsér, hogy az adatcsatorna (óránként, naponta, stb.) ütemezés szerint futtatott. Egy vonatkozó példáért lásd: [tartalmazó data factory - létrehozása a Data Factory felhasználói felület](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="azure-powershell"></a>Azure PowerShell
Ebben a szakaszban Azure PowerShell létrehozása a Data Factory-folyamat fut egy SSIS-csomag SSIS tevékenység használja. 

Kövesse [az Azure PowerShell telepítését és konfigurálását](/powershell/azure/install-azurerm-ps) ismertető cikkben szereplő utasításokat a legújabb Azure PowerShell-modulok telepítéséhez. 

### <a name="create-a-data-factory"></a>Data factory létrehozása
Használja az ugyanazon adat-előállítóban, amely rendelkezik az Azure-SSIS infravörös, vagy hozzon létre egy külön adat-előállítóban. Az alábbi eljárás lépéseit egy adat-előállító létrehozása. A data factory SSIS tevékenység hoz létre egy folyamatot. A SSIS tevékenység fut a SSIS-csomag. 

1. Adjon meg egy olyan változót, amelyet később a PowerShell-parancsokban az erőforráscsoport neveként fog használni. Másolja az alábbi parancsszöveget a PowerShellbe, adja meg az [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md) nevét idézőjelek között, majd futtassa a parancsot. Például: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Ha az erőforráscsoport már létezik, előfordulhat, hogy nem kívánja felülírni. Rendeljen egy másik értéket a `$ResourceGroupName` változóhoz, majd futtassa újra a parancsot
2. Futtassa az alábbi parancsot az Azure-erőforráscsoport létrehozásához: 

    ```powershell
    $ResGrp = New-AzureRmResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    Ha az erőforráscsoport már létezik, előfordulhat, hogy nem kívánja felülírni. Rendeljen egy másik értéket a `$ResourceGroupName` változóhoz, majd futtassa újra a parancsot. 
3. Adjon meg egy változót az adat-előállító nevéhez. 

    > [!IMPORTANT]
    >  Frissítse az adat-előállító nevét, hogy globálisan egyedi legyen. 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. Az adat-előállító létrehozásához futtassa az alábbi **Set-AzureRmDataFactoryV2** parancsmagot a $ResGrp változó Location és ResourceGroupName tulajdonsága használatával: 
    
    ```powershell       
    $DataFactory = Set-AzureRmDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName `
                                            -Location $ResGrp.Location `
                                            -Name $dataFactoryName 
    ```

Vegye figyelembe a következő szempontokat:

* Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a következő hibaüzenetet kapja, módosítsa a nevet, majd próbálkozzon újra.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Data Factory-példányok létrehozásához a felhasználói fióknak, amellyel belép az Azure-ba, a **közreműködő** vagy **tulajdonos** szerepkörök tagjának, vagy az Azure-előfizetés **rendszergazdájának** kell lennie.
* Jelenleg adat-előállító 2-es hozhat létre adat-előállítók csak az USA keleti régiója, kelet-US2, Nyugat-Európa, és Délkelet-Ázsia régiók. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más régiókban is lehetnek.

### <a name="create-a-pipeline-with-an-ssis-activity"></a>Hozzon létre egy folyamatot egy SSIS-tevékenység 
Ebben a lépésben egy SSIS tevékenységet hoz létre egy folyamatot. A tevékenység futtatása a SSIS-csomag. 

1. Hozzon létre egy JSON fájlt **RunSSISPackagePipeline.json** a a **C:\ADF\RunSSISPackage** mappa tartalmát az alábbi példához hasonló:

    > [!IMPORTANT]
    > Cserélje le a kijelölendő objektumok nevét, leírását és az elérési utak tulajdonság és paraméterértékeket, jelszavakat és más változók értékeinek a fájl mentése előtt. 

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
                    "environmentPath":   "FolderName/EnvironmentName",
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
                        "\\PackageName.dtsx\\MaxConcurrentExecutables ": {
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

2.  Az Azure PowerShell, váltson a `C:\ADF\RunSSISPackage` mappát.

3. A folyamat létrehozása **RunSSISPackagePipeline**- ben futtassa a **Set-AzureRmDataFactoryV2Pipeline** parancsmag.

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

### <a name="create-a-pipeline-run"></a>Folyamat futásának létrehozása
Használja a **Invoke-AzureRmDataFactoryV2Pipeline** parancsmag futtatjuk a folyamatot. A parancsmag visszaadja a folyamat futásának azonosítóját a későbbi monitorozás céljából.

```powershell
$RunId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName `
                                             -ResourceGroupName $ResGrp.ResourceGroupName `
                                             -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline-run"></a>A folyamat futásának monitorozása

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

Ugyanígy figyelheti a folyamatot, az Azure portál használatával. Részletes útmutatásért lásd: [a folyamat figyelése](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

### <a name="create-a-trigger"></a>Egy eseményindító létrehozása
Az előző lépésben a csővezeték-igény szerinti futtatta. Egy ütemezés eseményindító fussanak ütemezés szerint (óránként, naponta, stb.) a feldolgozási sor is létrehozhat.

1. Hozzon létre egy JSON fájlt **MyTrigger.json** a **C:\ADF\RunSSISPackage** mappa a következő tartalmát: 

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
                }
            ]
        }
    }    
    ```
2. A **Azure PowerShell**, váltson a **C:\ADF\RunSSISPackage** mappa.
3. Futtassa a **Set-AzureRmDataFactoryV2Trigger** parancsmagot, amely az eseményindítót hoz létre. 

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                    -DataFactoryName $DataFactory.DataFactoryName `
                                    -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. Alapértelmezés szerint az eseményindító leállított állapotban van. Indítsa el az eseményindító futtatásával a **Start-AzureRmDataFactoryV2Trigger** parancsmag. 

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName `
                                      -DataFactoryName $DataFactory.DataFactoryName `
                                      -Name "MyTrigger" 
    ```
5. Győződjön meg arról, hogy az eseményindító elindításával a **Get-AzureRmDataFactoryV2Trigger** parancsmag. 

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName `
                                    -DataFactoryName $DataFactoryName `
                                    -Name "MyTrigger"     
    ```    
6. A következő parancsot a következő óra múlva. Például ha az aktuális idő du. 3:25 UTC, futtassa a parancsot du. 4 UTC. 
    
    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName `
                                       -DataFactoryName $DataFactoryName `
                                       -TriggerName "MyTrigger" `
                                       -TriggerRunStartedAfter "2017-12-06" `
                                       -TriggerRunStartedBefore "2017-12-09"
    ```

    A következő lekérdezés alapján futtathatók az SSISDB adatbázis annak ellenőrzésére, hogy az Azure SQL Server a csomag végrehajtása. 

    ```sql
    select * from catalog.executions
    ```


## <a name="next-steps"></a>További lépések
Tekintse meg a következő blogbejegyzésben található:
-   [Korszerűsítésére és kiterjesztése a ETL/ELT munkafolyamatok SSIS-tevékenységek számára az ADF-folyamatok](https://blogs.msdn.microsoft.com/ssis/2018/05/23/modernize-and-extend-your-etlelt-workflows-with-ssis-activities-in-adf-pipelines/)
