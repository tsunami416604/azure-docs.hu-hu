---
title: Tiltsa le a vendég operációs rendszer tűzfala az Azure virtuális Gépen |} A Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: willchen
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: a8856bd46f516aa3c64965648d4f23b9ba665b1b
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56820025"
---
# <a name="disable-the-guest-os-firewall-in-azure-vm"></a>A vendég operációs rendszer tűzfalának letiltása az Azure-beli virtuális gépen

Ez a cikk egy hivatkozás, amelyben azt gyanítja, hogy a vendég operációs rendszer tűzfala van-e egy virtuális géphez (VM) részleges vagy teljes forgalmat szűrő helyzetekben. Ez akkor fordulhat elő, ha szándékosan változás által okozott sikertelen RDP-kapcsolatok a tűzfalon.

## <a name="solution"></a>Megoldás

Az ebben a cikkben leírt folyamat célja, hogy a valódi problémát, és hogyan állítható be a tűzfalszabályok helyesen van kijavítása összpontosíthat Áthidaló megoldásként használható. It\rquote s szeretné, hogy a Windows tűzfal összetevőnek engedélyezve kell lennie a Microsoft ajánlott eljárás. Hogyan konfigurálja a tűzfal-szabályok \cf3 attól függ, hogy a virtuális gép that\rquote s szükséges hozzáférési szintjét.

### <a name="online-solutions"></a>Online megoldások 

Ha a virtuális gép online állapotban, és a egy másik virtuális Géphez ugyanazon a virtuális hálózaton elérhetők, ezek a megoldások teheti a virtuális gép használatával.

#### <a name="mitigation-1-custom-script-extension-or-run-command-feature"></a>1. megoldás: Egyéni szkriptek futtatására szolgáló bővítmény vagy futtatása paranccsal funkció

Ha rendelkezik egy működő Azure-ügynököt, akkor használhatja [egyéni szkriptek futtatására szolgáló bővítmény](../extensions/custom-script-windows.md) vagy a [parancsok futtatása](../windows/run-command.md) távolról futtassa az alábbi parancsfájlok a szolgáltatás (csak Resource Manager virtuális gépek esetén).

> [!Note]
> * Ha a tűzfal helyileg be van állítva, futtassa a következő szkriptet:
>   ```
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\Standardprofile' -name "EnableFirewall" -Value 0 
>   Restart-Service -Name mpssvc
>   ```
> * Ha a tűzfal be van állítva, egy Active Directory csoportházirend segítségével, használhatja a következő parancsprogrammal ideiglenes hozzáférés céljából. 
>   ```
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile' name "EnableFirewall" -Value 0
>   Restart-Service -Name mpssvc
>   ```
>   Azonban, amint a rendszer újra alkalmazza a házirendet, meg fogjuk kell problémaelhárító kívül a távoli munkamenetet. Az állandó a probléma javítása, hogy az ezen a számítógépen alkalmazott házirend módosítása.

#### <a name="mitigation-2-remote-powershell"></a>2. megoldás: Remote PowerShell

1.  Kapcsolódás virtuális Géphez a virtuális gép RDP-kapcsolaton keresztül nem éri el ugyanazon a virtuális hálózaton található.

2.  Nyisson meg egy PowerShell-konzolablakot.

3.  Futtassa az alábbi parancsot:

    ```powershell
    Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
    netsh advfirewall set allprofiles state off
    Restart-Service -Name mpssvc 
    exit
    ```

> [!Note]
> A tűzfalon keresztül csoportházirend-objektum be van állítva, ha ez a módszer nem működnek, mivel ez a parancs csak a helyi beállításjegyzék-bejegyzések változik. Ha olyan házirend van beállítva, az felül fogja írni a módosítás. 

#### <a name="mitigation-3-pstools-commands"></a>3. megoldás: PSTools parancsok

1.  Töltse le a hibaelhárító virtuális Géphez, a [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools).

2.  Nyisson meg egy CMD-példányt, ezután pedig hozzáfér a virtuális gép a DIP keresztül.

