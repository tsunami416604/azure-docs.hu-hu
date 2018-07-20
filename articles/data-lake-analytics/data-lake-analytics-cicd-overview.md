---
title: CI/CD-folyamat beállítása az Azure Data Lake Analytics |} A Microsoft Docs
description: Megtudhatja, hogyan állíthat be folyamatos integrációt és folyamatos üzembe helyezés az Azure Data Lake Analytics.
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: ''
editor: ''
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: c069bc2a6147a021ea9bdf37e2926d5c8f33281c
ms.sourcegitcommit: 727a0d5b3301fe20f20b7de698e5225633191b06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39145009"
---
# <a name="how-to-set-up-cicd-pipeline-for-azure-data-lake-analytics"></a>Az Azure Data Lake Analytics CI/CD-folyamat beállítása

Ebben a dokumentumban, útmutató U-SQL-feladatok és a U-SQL-adatbázisok CI/CD-folyamat beállítása.

## <a name="cicd-for-u-sql-job"></a>CI/CD-U-SQL-feladat

Az Azure Data Lake Tools for Visual Studio biztosít a U-SQL projekt típusa, amely segítséget nyújt a U-SQL-parancsfájlok rendszerezheti. A további CI/CD-forgatókönyvek a U-SQL projekt használata kezelheti a U-SQL kód egyszerűen teszi.

## <a name="build-u-sql-project"></a>U-SQL projekt létrehozása

A U-SQL projekt építhetők fel az MSBuild tartalmában való böngészéshez illessze a megfelelő paramétereket. Kövesse az alábbi U-SQL-projekteket a buildelési folyamat beállítása.

### <a name="project-migration"></a>Migrálási projekt

Mielőtt beállítaná a U-SQL projekt összeállítási feladat, ügyeljen arra, hogy a U-SQL projekt legújabb verzióját használja. Nyissa meg a szerkesztőben a U-SQL projekt fájlt, és ellenőrizze, hogy importálás alatt:

```   
<!-- check for SDK Build target in current path then in USQLSDKPath-->
<Import Project="UsqlSDKBuild.targets" Condition="Exists('UsqlSDKBuild.targets')" />
<Import Project="$(USQLSDKPath)\UsqlSDKBuild.targets" Condition="!Exists('UsqlSDKBuild.targets') And '$(USQLSDKPath)' != '' And Exists('$(USQLSDKPath)\UsqlSDKBuild.targets')" />
``` 

Ha nem, a projekt áttelepítése két lehetősége van:

- 1. lehetőség: A régi importálása a fenti módosítsa.
- 2. lehetőség: Nyissa meg a régi projekttel Azure Data Lake Tools for Visual Studio verzió 2.3.3000.0 után. A régi projektsablonnal automatikusan frissül a legújabb verzióra. Az új verzió 2.3.3000.0 után létrehozott projekt közvetlenül az új sablont használ.

### <a name="get-nuget-package"></a>NuGet-csomag beszerzése

MSBuild nem biztosít beépített támogatást nyújt a U-SQL projekt típusa. Adja hozzá ezt a lehetőséget, adjon hozzá egy hivatkozást a megoldáshoz való kell a [Microsoft.Azure.DataLake.USQL.SDK Nuget-csomag](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) , amely hozzáadja a szükséges nyelvi szolgáltatás.

A NuGet-csomag hivatkozás hozzáadásához kattintson a jobb gombbal a megoldásra a Megoldáskezelőben és válassza a **NuGet-csomagok kezelése**. Hozzáadhat egy nevű fájlt, vagy `packages.config` megoldás a mappában, és adja hozzá a tartalom alatt azt.

```xml 
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180620" targetFramework="net452" />
</packages>
``` 

### <a name="manage-u-sql-database-references"></a>U-SQL-adatbázis hivatkozások kezelése

Ha a U-SQL-parancsfájlok a U-SQL projekt lekérdezési utasítás a U-SQL-adatbázis-objektumok, például lekérdezése egy U-SQL-táblára vagy egy szerelvény hivatkozik, kell hivatkoznia a megfelelő U-SQL adatbázis-projekt, amely tartalmazza ezeket az objektumokat, mielőtt definíciója a U-SQL projekt létrehozásához.

