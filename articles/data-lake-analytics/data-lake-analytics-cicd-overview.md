---
title: CI/CD-folyamat beállítása az Azure Data Lake Analytics szolgáltatáshoz
description: Ismerje meg, hogyan állíthatja be a folyamatos integrációt és a folyamatos üzembe helyezést az Azure Data Lake Analytics számára.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 09/14/2018
ms.openlocfilehash: b035be727df2dfecb613da79681affd740c69bec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60333860"
---
# <a name="how-to-set-up-a-cicd-pipeline-for-azure-data-lake-analytics"></a>CI/CD-folyamat beállítása az Azure Data Lake Analytics szolgáltatáshoz  

Ebben a cikkben megtudhatja, hogyan állíthat be egy folyamatos integrációs és üzembe helyezési (CI/CD) folyamat u-SQL feladatok és U-SQL adatbázisok.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-cicd-for-u-sql-jobs"></a>Ci/CD használata U-SQL feladatokhoz

Az Azure Data Lake Tools for Visual Studio biztosítja az U-SQL projekttípust, amely segít az U-SQL-parancsfájlok rendszerezésében. Az U-SQL projekt használata az U-SQL-kód kezeléséhez megkönnyíti a további CI/CD-forgatókönyveket.

## <a name="build-a-u-sql-project"></a>U-SQL projekt létrehozása

Az U-SQL-projekt a Microsoft Build Engine (MSBuild) segítségével a megfelelő paraméterek átadásával is létrehozható. A cikkben ismertetett lépéseket követve állítsa be az U-SQL-projektek létrehozási folyamatát.

### <a name="project-migration"></a>A projekt áttelepítése

Mielőtt létrehozna egy létrehozási feladatot egy U-SQL projekthez, győződjön meg arról, hogy az U-SQL projekt legújabb verziójával rendelkezik. Nyissa meg az U-SQL projektfájlt a szerkesztőben, és ellenőrizze, hogy vannak-e az alábbi importálási elemek:

```   
<!-- check for SDK Build target in current path then in USQLSDKPath-->
<Import Project="UsqlSDKBuild.targets" Condition="Exists('UsqlSDKBuild.targets')" />
<Import Project="$(USQLSDKPath)\UsqlSDKBuild.targets" Condition="!Exists('UsqlSDKBuild.targets') And '$(USQLSDKPath)' != '' And Exists('$(USQLSDKPath)\UsqlSDKBuild.targets')" />
``` 

Ha nem, két lehetősége van a projekt áttelepítésére:

- 1. lehetőség: Módosítsa a régi importálási elemet az előzőre.
- 2. lehetőség: Nyissa meg a régi projektet az Azure Data Lake Tools for Visual Studio alkalmazásban. A 2.3.3000.0-nál újabb verziót használjon. A régi projektsablon automatikusan frissül a legújabb verzióra. A 2.3.3000.0-nál újabb verziókkal létrehozott új projektek az új sablont használják.

### <a name="get-nuget"></a>A NuGet beszereznie

Az MSBuild nem nyújt beépített támogatást az U-SQL-projektekhez. A támogatás lekérni, hozzá kell adnia egy hivatkozást a megoldáshoz a [Microsoft.Azure.DataLake.USQL.SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) NuGet csomaghoz, amely hozzáadja a szükséges nyelvi szolgáltatást.

A NuGet csomaghivatkozás hozzáadásához kattintson a jobb gombbal a megoldásra a Visual Studio Solution Explorer alkalmazásban, és válassza **a NuGet-csomagok kezelése parancsot.** Vagy hozzáadhat egy fájlnak a megoldás mappájában, `packages.config` és a következő tartalmat helyezheti el benne:

```xml 
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180620" targetFramework="net452" />
</packages>
``` 

### <a name="manage-u-sql-database-references"></a>U-SQL adatbázis-hivatkozások kezelése

