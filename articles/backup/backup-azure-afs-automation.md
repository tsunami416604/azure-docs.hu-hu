---
title: Biztonsági mentése és visszaállítása az Azure Files Azure Backup és a PowerShell használatával
description: Biztonsági mentése és visszaállítása az Azure Files Azure Backup és a PowerShell használatával.
author: pvrk
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 03/05/2018
ms.author: pullabhk
ms.openlocfilehash: 83fe8d17699c19d442fd734d71d828eb9fd9d6ed
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258360"
---
# <a name="back-up-and-restore-azure-files-with-powershell"></a>Biztonsági mentése és visszaállítása a PowerShell-lel az Azure Files

Ez a cikk ismerteti az Azure PowerShell használatával biztonsági mentése és helyreállítása az Azure Files fájl megosztás használatával egy [Azure Backup](backup-overview.md) Recovery Services-tároló. 

Ez az oktatóanyag a következőket ismerteti:

> [!div class="checklist"]
> * Állítsa be a PowerShell és az Azure Recovery Services-szolgáltató regisztrálásához.
> * Recovery Services-tároló létrehozása.
> * Azure-fájlmegosztások biztonsági mentésének konfigurálásához.
> * A biztonsági mentési feladat futtatása.
> * Állítsa vissza a biztonsági mentést Azure-fájlmegosztást, illetve egy adott fájl megosztásból.
> * Figyelheti a biztonsági mentési és visszaállítási feladatok.


## <a name="before-you-start"></a>Előkészületek

- [További](backup-azure-recovery-services-vault-overview.md) Recovery Services-tárolók kapcsolatban.
- Olvassa el az előzetes funkciókat [Azure-fájlmegosztások biztonsági mentésével](backup-azure-files.md).
- Tekintse át a Recovery Services PowerShell objektum hierarchiában.


## <a name="recovery-services-object-hierarchy"></a>Recovery Services objektum hierarchia

Az alábbi ábrán az objektumhierarchia foglalja össze.

