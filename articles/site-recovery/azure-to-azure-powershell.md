---
title: Az Azure Site Recovery - beállítása és tesztelése a vészhelyreállítás Azure virtuális gépek Azure PowerShell-lel |} A Microsoft Docs
description: Ismerje meg, hogyan állítható be az Azure Site Recovery az Azure PowerShell használatával Azure-beli virtuális gépek vészhelyreállítása.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/02/2018
ms.author: sutalasi
ms.openlocfilehash: 1d0240b8635230c0fc6a7e577914ccaace36407b
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/19/2018
ms.locfileid: "49457251"
---
# <a name="set-up-disaster-recovery-for-azure-virtual-machines-using-azure-powershell"></a>Azure PowerShell-lel az Azure virtuális gépek vészhelyreállításának beállítása


Ebben a cikkben bemutatjuk, hogyan tesztelheti, majd a vészhelyreállítás Azure virtuális gépek Azure PowerShell-lel.

Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> - Recovery Services-tároló létrehozása.
> - A tárolási környezet beállítása a PowerShell-munkamenetben.
> - Készítse elő a tároló az Azure-beli virtuális gépek replikálásának megkezdéséhez.
> - Hozzon létre hálózati leképezésben.
> - Hozzon létre a tárfiókok, virtuális gépeket replikálhat.
> - Azure-beli virtuális gépek replikálása egy vész-helyreállítási régióban.
> - Feladatátvételi teszt végrehajtása, ellenőrzés, és a feladatátvételi teszt utáni karbantartás.
> - Feladatátvétel helyreállítási régióban.

> [!NOTE]
> Előfordulhat, hogy nem minden eset képességek a portálon keresztül elérhető Azure Powershellen keresztül érhető el. Az Azure Powershellen keresztül jelenleg nem támogatott forgatókönyv funkciói a következők:
> - Adja meg, hogy az összes lemez a virtuális gépen replikálni kell a virtuális gép minden lemezét explicit módon megadása nélkül lehetővé teszi.  

## <a name="prerequisites"></a>Előfeltételek

Előkészületek:
- Ismernie kell a [forgatókönyv-architektúrát és az összetevőket](azure-to-azure-architecture.md).
- Minden összetevőre vonatkozóan tekintse át a [támogatási követelményeket](azure-to-azure-support-matrix.md).
- 5.7.0 verzióval rendelkezik, vagy nagyobb, mint az AzureRm PowerShell-modul. Ha telepíteni vagy frissíteni az Azure PowerShell-lel van szüksége, kövesse ezt [útmutató az Azure PowerShell telepítése és konfigurálása](/powershell/azureps-cmdlets-docs).

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Jelentkezzen be a Microsoft Azure-előfizetés

Jelentkezzen be az Azure-előfizetéshez a Connect-AzureRmAccount parancsmag használatával

```azurepowershell
Connect-AzureRmAccount
```
Válassza ki az Azure-előfizetését. A Get-AzureRmSubscription parancsmaghoz használatával hozzáféréssel rendelkezik az Azure-előfizetések listájának beolvasása. Válassza ki a Select-AzureRmSubscription parancsmag használata az Azure-előfizetést.

