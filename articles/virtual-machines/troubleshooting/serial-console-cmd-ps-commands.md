---
title: CMD és PowerShell az Azure Windows rendszerű virtuális gépeken | Microsoft Docs
description: CMD-és PowerShell-parancsok használata az Azure-beli Windows rendszerű virtuális gépeken található SAC szolgáltatásban
services: virtual-machines-windows
documentationcenter: ''
author: alsin
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 77fe6f1ce416df049928697d2c166e2aba0abfe2
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935219"
---
# <a name="windows-commands---cmd-and-powershell"></a>Windows-parancsok – CMD és PowerShell

Ez a szakasz példákat tartalmaz a gyakori feladatok végrehajtásához olyan helyzetekben, amikor a Windows rendszerű virtuális gép eléréséhez szükség lehet a SAC használatára, például ha az RDP-kapcsolódási hibák elhárításához szükség van.

A SAC a Windows összes verziójában a Windows Server 2003 óta szerepel, de alapértelmezés szerint le van tiltva. A SAC a `sacdrv.sys` kernel-illesztőprogramra, a `Special Administration Console Helper` szolgáltatásra`sacsvr`() és a `sacsess.exe` folyamatra támaszkodik. További információ: válságkezelési [szolgáltatások eszközei és beállításai](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc787940(v%3dws.10)).

A SAC lehetővé teszi, hogy soros porton keresztül kapcsolódjon a futó operációs rendszerhez. Ha a cmd-t a sacből `cmd.exe` indítja el, `sacsess.exe` az a futó operációs rendszeren belül elindul. Láthatja, hogy a Feladatkezelőban, ha a virtuális gép RDP-kapcsolata egyszerre csatlakozik a SAC szolgáltatáshoz a soros konzolon keresztül. A SAC-n keresztül elért cmd ugyanaz, `cmd.exe` mint amikor RDP-kapcsolaton keresztül csatlakozik. Mindegyik parancs és eszköz elérhető, beleértve a PowerShell indításának lehetőségét is a CMD-példányból. Ez jelentős különbség a SAC és a Windows helyreállítási környezet (WinRE) között, amely lehetővé teszi a futó operációs rendszer felügyeletét, ahol a WinRE egy másik, minimális operációs rendszerbe kerül. Habár az Azure-beli virtuális gépek nem támogatják a WinRE elérésének lehetőségét a soros konzol szolgáltatással, az Azure-beli virtuális gépek a SAC használatával kezelhetők.

Mivel a SAC egy, a görgetést nem tartalmazó 80x24-pufferre korlátozódik `| more` , a Hozzáadás parancsokkal megjelenítheti a kimenetet egy oldalon egyszerre. A gombra `<enter>` kattintva megtekintheti a következő lapot, vagy megtekintheti a következő sort. `<spacebar>`  

`SHIFT+INSERT`a soros konzol ablakának beillesztési parancsikonja.

A SAC korlátozott képernyős puffere miatt a több parancs könnyebben begépelhető egy helyi szövegszerkesztőbe, majd beilleszthető a SACbe.

## <a name="view-and-edit-windows-registry-settings"></a>Windows beállításjegyzék-beállítások megtekintése és szerkesztése
### <a name="verify-rdp-is-enabled"></a>Az RDP ellenőrzése engedélyezve
`reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections`

`reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections`

A második kulcs (a \Policies alatt) csak akkor létezik, ha a megfelelő csoportházirend-beállítás konfigurálva van.

### <a name="enable-rdp"></a>RDP engedélyezése
`reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0`

`reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0` 

A második kulcs (\Policies) csak akkor szükséges, ha a megfelelő csoportházirend-beállítást konfigurálták. Ha a csoportházirendben be van állítva, a rendszer a következő csoportházirend-frissítéskor újraírja az értéket.

## <a name="manage-windows-services"></a>Windows-szolgáltatások kezelése

