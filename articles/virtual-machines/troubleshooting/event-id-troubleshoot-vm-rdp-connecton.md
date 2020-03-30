---
title: Az Azure VM RDP-csatlakozási problémáinak elhárítása eseményazonosító szerint | Microsoft dokumentumok
description: ''
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
ms.openlocfilehash: 166648402eec7f8033c090a3f7862a902bae4be6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71154197"
---
# <a name="troubleshoot-azure-vm-rdp-connection-issues-by-event-id"></a>Az Azure-beli virtuális gépek kapcsolati hibáinak elhárítása eseményazonosító alapján 

Ez a cikk bemutatja, hogyan használhatja az eseményazonosítókat az On-da (Virtuális gép) Távoli asztali protokoll (RDP) kapcsolatának megakadályozását okozó problémák elhárítására.

## <a name="symptoms"></a>Probléma

Egy RDP-munkamenet használatával próbál meg csatlakozni egy Azure-beli virtuális géphez. A hitelesítő adatok megadása után a kapcsolat megszakad, és a következő hibaüzenet jelenik meg:

**Ez a számítógép nem tud csatlakozni a távoli számítógéphez. Ha a probléma továbbra is fennáll, próbáljon meg újra csatlakozni, forduljon a távoli számítógép tulajdonosához vagy a hálózati rendszergazdához.**

A probléma elhárításához tekintse át az eseménynaplókat a virtuális gépen, majd olvassa el az alábbi eseteket.

## <a name="before-you-troubleshoot"></a>A hibaelhárítás előtt

### <a name="create-a-backup-snapshot"></a>Biztonsági pillanatkép létrehozása

Biztonsági másolat pillanatképének létrehozásához kövesse a [Lemez pillanatképe](../windows/snapshot-copy-managed-disk.md)című részben leírt lépéseket.

### <a name="connect-to-the-vm-remotely"></a>Csatlakozás a virtuális géphez távolról

A virtuális géphez való távoli csatlakozáshoz használja a Távoli eszközök használata az [Azure virtuális gépekkel kapcsolatos problémák elhárításához](remote-tools-troubleshoot-azure-vm-issues.md)című témakör egyik metódusát.

## <a name="scenario-1"></a>1. példa

### <a name="event-logs"></a>Eseménynaplók

CmD-példányban futtassa a következő parancsokat annak ellenőrzésére, hogy az 1058-as vagy az 1057-es esemény be van-e jelentkezve a rendszernaplóba az elmúlt 24 órában:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1058 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-TerminalServices-RemoteConnectionManager'] and EventID=1057 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Napló neve:**      Rendszer <br />
**Forrás:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Dátum:**          *idő* <br />
**Eseményazonosító:** 1058 <br />
**Feladatkategória:** Nincs <br />
**Szint:**         Hiba <br />
**Kulcsszavak:**      Klasszikus <br />
**Felhasználó:**          N/a <br />
**Számítógép:**      *számítógép* <br />
**Leírás:** A Távoli asztali munkamenetgazda-kiszolgáló nem tudta lecserélni a Távoli asztali munkamenetgazda-kiszolgáló SSL-kapcsolatokon történő hitelesítéséhez használt lejárt, saját aláírással aláírt tanúsítványt. A vonatkozó állapotkód a hozzáférés megtagadva volt.

**Napló neve:**      Rendszer <br />
**Forrás:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Dátum:**          *idő* <br />
**Eseményazonosító:** 1058 <br />
**Feladatkategória:** Nincs <br />
**Szint:**         Hiba <br />
**Kulcsszavak:**      Klasszikus <br />
**Felhasználó:**          N/a <br />
**Számítógép:**      *számítógép* <br />
**Leírás:** A Távoli asztali munkamenetgazda-kiszolgáló nem tudott új, önaláírt tanúsítványt létrehozni az SSL-kapcsolatokon a Távoli asztali munkamenetgazda-kiszolgáló hitelesítéséhez, a megfelelő állapotkód már létezik.

