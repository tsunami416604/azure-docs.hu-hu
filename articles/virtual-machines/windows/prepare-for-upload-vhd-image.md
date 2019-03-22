---
title: Töltse fel az Azure Windows virtuális merevlemez előkészítése |} A Microsoft Docs
description: A Windows VHD vagy VHDX előkészítése az Azure-ba való feltöltéséhez
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
ms.date: 12/13/2018
ms.author: genli
ms.openlocfilehash: 0988902e0a2154f2935a01ddcfb6a460be693df3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58093803"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Windows VHD vagy VHDX feltöltése az Azure előkészítése
Mielőtt egy Windows virtuális gépek (VM) a helyi Microsoft Azure-bA tölt fel, elő kell készítenie a virtuális merevlemez (VHD vagy vhdx-fájlt). Az Azure támogatja a **csak az 1. generációs virtuális gépeket** , amely a VHD formátumban, és rögzített méretű lemezt. A VHD számára engedélyezett maximális mérete 1,023 GB. Átválthat egy generation 1 VM a vhdx-fájlt a fájlrendszer VHD-t és a egy dinamikusan bővülő rögzített méretű lemezt. De nem módosíthatja a virtuális gép létrehozás. További információkért lásd: [érdemes létrehozni egy 1 vagy 2. generációs virtuális gép a Hyper-V](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

Azure virtuális gép a támogatási házirenddel kapcsolatos további információkért lásd: [Microsoft kiszolgálószoftveres támogatás az Microsoft Azure virtuális gépek](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

> [!Note]
> A jelen cikkben lévő utasítások a Windows Server 2008 R2 és újabb Windows server operációs rendszer 64 bites verziójára vonatkoznak. Operációs rendszer az Azure-ban futó 32 bites verzióval kapcsolatos információkért lásd: [32 bites operációs rendszerek Azure-beli virtuális gépeken támogatása](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines).

## <a name="convert-the-virtual-disk-to-vhd-and-fixed-size-disk"></a>A virtuális lemez konvertálása a VHD- és a rögzített méretű lemez 
Ha a kötelező formátumot az Azure-hoz a virtuális lemez konvertálása van szüksége, használja a módszerek közül az ebben a szakaszban. A virtuális gép biztonsági mentése előtt a virtuális lemez konvertálási folyamat futtatja, és ellenőrizze, hogy a Windows virtuális merevlemez megfelelően működik-e a helyi kiszolgálón. Javítsa ki a hibákat belül a virtuális gépre, mielőtt konvertálja, vagy töltse fel az Azure-bA.

Miután konvertálja a lemezt, hozzon létre a konvertált lemezt használó virtuális gép. Indítsa el, és jelentkezzen be a virtuális géphez a virtuális gép előkészítése feltöltéshez befejezéséhez.

### <a name="convert-disk-using-hyper-v-manager"></a>Konvertálja a lemezt a Hyper-V Manager segítségével
1. Nyissa meg a Hyper-V kezelőjét, és válassza ki a helyi számítógépen, a bal oldalon. A számítógép eszközlista feletti menüben kattintson **művelet** > **lemez szerkesztése**.
2. Az a **virtuális merevlemez keresése** képernyőn, keresse meg és jelölje ki a virtuális lemezt.
3. Az a **választott tevékenység** képernyőn, és válassza ki **konvertálása** és **tovább**.
4. Ha VHDX próbaverzióról van szüksége, válassza ki a **VHD** majd **tovább**.
5. Ha egy dinamikusan bővülő lemezek konvertálása van szüksége, válassza ki **rögzített méretű** majd **tovább**.
6. Keresse meg és válassza ki a új virtuális merevlemez fájl elérési útját.
7. Kattintson a **Befejezés** gombra.

>[!NOTE]
>Az ebben a cikkben szereplő parancsokkal egy emelt szintű PowerShell-munkamenetben kell futtatni.

### <a name="convert-disk-by-using-powershell"></a>Lemez konvertálása a PowerShell használatával
A virtuális lemez segítségével alakíthatja a [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) parancsot a Windows PowerShellben. Válassza ki **Futtatás rendszergazdaként** PowerShell indításakor. 

A következő példaparancs konvertálja a virtuális merevlemez VHDX az és a egy dinamikusan bővülő rögzített méretű lemezt:

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```
Ebben a parancsban cserélje le az értéket "-elérési út" az elérési útját a virtuális merevlemez, amelyek az átalakítandó és az értéke "-DestinationPath" az új elérési utat és a konvertált lemez neve.

### <a name="convert-from-vmware-vmdk-disk-format"></a>VMware VMDK lemez formátum konvertálása
Ha rendelkezik egy Windows Virtuálisgép-lemezkép a [VMDK-fájl formátuma](https://en.wikipedia.org/wiki/VMDK), konvertálja a virtuális merevlemez a [Microsoft VM Converter](https://www.microsoft.com/download/details.aspx?id=42497). További információ a blogot cikke [VMware VMDK átalakítása Hyper-V virtuális merevlemez](https://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx).

## <a name="set-windows-configurations-for-azure"></a>Az Azure Windows-konfigurációnak megadása

A virtuális gépen, amely azt tervezi, hogy töltse fel az Azure-ba, az alábbi lépéseket az összes parancsok futtatását egy [emelt jogosultságszintű parancssort](https://technet.microsoft.com/library/cc947813.aspx):

1. Távolítsa el az útválasztási táblázatban az állandó statikus útvonalakat:
   
   * Az útvonaltábla megtekintéséhez futtassa `route print` parancsot a parancssorba.
   * Ellenőrizze a **adatmegőrzés útvonalak** szakaszokat. Ha egy állandó útvonalat, használja a **route delete** távolítsa el a parancsot.
2. Távolítsa el a WinHTTP proxy:
   
    ```PowerShell
    netsh winhttp reset proxy
    ```

    Ha a virtuális gép kell semmilyen speciális proxykat, hozzá kell adnia a proxy-kivétel az Azure IP-cím ([168.63.129.16](https://blogs.msdn.microsoft.com/mast/2015/05/18/what-is-the-ip-address-168-63-129-16/
)), így a virtuális gép kapcsolódik az Azure-ban:
    ```
    $proxyAddress="<your proxy server>"
    $proxyBypassList="<your list of bypasses>;168.63.129.16"

    netsh winhttp set proxy $proxyAddress $proxyBypassList
    ```

3. A lemez a SAN-szabályzat beállítása [Onlineall](https://technet.microsoft.com/library/gg252636.aspx):
   
    ```PowerShell
    diskpart 
    ```
    A megnyitott parancssori ablakban írja be a következő parancsokat:

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Egyezményes világidő (UTC) idő beállítása a Windows és a Windows idő (w32time) szolgáltatás indítási típusának **automatikusan**:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -name "RealTimeIsUniversal" -Value 1 -Type DWord -force

    Set-Service -Name w32time -StartupType Automatic
    ```
5. A kiemelt profil beállítása a **nagy teljesítményű**:

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```
6. Győződjön meg arról, hogy a környezeti változók **TEMP** és **TMP** az alapértelmezett értékekre vannak beállítva:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TEMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -name "TMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -force
    ```

## <a name="check-the-windows-services"></a>Ellenőrizze a Windows-szolgáltatások
Győződjön meg arról, hogy a következő Windows-szolgáltatások mindegyike értékre van állítva, a **Windows alapértelmezett értékek**. Ezek a szolgáltatások, amelyek kell beállítani, győződjön meg arról, hogy a virtuális gép kapcsolódik, minimális számát. Az indítási beállítások alaphelyzetbe állítása, futtassa a következő parancsokat:
   
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

## <a name="update-remote-desktop-registry-settings"></a>A távoli asztal beállításjegyzék-beállítások frissítése
Győződjön meg arról, hogy a következő beállításokkal megfelelően van konfigurálva a távoli asztali kapcsolat:

>[!Note] 
>Egy hibaüzenet jelenhet meg, amikor futtatja a **Set-ItemProperty-elérési út "HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal szolgáltatások – neve &lt;objektumnév&gt; -érték &lt;érték&gt;**  az alábbi lépéseket. A hibaüzenet biztonságosan figyelmen kívül hagyhatja. Ez azt jelenti, hogy csak, hogy a tartományban van nem küld ez a konfiguráció segítségével a csoportházirend-objektum.
>
>

1. Engedélyezve van a távoli asztal protokoll (RDP):
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0 -Type DWord -force

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDenyTSConnections" -Value 0 -Type DWord -force
    ```
   
2. Az RDP-portra helyesen van beállítva (alapértelmezés szerint a 3389-es port):
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "PortNumber" -Value 3389 -Type DWord -force
    ```
    Amikor telepít egy virtuális Gépet, szemben a 3389-es port az alapértelmezett szabályok jönnek létre. Ha azt szeretné, hogy módosítsa a portszámot, ez nem az Azure-ban a virtuális gép üzembe helyezése után.

3. A figyelő minden hálózati felületen figyeli.
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "LanAdapter" -Value 0 -Type DWord -force
   ```
4. A hálózati szintű hitelesítés módját, az RDP-kapcsolatok konfigurálása:
   
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
8. Ha önaláírt tanúsítványokat az RDP-figyelő kötve van, távolítsa el őket:
    
    ```PowerShell
    Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SSLCertificateSHA1Hash" -force
    ```
    Ez a győződjön meg arról, hogy csatlakozott az elején, a virtuális gép telepítésekor. Emellett áttekintheti Ez az egy későbbi szakaszban a virtuális gép üzembe helyezése az Azure-ban, szükség esetén után.

9. Ha a virtuális gép lesz része egy tartománynak, ellenőrizze a következő beállításokat, győződjön meg arról, hogy a korábbi beállításokat nem állítja. A házirendekben, amelyek ellenőrizni kell a következők:
    
    | Cél                                     | Szabályzat                                                                                                                                                       | Érték                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | Az RDP engedélyezve van                           | Computer Configuration\Policies\Windows Settings\Administrative Templates\Components\Remote Desktop Services\Remote Desktop Session Host\Connections         | Lehetővé teszi a felhasználók távolról kapcsolódni a távoli asztal használatával                                  |
    | NLA csoportházirend                         | Settings\Administrative Templates\Components\Remote Desktop Services\Remote Desktop Session Host\Security                                                    | Felhasználói hitelesítés szükséges a távoli kapcsolatokhoz hálózati szintű hitelesítéssel |
    | Élő beállítások megtartása                      | Computer Configuration\Policies\Windows Settings\Administrative Templates\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections | Kapcsolat életben tartási intervallum                                                 |
    | Újracsatlakozás a beállítások                       | Computer Configuration\Policies\Windows Settings\Administrative Templates\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections | Az automatikus újracsatlakozás                                                                   |
    | Beállítások kapcsolatok számának korlátozása | Computer Configuration\Policies\Windows Settings\Administrative Templates\Windows Components\Remote Desktop Services\Remote Desktop Session Host\Connections | A kapcsolatok száma                                                              |

## <a name="configure-windows-firewall-rules"></a>Windows tűzfal-szabályok konfigurálása
1. A Windows tűzfal bekapcsolása a három profilt (a tartományhoz, a Standard és a nyilvános):

   ```PowerShell
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
   ```

2. Futtassa a következő parancsot a PowerShell WinRM engedélyezéséhez a három tűzfalprofil (tartományi, privát és nyilvános) keresztül, és a PowerShell távelérési szolgáltatásának engedélyezése:
   
   ```PowerShell
    Enable-PSRemoting -force

    Set-NetFirewallRule -DisplayName "Windows Remote Management (HTTP-In)" -Enabled True
   ```
3. A következő tűzfalszabályokat, hogy az RDP-forgalmat engedélyezi:

   ```PowerShell
    Set-NetFirewallRule -DisplayGroup "Remote Desktop" -Enabled True
   ```   
4. A fájl- és nyomtatómegosztás szabály engedélyezése, úgy, hogy a virtuális gép is válaszol a ping parancsra, a virtuális hálózaton belül:

   ```PowerShell
   Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Enabled True
   ``` 
5. Ha a virtuális gép lesz része egy tartománynak, ellenőrizze a következő beállításokat, győződjön meg arról, hogy a korábbi beállításokat nem állítja. Az AD-szabályzatokat, amelyek a ellenőrizni kell a következők:

    | Cél                                 | Szabályzat                                                                                                                                                  | Érték                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | A Windows tűzfal-profilok engedélyezése | Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows Firewall   | Az összes hálózati kapcsolatok védelme         |
    | Engedélyezze az RDP-                           | Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows Firewall   | A bejövő távoli asztali Kivételek tiltása |
    |                                      | Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Standard Profile\Windows Firewall | A bejövő távoli asztali Kivételek tiltása |
    | Engedélyezze az ICMP-V4                       | Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows Firewall   | Az ICMP-kivételek engedélyezése                   |
    |                                      | Computer Configuration\Policies\Windows Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Standard Profile\Windows Firewall | Az ICMP-kivételek engedélyezése                   |

## <a name="verify-vm-is-healthy-secure-and-accessible-with-rdp"></a>Ellenőrizze a virtuális gép a megfelelő, biztonságos és RDP-vel elérhető 
1. Ahhoz, hogy a lemez nincs kifogástalan állapotú és egységes, futtassa a következő virtuális gép újraindítása egy ellenőrzési lemez művelet:

    ```PowerShell
    Chkdsk /f
    ```
    Győződjön meg arról, hogy a jelentés megjeleníti egy letisztult és kifogástalan állapotú lemezt.

2. A rendszerindítási konfigurációs adatok (BCD) beállításainak megadása. 

    > [!Note]
    > Ellenőrizze, hogy futtassa a következő parancsokat egy emelt szintű PowerShell-ablakban a.
   
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
    # Setup the Guest OS to collect a kernel dump on an OS crash event
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name CrashDumpEnabled -Type DWord -force -Value 2
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name DumpFile -Type ExpandString -force -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name NMICrashDump -Type DWord -force -Value 1

    #Setup the Guest OS to collect user mode dumps on a service crash event
    $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
    New-ItemProperty -Path $key -name DumpFolder -Type ExpandString -force -Value "c:\CrashDumps"
    New-ItemProperty -Path $key -name CrashCount -Type DWord -force -Value 10
    New-ItemProperty -Path $key -name DumpType -Type DWord -force -Value 2
    Set-Service -Name WerSvc -StartupType Manual
    ```
4. Ellenőrizze, hogy a Windows Management Instrumentations tárház egységes. Ennek végrehajtásához futtassa a következő parancsot:

    ```PowerShell
    winmgmt /verifyrepository
    ```
    Ha a tárház sérült, [WMI: Tárház sérülése, vagy nem](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

5. Győződjön meg arról, hogy minden más alkalmazás nem használja a 3389-es portot. Ezt a portot használja az RDP-szolgáltatás az Azure-ban. Futtathat **netstat - anob** megtekintéséhez, hogy mely portokon a szolgálnak a virtuális gépen:

    ```PowerShell
    netstat -anob
    ```

6. Ha a feltölteni kívánt Windows virtuális Merevlemezt egy tartományvezérlőt, majd kövesse az alábbi lépéseket:

    1. Hajtsa végre a [további lépések](https://support.microsoft.com/kb/2904015) készíti elő a lemezen.

    1. Győződjön meg arról, hogy ismeri a DSRM-jelszót, abban az esetben el kell indítani a virtuális gép a címtárszolgáltatások Helyreállító módjában időpontban induljanak. Érdemes ezen a hivatkozáson beállításához tekintse meg a [DSRM-jelszót](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx).

7. Győződjön meg arról, hogy a beépített Rendszergazda fiók és jelszó is ismertek. Előfordulhat, hogy szeretné a jelenlegi helyi rendszergazdai jelszó visszaállítása, és győződjön meg arról, hogy ezt a fiókot használhatja bejelentkezni a Windows az RDP-kapcsolaton keresztül. A hozzáférési engedélyt a "Bejelentkezés engedélyezése a távoli asztali szolgáltatások használatával" csoportházirend-objektumot határozza meg. Ez az objektum a a Helyicsoportházirend-szerkesztő alatt tekintheti meg:

    Számítógép konfigurációja\A Windows beállításai\Biztonsági beállítások\Helyi házirend\Felhasználói jogok kiosztása

8. Ellenőrizze az alábbi AD győződjön meg arról, hogy nem blokkolja az RDP-hozzáférés RDP-Kapcsolaton keresztül, és nem is a hálózat szabályzatokkal:

    - Számítógép konfigurációja\A Windows beállításai\Biztonsági beállítások\Helyi házirend\Felhasználói jogok kiosztása\kötegelt munka bejelentkezésének megtagadása hozzáférést ehhez a számítógéphez a hálózatról

    - Számítógép konfigurációja\A Windows beállításai\Biztonsági beállítások\Helyi házirend\Felhasználói jogok kiosztása\helyi bejelentkezés megtagadása a távoli asztali szolgáltatások használatával


9. A következő AD házirendben győződjön meg arról, hogy nem távolítja el az alábbi, a hozzáférési fiókok:

   - Számítógép konfigurációja\A Windows beállításai\Biztonsági beállítások\Helyi házirend\Felhasználói jogok Assignment\Access a számítási, hálózati

     A következő csoportok szerepelnie kell a szabályzatra vonatkozó:

   - Rendszergazdák
   - Biztonságimásolat-felelősök
   - Mindenki
   - Felhasználók

10. Indítsa újra a virtuális Gépen, győződjön meg arról, hogy Windows továbbra is megfelelő, elérhető, az RDP-kapcsolat használatával. Ezen a ponton érdemes a helyi Hyper-V – győződjön meg róla a virtuális gép teljesen elindult-e, és ellenőrizze, hogy RDP érhető el a virtuális gép létrehozásához.

11. Távolítsa el a felesleges átviteli Driver Interface szűrőket, például egy szoftver, amely elemzi a TCP csomagokat, vagy további tűzfalakat. Emellett áttekintheti Ez az egy későbbi szakaszban a virtuális gép üzembe helyezése az Azure-ban, szükség esetén után.

12. Távolítsa el a harmadik féltől származó szoftverek és illesztőprogramot, amely kapcsolódik a fizikai összetevők vagy más virtualizációs technológiát.

### <a name="install-windows-updates"></a>Windows-frissítések telepítése
Az ideális konfiguráció **a javítási szintje a gép legkésőbb**. Ha ez nem lehetséges, győződjön meg arról, hogy telepítve vannak-e a következő frissítéseket:

| Összetevő               | Bináris         | Windows 7 SP1, Windows Server 2008 R2 SP1 | A Windows 8, Windows Server 2012-ben               | Windows 8.1, Windows Server 2012 R2 rendszerben | A Windows 10 1607-es verzió a Windows Server 2016-ban 1607-es verzió | Windows 10, 1703-as verzió    | A Windows 10 1709-es Windows Server 2016-ban 1709-es | A Windows 10 1803 Windows Server 2016 verziót 1803-as verzióban |
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
| Mag                    | ntoskrnl.exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.483             | -                                               | -                                               |
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
       
### A sysprep használata <a id="step23"></a>    

A Sysprep olyan folyamat, futtathatja azokat a windows-telepítés, amely alaphelyzetbe állítja a rendszer a telepítés és a egy "a kezdőélmény" személyes adatokat, és alaphelyzetbe állítása több összetevőből biztosít. Általában ezt megteheti, ha szeretne létrehozni egy sablont, amelyről telepíthet több más virtuális gépeket, amelyek adott konfigurációval rendelkezik. Ezt nevezzük a **rendszerkép általánosítva**.

Ha ehelyett szeretné csak egy virtuális gép létrehozása egy lemezről, nem kell a Sysprep eszközt használja. Ilyen esetben egyszerűen létrehozhatja a virtuális gép az úgynevezett egy **specializált rendszerkép**.

A virtuális gép létrehozása specializált lemezből kapcsolatos további információkért lásd:

- [Virtuális gép létrehozása specializált lemezből](create-vm-specialized.md)
- [Virtuális gép létrehozása speciális lemezről a virtuális merevlemez](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master)

Ha szeretne létrehozni egy általános rendszerképpel, meg kell futtassa a Sysprep eszközt. További információ a Sysprep: [Sysprep használata: Bevezetés](https://technet.microsoft.com/library/bb457073.aspx). 

Ez az általánosítás támogatja a nem minden szerepkör vagy alkalmazást, amely egy Windows-alapú számítógépre van telepítve. Mielőtt elkezdi a műveletet, ezért tekintse meg a következő cikket, győződjön meg arról, hogy a szerepkört az adott számítógép támogatja a sysprep. További információ [kiszolgálói szerepkörök támogatása a Sysprep](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

### <a name="steps-to-generalize-a-vhd"></a>Virtuális merevlemez általánosításához lépések

>[!NOTE]
> Az alábbi lépéseket a sysprep.exe futtatása után kapcsolja ki a virtuális Gépet, és nem kapcsolja be újra, amíg létre lemezképet, az abból az Azure-ban.

1. Jelentkezzen be a Windows virtuális gép.
2. Futtatás **parancssor** rendszergazdaként. 
3. Módosítsa a könyvtárat: **%windir%\system32\sysprep**, majd futtassa a **sysprep.exe**.
3. A **Rendszer-előkészítő eszköz** párbeszédpanelen válassza **A kezdőélmény indítása** lehetőséget, és győződjön meg róla, hogy be van-e jelölve az **Általánosítás** jelölőnégyzet.

    ![Rendszer-előkészítő eszköz](media/prepare-for-upload-vhd-image/syspre.png)
4. A **leállítási beállítások**válassza **leállítási**.
5. Kattintson az **OK** gombra.
6. A Sysprep befejezését követően a virtuális gép leállítására. Ne használjon **indítsa újra a** a virtuális gép leállítása.
7. A VHD-t most már elkészült tölthető fel. A virtuális gép létrehozása általánosított lemezről kapcsolatos további információkért lásd: [általános VHD feltöltése és ezzel hozzon létre egy új virtuális gépeket az Azure-ban](sa-upload-generalized.md).


>[!NOTE]
> Egy egyéni unattend.xml nem támogatott. Támogatjuk a additionalUnattendContent tulajdonság, miközben, amely csak korlátozott támogatást biztosít hozzáadása [microsoft-windows-rendszerhéj – telepítő](https://docs.microsoft.com/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) be az Azure a kiépítési ügynök által az unattend.xml beállítások. Például  használhatnak [additionalUnattendContent](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent?view=azure-dotnet) FirstLogonCommands és LogonCommands adhat hozzá. Lásd még: [additionalUnattendContent FirstLogonCommands példa](https://github.com/Azure/azure-quickstart-templates/issues/1407).


## <a name="complete-recommended-configurations"></a>Ajánlott beállításait
A következő beállítások nem befolyásolják a VHD feltöltésével. Azonban erősen ajánlott, hogy konfigurálta azokat.

* Telepítse a [Azure virtuális gépek ügynök](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Ezután engedélyezheti a Virtuálisgép-bővítmények. A Virtuálisgép-bővítmények megvalósítása a legtöbb kritikus funkciója, amely előfordulhat, hogy kíván használni a virtuális gépek új jelszavak, például az RDP konfigurálása, és így tovább. További információkért lásd: [Azure-beli virtuálisgép-ügynök – áttekintés](../extensions/agent-windows.md).
* Miután a virtuális gép létrehozása az Azure-ban, azt javasoljuk, hogy a teljesítmény javítása a "Historikus elnevezésű" köteten helyezze a lapozófájl méretét. Beállíthatja a következőképpen:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -name "PagingFiles" -Value "D:\pagefile.sys" -Type MultiString -force
   ```
  Ha a virtuális Géphez csatolt adatlemezre, az ideiglenes meghajtó kötet meghajtójának betűjelét általában-e "D" A kijelölés és a beállításokat, hogy az elérhető meghajtók száma függően eltérő lehet.

## <a name="next-steps"></a>További lépések
* [Windows Virtuálisgép-Rendszerkép feltöltése az Azure-bA a Resource Manager üzembe helyezések](upload-generalized-managed.md)
* [Azure Windows virtuális gép aktiválással kapcsolatos problémák elhárítása](troubleshoot-activation-problems.md)

