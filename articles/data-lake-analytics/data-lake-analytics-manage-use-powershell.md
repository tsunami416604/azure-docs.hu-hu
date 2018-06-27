---
title: Az Azure Data Lake Analytics kezelése az Azure PowerShell-lel
description: Ez a cikk ismerteti az Azure PowerShell használata a Data Lake Analytics-fiókok, adatforrások, felhasználók és feladatok kezelésére.
services: data-lake-analytics
ms.service: data-lake-analytics
author: matt1883
ms.author: mahi
manager: kfile
editor: jasonwhowell
ms.assetid: ad14d53c-fed4-478d-ab4b-6d2e14ff2097
ms.topic: conceptual
ms.date: 06/02/2018
ms.openlocfilehash: 560f36dc64480fd6aceaa50226b191ee40d2486f
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2018
ms.locfileid: "36959848"
---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Az Azure Data Lake Analytics kezelése az Azure PowerShell-lel
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Ez a cikk ismerteti az Azure Data Lake Analytics-fiókok, az adatforrások, a felhasználók és a feladatok kezelése az Azure PowerShell használatával.

## <a name="prerequisites"></a>Előfeltételek

PowerShell használata a Data Lake Analytics, gyűjtse össze az alábbi adatokra: 

* **Előfizetés-azonosító**: az azonosító az Azure-előfizetés, amely tartalmazza a Data Lake Analytics-fiók.
* **Erőforráscsoport**: az Azure erőforráscsoport, amely tartalmazza a Data Lake Analytics-fiók nevét.
* **A Data Lake Analytics-fiók neve**: a Data Lake Analytics-fiók nevét.
* **Alapértelmezett Data Lake Store-fiók neve**: minden Data Lake Analytics-fiókhoz tartozik egy alapértelmezett Data Lake Store fiók.
* **Hely**: Data Lake Analytics-fiókja, például az "USA keleti régiója 2", vagy más helyre támogatott helyeket.

A jelen oktatóanyagban szereplő PowerShell-kódrészletek ezeket a változókat használják adattárolásra

```powershell
$subId = "<SubscriptionId>"
$rg = "<ResourceGroupName>"
$adla = "<DataLakeAnalyticsAccountName>"
$adls = "<DataLakeStoreAccountName>"
$location = "<Location>"
```

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

### <a name="log-in-using-interactive-user-authentication"></a>Jelentkezzen be interaktív felhasználói hitelesítéssel

Jelentkezzen be egy előfizetés-azonosító, vagy a előfizetés neve

```powershell
# Using subscription id
Connect-AzureRmAccount -SubscriptionId $subId

# Using subscription name
Connect-AzureRmAccount -SubscriptionName $subname 
```

## <a name="saving-authenticaiton-context"></a>Hitelesítéskonfiguráló környezet mentése

A `Connect-AzureRmAccount` parancsmag mindig elkéri a hitelesítő adatokat. Meg kell adni a következő parancsmagok használatával elkerülheti a:

```powershell
# Save login session information
Save-AzureRmProfile -Path D:\profile.json  

# Load login session information
Select-AzureRmProfile -Path D:\profile.json 
```

### <a name="log-in-using-a-service-principal-identity-spi"></a>Jelentkezzen be a szolgáltatás egyszerű Identity (SPI) használatával

```powershell
$tenantid = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"  
$spi_appname = "appname" 
$spi_appid = "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX" 
$spi_secret = "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX" 

$pscredential = New-Object System.Management.Automation.PSCredential ($spi_appid, (ConvertTo-SecureString $spi_secret -AsPlainText -Force))
Login-AzureRmAccount -ServicePrincipal -TenantId $tenantid -Credential $pscredential -Subscription $subid
```

## <a name="manage-accounts"></a>Fiókok kezelése


### <a name="list-accounts"></a>Fiókok listázása

