---
title: CMD és PowerShell az Azure Windows virtuális gépekben | Microsoft dokumentumok
description: CmD- és PowerShell-parancsok használata a SAC-on belül az Azure Windows virtuális gépeken
services: virtual-machines-windows
documentationcenter: ''
author: alsin
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 493340764f507c4fa364a5000f65cc232630b243
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77167027"
---
# <a name="windows-commands---cmd-and-powershell"></a>Windows-parancsok – CMD és PowerShell

Ez a szakasz példaparancsokat tartalmaz a gyakori feladatok végrehajtásához olyan esetekben, ahol szükség lehet a SAC használatára a Windows virtuális gép eléréséhez, például amikor az RDP-kapcsolat hibáit el kell hárítania.

A SAC a Windows Server 2003 óta a Windows összes verziójában megtalálható, de alapértelmezés szerint le van tiltva. A SAC a `sacdrv.sys` kernel-illesztőprogramra, a `Special Administration Console Helper` szolgáltatásra (`sacsvr`), és a `sacsess.exe` folyamatra támaszkodik. További információt a [Válságkezelési szolgáltatások eszközei és beállításai című témakörben talál.](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc787940(v%3dws.10))

A SAC lehetővé teszi, hogy soros porton keresztül csatlakozzon a futó operációs rendszerhez. Amikor elindítja a CMD-t a SAC-ból, `sacsess.exe` elindul a futó operációs rendszeren `cmd.exe` belül. Láthatja, hogy a Feladatkezelőben, ha RDP a virtuális gép egy időben csatlakozik a SAC a soros konzol funkció. A CMD keresztül érhető el `cmd.exe` SAC ugyanaz, ha csatlakozik RDP.The CMD you access via SAC is same you use when connected via RDP. Ugyanazok a parancsok és eszközök érhetők el, beleértve a PowerShell indításának lehetőségét az adott CMD-példányból. Ez egy jelentős különbség a SAC és a Windows Helyreállítási környezet (WinRE) az, hogy a SAC is lehetővé teszi, hogy kezelje a futó operációs rendszer, ahol WinRE csizma egy másik, minimális operációs rendszer. Bár az Azure virtuális gépek nem támogatják a WinRE elérésének lehetőségét, a soros konzol funkcióval az Azure virtuális gépek a SAC-on keresztül kezelhetők.

Mivel a SAC egy 80x24-es képernyőpufferre `| more` korlátozódik, és nincs visszagörgetés, adja hozzá a parancsokat a kimenet oldalról oldalról történő megjelenítéséhez. A `<spacebar>` következő oldal megtekintéséhez `<enter>` vagy a következő sor megtekintéséhez használható.

`SHIFT+INSERT`a soros konzolablak beillesztési parancsikonja.

A SAC korlátozott képernyőpuffere miatt a hosszabb parancsok at könnyebb lehet kiírni a helyi szövegszerkesztőbe, majd beilleszteni a SAC-ba.

## <a name="view-and-edit-windows-registry-settings"></a>A Windows beállításjegyzék-beállításainak megtekintése és szerkesztése
### <a name="verify-rdp-is-enabled"></a>Az RDP engedélyezésének ellenőrzése
`reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections`

`reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections`

A második kulcs (a \Policies alatt) csak akkor létezik, ha a megfelelő csoportházirend-beállítás be van állítva.

### <a name="enable-rdp"></a>RdP engedélyezése
`reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0`

`reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0`

A második kulcsra (a \Policies) csak akkor lenne szükség, ha a megfelelő csoportházirend-beállítás konfigurálva lett volna. Az érték újraírásra kerül a következő csoportházirend-frissítéskor, ha csoportházirendben van konfigurálva.

## <a name="manage-windows-services"></a>A Windows-szolgáltatások kezelése

### <a name="view-service-state"></a>Szolgáltatás állapotának megtekintése
`sc query termservice`
###  <a name="view-service-logon-account"></a>Szolgáltatásbejelentkezési fiók megtekintése
`sc qc termservice`
### <a name="set-service-logon-account"></a>Szolgáltatás bejelentkezési fiókjának beállítása
`sc config termservice obj= "NT Authority\NetworkService"`

