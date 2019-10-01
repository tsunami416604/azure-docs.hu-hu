---
title: Windows rendszerű virtuális merevlemez előkészítése az Azure-ba való feltöltésre | Microsoft Docs
description: Útmutató Windows VHD-k vagy VHDX előkészítéséhez az Azure-ba való feltöltéshez
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2019
ms.author: genli
ms.openlocfilehash: cbae4455ae4cfcc0397b8b50b7f86843f7f82a59
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695384"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Windows VHD vagy VHDX előkészítése az Azure-ba való feltöltésre

Mielőtt feltöltötte a Windows rendszerű virtuális gépet (VM) a helyszínről az Azure-ba, elő kell készítenie a virtuális merevlemezt (VHD vagy VHDX). Az Azure támogatja az 1. és a 2. generációs virtuális gépeket, amelyek VHD-fájlformátumban vannak, és amelyek rögzített méretű lemezzel rendelkeznek. A VHD számára engedélyezett maximális méret 1 023 GB. 

Egy 1. generációs virtuális gépen a VHDX fájlrendszert virtuális merevlemezre konvertálhatja. A dinamikusan bővülő lemezeket rögzített méretű lemezre is konvertálhatja. A virtuális gép generációja azonban nem módosítható. További információ: hozzon [létre egy 1. vagy 2. generációs virtuális gépet a Hyper-V-ben?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) és [Azure-támogatást a 2. generációs virtuális gépekhez (előzetes verzió)](generation-2.md).

Az Azure-beli virtuális gépek támogatási szabályzatával kapcsolatos információkért lásd: [Microsoft Server szoftveres támogatás Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)-beli virtuális gépekhez.

> [!NOTE]
> A cikkben szereplő utasítások a következőkre vonatkoznak:
>1. A Windows Server 2008 R2 és újabb Windows Server operációs rendszerek 64 bites verziója. Az 32 bites operációs rendszerek Azure-ban való futtatásával kapcsolatos információkért lásd: [a 32 bites operációs rendszerek támogatása az Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)-beli virtuális gépeken.
>2. Ha a számítási feladat áttelepítéséhez vész-helyreállítási eszközt használ, például Azure Site Recovery vagy Azure Migrate, ezt a folyamatot továbbra is végre kell hajtani, és követnie kell a vendég operációs rendszert, hogy előkészítse a rendszerképet az áttelepítés előtt.

## <a name="convert-the-virtual-disk-to-a-fixed-size-and-to-vhd"></a>A virtuális lemez átalakítása rögzített méretűre és VHD-re

Ha át kell alakítania a virtuális lemezt az Azure-hoz szükséges formátumra, használja a szakasz egyik módszerét:

1. A virtuális gép biztonsági mentését a virtuális lemez átalakítási folyamatának futtatása előtt végezheti el.

1. Győződjön meg arról, hogy a Windows VHD megfelelően működik a helyi kiszolgálón. Az Azure-ba való konvertálás vagy az Azure-ba való feltöltés előtt javítsa ki a virtuális gépen található hibákat.

1. A VHD méretével kapcsolatban:

   1. Az Azure-ban az összes virtuális merevlemeznek 1 MB-ra igazított virtuális mérettel kell rendelkeznie. Nyers lemezről VHD-re való konvertáláskor győződjön meg arról, hogy a nyers lemez mérete 1 MB-nál több, az átalakítás előtt. A megabájt töredékei hibát okoznak a feltöltött virtuális merevlemezről származó lemezképek létrehozásakor.

   2. Az operációs rendszer VHD-je számára engedélyezett maximális méret a 2TB.


