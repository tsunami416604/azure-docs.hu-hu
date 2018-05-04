---
title: Azure virtuális gép soros konzol |} Microsoft Docs
description: Kétirányú soros konzol az Azure virtuális gépek.
services: virtual-machines-windows
documentationcenter: ''
author: harijay
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/05/2018
ms.author: harijay
ms.openlocfilehash: e891e9c9fd87f370f0c98639ff0c6fc5b8cc81af
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="virtual-machine-serial-console-preview"></a>Virtuális gép soros konzolon (előzetes verzió) 


A virtuális gép soros konzol az Azure-on Linux és a Windows virtuális gépek egy szöveges konzol hozzáférést biztosít. A soros kapcsolat COM1 soros port a virtuális gép és a virtuális gép hozzáférést biztosít, és a virtuálisgép-hálózathoz nem kapcsolódó / operációs rendszer állapotát. A virtuális gép a soros konzoljához való hozzáférés jelenleg csak Azure-portálon történik, és csak a virtuális gép közreműködő jogosult felhasználókhoz vagy újabb operációs rendszerre a virtuális géphez való hozzáférés engedélyezett. 

> [!Note] 
> Az előzetes verziójú funkciók rendelkezésre álló, feltéve, hogy elfogadja a használati feltételeket. További információkért tekintse meg az [a Microsoft Azure kiegészítő használati és a Microsoft Azure Előzetesekre.] (https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Jelenleg ez a szolgáltatás szerepel **nyilvános előzetes** és a soros konzoljához való hozzáférés a virtuális gépek globális Azure-régiók elérhető lesz. Ezen a ponton soros konzol nincs elérhető Azure Government, a Németországi Azure és az Azure Kína felhő.

 

## <a name="prerequisites"></a>Előfeltételek 

* A virtuális gépnek rendelkeznie kell [rendszerindítási diagnosztika](boot-diagnostics.md) engedélyezve 
* A soros konzol használata a fióknak rendelkeznie kell [közreműködői szerepkör](../../role-based-access-control/built-in-roles.md) a virtuális gép és a [rendszerindítási diagnosztika](boot-diagnostics.md) storage-fiók. 

## <a name="open-the-serial-console"></a>A soros konzol megnyitása
a virtuális gépek soros konzolon keresztül csak érhető el [Azure-portálon](https://portal.azure.com). A soros konzol virtuális gépek portálon lépéseit az alábbiakban 

  1. Nyissa meg az Azure-portálon
  2. A bal oldali menüben válassza ki a virtuális gépek.
  3. Kattintson a virtuális Gépen a listában. A virtuális gép – Áttekintés lapon nyílik meg.
  4. Görgessen le a támogatási + hibaelhárítási szakaszában, és kattintson a soros konzol (előzetes verzió) opciót. Egy új panelen a soros konzol megnyitja, és indítsa el a kapcsolatot.

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

### <a name="disable-feature"></a>A szolgáltatás letiltása
A soros konzol funkcióit a virtuális gép rendszerindítási diagnosztika beállítás letiltásával inaktiválhatók adott virtuális gépekhez.

## <a name="serial-console-security"></a>Soros konzol biztonsága 

### <a name="access-security"></a>Hozzáférés-biztonságot 
Soros konzoljához való hozzáférés korlátozódik, akik rendelkeznek [VM közreműködők](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) -e a virtuális géphez való hozzáférés. Ha az AAD-bérlőt többtényezős hitelesítést igényel, akkor a soros konzoljához való hozzáférés csak az MFA, az access keresztül [Azure-portálon](https://portal.azure.com).

### <a name="channel-security"></a>Biztonsági csatorna
Minden adat küldött vissza, és oda a keresztülhaladnak a hálózaton van titkosítva.

### <a name="audit-logs"></a>Naplók
A soros konzol hozzáférésének jelenleg bejelentkezett a [rendszerindítási diagnosztika](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) naplók a virtuális gép. Ezek a naplók elérését tulajdonában lévő és az Azure virtuális gép rendszergazda határozza meg.  

>[!CAUTION] 
Nincs hozzáférési jelszavak, a konzol naplózza, ha futtassa a konzolon belüli parancsokat tartalmaz, vagy kimeneti jelszavak, titkos kulcsok, felhasználóneveket vagy bármilyen egyéb a személyes azonosításra alkalmas adatokat (PII), miközben a rendszer úgy tünteti fel a virtuális gép rendszerindítási diagnosztika naplókat, valamint minden más látható szöveg, a soros konzol görgetési végrehajtásának részeként biztonsági funkcióit. Ezek a naplók körkörös, és csak egyének olvasási engedélyt a diagnosztikai tárfiók hozzáférhetnek, azonban az ajánlott eljárást a távoli asztali kapcsolattal számára, amely titkok és/vagy PII következő ajánlott. 

### <a name="concurrent-usage"></a>Egyidejű használata
Ha egy felhasználó csatlakozik a soros konzol és egy másik felhasználó sikeresen, hogy ugyanaz a virtuális gép hozzáférést kér, az első felhasználó le lesz választva, és a második felhasználó csatlakozik oly módon, mintha az első felhasználó állandó és a fizikai konzol és egy új elhagyása a felhasználó óta várakozik.

>[!CAUTION] 
Ez azt jelenti, hogy a felhasználó, aki megszakad nem naplózza a rendszer! Képes érvényesíteni a kijelentkezési (keresztül SIGHUP vagy hasonló mechanizmus) kapcsolat bontása után még mindig a programba. A Windows nincs SAC, engedélyezve az automatikus időtúllépés azonban Linux terminál timeout beállítás konfigurálása. 


## <a name="accessing-serial-console-for-windows"></a>A Windows soros konzol használata 
Az Azure-on újabb Windows Server-lemezképekben lesz [speciális felügyeleti konzol](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC) alapértelmezés szerint engedélyezett. SAC a Windows server-verziókban támogatott, de nincs az ügyfél-verziónkénti (például a Windows 10, Windows 8 vagy Windows 7). Ahhoz, hogy a Windows virtuális gépek használatával létrehozott soros konzol Feb2018 vagy alacsonyabb lemezképeket használja az alábbi lépéseket: 

1. Csatlakozás távoli asztalon keresztül a Windows virtuális géphez
2. Egy rendszergazdai parancssorból futtassa a következő parancsokat 
* `bcdedit /ems {current} on`
* `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
3. Indítsa újra a rendszert a SAC konzol engedélyezni kell

![](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Ha a szükséges SAC akkor engedélyezhető, kapcsolat nélkül is, 

1. A meglévő VM adatlemezt értéke SAC kívánt windows lemez csatlakoztatásához. 
2. Egy rendszergazdai parancssorból futtassa a következő parancsokat 
* `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
* `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

### <a name="how-do-i-know-if-sac-is-enabled-or-not"></a>Hogyan állapítható meg, hogy SAC engedélyezve van-e vagy sem 

Ha [SAC] (https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) nincs engedélyezve a soros konzol nem jelenik meg a SAC parancssorába. Bizonyos esetekben egy virtuális gép állapottal kapcsolatos adatok megmutatja, vagy üres lenne.  

### <a name="enabling-boot-menu-to-show-in-the-serial-console"></a>A soros konzol megjeleníti a rendszerindító menü engedélyezése 

Ha Windows rendszertöltő engedélyezni kell a megadását kéri, a soros konzol Windows rendszertöltő adhat hozzá a következő beállítások megjelenítése.

1. Csatlakozás távoli asztalon keresztül a Windows virtuális géphez
2. Egy rendszergazdai parancssorból futtassa a következő parancsokat 
* `bcdedit /set {bootmgr} displaybootmenu yes`
* `bcdedit /set {bootmgr} timeout 5`
* `bcdedit /set {bootmgr} bootems yes`
3. Indítsa újra a rendszert a rendszerindító menü engedélyezni kell

> [!NOTE] 
> Ez a függvény pont terméktámogatási kulcsok nincs engedélyezve, ha speciális rendszerindítási beállítások használatát bcdedit/set {aktuális} onetimeadvancedoptions van szüksége, tekintse meg [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set) további részletekért

## <a name="windows-commands---cmd"></a>Windows-parancsok - CMD 

Ez a szakasz Példaparancsok végrehajtható gyakori feladatok forgatókönyvekben, ahol szükség lehet a virtuális Gépet, például az RDP csatlakozási hibák elhárítása való eléréséhez használható SAC tartalmazza.

SAC szerepel a Windows összes verzióján óta Windows Server 2003, de alapértelmezés szerint le van tiltva. SAC támaszkodik a `sacdrv.sys` kernel-illesztőprogram, a `Special Administration Console Helper` szolgáltatás (`sacsvr`), és a `sacsess.exe` folyamat. További információkért lásd: [sürgős felügyeleti eszközök és beállítások](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc787940(v%3dws.10)).

SAC lehetővé teszi a futó operációs rendszer soros porton keresztül csatlakozni. A SAC, CMD elindításakor `sacsess.exe` elindítja `cmd.exe` a futó operációs rendszeren belül. Láthatja, hogy a feladat kezelő, ha meg egyszerre a virtuális gép RDP alkalommal, amikor csatlakozik SAC keresztül a soros konzol szolgáltatás. Az Ön érhetnek el SAC CMD megegyezik `cmd.exe` RDP-kapcsolaton keresztül csatlakozáskor használ. Ugyanazokat a parancsok és eszközök érhetők el, beleértve a indítsa el a Powershellt a CMD példányról. A fő különbség SAC között, amelyek a Windows helyreállítási környezet (WinRE), hogy SAC van és kezelheti a futó operációs rendszer, ha betölti a WinRE egy másik, a minimális operációsrendszer. Amíg az Azure virtuális gépek nem támogatják a WinRE, hozzáférhet a soros konzol szolgáltatás Azure virtuális gépek SAC keresztül is felügyelhetők.

Mivel a SAC vissza nem görgessen a 80-as x 24 képernyőpuffer korlátozódik, vegye fel `| more` parancsok egyszerre a kimenet egy lap megjelenítéséhez. Használjon `<spacebar>` a következő lap vagy `<enter>` megtekintéséhez a következő sorban.  

`SHIFT+INSERT` a beillesztés parancsikont a soros konzol ablakából van.

SAC által korlátozott képernyőpuffer, mert hosszabb parancsok könnyebben írja be a helyi szövegszerkesztőben, és SAC majd beilleszteni.

### <a name="view-and-edit-windows-registry-settings"></a>A Windows beállításjegyzék beállításainak megtekintése és szerkesztése
#### <a name="verify-rdp-is-enabled"></a>Ellenőrizze az RDP engedélyezve van
`reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections`

`reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections`

A második kulcs (a \Policies) csak ha a megfelelő csoportházirend-beállítás konfigurálva van jelen.

#### <a name="enable-rdp"></a>RDP engedélyezése
`reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0`

`reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0` 

A második kulcs (a \Policies) csak lenne szükség, ha a megfelelő csoportházirend-beállítás volt konfigurálva. Érték a következő csoportházirend-frissítés felülíródik, ha erre van konfigurálva, a csoportházirendben.

### <a name="manage-windows-services"></a>Windows-szolgáltatások kezelése

#### <a name="view-service-state"></a>Szolgáltatás állapotának megtekintése
`sc query termservice`
####  <a name="view-service-logon-account"></a>Nézet szolgáltatás bejelentkezési fiók
`sc qc termservice`
#### <a name="set-service-logon-account"></a>Bejelentkezési szolgáltatásfiók beállítása 
`sc config termservice obj= "NT Authority\NetworkService"`

Az egyenlőségjel után szóközt megadása kötelező.
#### <a name="set-service-start-type"></a>Beállítása szolgáltatás indítási típusa
`sc config termservice start= demand` 

Az egyenlőségjel után szóközt megadása kötelező. Kezdő lehetséges értékek a következők `boot`, `system`, `auto`, `demand`, `disabled`, `delayed-auto`.
#### <a name="set-service-dependencies"></a>Set függőségei
`sc config termservice depend= RPCSS`

Az egyenlőségjel után szóközt megadása kötelező.
#### <a name="start-service"></a>Szolgáltatás indítása
`net start termservice`

vagy

`sc start termservice`
#### <a name="stop-service"></a>Szolgáltatás leállítása
`net stop termservice`

vagy

`sc stop termservice`
### <a name="manage-networking-features"></a>Hálózatkezelési szolgáltatások kezelése
#### <a name="show-nic-properties"></a>A hálózati adapter tulajdonságainak megjelenítése
`netsh interface show interface` 
#### <a name="show-ip-properties"></a>IP-tulajdonságok megjelenítése
`netsh interface ip show config`
#### <a name="show-ipsec-configuration"></a>IPSec-konfiguráció megjelenítése
`netsh nap client show configuration`  
#### <a name="enable-nic"></a>Engedélyezze a hálózati adapter
`netsh interface set interface name="<interface name>" admin=enabled`
#### <a name="set-nic-to-use-dhcp"></a>Állítsa be a hálózati DHCP használatára
`netsh interface ip set address name="<interface name>" source=dhcp`

Azure virtuális gépek mindig a vendég operációs rendszer IP-cím beszerzése a DHCP használatára kell konfigurálni. Az Azure statikus IP-beállítása DHCP továbbra is használja ahhoz, hogy megkapja a statikus IP-címet a virtuális géphez.
#### <a name="ping"></a>Ping
`ping 8.8.8.8` 
#### <a name="port-ping"></a>Port ping  
A telnet-ügyfél telepítése

`dism /online /Enable-Feature /FeatureName:TelnetClient`

Kapcsolat tesztelése

`telnet bing.com 80`

A telnet-ügyfél eltávolítása

`dism /online /Disable-Feature /FeatureName:TelnetClient`

Ha korlátozott a Windowsban rendelkezésre álló metódusok alapértelmezés szerint, PowerShell jobb megközelítés port kapcsolat tesztelése az is lehet. Című rész a PowerShell alábbi példákat.
#### <a name="test-dns-name-resolution"></a>A DNS-névfeloldás tesztelése
`nslookup bing.com`
#### <a name="show-windows-firewall-rule"></a>A Windows tűzfal szabály megjelenítése
`netsh advfirewall firewall show rule name="Remote Desktop - User Mode (TCP-In)"`
#### <a name="disable-windows-firewall"></a>Tiltsa le a Windows tűzfal
`netsh advfirewall set allprofiles state off`

Hibaelhárításához ideiglenesen zárja ki a Windows tűzfal is használhatja ezt a parancsot. A következőket hajtja végre kell engedélyezése a következő újraindítás, vagy ha Ön enaable, az alábbi parancs használatával. Ne állítsa le a Windows tűzfalszolgáltatást (MPSSVC) vagy alap szűrés motor (BFE) szolgáltatás módja a Windows tűzfal kizárása. MPSSVC vagy BFE leállítása blokkolja az összes kapcsolat eredményez.
#### <a name="enable-windows-firewall"></a>A Windows tűzfal engedélyezése
`netsh advfirewall set allprofiles state on`
### <a name="manage-users-and-groups"></a>Felhasználók és csoportok kezelése
#### <a name="create-local-user-account"></a>Helyi felhasználói fiók létrehozása
`net user /add <username> <password>`
#### <a name="add-local-user-to-local-group"></a>Helyi felhasználó hozzáadása helyi csoporthoz
`net localgroup Administrators <username> /add`
#### <a name="verify-user-account-is-enabled"></a>Ellenőrizze a felhasználói fiók engedélyezve van
`net user <username> | find /i "active"`

Általánosított lemezkép alapján létrehozott Azure virtuális gépek lesz a helyi rendszergazdai fiók átnevezi a virtuális gép kiépítése során megadott név. Ezért általában nem lesz `Administrator`.
#### <a name="enable-user-account"></a>Felhasználói fiók engedélyezése
`net user <username> /active:yes`  
#### <a name="view-user-account-properties"></a>Felhasználói fiók tulajdonságainak megtekintése
`net user <username>`

Egy helyi rendszergazdai fiókot az egyik fontos sorok példa:

`Account active Yes`

`Account expires Never`

`Password expires Never`

`Workstations allowed All`

`Logon hours allowed All`

`Local Group Memberships *Administrators`

#### <a name="view-local-groups"></a>Helyi csoportok megtekintése
`net localgroup`
### <a name="manage-the-windows-event-log"></a>A Windows Eseménynapló kezelése
#### <a name="query-event-log-errors"></a>Lekérdezés Eseménynapló hibák
`wevtutil qe system /c:10 /f:text /q:"Event[System[Level=2]]" | more`

Változás `/c:10` adja vissza, vagy helyezze vissza az összes esemény a szűrőnek megfelelő események kívánt száma.
#### <a name="query-event-log-by-event-id"></a>Lekérdezés Eseménynapló azonosítójú esemény
`wevtutil qe system /c:1 /f:text /q:"Event[System[EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider"></a>Eseménynapló Azonosítójú esemény és a szolgáltató által lekérdezése
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Lekérdezés Eseménynapló Azonosítójú esemény és a szolgáltató által a legutóbbi 24 órában
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Használjon `604800000` kikeresni a következő 24 órában helyett vissza 7 nap.
#### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Lekérdezés Eseménynapló Azonosítójú esemény, a szolgáltató és a EventData által az elmúlt 7 napban
`wevtutil qe security /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
### <a name="view-or-remove-installed-applications"></a>Megtekinteni és eltávolítani a telepített alkalmazások
#### <a name="list-installed-applications"></a>Lista telepített alkalmazások
`wmic product get Name,InstallDate | sort /r | more`

A `sort /r` szerint csökkenő rendezés telepítés dátuma megkönnyítése, mi nemrég lett telepítve. Használjon `<spacebar>` ahhoz, hogy a kimenet, a következő lapra vagy `<enter>` ahhoz, hogy egy sor.
#### <a name="uninstall-an-application"></a>Alkalmazás telepítésének eltávolítása
`wmic path win32_product where name="<name>" call uninstall`

Cserélje le `<name>` az adott vissza a fenti parancsban a el kívánja távolítani az alkalmazás nevét.

### <a name="file-system-management"></a>Fájlrendszer kezelése
#### <a name="get-file-version"></a>A fájl verziója beolvasása
`wmic datafile where "drive='C:' and path='\\windows\\system32\\drivers\\' and filename like 'netvsc%'" get version /format:list`

Ebben a példában a virtuális hálózati adapter illesztőprogramja, amely netvsc.sys, netvsc63.sys vagy netvsc60.sys attól függően, hogy a Windows-verzió verziója adja vissza.
#### <a name="scan-for-system-file-corruption"></a>Meghibásodott rendszerfájl vizsgálata
`sfc /scannow`

Lásd még: [javítsa ki a Windows-lemezkép](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
#### <a name="scan-for-system-file-corruption"></a>Meghibásodott rendszerfájl vizsgálata
`dism /online /cleanup-image /scanhealth`

Lásd még: [javítsa ki a Windows-lemezkép](https://docs.microsoft.com/windows-hardware/manufacture/desktop/repair-a-windows-image).
#### <a name="export-file-permissions-to-text-file"></a>Fájlengedélyek exportálása szövegfájlba
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /t /c > %temp%\MachineKeys_permissions_before.txt`
#### <a name="save-file-permissions-to-acl-file"></a>Fájlengedélyek ACL-fájl mentése
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /save %temp%\MachineKeys_permissions_before.aclfile /t`  
#### <a name="restore-file-permissions-from-acl-file"></a>Állítsa vissza a fájl engedélyeit ACL-fájlból
`icacls %programdata%\Microsoft\Crypto\RSA /save %temp%\MachineKeys_permissions_before.aclfile /t`

Az elérési út használata esetén `/restore` kell lennie a fölérendelt mappája használatakor a megadott mappa `/save`. Ebben a példában `\RSA` a szülője a `\MachineKeys` megadott mappában a `/save` a fenti példa.
#### <a name="take-ntfs-ownership-of-a-folder"></a>NTFS saját tulajdonba mappa
`takeown /f %programdata%\Microsoft\Crypto\RSA\MachineKeys /a /r`  
#### <a name="grant-ntfs-permissions-to-a-folder-recursively"></a>Adja meg a mappa rekurzív módon NTFS-engedélyeket
`icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"`  
### <a name="manage-devices"></a>Eszközök kezelése
#### <a name="remove-non-present-pnp-devices"></a>Nem található PNP eszközök eltávolítása
`%windir%\System32\RUNDLL32.exe %windir%\System32\pnpclean.dll,RunDLL_PnpClean /Devices /Maxclean`
### <a name="manage-group-policy"></a>A Csoportházirend kezelése
#### <a name="force-group-policy-update"></a>Házirend-frissítés kényszerítése
`gpupdate /force /wait:-1`
### <a name="miscellaneous-tasks"></a>Egyéb feladatok
#### <a name="show-os-version"></a>Operációs rendszer verziójának megjelenítése
`ver`

vagy 

`wmic os get caption,version,buildnumber /format:list`

vagy 

`systeminfo  find /i "os name"`

`systeminfo | findstr /i /r "os.*version.*build"`
#### <a name="view-os-install-date"></a>Az operációs rendszer nézet telepítés dátuma
`systeminfo | find /i "original"`

vagy 

`wmic os get installdate`
#### <a name="view-last-boot-time"></a>Utolsó rendszerindítás megtekintése
`systeminfo | find /i "system boot time"`
#### <a name="view-time-zone"></a>Nézet időzóna
`systeminfo | find /i "time zone"`

vagy

`wmic timezone get caption,standardname /format:list`
#### <a name="restart-windows"></a>A Windows újraindítása
`shutdown /r /t 0`

Hozzáadás `/f` arra kényszeríti a futó alkalmazások felhasználók figyelmeztetés nélkül kívánja bezárni.
#### <a name="detect-safe-mode-boot"></a>A biztonságos mód rendszerindítási észlelése
`bcdedit /enum | find /i "safeboot"` 

## <a name="windows-commands---powershell"></a>Windows-parancsok - PowerShell

PowerShell futtatásához, SAC, miután meg egy parancssort, írja be:

`powershell <enter>`

>[!CAUTION]
A PowerShell-munkamenetet más PowerShell-parancsok futtatása előtt távolítsa el a PSReadLine modul. Nincs olyan ismert probléma, ahol felesleges karaktereket lehet bevezetni PSReadLine egy PowerShell-munkamenetben, SAC fut. Ha a vágólapról beillesztett szöveg.

Először ellenőrizze, ha a PSReadLine be van töltve. Alapértelmezés szerint a Windows Server 2016, a Windows 10 és a Windows újabb verziói betöltődik. Csak akkor kell megtalálható a Windows korábbi verzióinak Ha azt már manuálisan telepítette. 

Ha ez a parancs visszaadja a kérdés nélküli kimeneti, majd a modul nem lett betöltve, és folytathatja a PowerShell-munkamenetet a SAC a szokásos módon.

`get-module psreadline`

Ha a fenti parancs visszaadja a PSReadLine verziója, a következő parancsot a kikapcsoláshoz. Ez a parancs nem törölhető és nem a modul eltávolítása, azt csak eltávolítja azt a jelenlegi PowerShell-munkamenetben.

`remove-module psreadline`

### <a name="view-and-edit-windows-registry-settings"></a>A Windows beállításjegyzék beállításainak megtekintése és szerkesztése
#### <a name="verify-rdp-is-enabled"></a>Ellenőrizze az RDP engedélyezve van
`get-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections'`

`get-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections'`

A második kulcs (a \Policies) csak ha a megfelelő csoportházirend-beállítás konfigurálva van jelen.
#### <a name="enable-rdp"></a>RDP engedélyezése
`set-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections' 0 -type dword`

`set-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections' 0 -type dword`

A második kulcs (a \Policies) csak lenne szükség, ha a megfelelő csoportházirend-beállítás volt konfigurálva. Érték a következő csoportházirend-frissítés felülíródik, ha erre van konfigurálva, a csoportházirendben.
### <a name="manage-windows-services"></a>Windows-szolgáltatások kezelése
#### <a name="view-service-details"></a>Szolgáltatás részleteinek megtekintése
`get-wmiobject win32_service -filter "name='termservice'" |  format-list Name,DisplayName,State,StartMode,StartName,PathName,ServiceType,Status,ExitCode,ServiceSpecificExitCode,ProcessId`

`Get-Service` is használható, de nem tartalmazza a szolgáltatás bejelentkezési fiókjához. `Get-WmiObject win32-service` nincs.
#### <a name="set-service-logon-account"></a>Bejelentkezési szolgáltatásfiók beállítása
`(get-wmiobject win32_service -filter "name='termservice'").Change($null,$null,$null,$null,$null,$false,'NT Authority\NetworkService')`

Ha nem használ szolgáltatásfiókot `NT AUTHORITY\LocalService`, `NT AUTHORITY\NetworkService`, vagy `LocalSystem`, adja meg a fiók jelszavát a fiók neve után utolsó (nyolcadik) elemként.
#### <a name="set-service-startup-type"></a>Szolgáltatás indítási típusának beállítása
`set-service termservice -startuptype Manual`

`Set-service` elfogadja `Automatic`, `Manual`, vagy `Disabled` indítási típus.
#### <a name="set-service-dependencies"></a>Set függőségei
`Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\TermService' -Name DependOnService -Value @('RPCSS','TermDD')`
#### <a name="start-service"></a>Szolgáltatás indítása
`start-service termservice`
#### <a name="stop-service"></a>Szolgáltatás leállítása
`stop-service termservice`
### <a name="manage-networking-features"></a>Hálózatkezelési szolgáltatások kezelése
#### <a name="show-nic-properties"></a>A hálózati adapter tulajdonságainak megjelenítése
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} |  format-list status,name,ifdesc,macadDresS,driverversion,MediaConNectState,MediaDuplexState`

vagy 

`get-wmiobject win32_networkadapter -filter "servicename='netvsc'" |  format-list netenabled,name,macaddress`

`Get-NetAdapter` a 2012 +, 2008R2 használatra elérhető `Get-WmiObject`.
#### <a name="show-ip-properties"></a>IP-tulajdonságok megjelenítése
`get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'" |  format-list DNSHostName,IPAddress,DHCPEnabled,IPSubnet,DefaultIPGateway,MACAddress,DHCPServer,DNSServerSearchOrder`
#### <a name="enable-nic"></a>Engedélyezze a hálózati adapter
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | enable-netadapter`

vagy

`(get-wmiobject win32_networkadapter -filter "servicename='netvsc'").enable()`

`Get-NetAdapter` a 2012 +, 2008R2 használatra elérhető `Get-WmiObject`.
#### <a name="set-nic-to-use-dhcp"></a>Állítsa be a hálózati DHCP használatára
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | Set-NetIPInterface -DHCP Enabled`

`(get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'").EnableDHCP()`

`Get-NetAdapter` áll rendelkezésre a 2012 +. A 2008R2 használjon `Get-WmiObject`. Azure virtuális gépek mindig a vendég operációs rendszer IP-cím beszerzése a DHCP használatára kell konfigurálni. Az Azure statikus IP-beállítása DHCP továbbra is használja a virtuális gép biztosítanak az IP-cím.
#### <a name="ping"></a>Ping
`test-netconnection`

vagy

`get-wmiobject Win32_PingStatus -Filter 'Address="8.8.8.8"' | format-table -autosize IPV4Address,ReplySize,ResponseTime`

`Test-Netconnection` nélkül paramétereket megpróbálja ping `internetbeacon.msedge.net`. Érhető el a 2012 +. A 2008R2 használjon `Get-WmiObject` a második példában látható módon.
#### <a name="port-ping"></a>Port Ping
`test-netconnection -ComputerName bing.com -Port 80`

vagy

`(new-object Net.Sockets.TcpClient).BeginConnect('bing.com','80',$null,$null).AsyncWaitHandle.WaitOne(300)`

`Test-NetConnection` áll rendelkezésre a 2012 +. 2008R2 használatra `Net.Sockets.TcpClient`
#### <a name="test-dns-name-resolution"></a>A DNS-névfeloldás tesztelése
`resolve-dnsname bing.com` 

vagy 

`[System.Net.Dns]::GetHostAddresses('bing.com')`

`Resolve-DnsName` áll rendelkezésre a 2012 +. A 2008R2 használjon `System.Net.DNS`.
#### <a name="show-windows-firewall-rule-by-name"></a>Windows tűzfalszabály nevének megjelenítése
`get-netfirewallrule -name RemoteDesktop-UserMode-In-TCP` 
#### <a name="show-windows-firewall-rule-by-port"></a>A Windows tűzfal szabály port megjelenítése
`get-netfirewallportfilter | where {$_.localport -eq 3389} | foreach {Get-NetFirewallRule -Name $_.InstanceId} | format-list Name,Enabled,Profile,Direction,Action`

vagy

`(new-object -ComObject hnetcfg.fwpolicy2).rules | where {$_.localports -eq 3389 -and $_.direction -eq 1} | format-table Name,Enabled`

`Get-NetFirewallPortFilter` áll rendelkezésre a 2012 +. 2008R2 használja a `hnetcfg.fwpolicy2` COM-objektum. 
#### <a name="disable-windows-firewall"></a>Tiltsa le a Windows tűzfal
`Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False`

`Set-NetFirewallProfile` áll rendelkezésre a 2012 +. A 2008R2 használjon `netsh advfirewall` CMD fenti szerint hivatkozott.
### <a name="manage-users-and-groups"></a>Felhasználók és csoportok kezelése
#### <a name="create-local-user-account"></a>Helyi felhasználói fiók létrehozása
`new-localuser <name>`
#### <a name="verify-user-account-is-enabled"></a>Ellenőrizze a felhasználói fiók engedélyezve van
`(get-localuser | where {$_.SID -like "S-1-5-21-*-500"}).Enabled`

vagy 

`(get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'").Disabled`

`Get-LocalUser` áll rendelkezésre a 2012 +. A 2008R2 használjon `Get-WmiObject`. Ez a példa bemutatja a beépített helyi rendszergazdai fiók, amely mindig SID `S-1-5-21-*-500`. Általánosított lemezkép alapján létrehozott Azure virtuális gépek lesz a helyi rendszergazdai fiók átnevezi a virtuális gép kiépítése során megadott név. Ezért általában nem lesz `Administrator`.
#### <a name="add-local-user-to-local-group"></a>Helyi felhasználó hozzáadása helyi csoporthoz
`add-localgroupmember -group Administrators -member <username>`
#### <a name="enable-local-user-account"></a>Helyi felhasználói fiók engedélyezése
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | enable-localuser` 

Ez a példa lehetővé teszi, hogy a beépített helyi rendszergazdai fiók, amely mindig SID `S-1-5-21-*-500`. Általánosított lemezkép alapján létrehozott Azure virtuális gépek lesz a helyi rendszergazdai fiók átnevezi a virtuális gép kiépítése során megadott név. Ezért általában nem lesz `Administrator`.
#### <a name="view-user-account-properties"></a>Felhasználói fiók tulajdonságainak megtekintése
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | format-list *`

vagy 

`get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'" |  format-list Name,Disabled,Status,Lockout,Description,SID`

`Get-LocalUser` áll rendelkezésre a 2012 +. A 2008R2 használjon `Get-WmiObject`. Ez a példa bemutatja a beépített helyi rendszergazdai fiók, amely mindig SID `S-1-5-21-*-500`.
#### <a name="view-local-groups"></a>Helyi csoportok megtekintése
`(get-localgroup).name | sort` `(get-wmiobject win32_group).Name | sort`

`Get-LocalUser` áll rendelkezésre a 2012 +. A 2008R2 használjon `Get-WmiObject`.
### <a name="manage-the-windows-event-log"></a>A Windows Eseménynapló kezelése
#### <a name="query-event-log-errors"></a>Lekérdezés Eseménynapló hibák
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Level=2]]" | more`

