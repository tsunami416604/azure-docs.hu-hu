---
title: Hitelesítési hibák elhárítása Azure virtuális géphez csatlakozni RDP használatakor |} A Microsoft Docs
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
ms.openlocfilehash: 47d3b827099d3a4a7520ac66765d2928795b6e49
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52967980"
---
# <a name="troubleshoot-authentication-errors-when-you-use-rdp-to-connect-to-azure-vm"></a>Hitelesítési hibák elhárítása Azure virtuális géphez csatlakozni RDP használatakor

Ez a cikk segítséget nyújt a távoli asztal protokoll (RDP) kapcsolatot szeretne csatlakozni egy Azure virtuális gép (VM) használatakor fellépő hitelesítési hibák elhárítása.

## <a name="symptoms"></a>Probléma

Képernyőkép: egy Azure virtuális Gépen, amely azt jelzi, hogy az operációs rendszer fut-e, és megjeleníti az üdvözlőképernyőjét rögzíti. Azonban ha próbál csatlakozni a virtuális gép távoli asztali kapcsolattal, kap az alábbi hibaüzenetek valamelyike.

### <a name="error-message-1"></a>Chybová zpráva 1

**Hitelesítési hiba történt. Nem lehet kapcsolódni a helyi biztonsági szervezet.**

### <a name="error-message-2"></a>Chybová zpráva 2

**A távoli számítógép, amely a hálózati szintű hitelesítés (NLA) igényel csatlakozni próbált, de a Windows tartományvezérlő nem érhető el NLA végrehajtásához. Ha Ön rendszergazda, a távoli számítógépen, a beállításokat a távoli lapon a rendszer tulajdonságai párbeszédpanel használatával NLA is letilthatja.**

### <a name="error-message-3-generic-connection-error"></a>Chybová zpráva 3 (általános kapcsolati hiba)

**Ez a számítógép nem tud kapcsolódni a távoli számítógépen. Próbáljon meg újra, ha a probléma továbbra is fennáll, forduljon a tulajdonosa, a távoli számítógép vagy a hálózati rendszergazdához.**

## <a name="cause"></a>Ok

Több oka miért NLA blokkolhatják a virtuális géphez RDP-hozzáférést.

### <a name="cause-1"></a>OK: 1

A virtuális gép nem tud kommunikálni a tartományvezérlővel (DC). Ez a probléma megakadályozhatja, hogy egy RDP-munkamenetet egy virtuális gép eléréséhez tartományi hitelesítő adataival. Azonban Ön továbbra is lenne a helyi rendszergazdai hitelesítő adataival jelentkezhet be. Ez a probléma a következő esetekben fordulhat elő:

1. Ez a virtuális gép és a tartományvezérlő közötti Active Directory biztonsági csatorna megszakad.

2. A virtuális gép rendelkezik egy régi másolatot készít a fiók jelszavát, és a tartományvezérlő egy újabb példánnyal rendelkezik.

3. A Tartományvezérlőnek, amely csatlakozik a virtuális gép állapota nem megfelelő.

### <a name="cause-2"></a>OK 2

A virtuális gép titkosítási szintje magasabb, mint az egy, az ügyfélszámítógép által használt.

### <a name="cause-3"></a>OK: 3

A TLS 1.0, 1.1-es vagy 1.2-es (kiszolgáló) protokollok le vannak tiltva, a virtuális gépen.

### <a name="cause-4"></a>OK: 4

A virtuális gép letiltása a tartományi hitelesítő adataival bejelentkező be lett állítva, és a helyi biztonsági szervezet (LSA) helytelenül van beállítva.

### <a name="cause-5"></a>OK: 5

A virtuális gép be lett állítva csak Federal Information Processing Standard (FIPS) fogadására-szabványnak megfelelő algoritmus kapcsolatok. Általában ez történik, az Active Directory-házirendek használatával. Ez egy ritka konfiguráció, de csak a távoli asztali kapcsolatok FIPS kényszeríthető.

## <a name="before-you-troubleshoot"></a>Mielőtt hibaelhárítás

### <a name="create-a-backup-snapshot"></a>Biztonsági mentési pillanatkép létrehozása

Hozzon létre egy biztonsági mentési pillanatképet, kövesse a [lemez pillanatképének elkészítése](../windows/snapshot-copy-managed-disk.md).

