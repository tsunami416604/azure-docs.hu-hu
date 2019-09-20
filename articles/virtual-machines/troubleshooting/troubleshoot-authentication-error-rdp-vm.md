---
title: Hitelesítési hibák elhárítása, amikor RDP használatával csatlakozik az Azure-beli virtuális géphez | Microsoft Docs
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
ms.openlocfilehash: b7a561907e3f1968eb9adead3606822d7a1321c8
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155618"
---
# <a name="troubleshoot-authentication-errors-when-you-use-rdp-to-connect-to-azure-vm"></a>Hitelesítési hibák elhárítása, amikor RDP használatával csatlakozik az Azure-beli virtuális géphez

Ez a cikk segítséget nyújt az Azure-beli virtuális gépekhez (VM) való csatlakozáskor RDP protokoll (RDP) kapcsolat használatakor fellépő hitelesítési hibák elhárításához.

## <a name="symptoms"></a>Probléma

Egy olyan Azure-beli virtuális gép képernyőképét rögzíti, amely megjeleníti az üdvözlő képernyőt, és jelzi, hogy az operációs rendszer fut. Ha azonban a Távoli asztali kapcsolat használatával próbál csatlakozni a virtuális géphez, a következő hibaüzenetek egyike jelenik meg.

### <a name="error-message-1"></a>1\. hibaüzenet

**Hitelesítési hiba történt. Nem lehet felvenni a kapcsolatot a helyi biztonsági szolgáltatóval.**

### <a name="error-message-2"></a>2\. hibaüzenet

**Az a távoli számítógép, amelyhez csatlakozni próbál, hálózati szintű hitelesítés (NLA) szükséges, de a Windows-tartományvezérlő nem tud kapcsolódni a NLA végrehajtásához. Ha Ön rendszergazda a távoli számítógépen, a rendszer tulajdonságai párbeszédpanel távoli lapján található beállítások segítségével tilthatja le a NLA.**

### <a name="error-message-3-generic-connection-error"></a>3\. hibaüzenet (általános hiba)

**Ez a számítógép nem tud kapcsolódni a távoli számítógéphez. Próbálkozzon újra a csatlakozással, ha a probléma továbbra is fennáll, forduljon a távoli számítógép tulajdonosához vagy a hálózati rendszergazdához.**

## <a name="cause"></a>Ok

Több oka is van annak, hogy a NLA miért blokkolhatja a virtuális gép RDP-hozzáférését.

### <a name="cause-1"></a>OK: 1

A virtuális gép nem tud kommunikálni a tartományvezérlővel (DC). Ez a probléma megakadályozhatja, hogy egy RDP-munkamenet tartományi hitelesítő adatok használatával hozzáférjen a virtuális géphez. A helyi rendszergazdai hitelesítő adatok használatával azonban továbbra is bejelentkezhet. Ez a probléma a következő helyzetekben fordulhat elő:

1. A virtuális gép és a tartományvezérlő közötti Active Directory biztonsági csatorna megszakadt.

2. A virtuális gépen a fiók jelszavának egy régi példánya van, a tartományvezérlő pedig újabb másolattal rendelkezik.

3. A tartományvezérlő, amelyhez a virtuális gép csatlakozik, nem kifogástalan állapotú.

### <a name="cause-2"></a>OK 2

A virtuális gép titkosítási szintje nagyobb az ügyfélszámítógép által használt értéknél.

### <a name="cause-3"></a>3\. ok

A TLS 1,0, 1,1 vagy 1,2 (kiszolgáló) protokollok le vannak tiltva a virtuális gépen.

### <a name="cause-4"></a>4\. ok

A virtuális gép be lett állítva, hogy letiltsa a bejelentkezést a tartományi hitelesítő adatok használatával, és a helyi biztonsági szervezet (LSA) helytelenül van beállítva.

### <a name="cause-5"></a>5\. ok

A virtuális gép úgy lett beállítva, hogy csak a szövetségi adatfeldolgozási standard (FIPS) szabványnak megfelelő algoritmus-kapcsolatokat fogadja el. Ez általában Active Directory házirend használatával történik. Ez egy ritka konfiguráció, de a FIPS csak Távoli asztal kapcsolatok esetén kényszeríthető.

## <a name="before-you-troubleshoot"></a>A hibakeresés előtt

### <a name="create-a-backup-snapshot"></a>Biztonsági mentési pillanatkép létrehozása

Biztonsági mentési pillanatkép létrehozásához kövesse a [lemez pillanatképének](../windows/snapshot-copy-managed-disk.md)lépései című témakör lépéseit.

