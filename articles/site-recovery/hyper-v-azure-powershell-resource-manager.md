---
title: Hyper-V VM vész-helyreállítási azure site recovery és a PowerShell használatával
description: Automatizálhatja a Hyper-V virtuális gépek azure-ba történő vészutáni helyreállítását az Azure Site Recovery szolgáltatással a PowerShell és az Azure Resource Manager használatával.
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 01/10/2020
ms.author: sutalasi
ms.openlocfilehash: 6499c986bef965848303ee9833fd59f5e3f0889c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257991"
---
# <a name="set-up-disaster-recovery-to-azure-for-hyper-v-vms-using-powershell-and-azure-resource-manager"></a>Vészhelyreállítás beállítása az Azure-ba a PowerShell és az Azure Resource Manager használatával az Azure-ban a Virtuális-gépekkel

[Az Azure Site Recovery](site-recovery-overview.md) az Azure virtuális gépek, valamint a helyszíni virtuális gépek és fizikai kiszolgálók replikálásával, feladatátvételével és helyreállításával járul hozzá az üzletmenet-folytonossági és vész-helyreállítási (BCDR) stratégiához.

Ez a cikk azt ismerteti, hogy miként használhatja a Windows PowerShellt az Azure Resource Managerrel együtt a Hyper-V virtuális gépek Replikálásához az Azure-ba. A jelen cikkben használt példa bemutatja, hogyan replikálhatja a Hyper-V gazdagépen futó egyetlen virtuális gép az Azure-ba.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-powershell"></a>Azure PowerShell

Az Azure PowerShell parancsmagokkal kezelheti az Azure-t a Windows PowerShell használatával. Az Azure PowerShell for Azure Resource Manager segítségével megvédheti és helyreállíthatja a kiszolgálókat az Azure-ban.

A cikk használatához nem kell PowerShell-szakértőnek lennie, de meg kell értenie az alapvető fogalmakat, például a modulokat, a parancsmagokat és a munkameneteket. További információt a [PowerShell dokumentációjában](/powershell) és az [Azure PowerShell azure Resource Managerrel való használata című](../powershell-azure-resource-manager.md)témakörben talál.

> [!NOTE]
> A Felhőszolgáltató (CSP) programban részt vevő Microsoft-partnerek konfigurálhatják és kezelhetik az ügyfélkiszolgálók védelmét a megfelelő KP-előfizetésekhez (bérlői előfizetésekhez).

## <a name="before-you-start"></a>Előkészületek

Győződjön meg arról, hogy az alábbi előfeltételek a helyükön vannak:

- [Microsoft Azure-fiók.](https://azure.microsoft.com/) Kezdésként használhatja az [ingyenes próbaverziót](https://azure.microsoft.com/pricing/free-trial/) is. Ezenkívül az Azure [Site Recovery Manager díjszabásáról](https://azure.microsoft.com/pricing/details/site-recovery/)is olvashat.
- Azure PowerShell. A kiadásról és annak telepítéséről az [Azure PowerShell telepítése című](/powershell/azure/install-az-ps)témakörben talál további információt.

Ezenkívül a cikkben ismertetett konkrét példa a következő előfeltételekkel rendelkezik:

- Windows Server 2012 R2 vagy Microsoft Hyper-V Server 2012 R2 rendszert futtató Hyper-V állomás, amely egy vagy több virtuális gépet tartalmaz. A Hyper-V kiszolgálóknak közvetlenül vagy proxyn keresztül kell csatlakozniuk az internethez.
- A replikálni kívánt virtuális gépeknek meg kell felelniük [ezeknek az előfeltételeknek.](hyper-v-azure-support-matrix.md#replicated-vms)

## <a name="step-1-sign-in-to-your-azure-account"></a>1. lépés: Bejelentkezés Azure-fiókjába

1. Nyisson meg egy PowerShell-konzolt, és futtassa ezt a parancsot az Azure-fiókjába való bejelentkezéshez. A parancsmag egy weblapot hoz létre, amely a `Connect-AzAccount`fiók hitelesítő adatainak megadását kéri: .
   - A hitelesítő adatokat is megadhat paraméterként `Connect-AzAccount` a parancsmagban a **Hitelesítő adatok** paraméter használatával.
   - Ha ön egy bérlő nevében dolgozó csp-partner, adja meg az ügyfelet bérlőként a bérlőazonosító vagy a bérlő elsődleges tartománynevének használatával. Például:`Connect-AzAccount -Tenant "fabrikam.com"`
1. Társítsa a használni kívánt előfizetést a fiókhoz, mivel egy fióknak több előfizetése is lehet:

   ```azurepowershell
   Set-AzContext -Subscription $SubscriptionName
   ```

1. Ellenőrizze, hogy előfizetése regisztrálva van-e az Azure-szolgáltatók helyreállítási szolgáltatásokhoz és a Site Recovery használatához az alábbi parancsokkal:

   ```azurepowershell
   Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices
   ```

1. Ellenőrizze, hogy a parancskimenetben a **RegistrationState** **beállítása Regisztrált**, folytassa a 2. Ha nem, akkor regisztrálja a hiányzó szolgáltatót az előfizetésében a következő parancsok futtatásával:

   ```azurepowershell
   Register-AzResourceProvider -ProviderNamespace Microsoft.RecoveryServices
   ```

1. Ellenőrizze, hogy a szolgáltatók sikeresen regisztráltak-e a következő parancsokkal:

   ```azurepowershell
   Get-AzResourceProvider -ProviderNamespace  Microsoft.RecoveryServices
   ```

## <a name="step-2-set-up-the-vault"></a>2. lépés: A páncélterem beállítása

1. Hozzon létre egy Azure Resource Manager erőforráscsoportot, amelyben a tároló takar, vagy egy meglévő erőforráscsoport használata. Hozzon létre egy új erőforráscsoportot az alábbiak szerint. A `$ResourceGroupName` változó tartalmazza a létrehozni kívánt erőforráscsoport nevét, a $Geo változó pedig azt az Azure-régiót, amelyben az erőforráscsoportot létre kívánja hozni (például "Brazília déli része").

   ```azurepowershell
   New-AzResourceGroup -Name $ResourceGroupName -Location $Geo
   ```

1. Az előfizetéserőforrás-csoportok listájának beszerzéséhez `Get-AzResourceGroup` futtassa a parancsmast.
1. Hozzon létre egy új Azure Recovery Services-tárolót az alábbiak szerint:

   ```azurepowershell
   $vault = New-AzRecoveryServicesVault -Name <string> -ResourceGroupName <string> -Location <string>
   ```

A parancsmaggal lekérheti a `Get-AzRecoveryServicesVault` meglévő tárolók listáját.

## <a name="step-3-set-the-recovery-services-vault-context"></a>3. lépés: A Recovery Services-tároló környezetének beállítása

Állítsa be a tároló környezetét az alábbiak szerint:

```azurepowershell
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

## <a name="step-4-create-a-hyper-v-site"></a>4. lépés: Hyper-V webhely létrehozása

1. Hozzon létre egy új Hyper-V webhelyet az alábbiak szerint:

   ```azurepowershell
   $sitename = "MySite"                #Specify site friendly name
   New-AzRecoveryServicesAsrFabric -Type HyperVSite -Name $sitename
   ```

1. Ez a parancsmag elindítja a site recovery feladatot a hely létrehozásához, és egy Site Recovery feladatobjektumot ad vissza. Várja meg, amíg a feladat befejeződik, és ellenőrizze, hogy a feladat sikeresen befejeződött-e.
1. A `Get-AzRecoveryServicesAsrJob` parancsmag segítségével olvassa be a feladatobjektumot, és ellenőrizze a feladat aktuális állapotát.
1. Hozzon létre és töltsön le egy regisztrációs kulcsot a webhelyhez, az alábbiak szerint:

   ```azurepowershell
   $SiteIdentifier = Get-AzRecoveryServicesAsrFabric -Name $sitename | Select-Object -ExpandProperty SiteIdentifier
   $path = Get-AzRecoveryServicesVaultSettingsFile -Vault $vault -SiteIdentifier $SiteIdentifier -SiteFriendlyName $sitename
   ```

1. Másolja a letöltött kulcsot a Hyper-V állomásra. A kulcsra van szüksége a Hyper-V állomás regisztrálásához a webhelyen.

## <a name="step-5-install-the-provider-and-agent"></a>5. lépés: Telepítse a szolgáltatót és az ügynököt

1. Töltse le a telepítőt a Szolgáltató legújabb verziójához a [Microsofttól.](https://aka.ms/downloaddra)
1. Futtassa a telepítőt a Hyper-V gazdagépen.
1. A telepítés végén folytassa a regisztrációs lépést.
1. Amikor a rendszer kéri, adja meg a letöltött kulcsot, és a Hyper-V gazdagép teljes regisztrációját.
1. Ellenőrizze, hogy a Hyper-V állomás regisztrálva van-e a webhelyen az alábbiak szerint:

   ```azurepowershell
   $server = Get-AzRecoveryServicesAsrFabric -Name $siteName | Get-AzRecoveryServicesAsrServicesProvider -FriendlyName $server-friendlyname
   ```

Ha Hyper-V alapkiszolgálót futtat, töltse le a telepítőfájlt, és kövesse az alábbi lépéseket:

1. A parancs futtatásával bontsa ki a fájlokat az _AzureSiteRecoveryProvider.exe_ webhelyről egy helyi könyvtárba:

   ```console
   AzureSiteRecoveryProvider.exe /x:. /q
   ```

1. Futtassa az alábbi parancsot:

   ```console
   .\setupdr.exe /i
   ```

   Az eredmények et a _%ProgramData%\ASRLogs\DRASetupWizard.log fájl naplózza._

1. Regisztrálja a kiszolgálót a következő parancs futtatásával:

   ```console
   cd  C:\Program Files\Microsoft Azure Site Recovery Provider\DRConfigurator.exe" /r /Friendlyname "FriendlyName of the Server" /Credentials "path to where the credential file is saved"
   ```

## <a name="step-6-create-a-replication-policy"></a>6. lépés: Replikációs házirend létrehozása

Mielőtt elkezdené, a megadott tárfiók nak ugyanabban az Azure-régióban kell lennie, mint a tároló, és a georeplikáció engedélyezve kell lennie.

1. Hozzon létre replikációs házirendet az alábbiak szerint:

   ```azurepowershell
   $ReplicationFrequencyInSeconds = "300";        #options are 30,300,900
   $PolicyName = “replicapolicy”
   $Recoverypoints = 6                    #specify the number of recovery points
   $storageaccountID = Get-AzStorageAccount -Name "mystorea" -ResourceGroupName "MyRG" | Select-Object -ExpandProperty Id

   $PolicyResult = New-AzRecoveryServicesAsrPolicy -Name $PolicyName -ReplicationProvider “HyperVReplicaAzure” -ReplicationFrequencyInSeconds $ReplicationFrequencyInSeconds -NumberOfRecoveryPointsToRetain $Recoverypoints -ApplicationConsistentSnapshotFrequencyInHours 1 -RecoveryAzureStorageAccountId $storageaccountID
   ```

1. Ellenőrizze a visszaadott feladatot, és győződjön meg arról, hogy a replikációs házirend létrehozása sikeres.

1. A helynek megfelelő védelmi tároló lekérése az alábbiak szerint:

   ```azurepowershell
   $protectionContainer = Get-AzRecoveryServicesAsrProtectionContainer
   ```

1. Társítsa a védelmi tárolót a replikációs házirendhez az alábbiak szerint:

   ```azurepowershell
   $Policy = Get-AzRecoveryServicesAsrPolicy -FriendlyName $PolicyName
   $associationJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name $mappingName -Policy $Policy -PrimaryProtectionContainer $protectionContainer[0]
   ```

1. Várja meg, amíg a társítási feladat sikeresen befejeződik.

1. A védelmi tároló leképezésének beolvasása.

   ```azurepowershell
   $ProtectionContainerMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $protectionContainer
   ```

## <a name="step-7-enable-vm-protection"></a>7. lépés: Virtuálisgép-védelem engedélyezése

1. A védeni kívánt virtuális gépnek megfelelő védett elem lekérése az alábbiak szerint:

   ```azurepowershell
   $VMFriendlyName = "Fabrikam-app"          #Name of the VM
   $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
   ```

1. A virtuális gép védelme. Ha a védeni kívánt virtuális géphez egynél több lemez van csatlakoztatva, adja meg az operációs rendszer lemezét az **OSDiskName** paraméter használatával.

   ```azurepowershell
   $OSType = "Windows"          # "Windows" or "Linux"
   $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID
   ```

1. Várja meg, amíg a virtuális gépek a kezdeti replikáció után védett állapotot érnek el. Ez eltarthat egy ideig, olyan tényezőktől függően, mint például a replikálandó adatok mennyisége és az Azure számára rendelkezésre álló upstream sávszélesség. Ha védett állapot van érvényben, a feladat állapota és a StateDescription a következőképpen frissül:

   ```console
   PS C:\> $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob

   PS C:\> $DRjob | Select-Object -ExpandProperty State
   Succeeded

   PS C:\> $DRjob | Select-Object -ExpandProperty StateDescription
   Completed
   ```

1. Frissítse a helyreállítási tulajdonságokat (például a virtuális gép szerepkör méretét) és az Azure-hálózatot, amelyhez a feladatátvétel után csatolni szeretné a virtuális gép hálózati adapterét.

   ```console
   PS C:\> $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

   PS C:\> $VMFriendlyName = "Fabrikam-App"

   PS C:\> $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

   PS C:\> $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

   PS C:\> $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

   PS C:\> $UpdateJob | Select-Object -ExpandProperty state

   PS C:\> Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state

   Succeeded
   ```

> [!NOTE]
> Ha a CMK-kompatibilis felügyelt lemezekre szeretne replikálni az Azure-ban, tegye a következő lépéseket az Az PowerShell 3.3.0-s használatával:
>
> 1. Feladatátvétel engedélyezése a felügyelt lemezekre a virtuális gép tulajdonságainak frissítésével
> 1. A `Get-AzRecoveryServicesAsrReplicationProtectedItem` parancsmag segítségével olvassa be a védett elem minden egyes lemezének lemezazonosítóját
> 1. Hozzon létre egy `New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"` szótárobjektumot parancsmag használatával, amely tartalmazza a lemezazonosító lemeztitkosítási készlethez való hozzárendelését. Ezeket a lemeztitkosítási készleteket ön nek kell előre létrehoznia a célrégióban.
> 1. Frissítse a virtuális `Set-AzRecoveryServicesAsrReplicationProtectedItem` gép tulajdonságait a parancsmag használatával a **DiskIdToDiskEncryptionSetMap** paraméterszótár-objektumának átadásával.

## <a name="step-8-run-a-test-failover"></a>8. lépés: Tesztfeladat-átvétel futtatása

1. Futtasson egy teszt feladatátvételt az alábbiak szerint:

   ```azurepowershell
   $nw = Get-AzVirtualNetwork -Name "TestFailoverNw" -ResourceGroupName "MyRG" #Specify Azure vnet name and resource group

   $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

   $TFjob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $VM -Direction PrimaryToRecovery -AzureVMNetworkId $nw.Id
   ```

1. Ellenőrizze, hogy a teszt virtuális gép az Azure-ban jön létre. A teszt feladatátvételi feladat felfüggesztésre kerül a teszt virtuális gép létrehozása után az Azure-ban.
1. A teszt feladatátvétel ének megtisztításához és befejezéséhez futtassa a következőt:

   ```azurepowershell
   $TFjob = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $rpi -Comment "TFO done"
   ```

## <a name="next-steps"></a>További lépések

[További információ](/powershell/module/az.recoveryservices) az Azure Resource Manager PowerShell-parancsmagokkal rendelkező Azure Site Recovery szolgáltatásról.
