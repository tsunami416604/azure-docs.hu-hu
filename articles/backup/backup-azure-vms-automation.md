---
title: Az Azure-beli virtuális gépek biztonsági és helyreállítási biztonsági ellátása a PowerShell használatával
description: A cikk ismerteti, hogyan lehet biztonsági másolatot készíteni és visszaállítani az Azure-beli virtuális gépeket az Azure Backup segítségével a PowerShell használatával
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 1d1074eea3d530b17904e2f49fba7c0d24e84e59
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743298"
---
# <a name="back-up-and-restore-azure-vms-with-powershell"></a>Az Azure-beli virtuális gépek biztonsági és visszaállítása a PowerShell használatával

Ez a cikk bemutatja, hogyan készíthet biztonsági másolatot és állíthat vissza egy Azure-beli virtuális gép egy [Azure Backup](backup-overview.md) Recovery Services-tárolóban powershell-parancsmagok használatával.

Ebben a cikkben az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Hozzon létre egy Helyreállítási szolgáltatások tároló, és állítsa be a tároló környezetben.
> * Biztonsági mentési szabályzat meghatározása
> * A biztonsági mentési szabályzat alkalmazása több virtuális gép védelme érdekében
> * Indítsa el az igény szerinti biztonsági mentési feladatot a védett virtuális gépek előtt biztonsági másolatot készíteni (vagy védeni) egy virtuális gép, be kell fejeznie az [előfeltételeket, hogy előkészítse](backup-azure-arm-vms-prepare.md) a környezetet a virtuális gépek védelmére.

## <a name="before-you-start"></a>Előkészületek

