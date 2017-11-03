---
title: "Azure Data Lake Analytics az Azure PowerShell kezelése |} Microsoft Docs"
description: "Megtudhatja, hogyan kezelheti a Data Lake Analytics-fiókok, adatforrások, feladatok és a szolgáltatáskatalógusban található elemek. "
services: data-lake-analytics
documentationcenter: 
author: matt1883
manager: jhubbard
editor: cgronlun
ms.assetid: ad14d53c-fed4-478d-ab4b-6d2e14ff2097
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/23/2017
ms.author: mahi
ms.openlocfilehash: 65bf5928428b21e98c893a9de8ca596329329411
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Az Azure Data Lake Analytics kezelése az Azure PowerShell-lel
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Útmutató: Azure Data Lake Analytics-fiókok, adatforrások, feladatok és Azure PowerShell használatával a szolgáltatáskatalógusban található elemek kezeléséhez. 

## <a name="prerequisites"></a>Előfeltételek

Data Lake Analytics-fiók létrehozásakor meg kell tudnia:

* **Előfizetés-azonosító**: az Azure előfizetés-azonosító alapján, amely a Data Lake Analytics-fiók található.
* **Erőforráscsoport**: az Azure erőforráscsoport, amely tartalmazza a Data Lake Analytics-fiók nevét.
* **A Data Lake Analytics-fiók neve**: A fiók neve csak kisbetűket és számokat tartalmazhat.
* **Alapértelmezett Data Lake Store-fiók:** minden Data Lake Analytics-fiókhoz tartozik egy alapértelmezett Data Lake Store-fiók. A fiókoknak azonos helyen kell lenniük.
* **Hely**: Data Lake Analytics-fiókja, például az "USA keleti régiója 2", vagy más helyre támogatott helyeket. Támogatott helyek láthatóak a [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/data-lake-analytics/).

A jelen oktatóanyagban szereplő PowerShell-kódrészletek ezeket a változókat használják adattárolásra

```powershell
$subId = "<SubscriptionId>"
$rg = "<ResourceGroupName>"
$adla = "<DataLakeAnalyticsAccountName>"
$adls = "<DataLakeStoreAccountName>"
$location = "<Location>"
```

## <a name="log-in"></a>Bejelentkezés

Jelentkezzen be egy előfizetési azonosító használatával.

```powershell
Login-AzureRmAccount -SubscriptionId $subId
```

Jelentkezzen be egy előfizetés nevét.

```
Login-AzureRmAccount -SubscriptionName $subname 
```

A `Login-AzureRmAccount` parancsmag mindig elkéri a hitelesítő adatokat. Meg kell adni a következő parancsmagok használatával elkerülheti a:

```powershell
# Save login session information
Save-AzureRmProfile -Path D:\profile.json  

# Load login session information
Select-AzureRmProfile -Path D:\profile.json 
```

## <a name="managing-accounts"></a>Fiókok kezelése

### <a name="create-a-data-lake-analytics-account"></a>Data Lake Analytics-fiók létrehozása