### <a name="view-service-state"></a>Szolgáltatás állapotának megtekintése
`sc query termservice`
###  <a name="view-service-logon-account"></a>Szolgáltatás bejelentkezési fiókjának megtekintése
`sc qc termservice`
### <a name="set-service-logon-account"></a>Szolgáltatás bejelentkezési fiókjának beállítása 
`sc config termservice obj= "NT Authority\NetworkService"`

Az egyenlőségjel után szóközt kell megadni.
### <a name="set-service-start-type"></a>Szolgáltatás indítási típusának beállítása
`sc config termservice start= demand` 

Az egyenlőségjel után szóközt kell megadni. A lehetséges indítási értékek `boot` `system` `auto`a következők:`demand`,,,,. `delayed-auto` `disabled`
### <a name="set-service-dependencies"></a>Szolgáltatási függőségek beállítása
`sc config termservice depend= RPCSS`

Az egyenlőségjel után szóközt kell megadni.
### <a name="start-service"></a>Szolgáltatás indítása
`net start termservice`

vagy

`sc start termservice`
### <a name="stop-service"></a>Szolgáltatás leállítása
`net stop termservice`

vagy

`sc stop termservice`
## <a name="manage-networking-features"></a>Hálózatkezelési funkciók kezelése
### <a name="show-nic-properties"></a>NIC-tulajdonságok megjelenítése
`netsh interface show interface` 
### <a name="show-ip-properties"></a>IP-tulajdonságok megjelenítése
`netsh interface ip show config`
### <a name="show-ipsec-configuration"></a>IPSec-konfiguráció megjelenítése
`netsh nap client show configuration`  
### <a name="enable-nic"></a>Hálózati adapter engedélyezése
`netsh interface set interface name="<interface name>" admin=enabled`
### <a name="set-nic-to-use-dhcp"></a>Hálózati adapter beállítása DHCP használatára
`netsh interface ip set address name="<interface name>" source=dhcp`

