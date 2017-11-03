---
title: "PowerShell segítségével hozza létre és konfigurálja a Naplóelemzési munkaterület |} Microsoft Docs"
description: "A helyszíni kiszolgálók elemzés által használt adatok bejelentkezhet, illetve a felhőalapú infrastruktúra. Gép adatgyűjtést az Azure storage Azure diagnostics generálásakor."
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: jochan
editor: 
ms.assetid: 3b9b7ade-3374-4596-afb1-51b695f481c2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: article
ms.date: 11/21/2016
ms.author: richrund
ms.openlocfilehash: 6807ab67e3593da82c147669b29bfdae3b6c967c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="manage-log-analytics-using-powershell"></a>A Log Analytics felügyelete PowerShell használatával
Használhatja a [napló Analytics PowerShell-parancsmagok](https://msdn.microsoft.com/library/mt188224\(v=azure.300\).aspx) végzik el a különböző funkciókat a Naplóelemzési parancssori vagy parancsfájl részeként.  A PowerShell használatával végezheti el a feladatok közé:

* Munkaterületek létrehozása
* Hozzáadni vagy eltávolítani egy megoldást
* Importálás és exportálás a mentett keresések
* Hozzon létre egy számítógépcsoportot
* A Windows-ügynök telepítve az IIS-naplók számítógépekről gyűjtésének engedélyezése
* A Linux és Windows számítógépekről a teljesítményszámlálók adatainak összegyűjtése
* A Linux rendszerű számítógépeken syslog események gyűjtése 
* A Windows Eseménynapló eseményeinek gyűjtése
* Egyéni eseménynaplók gyűjtése
* A log analytics agent hozzáadása egy Azure virtuális gépen
* Naplóelemzési Azure diagnostics használatával gyűjt index adatok konfigurálása

Ez a cikk ismerteti, amelyek bemutatják a powershellből a végrehajtható függvények két mintakódok.  Olvassa el a [napló Analytics PowerShell parancsmag-referencia](https://msdn.microsoft.com/library/mt188224\(v=azure.300\).aspx) az egyéb funkciót.

> [!NOTE]
> Naplóelemzési korábban meghívták az Operational Insights, ezért a parancsmagokban használt a név.
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Ezek a példák 2.3.0 verziójával vagy későbbi a AzureRm.OperationalInsights modul működik.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Hozza létre és konfigurálja a Naplóelemzési munkaterület
Az alábbi parancsfájl minta bemutatja, hogyan:

1. Munkaterületek létrehozása
2. A rendelkezésre álló megoldások felsorolása
3. A munkaterület megoldások hozzáadása
4. Importálás mentett keresések
5. Exportálás mentett keresések
6. Hozzon létre egy számítógépcsoportot
7. A Windows-ügynök telepítve az IIS-naplók számítógépekről gyűjtésének engedélyezése
8. Logikai lemez teljesítményszámlálói gyűjteni a Linux rendszerű számítógépek (% Inode-OK; Szabad hely MB-ban; Foglalt hely; % / Mp átvitelek; Lemezolvasások/mp; Lemezírás/mp)
9. Syslog-események gyűjtése Linux számítógépekről
10. Hiba és figyelmeztetési események gyűjtése az alkalmazások eseménynaplójában a Windows rendszerű számítógépek
11. A Windows rendszerű számítógépek memória rendelkezésre álló memória (MB) teljesítményszámláló gyűjtése.
12. Egy egyéni napló gyűjtése 

```

$ResourceGroup = "oms-example"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique - Get-Random helps with this for the example code
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
        "Query":  "Type=Perf ObjectName=LogicalDisk InstanceName=\"C:\" CounterName=\"Current Disk Queue Length\"",
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
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($solution in $Solutions) {
    Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
}

#List enabled solutions
(Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json 

# Create Computer Group based on a query
New-AzureRmOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Create a computer group based on names (up to 5000)
$computerGroup = """servername1.contoso.com"",""servername2.contoso.com"",""servername3.contoso.com"",""servername4.contoso.com"""
New-AzureRmOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Named Servers" -DisplayName "Named Servers" -Category "My Saved Searches" -Query $computerGroup -Version 1

# Enable IIS Log Collection using agent
Enable-AzureRmOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzureRmOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzureRmOperationalInsightsLinuxCustomLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzureRmOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernal syslog collection"
Enable-AzureRmOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzureRmOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzureRmOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs
New-AzureRmOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```

## <a name="configuring-log-analytics-to-index-azure-diagnostics"></a>Az Azure diagnostics indexelésre Naplóelemzési konfigurálása
Az ügynök nélküli figyelés az Azure-erőforrások, az erőforrások kell rendelkeznie az Azure diagnostics engedélyezni és konfigurálni a Naplóelemzési munkaterület írni. Ez a megközelítés adatokat közvetlenül küld a Naplóelemzési, és nem szükséges adatok tárfiókba kellene írni. Támogatott erőforrások többek között:

| Erőforrás típusa | Logs | Mérőszámok |
| --- | --- | --- |
| Application Gateway átjárók    | Igen | Igen |
| Automation-fiókok     | Igen | |
| Batch-fiókok          | Igen | Igen |
| A Data Lake analytics     | Igen | | 
| A Data Lake store         | Igen | |
| A rugalmas SQL-készlet        |     | Igen |
| Event Hub névtér     |     | Igen |
| IoT-központok                |     | Igen |
| Key Vault               | Igen | |
| Terheléselosztók          | Igen | |
| Logic Apps              | Igen | Igen |
| Network Security Groups (Hálózati biztonsági csoportok) | Igen | |
| Redis Cache             |     | Igen |
| Szolgáltatások keresése         | Igen | Igen |
| Service Bus-névtér   |     | Igen |
| SQL (12-es verzió)               |     | Igen |
| Webhelyek               |     | Igen |
| Kiszolgáló-webfarmok        |     | Igen |

Az elérhető mérőszámok leírását, [támogatott Azure-figyelő metrikák](../monitoring-and-diagnostics/monitoring-supported-metrics.md).

A naplók leírását, [szolgáltatások és a séma támogatja a diagnosztikai naplók](../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md).

```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO" 

Set-AzureRmDiagnosticSetting -ResourceId $resourceId -WorkspaceId $workspaceId -Enabled $true
```

A fenti parancsmag segítségével is naplóinak gyűjtése az erőforrásokat, amelyek különböző előfizetésekhez. A parancsmag annak tud dolgozni előfizetések között, mert meg van adva mindkét az erőforrás létrehozása a naplókat, valamint a naplókat küld a munkaterület azonosítóját.


## <a name="configuring-log-analytics-to-index-azure-diagnostics-from-storage"></a>Naplóelemzési tárolóból az Azure diagnostics indexelésre konfigurálása
Napló amikből adatgyűjtést belül egy klasszikus felhőalapú szolgáltatás, vagy a service fabric-fürt futó példányát, először az adatok írása az Azure storage kell. A Naplóelemzési majd gyűjteni a tárfiók van beállítva. Támogatott erőforrások többek között:

* Klasszikus felhőszolgáltatások (webes és feldolgozói szerepkörök)
* Service fabric-fürtök

Az alábbi példában látható hogyan:

1. A meglévő tárfiókok és a Naplóelemzési indexeli az adatok helyek felsorolása
2. A tárfiók olvasni-konfiguráció létrehozása
3. Az újonnan létrehozott konfigurációjának frissítése index adatokat további helyekről
4. Az újonnan létrehozott konfiguráció törlése

```
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable" 
$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two lines with the storage account resource ID and the storage account key for the storage account you want to Log Analytics to  
$storageId = "/subscriptions/ec11ca60-1234-491e-5678-0ea07feae25c/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles")

# Remove the insight
Remove-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" 

```

A fenti parancsfájl segítségével is gyűjteni a tárfiók különböző előfizetésekhez. A parancsfájl tud dolgozni előfizetések között, mert meg van adva, a tárfiók erőforrás azonosítója és a megfelelő hozzáférési kulcsot. A hozzáférési kulcs módosításakor kell frissíteni az új kulcsot a tároló betekintést.


## <a name="next-steps"></a>Következő lépések
* [Tekintse át a napló Analytics PowerShell-parancsmagok](https://msdn.microsoft.com/library/mt188224\(v=azure.300\).aspx) további információt a PowerShell használatával Log Analytics-konfigurációhoz.

