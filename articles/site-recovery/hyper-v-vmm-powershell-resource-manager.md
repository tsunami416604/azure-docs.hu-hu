---
title: Hyper-V (VMM-mel) vész-helyreállítási szolgáltatás beállítása egy másodlagos helyre az Azure Site Recovery/PowerShell használatával
description: Bemutatja, hogyan állíthatja be a Virtuális-v virtuális gépek vészutáni helyreállítását a VMM-felhőkben egy másodlagos VMM-webhelyre az Azure Site Recovery és a PowerShell használatával.
services: site-recovery
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 1/10/2020
ms.author: sutalasi
ms.openlocfilehash: deef7bfdbc28d744cb81da59d3ffc13a1abee54d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77048614"
---
# <a name="set-up-disaster-recovery-of-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>A Hyper-V virtuális gépek vész-helyreállítási beállítása egy másodlagos helyre a PowerShell (Resource Manager) használatával

Ez a cikk bemutatja, hogyan automatizálhatja a System Center virtuális gépkezelőfelhőiben lévő hyper-V virtuális gépek replikációjának lépéseit egy virtuálisgép-kezelői felhőbe egy másodlagos helyszíni helyen az [Azure Site Recovery](site-recovery-overview.md)használatával.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- Tekintse át [a forgatókönyv-architektúrát és -összetevőket](hyper-v-vmm-architecture.md).
- Minden összetevőre vonatkozóan tekintse át a [támogatási követelményeket](site-recovery-support-matrix-to-sec-site.md).
- Győződjön meg arról, hogy a Virtual Machine Manager-kiszolgálók és a Hyper-V-állomások megfelelnek a [támogatási követelményeknek.](site-recovery-support-matrix-to-sec-site.md)
- Ellenőrizze, hogy a replikálni kívánt virtuális gépek megfelelnek-e a [replikált géptámogatásnak.](site-recovery-support-matrix-to-sec-site.md)

## <a name="prepare-for-network-mapping"></a>A hálózatleképezés előkészítése

[Hálózati leképezési leképezések](hyper-v-vmm-network-mapping.md) a helyszíni Virtuálisgép-kezelő virtuálisgép-hálózatok között a forrás- és a célfelhőkben. A leképezés a következőket hajtja végre:

- A feladatátvételt követően összekapcsolja a virtuális gépeket a megfelelő céloldali virtuálisgép-hálózatokkal.
- Optimális módon helyezi el a virtuális replikagépeket a céloldali Hyper-V gazdakiszolgálókon.
- Ha nem konfigurálja a hálózati hozzárendelést, a replika virtuális gépek nem csatlakoznak a virtuális gép hálózatához a feladatátvétel után.

Készítse elő a virtuálisgép-kezelőt az alábbiak szerint:

- Győződjön meg arról, hogy a [virtuálisgép-kezelő logikai hálózatai](https://docs.microsoft.com/system-center/vmm/network-logical) vannak a forrás- és a célvirtualgép-kezelő kiszolgálóin:
  - A forrásoldali kiszolgálón található logikai hálózatnak ahhoz a forrásfelhőhöz kell tartoznia, amelyikben a Hyper-V gazdagépek találhatók.
  - A céloldali kiszolgálón található logikai hálózatnak a célfelhőhöz kell tartoznia.
- Győződjön meg arról, hogy [a virtuálisgép-hálózatok](https://docs.microsoft.com/system-center/vmm/network-virtual) a forrás-és a cél Virtual Machine Manager-kiszolgálók. A virtuálisgép-hálózatokat minden helyen össze kell kapcsolni a logikai hálózattal.
- A forrásoldali Hyper-V gazdagépeken található virtuális gépeket a forrásoldali virtuálisgép-hálózathoz kell csatlakoztatni.

## <a name="prepare-for-powershell"></a>Felkészülés a PowerShellre

Győződjön meg arról, hogy az Azure PowerShell készen áll a használatra:

- Ha már használja a PowerShellt, frissítsen a 0.8.10-es vagy újabb verzióra. [További információ](/powershell/azureps-cmdlets-docs) a PowerShell beállításáról.
- A PowerShell beállítása és konfigurálása után tekintse át a [szolgáltatásparancsmagokat.](/powershell/azure/overview)
- Ha többet szeretne megtudni arról, hogyan használhatja a paraméterértékeket, bemeneteket és kimeneteket a PowerShellben, olvassa el az [Első lépések](/powershell/azure/get-started-azureps) útmutatót.

## <a name="set-up-a-subscription"></a>Előfizetés beállítása

1. A PowerShellből jelentkezzen be az Azure-fiókjába.

   ```azurepowershell
   $UserName = "<user@live.com>"
   $Password = "<password>"
   $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
   $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
   Connect-AzAccount #-Credential $Cred
   ```

1. Az előfizetések listájának lekérése az előfizetési azonosítókkal. Vegye figyelembe annak az előfizetésnek az azonosítóját, amelyben létre szeretné hozni a Recovery Services-tárolót.

   ```azurepowershell
   Get-AzSubscription
   ```

1. Állítsa be a tároló előfizetését.

   ```azurepowershell
   Set-AzContext –SubscriptionID <subscriptionId>
   ```

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

1. Hozzon létre egy Azure Resource Manager erőforráscsoportot, ha nem rendelkezik ilyen.

   ```azurepowershell
   New-AzResourceGroup -Name #ResourceGroupName -Location #location
   ```

1. Hozzon létre egy új helyreállítási szolgáltatások tároló. Mentse a tárolóobjektumot egy változóba, amelyet később használni szeretne.

   ```azurepowershell
   $vault = New-AzRecoveryServicesVault -Name #vaultname -ResourceGroupName #ResourceGroupName -Location #location
   ```

   A tárolóobjektum ot a `Get-AzRecoveryServicesVault` parancsmag használatával a létrehozás után lekérheti.

## <a name="set-the-vault-context"></a>A tároló környezetének beállítása

1. Egy meglévő tároló lekérése.

   ```azurepowershell
   $vault = Get-AzRecoveryServicesVault -Name #vaultname
   ```

1. Állítsa be a tároló környezetben.

   ```azurepowershell
   Set-AzRecoveryServicesAsrVaultContext -Vault $vault
   ```

## <a name="install-the-site-recovery-provider"></a>A Webhely-helyreállítási szolgáltató telepítése

1. A Virtual Machine Manager gépen hozzon létre egy könyvtárat a következő parancs futtatásával:

   ```azurepowershell
   New-Item -Path C:\ASR -ItemType Directory
   ```

1. Bontsa ki a fájlokat a letöltött szolgáltató telepítőfájljával.

   ```console
   pushd C:\ASR\
   .\AzureSiteRecoveryProvider.exe /x:. /q
   ```

1. Telepítse a szolgáltatót, és várja meg a telepítés befejezését.

   ```console
   .\SetupDr.exe /i
   $installationRegPath = "HKLM:\Software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
   do
   {
     $isNotInstalled = $true;
     if(Test-Path $installationRegPath)
     {
       $isNotInstalled = $false;
     }
   }While($isNotInstalled)
   ```

1. Regisztrálja a kiszolgálót a tárolóban.

   ```console
   $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
   pushd $BinPath
   $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice
   ```

## <a name="create-and-associate-a-replication-policy"></a>Replikációs házirend létrehozása és társítása

1. Hozzon létre replikációs házirendet, ebben az esetben a Hyper-V 2012 R2 esetében, az alábbiak szerint:

   ```azurepowershell
   $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
   $PolicyName = “replicapolicy”
   $RepProvider = HyperVReplica2012R2
   $Recoverypoints = 24                    #specify the number of hours to retain recovery points
   $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
   $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
   $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
   $InitialRepMethod = "Online" #options are "Online" or "Offline"

   $policyresult = New-AzRecoveryServicesAsrPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -NumberOfRecoveryPointsToRetain $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod
   ```

   > [!NOTE]
   > A Virtual Machine Manager-felhő tartalmazhat Hyper-V állomásokat, amelyek a Windows Server különböző verzióit futtatják, de a replikációs házirend az operációs rendszer egy adott verziójára vonatkozik. Ha különböző gazdagépei különböző operációs rendszereken futnak, hozzon létre külön replikációs házirendeket minden rendszerhez. Ha például öt állomás fut Windows Server 2012 rendszeren, és három állomás fut Windows Server 2012 R2 rendszeren, hozzon létre két replikációs házirendet. Minden operációs rendszertípushoz létrehoz egyet.

1. Az elsődleges védelmi tároló (elsődleges Virtual Machine Manager-felhő) és a helyreállítási védelem tároló (helyreállítási Virtual Machine Manager-felhő) lekérése.

   ```azurepowershell
   $PrimaryCloud = "testprimarycloud"
   $primaryprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $PrimaryCloud;

   $RecoveryCloud = "testrecoverycloud"
   $recoveryprotectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $RecoveryCloud;
   ```

1. A rövid név vel létrehozott replikációs házirend beolvasása.

   ```azurepowershell
   $policy = Get-AzRecoveryServicesAsrPolicy -FriendlyName $policyname
   ```

1. Indítsa el a védelmi tároló (Virtual Machine Manager-felhő) társítását a replikációs házirenddel.

   ```azurepowershell
   $associationJob  = New-AzRecoveryServicesAsrProtectionContainerMapping -Policy $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
   ```

1. Várja meg, amíg a házirend-társítási feladat befejeződik. Annak ellenőrzéséhez, hogy a feladat befejeződött-e, használja a következő PowerShell-kódrészletet:

   ```azurepowershell
   $job = Get-AzRecoveryServicesAsrJob -Job $associationJob

   if($job -eq $null -or $job.StateDescription -ne "Completed")
   {
     $isJobLeftForProcessing = $true;
   }
   ```

1. Miután a feladat befejezte a feldolgozást, futtassa a következő parancsot:

   ```azurepowershell
   if($isJobLeftForProcessing)
   {
     Start-Sleep -Seconds 60
   }
   While($isJobLeftForProcessing)
   ```

A művelet befejezésének ellenőrzéséhez kövesse a [Tevékenység figyelése című](#monitor-activity)részt.

##  <a name="configure-network-mapping"></a>Hálózatleképezés konfigurálása

1. Ezzel a paranccsal lekérheti az aktuális tároló kiszolgálóit. A parancs a Site Recovery `$Servers` kiszolgálókat a tömbváltozóban tárolja.

   ```azurepowershell
   $Servers = Get-AzRecoveryServicesAsrFabric
   ```

1. Ezzel a paranccsal beolvassa a forrás virtuálisgép-kezelő kiszolgáló és a cél virtualgép-kezelő kiszolgáló hálózatait.

   ```azurepowershell
   $PrimaryNetworks = Get-AzRecoveryServicesAsrNetwork -Fabric $Servers[0]

   $RecoveryNetworks = Get-AzRecoveryServicesAsrNetwork -Fabric $Servers[1]
   ```

   > [!NOTE]
   > A forrás virtuálisgép-kezelő kiszolgáló lehet a kiszolgálótömb első vagy második kiszolgálója. Ellenőrizze a Virtual Machine Manager kiszolgálónevét, és olvassa be megfelelően a hálózatokat.

1. Ez a parancsmag leképezést hoz létre az elsődleges hálózat és a helyreállítási hálózat között. Az elsődleges hálózatot határozza meg `$PrimaryNetworks`a fő elemeként. A helyreállítási hálózatot határozza meg `$RecoveryNetworks`a ( helyreállítási hálózat) első elemeként.

   ```azurepowershell
   New-AzRecoveryServicesAsrNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]
   ```

## <a name="enable-protection-for-vms"></a>Virtuális gépek védelmének engedélyezése

Miután a kiszolgálók, felhők és hálózatok megfelelően vannak konfigurálva, engedélyezze a virtuális gépek védelmét a felhőben.

1. A védelem engedélyezéséhez futtassa a következő parancsot a védelmi tároló lekéréséhez:

   ```azurepowershell
   $PrimaryProtectionContainer = Get-AzRecoveryServicesAsrProtectionContainer -FriendlyName $PrimaryCloudName
   ```

1. A védelmi entitás (VM) beszereznie az alábbiak szerint:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -FriendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
   ```

1. Engedélyezze a replikációt a virtuális gép számára.

   ```azurepowershell
   $jobResult = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $protectionentity -ProtectionContainerMapping $policy -VmmToVmm
   ```

> [!NOTE]
> Ha a CMK-kompatibilis felügyelt lemezekre szeretne replikálni az Azure-ban, tegye a következő lépéseket az Az PowerShell 3.3.0-s használatával:
>
> 1. Feladatátvétel engedélyezése a felügyelt lemezekre a virtuális gép tulajdonságainak frissítésével
> 1. A `Get-AzRecoveryServicesAsrReplicationProtectedItem` parancsmag segítségével olvassa be a védett elem minden egyes lemezének lemezazonosítóját
> 1. Hozzon létre egy `New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"` szótárobjektumot parancsmag használatával, amely tartalmazza a lemezazonosító lemeztitkosítási készlethez való hozzárendelését. Ezeket a lemeztitkosítási készleteket ön nek kell előre létrehoznia a célrégióban.
> 1. Frissítse a virtuális `Set-AzRecoveryServicesAsrReplicationProtectedItem` gép tulajdonságait a parancsmag használatával a **DiskIdToDiskEncryptionSetMap** paraméterszótár-objektumának átadásával.

## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

Az üzembe helyezés teszteléséhez futtasson egy tesztfeladat-átvételt egyetlen virtuális gépen. Több virtuális gépet tartalmazó helyreállítási tervet is létrehozhat, és futtathat egy tesztfeladat-átvételt a tervhez. A feladatátvételi teszt segítségével elkülönített hálózatban próbálhatja ki a feladatátvételi és helyreállítási mechanizmusokat.

1. Lekérni a virtuális gépet, amelybe a virtuális gépek feladatátvételt.

   ```azurepowershell
   $Servers = Get-AzRecoveryServicesASRFabric
   $RecoveryNetworks = Get-AzRecoveryServicesAsrNetwork -Name $Servers[1]
   ```

1. Tesztfeladat-átvétel végrehajtása.

   Egyetlen virtuális gép esetén:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrTestFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity -VMNetwork $RecoveryNetworks[1]
   ```

   Helyreállítási terv esetén:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrTestFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan -VMNetwork $RecoveryNetworks[1]
   ```

A művelet befejezésének ellenőrzéséhez kövesse a [Tevékenység figyelése című](#monitor-activity)részt.

## <a name="run-planned-and-unplanned-failovers"></a>Tervezett és nem tervezett feladatátvételek futtatása

1. Tervezett feladatátvétel végrehajtása.

   Egyetlen virtuális gép esetén:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrPlannedFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity
   ```

   Helyreállítási terv esetén:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrPlannedFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan
   ```

1. Nem tervezett feladatátvétel végrehajtása.

   Egyetlen virtuális gép esetén:

   ```azurepowershell
   $protectionEntity = Get-AzRecoveryServicesAsrProtectableItem -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

   $jobIDResult = Start-AzRecoveryServicesAsrUnplannedFailoverJob -Direction PrimaryToRecovery -ReplicationProtectedItem $protectionEntity
   ```

   Helyreállítási terv esetén:

   ```azurepowershell
   $recoveryplanname = "test-recovery-plan"

   $recoveryplan = Get-AzRecoveryServicesAsrRecoveryPlan -FriendlyName $recoveryplanname

   $jobIDResult = Start-AzRecoveryServicesAsrUnplannedFailoverJob -Direction PrimaryToRecovery -RecoveryPlan $recoveryplan
   ```

## <a name="monitor-activity"></a>Tevékenység figyelése

A következő parancsokkal figyelheti a feladatátvételi tevékenységet. Várja meg, amíg a feldolgozás befejeződik a feladatok között.

```azurepowershell
Do
{
    $job = Get-AzRecoveryServicesAsrJob -TargetObjectId $associationJob.JobId;
    Write-Host "Job State:{0}, StateDescription:{1}" -f Job.State, $job.StateDescription;
    if($job -eq $null -or $job.StateDescription -ne "Completed")
    {
        $isJobLeftForProcessing = $true;
    }

if($isJobLeftForProcessing)
    {
        Start-Sleep -Seconds 60
    }
}While($isJobLeftForProcessing)
```

## <a name="next-steps"></a>További lépések

[További információ](/powershell/module/az.recoveryservices) a PowerShell-parancsmagokkal rendelkező site recovery-ről.
