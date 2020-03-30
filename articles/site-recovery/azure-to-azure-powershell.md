---
title: Vészhelyreállítás az Azure PowerShell és az Azure Site Recovery használatával használt Azure-beli virtuális gépekszámára
description: Megtudhatja, hogyan állíthatja be a vészhelyreállítást az Azure virtuális gépekhez az Azure Site Recovery használatával az Azure PowerShell használatával.
services: site-recovery
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 3/29/2019
ms.author: sutalasi
ms.openlocfilehash: 583511194fb100add1d5fc4ea9c06a869cf652b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77212277"
---
# <a name="set-up-disaster-recovery-for-azure-virtual-machines-using-azure-powershell"></a>Vészhelyreállítás beállítása az Azure virtuális gépeihez az Azure PowerShell használatával

Ebben a cikkben megtudhatja, hogyan állíthatja be és tesztelheti a vészhelyreállítást az Azure PowerShell használatával az Azure virtuális gépekhez.

Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> - Recovery Services-tároló létrehozása.
> - Állítsa be a Vault környezetben a PowerShell-munkamenet.
> - Készítse elő a tárolót az Azure virtuális gépek replikálásának megkezdéséhez.
> - Hálózati hozzárendelések létrehozása.
> - Hozzon létre tárfiókokat a virtuális gépek replikálásához.
> - Az Azure virtuális gépei replikálhatók egy helyreállítási régióba a vészhelyreállításhoz.
> - Hajtson végre egy teszt feladatátvételi, érvényesítési és törlési teszt feladatátvételt.
> - Feladatátvétel a helyreállítási régióba.

> [!NOTE]
> Előfordulhat, hogy a portálon keresztül elérhető forgatókönyv-képességek közül nem mindegyik érhető el az Azure PowerShellen keresztül. Az Azure PowerShell jelenleg nem támogatott forgatókönyv-képességek a következők:
> - Annak megadása, hogy a virtuális gép összes lemezét replikálni kell anélkül, hogy a virtuális gép egyes lemezeit kifejezetten meg kellene adni.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Előkészületek:
- Ismernie kell a [forgatókönyv-architektúrát és az összetevőket](azure-to-azure-architecture.md).
- Minden összetevőre vonatkozóan tekintse át a [támogatási követelményeket](azure-to-azure-support-matrix.md).
- Az Azure PowerShell-modullal `Az` rendelkezik. Ha telepítenie vagy frissítenie kell az Azure PowerShellt, kövesse az [alábbi útmutatót az Azure PowerShell telepítéséhez és konfigurálásához.](/powershell/azure/install-az-ps)

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Bejelentkezés Microsoft Azure-előfizetésbe

Jelentkezzen be az Azure-előfizetésbe a `Connect-AzAccount` parancsmaggal.

```azurepowershell
Connect-AzAccount
```

Válassza ki az Azure-előfizetését. A `Get-AzSubscription` parancsmag segítségével leszeretné késelni az Azure-előfizetések, amelyekhez hozzáféréssel rendelkezik. Válassza ki az Azure-előfizetést a `Set-AzContext` parancsmag használatával való munkához.

```azurepowershell
Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

## <a name="get-details-of-the-virtual-machine-to-be-replicated"></a>A replikálandó virtuális gép részleteinek beszerezni

Ebben a cikkben egy virtuális gép az USA keleti régiójában replikálódik, és az USA nyugati régiójában 2 régióban helyreáll. A replikált virtuális gép operációsrendszer-lemezzel és egyetlen adatlemezzel rendelkezik. A példában használt virtuális gép `AzureDemoVM`neve .

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM
```

