---
title: VMware virtuális gépek és fizikai kiszolgálók az Azure-bA az Azure Site Recovery a vészhelyreállítás során a konfigurációs kiszolgáló hibáinak elhárítása |} A Microsoft Docs
description: Ez a cikk a konfigurációs kiszolgáló VMware virtuális gépek vészhelyreállítása és a fizikai kiszolgálók üzembe helyezése az Azure-bA az Azure Site Recovery hibaelhárítási információkat szolgáltat.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2018
ms.author: ramamill
ms.openlocfilehash: f5c8241907459a06f0a6206ae6865cdf3fe9ab89
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2019
ms.locfileid: "53998965"
---
# <a name="troubleshoot-configuration-server-issues"></a>Konfigurációs kiszolgáló hibáinak elhárítása

Ez a cikk segít a problémák, amikor üzembe helyezése és kezelése a [Azure Site Recovery](site-recovery-overview.md) konfigurációs kiszolgáló. A konfigurációs kiszolgáló felügyeleti kiszolgálóként működik, és a vészhelyreállítás beállítása helyszíni VMware virtuális gépek és fizikai kiszolgálók Azure-bA a Site Recovery használatával szolgál. A leggyakoribb hibák látható új konfigurációs kiszolgálót és a konfigurációs kiszolgáló kezelése a következő szakaszok ismerteti.

## <a name="registration-failures"></a>Regisztrációs hibák

Forrásgép konfigurációs kiszolgálót regisztrálja a mobilitási ügynök telepítése közben. Ez a lépés során hibákat hibakeresése végezhető el az alábbi útmutatást követve:

1. Ugrás a C:\ProgramData\ASR\home\svsystems\var\configurator_register_host_static_info.log fájlt. ProgramData mappa rejtett is lehet. Ha nem tudják találni, próbálja meg fedje a mappát. A hibák több problémák is okozhatják.
2. Keressen a "Nem érvényes IP-cím található" karakterláncot. Ha megtalálja,
    - Ha a kért állomásazonosító ugyanaz, mint a forrásgép ellenőrzése.
    - A forrásgép kell legalább egy IP-cím hozzárendelve a fizikai hálózati adapternek az ügynök regisztrálása a CS-sikeres.
    - Futtassa a parancsot a forrásgépen `> ipconfig /all` (a Windows operációs rendszer) és `# ifconfig -a` (a Linux operációs rendszert futtató) a forrásgép összes IP-címek lekérése.
    - Vegye figyelembe, hogy az ügynök regisztrálása igényel-e egy érvényes IP v4 cím hozzárendelve a fizikai hálózati adaptert.
3. Ha a fenti karakterláncot nem található, a keresési karakterlánc "OK" = > "NULL". Ha talál,
    - Ez a hiba akkor fordul elő, ha a forrásgép használ egy üres gazdagép Ha regisztrálni a konfigurációs kiszolgáló.
    - A hibák megoldása után hajtsa végre a megadott irányelveket [Itt](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server) manuálisan újra a regisztrációt.
4. Ha a fenti karakterláncot nem található, Ugrás a forrásgép és a naplóban C:\ProgramData\ASRSetupLogs\UploadedLogs\* ASRUnifiedAgentInstaller.log ProgramData mappa rejtett is lehet. Ha nem tudják találni, próbálja meg fedje a mappát. A hibák több problémák is okozhatják. Keresési karakterlánc "kérelem közzététele: (7) – nem sikerült csatlakozni a kiszolgálóhoz". Ha talál,
    - A forrás gép és a konfigurációs kiszolgáló között a hálózati problémák megoldásához. Győződjön meg arról, hogy a konfigurációs kiszolgáló forrásgépről használata a hálózati eszközök, mint például a ping, a traceroute, a webböngésző stb., győződjön meg arról, hogy a forrásgép képes elérni a konfigurációs kiszolgáló 443-as porton keresztül.
    - Annak ellenőrzése, hogy minden olyan tűzfalszabályokat forrásgépen forgalomszűrők blokkolják a forrása machine és a konfigurációs kiszolgáló közötti kapcsolat. Együttműködik a a hálózat rendszergazdájával, feloldása a kapcsolódási problémák.
    - Győződjön meg, hogy az említett mappák [Itt](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) a víruskereső szoftver nem tartoznak.
    - A hálózati hibák megoldása után próbálja megismételni a regisztrációt által megadott, a következő irányelveket [Itt](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).