![Recovery Services objektum hierarchia](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Tekintse át a **Az.RecoveryServices** [parancsmag-referencia](/powershell/module/az.recoveryservices) referencia az Azure-könyvtárban.


## <a name="set-up-and-install"></a>Beállítása és telepítése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Állítsa be a PowerShell a következőképpen:

1. [Töltse le a legújabb verzióját Az PowerShell](/powershell/azure/install-az-ps). A minimálisan szükséges verzió 1.0.0-s.

2. Keresse meg az Azure Backup PowerShell-parancsmagok a következő paranccsal:

    ```powershell
    Get-Command *azrecoveryservices*
    ```
3. Tekintse át az aliasokat, és az Azure Backup az Azure Site Recovery és a Recovery Services-tároló-parancsmagok jelennek meg. Íme egy példa mi látható. Akkor sem parancsmagok teljes listája.

    ![A Recovery Services-parancsmagok listája](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

3. Jelentkezzen be az Azure-fiókjába **Connect-AzAccount**.
4. A megjelenő webhelyen kéri, hogy adjon meg a fiók hitelesítő adatait.

    - Azt is megteheti, megadhatja a fiók hitelesítő adatait a paramétert a **Connect-AzAccount** parancsmagot **-hitelesítő adat**.
    - Ha Ön CSP-partner nevében egy bérlő dolgozik, adja meg az ügyfél egy bérlő, a Bérlőazonosítója vagy bérlői elsődleges tartománynév használatával. Például **Connect-AzAccount-bérlő** fabrikam.com.

4. Társítsa az előfizetés használni kívánt fiókkal, mert egy fiók több előfizetéssel is rendelkezik.

    ```powershell
    Select-AzSubscription -SubscriptionName $SubscriptionName
    ```

5. Ha első alkalommal használja az Azure Backup, használja a **Register-AzResourceProvider** parancsmagot, hogy az Azure Recovery Services-szolgáltató regisztrálása az előfizetéshez.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Győződjön meg arról, hogy a szolgáltatók regisztrálása sikeresen befejeződött:

    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
7. A parancs kimeneténél ellenőrizze, hogy **RegistrationState** vált **regisztrált**. Ha ez nem, futtassa a **Register-AzResourceProvider** újra a parancsmagot.



## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

Kövesse az alábbi lépéseket egy Recovery Services-tároló létrehozása.

- A Recovery Services-tároló, a Resource Manager-erőforrással, így a erőforráscsoporton belül kell elhelyeznie. Használhat egy meglévő erőforráscsoportot, vagy létrehozhat egy erőforráscsoportot a **New-AzResourceGroup** parancsmagot. Amikor létrehoz egy erőforráscsoportot, adja meg a nevét és az erőforráscsoport helyét. 

1. Egy erőforráscsoport egy tároló kerül. Ha nem rendelkezik egy meglévő erőforrást, csoport, hozzon létre egy újat a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-1.4.0). Ebben a példában létrehozunk egy új erőforráscsoportot az USA nyugati régiójában.

   ```powershell
   New-AzResourceGroup -Name "test-rg" -Location "West US"
   ```
2. Használja a [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/New-AzRecoveryServicesVault?view=azps-1.4.0) parancsmag segítségével hozza létre a tárolót. Adja meg a tároló ugyanarra a helyre, amint lett megadva az erőforráscsoport.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Adja meg a tároló tárolására a redundancia típusát.

   - Használhat [helyileg redundáns tárolás](../storage/common/storage-redundancy-lrs.md) vagy [georedundáns tárolás](../storage/common/storage-redundancy-grs.md).
   - A következő példa készletek a **- BackupStorageRedundancy** első számú megoldás a[Set-AzRecoveryServicesBackupProperties](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperties?view=azps-1.4.0) a cmd **testvault** beállítása  **GeoRedundant**.

     ```powershell
     $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
     Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
     ```

### <a name="view-the-vaults-in-a-subscription"></a>A tárolók megtekintheti az előfizetéshez

Az előfizetés összes tárolók megtekintéséhez használja [Get-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesvault?view=azps-1.4.0).

```powershell
Get-AzRecoveryServicesVault
```

A kimenet az alábbihoz hasonlít. Vegye figyelembe, hogy a társított erőforráscsoportot és helyet adott.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

### <a name="set-the-vault-context"></a>A tárolási környezet beállítása

Store a tár objektumot egy változóban, és állítsa be a tárolási környezet.

- Számos Azure Backup-parancsmaghoz szükséges bemenetként, a helyreállítási tár objektumot, hogy legyen egy változóban tárolni a a tár objektumot.
- A tárolási környezet a tár által védett adatok típusa. Állítsa be a [Set-AzRecoveryServicesVaultContext](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesvaultcontext?view=azps-1.4.0). A környezet beállítását követően minden további parancsmagra vonatkozik.


A következő példa állítja a tárolási környezetet a **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

### <a name="fetch-the-vault-id"></a>Beolvassa a tároló azonosítója

A tárolási környezet beállítását az Azure PowerShell irányelveinek megfelelően kivezetése tervezzük. Ehelyett tárolására vagy beolvasni a tár Azonosítóját, és adja át azt vonatkozó parancsokat, és a következő:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-a-backup-policy"></a>Biztonsági mentési szabályzat konfigurálása

A biztonsági mentési szabályzat meghatározza a biztonsági mentések ütemezését, és mennyi ideig a biztonsági mentési helyreállítási pontot kell tárolni:

- Biztonsági mentési szabályzat legalább egy adatmegőrzési házirend társítva. A megőrzési házirend határozza meg, mennyi ideig egy helyreállítási pontot a megtartani, mielőtt törölné a rendszer.
- Az alapértelmezett biztonsági mentési szabályzat megőrzési használatával nézet [Get-AzRecoveryServicesBackupRetentionPolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupretentionpolicyobject?view=azps-1.4.0).
- Az alapértelmezett biztonsági mentési szabályzat ütemezése használatával nézet [Get-AzRecoveryServicesBackupSchedulePolicyObject](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupschedulepolicyobject?view=azps-1.4.0).
-  Használja a [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0) parancsmaggal hozzon létre egy új biztonsági mentési szabályzatot. A ütemezése és megőrzése csoportházirend-objektumok adjon meg.

A következő példában változókat az ütemezési házirend és a megőrzési házirend tárolja. Majd használja e változó paraméterek egy új szabályzatot (**NewAFSPolicy**). **NewAFSPolicy** vesz egy napi biztonsági mentést, és 30 napig őrzi meg.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

A kimenet az alábbihoz hasonlít.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2017 1:30:00 AM
```



## <a name="enable-backup"></a>Biztonsági mentés engedélyezése

A biztonsági mentési házirend meghatározása, után engedélyezheti a a házirendet használja az Azure-fájlmegosztás védelmének.

### <a name="retrieve-a-backup-policy"></a>Biztonsági mentési szabályzat lekérése

Meg beolvasni a megfelelő csoportházirend-objektumot [Get-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupprotectionpolicy?view=azps-1.4.0). Ezt a parancsmagot használhatja egy adott szabályzatot, vagy egy számításifeladat-típust rendelt házirendek megtekintéséhez.

#### <a name="retrieve-a-policy-for-a-workload-type"></a>Egy számítási feladat esetén egy házirend beolvasása

Az alábbi példa lekéri a számításifeladat-típust szabályzatok **Azure filesba –**.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

A kimenet az alábbihoz hasonlít.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```
> [!NOTE]
> Időzónája a **BackupTime** mezőt a PowerShellben az egyezményes világidő (UTC). Ha biztonsági mentésének ideje jelenik meg az Azure Portalon, az idő a helyi időzóna igazodik.

### <a name="retrieve-a-specific-policy"></a>Egy adott házirend beolvasása

A következő szabályzatot kérdezi le a biztonsági mentési házirend nevű **dailyafs**.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

### <a name="enable-backup-and-apply-policy"></a>Biztonsági mentés engedélyezése és a szabályzat alkalmazása

A védelem engedélyezése [engedélyezése – AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Követően a házirend társítva a tárolóhoz, biztonsági mentések esetén a házirend-ütemezés szerint.

Az alábbi példa az Azure-fájlmegosztás védelmének engedélyezése **testAzureFileShare** tárfiókban **testStorageAcct**, a házirend **dailyafs**.

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

A parancs megvárja, amíg a védelem konfigurálásának feladata befejeződött, és a egy hasonló kimenetet biztosít látható módon.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

## <a name="trigger-an-on-demand-backup"></a>Indítson egy igény szerinti biztonsági mentést

Használat [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem?view=azps-1.4.0) egy igény szerinti biztonsági mentés egy védett Azure-fájlmegosztások futtatásához.

1. A storage-fiók- és fájlmegosztás a tárolóból a tárolóban, amely tárolja a biztonsági mentési adatok olvashatók be [Get-AzRecoveryServicesBackupContainer](/powershell/module/az.recoveryservices/get-Azrecoveryservicesbackupcontainer).
2. Indítsa el a biztonsági mentési feladat, szerezze be a virtuális Gépet a információit [Get-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/Get-AzRecoveryServicesBackupItem).
3. Egy igény szerinti biztonsági mentés futtatása[Backup-AzRecoveryServicesBackupItem](/powershell/module/az.recoveryservices/backup-Azrecoveryservicesbackupitem).

Futtassa az igény szerinti biztonsági mentést az alábbiak szerint:
    
```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -Name "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

A parancs visszaadja a követni kívánt azonosítójú feladat a következő példában látható módon.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Az Azure fájlmegosztási pillanatképeket használt közben a biztonsági másolatokat készít, ezért általában a feladat befejeződik a parancs a kimenetet visszaadja a ideje.

### <a name="modify-the-protection-policy"></a>Az alkalmazásvédelmi szabályzat módosítása

Ha módosítani szeretné a szabályzatot, az Azure-fájlmegosztások mentésére szolgál, használjon [engedélyezése – AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0). Adja meg a megfelelő biztonságimásolat-elem és az új biztonsági mentési szabályzatot.

A következő példát módosítások a **testAzureFS** protection-házirend **dailyafs** való **monthlyafs**.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="restore-azure-file-shares-and-files"></a>Azure-fájlmegosztások és a fájlok visszaállítása

Visszaállíthatja egy teljes fájlmegosztás vagy a megosztás megadott fájlokat. Visszaállíthatja az eredeti helyére vagy máshová. 

### <a name="fetch-recovery-points"></a>Helyreállítási pontok beolvasása

Használat [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0) az összes helyreállítási pontot a biztonsági másolat elemet a listában.

A következő parancsfájlban:

- A változó **$rp** helyreállítási pontjait a kiválasztott biztonsági mentési elemet az elmúlt hét napban tömbje.
- A tömb fordított sorrendben rendezi indexpozíciójánál található legújabb helyreállítási pont az idő **0**.
- Standard PowerShell tömb indexelő segítségével válassza ki a helyreállítási pont.
- A példában **$rp [0]** a legutóbbi helyreállítási pontot választja.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

A kimenet az alábbihoz hasonlít.

```powershell
FileShareSnapshotUri : https://testStorageAcct.file.core.windows.net/testAzureFS?sharesnapshot=2018-11-20T00:31:04.00000
                       00Z
RecoveryPointType    : FileSystemConsistent
RecoveryPointTime    : 11/20/2018 12:31:05 AM
RecoveryPointId      : 86593702401459
ItemName             : testAzureFS
Id                   : /Subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/Micros                      oft.RecoveryServices/vaults/testVault/backupFabrics/Azure/protectionContainers/StorageContainer;storage;teststorageRG;testStorageAcct/protectedItems/AzureFileShare;testAzureFS/recoveryPoints/86593702401462
WorkloadType         : AzureFiles
ContainerName        : storage;teststorageRG;testStorageAcct
ContainerType        : AzureStorage
BackupManagementType : AzureStorage
```
Miután a megfelelő helyreállítási pontot választja, visszaállítása a fájlmegosztás vagy a fájl az eredeti helyére vagy máshová.

### <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Azure-fájlmegosztás visszaállítása másik helyre

Használja a [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/en-us/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) állíthatja vissza a kijelölt helyreállítási pont. Adja meg ezeket a paramétereket, a másik helyre azonosításához: 

- **TargetStorageAccountName**: A storage-fiók, amelyre a biztonsági másolat tartalom helyreáll. A céloldali tárfiók és a tárolónak ugyanazon a helyen kell lennie.
- **TargetFileShareName**: A fájlmegosztások belül a céloldali tárfiók fiókot a biztonsági másolat tartalom helyreáll.
- **TargetFolder**: A fájlmegosztás adatainak visszaállítása, amelyhez a mappát. Ha a biztonsági másolatban szereplő tartalmat vissza kell állítani a gyökérmappájába, adjon meg a célként megadott mappa értékeket egy üres karakterlánccal.
- **ResolveConflict**: Ha a helyreállított adatokkal ütközés utasítás. Fogadja el **felülírása** vagy **kihagyása**.

Futtassa a parancsmagot a paramétereket a következőképpen:

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

A parancs visszaadja a követni kívánt azonosítójú feladat a következő példában látható módon.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

### <a name="restore-an-azure-file-to-an-alternate-location"></a>Állítsa vissza egy Azure-fájlt egy másik helyre

Használja a [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/en-us/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) állíthatja vissza a kijelölt helyreállítási pont. Adja meg ezeket a paramétereket, azonosíthatja a másik helyet, és a visszaállítani kívánt fájl egyedi azonosításához.

* **TargetStorageAccountName**: A storage-fiók, amelyre a biztonsági másolat tartalom helyreáll. A céloldali tárfiók és a tárolónak ugyanazon a helyen kell lennie.
* **TargetFileShareName**: A fájlmegosztások belül a céloldali tárfiók fiókot a biztonsági másolat tartalom helyreáll.
* **TargetFolder**: A fájlmegosztás adatainak visszaállítása, amelyhez a mappát. Ha a biztonsági másolatban szereplő tartalmat vissza kell állítani a gyökérmappájába, adjon meg a célként megadott mappa értékeket egy üres karakterlánccal.
* **SourceFilePath**: A fájlmegosztás karakterláncként belül vissza kell állítani a fájl abszolút elérési útja. Ez az elérési út használatban ugyanazt az útvonalat a **Get-AzStorageFile** PowerShell-parancsmagot.
* **SourceFileType**: E könyvtár vagy fájl van kiválasztva. Fogadja el **Directory** vagy **fájl**.
* **ResolveConflict**: Ha a helyreállított adatokkal ütközés utasítás. Fogadja el **felülírása** vagy **kihagyása**.

A további paraméterek (SourceFilePath és SourceFileType) csak a visszaállítani kívánt egyes fájl kapcsolódnak.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Ez a parancs visszaadja a követni kívánt azonosítójú feladat, az előző szakaszban látható módon.

### <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Azure-fájlmegosztások és a fájlok helyreállítása az eredeti helyre

Amikor helyreállítja az eredeti helyre, nem kell adja meg a cél - és cél kapcsolatos paramétereket. Csak **ResolveConflict** meg kell adni.

#### <a name="overwrite-an-azure-file-share"></a>Írja felül az Azure-fájlmegosztások

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Egy Azure fájl felülírása

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="track-backup-and-restore-jobs"></a>Nyomon követheti a biztonsági mentési és visszaállítási feladatok

Igény szerinti biztonsági mentési és visszaállítási műveletek mikor látható módon adja vissza egy feladat Azonosítóját, valamint hogy [futtatta egy igény szerinti biztonsági mentést](#trigger-an-on-demand-backup). Használja a [Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-1.4.0) parancsmag segítségével nyomon követheti a feladat állapotát és a részleteket.

```powershell
$job = Get-AzRecoveryServicesBackupJob -JobId 00000000-6c46-496e-980a-3740ccb2ad75 -VaultId $vaultID

 $job | fl


IsCancellable        : False
IsRetriable          : False
ErrorDetails         : {Microsoft.Azure.Commands.RecoveryServices.Backup.Cmdlets.Models.AzureFileShareJobErrorInfo}
ActivityId           : 00000000-5b71-4d73-9465-8a4a91f13a36
JobId                : 00000000-6c46-496e-980a-3740ccb2ad75
Operation            : Restore
Status               : Failed
WorkloadName         : testAFS
StartTime            : 12/10/2018 9:56:38 AM
EndTime              : 12/10/2018 11:03:03 AM
Duration             : 01:06:24.4660027
BackupManagementType : AzureStorage

$job.ErrorDetails

 ErrorCode ErrorMessage                                          Recommendations
 --------- ------------                                          ---------------
1073871825 Microsoft Azure Backup encountered an internal error. Wait for a few minutes and then try the operation again. If the issue persists, please contact Microsoft support.
```
## <a name="next-steps"></a>További lépések
[Ismerje meg](backup-azure-files.md) Azure Files biztonsági mentése az Azure Portalon.