Ha még nem rendelkezik egy [erőforráscsoport](../azure-resource-manager/resource-group-overview.md#resource-groups) használatához hozzon létre egyet. 

```powershell
New-AzureRmResourceGroup -Name  $rg -Location $location
```

A naplók tárolásához minden Data Lake Analytics-fiók esetében szükség van egy alapértelmezett Data Lake Store-fiókra. Egy meglévő fiókot újra, vagy hozzon létre egy fiókot. 

```powershell
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
```

Ha az erőforráscsoport és a Data Lake Store-fiók már elérhető, hozzon létre egy Data Lake Analytics-fiókot.

```powershell
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

### <a name="get-information-about-an-account"></a>Egy fiók adatainak beolvasása

Ezzel a fiókkal kapcsolatos adatokat.

```powershell
Get-AdlAnalyticsAccount -Name $adla
```

Ellenőrizze, létezik-e egy adott Data Lake Analytics-fiókot. A parancsmag ad vissza, vagy `True` vagy `False`.

```powershell
Test-AdlAnalyticsAccount -Name $adla
```

Ellenőrizze, létezik-e egy adott Data Lake Store-fiókot. A parancsmag ad vissza, vagy `True` vagy `False`.

```powershell
Test-AdlStoreAccount -Name $adls
```

### <a name="listing-accounts"></a>Fiókok listázása

Lista Data Lake Analytics-fiókok az aktuális előfizetésben.

```powershell
Get-AdlAnalyticsAccount
```

Lista Data Lake Analytics-fiókok egy adott erőforráscsoportban.

```powershell
Get-AdlAnalyticsAccount -ResourceGroupName $rg
```

## <a name="managing-firewall-rules"></a>Tűzfal-szabályok kezelése

Tűzfalszabályok listája.

```powershell
Get-AdlAnalyticsFirewallRule -Account $adla
```

Fel egy tűzfalszabályt.

```powershell
$ruleName = "Allow access from on-prem server"
$startIpAddress = "<start IP address>"
$endIpAddress = "<end IP address>"

Add-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

Egy tűzfalszabály módosítása.

```powershell
Set-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

Egy tűzfalszabály eltávolítása.

```powershell
Remove-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName
```



Azure IP-címek engedélyezése.

```powershell
Set-AdlAnalyticsAccount -Name $adla -AllowAzureIpState Enabled
```

```powershell
Set-AdlAnalyticsAccount -Name $adla -FirewallState Enabled
Set-AdlAnalyticsAccount -Name $adla -FirewallState Disabled
```

## <a name="managing-data-sources"></a>Az adatforrások kezelése
Az Azure Data Lake Analytics jelenleg a következő adatforrásokat támogatja:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage](../storage/common/storage-introduction.md)

Az Analytics-fiók létrehozásakor ki kell jelölnie egy Data Lake Store-fiókot az alapértelmezett adatforrás lehet. Az alapértelmezett Data Lake Store-fiók feladat metaadatok és a feladat a vizsgálati naplók tárolására szolgál. Data Lake Analytics-fiók létrehozása után hozzáadhat további Data Lake Store-fiókok és/vagy a Storage-fiókok. 

### <a name="find-the-default-data-lake-store-account"></a>Az alapértelmezett Data Lake Store-fiók található

```powershell
$adla_acct = Get-AdlAnalyticsAccount -Name $adla
$dataLakeStoreName = $adla_acct.DefaultDataLakeAccount
```

Az alapértelmezett Data Lake Store-fiók található adatforrások által listájának szűrése révén a `IsDefault` tulajdonság:

```powershell
Get-AdlAnalyticsDataSource -Account $adla  | ? { $_.IsDefault } 
```

### <a name="add-a-data-source"></a>Egy adatforrás hozzáadása

```powershell

# Add an additional Storage (Blob) account.
$AzureStorageAccountName = "<AzureStorageAccountName>"
$AzureStorageAccountKey = "<AzureStorageAccountKey>"
Add-AdlAnalyticsDataSource -Account $adla -Blob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey

# Add an additional Data Lake Store account.
$AzureDataLakeStoreName = "<AzureDataLakeStoreAccountName"
Add-AdlAnalyticsDataSource -Account $adla -DataLakeStore $AzureDataLakeStoreName 
```

### <a name="list-data-sources"></a>Az adatforrások listája

```powershell
# List all the data sources
Get-AdlAnalyticsDataSource -Name $adla

# List attached Data Lake Store accounts
Get-AdlAnalyticsDataSource -Name $adla | where -Property Type -EQ "DataLakeStore"

# List attached Storage accounts
Get-AdlAnalyticsDataSource -Name $adla | where -Property Type -EQ "Blob"
```

## <a name="submit-u-sql-jobs"></a>U-SQL-feladatok küldése

### <a name="submit-a-string-as-a-u-sql-script"></a>Küldje el egy karakterlánc, egy U-SQL-parancsfájl

```powershell
$script = @"
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
"@

$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 

Submit-AdlJob -AccountName $adla -Script $script -Name "Demo"
```


### <a name="submit-a-file-as-a-u-sql-script"></a>Küldje el a fájlt egy U-SQL-parancsfájl

```powershell
$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 
Submit-AdlJob -AccountName $adla –ScriptPath $scriptpath -Name "Demo"
```

## <a name="list-jobs-in-an-account"></a>Egy fiók feladatok listája

### <a name="list-all-the-jobs-in-the-account"></a>Sorolja fel a fiókban lévő összes feladatot. 

A kimenet tartalmazza az aktuálisan futó és a nemrégiben befejezett feladatokat is.

```powershell
Get-AdlJob -Account $adla
```


### <a name="list-a-specific-number-of-jobs"></a>Egy bizonyos számú feladatok listázása

A feladatok van rendezve, az alapértelmezés szerint idő nyújt. Ezért a legutóbb elküldött feladatok elsőként jelennek meg. Alapértelmezés szerint a ADLA fiók feladatok emlékszik 180 napig tart, de a Ge-AdlJob parancsmag alapértelmezés szerint csak az első 500 adja vissza. Használja az - felső paraméter egy bizonyos számú feladatok listázásához.

```powershell
$jobs = Get-AdlJob -Account $adla -Top 10
```


### <a name="list-jobs-based-on-the-value-of-job-property"></a>Lista feladatok feladat tulajdonság értéke alapján

Használja a `-State` paraméter. Ezek bármelyike kombinálhatja:

* `Accepted`
* `Compiling`
* `Ended`
* `New`
* `Paused`
* `Queued`
* `Running`
* `Scheduling`
* `Start`

```powershell
# List the running jobs
Get-AdlJob -Account $adla -State Running

# List the jobs that have completed
Get-AdlJob -Account $adla -State Ended

# List the jobs that have not started yet
Get-AdlJob -Account $adla -State Accepted,Compiling,New,Paused,Scheduling,Start
```

Használja a `-Result` paraméter észleli, hogy befejeződött a feladat sikeresen befejeződött-e. Ezekkel az értékekkel rendelkezik:

* Megszakítva
* Nem sikerült
* None
* Sikeres

``` powershell
# List Successful jobs.
Get-AdlJob -Account $adla -State Ended -Result Succeeded

# List Failed jobs.
Get-AdlJob -Account $adla -State Ended -Result Failed
```


A `-Submitter` paraméter segít azonosítani, ki egy feladat elküldése megtörtént.

```powershell
Get-AdlJob -Account $adla -Submitter "joe@contoso.com"
```

A `-SubmittedAfter` hasznos egy időtartományt a szűrés.


```powershell
# List  jobs submitted in the last day.
$d = [DateTime]::Now.AddDays(-1)
Get-AdlJob -Account $adla -SubmittedAfter $d

# List  jobs submitted in the last seven day.
$d = [DateTime]::Now.AddDays(-7)
Get-AdlJob -Account $adla -SubmittedAfter $d
```

### <a name="analyzing-job-history"></a>Feladatelőzmények elemzése

Azure PowerShell használatával történő elemzéséhez, amelyek futtatták a Data Lake analytics-feladatok előzményeinek a hatékony technika. Hozhat betekintést nyerhet használati és költségek. Ha megnézi többet is megtudhat a [feladat előzmények elemzési minta tárház](https://github.com/Azure-Samples/data-lake-analytics-powershell-job-history-analysis)  

## <a name="get-information-about-pipelines-and-recurrences"></a>Adatcsatornák és ismétlődések adatainak beolvasása

Használja a `Get-AdlJobPipeline` csővezeték-információt parancsmag korábban küldött feladatok.

```powershell
$pipelines = Get-AdlJobPipeline -Account $adla

$pipeline = Get-AdlJobPipeline -Account $adla -PipelineId "<pipeline ID>"
```

Használja a `Get-AdlJobRecurrence` parancsmaggal olvassa ismétlődési korábban küldött feladatok.

```powershell
$recurrences = Get-AdlJobRecurrence -Account $adla

$recurrence = Get-AdlJobRecurrence -Account $adla -RecurrenceId "<recurrence ID>"
```

## <a name="get-information-about-a-job"></a>A feladat adatainak beolvasása

### <a name="get-job-status"></a>Feladat állapotának beolvasása

Kérje le egy adott feladat állapotát.

```powershell
Get-AdlJob -AccountName $adla -JobId $job.JobId
```

### <a name="examine-the-job-outputs"></a>Vizsgálja meg a feladat kimenetének létrehozása

Miután a feladat befejeződött, ellenőrizze, hogy léteznek-e a kimeneti fájl a mappában lévő fájlok listáját.

```powershell
Get-AdlStoreChildItem -Account $adls -Path "/"
```

## <a name="manage-running-jobs"></a>Futó feladatok kezelése

### <a name="cancel-a-job"></a>Feladatok megszakítása

```powershell
Stop-AdlJob -Account $adls -JobID $jobID
```

### <a name="wait-for-a-job-to-finish"></a>Várjon, amíg a feladat befejeződésére

Ismétlődő helyett `Get-AdlAnalyticsJob` amíg a feladat befejeződik, használhatja a `Wait-AdlJob` parancsmag használatával, vagy várja meg a feladat befejezéséhez.

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

## <a name="manage-compute-policies"></a>Számítási házirendjeinek kezelése

### <a name="list-existing-compute-policies"></a>A meglévő számítási házirendek felsorolása

A `Get-AdlAnalyticsComputePolicy` parancsmag beolvassa a Data Lake Analytics-fiók számítási házirendekkel kapcsolatos információ.

```powershell
$policies = Get-AdlAnalyticsComputePolicy -Account $adla
```

### <a name="create-a-compute-policy"></a>Számítási házirend létrehozása

A `New-AdlAnalyticsComputePolicy` parancsmag létrehoz egy új számítási házirendet a Data Lake Analytics-fiók. Ebben a példában a rendelkezésre álló maximális ausztráliai állítja be a megadott felhasználó 50 és 250 minimális feladat prioritása.

```powershell
$userObjectId = (Get-AzureRmAdUser -SearchString "garymcdaniel@contoso.com").Id

New-AdlAnalyticsComputePolicy -Account $adla -Name "GaryMcDaniel" -ObjectId $objectId -ObjectType User -MaxDegreeOfParallelismPerJob 50 -MinPriorityPerJob 250
```

## <a name="check-for-the-existence-of-a-file"></a>Ellenőrizze, hogy létezik-e a fájl.

```powershell
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

## <a name="uploading-and-downloading"></a>Feltöltés és letöltés

Fájl feltöltése.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\data.tsv" -Destination "/data_copy.csv" 
```

Töltsön fel egy teljes mappát rekurzív módon.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\myData\" -Destination "/myData/" -Recurse
```

Töltse le a fájlt.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/data.csv" -Destination "c:\data.csv"
```

Töltse le egy teljes mappát rekurzív módon.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/" -Destination "c:\myData\" -Recurse
```

> [!NOTE]
> Ha a feltöltés és letöltés választható folyamat megszakad, esetleg az folytatásához újra a parancsmagot a ``-Resume`` jelzőt.

## <a name="manage-catalog-items"></a>A szolgáltatáskatalógusban található elemek kezelése

A U-SQL-katalógusról data és kód felépítését, így azok megoszthatók U-SQL-parancsfájlok szolgál. A katalógus lehetővé teszi, hogy a lehető legjobb teljesítmény szolgálja Azure Data Lake-adatokkal. További információk: [Use U-SQL catalog](data-lake-analytics-use-u-sql-catalog.md) (U-SQL-katalógus használata).

### <a name="list-items-in-the-u-sql-catalog"></a>A U-SQL catalog listaelemeit.

```powershell
# List U-SQL databases
Get-AdlCatalogItem -Account $adla -ItemType Database 

# List tables within a database
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database"

# List tables within a schema.
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database.schema"
```

A ADLA fiók összes adatbázisát a szerelvények listázása.

```powershell
$dbs = Get-AdlCatalogItem -Account $adla -ItemType Database

foreach ($db in $dbs)
{
    $asms = Get-AdlCatalogItem -Account $adla -ItemType Assembly -Path $db.Name

    foreach ($asm in $asms)
    {
        $asmname = "[" + $db.Name + "].[" + $asm.Name + "]"
        Write-Host $asmname
    }
}
```

### <a name="get-details-about-a-catalog-item"></a>Részletes információkat szolgáltatva katalóguselemek

```powershell
# Get details of a table
Get-AdlCatalogItem  -Account $adla -ItemType Table -Path "master.dbo.mytable"

# Test existence of a U-SQL database.
Test-AdlCatalogItem  -Account $adla -ItemType Database -Path "master"
```

### <a name="create-credentials-in-a-catalog"></a>Hitelesítő adatok létrehozása a katalógusban

U-SQL-adatbázis hozzon létre egy Azure-ban üzemeltetett adatbázis hitelesítő objektumot. U-SQL hitelesítő adatok jelenleg csak az ilyen típusú elemet, amely a PowerShell segítségével is létrehozhat.

```powershell
$dbName = "master"
$credentialName = "ContosoDbCreds"
$dbUri = "https://contoso.database.windows.net:8080"

New-AdlCatalogCredential -AccountName $adla `
          -DatabaseName $db `
          -CredentialName $credentialName `
          -Credential (Get-Credential) `
          -Uri $dbUri
```

### <a name="get-basic-information-about-an-adla-account"></a>ADLA fiók alapszintű adatainak beolvasása

Megadott fióknév, az alábbi kód megkeresi a fiókkal kapcsolatos alapvető információk

```
$adla_acct = Get-AdlAnalyticsAccount -Name "saveenrdemoadla"
$adla_name = $adla_acct.Name
$adla_subid = $adla_acct.Id.Split("/")[2]
$adla_sub = Get-AzureRmSubscription -SubscriptionId $adla_subid
$adla_subname = $adla_sub.Name
$adla_defadls_datasource = Get-AdlAnalyticsDataSource -Account $adla_name  | ? { $_.IsDefault } 
$adla_defadlsname = $adla_defadls_datasource.Name

Write-Host "ADLA Account Name" $adla_name
Write-Host "Subscription Id" $adla_subid
Write-Host "Subscription Name" $adla_subname
Write-Host "Defautl ADLS Store" $adla_defadlsname
Write-Host 

Write-Host '$subname' " = ""$adla_subname"" "
Write-Host '$subid' " = ""$adla_subid"" "
Write-Host '$adla' " = ""$adla_name"" "
Write-Host '$adls' " = ""$adla_defadlsname"" "
```

## <a name="working-with-azure"></a>Azure használata

### <a name="get-details-of-azurerm-errors"></a>Részletes AzureRm hibák

```powershell
Resolve-AzureRmError -Last
```

### <a name="verify-if-you-are-running-as-an-administrator"></a>Győződjön meg arról, ha rendszergazdaként futtatja

```powershell
function Test-Administrator  
{  
    $user = [Security.Principal.WindowsIdentity]::GetCurrent();
    $p = New-Object Security.Principal.WindowsPrincipal $user
    $p.IsInRole([Security.Principal.WindowsBuiltinRole]::Administrator)  
}
```

### <a name="find-a-tenantid"></a>A TenantID keresése

Előfizetés neve:

```powershell
function Get-TenantIdFromSubcriptionName( [string] $subname )
{
    $sub = (Get-AzureRmSubscription -SubscriptionName $subname)
    $sub.TenantId
}

Get-TenantIdFromSubcriptionName "ADLTrainingMS"
```

Az előfizetés-azonosító:

```powershell
function Get-TenantIdFromSubcriptionId( [string] $subid )
{
    $sub = (Get-AzureRmSubscription -SubscriptionId $subid)
    $sub.TenantId
}

$subid = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
Get-TenantIdFromSubcriptionId $subid
```

Például a "contoso.com" tartomány címről


```powershell
function Get-TenantIdFromDomain( $domain )
{
    $url = "https://login.windows.net/" + $domain + "/.well-known/openid-configuration"
    return (Invoke-WebRequest $url|ConvertFrom-Json).token_endpoint.Split('/')[3]
}

$domain = "contoso.com"
Get-TenantIdFromDomain $domain
```

### <a name="list-all-your-subscriptions-and-tenant-ids"></a>Az előfizetések listán, és a bérlői azonosító

```powershell
$subs = Get-AzureRmSubscription
foreach ($sub in $subs)
{
    Write-Host $sub.Name "("  $sub.Id ")"
    Write-Host "`tTenant Id" $sub.TenantId
}
```

## <a name="create-a-data-lake-analytics-account-using-a-template"></a>Egy sablon használatával a Data Lake Analytics-fiók létrehozása

Egy Azure erőforráscsoport-sablon a következő PowerShell-parancsfájl használatával is használhatja:

```powershell
$subId = "<Your Azure Subscription ID>"

