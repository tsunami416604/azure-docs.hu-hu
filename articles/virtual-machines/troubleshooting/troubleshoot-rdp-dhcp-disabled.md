---
title: Nem lehet távoli csatlakozás az Azure Virtual Machines a DHCP le van tiltva |} A Microsoft Docs
description: Ismerje meg, hogyan háríthatók el a RDP DHCP-ügyfélszolgáltatás okozó probléma le van tiltva, a Microsoft Azure-ban. |} A Microsoft Docs
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
ms.openlocfilehash: a469fe0d6057d865ec006d9eb14ad95f2d4b7005
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52308439"
---
#  <a name="cannot-rdp-to-azure-virtual-machines-because-the-dhcp-client-service-is-disabled"></a>Nem lehet RDP az Azure Virtual Machines, mert a DHCP-ügyfél szolgáltatás le van tiltva

Ez a cikk ismerteti a problémát, amelyben nem lehet a távoli asztal az Azure Windows Virtual Machines (VM) után a DHCP-ügyfél szolgáltatás le van tiltva, a virtuális gépen.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="symptoms"></a>Probléma 

Nem lehet RDP-kapcsolatok egy virtuális Gépet az Azure-ban a DHCP-ügyfél szolgáltatás le van tiltva, a virtuális gépen. Amikor ellenőrizheti a képernyőképen a a [rendszerindítási diagnosztika](../troubleshooting/boot-diagnostics.md) az Azure Portalon, megjelenik a virtuális gép indul el megfelelően, és a hitelesítő adatokat a bejelentkezési képernyőn vár. Ön távolról tekintse meg az eseménynaplókat a virtuális gépen az Eseménynapló használatával. Láthatja, hogy a DHCP-ügyfél szolgáltatás nincs elindítva vagy nem indul el. A következő egy minta jelentkezzen be:

**Naplófájl neve**: rendszer </br>
**Forrás**: szolgáltatásvezérlő kezelője </br>
**Dátum**: 12/16/2015 11:19:36 AM </br>
**Eseményazonosító**: 7022 </br>
**Feladat kategóriája**: nincs </br>
**Szint**: hiba </br>
**A kulcsszavak**: klasszikus</br> 
**Felhasználói**: N/A </br>
**Számítógép**: myvm.cosotos.com</br>
**Leírás**: A DHCP-ügyfél szolgáltatás indításkor lefagyott.</br>

Resource Manager-beli virtuális gépek soros hozzáférés funkció lekérdezéséhez az esemény-naplókat 7022 használja az alábbi parancsot is használhatja:

    wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more

Klasszikus virtuális gépek esetén kell kapcsolat nélküli üzemmódban működik, és a naplók gyűjtése az manuálisan.

## <a name="cause"></a>Ok

A DHCP-ügyfélszolgáltatás nem fut a virtuális gépen. 

> [!NOTE]
> Ez a cikk csak a DHCP-szolgáltatás és a nem DHCP-kiszolgáló számára vonatkozik. 

## <a name="solution"></a>Megoldás 

Mielőtt végrehajtaná ezeket a lépéseket, az érintett virtuális gép operációsrendszer-lemezének pillanatkép készítése a biztonsági mentéséhez. További információkért lásd: [lemez pillanatképének elkészítése](../windows/snapshot-copy-managed-disk.md).

A probléma megoldásához, soros vezérlőelem használatával engedélyezze a DHCP- vagy [hálózati adapter alaphelyzetbe állítása](reset-network-interface.md) a virtuális gép számára.

### <a name="use-serial-control"></a>Soros vezérlőelem használata

1. Csatlakozás [soros konzolon és a nyílt CMD-példány](./serial-console-windows.md#open-cmd-or-powershell-in-serial-console
). Ha a soros konzol nincs engedélyezve a virtuális Gépen, [hálózati adapter alaphelyzetbe állítása](reset-network-interface.md).
2. Ellenőrizze, hogy ha a DHCP a hálózati adapter le van tiltva:

        sc query DHCP
