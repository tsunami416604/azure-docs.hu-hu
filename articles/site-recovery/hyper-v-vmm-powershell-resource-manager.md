---
title: Állítsa be a Hyper-V virtuális gépek vészhelyreállítása egy másodlagos helyre a PowerShell és az Azure Site Recovery VMM-felhőkben |} A Microsoft Docs
description: Ismerteti, hogyan állítható be Hyper-V virtuális gépek vészhelyreállítása egy másodlagos VMM-helyről az Azure Site Recovery és a PowerShell használatával a VMM-felhőkben.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: sutalasi
ms.openlocfilehash: 78bd077b5491b093510b9c55bf7b5a42ee9cb578
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59787598"
---
# <a name="set-up-disaster-recovery-of-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>Állítsa be a Hyper-V virtuális gépek vészhelyreállítása egy másodlagos helyre a PowerShell (Resource Manager) használatával

Ez a cikk bemutatja, hogyan automatizálhatja a lépéseket, replikációját a Hyper-V virtuális gépeket a System Center Virtual Machine Manager-felhőkben a Virtual Machine Manager cloud egy másodlagos helyszíni hely használatával [Azure Site Recovery](site-recovery-overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- Tekintse át [a forgatókönyv-architektúrát és -összetevőket](hyper-v-vmm-architecture.md).
- Minden összetevőre vonatkozóan tekintse át a [támogatási követelményeket](site-recovery-support-matrix-to-sec-site.md).
- Győződjön meg arról, hogy a Virtual Machine Manager-kiszolgálók és a Hyper-V-gazdagépek megfelelnek a [memóriakonfigurációt](site-recovery-support-matrix-to-sec-site.md).
- Ellenőrizze, hogy a replikálni kívánt virtuális gépek megfelelnek [replikált gépek támogatása](site-recovery-support-matrix-to-sec-site.md).


## <a name="prepare-for-network-mapping"></a>A hálózatleképezés előkészítése

[A hálózatleképezés](hyper-v-vmm-network-mapping.md) közötti leképezéseket a helyszíni adatforrás és a célfelhő Virtual Machine Manager Virtuálisgép-hálózatok. A leképezés a következőket hajtja végre:

- A feladatátvételt követően összekapcsolja a virtuális gépeket a megfelelő céloldali virtuálisgép-hálózatokkal. 
- Optimális módon helyezi el a virtuális replikagépeket a céloldali Hyper-V gazdakiszolgálókon. 
- Ha nem konfigurálja a hálózatleképezést, a replika virtuális gépek nem csatlakozik Virtuálisgép-hálózat a feladatátvételt követően.

Virtual Machine Manager készítse elő a következőképpen:

* Ellenőrizze, hogy [Virtual Machine Manager logikai hálózatok](https://docs.microsoft.com/system-center/vmm/network-logical) a forrás- és Virtual Machine Manager-kiszolgálón:

    - A forrásoldali kiszolgálón található logikai hálózatnak ahhoz a forrásfelhőhöz kell tartoznia, amelyikben a Hyper-V gazdagépek találhatók.
    - A céloldali kiszolgálón található logikai hálózatnak a célfelhőhöz kell tartoznia.
* Ellenőrizze, hogy [Virtuálisgép-hálózatok](https://docs.microsoft.com/system-center/vmm/network-virtual) a forrás- és a Virtual Machine Manager kiszolgálójára. A virtuálisgép-hálózatokat minden helyen össze kell kapcsolni a logikai hálózattal.
* A forrásoldali Hyper-V gazdagépeken található virtuális gépeket a forrásoldali virtuálisgép-hálózathoz kell csatlakoztatni. 

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
        Connect-AzAccount #-Credential $Cred
2. Az előfizetés azonosítókat, az előfizetések listájának lekéréséhez. Megjegyzés: az előfizetés, amelyben szeretné létrehozni a Recovery Services-tároló azonosítója. 

        Get-AzSubscription
3. A tároló az előfizetés beállításához.

        Set-AzContext –SubscriptionID <subscriptionId>

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása
1. Ha nem rendelkezik ilyennel, hozzon létre egy Azure Resource Manager-erőforráscsoportot.

        New-AzResourceGroup -Name #ResourceGroupName -Location #location
2. Hozzon létre egy új Recovery Services-tárolót. Mentse a tár objektumot egy változóban későbbi felhasználás céljából. 

        $vault = New-AzRecoveryServicesVault -Name #vaultname -ResourceGroupName #ResourceGroupName -Location #location
   
    Miután a Get-AzRecoveryServicesVault parancsmaggal létrehozott kérheti le a tár objektumot.

## <a name="set-the-vault-context"></a>A tárolási környezet beállítása
1. Egy meglévő tároló lekéréséhez.

       $vault = Get-AzRecoveryServicesVault -Name #vaultname
2. Állítsa be a tárolási környezet.

       Set-AzSiteRecoveryVaultSettings -ARSVault $vault

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

        $policyresult = New-AzSiteRecoveryPolicy -Name $policyname -ReplicationProvider $RepProvider -ReplicationFrequencyInSeconds $Replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours $AppConsistentSnapshotFrequency -Authentication $AuthMode -ReplicationPort $AuthPort -ReplicationMethod $InitialRepMethod

    > [!NOTE]
    > A Virtual Machine Manager cloud Hyper-V-gazdagépeken futó Windows Server különböző verzióit is tartalmazhat, de a replikációs szabályzat van az operációs rendszer adott verziójához. Ha a különböző operációs rendszert futtató gazdagépeken, hozzon létre külön replikációs szabályzatok minden rendszer számára. Például ha öt gazdagépeken futó Windows Server 2012 és Windows Server 2012 R2 rendszeren futó, három gazdagép, hozzon létre két replikációs házirendet. Akkor hozzon létre egyet az egyes operációs rendszert.

2. Az elsődleges védelmi tároló (elsődleges Virtual Machine Manager cloud) és a helyreállítási védelmi tároló (Virtual Machine Manager cloud helyreállítási) lekérése.

       $PrimaryCloud = "testprimarycloud"
       $primaryprotectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

       $RecoveryCloud = "testrecoverycloud"
       $recoveryprotectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
3. A replikációs szabályzat a valódi név használatával létrehozott lekéréséhez.

       $policy = Get-AzSiteRecoveryPolicy -FriendlyName $policyname
4. Indítsa el a társítást a védelmi tároló (Virtual Machine Manager cloud) a replikációs házirendhez.

       $associationJob  = Start-AzSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
5. Várjon, amíg a szabályzat társítása feladat befejeződésére. Ellenőrizze, hogy ha a feladat befejeződött, használja az alábbi PowerShell-kódrészlettel:

       $job = Get-AzSiteRecoveryJob -Job $associationJob

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

        $Servers = Get-AzSiteRecoveryServer
2. Futtassa ezt a parancsot, a Virtual Machine Manager a forráskiszolgáló és a célkiszolgáló Virtual Machine Manager-hálózatok lekéréséhez.

        $PrimaryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[1]

    > [!NOTE]
    > A Virtual Machine Manager forráskiszolgáló lehet első és második egy, a tömböt. Ellenőrizze a Virtual Machine Manager-kiszolgáló nevét, és a hálózatok megfelelő lekéréséhez.


3. Ez a parancsmag létrehozza az elsődleges és a helyreállítási hálózat közötti leképezést. Az elsődleges hálózatot $PrimaryNetworks az első elemeként adja meg. A helyreállítási hálózat $RecoveryNetworks az első elemeként adja meg.

        New-AzSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]


## <a name="enable-protection-for-vms"></a>A virtuális gépek védelmének engedélyezése
A kiszolgálók, felhők és hálózatok megfelelő konfigurálását követően engedélyezni a védelmet a virtuális gépek, a felhőben.

1. Védelem engedélyezéséhez futtassa a következő parancsot a védelmi tároló lekéréséhez:

          $PrimaryProtectionContainer = Get-AzSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
2. A védelmi entitás (VM), kérje le a következő:

           $protectionEntity = Get-AzSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
3. A virtuális gép replikációjának engedélyezéséhez.

          $jobResult = Set-AzSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy

## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

Az üzemelő példány teszteléséhez, egyetlen virtuális gép feladatátvételi teszt futtatása. Még több virtuális gépet tartalmazó helyreállítási terv létrehozása és a terv feladatátvételi teszt futtatása. A feladatátvételi teszt segítségével elkülönített hálózatban próbálhatja ki a feladatátvételi és helyreállítási mechanizmusokat.

1. A virtuális gép, amelybe a virtuális gépek átadja lekéréséhez.

       $Servers = Get-AzSiteRecoveryServer
       $RecoveryNetworks = Get-AzSiteRecoveryNetwork -Server $Servers[1]

2. Feladatátvételi teszt végrehajtása.

   Egyetlen virtuális gép esetén:

        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1]
    
   A helyreállítási terv:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1]

A művelet befejezése után ellenőrizze, hogy kövesse [figyelése tevékenység](#monitor-activity).

## <a name="run-planned-and-unplanned-failovers"></a>A tervezett és nem tervezett feladatátvétel futtatása

1. Végezzen el egy tervezett feladatátvételt.

   Egyetlen virtuális gép esetén:

        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   A helyreállítási terv:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

2. Egy nem tervezett feladatátvétel végrehajtásához.

   Egyetlen virtuális gép esetén:
        
        $protectionEntity = Get-AzSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   A helyreállítási terv:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

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

[További](/powershell/module/az.recoveryservices) Site Recovery Resource Manager PowerShell-parancsmagokkal kapcsolatos.
