---
title: A Virtual Machine Manager-felhőkben a Hyper-V virtuális gépek replikálása másodlagos helyre a PowerShell-lel (Azure Resource Manager) |} A Microsoft Docs
description: Ismerteti, hogyan lehet a Virtual Machine Manager-felhőkben a Hyper-V virtuális gépek replikálása másodlagos Virtual Machine Manager-helyre PowerShell (Resource Manager) használatával
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: sutalasi
ms.openlocfilehash: 896b3cb3c40673dfbc7269cab1d37790f78912b0
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48041356"
---
# <a name="replicate-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>Hyper-V virtuális gépek replikálása másodlagos helyre a PowerShell (Resource Manager) használatával

Ez a cikk bemutatja, hogyan automatizálhatja a lépéseket, replikációját a Hyper-V virtuális gépeket a System Center Virtual Machine Manager-felhőkben a Virtual Machine Manager cloud egy másodlagos helyszíni hely használatával [Azure Site Recovery](site-recovery-overview.md).

## <a name="prerequisites"></a>Előfeltételek

- Tekintse át a [forgatókönyv-architektúra és összetevők](hyper-v-vmm-architecture.md).
- Minden összetevőre vonatkozóan tekintse át a [támogatási követelményeket](site-recovery-support-matrix-to-sec-site.md).
- Győződjön meg arról, hogy a Virtual Machine Manager-kiszolgálók és a Hyper-V-gazdagépek megfelelnek a [memóriakonfigurációt](site-recovery-support-matrix-to-sec-site.md).
- Ellenőrizze, hogy a replikálni kívánt virtuális gépek megfelelnek [replikált gépek támogatása](site-recovery-support-matrix-to-sec-site.md).


## <a name="prepare-for-network-mapping"></a>A hálózatleképezés előkészítése

[A hálózatleképezés](hyper-v-vmm-network-mapping.md) közötti leképezéseket a helyszíni adatforrás és a célfelhő Virtual Machine Manager Virtuálisgép-hálózatok. Leképezés a következőket teszi:

- Virtuális gépek a feladatátvételt követően megfelelő céloldali Virtuálisgép-hálózatok kapcsolódik. 
- Replika virtuális gépek optimálisan helyezi el a cél Hyper-V gazdakiszolgálókra. 
- Ha nem konfigurálja a hálózatleképezést, a replika virtuális gépek nem csatlakozik Virtuálisgép-hálózat a feladatátvételt követően.

Virtual Machine Manager készítse elő a következőképpen:

* Ellenőrizze, hogy [Virtual Machine Manager logikai hálózatok](https://docs.microsoft.com/system-center/vmm/network-logical) a forrás- és Virtual Machine Manager-kiszolgálón:

    - Lehet, hogy a forráskiszolgálón a logikai hálózat társítva a forrás-felhő, amelyben a Hyper-V-gazdagépek találhatók.
    - A célkiszolgálón a logikai hálózat társítva a célfelhő kell lennie.
* Ellenőrizze, hogy [Virtuálisgép-hálózatok](https://docs.microsoft.com/system-center/vmm/network-virtual) a forrás- és a Virtual Machine Manager kiszolgálójára. Virtuálisgép-hálózatot kösse össze az egyes helyeken a logikai hálózathoz.
* Virtuális gépek csatlakoztatása a forrás Hyper-V gazdagépeken a forrás Virtuálisgép-hálózathoz. 

## <a name="prepare-for-powershell"></a>Készítse elő a Powershellhez

Győződjön meg arról, hogy az Azure PowerShell-lel szeretné kipróbálni:

- Ha már használja a PowerShell, a frissítési verzióra 0.8.10 vagy újabb. [További](/powershell/azureps-cmdlets-docs) kapcsolatos beállítása a PowerShell.
- Miután beállítása és konfigurálása a PowerShell, tekintse át a [parancsmagok szolgáltatás](/powershell/azure/overview).
- Paraméterértékek, bemenetek és kimenetek a PowerShell használatával kapcsolatos további tudnivalókért olvassa el a [Ismerkedés](/powershell/azure/get-started-azureps) útmutató.

## <a name="set-up-a-subscription"></a>Előfizetés beállítása
1. A PowerShellben jelentkezzen be az Azure-fiókjával.

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Connect-AzureRmAccount #-Credential $Cred
2. Az előfizetés azonosítókat, az előfizetések listájának lekéréséhez. Megjegyzés: az előfizetés, amelyben szeretné létrehozni a Recovery Services-tároló azonosítója. 

        Get-AzureRmSubscription
3. A tároló az előfizetés beállításához.

        Set-AzureRmContext –SubscriptionID <subscriptionId>

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása
1. Ha nem rendelkezik ilyennel, hozzon létre egy Azure Resource Manager-erőforráscsoportot.

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location
2. Hozzon létre egy új Recovery Services-tárolót. Mentse a tár objektumot egy változóban későbbi felhasználás céljából. 

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location
   
    Miután a Get-AzureRMRecoveryServicesVault parancsmaggal létrehozott kérheti le a tár objektumot.

## <a name="set-the-vault-context"></a>A tárolási környezet beállítása
1. Egy meglévő tároló lekéréséhez.

       $vault = Get-AzureRmRecoveryServicesVault -Name #vaultname
2. Állítsa be a tárolási környezet.

       Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="install-the-site-recovery-provider"></a>A Site Recovery provider telepítése
1. A Virtual Machine Manager-gépen hozzon létre egy könyvtárat a következő parancs futtatásával:

       New-Item c:\ASR -type directory
2. Bontsa ki a fájlokat a letöltött szolgáltató telepítőfájlját használatával.

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

## <a name="create-and-associate-a-replication-policy"></a>A replikációs házirend létrehozása és társítása
1. Következőképpen hozhat létre egy replikációs szabályzatot. Ebben az esetben a Hyper-V 2012 R2-höz:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $RepProvider = HyperVReplica2012R2
        $Recoverypoints = 24                    #specify the number of hours to retain recovery pints
        $AppConsistentSnapshotFrequency = 4 #specify the frequency (in hours) at which app consistent snapshots are taken
        $AuthMode = "Kerberos"  #options are "Kerberos" or "Certificate"
        $AuthPort = "8083"  #specify the port number that will be used for replication traffic on Hyper-V hosts
        $InitialRepMethod = "Online" #options are "Online" or "Offline"

        $policyresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod

    > [!NOTE]
    > A Virtual Machine Manager cloud Hyper-V-gazdagépeken futó Windows Server különböző verzióit is tartalmazhat, de a replikációs szabályzat van az operációs rendszer adott verziójához. Ha a különböző operációs rendszert futtató gazdagépeken, hozzon létre külön replikációs szabályzatok minden rendszer számára. Például ha öt gazdagépeken futó Windows Server 2012 és Windows Server 2012 R2 rendszeren futó, három gazdagép, hozzon létre két replikációs házirendet. Akkor hozzon létre egyet az egyes operációs rendszert.

2. Az elsődleges védelmi tároló (elsődleges Virtual Machine Manager cloud) és a helyreállítási védelmi tároló (Virtual Machine Manager cloud helyreállítási) lekérése.

       $PrimaryCloud = "testprimarycloud"
       $primaryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

       $RecoveryCloud = "testrecoverycloud"
       $recoveryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
3. A replikációs szabályzat a valódi név használatával létrehozott lekéréséhez.

       $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname
4. Indítsa el a társítást a védelmi tároló (Virtual Machine Manager cloud) a replikációs házirendhez.

       $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
5. Várjon, amíg a szabályzat társítása feladat befejeződésére. Ellenőrizze, hogy ha a feladat befejeződött, használja az alábbi PowerShell-kódrészlettel:

       $job = Get-AzureRmSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }

6. A feladat a feldolgozás befejezését követően futtassa a következő parancsot:

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

A művelet befejezése után ellenőrizze, hogy kövesse [figyelése tevékenység](#monitor-activity).

##  <a name="configure-network-mapping"></a>Hálózatleképezés konfigurálása
1. Ez a parancs használatával lekérheti a jelenlegi tároló kiszolgálók. A parancs a Site Recovery-kiszolgálók a $Servers tömb változóban tárolja.

        $Servers = Get-AzureRmSiteRecoveryServer
2. Futtassa ezt a parancsot, a Virtual Machine Manager a forráskiszolgáló és a célkiszolgáló Virtual Machine Manager-hálózatok lekéréséhez.

        $PrimaryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

    > [!NOTE]
    > A Virtual Machine Manager forráskiszolgáló lehet első és második egy, a tömböt. Ellenőrizze a Virtual Machine Manager-kiszolgáló nevét, és a hálózatok megfelelő lekéréséhez.


3. Ez a parancsmag létrehozza az elsődleges és a helyreállítási hálózat közötti leképezést. Az elsődleges hálózatot $PrimaryNetworks az első elemeként adja meg. A helyreállítási hálózat $RecoveryNetworks az első elemeként adja meg.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]


## <a name="enable-protection-for-vms"></a>A virtuális gépek védelmének engedélyezése
A kiszolgálók, felhők és hálózatok megfelelő konfigurálását követően engedélyezni a védelmet a virtuális gépek, a felhőben.

1. Védelem engedélyezéséhez futtassa a következő parancsot a védelmi tároló lekéréséhez:

          $PrimaryProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
2. A védelmi entitás (VM), kérje le a következő:

           $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
3. A virtuális gép replikációjának engedélyezéséhez.

          $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy

## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

Az üzemelő példány teszteléséhez, egyetlen virtuális gép feladatátvételi teszt futtatása. Még több virtuális gépet tartalmazó helyreállítási terv létrehozása és a terv feladatátvételi teszt futtatása. A feladatátvételi teszt segítségével elkülönített hálózatban próbálhatja ki a feladatátvételi és helyreállítási mechanizmusokat.

1. A virtuális gép, amelybe a virtuális gépek átadja lekéréséhez.

       $Servers = Get-AzureRmSiteRecoveryServer
       $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

2. Feladatátvételi teszt végrehajtása.

   Egyetlen virtuális gép esetén:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1]
    
   A helyreállítási terv:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1]

A művelet befejezése után ellenőrizze, hogy kövesse [figyelése tevékenység](#monitor-activity).

## <a name="run-planned-and-unplanned-failovers"></a>A tervezett és nem tervezett feladatátvétel futtatása

1. Végezzen el egy tervezett feladatátvételt.

   Egyetlen virtuális gép esetén:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   A helyreállítási terv:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

2. Egy nem tervezett feladatátvétel végrehajtásához.

   Egyetlen virtuális gép esetén:
        
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   A helyreállítási terv:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

## <a name="monitor-activity"></a>Tevékenység figyelése
A következő parancsok használatával figyelése a feladatátvételt. Várjon, amíg a feldolgozás befejezéséhez feladatok között.

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



## <a name="next-steps"></a>További lépések

[További](/powershell/module/azurerm.recoveryservices.backup/#recovery) Site Recovery Resource Manager PowerShell-parancsmagokkal kapcsolatos.