Az egyenlőségjel után szóköz szükséges.
### <a name="set-service-start-type"></a>Szolgáltatás indítási típusának beállítása
`sc config termservice start= demand`

Az egyenlőségjel után szóköz szükséges. A lehetséges `boot`kezdőértékek a `delayed-auto`következők: , `system` `auto`, `demand`, , `disabled`, .
### <a name="set-service-dependencies"></a>Szolgáltatásfüggőségek beállítása
`sc config termservice depend= RPCSS`

Az egyenlőségjel után szóköz szükséges.
### <a name="start-service"></a>Szolgáltatás indítása
`net start termservice`

vagy

`sc start termservice`
### <a name="stop-service"></a>Szolgáltatás leállítása
`net stop termservice`

vagy

`sc stop termservice`
## <a name="manage-networking-features"></a>Hálózati szolgáltatások kezelése
### <a name="show-nic-properties"></a>Hálózati adapter tulajdonságainak megjelenítése
`netsh interface show interface`
### <a name="show-ip-properties"></a>IP-tulajdonságok megjelenítése
`netsh interface ip show config`
### <a name="show-ipsec-configuration"></a>IPSec-konfiguráció megjelenítése
`netsh nap client show configuration`
### <a name="enable-nic"></a>Hálózati adapter engedélyezése
`netsh interface set interface name="<interface name>" admin=enabled`
### <a name="set-nic-to-use-dhcp"></a>A hálózati adapter beállítása a DHCP használatára
`netsh interface ip set address name="<interface name>" source=dhcp`