```Output
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

Lemezadatok beszereznie a virtuális gép lemezeihez. A rendszer később a lemez adatait használja a virtuális gép replikációjának indításakor.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

Hozzon létre egy erőforráscsoportot, amelyben a Recovery Services-tároló létrehozásához.

> [!IMPORTANT]
> * A recovery szolgáltatások tárolóés a virtuális gépek védett, különböző Azure-helyeken kell lennie.
> * A Recovery services-tároló erőforráscsoportjának és a védett virtuális gépeknek különböző Azure-helyeken kell lenniük.
> * A Recovery services-tároló, és az erőforráscsoport, amelyhez tartozik, lehet ugyanabban az Azure-helyen.

Ebben a cikkben a példában a védett virtuális gép az USA keleti régiójában található. A vész-helyreállítási kijelölt helyreállítási régió az USA nyugati régiója 2 régió. A helyreállítási szolgáltatások tároló, és az erőforrás-csoport a tároló, mind a helyreállítási régióban, USA nyugati régióban 2.

```azurepowershell
#Create a resource group for the recovery services vault in the recovery Azure region
New-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"
```

```Output
ResourceGroupName : a2ademorecoveryrg
Location          : westus2
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg
```

Hozzon létre egy helyreállítási szolgáltatások tároló. Ebben a példában egy `a2aDemoRecoveryVault` elnevezett Helyreállítási szolgáltatások tároló jön létre az USA nyugati régiójában 2 régióban.

```azurepowershell
#Create a new Recovery services vault in the recovery region
$vault = New-AzRecoveryServicesVault -Name "a2aDemoRecoveryVault" -ResourceGroupName "a2ademorecoveryrg" -Location "West US 2"

Write-Output $vault
```

```Output
Name              : a2aDemoRecoveryVault
ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoRecoveryVault
Type              : Microsoft.RecoveryServices/vaults
Location          : westus2
ResourceGroupName : a2ademorecoveryrg
SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

## <a name="set-the-vault-context"></a>A tároló környezetének beállítása

Állítsa be a tároló környezetben a PowerShell-munkamenetben való használatra. A tároló környezetének beállítása után az Azure Site Recovery-műveletek a PowerShell-munkamenetben a kiválasztott tároló környezetében történik.

```azurepowershell
#Setting the vault context.
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

```Output
ResourceName         ResourceGroupName ResourceNamespace          ResourceType
------------         ----------------- -----------------          -----------
a2aDemoRecoveryVault a2ademorecoveryrg Microsoft.RecoveryServices Vaults
```

```azurepowershell
#Delete the downloaded vault settings file
Remove-Item -Path $Vaultsettingsfile.FilePath
```

Az Azure-azure-ból az Azure-ba való migrálás hoz a tároló környezetét az újonnan létrehozott tárolóra állíthatja be:

```azurepowershell
#Set the vault context for the PowerShell session.
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

## <a name="prepare-the-vault-to-start-replicating-azure-virtual-machines"></a>A tároló előkészítése az Azure virtuális gépek replikálásának megkezdéséhez

### <a name="create-a-site-recovery-fabric-object-to-represent-the-primary-source-region"></a>Site Recovery hálóobjektum létrehozása az elsődleges (forrás) régió képviseletére

A tárolóban lévő hálóobjektum egy Azure-régiót jelöl. Az elsődleges hálóobjektum jön létre, hogy képviselje az Azure-régióban, amely a tároló védett virtuális gépek. Ebben a cikkben a példában a védett virtuális gép az USA keleti régiójában található.

- Régiónként csak egy hálóobjektum hozható létre.
- Ha korábban engedélyezte a Site Recovery replikációt egy virtuális géphez az Azure Portalon, a Site Recovery automatikusan létrehoz egy hálóobjektumot. Ha egy adott területhez létezik hálóobjektum, nem hozhat létre újat.

Mielőtt elkezdené, ismerje meg, hogy a Site Recovery műveletek aszinkron módon hajtják végre. Amikor egy műveletet kezdeményez, egy Azure Site Recovery feladat elküldésre kerül, és egy feladatkövetési objektumot ad vissza. A feladatkövetési objektum segítségével a feladat (`Get-AzRecoveryServicesAsrJob`) legfrissebb állapotának leszámításához és a művelet állapotának figyeléséhez használható.

```azurepowershell
#Create Primary ASR fabric
$TempASRJob = New-AzRecoveryServicesAsrFabric -Azure -Location 'East US'  -Name "A2Ademo-EastUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$PrimaryFabric = Get-AzRecoveryServicesAsrFabric -Name "A2Ademo-EastUS"
```

