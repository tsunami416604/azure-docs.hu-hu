---
title: Engedélyezheti vagy tilthatja le egy tűzfalszabályt egy vendég operációs rendszer az Azure virtuális Gépen |} A Microsoft Docs
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
ms.openlocfilehash: cb2c548a94a91fe9126f684e382e9626adb93dd6
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/26/2018
ms.locfileid: "52319462"
---
# <a name="enable-or-disable-a-firewall-rule-on-a-azure-vm-guest-os"></a>Engedélyezheti vagy tilthatja le egy tűzfalszabályt egy Azure virtuális gép vendég operációs rendszeren

Ez a cikk egy rá mutató hivatkozást olyan helyzet, amelyben azt gyanítja, hogy a vendég operációs rendszer tűzfala van-e egy virtuális gépen (VM) részleges forgalmat szűrő hibaelhárításhoz. Ez hasznos lehet a következő okok miatt:

*   Ha a módosítás szándékosan a tűzfalhoz, amelyek miatt nem sikerül RDP-kapcsolatai, az egyéni szkriptek futtatására szolgáló bővítmény funkció használatával is megoldhatja a problémát.

*   Letiltja az összes tűzfalprofilnál módja a több üzembiztos, mint az RDP-specifikus tűzfalszabály beállítás a hibaelhárítás.

## <a name="solution"></a>Megoldás

A tűzfalszabályok konfigurálását hogyan attól függ, hogy a virtuális géphez szükséges hozzáférési szintjét. Az alábbi példák az RDP-szabályok használata. Azonban ugyanazokat a módszereket is alkalmazható bármilyen más típusú forgalom mutat, a megfelelő beállításkulcs.

### <a name="online-troubleshooting"></a>Online hibáinak elhárítása 

#### <a name="mitigation-1-custom-script-extension"></a>1. megoldás: Egyéni szkriptek futtatására szolgáló bővítmény

1.  Hozzon létre a parancsfájlt az alábbi sablon használatával.

    *   Egy szabály engedélyezése:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Egy szabály letiltása:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

2.  Ezt a szkriptet az Azure portal használatával töltse fel a [egyéni szkriptek futtatására szolgáló bővítmény](../extensions/custom-script-windows.md) funkció. 

#### <a name="mitigation-2-remote-powershell"></a>2. megoldás: Távoli PowerShell

Ha a virtuális gép online állapotban, és a egy másik virtuális Géphez ugyanazon a virtuális hálózaton elérhetők, akkor is használhatja az alábbi megoldások a többi virtuális gép használatával.

1.  A hibaelhárító virtuális Géphez nyissa meg egy PowerShell-konzolablakot.

2.  Futtassa a következő parancsokat, szükség szerint.

    *   Egy szabály engedélyezése:
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Enable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

    *   Egy szabály letiltása:
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Disable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

#### <a name="mitigation-3-pstools-commands"></a>3. megoldás: PSTools parancsok

Ha a virtuális gép online állapotban, és a egy másik virtuális Géphez ugyanazon a virtuális hálózaton elérhetők, akkor is használhatja az alábbi megoldások a többi virtuális gép használatával.

1.  Töltse le a hibaelhárító virtuális Géphez, a [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools).

2.  Nyisson meg egy CMD-példányt, és eléri a virtuális Gépet a belső IP (DIP) keresztül. 

    * Egy szabály engedélyezése:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Egy szabály letiltása:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

#### <a name="mitigation-4-remote-registry"></a>4. megoldás: A távoli beállításjegyzék

Ha a virtuális gép online állapotban, és a egy másik virtuális Géphez ugyanazon a virtuális hálózaton elérhetők, [távoli beállításjegyzék](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry) a többi virtuális gépen.

1.  A hibaelhárító virtuális Géphez, indítsa el a Beállításszerkesztőt (regedit.exe), és válassza ki **fájl** > **csatlakozás hálózati beállításjegyzék**.

2.  Nyissa meg a *CÉLGÉPEN*\SYSTEM ágban, és adja meg a következő értékeket:

    * Egy szabály engedélyezéséhez nyissa meg a következő beállításazonosítót:
    
        *CÉLSZÁMÍTÓGÉP*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP
    
        Majd módosíthatja **aktív = FALSE** való **aktív = TRUE** a karakterlánc:

        **v2.22 |} A művelet = engedélyezése |} Aktív = TRUE |} Dir = In |} Protokoll = 6 |} Profil = Domain |} Profil privát = |} Profil = nyilvános |} LPort 3389-es = |} App=%SystemRoot%\System32\svchost.exe| SVC = termservice |} Name =@FirewallAPI.dll,-28775 |} Leírás =@FirewallAPI.dll,-28756 |} EmbedCtxt =@FirewallAPI.dll,-28752 |}**
    
    * Egy szabály letiltása, nyissa meg a következő beállításazonosítót:
    
        *CÉLSZÁMÍTÓGÉP*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP

        Majd módosíthatja **aktív = TRUE** való **aktív = FALSE**:
        
        **v2.22 |} A művelet = engedélyezése |} Aktív = FALSE |} Dir = In |} Protokoll = 6 |} Profil = Domain |} Profil privát = |} Profil = nyilvános |} LPort 3389-es = |} App=%SystemRoot%\System32\svchost.exe| SVC = termservice |} Name =@FirewallAPI.dll,-28775 |} Leírás =@FirewallAPI.dll,-28756 |} EmbedCtxt =@FirewallAPI.dll,-28752 |}**

