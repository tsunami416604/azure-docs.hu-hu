---
title: Az Azure-beli Windows virtuális gépekhez RDP általános hiba elhárítása |} A Microsoft Docs
description: Ismerje meg, az Azure-beli Windows virtuális gépekhez RDP általános hiba elhárítása |} A Microsoft Docs
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
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 701373efa3c3c22eb5969705927e1c0cc6e3f36b
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2018
ms.locfileid: "50215804"
---
# <a name="troubleshoot-an-rdp-general-error-in-azure-vm"></a>Az Azure virtuális Gépen RDP általános hiba elhárítása

Ez a cikk ismerteti egy általános hibát tapasztalhat, ha a kapcsolat létrehozása távoli asztal protokoll (RDP) a Windows virtuális gép (VM) az Azure-ban.

## <a name="symptoms"></a>Probléma

Ha egy RDP-kapcsolat ablak virtuális géphez az Azure-ban, a következő általános hibaüzenet jelenhet meg:

**A távoli asztal az alábbi okok valamelyike nem tud kapcsolódni a távoli számítógéphez:**

1. **Távoli hozzáférés a kiszolgálón nincs engedélyezve**

2. **A távoli számítógép ki van kapcsolva.**

3. **A távoli számítógép nem érhető el a hálózaton**

**Ellenőrizze, hogy a távoli számítógéphez van kapcsolva, és csatlakozik a hálózathoz, és, hogy a távoli hozzáférés engedélyezve van-e.**

## <a name="cause"></a>Ok

Ez a probléma a következő okok miatt fordulhat elő:

### <a name="cause-1"></a>OK: 1

Az RDP-összetevő módon le van tiltva:

- Az összetevők szintjén
- A figyelő szintjén
- A terminálkiszolgáló
- A távoli asztali munkamenetgazda szerepkör

### <a name="cause-2"></a>OK 2

Nem fut a távoli asztali szolgáltatások (TermService).

### <a name="cause-3"></a>OK: 3

Az RDP-figyelő helytelenül van konfigurálva.

## <a name="solution"></a>Megoldás

Ez a probléma megoldásához [készítsen biztonsági másolatot az operációsrendszer-lemez](../windows/snapshot-copy-managed-disk.md), és [az operációsrendszer-lemez csatolása a virtuális gép mentési](troubleshoot-recovery-disks-portal-windows.md), majd hajtsa végre a megoldási lehetőségeket az ennek megfelelően, vagy próbálja ki a megoldásokat, egyenként.

### <a name="serial-console"></a>Soros konzol

#### <a name="step-1-turn-on-remote-desk"></a>1. lépés: Távoli asztali bekapcsolása

1. Hozzáférés a [soros konzol](serial-console-windows.md) kiválasztásával **támogatás és hibaelhárítás** > **soros konzol (előzetes verzió)**. A szolgáltatás engedélyezve van a virtuális gépen, a virtuális gép sikeresen csatlakoztathatja.

2. Hozzon létre egy új csatorna a CMD-példányhoz. Típus **CMD** elindítja a csatornát a csatorna nevének lekérése érdekében.

3. Váltson a csatornát a CMD-példányt futtató, ebben az esetben 1 csatornát kell lennie.

   ```
   ch -si 1
   ```

4. A távoli asztal engedélyezése a csoportházirend-objektum csoportházirend segítségével úgy módosítja a következő szabályzatot:

   ```
   Computer Configuration\Policies\Administrative Templates: Policy definitions\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections\Allow users to connect remotely by using Remote Desktop Services
   ```

5. Néhány más beállításkulcsok, amelyek a probléma, ellenőrizze a a beállításkulcsokat a következő:

   1. Győződjön meg arról, hogy engedélyezve van-e az RDP-összetevő.

      ```
      REM Get the local policy
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server " /v fDenyTSConnections

      REM Get the domain policy if any
      reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections
      ```

      Ha a tartományi házirend létezik, a rendszer felülírja a telepítőt a helyi házirend.

         - Ha a tartományi házirend arról, hogy RDP letiltva (1), majd az AD-házirend frissítése.
         - Ha a tartományi házirend arról, hogy az RDP engedélyezve (0), majd nincs szükség frissítésre.

      Ha a tartományi házirend nem létezik, és a helyi házirend szerint az, hogy RDP le van tiltva (1), engedélyezze az RDP a következő paranccsal:

         ```
         reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
         ```

   2. Ellenőrizze a terminálkiszolgáló aktuális konfigurációját.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled
      ```

   3. A parancs a 0 értéket adja vissza, ha a Terminálszolgáltatások kiszolgáló le van tiltva. Ezt követően engedélyezze a terminálkiszolgáló módon:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f
      ```

   4. A terminálkiszolgáló modul a kiürítési módot, ha a kiszolgáló a Terminálszolgáltatások kiszolgálófarmon (a távoli asztali szolgáltatások vagy Citrix) értékre van állítva. Ellenőrizze a Terminálszolgáltatások kiszolgáló-modul az aktuális módja.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode
      ```

   5. Ha a parancs visszaadja az 1, a Terminálszolgáltatások kiszolgáló-modul értéke kiürítési módot. Ezután állítsa be a modul működő módra a következő:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f
      ```

   6. Ellenőrizze, hogy terminálkiszolgáló csatlakozhat.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled
      ```

   7. A parancs visszaadja az 1, akkor tud csatlakozni a Terminálszolgáltatások kiszolgáló. Ezt követően engedélyezze a kapcsolatot a következő:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f
      ```

   8. Ellenőrizze a RDP-figyelő aktuális konfigurációját.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation
      ```

   9. A parancs a 0 értéket adja vissza, ha az RDP-figyelő le van tiltva. Ezt követően engedélyezze a figyelő a következő:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f
      ```

   10. Ellenőrizze, hogy csatlakozhat az RDP-figyelő.

      ```
      reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled
      ```

   11. Ha a parancs 1 ad vissza, nem lehet csatlakozni az RDP-figyelő. Ezt követően engedélyezze a kapcsolatot a következő:

      ```
      reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f
      ```

