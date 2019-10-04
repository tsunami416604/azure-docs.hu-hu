---
title: Gyakori kérdések a fájlok és mappák biztonsági mentésekor Azure Backup
description: A fájlok és mappák Azure Backupsal történő biztonsági mentésével kapcsolatos gyakori kérdéseket tárgyalja.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: dacurwin
ms.openlocfilehash: 99f14b14e9149f79ae992834ae75bcb8fdc3c74b
ms.sourcegitcommit: 15f7b641a67f3d6cf4fb4b4c11eaee18cf335923
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68601987"
---
# <a name="common-questions-about-backing-up-files-and-folders"></a>Fájlok és mappák biztonsági mentésével kapcsolatos gyakori kérdések

Ez a cikk gyakori kérdésekre adott válaszokat tartalmaz a fájlok és mappák biztonsági mentésére a [Azure Backup](backup-overview.md) szolgáltatásban található Microsoft Azure Recovery Services-(MARS-) ügynökkel.

## <a name="general"></a>Általános

## <a name="configure-backups"></a>Biztonsági mentések konfigurálása

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>Honnan tölthetők le a MARS-ügynök legújabb verziója?
A Windows Server-gépek, a System Center DPM és a Microsoft Azure Backup-kiszolgáló biztonsági mentésekor használt legújabb MARS-ügynök [letölthető](https://aka.ms/azurebackup_agent).

### <a name="how-long-are-vault-credentials-valid"></a>Meddig érvényes a tár hitelesítő adatai?
A tároló hitelesítő adatai 48 óra után lejárnak. Ha a hitelesítő adatok fájlja lejár, töltse le újra a fájlt a Azure Portalból.

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>Milyen meghajtókon tudok biztonsági másolatot készíteni a fájlokról és mappákról?

Nem lehet biztonsági másolatot készíteni a következő típusú meghajtókról és kötetekről:

* Cserélhető adathordozó: Minden biztonságimásolat-tétel forrásának rögzítettként kell jelentenie.
* Írásvédett kötetek: A kötetnek írhatónak kell lennie ahhoz, hogy a Kötet árnyékmásolata szolgáltatás (VSS) működjön.
* Offline kötetek: A kötetnek online állapotban kell lennie a VSS működéséhez.
* Hálózati megosztások: A kötetnek helyinek kell lennie azon a kiszolgálón, amelyről biztonsági másolatot szeretne készíteni az online biztonsági mentés használatával.
* BitLocker által védett kötetek: A kötetet fel kell oldani a biztonsági mentés megkezdése előtt.
* Fájlrendszer azonosítása: Az NTFS az egyetlen támogatott fájlrendszer.

### <a name="what-file-and-folder-types-are-supported"></a>Milyen típusú fájlok és mappák támogatottak?

[További](backup-support-matrix-mars-agent.md#supported-file-types-for-backup) információ a biztonsági mentéshez támogatott fájlok és mappák típusairól.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>Használhatom a MARS-ügynököt egy Azure-beli virtuális gépen található fájlok és mappák biztonsági mentésére?  
Igen. A Azure Backup az Azure VM-ügynök virtuálisgép-bővítményével biztosítja az Azure virtuális gépek virtuálisgép-szintű biztonsági mentését. Ha a virtuális gépen lévő vendég Windows operációs rendszer fájljairól és mappáiról szeretne biztonsági másolatot készíteni, a MARS-ügynököt is telepítheti.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>Használhatom a MARS-ügynököt a fájlok és mappák biztonsági mentésére az Azure-beli virtuális gép ideiglenes tárolójában?
Igen. Telepítse a MARS-ügynököt, és biztonsági másolatot készíthet a vendég Windows operációs rendszer fájljairól és mappáiról az ideiglenes tárolóra.

- A biztonsági mentési feladatok meghiúsulnak, ha az ideiglenes tárolási adatok törlődnek.
- Ha törli az ideiglenes tárolási adatmennyiséget, csak a nem felejtő tárolóra állíthatja vissza.

### <a name="how-do-i-register-a-server-to-another-region"></a>Hogyan regisztrálni a kiszolgálót egy másik régióba?

A biztonsági mentési adatmennyiséget azon tároló adatközpontjában küldik el, amelyben a kiszolgáló regisztrálva van. Az adatközpont módosításának legegyszerűbb módja az ügynök eltávolítása és újratelepítése, majd a gép regisztrálása egy új tárba a szükséges régióban.

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>Támogatja a MARS-ügynök a Windows Server 2012 deduplikálása szolgáltatást?
Igen. A MARS-ügynök a deduplikált adatmennyiséget a normál adatértékre konvertálja, amikor előkészíti a biztonsági mentési műveletet. Ezután optimalizálja az adatokat a biztonsági mentéshez, titkosítja az adatokat, majd elküldi a titkosított adatokat a tárolónak.

## <a name="manage-backups"></a>Biztonsági másolatok kezelése

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>Mi történik, ha átnevezek egy biztonsági mentésre konfigurált Windows-gépet?

Windows rendszerű gép átnevezése esetén az összes jelenleg konfigurált biztonsági mentés leállt.

- Regisztrálnia kell az új gép nevét a Backup-tárolóban.
- Amikor regisztrálja az új nevet a tárolóban, az első művelet egy *teljes* biztonsági mentés.
- Ha a régi kiszolgálónévvel a tárolóba mentett adatokat kell helyreállítani, akkor a visszaállítás másik helyre lehetőségre kell kattintania az adatok helyreállítása varázslóban. [További információk](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>Mi a fájl elérési útjának maximális hossza a biztonsági mentéshez?
A MARS-ügynök NTFS-re támaszkodik, és a [Windows API](/windows/desktop/FileIO/naming-a-file#fully-qualified-vs-relative-paths)által korlátozott filepath-hossz-specifikációt használ. Ha a védelemmel ellátni kívánt fájlok hosszabbak az engedélyezett értéknél, biztonsági mentést készíthet a szülőmappa vagy a lemezmeghajtóról.  

### <a name="what-characters-are-allowed-in-file-paths"></a>Milyen karakterek engedélyezettek a fájlelérési utakban?

A MARS-ügynök NTFS-re támaszkodik, és lehetővé teszi a [támogatott karakterek](/windows/desktop/FileIO/naming-a-file#naming-conventions) használatát a fájlnevekben/elérési utakban.

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>Megjelenik az "Azure Backups nincsenek konfigurálva ehhez a kiszolgálóhoz" figyelmeztetés.
Ez a figyelmeztetés akkor is megjelenhet, ha a biztonsági mentési szabályzatot konfigurálta, amikor a helyi kiszolgálón tárolt biztonsági mentési ütemterv beállításai nem egyeznek meg a Backup-tárolóban tárolt beállításokkal.
- Ha a kiszolgáló vagy a beállítások egy ismert jó állapotra lettek helyreállítva, a biztonsági mentési ütemtervek nem szinkronizálhatók.
- Ha ezt a figyelmeztetést kapja, [konfigurálja](backup-azure-manage-windows-server.md) újra a biztonsági mentési szabályzatot, majd futtasson egy igény szerinti biztonsági mentést, és szinkronizálja újra a helyi kiszolgálót az Azure-ban.


## <a name="manage-the-backup-cache-folder"></a>A biztonsági mentési gyorsítótár mappájának kezelése

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>Mi a gyorsítótármappa minimális méretkövetelménye?
A gyorsítótármappa mérete határozza meg azt az adatmennyiséget, amelyről biztonsági másolatot készít.
- A gyorsítótár-mappák köteteinek szabad területtel kell rendelkezniük, amely a biztonsági másolatok teljes méretének legalább 5-10%-ával egyenlő.
- Ha a kötet kevesebb, mint 5% szabad területtel rendelkezik, növelje a kötet méretét, vagy helyezze át a gyorsítótár mappáját egy elegendő hellyel rendelkező kötetre.
- A Windows rendszerállapotának biztonsági mentése esetén további 30-35 GB szabad területre lesz szüksége a gyorsítótár mappát tartalmazó köteten.

### <a name="how-to-check-if-scratch-folder-is-valid-and-accessible"></a>Hogyan ellenőrizhető, hogy a kaparós mappa érvényes és elérhető-e?

1. Alapértelmezés szerint a Scratch mappa a következő helyen található:`\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
2. Győződjön meg arról, hogy a mappa helyének elérési útja megegyezik az alább látható beállításkulcs-bejegyzések értékeivel:

  | Beállításjegyzékbeli elérési út | Beállításjegyzék kulcsa | Value |
  | --- | --- | --- |
  | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Új gyorsítótár-mappa helye* |
  | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Új gyorsítótár-mappa helye* |

### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>Hogyan módosítja a MARS-ügynök gyorsítótárának helyét?

1. Futtassa ezt a parancsot egy rendszergazda jogú parancssorban a biztonsági mentési motor leállításához:

    ```PS C:\> Net stop obengine```

2. Ha beállította a rendszerállapot biztonsági mentését, nyissa meg a Lemezkezelés segédprogramot, és válassza le a (z `"CBSSBVol_<ID>"`) formátumú lemez (eke) t a következő formátumban:.
3. Ne helyezze át a fájlokat. Ehelyett másolja a gyorsítótár-terület mappáját egy olyan meghajtóra, amely elegendő lemezterülettel rendelkezik.
4. Frissítse a következő beállításjegyzékbeli bejegyzéseket az új gyorsítótár mappájának elérési útjával.<br/>

    | Beállításjegyzékbeli elérési út | Beállításjegyzék kulcsa | Value |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Új gyorsítótár-mappa helye* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Új gyorsítótár-mappa helye* |

5. Indítsa újra a Backup motort egy rendszergazda jogú parancssorban:

    ```PS C:\> Net stop obengine```

    ```PS C:\> Net start obengine```

6. Futtasson egy ad-hoc biztonsági mentést. Miután a biztonsági mentés sikeresen befejeződött az új hely használatával, eltávolíthatja az eredeti gyorsítótár mappáját.


### <a name="where-should-the-cache-folder-be-located"></a>Hol kell elhelyezni a gyorsítótár mappáját?

A következő helyek nem ajánlottak a gyorsítótármappa számára:

* Hálózati megosztás/cserélhető adathordozó: A gyorsítótár mappájának helyinek kell lennie az online biztonsági mentés használatával biztonsági mentést igénylő kiszolgáló számára. A hálózati telephelyek vagy a cserélhető adathordozók, például az USB-meghajtók nem támogatottak
* Offline kötetek: Azure Backup ügynök használatával a gyorsítótár mappájának online állapotban kell lennie a várt biztonsági mentéshez

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>Vannak olyan attribútumok, amelyek nem támogatottak a gyorsítótár mappájában?
A következő attribútumok vagy kombinációik nem támogatottak a gyorsítótármappa számára:

* Titkosítva
* Deduplikált
* Tömörített
* Ritka
* Újraelemzési pont

Sem a gyorsítótármappa, sem a metaadatok virtuális merevlemez-meghajtója nem rendelkezik a szükséges attribútumokkal az Azure Backup-ügynökhöz.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>Be lehet állítani a biztonsági mentéshez használt sávszélesség mennyiségét?

Igen, a MARS-ügynök **Tulajdonságok módosítása** beállításával állíthatja be a sávszélességet és az időzítést. [További információk](backup-configure-vault.md#enable-network-throttling).

## <a name="restore"></a>Visszaállítás

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Mi történik, ha megszakítok egy folyamatban lévő visszaállítási feladatot?

Ha egy folyamatban lévő visszaállítási feladat meg lett szakítva, a visszaállítási folyamat leáll. A lemondás előtt visszaállított összes fájl a megadott célhelyen (eredeti vagy másodlagos helyen), a visszaállítások nélkül.


## <a name="next-steps"></a>További lépések

[Tudnivalók](tutorial-backup-windows-server-to-azure.md) a Windows rendszerű gépek biztonsági mentéséről.
