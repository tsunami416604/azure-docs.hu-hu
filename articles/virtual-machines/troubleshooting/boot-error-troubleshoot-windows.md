---
title: Az Azure virtuális gépek leállítása beragadt az újraindítás, a leállítás vagy a leállítási szolgáltatások leállítása korra | Microsoft dokumentumok
description: Ez a cikk segít az Azure Windows virtuális gépek szolgáltatáshibáinak elhárításában.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77371354"
---
# <a name="azure-windows-vm-shutdown-is-stuck-on-restarting-shutting-down-or-stopping-services"></a>Az Azure Windows virtuális gép leállítása beragadt az "Újraindítás", "Leállítás" vagy "Szolgáltatások leállítása"

Ez a cikk a "Újraindítás", a "Leállítás" vagy a "Szolgáltatások leállítása" üzenetek kelkapcsolatos problémák megoldásához, amelyek a Windows virtuális gépek (VM) Windows Azure-beli újraindításakor találkozhatnak.

## <a name="symptoms"></a>Probléma

Amikor [a rendszerindítási diagnosztika](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) segítségével tekinti meg a virtuális gép képernyőképét, láthatja, hogy a képernyőkép a "Újraindítás", "Leállítás" vagy "Szolgáltatások leállítása" üzenetet jeleníti meg.

![Újraindítás, A szolgáltatások képernyőinek leállítása és leállítása](./media/boot-error-troubleshooting-windows/restart-shut-down-stop-service.png)
 
## <a name="cause"></a>Ok

A Windows a leállítási folyamat ot használja a rendszerkarbantartási műveletek végrehajtására, valamint olyan folyamatmódosítások ra, mint a frissítések, a szerepkörök és a szolgáltatások. Nem ajánlott megszakítani ezt a kritikus folyamatot, amíg be nem fejeződik. A frissítések/módosítások számától és a virtuális gép méretétől függően a folyamat hosszú időt vehet igénybe. Ha a folyamat leáll, lehetséges, hogy az operációs rendszer megsérül. Csak akkor szakítsa meg a folyamatot, ha az túl sokáig tart.

## <a name="solution"></a>Megoldás

### <a name="collect-a-process-memory-dump"></a>Folyamat memóriakép gyűjtése

