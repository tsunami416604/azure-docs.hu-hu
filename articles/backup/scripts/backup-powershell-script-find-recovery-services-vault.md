---
title: PowerShell-parancsfájl – tároló létrehozása a Storage-fiókhoz
description: Megtudhatja, hogyan használható Azure PowerShell parancsfájl a Storage-fiók regisztrálásához használt Recovery Services-tároló megtalálásához.
ms.topic: sample
ms.date: 1/28/2020
ms.openlocfilehash: b343b2f93ed439188c5c0238bf108064f6e132c1
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86513251"
---
# <a name="powershell-script-to-find-the-recovery-services-vault-where-a-storage-account-is-registered"></a>PowerShell-parancsfájl azon Recovery Services-tároló megtalálásához, ahol a Storage-fiók regisztrálva van

Ez a szkript segít megtalálni a helyreállítási tárat, ahol a Storage-fiók regisztrálva van.

## <a name="sample-script"></a>Példaszkript

```powershell
Param(
        [Parameter(Mandatory=$True)][System.String] $ResourceGroupName,
        [Parameter(Mandatory=$True)][System.String] $StorageAccountName,
        [Parameter(Mandatory=$True)][System.String] $SubscriptionId
    )

Connect-AzAccount
Select-AzSubscription -Subscription $SubscriptionId
$vaults = Get-AzRecoveryServicesVault
$found = $false
foreach($vault in $vaults)
{
  Write-Verbose "Checking vault: $($vault.Id)" -Verbose
  
  $containers = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -FriendlyName $StorageAccountName -ResourceGroupName $ResourceGroupName -VaultId $vault.Id -Status Registered
  
  if($containers -ne $null)
  {
    $found = $True
    Write-Information "Found Storage account $StorageAccountName registered in vault: $($vault.Id)" -InformationAction Continue
    break;
  }
}

if(!$found)
{
     Write-Information "Storage account: $StorageAccountName is not registered in any vault of this subscription" -InformationAction Continue
}
```

## <a name="how-to-execute-the-script"></a>A szkript végrehajtása

1. Mentse a fenti szkriptet a gépen a választott névvel. Ebben a példában a *FindRegisteredStorageAccount.ps1ként *mentettük.
2. Futtassa a szkriptet a következő paraméterek megadásával:

    * **-ResourceGroupName** – a Storage-fiók erőforráscsoport
    * **-StorageAccountName** -Storage-fiók neve
    * **-SubscriptionID** – annak az előfizetésnek az azonosítója, amelyben a Storage-fiók megtalálható.

A következő példa megkísérli megkeresni azt a Recovery Services-tárolót, ahol a *afsaccount* Storage-fiók regisztrálva van:

```powershell
.\FindRegisteredStorageAccount.ps1 -ResourceGroupName AzureFiles -StorageAccountName afsaccount -SubscriptionId ef4ad5a7-c2c0-4304-af80-af49f49af3d1
```

## <a name="output"></a>Kimenet

A kimenet megjeleníti a Recovery Services-tároló teljes elérési útját, ahol a Storage-fiók regisztrálva van. Itt látható egy mintakimenet:

```output
Found Storage account afsaccount registered in vault: /subscriptions/ ef4ad5a7-c2c0-4304-af80-af49f49af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault123
```

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [készíthet biztonsági mentést az Azure-fájlmegosztás Azure Portal](../backup-afs.md)
