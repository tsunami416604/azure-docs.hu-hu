---
title: Azure Files biztonsági mentése a PowerShell-lel
description: Ebből a cikkből megtudhatja, hogyan készíthet biztonsági mentést Azure Files a Azure Backup szolgáltatás és a PowerShell használatával.
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: f85451e0da6458de34aea936836b46781f4c4a21
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79273539"
---
# <a name="back-up-azure-files-with-powershell"></a>Azure Files biztonsági mentése a PowerShell-lel

Ez a cikk azt ismerteti, hogyan használható a Azure PowerShell egy Azure Files-fájlmegosztás biztonsági mentésére egy [Azure Backup](backup-overview.md) Recovery Services-tároló használatával.

Ez a cikk a következőket ismerteti:

> [!div class="checklist"]
>
> * A PowerShell beállítása és az Azure Recovery Services-szolgáltató regisztrálása.
> * Recovery Services-tároló létrehozása.
> * Konfigurálja az Azure-fájlmegosztás biztonsági mentését.
> * Futtasson egy biztonsági mentési feladatot.

## <a name="before-you-start"></a>Előkészületek

* [További](backup-azure-recovery-services-vault-overview.md) információ a Recovery Services-tárolókkal kapcsolatban.
* További információ az [Azure-fájlmegosztás biztonsági mentésének](backup-afs.md)előzetes funkcióival kapcsolatban.
* Tekintse át Recovery Services PowerShell-objektumának hierarchiáját.

## <a name="recovery-services-object-hierarchy"></a>Recovery Services objektum-hierarchia

Az objektum-hierarchiát a következő ábra összegzi.

