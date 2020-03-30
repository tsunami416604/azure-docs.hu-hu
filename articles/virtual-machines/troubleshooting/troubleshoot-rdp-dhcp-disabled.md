---
title: Nem lehet távolról csatlakozni az Azure virtuális gépekhez, mert a DHCP le van tiltva| Microsoft dokumentumok
description: Megtudhatja, hogy miként háríthatja el a DHCP-ügyfélszolgáltatás által okozott ügyfélszolgáltatás okozta probléma hibaelhárítását a Microsoft Azure-ban.| Microsoft dokumentumok
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
ms.openlocfilehash: 2c5b0556554d280e57b2df51875e1b057b5fb4a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75749892"
---
#  <a name="cannot-rdp-to-azure-virtual-machines-because-the-dhcp-client-service-is-disabled"></a>Az RdP nem érhető el az Azure virtuális gépekhez, mert a DHCP-ügyfélszolgáltatás le van tiltva

Ez a cikk egy olyan problémát ismertet, amely nek köszönhetően nem lehet távoli asztalt az Azure Windows virtuális gépekre (VM-ek) távoli gépre, miután a DHCP-ügyfél szolgáltatás le van tiltva a virtuális gépben.


## <a name="symptoms"></a>Probléma
RdP-kapcsolat nem hozhat létre virtuális gép az Azure-ban, mert a DHCP-ügyfél szolgáltatás le van tiltva a virtuális gép. Amikor ellenőrzi a képernyőképet a [rendszerindítási diagnosztika](../troubleshooting/boot-diagnostics.md) az Azure Portalon, a virtuális gép általában megjelenik, és várja a hitelesítő adatokat a bejelentkezési képernyőn. Távolról megtekintheti az eseménynaplókat a virtuális gép en az Eseménynapló használatával. Láthatja, hogy a DHCP-ügyfélszolgáltatás nem indult el, vagy nem indul el. A következő mintanapló:

**Napló neve**: Rendszer </br>
**Forrás**: Szolgáltatásvezérlő </br>
**Dátum**: 12/16/2015 11:19:36 AM </br>
**Eseményazonosító**: 7022 </br>
**Feladatkategória**: Nincs </br>
**Szint**: Hiba </br>
**Kulcsszavak:** Klasszikus</br>
**Felhasználó**: N/A </br>
**Számítógép**: myvm.cosotos.com</br>
**Leírás**: A DHCP-ügyfél szolgáltatás a kezdéskor lógott.</br>

Az Erőforrás-kezelő virtuális gépei esetén a Serial Access Console szolgáltatással a következő paranccsal kérdezheti le a 7022-es eseménynaplókat:

    wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more

A klasszikus virtuális gépek, akkor kell dolgozni offline módban, és gyűjtsük össze a naplókat manuálisan.

## <a name="cause"></a>Ok

A DHCP-ügyfélszolgáltatás nem fut a virtuális számítógépen.

> [!NOTE]
> Ez a cikk csak a DHCP-ügyfélszolgáltatásra vonatkozik, a DHCP-kiszolgálóra nem.

## <a name="solution"></a>Megoldás

Az alábbi lépések végrehajtása előtt készítsen pillanatképet az érintett virtuális gép operációsrendszer-lemezéről biztonsági másolatként. További információt a [Lemez pillanatképe](../windows/snapshot-copy-managed-disk.md)című témakörben talál.

A probléma megoldásához használja a Soros vezérlőt a virtuális gép DHCP-címének engedélyezéséhez vagy a [hálózati kapcsolat alaphelyzetbe állításához.](reset-network-interface.md)

### <a name="use-serial-control"></a>Soros vezérlő használata

1. Csatlakozzon a [Soros konzolhoz, és nyissa meg a CMD-példányt.](serial-console-windows.md#use-cmd-or-powershell-in-serial-console)
). Ha a soros konzol nincs engedélyezve a virtuális gépen, olvassa el [a Hálózati adapter alaphelyzetbe állítása című témakört.](reset-network-interface.md)
2. Ellenőrizze, hogy a DHCP le van-e tiltva a hálózati adapteren:

        sc query DHCP
3. Ha a DHCP le van állítva, próbálja meg elindítani a szolgáltatást

        sc start DHCP

