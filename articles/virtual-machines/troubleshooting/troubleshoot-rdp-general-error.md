---
title: Általános RDP-hiba elhárítása windowsos virtuális géphez az Azure-ban | Microsoft dokumentumok
description: Általános rdp-hibák elhárítása Windows virtuális gépekkel az Azure-ban | Microsoft dokumentumok
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
ms.openlocfilehash: 7fc0fbf3362d18284ad6a80afa6396b6be1270a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058003"
---
# <a name="troubleshoot-an-rdp-general-error-in-azure-vm"></a>Általános RDP-hiba elhárítása az Azure VM-ben

Ez a cikk egy általános hibát ismerteti, amely akkor jelenhet meg, ha RDP-kapcsolatot létesít egy Windows virtuális géppel (VM) az Azure-ban.

## <a name="symptom"></a>Hibajelenség

Amikor RDP-kapcsolatot létesít egy Azure-beli ablakvirtuális géptel, a következő általános hibaüzenet jelenhet meg:

**A Távoli asztal a következő okok miatt nem tud csatlakozni a távoli számítógéphez:**

1. **A kiszolgáló távoli elérése nincs engedélyezve**

2. **A távoli számítógép ki van kapcsolva**

3. **A távoli számítógép nem érhető el a hálózaton**

**Ellenőrizze, hogy a távoli számítógép be van-e kapcsolva, és csatlakozik-e a hálózathoz, és hogy a távelérés engedélyezve van-e.**

## <a name="cause"></a>Ok

A probléma a következő okok miatt fordulhat elő:

### <a name="cause-1"></a>1. ok

Az RDP-összetevő a következőképpen van letiltva:

- Az összetevő szintjén
- A hallgató szintjén
- A terminálkiszolgálón
- A Távoli asztali munkamenetgazda szerepkörben

### <a name="cause-2"></a>2. ok

A Távoli asztali szolgáltatások (TermService) nem fut.

### <a name="cause-3"></a>3. ok

Az RDP-figyelő helytelenül van konfigurálva.

## <a name="solution"></a>Megoldás

A probléma megoldásához [készüljön el az operációs rendszer lemezéről](../windows/snapshot-copy-managed-disk.md), és [csatlakoztassa az operációs rendszer lemezét egy mentővirtuális géphez,](troubleshoot-recovery-disks-portal-windows.md)majd kövesse a lépéseket.

### <a name="serial-console"></a>Soros konzol

#### <a name="step-1-open-cmd-instance-in-serial-console"></a>1. lépés: CmD-példány megnyitása soros konzolon

1. A [Soros konzol](serial-console-windows.md) eléréséhez válassza **a Támogatás & hibaelhárítási** > **soros konzol (előzetes verzió)** lehetőséget választva. Ha a szolgáltatás engedélyezve van a virtuális gép, a virtuális gép sikeresen csatlakoztatható.

2. Hozzon létre egy új csatornát egy CMD-példányhoz. Írja be a **CMD-t** a csatorna elindításához a csatorna nevének leéséhez.

3. Váltson arra a csatornára, amely a CMD-példányt futtatja, ebben az esetben az 1-es csatornának kell lennie.

   ```
   ch -si 1
   ```

#### <a name="step-2-check-the-values-of-rdp-registry-keys"></a>2. lépés: Ellenőrizze az RDP rendszerleíró kulcsok értékeit:

1. Ellenőrizze, hogy az RDP le van-e tiltva a rendőrség által.

      ```
      REM Get the local policy 
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server " /v fDenyTSConnections

      REM Get the domain policy if any
      reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections
      ```

      - Ha a tartományi házirend létezik, a helyi házirend beállítása felülíródik.
      - Ha a tartományi házirend kimondja, hogy az RDP le van tiltva (1), akkor frissítse az AD-házirendet a tartományvezérlőről.
      - Ha a tartományi házirend kimondja, hogy az RDP engedélyezve van (0), akkor nincs szükség frissítésre.
      - Ha a tartományi házirend nem létezik, és a helyi házirend kimondja, hogy az RDP le van tiltva (1), engedélyezze az RDP-t a következő paranccsal: 
      
            reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
                  

2. Ellenőrizze a terminálkiszolgáló aktuális konfigurációját.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled
      ```

      Ha a parancs 0 értéket ad vissza, a terminálkiszolgáló le van tiltva. Ezután engedélyezze a terminálkiszolgálót az alábbiak szerint:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f
      ```

3. A terminálkiszolgáló modul leürítési módba van állítva, ha a kiszolgáló terminálkiszolgáló-farmon (RDS vagy Citrix) található. Ellenőrizze a Terminálkiszolgáló modul aktuális módját.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode
      ```

      Ha a parancs 1-et ad vissza, a Terminálkiszolgáló modul leeresztési üzemmódba van állítva. Ezután állítsa a modult munkamódba az alábbiak szerint:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f
      ```