A szolgáltatással kapcsolatos `netsh`további információkért [kattintson ide](https://docs.microsoft.com/windows-server/networking/technologies/netsh/netsh-contexts).

Az Azure-beli virtuális gépeket mindig úgy kell konfigurálni a vendég operációs rendszeren, hogy DHCP használatával igényeljenek IP-címet. Az Azure statikus IP-beállítása továbbra is DHCP protokollt használ, hogy a statikus IP-címet a virtuális géphez adja.
### <a name="ping"></a>Pingelési teszt
`ping 8.8.8.8` 
### <a name="port-ping"></a>Port pingelése  
A Telnet-ügyfél telepítése

`dism /online /Enable-Feature /FeatureName:TelnetClient`

Kapcsolat tesztelése

`telnet bing.com 80`

A Telnet-ügyfél eltávolítása

`dism /online /Disable-Feature /FeatureName:TelnetClient`

Ha a Windows rendszerben alapértelmezés szerint elérhető metódusokra korlátozódik, a PowerShell jobb megközelítés lehet a portok kapcsolatának teszteléséhez. Példákért tekintse meg az alábbi PowerShell-szakaszt.
### <a name="test-dns-name-resolution"></a>DNS-névfeloldási teszt
`nslookup bing.com`
### <a name="show-windows-firewall-rule"></a>Windows tűzfal szabály megjelenítése
`netsh advfirewall firewall show rule name="Remote Desktop - User Mode (TCP-In)"`
### <a name="disable-windows-firewall"></a>A Windows tűzfal letiltása
`netsh advfirewall set allprofiles state off`

Ezt a parancsot akkor használhatja, ha a hibaelhárítás során átmenetileg kizárják a Windows tűzfalat. A következő újraindításkor vagy az alábbi parancs használatával engedélyezhető. Ne állítsa le a Windows tűzfal szolgáltatás (MPSSVC) vagy az alapszűrő motor (BFE) szolgáltatást úgy, hogy kizárja a Windows tűzfalat. A MPSSVC vagy a BFE leállítása az összes kapcsolat blokkolásával jár.
### <a name="enable-windows-firewall"></a>A Windows tűzfal engedélyezése
`netsh advfirewall set allprofiles state on`
## <a name="manage-users-and-groups"></a>Felhasználók és csoportok kezelése
### <a name="create-local-user-account"></a>Helyi felhasználói fiók létrehozása
`net user /add <username> <password>`
### <a name="add-local-user-to-local-group"></a>Helyi felhasználó hozzáadása a helyi csoporthoz
`net localgroup Administrators <username> /add`
### <a name="verify-user-account-is-enabled"></a>Felhasználói fiók ellenőrzése engedélyezve
`net user <username> | find /i "active"`

Az általánosított rendszerképből létrehozott Azure-beli virtuális gépek helyi rendszergazdai fiókja átnevezve lesz a virtuális gép üzembe helyezése során megadott névre. Így általában nem `Administrator`fog megjelenni.
### <a name="enable-user-account"></a>Felhasználói fiók engedélyezése
`net user <username> /active:yes`  
### <a name="view-user-account-properties"></a>Felhasználói fiók tulajdonságainak megtekintése
`net user <username>`

Példa egy helyi rendszergazdai fiókból származó érdekes sorokra:

`Account active Yes`

`Account expires Never`

`Password expires Never`

`Workstations allowed All`

`Logon hours allowed All`

`Local Group Memberships *Administrators`

### <a name="view-local-groups"></a>Helyi csoportok megtekintése
`net localgroup`
## <a name="manage-the-windows-event-log"></a>A Windows-Eseménynapló kezelése
### <a name="query-event-log-errors"></a>Eseménynaplók lekérdezése – hibák
`wevtutil qe system /c:10 /f:text /q:"Event[System[Level=2]]" | more`

Váltson `/c:10` a visszaadni kívánt eseményekre, vagy helyezze át a szűrőnek megfelelő összes esemény visszaküldéséhez.
### <a name="query-event-log-by-event-id"></a>Eseménynapló lekérdezése eseményazonosító szerint
`wevtutil qe system /c:1 /f:text /q:"Event[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Eseménynapló lekérdezése eseményazonosító és szolgáltató szerint
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Eseménynapló lekérdezése eseményazonosító és szolgáltató szerint az elmúlt 24 órában
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

A `604800000` használatával 24 óra helyett 7 napig kereshet vissza.
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Eseménynapló lekérdezése eseményazonosító, szolgáltató és EventData szerint az elmúlt 7 napban
`wevtutil qe security /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>Telepített alkalmazások megtekintése vagy eltávolítása
### <a name="list-installed-applications"></a>Telepített alkalmazások listázása
`wmic product get Name,InstallDate | sort /r | more`

A `sort /r` telepítés dátuma alapján csökkenő sorrendbe rendezi a rendezést, így könnyen megtekintheti, hogy mi volt a közelmúltban telepítve. A `<spacebar>` paranccsal folytassa a kimenet következő oldalára, vagy `<enter>` egy sorba való haladást.
### <a name="uninstall-an-application"></a>Alkalmazás eltávolítása
`wmic path win32_product where name="<name>" call uninstall`

Cserélje `<name>` le a értéket a fenti parancsban visszaadott névre az eltávolítani kívánt alkalmazásra.

## <a name="file-system-management"></a>Fájlrendszer-kezelés
### <a name="get-file-version"></a>Fájl verziójának beolvasása
`wmic datafile where "drive='C:' and path='\\windows\\system32\\drivers\\' and filename like 'netvsc%'" get version /format:list`

Ez a példa a virtuális NIC-illesztőprogram netvsc. sys, netvsc63. sys vagy netvsc60. sys fájljának verzióját adja vissza a Windows-verziótól függően.
### <a name="scan-for-system-file-corruption"></a>Rendszerfájl sérülésének vizsgálata
`sfc /scannow`

Lásd még: [Windows-rendszerkép javítása](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
### <a name="scan-for-system-file-corruption"></a>Rendszerfájl sérülésének vizsgálata
`dism /online /cleanup-image /scanhealth`

Lásd még: [Windows-rendszerkép javítása](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
### <a name="export-file-permissions-to-text-file"></a>Fájl engedélyeinek exportálása szövegfájlba
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /t /c > %temp%\MachineKeys_permissions_before.txt`
### <a name="save-file-permissions-to-acl-file"></a>Fájlengedélyek mentése az ACL-fájlba
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /save %temp%\MachineKeys_permissions_before.aclfile /t`  
### <a name="restore-file-permissions-from-acl-file"></a>Fájlengedélyek visszaállítása az ACL-fájlból
`icacls %programdata%\Microsoft\Crypto\RSA /save %temp%\MachineKeys_permissions_before.aclfile /t`

Az elérési útnak `/restore` a használatakor megadott `/save`mappa szülőjének kell lennie. Ebben a példában `\RSA` a a fenti `/save` példában szereplő `\MachineKeys` mappa szülője.
### <a name="take-ntfs-ownership-of-a-folder"></a>Mappa NTFS-tulajdonjogának átvétele
`takeown /f %programdata%\Microsoft\Crypto\RSA\MachineKeys /a /r`  
### <a name="grant-ntfs-permissions-to-a-folder-recursively"></a>NTFS-engedélyek engedélyezése egy mappához rekurzív módon
`icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"`  
## <a name="manage-devices"></a>Eszközök kezelése
### <a name="remove-non-present-pnp-devices"></a>Nem jelennek meg a PNP-eszközök eltávolítása
`%windir%\System32\RUNDLL32.exe %windir%\System32\pnpclean.dll,RunDLL_PnpClean /Devices /Maxclean`
## <a name="manage-group-policy"></a>Csoportházirend kezelése
### <a name="force-group-policy-update"></a>Csoportházirend frissítésének kényszerítése
`gpupdate /force /wait:-1`
## <a name="miscellaneous-tasks"></a>Egyéb feladatok
### <a name="show-os-version"></a>Operációs rendszer verziójának megjelenítése
`ver`

vagy 

`wmic os get caption,version,buildnumber /format:list`

vagy 

`systeminfo  find /i "os name"`

`systeminfo | findstr /i /r "os.*version.*build"`
### <a name="view-os-install-date"></a>Operációs rendszer telepítési dátumának megtekintése
`systeminfo | find /i "original"`

vagy 

`wmic os get installdate`
### <a name="view-last-boot-time"></a>Utolsó rendszerindítás időpontjának megtekintése
`systeminfo | find /i "system boot time"`
### <a name="view-time-zone"></a>Időzóna megtekintése
`systeminfo | find /i "time zone"`

vagy

`wmic timezone get caption,standardname /format:list`
### <a name="restart-windows"></a>Windows újraindítása
`shutdown /r /t 0`

A `/f` hozzáadásával kényszerítheti az alkalmazások bezárását a figyelmeztetést nem használó felhasználók nélkül.
### <a name="detect-safe-mode-boot"></a>Csökkentett üzemmódú rendszerindítás észlelése
`bcdedit /enum | find /i "safeboot"` 

## <a name="windows-commands---powershell"></a>Windows-parancsok – PowerShell

Ha a PowerShellt a SACben szeretné futtatni, a parancssorba írja be a következőt:

`powershell <enter>`

> [!CAUTION]
> Távolítsa el a PSReadLine modult a PowerShell-munkamenetből, mielőtt bármilyen más PowerShell-parancsot futtatná. Létezik egy ismert probléma, amelyben további karakterek jelenhetnek meg a vágólapról beillesztett szövegben, ha a PSReadLine a SAC PowerShell-munkamenetében fut.

Először ellenőrizze, hogy a PSReadLine be van-e töltve. Alapértelmezés szerint a Windows Server 2016, a Windows 10 és a Windows újabb verzióiban töltődik be. Csak akkor jelennek meg a korábbi Windows-verziókban, ha azt manuálisan telepítették. 

Ha a parancs kimenet nélküli kérést ad vissza, akkor a modul nem töltődött be, és a PowerShell-munkamenetet a szokásos módon folytathatja a SAC szolgáltatásban.

`get-module psreadline`

Ha a fenti parancs a PSReadLine modul verzióját adja vissza, futtassa a következő parancsot az eltávolításához. Ez a parancs nem törli vagy eltávolítja a modult, csak az aktuális PowerShell-munkamenetből távolítja el.

`remove-module psreadline`

## <a name="view-and-edit-windows-registry-settings"></a>Windows beállításjegyzék-beállítások megtekintése és szerkesztése
### <a name="verify-rdp-is-enabled"></a>Az RDP ellenőrzése engedélyezve
`get-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections'`

`get-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections'`

A második kulcs (a \Policies alatt) csak akkor létezik, ha a megfelelő csoportházirend-beállítás konfigurálva van.
### <a name="enable-rdp"></a>RDP engedélyezése
`set-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections' 0 -type dword`

`set-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections' 0 -type dword`

A második kulcs (\Policies) csak akkor szükséges, ha a megfelelő csoportházirend-beállítást konfigurálták. Ha a csoportházirendben be van állítva, a rendszer a következő csoportházirend-frissítéskor újraírja az értéket.
## <a name="manage-windows-services"></a>Windows-szolgáltatások kezelése
### <a name="view-service-details"></a>Szolgáltatás részleteinek megtekintése
`get-wmiobject win32_service -filter "name='termservice'" |  format-list Name,DisplayName,State,StartMode,StartName,PathName,ServiceType,Status,ExitCode,ServiceSpecificExitCode,ProcessId`

`Get-Service`használható, de nem tartalmazza a szolgáltatás bejelentkezési fiókját. `Get-WmiObject win32-service`nem.
### <a name="set-service-logon-account"></a>Szolgáltatás bejelentkezési fiókjának beállítása
`(get-wmiobject win32_service -filter "name='termservice'").Change($null,$null,$null,$null,$null,$false,'NT Authority\NetworkService')`

Ha a (z), vagy `NT AUTHORITY\LocalService` `LocalSystem`rendszertől eltérő `NT AUTHORITY\NetworkService`szolgáltatásfiókot használ, adja meg a fiók jelszavát az utolsó (nyolcadik) argumentumként a fiók neve után.
### <a name="set-service-startup-type"></a>Szolgáltatás indítási típusának beállítása
`set-service termservice -startuptype Manual`

`Set-service`az indítási `Automatic`típushoz `Disabled` fogadja el a vagy a értéket. `Manual`
### <a name="set-service-dependencies"></a>Szolgáltatási függőségek beállítása
`Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\TermService' -Name DependOnService -Value @('RPCSS','TermDD')`
### <a name="start-service"></a>Szolgáltatás indítása
`start-service termservice`
### <a name="stop-service"></a>Szolgáltatás leállítása
`stop-service termservice`
## <a name="manage-networking-features"></a>Hálózatkezelési funkciók kezelése
### <a name="show-nic-properties"></a>NIC-tulajdonságok megjelenítése
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} |  format-list status,name,ifdesc,macadDresS,driverversion,MediaConNectState,MediaDuplexState`

vagy 

`get-wmiobject win32_networkadapter -filter "servicename='netvsc'" |  format-list netenabled,name,macaddress`

`Get-NetAdapter`2012 + esetében érhető el a 2008R2 használatához `Get-WmiObject`.
### <a name="show-ip-properties"></a>IP-tulajdonságok megjelenítése
`get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'" |  format-list DNSHostName,IPAddress,DHCPEnabled,IPSubnet,DefaultIPGateway,MACAddress,DHCPServer,DNSServerSearchOrder`
### <a name="enable-nic"></a>Hálózati adapter engedélyezése
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | enable-netadapter`

vagy

`(get-wmiobject win32_networkadapter -filter "servicename='netvsc'").enable()`

`Get-NetAdapter`2012 + esetében érhető el a 2008R2 használatához `Get-WmiObject`.
### <a name="set-nic-to-use-dhcp"></a>Hálózati adapter beállítása DHCP használatára
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | Set-NetIPInterface -DHCP Enabled`

`(get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'").EnableDHCP()`

`Get-NetAdapter`2012 +-on érhető el. 2008R2 használata `Get-WmiObject`esetén. Az Azure-beli virtuális gépeket mindig úgy kell konfigurálni a vendég operációs rendszeren, hogy DHCP használatával igényeljenek IP-címet. Az Azure statikus IP-beállítása továbbra is DHCP protokollt használ, hogy az IP-címet a virtuális géphez adja.
### <a name="ping"></a>Pingelési teszt
`test-netconnection`

vagy

`get-wmiobject Win32_PingStatus -Filter 'Address="8.8.8.8"' | format-table -autosize IPV4Address,ReplySize,ResponseTime`

`Test-Netconnection`paraméterek nélkül megpróbál pingelést `internetbeacon.msedge.net`végrehajtani. 2012 és újabb rendszereken érhető el. A 2008R2 a `Get-WmiObject` második példában látható módon használhatók.
### <a name="port-ping"></a>Port pingelése
`test-netconnection -ComputerName bing.com -Port 80`

vagy

`(new-object Net.Sockets.TcpClient).BeginConnect('bing.com','80',$null,$null).AsyncWaitHandle.WaitOne(300)`

`Test-NetConnection`2012 +-on érhető el. 2008R2 használata`Net.Sockets.TcpClient`
### <a name="test-dns-name-resolution"></a>DNS-névfeloldási teszt
`resolve-dnsname bing.com` 

vagy 

`[System.Net.Dns]::GetHostAddresses('bing.com')`

`Resolve-DnsName`2012 +-on érhető el. 2008R2 használata `System.Net.DNS`esetén.
### <a name="show-windows-firewall-rule-by-name"></a>A Windows tűzfal szabályának megjelenítése név szerint
`get-netfirewallrule -name RemoteDesktop-UserMode-In-TCP` 
### <a name="show-windows-firewall-rule-by-port"></a>A Windows tűzfal szabályának megjelenítése Port alapján
`get-netfirewallportfilter | where {$_.localport -eq 3389} | foreach {Get-NetFirewallRule -Name $_.InstanceId} | format-list Name,Enabled,Profile,Direction,Action`

vagy

`(new-object -ComObject hnetcfg.fwpolicy2).rules | where {$_.localports -eq 3389 -and $_.direction -eq 1} | format-table Name,Enabled`

`Get-NetFirewallPortFilter`2012 +-on érhető el. A 2008R2 a `hnetcfg.fwpolicy2` com-objektumot használják. 
### <a name="disable-windows-firewall"></a>A Windows tűzfal letiltása
`Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False`

`Set-NetFirewallProfile`2012 +-on érhető el. A fenti cmd `netsh advfirewall` szakaszban hivatkozott 2008R2 használja.
## <a name="manage-users-and-groups"></a>Felhasználók és csoportok kezelése
### <a name="create-local-user-account"></a>Helyi felhasználói fiók létrehozása
`new-localuser <name>`
### <a name="verify-user-account-is-enabled"></a>Felhasználói fiók ellenőrzése engedélyezve
`(get-localuser | where {$_.SID -like "S-1-5-21-*-500"}).Enabled`

vagy 

`(get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'").Disabled`

`Get-LocalUser`2012 +-on érhető el. 2008R2 használata `Get-WmiObject`esetén. Ez a példa a beépített helyi rendszergazdai fiókot mutatja be, amely mindig rendelkezik SID `S-1-5-21-*-500`-vel. Az általánosított rendszerképből létrehozott Azure-beli virtuális gépek helyi rendszergazdai fiókja átnevezve lesz a virtuális gép üzembe helyezése során megadott névre. Így általában nem `Administrator`fog megjelenni.
### <a name="add-local-user-to-local-group"></a>Helyi felhasználó hozzáadása a helyi csoporthoz
`add-localgroupmember -group Administrators -member <username>`
### <a name="enable-local-user-account"></a>Helyi felhasználói fiók engedélyezése
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | enable-localuser` 

Ez a példa engedélyezi a beépített helyi rendszergazdai fiókot, amely mindig rendelkezik SID `S-1-5-21-*-500`-vel. Az általánosított rendszerképből létrehozott Azure-beli virtuális gépek helyi rendszergazdai fiókja átnevezve lesz a virtuális gép üzembe helyezése során megadott névre. Így általában nem `Administrator`fog megjelenni.
### <a name="view-user-account-properties"></a>Felhasználói fiók tulajdonságainak megtekintése
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | format-list *`

vagy 

`get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'" |  format-list Name,Disabled,Status,Lockout,Description,SID`

`Get-LocalUser`2012 +-on érhető el. 2008R2 használata `Get-WmiObject`esetén. Ez a példa a beépített helyi rendszergazdai fiókot mutatja be, amely mindig rendelkezik SID `S-1-5-21-*-500`-vel.
### <a name="view-local-groups"></a>Helyi csoportok megtekintése
`(get-localgroup).name | sort` `(get-wmiobject win32_group).Name | sort`

`Get-LocalUser`2012 +-on érhető el. 2008R2 használata `Get-WmiObject`esetén.
## <a name="manage-the-windows-event-log"></a>A Windows-Eseménynapló kezelése
### <a name="query-event-log-errors"></a>Eseménynaplók lekérdezése – hibák
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Level=2]]" | more`

