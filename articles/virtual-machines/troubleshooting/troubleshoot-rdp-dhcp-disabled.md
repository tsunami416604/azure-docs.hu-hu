---
title: Nem lehet távolról kapcsolódni az Azure Virtual Machineshoz, mert a DHCP le van tiltva | Microsoft Docs
description: Megtudhatja, hogyan lehet letiltani a DHCP-ügyfélszolgáltatás által okozott RDP-problémák elhárítását Microsoft Azure. | Microsoft Docs
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75749892"
---
#  <a name="cannot-rdp-to-azure-virtual-machines-because-the-dhcp-client-service-is-disabled"></a>Nem lehet RDP-t létesíteni az Azure Virtual Machines miatt, mert a DHCP-ügyfélszolgáltatás le van tiltva

Ez a cikk egy olyan problémát ismertet, amelyben nem lehet távoli asztali Azure-Windows Virtual Machines (VM), miután a DHCP-ügyfélszolgáltatás le lett tiltva a virtuális gépen.


## <a name="symptoms"></a>Probléma
Azure-beli virtuális géppel nem lehet RDP-kapcsolattal csatlakozni, mert a DHCP-ügyfélszolgáltatás le van tiltva a virtuális gépen. Amikor bejelöli a képernyőképet a Azure Portal [rendszerindítási diagnosztika](../troubleshooting/boot-diagnostics.md) eszközében, a virtuális gép rendesen elindul, és megvárja a hitelesítő adatokat a bejelentkezési képernyőn. A virtuális gép eseménynaplóit a Eseménynapló használatával távolról tekintheti meg. Láthatja, hogy a DHCP-ügyfélszolgáltatás nincs elindítva vagy nem indul el. A következő egy minta napló:

**Napló neve**: rendszer </br>
**Forrás**: Service Control Manager </br>
**Dátum**: 12/16/2015 11:19:36 </br>
**Eseményazonosító**: 7022 </br>
**Feladat kategóriája**: nincs </br>
**Szint**: hiba </br>
**Kulcsszavak**: klasszikus</br>
**Felhasználó**: N/A </br>
**Számítógép**: myvm.cosotos.com</br>
**Leírás**: a DHCP-ügyfélszolgáltatás a kezdés után lefagyott.</br>

A Resource Manager-alapú virtuális gépek esetében a soros hozzáférési konzol funkció segítségével lekérdezheti az 7022-es eseménynaplókat a következő paranccsal:

    wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more

A klasszikus virtuális gépek esetében OFFLINE módban kell működnie, és manuálisan kell összegyűjtenie a naplókat.

## <a name="cause"></a>Ok

A DHCP-ügyfélszolgáltatás nem fut a virtuális gépen.

> [!NOTE]
> Ez a cikk csak a DHCP-ügyfélszolgáltatás és a DHCP-kiszolgáló esetében érvényes.

## <a name="solution"></a>Megoldás

Az alábbi lépések elvégzése előtt készítsen pillanatképet az érintett virtuális gép operációsrendszer-lemezéről biztonsági másolatként. További információ: [lemez pillanatképe](../windows/snapshot-copy-managed-disk.md).

A probléma megoldásához a soros vezérlőelem használatával engedélyezze a DHCP-t vagy a virtuális gép [hálózati adapterének alaphelyzetbe állítását](reset-network-interface.md) .

### <a name="use-serial-control"></a>Soros vezérlő használata

1. Kapcsolódjon a [soros konzolhoz, és nyissa meg a cmd-példányt](serial-console-windows.md#use-cmd-or-powershell-in-serial-console).
). Ha a soros konzol nincs engedélyezve a virtuális gépen, tekintse meg a [hálózati adapter alaphelyzetbe állítása](reset-network-interface.md)című témakört.
2. Ellenőrizze, hogy a DHCP le van-e tiltva a hálózati adapteren:

        sc query DHCP
3. Ha a DHCP leáll, próbálja meg elindítani a szolgáltatást.

        sc start DHCP

