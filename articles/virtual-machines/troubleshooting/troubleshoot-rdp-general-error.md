---
title: Általános RDP-hiba megoldása az Azure-beli Windows rendszerű virtuális gépeken | Microsoft Docs
description: Megtudhatja, hogyan lehet elhárítani az RDP általános hibáit az Azure-beli Windows rendszerű virtuális gépeken | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: abff12e1a304c51cb0df394534c7da0a35518008
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70089797"
---
# <a name="troubleshoot-an-rdp-general-error-in-azure-vm"></a>Az RDP általános hibáinak megoldása az Azure-beli virtuális gépen

Ez a cikk általános hibát ismertet, ha egy Azure-beli Windows rendszerű virtuális géphez (VM) RDP protokoll (RDP) való csatlakozást végez.

## <a name="symptom"></a>Jelenség

Amikor RDP-kapcsolattal rendelkezik az Azure-beli Windows rendszerű virtuális géppel, a következő általános hibaüzenet jelenhet meg:

**A távoli asztal az alábbi okok valamelyike nem tud kapcsolódni a távoli számítógéphez:**

1. **Távoli hozzáférés a kiszolgálón nincs engedélyezve**

2. **A távoli számítógép ki van kapcsolva.**

3. **A távoli számítógép nem érhető el a hálózaton**

**Ellenőrizze, hogy a távoli számítógéphez van kapcsolva, és csatlakozik a hálózathoz, és, hogy a távoli hozzáférés engedélyezve van-e.**

## <a name="cause"></a>Ok

Ez a probléma a következő okok miatt fordulhat elő:

### <a name="cause-1"></a>OK: 1

Az RDP-összetevő a következőképpen van Letiltva:

- Az összetevő szintjén
- A figyelő szintjén
- A terminálkiszolgálón
- Távoli asztal munkamenet-gazdagép szerepkör

### <a name="cause-2"></a>OK 2

A Távoli asztali szolgáltatások (TermService) nem fut.

### <a name="cause-3"></a>3\. ok

Az RDP-figyelő helytelenül van konfigurálva.

## <a name="solution"></a>Megoldás

A probléma megoldásához végezze el [az operációsrendszer-lemez biztonsági mentését](../windows/snapshot-copy-managed-disk.md), és [csatlakoztassa az operációs rendszer lemezét egy mentési virtuális géphez](troubleshoot-recovery-disks-portal-windows.md), majd kövesse a lépéseket.

### <a name="serial-console"></a>Soros konzol

#### <a name="step-1-open-cmd-instance-in-serial-console"></a>1\. lépés: A CMD-példány megnyitása a Serial consoleban

1. A [soros konzolt](serial-console-windows.md) a **support & hibaelhárítás** > **Serial Console (előzetes verzió)** lehetőség kiválasztásával érheti el. Ha a szolgáltatás engedélyezve van a virtuális gépen, akkor a virtuális gép sikeresen csatlakoztatható.

2. Hozzon létre egy új csatornát egy CMD-példányhoz. Írja be a **cmd** parancsot a csatorna nevének lekéréséhez.

3. Váltson át a CMD-példányt futtató csatornára, ebben az esetben az 1. csatornát kell megadnia.

   ```
   ch -si 1
   ```

#### <a name="step-2-check-the-values-of-rdp-registry-keys"></a>2\. lépés: Az RDP-beállításkulcsok értékének keresése:

1. Ellenőrizze, hogy a házirendek letiltották-e az RDP-t.

      ```
      REM Get the local policy 
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server " /v fDenyTSConnections

      REM Get the domain policy if any
      reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections
      ```

      - Ha a tartományi házirend létezik, a rendszer felülírja a helyi házirend beállítását.
      - Ha a tartományi házirend azt állítja be, hogy az RDP le van tiltva (1), akkor frissítse az AD-házirendet a tartományvezérlőről.
      - Ha a tartományi házirend azt állítja be, hogy az RDP engedélyezve van (0), akkor nincs szükség frissítésre.
      - Ha a tartományi házirend nem létezik, és a helyi házirend tiltja, hogy az RDP le van tiltva (1), engedélyezze az RDP-t a következő parancs használatával: 
      
            reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
                  

2. Keresse meg a terminálkiszolgáló aktuális konfigurációját.

      ```
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

8. Lépjen ki a cmd-példányból `exit`, írja be a parancsot, majd nyomja le kétszer az **ENTER billentyűt** .

9. Indítsa újra a virtuális gépet `restart`a paranccsal, majd kapcsolódjon a virtuális géphez.

Ha a probléma továbbra is fennáll, lépjen a 2. lépésre.

#### <a name="step-2-enable-remote-desktop-services"></a>2\. lépés: Távoli asztali szolgáltatások engedélyezése

További információ: [Távoli asztali szolgáltatások nem egy Azure-beli virtuális gépen indul](troubleshoot-remote-desktop-services-issues.md)el.

#### <a name="step-3-reset-rdp-listener"></a>3\. lépés: RDP-figyelő visszaállítása

További információ: [Távoli asztal gyakori leválasztása az Azure-beli virtuális gépen](troubleshoot-rdp-intermittent-connectivity.md).

### <a name="offline-repair"></a>Offline javítás

#### <a name="step-1-turn-on-remote-desktop"></a>1\. lépés: Távoli asztal bekapcsolása

1. [Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális Géphez](../windows/troubleshoot-recovery-disks-portal.md).
2. Indítsa el a helyreállítási virtuális Gépet egy távoli asztali kapcsolatot.
3. Győződjön meg arról, hogy a lemez megjelölt **Online** a Lemezkezelés konzol. Vegye figyelembe a meghajtóbetűjelet, amely a csatlakoztatott operációsrendszer-lemez van rendelve.
4. Indítsa el a helyreállítási virtuális Gépet egy távoli asztali kapcsolatot.
5. Nyisson meg egy rendszergazda jogú parancssort munkamenetet (**Futtatás rendszergazdaként**). Futtassa a következő parancsfájlokat. Ez a szkript feltételezzük, hogy a meghajtóbetűjel van rendelve a csatlakoztatott operációsrendszer-lemez-e F. cserélje le ezt a meghajtóbetűjelet, a virtuális gép a megfelelő értékkel.

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

#### <a name="step-2-enable-remote-desktop-services"></a>2\. lépés: Távoli asztali szolgáltatások engedélyezése

További információ: [Távoli asztali szolgáltatások nem egy Azure-beli virtuális gépen indul](troubleshoot-remote-desktop-services-issues.md)el.

#### <a name="step-3-reset-rdp-listener"></a>3\. lépés: RDP-figyelő visszaállítása

További információ: [Távoli asztal gyakori leválasztása az Azure-beli virtuális gépen](troubleshoot-rdp-intermittent-connectivity.md).

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.

Ha továbbra is segítségre van szüksége, [forduljon](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) az ügyfélszolgálathoz a probléma gyors megoldása érdekében.
