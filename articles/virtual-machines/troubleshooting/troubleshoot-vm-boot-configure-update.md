---
title: A virtuális gép indítása beragadt a Windows rendszerre kész állapotba. Ne kapcsolja ki a számítógépet az Azure-ban | Microsoft Docs
description: Bemutatjuk azokat a lépéseket, amelyekkel elhárítható a virtuális gép indításakor beragadt "a Windows rendszer kész állapotba kerül. Ne kapcsolja ki a számítógépet.”
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
ms.openlocfilehash: 415895b894261ade9b2332eb3fb926eba74fe937
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86078408"
---
# <a name="vm-startup-is-stuck-on-getting-windows-ready-dont-turn-off-your-computer-in-azure"></a>A virtuális gép indítása beragadt a Windows rendszerre kész állapotba. A számítógép kikapcsolása az Azure-ban

Ez a cikk a Windows rendszerű virtuális gépek (VM-EK) Microsoft Azure való indításakor felmerülő "felkészülést" és "Windows Ready" képernyőket ismerteti. Ez a témakör segítséget nyújt a támogatási jegy adatainak gyűjtéséhez.

 

## <a name="symptoms"></a>Probléma

Egy Windows rendszerű virtuális gép nem indul el. Ha **rendszerindítási diagnosztikát** használ a virtuális gép képernyőképének beszerzéséhez, láthatja, hogy a virtuális gép a "felkészülés" vagy a "Windows Ready" üzenetet jeleníti meg.

![Példa a Windows Server 2012 R2-re](./media/troubleshoot-vm-configure-update-boot/message1.png)

![Üzenet – példa](./media/troubleshoot-vm-configure-update-boot/message2.png)

## <a name="cause"></a>Ok

Ez a probléma általában akkor fordul elő, ha a kiszolgáló a konfiguráció módosítása után a végső újraindítást végzi. Előfordulhat, hogy a konfigurációs módosítást a Windows-frissítések vagy a kiszolgáló szerepkörei/funkciójának módosításai inicializálják. Windows Update esetén, ha a frissítések mérete nagy, az operációs rendszernek több időre van szüksége a módosítások újrakonfigurálásához.

## <a name="collect-an-os-memory-dump"></a>Operációs rendszer memóriaképének begyűjtése

Ha a probléma nem oldódik meg a módosítások feldolgozásának megkezdése után, be kell gyűjtenie egy memóriakép-fájlt, és kapcsolatba kell lépnie a támogatási szolgálattal. A memóriakép-fájl összegyűjtéséhez kövesse az alábbi lépéseket:

### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Az operációsrendszer-lemez csatlakoztatása egy helyreállítási virtuális géphez

1. Készítsen pillanatképet az érintett virtuális gép operációsrendszer-lemezéről biztonsági másolatként. További információ: [lemez pillanatképe](../windows/snapshot-copy-managed-disk.md).
2. [Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális géphez](../windows/troubleshoot-recovery-disks-portal.md).
3. Távoli asztalról a helyreállítási virtuális gépre. 
4. Ha az operációsrendszer-lemez titkosítva van, ki kell kapcsolnia a titkosítást, mielőtt továbblép a következő lépésre. További információ: [a titkosított operációsrendszer-lemez visszafejtése a virtuális gépen, amely nem indítható el](troubleshoot-bitlocker-boot-error.md#solution).

### <a name="locate-dump-file-and-submit-a-support-ticket"></a>Memóriaképfájl megkeresése és támogatási jegy beküldése

1. A helyreállítási virtuális gépen nyissa meg a Windows mappát a csatolt operációsrendszer-lemezen. Ha a csatlakoztatott operációsrendszer-lemezhez hozzárendelt illesztőprogram betűjele F, akkor a F:\Windows. kell lépnie.
2. Keresse meg a Memory. dmp fájlt, majd [küldjön el egy támogatási jegyet](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a memóriakép fájljában. 

Ha nem találja a memóriaképet, helyezze át a következő lépést a memóriakép és a soros konzol engedélyezéséhez.

### <a name="enable-dump-log-and-serial-console"></a>A memóriakép és a soros konzol engedélyezése

A memóriakép és a soros konzol engedélyezéséhez futtassa az alábbi szkriptet.

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
    
        ```console
        reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

        REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f
        REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "<DRIVE LETTER OF YOUR DATA DISK>:\MEMORY.DMP" /f

        reg unload HKLM\BROKENSYSTEM
        ```

3. [Válassza le az operációsrendszer-lemezt, majd csatlakoztassa újra az operációsrendszer-lemezt az érintett virtuális géphez](../windows/troubleshoot-recovery-disks-portal.md).
4. Indítsa el a virtuális gépet, és nyissa meg a soros konzolt.
5. A memóriakép kiváltásához válassza a **nem maszkolt megszakítás (NMI) küldése** lehetőséget.
    ![a nem maszkolt megszakítások küldésére szolgáló rendszerkép](./media/troubleshoot-vm-configure-update-boot/run-nmi.png)
6. Csatlakoztassa újra az operációsrendszer-lemezt egy helyreállítási virtuális géphez, és Gyűjtse össze a memóriaképet tartalmazó fájlt.

## <a name="contact-microsoft-support"></a>Kapcsolatfelvétel a Microsoft ügyfélszolgálatával

A memóriakép-fájl összegyűjtése után forduljon a [Microsoft támogatási szolgálatához](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , és elemezze a kiváltó okot.