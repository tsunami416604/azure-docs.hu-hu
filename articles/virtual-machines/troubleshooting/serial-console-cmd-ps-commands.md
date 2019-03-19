---
title: CMD és a PowerShell használatával a Windows Azure virtuális gépek |} A Microsoft Docs
description: SAC belüli CMD és a PowerShell-parancs használata az Azure Windows virtuális gépek
services: virtual-machines-windows
documentationcenter: ''
author: alsin
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: 55b7e45bb9e600267e1dad0e36e9a97eca9a7d40
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58074998"
---
# <a name="windows-commands---cmd-and-powershell"></a>Windows-parancsok - CMD és a PowerShell használatával

Ez a szakasz a végrehajtható gyakori feladatok forgatókönyvekben, ahol szükség lehet a Windows virtuális gép, például az RDP-csatlakozási hibák elhárítása számíthat eléréséhez használható SAC példa parancsokat is tartalmaz.

SAC Windows Server 2003 óta a Windows összes verzióján tartalmaz, de alapértelmezés szerint le van tiltva. SAC támaszkodik a `sacdrv.sys` kernel-illesztőprogram, a `Special Administration Console Helper` szolgáltatás (`sacsvr`), és a `sacsess.exe` folyamat. További információkért lásd: [válságkezelési szolgáltatások eszközei és beállításai](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc787940(v%3dws.10)).

SAC lehetővé teszi, hogy a futó operációs rendszer soros porton keresztül csatlakozni. Ha elindítja az SAC, a CMD `sacsess.exe` elindítja `cmd.exe` a futó operációs rendszeren belül. Láthatja, hogy a feladat Manager időt meg ugyanabban a virtuális gép RDP-vel, ha csatlakoznak SAC a soros konzol funkciójával. SAC keresztül fér hozzá a CMD megegyezik `cmd.exe` RDP-n keresztül való csatlakozáskor használ. Ugyanazokat parancsok és eszközök érhetők el, beleértve az indítsa el a Powershellt, az adott CMD-példány. SAC jelentős különbség a és a Windows helyreállítási környezet (WinRE) a SAC van, ami lehetővé teszi a futó operációs rendszer, ahol WinRE indul-e egy másik, a minimális operációs rendszer kezelése. Azure virtuális gépek nem támogatják a soros konzol szolgáltatás a hozzáférést a WinRE, amíg az Azure virtuális gépek SAC keresztül kezelhetők.

Mivel nincs görgessen vissza a 80-as x 24 képernyőpufferen SAC korlátozódik, vegye fel `| more` parancsok egyszerre a kimenetet egy lap megjelenítéséhez. Használat `<spacebar>` megtekintéséhez a következő lapon vagy `<enter>` megtekintéséhez a következő sorban.  

`SHIFT+INSERT` a beillesztési parancsikont a soros konzol ablakából van.

SAC a korlátozott képernyőpufferen, mert hosszabb parancsok adjon meg egy helyi szövegszerkesztő egyszerűbb lehet, és ezután beillessze SAC.

## <a name="view-and-edit-windows-registry-settings"></a>Windows beállításjegyzék-beállításainak megtekintése és szerkesztése
### <a name="verify-rdp-is-enabled"></a>Ellenőrizze az RDP engedélyezve van
`reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections`

`reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections`

A második kulcsot (alatt \Policies) csak lesz található, ha a megfelelő csoportházirend-beállítás van konfigurálva.

### <a name="enable-rdp"></a>Engedélyezze az RDP-
`reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0`

`reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0` 

A második kulcsot (alatt \Policies) csak akkor szükséges, ha a megfelelő csoportházirend-beállítás volt konfigurálva. Érték a következő csoportházirend-frissítés felülíródik, ha erre van konfigurálva, a csoportházirend.

## <a name="manage-windows-services"></a>Windows-szolgáltatások kezelése

