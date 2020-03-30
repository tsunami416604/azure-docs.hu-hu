---
title: Tűzfalszabály engedélyezése vagy letiltása vendég operációs rendszeren az Azure virtuális gépében | Microsoft dokumentumok
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
ms.openlocfilehash: 782240c51833fc841af9f4260860db4c03897c03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71086449"
---
# <a name="enable-or-disable-a-firewall-rule-on-an-azure-vm-guest-os"></a>Tűzfalszabály engedélyezése vagy letiltása egy Azure-beli virtuális gép vendég operációs rendszeréhez

Ez a cikk egy hivatkozást tartalmaz egy olyan helyzet elhárításához, amelyben azt gyanítja, hogy a vendég operációs rendszer tűzfala szűri a részleges forgalmat egy virtuális gépen (VM). Ez a következő okok miatt lehet hasznos:

*   Ha szándékosan történt a tűzfal, amely az RDP-kapcsolatok sikertelensítését okozta, az Egyéni parancsfájl-bővítmény szolgáltatás használatával megoldhatja a problémát.

*   Az összes tűzfalprofil letiltása üzembiztosabb hibaelhárítási módszer, mint az RDP-specifikus tűzfalszabály beállítása.

## <a name="solution"></a>Megoldás

A tűzfalszabályok konfigurálásának módja a ttól függ, hogy milyen szintű hozzáférés szükséges a virtuális géphez. A következő példák RDP-szabályokat használnak. Azonban ugyanazokat a módszereket lehet alkalmazni, hogy bármilyen más típusú forgalom mutatva a megfelelő beállításkulcsot.

### <a name="online-troubleshooting"></a>Online hibaelhárítás 

#### <a name="mitigation-1-custom-script-extension"></a>1. kockázatcsökkentés: Egyéni parancsfájl-bővítmény

1.  Hozza létre a parancsfájlt a következő sablon használatával.

    *   Szabály engedélyezése:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Szabály letiltása:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

2.  Töltse fel ezt a parancsfájlt az Azure Portalon az [egyéni parancsfájl-bővítmény](../extensions/custom-script-windows.md) funkció használatával. 

#### <a name="mitigation-2-remote-powershell"></a>2. kockázatcsökkentés: Távoli PowerShell

Ha a virtuális gép online állapotban van, és ugyanazon a virtuális hálózaton egy másik virtuális gépen érhető el, a másik virtuális gép használatával további megoldásokat érhet el.

1.  A hibaelhárítási virtuális gép, nyisson meg egy PowerShell konzolablak.

2.  Szükség szerint futtassa a következő parancsokat.

    *   Szabály engedélyezése:
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Enable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

    *   Szabály letiltása:
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Disable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

#### <a name="mitigation-3-pstools-commands"></a>3. kockázatcsökkentés: A PSTools parancsai

Ha a virtuális gép online állapotban van, és ugyanazon a virtuális hálózaton egy másik virtuális gépen érhető el, a másik virtuális gép használatával további megoldásokat érhet el.

