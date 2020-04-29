---
title: Az Azure VM RDP-kapcsolatával kapcsolatos hibák elhárítása eseményazonosító alapján | Microsoft Docs
description: Az eseményazonosító segítségével elháríthatja az Azure-beli virtuális gépekhez (VM) való Távoli asztal protokollon keresztüli kapcsolódást megakadályozó különböző problémákat.
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
ms.date: 11/01/2018
ms.author: delhan
ms.openlocfilehash: 2073d5f91b26cd2ae53e3291a6d1dad4d711b66d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80437060"
---
# <a name="troubleshoot-azure-vm-rdp-connection-issues-by-event-id"></a>Az Azure-beli virtuális gépek kapcsolati hibáinak elhárítása eseményazonosító alapján 

Ez a cikk azt ismerteti, hogyan használhatók az eseményazonosító az Azure-beli virtuális gépekhez (VM) Távoli asztal protokoll (RDP) kapcsolatait megakadályozó problémák elhárításához.

## <a name="symptoms"></a>Probléma

Egy Azure-beli virtuális géphez való kapcsolódáshoz egy Távoli asztal protokoll (RDP) munkamenetet próbál használni. A hitelesítő adatok bevitele után a kapcsolat meghiúsul, és a következő hibaüzenet jelenik meg:

**Ez a számítógép nem tud kapcsolódni a távoli számítógéphez. Próbálkozzon újra a csatlakozással, ha a probléma továbbra is fennáll, forduljon a távoli számítógép tulajdonosához vagy a hálózati rendszergazdához.**

A probléma megoldásához tekintse át az eseménynaplókat a virtuális gépen, majd tekintse át az alábbi forgatókönyveket.

## <a name="before-you-troubleshoot"></a>A hibakeresés előtt

### <a name="create-a-backup-snapshot"></a>Biztonsági mentési pillanatkép létrehozása

Biztonsági mentési pillanatkép létrehozásához kövesse a [lemez pillanatképének](../windows/snapshot-copy-managed-disk.md)lépései című témakör lépéseit.

### <a name="connect-to-the-vm-remotely"></a>Távolról csatlakozhat a virtuális géphez

Ha távolról szeretne csatlakozni a virtuális géphez, használja az egyik módszert az [Azure-beli virtuális gépekkel kapcsolatos hibák elhárításához használható távoli eszközök használatával](remote-tools-troubleshoot-azure-vm-issues.md).

## <a name="scenario-1"></a>1. példa

### <a name="event-logs"></a>Eseménynaplók

Egy CMD-példányban futtassa a következő parancsokat annak megadásához, hogy a 1058-es vagy a 1057-os esemény a rendszernaplóban van-e naplózva az elmúlt 24 órában:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1058 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1057 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Napló neve:**      Rendszer <br />
**Forrás:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Dátum:**          *idő* <br />
**Eseményazonosító:** 1058 <br />
**Feladat kategóriája:** NEz egy <br />
**Szint:**         Hiba <br />
**Kulcsszavak:**      Klasszikus <br />
**Felhasználó:**          N/A <br />
**Számítógép:**      *számítógép* <br />
**Leírás:** A távoli asztali munkamenetgazda-kiszolgáló nem tudta lecserélni a távoli asztali munkamenetgazda-kiszolgáló hitelesítéséhez használt lejárt önaláírt tanúsítványt a TLS-kapcsolatokon. A megfelelő állapotkód hozzáférése megtagadva.

**Napló neve:**      Rendszer <br />
**Forrás:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Dátum:**          *idő* <br />
**Eseményazonosító:** 1058 <br />
**Feladat kategóriája:** NEz egy <br />
**Szint:**         Hiba <br />
**Kulcsszavak:**      Klasszikus <br />
**Felhasználó:**          N/A <br />
**Számítógép:**      *számítógép* <br />
**Leírás:** A távoli asztali munkamenetgazda-kiszolgáló nem tudott létrehozni egy új önaláírt tanúsítványt, amelyet a távoli asztali munkamenetgazda-kiszolgáló hitelesítéséhez kíván használni a TLS-kapcsolatokon, a megfelelő állapotkód objektum már létezik.