Az U-SQL-projektek U-SQL-parancsfájljai rendelkezhetnek az U-SQL adatbázis-objektumok lekérdezési utasításával. Ebben az esetben az U-SQL-projekt létrehozása előtt hivatkoznia kell a megfelelő U-SQL adatbázis-projektre, amely tartalmazza az objektumok definícióját. Ilyen például egy U-SQL tábla lekérdezése vagy egy összeállításra való hivatkozás. 

További információ az [U-SQL adatbázis-projektről.](data-lake-analytics-data-lake-tools-develop-usql-database.md)

>[!NOTE]
>A DROP utasítás balesettörlési problémát okozhat. A DROP utasítás engedélyezéséhez explicit módon meg kell adnia az MSBuild argumentumokat. **Az AllowDropStatement** engedélyezi a nem adatokkal kapcsolatos DROP-műveletet, például a drop assembly és a drop table valued függvényt. **Az AllowDataDropStatement** engedélyezi az adatokkal kapcsolatos DROP-műveletet, például a drop table és a drop sémát. Az AllowDataDropStatement használata előtt engedélyeznie kell az AllowDropStatement utasítást.
>

### <a name="build-a-u-sql-project-with-the-msbuild-command-line"></a>U-SQL projekt létrehozása az MSBuild parancssorból

Először telepítse át a projektet, és kapja meg a NuGet csomagot. Ezután hívja meg a szabványos MSBuild parancssort a következő további argumentumokkal az U-SQL projekt létrehozásához: 

``` 
msbuild USQLBuild.usqlproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime;USQLTargetType=SyntaxCheck;DataRoot=datarootfolder;/p:EnableDeployment=true
``` 

Az argumentumok meghatározása és értékei a következők:

* **USQLSDKPath=\<U-SQL Nuget csomag>\build\runtime**. Ez a paraméter az U-SQL nyelvi szolgáltatás NuGet csomagjának telepítési útvonalára hivatkozik.
* **USQLTargetType=Merge vagy SyntaxCheck**:
    * **Egyesítés**. Az Egyesítési mód lefordítja a mögötti kódfájlokat. Ilyenek például **a .cs**, **.py**és **.r** fájlok. Az eredményül kapott felhasználó által definiált kódkönyvtárat az U-SQL parancsfájlba inlinek. Ilyenek például a dll bináris, Python vagy R-kód.
    * **Szintaxisellenőrzése**. A SyntaxCheck mód először egyesíti a mögötti kódfájlokat az U-SQL parancsfájlba. Ezután lefordítja az U-SQL parancsfájlt a kód érvényesítéséhez.
* **DataRoot=\<DataRoot elérési út>. ** A DataRoot csak syntaxCheck módban szükséges. Amikor syntaxCheck móddal építi fel a parancsfájlt, az MSBuild ellenőrzi a parancsfájlban lévő adatbázis-objektumokra mutató hivatkozásokat. A létrehozás előtt állítson be egy megfelelő helyi környezetet, amely a buildgép DataRoot mappájában található U-SQL adatbázis hivatkozott objektumait tartalmazza. Ezeket az adatbázis-függőségeket [egy U-SQL adatbázis-projektre hivatkozva](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project)is kezelheti. Az MSBuild csak az adatbázis-objektumhivatkozásokat ellenőrzi, a fájlokat nem.
* **EnableDeployment=true** vagy **false**. EnableDeployment jelzi, ha a hivatkozott U-SQL-adatbázisok telepítése a létrehozási folyamat során engedélyezett. Ha u-SQL adatbázis-projektre hivatkozik, és felhasználja az U-SQL parancsfájladatbázis-objektumait, állítsa ezt a paramétert **true**értékre.

### <a name="continuous-integration-through-azure-pipelines"></a>Folyamatos integráció az Azure-folyamatokon keresztül

A parancssor mellett a Visual Studio Build vagy egy MSBuild feladat segítségével is létrehozhat U-SQL-projekteket az Azure Pipelines-ban. Build-folyamat beállításához győződjön meg arról, hogy két feladatot ad hozzá a buildfolyamathoz: egy NuGet-visszaállítási feladatot és egy MSBuild feladatot.