**Napló neve:**      Rendszer <br />
**Forrás:**        Microsoft-Windows-TerminalServices-RemoteConnectionManager <br />
**Dátum:**          *idő* <br />
**Eseményazonosító:** 1057 <br />
**Feladatkategória:** Nincs <br />
**Szint:**         Hiba <br />
**Kulcsszavak:**      Klasszikus <br />
**Felhasználó:**          N/a <br />
**Számítógép:**      *számítógép* <br />
**Leírás:** A Távoli asztali munkamenetgazda-kiszolgáló nem tudott új, önaláírt tanúsítványt létrehozni a Távoli asztali munkamenetgazda-kiszolgáló SSL-kapcsolatokon történő hitelesítéséhez. A megfelelő állapotkód nem létezik a Kulcskészlet

A 36872-es és a 36870-es SCHANNEL-hibaeseményeket a következő parancsok futtatásával is ellenőrizheti:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36870 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36872 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Napló neve:**      Rendszer <br />
**Forrás:**        Schannel <br />
**Dátum:** — <br />
**Eseményazonosító:** 36870 <br />
**Feladatkategória:** Nincs <br />
**Szint:**         Hiba <br />
**Kulcsszavak:**       <br />
**Felhasználó:**          Rendszer <br />
**Számítógép:**      *számítógép* <br />
**Leírás:** Végzetes hiba történt az SSL-kiszolgáló hitelesítő adatainak titkos kulcsához való hozzáférés megkísérléseksorán. A kriptográfiai modulból visszaadott hibakód: 0x8009030D.  <br />
A belső hiba állapota 10001.

### <a name="cause"></a>Ok
Ez a probléma azért fordul elő, mert a helyi RSA titkosítási kulcsok a MachineKeys mappában a virtuális gép nem érhető el. A probléma a következő okok valamelyike miatt fordulhat elő:

1. Hibás engedélykonfiguráció a Gépkulcsok mappában vagy az RSA-fájlokban.

2. Sérült vagy hiányzó RSA kulcs.

### <a name="resolution"></a>Megoldás:

A probléma elhárításához az rdp-tanúsítványmegfelelő engedélyeit az alábbi lépésekkel kell beállítania.

#### <a name="grant-permission-to-the-machinekeys-folder"></a>Engedély megadása a MachineKeys mappának

1. Hozzon létre parancsfájlt a következő tartalommal:

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

2.  Futtassa ezt a parancsfájlt a MachineKey mappa engedélyeinek visszaállításához és az RSA-fájlok alapértelmezett értékekre való visszaállításához.

3.  Próbálja meg újra elérni a virtuális gép.

A parancsfájl futtatása után ellenőrizheti az alábbi, engedélyekkel kapcsolatos problémákat tapasztaló fájlokat:

* c:\temp\BeforeScript_permissions.txt
* c:\temp\AfterScript_permissions.txt

#### <a name="renew-rdp-self-signed-certificate"></a>RdP önaláírt tanúsítvány megújítása

Ha a probléma továbbra is fennáll, futtassa a következő parancsfájlt, és győződjön meg arról, hogy az RDP önaláírt tanúsítványa megújult:

```powershell
Import-Module PKI
Set-Location Cert:\LocalMachine
$RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint)
Remove-Item -Path $RdpCertThumbprint
Stop-Service -Name "SessionEnv"
Start-Service -Name "SessionEnv"
```

Ha nem tudja megújítani a tanúsítványt, az alábbi lépésekkel próbálja meg törölni a tanúsítványt:

1. Egy másik virtuális gép ugyanazon a virtuális hálózat, nyissa meg a **Futtatás** mezőben, írja **be az mmc**, majd nyomja le az OK **gombot.** 

2. A **Fájl** menüben válassza a **Beépülő modul hozzáadása/eltávolítása parancsot.**

3. Az **Elérhető beépülő modulok** listában válassza a **Tanúsítványok**elemet, majd a **Hozzáadás**lehetőséget.

4. Válassza **a Számítógépfiók (Számítógépfiók)** lehetőséget, majd a **Tovább**gombot.

