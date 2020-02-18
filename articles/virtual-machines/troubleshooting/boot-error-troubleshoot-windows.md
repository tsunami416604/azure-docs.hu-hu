---
title: Az Azure Virtual Machines leállítása megakadt a szolgáltatások újraindítása, leállítása vagy leállítása esetén | Microsoft Docs
description: Ez a cikk segítséget nyújt az Azure Windows Virtual Machines szolgáltatásbeli hibák elhárításában.
services: virtual-machines-windows
documentationCenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/19/2019
ms.author: tibasham
ms.openlocfilehash: 5d6396efc9ab25baa0d32e7c33c7715863516249
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/16/2020
ms.locfileid: "77371354"
---
# <a name="azure-windows-vm-shutdown-is-stuck-on-restarting-shutting-down-or-stopping-services"></a>Az Azure Windows rendszerű virtuális gépek leállítása a "újraindítás", "Leállítás" vagy "szolgáltatások leállítása" esetén beragadt

Ez a cikk a Windows rendszerű virtuális gépek (VM-EK) Microsoft Azure történő újraindításakor előforduló "újraindítás", "Leállítás" vagy "szolgáltatások leállítása" problémáinak elhárításához nyújt útmutatást.

## <a name="symptoms"></a>Probléma

Ha [rendszerindítási diagnosztika](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) használatával tekinti meg a virtuális gép képernyőképét, láthatja, hogy a képernyőképen az "újraindítás", a "Leállítás" vagy a "szolgáltatások leállítása" üzenet jelenik meg.

![A szolgáltatások képernyőjének újraindítása, leállítása és leállítása](./media/boot-error-troubleshooting-windows/restart-shut-down-stop-service.png)
 
## <a name="cause"></a>Ok

A Windows a leállítási folyamattal hajtja végre a rendszerkarbantartási műveleteket, és dolgozza fel a módosításokat, például a frissítéseket, a szerepköröket és a szolgáltatásokat. Ezt a kritikus folyamatot nem ajánlott a befejezésig megszakítani. A frissítések és a változtatások számától és a virtuális gép méretétől függően a folyamat hosszú időt is igénybe vehet. Ha a folyamat leáll, lehetséges, hogy az operációs rendszer megsérül. Csak akkor szakítsa meg a folyamatot, ha túl sokáig tart.

## <a name="solution"></a>Megoldás

### <a name="collect-a-process-memory-dump"></a>Folyamat-memóriakép gyűjtése

