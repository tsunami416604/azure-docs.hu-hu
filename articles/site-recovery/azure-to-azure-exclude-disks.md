---
title: Az Azure Virtuálisgép-lemezek kizárása a replikációból az Azure Site Recovery és az Azure PowerShell használatával
description: Megtudhatja, hogyan zárhatja ki az Azure-alapú virtuális gépek lemezeit az Azure Site Recovery során az Azure PowerShell használatával.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 02/18/2019
ms.openlocfilehash: 7355233bb7241571e3f3820aafac6952af245654
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973681"
---
# <a name="exclude-disks-from-powershell-replication-of-azure-vms"></a>Lemezek kizárása az Azure-beli virtuális gépek PowerShell-replikációjából

Ez a cikk ismerteti, hogyan zárhatja ki a lemezeket, amikor replikálja az Azure virtuális gépek. A felhasznált replikációs sávszélesség vagy a lemezek által használt céloldali erőforrások optimalizálása érdekében kizárhatja a lemezeket. Jelenleg ez a funkció csak az Azure PowerShellen keresztül érhető el.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Előkészületek:

- Győződjön meg arról, hogy megértette a [vész-helyreállítási architektúrát és az összetevőket.](azure-to-azure-architecture.md)
- Minden összetevőre vonatkozóan tekintse át a [támogatási követelményeket](azure-to-azure-support-matrix.md).
- Győződjön meg arról, hogy rendelkezik Az AzureRm PowerShell "Az" modul. A PowerShell telepítéséhez vagy frissítéséhez [olvassa el az Azure PowerShell-modul telepítése című témakört.](https://docs.microsoft.com/powershell/azure/install-az-ps)
- Győződjön meg arról, hogy legalább egyszer létrehozott egy helyreállítási szolgáltatások tárolót és védett virtuális gépeket. Ha még nem tette meg ezeket a dolgokat, kövesse a [folyamatbeállítása vész-helyreállítási Azure virtuális gépek az Azure PowerShell használatával.](azure-to-azure-powershell.md)
- Ha a replikációra engedélyezett Azure virtuális géphez lemezek hozzáadásával kapcsolatos információkat keres, [tekintse át ezt a cikket.](azure-to-azure-enable-replication-added-disk.md)

## <a name="why-exclude-disks-from-replication"></a>Miért zárja ki a lemezeket a replikációból?
Előfordulhat, hogy a lemezeket ki kell zárnia a replikációból, mert:

- A virtuális gép elérte az [Azure Site Recovery korlátait az adatváltozási arányok replikálása érdekében.](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix)

- A kizárt lemezen churned adatok nem fontos, vagy nem kell replikálni.

- Tároló- és hálózati erőforrásokat szeretne menteni úgy, hogy nem replikálja az adatokat.

## <a name="how-to-exclude-disks-from-replication"></a>Lemezek kizárása a replikációból

A példában egy virtuális gépet replikálunk, amely egy operációs rendszerrel és három adatlemezzel rendelkezik, amelyek az USA keleti régiójában vannak az USA nyugati régiójában 2 régióban. A virtuális gép neve *AzureDemoVM*. Kizárjuk az 1-es lemezt, és megtartjuk a 2-es és 3-as lemezeket.

## <a name="get-details-of-the-virtual-machines-to-replicate"></a>A replikálandó virtuális gépek részleteinek beszerezni

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

A virtuális gép lemezeivel kapcsolatos részletek. Ezt az információt fogja használni később, amikor elindítja a virtuális gép replikációját.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-an-azure-virtual-machine"></a>Azure-beli virtuális gép replikálása

A következő példában feltételezzük, hogy már rendelkezik egy gyorsítótár-tárfiókkal, replikációs házirenddel és hozzárendelésekkel. Ha nem rendelkezik ezekkel a dolgokkal, kövesse a folyamatot az [Azure PowerShell használatával az Azure virtuális gépekhez való vészhelyreállítás beállítása című](azure-to-azure-powershell.md)folyamatban.

Felügyelt lemezekkel rendelkező Azure-alapú virtuális gép *replikálása.*

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

Ha az indítási replikációs művelet sikeres, a virtuális gép adatai replikálódnak a helyreállítási régióba.

Az Azure Portalon megtekintheti a replikált virtuális gépeket a "replikált elemek" alatt.

A replikációs folyamat a helyreállítási régióban a virtuális gép replikálólemezeinek egy példányának beültetésével kezdődik. Ezt a fázist kezdeti replikációs fázisnak nevezzük.

A kezdeti replikáció befejezése után a replikáció a differenciálszinkronizációs fázisba lép. Ezen a ponton a virtuális gép védett. Válassza ki a védett virtuális gépet, hogy lássa, ki vannak-e zárva a lemezek.

## <a name="next-steps"></a>További lépések

További információ [a tesztfeladat-átvétel futtatásáról.](site-recovery-test-failover-to-azure.md)
