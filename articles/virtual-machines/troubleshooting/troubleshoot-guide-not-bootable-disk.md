---
title: Boot hiba - "ez nem egy bootolható lemez"
description: Ez a cikk olyan problémák megoldásához nyújt lépéseket, amelyek miatt a lemez nem indítható az Azure virtuális gépen
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5d6db4e3-c2f5-40c7-afea-54edf745f5eb
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/25/2020
ms.author: v-mibufo
ms.openlocfilehash: 9f0c6350b89dcfecefcadcc166f7af35abc4b128
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80300978"
---
# <a name="boot-error--this-is-not-a-bootable-disk"></a>Rendszerindítási hiba – Ez nem rendszerindításra alkalmas lemez

Ez a cikk olyan problémák megoldásához, ahol a lemez nem indítható egy Azure virtuális gép (VM) problémák megoldásához.

## <a name="symptoms"></a>Probléma

Amikor [a rendszerindítási diagnosztika](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) segítségével tekinti meg a virtuális gép képernyőképét, látni fogja, hogy a képernyőkép egy "Ez nem rendszerindításra alkalmas lemez" üzenettel jelenik meg. Helyezzen be egy bootolható hajlékonylemezt, és nyomja meg bármelyik billentyűt, hogy újra megpróbálja...'.

   1. ábra

   ![Ábra 1 mutatja az üzenetet *"Ez nem egy bootolható lemez. Helyezzen be egy bootolható hajlékonylemezt, és nyomja meg bármelyik billentyűt, hogy újra megpróbálja..."*](media/troubleshoot-guide-not-bootable-disk/1.jpg)

## <a name="cause"></a>Ok

Ez a hibaüzenet azt jelenti, hogy az operációs rendszer rendszerindítási folyamata nem talált aktív rendszerpartíciót. Ez a hiba azt is jelentheti, hogy hiányzik egy hivatkozás a rendszerindítási konfigurációs adatok (BCD) tárolóban, megakadályozva, hogy megtalálja a Windows partíciót.

## <a name="solution"></a>Megoldás

### <a name="process-overview"></a>Folyamat áttekintése

1. Hozzon létre és férjen hozzá egy javítási virtuális géphez.
2. A Partíció állapotának beállítása Aktív ra.
3. Javítsa ki a lemezpartíciót.
4. **Ajánlott:** A virtuális gép újraépítése előtt engedélyezze a soros konzol és a memóriakép gyűjteményét.
5. Építse újra az eredeti virtuális gép.

   > [!NOTE]
   > A rendszerindítási hiba találkozásakor a vendég operációs rendszer nem működik. A probléma megoldásához offline módban kell hibaelhárítást elhárítania.

### <a name="create-and-access-a-repair-vm"></a>Virtuális gép létrehozása és elérése

1. A [virtuális gép javítási parancsainak](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) 1-3.
2. A Távoli asztali kapcsolat használatával csatlakozzon a javítási virtuális géphez.

### <a name="set-partition-status-to-active"></a>Partíció állapotának beállítása aktívra

