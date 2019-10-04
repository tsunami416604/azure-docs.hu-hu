---
title: Az Azure Data Box-lemezek hibaelhárítási adatokat másolni a problémák |} A Microsoft Docs
description: Ismerteti, hogyan lehet Azure Data Box-lemezek naplók segítségével az adatok másolása során problémáinak elhárítása.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/13/2019
ms.author: alkohli
ms.openlocfilehash: 760f5c6c929aa082993683d7a466a71c6484289a
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148347"
---
# <a name="troubleshoot-data-copy-issues-in-azure-data-box-disk"></a>Az Azure Data Box-lemezek adatok másolási hibáinak elhárítása

Ez a cikk azt ismerteti, hogyan jelenik meg az adatok másolásának lemezekre esetleges problémák megoldásához, és a Microsoft Azure Data Box-lemezek vonatkozik. A cikk emellett ismerteti problémák, a felosztás másolás eszköz használatakor.


## <a name="data-copy-issues-when-using-a-linux-system"></a>Adatok másolása problémák Linux rendszer használata esetén

Ez a szakasz részletesen az egyes lemezek másolhat adatokat egy Linux-ügyfél használata során szembesülnek kapcsolatos leggyakoribb hibák.

### <a name="issue-drive-getting-mounted-as-read-only"></a>Probléma: Írásvédett meghajtóként első csatlakoztatott meghajtó
 
**OK** 

Ezt okozhatja egy kernelverziója fájlrendszer.

Data Box-lemezek írási-olvasási egy meghajtón szolgáltatással nem működik. Ez a forgatókönyv nem támogatott meghajtókkal dislocker visszafejteni. Előfordulhat, hogy rendelkezik sikerült csatlakoztatni az eszköz a következő paranccsal:

    `# mount -o remount, rw /mnt/DataBoxDisk/mountVol1`

Bár a szolgáltatással sikeres volt, az adatok nem megmaradnak.

**Felbontás**

Az alábbi lépéseket a Linux rendszeren:

1. Telepítse a `ntfsprogs` a ntfsfix segédprogram-csomagja.
2. Válassza le a csatlakoztatási pontok, a meghajtó a feloldás eszköz által biztosított. A csatlakoztatási pontok száma változhat meghajtókhoz.

    ```
    unmount /mnt/DataBoxDisk/mountVol1
    ```

3. Futtatás `ntfsfix` a megfelelő elérési úton. A kiemelt számot kell megadni, 2. lépés.

    ```
    ntfsfix /mnt/DataBoxDisk/bitlockerVol1/dislocker-file
    ```

4. Futtassa a következő paranccsal távolítható el a hibernálási metaadatokat, amelyek a csatlakozási problémát okozhatnak.

    ```
    ntfs-3g -o remove_hiberfile /mnt/DataBoxDisk/bitlockerVol1/dislocker-file /mnt/DataBoxDisk/mountVol1
    ```

5. Ezt egy tiszta leválasztás.

    ```
    ./DataBoxDiskUnlock_x86_64 /unmount
    ```

6. Hajtsa végre egy tiszta zárolásának feloldása, és csatlakoztassa.
7. Tesztelje a csatlakoztatási pont egy fájl írása.
8. Válassza le és csatlakoztassa újra a fájlmegőrzéssel ellenőrzése.
9. Folytassa az adatok másolását.
 
### <a name="issue-error-with-data-not-persisting-after-copy"></a>Probléma: Hiba történt az adatok másolása után nem megőrzése
 
**OK** 

Ha látja, hogy a meghajtó nem rendelkezik adatokkal után, leválasztani (bár az adatok másolta azt), akkor lehetséges, csak olvasható a meghajtó csatlakoztatása után ismét csatolva legyen olvasási és írási egy meghajtón.

**Felbontás**
 