Változás `/c:10` adja vissza, vagy helyezze vissza az összes esemény a szűrőnek megfelelő események kívánt száma.
#### <a name="query-event-log-by-event-id"></a>Lekérdezés Eseménynapló azonosítójú esemény
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider"></a>Eseménynapló Azonosítójú esemény és a szolgáltató által lekérdezése
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
#### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Lekérdezés Eseménynapló Azonosítójú esemény és a szolgáltató által a legutóbbi 24 órában
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Használjon `604800000` kikeresni a következő 24 órában helyett vissza 7 nap. |
#### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Lekérdezés Eseménynapló Azonosítójú esemény, a szolgáltató és a EventData által az elmúlt 7 napban
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
### <a name="view-or-remove-installed-applications"></a>Megtekinteni és eltávolítani a telepített alkalmazások
#### <a name="list-installed-software"></a>Lista telepített szoftver
`get-wmiobject win32_product | select installdate,name | sort installdate -descending | more`
#### <a name="uninstall-software"></a>Szoftver eltávolítása
`(get-wmiobject win32_product -filter "Name='<name>'").Uninstall()`
### <a name="file-system-management"></a>Fájlrendszer kezelése
#### <a name="get-file-version"></a>A fájl verziója beolvasása
`(get-childitem $env:windir\system32\drivers\netvsc*.sys).VersionInfo.FileVersion`

