---
title: VM indítása megragadt a "Felkészülés a Windows. Ne kapcsolja ki a számítógépet" az Azure-ban | Microsoft dokumentumok
description: A "Windows felindulása" című probléma elhárításának lépéseit ismertetheti a probléma megoldásához, amelyben a virtuális gép indítása korvan. Ne kapcsolja ki a számítógépet.”
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 09/18/2018
ms.author: delhan
ms.openlocfilehash: da45e24898bc3b5aead250077af69a61bdb33bab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73749643"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>VM indítása megragadt a "Felkészülés a Windows. Ne kapcsolja ki a számítógépet" az Azure-ban

Ez a cikk a "Felkészülés" és a "Felkészülés a Windows-ra" képernyőket ismerteti, amelyek a Windows virtuális gép (VM) Microsoft Azure-beli indításakor találkozhatnak. Ez a támogatási jegy adatainak gyűjtéséhez szükséges lépéseket tartalmazza.

 

## <a name="symptoms"></a>Probléma

A Windows virtuális gép nem indul el. Amikor **a rendszerindítási diagnosztika** segítségével a képernyőkép a virtuális gép, előfordulhat, hogy a virtuális gép megjeleníti a "Felkészülés" vagy a "A Windows készen áll".

![Példa üzenet a Windows Server 2012 R2 rendszerhez](./media/troubleshoot-vm-configure-update-boot/message1.png)

![Példa üzenetre](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>Ok

Ez a probléma általában akkor fordul elő, amikor a kiszolgáló a konfiguráció módosítása után a végső újraindítást végzi. A konfigurációs módosítást inicializálhatják a Windows-frissítések vagy a kiszolgáló szerepkörein/szolgáltatásán végrehajtott módosítások. A Windows Update rendszerben, ha a frissítések mérete nagy volt, az operációs rendszernek több időre van szüksége a módosítások újrakonfigurálásához.

## <a name="collect-an-os-memory-dump"></a>Operációs rendszer memóriaképgyűjtése

Ha a probléma nem oldódik meg, miután megvárta a módosítások feldolgozását, össze kell gyűjtenie egy memóriaképfájlt, és kapcsolatba kell lépnie az ügyfélszolgálattal. A kiírási fájl összegyűjtéséhez hajtsa végre az alábbi lépéseket:

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Az operációs rendszer lemezének csatolása helyreállítási virtuális géphez

1. Készítsen pillanatképet az érintett virtuális gép operációsrendszer-lemezéről biztonsági másolatként. További információt a [Lemez pillanatképe](../windows/snapshot-copy-managed-disk.md)című témakörben talál.
2. [Csatlakoztassa az operációs rendszer lemezét egy helyreállítási virtuális géphez.](../windows/troubleshoot-recovery-disks-portal.md)
3. Távoli asztal a helyreállítási virtuális géphez. 
4. Ha az operációs rendszer lemeze titkosítva van, ki kell kapcsolnia a titkosítást, mielőtt a következő lépésre lépne. További információt a nem indítható virtuális gép [titkosított operációsrendszer-lemezének visszafejtése című témakörben talál.](troubleshoot-bitlocker-boot-error.md#solution)

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
4. Indítsa el a virtuális gép, és a soros konzol eléréséhez.
5. A memóriakép aktiválásához válassza **a Nem maszkolható megszakítás (NMI) küldése** lehetőséget.
    ![a nem maszkolásos megszakítás elküldésének helye](./media/troubleshoot-vm-configure-update-boot/run-nmi.png)
6. Csatlakoztassa az operációs rendszer lemezét egy helyreállítási virtuális gép hez újra, gyűjtse dump fájlt.

## <a name="contact-microsoft-support"></a>Kapcsolatfelvétel a Microsoft ügyfélszolgálatával

A memóriaképfájl összegyűjtése után lépjen kapcsolatba a [Microsoft támogatási szolgálatával](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a kiváltó ok elemzéséhez.