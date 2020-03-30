---
title: PowerShell-parancsfájl – a Storage-fiók tárolójának megkeresése
description: Ismerje meg, hogyan használhatja az Azure PowerShell-parancsfájlt a helyreállítási szolgáltatások tárolójának megkereséséhez, ahol a tárfiók regisztrálva van.
ms.topic: sample
ms.date: 1/28/2020
ms.openlocfilehash: 786420ec8cef6516f7261c71b40641693efece07
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76775360"
---
# <a name="powershell-script-to-find-the-recovery-services-vault-where-a-storage-account-is-registered"></a>Powershell-parancsfájl a Recovery Services-tároló megkereséséhez, ahol a tárfiók regisztrálva van

Ez a parancsfájl segít megtalálni a helyreállítási szolgáltatások tárolóját, ahol a tárfiók regisztrálva van.

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

## <a name="how-to-execute-the-script"></a>Hogyan kell végrehajtani a forgatókönyvet

1. Mentse a fenti szkriptet a gépére egy választott névvel. Ebben a példában *a FindRegisteredStorageAccount.ps1*néven mentettük.
2. A parancsfájl végrehajtása a következő paraméterek megadásával:

    * **-ResourceGroupName** - A tárfiók erőforráscsoportja
    * **-StorageAccountName** - Tárfiók neve
    * **-SubscriptionID** - Az előfizetés azonosítója, ahol a tárfiók jelen van.

A következő példa megpróbálja megtalálni a helyreállítási szolgáltatások tárolóját, ahol az *afsaccount* tárfiók regisztrálva van:

```powershell
.\FindRegisteredStorageAccount.ps1 -ResourceGroupName AzureFiles -StorageAccountName afsaccount -SubscriptionId ef4ad5a7-c2c0-4304-af80-af49f49af3d1
```

## <a name="output"></a>Kimenet

A kimenet megjeleníti a helyreállítási szolgáltatások tárolójának teljes elérési útját, ahol a tárfiók regisztrálva van. Itt látható egy mintakimenet:

```output
Found Storage account afsaccount registered in vault: /subscriptions/ ef4ad5a7-c2c0-4304-af80-af49f49af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault123
```

## <a name="next-steps"></a>További lépések

Az [Azure-fájlmegosztások biztonsági mentése az Azure Portalról](https://docs.microsoft.com/azure/backup/backup-afs)
