---
title: A Távoli asztali szolgáltatások nem indul nak el az Azure virtuális gépen | Microsoft dokumentumok
description: A Távoli asztali szolgáltatások szolgáltatással kapcsolatos problémák elhárítása virtuális géphez való csatlakozáskor | Microsoft dokumentumok
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 4b314fbdb9cbc0c0b797cbee8e92ee4702bbea81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77919464"
---
# <a name="remote-desktop-services-isnt-starting-on-an-azure-vm"></a>A Távoli asztali szolgáltatások nem indul el egy Azure virtuális gépen

Ez a cikk ismerteti, hogyan hárítsa el a problémákat, amikor csatlakozik egy Azure virtuális gép (VM) és a Távoli asztali szolgáltatások, vagy a TermService, nem indul el, vagy nem indul el.


## <a name="symptoms"></a>Probléma

Amikor megpróbál csatlakozni egy virtuális géphez, a következő forgatókönyveket tapasztalja:

- A virtuális gép képernyőképe azt mutatja, hogy az operációs rendszer teljesen be van töltve, és a hitelesítő adatokra vár.

    ![Képernyőkép a virtuális gép állapotáról](./media/troubleshoot-remote-desktop-services-issues/login-page.png)

- Távolról megtekintheti az eseménynaplókat a virtuális gép en az Eseménynapló használatával. Láthatja, hogy a Távoli asztali szolgáltatások (TermService) nem indul el, vagy nem indul el. A következő napló egy minta:

    **Napló neve**: Rendszer </br>
    **Forrás**: Szolgáltatásvezérlő </br>
    **Dátum**: 12/16/2017 11:19:36 AM</br>
    **Eseményazonosító**: 7022</br>
    **Feladatkategória**: Nincs</br>
    **Szint**: Hiba</br>
    **Kulcsszavak:** Klasszikus</br>
    **Felhasználó**: N/A</br>
    **Számítógép**: vm.contoso.com</br>
    **Leírás**: A Távoli asztali szolgáltatások szolgáltatás indításkor megindult. 

    A Serial Access Console szolgáltatással a következő lekérdezés futtatásával is megkeresheti ezeket a hibákat: 

        wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more 

## <a name="cause"></a>Ok
 
A probléma oka az, hogy a Távoli asztali szolgáltatások szolgáltatás nem fut a virtuális gépen. Az ok a következő esetektől függhet: 

- A TermService szolgáltatás **letiltott**értékre van állítva. 
- A TermService szolgáltatás összeomlik, vagy nem válaszol. 
- A TermService nem indul el, mert helytelen konfiguráció van.

## <a name="solution"></a>Megoldás