![MSTevékenység létrehozása U-SQL projekthez](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 

1.  NuGet-visszaállítási feladat hozzáadásával leszeretné kelni a `Azure.DataLake.USQL.SDK`megoldásra hivatkozott NuGet csomagot, amely tartalmazza a t, hogy az MSBuild megtalálja az U-SQL nyelvi célokat. Állítsa **az** > Advanced `$(Build.SourcesDirectory)/packages` Destination**könyvtárat,** ha az MSBuild argumentumok mintáját közvetlenül a 2.

    ![NuGet visszaállítási feladat egy U-SQL projekthez](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2.  MsBuild argumentumok beállítása a Visual Studio buildeszközeiben vagy egy MSBuild feladatban az alábbi példában látható módon. Vagy ezekhez az argumentumokhoz változókat definiálhat az Azure-folyamatok buildfolyamatában.

    ![CI/CD MSBuild változók definiálása U-SQL projekthez](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables.png) 

    ```
    /p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime /p:USQLTargetType=SyntaxCheck /p:DataRoot=$(Build.SourcesDirectory) /p:EnableDeployment=true
    ```

### <a name="u-sql-project-build-output"></a>U-SQL projektbuild kimenet

A build futtatása után az U-SQL projekt összes parancsfájlja létrejöjjön, és egy zip nevű fájlba adja ki a kimenetet. `USQLProjectName.usqlpack` A projekt mappastruktúrája a tömörített build kimenetben marad.

> [!NOTE]
>
> Az egyes U-SQL-parancsfájlok háttérkódfájljai szövegközi utasításként egyesülnek a parancsfájl buildkimenetéhez.
>

## <a name="test-u-sql-scripts"></a>U-SQL-szkriptek tesztelése

Az Azure Data Lake tesztprojekteket biztosít u-SQL-parancsfájlok és C# UDO/UDAG/UDF:
* További információ az [U-SQL-parancsfájlok és a kiterjesztett C# kód teszteseteinek hozzáadásáról.](data-lake-analytics-cicd-test.md#test-u-sql-scripts)
* Ismerje meg, hogyan [futtathat teszteseteket az Azure Pipelines-ban.](data-lake-analytics-cicd-test.md#run-test-cases-in-azure-devops)

## <a name="deploy-a-u-sql-job"></a>U-SQL-feladat telepítése

Miután ellenőrizte a kódot a létrehozási és tesztelési folyamat során, elküldheti az U-SQL-feladatokat közvetlenül az Azure Pipelines-ból egy Azure PowerShell-feladaton keresztül. A parancsfájlt az Azure Data Lake Store vagy az Azure Blob storage-ba is telepítheti, és [az Azure Data Factory-n keresztül futtathatja az ütemezett feladatokat.](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics)

### <a name="submit-u-sql-jobs-through-azure-pipelines"></a>U-SQL-feladatok küldése az Azure-folyamatokon keresztül

Az U-SQL projekt buildkimenete egy **USQLProjectName.usqlpack**nevű zip fájl. A zip fájl tartalmazza az összes U-SQL szkriptek a projektben. Használhatja az [Azure PowerShell-feladatot](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-powershell?view=vsts) a folyamatokban a következő minta PowerShell-parancsfájllal, hogy u-SQL-feladatokat küldjön közvetlenül az Azure Pipelines-ból.

```powershell
<#
    This script can be used to submit U-SQL Jobs with given U-SQL project build output(.usqlpack file).
    This will unzip the U-SQL project build output, and submit all scripts one-by-one.

    Note: the code behind file for each U-SQL script will be merged into the built U-SQL script in build output.
          
    Example :
        USQLJobSubmission.ps1 -ADLAAccountName "myadlaaccount" -ArtifactsRoot "C:\USQLProject\bin\debug\" -DegreeOfParallelism 2
#>

param(
    [Parameter(Mandatory=$true)][string]$ADLAAccountName, # ADLA account name to submit U-SQL jobs
    [Parameter(Mandatory=$true)][string]$ArtifactsRoot, # Root folder of U-SQL project build output
    [Parameter(Mandatory=$false)][string]$DegreeOfParallelism = 1
)

function Unzip($USQLPackfile, $UnzipOutput)
{
    $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
    Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
    Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

## Get U-SQL scripts in U-SQL project build output(.usqlpack file)
Function GetUsqlFiles()
{

    $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

    $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

    foreach ($USQLPackfile in $USQLPackfiles)
    {
        Unzip $USQLPackfile $UnzipOutput
    }

    $USQLFiles = Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue

    return $USQLFiles
}

## Submit U-SQL scripts to ADLA account one-by-one
Function SubmitAnalyticsJob()
{
    $usqlFiles = GetUsqlFiles

    Write-Output "$($usqlFiles.Count) jobs to be submitted..."

    # Submit each usql script and wait for completion before moving ahead.
    foreach ($usqlFile in $usqlFiles)
    {
        $scriptName = "[Release].[$([System.IO.Path]::GetFileNameWithoutExtension($usqlFile.fullname))]"

        Write-Output "Submitting job for '{$usqlFile}'"

        $jobToSubmit = Submit-AzDataLakeAnalyticsJob -Account $ADLAAccountName -Name $scriptName -ScriptPath $usqlFile -DegreeOfParallelism $DegreeOfParallelism
        
        LogJobInformation $jobToSubmit
        
        Write-Output "Waiting for job to complete. Job ID:'{$($jobToSubmit.JobId)}', Name: '$($jobToSubmit.Name)' "
        $jobResult = Wait-AzDataLakeAnalyticsJob -Account $ADLAAccountName -JobId $jobToSubmit.JobId  
        LogJobInformation $jobResult
    }
}

Function LogJobInformation($jobInfo)
{
    Write-Output "************************************************************************"
    Write-Output ([string]::Format("Job Id: {0}", $(DefaultIfNull $jobInfo.JobId)))
    Write-Output ([string]::Format("Job Name: {0}", $(DefaultIfNull $jobInfo.Name)))
    Write-Output ([string]::Format("Job State: {0}", $(DefaultIfNull $jobInfo.State)))
    Write-Output ([string]::Format("Job Started at: {0}", $(DefaultIfNull  $jobInfo.StartTime)))
    Write-Output ([string]::Format("Job Ended at: {0}", $(DefaultIfNull  $jobInfo.EndTime)))
    Write-Output ([string]::Format("Job Result: {0}", $(DefaultIfNull $jobInfo.Result)))
    Write-Output "************************************************************************"
}

Function DefaultIfNull($item)
{
    if ($item -ne $null)
    {
        return $item
    }
    return ""
}

Function Main()
{
    Write-Output ([string]::Format("ADLA account: {0}", $ADLAAccountName))
    Write-Output ([string]::Format("Root folde for usqlpack: {0}", $ArtifactsRoot))
    Write-Output ([string]::Format("AU count: {0}", $DegreeOfParallelism))

    Write-Output "Starting USQL script deployment..."
    
    SubmitAnalyticsJob

    Write-Output "Finished deployment..."
}

Main
```

### <a name="deploy-u-sql-jobs-through-azure-data-factory"></a>U-SQL-feladatok telepítése az Azure Data Factory-n keresztül

Az U-SQL-feladatokat közvetlenül az Azure Pipelines-ból küldheti el. Vagy feltöltheti a beépített parancsfájlokat az Azure Data Lake Store vagy az Azure Blob storage szolgáltatásba, és [futtathatja az ütemezett feladatokat az Azure Data Factory-n keresztül.](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics)

Használja az [Azure PowerShell-feladatot](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-powershell?view=vsts) az Azure-folyamatokban a következő PowerShell-parancsfájlokkal az U-SQL-parancsfájlok feltöltéséhez egy Azure Data Lake Áruházbeli fiókba:

```powershell
<#
    This script can be used to upload U-SQL files to ADLS with given U-SQL project build output(.usqlpack file).
    This will unzip the U-SQL project build output, and upload all scripts to ADLS one-by-one.
          
    Example :
        FileUpload.ps1 -ADLSName "myadlsaccount" -ArtifactsRoot "C:\USQLProject\bin\debug\"
#>

param(
    [Parameter(Mandatory=$true)][string]$ADLSName, # ADLS account name to upload U-SQL scripts
    [Parameter(Mandatory=$true)][string]$ArtifactsRoot, # Root folder of U-SQL project build output
    [Parameter(Mandatory=$false)][string]$DestinationFolder = "USQLScriptSource" # Destination folder in ADLS
)

Function UploadResources()
{
    Write-Host "************************************************************************"
    Write-Host "Uploading files to $ADLSName"
    Write-Host "***********************************************************************"

    $usqlScripts = GetUsqlFiles

    $files = @(get-childitem $usqlScripts -recurse)
    foreach($file in $files)
    {
        Write-Host "Uploading file: $($file.Name)"
        Import-AzDataLakeStoreItem -AccountName $ADLSName -Path $file.FullName -Destination "/$(Join-Path $DestinationFolder $file)" -Force
    }
}

function Unzip($USQLPackfile, $UnzipOutput)
{
    $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
    Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
    Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

Function GetUsqlFiles()
{

    $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

    $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

    foreach ($USQLPackfile in $USQLPackfiles)
    {
        Unzip $USQLPackfile $UnzipOutput
    }

    return Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue
}

UploadResources
```

## <a name="cicd-for-a-u-sql-database"></a>CI/CD U-SQL adatbázishoz

Az Azure Data Lake Tools for Visual Studio U-SQL adatbázis-projektsablonokat biztosít, amelyek segítségével fejlesztheti, kezelheti és telepítheti az U-SQL-adatbázisokat. További információ az [U-SQL adatbázis-projektekről.](data-lake-analytics-data-lake-tools-develop-usql-database.md)

## <a name="build-u-sql-database-project"></a>U-SQL adatbázis-projekt létrehozása

### <a name="get-the-nuget-package"></a>Szerezd meg a NuGet csomagot

Az MSBuild nem nyújt beépített támogatást az U-SQL adatbázis-projektekhez. Ennek a képességnek a megsegítéséhez hozzá kell adnia egy hivatkozást a megoldáshoz a [Microsoft.Azure.DataLake.USQL.SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) NuGet csomaghoz, amely hozzáadja a szükséges nyelvi szolgáltatást.

A NuGet csomaghivatkozás hozzáadásához kattintson a jobb gombbal a megoldásra a Visual Studio Solution Explorer alkalmazásban. Válassza **a NuGet-csomagok kezelése**lehetőséget. Ezután keresse meg és telepítse a NuGet csomagot. Vagy hozzáadhat egy **packages.config** nevű fájlt a megoldás mappába, és a következő tartalmat helyezheti el benne:

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180615" targetFramework="net452" />
</packages>
```

### <a name="build-u-sql-a-database-project-with-the-msbuild-command-line"></a>Az U-SQL adatbázis-projektjének létrehozása az MSBuild parancssorból

Az U-SQL adatbázis-projekt létrehozásához hívja meg a szabványos MSBuild parancssort, és adja át az U-SQL SDK NuGet csomaghivatkozást további argumentumként. Lásd a következő példát: 

```
msbuild DatabaseProject.usqldbproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime
```

Az `USQLSDKPath=<U-SQL Nuget package>\build\runtime` argumentum az U-SQL nyelvi szolgáltatás NuGet csomagjának telepítési útvonalára hivatkozik.

### <a name="continuous-integration-with-azure-pipelines"></a>Folyamatos integráció az Azure Pipelinesszal

A parancssor mellett a Visual Studio Build vagy egy MSBuild feladat segítségével u-SQL adatbázis-projekteket hozhat létre az Azure Pipelines-ban. Buildfeladat beállításához győződjön meg arról, hogy két feladatot ad hozzá a buildfolyamathoz: egy NuGet-visszaállítási feladatot és egy MSBuild feladatot.

   ![CI/CD MSFeladat létrehozása U-SQL projekthez](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 


1. NuGet-visszaállítási feladat hozzáadásával leszeretné kelni a `Azure.DataLake.USQL.SDK`megoldásra hivatkozott NuGet csomagot, amely tartalmazza a t, hogy az MSBuild megtalálja az U-SQL nyelvi célokat. Állítsa **az** > Advanced `$(Build.SourcesDirectory)/packages` Destination**könyvtárat,** ha az MSBuild argumentumok mintáját közvetlenül a 2.

   ![CI/CD NuGet feladat egy U-SQL projekthez](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2. MsBuild argumentumok beállítása a Visual Studio buildeszközeiben vagy egy MSBuild feladatban az alábbi példában látható módon. Vagy ezekhez az argumentumokhoz változókat definiálhat az Azure-folyamatok buildfolyamatában.

   ![CI/CD MSDefiniálÁs Változók létrehozása U-SQL adatbázis-projekthez](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables-database-project.png) 

   ```
   /p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime
   ```
 
### <a name="u-sql-database-project-build-output"></a>U-SQL adatbázis-projekt build kimenete

Az U-SQL adatbázis-projekt buildkimenete egy U-SQL adatbázis-telepítési csomag, amelynek neve az utótaggal van elnevezve. `.usqldbpack` A `.usqldbpack` csomag egy zip fájl, amely tartalmazza az összes DDL utasítást egyetlen U-SQL parancsfájlban egy DDL mappában. Ez magában foglalja az összes **.dlls** és további fájlokat összeszerelés egy ideiglenes mappában.

## <a name="test-table-valued-functions-and-stored-procedures"></a>Teszttábla-értékű függvények és tárolt eljárások

A táblaértékű függvényekhez és a tárolt eljárásokhoz való tesztesetek hozzáadása jelenleg nem támogatott. Kerülő megoldásként létrehozhat egy U-SQL-projektet, amely U-SQL parancsfájlokkal rendelkezik, amelyek meghívják ezeket a függvényeket, és teszteseteket írnak nekik. A következő lépésekkel állítsa be a táblaértékű függvények és az U-SQL adatbázis-projektben definiált tárolt eljárások teszteseteit:

1.  Hozzon létre egy U-SQL projektet tesztelési célokra, és írjon U-SQL parancsfájlokat, amelyek meghívják a tábla értékű függvényeket és a tárolt eljárásokat.
2.  Adatbázis-hivatkozás hozzáadása az U-SQL projekthez. A táblaértékű függvény és a tárolt eljárásdefiníció lekért szeretné, hivatkoznia kell a DDL utasítást tartalmazó adatbázisprojektre. További információ az [adatbázis-referenciákról.](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project)
3.  Adja meg a teszteseteket az U-SQL parancsfájlokhoz, amelyek táblaértékű függvényeket és tárolt eljárásokat hívnak meg. További információ az [U-SQL-parancsfájlok teszteseteinek hozzáadásáról.](data-lake-analytics-cicd-test.md#test-u-sql-scripts)

## <a name="deploy-u-sql-database-through-azure-pipelines"></a>U-SQL-adatbázis telepítése az Azure-folyamatokon keresztül

`PackageDeploymentTool.exe`biztosítja a programozási és parancssori felületeket, amelyek segítenek az U-SQL adatbázis-telepítési csomagok telepítésében, **.usqldbpack**. Az SDK az [U-SQL SDK NuGet csomag](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/)része, amely a **build/runtime/PackageDeploymentTool.exe fájl/futásidejű/packagedeploymenttool.exe helyen**található. A `PackageDeploymentTool.exe`használatával a használatával u-SQL adatbázisok at telepítheti az Azure Data Lake Analytics és a helyi fiókok.

> [!NOTE]
>
> A PowerShell parancssori támogatása és az Azure Pipelines kiadási feladattámogatása az U-SQL-adatbázis üzembe helyezéséhez jelenleg függőben van.
>

Az alábbi lépésekkel hozzon létre egy adatbázis-telepítési feladatot az Azure-folyamatokban:

1. Adjon hozzá egy PowerShell-parancsfájl-feladatot egy build- vagy kiadási folyamathoz, és hajtsa végre a következő PowerShell-parancsfájlt. Ez a feladat segít az Azure `PackageDeploymentTool.exe` SDK-függőségek be- és `PackageDeploymentTool.exe`. Az **-AzureSDK** és a **-DBDeploymentTool** paraméterek et beállíthatja a függőségek és a központi telepítési eszköz adott mappákba való betöltéséhez. Adja át az **-AzureSDK** elérési utat az `PackageDeploymentTool.exe` **-AzureSDKPath** paraméterként a 2. 

    ```powershell
    <#
        This script is used for getting dependencies and SDKs for U-SQL database deployment.
        PowerShell command line support for deploying U-SQL database package(.usqldbpack file) will come soon.
        
        Example :
            GetUSQLDBDeploymentSDK.ps1 -AzureSDK "AzureSDKFolderPath" -DBDeploymentTool "DBDeploymentToolFolderPath"
    #>

    param (
        [string]$AzureSDK = "AzureSDK", # Folder to cache Azure SDK dependencies
        [string]$DBDeploymentTool = "DBDeploymentTool", # Folder to cache U-SQL database deployment tool
        [string]$workingfolder = "" # Folder to execute these command lines
    )

    if ([string]::IsNullOrEmpty($workingfolder))
    {
        $scriptpath = $MyInvocation.MyCommand.Path
        $workingfolder = Split-Path $scriptpath
    }
    cd $workingfolder

    echo "workingfolder=$workingfolder, outputfolder=$outputfolder"
    echo "Downloading required packages..."

    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Analytics/3.5.1-preview -outf Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Store/2.4.1-preview -outf Microsoft.Azure.Management.DataLake.Store.2.4.1-preview.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.3 -outf Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime/2.3.11 -outf Microsoft.Rest.ClientRuntime.2.3.11.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure/3.3.7 -outf Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure.Authentication/2.3.3 -outf Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip
    iwr https://www.nuget.org/api/v2/package/Newtonsoft.Json/6.0.8 -outf Newtonsoft.Json.6.0.8.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.DataLake.USQL.SDK/ -outf USQLSDK.zip

    echo "Extracting packages..."

    Expand-Archive Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview -Force
    Expand-Archive Microsoft.Azure.Management.DataLake.Store.2.4.1-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Store.2.4.1-preview -Force
    Expand-Archive Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip -DestinationPath Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.2.3.11.zip -DestinationPath Microsoft.Rest.ClientRuntime.2.3.11 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.3.3.7 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3 -Force
    Expand-Archive Newtonsoft.Json.6.0.8.zip -DestinationPath Newtonsoft.Json.6.0.8 -Force
    Expand-Archive USQLSDK.zip -DestinationPath USQLSDK -Force

    echo "Copy required DLLs to output folder..."

    mkdir $AzureSDK -Force
    mkdir $DBDeploymentTool -Force
    copy Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview\lib\net452\*.dll $AzureSDK
    copy Microsoft.Azure.Management.DataLake.Store.2.4.1-preview\lib\net452\*.dll $AzureSDK
    copy Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.2.3.11\lib\net452\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3\lib\net452\*.dll $AzureSDK
    copy Newtonsoft.Json.6.0.8\lib\net45\*.dll $AzureSDK
    copy USQLSDK\build\runtime\*.* $DBDeploymentTool
    ```

2. Adjon hozzá **egy parancssori feladatot** egy build- vagy `PackageDeploymentTool.exe`kiadási folyamathoz, és töltse ki a parancsfájlt a hívásával. `PackageDeploymentTool.exe`a megadott **$DBDeploymentTool** mappa alatt található. A minta szkript a következő: 

    * U-SQL-adatbázis helyi telepítése:

        ```
        PackageDeploymentTool.exe deploylocal -Package <package path> -Database <database name> -DataRoot <data root path>
        ```

    * Az Interaktív hitelesítési mód használatával telepíthet egy U-SQL-adatbázist egy Azure Data Lake Analytics-fiókba:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -AzureSDKPath <azure sdk path> -Interactive
        ```

    * Az U-SQL-adatbázis Azure Data Lake Analytics-fiókba való üzembe helyezéséhez használjon **titkos** hitelesítést:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete>
        ```

    * A **certFile-hitelesítés** használatával telepíthet egy U-SQL-adatbázist egy Azure Data Lake Analytics-fiókba:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete> -CertFile <certFile>
        ```

### <a name="packagedeploymenttoolexe-parameter-descriptions"></a>A PackageDeploymentTool.exe paraméter leírása

#### <a name="common-parameters"></a>Gyakori paraméterek

| Paraméter | Leírás | Alapértelmezett érték | Kötelező |
|---------|-----------|-------------|--------|
|Csomag|A telepítendő U-SQL-adatbázis központi telepítési csomagjának elérési útja.|null|igaz|
|Adatbázis|A telepítendő vagy létrehozandó adatbázisnév.|master|hamis|
|Logfile|A naplózatra szolgáló fájl elérési útja. Alapértelmezés szerint a standard out (konzol).|null|hamis|
|Naplózási szint|Naplószint: Részletes, Normál, Figyelmeztetés vagy Hiba.|LogLevel.Normál|hamis|

#### <a name="parameter-for-local-deployment"></a>Helyi telepítés paramétere

|Paraméter|Leírás|Alapértelmezett érték|Kötelező|
|---------|-----------|-------------|--------|
|DataRoot|A helyi adatgyökérmappa elérési útja.|null|igaz|

#### <a name="parameters-for-azure-data-lake-analytics-deployment"></a>Az Azure Data Lake Analytics telepítésének paraméterei

|Paraméter|Leírás|Alapértelmezett érték|Kötelező|
|---------|-----------|-------------|--------|
|Fiók|Megadja, hogy melyik Azure Data Lake Analytics-fiókra telepítse a fiók nevét.|null|igaz|
|ResourceGroup|Az Azure Data Lake Analytics-fiók Azure-erőforráscsoport neve.|null|igaz|
|SubscriptionId|Az Azure Data Lake Analytics-fiók Azure-előfizetés-azonosítója.|null|igaz|
|Bérlő|A bérlő neve az Azure Active Directory (Azure AD) tartományneve. Keresse meg az előfizetés-felügyeleti lapon az Azure Portalon.|null|igaz|
|AzureSDKPath|A függő szerelvények azure SDK-ban való keresésének elérési útja.|null|igaz|
|Interaktív|A hitelesítéshez az interaktív mód használata.|hamis|hamis|
|ClientID|Az Azure AD alkalmazásazonosító ja nem interaktív hitelesítéshez szükséges.|null|Nem interaktív hitelesítéshez szükséges.|
|Titkos|A nem interaktív hitelesítés titkos sága vagy jelszava. Csak megbízható és biztonságos környezetben használható.|null|A nem interaktív hitelesítéshez szükséges, vagy használja a SecreteFile fájlt.|
|Titkos fájl|A fájl menti a titkos kulcsot vagy a jelszót a nem interaktív hitelesítéshez. Ügyeljen arra, hogy csak az aktuális felhasználó legyen olvasható.|null|Nem interaktív hitelesítéshez szükséges, vagy használja a Titkos kulcsot.|
|Bizonyítványfájl|A fájl elmenti az X.509 tanúsítványt a nem interaktív hitelesítéshez. Az alapértelmezett beállítás az ügyféltitkos hitelesítés használata.|null|hamis|
| JobPrefix | Az U-SQL DDL-feladat adatbázis-központi telepítésének előtagja. | Deploy_ + DateTime.Now | hamis |

## <a name="next-steps"></a>További lépések

- [Az Azure Data Lake Analytics-kód tesztelése.](data-lake-analytics-cicd-test.md)
- [Futtassa az U-SQL parancsfájlt a helyi számítógépen.](data-lake-analytics-data-lake-tools-local-run.md)
- [U-SQL adatbázis-projekt használata U-SQL adatbázis fejlesztéséhez.](data-lake-analytics-data-lake-tools-develop-usql-database.md)
