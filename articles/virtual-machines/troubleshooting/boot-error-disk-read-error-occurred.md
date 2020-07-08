---
title: Rendszerindítási hiba – hiba történt a lemez olvasásakor
description: Ez a cikk az Azure-beli virtuális gépeken nem olvasható problémák megoldásának lépéseit ismerteti.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 817c9c5c-6a81-4b42-a6ad-0a0a11858b84
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/01/2020
ms.author: v-miegge
ms.openlocfilehash: dea09b1ac29db99e1c52a31a605007fa4129e8ea
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84300525"
---
# <a name="troubleshoot-boot-error---disk-read-error-occurred"></a>Rendszerindítási hiba – hiba történt a lemez olvasásakor

Ez a cikk olyan problémák megoldását ismerteti, amelyekben a lemez nem olvasható egy Azure-beli virtuális gépen (VM).

## <a name="symptoms"></a>Probléma

Ha [rendszerindítási diagnosztikát](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) használ a virtuális gép képernyőképének megtekintéséhez, látni fogja, hogy a képernyőképen megjelenik a "lemezes olvasási hiba történt" üzenet. Az újraindításhoz nyomja le a Ctrl+Alt+Del billentyűkombinációt.”

   ![Hibaüzenet: lemezes olvasási hiba történt. Indítsa újra a CTRL + ALT + DEL billentyűkombinációt.](./media/disk-read-error-occurred/1.png)

## <a name="cause"></a>Ok

Ez a hibaüzenet azt jelzi, hogy a lemez szerkezete sérült, és nem olvasható. Ha 1. generációs virtuális gépet használ, az is előfordulhat, hogy a rendszerindítási konfigurációt tartalmazó lemezpartíció nem **aktív**értékre van állítva.

## <a name="solution"></a>Megoldás

### <a name="process-overview"></a>Folyamat áttekintése

