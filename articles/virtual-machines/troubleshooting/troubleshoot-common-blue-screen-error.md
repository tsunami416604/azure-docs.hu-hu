---
title: Kék képernyős hibák azure-beli virtuális gép indításakor| Microsoft dokumentumok
description: További információ a kék képernyős hiba indításkor történt hibájának elhárításáról| Microsoft dokumentumok
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266935"
---
# <a name="windows-shows-blue-screen-error-when-booting-an-azure-vm"></a>A Windows kék képernyős hibát jelenít meg az Azure virtuális gép indításakor
Ez a cikk a kék képernyős hibákat ismerteti, amelyek a Windows virtuális gép (VM) Microsoft Azure-beli indításakor találkozhatnak. Ez a támogatási jegy adatainak gyűjtéséhez szükséges lépéseket tartalmazza. 


## <a name="symptom"></a>Hibajelenség 

A Windows virtuális gép nem indul el. Amikor a rendszerindítási képernyőképeket a [Rendszerindítási diagnosztika](./boot-diagnostics.md)alkalmazásban ellenőrzi, kék képernyőn az alábbi hibaüzenetek egyike jelenik meg:

- a pc futott be a problémát, és újra kell indítani. Csak néhány hibainformációt gyűjtünk, és újraindíthatja.
- A számítógép problémába ütközött, ezért újra kell indítani a számítógépet.

Ez a szakasz a virtuális gépek kezelése során előforduló gyakori hibaüzeneteket sorolja fel:

## <a name="cause"></a>Ok

Nem lehet több oka, hogy miért kapna egy stop hiba. A leggyakoribb okok a következők:

- Probléma a vezető
- Sérült rendszerfájl vagy memória
- Egy alkalmazás hozzáfér a memória tiltott szektorához

## <a name="collect-memory-dump-file"></a>Memóriaképfájl gyűjtése

A probléma megoldásához először össze kell gyűjtenie az összeomlás hoz a memóriaképfájlra vonatkozó kiírási fájlt, és kapcsolatba kell lépnie a memóriaképfájllal. A kiírási fájl összegyűjtéséhez hajtsa végre az alábbi lépéseket:

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Az operációs rendszer lemezének csatolása helyreállítási virtuális géphez

1. Készítsen pillanatképet az érintett virtuális gép operációsrendszer-lemezéről biztonsági másolatként. További információt a [Lemez pillanatképe](../windows/snapshot-copy-managed-disk.md)című témakörben talál.
2. [Csatlakoztassa az operációs rendszer lemezét egy helyreállítási virtuális géphez.](../windows/troubleshoot-recovery-disks-portal.md) 
3. Távoli asztal a helyreállítási virtuális géphez.

### <a name="locate-dump-file-and-submit-a-support-ticket"></a>Dump fájl megkeresése és támogatási jegy beküldése

1. A helyreállítási virtuális gép, ugrás a Windows mappába a csatlakoztatott operációs rendszer lemezén. Ha a csatlakoztatott operációsrendszer-lemezhez rendelt illesztőprogram-betű "F" betű, akkor az F:\Windows rendszerre kell lépnie.
2. Keresse meg a memory.dmp fájlt, majd [küldjön el egy támogatási jegyet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a memóriaképfájllal. 

Ha nem találja a memóriaképfájlt, a következő lépéssel engedélyezze a memóriaképnaplót és a Soros konzolt.

### <a name="enable-dump-log-and-serial-console"></a>Memóriaképnapló és soros konzol engedélyezése

A memóriaképnapló és a Soros konzol engedélyezéséhez futtassa a következő parancsfájlt.

1. Nyissa meg a rendszergazdai jogú parancssori munkamenetet (futtatás rendszergazdaként).
2. Futtassa a következő parancsfájlt:

    Ebben a parancsfájlban feltételezzük, hogy a csatlakoztatott operációsrendszer-lemezhez rendelt meghajtóbetűjel f.  Cserélje le a megfelelő értéket a virtuális gép.

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

    1. Győződjön meg arról, hogy elegendő hely van a lemezen annyi memória lefoglalásához, mint a RAM, amely a virtuális géphez kiválasztott mérettől függ.
    2. Ha nincs elég hely, vagy ez egy nagy méretű virtuális gép (G, GS vagy E sorozat), akkor módosíthatja a helyet, ahol ez a fájl jön létre, és olvassa el, hogy bármely más adatlemez, amely a virtuális géphez csatlakozik. Ehhez meg kell változtatnia a következő kulcsot:

            reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

            REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
            REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f

            reg unload HKLM\BROKENSYSTEM

3. [Válassza le az operációs rendszer lemezét, majd csatlakoztassa újra az operációs rendszer lemezét az érintett virtuális géphez](../windows/troubleshoot-recovery-disks-portal.md).
4. Indítsa el a virtuális gép reprodukálni a problémát, majd egy memóriakép fájl jön létre.
5. Csatolja az operációs rendszer lemezét egy helyreállítási virtuális géphez, gyűjtse össze a memóriaképfájlt, majd [küldjön el egy támogatási jegyet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a memóriaképfájllal.