A probléma elhárításához használja a Soros konzolt. Vagy pedig [javítsa ki a virtuális gép offline](#repair-the-vm-offline) csatlakoztatásával a virtuális gép operációs rendszer lemeze egy helyreállítási virtuális gép.

### <a name="use-serial-console"></a>Soros konzol használata

1. A [Soros konzol](serial-console-windows.md) eléréséhez válassza **a Támogatás & a hibaelhárítási** > **soros konzolt.** Ha a szolgáltatás engedélyezve van a virtuális gép, a virtuális gép sikeresen csatlakoztatható.

2. Hozzon létre egy új csatornát egy CMD-példányhoz. Adja meg a **CMD-t** a csatorna elindításához és a csatorna nevének beírásához.

3. Váltson a CMD-példányt futtató csatornára. Ebben az esetben az 1-es csatornának kell lennie:

   ```
   ch -si 1
   ```

4. Válassza **az Enter** again lehetőséget, és adjon meg egy érvényes felhasználónevet és jelszót, helyi vagy tartományazonosítót a virtuális géphez.

5. A TermService szolgáltatás állapotának lekérdezése:

   ```
   sc query TermService
   ```

6. Ha a szolgáltatás állapota **Leállítva**látható, próbálja meg elindítani a szolgáltatást:

    ```
    sc start TermService
     ``` 

7. A szolgáltatás ismételt lekérdezése a szolgáltatás sikeres elindításához:

   ```
   sc query TermService
   ```
8. Ha a szolgáltatás nem indul el, kövesse a megoldást a kapott hiba alapján:

    |  Hiba |  Javaslat |
    |---|---|
    |5 - HOZZÁFÉRÉS MEGTAGADVA |Lásd: [A TermService szolgáltatás hozzáférés-megtagadási hiba miatt leállt.](#termservice-service-is-stopped-because-of-an-access-denied-problem) |
    |1053 - ERROR_SERVICE_REQUEST_TIMEOUT  |Lásd: [A TermService szolgáltatás le van tiltva.](#termservice-service-is-disabled)  |  
    |1058 - ERROR_SERVICE_DISABLED  |Lásd: [A TermService szolgáltatás összeomlik vagy lefagy.](#termservice-service-crashes-or-hangs)  |
    |1059 - ERROR_CIRCULAR_DEPENDENCY |A probléma gyors megoldásához [forduljon](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) az ügyfélszolgálathoz.|
    |1067 - ERROR_PROCESS_ABORTED  |Lásd: [A TermService szolgáltatás összeomlik vagy lefagy.](#termservice-service-crashes-or-hangs)  |
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL|A probléma gyors megoldásához [forduljon](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) az ügyfélszolgálathoz.|
    |1069 - ERROR_SERVICE_LOGON_FAILED  |Lásd: [A TermService szolgáltatás sikertelen bejelentkezési hiba miatt](#termservice-service-fails-because-of-logon-failure) |
    |1070 - ERROR_SERVICE_START_HANG   | Lásd: [A TermService szolgáltatás összeomlik vagy lefagy.](#termservice-service-crashes-or-hangs) |
    |1077 - ERROR_SERVICE_NEVER_STARTED   | Lásd: [A TermService szolgáltatás le van tiltva.](#termservice-service-is-disabled)  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   |A probléma gyors megoldásához [forduljon](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) az ügyfélszolgálathoz. |
    |1753   |A probléma gyors megoldásához [forduljon](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) az ügyfélszolgálathoz.   |
    
#### <a name="termservice-service-is-stopped-because-of-an-access-denied-problem"></a>A TermService szolgáltatás leáll egy hozzáférés megtagadása miatt probléma miatt

1. Csatlakozzon a [Soros konzolhoz,](serial-console-windows.md) és nyisson meg egy PowerShell-példányt.
2. Töltse le a Folyamatfigyelő eszközt a következő parancsfájl futtatásával:

   ```
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

4. Reprodukálja a problémát a hozzáférés **megtagadva**szolgáltatást nyújtó szolgáltatás elindításával: 

   ```
   sc start TermService 
   ```

   Ha nem sikerül, szakítsa meg a Folyamatfigyelő nyomkövetését:

   ```   
   procmon /Terminate 
   ```

5. Gyűjtse össze a **c:\temp\ProcMonTrace.PML**fájlt:

    1. [Adatlemez csatolása a virtuális géphez](../windows/attach-managed-disk-portal.md
).
    2. A Serial Console segítségével a fájlt az új meghajtóra másolhatja. Például: `copy C:\temp\ProcMonTrace.PML F:\`. Ebben a parancsban Az F a csatolt adatlemez illesztőprogram-betűjele.
    3. Válassza le az adatmeghajtót, és csatolja egy működő virtuális géphez, amelyen telepítve van a Folyamatfigyelő ubstakke.

6. Nyissa meg **a ProconTrace.PML-t** a működő virtuális gép folyamatfigyelésével. Ezután a szűrés eredmény szerint **az ACCESS DENIED**, ahogy az a következő képernyőképen látható:

    ![Szűrés eredmény szerint a Folyamatfigyelőben](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

 
6. Javítsa ki a kimeneten lévő beállításkulcsokat, mappákat vagy fájlokat. Ezt a problémát általában akkor okozza, ha a szolgáltatásban használt bejelentkezési fiók nem rendelkezik ACL-engedéllyel ezekhez az objektumokhoz. A bejelentkezési fiók megfelelő ACL-engedélyének megismeréséhez ellenőrizheti a kifogástalan állapotú virtuális gép. 

#### <a name="termservice-service-is-disabled"></a>A TermService szolgáltatás le van tiltva

1. A szolgáltatás visszaállítása az alapértelmezett indítási értékre:

   ```
   sc config TermService start= demand 
   ```

2. Indítsa el a szolgáltatást:

   ```
   sc start TermService
   ```

3. Ismét lekell kérdezni az állapotát, hogy a szolgáltatás futjon:

   ```
   sc query TermService 
   ```

4. Próbáljon meg csatlakozni a virtuális géphez a Távoli asztal használatával.

#### <a name="termservice-service-fails-because-of-logon-failure"></a>A TermService szolgáltatás bejelentkezési hiba miatt sikertelen

1. Ez a probléma akkor fordul elő, ha a szolgáltatás indítási fiókja megváltozott. Ezt visszamódosította az alapértelmezett értékre: 

        sc config TermService obj= 'NT Authority\NetworkService'
2. Indítsa el a szolgáltatást:

        sc start TermService
3. Próbáljon meg csatlakozni a virtuális géphez a Távoli asztal használatával.

#### <a name="termservice-service-crashes-or-hangs"></a>A TermService szolgáltatás összeomlik vagy lefagy
1. Ha a szolgáltatás állapota beragadt az **Indítás** vagy **a Leállítás**szolgáltatásban, próbálja meg leállítani a szolgáltatást: 

        sc stop TermService
2. Különítse el a szolgáltatást a saját "svchost" tárolóján:

        sc config TermService type= own
3. Indítsa el a szolgáltatást:

        sc start TermService
4. Ha a szolgáltatás továbbra sem indul el, [forduljon az ügyfélszolgálathoz.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

### <a name="repair-the-vm-offline"></a>A virtuális gép kapcsolat nélküli javítása

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Az operációs rendszer lemezének csatolása helyreállítási virtuális géphez

1. [Csatlakoztassa az operációs rendszer lemezét egy helyreállítási virtuális géphez.](../windows/troubleshoot-recovery-disks-portal.md)
2. Távoli asztali kapcsolat indítása a helyreállítási virtuális géppel. Győződjön meg arról, hogy a csatlakoztatott lemez **online** ként van megjelölve a Lemezkezelés konzolon. Jegyezze fel a csatlakoztatott operációsrendszer-lemezhez rendelt meghajtóbetűjelet.
3. Nyisson meg egy rendszergazdai jogú parancssorpéldányt (**Futtatás rendszergazdaként**). Ezután futtassa a következő parancsfájlt. Feltételezzük, hogy a csatlakoztatott operációsrendszer-lemezhez rendelt meghajtóbetűjel **F**. Cserélje le a megfelelő értéket a virtuális gép. 

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

4. [Válassza le az operációs rendszer lemezét, és hozza létre újra a virtuális gép](../windows/troubleshoot-recovery-disks-portal.md). Ezután ellenőrizze, hogy a probléma megoldódott-e.

## <a name="need-help-contact-support"></a>Segítségre van szüksége? Kapcsolatfelvétel a támogatási szolgáltatással

Ha továbbra is segítségre van szüksége, a probléma megoldásához [forduljon](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) az ügyfélszolgálathoz.
