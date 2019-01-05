---
title: Hibaelhárítás a konfigurációs kiszolgáló VMware virtuális gépek és fizikai kiszolgálók Azure-bA a vészhelyreállítás során az Azure Site Recovery használatával |} A Microsoft Docs
description: Ez a cikk elhárításával kapcsolatban biztosít információkat üzembe helyezéséhez használt konfigurációs kiszolgáló VMware virtuális gépek vészhelyreállítása és fizikai kiszolgálók Azure-bA az Azure Site Recovery használatával.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2018
ms.author: ramamill
ms.openlocfilehash: 597b8f59ef6991f7868d3de481e98ed9a459077b
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54050795"
---
# <a name="troubleshoot-configuration-server-issues"></a>Konfigurációs kiszolgáló hibáinak elhárítása

Ez a cikk segít a hibák elhárításához, ha telepíti és kezeli a [Azure Site Recovery](site-recovery-overview.md) konfigurációs kiszolgáló. A konfigurációs kiszolgáló felügyeleti kiszolgálóként működik. A konfigurációs kiszolgáló használatával állítsa be vészhelyreállítást helyszíni VMware virtuális gépek és fizikai kiszolgálók Azure-bA a Site Recovery használatával. A következő részekben bemutatjuk a leggyakrabban előforduló hibákat tapasztalhat, amikor hozzáad egy új konfigurációs kiszolgáló és a konfigurációs kiszolgáló kezelése során.

## <a name="registration-failures"></a>Regisztrációs hibák

A forrásgép a konfigurációs kiszolgálót regisztrálja a mobilitási ügynök telepítésekor. E lépés során hibákat is hibakeresési ezeket az irányelveket követve:

1. Nyissa meg a C:\ProgramData\ASR\home\svsystems\var\configurator_register_host_static_info.log fájlt. (A ProgramData mappa rejtett mappa lehet. Ha nem látja a ProgramData mappa, a Fájlkezelőben, a a **nézet** lap a **megjelenítése/elrejtése** szakaszban jelölje be a **rejtett elemek** jelölőnégyzet jelölését.) Hibák több problémák oka lehet.

2. Keresse meg benne **nem található érvényes IP-cím**. Ha a karakterlánc megtalálható:
    1. Ellenőrizze, hogy a kért állomás azonosítója megegyezik az állomás Azonosítóját a forrásgép.
    2. Ellenőrizze, hogy a forrásgép rendelkezik-e legalább egy IP-cím hozzárendelve a fizikai hálózati adaptert. Az ügynök regisztrációja a konfigurációs kiszolgálóval sikeres a forrásgép rendelkeznie kell legalább egy érvényes IP v4 cím hozzárendelve a fizikai hálózati adaptert.
    3. A forrásgép IP-címeket a forrásoldali virtuális gépen futtassa a következő parancsok egyikét:
      - A Windows: `> ipconfig /all`
      - Linux esetén: `# ifconfig -a`

3. Ha a karakterlánc **nem található érvényes IP-cím** nem található, a karakterlánc keresése **OK = > NULL**. Ez a hiba akkor fordul elő, ha a forrásgép használ egy üres gazdagép szeretne regisztrálni a konfigurációs kiszolgálót. Ha a karakterlánc megtalálható:
    - A problémák megoldása után hajtsa végre az irányelveket [a forrásgép a konfigurációs kiszolgálót regisztrálja](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server) manuálisan újra a regisztrációt.

4. Ha a karakterlánc **OK = > NULL** nem található, a forrásgépen, nyissa meg a C:\ProgramData\ASRSetupLogs\UploadedLogs\ASRUnifiedAgentInstaller.log fájlt. (A ProgramData mappa rejtett mappa lehet. Ha nem látja a ProgramData mappa, a Fájlkezelőben, a a **nézet** lap a **megjelenítése/elrejtése** szakaszban jelölje be a **rejtett elemek** jelölőnégyzet jelölését.) Hibák több problémák oka lehet. 