```azurepowershell
Select-AzureRmSubscription -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

## <a name="get-details-of-the-virtual-machines-to-be-replicated"></a>Részletek a replikálni kívánt virtuális gép(ek)

Ebben a cikkben a példában egy virtuális gépet az USA keleti régiójában fog replikálja és az USA 2. nyugati régió helyreállítása. A virtuális gép replikálása folyamatban az operációsrendszer-lemezt és egy adatlemezt egy virtuális gépet. A virtuális gép, a példában használt név AzureDemoVM.

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

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

Hozzon létre egy erőforráscsoportot, amelyben létrehozza a Recovery Services-tároló.

> [!IMPORTANT]
> * A Recovery services-tároló és a virtuális gépek védett, különböző Azure-helyen kell lennie.
> * Az erőforráscsoport, a Recovery services-tárolót, és a virtuális gépek védett, különböző Azure-helyen kell lennie.
> * A Recovery services-tárolót, és az erőforráscsoportot, amelyhez tartozik, ugyanazon Azure-helyen lehet.

A példában ez a cikk a védett virtuális gép van, az USA keleti régiójában. A katasztrófa utáni helyreállításra kijelölt helyreállítási régió az USA 2. nyugati régió. A recovery services-tárolót, és az erőforráscsoportot, a tároló mind a helyreállítási régióban (USA nyugati RÉGIÓJA 2)

```azurepowershell
#Create a resource group for the recovery services vault in the recovery Azure region
New-AzureRmResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"
```
```
ResourceGroupName : a2ademorecoveryrg
Location          : westus2
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg
```

Hozzon létre egy Recovery services-tárolót. Recovery Services-tároló nevű a2aDemoRecoveryVault az alábbi példában az USA 2. nyugati régióban jön.

```azurepowershell
#Create a new Recovery services vault in the recovery region
$vault = New-AzureRmRecoveryServicesVault -Name "a2aDemoRecoveryVault" -ResourceGroupName "a2ademorecoveryrg" -Location "West US 2"

Write-Output $vault
```
```
Name              : a2aDemoRecoveryVault
ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoRecoveryVault
Type              : Microsoft.RecoveryServices/vaults
Location          : westus2
ResourceGroupName : a2ademorecoveryrg
SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```
## <a name="set-the-vault-context"></a>A tárolási környezet beállítása

> [!TIP]
> A legtöbb parancsmag könnyen használható aliasok az Azure Site Recovery PowerShell modul (AzureRm.RecoveryServices.SiteRecovery modul) tartalmaz. A modul parancsmagjai utat  *\<művelet >-**: AzureRmRecoveryServicesAsr**\<objektum >* és egyenértékű aliast is beállíthat, amely formájában  *\<Művelet >-**ASR**\<objektum >*. Ebben a cikkben a parancsmag aliasok átláthatóbbá tétele.

A tárolási környezet használatra beállítása a PowerShell-munkamenetben. Ehhez töltse le a tároló beállításait, és importálni a letöltött fájlt a PowerShell-munkamenetben, a tárolási környezet beállításához.

Beállítása után a PowerShell-munkamenetben későbbi Azure Site Recovery-műveletek a kiválasztott tár környezetében történik.

 ```azurepowershell
#Download the vault settings file for the vault.
$Vaultsettingsfile = Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteRecovery -Path C:\users\user\Documents\

#Import the downloaded vault settings file to set the vault context for the PowerShell session.
Import-AzureRmRecoveryServicesAsrVaultSettingsFile -Path $Vaultsettingsfile.FilePath

```
```
ResourceName         ResourceGroupName ResourceNamespace          ResouceType
------------         ----------------- -----------------          -----------
a2aDemoRecoveryVault a2ademorecoveryrg Microsoft.RecoveryServices Vaults     
```

```azurepowershell
#Delete the downloaded vault settings file
Remove-Item -Path $Vaultsettingsfile.FilePath
```
## <a name="prepare-the-vault-to-start-replicating-azure-virtual-machines"></a>A tároló replikáljon az Azure-beli virtuális gépek előkészítése

### <a name="create-a-site-recovery-fabric-object-to-represent-the-primary-source-region"></a>Hozzon létre egy Site Recovery fabric objektumot képviselő az elsődleges (forrás) régióban

A fabric objektum a tároló egy Azure-régiót jelöli. Az elsődleges fabric objektumot képviselő tartozó virtuális gépek védelméről a tároló az Azure-régióban jön létre. A példában ez a cikk a védett virtuális gép van, az USA keleti régiójában.

- Régiónként csak egy hálóhoz objektum lehet létrehozni. 
- Ha korábban engedélyezte az Azure Portal virtuális gép Site Recovery-replikációja, a Site Recovery automatikusan létrehoz egy fabric objektumot. A fabric objektum létezik, régió, ha nem hozható létre egy újat.


A Kezdés előtt vegye figyelembe, hogy a Site Recovery-műveletek aszinkron módon vannak végrehajtva. Egy művelet kezdeményezésekor az Azure Site Recovery-feladatok elküldésekor, és a egy feladat nyomon követése az objektumot ad vissza. Használja a nyomkövetési objektum feladat, legfrissebb állapotának beolvasása a feladat (Get-ASRJob), és a művelet állapotának figyelésére.

```azurepowershell
#Create Primary ASR fabric
$TempASRJob = New-ASRFabric -Azure -Location 'East US'  -Name "A2Ademo-EastUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State