5. Ha nem található, a karakterlánc azonos log keresés "kérelem: (60) – adott CA-tanúsítványok társ tanúsítvány nem hitelesíthető a. ". Ha talál, 
    - Ez a hiba oka az lehet, a konfigurációs kiszolgáló tanúsítványa lejárt vagy a forrásgép nem támogatja a TLS 1.0-s és újabb SSL protokollok vagy, amelyek forrása machine és a konfigurációs kiszolgáló közötti SSL-kommunikáció nem blokkolja tűzfal.
    - Konfigurációs kiszolgáló IP-cím URI https:// segítségével. a forrásgépen webböngészővel csatlakozzon háríthatja el<CSIPADDRESS>: 443 /. Győződjön meg arról, hogy a forrásgép képes elérni a konfigurációs kiszolgáló 443-as porton keresztül.
    - Annak ellenőrzése, hogy minden olyan tűzfalszabályokat forrásgépen forgalomszűrők blokkolják a forrása machine és a konfigurációs kiszolgáló közötti kapcsolat. Együttműködik a a hálózat rendszergazdájával, feloldása a kapcsolódási problémák.
    - Győződjön meg, hogy az említett mappák [Itt](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) a víruskereső szoftver nem tartoznak.  
    - A hibák megoldása után próbálja megismételni a regisztrációt által megadott, a következő irányelveket [Itt](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).
6. A Linux Ha < INSTALLATION_DIR > /etc/drscout.conf platformja értékét sérült, majd regisztráció sikertelen lesz. Azonosítsa, nyissa meg a napló /var/log/ua_install.log. Megtalálja a karakterláncot "Konfigurációs megszakítása VM_PLATFORM értéke null, vagy azt, nem VmWare/Azure-beli." A platform "VmWare" vagy "Azure" kell beállítani. Mivel a drscout.conf fájl sérült, javasoljuk, hogy [eltávolítása](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service) a mobilitási ügynök, és telepítse újra. Ha kiszolgálón.%0 nem sikerül, kövesse az alábbi lépéseket:
    - Nyissa meg a fájl Installation_Directory/uninstall.sh és tegye megjegyzésbe a függvény hívása *StopServices*
    - Nyissa meg Installation_Directory/Vx/bin/uninstall fájlt, és tegye megjegyzésbe a függvény hívása `stop_services`
    - Nyissa meg a Installation_Directory/Fx/uninstall fájlt, és tegye megjegyzésbe a teljes szakasz-e a Fx szolgáltatás leállítása.
    - Most megpróbálja [eltávolítása](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service) a mobilitási ügynök. A sikeres kiszolgálón.%0 után indítsa újra a rendszert, és próbálja meg ismét telepíteni az ügynököt.

## <a name="installation-failure---failed-to-load-accounts"></a>Telepítési hiba - fiókok betöltése nem sikerült

Ez a hiba akkor fordul elő, amikor a szolgáltatás nem tudott adatokat olvasni az átviteli kapcsolatból mobilitási ügynök telepítése és a konfigurációs kiszolgáló regisztrálása közben. Megoldásához, győződjön meg arról, a TLS 1.0 a beállítás engedélyezve a forrásgépen.

## <a name="change-ip-address-of-configuration-server"></a>Konfigurációs kiszolgáló IP-címének módosítása

Javasoljuk, hogy ne módosítsa a konfigurációs kiszolgáló IP-címét. Győződjön meg, hogy minden IP-címet, és a konfigurációs kiszolgáló hozzárendelt statikus IP-címek és a nem DHCP IP-címek.

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008: SAML-jogkivonat érvénytelen

Ez a hiba elkerülése érdekében győződjön meg arról, hogy a rendszeróra ideje nem több mint 15 perccel tér a helyi időt. A regisztráció befejezéséhez futtassa ismét a telepítőt.

## <a name="failed-to-create-certificate"></a>Nem sikerült létrehozni a tanúsítványt

A Site Recovery hitelesítéséhez szükséges tanúsítvány nem hozható létre. Miután meggyőződött arról, hogy futtatja a telepítőt helyi rendszergazdaként futtassa újra a telepítőt.

## <a name="register-source-machine-with-configuration-server"></a>Forrásgép regisztrálni a konfigurációs kiszolgáló

### <a name="if-source-machine-has-windows-os"></a>Ha Windows operációs rendszer forrásgépen

Futtassa a következő parancsot a forrásgépen

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```
**Beállítás** | **Részletek**
--- | ---
Használat | UnifiedAgentConfigurator.exe/csendpoint  <CSIP> /passphrasefilepath <PassphraseFilePath>
Az ügynök konfigurációs naplók | A % ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | A paraméter megadása kötelező. Itt adhatja meg a konfigurációs kiszolgáló IP-címét. Bármilyen érvényes IP-címet használja.
/PassphraseFilePath |  Kötelező. A hozzáférési kódot helye. Bármely érvényes UNC vagy helyi fájl elérési útját használja.

### <a name="if-source-machine-has-linux-os"></a>Ha a forrásgépen. Linux operációs rendszer

Futtassa a következő parancsot a forrásgépen

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
  ```
**Beállítás** | **Részletek**
--- | ---
Használat | CD /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i <CSIP> - P <PassphraseFilePath>
-i | A paraméter megadása kötelező. Itt adhatja meg a konfigurációs kiszolgáló IP-címét. Bármilyen érvényes IP-címet használja.
-P |  Kötelező. Teljes elérési útja, amelyben a jelszót a rendszer menti a fájlt. Bármely érvényes mappát használni