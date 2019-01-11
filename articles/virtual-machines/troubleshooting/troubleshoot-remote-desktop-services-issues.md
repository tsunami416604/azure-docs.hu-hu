---
title: Távoli asztali szolgáltatások-beli virtuális gépen nem indítása |} A Microsoft Docs
description: Ismerje meg, és a távoli asztali szolgáltatások hibáinak elhárítása, amikor csatlakozik a virtuális gépek |} A Microsoft Docs
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
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 3d747f3b8f54dfefe7e96c378eddbce320bcc8f7
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2019
ms.locfileid: "54215116"
---
# <a name="remote-desktop-services-isnt-starting-on-an-azure-vm"></a>Távoli asztali szolgáltatások-beli virtuális gépen nem indítása

Ez a cikk ismerteti a problémák elhárításához, ha csatlakozik egy Azure virtuális gép (VM) és a távoli asztali szolgáltatások vagy TermService, nem indítása, vagy nem indul el.

> [!NOTE]  
> Az Azure két különböző üzemi modellek létrehozását és használatát erőforrásokkal rendelkezik: [Az Azure Resource Manager és klasszikus](../../azure-resource-manager/resource-manager-deployment-model.md). A cikk a Resource Manager üzemi modell használatával. Azt javasoljuk, hogy az új központi telepítéseknél a klasszikus üzemi modell helyett ezt a modellt használja.

## <a name="symptoms"></a>Probléma

Ha egy virtuális Géphez való csatlakozáshoz próbál, a következő esetekben tapasztalja:

- A virtuális gép képernyőképen látható, az operációs rendszer teljesen betöltött és Várakozás a hitelesítő adatokat.

    ![Képernyőkép a virtuális gép állapota](./media/troubleshoot-remote-desktop-services-issues/login-page.png)

- Ön távolról tekintse meg az eseménynaplókat a virtuális gépen az Eseménynapló használatával. Láthatja, hogy a távoli asztali szolgáltatások, TermService, nem indítása, vagy nem indul el. A következő naplófájl a következő egy minta:

    **Naplófájl neve**:      Rendszer </br>
    **forrás**:        Szolgáltatásvezérlő kezelője </br>
    **Dátum**:          12/16/2017 11:19:36 AM</br>
    **Eseményazonosító**:      7022</br>
    **Feladat kategóriája**: None</br>
    **Szint**:         Hiba</br>
    **A kulcsszavak**:      Klasszikus</br>
    **Felhasználói**:          –</br>
    **Számítógép**: vm.contoso.com</br>
    **Leírás**: A távoli asztali szolgáltatások szolgáltatás indításkor lefagyott. 

    A soros hozzáférés funkció segítségével keresse meg ezeket a hibákat a következő lekérdezés futtatásával: 

        wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more 

## <a name="cause"></a>Ok
 
A probléma oka, hogy a távoli asztali szolgáltatások nem fut a virtuális gépen. Az OK függ a következő esetekben: 

- A TermService szolgáltatás **letiltott**. 
- A TermService szolgáltatás összeomlik, vagy a függő. 
- A TermService miatt nem indul a helytelen konfiguráció.

## <a name="solution"></a>Megoldás

