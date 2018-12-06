---
title: Az Azure Data Lake Analytics egy CI/CD-folyamat beállítása
description: Megtudhatja, hogyan állíthat be folyamatos integrációt és folyamatos üzembe helyezés az Azure Data Lake Analytics.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 09/14/2018
ms.openlocfilehash: 76bfcd5e1b7e0215cfea7fbbfe1c51726d305fbc
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52969839"
---
# <a name="how-to-set-up-a-cicd-pipeline-for-azure-data-lake-analytics"></a>Az Azure Data Lake Analytics egy CI/CD-folyamat beállítása  

Ebből a cikkből megismerheti, hogyan állítható be a folyamatos integráció és készregyártás (CI/CD) a U-SQL-feladatok és a U-SQL-adatbázisok.  

## <a name="use-cicd-for-u-sql-jobs"></a>CI/CD használata a U-SQL-feladatok

Az Azure Data Lake Tools for Visual Studio biztosít a U-SQL projekt típusa, amely segítséget nyújt a U-SQL-parancsfájlok rendszerezheti. A U-SQL projekt használata kezelheti a U-SQL kód egyszerűen további CI/CD-forgatókönyvekhez.

## <a name="build-a-u-sql-project"></a>A U-SQL projekt létrehozása

A U-SQL projekt építhetők fel a Microsoft Build Engine (MSBuild) a megfelelő paraméter átadásával. Kövesse a cikkben egy U-SQL projekt buildelési folyamat beállítása.

### <a name="project-migration"></a>Migrálási projekt

-Létrehozási feladat egy U-SQL projekt beállítása előtt győződjön meg arról, hogy a legújabb verzióra a U-SQL projekt. Nyissa meg a U-SQL projekt fájlt a saját szerkesztőben, és ellenőrizze, hogy rendelkezik ezen elemek importálásához:

```   
<!-- check for SDK Build target in current path then in USQLSDKPath-->
<Import Project="UsqlSDKBuild.targets" Condition="Exists('UsqlSDKBuild.targets')" />
<Import Project="$(USQLSDKPath)\UsqlSDKBuild.targets" Condition="!Exists('UsqlSDKBuild.targets') And '$(USQLSDKPath)' != '' And Exists('$(USQLSDKPath)\UsqlSDKBuild.targets')" />
``` 

Ha nem, a projekt áttelepítése két lehetősége van:

- 1. lehetőség: Módosítsa a régi importálás elem az előző egy.
- 2. lehetőség: Nyissa meg a régi projekttel az Azure Data Lake Tools for Visual Studio. 2.3.3000.0 frissebb verzióját használja. A régi projektsablonnal automatikusan frissül a legújabb verzióra. 2.3.3000.0 frissebb verziójával létrehozott új projektek használja az új sablon.

### <a name="get-nuget"></a>NuGet beolvasása

MSBuild nem beépített támogatást nyújt a U-SQL-projekteket. Ez segítségre van szüksége, adjon hozzá egy hivatkozást a megoldáshoz való kell a [Microsoft.Azure.DataLake.USQL.SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) NuGet-csomagot, amely hozzáadja a szükséges nyelvi szolgáltatás.

A NuGet-csomag hivatkozás hozzáadásához kattintson a jobb gombbal a megoldást a Visual Studio Solution Explorerben, és válassza a **NuGet-csomagok kezelése**. Hozzáadhat egy nevű fájlt, vagy `packages.config` a megoldás mappában, és a put, a következő részből álló tartalmat a fájlba:

```xml 
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180620" targetFramework="net452" />
</packages>
``` 

### <a name="manage-u-sql-database-references"></a>U-SQL-adatbázis hivatkozások kezelése

Előfordulhat, hogy az U-SQL projekt U-SQL-parancsfájlok lekérdezési utasítás a U-SQL-adatbázis-objektumok. Ebben az esetben, hivatkoznia kell a megfelelő U-SQL database-projekt, amely tartalmazza az objektum definíciója a U-SQL projekt elkészítése előtt. Ez például akkor, amikor egy U-SQL-táblából, vagy egy szerelvény hivatkozik. 