4. Ellenőrizze, hogy tud-e csatlakozni a terminálkiszolgálóhoz.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled
      ```

      Ha a parancs 1 értéket ad vissza, nem tud csatlakozni a terminálkiszolgálóhoz. Ezután engedélyezze a kapcsolatot az alábbiak szerint:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f
      ```
5. Ellenőrizze az RDP-figyelő aktuális konfigurációját.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation
      ```

      Ha a parancs 0-t ad vissza, az RDP-figyelő le van tiltva. Ezután engedélyezze a figyelőt az alábbiak szerint:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f
      ```

6. Ellenőrizze, hogy tud-e csatlakozni az RDP-figyelőhöz.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled
      ```

   Ha a parancs 1-et ad vissza, nem tud csatlakozni az RDP-figyelőhöz. Ezután engedélyezze a kapcsolatot az alábbiak szerint:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f
      ```

7. Indítsa újra a virtuális gépet.

8. Lépjen ki a CMD-példányból a beírással, `exit`majd nyomja le kétszer az **Enter** billentyűt.

9. Indítsa újra a virtuális `restart`gép beírásával, majd csatlakozzon a virtuális géphez.

Ha a probléma továbbra is fennáll, lépjen a 2.

#### <a name="step-2-enable-remote-desktop-services"></a>2. lépés: Távoli asztali szolgáltatások engedélyezése

További információ: [Távoli asztali szolgáltatások nem indul el egy Azure virtuális gép.](troubleshoot-remote-desktop-services-issues.md)

#### <a name="step-3-reset-rdp-listener"></a>3. lépés: Az RDP-figyelő alaphelyzetbe állítása

További információ: [Távoli asztal leválasztása gyakran az Azure virtuális gép.](troubleshoot-rdp-intermittent-connectivity.md)

### <a name="offline-repair"></a>Offline javítás

#### <a name="step-1-turn-on-remote-desktop"></a>1. lépés: A Távoli asztal bekapcsolása

1. [Csatlakoztassa az operációs rendszer lemezét egy helyreállítási virtuális géphez.](../windows/troubleshoot-recovery-disks-portal.md)
2. Távoli asztali kapcsolat indítása a helyreállítási virtuális géppel.
3. Győződjön meg arról, hogy a lemez **online** ként van megjelölve a Lemezkezelés konzolon. Jegyezze fel a csatlakoztatott operációsrendszer-lemezhez rendelt meghajtóbetűjelet.
4. Távoli asztali kapcsolat indítása a helyreállítási virtuális géppel.
5. Nyisson meg egy rendszergazdai parancssori munkamenetet (**Futtatás rendszergazdaként**). Futtassa a következő parancsfájlokat. Ebben a parancsfájlban feltételezzük, hogy a csatlakoztatott operációsrendszer-lemezhez rendelt meghajtóbetűjel f. Cserélje le ezt a meghajtóbetűjelet a virtuális gép megfelelő értékével.

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

6. Ha a virtuális gép tartományhoz csatlakozott, ellenőrizze a következő beállításkulcsot, hogy van-e olyan csoportházirend, amely letiltja az RDP-t. 

      ```
      HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services\fDenyTSConnectionS
      ```

      Ha ez a kulcs érték 1, az az azt jelenti, hogy a házirend letiltja az RDP-t. Ha engedélyezni szeretné a Távoli asztal szolgáltatást a csoportházirend-házirenden keresztül, módosítsa a következő házirendet a tartományvezérlőről:

   
      **Számítógép konfigurációja\Házirendek\Felügyeleti sablonok:**

      Házirend-definíciók\Windows-összetevők\Távoli asztali szolgáltatások\Távoli asztali munkamenetgazda\Kapcsolatok\A felhasználók távolról csatlakozhatnak a Távoli asztali szolgáltatások használatával
  
1. Válassza le a lemezt a mentési virtuális gépről.
1. [Hozzon létre egy új virtuális gép a lemezről](../windows/create-vm-specialized.md).

Ha a probléma továbbra is fennáll, lépjen a 2.

#### <a name="step-2-enable-remote-desktop-services"></a>2. lépés: Távoli asztali szolgáltatások engedélyezése

További információ: [Távoli asztali szolgáltatások nem indul el egy Azure virtuális gép.](troubleshoot-remote-desktop-services-issues.md)

#### <a name="step-3-reset-rdp-listener"></a>3. lépés: Az RDP-figyelő alaphelyzetbe állítása

További információ: [Távoli asztal leválasztása gyakran az Azure virtuális gép.](troubleshoot-rdp-intermittent-connectivity.md)

## <a name="need-help-contact-support"></a>Segítségre van szüksége? Kapcsolatfelvétel a támogatási szolgáltatással

Ha további segítségre van szüksége, [vegye fel a kapcsolatot az ügyfélszolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.