```powershell
# List Data Lake Analytics accounts within the current subscription.
Get-AdlAnalyticsAccount

# List Data Lake Analytics accounts within a specific resource group.
Get-AdlAnalyticsAccount -ResourceGroupName $rg
```

### <a name="create-an-account"></a>Fiók létrehozása

A naplók tárolásához minden Data Lake Analytics-fiók esetében szükség van egy alapértelmezett Data Lake Store-fiókra. Egy meglévő fiókot újra, vagy hozzon létre egy fiókot. 

```powershell
# Create a data lake store if needed, or you can re-use an existing one
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

### <a name="get-account-information"></a>Fiók adatainak lekérése

Ezzel a fiókkal kapcsolatos adatokat.

```powershell
Get-AdlAnalyticsAccount -Name $adla
```

### <a name="check-if-an-account-exists"></a>Ellenőrizze, hogy a fiók létezik-e

```powershell
Test-AdlAnalyticsAccount -Name $adla
```

## <a name="manage-data-sources"></a>Adatforrások kezelése
Az Azure Data Lake Analytics jelenleg a következő adatforrásokat támogatja:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage](../storage/common/storage-introduction.md)

Minden Data Lake Analytics-fiókja alapértelmezett Data Lake Store-fiók rendelkezik. Az alapértelmezett Data Lake Store-fiók feladat metaadatok és a feladat a vizsgálati naplók tárolására szolgál. 

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

### <a name="submit-a-string-as-a-u-sql-job"></a>Küldje el a karakterlánc egy U-SQL-feladat

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

### <a name="submit-a-file-as-a-u-sql-job"></a>Küldje el a fájlt egy U-SQL-feladat

```powershell
$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 
Submit-AdlJob -AccountName $adla –ScriptPath $scriptpath -Name "Demo"
```

### <a name="list-jobs"></a>Lista feladatok

A kimenet tartalmazza az aktuálisan futó és a nemrégiben befejezett feladatokat is.

```powershell
Get-AdlJob -Account $adla
```

### <a name="list-the-top-n-jobs"></a>A legfontosabb N feladatok listázásához

A feladatok van rendezve, az alapértelmezés szerint idő nyújt. Ezért a legutóbb elküldött feladatok elsőként jelennek meg. Alapértelmezés szerint a ADLA fiók feladatok emlékszik 180 napig tart, de a Get-AdlJob parancsmag alapértelmezés szerint csak az első 500 adja vissza. Használja az - felső paraméter egy bizonyos számú feladatok listázásához.

```powershell
$jobs = Get-AdlJob -Account $adla -Top 10
```

### <a name="list-jobs-by-job-state"></a>Lista feladatok feladat állapota alapján

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

### <a name="list-jobs-by-job-result"></a>Lista feladatok feladat eredménye

Használja a `-Result` paraméter észleli, hogy befejeződött a feladat sikeresen befejeződött-e. Ezekkel az értékekkel rendelkezik:

* Megszakítva
* Meghiúsult
* None
* Sikeres

``` powershell
# List Successful jobs.
Get-AdlJob -Account $adla -State Ended -Result Succeeded

# List Failed jobs.
Get-AdlJob -Account $adla -State Ended -Result Failed
```

### <a name="list-jobs-by-job-submitter"></a>Feladat küldő által lista feladatok

A `-Submitter` paraméter segít azonosítani, ki egy feladat elküldése megtörtént.

```powershell
Get-AdlJob -Account $adla -Submitter "joe@contoso.com"
```

### <a name="list-jobs-by-submission-time"></a>Lista feladatok által küldésének ideje

A `-SubmittedAfter` hasznos egy időtartományt a szűrés.


```powershell
# List  jobs submitted in the last day.
$d = [DateTime]::Now.AddDays(-1)
Get-AdlJob -Account $adla -SubmittedAfter $d

