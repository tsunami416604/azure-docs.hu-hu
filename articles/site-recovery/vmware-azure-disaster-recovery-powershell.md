---
title: Állítsa be a VMware virtuális gépek vészhelyreállítása az Azure-bA az Azure Site Recovery PowerShell használatával |} A Microsoft Docs
description: Ismerje meg, hogyan állítható be a replikáció és feladatátvétel az Azure PowerShell-lel az Azure Site Recovery VMware virtuális gépek vész-helyreállítási.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.date: 07/06/2018
ms.topic: conceptual
ms.author: sutalasi
ms.openlocfilehash: 631225e6e0a7bb3bc42741e2761e3746c001c217
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50211638"
---
# <a name="set-up-disaster-recovery-of-vmware-vms-to-azure-with-powershell"></a>Állítsa be a VMware virtuális gépek vészhelyreállítása az Azure-ban a PowerShell-lel

Ebben a cikkben láthatja a replikálás és feladatátvételi VMware virtuális gépeket az Azure PowerShell használatával. 

Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> - Hozzon létre egy Recovery Services-tárolót, és állítsa be a tárolási környezet.
> - Ellenőrizze a kiszolgáló regisztrálása a tárolóban.
> - A replikáció, többek között a replikációs szabályzat beállítása. A vCenter-kiszolgáló hozzáadása, és a virtuális gépek felderítéséhez. > - A vCenter-kiszolgáló hozzáadása és felderítése 
> - Replikációs adatok tárolásához a storage-fiókok létrehozásához, és a virtuális gépek replikálása.
> - Feladatátvétel végrehajtása. Feladatátvétel-beállítások konfigurálása, egy e beállítások végrehajtani a virtuális gépek replikálásához.

## <a name="prerequisites"></a>Előfeltételek

Előkészületek:

- Ismernie kell a [forgatókönyv-architektúrát és az összetevőket](vmware-azure-architecture.md).
- Minden összetevőre vonatkozóan tekintse át a [támogatási követelményeket](site-recovery-support-matrix-to-azure.md).
- 5.0.1-es verzióval rendelkezik, vagy nagyobb, mint az AzureRm PowerShell-modul. Ha telepíteni vagy frissíteni az Azure PowerShell-lel van szüksége, kövesse ezt [útmutató az Azure PowerShell telepítése és konfigurálása](/powershell/azureps-cmdlets-docs).

## <a name="log-into-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure-előfizetés a Connect-AzureRmAccount parancsmag használatával:

```azurepowershell
Connect-AzureRmAccount
```
A VMware virtuális gépek replikálásához használni kívánt Azure-előfizetés kiválasztásához. A Get-AzureRmSubscription parancsmaghoz használatával hozzáféréssel rendelkezik az Azure-előfizetések listájának beolvasása. Válassza ki a Select-AzureRmSubscription parancsmag használata az Azure-előfizetést.

```azurepowershell
Select-AzureRmSubscription -SubscriptionName "ASR Test Subscription"
```
## <a name="set-up-a-recovery-services-vault"></a>Helyreállítási tár beállítása

1. Hozzon létre egy erőforráscsoportot, amelyben létrehozza a Recovery Services-tároló. Az alábbi példában az erőforráscsoport VMwareDRtoAzurePS neve, és a kelet-ázsiai régióban jön létre.

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
   
2. Hozzon létre egy Recovery services-tárolót. Az alábbi példában a Recovery services-tároló VMwareDRToAzurePs neve, és a Kelet-Ázsia régióban, és az előző lépésben létrehozott erőforráscsoportban jön létre.

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

3. Töltse le a tárregisztrációs kulcsot a tárolóhoz. Ebben a tárolóban, a helyszíni konfigurációs kiszolgálót regisztrálja a tárregisztrációs kulcsot szolgál. Regisztráció a konfigurációs kiszolgáló szoftvertelepítési folyamat részét képezi.

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

