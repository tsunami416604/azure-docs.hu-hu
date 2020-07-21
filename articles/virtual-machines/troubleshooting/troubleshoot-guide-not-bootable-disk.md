---
title: Rendszerindítási hiba – "ez nem rendszerindító lemez"
description: Ez a cikk a hibák megoldásának lépéseit ismerteti, amelyekkel a lemez nem indítható el egy Azure-beli virtuális gépen
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
ms.openlocfilehash: 16f6919577955bda5b04db26deb9fe78a467e364
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86509035"
---
# <a name="boot-error--this-is-not-a-bootable-disk"></a>Rendszerindítási hiba – ez nem rendszerindító lemez

Ez a cikk olyan problémák megoldását ismerteti, amelyekben a lemez nem indítható el egy Azure-beli virtuális gépen (VM).

## <a name="symptoms"></a>Probléma

Ha [rendszerindítási diagnosztikát](./boot-diagnostics.md) használ a virtuális gép képernyőképének megtekintéséhez, látni fogja, hogy a képernyőképen megjelenik egy üzenet, amely a következő üzenetet jeleníti meg: "ez nem rendszerindító lemez. Helyezzen be egy rendszerindító lemezt, és nyomja le az egyik billentyűt az újbóli próbálkozáshoz...

   1. ábra

   ![Az 1. ábra a következő üzenetet jeleníti meg: * "ez nem rendszerindító lemez. Helyezzen be egy rendszerindító hajlékonylemezt, és nyomja le az egyik billentyűt az újbóli próbálkozáshoz... "*](media/troubleshoot-guide-not-bootable-disk/1.jpg)

## <a name="cause"></a>Ok

Ez a hibaüzenet azt jelenti, hogy az operációs rendszer rendszerindítási folyamata nem talált aktív rendszerpartíciót. Ez a hiba azt is jelentheti, hogy hiányzik egy hivatkozás a rendszerindítási konfigurációs adatok (BCD) tárolóban, ami megakadályozza a Windows-partíció megkeresését.

## <a name="solution"></a>Megoldás

### <a name="process-overview"></a>Folyamat áttekintése

1. Hozzon létre és nyissa meg a javítási virtuális gépet.
2. A partíció állapotának beállítása aktívra.
3. Javítsa ki a lemez partícióját.
4. **Ajánlott**: a virtuális gép újraépítése előtt engedélyezze a soros konzol és a memóriakép gyűjteményét.
5. Hozza létre újra az eredeti virtuális gépet.

   > [!NOTE]
   > Ha ezt a rendszerindítási hibát tapasztalja, a vendég operációs rendszer nem működik. A probléma megoldásához offline módban hibaelhárítást hajthat végre.

### <a name="create-and-access-a-repair-vm"></a>Javítási virtuális gép létrehozása és elérése

1. A virtuálisgép- [javítási parancsok](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) 1-3-es lépéseit használva készítse elő a javítási virtuális gépet.
2. A Távoli asztali kapcsolat kapcsolódása a javítási virtuális géphez.

### <a name="set-partition-status-to-active"></a>Partíció állapotának beállítása aktívra