### <a name="view-service-state"></a>Szolgáltatás állapotának megtekintése
`sc query termservice`
###  <a name="view-service-logon-account"></a>Szolgáltatás bejelentkezési fiók megtekintése
`sc qc termservice`
### <a name="set-service-logon-account"></a>Állítsa be a szolgáltatás bejelentkezési fiók 
`sc config termservice obj= "NT Authority\NetworkService"`

Az egyenlőségjel után szóközt szükség.
### <a name="set-service-start-type"></a>Beállítása szolgáltatás indítási típusa
`sc config termservice start= demand` 

Az egyenlőségjel után szóközt szükség. Lehetséges kezdete értékeket tartalmazza `boot`, `system`, `auto`, `demand`, `disabled`, `delayed-auto`.
### <a name="set-service-dependencies"></a>Set függőségei
`sc config termservice depend= RPCSS`

Az egyenlőségjel után szóközt szükség.
### <a name="start-service"></a>Szolgáltatás indítása
`net start termservice`

vagy

`sc start termservice`
### <a name="stop-service"></a>Szolgáltatás leállítása
`net stop termservice`

vagy

`sc stop termservice`
## <a name="manage-networking-features"></a>A hálózati szolgáltatásokkal kezelése
### <a name="show-nic-properties"></a>Hálózati adapter tulajdonságainak megjelenítése
`netsh interface show interface` 
### <a name="show-ip-properties"></a>IP-tulajdonságok megjelenítése
`netsh interface ip show config`
### <a name="show-ipsec-configuration"></a>IPSec-konfiguráció megjelenítése
`netsh nap client show configuration`  
### <a name="enable-nic"></a>Hálózati adapter engedélyezése
`netsh interface set interface name="<interface name>" admin=enabled`
### <a name="set-nic-to-use-dhcp"></a>Állítsa be a DHCP használatára a hálózati adapter
`netsh interface ip set address name="<interface name>" source=dhcp`