Az *1.* Ha 2. generációs virtuális gépe van, ugorjon előre [a Lemezpartíció javítása](#fix-the-disk-partition)elemre, mivel az *állapotjelző* elavult a későbbi generációban.

1. Nyisson meg egy rendszergazda jogú parancssort *(cmd.exe).*
2. A DISKPART eszköz elindításához írja be a *diskpart* értéket.
3. Írja be a *listalemezt* a rendszer lemezeinek listázásához, és azonosítsa a csatlakoztatott operációsrendszer-virtuális merevlemezt.
4. Miután a csatlakoztatott operációs rendszer virtuális merevlemeztalálható, írja be *sel disk #* a lemez kiválasztásához.  Lásd a 2.

   2. ábra

   ![Ábra 2.  Azt is mutatja kimeneta sel disk 1 parancs, 1.](media/troubleshoot-guide-not-bootable-disk/2.jpg)

5. A lemez kiválasztása után adja meg a *listapartíciót* a kijelölt lemez partícióinak listázásához.
6. Miután a rendszerindító partíciót azonosította, írja be *a sel partition # parancsot* a partíció kiválasztásához.  Általában a boot partíció körülbelül 350 MB méretű lesz.  Lásd a 3.

   3. ábra

   ![3. ábra a *DISKPART* ablakot mutatja a *list partition* parancs kimenetével. Az 1- es és a 2-es partíció megjelenik a táblázatban. Azt is mutatja, a kimeneta a *sel partíció 1 * parancs, ha a partíció 1 a kijelölt lemez.](media/troubleshoot-guide-not-bootable-disk/3.jpg)

7. Adja meg a "részletpartíció" értéket a partíció állapotának ellenőrzéséhez. Lásd a 4. *Active: No*

   4.

   ![Ábra 4 mutatja a *DISKPART* ablakkimenettel a * részlet partíció * parancs, ha partíció 1 van beállítva, hogy *Aktív: Nem *](media/troubleshoot-guide-not-bootable-disk/4.jpg)

   5. ábra

   ![Ábra 5 mutatja a *DISKPART* ablak kimenetét a * részlet partíció * parancs, ha partíció 1 van beállítva, hogy *Aktív: Igen *.](media/troubleshoot-guide-not-bootable-disk/5.jpg)

8. Ha a partíció **nem aktív,** adja meg az Aktív jelző módosításához *az* *aktív* értéket.
9. Ellenőrizze, hogy az állapotváltozás megfelelően történt-e a *részletpartíció*beírásával.

   6. ábra

   ![Ábra 6 mutatja a diskpart ablak kimeneti * részlet partíció * parancs, ha partíció 1 van beállítva, hogy *Aktív: Igen *](media/troubleshoot-guide-not-bootable-disk/6.jpg)

10. A DISKPART eszköz bezárásához és a konfigurációs módosítások mentéséhez lépjen *be.*

### <a name="fix-the-disk-partition"></a>A lemezpartíció javítása

1. Nyisson meg egy rendszergazda jogú parancssort (cmd.exe).
2. A *CHKDSK* futtatásához és a hibák javításához használja a következő parancsot:

   `chkdsk <DRIVE LETTER>: /f`

   A '/f' parancs hozzáadásával kijavítja a lemezen található hibákat. Győződjön meg <DRIVE LETTER> arról, hogy cserélje ki a mellékelt operációs rendszer virtuális merevlemezének betűjét.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Ajánlott: A virtuális gép újraépítése előtt engedélyezze a soros konzol és a memóriakép gyűjteményét

A memóriakép-gyűjtemény és a Soros konzol engedélyezéséhez futtassa a következő parancsfájlt:

1. Nyisson meg egy rendszergazdai parancssori munkamenetet (Futtatás rendszergazdaként).
2. Futtassa az alábbi parancsot:

   Soros konzol engedélyezése

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

3. Ellenőrizze, hogy az operációs rendszer lemezén lévő szabad terület legalább annyira megvan-e, mint a virtuális gép memóriamérete (RAM).

   Ha nincs elég hely az operációs rendszer lemezén, meg kell változtatnia a helyet, ahol a memóriakép fájl jön létre, és olvassa el, hogy minden adatlemez csatlakozik a virtuális gép, amely elegendő szabad terület. A hely módosításához cserélje le a(z) "%SystemRoot%" parancsot az alábbi parancsokban lévő adatlemez meghajtóbetűjelére (például "F:").

#### <a name="suggested-configuration-to-enable-os-dump"></a>Javasolt konfiguráció az operációs rendszer memóriaképengedélyezéséhez

**Hibás operációsrendszer-lemez betöltése:**

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**Engedélyezés a ControlSet001-en:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Engedélyezés a ControlSet002-n:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Hibás operációsrendszer-lemez eltávolítása:**

`REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-original-vm"></a>Az eredeti virtuális gép újraépítése

A [virtuális gép javítási parancsainak 5.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example)
