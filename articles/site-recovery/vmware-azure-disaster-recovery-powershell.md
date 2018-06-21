---
title: Replikálásához és feladatátadásához a VMware virtuális gépek Azure-bA az Azure Site Recovery PowerShell használatával |} Microsoft Docs
description: Megtudhatja, hogyan állíthat be replikációs és feladatátvételi Azure VMware virtuális gépek az Azure Site Recovery PowerShell használatával.
services: site-recovery
author: bsiva
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: bsiva
ms.openlocfilehash: 051bc3a0e1c0126826e96b49ff0a4e8008c88006
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287854"
---
# <a name="replicate-and-fail-over-vmware-vms-to-azure-with-powershell"></a>Replikálásához és feladatátadásához a VMware virtuális gépek az Azure PowerShell használatával

Ebből a cikkből látni, hogyan replikáljon és feladatátvételi VMware virtuális gépeket az Azure-ban az Azure PowerShell használatával. 

Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> - Recovery Services-tároló létrehozása és beállítása a tárolóban a környezetben.
> - Ellenőrizze a kiszolgáló regisztrálása a tárolóban lévő állapottal.
> - A replikáció beállítása, beleértve a replikációs házirendet. Vegye fel a vCenter-kiszolgálót, és a virtuális gépek felderítése. > – A vCenter-kiszolgáló hozzáadása és felderítése 
> - Replikációs adatok tárolásához tárfiókok létrehozása, és a virtuális gépek replikálása.
> - Végezzen el egy feladatátvételt. Feladatátvétel konfigurálása, egy e beállítások végrehajtani a virtuális gépek replikálásához.

## <a name="prerequisites"></a>Előfeltételek

Előkészületek:

- Ismernie kell a [forgatókönyv-architektúrát és az összetevőket](vmware-azure-architecture.md).
- Minden összetevőre vonatkozóan tekintse át a [támogatási követelményeket](site-recovery-support-matrix-to-azure.md).
- Verziót 5.0.1-es vagy nagyobb a AzureRm PowerShell modul. Ha szeretné telepíteni vagy frissíteni az Azure PowerShell, folytassa a [útmutató az Azure PowerShell telepítése és konfigurálása](/powershell/azureps-cmdlets-docs).

## <a name="log-into-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetéshez a Connect-AzureRmAccount parancsmag használatával:

```azurepowershell
Connect-AzureRmAccount
```
Válassza ki az Azure-előfizetés szeretné replikálni a VMware virtuális gépeket. A Get-AzureRmSubscription parancsmag segítségével juthatnak az Azure-előfizetéssel rendelkezik listáját. Válassza ki az Azure-előfizetés a Select-AzureRmSubscription parancsmaggal használható.

```azurepowershell
Select-AzureRmSubscription -SubscriptionName "ASR Test Subscription"
```
## <a name="set-up-a-recovery-services-vault"></a>Helyreállítási tár beállítása

1. Hozzon létre egy erőforráscsoportot a Recovery Services-tároló létrehozásához. Az alábbi példában az erőforráscsoport neve VMwareDRtoAzurePS, és Kelet-Ázsia régióban jön létre.

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
   
2. A Recovery services-tároló létrehozása. Az alábbi példában a Recovery services-tároló neve VMwareDRToAzurePs, és a Kelet-Ázsia régióban, és az előző lépésben létrehozott erőforráscsoportban jön létre.

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

3. Töltse le a tárolóregisztrációs kulcsot a tároló. A tárolóbeli regisztrációs kulcs segítségével regisztrálja a helyi konfigurációs kiszolgálót ehhez a tárolóhoz. Regisztráció a konfigurációs kiszolgáló szoftvertelepítési folyamat része.

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