További információ `netsh`érti, [kattintson ide](https://docs.microsoft.com/windows-server/networking/technologies/netsh/netsh-contexts).

Az Azure virtuális gépek mindig konfigurálva kell lennie a vendég operációs rendszer DHCP-cím megszerzéséhez. Az Azure statikus IP-beállítás továbbra is DHCP-t használ a statikus IP-cím a virtuális gépnek.
### <a name="ping"></a>Ping
`ping 8.8.8.8`
### <a name="port-ping"></a>Port pingelése
A telnet-ügyfél telepítése

`dism /online /Enable-Feature /FeatureName:TelnetClient`

Kapcsolat tesztelése

`telnet bing.com 80`

A telnet-ügyfél eltávolítása

`dism /online /Disable-Feature /FeatureName:TelnetClient`

Ha alapértelmezés szerint a Windows rendszerben elérhető módszerekre korlátozódik, a PowerShell jobb megközelítés lehet a portkapcsolat teszteléséhez. Tekintse meg az alábbi PowerShell szakaszban a példákat.
### <a name="test-dns-name-resolution"></a>DNS-névfeloldás ának tesztelése
`nslookup bing.com`
### <a name="show-windows-firewall-rule"></a>A Windows tűzfal szabályának megjelenítése
`netsh advfirewall firewall show rule name="Remote Desktop - User Mode (TCP-In)"`
### <a name="disable-windows-firewall"></a>A Windows tűzfal letiltása
`netsh advfirewall set allprofiles state off`

Ezt a parancsot hibaelhárításkor a Windows tűzfal ideiglenes kizárásához használhatja. Ez lesz engedélyezve a következő újraindítás, vagy ha engedélyezi azt az alábbi parancsot. A Windows tűzfal kizárása érdekében ne állítsa le a Windows tűzfal (MPSSVC) vagy az Base Filtering Engine (BFE) szolgáltatást. Az MPSSVC vagy a BFE leállítása az összes kapcsolat blokkolását eredményezi.
### <a name="enable-windows-firewall"></a>A Windows tűzfal engedélyezése
`netsh advfirewall set allprofiles state on`
## <a name="manage-users-and-groups"></a>Felhasználók és csoportok kezelése
### <a name="create-local-user-account"></a>Helyi felhasználói fiók létrehozása
`net user /add <username> <password>`
### <a name="add-local-user-to-local-group"></a>Helyi felhasználó hozzáadása a helyi csoporthoz
`net localgroup Administrators <username> /add`
### <a name="verify-user-account-is-enabled"></a>Annak ellenőrzése, hogy a felhasználói fiók engedélyezve van-e
`net user <username> | find /i "active"`

Az általánosított lemezképből létrehozott Azure virtuális gépek a helyi rendszergazdai fiók átnevezése a virtuális gép kiépítése során megadott névre lesz átnevezve. Tehát ez általában `Administrator`nem lesz .
### <a name="enable-user-account"></a>Felhasználói fiók engedélyezése
`net user <username> /active:yes`
### <a name="view-user-account-properties"></a>Felhasználói fiók tulajdonságainak megtekintése
`net user <username>`

Példa érdeklődési sorokra egy helyi rendszergazdai fiókból:

`Account active Yes`

`Account expires Never`

`Password expires Never`

`Workstations allowed All`

`Logon hours allowed All`

`Local Group Memberships *Administrators`

### <a name="view-local-groups"></a>Helyi csoportok megtekintése
`net localgroup`
## <a name="manage-the-windows-event-log"></a>A Windows eseménynaplójának kezelése
### <a name="query-event-log-errors"></a>Lekérdezési eseménynapló hibái
`wevtutil qe system /c:10 /f:text /q:"Event[System[Level=2]]" | more`

Módosítsa `/c:10` a kívánt számú eseményt a visszatéréshez, vagy helyezze át a szűrőnek megfelelő összes esemény visszaadásához.
### <a name="query-event-log-by-event-id"></a>Lekérdezési eseménynapló eseményazonosító szerint
`wevtutil qe system /c:1 /f:text /q:"Event[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Lekérdezési eseménynapló eseményazonosító és szolgáltató szerint
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Lekérdezési eseménynapló eseményazonosító és szolgáltató szerint az elmúlt 24 órában
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

24 óra helyett 7 napra való visszatekintésre használható. `604800000`
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Lekérdezési eseménynapló eseményazonosító, szolgáltató és EventData szerint az elmúlt 7 napban
`wevtutil qe security /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>Telepített alkalmazások megtekintése és eltávolítása
### <a name="list-installed-applications"></a>Telepített alkalmazások listázása
`wmic product get Name,InstallDate | sort /r | more`

A `sort /r` rendezés csökkenő telepítési dátum, hogy könnyen látni, mi volt a közelmúltban telepítve. A `<spacebar>` kimenet következő oldalára, vagy `<enter>` egy sor előrelépésére használható.
### <a name="uninstall-an-application"></a>Alkalmazás eltávolítása
`wmic path win32_product where name="<name>" call uninstall`

Cserélje `<name>` le az eltávolítani kívánt alkalmazás fenti parancsában visszaadott névre.

## <a name="file-system-management"></a>Fájlrendszer-kezelés
### <a name="get-file-version"></a>Fájlverzió beszereznie
`wmic datafile where "drive='C:' and path='\\windows\\system32\\drivers\\' and filename like 'netvsc%'" get version /format:list`

Ez a példa a virtuális hálózati adapter illesztőprogramjának fájlverzióját adja vissza, amely a Windows-verziótól függően netvsc.sys, netvsc63.sys vagy netvsc60.sys.
### <a name="scan-for-system-file-corruption"></a>Rendszerfájl-sérülés ellenőrzése
`sfc /scannow`

Lásd még: [Windows-lemezkép javítása](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
### <a name="scan-for-system-file-corruption"></a>Rendszerfájl-sérülés ellenőrzése
`dism /online /cleanup-image /scanhealth`

Lásd még: [Windows-lemezkép javítása](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
### <a name="export-file-permissions-to-text-file"></a>Fájlengedélyek exportálása szövegfájlba
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /t /c > %temp%\MachineKeys_permissions_before.txt`
### <a name="save-file-permissions-to-acl-file"></a>Fájlengedélyek mentése ACL-fájlba
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /save %temp%\MachineKeys_permissions_before.aclfile /t`
### <a name="restore-file-permissions-from-acl-file"></a>Fájlengedélyek visszaállítása az ACL-fájlból
`icacls %programdata%\Microsoft\Crypto\RSA /save %temp%\MachineKeys_permissions_before.aclfile /t`

A használat `/restore` során megadott elérési útnak a használata `/save`során megadott mappa szülőmappájának kell lennie. Ebben a `\RSA` példában a `\MachineKeys` fenti `/save` példában megadott mappa szülője.
### <a name="take-ntfs-ownership-of-a-folder"></a>Mappa NTFS-tulajdonjogának átvétele
`takeown /f %programdata%\Microsoft\Crypto\RSA\MachineKeys /a /r`
### <a name="grant-ntfs-permissions-to-a-folder-recursively"></a>NTFS-engedélyek megadása ismétlődően egy mappához
`icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"`
## <a name="manage-devices"></a>Eszközök kezelése
### <a name="remove-non-present-pnp-devices"></a>A nem jelen lévő PNP-eszközök eltávolítása
`%windir%\System32\RUNDLL32.exe %windir%\System32\pnpclean.dll,RunDLL_PnpClean /Devices /Maxclean`
## <a name="manage-group-policy"></a>Csoportházirend kezelése
### <a name="force-group-policy-update"></a>Csoportházirend frissítésének kényszerítése
`gpupdate /force /wait:-1`
## <a name="miscellaneous-tasks"></a>Egyéb feladatok
### <a name="show-os-version"></a>Az operációs rendszer verziójának megjelenítése
`ver`

vagy

`wmic os get caption,version,buildnumber /format:list`

vagy

`systeminfo  find /i "os name"`

`systeminfo | findstr /i /r "os.*version.*build"`
### <a name="view-os-install-date"></a>Az operációs rendszer telepítésének dátumának megtekintése
`systeminfo | find /i "original"`

vagy

`wmic os get installdate`
### <a name="view-last-boot-time"></a>Az utolsó rendszerindítási idő megtekintése
`systeminfo | find /i "system boot time"`
### <a name="view-time-zone"></a>Időzóna megtekintése
`systeminfo | find /i "time zone"`

vagy

`wmic timezone get caption,standardname /format:list`
### <a name="restart-windows"></a>A Windows újraindítása
`shutdown /r /t 0`

A `/f` hozzáadás arra kényszeríti a futó alkalmazásokat, hogy figyelmeztetés nélkül zárják be őket.
### <a name="detect-safe-mode-boot"></a>Csökkentett mód ú rendszerindítás észlelése
`bcdedit /enum | find /i "safeboot"`

## <a name="windows-commands---powershell"></a>Windows-parancsok – PowerShell

A PowerShell sac-ban való futtatásához a CMD-parancssor elérése után írja be a következőt:

`powershell <enter>`

> [!CAUTION]
> Bármely más PowerShell-parancs futtatása előtt távolítsa el a PSReadLine modult a PowerShell-munkamenetből. Van egy ismert probléma, amely miatt további karakterek et lehet bevezetni a vágólapról beillesztett szövegben, ha a PSReadLine a SAC PowerShell-munkamenetében fut.

Először ellenőrizze, hogy a PSReadLine be van-e töltve. Alapértelmezés szerint a Windows Server 2016, a Windows 10 és a Windows újabb verzióiban töltődik be. Ez csak akkor jelen iktatható a korábbi Windows verziókban, ha manuálisan lett volna telepítve.

Ha ez a parancs kimenet nélküli parancssorba visszatér, akkor a modul nem töltődik be, és a sac-ban folytathatja a PowerShell-munkamenet normál használatát.

`get-module psreadline`

Ha a fenti parancs a PSReadLine modul verzióját adja vissza, futtassa a következő parancsot a kiürítéséhez. Ez a parancs nem törli vagy távolítja el a modult, csak az aktuális PowerShell-munkamenetből távolítja el azt.

`remove-module psreadline`

## <a name="view-and-edit-windows-registry-settings"></a>A Windows beállításjegyzék-beállításainak megtekintése és szerkesztése
### <a name="verify-rdp-is-enabled"></a>Az RDP engedélyezésének ellenőrzése
`get-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections'`

`get-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections'`

A második kulcs (a \Policies alatt) csak akkor létezik, ha a megfelelő csoportházirend-beállítás be van állítva.
### <a name="enable-rdp"></a>RdP engedélyezése
`set-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections' 0 -type dword`

`set-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections' 0 -type dword`

A második kulcsra (a \Policies) csak akkor lenne szükség, ha a megfelelő csoportházirend-beállítás konfigurálva lett volna. Az érték újraírásra kerül a következő csoportházirend-frissítéskor, ha csoportházirendben van konfigurálva.
## <a name="manage-windows-services"></a>A Windows-szolgáltatások kezelése
### <a name="view-service-details"></a>Szolgáltatás részleteinek megtekintése
`get-wmiobject win32_service -filter "name='termservice'" |  format-list Name,DisplayName,State,StartMode,StartName,PathName,ServiceType,Status,ExitCode,ServiceSpecificExitCode,ProcessId`

`Get-Service`használható, de nem tartalmazza a szolgáltatás bejelentkezési fiókját. `Get-WmiObject win32-service`Nem.
### <a name="set-service-logon-account"></a>Szolgáltatás bejelentkezési fiókjának beállítása
`(get-wmiobject win32_service -filter "name='termservice'").Change($null,$null,$null,$null,$null,$false,'NT Authority\NetworkService')`

Ha a `NT AUTHORITY\LocalService` `NT AUTHORITY\NetworkService`vagytól eltérő szolgáltatásfiókot használ, vagy `LocalSystem`a , adja meg a fiók jelszavát a fiók név utáni utolsó (nyolcadik) argumentumként.
### <a name="set-service-startup-type"></a>Szolgáltatás indítási típusának beállítása
`set-service termservice -startuptype Manual`

`Set-service`elfogadja `Automatic`a `Manual`, `Disabled` vagy indítási típushoz.
### <a name="set-service-dependencies"></a>Szolgáltatásfüggőségek beállítása
`Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\TermService' -Name DependOnService -Value @('RPCSS','TermDD')`
### <a name="start-service"></a>Szolgáltatás indítása
`start-service termservice`
### <a name="stop-service"></a>Szolgáltatás leállítása
`stop-service termservice`
## <a name="manage-networking-features"></a>Hálózati szolgáltatások kezelése
### <a name="show-nic-properties"></a>Hálózati adapter tulajdonságainak megjelenítése
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} |  format-list status,name,ifdesc,macadDresS,driverversion,MediaConNectState,MediaDuplexState`

vagy

`get-wmiobject win32_networkadapter -filter "servicename='netvsc'" |  format-list netenabled,name,macaddress`

`Get-NetAdapter`2012+-ban kapható, 2008R2 `Get-WmiObject`használatra.
### <a name="show-ip-properties"></a>IP-tulajdonságok megjelenítése
`get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'" |  format-list DNSHostName,IPAddress,DHCPEnabled,IPSubnet,DefaultIPGateway,MACAddress,DHCPServer,DNSServerSearchOrder`
### <a name="enable-nic"></a>Hálózati adapter engedélyezése
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | enable-netadapter`

vagy

`(get-wmiobject win32_networkadapter -filter "servicename='netvsc'").enable()`

`Get-NetAdapter`2012+-ban kapható, 2008R2 `Get-WmiObject`használatra.
### <a name="set-nic-to-use-dhcp"></a>A hálózati adapter beállítása a DHCP használatára
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | Set-NetIPInterface -DHCP Enabled`

`(get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'").EnableDHCP()`

`Get-NetAdapter`2012+-án érhető el. 2008R2 esetén. `Get-WmiObject` Az Azure virtuális gépek mindig konfigurálva kell lennie a vendég operációs rendszer DHCP-cím megszerzéséhez. Az Azure statikus IP-beállítás továbbra is dhcp-t használ az IP-cím a virtuális gépnek.
### <a name="ping"></a>Ping
`test-netconnection`

> [!NOTE]
> Előfordulhat, hogy a Write-Progress parancsmag nem működik ezzel a paranccsal. A megoldás, futtathatja `$ProgressPreference = "SilentlyContinue"` a PowerShell letilthatja a folyamatjelző sáv.

vagy

`get-wmiobject Win32_PingStatus -Filter 'Address="8.8.8.8"' | format-table -autosize IPV4Address,ReplySize,ResponseTime`

`Test-Netconnection`paraméterek nélkül megpróbálja ping `internetbeacon.msedge.net`. 2012+-án érhető el. A 2008R2 `Get-WmiObject` használata, mint a második példában.
### <a name="port-ping"></a>Port pingelése
`test-netconnection -ComputerName bing.com -Port 80`

vagy

`(new-object Net.Sockets.TcpClient).BeginConnect('bing.com','80',$null,$null).AsyncWaitHandle.WaitOne(300)`

`Test-NetConnection`2012+-án érhető el. 2008R2-használatához`Net.Sockets.TcpClient`
### <a name="test-dns-name-resolution"></a>DNS-névfeloldás ának tesztelése
`resolve-dnsname bing.com`

vagy

`[System.Net.Dns]::GetHostAddresses('bing.com')`

`Resolve-DnsName`2012+-án érhető el. 2008R2 esetén. `System.Net.DNS`
### <a name="show-windows-firewall-rule-by-name"></a>A Windows tűzfalszabály megjelenítése név szerint
`get-netfirewallrule -name RemoteDesktop-UserMode-In-TCP`
### <a name="show-windows-firewall-rule-by-port"></a>Windows tűzfalszabály megjelenítése port szerint
`get-netfirewallportfilter | where {$_.localport -eq 3389} | foreach {Get-NetFirewallRule -Name $_.InstanceId} | format-list Name,Enabled,Profile,Direction,Action`

vagy

`(new-object -ComObject hnetcfg.fwpolicy2).rules | where {$_.localports -eq 3389 -and $_.direction -eq 1} | format-table Name,Enabled`

`Get-NetFirewallPortFilter`2012+-án érhető el. 2008R2 esetén `hnetcfg.fwpolicy2` használja a COM objektumot.
### <a name="disable-windows-firewall"></a>Windows tűzfal letiltása
`Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False`

`Set-NetFirewallProfile`2012+-án érhető el. 2008R2 esetében `netsh advfirewall` a fenti CMD szakaszban hivatkozott módon kell használni.
## <a name="manage-users-and-groups"></a>Felhasználók és csoportok kezelése
### <a name="create-local-user-account"></a>Helyi felhasználói fiók létrehozása
`new-localuser <name>`
### <a name="verify-user-account-is-enabled"></a>Annak ellenőrzése, hogy a felhasználói fiók engedélyezve van-e
`(get-localuser | where {$_.SID -like "S-1-5-21-*-500"}).Enabled`

vagy

`(get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'").Disabled`

`Get-LocalUser`2012+-án érhető el. 2008R2 esetén. `Get-WmiObject` Ez a példa a beépített helyi rendszergazdai `S-1-5-21-*-500`fiókot mutatja be, amely mindig SID-vel rendelkezik. Az általánosított lemezképből létrehozott Azure virtuális gépek a helyi rendszergazdai fiók átnevezése a virtuális gép kiépítése során megadott névre lesz átnevezve. Tehát ez általában `Administrator`nem lesz .
### <a name="add-local-user-to-local-group"></a>Helyi felhasználó hozzáadása a helyi csoporthoz
`add-localgroupmember -group Administrators -member <username>`
### <a name="enable-local-user-account"></a>Helyi felhasználói fiók engedélyezése
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | enable-localuser`

Ez a példa engedélyezi a beépített helyi rendszergazdai fiókot, amely mindig sid-vel `S-1-5-21-*-500`rendelkezik. Az általánosított lemezképből létrehozott Azure virtuális gépek a helyi rendszergazdai fiók átnevezése a virtuális gép kiépítése során megadott névre lesz átnevezve. Tehát ez általában `Administrator`nem lesz .
### <a name="view-user-account-properties"></a>Felhasználói fiók tulajdonságainak megtekintése
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | format-list *`

vagy

`get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'" |  format-list Name,Disabled,Status,Lockout,Description,SID`

`Get-LocalUser`2012+-án érhető el. 2008R2 esetén. `Get-WmiObject` Ez a példa a beépített helyi rendszergazdai `S-1-5-21-*-500`fiókot mutatja be, amely mindig SID-vel rendelkezik.
### <a name="view-local-groups"></a>Helyi csoportok megtekintése
`(get-localgroup).name | sort` `(get-wmiobject win32_group).Name | sort`

`Get-LocalUser`2012+-án érhető el. 2008R2 esetén. `Get-WmiObject`
## <a name="manage-the-windows-event-log"></a>A Windows eseménynaplójának kezelése
### <a name="query-event-log-errors"></a>Lekérdezési eseménynapló hibái
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Level=2]]" | more`

