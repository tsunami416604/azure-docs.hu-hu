---
title: Futtassa az SSIS-csomag végrehajtása SSIS csomag tevékenységgel – Azure |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan futtathat egy SQL Server Integration Services (SSIS) csomag az Azure Data Factory-folyamatot az SSIS-csomag végrehajtása tevékenység használatával.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/16/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 3a43c0cd13300918979ae03c7f6c703796b65dc9
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39114225"
---
# <a name="run-an-ssis-package-with-the-execute-ssis-package-activity-in-azure-data-factory"></a>A végrehajtás SSIS csomag tevékenységgel rendelkező Azure Data Factory SSIS-csomag futtatása
Ez a cikk ismerteti az Azure Data Factory-folyamatot egy SSIS-csomag futtatása SSIS-csomag végrehajtása tevékenységek segítségével. 

## <a name="prerequisites"></a>Előfeltételek

**Azure SQL Database** Ebben a cikkben található útmutatások követéséhez használja az Azure SQL database, amelyen az SSIS-katalógus. Egy Azure SQL felügyelt példány (előzetes verzió) is használhatja.

## <a name="create-an-azure-ssis-integration-runtime"></a>Azure SSIS integrációs modul létrehozása
Hozzon létre egy Azure-SSIS integrációs modult, ha még nincs fiókja, a részletes utasításokat a következő a [oktatóanyag: üzembe SSIS-csomagok](tutorial-create-azure-ssis-runtime-portal.md).

## <a name="run-a-package-in-the-azure-portal"></a>A csomag futtatása az Azure Portalon
Ebben a szakaszban a Data Factory felhasználói felülete egy SSIS-csomag végrehajtása tevékenységgel, amely SSIS-csomag létrehozása a Data Factory-folyamatok használhatja.

### <a name="create-a-data-factory"></a>Data factory létrehozása
Első lépés az adat-előállító létrehozása az Azure portal használatával. 

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
4. A **Verzió** résznél válassza a **V2** értéket.
5. Válassza ki a Data Factory **helyét**. A legördülő listában csak a Data Factory által támogatott helyek jelennek meg. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más helyeken is lehetnek.
6. Válassza a **Rögzítés az irányítópulton** lehetőséget.     
7. Kattintson a **Create** (Létrehozás) gombra.
8. Az irányítópulton megjelenő csempén a következő állapotleírás látható: **Adat-előállító üzembe helyezése**. 

    ![adat-előállító üzembe helyezése csempe](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. A létrehozás befejezése után a **Data Factory** lap a képen látható módon jelenik meg.
   
    ![Data factory kezdőlap](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Az Azure Data Factory felhasználói felület (UI) alkalmazás külön lapon történő elindításához kattintson az **Létrehozás és monitorozás** csempére. 

### <a name="create-a-pipeline-with-an-execute-ssis-package-activity"></a>Az SSIS-csomag végrehajtása tevékenységgel rendelkező folyamat létrehozása
Ebben a lépésben a Data Factory felhasználói felülete létrehoz egy folyamatot használja. SSIS-csomag végrehajtása tevékenység hozzáadása a folyamathoz, és konfigurálja úgy, hogy az SSIS-csomag futtatása. 

1. Az első lépések oldalán kattintson **folyamat létrehozása**: 

    ![Első lépések lap](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. Az a **tevékenységek** eszközkészletben bontsa ki a **általános**, és áthúzása a **SSIS-csomag végrehajtása** tevékenységet a folyamat tervezőfelületére. 

   ![A Tervező felületére az SSIS-csomag végrehajtása tevékenység húzása](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-designer.png) 

3. Az a **általános** fülre az SSIS-csomag végrehajtása tevékenység tulajdonságai, adjon meg egy nevet és leírást a tevékenység. Állítsa be a választható időkorlátja, és ismételje meg a értékeket.

    ![Az Általános lapon tulajdonságainak megadása](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-general.png)

4. Az a **beállítások** az SSIS-csomag végrehajtása tevékenységre, válassza az Azure-SSIS integrációs modul társított tulajdonságok lapján a `SSISDB` adatbázis, ahol a csomag telepítése. Adja meg a csomag elérési útját a `SSISDB` adatbázis a következő formátumban `<folder name>/<project name>/<package name>.dtsx`. Igény szerint adja meg a 32 bites végrehajtását és a egy előre definiált vagy egyéni naplózási szint, és adja meg az elérési útnak a következő formátumban környezet `<folder name>/<environment name>`.

    ![A beállítások lapon tulajdonságainak megadása](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-settings.png)

5. A folyamat konfiguráció érvényesítéséhez kattintson **ellenőrzése** az eszköztáron. A **Folyamatérvényesítési jelentés** bezárásához kattintson a **>>** jelre.

6. A folyamat közzétételére az adat-előállító kattintva **összes közzététele** gombra. 

### <a name="optionally-parameterize-the-activity"></a>Másik lehetőségként paraméterezni a tevékenység

Szükség esetén értékek, kifejezések és függvények, amelyek a Data Factory rendszerváltozók hivatkozhatnak, hozzárendelése a projekthez, vagy a csomag paraméterek használatával JSON formátumban a **nézet forráskód** a végrehajtás SSIS alján gomb Csomag tevékenység mezőben vagy a **kód** gombra a folyamat terület jobb felső sarokban. Hozzárendelhet például az SSIS-projektjéhez Data Factory-folyamat paramétereihez vagy a csomag paraméterek, ahogyan az alábbi képernyőfelvételnek megfelelően:

![SSIS-csomag végrehajtása tevékenység JSON-parancsfájl szerkesztése](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-parameters.png)

![Paraméterek hozzáadása az SSIS-csomag végrehajtása tevékenység](media/how-to-invoke-ssis-package-ssis-activity/ssis-activity-parameters2.png)

### <a name="run-the-pipeline"></a>A folyamat futtatása
Ebben a szakaszban a folyamat futásának aktiválásához, és megfigyeli azt. 

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
Ebben a szakaszban az Azure PowerShell használatával hozzon létre egy Data Factory-folyamatot egy SSIS-csomag végrehajtása tevékenység, amely SSIS-csomag. 

Kövesse [az Azure PowerShell telepítését és konfigurálását](/powershell/azure/install-azurerm-ps) ismertető cikkben szereplő utasításokat a legújabb Azure PowerShell-modulok telepítéséhez. 

### <a name="create-a-data-factory"></a>Data factory létrehozása
Az azonos adat-előállítót, amely rendelkezik az Azure-SSIS integrációs modul használata, vagy egy különálló adat-előállító létrehozásához. Az alábbi eljárás lépéseit egy adat-előállító létrehozásához. Egy SSIS-csomag végrehajtása tevékenysége az adat-előállító folyamatot hoz létre. Az SSIS-csomag végrehajtása tevékenységfuttatások az SSIS-csomag. 

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
* Azon Azure-régiók megtekintéséhez, amelyekben jelenleg elérhető a Data Factory, a következő lapon válassza ki az Önt érdeklő régiókat, majd bontsa ki az **Elemzés** részt, és keresse meg a **Data Factory**: [Elérhető termékek régiók szerint](https://azure.microsoft.com/global-infrastructure/services/) szakaszt. Az adat-előállítók által használt adattárak (Azure Storage, Azure SQL Database stb.) és számítási erőforrások (HDInsight stb.) más régiókban is lehetnek.

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

2.  Az Azure PowerShellben váltson a `C:\ADF\RunSSISPackage` mappát.

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
                }
            ]
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
