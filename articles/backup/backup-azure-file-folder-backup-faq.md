---
title: Fájlok és mappák biztonsági mentése – gyakori kérdések
description: Kijavítottuk a fájlok és mappák Azure Backup segítségével való biztonsági mentésével kapcsolatos gyakori kérdéseket.
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: adcbf5c3b404de46634423f8f59c4798d44bebe0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273422"
---
# <a name="common-questions-about-backing-up-files-and-folders"></a>Gyakori kérdések a fájlok és mappák biztonsági mentésével kapcsolatban

Ez a cikk választ ad a fájlok és mappák biztonsági mentésére vonatkozó gyakori kérdésekre az [Azure Backup](backup-overview.md) szolgáltatás Microsoft Azure Recovery Services (MARS) ügynökével.

## <a name="configure-backups"></a>Biztonsági mentések konfigurálása

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>Hol tölthetem le a MARS-ügynök legújabb verzióját?

A Windows Server-gépek, a System Center DPM és a Microsoft Azure Backup kiszolgáló biztonsági mentésekor használt legújabb MARS-ügynök [letölthető.](https://aka.ms/azurebackup_agent)

### <a name="how-long-are-vault-credentials-valid"></a>Mennyi ideig érvényesek a tároló hitelesítő adatai?

A tároló hitelesítő adatai 48 óra után lejárnak. Ha a hitelesítő adatok fájl lejár, töltse le újra a fájlt az Azure Portalon.

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>Milyen meghajtókról tudok biztonsági másolatot csinálni a fájlokról és mappákról?

A következő típusú meghajtókról és kötetekről nem lehet biztonsági másolatot kapni:

* Cserélhető adathordozók: Minden biztonságimásolat-elemforrásnak rögzítettként kell jelentenie.
* Csak olvasható kötetek: A kötetnek írhatónak kell lennie ahhoz, hogy a kötet árnyékmásolata szolgáltatás (VSS) működjön.
* Kapcsolat nélküli kötetek: A kötetnek online állapotban kell lennie ahhoz, hogy a VSS működjön.
* Hálózati megosztások: A kötetnek helyinek kell lennie a kiszolgálón ahhoz, hogy az online biztonsági mentés használatával biztonsági mentéskészüljön.
* BitLocker-védelemmel ellátott kötetek: A kötetet fel kell oldani a biztonsági mentés előtt.
* Fájlrendszer azonosítása: Csak az NTFS fájlrendszer támogatott.

### <a name="what-file-and-folder-types-are-supported"></a>Milyen fájl- és mappatípusok támogatottak?

[További információ](backup-support-matrix-mars-agent.md#supported-file-types-for-backup) a biztonsági mentéshez támogatott fájlok és mappák típusairól.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>Használhatom a MARS-ügynök fájlok és mappák biztonsági másolatot egy Azure virtuális gép?  

Igen. Az Azure Backup virtuálisgép-szintű biztonsági mentést biztosít az Azure virtuális gépek számára az Azure virtuálisgép-ügynök virtuálisgép-bővítmény használatával. Ha a virtuális gép vendég Windows operációs rendszerén lévő fájlokról és mappákról szeretne biztonsági másolatot tenni, ehhez telepítheti a MARS-ügynököt.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>Használhatom a MARS-ügynök biztonsági másolatot fájlokról és mappákról az Azure virtuális gép ideiglenes tárolójában?

Igen. Telepítse a MARS-ügynököt, és biztonsági másolatot kell tartania a vendég Windows operációs rendszeren lévő fájlokról és mappákról az ideiglenes tárolóba.

* A biztonsági mentési feladatok sikertelenek, ha az ideiglenes tárolási adatokat törlik.
* Ha az ideiglenes tárolási adatok törlődnek, csak a nem felejtő tárolót állíthatja vissza.

### <a name="how-do-i-register-a-server-to-another-region"></a>Hogyan regisztrálhatok kiszolgálót egy másik régióba?

A biztonsági mentési adatokat a rendszer annak a tárolónak az adatközpontba küldi, amelyben a kiszolgáló regisztrálva van. Az adatközpont módosításának legegyszerűbb módja az ügynök eltávolítása és újratelepítése, majd a gép regisztrálása egy új tárolóba a szükséges régióban.

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>Támogatja a MARS-ügynök a Windows Server 2012 deduplikációját?

Igen. A MARS-ügynök a deduplikált adatokat normál adatokká alakítja a biztonsági mentési művelet előkészítésekor. Ezután optimalizálja az adatokat biztonsági mentésre, titkosítja az adatokat, majd elküldi a titkosított adatokat a tárolóba.

## <a name="manage-backups"></a>Biztonsági másolatok kezelése

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>Mi történik, ha átnevezek egy biztonsági mentésre konfigurált Windows-gépet?

Windows-gép átnevezésekénél az összes jelenleg konfigurált biztonsági mentés leáll.

* Regisztrálnia kell az új gépnevét a biztonsági mentési tárolóban.
* Amikor regisztrálja az új nevet a tárolóban, az első művelet egy *teljes* biztonsági mentés.
* Ha a régi kiszolgálónévvel kell helyreállítania a tárolóba biztonsági mentést készítő adatokat, használja a lehetőséget az Adatok helyreállítása varázsló egy másik helyére való visszaállításához. [További információ](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>Mekkora a biztonsági mentés maximális fájlelérési útja?

A MARS-ügynök az NTFS fájlrendszerre támaszkodik, és a [Windows API](/windows/desktop/FileIO/naming-a-file#fully-qualified-vs-relative-paths)által korlátozott fájlelérési út hosszspecifikációját használja. Ha a védeni kívánt fájlok hosszabbak az engedélyezett értéknél, biztonsági másolatot készíteni a szülőmappáról vagy a lemezmeghajtóról.  

### <a name="what-characters-are-allowed-in-file-paths"></a>Milyen karakterek engedélyezettek a fájlelérési utakban?

A MARS-ügynök az NTFS fájlrendszerre támaszkodik, és lehetővé teszi a [támogatott karaktereket](/windows/desktop/FileIO/naming-a-file#naming-conventions) a fájlnevekben/elérési utakban.

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>Megjelenik az "Azure biztonsági mentések nincs konfigurálva ehhez a kiszolgálóhoz" figyelmeztetés

Ez a figyelmeztetés akkor is megjelenhet, ha beállított egy biztonsági mentési házirendet, ha a helyi kiszolgálón tárolt biztonsági mentési ütemezési beállítások nem egyeznek meg a biztonsági mentési tárolóban tárolt beállításokkal.

* Ha a kiszolgáló vagy a beállítások helyreállnak egy ismert jó állapotban, a biztonsági mentési ütemezések szinkronizálatlanná válhatnak.
* Ha ezt a figyelmeztetést kapja, [konfigurálja újra](backup-azure-manage-windows-server.md) a biztonsági mentési szabályzatot, majd futtasson egy igény szerinti biztonsági mentést a helyi kiszolgáló és az Azure újraszinkronizálásához.

## <a name="manage-the-backup-cache-folder"></a>A biztonsági másolat gyorsítótármappájának kezelése

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>Mi a gyorsítótármappa minimális méretkövetelménye?

A gyorsítótármappa mérete határozza meg azt az adatmennyiséget, amelyről biztonsági másolatot készít.

* A gyorsítótármappa köteteinek olyan szabad területtel kell rendelkezniük, amely a biztonsági mentési adatok teljes méretének legalább 5–10%-ának felel meg.
* Ha a köteten kevesebb, mint 5%-os szabad terület van, növelje a kötet méretét, vagy helyezze át a gyorsítótármappát egy elegendő hellyel rendelkező kötetre [az alábbi lépések](#how-do-i-change-the-cache-location-for-the-mars-agent)végrehajtásával.
* Ha biztonsági másolatot készít a Windows rendszerállapotáról, további 30–35 GB szabad területre van szükség a gyorsítótármappát tartalmazó köteten.

### <a name="how-to-check-if-scratch-folder-is-valid-and-accessible"></a>Hogyan lehet ellenőrizni, hogy a scratch mappa érvényes és hozzáférhető?

1. Alapértelmezés szerint a semmiből álló mappa a`\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
2. Győződjön meg arról, hogy a kaparós sorsjegy elérési útja megegyezik az alábbi rendszerleíró kulcs bejegyzéseinek értékeivel:

    | Beállításjegyzékbeli elérési út | Beállításjegyzék kulcsa | Érték |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Új gyorsítótár-mappa helye* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Új gyorsítótár-mappa helye* |

### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>Hogyan módosíthatom a MARS-ügynök gyorsítótárának helyét?

1. Futtassa ezt a parancsot egy rendszergazda jogú parancssorban a Biztonsági másolat motor leállításához:

    ```Net stop obengine```
2. Ha konfigurálta a Rendszerállapot biztonsági mentést, nyissa meg a Lemezkezelés segédprogramot, és bontsa le a lemez(eke)t a formátumú nevekkel. `"CBSSBVol_<ID>"`
3. Alapértelmezés szerint a semmiből szóló mappa a`\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
4. Másolja a `\Scratch` teljes mappát egy másik meghajtóra, amely elegendő helyet foglal. Győződjön meg arról, hogy a tartalom másolása, nem áthelyezése.
5. Frissítse a következő rendszerleíró bejegyzéseket az újonnan áthelyezett kaparós sorsmappa elérési útjával.

    | Beállításjegyzékbeli elérési út | Beállításjegyzék kulcsa | Érték |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Új scratch mappa helye* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Új scratch mappa helye* |

6. Indítsa újra a Biztonsági másolat motort emelt szintű parancssorban:

    ```command
    Net stop obengine

    Net start obengine
    ```

7. Igény szerinti biztonsági mentés futtatása. Miután a biztonsági mentés sikeresen befejeződött az új hely használatával, eltávolíthatja az eredeti gyorsítótármappát.

### <a name="where-should-the-cache-folder-be-located"></a>Hol található a gyorsítótármappa?

A gyorsítótármappa következő helyei nem ajánlottak:

* Hálózati megosztás/cserélhető adathordozó: A gyorsítótármappának az online biztonsági másolat tal biztonsági mentést igényelő kiszolgáló helyi kiszolgálójának kell lennie. A hálózati helyek vagy a cserélhető adathordozók, például az USB-meghajtók nem támogatottak.
* Kapcsolat nélküli kötetek: A gyorsítótármappának online állapotban kell lennie a várt biztonsági mentéshez az Azure Backup Agent használatával

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>Vannak olyan attribútumok a gyorsítótármappában, amelyek nem támogatottak?

A következő attribútumok vagy kombinációik nem támogatottak a gyorsítótármappa számára:

* Titkosított
* Deduplikált
* Tömörített
* Ritka
* Újraelemzési pont

A gyorsítótár-mappa és a metaadat-virtuális merevlemez nem rendelkezik az Azure Backup-ügynök höz szükséges attribútumokkal.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>Van mód a biztonsági mentéshez használt sávszélesség beállítására?

Igen, a MARS-ügynök **Tulajdonságok módosítása** beállításával módosíthatja a sávszélességet és az időzítést. [További információ](backup-windows-with-mars-agent.md#enable-network-throttling).

## <a name="restore"></a>Visszaállítás

### <a name="manage"></a>Kezelés

**Helyreállhatok, ha elfelejtettem a jelmondatomat?**
Az Azure Backup ügynök megköveteli a jelszót (amely a regisztráció során megadott) a biztonsági másolatot készíteni az adatok visszaállítása során. Tekintse át az alábbi forgatókönyveket, hogy megismerjék az elveszett jelszó kezelésének lehetőségeit:

| Eredeti gép <br> *(forrásgép, ahol biztonsági másolatot készítettek)* | Jelszót | Elérhető beállítások |
| --- | --- | --- |
| Elérhető |Elveszett |Ha az eredeti gép (ahol a biztonsági mentések készültek) elérhető, és továbbra is regisztrálva van ugyanazzal a Recovery Services-tárolóval, akkor az alábbi [lépések](https://docs.microsoft.com/azure/backup/backup-azure-manage-mars#re-generate-passphrase)végrehajtásával újragenerálhatja a jelszót.  |
| Elveszett |Elveszett |Nem lehet helyreállítani az adatokat, vagy az adatok nem állnak rendelkezésre |

Vegye figyelembe a következő feltételeket:

* Ha eltávolítja és újra regisztrálja az ügynököt ugyanazon az eredeti
  * *Ugyanaz a jelszó*, akkor képes lesz arra, hogy visszaállítsa a biztonsági másolatot az adatokat.
  * *A különböző jelszó,* majd nem tudja visszaállítani a biztonsági másolatot.
* Ha az ügynököt egy *másik számítógépre* telepíti,
  * *Ugyanaz a jelszó* (az eredeti gépben használatos), akkor visszaállíthatja a biztonsági másolatot.
  * *Eltérő jelszó ,* nem lesz képes visszaállítani a biztonsági másolatot.
* Ha az eredeti gép sérült (megakadályozza, hogy a marsi konzolon keresztül regenerálja a jelszót), de visszaállíthatja vagy elérheti a MARS-ügynök által használt eredeti kaparós sorsjegymappát, akkor visszaállíthatja (ha elfelejtette a jelszót). További segítségért forduljon az ügyfélszolgálathoz.

**Hogyan állíthatók vissza, ha elvesztettem az eredeti gépemet (ahol biztonsági mentéskészült)?**

Ha az eredeti gépnek ugyanaz a jelmondata (amelyet a regisztráció során megadott), akkor visszaállíthatja a biztonsági másolatot egy másik gépre. Tekintse át az alábbi forgatókönyveket a visszaállítási lehetőségek megértéséhez.

| Eredeti gép | Jelszót | Elérhető beállítások |
| --- | --- | --- |
| Elveszett |Elérhető |A MARS-ügynököt egy másik gépre is telepítheti és regisztrálhatja ugyanazzal a jelszóval, amelyet az eredeti gép regisztrációja során megadott. Válassza **a Helyreállítási lehetőség** > lehetőséget: A visszaállítás végrehajtásához válassza a**másik helyet.** További információkért tekintse meg [ezt a cikket](https://docs.microsoft.com/azure/backup/backup-azure-restore-windows-server#use-instant-restore-to-restore-data-to-an-alternate-machine).
| Elveszett |Elveszett |Nem lehet helyreállítani az adatokat, vagy az adatok nem állnak rendelkezésre |


### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Mi történik, ha megszakítok egy folyamatban lévő visszaállítási feladatot?

Ha egy folyamatban lévő visszaállítási feladat megszakad, a visszaállítási folyamat leáll. A megszakítás előtt visszaállított összes fájl a beállított célhelyen (eredeti vagy alternatív helyen) marad, visszaállítás nélkül.

### <a name="does-the-mars-agent-back-up-and-restore-acls-set-on-files-folders-and-volumes"></a>A MARS-ügynök biztonsági másolatot ad és visszaállítja a fájlokra, mappákra és kötetekre beállított ACL-eket?

* A MARS-ügynök biztonsági másolatot ad a fájlokon, mappákon és köteteken beállított ACL-król
* A Kötet-visszaállítás helyreállítási beállítás esetén a MARS-ügynök lehetőséget biztosít az ACL-engedélyek visszaállításának kihagyására a helyreállított fájlhoz vagy mappához.
* Az egyes fájl- és mappahelyreállítási lehetőségek esetében a MARS-ügynök acl-engedélyekkel visszaállítja a visszaadást (az ACL-visszaállítást nem lehet kihagyni).

## <a name="next-steps"></a>További lépések

[További információ](tutorial-backup-windows-server-to-azure.md) a Windows-gépek biztonsági és biztonsági biztonsági rendszerének biztonsági rendszerről.