Tudjon meg többet [U-SQL database-projekt](data-lake-analytics-data-lake-tools-develop-usql-database.md).

>[!NOTE]
>U-SQL database-projekt jelenleg nyilvános előzetes verzióban érhető el. Ha DROP utasítást a projekt, a build sikertelen lesz. A DROP utasítást hamarosan engedélyezve lesz.
>

### <a name="build-a-u-sql-project-with-the-msbuild-command-line"></a>Az MSBuild parancssorral a U-SQL projekt létrehozása

Először a projekt áttelepítése, és lekérése a NuGet-csomagot. Ezután hívja meg a standard szintű MSBuild-parancssorból a következő további argumentumok a U-SQL projekt: 

``` 
msbuild USQLBuild.usqlproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime;USQLTargetType=SyntaxCheck;DataRoot=datarootfolder;/p:EnableDeployment=true
``` 

Az argumentumok definíció- és értékek a következők:

* **USQLSDKPath = < U-SQL Nuget-csomag > \build\runtime**. Ezt a paramétert a telepítési útvonalat a NuGet-csomag az U-SQL nyelvi szolgáltatás hivatkozik.
* **USQLTargetType = egyesítési vagy SyntaxCheck**:
    * **Egyesítse**. Egyesítéssel lefordítja a háttérkód-fájlokat. Példa **.cs**, **.py**, és **.r** fájlokat. Ez a U-SQL-parancsfájlt az eredményül kapott felhasználói kód kódtár inlines. Példák: egy dll bináris, Python vagy R kódot.
    * **SyntaxCheck**. SyntaxCheck mód a U-SQL parancsfájl először egyesít háttérkód-fájlokat. Majd azt állítja össze a kódot a U-SQL parancsfájl.
* **DataRoot =<DataRoot path>**. DataRoot csak SyntaxCheck mód van szükség. Ha, létrehozta a parancsfájl SyntaxCheck móddal, MSBuild ellenőrzi, adatbázis-objektumok a szkriptben mutató hivatkozásokat. Épület, mielőtt a U-SQL-adatbázis a build gép DataRoot mappában található a hivatkozott objektumokat tartalmazó egyező helyi környezet beállítása. Is kezelheti a adatbázis függőségek szerint [hivatkozik egy U-SQL database-projekt](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project). MSBuild csak adatbázis-objektum hivatkozik rá, fájlok nem ellenőrzi.
* **EnableDeployment = true** vagy **hamis**. EnableDeployment azt jelzi, ha engedélyezett üzembe helyezése a hivatkozott U-SQL-adatbázisok az összeállítási folyamat során. Hivatkozhat egy U-SQL database-projektet, és az adatbázis-objektumok használata a U-SQL-szkript, ha a paraméter értéke **igaz**.

### <a name="continuous-integration-through-azure-pipelines"></a>Folyamatos integráció révén az Azure-folyamatok

A parancssor mellett is használhatja a Visual Studio-Build vagy az MSBuild feladat létrehozásához U-SQL-projektek Azure folyamatokban. A buildelési folyamat beállítása, hogy ne felejtse el hozzáadni a két feladatot a buildelési folyamat: NuGet visszaállítási feladat és a egy MSBuild feladatot.

