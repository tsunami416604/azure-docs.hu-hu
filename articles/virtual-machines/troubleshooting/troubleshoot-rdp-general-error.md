---
title: Általános RDP-hiba megoldása az Azure-beli Windows rendszerű virtuális gépeken | Microsoft Docs
description: Megtudhatja, hogyan lehet elhárítani az RDP általános hibáit az Azure-beli Windows rendszerű virtuális gépeken | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: df318fea4960601dcbfa84149fdc47bedc9104a2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87079825"
---
# <a name="troubleshoot-an-rdp-general-error-in-azure-vm"></a>Az RDP általános hibáinak megoldása az Azure-beli virtuális gépen

Ez a cikk általános hibát ismertet, ha egy Azure-beli Windows rendszerű virtuális géphez (VM) RDP protokoll (RDP) való csatlakozást végez.

## <a name="symptom"></a>Hibajelenség

Amikor RDP-kapcsolattal rendelkezik az Azure-beli Windows rendszerű virtuális géppel, a következő általános hibaüzenet jelenhet meg:

**Távoli asztal nem tud csatlakozni a távoli számítógéphez a következő okok valamelyike miatt:**

1. **A kiszolgálóhoz való távoli hozzáférés nincs engedélyezve**

2. **A távoli számítógép ki van kapcsolva**

3. **A távoli számítógép nem érhető el a hálózaton**

**Ellenőrizze, hogy a távoli számítógép be van-e kapcsolva és csatlakoztatva van-e a hálózathoz, és hogy engedélyezve van-e a távoli hozzáférés.**

## <a name="cause"></a>Ok

Ez a probléma a következő okok miatt fordulhat elő:

### <a name="cause-1"></a>1. ok

Az RDP-összetevő a következőképpen van Letiltva:

- Az összetevő szintjén
- A figyelő szintjén
- A terminálkiszolgálón
- Távoli asztal munkamenet-gazdagép szerepkör

### <a name="cause-2"></a>2. ok

A Távoli asztali szolgáltatások (TermService) nem fut.

### <a name="cause-3"></a>3. ok

Az RDP-figyelő helytelenül van konfigurálva.

## <a name="solution"></a>Megoldás

Az alábbi lépések elvégzése előtt készítsen pillanatképet az érintett virtuális gép operációsrendszer-lemezéről biztonsági másolatként. A probléma megoldásához használja a soros vezérlőelemet, vagy javítsa ki a virtuális gépet kapcsolat nélküli üzemmódban.

### <a name="serial-console"></a>Soros konzol

#### <a name="step-1-open-cmd-instance-in-serial-console"></a>1. lépés: a CMD-példány megnyitása a Serial consoleban

1. A [soros konzolt](serial-console-windows.md) a **support & hibaelhárítás**  >  **Serial Console (előzetes verzió)** lehetőség kiválasztásával érheti el. Ha a szolgáltatás engedélyezve van a virtuális gépen, akkor a virtuális gép sikeresen csatlakoztatható.

2. Hozzon létre egy új csatornát egy CMD-példányhoz. Írja be a **cmd** parancsot a csatorna nevének lekéréséhez.

3. Váltson át a CMD-példányt futtató csatornára, ebben az esetben az 1. csatornát kell megadnia.

   ```
   ch -si 1
   ```

#### <a name="step-2-check-the-values-of-rdp-registry-keys"></a>2. lépés: az RDP-beállításkulcsok értékének megkeresése:

1. Ellenőrizze, hogy az RDP le van-e tiltva a csoportba tartozó házirendek alapján.

    ```
    REM Get the group policy 
    reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections
    ```
    Ha a csoportházirend tiltja, hogy az RDP le van tiltva (a fDenyTSConnections értéke 0x1), futtassa a következő parancsot a TermService szolgáltatás engedélyezéséhez. Ha a beállításkulcs nem található, nincs olyan csoportházirend konfigurálva, amely letiltja az RDP-t. A következő lépésre léphet.

    ```
    REM update the fDenyTSConnections value to enable TermService service
    reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0 /f
    ```
    > [!NOTE]
    > Ez a lépés átmenetileg engedélyezi a TermService szolgáltatást. A rendszer alaphelyzetbe állítja a változást a csoportházirend-beállítások frissítésekor. A probléma megoldásához ellenőrizze, hogy az TermService szolgáltatást letiltotta-e a helyi csoportházirend vagy a tartományi csoportházirend, majd frissítse a házirend-beállításokat ennek megfelelően.
    
2. A távoli kapcsolatok aktuális konfigurációjának megtekintése.
    ```
    REM Get the local remote connection setting
    reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections
    ```
    Ha a parancs 0x1 ad vissza, a virtuális gép nem engedélyezi a távoli kapcsolatokat. Ezután engedélyezze a távoli kapcsolatokat a következő parancs használatával:
     ```
     reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
     ```
    