További információ `netsh`, [ide](https://docs.microsoft.com/windows-server/networking/technologies/netsh/netsh-contexts).

Az Azure virtuális gépek mindig konfigurálni kell a vendég operációs rendszer IP-cím beszerzése a DHCP használatával. Az Azure statikus IP-beállítás a DHCP továbbra is használ a statikus IP-címet a virtuális gép számára.
### <a name="ping"></a>Ping
`ping 8.8.8.8` 
### <a name="port-ping"></a>Port ping  
A telnet-ügyfél telepítése

`dism /online /Enable-Feature /FeatureName:TelnetClient`

Kapcsolat tesztelése

`telnet bing.com 80`

A telnet-ügyfél eltávolítása

`dism /online /Disable-Feature /FeatureName:TelnetClient`

Ha korlátozott a Windows által választható módszerek alapértelmezés szerint, PowerShell portkapcsolatokat tesztelési esetleg jobb megközelítés is lehet. Tekintse meg a PowerShell szakaszt alább példákat.
### <a name="test-dns-name-resolution"></a>DNS-névfeloldás tesztelése
`nslookup bing.com`
### <a name="show-windows-firewall-rule"></a>Windows Firewall-rule show
`netsh advfirewall firewall show rule name="Remote Desktop - User Mode (TCP-In)"`
### <a name="disable-windows-firewall"></a>Windows tűzfal letiltása
`netsh advfirewall set allprofiles state off`

Hibaelhárításkor ideiglenesen zárja ki a Windows tűzfal is használhatja ezt a parancsot. Csak engedélyezze a következő újraindítás, vagy ha engedélyezi azt az alábbi paranccsal. Ne állítsa le a Windows tűzfalszolgáltatást (MPSSVC) vagy az Alap szűrőprogram (BFE) szolgáltatás, így zárja ki a Windows tűzfal. MPSSVC vagy BFE leállítása blokkolja az összes kapcsolat eredményez.
### <a name="enable-windows-firewall"></a>Windows tűzfal engedélyezése
`netsh advfirewall set allprofiles state on`
## <a name="manage-users-and-groups"></a>Felhasználók és csoportok kezelése
### <a name="create-local-user-account"></a>Helyi felhasználói fiók létrehozása
`net user /add <username> <password>`
### <a name="add-local-user-to-local-group"></a>Helyi felhasználó felvétele helyi csoportba
`net localgroup Administrators <username> /add`
### <a name="verify-user-account-is-enabled"></a>Ellenőrizze a felhasználói fiók engedélyezve van
`net user <username> | find /i "active"`

Általános rendszerkép alapján létrehozott Azure virtuális gépek a helyi rendszergazdai fiók új neve a virtuális gépek üzembe helyezésekor megadott név lesz. Így nem lehet `Administrator`.
### <a name="enable-user-account"></a>Felhasználói fiók engedélyezése
`net user <username> /active:yes`  
### <a name="view-user-account-properties"></a>Felhasználói fiók tulajdonságainak megtekintése
`net user <username>`

Egy helyi rendszergazdai fiók érdeklődésnek példasorok:

`Account active Yes`

`Account expires Never`

`Password expires Never`

`Workstations allowed All`

`Logon hours allowed All`

`Local Group Memberships *Administrators`

### <a name="view-local-groups"></a>Helyi csoportok megtekintése
`net localgroup`
## <a name="manage-the-windows-event-log"></a>A Windows Eseménynapló kezelése
### <a name="query-event-log-errors"></a>Lekérdezési Eseménynapló hibák
`wevtutil qe system /c:10 /f:text /q:"Event[System[Level=2]]" | more`

Változás `/c:10` , vagy helyezze vissza az összes esemény a szűrőnek megfelelő események kívánt száma.
### <a name="query-event-log-by-event-id"></a>Lekérdezés Eseménynapló esemény azonosítója
`wevtutil qe system /c:1 /f:text /q:"Event[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Eseménynapló esemény Azonosítóját, valamint a szolgáltató által lekérdezése
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Lekérdezés Eseménynapló esemény Azonosítóját, valamint a szolgáltató által az elmúlt 24 óra
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Használat `604800000` keresse ki az újra 7 nap, 24 óra helyett.
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Lekérdezés Eseménynapló eseményazonosító, a szolgáltató és a EventData által az elmúlt 7 napban
`wevtutil qe security /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>Megtekintheti, vagy távolítsa el a telepített alkalmazások
### <a name="list-installed-applications"></a>Alkalmazások telepítése listázása
`wmic product get Name,InstallDate | sort /r | more`

A `sort /r` szerint csökkenő rendezés telepítés dátuma, hogy könnyen látható, milyen nemrég lett telepítve. Használat `<spacebar>` , folytassa a következő lapra kimeneti vagy `<enter>` azt mutatja be egy sort a.
### <a name="uninstall-an-application"></a>Alkalmazás eltávolítása
`wmic path win32_product where name="<name>" call uninstall`

Cserélje le `<name>` adott vissza a fenti parancsban a el kívánja távolítani az alkalmazás nevét.

## <a name="file-system-management"></a>Fájlrendszer kezelése
### <a name="get-file-version"></a>Fájl-verzió beolvasása
`wmic datafile where "drive='C:' and path='\\windows\\system32\\drivers\\' and filename like 'netvsc%'" get version /format:list`

Ebben a példában a fájlverziót netvsc.sys, netvsc63.sys vagy a Windows-verziójától függően netvsc60.sys virtuális NIC-illesztőprogram adja vissza.
### <a name="scan-for-system-file-corruption"></a>Meghibásodott rendszerfájl keresése
`sfc /scannow`

Lásd még: [javítsa ki a Windows-lemezkép](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
### <a name="scan-for-system-file-corruption"></a>Meghibásodott rendszerfájl keresése
`dism /online /cleanup-image /scanhealth`

Lásd még: [javítsa ki a Windows-lemezkép](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
### <a name="export-file-permissions-to-text-file"></a>Fájl engedélyeinek exportálása szövegfájlba
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /t /c > %temp%\MachineKeys_permissions_before.txt`
### <a name="save-file-permissions-to-acl-file"></a>Fájlengedélyek mentése ACL-fájlba
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /save %temp%\MachineKeys_permissions_before.aclfile /t`  
### <a name="restore-file-permissions-from-acl-file"></a>Fájlengedélyek visszaállítása ACL-fájlból
`icacls %programdata%\Microsoft\Crypto\RSA /save %temp%\MachineKeys_permissions_before.aclfile /t`

Az elérési út használata esetén `/restore` kell lennie a használatakor a megadott mappa szülőmappáját `/save`. Ebben a példában `\RSA` a szülője a `\MachineKeys` megadott mappában a `/save` a fenti példa.
### <a name="take-ntfs-ownership-of-a-folder"></a>Saját tulajdonba vétele NTFS-mappa
`takeown /f %programdata%\Microsoft\Crypto\RSA\MachineKeys /a /r`  
### <a name="grant-ntfs-permissions-to-a-folder-recursively"></a>Egy mappa rekurzív NTFS-engedélyek megadása
`icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"`  
## <a name="manage-devices"></a>Eszközök kezelése
### <a name="remove-non-present-pnp-devices"></a>Nem található PNP eszközök eltávolítása
`%windir%\System32\RUNDLL32.exe %windir%\System32\pnpclean.dll,RunDLL_PnpClean /Devices /Maxclean`
## <a name="manage-group-policy"></a>A Csoportházirend kezelése
### <a name="force-group-policy-update"></a>Kényszerítheti a csoportházirend frissítése
`gpupdate /force /wait:-1`
## <a name="miscellaneous-tasks"></a>Egyéb feladatok
### <a name="show-os-version"></a>Operációsrendszer-verzió megjelenítése
`ver`

vagy 

`wmic os get caption,version,buildnumber /format:list`

vagy 

`systeminfo  find /i "os name"`

`systeminfo | findstr /i /r "os.*version.*build"`
### <a name="view-os-install-date"></a>Nézet operációsrendszer-telepítés dátuma
`systeminfo | find /i "original"`

vagy 

`wmic os get installdate`
### <a name="view-last-boot-time"></a>Nézet utolsó rendszerindítás ideje
`systeminfo | find /i "system boot time"`
### <a name="view-time-zone"></a>Nézet időzóna
`systeminfo | find /i "time zone"`

vagy

`wmic timezone get caption,standardname /format:list`
### <a name="restart-windows"></a>Indítsa újra a Windows
`shutdown /r /t 0`

Hozzáadás `/f` kényszeríti a futó alkalmazások bezárása nélkül figyelmezteti a felhasználót.
### <a name="detect-safe-mode-boot"></a>A csökkentett mód rendszerindítási észlelése
`bcdedit /enum | find /i "safeboot"` 

# <a name="windows-commands---powershell"></a>Windows-parancsok – PowerShell

SAC, a PowerShell futtatása után eléri egy parancssort, írja be:

`powershell <enter>`

> [!CAUTION]
> Távolítsa el a PSReadLine modul a PowerShell-munkamenetet más PowerShell-parancsok futtatása előtt. Nincs olyan ismert probléma, ahol znaky navíc bevezetésre kerülő szöveget a vágólapról beillesztett, ha a PowerShell-munkamenetben, SAC PSReadLine fut a.

Először ellenőrizze, ha be van-e töltve PSReadLine. A Windows Server 2016, Windows 10-es és újabb verzióiban a Windows alapértelmezés szerint töltődik be. Csak akkor kell jelen a Windows korábbi verzióiban, ha azt korábban manuálisan telepítette. 

Ha ez a parancs visszatér az semmilyen kimenet egy parancssort, majd a modul nem lett betöltve, és továbbra is használhatja a PowerShell-munkamenet SAC a szokásos módon.

`get-module psreadline`

Ha a fenti parancs a PSReadLine Modulverzió ad vissza, a következő parancsot a kikapcsoláshoz. Ez a parancs nem törli vagy eltávolítja a modult, akkor csak eltávolítja azt az aktuális PowerShell-munkamenetből.

`remove-module psreadline`

## <a name="view-and-edit-windows-registry-settings"></a>Windows beállításjegyzék-beállításainak megtekintése és szerkesztése
### <a name="verify-rdp-is-enabled"></a>Ellenőrizze az RDP engedélyezve van
`get-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections'`

`get-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections'`

A második kulcsot (alatt \Policies) csak lesz található, ha a megfelelő csoportházirend-beállítás van konfigurálva.
### <a name="enable-rdp"></a>Engedélyezze az RDP-
`set-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections' 0 -type dword`

`set-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections' 0 -type dword`

A második kulcsot (alatt \Policies) csak akkor szükséges, ha a megfelelő csoportházirend-beállítás volt konfigurálva. Érték a következő csoportházirend-frissítés felülíródik, ha erre van konfigurálva, a csoportházirend.
## <a name="manage-windows-services"></a>Windows-szolgáltatások kezelése
### <a name="view-service-details"></a>Szolgáltatás részleteinek megtekintése
`get-wmiobject win32_service -filter "name='termservice'" |  format-list Name,DisplayName,State,StartMode,StartName,PathName,ServiceType,Status,ExitCode,ServiceSpecificExitCode,ProcessId`

`Get-Service` lehet, de nem tartalmazza a szolgáltatás bejelentkezési fiókjához. `Get-WmiObject win32-service` hajtja végre.
### <a name="set-service-logon-account"></a>Állítsa be a szolgáltatás bejelentkezési fiók
`(get-wmiobject win32_service -filter "name='termservice'").Change($null,$null,$null,$null,$null,$false,'NT Authority\NetworkService')`

Ha nem használ szolgáltatásfiókot `NT AUTHORITY\LocalService`, `NT AUTHORITY\NetworkService`, vagy `LocalSystem`, adja meg a fiók jelszavát az elmúlt (nyolcadik) argumentum után a fiók nevét.
### <a name="set-service-startup-type"></a>Szolgáltatás indítási típusának beállítása
`set-service termservice -startuptype Manual`

`Set-service` fogadja el `Automatic`, `Manual`, vagy `Disabled` indítási típus.
### <a name="set-service-dependencies"></a>Set függőségei
`Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\TermService' -Name DependOnService -Value @('RPCSS','TermDD')`
### <a name="start-service"></a>Szolgáltatás indítása
`start-service termservice`
### <a name="stop-service"></a>Szolgáltatás leállítása
`stop-service termservice`
## <a name="manage-networking-features"></a>A hálózati szolgáltatásokkal kezelése
### <a name="show-nic-properties"></a>Hálózati adapter tulajdonságainak megjelenítése
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} |  format-list status,name,ifdesc,macadDresS,driverversion,MediaConNectState,MediaDuplexState`

vagy 

`get-wmiobject win32_networkadapter -filter "servicename='netvsc'" |  format-list netenabled,name,macaddress`

`Get-NetAdapter` elérhető a 2012 +, 2008R2 használatra `Get-WmiObject`.
### <a name="show-ip-properties"></a>IP-tulajdonságok megjelenítése
`get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'" |  format-list DNSHostName,IPAddress,DHCPEnabled,IPSubnet,DefaultIPGateway,MACAddress,DHCPServer,DNSServerSearchOrder`
### <a name="enable-nic"></a>Hálózati adapter engedélyezése
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | enable-netadapter`

vagy

`(get-wmiobject win32_networkadapter -filter "servicename='netvsc'").enable()`

`Get-NetAdapter` elérhető a 2012 +, 2008R2 használatra `Get-WmiObject`.
### <a name="set-nic-to-use-dhcp"></a>Állítsa be a DHCP használatára a hálózati adapter
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | Set-NetIPInterface -DHCP Enabled`

`(get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'").EnableDHCP()`

`Get-NetAdapter` a 2012 + érhető el. A 2008R2 használja `Get-WmiObject`. Az Azure virtuális gépek mindig konfigurálni kell a vendég operációs rendszer IP-cím beszerzése a DHCP használatával. Az Azure statikus IP-beállítás továbbra is használ a DHCP biztosíthat a IP-cím a virtuális géphez.
### <a name="ping"></a>Ping
`test-netconnection`

vagy

`get-wmiobject Win32_PingStatus -Filter 'Address="8.8.8.8"' | format-table -autosize IPV4Address,ReplySize,ResponseTime`

`Test-Netconnection` a paramétereket próbálja meg megpingelni nélkül `internetbeacon.msedge.net`. Érhető el a 2012 +. A 2008R2 használja `Get-WmiObject` a második példában látható módon.
### <a name="port-ping"></a>Port Ping
`test-netconnection -ComputerName bing.com -Port 80`

vagy

`(new-object Net.Sockets.TcpClient).BeginConnect('bing.com','80',$null,$null).AsyncWaitHandle.WaitOne(300)`

`Test-NetConnection` a 2012 + érhető el. A 2008R2 használata `Net.Sockets.TcpClient`
### <a name="test-dns-name-resolution"></a>DNS-névfeloldás tesztelése
`resolve-dnsname bing.com` 

vagy 

`[System.Net.Dns]::GetHostAddresses('bing.com')`

`Resolve-DnsName` a 2012 + érhető el. A 2008R2 használja `System.Net.DNS`.
### <a name="show-windows-firewall-rule-by-name"></a>Megjelenítése a Windows tűzfal szabály neve
`get-netfirewallrule -name RemoteDesktop-UserMode-In-TCP` 
### <a name="show-windows-firewall-rule-by-port"></a>Windows tűzfalszabály port show
`get-netfirewallportfilter | where {$_.localport -eq 3389} | foreach {Get-NetFirewallRule -Name $_.InstanceId} | format-list Name,Enabled,Profile,Direction,Action`

vagy

`(new-object -ComObject hnetcfg.fwpolicy2).rules | where {$_.localports -eq 3389 -and $_.direction -eq 1} | format-table Name,Enabled`

`Get-NetFirewallPortFilter` a 2012 + érhető el. A 2008R2 használja a `hnetcfg.fwpolicy2` COM-objektummal. 
### <a name="disable-windows-firewall"></a>Windows tűzfal letiltása
`Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False`

`Set-NetFirewallProfile` a 2012 + érhető el. A 2008R2 használja `netsh advfirewall` , hivatkozott CMD című fenti szakaszban.
## <a name="manage-users-and-groups"></a>Felhasználók és csoportok kezelése
### <a name="create-local-user-account"></a>Helyi felhasználói fiók létrehozása
`new-localuser <name>`
### <a name="verify-user-account-is-enabled"></a>Ellenőrizze a felhasználói fiók engedélyezve van
`(get-localuser | where {$_.SID -like "S-1-5-21-*-500"}).Enabled`

vagy 

`(get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'").Disabled`

`Get-LocalUser` a 2012 + érhető el. A 2008R2 használja `Get-WmiObject`. Ez a példa bemutatja a beépített helyi rendszergazdai fiók, amely mindig rendelkezik SID `S-1-5-21-*-500`. Általános rendszerkép alapján létrehozott Azure virtuális gépek a helyi rendszergazdai fiók új neve a virtuális gépek üzembe helyezésekor megadott név lesz. Így nem lehet `Administrator`.
### <a name="add-local-user-to-local-group"></a>Helyi felhasználó felvétele helyi csoportba
`add-localgroupmember -group Administrators -member <username>`
### <a name="enable-local-user-account"></a>Helyi felhasználói fiók engedélyezése
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | enable-localuser` 

Ebben a példában lehetővé teszi, hogy a beépített helyi rendszergazdai fiók, amely mindig rendelkezik SID `S-1-5-21-*-500`. Általános rendszerkép alapján létrehozott Azure virtuális gépek a helyi rendszergazdai fiók új neve a virtuális gépek üzembe helyezésekor megadott név lesz. Így nem lehet `Administrator`.
### <a name="view-user-account-properties"></a>Felhasználói fiók tulajdonságainak megtekintése
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | format-list *`

vagy 

`get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'" |  format-list Name,Disabled,Status,Lockout,Description,SID`

`Get-LocalUser` a 2012 + érhető el. A 2008R2 használja `Get-WmiObject`. Ez a példa bemutatja a beépített helyi rendszergazdai fiók, amely mindig rendelkezik SID `S-1-5-21-*-500`.
### <a name="view-local-groups"></a>Helyi csoportok megtekintése
`(get-localgroup).name | sort` `(get-wmiobject win32_group).Name | sort`

`Get-LocalUser` a 2012 + érhető el. A 2008R2 használja `Get-WmiObject`.
## <a name="manage-the-windows-event-log"></a>A Windows Eseménynapló kezelése
### <a name="query-event-log-errors"></a>Lekérdezési Eseménynapló hibák
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Level=2]]" | more`