1.  A hibaelhárítási virtuális gép, töltse le [pstools](https://docs.microsoft.com/sysinternals/downloads/pstools).

2.  Nyisson meg egy CMD-példányt, és érje el a virtuális gép belső IP-címén (DIP) keresztül. 

    * Szabály engedélyezése:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Szabály letiltása:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

#### <a name="mitigation-4-remote-registry"></a>4. kockázatcsökkentés: Távoli beállításjegyzék

Ha a virtuális gép online állapotban van, és ugyanazon a virtuális hálózaton egy másik virtuális gépen érhető el, használhatja a [távoli beállításjegyzéket](https://support.microsoft.com/help/314837/how-to-manage-remote-access-to-the-registry) a másik virtuális gépen.

1.  A hibaelhárítási virtuális gépen indítsa el a Rendszerleíróadatbázis-szerkesztőt (regedit.exe), majd válassza a **File** > **Connect Network Registry**lehetőséget.

2.  Nyissa meg a *TARGET MACHINE*\SYSTEM ágat, majd adja meg a következő értékeket:

    * Szabály engedélyezéséhez nyissa meg a következő rendszerleíró értéket:
    
        *CÉLGÉP*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP
    
        Ezután módosítsa **az Active=FALSE** értéket **Active=TRUE** értékre a karakterláncban:

        **2.22-es névre biski| Action=Allow| Active=TRUE| Dir=In| Protokoll=6| Profile=Tartomány| Profile=Magán| Profile=Nyilvános| LPort=3389| App=%SystemRoot%\system32\svchost.exe| Svc=termservice| Name=\@FirewallAPI.dll,-28775| Desc=\@FirewallAPI.dll,-28756| EmbedCtxt=\@FirewallAPI.dll,-28752|**
    
    * Szabály letiltásához nyissa meg a következő rendszerleíró értéket:
    
        *CÉLGÉP*\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP

        Ezután módosítsa **az Aktív =TRUE** értéket **Active=FALSE értékre:**
        
        **2.22-es névre biski| Action=Allow| Active=HAMIS| Dir=In| Protokoll=6| Profile=Tartomány| Profile=Magán| Profile=Nyilvános| LPort=3389| App=%SystemRoot%\system32\svchost.exe| Svc=termservice| Name=\@FirewallAPI.dll,-28775| Desc=\@FirewallAPI.dll,-28756| EmbedCtxt=\@FirewallAPI.dll,-28752|**

3.  A módosítások alkalmazásához indítsa újra a virtuális gép.

### <a name="offline-troubleshooting"></a>Offline hibaelhárítás 

Ha nem tudja elérni a virtuális gép bármilyen módszerrel, az egyéni parancsfájl-bővítmény használata sikertelen lesz, és offline módban kell dolgoznia közvetlenül a rendszerlemezen keresztül.

Az alábbi lépések végrehajtása előtt készítsen pillanatképet az érintett virtuális gép rendszerlemezéről biztonsági másolatként. További információt a [Lemez pillanatképe](../windows/snapshot-copy-managed-disk.md)című témakörben talál.

1.  [Csatlakoztassa a rendszerlemezt egy helyreállítási virtuális géphez.](troubleshoot-recovery-disks-portal-windows.md)

2.  Távoli asztali kapcsolat indítása a helyreállítási virtuális géppel.

3.  Győződjön meg arról, hogy a lemez **online** ként van megjelölve a Lemezkezelés konzolon. Ne feledje, hogy a csatlakoztatott rendszerlemezhez rendelt meghajtóbetűjel.

4.  Mielőtt bármilyen módosítást eszközölt volna, hozzon létre egy másolatot a \windows\system32\config mappáról, ha a módosítások visszaállítása szükséges.

5.  A hibaelhárítási virtuális gépen indítsa el a Rendszerleíróadatbázis-szerkesztőt (regedit.exe).

6.  Jelölje ki a **HKEY_LOCAL_MACHINE** kulcsot, majd válassza a menü**Fájlbetöltési** **File** > struktúra parancsát.

    ![Összefagy](./media/enable-or-disable-firewall-rule-guest-os/load-registry-hive.png)

7.  Keresse meg, majd nyissa meg a \windows\system32\config\SYSTEM fájlt. 

    > [!Note]
    > A program kéri a név megnevezését. Írja be **a BROKENSYSTEM**parancsot, majd bontsa ki **HKEY_LOCAL_MACHINE.** Ekkor megjelenik egy további kulcs, amelynek neve **BROKENSYSTEM**. Ehhez a hibaelhárításhoz ezeket a problémakaptárakat **BROKENSYSTEM**néven szereljük fel.

8.  Hajtsa végre a következő módosításokat a BROKENSYSTEM ágon:

    1.  Ellenőrizze, hogy a virtuális gép melyik **ControlSet** beállításkulcsból indul ki. A kulcsszámát a HKLM\BROKENSYSTEM\Select\Current mappában láthatja.

    2.  Szabály engedélyezéséhez nyissa meg a következő rendszerleíró értéket:
    
        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In TCP
        
        Ezután módosítsa **az Active=FALSE** értéket **Active=True**értékre.
        
        **2.22-es névre biski| Action=Allow| Active=TRUE| Dir=In| Protokoll=6| Profile=Tartomány| Profile=Magán| Profile=Nyilvános| LPort=3389| App=%SystemRoot%\system32\svchost.exe| Svc=termservice| Name=\@FirewallAPI.dll,-28775| Desc=\@FirewallAPI.dll,-28756| EmbedCtxt=\@FirewallAPI.dll,-28752|**

    3.  Szabály letiltásához nyissa meg a következő beállításkulcsot:

        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In TCP

        Ezután módosítsa **az Active=True** értéket **Active=FALSE értékre.**
        
        **2.22-es névre biski| Action=Allow| Active=HAMIS| Dir=In| Protokoll=6| Profile=Tartomány| Profile=Magán| Profile=Nyilvános| LPort=3389| App=%SystemRoot%\system32\svchost.exe| Svc=termservice| Name=\@FirewallAPI.dll,-28775| Desc=\@FirewallAPI.dll,-28756| EmbedCtxt=\@FirewallAPI.dll,-28752|**

9.  Jelölje ki **a BROKENSYSTEM**elemet, majd válassza a menü **Fájleltávolítása** > **hive** parancsát.

10. [Válassza le a rendszerlemezt, és hozza létre újra a virtuális gép](troubleshoot-recovery-disks-portal-windows.md).

11. Ellenőrizze, hogy megoldódott-e a probléma.
