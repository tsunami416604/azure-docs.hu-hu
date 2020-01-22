---
title: 'GYIK: Azure Files biztonsági mentése'
description: Ebből a cikkből megismerheti az Azure-fájlmegosztás Azure Backup szolgáltatással való védelemmel kapcsolatos gyakori kérdésekre adott válaszokat.
ms.date: 07/29/2019
ms.topic: conceptual
ms.openlocfilehash: 053fe38b07fff5d289c72024ef185fbf5d647f70
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293997"
---
# <a name="questions-about-backing-up-azure-files"></a>Kérdések az Azure Files biztonsági mentéséről

Ez a cikk az Azure Files biztonsági mentésével kapcsolatos általános kérdéseket válaszol meg. Egyes válaszokban részletes információkat tartalmazó cikkekre mutató hivatkozások találhatók. Emellett egy fórumbejegyzésben is feltehet kérdéseket az Azure Backup szolgáltatással kapcsolatban a [vitafórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

A cikk szakaszainak gyors áttekintéséhez használja **A cikk tartalma** terület hivatkozásait a jobb oldalon.

## <a name="configuring-the-backup-job-for-azure-files"></a>Biztonsági mentési feladat konfigurálása Azure Fileshoz

### <a name="why-cant-i-see-some-of-my-storage-accounts-i-want-to-protect-that-contain-valid-azure-file-shares"></a>Miért nem látok néhány, általam védeni kívánt tárfiókot, amelyek érvényes Azure-fájlmegosztásokat tartalmaznak?

Az előzetes verzióban az Azure-fájlmegosztások biztonsági mentése nem támogatja a tárfiókok minden típusát. Tekintse át [itt](troubleshoot-azure-files.md#limitations-for-azure-file-share-backup-during-preview) a támogatott tárfiókok listáját. Elképzelhető emellett, hogy a keresett tárfiók már védett, vagy másik tárolóban van regisztrálva. [Törölje a regisztrációt](troubleshoot-azure-files.md#configuring-backup) a tárolóban, ha a tárfiókot el szeretné érni egy másik tárolóban a védelem beállításához.

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup"></a>Miért nem látom néhány Azure-fájlmegosztásomat a tárfiókban, miközben a biztonsági mentést próbálom konfigurálni?

Ellenőrizze, hogy az Azure-fájlmegosztás már védve van-e ugyanabban a helyreállítási tárban, vagy nemrégiben lett-e törölve.

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync"></a>Meg tudom védeni az Azure Files Sync szinkronizált csoportjához csatlakoztatott fájlmegosztásaimat?

Igen. A szinkronizált csoportokhoz csatlakoztatott Azure-fájlmegosztások védelme engedélyezett, és a nyilvános előzetes verzió része.

### <a name="when-trying-to-back-up-file-shares-i-clicked-on-a-storage-account-for-discovering-the-file-shares-in-it-however-i-did-not-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>Amikor fájlmegosztásokról próbáltam biztonsági másolatot készíteni, egy Storage-fiókra kattintottam a benne lévő fájlmegosztások felderítése érdekében. Azonban nem biztosítottam számukra védelmet. Hogyan védhetem meg ezeket a fájlmegosztásokat másik tárolóval?

Ha biztonsági mentéskor egy Storage-fiókra kattint az abban lévő fájlmegosztások felderítése érdekében, a Storage-fiókot abból a tárolóról regisztrálja, amelyről ezt végzi. Ha úgy dönt, hogy egy másik tárolóval védi meg a fájlmegosztásokat, [szüntesse meg](troubleshoot-azure-files.md#configuring-backup) a kiválasztott Storage-fiók regisztrációját a tárolóból.

### <a name="can-i-change-the-vault-to-which-i-back-up-my-file-shares"></a>Módosíthatom azt a tárolót, amelyre biztonsági másolatot készítek a fájlmegosztást?

Igen. Azonban [le kell állítania egy fájlmegosztás védelmét](manage-afs-backup.md#stop-protection-on-a-file-share) a csatlakoztatott tárolóból, törölni kell a Storage-fiók [regisztrációját](troubleshoot-azure-files.md#configuring-backup) , majd egy másik tárból kell megadnia azt.

### <a name="in-which-geos-can-i-back-up-azure-file-shares"></a>Milyen térségek tud biztonsági másolatot készíteni az Azure-fájlmegosztásról?

Az Azure-fájlmegosztások biztonsági mentése jelenleg előzetes verzióban és csak a következő helyeken érhető el:

- Kelet-Ausztrália (AE)
- Délkelet-Ausztrália (ASE)
- Dél-Brazília (BRS)
- Közép-Kanada (CNC)
- Kelet-Kanada (CE)
- USA középső régiója (CUS)
- Kelet-Ázsia (EA)
- USA keleti régiója (EUS)
- USA 2. keleti régiója (EUS2)
- Kelet-Japán (JPE)
- Nyugat-Japán (JPW)
- Közép-India (INC)
- Dél-India (INS)
- Korea középső régiója (KRC)
- Korea déli régiója (KRS)
- USA északi középső régiója (NCUS)
- Észak-Európa (NE)
- USA déli középső régiója (SCUS)
- Délkelet-Ázsia (SEA)
- Egyesült Királyság déli régiója (UKS)
- Egyesült Királyság nyugati régiója (UKW)
- Nyugat-Európa (WE)
- USA nyugati régiója (WUS)
- USA nyugati középső régiója (WCUS)
- USA 2. nyugati régiója (WUS 2)
- US Gov Arizona (UGA)
- US Gov Texas (UGT)
- US Gov Virginia (UGV)
- Közép-Ausztrália (ACL)
- Nyugat-India (INW)
- Dél-Afrika északi régiója (SAN)
- Észak-Egyesült Arab (karbamid)
- Közép-Franciaország (FRC)
- Észak-Németország (GN)                       
- Középnyugat-Németország (GWC)
- Dél-Afrika nyugati régiója (SAW)
- UAE Central (UAC)
- NAZT (Kelet-Norvégia)     
- NWW (Norvégia nyugati régiója)
- SZN (Észak-Svájc)

Ha a fenti listában nem szereplő régióban kell használnia ezt a megoldást, írjon a következő e-mail-címre: [AskAzureBackupTeam@microsoft.com](email:askazurebackupteam@microsoft.com).

### <a name="how-many-azure-file-shares-can-i-protect-in-a-vault"></a>Hány Azure-fájlmegosztást védhetek meg egy tárban?

Az előzetes verzióban táranként legfeljebb 50 tárfiók Azure-fájlmegosztásait védheti meg. Ezenkívül egyetlen tárban akár 200 Azure-fájlmegosztást is megóvhat.

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>Biztosíthatok védelmet két különböző fájlmegosztás számára ugyanabból a Storage-fiókból különböző tárolókba?

Nem. A Storage-fiókban lévő összes fájlmegosztás számára csak ugyanazzal a tárolóval biztosítható védelem.

## <a name="backup"></a>Biztonsági mentés

### <a name="how-many-scheduled-backups-can-i-configure-per-file-share"></a>Hány ütemezett biztonsági mentést állíthatok be fájlmegosztás esetén?

A Azure Backup jelenleg az Azure-fájlmegosztás napi biztonsági mentésének ütemezését támogatja.

### <a name="how-many-on-demand-backups-can-i-take-per-file-share"></a>Hány igény szerinti biztonsági mentést készíthetek fájlmegosztásonként?

Egy tetszőleges időpontban legfeljebb 200 pillanatkép készíthető fájlmegosztásonként. A korlátba a szabályzatban meghatározottak szerint beletartoznak az Azure Backup által készített pillanatképek is. Ha biztonsági mentései kezdenek meghiúsulni ezen korlát elérése után, akkor töröljön néhány igény szerinti visszaállítási pontot a jövőbeli sikeres biztonsági mentések érdekében.

## <a name="restore"></a>Visszaállítás

### <a name="can-i-recover-from-a-deleted-azure-file-share"></a>Tudok helyreállítani törölt Azure-fájlmegosztásból?

Azure-fájlmegosztás törlésekor egy lista jelenik meg azokról a biztonsági másolatokról, amelyek a szintén törölve lesznek, és a rendszer megerősítést kér Öntől. Törölt Azure-fájlmegosztást nem lehet visszaállítani.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share"></a>Vissza tudok állítani biztonsági mentésekből, ha leállítottam az Azure-fájlmegosztás védelmét?

Igen. Ha a védelem leállításakor a **Biztonsági másolatok adatainak megőrzése** lehetőséget választotta, akkor minden meglévő visszaállítási pontból vissza tud állítani.

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Mi történik, ha megszakítok egy folyamatban lévő visszaállítási feladatot?

Ha egy folyamatban lévő visszaállítási feladat meg lett szakítva, a visszaállítási folyamat leáll, és a megszakítás előtt visszaállított összes fájl megmarad a konfigurált célhelyen (eredeti vagy másodlagos helyen) a visszaállítások nélkül.

## <a name="manage-backup"></a>Biztonsági mentés kezelése

### <a name="can-i-use-powershell-to-configuremanagerestore-backups-of-azure-file-shares"></a>Használhatom a PowerShellt az Azure-fájlmegosztás biztonsági másolatainak konfigurálására/kezelésére/visszaállítására?

Igen. A részletes dokumentációt [itt](backup-azure-afs-automation.md) tekintheti meg

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-it"></a>Hozzáférhetek az Azure biztonsági mentések által készített pillanatképekhez és csatlakoztathatom azokat?

Az Azure Backup által készített minden pillanatkép elérhető a portál Pillanatképek megtekintése részén, illetve a PowerShell vagy a parancssori felület segítségével. Az Azure Files megosztási pillanatképeiről információkért lásd: [Az Azure Files megosztási pillanatképeinek áttekintése (előzetes verzió)](../storage/files/storage-snapshots-files.md).

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups"></a>Mi a biztonsági mentésekhez konfigurálható maximális adatmegőrzés?

Az Azure-fájlmegosztás biztonsági mentése lehetővé teszi, hogy a szabályzatokat 180 napig megtartva konfigurálja. A [PowerShell "igény szerinti biztonsági mentés" lehetőségének](backup-azure-afs-automation.md#trigger-an-on-demand-backup)használatával azonban akár 10 évig is megtarthatja a helyreállítási pontot.

### <a name="what-happens-when-i-change-the-backup-policy-for-an-azure-file-share"></a>Mi történik, ha módosítom az Azure-fájlmegosztáshoz tartozó biztonsági mentési szabályzatot?

Amikor új házirendet alkalmaznak a fájlmegosztáso(ko)n, az új szabályzat ütemezése és megőrzése érvényes. Ha növeli a megőrzési időtartamot, a meglévő helyreállítási pontok az új szabályzatnak megfelelően megmaradnak. Ha csökkenti a megőrzési időtartamot, a helyreállítási pontok a következő tisztítási feladat során törlendőként lesznek megjelölve.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Azure Backup egyéb területeiről, tekintse meg a következő biztonsági mentési gyakori kérdéseket:

- [Helyreállítási tár – GYIK](backup-azure-backup-faq.md)
- [Azure-beli virtuális gép biztonsági mentése – GYIK](backup-azure-vm-backup-faq.md)
- [Az Azure Backup ügynöke – GYIK](backup-azure-file-folder-backup-faq.md)