1. Keresse meg a terminálkiszolgáló aktuális konfigurációját.

    ```
    REM Get the local remote connection setting
    reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled
    ```

      Ha a parancs 0 értéket ad vissza, a terminálkiszolgáló le van tiltva. Ezután engedélyezze a terminálkiszolgálót a következőképpen:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f
      ```

3. A terminálkiszolgálói modul a kiürítési módra van beállítva, ha a kiszolgáló egy terminálkiszolgálói farmon (RDS vagy Citrix) található. A terminálkiszolgáló-modul aktuális üzemmódjának megtekintése.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode
      ```

      Ha a parancs 1 értéket ad vissza, a terminálkiszolgáló modulja kiürítési módba van állítva. Ezután állítsa a modult a következőre:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f
      ```

4. Győződjön meg arról, hogy tud-e csatlakozni a terminálkiszolgálóhoz.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled
      ```

      Ha a parancs 1 értéket ad vissza, nem tud csatlakozni a terminálkiszolgálóhoz. Ezután engedélyezze a következőt a kapcsolatban:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f
      ```
5. Keresse meg az RDP-figyelő aktuális konfigurációját.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation
      ```

      Ha a parancs 0 értéket ad vissza, az RDP-figyelő le van tiltva. Ezután engedélyezze a figyelőt a következőképpen:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f
      ```

6. Győződjön meg arról, hogy tud-e csatlakozni az RDP-figyelőhöz.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled
      ```

   Ha a parancs 1 értéket ad vissza, nem tud csatlakozni az RDP-figyelőhöz. Ezután engedélyezze a következőt a kapcsolatban:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f
      ```

7. Indítsa újra a virtuális gépet.

8. Lépjen ki a CMD-példányból `exit` , írja be a parancsot, majd nyomja le kétszer az **ENTER billentyűt** .

9. Indítsa újra a virtuális gépet a paranccsal `restart` , majd kapcsolódjon a virtuális géphez.

Ha a probléma továbbra is fennáll, lépjen a 2. lépésre.

#### <a name="step-2-enable-remote-desktop-services"></a>2. lépés: a távoli asztali szolgáltatások engedélyezése

További információ: [Távoli asztali szolgáltatások nem egy Azure-beli virtuális gépen indul](troubleshoot-remote-desktop-services-issues.md)el.

#### <a name="step-3-reset-rdp-listener"></a>3. lépés: RDP-figyelő visszaállítása

További információ: [Távoli asztal gyakori leválasztása az Azure-beli virtuális gépen](troubleshoot-rdp-intermittent-connectivity.md).

### <a name="offline-repair"></a>Offline javítás

#### <a name="step-1-turn-on-remote-desktop"></a>1. lépés: a Távoli asztal bekapcsolása

1. [Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális géphez](./troubleshoot-recovery-disks-portal-windows.md).
2. Távoli asztal-Kapcsolódás elindítása a helyreállítási virtuális géphez.
3. Győződjön meg arról, hogy a lemez **online** állapotban van megjelölve a Lemezkezelés konzolon. Jegyezze fel a csatlakoztatott operációsrendszer-lemezhez rendelt meghajtóbetűjelet.
4. Távoli asztal-Kapcsolódás elindítása a helyreállítási virtuális géphez.
5. Nyisson meg egy rendszergazda jogú parancssor-munkamenetet (**Futtatás rendszergazdaként**). Futtassa a következő parancsfájlokat. Ebben a parancsfájlban feltételezzük, hogy a csatlakoztatott operációsrendszer-lemezhez rendelt meghajtóbetűjel F. cserélje le a meghajtóbetűjelet a virtuális gép megfelelő értékére.

      ```
      reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv 
      reg load HKLM\BROKENSOFTWARE F:\windows\system32\config\SOFTWARE.hiv 
 
      REM Ensure that Terminal Server is enabled 

      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f 

      REM Ensure Terminal Service is not set to Drain mode 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f 

      REM Ensure Terminal Service has logon enabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f 

      REM Ensure the RDP Listener is not disabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f 

      REM Ensure the RDP Listener accepts logons 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f 

      REM RDP component is enabled 
      reg add "HKLM\BROKENSYSTEM\ControlSet001\control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSYSTEM\ControlSet002\control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f 
      reg add "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0 /f 

      reg unload HKLM\BROKENSYSTEM 
      reg unload HKLM\BROKENSOFTWARE 
      ```

6. Ha a virtuális gép tartományhoz csatlakozik, a következő beállításkulcs segítségével ellenőrizze, hogy van-e olyan csoportházirend, amely letiltja az RDP-t. 

      ```
      HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services\fDenyTSConnectionS
      ```

      Ha a kulcs értéke 1, ez azt jelenti, hogy az RDP-t letiltja a házirend. Az Távoli asztal csoportházirend-objektumon keresztüli engedélyezéséhez módosítsa a következő házirendet a tartományvezérlőről:

   
      **Számítógépes Computer-sablonok:**

      A szabályzat definitions\Windows Windows-összetevők asztali Windows-összetevők asztali munkamenet-Host\Connections\Allow a felhasználókat a Távoli asztali szolgáltatások használatával történő távoli kapcsolódáshoz
  
1. Válassza le a lemezt a mentési virtuális gépről.
1. [Hozzon létre egy új virtuális gépet a lemezről](../windows/create-vm-specialized.md).

Ha a probléma továbbra is fennáll, lépjen a 2. lépésre.

#### <a name="step-2-enable-remote-desktop-services"></a>2. lépés: a távoli asztali szolgáltatások engedélyezése

További információ: [Távoli asztali szolgáltatások nem egy Azure-beli virtuális gépen indul](troubleshoot-remote-desktop-services-issues.md)el.

#### <a name="step-3-reset-rdp-listener"></a>3. lépés: RDP-figyelő visszaállítása

További információ: [Távoli asztal gyakori leválasztása az Azure-beli virtuális gépen](troubleshoot-rdp-intermittent-connectivity.md).

## <a name="need-help-contact-support"></a>Segítségre van szüksége? Kapcsolatfelvétel a támogatási szolgáltatással

Ha további segítségre van szüksége, [vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.