4. Használja a letöltött regisztrációs kulcsával, és kövesse az alábbiakban láthatók a telepítés befejezéséhez és a kiszolgáló regisztrációjának cikkeket.
   - [Védelmi célok megválasztása](vmware-azure-set-up-source.md#choose-your-protection-goals)
   - [A forráskörnyezet beállítása](vmware-azure-set-up-source.md#set-up-the-configuration-server) 

### <a name="set-the-vault-context"></a>A tároló környezet beállítása

Állítsa be a tároló a környezetben, a Set-ASRVaultContext parancsmaggal. Beállítása után további Azure Site Recovery a PowerShell-munkamenetben műveleteket a kijelölt tároló környezetében.

> [!TIP]
> Az Azure Site Recovery PowerShell modul (AzureRm.RecoveryServices.SiteRecovery modul) könnyen használható aliasok legtöbb parancsmagokat tartalmaz. A modul parancsmagjai formájában  *\<művelet >-**AzureRmRecoveryServicesAsr**\<objektum >* és egyenértékű aliast is, amely formájú  *\<Művelet >-**ASR**\<objektum >*. Ez a cikk a parancsmag aliasok olvasási könnyű használ.

Az alábbi példában a $vault tároló adataiból változó segítségével adja meg a tároló környezetet a PowerShell-munkamenethez.

   ```azurepowershell
   Set-ASRVaultContext -Vault $vault
   ```
   ```
   ResourceName      ResourceGroupName ResourceNamespace          ResouceType
   ------------      ----------------- -----------------          -----------
   VMwareDRToAzurePs VMwareDRToAzurePs Microsoft.RecoveryServices vaults
   ```

A Set-ASRVaultContext parancsmag alternatívájaként egy is az Import-AzureRmRecoveryServicesAsrVaultSettingsFile parancsmag beállításához használja a tárolóban a környezetben. Adja meg az elérési, ahol a tároló regisztrációs kulcs fájlját, az importálás-AzureRmRecoveryServicesAsrVaultSettingsFile parancsmagnak a - path paramétert. Példa:

   ```azurepowershell
   Get-AzureRmRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   Import-AzureRmRecoveryServicesAsrVaultSettingsFile -Path "C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials"
   ```
Ez a cikk ezt követő szakaszok azt feltételezik, hogy az Azure Site Recovery-műveleteket a tároló környezetben van beállítva.

## <a name="validate-vault-registration"></a>Tároló regisztráció érvényesítése

Ehhez a példához vezetünk be a következőket:

- A konfigurációs kiszolgáló (**ConfigurationServer**) van regisztrálva ehhez a tárolóhoz.
- Egy további folyamatkiszolgáló (**ScaleOut-ProcessServer**) van regisztrálva az *ConfigurationServer*
- Fiókok (**vCenter_account**, **WindowsAccount**, **LinuxAccount**) be van állítva a konfigurációs kiszolgálón. Ezek a fiókok segítségével adja hozzá a vCenter-kiszolgálót, azon virtuális gépeinek felderítése, és leküldéses-telepíteni a mobilitási szolgáltatás szoftver a Windows és Linux-kiszolgálókon, amelyek replikálható.

1. A háló objektum a Site Recovery szolgáltatásban regisztrált konfigurációs kiszolgálók jelöli. A tárolóban lévő objektumok a háló listájának lekérdezése, és a konfigurációs kiszolgáló azonosítására szolgál.

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

2. Azonosítsa a folyamat, amely a gépek replikálása használható.

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

3. A konfigurációs kiszolgálón beállított fiókjait azonosítják.

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

## <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

Ebben a lépésben létrehozott két replikációs házirendeket. VMware virtuális gépek replikálása Azure, és az egyéb replikálni egy házirendet a feladatátvételt az Azure-ban futó virtuális gépek biztonsági helyszíni VMware-hely.

> [!NOTE]
> A legtöbb Azure Site Recovery-műveleteket aszinkron módon végrehajtása. Egy művelet kezdeményezésekor egy Azure Site Recovery-feladat nyújtják, és egy objektum követési feladatot ad vissza. Ez a feladat objektum nyomon követése a művelet állapotának figyelésére használható.

1. Hozzon létre egy replikációs házirendet nevű *ReplicationPolicy* a VMware virtuális gépek replikálása Azure-bA a megadott tulajdonságokkal.

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

2. A feladat-visszavétel az Azure-ból a helyszíni VMware-hely használandó replikációs házirend létrehozása.

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

   $Job_AssociatePolicy = New-ASRProtectionContainerMapping -Name "PolicyAssociation1" -PrimaryProtectionContainer $ProtectionContainer -Policy $ReplicationPolicy

   # Check the job status
   while (($Job_AssociatePolicy.State -eq "InProgress") -or ($Job_AssociatePolicy.State -eq "NotStarted")){ 
           sleep 10; 
           $Job_AssociatePolicy = Get-ASRJob -Job $Job_AssociatePolicy
   }
   $Job_AssociatePolicy.State

   <# In the protection container mapping used for failback (replicating failed over virtual machines 
      running in Azure, to the primary VMware site.) the protection container corresponding to the 
      Configuration server acts as both the Primary protection container and the recovery protection
      container
   #>
    $Job_AssociateFailbackPolicy = New-ASRProtectionContainerMapping -Name "FailbackPolicyAssociation" -PrimaryProtectionContainer $ProtectionContainer -RecoveryProtectionContainer $ProtectionContainer -Policy $FailbackReplicationPolicy

   # Check the job status
   while (($Job_AssociateFailbackPolicy.State -eq "InProgress") -or ($Job_AssociateFailbackPolicy.State -eq "NotStarted")){ 
           sleep 10; 
           $Job_AssociateFailbackPolicy = Get-ASRJob -Job $Job_AssociateFailbackPolicy
   }
   $Job_AssociateFailbackPolicy.State

   ```

## <a name="add-a-vcenter-server-and-discover-vms"></a>Vegye fel a vCenter-kiszolgáló és a virtuális gépek felderítése

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

## <a name="create-storage-accounts-for-replication"></a>A replikáció storage-fiókok létrehozása

Ebben a lépésben a tárfiókok replikációs használandó jönnek létre. Ezekre a tárfiókokra később a virtuális gépek replikálása szolgálnak. Győződjön meg arról, hogy a storage-fiókok és a tárolónak Azure ugyanabban a régióban jönnek létre. Ezt a lépést kihagyhatja, ha meglévő tárfiók replikáláshoz használni szeretne.

> [!NOTE]
> A prémium szintű storage-fiókok a helyszíni virtuális gépek replikálása, miközben meg kell adnia egy további standard szintű tárfiók (napló tárfiók). A naplózási tárfiókot a replikálási naplókhoz köztes tárolóként történő rendelkezik, a naplók a prémium szintű storage célszámítógépen telepítéséig.
>

```azurepowershell

$PremiumStorageAccount = New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "premiumstorageaccount1" -Location "East Asia" -SkuName Premium_LRS

$LogStorageAccount = New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "logstorageaccount1" -Location "East Asia" -SkuName Standard_LRS

$ReplicationStdStorageAccount= New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "replicationstdstorageaccount1" -Location "East Asia" -SkuName Standard_LRS
```

## <a name="replicate-vmware-vms"></a>VMware virtuális gépek replikálása

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
|Win2K12VM1       |ScaleOut-ProcessServer|premiumstorageaccount1       |logstorageaccount1   |ReplicationPolicy|WindowsAccount                           |VMwareDRToAzurePs      |ASR-vnet                 |Alhálózat-1       |
|CentOSVM1       |ConfigurationServer   |replicationstdstorageaccount1| –                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR-vnet                 |Alhálózat-1       |   
|CentOSVM2       |ConfigurationServer   |replicationstdstorageaccount1| –                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR-vnet                 |Alhálózat-1       |   

 
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

## <a name="configure-failover-settings"></a>Feladatátvétel konfigurálása

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

## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

1. Futtassa a vész-Helyreállítási részletezési (feladatátvételi teszt) az alábbiak szerint:

   ```azurepowershell
   #Test failover of Win2K12VM1 to the test virtual network "V2TestNetwork"

   #Get details of the test failover virtual network to be used
   TestFailovervnet = Get-AzureRmVirtualNetwork -Name "V2TestNetwork" -ResourceGroupName "asrrg" 

   #Start the test failover operation
   $TFOJob = Start-ASRTestFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -AzureVMNetworkId $TestFailovervnet.Id -Direction PrimaryToRecovery
   ```
2. Ha a teszt feladatátvételi feladat sikeresen befejeződik, megfigyelheti, hogy egy virtuális gépet a utótaggal *"-tesztelése"* (Win2K12VM1-teszt ebben az esetben) az nevére jön létre az Azure-ban.
3. Most már a teszt virtuális gép a feladatátvételt csatlakozhat, és a teszt feladatátvétel érvényesítése.
4. Számolja fel a feladatátvételi tesztet a Start-ASRTestFailoverCleanupJob parancsmaggal. Ez a művelet törli a virtuális géphez, hozza létre a teszt feladatátvételi művelet részeként.

   ```azurepowershell
   $Job_TFOCleanup = Start-ASRTestFailoverCleanupJob -ReplicationProtectedItem $ReplicatedVM1
   ```

## <a name="fail-over-to-azure"></a>Feladatok átadása a Azure

Ebben a lépésben nem sikerül egy adott helyreállítási pontot a virtuális gép Win2K12VM1 keresztül.

1. Rendelkezésre álló helyreállítási pontok a feladatátvétel használandó listájának beolvasása:
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

2. Sikeres legyen, hogy a feladatátvételt követően véglegesítse a feladatátvételi művelet, és állítsa be a visszirányú replikálás az Azure-ból biztonsági helyszíni VMware-hely.

## <a name="next-steps"></a>További lépések
További feladatok automatizálása a [Azure Site Recovery PowerShell hivatkozás ](https://docs.microsoft.com/powershell/module/AzureRM.RecoveryServices.SiteRecovery).
