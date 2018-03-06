---
title: "GYIK: Azure Files biztonsági mentése"
description: "A cikk részletesen ismerteti az Azure Files Azure-ban történő védelmének módját. Ez a kivonat jelenik meg a keresési eredmények között."
services: backup
keywords: "Ne adjon hozzá kulcsszavakat és ne szerkessze azokat a keresőoptimalizálást végző szakemberrel való egyeztetés nélkül."
author: markgalioto
ms.author: markgal
ms.date: 2/21/2018
ms.topic: tutorial
ms.service: backup
ms.workload: storage-backup-recovery
manager: carmonm
ms.openlocfilehash: 3d09914c93d0f48b8f6bed405202682aaf925a5f
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2018
---
# <a name="questions-about-backing-up-azure-files"></a>Kérdések az Azure Files biztonsági mentéséről
Ez a cikk az Azure Files biztonsági mentésével kapcsolatos általános kérdéseket válaszol meg. Egyes válaszokban részletes információkat tartalmazó cikkekre mutató hivatkozások találhatók. Emellett egy fórumbejegyzésben is feltehet kérdéseket az Azure Backup szolgáltatással kapcsolatban a [vitafórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

A cikk szakaszainak gyors áttekintéséhez használja **A cikk tartalma** terület hivatkozásait a jobb oldalon.

## <a name="configuring-the-backup-job-for-azure-files"></a>Biztonsági mentési feladat konfigurálása Azure Fileshoz

### <a name="why-cant-i-see-some-of-my-storage-accounts-i-want-to-protect-that-contain-valid-file-shares-br"></a>Miért nem látok néhány, általam védeni kívánt tárfiókot, amelyek érvényes fájlmegosztásokat tartalmaznak? <br/>
Az előzetes verzióban az Azure-fájlmegosztások biztonsági mentése nem támogatja a tárfiókok minden típusát. Tekintse át [itt](troubleshoot-azure-files.md#preview-boundaries) a támogatott tárfiókok listáját.

### <a name="why-cant-i-see-some-of-my-file-shares-in-the-storage-account-when-im-trying-to-configure-backup-br"></a>Miért nem látom néhány fájlmegosztásom a tárfiókban, miközben a biztonsági mentést próbálom konfigurálni? <br/>
Ellenőrizze, hogy a fájlmegosztás már védve van-e ugyanabban a helyreállítási tárban vagy nemrégiben lett-e törölve.

### <a name="why-cant-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync-br"></a>Miért nem tudom védeni az Azure Files Sync szinkronizált csoportjához csatlakoztatott fájlmegosztásaimat? <br/>
A szinkronizált csoportokhoz csatlakoztatott Azure-fájlmegosztások védelme korlátozott az előzetes kiadásban. Hozzáférés igényléséhez írja meg előfizetési azonosítóját a következő e-mail-címre: [AskAzureBackupTeam@microsoft.com](email:askazurebackupteam@microsoft.com). 

### <a name="in-which-geos-can-i-back-up-azure-file-shares-br"></a>Milyen földrajzi helyeken készíthetek biztonsági mentést Azure-fájlmegosztásaimról <br/>
Az Azure-fájlmegosztások biztonsági mentése jelenleg előzetes verzióban és csak a következő helyeken érhető el: 
-   Délkelet-Ausztrália (ASE) 
- Dél-Brazília (BRS)
- Közép-Kanada (CNC)
-   Kelet-Kanada (CE)
-   USA középső régiója (CUS)
-   Kelet-Ázsia (EA)
-   Kelet-Ausztrália (AE) 
-   USA keleti régiója (EUS)
-   USA 2. keleti régiója (EUS2)
-   Közép-India (INC) 
-   USA északi középső régiója (NCUS) 
-   Észak-Európa (NE) 
-   USA déli középső régiója (SCUS) 
-   Délkelet-Ázsia (SEA)
-   Egyesült Királyság déli régiója (UKS) 
-   Egyesült Királyság nyugati régiója (UKW) 
-   Nyugat-Európa (WE) 
-   USA nyugati régiója (WUS)
-   USA nyugati középső régiója (WCUS)
-   USA 2. nyugati régiója (WUS 2)

Ha a fenti listában nem található régióban kell használnia ezt a megoldást, akkor írjon a következő e-mail-címre: [AskAzureBackupTeam@microsoft.com](email:askazurebackupteam@microsoft.com).

### <a name="how-many-file-shares-can-i-protect-in-a-vaultbr"></a>Hány fájlmegosztást védhetek meg egy tárban?<br/>
Az előzetes verzióban táranként legfeljebb 25 tárfiók fájlmegosztásait védheti meg. Ezenkívül egyetlen tárban akár 200 fájlmegosztást is megóvhat. 

## <a name="backup"></a>Biztonsági mentés

### <a name="how-many-on-demand-backups-can-i-take-per-file-share-br"></a>Hány igény szerinti biztonsági mentést készíthetek fájlmegosztásonként? <br/>
Bármely időpontban legfeljebb 200 pillanatkép készíthető fájlmegosztásonként. A korlátba a szabályzatban meghatározottak szerint beletartoznak az Azure Backup által készített pillanatképek is. Ha biztonsági mentései kezdenek meghiúsulni ezen korlát elérése után, akkor töröljön néhány igény szerinti visszaállítási pontot a jövőbeli sikeres biztonsági mentések érdekében.

### <a name="after-enabling-virtual-networks-on-my-storage-account-the-backup-of-file-shares-in-the-account-started-failing-why"></a>A virtuális hálózatok tárfiókomon való engedélyezését követően a fiókban lévő fájlmegosztások biztonsági mentése elkezdett meghiúsulni. Hogy miért?
Az Azure-fájlmegosztások biztonsági mentése nem támogatja azokat a tárfiókokat, amelyeken engedélyezve vannak a virtuális hálózatok. A sikeres biztonsági mentés engedélyezéséhez tiltsa le a virtuális hálózatokat a tárfiókoknál. 

## <a name="restore"></a>Visszaállítás

### <a name="can-i-recover-from-a-deleted-file-share-br"></a>Tudok helyreállítani törölt fájlmegosztásból? <br/>
Fájlmegosztás törlésekor egy lista jelenik meg azokról a biztonsági másolatokról, amelyek a szintén törölve lesznek, és a rendszer megerősítést kér Öntől. Törölt fájlmegosztást nem lehet visszaállítani.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-a-file-share-br"></a>Vissza tudok állítani biztonsági mentésekből, ha leállítottam a fájlmegosztás védelmét? <br/>
Igen. Ha a védelem leállításakor a **Biztonsági másolatok adatainak megőrzése** lehetőséget választotta, akkor minden meglévő visszaállítási pontból vissza tud állítani.

## <a name="manage-backup"></a>Biztonsági mentés kezelése

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-it-br"></a>Hozzáférhetek az Azure biztonsági mentések által készített pillanatképekhez és csatlakoztathatom azokat? <br/>
Az Azure Backup által készített minden pillanatkép elérhető a portál Pillanatképek megtekintése részén, illetve a PowerShell vagy a parancssori felület segítségével. Az [itt](../storage/files/storage-how-to-use-files-snapshots.md#mount-a-file-share) látható eljárás segítségével csatlakoztathatja őket.

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups-br"></a>Mi a biztonsági mentésekhez konfigurálható megőrzés maximális száma? <br/>
Az Azure-fájlmegosztások biztonsági mentése segítségével akár 120 napig is megőrizheti napi biztonsági mentéseit.

### <a name="what-happens-when-i-change-the-backup-policy-for-a-file-share-br"></a>Mi történik, ha módosítom a fájlmegosztáshoz tartozó biztonsági mentési szabályzatot? <br/>
Amikor új házirendet alkalmaznak a fájlmegosztáso(ko)n, az új szabályzat ütemezése és megőrzése érvényes. Ha növeli a megőrzési időtartamot, a meglévő helyreállítási pontok az új szabályzatnak megfelelően megmaradnak. Ha csökkenti a megőrzési időtartamot, a helyreállítási pontok a következő tisztítási feladat során törlendőként lesznek megjelölve.


## <a name="see-also"></a>Lásd még
Ezek az információk kizárólag az Azure Files biztonsági mentésére vonatkoznak, az Azure Backup más területeinek megismeréséhez tekintse át az alábbi Backup GYIK-fejezeteket:
-  [Helyreállítási tár – GYIK](backup-azure-backup-faq.md)
-  [Azure-beli virtuális gép biztonsági mentése – GYIK](backup-azure-vm-backup-faq.md)
-  [Az Azure Backup ügynöke – GYIK](backup-azure-file-folder-backup-faq.md)
