---
title: Távoli asztali szolgáltatások nem indul el Azure-beli virtuális gépen | Microsoft Docs
description: A virtuális géphez való csatlakozáskor megtudhatja, hogyan lehet elhárítani a Távoli asztali szolgáltatások kapcsolatos problémákat. Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 845e9c17d6f7facb4e24f3069b3622b6449295ca
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70103337"
---
# <a name="remote-desktop-services-isnt-starting-on-an-azure-vm"></a>Távoli asztali szolgáltatások nem Azure-beli virtuális gépen indul

Ez a cikk az Azure-beli virtuális gépekhez (VM) és Távoli asztali szolgáltatások vagy TermService való csatlakozás során felmerülő hibák elhárítását ismerteti, nem indul el vagy nem indul el.

> [!NOTE]  
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és működéséhez: [Azure Resource Manager és klasszikus](../../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk a Resource Manager-alapú üzemi modell használatát ismerteti. Javasoljuk, hogy ezt a modellt a klasszikus üzemi modell helyett új központi telepítések esetén használja.

## <a name="symptoms"></a>Probléma

Amikor megpróbál csatlakozni egy virtuális géphez, a következő esetekben fog megjelenni:

- A virtuális gép képernyőképe azt mutatja, hogy az operációs rendszer teljes mértékben be van töltve, és a hitelesítő adatokra vár.

    ![Képernyőfelvétel a virtuális gép állapotáról](./media/troubleshoot-remote-desktop-services-issues/login-page.png)

- Ön távolról tekintse meg az eseménynaplókat a virtuális gépen az Eseménynapló használatával. Láthatja, hogy Távoli asztali szolgáltatások, TermService, nem indul el vagy nem indul el. A következő napló egy példa:

    **Napló neve**:      Rendszer </br>
    **Forrás**:        Service Control Manager </br>
    **Dátum**:          12/16/2017 11:19:36</br>
    **Eseményazonosító**:      7022</br>
    **Feladat kategóriája**: Nincsenek</br>
    **Szint**:         Hiba</br>
    **Kulcsszavak**:      Klasszikus</br>
    **Felhasználó**:          –</br>
    **Számítógép**: VM.contoso.com</br>
    **Leírás**: A Távoli asztali szolgáltatások szolgáltatás indításkor lefagyott. 

    Az alábbi lekérdezés futtatásával is megkeresheti ezeket a hibákat a soros hozzáférés konzoljának használatával: 

        wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more 

## <a name="cause"></a>Ok
 
Ez a probléma azért fordul elő, mert Távoli asztali szolgáltatások nem fut a virtuális gépen. Az ok a következő esetekben változhat: 

- A TermService szolgáltatás letiltvaértékre van állítva. 
- A TermService szolgáltatás összeomlik vagy nem válaszol. 
- A TermService helytelen konfiguráció miatt nem indul el.

## <a name="solution"></a>Megoldás

A probléma megoldásához használja a soros konzolt. Másik lehetőségként [javítsa ki a virtuális gépet](#repair-the-vm-offline) úgy, hogy a virtuális gép operációsrendszer-lemezét egy helyreállítási virtuális géphez csatolja.

### <a name="use-serial-console"></a>Soros konzol használata

1. A [soros konzolt](serial-console-windows.md) a **support & hibaelhárítás** > **Serial Console**lehetőség kiválasztásával érheti el. Ha a szolgáltatás engedélyezve van a virtuális gépen, akkor a virtuális gép sikeresen csatlakoztatható.

2. Hozzon létre egy új csatornát egy CMD-példányhoz. Írja be a **cmd parancsot** a csatorna elindításához és a csatorna nevének lekéréséhez.

3. Váltson arra a csatornára, amelyen a CMD-példány fut. Ebben az esetben az 1. csatornát kell megadnia:

   ```
   ch -si 1
   ```

4. Válassza újra az **ENTER billentyűt** , és adjon meg egy érvényes felhasználónevet és jelszót, helyi vagy tartományi azonosítót a virtuális géphez.

5. A TermService szolgáltatás állapotának lekérdezése:

   ```
   sc query TermService
   ```

6. Ha a szolgáltatás állapota **leállt**, próbálja meg elindítani a szolgáltatást:

    ```
    sc start TermService
     ``` 

7. Kérdezze le újra a szolgáltatást, hogy meggyőződjön arról, hogy a szolgáltatás sikeresen elindult:

   ```
   sc query TermService
   ```
8. Ha a szolgáltatás nem indul el, a kapott hiba alapján kövesse a megoldást:

    |  Hiba |  Javaslat |
    |---|---|
    |5 – HOZZÁFÉRÉS MEGTAGADVA |[A hozzáférés-megtagadási hiba miatt a TermService szolgáltatás leállt](#termservice-service-is-stopped-because-of-an-access-denied-problem). |
    |1053 - ERROR_SERVICE_REQUEST_TIMEOUT  |Lásd: [a TermService szolgáltatás le van tiltva](#termservice-service-is-disabled).  |  
    |1058 - ERROR_SERVICE_DISABLED  |Lásd: [TermService szolgáltatás összeomlik vagy lefagy](#termservice-service-crashes-or-hangs).  |
    |1059 - ERROR_CIRCULAR_DEPENDENCY |A probléma gyors megoldásához [forduljon](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) az ügyfélszolgálathoz.|
    |1067 - ERROR_PROCESS_ABORTED  |Lásd: [TermService szolgáltatás összeomlik vagy lefagy](#termservice-service-crashes-or-hangs).  |
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL|A probléma gyors megoldásához [forduljon](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) az ügyfélszolgálathoz.|
    |1069 - ERROR_SERVICE_LOGON_FAILED  |[A bejelentkezési hiba miatt nem sikerül a TermService szolgáltatás](#termservice-service-fails-because-of-logon-failure) : |
    |1070 - ERROR_SERVICE_START_HANG   | Lásd: [TermService szolgáltatás összeomlik vagy lefagy](#termservice-service-crashes-or-hangs). |
    |1077 - ERROR_SERVICE_NEVER_STARTED   | Lásd: [a TermService szolgáltatás le van tiltva](#termservice-service-is-disabled).  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   |A probléma gyors megoldásához [forduljon](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) az ügyfélszolgálathoz. |
    |1753   |A probléma gyors megoldásához [forduljon](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) az ügyfélszolgálathoz.   |
    
#### <a name="termservice-service-is-stopped-because-of-an-access-denied-problem"></a>A TermService szolgáltatás egy hozzáférés-megtagadási probléma miatt leáll

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

4. A **hozzáférés**megtagadását biztosító szolgáltatás indításával reprodukálja a problémát: 

   ```
   sc start TermService 
   ```

   Ha nem sikerül, leállítja a folyamat figyelője nyomkövetés:

   ```   
   procmon /Terminate 
   ```

5. Gyűjtse össze a fájl **c:\temp\ProcMonTrace.PML**:

    1. [Adatlemez csatolása a virtuális géphez](../windows/attach-managed-disk-portal.md
).
    2. A fájl átmásolása az új meghajtó soros konzol használata. Például: `copy C:\temp\ProcMonTrace.PML F:\`. Ebben a parancsban F a csatlakoztatott lemez meghajtóbetűjelét.
    3. Válassza le az adatmeghajtót, és csatolja egy olyan működő virtuális gépen, amelyen telepítve van a Process monitor ubstakke.

6. Nyissa meg a **ProcMonTrace. PML** a Munkavirtuális gép folyamat-figyelési funkciójával. Majd szűrés **eredménye a hozzáférés MEGTAGADVA**, ahogy az alábbi képernyőfelvételen látható:

    ![Szűrés eredménye a folyamat figyelője](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

 
6. Javítsa ki a beállításkulcsokat, mappákhoz vagy a kimeneti fájlok. Általában a problémát az okozza, ha a bejelentkezési fiókot, amelynek a szolgáltatás nem rendelkezik ACL ezek az objektumok hozzáférési engedélyt. A bejelentkezési fiók megfelelő ACL-engedélyének megismeréséhez megtekintheti az egészséges virtuális gépet. 

#### <a name="termservice-service-is-disabled"></a>A TermService szolgáltatás le van tiltva

1. Állítsa vissza a szolgáltatás az alapértelmezett rendszerindító értékre:

   ```
   sc config TermService start= demand 
   ```

2. Indítsa el a szolgáltatást:

   ```
   sc start TermService
   ```

3. A szolgáltatás futásának ellenőrzéséhez ismét kérdezze le az állapotát:

   ```
   sc query TermService 
   ```

4. A Távoli asztal használatával próbáljon csatlakozni a virtuális géphez.

#### <a name="termservice-service-fails-because-of-logon-failure"></a>A TermService szolgáltatás sikertelen bejelentkezési hiba miatt meghiúsult

1. Ez a probléma akkor fordul elő, ha a szolgáltatás indítási fiókja módosult. A visszaállítás visszaállítva az alapértelmezett értékre: 

        sc config TermService obj= 'NT Authority\NetworkService'
2. Indítsa el a szolgáltatást:

        sc start TermService
3. A Távoli asztal használatával próbáljon csatlakozni a virtuális géphez.

#### <a name="termservice-service-crashes-or-hangs"></a>TermService szolgáltatás összeomlik vagy lefagy
1. Ha a szolgáltatás állapota **Indítás** vagy **Leállítás**közben megakad, próbálja meg leállítani a szolgáltatást: 

        sc stop TermService
2. A szolgáltatás a saját "svchost" tároló elkülönítése:

        sc config TermService type= own
3. Indítsa el a szolgáltatást:

        sc start TermService
4. Ha a szolgáltatás még nem indul el, forduljon az [ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

### <a name="repair-the-vm-offline"></a>Javítsa ki a virtuális Gépet kapcsolat nélküli módban

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális Géphez

1. [Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális Géphez](../windows/troubleshoot-recovery-disks-portal.md).
2. Indítsa el a helyreállítási virtuális Gépet egy távoli asztali kapcsolatot. Győződjön meg arról, hogy a csatlakoztatott lemez megjelölt **Online** a Lemezkezelés konzol. Vegye figyelembe a meghajtóbetűjelet, amely a csatlakoztatott operációsrendszer-lemez van rendelve.
3. Nyissa meg a rendszergazda jogú parancssort (**Futtatás rendszergazdaként**). Ezután futtassa az alábbi parancsfájlt. Feltételezzük, hogy a csatlakoztatott operációsrendszer-lemezhez rendelt meghajtóbetűjel **F**. Cserélje le a megfelelő értékre a virtuális gépen. 

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

4. [Az operációsrendszer-lemez leválasztása, és hozza létre újra a virtuális gép](../windows/troubleshoot-recovery-disks-portal.md). Ezután győződjön meg arról, hogy a probléma megoldódott-e.

## <a name="need-help-contact-support"></a>Segítség Forduljon a támogatási szolgálathoz.

Ha továbbra is segítségre van szüksége, forduljon az ügyfélszolgálathoz, és [kérje](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) meg a probléma megoldását.
