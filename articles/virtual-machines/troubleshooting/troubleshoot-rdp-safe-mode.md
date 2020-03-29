---
title: Távolról nem lehet csatlakozni az Azure virtuális gépekhez, mert a virtuális gép csökkentett módban indul el | Microsoft dokumentumok
description: Megtudhatja, hogy miként hárítható el egy olyan probléma, amely nem képes az RDP-t a virtuális gépre, mert a virtuális gép csökkentett módba vált.| Microsoft dokumentumok
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/13/2018
ms.author: genli
ms.openlocfilehash: 7bc2c0f472a03c3f069a889c360bea9017a780f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918206"
---
#  <a name="cannot-rdp-to-a-vm-because-the-vm-boots-into-safe-mode"></a>Az RDP nem helyezhető el virtuális gépszámára, mert a virtuális gép csökkentett módba vált

Ez a cikk bemutatja, hogyan oldható meg az a probléma, amelyben nem tud csatlakozni az Azure Windows virtuális gépekhez (VM-ek), mert a virtuális gép csökkentett módban van beállítva.


## <a name="symptoms"></a>Probléma

RdP-kapcsolatot vagy más kapcsolatokat (például HTTP) nem hozhat létre egy virtuális géphez az Azure-ban, mert a virtuális gép csökkentett módban való indításra van konfigurálva. Ha ellenőrzi a képernyőképet a [rendszerindítási diagnosztika](../troubleshooting/boot-diagnostics.md) az Azure Portalon, előfordulhat, hogy a virtuális gép elindul a szokásos módon, de a hálózati felület nem érhető el:

![Kép a hálózati inferce csökkentett módban](./media/troubleshoot-rdp-safe-mode/network-safe-mode.png)

## <a name="cause"></a>Ok

Az RDP szolgáltatás csökkentett módban nem érhető el. Csak az alapvető rendszerprogramok és szolgáltatások töltődnek be, amikor a virtuális gép csökkentett módba vált. Ez vonatkozik a két különböző változatai Csökkentett mód, amelyek "Safe Boot minimális" és a "Safe Boot kapcsolat".


## <a name="solution"></a>Megoldás

Az alábbi lépések végrehajtása előtt készítsen pillanatképet az érintett virtuális gép operációsrendszer-lemezéről biztonsági másolatként. További információt a [Lemez pillanatképe](../windows/snapshot-copy-managed-disk.md)című témakörben talál.

A probléma megoldásához a Soros vezérlővel konfigurálja a virtuális gép normál módban történő indítását, vagy [javítsa ki a virtuális gép offline](#repair-the-vm-offline) állapotba egy helyreállítási virtuális gép használatával.

### <a name="use-serial-control"></a>Soros vezérlő használata

1. Csatlakozzon a [Soros konzolhoz, és nyissa meg a CMD-példányt.](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
   ) Ha a soros konzol nincs engedélyezve a virtuális gépen, olvassa el [a virtuális gép javítása offline című témakört.](#repair-the-vm-offline)
2. Ellenőrizze a rendszerindítási konfigurációs adatokat:

        bcdedit /enum

    Ha a virtuális gép úgy van beállítva, hogy csökkentett módban induljon el, egy további jelző jelenik meg a **Windows rendszertöltő** szakasza alatt, a **safeboot**. Ha nem látja a **safeboot** jelzőt, a virtuális gép nincs csökkentett módban. Ez a cikk nem vonatkozik a forgatókönyvre.

    A **safeboot** jelző a következő értékekkel jelenhet meg:
   - Minimális
   - Network (Hálózat)

     A két mód bármelyikében az RDP nem indul el. Ezért a javítás ugyanaz marad.

     ![Kép a Csökkentett mód jelzőről](./media/troubleshoot-rdp-safe-mode/safe-mode-tag.png)

3. Törölje a **safemoade** jelzőt, így a virtuális gép normál módban indul el:

        bcdedit /deletevalue {current} safeboot

4. Ellenőrizze a rendszerindítási konfigurációs adatokat, és győződjön meg arról, hogy a **safeboot** jelző el van távolítva:

        bcdedit /enum

5. Indítsa újra a virtuális gép, majd ellenőrizze, hogy a probléma megoldódott.Restart the VM, and check whether the issue is resolved.

### <a name="repair-the-vm-offline"></a>A virtuális gép kapcsolat nélküli javítása

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Az operációs rendszer lemezének csatolása helyreállítási virtuális géphez

1. [Csatlakoztassa az operációs rendszer lemezét egy helyreállítási virtuális géphez.](../windows/troubleshoot-recovery-disks-portal.md)
2. Távoli asztali kapcsolat indítása a helyreállítási virtuális géppel.
3. Győződjön meg arról, hogy a lemez **online** ként van megjelölve a Lemezkezelés konzolon. Jegyezze fel a csatlakoztatott operációsrendszer-lemezhez rendelt meghajtóbetűjelet.

#### <a name="enable-dump-log-and-serial-console-optional"></a>Memóriaképnapló és soros konzol engedélyezése (nem kötelező)

A memóriaképnapló és a Soros konzol segít a további hibaelhárításban, ha a problémát a jelen cikkben szereplő megoldás nem tudja megoldani.

A memóriaképnapló és a Soros konzol engedélyezéséhez futtassa a következő parancsfájlt.

1. Nyisson meg egy rendszergazdai parancssori munkamenetet (**Futtatás rendszergazdaként**).
2. Futtassa a következő parancsfájlt:

    Ebben a parancsfájlban feltételezzük, hogy a csatlakoztatott operációsrendszer-lemezhez rendelt meghajtóbetűjel f. Cserélje le ezt a meghajtóbetűjelet a virtuális gép megfelelő értékével.

    ```powershell
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM

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

#### <a name="configure-the-windows-to-boot-into-normal-mode"></a>A Windows normál üzemmódba való indításának beállítása

1. Nyisson meg egy rendszergazdai parancssori munkamenetet (**Futtatás rendszergazdaként**).
2. Ellenőrizze a rendszerindítási konfigurációs adatokat. A következő parancsokban feltételezzük, hogy a csatlakoztatott operációsrendszer-lemezhez rendelt meghajtóbetűjel f. Cserélje le ezt a meghajtóbetűjelet a virtuális gép megfelelő értékével.

        bcdedit /store F:\boot\bcd /enum
    Jegyezze fel a **\windows** mappát tartalmazó partíció azonosítónevét. Alapértelmezés szerint az azonosító neve "Alapértelmezett".

    Ha a virtuális gép úgy van beállítva, hogy csökkentett módban induljon el, egy további jelző jelenik meg a **Windows rendszertöltő** szakasza alatt, a **safeboot**. Ha nem látja a **safeboot** jelzőt, ez a cikk nem vonatkozik a forgatókönyvre.

    ![A rendszerindítási azonosítóról szóló kép](./media/troubleshoot-rdp-safe-mode/boot-id.png)

3. Távolítsa el a **safeboot** jelzőt, hogy a virtuális gép normál módban induljön el:

        bcdedit /store F:\boot\bcd /deletevalue {Default} safeboot
4. Ellenőrizze a rendszerindítási konfigurációs adatokat, és győződjön meg arról, hogy a **safeboot** jelző el van távolítva:

        bcdedit /store F:\boot\bcd /enum
5. [Válassza le az operációs rendszer lemezét, és hozza létre újra a virtuális gép](../windows/troubleshoot-recovery-disks-portal.md). Ezután ellenőrizze, hogy a probléma megoldódott-e.
