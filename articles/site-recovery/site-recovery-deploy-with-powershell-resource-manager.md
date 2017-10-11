---
title: "A PowerShell és az Azure Resource Manager a Hyper-V virtuális gépek replikálása |} Microsoft Docs"
description: "Automatizálhatja a replikálást a Hyper-V virtuális gépek Azure-bA az Azure Site Recovery PowerShell és az Azure Resource Manager használatával."
services: site-recovery
documentationcenter: 
author: bsiva
manager: abhiag
editor: 
ms.assetid: 05e0d76e-c3f5-4845-8052-094019b6d102
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/31/2017
ms.author: bsiva
ms.openlocfilehash: dbd562b73b0caecd0feb993bd6fb796dddb0438c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="replicate-between-on-premises-hyper-v-virtual-machines-and-azure-by-using-powershell-and-azure-resource-manager"></a>PowerShell és az Azure Resource Manager használatával a helyszíni Hyper-V virtuális gépek és az Azure közötti replikáció
> [!div class="op_single_selector"]
> * [Azure Portal](site-recovery-hyper-v-site-to-azure.md)
> * [PowerShell – Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md)
> * [Klasszikus portál](site-recovery-hyper-v-site-to-azure-classic.md)
>
>

## <a name="overview"></a>Áttekintés
Az Azure Site Recovery hozzájárul az üzleti folytonossági és vészhelyreállítási helyreállítási stratégia megvalósításában a replikáció, feladatátvétel és a különböző telepítési forgatókönyvek esetén a virtuális gépek helyreállítása. Központi telepítési forgatókönyvek teljes listáját lásd: a [Azure Site Recovery áttekintését](site-recovery-overview.md).

Az Azure PowerShell modul, amely kezelése a Windows PowerShell segítségével Azure-parancsmagokat kínál. Együtt tudjon működni az kétféle modulok: az Azure-profil modul, vagy az Azure Resource Manager modul.

Site Recovery PowerShell parancsmagokat, elérhető az Azure PowerShell az Azure Resource Manager segítségével védelme és helyreállítása a kiszolgálók az Azure-ban.

A cikkből megtudhatja, hogyan használhatja a Windows PowerShell, együtt Azure Resource Manager konfigurálását és levezényléséhez server védelme az Azure Site Recovery üzembe. A példában ez a cikk bemutatja, hogyan védelme, a feladatátvétel, és az Azure-ba, a Hyper-V gazdagépen található virtuális gépek helyreállítása Azure PowerShell használatával az Azure Resource Manager eszközzel.

> [!NOTE]
> A Site Recovery PowerShell parancsmagokat jelenleg lehetővé teszik a következő konfigurálását: egy a Virtual Machine Manager-helyet, amely egy másik, a Virtual Machine Manager helyet az Azure és a Hyper-V helyet az Azure-bA.
>
>

