---
title: Az Azure virtuális gép nem indul el a távoli asztali szolgáltatások |} A Microsoft Docs
description: 'Útmutató: távoli asztali szolgáltatásokkal együtt hibák elhárításához, ha egy virtuális gép csatlakoztatása |} A Microsoft Docs'
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
ms.openlocfilehash: a9967aec61aaab5bc6b4517407f36e2a6c7342c8
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50238862"
---
# <a name="remote-desktop-services-isnt-starting-on-an-azure-vm"></a>Távoli asztali szolgáltatások-beli virtuális gépen nem indítása

Ez a cikk bemutatja, hogyan kapcsolódik, egy Azure virtuális gép (VM), amikor a távoli asztali szolgáltatások (TermService) nem indítása, vagy nem indul el a hibaelhárítás.

>[!NOTE]
>Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../../azure-resource-manager/resource-manager-deployment-model.md). A cikk a Resource Manager üzemi modell használatával. Azt javasoljuk, hogy az új központi telepítéseknél a klasszikus üzemi modell helyett ezt a modellt használja.

## <a name="symptoms"></a>Probléma

Ha egy virtuális Géphez való csatlakozáshoz próbál, a következő esetekben tapasztalja:

- A virtuális gép képernyőképen látható, az operációs rendszer teljesen betöltött és Várakozás a hitelesítő adatokat.

    ![Képernyőkép a virtuális gép állapota](./media/troubleshoot-remote-desktop-services-issues/login-page.png)

- Ön távolról tekintse meg az eseménynaplókat a virtuális gépen az Eseménynapló használatával úgy, hogy a távoli asztali szolgáltatások (Terminálkiszolgáló) nem indítása vagy nem indul el. Az alábbiakban látható egy minta napló:

    **Naplófájl neve**: rendszer </br>
    **Forrás**: szolgáltatásvezérlő kezelője </br>
    **Dátum**: 12/16/2017-11:19:36 AM</br>
    **Eseményazonosító**: 7022</br>
    **Feladat kategóriája**: nincs</br>
    **Szint**: hiba</br>
    **A kulcsszavak**: klasszikus</br>
    **Felhasználói**: N/A</br>
    **Számítógép**: vm.contoso.com</br>
    **Leírás**: A távoli asztali szolgáltatások szolgáltatás indításkor lefagyott. 

    A soros hozzáférés funkció segítségével keresse meg ezeket a hibákat, használja a következő lekérdezést: 

        wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more 

## <a name="cause"></a>Ok
 
A probléma oka, hogy a távoli asztali szolgáltatások nem fut a virtuális gépen. Az OK függ a következő esetekben: 

- A TermService szolgáltatás **letiltott**. 
- A TermService szolgáltatás összeomlik, vagy a függő. 

## <a name="solution"></a>Megoldás

