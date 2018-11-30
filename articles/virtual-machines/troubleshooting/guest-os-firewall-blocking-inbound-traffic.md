---
title: Az Azure virtuális gép vendég operációs rendszer tűzfal blokkolja a bejövő forgalom |} A Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: willchen
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: 3ddd2f122de832654be295c5978a88bec702558c
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52319434"
---
# <a name="azure-vm-guest-os-firewall-is-blocking-inbound-traffic"></a>Az Azure virtuális gép vendég operációs rendszer tűzfal blokkolja a bejövő forgalom

Ez a cikk ismerteti, hogyan háríthatja el a távoli asztali Portal (RDP) a probléma akkor jelentkezik, ha a vendég operációs rendszer tűzfala blokkolja a bejövő forgalom.

## <a name="symptoms"></a>Probléma

Nem használhat RDP-kapcsolatának szeretne csatlakozni egy Azure virtuális gép (VM). A rendszerindítási diagnosztika képernyőképe ->, akkor látható, hogy az operációs rendszer teljes betöltése az üdvözlőképernyőn (Ctrl + Alt + Del).

## <a name="cause"></a>Ok

### <a name="cause-1"></a>OK: 1 

Az RDP-szabályban nincs beállítva, hogy az RDP-forgalmat.

### <a name="cause-2"></a>OK 2 

A Vendég-tűzfal profilok beállítása az összes bejövő kapcsolat blokkolása, ideértve az RDP-forgalmat.

![Tűzfalbeállítás](./media/guest-os-firewall-blocking-inbound-traffic/firewall-advanced-setting.png)

## <a name="solution"></a>Megoldás 

Mielőtt végrehajtaná ezeket a lépéseket, pillanatkép készítése a rendszerlemezt az érintett virtuális gép biztonsági mentéséhez. További információkért lásd: [lemez pillanatképének elkészítése](../windows/snapshot-copy-managed-disk.md).

A probléma megoldásához használja a módszer [táveszközök használata Azure virtuális gép hibáinak elhárítása](remote-tools-troubleshoot-azure-vm-issues.md) távolról csatlakozhat a virtuális Gépet, és a vendég operációs rendszer tűzfal-szabályokat, majd szerkessze **engedélyezése** RDP-forgalmat .

### <a name="online-troubleshooting"></a>Online hibáinak elhárítása

