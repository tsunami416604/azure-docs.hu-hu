---
title: Töltse fel az Azure Windows virtuális merevlemez előkészítése |} A Microsoft Docs
description: Ismerje meg, hogyan készíti elő egy Windows VHD vagy VHDX, az Azure-bA feltöltendő
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/11/2019
ms.author: genli
ms.openlocfilehash: cc942aeb34d17e8dff064c6a21a3c7b2099c742a
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67151028"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Windows VHD vagy VHDX feltöltése az Azure előkészítése

Mielőtt egy Windows virtuális gép (VM) a helyszínről az Azure-bA tölt fel, elő kell készítenie a virtuális merevlemez (VHD vagy vhdx-fájlt). Az Azure támogatja az 1. és 2. generációs virtuális gépek, amelyek VHD formátumú, és amelyek egy rögzített méretű lemezt is. A VHD számára engedélyezett maximális mérete 1,023 GB. 

Az 1. generációs virtuális gép, átválthat egy VHDX-fájl rendszer VHD-t. A rögzített méretű lemez átválthat egy dinamikusan bővülő lemezek is. De nem módosíthatja a virtuális gép létrehozás. További információkért lásd: [érdemes létrehozni egy 1 vagy 2. generációs virtuális gép a Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) és [generation 2 virtuális gépen (előzetes verzió) támogatása az Azure](generation-2.md).

