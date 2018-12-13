---
title: Üzembe helyezés és kezelés a PowerShell-lel az Azure fájlmegosztások biztonsági mentése
description: Üzembe helyezése és az Azure-ban az Azure-fájlmegosztások biztonsági másolatainak kezelése PowerShell használatával
services: backup
author: pvrk
manager: shivamg
keywords: PowersShell; Az Azure files biztonsági mentése; Az Azure files-visszaállítás;
ms.service: backup
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: pullabhk
ms.assetid: 80da8ece-2cce-40dd-8dce-79960b6ae073
ms.openlocfilehash: 90623981f67bbed15ade743192525676e58a0a83
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53318693"
---
# <a name="use-powershell-to-back-up-and-restore-azure-file-shares"></a>PowerShell-lel történő biztonsági mentése és visszaállítása az Azure-fájlmegosztások

Ez a cikk bemutatja, hogyan biztonsági mentése és helyreállítása Azure-fájlmegosztások a Recovery Services-tárolót az Azure PowerShell-parancsmagok használatával. Recovery Services-tároló egy Azure Resource Manager-erőforrás használt adatokat és adategységeket az Azure Backup és az Azure Site Recovery szolgáltatást.

## <a name="concepts"></a>Alapelvek

Ha nem ismeri az Azure Backup szolgáltatás a szolgáltatás áttekintését ismertető cikkben [Mi az Azure Backup?](backup-introduction-to-azure-backup.md). Mielőtt elkezdené, győződjön meg arról, hogy, jegyezze fel az előzetes verzióként elérhető funkciókat dokumentált Azure-fájlmegosztások biztonsági mentésével [Itt](backup-azure-files.md).

A PowerShell segítségével hatékonyan, fontos tudni, hogy a hierarchiában, és hol kell elkezdeni az objektumok.

![Recovery Services objektum hierarchia](./media/backup-azure-vms-arm-automation/recovery-services-object-hierarchy.png)

