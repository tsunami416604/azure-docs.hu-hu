---
title: Az Azure Data Box Disk hibaelhárítási adatmásolási problémákat| Microsoft dokumentumok
description: Bemutatja, hogyan hárítsa el az Azure Data Box Disk adatmásolása során a naplók használatával észlelt problémákat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/13/2019
ms.author: alkohli
ms.openlocfilehash: 760f5c6c929aa082993683d7a466a71c6484289a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67148347"
---
# <a name="troubleshoot-data-copy-issues-in-azure-data-box-disk"></a>Adatmásolási problémák elhárítása az Azure Data Box Disk-ben

Ez a cikk a Microsoft Azure Data Box Disk lemezre vonatkozik, és ismerteti, hogyan háríthatók el az adatok lemezekre másolásakor felmerülő problémák. A cikk az osztott másolási eszköz használatakor is foglalkozik problémákkal.


## <a name="data-copy-issues-when-using-a-linux-system"></a>Adatmásolási problémák Linux rendszer használata esetén

Ez a szakasz részletezi a linuxos ügyfél lemezekre másolásakor felmerülő legfontosabb problémákat.

### <a name="issue-drive-getting-mounted-as-read-only"></a>Probléma: A meghajtó írásvédettként való csatlakoztatása
 
**Okoz** 

Ez lehet az oka, hogy egy tisztátalan fájlrendszer.

A meghajtó újracsatlakoztatása olvasási és írási műveletekként nem működik a Data Box Disks segítségével. Ez a forgatókönyv nem támogatott a dislocker által visszafejtett meghajtók. Lehet, hogy sikeresen újracsatlakoztatta az eszközt a következő paranccsal:

    `# mount -o remount, rw /mnt/DataBoxDisk/mountVol1`

Bár az újracsatlakoztatás sikeres volt, az adatok nem maradnak meg.

**Resolution** (Osztás)

Tegye a következő lépéseket a Linux rendszeren:

1. Telepítse `ntfsprogs` az ntfsfix segédprogram csomagját.
2. A feloldó eszköz lekell választania a meghajtóhoz biztosított csatlakoztatási pontokat. A csatlakoztatási pontok száma a meghajtók esetében eltérő lehet.

    ```
    unmount /mnt/DataBoxDisk/mountVol1
    ```

3. Fuss `ntfsfix` on the megfelelő elérési útját. A kiemelt számnak meg kell egyeznie a 2.

    ```
    ntfsfix /mnt/DataBoxDisk/bitlockerVol1/dislocker-file
    ```

4. A következő paranccsal távolítsa el a hibernálási metaadatokat, amelyek a csatlakoztatási problémát okozhatják.

    ```
    ntfs-3g -o remove_hiberfile /mnt/DataBoxDisk/bitlockerVol1/dislocker-file /mnt/DataBoxDisk/mountVol1
    ```

5. Csinálj egy tiszta leválasztás.

    ```
    ./DataBoxDiskUnlock_x86_64 /unmount
    ```

6. Csinál egy tiszta kinyit és felmászik.
7. Tesztelje a csatlakoztatási pontot egy fájl írásával.
8. A fájlmegőrzés érvényesítéséhez lekell választani és újra kell csatlakoztatni.
9. Folytassa az adatmásolással.
 
### <a name="issue-error-with-data-not-persisting-after-copy"></a>Probléma: Hiba, ha az adatok másolás után nem maradnak meg
 
**Okoz** 

Ha azt látja, hogy a meghajtó nem tartalmaz adatokat a leválasztás után (bár az adatokat átmásolták), akkor lehetséges, hogy a meghajtóírás-írási csatlakoztatás után a meghajtót írásvédettként újracsatlakoztatta.

**Resolution** (Osztás)
 