A soros konzol használatával a probléma elhárításához. Vagy [javítsa ki a virtuális Gépet offline](#repair-the-vm-offline) által a virtuális gép operációsrendszer-lemez egy helyreállítási virtuális Géphez csatolása.

### <a name="use-serial-console"></a>Soros konzol használata

1. Hozzáférés a [soros konzol](serial-console-windows.md) kiválasztásával **támogatás és hibaelhárítás** > **soros konzol**. A szolgáltatás engedélyezve van a virtuális gépen, a virtuális gép sikeresen csatlakoztathatja.

2. Hozzon létre egy új csatorna a CMD-példányhoz. Adja meg **CMD** elindítja a csatornát, és kérje le a csatorna nevét.

3. Váltson a csatorna, amelyen a CMD-példány. Ebben az esetben 1 csatornát kell lennie:

   ```
   ch -si 1
   ```

4. Válassza ki **Enter** újra, és adja meg egy érvényes felhasználónevet és jelszót, helyi vagy tartományi azonosító, a virtuális gép.

5. A TermService szolgáltatás állapotának lekérdezése:

   ```
   sc query TermService
   ```

6. Ha a szolgáltatás állapotát jeleníti meg **leállítva**, próbálja meg elindítani a szolgáltatást:

    ```
    sc start TermService
     ``` 

7. A szolgáltatás ismét győződjön meg arról, hogy a szolgáltatás sikeresen elindult-e lekérdezés:

   ```
   sc query TermService
   ```
8. Ha a szolgáltatás nem indul el, kövesse a megoldás, amely az észlelt hiba:

    |  Hiba |  Javaslat |
    |---|---|
    |5 – HOZZÁFÉRÉS MEGTAGADVA |Lásd: [TermService szolgáltatás le van állítva, a hozzáférés megtagadva hiba miatt](#termService-service-is-stopped-because-of-an-access-denied-problem). |   |1053 - ERROR_SERVICE_REQUEST_TIMEOUT  |Lásd: [TermService szolgáltatás le van tiltva](#termService-service-is-disabled).  |  
    |1058 - ERROR_SERVICE_DISABLED  |Lásd: [TermService szolgáltatás összeomlik vagy lefagy](#termService-service-crashes-or-hangs).  |
    |1059 - ERROR_CIRCULAR_DEPENDENCY |[Forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.|
    |1067 - ERROR_PROCESS_ABORTED  |Lásd: [TermService szolgáltatás összeomlik vagy lefagy](#termService-service-crashes-or-hangs).  |
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL|[Forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.|
    |1069 - ERROR_SERVICE_LOGON_FAILED  |Lásd: [TermService szolgáltatás bejelentkezési hiba miatt sikertelen](#termService-service-fails-because-of-logon-failure) |
    |1070 - ERROR_SERVICE_START_HANG   | Lásd: [TermService szolgáltatás összeomlik vagy lefagy](#termService-service-crashes-or-hangs). |
    |1077 - ERROR_SERVICE_NEVER_STARTED   | Lásd: [TermService szolgáltatás le van tiltva](#termService-service-is-disabled).  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   |[Forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében. |
    |1753   |[Forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.   |   |5 – HOZZÁFÉRÉS MEGTAGADVA |Lásd: [TermService szolgáltatás le van állítva, a hozzáférés megtagadva hiba miatt](#termService-service-is-stopped-because-of-an-access-denied-error). |
    
#### <a name="termservice-service-is-stopped-because-of-an-access-denied-problem"></a>TermService szolgáltatás le van állítva, a hozzáférés megtagadva hiba miatt

1. Csatlakozás [soros konzol](serial-console-windows.md) , és nyissa meg egy PowerShell-példány.
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

4. Reprodukálja a problémát, amely lehetővé teszi a szolgáltatás indításával **hozzáférés megtagadva**: 

   ```
   sc start TermService 
   ```

   Ha nem sikerül, leállítja a folyamat figyelője nyomkövetés:

   ```   
   procmon /Terminate 
   ```

5. A fájl begyűjtése **c:\temp\ProcMonTrace.PML**:

    1. [Adatlemez csatolása a virtuális géphez](../windows/attach-managed-disk-portal.md
).
    2. A fájl átmásolása az új meghajtó soros konzol használata. Például: `copy C:\temp\ProcMonTrace.PML F:\`. Ebben a parancsban F a csatlakoztatott lemez meghajtóbetűjelét.
    3. Az adatmeghajtó leválasztása, és mellékelje egy működő virtuális Gépet, amely a folyamat figyelője ubstakke telepítve van a.

6. Nyissa meg **ProcMonTrace.PML** folyamat figyelője a működő virtuális gép használatával. Majd szűrés **eredménye a hozzáférés MEGTAGADVA**, ahogy az alábbi képernyőfelvételen látható:

    ![Szűrés eredménye a folyamat figyelője](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

 
6. Javítsa ki a beállításkulcsokat, mappákhoz vagy a kimeneti fájlok. Általában a problémát az okozza, ha a bejelentkezési fiókot, amelynek a szolgáltatás nem rendelkezik ACL ezek az objektumok hozzáférési engedélyt. Tudni, hogy a megfelelő ACL jogosultságot a bejelentkezési fiók, ellenőrizheti a virtuális gép kifogástalan állapotú. 

#### <a name="termservice-service-is-disabled"></a>TermService szolgáltatás le van tiltva

1. Állítsa vissza a szolgáltatás az alapértelmezett rendszerindító értékre:

   ```
   sc config TermService start= demand 
   ```

2. Indítsa el a szolgáltatást:

   ```
   sc start TermService
   ```

3. Ismét, hogy fut a szolgáltatás állapotának lekérdezéséhez:

   ```
   sc query TermService 
   ```

4. Próbálja meg csatlakozni a virtuális Géphez a távoli asztal használatával.

#### <a name="termservice-service-fails-because-of-logon-failure"></a>Bejelentkezési hiba miatt sikertelen TermService szolgáltatás

1. Ez a probléma akkor fordul elő, ha a szolgáltatás indítófiókjának módosítása. Ezen vissza az alapértelmezett változott: 

        sc config TermService obj= 'NT Authority\NetworkService'
2. Indítsa el a szolgáltatást:

        sc start TermService
3. Próbálja meg csatlakozni a virtuális Géphez a távoli asztal használatával.

#### <a name="termservice-service-crashes-or-hangs"></a>TermService szolgáltatás összeomlik vagy lefagy
1. Ha a szolgáltatás állapotának Beragadt **kezdő** vagy **leállítása**, próbálja meg leállítani a szolgáltatást: 

        sc stop TermService
2. A szolgáltatás a saját "svchost" tároló elkülönítése:

        sc config TermService type= own
3. Indítsa el a szolgáltatást:

        sc start TermService
4. Ha a szolgáltatás továbbra is sikertelen elindításához [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>Javítsa ki a virtuális Gépet kapcsolat nélküli módban

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális Géphez

1. [Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális Géphez](../windows/troubleshoot-recovery-disks-portal.md).
2. Indítsa el a helyreállítási virtuális Gépet egy távoli asztali kapcsolatot. Győződjön meg arról, hogy a csatlakoztatott lemez megjelölt **Online** a Lemezkezelés konzol. Vegye figyelembe a meghajtóbetűjelet, amely a csatlakoztatott operációsrendszer-lemez van rendelve.
3.  Nyissa meg a rendszergazda jogú parancssort (**Futtatás rendszergazdaként**). Ezután futtassa az alábbi parancsfájlt. Feltételezzük, hogy van-e a meghajtóbetűjelet, a csatlakoztatott operációsrendszer-lemez rendelt **F**. Cserélje le a megfelelő értéket a virtuális gépen. 

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv
        
   REM Set default values back on the broken service 
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v start /t REG_DWORD /d 3 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService“ /f
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v type /t REG_DWORD /d 16 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v start /t REG_DWORD /d 3 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService" /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v type /t REG_DWORD /d 16 /f
   ```

4. [Az operációsrendszer-lemez leválasztása, és hozza létre újra a virtuális gép](../windows/troubleshoot-recovery-disks-portal.md). Ezután ellenőrizze, hogy a probléma megoldódott-e.

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha továbbra is segítségre van szüksége, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a megoldott probléma.
