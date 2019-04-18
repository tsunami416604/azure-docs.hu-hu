---
title: Az Azure Site Recovery – Azure PowerShell-lel az Azure virtuális gépek replikálása során lemezek kizárásának |} A Microsoft Docs
description: Ismerje meg, hogyan zárhat ki lemezeket az Azure-beli virtuális gépek Azure Site Recovery során Azure PowerShell használatával.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/18/2019
ms.author: asgang
ms.openlocfilehash: 54a32d7f7aa4bcab73f5828da3e7eba9d25276be
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678275"
---
# <a name="exclude-disks-from-powershell-replication-of-azure-vms"></a>Lemezek kizárása a PowerShell Azure virtuális gépek replikálása

Ez a cikk azt ismerteti, hogyan zárhat ki lemezeket az Azure virtuális gépek replikálásakor. A felhasznált replikációs sávszélességet vagy ezeket a lemezeket használó a céloldali erőforrások optimalizálása lemezek előfordulhat, hogy kizárja. Ez a funkció jelenleg csak az Azure Powershellen keresztül érhető el.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Előkészületek:

- Győződjön meg arról, hogy megértette a [vész-helyreállítási architektúra és összetevők](azure-to-azure-architecture.md).
- Minden összetevőre vonatkozóan tekintse át a [támogatási követelményeket](azure-to-azure-support-matrix.md).
- Győződjön meg arról, hogy "Az" AzureRm PowerShell modult. Telepítse vagy frissítse a PowerShell, lásd: [Azure PowerShell-modul telepítéséhez](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Győződjön meg arról, hogy létrehozott egy recovery services-tárolót, és legalább egy védett virtuális gépet. Ha ezeket nem tette, hajtsa végre az eljárást [vészhelyreállítás Azure virtuális gépek Azure PowerShell-lel beállítása](azure-to-azure-powershell.md).

## <a name="why-exclude-disks-from-replication"></a>Miért érdemes lemezeket kizárni a replikációból
A lemezek kizárása a replikációból, mert lehet szükség:

- Elérte a virtuális gép [az adatok replikálása Azure Site Recovery-korlátok rátáról](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix).

- A a kizárt lemezen előforduló adatváltozások nem fontos, vagy nincs szükség azok replikálására.

- Az adatok replikálásának tárolási és hálózati erőforrások menteni szeretné.

## <a name="how-to-exclude-disks-from-replication"></a>Hogyan zárhat ki lemezeket a replikációból

Ebben a példában azt replikálni egy virtuális gépet, amely egy operációs rendszer és az USA 2. nyugati régió az USA keleti régiójában lévő három adatlemezeket. A virtuális gép neve *AzureDemoVM*. 1. lemezének kizárása, és amelyet tartsa lemezeket 2. és 3.

## <a name="get-details-of-the-virtual-machines-to-replicate"></a>Részletek a virtuális gépek replikálásához

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

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

Ismerje meg a virtuális gép lemezeivel kapcsolatos részleteket. Ez az információ később lesz, replikáció a virtuális gép indításakor.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-an-azure-virtual-machine"></a>Az Azure virtuális gépek replikálása

A következő példában feltételezzük, hogy már rendelkezik egy gyorsítótárfiókot, replikációs szabályzatot és leképezéseket. Ha nem rendelkezik ezekről a dolgokról, hajtsa végre az eljárást [vészhelyreállítás Azure virtuális gépek Azure PowerShell-lel beállítása](azure-to-azure-powershell.md).

Egy Azure-beli virtuális gép replikálása *felügyelt lemezek*.

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration).

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data Disk 1 i.e StorageProfile.DataDisks[0] is excluded, so we will provide it during the time of replication. 

# Data disk 2
$datadiskId2  = $vm.StorageProfile.DataDisks[1].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[1]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[1]. StorageAccountType

$DataDisk2ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId2 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

# Data Disk 3

$datadiskId3  = $vm.StorageProfile.DataDisks[2].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[2]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[2]. StorageAccountType

$DataDisk3ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId3 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk2ReplicationConfig, $DataDisk3ReplicationConfig


#Start replication by creating a replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

A start-replikálási művelet sikeres, ha a rendszer a helyreállítási régióba replikálja a virtuális gép adatait.

Az Azure Portalon, és tekintse meg a replikált virtuális gépek, a "replikált elemek."

A replikálási folyamat egy másolatát a replikáló lemez a virtuális gép a helyreállítási régióban beültetés indítja el. Ebben a fázisban a kezdeti replikációs fázis neve.

Kezdeti replikálás befejezése után replikációs áthelyezi a különbségi-szinkronizálási fázis. Ezen a ponton a virtuális gép védelméhez. Válassza ki a védett virtuális gépen, hogy ha kizárt lemezeket.

## <a name="next-steps"></a>További lépések

Ismerje meg [feladatátvételi teszt futtatása](site-recovery-test-failover-to-azure.md).
