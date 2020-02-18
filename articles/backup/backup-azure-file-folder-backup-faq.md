---
title: Fájlok és mappák biztonsági mentése – gyakori kérdések
description: A fájlok és mappák Azure Backupsal történő biztonsági mentésével kapcsolatos gyakori kérdéseket tárgyalja.
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 7b80932d49038bb42fa93f71b3ac0194c2869489
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2020
ms.locfileid: "77425068"
---
# <a name="common-questions-about-backing-up-files-and-folders"></a>Fájlok és mappák biztonsági mentésével kapcsolatos gyakori kérdések

Ez a cikk a [Azure Backup](backup-overview.md) szolgáltatásban található Microsoft Azure Recovery Services-(MARS-) ügynökkel a fájlok és mappák biztonsági mentésével kapcsolatos gyakori kérdésekre ad választ.

## <a name="configure-backups"></a>Biztonsági mentések konfigurálása

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>Honnan tölthetők le a MARS-ügynök legújabb verziója?

A Windows Server-gépek, a System Center DPM és a Microsoft Azure Backup-kiszolgáló biztonsági mentésekor használt legújabb MARS-ügynök [letölthető](https://aka.ms/azurebackup_agent).

### <a name="how-long-are-vault-credentials-valid"></a>Meddig érvényes a tár hitelesítő adatai?

A tároló hitelesítő adatai 48 óra után lejárnak. Ha a hitelesítő adatok fájlja lejár, töltse le újra a fájlt a Azure Portalból.

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>Milyen meghajtókon tudok biztonsági másolatot készíteni a fájlokról és mappákról?

Nem lehet biztonsági másolatot készíteni a következő típusú meghajtókról és kötetekről:

* Cserélhető adathordozó: a biztonsági mentési elemek összes forrásának rögzítettként kell jelentenie.
* Írásvédett kötetek: a kötetnek írhatónak kell lennie ahhoz, hogy a Kötet árnyékmásolata szolgáltatás (VSS) működjön.
* Offline kötetek: a kötetnek online állapotban kell lennie a VSS működéséhez.
* Hálózati megosztások: a kötetnek helyinek kell lennie ahhoz a kiszolgálóhoz, amelyről biztonsági másolatot szeretne készíteni az online biztonsági mentés használatával.
* BitLocker által védett kötetek: a kötetet fel kell oldani a biztonsági mentés megkezdése előtt.
* Fájlrendszer azonosítása: Csak az NTFS fájlrendszer támogatott.

### <a name="what-file-and-folder-types-are-supported"></a>Milyen típusú fájlok és mappák támogatottak?

[További](backup-support-matrix-mars-agent.md#supported-file-types-for-backup) információ a biztonsági mentéshez támogatott fájlok és mappák típusairól.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>Használhatom a MARS-ügynököt egy Azure-beli virtuális gépen található fájlok és mappák biztonsági mentésére?  

Igen. A Azure Backup az Azure VM-ügynök virtuálisgép-bővítményével biztosítja az Azure virtuális gépek virtuálisgép-szintű biztonsági mentését. Ha a virtuális gépen lévő vendég Windows operációs rendszer fájljairól és mappáiról szeretne biztonsági másolatot készíteni, a MARS-ügynököt is telepítheti.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>Használhatom a MARS-ügynököt a fájlok és mappák biztonsági mentésére az Azure-beli virtuális gép ideiglenes tárolójában?

Igen. Telepítse a MARS-ügynököt, és biztonsági másolatot készíthet a vendég Windows operációs rendszer fájljairól és mappáiról az ideiglenes tárolóra.

* A biztonsági mentési feladatok meghiúsulnak, ha az ideiglenes tárolási adatok törlődnek.
* Ha törli az ideiglenes tárolási adatmennyiséget, csak a nem felejtő tárolóra állíthatja vissza.

### <a name="how-do-i-register-a-server-to-another-region"></a>Hogyan regisztrálni a kiszolgálót egy másik régióba?

A biztonsági mentési adatmennyiséget azon tároló adatközpontjában küldik el, amelyben a kiszolgáló regisztrálva van. Az adatközpont módosításának legegyszerűbb módja az ügynök eltávolítása és újratelepítése, majd a gép regisztrálása egy új tárba a szükséges régióban.

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>Támogatja a MARS-ügynök a Windows Server 2012 deduplikálása szolgáltatást?

Igen. A MARS-ügynök a deduplikált adatmennyiséget a normál adatértékre konvertálja, amikor előkészíti a biztonsági mentési műveletet. Ezután optimalizálja az adatokat a biztonsági mentéshez, titkosítja az adatokat, majd elküldi a titkosított adatokat a tárolónak.

## <a name="manage-backups"></a>Biztonsági másolatok kezelése

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>Mi történik, ha átnevezek egy biztonsági mentésre konfigurált Windows-gépet?

Windows rendszerű gép átnevezése esetén az összes jelenleg konfigurált biztonsági mentés leállt.

* Regisztrálnia kell az új gép nevét a Backup-tárolóban.
* Amikor regisztrálja az új nevet a tárolóban, az első művelet egy *teljes* biztonsági mentés.
* Ha a régi kiszolgálónévvel a tárolóba mentett adatokat kell helyreállítani, akkor a visszaállítás másik helyre lehetőségre kell kattintania az adatok helyreállítása varázslóban. [További információk](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>Mi a fájl elérési útjának maximális hossza a biztonsági mentéshez?

A MARS-ügynök NTFS-re támaszkodik, és a [Windows API](/windows/desktop/FileIO/naming-a-file#fully-qualified-vs-relative-paths)által korlátozott filepath-hossz-specifikációt használ. Ha a védelemmel ellátni kívánt fájlok hosszabbak az engedélyezett értéknél, biztonsági mentést készíthet a szülőmappa vagy a lemezmeghajtóról.  

### <a name="what-characters-are-allowed-in-file-paths"></a>Milyen karakterek engedélyezettek a fájlelérési utakban?

A MARS-ügynök NTFS-re támaszkodik, és lehetővé teszi a [támogatott karakterek](/windows/desktop/FileIO/naming-a-file#naming-conventions) használatát a fájlnevekben/elérési utakban.

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>Megjelenik az "Azure Backups nincsenek konfigurálva ehhez a kiszolgálóhoz" figyelmeztetés

Ez a figyelmeztetés akkor is megjelenhet, ha a biztonsági mentési szabályzatot konfigurálta, amikor a helyi kiszolgálón tárolt biztonsági mentési ütemterv beállításai nem egyeznek meg a Backup-tárolóban tárolt beállításokkal.

* Ha a kiszolgáló vagy a beállítások egy ismert jó állapotra lettek helyreállítva, a biztonsági mentési ütemtervek nem szinkronizálhatók.
* Ha ezt a figyelmeztetést kapja, [konfigurálja](backup-azure-manage-windows-server.md) újra a biztonsági mentési szabályzatot, majd futtasson egy igény szerinti biztonsági mentést, és szinkronizálja újra a helyi kiszolgálót az Azure-ban.

## <a name="manage-the-backup-cache-folder"></a>A biztonsági mentési gyorsítótár mappájának kezelése

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>Mi a gyorsítótármappa minimális méretkövetelménye?

A gyorsítótármappa mérete határozza meg azt az adatmennyiséget, amelyről biztonsági másolatot készít.

* A gyorsítótár-mappák köteteinek szabad területtel kell rendelkezniük, amely a biztonsági másolatok teljes méretének legalább 5-10%-ával egyenlő.
* Ha a kötet kevesebb, mint 5% szabad területtel rendelkezik, növelje a kötet méretét, vagy helyezze át a gyorsítótár mappát egy olyan kötetre, amely elegendő hellyel rendelkezik a következő [lépések végrehajtásával](#how-do-i-change-the-cache-location-for-the-mars-agent).
* A Windows rendszerállapotának biztonsági mentése esetén további 30-35 GB szabad terület szükséges a gyorsítótár mappát tartalmazó köteten.

### <a name="how-to-check-if-scratch-folder-is-valid-and-accessible"></a>Hogyan ellenőrizhető, hogy a kaparós mappa érvényes és elérhető-e?

1. Alapértelmezés szerint a Scratch mappa a következő helyen található: `\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
2. Győződjön meg arról, hogy a mappa helyének elérési útja megegyezik az alább látható beállításkulcs-bejegyzések értékeivel:

    | Beállításjegyzékbeli elérési út | Beállításjegyzék kulcsa | Érték |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Új gyorsítótár-mappa helye* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Új gyorsítótár-mappa helye* |

### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>Hogyan módosítja a MARS-ügynök gyorsítótárának helyét?

1. Futtassa ezt a parancsot egy rendszergazda jogú parancssorban a biztonsági mentési motor leállításához:

    ```Net stop obengine```
2. Ha beállította a rendszerállapot biztonsági mentését, nyissa meg a Lemezkezelés segédprogramot, és válassza le a (z) `"CBSSBVol_<ID>"`formátumú lemez (eke) t.
3. Alapértelmezés szerint a Scratch mappa a következő helyen található: `\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
4. Másolja a teljes `\Scratch` mappát egy másik, elegendő lemezterülettel rendelkező meghajtóra. Győződjön meg arról, hogy a tartalom másolása megtörtént, nincs áthelyezve.
5. Frissítse a következő beállításjegyzékbeli bejegyzéseket az újonnan áthelyezett Scratch mappa elérési útjával.

    | Beállításjegyzékbeli elérési út | Beállításjegyzék kulcsa | Érték |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Új mappa helye* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Új mappa helye* |

6. Indítsa újra a Backup motort egy rendszergazda jogú parancssorban:

    ```command
    Net stop obengine

    Net start obengine
    ```

7. Futtasson egy igény szerinti biztonsági mentést. Miután a biztonsági mentés sikeresen befejeződött az új hely használatával, eltávolíthatja az eredeti gyorsítótár mappáját.

### <a name="where-should-the-cache-folder-be-located"></a>Hol kell elhelyezni a gyorsítótár mappáját?

A gyorsítótár mappájának következő helyei nem ajánlottak:

* Hálózati megosztás/cserélhető adathordozó: a gyorsítótár mappájának helyinek kell lennie azon a kiszolgálón, amelynek biztonsági mentést kell végeznie az online biztonsági mentés használatával. A hálózati telephelyek vagy a cserélhető adathordozók, például az USB-meghajtók nem támogatottak.
* Offline kötetek: a gyorsítótár mappájának online állapotban kell lennie a várt biztonsági mentéshez Azure Backup ügynök használatával

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>Vannak olyan attribútumok, amelyek nem támogatottak a gyorsítótár mappájában?

A következő attribútumok vagy kombinációik nem támogatottak a gyorsítótármappa számára:

* Titkosított
* Deduplikált
* Tömörített
* Ritka
* Újraelemzési pont

A gyorsítótár mappája és a metaadatok virtuális merevlemeze nem rendelkezik a Azure Backup ügynökhöz szükséges attribútumokkal.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>Be lehet állítani a biztonsági mentéshez használt sávszélesség mennyiségét?

Igen, a MARS-ügynök **Tulajdonságok módosítása** beállításával állíthatja be a sávszélességet és az időzítést. [További információk](backup-configure-vault.md#enable-network-throttling).

## <a name="restore"></a>Visszaállítás

### <a name="manage"></a>Kezelés

**Visszaállíthatók, ha elfelejtettem a jelszavam?**
A Azure Backup ügynöknek szüksége van egy jelszóra (amelyet a regisztráció során megadott) a biztonsági másolatban tárolt információk visszafejtéséhez. Tekintse át az alábbi forgatókönyveket az elveszett hozzáférési kódok kezelési lehetőségeinek megismeréséhez:

| Eredeti gép <br> *(forrásoldali gép, ahol a biztonsági másolatok készültek)* | Passphrase | Elérhető beállítások |
| --- | --- | --- |
| Elérhető |Elveszett |Ha az eredeti gép (ahol a biztonsági másolatok készültek) elérhető, és továbbra is ugyanazon a Recovery Services-tárolóban van regisztrálva, akkor a következő [lépésekkel](https://docs.microsoft.com/azure/backup/backup-azure-manage-mars#re-generate-passphrase)újból létrehozhatja a jelszót.  |
| Elveszett |Elveszett |Nem lehet helyreállítani az adatokat, vagy az adatok nem érhetők el |

Vegye figyelembe a következő feltételeket:

* Ha eltávolítja és újra regisztrálja az ügynököt ugyanarra az eredeti gépre,
  * *Ugyanez a jelszó*, akkor a biztonsági másolatok adatait is visszaállíthatja.
  * *Más jelszó*, nem fogja tudni visszaállítani a mentett adatait.
* Ha az ügynököt egy *másik gépre* telepíti, amelyen a
  * *Ugyanaz a jelszó* (amelyet az eredeti gépen használ), majd visszaállíthatja a biztonsági másolatok adatait.
  * *Különböző hozzáférési kód*, nem fogja tudni visszaállítani a mentett adatait.
* Ha az eredeti gép sérült (megakadályozza a jelszó újragenerálását a MARS-konzolon keresztül), de visszaállíthatja vagy elérheti a MARS-ügynök által használt eredeti mappát, akkor előfordulhat, hogy vissza tudja állítani (Ha elfelejtette a jelszót). További segítségért forduljon az ügyfélszolgálathoz.

**Hogyan a helyreállítást, ha elvesztettem az eredeti gépet (a biztonsági mentések során)?**

Ha ugyanazzal a jelszóval rendelkezik (amelyet a regisztráció során megadott), akkor visszaállíthatja a biztonsági másolatban szereplő összes adathalmazt egy másik gépre. A visszaállítási lehetőségek megismeréséhez tekintse át az alábbi forgatókönyveket.

| Eredeti gép | Passphrase | Elérhető beállítások |
| --- | --- | --- |
| Elveszett |Elérhető |A MARS-ügynököt telepítheti és regisztrálhatja egy másik gépen ugyanazzal a jelszóval, amelyet az eredeti gép regisztrálása során adott meg. Válassza a **helyreállítás lehetőséget** > **egy másik helyet** a visszaállítás végrehajtásához. További információkért tekintse meg [ezt a cikket](https://docs.microsoft.com/azure/backup/backup-azure-restore-windows-server#use-instant-restore-to-restore-data-to-an-alternate-machine).
| Elveszett |Elveszett |Nem lehet helyreállítani az adatokat, vagy az adatok nem érhetők el |


### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Mi történik, ha megszakítok egy folyamatban lévő visszaállítási feladatot?

Ha egy folyamatban lévő visszaállítási feladat meg lett szakítva, a visszaállítási folyamat leáll. A lemondás előtt visszaállított összes fájl a megadott célhelyen (eredeti vagy másodlagos helyen), a visszaállítások nélkül.

### <a name="does-the-mars-agent-back-up-and-restore-acls-set-on-files-folders-and-volumes"></a>A MARS-ügynök biztonsági mentését és visszaállítását a fájlokra, mappákra és kötetekre beállított ACL-ek végzik?

* A MARS-ügynök biztonsági mentést készít a fájlokra, mappákra és kötetekre beállított ACL-listákról
* A kötet-visszaállítás helyreállítási lehetőségnél a MARS-ügynök lehetőséget biztosít az ACL-engedélyek visszaállítására a helyreállított fájlra vagy mappára vonatkozóan.
* Az egyes fájlok és mappák helyreállítási lehetőség esetén a MARS-ügynök ACL-engedélyekkel lesz visszaállítva (nincs lehetőség az ACL-visszaállítás kihagyására).

## <a name="next-steps"></a>Következő lépések

[Tudnivalók](tutorial-backup-windows-server-to-azure.md) a Windows rendszerű gépek biztonsági mentéséről.
