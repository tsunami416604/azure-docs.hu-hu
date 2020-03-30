---
title: A vmware virtuális gépek és fizikai kiszolgálók Azure-ba történő vészhelyreállítása során a konfigurációs kiszolgálóval kapcsolatos problémák elhárítása az Azure Site Recovery használatával | Microsoft dokumentumok
description: Ez a cikk hibaelhárítási információkat tartalmaz a konfigurációs kiszolgáló üzembe helyezéséhez a VMware virtuális gépek és a fizikai kiszolgálók vész-helyreállítási az Azure-ba az Azure Site Recovery használatával.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/13/2019
ms.author: ramamill
ms.openlocfilehash: 25e2b488d3b6e7e5cabd1a71d1489efaf01231b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73748550"
---
# <a name="troubleshoot-configuration-server-issues"></a>A konfigurációs kiszolgáló problémáinak elhárítása

Ez a cikk segít az Azure [Site Recovery konfigurációs](site-recovery-overview.md) kiszolgáló telepítése és kezelése során felmerülő problémák elhárításához. A konfigurációs kiszolgáló felügyeleti kiszolgálóként működik. A konfigurációs kiszolgáló segítségével állítsa be a helyszíni VMware virtuális gépek és fizikai kiszolgálók vészutáni helyreállítását az Azure-ba a Site Recovery használatával. A következő szakaszok ismertetik azokat a leggyakoribb hibákat, amelyek et az új konfigurációs kiszolgáló hozzáadásakor és a konfigurációs kiszolgáló kezelése során tapasztalhat.

## <a name="registration-failures"></a>Regisztrációs hibák

A forrásgép regisztrálja magát a konfigurációs kiszolgálón a mobilitási ügynök telepítésekor. A lépés során fellépő hibák hibakeresése az alábbi irányelvek betartásával:

1. Nyissa meg a C:\ProgramData\ASR\home\svsystems\var\configurator_register_host_static_info.log fájlt. (Lehet, hogy a ProgramData mappa rejtett mappa. Ha nem látható a ProgramData mappa, **a** Fájlkezelő nézetlapján, a **Megjelenítés/elrejtés** csoportban jelölje be a **Rejtett elemek** jelölőnégyzetet.) A hibákat több probléma is okozhatja.

2. Keresse meg a **nem érvényes IP-cím karakterláncot.** Ha a karakterlánc megtalálható:
   1. Ellenőrizze, hogy a kért állomásazonosító megegyezik-e a forrásgép állomásazonosítójával.
   2. Ellenőrizze, hogy a forrásgéphez legalább egy IP-cím van-e rendelve a fizikai hálózati adapterhez. Ahhoz, hogy az ügynök regisztrációja a konfigurációs kiszolgálón sikeres legyen, a forrásgépnek legalább egy érvényes IP v4-címmel kell rendelkeznie a fizikai hálózati adapterhez.
   3. Futtassa az alábbi parancsok egyikét a forrásgépen a forrásgép összes IP-címének levételéhez:
      - Windows esetén:`> ipconfig /all`
      - Linux esetén:`# ifconfig -a`

3. Ha nem **található érvénytelen érvénytelen IP-cím** karakterlánc, keresse meg a **Reason=>NULL**karakterláncot. Ez a hiba akkor fordul elő, ha a forrásgép üres állomást használ a konfigurációs kiszolgálóval való regisztrációhoz. Ha a karakterlánc megtalálható:
    - A problémák megoldása után kövesse [a Forrásgép regisztrálása a konfigurációs kiszolgálóval](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server) című útmutató utasításait a regisztráció manuális újrapróbálkozásához.

4. Ha a **Reason=>NULL** karakterlánc nem található, nyissa meg a C:\ProgramData\ASRSetupLogs\UploadedLogs\ASRUnifiedAgentInstaller.log fájlt. (Lehet, hogy a ProgramData mappa rejtett mappa. Ha nem látható a ProgramData mappa, **a** Fájlkezelő nézetlapján, a **Megjelenítés/elrejtés** csoportban jelölje be a **Rejtett elemek** jelölőnégyzetet.) A hibákat több probléma is okozhatja. 