**Napló neve:**      Rendszer <br />
**Forrás:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Dátum:**          *idő* <br />
**Eseményazonosító:** 1057 <br />
**Feladat kategóriája:** NEz egy <br />
**Szint:**         Hiba <br />
**Kulcsszavak:**      Klasszikus <br />
**Felhasználó:**          N/A <br />
**Számítógép:**      *számítógép* <br />
**Leírás:** A távoli asztali munkamenetgazda-kiszolgáló nem tudott létrehozni egy új önaláírt tanúsítványt, amelyet a távoli asztali munkamenetgazda kiszolgáló hitelesítéséhez kíván használni a TLS-kapcsolatokon. A vonatkozó állapotkód nem létezik kulcskészlet.

A következő parancsok futtatásával is megkeresheti az SCHANNEL-hibák 36872-es és 36870-as eseményeit:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36870 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36872 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Napló neve:**      Rendszer <br />
**Forrás:**        Schannel <br />
**Dátum:** – <br />
**Eseményazonosító:** 36870 <br />
**Feladat kategóriája:** NEz egy <br />
**Szint:**         Hiba <br />
**Kulcsszavak**       <br />
**Felhasználó:**          RENDSZER <br />
**Számítógép:**      *számítógép* <br />
**Leírás:** Végzetes hiba történt a TLS-kiszolgáló hitelesítő adatainak titkos kulcsának elérésére tett kísérlet során. A titkosítási modul által visszaadott hibakód 0x8009030D.  <br />
A belső hiba állapota 10001.

### <a name="cause"></a>Ok
Ez a probléma azért fordul elő, mert a virtuális gép következő mappájában lévő helyi RSA titkosítási kulcsok nem érhetők el. Ez a probléma a következő okok egyike miatt fordulhat elő:

1. Helytelen engedélyek konfigurálása a következő mappában vagy az RSA-fájlokban.

2. Sérült vagy hiányzó RSA-kulcs.

### <a name="resolution"></a>Megoldás:

A probléma elhárításához az alábbi lépéseket követve be kell állítania az RDP-tanúsítvány megfelelő engedélyeit.

#### <a name="grant-permission-to-the-machinekeys-folder"></a>Engedély megadása a következő mappához

1. Hozzon létre egy parancsfájlt a következő tartalom használatával:

   ```powershell
   remove-module psreadline 
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt
   takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"
   icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt
   Restart-Service TermService -Force
   ```

2.  Futtassa ezt a szkriptet a MachineKey mappa engedélyeinek alaphelyzetbe állításához, és állítsa alaphelyzetbe az RSA-fájlokat az alapértelmezett értékekre.

3.  Próbálja újra elérni a virtuális gépet.

A parancsfájl futtatása után megtekintheti a következő fájlokat, amelyek az engedélyek problémáit tapasztalják:

* c:\temp\ BeforeScript_permissions. txt
* c:\temp\ AfterScript_permissions. txt

#### <a name="renew-rdp-self-signed-certificate"></a>RDP önaláírt tanúsítvány megújítása

Ha a probléma továbbra is fennáll, futtassa a következő parancsfájlt, és győződjön meg arról, hogy az RDP önaláírt tanúsítvány megújításra kerül:

```powershell
Import-Module PKI
Set-Location Cert:\LocalMachine
$RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint)
Remove-Item -Path $RdpCertThumbprint
Stop-Service -Name "SessionEnv"
Start-Service -Name "SessionEnv"
```

Ha nem tudja megújítani a tanúsítványt, kövesse az alábbi lépéseket a tanúsítvány törléséhez:

1. Egy másik virtuális gépen ugyanabban a VNET nyissa meg a **Futtatás** mezőt, írja be az **MMC**parancsot, majd nyomja meg az **OK**gombot. 

2. A **fájl** menüben kattintson a **beépülő modul hozzáadása/eltávolítása**elemre.

3. Az **elérhető beépülő modulok** listában válassza a **tanúsítványok**lehetőséget, majd válassza a **Hozzáadás**lehetőséget.

