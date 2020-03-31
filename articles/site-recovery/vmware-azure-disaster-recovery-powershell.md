---
title: A VMware vész-helyreállítási szolgáltatásának beállítása a PowerShell használatával az Azure Site Revoery-ben
description: Megtudhatja, hogyan állíthatja be a replikációt és a feladatátvételt az Azure-ba a Virtuálisgép-automaták vészhelyreállításához a PowerShell használatával az Azure Site Recovery szolgáltatásban.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.date: 01/10/2020
ms.topic: conceptual
ms.author: sutalasi
ms.openlocfilehash: d2dfaab3d01ea29b0f9ecba1e9d748415bed2edc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257198"
---
# <a name="set-up-disaster-recovery-of-vmware-vms-to-azure-with-powershell"></a>A VMware virtuális gépek vészutáni helyreállításának beállítása az Azure-ba a PowerShell segítségével

Ebben a cikkben megtudhatja, hogyan replikálhatja és feladatátvételi VMware virtuális gépek az Azure-ba az Azure PowerShell használatával.

Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> - Hozzon létre egy Helyreállítási szolgáltatások tároló, és állítsa be a tároló környezetben.
> - Ellenőrizze a kiszolgáló regisztrációját a tárolóban.
> - A replikáció beállítása, beleértve a replikációs házirendet is. Adja hozzá a vCenter-kiszolgálót, és fedezze fel a virtuális gépeket.
> - VCenter-kiszolgáló hozzáadása és felderítés
> - Tárolófiókok létrehozása replikációs naplók vagy adatok tárolásához és a virtuális gépek replikálásához.
> - Feladatátvétel végrehajtása. Konfigurálja a feladatátvételi beállításokat, hajtson végre egy beállítást a virtuális gépek replikálásához.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Előkészületek:

- Ismernie kell a [forgatókönyv-architektúrát és az összetevőket](vmware-azure-architecture.md).
- Minden összetevőre vonatkozóan tekintse át a [támogatási követelményeket](site-recovery-support-matrix-to-azure.md).
- Az Azure PowerShell-modullal `Az` rendelkezik. Ha telepítenie vagy frissítenie kell az Azure PowerShellt, kövesse az [alábbi útmutatót az Azure PowerShell telepítéséhez és konfigurálásához.](/powershell/azure/install-az-ps)

## <a name="log-into-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be Azure-előfizetésébe a Connect-AzAccount parancsmag használatával:

```azurepowershell
Connect-AzAccount
```
Válassza ki azt az Azure-előfizetést, amelybe a VMware virtuális gépeit replikálni szeretné. A Get-AzSubscription parancsmag segítségével leszeretné késelni a hozzáféréssel rendelkező Azure-előfizetések listáját. Válassza ki az Azure-előfizetést a Select-AzSubscription parancsmag használatával.

```azurepowershell
Select-AzSubscription -SubscriptionName "ASR Test Subscription"
```
## <a name="set-up-a-recovery-services-vault"></a>Helyreállítási tár beállítása

1. Hozzon létre egy erőforráscsoportot, amelyben a Recovery Services-tároló létrehozásához. Az alábbi példában az erőforráscsoport neve VMwareDRtoAzurePS, és a kelet-ázsiai régióban jön létre.

   ```azurepowershell
   New-AzResourceGroup -Name "VMwareDRtoAzurePS" -Location "East Asia"
   ```
   ```
   ResourceGroupName : VMwareDRtoAzurePS
   Location          : eastasia
   ProvisioningState : Succeeded
   Tags              :
   ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRtoAzurePS
   ```