Módosítsa `/c:10` a kívánt számú eseményt a visszatéréshez, vagy helyezze át a szűrőnek megfelelő összes esemény visszaadásához.
### <a name="query-event-log-by-event-id"></a>Lekérdezési eseménynapló eseményazonosító szerint
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Lekérdezési eseménynapló eseményazonosító és szolgáltató szerint
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Lekérdezési eseménynapló eseményazonosító és szolgáltató szerint az elmúlt 24 órában
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

24 óra helyett 7 napra való visszatekintésre használható. `604800000` |
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Lekérdezési eseménynapló eseményazonosító, szolgáltató és EventData szerint az elmúlt 7 napban
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>Telepített alkalmazások megtekintése és eltávolítása
### <a name="list-installed-software"></a>Telepített szoftverek listázása
`get-wmiobject win32_product | select installdate,name | sort installdate -descending | more`
### <a name="uninstall-software"></a>Szoftver eltávolítása
`(get-wmiobject win32_product -filter "Name='<name>'").Uninstall()`
## <a name="file-system-management"></a>Fájlrendszer-kezelés
### <a name="get-file-version"></a>Fájlverzió beszereznie
`(get-childitem $env:windir\system32\drivers\netvsc*.sys).VersionInfo.FileVersion`

Ez a példa a netvsc.sys, netvsc63.sys vagy netvsc60.sys nevű virtuális hálózati adapter illesztőprogram-illesztőprogram fájlverzióját adja vissza a Windows verziójától függően.
### <a name="download-and-extract-file"></a>Fájl letöltése és kibontása
`$path='c:\bin';md $path;cd $path;(new-object net.webclient).downloadfile( ('htTp:/'+'/download.sysinternals.com/files/SysinternalsSuite.zip'),"$path\SysinternalsSuite.zip");(new-object -com shelL.apPlication).namespace($path).CopyHere( (new-object -com shelL.apPlication).namespace("$path\SysinternalsSuite.zip").Items(),16)`