4. Válassza a **számítógépfiók**lehetőséget, majd kattintson a **Tovább gombra**.

5. Válasszon **másik számítógépet**, majd adja hozzá a problémát okozó virtuális gép IP-címét.
   >[!Note]
   >Próbálja meg a belső hálózatot használni a virtuális IP-címek használatának elkerüléséhez.

6. Válassza a **Befejezés**lehetőséget, majd kattintson **az OK gombra**.

   ![Számítógép kiválasztása](./media/event-id-troubleshoot-vm-rdp-connecton/select-computer.png)

7. Bontsa ki a tanúsítványok csomópontot, nyissa meg a távoli Desktop\Certificates mappát, kattintson a jobb gombbal a tanúsítványra, majd válassza a **Törlés**lehetőséget.

8. Indítsa újra a Távoli asztal konfigurációs szolgáltatást:

   ```cmd
   net stop SessionEnv
   net start SessionEnv
   ```

   >[!Note]
   >Ha ezen a ponton frissíti az áruházat az MMC-ből, a tanúsítvány újra megjelenik. 

Próbálja meg újra az RDP használatával elérni a virtuális gépet.

#### <a name="update-tlsssl-certificate"></a>TLS/SSL-tanúsítvány frissítése

Ha a virtuális gépet TLS/SSL-tanúsítvány használatára állítja be, a következő parancs futtatásával kérheti le az ujjlenyomatot. Ezután győződjön meg arról, hogy a Tanúsítvány ujjlenyomata megegyezik-e:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

Ha nem, módosítsa az ujjlenyomatot:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash /t REG_BINARY /d <CERTIFICATE THUMBPRINT>
```

Megpróbálhatja törölni a kulcsot úgy is, hogy az RDP az önaláírt tanúsítványt használja az RDP-hez:

```cmd
reg delete "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

## <a name="scenario-2"></a>2. példa

### <a name="event-log"></a>Eseménynapló

Egy CMD-példányban futtassa a következő parancsokat, hogy meggyőződjön arról, hogy a 36871-es SCHANNEL-hiba az elmúlt 24 órában a rendszernaplóban van-e naplózva:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36871 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Napló neve:**      Rendszer <br />
**Forrás:**        Schannel <br />
**Dátum:** – <br />
**Eseményazonosító:** 36871 <br />
**Feladat kategóriája:** NEz egy <br />
**Szint:**         Hiba <br />
**Kulcsszavak**       <br />
**Felhasználó:**          RENDSZER <br />
**Számítógép:**      *számítógép* <br />
**Leírás:** Végzetes hiba történt a TLS-kiszolgáló hitelesítő adatainak létrehozásakor. A belső hiba állapota 10013.
 
### <a name="cause"></a>Ok

Ezt a problémát a biztonsági házirendek okozzák. Ha a TLS régebbi verziói (például 1,0) le vannak tiltva, az RDP-hozzáférés meghiúsul.

### <a name="resolution"></a>Megoldás:

Az RDP a TLS 1,0 protokollt használja alapértelmezett protokollként. Előfordulhat azonban, hogy a protokoll a TLS 1,1-re módosul, amely az új standard.