1. Hozzon létre és nyissa meg a javítási virtuális gépet.
1. Válasszon megoldást:
   - [Partíció állapotának beállítása aktívra](#set-partition-status-to-active)
   - [A lemezpartíció kijavítása](#fix-the-disk-partition)
1. A soros konzol és a memóriakép gyűjteményének engedélyezése.
1. Hozza létre újra a virtuális gépet.

> [!NOTE]
> A rendszerindítási hiba észlelésekor a vendég operációs rendszer (OS) nem működik. A probléma megoldásához offline módban hibaelhárítást hajthat végre.

### <a name="create-and-access-a-repair-vm"></a>Javítási virtuális gép létrehozása és elérése

1. A virtuálisgép- [javítási parancsok](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands) 1-3-es lépéseit használva készítse elő a javítási virtuális gépet.
1. A Távoli asztali kapcsolat használatával kapcsolódjon a javítási virtuális géphez.

### <a name="set-partition-status-to-active"></a>Partíció állapotának beállítása aktívra

Az 1. generációs virtuális gépeknek először ellenőriznie kell, hogy a BCD-tárolót birtokló operációsrendszer-partíció **aktívként**van-e megjelölve. Ha 2. generációs virtuális géppel rendelkezik, ugorjon előre a [lemezpartíció kijavítása](#fix-the-disk-partition)érdekében, mivel a későbbi generációban az állapot jelzője elavult volt.

1. Nyisson meg egy rendszergazda jogú parancssort (cmd.exe).
1. Adja meg a **DiskPart** eszközt a **DiskPart** eszköz elindításához.
1. Írja be a **lemez listázása** lehetőséget a rendszerlemezek listázásához és a csatlakoztatott operációs rendszer virtuális merevlemezének (VHD) azonosításához.
1. Ha a csatlakoztatott operációs rendszer VHD-je található, írja be a **sel Disk #** értéket a lemez kiválasztásához. Tekintse meg az alábbi ábrát egy példához, ahol az 1. lemez a csatolt operációs rendszer VHD-je.

   ![A * * lista lemez * * parancs kimenetét tartalmazó DiskPart ablak, ahol a tábla 0 és 1. lemez jelenik meg. Az ablakban a * * sel-lemez 1 * * parancs kimenete is látható, ahol a kiválasztott lemez 1. lemez](./media/disk-read-error-occurred/2.png)

1. A lemez kijelölése után írja be a **lista partícióját** a kiválasztott lemez partícióinak listázásához.
1. A rendszerindító partíció azonosítása után adja meg a **sel Partition #** elemet a partíció kiválasztásához. A rendszerindító partíció általában körülbelül 350 MB méretű.  Tekintse meg a következő képet, például az 1. partíció a rendszerindító partíció.

   ![A (z) * * List Partition * * parancs kimenetével rendelkező DiskPart ablak, ahol a tábla 1. és 2. partíciója jelenik meg. Az ablakban a * * sel Partition 1 * * parancs kimenete is látható, ahol a kiválasztott lemez 1. partíció.](./media/disk-read-error-occurred/3.png)

1. A partíció állapotának vizsgálatához adja meg a **részletek partíciót** . Tekintse meg a következő képernyőképeket, amelyek az aktív partícióra vonatkozó példákat tartalmaznak **: nem** vagy **aktív: igen**.

   **Aktív: nem**

   ![A (z) * * detail Partition * * parancs kimenetét tartalmazó DiskPart ablak, ahol az 1. partíció értéke * * aktív: nem * *.](./media/disk-read-error-occurred/4.png)

   **Aktív: igen**

   ![A (z) * * detail Partition * * parancs kimenetét tartalmazó DiskPart ablak, ahol az 1. partíció értéke * * aktív: igen * *.](./media/disk-read-error-occurred/5.png)

1. Ha a partíció nem **aktív**, az aktív jelző megváltoztatásához adja meg az **aktív** értéket.
1. A **részletek partíciójának** megadásával ellenőrizze, hogy az állapot változása megfelelően fejeződött-e be, és ellenőrizze, hogy a kimenet tartalmazza-e az **aktív: igen**. 
1. A **Kilépés** gombra kattintva zárja be a DiskPart eszközt, és mentse a konfiguráció módosításait.

### <a name="fix-the-disk-partition"></a>A lemezpartíció kijavítása

1. Nyisson meg egy rendszergazda jogú parancssort (cmd.exe).
1. A következő parancs használatával futtassa a **chkdsk** parancsot a lemez (ek) ben, és végezzen hibajavításokat:

   `chkdsk <DRIVE LETTER>: /f`

   Az **/f** parancs hozzáadásával kijavíthatja az esetleges hibákat a lemezen. Ne felejtse el lecserélni **< meghajtóbetűjelet >** a csatolt operációs rendszer virtuális merevlemezének betűjelére.

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>A soros konzol és a memóriakép gyűjteményének engedélyezése

**Ajánlott**: a virtuális gép újraépítése előtt engedélyezze a soros konzol és a memóriakép gyűjteményét a következő parancsfájl futtatásával:

1. Nyisson meg egy rendszergazda jogú parancssor-munkamenetet.
1. Futtassa az alábbi parancsot:

   **A soros konzol engedélyezése**:
   
   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. Győződjön meg arról, hogy az operációsrendszer-lemez szabad területe nagyobb, mint a virtuális gép memóriájának mérete (RAM).

   Ha nincs elég hely az operációsrendszer-lemezen, akkor módosítsa a memóriakép fájljának helyét, és ezt a helyet a virtuális géphez csatolt, elegendő szabad területtel rendelkező adatlemezre kell hivatkoznia. A hely módosításához cserélje le a **% systemroot%** betűjelet az adatlemez meghajtóbetűjelére (pl **. F:**) a következő parancsokban.

   Az operációs rendszer memóriaképének engedélyezéséhez javasolt konfiguráció:

   **Töltse be a beállításjegyzék-struktúrát a hibás operációsrendszer-lemezről:**

   ```
   REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
   ```

   **Engedélyezés a ControlSet001:**

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **Engedélyezés a ControlSet002:**

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f 
   ```

   **Sérült operációsrendszer-lemez eltávolítása:**

   ```
   REG UNLOAD HKLM\BROKENSYSTEM
   ```
   
### <a name="rebuild-the-vm"></a>A virtuális gép újraépítése

A virtuális gép újraépítéséhez használja [a virtuális gép javítási parancsainak 5. lépését](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) .
