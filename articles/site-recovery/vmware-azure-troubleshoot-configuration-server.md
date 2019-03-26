---
title: Hibaelhárítás a konfigurációs kiszolgáló VMware virtuális gépek és fizikai kiszolgálók Azure-bA a vészhelyreállítás során az Azure Site Recovery használatával |} A Microsoft Docs
description: Ez a cikk elhárításával kapcsolatban biztosít információkat üzembe helyezéséhez használt konfigurációs kiszolgáló VMware virtuális gépek vészhelyreállítása és fizikai kiszolgálók Azure-bA az Azure Site Recovery használatával.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/13/2019
ms.author: ramamill
ms.openlocfilehash: 287a4104104c12e33fa2c50c398f422f9e6ea8c5
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58418703"
---
# <a name="troubleshoot-configuration-server-issues"></a>Konfigurációs kiszolgáló hibáinak elhárítása

Ez a cikk segít a hibák elhárításához, ha telepíti és kezeli a [Azure Site Recovery](site-recovery-overview.md) konfigurációs kiszolgáló. A konfigurációs kiszolgáló felügyeleti kiszolgálóként működik. A konfigurációs kiszolgáló használatával állítsa be vészhelyreállítást helyszíni VMware virtuális gépek és fizikai kiszolgálók Azure-bA a Site Recovery használatával. A következő részekben bemutatjuk a leggyakrabban előforduló hibákat tapasztalhat, amikor hozzáad egy új konfigurációs kiszolgáló és a konfigurációs kiszolgáló kezelése során.

## <a name="registration-failures"></a>Regisztrációs hibák

A forrásgép a konfigurációs kiszolgálót regisztrálja a mobilitási ügynök telepítésekor. E lépés során hibákat is hibakeresési ezeket az irányelveket követve:

1. Open the C:\ProgramData\ASR\home\svsystems\var\configurator_register_host_static_info.log file. (A ProgramData mappa rejtett mappa lehet. Ha nem látja a ProgramData mappa, a Fájlkezelőben, a a **nézet** lap a **megjelenítése/elrejtése** szakaszban jelölje be a **rejtett elemek** jelölőnégyzet jelölését.) Hibák több problémák oka lehet.

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

