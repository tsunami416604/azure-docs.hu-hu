---
title: Az Azure Data Lake Analytics kezelése az Azure PowerShell-lel
description: Ez a cikk ismerteti a Data Lake Analytics-fiókok, adatforrások, felhasználók & feladatok kezelése az Azure PowerShell használatával.
services: data-lake-analytics
ms.service: data-lake-analytics
author: matt1883
ms.author: mahi
ms.reviewer: jasonwhowell
ms.assetid: ad14d53c-fed4-478d-ab4b-6d2e14ff2097
ms.topic: conceptual
ms.date: 06/29/2018
ms.openlocfilehash: 5bd8763234aa02d68b6e86b7259fcf10b4ef4ac5
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51684282"
---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Az Azure Data Lake Analytics kezelése az Azure PowerShell-lel
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Ez a cikk ismerteti az Azure Data Lake Analytics-fiókok, adatforrások, felhasználók és feladatok kezelése az Azure PowerShell használatával.

## <a name="prerequisites"></a>Előfeltételek

A PowerShell használata a Data Lake Analytics, gyűjtse össze a következő információt: 

* **Előfizetés-azonosító**: a Data Lake Analytics-fiókot tartalmazó Azure-előfizetés azonosítója.
* **Erőforráscsoport**: az Azure-erőforráscsoportot, amely tartalmazza a Data Lake Analytics-fiók nevére.
* **A Data Lake Analytics-fiók neve**: a Data Lake Analytics-fiók nevét.
* **Alapértelmezett Data Lake Store-fiók neve**: minden Data Lake Analytics-fiók tartozik egy alapértelmezett Data Lake Store-fiók.
* **Hely**: A hely a Data Lake Analytics-fiók, például az "USA keleti RÉGIÓJA 2" vagy egyéb támogatott helyeket.

A jelen oktatóanyagban szereplő PowerShell-kódrészletek ezeket a változókat használják adattárolásra

```powershell
$subId = "<SubscriptionId>"
$rg = "<ResourceGroupName>"
$adla = "<DataLakeAnalyticsAccountName>"
$adls = "<DataLakeStoreAccountName>"
$location = "<Location>"
```

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

### <a name="log-in-using-interactive-user-authentication"></a>Jelentkezzen be, interaktív felhasználói hitelesítéssel

Jelentkezzen be egy előfizetés-azonosító vagy az előfizetés neve

```powershell
# Using subscription id
Connect-AzureRmAccount -SubscriptionId $subId

# Using subscription name
Connect-AzureRmAccount -SubscriptionName $subname 
```

## <a name="saving-authentication-context"></a>Hitelesítési környezetet mentése

A `Connect-AzureRmAccount` parancsmag minden esetben kéri a hitelesítő adatokat. Elkerülheti az alábbi parancsmagokkal erre kéri:

```powershell
# Save login session information
Save-AzureRmProfile -Path D:\profile.json  

# Load login session information
Select-AzureRmProfile -Path D:\profile.json 
```

### <a name="log-in-using-a-service-principal-identity-spi"></a>Jelentkezzen be a szolgáltatás egyszerű identitás (ÜTI)

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

A naplók tárolásához minden Data Lake Analytics-fiók esetében szükség van egy alapértelmezett Data Lake Store-fiókra. Újból egy meglévő fiókot, vagy hozzon létre egy fiókot. 

```powershell
# Create a data lake store if needed, or you can re-use an existing one
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

### <a name="get-account-information"></a>Fiók adatainak beolvasása

Egy fiók részleteinek beolvasása.

```powershell
Get-AdlAnalyticsAccount -Name $adla
```

### <a name="check-if-an-account-exists"></a>Ellenőrizze, hogy létezik-e fiók

```powershell
Test-AdlAnalyticsAccount -Name $adla
```

## <a name="manage-data-sources"></a>Adatforrások kezelése
Az Azure Data Lake Analytics jelenleg a következő adatforrások használatát támogatja:

* [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage](../storage/common/storage-introduction.md)

Minden Data Lake Analytics-fiók tartozik egy alapértelmezett Data Lake Store-fiók. Az alapértelmezett Data Lake Store-fiók feladat metaadatok és a feladat a vizsgálati naplók tárolására szolgál. 

### <a name="find-the-default-data-lake-store-account"></a>Keresse meg az alapértelmezett Data Lake Store-fiók

```powershell
$adla_acct = Get-AdlAnalyticsAccount -Name $adla
$dataLakeStoreName = $adla_acct.DefaultDataLakeAccount
```

Az alapértelmezett Data Lake Store-fiók találhatja meg a tanúsítványlista adatforrások által a `IsDefault` tulajdonság:

```powershell
Get-AdlAnalyticsDataSource -Account $adla  | ? { $_.IsDefault } 
```

### <a name="add-a-data-source"></a>Adatforrás hozzáadása

```powershell