Ez a `c:\bin` példa létrehoz egy mappát, majd letölti és kibontja a Sysinternals eszközcsomagot a rendszerbe. `c:\bin`
## <a name="miscellaneous-tasks"></a>Egyéb feladatok
### <a name="show-os-version"></a>Az operációs rendszer verziójának megjelenítése
`get-wmiobject win32_operatingsystem | format-list caption,version,buildnumber`
### <a name="view-os-install-date"></a>Az operációs rendszer telepítésének dátumának megtekintése
`(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).installdate)`
### <a name="view-last-boot-time"></a>Az utolsó rendszerindítási idő megtekintése
`(get-wmiobject win32_operatingsystem).lastbootuptime`
### <a name="view-windows-uptime"></a>A Windows állásidejének megtekintése
`"{0:dd}:{0:hh}:{0:mm}:{0:ss}.{0:ff}" -f ((get-date)-(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).lastbootuptime))`

Az uptime-ot `<days>:<hours>:<minutes>:<seconds>:<milliseconds>` `49:16:48:00.00`például .
### <a name="restart-windows"></a>A Windows újraindítása
`restart-computer`

A `-force` hozzáadás arra kényszeríti a futó alkalmazásokat, hogy figyelmeztetés nélkül zárják be őket.
## <a name="instance-metadata"></a>Példány metaadatai