Ebben a példában a netvsc.sys, netvsc63.sys vagy attól függően, hogy a Windows-verzióhoz netvsc60.sys nevű virtuális hálózati adapter illesztőprogramja verzió adja vissza.
#### <a name="download-and-extract-file"></a>Töltse le, és bontsa ki a fájl
`$path='c:\bin';md $path;cd $path;(new-object net.webclient).downloadfile( ('htTp:/'+'/download.sysinternals.com/files/SysinternalsSuite.zip'),"$path\SysinternalsSuite.zip");(new-object -com shelL.apPlication).namespace($path).CopyHere( (new-object -com shelL.apPlication).namespace("$path\SysinternalsSuite.zip").Items(),16)`

Ez a példa létrehoz egy `c:\bin` mappát, majd letölti és kibontja a Sysinternals eszközcsomagot jelent a `c:\bin`.
### <a name="miscellaneous-tasks"></a>Egyéb feladatok
#### <a name="show-os-version"></a>Operációs rendszer verziójának megjelenítése
`get-wmiobject win32_operatingsystem | format-list caption,version,buildnumber` 
#### <a name="view-os-install-date"></a>Az operációs rendszer nézet telepítés dátuma
`(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).installdate)`
#### <a name="view-last-boot-time"></a>Utolsó rendszerindítás megtekintése
`(get-wmiobject win32_operatingsystem).lastbootuptime`
#### <a name="view-windows-uptime"></a>Windows hasznos üzemidő megtekintése
`"{0:dd}:{0:hh}:{0:mm}:{0:ss}.{0:ff}" -f ((get-date)-(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).lastbootuptime))`

