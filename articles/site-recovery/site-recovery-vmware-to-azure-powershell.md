---
title: "Replikálásához és feladatátvételéhez VMware virtuális gépeket az Azure-ban az Azure Site Recovery PowerShell használatával |} Microsoft Docs"
description: "Replikálásához és feladatátvételéhez VMware virtuális gépeket az Azure-ban az Azure Site Recovery PowerShell használatával"
services: site-recovery
documentationcenter: 
author: bsiva
manager: abhemraj
editor: raynew
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/02/2018
ms.author: bsiva
ms.openlocfilehash: ee4847a61392a8eacde82ea62c3812d601b489f3
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/13/2018
---
# <a name="replicate-and-failover-vmware-virtual-machines-to-azure-using-azure-site-recovery-powershell"></a>Replikálásához és feladatátvételéhez VMware virtuális gépeket az Azure-ban az Azure Site Recovery PowerShell használatával

Ebből a cikkből látni, hogyan replikáljon és feladatátvételi VMware virtuális gépeket az Azure-ban az Azure PowerShell használatával. 

Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> - Recovery Services-tároló létrehozása.
> - A tároló környezet beállítása.
> - Ellenőrizze, hogy a konfigurációs kiszolgáló és a folyamat kiszolgálók kibővítési regisztrálva van a tárolóban.
> - Hozzon létre egy replikációs házirendet, és képezze le azt a konfigurációs kiszolgálón való használatra.
> - Vegye fel a vCenter-kiszolgáló és a VMware virtuális gépek észlelése.
> - Virtuális gépek replikálásához storage-fiókok létrehozása.
> - VMware virtuális gépek replikálása az Azure storage-fiókokra.
> - Feladatátvételi beállítások konfigurálása virtuális gépek replikálásához.
> - Feladatátvételi teszt végrehajtása, ellenőrzéséhez és karbantartása a feladatátvételi teszthez.
> - Feladatátvétel az Azure-bA.

## <a name="prerequisites"></a>Előfeltételek

Előkészületek:
- Győződjön meg arról, hogy tudomásul veszi a [forgatókönyv architektúrája és összetevői](concepts-vmware-to-azure-architecture.md).
- Tekintse át a [igényeinek támogatására](site-recovery-support-matrix-to-azure.md) lévő valamennyi összetevőnél.
- Verziót 5.0.1-es vagy nagyobb a AzureRm PowerShell modul. Ha szeretné telepíteni vagy frissíteni az Azure PowerShell, folytassa a [útmutató az Azure PowerShell telepítése és konfigurálása](/powershell/azureps-cmdlets-docs).

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Jelentkezzen be a Microsoft Azure-előfizetés

Jelentkezzen be az Azure-előfizetéshez Login-AzureRmAccount parancsmag segítségével

```azurepowershell
Login-AzureRmAccount
```
Válassza ki az Azure-előfizetés szeretné replikálni a VMware virtuális gépeket. A Get-AzureRmSubscription parancsmag segítségével juthatnak az Azure-előfizetéssel rendelkezik listáját. Válassza ki az Azure-előfizetés a Select-AzureRmSubscription parancsmaggal használható.

```azurepowershell
Select-AzureRmSubscription -SubscriptionName "ASR Test Subscription"
```
## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

* Hozzon létre egy erőforráscsoportot a Recovery Services-tároló létrehozásához. Az alábbi példában az erőforráscsoport neve VMwareDRtoAzurePS, és Kelet-Ázsia régióban jön létre.

```azurepowershell
New-AzureRmResourceGroup -Name "VMwareDRtoAzurePS" -Location "East Asia"
```
```
ResourceGroupName : VMwareDRtoAzurePS
Location          : eastasia
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRtoAzurePS
```
   
* A Recovery services-tároló létrehozása. Az alábbi példában a Recovery services-tároló neve VMwareDRToAzurePs, és a Kelet-Ázsia régióban, és az előző lépésben létrehozott erőforráscsoportban jön létre.