3.  Futtassa az alábbi parancsot:

    ```cmd
    psexec \\<DIP> -u <username> cmd
    netsh advfirewall set allprofiles state off
    psservice restart mpssvc
    ```

#### <a name="mitigation-4-remote-registry"></a>4. megoldás: Remote Registry 

Kövesse az alábbi lépéseket a használandó [távoli beállításjegyzék](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry).

1.  A hibaelhárító virtuális Géphez, indítsa el a Beállításszerkesztőt, és folytassa a **fájl** > **csatlakozás hálózati beállításjegyzék**.

2.  Nyissa meg a *CÉLGÉPEN*\SYSTEM ágban, és adja meg a következő értékeket:

    ```
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile\EnableFirewall         -->        0
    ```

3.  Indítsa újra a szolgáltatást. Ön nem teheti meg, hogy a távoli beállításjegyzék használatával, mert a szolgáltatás konzolján távolítsa el kell használnia.

4.  Nyissa meg a **Services.msc**.

5.  Kattintson a **szolgáltatások (helyi)**.

6.  Válassza ki **Csatlakozás másik számítógéphez**.

7.  Adja meg a **magánhálózati IP-cím (DIP)** a problémát a virtuális gép.

8.  Indítsa újra a helyi tűzfal-házirendet.

9.  Próbálja meg csatlakozni a virtuális gép RDP-Kapcsolaton keresztül a helyi számítógépről.

### <a name="offline-solutions"></a>A kapcsolat nélküli megoldások 

Ha rendelkezik olyan helyzet, amelyben bármilyen módszerrel a virtuális gép nem tudja elérni, egyéni szkriptek futtatására szolgáló bővítmény sikertelen lesz, és el közvetlenül a rendszerlemez mivel OFFLINE módban működik. Ehhez kövesse az alábbi lépéseket:

1.  [A rendszer lemez csatolása egy helyreállítási virtuális Géphez](troubleshoot-recovery-disks-portal-windows.md).

2.  Indítsa el a helyreállítási virtuális Gépet egy távoli asztali kapcsolatot.

3.  Győződjön meg arról, hogy a lemez a Lemezkezelés konzol, Online megjelölésekor. Vegye figyelembe a meghajtóbetűjelet, amely a csatolt rendszerlemez van rendelve.

4.  Végezze el a módosításokat, mielőtt a \windows\system32\config mappájába másolatának létrehozása, abban az esetben egy, a módosítások visszaállítása szükséges.

5.  Az a hibaelhárító virtuális Géphez indítsa el a Beállításszerkesztőt (regedit.exe). 

6.  A hibaelhárítási eljáráshoz azt a struktúrák BROKENSYSTEM és BROKENSOFTWARE vannak csatlakoztatása.

7.  Jelölje ki a HKEY_LOCAL_MACHINE a kulcsot, és válassza ki a fájl > a struktúra betöltése a menüből.

8.  Keresse meg a \windows\system32\config\SYSTEM fájlt a rendszer csatolt lemezen.

9.  Nyisson meg egy rendszergazda jogú PowerShell-példány, és futtassa a következő parancsokat:

    ```cmd
    # Load the hives - If your attached disk is not F, replace the letter assignment here
    reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM
    reg load HKLM\BROKENSOFTWARE f:\windows\system32\config\SOFTWARE 
    # Disable the firewall on the local policy
    $ControlSet = (get-ItemProperty -Path 'HKLM:\BROKENSYSTEM\Select' -name "Current").Current
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # To ensure the firewall is not set thru AD policy, check if the following registry entries exist and if they do, then check if the following entries exist:
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # Unload the hives
    reg unload HKLM\BROKENSYSTEM
    reg unload HKLM\BROKENSOFTWARE
    ```

10. [Válassza le a rendszer lemezt, és hozza létre újból a virtuális gép](troubleshoot-recovery-disks-portal-windows.md).

11. Ellenőrizze, hogy a probléma megoldódott.