* [További információ](backup-azure-recovery-services-vault-overview.md) a Recovery Services-tárolókról.
* [Tekintse át](backup-architecture.md#architecture-built-in-azure-vm-backup) az Azure virtuális gépek biztonsági mentésének architektúráját, ismerje meg a biztonsági mentési [folyamatot,](backup-azure-vms-introduction.md) és [tekintse át](backup-support-matrix-iaas.md) a támogatást, a korlátozásokat és az előfeltételeket.
* Tekintse át a PowerShell-objektumhierarchiát a Recovery Services számára.

## <a name="recovery-services-object-hierarchy"></a>Helyreállítási szolgáltatások objektumhierarchiája

Az objektumhierarchiát a következő ábra foglalja össze.

![Helyreállítási szolgáltatások objektumhierarchiája](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Tekintse át az **Az.RecoveryServices** [parancsmag referenciahivatkozást](https://docs.microsoft.com/powershell/module/Az.RecoveryServices/?view=azps-1.4.0) az Azure-kódtárban.

## <a name="set-up-and-register"></a>Beállítás és regisztrálás

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A kezdéshez:

1. [A PowerShell legújabb verziójának letöltése](https://docs.microsoft.com/powershell/azure/install-az-ps)

2. Keresse meg az elérhető Azure Backup PowerShell-parancsmagokat a következő parancs beírásával:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

    Az Azure Backup, az Azure Site Recovery és a Recovery Services-tároló aliasai és parancsmagjai jelennek meg. Az alábbi kép egy példa arra, hogy mit fog látni. Ez nem a parancsmagok teljes listája.

    ![helyreállítási szolgáltatások listája](./media/backup-azure-vms-automation/list-of-recoveryservices-ps.png)

3. Jelentkezzen be Azure-fiókjába a **Connect-AzAccount használatával.** Ez a parancsmag egy weblapot hoz létre, amely a fiók hitelesítő adataira vonatkozó kérdést teszi fel:

    * A **-Credential** paraméter használatával a **Connect-AzAccount** parancsmagban paraméterként is megadhatja a fiók hitelesítő adatait.
    * Ha egy bérlő nevében dolgozó csp partner, adja meg az ügyfelet bérlőként a bérlőazonosító vagy a bérlő elsődleges tartománynevének használatával. Például: **Connect-AzAccount -Bérlő "fabrikam.com"**

4. Társítsa a használni kívánt előfizetést a fiókhoz, mivel egy fióknak több előfizetése is lehet:

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. Ha első alkalommal használja az Azure Backup szolgáltatást, a **[Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)** parancsmaggal kell regisztrálnia az Azure Recovery Service szolgáltatót az előfizetéssel.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. A következő parancsokkal ellenőrizheti, hogy a szolgáltatók sikeresen regisztráltak-e:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

    A parancskimenetben a **RegistrationState-nek** Regisztrált ra kell **váltania.** Ha nem, csak futtassa újra a **[Register-AzResourceProvider](https://docs.microsoft.com/powershell/module/az.resources/register-azresourceprovider)** parancsmagát.

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A következő lépések segítségével hozzon létre egy Recovery Services-tároló. A Recovery Services-tároló eltér a biztonsági mentési tároló.

1. A Recovery Services-tároló egy Erőforrás-kezelő erőforrás, ezért egy erőforráscsoporton belül kell elhelyezni. Használhat egy meglévő erőforráscsoportot, vagy létrehozhat egy erőforráscsoportot a **[New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)** parancsmaggal. Erőforráscsoport létrehozásakor adja meg az erőforráscsoport nevét és helyét.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```

2. A Recovery Services-tároló létrehozásához használja a [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/new-azrecoveryservicesvault?view=azps-1.4.0) parancsmag. Ügyeljen arra, hogy adja meg ugyanazt a helyet a tároló, mint az erőforráscsoport használt.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Adja meg a használni kívánt tárolási redundancia típusát; [helyileg redundáns tárolás (LRS)](../storage/common/storage-redundancy-lrs.md) vagy [georedundáns tárolás (GRS)](../storage/common/storage-redundancy-grs.md)használható. A következő példa bemutatja a -BackupStorageRedundancy beállítás testvault van beállítva GeoRedundant.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperty  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

   > [!TIP]
   > Számos Azure Backup-parancsmaghoz szükséges bemenetként a helyreállítási tár objektum. Ebből az okból célszerű egy változóban tárolni a helyreállítási tár objektumot.
   >
   >

## <a name="view-the-vaults-in-a-subscription"></a>A tárolók megtekintése egy előfizetésben

Az előfizetés összes tárolójának megtekintéséhez használja a [Get-AzRecoveryServicesVault (Get-AzRecoveryServicesVault) szolgáltatást:](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0)

```powershell
Get-AzRecoveryServicesVault
```

A kimenet hasonló a következő példához, figyelje meg a kapcsolódó ResourceGroupName és hely van megadva.

```output
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

## <a name="back-up-azure-vms"></a>Azure-beli virtuális gépek biztonsági mentése

A helyreállítási szolgáltatások tárolója segítségével védheti a virtuális gépeket. A védelem alkalmazása előtt állítsa be a tároló környezetét (a tárolóban védett adatok típusát), és ellenőrizze a védelmi házirendet. A védelmi házirend a biztonsági mentési feladatok futtatásának ütemezése, és az egyes biztonsági mentési pillanatképek megőrzési ideje.

### <a name="set-vault-context"></a>Tároló környezetének beállítása

Mielőtt engedélyezne védelmet egy virtuális gépen, használja a [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0) a tároló környezetének beállításához. A tárolási környezet beállítását követően az minden további parancsmagra érvényes lesz. A következő példa a vault-környezetet állítja be a *testvault.*

```powershell
Get-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "Contoso-docs-rg" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>A tároló azonosítójának beolvasása

Azt tervezzük, hogy az Azure PowerShell irányelveinek megfelelően elavulttá tesszük a tárolókörnyezet-beállítást. Ehelyett tárolhatja vagy lehívhatja a tárolóazonosítót, és átadhatja a megfelelő parancsoknak. Így ha nem állította be a tároló környezetét, vagy meg szeretné adni a parancsot egy adott tárolószámára, adja át a tárolóazonosítót "-vaultID" azonosítóként az összes vonatkozó parancsnak, az alábbiak szerint:

```powershell
$targetVault = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault"
$targetVault.ID
```

Vagy

```powershell
$targetVaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

### <a name="modifying-storage-replication-settings"></a>A tároló replikációs beállításainak módosítása

A [Set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/Set-AzRecoveryServicesBackupProperty) paranccsal állítsa a tároló tároló replikációs konfigurációját LRS/GRS-re

```powershell
Set-AzRecoveryServicesBackupProperty -Vault $targetVault -BackupStorageRedundancy GeoRedundant/LocallyRedundant
```

> [!NOTE]
> A tárolási redundancia csak akkor módosítható, ha nincsenek biztonsági mentési elemek a tárolóban.

### <a name="create-a-protection-policy"></a>Védelmi házirend létrehozása

Helyreállítási tár létrehozásakor a tár alapértelmezett védelmi és megőrzési szabályzatokkal rendelkezik. Az alapértelmezett védelmi szabályzat naponta egyszer, adott időben aktivál egy biztonsági mentési feladatot. Az alapértelmezett megőrzési szabályzat 30 napig őrzi meg a napi helyreállítási pontokat. Az alapértelmezett szabályzat segítségével gyorsan védheti a virtuális gép, és a szabályzat későbbi szerkesztése különböző részletekkel.

A **[Get-AzRecoveryServicesBackupProtectionPolicy használatával](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy)** tekintse meg a tárolóban elérhető védelmi házirendeket. Ezzel a parancsmaggal egy adott szabályzatot kaphat, vagy megtekintheti a számítási feladatok típusához társított házirendeket. A következő példa lekéri a számítási feladatok típusa, AzureVM szabályzatok.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

A kimenet a következő példához hasonló:

```output
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
DefaultPolicy        AzureVM            AzureVM              4/14/2016 5:00:00 PM
```

> [!NOTE]
> A PowerShell BackupTime mezőjének időzónája UTC. Azonban ha a biztonsági mentési idő jelenik meg az Azure Portalon, az idő a helyi időzónához igazodik.
>
>

A biztonsági mentési védelmi házirend legalább egy adatmegőrzési házirendhez van társítva. Az adatmegőrzési szabály határozza meg, hogy mennyi ideig a helyreállítási pont megtartása előtt törlődik.

* A [Get-AzRecoveryServicesBackupRetentionPolicyObject objektummal](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject) tekintse meg az alapértelmezett adatmegőrzési házirendet.
* Hasonlóképpen használhatja [a Get-AzRecoveryServicesBackupSchedulePolicyObject objektumot](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject) az alapértelmezett ütemezési házirend beszerzéséhez.
* A [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy) parancsmag létrehoz egy PowerShell-objektumot, amely biztonsági mentési házirend adatait tartalmazza.
* Az ütemezési és adatmegőrzési házirend-objektumok a New-AzRecoveryServicesBackupProtectionPolicy parancsmag bemeneteként használatosak.

Alapértelmezés szerint a kezdési időpont az Ütemezési házirendobjektumban van definiálva. A következő példában módosíthatja a kezdési időpontot a kívánt kezdési időpontra. A kívánt kezdési időpontnak UTC-ben is meg kell lennie. Az alábbi példa feltételezi, hogy a kívánt kezdési időpont 01:00 UTC a napi biztonsági mentések.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM" 
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> Meg kell adnia a kezdési időt csak 30 perces többszörösökben. A fenti példában csak "01:00:00" vagy "02:30:00" lehet. A kezdési időpont nem lehet "01:15:00"

A következő példa tárolja az ütemezési szabályzatot és az adatmegőrzési házirendet változókban. A példa ezeket a változókat használja a paraméterek meghatározására a *NewPolicy*védelmi házirend létrehozásakor.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM" 
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -WorkloadType "AzureVM" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultId $targetVault.ID
```

A kimenet a következő példához hasonló:

```output
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewPolicy           AzureVM            AzureVM              4/24/2016 1:30:00 AM
```

### <a name="enable-protection"></a>Védelem engedélyezése

Miután definiálta a védelmi szabályzatot, továbbra is engedélyeznie kell egy elem házirendét. A védelem engedélyezéséhez használja az [Enable-AzRecoveryServicesBackupProtection szolgáltatást.](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) A védelem engedélyezéséhez két objektumra van szükség - az elemre és a házirendre. Miután a szabályzat társítva lett a tárolóval, a biztonsági mentési munkafolyamat a házirend-ütemezésben meghatározott időpontban aktiválódik.

> [!IMPORTANT]
> A PS használatával egyszerre több virtuális gép biztonsági mentésének engedélyezéséhez győződjön meg arról, hogy egyetlen szabályzathoz nem tartozik 100-nál több virtuális gép. Ajánlott [eljárás.](https://docs.microsoft.com/azure/backup/backup-azure-vm-backup-faq#is-there-a-limit-on-number-of-vms-that-can-beassociated-with-a-same-backup-policy) Jelenleg a PS-ügyfél nem kifejezetten blokkolja, ha több mint 100 virtuális gép van, de az ellenőrzés a tervek szerint a jövőben hozzáadódik.

A következő példák lehetővé teszik az elem, v2VM védelmét a newpolicy házirend használatával. A példák eltérnek attól függően, hogy a virtuális gép titkosítva van-e, és milyen típusú titkosítást.

A **nem titkosított Erőforrás-kezelő virtuális gépeken**a védelem engedélyezése :

```powershell
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

A védelem engedélyezéséhez a titkosított virtuális gépeken (BEK és KEK használatával titkosított), meg kell adnia az Azure Backup szolgáltatás engedélyt kulcsok és titkos kulcsok a key vaultból.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToKeys backup,get,list -PermissionsToSecrets get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

A védelem engedélyezéséhez **a titkosított virtuális gépeken (csak bek használatával titkosított)** engedélyt kell adnia az Azure Backup szolgáltatásnak a kulcstartóból származó titkos kulcsok olvasásához.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName "KeyVaultName" -ResourceGroupName "RGNameOfKeyVault" -PermissionsToSecrets backup,get,list -ServicePrincipalName 262044b1-e2ce-469f-a196-69ab7ada62d3
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Policy $pol -Name "V2VM" -ResourceGroupName "RGName1" -VaultId $targetVault.ID
```

> [!NOTE]
> Ha az Azure Government-felhőt használja, használja az ff281ffe-705c-4f53-9f37-a40e6f2c68f3 értéket a [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) parancsmag ServicePrincipalName paraméteréhez.
>

## <a name="monitoring-a-backup-job"></a>Biztonsági mentési feladat figyelése

Az Azure Portal használata nélkül figyelheti a hosszú ideig futó műveleteket, például a biztonsági mentési feladatokat. Egy folyamatban lévő feladat állapotának leminősítéséhez használja a [Get-AzRecoveryservicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob) parancsmag. Ez a parancsmag lekéri egy adott tároló biztonsági mentési feladatait, és a tároló a tároló környezetben van megadva. A következő példa egy folyamatban lévő feladat tömbként való állapotát kapja meg, és az állapotot a $joblist változóban tárolja.

```powershell
$joblist = Get-AzRecoveryservicesBackupJob –Status "InProgress" -VaultId $targetVault.ID
$joblist[0]
```

A kimenet a következő példához hasonló:

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM             Backup               InProgress            4/23/2016                5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

Ahelyett, hogy ezeket a feladatokat a befejezéshez - ami szükségtelen további kódot - használja a [Wait-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) parancsmag. Ez a parancsmag szünetelteti a végrehajtást, amíg a feladat be nem fejeződik, vagy el nem éri a megadott időtúllépési értéket.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $joblist[0] -Timeout 43200 -VaultId $targetVault.ID
```

## <a name="manage-azure-vm-backups"></a>Azure-beli virtuális gépek biztonsági mentésének kezelése

### <a name="modify-a-protection-policy"></a>Védelmi házirend módosítása

A védelmi házirend módosításához használja a [Set-AzRecoveryServicesBackupProtectionPolicy házirendet](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy) a SchedulePolicy vagy retentionpolicy objektumok módosításához.

#### <a name="modifying-scheduled-time"></a>Ütemezett idő módosítása

Védelmi házirend létrehozásakor alapértelmezés szerint kezdési időpontot kap. Az alábbi példák bemutatják, hogyan módosíthatja a védelmi házirendek kezdési idejét.

````powershell
$SchPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureVM"
$UtcTime = Get-Date -Date "2019-03-20 01:00:00Z" (This is the time that the customer wants to start the backup)
$UtcTime = $UtcTime.ToUniversalTime()
$SchPol.ScheduleRunTimes[0] = $UtcTime
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -SchedulePolicy $SchPol -VaultId $targetVault.ID
````

#### <a name="modifying-retention"></a>Megőrzés módosítása

A következő példa a helyreállítási pont megőrzési 365 napra módosítja.

```powershell
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureVM"
$retPol.DailySchedule.DurationCountInDays = 365
$pol = Get-AzRecoveryServicesBackupProtectionPolicy -Name "NewPolicy" -VaultId $targetVault.ID
Set-AzRecoveryServicesBackupProtectionPolicy -Policy $pol  -RetentionPolicy $RetPol -VaultId $targetVault.ID
```

#### <a name="configuring-instant-restore-snapshot-retention"></a>Azonnali visszaállítási pillanatkép-megőrzés konfigurálása

> [!NOTE]
> Az Az PS 1.6.0-s verziójától kezdve frissítheti az azonnali visszaállítás pillanatkép-megőrzési időszakát a házirendben a Powershell használatával

````powershell
$bkpPol = Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureVM" -VaultId $targetVault.ID
$bkpPol.SnapshotRetentionInDays=7
Set-AzRecoveryServicesBackupProtectionPolicy -policy $bkpPol -VaultId $targetVault.ID
````

Az alapértelmezett érték 2 lesz, a felhasználó beállíthatja az értéket 1 és legfeljebb 5-tel. Heti biztonsági mentési házirendek esetén az időszak 5-re van állítva, és nem módosítható.

#### <a name="creating-azure-backup-resource-group-during-snapshot-retention"></a>Azure Backup erőforráscsoport létrehozása pillanatkép-megőrzés közben

> [!NOTE]
> Az Azure PS 3.7.0-s verziójától kezdve létrehozhatja és szerkesztheti az azonnali pillanatképek tárolására létrehozott erőforráscsoportot.

Ha többet szeretne megtudni az erőforráscsoport-létrehozási szabályokról és más fontos részletekről, tekintse meg az [Azure Backup erőforráscsoport virtuális gépek dokumentációját.](https://docs.microsoft.com/azure/backup/backup-during-vm-creation#azure-backup-resource-group-for-virtual-machines)

```powershell
$bkpPol = Get-AzureRmRecoveryServicesBackupProtectionPolicy -name "DefaultPolicyForVMs"
$bkpPol.AzureBackupRGName="Contosto_"
$bkpPol.AzureBackupRGNameSuffix="ForVMs"
Set-AzureRmRecoveryServicesBackupProtectionPolicy -policy $bkpPol
```

### <a name="trigger-a-backup"></a>Biztonsági mentés aktiválása

A [Backup-AzRecoveryServicesBackupItem használatával](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) indítsa el a biztonsági mentési feladatot. Ha ez a kezdeti biztonsági mentés, akkor teljes biztonsági mentés. Az ezt követő biztonsági mentések növekményes másolatot vesznek igénybe. A következő példa egy virtuális gép biztonsági mentését 60 napig megőrzi.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$item = Get-AzRecoveryServicesBackupItem -Container $namedContainer -WorkloadType "AzureVM" -VaultId $targetVault.ID
$endDate = (Get-Date).AddDays(60).ToUniversalTime()
$job = Backup-AzRecoveryServicesBackupItem -Item $item -VaultId $targetVault.ID -ExpiryDateTimeUTC $endDate
```

A kimenet a következő példához hasonló:

```output
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   ----------
V2VM              Backup              InProgress          4/23/2016                  5:00:30 PM                cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

> [!NOTE]
> A PowerShell StartTime és EndTime mezőinek időzónája UTC. Ha azonban az idő megjelenik az Azure Portalon, az idő a helyi időzónához igazodik.
>
>

### <a name="change-policy-for-backup-items"></a>Biztonsági másolat elemeire vonatkozó házirend módosítása

A felhasználó módosíthatja a meglévő házirendet, vagy módosíthatja a biztonsági elem házirend1-ről Policy2-re történő házirendét. Ha egy biztonsági másolatban szereplő elem házirendjei között szeretne váltani, olvassa le a megfelelő házirendet, és készítsen biztonsági másolatot, és használja az [Enable-AzRecoveryServices](https://docs.microsoft.com/powershell/module/az.recoveryservices/Enable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) parancsot biztonsági másolattal a paraméterként.

````powershell
$TargetPol1 = Get-AzRecoveryServicesBackupProtectionPolicy -Name <PolicyName> -VaultId $targetVault.ID
$anotherBkpItem = Get-AzRecoveryServicesBackupItem -WorkloadType AzureVM -BackupManagementType AzureVM -Name "<BackupItemName>" -VaultId $targetVault.ID
Enable-AzRecoveryServicesBackupProtection -Item $anotherBkpItem -Policy $TargetPol1 -VaultId $targetVault.ID
````

A parancs megvárja, amíg a konfigurálási biztonsági mentés befejeződik, és a következő kimenetet adja vissza.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
TestVM           ConfigureBackup      Completed            3/18/2019 8:00:21 PM      3/18/2019 8:02:16 PM      654e8aa2-4096-402b-b5a9-e5e71a496c4e
```

### <a name="stop-protection"></a>A védelem kikapcsolása

#### <a name="retain-data"></a>Adatok megőrzése

Ha a felhasználó le kívánja állítani a védelmet, használhatja az [Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/Disable-AzRecoveryServicesBackupProtection?view=azps-1.5.0) PS parancsmagát. Ez leállítja az ütemezett biztonsági mentéseket, de az eddig biztonsági másolatban szereplő adatok örökre megmaradnak.

````powershell
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureVM -WorkloadType AzureVM -Name "<backup item name>" -VaultId $targetVault.ID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID
````

#### <a name="delete-backup-data"></a>Biztonsági mentési adatok törlése

Annak érdekében, hogy teljesen távolítsa el a tárolt biztonsági mentési adatokat a tárolóban, csak add "-RemoveRecoveryPoints" jelző / kapcsoló a ["disable" védelmi parancs](#retain-data).

````powershell
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $targetVault.ID -RemoveRecoveryPoints
````

## <a name="restore-an-azure-vm"></a>Azure-beli virtuális gép visszaállítása

Fontos különbség van a virtuális gép visszaállítása az Azure Portalhasználatával, és a virtuális gép visszaállítása a PowerShell használatával. A PowerShell, a visszaállítási művelet befejeződött, ha a lemezek és a konfigurációs adatok a helyreállítási pont jönnek létre. A visszaállítási művelet nem hozza létre a virtuális gépet. Virtuális gép lemezről történő létrehozásához olvassa el a Virtuális gép létrehozása visszaállított lemezekről című [szakaszt.](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) Ha nem szeretné visszaállítani a teljes virtuális gép, de vissza szeretné állítani, vagy visszaállítani néhány fájlt egy Azure virtuális gép biztonsági mentés, olvassa el a [fájl-helyreállítási szakaszban.](backup-azure-vms-automation.md#restore-files-from-an-azure-vm-backup)

> [!Tip]
> A visszaállítási művelet nem hozza létre a virtuális gépet.
>
>

A következő ábra az objektumhierarchiát mutatja a RecoveryServicesVault-tól a BackupRecoveryPointig.

![A Helyreállítási szolgáltatások objektumhierarchiája a BackupContainer segítségével](./media/backup-azure-vms-arm-automation/backuprecoverypoint-only.png)

A biztonsági mentési adatok visszaállításához azonosítsa a biztonsági másolatban szereplő elemet és a helyreállítási pontot, amely az időponthoz kötött adatokat tartalmazza. A [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) segítségével állítsa vissza az adatokat a tárolóból a fiókjába.

Az Azure virtuális gépek visszaállításának alapvető lépései a következők:

* Válassza ki a virtuális gépet.
* Válasszon helyreállítási pontot.
* Állítsa vissza a lemezeket.
* A virtuális gép létrehozása tárolt lemezekről.

### <a name="select-the-vm"></a>Válassza ki a virtuális gép

A powershell-objektum, amely azonosítja a megfelelő biztonsági mentési elemet, indítsa el a tárolóa tárolóban, és a munka az utat lefelé az objektum hierarchia. A virtuális gép reprezentatot jelölő tároló kiválasztásához használja a [Get-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) parancsmast és a [Get-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) parancsmagba hozzávezett csövet.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

### <a name="choose-a-recovery-point"></a>Helyreállítási pont kiválasztása

A [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) parancsmag segítségével sorolja fel a biztonsági másolat elemének összes helyreállítási pontját. Ezután válassza ki a helyreállítási pontot a visszaállításához. Ha nem biztos abban, hogy melyik helyreállítási pontot használja, célszerű kiválasztani a lista legújabb RecoveryPointType = AppConsistent pontját.

A következő parancsfájlban a **$rp**változó a kijelölt biztonsági másolat hoz az elmúlt hét nap helyreállítási pontjainak tömbje. A tömb az idő fordított sorrendjében van rendezve, a legutóbbi helyreállítási pont pedig a 0-s indexnél. A helyreállítási pont kiválasztásához használja a szabványos PowerShell-tömbindexelést. A példában $rp[0] kiválasztja a legújabb helyreállítási pontot.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
$rp[0]
```

A kimenet a következő példához hasonló:

```output
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="restore-the-disks"></a>A lemezek visszaállítása

A [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem) parancsmag segítségével állítsa vissza a biztonsági másolat elemének adatait és konfigurációját egy helyreállítási pontra. Miután azonosította a helyreállítási pontot, használja azt a **-RecoveryPoint** paraméter értékeként. A fenti mintában **$rp[0]** volt a használandó visszanyerési pont. A következő mintakódban **a $rp[0]** a lemez visszaállításához használandó helyreállítási pont.

A lemezek és a konfigurációs adatok visszaállítása:

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -VaultId $targetVault.ID
$restorejob
```

#### <a name="restore-managed-disks"></a>Felügyelt lemezek visszaállítása

> [!NOTE]
> Ha a támogatott virtuális gép felügyelt lemezekkel rendelkezik, és szeretné visszaállítani őket felügyelt lemezként, bevezettük az Azure PowerShell RM modul 6.7.0-s moduljának képességét. Től
>
>

Adjon meg egy további **TargetResourceGroupName paramétert** annak az RG-nek a megadásához, amelyre a felügyelt lemezeket vissza állítja.

> [!NOTE]
> Erősen ajánlott a **TargetResourceGroupName** paraméter használata a felügyelt lemezek visszaállításához, mivel jelentős teljesítményjavulást eredményez. Emellett az Azure Powershell Az 1.0-s modul1.0-s részétől ez a paraméter kötelező felügyelt lemezekkel rendelkező visszaállítás esetén
>
>

```powershell
$restorejob = Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -StorageAccountName "DestAccount" -StorageAccountResourceGroupName "DestRG" -TargetResourceGroupName "DestRGforManagedDisks" -VaultId $targetVault.ID
```

A **VMConfig.JSON** fájl visszaáll a tárfiókba, és a felügyelt lemezek visszaállnak a megadott RG célra.

A kimenet a következő példához hasonló:

```output
WorkloadName     Operation          Status               StartTime                 EndTime            JobID
------------     ---------          ------               ---------                 -------          ----------
V2VM              Restore           InProgress           4/23/2016 5:00:30 PM                        cf4b3ef5-2fac-4c8e-a215-d2eba4124f27
```

A [Wait-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) parancsmag segítségével várja meg a visszaállítási feladat befejezését.

```powershell
Wait-AzRecoveryServicesBackupJob -Job $restorejob -Timeout 43200
```

Miután a visszaállítási feladat befejeződött, használja a [Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) parancsmag a visszaállítási művelet részleteinek lekért. A JobDetails tulajdonság rendelkezik a virtuális gép újraépítéséhez szükséges információkkal.

```powershell
$restorejob = Get-AzRecoveryServicesBackupJob -Job $restorejob -VaultId $targetVault.ID
$details = Get-AzRecoveryServicesBackupJobDetails -Job $restorejob -VaultId $targetVault.ID
```

Miután visszaállította a lemezeket, lépjen a következő szakaszba a virtuális gép létrehozásához.

## <a name="replace-disks-in-azure-vm"></a>Lemezek cseréje az Azure VM-ben

A lemezek és a konfigurációs adatok cseréjéhez hajtsa végre az alábbi lépéseket:

* 1. lépés: [A lemezek visszaállítása](backup-azure-vms-automation.md#restore-the-disks)
* 2. lépés: [Adatlemez leválasztása a PowerShell használatával](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk#detach-a-data-disk-using-powershell)
* 3. lépés: [Adatlemez csatolása Windows virtuális géphez a PowerShell segítségével](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps)

## <a name="create-a-vm-from-restored-disks"></a>Virtuális gép létrehozása visszaállított lemezekről

A lemezek visszaállítása után az alábbi lépésekkel hozza létre és konfigurálhatja a virtuális gépet a lemezről.

> [!NOTE]
>
> 1. Az AzureAz 3.0.0-s vagy újabb modulja szükséges. <br>
> 2. Ahhoz, hogy titkosított virtuális gépeket hozzon létre a visszaállított lemezekről, az Azure-szerepkörnek engedéllyel kell rendelkeznie a **Microsoft.KeyVault/vaults/deploy/action**művelet végrehajtásához. Ha a szerepkör nem rendelkezik ezzel az engedéllyel, hozzon létre egy egyéni szerepkört ezzel a művelettel. További információ: [Egyéni szerepkörök az Azure RBAC-ban.](../role-based-access-control/custom-roles.md) <br>
> 3. A lemezek visszaállítása után most már lehívhat egy központi telepítési sablont, amely közvetlenül használhatja egy új virtuális gép létrehozásához. Nincs több különböző PS-parancsmag a felügyelt/nem felügyelt virtuális gépek létrehozásához, amelyek titkosítottak/titkosítatlanok.<br>
> <br>

### <a name="create-a-vm-using-the-deployment-template"></a>Virtuális gép létrehozása a központi telepítési sablon használatával

Az eredményül bejön a feladat részletei a sablon URI-ját adják meg, amely lekérdezhető és üzembe helyezhető.

```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $templateBlobURI = $properties["Template Blob Uri"]
```

A sablon nem érhető el közvetlenül, mivel az ügyfél tárfiók és az adott tároló alatt található. A sablon eléréséhez a teljes URL-címre van szükség (egy ideiglenes SAS-jogkivonattal együtt).

1. Először bontsa ki a sablon nevét a sablonBlobURI. A formátum az alábbiakban látható. A Powershell felosztási műveletével kinyerheti a végleges sablonnevet erről az URL-címről.

```http
https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
```

2. Ezután a teljes URL-t lehet generálni, amint [azt itt](https://docs.microsoft.com/azure/azure-resource-manager/templates/secure-template-with-sas-token?tabs=azure-powershell#provide-sas-token-during-deployment).

```powershell
Set-AzCurrentStorageAccount -Name $storageAccountName -ResourceGroupName <StorageAccount RG name>
$templateBlobFullURI = New-AzStorageBlobSASToken -Container $containerName -Blob <templateName> -Permission r -FullUri
```

3. Telepítse a sablont egy új virtuális gép létrehozásához az [itt](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)leírtak szerint.

```powershell
New-AzResourceGroupDeployment -Name ExampleDeployment ResourceGroupName ExampleResourceGroup -TemplateUri $templateBlobFullURI -storageAccountType Standard_GRS
```

### <a name="create-a-vm-using-the-config-file"></a>Virtuális gép létrehozása a konfigurációs fájl használatával

A következő szakasz felsorolja a virtuális gép "VMConfig" fájl használatával történő létrehozásához szükséges lépéseket.

> [!NOTE]
> Erősen ajánlott a fent részletezett telepítési sablon használata virtuális gép létrehozásához. Ez a szakasz (1-6. pont) hamarosan elavult.

1. A feladat részleteinek lekérdezése a visszaállított lemez tulajdonságairól.

   ```powershell
   $properties = $details.properties
   $storageAccountName = $properties["Target Storage Account Name"]
   $containerName = $properties["Config Blob Container Name"]
   $configBlobName = $properties["Config Blob Name"]
   ```

2. Állítsa be az Azure storage-környezetet, és állítsa vissza a JSON konfigurációs fájlt.

   ```powershell
   Set-AzCurrentStorageAccount -Name $storageaccountname -ResourceGroupName "testvault"
   $destination_path = "C:\vmconfig.json"
   Get-AzStorageBlobContent -Container $containerName -Blob $configBlobName -Destination $destination_path
   $obj = ((Get-Content -Path $destination_path -Raw -Encoding Unicode)).TrimEnd([char]0x00) | ConvertFrom-Json
   ```

3. A JSON konfigurációs fájl segítségével hozza létre a virtuális gép konfigurációját.

   ```powershell
   $vm = New-AzVMConfig -VMSize $obj.'properties.hardwareProfile'.vmSize -VMName "testrestore"
   ```

4. Csatlakoztassa az operációs rendszer lemezét és adatlemezeit. Ez a lépés példákat a különböző felügyelt és titkosított virtuálisgép-konfigurációk. Használja a virtuális gép konfigurációjának megfelelő példát.

* **Nem felügyelt és nem titkosított virtuális gépek** – A következő minta használata nem felügyelt, nem titkosított virtuális gépekhez.

```powershell
       Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
       $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
       foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
       {
        $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
       }
```

* **Nem felügyelt és titkosított virtuális gépek az Azure AD (BEK csak)** – A nem felügyelt, titkosított virtuális gépek az Azure AD -val (csak bek használatával titkosított), vissza kell állítania a titkos kulcsot a key vault lemezek csatolása előtt. További információt a [Titkosított virtuális gép visszaállítása az Azure Biztonsági másolat helyreállítási pontjáról](backup-azure-restore-key-secret.md)című témakörben talál. Az alábbi minta bemutatja, hogyan csatolhat operációs rendszert és adatlemezeket a titkosított virtuális gépekhez. Az operációs rendszer lemezének beállításakor mindenképpen említse meg a megfelelő operációsrendszer-típust.

```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $dekUrl = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows/Linux
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
      {
       $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
```

* **Nem felügyelt és titkosított virtuális gépek az Azure AD (BEK és KEK)** – A nem felügyelt, titkosított virtuális gépek az Azure AD (titkosított BEK és KEK), állítsa vissza a kulcsot és titkos kulcsot a key vault a lemezek csatlakoztatása előtt. További információ: [Titkosított virtuális gép visszaállítása az Azure biztonsági mentéshelyreállítási pontról](backup-azure-restore-key-secret.md)című témakörben talál. Az alábbi minta bemutatja, hogyan csatolhat operációs rendszert és adatlemezeket a titkosított virtuális gépekhez.

```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net:443/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $kekUrl = "https://ContosoKeyVault.vault.azure.net:443/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.storageProfile'.osDisk.vhd.uri -DiskEncryptionKeyUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -CreateOption "Attach" -Windows
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.storageProfile'.osDisk.osType
      foreach($dd in $obj.'properties.storageProfile'.dataDisks)
     {
     $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
     }
```

* **Nem felügyelt és titkosított virtuális gépek Azure AD nélkül (csak BEK)** – Nem felügyelt, titkosított virtuális gépek azure AD nélkül (csak BEK használatával titkosított), ha a forrás **keyVault/titkos nem érhető el** visszaállítani a titkos kulcsokat a kulcstárolóba a [nem titkosított virtuális gép visszaállítása az Azure Biztonsági mentés helyreállítási pontról.](backup-azure-restore-key-secret.md) Ezután hajtsa végre a következő parancsfájlokat a visszaállított operációsrendszer-blob titkosítási részleteinek beállításához (ez a lépés nem szükséges az adatblobhoz). A $dekurl a visszaállított keyVault-ból is bekéshető.

Az alábbi parancsfájlt csak akkor kell végrehajtani, ha a forrás keyVault/secret nem érhető el.

```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""}}]}"
      $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
      $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
```

Miután a **titkos kulcsok elérhetők,** és a titkosítási részletek is be vannak állítva az operációs rendszer blob, csatolja a lemezeket az alábbi parancsfájl használatával.

Ha a forrás keyVault/titkos kulcsok már elérhetők, majd a fenti parancsfájlt nem kell végrehajtani.

```powershell
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
```

* **Nem felügyelt és titkosított virtuális gépek Azure AD (BEK és KEK) nélkül** – Nem felügyelt, nem felügyelt, titkosított virtuális gépek azure AD nélkül (BEK & KEK használatával titkosított), ha a forrás **keyVault/key/secret nem érhetők el** visszaállítani a kulcsot és a titkos kulcsokat a kulcstárolóhoz a nem titkosított virtuális gép [visszaállítása azure-beli biztonsági mentés helyreállítási pontról.](backup-azure-restore-key-secret.md) Ezután hajtsa végre a következő parancsfájlokat a visszaállított operációsrendszer-blob titkosítási részleteinek beállításához (ez a lépés nem szükséges az adatblobhoz). A $dekurl és $kekurl a visszaállított keyVault-ból is lehívható.

Az alábbi parancsfájlt csak akkor kell végrehajtani, ha a forrás keyVault/key/secret nem érhető el.

```powershell
      $dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
      $kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
      $keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
      $encSetting = "{""encryptionEnabled"":true,""encryptionSettings"":[{""diskEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""secretUrl"":""$dekUrl""},""keyEncryptionKey"":{""sourceVault"":{""id"":""$keyVaultId""},""keyUrl"":""$kekUrl""}}]}"
      $osBlobName = $obj.'properties.StorageProfile'.osDisk.name + ".vhd"
      $osBlob = Get-AzStorageBlob -Container $containerName -Blob $osBlobName
      $osBlob.ICloudBlob.Metadata["DiskEncryptionSettings"] = $encSetting
      $osBlob.ICloudBlob.SetMetadata()
```

Miután a **kulcs/titkos kulcsok elérhetők,** és a titkosítási részletek be vannak állítva az operációs rendszer blob, csatolja a lemezeket az alábbi parancsfájl használatával.

Ha a forrás keyVault/key/secrets érhetők el, majd a fenti parancsfájlt nem kell végrehajtani.

```powershell
      Set-AzVMOSDisk -VM $vm -Name "osdisk" -VhdUri $obj.'properties.StorageProfile'.osDisk.vhd.Uri -CreateOption "Attach"
      $vm.StorageProfile.OsDisk.OsType = $obj.'properties.StorageProfile'.OsDisk.OsType
      foreach($dd in $obj.'properties.StorageProfile'.DataDisks)
      {
      $vm = Add-AzVMDataDisk -VM $vm -Name "datadisk1" -VhdUri $dd.vhd.Uri -DiskSizeInGB 127 -Lun $dd.Lun -CreateOption "Attach"
      }
```

* **Felügyelt és nem titkosított virtuális gépek** – felügyelt, nem titkosított virtuális gépek esetén csatolja a visszaállított felügyelt lemezeket. Részletes információt az [Adatlemez csatolása Windows virtuális géphez a PowerShell használatával című témakörben talál.](../virtual-machines/windows/attach-disk-ps.md)

* **Felügyelt és titkosított virtuális gépek az Azure AD (BEK csak)** - Felügyelt titkosított virtuális gépek az Azure AD (csak BEK használatával titkosított), csatolja a visszaállított felügyelt lemezek. Részletes információt az [Adatlemez csatolása Windows virtuális géphez a PowerShell használatával című témakörben talál.](../virtual-machines/windows/attach-disk-ps.md)

* **Felügyelt és titkosított virtuális gépek az Azure AD (BEK és KEK)** – Felügyelt titkosított virtuális gépek az Azure AD (titkosított BEK és KEK), csatolja a visszaállított felügyelt lemezeket. Részletes információt az [Adatlemez csatolása Windows virtuális géphez a PowerShell használatával című témakörben talál.](../virtual-machines/windows/attach-disk-ps.md)

* **Felügyelt és titkosított virtuális gépek nélkül Azure AD (BEK csak)** -Felügyelt, titkosított virtuális gépek nélkül Azure AD (csak BEK használatával titkosított), ha a forrás **keyVault/titkos nem érhetők el** visszaállítani a titkos kulcsokat a kulcstárolóeljárás segítségével [visszaállítása nem titkosított virtuális gép egy Azure Backup helyreállítási pont.](backup-azure-restore-key-secret.md) Ezután hajtsa végre a következő parancsfájlokat a titkosítási részletek beállításához a visszaállított operációsrendszer-lemezen (ez a lépés nem szükséges az adatlemezhez). A $dekurl a visszaállított keyVault-ból is bekéshető.

Az alábbi parancsfájlt csak akkor kell végrehajtani, ha a forrás keyVault/secret nem érhető el.  

```powershell
$dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
$keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
$diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
$encryptionSettingsElement = New-Object Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement
$encryptionSettingsElement.DiskEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndSecretReference
$encryptionSettingsElement.DiskEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
$encryptionSettingsElement.DiskEncryptionKey.SourceVault.Id = $keyVaultId
$encryptionSettingsElement.DiskEncryptionKey.SecretUrl = $dekUrl
$diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings = New-Object System.Collections.Generic.List[Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement]
$diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings.Add($encryptionSettingsElement)
$diskupdateconfig.EncryptionSettingsCollection.EncryptionSettingsVersion = "1.1"
Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
```

Miután a titkos kulcsok elérhetők, és a titkosítási részletek be vannak állítva az operációs rendszer lemezén, a visszaállított felügyelt lemezek csatolásához olvassa el az [Adatlemez csatolása Windows virtuális géphez a PowerShell használatával című témakört.](../virtual-machines/windows/attach-disk-ps.md)

* **Felügyelt és titkosított virtuális gépek Nélkül Azure AD (BEK és KEK)** - Felügyelt, titkosított virtuális gépek nélkül Azure AD (titkosított BEK & KEK), ha a forrás **keyVault/key/secret nem állnak rendelkezésre** visszaállítani a kulcsot és titkos kulcsokat a kulcs-és titkos kulcsok at key vault eljárás segítségével [visszaállítása nem titkosított virtuális gép egy Azure Backup helyreállítási pont.](backup-azure-restore-key-secret.md) Ezután hajtsa végre a következő parancsfájlokat a titkosítási részletek beállításához a visszaállított operációsrendszer-lemezen (ez a lépés nem szükséges az adatlemezek esetében). A $dekurl és $kekurl a visszaállított keyVault-ból is lehívható.

Az alábbi parancsfájlt csak akkor kell végrehajtani, ha a forrás keyVault/key/secret nem érhető el.

```powershell
$dekUrl = "https://ContosoKeyVault.vault.azure.net/secrets/ContosoSecret007/xx000000xx0849999f3xx30000003163"
$kekUrl = "https://ContosoKeyVault.vault.azure.net/keys/ContosoKey007/x9xxx00000x0000x9b9949999xx0x006"
$keyVaultId = "/subscriptions/abcdedf007-4xyz-1a2b-0000-12a2b345675c/resourceGroups/ContosoRG108/providers/Microsoft.KeyVault/vaults/ContosoKeyVault"
$diskupdateconfig = New-AzDiskUpdateConfig -EncryptionSettingsEnabled $true
$encryptionSettingsElement = New-Object Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement
$encryptionSettingsElement.DiskEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndSecretReference
$encryptionSettingsElement.DiskEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
$encryptionSettingsElement.DiskEncryptionKey.SourceVault.Id = $keyVaultId
$encryptionSettingsElement.DiskEncryptionKey.SecretUrl = $dekUrl
$encryptionSettingsElement.KeyEncryptionKey = New-Object Microsoft.Azure.Management.Compute.Models.KeyVaultAndKeyReference
$encryptionSettingsElement.KeyEncryptionKey.SourceVault = New-Object Microsoft.Azure.Management.Compute.Models.SourceVault
$encryptionSettingsElement.KeyEncryptionKey.SourceVault.Id = $keyVaultId
$encryptionSettingsElement.KeyEncryptionKey.KeyUrl = $kekUrl
$diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings = New-Object System.Collections.Generic.List[Microsoft.Azure.Management.Compute.Models.EncryptionSettingsElement]
$diskupdateconfig.EncryptionSettingsCollection.EncryptionSettings.Add($encryptionSettingsElement)
$diskupdateconfig.EncryptionSettingsCollection.EncryptionSettingsVersion = "1.1"
Update-AzDisk -ResourceGroupName "testvault" -DiskName $obj.'properties.StorageProfile'.osDisk.name -DiskUpdate $diskupdateconfig
```

Miután a kulcs/titkos kulcsok elérhetők, és a titkosítási részletek be vannak állítva az operációs rendszer lemezén, a visszaállított felügyelt lemezek csatlakoztatásához olvassa el az [Adatlemez csatolása Windows virtuális géphez a PowerShell használatával című témakört.](../virtual-machines/windows/attach-disk-ps.md)

5. Állítsa be a Hálózati beállításokat.

    ```powershell
    $nicName="p1234"
    $pip = New-AzPublicIpAddress -Name $nicName -ResourceGroupName "test" -Location "WestUS" -AllocationMethod Dynamic
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName "test" -Location "WestUS" -Name "testvNET" -AddressPrefix 10.0.0.0/16
    $virtualNetwork | Set-AzVirtualNetwork
    $vnet = Get-AzVirtualNetwork -Name "testvNET" -ResourceGroupName "test"
    $subnetindex=0
    $nic = New-AzNetworkInterface -Name $nicName -ResourceGroupName "test" -Location "WestUS" -SubnetId $vnet.Subnets[$subnetindex].Id -PublicIpAddressId $pip.Id
    $vm=Add-AzVMNetworkInterface -VM $vm -Id $nic.Id
    ```

6. Hozza létre a virtuális gépet.

    ```powershell  
    New-AzVM -ResourceGroupName "test" -Location "WestUS" -VM $vm
    ```

7. Nyomja le az ADE kiterjesztést.
   Ha az ADE-bővítmények nem kerülnek leküldéses, akkor az adatlemezek titkosítatlanként lesznek megjelölve, ezért az alábbi lépések végrehajtása kötelező:

   * **Virtuális gép és Az Azure AD** – A következő paranccsal manuálisan engedélyezheti a titkosítást az adatlemezekhez  

     **Csak BEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

     **BEK és KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId  -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -VolumeType Data
      ```

   * **Azure AD nélküli virtuális gép esetén** – A következő paranccsal manuálisan engedélyezheti a titkosítást az adatlemezekhez.

     Ha a parancs végrehajtása során kéri az AADClientID, majd frissítenie kell az Azure PowerShell.

     **Csak BEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

      **BEK és KEK**

      ```powershell  
      Set-AzVMDiskEncryptionExtension -ResourceGroupName $RG -VMName $vm.Name -DiskEncryptionKeyVaultUrl $dekUrl -DiskEncryptionKeyVaultId $keyVaultId -KeyEncryptionKeyUrl $kekUrl -KeyEncryptionKeyVaultId $keyVaultId -SkipVmBackup -VolumeType "All"
      ```

> [!NOTE]
> Győződjön meg arról, hogy manuálisan törli a titkosított virtuálisgép-visszaállítási lemezfolyamat részeként létrehozott JASON-fájlokat.

## <a name="restore-files-from-an-azure-vm-backup"></a>Fájlok visszaállítása az Azure virtuális gép biztonsági másolatából

A lemezek visszaállítása mellett az egyes fájlokat is visszaállíthatja egy Azure virtuális gép biztonsági mentéséből. A visszaállítási fájlok funkció hozzáférést biztosít a helyreállítási pont összes fájljához. Kezelje a fájlokat a Fájlkezelőn keresztül, a normál fájlokhoz.

Az Azure virtuális gép biztonsági mentéséből származó fájlok visszaállításának alapvető lépései a következők:

* Válassza ki a virtuális gép
* Helyreállítási pont kiválasztása
* A helyreállítási pont lemezeinek csatlakoztatása
* A szükséges fájlok másolása
* A lemez leválasztása

### <a name="select-the-vm"></a>Válassza ki a virtuális gép

A powershell-objektum, amely azonosítja a megfelelő biztonsági mentési elemet, indítsa el a tárolóa tárolóban, és a munka az utat lefelé az objektum hierarchia. A virtuális gép reprezentatot jelölő tároló kiválasztásához használja a [Get-AzRecoveryServicesBackupContainer](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupcontainer) parancsmast és a [Get-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupitem) parancsmagba hozzávezett csövet.

```powershell
$namedContainer = Get-AzRecoveryServicesBackupContainer  -ContainerType "AzureVM" -Status "Registered" -FriendlyName "V2VM" -VaultId $targetVault.ID
$backupitem = Get-AzRecoveryServicesBackupItem -Container $namedContainer  -WorkloadType "AzureVM" -VaultId $targetVault.ID
```

### <a name="choose-a-recovery-point"></a>Helyreállítási pont kiválasztása

A [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint) parancsmag segítségével sorolja fel a biztonsági másolat elemének összes helyreállítási pontját. Ezután válassza ki a helyreállítási pontot a visszaállításához. Ha nem biztos abban, hogy melyik helyreállítási pontot használja, célszerű kiválasztani a lista legújabb RecoveryPointType = AppConsistent pontját.

A következő parancsfájlban a **$rp**változó a kijelölt biztonsági másolat hoz az elmúlt hét nap helyreállítási pontjainak tömbje. A tömb az idő fordított sorrendjében van rendezve, a legutóbbi helyreállítási pont pedig a 0-s indexnél. A helyreállítási pont kiválasztásához használja a szabványos PowerShell-tömbindexelést. A példában $rp[0] kiválasztja a legújabb helyreállítási pontot.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $backupitem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime() -VaultId $targetVault.ID
$rp[0]
```

A kimenet a következő példához hasonló:

```output
RecoveryPointAdditionalInfo :
SourceVMStorageType         : NormalStorage
Name                        : 15260861925810
ItemName                    : VM;iaasvmcontainer;RGName1;V2VM
RecoveryPointId             : /subscriptions/XX/resourceGroups/ RGName1/providers/Microsoft.RecoveryServices/vaults/testvault/backupFabrics/Azure/protectionContainers/IaasVMContainer;iaasvmcontainer;RGName1;V2VM/protectedItems/VM;iaasvmcontainer; RGName1;V2VM/recoveryPoints/15260861925810
RecoveryPointType           : AppConsistent
RecoveryPointTime           : 4/23/2016 5:02:04 PM
WorkloadType                : AzureVM
ContainerName               : IaasVMContainer;iaasvmcontainer; RGName1;V2VM
ContainerType               : AzureVM
BackupManagementType        : AzureVM
```

### <a name="mount-the-disks-of-recovery-point"></a>A helyreállítási pont lemezeinek csatlakoztatása

A [Get-AzRecoveryServicesBackupRP.](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprpmountscript)

> [!NOTE]
> A lemezek iSCSI-hez csatlakoztatott lemezekként vannak csatlakoztatva ahhoz a számítógéphez, amelyen a parancsfájl fut. A felszerelés azonnal megtörténik, és nem kell fizetnie semmilyen díjat.
>
>

```powershell
Get-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0] -VaultId $targetVault.ID
```

A kimenet a következő példához hasonló:

```output
OsType  Password        Filename
------  --------        --------
Windows e3632984e51f496 V2VM_wus2_8287309959960546283_451516692429_cbd6061f7fc543c489f1974d33659fed07a6e0c2e08740.exe
```

Futtassa a parancsfájlt azon a számítógépen, ahol vissza szeretné állítani a fájlokat. A parancsfájl végrehajtásához meg kell adnia a megadott jelszót. A lemezek csatlakoztatása után a Windows Fájlkezelővel tallózzon az új köteteken és fájlokban. További információt a Biztonsági másolat című, Fájlok helyreállítása az [Azure virtuálisgép-biztonsági mentésből című cikkében](backup-azure-restore-files-from-vm.md)talál.

### <a name="unmount-the-disks"></a>A lemezek leválasztása

A szükséges fájlok másolása után az [Disable-AzRecoveryServicesBackupRP.Script](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackuprpmountscript) segítségével letilthatja a lemezeket. Ügyeljen arra, hogy leválasztani a lemezeket, így a hozzáférést a fájlokat a helyreállítási pont törlődik.

```powershell
Disable-AzRecoveryServicesBackupRPMountScript -RecoveryPoint $rp[0] -VaultId $targetVault.ID
```

## <a name="next-steps"></a>További lépések

Ha a PowerShell használatával szeretné kezelni az Azure-erőforrásokat, olvassa el a PowerShell Windows [Server-alapú biztonsági mentés telepítése és kezelése című cikkét.](backup-client-automation.md) Ha a DPM biztonsági másolatait kezeli, olvassa el a [DPM biztonsági mentésének telepítése és kezelése](backup-dpm-automation.md)című cikket.
