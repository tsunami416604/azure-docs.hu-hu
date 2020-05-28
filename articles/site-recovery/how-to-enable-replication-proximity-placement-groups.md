---
title: Közelségben lévő elhelyezési csoportokban futó Azure-beli virtuális gépek replikálása
description: Megtudhatja, hogyan replikálhatja az Azure-beli virtuális gépeket Azure Site Recovery használatával a közelségi elhelyezési csoportokban.
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 05/25/2020
ms.openlocfilehash: 204ac3be46ac7ba0e1ea96e50379ca417b1299ce
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/26/2020
ms.locfileid: "83847633"
---
# <a name="replicate-azure-virtual-machines-running-in-proximity-placement-groups-to-another-region"></a>Közelségben lévő elhelyezési csoportokban futó Azure-beli virtuális gépek replikálása egy másik régióba

Ez a cikk azt ismerteti, hogyan replikálhatja, feladatátvételi és feladat-visszavételi pontokon futó virtuális gépeket egy másodlagos régióba helyezheti el egy közeli elhelyezési csoportban.

A [közelségi csoportok](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups-portal) egy Azure-beli virtuális gépek logikai csoportosítási funkciója, amellyel csökkentheti az alkalmazásaihoz tartozó, virtuális gépek közötti hálózati késést. Ha a virtuális gépek ugyanarra a közelségi elhelyezési csoportba vannak helyezve, fizikailag a lehető legközelebb helyezkednek el egymáshoz. A közelségi elhelyezési csoportok különösen hasznosak a késésre érzékeny munkaterhelések követelményeinek kielégítése érdekében.

## <a name="disaster-recovery-with-proximity-placement-groups"></a>Vész-helyreállítás földrajzi elhelyezési csoportokkal

Egy tipikus forgatókönyv esetében előfordulhat, hogy a virtuális gépek egy közeli elhelyezési csoportban futnak, hogy elkerülje az alkalmazás különböző szintjei közötti hálózati késést. Habár ez az alkalmazás optimális hálózati késését biztosítja, ezeket az alkalmazásokat a Site Recovery bármely régió szintű meghibásodás esetén szeretné biztosítani. Site Recovery replikálja az egyik régió adatait egy másik Azure-régióba, és felveszi a számítógépeket a vész-helyreállítási régióban a feladatátvételi esemény során.

## <a name="considerations"></a>Megfontolandó szempontok

- A legjobb megoldás az, ha a virtuális gépeket egy közelségi elhelyezési csoportba helyezi a feladatátvétel/feladat-visszavétel során. Ha azonban a virtuális gép a feladatátvétel/feladat-visszavétel során nem tud megjelenni a közelségben, akkor a feladatátvétel/feladat-visszavétel továbbra is megtörténik, és a virtuális gépeket a közelségi elhelyezési csoporton kívül hozza létre a rendszer.
-  Ha egy rendelkezésre állási csoport egy közelségi helyhez van rögzítve, és a rendelkezésre állási csoportban lévő feladatátvételi/feladat-visszavételi virtuális gépeken foglalási korlátozás van megadva, akkor a virtuális gépek a rendelkezésre állási csoporton és a közelségi elhelyezési csoportban kívül is létrejönnek.
-  Nem felügyelt lemezek esetén nem támogatott a közelségi csoportok Site Recovery.

## <a name="prerequisites"></a>Előfeltételek