3.  Indítsa újra a virtuális Gépet, a módosítások életbe léptetéséhez.

### <a name="offline-troubleshooting"></a>A kapcsolat nélküli hibáinak elhárítása 

Ha bármilyen módszerrel nem éri el a virtuális gép, egyéni Szkriptbővítmény használatával sikertelen lesz, és el közvetlenül a rendszerlemez mivel OFFLINE módban működik.

Mielőtt végrehajtaná ezeket a lépéseket, pillanatkép készítése a rendszerlemezt az érintett virtuális gép biztonsági mentéséhez. További információkért lásd: [lemez pillanatképének elkészítése](../windows/snapshot-copy-managed-disk.md).

1.  [A rendszer lemez csatolása egy helyreállítási virtuális Géphez](troubleshoot-recovery-disks-portal-windows.md).

2.  Indítsa el a helyreállítási virtuális Gépet egy távoli asztali kapcsolatot.

3.  Győződjön meg arról, hogy a lemez megjelölt **Online** a Lemezkezelés konzol. Vegye figyelembe, hogy a meghajtó betűjele, amely hozzá van rendelve a csatolt rendszerlemezt.

4.  Végezze el a módosításokat, mielőtt a \windows\system32\config mappájába másolatának létrehozása, abban az esetben egy, a módosítások visszaállítása szükséges.

5.  A hibaelhárító virtuális Géphez indítsa el a Beállításszerkesztőt (regedit.exe).

6.  Jelölje ki a **HKEY_LOCAL_MACHINE** kulcsát, és válassza ki **fájl** > **a struktúra betöltése** a menüből.

    ![Regedit](./media/enable-or-disable-firewall-rule-guest-os/load-registry-hive.png)

7.  Keresse meg, és nyissa meg a \windows\system32\config\SYSTEM fájlt. 

    > [!Note]
    > A név megadását kéri. Adja meg **BROKENSYSTEM**, majd **HKEY_LOCAL_MACHINE**. Ekkor megjelenik egy további kulcs nevű **BROKENSYSTEM**. A hibaelhárításhoz, ezek a probléma struktúrák csatlakoztatási azt **BROKENSYSTEM**.

8.  Hajtsa végre a következő módosításokat a BROKENSYSTEM ág:

    1.  Ellenőrizze, hogy mely **ControlSet** a virtuális gép elindul a beállításjegyzék-kulcsot. Látni fogja a HKLM\BROKENSYSTEM\Select\Current kulcs száma.

    2.  Egy szabály engedélyezéséhez nyissa meg a következő beállításazonosítót:
    
        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-felhasználói módú-az-TCP
        
        Majd módosíthatja **aktív = FALSE** való **aktív = True**.
        
        **v2.22 |} A művelet = engedélyezése |} Aktív = TRUE |} Dir = In |} Protokoll = 6 |} Profil = Domain |} Profil privát = |} Profil = nyilvános |} LPort 3389-es = |} App=%SystemRoot%\System32\svchost.exe| SVC = termservice |} Name =@FirewallAPI.dll,-28775 |} Leírás =@FirewallAPI.dll,-28756 |} EmbedCtxt =@FirewallAPI.dll,-28752 |}**

    3.  Egy szabály letiltása, nyissa meg a következő beállításkulcsot:

        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-felhasználói módú-az-TCP

        Majd módosíthatja **aktív = True** való **aktív = FALSE**.
        
        **v2.22 |} A művelet = engedélyezése |} Aktív = FALSE |} Dir = In |} Protokoll = 6 |} Profil = Domain |} Profil privát = |} Profil = nyilvános |} LPort 3389-es = |} App=%SystemRoot%\System32\svchost.exe| SVC = termservice |} Name =@FirewallAPI.dll,-28775 |} Leírás =@FirewallAPI.dll,-28756 |} EmbedCtxt =@FirewallAPI.dll,-28752 |}**

9.  Jelöljön ki **BROKENSYSTEM**, majd válassza ki **fájl** > **struktúra** a menüből.

10. [Válassza le a rendszer lemezt, és hozza létre újból a virtuális gép](troubleshoot-recovery-disks-portal-windows.md).

11. Ellenőrizze, hogy a probléma megoldódott.