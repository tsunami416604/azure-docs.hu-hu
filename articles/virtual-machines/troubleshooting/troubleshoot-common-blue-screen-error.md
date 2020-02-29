---
title: Blue Screen-hibák egy Azure-beli virtuális gép indításakor | Microsoft Docs
description: Megtudhatja, hogyan lehet elhárítani a rendszerindításkor a kék képernyő hibája miatti hibát. Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/28/2018
ms.author: genli
ms.openlocfilehash: beb1562738699bbcede58d8214e69342abbb7c93
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77921470"
---
# <a name="windows-shows-blue-screen-error-when-booting-an-azure-vm"></a>A Windows egy Azure-beli virtuális gép indításakor kék képernyős hibát jelez
Ez a cikk a Windows rendszerű virtuális gépek (VM-EK) Microsoft Azure-ben való indításakor felmerülő kék képernyős hibákat ismerteti. Ez a témakör segítséget nyújt a támogatási jegy adatainak gyűjtéséhez. 


## <a name="symptom"></a>Hibajelenség 

Egy Windows rendszerű virtuális gép nem indul el. Amikor bejelöli a rendszerindítási [diagnosztika](./boot-diagnostics.md)rendszerindítási funkcióit, a következő hibaüzenetek egyike jelenik meg egy kék képernyőn:

- a számítógép hibába ütközött, és újra kell indítani. Most gyűjtünk néhány hibaüzenetet, majd újraindíthatjuk.
- A számítógép hibát észlelt, és újra kell indítania.

Ez a szakasz a virtuális gépek kezelésekor felmerülő gyakori hibaüzeneteket sorolja fel:

## <a name="cause"></a>Ok

Több oka is lehet annak, hogy miért lenne leállítási hiba. A leggyakoribb okok a következők:

- Probléma egy illesztőprogrammal
- Sérült rendszerfájl vagy memória
- Egy alkalmazás a memória tiltott szektorához fér hozzá

## <a name="collect-memory-dump-file"></a>Memóriakép-fájl gyűjtése

A probléma megoldásához először össze kell gyűjtenie az összeomláshoz tartozó memóriakép-fájlt, és kapcsolatba kell lépnie a memóriakép fájllal. A memóriakép-fájl összegyűjtéséhez kövesse az alábbi lépéseket:

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális Géphez

1. Készítsen pillanatképet az érintett virtuális gép operációsrendszer-lemezéről biztonsági másolatként. További információ: [lemez pillanatképe](../windows/snapshot-copy-managed-disk.md).
2. [Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális géphez](../windows/troubleshoot-recovery-disks-portal.md). 
3. Távoli asztalról a helyreállítási virtuális gépre.

### <a name="locate-dump-file-and-submit-a-support-ticket"></a>Memóriaképfájl megkeresése és támogatási jegy beküldése

1. A helyreállítási virtuális gépen nyissa meg a Windows mappát a csatolt operációsrendszer-lemezen. Ha a csatlakoztatott operációsrendszer-lemezhez hozzárendelt illesztőprogram betűjele F, akkor a F:\Windows. kell lépnie.
2. Keresse meg a Memory. dmp fájlt, majd [küldjön el egy támogatási jegyet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a memóriakép fájljában. 

Ha nem találja a memóriaképet, helyezze át a következő lépést a memóriakép és a soros konzol engedélyezéséhez.

### <a name="enable-dump-log-and-serial-console"></a>Memóriakép napló és a soros konzol engedélyezése

Memóriakép napló és a soros konzol engedélyezéséhez futtassa a következő szkriptet.

1. Nyisson meg egy rendszergazda jogú parancssor-munkamenetet (Futtatás rendszergazdaként).
2. Futtassa a következő parancsfájlt:

    Ebben a parancsfájlban feltételezzük, hogy a csatlakoztatott operációsrendszer-lemezhez rendelt meghajtóbetűjel F.  Cserélje le a megfelelő értékre a virtuális gépen.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

    1. Győződjön meg arról, hogy elegendő lemezterület áll rendelkezésre a lemezen a RAM memóriájának lefoglalásához, amely a virtuális gép számára kiválasztott mérettől függ.
    2. Ha nincs elég hely, vagy nagy méretű virtuális gép (G, GS vagy E sorozat), akkor megváltoztathatja a fájl létrehozásának helyét, és a virtuális géphez csatolt bármely más adatlemezre hivatkozni fog. Ehhez módosítania kell a következő kulcsot:

            reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

            REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
            REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f

            reg unload HKLM\BROKENSYSTEM

3. [Válassza le az operációsrendszer-lemezt, majd csatlakoztassa újra az operációsrendszer-lemezt az érintett virtuális géphez](../windows/troubleshoot-recovery-disks-portal.md).
4. Indítsa el a virtuális gépet a probléma újbóli előállításához, majd hozzon létre egy memóriaképfájl-fájlt.
5. Csatolja az operációsrendszer-lemezt egy helyreállítási virtuális géphez, gyűjtsön egy memóriakép-fájlt, majd [küldjön be egy támogatási jegyet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a memóriakép fájljába.