### <a name="connect-to-the-vm-remotely"></a>Távolról csatlakozhat a virtuális géphez

Ha távolról szeretne csatlakozni a virtuális géphez, használja az egyik módszert az [Azure-beli virtuális gépekkel kapcsolatos hibák elhárításához használható távoli eszközök használatával](remote-tools-troubleshoot-azure-vm-issues.md).

### <a name="group-policy-client-service"></a>Csoportházirend-ügyfél szolgáltatás

Ha ez egy tartományhoz csatlakoztatott virtuális gép, először állítsa le a Csoportházirend ügyfélszolgáltatás számára, hogy megakadályozza, hogy a Active Directory házirend felülírja a módosításokat. Ehhez futtassa a következő parancsot:

```cmd
REM Disable the member server to retrieve the latest GPO from the domain upon start
REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
```

A probléma javítása után állítsa vissza a virtuális gép azon képességét, hogy kapcsolatba lépjen a tartománnyal, és kérje le a legújabb csoportházirend-objektumot a tartományból. Ehhez futtassa a következő parancsokat:

```cmd
sc config gpsvc start= auto
sc start gpsvc

gpupdate /force
```

Ha a módosítás visszaállt, az azt jelenti, hogy egy Active Directory házirend okozza a problémát. 

### <a name="workaround"></a>Áthidaló megoldás

A probléma megkerüléséhez futtassa a következő parancsokat a parancsablakban a NLA letiltásához:

```cmd
REM Disable the Network Level Authentication
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 0
```

Ezután indítsa újra a virtuális gépet.

A NLA újbóli engedélyezéséhez futtassa a következő parancsot, majd indítsa újra a virtuális gépet:

```cmd
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds /t REG_DWORD /d 0 /f

REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f
```

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="for-domain-joined-vms"></a>Tartományhoz csatlakoztatott virtuális gépek esetén

A probléma elhárításához először győződjön meg arról, hogy a virtuális gép tud-e csatlakozni a TARTOMÁNYVEZÉRLŐhöz, és hogy a tartományvezérlő rendelkezik-e "kifogástalan" állapottal, és képes-e kezelni a virtuális gépről érkező kéréseket.

>[!Note] 
>A DC állapotának teszteléséhez egy másik virtuális gépet is használhat ugyanazon a VNET és alhálózaton, amelyek ugyanazt a bejelentkezési kiszolgálót használják.

Kapcsolódjon a Serial console, távoli CMD vagy távoli PowerShell használatával a problémával rendelkező virtuális géphez a "kapcsolódás a virtuális géphez távolról" szakasz lépéseinek megfelelően.

Annak megállapításához, hogy a virtuális gép melyik TARTOMÁNYVEZÉRLŐhöz csatlakozik, futtassa a következő parancsot a konzolon: 

```cmd
set | find /i "LOGONSERVER"
```

Ezután vizsgálja meg a biztonságos csatorna állapotát a virtuális gép és a tartományvezérlő között. Ehhez futtassa a következő parancsot egy emelt szintű PowerShell-példányban. Ez a parancs egy logikai jelzőt ad vissza, amely jelzi, hogy a biztonságos csatorna életben van-e:

```powershell
Test-ComputerSecureChannel -verbose
```

Ha a csatorna megszakad, futtassa a következő parancsot a kijavításához:

```powershell
Test-ComputerSecureChannel -repair
```

Győződjön meg arról, hogy a számítógépfiók jelszava Active Directory frissül a virtuális gépen és a TARTOMÁNYVEZÉRLŐn:

```powershell
Reset-ComputerMachinePassword -Server "<COMPUTERNAME>" -Credential <DOMAIN CREDENTIAL WITH DOMAIN ADMIN LEVEL>
```

Ha a tartományvezérlő és a virtuális gép közötti kommunikáció jó, de a tartományvezérlő nem elég kifogástalan ahhoz, hogy egy RDP-munkamenetet nyisson meg, próbálja meg újraindítani a TARTOMÁNYVEZÉRLŐt.

Ha az előző parancs nem javította a kommunikációs problémát a tartományon, akkor a virtuális gépet újra csatlakoztathatja a tartományhoz. Ehhez kövesse az alábbi lépéseket:

1. Hozzon létre egy unjoin. ps1 nevű parancsfájlt a következő tartalom használatával, majd telepítse a parancsfájlt egyéni parancsfájl-bővítményként a Azure Portal:

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```
    
    Ez a szkript a virtuális gépet a tartományon kívülről kényszeríti, majd 10 másodperccel később újraindítja. Ezután törölje a számítógép-objektumot a tartomány oldalon.

2.  A karbantartás befejezése után csatlakoztassa újra a virtuális gépet a tartományhoz. Ehhez hozzon létre egy JoinDomain. ps1 nevű szkriptet a következő tartalom használatával, majd telepítse a parancsfájlt egyéni parancsfájl-bővítményként a Azure Portal: 

    ```cmd
    cmd /c "netdom join <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10"
    ```

    >[!Note] 
    >Ez a megadott hitelesítő adatok használatával csatlakozik a virtuális géphez a tartományhoz.

Ha a Active Directory csatorna kifogástalan állapotban van, a számítógép jelszava frissül, és a tartományvezérlő a várt módon működik, próbálkozzon a következő lépésekkel.

Ha a probléma továbbra is fennáll, ellenőrizze, hogy le van-e tiltva a tartomány hitelesítő adatai. Ehhez nyisson meg egy rendszergazda jogú parancssort, majd futtassa a következő parancsot annak megállapításához, hogy a virtuális gép be van-e állítva a tartományi fiókok letiltására a virtuális gépre való bejelentkezéshez:

```cmd
REG query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds
```

Ha a kulcs értéke **1**, ez azt jelenti, hogy a kiszolgálót úgy állították be, hogy ne engedélyezze a tartományi hitelesítő adatokat. Módosítsa a kulcsot **0-ra**.

### <a name="for-standalone-vms"></a>Önálló virtuális gépek esetén

#### <a name="check-minencryptionlevel"></a>MinEncryptionLevel-ellenőrzési

Egy CMD-példányban futtassa a következő parancsot a **MinEncryptionLevel** beállításazonosító lekérdezéséhez:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

A beállításjegyzék értéke alapján kövesse az alábbi lépéseket:

* 4 (FIPS): Lépjen az [FIPs-kompatibilis algoritmusok kapcsolatainak vizsgálatához](#fips-compliant).

* 3 (128 bites titkosítás): Állítsa a súlyosságot a **2** értékre a következő parancs futtatásával:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2 /f
    ```

* 2 (az ügyfél által diktált legmagasabb titkosítási mód): A következő parancs futtatásával megpróbálhatja beállítani a titkosítást a minimális **1** értékre:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 1 /f
    ```
    
Indítsa újra a virtuális gépet, hogy a beállításjegyzék módosításai érvénybe lépnek.

#### <a name="tls-version"></a>TLS-verzió

A rendszertől függően az RDP a TLS 1,0, 1,1 vagy 1,2 (Server) protokollt használja. Ha le szeretné kérdezni, hogyan vannak beállítva ezek a protokollok a virtuális gépen, nyisson meg egy CMD-példányt, majd futtassa a következő parancsokat:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled
```

Ha a visszaadott értékek nem mind **1**, ez azt jelenti, hogy a protokoll le van tiltva. A protokollok engedélyezéséhez futtassa a következő parancsokat:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
```

Más protokoll-verziók esetében a következő parancsokat futtathatja:

<pre lang="bat">
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
</pre>

> [!Note]
> Szerezze be az SSH/TLS verzió x. x verzióját a vendég operációs rendszer naplóiból a SCHANNEL-hibákon.

#### <a name="fips-compliant"></a>FIPs-kompatibilis algoritmusok kapcsolatainak keresése

A távoli asztal kényszeríthető úgy, hogy csak a FIPs-kompatibilis algoritmus-kapcsolatokat használja. Ezt beállításkulcs használatával lehet beállítani. Ehhez nyisson meg egy rendszergazda jogú parancssort, és kérdezze le a következő kulcsokat:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled
```

Ha a parancs **1**értéket ad vissza, módosítsa a beállításazonosító értékét **0-ra**.

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled /t REG_DWORD /d 0
```

Győződjön meg arról, hogy a virtuális gép aktuális MinEncryptionLevel:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Ha a parancs **4**értéket ad vissza, módosítsa a beállításjegyzék értékét **2** értékre.

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2
```

Indítsa újra a virtuális gépet, hogy a beállításjegyzék módosításai érvénybe lépnek.

## <a name="next-steps"></a>További lépések

[A Win32_TSGeneralSetting osztály SetEncryptionLevel metódusa](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting-setencryptionlevel)

[Kiszolgáló hitelesítési és titkosítási szintjeinek konfigurálása](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770833(v=ws.11))

[Win32_TSGeneralSetting osztály](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting)
