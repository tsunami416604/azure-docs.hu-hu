---
title: Üzembe helyezése és kezelése az Azure-fájlmegosztások biztonsági mentése PowerShell-lel
description: Üzembe helyezése és az Azure-ban az Azure-fájlmegosztások biztonsági másolatainak kezelése PowerShell használatával
services: backup
author: pvrk
manager: shivamg
keywords: PowerShell; Az Azure Files biztonsági mentése; Az Azure Files-visszaállítás;
ms.service: backup
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: pullabhk
ms.assetid: 80da8ece-2cce-40dd-8dce-79960b6ae073
ms.openlocfilehash: 912336d697e8f7b5d9c71080ec9a052ca562da4b
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55101131"
---
# <a name="use-powershell-to-back-up-and-restore-azure-file-shares"></a>PowerShell-lel történő biztonsági mentése és visszaállítása az Azure-fájlmegosztások

Ez a cikk bemutatja, hogyan biztonsági mentése és helyreállítása Azure-fájlmegosztások a Recovery Services-tárolót az Azure PowerShell-parancsmagok használatával. Recovery Services-tároló egy Azure Resource Manager-erőforrás, amely adatokat és adategységeket az Azure Backup és az Azure Site Recovery segítségével.

## <a name="concepts"></a>Alapelvek

Ha még nem ismeri az Azure Backup szolgáltatással, a szolgáltatás áttekintését, [Mi az Azure Backup?](backup-introduction-to-azure-backup.md). Mielőtt elkezdené, tekintse meg az előzetes verzióként elérhető funkciókat, amelyek biztonsági mentése az Azure-fájlmegosztások a [biztonsági mentése az Azure-fájlmegosztások](backup-azure-files.md).

A PowerShell segítségével hatékonyan, fontos tudni, hogy a hierarchiában, és hol kell elkezdeni az objektumok.