![MSBuild-feladat U-SQL projekt](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 

1.  Adja hozzá a megoldás által hivatkozott NuGet-csomagot, amely tartalmazza az első NuGet visszaállítási feladat `Azure.DataLake.USQL.SDK`, hogy az MSBuild megtalálhatja a U-SQL nyelv célokat. Állítsa be **speciális** > **célkönyvtárban duplikátum** való `$(Build.SourcesDirectory)/packages` Ha közvetlenül a 2. lépésben az MSBuild-argumentumok minta használni szeretne.

    ![U-SQL projekt NuGet-visszaállítási feladat](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2.  Állítsa be MSBuild-argumentumok a Visual Studio build tools vagy az MSBuild feladat a következő példában látható módon. Vagy ezek az argumentumok változóival adhatja meg az Azure-folyamatok buildelési folyamat.

    ![U-SQL projekt CI/CD MSBuild változókat határozhat meg](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables.png) 

    ```
    /p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime /p:USQLTargetType=SyntaxCheck /p:DataRoot=$(Build.SourcesDirectory) /p:EnableDeployment=true
    ```

### <a name="u-sql-project-build-output"></a>U-SQL projekt felépítési művelet kimenetében

Miután lefuttatta a build, az összes parancsfájl a U-SQL projekt fejlesztett és nevű zip-fájlba való `USQLProjectName.usqlpack`. A mappastruktúra a projektben a tömörített felépítési művelet kimenetében másolatok.

> [!NOTE]
>
> Háttérkód fájlok az egyes U-SQL-szkript, parancsfájl kimenete egy beágyazott utasítást összevonva fogja tartalmazni.
>

## <a name="test-u-sql-scripts"></a>U-SQL-parancsfájlok tesztelése

Az Azure Data Lake U-SQL-parancsfájlok és C# UDO/UDAG/UDF tesztelési projekt tartalmazza:
* Ismerje meg, hogyan [vizsgálati eset a U-SQL-parancsfájlok és a kiterjesztett C#-kód hozzáadása](data-lake-analytics-cicd-test.md#test-u-sql-scripts).
* Ismerje meg, hogyan [vizsgálati eset futtatása az Azure-folyamatok](data-lake-analytics-cicd-test.md#run-test-cases-in-azure-devops).

## <a name="deploy-a-u-sql-job"></a>A U-SQL-feladat telepítése

Miután ellenőrizte a kódot a build és a vizsgálati folyamat során, elküldheti a közvetlenül az Azure-folyamatok keresztül az Azure PowerShell-feladat U-SQL feladatok. A szkript üzembe helyezése az Azure Data Lake Store vagy az Azure Blob storage- és [az ütemezett feladatok futtatása az Azure Data factoryvel](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics).

### <a name="submit-u-sql-jobs-through-azure-pipelines"></a>Az Azure-folyamatok keretében U-SQL-feladatok elküldése

A build kimenetét a U-SQL projekt egy zip-fájl neve **USQLProjectName.usqlpack**. A zip-fájl tartalmazza a projekt összes U-SQL-parancsfájlok. Használhatja a [Azure PowerShell-lel feladat](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-powershell?view=vsts) közvetlenül az Azure-folyamatok U-SQL-feladatok elküldése a következő PowerShell-parancsfájlt a folyamatokban.

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

        $jobToSubmit = Submit-AzureRmDataLakeAnalyticsJob -Account $ADLAAccountName -Name $scriptName -ScriptPath $usqlFile -DegreeOfParallelism $DegreeOfParallelism
        
        LogJobInformation $jobToSubmit
        
        Write-Output "Waiting for job to complete. Job ID:'{$($jobToSubmit.JobId)}', Name: '$($jobToSubmit.Name)' "
        $jobResult = Wait-AzureRmDataLakeAnalyticsJob -Account $ADLAAccountName -JobId $jobToSubmit.JobId  
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

### <a name="deploy-u-sql-jobs-through-azure-data-factory"></a>Azure Data factoryvel U-SQL feladatok üzembe helyezése

U-SQL feladatok közvetlenül az Azure-folyamatok küldhet. A kész parancsprogramok feltöltheti az Azure Data Lake Store vagy az Azure Blob storage- vagy és [az ütemezett feladatok futtatása az Azure Data factoryvel](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics).

Használja a [Azure PowerShell-lel feladat](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-powershell?view=vsts) a U-SQL-parancsfájlokat tölthet fel egy Azure Data Lake Store-fiókot a következő PowerShell-parancsfájlt az Azure-folyamatokban:

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
    [Parameter(Mandatory=$false)][string]$DesitinationFolder = "USQLScriptSource" # Desitination folder in ADLS
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
        Import-AzureRmDataLakeStoreItem -AccountName $ADLSName -Path $file.FullName -Destination "/$(Join-Path $DesitinationFolder $file)" -Force
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

## <a name="cicd-for-a-u-sql-database"></a>CI/CD-U-SQL-adatbázis

Az Azure Data Lake Tools for Visual Studio biztosít a U-SQL database projektsablonjai, amelyek segítségével fejlesztése, kezelése és üzembe helyezése a U-SQL-adatbázisok. Tudjon meg többet a [U-SQL database-projekt](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="build-u-sql-database-project"></a>U-SQL database-projekt létrehozása

### <a name="get-the-nuget-package"></a>A NuGet-csomag beszerzése

MSBuild nem beépített támogatást nyújt a U-SQL database-projektek. Első ezt a lehetőséget, adjon hozzá egy hivatkozást a megoldáshoz való kell a [Microsoft.Azure.DataLake.USQL.SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) NuGet-csomagot, amely hozzáadja a szükséges nyelvi szolgáltatás.

A NuGet-csomag hivatkozás hozzáadásához kattintson a jobb gombbal a megoldást a Visual Studio Megoldáskezelőjében. Válasszon **NuGet-csomagok kezelése**. Majd keresse meg és telepítse a NuGet-csomagot. Hozzáadhat egy nevű fájlt, vagy **packages.config** a megoldás mappában, és a put, a következő részből álló tartalmat a fájlba:

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180615" targetFramework="net452" />
</packages>
```

### <a name="build-u-sql-a-database-project-with-the-msbuild-command-line"></a>U-SQL az MSBuild parancssorral adatbázis a projekt létrehozása

A U-SQL database-projekt létrehozásához, a standard szintű MSBuild parancssor hívja, és adja át a U-SQL SDK NuGet-csomag hivatkozás a további argumentumként. Lásd a következő példát: 

```
msbuild DatabaseProject.usqldbproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime
```

Az argumentum `USQLSDKPath=<U-SQL Nuget package>\build\runtime` telepítési elérési útját a NuGet-csomagot a U-SQL nyelvi szolgáltatás hivatkozik.

### <a name="continuous-integration-with-azure-pipelines"></a>Folyamatos integráció Azure folyamatok

A parancssor mellett használhatja a Visual Studio Build vagy egy MSBuild feladat létrehozásához U-SQL database-projektek az Azure-folyamatok. Szeretne beállítani egy összeállítási feladat, ne felejtse el hozzáadni a két feladatot a buildelési folyamat: NuGet visszaállítási feladat és a egy MSBuild feladatot.

   ![CI/CD MSBuild feladat U-SQL projekt](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 


1.  Adja hozzá a NuGet visszaállítási feladat lekérése a megoldás hivatkozott NuGet-csomagot, amely tartalmazza a `Azure.DataLake.USQL.SDK`, hogy az MSBuild megtalálhatja a U-SQL nyelv célokat. Állítsa be **speciális** > **célkönyvtárban duplikátum** való `$(Build.SourcesDirectory)/packages` Ha közvetlenül a 2. lépésben az MSBuild-argumentumok minta használni szeretne.

    ![CI/CD NuGet-feladat U-SQL projekt](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2.  Állítsa be MSBuild-argumentumok a Visual Studio build tools vagy az MSBuild feladat a következő példában látható módon. Vagy ezek az argumentumok változóival adhatja meg az Azure-folyamatok buildelési folyamat.

   ![U-SQL-adatbázis projekt CI/CD MSBuild változókat határozhat meg](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables-database-project.png) 

    ```
    /p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime
    ```
 
### <a name="u-sql-database-project-build-output"></a>U-SQL database projekt felépítési művelet kimenetében

A U-SQL database-projekt van egy U-SQL-adatbázis központi telepítési csomagot, az utótag nevű kimeneti build `.usqldbpack`. A `.usqldbpack` csomag egy zip-fájlt, amely tartalmazza az összes DDL-utasítást egy U-SQL parancsfájl egy DDL-mappában. Ez tartalmazza az összes **.dll** és további fájlokat egy ideiglenes mappába a szerelvény.

## <a name="test-table-valued-functions-and-stored-procedures"></a>Táblázat értékű függvények és tárolt eljárások tesztelése

A táblázat értékű függvények és tárolt eljárások teszteseteket közvetlenül hozzáadása jelenleg nem támogatott. Áthidaló megoldásként létrehozhat egy U-SQL projekt, amely rendelkezik a U-SQL-parancsfájlok meg az ezekhez a függvényekhez teszteseteket írni a számukra. A következő lépésekkel állíthatja be a táblázat értékű függvények és tárolt eljárások a U-SQL-adatbázis projektben meghatározott esetek:

1.  Hozzon létre egy U-SQL projekt tesztelési célokra, és a táblázat értékű függvények és tárolt eljárások hívása U-SQL-parancsfájlokat írhat.
2.  Vegyen fel egy adatbázis hivatkozást a U-SQL projekt. A táblázat értékű függvény és a tárolt eljárás definíció lekéréséhez, hivatkoznia kell az adatbázis-projektje, amely tartalmazza a DDL-utasítást. Tudjon meg többet [adatbázis-hivatkozások](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).
3.  Adja hozzá a U-SQL-parancsprogramokat, táblázat értékű függvények és tárolt eljárások hívása vizsgálati eset. Ismerje meg, hogyan [adja hozzá a U-SQL-parancsfájlok esetek](data-lake-analytics-cicd-test.md#test-u-sql-scripts).

## <a name="deploy-u-sql-database-through-azure-pipelines"></a>Az Azure-folyamatok keretében U-SQL-adatbázis üzembe helyezése

`PackageDeploymentTool.exe` biztosítja a programozás és parancssori felületek, amelyek segítenek a U-SQL-adatbázis központi telepítési csomagok üzembe helyezése **.usqldbpack**. Az SDK tartalmazza a [U-SQL SDK NuGet-csomagot](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), a következő helyen található **build/runtime/PackageDeploymentTool.exe**. Használatával `PackageDeploymentTool.exe`, telepítheti a U-SQL-adatbázisok az Azure Data Lake Analytics és a helyi fiókok.

> [!NOTE]
>
> A U-SQL-adatbázis üzembe helyezése folyamatban a PowerShell parancssori támogatással és a feladatkiadási tevékenység Azure folyamatok támogatása.
>

Az alábbi lépéseket egy adatbázis-üzembe helyezési feladat az Azure-folyamatok beállítása:

1. PowerShell-parancsprogram feladat hozzáadása a build vagy kibocsátásában, és hajtsa végre a következő PowerShell-parancsfájlt. Ez a feladat segít az Azure SDK-függőségek első `PackageDeploymentTool.exe` és `PackageDeploymentTool.exe`. Beállíthatja a **- AzureSDK** és **- DBDeploymentTool** betölteni a függőségeket és a központi telepítési eszköz bizonyos mappákra paramétereket. Adja át a **- AzureSDK** elérési útját `PackageDeploymentTool.exe` , a **- AzureSDKPath** paraméter a 2. lépésben. 

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

2. Adjon hozzá egy **parancssori feladatot** egy build és kiadás folyamat, és adja meg a szkript hívásával `PackageDeploymentTool.exe`. `PackageDeploymentTool.exe` található a meghatározott alatt **$DBDeploymentTool** mappát. A minta parancsfájl a következőképpen történik: 

    * U-SQL-adatbázis helyi üzembe helyezése:

        ```
        PackageDeploymentTool.exe deploylocal -Package <package path> -Database <database name> -DataRoot <data root path>
        ```

    * Interaktív hitelesítés üzemmód használata a U-SQL-adatbázis üzembe helyezése egy Azure Data Lake Analytics-fiókhoz:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -AzureSDKPath <azure sdk path> -Interactive
        ```

    * Használat **secrete** U-SQL-adatbázis üzembe helyezése egy Azure Data Lake Analytics-fiókhoz való hitelesítés:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete>
        ```

    * Használat **tanúsítványfájl** U-SQL-adatbázis üzembe helyezése egy Azure Data Lake Analytics-fiókhoz való hitelesítés:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete> -CertFile <certFile>
        ```

### <a name="packagedeploymenttoolexe-parameter-descriptions"></a>PackageDeploymentTool.exe paraméter leírása

#### <a name="common-parameters"></a>Az általános paraméterek

| Paraméter | Leírás | Alapértelmezett érték | Szükséges |
|---------|-----------|-------------|--------|
|Csomag|A U-SQL-adatbázis központi telepítési csomag telepítendő elérési útja.|NULL|true|
|Adatbázis|A telepített vagy létrehozott adatbázis neve.|master|false|
|Naplófájl|A fájl elérési útját a naplózást. Alapértelmezés szerint ki (konzol) standard.|NULL|false|
|LogLevel|Naplózási szintjének: részletes, normál, figyelmeztetés vagy hiba.|LogLevel.Normal|false|

#### <a name="parameter-for-local-deployment"></a>Helyi telepítés paraméter

|Paraméter|Leírás|Alapértelmezett érték|Szükséges|
|---------|-----------|-------------|--------|
|DataRoot|A helyi adatok gyökérmappa elérési útja.|NULL|true|

#### <a name="parameters-for-azure-data-lake-analytics-deployment"></a>Az Azure Data Lake Analytics üzembe helyezési paraméterek

|Paraméter|Leírás|Alapértelmezett érték|Szükséges|
|---------|-----------|-------------|--------|
|Fiók|Itt adhatja meg, amely az Azure Data Lake Analytics-fiók telepíteni a fiók nevét.|NULL|true|
|ResourceGroup|Az Azure erőforráscsoport nevét az Azure Data Lake Analytics-fiók.|NULL|true|
|SubscriptionId|Az Azure Data Lake Analytics-fiók Azure-előfizetés azonosítója.|NULL|true|
|Bérlő|A bérlő neve az Azure Active Directory (Azure AD) tartománynév. Az előfizetés-kezelési oldalán, az Azure Portalon keresse meg azt.|NULL|true|
|AzureSDKPath|Függő szerelvényei keresése az Azure SDK elérési útja.|NULL|true|
|Interaktív|E interaktív módban használja a hitelesítéshez.|false|false|
|ClientID|Az Azure AD-alkalmazás azonosítója nem interaktív hitelesítés szükséges.|NULL|Nem interaktív hitelesítés szükséges.|
|Secrete|A secrete vagy a jelszó nem interaktív hitelesítés. Csak a megbízható és biztonságos környezetben használandó.|NULL|Nem interaktív hitelesítéssel, vagy pedig SecreteFile használata szükséges.|
|SecreteFile|A fájl mentésekor a secrete vagy a jelszó nem interaktív hitelesítés. Győződjön meg arról, hogy csak az aktuális felhasználó által olvasható legyen.|NULL|Nem interaktív hitelesítéssel, vagy pedig Secrete használata szükséges.|
|Tanúsítványfájl|A fájl mentésekor X.509 tanúsítvány, nem interaktív hitelesítés. Az alapértelmezett érték használatához az ügyfél hitelesítési secrete.|NULL|false|
| JobPrefix | Az előtag, az adatbázis DDL U-SQL feladatok üzembe helyezéséhez. | Deploy_ + DateTime.Now | false |

## <a name="next-steps"></a>További lépések

- [Az Azure Data Lake Analytics kód tesztelése](data-lake-analytics-cicd-test.md).
- [U-SQL-szkript futtatása helyi gépen](data-lake-analytics-data-lake-tools-local-run.md).
- [U-SQL adatbázis projekt használata a U-SQL-adatbázis fejleszthet](data-lake-analytics-data-lake-tools-develop-usql-database.md).