$PrimaryFabric = Get-AsrFabric -Name "A2Ademo-EastUS"
```
Ha vannak védett virtuális gépek több Azure-régióban ugyanazzal a tárral, egy háló objektumot hoz létre minden forrás az Azure-régióban.

### <a name="create-a-site-recovery-fabric-object-to-represent-the-recovery-region"></a>Hozzon létre egy Site Recovery fabric objektumot képviselő helyreállítási régióban

A helyreállítási fabric objektum képviseli a helyreállítás az Azure-helyen. A virtuális gépeket replikálja, és a helyreállítási háló által képviselt helyreállítási régióban (feladatátvétel) esetén helyre. A helyreállítás ebben a példában használt Azure-régió az USA 2. nyugati.

```azurepowershell
#Create Recovery ASR fabric
$TempASRJob = New-ASRFabric -Azure -Location 'West US 2'  -Name "A2Ademo-WestUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State

$RecoveryFabric = Get-AsrFabric -Name "A2Ademo-WestUS"

```

### <a name="create-a-site-recovery-protection-container-in-the-primary-fabric"></a>A Site Recovery védelmi tároló létrehozása elsődleges-hálóban

A védelmi tároló egy olyan replikált elemek a háló belül csoportosítására használhatók, tároló.

```azurepowershell
#Create a Protection container in the primary Azure region (within the Primary fabric)
$TempASRJob = New-AzureRmRecoveryServicesAsrProtectionContainer -InputObject $PrimaryFabric -Name "A2AEastUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

Write-Output $TempASRJob.State

$PrimaryProtContainer = Get-ASRProtectionContainer -Fabric $PrimaryFabric -Name "A2AEastUSProtectionContainer"
```
### <a name="create-a-site-recovery-protection-container-in-the-recovery-fabric"></a>A helyreállítási hálóban a Site Recovery védelmi tároló létrehozása

```azurepowershell
#Create a Protection container in the recovery Azure region (within the Recovery fabric)
$TempASRJob = New-AzureRmRecoveryServicesAsrProtectionContainer -InputObject $RecoveryFabric -Name "A2AWestUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"

Write-Output $TempASRJob.State

$RecoveryProtContainer = Get-ASRProtectionContainer -Fabric $RecoveryFabric -Name "A2AWestUSProtectionContainer"
```

### <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

```azurepowershell
#Create replication policy
$TempASRJob = New-ASRPolicy -AzureToAzure -Name "A2APolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State

$ReplicationPolicy = Get-ASRPolicy -Name "A2APolicy"
```
### <a name="create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container"></a>Hozzon létre egy között az elsődleges és a helyreállítási védelmi tároló védelmitároló-leképezés

Egy védelmitároló-leképezés az elsődleges védelmi tároló egy helyreállítási védelmi tároló és a egy replikációs szabályzatot hozzárendeli. Hozzon létre minden egyes virtuális gépeket replikálhat a védelmi tároló két között fogjuk replikációs házirend egy hozzárendelését.

```azurepowershell
#Create Protection container mapping between the Primary and Recovery Protection Containers with the Replication policy
$TempASRJob = New-ASRProtectionContainerMapping -Name "A2APrimaryToRecovery" -Policy $ReplicationPolicy -PrimaryProtectionContainer $PrimaryProtContainer -RecoveryProtectionContainer $RecoveryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State

