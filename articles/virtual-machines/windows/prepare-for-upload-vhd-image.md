---
title: Windows vHD előkészítése az Azure-ba való feltöltéshez
description: Megtudhatja, hogy miként készíthet elő Windows VHD vagy VHDX rendszert az Azure-ba való feltöltéshez
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
ms.openlocfilehash: 719a1985aeb0db7b0cf7f55a10762bf3ebb3e045
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250191"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Windows rendszerű VHD vagy VHDX előkészítése az Azure-ba való feltöltéshez

Mielőtt feltölt egy Windows virtuális gépet (VM) a helyszíni Azure-ba, elő kell készítenie a virtuális merevlemezt (VHD vagy VHDX). Az Azure támogatja mind az 1. A virtuális merevlemez maximális mérete 1023 GB. 

Az 1. generációs virtuális gépben a VHDX fájlrendszert virtuális merevlemezre konvertálhatja. A dinamikusan bővülő lemezt rögzített méretű lemezzé is konvertálhatja. De nem módosíthatja a virtuális gép generációját. További információ: [Azure support for generation 2 VMs (preview)](generation-2.md) [Hozzon létre egy 1.](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

Az Azure virtuális gépek támogatási szabályzatáról a [Microsoft server szoftvertámogatási szolgáltatása az Azure virtuális gépekhez](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)című témakörben talál.

> [!NOTE]
> Az ebben a cikkben található utasítások a következőkre vonatkoznak:
>1. A Windows Server 2008 R2 és újabb Windows Server operációs rendszerek 64 bites verziója. A 32 bites operációs rendszer Azure-beli futtatásáról az [Azure-beli virtuális gépek 32 bites operációs rendszereinek támogatása](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines)című témakörben talál további információt.
>2. Ha bármelyik vész-helyreállítási eszköz lesz használva a számítási feladatok áttelepítéséhez, például az Azure Site Recovery vagy az Azure Migrate, ezt a folyamatot továbbra is el kell végezni, és a vendég operációs rendszeren kell követni a rendszerkép előkészítése az áttelepítés előtt.

## <a name="system-file-checker-sfc-command"></a>Rendszerfájl-ellenőrző (SFC) parancs

### <a name="run-windows-system-file-checker-utility-run-sfc-scannow-on-os-prior-to-generalization-step-of-creating-customer-os-image"></a>A Windows rendszerfájl-ellenőrző segédprogram futtatása (sfc /scannow futtatása) operációs rendszeren az ügyfél operációsrendszer-lemezkép létrehozásának általánosítási lépése előtt

A Rendszerfájl-ellenőrző (SFC) parancs a Windows rendszerfájlok ellenőrzésére és cseréjére szolgál.

Az SFC parancs futtatása:

1. Nyisson meg egy emelt szintű CMD-kérdést rendszergazdaként.
1. Írja `sfc /scannow` be a Szöveget, és válassza **az Enter**lehetőséget.

    ![Rendszerfájl-ellenőrző](media/prepare-for-upload-vhd-image/system-file-checker.png)


Az SFC-vizsgálat befejezése után próbálja meg telepíteni a Windows-frissítéseket, és indítsa újra a számítógépet.

## <a name="convert-the-virtual-disk-to-a-fixed-size-and-to-vhd"></a>A virtuális lemez konvertálása rögzített méretre és virtuális merevlemezre

Ha a virtuális lemezt az Azure-hoz szükséges formátumra kell konvertálnia, használja az ebben a szakaszban ismertethető módszerek egyikét:

1. A virtuális lemez konvertálási folyamatának futtatása előtt biztonsági másolatot készíteni a virtuális gépről.

1. Ellenőrizze, hogy a Windows virtuális merevlemez megfelelően működik-e a helyi kiszolgálón. A virtuális gépen belüli hibákat, mielőtt megpróbálja konvertálni vagy feltölteni az Azure-ba.

1. A Virtuális merevlemez méretét illetően:

   1. Az Azure-beli virtuális hálózat minden virtuális d-jének 1 MB-hoz igazított virtuális mérettel kell rendelkeznie. Nyers lemezről virtuális merevlemezre történő konvertáláskor győződjön meg arról, hogy a nyers lemez mérete a konvertálás előtt 1 MB többszöröse. A megabájtok törtrészei hibákat okoznak a feltöltött virtuális merevlemezről származó képek létrehozásakor.

   2. Az operációs rendszer virtuális merevlemezének maximális mérete 2 TB.


A lemez konvertálása után hozzon létre egy virtuális gép, amely a lemezt használja. Indítsa el, és jelentkezzen be a virtuális gépre a feltöltéselőkészítésének befejezéséhez.

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>A lemez konvertálása a Hyper-V Manager segítségével 
1. Nyissa meg a Hyper-V Kezelőt, és válassza ki a helyi számítógépet a bal oldalon. A számítógéplista feletti menüben válassza a > **Műveletszerkesztési lemez lehetőséget**. **Action**
2. A **Virtuális merevlemez megkeresése** lapon jelölje ki a virtuális lemezt.
3. A **Művelet kiválasztása** lapon válassza a**Tovább** **konvertálás** > lehetőséget.
4. Ha a VHDX-ről kell konvertálnia, válassza a **VHD** > **Next lehetőséget.**
5. Ha dinamikusan bővülő lemezről szeretne konvertálni, válassza a **Rögzített méret** > **Ezután**lehetőséget.
6. Keresse meg és jelölje ki azt az elérési utat, amelybe az új VHD-fájlt menteni szeretné.
7. Válassza a **Finish** (Befejezés) elemet.

> [!NOTE]
> A cikkben található parancsok futtatásához használjon emelt szintű PowerShell-munkamenetet.

### <a name="use-powershell-to-convert-the-disk"></a>A lemez konvertálása a PowerShell használatával 
Virtuális lemez konvertálható a Windows PowerShell [Konvertálás-VHD](https://technet.microsoft.com/library/hh848454.aspx) paranccsal. Válassza a **Futtatás rendszergazdaként** lehetőséget a PowerShell indításakor. 

A következő példa parancs átalakítja a lemezt VHDX-ről VHD-re. A parancs a lemezt dinamikusan bővülő lemezről rögzített méretű lemezzé is konvertálja.

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

Ebben a parancsban cserélje `-Path` le az értéket a konvertálni kívánt virtuális merevlemez elérési útjának. Cserélje le `-DestinationPath` az értéket az átalakított lemez új elérési parancsára és nevére.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Konvertálás VMware VMDK lemezformátumból
Ha [a VMDK fájlformátumú](https://en.wikipedia.org/wiki/VMDK)Windows VM-lemezképet használja, a [Microsoft virtual machine converter](https://www.microsoft.com/download/details.aspx?id=42497) segítségével konvertálja azt VHD formátumba. További információ: [VMware VMDK konvertálása Hyper-V VHD felbontássá.](https://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx)

## <a name="set-windows-configurations-for-azure"></a>Windows-konfigurációk beállítása az Azure-hoz

> [!NOTE]
> Az Azure platform iSO-fájlt csatlakoztat a DVD-ROM-hoz, ha egy általános rendszerképből windowsos virtuális gép jön létre.
> Ezért a DVD-ROM-ot engedélyezni kell az operációs rendszerben az általános lemezképben. Ha le van tiltva, a Windows virtuális gép beragadt a kezdőélmény.If it is disabled, the Windows VM will be stuck at OOBE.

Az Azure-ba feltölteni kívánt virtuális gépen futtassa a következő parancsokat egy [rendszergazda jogú parancssorablakból:](https://technet.microsoft.com/library/cc947813.aspx)

1. Az útválasztási táblában lévő statikus állandó útvonal eltávolítása:
   
   * Az útvonaltábla megtekintéséhez `route print` futtassa a parancssorból.
   * Nézd `Persistence Routes` meg a részeket. Ha van állandó útvonal, a `route delete` paranccsal távolíthatja el.
2. A WinHTTP-proxy eltávolítása:
   
    ```PowerShell
    netsh winhttp reset proxy
    ```

    Ha a virtuális gépnek egy adott proxyval kell dolgoznia, adjon hozzá egy proxykivételt az Azure IP-címhez ([168.63.129.16](https://blogs.msdn.microsoft.com/mast/2015/05/18/what-is-the-ip-address-168-63-129-16/
)), hogy a virtuális gép csatlakozhasson az Azure-hoz:
    ```
    $proxyAddress="<your proxy server>"
    $proxyBypassList="<your list of bypasses>;168.63.129.16"

    netsh winhttp set proxy $proxyAddress $proxyBypassList
    ```

3. Állítsa a lemez [`Onlineall`](https://technet.microsoft.com/library/gg252636.aspx)SAN-házirendet a következőre:
   
    ```PowerShell
    diskpart 
    ```
    A megnyitott parancssorablakba írja be a következő parancsokat:

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Állítsa be az egyezményes világidő (UTC) idejét a Windows rendszerhez. Állítsa be a Windows időszolgáltatás`w32time`( `Automatic`) indítási típusát is a következőre:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -Name "RealTimeIsUniversal" -Value 1 -Type DWord -Force

    Set-Service -Name w32time -StartupType Automatic
    ```
5. Állítsa a teljesítményprofilt nagy teljesítményre:

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```
6. Győződjön meg arról, hogy a környezeti változók `TEMP` és `TMP` az alapértelmezett értékek:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name "TEMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name "TMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
    ```

## <a name="check-the-windows-services"></a>Windows-szolgáltatások ellenőrzése
Győződjön meg arról, hogy az alábbi Windows-szolgáltatások mindegyike a Windows alapértelmezett értékeire van állítva. Ezek a szolgáltatások a virtuális gép kapcsolatának biztosításához beállítható minimális szolgáltatások. Az indítási beállítások alaphelyzetbe állításához futtassa a következő parancsokat:
   
```PowerShell
Get-Service -Name bfe | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name dhcp | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name dnscache | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name IKEEXT | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name iphlpsvc | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name netlogon | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name netman | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name nsi | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name TermService | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name MpsSvc | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name RemoteRegistry | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
```
## <a name="update-remote-desktop-registry-settings"></a>Távoli asztali beállításjegyzék-beállítások frissítése
Ellenőrizze, hogy a következő beállítások megfelelően vannak-e konfigurálva a táveléréshez:

>[!NOTE] 
>Futtatásakor `Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -Name <object name> -Value <value>`hibaüzenet jelenhet meg. Az üzenet biztonságosan figyelmen kívül hagyható. Ez csak azt jelenti, hogy a tartomány nem egy csoportházirend-objektumon keresztül tolja le a konfigurációt.

1. A Távoli asztali protokoll (RDP) engedélyezve van:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -Name "fDenyTSConnections" -Value 0 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "fDenyTSConnections" -Value 0 -Type DWord -Force
    ```
   
2. Az RDP-port megfelelően van beállítva. Az alapértelmezett port a 3389:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "PortNumber" -Value 3389 -Type DWord -Force
    ```
    Virtuális gép telepítésekor az alapértelmezett szabályok a 3389-es porton keresztül jönnek létre. Ha módosítani szeretné a port számát, tegye ezt a virtuális gép Azure-beli üzembe helyezése után.

3. A figyelő minden hálózati csatolón figyel:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "LanAdapter" -Value 0 -Type DWord -Force
   ```
4. Konfigurálja az RDP-kapcsolatok hálózati szintű hitelesítési (NLA) módját:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "UserAuthentication" -Value 1 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "SecurityLayer" -Value 1 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "fAllowSecProtocolNegotiation" -Value 1 -Type DWord -Force
     ```

5. Állítsa be az életben tartás értékét:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "KeepAliveEnable" -Value 1  -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "KeepAliveInterval" -Value 1  -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "KeepAliveTimeout" -Value 1 -Type DWord -Force
    ```
6. Újra:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "fDisableAutoReconnect" -Value 0 -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "fInheritReconnectSame" -Value 1 -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "fReconnectSame" -Value 0 -Type DWord -Force
    ```
7. Az egyidejű kapcsolatok számának korlátozása:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "MaxInstanceCount" -Value 4294967295 -Type DWord -Force
    ```
8. Távolítsa el az RDP-figyelőhöz kötött önaláírt tanúsítványokat:
    
    ```PowerShell
    if ((Get-Item -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp').Property -contains "SSLCertificateSHA1Hash")
    {
        Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "SSLCertificateSHA1Hash" -Force
    }
    ```
    Ez a kód biztosítja, hogy a virtuális gép telepítésekor az elején csatlakozhat. Ha ezt később át kell tekintenie, ezt a virtuális gép Azure-beli üzembe helyezése után teheti meg.

9. Ha a virtuális gép egy tartomány része lesz, ellenőrizze az alábbi szabályzatokat, hogy a korábbi beállítások nem állnak vissza. 
    
    | Cél                                     | Szabályzat                                                                                                                                                       | Érték                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | Az RDP engedélyezve van                           | Számítógép konfigurációja\Házirendek\Windows-beállítások\Felügyeleti sablonok\Összetevők\Távoli asztali szolgáltatások\Távoli asztali munkamenetgazda\Kapcsolatok         | A távoli csatlakozás engedélyezése a felhasználók nak a Távoli asztal használatával                                  |
    | NLA-csoportházirend                         | Beállítások\Felügyeleti sablonok\Összetevők\Távoli asztali szolgáltatások\Távoli asztali munkamenetgazda\Biztonság                                                    | Felhasználói hitelesítés megkövetelése a táveléréshez az NLA használatával |
    | Életben tartás beállításai                      | Számítógép konfigurációja\Házirendek\Windows-beállítások\Felügyeleti sablonok\Windows-összetevők\Távoli asztali szolgáltatások\Távoli asztali munkamenetgazda\Kapcsolatok | Az életben tartás kapcsolati időköz beállítása                                                 |
    | Beállítások újracsatlakoztatása                       | Számítógép konfigurációja\Házirendek\Windows-beállítások\Felügyeleti sablonok\Windows-összetevők\Távoli asztali szolgáltatások\Távoli asztali munkamenetgazda\Kapcsolatok | Automatikus újracsatlakozás                                                                   |
    | Korlátozott számú csatlakozási beállítás | Számítógép konfigurációja\Házirendek\Windows-beállítások\Felügyeleti sablonok\Windows-összetevők\Távoli asztali szolgáltatások\Távoli asztali munkamenetgazda\Kapcsolatok | Kapcsolatok számának korlátozása                                                              |

## <a name="configure-windows-firewall-rules"></a>A Windows tűzfal szabályainak konfigurálása
1. A Windows tűzfal bekapcsolása a három profilon (tartomány, normál és nyilvános):

   ```PowerShell
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
   ```

2. Futtassa a következő parancsot a PowerShellben a WinRM három tűzfalprofilon (tartományon, magán- és nyilvános) keresztül történő engedélyezéséhez, és engedélyezze a PowerShell távoli szolgáltatást:
   
   ```PowerShell
    Enable-PSRemoting -Force

    Set-NetFirewallRule -DisplayName "Windows Remote Management (HTTP-In)" -Enabled True
   ```
3. Engedélyezze a következő tűzfalszabályokat az RDP-forgalom engedélyezéséhez:

   ```PowerShell
    Set-NetFirewallRule -DisplayGroup "Remote Desktop" -Enabled True
   ```   
4. Engedélyezze a fájl- és nyomtatómegosztásszabályát, hogy a virtuális gép válaszolhasson a virtuális hálózaton belüli ping parancsra:

   ```PowerShell
   Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Enabled True
   ``` 
5. Hozzon létre egy szabályt az Azure platformhálózathoz:

   ```PowerShell
    New-NetFirewallRule -DisplayName "AzurePlatform" -Direction Inbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow -EdgeTraversalPolicy Allow
    New-NetFirewallRule -DisplayName "AzurePlatform" -Direction Outbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow
   ``` 
6. Ha a virtuális gép egy tartomány része lesz, ellenőrizze a következő Azure AD-szabályzatok győződjön meg arról, hogy a korábbi beállítások nem állnak vissza. 

    | Cél                                 | Szabályzat                                                                                                                                                  | Érték                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | A Windows tűzfal-profilok engedélyezése | Számítógép konfigurációja\Házirendek\Windows-beállítások\Felügyeleti sablonok\Hálózat\Hálózati kapcsolat\Windows tűzfal\Tartományprofil\Windows tűzfal   | Az összes hálózati kapcsolat védelme         |
    | RdP engedélyezése                           | Számítógép konfigurációja\Házirendek\Windows-beállítások\Felügyeleti sablonok\Hálózat\Hálózati kapcsolat\Windows tűzfal\Tartományprofil\Windows tűzfal   | Bejövő Távoli asztal kivételek engedélyezése |
    |                                      | Számítógép konfigurációja\Házirendek\Windows-beállítások\Felügyeleti sablonok\Hálózat\Hálózati kapcsolat\Windows tűzfal\Szabványos profil\Windows tűzfal | Bejövő Távoli asztal kivételek engedélyezése |
    | ICMP-V4 engedélyezése                       | Számítógép konfigurációja\Házirendek\Windows-beállítások\Felügyeleti sablonok\Hálózat\Hálózati kapcsolat\Windows tűzfal\Tartományprofil\Windows tűzfal   | ICMP-kivételek engedélyezése                   |
    |                                      | Számítógép konfigurációja\Házirendek\Windows-beállítások\Felügyeleti sablonok\Hálózat\Hálózati kapcsolat\Windows tűzfal\Szabványos profil\Windows tűzfal | ICMP-kivételek engedélyezése                   |

## <a name="verify-the-vm"></a>A virtuális gép ellenőrzése 

Győződjön meg arról, hogy a virtuális gép kifogástalan, biztonságos és RDP-szolgáltatással érhető el: 

1. Annak érdekében, hogy a lemez kifogástalan és konzisztens legyen, ellenőrizze a lemezt a következő virtuális gép újraindításakor:

    ```PowerShell
    Chkdsk /f
    ```
    Győződjön meg arról, hogy a jelentés tiszta és kifogástalan lemezt jelenít meg.

2. A rendszerindítási konfigurációs adatok (BCD) beállításainak megadása. 

    > [!NOTE]
    > A parancsok futtatásához használjon emelt szintű PowerShell-ablakot.
   
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
3. A memóriaképnapló hasznos lehet a Windows összeomlási problémáinak elhárításában. A memóriaképnapló-gyűjtemény engedélyezése:

    ```powershell
    # Set up the guest OS to collect a kernel dump on an OS crash event
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name CrashDumpEnabled -Type DWord -Force -Value 2
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name DumpFile -Type ExpandString -Force -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name NMICrashDump -Type DWord -Force -Value 1

    # Set up the guest OS to collect user mode dumps on a service crash event
    $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
    New-ItemProperty -Path $key -Name DumpFolder -Type ExpandString -Force -Value "c:\CrashDumps"
    New-ItemProperty -Path $key -Name CrashCount -Type DWord -Force -Value 10
    New-ItemProperty -Path $key -Name DumpType -Type DWord -Force -Value 2
    Set-Service -Name WerSvc -StartupType Manual
    ```
4. Ellenőrizze, hogy a Windows Management Instrumentation (WMI) adattárka konzisztens-e:

    ```PowerShell
    winmgmt /verifyrepository
    ```
    Ha a tárház sérült, olvassa el a [WMI: Repository sérülése vagy nem](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not)című témakört.

5. Győződjön meg arról, hogy más alkalmazás nem használja a 3389-es portot. Ez a port az Azure-ban az RDP szolgáltatáshoz használatos. A virtuális gépen használt portok `netstat -anob`megtekintéséhez futtassa a következőt:

    ```PowerShell
    netstat -anob
    ```

6. Tartományvezérlőként lévő Windows virtuális merevlemez feltöltése:

   * A lemez előkészítéséhez kövesse az [alábbi további lépéseket.](https://support.microsoft.com/kb/2904015)

   * Győződjön meg róla, hogy ismeri a Címtárszolgáltatások visszaállítási mód (DSRM) jelszavát abban az esetben, ha valamikor el kell indítania a virtuális szolgáltatást a DSRM-ben. További információt a [DSRM-jelszó beállítása](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx)című témakörben talál.

7. Győződjön meg arról, hogy ismeri a beépített rendszergazdai fiókot és jelszót. Előfordulhat, hogy alaphelyzetbe szeretné állítani az aktuális helyi rendszergazdai jelszót, és ezzel a fiókkal is be tud jelentkezni a Windows rendszerbe az RDP-kapcsolaton keresztül. Ezt a hozzáférési engedélyt a "Bejelentkezés engedélyezése a Távoli asztali szolgáltatásokon keresztül" csoportházirend-objektum szabályozza. Az objektum megtekintése a Helyi csoportházirend-szerkesztőben itt:

    Számítógép konfigurációja\Windows beállításai\Biztonsági beállítások\Helyi házirendek\Felhasználói jogok hozzárendelése

8. Ellenőrizze az alábbi Azure AD-szabályzatokat, és győződjön meg arról, hogy nem blokkolja az RDP-hozzáférést az RDP-n vagy a hálózaton keresztül:

    - Számítógép konfigurációja\Windows beállításai\Biztonsági beállítások\Helyi házirendek\Felhasználói jogok hozzárendelése\Hozzáférés megtagadása a számítógéphez a hálózatról

    - Számítógép konfigurációja\Windows beállításai\Biztonsági beállítások\Helyi házirendek\Felhasználói jogok hozzárendelése\Bejelentkezés megtagadása a Távoli asztali szolgáltatásokon keresztül


9. Ellenőrizze a következő Azure AD-szabályzatot, és győződjön meg arról, hogy nem távolítja el a szükséges hozzáférési fiókokat:

   - Számítógép konfigurációja\Windows beállításai\Biztonsági beállítások\Helyi házirendek\Felhasználói jogok hozzárendelése\A számítógép elérése a hálózatról

   A házirendnek a következő csoportokat kell felsorolnia:

   - Rendszergazdák

   - Backup-operátorok

   - Mindenki

   - Felhasználók

10. Indítsa újra a virtuális gép, győződjön meg arról, hogy a Windows továbbra is kifogástalan állapotú, és az RDP-kapcsolaton keresztül érhető el. Ezen a ponton érdemes lehet létrehozni egy virtuális gép a helyi Hyper-V győződjön meg arról, hogy a virtuális gép teljesen elindul. Ezután ellenőrizze, hogy a virtuális gép rdp-n keresztül érhető-e el.

11. Távolítson el minden további átviteli illesztőprogram-illesztő (TDI) szűrőt. Távolítsa el például a TCP-csomagokat vagy további tűzfalakat elemző szoftvereket. Ha ezt később át kell tekintenie, ezt a virtuális gép Azure-beli üzembe helyezése után teheti meg.

12. Távolítson el minden más, külső gyártótól származó szoftvert vagy illesztőprogramot, amely fizikai összetevőkkel vagy bármely más virtualizációs technológiával kapcsolatos.

### <a name="install-windows-updates"></a>Windows-frissítések telepítése
Ideális esetben a gépet a *javítás szintjén*kell frissíteni. Ha ez nem lehetséges, ellenőrizze, hogy telepítve vannak-e a következő frissítések. A legújabb frissítéseket a Windows update előzményoldalain, a Windows 10 és a [Windows Server 2019](https://support.microsoft.com/help/4000825), [a Windows 8.1 és a Windows Server 2012 R2,](https://support.microsoft.com/help/4009470) valamint a Windows 7 SP1 és a [Windows Server 2008 R2 SP1 webhelyen kaphatja](https://support.microsoft.com/help/4009469)meg.

| Összetevő               | Bináris         | Windows 7 SP1, Windows Server 2008 R2 SP1 | Windows 8, Windows Server 2012               | Windows 8.1, Windows Server 2012 R2 | Windows 10 v1607, Windows Server 2016 v1607 | Windows 10 v1703    | Windows 10 v1709, Windows Server 2016 v1709 | Windows 10 v1803, Windows Server 2016 v1803 |
|-------------------------|----------------|-------------------------------------------|---------------------------------------------|------------------------------------|---------------------------------------------------------|----------------------------|-------------------------------------------------|-------------------------------------------------|
| Storage                 | disk.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061         | -                                                       | -                          | -                                               | -                                               |
|                         | storport.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.332             | -                                               | -                                               |
|                         | Ntfs.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Iologmsg.dll fájl   | 6.1.7601.23403 - KB3125574                | 6.2.9200.16384 - KB2995387                  | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Osztálypnp.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614         | 10.0.14393.953 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Volsnap.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384         | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | partmgr.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 - KB3000850         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | volmgr.sys     |                                           |                                             |                                    |                                                         | 10.0.15063.0               | -                                               | -                                               |
|                         | Volmgrx.sys között    | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | Msiscsi.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726         | 10.0.14393.1066 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Msdsm.sys      | 6.1.7601.23403 - KB3125574                | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726         | -                                                       | -                          | -                                               | -                                               |
|                         | Mpio.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726         | 10.0.14393.1198 - KB4022715                             | -                          | -                                               | -                                               |
|                         | vmstorfl.sys   | 6.3.9600.18907 - KB4072650                | 6.3.9600.18080 - KB3063109                  | 6.3.9600.18907 - KB4072650         | 10.0.14393.2007 - KB4345418                             | 10.0.15063.850 - KB4345419 | 10.0.16299.371 - KB4345420                      | -                                               |
|                         | Fveapi.dll     | 6.1.7601.23311 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614         | 10.0.14393.576 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Fveapibase.dll | 6.1.7601.23403 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614         | 10.0.14393.206 - KB4022715                              | -                          | -                                               | -                                               |
| Network (Hálózat)                 | netvsc.sys     | -                                         | -                                           | -                                  | 10.0.14393.1198 - KB4022715                             | 10.0.15063.250 - KB4020001 | -                                               | -                                               |
|                         | mrxsmb10.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726         | 10.0.14393.479 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb20.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | mrxsmb.sys     | 6.1.7601.23816 - KB4022722                | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | Tcpip.sys      | 6.1.7601.23761 - KB4022722                | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | http.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726         | 10.0.14393.251 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | vmswitch.sys   | 6.1.7601.23727 - KB4022719                | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.138             | -                                               | -                                               |
| Mag                    | Ntoskrnl.exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.483             | -                                               | -                                               |
| Távoli asztali szolgáltatások | rdpcorets.dll  | 6.2.9200.21506 - KB4022719                | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.0               | -                                               | -                                               |
|                         | Termsrv.dll    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 - KB3000850         | 10.0.14393.0 - KB4022715                                | 10.0.15063.0               | -                                               | -                                               |
|                         | termdd.sys     | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | win32k.sys     | 6.1.7601.23807 - KB4022719                | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726         | 10.0.14393.594 - KB4022715                              | -                          | -                                               | -                                               |
|                         | rdpdd.dll      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | rdpwd.sys      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
| Biztonság                | MS17-010       | KB4012212                                 | KB4012213                                   | KB4012213                          | KB4012606                                               | KB4012606                  | -                                               | -                                               |
|                         |                |                                           | KB4012216                                   |                                    | KB4013198                                               | KB4013198                  | -                                               | -                                               |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                          | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         |                |                                           | KB4012217                                   |                                    | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         | CVE-2018-0886  | KB4103718               | KB4103730                | KB4103725       | KB4103723                                               | KB4103731                  | KB4103727                                       | KB4103721                                       |
|                         |                | KB4103712          | KB4103726          | KB4103715|                                                         |                            |                                                 |                                                 |
       
> [!NOTE]
> A virtuális gépek kiépítése során a véletlen újraindítás elkerülése érdekében azt javasoljuk, hogy a Windows Update összes telepítése befejeződött legyen, és hogy nincsenek függőben lévő frissítések. Ennek egyik módja az összes lehetséges Windows-frissítés telepítése és újraindítása egyszer a Sysprep parancs futtatása előtt.

### <a name="determine-when-to-use-sysprep"></a>A Sysprep használatának meghatározása<a id="step23"></a>    

A Rendszerelőkészítő eszköz (Sysprep) egy olyan folyamat, amelyen futtatható a Windows-telepítés alaphelyzetbe állítása. A Sysprep "out of the box" élményt nyújt az összes személyes adat eltávolításával és több összetevő alaphelyzetbe állításával. 

A Sysprep programot általában egy olyan sablon létrehozásához futtatja, amelyből több más, adott konfigurációval rendelkező virtuális gépet is telepíthet. A sablon neve *általános kép*.

Ha csak egy virtuális gép egy lemezről szeretne létrehozni, nem kell használnia a Sysprep programot. Ehelyett létrehozhatja a virtuális gép egy *speciális rendszerképből.* A virtuális gépek speciális lemezről történő létrehozásáról a következő témakörökben talál további információt:

- [Virtuális gép létrehozása specializált lemezből](create-vm-specialized.md)
- [Virtuális gép létrehozása speciális virtuális merevlemezről](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master)

Ha általános lemezképet szeretne létrehozni, futtatnia kell a Sysprep programot. További információt a [Sysprep használata: Bevezetés](https://technet.microsoft.com/library/bb457073.aspx). 

Nem minden Windows-alapú számítógépre telepített szerepkör vagy alkalmazás támogatja az általánosított lemezképeket. Az eljárás futtatása előtt győződjön meg arról, hogy a Sysprep támogatja a számítógép szerepkörét. További információt a [Sysprep kiszolgálói szerepkörök támogatása című témakörben talál.](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

### <a name="generalize-a-vhd"></a>Virtuális merevlemez általánosítása

>[!NOTE]
> Miután `sysprep.exe` a következő lépésekben futtatta, kapcsolja ki a virtuális gép. Ne kapcsolja be újra, amíg létre nem hoz egy lemezképet az Azure-ban.

1. Jelentkezzen be a Windows virtuális gépbe.
1. **Futtassa a parancssort** rendszergazdaként. 
1. Módosítsa a `%windir%\system32\sysprep`könyvtárat . Ez után futtassa a `sysprep.exe` parancsot.
1. A **Rendszer-előkészítő eszköz** párbeszédpanelen válassza **A kezdőélmény indítása** lehetőséget, és győződjön meg róla, hogy be van-e jelölve az **Általánosítás** jelölőnégyzet.

    ![Rendszerelőkészítő eszköz](media/prepare-for-upload-vhd-image/syspre.png)
1. A **Leállítási beállítások párbeszédpanelen**válassza a **Leállítás**lehetőséget.
1. Válassza **az OK gombot.**
1. Amikor a Sysprep befejeződik, állítsa le a virtuális gép. Ne használja **az újraindítást** a virtuális gép leállításához.

Most a VHD készen áll a feltöltésre. Ha többet szeretne tudni arról, hogyan hozhat létre virtuális gépeket általános lemezről, [olvassa el az Általános virtuális merevlemez feltöltése és új virtuális gép létrehozásának az Azure-ban című témakört.](sa-upload-generalized.md)


>[!NOTE]
> Az egyéni *unattend.xml* fájl nem támogatott. Bár támogatjuk `additionalUnattendContent` a tulajdonságot, amely csak korlátozott támogatást nyújt a [microsoft-windows-shell-setup](https://docs.microsoft.com/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) beállítások hozzáadásához az *unattend.xml* fájlhoz, amelyet az Azure kiépítési ügynök használ. A FirstLogonCommands és a LogonCommands segítségével például [továbbiUnattendContent](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent?view=azure-dotnet) parancsot adhat hozzá. További információ: [additionalUnattendContent FirstLogonCommands example](https://github.com/Azure/azure-quickstart-templates/issues/1407).


## <a name="complete-the-recommended-configurations"></a>Az ajánlott konfigurációk befejezése
A következő beállítások nem befolyásolják a virtuális merevlemezek feltöltését. Azonban azt javasoljuk, hogy konfigurálja őket.

* Telepítse az [Azure virtuálisgép-ügynök](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Ezután engedélyezheti a virtuálisgép-bővítményeket. A virtuálisgép-bővítmények valósítják meg a legtöbb kritikus funkciót, amelyet érdemes használni a virtuális gépek. A bővítményekre például a jelszavak alaphelyzetbe állításához vagy az RDP konfigurálásához kell szükség. További információ: [Azure Virtual Machine Agent overview](../extensions/agent-windows.md).
* Miután létrehozta a virtuális gép az Azure-ban, azt javasoljuk, hogy helyezze el a lapozófájlt a *temporális meghajtó kötetteljesítmény* ének javítása érdekében. A fájlelhelyezést a következőképpen állíthatja be:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -Name "PagingFiles" -Value "D:\pagefile.sys" -Type MultiString -Force
   ```
  Ha egy adatlemez van csatolva a virtuális géphez, az időbeli meghajtó kötetének betűjele általában *D.* Ez a megjelölés a beállításoktól és a rendelkezésre álló meghajtók számától függően eltérő lehet.
  * Javasoljuk, hogy tiltsa le a víruskereső szoftver által biztosított parancsfájl-blokkolókat. Ezek zavarhatják, és blokkolja a Windows kiépítési ügynök parancsfájlok végre, amikor telepíti az új virtuális gép a lemezképből.
  
## <a name="next-steps"></a>További lépések
* [Windows virtuális géplemezkép feltöltése az Azure-ba az Erőforrás-kezelő központi telepítéseihez](upload-generalized-managed.md)
* [Az Azure Windows virtuális gépek aktiválási problémáinak elhárítása](troubleshoot-activation-problems.md)