[További tudnivalók a U-SQL database-projekt](data-lake-analytics-data-lake-tools-develop-usql-database.md)

>[!NOTE]
>U-SQL database-projekt jelenleg nyilvános előzetes verzióban érhető el. Ha DROP utasítást a projekt, a build sikertelen lesz. A DROP utasítást hamarosan engedélyezve lesz.
>

### <a name="build-u-sql-project-with-msbuild-command-line"></a>U-SQL projekt az MSBuild parancssori felületével

Után a projekt a migrálást, és a NuGet-csomag első, a standard szintű MSBuild parancssor az alábbi további argumentumokkal meghívhatja a U-SQL projekt:

``` 
msbuild USQLBuild.usqlproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime;USQLTargetType=SyntaxCheck;DataRoot=datarootfolder;/p:EnableDeployment=true
``` 

Az argumentumok definíció és az értékek a következők:

* USQLSDKPath = < U-SQL Nuget-csomag > \build\runtime: ezt a paramétert a telepítési útvonal a NuGet-csomag az U-SQL nyelvi szolgáltatás hivatkozik.
* USQLTargetType = egyesítési vagy SyntaxCheck:
    * Egyesítés: Egyesítési lefordítja a háttérkód-fájlok, például .cs, .py és .r-fájlok és az eredményül kapott felhasználói kód library inlines (például dll bináris, Python vagy R-kód), a U-SQL parancsfájl.
    * SyntaxCheck: SyntaxCheck mód háttérkód fájlok először egyesíti a U-SQL-parancsfájlt, és majd lefordítja a U-SQL-parancsfájl érvényesítéséhez a kódot.
* DataRoot =<DataRoot path>: DataRoot csak SyntaxCheck módra van szükség. A parancsfájl SyntaxCheck móddal kiépítésekor MSBuild ellenőrzi a parancsfájl az adatbázis-objektumok mutató hivatkozásokat. Győződjön meg arról, hogy a megfelelő helyi környezetet, amely tartalmazza az U-SQL-adatbázist, a build gép DataRoot mappa létrehozása előtt a hivatkozott objektumokat. Is kezelheti a adatbázis függőségek szerint [hivatkozik egy U-SQL database-projekt](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project). Vegye figyelembe, hogy az MSBuild csak ellenőrzi adatbázis objektumok referencia, nem a fájlokat.
* EnableDeployment = igaz vagy hamis értéket: EnableDeployment azt jelzi, ha engedélyezett a hivatkozott U-SQL-adatbázisok üzembe helyezése a létrehozási folyamat során. U-SQL database-projekt hivatkoznak, és az adatbázis-objektumok használata a U-SQL-szkript, ha a paraméter értéke igaz.

### <a name="continuous-integration-with-visual-studio-team-service"></a>Folyamatos integráció a Visual Studio Team Service

A parancssor mellett ügyfelek is használhatják a Visual Studio Build vagy MSBuild feladat U-SQL-projekteket a Visual Studio Team Services build. Összeállítási feladat beállítása, győződjön meg arról, hogy:

1.  Adja hozzá a NuGet-visszaállítási feladatot a megoldás első hivatkozott, beleértve a NuGet-csomag `Azure.DataLake.USQL.SDK`, hogy az MSBuild megtalálhatja a U-SQL nyelv célokat. Állítsa be **Speciális > célkönyvtárban duplikátum** , `$(Build.SourcesDirectory)/packages` Ha közvetlenül a 2. lépésben az MSBuild-argumentumok minta használni szeretne.

    ![Data Lake beállítása CI CD MSBuild feladat U-SQL projekt](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 

    ![Data Lake beállítása CI CD Nuget feladat U-SQL projekt](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2.  MSBuild-argumentumok set, és az argumentumok állítható be alább például a Visual Studio Build vagy MSBuild feladat, vagy a VSTS builddefiníció adhatja meg ezek az argumentumok változókat.

    ```
    /p:USQLSDKPath=/p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime /p:USQLTargetType=SyntaxCheck /p:DataRoot=$(Build.SourcesDirectory) /p:EnableDeployment=true
    ```

    ![Data Lake CI CD MSBuild változók beállítása a U-SQL projekt](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables.png) 

### <a name="u-sql-project-build-output"></a>U-SQL projekt felépítési művelet kimenetében

A U-SQL projekt összes parancsfájl fejlesztett és használt kimeneti adattípus egy zip-fájlba nevű után buildet futtató `USQLProjectName.usqlpack`. A mappastruktúra a projektben a tömörített felépítési művelet kimenetében tárolni fogja.

>[!NOTE]
>
>Minden egyes U-SQL parancsfájl a fájl mögötti kódban összevonva fogja tartalmazni, a parancsfájl kimenete beágyazott utasítást.
>

## <a name="test-u-sql-script"></a>U-SQL parancsfájl tesztelési

Az Azure Data Lake U-SQL-szkript és a C# UDO/UDAG/UDF tesztelési projekt biztosítja:
* [Útmutató U-SQL-szkript és a kiterjesztett C#-kód esetek hozzáadása](data-lake-analytics-cicd-test.md#test-u-sql-scripts)
* [Ismerje meg, ezek vizsgálati eset futtatása a Visual Studio Team Service](data-lake-analytics-cicd-test.md#run-test-cases-in-visual-studio-team-service)

## <a name="u-sql-job-deployment"></a>U-SQL projekt üzembe helyezés

Létrehozási és tesztelési kód ellenőrzése után folyamat, U-SQL feladatok közvetlenül a Visual Studio Team Service keresztül nyújthatja **Azure PowerShell-lel feladat**. A szkript üzembe helyezése az Azure Data Lake Store vagy az Azure Blob Storage- és [az ütemezett feladatok futtatása az Azure Data factoryvel](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics).

### <a name="submit-u-sql-jobs-through-visual-studio-team-service"></a>A Visual Studio Team Services segítségével U-SQL-feladatok elküldése

A build kimenetét a U-SQL projekt egy zip-fájl neve **USQLProjectName.usqlpack** összes U-SQL parancsfájl tartalmazza a projektben. Használhatja a [Azure PowserShell feladatot a Visual Studio Team Service](https://docs.microsoft.com/vsts/pipelines/tasks/deploy/azure-powershell?view=vsts) az alábbi mintaparancsfájl PowserShell elküldeni a U-SQL feladatok közvetlenül a Visual Studio Team Services build vagy kibocsátásában.

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

Mellett, közvetlenül a Visual Studio Team Services a U-SQL-feladatok elküldése, feltöltheti a kész parancsprogramok az Azure Data Lake Store vagy az Azure Blob Storage- és [az ütemezett feladatok futtatása az Azure Data factoryvel](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics).

Használja a [Azure PowerShell-feladatot a Visual Studio Team Service](https://docs.microsoft.com/vsts/pipelines/tasks/deploy/azure-powershell?view=vsts) az alábbi PowerShell-példaszkript feltölteni a U-SQL-szkriptek az Azure Data Lake Store-fiókba.

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

## <a name="cicd-for-u-sql-database"></a>CI/CD-U-SQL-adatbázis

Az Azure Data Lake Tools for Visual Studio ide, U-SQL-adatbázis projektsablon segít a fejlesztőknek a fejlesztést, kezelése és üzembe helyezése a U-SQL-adatbázisok, gyors és egyszerű. [További információ a U-SQL database-projekt](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="build-u-sql-database-project"></a>U-SQL database-projekt létrehozása

### <a name="get-nuget-package"></a>NuGet-csomag beszerzése

MSBuild nem biztosít beépített támogatást nyújt a U-SQL-adatbázis projekt típusa. Adja hozzá ezt a lehetőséget, adjon hozzá egy hivatkozást a megoldáshoz való kell a [Microsoft.Azure.DataLake.USQL.SDK Nuget-csomag](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) , amely hozzáadja a szükséges nyelvi szolgáltatás.

A NuGet-csomag hivatkozás hozzáadásához kattintson a jobb gombbal a megoldásra a Megoldáskezelőben, és válassza a **NuGet-csomagok kezelése** megoldás, majd keresse meg és telepítse a NuGet-csomagot. Vagy adjon hozzá egy fájlt "packages.config" nevű megoldás a mappában, és adja hozzá a tartalom alatt azt.

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180615" targetFramework="net452" />
</packages>
```

### <a name="build-u-sql-database-project-with-msbuild-command-line"></a>MSBuild parancssorral U-SQL database-projekt létrehozása

A standard szintű MSBuild parancssor és a U-SQL-adatbázis projekt alábbi módon további argumentumként a U-SQL SDK NuGet-csomag hivatkozás pass hívása:

```
msbuild DatabaseProject.usqldbproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime
```

Az argumentumok `USQLSDKPath=<U-SQL Nuget package>\build\runtime` telepítési elérési útját a NuGet-csomagot a U-SQL nyelvi szolgáltatás hivatkozik.

### <a name="continuous-integration-with-visual-studio-team-service"></a>Folyamatos integráció a Visual Studio Team Service

A parancssor mellett ügyfelek is használhatják **Visual Studio Build** vagy **MSBuild feladat** U-SQL database-projektek a Visual Studio Team Service hozhat létre. Összeállítási feladat beállítása, győződjön meg arról, hogy:

1.  Adja hozzá a NuGet-visszaállítási feladatot a megoldás első hivatkozott, beleértve a NuGet-csomag `Azure.DataLake.USQL.SDK`, hogy az MSBuild megtalálhatja a U-SQL nyelv célokat. Állítsa be **Speciális > célkönyvtárban duplikátum** , `$(Build.SourcesDirectory)/packages` Ha közvetlenül a 2. lépésben az MSBuild-argumentumok minta használni szeretne.

    ![Data Lake beállítása CI CD MSBuild feladat U-SQL projekt](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 

    ![Data Lake beállítása CI CD Nuget feladat U-SQL projekt](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2.  MSBuild-argumentumok set, és az argumentumok állítható be alább például a Visual Studio Build vagy MSBuild feladat, vagy a VSTS builddefiníció adhatja meg ezek az argumentumok változókat.

    ```
    /p:USQLSDKPath=/p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime
    ```

    ![A Data Lake U-SQL-adatbázis projekt CI CD MSBuild-változók beállítása](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables-database-project.png) 

### <a name="u-sql-database-project-build-output"></a>U-SQL database projekt felépítési művelet kimenetében

A U-SQL database-projekt van egy U-SQL-adatbázis központi telepítési csomagot, utótaggal rendelkező nevű kimeneti build `.usqldbpack`. A `.usqldbpack` csomag egy zip-fájlt tartalmaz minden DDL-utasítások a DDL-mappát, és mindegyik .dll és további fájlokat egy U-SQL parancsfájl szerelvények Temp mappában.

## <a name="test-table-valued-function-and-stored-procedure"></a>Táblázat értékű függvény és a tárolt eljárás tesztelése

Jelen pillanatban hozzáadása a táblázat értékű függvények és tárolt eljárások esetek közvetlenül nem támogatott. Áthidaló megoldásként hozzon létre egy U-SQL-projektet, amely rendelkezik ezekhez a függvényekhez hívása U-SQL-parancsfájlok és a vizsgálati eset írása a számukra. Kövesse az alábbi táblázat értékű függvények és tárolt eljárások a U-SQL-adatbázis projektben meghatározott teszteseteket beállításának lépései:

1.  Hozzon létre egy U-SQL projekt tesztelési célra, és a táblázat értékű függvények és tárolt eljárások hívása U-SQL-parancsfájlokat írhat.
2.  Fel kell vennie a U-SQL projekt adatbázis hivatkozást. Annak érdekében, hogy a táblázat értékű függvény és a tárolt eljárás definíció lekéréséhez, hivatkoznia kell az adatbázis-projektje, amely tartalmazza a DDL-utasítást. [További információ az adatbázis-hivatkozás](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).
3.  Adja hozzá az U-SQL-parancsfájlok, amelyek a táblázat értékű függvények és tárolt eljárások hívása vizsgálati eset. [Útmutató U-SQL parancsfájl tesztelési eseteinek hozzáadása](data-lake-analytics-cicd-test.md#test-u-sql-scripts).

## <a name="deploy-u-sql-database-through-visual-studio-team-service"></a>A Visual Studio Team Services segítségével U-SQL-adatbázis üzembe helyezése

`PackageDeploymentTool.exe` biztosítja a programozás és parancssori felületek, amelyek segítenek a U-SQL-adatbázis üzembe helyezési package(.usqldbpack) üzembe helyezéséhez. Az SDK része a [U-SQL SDK NuGet-csomagot](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), build/runtime/PackageDeploymentTool.exe címen található. Használatával `PackageDeploymentTool.exe`, U-SQL-adatbázisok az Azure Data Lake Analytics és a helyi fiókot is telepíthet.

>[!NOTE]
>
>Sor támogatása és a Visual Studio Team Services kiadási tevékenység támogatása U-SQL-adatbázis üzembe helyezése PowerShell-parancs van folyamatban.
>

Kövesse az alábbi lépéseket a Visual Studio Team Service adatbázis-telepítési feladat beállításához:

1. PowerShell-parancsprogram feladat hozzáadása a build vagy kiadási folyamatot, és hajtsa végre a lenti PowerShell-parancsfájlt. Ez a feladat segít az Azure SDK-függőségek első `PackageDeploymentTool.exe` és `PackageDeploymentTool.exe`. A betölteni a függőségeket és üzembe helyezési eszköz egy bizonyos mappákra - AzureSDK és - DBDeploymentTool paramétereket állíthatja be. -AzureSDK elérési útját adja át `PackageDeploymentTool.exe` - AzureSDKPath paraméterként a 2. lépésben. 

    ```powershell
    <#
        This script is used for getting dependencies and SDKs for U-SQL database deployment.
        PowerShell command line support for deploying U-SQL database package(.usqldbpack file) will come soon.
        
        Example :
            GetUSQLDBDeploymentSDK.ps1 -AzureSDK "AzureSDKFolderPath" -DBDeploymentTool "DBDeploymentToolFolderPath"
    #>

    param (
        [string]$AzureSDK = "AzureSDK", # Folder to cache Azure SDK dependencies
        [string]$DBDeploymentTool = "DBDeploymentTool", # Folder to cache U-SQL dabatase deployment tool
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

    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Analytics/3.2.3-preview -outf Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Store/2.3.3-preview -outf Microsoft.Azure.Management.DataLake.Store.2.3.3-preview.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.3 -outf Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime/2.3.11 -outf Microsoft.Rest.ClientRuntime.2.3.11.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure/3.3.7 -outf Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure.Authentication/2.3.3 -outf Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip
    iwr https://www.nuget.org/api/v2/package/Newtonsoft.Json/6.0.8 -outf Newtonsoft.Json.6.0.8.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.DataLake.USQL.SDK/ -outf USQLSDK.zip

    echo "Extracting packages..."

    Expand-Archive Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview -Force
    Expand-Archive Microsoft.Azure.Management.DataLake.Store.2.3.3-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Store.2.3.3-preview -Force
    Expand-Archive Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip -DestinationPath Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.2.3.11.zip -DestinationPath Microsoft.Rest.ClientRuntime.2.3.11 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.3.3.7 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3 -Force
    Expand-Archive Newtonsoft.Json.6.0.8.zip -DestinationPath Newtonsoft.Json.6.0.8 -Force
    Expand-Archive USQLSDK.zip -DestinationPath USQLSDK -Force

    echo "Copy required DLLs to output folder..."

    mkdir $AzureSDK -Force
    mkdir $DBDeploymentTool -Force
    copy Microsoft.Azure.Management.DataLake.Analytics.3.2.3-preview\lib\net452\*.dll $AzureSDK
    copy Microsoft.Azure.Management.DataLake.Store.2.3.3-preview\lib\net452\*.dll $AzureSDK
    copy Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.2.3.11\lib\net452\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3\lib\net452\*.dll $AzureSDK
    copy Newtonsoft.Json.6.0.8\lib\net45\*.dll $AzureSDK
    copy USQLSDK\build\runtime\*.* $DBDeploymentTool
    ```

2. Adjon hozzá egy **parancssori feladatot** a, illetve a kibocsátási folyamat, és adja meg a szkript meghívása `PackageDeploymentTool.exe`. `PackageDeploymentTool.exe` meghatározott $DBDeploymentTool mappában található. A minta parancsfájl a következőképpen történik: 

    * Helyileg a U-SQL-adatbázis üzembe helyezése

        ```
        PackageDeploymentTool.exe deploylocal -Package <package path> -Database <database name> -DataRoot <data root path>
        ```

    * Interaktív hitelesítés üzemmód használata a U-SQL-adatbázis üzembe helyezése az Azure Data Lake Analytics-fiók:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tanant name> -AzureSDKPath <azure sdk path> -Interactive
        ```

    * Használja a U-SQL-adatbázis üzembe helyezése az Azure Data Lake Analytics-fiók hitelesítési secrete:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tanant name> -ClientId <client id> -Secrete <secrete>
        ```

    * Tanúsítványfájl hitelesítés használata a U-SQL-adatbázis üzembe helyezése az Azure Data Lake Analytics-fiók:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tanant name> -ClientId <client id> -Secrete <secrete> -CertFile <certFile>
        ```

**PackageDeploymentTool.exe paraméter leírása:**

**Általános paraméterek:**

|Paraméter|Leírás|Alapértelmezett érték|Szükséges|
|---------|-----------|-------------|--------|
|Csomag|A U-SQL-adatbázis központi telepítési csomag telepítendő elérési útja|NULL|true|
|Adatbázis|Adatbázis neve telepíthető / vagy létrehozása|master|false|
|Naplófájl|Naplózás, alapértelmezett standard ki (konzol) fájl elérési útját|NULL|false|
|LogLevel|Naplózási szintjének: részletes, normál, figyelmeztetés, hiba|LogLevel.Normal|false|

**Helyi telepítés paraméter:**

|Paraméter|Leírás|Alapértelmezett érték|Szükséges|
|---------|-----------|-------------|--------|
|DataRoot|A helyi adatok legfelső szintű mappa elérési útja|NULL|true|

**Az Azure Data Lake Analytics-telepítéshez. paraméter:**

|Paraméter|Leírás|Alapértelmezett érték|Szükséges|
|---------|-----------|-------------|--------|
|Fiók|Itt adhatja meg, mely az Azure Data Lake Analytics-fiók által fiók nevével üzembe helyezése|NULL|true|
|ResourceGroup|Azure erőforráscsoport-neve az Azure Data Lake Analytics-fiók|NULL|true|
|SubscriptionId|Az Azure Data Lake Analytics-fiókhoz tartozó Azure-előfizetés azonosítója|NULL|true|
|Bérlő|Bérlő neve (aad-ben directory-tartománynév, megtalálja az előfizetés-kezelési oldalán az Azure Portalon)|NULL|true|
|AzureSDKPath|Azure SDK-t a függő szerelvényei keresés elérési útja|NULL|true|
|Interaktív|Interaktív mód használja a hitelesítéshez, vagy sem|false|false|
|ClientId|AAD-Alkalmazásazonosító egyikhez sem szükséges, az interaktív hitelesítéshez interaktív hitelesítés|NULL|Nincs szükséges interaktív hitelesítés|
|Secrete|Secrete vagy jelszót sem az interaktív hitelesítéshez, azt csak használja a megbízható és biztonságos környezetben|NULL|Nincs szükséges az interaktív hitelesítéshez, vagy használja SecreteFile|
|SecreteFile|Fájl mentésekor secrete/jelszó nincs az interaktív hitelesítéshez, győződjön meg arról, hogy csak olvasható legyen aktuális felhasználó|NULL|Nincs szükséges az interaktív hitelesítéshez, vagy használja Secrete|
|Tanúsítványfájl|Fájl mentésekor X.509 tanúsítvány nincs az interaktív hitelesítéshez, alapértelmezés szerint a rendszer-ügyfél hitelesítési secrete|NULL|false|
|JobPrefix|Az adatbázis üzembe helyezése előtag a U-SQL DDL-feladat|Deploy_ + DateTime.Now|false|

## <a name="next-steps"></a>További lépések

- [Az Azure Data Lake Analytics kód tesztelése](data-lake-analytics-cicd-test.md)
- [U-SQL-szkript futtatása helyi gépen](data-lake-analytics-data-lake-tools-local-run.md)
- [Használata U-SQL database-projekt U-SQL-adatbázis fejlesztéséhez](data-lake-analytics-data-lake-tools-develop-usql-database.md)
