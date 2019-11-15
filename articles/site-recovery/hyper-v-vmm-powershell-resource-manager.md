---
title: A Hyper-V (VMM) szolgáltatás vész-helyreállításának beállítása másodlagos helyre Azure Site Recovery/PowerShell-lel
description: Ismerteti, hogyan állítható be a VMM-felhőkben futó Hyper-V virtuális gépek vész-helyreállítási folyamata egy másodlagos VMM-helyre a Azure Site Recovery és a PowerShell használatával.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: sutalasi
ms.openlocfilehash: 2fc66514bdf33611f9e6266d35a2d537fe3b9261
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084908"
---
# <a name="set-up-disaster-recovery-of-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>A Hyper-V virtuális gépek vész-helyreállításának beállítása másodlagos helyre a PowerShell használatával (Resource Manager)

Ez a cikk bemutatja, hogyan automatizálható a System Center Virtual Machine Manager-felhőben lévő Hyper-V virtuális gépek replikálása a Virtual Machine Manager felhőbe egy másodlagos helyszíni helyen a [Azure site Recovery](site-recovery-overview.md)használatával.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- Tekintse át [a forgatókönyv-architektúrát és -összetevőket](hyper-v-vmm-architecture.md).
- Minden összetevőre vonatkozóan tekintse át a [támogatási követelményeket](site-recovery-support-matrix-to-sec-site.md).
- Győződjön meg arról, hogy a Virtual Machine Manager-kiszolgálók és a Hyper-V-gazdagépek megfelelnek a [támogatási követelményeknek](site-recovery-support-matrix-to-sec-site.md).
- Győződjön meg arról, hogy a replikálni kívánt virtuális gépek megfelelnek a [replikált gépek támogatásának](site-recovery-support-matrix-to-sec-site.md).


## <a name="prepare-for-network-mapping"></a>A hálózatleképezés előkészítése

[Hálózati leképezési](hyper-v-vmm-network-mapping.md) térképek a helyszíni Virtual Machine Manager virtuálisgép-hálózatok között a forrás-és a cél felhőkben. A leképezés a következőket hajtja végre:

- A feladatátvételt követően összekapcsolja a virtuális gépeket a megfelelő céloldali virtuálisgép-hálózatokkal. 
- Optimális módon helyezi el a virtuális replikagépeket a céloldali Hyper-V gazdakiszolgálókon. 
- Ha nem konfigurálja a hálózati leképezést, a replika virtuális gépek nem lesznek csatlakoztatva a virtuálisgép-hálózathoz a feladatátvételt követően.

A Virtual Machine Manager előkészítése a következőképpen történik:

