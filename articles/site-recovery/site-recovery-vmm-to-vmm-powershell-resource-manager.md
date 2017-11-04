---
title: "A VMM-ben a Hyper-V virtuális gépek replikálása egy másodlagos helyre a PowerShell használatával (Azure Resource Manager) |} Microsoft Docs"
description: "Ismerteti, hogyan lehet az Azure Site Recovery-bA replikálása, feladatátvétele és helyreállítása a Hyper-V virtuális gépek VMM-felhőkben a VMM másodlagos hely PowerShell (Resource Manager) használatával történő telepítése"
services: site-recovery
documentationcenter: 
author: sujaytalasila
manager: rochakm
editor: raynew
ms.assetid: 9d38e9c3-217c-4e44-830c-575e9a4141f2
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: sutalasi
ms.openlocfilehash: c978c2e31e775f56824d765491f6d7b73648b8ae
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2017
---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site-using-powershell-resource-manager"></a>Hyper-V virtuális gépek VMM-felhőkben replikálása egy másodlagos VMM-hely PowerShell (Resource Manager) használatával

Ez a cikk bemutatja, hogyan lehet gyakori feladatok automatizálásához a System Center VMM-felhőkben Hyper-V virtuális gépek replikálása egy másodlagos hely System Center VMM-felhők Azure Site Recovery beállításához a PowerShell használatával.



## <a name="on-premises-prerequisites"></a>Helyszíni előfeltételek
Itt van a következőkre lesz szüksége a az helyszíni elsődleges és másodlagos helyek a forgatókönyv megvalósításához:

| **Előfeltételek** | **Részletek** |
| --- | --- |
| **VMM** |Azt javasoljuk, hogy a VMM-kiszolgáló az elsődleges hely és a VMM-kiszolgálót a másodlagos helyet telepít.<br/><br/> Egyetlen VMM-kiszolgálón felhők között is replikálhatja. Ehhez szüksége lesz legalább két, a VMM-kiszolgálón konfigurált felhők.<br/><br/> VMM-kiszolgálókon legalább futnia kell a System Center 2012 SP1 a legújabb frissítésekkel.<br/><br/> Minden VMM-kiszolgálót rendelkeznie kell egy vagy több konfigurált felhők és az összes felhő rendelkeznie kell a Hyper-V kapacitásprofilhoz beállítása. <br/><br/>Felhő legalább egy VMM-gazdagépcsoportot kell tartalmaznia. VMM-kiszolgálókon internet-hozzáféréssel kell rendelkeznie. |
| **Hyper-V** |Hyper-V kiszolgálók legalább futnia kell a Hyper-V szerepkör és a Windows Server 2012 és a legújabb frissítések telepítve van.<br/><br/> Minden Hyper-V kiszolgáló tartalmazzon legalább egy virtuális gépet.<br/><br/>  Hyper-V gazdakiszolgálók csoportok tárolása az elsődleges és másodlagos VMM-felhőkben kell elhelyezkedniük.<br/><br/> Ha futtatja a Hyper-V fürt Windows Server 2012 R2 rendszeren kell telepítenie [2961977 frissítése](https://support.microsoft.com/kb/2961977)<br/><br/> Ha futtatja a Hyper-V fürt Windows Server 2012 Megjegyzés: a fürtszervező nem hozza létre automatikusan Ha egy statikus IP cím alapú fürtöt. Ebben az esetben manuálisan kell konfigurálnia a fürtszervezőt. [További információk](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx). |
| **Szolgáltató** |A Site Recovery üzembe helyezése során az Azure Site Recovery Providert a VMM-kiszolgáló telepítése. A szolgáltató replikációra HTTPS 443-as keresztül kommunikál a Site Recovery. Adatok replikáció az elsődleges és másodlagos Hyper-V-kiszolgáló között a helyi vagy a VPN-kapcsolaton keresztül.<br/><br/> A VMM-kiszolgálón futó Provider hozzá kell férnie az URL-címek: *. hypervrecoverymanager.windowsazure.com; *. accesscontrol.windows.net; *. backup.windowsazure.com; *. blob.core.windows.net; *. store.core.windows.net.<br/><br/> Továbbá lehetővé teszi a VMM-kiszolgáló a tűzfal-kommunikációt a [Azure datacenter IP-címtartományok](https://www.microsoft.com/download/confirmation.aspx?id=41653) és a HTTPS (443) protokollt engedélyezi. |

### <a name="network-mapping-prerequisites"></a>Hálózatleképezési előfeltételek
Hálózatleképezés kapcsolatot hoz létre az elsődleges és másodlagos VMM-kiszolgálókon a következő VMM Virtuálisgép-hálózatok között:

* Optimális helyezze el a replika virtuális gépek másodlagos Hyper-V-gazdagépek a feladatátvételt követően.
* A replika virtuális gépek csatlakozni a megfelelő Virtuálisgép-hálózatok.
* Ha nem konfigurálja a hálózati leképezési replika virtuális gép nem fog csatlakozni egyetlen hálózathoz sem a feladatátvételt követően.
* Ha azt szeretné, hogy a hálózat beállítása során a Site Recovery leképezése itt kell telepíteni következőkre lesz szüksége:

  * Ellenőrizze, hogy a forrás Hyper-V gazdakiszolgálón futó virtuális gépek csatlakoznak-e egy VMM-virtuálisgép-hálózathoz. Ezt a hálózatot kösse össze egy, a felhőhöz társított logikai hálózattal.
  * Ellenőrizze, hogy a másodlagos szeretné használni a helyreállítási felhőben van-e a megfelelő Virtuálisgép-hálózatot. A Virtuálisgép-hálózatot kösse össze a másodlagos felhőhöz társított logikai hálózatot.

További információ a VMM-hálózatok konfigurálásáról az alábbi cikkek

* [Logikai hálózatok konfigurálása a VMM-ben](http://go.microsoft.com/fwlink/p/?LinkId=386307)
* [A Virtuálisgép-hálózatok és átjárók konfigurálása a VMM-ben](http://go.microsoft.com/fwlink/p/?LinkId=386308)


### <a name="powershell-prerequisites"></a>PowerShell-Előfeltételek
Győződjön meg arról, hogy készen áll az Azure PowerShell. Ha már használ PowerShell, szüksége lesz a frissítési verzióra 0.8.10 vagy újabb. PowerShell beállításával kapcsolatos információkért lásd: a [útmutató az Azure PowerShell telepítése és konfigurálása](/powershell/azureps-cmdlets-docs). Miután beállítása és PowerShell konfigurált, megtekintheti az összes elérhető parancsmagok a szolgáltatás [Itt](/powershell/azure/overview).

Című témakörben olvashat tippeket, amelyekkel a parancsmagok, például a paraméterértékek, bemenetekhez és kimenetekhez általában kezelésének módja az Azure PowerShell használata a [útmutatóban Ismerkedés az Azure-parancsmagok](/powershell/azure/get-started-azureps).

## <a name="step-1-set-the-subscription"></a>1. lépés: Az előfizetés beállításához
1. Az Azure powershell, jelentkezzen be az Azure-fiókjával: a következő parancsmagok használatával

        $UserName = "<user@live.com>"
        $Password = "<password>"
        $SecurePassword = ConvertTo-SecureString -AsPlainText $Password -Force
        $Cred = New-Object System.Management.Automation.PSCredential -ArgumentList $UserName, $SecurePassword
        Login-AzureRmAccount #-Credential $Cred
2. Az előfizetések listájának lekérése. Ez is kilistázza a subscriptionIDs az egyes előfizetések. Jegyezze fel az előfizetés-azonosító az előfizetés, amelyben létre szeretne hozni a recovery services-tároló    

        Get-AzureRmSubscription
3. Az előfizetés, amelyen a recovery services-tároló, hogy hozza létre az előfizetés-azonosító megemlíteni beállításához

        Set-AzureRmContext –SubscriptionID <subscriptionId>

## <a name="step-2-create-a-recovery-services-vault"></a>2. lépés: Recovery Services-tároló létrehozása
1. Ha már nincs ilyen, hozzon létre egy Azure Resource Manager erőforráscsoportot

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location
2. Hozzon létre egy új Recovery Services-tárolót, és mentse a Recovery Services-tároló objektumot egy változóban (használandó később). Is kérheti le a tároló objektum post létrehozása a Get-AzureRMRecoveryServicesVault parancsmag segítségével:-

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location

## <a name="step-3-set-the-recovery-services-vault-context"></a>3. lépés: A Recovery Services-tároló környezet beállítása
1. Ha egy már létrehozott tároló, futtassa az alábbi parancs használatával beszerezheti a tárolóban.

       $vault = Get-AzureRmRecoveryServicesVault -Name #vaultname
2. A tároló környezet beállítása futtatásával az alábbi parancsot.

       Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="step-4-install-the-azure-site-recovery-provider"></a>4. lépés: Az Azure Site Recovery Provider telepítése
1. A VMM-gépen hozzon létre egy könyvtárat a következő parancs futtatásával:

       New-Item c:\ASR -type directory
2. Bontsa ki a fájlokat a letöltött szolgáltató használatával a következő parancs futtatásával

       pushd C:\ASR\
       .\AzureSiteRecoveryProvider.exe /x:. /q
3. Telepítse a szolgáltatót, a következő parancsokkal:

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

   Várjon, amíg a telepítés befejezéséhez.
4. Regisztrálja a kiszolgálót a tárolóban, a következő parancsot:

       $BinPath = $env:SystemDrive+"\Program Files\Microsoft System Center 2012 R2\Virtual Machine Manager\bin"
       pushd $BinPath
       $encryptionFilePath = "C:\temp\".\DRConfigurator.exe /r /Credentials $VaultSettingFilePath /vmmfriendlyname $env:COMPUTERNAME /dataencryptionenabled $encryptionFilePath /startvmmservice

## <a name="step-5-create-and-associate-a-replication-policy"></a>5. lépés: Házirend létrehozása és társítása a replikációs
1. Hozzon létre egy Hyper-V 2012 R2-ben replikációs házirendet a következő parancs futtatásával:

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
    > A VMM-felhő (ahogy azt a Hyper-V előfeltételei) Windows Server különböző verzióit futtató Hyper-V-gazdagépek is tartalmazhat, de a replikációs házirend adott operációsrendszer-verzió. Ha a különböző operációs rendszereken futó különböző gazdagépeken, majd hozza létre külön replikációs házirendet minden operációs rendszer verziója. Például ha öt gazdagépeken futó Windows-kiszolgálók 2012 és Windows Server 2012 R2 három, hozzon létre két replikációs házirendek – egyet az egyes operációs rendszerekhez.

1. Töltse le az elsődleges védelmi tároló (elsődleges VMM-felhőben) és a helyreállítási védelmi tároló (helyreállítási VMM-felhőben) a következő parancsok futtatásával:

       $PrimaryCloud = "testprimarycloud"
       $primaryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  

       $RecoveryCloud = "testrecoverycloud"
       $recoveryprotectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $RecoveryCloud;  
2. A házirend a rövid név használatával 1. lépésben létrehozott szabályzat beolvasása

       $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname
3. Indítsa el a társítást a védelmi tároló (VMM-felhőben) a replikációs házirend:

       $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $primaryprotectionContainer -RecoveryProtectionContainer $recoveryprotectionContainer
4. Várja meg a házirend társítása feladat befejeződik. Ha a feladat befejeződött a következő PowerShell-részlet használatával ellenőrizheti.

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

A művelet a befejezése ellenőrzéséhez kövesse [figyelési tevékenység](#monitor).

## <a name="step-6-configure-network-mapping"></a>6. lépés: Hálózatleképezés konfigurálása
1. Az első parancs kiszolgálók lekéri a jelenlegi Azure Site Recovery-tárolóban. A parancs a Microsoft Azure Site Recovery kiszolgálók $Servers tömb változó tárolja.

        $Servers = Get-AzureRmSiteRecoveryServer
2. A következő parancsot a Site Recovery hálózati lekérése a forrás VMM-kiszolgálón és a cél VMM-kiszolgálóval.

        $PrimaryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]        

        $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]

    > [!NOTE]
    > A forrás VMM-kiszolgálón az elsőt vagy a másikat a kiszolgálók tömb lehet. Ellenőrizze a VMM-kiszolgáló nevét, és a hálózatok megfelelően beolvasása


1. A végső parancsmag hoz létre az elsődleges és a helyreállítási hálózat közötti leképezést. A parancsmag, mint az első elem $PrimaryNetworks és a helyreállítási hálózati $RecoveryNetworks első elemeként adja meg az elsődleges hálózatot.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $PrimaryNetworks[0] -RecoveryNetwork $RecoveryNetworks[0]

## <a name="step-7-configure-storage-mapping"></a>7. lépés: Konfigurálja a tárolási leképezése
1. Az alábbi parancs beolvassa a tárhelybesorolások $storageclassifications változó be listáját.

        $storageclassifications = Get-AzureRmSiteRecoveryStorageClassification
2. A következő parancsokat a $SourceClassificaion változó be forrás besorolást és a cél besorolás $TargetClassification változó be kapják meg.

        $SourceClassificaion = $storageclassifications[0]

        $TargetClassification = $storageclassifications[1]

    > [!NOTE]
    > A forrás- és besorolásokat is lehet bármely a tömb elemei. Tekintse meg a kimeneti az alábbi parancsot, mi a forrás és cél besorolások $storageclassifications tömb indexét.

    > Get-AzureRmSiteRecoveryStorageClassification |} Select-Object - tulajdonság FriendlyName, azonosító |} Táblázat formázása


1. Az alábbi parancsmag létrehoz egy leképezésnek a forrás besorolást és a cél egymáshoz.

        New-AzureRmSiteRecoveryStorageClassificationMapping -PrimaryStorageClassification $SourceClassificaion -RecoveryStorageClassification $TargetClassification

## <a name="step-8-enable-protection-for-virtual-machines"></a>8. lépés: A virtuális gépek védelmének engedélyezése
A kiszolgálók, felhők és hálózatok megfelelő konfigurálását követően engedélyezheti a felhőben található virtuális gépek védelmét.

1. Védelem engedélyezéséhez futtassa a következő parancsot a védelmi tároló:

          $PrimaryProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloudName
2. Töltse le a védelmi entitás (VM) a következő parancs futtatásával:

           $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $PrimaryProtectionContainer
3. A virtuális gép replikációjának engedélyezéséhez a következő parancs futtatásával:

          $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable -Policy $policy

## <a name="test-your-deployment"></a>A központi telepítés tesztelése
Az üzemelő példány el egyetlen virtuális gépre, a feladatátvételi teszt futtatása vagy több virtuális gépet tartalmazó helyreállítási tervet létrehozni és futtassa a terv feladatátvételi tesztjét. A feladatátvételi teszt segítségével elkülönített hálózatban próbálhatja ki a feladatátvételi és helyreállítási mechanizmusokat.

> [!NOTE]
> Az alkalmazás helyreállítási tervet hozhat létre Azure-portálon.
>
>

A művelet a befejezése ellenőrzéséhez kövesse [figyelési tevékenység](#monitor).

### <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása
1. Futtassa az alábbi parancsmagok a Virtuálisgép-hálózatot, amelyhez a feladatátvételi teszt szeretné beolvasni a virtuális gép.

       $Servers = Get-AzureRmSiteRecoveryServer
       $RecoveryNetworks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[1]
2. A virtuális gépek feladatátvételi teszt végrehajtása a következő módon:

       $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMName -ProtectionContainer $PrimaryprotectionContainer

       $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -VMNetwork $RecoveryNetworks[1]
3. A helyreállítási terv feladatátvételi teszt végrehajtása a következő módon:

       $recoveryplanname = "test-recovery-plan"

       $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

       $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan -VMNetwork $RecoveryNetworks[1]

### <a name="run-a-planned-failover"></a>Tervezett feladatátvétel futtatása
1. A virtuális gépek tervezett feladatátvétel végrehajtása a következő módon:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity
2. A helyreállítási terv tervezett feladatátvétel végrehajtása a következő módon:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -Recoveryplan $recoveryplan

### <a name="run-an-unplanned-failover"></a>A nem tervezett feladatátvétel
1. Hajtsa végre a nem tervezett feladatátvételt a virtuális gépek a következő módon:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $PrimaryprotectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

2 a helyreállítási terv nem tervezett feladatátvétel végrehajtása a következő módon:

        $recoveryplanname = "test-recovery-plan"

        $recoveryplan = Get-AzureRmSiteRecoveryRecoveryPlan -FriendlyName $recoveryplanname

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity

## <a name=monitor></a>Figyelési tevékenység
Az alábbi parancsokkal tevékenységének figyelését. Vegye figyelembe, hogy várnia kell, a feldolgozását a Befejezés gombra a feladatok között.

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
[További](/powershell/module/azurerm.recoveryservices.backup/#recovery) Azure Site Recovery Azure Resource Manager PowerShell-parancsmagokkal kapcsolatos.