Változás `/c:10` , vagy helyezze vissza az összes esemény a szűrőnek megfelelő események kívánt száma.
### <a name="query-event-log-by-event-id"></a>Lekérdezés Eseménynapló esemény azonosítója
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Eseménynapló esemény Azonosítóját, valamint a szolgáltató által lekérdezése
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Lekérdezés Eseménynapló esemény Azonosítóját, valamint a szolgáltató által az elmúlt 24 óra
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Használat `604800000` keresse ki az újra 7 nap, 24 óra helyett. |
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Lekérdezés Eseménynapló eseményazonosító, a szolgáltató és a EventData által az elmúlt 7 napban
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications"></a>Megtekintheti, vagy távolítsa el a telepített alkalmazások
### <a name="list-installed-software"></a>Lista telepített szoftver
`get-wmiobject win32_product | select installdate,name | sort installdate -descending | more`
### <a name="uninstall-software"></a>Szoftver eltávolítása
`(get-wmiobject win32_product -filter "Name='<name>'").Uninstall()`
## <a name="file-system-management"></a>Fájlrendszer kezelése
### <a name="get-file-version"></a>Fájl-verzió beolvasása
`(get-childitem $env:windir\system32\drivers\netvsc*.sys).VersionInfo.FileVersion`

Ebben a példában a fájlverziót netvsc.sys, netvsc63.sys vagy a Windows-verziójától függően netvsc60.sys nevű virtuális hálózati adapter illesztőprogram adja vissza.
### <a name="download-and-extract-file"></a>Töltse le és csomagolja ki fájlt
`$path='c:\bin';md $path;cd $path;(new-object net.webclient).downloadfile( ('htTp:/'+'/download.sysinternals.com/files/SysinternalsSuite.zip'),"$path\SysinternalsSuite.zip");(new-object -com shelL.apPlication).namespace($path).CopyHere( (new-object -com shelL.apPlication).namespace("$path\SysinternalsSuite.zip").Items(),16)`