5. Keresse meg benne **kérelem küldése: (7) – nem sikerült csatlakozni a kiszolgálóhoz**. Ha a karakterlánc megtalálható:
    1. A forrásgép és a konfigurációs kiszolgáló között a hálózati problémák megoldásához. Ellenőrizze, hogy a konfigurációs kiszolgálót a forrásgépről hálózati eszközök, mint például a ping, a traceroute vagy egy webböngésző használatával. Győződjön meg arról, hogy a forrásgép a konfigurációs kiszolgáló 443-as porton keresztül érhető el.
    2. Ellenőrizze, hogy bármely-tűzfalszabályok a forrása machine blokk a kapcsolat a forrásgép és a konfigurációs kiszolgáló között. A hálózati rendszergazdák bármely kapcsolódási problémák feloldásának működnek.
    3. Ellenőrizze, hogy a felsorolt mappák [víruskereső program a Site Recovery mappakivételek](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) a víruskereső szoftver nem tartoznak.
    4. Ha hálózati hibák elhárulnak, ismételje meg a regisztráció az alábbi az irányelveket a [a forrásgép a konfigurációs kiszolgálót regisztrálja](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).

6. Ha a karakterlánc **kérelem küldése: (7) – nem sikerült csatlakozni a kiszolgálóhoz** nem található, az ugyanabban a naplófájlban, keresse meg a karakterlánc **kérelem: (60) – adott CA-tanúsítványok társ tanúsítvány nem hitelesíthető a**. Ez a hiba akkor fordulhat elő, mert a konfigurációs kiszolgáló tanúsítványának érvényessége lejárt, vagy a forrásgép nem támogatja a TLS 1.0-s vagy újabb SSL protokollokat. Azt is fordulhat elő, ha tűzfal blokkolja az SSL-kommunikáció a forrásgép és a konfigurációs kiszolgáló között. Ha a karakterlánc megtalálható: 
    1. Háríthatja el csatlakozhat a konfigurációs kiszolgáló IP-címét egy webböngészőben a forrásgépen. Az URI https használatára:\/\/< konfigurációs kiszolgáló IP-cím\>: 443 /. Győződjön meg arról, hogy a forrásgép a konfigurációs kiszolgáló 443-as porton keresztül érhető el.
    2. Ellenőrizze, hogy a forrásgépen minden olyan tűzfalszabályokat kell-e hozzáadva vagy eltávolítva a forrásgép a konfigurációs kiszolgálóval kommunikáljon. Miatt a különböző is használatban tűzfal szoftver hogy összes szükséges tűzfal-konfiguráció nem listázhatók. A hálózati rendszergazdák bármely kapcsolódási problémák feloldásának működnek.
    3. Ellenőrizze, hogy a felsorolt mappák [víruskereső program a Site Recovery mappakivételek](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) a víruskereső szoftver nem tartoznak.  
    4. A problémák megoldása után próbálja megismételni a regisztrációt a következő irányelvek alapján [a forrásgép a konfigurációs kiszolgálót regisztrálja](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).

7. Linuxon Ha a platform értékét < INSTALLATION_DIR\>/etc/drscout.conf sérült, regisztráció meghiúsul. A probléma azonosításához, nyissa meg a /var/log/ua_install.log fájlt. Keresse meg benne **konfigurációs megszakítása VM_PLATFORM értéke null, vagy azt, nem VmWare/Azure-beli**. A platform kell megadni **VmWare** vagy **Azure**. Ha a drscout.conf fájl sérült, azt javasoljuk, hogy Ön [távolítsa el a mobilitási](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service) , majd telepítse újra a mobilitási ügynök. Ha az eltávolítás nem sikerül, kövesse az alábbi lépéseket:
    1. Nyissa meg a Installation_Directory/uninstall.sh fájlt, és tegye megjegyzésbe a hívást a **StopServices** függvény.
    2. Nyissa meg a Installation_Directory/Vx/bin/uninstall.sh fájlt, és tegye megjegyzésbe a hívást a **stop_services** függvény.
    3. Nyissa meg a Installation_Directory/Fx/uninstall.sh fájlt, és tegye megjegyzésbe a teljes szakasz-e a Fx szolgáltatás leállításához.
    4. [Távolítsa el](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service) a mobilitási ügynök. A sikeres eltávolítása után indítsa újra a rendszert, és próbálja meg újra a mobilitási ügynök.

