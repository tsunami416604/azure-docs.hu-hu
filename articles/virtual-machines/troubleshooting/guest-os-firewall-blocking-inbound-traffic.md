---
title: Az Azure virtuális gép vendég operációs rendszertűzfala blokkolja a bejövő forgalmat | Microsoft dokumentumok
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: 0cbd1a24f5c460e248d55777735da6809befba63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72028793"
---
# <a name="azure-vm-guest-os-firewall-is-blocking-inbound-traffic"></a>Az Azure virtuális gép vendég operációsrendszer-tűzfala blokkolja a bejövő forgalmat

Ez a cikk azt ismerteti, hogyan lehet kijavítani a Távoli asztali portál (RDP) azon problémát, amely akkor fordul elő, ha a vendég operációs rendszer tűzfala blokkolja a bejövő forgalmat.

## <a name="symptoms"></a>Probléma

RdP-kapcsolat nem használható azure-beli virtuális géphez (VM) való csatlakozáshoz. A Boot diagnostics -> Screenshot, azt mutatja, hogy az operációs rendszer teljesen bevan töltve az üdvözlőképernyőn (Ctrl+Alt +Del).

## <a name="cause"></a>Ok

### <a name="cause-1"></a>1. ok

Az RDP-szabály nincs beállítva az RDP-forgalom engedélyezéséhez.

### <a name="cause-2"></a>2. ok

A vendégrendszer tűzfalprofiljai úgy vannak beállítva, hogy blokkolják az összes bejövő kapcsolatot, beleértve az RDP-forgalmat is.

![Tűzfal beállítása](./media/guest-os-firewall-blocking-inbound-traffic/firewall-advanced-setting.png)

## <a name="solution"></a>Megoldás

Az alábbi lépések végrehajtása előtt készítsen pillanatképet az érintett virtuális gép rendszerlemezéről biztonsági másolatként. További információt a [Lemez pillanatképe](../windows/snapshot-copy-managed-disk.md)című témakörben talál.

A probléma megoldásához használja a távoli eszközök használatával az [Azure virtuális gépkel kapcsolatos problémák elhárításához](remote-tools-troubleshoot-azure-vm-issues.md) használt módszerek egyikét a virtuális géphez való távoli csatlakozáshoz, majd az RDP-forgalom **engedélyezéséhez** a vendég operációs rendszer tűzfalszabályainak szerkesztésével.

### <a name="online-troubleshooting"></a>Online hibaelhárítás