$EusToWusPCMapping = Get-ASRProtectionContainerMapping -ProtectionContainer $PrimaryProtContainer -Name "A2APrimaryToRecovery"
```

### <a name="create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover"></a>Hozzon létre egy feladat-visszavételhez (egy feladatátvétel után a visszirányú replikálás) védelmitároló-leképezés

Amikor elkészült, ahhoz, hogy a feladatátviteli virtuális géphez vissza az eredeti Azure-régió, feladat-visszavételt a feladatátvételt. Történő feladat-visszavételhez, a feladatátviteli virtuális géphez fordított replikálódnak az a régió az eredeti régióba keresztül. Visszirányú replikálás kapcsoló a szerepkörök az eredeti régióban és a helyreállítási régióban. Az eredeti régió mostantól az új helyreállítási régióban, és mi eredetileg volt a helyreállítási régióban most lesz az elsődleges régióba. A visszirányú replikálás védelmitároló-leképezés a kapcsolt szerepkörök eredeti és helyreállítási régiót jelöli.

```azurepowershell
#Create Protection container mapping (for failback) between the Recovery and Primary Protection Containers with the Replication policy
$TempASRJob = New-ASRProtectionContainerMapping -Name "A2ARecoveryToPrimary" -Policy $ReplicationPolicy -PrimaryProtectionContainer $RecoveryProtContainer -RecoveryProtectionContainer $PrimaryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

## <a name="create-cache-storage-accounts-and-target-storage-accounts"></a>Gyorsítótárfiók(ok) és a céltárfiók létrehozása

A gyorsítótárfiók egy standard szintű tárfiókot a replikált virtuális gép is az azonos Azure-régióban. A gyorsítótárfiók segítségével átmenetileg, tartsa replikációs módosításokat, mielőtt a módosítások átkerülnek a helyreállítási Azure-régióban. Dönthet úgy (de nem kell) adja meg a virtuális gép különböző lemezek különböző gyorsítótárfiókok.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$EastUSCacheStorageAccount = New-AzureRmStorageAccount -Name "a2acachestorage" -ResourceGroupName "A2AdemoRG" -Location 'East US' -SkuName Standard_LRS -Kind Storage
```

A virtuális gépek **nem használ felügyelt lemezeket**, a célként megadott tárfiók a tárfiók(ok) a helyreállítási régióban, amelyhez a rendszer a virtuális gép lemezét replikálja. A célként megadott tárfiók vagy a standard szintű tárfiókot, vagy a premium storage-fiók lehet. Válassza ki a storage-fiók szükséges típusú adatváltozási sebesség (i/o írási arány) a lemezek és a tárolási típust az Azure Site Recovery által támogatott lemorzsolódási korlátai alapján.

```azurepowershell
#Create Target storage account in the recovery region. In this case a Standard Storage account
$WestUSTargetStorageAccount = New-AzureRmStorageAccount -Name "a2atargetstorage" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -SkuName Standard_LRS -Kind Storage