Hasznos üzemidő, adja vissza `<days>:<hours>:<minutes>:<seconds>:<milliseconds>`, például `49:16:48:00.00`. 
#### <a name="restart-windows"></a>A Windows újraindítása
`restart-computer`

Hozzáadás `-force` arra kényszeríti a futó alkalmazások felhasználók figyelmeztetés nélkül kívánja bezárni.
### <a name="instance-metadata"></a>Példány metaadatok

Azure-példányokon metaadatok lekérheti például osType helyét, vmSize, vmId, neve, erőforráscsoport-név, előfizetés-azonosító, privateipaddress tulajdonságot vagy publicIpAddress a részletek megtekintéséhez az Azure virtuális Gépen belül.

Kifogástalan Vendég hálózati kapcsolatot, példány metaadatok lekérdezéséhez van szükség, mert így egy REST-hívást a példány metaadatok szolgáltatáshoz Azure gazdagépen keresztül. Ezért ha példány metaadatok lekérdezni, amely szerint a Vendég képes az Azure által üzemeltetett szolgáltatás történő, hálózaton keresztüli kommunikációra.

További információkért lásd: [Azure példány metaadat-szolgáltatás](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service).

#### <a name="instance-metadata"></a>Példány metaadatok
`$im = invoke-restmethod -headers @{"metadata"="true"} -uri http://169.254.169.254/metadata/instance?api-version=2017-08-01 -method get`

