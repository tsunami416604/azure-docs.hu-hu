---
title: A vendég operációsrendszer-tűzfal letiltása az Azure VM-ben | Microsoft dokumentumok
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: dbb9b0f865c7ec5d9d29e2310ae41abbec287bd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79464962"
---
# <a name="disable-the-guest-os-firewall-in-azure-vm"></a>A vendég operációs rendszer tűzfalának letiltása az Azure-beli virtuális gépen

Ez a cikk olyan helyzetekre vonatkozó hivatkozásokat tartalmaz, amelyekben azt gyanítja, hogy a vendég operációs rendszer tűzfala részleges vagy teljes forgalmat szűr egy virtuális gépre (VM). Ez akkor fordulhat elő, ha a tűzfalon szándékosan olyan módosítások történtek, amelyek miatt az RDP-kapcsolatok meghibásodtak.

## <a name="solution"></a>Megoldás

A cikkben ismertetett folyamat kerülő megoldásként szolgál, így a valódi probléma megoldására összpontosíthat, így hogyan állíthatja be helyesen a tűzfalszabályokat. A Microsoft ajánlott eljárása, ha a Windows tűzfal összetevő engedélyezve van. A tűzfalszabályok konfigurálásának módja a virtuális géphez való hozzáférés szükséges szintjétől függ.

### <a name="online-solutions"></a>Online megoldások 

Ha a virtuális gép online állapotban van, és ugyanazon a virtuális hálózaton egy másik virtuális gépen érhető el, ezeket a megoldásokat a másik virtuális gép használatával teheti meg.

#### <a name="mitigation-1-custom-script-extension-or-run-command-feature"></a>1. kockázatcsökkentés: Egyéni parancsfájl-bővítmény vagy parancs futtatása szolgáltatás

Ha egy működő Azure-ügynök, használhatja [az egyéni parancsfájl-bővítmény](../extensions/custom-script-windows.md) vagy a [Parancsok futtatása](../windows/run-command.md) szolgáltatás (Erőforrás-kezelő virtuális gépek csak) távolról futtathatja a következő parancsfájlokat.

> [!Note]
> * Ha a tűzfal helyileg van beállítva, futtassa a következő parancsfájlt:
>   ```
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\Standardprofile' -name "EnableFirewall" -Value 0 
>   Restart-Service -Name mpssvc
>   ```
> * Ha a tűzfal Active Directory-házirenden keresztül van beállítva, a következő parancsfájl futtatásával ideiglenes en keresztül. 
>   ```
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile' name "EnableFirewall" -Value 0
>   Restart-Service -Name mpssvc
>   ```
>   Azonban, amint a házirend újra alkalmazza, akkor ki kell rúgni a távoli munkamenet. A probléma végleges javítása a számítógépen alkalmazott házirend módosítása.

#### <a name="mitigation-2-remote-powershell"></a>2. kockázatcsökkentés: Távoli PowerShell

1.  Csatlakozzon egy virtuális géphez, amely ugyanazon a virtuális hálózaton található, mint a virtuális gép, amely nem érhető el RDP-kapcsolat használatával.

2.  Nyisson meg egy PowerShell-konzolablakot.

3.  Futtassa az alábbi parancsot:

    ```powershell
    Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
    netsh advfirewall set allprofiles state off
    Restart-Service -Name mpssvc 
    exit
    ```

> [!Note]
> Ha a tűzfal csoportházirend-objektumon keresztül van beállítva, előfordulhat, hogy ez a módszer nem működik, mert ez a parancs csak a helyi rendszerleíró bejegyzéseket módosítja. Ha egy házirend érvényben van, felülírja ezt a módosítást. 

#### <a name="mitigation-3-pstools-commands"></a>3. kockázatcsökkentés: A PSTools parancsai

1.  A hibaelhárítási virtuális gép, töltse le [pstools](https://docs.microsoft.com/sysinternals/downloads/pstools).

2.  Nyisson meg egy CMD-példányt, majd a DIP-en keresztül érje el a virtuális gép.

3.  Futtassa az alábbi parancsot:

    ```cmd
    psexec \\<DIP> -u <username> cmd
    netsh advfirewall set allprofiles state off
    psservice restart mpssvc
    ```

#### <a name="mitigation-4-remote-registry"></a>4. kockázatcsökkentés: Távoli beállításjegyzék 

A [Távoli beállításjegyzék](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry)használatához kövesse az alábbi lépéseket.

1.  A hibaelhárítási virtuális gépen indítsa el a rendszerleíró adatbázis szerkesztőjét, majd nyissa meg a File Connect Network Registry > **(Fájlcsatlakoztatási hálózat beállításjegyzékét).** **File**

2.  Nyissa meg a *TARGET MACHINE*\SYSTEM ágat, és adja meg a következő értékeket:

    ```
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile\EnableFirewall         -->        0
    ```

3.  Indítsa újra a szolgáltatást. Mivel ezt nem tudja megtenni a távoli beállításjegyzék használatával, a Távoli szolgáltatáskonzolt kell használnia.

4.  Nyissa meg a **Services.msc egy példányát.**

5.  Kattintson **a Szolgáltatások (Helyi) elemre.**

6.  Válassza **a Csatlakozás másik számítógépre**lehetőséget.

7.  Adja meg a probléma virtuális gépének **privát IP-címét (DIP).**

8.  Indítsa újra a helyi tűzfalházirendet.

9.  Próbáljon meg újra csatlakozni a virtuális géphez a helyi számítógépről.

### <a name="offline-solutions"></a>Offline megoldások 

Ha olyan helyzetben van, amelyben nem tudja elérni a virtuális gép semmilyen módszerrel, egyéni parancsfájl-bővítmény sikertelen lesz, és meg kell dolgozni offline módban dolgozik közvetlenül a rendszerlemezen keresztül. Ehhez kövesse az alábbi lépéseket:

1.  [Csatlakoztassa a rendszerlemezt egy helyreállítási virtuális géphez.](troubleshoot-recovery-disks-portal-windows.md)

2.  Távoli asztali kapcsolat indítása a helyreállítási virtuális géppel.

3.  Győződjön meg arról, hogy a lemez online ként van megjelölve a Lemezkezelés konzolon. Jegyezze fel a csatlakoztatott rendszerlemezhez rendelt meghajtóbetűjelet.

4.  Mielőtt bármilyen módosítást eszközölt volna, hozzon létre egy másolatot a \windows\system32\config mappáról, ha a módosítások visszaállítása szükséges.

5.  A hibaelhárítási virtuális gépen indítsa el a rendszerleíró adatbázis szerkesztőjét (regedit.exe). 

6.  Ehhez a hibaelhárítási eljáráshoz a kaptárakat BROKENSYSTEM és BROKENSOFTWARE néven szereljük fel.

7.  Jelölje ki a HKEY_LOCAL_MACHINE kulcsot, majd válassza a menü Fájl > Hive betöltése parancsát.

8.  Keresse meg a \windows\system32\config\SYSTEM fájlt a csatolt rendszerlemezen.

9.  Nyisson meg egy emelt szintű PowerShell-példányt, majd futtassa a következő parancsokat:

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
    # To ensure the firewall is not set through AD policy, check if the following registry entries exist and if they do, then check if the following entries exist:
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

10. [Válassza le a rendszerlemezt, és hozza létre újra a virtuális gép](troubleshoot-recovery-disks-portal-windows.md).

11. Ellenőrizze, hogy megoldódott-e a probléma.
