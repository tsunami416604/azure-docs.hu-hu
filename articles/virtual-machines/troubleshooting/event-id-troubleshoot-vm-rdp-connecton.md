---
title: Eseményazonosító szerint az Azure virtuális gép RDP-kapcsolatok problémáinak elhárítása |} A Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/01/2018
ms.author: delhan
ms.openlocfilehash: b84992f5deea1135692c368900f63773b51453bb
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2018
ms.locfileid: "50634064"
---
# <a name="troubleshoot-azure-vm-rdp-connection-issues-by-event-id"></a>Eseményazonosító szerint az Azure virtuális gép RDP-kapcsolatok problémáinak hibaelhárítása 

Ez a cikk bemutatja, hogyan eseményazonosítók használata a problémák, amelyek meggátolják a távoli asztal protokoll (RDP) kapcsolatot, egy Azure virtuális gép (VM).

## <a name="symptoms"></a>Probléma

A távoli asztal protokoll (RDP) munkamenet használata egy Azure virtuális Géphez való csatlakozáshoz próbálja ki. Azt adja meg a hitelesítő adatokat, miután létesített kapcsolat megszakad, és a következő hibaüzenetet kapja:

**Ez a számítógép nem tud kapcsolódni a távoli számítógépen. Próbáljon meg újra, ha a probléma továbbra is fennáll, forduljon a tulajdonosa, a távoli számítógép vagy a hálózati rendszergazdához.**

A probléma elhárításához eseménynaplójában keresse meg a virtuális gépen, és ezután tekintse meg a következő esetekben.

## <a name="before-you-troubleshoot"></a>Mielőtt hibaelhárítás

### <a name="create-a-backup-snapshot"></a>Biztonsági mentési pillanatkép létrehozása

Hozzon létre egy biztonsági mentési pillanatképet, kövesse a [lemez pillanatképének elkészítése](..\windows\snapshot-copy-managed-disk.md).

### <a name="connect-to-the-vm-remotely"></a>Távolról csatlakozhat a virtuális gép

Távolról csatlakozhat a virtuális Gépet, használja a módszer [táveszközök használata Azure virtuális gép hibáinak elhárítása](remote-tools-troubleshoot-azure-vm-issues.md).

## <a name="scenario-1"></a>1. forgatókönyv

### <a name="event-logs"></a>Eseménynaplók

CMD-példányban futtassa a következő parancsok futtatásával ellenőrizze, hogy 1058 és 1057 eseményekre kerül a rendszernaplóba az elmúlt 24 órában:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1058 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1057 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Napló neve:** rendszer <br />
**Forrás:** Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Dátum:***idő*  <br />
**Eseményazonosító:** 1058 <br />
**Feladat kategóriája:** None <br />
**Szint:** hiba <br />
**Kulcsszavak:** klasszikus <br />
**Felhasználó:** N/A <br />
**Számítógép:***számítógép*  <br />
**Leírás:** a távoli asztali munkamenet kiszolgáló nem tudta cserélje le az önaláírt SSL-kapcsolatok a távoli asztali munkamenet kiszolgáló hitelesítéshez használt tanúsítvány lejárt. A megfelelő állapotkód volt, a hozzáférés megtagadva.

**Napló neve:** rendszer <br />
**Forrás:** Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Dátum:***idő*  <br />
**Eseményazonosító:** 1058 <br />
**Feladat kategóriája:** None <br />
**Szint:** hiba <br />
**Kulcsszavak:** klasszikus <br />
**Felhasználó:** N/A <br />
**Számítógép:***számítógép*  <br />
**Leírás:** hozzon létre egy új önaláírt tanúsítvány használható SSL-kapcsolatok a távoli asztali munkamenet gazdagép kiszolgálóhitelesítés távoli asztali munkamenetgazda kiszolgálónak nem sikerült, a megfelelő állapotkód volt az objektum már létezik.

**Napló neve:** rendszer <br />
**Forrás:** Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Dátum:***idő*  <br />
**Eseményazonosító:** 1057 <br />
**Feladat kategóriája:** None <br />
**Szint:** hiba <br />
**Kulcsszavak:** klasszikus <br />
**Felhasználó:** N/A <br />
**Számítógép:***számítógép*  <br />
**Leírás:** a távoli asztali munkamenet kiszolgáló nem tudta hozzon létre egy új önaláírt SSL-kapcsolatok a távoli asztali munkamenet kiszolgáló hitelesítéshez használandó tanúsítványt. Az ide tartozó állapotkód kulcskészletet nem létezik.

Ellenőrizheti az SCHANNEL-események hiba 36872-es és 36870-es a következő parancsok futtatásával:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36870 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36872 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Napló neve:** rendszer <br />
**Forrás:** Schannel <br />
**Dátum:** – <br />
**Eseményazonosító:** 36870-es <br />
**Feladat kategóriája:** None <br />
**Szint:** hiba <br />
**Kulcsszavak:**       <br />
**Felhasználó:** rendszer <br />
**Számítógép:***számítógép*  <br />
**Leírás:** végzetes hiba történt az SSL kiszolgálói hitelesítő adatok titkos kulcsának elérésekor. A titkosító modul által visszaadott hibakód: 0x8009030d.  <br />
Belső hiba állapota 10001.