A lemez konvertálása után hozzon létre egy virtuális gépet, amely a lemezt használja. Indítsa el a virtuális gépet, és jelentkezzen be a feltöltésre való felkészülés befejezéséhez.

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>A lemez átalakítása a Hyper-V kezelőjével 
1. Nyissa meg a Hyper-V kezelőjét, és a bal oldalon válassza ki a helyi számítógépet. A számítógép lista fölötti menüben válassza a **művelet** > **lemez szerkesztése**lehetőséget.
2. A **virtuális merevlemez keresése** lapon válassza ki a virtuális lemezt.
3. A **művelet kiválasztása** lapon válassza a **Konvertálás** > **tovább**lehetőséget.
4. Ha a VHDX-ből kell konvertálnia, válassza a **VHD** > **tovább**lehetőséget.
5. Ha dinamikusan bővülő lemezről kell váltania, válassza a **rögzített méret** > **tovább**lehetőséget.
6. Keresse meg és válassza ki azt az elérési utat, amelybe menteni szeretné az új VHD-fájlt.
7. Válassza a **Finish** (Befejezés) elemet.

> [!NOTE]
> A cikkben szereplő parancsok futtatásához emelt szintű PowerShell-munkamenetet használjon.

### <a name="use-powershell-to-convert-the-disk"></a>A lemez konvertálása a PowerShell használatával 
A virtuális lemezeket a Windows PowerShell [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) parancsának használatával alakíthatja át. A PowerShell indításakor válassza **a Futtatás rendszergazdaként** lehetőséget. 

A következő példa parancs átalakítja a lemezt a VHDX-ről a VHD-re. A parancs a dinamikusan bővülő lemezről a rögzített méretű lemezre konvertálja a lemezt is.

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