2. Hozzon létre egy helyreállítási szolgáltatások tároló. Az alábbi példában a helyreállítási szolgáltatások tárolójának neve VMwareDRToAzurePs, és a kelet-ázsiai régióban és az előző lépésben létrehozott erőforráscsoportban jön létre.

   ```azurepowershell
   New-AzRecoveryServicesVault -Name "VMwareDRToAzurePs" -Location "East Asia" -ResourceGroupName "VMwareDRToAzurePs"
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

3. Töltse le a trezor regisztrációs kulcsát a trezorhoz. A tároló regisztrációs kulcs a helyszíni konfigurációs kiszolgáló regisztrálására szolgál a tárolóba. A regisztráció a Configuration Server szoftvertelepítési folyamatának része.

   ```azurepowershell
   #Get the vault object by name and resource group and save it to the $vault PowerShell variable 
   $vault = Get-AzRecoveryServicesVault -Name "VMwareDRToAzurePS" -ResourceGroupName "VMwareDRToAzurePS"

   #Download vault registration key to the path C:\Work
   Get-AzRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   ```
   ```
   FilePath
   --------
   C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials
   ```

4. Használja a letöltött tároló regisztrációs kulcsát, és kövesse az alábbi cikkekben leírt lépéseket a konfigurációs kiszolgáló telepítésének és regisztrálásának befejezéséhez.
   - [Válaszd ki a védelmi célokat](vmware-azure-set-up-source.md#choose-your-protection-goals)
   - [A forráskörnyezet beállítása](vmware-azure-set-up-source.md#set-up-the-configuration-server)

### <a name="set-the-vault-context"></a>A tároló környezetének beállítása

Állítsa be a tároló környezetben a Set-ASRVaultContext parancsmag használatával. A beállítás után a PowerShell-munkamenet ben végrehajtott azure site recovery-műveletek a kiválasztott tároló környezetében lesznek végrehajtva.

> [!TIP]
> Az Azure Site Recovery PowerShell modul (Az.RecoveryServices modul) a legtöbb parancsmag hoz könnyen használható aliasokat tartalmazza. A modulban lévő parancsmagok a * \<>-**AzRecoveryServicesAsr**\<object>* művelet és az * \<>-**ASR**\<objektum>*. A parancsmag aliasok a könnyű használat érdekében.

Az alábbi példában a tároló részleteit a $vault változó a PowerShell-munkamenet tárolókörnyezetének megadásához.

   ```azurepowershell
   Set-ASRVaultContext -Vault $vault
   ```
   ```
   ResourceName      ResourceGroupName ResourceNamespace          ResourceType
   ------------      ----------------- -----------------          -----------
   VMwareDRToAzurePs VMwareDRToAzurePs Microsoft.RecoveryServices vaults
   ```

A Set-ASRVaultContext parancsmag alternatívájaként az Import-AzRecoveryServicesAsrVaultSettingsFile parancsmag segítségével is beállíthatja a tároló környezetét. Adja meg azt az elérési utat, amelyen a tároló regisztrációs kulcsfájlja az Import-AzRecoveryServicesAsrVaultSettingsFile parancsmag -path paramétereként található. Példa:

   ```azurepowershell
   Get-AzRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
   Import-AzRecoveryServicesAsrVaultSettingsFile -Path "C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials"
   ```
A cikk további szakaszai feltételezik, hogy az Azure Site Recovery-műveletek tárolókörnyezete be van állítva.

## <a name="validate-vault-registration"></a>Tároló regisztrációjának ellenőrzése

Ebben a példában a következők vannak:

- Ehhez a tárolóhoz egy konfigurációs kiszolgáló (**ConfigurationServer**) van regisztrálva.
- Egy további folyamatkiszolgáló **(ScaleOut-ProcessServer)** regisztrálva van a *ConfigurationServer rendszerben*
- A konfigurációs kiszolgálón fiókok (**vCenter_account**, **WindowsAccount**, **LinuxAccount**) lettek beállítva. Ezek a fiókok a vCenter-kiszolgáló hozzáadására, a virtuális gépek felderítésére, valamint a replikálandó Windows- és Linux-kiszolgálókon a mobilitási szolgáltatás szoftverének leküldéses telepítésére szolgálnak.

1. A regisztrált konfigurációs kiszolgálókat egy hálóobjektum képviseli a Site Recovery szolgáltatásban. A tárolóban lévő hálóobjektumok listájának leése és a konfigurációs kiszolgáló azonosítása.

   ```azurepowershell
   # Verify that the Configuration server is successfully registered to the vault
   $ASRFabrics = Get-AzRecoveryServicesAsrFabric
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

2. Azonosítsa a gépek replikálására használható folyamatkiszolgálókat.

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

   A fenti kimenetből ***$ProcessServers[0]*** a *ScaleOut-ProcessServer-nek* felel ***meg, $ProcessServers[1]*** pedig a *ConfigurationServer* Process Server szerepkörének felel meg.

3. Azonosítsa a konfigurációs kiszolgálón beállított fiókokat.

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

   A fenti kimenetből ***$AccountHandles[0]*** a fiók *vCenter_account*, ***$AccountHandles[1]*** felel meg a *WindowsAccount*fiókhoz , és ***$AccountHandles[2]*** a *LinuxAccount* fiókhoz

## <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

Ebben a lépésben két replikációs házirend jön létre. Az egyik szabályzat a VMware virtuális gépek Replikálására az Azure-ba, a másik pedig az Azure-ban futó virtuális gépek replikálása a helyszíni VMware-webhelyre.