```azurepowershell
New-AzureRmRecoveryServicesVault -Name "VMwareDRToAzurePs" -Location "East Asia" -ResourceGroupName "VMwareDRToAzurePs"
```
```
Name              : VMwareDRToAzurePs
ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs
Type              : Microsoft.RecoveryServices/vaults
Location          : eastasia
ResourceGroupName : VMwareDRToAzurePs
SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
``` 

* Töltse le a tárolóregisztrációs kulcsot a tároló. A tárolóbeli regisztrációs kulcs segítségével regisztrálja a helyi konfigurációs kiszolgálót ehhez a tárolóhoz. Regisztráció a konfigurációs kiszolgáló szoftvertelepítési folyamat része.

```azurepowershell
#Get the vault object by name and resource group and save it to the $vault PowerShell variable 
$vault = Get-AzureRmRecoveryServicesVault -Name "VMwareDRToAzurePS" -ResourceGroupName "VMwareDRToAzurePS"

#Download vault registration key to the path C:\Work
Get-AzureRmRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
```
```
FilePath
--------
C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials
```

Használja a letöltött regisztrációs kulcsával, és kövesse az alábbiakban láthatók a telepítés befejezéséhez és a kiszolgáló regisztrációjának cikkeket.
* [Védelmi célok megválasztása](site-recovery-set-up-vmware-to-azure.md#choose-your-protection-goals)
* [A forráskörnyezet beállítása](site-recovery-set-up-vmware-to-azure.md#set-up-the-source-environment) 

## <a name="set-the-vault-context"></a>A tároló környezet beállítása

> [!TIP]
> Az Azure Site Recovery PowerShell modul (AzureRm.RecoveryServices.SiteRecovery modul) könnyen használható aliasok legtöbb parancsmagokat tartalmaz. A modul parancsmagjai formájában  *\<művelet >-**AzureRmRecoveryServicesAsr**\<objektum >* és egyenértékű aliast is, amely formájú  *\<Művelet >-**ASR**\<objektum >*. Ez a cikk a parancsmag aliasok olvasási könnyű használ.

Állítsa be a tároló a környezetben, a Set-ASRVaultContext parancsmaggal. Beállítása után további Azure Site Recovery a PowerShell-munkamenetben műveleteket a kijelölt tároló környezetében. Az alábbi példában a $vault tároló adataiból változó segítségével adja meg a tároló környezetet a PowerShell-munkamenethez.
 ```azurepowershell
Set-ASRVaultContext -Vault $vault
```
```
ResourceName      ResourceGroupName ResourceNamespace          ResouceType
------------      ----------------- -----------------          -----------
VMwareDRToAzurePs VMwareDRToAzurePs Microsoft.RecoveryServices vaults
```

Ez a cikk ezt követő szakaszok azt feltételezik, hogy az Azure Site Recovery-műveleteket a tároló környezetben van beállítva.

## <a name="validate-that-your-configuration-server-and-scale-out-process-servers-are-registered-to-the-vault"></a>Ellenőrizze, hogy a konfigurációs kiszolgáló és a folyamat kiszolgálók kibővítési regisztrált-e a tárolóba

 Tegyük fel:
- A konfigurációs kiszolgáló nevű *ConfigurationServer* regisztrálva van ehhez a tárolóhoz
- Egy további Folyamatkiszolgáló nevű *ScaleOut-ProcessServer* van regisztrálva az *ConfigurationServer*
- Fiókok nevű *vCenter_account*, *WindowsAccount*, és *LinuxAccount* be van állítva a konfigurációs kiszolgálón. Ezek a fiókok segítségével vegye fel a vCenter-kiszolgáló azon virtuális gépeinek felderítése, és leküldéses-telepíteni a mobilitási szolgáltatás szoftver a Windows és Linux-kiszolgálókon, amelyek replikációja.

A háló objektum az Azure Site Recovery által regisztrált konfigurációs kiszolgálók jelennek meg. Ebben a lépésben a tárolóban lévő objektumok a háló listájának lekérdezése, és a konfigurációs kiszolgáló azonosítására szolgál.

```azurepowershell
# Verify that the Configuration server is successfully registered to the vault
$ASRFabrics = Get-ASRFabric
$ASRFabrics.count
```
```
1
```
```azurepowershell
#Print details of the Configuration Server
$ASRFabrics[0]
```
```
Name                  : 2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
FriendlyName          : ConfigurationServer
ID                    : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationFabrics
                        /2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
Type                  : Microsoft.RecoveryServices/vaults/replicationFabrics
FabricType            : VMware
SiteIdentifier        : ef7a1580-f356-4a00-aa30-7bf80f952510
FabricSpecificDetails : Microsoft.Azure.Commands.RecoveryServices.SiteRecovery.ASRVMWareSpecificDetails
```

* Azonosítsa a folyamat, amely a gépek replikálása használható.

```azurepowershell
$ProcessServers = $ASRFabrics[0].FabricSpecificDetails.ProcessServers
for($i=0; $i -lt $ProcessServers.count; $i++) {
 "{0,-5} {1}" -f $i, $ProcessServers[$i].FriendlyName
}
```
```
0     ScaleOut-ProcessServer
1     ConfigurationServer
```

A fenti kimenetben ***$ProcessServers [0]*** megfelel-e *ScaleOut-ProcessServer* és ***$ProcessServers [1]*** megfelel-e a folyamat kiszolgálói szerepkört az *ConfigurationServer*

* A konfigurációs kiszolgálón beállított fiókjait azonosítják.

```azurepowershell
$AccountHandles = $ASRFabrics[0].FabricSpecificDetails.RunAsAccounts
#Print the account details
$AccountHandles
```
```
AccountId AccountName
--------- -----------
1         vCenter_account
2         WindowsAccount
3         LinuxAccount
```

A fenti kimenetben ***$AccountHandles [0]*** fióknak megfelelő *vCenter_account*, ***$AccountHandles [1]*** fiókhoz *WindowsAccount*, és ***$AccountHandles [2]*** fiókhoz *LinuxAccount*

## <a name="create-a-replication-policy-and-map-it-for-use-with-the-configuration-server"></a>Hozzon létre egy replikációs házirendet, és képezze le azt a konfigurációs kiszolgálón való használatra

Ebben a lépésben létrehozott két replikációs házirendeket. VMware virtuális gépek replikálása Azure, és az egyéb replikálni egy házirendet a feladatátvételt az Azure-ban futó virtuális gépek biztonsági helyszíni VMware-hely.

> [!NOTE]
> A legtöbb Azure Site Recovery-műveleteket aszinkron módon végrehajtása. Egy művelet kezdeményezésekor egy Azure Site Recovery-feladat nyújtják, és egy objektum követési feladatot ad vissza. Ez a feladat objektum nyomon követése a művelet állapotának figyelésére használható.

* Hozzon létre egy replikációs házirendet nevű *ReplicationPolicy* a VMware virtuális gépek replikálása Azure-bA a megadott tulajdonságokkal.

```azurepowershell
$Job_PolicyCreate = New-ASRPolicy -VMwareToAzure -Name "ReplicationPolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60

# Track Job status to check for completion
while (($Job_PolicyCreate.State -eq "InProgress") -or ($Job_PolicyCreate.State -eq "NotStarted")){ 
        sleep 10; 
        $Job_PolicyCreate = Get-ASRJob -Job $Job_PolicyCreate
}

#Display job status
$Job_PolicyCreate
```
```
Name             : 8d18e2d9-479f-430d-b76b-6bc7eb2d0b3e
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/8d18e2d
                   9-479f-430d-b76b-6bc7eb2d0b3e
Type             :
JobType          : AddProtectionProfile
DisplayName      : Create replication policy
ClientRequestId  : a162b233-55d7-4852-abac-3d595a1faac2 ActivityId: 9895234a-90ea-4c1a-83b5-1f2c6586252a
State            : Succeeded
StateDescription : Completed
StartTime        : 11/24/2017 2:49:24 AM
EndTime          : 11/24/2017 2:49:23 AM
TargetObjectId   : ab31026e-4866-5440-969a-8ebcb13a372f
TargetObjectType : ProtectionProfile
TargetObjectName : ReplicationPolicy
AllowedActions   :
Tasks            : {Prerequisites check for creating the replication policy, Creating the replication policy}
Errors           : {}
```

* A feladat-visszavétel az Azure-ból a helyszíni VMware-hely használandó replikációs házirend létrehozása.

```azurepowershell
$Job_FailbackPolicyCreate = New-ASRPolicy -AzureToVMware -Name "ReplicationPolicy-Failback" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60
```

Használja a feladat részleteit a *$Job_FailbackPolicyCreate* nyomon követéséhez a művelet befejezését.

* Hozzon létre egy replikációs házirendeket és a konfigurációs kiszolgáló leképezni védelmitároló-leképezés.

```azurepowershell
#Get the protection container corresponding to the Configuration Server
$ProtectionContainer = Get-ASRProtectionContainer -Fabric $ASRFabrics[0]

#Get the replication policies to map by name.
$ReplicationPolicy = Get-ASRPolicy -Name "ReplicationPolicy"
$FailbackReplicationPolicy = Get-ASRPolicy -Name "ReplicationPolicy-Failback"

# Associate the replication policies to the protection container corresponding to the Configuration Server. 

$Job_AssociatePolicy = New-ASRProtectionContainerMapping -Name "PolicyAssociation1" -PrimaryProtectionContainer $ProtectionContainer -Policy $Policy[0]

# Check the job status
while (($Job_AssociatePolicy.State -eq "InProgress") -or ($Job_AssociatePolicy.State -eq "NotStarted")){ 
        sleep 10; 
        $Job_AssociatePolicy = Get-ASRJob -Job $Job_AssociatePolicy
}
$Job_AssociatePolicy.State

$Job_AssociateFailbackPolicy = New-ASRProtectionContainerMapping -Name "FailbackPolicyAssociation" -PrimaryProtectionContainer $ProtectionContainer -Policy $Policy[0]

# Check the job status
while (($Job_AssociateFailbackPolicy.State -eq "InProgress") -or ($Job_AssociateFailbackPolicy.State -eq "NotStarted")){ 
        sleep 10; 
        $Job_AssociateFailbackPolicy = Get-ASRJob -Job $Job_AssociateFailbackPolicy
}
$Job_AssociateFailbackPolicy.State

```

## <a name="add-a-vcenter-server-and-discover-vmware-virtual-machines"></a>Vegye fel a vCenter-kiszolgáló és a VMware virtuális gépek észlelése

Vegyen fel egy vCenter-kiszolgáló IP-cím vagy állomásnév alapján. A **-port** paraméter meghatározza azt a portot a vCenter-kiszolgáló, amelyhez csatlakozni kíván, **-név** paraméter adja meg egy rövid nevet a a vCenter-kiszolgáló és a **-fiók** a paraméter a fiók leíró használandó a vCenter-kiszolgáló által kezelt virtuális gép konfigurációs kiszolgálón.

```azurepowershell
# The $AccountHandles[0] variable holds details of vCenter_account

$Job_AddvCenterServer = New-ASRvCenter -Fabric $ASRFabrics[0] -Name "MyvCenterServer" -IpOrHostName "10.150.24.63" -Account $AccountHandles[0] -Port 443

#Wait for the job to complete and ensure it completed successfully

while (($Job_AddvCenterServer.State -eq "InProgress") -or ($Job_AddvCenterServer.State -eq "NotStarted")) { 
        sleep 30; 
        $Job_AddvCenterServer = Get-ASRJob -Job $Job_AddvCenterServer
}
$Job_AddvCenterServer
```
```
Name             : 0f76f937-f9cf-4e0e-bf27-10c9d1c252a4
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/0f76f93
                   7-f9cf-4e0e-bf27-10c9d1c252a4
Type             :
JobType          : DiscoverVCenter
DisplayName      : Add vCenter server
ClientRequestId  : a2af8892-5686-4d64-a528-10445bc2f698 ActivityId: 7ec05aad-002e-4da0-991f-95d0de7a9f3a
State            : Succeeded
StateDescription : Completed
StartTime        : 11/24/2017 2:41:47 AM
EndTime          : 11/24/2017 2:44:37 AM
TargetObjectId   : 10.150.24.63
TargetObjectType : VCenter
TargetObjectName : MyvCenterServer
AllowedActions   :
Tasks            : {Adding vCenter server}
Errors           : {}
```

## <a name="create-storage-accounts"></a>Tárfiókok létrehozása

Ebben a lépésben a tárfiókok replikációs használandó jönnek létre. Ezekre a tárfiókokra később a virtuális gépek replikálása szolgálnak. Győződjön meg arról, hogy a storage-fiókok és a tárolónak Azure ugyanabban a régióban jönnek létre. Ezt a lépést kihagyhatja, ha meglévő tárfiók replikáláshoz használni szeretne.

> [!NOTE]
> A prémium szintű storage-fiókok a helyszíni virtuális gépek replikálása, miközben meg kell adnia egy további standard szintű tárfiók (napló tárfiók). A naplózási tárfiókot a replikálási naplókhoz köztes tárolóként történő rendelkezik, a naplók a prémium szintű storage célszámítógépen telepítéséig.
>

```azurepowershell

$PremiumStorageAccount = New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "premiumstorageaccount1" -Location "East Asia" -SkuName Premium_LRS

$LogStorageAccount = New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "logstorageaccount1" -Location "East Asia" -SkuName Standard_LRS

$ReplicationStdStorageAccount= New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "replicationstdstorageaccount1" -Location "East Asia" -SkuName Standard_LRS
```

## <a name="replicate-vmware-virtual-machines"></a>VMware virtuális gépek replikálása

Virtuális gépek a vCenter-kiszolgáló felderítését körülbelül 15 – 20 percet vesz igénybe. Felderített, miután egy védhető objektum létrehozása az Azure Site Recovery minden felderített virtuális géphez. Ebben a lépésben a felderített virtuális gépek közül hármat replikálódnak az előző lépésben létrehozott Azure Storage-fiókokat.

Szüksége lesz egy felderített virtuális gép védelmét a következő adatokat:
* A védhető replikálni.
* A tárfiókot, amelybe replikálhatók a virtuális gépet. A naplók tárolásához, a prémium szintű tárfiók a virtuális gépek védelmére van szükség.
* A Folyamatkiszolgáló replikálás használandó. Rendelkezésre álló folyamat kiszolgálók listájának lekérése és menti a ***$ProcessServers [0]****(ScaleOut-ProcessServer)* és ***$ProcessServers [1]*** *(ConfigurationServer)* változók.
* A leküldéses telepítés a mobilitási szolgáltatás szoftvereket a gépek használandó fiókot. A rendelkezésre álló fiókok listájának lekérése és tárolja a ***$AccountHandles*** változó.
* A védelmitároló-leképezés a replikációs házirend-replikációhoz használt.
* Az erőforráscsoport, amelyben virtuális gépek léteznie kell a feladatátvevő.
* Szükség esetén az Azure-beli virtuális hálózat és az alhálózatot, amelyhez a feladatokat átadó virtuális gép kell csatlakoztatni.

Most replikálja a következő virtuális gépek, az ebben a táblázatban megadott beállításokkal


|Virtuális gép  |Folyamatkiszolgáló        |Tárfiók              |Napló Storage-fiók  |Szabályzat           |A mobilitási szolgáltatás telepítési fiók|Cél-erőforráscsoport  | Virtuális hálózati cél  |Cél alhálózathoz  |
|-----------------|----------------------|-----------------------------|---------------------|-----------------|-----------------------------------------|-----------------------|-------------------------|---------------|
|Win2K12VM1       |ScaleOut-ProcessServer|premiumstorageaccount1       |logstorageaccount1   |ReplicationPolicy|WindowsAccount                           |VMwareDRToAzurePs      |Az ASR-hálózatok                 |Alhálózat-1       |
|CentOSVM1       |ConfigurationServer   |replicationstdstorageaccount1| –                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |Az ASR-hálózatok                 |Alhálózat-1       |   
|CentOSVM2       |ConfigurationServer   |replicationstdstorageaccount1| –                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |Az ASR-hálózatok                 |Alhálózat-1       |   

 
```azurepowershell

#Get the target resource group to be used
$ResourceGroup = Get-AzureRmResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzureRmVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg" 

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap  = Get-ASRProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine Win2K12VM1
$VM1 = Get-ASRProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "Win2K12VM1"

# Enable replication for virtual machine Win2K12VM1
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableRepication1 = New-ASRReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $PremiumStorageAccount.Id -LogStorageAccountId $LogStorageAccount.Id -ProcessServer $ProcessServers[0] -Account $AccountHandles[1] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" 

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM2 = Get-ASRProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1
$Job_EnableRepication2 = New-ASRReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM2 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine CentOSVM2
$VM3 = Get-ASRProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM2"

# Enable replication for virtual machine CentOSVM2
$Job_EnableRepication3 = New-ASRReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM3 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" 

```

Miután a engedélyezése replikációs feladat sikeresen befejeződik, a kezdeti replikálás a virtuális gépek elindult. Kezdeti replikálás eltarthat egy ideig, attól függően, hogy replikálni mennyiségétől és a sávszélesség álljon rendelkezésre a replikáció. Kezdeti replikáció befejezése után a virtuális gép áthelyezése egy védett állapotban. Ha a virtuális gép elérte a védett állapotban végezheti el a virtuális gép feladatátvételi tesztjét, adja hozzá a helyreállítási terv stb.

Ellenőrizheti a replikációs állapot és a Get-ASRReplicationProtectedItem parancsmag a virtuális gép replikációs állapotát.

```azurepowershell
Get-ASRReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState                 ReplicationHealth
------------ ---------------                 -----------------
CentOSVM1    Protected                       Normal
CentOSVM2    InitialReplicationInProgress    Normal
Win2K12VM1   Protected                       Normal
```

## <a name="configure-failover-settings-for-replicating-virtual-machines"></a>Virtuális gépek replikálásához feladatátvételi beállításainak konfigurálása

Védett gépek feladatátvevő beállításait a Set-ASRReplicationProtectedItem parancsmaggal lehet frissíteni. Ez a parancsmag keresztül frissíthető beállítások a következők:
* Feladatátvétel létrehozni a virtuális gép neve
* A virtuális gép feladatátvételi hozható létre Virtuálisgép-méretet
* Azure-beli virtuális hálózat és az alhálózatot, amely a hálózati adaptert a virtuális gép csatlakoznia kell a feladatátvételi
* Felügyelt lemezek feladatátvétel
* Azure hibrid használata juttatás alkalmazása
* Rendelje hozzá egy statikus IP-címet a cél virtuális hálózat hozzárendelését a virtuális gép a feladatátvételt.

Ebben a példában a Virtuálisgép-méretet, a virtuális gép hozza létre a virtuális gép frissítjük *Win2K12VM1* , és adja meg, hogy kezeli-e a virtuális gép használata a feladatátvételi lemezzel.

```azurepowershell
$ReplicatedVM1 = Get-ASRReplicationProtectedItem -FriendlyName "Win2K12VM1" -ProtectionContainer $ProtectionContainer

Set-ASRReplicationProtectedItem -InputObject $ReplicatedVM1 -Size "Standard_DS11" -UseManagedDisk True
```
```
Name             : cafa459c-44a7-45b0-9de9-3d925b0e7db9
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/cafa459
                   c-44a7-45b0-9de9-3d925b0e7db9
Type             :
JobType          : UpdateVmProperties
DisplayName      : Update the virtual machine
ClientRequestId  : b0b51b2a-f151-4e9a-a98e-064a5b5131f3 ActivityId: ac2ba316-be7b-4c94-a053-5363f683d38f
State            : InProgress
StateDescription : InProgress
StartTime        : 11/24/2017 2:04:26 PM
EndTime          :
TargetObjectId   : 88bc391e-d091-11e7-9484-000c2955bb50
TargetObjectType : ProtectionEntity
TargetObjectName : Win2K12VM1
AllowedActions   :
Tasks            : {Update the virtual machine properties}
Errors           : {}
```

## <a name="perform-a-test-failover-validate-and-cleanup-test-failover"></a>Feladatátvételi teszt végrehajtása, ellenőrzéséhez és karbantartása a feladatátvételi teszt

```azurepowershell
#Test failover of Win2K12VM1 to the test virtual network "V2TestNetwork"

#Get details of the test failover virtual network to be used
TestFailovervnet = Get-AzureRmVirtualNetwork -Name "V2TestNetwork" -ResourceGroupName "asrrg" 

#Start the test failover operation
$TFOJob = Start-ASRTestFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -AzureVMNetworkId $TestFailovervnet.Id -Direction PrimaryToRecovery
```
Ha a teszt feladatátvételi feladat sikeresen befejeződik, megfigyelheti, hogy egy virtuális gépet a utótaggal *"-tesztelése"* (Win2K12VM1-teszt ebben az esetben) az nevére jön létre az Azure-ban. 

Most már a teszt virtuális gép a feladatátvételt csatlakozhat, és a teszt feladatátvétel érvényesítése.

Tisztítás használatával a feladatátvételi teszthez a Start-ASRTestFailoverCleanupJob parancsmag. Ez a művelet törli a virtuális géphez, hozza létre a teszt feladatátvételi művelet részeként.

```azurepowershell
$Job_TFOCleanup = Start-ASRTestFailoverCleanupJob -ReplicationProtectedItem $ReplicatedVM1
```

## <a name="failover-to-azure"></a>Feladatátvétel az Azure-bA

Ebben a lépésben azt feladatátvételt a virtuális gép Win2K12VM1 egy adott helyreállítási pontot.

```azurepowershell
# Get the list of available recovery points for Win2K12VM1
$RecoveryPoints = Get-ASRRecoveryPoint -ReplicationProtectedItem $ReplicatedVM1
"{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[0].RecoveryPointTime
```
```
CrashConsistent 11/24/2017 5:28:25 PM
```
```azurepowershell

#Start the failover job
$Job_Failover = Start-ASRUnplannedFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[0]
do {
        $Job_Failover = Get-ASRJob -Job $Job_Failover;
        sleep 60;
} while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))
$Job_Failover.State
```
```
Succeeded
```

Ha feladatátvételt sikeresen, véglegesítheti a feladatátvételi művelet, és a fordított replikáció beállítása az Azure-ból a helyszíni VMware-hely biztonsági.

## <a name="next-steps"></a>További lépések
Nézet a [Azure Site Recovery PowerShell-referencia ](https://docs.microsoft.com/powershell/module/AzureRM.RecoveryServices.SiteRecovery) megtudhatja, hogyan egyéb feladatokhoz, mint a helyreállítási terv létrehozása és tesztelése a feladatátvételi helyreállítási tervek a PowerShell segítségével végezheti el.