`$im | convertto-json`
#### <a name="os-type-instance-metadata"></a>Operációs rendszer típusa (példány metaadatok)
`$im.Compute.osType`
#### <a name="location-instance-metadata"></a>Hely (példány metaadatok)
`$im.Compute.Location`
#### <a name="size-instance-metadata"></a>Méret (példány metaadatok)
`$im.Compute.vmSize`
#### <a name="vm-id-instance-metadata"></a>Virtuális gép azonosítója (példány metaadatok)
`$im.Compute.vmId`
#### <a name="vm-name-instance-metadata"></a>Virtuális gép neve (példány metaadatok)
`$im.Compute.name`
#### <a name="resource-group-name-instance-metadata"></a>Erőforráscsoport neve (példány metaadatok)
`$im.Compute.resourceGroupName`
#### <a name="subscription-id-instance-metadata"></a>Előfizetés-azonosító (példány metaadatok)
`$im.Compute.subscriptionId`
#### <a name="tags-instance-metadata"></a>Címkék (példány metaadatok)
`$im.Compute.tags`
#### <a name="placement-group-id-instance-metadata"></a>Elhelyezési Csoportazonosító (példány metaadatok)
`$im.Compute.placementGroupId`
#### <a name="platform-fault-domain-instance-metadata"></a>Platform tartalék tartománya (példány metaadatok)
`$im.Compute.platformFaultDomain`
#### <a name="platform-update-domain-instance-metadata"></a>Platform frissítési tartományt (példány metaadatok)
`$im.Compute.platformUpdateDomain`
#### <a name="ipv4-private-ip-address-instance-metadata"></a>IPv4-alapú magánhálózati IP-cím (példány metaadatok)
`$im.network.interface.ipv4.ipAddress.privateIpAddress`
#### <a name="ipv4-public-ip-address-instance-metadata"></a>IPv4-alapú nyilvános IP-cím (példány metaadatok)
`$im.network.interface.ipv4.ipAddress.publicIpAddress`
#### <a name="ipv4-subnet-address--prefix-instance-metadata"></a>IPv4-alhálózatnak cím / előtag (metaadat-példány)
`$im.network.interface.ipv4.subnet.address`