Váltson `/c:10` a visszaadni kívánt eseményekre, vagy helyezze át a szűrőnek megfelelő összes esemény visszaküldéséhez.
### <a name="query-event-log-by-event-id"></a>Eseménynapló lekérdezése eseményazonosító szerint
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Eseménynapló lekérdezése eseményazonosító és szolgáltató szerint
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Eseménynapló lekérdezése eseményazonosító és szolgáltató szerint az elmúlt 24 órában
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

A `604800000` használatával 24 óra helyett 7 napig kereshet vissza. |
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Eseménynapló lekérdezése eseményazonosító, szolgáltató és EventData szerint az elmúlt 7 napban
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>Telepített alkalmazások megtekintése vagy eltávolítása
### <a name="list-installed-software"></a>Telepített szoftverek listázása
`get-wmiobject win32_product | select installdate,name | sort installdate -descending | more`
### <a name="uninstall-software"></a>Szoftver eltávolítása
`(get-wmiobject win32_product -filter "Name='<name>'").Uninstall()`
## <a name="file-system-management"></a>Fájlrendszer-kezelés
### <a name="get-file-version"></a>Fájl verziójának beolvasása
`(get-childitem $env:windir\system32\drivers\netvsc*.sys).VersionInfo.FileVersion`

Ez a példa a netvsc. sys, a netvsc63. sys vagy a netvsc60. sys nevű virtuális NIC-illesztőprogram verziószámát adja vissza a Windows-verziótól függően.
### <a name="download-and-extract-file"></a>Fájl letöltése és kibontása
`$path='c:\bin';md $path;cd $path;(new-object net.webclient).downloadfile( ('htTp:/'+'/download.sysinternals.com/files/SysinternalsSuite.zip'),"$path\SysinternalsSuite.zip");(new-object -com shelL.apPlication).namespace($path).CopyHere( (new-object -com shelL.apPlication).namespace("$path\SysinternalsSuite.zip").Items(),16)`