1. Győződjön meg arról, hogy rendelkezik a Azure PowerShell az modulhoz. Ha Azure PowerShellt kell telepítenie vagy frissítenie, a [Azure PowerShell telepítéséhez és konfigurálásához](https://docs.microsoft.com/powershell/azure/install-az-ps)kövesse az alábbi útmutatót.

## <a name="set-up-site-recovery-for-virtual-machines-in-proximity-placement-group"></a>Virtual Machines Site Recovery beállítása a közelségi elhelyezési csoportban

### <a name="azure-to-azure"></a>Azure – Azure

1. [Jelentkezzen](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#sign-in-to-your-microsoft-azure-subscription) be a fiókjába, és állítsa be az előfizetését.
2. Szerezze be a replikálni kívánt virtuális gép részleteit az [itt](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#get-details-of-the-virtual-machine-to-be-replicated)leírtak szerint.
3. [Hozza létre](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-recovery-services-vault) a Recovery Services-tárolót, és [állítsa be](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#set-the-vault-context) a tár környezetét.
4. Készítse elő a tárolót a replikálási virtuális gép elindításához. Ez magában foglalja a [Service Fabric-objektum](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-site-recovery-fabric-object-to-represent-the-primary-source-region) létrehozását az elsődleges és a helyreállítási régiók esetében is.
5. [Hozzon létre](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-site-recovery-protection-container-in-the-primary-fabric) egy site Recovery védelmi tárolót mind az elsődleges, mind a helyreállítási hálóhoz.
6. [Hozzon létre](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-replication-policy) egy replikációs házirendet.
7. Hozzon létre egy védelmi tárolót az elsődleges és a helyreállítási védelmi tároló között az [alábbi](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container) lépésekkel és egy védelmi tároló hozzárendelésével az [itt](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover)említett feladat-visszavételhez.
8. A cache Storage-fiók létrehozásához kövesse [az](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-cache-storage-account-and-target-storage-account) alábbi lépéseket.
9. Hozza létre a szükséges hálózati leképezéseket az [itt](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-network-mappings)leírtak szerint.
10. Az Azure-beli virtuális gép felügyelt lemezekkel való replikálásához használja az alábbi PowerShell-parancsmagot: 

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId = $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id ` -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType ` -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType -RecoveryProximityPlacementGroupId $recPpg.Id

# Data disk
$datadiskId1 = $vm.StorageProfile.DataDisks[0].ManagedDisk.Id
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id ` -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType ` -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.

$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.

$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $recPpg.Id
```
Miután a replikálás megkezdése sikeres volt, a rendszer replikálja a virtuális gépeket a helyreállítási régióba.

A replikálási folyamat kezdetben a helyreállítási régióban lévő virtuális gép replikáló lemezének egy másolatát indítja el. Ezt a fázist a kezdeti replikálási fázisnak nevezzük.

A kezdeti replikálás befejeződése után a replikálás a különbözeti szinkronizálási fázisba kerül. Ezen a ponton a virtuális gép védett, és egy teszt feladatátvételi művelet is elvégezhető rajta. A virtuális gépet jelképező replikált elem replikációs állapota a kezdeti replikálás befejeződése után védett állapotba kerül.

A virtuális gép replikációs állapotának és replikálási állapotának figyeléséhez a hozzá tartozó replikációval védett elem részleteit kell beszereznie. 

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

11. A feladatátvételi teszt végrehajtásához kövesse [az alábbi](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#do-a-test-failover-validate-and-cleanup-test-failover) lépéseket.
12. A feladatátvételhez kövesse az [itt](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#fail-over-to-azure)említett lépéseket.
13. Az alábbi PowerShell-parancsmag használatával a forrás-régióba történő ismételt védelemmel és feladat-visszavételsel

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage


#Use the recovery protection container, new cache storage account in West US and the source region VM resource group 
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure -ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId -RecoveryProximityPlacementGroupId $vm.ProximityPlacementGroup.Id
```
14. A replikáció letiltásához kövesse az [alábbi lépéseket.](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#disable-replication)

### <a name="vmware-to-azure"></a>VMware – Azure

1. Győződjön meg arról, hogy [előkészíti a helyszíni VMware-kiszolgálókat](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises) az Azure-ba való vész-helyreállításra.
2. Jelentkezzen be a fiókjába, és állítsa be az előfizetését az [itt](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#log-into-azure)megadott módon.
3. [Hozzon](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#set-up-a-recovery-services-vault) létre egy Recovery Services-tárolót, és [állítsa be a tár környezetét](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#set-the-vault-context).
4. A tár regisztrációjának [ellenőrzése](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#validate-vault-registration) .
5. [Hozzon létre](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#create-a-replication-policy) egy replikációs házirendet.
6. [Adjon hozzá](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#add-a-vcenter-server-and-discover-vms) egy vCenter-kiszolgálót, és fedezze fel a virtuális gépeket, és [hozzon létre](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#create-storage-accounts-for-replication) tárolási fiókokat a replikáláshoz
7. A VMware Virtual Machines replikálásához tekintse meg a részleteket, és kövesse az alábbi PowerShell-parancsmagot:

```azurepowershell
#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg" 

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1 using the Az.RecoveryServices module 2.0.0 onwards to replicate to managed disks
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" -RecoveryProximityPlacementGroupId $recPpg.Id
```
8. A Get-ASRReplicationProtectedItem parancsmaggal megtekintheti a virtuális gép replikációs állapotát és a replikáció állapotát.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
9. A feladatátvételi beállítások konfigurálásához [kövesse az alábbi lépéseket.](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#configure-failover-settings)
10. [Futtasson](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#run-a-test-failover) feladatátvételi tesztet. 
11. Feladatátvétel az Azure-ba az [alábbi](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#fail-over-to-azure) lépésekkel.

### <a name="hyper-v-to-azure"></a>Hyper-V – Azure

1. Győződjön meg arról, hogy [előkészíti a helyszíni Hyper-V-kiszolgálókat](https://docs.microsoft.com/azure/site-recovery/hyper-v-prepare-on-premises-tutorial) a vész-helyreállításra az Azure-ban.
2. [Jelentkezzen](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-1-sign-in-to-your-azure-account) be az Azure-ba.
3. [Állítsa](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-2-set-up-the-vault) be a tárolót, és [állítsa be](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-3-set-the-recovery-services-vault-context) a Recovery Services-tároló környezetét.
4. [Hozzon létre](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-4-create-a-hyper-v-site) egy Hyper-V-helyet.
5. [Telepítse](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-5-install-the-provider-and-agent) a szolgáltatót és az ügynököt.
6. [Hozzon létre](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-6-create-a-replication-policy) egy replikációs házirendet.
7. Engedélyezze a replikálást az alábbi lépések használatával – 
    
    a. Kérje le a védelemmel ellátni kívánt virtuális géphez tartozó védhető elem beolvasását az alábbiak szerint:

    ```azurepowershell
    $VMFriendlyName = "Fabrikam-app"          #Name of the VM
    $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
    ```
    b. A virtuális gép megóvása. Ha a védeni kívánt virtuális gépnek több lemeze is van, akkor az operációs rendszer lemezét a OSDiskName paraméter használatával kell megadni.
    
    ```azurepowershell
    $OSType = "Windows"          # "Windows" or "Linux"
    $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId   $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID -RecoveryProximityPlacementGroupId $recPpg.Id
    ```
    c. Várja meg, hogy a virtuális gépek a kezdeti replikálás után elérjék a védett állapotot. Ez eltarthat egy ideig, attól függően, hogy milyen tényezőket kell replikálni, valamint az elérhető upstream sávszélességet az Azure-ba. Ha védett állapot van érvényben, a rendszer a következő módon frissíti a feladatok állapotát és a StateDescription: 
    
    ```azurepowershell
    $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob
    $DRjob | Select-Object -ExpandProperty State

    $DRjob | Select-Object -ExpandProperty StateDescription
    ```
    d. Frissítse a helyreállítási tulajdonságokat (például a virtuálisgép-szerepkör méretét) és az Azure-hálózatot, amelyhez a virtuális gép hálózati adapterét a feladatátvételt követően csatlakoztatni szeretné.

    ```azurepowershell
    $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

    $VMFriendlyName = "Fabrikam-App"

    $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

    $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

    $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

    $UpdateJob | Select-Object -ExpandProperty state

    Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state
    ```
8. Futtasson [feladatátvételi](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-8-run-a-test-failover)tesztet.


## <a name="next-steps"></a>Következő lépések

A VMware-ből az Azure-ba történő ismételt védelem és feladat-visszavétel végrehajtásához kövesse az [itt](https://docs.microsoft.com/azure/site-recovery/vmware-azure-prepare-failback)ismertetett lépéseket.

A Hyper-V-ről az Azure-ba történő feladatátvétel végrehajtásához kövesse az [itt](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) ismertetett lépéseket, és hajtsa végre a feladat-visszavételt, kövesse az [itt](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-failback)ismertetett lépéseket.

További információ: [feladatátvétel site Recoveryban](site-recovery-failover.md).
