---
title: Az Azure-beli virtuális gép vendég operációs rendszerének tűzfala blokkolja a bejövő forgalmat | Microsoft Docs
description: Ismerje meg, hogy miként javítható a Távoli asztal-portál (RDP) kapcsolódási hibája, hogy a vendég operációs rendszer tűzfala blokkolja a bejövő forgalmat.
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
ms.openlocfilehash: 1b80fc997a4b3d2b472717b1ec2f379a4e958d8c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80422559"
---
# <a name="azure-vm-guest-os-firewall-is-blocking-inbound-traffic"></a>Az Azure-beli virtuális gép vendég operációs rendszerének tűzfala blokkolja a bejövő forgalmat

Ez a cikk azt ismerteti, hogyan lehet kijavítani a Távoli asztal portál (RDP) problémát, amely akkor fordul elő, ha a vendég operációs rendszer tűzfala blokkolja a bejövő forgalmat.

## <a name="symptoms"></a>Probléma

Azure-beli virtuális géphez (VM) való kapcsolódáshoz nem használható RDP-kapcsolat. A rendszerindítási diagnosztika – > képernyőképen látható, hogy az operációs rendszer teljes mértékben be van töltve az üdvözlőképernyőn (Ctrl + Alt + Del).

## <a name="cause"></a>Ok

### <a name="cause-1"></a>1. ok

Az RDP-szabály úgy van beállítva, hogy engedélyezze az RDP-forgalmat.

### <a name="cause-2"></a>2. ok

A vendég rendszertűzfal profiljai az összes bejövő kapcsolat blokkolására vannak beállítva, beleértve az RDP-forgalmat is.

![Tűzfalbeállítások](./media/guest-os-firewall-blocking-inbound-traffic/firewall-advanced-setting.png)

## <a name="solution"></a>Megoldás

Az alábbi lépések elvégzése előtt készítsen pillanatképet az érintett virtuális gép rendszerlemezéről biztonsági másolatként. További információ: [lemez pillanatképe](../windows/snapshot-copy-managed-disk.md).

A probléma megoldásához használja az Azure-beli [virtuális gépekkel kapcsolatos hibák elhárításához használható távoli eszközöket](remote-tools-troubleshoot-azure-vm-issues.md) , hogy távolról csatlakozzon a virtuális géphez, majd szerkessze a vendég operációs rendszer tűzfalszabályok az RDP-forgalom **engedélyezéséhez** .

### <a name="online-troubleshooting"></a>Online hibaelhárítás