Ha ez a helyzet, nézzék meg a [írásvédett meghajtóként első csatlakoztatott meghajtók](#issue-drive-getting-mounted-as-read-only).

Amely nem másolja a naplókat az a mappa, amely a Data Box lemez zárolásának feloldásához eszköz és [forduljon a Microsoft Support](data-box-disk-contact-microsoft-support.md).


## <a name="data-box-disk-split-copy-tool-errors"></a>A Data Box Disk másolásfelosztó eszközének hibái

Az adatok felosztása több lemez keresztül egy Split Copy eszköz használatakor láthatók a hibák az alábbi táblázat foglalja össze.

|Hibaüzenet/figyelmeztetések |Javaslatok |
|---------|---------|
|[Információ] Kötet BitLocker-jelszó beolvasása: m <br>[Hiba] Kivétel történt a kötet m: BitLocker-kulcs beolvasása közben<br> A szekvencia nem tartalmaz elemeket.|Ez a hibaüzenet azt jelzi, hogy a Data Box Disk célhelye offline állapotú. <br> A lemezek online állapotúvá tételéhez használja a `diskmgmt.msc` eszközt.|
|[Hiba] Kivétel lépett fel: Nem sikerült a WMI-műveletet:<br> Method=UnlockWithNumericalPassword, ReturnValue=2150694965, <br>Win32Message=A megadott helyreállítási jelszó formátuma érvénytelen. <br>A Bitlocker helyreállítási jelszavai 48 számjegyűek. <br>Ellenőrizze a helyreállítási jelszó formátumát, majd próbálkozzon újra.|Először oldja fel a lemezek zárolását a Data Box Disk lemezzárolás-feloldó eszközével, majd próbálja újból végrehajtani a parancsot. További információért lásd: <li> [a Data Box Disk lemezzárolás-feloldó eszközének Windows-ügyfélen való használatát ismertető részt](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client). </li><li> [a Data Box Disk lemezzárolás-feloldó eszközének Linux-ügyfélen való használatát ismertető részt](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client). </li>|
|[Hiba] Kivétel lépett fel: DriveManifest.xml fájl létezik a cél-meghajtón. <br> Ez azt jelezheti, hogy a célmeghajtót más naplófájllal készítették elő. <br>Ha további adatokat szeretne hozzáadni ugyanahhoz a meghajtóhoz, használja az előző naplófájlt. Törölje a meglévő adatokat, és újra felhasználhatja a célmeghajtó egy új importálási feladat, törli a *DriveManifest.xml* a meghajtón található. Futtassa újra ezt a parancsot egy új naplófájllal.| Ez a hiba akkor fordulhat elő, ha több importálási munkamenethez is ugyanazt a meghajtókészletet próbálja használni. <br> Egy adott meghajtókészletet csak egyetlen másolásfelosztási munkamenethez használjon.|
|[Hiba] Kivétel lépett fel: CopySessionId importadatokat szeptembertől-teszt – 1. egy előző másolási munkamenet hivatkozik, és nem használható fel újra egy új példányt munkamenethez.|Ez a hiba akkor fordulhat elő, ha egy új feladat neveként egy korábban már sikeresen befejeződött feladat nevét próbálja megadni.<br> Egyedi nevet adjon meg az új feladat számára.|
|[Információ] A célfájl vagy -könyvtár neve túllépte az NTFS fájlrendszerre érvényes hosszkorlátot. |Ezt az üzenetet akkor kapja, ha át kellett nevezni a célfájlt, mert túl hosszú volt a fájl elérési útja.<br> Ezt a viselkedést a `config.json` fájl diszponálási beállításánál módosíthatja.|
|[Hiba] Kivétel lépett fel: Hibás JSON escape-szekvencia. |Ezt az üzenetet akkor kapja, ha a config.json fájl érvénytelen formátumot használ. <br> A fájl mentése előtt ellenőrizze a `config.json` fájlt a [JSONlint](https://jsonlint.com/) eszközzel.|


## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [érvényesítési eszköz hibáinak elhárítása](data-box-disk-troubleshoot.md).