![Recovery Services objektum hierarchia](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Megtekintéséhez a **AzureRm.RecoveryServices.Backup** PowerShell-parancsmagok leírása, lásd: [Azure Backup – Recovery Services-parancsmagok](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup) az Azure-könyvtárban.

## <a name="setup-and-registration"></a>Beállítása és regisztrálása

> [!NOTE]
> Amint [Azure PowerShell-modul telepítéséhez](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.13.0), az AzureRM-modul a 2018 November végződő az új funkciókkal. Támogatást biztosítunk az új Az PowerShell-modul, amely már általánosan elérhető az Azure-fájlmegosztások biztonsági mentése.

Kövesse az alábbi lépéseket a kezdéshez.

1. [Töltse le a legújabb verzióját Az PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azurermps-6.13.0). A minimálisan szükséges verzió 1.0.0-s.

2. Keresse meg a **Azure Backup PowerShell** parancsmagok érhető el a következő parancs beírásával.

    ```powershell
    Get-Command *azrecoveryservices*
    ```
    Az aliasok és az Azure Backup az Azure Site Recovery és a Recovery Services-tároló-parancsmagok jelennek meg. Az alábbi képen látható egy példa megjelenő. Akkor sem a parancsmagok teljes listája.

    ![A Recovery Services-parancsmagok listája](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

3. Jelentkezzen be az Azure-fiók használatával **Connect-AzAccount**. Ez a parancsmag kimenetei egy weblap, amelyen a fiók hitelesítő adatainak megadását kéri:

    * Azt is megteheti, megadhatja a fiók hitelesítő adatait a paramétert a **Connect-AzAccount** parancsmag használatával a **-hitelesítő adat** paraméter.
    * Ha Ön CSP-partner nevében egy bérlő dolgozik, adja meg az ügyfél bérlőként Bérlőazonosítója vagy bérlői elsődleges tartománynév használatával. Például **Connect-AzAccount-bérlő** fabrikam.com.

4. Az előfizetés, mert egy fiók több előfizetéssel is rendelkezik a fiókkal használni kívánt társítja.

    ```powershell
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

5. Ha első alkalommal használja az Azure Backup, használja a **Register-AzResourceProvider** parancsmagot, hogy az Azure Recovery Services-szolgáltató regisztrálása az előfizetéshez.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Győződjön meg arról, hogy a szolgáltatók regisztrálása sikeresen befejeződött a következő parancs segítségével.
    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
    A parancs kimeneténél **RegistrationState** vált **regisztrált**. Ha nem látja ezt a módosítást, futtassa a **Register-AzResourceProvider** újra a parancsmagot.

A PowerShell használatával automatizálható a következő feladatokat:

* Recovery Services-tároló létrehozása.
* Azure-fájlmegosztások biztonsági mentésének konfigurálásához.
* Biztonsági mentési feladat aktiválása.
* A biztonsági mentési feladat monitorozása.
* Azure-fájlmegosztás visszaállítása.
* Egy külön Azure fájl visszaállítása egy Azure-fájlmegosztást.

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

Kövesse az alábbi lépéseket egy Recovery Services-tároló létrehozása.

1. A Recovery Services-tároló, a Resource Manager-erőforrással, így a erőforráscsoporton belül kell elhelyeznie. Használhat egy meglévő erőforráscsoportot, vagy létrehozhat egy erőforráscsoportot a **New-AzResourceGroup** parancsmagot. Amikor létrehoz egy erőforráscsoportot, adja meg a nevét és az erőforráscsoport helyét.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```
2. Használja a **New-AzRecoveryServicesVault** parancsmagot a Recovery Services-tároló létrehozásához. Adja meg a tároló ugyanarra a helyre, amint lett megadva az erőforráscsoport.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Adja meg a használandó tárhely-redundancia típusát. Használhat [helyileg redundáns tárolás](../storage/common/storage-redundancy-lrs.md) vagy [georedundáns tárolás](../storage/common/storage-redundancy-grs.md). A következő példa bemutatja a **- BackupStorageRedundancy** beállításhoz tartozó **testvault** beállítása **GeoRedundant**.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>A tárolók megtekintheti az előfizetéshez

Az előfizetés összes tárolók megtekintéséhez használja **Get-AzRecoveryServicesVault**.

```powershell
Get-AzRecoveryServicesVault
```

A kimenet a következő példához hasonló. Figyelje meg, hogy a társított **ResourceGroupName** és **hely** vannak megadva.

```powershell
Name              : Contoso-vault
ID                : /subscriptions/1234
Type              : Microsoft.RecoveryServices/vaults
Location          : WestUS
ResourceGroupName : Contoso-docs-rg
SubscriptionId    : 1234-567f-8910-abc
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

Számos Azure Backup-parancsmaghoz szükséges bemenetként a helyreállítási tár objektum.

Használat **Set-AzRecoveryServicesVaultContext** a tárolási környezet beállításához. A tárolási környezet beállítását követően minden további parancsmagra vonatkozik. A következő példa állítja a tárolási környezetet a **testvault**.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

> [!NOTE]
> Tervezzük kivezetjük a tároló környezet beállítása az Azure PowerShell igényeinek megfelelően. Ehelyett azt javasoljuk, hogy a felhasználók adja át a tárolót azonosító említetteknek megfelelően az alábbi utasításokat.

Azt is megteheti tárolásához, vagy beolvassa az azonosítója, amelyhez a PowerShell művelet végrehajtása, és adja át azt az adott parancs szeretne a tárolóhoz.

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-backup-for-an-azure-file-share"></a>Azure-fájlmegosztások biztonsági mentésének konfigurálása

### <a name="create-a-protection-policy"></a>Egy védelmi szabályzat létrehozása

A biztonsági mentési alkalmazásvédelmi szabályzat legalább egy adatmegőrzési házirend társítva. A megőrzési házirend határozza meg, mennyi ideig egy helyreállítási pontot a megtartani, mielőtt törölné a rendszer. Használat **Get-AzRecoveryServicesBackupRetentionPolicyObject** az alapértelmezett megőrzési szabályzat megtekintéséhez. 

Ehhez hasonlóan használhatja **Get-AzRecoveryServicesBackupSchedulePolicyObject** az alapértelmezett ütemezés házirend beszerzéséhez. A **New-AzRecoveryServicesBackupProtectionPolicy** parancsmag létrehoz egy PowerShell-objektumot, amely tartalmazza a biztonsági mentési szabályzat. A ütemezése és megőrzése csoportházirend-objektumok használhatók bemeneteként a **New-AzRecoveryServicesBackupProtectionPolicy** parancsmagot. 

A következő példában változókat az ütemezési házirend és a megőrzési házirend tárolja. A példában ezeket a változókat a paraméterek meghatározása során a **NewPolicy** alkalmazásvédelmi szabályzat jön létre.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

A kimenet a következő példához hasonló.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2017 1:30:00 AM
```

**NewAFSPolicy** vesz egy napi biztonsági mentést, és 30 napig őrzi meg.

### <a name="enable-protection"></a>Védelem engedélyezése

Az alkalmazásvédelmi szabályzat határozza meg, miután a védelem az Azure-fájlmegosztást az ehhez a szabályzathoz tartozó engedélyezheti.

Először beolvassa a megfelelő csoportházirend-objektumot a **Get-AzRecoveryServicesBackupProtectionPolicy** parancsmagot. Ezt a parancsmagot használhatja egy adott szabályzatot vagy számításifeladat-típust rendelt házirendek megtekintéséhez.

Az alábbi példa lekéri a szabályzatok a számítási típus **Azure filesba –**.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

A kimenet a következő példához hasonló.

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> Időzónája a **BackupTime** mezőt a PowerShellben az egyezményes világidő (UTC). Ha biztonsági mentésének ideje jelenik meg az Azure Portalon, az idő a helyi időzóna igazodik.
>
>

A következő szabályzatot kérdezi le a biztonsági mentési házirend nevű **dailyafs**.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

Használat **engedélyezése – AzRecoveryServicesBackupProtection** kívánja engedélyezni a védelmet a cikk a házirend. Miután a szabályzat rendelve a tárolóhoz, a biztonsági mentési munkafolyamat akkor aktiválódik, a házirend-ütemezés meghatározott időben.

Az alábbi példa az Azure-fájlmegosztás védelmének engedélyezése **testAzureFileShare** a storage-fiókban **testStorageAcct** szabályzat **dailyafs**.

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

A parancs megvárja, amíg a védelem konfigurálásának feladata befejeződött, és a egy hasonló kimenetet biztosít látható módon.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

### <a name="trigger-an-on-demand-backup"></a>Indítson egy igény szerinti biztonsági mentést

Használat **Backup-AzRecoveryServicesBackupItem** védett Azure fájlmegosztás biztonsági mentési feladat aktiválásához. Kérje le a tárolási fiók és a fájlmegosztást a benne található a következő parancsokat, és indítson egy igény szerinti biztonsági mentést.

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

Ha módosítani szeretné a szabályzatot, amellyel az Azure-fájlmegosztás védelmét, használjon **engedélyezése – AzRecoveryServicesBackupProtection** a megfelelő biztonságimásolat-elem és az új védelmi házirendet.

A következő példát módosítások a **testAzureFS** protection-házirend **dailyafs** való **monthlyafs**.

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="restore-azure-file-shares-and-azure-files"></a>Azure-fájlmegosztások és Azure-fájlok visszaállítása

Egy teljes fájlmegosztás visszaállíthatja az eredeti helyére vagy máshová. Ehhez hasonlóan a fájlmegosztás egyes fájlokra is lehet visszaállítani, túl.

### <a name="fetch-recovery-points"></a>Helyreállítási pontok beolvasása

Használja a **Get-AzRecoveryServicesBackupRecoveryPoint** parancsmag használatával listázhatja a biztonságimásolat-elem tartozó összes helyreállítási pontot. A következő parancsfájlt, a változó a **$rp** helyreállítási pontjait a kiválasztott biztonsági mentési elemet az elmúlt hét napban tömbje. A tömb fordított sorrendben rendezi indexpozíciójánál található legújabb helyreállítási pont az idő **0**. Standard PowerShell tömb indexelő segítségével válassza ki a helyreállítási pont. A példában **$rp [0]** a legutóbbi helyreállítási pontot választja.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

A kimenet a következő példához hasonló.

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

Után a megfelelő helyreállítási pontot választja, állítsa vissza a fájlmegosztás vagy egy másik helyre vagy az eredeti helyre itt leírtak.

### <a name="restore-azure-file-shares-to-an-alternate-location"></a>Azure-fájlmegosztások visszaállítás másik helyre

#### <a name="restore-an-azure-file-share"></a>Azure-fájlmegosztás visszaállítása

Határozza meg a másik helyet azáltal, hogy a következő információkat:

* **TargetStorageAccountName**: A storage-fiók, amelyre a biztonsági másolat tartalom helyreáll. A céloldali tárfiók és a tárolónak ugyanazon a helyen kell lennie.
* **TargetFileShareName**: A fájlmegosztások belül a céloldali tárfiók fiókot a biztonsági másolat tartalom helyreáll.
* **TargetFolder**: A fájlmegosztás adatainak visszaállítása, amelyhez a mappát. Ha a biztonsági másolatban szereplő tartalmat vissza kell állítani a gyökérmappájába, adjon meg a célként megadott mappa értékeket egy üres karakterlánccal.
* **ResolveConflict**: Ha a helyreállított adatokkal ütközés utasítás. Fogadja el **felülírása** vagy **kihagyása**.

Adja meg ezeket a paramétereket a restore parancsot készül fájlmegosztás visszaállítása másik helyre.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

A parancs visszaadja a követni kívánt azonosítójú feladat a következő példában látható módon.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

#### <a name="restore-an-azure-file"></a>Állítsa vissza egy Azure-fájlt

Egy egyedi fájl helyett egy teljes fájlmegosztás visszaállítását, egyedi azonosításához az adott fájl a következő paraméterek megadásával:

* **TargetStorageAccountName**: A storage-fiók, amelyre a biztonsági másolat tartalom helyreáll. A céloldali tárfiók és a tárolónak ugyanazon a helyen kell lennie.
* **TargetFileShareName**: A fájlmegosztások belül a céloldali tárfiók fiókot a biztonsági másolat tartalom helyreáll.
* **TargetFolder**: A fájlmegosztás adatainak visszaállítása, amelyhez a mappát. Ha a biztonsági másolatban szereplő tartalmat vissza kell állítani a gyökérmappájába, adjon meg a célként megadott mappa értékeket egy üres karakterlánccal.
* **SourceFilePath**: A fájlmegosztás karakterláncként belül vissza kell állítani a fájl abszolút elérési útja. Ez az elérési út használatban ugyanazt az útvonalat a **Get-AzStorageFile** PowerShell-parancsmagot.
* **SourceFileType**: E könyvtár vagy fájl van kiválasztva. Fogadja el **Directory** vagy **fájl**.
* **ResolveConflict**: Ha a helyreállított adatokkal ütközés utasítás. Fogadja el **felülírása** vagy **kihagyása**.

A további paraméterek csak vissza kell állítani az egyes fájlok kapcsolódnak.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Ez a parancs is visszaadja a követni kívánt azonosítójú feladat előzőekben ismertetettek szerint.

### <a name="restore-azure-file-shares-to-the-original-location"></a>Azure-fájlmegosztások visszaállítás az eredeti helyre

Amikor helyreállítja az eredeti helyre, cél - és cél kapcsolatos összes paraméter nem kell megadni. Csak **ResolveConflict** meg kell adni.

#### <a name="overwrite-an-azure-file-share"></a>Írja felül az Azure-fájlmegosztások

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Egy Azure fájl felülírása

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="track-backup-and-restore-jobs"></a>Nyomon követheti a biztonsági mentési és visszaállítási feladatok

Igény szerinti biztonsági mentési és visszaállítási műveletek az előző szakaszban látható módon adja vissza egy feladat Azonosítóját, valamint ["Aktiválás egy igény szerinti biztonsági mentést."](#trigger-an-on-demand-backup) Használja a **Get-AzRecoveryServicesBackupJobDetails** parancsmagot, hogy a feladat előrehaladását úgy követheti nyomon, és beolvassa a további részleteket.

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