# Add an additional Storage (Blob) account.
$AzureStorageAccountName = "<AzureStorageAccountName>"
$AzureStorageAccountKey = "<AzureStorageAccountKey>"
Add-AdlAnalyticsDataSource -Account $adla -Blob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey

# Add an additional Data Lake Store account.
$AzureDataLakeStoreName = "<AzureDataLakeStoreAccountName"
Add-AdlAnalyticsDataSource -Account $adla -DataLakeStore $AzureDataLakeStoreName 
```

### <a name="list-data-sources"></a>Adatforrások listája

```powershell
# List all the data sources
Get-AdlAnalyticsDataSource -Account $adla

# List attached Data Lake Store accounts
Get-AdlAnalyticsDataSource -Account $adla | where -Property Type -EQ "DataLakeStore"

# List attached Storage accounts
Get-AdlAnalyticsDataSource -Account $adla | where -Property Type -EQ "Blob"
```

## <a name="submit-u-sql-jobs"></a>U-SQL-feladatok küldése

### <a name="submit-a-string-as-a-u-sql-job"></a>Küldje el egy karakterláncot egy U-SQL-feladat

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

### <a name="submit-a-file-as-a-u-sql-job"></a>Küldje el egy fájlt egy U-SQL-feladat

```powershell
$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 
Submit-AdlJob -AccountName $adla –ScriptPath $scriptpath -Name "Demo"
```

### <a name="list-jobs"></a>Feladatok listája

A kimenet tartalmazza az aktuálisan futó és a nemrégiben befejezett feladatokat is.

```powershell
Get-AdlJob -Account $adla
```

### <a name="list-the-top-n-jobs"></a>A felső N-feladatok listázása

A feladatok alapján vannak rendezve, akkor alapértelmezés szerint küldési ideje. Ezért a legutóbb elküldött feladatok elsőként jelennek meg. Alapértelmezés szerint az ADLA fiók megjegyzi a feladatok 180 napig, de a Get-AdlJob parancsmagot alapértelmezés szerint csak az első 500 adja vissza. Használja az - listázhatja egy adott feladatok száma a felső paraméter.

```powershell
$jobs = Get-AdlJob -Account $adla -Top 10
```

### <a name="list-jobs-by-job-state"></a>Feladatállapotra listázhatók a feladatok

Használatával a `-State` paraméter. Kombinálhatja ezeket az értékeket:

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

### <a name="list-jobs-by-job-result"></a>Feladat eredménye listázhatók a feladatok

Használja a `-Result` észleli, hogy befejeződött a feladat sikeresen befejeződött-e a paraméter. Ezekkel az értékekkel rendelkezik:

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

### <a name="list-jobs-by-job-submitter"></a>A feladat küldője listázhatók a feladatok

A `-Submitter` paraméter segítségével azonosíthatja, aki a feladat elküldve.

```powershell
Get-AdlJob -Account $adla -Submitter "joe@contoso.com"
```

### <a name="list-jobs-by-submission-time"></a>Lista feladatok küldésének ideje

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

### <a name="wait-for-a-job-to-finish"></a>Várjon, amíg a feladat befejeződik

Ismétlődő helyett `Get-AdlAnalyticsJob` feladat befejeződéséig, használhatja a `Wait-AdlJob` parancsmagot, hogy a feladat befejezéséhez.

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

## <a name="analyzing-job-history"></a>Feladatelőzmények elemzése

Azure PowerShell-lel, amelyek futtatták a Data Lake analytics-feladatok előzményeinek elemzésére egy hatékony módszer. Használat és költségek betekintést használhatja azt. További megnézzük a [mintatárból Projektelemzés előzmények](https://github.com/Azure-Samples/data-lake-analytics-powershell-job-history-analysis)  

## <a name="list-job-pipelines-and-recurrences"></a>Lista feladat folyamatok és ismétlődések

Használja a `Get-AdlJobPipeline` parancsmag folyamat olvassa el a korábban elküldött feladatok.

```powershell
$pipelines = Get-AdlJobPipeline -Account $adla
$pipeline = Get-AdlJobPipeline -Account $adla -PipelineId "<pipeline ID>"
```

Használja a `Get-AdlJobRecurrence` parancsmag használatával tekintse meg az ismétlődési adatait a korábban elküldött feladatok.

```powershell
$recurrences = Get-AdlJobRecurrence -Account $adla

