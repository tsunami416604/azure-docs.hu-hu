---
title: Az Azure-fájlok visszaállítása a PowerShell segítségével
description: Ebben a cikkben megtudhatja, hogyan állíthatja vissza az Azure Files az Azure Backup szolgáltatás és a PowerShell használatával.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 12bff49bc249b23542534d218b13b517411f461b
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756196"
---
# <a name="restore-azure-files-with-powershell"></a>Az Azure-fájlok visszaállítása a PowerShell segítségével

Ez a cikk bemutatja, hogyan állíthatja vissza a teljes fájlmegosztást, vagy adott fájlokat az Azure Backup szolgáltatás által az Azure [PowerShell](backup-overview.md) használatával létrehozott visszaállítási pontról.

A megosztáson lévő teljes fájlmegosztást vagy adott fájlokat visszaállíthatja. Visszaállíthatja az eredeti vagy egy másik helyre.

> [!WARNING]
> Győződjön meg arról, hogy a PS-verzió az AFS biztonsági mentések esetében az "Az.RecoveryServices 2.6.0" minimális verziójára van frissítve. További információt a módosítás követelményét tagjelző [szakaszban](backup-azure-afs-automation.md#important-notice---backup-item-identification-for-afs-backups) talál.

>[!NOTE]
>Az Azure Backup mostantól támogatja több fájl vagy mappa visszaállítását az eredeti vagy alternatív helyre a PowerShell használatával. A dokumentum [ezen részében](#restore-multiple-files-or-folders-to-original-or-alternate-location) megtudhatja, hogyan.

## <a name="fetch-recovery-points"></a>Helyreállítási pontok beolvasása

A [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0) segítségével sorolja fel a biztonsági másolatban szereplő elem összes helyreállítási pontját.

A következő parancsfájlban:

* A **$rp** változó a kiválasztott biztonsági másolat elemének helyreállítási pontjainak tömbje az elmúlt hét napból.
* A tömb az idő sorrendje fordított sorrendben van rendezve, a legutóbbi helyreállítási pont pedig **0**indexben.
* A helyreállítási pont kiválasztásához használja a szabványos PowerShell-tömbindexelést.
* A példában **$rp[0]** kiválasztja a legújabb helyreállítási pontot.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

A kimenet hasonló a következőhöz.

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

A megfelelő helyreállítási pont kiválasztása után visszaállíthatja a fájlmegosztást vagy -fájlt az eredeti helyre vagy egy másik helyre.

## <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Azure-fájlmegosztás visszaállítása másik helyre

A [restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) segítségével állítsa vissza a kijelölt helyreállítási pontot. Adja meg ezeket a paramétereket a másodlagos hely azonosításához:

* **TargetStorageAccountName**: Az a tárfiók, amelyre a biztonsági másolatot készítő tartalom visszaáll. A céltárfióknak ugyanazon a helyen kell lennie, mint a tárolónak.
* **TargetFileShareName**: A fájl megosztások a cél tárfiók, amelyre a biztonsági másolatot készítő tartalom vissza.
* **TargetFolder**: Az a mappa a fájlmegosztás alatt, amelyre az adatokat visszaállítják. Ha a biztonsági másolatot tartalmazó tartalmat vissza kell állítani egy gyökérmappába, adja meg a célmappa értékeit üres karakterláncként.
* **ResolveConflict**: Utasítás, ha ütközés van a visszaállított adatokkal. Elfogadja **a Felülírást** vagy **a Kihagyás műveletet.**

Futtassa a parancsmabot a következő paraméterekkel:

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

A parancs egy nyomon követendő azonosítóval rendelkező feladatot ad vissza, ahogy az a következő példában látható.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

## <a name="restore-an-azure-file-to-an-alternate-location"></a>Azure-fájl visszaállítása másik helyre

A [restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) segítségével állítsa vissza a kijelölt helyreállítási pontot. Adja meg ezeket a paramétereket a másodlagos hely azonosításához és a visszaállítani kívánt fájl egyedi azonosításához.

* **TargetStorageAccountName**: Az a tárfiók, amelyre a biztonsági másolatot készítő tartalom visszaáll. A céltárfióknak ugyanazon a helyen kell lennie, mint a tárolónak.
* **TargetFileShareName**: A fájl megosztások a cél tárfiók, amelyre a biztonsági másolatot készítő tartalom vissza.
* **TargetFolder**: Az a mappa a fájlmegosztás alatt, amelyre az adatokat visszaállítják. Ha a biztonsági másolatot tartalmazó tartalmat vissza kell állítani egy gyökérmappába, adja meg a célmappa értékeit üres karakterláncként.
* **SourceFilePath**: A fájl abszolút elérési útja, amelyet a fájlmegosztáson belül karakterláncként kell visszaállítani. Ez az elérési út ugyanaz az elérési út, amelyet a **Get-AzStorageFile** PowerShell-parancsmag használ.
* **SourceFileType**: Azt jelzi, hogy egy könyvtár vagy fájl van-e kijelölve. Elfogadja a **címzetet** vagy **a fájlt.**
* **ResolveConflict**: Utasítás, ha ütközés van a visszaállított adatokkal. Elfogadja **a Felülírást** vagy **a Kihagyás műveletet.**

A további paraméterek (SourceFilePath és SourceFileType) csak a visszaállítani kívánt fájlhoz kapcsolódnak.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Ez a parancs egy nyomon követendő azonosítóval rendelkező feladatot ad vissza, ahogy az az előző szakaszban látható.

## <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Az Azure-fájlmegosztások és -fájlok visszaállítása az eredeti helyre

Amikor visszaállít egy eredeti helyre, nem kell megadnia a cél- és célhoz kapcsolódó paramétereket. Csak **ResolveConflict** adható meg.

### <a name="overwrite-an-azure-file-share"></a>Azure-fájlmegosztás felülírása

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

### <a name="overwrite-an-azure-file"></a>Azure-fájl felülírása

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>Több fájl vagy mappa visszaállítása eredeti vagy alternatív helyre

Használja a [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) parancsot úgy, hogy átadja az összes visszaállítani kívánt fájl vagy mappa elérési útját a **MultipleSourceFilePath** paraméter értékeként.

### <a name="restore-multiple-files"></a>Több fájl visszaállítása

A következő parancsfájlban megpróbáljuk visszaállítani a *FileSharePage.png* és a *MyTestFile.txt* fájlokat.

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"

$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID

$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"

$RP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID

$files = ("FileSharePage.png", "MyTestFile.txt")

Restore-AzRecoveryServicesBackupItem -RecoveryPoint $RP[0] -MultipleSourceFilePath $files -SourceFileType File -ResolveConflict Overwrite -VaultId $vault.ID -VaultLocation $vault.Location
```

### <a name="restore-multiple-directories"></a>Több könyvtár visszaállítása

A következő parancsfájlban próbáljuk visszaállítani a *zrs1_restore* és *visszaállítása* könyvtárakat.

```powershell
$vault = Get-AzRecoveryServicesVault -ResourceGroupName "azurefiles" -Name "azurefilesvault"

$Container = Get-AzRecoveryServicesBackupContainer -ContainerType AzureStorage -Status Registered -FriendlyName "afsaccount" -VaultId $vault.ID

$BackupItem = Get-AzRecoveryServicesBackupItem -Container $Container -WorkloadType AzureFiles -VaultId $vault.ID -FriendlyName "azurefiles"

$RP = Get-AzRecoveryServicesBackupRecoveryPoint -Item $BackupItem -VaultId $vault.ID

$files = ("Restore","zrs1_restore")

Restore-AzRecoveryServicesBackupItem -RecoveryPoint $RP[0] -MultipleSourceFilePath $files -SourceFileType Directory -ResolveConflict Overwrite -VaultId $vault.ID -VaultLocation $vault.Location
```

A kimenet az alábbihoz hasonló lesz:

```output
WorkloadName         Operation         Status          StartTime                EndTime       JobID
------------         ---------         ------          ---------                -------       -----
azurefiles           Restore           InProgress      4/5/2020 8:01:24 AM                    cd36abc3-0242-44b1-9964-0a9102b74d57
```

Ha több fájlt vagy mappát szeretne másik helyre állítani, használja a fenti parancsfájlokat a célhelyhez kapcsolódó paraméterértékek megadásával, ahogy azt a [fenti, Egy Azure-fájl visszaállítása egy másik helyre](#restore-an-azure-file-to-an-alternate-location)című részben ismertetett.

## <a name="next-steps"></a>További lépések

[Ismerje meg](restore-afs.md) az Azure-fájlok visszaállítását az Azure Portalon.
