---
title: Nem lehet távolról kapcsolódni az Azure Virtual Machineshoz, mert a virtuális gép biztonságos módba indul | Microsoft Docs
description: Megtudhatja, hogyan lehet elhárítani egy olyan problémát, amely nem tud RDP-t létesíteni a virtuális géppel, mert a virtuális gép biztonságos módba indul. | Microsoft Docs
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77918206"
---
#  <a name="cannot-rdp-to-a-vm-because-the-vm-boots-into-safe-mode"></a>Nem lehet RDP-t a virtuális géphez, mert a virtuális gép biztonságos módba indul

Ez a cikk bemutatja, hogyan oldható meg a probléma, amikor nem tud csatlakozni az Azure Windows Virtual Machines (VM) szolgáltatáshoz, mert a virtuális gép csökkentett módban történő rendszerindításra van konfigurálva.


## <a name="symptoms"></a>Probléma

Az Azure-beli virtuális gépekhez nem lehet RDP-kapcsolatot vagy más kapcsolatokat (például HTTP) csatlakoztatni, mert a virtuális gép csökkentett módban történő rendszerindításra van konfigurálva. Ha a Azure Portal a [rendszerindítási diagnosztika](../troubleshooting/boot-diagnostics.md) képernyőképét, láthatja, hogy a virtuális gép rendesen elindul, de a hálózati adapter nem érhető el:

![Hálózati inferce kapcsolatos rendszerképek csökkentett módban](./media/troubleshoot-rdp-safe-mode/network-safe-mode.png)

## <a name="cause"></a>Ok

Az RDP szolgáltatás nem érhető el csökkentett módban. Csak az alapvető rendszerprogramok és szolgáltatások töltődnek be, amikor a virtuális gép biztonságos módba indul. Ez a csökkentett üzemmód két különböző verziójára vonatkozik, amelyek a "biztonságos rendszerindítás minimális száma" és a "biztonságos rendszerindítás kapcsolattal".


## <a name="solution"></a>Megoldás

Az alábbi lépések elvégzése előtt készítsen pillanatképet az érintett virtuális gép operációsrendszer-lemezéről biztonsági másolatként. További információ: [lemez pillanatképe](../windows/snapshot-copy-managed-disk.md).

A probléma megoldásához a soros vezérlő használatával konfigurálja a virtuális gépet normál módba való rendszerindításra, vagy [javítsa ki a virtuális gépet](#repair-the-vm-offline) egy helyreállítási virtuális gép használatával.

### <a name="use-serial-control"></a>Soros vezérlő használata

1. Kapcsolódjon a [soros konzolhoz, és nyissa meg a cmd-példányt](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
   ). Ha a soros konzol nincs engedélyezve a virtuális gépen, tekintse meg [a virtuális gép offline állapotba](#repair-the-vm-offline)helyezését ismertető témakört.
2. A rendszerindítási konfigurációs adatértékek keresése:

        bcdedit /enum

    Ha a virtuális gép csökkentett módban történő rendszerindításra van konfigurálva, a **Windows rendszerindítási betöltő** szakaszának **safeboot**nevű további jelző jelenik meg. Ha nem látja a **safeboot** jelzőt, a virtuális gép nem biztonságos módban van. Ez a cikk nem vonatkozik a forgatókönyvre.

    A **safeboot** jelző a következő értékekkel jelenhet meg:
   - Minimális
   - Network (Hálózat)

     A két mód egyikében az RDP nem lesz elindítva. A javítás ezért változatlan marad.

     ![A biztonságos mód jelzőjét ábrázoló rendszerkép](./media/troubleshoot-rdp-safe-mode/safe-mode-tag.png)

3. Törölje a **safemoade** jelzőt, így a virtuális gép normál módba fog indulni:

        bcdedit /deletevalue {current} safeboot

4. Ellenőrizze a rendszerindítási konfigurációs beállításokat, és győződjön meg arról, hogy a **safeboot** jelző el lett távolítva:

        bcdedit /enum

5. Indítsa újra a virtuális gépet, és győződjön meg arról, hogy a probléma megoldódott-e.

### <a name="repair-the-vm-offline"></a>A virtuális gép kijavítása kapcsolat nélküli üzemmódban

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Az operációsrendszer-lemez csatlakoztatása egy helyreállítási virtuális géphez

1. [Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális géphez](../windows/troubleshoot-recovery-disks-portal.md).
2. Távoli asztal-Kapcsolódás elindítása a helyreállítási virtuális géphez.
3. Győződjön meg arról, hogy a lemez **online** állapotban van megjelölve a Lemezkezelés konzolon. Jegyezze fel a csatlakoztatott operációsrendszer-lemezhez rendelt meghajtóbetűjelet.

#### <a name="enable-dump-log-and-serial-console-optional"></a>A memóriakép és a soros konzol engedélyezése (nem kötelező)

A memóriakép naplója és a soros konzol segít a további hibaelhárításban, ha a probléma nem oldható fel a megoldással ebben a cikkben.

A memóriakép és a soros konzol engedélyezéséhez futtassa az alábbi szkriptet.

1. Nyisson meg egy rendszergazda jogú parancssor-munkamenetet (**Futtatás rendszergazdaként**).
2. Futtassa a következő parancsfájlt:

    Ebben a parancsfájlban feltételezzük, hogy a csatlakoztatott operációsrendszer-lemezhez rendelt meghajtóbetűjel F. cserélje le a meghajtóbetűjelet a virtuális gép megfelelő értékére.

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

#### <a name="configure-the-windows-to-boot-into-normal-mode"></a>A Windows beállítása normál módba indításra

1. Nyisson meg egy rendszergazda jogú parancssor-munkamenetet (**Futtatás rendszergazdaként**).
2. Keresse meg a rendszerindítási konfigurációs adatkészletet. A következő parancsokban feltételezzük, hogy a csatlakoztatott operációsrendszer-lemezhez rendelt meghajtóbetűjel F. cserélje le a meghajtóbetűjelet a virtuális gép megfelelő értékére.

        bcdedit /store F:\boot\bcd /enum
    Jegyezze fel a **\Windows** mappát tartalmazó partíció azonosítójának nevét. Alapértelmezés szerint az azonosító neve "default".

    Ha a virtuális gép csökkentett módban történő rendszerindításra van konfigurálva, a **Windows rendszerindítási betöltő** szakaszának **safeboot**nevű további jelző jelenik meg. Ha nem látja a **safeboot** jelzőt, ez a cikk nem vonatkozik a forgatókönyvre.

    ![A rendszerindítási azonosítóval kapcsolatos rendszerkép](./media/troubleshoot-rdp-safe-mode/boot-id.png)

3. Távolítsa el a **safeboot** jelzőt, így a virtuális gép normál módba fog indulni:

        bcdedit /store F:\boot\bcd /deletevalue {Default} safeboot
4. Ellenőrizze a rendszerindítási konfigurációs beállításokat, és győződjön meg arról, hogy a **safeboot** jelző el lett távolítva:

        bcdedit /store F:\boot\bcd /enum
5. [Válassza le az operációsrendszer-lemezt, és hozza létre újra a virtuális gépet](../windows/troubleshoot-recovery-disks-portal.md). Ezután győződjön meg arról, hogy a probléma megoldódott-e.