Nem kell lennie a PowerShell használatával Ez a cikk szakértői, de meg kell ismernie az alapszintű fogalmakkal, mint a modulok, a parancsmagok és a munkamenetek. További információ a Windows PowerShellről: [Ismerkedés a Windows PowerShell-lel](http://technet.microsoft.com/library/hh857337.aspx).

Akkor is olvashat további tudnivalókat [az Azure PowerShell használata Azure Resource Managerrel](../powershell-azure-resource-manager.md).

> [!NOTE]
> A Cloud Solution Provider (CSP) program részét képező Microsoft-partnerek konfigurálható és kezelhető az ügyfelek megfelelő CSP előfizetések (bérlői előfizetések) az ügyfelek kiszolgálók védelmét.
>
>

## <a name="before-you-start"></a>Előkészületek
Győződjön meg arról, hogy az előfeltételek teljesülnek:

* A [Microsoft Azure](https://azure.microsoft.com/) fiók. Kezdésként használhatja az [ingyenes próbaverziót](https://azure.microsoft.com/pricing/free-trial/) is. Emellett áttekintheti az [Azure Site Recovery Manager árképzési](https://azure.microsoft.com/pricing/details/site-recovery/).
* Azure PowerShell 1.0. Ebben a kiadásban és telepítéséről kapcsolatos információkért lásd: [Azure PowerShell 1.0.](https://azure.microsoft.com/)
* A [AzureRM.SiteRecovery](https://www.powershellgallery.com/packages/AzureRM.SiteRecovery/) és [AzureRM.RecoveryServices](https://www.powershellgallery.com/packages/AzureRM.RecoveryServices/) modulok. Ezek a modulok a legújabb verziói kaphat a [PowerShell-galériában](https://www.powershellgallery.com/)

Ez a cikk bemutatja, hogyan használhatja az Azure Powershellt Azure Resource Manager konfigurálását és kezelését a kiszolgálók védelmét. A példában ez a cikk bemutatja, hogyan védi meg egy olyan virtuális géphez, a Hyper-V gazdagépen, az Azure-bA. Ebben a példában a következő előfeltételek vonatkoznak. Átfogóbb bizonyos követelmények a Site Recovery több, különböző esetekre tekintse meg a forgatókönyv vonatkozó dokumentációt.

* A Hyper-V gazdagépen fut a Windows Server 2012 R2 vagy Microsoft Hyper-V Server 2012 R2 tartalmazó egy vagy több virtuális géphez.
* Hyper-V kiszolgálók csatlakozik az internethez, közvetlen vagy proxyn keresztül.
* A védeni kívánt virtuális gépek meg kell felelnie az [a virtuális gép előfeltételei](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

## <a name="step-1-sign-in-to-your-azure-account"></a>1. lépés: Bejelentkezés az Azure-fiókjával
1. Nyisson meg egy PowerShell-konzolt, és futtassa ezt a parancsot az Azure-fiókjával bejelentkezhet. A parancsmag megnyitása egy weblap, amelyen kérni fogja a hitelesítő adatait.

        Login-AzureRmAccount

    Alternatív megoldásként akkor is lehetnek a fiók hitelesítő adataival paramétereként a `Login-AzureRmAccount` parancsmag használatával a `-Credential` paraméter.

    Ha működik a bérlő nevében CSP partner, adja meg az ügyfél a bérlők a tenantID vagy bérlői elsődleges tartománynév használatával.

        Login-AzureRmAccount -Tenant "fabrikam.com"
2. Egy fiók több előfizetések van így társítania kell a fiókhoz használni kívánt előfizetést.

        Select-AzureRmSubscription -SubscriptionName $SubscriptionName
3. Győződjön meg arról, hogy az előfizetés regisztrálva van-e az Azure szolgáltatók használatához a Recovery Services és a hely helyreállítását követően a következő parancsokkal:

   * `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices`
   * `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`

   A kimenet parancsok Ha a **RegistrationState** értéke **regisztrált**, folytathatja a 2. lépés. Ha nem, a hiányzó szolgáltató regisztrálnia kell az előfizetésben.

   Az Azure-szolgáltató regisztrálása a Site Recovery és helyreállítási szolgáltatásokhoz, a következő parancsokat:

       Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SiteRecovery
       Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices

   Győződjön meg arról, hogy a szolgáltatók regisztrálása sikeresen befejeződött a következő parancsokkal: `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` és `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`.

## <a name="step-2-set-up-the-recovery-services-vault"></a>2. lépés: A Recovery Services-tároló beállítása
1. Hozzon létre egy Azure Resource Manager-csoportot, amelyben meg kell hozzon létre a tárolót, vagy használjon egy meglévő erőforráscsoportot. Új erőforráscsoport létrehozásához a következő parancsot:

        New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Geo  

    ahol a $ResourceGroupName változó szeretne létrehozni az erőforráscsoport nevét tartalmazza, és a $Geo változó tartalmazza az Azure-régió, amihez létre kívánja hozni az erőforráscsoportot (például "Dél-Brazília") a.

    Ezt úgy szerezheti be az erőforráscsoportok listáját az előfizetésében használatával a `Get-AzureRmResourceGroup` parancsmag.
2. Hozzon létre egy új Azure Recovery Services-tároló az alábbiak szerint:

        $vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>

    Meglévő tárolók listája használatával kérheti le a `Get-AzureRmRecoveryServicesVault` parancsmag.

> [!NOTE]
> Ha a klasszikus portálon vagy az Azure Service Management PowerShell-modul segítségével létrehozni a Site Recovery-tárolóhoz a műveletek végrehajtásához, használatával kérheti le az ilyen tárolók listája az `Get-AzureRmSiteRecoveryVault` parancsmag. Hozzon létre egy új Recovery Services-tároló minden új műveletnél. A Site Recovery-tárolóhoz, korábban létrehozott támogatottak, de nem rendelkezik a legújabb funkciókat.
>
>

## <a name="step-3-set-the-recovery-services-vault-context"></a>3. lépés: A Recovery Services-tároló környezet beállítása
1. A tároló környezet beállítása a következő parancs futtatásával:

       Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault

## <a name="step-4-create-a-hyper-v-site-and-generate-a-new-vault-registration-key-for-the-site"></a>4. lépés: Hyper-V-hely létrehozása, és hozzon létre egy új tároló regisztrációs kulcsot a hely.
1. Hozzon létre egy új Hyper-V hely az alábbiak szerint:

        $sitename = "MySite"                #Specify site friendly name
        New-AzureRmSiteRecoverySite -Name $sitename

    Ez a parancsmag elindítja a Site Recovery feladatot, a webhely létrehozása, és a Site Recovery feladat objektum beállítása/beolvasása. Várjon, amíg a feladat befejeződését, és ellenőrizze, hogy a feladat sikeresen befejeződött.

    Kérje le a feladat objektumot, és ezáltal a feladat aktuális állapotának ellenőrzése a Get-AzureRmSiteRecoveryJob parancsmag használatával.
2. Létrehozni, és töltse le a hely egy regisztrációs kulcsot az alábbiak szerint:

        $SiteIdentifier = Get-AzureRmSiteRecoverySite -Name $sitename | Select -ExpandProperty SiteIdentifier
        Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename -Path $Path

    Másolja a letöltött kulcs a Hyper-V gazdagépen. A Hyper-V gazdagép, a hely regisztrálja a kulccsal van szüksége.

## <a name="step-5-install-the-azure-site-recovery-provider-and-azure-recovery-services-agent-on-your-hyper-v-host"></a>5. lépés: Az Azure Site Recovery provider és az Azure Recovery Services Agent telepítését a Hyper-V gazdagépen
1. A telepítő a szolgáltató legújabb verziójának letöltése [Microsoft](https://aka.ms/downloaddra).
2. A regisztrációs lépésében továbbra is futtatható a telepítő a Hyper-V gazdagépen, és a telepítés végén.
3. Amikor a rendszer kéri, adja meg a letöltött hely regisztrációs kulcsot, és a Hyper-V gazdagép a hely teljes regisztrálását.
4. Győződjön meg arról, hogy a Hyper-V gazdagép regisztrálva van a helyhez a következő paranccsal:

        $server =  Get-AzureRmSiteRecoveryServer -FriendlyName $server-friendlyname

## <a name="step-6-create-a-replication-policy-and-associate-it-with-the-protection-container"></a>6. lépés: Hozzon létre egy replikációs házirendet, és rendelje hozzá azt a védelmi tároló
1. Egy replikációs házirend létrehozásához az alábbiak szerint:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                    #specify the number of recovery points
        $storageaccountID = Get-AzureRmStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id

        $PolicyResult = New-AzureRmSiteRecoveryPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

    Ellenőrizze a visszaadott feladatot annak érdekében, hogy a replikációs házirend létrehozása sikeres.

   > [!IMPORTANT]
   > A megadott tárfiók ugyanabban a régióban Azure és a Recovery Services-tárolónak kell lennie, és rendelkeznie kell a georeplikáció engedélyezve van.
   >
   > * Ha a megadott helyreállítási tárfiók típusú Azure Storage (klasszikus), a feladatátvétel a védett gépek Azure IaaS (klasszikus) a gép állítható helyre.
   > * Ha a megadott helyreállítási tárfiók típusú Azure Storage (az Azure Resource Manager), a védett gépek feladatátvétele Azure IaaS (Azure Resource Manager) a gép állítható helyre.
   >
   >
2. Töltse le a védelmi tároló megfelelő a helyhez, az alábbiak szerint:

        $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer
3. Indítsa el a társítást a védelmi tároló a replikációs házirend a következőképpen:

     $Policy = get-AzureRmSiteRecoveryPolicy - FriendlyName $PolicyName $associationJob = a Start-AzureRmSiteRecoveryPolicyAssociationJob-házirend $Policy - PrimaryProtectionContainer $protectionContainer

   Várjon, amíg a társítás feladat befejeződik, és győződjön meg arról, hogy sikeresen befejeződött-e.

## <a name="step-7-enable-protection-for-virtual-machines"></a>7. lépés: Virtuális gépek védelmének engedélyezése
1. Töltse le a megfelelő módon védeni kívánt virtuális gép védelmi entitás:

        $VMFriendlyName = "Fabrikam-app"                    #Name of the VM
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
2. Indítsa el a virtuális gép védelmét az alábbiak szerint:

        $Ostype = "Windows"                                 # "Windows" or "Linux"
        $DRjob = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity -Policy $Policy -Protection Enable -RecoveryAzureStorageAccountId $storageaccountID  -OS $OStype -OSDiskName $protectionEntity.Disks[0].Name

   > [!IMPORTANT]
   > A megadott tárfiók ugyanabban a régióban Azure és a Recovery Services-tárolónak kell lennie, és rendelkeznie kell a georeplikáció engedélyezve van.
   >
   > * Ha a megadott helyreállítási tárfiók típusú Azure Storage (klasszikus), a feladatátvétel a védett gépek Azure IaaS (klasszikus) a gép állítható helyre.
   > * Ha a megadott helyreállítási tárfiók típusú Azure Storage (az Azure Resource Manager), a védett gépek feladatátvétele Azure IaaS (Azure Resource Manager) a gép állítható helyre.
   >
   > Ha a védett virtuális gép nem csatlakoztatható egynél több lemez, adja meg az operációsrendszer-lemez használatával a *OSDiskName* paraméter.
   >
   >
3. Várjon, amíg a kezdeti replikálás után a védett állapotot elérni a virtuális gépeket. Ez eltarthat egy ideig, például replikálni adatok mennyisége és az Azure-bA fölérendelt rendelkezésre álló sávszélességet tényezőktől függően. A feladat állapotát és StateDescription frissítve lett, az alábbiak szerint a virtuális gép védett állapotban elérése után.

        PS C:\> $DRjob = Get-AzureRmSiteRecoveryJob -Job $DRjob

        PS C:\> $DRjob | Select-Object -ExpandProperty State
        Succeeded

        PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
        Completed
4. Frissítse a helyreállítási tulajdonságait, például a Virtuálisgép-szerepkör méretéhez és csatolni a virtuális gép hálózati kártya feladatátvétel esetén az Azure-hálózatot.

        PS C:\> $nw1 = Get-AzureRmVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

        PS C:\> $VMFriendlyName = "Fabrikam-App"

        PS C:\> $VM = Get-AzureRmSiteRecoveryVM -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

        PS C:\> $UpdateJob = Set-AzureRmSiteRecoveryVM -VirtualMachine $VM -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

        PS C:\> $UpdateJob = Get-AzureRmSiteRecoveryJob -Job $UpdateJob

        PS C:\> $UpdateJob

        Name             : b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        ID               : /Subscriptions/a731825f-4bf2-4f81-a611-c331b272206e/resourceGroups/MyRG/providers/Microsoft.RecoveryServices/vault
                           s/MyVault/replicationJobs/b8a647e0-2cb9-40d1-84c4-d0169919e2c5
        Type             : Microsoft.RecoveryServices/vaults/replicationJobs
        JobType          : UpdateVmProperties
        DisplayName      : Update the virtual machine
        ClientRequestId  : 805a22a3-be86-441c-9da8-f32685673112-2015-12-10 17:55:51Z-P
        State            : Succeeded
        StateDescription : Completed
        StartTime        : 10-12-2015 17:55:53 +00:00
        EndTime          : 10-12-2015 17:55:54 +00:00
        TargetObjectId   : 289682c6-c5e6-42dc-a1d2-5f9621f78ae6
        TargetObjectType : ProtectionEntity
        TargetObjectName : Fabrikam-App
        AllowedActions   : {Restart}
        Tasks            : {UpdateVmPropertiesTask}
        Errors           : {}



## <a name="step-8-run-a-test-failover"></a>8. lépés: Feladatátvételi teszt futtatása
1. A tesztfeladat feladatátvételt futtassa a következőképpen:

        $nw = Get-AzureRmVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMFriendlyName -ProtectionContainer $protectionContainer

        $TFjob = Start-AzureRmSiteRecoveryTestFailoverJob -ProtectionEntity $protectionEntity -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
2. Győződjön meg arról, hogy a teszt virtuális gép létrehozása az Azure-ban. (A teszt feladatátvételi feladatot fel van függesztve, az Azure-ban a teszteléshez használt virtuális gép létrehozása után. A feladat be visszakapcsolását a feladat létrehozott vakpróbát törléséről a következő lépésben ismertetett módon.)
3. Végezze el a feladatátvételi tesztet, az alábbiak szerint:

        $TFjob = Resume-AzureRmSiteRecoveryJob -Job $TFjob

## <a name="next-steps"></a>Következő lépések
[További](https://msdn.microsoft.com/library/azure/mt637930.aspx) Azure Site Recovery Azure Resource Manager PowerShell-parancsmagokkal kapcsolatos.
