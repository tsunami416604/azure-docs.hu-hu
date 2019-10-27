---
title: Log Analytics munkaterület létrehozása és konfigurálása a PowerShell használatával | Microsoft Docs
description: Log Analytics munkaterületek Azure Monitor a helyszíni vagy Felhőbeli infrastruktúrában található kiszolgálók adatait tárolják. Az Azure-beli tárolóból származó gépi adatokat gyűjthet, ha azokat az Azure Diagnostics szolgáltatás hozza létre.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/19/2019
ms.openlocfilehash: 9d5bbaf02798c0fd87c40f1d952db19aac7b0b7e
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932082"
---
# <a name="manage-log-analytics-workspace-in-azure-monitor-using-powershell"></a>Log Analytics munkaterület kezelése a Azure Monitor a PowerShell használatával

A [log Analytics PowerShell-parancsmagokkal](https://docs.microsoft.com/powershell/module/az.operationalinsights/) különböző függvényeket hajthat végre a Azure Monitor egy log Analytics munkaterületen, a parancssorból vagy egy parancsfájl részeként.  A PowerShell-lel végrehajtható feladatok például a következők:

* Munkaterületek létrehozása
* Megoldás hozzáadása vagy eltávolítása
* Mentett keresések importálása és exportálása
* Számítógépcsoport létrehozása
* IIS-naplók gyűjteményének engedélyezése a telepített Windows-ügynökkel rendelkező számítógépekről
* Teljesítményszámlálók összegyűjtése Linux és Windows rendszerű számítógépekről
* Események gyűjtése a syslog-ből Linux rendszerű számítógépeken
* Események gyűjtése a Windows-eseménynaplóból
* Egyéni eseménynaplók gyűjtése
* A log Analytics-ügynök hozzáadása egy Azure-beli virtuális géphez
* A log Analytics konfigurálása az Azure Diagnostics használatával gyűjtött adatok indexeléséhez

Ez a cikk két olyan kódrészletet tartalmaz, amelyek a PowerShellből végrehajtható függvények némelyikét szemléltetik.  További függvényekért tekintse meg a [log Analytics PowerShell-parancsmag referenciáját](https://docs.microsoft.com/powershell/module/az.operationalinsights/) .

> [!NOTE]
> Log Analytics korábban Operational Insights néven hívták, ezért a parancsmagokban használt név.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek
Ezek a példák az az. OperationalInsights modulhoz tartozó 1.0.0 vagy újabb verzióval működnek.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Log Analytics munkaterület létrehozása és konfigurálása
A következő parancsfájl a következőket szemlélteti:

1. Munkaterületek létrehozása
2. Az elérhető megoldások listázása
3. Megoldások hozzáadása a munkaterülethez
4. Mentett keresések importálása
5. Mentett keresések exportálása
6. Számítógépcsoport létrehozása
7. IIS-naplók gyűjteményének engedélyezése a telepített Windows-ügynökkel rendelkező számítógépekről
8. Logikai lemezes teljesítmény-számlálók gyűjtése Linux rendszerű számítógépekről (felhasznált inode%-ban) Szabad megabájt; Felhasznált terület%-ban; Lemez átvitele/mp; Olvasási sebesség (lemez/mp) Írási idő/mp)
9. Syslog-események gyűjtése Linux rendszerű számítógépekről
10. Hiba-és figyelmeztetési események gyűjtése a Windows rendszerű számítógépekről származó alkalmazás-eseménynaplóból
11. Memória rendelkezésre álló memóriájának összegyűjtése (MB) teljesítményszámláló a Windows rendszerű számítógépekről
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
New-AzOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
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
A fenti példában a regexDelimiter "\\n" értékkel lett definiálva a sortöréshez. A naplózási határolójel is lehet időbélyeg.  A támogatott formátumok a következők:

| Formátum | A JSON RegEx formátuma két \\ használ a standard RegExben, így ha egy RegEx alkalmazásban végzett tesztelés csökkenti \\ | | |
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
| `dd/MMM/yyyy:HH:mm:ss +zzzz` <br> ahol a + + vagy a- <br> zzzz idő eltolása | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\/((19|20)[0-9][0-9]):([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])\\s[\\+|\\-][0-9]{4})` | | |
| `yyyy-MM-ddTHH:mm:ss` <br> A T egy literál betű, a t | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))T((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` | | |