1. Töltse le a [Procdump eszközt](http://download.sysinternals.com/files/Procdump.zip) egy új vagy meglévő adatlemezre, amely ugyanahhoz a régióhoz tartozó működő virtuális géphez van csatolva.

2. Válassza le a működő virtuális gépről szükséges fájlokat tartalmazó lemezt, és csatlakoztassa a lemezt a hibás virtuális géphez. Ezt a lemezt hívjuk a **segédprogram lemezének**.

A következő lépések végrehajtásához használja a [soros konzolt](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows) :

1. Nyisson meg egy felügyeleti PowerShellt, és győződjön meg arról, hogy a szolgáltatás leáll a leállítás után.

   ``
   Get-Service | Where-Object {$_.Status -eq "STOP_PENDING"}
   ``

2. A felügyeleti CMD eszközben szerezze be a lefagyott szolgáltatás PID azonosítóját.

   ``
   tasklist /svc | findstr /i <STOPING SERVICE>
   ``

3. Szerezzen be egy memóriaképfájl-mintát a lefagyott folyamat <STOPPING SERVICE>.

   ``
   procdump.exe -s 5 -n 3 -ma <PID>
   ``

4. A leállítási folyamat zárolásának feloldásához most öld meg a lefagyott folyamatot.

   ``
   taskkill /PID <PID> /t /f
   ``

Ha az operációs rendszer újraindul, ha a szokásos módon elindul, akkor csak ellenőrizze, hogy az operációs rendszer konzisztenciaa rendben van-e. Ha a rendszer sérülést jelez, futtassa a következő parancsot, amíg a lemez nem sérül:

``
dism /online /cleanup-image /restorehealth
``

Ha nem tudja összegyűjteni a folyamat memóriaképét, vagy a probléma rekurzív, és a kiváltó okok elemzését igényli, folytassa az operációs rendszer memóriaképének begyűjtésével, folytassa a támogatási kérelem megnyitásával.

### <a name="collect-an-os-memory-dump"></a>Operációs rendszer memóriaképének begyűjtése

Ha a probléma nem oldódik meg a módosítások feldolgozásának megkezdése után, be kell gyűjtenie egy memóriakép-fájlt, és kapcsolatba kell lépnie a támogatási szolgálattal. A memóriakép-fájl összegyűjtéséhez kövesse az alábbi lépéseket:

**Az operációsrendszer-lemez csatlakoztatása egy helyreállítási virtuális géphez**

1. Készítsen pillanatképet az érintett virtuális gép operációsrendszer-lemezéről biztonsági másolatként. További információ: [lemez pillanatképe](https://docs.microsoft.com/azure/virtual-machines/windows/snapshot-copy-managed-disk).

2. [Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális géphez](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal).

3. Távoli asztalról a helyreállítási virtuális gépre.

4. Ha az operációsrendszer-lemez titkosítva van, ki kell kapcsolnia a titkosítást, mielőtt továbblép a következő lépésre. További információ: [a titkosított operációsrendszer-lemez visszafejtése a virtuális gépen, amely nem indítható el](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-bitlocker-boot-error#solution).

**Memóriaképfájl megkeresése és támogatási jegy beküldése**

1. A helyreállítási virtuális gépen nyissa meg a Windows mappát a csatolt operációsrendszer-lemezen. Ha a csatlakoztatott operációsrendszer-lemezhez hozzárendelt illesztőprogram betűjele F, akkor a F:\Windows. kell lépnie.

2. Keresse meg a Memory. dmp fájlt, majd [küldjön el egy támogatási jegyet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a memóriakép fájljában.

Ha nem találja a memóriaképet, helyezze át a következő lépést a memóriakép és a soros konzol engedélyezéséhez.

**A memóriakép és a soros konzol engedélyezése**

Memóriakép napló és a soros konzol engedélyezéséhez futtassa a következő szkriptet.

1. Nyisson meg egy rendszergazda jogú parancssor-munkamenetet (Futtatás rendszergazdaként).

2. Futtassa a következő parancsfájlt:

   Ebben a parancsfájlban feltételezzük, hogy a csatlakoztatott operációsrendszer-lemezhez rendelt meghajtóbetűjel F. cserélje le a virtuális gép megfelelő értékére.

   ```
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
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump / t REG_DWORD /d 1 /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

3. Ellenőrizze, hogy van-e elegendő hely a lemezen a RAM memóriájának lefoglalásához, amely a virtuális gép számára kiválasztott mérettől függ.

4. Ha nincs elég hely, vagy a virtuális gép nagyméretű (G, GS vagy E sorozat), módosíthatja a fájl létrehozásának helyét, és a virtuális géphez csatolt bármely más adatlemezre hivatkozni fog. A hely módosításához módosítania kell a következő kulcsot:

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

5. [Válassza le az operációsrendszer-lemezt, majd csatlakoztassa újra az operációsrendszer-lemezt az érintett virtuális géphez](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal).

6. Indítsa el a virtuális gépet, és nyissa meg a soros konzolt.

7. A memóriakép kiváltásához válassza a nem maszkolt megszakítás (NMI) küldése lehetőséget.

   ![Nem maszkolt megszakítás küldése](./media/boot-error-troubleshooting-windows/send-nonmaskable-interrupt.png)

8. Csatlakoztassa újra az operációsrendszer-lemezt egy helyreállítási virtuális géphez, és Gyűjtse össze a memóriaképet tartalmazó fájlt.

## <a name="contact-microsoft-support"></a>Kapcsolatfelvétel a Microsoft ügyfélszolgálatával

A memóriakép-fájl összegyűjtése után forduljon a Microsoft támogatási szolgálatához, és állapítsa meg a kiváltó okot.
