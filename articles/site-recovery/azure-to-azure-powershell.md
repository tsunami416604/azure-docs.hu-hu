---
title: Az Azure Site Recovery - telepítő és vészhelyreállítás Azure virtuális gépek az Azure PowerShell tesztelése |} Microsoft Docs
description: Ismerje meg, hogyan állíthat be az Azure Site Recovery Azure PowerShell használatával az Azure virtuális gépek vész-helyreállítási.
services: site-recovery
author: bsiva
manager: abhemraj
editor: raynew
ms.service: site-recovery
ms.topic: article
ms.date: 05/31/2018
ms.author: bsiva
ms.openlocfilehash: 3fa9ee27a1b9717d8011b7b46a1116f1f1ac1df5
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34716329"
---
# <a name="set-up-disaster-recovery-for-azure-virtual-machines-using-azure-powershell"></a>Állítsa be az Azure virtuális gépek az Azure PowerShell katasztrófa utáni helyreállítás

Ebben a cikkben láthatja, hogyan kell beállítania, és tesztelje az Azure virtuális gépek az Azure PowerShell katasztrófa utáni helyreállítás. 

Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> - Recovery Services-tároló létrehozása.
> - A tároló környezet beállítása a PowerShell-munkamenethez.
> - Készítse elő a tárolóban, az Azure virtuális gépek replikálást indítani.
> - Hozzon létre a hálózatok leképezését.
> - Virtuális gépek replikálásához storage-fiókok létrehozása.
> - Az Azure virtuális gépeket replikálhat a vész-helyreállítási helyreállítási régióban.
> - Feladatátvételi teszt végrehajtása, ellenőrzéséhez és karbantartása a feladatátvételi teszthez.
> - A helyreállítási régió feladatátvételt.

> [!NOTE]
> Nem minden forgatókönyv képességek a portálon keresztül elérhető Azure PowerShell keresztül érhetők el. A forgatókönyv képességek az Azure PowerShell jelenleg nem támogatott a következők:
> - Lehetővé teszi az Azure virtuális gépek, amelyek felügyelt lemezek replikálásához.
> - Annak megadása, hogy az összes lemez a virtuális gépen replikálni kell explicit módon az egyes lemezek, a virtuális gép megadása nélkül.  

## <a name="prerequisites"></a>Előfeltételek

Előkészületek:
- Ismernie kell a [forgatókönyv-architektúrát és az összetevőket](azure-to-azure-architecture.md).
- Minden összetevőre vonatkozóan tekintse át a [támogatási követelményeket](azure-to-azure-support-matrix.md).
- Verziót 5.7.0 vagy nagyobb a AzureRm PowerShell modul. Ha szeretné telepíteni vagy frissíteni az Azure PowerShell, folytassa a [útmutató az Azure PowerShell telepítése és konfigurálása](/powershell/azureps-cmdlets-docs).

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Jelentkezzen be a Microsoft Azure-előfizetés

Jelentkezzen be az Azure-előfizetéshez a Connect-AzureRmAccount parancsmaggal

```azurepowershell
Connect-AzureRmAccount
```
Válassza ki az Azure-előfizetését. A Get-AzureRmSubscription parancsmag segítségével juthatnak az Azure-előfizetéssel rendelkezik listáját. Válassza ki az Azure-előfizetés a Select-AzureRmSubscription parancsmaggal használható.