![Recovery Services objektum-hierarchia](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Tekintse át az az **. recoveryservices szolgáltatónál** [parancsmag](/powershell/module/az.recoveryservices) -referenciát az Azure Library-ben.

## <a name="set-up-and-install"></a>Beállítás és telepítés

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A PowerShell beállítása a következőképpen történik:

1. [Töltse le az az PowerShell legújabb verzióját](/powershell/azure/install-az-ps). A minimálisan szükséges verzió a 1.0.0.

> [!WARNING]
> Az előzetes verzióhoz szükséges PS minimális verziója az "az 1.0.0" volt. A GA közelgő változásai miatt a minimálisan szükséges PS-verzió az "az. Recoveryservices szolgáltatónál 2.6.0" lesz. Nagyon fontos, hogy az összes meglévő PS-verziót frissítse erre a verzióra. Ellenkező esetben a meglévő parancsfájlok a GA után törnek le. Telepítse a minimális verziót a következő PS-parancsokkal

```powershell
Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
```

2. Keresse meg a Azure Backup PowerShell-parancsmagokat a következő paranccsal:

    ```powershell
    Get-Command *azrecoveryservices*
    ```

3. Tekintse át a Azure Backup, Azure Site Recovery és a Recovery Services tároló aliasait és parancsmagait. Íme egy példa arra, hogy mit láthat. Ez nem a parancsmagok teljes listája.

    ![Recovery Services-parancsmagok listája](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

4. Jelentkezzen be az Azure-fiókjába a **AzAccount**használatával.
5. A megjelenő weblapon meg kell adnia a fiók hitelesítő adatait.

    * Másik lehetőségként megadhatja a fiók hitelesítő adatait a kapcsolat- **AzAccount** parancsmag és a **-hitelesítő adatok**paraméterei között.
    * Ha Ön egy bérlő nevében dolgozó CSP-partner, adja meg az ügyfelet bérlőként a tenantID vagy a bérlő elsődleges tartománynevének használatával. Ilyen például a **AzAccount-bérlő** fabrikam.com.

6. Társítsa a fiókhoz használni kívánt előfizetést, mert egy fiók több előfizetéssel is rendelkezhet.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

7. Ha első alkalommal használja a Azure Backup, a **Register-AzResourceProvider** parancsmag használatával regisztrálja az Azure Recovery Services-szolgáltatót az előfizetésében.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

8. Ellenőrizze, hogy a szolgáltatók regisztrálása sikeres volt-e:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

9. A parancs kimenetében ellenőrizze, hogy a **RegistrationState** módosításai **regisztrálva**vannak-e. Ha nem, futtassa újra a **Register-AzResourceProvider** parancsmagot.

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A Recovery Services-tároló Resource Manager-erőforrás, ezért egy erőforráscsoporthoz kell helyeznie. Használhat meglévő erőforráscsoportot is, vagy létrehozhat egy erőforráscsoportot a **New-AzResourceGroup** parancsmaggal. Ha létrehoz egy erőforráscsoportot, adja meg az erőforráscsoport nevét és helyét.

Recovery Services-tároló létrehozásához kövesse az alábbi lépéseket.

1. Egy tároló egy erőforráscsoporthoz kerül. Ha nem rendelkezik meglévő erőforráscsoporthoz, hozzon létre egy újat a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). Ebben a példában egy új erőforráscsoportot hozunk létre az USA nyugati régiójában.

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```

2. A [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) parancsmag használatával hozza létre a tárolót. Azonos helyet kell megadnia a tárolóhoz az erőforráscsoport esetében.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```

3. Adja meg a tároló tárolásához használni kívánt redundancia típusát.

   * [Helyileg redundáns tárolást](../storage/common/storage-redundancy-lrs.md) vagy [földrajzilag redundáns tárolást](../storage/common/storage-redundancy-grs.md)használhat.
   * A következő példa beállítja a **-BackupStorageRedundancy** beállítást a[set-AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) cmd számára a **testvault** beállításnál a **GeoRedundant**értékre.

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>Az előfizetésben található tárolók megtekintése

Az előfizetés összes tárolójának megtekintéséhez használja a [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

A kimenet az alábbihoz hasonló. Vegye figyelembe, hogy a társított erőforráscsoport és hely van megadva.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>A tár környezetének beállítása

Tárolja a tároló objektumot egy változóban, és állítsa be a tár környezetét.

* Számos Azure Backup parancsmagnak bemenetként kell megkövetelni a Recovery Services-tároló objektumát, így kényelmes a tároló objektum tárolása egy változóban.
* A tárolási környezet a tár által védett adatok típusa. Állítsa be a [set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). A környezet beállítása után az az összes további parancsmagra vonatkozik.

Az alábbi példa a **testvault**tároló környezetét állítja be.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>A tár AZONOSÍTÓjának beolvasása

A tár környezeti beállítását a Azure PowerShell irányelvek alapján tervezzük. Ehelyett tárolhatja vagy beolvashatja a tár AZONOSÍTÓját, és átadhatja azokat a megfelelő parancsoknak. Tehát ha még nem állította be a tár környezetét, vagy egy adott tárolóhoz szeretne futtatni egy parancsot, adja át a tároló AZONOSÍTÓját "-vaultID" értékként az összes vonatkozó parancsra az alábbiak szerint:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol -VaultID $vaultID
```

## <a name="configure-a-backup-policy"></a>Biztonsági mentési szabályzat konfigurálása

A biztonsági mentési szabályzat meghatározza a biztonsági mentések ütemezését, valamint azt, hogy mennyi ideig kell megőrizni a biztonsági mentési helyreállítási pontokat:

* A biztonsági mentési szabályzat legalább egy adatmegőrzési szabályzattal van társítva. Egy adatmegőrzési házirend határozza meg, hogy mennyi ideig tart a helyreállítási pont a törlése előtt.
* Tekintse meg az alapértelmezett biztonsági mentési szabályzatot a [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0)használatával.
* Tekintse meg az alapértelmezett biztonsági mentési szabályzatot a [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0)használatával.
* Új biztonsági mentési szabályzat létrehozásához használja a [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) parancsmagot. Megadhatja az ütemterv és adatmegőrzési szabályzat objektumait.

Alapértelmezés szerint a rendszer a kezdési időt határozza meg az ütemezett házirend objektumban. A következő példa használatával módosíthatja a kezdési időt a kívánt kezdési időpontra. A kívánt kezdési időpontnak UTC-ként is kell lennie. Az alábbi példa feltételezi, hogy a várt kezdési idő a napi biztonsági másolatok 01:00-as UTC-értéke.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$UtcTime = Get-Date -Date "2019-03-20 01:30:00Z"
$UtcTime = $UtcTime.ToUniversalTime()
$schpol.ScheduleRunTimes[0] = $UtcTime
```

> [!IMPORTANT]
> A kezdési időt csak 30 percenként kell megadnia. A fenti példában csak "01:00:00" vagy "02:30:00" lehet. A kezdési időpont nem lehet "01:15:00"

A következő példa az ütemezett házirendet és a változókban tárolt adatmegőrzési szabályzatot tárolja. Ezután ezeket a változókat paraméterekként használja egy új szabályzathoz (**NewAFSPolicy**). A **NewAFSPolicy** napi biztonsági mentést készít, és 30 napig őrzi meg.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

A kimenet az alábbihoz hasonló.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2019 1:30:00 AM
```

## <a name="enable-backup"></a>Biztonsági mentés engedélyezése

A biztonsági mentési szabályzat meghatározása után engedélyezheti az Azure-fájlmegosztás védelmét a szabályzat használatával.

### <a name="retrieve-a-backup-policy"></a>Biztonsági mentési szabályzat beolvasása

A megfelelő házirend-objektum beolvasása a [Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0). Ezzel a parancsmaggal kérhet le egy adott szabályzatot, vagy megtekintheti a munkaterhelés-típushoz társított házirendeket.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Házirend beolvasása egy munkaterhelés-típushoz

Az alábbi példa lekéri a **AzureFiles**számítási feladatok típusára vonatkozó házirendeket.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

A kimenet az alábbihoz hasonló.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> A PowerShell **BackupTime** mezőjének időzónája az egyezményes VILÁGIDŐ (UTC). Ha a biztonsági mentés ideje megjelenik a Azure Portalban, az idő a helyi időzónára van igazítva.

### <a name="retrieve-a-specific-policy"></a>Adott szabályzat beolvasása

A következő szabályzat lekéri a **dailyafs**nevű biztonsági mentési szabályzatot.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>Biztonsági mentés engedélyezése és házirend alkalmazása

Engedélyezze a védelmet az [enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Miután a házirend társítva van a tárolóhoz, a biztonsági mentések a szabályzat ütemezésével összhangban lesznek aktiválva.

A következő példa lehetővé teszi az Azure-fájlmegosztás **testAzureFileShare** való védelmét a Storage-fiók **testStorageAcct**, a szabályzat **dailyafs**.

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

A parancs megvárja, amíg a védelmi beállítás be nem fejeződik, és hasonló kimenetet ad az ábrán látható módon.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="important-notice---backup-item-identification-for-afs-backups"></a>Fontos figyelmeztetés – az AFS biztonsági mentések biztonsági másolati elemének azonosítása

Ez a szakasz egy fontos változást vázol fel az AFS Backup szolgáltatásban a GA előkészítéséhez.

Az AFS biztonsági mentésének engedélyezésekor a felhasználó a felhasználóbarát fájlmegosztás nevét adja meg az entitás neveként, és létrehoz egy biztonsági mentési elemet. A biztonsági mentési tétel neve a Azure Backup szolgáltatás által létrehozott egyedi azonosító. Általában az azonosító felhasználóbarát nevet is magában foglal. A Soft-delete fontos forgatókönyvének kezeléséhez azonban, ahol egy fájlmegosztás törölhető, és egy másik fájlmegosztás is létrehozható ugyanazzal a névvel, az Azure-fájlmegosztás egyedi identitása mostantól az ügyfél rövid neve helyett azonosító lesz. Az egyes elemek egyedi azonosítójának/nevének megismeréséhez egyszerűen futtassa az ```Get-AzRecoveryServicesBackupItem``` parancsot a megfelelő szűrőkkel a backupManagementType és a WorkloadType számára az összes releváns elem lekéréséhez, majd figyelje meg a visszaadott PS objektum/válasz név mezőjét. Az elemek listázása mindig ajánlott, majd válaszként a "név" mezőből olvassa be az egyedi nevet. Ezzel az értékkel szűrheti az elemeket a "Name" paraméterrel. Ellenkező esetben használja a FriendlyName paramétert, hogy lekérje az adott tételt az ügyfél rövid nevével/azonosítójával.

> [!WARNING]
> Ellenőrizze, hogy a PS verziója frissítve lett-e az AFS biztonsági mentések esetében az "az. Recoveryservices szolgáltatónál 2.6.0" minimális verziójára. Ebben a verzióban az "friendlyName" szűrő ```Get-AzRecoveryServicesBackupItem``` parancshoz érhető el. Adja át az Azure-fájlmegosztás nevét a friendlyName paraméternek. Ha átadja az Azure-fájlmegosztás nevét a "név" paraméternek, ez a verzió figyelmeztetést küld, hogy a rövid nevet adja át a felhasználóbarát név paraméternek. A minimális verzió telepítése nem okozhatja a meglévő parancsfájlok meghibásodását. Telepítse a PS minimális verzióját a következő paranccsal.

```powershell
Install-module -Name Az.RecoveryServices -RequiredVersion 2.6.0
```

## <a name="trigger-an-on-demand-backup"></a>Igény szerinti biztonsági mentés indítása

A [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) használatával igény szerinti biztonsági mentést futtathat egy védett Azure-fájlmegosztás számára.

1. Kérje le a Storage-fiókot abban a tárolóban, amely a biztonsági mentési adatait a [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer)tárolja.
2. A biztonsági mentési feladatok elindításához a [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem)használatával szerezheti be az Azure-fájlmegosztás adatait.
3. Futtasson egy igény szerinti biztonsági mentést a[Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).

Futtassa az igény szerinti biztonsági mentést az alábbiak szerint:

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -FriendlyName "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

A parancs a következő példában látható módon visszaadja a nyomon követett AZONOSÍTÓval ellátott feladatot.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

A biztonsági mentések során az Azure-fájlmegosztás pillanatképeit használja a rendszer, így általában a művelet a parancs által visszaadott kimenettel fejeződik be.

### <a name="using-on-demand-backups-to-extend-retention"></a>Igény szerinti biztonsági másolatok használata a megőrzés kiterjesztéséhez

Az igény szerinti biztonsági mentések segítségével 10 évig megőrizheti a pillanatképeket. A ütemező használatával igény szerinti PowerShell-szkripteket futtathat a választott megőrzéssel, és így minden héten, hónapban vagy évben rendszeres időközönként pillanatfelvételeket készíthet. Rendszeres Pillanatképek készítésekor tekintse át az Azure Backup használatával [igény szerinti biztonsági mentések korlátozásait](https://docs.microsoft.com/azure/backup/backup-azure-files-faq#how-many-on-demand-backups-can-i-take-per-file-share) .

Ha minta parancsfájlokat keres, tekintse meg a GitHubon (<https://github.com/Azure-Samples/Use-PowerShell-for-long-term-retention-of-Azure-Files-Backup>) található minta parancsfájlt Azure Automation runbook használatával, amely lehetővé teszi a biztonsági másolatok rendszeres ütemezését, és akár 10 évig is megőrizheti azokat.

> [!WARNING]
> Győződjön meg arról, hogy a PS verziója frissítve lett az "az. Recoveryservices szolgáltatónál 2.6.0" minimális verziójára az Automation-runbookok lévő AFS-alapú biztonsági mentésekhez. A régi "AzureRM" modult az "az" modullal kell helyettesíteni. Ebben a verzióban az "friendlyName" szűrő ```Get-AzRecoveryServicesBackupItem``` parancshoz érhető el. Adja át az Azure-fájlmegosztás nevét a friendlyName paraméternek. Ha átadja az Azure-fájlmegosztás nevét a "név" paraméternek, ez a verzió figyelmeztetést küld, hogy a rövid nevet adja át a felhasználóbarát név paraméternek.

## <a name="next-steps"></a>Következő lépések

[Tudnivalók](backup-afs.md) a Azure Portal Azure Files biztonsági mentéséről.
