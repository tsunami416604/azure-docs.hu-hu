---
title: Operációs rendszer indításának hibája – Windows Update telepítési kapacitás
description: A hibák elhárításának lépései, amelyekben Windows Update (KB) hibaüzenetet kap, és nem válaszol egy Azure-beli virtuális gépen.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 6359e486-7b02-4c1e-995c-ef6d505f85f4
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: v-miegge
ms.openlocfilehash: 596303223554589ef26938486ccfd2281ccd46f5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86999105"
---
# <a name="troubleshoot-os-start-up--windows-update-installation-capacity"></a>Operációs rendszer indításának hibája – Windows Update telepítési kapacitás

Ez a cikk egy olyan Azure-beli virtuális gép (VM) hibáinak megoldásához nyújt útmutatást, ahol a Windows Update (KB) hibaüzenetet kap, és nem válaszol.

## <a name="symptom"></a>Hibajelenség

Ha rendszerindítási diagnosztikát használ a virtuális gép képernyőképének megtekintéséhez, látni fogja, hogy a képernyőképen a Windows Update (KB) jelenik meg, a hibakód azonban sikertelen: **C01A001D**. Az alábbi Windows Update képen a "hiba C01A001D a frissítési művelet alkalmazása # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # # #

![Windows Update (KB) a következő üzenettel van beragadva: "hiba történt az Y (Z) C01A001D alkalmazásakor.](./media/troubleshoot-windows-update-installation-capacity/1.png)

## <a name="cause"></a>Ok

Ebben az esetben az operációs rendszer (OS) nem tud befejezni egy Windows Update (KB) telepítést, mert nem hozható létre alapfájl a fájlrendszerben. A hibakód alapján az operációs rendszer nem tud fájlokat írni a lemezre.

## <a name="solution"></a>Megoldás

### <a name="process-overview"></a>Folyamat áttekintése:

1. Hozzon létre és nyissa meg a javítási virtuális gépet.
1. Szabad terület a lemezen.
1. A soros konzol és a memóriakép gyűjteményének engedélyezése.
1. Hozza létre újra a virtuális gépet.

> [!NOTE]
> Ha ezt a hibát tapasztalja, a vendég operációs rendszer nem működik. A probléma elhárítása offline módban a probléma megoldásához.

### <a name="create-and-access-a-repair-vm"></a>Javítási virtuális gép létrehozása és elérése

1. A virtuálisgép- [javítási parancsok](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) 1-3-es lépéseit használva készítse elő a javítási virtuális gépet.
1. A Távoli asztali kapcsolat használatával kapcsolódjon a javítási virtuális géphez.

### <a name="free-up-space-on-the-disk"></a>Szabadítson fel lemezterületet a lemezen

A probléma megoldásához:

- Méretezze át a lemezt legfeljebb 1 TB-ra, ha az még nem a maximális 1 TB-os méretnél.
- Hajtson végre egy Lemezkarbantartó-karbantartási műveletet.
- A meghajtó dedarabolása.

1. Ellenőrizze, hogy a lemez megtelt-e. Ha a lemez mérete 1 TB-nál kisebb, akkor a [PowerShell használatával](../windows/expand-os-disk.md)legfeljebb 1 TB-ra bontsa ki.
1. Ha a lemez már 1 TB, akkor el kell végeznie a lemez karbantartását.
   1. Válassza le az adatlemezt [a hibás virtuális](../windows/detach-disk.md)gépről.
   1. Csatlakoztassa az adatlemezt [egy működő virtuális géphez](../windows/attach-disk-ps.md#attach-an-existing-data-disk-to-a-vm).
   1. Lemezterület felszabadításához használja a [lemezkarbantartó eszközt](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) .
1. Az átméretezés és a tisztítás befejezése után a következő paranccsal távolíthatja el a meghajtót:

   ```
   defrag <LETTER ASSIGN TO THE OS DISK>: /u /x /g
   ```
   
A töredezettség szintjétől függően a detöredezettség több órát is igénybe vehet.

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

    **Töltse be a hibás operációsrendszer-lemezt:**

   ```
   REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM 
   ```
   
   **Engedélyezés a ControlSet001**:

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
   ```
   
   **Engedélyezés a ControlSet002**:

   ```
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f 
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
   ```
   
   **Sérült operációsrendszer-lemez eltávolítása**:

   ```
   REG UNLOAD HKLM\BROKENSYSTEM
   ```
   
### <a name="rebuild-the-vm"></a>A virtuális gép újraépítése

A virtuális gép újraépítéséhez használja [a virtuális gép javítási parancsainak 5. lépését](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example) .