5. Keresse meg a string **post kérés: (7) - Nem sikerült csatlakozni a szerverhez**. Ha a karakterlánc megtalálható:
    1. A forrásgép és a konfigurációs kiszolgáló közötti hálózati problémák megoldása. Ellenőrizze, hogy a konfigurációs kiszolgáló elérhető-e a forrásgépről olyan hálózati eszközökkel, mint a ping, traceroute vagy webböngésző. Győződjön meg arról, hogy a forrásgép a 443-as porton keresztül el tudja érni a konfigurációs kiszolgálót.
    2. Ellenőrizze, hogy a forrásgépen lévő tűzfalszabályok blokkolják-e a forrásgép és a konfigurációs kiszolgáló közötti kapcsolatot. A hálózati rendszergazdákkal együttműködve feloldhatja a csatlakozási problémák at.
    3. Győződjön meg arról, hogy a [Webhely-helyreállítási mappa víruskereső programokból való kizárásai](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) ki vannak zárva a víruskereső szoftverből.
    4. A hálózati problémák feloldása után próbálkozzon újra a regisztrációval [a Forrásgép regisztrálása a konfigurációs kiszolgálóval](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)című útmutató ban található irányelvek betartásával.

6. Ha a **karakterlánc-bejegyzéskérése: (7) - Nem található a kiszolgálóhoz való csatlakozás,** ugyanabban a naplófájlban keresse meg a karakterlánc-kérelmet: **(60) - A társtanúsítvány nem hitelesíthető adott hitelesítésszolgáltatói tanúsítványokkal.** Ez a hiba azért fordulhat elő, mert a konfigurációs kiszolgáló tanúsítványa lejárt, vagy a forrásgép nem támogatja a TLS 1.0 vagy újabb SSL protokollokat. Az akkor is előfordulhat, ha egy tűzfal blokkolja az SSL-kommunikációt a forrásgép és a konfigurációs kiszolgáló között. Ha a karakterlánc megtalálható: 
    1. A probléma megoldásához csatlakozzon a konfigurációs kiszolgáló IP-címéhez a forrásgép en lévő webböngészővel. Használja az URI\/ \/ https:<\>konfigurációs kiszolgáló IP-címe :443/. Győződjön meg arról, hogy a forrásgép a 443-as porton keresztül el tudja érni a konfigurációs kiszolgálót.
    2. Ellenőrizze, hogy a forrásszámítógépen lévő tűzfalszabályokat hozzá kell-e adni vagy el kell-e távolítani ahhoz, hogy a forrásgép beszélhesse a konfigurációs kiszolgálót. A használatban lévő tűzfalszoftverek sokfélesége miatt nem tudjuk felsorolni az összes szükséges tűzfalkonfigurációt. A hálózati rendszergazdákkal együttműködve feloldhatja a csatlakozási problémák at.
    3. Győződjön meg arról, hogy a [Webhely-helyreállítási mappa víruskereső programokból való kizárásai](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) ki vannak zárva a víruskereső szoftverből.  
    4. A probléma megoldása után próbálkozzon újra a regisztrációval [a Forrásgép regisztrálása a konfigurációs kiszolgálóval](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server)című útmutatóban található irányelvek betartásával.