Ha több Azure-régióból származó virtuális gépek et ugyanahhoz a tárolóhoz védik, hozzon létre egy hálóobjektumot minden egyes azure-forrás-Azure-régióhoz.

### <a name="create-a-site-recovery-fabric-object-to-represent-the-recovery-region"></a>Site Recovery hálóobjektum létrehozása a helyreállítási régió képviseletére

A helyreállítási háló objektum képviseli a helyreállítási Azure helyét. Ha van egy feladatátvétel, a virtuális gépek replikálódiknak, és a helyreállítási környezet által képviselt helyreállítási régióba. Ebben a példában használt helyreállítási Azure-régió az USA nyugati régiója 2.

```azurepowershell
#Create Recovery ASR fabric
$TempASRJob = New-AzRecoveryServicesAsrFabric -Azure -Location 'West US 2'  -Name "A2Ademo-WestUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$RecoveryFabric = Get-AzRecoveryServicesAsrFabric -Name "A2Ademo-WestUS"
```

### <a name="create-a-site-recovery-protection-container-in-the-primary-fabric"></a>Hely-helyreállítási védelmi tároló létrehozása az elsődleges hálóban

A védelmi tároló egy tároló, amely a hálón belül replikált elemek csoportosítására szolgál.

```azurepowershell
#Create a Protection container in the primary Azure region (within the Primary fabric)
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainer -InputObject $PrimaryFabric -Name "A2AEastUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

Write-Output $TempASRJob.State

$PrimaryProtContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $PrimaryFabric -Name "A2AEastUSProtectionContainer"
```

### <a name="create-a-site-recovery-protection-container-in-the-recovery-fabric"></a>Hely-helyreállítási védelmi tároló létrehozása a helyreállítási hálóban

```azurepowershell
#Create a Protection container in the recovery Azure region (within the Recovery fabric)
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainer -InputObject $RecoveryFabric -Name "A2AWestUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"

Write-Output $TempASRJob.State

$RecoveryProtContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $RecoveryFabric -Name "A2AWestUSProtectionContainer"
```

### <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

```azurepowershell
#Create replication policy
$TempASRJob = New-AzRecoveryServicesAsrPolicy -AzureToAzure -Name "A2APolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$ReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "A2APolicy"
```

### <a name="create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container"></a>Védelmi tároló leképezésének létrehozása az elsődleges és a helyreállítási védelmi tároló között

A védelmi tároló leképezése leképezi az elsődleges védelmi tárolót egy helyreállítási védelmi tárolóval és egy replikációs házirenddel. Hozzon létre egy-egy leképezést minden olyan replikációs házirendhez, amelyet a virtuális gépek védelmi tárolópár közötti replikálására fog használni.

```azurepowershell
#Create Protection container mapping between the Primary and Recovery Protection Containers with the Replication policy
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "A2APrimaryToRecovery" -Policy $ReplicationPolicy -PrimaryProtectionContainer $PrimaryProtContainer -RecoveryProtectionContainer $RecoveryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$EusToWusPCMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $PrimaryProtContainer -Name "A2APrimaryToRecovery"
```

### <a name="create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover"></a>Védelmi tároló hozzárendelésének létrehozása feladat-visszavételhez (feladatátvétel utáni fordított replikáció)

Feladatátvétel után, ha készen áll, hogy a feladatátvételi virtuális gép vissza az eredeti Azure-régióban, feladat-visszavétel. A feladat-visszavételhez a feladatátvételi virtuális gép fordított replikálása a feladatátvevő régióból az eredeti régióba. A fordított replikációhoz az eredeti régió és a helyreállítási régió kapcsolója szerepkörei. Az eredeti régió most lesz az új helyreállítási régió, és mi volt eredetileg a helyreállítási régió most lesz az elsődleges régió. A fordított replikáció védelmi tárolóleképezése az eredeti és a helyreállítási régiók kapcsolt szerepköreit jelöli.