Csatlakozás a [soros konzolon, és nyissa meg egy PowerShell-példány](serial-console-windows.md#open-cmd-or-powershell-in-serial-console). Ha a soros konzol nincs engedélyezve a virtuális gépen, lépjen a "[javítsa ki a virtuális gép Offline](troubleshoot-rdp-internal-error.md#repair-the-vm-offline).

#### <a name="mitigation-1"></a>1. megoldás

1.  Ha az Azure-ügynök telepítve van és megfelelően működik, a virtuális gépen, használhatja a "Csak a konfiguráció alaphelyzetbe állítása" lehetőség alatt **támogatás + hibaelhárítás** > **jelszó alaphelyzetbe állítása** a virtuális gép menüben.

2.  A helyreállítási futtatják a következőket teszi:

    *   Lehetővé teszi egy RDP-összetevő, ha le van tiltva.

    *   Lehetővé teszi az összes Windows tűzfalprofilt.

    *   Győződjön meg arról, hogy az RDP-szabályban engedélyezve van a Windows tűzfalon.

    *   Ha az előző lépések nem működnek, manuálisan állítsa vissza a tűzfalszabály. Ehhez kérdezze a szabályokat, amelyek tartalmazzák a "Távoli asztali" neve a következő parancs futtatásával:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(Name.*Remote Desktop)" -context 9,4 | more
        ```

        Az RDP-port a 3389-es nem bármely más portot adta meg, akkor keresse meg minden olyan egyéni szabályt, amely előfordulhat, hogy létrehozott és a port beállítása. A bejövő szabályok, amelyek rendelkeznek egy egyéni portot lekérdezéséhez futtassa a következő parancsot:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<CUSTOM PORT>)" -context 9,4 | more
        ```

3.  Ha látja, hogy a szabály le van tiltva, engedélyezze azt. Nyisson meg egy teljes csoport, mint a beépített távoli asztali, futtassa a következő parancsot:

    ```cmd
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
    ```
    
    Nyissa meg az adott szabályokat a távoli asztal (TCP, bejövő), ellenkező esetben futtassa a következő parancsot:

    ```cmd
    netsh advfirewall firewall set rule name="<CUSTOM RULE NAME>" new enable=yes
    ```

4.  Hibaelhárítási, tűzfalprofilt, amelyek ki is kapcsolhatja:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Hibaelhárítás és a tűzfal megfelelően beállítás befejezése után engedélyezze újra a tűzfalat.
    
    > [!Note]
    > Nem kell újraindítani a virtuális Gépet a alkalmazni ezeket a módosításokat.

5.  Próbálja meg egy RDP-kapcsolatot a virtuális gép eléréséhez.

#### <a name="mitigation-2"></a>2. megoldás

1.  Határozza meg, akár a tűzfaltámogatás bejövő adatforgalmához szabályzat úgy van-e beállítva tűzfalprofilt lekérdezése *BlockInboundAlways*:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    ![AllProfiles](./media/guest-os-firewall-blocking-inbound-traffic/firewall-profiles.png)

    > [!Note]
    > A tűzfal-házirendet, attól függően, hogyan állítsa be a következőkre vonatkoznak:
    >    * *BlockInbound*: minden bejövő forgalom le lesz tiltva, ha nincsenek érvényben, amely lehetővé teszi, hogy a forgalmat egy szabályt.
    >    * *BlockInboundAlways*: figyelmen kívül az összes tűzfalszabályt, és minden forgalom le lesz tiltva.

2.  Szerkessze a *DefaultInboundAction* , ha szeretné ezeket a profilokat **engedélyezése** forgalmat. Ehhez futtassa a következő parancsot:

    ```cmd
    netsh advfirewall set allprofiles firewallpolicy allowinbound,allowoutbound
    ```

3.  A lekérdezés a profilokat újra győződjön meg arról, hogy a módosítás sikeresen létrejött-e. Ehhez futtassa a következő parancsot:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    > [!Note]
    > Nem kell újraindítani a virtuális Gépet, a módosítások életbe léptetéséhez. 

4.  Próbálja meg újból elérni a virtuális gép RDP-Kapcsolaton keresztül.

### <a name="offline-mitigations"></a>A kapcsolat nélküli megoldások 

1.  [A rendszer lemez csatolása egy helyreállítási virtuális Géphez](troubleshoot-recovery-disks-portal-windows.md).

2.  Indítsa el a helyreállítási virtuális Gépet egy távoli asztali kapcsolatot.

3.  Győződjön meg arról, hogy a lemez megjelölt **Online** a Lemezkezelés konzol. Vegye figyelembe a meghajtóbetűjelet, amely a csatolt rendszerlemez van rendelve.

#### <a name="mitigation-1"></a>1. megoldás

Lásd: [hogyan engedélyezzen vagy tiltson le egy tűzfal, a vendég operációs rendszer DOC a szabály]().

#### <a name="mitigation-2"></a>2. megoldás

1.  [A rendszer lemez csatolása egy helyreállítási virtuális Géphez](troubleshoot-recovery-disks-portal-windows.md).

2.  Indítsa el a helyreállítási virtuális Gépet egy távoli asztali kapcsolatot.

3.  Miután a rendszer lemez csatolva van a helyreállítási virtuális Gépet, győződjön meg arról, hogy a lemez megjelölt **Online** a Lemezkezelés konzol. Vegye figyelembe a meghajtóbetűjelet, amely a csatlakoztatott operációsrendszer-lemez van rendelve.

4.  Nyisson meg egy rendszergazda jogú CMD-példányt, és futtassa a következő parancsfájlt:

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

5.  [Válassza le a rendszer lemezt, és hozza létre újból a virtuális gép](troubleshoot-recovery-disks-portal-windows.md).

6.  Ellenőrizze, hogy a probléma megoldódott.
