---
title: Az Azure Site Recovery – Azure PowerShell-lel az Azure virtuális gépek replikálása során zárható ki lemez |} A Microsoft Docs
description: Ismerje meg, hogyan zárhat ki lemez Azure-beli virtuális gépek az Azure Site Recovery az Azure PowerShell használatával.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/18/2019
ms.author: asgang
ms.openlocfilehash: b378f77874b1ebef243836c101fa71a53f4775d1
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58517743"
---
# <a name="exclude-disks-from-replication-of-azure-vms-to-azure-using-azure-powershell"></a>Lemezek kizárása a replikációból Azure virtuális gépek az Azure-bA az Azure PowerShell-lel

Ez a cikk azt ismerteti, hogyan zárhat ki lemezeket, amikor az Azure virtuális gépek replikálása. A kizárás segítségével optimalizálható a felhasznált replikációs sávszélesség, valamint optimalizálhatók a lemezek által felhasznált céloldali erőforrások. Ez a funkció jelenleg csak az Azure PowerShell használatával van közzétéve.

## <a name="prerequisites"></a>Előfeltételek

Előkészületek:

- Ismernie kell a [forgatókönyv-architektúrát és az összetevőket](azure-to-azure-architecture.md).
- Minden összetevőre vonatkozóan tekintse át a [támogatási követelményeket](azure-to-azure-support-matrix.md).
- 5.7.0 verzióval rendelkezik, vagy nagyobb, mint az AzureRm PowerShell-modul. Ha telepíteni vagy frissíteni az Azure PowerShell-lel van szüksége, kövesse ezt [útmutató az Azure PowerShell telepítése és konfigurálása](/powershell/azureps-cmdlets-docs).
- Már létrehozott Recovery services-tárolót, és még meg a virtuális gépek legalább egyszer védelméről. Ha nem, majd megteheti az említett dokumentációnk segítségével [Itt](azure-to-azure-powershell.md) 

## <a name="why-exclude-disks-from-replication"></a>Miért érdemes lemezeket kizárni a replikációból?
A lemezek kizárása a replikációból az alábbiak miatt lehet szükséges:

- Elérte a virtuális gép [rátáról Azure Site Recovery-korlátok az adatok replikálása](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix)

- A kizárt lemezen előforduló adatváltozások nem fontosak, vagy nincs szükség azok replikálására.

- Tárterületet és hálózati erőforrásokat szeretne megtakarítani az adatváltozások replikálásának kihagyásával.


## <a name="how-to-exclude-disks-from-replication"></a>Hogyan zárhatók ki lemezek a replikációból?

A példában ez a cikk egy virtuális gépet, amely 1 rendelkezik az operációs rendszer és az USA keleti régiójában, 3 adatlemezek replikálja az USA 2. nyugati régió. A virtuális gép, a példában használt név AzureDemoVM. majd ki fogja zárni a lemez 1 fogja megőrizni a lemezen, 2. és 3

## <a name="get-details-of-the-virtual-machines-to-be-replicated"></a>Részletek a replikálni kívánt virtuális gép(ek)

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzureRmVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM     
```

```
ResourceGroupName  : A2AdemoRG
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/A2AdemoRG/providers/Microsoft.Compute/virtualMachines/AzureDemoVM
VmId               : 1b864902-c7ea-499a-ad0f-65da2930b81b
Name               : AzureDemoVM
Type               : Microsoft.Compute/virtualMachines
Location           : eastus
Tags               : {}
DiagnosticsProfile : {BootDiagnostics}
HardwareProfile    : {VmSize}
NetworkProfile     : {NetworkInterfaces}
OSProfile          : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
ProvisioningState  : Succeeded
StorageProfile     : {ImageReference, OsDisk, DataDisks}
```


Szerezze be a virtuális gép lemezei lemez adatai. Lemez adatai fogja használni, amikor később kezdve a virtuális gép replikációját.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-azure-virtual-machine"></a>Azure virtuális gépek replikálása

Az az alábbi példában a Microsoft rendelkezik feltételezi, hogy már rendelkezik egy gyorsítótárfiókot, a replikációs házirend és a leképezések. Ha nem, majd megteheti az említett dokumentációnk segítségével [Itt](azure-to-azure-powershell.md) 


Az Azure-beli virtuális gép replikálása **felügyelt lemezek**.

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzureRmResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data Disk 1 i.e StorageProfile.DataDisks[0] is excluded so we will provide it during the time of replication 

# Data disk 2
$datadiskId2  = $vm.StorageProfile.DataDisks[1].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[1]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[1]. StorageAccountType

$DataDisk2ReplicationConfig  = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId2 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

# Data Disk 3

$datadiskId3  = $vm.StorageProfile.DataDisks[2].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[2]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[2]. StorageAccountType

$DataDisk3ReplicationConfig  = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId3 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk2ReplicationConfig, $DataDisk3ReplicationConfig


#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

A kezdő replikációs művelet sikeres, ha a rendszer a helyreállítási régióba replikálja a virtuális gépek adatainak.

Az Azure Portalra léphet, és a replikált elemek felületen tekintse meg a virtuális gép kezdeti replikálása.
A replikálási folyamat elindítja a kezdeti beültetés egy példányát a replikáló lemez a virtuális gép a helyreállítási régióban. Ebben a fázisban a kezdeti replikáció fázis neve.

Kezdeti replikálás befejezése után a különbözeti szinkronizálási fázis replikációs helyezi át. Ezen a ponton a virtuális gép védelméhez. A védett virtuális gépen kattintson > lemezek, hogy ha a lemez ki van zárva, vagy sem.

## <a name="next-steps"></a>További lépések

[További](site-recovery-test-failover-to-azure.md) feladatátvételi teszt futtatásáról.