```azurepowershell
#Create Protection container mapping (for fail back) between the Recovery and Primary Protection Containers with the Replication policy
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "A2ARecoveryToPrimary" -Policy $ReplicationPolicy -PrimaryProtectionContainer $RecoveryProtContainer -RecoveryProtectionContainer $PrimaryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$WusToEusPCMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $RecoveryProtContainer -Name "A2ARecoveryToPrimary"
```

## <a name="create-cache-storage-account-and-target-storage-account"></a>Gyorsítótár-tárfiók és céltárfiók létrehozása

A gyorsítótár-tárfiók egy szabványos tárfiók ugyanabban az Azure-régióban, mint a replikált virtuális gép. A gyorsítótár-tárfiók a replikációs módosítások ideiglenes tárolására szolgál, mielőtt a módosítások átkerülnek a helyreállítási Azure-régióba. Választhat, de ez nem szükséges, a virtuális gép különböző lemezeikülönböző gyorsítótártári fiókok megadásához.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$EastUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestorage" -ResourceGroupName "A2AdemoRG" -Location 'East US' -SkuName Standard_LRS -Kind Storage
```

A **nem felügyelt lemezeket használó**virtuális gépek esetében a céltárfiók a helyreállítási régióban található tárfiók, amelyre a virtuális gép lemezei replikálódnak. A céltárfiók lehet egy standard szintű tárfiók vagy egy prémium szintű tárfiók. Válassza ki a szükséges tárfiók típusát a lemezek adatváltozási sebessége (IO-írási sebesség) és az Azure Site Recovery által támogatott lemorzsolódási korlátok a tárolási típus hoz.

```azurepowershell
#Create Target storage account in the recovery region. In this case a Standard Storage account
$WestUSTargetStorageAccount = New-AzStorageAccount -Name "a2atargetstorage" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -SkuName Standard_LRS -Kind Storage
```

## <a name="create-network-mappings"></a>Hálózati hozzárendelések létrehozása

A hálózati leképezés az elsődleges régióban lévő virtuális hálózatokat a helyreállítási régió ban lévő virtuális hálózatokhoz rendeli hozzá. A hálózati leképezés határozza meg az Azure virtuális hálózat a helyreállítási régióban, amely egy virtuális gép az elsődleges virtuális hálózat feladatátvételt. Egyetlen Azure virtuális hálózat csak egyetlen Azure virtuális hálózathoz képezhető le egy helyreállítási régióban.

- Hozzon létre egy Azure virtuális hálózatot a helyreállítási régióban, hogy feladatátvételt:

   ```azurepowershell
    #Create a Recovery Network in the recovery region
    $WestUSRecoveryVnet = New-AzVirtualNetwork -Name "a2arecoveryvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.0.0.0/16"

    Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $WestUSRecoveryVnet -AddressPrefix "10.0.0.0/20" | Set-AzVirtualNetwork

    $WestUSRecoveryNetwork = $WestUSRecoveryVnet.Id
   ```

- Az elsődleges virtuális hálózat beolvasása. A virtuális hálózat, amelyhez a virtuális gép csatlakozik:

   ```azurepowershell
    #Retrieve the virtual network that the virtual machine is connected to

    #Get first network interface card(nic) of the virtual machine
    $SplitNicArmId = $VM.NetworkProfile.NetworkInterfaces[0].Id.split("/")

    #Extract resource group name from the ResourceId of the nic
    $NICRG = $SplitNicArmId[4]

    #Extract resource name from the ResourceId of the nic
    $NICname = $SplitNicArmId[-1]

    #Get network interface details using the extracted resource group name and resource name
    $NIC = Get-AzNetworkInterface -ResourceGroupName $NICRG -Name $NICname

    #Get the subnet ID of the subnet that the nic is connected to
    $PrimarySubnet = $NIC.IpConfigurations[0].Subnet

    # Extract the resource ID of the Azure virtual network the nic is connected to from the subnet ID
    $EastUSPrimaryNetwork = (Split-Path(Split-Path($PrimarySubnet.Id))).Replace("\","/")
   ```

- Hozzon létre hálózati leképezést az elsődleges virtuális hálózat és a helyreállítási virtuális hálózat között:

   ```azurepowershell
    #Create an ASR network mapping between the primary Azure virtual network and the recovery Azure virtual network
    $TempASRJob = New-AzRecoveryServicesAsrNetworkMapping -AzureToAzure -Name "A2AEusToWusNWMapping" -PrimaryFabric $PrimaryFabric -PrimaryAzureNetworkId $EastUSPrimaryNetwork -RecoveryFabric $RecoveryFabric -RecoveryAzureNetworkId $WestUSRecoveryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
    Write-Output $TempASRJob.State
   ```

- Hálózati leképezés létrehozása fordított irányhoz (visszavétel):

    ```azurepowershell
    #Create an ASR network mapping for fail back between the recovery Azure virtual network and the primary Azure virtual network
    $TempASRJob = New-AzRecoveryServicesAsrNetworkMapping -AzureToAzure -Name "A2AWusToEusNWMapping" -PrimaryFabric $RecoveryFabric -PrimaryAzureNetworkId $WestUSRecoveryNetwork -RecoveryFabric $PrimaryFabric -RecoveryAzureNetworkId $EastUSPrimaryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
    Write-Output $TempASRJob.State
    ```

## <a name="replicate-azure-virtual-machine"></a>Az Azure virtuális gép replikálása

Replikálja az Azure virtuális gépet **felügyelt lemezekkel.**

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId = $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data disk
$datadiskId1 = $vm.StorageProfile.DataDisks[0].ManagedDisk.Id
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

Replikálja az Azure virtuális gépet **nem felügyelt lemezekkel.**

```azurepowershell
#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#Disk replication configuration for the OS disk
$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $OSDiskVhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Disk replication configuration for data disk
$DataDisk1ReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $DataDisk1VhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

Miután a replikációs művelet sikeres volt, a rendszer replikálja a virtuálisgép-adatokat a helyreállítási régióba.

A replikációs folyamat először a helyreállítási régióban a virtuális gép replikálólemezeinek egy példányát veti el. Ezt a fázist a kezdeti replikációs fázisnak nevezzük.

Az AFter kezdeti replikációja befejeződik, a replikáció a különbözeti szinkronizálási fázisba kerül. Ezen a ponton a virtuális gép védett, és egy teszt feladatátvételi műveletet lehet végrehajtani rajta. A virtuális gépet képviselő replikált elem replikációs állapota a kezdeti replikáció befejezése után **védett** állapotba kerül.

Figyelje a virtuális gép replikációs állapotát és replikációs állapotát a replikációval védett elem nek megfelelő részletek beszerzésével.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

```
FriendlyName ProtectionState ReplicationHealth
------------ --------------- -----------------
AzureDemoVM  Protected       Normal
```

## <a name="do-a-test-failover-validate-and-cleanup-test-failover"></a>A teszt feladatátvételi, érvényesítésére és karbantartására teszt feladatátvétel

Miután a virtuális gép replikációja elérte a védett állapotot, a virtuális gépen (a virtuális gép replikációs védett elemén) tesztfeladat-átvételi művelet hajtható végre.

```azurepowershell
#Create a separate network for test failover (not connected to my DR network)
$TFOVnet = New-AzVirtualNetwork -Name "a2aTFOvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.3.0.0/16"

Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $TFOVnet -AddressPrefix "10.3.0.0/20" | Set-AzVirtualNetwork

$TFONetwork= $TFOVnet.Id
```

Végezze el a teszt feladatátvételt.

```azurepowershell
$ReplicationProtectedItem = Get-AzRecoveryServicesAsrReplicationProtectedItem -FriendlyName "AzureDemoVM" -ProtectionContainer $PrimaryProtContainer

$TFOJob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -AzureVMNetworkId $TFONetwork -Direction PrimaryToRecovery
```

Várja meg, amíg a teszt feladatátvételi művelet befejeződik.

```azurepowershell
Get-AzRecoveryServicesAsrJob -Job $TFOJob
```

```Output
Name             : 3dcb043e-3c6d-4e0e-a42e-8d4245668547
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/3dcb043e-3c6d-4e0e-a42e-8d4245668547
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : TestFailover
DisplayName      : Test failover
ClientRequestId  : 1ef8515b-b130-4452-a44d-91aaf071931c ActivityId: 907bb2bc-ebe6-4732-8b66-77d0546eaba8
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:29:43 AM
EndTime          : 4/25/2018 4:33:06 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   :
Tasks            : {Prerequisites check for test failover, Create test virtual machine, Preparing the virtual machine, Start the virtual machine}
Errors           : {}
```

Miután a teszt feladatátvételi feladat sikeresen befejeződött, csatlakozhat a virtuális gépen átvett teszthez, és ellenőrizheti a teszt feladatátvételt.

Miután a tesztelés befejeződött a teszt feladatátvételi virtuális gépen, tisztítsa meg a teszt példányt a tisztítási teszt feladatátvételi művelet elindításával. Ez a művelet törli a tesztfeladat-átvétel által létrehozott virtuális gép tesztpéldányát.

```azurepowershell
$Job_TFOCleanup = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-AzRecoveryServicesAsrJob -Job $Job_TFOCleanup | Select State
```

```Output
State
-----
Succeeded
```

## <a name="fail-over-to-azure"></a>Feladatátvétel az Azure-ba

Feladatátvétel a virtuális gép egy adott helyreállítási pont.

```azurepowershell
$RecoveryPoints = Get-AzRecoveryServicesAsrRecoveryPoint -ReplicationProtectedItem $ReplicationProtectedItem

#The list of recovery points returned may not be sorted chronologically and will need to be sorted first, in order to be able to find the oldest or the latest recovery points for the virtual machine.
"{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[-1].RecoveryPointTime
```

```Output
CrashConsistent 4/24/2018 11:10:25 PM
```

```azurepowershell
#Start the fail over job
$Job_Failover = Start-AzRecoveryServicesAsrUnplannedFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[-1]

do {
        $Job_Failover = Get-AzRecoveryServicesAsrJob -Job $Job_Failover;
        sleep 30;
} while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))

$Job_Failover.State
```

```Output
Succeeded
```

Ha a feladatátvételi feladat sikeres, véglegesítheti a feladatátvételi műveletet.

```azurepowershell
$CommitFailoverJOb = Start-AzRecoveryServicesAsrCommitFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-AzRecoveryServicesAsrJob -Job $CommitFailoverJOb
```

```Output
Name             : 58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : CommitFailover
DisplayName      : Commit
ClientRequestId  : 10a95d6c-359e-4603-b7d9-b7ee3317ce94 ActivityId: 8751ada4-fc42-4238-8de6-a82618408fcf
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:50:58 AM
EndTime          : 4/25/2018 4:51:01 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   :
Tasks            : {Prerequisite check, Commit}
Errors           : {}
```

## <a name="reprotect-and-fail-back-to-the-source-region"></a>A forrásrégió újbóli védelme és visszavétele

Feladatátvétel után, ha készen áll az eredeti régióba való visszatérésre, indítsa el `Update-AzRecoveryServicesAsrProtectionDirection` a fordított replikációt a replikációval védett elemhez a parancsmag használatával.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage
```

```azurepowershell
#Use the recovery protection container, new cache storage account in West US and the source region VM resource group
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure
-ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId
```

Miután az újravédelem befejeződött, átadhathatja a fordított irányba, USA nyugati régiója és USA keleti régiója, és a feladat-vissza a forrásrégióba.

## <a name="disable-replication"></a>A replikálás letiltása

A parancsmaggal `Remove-AzRecoveryServicesAsrReplicationProtectedItem` letilthatja a replikációt.

```azurepowershell
Remove-AzRecoveryServicesAsrReplicationProtectedItem -ReplicationProtectedItem $ReplicatedItem
```

## <a name="next-steps"></a>További lépések

Tekintse meg az [Azure Site Recovery PowerShell-hivatkozást,](/powershell/module/az.RecoveryServices) hogy megtudja, hogyan hozhat létre más feladatokat, például helyreállítási tervek létrehozása és a helyreállítási tervek powershell-alapú feladatátvétel tesztelése.
