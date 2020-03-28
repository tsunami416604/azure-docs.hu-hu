---
title: Rugalmasfeladat-ügynök létrehozása a PowerShell használatával
description: Megtudhatja, hogyan hozhat létre rugalmasfeladat-ügynököt a PowerShell használatával.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: tutorial
author: johnpaulkee
ms.author: joke
ms.reviwer: sstein
ms.date: 03/13/2019
ms.openlocfilehash: 74a72df9d8c0bc8a578fea57ab81fb496f8e6add
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74420368"
---
# <a name="create-an-elastic-job-agent-using-powershell"></a>Rugalmasfeladat-ügynök létrehozása a PowerShell használatával

A [rugalmas feladatok](sql-database-job-automation-overview.md#elastic-database-jobs-preview) lehetővé teszik egy vagy több Transact-SQL- (T-SQL-) szkript több adatbázisban történő, párhuzamos futtatását.

Ebben az oktatóanyagban megtudhatja, hogy mi szükséges a lekérdezés több adatbázisban való futtatásához:

> [!div class="checklist"]
> * Rugalmasfeladat-ügynök létrehozása
> * A feladatok hitelesítő adatainak létrehozása, amelyek segítségével a feladatok a célhelyen tudják végrehajtani a szkripteket
> * A feladatok futtatási célhelyeinek (kiszolgálók, rugalmas készletek, adatbázisok, szegmenstérképek) meghatározása
> * Adatbázishoz kötődő hitelesítő adatok létrehozása a céladatbázisokban, amelyekhez az ügynök csatlakozni tud a feladatok végrehajtásához
> * Feladat létrehozása
> * Feladatlépések hozzáadása egy feladathoz
> * Feladat-végrehajtás indítása
> * Feladat monitorozása

## <a name="prerequisites"></a>Előfeltételek

Az Elastic Database-feladatok frissített verziója rendelkezik egy új PowerShell-parancsmaggal az áttelepítés során használható. Ezek az új parancsmagok az összes meglévő feladathitelesítő adatokat, célokat (beleértve az adatbázisokat, kiszolgálókat, egyéni beszedéseket), a feladateseményindítókat, a feladatütemezéseket, a feladat tartalmát és a feladatokat egy új rugalmas feladatügynökre.

### <a name="install-the-latest-elastic-jobs-cmdlets"></a>Telepítse a legújabb Elastic Jobs parancsmagokat

Ha még nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladat megkezdése előtt.

Telepítse az **Az.Sql** modult a legújabb rugalmas feladat parancsmagok beszerzéséhez. Futtassa a következő parancsokat a PowerShell-ben rendszergazdai hozzáféréssel.

```powershell
# installs the latest PackageManagement and PowerShellGet packages
Find-Package PackageManagement | Install-Package -Force
Find-Package PowerShellGet | Install-Package -Force

# Restart your powershell session with administrative access

# Install and import the Az.Sql module, then confirm
Install-Module -Name Az.Sql
Import-Module Az.Sql

Get-Module Az.Sql
```

Az **Az.Sql** modul mellett ez az oktatóanyag is megköveteli az *SqlServer* PowerShell modul. További információt az [SQL Server PowerShell modul telepítése című témakörben](/sql/powershell/download-sql-server-ps-module)talál.

## <a name="create-required-resources"></a>A szükséges erőforrások létrehozása

A rugalmasfeladat-ügynök létrehozásához [feladat-adatbázisként](sql-database-job-automation-overview.md#job-database) használható (S0 vagy magasabb szintű) adatbázisra van szükség.

Az alábbi szkript egy új erőforráscsoportot, kiszolgálót és feladat-adatbázisként használható adatbázist hoz létre. A második parancsfájl létrehoz egy második kiszolgálót két üres adatbázissal, amelyen feladatokat hajthat végre.

A rugalmas feladatokhoz nem tartoznak külön elnevezési követelmények, így tetszőleges elnevezési konvenciót alkalmazhat, ha az megfelel [az Azure követelményeinek](/azure/architecture/best-practices/resource-naming).

```powershell
# sign in to Azure account
Connect-AzAccount

# create a resource group
Write-Output "Creating a resource group..."
$resourceGroupName = Read-Host "Please enter a resource group name"
$location = Read-Host "Please enter an Azure Region"
$rg = New-AzResourceGroup -Name $resourceGroupName -Location $location
$rg

# create a server
Write-Output "Creating a server..."
$agentServerName = Read-Host "Please enter an agent server name"
$agentServerName = $agentServerName + "-" + [guid]::NewGuid()
$adminLogin = Read-Host "Please enter the server admin name"
$adminPassword = Read-Host "Please enter the server admin password"
$adminPasswordSecure = ConvertTo-SecureString -String $AdminPassword -AsPlainText -Force
$adminCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $adminLogin, $adminPasswordSecure
$agentServer = New-AzSqlServer -ResourceGroupName $resourceGroupName -Location $location `
    -ServerName $agentServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($adminCred)

# set server firewall rules to allow all Azure IPs
Write-Output "Creating a server firewall rule..."
$agentServer | New-AzSqlServerFirewallRule -AllowAllAzureIPs
$agentServer

# create the job database
Write-Output "Creating a blank SQL database to be used as the Job Database..."
$jobDatabaseName = "JobDatabase"
$jobDatabase = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $agentServerName -DatabaseName $jobDatabaseName -RequestedServiceObjectiveName "S0"
$jobDatabase
```

```powershell
# create a target server and sample databases - uses the same credentials
Write-Output "Creating target server..."
$targetServerName = Read-Host "Please enter a target server name"
$targetServerName = $targetServerName + "-" + [guid]::NewGuid()
$targetServer = New-AzSqlServer -ResourceGroupName $resourceGroupName -Location $location `
    -ServerName $targetServerName -ServerVersion "12.0" -SqlAdministratorCredentials ($adminCred)

# set target server firewall rules to allow all Azure IPs
$targetServer | New-AzSqlServerFirewallRule -AllowAllAzureIPs
$targetServer | New-AzSqlServerFirewallRule -StartIpAddress 0.0.0.0 -EndIpAddress 255.255.255.255 -FirewallRuleName AllowAll
$targetServer

# create sample databases to execute jobs against
$db1 = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $targetServerName -DatabaseName "database1"
$db1
$db2 = New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $targetServerName -DatabaseName "database2"
$db2
```

## <a name="use-elastic-jobs"></a>Rugalmas feladatok használata

Rugalmas feladatok használatához regisztrálja a funkciót az Azure-előfizetésben a következő parancs futtatásával. Ezt a parancsot egyszer futtatja az előfizetéshez, amelyben ki kívánja építeni a rugalmas feladat ügynök. Azokat az előfizetéseket, amelyek csak feladatcélokat tartalmazó adatbázisokat tartalmaznak, nem kell regisztrálni.

```powershell
Register-AzProviderFeature -FeatureName sqldb-JobAccounts -ProviderNamespace Microsoft.Sql
```

### <a name="create-the-elastic-job-agent"></a>Rugalmasfeladat-ügynök létrehozása

A rugalmasfeladat-ügynök a feladatok létrehozásához, futtatásához és kezeléséhez használt Azure-erőforrás. Az ügynök ütemezés szerint vagy egyszeri alkalommal hajtja végre a feladatokat.

A **New-AzSqlElasticJobAgent** parancsmaghoz már léteznie kell egy Azure SQL-adatbázis, ezért a *resourceGroupName*, *serverName*és *databaseName* paramétereknek a meglévő erőforrásokra kell mutatniuk.

```powershell
Write-Output "Creating job agent..."
$agentName = Read-Host "Please enter a name for your new Elastic Job agent"
$jobAgent = $jobDatabase | New-AzSqlElasticJobAgent -Name $agentName
$jobAgent
```

### <a name="create-the-job-credentials"></a>A feladat hitelesítő adatainak létrehozása

A feladatok az adatbázis hatókörrel rendelkező hitelesítő adatokat használnak a célcsoport által megadott céladatbázisokhoz való csatlakozáshoz a végrehajtás és a parancsfájlok végrehajtásakor. A rendszer szintén ezeket az adatbázishoz kötődő hitelesítő adatokat használja a master adatbázishoz való csatlakozáshoz, az adott kiszolgálón vagy rugalmas készletben lévő adatbázisok enumerálására, ha a célcsoporttagtípus ezek valamelyike.

Az adatbázishoz kötődő hitelesítő adatokat a feladat-adatbázisban kell létrehozni. A feladat sikeres befejezéséhez minden céladatbázisnak rendelkeznie kell egy megfelelő engedélyeket tartalmazó bejelentkezési hozzáféréssel.

![Rugalmas feladatokhoz tartozó hitelesítő adatok](media/elastic-jobs-overview/job-credentials.png)

A képen szereplő hitelesítő adatok áttekintésén kívül vegye figyelembe azt is, hogy az alábbi szkriptben **GRANT**-parancsok is szerepelnek. Ezek az engedélyek szükségesek a jelen példafeladathoz kiválasztott szkript számára. Mivel a példa új táblát hoz létre a megcélzott adatbázisokban, minden céladatbázisnak megfelelő engedélyekre van szüksége a sikeres futtatáshoz.

A feladatokhoz tartozó hitelesítő adatok (feladat-adatbázisban történő) létrehozásához futtassa a következő szkriptet:

```powershell
# in the master database (target server)
# create the master user login, master user, and job user login
$params = @{
  'database' = 'master'
  'serverInstance' =  $targetServer.ServerName + '.database.windows.net'
  'username' = $adminLogin
  'password' = $adminPassword
  'outputSqlErrors' = $true
  'query' = "CREATE LOGIN masteruser WITH PASSWORD='password!123'"
}
Invoke-SqlCmd @params
$params.query = "CREATE USER masteruser FROM LOGIN masteruser"
Invoke-SqlCmd @params
$params.query = "CREATE LOGIN jobuser WITH PASSWORD='password!123'"
Invoke-SqlCmd @params

# for each target database
# create the jobuser from jobuser login and check permission for script execution
$targetDatabases = @( $db1.DatabaseName, $Db2.DatabaseName )
$createJobUserScript =  "CREATE USER jobuser FROM LOGIN jobuser"
$grantAlterSchemaScript = "GRANT ALTER ON SCHEMA::dbo TO jobuser"
$grantCreateScript = "GRANT CREATE TABLE TO jobuser"

$targetDatabases | % {
  $params.database = $_
  $params.query = $createJobUserScript
  Invoke-SqlCmd @params
  $params.query = $grantAlterSchemaScript
  Invoke-SqlCmd @params
  $params.query = $grantCreateScript
  Invoke-SqlCmd @params
}

# create job credential in Job database for master user
Write-Output "Creating job credentials..."
$loginPasswordSecure = (ConvertTo-SecureString -String "password!123" -AsPlainText -Force)

$masterCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "masteruser", $loginPasswordSecure
$masterCred = $jobAgent | New-AzSqlElasticJobCredential -Name "masteruser" -Credential $masterCred

$jobCred = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList "jobuser", $loginPasswordSecure
$jobCred = $jobAgent | New-AzSqlElasticJobCredential -Name "jobuser" -Credential $jobCred
```

### <a name="define-the-target-databases-to-run-the-job-against"></a>A feladat futtatásához.

A [célcsoport](sql-database-job-automation-overview.md#target-group) határozza meg azt az egy vagy több adatbázist, amely(ek)en az adott feladatlépés végre lesz hajtva.

A következő kódrészlet két célcsoportot hoz létre: *serverGroup*és *serverGroupExcludingDb2*. *a serverGroup* a kiszolgálón a végrehajtás időpontjában létező összes adatbázist, a *serverGroupExcludingDb2* pedig a kiszolgáló összes adatbázisát célozza meg, kivéve a *targetDb2 adatbázist:*

```powershell
Write-Output "Creating test target groups..."
# create ServerGroup target group
$serverGroup = $jobAgent | New-AzSqlElasticJobTargetGroup -Name 'ServerGroup'
$serverGroup | Add-AzSqlElasticJobTarget -ServerName $targetServerName -RefreshCredentialName $masterCred.CredentialName

# create ServerGroup with an exclusion of db2
$serverGroupExcludingDb2 = $jobAgent | New-AzSqlElasticJobTargetGroup -Name 'ServerGroupExcludingDb2'
$serverGroupExcludingDb2 | Add-AzSqlElasticJobTarget -ServerName $targetServerName -RefreshCredentialName $masterCred.CredentialName
$serverGroupExcludingDb2 | Add-AzSqlElasticJobTarget -ServerName $targetServerName -Database $db2.DatabaseName -Exclude
```

### <a name="create-a-job-and-steps"></a>Feladat létrehozása és lépések

Ez a példa egy feladatot és két feladatlépést határoz meg a feladat futtatásához. Az első lépésben (*step1*) egy új táblát (*Step1Table*) hozunk létre a *ServerGroup* célcsoport összes adatbázisában. A második lépésben (*step2*) egy új táblát (*Step2Table*) hozunk létre a *TargetDb2* kivételével az összes adatbázisban, mivel a korábban meghatározott célcsoport esetében azt kizártuk.

```powershell
Write-Output "Creating a new job..."
$jobName = "Job1"
$job = $jobAgent | New-AzSqlElasticJob -Name $jobName -RunOnce
$job

Write-Output "Creating job steps..."
$sqlText1 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step1Table')) CREATE TABLE [dbo].[Step1Table]([TestId] [int] NOT NULL);"
$sqlText2 = "IF NOT EXISTS (SELECT * FROM sys.tables WHERE object_id = object_id('Step2Table')) CREATE TABLE [dbo].[Step2Table]([TestId] [int] NOT NULL);"

$job | Add-AzSqlElasticJobStep -Name "step1" -TargetGroupName $serverGroup.TargetGroupName -CredentialName $jobCred.CredentialName -CommandText $sqlText1
$job | Add-AzSqlElasticJobStep -Name "step2" -TargetGroupName $serverGroupExcludingDb2.TargetGroupName -CredentialName $jobCred.CredentialName -CommandText $sqlText2
```

### <a name="run-the-job"></a>A feladat futtatása

Futtassa a következő parancsot a feladat azonnali futtatásához:

```powershell
Write-Output "Start a new execution of the job..."
$jobExecution = $job | Start-AzSqlElasticJob
$jobExecution
```

A művelet sikeres befejezését követően két új tábla jelenik meg a TargetDb1 adatbázisban, és csak egy új tábla a TargetDb2 adatbázisban:

   ![az új táblák ellenőrzése az SSMS-ben](media/elastic-jobs-overview/job-execution-verification.png)

A feladat későbbi futtatását is ütemezheti. A következő paranccsal ütemezheti az adott feladat futtatását egy későbbi időpontra:

```powershell
# run every hour starting from now
$job | Set-AzSqlElasticJob -IntervalType Hour -IntervalCount 1 -StartTime (Get-Date) -Enable
```

### <a name="monitor-status-of-job-executions"></a>A feladat-végrehajtások állapotának monitorozása

A feladat-végrehajtások részletes adatai a következő kódrészlettel kérdezhetők le:

```powershell
# get the latest 10 executions run
$jobAgent | Get-AzSqlElasticJobExecution -Count 10

# get the job step execution details
$jobExecution | Get-AzSqlElasticJobStepExecution

# get the job target execution details
$jobExecution | Get-AzSqlElasticJobTargetExecution -Count 2
```

Az alábbi táblázat a lehetséges feladat-végrehajtási állapotokat sorolja fel:

|Állapot|Leírás|
|:---|:---|
|**Létrehozva** | A feladat végrehajtása csak létre, és még nem folyamatban van.|
|**Folyamatban** | A feladat végrehajtása folyamatban van.|
|**Várakozás: Újrapróbálkozás** | A feladat végrehajtása nem tudta befejezni a műveletet, és arra vár, hogy újra megpróbálja.|
|**Sikeres** | A feladat végrehajtása sikeresen befejeződött.|
|**SucceededWithSkiped (Sikeres)** | A feladat végrehajtása sikeresen befejeződött, de néhány gyermeke kimaradt.|
|**Nem sikerült** | A feladat végrehajtása sikertelen volt, és kimerítette az újrapróbálkozásokat.|
|**Időd-kiállás** | A feladat végrehajtása időtelt.|
|**Megszakítva** | A feladat végrehajtása megszakadt.|
|**Kimarad** | A feladat végrehajtása kimarad, mert ugyanannak a feladatlépésnek egy másik végrehajtása már ugyanazon a célon futott.|
|**WaitingForChildJobExecutions** | A feladat végrehajtása a gyermekkivégzések befejezésére vár.|

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A jelen oktatóanyagban létrehozott erőforrásokat az erőforráscsoport törlésével távolíthatja el.

> [!TIP]
> Ha továbbra is ezekkel a feladatokkal szeretne dolgozni, nem törli a cikkben létrehozott erőforrásokat.

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban több adatbázisban fog futtatni egy Transact-SQL-szkriptet. Megismerte, hogyan hajthatja végre a következő feladatokat:

> [!div class="checklist"]
> * Rugalmasfeladat-ügynök létrehozása
> * A feladatok hitelesítő adatainak létrehozása, amelyek segítségével a feladatok a célhelyen tudják végrehajtani a szkripteket
> * A feladatok futtatási célhelyeinek (kiszolgálók, rugalmas készletek, adatbázisok, szegmenstérképek) meghatározása
> * Adatbázishoz kötődő hitelesítő adatok létrehozása a céladatbázisokban, amelyekhez az ügynök csatlakozni tud a feladatok végrehajtásához
> * Feladat létrehozása
> * Feladatlépés hozzáadása a feladathoz
> * Feladat-végrehajtás indítása
> * A feladat figyelése

> [!div class="nextstepaction"]
> [Rugalmas feladatok kezelése Transact-SQL használatával](elastic-jobs-tsql.md)