Csatlakozzon a [soros konzolhoz, majd nyisson meg egy PowerShell-példányt.](serial-console-windows.md#use-cmd-or-powershell-in-serial-console) Ha a soros konzol nincs engedélyezve a virtuális gép, nyissa meg a "[Javítsa ki a virtuális gép offline](troubleshoot-rdp-internal-error.md#repair-the-vm-offline).

#### <a name="mitigation-1"></a>1. kockázatcsökkentés

1.  Ha az Azure Agent telepítve van, és megfelelően működik a virtuális gépen, használhatja a "Konfiguráció visszaállítása csak" lehetőséget a **Támogatás + hibaelhárítás** > **jelszó visszaállítása** a virtuális gép menüben.

2.  A helyreállítási beállítás futtatása a következőket teszi:

    *   Engedélyezi az RDP-összetevőt, ha az le van tiltva.

    *   Engedélyezi az összes Windows tűzfalprofilt.

    *   Győződjön meg arról, hogy az RDP-szabály be van kapcsolva a Windows tűzfalban.

    *   Ha az előző lépések nem működnek, manuálisan állítsa alaphelyzetbe a tűzfalszabályt. Ehhez a következő parancs futtatásával kérdezze le a "Távoli asztal" nevet tartalmazó összes szabályt:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(Name.*Remote Desktop)" -context 9,4 | more
        ```

        Ha az RDP-port a 3389-estől eltérő portra volt beállítva, meg kell találnia minden olyan egyéni szabályt, amely et létrehozták, és erre a portra állították. Ha az egyéni porttal rendelkező összes bejövő szabályt le szeretné kérdezni, futtassa a következő parancsot:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<CUSTOM PORT>)" -context 9,4 | more
        ```

3.  Ha azt látja, hogy a szabály le van tiltva, engedélyezze azt. Ha egy egész csoportot, például a beépített Távoli asztal csoportot szeretne megnyitni, futtassa a következő parancsot:

    ```cmd
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
    ```

    Ellenkező esetben az adott Távoli asztal (TCP-In) szabály megnyitásához futtassa a következő parancsot:

    ```cmd
    netsh advfirewall firewall set rule name="<CUSTOM RULE NAME>" new enable=yes
    ```

4.  Hibaelhárításhoz a tűzfalprofilokat kikapcsolva:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    A hibaelhárítás befejezése és a tűzfal helyes beállítása után engedélyezze újra a tűzfalat.

    > [!Note]
    > Nem kell újraindítani a virtuális gép ezeket a módosításokat.

5.  Próbáljon meg rdp-kapcsolatot létesíteni a virtuális gép eléréséhez.

#### <a name="mitigation-2"></a>2. kockázatcsökkentés

1.  A tűzfalprofilok lekérdezése annak megállapításához, hogy a bejövő tűzfalházirend *BlockInboundAlways (BlockInboundAlways)* beállításra van-e állítva:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    ![Mindenprofil](./media/guest-os-firewall-blocking-inbound-traffic/firewall-profiles.png)

    > [!Note]
    > A tűzfalházirendre az alábbi irányelvek vonatkoznak, attól függően, hogy hogyan van beállítva:
    >    * *BlockInbound*: Minden bejövő forgalom blokkolva lesz, kivéve, ha van egy szabály érvényben, amely lehetővé teszi, hogy a forgalom.
    >    * *BlockInboundAlways*: Minden tűzfalszabály figyelmen kívül lesz hagyva, és minden forgalom blokkolva lesz.

2.  A *DefaultInboundAction* szerkesztéséhez állítsa be ezeket a profilokat a forgalom **engedélyezése beállítására.** Ehhez futtassa az alábbi parancsot:

    ```cmd
    netsh advfirewall set allprofiles firewallpolicy allowinbound,allowoutbound
    ```

3.  A módosítások sikeressítése érdekében ismét kérdezze le a profilokat. Ehhez futtassa az alábbi parancsot:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    > [!Note]
    > A módosítások alkalmazásához nem kell újraindítania a virtuális számítógépet.

4.  Próbálkozzon újra a virtuális gép RDP-n keresztüli eléréséhez.

### <a name="offline-mitigations"></a>Kapcsolat nélküli megoldások

1.  [Csatlakoztassa a rendszerlemezt egy helyreállítási virtuális géphez.](troubleshoot-recovery-disks-portal-windows.md)

2.  Távoli asztali kapcsolat indítása a helyreállítási virtuális géppel.

3.  Győződjön meg arról, hogy a lemez **online** ként van megjelölve a Lemezkezelés konzolon. Jegyezze fel a csatlakoztatott rendszerlemezhez rendelt meghajtóbetűjelet.

#### <a name="mitigation-1"></a>1. kockázatcsökkentés

Lásd: [Tűzfalszabály engedélyezése-letiltása vendég operációs rendszeren](enable-disable-firewall-rule-guest-os.md).

#### <a name="mitigation-2"></a>2. kockázatcsökkentés

1.  [Csatlakoztassa a rendszerlemezt egy helyreállítási virtuális géphez.](troubleshoot-recovery-disks-portal-windows.md)

2.  Távoli asztali kapcsolat indítása a helyreállítási virtuális géppel.

3.  Miután a rendszerlemez csatlakoztatva van a helyreállítási virtuális géphez, győződjön meg arról, hogy a lemez **online** ként van megjelölve a Lemezkezelés konzolon. Jegyezze fel a csatlakoztatott operációsrendszer-lemezhez rendelt meghajtóbetűjelet.

4.  Nyisson meg egy emelt szintű CMD-példányt, majd futtassa a következő parancsfájlt:

    ```cmd
    REM Backup the registry prior doing any change
    robocopy f:\windows\system32\config f:\windows\system32\config.BACK /MT

    REM Mount the hive
    reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM

    REM Delete the keys to block all inbound connection scenario
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile" /v DoNotAllowExceptions

    REM Unmount the hive
    reg unload HKLM\BROKENSYSTEM
    ```

5.  [Válassza le a rendszerlemezt, és hozza létre újra a virtuális gép](troubleshoot-recovery-disks-portal-windows.md).

6.  Ellenőrizze, hogy megoldódott-e a probléma.