Kapcsolódjon a [soros konzolhoz, és nyisson meg egy PowerShell-példányt](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Ha a soros konzol nincs engedélyezve a virtuális gépen, lépjen a következő helyre: "[a virtuális gép kijavítása offline állapotú](troubleshoot-rdp-internal-error.md#repair-the-vm-offline).

#### <a name="mitigation-1"></a>1. enyhítés

1.  Ha az Azure-ügynök telepítve van és megfelelően működik a virtuális gépen, akkor a virtuális gép menüben a **támogatás és hibaelhárítás**  >  **alaphelyzetbe állítása jelszó** lehetőség alatt a "konfiguráció visszaállítása" lehetőséget használhatja.

2.  A helyreállítási lehetőség futtatása a következő műveleteket végzi el:

    *   Engedélyezi egy RDP-összetevő használatát, ha az le van tiltva.

    *   Engedélyezi az összes Windows tűzfal-profilt.

    *   Győződjön meg arról, hogy az RDP-szabály be van kapcsolva a Windows tűzfalban.

    *   Ha az előző lépések nem működnek, manuálisan állítsa alaphelyzetbe a tűzfalszabály beállításait. Ehhez a következő parancs futtatásával kérdezheti le az összes olyan szabályt, amely a "Távoli asztal" nevet tartalmazza:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(Name.*Remote Desktop)" -context 9,4 | more
        ```

        Ha az RDP-port a 3389-től eltérő más portra lett beállítva, akkor meg kell találnia minden olyan egyéni szabályt, amely létrehozva lett, és erre a portra lett beállítva. Az egyéni portot tartalmazó bejövő szabályok lekérdezéséhez futtassa a következő parancsot:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<CUSTOM PORT>)" -context 9,4 | more
        ```

3.  Ha úgy látja, hogy a szabály le van tiltva, engedélyezze azt. Egy egész csoport, például a beépített Távoli asztal csoport megnyitásához futtassa a következő parancsot:

    ```cmd
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
    ```

    Ellenkező esetben az adott Távoli asztal (TCP-in) szabály megnyitásához futtassa a következő parancsot:

    ```cmd
    netsh advfirewall firewall set rule name="<CUSTOM RULE NAME>" new enable=yes
    ```

4.  Hibaelhárításhoz a tűzfal profiljait kikapcsolhatja:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Miután befejezte a hibaelhárítást, és helyesen beállította a tűzfalat, engedélyezze újra a tűzfalat.

    > [!Note]
    > A módosítások érvénybe léptetéséhez nem kell újraindítani a virtuális gépet.

5.  A virtuális géphez való hozzáféréshez próbáljon RDP-kapcsolatot létrehozni.

#### <a name="mitigation-2"></a>2. mérséklés

1.  A tűzfal profiljainak lekérdezése annak megállapításához, hogy a bejövő tűzfal házirendje *BlockInboundAlways*van-e beállítva:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    ![AllProfiles](./media/guest-os-firewall-blocking-inbound-traffic/firewall-profiles.png)

    > [!Note]
    > A következő irányelvek érvényesek a tűzfal-házirendre, attól függően, hogy milyen módon van beállítva:
    >    * *BlockInbound*: minden bejövő forgalom le lesz tiltva, ha nem rendelkezik a forgalom engedélyezésére vonatkozó szabállyal.
    >    * *BlockInboundAlways*: minden tűzfalszabály figyelmen kívül lesz hagyva, és az összes forgalom le lesz tiltva.

2.  Szerkessze a *DefaultInboundAction* , és állítsa be ezeket a profilokat a forgalom **engedélyezéséhez** . Ehhez futtassa az alábbi parancsot:

    ```cmd
    netsh advfirewall set allprofiles firewallpolicy allowinbound,allowoutbound
    ```

3.  A profilok ismételt lekérdezésével győződjön meg arról, hogy a módosítás sikeresen létrejött. Ehhez futtassa az alábbi parancsot:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    > [!Note]
    > A módosítások érvénybe léptetéséhez nem kell újraindítani a virtuális gépet.

4.  Próbálkozzon újra a virtuális gép RDP-kapcsolaton keresztüli elérésével.

### <a name="offline-mitigations"></a>Offline megoldás

1.  [Csatlakoztassa a rendszerlemezt egy helyreállítási virtuális géphez](troubleshoot-recovery-disks-portal-windows.md).

2.  Távoli asztal-Kapcsolódás elindítása a helyreállítási virtuális géphez.

3.  Győződjön meg arról, hogy a lemez **online** állapotban van megjelölve a Lemezkezelés konzolon. Jegyezze fel a csatlakoztatott rendszerlemezhez rendelt meghajtóbetűjelet.

#### <a name="mitigation-1"></a>1. enyhítés

Lásd: a [Tűzfalszabály letiltása egy vendég operációs rendszeren](enable-disable-firewall-rule-guest-os.md).

#### <a name="mitigation-2"></a>2. mérséklés

1.  [Csatlakoztassa a rendszerlemezt egy helyreállítási virtuális géphez](troubleshoot-recovery-disks-portal-windows.md).

2.  Távoli asztal-Kapcsolódás elindítása a helyreállítási virtuális géphez.

3.  Miután a rendszerlemez csatlakoztatva lett a helyreállítási virtuális géphez, ellenőrizze, hogy a lemez **online** állapotban van-e megjelölve a Lemezkezelés konzolon. Jegyezze fel a csatlakoztatott operációsrendszer-lemezhez rendelt meghajtóbetűjelet.

4.  Nyisson meg egy rendszergazda jogú CMD-példányt, majd futtassa a következő parancsfájlt:

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

5.  [Válassza le a rendszerlemezt, és hozza létre újra a virtuális gépet](troubleshoot-recovery-disks-portal-windows.md).

6.  Győződjön meg arról, hogy a probléma megoldódott-e.