4. A szolgáltatás ismételt lekérdezése a szolgáltatás sikeres elindításának biztosításához.

        sc query DHCP

    Próbáljon meg csatlakozni a virtuális géphez, és nézze meg, hogy a probléma megoldódott-e.
5. Ha a szolgáltatás nem indul el, használja a következő megfelelő megoldást a kapott hibaüzenet alapján:

    | Hiba  |  Megoldás |
    |---|---|
    | 5 - HOZZÁFÉRÉS MEGTAGADVA  | Lásd: [A DHCP-ügyfél szolgáltatás leáll a hozzáférés megtagadása miatt.](#dhcp-client-service-is-stopped-because-of-an-access-denied-error)  |
    |1053 - ERROR_SERVICE_REQUEST_TIMEOUT   | Lásd: [A DHCP-ügyfél szolgáltatás összeomlik vagy lefagy.](#dhcp-client-service-crashes-or-hangs)  |
    | 1058 - ERROR_SERVICE_DISABLED  | Lásd: [A DHCP-ügyfél szolgáltatás le van tiltva.](#dhcp-client-service-is-disabled)  |
    | 1059 - ERROR_CIRCULAR_DEPENDENCY  |A probléma gyors megoldásához [forduljon](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) az ügyfélszolgálathoz.   |
    | 1067 - ERROR_PROCESS_ABORTED |Lásd: [A DHCP-ügyfél szolgáltatás összeomlik vagy lefagy.](#dhcp-client-service-crashes-or-hangs)   |
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL   | A probléma gyors megoldásához [forduljon](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) az ügyfélszolgálathoz.  |
    |1069 - ERROR_SERVICE_LOGON_FAILED   |  Tekintse meg, hogy [a DHCP-ügyfélszolgáltatás bejelentkezési hiba miatt sikertelen](#dhcp-client-service-fails-because-of-logon-failure) |
    | 1070 - ERROR_SERVICE_START_HANG  | Lásd: [A DHCP-ügyfél szolgáltatás összeomlik vagy lefagy.](#dhcp-client-service-crashes-or-hangs)  |
    | 1077 - ERROR_SERVICE_NEVER_STARTED  | Lásd: [A DHCP-ügyfél szolgáltatás le van tiltva.](#dhcp-client-service-is-disabled)  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   | A probléma gyors megoldásához [forduljon](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) az ügyfélszolgálathoz.  |
    |1053 | A probléma gyors megoldásához [forduljon](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) az ügyfélszolgálathoz.  |


#### <a name="dhcp-client-service-is-stopped-because-of-an-access-denied-error"></a>A DHCP-ügyfél szolgáltatás leáll a hozzáférés megtagadása miatt hiba miatt

1. Csatlakozzon a [Soros konzolhoz,](serial-console-windows.md) és nyisson meg egy PowerShell-példányt.
2. Töltse le a Folyamatfigyelő eszközt a következő parancsfájl futtatásával:

   ```powershell
   remove-module psreadline
   $source = "https://download.sysinternals.com/files/ProcessMonitor.zip"
   $destination = "c:\temp\ProcessMonitor.zip"
   $wc = New-Object System.Net.WebClient
   $wc.DownloadFile($source,$destination)
   ```
3. Most kezdj el egy **procmon** nyomkövetést:

   ```
   procmon /Quiet /Minimized /BackingFile c:\temp\ProcMonTrace.PML
   ```
4. Reprodukálja a problémát a **hozzáférés** megtagadása üzenetet generáló szolgáltatás elindításával:

   ```
   sc start DHCP
   ```

   Ha nem sikerül, szakítsa meg a Folyamatfigyelő nyomkövetését:

   ```
   procmon /Terminate
   ```
5. Gyűjtse össze a **c:\temp\ProcMonTrace.PML** fájlt:

    1. [Adatlemez csatolása a virtuális géphez](../windows/attach-managed-disk-portal.md
).
    2. A Serial Console segítségével a fájlt az új meghajtóra másolhatja. Például: `copy C:\temp\ProcMonTrace.PML F:\`. Ebben a parancsban Az F a csatolt adatlemez illesztőprogram-betűjele. Cserélje le a betűt a megfelelő értékre.
    3. Válassza le az adatmeghajtót, majd csatlakoztassa egy működő virtuális géphez, amelyen telepítve van a Folyamatfigyelő ubstakke.

6. Nyissa meg **a ProconTrace.PML-t** a folyamatfigyelő használatával a működő virtuális számítógépen. Ezután a szűrés eredmény szerint **az ACCESS DENIED**, ahogy az a következő képernyőképen látható:

    ![Szűrés eredmény szerint a Folyamatfigyelőben](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

7. Javítsa ki a kimeneten lévő beállításkulcsokat, mappákat vagy fájlokat. Ezt a problémát általában akkor okozza, ha a szolgáltatásban használt bejelentkezési fiók nem rendelkezik ACL-engedéllyel ezekhez az objektumokhoz. A bejelentkezési fiók megfelelő ACL-engedélyének meghatározásához ellenőrizheti a kifogástalan állapotú virtuális gép.

#### <a name="dhcp-client-service-is-disabled"></a>A DHCP-ügyfél szolgáltatás le van tiltva

1. A szolgáltatás visszaállítása az alapértelmezett indítási értékre:

   ```
   sc config DHCP start= auto
   ```

2. Indítsa el a szolgáltatást:

   ```
   sc start DHCP
   ```

3. A szolgáltatás állapotának lekérdezése ismét, hogy biztosan fusson:

   ```
   sc query DHCP
   ```

4. Próbáljon meg csatlakozni a virtuális géphez a Távoli asztal használatával.

#### <a name="dhcp-client-service-fails-because-of-logon-failure"></a>A DHCP-ügyfél szolgáltatás bejelentkezési hiba miatt sikertelen

1. Mivel ez a probléma a szolgáltatás indítási fiókjának módosításakor jelentkezik, a fiók visszaállítása az alapértelmezett állapotra:

        sc config DHCP obj= 'NT Authority\Localservice'
2. Indítsa el a szolgáltatást:

        sc start DHCP
3. Próbáljon meg csatlakozni a virtuális géphez a Távoli asztal használatával.

#### <a name="dhcp-client-service-crashes-or-hangs"></a>A DHCP-ügyfél szolgáltatás összeomlik vagy lefagy

1. Ha a szolgáltatás állapota **indítási** vagy **leállítási** állapotban van, próbálja meg leállítani a szolgáltatást:

        sc stop DHCP
2. Különítse el a szolgáltatást a saját "svchost" tárolóján:

        sc config DHCP type= own
3. Indítsa el a szolgáltatást:

        sc start DHCP
4. Ha a szolgáltatás továbbra sem indul el, [forduljon az ügyfélszolgálathoz.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

### <a name="repair-the-vm-offline"></a>A virtuális gép kapcsolat nélküli javítása

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Az operációs rendszer lemezének csatolása helyreállítási virtuális géphez

1. [Csatlakoztassa az operációs rendszer lemezét egy helyreállítási virtuális géphez.](../windows/troubleshoot-recovery-disks-portal.md)
2. Távoli asztali kapcsolat indítása a helyreállítási virtuális géppel. Győződjön meg arról, hogy a csatlakoztatott lemez **online** ként van megjelölve a Lemezkezelés konzolon. Jegyezze fel a csatlakoztatott operációsrendszer-lemezhez rendelt meghajtóbetűjelet.
3.  Nyisson meg egy rendszergazdai jogú parancssorpéldányt (**Futtatás rendszergazdaként**). Ezután futtassa a következő parancsfájlt. Ez a parancsfájl feltételezi, hogy a csatlakoztatott operációsrendszer-lemezhez rendelt meghajtóbetűjel **F.** Cserélje le a betűt a virtuális gép értékére.

    ```
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM

    REM Set default values back on the broken service
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v start /t REG_DWORD /d 2 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v ObjectName /t REG_SZ /d "NT Authority\LocalService" /f
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v type /t REG_DWORD /d 16 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v start /t REG_DWORD /d 2 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v ObjectName /t REG_SZ /d "NT Authority\LocalService" /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v type /t REG_DWORD /d 16 /f

    reg unload HKLM\BROKENSYSTEM
    ```

4. [Válassza le az operációs rendszer lemezét, és hozza létre újra a virtuális gép](../windows/troubleshoot-recovery-disks-portal.md). Ezután ellenőrizze, hogy a probléma megoldódott-e.

## <a name="next-steps"></a>További lépések

Ha továbbra is segítségre van szüksége, lépjen kapcsolatba az [ügyfélszolgálattal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma megoldásához.