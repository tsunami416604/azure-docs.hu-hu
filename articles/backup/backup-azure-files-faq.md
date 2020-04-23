---
title: 'GYIK: Azure Files biztonsági mentése'
description: Ebből a cikkből megismerheti az Azure-fájlmegosztás Azure Backup szolgáltatással való védelemmel kapcsolatos gyakori kérdésekre adott válaszokat.
ms.date: 04/22/2020
ms.topic: conceptual
ms.openlocfilehash: d7b19fd11e6784a188a18f6a613eef5ff4f77764
ms.sourcegitcommit: 354a302d67a499c36c11cca99cce79a257fe44b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82105641"
---
# <a name="questions-about-backing-up-azure-files"></a>Kérdések az Azure Files biztonsági mentéséről

Ez a cikk az Azure Files biztonsági mentésével kapcsolatos általános kérdéseket válaszol meg. Egyes válaszokban részletes információkat tartalmazó cikkekre mutató hivatkozások találhatók. Emellett egy fórumbejegyzésben is feltehet kérdéseket az Azure Backup szolgáltatással kapcsolatban a [vitafórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

A cikk szakaszainak gyors áttekintéséhez használja **A cikk tartalma** terület hivatkozásait a jobb oldalon.

## <a name="configuring-the-backup-job-for-azure-files"></a>Biztonsági mentési feladat konfigurálása Azure Fileshoz

### <a name="why-cant-i-see-some-of-my-storage-accounts-that-i-want-to-protect-which-contain-valid-azure-file-shares"></a>Miért nem látok a védelemmel ellátni kívánt tárterület-fiókokat, amelyek érvényes Azure-fájlmegosztást tartalmaznak?

Tekintse át az [Azure-fájlmegosztás támogatási mátrixát](azure-file-share-support-matrix.md) , és győződjön meg arról, hogy a Storage-fiók a támogatott Storage-fiókok egyikéhez tartozik. Az is előfordulhat, hogy a keresett Storage-fiók már védett, vagy egy másik tárolóban van regisztrálva. [Szüntesse meg a Storage-fiók regisztrációját](manage-afs-backup.md#unregister-a-storage-account) a tárolóból, hogy felderítse a Storage-fiókot a többi tárolóban a védelem érdekében.

### <a name="why-cant-i-see-some-of-my-azure-file-shares-in-the-storage-account-when-im-trying-to-configure-backup"></a>Miért nem látom néhány Azure-fájlmegosztásomat a tárfiókban, miközben a biztonsági mentést próbálom konfigurálni?

Ellenőrizze, hogy az Azure-fájlmegosztás már védve van-e ugyanabban a helyreállítási tárban, vagy nemrégiben lett-e törölve.

### <a name="can-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync"></a>Meg tudom védeni az Azure Files Sync szinkronizált csoportjához csatlakoztatott fájlmegosztásaimat?

Igen. A szinkronizálási csoportokhoz csatlakozó Azure-fájlmegosztás védelme engedélyezve van.

### <a name="when-trying-to-back-up-file-shares-i-clicked-on-a-storage-account-for-discovering-the-file-shares-in-it-however-i-didnt-protect-them-how-do-i-protect-these-file-shares-with-any-other-vault"></a>Amikor fájlmegosztásokról próbáltam biztonsági másolatot készíteni, egy Storage-fiókra kattintottam a benne lévő fájlmegosztások felderítése érdekében. Azonban nem védik őket. Hogyan a fájlmegosztást bármilyen más tárolóval?

Amikor megkísérli a biztonsági mentést, válassza ki azt a Storage-fiókot, amellyel a fájlmegosztást fel szeretné deríteni, regisztrálja a Storage-fiókot azzal a tárolóval, amelyről ez történik. Ha úgy dönt, hogy egy másik tárolóval gondoskodik a fájlmegosztás megosztásáról, akkor szüntesse meg a kiválasztott Storage-fiók [regisztrációját](manage-afs-backup.md#unregister-a-storage-account) ebből a tárból.

### <a name="can-i-change-the-vault-to-which-i-back-up-my-file-shares"></a>Módosíthatom azt a tárolót, amelyre biztonsági másolatot készítek a fájlmegosztást?

Igen. Azonban [le kell állítania a fájlmegosztás védelmét](manage-afs-backup.md#stop-protection-on-a-file-share) a csatlakoztatott tárolóból, törölni kell a Storage-fiók [regisztrációját](manage-afs-backup.md#unregister-a-storage-account) , majd egy másik tárból kell védelemmel ellátnia.

### <a name="how-many-azure-file-shares-can-i-protect-in-a-vault"></a>Hány Azure-fájlmegosztást védhetek meg egy tárban?

Az Azure-fájlmegosztás akár 50 Storage-fiókkal is védhető. Ezenkívül egyetlen tárban akár 200 Azure-fájlmegosztást is megóvhat.

### <a name="can-i-protect-two-different-file-shares-from-the-same-storage-account-to-different-vaults"></a>Biztosíthatok védelmet két különböző fájlmegosztás számára ugyanabból a Storage-fiókból különböző tárolókba?

Nem. A Storage-fiókban lévő összes fájlmegosztás számára csak ugyanazzal a tárolóval biztosítható védelem.

## <a name="backup"></a>Backup

### <a name="what-should-i-do-if-my-backups-start-failing-due-to-the-maximum-limit-reached-error"></a>Mi a teendő, ha a biztonsági mentések sikertelenek, mert a maximális korlát elérte a hibát?

Egy tetszőleges időpontban legfeljebb 200 pillanatkép készíthető fájlmegosztásonként. A korlátba a szabályzatban meghatározottak szerint beletartoznak az Azure Backup által készített pillanatképek is. Ha a biztonsági mentések a korlát elérése után sikertelenek lesznek, törölje az igény szerinti pillanatképeket a sikeres jövőbeli biztonsági mentésekhez.

## <a name="restore"></a>Visszaállítás

### <a name="can-i-recover-from-a-deleted-azure-file-share"></a>Tudok helyreállítani törölt Azure-fájlmegosztásból?

Ha töröl egy Azure-fájlmegosztást, megjelenik a törölni kívánt biztonsági másolatok listája, és a rendszer megerősítést kér. Jelenleg egy törölt Azure-fájlmegosztás nem állítható vissza.

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-an-azure-file-share"></a>Vissza tudok állítani biztonsági mentésekből, ha leállítottam az Azure-fájlmegosztás védelmét?

Igen. Ha a védelem leállításakor a **Biztonsági másolatok adatainak megőrzése** lehetőséget választotta, akkor minden meglévő visszaállítási pontból vissza tud állítani.

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Mi történik, ha megszakítok egy folyamatban lévő visszaállítási feladatot?

Ha egy folyamatban lévő visszaállítási feladat meg lett szakítva, a visszaállítási folyamat leáll, és a megszakítás előtt visszaállított összes fájl megmarad a konfigurált célhelyen (eredeti vagy másodlagos helyen) a visszaállítások nélkül.

## <a name="manage-backup"></a>Biztonsági mentés kezelése

### <a name="can-i-use-powershell-to-configuremanagerestore-backups-of-azure-file-shares"></a>Használhatom a PowerShellt az Azure-fájlmegosztás biztonsági másolatainak konfigurálására/kezelésére/visszaállítására?

Igen. A részletes dokumentációt [itt](backup-azure-afs-automation.md)találja.

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-them"></a>Hozzáférhetek az Azure Backups által készített pillanatképekhez, és csatlakoztatják őket?

A Azure Backup által készített Pillanatképek a portálon, a PowerShellben vagy a CLI-ben is elérhetők. Ha többet szeretne megtudni a Azure Files-megosztási pillanatképekről, tekintse meg a [Azure Files megosztási Pillanatképek áttekintése](../storage/files/storage-snapshots-files.md)című témakört.

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups"></a>Mi a biztonsági mentésekhez konfigurálható maximális adatmegőrzés?

A maximális megőrzéssel kapcsolatos részletekért tekintse meg a [támogatási mátrixot](azure-file-share-support-matrix.md) . Azure Backup valós idejű számítást végez a pillanatképek számának megadásával, amikor a biztonsági mentési szabályzatot konfigurálja a megőrzési értékekhez. Amint a megadott adatmegőrzési értékeknek megfelelő Pillanatképek száma meghaladja az 200-as értéket, a portál egy figyelmeztetést jelenít meg, amely a megőrzési értékek módosítására kéri. Így nem lépi túl a Azure Files által támogatott Pillanatképek maximális számát az adott időpontban.

### <a name="what-happens-when-i-change-the-backup-policy-for-an-azure-file-share"></a>Mi történik, ha módosítom az Azure-fájlmegosztáshoz tartozó biztonsági mentési szabályzatot?

Amikor új házirendet alkalmaznak a fájlmegosztáso(ko)n, az új szabályzat ütemezése és megőrzése érvényes. Ha növeli a megőrzési időtartamot, a meglévő helyreállítási pontok az új szabályzatnak megfelelően megmaradnak. Ha csökkenti a megőrzési időtartamot, a helyreállítási pontok a következő tisztítási feladat során törlendőként lesznek megjelölve.

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Azure Backup egyéb területeiről, tekintse meg a következő biztonsági mentési gyakori kérdéseket:

- [Helyreállítási tár – GYIK](backup-azure-backup-faq.md)
- [Azure-beli virtuális gép biztonsági mentése – GYIK](backup-azure-vm-backup-faq.md)
- [Az Azure Backup ügynöke – GYIK](backup-azure-file-folder-backup-faq.md)