A probléma elhárításához, használja a soros konzol vagy [javítsa ki a virtuális Gépet offline](#repair-the-vm-offline) által a virtuális gép operációsrendszer-lemez egy helyreállítási virtuális Géphez csatolása.

### <a name="use-serial-console"></a>Soros konzol használata

1. Hozzáférés a [soros konzol](serial-console-windows.md) kiválasztásával **támogatás és hibaelhárítás** > **soros konzol**. A szolgáltatás engedélyezve van a virtuális gépen, a virtuális gép sikeresen csatlakoztathatja.

2. Hozzon létre egy új csatorna a CMD-példányhoz. Típus **CMD** elindítja a csatornát a csatorna nevének lekérése érdekében.

3. Váltson a csatornát a CMD-példányt futtató. Ebben az esetben 1 csatornát kell lennie.

   ```
   ch -si 1
   ```

4. Nyomja meg **Enter** újra, és adja meg egy érvényes felhasználónevet és jelszót (helyi vagy tartományi azonosító) a virtuális gép számára.

5. A lekérdezés a TermService szolgáltatás állapotát.

   ```
   sc query TermService
   ```

6. Ha a szolgáltatás állapotát jeleníti meg **leállítva**, próbálja meg elindítani a szolgáltatást.

    ```
    sc start TermService
     ``` 

7. A lekérdezés a szolgáltatás ismét győződjön meg arról, hogy a szolgáltatás sikeresen elindult-e.

   ```
   sc query TermService
   ```
    Ha a szolgáltatás nem indul el, kövesse a megoldás, amely az észlelt hiba:

    |  Hiba |  Javaslat |
    |---|---|
    |5 – HOZZÁFÉRÉS MEGTAGADVA |Lásd: [TermService szolgáltatás le van állítva, mert a hozzáférés megtagadva hiba](#termService-service-is-stopped-because-of-access-denied-error) |
    |1058 - ERROR_SERVICE_DISABLED  |Lásd: [TermService szolgáltatás le van tiltva.](#termService-service-is-disabled)  |
    |1059 - ERROR_CIRCULAR_DEPENDENCY |[Forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása|
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL|[Forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása|
    |1069 - ERROR_SERVICE_LOGON_FAILED  |[Forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása    |
    |1070 - ERROR_SERVICE_START_HANG   | [Forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása  |
    |1077 - ERROR_SERVICE_NEVER_STARTED   | Lásd: [TermService szolgáltatás le van tiltva](#termService-service-is-disabled)  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   |[Forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása |
    |1753   |[Forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása   |

#### <a name="termservice-service-is-stopped-because-of-access-denied-error"></a>TermService szolgáltatás le van állítva, mert a hozzáférés megtagadva hiba

1. Csatlakozás [soros konzol](serial-console-windows.md#) , és nyissa meg egy PowerShell-példány.
2. Töltse le a folyamat figyelője eszközt a következő szkript futtatásával:

        remove-module psreadline  
        $source = "https://download.sysinternals.com/files/ProcessMonitor.zip" 
        $destination = "c:\temp\ProcessMonitor.zip" 
        $wc = New-Object System.Net.WebClient 
        $wc.DownloadFile($source,$destination) 
3. Most indítsa el a nyomkövetési procmon:

        procmon /Quiet /Minimized /BackingFile c:\temp\ProcMonTrace.PML 
4. A probléma a szolgáltatás, amely lehetővé teszi hozzáférési indításával reprodukálása megtagadása: 

        sc start TermService 
        
    Ha sikertelen, lépjen tovább, és leállítja a folyamatot a figyelő nyomkövetési:

        procmon /Terminate 
5. A fájl begyűjtése **c:\temp\ProcMonTrace.PML**, nyissa meg a procmon használatával, és majd szűrés **eredménye a hozzáférés MEGTAGADVA** , az alábbi képernyőfelvételen látható:

    ![Szűrés eredménye a folyamat figyelője](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

 
6. Javítsa ki a beállításkulcsok, mappákat vagy fájlokat, amelyek a kimenetben. Általában ez a probléma okozza a napló a szolgáltatásban használt fiók nincs engedélye ACL elérni ezeket az objektumokat. Tudni, hogy a megfelelő ACL-t a bejelentkezési fiók engedéllyel, ellenőrizheti a virtuális gép kifogástalan állapotú. 

#### <a name="termservice-service-is-disabled"></a>TermService szolgáltatás le van tiltva

1.  Állítsa vissza a szolgáltatás az alapértelmezett rendszerindító értékre:

        sc config TermService start= demand 
        
2.  Indítsa el a szolgáltatást:

        sc start TermService 
3.  Ismét, hogy ellenőrizze, fut-e a szolgáltatás állapotának lekérdezéséhez: sc lekérdezés TermService 
4.  Próbálja meg conntet virtuális géphez a távoli asztal használatával.


### <a name="repair-the-vm-offline"></a>Javítsa ki a virtuális Gépet kapcsolat nélküli módban

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális Géphez

1. [Csatlakoztassa az operációsrendszer-lemezt egy helyreállítási virtuális Géphez](../windows/troubleshoot-recovery-disks-portal.md).
2. Indítsa el a helyreállítási virtuális Gépet egy távoli asztali kapcsolatot. Győződjön meg arról, hogy a csatlakoztatott lemez megjelölt **Online** a Lemezkezelés konzol. Vegye figyelembe a meghajtóbetűjelet, amely a csatlakoztatott operációsrendszer-lemez van rendelve.
3.  Nyissa meg a rendszergazda jogú parancssort (**Futtatás rendszergazdaként**), majd futtassa az alábbi parancsfájlt. Azt feltételezik, hogy a meghajtóbetűjel, a csatlakoztatott operációsrendszer-lemez rendelt F. cserélje le a megfelelő értéket a virtuális gépen. 

        reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv
        
        REM Set default values back on the broken service 
        reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v start /t REG_DWORD /d 3 /f
        reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService“ /f
        reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v type /t REG_DWORD /d 16 /f
        reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v start /t REG_DWORD /d 3 /f
        reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService" /f
        reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v type /t REG_DWORD /d 16 /f
4. [Az operációsrendszer-lemez leválasztása, és hozza létre újra a virtuális gép](../windows/troubleshoot-recovery-disks-portal.md), és ellenőrizze, hogy a probléma megoldódott.

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha továbbra is segítségre van szüksége, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.