Az AzureRm.RecoveryServices.Backup PowerShell-parancsmagok leírása, tekintse meg a [Azure Backup – Recovery Services-parancsmagok](https://docs.microsoft.com/powershell/module/azurerm.recoveryservices.backup) az Azure-könyvtárban.

## <a name="setup-and-registration"></a>Beállítása és regisztrálása

> [!NOTE]
> Feljegyzett [Itt](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.13.0), az AzureRM modul véget ér 2018. november az új funkciókkal. Ezért adjuk támogatása az Azure-fájlmegosztások biztonsági mentése az új "Az' PS-modullal. Tervezzük az előkészíteni a GA kiadás Az modul is.

A kezdéshez:

1. ["Az" PowerShell legújabb verzióját töltse](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azurermps-6.13.0) (a szükséges minimális verziója: 0.7.0)

2. Keresse meg a rendelkezésre álló Azure Backup PowerShell-parancsmagok a következő parancs beírásával:

    ```powershell
    Get-Command *azrecoveryservices*
    ```
    Az aliasok és az Azure Backup az Azure Site Recovery és a Recovery Services-tároló-parancsmagok jelennek meg. Az alábbi képen látható egy példa látni. Akkor sem a parancsmagok teljes listája.

    ![a Recovery Services listája](./media/backup-azure-afs-automation/list-of-recoveryservices-ps-az.png)

3. Jelentkezzen be az Azure-fiók használatával **Connect-AzAccount**. Ez a parancsmag kimenetei weblap kéri a hitelesítő adatait:

    * Azt is megteheti, megadhatja a fiók hitelesítő adatait a paramétert a **Connect-AzAccount** parancsmag használatával a **-hitelesítő adat** paraméter.
    * Ha Ön CSP-partner nevében egy bérlő dolgozik, adja meg az ügyfél egy bérlőt az elsődleges tartomány Bérlőazonosítója vagy a bérlő neve. Példa: **Csatlakozás AzAccount-bérlő "fabrikam.com"**

4. Társítsa az előfizetést szeretné használni a fiókot, mert egy fiók több előfizetéssel is rendelkezik:

    ```powershell
    Select-AzureRmSubscription -SubscriptionName $SubscriptionName
    ```

5. Az Azure Backup használatakor az első alkalommal kell használnia a **Register-AzResourceProvider** parancsmagot, hogy regisztrálja az Azure Recovery Services-szolgáltatót az előfizetésében.

    ```powershell
    Register-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```

6. Ellenőrizheti, hogy a szolgáltatók regisztrálása sikeresen befejeződött, a következő parancsokkal:
    ```powershell
    Get-AzResourceProvider -ProviderNamespace "Microsoft.RecoveryServices"
    ```
    A parancs kimenetében a **RegistrationState** kell módosítani a **regisztrált**. Ha nem, egyszerűen csak futtatnia kell a **Register-AzResourceProvider** újra a parancsmagot.

A PowerShell használatával automatizálható a következő feladatokat:

* Recovery Services-tároló létrehozása
* Azure-fájlmegosztások biztonsági mentésének konfigurálása
* Biztonsági mentési feladat aktiválása
* A biztonsági mentési feladat monitorozása
* Azure-fájlmegosztás visszaállítása
* Egy külön Azure fájl Azure-fájlmegosztás visszaállítása

## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

A következő lépések végigvezetik Önt a Recovery Services-tároló létrehozása.

1. A Recovery Services-tároló, a Resource Manager-erőforrással, így a elhelyezi egy erőforráscsoporton belül kell. Használjon egy meglévő erőforráscsoportot, vagy hozzon létre egy erőforráscsoportot a **New-AzResourceGroup** parancsmagot. Egy erőforráscsoport létrehozásakor adja meg a nevét és az erőforráscsoport helyét.  

    ```powershell
    New-AzResourceGroup -Name "test-rg" -Location "West US"
    ```
2. Használja a **New-AzRecoveryServicesVault** parancsmagot a Recovery Services-tároló létrehozásához. Mindenképpen adja meg a tároló ugyanazon a helyen, mint az erőforráscsoport.

    ```powershell
    New-AzRecoveryServicesVault -Name "testvault" -ResourceGroupName "test-rg" -Location "West US"
    ```
3. Adja meg a használandó; tárhely-redundancia típusát használhat [helyileg redundáns tárolás (LRS)](../storage/common/storage-redundancy-lrs.md) vagy [Georedundáns tárolás (GRS)](../storage/common/storage-redundancy-grs.md). Az alábbi példa bemutatja, hogy a - BackupStorageRedundancy beállítás a testvault GeoRedundant értékre van állítva.

    ```powershell
    $vault1 = Get-AzRecoveryServicesVault -Name "testvault"
    Set-AzRecoveryServicesBackupProperties  -Vault $vault1 -BackupStorageRedundancy GeoRedundant
    ```

## <a name="view-the-vaults-in-a-subscription"></a>A tárolók megtekintheti az előfizetéshez

Az előfizetés összes tárolók megtekintéséhez használja **Get-AzRecoveryServicesVault**:

```powershell
Get-AzRecoveryServicesVault
```

A kimenet a következő példához hasonló, figyelje meg, hogy a társított ResourceGroupName és a helyet.

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

Használat **Set-AzRecoveryServicesVaultContext** a tárolási környezet beállításához. A tárolási környezet beállítását követően az minden további parancsmagra érvényes lesz. Az alábbi példa beállítja a tárolóhoz, a tárolási környezet *testvault*.

```powershell
Get-AzRecoveryServicesVault -Name "testvault" | Set-AzRecoveryServicesVaultContext
```

> [!NOTE]
> Azt tervezi, hogy az Azure PowerShell irányelveinek megfelelően tárolási környezet beállítását kivezetjük. Ehelyett azt javasoljuk, hogy felhasználók számára, hogy az alábbiakban említett vault azonosítója

Azt is megteheti akkor is store/fetch, amelyhez hozzá szeretné PowerShell művelet végrehajtása, és adja át azt a megfelelő parancsot a tároló azonosítója.

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "Contoso-docs-rg" -Name "testvault" | select -ExpandProperty ID
```

## <a name="configure-backup-for-an-azure-file-share"></a>Azure-fájlmegosztások biztonsági mentésének konfigurálása

### <a name="create-protection-policy"></a>Védelmi szabályzat létrehozása

A biztonsági mentési alkalmazásvédelmi szabályzat legalább egy adatmegőrzési házirend társítva. Adatmegőrzési szabályzat határozza meg, mennyi ideig egy helyreállítási pontot a megtartani, mielőtt törölné a rendszer. Használat **Get-AzRecoveryServicesBackupRetentionPolicyObject** az alapértelmezett megőrzési szabályzat megtekintéséhez.  Ehhez hasonlóan használhatja **Get-AzRecoveryServicesBackupSchedulePolicyObject** az alapértelmezett ütemezés házirend beszerzéséhez. A **New-AzRecoveryServicesBackupProtectionPolicy** parancsmag létrehoz egy PowerShell-objektumot, amely tartalmazza a biztonsági mentési szabályzat. A ütemezése és megőrzése csoportházirend-objektumok használhatók bemeneteként a **New-AzRecoveryServicesBackupProtectionPolicy** parancsmagot. A következő példában változókat az ütemezési házirend és a megőrzési házirend tárolja. A példában használja ezeket a változókat paraméterek megadásához egy védelmi szabályzat létrehozásakor *NewPolicy*.

```powershell
$schPol = Get-AzRecoveryServicesBackupSchedulePolicyObject -WorkloadType "AzureFiles"
$retPol = Get-AzRecoveryServicesBackupRetentionPolicyObject -WorkloadType "AzureFiles"
New-AzRecoveryServicesBackupProtectionPolicy -Name "NewAFSPolicy" -WorkloadType "AzureFiles" -RetentionPolicy $retPol -SchedulePolicy $schPol
```

A kimenet a következő példához hasonló:

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
NewAFSPolicy           AzureFiles            AzureStorage              10/24/2017 1:30:00 AM
```

### <a name="enable-protection"></a>Védelem engedélyezése

Miután meghatározta az alkalmazásvédelmi szabályzatot, engedélyezheti a védelmét, az Azure-fájlmegosztást az ehhez a szabályzathoz.

Először beolvassa a megfelelő csoportházirend-objektumot a **Get-AzRecoveryServicesBackupProtectionPolicy** parancsmagot. Használhatja ezt a parancsmagot egy adott szabályzatot, vagy egy számításifeladat-típust rendelt házirendek megtekintéséhez.

Az alábbi példa lekéri a számítási feladatok típusa, az Azure filesba – szabályzatok.

```powershell
Get-AzRecoveryServicesBackupProtectionPolicy -WorkloadType "AzureFiles"
```

A kimenet a következő példához hasonló:

```powershell
Name                 WorkloadType       BackupManagementType BackupTime                DaysOfWeek
----                 ------------       -------------------- ----------                ----------
dailyafs             AzureFiles         AzureStorage         1/10/2018 12:30:00 AM
```

> [!NOTE]
> A PowerShellben a BackupTime mező az időzóna UTC. Azonban amikor biztonsági mentésének ideje jelenik meg az Azure Portalon, az idő igazodik a helyi időzónában.
>
>

A következő szabályzatot a biztonsági mentési házirend nevű, "dailyafs" kérdezi le.

```powershell
$afsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "dailyafs"
```

Használat **engedélyezése – AzRecoveryServicesBackupProtection** kívánja engedélyezni a védelmet a cikk a házirend. Ha a házirend társítva a tárolóhoz, a biztonsági mentési munkafolyamat akkor aktiválódik, a házirend-ütemezés meghatározott időben.

Az alábbi példa lehetővé teszi, hogy az Azure-fájlmegosztást, "testAzureFileShare", a tárolási fiók "testStorageAcct", mellett a házirend dailyafs a védelmét.

```powershell
Enable-AzRecoveryServicesBackupProtection -StorageAccountName "testStorageAcct" -Name "testAzureFS" -Policy $afsPol
```

A parancs megvárja, amíg a védelem konfigurálásának feladata befejeződött, és lehetővé teszi a hasonló kimenet, ahogy az alábbi.

```cmd
WorkloadName       Operation            Status                 StartTime                                                                                                         EndTime                   JobID
------------             ---------            ------               ---------                                  -------                   -----
testAzureFS       ConfigureBackup      Completed            11/12/2018 2:15:26 PM     11/12/2018 2:16:11 PM     ec7d4f1d-40bd-46a4-9edb-3193c41f6bf6
```

### <a name="trigger-an-on-demand-backup"></a>Indítson egy igény szerinti biztonsági mentést

Használat **Backup-AzRecoveryServicesBackupItem** védett Azure fájlmegosztás biztonsági mentési feladat aktiválásához. Kérje le a tárolási fiók és a fájlmegosztást a benne a következő parancsokat, és indítson egy igény szerinti biztonsági mentést.

```powershell
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType "AzureFiles" -Name "testAzureFS"
$job =  Backup-AzRecoveryServicesBackupItem -Item $afsBkpItem
```

A parancs visszaadja egy feladatot a következő példához hasonlóan azonosítójú nyomon kell követni.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS       Backup               Completed            11/12/2018 2:42:07 PM     11/12/2018 2:42:11 PM     8bdfe3ab-9bf7-4be6-83d6-37ff1ca13ab6
```

Azt a biztonsági másolatok készítése közben vannak kihasználva Azure fájlmegosztási pillanatképeket, és ezért általában a feladat befejeződik a parancs a kimenetet visszaadja a ideje

### <a name="modify-protection-policy"></a>Alkalmazásvédelmi szabályzat módosítása

Ha meg szeretné változtatni a-házirend esetén, amelyek az Azure-fájlmegosztás védelmét, használja a **engedélyezése – AzRecoveryServicesBackupProtection** a megfelelő biztonságimásolat-elem és az új védelmi házirendet.

Az alábbi példák módosítja a védelmi házirendje "monthlyafs", "dailyafs" a "testAzureFS"

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="restore-azure-file-sharesazure-files"></a>Állítsa vissza az Azure fájlmegosztáso / Azure fájlok

Egy teljes fájlmegosztás visszaállíthatja az eredeti vagy egy másik helyre. Hasonlóképpen a fájlmegosztás egyes fájlokra is állíthatók vissza.

### <a name="fetching-recovery-points"></a>Helyreállítási pontok beolvasása

Használja a **Get-AzRecoveryServicesBackupRecoveryPoint** parancsmag használatával listázhatja a biztonságimásolat-elem tartozó összes helyreállítási pontot. A következő parancsfájlt, a változó a **$rp**, helyreállítási pontot a kiválasztott biztonsági mentési elemet, az elmúlt hét napban tömbje. A tömb fordított sorrendben idő 0. indexnél a legújabb helyreállítási ponttal van rendezve. Standard PowerShell tömb indexelő segítségével válassza ki a helyreállítási pont. $Rp [0] a példában a legutóbbi helyreállítási pontot választja.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

A kimenet a következő példához hasonló:

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

Után a megfelelő helyreállítási pontot választja, folytassa a fájlmegosztás/fájl visszaállítása egy másodlagos/eredeti helyre, kövesse az alábbi utasításokat.

### <a name="restore-azure-file-shares-to-an-alternate-location"></a>Azure-fájlmegosztások visszaállítás másik helyre

#### <a name="restoring-an-azure-file-share"></a>Azure-fájlmegosztás visszaállítása

Határozza meg a másik helyet azáltal, hogy a következő információkat:

* ***TargetStorageAccountName***: Storage-fiók, amelyre a biztonsági másolat tartalom helyreáll. A célként megadott tárfiók, tároló, amely ugyanazon a helyen kell lennie.
* ***TargetFileShareName***: A célként megadott tárfiók, amelyre a biztonsági másolat tartalom helyreáll lévő fájlmegosztások
* ***TargetFolder***: A fájlmegosztás adatainak visszaállítása, amelyhez a mappát. Ha gyökérmappájába, a biztonsági másolat tartalmat kell visszaállítani, adja meg a célként megadott mappa értékek üres karakterlánc
* ***ResolveConflict***: A visszaállított adatokkal ütközés utasítás. Fogadja el a "Felülírása" vagy "kihagyja"

Adja meg ezeket a paramétereket egy másik helyre fájlmegosztás visszaállítása biztonsági a restore parancsot.

````powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
````

A parancs visszaadja az alábbi példában látható módon követendő azonosítójú feladat.

````powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
````

#### <a name="restoring-an-azure-file"></a>Egy Azure fájl visszaállítása

Abban az esetben, szeretne egy egyedi fájl helyett egy teljes fájlmegosztás visszaállítását, az egyes fájl egyedi módon azonosítani kell a következő paraméterek megadásával.

* ***TargetStorageAccountName***: Storage-fiók, amelyre a biztonsági másolat tartalom helyreáll. A célként megadott tárfiók, tároló, amely ugyanazon a helyen kell lennie.
* ***TargetFileShareName***: A célként megadott tárfiók, amelyre a biztonsági másolat tartalom helyreáll lévő fájlmegosztások
* ***TargetFolder***: A fájlmegosztás adatainak visszaállítása, amelyhez a mappát. Ha gyökérmappájába, a biztonsági másolat tartalmat kell visszaállítani, adja meg a célként megadott mappa értékek üres karakterlánc
* ***SourceFilePath***: A fájlmegosztás karakterláncként belül vissza kell állítani a fájl abszolút elérési útja. Ez a ugyanazt az útvonalat a használt ```Get-AzStorageFile``` PS-parancsmagot.
* ***SourceFileType***: E könyvtár vagy fájl van kiválasztva. Fogadja el a "Directory" vagy "Fájl"
* ***ResolveConflict***: A visszaállított adatokkal ütközés utasítás. Fogadja el a "Felülírása" vagy "kihagyja"

Amint láthatja, a további paraméterek csak kapcsolódó vissza kell állítani a fájlméretet.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Ez is visszaad egy feladatot, amely egy azonosítóval, ahogyan fentebb nyomon követheti.

### <a name="restore-azure-file-shares-to-original-location"></a>Azure-fájlmegosztások visszaállítás az eredeti helyre

Eredeti hely visszaállítását, esetén az összes cél kapcsolódó paraméterei nem adhatók meg. Csak ```ResolveConflict``` kell megadni

#### <a name="overwrite-an-azure-file-share"></a>Írja felül az Azure-fájlmegosztások

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Egy Azure fájl felülírása

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="track-backup-and-restore-jobs"></a>Nyomon követheti a biztonsági mentési és visszaállítási feladatok

Igény szerinti biztonsági mentési és visszaállítási műveletek látható módon adja vissza egy feladat és a egy azonosító [fent](#trigger-an-on-demand-backup). Használja a ```Get-AzRecoveryServicesBackupJobDetails``` parancsmag a feladat előrehaladását úgy követheti nyomon, és beolvassa a további részleteket.

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
1073871825 Microsoft Azure Backup encountered an internal error. Wait for a few minutes and then try the operation again. If the issue persists, please contact Microsoft support
```