$recurrence = Get-AdlJobRecurrence -Account $adla -RecurrenceId "<recurrence ID>"
```


## <a name="manage-compute-policies"></a>Compute-szabályzatok kezelése

### <a name="list-existing-compute-policies"></a>A meglévő számítási szabályzatok listája

A `Get-AdlAnalyticsComputePolicy` parancsmag kapcsolatos információ a Data Lake Analytics-fiók számítási házirendek kérdezi le.

```powershell
$policies = Get-AdlAnalyticsComputePolicy -Account $adla
```

### <a name="create-a-compute-policy"></a>Számítási szabályzat létrehozása

A `New-AdlAnalyticsComputePolicy` parancsmag egy új Data Lake Analytics-fiók számítási szabályzatot hoz létre. Ebben a példában a maximális au-k érhető el a megadott felhasználó 50-re, és a minimális feladat prioritása a 250 állítja be.

```powershell
$userObjectId = (Get-AzureRmAdUser -SearchString "garymcdaniel@contoso.com").Id

New-AdlAnalyticsComputePolicy -Account $adla -Name "GaryMcDaniel" -ObjectId $objectId -ObjectType User -MaxDegreeOfParallelismPerJob 50 -MinPriorityPerJob 250
```
## <a name="manage-files"></a>Fájlok kezelése

### <a name="check-for-the-existence-of-a-file"></a>Ellenőrizze, hogy létezik-e a fájl.

```powershell
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

### <a name="uploading-and-downloading"></a>Fel- és letöltése

Töltsön fel egy fájlt.

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

Töltse le az egész mappáról rekurzív módon.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/" -Destination "c:\myData\" -Recurse
```

> [!NOTE]
> Ha a feltöltés és a letöltés folyamata megszakad, megpróbálhatja a folyamat folytatásához újra a parancsmagot a ``-Resume`` jelzőt.

## <a name="manage-the-u-sql-catalog"></a>A U-SQL-katalógus kezelése

A U-SQL-katalógus adatait és a kód felépítését, így azok megoszthatók U-SQL-parancsfájlok szolgál. A katalógus lehetővé teszi, hogy a lehető legjobb teljesítmény szolgálja, az Azure Data Lake adatokkal. További információk: [Use U-SQL catalog](data-lake-analytics-use-u-sql-catalog.md) (U-SQL-katalógus használata).

### <a name="list-items-in-the-u-sql-catalog"></a>Listaelemek az U-SQL-katalógusban

```powershell
# List U-SQL databases
Get-AdlCatalogItem -Account $adla -ItemType Database 

# List tables within a database
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database"

# List tables within a schema.
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database.schema"
```

### <a name="list-all-the-assemblies-the-u-sql-catalog"></a>A szerelvények a U-SQL-katalógus listázása

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

### <a name="get-details-about-a-catalog-item"></a>Részletes információkat a Katalóguselem

```powershell
# Get details of a table
Get-AdlCatalogItem  -Account $adla -ItemType Table -Path "master.dbo.mytable"