5. Válassza a **Másik számítógép**lehetőséget, majd adja hozzá a problémákkal kapcsolatos virtuális gép IP-címét.
   >[!Note]
   >Próbálja meg a belső hálózatot használni a virtuális IP-cím használatának elkerülésére.

6. Válassza **a Befejezés**lehetőséget, majd az **OK**gombot.

   ![Számítógép kiválasztása](./media/event-id-troubleshoot-vm-rdp-connecton/select-computer.png)

7. Bontsa ki a tanúsítványokat, nyissa meg a Távoli asztal\Tanúsítványok mappát, kattintson a jobb gombbal a tanúsítványra, és válassza a **Törlés parancsot.**

8. Indítsa újra a Távoli asztal konfigurációja szolgáltatást:

   ```cmd
   net stop SessionEnv
   net start SessionEnv
   ```

   >[!Note]
   >Ezen a ponton, ha frissíti a tárolót az mmc-ről, a tanúsítvány újra megjelenik. 

Próbálja meg elérni a virtuális gép segítségével RDP újra.

#### <a name="update-secure-sockets-layer-ssl-certificate"></a>Secure Sockets Layer (SSL) tanúsítvány frissítése

Ha a virtuális gép beállítása ssl-tanúsítvány használatára, futtassa a következő parancsot az ujjlenyomat levételéhez. Ezután ellenőrizze, hogy ugyanaz-e, mint a tanúsítvány ujjlenyomata:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

