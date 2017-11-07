---
title: "Az Azure-bA feltölteni egy Windows virtuális merevlemez előkészítése |} Microsoft Docs"
description: "A Windows VHD- vagy VHDX előkészítése az Azure-bA feltöltés előtt"
services: virtual-machines-windows
documentationcenter: 
author: glimoli
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7802489d-33ec-4302-82a4-91463d03887a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 67832fd20b758af6fd7a31c0099ce8019bb2442d
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Az Azure-bA feltöltendő Windows VHD vagy VHDX előkészítése
A Windows virtuális gépek (VM) a helyszíni Microsoft Azure feltöltés előtt elő kell készítenie a virtuális merevlemez (VHD- vagy VHDX-). Azure csak 1. generációs virtuális gépek, amelyek a VHD formátumban és a rögzített méretű lemez támogatja. A VHD számára engedélyezett maximális mérete 1,023 GB. 1 virtuális gép a vhdx-fájl a fájlrendszerben VHD-t és a rögzített méretű dinamikusan bővülő lemezek generáció válthat. De nem módosíthatja a virtuális gép generációját. További információkért lásd: [érdemes létrehozni egy 1 vagy 2. generációs virtuális gép a Hyper-V](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

Az Azure virtuális gépekhez a támogatási szabályzata kapcsolatos további információkért lásd: [Microsoft server szoftver a Microsoft Azure virtuális gépek támogatása](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines).

> [!Note]
> A jelen cikkben lévő utasítások a Windows Server 2008 R2 és újabb Windows server operációs rendszer 64 bites verziójára vonatkoznak. További információ az operációs rendszer az Azure-ban futó 32 bites verzióját: [az Azure virtuális gépeken 32 bites operációs rendszerek támogatása](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines).

## <a name="convert-the-virtual-disk-to-vhd-and-fixed-size-disk"></a>A virtuális lemez konvertálása virtuális merevlemez és a rögzített méretű lemez 
Ha a virtuális lemez konvertálása a megkívánt formátumban Azure van szüksége, használja a módszerek közül az ebben a szakaszban. A virtuális gép biztonsági mentése előtt a virtuális lemezen konvertálási folyamat futtatja, és győződjön meg arról, hogy a Windows virtuális merevlemez megfelelően működik-e a helyi kiszolgálón. Javítsa ki a hibákat, a virtuális gépért belül, mielőtt alakítható, vagy töltse fel az Azure.

A lemez konvertálása után a konvertált lemezt használó virtuális gép létrehozása. Indítsa el, és jelentkezzen be a virtuális Gépet a virtuális gép előkészítése a feltöltés befejeződik.

### <a name="convert-disk-using-hyper-v-manager"></a>Hyper-V kezelőjével lemez konvertálása
1. Nyissa meg a Hyper-V kezelőt, és válassza ki a helyi számítógépen, a bal oldalon. A számítógép-lista fent menüben kattintson a **művelet** > **szerkesztése lemezre**.
2. Az a **virtuális merevlemez keresése** képernyőn, keresse meg és válassza ki a virtuális lemezt.
3. Az a **művelet kiválasztása** képernyőn, majd válassza ki **átalakítása** és **következő**.
4. Ha VHDX konvertálása van szüksége, válassza ki a **VHD** majd **tovább**
5. Ha egy dinamikusan bővülő lemezek konvertálása van szüksége, válassza ki a **mérete rögzített** majd **tovább**
6. Keresse meg és válassza ki, mentse az új VHD-fájl elérési útját.
7. Kattintson a **Befejezés** gombra.

>[!NOTE]
>Ebben a cikkben a parancsokat egy rendszergazda jogú PowerShell-munkamenetben kell futtatni.

### <a name="convert-disk-by-using-powershell"></a>Konvertálja a lemezt a PowerShell használatával
Virtuális lemez segítségével válthat a [Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx) Windows PowerShell parancsot. Válassza ki **Futtatás rendszergazdaként** PowerShell indításakor. 

A következő példaparancs alakítja át a virtuális merevlemez VHDX, és egy dinamikusan bővülő rögzített méretű lemezt:

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```
Ebben a parancsban cserélje le a értéke "-elérési út" az elérési útját az átalakítani kívánt virtuális merevlemez és a értéke "-DestinationPath" az új elérési úttal és a konvertált lemez neve.

### <a name="convert-from-vmware-vmdk-disk-format"></a>VMware VMDK lemezformátum konvertálása
Ha rendelkezik Windows Virtuálisgép-lemezképet a [VMDK-fájl formátumú](https://en.wikipedia.org/wiki/VMDK), átalakíthatja a virtuális merevlemez használatával a [Microsoft VM konverter](https://www.microsoft.com/download/details.aspx?id=42497). További információ a blog cikke [VMware VMDK átalakítása Hyper-V virtuális merevlemez](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx).

## <a name="set-windows-configurations-for-azure"></a>Állítsa be a Windows Azure konfigurációi

A virtuális Gépre az Azure-bA feltölteni kívánt, az alábbi lépéseket az összes parancsok futtatását egy [emelt jogosultságszintű parancssort](https://technet.microsoft.com/library/cc947813.aspx):

1. Távolítsa el az útválasztási táblázat bármely statikus állandó útvonal:
   
   * Az útvonaltábla megtekintéséhez futtassa `route print` parancsot a parancssorba.
   * Ellenőrizze a **adatmegőrzési útvonalak** szakaszok. Ha egy állandó útvonal, akkor [útvonal delete](https://technet.microsoft.com/library/cc739598.apx) távolítsa el.
2. Távolítsa el a WinHTTP proxy:
   
    ```PowerShell
    netsh winhttp reset proxy
    ```
3. A lemez SAN házirend beállítása [Onlineall](https://technet.microsoft.com/library/gg252636.aspx). 
   
    ```PowerShell
    diskpart 
    ```
    Nyissa meg a parancssori ablakot írja be a következő parancsokat:

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Állítsa be az idő egyezményes világidő (UTC) Windows és a Windows időszolgáltatás (w32time) szolgáltatás indítási típusának **automatikusan**:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -name "RealTimeIsUniversal" 1 -Type DWord

    Set-Service -Name w32time -StartupType Auto
    ```
5. Az energiagazdálkodási profil beállítása a **nagy teljesítményű**:

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```

## <a name="check-the-windows-services"></a>A központi Windows-szolgáltatások ellenőrzése
Győződjön meg arról, hogy a következő Windows-szolgáltatások beállításai a **Windows alapértelmezett értékek**. Ezek a szolgáltatások, győződjön meg arról, hogy a virtuális gép rendelkezik-e a kapcsolat beállított minimális számát. Az indítási beállítások visszaállításához futtassa a következő parancsokat:
   
```PowerShell
Set-Service -Name bfe -StartupType Auto
Set-Service -Name dhcp -StartupType Auto
Set-Service -Name dnscache -StartupType Auto
Set-Service -Name IKEEXT -StartupType Auto
Set-Service -Name iphlpsvc -StartupType Auto
Set-Service -Name netlogon -StartupType Manual
Set-Service -Name netman -StartupType Manual
Set-Service -Name nsi -StartupType Auto
Set-Service -Name termService -StartupType Manual
Set-Service -Name MpsSvc -StartupType Auto
Set-Service -Name RemoteRegistry -StartupType Auto
```

## <a name="update-remote-desktop-registry-settings"></a>A távoli asztal beállításjegyzék-beállítások frissítése
Győződjön meg arról, hogy a következő beállításai megfelelően vannak a távoli asztali kapcsolat:

>[!Note] 
>Hibaüzenet jelenhet meg, ha futtatja a **Set-ItemProperty-elérési út "HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal szolgáltatások – name &lt;objektumnév&gt; &lt;érték&gt;** az alábbi lépéseket. A hibaüzenet a következő biztonságosan figyelmen kívül hagyhatja. Ez azt jelenti, hogy csak, hogy a tartomány nem küldését, hogy a konfigurálás egy csoportházirend-objektumon keresztül.
>
>

1. Engedélyezve van a távoli asztal protokoll (RDP):
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -name "fDenyTSConnections" -Value 0 -Type DWord

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDenyTSConnections" -Value 0 -Type DWord
    ```
   
2. Az RDP-portjára helyesen van beállítva (3389-es alapértelmezett):
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "PortNumber" 3389 -Type DWord
    ```
    A virtuális gép telepítésekor az alapértelmezett szabályok 3389-es port alapján jönnek létre. Ha azt szeretné, hogy módosítsa a portszámot, ehhez a virtuális Gépet az Azure-ban telepítése után.

3. A figyelő minden hálózati felületen figyeli.
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "LanAdapter" 0 -Type DWord
   ```
4. Az RDP-kapcsolatok esetén a hálózati szintű hitelesítéssel mód konfigurálása:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "UserAuthentication" 1 -Type DWord

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SecurityLayer" 1 -Type DWord

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "fAllowSecProtocolNegotiation" 1 -Type DWord
     ```

5. Állítsa be a életben tartási értéket:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveEnable" 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "KeepAliveInterval" 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "KeepAliveTimeout" 1 -Type DWord
    ```
6. Csatlakozzon újra:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -name "fDisableAutoReconnect" 0 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fInheritReconnectSame" 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "fReconnectSame" 0 -Type DWord
    ```
7. Az egyidejű kapcsolatok számának korlátja:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -name "MaxInstanceCount" 4294967295 -Type DWord
    ```
8. Ha bármely önaláírt tanúsítványokat az RDP-figyelő kötődik, távolítsa el őket:
    
    ```PowerShell
    Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name "SSLCertificateSHA1Hash"
    ```
    Ez a, győződjön meg arról, hogy csatlakoztathatja elején a virtuális gép telepítésekor. Emellett áttekintheti a később a után a virtuális Gépet a rendszer telepíti az Azure-ban, ha szükséges.

9. Ha a virtuális Gépet egy tartományhoz tartozik, ellenőrizze a következő beállításokat, győződjön meg arról, hogy a korábbi beállításokat nem tért vissza. A házirendekben, amelyek ellenőrizni kell a következők:
    
    - RDP engedélyezve van:

         Számítógép konfigurációja\Házirendek\A Settings\Administrative Templates\ összetevők\Távoli asztali szolgáltatások\Távoli asztali munkamenetgazda\Kapcsolatok:
         
         **Lehetővé teszi a felhasználók távolról történő csatlakozást a távoli asztal használatával**

    - NLA csoportházirend:

        Settings\Administrative Templates\Components\Remote asztali szolgáltatások\Távoli asztali munkamenetgazda\Biztonság: 
        
        **Távoli kapcsolatok hálózati szintű hitelesítéssel felhasználói hitelesítés megkövetelése**
    
    - Élő beállítások megtartásához:

        Számítógép konfigurációja\Házirendek\A Settings\Administrative sablonok\Windows összetevők\Távoli asztali szolgáltatások\Távoli asztali munkamenetgazda\Kapcsolatok: 
        
        **Keep-alive kapcsolat időköz beállítása**

    - Csatlakozzon újra a beállításokat:

        Számítógép konfigurációja\Házirendek\A Settings\Administrative sablonok\Windows összetevők\Távoli asztali szolgáltatások\Távoli asztali munkamenetgazda\Kapcsolatok: 
        
        **Az automatikus újracsatlakozás**

    - Beállítások kapcsolatok számának korlátozása:

        Számítógép konfigurációja\Házirendek\A Settings\Administrative sablonok\Windows összetevők\Távoli asztali szolgáltatások\Távoli asztali munkamenetgazda\Kapcsolatok: 
        
        **A kapcsolatok száma**

## <a name="configure-windows-firewall-rules"></a>A Windows tűzfal-szabályok konfigurálása
1. A Windows tűzfal bekapcsolása a három profilok (a tartományt, a Standard és a nyilvános):

   ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile' -name "EnableFirewall" -Value 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile' -name "EnableFirewall" -Value 1 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\Standardprofile' -name "EnableFirewall" -Value 1 -Type DWord
   ```

2. A PowerShell lehetővé teszik a Rendszerfelügyeleti webszolgáltatások (tartományi, privát és nyilvános) három tűzfalprofil keresztül és a PowerShell távelérési szolgáltatás használatával futtassa a következő parancsot:
   
   ```PowerShell
    Enable-PSRemoting -force
    netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
    netsh advfirewall firewall set rule dir=in name="Windows Remote Management (HTTP-In)" new enable=yes
   ```
3. A következő tűzfalszabályokat engedélyezi az RDP-forgalmát engedélyezi. 

   ```PowerShell
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
   ```   
4. A fájl- és nyomtatómegosztás-szabály engedélyezése, hogy a virtuális gép válaszolhassanak a virtuális hálózaton belül ping parancsra:

   ```PowerShell
    netsh advfirewall firewall set rule dir=in name="File and Printer Sharing (Echo Request - ICMPv4-In)" new enable=yes
   ``` 
5. Ha a virtuális Gépet egy tartományhoz tartozik, ellenőrizze a következő beállításokat, győződjön meg arról, hogy a korábbi beállításokat nem tért vissza. Az AD-szabályzatokat, amelyek ellenőrizni kell a következők:

    - A Windows tűzfal-profilok engedélyezése

        Számítógép konfigurációja\Házirendek\A Settings\Administrative sablonok\Hálózat\Hálózati Connection\Windows Firewall\Domain Profile\Windows tűzfal: **minden hálózati kapcsolatok védelme**

       Számítógép konfigurációja\Házirendek\A Settings\Administrative sablonok\Hálózat\Hálózati Connection\Windows Firewall\Standard Profile\Windows tűzfal: **minden hálózati kapcsolatok védelme**

    - RDP engedélyezése 

        Számítógép konfigurációja\Házirendek\A Settings\Administrative sablonok\Hálózat\Hálózati Connection\Windows Firewall\Domain Profile\Windows tűzfal: **bejövő távoli asztal Kivételek tiltása**

        Számítógép konfigurációja\Házirendek\A Settings\Administrative sablonok\Hálózat\Hálózati Connection\Windows Firewall\Standard Profile\Windows tűzfal: **bejövő távoli asztal Kivételek tiltása**

    - ICMP-V4 engedélyezése

        Számítógép konfigurációja\Házirendek\A Settings\Administrative sablonok\Hálózat\Hálózati Connection\Windows Firewall\Domain Profile\Windows tűzfal: **ICMP kivételek tiltása**

        Számítógép konfigurációja\Házirendek\A Settings\Administrative sablonok\Hálózat\Hálózati Connection\Windows Firewall\Standard Profile\Windows tűzfal: **ICMP kivételek tiltása**

## <a name="verify-vm-is-healthy-secure-and-accessible-with-rdp"></a>Győződjön meg arról VM kifogástalan, biztonságos és RDP-érhető el 
1. Ellenőrizze, hogy a lemez kifogástalan állapotú és egységes, futtassa a következő virtuális gép újraindításkor egy ellenőrző lemez művelet:

    ```PowerShell
    Chkdsk /f
    ```
    Győződjön meg arról, hogy ez a jelentés tartalmazza-e egy tiszta és egészséges lemezt.

2. A rendszerindítási konfigurációs adatok (BCD) beállításainak megadása. 

    > [!Note]
    > Győződjön meg arról, hogy egy rendszergazda jogú CMD-ablakban futtassa az alábbi parancsokat és **nem** a PowerShell:
   
   ```CMD
   bcdedit /set {bootmgr} integrityservices enable
   
   bcdedit /set {default} device partition=C:
   
   bcdedit /set {default} integrityservices enable
   
   bcdedit /set {default} recoveryenabled Off
   
   bcdedit /set {default} osdevice partition=C:
   
   bcdedit /set {default} bootstatuspolicy IgnoreAllFailures
   ```
3. Ellenőrizze, hogy a Windows Management Instrumentations tárház egységes. Ennek elvégzéséhez futtassa a következő parancsot:

    ```PowerShell
    winmgmt /verifyrepository
    ```
    Ha a tárház sérült, lásd: [WMI: tárház sérülése, vagy nem](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

4. Győződjön meg arról, hogy minden más alkalmazás nem használja a 3389-es port. Ezt a portot használja az RDP-szolgáltatás az Azure-ban. Futtathat **netstat - anob** portokat a szolgáló a virtuális Gépen:

    ```PowerShell
    netstat -anob
    ```

5. Ha a feltölteni kívánt Windows virtuális Merevlemezt egy olyan tartományvezérlőre, majd kövesse az alábbi lépéseket:

    A. Hajtsa végre a [további lépések](https://support.microsoft.com/kb/2904015) készíti elő a lemezen.

    B. Győződjön meg arról, hogy ismeri a DSRM-jelszót, abban az esetben el kell indítani a virtuális gép a Címtárszolgáltatások helyreállító MÓDJÁBAN valamely pontján. Érdemes lehet hivatkozni erre a hivatkozásra kattintva állítsa be a [DSRM-jelszót](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx).

6. Győződjön meg arról, hogy a beépített Rendszergazda fiók és jelszó ismert-e meg. Érdemes lehet az aktuális helyi rendszergazda jelszavát, és győződjön meg arról, hogy, ez a fiók használatával jelentkezzen be a Windows az RDP-kapcsolaton keresztül. A hozzáférési engedélyt a "Bejelentkezés engedélyezése a távoli asztali szolgáltatások használatával" csoportházirend-objektum vezérli. Ez az objektum a a Helyicsoportházirend-szerkesztő alatt tekintheti meg:

    Számítógép konfigurációja\A Windows beállításai\Biztonsági beállítások\Helyi házirendek\Felhasználói jogosultságok kiosztása

7. Ellenőrizze, hogy a következő AD győződjön meg arról, hogy az RDP-hozzáférés RDP keresztül, és nem a hálózatról nem blokkolják házirendeket:

    - Számítógép konfigurációja\A Windows beállításai\Biztonsági beállítások\Helyi házirendek\Felhasználói jogosultságok Assignment\Deny hozzáférést a hálózatról

    - Számítógép konfigurációja\A Windows beállításai\Biztonsági beállítások\Helyi házirendek\Felhasználói jogosultságok Assignment\Deny naplót a távoli asztali szolgáltatások használatával


8. Indítsa újra a virtuális Gépet, győződjön meg arról, hogy a Windows továbbra is működik megfelelően-e az RDP-kapcsolaton keresztül elérhető. Ezen a ponton érdemes lehet a helyi Hyper-V győződjön meg arról, hogy a virtuális gép teljesen indul, és ellenőrizze, hogy elérhető-e az RDP-e a virtuális gép létrehozása.

9. Távolítsa el a felesleges Transport Driver Interface szűrőket, például szoftver, amely elemzi a TCP csomagokat, vagy további tűzfalakat. Emellett áttekintheti a később a után a virtuális Gépet a rendszer telepíti az Azure-ban, ha szükséges.

10. Távolítsa el a harmadik féltől származó szoftverek és illesztőprogramot, amely kapcsolódik a fizikai összetevők vagy a más virtualizációs technológiát.

### <a name="install-windows-updates"></a>Windows-frissítések telepítése
Az ideális konfigurálás az, hogy **a gép legkésőbb javítás szintű**. Ha ez nem lehetséges, győződjön meg arról, hogy telepítve vannak-e a következő frissítéseket:

|                       |                   |           |                                       Minimális verzió x64       |                                      |                                      |                            |
|-------------------------|-------------------|------------------------------------|---------------------------------------------|--------------------------------------|--------------------------------------|----------------------------|
| Összetevő               | Bináris            | Windows 7 és Windows Server 2008 R2 | Windows 8 és Windows Server 2012-ben             | Windows 8.1 és Windows Server 2012 R2 rendszerben | Windows 10 és Windows Server 2016 RS1 | Windows 10 RS2             |
| Storage                 | a Disk.sys          | 6.1.7601.23403 - KB3125574         | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061           | -                                    | -                          |
|                         | Storport.sys      | 6.1.7601.23403 - KB3125574         | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726           | 10.0.14393.1358 - KB4022715          | 10.0.15063.332             |
|                         | NTFS.sys          | 6.1.7601.23403 - KB3125574         | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726           | 10.0.14393.1198 - KB4022715          | 10.0.15063.447             |
|                         | Iologmsg.dll      | 6.1.7601.23403 - KB3125574         | 6.2.9200.16384 - KB2995387                  | -                                    | -                                    | -                          |
|                         | Classpnp.sys      | 6.1.7601.23403 - KB3125574         | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614           | 10.0.14393.953 - KB4022715           | -                          |
|                         | Volsnap.sys       | 6.1.7601.23403 - KB3125574         | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384           | -                                    | 10.0.15063.0               |
|                         | PartMgr.sys       | 6.1.7601.23403 - KB3125574         | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 - KB3000850           | 10.0.14393.953 - KB4022715           | 10.0.15063.0               |
|                         | volmgr.sys        |                                    |                                             |                                      |                                      | 10.0.15063.0               |
|                         | Volmgrx.sys       | 6.1.7601.23403 - KB3125574         | -                                           | -                                    | -                                    | 10.0.15063.0               |
|                         | Msiscsi.sys       | 6.1.7601.23403 - KB3125574         | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726           | 10.0.14393.1066 - KB4022715          | 10.0.15063.447             |
|                         | Msdsm.sys         | 6.1.7601.23403 - KB3125574         | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726           | -                                    | -                          |
|                         | MPIO.sys          | 6.1.7601.23403 - KB3125574         | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726           | 10.0.14393.1198 - KB4022715          | -                          |
|                         | Fveapi.dll        | 6.1.7601.23311 - KB3125574         | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614           | 10.0.14393.576 - KB4022715           | -                          |
|                         | Fveapibase.dll    | 6.1.7601.23403 - KB3125574         | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614           | 10.0.14393.206 - KB4022715           | -                          |
| Network (Hálózat)                 | netvsc.sys        | -                                  | -                                           | -                                    | 10.0.14393.1198 - KB4022715          | 10.0.15063.250 - KB4020001 |
|                         | mrxsmb10.sys      | 6.1.7601.23816 - KB4022722         | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726           | 10.0.14393.479 - KB4022715           | 10.0.15063.483             |
|                         | mrxsmb20.sys      | 6.1.7601.23816 - KB4022722         | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726           | 10.0.14393.953 - KB4022715           | 10.0.15063.483             |
|                         | Mrxsmb.sys        | 6.1.7601.23816 - KB4022722         | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726           | 10.0.14393.953 - KB4022715           | 10.0.15063.0               |
|                         | Tcpip.sys         | 6.1.7601.23761 - KB4022722         | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726           | 10.0.14393.1358 - KB4022715          | 10.0.15063.447             |
|                         | a HTTP.sys          | 6.1.7601.23403 - KB3125574         | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726           | 10.0.14393.251 - KB4022715           | 10.0.15063.483             |
|                         | vmswitch.sys      | 6.1.7601.23727 - KB4022719         | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726           | 10.0.14393.1358 - KB4022715          | 10.0.15063.138             |
| Mag                    | Ntoskrnl.exe      | 6.1.7601.23807 - KB4022719         | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726           | 10.0.14393.1358 - KB4022715          | 10.0.15063.483             |
| Távoli asztali szolgáltatások | rdpcorets.dll     | 6.2.9200.21506 - KB4022719         | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726           | 10.0.14393.1198 - KB4022715          | 10.0.15063.0               |
|                         | TERMSRV.dll       | 6.1.7601.23403 - KB3125574         | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 - KB3000850           | 10.0.14393.0 - KB4022715             | 10.0.15063.0               |
|                         | Termdd.sys        | 6.1.7601.23403 - KB3125574         | -                                           | -                                    | -                                    | -                          |
|                         | Win32k.sys        | 6.1.7601.23807 - KB4022719         | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726           | 10.0.14393.594 - KB4022715           | -                          |
|                         | rdpdd.dll         | 6.1.7601.23403 - KB3125574         | -                                           | -                                    | -                                    | -                          |
|                         | Rdpwd.sys         | 6.1.7601.23403 - KB3125574         | -                                           | -                                    | -                                    | -                          |
| Biztonság                | WannaCrypt miatt | KB4012212                          | KB4012213                                   | KB4012213                            | KB4012606                            | KB4012606                  |
|                         |                   |                                    | KB4012216                                   |                                      | KB4013198                            | KB4013198                  |
|                         |                   | KB4012215                          | KB4012214                                   | KB4012216                            | KB4013429                            | KB4013429                  |
|                         |                   |                                    | KB4012217                                   |                                      | KB4013429                            | KB4013429                  |
       
### A sysprep használatával<a id="step23"></a>    

A Sysprep egy folyamat, amely alaphelyzetbe állítja a rendszer, és gondoskodik az "a kezdőélmény" eltávolíthatja személyes adatokat, és alaphelyzetbe állításával több összetevőt windows-telepítést történő futtatásával. Általában ehhez Ha azt szeretné, ahol központilag telepíthető a többi virtuális adott konfigurációval rendelkezik sablon létrehozása. Ez a lehetőség egy **kép általánosítva**.

Ha ehelyett szeretné csak egy virtuális gép létrehozása egy lemezről, nincs a Sysprep eszközt használja. Ebben a helyzetben csak létrehozhat a virtuális gép úgynevezett egy **speciális kép**.

Speciális lemez a virtuális gépek létrehozásával kapcsolatos további információkért lásd:

- [Virtuális gép létrehozása egy speciális lemezről](create-vm-specialized.md)
- [Hozzon létre egy virtuális Gépet egy speciális VHD lemezről](https://azure.microsoft.com/resources/templates/201-vm-specialized-vhd/)

Ha általános lemezkép létrehozásához szüksége futtatni a Sysprep programot. További információ a Sysprep: [hogyan használja a Sysprep: Bevezetés](http://technet.microsoft.com/library/bb457073.aspx). 

Nem minden szerepkör vagy a Windows-alapú számítógépen telepített alkalmazás támogatja-e általánosítása. Ezért ahhoz, hogy futtatni ezt az eljárást, tekintse meg a következő cikk győződjön meg arról, hogy a szerepkört az adott számítógép támogatja a sysprep. További információ [Sysprep támogatási kiszolgálói szerepköre tekintetében](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

### <a name="steps-to-generalize-a-vhd"></a>Virtuális merevlemez generalize lépései

>[!NOTE]
> Az alábbi lépéseket a sysprep.exe futtatása után a virtuális gép kikapcsolása, és nem kapcsolja vissza addig, amíg az Azure-ban, a lemezkép létrehozása..

1. Jelentkezzen be a Windows virtuális gép.
2. Futtatás **parancssor** rendszergazdaként. 
3. Módosítsa a könyvtárat: **%windir%\system32\sysprep**, majd futtassa a **sysprep.exe**.
3. Az a **rendszer-előkészítő eszköz** párbeszédpanelen jelölje ki **adja meg a rendszer Out-of-Box élmény (OOBE)**, és győződjön meg arról, hogy a **Generalize** jelölőnégyzet be van jelölve.

    ![Rendszer-előkészítő eszköz](media/prepare-for-upload-vhd-image/syspre.png)
4. A **leállítási beállítások**, jelölje be **leállítási**.
5. Kattintson az **OK** gombra.
6. A Sysprep befejezése után állítsa le a virtuális gép. Ne használjon **indítsa újra a** a virtuális gép leállítása.
7. Most a virtuális merevlemez készen áll a fel kell tölteni. Általános lemez a virtuális gépek létrehozásával kapcsolatos további információkért lásd: [egy általánosított virtuális merevlemez feltöltéséhez, és használja az Azure-ban hozzon létre egy új virtuális gépek](sa-upload-generalized.md).


## <a name="complete-recommended-configurations"></a>Adja meg az ajánlott beállításait
A következő beállítások nem befolyásolják a virtuális merevlemez feltöltése. Azonban javasoljuk, hogy azokat konfigurálta.

* Telepítse a [Azure virtuális gépek ügynök](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Ezt követően engedélyezheti a Virtuálisgép-bővítmények. A Virtuálisgép-bővítmények valósítja meg a legtöbb kritikus funkciója, amely előfordulhat, hogy használja a virtuális gépek például a jelszavak, alaphelyzetbe állítását RDP konfigurálása, és így tovább. További információkért lásd:

    - [Ügynök és Virtuálisgép-bővítmények – 1. rész](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-1/)
    - [Ügynök és Virtuálisgép-bővítmények – 2. rész](https://azure.microsoft.com/blog/vm-agent-and-extensions-part-2/)
* A biztonsági napló Windows összeomlási kapcsolatos problémák elhárításához hasznos lehet. A biztonsági napló gyűjtésének engedélyezése:
  
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "CrashDumpEnable" -Value "2" -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "DumpFile" -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "AutoReboot" -Value 0 -Type DWord
    New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    New-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpFolder" -Value "c:\CrashDumps"
    New-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpCount" -Value 10 -Type DWord
    New-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpType" -Value 2 -Type DWord
    Set-Service -Name WerSvc -StartupType Manual
    ```
    Ha bármilyen hiba jelentkezik az ebben a cikkben használt eljárás lépéseit, ez azt jelenti, hogy létezik-e már a beállításkulcsokat. Ilyen esetben használja a következő parancsokat:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "CrashDumpEnable" -Value "2" -Type DWord
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -name "DumpFile" -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpFolder" -Value "c:\CrashDumps"
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpCount" -Value 10 -Type DWord
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps' -name "DumpType" -Value 2 -Type DWord
    Set-Service -Name WerSvc -StartupType Manual
    ```
*  Miután a virtuális gép létrehozása az Azure-ban, azt javasoljuk, hogy a "Historikus meghajtó" köteten a teljesítmény javítása érdekében helyezze a lapozófájl méretét. Állíthat be a következőképpen:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -name "PagingFiles" -Value "D:\pagefile"
    ```
Ha bármely adatlemez, amely a virtuális Géphez van csatolva, az ideiglenes meghajtón kötet meghajtójának betűjelét általában-e "D" Lehet, hogy a kijelölés különböző, attól függően, hogy az elérhető meghajtók számát és a beállítások.

## <a name="next-steps"></a>Következő lépések
* [Windows Virtuálisgép-lemezkép feltöltése az Azure Resource Manager üzembe helyezések esetében](upload-generalized-managed.md)
* [Azure Windows virtuális gép aktiválással kapcsolatos problémák elhárítása](troubleshoot-activation-problems.md)