6. Indítsa újra a virtuális gépet.

7. Írja be a CMD-példányból kilépési `exit`, és nyomja le az **Enter** kétszer.

8. Írja be a virtuális gép újraindítása `restart`.

Ha a probléma továbbra is történik, helyezze át a 2. lépés.

#### <a name="step-2-enable-remote-desktop-services"></a>2. lépés: Távoli asztali szolgáltatások engedélyezése

További információkért lásd: [távoli asztali szolgáltatások-beli virtuális gépen nem indítása](troubleshoot-remote-desktop-services-issues.md).

#### <a name="step-3-reset-rdp-listener"></a>3. lépés: Új RDP-figyelő

További információkért lásd: [a távoli asztal gyakran leválasztja az Azure virtuális Gépen](troubleshoot-rdp-intermittent-connectivity.md).

### <a name="offline-repair"></a>Offline javítása

#### <a name="step-1-turn-on-remote-desk"></a>1. lépés: Távoli asztali bekapcsolása

> [!NOTE]  
> Azt feltételezik, hogy a meghajtóbetűjel, a csatlakoztatott operációsrendszer-lemez rendelt F. cserélje le a megfelelő értéket a virtuális gépen. A rendszer és a SZOFTVEREK struktúrák kell majd csatlakoztatni és leválasztani.

1. Nyisson meg egy rendszergazda jogú CMD-példányt, és futtassa az alábbi parancsfájlok a virtuális gép mentési:

   ```
   reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM.hiv
   reg load HKLM\BROKENSOFTWARE f:\windows\system32\config\SOFTWARE.hiv

   REM Ensure that Terminal Server is enabled
   reg add "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server" /v TSEnabled /t REG_DWORD /d 1 /f

   REM Ensure Terminal Service is not set to Drain mode
   reg add "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server" /v TSServerDrainMode /t REG_DWORD /d 0 /f

   REM Ensure Terminal Service has logon enabled
   reg add "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server" /v TSUserEnabled /t REG_DWORD /d 0 /f

   REM Ensure the RDP Listener is not disabled
   reg add "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\Winstations\RDP-Tcp" /v fEnableWinStation /t REG_DWORD /d 1 /f

   REM Ensure the RDP Listener accepts logons
   reg add "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\Winstations\RDP-Tcp" /v fLogonDisabled /t REG_DWORD /d 0 /f

   REM RDP component is enabled
   reg add "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
   reg add "HKLM\BROKENSOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0 /f

   reg unload HKLM\BROKENSYSTEM
   reg unload HKLM\BROKENSOFTWARE
   ```

2. Csatlakoztassa a rendszer és a SZOFTVEREK struktúrák.

   ```
   reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM
   reg load HKLM\BROKENSOFTWARE f:\windows\system32\config\SOFTWARE
   ```

3. Ha a virtuális gép tartományhoz, RDP házirend szintű sikerült letiltani. Annak ellenőrzése, ha ez a helyzet, ellenőrizze a következő beállításkulcsot:

   ```
   HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services\fDenyTSConnectionS
   ```

4. Ha a kulcs értékét 1-re van állítva, az RDP a házirend le van tiltva.

5. A távoli asztal engedélyezése a csoportházirend-objektum csoportházirend segítségével úgy módosítja a következő szabályzatot:

   ```
   Computer Configuration\Policies\Administrative Templates: Policy definitions\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections\Allow users to connect remotely by using Remote Desktop Services
   ```

6. Ha a beállításkulcs nem létezik, ellenőrizze a következő beállításkulcsot:

   ```
   HKLM\System\CurrentControlSet\Control\Terminal Server\fDenyTSConnections
   ```

7. Ha ez a kulcs 1 értékre van állítva, az RDP kapcsolja be kapcsolva. A kulcs értékét állítsa 0.
8. Válassza le a lemezt a virtuális gép mentési.
9. [Hozzon létre egy új virtuális Gépet a lemezről](../windows/create-vm-specialized.md).

Ha a probléma továbbra is történik, helyezze át a 2. lépés.

#### <a name="step-2-enable-remote-desktop-services"></a>2. lépés: Távoli asztali szolgáltatások engedélyezése

További információkért lásd: [távoli asztali szolgáltatások-beli virtuális gépen nem indítása](troubleshoot-remote-desktop-services-issues.md).

#### <a name="step-3-reset-rdp-listener"></a>3. lépés: Új RDP-figyelő

További információkért lásd: [a távoli asztal gyakran leválasztja az Azure virtuális Gépen](troubleshoot-rdp-intermittent-connectivity.md).

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha továbbra is segítségre van szüksége, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.
