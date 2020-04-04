---
title: Az Azure virtuális gép e-vel nem válaszol c01A001D hibával a Windows Update alkalmazásakor
description: Ez a cikk lépéseket tartalmaz a problémák megoldásához, ahol a Windows-frissítés hibát generál, és nem válaszol egy Azure virtuális gép.
services: virtual-machines-windows
documentationcenter: ''
author: TobyTu
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: a97393c3-351d-4324-867d-9329e31b3528
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 03/31/2020
ms.author: v-mibufo
ms.openlocfilehash: 16c8eed3377c2191b4345ec59ec1eba8be01369d
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633956"
---
# <a name="vm-is-unresponsive-with-c01a001d-error-when-applying-windows-update"></a>A Virtuális gép nem válaszol a "C01A001D" hibaüzenettel a Windows Update alkalmazásakor

Ez a cikk lépéseket tartalmaz a problémák megoldásához, ahol a Windows Update (KB) hibát generál, és nem válaszol egy Azure-beli virtuális gép.

## <a name="symptoms"></a>Probléma

Ha a [rendszerindítási diagnosztikát](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) használja a virtuális gép képernyőképének megtekintéséhez, megjelenik a folyamatban lévő Windows Update (KB), de a "C01A001D" hibakóddal sikertelen.

![nem válaszol a Windows Update](./media/unresponsive-vm-apply-windows-update/unresponsive-windows-update.png)

## <a name="cause"></a>Ok

A fájlrendszerben nem hozható létre alapfájl. Az operációs rendszer nem tud fájlokat írni a lemezre.

## <a name="resolution"></a>Megoldás:

### <a name="process-overview"></a>Folyamat áttekintése

1. [Hozzon létre és férjen hozzá egy javító virtuális géphez.](#create-and-access-a-repair-vm)
2. [Szabadítson fel helyet a merevlemezen](#free-up-space-on-the-hard-disk).
3. [Ajánlott: A virtuális gép újraépítése előtt engedélyezze a soros konzol és a memóriakép gyűjteményét.](#recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection)
4. [Építse újra a virtuális gép](#rebuild-the-vm).

> [!NOTE]
> Ha ez a hiba történik, a vendég operációs rendszer nem működik. A probléma megoldásához kapcsolat nélküli módban kell elhárítania a hibákat.

### <a name="create-and-access-a-repair-vm"></a>Javítási virtuális gép létrehozása és elérése

1. A [virtuális gép javítási parancsainak 1-3.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands)
2. Csatlakozzon a javítási virtuális géphez a Távoli asztali kapcsolat használatával.

### <a name="free-up-space-on-the-hard-disk"></a>Hely felszabadítása a merevlemezen

Ha a lemez még nem 1 Tb, át kell méretezni. Ha a lemez 1 TB-os, hajtsa végre a lemez karbantartását és a meghajtó töredezettségmentesítését.

1. Ellenőrizze, hogy megtelt-e a lemez. Ha a lemez 1 Tb alatt van, [bontsa ki legfeljebb 1 Tb-re a PowerShell használatával.](https://docs.microsoft.com/azure/virtual-machines/windows/expand-os-disk?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json)
2. Ha a lemez 1 Tb-ot ért el, végezzen lemeztisztítást.
    - [Válassza le az adatlemezt a hibás virtuális gépről.](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk)
    - [Csatlakoztassa az adatlemezt egy működő virtuális géphez.](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps#attach-an-existing-data-disk-to-a-vm)
    - A [Lemezkarbantartó eszközzel](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) szabadíthat fel helyet.
3. Az átméretezés és a karbantartás után töredezettségmentesítse a meghajtót:

    ```
    defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
    ```
    A töredezettség szintjétől függően ez órákig is eltarthat.

### <a name="recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection"></a>Ajánlott: A virtuális gép újraépítése előtt engedélyezze a soros konzol és a memóriakép gyűjteményét

1. Nyisson meg egy rendszergazdai parancssori munkamenetet (Futtatás rendszergazdaként).
2. Futtassa az alábbi parancsot:

    Soros konzol engedélyezése:

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
3. Győződjön meg arról, hogy az operációs rendszer lemezén a szabad terület legalább egyenlő a virtuális gép memória (RAM) mérete.

    Ha nincs elég hely az operációs rendszer lemezén, módosítsa a helyet, ahol a memóriakép fájl jön létre, és utalja át a virtuális géphez csatolt adatlemezre, és elegendő szabad terület. A hely módosításához `%SystemRoot%` cserélje le az adatlemez meghajtóbetűjelét (például "F:") az alábbi parancsokban:

    **Az operációs rendszer kiírásának engedélyezése javasolt konfiguráció:**

    Hibás operációsrendszer-lemez betöltése:

    ```
    REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
    ```

    Engedélyezés a ControlSet001-en:

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    Engedélyezés a ControlSet002-n:

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    Hibás operációsrendszer-lemez eltávolítása:

    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```

### <a name="rebuild-the-vm"></a>A virtuális gép újraépítése

A [virtuális gép javítási parancsainak 5.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example)