Ez a példa létrehoz egy `c:\bin` mappát, majd letölti, és kinyeri a Sysinternals suite az eszközök `c:\bin`.
## <a name="miscellaneous-tasks"></a>Egyéb feladatok
### <a name="show-os-version"></a>Operációsrendszer-verzió megjelenítése
`get-wmiobject win32_operatingsystem | format-list caption,version,buildnumber` 
### <a name="view-os-install-date"></a>Nézet operációsrendszer-telepítés dátuma
`(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).installdate)`
### <a name="view-last-boot-time"></a>Nézet utolsó rendszerindítás ideje
`(get-wmiobject win32_operatingsystem).lastbootuptime`
### <a name="view-windows-uptime"></a>Windows-os üzemidő megtekintése
`"{0:dd}:{0:hh}:{0:mm}:{0:ss}.{0:ff}" -f ((get-date)-(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).lastbootuptime))`

Adja vissza, hasznos üzemidőt `<days>:<hours>:<minutes>:<seconds>:<milliseconds>`, például `49:16:48:00.00`. 
### <a name="restart-windows"></a>Indítsa újra a Windows
`restart-computer`

Hozzáadás `-force` kényszeríti a futó alkalmazások bezárása nélkül figyelmezteti a felhasználót.
## <a name="instance-metadata"></a>Instance Metadata

