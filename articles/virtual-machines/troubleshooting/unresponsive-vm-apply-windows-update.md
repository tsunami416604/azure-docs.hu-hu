---
title: Az Azure-beli virtuális gép nem válaszol a C01A001D hibával a Windows Update alkalmazásakor
description: Ez a cikk olyan problémák megoldását ismerteti, amelyekben a Windows Update hibát generál, és egy Azure-beli virtuális gépen nem válaszol.
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
ms.openlocfilehash: 76c3f729a8520c7bff7b49a1d2200d7950f8a9f4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87074294"
---
# <a name="vm-is-unresponsive-with-c01a001d-error-when-applying-windows-update"></a>A virtuális gép nem válaszol "C01A001D" hibaüzenettel Windows Update alkalmazása során

Ez a cikk azokat a problémákat ismerteti, amelyekkel a Windows Update (KB) hibát generál, és egy Azure-beli virtuális gépen nem válaszol.

## <a name="symptoms"></a>Probléma

Ha [rendszerindítási diagnosztikát](./boot-diagnostics.md) használ a virtuális gép képernyőképének megtekintéséhez, a folyamatban lévő Windows Update (kb) megjelenik, de a hibakód: "C01A001D".

![nem válaszol Windows Update](./media/unresponsive-vm-apply-windows-update/unresponsive-windows-update.png)

## <a name="cause"></a>Ok

Nem hozható létre alapvető fájl a fájlrendszerben. Az operációs rendszer nem tud fájlokat írni a lemezre.

## <a name="resolution"></a>Feloldás

### <a name="process-overview"></a>Folyamat áttekintése

1. [Hozzon létre és nyissa meg a javítási virtuális gépet](#create-and-access-a-repair-vm).
2. [Szabadítson fel lemezterületet a merevlemezen](#free-up-space-on-the-hard-disk).
3. [Ajánlott: a virtuális gép újraépítése előtt engedélyezze a soros konzol és a memóriakép gyűjteményét](#recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection).
4. Hozza [létre újra a virtuális gépet](#rebuild-the-vm).

> [!NOTE]
> Ha ez a hiba történik, a vendég operációs rendszer nem működik. A probléma megoldásához offline módban kell hibaelhárítást végeznie.

### <a name="create-and-access-a-repair-vm"></a>Javítási virtuális gép létrehozása és elérése

1. A javítási virtuális gép előkészítéséhez kövesse [a virtuális gép javítási parancsainak 1-3. lépéseit](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) .
2. Kapcsolódjon a javítási virtuális géphez Távoli asztali kapcsolat használatával.

### <a name="free-up-space-on-the-hard-disk"></a>Szabadítson fel lemezterületet a merevlemezen

Ha a lemez még nem 1 TB, át kell méreteznie. Ha a lemez 1 TB méretű, hajtson végre egy Lemezkarbantartó-lemezt és a meghajtó töredezettségmentesítését.

1. Ellenőrizze, hogy a lemez megtelt-e. Ha a lemez 1 TB-nál kisebb, [akkor a PowerShell használatával legfeljebb 1 TB-ra bontsa ki](../windows/expand-os-disk.md?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json).
2. Ha a lemez 1 TB, hajtson végre egy Lemezkarbantartó-karbantartási műveletet.
    - [Válassza le az adatlemezt a hibás virtuális](../windows/detach-disk.md)gépről.
    - [Csatlakoztassa az adatlemezt egy működő virtuális géphez](../windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).
    - Lemezterület felszabadításához használja a [lemezkarbantartó eszközt](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) .
3. Az átméretezés és a karbantartás után a meghajtó töredezettségmentesítése:

    ```
    defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
    ```
    A töredezettség szintjétől függően ez órákig is eltarthat.

### <a name="recommended-before-rebuilding-the-vm-enable-serial-console-and-memory-dump-collection"></a>Ajánlott: a virtuális gép újraépítése előtt engedélyezze a soros konzol és a memóriaképek gyűjtését

1. Nyisson meg egy rendszergazda jogú parancssor-munkamenetet (Futtatás rendszergazdaként).
2. Futtassa az alábbi parancsot:

    Soros konzol engedélyezése:

    ```
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```
3. Győződjön meg arról, hogy az operációsrendszer-lemez szabad területe eléri a virtuális gép memóriájának (RAM) méretét.

    Ha nincs elég hely az operációsrendszer-lemezen, akkor módosítsa a memóriakép fájljának helyét, és a virtuális géphez csatlakoztatott adatlemezre, valamint elegendő szabad területtel forduljon. A hely módosításához cserélje le az `%SystemRoot%` adatlemez betűjelét (például "F:") az alábbi parancsokban található adatlemezre:

    **OPERÁCIÓSRENDSZER-memóriakép javasolt konfigurációjának engedélyezése:**

    Sérült operációsrendszer-lemez betöltése:

    ```
    REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
    ```

    Engedélyezés a ControlSet001:

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    Engedélyezés a ControlSet002:

    ```
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
    ```

    Sérült operációsrendszer-lemez eltávolítása:

    ```
    REG UNLOAD HKLM\BROKENSYSTEM
    ```

### <a name="rebuild-the-vm"></a>A virtuális gép újraépítése

A virtuális gép újraösszeállításához használja [a virtuális gép javítási parancsainak 5. lépését](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) .
