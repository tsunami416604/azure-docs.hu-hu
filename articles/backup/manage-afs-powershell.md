---
title: Azure-fájlmegosztási biztonsági mentések kezelése a PowerShell segítségével
description: Megtudhatja, hogyan használhatja a PowerShellt az Azure Backup szolgáltatás által biztonsági másolatot készítő Azure-fájlmegosztások kezelésére és figyelésére.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: a9dc421db740963fc5cd11e868eb383694376ce1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083166"
---
# <a name="manage-azure-file-share-backups-with-powershell"></a>Azure-fájlmegosztási biztonsági mentések kezelése a PowerShell segítségével

Ez a cikk ismerteti, hogyan azure PowerShell használatával az Azure-fájlmegosztások, amelyek az Azure Backup szolgáltatás által biztonsági másolatot készíteni.

> [!WARNING]
> Győződjön meg arról, hogy a PS-verzió az AFS biztonsági mentések esetében az "Az.RecoveryServices 2.6.0" minimális verziójára van frissítve. További [részletekért](backup-azure-afs-automation.md#important-notice---backup-item-identification-for-afs-backups) tekintse meg a módosítás követelményét felvázoló szakaszt.

## <a name="modify-the-protection-policy"></a>A védelmi házirend módosítása

Az Azure-fájlmegosztás biztonsági mentéséhez használt házirend módosításához használja az [Enable-AzRecoveryServicesBackupProtection parancsot.](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection?view=azps-1.4.0) Adja meg a megfelelő biztonsági mentési elemet és az új biztonsági mentési házirendet.

A következő példa módosítja a **testAzureFS** védelmi szabályzatot **dailyafs-ról** **monthlyafs-ra.**

```powershell
$monthlyafsPol =  Get-AzRecoveryServicesBackupProtectionPolicy -Name "monthlyafs"
$afsContainer = Get-AzRecoveryServicesBackupContainer -FriendlyName "testStorageAcct" -ContainerType AzureStorage
$afsBkpItem = Get-AzRecoveryServicesBackupItem -Container $afsContainer -WorkloadType AzureFiles -Name "testAzureFS"
Enable-AzRecoveryServicesBackupProtection -Item $afsBkpItem -Policy $monthlyafsPol
```

## <a name="track-backup-and-restore-jobs"></a>Feladatok nyomon követése és visszaállítása

Az igény szerinti biztonsági mentési és visszaállítási műveletek egy feladatot és egy azonosítót adnak vissza, ahogy az [az igény szerinti biztonsági mentés futtatásakor](backup-azure-afs-automation.md#trigger-an-on-demand-backup)is látható. A [Get-AzRecoveryServicesBackupJobDetails](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-1.4.0) parancsmag segítségével nyomon követheti a feladat előrehaladását és részleteit.

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

## <a name="stop-protection-on-a-file-share"></a>Egy fájlmegosztás védelmének leállítása

Az Azure-fájlmegosztások védelmét kétféle módon szüntetheti meg:

* Az összes jövőbeli biztonsági mentési feladat leállítása és az összes helyreállítási pont *törlése*
* Állítsa le az összes jövőbeli biztonsági mentési feladatot, de *hagyja el* a helyreállítási pontokat

Előfordulhat, hogy a helyreállítási pontok tárolása során a helyreállítási pontok at, az Azure Backup által létrehozott alapul szolgáló pillanatképek megmaradnak. A helyreállítási pontok elhagyása azonban a fájlmegosztást később is visszaállíthatja, ha szükséges. A helyreállítási pontok elhagyásának költségeiről az [árképzés részleteiben](https://azure.microsoft.com/pricing/details/storage/files/)talál további információt. Ha úgy dönt, hogy törli az összes helyreállítási pontot, nem állíthatja vissza a fájlmegosztást.

## <a name="stop-protection-and-retain-recovery-points"></a>Védelem leállítása és helyreállítási pontok megtartása

Az adatok megőrzése közbeni védelem leállításához használja az [Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-3.3.0) parancsmag használatát.

A következő példa leállítja az *afsfileshare* fájlmegosztás védelmét, de megőrzi az összes helyreállítási pontot:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "afstesting" -Name "afstest" | select -ExpandProperty ID
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureStorage -WorkloadType AzureFiles -Name "afsfileshare" -VaultId $vaultID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $vaultID
```

```output
WorkloadName     Operation         Status         StartTime                 EndTime                   JobID
------------     ---------         ------         ---------                 -------                   -----
afsfileshare     DisableBackup     Completed      1/26/2020 2:43:59 PM      1/26/2020 2:44:21 PM      98d9f8a1-54f2-4d85-8433-c32eafbd793f
```

A kimenetben lévő Feladatazonosító attribútum megfelel annak a feladatnak a feladatazonosítójának, amelyet a "stop protection" művelethez a biztonsági mentési szolgáltatás hoz létre. A feladat állapotának nyomon követéséhez használja a [Get-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/get-azrecoveryservicesbackupjob?view=azps-3.3.0) parancsmag.

## <a name="stop-protection-without-retaining-recovery-points"></a>Védelem leállítása a helyreállítási pontok megtartása nélkül

A helyreállítási pontok megtartása nélküli védelem leállításához használja az [Disable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/disable-azrecoveryservicesbackupprotection?view=azps-3.3.0) parancsmast, és adja hozzá az **-RemoveRecoveryPoints** paramétert.

A következő példa leállítja az *afsfileshare* fájlmegosztás védelmét a helyreállítási pontok megőrzése nélkül:

```powershell
$vaultID = Get-AzRecoveryServicesVault -ResourceGroupName "afstesting" -Name "afstest" | select -ExpandProperty ID
$bkpItem = Get-AzRecoveryServicesBackupItem -BackupManagementType AzureStorage -WorkloadType AzureFiles -Name "afsfileshare" -VaultId $vaultID
Disable-AzRecoveryServicesBackupProtection -Item $bkpItem -VaultId $vaultID -RemoveRecoveryPoints
```

```output
WorkloadName     Operation            Status         StartTime                 EndTime                   JobID
------------     ---------            ------         ---------                 -------                   -----
afsfileshare     DeleteBackupData     Completed      1/26/2020 2:50:57 PM      1/26/2020 2:51:39 PM      b1a61c0b-548a-4687-9d15-9db1cc5bcc85
```

## <a name="next-steps"></a>További lépések

[Ismerje meg az](manage-afs-backup.md) Azure-fájlmegosztási biztonsági mentések kezelését az Azure Portalon.
