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
ms.openlocfilehash: 39b793e2722766f3f28829b4dc48534054abd97e
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49989219"
---
# <a name="remote-desktop-services-is-not-starting-on-an-azure-vm"></a>Az Azure virtuális gép nem indul el a távoli asztali szolgáltatások

Ez a cikk ismerteti a kapcsolódáskor, egy Azure virtuális gép (VM), amikor nem indítása vagy nem indul el a távoli asztali szolgáltatások (TermService) kapcsolatos problémák elhárítása.

>[!NOTE]
>Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../../azure-resource-manager/resource-manager-deployment-model.md). A cikk a Resource Manager üzemi modell használatával. Azt javasoljuk, hogy az új központi telepítéseknél a klasszikus üzemi modell helyett ezt a modellt használja.

## <a name="symptoms"></a>Probléma

Ha egy virtuális Géphez való csatlakozáshoz próbál, a következő esetekben tapasztalja:

- A virtuális gép képernyőképen látható, az operációs rendszer teljesen betöltött és Várakozás a hitelesítő adatokat.
- A virtuális gép az összes alkalmazás a várt módon működik, és elérhető.
- A virtuális gép válaszol a a TCP-kapcsolatot a Microsoft Remote Desktop Protocol (RDP) port (alapértelmezés: 3389).
- Nem kéri a hitelesítő adatokat, amikor egy RDP-kapcsolatot próbál.

## <a name="cause"></a>Ok

A probléma oka, hogy a távoli asztali szolgáltatások nem fut a virtuális gépen. Az OK függ a következő esetekben:

- A TermService szolgáltatás lett beállítva, **letiltott**.
- A TermService szolgáltatás összeomlik, vagy a függő.

## <a name="solution"></a>Megoldás

A probléma megoldásához használja az alábbi megoldások egyikét, vagy próbálja meg egyesével megoldások:

### <a name="solution-1-using-the-serial-console"></a>1. megoldás: A soros konzol használata

1. Hozzáférés a [soros konzol](serial-console-windows.md) kiválasztásával **támogatás és hibaelhárítás** > **soros konzol (előzetes verzió)**. A szolgáltatás engedélyezve van a virtuális gépen, a virtuális gép sikeresen csatlakoztathatja.

2. Hozzon létre egy új csatorna a CMD-példányhoz. Típus **CMD** elindítja a csatornát a csatorna nevének lekérése érdekében.

3. Váltson a csatornát a CMD-példányt futtató, ebben az esetben 1 csatornát kell lennie.

   ```
   ch -si 1
   ```

4. Nyomja meg **Enter** újra és írja be egy érvényes felhasználónevet és jelszót (helyi vagy tartományi azonosító) a virtuális gép számára.

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

### <a name="solution-2-using-a-recovery-vm-to-enable-the-service"></a>2. megoldás: Egy helyreállítási virtuális Gépet használ a szolgáltatás engedélyezése

[Készítsen biztonsági másolatot az operációsrendszer-lemez](../windows/snapshot-copy-managed-disk.md) és [az operációsrendszer-lemez csatolása a virtuális gép mentési](../windows/troubleshoot-recovery-disks-portal.md). Ezután nyisson meg egy rendszergazda jogú CMD-példányt, és futtassa az alábbi parancsfájlok a készenléti Virtuálisgép:

>[!NOTE]
>Azt feltételezik, hogy a meghajtóbetűjel, a csatlakoztatott operációsrendszer-lemez rendelt F. cserélje le a megfelelő értéket a virtuális gépen. Miután ez elkészült, válassza le a lemezt a helyreállítási virtuális Gépet és [hozza létre újból a virtuális gép](../windows/create-vm-specialized.md). További hibaelhárításhoz használható **megoldás 1** , mert a soros konzol engedélyezve van.

```
reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM

REM Enable Serial Console
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /set {bootmgr} displaybootmenu yes
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /set {bootmgr} timeout 10
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /set {bootmgr} bootems yes
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /ems {<Boot Loader Identifier>} ON
bcdedit /store <Volume Letter Where The BCD Folder Is>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

REM Get the current ControlSet from where the OS is booting
for /f "tokens=3" %x in ('REG QUERY HKLM\BROKENSYSTEM\Select /v Current') do set ControlSet=%x
set ControlSet=%ControlSet:~2,1%

REM Suggested configuration to enable OS Dump
set key=HKLM\BROKENSYSTEM\ControlSet00%ControlSet%\Control\CrashControl
REG ADD %key% /v CrashDumpEnabled /t REG_DWORD /d 2 /f
REG ADD %key% /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
REG ADD %key% /v NMICrashDump /t REG_DWORD /d 1 /f

REM Set default values back on the broken service
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v ImagePath /t REG_EXPAND_SZ /d "<Image Path>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v ObjectName /t REG_SZ /d "<Startup Account>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Process Name>" /v type /t REG_DWORD /d 16 /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v ImagePath /t REG_EXPAND_SZ /d "<Startup Account>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v ObjectName /t REG_SZ /d "<Startup Account>" /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Process Name>" /v type /t REG_DWORD /d 16 /f

REM Enable default dependencies from the broken service
reg add "HKLM\BROKENSYSTEM\ControlSet001\services\<Driver/Service Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg add "HKLM\BROKENSYSTEM\ControlSet002\services\<Driver/Service Name>" /v start /t REG_DWORD /d <Startup Type> /f
reg unload HKLM\BROKENSYSTEM
```

## <a name="need-help-contact-support"></a>Segítség Kapcsolatfelvétel a támogatási szolgáltatással

Ha továbbra is segítségre van szüksége, [forduljon az ügyfélszolgálathoz](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a probléma gyors megoldása érdekében.