# List  jobs submitted in the last seven day.
$d = [DateTime]::Now.AddDays(-7)
Get-AdlJob -Account $adla -SubmittedAfter $d
```

### <a name="get-job-status"></a>Feladat állapotának beolvasása

Kérje le egy adott feladat állapotát.

```powershell
Get-AdlJob -AccountName $adla -JobId $job.JobId
```


### <a name="cancel-a-job"></a>Feladatok megszakítása

```powershell
Stop-AdlJob -Account $adla -JobID $jobID
```

### <a name="wait-for-a-job-to-finish"></a>Várjon, amíg a feladat befejeződésére

Ismétlődő helyett `Get-AdlAnalyticsJob` amíg a feladat befejeződik, használhatja a `Wait-AdlJob` parancsmag használatával, vagy várja meg a feladat befejezéséhez.

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

## <a name="analyzing-job-history"></a>Feladatelőzmények elemzése

Azure PowerShell használatával történő elemzéséhez, amelyek futtatták a Data Lake analytics-feladatok előzményeinek a hatékony technika. Hozhat betekintést nyerhet használati és költségek. Ha megnézi többet is megtudhat a [feladat előzmények elemzési minta tárház](https://github.com/Azure-Samples/data-lake-analytics-powershell-job-history-analysis)  

## <a name="list-job-pipelines-and-recurrences"></a>lista feladat folyamatok és ismétlődések

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
## <a name="manage-files"></a>Fájlok kezelése

### <a name="check-for-the-existence-of-a-file"></a>Ellenőrizze, hogy létezik-e a fájl.

```powershell
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

### <a name="uploading-and-downloading"></a>Feltöltés és letöltés

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

## <a name="manage-the-u-sql-catalog"></a>A U-SQL-katalógus kezelése

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

### <a name="list-all-the-assemblies-the-u-sql-catalog"></a>A szerelvények a U-SQL-katalógus felsorolása

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

### <a name="store-credentials-in-the-catalog"></a>A katalógus adattárolóhoz használandó hitelesítő adatok

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

## <a name="manage-firewall-rules"></a>Tűzfal-szabályok kezelése

### <a name="list-firewall-rules"></a>Tűzfalszabályok listája

```powershell
Get-AdlAnalyticsFirewallRule -Account $adla
```

### <a name="add-a-firewall-rule"></a>A tűzfalszabályok hozzáadása

```powershell
$ruleName = "Allow access from on-prem server"
$startIpAddress = "<start IP address>"
$endIpAddress = "<end IP address>"

Add-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

### <a name="modify-a-firewall-rule"></a>Egy tűzfalszabály módosítása

```powershell
Set-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

### <a name="remove-a-firewall-rule"></a>Egy tűzfalszabály törlése

```powershell
Remove-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName
```

### <a name="allow-azure-ip-addresses"></a>Azure IP-címek engedélyezése

```powershell
Set-AdlAnalyticsAccount -Name $adla -AllowAzureIpState Enabled
```

```powershell
Set-AdlAnalyticsAccount -Name $adla -FirewallState Enabled
Set-AdlAnalyticsAccount -Name $adla -FirewallState Disabled
```


## <a name="working-with-azure"></a>Azure használata

### <a name="get-details-of-azurerm-errors"></a>Részletes AzureRm hibák

```powershell
Resolve-AzureRmError -Last
```

### <a name="verify-if-you-are-running-as-an-administrator-on-your-windows-machine"></a>Győződjön meg arról, ha futtatja rendszergazdaként a Windows-számítógépen

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

Egy Azure erőforráscsoport-sablont, az alábbi minta használatával is használhatja: [sablon használatával a Data Lake Analytics-fiók létrehozása](https://github.com/Azure-Samples/data-lake-analytics-create-account-with-arm-template)

## <a name="next-steps"></a>További lépések
* [A Microsoft Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* A Data Lake Analytics használatának első lépései [Azure-portálon](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [CLI 2.0](data-lake-analytics-get-started-cli2.md)
* Azure Data Lake Analytics használatának kezelése [Azure-portálon](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [parancssori felület](data-lake-analytics-manage-use-cli.md) 
