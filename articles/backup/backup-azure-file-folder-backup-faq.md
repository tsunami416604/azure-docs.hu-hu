---
title: Gyakori kérdések biztonsági mentése a fájlok és mappák Azure Backup szolgáltatással
description: Oldja meg a fájlok és mappák Azure Backup szolgáltatással biztonsági mentésével kapcsolatos gyakori kérdésekre.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: dacurwin
ms.openlocfilehash: d4d1044a30d4ebc551cf1305993aba2a201c4c94
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514451"
---
# <a name="common-questions-about-backing-up-files-and-folders"></a>Fájlok és mappák biztonsági mentésével kapcsolatos gyakori kérdések

Ez a cikk gyakori kérdésekre adott válaszok abound biztonsági mentése a fájlok és mappák, a Microsoft Azure Recovery Services-(MARS) ügynökkel az rendelkezik a [Azure Backup](backup-overview.md) szolgáltatás.

## <a name="general"></a>Általános kérdések

## <a name="configure-backups"></a>Biztonsági mentések konfigurálása

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>Ahonnan letöltheti a MARS-ügynök legújabb verzióját?
A legújabb MARS-ügynök, amikor Windows Serveres gépek, a System Center DPM és a Microsoft Azure Backup server biztonsági mentésével érhető el az [letöltése](https://aka.ms/azurebackup_agent).

### <a name="how-long-are-vault-credentials-valid"></a>Mennyi ideig vannak a tároló hitelesítő adatainak érvényes?
A tároló hitelesítő adatai 48 óra után lejárnak. Ha lejár a hitelesítőadat-fájlja, töltse le a fájlt újra az Azure Portalról.

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>Milyen meghajtókról készíthetek biztonsági másolatot fájlokhoz és mappákhoz?

Nem lehet biztonsági másolatot készíteni a következő típusú meghajtókat és a kötetek:

* Cserélhető adathordozó: Az összes biztonsági másolati elem forrás kell jelentést rögzített.
* Csak olvasható kötetek: A köteten a kötet árnyékmásolata szolgáltatás (VSS) működéséhez írhatónak kell lennie.
* Offline kötetek: A kötet online-hoz a VSS függvénynek kell lennie.
* Hálózati megosztások: A kötet online biztonsági mentés használatával kell készíteni a kiszolgáló helyi kell lennie.
* A BitLocker által védett kötetek: A kötet zárolását kell, mielőtt a biztonsági mentés.
* Fájlrendszer azonosítása: NTFS csak a fájlrendszer támogatott.

### <a name="what-file-and-folder-types-are-supported"></a>Milyen fájl- és típusok támogatottak?

[További](backup-support-matrix-mars-agent.md#supported-file-types-for-backup) fájlok és mappák biztonsági mentésének támogatott típusaival kapcsolatban.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>A MARS-ügynök használatával fájlok és mappák biztonsági mentése az Azure virtuális gépen?  
Igen. Az Azure Backup Virtuálisgép-szintű biztonsági mentést biztosít az Azure virtuális gépek a VM-bővítmény használata az Azure-beli Virtuálisgép-ügynök. Ha azt szeretné, a fájlok és mappák biztonsági mentése a virtuális gép vendég Windows operációs rendszeren, ehhez a MARS-ügynök is telepítheti.

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>A MARS-ügynök használatával fájlok és mappák biztonsági mentése az ideiglenes tároló az Azure virtuális gép?
Igen. Telepítse a MARS-ügynök, és a fájlok és mappák biztonsági mentése a vendég Windows operációs rendszeren egy ideiglenes tárolóra.

- Biztonsági mentési feladat sikertelen, amikor az ideiglenes tároló adatait törli.
- Ha az ideiglenes tároló adatait törli, csak visszaállíthatja állandó tárolóba.

### <a name="how-do-i-register-a-server-to-another-region"></a>Hogyan regisztrálhatom a kiszolgáló egy másik régióban?

Biztonsági mentési adatokat a tároló, amelyben a kiszolgáló regisztrálva van az Adatközpont érkezik. Az Adatközpont módosításának legegyszerűbb módja eltávolítása, majd telepítse újra az ügynököt, majd pedig regisztrálhatja az új tárolóra a gép van szüksége a régióban.

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>A MARS-ügynök támogatás Windows Server 2012 adatdeduplikáció nem?
Igen. A MARS-ügynök átalakítja a deduplikált adatokat normális adatokká, amikor előkészíti a biztonsági mentési műveletet. Majd optimalizálja az adatokat a biztonsági mentéshez, titkosítja az adatokat és ezután elküldi a titkosított adatokat a tárolóba.

## <a name="manage-backups"></a>Biztonsági másolatok kezelése

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>Mi történik, ha átnevezek egy Windows gép biztonsági mentése konfigurálva?

Ha átnevez egy Windows-gépen, minden aktuálisan konfigurált biztonsági mentés leáll.

- Szeretne regisztrálni a számítógép új nevét a Backup-tárolóban.
- Ha regisztrálja az új nevet a tárolóval, az első művelet van egy *teljes* biztonsági mentés.
- Ha a régi kiszolgálónévvel a tárolóba mentett adatok helyreállítása van szüksége, használja a a lehetőséget az adat-helyreállító varázslóban egy másodlagos helyre történő helyreállításához. [További információk](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine).

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>Mi a fájl maximális elérési út hossza biztonsági mentés?
A MARS-ügynök az NTFS támaszkodik, és használja a fájl elérési útja hossz-specifikációját korlátozza a [Windows API](/windows/desktop/FileIO/naming-a-file#fully-qualified-vs-relative-paths). Ha a védeni kívánt fájlok hosszabb, mint az engedélyezett értéket, készítsen biztonsági másolatot a mappa vagy a lemezmeghajtóról készítse.  

### <a name="what-characters-are-allowed-in-file-paths"></a>Mely karakterek engedélyezettek a fájlok elérési útjában?

A MARS-ügynök az NTFS támaszkodik, és lehetővé teszi, hogy [támogatott karakterek](/windows/desktop/FileIO/naming-a-file#naming-conventions) a fájl neve/elérési utakat.

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>A "Az Azure Backup nem lett konfigurálva a kiszolgálón" figyelmeztetés jelenik meg.
Ez a figyelmeztetés annak ellenére, hogy konfigurálta a biztonsági mentési házirend, amikor a helyi kiszolgálón tárolt ütemezett biztonsági mentési beállítások nem ugyanaz, mint a biztonsági mentési tárolóban tárolt beállításokkal is megjelenhetnek.
- Ha a kiszolgáló vagy a beállítások egy korábbi hibátlan állapotra lettek visszaállítva, biztonsági mentési ütemezések nem szinkronizált válhat.
- Ha ezt a figyelmeztetést kap [konfigurálása](backup-azure-manage-windows-server.md) a újra a biztonsági mentési szabályzatot, majd a Futtatás egy igény szerinti biztonsági mentés az Azure-ral a helyi kiszolgáló újraszinkronizálása.


## <a name="manage-the-backup-cache-folder"></a>A biztonsági mentési gyorsítótár-mappa kezelése

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>Mi a gyorsítótármappa minimális méretkövetelménye?
A gyorsítótármappa mérete határozza meg azt az adatmennyiséget, amelyről biztonsági másolatot készít.
- A gyorsítótár-mappa kötetek kell rendelkeznie, amely egyenlő a biztonsági mentési adatok teljes mérete legalább 5 – 10 % szabad terület.
- Ha a kötet kisebb, mint 5 % szabad terület, növelje a kötet méretét, vagy helyezze át a gyorsítótármappa egy elegendő lemezterülettel rendelkező kötetre.
- Ha Windows rendszerállapotának biztonsági mentését, kell egy további 30 – 35 GB szabad hely a kötet a gyorsítótár mappáját tartalmazó
### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>Hogyan változtatható meg a MARS-ügynök a gyorsítótár helyét?


1. Egy rendszergazda jogú parancssort a Backup motort leállítani a következő parancs futtatásával:

    ```PS C:\> Net stop obengine```

2. Ne helyezze át a fájlokat. Ehelyett másolja a gyorsítótár mappáját egy másik, elegendő szabad hellyel rendelkező meghajtót.
3. Frissítse a következő beállításjegyzékbeli bejegyzéseket az új gyorsítótár-mappa elérési útját.<br/>

    | Beállításjegyzékbeli elérési út | Beállításjegyzék kulcsa | Érték |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Új gyorsítótár-mappa helye* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Új gyorsítótár-mappa helye* |

4. Indítsa újra a Backup motort egy rendszergazda jogú parancssorban:

    ```PS C:\> Net start obengine```

5. Sikeresen használják az új helyre a biztonsági mentés befejezése után eltávolíthatja az eredeti gyorsítótármappát.


### <a name="where-should-the-cache-folder-be-located"></a>Ha a gyorsítótár mappája kell lennie?

A következő helyek nem ajánlottak a gyorsítótármappa számára:

* Hálózati megosztás vagy cserélhető adathordozó: A gyorsítótár helyinek kell lennie az online biztonsági mentés használatával mentést igénylő kiszolgáló számára. A hálózati helyeket vagy cserélhető adathordozóra, például USB-meghajtók nem támogatottak.
* Offline kötetek: A gyorsítótármappa kell lennie az online biztonsági mentés várt Azure Backup szolgáltatás ügynökének használatával

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>Vannak-e olyan nem támogatottak a gyorsítótármappa attribútumai?
A következő attribútumok vagy kombinációik nem támogatottak a gyorsítótármappa számára:

* Titkosított
* Deduplikált
* Tömörített
* Ritka
* Újraelemzési pont

Sem a gyorsítótármappa, sem a metaadatok virtuális merevlemez-meghajtója nem rendelkezik a szükséges attribútumokkal az Azure Backup-ügynökhöz.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>Van mód a biztonsági mentéshez használt sávszélesség nagyságának?

Igen, használhatja a **tulajdonságainak módosítása** lehetőség a MARS-ügynök a sávszélességet és az időzítési beállításához. [További információk](backup-configure-vault.md#enable-network-throttling).

## <a name="restore"></a>Visszaállítás

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Mi történik, ha megszakítom egy folyamatban lévő visszaállítási feladat?

Ha egy folyamatban lévő visszaállítási feladat meg lett szakítva, a visszaállítási folyamat leáll. Minden fájl visszaállítva a törlés előtt konfigurált cél (eredeti helyére vagy máshová), anélkül, hogy bármely visszagörgetése legyen naprakész.


## <a name="next-steps"></a>További lépések

[Ismerje meg,](tutorial-backup-windows-server-to-azure.md) hogyan Windows gép biztonsági mentése.