Ez a példa létrehoz `c:\bin` egy mappát, majd letölti és kicsomagolja a Sysinternals `c:\bin`csomagot a alkalmazásba.
## <a name="miscellaneous-tasks"></a>Egyéb feladatok
### <a name="show-os-version"></a>Operációs rendszer verziójának megjelenítése
`get-wmiobject win32_operatingsystem | format-list caption,version,buildnumber` 
### <a name="view-os-install-date"></a>Operációs rendszer telepítési dátumának megtekintése
`(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).installdate)`
### <a name="view-last-boot-time"></a>Utolsó rendszerindítás időpontjának megtekintése
`(get-wmiobject win32_operatingsystem).lastbootuptime`
### <a name="view-windows-uptime"></a>Windows üzemidő megtekintése
`"{0:dd}:{0:hh}:{0:mm}:{0:ss}.{0:ff}" -f ((get-date)-(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).lastbootuptime))`

Az üzemidőt `<days>:<hours>:<minutes>:<seconds>:<milliseconds>`adja vissza, például:. `49:16:48:00.00` 
### <a name="restart-windows"></a>Windows újraindítása
`restart-computer`

A `-force` hozzáadásával kényszerítheti az alkalmazások bezárását a figyelmeztetést nem használó felhasználók nélkül.
## <a name="instance-metadata"></a>Példány metaadatainak

