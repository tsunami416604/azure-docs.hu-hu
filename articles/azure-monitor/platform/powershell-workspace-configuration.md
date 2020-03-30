---
title: A Log Analytics létrehozása & konfigurálása a PowerShell használatával
description: Az Azure Monitor Log Analytics-munkaterületei a helyszíni vagy felhőalapú infrastruktúra kiszolgálóiról tárolnak adatokat. Az Azure-diagnosztika által generált gépi adatokat gyűjthet az Azure storage-ból.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/19/2019
ms.openlocfilehash: 2584cedceab1386cbab9c72bb4b510eebe2122bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80054697"
---
# <a name="manage-log-analytics-workspace-in-azure-monitor-using-powershell"></a>A Log Analytics-munkaterület kezelése az Azure Monitorban a PowerShell használatával

A Log [Analytics PowerShell-parancsmagokkal](https://docs.microsoft.com/powershell/module/az.operationalinsights/) különböző funkciókat hajthat végre az Azure Monitor naplószolgáltatásának munkaterületén egy parancssorból vagy egy parancsfájl részeként.  Példák a PowerShell használatával végrehajtható feladatokra:

* Munkaterület létrehozása
* Megoldás hozzáadása vagy eltávolítása
* Mentett keresések importálása és exportálása
* Számítógépcsoport létrehozása
* IIS-naplók gyűjtésének engedélyezése olyan számítógépekről, amelyeken telepítve van a Windows-ügynök
* Teljesítményszámlálók gyűjtése Linux- és Windows-számítógépekről
* Események gyűjtése a syslogból Linux rendszerű számítógépeken
* Események gyűjtése a Windows eseménynaplóiból
* Egyéni eseménynaplók gyűjtése
* A logelemző ügynök hozzáadása egy Azure virtuális géphez
* Naplóelemzés konfigurálása az Azure-diagnosztika használatával gyűjtött adatok indexelésére

Ez a cikk két kódmintát tartalmaz, amelyek a PowerShellből végrehajtható funkciók némelyikét szemléltetik.  A [Log Analytics PowerShell-parancsmag hivatkozási referencia](https://docs.microsoft.com/powershell/module/az.operationalinsights/) más függvények.

> [!NOTE]
> A Log Analytics neve korábban operatív elemzés, ezért a parancsmagokban használt név.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek
Ezek a példák az Az.OperationalInsights modul 1.0.0-s vagy újabb verziójával működnek.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Log Analytics-munkaterület létrehozása és konfigurálása
A következő parancsfájlminta bemutatja, hogyan kell:

1. Munkaterület létrehozása
2. Az elérhető megoldások felsorolása
3. Megoldások hozzáadása a munkaterülethez
4. Mentett keresések importálása
5. Mentett keresések exportálása
6. Számítógépcsoport létrehozása
7. IIS-naplók gyűjtésének engedélyezése olyan számítógépekről, amelyeken telepítve van a Windows-ügynök
8. Logikai lemezperf számlálók gyűjtése Linux rendszerű számítógépekről (% használt inodák; Ingyenes Megabájt; % Használt terület; Lemezátvitel/mp; Lemezolvasás/mp; Lemezírás/mp)
9. Syslog-események gyűjtése Linux-számítógépekről
10. Hiba- és figyelmeztetési események gyűjtése az alkalmazás eseménynaplójából Windows rendszerű számítógépekről
11. A rendelkezésre álló memória mbyte-teljesítményszámlálójának összegyűjtése Windows rendszerű számítógépekről
12. Egyéni napló gyűjtése

```powershell

$ResourceGroup = "oms-example"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique across all Azure subscriptions - Get-Random helps with this for the example code
$Location = "westeurope"

# List of solutions to enable
$Solutions = "Security", "Updates", "SQLAssessment"

# Saved Searches to import
$ExportedSearches = @"
[
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "WAD Events (All)",
        "Query":  "Type=Event SourceSystem:AzureStorage ",
        "Version":  1
    },
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "Current Disk Queue Length",
        "Query":  "Perf | where ObjectName == "LogicalDisk" and CounterName == "Current Disk Queue Length" and InstanceName == "C:"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Custom Log to collect
$CustomLog = @"
{
    "customLogName": "sampleCustomLog1",
    "description": "Example custom log datasource",
    "inputs": [
        {
            "location": {
            "fileSystemLocations": {
                "windowsFileTypeLogPaths": [ "e:\\iis5\\*.log" ],
                "linuxFileTypeLogPaths": [ "/var/logs" ]
                }
            },
        "recordDelimiter": {
            "regexDelimiter": {
                "pattern": "\\n",
                "matchIndex": 0,
                "matchIndexSpecified": true,
                "numberedGroup": null
                }
            }
        }
    ],
    "extractions": [
        {
            "extractionName": "TimeGenerated",
            "extractionType": "DateTime",
            "extractionProperties": {
                "dateTimeExtraction": {
                    "regex": null,
                    "joinStringRegex": null
                    }
                }
            }
        ]
    }
"@

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($solution in $Solutions) {
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
}

# List enabled solutions
(Get-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json

# Create Computer Group based on a query
New-AzOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Enable IIS Log Collection using agent
Enable-AzOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzOperationalInsightsLinuxPerformanceCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernel syslog collection"
Enable-AzOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs

New-AzOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```

> [!NOTE]
> Az egyéni napló konfigurációját meghatározó **CustomLogRawJson** paraméter formátuma összetett lehet. A [Get-AzOperationalInsightsDataSource](https://docs.microsoft.com/powershell/module/az.operationalinsights/get-azoperationalinsightsdatasource?view=azps-3.2.0) segítségével egy meglévő egyéni napló konfigurációjának beolvasásához használja. A **Properties** tulajdonság a **CustomLogRawJson** paraméterhez szükséges konfiguráció.

A fenti példában a regexDelimiter\\"n" volt a newline esetében. A naplóhatároló időbélyeg is lehet.  Ezek a támogatott formátumok:

| Formátum | Json RegEx formátum \\ használ két minden \ egy szabványos RegEx így \\ ha a vizsgálat egy RegEx app csökken \ | | |
| --- | --- | --- | --- |
| `YYYY-MM-DD HH:MM:SS` | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` | | |
| `M/D/YYYY HH:MM:SS AM/PM` | `(([0-1]\\d)|[0-9])/(([0-3]\\d)|(\\d))/((\\d{2})|(\\d{4}))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]\\s(AM|PM|am|pm)` | | |
| `dd/MMM/yyyy HH:MM:SS` | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|May|Apr|Jul|Jun|Aug|Oct|Sep|Nov|Dec|jan|feb|mar|may|apr|jul|jun|aug|oct|sep|nov|dec)\\/((19|20)[0-9][0-9]))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9])` |
| `MMM dd yyyy HH:MM:SS` | `(((?:Jan(?:uary)?|Feb(?:ruary)?|Mar(?:ch)?|Apr(?:il)?|May|Jun(?:e)?|Jul(?:y)?|Aug(?:ust)?|Sep(?:tember)?|Sept|Oct(?:ober)?|Nov(?:ember)?|Dec(?:ember)?)).*?((?:(?:[0-2]?\\d{1})|(?:[3][01]{1})))(?![\\d]).*?((?:(?:[1]{1}\\d{1}\\d{1}\\d{1})|(?:[2]{1}\\d{3})))(?![\\d]).*?((?:(?:[0-1][0-9])|(?:[2][0-3])|(?:[0-9])):(?:[0-5][0-9])(?::[0-5][0-9])?(?:\\s?(?:am|AM|pm|PM))?))` | | |
| `yyMMdd HH:mm:ss` | `([0-9]{2}([0][1-9]|[1][0-2])([0-2][0-9]|[3][0-1])\\s\\s?([0-1]?[0-9]|[2][0-3]):[0-5][0-9]:[0-5][0-9])` | | |
| `ddMMyy HH:mm:ss` | `(([0-2][0-9]|[3][0-1])([0][1-9]|[1][0-2])[0-9]{2}\\s\\s?([0-1]?[0-9]|[2][0-3]):[0-5][0-9]:[0-5][0-9])` | | |
| `MMM d HH:mm:ss` | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s\\s?([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0-1]?[0-9]|[2][0-3]):([0-5][0-9]):([0-5][0-9])` | | |
| `MMM  d HH:mm:ss` <br> két szóköz az MMM után | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s\\s([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])` | | |
| `MMM d HH:mm:ss` | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])` | | |
| `dd/MMM/yyyy:HH:mm:ss +zzzz` <br> ahol + + vagy egy - <br> ahol zzzz idő eltolás | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\/((19|20)[0-9][0-9]):([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])\\s[\\+|\\-][0-9]{4})` | | |
| `yyyy-MM-ddTHH:mm:ss` <br> A T egy szó szerinti T betű | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))T((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` | | |

## <a name="configuring-log-analytics-to-send-azure-diagnostics"></a>A Log Analytics konfigurálása az Azure-diagnosztika küldéséhez
Az Azure-erőforrások ügynök nélküli figyelése esetén az erőforrásokat engedélyezni kell és konfigurálni kell a Log Analytics-munkaterületre való íráshoz. Ez a megközelítés közvetlenül a munkaterületre küldi az adatokat, és nem igényel adatokat egy tárfiókba. A támogatott erőforrások a következők:

| Erőforrás típusa | Naplók | Mérőszámok |
| --- | --- | --- |
| Application Gateway-átjárók    | Igen | Igen |
| Automation-fiókok     | Igen | |
| Batch-fiókok          | Igen | Igen |
| Data Lake analitika     | Igen | |
| Data Lake áruház         | Igen | |
| Rugalmas SQL-készlet        |     | Igen |
| Event Hubs-névtér     |     | Igen |
| IoT Hubs                |     | Igen |
| Key Vault               | Igen | |
| Terheléselosztók          | Igen | |
| Logic Apps              | Igen | Igen |
| Network Security Groups (Hálózati biztonsági csoportok) | Igen | |
| Azure Cache for Redis             |     | Igen |
| Keresési szolgáltatások         | Igen | Igen |
| Service Bus névtere   |     | Igen |
| SQL (12. v.)               |     | Igen |
| Webhelyek               |     | Igen |
| Webkiszolgáló-farmok        |     | Igen |

A rendelkezésre álló metrikák részleteit az [Azure Monitor támogatott metrikák](../../azure-monitor/platform/metrics-supported.md)című részében találja.

A rendelkezésre álló naplók részleteit a [támogatott szolgáltatások és az erőforrásnaplók sémája](../../azure-monitor/platform/diagnostic-logs-schema.md)tartalmazza.

```powershell
$workspaceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzDiagnosticSetting -ResourceId $resourceId -WorkspaceId $workspaceId -Enabled $true
```

Az előző parancsmag használatával naplók gyűjtése a különböző előfizetésekben lévő erőforrásokból. A parancsmag képes az előfizetések között dolgozni, mivel az erőforrás-létrehozási naplók és a naplók által küldött munkaterület azonosítóját adja meg.


## <a name="configuring-log-analytics-workspace-to-collect-azure-diagnostics-from-storage"></a>A Log Analytics munkaterület konfigurálása az Azure-diagnosztika storage-ból történő gyűjtéséhez
Naplóadatok gyűjtése egy klasszikus felhőszolgáltatás vagy egy szolgáltatásháló-fürt futó példányán belül, először meg kell írnia az adatokat az Azure storage-ba. A Log Analytics-munkaterület ezután úgy van konfigurálva, hogy a naplókat a tárfiókból gyűjtse. A támogatott erőforrások a következők:

* Klasszikus felhőszolgáltatások (webes és feldolgozói szerepkörök)
* Szolgáltatásháló-fürtök

A következő példa bemutatja, hogyan:

1. Sorolja fel azokat a meglévő tárfiókokat és helyeket, amelyekből a munkaterület indexeli az adatokat
2. Tárfiókból olvasandó konfiguráció létrehozása
3. Az újonnan létrehozott konfiguráció frissítése további helyekről származó adatok indexelésére
4. Az újonnan létrehozott konfiguráció törlése

```powershell
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable"
$workspace = (Get-AzOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two lines with the storage account resource ID and the storage account key for the storage account you want the workspace to index
$storageId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles")

# Remove the insight
Remove-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight"

```

Az előző parancsfájl használatával is gyűjthetnaplókat a különböző előfizetésekben lévő tárfiókokból. A parancsfájl képes dolgozni az előfizetések között, mivel a tárfiók erőforrás-azonosítóját és a megfelelő hozzáférési kulcsot biztosítja. Amikor módosítja a hozzáférési kulcsot, frissítenie kell a tárolási betekintést, hogy az új kulcs.


## <a name="next-steps"></a>További lépések
* [Tekintse át a Log Analytics PowerShell-parancsmagjait,](https://docs.microsoft.com/powershell/module/az.operationalinsights/) ha további információt talál a PowerShell használatával kapcsolatban a Log Analytics konfigurálása érdekében.

