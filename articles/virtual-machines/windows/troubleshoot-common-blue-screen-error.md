---
title: Kék képernyő hibák, amikor egy Azure virtuális gép elindítása |} A Microsoft Docs
description: Ismerje meg a probléma elhárításához a kék képernyő hibát fogadott történő rendszerindítás esetén |} A Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
authors: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 00/19/2018
ms.author: genli
ms.openlocfilehash: 4a029e331129d40b0380fc142d858aa299b7751b
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47225446"
---
# <a name="windows-shows-blue-screen-error-when-booting-an-azure-vm"></a>Windows kék képernyő hibát jeleníti meg, ha egy Azure virtuális gép elindítása
Ez a cikk ismerteti, amely felmerülhet, amikor elindítja a Windows virtuális gép (VM) a Microsoft Azure-ban kék képernyő hibákat. Ismerteti egy támogatási jegyet az adatgyűjtéshez nyújt segítséget. 

> [!NOTE] 
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk ismerteti, javasoljuk, hogy az új központi telepítéseknél helyett a klasszikus üzemi modell használatával Resource Manager üzemi modell használatával.

## <a name="symptom"></a>Jelenség 

Windows virtuális gép nem indul el. Amikor ellenőrizheti a rendszerindító képernyőképek a [rendszerindítási diagnosztika](./boot-diagnostics.md), az alábbi hibaüzenetek kékképernyős egyikét látja:

- a számítógép hibába ütközött egy problémát, és indítsa újra kell. Csak adatok néhány hiba adatait, és majd újraindíthatja.
- A számítógép hibába ütközött egy problémát, és indítsa újra kell.

Ez a szakasz ismerteti a Gyakori hibaüzenetek jelentkezhetnek, ha a virtuális gépek kezelése:

## <a name="cause"></a>Ok

Miért leállási hiba számíthat, több oka lehet. A leggyakoribb okok a következők:

- Egy illesztőprogram-probléma
- A rendszer sérült fájl vagy a memória
- Egy alkalmazás eléri egy tiltott szektort a memória

## <a name="collect-memory-dump-file"></a>Memóriakép gyűjtése

A probléma megoldásához, gyűjtse össze az összeomlási memóriakép fájlt, és forduljon az ügyfélszolgálathoz a memóriakép-fájl első kell lennie. A memóriakép-fájl összegyűjtése, kövesse az alábbi lépéseket:

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális Géphez

1. Pillanatkép készítése az operációsrendszer-lemez az érintett virtuális gépek biztonsági mentéséhez. További információkért lásd: [lemez pillanatképének elkészítése](snapshot-copy-managed-disk.md).
2. [Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális Géphez](troubleshoot-recovery-disks-portal.md). 
3. Távoli asztal, a helyreállítási virtuális Gépet.

### <a name="locate-dump-file-and-submit-a-support-ticket"></a>Keresse meg a memóriakép-fájl, és a egy támogatási jegyet is küldhet

1. A helyreállítási virtuális Gépet lépjen a csatlakoztatott operációsrendszer-lemez a windows-mappában. Ha van rendelve a csatlakoztatott operációsrendszer-lemez meghajtóbetűjelét F, kell F:\Windows Ugrás.
2. Th memory.dmp fájlt, keresse meg, majd [támogatási jegyet is küldhet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) memóriakép-fájllal. 

Ha nem találja a memóriakép-fájl, helyezze át a következő lépéssel engedélyezheti a memóriakép naplóját és a soros konzol.

### <a name="enable-dump-log-and-serial-console"></a>Memóriakép napló és a soros konzol engedélyezése

Memóriakép napló és a soros konzol engedélyezéséhez futtassa a következő szkriptet.

1. -Munkamenetet emelt szintű parancssorból (Futtatás rendszergazdaként).
2. Futtassa a következő parancsfájlt:

    Ez a szkript feltételezzük, hogy a meghajtóbetűjel van rendelve a csatlakoztatott operációsrendszer-lemez-e f  Cserélje le a megfelelő értéket a virtuális gépen.

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

    1. Győződjön meg arról, hogy van-e elég hely a lemezen a legtöbb memóriát lefoglalni, a RAM, amely kiválasztja a virtuális gép méretétől függ.
    2. Ha nem áll elég hely, vagy egy nagy méretű virtuális gép (G, GS vagy E sorozat), majd sikerült módosítani a helyet, ahol ez a fájl jön létre, és tekintse meg, amely bármely más adatlemezt a virtuális géphez van csatolva. Ehhez szüksége lesz a következő kulcs módosítása:

            reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

            REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
            REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f

            reg unload HKLM\BROKENSYSTEM

3. [Az operációsrendszer-lemez leválasztása, és ezután csatlakoztassa újra az érintett virtuális gépre az operációsrendszer-lemez](troubleshoot-recovery-disks-portal.md).
4. Indítsa el a virtuális Gépet reprodukálnia a hibát, majd a memóriakép-fájl jön.
5. Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális Géphez, gyűjtése memóriakép-fájl, majd [támogatási jegyet is küldhet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) memóriakép-fájllal.



