---
title: "Hyper-V virtuális gépek VMM-felhőkben Azure Site Recovery és a PowerShell használatával (erőforrás-kezelő) replikálása |} Microsoft Docs"
description: "Hyper-V virtuális gépek replikálása az Azure Site Recovery és a PowerShell használatával a VMM-felhők"
services: site-recovery
documentationcenter: 
author: Rajani-Janaki-Ram
manager: rochakm
editor: raynew
ms.assetid: 6ac509ad-5024-43d8-b621-d8fec019b9a9
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: rajanaki
ms.openlocfilehash: cc832d06611c10901d4370dc7467f0b681d89cbd
ms.sourcegitcommit: c25cf136aab5f082caaf93d598df78dc23e327b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2017
---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-powershell-and-azure-resource-manager"></a>VMM-felhőkben Hyper-V virtuális gépek replikálása az Azure PowerShell és az Azure Resource Manager használatával
> [!div class="op_single_selector"]
> * [Azure Portal](site-recovery-vmm-to-azure.md)
> * [PowerShell – Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
> * [Klasszikus portál](site-recovery-vmm-to-azure-classic.md)
> * [PowerShell – Klasszikus](site-recovery-deploy-with-powershell.md)
>
>

## <a name="overview"></a>Áttekintés
Az Azure Site Recovery hozzájárul az üzleti folytonossági és vészhelyreállítási (BCDR) helyreállítási stratégia megvalósításában replikációjának, feladatátvételének és helyreállítási virtuális gép a különböző telepítési forgatókönyvek esetén. Teljes listáját a telepítési forgatókönyvek tekintse meg a [Azure Site Recovery áttekintését](site-recovery-overview.md).

Ez a cikk bemutatja, hogyan kell végrehajtani, amikor állít be Azure Site Recovery System Center VMM-felhőkben Hyper-V virtuális gépek replikálása az Azure storage gyakori feladatok automatizálása a PowerShell használatával.

A cikk a forgatókönyv előfeltételei tartalmazza, és bemutatja

* A Recovery Services-tároló beállítása
* A forrás VMM-kiszolgálón az Azure Site Recovery Provider telepítése
* Regisztrálja a kiszolgálót a tárolóban, az Azure-tárfiók hozzáadása
* Az Azure Recovery Services agent telepítése a Hyper-V gazdakiszolgálókra
* VMM-felhő, az összes védett virtuális gépek alkalmazandó védelmi beállításainak konfigurálása
* Ezen virtuális gépek védelmének engedélyezése.
* Győződjön meg arról, hogy minden a feladatátvételi teszt a várt módon működik.

Ha ez a forgatókönyv beállítása problémát tapasztal, kérdéseit a a [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

> [!NOTE]
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk a Resource Manager telepítési modell használatát bemutatja.
>
>

## <a name="before-you-start"></a>Előkészületek
Győződjön meg arról, hogy az előfeltételek teljesülnek:

### <a name="azure-prerequisites"></a>Azure-előfeltételek
* Szüksége lesz egy [Microsoft Azure](https://azure.microsoft.com/)-fiókra. Ha még nincs fiókja, kezdje a [ingyenes fiókot](https://azure.microsoft.com/free). Emellett áttekintheti az a [Azure Site Recovery Manager árképzési](https://azure.microsoft.com/pricing/details/site-recovery/).
* Ha próbálja ki a replikációt egy CSP-előfizetés forgatókönyv szüksége lesz egy CSP-előfizetést. További információ a CSP programra [hogyan regisztrálja az eszközt, a CSP programban](https://msdn.microsoft.com/library/partnercenter/mt156995.aspx).
* Szüksége lesz egy Azure v2 (erőforrás-kezelő) storage-fiókot az Azure-bA replikált adatok tárolására. A fiókot kell georeplikáció engedélyezve van. Azt az Azure Site Recovery szolgáltatásnak ugyanabban a régióban legyen, és ugyanahhoz az előfizetéshez vagy a CSP-előfizetés. Az Azure storage beállításával kapcsolatos további tudnivalókért tekintse meg a [Microsoft Azure Storage bemutatása](../storage/common/storage-introduction.md) referenciaként.
* Győződjön meg arról, hogy védeni kívánt virtuális gépek ahhoz kell a [Azure virtuális gép Előfeltételek](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

> [!NOTE]
> Csak a virtuális gép szintű műveletek jelenleg Powershellen keresztül lehetséges. Helyreállítási terv szintű műveletek támogatása hamarosan fog történni.  Most azonban legfeljebb sikertelenek-garantál csak a "védett virtuális gép" lépésköz és nem a helyreállítási terv szintű végrehajtására.
>
>

### <a name="vmm-prerequisites"></a>A VMM-Előfeltételek
* A System Center 2012 R2 rendszeren futó VMM-kiszolgáló lesz szüksége.
* Minden védeni kívánt virtuális gépeket tartalmazó VMM-kiszolgálón futnia kell az Azure Site Recovery Provider. Ez az Azure Site Recovery üzembe helyezése során telepítve van.
* A védeni kívánt VMM-kiszolgálón legalább egy felhő lesz szüksége. A felhők tartalmazzák:
  * Legalább egy VMM-gazdagépcsoport.
  * Minden gazdagépcsoportban legalább egy Hyper-V gazdakiszolgáló vagy fürt.
  * Egy vagy több virtuális gépnek a forrás Hyper-V kiszolgálón.
* További információ a VMM-felhőkben beállítása:
  * További információk a VMM-magánfelhőkben [újdonságai a System Center 2012 R2 VMM Magánfelhő](http://go.microsoft.com/fwlink/?LinkId=324952) és a [VMM 2012 és a felhők](http://go.microsoft.com/fwlink/?LinkId=324956).
  * További tudnivalók [konfigurálása a VMM felhőbeli háló](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
  * A magánfelhők létrehozásával kapcsolatos további követően a felhőbeli háló elemek helyen [magánfelhő létrehozása a VMM Alkalmazásban](http://go.microsoft.com/fwlink/p/?LinkId=324953) és a [bemutató: magánfelhők létrehozása a System Center 2012 SP1 VMM](http://go.microsoft.com/fwlink/p/?LinkId=324954).

### <a name="hyper-v-prerequisites"></a>Hyper-V előfeltételei
* A Hyper-V gazdakiszolgálókon legalább futnia kell **Windows Server 2012** Hyper-V szerepkörrel vagy **Microsoft Hyper-V Server 2012** és a legújabb frissítések telepítve van.
* Ha fürtben futtatja a Hyper-V-t, ne feledje, hogy ha statikus IP-címen alapuló fürtöt használ, a rendszer nem hozza létre automatikusan a fürtszervezőt. Ebben az esetben manuálisan kell konfigurálnia a fürtszervezőt. A
* Útmutatásért lásd: [hogyan konfigurálja a Hyper-V Replikaszervező](http://blogs.technet.com/b/haroldwong/archive/2013/03/27/server-virtualization-series-hyper-v-replica-broker-explained-part-15-of-20-by-yung-chou.aspx).
* Bármely Hyper-V gazdakiszolgáló vagy fürt, amelynek védelmi kezelni kívánt szerepelnie kell egy VMM-felhőben.

### <a name="network-mapping-prerequisites"></a>Hálózatleképezési előfeltételek
Ha Azure, a hálózatleképezés kapcsolatot hoz létre a virtuálisgép-hálózatok a forrás VMM-kiszolgálón lévő virtuális gépek védelmét, és cél Azure-hálózatok engedélyezéséhez a következő:

* Minden olyan gép, amelyek ugyanazon a hálózaton keresztül nem képes csatlakozni egymáshoz, melyik helyreállítási tervhez függetlenül vannak.
* Ha a cél Azure-hálózatban hálózati átjáró működik, a virtuális gépek képesek csatlakozni a többi helyszíni virtuális géphez.
* Hálózatleképezés ne állítson be, ha csak az azonos helyreállítási tervben feladatátvételt virtuális gépek tudnak csatlakozni egymáshoz az Azure-bA a feladatátvétel után.

Hálózatleképezés üzembe helyezéséhez a következőkre lesz szüksége:

* A forrás VMM-kiszolgálón futó, védelemmel ellátni kívánt virtuális gépeknek csatlakozniuk kell egy virtuálisgép-hálózathoz. Ezt a hálózatot kösse össze egy, a felhőhöz társított logikai hálózattal.
* Az Azure-hálózatot, amelyhez a replikált virtuális gépek feladatátvételi után csatlakozhat. Ezt a hálózatot a feladatátvételi időpontjában válasszon ki. A hálózatnak és az Azure Site Recovery-előfizetésnek ugyanahhoz a régióhoz kell tartoznia.

További információ a hálózatra való leképezés

* [Logikai hálózatok konfigurálása a VMM-ben](http://go.microsoft.com/fwlink/p/?LinkId=386307)
* [A Virtuálisgép-hálózatok és átjárók konfigurálása a VMM-ben](http://go.microsoft.com/fwlink/p/?LinkId=386308)
* [Konfigurálása és a virtuális hálózatok figyelése az Azure-ban](https://azure.microsoft.com/documentation/services/virtual-network/)

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
1. Hozzon létre egy erőforráscsoportot az Azure Resource Manager Ha még nem rendelkezik már

        New-AzureRmResourceGroup -Name #ResourceGroupName -Location #location
2. Hozzon létre egy új Recovery Services-tároló, és mentse a létrehozott automatikus rendszer-Helyreállítás tároló objektum egy változóban (használandó később). Az automatikus rendszer-Helyreállítás tároló objektum post létrehozása a Get-AzureRMRecoveryServicesVault parancsmaggal is lekérhet:-

        $vault = New-AzureRmRecoveryServicesVault -Name #vaultname -ResouceGroupName #ResourceGroupName -Location #location

## <a name="step-3-set-the-recovery-services-vault-context"></a>3. lépés: A Recovery Services-tároló környezet beállítása

A tároló környezet beállítása futtatásával az alábbi parancsot.

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

## <a name="step-5-create-an-azure-storage-account"></a>5. lépés: Az Azure storage-fiók létrehozása

Egy Azure storage-fiók nem rendelkezik, ha engedélyezett a georeplikáció fiók létrehozása és a tárolónak ugyanazon földrajzi a következő parancs futtatásával:

        $StorageAccountName = "teststorageacc1"    #StorageAccountname
        $StorageAccountGeo  = "Southeast Asia"     
        $ResourceGroupName =  “myRG”             #ResourceGroupName
        $RecoveryStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Type “StandardGRS” -Location $StorageAccountGeo

Vegye figyelembe, hogy a tárfiókot kell az Azure Site Recovery szolgáltatásnak ugyanabban a régióban legyen, és társítható ugyanahhoz az előfizetéshez.

## <a name="step-6-install-the-azure-recovery-services-agent"></a>6. lépés: Az Azure Recovery Services Agent telepítése
1. Töltse le az Azure Recovery Services Agent ügynököt a [http://aka.ms/latestmarsagent](http://aka.ms/latestmarsagent) telepítse azt minden védeni kívánt VMM-felhőkben található Hyper-V gazdagép-kiszolgálón.
2. A következő parancsot az összes VMM-gazdagépek:

       marsagentinstaller.exe /q /nu

## <a name="step-7-configure-cloud-protection-settings"></a>7. lépés: Konfigurálja a felhő védelmi beállításait
1. Hozzon létre egy replikációs házirendet, az Azure-bA az alábbi parancs futtatásával:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                    #specify the number of recovery points

        $policryresult = New-AzureRmSiteRecoveryPolicy -Name $policyname -ReplicationProvider HyperVReplicaAzure -ReplicationFrequencyInSeconds $replicationfrequencyinseconds -RecoveryPoints $recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId "/subscriptions/q1345667/resourceGroups/test/providers/Microsoft.Storage/storageAccounts/teststorageacc1"

1. A védelmi tároló érhető el a következő parancsok futtatásával:

       $PrimaryCloud = "testcloud"
       $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $PrimaryCloud;  
2. Ezzel a házirend adatokat egy változóhoz, a feldolgozással létrehozott és a rövid házirendnév megemlíteni:

       $policy = Get-AzureRmSiteRecoveryPolicy -FriendlyName $policyname
3. Indítsa el a társítást a védelmi tároló a replikációs házirend:

       $associationJob  = Start-AzureRmSiteRecoveryPolicyAssociationJob -Policy     $Policy -PrimaryProtectionContainer $protectionContainer  
4. A feladat befejeződését követően futtassa a következő parancsot:

       $job = Get-AzureRmSiteRecoveryJob -Job $associationJob

       if($job -eq $null -or $job.StateDescription -ne "Completed")
       {
         $isJobLeftForProcessing = $true;
       }
5. A feladat feldolgozása után futtassa a következő parancsot:

       if($isJobLeftForProcessing)
       {
         Start-Sleep -Seconds 60
       }
       }While($isJobLeftForProcessing)

A művelet a befejezése ellenőrzéséhez kövesse [figyelési tevékenység](#monitor).

## <a name="step-8-configure-network-mapping"></a>8. lépés: Hálózatleképezés konfigurálása
A hálózatleképezés megkezdése előtt ellenőrizze, hogy a forrás VMM-kiszolgálón működő virtuális gépek csatlakoznak-e a virtuálisgép-hálózathoz. Továbbá hozzon létre legalább egy Azure virtuális hálózatot.

További tudnivalók a virtuális hálózat létrehozása Azure Resource Manager és a PowerShell, a [virtuális hálózat létrehozása az Azure Resource Manager és a PowerShell használatával pont-pont VPN-kapcsolattal](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

Vegye figyelembe, hogy egyetlen Azure-hálózatra több virtuálisgép-hálózatokhoz rendelhetők. Ha a célhálózatban már több alhálózat működik, és ezek egyikének azonos a neve, mint amelyen a forrás virtuális gép is található, majd a replika virtuális géphez csatlakoznak a cél alhálózathoz feladatátvételi után. Ha nincsenek egyező nevű cél alhálózathoz, a virtuális gép a hálózat első alhálózathoz csatlakoznak.

1. Az első parancs kiszolgálók lekéri a jelenlegi Azure Site Recovery-tárolóban. A parancs a Microsoft Azure Site Recovery kiszolgálók $Servers tömb változó tárolja.

        $Servers = Get-AzureRmSiteRecoveryServer
2. A második parancs a helyreállítási helyen lévő hálózat a $Servers tömb első kiszolgáló kéri. A parancs a hálózatok $Networks változó tárolja.

        $Networks = Get-AzureRmSiteRecoveryNetwork -Server $Servers[0]

1. A harmadik parancs a $AzureVmNetworks változóban kéri az Azure virtuális hálózataihoz, és ezt az értéket.

        $AzureVmNetworks =  Get-AzureRmVirtualNetwork
2. A végső parancsmag hoz létre az elsődleges hálózatot és az Azure virtuális gép hálózati közötti leképezést. A parancsmag $Networks első elemeként adja meg az elsődleges hálózatot. A parancsmag egy virtuális gép hálózati $AzureVmNetworks első elemeként adja meg.

        New-AzureRmSiteRecoveryNetworkMapping -PrimaryNetwork $Networks[0] -AzureVMNetworkId $AzureVmNetworks[0]

## <a name="step-9-enable-protection-for-virtual-machines"></a>9. lépés: Virtuális gépek védelmének engedélyezése
A kiszolgálók, felhők és hálózatok megfelelő konfigurálását követően engedélyezheti a felhőben található virtuális gépek védelmét.

 Vegye figyelembe a következőket:

* Virtuális gépek Azure-követelményeknek kell megfelelniük. Ellenőrizze, hogy ezek szerepel [Előfeltételek és a támogatás](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) a tervezési útmutatóban.
* Engedélyezze a védelmet, az operációs rendszer és az operációs rendszer lemez tulajdonságainak kell állítani a virtuális géphez. A tulajdonságokat megadhatja például, amikor virtuálisgép-sablon segítségével virtuális gépeket hoz létre a VMM-ben. A már létrehozott virtuális gépek tulajdonságait a virtuális gép tulajdonságaiban, az **Általános** és a **Hardverkonfiguráció** lapon állíthatja be. Ha a VMM-ben nem állította be ezeket a tulajdonságokat, a konfigurációt az Azure Site Recovery portálon is elvégezheti.

1. Védelem engedélyezéséhez futtassa a következő parancsot a védelmi tároló:

          $ProtectionContainer = Get-AzureRmSiteRecoveryProtectionContainer -friendlyName $CloudName
2. Töltse le a védelmi entitás (VM) a következő parancs futtatásával:

           $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -friendlyName $VMName -ProtectionContainer $protectionContainer
3. A virtuális gép DR engedélyezése a következő parancs futtatásával:

          $jobResult = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionentity -Protection Enable –Force -Policy $policy -RecoveryAzureStorageAccountId  $storageID "/subscriptions/217653172865hcvkchgvd/resourceGroups/rajanirgps/providers/Microsoft.Storage/storageAccounts/teststorageacc1

## <a name="test-your-deployment"></a>A központi telepítés tesztelése
A telepítés el futtassa a teszt feladatátvételi egyetlen virtuális géphez, vagy hozzon létre több virtuális gépet tartalmazó helyreállítási tervet, és futtassa a teszt feladatátvételi a terv tesztelésére. Feladatátvételi teszt elszigetelt hálózatot a feladatátvételi és helyreállítási mechanizmusokat szimulálja. Vegye figyelembe:

* Szeretne csatlakozni a virtuális géphez a távoli asztali kapcsolat segítségével a feladatátvétel után, ha engedélyezi a távoli asztali kapcsolat a virtuális gépen, a feladatátvételi teszt futtatása előtt.
* Után feladatátvételi egy nyilvános IP-címet fogja használni a távoli asztali kapcsolat segítségével a virtuális gép csatlakozni. Ha ezt szeretné tenni, ellenőrizze, hogy nincsenek-e érvényben tartományi szabályzatok, amelyek meggátolják, hogy nyilvános cím segítségével csatlakozzon a virtuális gépekhez.

A művelet a befejezése ellenőrzéséhez kövesse [figyelési tevékenység](#monitor).

### <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása
- A feladatátvételi teszt indítása a következő parancs futtatásával:

       $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

       $jobIDResult =  Start-AzureRmSiteRecoveryTestFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### <a name="run-a-planned-failover"></a>Tervezett feladatátvétel futtatása
- Indítsa el a tervezett feladatátvétel a következő parancs futtatásával:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

### <a name="run-an-unplanned-failover"></a>A nem tervezett feladatátvétel
- Indítsa el a nem tervezett feladatátvétel a következő parancs futtatásával:

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -Name $VMName -ProtectionContainer $protectionContainer

        $jobIDResult =  Start-AzureRmSiteRecoveryUnPlannedFailoverJob -Direction PrimaryToRecovery -ProtectionEntity $protectionEntity -AzureVMNetworkId <string>  

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