`$im.network.interface.ipv4.subnet.prefix`
#### <a name="ipv6-ip-address-instance-metadata"></a>IPv6-alapú IP-cím (példány metaadatok)
`$im.network.interface.ipv6.ipAddress`
#### <a name="mac-address-instance-metadata"></a>MAC-cím (példány metaadatok)
`$im.network.interface.macAddress`

## <a name="errors"></a>Hibák
A legtöbb hiba átmeneti jellegű, és ezek kapcsolódási címet újrapróbálkozás. Táblázat alatti hibák és a megoldás listáját tartalmazza 

Hiba                            |   Kezelés 
:---------------------------------|:--------------------------------------------|
Nem sikerült beolvasni a rendszerindítási diagnosztika beállításainak "<VMNAME>". A soros konzol használatához győződjön meg arról, hogy a rendszerindítási diagnosztika engedélyezve van a virtuális gép. | Győződjön meg arról, hogy rendelkezik-e a virtuális gép [rendszerindítási diagnosztika](boot-diagnostics.md) engedélyezve van. 
A virtuális gép leállított felszabadított állapotban van. Indítsa el a virtuális Gépet, majd próbálja megismételni a soros konzol kapcsolat. | Virtuális gép a soros konzol eléréséhez elindított állapotban kell lennie.
Nincs a virtuális gép soros konzol használatához a szükséges engedélyekkel. Ellenőrizze, hogy legalább VM közreműködői szerepkör engedélyekkel.| Soros konzol használatához bizonyos hozzáférési engedélyt. Lásd: [hozzáférési követelmények](#prerequisites) részletek
Nem sikerült meghatározni az erőforráscsoport a rendszerindítási diagnosztikai tárfiók "<STORAGEACCOUNTNAME>". Győződjön meg arról, hogy engedélyezve van a rendszerindítási diagnosztika a virtuális gép, és rendelkezik hozzáféréssel ehhez a tárfiókhoz. | Soros konzol használatához bizonyos hozzáférési engedélyt. Lásd: [hozzáférési követelmények](#prerequisites) részletek

## <a name="known-issues"></a>Ismert problémák 
Mivel azt még szakaszaiban preview a soros konzol hozzáféréshez, azt ismert problémákat keresztül dolgozik, a alatt a listája, ezen a lehetséges megoldások 

Probléma                           |   Kezelés 
:---------------------------------|:--------------------------------------------|
A virtuális gép méretezési készlet példány soros konzolon nincs lehetőség | A virtuálisgép-méretezési készlet példánya a soros konzoljához való hozzáférés előzetes időpontjában nem támogatott.
Elérte-e meg, miután a kapcsolat szalagcím nem jeleníti meg a napló a kérdés | [Elérte-e meg, nincs semmi hatása.](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md)
Csak állapotfigyelő információk jelennek meg a Windows virtuális gépek történő csatlakozás során| [Windows állapotfigyelő jelek](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md)
Nem sikerült írja be a SAC kérdezzen rá, ha kernel-hibakereső be van kapcsolva | A virtuális gép, és futtassa az RDP `bcdedit /debug {current} off` parancsot egy emelt szintű parancssorból. Ha nem tudja RDP helyette az operációsrendszer-lemezképet csatlakoztatni egy másik Azure virtuális gép szabadon módosítható, amíg a lemez használatával csatlakoztatva `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off`, a lemez vissza felcserélni.

## <a name="frequently-asked-questions"></a>Gyakori kérdések 
**Q. Hogyan küldhetek visszajelzést?**

A. Visszajelzés küldése problémát, a https://aka.ms/serialconsolefeedback. Másik lehetőségként kevesebb (elsődleges) visszajelzés küldése keresztül azserialhelp@microsoft.com vagy a virtuális gép kategóriája http://feedback.azure.com 

**Q. Hiba "meglévő konzolnak ütköző operációs rendszerének típusa"Windows"Linux operációs rendszer a kért típussal rendelkező?**

A. Egy ismert probléma elhárításához, egyszerűen nyissa meg [Azure Cloud rendszerhéj](https://docs.microsoft.com/azure/cloud-shell/overview) bash üzemmódban, majd próbálja meg újra.

**Q. Nem tudom elérni a soros konzol, ahol is fájl támogatási esetet?**

A. Az előzetes funkcióval keresztül Azure Preview feltételek vonatkozik. Ez támogatása a kezelését érdemes a fent említett csatornán keresztül. 

## <a name="next-steps"></a>További lépések
* A soros konzol érhető el is [Linux](../linux/serial-console.md) virtuális gépek
* További információ [bootdiagnostics](boot-diagnostics.md)