## <a name="installation-failure-failed-to-load-accounts"></a>Telepítési hiba: A fiókok betöltése nem sikerült

Ez a hiba akkor fordul elő, ha a szolgáltatás nem tudja olvasni az átviteli kapcsolatból származó adatokat, amikor a mobilitási ügynök telepítése, és regisztrálja a konfigurációs kiszolgálóval. A probléma megoldásához, győződjön meg arról, hogy a TLS 1.0 engedélyezve van-e a forrásgépen.

## <a name="change-the-ip-address-of-the-configuration-server"></a>A konfigurációs kiszolgáló IP-címének módosítása

Javasoljuk, hogy nem módosítja a konfigurációs kiszolgáló IP-címét. Győződjön meg arról, hogy a konfigurációs kiszolgálóhoz rendelt összes IP-cím statikus IP-címeket. Ne használja a DHCP IP-címeket.

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008: SAML-jogkivonat érvénytelen

Ez a hiba elkerülése érdekében győződjön meg arról, a rendszeróra ideje nem ez eltér a helyi ideje szerint több mint 15 perc. A regisztráció befejezéséhez futtassa ismét a telepítőt.

## <a name="failed-to-create-a-certificate"></a>Nem sikerült létrehozni a tanúsítványt

A Site Recovery hitelesítéséhez szükséges tanúsítvány nem hozható létre. Miután meggyőződött arról, hogy a telepítő futtatja egy helyi rendszergazdaként futtassa újra a telepítőt.

## <a name="register-the-source-machine-with-the-configuration-server"></a>A forrásgép regisztrálja a konfigurációs kiszolgáló

### <a name="if-the-source-machine-runs-windows"></a>Ha a forrásgép Windows fut

Futtassa a következő parancsot a forrásoldali virtuális gépen:

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <configuration server IP address> /PassphraseFilePath <passphrase file path>
  ```

Beállítás | Részletek
--- | ---
Használat | UnifiedAgentConfigurator.exe/csendpoint < konfigurációs kiszolgáló IP-cím \> /passphrasefilepath < jelszó fájl elérési útja\>
Az ügynök konfigurációs naplók | A(z) % ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log alatt található.
/CSEndPoint | A paraméter megadása kötelező. Itt adhatja meg a konfigurációs kiszolgáló IP-címét. Bármilyen érvényes IP-címet használja.
/PassphraseFilePath |  Kötelező. A hozzáférési kódot helye. Bármely érvényes UNC vagy helyi fájl elérési útját használja.

### <a name="if-the-source-machine-runs-linux"></a>Ha a forrásgép Linux fut

Futtassa a következő parancsot a forrásoldali virtuális gépen:

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <configuration server IP address> -P /var/passphrase.txt
  ```

Beállítás | Részletek
--- | ---
Használat | CD /usr/local/ASR/Vx/bin<br /><br /> UnifiedAgentConfigurator.sh -i < konfigurációs kiszolgáló IP-cím\> - P < jelszó fájl elérési útja\>
-i | A paraméter megadása kötelező. Itt adhatja meg a konfigurációs kiszolgáló IP-címét. Bármilyen érvényes IP-címet használja.
-P |  Kötelező. A fájl teljes elérési útját, amelyben a jelszót a rendszer menti a fájlt. Bármely érvényes mappát használja.