A probléma megoldásához lásd: [hitelesítési hibák elhárítása, amikor RDP használatával csatlakozik az Azure-beli virtuális géphez](troubleshoot-authentication-error-rdp-vm.md#tls-version).

## <a name="scenario-3"></a>3. példa

Ha telepítette a **Távoli asztali kapcsolatszervező** szerepkört a virtuális gépen, ellenőrizze, hogy van-e az elmúlt 24 órában a 2056-es vagy a 1296-es esemény. Futtassa a következő parancsokat egy CMD-példányban: 

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker '] and EventID=2056 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker-Client '] and EventID=1296 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Napló neve:**      Microsoft-Windows-TerminalServices-SessionBroker/Operational <br />
**Forrás:**        Microsoft-Windows-TerminalServices-SessionBroker <br />
**Dátum:**          *idő* <br />
**Eseményazonosító:** 2056 <br />
**Feladat kategóriája:** (109) <br />
**Szint:**         Hiba <br />
**Kulcsszavak**       <br />
**Felhasználó:**          HÁLÓZATI SZOLGÁLTATÁS <br />
**Számítógép:**      *számítógép teljes tartományneve* <br />
**Leírás:** Az 2056-as AZONOSÍTÓJÚ esemény leírása a forrás Microsoft-Windows-TerminalServices-SessionBroker nem található. Vagy az eseményt felváltó összetevő nincs telepítve a helyi számítógépen, vagy a telepítés megsérült. Telepítheti vagy kijavíthatja a helyi számítógépen található összetevőt. <br />
Ha az esemény egy másik számítógépen származik, a megjelenítési adatokat az eseménysel kell menteni. <br />
Az esemény a következő információkat tartalmazza: <br />
NULL <br />
NULL <br />
Sikertelen volt az adatbázisba való bejelentkezés.

**Napló neve:**      Microsoft-Windows-TerminalServices-SessionBroker-Client/Operational <br />
**Forrás:**        Microsoft-Windows-TerminalServices-SessionBroker-Client <br />
**Dátum:**          *idő* <br />
**Eseményazonosító:** 1296 <br />
**Feladat kategóriája:** (104) <br />
**Szint:**         Hiba <br />
**Kulcsszavak**       <br />
**Felhasználó:**          HÁLÓZATI SZOLGÁLTATÁS <br />
**Számítógép:**      *számítógép teljes tartományneve* <br />
**Leírás:** A 1296-as AZONOSÍTÓJÚ esemény leírása a forrás Microsoft-Windows-TerminalServices-SessionBroker-Client nem található. Vagy az eseményt felváltó összetevő nincs telepítve a helyi számítógépen, vagy a telepítés megsérült. Telepítheti vagy kijavíthatja a helyi számítógépen található összetevőt.
Ha az esemény egy másik számítógépen származik, a megjelenítési adatokat az eseménysel kell menteni.
Az esemény a következő információkat tartalmazza:  <br />
*szöveg* <br />
*szöveg* <br />
A Távoli asztali kapcsolatszervező nem áll készen az RPC-kommunikációra.

### <a name="cause"></a>Ok

Ez a probléma azért fordul elő, mert a Távoli asztali kapcsolatszervező-kiszolgáló állomásneve megváltozik, ami nem támogatott változás. 

Az állomásnév a belső Windows-adatbázis bejegyzéseivel és függőségeivel rendelkezik, amelyekre szükség van a Távoli asztal Service Farm számára ahhoz, hogy működni tudjon. Ha úgy módosítja a gazdagépet, hogy a farm már készen áll, számos hibát okoz, és a közvetítő kiszolgáló leállhat.

### <a name="resolution"></a>Megoldás: 

A probléma megoldásához újra kell telepíteni a Távoli asztali kapcsolatszervező szerepkört és a belső Windows-adatbázist.

## <a name="next-steps"></a>Következő lépések

[Schannel-események](https://technet.microsoft.com/library/dn786445(v=ws.11).aspx)

[Schannel SSP Technical Overview (A Schannel SSP műszaki áttekintése)](https://technet.microsoft.com/library/dn786429(v=ws.11).aspx)

[Az RDP sikertelen az 1058-es azonosítójú esemény & a 36870-es azonosítójú esemény Távoli asztal a munkamenet-gazdagép tanúsítványával & SSL-kommunikáció](https://blogs.technet.microsoft.com/askperf/2014/10/22/rdp-fails-with-event-id-1058-event-36870-with-remote-desktop-session-host-certificate-ssl-communication/)

[Schannel 36872 vagy Schannel 36870 egy tartományvezérlőn](https://blogs.technet.microsoft.com/instan/2009/01/05/schannel-36872-or-schannel-36870-on-a-domain-controller/)

[1058-es AZONOSÍTÓJÚ esemény – hitelesítés és titkosítás Távoli asztali szolgáltatások](https://technet.microsoft.com/library/ee890862(v=ws.10).aspx)