> [!NOTE]
> A legtöbb Azure Site Recovery műveletek aszinkron módon hajtják végre. Amikor egy műveletet kezdeményez, egy Azure Site Recovery feladat elküldésre kerül, és egy feladatkövetési objektumot ad vissza. Ez a feladatkövetési objektum a művelet állapotának figyelésére használható.

1. Hozzon létre egy *ReplicationPolicy* nevű replikációs házirendet a VMware virtuális gépek Replikálásához az Azure-ba a megadott tulajdonságokkal.

   ```azurepowershell
   $Job_PolicyCreate = New-AzRecoveryServicesAsrPolicy -VMwareToAzure -Name "ReplicationPolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60

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

2. Hozzon létre egy replikációs szabályzatot az Azure-ból a helyszíni VMware-helyre történő feladat-visszavételhez.

   ```azurepowershell
   $Job_FailbackPolicyCreate = New-AzRecoveryServicesAsrPolicy -AzureToVMware -Name "ReplicationPolicy-Failback" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60
   ```

   A *$Job_FailbackPolicyCreate* $Job-ben található feladat részleteivel nyomon követheti a művelet befejezéséig.

   * Hozzon létre egy védelmi tárolóleképezést a replikációs házirendek leképezéséhez a konfigurációs kiszolgálóval.

   ```azurepowershell
   #Get the protection container corresponding to the Configuration Server
   $ProtectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $ASRFabrics[0]

   #Get the replication policies to map by name.
   $ReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "ReplicationPolicy"
   $FailbackReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "ReplicationPolicy-Failback"

   # Associate the replication policies to the protection container corresponding to the Configuration Server.

   $Job_AssociatePolicy = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "PolicyAssociation1" -PrimaryProtectionContainer $ProtectionContainer -Policy $ReplicationPolicy

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
    $Job_AssociateFailbackPolicy = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "FailbackPolicyAssociation" -PrimaryProtectionContainer $ProtectionContainer -RecoveryProtectionContainer $ProtectionContainer -Policy $FailbackReplicationPolicy

   # Check the job status
   while (($Job_AssociateFailbackPolicy.State -eq "InProgress") -or ($Job_AssociateFailbackPolicy.State -eq "NotStarted")){
           sleep 10;
           $Job_AssociateFailbackPolicy = Get-ASRJob -Job $Job_AssociateFailbackPolicy
   }
   $Job_AssociateFailbackPolicy.State

   ```

## <a name="add-a-vcenter-server-and-discover-vms"></a>VCenter-kiszolgáló hozzáadása és virtuális gépek felderítése

VCenter-kiszolgáló hozzáadása IP-cím vagy állomásnév alapján. A **-port** paraméter megadja a vCenter-kiszolgáló nóca portját, **a -Name** paraméter a vCenter-kiszolgálóhoz használandó rövid nevet adja meg, a **-Account** paraméter pedig a konfigurációs kiszolgálón a vCenter-kiszolgáló által kezelt virtuális gépek felderítéséhez használandó fiókleírót adja meg.

```azurepowershell
# The $AccountHandles[0] variable holds details of vCenter_account

$Job_AddvCenterServer = New-AzRecoveryServicesAsrvCenter -Fabric $ASRFabrics[0] -Name "MyvCenterServer" -IpOrHostName "10.150.24.63" -Account $AccountHandles[0] -Port 443

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

## <a name="create-storage-accounts-for-replication"></a>Tárfiókok létrehozása replikációhoz

