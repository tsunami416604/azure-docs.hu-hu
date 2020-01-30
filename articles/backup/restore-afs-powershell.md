---
title: Azure Files visszaállítása a PowerShell-lel
description: Ebből a cikkből megtudhatja, hogyan állíthatja vissza Azure Files a Azure Backup szolgáltatás és a PowerShell használatával.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: c1c116033dbf44e6e1f332195a18c7dfdbcd6c71
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76776338"
---
# <a name="restore-azure-files-with-powershell"></a>Azure Files visszaállítása a PowerShell-lel

Ez a cikk azt ismerteti, hogyan lehet visszaállítani egy teljes fájlmegosztást vagy adott fájlokat a [Azure Backup](backup-overview.md) szolgáltatás által az Azure PowerShell használatával létrehozott visszaállítási pontról.

Visszaállíthat egy teljes fájlmegosztást vagy adott fájlokat a megosztáson. Az eredeti helyre vagy egy másik helyre is visszaállíthatja.

## <a name="fetch-recovery-points"></a>Helyreállítási pontok beolvasása

A [Get-AzRecoveryServicesBackupRecoveryPoint](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackuprecoverypoint?view=azps-1.4.0) használatával listázhatja a biztonsági másolatban szereplő elemek összes helyreállítási pontját.

A következő parancsfájlban:

* Az **$RP** változó a kiválasztott biztonsági mentési elemhez tartozó helyreállítási pontok tömbje az elmúlt hét napban.
* A tömb a **0**. indexben szereplő legutóbbi helyreállítási ponttal fordított sorrendben van rendezve.
* A helyreállítási pont kiválasztásához használja a PowerShell-tömb szabványos indexelését.
* A példában a **$RP [0]** kiválasztja a legutóbbi helyreállítási pontot.

```powershell
$startDate = (Get-Date).AddDays(-7)
$endDate = Get-Date
$rp = Get-AzRecoveryServicesBackupRecoveryPoint -Item $afsBkpItem -StartDate $startdate.ToUniversalTime() -EndDate $enddate.ToUniversalTime()

$rp[0] | fl
```

A kimenet az alábbihoz hasonló.

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

A megfelelő helyreállítási pont kiválasztása után visszaállíthatja a fájlmegosztást vagy a fájlt az eredeti helyre, vagy egy másik helyre.

## <a name="restore-an-azure-file-share-to-an-alternate-location"></a>Azure-fájlmegosztás visszaállítása másik helyre

A [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) használatával állítsa vissza a kijelölt helyreállítási pontot. Adja meg ezeket a paramétereket a másik hely azonosításához:

* **TargetStorageAccountName**: a Storage-fiók, amelyhez a biztonsági másolat tartalma vissza lesz állítva. A célként megadott Storage-fióknak a tárolóval megegyező helyen kell lennie.
* **TargetFileShareName**: a fájlmegosztás azon fiókján belüli megosztás, amelyhez a biztonsági másolat tartalma vissza lesz állítva.
* **TargetFolder**: a fájlmegosztás alatt lévő mappa, amelyhez az adat vissza lett állítva. Ha a biztonsági másolatban lévő tartalmat vissza szeretné állítani egy gyökérkönyvtárba, adja meg a célmappa értékeit üres karakterláncként.
* **ResolveConflict**: utasítás, ha a visszaállított adattal ütközik. A **felülírás** vagy **kihagyás**elfogadása.

Futtassa a parancsmagot a következő paraméterekkel:

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -ResolveConflict Overwrite
```

A parancs a következő példában látható módon visszaadja a nyomon követett AZONOSÍTÓval ellátott feladatot.

```powershell
WorkloadName     Operation            Status               StartTime                 EndTime                   JobID
------------     ---------            ------               ---------                 -------                   -----
testAzureFS        Restore              InProgress           12/10/2018 9:56:38 AM                               9fd34525-6c46-496e-980a-3740ccb2ad75
```

## <a name="restore-an-azure-file-to-an-alternate-location"></a>Azure-fájl visszaállítása másik helyre

A [Restore-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/restore-azrecoveryservicesbackupitem?view=azps-1.4.0) használatával állítsa vissza a kijelölt helyreállítási pontot. Adja meg ezeket a paramétereket a másik hely azonosításához, valamint a visszaállítani kívánt fájl egyedi azonosításához.

* **TargetStorageAccountName**: a Storage-fiók, amelyhez a biztonsági másolat tartalma vissza lesz állítva. A célként megadott Storage-fióknak a tárolóval megegyező helyen kell lennie.
* **TargetFileShareName**: a fájlmegosztás azon fiókján belüli megosztás, amelyhez a biztonsági másolat tartalma vissza lesz állítva.
* **TargetFolder**: a fájlmegosztás alatt lévő mappa, amelyhez az adat vissza lett állítva. Ha a biztonsági másolatban lévő tartalmat vissza szeretné állítani egy gyökérkönyvtárba, adja meg a célmappa értékeit üres karakterláncként.
* **SourceFilePath**: a fájl abszolút elérési útja, amelyet a fájlmegosztás keretén belül helyre kell állítani karakterláncként. Ez az elérési út a **Get-AzStorageFile** PowerShell-parancsmagban használt elérési út.
* **SourceFileType**: azt határozza meg, hogy egy könyvtár vagy egy fájl ki van-e választva. **Címtár** vagy **fájl**elfogadása.
* **ResolveConflict**: utasítás, ha a visszaállított adattal ütközik. A **felülírás** vagy **kihagyás**elfogadása.

A további paraméterek (SourceFilePath és SourceFileType) csak a visszaállítani kívánt egyes fájlokhoz kapcsolódnak.

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -TargetStorageAccountName "TargetStorageAcct" -TargetFileShareName "DestAFS" -TargetFolder "testAzureFS_restored" -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

Ez a parancs visszaadja a nyomon követett AZONOSÍTÓval rendelkező feladatot, ahogy az az előző szakaszban is látható.

## <a name="restore-azure-file-shares-and-files-to-the-original-location"></a>Az Azure-fájlmegosztás és-fájlok visszaállítása az eredeti helyre

Amikor eredeti helyre állítja vissza a visszaállítást, nem kell megadnia a cél és a cél kapcsolatos paramétereket. Csak **ResolveConflict** kell megadni.

#### <a name="overwrite-an-azure-file-share"></a>Azure-fájlmegosztás felülírása

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -ResolveConflict Overwrite
```

#### <a name="overwrite-an-azure-file"></a>Azure-fájl felülírása

```powershell
Restore-AzRecoveryServicesBackupItem -RecoveryPoint $rp[0] -SourceFileType File -SourceFilePath "TestDir/TestDoc.docx" -ResolveConflict Overwrite
```

## <a name="next-steps"></a>Következő lépések

[Tudnivalók](restore-afs.md) a Azure Portal Azure Files visszaállításáról.