```

## <a name="create-network-mappings"></a>Hálózatleképezések létrehozása

A hálózatleképezés az elsődleges régióban lévő virtuális hálózatok rendel a virtuális hálózatok a helyreállítási régióban. A hálózatleképezést a helyreállítási régióban, amely az elsődleges virtuális hálózat egy virtuális gép feladatátvételt kell az Azure virtuális hálózat határozza meg. Egy Azure virtuális hálózat csak egy Azure virtuális hálózatnak egy helyreállítási régióban is rendelhető.

- A feladatátvétel helyreállítási régióban hozza létre Azure-beli virtuális hálózathoz

   ```azurepowershell
    #Create a Recovery Network in the recovery region
    $WestUSRecoveryVnet = New-AzureRmVirtualNetwork -Name "a2arecoveryvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.0.0.0/16"

    Add-AzureRmVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $WestUSRecoveryVnet -AddressPrefix "10.0.0.0/20" | Set-AzureRmVirtualNetwork

    $WestUSRecoveryNetwork = $WestUSRecoveryVnet.Id
   ```
- Az elsődleges virtuális hálózat (a virtuális hálózattal, a virtuális gép csatlakozik) beolvasása
   ```azurepowershell
    #Retrieve the virtual network that the virtual machine is connected to

    #Get first network interface card(nic) of the virtual machine
    $SplitNicArmId = $VM.NetworkProfile.NetworkInterfaces[0].Id.split("/")

    #Extract resource group name from the ResourceId of the nic
    $NICRG = $SplitNicArmId[4]

    #Extract resource name from the ResourceId of the nic
    $NICname = $SplitNicArmId[-1]

    #Get network interface details using the extracted resource group name and resourec name
    $NIC = Get-AzureRmNetworkInterface -ResourceGroupName $NICRG -Name $NICname

    #Get the subnet ID of the subnet that the nic is connected to
    $PrimarySubnet = $NIC.IpConfigurations[0].Subnet

    # Extract the resource ID of the Azure virtual network the nic is connected to from the subnet ID
    $EastUSPrimaryNetwork = (Split-Path(Split-Path($PrimarySubnet.Id))).Replace("\","/")  
   ```
- Az elsődleges virtuális hálózat és a helyreállítási virtuális hálózat közötti hálózatleképezést létrehozása
   ```azurepowershell
    #Create an ASR network mapping between the primary Azure virtual network and the recovery Azure virtual network
    $TempASRJob = New-ASRNetworkMapping -AzureToAzure -Name "A2AEusToWusNWMapping" -PrimaryFabric $PrimaryFabric -PrimaryAzureNetworkId $EastUSPrimaryNetwork -RecoveryFabric $RecoveryFabric -RecoveryAzureNetworkId $WestUSRecoveryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-ASRJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
    Write-Output $TempASRJob.State

   ```
- A fordított irányát (feladat-visszavétel) hálózatleképezés létrehozása
    ```azurepowershell
    #Create an ASR network mapping for failback between the recovery Azure virtual network and the primary Azure virtual network
    $TempASRJob = New-ASRNetworkMapping -AzureToAzure -Name "A2AWusToEusNWMapping" -PrimaryFabric $RecoveryFabric -PrimaryAzureNetworkId $WestUSRecoveryNetwork -RecoveryFabric $PrimaryFabric -RecoveryAzureNetworkId $EastUSPrimaryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-ASRJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
    Write-Output $TempASRJob.State
    ```

## <a name="replicate-azure-virtual-machine"></a>Azure virtuális gépek replikálása

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

# Data disk
$datadiskId1  = $vm.StorageProfile.DataDisks[0].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[0]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[0]. StorageAccountType

$DataDisk1ReplicationConfig  = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId1 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig


#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId

```

Az Azure-beli virtuális gép replikálása **nem felügyelt lemezek**.

```azurepowershell
#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#Disk replication configuration for the OS disk
$OSDiskReplicationConfig = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $OSDiskVhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Disk replication configuration for data disk
$DataDisk1ReplicationConfig = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $DataDisk1VhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzureRmResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.  
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-ASRJob -Job $TempASRJob
}


#Check if the Job completed successfully. The updated job state of a successfuly completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

A kezdő replikációs művelet sikeres, ha a rendszer a helyreállítási régióba replikálja a virtuális gépek adatainak.

A replikálási folyamat elindítja a kezdeti beültetés egy példányát a replikáló lemez a virtuális gép a helyreállítási régióban. Ebben a fázisban a kezdeti replikáció fázis neve.

Kezdeti replikálás befejezése után a különbözeti szinkronizálási fázis replikációs helyezi át. Ezen a ponton a virtuális gép védett, és a egy teszt feladatátvételi művelet végezhető rajta. Kezdeti replikálás befejezése után a replikált elemet képviselő a virtuális gép replikációs állapota "Védett" állapotba kerül.

A replikációs állapotot és a virtuális gép replikációs állapotát figyeli a hozzá tartozó replikációval védett elem részleteinek beolvasása közben.
```azurepowershell
 Get-ASRReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState ReplicationHealth
------------ --------------- -----------------
AzureDemoVM  Protected       Normal           
```

## <a name="perform-a-test-failover-validate-and-cleanup-test-failover"></a>Feladatátvételi teszt végrehajtása, ellenőrzés, és a feladatátvételi teszt utáni karbantartás

A virtuális gép replikálása elérte a védett állapotba, ha a teszt feladatátvételi művelet is elvégezhető a virtuális gépen (a virtuális gép replikációval védett elemen.)

```azurepowershell
#Create a seperate network for test failover (not connected to my DR network)
$TFOVnet = New-AzureRmVirtualNetwork -Name "a2aTFOvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.3.0.0/16"

Add-AzureRmVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $TFOVnet -AddressPrefix "10.3.0.0/20" | Set-AzureRmVirtualNetwork

$TFONetwork= $TFOVnet.Id
```

Feladatátvételi teszt végrehajtása.
```azurepowershell
$ReplicationProtectedItem = Get-ASRReplicationProtectedItem -FriendlyName "AzureDemoVM" -ProtectionContainer $PrimaryProtContainer

$TFOJob = Start-ASRTestFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -AzureVMNetworkId $TFONetwork -Direction PrimaryToRecovery


```

Várjon, amíg a teszt feladatátvételi művelet végrehajtásához.
```azurepowershell
Get-ASRJob -Job $TFOJob
```

```
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
Ha a teszt feladatátvételi feladat sikeresen befejeződött, a teszt virtuális gép feladatátvétele csatlakozhat, és a teszt feladatátvétel érvényesítése.

Tesztelés befejezése után a teszteléshez használt virtuális gép feladatátvétele a távolítsa el a tesztelési másolatot a tisztítás elindításával tesztelheti a feladatátvételi művelet. Ez a művelet törli a tesztelési másolatot a virtuális gép feladatátvételi teszt által létrehozott.

```azurepowershell
$Job_TFOCleanup = Start-ASRTestFailoverCleanupJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-ASRJob -Job $Job_TFOCleanup | Select State
```
```
State    
-----    
Succeeded
```

## <a name="failover-to-azure"></a>Feladatátvétel az Azure-ba

Feladatátvétel a virtuális gép egy adott helyreállítási pontot.

```azurepowershell
$RecoveryPoints = Get-ASRRecoveryPoint -ReplicationProtectedItem $ReplicationProtectedItem

#The list of recovery points returned may not be sorted chronologically and will need to be sorted first, in order to be able to find the oldest or the latest recovery points for the virtual machine.
"{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[-1].RecoveryPointTime
```
```
CrashConsistent 4/24/2018 11:10:25 PM
```


```azurepowershell
#Start the failover job
$Job_Failover = Start-ASRUnplannedFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[-1]

do {
        $Job_Failover = Get-ASRJob -Job $Job_Failover;
        sleep 30;
} while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))

$Job_Failover.State
```
```
Succeeded
```

Miután a feladatátvétel sikeresen véglegesítheti a feladatátvételi műveletet.
```azurepowershell
$CommitFailoverJOb = Start-ASRCommitFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-ASRJOb -Job $CommitFailoverJOb
```

```
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

A feladatátvétel után a amikor elkészült, lépjen vissza az eredeti régióban, az Update-AzureRmRecoveryServicesAsrProtectionDirection parancsmag segítségével replikációval védett elem visszirányú replikálás indítása.

## <a name="next-steps"></a>További lépések
Nézet a [Azure Site Recovery PowerShell-referencia ](https://docs.microsoft.com/powershell/module/AzureRM.RecoveryServices.SiteRecovery) megtudhatja, hogyan hajthat végre egyéb feladatok, például a helyreállítási tervek létrehozása és tesztelése a Powershellen keresztül a helyreállítási terv feladatátvételét.
