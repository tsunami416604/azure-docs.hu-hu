---
title: Nem lehet csatlakozni az Azure Virtual Machines távolról, mert a virtuális gép csökkentett módban indul |} A Microsoft Docs
description: Ismerje meg, hogyan háríthatók el a problémát, egy virtuális gép RDP-vel, amelyben nem, mert a virtuális gép csökkentett módban indul. |} A Microsoft Docs
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
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "77918206"
---
#  <a name="cannot-rdp-to-a-vm-because-the-vm-boots-into-safe-mode"></a>Nem hajtható végre egy virtuális gép RDP-vel, mert a virtuális gép csökkentett módban indul.

Ez a cikk bemutatja, hogyan, amelyben nem lehet csatlakoztatni az Azure Windows Virtual Machines (VM), mert a virtuális gép úgy van beállítva. a probléma megoldásához indítsa el a csökkentett módban való.


## <a name="symptoms"></a>Probléma

Nem lehet RDP-kapcsolatok vagy egyéb kapcsolatok (például a HTTP) az Azure-beli virtuális géphez, mert a virtuális gép konfigurálva van, indítsa el a csökkentett mód. Ha a Azure Portal a [rendszerindítási diagnosztika](../troubleshooting/boot-diagnostics.md) képernyőképét, láthatja, hogy a virtuális gép rendesen elindul, de a hálózati adapter nem érhető el:

![Hálózati inferce csökkentett módban bemutató kép](./media/troubleshoot-rdp-safe-mode/network-safe-mode.png)

## <a name="cause"></a>Ok

Az RDP-szolgáltatás nem érhető el csökkentett üzemmódban. Csak alapvető rendszer programok és szolgáltatások letöltése, amikor a virtuális gép csökkentett módban indul. Ez vonatkozik a csökkentett mód, amelyek "Minimális biztonságos indítás" és "Biztonságos rendszerindítási kapcsolattal rendelkező" két különböző verzióit.


## <a name="solution"></a>Megoldás

Mielőtt végrehajtaná ezeket a lépéseket, az érintett virtuális gép operációsrendszer-lemezének pillanatkép készítése a biztonsági mentéséhez. További információ: [lemez pillanatképe](../windows/snapshot-copy-managed-disk.md).

A probléma megoldásához a soros vezérlő használatával konfigurálja a virtuális gépet normál módba való rendszerindításra, vagy [javítsa ki a virtuális gépet](#repair-the-vm-offline) egy helyreállítási virtuális gép használatával.

### <a name="use-serial-control"></a>Soros vezérlőelem használata

1. Kapcsolódjon a [soros konzolhoz, és nyissa meg a cmd-példányt](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
   ). Ha a soros konzol nincs engedélyezve a virtuális gépen, tekintse meg [a virtuális gép offline állapotba](#repair-the-vm-offline)helyezését ismertető témakört.
2. A rendszerindítási konfigurációs adatok ellenőrzése:

        bcdedit /enum

    Ha a virtuális gép csökkentett módban történő rendszerindításra van konfigurálva, a **Windows rendszerindítási betöltő** szakaszának **safeboot**nevű további jelző jelenik meg. Ha nem látja a **safeboot** jelzőt, a virtuális gép nem biztonságos módban van. Ez a cikk nem vonatkozik a forgatókönyvéhez.

    A **safeboot** jelző a következő értékekkel jelenhet meg:
   - Minimális
   - Hálózat

     E két mód közül választhat RDP már nem indulnak el. A javítás ezért változatlan marad.

     ![A csökkentett mód jelzőjét bemutató kép](./media/troubleshoot-rdp-safe-mode/safe-mode-tag.png)

3. Törölje a **safemoade** jelzőt, így a virtuális gép normál módba fog indulni:

        bcdedit /deletevalue {current} safeboot

4. Ellenőrizze a rendszerindítási konfigurációs beállításokat, és győződjön meg arról, hogy a **safeboot** jelző el lett távolítva:

        bcdedit /enum

5. Indítsa újra a virtuális Gépet, és majd ellenőrizze, hogy a probléma megoldódott.

### <a name="repair-the-vm-offline"></a>Javítsa ki a virtuális Gépet kapcsolat nélküli módban

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális Géphez

1. [Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális géphez](../windows/troubleshoot-recovery-disks-portal.md).
2. Indítsa el a helyreállítási virtuális Gépet egy távoli asztali kapcsolatot.
3. Győződjön meg arról, hogy a lemez **online** állapotban van megjelölve a Lemezkezelés konzolon. Vegye figyelembe a meghajtóbetűjelet, amely a csatlakoztatott operációsrendszer-lemez van rendelve.

#### <a name="enable-dump-log-and-serial-console-optional"></a>Engedélyezze a memóriakép naplóját és a soros konzol (nem kötelező)

A memóriakép napló és a soros konzol segíthet számunkra hajtsa végre a további hibaelhárítási, ha a probléma nem lehet feloldani ebben a cikkben a megoldással.

Memóriakép napló és a soros konzol engedélyezéséhez futtassa a következő szkriptet.

1. Nyisson meg egy rendszergazda jogú parancssor-munkamenetet (**Futtatás rendszergazdaként**).
2. Futtassa a következő parancsfájlt:

    Ez a szkript feltételezzük, hogy a meghajtóbetűjel van rendelve a csatlakoztatott operációsrendszer-lemez-e F. cserélje le ezt a meghajtóbetűjelet, a virtuális gép a megfelelő értékkel.

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