### <a name="cause"></a>Ok
A probléma oka, hogy a virtuális gép MachineKeys mappájában lévő helyi RSA titkosítási kulcsok nem érhetők el. A probléma a következő okok miatt fordulhat elő:

1. Nem megfelelő engedélyek konfigurálása a Machinekeys mappát vagy az RSA-fájlokat.

2. Sérült vagy hiányzó RSA-kulcsot.

### <a name="resolution"></a>Megoldás:

A probléma elhárításához, akkor állítsa be a megfelelő engedélyeket az RDP-tanúsítványt az alábbi lépéseket.

#### <a name="grant-permission-to-the-machinekeys-folder"></a>Engedély megadása a MachineKeys mappa

1. Hozzon létre egy parancsfájl segítségével az alábbi tartalommal:

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

2.  Futtassa ezt a szkriptet, alaphelyzetbe állítja a MachineKey mappa engedélyeit és az RSA-fájlok visszaállítása az alapértelmezett értékeket.

3.  Próbálja meg ismét elérni a virtuális gép.

A szkript futtatása után a következő fájlokat, amelyek engedélyek problémákat tapasztal, amikor ellenőrizheti:

* c:\temp\BeforeScript_permissions.txt
* c:\temp\AfterScript_permissions.txt

#### <a name="renew-rdp-self-signed-certificate"></a>RDP-önaláírt tanúsítvány megújítása

Ha a probléma nem szűnik meg, futtassa a következő szkriptet győződjön meg arról, hogy az RDP önaláírt tanúsítvány megújításának:

```powershell
Import-Module PKI
Set-Location Cert:\LocalMachine
$RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint)
Remove-Item -Path $RdpCertThumbprint
Stop-Service -Name "SessionEnv"
Start-Service -Name "SessionEnv"
```

Ha a tanúsítvány megújításához nem próbálja meg törölni a tanúsítványt az alábbi lépésekkel:

1. Egy másik virtuális Géphez ugyanazon a vneten, nyissa meg a **futtatása** mezőbe írja be **mmc**, és nyomja le az **OK**. 

2. Az a **fájl** menüjében válassza **beépülő modul hozzáadása/eltávolítása**.

3. Az a **elérhető beépülő modulok** listáról válassza ki **tanúsítványok**, majd válassza ki **Hozzáadás**.

4. Válassza ki **számítógépfiók**, majd válassza ki **tovább**.

5. Válassza ki **egy másik számítógépre**, majd adja hozzá az IP-címet a virtuális gép által kapcsolatban probléma merült fel.
   >[!Note]
   >Próbálja meg használni a belső hálózathoz virtuális IP-címet használó elkerülése érdekében.

6. Válassza ki **Befejezés**, majd válassza ki **OK**.

   ![Számítógép kiválasztása](./media/event-id-troubleshoot-vm-rdp-connecton/select-computer.png)

7. Bontsa ki a tanúsítványokat, nyissa meg a távoli Desktop\Certificates mappát, kattintson a jobb gombbal a tanúsítványt, és válassza **törlése**.

8. Indítsa újra a távoli asztal konfigurálásának szolgáltatást:

   ```cmd
   net stop SessionEnv
   net start SessionEnv
   ```

   >[!Note]
   >Ezen a ponton frissíti a tároló az mmc, ha a tanúsítványt ismét megjelenik. 

Próbálja meg elérni a virtuális gép RDP használatával újra.

#### <a name="update-secure-sockets-layer-ssl-certificate"></a>Secure Sockets Layer (SSL)-tanúsítvány frissítése

Ha beállította a virtuális gép egy SSL-tanúsítvány használatára, a következő parancsot az ujjlenyomat beolvasása. Ezután ellenőrizze, hogy ez ugyanaz, mint a tanúsítvány ujjlenyomata:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

Ha nem, módosítsa az ujjlenyomat:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash /t REG_BINARY /d <CERTIFICATE THUMBPRINT>
```

Is megpróbálhatja törölni szeretné a kulcsot, hogy az RDP RDP-hez az önaláírt tanúsítványt használ:

```cmd
reg delete "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

## <a name="scenario-2"></a>2. forgatókönyv

### <a name="event-log"></a>Eseménynapló

CMD-példányban futtassa a következő parancsok futtatásával ellenőrizze, hogy az SCHANNEL hiba esemény 36871 a rendszernaplóba kerül, az elmúlt 24 órában:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36871 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Napló neve:** rendszer <br />
**Forrás:** Schannel <br />
**Dátum:** – <br />
**Eseményazonosító:** 36871 <br />
**Feladat kategóriája:** None <br />
**Szint:** hiba <br />
**Kulcsszavak:**       <br />
**Felhasználó:** rendszer <br />
**Számítógép:***számítógép*  <br />
**Leírás:** végzetes hiba történt egy TLS kiszolgálói hitelesítő adatok létrehozása során. Belső hiba állapota 10013.
 
