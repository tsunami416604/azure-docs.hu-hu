---
title: A hálózati biztonsági csoport folyamatnaplóinak tárolási blobjainak törlése az Azure Network Watcherben | Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan törölheti a hálózati biztonsági csoport folyamatnapló-tárolási blobok, amelyek kívül esnek az adatmegőrzési szabályzat időszakaz Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2019
ms.author: damendo
ms.openlocfilehash: 6d535bcc2e0831baae658796f76c8087d74c6a85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587209"
---
# <a name="delete-network-security-group-flow-log-storage-blobs-in-network-watcher"></a>Hálózati biztonsági csoport folyamatnapló-tárolóblobjainak törlése a Hálózatfigyelőben

Jelenleg van egy probléma, amely miatt [a hálózati biztonsági csoport (NSG) folyamatnaplók](network-watcher-nsg-flow-logging-overview.md) network watcher nem automatikusan törlődik a Blob storage megőrzési házirend beállításai alapján. Most már futtatnia kell egy PowerShell-parancsfájlt a folyamatnaplók manuális törléséhez a tárfiókból a jelen cikkben leírtak szerint.

## <a name="run-powershell-script-to-delete-nsg-flow-logs"></a>PowerShell-parancsfájl futtatása az NSG-folyamatnaplók törléséhez
 
Másolja és mentse a következő parancsfájlt egy olyan helyre, például az aktuális munkakönyvtárba. 

```powershell
# This powershell script deletes all NSG flow log blobs that should not be retained anymore as per configured retention policy.
# While configuring NSG flow logs on Azure portal, the user configures the retention period of NSG flow log blobs in
# their storage account (in days).
# This script reads all blobs and deletes blobs that are not to be retained (outside retention window)
# if the retention days are zero; all blobs are retained forever and hence no blobs are deleted.
#
#

param (
        [string] [Parameter(Mandatory=$true)]  $SubscriptionId,
        [string] [Parameter(Mandatory=$true)]  $Location,
        [switch] [Parameter(Mandatory=$false)] $Confirm
    )

Login-AzAccount

$SubId = Get-AzSubscription| Where-Object {$_.Id.contains($SubscriptionId.ToLower())}

if ($SubId.Count -eq 0)
{
    Write-Error 'The SubscriptionId does not exist' -ErrorAction Stop
}

Set-AzContext -SubscriptionId $SubscriptionId

$NsgList = Get-AzNetworkSecurityGroup | Where-Object {$_.Location -eq $Location}
$NW = Get-AzNetworkWatcher | Where-Object {$_.Location -eq $Location}

$FlowLogsList = @()
foreach ($Nsg in $NsgList)
{
    # Query Flow Log Status which are enabled
    $NsgFlowLog = Get-AzNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $Nsg.Id | Where-Object {$_.Enabled -eq "True"}
    if ($NsgFlowLog.Count -gt 0)
    {
        $FlowLogsList +=  $NsgFlowLog
        Write-Output ('Enabled NSG found: ' +  $NsgFlowLog.TargetResourceId)
    }
}

foreach ($Psflowlog in $FlowLogsList)
{
    $RetentionDays = $Psflowlog.RetentionPolicy.Days
    if ($RetentionDays -le 0)
    {
        continue
    }

    $Strings = $Psflowlog.StorageId -split '/'
    $RGName = $Strings[4]
    $StorageAccountName = $Strings[-1]

    $Key = (Get-AzStorageAccountKey -ResourceGroupName $RGName -Name $StorageAccountName).Value[1]
    $StorageAccount = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $Key

    $ContainerName = 'insights-logs-networksecuritygroupflowevent'  
    $BLobsList = Get-AzStorageBlob -Container $ContainerName -Context $StorageAccount.Context

    $TargetBLobsList = $BLobsList | Where-Object {$_.Name.contains($Psflowlog.TargetResourceId.ToUpper())}

    $RetentionDate = Get-Date
    $RetentionDate = $RetentionDate.AddDays(-1*$RetentionDays)
    $RetentionDateInUTC = $RetentionDate.ToUniversalTime()

    foreach ($Blob in $TargetBLobsList)
    {
        $BlobLastModifietedDTinUTC = [datetime]$Blob.LastModified.UtcDateTime

        if ($BlobLastModifietedDTinUTC -ge  $RetentionDateInUTC)
        {
            Write-Output ($Blob.Name + '===>' + $BlobLastModifietedDTinUTC  + ' ===> RETAINED')
            continue
        }

        if ($Confirm)
        {
            Write-Output (Blob to be deleted: $Blob.Name)
            $Confirmation = Read-Host "Are you sure you want to remove this blob (Y/N)?"
        }

        if ((-not $Confirm) -or ($Confirmation -eq 'Y'))
        {
            Write-Output ($Blob.Name + '===>' + $BlobLastModifietedDTinUTC  + ' ===> DELETED')
            Remove-AzStorageBlob -Container $ContainerName -Context $StorageAccount.Context -Blob $Blob.Name
        }
        else
        {
            Write-Output ($Blob.Name + '===>' + $BlobLastModifietedDTinUTC  + ' ===> RETAINED')
        }
    }
}

Write-Output ('Retention policy for all NSGs evaluated and completed successfully')
```

1. Szükség szerint adja meg a következő paramétereket a parancsfájlban:
   - **Előfizetési azonosító** [Kötelező]: Az előfizetés-azonosító, ahonnan törölni szeretné az NSG-folyamatnapló blobjait.
   - **Hely** [Kötelező]: Az NSG-k azon régiójának _helykarakterlánca,_ amelyhez törölni szeretné az NSG-folyamatnapló blobjait. Ezeket az információkat az Azure Portalon vagy a [GitHubon](https://github.com/Azure/azure-extensions-cli/blob/beb3d3fe984cfa9c7798cb11a274c5337968cbc5/regions.go#L23)tekintheti meg.
   - **Erősítse meg** [Nem kötelező]: Adja át a megerősítési jelzőt, ha manuálisan szeretné megerősíteni az egyes tárolási blobok törlését.

1. Futtassa a mentett parancsfájlt a következő példában látható módon, ahol a parancsfájl **delete-NsgFlowLogsBlobs.ps1**néven lett mentve:
   ```
   .\Delete-NsgFlowLogsBlobs.ps1 -SubscriptionId <subscriptionId> -Location  <location> -Confirm
   ```
    
## <a name="next-steps"></a>További lépések
- Az ügyfelek automatizálhatják a parancsfájl futtatását az [Azure Logic Apps](../logic-apps/logic-apps-overview.md) vagy az Azure [Automation](https://azure.microsoft.com/services/automation/) használatával
- Az NSG-naplózásról az [Azure Monitor hálózati biztonsági csoportok (NSG-k) naplói](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)című témakörben olvashat bővebben.

