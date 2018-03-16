---
title: "A Virtual Machine Manager-felhőkhöz Hyper-V virtuális gépek replikálása egy másodlagos helyre, amelynek PowerShell (Azure Resource Manager) |} Microsoft Docs"
description: "Ismerteti, hogyan lehet a Virtual Machine Manager-felhőkhöz Hyper-V virtuális gépek replikálása egy másodlagos helyre a Virtual Machine Manager PowerShell (Resource Manager) használatával"
services: site-recovery
author: sujaytalasila
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/12/2018
ms.author: sutalasi
ms.openlocfilehash: ea4c2ed287619b92dba1b9b966cc0d52e0eb89c5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
# <a name="replicate-hyper-v-vms-to-a-secondary-site-by-using-powershell-resource-manager"></a>Hyper-V virtuális gépek replikálása másodlagos helyre (erőforrás-kezelő) PowerShell használatával

Ez a cikk bemutatja, hogyan lépések automatizálásához a System Center Virtual Machine Manager-felhőkben lévő másodlagos a Virtual Machine Manager felhő Hyper-V virtuális gépek replikálása a helyszíni hely használatával [Azure Site Recovery](site-recovery-overview.md).

## <a name="prerequisites"></a>Előfeltételek

- Tekintse át a [forgatókönyv architektúrája és összetevői](hyper-v-vmm-architecture.md).
- Minden összetevőre vonatkozóan tekintse át a [támogatási követelményeket](site-recovery-support-matrix-to-sec-site.md).
- Győződjön meg arról, hogy a Virtual Machine Manager-kiszolgálók és a Hyper-V-gazdagépek megfelelnek [igényeinek támogatására](site-recovery-support-matrix-to-sec-site.md).
- Ellenőrizze, hogy megfelelnek a replikálni kívánt virtuális gépek [replikált gép támogatási](site-recovery-support-matrix-to-sec-site.md).


## <a name="prepare-for-network-mapping"></a>A hálózatleképezés előkészítése

[A hálózatleképezés](hyper-v-vmm-network-mapping.md) megfelelteti a helyszíni forrása és célja felhők a Virtual Machine Manager Virtuálisgép-hálózatok. Leképezés a következőket teszi:

- Virtuális gépek csatlakozik a megfelelő cél Virtuálisgép-hálózatok a feladatátvételt követően. 
- A cél Hyper-V gazdakiszolgálókra optimális helyezi a replika virtuális gép. 
- Ha nem adja meg a hálózatleképezés, a replika virtuális gépek nem csatlakozik Virtuálisgép-hálózatot a feladatátvételt követően.

Készítse elő a Virtual Machine Manager a következőképpen:

* Győződjön meg arról, hogy [a Virtual Machine Manager logikai hálózatok](https://docs.microsoft.com/system-center/vmm/network-logical) a forrás- és a Virtual Machine Manager-kiszolgálókon:

    - A forráskiszolgálón a logikai hálózatot, amelyben a Hyper-V-gazdagépek találhatók a forrás-felhő társítva kell lennie.
    - A célkiszolgálón a logikai hálózat társítva a célfelhő kell lennie.
* Győződjön meg arról, hogy [Virtuálisgép-hálózatok](https://docs.microsoft.com/system-center/vmm/network-virtual) a forrás- és a Virtual Machine Manager kiszolgálón. Virtuálisgép-hálózatot kösse össze az egyes helyeken a logikai hálózathoz.
* A forrás Hyper-V gazdagépeken a forrás Virtuálisgép-hálózathoz csatlakozzon a virtuális gépek. 

## <a name="prepare-for-powershell"></a>PowerShell előkészítése

Győződjön meg arról, hogy az Azure PowerShell készen áll:

- Ha már használja a PowerShell, a frissítési verzióra 0.8.10 vagy újabb. [További](/powershell/azureps-cmdlets-docs) PowerShell beállításával kapcsolatban.
- Miután beállítása és konfigurálása a PowerShell, tekintse át a [parancsmagok szolgáltatás](/powershell/azure/overview).
- A paraméterértékek, bemenetekhez és kimenetekhez a PowerShell használatával kapcsolatos további tudnivalókért olvassa el a [Ismerkedés](/powershell/azure/get-started-azureps) útmutató.

## <a name="set-up-a-subscription"></a>Előfizetés beállítása
1. A Powershellből jelentkezzen be az Azure-fiókjával.

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Login-AzureRmAccount #-Credential $Cred
2. Az előfizetések, az előfizetéshez azonosítók listájának lekérésében. Megjegyzés: az előfizetés, amelyben a Recovery Services-tároló létrehozásához kívánt azonosító. 

        Get-AzureRmSubscription
3. A tároló az előfizetés beállításához.

        Set-AzureRmContext –SubscriptionID <subscriptionId>

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása
1. Hozzon létre egy Azure Resource Manager erőforráscsoportot, ha még nem rendelkezik ilyennel.

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location
2. Hozzon létre egy új Recovery Services-tároló. A tároló objektum mentése a későbbi változóban. 

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location
   
    A Get-AzureRMRecoveryServicesVault parancsmaggal létrehozását követően a tároló objektum kérheti le.

## <a name="set-the-vault-context"></a>A tároló környezet beállítása
1. Egy meglévő tárolóban beolvasása.

       $vault = Get-AzureRmRecoveryServicesVault -Name #vaultname
2. A tároló környezet beállítása.

       Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="install-the-site-recovery-provider"></a>A Site Recovery provider telepítése
1. A Virtual Machine Manager gép hozzon létre egy könyvtárat a következő parancs futtatásával:

       New-Item c:\ASR -type directory
2. Bontsa ki a fájlokat a letöltött szolgáltató fájl használatával.

       pushd C:\ASR\
       .\AzureSiteRecoveryProvider.exe /x:. /q
3. Telepítse a szolgáltatót, majd várjon a telepítés befejezéséhez.

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
1. Hozzon létre egy replikációs házirendet, ebben az esetben a 2012 R2 Hyper-V, az alábbiak szerint:

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
    > A Virtual Machine Manager felhő Windows Server különböző verzióit futtató Hyper-V-gazdagépek is tartalmazhat, de a replikációs házirend az operációs rendszer adott verziójához. Ha különböző állomások különböző operációs rendszereken futó, hozzon létre az egyes rendszerek különálló replikációs házirendeket. Például ha fut a Windows Server 2012 és Windows Server 2012 R2 rendszerű három gazdagépek öt állomás létezik, hozzon létre két replikációs házirend. Akkor hozzon létre egyet az egyes operációs rendszer.

2. Az elsődleges védelmi tároló (elsődleges a Virtual Machine Manager felhő) és a helyreállítási védelmi tároló (a Virtual Machine Manager felhő helyreállítási) beolvasása.

       $PrimaryCloud = "testprimarycloud"
       $primaryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

       $RecoveryCloud = "testrecoverycloud"
       $recoveryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
3. A replikációs házirend, a rövid név használatával létrehozott beolvasása.

       $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname
4. Indítsa el a társítást a védelmi tároló (a Virtual Machine Manager felhő) a replikációs házirend.

       $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
5. Várjon, amíg a házirend társítása feladat befejeződésére. Annak ellenőrzéséhez, ha a feladat befejeződött, a következő PowerShell-kódrészletet használja:

       $job = Get-AzureRmSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }

6. A feladat a feldolgozás befejezése után futtassa a következő parancsot:

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

A művelet a befejezése ellenőrzéséhez kövesse [figyelése](#monitor-activity).

##  <a name="configure-network-mapping"></a>Hálózatleképezés konfigurálása
1. A parancs segítségével a jelenlegi tároló kiszolgálók beolvasása. A parancs a Site Recovery kiszolgálók $Servers tömb változó tárolja.

        $Servers = Get-AzureRmSiteRecoveryServer
2. Futtassa ezt a parancsot a forráskiszolgálón a Virtual Machine Manager és a célkiszolgáló a Virtual Machine Manager hálózatok beolvasása.

        $PrimaryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

    > [!NOTE]
    > A forrás Virtuálisgép-kezelő kiszolgáló lehet a kiszolgáló a tömb első és második szerkezetével. Ellenőrizze a Virtual Machine Manager kiszolgáló nevét, és a hálózatok megfelelően beolvasása.


3. Ez a parancsmag létrehozza az elsődleges és a helyreállítási hálózat közötti leképezést. Az elsődleges hálózati $PrimaryNetworks első elemeként adja meg. A helyreállítási hálózati $RecoveryNetworks első elemeként adja meg.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]


## <a name="enable-protection-for-vms"></a>Virtuális gépek védelmének engedélyezése
A kiszolgálók, felhők és hálózatok megfelelő konfigurálását követően engedélyezze a védelmet a felhőben található virtuális gépek.

1. Védelem engedélyezéséhez futtassa a következő parancsot a védelmi tároló lekéréséhez:

          $PrimaryProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
2. Töltse le a védelmi entitás (VM), az alábbiak szerint:

           $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
3. Engedélyezze a virtuális gép replikálását.

          $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy

## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

A telepítés tesztelésére, egyetlen virtuális gép feladatátvételi teszt futtatása. Is, amely tartalmazza a több virtuális gép helyreállítási terv létrehozása és a terv feladatátvételi teszt futtatása. A feladatátvételi teszt segítségével elkülönített hálózatban próbálhatja ki a feladatátvételi és helyreállítási mechanizmusokat.

1. A virtuális Gépet, amelybe virtuális gépek hajt végre feladatátvételt beolvasása.

       $Servers = Get-AzureRmSiteRecoveryServer
       $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

2. Feladatátvételi teszt végrehajtása.

   Egyetlen virtuális géphez:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1]
    
   A helyreállítási terv:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1]

A művelet a befejezése ellenőrzéséhez kövesse [figyelése](#monitor-activity).

## <a name="run-planned-and-unplanned-failovers"></a>Tervezett és nem tervezett feladatátvételt

1. Végezzen el egy tervezett feladatátvételt.

   Egyetlen virtuális géphez:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   A helyreállítási terv:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

2. Hajtsa végre a nem tervezett feladatátvételre.

   Egyetlen virtuális géphez:
        
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

   A helyreállítási terv:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

## <a name="monitor-activity"></a>Figyelési tevékenység
Az alábbi parancsokkal feladatátvételi figyelése. Várjon, amíg a feldolgozás Between feladatok befejezéséhez.

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

[További](/powershell/module/azurerm.recoveryservices.backup/#recovery) Site Recovery a Resource Manager PowerShell-parancsmagokkal kapcsolatos.