4. A szolgáltatás ismételt lekérdezésekor ellenőrizze, hogy a szolgáltatás sikeresen elindult-e.

        sc query DHCP

    Próbáljon csatlakozni a virtuális géphez, és ellenőrizze, hogy megoldódott-e a probléma.
5. Ha a szolgáltatás nem indul el, használja a következő megfelelő megoldást a kapott hibaüzenet alapján:

    | Hiba  |  Megoldás |
    |---|---|
    | 5 – HOZZÁFÉRÉS MEGTAGADVA  | [A hozzáférés-megtagadási hiba miatt a rendszer leállítja a DHCP-ügyfélszolgáltatás szolgáltatást](#dhcp-client-service-is-stopped-because-of-an-access-denied-error).  |
    |1053 – ERROR_SERVICE_REQUEST_TIMEOUT   | Lásd: a [DHCP-ügyfélszolgáltatás összeomlik vagy lefagy](#dhcp-client-service-crashes-or-hangs).  |
    | 1058 – ERROR_SERVICE_DISABLED  | Lásd: [a DHCP-ügyfélszolgáltatás le van tiltva](#dhcp-client-service-is-disabled).  |
    | 1059 – ERROR_CIRCULAR_DEPENDENCY  |A probléma megoldásához [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .   |
    | 1067 – ERROR_PROCESS_ABORTED |Lásd: a [DHCP-ügyfélszolgáltatás összeomlik vagy lefagy](#dhcp-client-service-crashes-or-hangs).   |
    |1068 – ERROR_SERVICE_DEPENDENCY_FAIL   | A probléma megoldásához [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .  |
    |1069 – ERROR_SERVICE_LOGON_FAILED   |  [A sikertelen bejelentkezés miatt nem sikerül a DHCP-ügyfél szolgáltatás](#dhcp-client-service-fails-because-of-logon-failure) : |
    | 1070 – ERROR_SERVICE_START_HANG  | Lásd: a [DHCP-ügyfélszolgáltatás összeomlik vagy lefagy](#dhcp-client-service-crashes-or-hangs).  |
    | 1077 – ERROR_SERVICE_NEVER_STARTED  | Lásd: [a DHCP-ügyfélszolgáltatás le van tiltva](#dhcp-client-service-is-disabled).  |
    |1079 – ERROR_DIFERENCE_SERVICE_ACCOUNT   | A probléma megoldásához [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .  |
    |1053 | A probléma megoldásához [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) .  |


#### <a name="dhcp-client-service-is-stopped-because-of-an-access-denied-error"></a>A DHCP-ügyfélszolgáltatás egy hozzáférés-megtagadási hiba miatt leáll

1. Kapcsolódjon a [soros konzolhoz](serial-console-windows.md) , és nyisson meg egy PowerShell-példányt.
2. Töltse le a Process monitor eszközt a következő parancsfájl futtatásával:

   ```powershell
   remove-module psreadline
   $source = "https://download.sysinternals.com/files/ProcessMonitor.zip"
   $destination = "c:\temp\ProcessMonitor.zip"
   $wc = New-Object System.Net.WebClient
   $wc.DownloadFile($source,$destination)
   ```
3. Most indítson el egy **Procmon** -nyomkövetést:

   ```
   procmon /Quiet /Minimized /BackingFile c:\temp\ProcMonTrace.PML
   ```
4. A **hozzáférés megtagadva** üzenetet generáló szolgáltatás indításával reprodukálja a problémát:

   ```
   sc start DHCP
   ```

   Ha nem sikerül, szakítsa meg a folyamat figyelője nyomkövetését:

   ```
   procmon /Terminate
   ```
5. Gyűjtse össze a **c:\temp\ProcMonTrace.PML** fájlt:

    1. [Adatlemez csatolása a virtuális géphez](../windows/attach-managed-disk-portal.md
).
    2. A soros konzol segítségével átmásolhatja a fájlt az új meghajtóra. Például: `copy C:\temp\ProcMonTrace.PML F:\`. Ebben a parancsban az F a csatolt adatlemez illesztőprogram-betűjele. Cserélje le a betűt a megfelelő értékre.
    3. Válassza le az adatmeghajtót, majd csatolja egy olyan működő virtuális géphez, amelyen telepítve van a Process monitor ubstakke.

6. Nyissa meg a **ProcMonTrace. PML** a munkavirtuális gépen a Process monitor használatával. Ezután a szűrés **eredmény alapján hozzáférés MEGtagadva**, az alábbi képernyőképen látható módon:

    ![Szűrés eredményként a Process monitorban](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

7. Javítsa ki a beállításjegyzékben található beállításkulcsokat, mappákat vagy fájlokat. Ezt a problémát általában akkor okozhatja, ha a szolgáltatásban használt bejelentkezési fiók nem rendelkezik ACL-engedéllyel az objektumok eléréséhez. A bejelentkezési fiók megfelelő ACL-engedélyének meghatározásához megtekintheti az egészséges virtuális gépet.

#### <a name="dhcp-client-service-is-disabled"></a>A DHCP-ügyfélszolgáltatás le van tiltva

1. Állítsa vissza a szolgáltatást az alapértelmezett indítási értékre:

   ```
   sc config DHCP start= auto
   ```

2. A szolgáltatás elindítása:

   ```
   sc start DHCP
   ```

3. A szolgáltatás állapotának lekérése, hogy ellenőrizze, hogy fut-e:

   ```
   sc query DHCP
   ```

4. A Távoli asztal használatával próbáljon csatlakozni a virtuális géphez.

#### <a name="dhcp-client-service-fails-because-of-logon-failure"></a>A DHCP-ügyfélszolgáltatás sikertelen bejelentkezési hiba miatt meghiúsult

1. Mivel ez a probléma akkor fordul elő, ha a szolgáltatás indítási fiókja módosult, a fiók visszaállítása az alapértelmezett állapotra:

        sc config DHCP obj= 'NT Authority\Localservice'
2. A szolgáltatás elindítása:

        sc start DHCP
3. A Távoli asztal használatával próbáljon csatlakozni a virtuális géphez.

#### <a name="dhcp-client-service-crashes-or-hangs"></a>DHCP-ügyfélszolgáltatás összeomlik vagy lefagy

1. Ha a szolgáltatás állapota elakad a **kezdő** vagy a **Leállítás** állapotban, próbálja meg leállítani a szolgáltatást:

        sc stop DHCP
2. A szolgáltatás elkülönítése a saját "Svchost" tárolóján:

        sc config DHCP type= own
3. A szolgáltatás elindítása:

        sc start DHCP
4. Ha a szolgáltatás még nem indul el, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>A virtuális gép kijavítása kapcsolat nélküli üzemmódban

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Az operációsrendszer-lemez csatlakoztatása egy helyreállítási virtuális géphez

1. [Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális géphez](../windows/troubleshoot-recovery-disks-portal.md).
2. Távoli asztal-Kapcsolódás elindítása a helyreállítási virtuális géphez. Győződjön meg arról, hogy a csatlakoztatott lemez **online** állapotban van megjelölve a Lemezkezelés konzolon. Jegyezze fel a csatlakoztatott operációsrendszer-lemezhez rendelt meghajtóbetűjelet.
3.  Nyisson meg egy rendszergazda jogú parancssor-példányt (**Futtatás rendszergazdaként**). Ezután futtassa az alábbi szkriptet. Ez a parancsfájl feltételezi, hogy a csatlakoztatott operációsrendszer-lemezhez rendelt meghajtóbetűjel **F**. A betűt a megfelelő módon cserélje le a virtuális gép értékére.

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

4. [Válassza le az operációsrendszer-lemezt, és hozza létre újra a virtuális gépet](../windows/troubleshoot-recovery-disks-portal.md). Ezután győződjön meg arról, hogy a probléma megoldódott-e.

## <a name="next-steps"></a>További lépések

Ha továbbra is segítségre van szüksége, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) , és kérje meg a probléma megoldását.