---
title: "A Linux rendszerű számítógépek csatlakozni az Operations Management Suite (OMS) |} Microsoft Docs"
description: "A cikkből megtudhatja, hogyan csatlakozzon az Azure, a más felhőalapú vagy helyszíni OMS-ügynök használatával Linux OMS üzemeltetett Linux rendszerű számítógépek."
services: log-analytics
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: 
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2017
ms.author: magoedte
ms.openlocfilehash: c9902e1b8644c2b0a894f9cde98f2056564775c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="connect-your-linux-computers-to-operations-management-suite-oms"></a>A Linux rendszerű számítógépek csatlakozni az Operations Management Suite (OMS) 

A Microsoft Operations Management Suite (OMS), összegyűjtheti, és Linux rendszerű számítógépek és a tároló-megoldásokkal, például a Docker, fizikai kiszolgálóként vagy virtuális gépek, virtuális gépek a helyszíni adatközpontban található adatok intézkedjen egy például az Amazon Web Services (AWS) vagy a Microsoft Azure-felhőben üzemeltetett szolgáltatás. Is használhatja OMS elérhető megoldások például a változások követése, konfigurációs módosításokat, és a frissítéskezelés szoftverfrissítések segítségével proaktívan felügyelheti a Linux virtuális gépek az életciklus kezeléséhez. 