### <a name="connect-to-the-vm-remotely"></a>Távolról csatlakozhat a virtuális gép

Távolról csatlakozhat a virtuális Gépet, használja a módszer [táveszközök használata Azure virtuális gép hibáinak elhárítása](remote-tools-troubleshoot-azure-vm-issues.md).

### <a name="group-policy-client-service"></a>Csoportházirend-ügyfél szolgáltatás

Ha ez egy tartományhoz csatlakoztatott virtuális Gépet, először állítsa le a csoportházirend-ügyfél szolgáltatás megakadályozza, hogy minden olyan Active Directory-házirend módosítások felülírása. Ehhez futtassa a következő parancsot:

```cmd
REM Disable the member server to retrieve the latest GPO from the domain upon start
REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
```

Miután a problémát, állítsa vissza a virtuális gép azon képessége, hogy lépjen kapcsolatba a tartomány a legújabb csoportházirend-objektum lekérése a tartományhoz. Ehhez futtassa a következő parancsokat:

```cmd
sc config gpsvc start= auto
sc start gpsvc

gpupdate /force
```

Ha a módosítás visszavonásra kerül, az azt jelenti, hogy az Active Directory házirend okozza a problémát. 

### <a name="workaround"></a>Áthidaló megoldás

Ez a probléma a következő parancsokat a parancssori ablakba NLA letiltása:

```cmd
REM Disable the Network Level Authentication
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 0
```

Ezután indítsa újra a virtuális Gépet.

NLA újbóli engedélyezéséhez futtassa a következő parancsot, és indítsa újra a virtuális Gépet:

```cmd
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds /t REG_DWORD /d 0 /f

REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f
```

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="for-domain-joined-vms"></a>A tartományhoz csatlakoztatott virtuális gépek

A hiba elhárításához, először ellenőrizze, hogy a virtuális gép egy tartományvezérlő csatlakozhat, és e a tartományvezérlő "kifogástalan" állapotú, és képes kezelni a virtuális gép kér.

>[!Note] 
>A tartományvezérlő állapotának ellenőrzéséhez használhatja egy másik virtuális Géphez ugyanazon a virtuális hálózat és alhálózat, amelyek ugyanazon bejelentkezési kiszolgáló.

Csatlakozzon a virtuális Géphez, amely rendelkezik a probléma soros konzolon, távoli CMD vagy szerint a "Csatlakozás a virtuális gép távolról" című szakaszának lépéseit, távoli PowerShell használatával.

Annak megállapításához, a virtuális gép csatlakozik, hogy melyik tartományvezérlő, a konzolban futtassa a következő parancsot: 

```cmd
set | find /i "LOGONSERVER"
```

Ezt követően ellenőrizze a virtuális gép és a tartományvezérlő között a biztonságos csatorna állapotát. Ehhez futtassa a következő parancsot egy emelt szintű PowerShell-példányban. Ez a parancs visszaadja azt a logikai jelzőt, amely azt jelzi, hogy a biztonságos csatorna élve:

```powershell
Test-ComputerSecureChannel -verbose
```

Ha a csatorna megszakad, futtassa a következő parancsot, javításának:

```powershell
Test-ComputerSecureChannel -repair
```

Győződjön meg arról, hogy a számítógép fiók jelszavát az Active Directoryban a virtuális gép és a tartományvezérlő frissül:

```powershell
Reset-ComputerMachinePassword -Server "<COMPUTERNAME>" -Credential <DOMAIN CREDENTIAL WITH DOMAIN ADMIN LEVEL>
```

Ha a tartományvezérlő és a virtuális gép közötti kommunikáció helyes, de a tartományvezérlő nem elég kifogástalan-e az RDP-munkamenetet, próbálja meg a tartományvezérlő újraindítása.

Ha az előző parancsokban nem oldotta meg a kommunikációs probléma a tartományhoz, újra csatlakozhat a virtuális Gépet a tartományhoz. Ehhez kövesse az alábbi lépéseket:

1. Hozzon létre egy parancsfájlt a következő tartalmától Unjoin.ps1 nevű, és telepíteni a parancsfájlt, egy egyéni szkriptek bővítménye az Azure Portalon:

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```
    
    Ez a szkript a virtuális gép kényszerített veszi a tartományból, majd újraindítja 10 másodperc múlva. Ezt követően kell törölni a számítógép-objektumot a tartomány oldalon.

2.  Miután a karbantartás elkészült, csatlakoztassa újra a virtuális Gépet a tartományhoz. Ehhez hozzon létre egy parancsfájlt a következő tartalmától JoinDomain.ps1 nevű, és telepíteni a parancsfájlt, egy egyéni szkriptek bővítménye az Azure Portalon: 

    ```cmd
    cmd /c "netdom join <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10"
    ```

    >[!Note] 
    >Ez csatlakoztatja a virtuális Gépet a tartományhoz a megadott hitelesítő adataival.

Ha az Active Directory-csatorna állapota kifogástalan, a számítógép jelszava frissül, és a tartományvezérlő a várt módon működik, próbálja meg az alábbi lépéseket.

Ha a probléma tartósan fennáll, ellenőrizze az e tiltani a tartományi hitelesítő adatokat. Ehhez nyisson meg egy rendszergazda jogú parancssort, és futtassa a következő parancsot határozza meg, hogy a virtuális gép letiltása a tartományi fiókok esetében jelentkezzen be a virtuális gép beállítása:

```cmd
REG query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds
```

Ha a kulcs értéke **1**, ez azt jelenti, hogy lett-e beállítva a kiszolgáló nem engedélyezi a tartományi hitelesítő adatok akár. Módosítsa ezt a kulcsot **0**.

### <a name="for-standalone-vms"></a>Az önálló virtuális gépek

#### <a name="check-minencryptionlevel"></a>MinEncryptionLevel ellenőrzése

CMD-példányban, futtassa a következő parancsot a lekérdezés a **MinEncryptionLevel** beállításazonosító:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

A beállításazonosító alapján, kövesse az alábbi lépéseket:

* 4 (FIPS): Ugrás [ellenőrizze FIPS szabványnak megfelelő algoritmus kapcsolatok](#fips-compliant).

* 3 (128 bites titkosítás): a súlyosság beállítása **2** a következő parancs futtatásával:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2 /f
    ```

* 2 (titkosítási lehető legmagasabb, az ügyfél által előírtaknak megfelelően): állítsa be a titkosítási minimális értékét, próbálkozzon **1** a következő parancs futtatásával:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 1 /f
    ```
    
A virtuális gép újraindítása, hogy a beállításjegyzékben a módosítások érvénybe léptetéséhez.

#### <a name="tls-version"></a>TLS-verzió

Attól függően, a rendszer az RDP a TLS 1.0, 1.1-es vagy 1.2-es (kiszolgáló) protokollt használja. Lekérdezés, hogyan lehet ezeket a protokollokat a virtuális gép beállítani, nyisson meg egy CMD-példányt, és futtassa a következő parancsokat:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled
```

Ha a visszaadott értékek nem minden **1**, ez azt jelenti, hogy a a protokoll le van tiltva. Ahhoz, hogy ezeket a protokollokat, futtassa a következő parancsokat:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
```

Más protokollverziója futtassa a következő parancsokat:

<pre lang="bat">
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
</pre>

> [!Note]
> Az SSH/TLS-verzió listában le a vendég operációs rendszer naplóit, az SCHANNEL-hiba esetén.

#### <a name="fips-compliant"></a> FIPS szabványnak megfelelő algoritmus kapcsolatok ellenőrzése

A távoli asztal használata csak a FIPs előírásainak megfelelő algoritmust kapcsolatok kényszeríthető. Ez a beállításkulcs használatával állítható be. Ehhez nyisson meg egy rendszergazda jogú parancssort, és majd lekérdezheti a következő kulcsokat:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled
```

Ha a parancs visszaadja **1**, a beállításazonosítót módosítani **0**.

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled /t REG_DWORD /d 0
```

Ellenőrzés, amely az aktuális MinEncryptionLevel a virtuális gépen:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Ha a parancs visszaadja **4**, a beállításazonosítót módosítani **2**

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2
```

A virtuális gép újraindítása, hogy a beállításjegyzékben a módosítások érvénybe léptetéséhez.

## <a name="next-steps"></a>További lépések

[Az Win32_TSGeneralSetting osztály SetEncryptionLevel metódusa](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting-setencryptionlevel)

[Kiszolgáló-hitelesítési és titkosítási szintek konfigurálása](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770833(v=ws.11))

[Win32_TSGeneralSetting osztályban](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting)