3. Ha a DHCP le van állítva, próbálja meg elindítani a szolgáltatást

        sc start DHCP
        
4. A lekérdezés a szolgáltatás ismét győződjön meg arról, hogy a szolgáltatás sikeresen elindult-e.

        sc query DHCP

    Próbálja meg a virtuális gép csatlakozik, és a probléma megoldódott.
5. Ha a szolgáltatás nem indul el, használja a következő megfelelő megoldást, a kapott hibaüzenet alapján:

    | Hiba  |  Megoldás |
    |---|---|
    | 5 – HOZZÁFÉRÉS MEGTAGADVA  | Lásd: [DHCP-ügyfél szolgáltatás le van állítva, a hozzáférés megtagadva hiba miatt](#dhcp-client-service-is-stopped-because-of-an-access-denied-error).  |
    |1053 - ERROR_SERVICE_REQUEST_TIMEOUT   | Lásd: [DHCP-ügyfél szolgáltatás összeomlik vagy lefagy](#dhcp-client-service-crashes-or-hangs).  |
    | 1058 - ERROR_SERVICE_DISABLED  | Lásd: [DHCP-ügyfél szolgáltatás le van tiltva](#dhcp-client-service-is-disabled).  |
    | 1059 - ERROR_CIRCULAR_DEPENDENCY  |[Forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) beolvasni a probléma gyors megoldása érdekében.   |
    | 1067 - ERROR_PROCESS_ABORTED |Lásd: [DHCP-ügyfél szolgáltatás összeomlik vagy lefagy](#dhcp-client-service-crashes-or-hangs).   |
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL   | [Forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) beolvasni a probléma gyors megoldása érdekében.  |
    |1069 - ERROR_SERVICE_LOGON_FAILED   |  Lásd: [DHCP-szolgáltatás bejelentkezési hiba miatt sikertelen](#dhcp-client-service-fails-because-of-logon-failure) |
    | 1070 - ERROR_SERVICE_START_HANG  | Lásd: [DHCP-ügyfél szolgáltatás összeomlik vagy lefagy](#dhcp-client-service-crashes-or-hangs).  |
    | 1077 - ERROR_SERVICE_NEVER_STARTED  | Lásd: [DHCP-ügyfél szolgáltatás le van tiltva](#dhcp-client-service-is-disabled).  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   | [Forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) beolvasni a probléma gyors megoldása érdekében.  | 
    |1053 | [Forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) beolvasni a probléma gyors megoldása érdekében.  |
    

#### <a name="dhcp-client-service-is-stopped-because-of-an-access-denied-error"></a>DHCP-ügyfél szolgáltatás a hozzáférés megtagadva hiba miatt leállt

1. Csatlakozás [soros konzol](serial-console-windows.md#) , és nyissa meg egy PowerShell-példány.
2. Töltse le a folyamat figyelője eszközt a következő szkript futtatásával:

   ```
   remove-module psreadline  
   $source = "https://download.sysinternals.com/files/ProcessMonitor.zip" 
   $destination = "c:\temp\ProcessMonitor.zip" 
   $wc = New-Object System.Net.WebClient 
   $wc.DownloadFile($source,$destination) 
   ```
3. Most indítsa el a **procmon** nyomkövetési:

   ```
   procmon /Quiet /Minimized /BackingFile c:\temp\ProcMonTrace.PML 
   ```
4. Reprodukálja a problémát úgy, hogy a szolgáltatás indítása a **hozzáférés megtagadva** üzenet: 

   ```
   sc start DHCP
   ```

   Ha nem sikerül, leállítja a folyamat figyelője nyomkövetés:

   ```   
   procmon /Terminate 
   ```
5. Collect a **c:\temp\ProcMonTrace.PML** fájlt:

    1. [Adatlemez csatolása a virtuális géphez](../windows/attach-managed-disk-portal.md
).
    2. A fájl átmásolása az új meghajtó soros konzol használata. Például: `copy C:\temp\ProcMonTrace.PML F:\`. Ebben a parancsban F a csatlakoztatott lemez meghajtóbetűjelét. A levél szükség szerint cserélje le a megfelelő értéket.
    3. Az adatmeghajtó leválasztása, és mellékelje egy működő virtuális Gépet, amely a folyamat figyelője ubstakke telepítve van.

6. Nyissa meg **ProcMonTrace.PML** folyamat figyelője a működő virtuális gép használatával. Majd szűrés **eredménye a hozzáférés MEGTAGADVA**, ahogy az alábbi képernyőfelvételen látható:

    ![Szűrés eredménye a folyamat figyelője](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

7. Javítsa ki a beállításkulcsokat, mappákhoz vagy a kimeneti fájlok. Általában a problémát az okozza, ha a bejelentkezési fiókot, amelynek a szolgáltatás nem rendelkezik ACL ezek az objektumok hozzáférési engedélyt. Annak megállapításához, a megfelelő ACL jogosultságot a bejelentkezési fiók, ellenőrizheti a kifogástalan állapotú virtuális gép. 

#### <a name="dhcp-client-service-is-disabled"></a>DHCP-ügyfél szolgáltatás le van tiltva

1. Állítsa vissza a szolgáltatás az alapértelmezett rendszerindító értékre:

   ```
   sc config DHCP start= auto
   ```

2. Indítsa el a szolgáltatást:

   ```
   sc start DHCP
   ```

3. A szolgáltatás állapotát újra, hogy fut-e:

   ```
   sc query DHCP
   ```

4. Próbálja ki a virtuális Géphez való csatlakozáshoz a távoli asztal használatával.

#### <a name="dhcp-client-service-fails-because-of-logon-failure"></a>DHCP-szolgáltatás bejelentkezési hiba miatt sikertelen

1. Ez a probléma akkor fordul elő, ha a szolgáltatás indítófiókjának módosítása, mert visszaállítása a fiók alapértelmezett állapota: 

        sc config DHCP obj= 'NT Authority\Localservice'
2. Indítsa el a szolgáltatást:

        sc start DHCP
3. Próbálja ki a virtuális Géphez való csatlakozáshoz a távoli asztal használatával.

#### <a name="dhcp-client-service-crashes-or-hangs"></a>DHCP-ügyfél szolgáltatás összeomlik vagy lefagy
1. Ha a szolgáltatás állapotának Beragadt a **kezdő** vagy **leállítása** állapotban van, próbálja meg leállítani a szolgáltatást: 

        sc stop DHCP
2. A szolgáltatás a saját "svchost" tároló elkülönítése:

        sc config DHCP type= own
3. Indítsa el a szolgáltatást:

        sc start DHCP
4. A szolgáltatás még mindig nem indul el, ha [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>Javítsa ki a virtuális Gépet kapcsolat nélküli módban

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális Géphez

1. [Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális Géphez](../windows/troubleshoot-recovery-disks-portal.md).
2. Indítsa el a helyreállítási virtuális Gépet egy távoli asztali kapcsolatot. Győződjön meg arról, hogy a csatlakoztatott lemez megjelölt **Online** a Lemezkezelés konzol. Vegye figyelembe a meghajtóbetűjelet, amely a csatlakoztatott operációsrendszer-lemez van rendelve.
3.  Nyissa meg a rendszergazda jogú parancssort (**Futtatás rendszergazdaként**). Ezután futtassa az alábbi parancsfájlt. Ez a parancsfájl feltételezi, hogy a meghajtóbetűjel, a csatlakoztatott operációsrendszer-lemez rendelt **F**. A levél szükség szerint cserélje le az értéket a virtuális gépen. 

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

4. [Az operációsrendszer-lemez leválasztása, és hozza létre újra a virtuális gép](../windows/troubleshoot-recovery-disks-portal.md). Majd ellenőrizze, hogy a probléma megoldódott.

## <a name="next-steps"></a>További lépések

Ha továbbra is segítségre van szüksége, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) beolvasni a megoldott probléma.