Ebben az esetben tekintse meg az [írásvédettként csatlakoztatott meghajtók](#issue-drive-getting-mounted-as-read-only)felbontását.

Ha nem ez volt a helyzet, másolja a naplókat a Data Box Disk Unlock eszközt tartalmazó mappából, és lépjen kapcsolatba a [Microsoft támogatási szolgálatával.](data-box-disk-contact-microsoft-support.md)


## <a name="data-box-disk-split-copy-tool-errors"></a>A Data Box Disk másolásfelosztó eszközének hibái

Az adatok több lemezre történő felosztása korlátható problémákat az alábbi táblázat foglalja össze.

|Hibaüzenet/figyelmeztetések |Javaslatok |
|---------|---------|
|- Nem, nem, nem, nem, nem, BitLocker jelszó beolvasása kötethez: m <br>[Hiba] Kivétel a BitLocker kulcs beolvasása közben m kötethez:<br> A szekvencia nem tartalmaz elemeket.|Ez a hibaüzenet azt jelzi, hogy a Data Box Disk célhelye offline állapotú. <br> A lemezek online állapotúvá tételéhez használja a `diskmgmt.msc` eszközt.|
|[Hiba] A következő kivétel történt: A WMI-művelet sikertelen volt:<br> Method=UnlockWithNumericalPassword, ReturnValue=2150694965, <br>Win32Message=A megadott helyreállítási jelszó formátuma érvénytelen. <br>A Bitlocker helyreállítási jelszavai 48 számjegyűek. <br>Ellenőrizze a helyreállítási jelszó formátumát, majd próbálkozzon újra.|Először oldja fel a lemezek zárolását a Data Box Disk lemezzárolás-feloldó eszközével, majd próbálja újból végrehajtani a parancsot. További információért lásd: <li> [a Data Box Disk lemezzárolás-feloldó eszközének Windows-ügyfélen való használatát ismertető részt](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client). </li><li> [a Data Box Disk lemezzárolás-feloldó eszközének Linux-ügyfélen való használatát ismertető részt](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client). </li>|
|[Hiba] A következő kivétel történt: A DriveManifest.xml fájl megtalálható a célmeghajtón. <br> Ez azt jelezheti, hogy a célmeghajtót más naplófájllal készítették elő. <br>Ha további adatokat szeretne hozzáadni ugyanahhoz a meghajtóhoz, használja az előző naplófájlt. Meglévő adatok törléséhez és a célmeghajtó újbóli felhasználásához törölje a *DriveManifest.xml fájlt* a meghajtón. Futtassa újra ezt a parancsot egy új naplófájllal.| Ez a hiba akkor fordulhat elő, ha több importálási munkamenethez is ugyanazt a meghajtókészletet próbálja használni. <br> Egy adott meghajtókészletet csak egyetlen másolásfelosztási munkamenethez használjon.|
|[Hiba] A következő kivétel történt: A CopySessionId importdata-sept-test-1 egy korábbi másolási munkamenetre hivatkozik, és nem használható fel újra egy új másolási munkamenethez.|Ez a hiba akkor fordulhat elő, ha egy új feladat neveként egy korábban már sikeresen befejeződött feladat nevét próbálja megadni.<br> Egyedi nevet adjon meg az új feladat számára.|
|[Információ] A célfájl vagy -könyvtár neve túllépte az NTFS fájlrendszerre érvényes hosszkorlátot. |Ezt az üzenetet akkor kapja, ha át kellett nevezni a célfájlt, mert túl hosszú volt a fájl elérési útja.<br> Ezt a viselkedést a `config.json` fájl diszponálási beállításánál módosíthatja.|
|[Hiba] A következő kivétel történt: Hibás JSON-feloldókarakter. |Ezt az üzenetet akkor kapja, ha a config.json fájl érvénytelen formátumot használ. <br> A fájl mentése előtt ellenőrizze a `config.json` fájlt a [JSONlint](https://jsonlint.com/) eszközzel.|


## <a name="next-steps"></a>További lépések

- További információ az [érvényesítési eszközökkel kapcsolatos problémák elhárításáról.](data-box-disk-troubleshoot.md)
