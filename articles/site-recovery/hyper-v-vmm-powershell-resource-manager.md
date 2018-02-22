---
title: "Hyper-V virtuális gépek VMM-felhőkben replikálása egy másodlagos helyre a PowerShell használatával (Azure Resource Manager) |} Microsoft Docs"
description: "Ismerteti, hogyan helyre történő replikálásához a Hyper-V virtuális gépek VMM-felhőkben egy másodlagos VMM PowerShell (Resource Manager) használatával"
services: site-recovery
author: sujaytalasila
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/12/2018
ms.author: sutalasi
ms.openlocfilehash: a5e36546494223b20844303f3f76782746658411
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="replicate-hyper-v-vms-to-a-secondary-site-using-powershell-resource-manager"></a>Hyper-V virtuális gépek replikálása egy másodlagos helyre PowerShell (Resource Manager) használatával

Ez a cikk bemutatja a System Center Virtual Machine Manager (VMM) felhőkben a Hyper-V virtuális gépek replikálása egy másodlagos helyszíni helyre, a VMM-felhőhöz lépések automatizálásához használatával [Azure Site Recovery](site-recovery-overview.md).

## <a name="prerequisites"></a>Előfeltételek

- Tekintse át a [forgatókönyv architektúrája és összetevői](hyper-v-vmm-architecture.md).
- Minden összetevőre vonatkozóan tekintse át a [támogatási követelményeket](site-recovery-support-matrix-to-sec-site.md).
- Győződjön meg arról, hogy a VMM-kiszolgálók és a Hyper-V-gazdagépek megfelelnek [igényeinek támogatására](site-recovery-support-matrix-to-sec-site.md).
- Ellenőrizze, hogy a replikálni kívánt virtuális gépeket ahhoz [replikált gépek támogatása](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions)


## <a name="prepare-for-network-mapping"></a>A hálózatleképezés előkészítése

[A hálózatleképezés](hyper-v-vmm-network-mapping.md) megfelelteti a helyszíni VMM-Virtuálisgép-hálózatok forrása és célja felhők. Leképezés a következőket teszi:

- Virtuális gépek csatlakozik a megfelelő cél Virtuálisgép-hálózatok a feladatátvételt követően. 
- A cél Hyper-V gazdakiszolgálókra optimális helyezi a replika virtuális gép. 
- Ha nem adja meg a hálózatleképezés, a replika virtuális gépek nem csatlakozik Virtuálisgép-hálózatot a feladatátvételt követően.

Készítse elő a VMM a következőképpen:

1. Győződjön meg arról, hogy [VMM logikai hálózatok](https://docs.microsoft.com/system-center/vmm/network-logical) a forrás és cél VMM-kiszolgálókon.
    - A forráskiszolgálón a logikai hálózatot, amelyben a Hyper-V-gazdagépek találhatók a forrás-felhő társítva kell lennie.
    - A célkiszolgálón a logikai hálózat társítva a célfelhő kell lennie.
1. Győződjön meg arról, hogy [Virtuálisgép-hálózatok](https://docs.microsoft.com/system-center/vmm/network-virtual) a forrás és cél VMM-kiszolgálókon. Virtuálisgép-hálózatot kösse össze az egyes helyeken a logikai hálózathoz.
2. A forrás Hyper-V gazdagépeken a forrás Virtuálisgép-hálózathoz csatlakozzon a virtuális gépek. 

## <a name="prepare-for-powershell"></a>PowerShell előkészítése

Győződjön meg arról, hogy készen áll az Azure PowerShell.

- Ha már használ PowerShell, szüksége lesz a frissítési verzióra 0.8.10 vagy újabb.  [További](/powershell/azureps-cmdlets-docs) PowerShell beállításával kapcsolatos.
- Miután beállítása és PowerShell konfigurált, tekintse át a [parancsmagok szolgáltatás](/powershell/azure/overview).
- A paraméterértékek, bemenetekhez és kimenetekhez Azure PowerShell használatával kapcsolatos további tudnivalókért olvassa el a [bevezetés](/powershell/azure/get-started-azureps) útmutató.

## <a name="set-up-a-subscription"></a>Előfizetés beállítása
1. Az Azure PowerShell jelentkezzen be a Azure-fiókba:

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Login-AzureRmAccount #-Credential $Cred
2. Az előfizetések, az előfizetéshez azonosítók listájának lekérésében. Jegyezze fel az előfizetés, amelyben a helyreállítási szolgáltatás tároló létrehozása kívánt azonosító.   

        Get-AzureRmSubscription
3. A tároló az előfizetés beállításához:

        Set-AzureRmContext –SubscriptionID <subscriptionId>

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása
1. Hozzon létre egy Azure Resource Manager erőforráscsoportot, ha még nem rendelkezik ilyennel.

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location
2. Hozzon létre egy új Recovery Services-tárolót, és a tároló objektum mentése változóként később használható: 

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location
   
    A tároló objektum létrehozása után, a Get-AzureRMRecoveryServicesVault parancsmag használatával kérheti le.

## <a name="set-the-vault-context"></a>A tároló környezet beállítása
1. Egy meglévő tárolóban beolvasása:

       $vault = Get-AzureRmRecoveryServicesVault -Name #vaultname
2. A tároló környezet beállítása:

       Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="install-the-azure-site-recovery-provider"></a>Az Azure Site Recovery Provider telepítése
1. A VMM-gépen hozzon létre egy könyvtárat a következő parancs futtatásával:

       New-Item c:\ASR -type directory
2. Bontsa ki a fájlokat a letöltött fájl szolgáltató használata: pushd C:\ASR\
       .\AzureSiteRecoveryProvider.exe /x:. /q
3. Telepítse a szolgáltatót, majd várjon a telepítés befejezéséhez:

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

4. Regisztrálja a kiszolgálót a tárolóban:

       $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
       pushd $BinPath
       $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

## <a name="create-and-associate-a-replication-policy"></a>A replikációs házirend létrehozása és társítása
1. Hozzon létre egy replikációs házirendet (ebben az esetben a Hyper-V 2012 R2 rendszerben), az alábbiak szerint:

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
    > A VMM-felhő Windows Server különböző verzióit futtató Hyper-V-gazdagépek is tartalmazhat, de a replikációs házirend az operációs rendszer adott verziójához. Ha a különböző operációs rendszert futtató gazdagépeken, majd hozzon létre minden rendszer külön replikációs házirendeket. Például ha öt gazdagépeken futó Windows-kiszolgálók 2012 és Windows Server 2012 R2 három, hozzon létre két replikációs házirendek – egyet az egyes operációs rendszer.

2. Lekéri az elsődleges védelmi tároló (elsődleges VMM c), és a helyreállítási védelmi tároló (helyreállítási VMM-felhőben):

       $PrimaryCloud = "testprimarycloud"
       $primaryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

       $RecoveryCloud = "testrecoverycloud"
       $recoveryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
2. Lekéri a replikációs házirend létrehozva, a rövid név használatával:

       $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname
3. Indítsa el a társítást a védelmi tároló (VMM-felhőben) a replikációs házirend:

       $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
4. Várja meg a házirend társítása feladat befejeződik. Ha a feladat befejeződött a következő PowerShell-részlet használatával ellenőrizheti:

       $job = Get-AzureRmSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }

   A feladat feldolgozása után futtassa a következő parancsot:

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

A művelet a befejezése ellenőrzéséhez kövesse [figyelése](#monitor-activity).

##  <a name="configure-network-mapping"></a>Hálózatleképezés konfigurálása
1. A parancs segítségével a jelenlegi tároló kiszolgálók beolvasása. A parancs az Azure Site Recovery kiszolgálók $Servers tömb változó tárolja.

        $Servers = Get-AzureRmSiteRecoveryServer
2. Futtassa a parancsot retrievet a hálózatok, a forrás VMM-kiszolgálón és a cél VMM-kiszolgálóval.

        $PrimaryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

    > [!NOTE]
    > A forrás VMM-kiszolgáló lehet a kiszolgálók tömb első és második szerkezetével. Ellenőrizze a VMM-kiszolgáló nevét, és a hálózatok megfelelően beolvasása.


3. Ez a parancsmag létrehozza az elsődleges és a helyreállítási hálózat közötti leképezést. Azt adja meg, mint az első elem az $PrimaryNetworks elsődleges hálózati és a helyreállítási hálózati $RecoveryNetworks első elemeként.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]


## <a name="enable-protection-for-vms"></a>Virtuális gépek védelmének engedélyezése
A kiszolgálók, felhők és hálózatok megfelelő konfigurálását követően engedélyezheti a felhőben található virtuális gépek védelmét.

1. Védelem engedélyezéséhez futtassa a következő parancsot a védelmi tároló lekéréséhez:

          $PrimaryProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
2. Töltse le a védelmi entitás (VM) az alábbiak szerint:

           $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
3. A virtuális gép replikációjának engedélyezése:

          $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy

## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

Az üzemelő példány egyetlen virtuális gép feladatátvételi tesztet futtatni, vagy hozzon létre egy helyreállítási tervet, amely tartalmazza a több virtuális géphez, és futtassa a terv feladatátvételi tesztjét. A feladatátvételi teszt segítségével elkülönített hálózatban próbálhatja ki a feladatátvételi és helyreállítási mechanizmusokat.

1. A virtuális Gépet, amelybe virtuális gépek hajt végre feladatátvételt beolvasása:

       $Servers = Get-AzureRmSiteRecoveryServer
       $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]
2. Feladatátvételi teszt végrehajtása az alábbiak szerint:
    - Egyetlen virtuális gépeknél

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1]
    
    - A helyreállítási terv:

        $recoveryplanname = "test-helyreállítási-terv"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1]

A művelet a befejezése ellenőrzéséhez kövesse [figyelése](#monitor-activity).

## <a name="run-planned-and-unplanned-failovers"></a>Tervezett és nem tervezett feladatátvételt

1. Tervezett feladatátvétel végrehajtása az alábbiak szerint:
    -  Egyetlen virtuális géphez:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity
    - A helyreállítási terv

        $recoveryplanname = "test-helyreállítási-terv"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan
2. Nem tervezett feladatátvétel végre az alábbiak szerint:
    - Egyetlen virtuális gépeknél
        
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

    - A helyreállítási terv:

        $recoveryplanname = "test-helyreállítási-terv"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

## <a name="monitor-activity"></a>Figyelési tevékenység
Az alábbi parancsokkal failver figyelése. Vegye figyelembe, hogy várnia kell, a feldolgozását a Befejezés gombra a feladatok között.

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

[További](/powershell/module/azurerm.recoveryservices.backup/#recovery) Site Recovery Azure Resource Manager PowerShell-parancsmagokkal kapcsolatos.