7. Linuxon, ha a platform értéke\><INSTALLATION_DIR /etc/drscout.conf sérült, a regisztráció sikertelen lesz. A probléma azonosításához nyissa meg a /var/log/ua_install.log fájlt. Keresse meg a karakterlánc **megszakítása konfiguráció VM_PLATFORM érték null, vagy nem VmWare / Azure**. A platformot a **VmWare** vagy az **Azure**beállításra kell állítani. Ha a drscout.conf fájl sérült, javasoljuk, hogy [távolítsa el a mobilitási ügynököt,](vmware-physical-manage-mobility-service.md#uninstall-mobility-service) majd telepítse újra a mobilitási ügynököt. Ha az eltávolítás sikertelen, hajtsa végre a következő lépéseket: a. Nyissa meg a Installation_Directory/uninstall.sh fájlt, és fűzzön megjegyzést a StopServices függvény **hívásához.**
    b. Nyissa meg a Installation_Directory/Vx/bin/uninstall.sh fájlt, és fűzzön hozzá a **stop_services** függvényhez intézett híváshoz.
    c. Nyissa meg a Installation_Directory/Fx/uninstall.sh fájlt, és fűzzön hozzá megjegyzést az fx szolgáltatás leállítására váró teljes szakaszhoz.
    d. [Távolítsa el](vmware-physical-manage-mobility-service.md#uninstall-mobility-service) a mobilitási ügynököt. A sikeres eltávolítás után indítsa újra a rendszert, majd próbálja meg újratelepíteni a mobilitási ügynököt.

## <a name="installation-failure-failed-to-load-accounts"></a>Telepítési hiba: Nem sikerült betölteni a fiókokat

Ez a hiba akkor fordul elő, ha a szolgáltatás nem tudja olvasni az adatokat az átviteli kapcsolatról, amikor telepíti a mobilitási ügynököt, és regisztrál a konfigurációs kiszolgálón. A probléma megoldásához győződjön meg arról, hogy a TLS 1.0 engedélyezve van a forrásszámítógépen.

## <a name="vcenter-discovery-failures"></a>vCenter felderítési hibák

A vCenter-felderítési hibák megoldásához adja hozzá a vCenter-kiszolgálót a byPass proxybeállításokhoz. 

- Töltse le psexec eszköz [innen](https://aka.ms/PsExec) eléréséhez Rendszer felhasználói tartalom.
- Az Internet Explorer megnyitása a rendszerfelhasználói tartalomban a következő psexec -s -i "%programfiles%\Internet Explorer\iexplore.exe" parancssor futtatásával
- Adja hozzá a proxybeállításokat az IE-ben, és indítsa újra a tmanssvc szolgáltatást.
- A DRA-proxybeállítások konfigurálásához futtassa a cd C:\Program Files\Microsoft Azure Site Recovery Provider parancsot
- Ezután hajtsa végre a DRConfiguratort. EXE /configure /AddBypassUrls [add IP-cím/FQDN a vCenter Server megadott **konfigurálása során vCenter Server/vSphere ESXi kiszolgáló** lépés [konfigurációs kiszolgáló központi telepítés](vmware-azure-deploy-configuration-server.md#configure-settings)]

## <a name="change-the-ip-address-of-the-configuration-server"></a>A konfigurációs kiszolgáló IP-címének módosítása

Javasoljuk, hogy ne módosítsa a konfigurációs kiszolgáló IP-címét. Győződjön meg arról, hogy a konfigurációs kiszolgálóhoz rendelt összes IP-cím statikus IP-cím. Ne használjon DHCP IP-címeket.

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008: Az SAML token érvénytelen

A hiba elkerülése érdekében győződjön meg arról, hogy a rendszeróra ideje nem különbözik 15 percnél tovább a helyi időtől. A regisztráció befejezéséhez futtassa ismét a telepítőt.

## <a name="failed-to-create-a-certificate"></a>Nem sikerült létrehozni egy tanúsítványt

A hely-helyreállítás hitelesítéséhez szükséges tanúsítvány nem hozható létre. Futtassa újra a telepítőt, miután biztosította, hogy helyi rendszergazdaként futtatja a telepítőt.

## <a name="failure-to-activate-windows-license-from-server-standard-evaluation-to-server-standard"></a>Nem sikerült aktiválni a Windows-licencet a kiszolgálói standard értékeléstől a kiszolgálószabványig

1. A konfigurációs kiszolgáló OVF-en keresztültörténő telepítésének részeként egy 180 napig érvényes kiértékelési licencet használ. A licenc lejárta előtt aktiválnia kell ezt a licencet. Ez a konfigurációs kiszolgáló gyakori leállítását eredményezheti, és így akadályozza a replikációs tevékenységeket.
2. Ha nem tudja aktiválni a Windows-licencet, a probléma megoldásához forduljon a [Windows támogatási csapatához.](https://aka.ms/Windows_Support)

## <a name="register-source-machine-with-configuration-server"></a>Forrásgép regisztrálása a konfigurációs kiszolgálóval

### <a name="if-the-source-machine-runs-windows"></a>Ha a forrásgép en Windows

Futtassa a következő parancsot a forrásszámítógépen:

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <configuration server IP address> /PassphraseFilePath <passphrase file path>
```

Beállítás | Részletek
--- | ---
Használat | UnifiedAgentConfigurator.exe /CSEndPoint <konfigurációs kiszolgáló IP-címe\> /PassphraseFilePath <jelszófájl elérési útját\>
Ügynök konfigurációs naplói | A %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log fájlban található.
/CSEndPoint | Kötelező paraméter. Megadja a konfigurációs kiszolgáló IP-címét. Használjon érvényes IP-címet.
/PassphraseFilePath |  Kötelező. A jelszó helye. Használjon érvényes UNC-t vagy helyi fájlelérési utat.

### <a name="if-the-source-machine-runs-linux"></a>Ha a forrásgép Linuxot futtat

Futtassa a következő parancsot a forrásszámítógépen:

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <configuration server IP address> -P /var/passphrase.txt
  ```

Beállítás | Részletek
--- | ---
Használat | cd /usr/local/ASR/Vx/bin<br /><br /> UnifiedAgentConfigurator.sh -i <konfigurációs kiszolgáló IP-címe\> -P <jelszófájl elérési útja\>
-i | Kötelező paraméter. Megadja a konfigurációs kiszolgáló IP-címét. Használjon érvényes IP-címet.
-P |  Kötelező. Annak a fájlnak a teljes fájlelérési útja, amelybe a jelszót menti a rendszer. Használjon bármilyen érvényes mappát.

## <a name="unable-to-configure-the-configuration-server"></a>Nem lehet konfigurálni a konfigurációs kiszolgálót

Ha a konfigurációs kiszolgálótól eltérő alkalmazásokat telepít a virtuális gépen, előfordulhat, hogy nem tudja konfigurálni a fő célt. 

A konfigurációs kiszolgálónak egyetlen célú kiszolgálónak kell lennie, és megosztott kiszolgálóként kell használnia. 

További információt a [Konfigurációs kiszolgáló telepítése című](vmware-azure-deploy-configuration-server.md#faqs)témakörkonfigurációs gyakori kérdések című témakörben talál. 

## <a name="remove-the-stale-entries-for-protected-items-from-the-configuration-server-database"></a>A védett elemek elavult bejegyzéseinek eltávolítása a konfigurációs kiszolgáló adatbázisából 

Az elavult védett számítógép konfigurációs kiszolgálón való eltávolításához kövesse az alábbi lépéseket. 
 
1. Az elavult bejegyzés forrásgépének és IP-címének meghatározása: 

    1. Nyissa meg a MYSQL cmdline-t rendszergazdai módban. 
    2. Hajtsa végre a következő parancsokat. 
   
        ```
        mysql> use svsdb1;
        mysql> select id as hostid, name, ipaddress, ostype as operatingsystem, from_unixtime(lasthostupdatetime) as heartbeat from hosts where name!='InMageProfiler'\G;
        ```

        Ez visszaadja a regisztrált gépek listáját az IP-címekkel és az utolsó szívveréssel együtt. Keresse meg az elavult replikációs párokkal rendelkező állomást.

2. Nyisson meg egy rendszergazda jogú parancssort, és keresse meg a C:\ProgramData\ASR\home\svsystems\bin mappát. 
4. A regisztrált állomások adatainak és az elavult bejegyzési adatoknak a konfigurációs kiszolgálóról való eltávolításához futtassa a következő parancsot a forrásgép és az elavult bejegyzés IP-címe segítségével. 
   
    `Syntax: Unregister-ASRComponent.pl -IPAddress <IP_ADDRESS_OF_MACHINE_TO_UNREGISTER> -Component <Source/ PS / MT>`
 
    Ha az "OnPrem-VM01" forráskiszolgáló-bejegyzése 10.0.0.4 ip-címmel rendelkezik, akkor használja inkább a következő parancsot.
 
    `perl Unregister-ASRComponent.pl -IPAddress 10.0.0.4 -Component Source`
 
5. A konfigurációs kiszolgálóval való újraregisztráláshoz indítsa újra a következő szolgáltatásokat a forrásszámítógépen. 
 
    - InMage Scout Application Service
    - InMage Scout VX ügynök - Sentinel / Előőrs

## <a name="upgrade-fails-when-the-services-fail-to-stop"></a>A frissítés sikertelen, ha a szolgáltatások nem állnak le

A konfigurációs kiszolgáló frissítése sikertelen, ha bizonyos szolgáltatások nem állnak le. 

A probléma azonosításához keresse meg a konfigurációs kiszolgálóN található C:\ProgramData\ASRSetupLogs\CX_TP_InstallLogFile webhelyet. Ha a következő hibákat találja, az alábbi lépésekkel oldja meg a problémát: 

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

A probléma megoldása:

Manuálisan állítsa le a következő szolgáltatásokat:

- cxprocessserver
- InMage Scout VX ügynök – Sentinel/Előőrs, 
- Microsoft Azure helyreállítási szolgáltatások ügynöke, 
- Microsoft Azure webhely-helyreállítási szolgáltatás, 
- tmansvc között
  
A konfigurációs kiszolgáló frissítéséhez futtassa újra az [egységes telepítőt.](service-updates-how-to.md#links-to-currently-supported-update-rollups)

## <a name="azure-active-directory-application-creation-failure"></a>Az Azure Active Directory-alkalmazások létrehozása sikertelen

Nem rendelkezik megfelelő engedélyekkel egy alkalmazás létrehozásához az Azure Active Directoryban (AAD) az [Open Virtualization Application (OVA)](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template
) sablon használatával.

A probléma megoldásához jelentkezzen be az Azure Portalra, és tegye az alábbiak egyikét:

- Kérje az alkalmazásfejlesztői szerepkört az AAD-ben. Az Alkalmazásfejlesztői szerepkörről további információt az [Azure Active Directory rendszergazdai szerepkör-engedélyei című témakörben talál.](../active-directory/users-groups-roles/directory-assign-admin-roles.md)
- Ellenőrizze, hogy a **felhasználó létrehozhat-e** alkalmazásjelzőt, és állítsa *igaz* az AAD-ben. További információ: [Hogyan: A portál használata az erőforrásokhoz hozzáférő Azure AD-alkalmazás és egyszerű szolgáltatás létrehozásához](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)című témakörben található.

## <a name="process-servermaster-target-are-unable-to-communicate-with-the-configuration-server"></a>A folyamatkiszolgáló/főtároló nem tud kommunikálni a konfigurációs kiszolgálóval 

A folyamatkiszolgáló (PS) és a master target (MT) modulok nem tudnak kommunikálni a konfigurációs kiszolgálóval (CS), és állapotuk nem csatlakozik az Azure Portalon.

Ezt általában a 443-as port hibája okozta. A következő lépésekkel feloldja a port blokkolását, és újra engedélyezze a kommunikációt a CS-vel.

**Ellenőrizze, hogy a Fő célügynök hívja-e meg a MARS-ügynököt**

Annak ellenőrzéséhez, hogy a fő célügynök képes-e TCP-munkamenetet létrehozni a konfigurációs kiszolgáló IP-címéhez, keressen a fő célügynök naplóiban az alábbihoz hasonló nyomkövetést:

TCP \<csere IP CS IP itt>:52739 \<Cserélje le az IP CS IP itt>:443 SYN_SENT 

TCP 192.168.1.40:52739 192.168.1.40:443 SYN_SENT // Ip cseréje CS IP itt

Ha az MT-ügynöknaplókban az alábbihoz hasonló nyomokat talál, az MT ügynök hibákat jelez a 443-as porton:

    #~> (11-20-2018 20:31:51):   ERROR  2508 8408 313 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
    #~> (11-20-2018 20:31:54):   ERROR  2508 8408 314 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
 
Ez a hiba akkor jelentkezik, ha más alkalmazások is használják a 443-as portot, vagy mert a tűzfal a portblokkolást blokkolja.

A probléma megoldása:

- Ellenőrizze, hogy a tűzfal nem blokkolja-e a 443-as portot.
- Ha a port nem érhető el egy másik, a portot használó alkalmazás miatt, állítsa le és távolítsa el az alkalmazást.
  - Ha az alkalmazás leállítása nem valósítható meg, állítson be egy új, tiszta CS-t.
- Indítsa újra a konfigurációs kiszolgálót.
- Indítsa újra az IIS szolgáltatást.

### <a name="configuration-server-is-not-connected-due-to-incorrect-uuid-entries"></a>A konfigurációs kiszolgáló nem csatlakozik helytelen UUID bejegyzések miatt

Ez a hiba akkor fordulhat elő, ha az adatbázisban több konfigurációs kiszolgáló (CS) példány UUID bejegyzés található. A probléma gyakran akkor fordul elő, ha klónozza a konfigurációs kiszolgáló virtuális gép.

A probléma megoldása:

1. Távolítsa el az elavult/régi CS-virtuális gép a vCenterből. További információt a [Kiszolgálók eltávolítása és a védelem letiltása](site-recovery-manage-registration-and-protection.md)című témakörben talál.
2. Jelentkezzen be a konfigurációs kiszolgáló virtuális gép, és csatlakozzon a MySQL svsdb1 adatbázishoz. 
3. Hajtsa végre a következő lekérdezést:

    > [!IMPORTANT]
    >
    > Ellenőrizze, hogy megadja-e a klónozott konfigurációs kiszolgáló UUID adatait, vagy a konfigurációs kiszolgáló elavult bejegyzését, amelyet már nem használnak a virtuális gépek védelmére. Ha helytelen UUID azonosítót ad meg, az összes meglévő védett elem adatai elvesznek.
   
    ```
        MySQL> use svsdb1;
        MySQL> delete from infrastructurevms where infrastructurevmid='<Stale CS VM UUID>';
        MySQL> commit; 
    ```
4. Frissítse a portállapot.

## <a name="an-infinite-sign-in-loop-occurs-when-entering-your-credentials"></a>A hitelesítő adatok megadásakor végtelen bejelentkezési ciklus lép fel

Miután megadta a megfelelő felhasználónevet és jelszót az OVF konfigurációs kiszolgálón, az Azure bejelentkezése továbbra is kéri a megfelelő hitelesítő adatokat.

Ez a probléma akkor fordulhat elő, ha a rendszeridő helytelen.

A probléma megoldása:

Állítsa be a megfelelő időt a számítógépen, majd próbálja meg újra a bejelentkezést. 
 