# Test existence of a U-SQL database.
Test-AdlCatalogItem  -Account $adla -ItemType Database -Path "master"
```

### <a name="store-credentials-in-the-catalog"></a>Store hitelesítő adatai a katalógusban

Egy U-SQL-adatbázison belül hozzon létre egy Azure-ban futtatott adatbázis-hitelesítő objektumot. U-SQL hitelesítő adatok jelenleg csak az ilyen típusú Katalóguselem PowerShell-lel hozhatja létre.

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

## <a name="manage-firewall-rules"></a>Tűzfalszabályok kezelése

### <a name="list-firewall-rules"></a>Tűzfal-szabályok listája

```powershell
Get-AdlAnalyticsFirewallRule -Account $adla
```

### <a name="add-a-firewall-rule"></a>Vegyen fel egy tűzfalszabályt

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

### <a name="remove-a-firewall-rule"></a>A tűzfalszabályok eltávolítása

```powershell
Remove-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName
```

### <a name="allow-azure-ip-addresses"></a>Az Azure IP-címek engedélyezése

```powershell
Set-AdlAnalyticsAccount -Name $adla -AllowAzureIpState Enabled
```

```powershell
Set-AdlAnalyticsAccount -Name $adla -FirewallState Enabled
Set-AdlAnalyticsAccount -Name $adla -FirewallState Disabled
```


## <a name="working-with-azure"></a>Az Azure-ral működő

### <a name="get-details-of-azurerm-errors"></a>Get-AzureRm hibái

```powershell
Resolve-AzureRmError -Last
```

### <a name="verify-if-you-are-running-as-an-administrator-on-your-windows-machine"></a>Győződjön meg arról, ha futtatásakor rendszergazdaként a Windows-gépen

```powershell
function Test-Administrator  
{  
    $user = [Security.Principal.WindowsIdentity]::GetCurrent();
    $p = New-Object Security.Principal.WindowsPrincipal $user
    $p.IsInRole([Security.Principal.WindowsBuiltinRole]::Administrator)  
}
```

### <a name="find-a-tenantid"></a>Keresse meg a bérlő azonosítója

Forrás-előfizetés neve:

```powershell
function Get-TenantIdFromSubscriptionName( [string] $subname )
{
    $sub = (Get-AzureRmSubscription -SubscriptionName $subname)
    $sub.TenantId
}

Get-TenantIdFromSubscriptionName "ADLTrainingMS"
```

Az egy előfizetés azonosítója:

```powershell
function Get-TenantIdFromSubscriptionId( [string] $subid )
{
    $sub = (Get-AzureRmSubscription -SubscriptionId $subid)
    $sub.TenantId
}

$subid = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
Get-TenantIdFromSubscriptionId $subid
```

Például a "contoso.com" tartomány-címről

```powershell
function Get-TenantIdFromDomain( $domain )
{
    $url = "https://login.windows.net/" + $domain + "/.well-known/openid-configuration"
    return (Invoke-WebRequest $url|ConvertFrom-Json).token_endpoint.Split('/')[3]
}

$domain = "contoso.com"
Get-TenantIdFromDomain $domain
```

### <a name="list-all-your-subscriptions-and-tenant-ids"></a>Összes előfizetés listázása és a bérlőazonosítók

```powershell
$subs = Get-AzureRmSubscription
foreach ($sub in $subs)
{
    Write-Host $sub.Name "("  $sub.Id ")"
    Write-Host "`tTenant Id" $sub.TenantId
}
```

## <a name="create-a-data-lake-analytics-account-using-a-template"></a>Egy sablon használatával a Data Lake Analytics-fiók létrehozása

Egy Azure erőforráscsoport-sablon a következő minta használatával is használhatja: [-sablon használatával a Data Lake Analytics-fiók létrehozása](https://github.com/Azure-Samples/data-lake-analytics-create-account-with-arm-template)

## <a name="next-steps"></a>További lépések
* [A Microsoft Azure Data Lake Analytics áttekintése](data-lake-analytics-overview.md)
* A Data Lake Analytics használatának első lépései a [az Azure portal](data-lake-analytics-get-started-portal.md) | [Azure PowerShell-lel](data-lake-analytics-get-started-powershell.md) | [Azure CLI-vel](data-lake-analytics-get-started-cli.md)
* Az Azure Data Lake Analytics használata kezelheti [az Azure portal](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell-lel](data-lake-analytics-manage-use-powershell.md) | [parancssori felület](data-lake-analytics-manage-use-cli.md) 
