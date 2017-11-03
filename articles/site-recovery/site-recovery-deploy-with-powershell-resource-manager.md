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
ms.date: 10/19/2017
ms.author: bsiva
ms.openlocfilehash: d93be3b958f85cd2892f92d84ed68996909a5d6b
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2017
---
# <a name="set-up-disaster-recovery-to-azure-for-hyper-v-vms-using-powershell-and-azure-resource-manager"></a>Állítsa be az Azure-bA vész-helyreállítási Hyper-V virtuális gépek PowerShell és az Azure Resource Manager használatával

[Az Azure Site Recovery](site-recovery-overview.md) járul hozzá az üzletmenet folytonosságát és a vész-helyreállítási (BCDR) stratégia megvalósításában a replikáció, feladatátvétel és helyreállítás Azure virtuális gépek (VM), és a helyszíni virtuális gépek és fizikai kiszolgálók.

A cikkből megtudhatja, hogyan használható a Windows PowerShell, együtt Azure Resource Manager Hyper-V virtuális gépek replikálása az Azure-bA. A példában ez a cikk bemutatja, hogyan egy, az Azure-bA egy Hyper-V állomáson futó virtuális replikálásához.

## <a name="overview"></a>Áttekintés

Az Azure PowerShell kezelése az Azure-ban a Windows PowerShell-parancsmagokat kínál. Site Recovery PowerShell parancsmagokat, elérhető az Azure PowerShell az Azure Resource Manager segítségével védelme és helyreállítása a kiszolgálók az Azure-ban.

Nem kell lennie a PowerShell használatával Ez a cikk szakértői, de meg kell ismernie az alapszintű fogalmakkal, mint a modulok, a parancsmagok és a munkamenetek. Olvasási [Ismerkedés a Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx), és [az Azure PowerShell használata Azure Resource Managerrel](../powershell-azure-resource-manager.md).

> [!NOTE]
> Microsoft-partnereknek, a Cloud Solution Provider (CSP) programban konfigurálható és kezelhető a megfelelő CSP-előfizetésekhez (bérlői előfizetések) vevő kiszolgálók védelmét.
>
>

## <a name="before-you-start"></a>Előkészületek
Győződjön meg arról, hogy az előfeltételek teljesülnek:

* A [Microsoft Azure](https://azure.microsoft.com/) fiók. Kezdésként használhatja az [ingyenes próbaverziót](https://azure.microsoft.com/pricing/free-trial/) is. Emellett áttekintheti az [Azure Site Recovery Manager árképzési](https://azure.microsoft.com/pricing/details/site-recovery/).
* Azure PowerShell 1.0. Ebben a kiadásban és telepítéséről kapcsolatos információkért lásd: [Azure PowerShell 1.0.](https://azure.microsoft.com/)
* A [AzureRM.SiteRecovery](https://www.powershellgallery.com/packages/AzureRM.SiteRecovery/) és [AzureRM.RecoveryServices](https://www.powershellgallery.com/packages/AzureRM.RecoveryServices/) modulok. Ezek a modulok a legújabb verziói kaphat a [PowerShell-galériában](https://www.powershellgallery.com/)

Emellett az adott példa cikkben leírt előfeltételei a következők:

* Windows Server 2012 R2 vagy Microsoft Hyper-V Server 2012 R2 egy vagy több virtuális gépeket tartalmazó rendszeren futó Hyper-V gazdagéphez. Közvetlen vagy proxyn keresztüli Hyper-V kiszolgálók kell csatlakoztatni az internethez.
* A replikálni kívánt virtuális gépeket meg kell felelnie az [ezekről az előfeltételekről](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

## <a name="step-1-sign-in-to-your-azure-account"></a>1. lépés: Bejelentkezés az Azure-fiókjával

1. Nyisson meg egy PowerShell-konzolt, és futtassa ezt a parancsot az Azure-fiókjával bejelentkezhet. A parancsmag megnyitása egy weblap bekéri a hitelesítő adatait: **Login-AzureRmAccount**.
    - Alternatív megoldásként paraméterként megadhat a fiók hitelesítő adatait a **Login-AzureRmAccount** parancsmag használatával a **-hitelesítő adat** paraméter.
    - Ha működik a bérlő nevében CSP partner, adja meg az ügyfél a bérlők a tenantID vagy bérlői elsődleges tartománynév használatával. Például: **Login-AzureRmAccount-bérlői "fabrikam.com"**
2. Társítsa az előfizetést szeretné használni a fiók, mert egy fiók több előfizetéssel is rendelkezik:

    `Select-AzureRmSubscription -SubscriptionName $SubscriptionName`

3. Győződjön meg arról, hogy az előfizetés regisztrálva van-e az Azure-szolgáltatók használatához a Recovery Services és a hely helyreállítása után az alábbi parancsokkal:

    `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`

4. Ellenőrizze, hogy a parancs kimenetében, a **RegistrationState** értéke **regisztrált**, folytassa a 2. lépésre. Ha nem, akkor regisztrálnia kell a hiányzó szolgáltató az előfizetésében, ezek a parancsok futtatásával:

    `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SiteRecovery` `Register-AzureRmResourceProvider -ProviderNamespace Microsoft.RecoveryServices`

5. Győződjön meg arról, hogy a szolgáltatók regisztrálása sikeresen befejeződött, a következő parancsokkal:

    `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.RecoveryServices` `Get-AzureRmResourceProvider -ProviderNamespace  Microsoft.SiteRecovery`.

## <a name="step-2-set-up-the-vault"></a>2. lépés: Állítsa be a tárolóba

1. Hozzon létre egy Azure Resource Manager erőforráscsoportot, a tároló létrehozásához, vagy használjon egy meglévő erőforráscsoportot. Az alábbiak szerint hozzon létre egy új erőforráscsoportot. $ResourceGroupName változó szeretne létrehozni az erőforráscsoport nevét tartalmazza, és a $Geo változó tartalmazza az Azure-régió, amelyben az erőforráscsoport (például "Dél-Brazília") létrehozásához.

    `New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Geo` 

2. Az előfizetés, futtassa az erőforráscsoportok listáját beszerzése a **Get-AzureRmResourceGroup** parancsmag.
2. Hozzon létre egy új Azure Recovery Services-tároló az alábbiak szerint:

        $vault = New-AzureRmRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>

    A meglévő tárolók listája kérheti le a **Get-AzureRmRecoveryServicesVault** parancsmag.


## <a name="step-3-set-the-recovery-services-vault-context"></a>3. lépés: A Recovery Services-tároló környezet beállítása

A tároló környezet beállítása az alábbiak szerint:

`Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault`

## <a name="step-4-create-a-hyper-v-site"></a>4. lépés: Hyper-V hely létrehozása

1. Hozzon létre egy új Hyper-V hely az alábbiak szerint:

        $sitename = "MySite"                #Specify site friendly name
        New-AzureRmSiteRecoverySite -Name $sitename

2. Ez a parancsmag elindítja a Site Recovery feladatot, a webhely létrehozása, és a Site Recovery feladat objektum beállítása/beolvasása. Várjon, amíg a feladat befejeződését, és ellenőrizze, hogy a feladat sikeresen befejeződött.
3. Használja a **Get-AzureRmSiteRecoveryJob parancsmag**beolvasni a feladatobjektumot, majd ellenőrizze a feladat aktuális állapotát.
4. Létrehozni, és töltse le a hely egy regisztrációs kulcsot az alábbiak szerint:

    ```
    $SiteIdentifier = Get-AzureRmSiteRecoverySite -Name $sitename | Select -ExpandProperty SiteIdentifier
        Get-AzureRmRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename -Path $Path
    ```

5. Másolja a letöltött kulcs a Hyper-V gazdagépen. A Hyper-V gazdagép, a hely regisztrálja a kulccsal van szüksége.

## <a name="step-5-install-the-provider-and-agent"></a>5. lépés: A szolgáltató és az ügynök telepítése

1. A telepítő a szolgáltató legújabb verziójának letöltése [Microsoft](https://aka.ms/downloaddra).
2. A telepítő futtatásához theHyper-V gazdagépen.
3. A telepítés végén továbbra is a regisztrációs lépésében.
4. Amikor a rendszer kéri, adja meg a letöltött kulcsát, és elvégezheti a regisztrálást a Hyper-V gazdagép.
5. Győződjön meg arról, hogy a Hyper-V gazdagép regisztrálva van a helyhez az alábbiak szerint:

        $server =  Get-AzureRmSiteRecoveryServer -FriendlyName $server-friendlyname

## <a name="step-6-create-a-replication-policy"></a>6. lépés: A replikációs házirend létrehozása

Mielőtt elkezdené, vegye figyelembe, hogy a megadott tárfiók ugyanabban a régióban Azure és a tárolónak kell lennie, és rendelkeznie kell a georeplikáció engedélyezve van.

1. Egy replikációs házirend létrehozásához az alábbiak szerint:

        $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
        $PolicyName = “replicapolicy”
        $Recoverypoints = 6                    #specify the number of recovery points
        $storageaccountID = Get-AzureRmStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select -ExpandProperty Id

        $PolicyResult = New-AzureRmSiteRecoveryPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds  -RecoveryPoints $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID

2. Ellenőrizze a visszaadott feladatot annak érdekében, hogy a replikációs házirend létrehozása sikeres.

3. Lekéri a védelmi tároló, amely megfelel a helyhez, az alábbiak szerint:

        $protectionContainer = Get-AzureRmSiteRecoveryProtectionContainer
3. A védelmi tároló replikációs szabályzat társításához, az alábbiak szerint:

     $Policy = get-AzureRmSiteRecoveryPolicy - FriendlyName $PolicyName $associationJob = a Start-AzureRmSiteRecoveryPolicyAssociationJob-házirend $Policy - PrimaryProtectionContainer $protectionContainer

4. Várjon, amíg a társítás feladat sikeresen befejeződik.

## <a name="step-7-enable-vm-protection"></a>7. lépés: Virtuális gép védelmének engedélyezése

1. Lekéri a védelmi entitás, amely megfelel a virtuális gép védelmében, az alábbiak szerint:

        $VMFriendlyName = "Fabrikam-app"                    #Name of the VM
        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
2. A virtuális gép védelmét. Ha a védett virtuális gép nem csatlakoztatható egynél több lemez, adja meg az operációsrendszer-lemez használatával a *OSDiskName* paraméter.

        $Ostype = "Windows"                                 # "Windows" or "Linux"
        $DRjob = Set-AzureRmSiteRecoveryProtectionEntity -ProtectionEntity $protectionEntity -Policy $Policy -Protection Enable -RecoveryAzureStorageAccountId $storageaccountID  -OS $OStype -OSDiskName $protectionEntity.Disks[0].Name

3. Várjon, amíg a kezdeti replikálás után a védett állapotot elérni a virtuális gépeket. Ezzel időt is igénybe, például a replikálható adatmennyiséget, és a felsőbb rétegbeli rendelkezésre álló sávszélesség tényezőktől függően az Azure-bA. Ha egy védett állapotban van beállítva, a feladat állapotát és StateDescription frissítve lett, az alábbiak szerint: 

        PS C:\> $DRjob = Get-AzureRmSiteRecoveryJob -Job $DRjob

        PS C:\> $DRjob | Select-Object -ExpandProperty State
        Succeeded

        PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
        Completed
4. Frissítés helyreállítási tulajdonságait (például a Virtuálisgép-szerepkör méretéhez,), és az Azure-hálózatot, amelyhez csatolni a virtuális gép hálózati adapter a feladatátvételt követően.

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
1. Feladatátvételi teszt futtatása az alábbiak szerint:

        $nw = Get-AzureRmVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

        $protectionEntity = Get-AzureRmSiteRecoveryProtectionEntity -FriendlyName $VMFriendlyName -ProtectionContainer $protectionContainer

        $TFjob = Start-AzureRmSiteRecoveryTestFailoverJob -ProtectionEntity $protectionEntity -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
2. Győződjön meg arról, hogy a teszt virtuális gép létrehozása az Azure-ban. A tesztfeladat feladatátvételt fel van függesztve, az Azure-ban a teszteléshez használt virtuális gép létrehozása után.
3. Számolja fel, és végezze el a feladatátvételi tesztet, futtassa:

        $TFjob = Resume-AzureRmSiteRecoveryJob -Job $TFjob

## <a name="next-steps"></a>Következő lépések
[További](https://msdn.microsoft.com/library/azure/mt637930.aspx) Azure Site Recovery Azure Resource Manager PowerShell-parancsmagokkal kapcsolatos.
