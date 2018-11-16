---
title: Nem lehet csatlakozni az Azure Virtual Machines távolról, mert a virtuális gép csökkentett módban indul |} A Microsoft Docs
description: Ismerje meg, hogyan háríthatók el a problémát, egy virtuális gép RDP-vel, amelyben nem, mert a virtuális gép csökkentett módban indul. |} A Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/13/2018
ms.author: genli
ms.openlocfilehash: 8dfe61430423298eea81510d3e92d49066217a05
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2018
ms.locfileid: "51708761"
---
#  <a name="cannot-rdp-to-a-vm-because-the-vm-boots-into-safe-mode"></a>Nem hajtható végre egy virtuális gép RDP-vel, mert a virtuális gép csökkentett módban indul.

Ez a cikk bemutatja, hogyan, amelyben nem lehet a távoli asztal az Azure Windows Virtual Machines (VM), mert a virtuális gép konfigurálva van a probléma megoldásához indítsa el a csökkentett módban való.

> [!NOTE] 
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk ismerteti a Resource Manager üzemi modell, amely az új központi telepítéseknél helyett a klasszikus üzemi modell használatát javasoljuk. 

## <a name="symptoms"></a>Probléma 

Nem lehet RDP-kapcsolatok és egyéb kapcsolatok (például a HTTP) az Azure-beli virtuális géphez, mert a virtuális gép konfigurálva van, indítsa el a csökkentett mód. Amikor ellenőrizheti a képernyőképen a a [rendszerindítási diagnosztika](../troubleshooting/boot-diagnostics.md) az Azure Portalon, láthatja a virtuális Gépen indul el megfelelően, de a hálózati adapter nem érhető el:

![Hálózati inferce csökkentett módban bemutató kép](./media/troubleshoot-rdp-safe-mode/network-safe-mode.png)

## <a name="cause"></a>Ok

Az RDP-szolgáltatás nem érhető el csökkentett üzemmódban. Csak alapvető rendszer programok és szolgáltatások letöltése, amikor a virtuális gép csökkentett módban indul. Ez vonatkozik a csökkentett mód, amelyek "Minimális biztonságos indítás" és "Biztonságos rendszerindítási kapcsolattal rendelkező" két különböző verzióit.


## <a name="solution"></a>Megoldás 

Mielőtt végrehajtaná ezeket a lépéseket, az érintett virtuális gép operációsrendszer-lemezének pillanatkép készítése a biztonsági mentéséhez. További információkért lásd: [lemez pillanatképének elkészítése](../windows/snapshot-copy-managed-disk.md).

A probléma megoldásához, soros vezérlőelem segítségével konfigurálhatja a virtuális Gépet, indítsa el a normál módban vagy [javítsa ki a virtuális Gépet offline](#repair-the-vm-offline) egy helyreállítási virtuális gép használatával.

### <a name="use-serial-control"></a>Soros vezérlőelem használata

1. Csatlakozás [soros konzolon és a nyílt CMD-példány](./serial-console-windows.md#open-cmd-or-powershell-in-serial-console
). Ha a soros konzol nincs engedélyezve a virtuális Gépen, [javítsa ki a virtuális Gépet offline](#repair-the-vm-offline).
2. A rendszerindítási konfigurációs adatok ellenőrzése: 

        bcdedit /enum

    Ha a virtuális gép úgy van beállítva. csökkentett módban indul, egy extra jelzőt alatt megjelenik a **Windows rendszertöltő** nevű szakaszt **csökkentett mód**. Ha nem látja a **csökkentett mód** jelző, a virtuális gép nem biztonságos módban van. Ez a cikk nem vonatkozik a forgatókönyvéhez.

    A csökkentett mód jelzőjét sikerült jelennek meg a következő értékeket:
    - Minimális
    - Network (Hálózat)

    E két mód bármelyikében RDP nem indul. Így a javítás változatlan marad.

    ![A csökkentett mód jelzőjét bemutató kép](./media/troubleshoot-rdp-safe-mode/safe-mode-tag.png)

3. Törölje a **safemoade** jelzőt, így a virtuális gép normál üzemmódban fog elindulni:

        bcdedit /deletevalue {current} safeboot
        
4. Győződjön meg arról, hogy a rendszer eltávolítja a csökkentett mód jelzőjét, hogy a rendszerindítási konfigurációs adatok ellenőrzése:

        bcdedit /enum

5. Indítsa újra a virtuális Gépet, és majd ellenőrizze, hogy a probléma megoldódott.

### <a name="repair-the-vm-offline"></a>Javítsa ki a virtuális Gépet kapcsolat nélküli módban

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális Géphez

1. [Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális Géphez](../windows/troubleshoot-recovery-disks-portal.md).
2. Indítsa el a helyreállítási virtuális Gépet egy távoli asztali kapcsolatot. 
3. Győződjön meg arról, hogy a lemez megjelölt **Online** a Lemezkezelés konzol. Vegye figyelembe a meghajtóbetűjelet, amely a csatlakoztatott operációsrendszer-lemez van rendelve.

#### <a name="enable-dump-log-and-serial-console-optional"></a>Memóriakép napló és a soros konzolhoz (nem kötelező)

A memóriakép napló és a soros konzol segíthet számunkra hajtsa végre a további hibaelhárítási, ha a probléma nem lehet feloldani ebben a cikkben a megoldással.

Memóriakép napló és a soros konzol engedélyezéséhez futtassa a következő szkriptet.

1. Nyisson meg egy rendszergazda jogú parancssort munkamenetet (**Futtatás rendszergazdaként**).
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

#### Configure the Windows to boot into normal mode

1. Open an elevated command prompt session (**Run as administrator**).
2. Check the boot configuration data. In the following commands, we assume that the drive letter that is assigned to the attached OS disk is F. Replace this drive letter with the appropriate value for your VM. 

        bcdedit /store F:\boot\bcd /enum
    Take note of the Identifier name of the partition that has the **\windows** folder. By default, the  Identifier name is "Default".  

    If the VM is configured to boot into Safe Mode, you will see an extra flag under the **Windows Boot Loader** section called **safeboot**. If you do not see the “safeboot” flag, this article does not apply to your scenario.

    ![The image about boot Identifier](./media/troubleshoot-rdp-safe-mode/boot-id.png)

3. Remove the **safeboot** flag, so the VM will boot into normal mode:

        bcdedit /store F:\boot\bcd /deletevalue {Default} safeboot
4. Check the boot configuration data to make sure that the safeboot flag is removed:

        bcdedit /store F:\boot\bcd /enum
5. [Detach the OS disk and recreate the VM](../windows/troubleshoot-recovery-disks-portal.md). Then check whether the issue is resolved.