Az Azure-példány metaadatait az Azure virtuális gépen belül lekérdezheti olyan részletek megtekintéséhez, mint az osType, a Hely, a vmSize, a vmId, a name, a resourceGroupName, az előfizetési azonosító, a privateIpAddress és a publicIpAddress.

A példány metaadatainak lekérdezése kifogástalan vendéghálózati kapcsolatot igényel, mert rest-hívást hoz létre az Azure-gazdagépen keresztül a példány metaadat-szolgáltatásához. Így ha képes lekérdezni a példány metaadatait, amely azt jelzi, hogy a vendég képes kommunikálni a hálózaton keresztül egy Azure által üzemeltetett szolgáltatás.

További információ: [Azure Instance Metadata service](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service).

### <a name="instance-metadata"></a>Példány metaadatai
`$im = invoke-restmethod -headers @{"metadata"="true"} -uri http://169.254.169.254/metadata/instance?api-version=2017-08-01 -method get`

`$im | convertto-json`
### <a name="os-type-instance-metadata"></a>Operációs rendszer típusa (példány metaadatai)
`$im.Compute.osType`
### <a name="location-instance-metadata"></a>Hely (példány metaadatai)
`$im.Compute.Location`
### <a name="size-instance-metadata"></a>Méret (példány metaadatai)
`$im.Compute.vmSize`
### <a name="vm-id-instance-metadata"></a>Virtuálisgép-azonosító (példány metaadatai)
`$im.Compute.vmId`
### <a name="vm-name-instance-metadata"></a>Virtuális gép neve (példány metaadatai)
`$im.Compute.name`
### <a name="resource-group-name-instance-metadata"></a>Erőforráscsoport neve (példány metaadatai)
`$im.Compute.resourceGroupName`
### <a name="subscription-id-instance-metadata"></a>Előfizetés-azonosító (példány metaadatai)
`$im.Compute.subscriptionId`
### <a name="tags-instance-metadata"></a>Címkék (példány metaadatai)
`$im.Compute.tags`
### <a name="placement-group-id-instance-metadata"></a>Elhelyezési csoport azonosítója (példány metaadatai)
`$im.Compute.placementGroupId`
### <a name="platform-fault-domain-instance-metadata"></a>Platformhiba-tartomány (példány metaadatai)
`$im.Compute.platformFaultDomain`
### <a name="platform-update-domain-instance-metadata"></a>Platformfrissítési tartomány (példány metaadatai)
`$im.Compute.platformUpdateDomain`
### <a name="ipv4-private-ip-address-instance-metadata"></a>IPv4-személyes IP-cím (példánymetaadatok)
`$im.network.interface.ipv4.ipAddress.privateIpAddress`
### <a name="ipv4-public-ip-address-instance-metadata"></a>IPv4 nyilvános IP-cím (példány metaadatai)
`$im.network.interface.ipv4.ipAddress.publicIpAddress`
### <a name="ipv4-subnet-address--prefix-instance-metadata"></a>IPv4-alhálózati cím / előtag (példánymetaadatok)
`$im.network.interface.ipv4.subnet.address`

`$im.network.interface.ipv4.subnet.prefix`
### <a name="ipv6-ip-address-instance-metadata"></a>IPv6 IP-cím (példány metaadatai)
`$im.network.interface.ipv6.ipAddress`
### <a name="mac-address-instance-metadata"></a>MAC-cím (példány metaadatai)
`$im.network.interface.macAddress`

## <a name="next-steps"></a>További lépések
* A fő soros konzol Windows dokumentációs lap [itt](serial-console-windows.md)található .
* A soros konzol [linuxos](serial-console-linux.md) virtuális gépekhez is elérhető.
* További információ a [rendszerindítási diagnosztikáról](boot-diagnostics.md).