4. A letöltött regisztrációs kulcsot használja, és kövesse a telepítés befejezéséhez és a konfigurációs kiszolgáló regisztrálása az alábbiakban a cikkeket.
   - [Védelmi célok megválasztása](vmware-azure-set-up-source.md#choose-your-protection-goals)
   - [A forráskörnyezet beállítása](vmware-azure-set-up-source.md#set-up-the-configuration-server) 

### <a name="set-the-vault-context"></a>A tárolási környezet beállítása

Állítsa be a tárolási környezet, a Set-ASRVaultContext parancsmag használatával. Beállítása után a PowerShell-munkamenetben későbbi Azure Site Recovery-műveletek a kiválasztott tár környezetében történik.

> [!TIP]
> A legtöbb parancsmag könnyen használható aliasok az Azure Site Recovery PowerShell modul (AzureRm.RecoveryServices.SiteRecovery modul) tartalmaz. A modul parancsmagjai utat  *\<művelet >-**: AzureRmRecoveryServicesAsr**\<objektum >* és egyenértékű aliast is beállíthat, amely formájában  *\<Művelet >-**ASR**\<objektum >*. Ebben a cikkben a parancsmag aliasok átláthatóbbá tétele.

Az alábbi példában a tároló részleteit a $vault változójával adja meg a tárolási környezetet a PowerShell-munkamenetben.

   ```azurepowershell
   Set-ASRVaultContext -Vault $vault
   ```
   ```
   ResourceName      ResourceGroupName ResourceNamespace          ResouceType
   ------------      ----------------- -----------------          -----------
   VMwareDRToAzurePs VMwareDRToAzurePs Microsoft.RecoveryServices vaults
   ```

A Set-ASRVaultContext parancsmag helyett, egyet az Import-AzureRmRecoveryServicesAsrVaultSettingsFile-parancsmagot is használhatja a tárolási környezet beállításához. Az elérési út, amikor a tároló regisztrációs kulcsfájl nem található, mint a Import-AzureRmRecoveryServicesAsrVaultSettingsFile parancsmagot a - path paraméterrel adja meg. Példa:

   ```azurepowershell
   Get-AzureRmRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   Import-AzureRmRecoveryServicesAsrVaultSettingsFile -Path "C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials"
   ```
Ez a cikk későbbi részeiben azt feltételezik, hogy a tárolási környezetet az Azure Site Recovery-műveletek van beállítva.

## <a name="validate-vault-registration"></a>Tároló regisztráció érvényesítése

Ebben a példában az alábbiakkal:

- Konfigurációs kiszolgáló (**ConfigurationServer**) ehhez a tárolóhoz van regisztrálva.
- Egy további folyamatkiszolgáló (**horizontális Felskálázás-ProcessServer**) regisztrálva van *ConfigurationServer*
- Fiókok (**vCenter_account**, **WindowsAccount**, **LinuxAccount**) be van állítva a konfigurációs kiszolgálón. Ezek a fiókok segítségével a virtuális gépek felderítése és a leküldéses telepítés a mobilitási szolgáltatása a Windows és Linux-kiszolgálókon, amelyek replikációja a vCenter-kiszolgáló hozzáadása.

1. A fabric objektum a Site Recoveryben regisztrált konfigurációs kiszolgálók jelöli. A tárolóban lévő objektumok fabric listájának beolvasása, és azonosítani a konfigurációs kiszolgálón.

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

2. Azonosítsa a folyamatkiszolgálók a gépek replikálása használható.

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

   A fenti kimenetben ***$ProcessServers [0]*** felel meg *horizontális Felskálázás-ProcessServer* és ***$ProcessServers [1]*** felel meg a Folyamatkiszolgáló szerepkört *ConfigurationServer*

3. A konfigurációs kiszolgálón beállított fiók azonosításához.

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

   A fenti kimenetben ***$AccountHandles [0]*** a fióknak megfelelő *vCenter_account*, ***$AccountHandles [1]*** fiókhoz *WindowsAccount*, és ***$AccountHandles: [2]*** fiókhoz *LinuxAccount*

## <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

Ebben a lépésben két replikációs házirendek jönnek létre. A VMware virtuális gépek replikálása az Azure, a másik pedig replikálni egy szabályzat átadta a feladatait az Azure-ban futó virtuális gépek biztonsági másolatot a helyszíni VMware-helyre.

> [!NOTE]
> A legtöbb Azure Site Recovery-műveletek aszinkron végrehajtása. Egy művelet kezdeményezésekor az Azure Site Recovery-feladatok elküldésekor, és a egy feladat nyomon követése az objektumot ad vissza. A feladat nyomon követése az objektumot a művelet állapotának figyelésére használható.

1. Hozzon létre egy replikációs házirendet nevű *ReplicationPolicy* a VMware virtuális gépek replikálása az Azure-bA a megadott tulajdonságokkal.

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

2. A feladat-visszavétel az Azure-ból helyszíni VMware-hely használandó replikációs szabályzat létrehozásához.

   ```azurepowershell
   $Job_FailbackPolicyCreate = New-ASRPolicy -AzureToVMware -Name "ReplicationPolicy-Failback" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60
   ```

   Használja a feladat részleteit a *$Job_FailbackPolicyCreate* nyomon követéséhez a művelet befejezését.

   * Hozzon létre egy replikációs házirendek a konfigurációs kiszolgálóval leképezésére védelmitároló-leképezés.

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

## <a name="add-a-vcenter-server-and-discover-vms"></a>Adja hozzá a vCenter-kiszolgáló és a virtuális gépek felderítése

Adjon hozzá egy vCenter-kiszolgáló IP-cím vagy állomásnév alapján. A **-port** paraméter meghatározza azt a portot szeretne csatlakozni, a vCenter-kiszolgáló **-név** paraméter adja meg egy rövid nevet a vCenter-kiszolgáló és a **-fiók** a paraméter a fiók leíró használatával a vCenter-kiszolgáló által kezelt virtuális gépek felderítése a konfigurációs kiszolgálón.

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

## <a name="create-storage-accounts-for-replication"></a>Replikáció a storage-fiókok létrehozása

Ebben a lépésben a tárfiókok replikációs használatra jönnek létre. A storage-fiókok segítségével később virtuális gépek replikálása. Győződjön meg arról, hogy a tárfiókok ugyanabban a régióban az Azure és a tárolónak jönnek létre. Ezt a lépést kihagyhatja, ha azt tervezi, hogy egy meglévő tárfiókot replikáláshoz használni.

> [!NOTE]
> A helyszíni virtuális gépek premium storage-fiók replikálásakor kell egy további standard szintű storage-fiókot (naplótárolási fiók). A naplózási tárfiók a replikációs naplók egy köztes tárolóként tárolja, mindaddig, amíg a naplók a prémium szintű storage cél alkalmazhatók.
>

```azurepowershell

$PremiumStorageAccount = New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "premiumstorageaccount1" -Location "East Asia" -SkuName Premium_LRS

$LogStorageAccount = New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "logstorageaccount1" -Location "East Asia" -SkuName Standard_LRS

$ReplicationStdStorageAccount= New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "replicationstdstorageaccount1" -Location "East Asia" -SkuName Standard_LRS
```

## <a name="replicate-vmware-vms"></a>VMware virtuális gépek replikálása

A virtuális gépek számára, hogy a vCenter-kiszolgáló felderítése körülbelül 15 – 20 percet vesz igénybe. Miután felderített, egy védhető objektum az Azure Site Recoveryben jön létre minden felderített virtuális géphez. Ebben a lépésben a felderített virtuális gépek közül három replikálódnak az előző lépésben létrehozott Azure Storage-fiókokat.

Szüksége lesz a felderített virtuális gépek védelme érdekében a következő adatokat:

* Replikálandó védhető.
* A tárfiók a virtuális gép replikálása. Ezenkívül a naplók tárolásához szükséges virtuális gépek premium storage-fiók védelme.
* A Folyamatkiszolgáló replikációs használatra. A folyamat rendelkezésre álló kiszolgálók listáját olvassa és menti a ***$ProcessServers [0]***  *(horizontális Felskálázás-ProcessServer)* és ***$ProcessServers [1]*** *(ConfigurationServer)* változókat.
* A fiókot, amellyel a leküldéses telepítés a mobilitási szolgáltatás a gépek szoftvereket. A rendelkezésre álló fiókok listájának lekérése és tárolja a ***$AccountHandles*** változó.
* A védelmitároló-leképezés használatos replikációt a replikációs házirend.
* Az erőforráscsoport, amelyben a virtuális gépek feladatátvételi kell létrehozni.
* Szükség esetén az Azure virtuális hálózat és alhálózat, amelyhez a feladatátviteli virtuális géphez kell csatlakoztatni.

Most már az ebben a táblázatban megadott beállításokat használja a következő virtuális gépek replikálása


|Virtuális gép  |Folyamatkiszolgáló        |Tárfiók              |Log Storage-fiók  |Szabályzat           |A fiók a mobilitási szolgáltatás telepítése|Céloldali erőforráscsoport  | Cél virtuális hálózattal  |Cél alhálózat  |
|-----------------|----------------------|-----------------------------|---------------------|-----------------|-----------------------------------------|-----------------------|-------------------------|---------------|
|Win2K12VM1       |ScaleOut-ProcessServer|premiumstorageaccount1       |logstorageaccount1   |ReplicationPolicy|WindowsAccount                           |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |
|CentOSVM1       |ConfigurationServer   |replicationstdstorageaccount1| –                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |   
|CentOSVM2       |ConfigurationServer   |replicationstdstorageaccount1| –                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |   

 
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

A engedélyezése replikációs feladat sikeres befejeződése után a virtuális gépek kezdeti replikációs elindult. Kezdeti replikálás eltarthat egy ideig, attól függően, a replikálandó adatok mennyisége és a rendelkezésre álló sávszélesség replikálásra. Kezdeti replikálás befejezése után a virtuális gép áthelyezése egy védett állapotban. A virtuális gép egy feladatátvételi tesztet a virtuális gép eléri a védett állapotba hajthat végre, miután hozzáadása helyreállítási tervekhez stb.

A replikációs állapotot és a replikáció állapotát a virtuális gépet a Get-ASRReplicationProtectedItem parancsmaggal ellenőrizheti.

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

## <a name="configure-failover-settings"></a>Feladatátvétel-beállítások konfigurálása

Védett gépek feladatátvételi beállításait a Set-ASRReplicationProtectedItem parancsmaggal lehet frissíteni. A beállításokat, amelyek ezzel a parancsmaggal lehet frissíteni a következők:
* A feladatátvételkor létrehozandó a virtuális gép neve
* A virtuális gép a feladatátvételkor létrehozandó Virtuálisgép-mérete
* Az Azure virtuális hálózatot és alhálózatot, amelyet a hálózati adaptereket a virtuális gép feladatátvételi ekkor csatlakoznia kell
* Felügyelt lemezekre történő feladatátvétel
* A alkalmazni az Azure Hybrid Use Benefit juttatás
* Statikus IP-cím hozzárendelését a cél virtuális hálózattal, hozzá kell rendelni a virtuális gép a feladatátvételt.

Ebben a példában a Virtuálisgép-méretet a virtuális gép hozható létre a virtuális gép feladatátvételi frissítjük *Win2K12VM1* , és adja meg, hogy a virtuális gép használata felügyelt lemezek a feladatátvételkor.

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

1. Vészhelyreállítási próba (teszt feladatátvétel) futtassa az alábbiak szerint:

   ```azurepowershell
   #Test failover of Win2K12VM1 to the test virtual network "V2TestNetwork"

   #Get details of the test failover virtual network to be used
   TestFailovervnet = Get-AzureRmVirtualNetwork -Name "V2TestNetwork" -ResourceGroupName "asrrg" 

   #Start the test failover operation
   $TFOJob = Start-ASRTestFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -AzureVMNetworkId $TestFailovervnet.Id -Direction PrimaryToRecovery
   ```
2. A teszt feladatátvételi feladat sikeres befejeződése után láthatja majd, hogy a virtuális gépek utótaggal *"-tesztelése"* (Win2K12VM1-teszt ebben az esetben) az nevére jön létre az Azure-ban.
3. Most már a teszt virtuális gép feladatátvétele csatlakozhat, és a teszt feladatátvétel érvényesítése.
4. A Start-ASRTestFailoverCleanupJob parancsmaggal feladatátvételi teszt karbantartása. Ez a művelet törli a teszt feladatátvételi művelet részeként létrehozott virtuális gépbe.

   ```azurepowershell
   $Job_TFOCleanup = Start-ASRTestFailoverCleanupJob -ReplicationProtectedItem $ReplicatedVM1
   ```

## <a name="fail-over-to-azure"></a>Feladatátvétel az Azure-bA

Ebben a lépésben azt átadja a feladatokat a virtuális gép Win2K12VM1 egy adott helyreállítási pontot.

1. A feladatátvétel végrehajtásához rendelkezésre álló helyreállítási pontok listájának lekérése:
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

2. Miután sikeresen megtörtént, akkor feladatátvétel érvényesítése a feladatátvételi művelet, és állítsa be a visszirányú replikálás az Azure-ból biztonsági a helyszíni VMware-hely.

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan további feladatainak automatizálása a [Azure Site Recovery PowerShell-referencia ](https://docs.microsoft.com/powershell/module/AzureRM.RecoveryServices.SiteRecovery).