$rg = "<New Azure Resource Group Name>"
$location = "<Location (such as East US 2)>"
$adls = "<New Data Lake Store Account Name>"
$adla = "<New Data Lake Analytics Account Name>"

$deploymentName = "MyDataLakeAnalyticsDeployment"
$armTemplateFile = "<LocalFolderPath>\azuredeploy.json"  # update the JSON template path 

# Log in to Azure
Login-AzureRmAccount -SubscriptionId $subId

# Create the resource group
New-AzureRmResourceGroup -Name $rg -Location $location

# Create the Data Lake Analytics account with the default Data Lake Store account.
$parameters = @{"adlAnalyticsName"=$adla; "adlStoreName"=$adls}
New-AzureRmResourceGroupDeployment -Name $deploymentName -ResourceGroupName $rg -TemplateFile $armTemplateFile -TemplateParameterObject $parameters 
```

További információkért lásd: [Azure Resource Manager-sablon az alkalmazás központi telepítését](../azure-resource-manager/resource-group-template-deploy.md) és [Azure Resource Manager-sablonok készítése](../azure-resource-manager/resource-group-authoring-templates.md).

**Példa sablon**

Mentse a következő szöveget a `.json` fájlt, és az előző PowerShell-parancsfájl segítségével használhatja a sablont. 

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adlAnalyticsName": {
      "type": "string",
      "metadata": {
        "description": "The name of the Data Lake Analytics account to create."
      }
    },
    "adlStoreName": {
      "type": "string",
      "metadata": {
        "description": "The name of the Data Lake Store account to create."
      }
    }
  },
  "resources": [
    {
      "name": "[parameters('adlStoreName')]",
      "type": "Microsoft.DataLakeStore/accounts",
      "location": "East US 2",
      "apiVersion": "2015-10-01-preview",
      "dependsOn": [ ],
      "tags": { }
    },
    {
      "name": "[parameters('adlAnalyticsName')]",
      "type": "Microsoft.DataLakeAnalytics/accounts",
      "location": "East US 2",
      "apiVersion": "2015-10-01-preview",
      "dependsOn": [ "[concat('Microsoft.DataLakeStore/accounts/',parameters('adlStoreName'))]" ],
      "tags": { },
      "properties": {
        "defaultDataLakeStoreAccount": "[parameters('adlStoreName')]",
        "dataLakeStoreAccounts": [
          { "name": "[parameters('adlStoreName')]" }
        ]
      }
    }
  ],
  "outputs": {
    "adlAnalyticsAccount": {
      "type": "object",
      "value": "[reference(resourceId('Microsoft.DataLakeAnalytics/accounts',parameters('adlAnalyticsName')))]"
    },
    "adlStoreAccount": {
      "type": "object",
      "value": "[reference(resourceId('Microsoft.DataLakeStore/accounts',parameters('adlStoreName')))]"
    }
  }
}
```

## <a name="next-steps"></a>Következő lépések
* [A Microsoft Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* A Data Lake Analytics használatának első lépései [Azure-portálon](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [CLI 2.0](data-lake-analytics-get-started-cli2.md)
* Azure Data Lake Analytics használatának kezelése [Azure-portálon](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [parancssori felület](data-lake-analytics-manage-use-cli.md) 
