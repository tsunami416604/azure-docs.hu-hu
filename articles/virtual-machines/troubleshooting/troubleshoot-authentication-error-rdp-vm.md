---
title: Hitelesítési hibák elhárítása, amikor RDP protokollt használ az Azure VM-hez való csatlakozáshoz | Microsoft dokumentumok
description: Ismerje meg, hogyan háríthatja el a hitelesítési hibákat, amelyek akkor fordulnak elő, amikor a Távoli asztali protokoll (RDP) használatával csatlakozik egy Azure virtuális géphez (VM).
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
ms.openlocfilehash: 03356c0b4a93f4befdbc529523e58642137a8887
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420812"
---
# <a name="troubleshoot-authentication-errors-when-you-use-rdp-to-connect-to-azure-vm"></a>Azure-beli virtuális géphez RDP használatával való csatlakozáskor jelentkező hitelesítési hibák elhárítása

Ez a cikk segítséget nyújt az Azure virtuális géphez (VM) való csatlakozáshoz való csatlakozáshoz használt RDP-kapcsolat használata során bekövetkező hitelesítési hibák elhárításában.

## <a name="symptoms"></a>Probléma

Egy Azure virtuális gép képernyőképének rögzítésével az üdvözlőképernyőt jeleníti meg, és jelzi, hogy az operációs rendszer fut. Ha azonban távoli asztali kapcsolathasználatával próbál csatlakozni a virtuális géphez, az alábbi hibaüzenetek egyike jelenik meg.

### <a name="error-message-1"></a>1. hibaüzenet

**Hitelesítési hiba történt. A helyi biztonsági hatósággal nem lehet kapcsolatba lépni.**

### <a name="error-message-2"></a>2. hibaüzenet

**A távoli számítógép, amelyhez csatlakozni próbál, hálózati szintű hitelesítést (NLA) igényel, de a Windows tartományvezérlővel nem lehet kapcsolatba lépni az NLA végrehajtásához. Ha ön a távoli számítógép rendszergazdája, a Rendszertulajdonságai párbeszédpanel Távoli lapján található beállításokkal letilthatja az NLA-t.**

### <a name="error-message-3-generic-connection-error"></a>3. hibaüzenet (általános csatlakozási hiba)

**Ez a számítógép nem tud csatlakozni a távoli számítógéphez. Ha a probléma továbbra is fennáll, próbáljon meg újra csatlakozni, forduljon a távoli számítógép tulajdonosához vagy a hálózati rendszergazdához.**

## <a name="cause"></a>Ok

Több oka is lehet, hogy az NLA blokkolja az RDP-hozzáférést a virtuális géphez.

### <a name="cause-1"></a>1. ok

A virtuális gép nem tud kommunikálni a tartományvezérlővel.The VM cannot communicatewith the domain controller (DC). Ez a probléma megakadályozhatja, hogy egy RDP-munkamenet tartományi hitelesítő adatok használatával hozzáférjen a virtuális géphez. A helyi rendszergazda hitelesítő adataival azonban továbbra is be tud jelentkezni. Ez a probléma a következő esetekben fordulhat elő:

1. A virtuális gép és a tartományvezérlő közötti Active Directory biztonsági csatorna megszakadt.

2. A virtuális gép rendelkezik a fiók jelszavának egy régi másolatával, és a tartományvezérlő egy újabb példányt.

3. A tartományvezérlő, amelyhez a virtuális gép csatlakozik, nem kifogástalan.

### <a name="cause-2"></a>2. ok

A virtuális gép titkosítási szintje magasabb, mint az ügyfélszámítógép által használt.

### <a name="cause-3"></a>3. ok

A TLS 1.0, 1.1 vagy 1.2 (kiszolgáló) protokollok le vannak tiltva a virtuális gép.

### <a name="cause-4"></a>4. ok

A virtuális gép úgy lett beállítva, hogy letiltsa a bejelentkezést a tartományi hitelesítő adatok használatával, és a helyi biztonsági szervezet (LSA) helytelenül van beállítva.

### <a name="cause-5"></a>5. ok

A virtuális gép csak a Federal Information Processing Standard (FIPS) szabványnak megfelelő algoritmuskapcsolatok fogadására lett beállítva. Ezt általában az Active Directory házirendje használja. Ez egy ritka konfiguráció, de a FIPS csak távoli asztali kapcsolatok esetén kényszeríthető.

## <a name="before-you-troubleshoot"></a>A hibaelhárítás előtt

### <a name="create-a-backup-snapshot"></a>Biztonsági pillanatkép létrehozása

Biztonsági másolat pillanatképének létrehozásához kövesse a [Lemez pillanatképe](../windows/snapshot-copy-managed-disk.md)című részben leírt lépéseket.