7. Linuxon Ha a platform értékét < INSTALLATION_DIR\>/etc/drscout.conf sérült, regisztráció meghiúsul. A probléma azonosításához, nyissa meg a /var/log/ua_install.log fájlt. Keresse meg benne **konfigurációs megszakítása VM_PLATFORM értéke null, vagy azt, nem VmWare/Azure-beli**. A platform kell megadni **VmWare** vagy **Azure**. Ha a drscout.conf fájl sérült, azt javasoljuk, hogy Ön [távolítsa el a mobilitási](vmware-physical-manage-mobility-service.md#uninstall-mobility-service) , majd telepítse újra a mobilitási ügynök. Ha az eltávolítás nem sikerül, kövesse az alábbi lépéseket: egy. Nyissa meg a Installation_Directory/uninstall.sh fájlt, és tegye megjegyzésbe a hívást a **StopServices** függvény.
    b. Nyissa meg a Installation_Directory/Vx/bin/uninstall.sh fájlt, és tegye megjegyzésbe a hívást a **stop_services** függvény.
    c. Nyissa meg a Installation_Directory/Fx/uninstall.sh fájlt, és tegye megjegyzésbe a teljes szakasz-e a Fx szolgáltatás leállításához.
    d. [Távolítsa el](vmware-physical-manage-mobility-service.md#uninstall-mobility-service) a mobilitási ügynök. A sikeres eltávolítása után indítsa újra a rendszert, és próbálja meg újra a mobilitási ügynök.

## <a name="installation-failure-failed-to-load-accounts"></a>Telepítési hiba: A fiókok betöltése nem sikerült

Ez a hiba akkor fordul elő, ha a szolgáltatás nem tudja olvasni az átviteli kapcsolatból származó adatokat, amikor a mobilitási ügynök telepítése, és regisztrálja a konfigurációs kiszolgálóval. A probléma megoldásához, győződjön meg arról, hogy a TLS 1.0 engedélyezve van-e a forrásgépen.

## <a name="vcenter-discovery-failures"></a>vCenter-felderítési hibák

VCenter-felderítési hibák elhárításához a vCenter-kiszolgáló hozzáadása a megkerülési lista proxybeállításokat. 

- Töltse le a PsExec eszköz [Itt](https://aka.ms/PsExec) rendszer felhasználói tartalomhoz való hozzáférést.
- A rendszer felhasználói tartalom Internet Explorer megnyitásához futtassa a következő parancssori psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe"
- Proxybeállítások hozzáadása az Internet Explorer és tmanssvc szolgáltatást.
- A DRA Proxybeállítások konfigurálása, futtassa a C:\Program Files\Microsoft Azure Site Recovery Provider cd
- Ezután hajtsa végre a DRCONFIGURATOR. EXE / /AddBypassUrls konfigurálása [adja hozzá az IP-címet/FQDN a vCenter Server során megadott **vCenter-kiszolgáló vagy vSphere ESXi kiszolgáló konfigurálása** lépésében [konfigurációs kiszolgáló telepítési](vmware-azure-deploy-configuration-server.md#configure-settings)]

## <a name="change-the-ip-address-of-the-configuration-server"></a>A konfigurációs kiszolgáló IP-címének módosítása

Javasoljuk, hogy nem módosítja a konfigurációs kiszolgáló IP-címét. Győződjön meg arról, hogy a konfigurációs kiszolgálóhoz rendelt összes IP-cím statikus IP-címeket. Ne használja a DHCP IP-címeket.

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008: SAML-jogkivonat érvénytelen

Ez a hiba elkerülése érdekében győződjön meg arról, a rendszeróra ideje nem ez eltér a helyi ideje szerint több mint 15 perc. A regisztráció befejezéséhez futtassa ismét a telepítőt.

## <a name="failed-to-create-a-certificate"></a>Nem sikerült létrehozni a tanúsítványt

A Site Recovery hitelesítéséhez szükséges tanúsítvány nem hozható létre. Miután meggyőződött arról, hogy a telepítő futtatja egy helyi rendszergazdaként futtassa újra a telepítőt.

## <a name="failure-to-activate-windows-license-from-server-standard-evaluation-to-server-standard"></a>Server Standard, Windows Server Standard értékelése licence aktiválásának

1. OVF-keresztül a konfigurációs kiszolgáló telepítésének részeként egy próbalicencre van használatban, amely érvényes 180 napig. Kell aktiválnia a jelen licenc, mielőtt ez lejár. Más esetben ez eredményez a konfigurációs kiszolgáló gyakori leállítása és replikációs tevékenységek így gátolhatja.
2. Ha nem tudja aktiválni a Windows-licenccel, kapcsolatba [Windows támogatási csoportjának](https://aka.ms/Windows_Support) a probléma megoldásához.

## <a name="register-source-machine-with-configuration-server"></a>Forrásgép regisztrálni a konfigurációs kiszolgáló

### <a name="if-the-source-machine-runs-windows"></a>Ha a forrásgép Windows fut

Futtassa a következő parancsot a forrásoldali virtuális gépen:

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <configuration server IP address> /PassphraseFilePath <passphrase file path>
```

Beállítás | Részletek
--- | ---
Használat | UnifiedAgentConfigurator.exe/csendpoint < konfigurációs kiszolgáló IP-cím \> /passphrasefilepath < jelszó fájl elérési útja\>
Az ügynök konfigurációs naplók | Located under %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | A paraméter megadása kötelező. Itt adhatja meg a konfigurációs kiszolgáló IP-címét. Bármilyen érvényes IP-címet használja.
/PassphraseFilePath |  Kötelező. A hozzáférési kódot helye. Bármely érvényes UNC vagy helyi fájl elérési útját használja.

### <a name="if-the-source-machine-runs-linux"></a>Ha a forrásgép Linux fut

Futtassa a következő parancsot a forrásoldali virtuális gépen:

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <configuration server IP address> -P /var/passphrase.txt
  ```

Beállítás | Részletek
--- | ---
Használat | cd /usr/local/ASR/Vx/bin<br /><br /> UnifiedAgentConfigurator.sh -i <configuration server IP address\> -P <passphrase file path\>
-i | A paraméter megadása kötelező. Itt adhatja meg a konfigurációs kiszolgáló IP-címét. Bármilyen érvényes IP-címet használja.
-P |  Kötelező. A fájl teljes elérési útját, amelyben a jelszót a rendszer menti a fájlt. Bármely érvényes mappát használja.

## <a name="unable-to-configure-the-configuration-server"></a>Nem sikerült beállítani a konfigurációs kiszolgáló

Ha alkalmazások, kívül a konfigurációs kiszolgáló telepítése a virtuális gépen, valószínűleg a fő célkiszolgáló nem konfigurálható. 

A konfigurációs kiszolgálón kell lennie egy egycélú kiszolgáló és a egy megosztott kiszolgálói nem támogatott, használja azt. 

További információkért lásd: a konfiguráció – gyakori kérdések a [konfigurációs kiszolgáló üzembe helyezése](vmware-azure-deploy-configuration-server.md#faq). 

## <a name="remove-the-stale-entries-for-protected-items-from-the-configuration-server-database"></a>A konfigurációs kiszolgáló adatbázisból az elavult bejegyzések védett elemek eltávolítása 

A konfigurációs kiszolgálón elavult védett gép eltávolításához használja az alábbi lépéseket. 
 
1. A forrásgép és a elavult bejegyzés IP-címének meghatározásához: 

    1. Nyissa meg a MYSQL-Parancssor rendszergazdai módban. 
    2. Hajtsa végre a következő parancsokat. 
   
        ```
        mysql> use svsdb1;
        mysql> select id as hostid, name, ipaddress, ostype as operatingsystem, from_unixtime(lasthostupdatetime) as heartbeat from hosts where name!='InMageProfiler'\G;
        ```

        Ez az IP-címek és a legutóbbi szívverés regisztrált gépekhez listáját adja vissza. Keresse meg a gazdagépet, amely rendelkezik az elavult replikációs párok.

2. Nyisson meg egy rendszergazda jogú parancssort, és navigáljon a C:\ProgramData\ASR\home\svsystems\bin. 
4. A konfigurációs kiszolgálóról a regisztrált gazdagép adatait és az elavult bejegyzés eltávolításához futtassa a következő parancs használatával a forrásgép és a elavult bejegyzés IP-címét. 
   
    `Syntax: Unregister-ASRComponent.pl -IPAddress <IP_ADDRESS_OF_MACHINE_TO_UNREGISTER> -Component <Source/ PS / MT>`
 
    Ha a forrás kiszolgáló bejegyzés "Rendszert-VM01", és a egy ip-címe 10.0.0.4 majd a következő parancsot használja.
 
    `perl Unregister-ASRComponent.pl -IPAddress 10.0.0.4 -Component Source`
 
5. Indítsa újra a következő szolgáltatásokat a forrásgép a konfigurációs kiszolgálóval regisztrációjához. 
 
    - Az InMage Scout alkalmazásszolgáltatás
    - Az InMage Scout VX Agent – Sentinel/Outpost

## <a name="upgrade-fails-when-the-services-fail-to-stop"></a>Frissítés sikertelen lesz, ha a szolgáltatás leállítása

A konfigurációs kiszolgáló frissítés sikertelen lesz, amikor bizonyos szolgáltatások nem állítja le. 

A probléma azonosításához, lépjen a konfigurációs kiszolgálón C:\ProgramData\ASRSetupLogs\CX_TP_InstallLogFile. Ha azt tapasztalja, a következő hibák, használja az alábbi lépéseket a probléma megoldásához: 

    2018-06-28 14:28:12.943   Successfully copied php.ini to C:\Temp from C:\thirdparty\php5nts
    2018-06-28 14:28:12.943   svagents service status - SERVICE_RUNNING
    2018-06-28 14:28:12.944   Stopping svagents service.
    2018-06-28 14:31:32.949   Unable to stop svagents service.
    2018-06-28 14:31:32.949   Stopping svagents service.
    2018-06-28 14:34:52.960   Unable to stop svagents service.
    2018-06-28 14:34:52.960   Stopping svagents service.
    2018-06-28 14:38:12.971   Unable to stop svagents service.
    2018-06-28 14:38:12.971   Rolling back the install changes.
    2018-06-28 14:38:12.971   Upgrade has failed.

A probléma megoldásához:

Manuálisan állítsa le a következő szolgáltatásokat:

- cxprocessserver
- Az InMage Scout VX Agent – Sentinel/Outpost és 
- A Microsoft Azure Recovery Services Agent 
- A Microsoft Azure Site Recovery szolgáltatás 
- tmansvc
  
A konfigurációs kiszolgáló frissítéséhez futtassa a [egyesített telepítővel](service-updates-how-to.md#links-to-currently-supported-update-rollups) újra.

## <a name="azure-active-directory-application-creation-failure"></a>Az Azure Active Directory-alkalmazás létrehozásának sikertelensége

Nincs megfelelő engedélye alkalmazások létrehozására az Azure Active Directory (AAD) használatával a [Open Virtualization alkalmazás (OVA)](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template
) sablont.

A probléma megoldásához jelentkezzen be az Azure Portalon, és tegye a következők egyikét:

- Kérelem az alkalmazás fejlesztőjének az aad-ben. További információ a alkalmazásfejlesztő szerepkör: [rendszergazdája szerepkör engedélyei az Azure Active Directoryban](../active-directory/users-groups-roles/directory-assign-admin-roles.md).
- Ellenőrizze, hogy a **felhasználó hozhat létre alkalmazást** jelző értéke *igaz* az aad-ben. További információkért lásd: [hogyan: A portál használatával hozzon létre egy Azure AD alkalmazás és -szolgáltatásnév erőforrások eléréséhez](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

## <a name="process-servermaster-target-are-unable-to-communicate-with-the-configuration-server"></a>Folyamat vagy a fő cél nem tudnak kommunikálni a konfigurációs kiszolgáló 

A folyamatkiszolgáló (PS) és a fő célkiszolgálót (MT) modulok nem tudnak kommunikálni a konfigurációs kiszolgálót (CS), és azok állapota nincs csatlakoztatva az Azure Portalon.

Ez általában a 443-as hiba miatt. Az alábbi lépések segítségével a port zárolásának feloldásához, majd engedélyezze újra a CS folytatott kommunikáció.

**Ellenőrizze, hogy a fő célkiszolgáló ügynök által meghívott a MARS-ügynök**

Győződjön meg arról, hogy a fő cél ügynök egy TCP-munkamenet hozhat létre a konfigurációs kiszolgáló IP-cím, keresse meg a fő célkiszolgáló agent-naplók a következőhöz hasonló nyomot:

TCP <Replace IP with CS IP here>: 52739 <Replace IP with CS IP here>: 443-AS SYN_SENT 

TCP 192.168.1.40:52739 192.168.1.40:443 SYN_SENT / / cseréje IP itt CS IP-címmel

Ha talál nyomkövetéseket az alábbihoz hasonló az MT agent-naplók, a fő Célkiszolgáló ügynök jelent hibákat a 443-as port:

    #~> (11-20-2018 20:31:51):   ERROR  2508 8408 313 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
    #~> (11-20-2018 20:31:54):   ERROR  2508 8408 314 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
 
Ez a hiba is lehet észlelt, amikor más alkalmazások is vagy egy tűzfalbeállítás blokkolja-e a port 443-as portot használja.

A probléma megoldásához:

- Győződjön meg arról, hogy 443-as portot nem blokkolja a tűzfal.
- Ha a port nem érhető el, mert egy másik alkalmazás használja ezt a portot, állítsa le, és távolítsa el az alkalmazást.
  - Ha a az alkalmazás leállítása nem lehetséges, a telepítő egy új tiszta CS.
- Indítsa újra a konfigurációs kiszolgálót.
- Indítsa újra az IIS szolgáltatást.

### <a name="configuration-server-is-not-connected-due-to-incorrect-uuid-entries"></a>Konfigurációs kiszolgáló nincs csatlakoztatva helytelen UUID-bejegyzések miatt

Ez a hiba akkor fordulhat elő, ha több konfigurációs kiszolgálót (CS) példány UUID bejegyzés található az adatbázisban. A probléma akkor fordul elő, amikor a konfigurációs kiszolgáló virtuális gép klónozása.

A probléma megoldásához:

1. Elavult/régi CS virtuális gép vCenter távolítsa el. További információkért lásd: [kiszolgálók eltávolítása és a tiltsa le a védelmet](site-recovery-manage-registration-and-protection.md).
2. Jelentkezzen be a konfigurációs kiszolgáló virtuális gép, és csatlakozzon a MySQL svsdb1 adatbázishoz. 
3. Hajtsa végre a következő lekérdezést:

    > [!IMPORTANT]
    >
    > Győződjön meg arról, hogy a klónozott konfigurációs kiszolgáló UUID részleteit, vagy a virtuális gépek védelme már nem használt konfigurációs kiszolgáló elavult bejegyzés megadása. Egy helytelen UUID azonosító megadása azt eredményezi az összes meglévő védett elemek az adatok elvesztése.
   
    ```
        MySQL> use svsdb1;
        MySQL> delete from infrastructurevms where infrastructurevmid='<Stale CS VM UUID>';
        MySQL> commit; 
    ```
4. Frissítse a portál oldala.

## <a name="an-infinite-sign-in-loop-occurs-when-entering-your-credentials"></a>Egy végtelen ciklust bejelentkezési akkor történik, ha a hitelesítő adatok megadása

Miután megadta a megfelelő felhasználónevet és jelszót a konfigurációs kiszolgáló OVF, Azure bejelentkezés továbbra is meg kell adni a megfelelő hitelesítő adatokat.

A probléma akkor fordulhat elő, amikor a rendszer pontos ideje szerinti helytelen.

A probléma megoldásához:

A helyes időre beállítania a számítógépen, és próbálkozzon újra a bejelentkezést. 
 