Ha nem, módosítsa az ujjlenyomatot:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash /t REG_BINARY /d <CERTIFICATE THUMBPRINT>
```

Megpróbálhatja törölni a kulcsot is, hogy az RDP az RDP önaláírt tanúsítványát használja:

```cmd
reg delete "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SSLCertificateSHA1Hash
```

## <a name="scenario-2"></a>2. példa

### <a name="event-log"></a>Eseménynapló

CmD-példányban futtassa a következő parancsokat annak ellenőrzésére, hogy a 36871-es SCHANNEL-hibaesemény be van-e jelentkezve a rendszernaplóba az elmúlt 24 órában:

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Schannel'] and EventID=36871 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Napló neve:**      Rendszer <br />
**Forrás:**        Schannel <br />
**Dátum:** — <br />
**Eseményazonosító:** 36871 <br />
**Feladatkategória:** Nincs <br />
**Szint:**         Hiba <br />
**Kulcsszavak:**       <br />
**Felhasználó:**          Rendszer <br />
**Számítógép:**      *számítógép* <br />
**Leírás:** Végzetes hiba történt a TLS-kiszolgáló hitelesítő adatainak létrehozása közben. A belső hiba állapota 10013.
 
### <a name="cause"></a>Ok

A problémát biztonsági házirendek okozzák. Ha a TLS régebbi verziói (például az 1.0) le vannak tiltva, az RDP-hozzáférés sikertelen lesz.

### <a name="resolution"></a>Megoldás:

Az RDP a TLS 1.0 protokollt használja alapértelmezett protokollként. A protokoll azonban tls 1.1-re módosítható, ami az új szabvány.

A probléma elhárításához olvassa [el A hitelesítési hibák elhárítása az RdP használatával az Azure VM szolgáltatáshoz való csatlakozáshoz című témakört.](troubleshoot-authentication-error-rdp-vm.md#tls-version)

## <a name="scenario-3"></a>3. példa

Ha telepítette a **Távoli asztali kapcsolatszervező** szerepkört a virtuális gépre, ellenőrizze, hogy van-e esemény 2056 vagy esemény 1296 az elmúlt 24 órában. CmD-példányban futtassa a következő parancsokat: 

```cmd
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker '] and EventID=2056 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name=' Microsoft-Windows-TerminalServices-SessionBroker-Client '] and EventID=1296 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more
```

**Napló neve:**      Microsoft-Windows-TerminalServices-SessionBroker/Operational <br />
**Forrás:**        Microsoft-Windows-TerminalServices-SessionBroker <br />
**Dátum:**          *idő* <br />
**Eseményazonosító:** 2056 <br />
**Feladat kategória:** (109) <br />
**Szint:**         Hiba <br />
**Kulcsszavak:**       <br />
**Felhasználó:**          HÁLÓZATI SZOLGÁLTATÁS <br />
**Számítógép:**      *számítógép fqdn* <br />
**Leírás:** A Microsoft-Windows-TerminalServices-SessionBroker forrásból származó 2056-os eseményazonosító leírása nem található. Az eseményt okozó összetevő nincs telepítve a helyi számítógépre, vagy a telepítés sérült. Az összetevőt telepítheti vagy javíthatja a helyi számítógépen. <br />
Ha az esemény egy másik számítógépről származik, a megjelenítési adatokat az eseménysel együtt kellett menteni. <br />
Az esemény a következő információkat tartalmazta: <br />
NULL <br />
NULL <br />
Nem sikerült bejelentkezni az adatbázisba.

**Napló neve:**      Microsoft-Windows-TerminalServices-SessionBroker-Kliens/Működés <br />
**Forrás:**        Microsoft-Windows-TerminalServices-SessionBroker-Ügyfél <br />
**Dátum:**          *idő* <br />
**Esemény azonosítója:** 1296 <br />
**Feladat kategória:** (104) <br />
**Szint:**         Hiba <br />
**Kulcsszavak:**       <br />
**Felhasználó:**          HÁLÓZATI SZOLGÁLTATÁS <br />
**Számítógép:**      *számítógép fqdn* <br />
**Leírás:** A Microsoft-Windows-TerminalServices-SessionBroker-Client forrásból származó 1296-os eseményazonosító leírása nem található. Az eseményt okozó összetevő nincs telepítve a helyi számítógépre, vagy a telepítés sérült. Az összetevőt telepítheti vagy javíthatja a helyi számítógépen.
Ha az esemény egy másik számítógépről származik, a megjelenítési adatokat az eseménysel együtt kellett menteni.
Az esemény a következő információkat tartalmazta:  <br />
*Szöveg* <br />
*Szöveg* <br />
A Távoli asztali kapcsolatszervező nem áll készen az RPC-kommunikációra.

### <a name="cause"></a>Ok

A probléma oka az, hogy a Távoli asztali kapcsolatszervező kiszolgáló állomásneve megváltozott, ami nem támogatott módosítás. 

Az állomásnév bejegyzéseket és függőségeket tartalmaz a Windows belső adatbázisában, amelyre a Távoli asztali szolgáltatás farmnak szüksége van a munka elvégzéséhez. A gazdagépnév módosítása a farm felépítése után sok hibát okoz, és a közvetítőkiszolgáló leáll.

### <a name="resolution"></a>Megoldás: 

A probléma megoldásához újra kell telepíteni a Távoli asztali kapcsolatszervező szerepkört és a Windows belső adatbázisát.

## <a name="next-steps"></a>Következő lépések

[Schannel események](https://technet.microsoft.com/library/dn786445(v=ws.11).aspx)

[Schannel SSP Technical Overview (A Schannel SSP műszaki áttekintése)](https://technet.microsoft.com/library/dn786429(v=ws.11).aspx)

[Az RDP nem működik az 1058-as & 36870-es eseményazonosítóval, amely távoli asztali munkamenetgazda-tanúsítvánnyal & SSL-kommunikációval](https://blogs.technet.microsoft.com/askperf/2014/10/22/rdp-fails-with-event-id-1058-event-36870-with-remote-desktop-session-host-certificate-ssl-communication/)

[Schannel 36872 vagy Schannel 36870 tartományvezérlőn](https://blogs.technet.microsoft.com/instan/2009/01/05/schannel-36872-or-schannel-36870-on-a-domain-controller/)

[1058-as eseményazonosító – Távoli asztali szolgáltatások hitelesítése és titkosítása](https://technet.microsoft.com/library/ee890862(v=ws.10).aspx)