```azurepowershell
Select-AzureRmSubscription -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

## <a name="get-details-of-the-virtual-machines-to-be-replicated"></a>Ezzel a virtuális gépek replikálása az adatokat

Ebben a cikkben a példában a virtuális gép USA keleti régiójában fog kell replikált és 2 USA nyugati régiója régióban helyre. A replikált virtuális gép operációsrendszer-lemez, és egyetlen adatlemezt rendelkező virtuális gép. Az a virtuális gép nevét, a példában használt AzureDemoVM.

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

A lemezek, a virtuális gép beolvasása – részletek. A céllemez adatai később kezdődően Ha a virtuális gép replikálása fogja használni.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

Hozzon létre egy erőforráscsoportot a Recovery Services-tároló létrehozásához. 

> [!IMPORTANT]
> * A Recovery services-tároló és a virtuális gépeket védetté tétele folyamatban, különböző Azure helyen kell lennie.
> * Az erőforráscsoport a Recovery services-tároló és a virtuális gépeket védetté tétele folyamatban, az Azure különböző helyeken kell lennie.
> * A Recovery services-tároló és az erőforráscsoporthoz, amelyhez tartozik, az Azure ugyanazon a helyen lehet.
 
Ez a cikk a példában a védett virtuális gépet az USA keleti régiója régióban van. A megadott vész-helyreállítási helyreállítási régió, a 2. nyugati US régió. A recovery services-tároló és az erőforráscsoport, a tároló, mind a helyreállítási régióban (USA nyugati 2)

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
   
A Recovery services-tároló létrehozása. A Recovery Services-tároló a2aDemoRecoveryVault nevű az alábbi példában jön létre a 2. nyugati US régió.

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
## <a name="set-the-vault-context"></a>A tároló környezet beállítása

> [!TIP]
> Az Azure Site Recovery PowerShell modul (AzureRm.RecoveryServices.SiteRecovery modul) könnyen használható aliasok legtöbb parancsmagokat tartalmaz. A modul parancsmagjai formájában  *\<művelet >-**AzureRmRecoveryServicesAsr**\<objektum >* és egyenértékű aliast is, amely formájú  *\<Művelet >-**ASR**\<objektum >*. Ez a cikk a parancsmag aliasok olvasási könnyű használ.

Állítsa be a tároló környezetben használható a PowerShell-munkamenetben. Ehhez a tároló beállításai fájlt, és importálja a letöltött fájlt a PowerShell-munkamenetben a tároló környezet beállítása. 

Beállítása után további Azure Site Recovery a PowerShell-munkamenetben műveleteket a kijelölt tároló környezetében. 

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
## <a name="prepare-the-vault-to-start-replicating-azure-virtual-machines"></a>A tároló az Azure virtuális gépek replikálást indítani előkészítése

####<a name="1-create-a-site-recovery-fabric-object-to-represent-the-primarysource-region"></a>1. Hozzon létre egy Site Recovery háló objektumot képviselő primary(source) régió

A háló objektum a tárolóban az Azure-régiót jelöli. Az elsődleges háló objektum az Azure-régió, amelyek a tárolóba védett virtuális gépek jelölésére létrehozott háló objektum. Ez a cikk a példában a védett virtuális gépet az USA keleti régiója régióban van.

> [!NOTE]
> Az Azure Site Recovery-műveleteket aszinkron módon végrehajtása. Egy művelet kezdeményezésekor egy Azure Site Recovery-feladat nyújtják, és egy objektum követési feladatot ad vissza. A feldolgozással objektum követési legfrissebb állapotának beolvasása (Get-ASRJob) feladat, és a művelet állapotának figyelésére.

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
Ha több Azure-régiók származó virtuális gépek ugyanahhoz a tárolóhoz élveznek védelmet, hozzon létre az egyes források Azure-régió, egy háló objektum.

####<a name="2-create-a-site-recovery-fabric-object-to-represent-the-recovery-region"></a>2. A helyreállítási régió képviselő Site Recovery háló objektum létrehozása

A helyreállítási háló objektum az Azure-beli hely helyreállítási jelöli. Virtuális gépek a rendszer replikálja, és a helyreállítási terület a helyreállítási háló által képviselt (feladatátvételnél) helyre. A helyreállítás ebben a példában használt Azure-régió az USA nyugati régiója 2.

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

####<a name="3-create-a-site-recovery-protection-container-in-the-primary-fabric"></a>3. Az elsődleges háló a Site Recovery védelmi tároló létrehozása

A védelmi tároló egy olyan tároló, a replikált elemek belül a háló csoportosításához használt.

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
####<a name="4-create-a-site-recovery-protection-container-in-the-recovery-fabric"></a>4. A helyreállítási háló a Site Recovery védelmi tároló létrehozása

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

####<a name="5-create-a-replication-policy"></a>5. Replikációs házirend létrehozása

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
####<a name="6-create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container"></a>6. Hozzon létre egy elsődleges és a helyreállítási védelmi tároló között védelmitároló-leképezés

A védelmi tároló egyhez az elsődleges védelmet tárolóhoz a helyreállítási védelmi tároló és a replikációs házirend. Hozzon létre egy leképezést fogja használni virtuális gépeket replikálhat a védelmi tároló párjai közötti replikációs házirendet.

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

####<a name="7-create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover"></a>7. A feladat-visszavételi (egy feladatátvétel után a visszirányú replikálás) védelmitároló-leképezés létrehozása

Feladatátvétel után, amikor készen áll a kerüljön a feladatokat átadó virtuális gép vissza az eredeti Azure-régió, hogy feladat-visszavétel. Feladat-visszavételi, a feladatokat átadó virtuális gép visszirányú replikálása a sikertelen a régióban legyen, az eredeti terület felett. Visszirányú replikálás a szerepköröket, az eredeti területi és a helyreállítási régió kapcsoló. Az eredeti régió most lesz az új helyreállítási régió, pedig mi eredetileg volt a helyreállítási régió most az elsődleges régióban. A fordított replikáció védelmitároló-leképezés az eredeti és helyreállítási régiók kapcsolt szerepkörök jelöli.

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

## <a name="create-cache-storage-accounts-and-target-storage-accounts"></a>Gyorsítótár-tárfiókok és a cél tárfiókok létrehozása

A gyorsítótár storage-fiók egy standard szintű tárfiókot replikált virtuális gépként Azure ugyanabban a régióban. A gyorsítótár tárfiók segítségével történő replikációs módosításokat ideiglenesen, tartsa, mielőtt a módosítások átkerülnek a helyreállítást az Azure-régió. Ha szeretné (de nem kell megegyeznie) adjon meg másik gyorsítótármappa storage-fiókok a virtuális gépek különböző lemezei esetén.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$EastUSCacheStorageAccount = New-AzureRmStorageAccount -Name "a2acachestorage" -ResourceGroupName "A2AdemoRG" -Location 'East US' -SkuName Standard_LRS -Kind Storage
```