Ebben a parancsban cserélje le a értéket `-Path` az értékre az átalakítani kívánt virtuális merevlemez elérési útjával. Cserélje le a értéket `-DestinationPath` a értékre a konvertált lemez új elérési útjával és nevével.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Konvertálás VMware VMDK lemez formátumból
Ha a Windows rendszerű virtuálisgép-lemezképpel [VMDK fájlformátumban](https://en.wikipedia.org/wiki/VMDK)van, a [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497) használatával alakítsa át VHD formátumra. További információ: [VMware VMDK konvertálása Hyper-V virtuális merevlemezre](https://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx).

## <a name="set-windows-configurations-for-azure"></a>Windows-konfigurációk beállítása az Azure-hoz

Az Azure-ba feltölteni kívánt virtuális gépen futtassa a következő parancsokat egy rendszergazda [jogú parancssorból](https://technet.microsoft.com/library/cc947813.aspx):

1. Távolítsa el az útválasztási táblázat statikus állandó útvonalait:
   
   * Az útválasztási táblázat megtekintéséhez futtassa `route print` a parancsot a parancssorban.
   * Tekintse `Persistence Routes` át a szakaszt. Állandó útvonal esetén a `route delete` parancs használatával távolítsa el azt.
2. Távolítsa el a WinHTTP proxyt:
   
    ```PowerShell
    netsh winhttp reset proxy
    ```

    Ha a virtuális gépnek egy adott proxyval kell dolgoznia, vegyen fel egy proxy-kivételt az[Azure](https://blogs.msdn.microsoft.com/mast/2015/05/18/what-is-the-ip-address-168-63-129-16/
)IP-címére (168.63.129.16), hogy a virtuális gép csatlakozni tud az Azure-hoz:
    ```
    $proxyAddress="<your proxy server>"
    $proxyBypassList="<your list of bypasses>;168.63.129.16"

    netsh winhttp set proxy $proxyAddress $proxyBypassList
    ```

3. A lemez SAN-házirendjének [`Onlineall`](https://technet.microsoft.com/library/gg252636.aspx)beállítása a következőre:
   
    ```PowerShell
    diskpart 
    ```
    A parancssori ablak megnyitásához írja be a következő parancsokat:

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Állítsa be a Windows egyezményes világidő (UTC) időpontját. Állítsa be a Windows időszolgáltatás (`w32time` `Automatic`) indítási típusát is a következőre:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -name "RealTimeIsUniversal" -Value 1 -Type DWord -force

    Set-Service -Name w32time -StartupType Automatic
    ```
5. Állítsa be az energiagazdálkodási profilt nagy teljesítményre:

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```
6. Győződjön meg arról, hogy `TEMP` a `TMP` környezeti változók és az alapértelmezett értékek vannak beállítva:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TEMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force
    ```

## <a name="check-the-windows-services"></a>A Windows-szolgáltatások keresése
Győződjön meg arról, hogy a következő Windows-szolgáltatások mindegyike a Windows alapértelmezett értékeire van beállítva. Ezek a szolgáltatások minimálisan szükségesek a virtuális gépekhez való kapcsolódás biztosításához. Az indítási beállítások alaphelyzetbe állításához futtassa a következő parancsokat:
   
```PowerShell
Set-Service -Name bfe -StartupType Automatic
Set-Service -Name dhcp -StartupType Automatic
Set-Service -Name dnscache -StartupType Automatic
Set-Service -Name IKEEXT -StartupType Automatic
Set-Service -Name iphlpsvc -StartupType Automatic
Set-Service -Name netlogon -StartupType Manual
Set-Service -Name netman -StartupType Manual
Set-Service -Name nsi -StartupType Automatic
Set-Service -Name termService -StartupType Manual
Set-Service -Name MpsSvc -StartupType Automatic
Set-Service -Name RemoteRegistry -StartupType Automatic
```

## <a name="update-remote-desktop-registry-settings"></a>Távoli asztal beállításjegyzék-beállításainak frissítése
Győződjön meg arról, hogy a következő beállítások megfelelően vannak konfigurálva a táveléréshez:

>[!NOTE] 
>Előfordulhat, hogy a futtatásakor `Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -name <object name> -value <value>`hibaüzenet jelenik meg. Nyugodtan figyelmen kívül hagyhatja ezt az üzenetet. Ez azt jelenti, hogy a tartomány nem küldi el ezt a konfigurációt egy Csoportházirend objektumon keresztül.

1. A RDP protokoll (RDP) engedélyezve van:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDenyTSConnections" -Value 0 -Type DWord -force
    ```
   
2. Az RDP-port helyesen van beállítva. Az alapértelmezett port a 3389:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "PortNumber" -Value 3389 -Type DWord -force
    ```
    Amikor üzembe helyez egy virtuális gépet, az alapértelmezett szabályok a 3389-es porton jönnek létre. Ha módosítani szeretné a portszámot, tegye azt a virtuális gép üzembe helyezése után az Azure-ban.

3. A figyelő minden hálózati adapteren figyeli a következőket:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "LanAdapter" -Value 0 -Type DWord -force
   ```
4. Adja meg az RDP-kapcsolatok hálózati szintű hitelesítésének (NLA) módját:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "UserAuthentication" -Value 1 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SecurityLayer" -Value 1 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "fAllowSecProtocolNegotiation" -Value 1 -Type DWord -force
     ```

5. A Keep-Alive érték beállítása:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveEnable" -Value 1  -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveInterval" -Value 1  -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "KeepAliveTimeout" -Value 1 -Type DWord -force
    ```
6. Újra
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDisableAutoReconnect" -Value 0 -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fInheritReconnectSame" -Value 1 -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fReconnectSame" -Value 0 -Type DWord -force
    ```
7. Az egyidejű kapcsolatok számának korlátozása:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "MaxInstanceCount" -Value 4294967295 -Type DWord -force
    ```
8. Távolítsa el az RDP-figyelőhöz kötött összes önaláírt tanúsítványt:
    
    ```PowerShell
    Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SSLCertificateSHA1Hash" -force
    ```
    Ez a kód gondoskodik arról, hogy a virtuális gép telepítésekor a rendszer az elején is csatlakozhasson. Ha később is át kell tekintenie ezt, ezt a virtuális gép Azure-ban való üzembe helyezése után teheti meg.

9. Ha a virtuális gép egy tartomány részévé válik, ellenőrizze a következő házirendeket, és győződjön meg arról, hogy a korábbi beállítások nem állnak-e újra. 
    
    | Cél                                     | Szabályzat                                                                                                                                                       | Value                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | Az RDP engedélyezve van                           | Computer Configuration\Policies\Windows Settings\Administrative Templates\Components\Remote Desktop Services\Remote Desktop Session Host\Connections         | Távolról való kapcsolódás engedélyezése a felhasználók számára a Távoli asztal használatával                                  |
    | NLA csoportházirend                         | Settings\Administrative Templates\Components\Remote asztali Windows-összetevők asztali munkamenet biztonság csomópontban található                                                    | A távelérés felhasználói hitelesítésének megkövetelése a NLA használatával |
    | Életben tartási beállítások                      | Computer Configuration\Policies\Windows Settings\Administrative Templates\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections | Életben tartási kapcsolatok intervallumának konfigurálása                                                 |
    | Beállítások újracsatolása                       | Computer Configuration\Policies\Windows Settings\Administrative Templates\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections | Automatikus újrakapcsolás                                                                   |
    | Korlátozott számú kapcsolattípus | Computer Configuration\Policies\Windows Settings\Administrative Templates\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections | Kapcsolatok számának korlátozása                                                              |

## <a name="configure-windows-firewall-rules"></a>A Windows tűzfal szabályainak konfigurálása
1. A Windows tűzfal bekapcsolása a három profilra (tartomány, standard és nyilvános):

   ```PowerShell
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
   ```

2. Futtassa a következő parancsot a PowerShellben a WinRM engedélyezéséhez a három tűzfal-profil (tartomány, privát és nyilvános) használatával, és engedélyezze a távoli PowerShell-szolgáltatást:
   
   ```PowerShell
    Enable-PSRemoting -force

    Set-NetFirewallRule -DisplayName "Windows Remote Management (HTTP-In)" -Enabled True
   ```
3. Az RDP-forgalom engedélyezéséhez engedélyezze a következő tűzfalszabályok használatát:

   ```PowerShell
    Set-NetFirewallRule -DisplayGroup "Remote Desktop" -Enabled True
   ```   
4. Engedélyezze a fájl-és nyomtatómegosztás szabályát, hogy a virtuális gép válaszolni tudjon a ping parancsra a virtuális hálózaton belül:

   ```PowerShell
   Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Enabled True
   ``` 
5. Ha a virtuális gép egy tartomány részévé válik, ellenőrizze az alábbi Azure AD-házirendeket, és győződjön meg arról, hogy a korábbi beállítások nem lettek visszaállítva. 

    | Cél                                 | Szabályzat                                                                                                                                                  | Value                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | A Windows tűzfal profiljainak engedélyezése | Számítógép konfigurációja \ Settings\Administrative hálózat Connection\Windows Firewall\Domain Profile\Windows tűzfal   | Az összes hálózati kapcsolat biztosítása         |
    | RDP engedélyezése                           | Számítógép konfigurációja \ Settings\Administrative hálózat Connection\Windows Firewall\Domain Profile\Windows tűzfal   | Bejövő Távoli asztal kivételek engedélyezése |
    |                                      | Számítógép konfigurációja \ Settings\Administrative hálózat Connection\Windows Firewall\Standard Profile\Windows tűzfal | Bejövő Távoli asztal kivételek engedélyezése |
    | ICMP engedélyezése – v4                       | Számítógép konfigurációja \ Settings\Administrative hálózat Connection\Windows Firewall\Domain Profile\Windows tűzfal   | ICMP-kivételek engedélyezése                   |
    |                                      | Számítógép konfigurációja \ Settings\Administrative hálózat Connection\Windows Firewall\Standard Profile\Windows tűzfal | ICMP-kivételek engedélyezése                   |

## <a name="verify-the-vm"></a>A virtuális gép ellenőrzése 

Győződjön meg arról, hogy a virtuális gép kifogástalan, biztonságos, és az RDP elérhető: 

1. Győződjön meg arról, hogy a lemez kifogástalan és konzisztens állapotban van, ellenőrizze a következő virtuális gép újraindításának lemezét:

    ```PowerShell
    Chkdsk /f
    ```
    Győződjön meg arról, hogy a jelentés tiszta és kifogástalan állapotú lemezt jelenít meg.

2. Adja meg a rendszerindítási konfigurációs adatok (BCD) beállításait. 

    > [!NOTE]
    > Futtassa a parancsot egy emelt szintű PowerShell-ablak használatával.
   
   ```powershell
    bcdedit /set "{bootmgr}" integrityservices enable
    bcdedit /set "{default}" device partition=C:
    bcdedit /set "{default}" integrityservices enable
    bcdedit /set "{default}" recoveryenabled Off
    bcdedit /set "{default}" osdevice partition=C:
    bcdedit /set "{default}" bootstatuspolicy IgnoreAllFailures

    #Enable Serial Console Feature
    bcdedit /set "{bootmgr}" displaybootmenu yes
    bcdedit /set "{bootmgr}" timeout 5
    bcdedit /set "{bootmgr}" bootems yes
    bcdedit /ems "{current}" ON
    bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```
3. A memóriakép naplója hasznos lehet a Windows összeomlási problémáinak elhárításához. Memóriakép-gyűjtemény engedélyezése:

    ```powershell
    # Set up the guest OS to collect a kernel dump on an OS crash event
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name CrashDumpEnabled -Type DWord -force -Value 2
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name DumpFile -Type ExpandString -force -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name NMICrashDump -Type DWord -force -Value 1

    # Set up the guest OS to collect user mode dumps on a service crash event
    $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
    New-ItemProperty -Path $key -name DumpFolder -Type ExpandString -force -Value "c:\CrashDumps"
    New-ItemProperty -Path $key -name CrashCount -Type DWord -force -Value 10
    New-ItemProperty -Path $key -name DumpType -Type DWord -force -Value 2
    Set-Service -Name WerSvc -StartupType Manual
    ```
4. Győződjön meg arról, hogy a Windows Management Instrumentation (WMI) tárház konzisztens:

    ```PowerShell
    winmgmt /verifyrepository
    ```
    Ha a tárház sérült, tekintse [meg a WMI: Adattár sérülése vagy](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not)nem.

5. Győződjön meg arról, hogy egyetlen másik alkalmazás sem használja az 3389-es portot. Ez a port az Azure-beli RDP szolgáltatáshoz használatos. A virtuális gépen használt portok megtekintéséhez futtassa `netstat -anob`a következőt:

    ```PowerShell
    netstat -anob
    ```

6. Tartományvezérlőhöz tartozó Windows virtuális merevlemez feltöltése:

   * A lemez előkészítéséhez kövesse [ezeket a további lépéseket](https://support.microsoft.com/kb/2904015) .

   * Győződjön meg arról, hogy ismeri a Címtárszolgáltatások helyreállító módjának jelszavát, ha a virtuális gépet a Címtárszolgáltatások helyreállító módjában kell elindítania egy bizonyos ponton. További információt a [Címtárszolgáltatások helyreállító módjának beállítása](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx)című témakörben talál.

7. Győződjön meg arról, hogy ismeri a beépített rendszergazdai fiókot és jelszót. Előfordulhat, hogy alaphelyzetbe kívánja állítani a helyi rendszergazda aktuális jelszavát, és a fiók használatával bejelentkezhet a Windowsba az RDP-kapcsolaton keresztül. Ezt a hozzáférési engedélyt a "bejelentkezés engedélyezése Távoli asztali szolgáltatások" Csoportházirend objektum vezérli. Tekintse meg ezt az objektumot a Helyicsoportházirend-szerkesztő itt:

    Számítógép konfigurációja \ beállítások \ helyi házirend \ felhasználói Rights-hozzárendelés

8. Ellenőrizze az alábbi Azure AD-szabályzatokat, és győződjön meg arról, hogy nem blokkolja az RDP-hozzáférést RDP-n keresztül vagy a hálózatról:

    - Számítógép konfigurációja \ beállítások \ helyi házirend \ felhasználói Rights kiosztása hozzáférés a számítógéphez a hálózatról

    - Számítógép konfigurációja \ beállítások \ helyi házirend \ felhasználói Rights kiosztása bejelentkezés Távoli asztali szolgáltatások


9. Ellenőrizze az alábbi Azure AD-szabályzatot, és győződjön meg arról, hogy nem távolítja el a szükséges hozzáférési fiókokat:

   - Számítógép konfigurációja \ beállítások \ helyi házirend \ felhasználói Rights Assignment\Access ez a számítógép a hálózatról

   A szabályzatnak a következő csoportokat kell kilistáznia:

   - A rendszergazdák

   - Biztonságimásolat-felelősök

   - Mindenki

   - Felhasználók

10. Indítsa újra a virtuális gépet, és győződjön meg arról, hogy a Windows továbbra is kifogástalan állapotú, és az RDP-kapcsolaton keresztül érhető el. Ezen a ponton előfordulhat, hogy létre kell hoznia egy virtuális gépet a helyi Hyper-V-ben, hogy a virtuális gép teljesen elindul. Ezután ellenőrizze, hogy a virtuális gép RDP-kapcsolaton keresztül is elérhető-e.

11. Távolítsa el a további Transport Driver Interface (TDI) szűrőket. Távolítsa el például a TCP-csomagokat vagy a további tűzfalakat elemző szoftvereket. Ha később is át kell tekintenie ezt, ezt a virtuális gép Azure-ban való üzembe helyezése után teheti meg.

12. Távolítson el minden olyan külső gyártótól származó szoftvert vagy illesztőprogramot, amely fizikai összetevőkhöz vagy más virtualizációs technológiához kapcsolódik.

### <a name="install-windows-updates"></a>Windows-frissítések telepítése
Ideális esetben a gépet a *javítási szinten*kell frissíteni. Ha ez nem lehetséges, ellenőrizze, hogy telepítve vannak-e a következő frissítések:

| Összetevő               | Binary         | Windows 7 SP1, Windows Server 2008 R2 SP1 | Windows 8, Windows Server 2012               | Windows 8,1, Windows Server 2012 R2 | Windows 10 v1607, Windows Server 2016 v1607 | Windows 10 v1703    | Windows 10 v1709, Windows Server 2016 v1709 | Windows 10 v1803, Windows Server 2016 v1803 |
|-------------------------|----------------|-------------------------------------------|---------------------------------------------|------------------------------------|---------------------------------------------------------|----------------------------|-------------------------------------------------|-------------------------------------------------|
| Storage                 | Disk. sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061         | -                                                       | -                          | -                                               | -                                               |
|                         | Storport. sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.332             | -                                               | -                                               |
|                         | NTFS. sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Iologmsg.dll   | 6.1.7601.23403 - KB3125574                | 6.2.9200.16384 - KB2995387                  | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | CLASSPNP. sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614         | 10.0.14393.953 - KB4022715                              | -                          | -                                               | -                                               |
|                         | VolSnap. sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384         | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | partmgr.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 - KB3000850         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | volmgr.sys     |                                           |                                             |                                    |                                                         | 10.0.15063.0               | -                                               | -                                               |
|                         | Volmgrx.sys    | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | Msiscsi. sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726         | 10.0.14393.1066 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Msdsm.sys      | 6.1.7601.23403 - KB3125574                | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726         | -                                                       | -                          | -                                               | -                                               |
|                         | MPIO. sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726         | 10.0.14393.1198 - KB4022715                             | -                          | -                                               | -                                               |
|                         | vmstorfl. sys   | 6.3.9600.18907 - KB4072650                | 6.3.9600.18080 - KB3063109                  | 6.3.9600.18907 - KB4072650         | 10.0.14393.2007 - KB4345418                             | 10.0.15063.850 - KB4345419 | 10.0.16299.371 - KB4345420                      | -                                               |
|                         | Fveapi.dll     | 6.1.7601.23311 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614         | 10.0.14393.576 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Fveapibase.dll | 6.1.7601.23403 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614         | 10.0.14393.206 - KB4022715                              | -                          | -                                               | -                                               |
| Network (Hálózat)                 | netvsc.sys     | -                                         | -                                           | -                                  | 10.0.14393.1198 - KB4022715                             | 10.0.15063.250 - KB4020001 | -                                               | -                                               |
|                         | mrxsmb10. sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726         | 10.0.14393.479 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb20. sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | MRxSmb. sys     | 6.1.7601.23816 - KB4022722                | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | tcpip. sys      | 6.1.7601.23761 - KB4022722                | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | http. sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726         | 10.0.14393.251 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | vmswitch.sys   | 6.1.7601.23727 - KB4022719                | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.138             | -                                               | -                                               |
| Core                    | ntoskrnl.exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.483             | -                                               | -                                               |
| Távoli asztali szolgáltatások | rdpcorets. dll  | 6.2.9200.21506 - KB4022719                | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.0               | -                                               | -                                               |
|                         | termsrv. dll    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 - KB3000850         | 10.0.14393.0 - KB4022715                                | 10.0.15063.0               | -                                               | -                                               |
|                         | TermDD. sys     | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Win32k. sys     | 6.1.7601.23807 - KB4022719                | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726         | 10.0.14393.594 - KB4022715                              | -                          | -                                               | -                                               |
|                         | rdpdd. dll      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Rdpwd. sys      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
| Biztonság                | MS17-010       | KB4012212                                 | KB4012213                                   | KB4012213                          | KB4012606                                               | KB4012606                  | -                                               | -                                               |
|                         |                |                                           | KB4012216                                   |                                    | KB4013198                                               | KB4013198                  | -                                               | -                                               |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                          | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         |                |                                           | KB4012217                                   |                                    | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         | CVE-2018-0886  | KB4103718               | KB4103730                | KB4103725       | KB4103723                                               | KB4103731                  | KB4103727                                       | KB4103721                                       |
|                         |                | KB4103712          | KB4103726          | KB4103715|                                                         |                            |                                                 |                                                 |
       
### A Sysprep használatának időpontjának meghatározása<a id="step23"></a>    

A rendszer-előkészítő eszköz (Sysprep) egy olyan folyamat, amely a Windows telepítésének alaphelyzetbe állítására szolgál. A Sysprep az összes személyes információ eltávolításával és számos összetevő alaphelyzetbe állításával "kifogyott" élményt biztosít. 

A Sysprep általában úgy fut, hogy olyan sablont hozzon létre, amelyből több, adott konfigurációval rendelkező virtuális gép is telepíthető. A sablont *általánosított rendszerképnek*nevezzük.

Ha csak egy virtuális gépet szeretne létrehozni egy lemezről, nem kell a Sysprept használnia. Ehelyett a virtuális gépet egy *speciális rendszerképből*is létrehozhatja. A virtuális gépek speciális lemezről történő létrehozásával kapcsolatos információkért lásd:

- [Virtuális gép létrehozása speciális lemezről](create-vm-specialized.md)
- [Virtuális gép létrehozása speciális VHD-lemezről](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master)

Ha általánosított rendszerképet szeretne létrehozni, futtatnia kell a Sysprep programot. További információ [: a Sysprep használata: Bevezetés](https://technet.microsoft.com/library/bb457073.aspx). 

Nem minden Windows-alapú számítógépre telepített szerepkör vagy alkalmazás támogatja az általánosított rendszerképeket. Ezért az eljárás futtatása előtt győződjön meg arról, hogy a Sysprep támogatja a számítógép szerepkörét. További információ: a [Sysprep-támogatás a kiszolgálói szerepkörökhöz](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

### <a name="generalize-a-vhd"></a>Virtuális merevlemez általánosítása

>[!NOTE]
> Miután a következő `sysprep.exe` lépésekben futtatta a parancsot, kapcsolja ki a virtuális gépet. Ne kapcsolja vissza, amíg létre nem hoz egy rendszerképet az Azure-ban.

1. Jelentkezzen be a Windows rendszerű virtuális gépre.
1. Futtassa a parancssort rendszergazdaként. 
1. Módosítsa a könyvtárat `%windir%\system32\sysprep`a következőre:. Ez után futtassa a `sysprep.exe` parancsot.
1. A **Rendszer-előkészítő eszköz** párbeszédpanelen válassza **A kezdőélmény indítása** lehetőséget, és győződjön meg róla, hogy be van-e jelölve az **Általánosítás** jelölőnégyzet.

    ![Rendszerelőkészítő eszköz](media/prepare-for-upload-vhd-image/syspre.png)
1. A **leállítási beállítások**területen válassza a **Leállítás**lehetőséget.
1. Kattintson az **OK** gombra.
1. A Sysprep befejeződése után állítsa le a virtuális gépet. Ne használja az **Újraindítás** lehetőséget a virtuális gép leállításához.

Most már készen áll a virtuális merevlemez feltöltésére. A virtuális gépek általánosított lemezről történő létrehozásával kapcsolatos további információkért lásd: [általánosított virtuális merevlemez feltöltése és használata új virtuális gép létrehozásához az Azure-ban](sa-upload-generalized.md).


>[!NOTE]
> Az egyéni *Unattend. XML* fájl nem támogatott. Bár támogatjuk a `additionalUnattendContent` tulajdonságot, amely csak korlátozott támogatást biztosít a [Microsoft-Windows-rendszerhéj-telepítési](https://docs.microsoft.com/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) beállításoknak az Azure-beli kiépítési ügynök által használt *Unattend. XML* fájlhoz való hozzáadásához. Használhatja például a [additionalUnattendContent](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent?view=azure-dotnet) -t a FirstLogonCommands és a LogonCommands hozzáadásához. További információ: [AdditionalUnattendContent FirstLogonCommands example](https://github.com/Azure/azure-quickstart-templates/issues/1407).


## <a name="complete-the-recommended-configurations"></a>A javasolt konfigurációk végrehajtása
A következő beállítások nem érintik a VHD feltöltését. Javasoljuk azonban, hogy konfigurálja azokat.

* Telepítse az [Azure Virtual Machine Agent ügynököt](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Ezután engedélyezheti a virtuálisgép-bővítményeket. A virtuálisgép-bővítmények a legtöbb kritikus funkciót implementálják, amelyeket érdemes használni a virtuális gépekhez. Szüksége lesz a bővítményekre, például a jelszavak alaphelyzetbe állításához vagy az RDP konfigurálásához. További információ: az [Azure Virtual Machine Agent áttekintése](../extensions/agent-windows.md).
* Miután létrehozta a virtuális gépet az Azure-ban, javasoljuk, hogy a teljesítmény növelése érdekében helyezze el a lapozófájlt az *ideiglenes meghajtó kötetén* . A fájl elhelyezését az alábbiak szerint állíthatja be:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -name "PagingFiles" -Value "D:\pagefile.sys" -Type MultiString -force
   ```
  Ha egy adatlemez csatlakozik a virtuális géphez, az ideiglenes meghajtó kötetének betűjele általában *D*. Ez a megjelölés eltérő lehet a beállításaitól és a rendelkezésre álló meghajtók számától függően.
  * Javasoljuk, hogy tiltsa le a víruskereső szoftverek által biztosított parancsfájl-blokkolók letiltását. Előfordulhat, hogy az új virtuális gép lemezképből való üzembe helyezése során a Windows kiépítési ügynök parancsfájljait nem lehet megakadályozni és letiltani.
  
## <a name="next-steps"></a>További lépések
* [Windowsos virtuálisgép-rendszerkép feltöltése az Azure-ba Resource Manager-alapú üzembe helyezésekhez](upload-generalized-managed.md)
* [Az Azure Windows VM aktiválási problémáinak elhárítása](troubleshoot-activation-problems.md)