**Felügyelt lemezre írni, használja [A Powershell Az.RecoveryServices modul 2.0.0-tól.](https://www.powershellgallery.com/packages/Az.RecoveryServices/2.0.0-preview)** Csak egy naplótárfiókot kell létrehoznia. Ajánlott egy szabványos fióktípus és LRS redundancia használata, mivel csak ideiglenes naplók tárolására szolgál. Győződjön meg arról, hogy a tárfiók ugyanabban az Azure-régióban jön létre, mint a tároló.

Ha az Az.RecoveryServices modul 2.0.0-nál régebbi verzióját használja, az alábbi lépésekkel hozzon létre tárfiókokat. Ezek a tárfiókok később a virtuális gépek replikálására szolgálnak. Győződjön meg arról, hogy a tárfiókok ugyanabban az Azure-régióban jönnek létre, mint a tároló. Ezt a lépést kihagyhatja, ha egy meglévő tárfiókot kíván használni a replikációhoz.

> [!NOTE]
> A helyszíni virtuális gépek replikálása során egy prémium szintű tárfiókba, meg kell adnia egy további standard szintű tárfiók (naplótárfiók). A naplótár-fiók köztes tárolóként tárolja a replikációs naplókat, amíg a naplók nem alkalmazhatók a prémium szintű tárolási célon.
>

```azurepowershell

$PremiumStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "premiumstorageaccount1" -Location "East Asia" -SkuName Premium_LRS

$LogStorageAccount = New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "logstorageaccount1" -Location "East Asia" -SkuName Standard_LRS

$ReplicationStdStorageAccount= New-AzStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "replicationstdstorageaccount1" -Location "East Asia" -SkuName Standard_LRS
```

## <a name="replicate-vmware-vms"></a>VMware virtuális gépek replikálása

Körülbelül 15–20 percet vesz igénybe a virtuális gépek felderítése a vCenter-kiszolgálóról. Miután felfedezték, egy védett elem objektum jön létre az Azure Site Recovery minden felderített virtuális gép. Ebben a lépésben a felderített virtuális gépek három replikálódik az előző lépésben létrehozott Azure Storage-fiókok.

A felderített virtuális gépek védelméhez a következő adatokra lesz szüksége:

* A replikálandó védett elem.
* A tárfiók replikálni a virtuális gépet (csak akkor, ha a tárfiókreplikálása). 
* A virtuális gépek prémium szintű tárfiókba vagy felügyelt lemezre való védelméhez naplótár szükséges.
* A replikációhoz használandó folyamatkiszolgáló. Az elérhető folyamatkiszolgálók listáját a ***$ProcessServers[0]***  *(ScaleOut-ProcessServer)* és ***$ProcessServers[1]*** *(ConfigurationServer)* változókban sikerült beolvasni és menteni.
* A mobilszolgáltatás szoftverének a gépekre történő leküldéses telepítéséhez használt fiók. Az elérhető fiókok listáját a rendszer beolvassa és tárolja a ***$AccountHandles*** változóban.
* A replikációhoz használandó replikációs házirend védelmi tárolóleképezése.
* Az erőforráscsoport, amelyben a virtuális gépeket feladatátvételkor kell létrehozni.
* Szükség esetén az Azure virtuális hálózat és alhálózat, amelyhez a feladatátvételi virtuális gép csatlakozik.

Most replikálja a következő virtuális gépeket a táblázatban megadott beállításokkal


|Virtuális gép  |Folyamatkiszolgáló        |Tárfiók              |Tárfiók naplózása  |Szabályzat           |A Mobilitási szolgáltatás telepítésével kapcsolatos fiók|Cél erőforráscsoport  | Virtuális hálózat megcélzása  |Cél alhálózat  |
|-----------------|----------------------|-----------------------------|---------------------|-----------------|-----------------------------------------|-----------------------|-------------------------|---------------|
|CentOSVM1       |ConfigurationServer kiszolgáló   |N/A| logstorageaccount1                 |ReplicationPolicy (Replikációs házirend)|LinuxFiók                             |VMwareDRAzurePs      |ASR-vnet                 |Alhálózat-1       |
|Win2K12VM1       |ScaleOut-ProcessServer|prémiumtárszámla1       |logstorageaccount1   |ReplicationPolicy (Replikációs házirend)|WindowsAccount                           |VMwareDRAzurePs      |ASR-vnet                 |Alhálózat-1       |   
|CentOSVM2       |ConfigurationServer kiszolgáló   |replicationstdstorageaccount1| N/A                 |ReplicationPolicy (Replikációs házirend)|LinuxFiók                             |VMwareDRAzurePs      |ASR-vnet                 |Alhálózat-1       |   


```azurepowershell

#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg" 

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap  = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1 using the Az.RecoveryServices module 2.0.0 onwards to replicate to managed disks
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

# Alternatively, if the virtual machine CentOSVM1 has CMK enabled disks, enable replication using Az module 3.3.0 onwards as below
# $diskID is the Disk Encryption Set ID to be used for all replica managed disks and target managed disks in the target region
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId -DiskEncryptionSetId $diskId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine Win2K12VM1
$VM2 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "Win2K12VM1"

# Enable replication for virtual machine Win2K12VM1
$Job_EnableReplication2 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM2 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $PremiumStorageAccount.Id -LogStorageAccountId $LogStorageAccount.Id -ProcessServer $ProcessServers[0] -Account $AccountHandles[1] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine CentOSVM2
$VM3 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM2"

# Enable replication for virtual machine CentOSVM2
$Job_EnableReplication3 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM3 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

```

Miután a replikációs feladat engedélyezése sikeresen befejeződött, a virtuális gépek kezdeti replikációja elindul. A kezdeti replikáció eltarthat egy ideig a replikálandó adatok mennyiségétől és a replikációhoz rendelkezésre álló sávszélességtől függően. A kezdeti replikáció befejezése után a virtuális gép védett állapotba kerül. Miután a virtuális gép eléri a védett állapotban végezhet egy teszt feladatátvétela a virtuális gép, add hozzá a helyreállítási tervek, stb.

A Get-ASRReplicationProtectedItem parancsmagsegítségével ellenőrizheti a virtuális gép replikációs állapotát és replikációs állapotát.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState                 ReplicationHealth
------------ ---------------                 -----------------
CentOSVM1    Protected                       Normal
CentOSVM2    InitialReplicationInProgress    Normal
Win2K12VM1   Protected                       Normal
```

## <a name="configure-failover-settings"></a>Feladatátvételi beállítások konfigurálása

A védett gépek feladatátvételi beállításai frissíthetők a Set-ASRReplicationProtectedItem parancsmag használatával. A parancsmagon keresztül frissíthető beállítások a következők:
* A feladatátvételkor létrehozandó virtuális gép neve
* A feladatátvételkor létrehozandó virtuális gép virtuális gépmérete
* Az Azure virtuális hálózat és alhálózat, amelyhez a virtuális gép hálózati adaptereit feladatátvételesetén csatlakoztatni kell
* Feladatátvétel a felügyelt lemezekre
* Azure hibrid használati előny alkalmazása
* Rendeljen hozzá egy statikus IP-címet a cél virtuális hálózatból a feladatátvételi rendszerhez rendelve.

Ebben a példában frissítjük a virtuális gép méretét a *Win2K12VM1* virtuális gép feladatátvételkor létrehozandó virtuális gép mérete, és megadhatjuk, hogy a virtuális gép felügyelt lemezeket használjon feladatátvételkor.

```azurepowershell
$ReplicatedVM1 = Get-AzRecoveryServicesAsrReplicationProtectedItem -FriendlyName "Win2K12VM1" -ProtectionContainer $ProtectionContainer

Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $ReplicatedVM1 -Size "Standard_DS11" -UseManagedDisk True
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

1. A VÉSZ-gyakorlat (tesztfeladat-átvétel) futtatása az alábbiak szerint:

   ```azurepowershell
   #Test failover of Win2K12VM1 to the test virtual network "V2TestNetwork"

   #Get details of the test failover virtual network to be used
   TestFailovervnet = Get-AzVirtualNetwork -Name "V2TestNetwork" -ResourceGroupName "asrrg" 

   #Start the test failover operation
   $TFOJob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -AzureVMNetworkId $TestFailovervnet.Id -Direction PrimaryToRecovery
   ```
2. Miután a teszt feladatátvételi feladat sikeresen befejeződött, észre fogja venni, hogy egy virtuális gép utótag *"-teszt"* (Win2K12VM1-test ebben az esetben) a nevét hoz létre az Azure-ban.
3. Most már csatlakozhat a virtuális gépen átvett feladatátvételi feladathoz, és érvényesítheti a teszt feladatátvételt.
4. Törölje a teszt feladatátvételt a Start-ASRTestFailoverCleanupJob parancsmag használatával. Ez a művelet törli a teszt feladatátvételi művelet részeként létrehozott virtuális gépet.

   ```azurepowershell
   $Job_TFOCleanup = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $ReplicatedVM1
   ```

## <a name="fail-over-to-azure"></a>Feladatátvétel az Azure-ba

Ebben a lépésben a win2K12VM1 virtuális gép egy adott helyreállítási pont.

1. A feladatátvételhez használható elérhető helyreállítási pontok listájának beszerzése:
   ```azurepowershell
   # Get the list of available recovery points for Win2K12VM1
   $RecoveryPoints = Get-AzRecoveryServicesAsrRecoveryPoint -ReplicationProtectedItem $ReplicatedVM1
   "{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[0].RecoveryPointTime
   ```
   ```
   CrashConsistent 11/24/2017 5:28:25 PM
   ```
   ```azurepowershell

   #Start the failover job
   $Job_Failover = Start-AzRecoveryServicesAsrUnplannedFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[0]
   do {
           $Job_Failover = Get-ASRJob -Job $Job_Failover;
           sleep 60;
   } while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))
   $Job_Failover.State
   ```
   ```
   Succeeded
   ```

2. Miután sikeresen sikeresen sikeresen sikeresen sikeresen átvételre, véglegesítheti a feladatátvételi műveletet, és állítsa be a fordított replikáció az Azure-ból vissza a helyszíni VMware-hely.

## <a name="next-steps"></a>További lépések
Megtudhatja, hogy miként automatizálhat további feladatokat az [Azure Site Recovery PowerShell-hivatkozáshasználatával.](https://docs.microsoft.com/powershell/module/Az.RecoveryServices)