A virtuális gépek nem felügyelt lemezt használ a céloldali tárfiók a tárfiókok, amelyhez a virtuális gépek replikálva vannak a helyreállítási régióban. A céloldali tárfiók lehet vagy egy standard szintű tárfiókot, vagy a prémium szintű storage-fiók. Válassza ki, hogy a tárfiók szükséges milyen alapján az adatmódosítási arány (IO írási sebesség) a lemezek és az Azure Site Recovery támogatott forgalom határértékeit tárolási típusát.

```azurepowershell
#Create Target storage account in the recovery region. In this case a Standard Storage account
$WestUSTargetStorageAccount = New-AzureRmStorageAccount -Name "a2atargetstorage" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -SkuName Standard_LRS -Kind Storage

```

## <a name="create-network-mappings"></a>Hálózati hozzárendelések létrehozása

A hálózatleképezés az elsődleges régióban lévő virtuális hálózatok rendel a virtuális hálózatok a helyreállítási régióban. A hálózatra való leképezést a helyreállítási régió, egy virtuális gép által az elsődleges virtuális hálózatot a feladatátvétel az Azure virtuális hálózat határozza meg. Egy Azure virtuális hálózat csak egy Azure virtuális hálózatnak egy helyreállítási régióban rendelhetők.

- A helyreállítási régióban, így az Azure virtuális hálózat létrehozása

   ```azurepowershell
    #Create a Recovery Network in the recovery region
    $WestUSRecoveryVnet = New-AzureRmVirtualNetwork -Name "a2arecoveryvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.0.0.0/16"

    Add-AzureRmVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $WestUSRecoveryVnet -AddressPrefix "10.0.0.0/20" | Set-AzureRmVirtualNetwork

    $WestUSRecoveryNetwork = $WestUSRecoveryVnet.Id
   ```