Az Azure-példány metaadatait az Azure-beli virtuális gépről lekérdezve megtekintheti a részleteket, például a osType, a Location, a vmSize, a vmId, a name, a resourceGroupName, a subscriptionId, a Privateipaddress tulajdonságot és a publicIpAddress.

A példány metaadatainak lekérdezéséhez kifogástalan hálózati kapcsolatra van szükség, mivel az Azure-gazdagépen keresztül REST-hívást kezdeményez a példány metaadatainak szolgáltatásához. Tehát ha le tudja kérdezni a példány metaadatait, hogy a vendég képes legyen kommunikálni a hálózaton keresztül egy Azure által üzemeltetett szolgáltatás felé.

További információ: Azure- [példány metaadatainak szolgáltatása](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service).

### <a name="instance-metadata"></a>Példány metaadatainak
`$im = invoke-restmethod -headers @{"metadata"="true"} -uri http://169.254.169.254/metadata/instance?api-version=2017-08-01 -method get`

`$im | convertto-json`
### <a name="os-type-instance-metadata"></a>Operációs rendszer típusa (példány metaadatainak)
`$im.Compute.osType`
### <a name="location-instance-metadata"></a>Hely (példány metaadatainak)
`$im.Compute.Location`
### <a name="size-instance-metadata"></a>Méret (példány metaadatainak)
`$im.Compute.vmSize`
### <a name="vm-id-instance-metadata"></a>VM-azonosító (példány metaadatainak)
`$im.Compute.vmId`
### <a name="vm-name-instance-metadata"></a>Virtuális gép neve (példány metaadatainak)
`$im.Compute.name`
### <a name="resource-group-name-instance-metadata"></a>Erőforráscsoport neve (példány metaadatainak)
`$im.Compute.resourceGroupName`
### <a name="subscription-id-instance-metadata"></a>Előfizetés-azonosító (példány metaadatainak)
`$im.Compute.subscriptionId`
### <a name="tags-instance-metadata"></a>Címkék (példány metaadatainak)
`$im.Compute.tags`
### <a name="placement-group-id-instance-metadata"></a>Elhelyezési csoport azonosítója (példány metaadatainak)
`$im.Compute.placementGroupId`
### <a name="platform-fault-domain-instance-metadata"></a>Platform tartalék tartománya (példány metaadatainak)
`$im.Compute.platformFaultDomain`
### <a name="platform-update-domain-instance-metadata"></a>Platform frissítési tartománya (példány metaadatainak)
`$im.Compute.platformUpdateDomain`
### <a name="ipv4-private-ip-address-instance-metadata"></a>IPv4 Private IP Address (Instance Metadata)
`$im.network.interface.ipv4.ipAddress.privateIpAddress`
### <a name="ipv4-public-ip-address-instance-metadata"></a>IPv4 Public IP Address (Instance Metadata)
`$im.network.interface.ipv4.ipAddress.publicIpAddress`
### <a name="ipv4-subnet-address--prefix-instance-metadata"></a>IPv4-alhálózat címe/előtagja (példány metaadatainak)
`$im.network.interface.ipv4.subnet.address`

`$im.network.interface.ipv4.subnet.prefix`
### <a name="ipv6-ip-address-instance-metadata"></a>IPv6 IP Address (Instance Metadata)
`$im.network.interface.ipv6.ipAddress`
### <a name="mac-address-instance-metadata"></a>MAC-címek (példány metaadatainak)
`$im.network.interface.macAddress`

## <a name="next-steps"></a>További lépések
* [Itt](serial-console-windows.md)található a soros konzol fő Windows-dokumentációs lapja.
* A soros konzolon érhető el is [Linux](serial-console-linux.md) virtuális gépeket.
* Tudjon meg többet [rendszerindítási diagnosztika](boot-diagnostics.md).
