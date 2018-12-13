---
title: A powershellel létrehozása és konfigurálása a Log Analytics-munkaterület |} A Microsoft Docs
description: Log Analytics-adatait használja a helyszíni kiszolgálók, vagy a felhő-infrastruktúráról. Ha az Azure diagnostics által létrehozott Azure storage-ból is összegyűjtheti a számítógépadatok.
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: jochan
editor: ''
ms.assetid: 3b9b7ade-3374-4596-afb1-51b695f481c2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: conceptual
ms.date: 11/21/2016
ms.author: richrund
ms.openlocfilehash: b68cab9451503ed3634f1086c5711cca1eb6936f
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53191274"
---
# <a name="manage-log-analytics-using-powershell"></a>A Log Analytics felügyelete PowerShell használatával
Használhatja a [Log Analytics PowerShell-parancsmagok](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/) parancssori vagy parancsfájl részeként különféle funkciók végrehajtásához a Log Analyticsben.  A PowerShell használatával is elvégezheti a feladatok közé:

* Munkaterület létrehozása
* Adja hozzá, vagy eltávolíthat egy megoldást
* Importálás és exportálás mentett keresések
* Számítógépcsoport létrehozása
* Telepített Windows-ügynökkel rendelkező számítógépek IIS-naplók gyűjtésének engedélyezéséhez
* A Linux és Windows-számítógépekről teljesítményszámlálók gyűjtése
* Syslog események gyűjtésére a Linux rendszerű számítógépek 
* Windows-Eseménynapló eseményeinek gyűjtése
* Egyéni események naplóinak összegyűjtése
* A log analytics-ügynök hozzáadása egy Azure virtuális gépen
* Konfigurálja a log analytics index adatokat gyűjteni, az Azure diagnostics használatával

Ez a cikk két kódmintákért a függvények a Powershellből hajthat végre.  Olvassa el a [Log Analytics PowerShell-parancsmagok leírása](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/) más függvények.

> [!NOTE]
> A log Analytics korábbi neve Operational Insights, ezért a parancsmagok a név legyen.
> 
> 

## <a name="prerequisites"></a>Előfeltételek
Ezekben a példákban 2.3.0-át verziójú vagy újabb verzióiban az AzureRm.OperationalInsights modul működik.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Létrehozása és konfigurálása a Log Analytics-munkaterület
Az alábbi parancsfájl a példa bemutatja, hogyan lehet:

1. Munkaterület létrehozása
2. Az elérhető megoldások listája
3. Megoldások hozzáadása a munkaterülethez
4. Importálás mentett keresések
5. Exportálás mentett keresések
6. Számítógépcsoport létrehozása
7. Telepített Windows-ügynökkel rendelkező számítógépek IIS-naplók gyűjtésének engedélyezéséhez
8. Logikai lemez teljesítményszámlálók gyűjtése Linux rendszerű számítógépek (% Inode-OK; Szabad hely MB-ban; Foglalt hely; % Lemez átvitel/mp-ben; Lemezolvasások/mp; Lemezírások/mp)
9. Syslog-események gyűjtésére a Linux rendszerű számítógépek
10. Az alkalmazások eseménynaplójában a Windows-számítógépek hiba és figyelmeztetés eseményeinek gyűjtése
11. Windows-számítógépekről memória rendelkezésre álló memória (MB) teljesítményszámláló gyűjtése.
12. Egyéni napló gyűjtése 

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

# List enabled solutions
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

## <a name="configuring-log-analytics-to-index-azure-diagnostics"></a>A Log Analytics indexelése az Azure diagnostics konfigurálása
Az ügynök nélküli figyelés az Azure-erőforrások, az erőforrásokat az Azure diagnostics engedélyezni és konfigurálni a Log Analytics-munkaterület írni rendelkeznie kell. Ez a megközelítés elküldi az adatokat közvetlenül a Log Analyticshez, és nem szükséges egy tárfiókba írható adat. Támogatott erőforrások közé tartoznak:

| Erőforrás típusa | Logs | Mérőszámok |
| --- | --- | --- |
| Application Gateway-átjárók    | Igen | Igen |
| Automation-fiókok     | Igen | |
| Batch-fiókok          | Igen | Igen |
| A Data Lake analytics     | Igen | | 
| A Data Lake store         | Igen | |
| Rugalmas SQL-készlet        |     | Igen |
| Event Hubs-névtér     |     | Igen |
| IoT Hubok                |     | Igen |
| Key Vault               | Igen | |
| Terheléselosztók          | Igen | |
| Logic Apps              | Igen | Igen |
| Network Security Groups (Hálózati biztonsági csoportok) | Igen | |
| Azure Cache for Redis             |     | Igen |
| Szolgáltatások keresése         | Igen | Igen |
| Service Bus-névtér   |     | Igen |
| SQL (v12)               |     | Igen |
| Webhelyek               |     | Igen |
| Webkiszolgálófarmok        |     | Igen |

A rendelkezésre álló metrikák részleteit, tekintse meg a [az Azure monitorban támogatott mérőszámok](../../monitoring-and-diagnostics/monitoring-supported-metrics.md).

A részletes naplók, tekintse meg [szolgáltatások és a séma támogatja a diagnosztikai naplók](../../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md).

```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO" 

Set-AzureRmDiagnosticSetting -ResourceId $resourceId -WorkspaceId $workspaceId -Enabled $true
```

A fenti parancsmag használatával gyűjtsön naplókat azokról erőforrások különböző előfizetésekhez tartoznak. A parancsmag is képes működni előfizetések között, mivel meg van adva a naplók és a naplókat küld a munkaterület létrehozása egyaránt az erőforrás azonosítója.


## <a name="configuring-log-analytics-to-index-azure-diagnostics-from-storage"></a>A Log Analytics indexelése a storage-ból az Azure diagnostics konfigurálása
Klasszikus a felhőszolgáltatás és a egy service fabric-fürtben futó példányát belül származó naplóadatokat gyűjthet, először az Azure storage-szeretne adatokat írni kell. A log Analytics majd van konfigurálva a tárfiókot a naplók gyűjtését. Támogatott erőforrások közé tartoznak:

* Klasszikus cloud services (webes és feldolgozói szerepkörök)
* Service fabric-fürtök

A következő példa bemutatja, hogyan:

1. A meglévő tárfiókok és a Log Analytics az adatokat indexeli helyek listája
2. Hozzon létre egy storage-fiókból olvassa el a konfigurációt
3. Frissítse az újonnan létrehozott konfigurációt index adatokat további helyekről
4. Az újonnan létrehozott konfigurációjának törlése

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

Az előző parancsfájlt használhatja gyűjteni a tárfiókok eltérő előfizetésekben is. A parancsfájl is képes működni előfizetések között, mivel meg van adva, a tárolási fiók erőforrás-azonosítója és a egy megfelelő hozzáférési kulccsal. Amikor módosítja a hozzáférési kulcsot, az új kulcsot, a storage insight frissíteni szeretné.


## <a name="next-steps"></a>További lépések
* [Tekintse át a Log Analytics PowerShell-parancsmagok](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/) további információk a PowerShell használatával a Log Analytics-konfigurációhoz.