- Az elsődleges virtuális hálózat (a virtuális hálózat, amely a virtuális gép csatlakozik) beolvasása
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
- Az elsődleges virtuális hálózat és a helyreállítási virtuális hálózat között hálózatra való leképezés létrehozása
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
- A fordított irányában (feladat-visszavétel) hálózatra való leképezés létrehozása
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

Az Azure virtuális gép replikálása.

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

A start replikációs művelet sikeres, ha virtuálisgép-adatokat a helyreállítási régió replikálódik. 

A replikálási folyamat egy példányát a replikálás alatt álló helyreállítási régióban a virtuális gép lemezei kezdetben összehangolásával kezdődik. Ebben a fázisban a kezdeti replikációs szakasz neve. 

Kezdeti replikálás után a replikáció áthelyezi a különbözeti szinkronizálási fázis. Ezen a ponton a virtuális gép védett, és a teszt feladatátvételi művelet is végezhető el rajta. Kezdeti replikáció befejezése után a replikált elemet képviselő a virtuális gép replikációs állapota a "Protected" állapotba kerül.

A védett replikációs elem rá vonatkozó információk lekérdezése figyelje a replikációs állapot és a virtuális gép replikációs állapotát.
```azurepowershell
 Get-ASRReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState ReplicationHealth
------------ --------------- -----------------
AzureDemoVM  Protected       Normal           
```

## <a name="perform-a-test-failover-validate-and-cleanup-test-failover"></a>Feladatátvételi teszt végrehajtása, ellenőrzéséhez és karbantartása a feladatátvételi teszt

Miután a virtuális gép replikálása elérte a védett állapotban, a teszt feladatátvételi művelet is végezhető el a virtuális gépen (a replikációval védett elemen a virtuális gép.)

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

Várja meg a teszt feladatátvételi művelet elvégzéséhez.
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
Ha a teszt feladatátvételi feladat sikeresen befejeződik, a teszt virtuális gép a feladatátvételt csatlakozhat, és a teszt feladatátvétel érvényesítése.

Tesztelés befejezése után a teszteléshez használt virtuális gép a feladatátvételt, karbantartása a tesztelési másolatot a karbantartás elindításával teszt feladatátvételi művelet. Ez a művelet törli a teszt másolatot a virtuális gép által a teszt feladatátvételhez létrehozott.

```azurepowershell
$Job_TFOCleanup = Start-ASRTestFailoverCleanupJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-ASRJob -Job $Job_TFOCleanup | Select State
```
```
State    
-----    
Succeeded
```

## <a name="failover-to-azure"></a>Feladatátvétel az Azure-bA

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

Ha feladatátvételt sikeresen, véglegesítése a a feladatátvételi műveletet.
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

A feladatátvétel után a térhet vissza az eredeti régió készen áll, ha a frissítés-AzureRmRecoveryServicesAsrProtectionDirection parancsmaggal replikációval védett elem visszirányú replikálás indítása.

## <a name="next-steps"></a>További lépések
Nézet a [Azure Site Recovery PowerShell-referencia ](https://docs.microsoft.com/powershell/module/AzureRM.RecoveryServices.SiteRecovery) megtudhatja, hogyan egyéb feladatokhoz, mint a helyreállítási terv létrehozása és tesztelése a feladatátvételi helyreállítási tervek a PowerShell segítségével végezheti el.