Az 1. generációs virtuális gépeknek először ellenőriznie kell, hogy a BCD-tárolót birtokló operációsrendszer-partíció *aktívként*van-e megjelölve. Ha 2. generációs virtuális géppel rendelkezik, ugorjon előre a [lemezpartíció kijavítása](#fix-the-disk-partition)érdekében, mivel a későbbi generációban az *állapot* jelzője elavult volt.

1. Nyisson meg egy rendszergazda jogú parancssort *(cmd.exe)*.
2. Adja meg a *DiskPart* eszközt a DiskPart eszköz elindításához.
3. Írja be a *lemez listázása* lehetőséget a rendszerlemezek listázásához, és keresse meg a csatolt operációs rendszer virtuális merevlemezét.
4. Ha a csatlakoztatott operációs rendszer VHD-je található, írja be a *sel Disk #* értéket a lemez kiválasztásához.  Lásd a 2. ábrát, ahol az 1. lemez a csatolt operációs rendszer VHD-je.

   2. ábra

   ![A 2. ábrán a * DISKPART * ablak látható, amely a tábla lemez megjelenítése parancs, a 0. lemez és az 1. lemez megjelenítésének kimenetét mutatja a táblában.  A sel-lemez 1 parancs kimenetét is megjeleníti, 1. lemez a kiválasztott lemez](media/troubleshoot-guide-not-bootable-disk/2.jpg)

5. A lemez kijelölése után írja be a *lista partícióját* a kiválasztott lemez partícióinak listázásához.
6. A rendszerindító partíció azonosítása után adja meg a *sel Partition #* elemet a partíció kiválasztásához.  Általában a rendszerindító partíció körülbelül 350 MB méretű lesz.  Lásd a 3. ábrát, ahol az 1. partíció a rendszerindító partíció.

   3. ábra

   ![A 3. ábrán a * DISKPART * ablak látható a * List Partition * parancs kimenetével. Az 1. és a 2. partíció megjelenik a táblázatban. Emellett a * sel Partition 1 * parancs kimenetét is megjeleníti, ha az 1. partíció a kiválasztott lemez.](media/troubleshoot-guide-not-bootable-disk/3.jpg)

7. A partíció állapotának vizsgálatához adja meg a "részletek partíció" értéket. Lásd: 4. ábra, ahol a partíció *aktív: nem*, vagy 5. ábra, ahol a partíció "Active: igen".

   4. ábra

   ![A 4. ábrán a * DISKPART * ablak látható a * detail Partition * parancs kimenetével, ha az 1. partíció értéke * Active: No *](media/troubleshoot-guide-not-bootable-disk/4.jpg)

   5. ábra

   ![Az 5. ábrán a * DISKPART * ablak látható a * detail Partition * parancs kimenetével, ha az 1. partíció az * Active: Igen * értékre van állítva.](media/troubleshoot-guide-not-bootable-disk/5.jpg)

8. Ha a partíció **nem aktív** *, az aktív jelző* megváltoztatásához adja meg az *aktív* értéket.
9. A *részletek partíció*beírásával győződjön meg róla, hogy az állapot változása megfelelően lett végrehajtva.

   6. ábra

   ![A 6. ábrán a DiskPart ablak a * detail Partition * parancs kimenetével jelenik meg, ha az 1. partíció értéke * aktív: igen *](media/troubleshoot-guide-not-bootable-disk/6.jpg)

10. A *Kilépés* gombra kattintva zárja be a DiskPart eszközt, és mentse a konfiguráció módosításait.

### <a name="fix-the-disk-partition"></a>A lemezpartíció kijavítása

1. Nyisson meg egy rendszergazda jogú parancssort (cmd.exe).
2. A következő parancs használatával futtassa a *chkdsk* parancsot a lemez (ek) re, és javítsa a hibákat:

   `chkdsk <DRIVE LETTER>: /f`

   Az "/f" parancs hozzáadásával kijavíthatja az esetleges hibákat a lemezen. Ügyeljen arra, hogy a helyére a <DRIVE LETTER> csatolt operációs rendszer virtuális merevlemezének betűjelét írja.

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>Ajánlott: a virtuális gép újraépítése előtt engedélyezze a soros konzol és a memóriaképek gyűjtését

A memóriakép-gyűjtés és a soros konzol engedélyezéséhez futtassa a következő parancsfájlt:

1. Nyisson meg egy rendszergazda jogú parancssor-munkamenetet (Futtatás rendszergazdaként).
2. Futtassa az alábbi parancsot:

   Soros konzol engedélyezése

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

   `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

3. Győződjön meg arról, hogy az operációsrendszer-lemezen lévő szabad terület a virtuális gép memóriájának méretétől (RAM) függ.

   Ha nincs elég hely az operációsrendszer-lemezen, akkor módosítania kell a memóriakép fájljának helyét, és a virtuális géphez csatlakoztatott minden olyan adatlemezre, amely elegendő szabad hellyel rendelkezik. A hely módosításához cserélje le a "% SystemRoot%" kifejezést az alábbi parancsokban található adatlemez meghajtóbetűjelére (például "F:").

#### <a name="suggested-configuration-to-enable-os-dump"></a>Az operációs rendszer kiírásának engedélyezése javasolt konfiguráció

**Sérült operációsrendszer-lemez betöltése**:

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**Engedélyezés a ControlSet001:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Engedélyezés a ControlSet002:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**Sérült operációsrendszer-lemez eltávolítása:**

`REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-original-vm"></a>Az eredeti virtuális gép újraépítése

A virtuális gép újraösszeállításához használja [a virtuális gép javítási parancsainak 5. lépését](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) .