Azure-beli példány metaadataiból lekérdezheti, ha például osType, hely, vmSize, vmId, name, resourceGroupName, előfizetés-azonosító, privateIpAddress és publicIpAddress megtekintése az Azure virtuális gépen.

Kifogástalan állapotú Vendég hálózati kapcsolatot, példány metaadatok lekérdezése van szükség, mert így keresztül az Azure-gazdagéppel a instance metadata szolgáltatás REST-hívást. Ezért ha példány-metaadat lekérdezi, amely ismerteti a Vendég egy Azure-ban üzemeltetett szolgáltatáshoz a hálózaton keresztül kommunikálhat.

További információkért lásd: [Azure Instance Metadata szolgáltatás](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service).

### <a name="instance-metadata"></a>Példány-metaadat
`$im = invoke-restmethod -headers @{"metadata"="true"} -uri http://169.254.169.254/metadata/instance?api-version=2017-08-01 -method get`

`$im | convertto-json`
### <a name="os-type-instance-metadata"></a>Operációs rendszer típusa (Instance Metadata)
`$im.Compute.osType`
### <a name="location-instance-metadata"></a>Hely (Instance Metadata)
`$im.Compute.Location`
### <a name="size-instance-metadata"></a>Méret (Instance Metadata)
`$im.Compute.vmSize`
### <a name="vm-id-instance-metadata"></a>Virtuális gép azonosítója (Instance Metadata)
`$im.Compute.vmId`
### <a name="vm-name-instance-metadata"></a>Virtuális gép neve (példány metaadatai)
`$im.Compute.name`
### <a name="resource-group-name-instance-metadata"></a>Erőforráscsoport neve (példány metaadatai)
`$im.Compute.resourceGroupName`
### <a name="subscription-id-instance-metadata"></a>Előfizetés-azonosító (Instance Metadata)
`$im.Compute.subscriptionId`
### <a name="tags-instance-metadata"></a>Címkék (Instance Metadata)
`$im.Compute.tags`
### <a name="placement-group-id-instance-metadata"></a>Elhelyezési csoport azonosítója (Instance Metadata)
`$im.Compute.placementGroupId`
### <a name="platform-fault-domain-instance-metadata"></a>Platform tartalék tartományának (Instance Metadata)
`$im.Compute.platformFaultDomain`
### <a name="platform-update-domain-instance-metadata"></a>Platformfrissítési tartomány (Instance Metadata)
`$im.Compute.platformUpdateDomain`
### <a name="ipv4-private-ip-address-instance-metadata"></a>IPv4 Private IP Address (Instance Metadata)
`$im.network.interface.ipv4.ipAddress.privateIpAddress`
### <a name="ipv4-public-ip-address-instance-metadata"></a>IPv4 Public IP Address (Instance Metadata)
`$im.network.interface.ipv4.ipAddress.publicIpAddress`
### <a name="ipv4-subnet-address--prefix-instance-metadata"></a>IPv4 alhálózati cím / előtag (példány-metaadat)
`$im.network.interface.ipv4.subnet.address`

`$im.network.interface.ipv4.subnet.prefix`
### <a name="ipv6-ip-address-instance-metadata"></a>IPv6 IP Address (Instance Metadata)
`$im.network.interface.ipv6.ipAddress`
### <a name="mac-address-instance-metadata"></a>MAC-cím (Instance Metadata)
`$im.network.interface.macAddress`

## <a name="next-steps"></a>További lépések
* A soros konzol fő Windows dokumentációs oldal [Itt](serial-console-windows.md).
* A soros konzolon érhető el is [Linux](serial-console-linux.md) virtuális gépeket.
* Tudjon meg többet [rendszerindítási diagnosztika](boot-diagnostics.md).