### <a name="connect-to-the-vm-remotely"></a>Csatlakozás a virtuális géphez távolról

A virtuális géphez való távoli csatlakozáshoz használja a Távoli eszközök használata az [Azure virtuális gépekkel kapcsolatos problémák elhárításához](remote-tools-troubleshoot-azure-vm-issues.md)című módszer egyikét.

### <a name="group-policy-client-service"></a>Csoportházirend-ügyfélszolgáltatás

Ha tartományhoz csatlakozott virtuális gépről van szükség, először állítsa le a Csoportházirend-ügyfél szolgáltatást, hogy megakadályozza az Active Directory-házirend ek felülírását a módosításokban. Ehhez futtassa az alábbi parancsot:

```cmd
REM Disable the member server to retrieve the latest GPO from the domain upon start
REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
```

A probléma megoldása után állítsa vissza a virtuális gép azon képességét, hogy kapcsolatba lépjen a tartománnyal a tartomány legújabb főházirend-jámánca lekéréséhez. Ehhez futtassa a következő parancsokat:

```cmd
sc config gpsvc start= auto
sc start gpsvc

gpupdate /force
```

Ha a módosítás visszaáll, az azt jelenti, hogy egy Active Directory-házirend okozza a problémát. 

### <a name="workaround"></a>Áthidaló megoldás

A probléma kerülő megoldásához futtassa a következő parancsokat a parancsablakban az NLA letiltásához:

```cmd
REM Disable the Network Level Authentication
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 0
reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 0
```

Ezután indítsa újra a virtuális gép.

Az NLA újbóli engedélyezéséhez futtassa a következő parancsot, majd indítsa újra a virtuális gép:

```cmd
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds /t REG_DWORD /d 0 /f

REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f
REG add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f
```

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="for-domain-joined-vms"></a>Tartományhoz csatlakozó virtuális gépek esetén

A probléma elhárításához először ellenőrizze, hogy a virtuális gép tud-e csatlakozni egy tartományvezérlőhöz, és hogy a tartományvezérlő állapota "kifogástalan", és képes-e kezelni a virtuális géptől érkező kéréseket.

>[!Note] 
>A tartományvezérlő állapotának teszteléséhez használhat egy másik virtuális gép ugyanazon a virtuális hálózaton és alhálózaton, amelyek ugyanazt a bejelentkezési kiszolgálót használják.

Csatlakozzon a virtuális gép, amely a problémát a soros konzol, a távoli CMD vagy a távoli PowerShell használatával, a "Csatlakozás a virtuális géphez távolról" szakasz lépései szerint.

Annak megállapításához, hogy a virtuális gép melyik tartományvezérlőhöz csatlakozik, futtassa a következő parancsot a konzolon: 

```cmd
set | find /i "LOGONSERVER"
```

Ezután ellenőrizze a biztonságos csatorna állapotát a virtuális gép és a tartományvezérlő között. Ehhez futtassa a következő parancsot egy emelt szintű PowerShell-példányban. Ez a parancs logikai jelzőt ad vissza, amely azt jelzi, hogy a biztonságos csatorna él-e:

```powershell
Test-ComputerSecureChannel -verbose
```

Ha a csatorna megszakadt, futtassa a következő parancsot a javításhoz:

```powershell
Test-ComputerSecureChannel -repair
```

Győződjön meg arról, hogy a számítógépfiók jelszava az Active Directoryban frissült a virtuális számítógépen és a tartományvezérlőn:

```powershell
Reset-ComputerMachinePassword -Server "<COMPUTERNAME>" -Credential <DOMAIN CREDENTIAL WITH DOMAIN ADMIN LEVEL>
```

Ha a tartományvezérlő és a virtuális gép közötti kommunikáció jó, de a tartományvezérlő nem elég kifogástalan az RDP-munkamenet megnyitásához, megpróbálhatja újraindítani a tartományvezérlőt.

Ha az előző parancsok nem oldották meg a tartománykommunikációs problémát, újra csatlakozhat ehhez a virtuális géphez a tartományhoz. Ehhez kövesse az alábbi lépéseket:

1. Hozzon létre egy Unjoin.ps1 nevű parancsfájlt a következő tartalom használatával, majd telepítse a parancsfájlt egyéni parancsfájl-bővítményként az Azure Portalon:

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```
    
    Ez a parancsfájl kiveszi a virtuális gép a tartományból erőszakkal, és újraindítja azt 10 másodperccel később. Ezután meg kell tisztítania a Számítógép objektumot a tartomány oldalán.

2.  A karbantartás befejezése után csatlakozzon újra a virtuális géphez. Ehhez hozzon létre egy JoinDomain.ps1 nevű parancsfájlt a következő tartalom használatával, majd telepítse a parancsfájlt egyéni parancsfájl-bővítményként az Azure Portalon: 

    ```cmd
    cmd /c "netdom join <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10"
    ```

    >[!Note] 
    >Ez a megadott hitelesítő adatok használatával csatlakozik a virtuális gép a tartományban.

Ha az Active Directory-csatorna kifogástalan állapotú, a számítógép jelszava frissül, és a tartományvezérlő a várt módon működik, próbálkozzon az alábbi lépésekkel.

Ha a probléma továbbra is fennáll, ellenőrizze, hogy a tartományi hitelesítő adatok le vannak-e tiltva. Ehhez nyisson meg egy rendszergazda jogú parancssori ablakot, majd futtassa a következő parancsot annak megállapítására, hogy a virtuális gép be van-e állítva a virtuális gépbe való bejelentkezéshez szükséges tartományi fiókok letiltására:

```cmd
REG query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa" /v disabledomaincreds
```

Ha a kulcs **1-re**van állítva, az azt jelenti, hogy a kiszolgáló úgy lett beállítva, hogy ne engedélyezze a tartományi hitelesítő adatokat. Módosítsa ezt a kulcsot **0-ra.**

### <a name="for-standalone-vms"></a>Önálló virtuális gépekhez

#### <a name="check-minencryptionlevel"></a>MinEncryptionLevel ellenőrzése

CmD-példányban futtassa a következő parancsot a **MinEncryptionLevel** rendszerleíró érték lekérdezéséhez:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

A rendszerleíró érték alapján hajtsa végre az alábbi lépéseket:

* 4 (FIPS): Nyissa meg a [FIPs-kompatibilis algoritmusok kapcsolatainak ellenőrzése című menüt.](#fips-compliant)

* 3 (128 bites titkosítás): Állítsa a súlyosságát **2-re** a következő parancs futtatásával:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2 /f
    ```

* 2 (A lehető legmagasabb szintű titkosítás, az ügyfél által diktált módon): Megpróbálhatja a titkosítást az **1-es** minimális értékre állítani a következő parancs futtatásával:

    ```cmd
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 1 /f
    ```
    
Indítsa újra a virtuális gép, hogy a módosítások a rendszerleíró adatbázis ban lépnek életbe.

#### <a name="tls-version"></a>TLS verzió

A rendszertől függően az RDP a TLS 1.0, 1.1 vagy 1.2 (kiszolgálói) protokollt használja. Ha le szeretné kérdezni, hogy ezek a protokollok hogyan vannak beállítva a virtuális számítógépen, nyisson meg egy CMD-példányt, majd futtassa a következő parancsokat:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled
```

Ha a visszaadott értékek nem **mind az 1,** az azt jelenti, hogy a protokoll le van tiltva. A protokollok engedélyezéséhez futtassa a következő parancsokat:

```cmd
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f
reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
```

Más protokollverziók esetén a következő parancsokat futtathatja:

<pre lang="bat">
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
reg query "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS <i>x.x</i>\Server" /v Enabled
</pre>

> [!Note]
> Az SSH/TLS x.x verziójának beszereznie a vendég operációsrendszer-naplókból az SCHANNEL-hibákról.

#### <a name="check-fips-compliant-algorithms-connections"></a><a name="fips-compliant"></a>FiP-kompatibilis algoritmusok kapcsolatainak ellenőrzése

A távoli asztal csak fip-kompatibilis algoritmuskapcsolatok használatára kényszeríthető. Ezt rendszerleíró kulccsal lehet beállítani. Ehhez nyisson meg egy rendszergazda jogú parancssori ablakot, majd kérdezze le a következő kulcsokat:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled
```

Ha a parancs **1**értéket ad vissza, módosítsa a rendszerleíró értéket **0-ra.**

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy" /v Enabled /t REG_DWORD /d 0
```

Ellenőrizze, hogy melyik az aktuális MinEncryptionLevel a virtuális gépen:

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel
```

Ha a parancs **4**értéket ad vissza, módosítsa a rendszerleíró értéket **2-re.**

```cmd
reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" /v MinEncryptionLevel /t REG_DWORD /d 2
```

Indítsa újra a virtuális gép, hogy a módosítások a rendszerleíró adatbázis ban lépnek életbe.

## <a name="next-steps"></a>További lépések

[A Win32_TSGeneralSetting osztály SetEncryptionLevel metódusa](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting-setencryptionlevel)

[Kiszolgálóhitelesítési és titkosítási szintek konfigurálása](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770833(v=ws.11))

[Win32_TSGeneralSetting osztály](https://docs.microsoft.com/windows/desktop/TermServ/win32-tsgeneralsetting)