Azure virtuális gépek a támogatási házirenddel kapcsolatos információkért lásd: [Microsoft kiszolgálószoftveres támogatás az Azure virtuális gépek](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

> [!NOTE]
> A jelen cikkben lévő utasítások a Windows Server 2008 R2 és újabb Windows Server operációs rendszerek 64 bites verziójára vonatkoznak. További információ a 32 bites operációs rendszert futtató Azure-ban: [Azure virtuális gépeken futó 32 bites operációs rendszerek támogatása](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines).

## <a name="convert-the-virtual-disk-to-a-fixed-size-and-to-vhd"></a>A virtuális lemez konvertálása egy rögzített méretű és a virtuális merevlemez 
Ha a kötelező formátumot az Azure-hoz a virtuális lemez konvertálása van szüksége, használja a módszerek közül az ebben a szakaszban. Készítsen biztonsági másolatot a virtuális Gépet, mielőtt megkezdené a virtuális lemezt. Győződjön meg arról, hogy a Windows virtuális merevlemez megfelelően működik-e a helyi kiszolgálón. Majd javítsa ki a hibákat a virtuális gépre belül előtt konvertálja, vagy töltse fel az Azure-bA.

Miután konvertálja a lemezt, hozzon létre a lemezt használó virtuális gép. Indítsa el, és jelentkezzen be a virtuális gép előkészítése feltöltése befejeződik.

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>A Hyper-V kezelőjével konvertálhatja a lemez 
1. Nyissa meg a Hyper-V kezelőjét, és válassza ki a helyi számítógépen, a bal oldalon. Válassza a számítógép eszközlista feletti menüben **művelet** > **lemez szerkesztése**.
2. Az a **virtuális merevlemez keresése** lapra, jelölje be a virtuális lemezt.
3. Az a **választott tevékenység** lapon jelölje be **konvertálása** > **tovább**.
4. Ha VHDX próbaverzióról van szüksége, válassza ki a **VHD** > **tovább**.
5. Ha egy dinamikusan bővülő lemezek konvertálása van szüksége, válassza ki a **rögzített méretű** > **tovább**.
6. Keresse meg és válassza ki a új virtuális merevlemez fájl elérési útját.
7. Válassza a **Finish** (Befejezés) elemet.

> [!NOTE]
> Egy rendszergazda jogú PowerShell-munkamenetben használja a parancsok futtatásához az ebben a cikkben.

### <a name="use-powershell-to-convert-the-disk"></a>A lemez konvertálása a PowerShell használatával 
A virtuális lemez segítségével alakíthatja a [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) parancsot a Windows PowerShellben. Válassza ki **Futtatás rendszergazdaként** PowerShell indításakor. 

A következő példaparancs átalakítja a VHDX VHD-t. A parancs is átalakítja a dinamikusan bővülő lemezek egy rögzített méretű lemezt.

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

Ebben a parancsban cserélje le az értéket `-Path` az átalakítani kívánt virtuális merevlemez elérési útját. Cserélje le az értéket a `-DestinationPath` az új elérési utat és a konvertált lemez neve.

### <a name="convert-from-vmware-vmdk-disk-format"></a>VMware VMDK lemez formátum konvertálása
Ha rendelkezik egy Windows Virtuálisgép-lemezkép a [VMDK-fájl formátuma](https://en.wikipedia.org/wiki/VMDK), használja a [Microsoft Virtual Machine Converter](https://www.microsoft.com/download/details.aspx?id=42497) VHD formátumra átalakítandó. További információkért lásd: [VMware VMDK átalakítása Hyper-V virtuális merevlemez](https://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx).

## <a name="set-windows-configurations-for-azure"></a>Az Azure Windows-konfigurációnak megadása

A virtuális gép feltöltése az Azure-bA felvenni kívánt, futtassa a következő parancsokat egy [emelt jogosultságszintű parancssort](https://technet.microsoft.com/library/cc947813.aspx):

1. Távolítsa el az útválasztási táblázatban az állandó statikus útvonalakat:
   
   * Az útvonaltábla megtekintéséhez futtassa `route print` parancsot a parancssorba.
   * Ellenőrizze a `Persistence Routes` szakaszokat. Ha egy állandó útvonalat, használja a `route delete` távolítsa el a parancsot.
2. Távolítsa el a WinHTTP proxy:
   
    ```PowerShell
    netsh winhttp reset proxy
    ```

    Ha a virtuális Gépet kell dolgoznia egy adott proxy, egy proxy kivétel hozzáadása az Azure IP-cím ([168.63.129.16](https://blogs.msdn.microsoft.com/mast/2015/05/18/what-is-the-ip-address-168-63-129-16/
)), a virtuális gép csatlakozhatnak az Azure-bA:
    ```
    $proxyAddress="<your proxy server>"
    $proxyBypassList="<your list of bypasses>;168.63.129.16"

    netsh winhttp set proxy $proxyAddress $proxyBypassList
    ```

3. A lemez a SAN-szabályzat beállítása [ `Onlineall` ](https://technet.microsoft.com/library/gg252636.aspx):
   
    ```PowerShell
    diskpart 
    ```
    A megnyitott parancssori ablakban írja be a következő parancsokat:

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Egyezményes világidő (UTC) idő beállítása a Windows számára. A Windows időszolgáltatás indítási típusát is beállíthatja (`w32time`) való `Automatic`:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -name "RealTimeIsUniversal" -Value 1 -Type DWord -force

    Set-Service -Name w32time -StartupType Automatic
    ```
5. Nagy teljesítményű állítsa be a power profil:

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```
6. Győződjön meg arról, hogy a környezeti változók `TEMP` és `TMP` az alapértelmezett értékekre vannak beállítva:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TEMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force
    ```

## <a name="check-the-windows-services"></a>Ellenőrizze a Windows-szolgáltatások
Győződjön meg arról, hogy a következő Windows-szolgáltatások mindegyike a Windows alapértelmezett értékre állítva. Ezek a szolgáltatások biztosításához a virtuális gépek kapcsolatai beállított minimális. Az indítási beállítások alaphelyzetbe állítása, futtassa a következő parancsokat:
   
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

## <a name="update-remote-desktop-registry-settings"></a>Remote-desktop beállításjegyzék-beállítások frissítése
Győződjön meg arról, hogy a következő beállításokkal megfelelően vannak-e konfigurálva a távoli hozzáféréshez:

>[!NOTE] 
>Hibaüzenetet kaphat futtatásakor `Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -name &lt;object name&gt; -value &lt;value&gt;`. Ez az üzenet biztonságosan figyelmen kívül hagyja. Ez azt jelenti, hogy csak, hogy a tartomány nem küld ez a konfiguráció segítségével a csoportházirend-objektum.

1. Engedélyezve van a távoli asztal protokoll (RDP):
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDenyTSConnections" -Value 0 -Type DWord -force
    ```
   
2. Az RDP-portra megfelelően van beállítva. Az alapértelmezett port: 3389-es:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "PortNumber" -Value 3389 -Type DWord -force
    ```
    Amikor telepít egy virtuális Gépet, szemben a 3389-es port az alapértelmezett szabályok jönnek létre. Ha azt szeretné, hogy módosítsa a portszámot, ez nem az Azure-ban a virtuális gép üzembe helyezése után.

3. A figyelő minden hálózati felületen figyeli.
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "LanAdapter" -Value 0 -Type DWord -force
   ```
4. A hálózati szintű hitelesítéssel (NLA) módot, az RDP-kapcsolatok konfigurálása:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "UserAuthentication" -Value 1 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SecurityLayer" -Value 1 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "fAllowSecProtocolNegotiation" -Value 1 -Type DWord -force
     ```

5. Életben tartási értékeként adja meg:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveEnable" -Value 1  -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveInterval" -Value 1  -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "KeepAliveTimeout" -Value 1 -Type DWord -force
    ```
6. Csatlakoztassa újra:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDisableAutoReconnect" -Value 0 -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fInheritReconnectSame" -Value 1 -Type DWord -force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fReconnectSame" -Value 0 -Type DWord -force
    ```
7. Egyidejű kapcsolatok számának korlátozása:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "MaxInstanceCount" -Value 4294967295 -Type DWord -force
    ```
8. Az RDP-figyelő kötött önaláírt tanúsítványok eltávolítása:
    
    ```PowerShell
    Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SSLCertificateSHA1Hash" -force
    ```
    Ez a kód biztosítja, hogy csatlakozott az elején, a virtuális gép telepítésekor. Ha szeretne később tekintse át ezt, megteheti az Azure-ban a virtuális gép üzembe helyezése után.

9. Ha a virtuális gép lesz része egy tartománynak, ellenőrizze, hogy a korábbi beállításokat nem állítja vissza a rendszer a következő házirendek. 
    
    | Cél                                     | Szabályzat                                                                                                                                                       | Érték                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | Az RDP engedélyezve van                           | Computer Configuration\Policies\Windows Settings\Administrative Templates\Components\Remote Desktop Services\Remote Desktop Session Host\Connections         | Lehetővé teszi a felhasználók távolról kapcsolódni a távoli asztal használatával                                  |
    | NLA csoportházirend                         | Settings\Administrative Templates\Components\Remote Desktop Services\Remote Desktop Session Host\Security                                                    | Felhasználói hitelesítés szükséges a távoli hozzáféréshez NLA használatával |
    | Életben tartási beállításai                      | Computer Configuration\Policies\Windows Settings\Administrative Templates\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections | Kapcsolat életben tartási intervallum                                                 |
    | Újracsatlakozás a beállítások                       | Computer Configuration\Policies\Windows Settings\Administrative Templates\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections | Csatlakoztassa újra automatikusan                                                                   |
    | Korlátozott számú kapcsolat beállításai | Computer Configuration\Policies\Windows Settings\Administrative Templates\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections | A kapcsolatok száma                                                              |

## <a name="configure-windows-firewall-rules"></a>Windows tűzfal-szabályok konfigurálása
1. A Windows tűzfal bekapcsolása a három profilt (a tartományhoz, standard és a nyilvános):

   ```PowerShell
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
   ```

2. Futtassa a következő parancsot a PowerShell WinRM engedélyezéséhez a három tűzfalprofil (tartományi, privát és nyilvános) keresztül, és engedélyezze a távoli PowerShell-szolgáltatást:
   
   ```PowerShell
    Enable-PSRemoting -force

    Set-NetFirewallRule -DisplayName "Windows Remote Management (HTTP-In)" -Enabled True
   ```
3. A következő tűzfalszabályokat, hogy az RDP-forgalmat engedélyezi:

   ```PowerShell
    Set-NetFirewallRule -DisplayGroup "Remote Desktop" -Enabled True
   ```   
4. A fájl- és nyomtatómegosztás a ping parancsra, a virtuális hálózaton belül a virtuális gép reagáljon a szabály engedélyezése:

   ```PowerShell
   Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Enabled True
   ``` 
5. Ha a virtuális gép lesz része egy tartománynak, ellenőrizze, hogy a korábbi beállításokat nem állítja vissza a rendszer a következő Azure AD-házirendek. 

    | Cél                                 | Szabályzat                                                                                                                                                  | Érték                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | A Windows tűzfal-profilok engedélyezése | Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows Firewall   | Az összes hálózati kapcsolatok védelme         |
    | Engedélyezze az RDP-                           | Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows Firewall   | A bejövő távoli asztali Kivételek tiltása |
    |                                      | Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Standard Profile\Windows Firewall | A bejövő távoli asztali Kivételek tiltása |
    | Engedélyezze az ICMP-V4                       | Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows Firewall   | Az ICMP-kivételek engedélyezése                   |
    |                                      | Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Standard Profile\Windows Firewall | Az ICMP-kivételek engedélyezése                   |

## <a name="verify-the-vm"></a>Ellenőrizze a virtuális gép 

Győződjön meg arról, hogy a virtuális gép kifogástalan állapotú, biztonságos, és elérhető-e az RDP: 

1. Győződjön meg arról, hogy a lemez kifogástalan állapotú és egységes, ellenőrizze a lemez található a következő virtuális gép újraindítása:

    ```PowerShell
    Chkdsk /f
    ```
    Ellenőrizze, hogy a jelentés megjeleníti egy letisztult és kifogástalan állapotú lemezt.

2. A rendszerindítási konfigurációs adatok (BCD) beállításainak megadása. 

    > [!NOTE]
    > Futtassa a következő parancsokat használja egy rendszergazda jogú PowerShell-ablakban.
   
   ```powershell
    cmd

    bcdedit /set {bootmgr} integrityservices enable
    bcdedit /set {default} device partition=C:
    bcdedit /set {default} integrityservices enable
    bcdedit /set {default} recoveryenabled Off
    bcdedit /set {default} osdevice partition=C:
    bcdedit /set {default} bootstatuspolicy IgnoreAllFailures

    #Enable Serial Console Feature
    bcdedit /set {bootmgr} displaybootmenu yes
    bcdedit /set {bootmgr} timeout 5
    bcdedit /set {bootmgr} bootems yes
    bcdedit /ems {current} ON
    bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200

    exit
   ```
3. A memóriakép napló a hibaelhárítást a Windows összeomlási hasznos lehet. A napló memóriakép-gyűjtés engedélyezése:

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
4. Győződjön meg arról, hogy a Windows Management Instrumentation (WMI) tárház egységes:

    ```PowerShell
    winmgmt /verifyrepository
    ```
    Ha a tárház sérült, [WMI: A tárház megsérült vagy nem](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

5. Győződjön meg arról, hogy egyetlen másik alkalmazás nem 3389-es portot használ. Ezt a portot használja az RDP-szolgáltatás az Azure-ban. Milyen portokat használják a virtuális gépen futtassa `netstat -anob`:

    ```PowerShell
    netstat -anob
    ```

6. Feltölteni egy Windows, a tartományvezérlő virtuális Merevlemezt:

   * Hajtsa végre a [további lépések](https://support.microsoft.com/kb/2904015) készíti elő a lemezen.

   * Ellenőrizze, hogy ismeri a Directory címtárszolgáltatások helyreállító módjának (DSRM) jelszavát abban az esetben el kell indítani a virtuális gép a címtárszolgáltatások Helyreállító módjában időpontban induljanak. További információkért lásd: [állítsa be a DSRM-jelszót](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx).

7. Ellenőrizze, hogy ismeri a beépített rendszergazdai fiókkal és jelszóval. Előfordulhat, hogy szeretné visszaállítani a jelenlegi helyi rendszergazdai jelszót, és ellenőrizze, hogy ezt a fiókot használhatja a bejelentkezni a Windows az RDP-kapcsolaton keresztül. A hozzáférési engedélyt a "Bejelentkezés engedélyezése a távoli asztali szolgáltatások használatával" csoportházirend-objektumot határozza meg. Ezen objektum megtekintésére a a Helyicsoportházirend-szerkesztő itt:

    Számítógép konfigurációja\A Windows beállításai\Biztonsági beállítások\Helyi házirend\Felhasználói jogok kiosztása

8. Az alábbi Azure AD-házirendek, győződjön meg arról, hogy nem blokkolja a RDP-Kapcsolaton keresztül, vagy a hálózatról az RDP-hozzáférés ellenőrzése:

    - Számítógép konfigurációja\A Windows beállításai\Biztonsági beállítások\Helyi házirend\Felhasználói jogok kiosztása\kötegelt munka bejelentkezésének megtagadása hozzáférést ehhez a számítógéphez a hálózatról

    - Számítógép konfigurációja\A Windows beállításai\Biztonsági beállítások\Helyi házirend\Felhasználói jogok kiosztása\helyi bejelentkezés megtagadása a távoli asztali szolgáltatások használatával


9. Ellenőrizze a következő Azure AD-szabályzatot, hogy még nem eltávolítása a hozzáférési fiókokat bármelyikét:

   - Számítógép konfigurációja\A Windows beállításai\Biztonsági beállítások\Helyi házirend\Felhasználói jogok Assignment\Access ezen a számítógépen, a hálózatról

   A szabályzat felsorolásban szerepelnie kell a következő csoportok:

   - A rendszergazdák

   - Biztonságimásolat-felelősök

   - Mindenki számára

   - Felhasználók

10. Indítsa újra a virtuális Gépen, győződjön meg arról, hogy a Windows továbbra is működik megfelelően, és az RDP-kapcsolaton keresztül érhető el. Ezen a ponton érdemes a helyi Hyper-V – ellenőrizze, hogy a virtuális gép teljesen elindul a virtuális gép létrehozásához. Ellenőrizze, hogy ellenőrizze, hogy a virtuális gép RDP-Kapcsolaton keresztül érheti el.

11. Távolítsa el a felesleges átviteli Átvitelmeghajtói felület (TDI) szűrőket. Például, távolítsa el a szoftver, amely elemzi a TCP csomagokat, vagy további tűzfalakat. Ha szeretne később tekintse át ezt, megteheti az Azure-ban a virtuális gép üzembe helyezése után.

12. Távolítsa el a harmadik féltől származó szoftverek vagy illesztőprogramot, amely a fizikai kapcsolódó összetevők vagy más virtualizációs technológiát.

### <a name="install-windows-updates"></a>Windows-frissítések telepítése
Ideális esetben a gép frissíteni kell tartania a *javítási szintje*. Ha ez nem lehetséges, győződjön meg arról, hogy a következő frissítések telepítve vannak:

| Összetevő               | binary         | Windows 7 SP1, Windows Server 2008 R2 SP1 | A Windows 8, Windows Server 2012-ben               | Windows 8.1, Windows Server 2012 R2 rendszerben | A Windows 10-es v1607, a Windows Server 2016 v1607 | Windows 10 v1703    | A Windows 10-es v1709, a Windows Server 2016 v1709 | A Windows 10-es v1803, a Windows Server 2016 v1803 |
|-------------------------|----------------|-------------------------------------------|---------------------------------------------|------------------------------------|---------------------------------------------------------|----------------------------|-------------------------------------------------|-------------------------------------------------|
| Storage                 | Disk.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061         | -                                                       | -                          | -                                               | -                                               |
|                         | Storport.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.332             | -                                               | -                                               |
|                         | NTFS.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Iologmsg.dll   | 6.1.7601.23403 - KB3125574                | 6.2.9200.16384 - KB2995387                  | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Classpnp.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614         | 10.0.14393.953 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Volsnap.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384         | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | partmgr.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 - KB3000850         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | volmgr.sys     |                                           |                                             |                                    |                                                         | 10.0.15063.0               | -                                               | -                                               |
|                         | Volmgrx.sys    | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | Msiscsi.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726         | 10.0.14393.1066 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Msdsm.sys      | 6.1.7601.23403 - KB3125574                | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726         | -                                                       | -                          | -                                               | -                                               |
|                         | MPIO.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726         | 10.0.14393.1198 - KB4022715                             | -                          | -                                               | -                                               |
|                         | vmstorfl.sys   | 6.3.9600.18907 - KB4072650                | 6.3.9600.18080 - KB3063109                  | 6.3.9600.18907 - KB4072650         | 10.0.14393.2007 - KB4345418                             | 10.0.15063.850 - KB4345419 | 10.0.16299.371 - KB4345420                      | -                                               |
|                         | Fveapi.dll     | 6.1.7601.23311 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614         | 10.0.14393.576 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Fveapibase.dll | 6.1.7601.23403 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614         | 10.0.14393.206 - KB4022715                              | -                          | -                                               | -                                               |
| Network (Hálózat)                 | netvsc.sys     | -                                         | -                                           | -                                  | 10.0.14393.1198 - KB4022715                             | 10.0.15063.250 - KB4020001 | -                                               | -                                               |
|                         | mrxsmb10.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726         | 10.0.14393.479 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb20.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | Mrxsmb.sys     | 6.1.7601.23816 - KB4022722                | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | Tcpip.sys      | 6.1.7601.23761 - KB4022722                | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | a HTTP.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726         | 10.0.14393.251 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | vmswitch.sys   | 6.1.7601.23727 - KB4022719                | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.138             | -                                               | -                                               |
| Core                    | ntoskrnl.exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.483             | -                                               | -                                               |
| Távoli asztali szolgáltatások | rdpcorets.dll  | 6.2.9200.21506 - KB4022719                | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.0               | -                                               | -                                               |
|                         | termsrv.dll    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 - KB3000850         | 10.0.14393.0 - KB4022715                                | 10.0.15063.0               | -                                               | -                                               |
|                         | termdd.sys     | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Win32k.sys     | 6.1.7601.23807 - KB4022719                | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726         | 10.0.14393.594 - KB4022715                              | -                          | -                                               | -                                               |
|                         | rdpdd.dll      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Rdpwd.sys      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
| Biztonság                | MS17-010       | KB4012212                                 | KB4012213                                   | KB4012213                          | KB4012606                                               | KB4012606                  | -                                               | -                                               |
|                         |                |                                           | KB4012216                                   |                                    | KB4013198                                               | KB4013198                  | -                                               | -                                               |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                          | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         |                |                                           | KB4012217                                   |                                    | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         | CVE – 2018-0886  | KB4103718               | KB4103730                | KB4103725       | KB4103723                                               | KB4103731                  | KB4103727                                       | KB4103721                                       |
|                         |                | KB4103712          | KB4103726          | KB4103715|                                                         |                            |                                                 |                                                 |
       
### Mikor a Sysprep program használata <a id="step23"></a>    

Rendszer-előkészítő eszköz (Sysprep) egy olyan folyamat, egy Windows-telepítési alaphelyzetbe futtathatja. A Sysprep "a kezdő" verzióról felületet biztosítja a személyes adatokat, és alaphelyzetbe állítása több összetevőt. 

Általában, amelyről telepíthet több más virtuális gépek adott konfigurációval rendelkező sablon létrehozása a Sysprep futtatása. A sablon neve egy *rendszerkép általánosítva*.

Ha csak egy virtuális gép létrehozása egy lemezről, nem kell a Sysprep eszközt használja. Ehelyett a virtuális Gépet hozhat létre egy *specializált rendszerkép*. A virtuális gép létrehozása specializált lemezből kapcsolatos információkért lásd:

- [Virtuális gép létrehozása specializált lemezből](create-vm-specialized.md)
- [Virtuális gép létrehozása speciális lemezről a virtuális merevlemez](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master)

Ha szeretne létrehozni egy általános rendszerképpel, meg kell futtassa a Sysprep eszközt. További információkért lásd: [a Sysprep használata: Bevezetés](https://technet.microsoft.com/library/bb457073.aspx). 

Nem minden szerepkör vagy alkalmazást, amely egy Windows-alapú számítógépre van telepítve az általánosított képeket támogatja. Ezért mielőtt elkezdi a műveletet, ellenőrizze, hogy a Sysprep támogatja a számítógép a szerepkör. További információkért lásd: [kiszolgálói szerepkörök támogatása a Sysprep](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

### <a name="generalize-a-vhd"></a>Virtuális merevlemez generalize

>[!NOTE]
> Futtatása után `sysprep.exe` a következő lépésekben kikapcsolja a virtuális Gépet. Ne kapcsolja be újra, amíg létre lemezképet, az abból az Azure-ban.

1. Jelentkezzen be a Windows virtuális gép.
1. Futtatás **parancssor** rendszergazdaként. 
1. Módosítsa a könyvtárat a `%windir%\system32\sysprep`. Ez után futtassa a `sysprep.exe` parancsot.
1. A **Rendszer-előkészítő eszköz** párbeszédpanelen válassza **A kezdőélmény indítása** lehetőséget, és győződjön meg róla, hogy be van-e jelölve az **Általánosítás** jelölőnégyzet.

    ![Rendszer-előkészítő eszköz](media/prepare-for-upload-vhd-image/syspre.png)
1. A **leállítási beállítások**válassza **leállítási**.
1. Kattintson az **OK** gombra.
1. A Sysprep befejeződése után állítsa le a virtuális Gépet. Ne használjon **indítsa újra a** a virtuális gép leállítása.

A VHD-t most már elkészült tölthető fel. A virtuális gép létrehozása általánosított lemezről kapcsolatos további információkért lásd: [általános VHD feltöltése és ezzel hozzon létre egy új virtuális Gépet az Azure-ban](sa-upload-generalized.md).


>[!NOTE]
> Egyéni *unattend.xml* fájl nem támogatott. Bár támogatjuk a `additionalUnattendContent` tulajdonság, amely csak korlátozott támogatást biztosít a hozzáadása [microsoft-windows-rendszerhéj – telepítő](https://docs.microsoft.com/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) lehetőség be a *unattend.xml* fájlt, amely az Azure-kiépítés ügynök használja. Is használhatja, például [additionalUnattendContent](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent?view=azure-dotnet) FirstLogonCommands és LogonCommands adhat hozzá. További információkért lásd: [additionalUnattendContent FirstLogonCommands példa](https://github.com/Azure/azure-quickstart-templates/issues/1407).


## <a name="complete-the-recommended-configurations"></a>Az ajánlott konfiguráció befejezéséhez.
A következő beállítások nem befolyásolják a VHD feltöltésével. Azonban erősen ajánlott, hogy konfigurálta azokat.

* Telepítse a [Azure-beli virtuálisgép-ügynök](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Ezután engedélyezheti a Virtuálisgép-bővítmények. A Virtuálisgép-bővítmények a kritikus fontosságú funkciókat, amelyeket érdemes a virtuális gépek használata a legtöbb megvalósításához. Szüksége lesz a bővítmények, például jelszavakat vagy RDP konfigurálása. További információkért lásd: [Azure-beli virtuálisgép-ügynök – áttekintés](../extensions/agent-windows.md).
* Miután létrehozta a virtuális gép az Azure-ban, azt javasoljuk, hogy Ön telepít a lapozófájlt a *historikus meghajtó* teljesítmény javítása érdekében. Beállíthat elhelyezéséhez a következőképpen:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -name "PagingFiles" -Value "D:\pagefile.sys" -Type MultiString -force
   ```
  Ha egy adatlemezt a virtuális Géphez van csatlakoztatva, az ideiglenes meghajtó kötetbetűt általában van-e *D*. Ez a megnevezés a beállítások és a elérhető meghajtók számától függően eltérő lehet.

## <a name="next-steps"></a>További lépések
* [Windows Virtuálisgép-Rendszerkép feltöltése az Azure-bA a Resource Manager üzembe helyezések](upload-generalized-managed.md)
* [Azure Windows virtuális gép aktiválással kapcsolatos problémák elhárítása](troubleshoot-activation-problems.md)