### <a name="cause"></a>Ok

A probléma okozza a biztonsági szabályzatokat. Ha régebbi verziói a TLS (például az 1.0-s) le vannak tiltva, az RDP-hozzáférés meghiúsul.

### <a name="resolution"></a>Megoldás:

RDP TLS 1.0-t használja, mint az alapértelmezett protokoll. Azonban a protokoll előfordulhat, hogy módosítható a TLS 1.1, vagyis az új standard.

A probléma elhárításához lásd: [hitelesítési hibák elhárítása Azure virtuális géphez csatlakozni RDP használatakor](troubleshoot-authentication-error-rdp-vm.md#tls-version).

## <a name="scenario-3"></a>3. forgatókönyv

Ha már telepítette a **távoli asztali Kapcsolatszervező** szerepkör a virtuális gépen, ellenőrizze, hogy van-e esemény 2056 vagy 1296 eseményt az elmúlt 24 órában. CMD-példányban futtassa a következő parancsokat: 

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker '] and EventID=2056 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker-Client '] and EventID=1296 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Napló neve:** Microsoft-Windows-TerminalServices-SessionBroker/Operational <br />
**Forrás:** Microsoft-Windows-TerminalServices-SessionBroker <br />
**Dátum:***idő*  <br />
**Eseményazonosító:** 2056 <br />
**Feladat kategóriája:** (109) <br />
**Szint:** hiba <br />
**Kulcsszavak:**       <br />
**Felhasználó:** hálózati szolgáltatás <br />
**Számítógép:***számítógép teljes tartományneve*  <br />
**Leírás:** Event ID 2056 forrásból Microsoft-Windows-TerminalServices-SessionBroker leírása nem található. Az összetevő, amely kiváltja ezt az eseményt a helyi számítógépen nincs telepítve, vagy sérült a telepítés. Telepítse, vagy javítsa ki az összetevő a helyi számítógépen. <br />
Ha az esemény származik, egy másik számítógépen, a megjelenített információk kellett menthető az eseményhez. <br />
Az esemény lett a következő információkat: <br />
NULL <br />
NULL <br />
Nem sikerült bejelentkezni az adatbázisba.

**Napló neve:** Microsoft-Windows-TerminalServices-SessionBroker-Client/Operational <br />
**Forrás:** Microsoft-Windows-TerminalServices-SessionBroker-ügyfél <br />
**Dátum:***idő*  <br />
**Eseményazonosító:** 1296 <br />
**Feladat kategóriája:** (104) <br />
**Szint:** hiba <br />
**Kulcsszavak:**       <br />
**Felhasználó:** hálózati szolgáltatás <br />
**Számítógép:***számítógép teljes tartományneve*  <br />
**Leírás:** Event ID 1296 forrásból Microsoft-Windows-TerminalServices-SessionBroker-ügyfél leírása nem található. Az összetevő, amely kiváltja ezt az eseményt a helyi számítógépen nincs telepítve, vagy sérült a telepítés. Telepítse, vagy javítsa ki az összetevő a helyi számítógépen.
Ha az esemény származik, egy másik számítógépen, a megjelenített információk kellett menthető az eseményhez.
Az esemény lett a következő információkat:  <br />
*Szöveg* <br />
*Szöveg* <br />
Távoli asztali Kapcsolatszervező nem áll készen a RPC-kommunikációhoz.

### <a name="cause"></a>Ok

Ez a probléma akkor fordul elő, mert a gazdagép nevét a távoli asztali Kapcsolatszervező kiszolgáló módosult, ami nem támogatott, ha változás történik. 

A hostname rendelkezik bejegyzéseket és a függőségek a Windows belső adatbázis, váljanak a távoli asztali szolgáltatás farm annak érdekében, hogy képesek együttműködni. Az eszköznév módosítása után a farm már épül sok hibát okoz, és nem működik az átvitelszervező kiszolgálón okozhat.

### <a name="resolution"></a>Megoldás: 

A probléma megoldásához, a távoli asztali Kapcsolatszervező szerepkör és a belső Windows-adatbázist újra kell telepíteni.

## <a name="next-steps"></a>További lépések

[Schannel-események](https://technet.microsoft.com/library/dn786445(v=ws.11).aspx)

[Schannel SSP műszaki áttekintése](https://technet.microsoft.com/library/dn786429(v=ws.11).aspx)

[Az eseményazonosító 1058 & 36870-es esemény a távoli asztali munkamenetet gazdagép tanúsítványának & SSL-kommunikáció sikertelen RDP](https://blogs.technet.microsoft.com/askperf/2014/10/22/rdp-fails-with-event-id-1058-event-36870-with-remote-desktop-session-host-certificate-ssl-communication/)

[Schannel 36872-es vagy Schannel 36870-es on a Domain Controller](https://blogs.technet.microsoft.com/instan/2009/01/05/schannel-36872-or-schannel-36870-on-a-domain-controller/)

[Eseményazonosító 1058 – Hitelesítési és titkosítást a távoli asztali szolgáltatások](https://technet.microsoft.com/library/ee890862(v=ws.10).aspx)