## <a name="configuring-log-analytics-to-send-azure-diagnostics"></a>Log Analytics konfigurálása az Azure Diagnostics elküldéséhez
Az Azure-erőforrások ügynök nélküli figyeléséhez az erőforrásoknak engedélyezni kell az Azure Diagnostics szolgáltatást, és úgy kell konfigurálni, hogy Log Analytics munkaterületre írna. Ez a módszer közvetlenül a munkaterületre küld adatokat, és nem igényli, hogy az adatokat egy Storage-fiókba írja. A támogatott erőforrások a következők:

| Erőforrás típusa | Naplók | Metrikák |
| --- | --- | --- |
| Application Gateway átjárók    | Igen | Igen |
| Automation-fiókok     | Igen | |
| Batch-fiókok          | Igen | Igen |
| Data Lake Analitika     | Igen | |
| Data Lake áruház         | Igen | |
| Rugalmas SQL-készlet        |     | Igen |
| Event Hubs-névtér     |     | Igen |
| Iot Hubok                |     | Igen |
| Key Vault               | Igen | |
| Terheléselosztók          | Igen | |
| Logic Apps              | Igen | Igen |
| Hálózati biztonsági csoportok | Igen | |
| Azure Cache for Redis             |     | Igen |
| Szolgáltatások keresése         | Igen | Igen |
| Service Bus névtér   |     | Igen |
| SQL (V12)               |     | Igen |
| Webhelyek               |     | Igen |
| Webkiszolgáló-farmok        |     | Igen |

Az elérhető metrikák részleteiért tekintse meg a [támogatott mérőszámokat Azure monitor](../../azure-monitor/platform/metrics-supported.md)használatával.

Az elérhető naplók részleteiért tekintse meg a [támogatott szolgáltatások és sémák a diagnosztikai naplókhoz](../../azure-monitor/platform/diagnostic-logs-schema.md)című témakört.

```powershell
$workspaceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzDiagnosticSetting -ResourceId $resourceId -WorkspaceId $workspaceId -Enabled $true
```

Az előző parancsmagot is használhatja a különböző előfizetésekben található erőforrásokból származó naplók összegyűjtésére. A parancsmag az előfizetések között tud működni, mivel az erőforrás-létrehozási naplókat és a naplókat küldő munkaterületet is megadja.


## <a name="configuring-log-analytics-workspace-to-collect-azure-diagnostics-from-storage"></a>Log Analytics munkaterület konfigurálása az Azure Diagnostics tárolóból való összegyűjtéséhez
Ha a naplózási adatokat egy klasszikus felhőalapú szolgáltatás vagy egy Service Fabric-fürt futó példányán belül szeretné összegyűjteni, először az Azure Storage-ba kell írnia az adatokat. Ezután egy Log Analytics munkaterület lesz konfigurálva a naplók a Storage-fiókból való összegyűjtéséhez. A támogatott erőforrások a következők:

* Klasszikus Cloud Services (webes és feldolgozói szerepkörök)
* Service Fabric-fürtök

Az alábbi példa bemutatja, hogyan:

1. Felsorolja a meglévő Storage-fiókokat és-helyeket, amelyeket a munkaterület az adatok indexeléséhez fog indexelni
2. Storage-fiókból beolvasott konfiguráció létrehozása
3. Az újonnan létrehozott konfiguráció frissítése további helyekről származó adatok indexeléséhez
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

Az előző szkripttel is gyűjthet naplókat a különböző előfizetésekben lévő Storage-fiókokból. A szkript az előfizetések között tud működni, mivel a Storage-fiók erőforrás-AZONOSÍTÓját és egy hozzá tartozó hozzáférési kulcsot biztosít. A hozzáférési kulcs módosításakor frissítenie kell a tárolási betekintést, hogy az új kulcs legyen.


## <a name="next-steps"></a>Következő lépések
* [Tekintse át log Analytics PowerShell-parancsmagokat](https://docs.microsoft.com/powershell/module/az.operationalinsights/) a log Analytics konfigurálásához a PowerShell használatával kapcsolatos további információkért.