* Győződjön meg arról, hogy rendelkezik [Virtual Machine Manager logikai hálózatokkal](https://docs.microsoft.com/system-center/vmm/network-logical) a forrás és a cél Virtual Machine Manager kiszolgálókon:

    - A forrásoldali kiszolgálón található logikai hálózatnak ahhoz a forrásfelhőhöz kell tartoznia, amelyikben a Hyper-V gazdagépek találhatók.
    - A céloldali kiszolgálón található logikai hálózatnak a célfelhőhöz kell tartoznia.
* Győződjön meg arról, hogy rendelkezik virtuálisgép- [hálózatokkal](https://docs.microsoft.com/system-center/vmm/network-virtual) a forrás-és a cél Virtual Machine Manager-kiszolgálókon. A virtuálisgép-hálózatokat minden helyen össze kell kapcsolni a logikai hálózattal.
* A forrásoldali Hyper-V gazdagépeken található virtuális gépeket a forrásoldali virtuálisgép-hálózathoz kell csatlakoztatni. 

## <a name="prepare-for-powershell"></a>Felkészülés a PowerShellre

Győződjön meg arról, hogy Azure PowerShell készen áll:

- Ha már használja a PowerShellt, frissítsen a 0.8.10 vagy újabb verzióra. [További](/powershell/azureps-cmdlets-docs) információ a PowerShell beállításáról.
- A PowerShell beállítása és konfigurálása után tekintse át a [szolgáltatás-parancsmagokat](/powershell/azure/overview).
- Ha többet szeretne megtudni a paraméterek értékeinek, bemenetének és kimenetének a PowerShellben való használatáról, olvassa el az [első lépések](/powershell/azure/get-started-azureps) útmutatóját.

## <a name="set-up-a-subscription"></a>Előfizetés beállítása
1. Jelentkezzen be az Azure-fiókjába a PowerShellből.

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Connect-AzAccount #-Credential $Cred
2. Az előfizetések listájának beolvasása az előfizetés-azonosítókkal. Jegyezze fel annak az előfizetésnek az AZONOSÍTÓját, amelyben létre szeretné hozni a Recovery Services-tárolót. 

        Get-AzSubscription
3. Állítsa be a tároló előfizetését.

        Set-AzContext –SubscriptionID <subscriptionId>

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása
1. Ha még nem rendelkezik ilyennel, hozzon létre egy Azure Resource Manager erőforráscsoportot.

        New-AzResourceGroup -Name #ResourceGroupName -Location #location
2. Hozzon létre egy új Recovery Services-tárolót. Mentse a tároló objektumot egy változóban, amelyet később szeretne használni. 

        $vault = New-AzRecoveryServicesVault -Name #vaultname -ResourceGroupName #ResourceGroupName -Location #location
   
    A tároló objektumot a Get-AzRecoveryServicesVault parancsmag használatával kérheti le.

## <a name="set-the-vault-context"></a>A tár környezetének beállítása
1. Meglévő tár beolvasása.

       $vault = Get-AzRecoveryServicesVault -Name #vaultname
2. Állítsa be a tár környezetét.

       Set-AzSiteRecoveryVaultSettings -ARSVault $vault

## <a name="install-the-site-recovery-provider"></a>A Site Recovery-szolgáltató telepítése
1. A Virtual Machine Manager gépen hozzon létre egy könyvtárat a következő parancs futtatásával:

       New-Item c:\ASR -type directory
2. Bontsa ki a fájlokat a letöltött szolgáltató telepítési fájljának használatával.

       pushd C:\ASR\
       .\AzureSiteRecoveryProvider.exe /x:. /q
3. Telepítse a szolgáltatót, és várjon, amíg a telepítés befejeződik.

       .\SetupDr.exe /i
       $installationRegPath = "hklm:\software\Microsoft\Microsoft System Center Virtual Machine Manager Server\DRAdapter"
       do
       {
         $isNotInstalled = $true;
         if(Test-Path $installationRegPath)
         {
           $isNotInstalled = $false;
         }
       }While($isNotInstalled)

4. Regisztrálja a kiszolgálót a tárolóban.

       $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
       pushd $BinPath
       $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

## <a name="create-and-associate-a-replication-policy"></a>Replikációs házirend létrehozása és hozzárendelése
1. Hozzon létre egy replikációs házirendet, ebben az esetben a Hyper-V 2012 R2 esetében a következő módon:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $RepProvider = HyperVReplica2012R2
        $Recoverypoints = 24                    #specify the number of hours to retain recovery pints
        $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
        $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
        $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
        $InitialRepMethod = "Online" #options are "Online" or "Offline"

        $policyresult = New-AzSiteRecoveryPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod

    > [!NOTE]
    > A Virtual Machine Manager felhő tartalmazhat olyan Hyper-V-gazdagépeket, amelyek a Windows Server különböző verzióit futtatják, de a replikációs házirend az operációs rendszer egy adott verziójára vonatkozik. Ha különböző operációs rendszereken futó gazdagépekkel rendelkezik, hozzon létre külön replikációs házirendeket az egyes rendszerekhez. Ha például öt gazdagép fut a Windows Server 2012-on, a Windows Server 2012 R2-ben pedig három gazdagép fut, hozzon létre két replikációs házirendet. Mindegyik operációs rendszerhez létre kell hoznia egyet.

2. Kérje le az elsődleges védelmi tárolót (az elsődleges Virtual Machine Manager felhőt) és a helyreállítási védelmi tárolót (Recovery Virtual Machine Manager Cloud).

       $PrimaryCloud = "testprimarycloud"
       $primaryprotectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

       $RecoveryCloud = "testrecoverycloud"
       $recoveryprotectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
3. Kérje le a létrehozott replikációs szabályzatot a rövid név használatával.

       $policy = Get-AzSiteRecoveryPolicy -FriendlyName $policyname
4. Indítsa el a védelmi tároló (Virtual Machine Manager felhő) társítását a replikációs házirenddel.

       $associationJob  = Start-AzSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
5. Várjon, amíg a házirend-hozzárendelési feladatok befejeződik. A következő PowerShell-kódrészlettel ellenőrizze, hogy a feladatok befejeződtek-e:

       $job = Get-AzSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }

6. Miután a folyamat befejezte a feldolgozást, futtassa a következő parancsot:

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

A művelet befejezésének ellenőrzéséhez kövesse a [tevékenység figyelése](#monitor-activity)című témakör lépéseit.

##  <a name="configure-network-mapping"></a>Hálózatleképezés konfigurálása
1. Ezzel a paranccsal lekérheti a kiszolgálókat az aktuális tárolóhoz. A parancs a $Servers Array változóban tárolja a Site Recovery-kiszolgálókat.

        $Servers = Get-AzSiteRecoveryServer
2. Futtassa ezt a parancsot a forrás Virtual Machine Manager-kiszolgáló és a cél Virtual Machine Manager-kiszolgáló hálózatának beolvasásához.

        $PrimaryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[1]

    > [!NOTE]
    > A forrás Virtual Machine Manager kiszolgáló lehet az első vagy második a kiszolgáló tömbben. Győződjön meg arról, hogy Virtual Machine Manager a kiszolgálók neveit, és megfelelően kéri le a hálózatokat.


3. Ez a parancsmag létrehoz egy leképezést az elsődleges hálózat és a helyreállítási hálózat között. A $PrimaryNetworks első elemeként adja meg az elsődleges hálózatot. A helyreállítási hálózatot a $RecoveryNetworks első elemeként adja meg.

        New-AzSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]


## <a name="enable-protection-for-vms"></a>Virtuális gépek védelmének engedélyezése
Miután a kiszolgálók, a felhők és a hálózatok megfelelően vannak konfigurálva, engedélyezze a Felhőbeli virtuális gépek védelmét.

1. A védelem engedélyezéséhez futtassa a következő parancsot a védelmi tároló lekéréséhez:

          $PrimaryProtectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
2. Szerezze be a védelmi entitást (VM) a következőképpen:

           $protectionEntity = Get-AzSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
3. Engedélyezze a virtuális gép replikálását.

          $jobResult = Set-AzSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy

## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

Az üzemelő példány teszteléséhez futtasson feladatátvételi tesztet egyetlen virtuális géphez. Létrehozhat egy olyan helyreállítási tervet is, amely több virtuális gépet is tartalmaz, és feladatátvételi tesztet futtat a csomaghoz. A feladatátvételi teszt segítségével elkülönített hálózatban próbálhatja ki a feladatátvételi és helyreállítási mechanizmusokat.

1. Kérje le azt a virtuális gépet, amelybe a virtuális gépek feladatátvételt hajtanak végre.

       $Servers = Get-AzSiteRecoveryServer
       $RecoveryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[1]

2. Végezzen feladatátvételi tesztet.

   Egyetlen virtuális gép esetén:

        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1]
    
   Helyreállítási tervhez:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1]

A művelet befejezésének ellenőrzéséhez kövesse a [tevékenység figyelése](#monitor-activity)című témakör lépéseit.

## <a name="run-planned-and-unplanned-failovers"></a>Tervezett és nem tervezett feladatátvételek futtatása

1. Végezzen el egy tervezett feladatátvételt.

   Egyetlen virtuális gép esetén:

        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   Helyreállítási tervhez:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

2. Végezzen nem tervezett feladatátvételt.

   Egyetlen virtuális gép esetén:
        
        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   Helyreállítási tervhez:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

## <a name="monitor-activity"></a>Tevékenység figyelése
A feladatátvételi tevékenység figyeléséhez használja a következő parancsokat. Várjon, amíg a feldolgozás befejeződik a feladatok között.

    Do
    {
        $job = Get-AzureSiteRecoveryJob -Id $associationJob.JobId;
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



## <a name="next-steps"></a>Következő lépések

[További](/powershell/module/az.recoveryservices) információ a site Recovery Resource Manager PowerShell-parancsmagokkal.