1. [Procdump eszköz](http://download.sysinternals.com/files/Procdump.zip) letöltése egy új vagy meglévő adatlemezre, amely ugyanabból a régióból származó működő virtuális géphez van csatlakoztatva.

2. Válassza le a szükséges fájlokat tartalmazó lemezt a működő virtuális gépről, és csatlakoztassa a lemezt a hibás virtuális géphez. Hívjuk ezt a lemezt a **Segédprogram lemez**.

A [serial console használatával](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows) hajtsa végre az alábbi lépéseket:

1. Nyisson meg egy felügyeleti Powershellt, és ellenőrizze a leállításkor lefagyott szolgáltatást.

   ``
   Get-Service | Where-Object {$_.Status -eq "STOP_PENDING"}
   ``

2. Egy felügyeleti CMD, a lefagyott szolgáltatás PID-je.

   ``
   tasklist /svc | findstr /i <STOPING SERVICE>
   ``

3. Memóriakép minta beszerezni <STOPPING SERVICE>a felfüggesztett folyamatból .

   ``
   procdump.exe -s 5 -n 3 -ma <PID>
   ``

4. Most öld meg a felfüggesztett folyamatot, hogy feltárja a leállítási folyamatot.

   ``
   taskkill /PID <PID> /t /f
   ``

Miután az operációs rendszer újra indul, ha elindul rendesen, akkor csak győződjön meg arról, hogy az operációs rendszer konzisztenciája rendben van. Ha a rendszer korrupciót jelent, futtassa a következő parancsot, amíg a lemez sérülésmentes nem lesz:

``
dism /online /cleanup-image /restorehealth
``

Ha nem tud összegyűjteni egy folyamat memóriakép, vagy ez a probléma rekurzív, és szüksége van egy kiváltó ok elemzése, folytassa az alábbi operációs rendszer memóriakép gyűjtése, a folytatást, hogy nyissa meg a támogatási kérelmet.

### <a name="collect-an-os-memory-dump"></a>Operációs rendszer memóriaképgyűjtése

Ha a probléma nem oldódik meg, miután megvárta a módosítások feldolgozását, össze kell gyűjtenie egy memóriaképfájlt, és kapcsolatba kell lépnie az ügyfélszolgálattal. A kiírási fájl összegyűjtéséhez hajtsa végre az alábbi lépéseket:

**Az operációs rendszer lemezének csatolása helyreállítási virtuális géphez**

1. Készítsen pillanatképet az érintett virtuális gép operációsrendszer-lemezéről biztonsági másolatként. További információt a [Lemez pillanatképe](https://docs.microsoft.com/azure/virtual-machines/windows/snapshot-copy-managed-disk)című témakörben talál.

2. [Csatlakoztassa az operációs rendszer lemezét egy helyreállítási virtuális géphez.](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal)

3. Távoli asztal a helyreállítási virtuális géphez.

4. Ha az operációs rendszer lemeze titkosítva van, ki kell kapcsolnia a titkosítást, mielőtt a következő lépésre lépne. További információt a nem indítható virtuális gép [titkosított operációsrendszer-lemezének visszafejtése című témakörben talál.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-bitlocker-boot-error#solution)

**Dump fájl megkeresése és támogatási jegy beküldése**

1. A helyreállítási virtuális gép, ugrás a Windows mappába a csatlakoztatott operációs rendszer lemezén. Ha a csatlakoztatott operációsrendszer-lemezhez rendelt illesztőprogram-betű "F" betű, akkor az F:\Windows rendszerre kell lépnie.

2. Keresse meg a memory.dmp fájlt, majd [küldjön el egy támogatási jegyet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a memóriaképfájllal.

Ha nem találja a memóriaképfájlt, a következő lépéssel engedélyezze a memóriaképnaplót és a Soros konzolt.

**Memóriaképnapló és soros konzol engedélyezése**

A memóriaképnapló és a Soros konzol engedélyezéséhez futtassa a következő parancsfájlt.

1. Nyissa meg a rendszergazdai jogú parancssori munkamenetet (futtatás rendszergazdaként).

2. Futtassa a következő parancsfájlt:

   Ebben a parancsfájlban feltételezzük, hogy a csatlakoztatott operációsrendszer-lemezhez rendelt meghajtóbetűjel f. Cserélje le a megfelelő értéket a virtuális gép.

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

3. Ellenőrizze, hogy van-e elegendő hely a lemezen annyi memória lefoglalásához, mint a RAM, amely a virtuális géphez kiválasztott mérettől függ.

4. Ha nincs elég hely, vagy a virtuális gép nagy (G, GS vagy E sorozat), módosíthatja a helyet, ahol ez a fájl jön létre, és olvassa el, hogy bármely más adatlemez, amely a virtuális géphez csatlakozik. A hely módosításához a következő kulcsot kell módosítania:

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

   REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
   
   reg unload HKLM\BROKENSYSTEM
   ```

5. [Válassza le az operációs rendszer lemezét, majd csatlakoztassa újra az operációs rendszer lemezét az érintett virtuális géphez](https://docs.microsoft.com/azure/virtual-machines/windows/troubleshoot-recovery-disks-portal).

6. Indítsa el a virtuális gép, és a soros konzol eléréséhez.

7. A memóriakép aktiválásához válassza a Nem maszkolható megszakítás (NMI) küldése lehetőséget.

   ![Nem maszkolásos megszakítás küldése](./media/boot-error-troubleshooting-windows/send-nonmaskable-interrupt.png)

8. Csatlakoztassa az operációs rendszer lemezét egy helyreállítási virtuális gép hez újra, gyűjtse dump fájlt.

## <a name="contact-microsoft-support"></a>Kapcsolatfelvétel a Microsoft ügyfélszolgálatával

A memóriaképfájl összegyűjtése után lépjen kapcsolatba a Microsoft támogatási szolgálatával a kiváltó ok megállapítása érdekében.