Az OMS-ügynököt a Linux rendszerhez használt 443-as TCP-porton keresztül kommunikál az OMS szolgáltatással kimenő, és ha a számítógép csatlakozik egy tűzfal vagy a proxy-kiszolgálót kommunikálnak az interneten keresztül tekintse meg [használható az ügynök OMS vagy HTTP-proxy kiszolgáló konfigurálása Átjáró](#configuring-the-agent-for-use-with-an-http-proxy-server-or-oms-gateway) milyen konfiguráció módosításait fogja tudni alkalmazni kell.  Ha a System Center 2016 - Operations Manager, illetve az Operations Manager 2012 R2-ben futtató számítógép lehet többhelyű az adatok gyűjtéséhez és a szolgáltatás továbbítja, és továbbra is az Operations Manager által figyelt az OMS szolgáltatással.  Egy Operations Manager OMS szolgáltatással integrált felügyeleti csoportot által figyelt Linux rendszerű számítógépek nem kapják meg a konfigurációs adatforrások, és előre gyűjtött adatokat a felügyeleti csoporton keresztül.  Az OMS-ügynököt nem konfigurálható a jelentés egynél több munkaterületet.  

Ha az IT-biztonsági házirendeknek nem engedélyezi a számítógépek a hálózat csatlakozik az internethez, az ügynök beállítható úgy, hogy az OMS-átjárón, attól függően, hogy a megoldás engedélyezte az összegyűjtött adatok küldésére és fogadására a konfigurációs adatokat. További információt és az OMS Linux-ügynök kommunikációja áthaladjon egy OMS-átjárót az OMS szolgáltatáshoz konfigurálásával kapcsolatos lépéseket, [számítógépeket csatlakoztatni az OMS Szolgáltatáshoz az OMS-átjáró](log-analytics-oms-gateway.md).  

A következő ábra szemlélteti a Linux rendszerű számítógépek ügynök által felügyelt és az OMS-ben, beleértve a irányát és portok közötti kapcsolat.

![közvetlen ügynökkommunikációhoz OMS diagramja](./media/log-analytics-agent-linux/log-analytics-agent-linux-communication.png)

## <a name="system-requirements"></a>Rendszerkövetelmények
Megkezdése előtt tekintse át az alábbi részleteket megfelel az Előfeltételek ellenőrzése.

### <a name="supported-linux-operating-systems"></a>Támogatott Linux operációs rendszerek
A következő Linux terjesztésekről hivatalosan támogatottak.  Azonban az OMS-ügynököt Linux előfordulhat, hogy is futtathatja más azokat a terjesztéseket nem szerepel a listában.

* Amazon Linux 2012.09 való 2015.09 (x86/x64)
* CentOS Linux 5, 6 és 7 (x86/x64)
* Oracle Linux 5, 6 és 7 (x86/x64)
* Red Hat Enterprise Linux Server 5, 6, 7 (x86/x64)
* Debian GNU/Linux 8 (x86/x64), 6, 7
* Ubuntu 12.04 LTS, 14.04 LTS, 15.04, 15.10, 16.04 LTS (x86/x64)
* SUSE Linux Enterprise Server 11 és 12 (x86/x64)

### <a name="network"></a>Network (Hálózat)
Az alábbi lista a proxy és tűzfal konfigurációs adatokat, a Linux-ügynök OMS folytatott kommunikációhoz szükséges információt. Akkor kimenő forgalomról beszélünk a hálózatról az OMS szolgáltatáshoz. 

|Ügynök erőforrása| Portok |  
|------|---------|  
|*.ods.opinsights.azure.com | 443-as port|   
|*.oms.opinsights.azure.com | 443-as port|   
|*.BLOB.Core.Windows.NET/ | 443-as port|   
|*.azure-automation.net | 443-as port|  

### <a name="package-requirements"></a>Csomag követelmények

 **Szükséges csomag**   | **Leírás**   | **Minimális verzió**
--------------------- | --------------------- | -------------------
Glibc | GNU C-függvénytár   | 2.5-12 
Openssl | OpenSSL-függvénytárak | 0.9.8e vagy 1.0
Curl | cURL-webügyfél | 7.15.5
Python-ctypes | | 
A PAM | Cserélhető hitelesítési modulok | 

> [!NOTE]
>  Rsyslog vagy a syslog-ng kell gyűjteni a syslog-üzeneteket. Syslog-események gyűjtése nem támogatott az alapértelmezett a syslog démon a Red Hat Enterprise Linux-, CentOS, és Oracle Linux verziója (sysklog) 5-ös verzióját. Syslog-adatokat gyűjteni a terjesztéseket ezen verziója, a rsyslog démon kell telepíthetők és konfigurálhatók lecseréli sysklog, 

Az ügynök több csomagot tartalmaz. A kiadási fájl tartalmazza a következő csomagok, futtassa a rendszerhéj-csomagot a rendelkezésre álló `--extract`:

**Csomag** | **Verzió** | **Leírás**
----------- | ----------- | --------------
omsagent | 1.4.1 | Az Operations Management Suite-ügynök Linux
omsconfig | 1.1.1 | Az OMS-ügynök konfigurálása ügynök
OMI | 1.2.0 | Nyissa meg a Management Infrastructure (OMI) – egy egyszerűsített CIM-kiszolgáló
az scx | 1.6.3 | Operációs rendszer teljesítménymutatók OMI a CIM-szolgáltatók
Apache-cimprov | 1.0.1 | Apache HTTP Server teljesítményfigyelés-szolgáltató az OMI. Apache HTTP Server észlelésekor telepítve.
MySQL-cimprov | 1.0.1 | MySQL-kiszolgáló teljesítményfigyelés-szolgáltató az OMI. Ha a MySQL/MariaDB kiszolgáló észleli telepítve.
docker-cimprov | 1.0.0 | Docker-szolgáltató az OMI. Ha a Docker észleli telepítve.

### <a name="compatibility-with-system-center-operations-manager"></a>A System Center Operations Manager kompatibilitása
Az OMS-ügynököt a Linux ügynök bináris fájlokat osztanak meg a System Center Operations Manager-ügynök. Ha az OMS-ügynököt a Linux, felügyelete jelenleg az Operations Manager rendszerre telepíti, egy újabb verzióra frissíti az OMI és SCX csomagokat a számítógépen. Ebben a kiadásban az OMS Szolgáltatáshoz és a System Center 2016 - Operations Manager, illetve az Operations Manager 2012 R2 ügynökök Linux kompatibilisek. 

> [!NOTE]
> A System Center 2012 SP1 és a korábbi verziók jelenleg nem kompatibilis vagy nem támogatott a Linux OMS-ügynököt.<br>
> Ha olyan számítógépre, amelyen az Operations Manager által jelenleg nem figyeli a Linux az OMS-ügynök telepítve van és folyamatosan figyelje a számítógépet az Operations Manager majd kívánja, módosítania kell a [OMI konfigurációs](#enable-the-oms-agent-for-linux-to-report-to-system-center-operations-manager) előtt felderítése a számítógépen. **Ez a lépés *nem* szükséges, ha az Operations Manager-ügynök telepítve van az OMS-ügynököt előtt Linux.**

### <a name="system-configuration-changes"></a>Rendszer-konfigurációs módosítások
Az OMS-ügynököt a Linux-csomagok telepítése után a következő további rendszerszintű konfigurációs módosítások. Ezen összetevők a omsagent csomag eltávolításakor a rendszer törli.

* Egy jogosulatlan felhasználó nevű: `omsagent` jön létre. A omsagent démon fut, ezt a fiókot.
* /Etc/sudoers.d/omsagent sudoers "tartalmaz" fájl jön létre. Ez a fájl engedélyezi a syslog és omsagent démonok újraindítására omsagent. Ha a sudo "tartalmaz" direktívák nem támogatottak a sudo telepített verziója, ezek a bejegyzések /etc/sudoers kerülnek.
* A rendszernaplózás konfigurálásánál úgy módosul, hogy események csoportját, hogy az ügynök továbbítja. További információkért lásd: a **adatok gyűjtésének konfigurálása** az alábbi szakasz.

### <a name="upgrade-from-a-previous-release"></a>Frissítse korábbi kiadásáról
Ebben a kiadásban 1.0.0-47 támogatottnál korábbi verzióit frissíteni. A telepítés végrehajtása a `--upgrade` parancs összetevők az ügynök a legújabb verzióra frissíti.

## <a name="installing-the-agent"></a>Az ügynök telepítése

Ez a szakasz ismerteti az OMS-ügynök telepítése Linux rendszert egy bunndle, az ügynök összetevői a Debian és RPM csomagokat tartalmazó.  Ez közvetlenül telepített vagy kibontva, az egyes csomagok lekérdezése.  

Először van szüksége az OMS-munkaterület azonosítója és a kulcs, amely átvált a [OMS klasszikus portál](https://mms.microsoft.com).  A a **áttekintése** lap, a felső menüben válassza **beállítások**, és navigáljon arra **Sources\Linux kiszolgálók csatlakoztatva**.  Jobb oldalán érték **munkaterület azonosítója** és **elsődleges kulcs**.  Másolással illessze be a kedvenc szerkesztő mindkettő.    

1. Töltse le a legújabb [Linux (x64) OMS-ügynököt](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.1-45/omsagent-1.4.1-45.universal.x64.sh) vagy [Linux x86 OMS-ügynököt](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.1-45/omsagent-1.4.1-45.universal.x86.sh) a Githubról.  
2. Vigye át a megfelelő csomagot (x86 vagy x64) a Linux-számítógép scp/sftp használatával.
3. A csomag telepítéséhez használja a `--install` vagy `--upgrade` argumentum. 

    > [!NOTE]
    > Ha a meglévő csomagokat például ha már telepítve van a a System Center Operations Manager Linux-ügynök van telepítve, használja a `--upgrade` argumentum. A telepítés során kapcsolódjon az Operations Management Suite, adja meg a `-w <WorkspaceID>` és `-s <Shared Key>` paraméterek.


#### <a name="to-install-and-onboard-directly"></a>A telepítendő és közvetlen előkészítéséről
```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade -w <workspace id> -s <shared key>
```

#### <a name="to-upgrade-the-agent-package"></a>Az ügynök-csomag frissítése
```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade
```

#### <a name="to-install-and-onboard-to-a-workspace-in-us-government-cloud"></a>A telepítendő és érheti el a felhőben US Government munkaterület
```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
```

## <a name="configuring-the-agent-for-use-with-a-proxy-server-or-oms-gateway"></a>Az ügynök használatra konfigurálja a proxykiszolgáló vagy az OMS-átjáró
Az OMS-ügynököt a Linux támogatja, akár a proxykiszolgáló vagy az OMS átjáró az OMS szolgáltatáshoz, a HTTPS protokoll használatával kommunikál.  A névtelen és alapszintű hitelesítést (felhasználónév/jelszó) is támogatja.  

### <a name="proxy-configuration"></a>Proxykiszolgáló-konfiguráció
A proxy konfigurációs érték szintaxisa a következő:

`[protocol://][user:password@]proxyhost[:port]`

Tulajdonság|Leírás
-|-
Protokoll|https
Felhasználó|Nem kötelező felhasználónév a proxyhitelesítéshez
jelszó|Opcionális jelszót a proxyhitelesítéshez
proxyhost|Cím vagy FQDN-jét a proxy server/OMS átjáró
port|A proxy server/OMS átjáró választható portszáma

Például:`https://user01:password@proxy01.contoso.com:30443`

A proxy kiszolgáló telepítése során vagy a telepítés után a proxy.conf konfigurációs fájl módosításával adható meg.   

### <a name="specify-proxy-configuration-during-installation"></a>Adja meg a proxy konfigurációjának telepítése során
A `-p` vagy `--proxy` argumentuma a omsagent telepítési csomagot, adja meg a proxykiszolgáló-konfigurációt szeretne használni. 

```
sudo sh ./omsagent-<version>.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
```

### <a name="define-the-proxy-configuration-in-a-file"></a>Adja meg a proxy konfigurációs fájlba
A proxykonfiguráció állítható be a fájlok `/etc/opt/microsoft/omsagent/proxy.conf` és `/etc/opt/microsoft/omsagent/conf/proxy.conf `. A fájlok is kell közvetlenül létrehozott vagy módosított, de a rájuk vonatkozó engedélyek meg kell adni a omiuser felhasználó olvasási jogosultsággal a fájlokra. Példa:
```
proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf
sudo chmod 600 /etc/opt/microsoft/omsagent/proxy.conf /etc/opt/microsoft/omsagent/conf/proxy.conf  
sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
```

### <a name="removing-the-proxy-configuration"></a>A proxykiszolgáló-konfiguráció eltávolítása
Távolítsa el a korábban meghatározott proxy konfigurációját, és a közvetlen kapcsolat visszaállításához, távolítsa el a proxy.conf fájlt:
```
sudo rm /etc/opt/microsoft/omsagent/proxy.conf /etc/opt/microsoft/omsagent/conf/proxy.conf
sudo /opt/microsoft/omsagent/bin/service_control restart 
```

## <a name="onboarding-with-operations-management-suite"></a>Az Operations Management Suite előkészítési
A munkaterület azonosítója és kulcsa nem lett megadva a csomag telepítése során, ha az ügynök később regisztrálni kell az Operations Management Suite szolgáltatásban.

### <a name="onboarding-using-the-command-line"></a>Bevezetés a parancssor használatával
Futtassa a omsadmin.sh parancsot, hogy megadja a munkaterület azonosítója és a munkaterület kulcsát. Ez a parancs (rendelkező sudo jogosultsági szint emeléséhez) rendszergazdaként kell futtatnia:
```
cd /opt/microsoft/omsagent/bin
sudo ./omsadmin.sh -w <WorkspaceID> -s <Shared Key>
```

### <a name="onboarding-using-a-file"></a>A bevezetési fájl használatával
1.  A fájl létrehozása `/etc/omsagent-onboard.conf`. A fájl olvasható és írható az alapvető kell lennie.
`sudo vi /etc/omsagent-onboard.conf`
2.  Helyezze be a fájlt a munkaterület azonosítója és a megosztott kulcs a következő sorokat:

        WORKSPACE_ID=<WorkspaceID>  
        SHARED_KEY=<Shared Key>  
   
3.  A következő parancsot a Onboard az OMS-be:`sudo /opt/microsoft/omsagent/bin/omsadmin.sh`
4.  A fájl törlődik a sikeres bevezetése.

## <a name="enable-the-oms-agent-for-linux-to-report-to-system-center-operations-manager"></a>A Linux számára, hogy a System Center Operations Manager OMS-ügynök engedélyezése
Hajtsa végre a következő lépésekkel állíthatja be az OMS-ügynököt, hogy egy System Center Operations Manager felügyeleti csoport linuxos.  

1. A fájl szerkesztése`/etc/opt/omi/conf/omiserver.conf`
2. Győződjön meg arról, hogy a kezdődő **httpsport =** határozza meg az 1270-es port. Például:`httpsport=1270`
3. Indítsa újra az OMI-kiszolgálón:`sudo /opt/omi/bin/service_control restart`

## <a name="agent-logs"></a>Ügynök naplók
Linux OMS-ügynököt a naplókban találhatók: `/var/opt/microsoft/omsagent/<workspace id>/log/` a naplókat a omsconfig (ügynök konfigurációjának) program helyen találhatók: `/var/opt/microsoft/omsconfig/log/` (amely teljesítményadatokat metrikák) OMI és az SCX-összetevők naplóinak helyen találhatók:`/var/opt/omi/log/ and /var/opt/microsoft/scx/log`

### <a name="log-rotation-configuration"></a>Napló Elforgatás konfigurációs ##
A napló omsagent Elforgatás konfigurációs helyen találhatók:`/etc/logrotate.d/omsagent-<workspace id>`

Az alapértelmezett beállítások a következők: 
```
/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log {
    rotate 5
    missingok
    notifempty
    compress
    size 50k
    copytruncate
}
```

## <a name="uninstalling-the-oms-agent-for-linux"></a>A Linux OMS-ügynök eltávolítása
A ügynökcsomagokat el lehet távolítani a csomagot .sh fájl futtatásával az `--purge` argumentum, amely az ügynök és a konfiguráció teljesen eltávolítja a számítógépről.   

```
> sudo rpm -e omsconfig
> sudo rpm -e omsagent
> sudo /opt/microsoft/scx/bin/uninstall
```

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="issue-unable-to-connect-through-proxy-to-oms"></a>Probléma: Nem lehet csatlakozni az OMS-proxy használatával

#### <a name="probable-causes"></a>Lehetséges okok
* A bevezetési során megadott proxy helytelen volt.
* Az OMS végpontok nincsenek az adatközpontban található whitelistested 

#### <a name="resolutions"></a>Megoldások
1. Az OMS szolgáltatáshoz az OMS-ügynököt a Linux-a kapcsolóval használja a következő parancsot a Reonboard `-v` engedélyezve van. A settubg lehetővé teszi, hogy az ügynök az OMS szolgáltatáshoz a proxyn keresztül történő kapcsolódás részletes kimenet. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`

2. Tekintse át a szakasz [az ügynök használatra konfigurálja a proxykiszolgáló vagy az OMS-átjáró](#configuring the-agent-for-use-with-a-proxy-server-or-oms-gateway) megfelelően konfigurált proxykiszolgálón keresztül kommunikálnak az ügynököt ellenőrzéséhez.    
* Kettős ellenőrizze, hogy az OMS Szolgáltatásvégpontok szerepel az engedélyezési listán:

    |Ügynök erőforrása| Portok |  
    |------|---------|  
    |*.ods.opinsights.azure.com | 443-as port|   
    |*.oms.opinsights.azure.com | 443-as port|   
    |ods.systemcenteradvisor.com | 443-as port|   
    |*.BLOB.Core.Windows.NET/ | 443-as port|   

### <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Probléma: 403-as hibaüzenetet kap közben előkészítésére

#### <a name="probable-causes"></a>Lehetséges okok
* Dátum és idő nem megfelelő Linux-kiszolgálón 
* Munkaterületének Azonosítóját és kulcsát használt nem megfelelőek

#### <a name="resolution"></a>Megoldás:

1. Ellenőrizze az időt a parancs dátuma a Linux-kiszolgálóra. Ha a idő 15 perc, az aktuális időponthoz képest +/-, akkor bevezetési sikertelen lesz. Megfelelő ez frissítse a dátumot és/vagy a Linux-kiszolgáló időzónáját. 
2. Ellenőrizze, hogy a legújabb verzióját az OMS-ügynököt telepítette Linux.  A legújabb verziója most értesíti, ha időeltérési a bevezetési hibát okozó.
3. A Reonboard használja a megfelelő munkaterület Azonosítóját és kulcsát a következő témakör korábbi szakaszában a telepítési utasításokat.

### <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Probléma: Egy 500 és 404-es hiba látható a naplófájlban bevezetése után
Ez a hiba nem egy ismert probléma, amely az OMS-munkaterület Linux adatok első feltöltéskor. Ez a hiba nem befolyásolja az elküldött és a szolgáltatás élmény adatok.

### <a name="issue-you-are-not-seeing-any-data-in-the-oms-portal"></a>Probléma: Nem Ön az OMS-portálon megadott adattal sem

#### <a name="probable-causes"></a>Lehetséges okok

- Bevezetés az OMS szolgáltatáshoz sikertelen volt
- Az OMS szolgáltatáshoz kapcsolat le van tiltva.
- OMS-ügynököt a Linux-adatok biztonsági mentése

#### <a name="resolutions"></a>Megoldások
1. Ellenőrizze, hogy bevezetése az OMS szolgáltatáshoz a következő fájl létezésének ellenőrzésével sikeres volt:`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. Reonboard használatával a `omsadmin.sh` parancssori utasításokat
3. Ha proxyt használ, tekintse meg a korábban megadott proxy megoldási lépések.
4. Egyes esetekben amikor Linux az OMS-ügynök nem tud kommunikálni az OMS szolgáltatással adatai az ügynökön várólistára van állítva 50 MB teljes puffer mérete. A Linux OMS-ügynököt újra kell indítani a következő parancs futtatásával: `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`. 

    >[!NOTE]
    >Ez a probléma fennáll ügynök verziója 1.1.0-28 és újabb verziók.

