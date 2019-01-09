---
title: Az Azure Log Analytics Linux-ügynök hibaelhárítása |} A Microsoft Docs
description: A jelenség okok és a leggyakoribb problémák megoldási ismertetik a Linuxhoz készült Log Analytics-ügynök.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: magoedte
ms.openlocfilehash: b95a9b2243dda83be7de67802a8b6678419e335f
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54102584"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-linux"></a>A Linuxhoz készült Log Analytics-ügynök hibáinak elhárítása 

Ez a cikk ismerteti a hibaelhárítást hibákat tapasztalhat a Linuxhoz készült Log Analytics-ügynök, és azok megoldását lehetséges megoldásokat javasol.

Ha a fentiek egyikével sem működik, a következő támogatási csatornákat is elérhetők:

* Ügyfeleink a Premier szintű támogatási kedvezményekre megnyithat egy támogatási kérést az [Premier](https://premier.microsoft.com/).
* Az Azure támogatási szerződés rendelkező ügyfelek is nyisson egy támogatási kérést [az Azure Portalon](https://manage.windowsazure.com/?getsupport=true).
* Az OMI a következő problémák diagnosztizálása a [OMI – hibaelhárítási útmutató](https://github.com/Microsoft/omi/blob/master/Unix/doc/diagnose-omi-problems.md).
* Fájl egy [GitHub-problémát](https://github.com/Microsoft/OMS-Agent-for-Linux/issues).
* A Log Analytics visszajelzési oldalán tekintse át a beküldött ötletekre és hibák [ https://aka.ms/opinsightsfeedback ](https://aka.ms/opinsightsfeedback) vagy egy új fájlt.  

## <a name="important-log-locations-and-log-collector-tool"></a>Fontos a naplók helye és a naplógyűjtő eszköz

 Fájl | Útvonal
 ---- | -----
 Log Analytics-ügynök Linux naplófájl | `/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log `
 Log Analytics az ügynök konfigurációs naplófájl | `/var/opt/microsoft/omsconfig/omsconfig.log`

 Azt javasoljuk, hogy a napló gyűjtő eszköz használatával lekérheti az fontos naplók hibaelhárítási vagy egy GitHub-problémát elküldése előtt. További információ az eszköz és futtatásának módjáról olvashat [Itt](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md).

## <a name="important-configuration-files"></a>Fontos konfigurációs fájlok

 Kategória | Fájl helye
 ----- | -----
 Rendszernapló | `/etc/syslog-ng/syslog-ng.conf` vagy `/etc/rsyslog.conf` vagy `/etc/rsyslog.d/95-omsagent.conf`
 Teljesítmény, Nagios, Zabbix, a Log Analytics-kimenet és az általános ügynök | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`
 További konfigurációk | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/*.conf`

 >[!NOTE]
 >Ha a gyűjtemény van konfigurálva a teljesítményszámlálókat és a Syslog konfigurációs fájlok szerkesztésével felülírja a [adatok menü Log Analytics speciális beállításai](../../azure-monitor/platform/agent-data-sources.md#configuring-data-sources) a munkaterület az Azure Portalon. Az ügynökök konfigurációja letiltásához tiltsa le az a Log Analytics **speciális beállítások** , vagy futtassa a következő ügynököt:  
> `sudo su omsagent -c /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable`

## <a name="installation-error-codes"></a>Telepítési hibakódok

| Hibakód | Jelentés |
| --- | --- |
| NOT_DEFINED | Nincsenek telepítve a szükséges függőséget, mert a auoms auditd beépülő modul nem lesz telepítve | Nem sikerült, auoms telepítés csomag auditd. |
| 2 | Érvénytelen paraméter a rendszerhéj-csomag számára biztosított. Futtatás `sudo sh ./omsagent-*.universal*.sh --help` használatra |
| 3 | Nincs lehetőség a rendszerhéj-csomag számára biztosított. Futtatás `sudo sh ./omsagent-*.universal*.sh --help` használatra. |
| 4 | Érvénytelen a csomag, írja be vagy érvénytelen proxybeállítások; omsagent -*rpm*.sh csomagok csak telepíthető az RPM-alapú rendszereken, és omsagent -*deb*.sh csomagok csak telepíthető Debian-alapú rendszereken. Az univerzális telepítővel az ajánlott a [legújabb kiadása](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux). Emellett [tekintse át](#issue:-unable-to-connect-through-proxy-to-log-analytics) , ellenőrizze a beállításokat. |
| 5 | A rendszerhéj-csomagot kell végrehajtani a legfelső szintű vagy 403-as hibát adott vissza az előkészítés során. A parancs használatával futtassa `sudo`. |
| 6 | Érvénytelen architektúra csomag vagy; bevezetés során visszaadott hiba 200-as hiba omsagent -*x64.sh csomagok csak a 64 bites rendszerek és omsagent - telepíthető*x86.sh csomagok csak 32 bites rendszerekre telepíthető. Töltse le az architektúra a megfelelő csomagot a [legújabb kiadása](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/latest). |
| 17 | OMS-csomag telepítése nem sikerült. Nézze át a parancs kimenete a legfelső szintű hiba. |
| 19 | OMI csomag telepítése nem sikerült. Nézze át a parancs kimenete a legfelső szintű hiba. |
| 20 | Az SCX-csomag telepítése nem sikerült. Nézze át a parancs kimenete a legfelső szintű hiba. |
| 21 | Szolgáltató kits telepítése nem sikerült. Nézze át a parancs kimenete a legfelső szintű hiba. |
| 22 | Csomagolt csomag telepítése nem sikerült. Nézze át a legfelső szintű hiba a parancs kimenete |
| 23 | Az SCX vagy OMI a csomag már telepítve van. Használat `--upgrade` helyett `--install` a rendszerhéj-csomag telepítéséhez. |
| 30 | A csomag belső hiba történt. Fájl egy [GitHub-problémát](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) kimenetből származó adatokkal. |
| 55 | Nem támogatott openssl-verziót, vagy nem csatlakozhat a Log Analytics szolgáltatás, vagy dpkg zárolt vagy hiányzik a curl program. |
| 61 | Hiányzó Python ctypes könyvtár. Telepítse a Python ctypes függvénytár vagy csomag (python-ctypes). |
| 62 | Hiányzó tar program, telepítés tar. |
| 63 | Hiányzó csökkentésének programot, a telepítés sed. |
| 64 | Hiányzik a curl program, telepítse a curl. |
| 65 | Hiányzó gpg program, telepítés gpg. |

## <a name="onboarding-error-codes"></a>Bevezetési hibakódok

| Hibakód | Jelentés |
| --- | --- |
| 2 | Érvénytelen paraméter a omsadmin parancsfájl számára megadott. Futtatás `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` használatra. |
| 3 | Érvénytelen konfiguráció omsadmin parancsfájl a megadott. Futtatás `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` használatra. |
| 4 | Érvénytelen proxy omsadmin parancsfájl a megadott. Ellenőrizze a proxy, és tekintse meg a [HTTP proxyk használatára vonatkozó dokumentáció](log-analytics-agent.md#network-firewall-requirements). |
| 5 | 403-as HTTP-hiba érkezett a Log Analytics szolgáltatásnak. A részletekért omsadmin parancsfájl teljes kimenet megtekintéséhez. |
| 6 | A Log Analytics szolgáltatásból származó nem – 200-as HTTP-hibát. A részletekért omsadmin parancsfájl teljes kimenet megtekintéséhez. |
| 7 | Nem lehet kapcsolódni a Log Analytics szolgáltatásnak. A részletekért omsadmin parancsfájl teljes kimenet megtekintéséhez. |
| 8 | Hiba történt a bevezetési Log Analytics-munkaterülethez. A részletekért omsadmin parancsfájl teljes kimenet megtekintéséhez. |
| 30 | Belső hiba a parancsfájlt. Fájl egy [GitHub-problémát](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) kimenetből származó adatokkal. |
| 31 | Hiba történt az ügynök létrehozni azonosítóját. Fájl egy [GitHub-problémát](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) kimenetből származó adatokkal. |
| 32 | Hiba történt a tanúsítványok létrehozásához. A részletekért omsadmin parancsfájl teljes kimenet megtekintéséhez. |
| 33 | Hiba történt a omsconfig metaconfiguration létrehozása. Fájl egy [GitHub-problémát](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) kimenetből származó adatokkal. |
| 34 | Metaconfiguration generációs parancsprogram nem található. Ismételje meg a bevezetési `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key>`. |

## <a name="enable-debug-logging"></a>A hibakeresési naplózást engedélyező
### <a name="oms-output-plugin-debug"></a>OMS kimeneti beépülő modul hibakeresése
 FluentD beépülő modul-specifikus naplózási szintek így adja meg a bemenetek és kimenetek különböző naplózási szintek lehetővé teszi. OMS-kimenet egy másik naplózási szint megadásához, az általános ügynökkonfiguráció szerkesztése `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`.  

 Az OMS kimeneti beépülő modul a konfigurációs fájl vége előtt módosítsa a `log_level` tulajdonságot `info` való `debug`:

 ```
 <match oms.** docker.**>
  type out_oms
  log_level debug
  num_threads 5
  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
 ```

A hibakeresési naplózás lehetővé teszi a Log Analytics szolgáltatás elválasztva írja be, az elemeket, és a Küldés ideje számát kötegelt feltöltése:

*A példában engedélyezve hibakeresési napló:*

```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

### <a name="verbose-output"></a>Részletes kimenet
Az OMS kimeneti beépülő modul használata helyett is készíthető adatelemek közvetlenül `stdout`, vagyis a Log Analytics-ügynök Linux naplófájl látható.

A Log Analytics általános ügynök konfigurációs fájlban a következő `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`, tegye megjegyzésbe az OMS kimeneti beépülő modul hozzáadásával egy `#` minden sor elé:

```
#<match oms.** docker.**>
#  type out_oms
#  log_level info
#  num_threads 5
#  buffer_chunk_limit 5m
#  buffer_type file
#  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
#  buffer_queue_limit 10
#  flush_interval 20s
#  retry_limit 10
#  retry_wait 30s
#</match>
```

Alább a kimeneti beépülő modult, állítsa vissza a következő szakasz eltávolításával a `#` minden sor elé:

```
<match **>
  type stdout
</match>
```

## <a name="issue-unable-to-connect-through-proxy-to-log-analytics"></a>Probléma: Nem lehet kapcsolódni a Log Analytics-proxy használatával

### <a name="probable-causes"></a>Lehetséges okok
* A proxy, az előkészítés során megadott helytelen volt.
* A Log Analytics és Azure Automation szolgáltatás végpontok nem érhetők szerepel az engedélyezési listán a helyi adatközpontban 

### <a name="resolution"></a>Megoldás:
1. A Log Analytics szolgáltatás a Log Analytics-ügynökkel rendelkező Linux rendszeren a beállítással a következő parancs használatával Reonboard `-v` engedélyezve van. Az ügynök csatlakoztatása a Log Analytics szolgáltatáshoz a proxyn keresztül részletes kimenet lehetővé teszi. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key> -p <Proxy Conf> -v`

2. Tekintse át a szakasz [proxy beállításainak frissítése](agent-manage.md#update-proxy-settings) ellenőrizze, hogy megfelelően konfigurálta az ügynök közötti kommunikációhoz egy proxykiszolgálón keresztül.    
* Ellenőrizze, hogy a következő Log Analytics-végpontok-e az engedélyezési listán:

    |Ügynök erőforrása| Portok | Irány |
    |------|---------|----------|  
    |*.ods.opinsights.azure.com | 443-as port| Bejövő és kimenő |  
    |*.oms.opinsights.azure.com | 443-as port| Bejövő és kimenő |  
    |*.blob.core.windows.net | 443-as port| Bejövő és kimenő |  
    |*.azure-automation.net | 443-as port| Bejövő és kimenő | 

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Probléma: A 403-as hibaüzenetet kapja, amikor megpróbálja üzembe helyezni

### <a name="probable-causes"></a>Lehetséges okok
* Dátum és idő nem megfelelő Linux-kiszolgálón 
* Munkaterület-Azonosítót és a használt munkaterület kulcsa nem megfelelőek

### <a name="resolution"></a>Megoldás:

1. Ellenőrizze az idő a Linux-kiszolgálón, a parancs dátummal. Ha az idő +/-aktuális időtől számítva 15 percig, majd előkészítés sikertelen lesz. Megfelelő ez frissítse a dátumot és/vagy a Linux-kiszolgáló időzónáját. 
2. Ellenőrizze, hogy telepítette a Linuxhoz készült Log Analytics-ügynök legújabb verzióját.  A legújabb verzióra most már értesítést küld, ha időeltérési a bevezetési hibát okozó.
3. Használja a megfelelő munkaterület-Azonosítót és a cikk korábbi telepítési utasításai Munkaterületkulcsot Reonboard.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Probléma: Lásd: 500 és 404-es hiba történt a naplófájl a közvetlenül az előkészítés után
Ez az egy ismert probléma, amely akkor fordul elő, a Log Analytics-munkaterület az első Linux adatok feltöltése. Ez nem befolyásolja a adat rangsorát elküldött vagy service felhasználói élményt.

## <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>Probléma: Nem jelennek meg adatok az Azure Portalon

### <a name="probable-causes"></a>Lehetséges okok

- A Log Analytics szolgáltatás üzembe helyezése nem sikerült
- A Log Analytics szolgáltatással létesített kapcsolat le van tiltva.
- Log Analytics-ügynököket a Linux-adatok biztonsági mentése

### <a name="resolution"></a>Megoldás:
1. Ellenőrizze, hogy ha bevezetése a Log Analytics szolgáltatás sikeres volt-e a következő fájl létezik: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. Reonboard használatával a `omsadmin.sh` parancssori utasításokat
3. Ha proxyt használ, tekintse meg a korábban megadott proxy megoldási lépések.
4. Bizonyos esetekben amikor a Linuxhoz készült Log Analytics-ügynök nem tud kommunikálni a szolgáltatás adatai az ügynökön várólistára van állítva a teljes puffert méretét, amely 50 MB-ot. Az ügynök újra kell indítani a következő parancs futtatásával: `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`. 

    >[!NOTE]
    >Az ügynök verziója 1.1.0-28 és újabb verzióiban ez a problémát megoldottuk.


## <a name="issue-you-are-not-seeing-forwarded-syslog-messages"></a>Probléma: Nem jelennek meg a továbbított Syslog-üzeneteket 

### <a name="probable-causes"></a>Lehetséges okok
* A Linux-kiszolgálón a alkalmazni a konfigurációt nem teszi lehetővé az elküldött létesítmények és/vagy a naplózási szintek gyűjteménye
* Syslog nem lesznek továbbítva megfelelően a a Linux-kiszolgálón
* A másodpercenként továbbított üzenetek száma túl nagyok, a Log Analytics-ügynököket kezelése Linux alap konfigurációjának

### <a name="resolution"></a>Megoldás:
* Ellenőrizze a konfiguráció a Log Analytics-munkaterületen a Syslog rendelkezik minden eszközt és a megfelelő naplózási szintek. Felülvizsgálat [rendszernaplók gyűjtése konfigurálása az Azure Portalon](../../azure-monitor/platform/data-sources-syslog.md#configure-syslog-in-the-azure-portal)
* Ellenőrizze, üzenetkezelés démonok a natív syslog (`rsyslog`, `syslog-ng`) sikerült továbbított üzenetek fogadásához
* Ellenőrizze a tűzfalbeállításokat a Syslog-kiszolgálón, győződjön meg arról, hogy üzeneteket nem akadályozza
* A Syslog-üzenet a Log Analytics használatával szimulálása `logger` parancs
  * `logger -p local0.err "This is my test message"`

## <a name="issue-you-are-receiving-errno-address-already-in-use-in-omsagent-log-file"></a>Probléma: Azért küldtük Önnek, Errno cím már használatban lévő omsagent naplófájl
Ha látja `[error]: unexpected error error_class=Errno::EADDRINUSE error=#<Errno::EADDRINUSE: Address already in use - bind(2) for "127.0.0.1" port 25224>` omsagent.log a.

### <a name="probable-causes"></a>Lehetséges okok
Ez a hiba azt jelzi, hogy a Linux diagnosztikai bővítmény (LAD) párhuzamosan lesz a Log Analytics Linux Virtuálisgép-bővítmény telepítve van, és a syslog-gyűjtemény, omsagent ugyanazt a portot használja.

### <a name="resolution"></a>Megoldás:
1. Gyökér szintű hajtsa végre a következő parancsokat (25224 példaként, lehetséges, hogy a környezetben látja LAD által használt port számát. megjegyzés):

    ```
    /opt/microsoft/omsagent/bin/configure_syslog.sh configure LAD 25229

    sed -i -e 's/25224/25229/' /etc/opt/microsoft/omsagent/LAD/conf/omsagent.d/syslog.conf
    ```

    Ezután kell szerkesztenie a megfelelő `rsyslogd` vagy `syslog_ng` konfigurációs fájlt, és módosítsa a port 25229 írni a LAD kapcsolatos konfigurációját.

2. Ha a virtuális gép fut-e `rsyslogd`, a fájl a módosítandó: `/etc/rsyslog.d/95-omsagent.conf` (ha létezik, máskülönben `/etc/rsyslog`). Ha a virtuális gép fut-e `syslog_ng`, a fájl a módosítandó: `/etc/syslog-ng/syslog-ng.conf`.
3. Indítsa újra a omsagent `sudo /opt/microsoft/omsagent/bin/service_control restart`.
4. Indítsa újra a syslog-szolgáltatást.

## <a name="issue-you-are-unable-to-uninstall-omsagent-using-purge-option"></a>Probléma: Nem végleges törlése lehetőséggel omsagent eltávolítása

### <a name="probable-causes"></a>Lehetséges okok

* Linux diagnosztikai bővítmény telepítve van
* Linux diagnosztikai bővítmény telepítése és eltávolítása, de továbbra sem mdsd által használt omsagent kapcsolatos hibát, és nem távolítható el.

### <a name="resolution"></a>Megoldás:
1. Távolítsa el a Linux diagnosztikai bővítmény (LAD).
2. Linux diagnosztikai bővítmény fájlok eltávolítása a számítógépről, ha ezek meg adva, a következő helyen: `/var/lib/waagent/Microsoft.Azure.Diagnostics.LinuxDiagnostic-<version>/` és `/var/opt/microsoft/omsagent/LAD/`.

## <a name="issue-you-cannot-see-data-any-nagios-data"></a>Probléma: Meg nem jelennek meg adatok Nagios adatok 

### <a name="probable-causes"></a>Lehetséges okok
* Omsagent felhasználó nem rendelkezik engedélyekkel Nagios log fájl
* Nagios-forrás- és a szűrő nem lettek uncommented omsagent.conf fájlból

### <a name="resolution"></a>Megoldás:
1. Adja hozzá a következő Nagios-fájlból olvassa omsagent felhasználót [utasításokat](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts).
2. A Log Analytics-ügynök a Linux általános konfigurációs fájlban a következő `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`, ügyeljen arra, hogy **mindkét** a Nagios forrás- és szűrő uncommented.

    ```
    <source>
      type tail
      path /var/log/nagios/nagios.log
      format none
      tag oms.nagios
    </source>

    <filter oms.nagios>
      type filter_nagios_log
    </filter>
    ```

## <a name="issue-you-are-not-seeing-any-linux-data"></a>Probléma: Nem jelennek meg minden olyan Linux-adatok 

### <a name="probable-causes"></a>Lehetséges okok
* A Log Analytics szolgáltatás üzembe helyezése nem sikerült
* A Log Analytics szolgáltatással való kapcsolat le van tiltva.
* Virtuális gép újra lett indítva.
* OMI csomagot manuálisan frissített egy újabb verzióra, mi telepítette a Log Analytics-ügynököket a Linux-csomag képest
* DSC-erőforrás naplói *osztály nem található* hiba történt a `omsconfig.log` naplófájl
* Log Analytics-ügynököket az adatok biztonsági mentése
* DSC-naplók *jelenlegi konfigurációja nem létezik. Hajtsa végre a kezdő-DscConfiguration parancsot a - Path paraméterrel adja meg a konfigurációs fájlt, és először hozzon létre egy aktuális konfigurációt.* a `omsconfig.log` kapcsolatos létezik naplófájl, de nincs naplófájlüzenetre `PerformRequiredConfigurationChecks` műveleteket.

### <a name="resolution"></a>Megoldás:
1. Telepítse az összes függőségét, például auditd csomagot.
2. Ellenőrizze, hogy a Log Analytics szolgáltatás üzembe helyezése a következő fájl létezik-e sikeres volt: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`.  Ha nem, a omsadmin.sh parancssorral reonboard [utasításokat](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line).
4. Ha proxyt használ, ellenőrizze a proxy hibaelhárítási lépéseket.
5. Egyes Azure terjesztési rendszerekben omid OMI kiszolgáló démon nem indul el a virtuális gép újraindítása után. Nem jelennek meg a naplózási, Change Tracking vagy UpdateManagement megoldás kapcsolatos adatokat eredményez. A megoldás az, hogy manuálisan indítsa el az omi-kiszolgáló futtatásával `sudo /opt/omi/bin/service_control restart`.
6. OMI csomag manuális frissítése újabb verzióra, után rendelkezik Log Analytics-ügynököket, hogy tovább működjenek, manuálisan újra kell indítani. Ebben a lépésben szükség néhány disztribúciókhoz ahol OMI-kiszolgáló nem indul el automatikusan az átalakítás után. Futtatás `sudo /opt/omi/bin/service_control restart` OMI újraindítására.
7. Ha látja a DSC-erőforrás *osztály nem található* hiba omsconfig.log, futtassa a `sudo /opt/omi/bin/service_control restart`.
8. Bizonyos esetekben amikor a Linuxhoz készült Log Analytics-ügynököt a Log Analytics szolgáltatás nem tud kommunikálni az adatai az ügynökön biztonsági másolatot a a teljes puffer mérete: 50 MB-OT. Az ügynök újra kell indítani a következő parancs futtatásával `/opt/microsoft/omsagent/bin/service_control restart`.

    >[!NOTE]
    >Ez a probléma kijavítása ügynök verziója 1.1.0-28 vagy újabb verzió
    >

* Ha `omsconfig.log` naplófájl nem jelzi, hogy `PerformRequiredConfigurationChecks` művelet a rendszer rendszeres időközönként fut, előfordulhat, hogy egy cron feladat/szolgáltatási problémából. Ellenőrizze, hogy létezik cron feladat `/etc/cron.d/OMSConsistencyInvoker`. Ha szükséges, futtassa az alábbi parancsokat a cron feladat létrehozásához:

    ```
    mkdir -p /etc/cron.d/
    echo "*/15 * * * * omsagent /opt/omi/bin/OMSConsistencyInvoker >/dev/null 2>&1" | sudo tee /etc/cron.d/OMSConsistencyInvoker
    ```

    Ügyeljen arra, hogy fut a cron-szolgáltatás. Használhat `service cron status` Debian, Ubuntu, a SUSE, vagy `service crond status` RHEL, CentOS, Oracle Linux rendszerű, ez a szolgáltatás állapotának ellenőrzéséhez. Ha a szolgáltatás nem létezik, a bináris fájljainak telepítéséhez, és indítsa el a szolgáltatást az alábbiak használatával:

    **Ubuntu/Debian**

    ```
    # To Install the service binaries
    sudo apt-get install -y cron
    # To start the service
    sudo service cron start
    ```

    **SUSE**

    ```
    # To Install the service binaries
    sudo zypper in cron -y
    # To start the service
    sudo systemctl enable cron
    sudo systemctl start cron
    ```

    **RHEL/CeonOS**

    ```
    # To Install the service binaries
    sudo yum install -y crond
    # To start the service
    sudo service crond start
    ```

    **Oracle Linux**

    ```
    # To Install the service binaries
    sudo yum install -y cronie
    # To start the service
    sudo service crond start
    ```

## <a name="issue-when-configuring-collection-from-the-portal-for-syslog-or-linux-performance-counters-the-settings-are-not-applied"></a>Probléma: A portálon a Syslog- vagy Linux-teljesítményszámlálók gyűjtése konfigurálásakor a rendszer nem alkalmazza a beállításokat

### <a name="probable-causes"></a>Lehetséges okok
* A Linuxhoz készült Log Analytics-ügynök még nem követi a legújabb konfigurációt
* A rendszer nem alkalmazta a módosított beállítások a portálon

### <a name="resolution"></a>Megoldás:
**Háttér:** `omsconfig` az a Log Analytics-ügynök Linux konfigurációs ügynök, amely az új portal-oldali konfiguráció öt percenként keres. Ez a konfiguráció alkalmazva lesz a Log Analytics-ügynököket a Linux-konfigurációs fájlok /etc/opt/microsoft/omsagent/conf/omsagent.conf helyen található.

* Bizonyos esetekben a Log Analytics-ügynök Linux konfigurációja ügynökhöz nem tud kommunikálni a portál konfigurációs szolgáltatás nem alkalmazza, a legújabb konfigurációt eredményez.
  1. Ellenőrizze, hogy a `omsconfig` -ügynök telepítve van a futó `dpkg --list omsconfig` vagy `rpm -qi omsconfig`.  Ha nincs telepítve, telepítse újra a Linuxhoz készült Log Analytics-ügynök legújabb verzióját.

  2. Ellenőrizze, hogy a `omsconfig` ügynök kommunikálhatnak a Log Analytics szolgáltatással, a következő parancs futtatásával `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`. Ez a parancs visszaadja a konfiguráció az ügynök kap a szolgáltatástól, beleértve a Syslog beállításai, Linux-teljesítményszámlálók és egyéni naplókat. Ha ez a parancs sikertelen, futtassa a következő parancsot `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'`. Ez a parancs kikényszeríti a omsconfig ügynök kommunikáljon a Log Analytics szolgáltatásnak, és a legújabb konfigurációt lekéréséhez.

## <a name="issue-you-are-not-seeing-any-custom-log-data"></a>Probléma: Nem jelennek meg minden olyan egyéni naplózási adatok 

### <a name="probable-causes"></a>Lehetséges okok
* A Log Analytics szolgáltatás üzembe helyezése nem sikerült.
* A beállítás **saját Linux-kiszolgálókon alkalmazza az alábbi konfigurációját** nincs kiválasztva.
* omsconfig rendelkezik nem követi a legújabb egyéni naplózási konfigurációt a szolgáltatásból.
* Log Analytics-ügynököket Linux felhasználói `omsagent` nem fér hozzá az egyéni napló engedélyekkel kapcsolatos probléma vagy nem található.  Hibák a következők jelenhetnek meg:
 * `[DATETIME] [warn]: file not found. Continuing without tailing it.`
 * `[DATETIME] [error]: file not accessible by omsagent.`
* Ismert problémája javítva a Log Analytics-ügynököket a Linux-verzió 1.1.0-217 versenyhelyzet

### <a name="resolution"></a>Megoldás:
1. Ellenőrizze a következő fájl létezik-e a Log Analytics üzembe helyezése sikeres volt: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`. Ha nem, vagy:  

  1. A omsadmin.sh parancssorral Reonboard [utasításokat](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line).
  2. A **speciális beállítások** az Azure Portalon ellenőrizze, hogy a beállítás **saját Linux-kiszolgálókon alkalmazza az alábbi konfigurációját** engedélyezve van.  

2. Ellenőrizze, hogy a `omsconfig` ügynök kommunikálhatnak a Log Analytics szolgáltatással, a következő parancs futtatásával `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`.  Ez a parancs visszaadja a konfiguráció az ügynök kap a szolgáltatástól, beleértve a Syslog beállításai, Linux-teljesítményszámlálók és egyéni naplókat. Ha ez a parancs sikertelen, futtassa a következő parancsot `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py`. Ez a parancs kikényszeríti a omsconfig ügynök kommunikáljon a Log Analytics szolgáltatásnak, és a legújabb konfigurációt lekéréséhez.

**Háttér:** A Log Analytics-ügynök Linux futtatásához emelt szintű felhasználói - helyett `root`, az ügynök fut, a `omsagent` felhasználói. A legtöbb esetben kifejezett engedélyt kell adni a felhasználónak ahhoz, hogy bizonyos fájlokat kell olvasni. Hogy adjon engedélyt `omsagent` felhasználói, futtassa a következő parancsokat:

1. Adja hozzá a `omsagent` felhasználó adott csoportra `sudo usermod -a -G <GROUPNAME> <USERNAME>`
2. Univerzális olvasási hozzáférést biztosít a szükséges fájl `sudo chmod -R ugo+rx <FILE DIRECTORY>`

Nincs Linux-verzió 1.1.0-217-nél korábbi Log Analytics-ügynökkel rendelkező versenyhelyzet ismert problémái. A legújabb ügynök frissítését követően futtassa a következő parancsot, a kimeneti beépülő modul legújabb verziójának beszerzéséhez `sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`.

## <a name="issue-you-are-trying-to-reonboard-to-a-new-workspace"></a>Probléma: Egy új munkaterületet reonboard kívánt
Amikor egy új munkaterületet ügynököt próbál reonboard, a Log Analytics-ügynök konfigurációjának reonboarding előtt törölni kell. Régi konfiguráció az ügynök karbantartása, a rendszerhéj-csomagot a futtatja `--purge`

```
sudo sh ./omsagent-*.universal.x64.sh --purge
```
Vagy

```
sudo sh ./onboard_agent.sh --purge
```

Reonboard használata után továbbra is a `--purge` lehetőség

## <a name="log-analytics-agent-extension-in-the-azure-portal-is-marked-with-a-failed-state-provisioning-failed"></a>Log Analytics-ügynök bővítmény az Azure Portalon a hibás állapotban van megjelölve: Kiépítés sikertelen

### <a name="probable-causes"></a>Lehetséges okok
* Log Analytics-ügynököket el lett távolítva az operációs rendszer
* Log Analytics-ügynök szolgáltatás nem működik, le van tiltva, vagy nincs konfigurálva

### <a name="resolution"></a>Megoldás: 
Hajtsa végre az alábbi lépéseket a probléma.
1. Távolítsa el a bővítményt az Azure Portalról.
2. Telepítse az ügynököt a következő a [utasításokat](../../azure-monitor/learn/quick-collect-linux-computer.md).
3. Indítsa újra az ügynököt a következő parancs futtatásával: `sudo /opt/microsoft/omsagent/bin/service_control restart`.
* Várjon néhány percet, és a kiépítési állapot **sikeres kiépítés**.


## <a name="issue-the-log-analytics-agent-upgrade-on-demand"></a>Probléma: A Log Analytics az ügynök frissítési igény szerinti

### <a name="probable-causes"></a>Lehetséges okok

A Log Analytics ügynökcsomagokat telepíti a gazdagépen elavultak.

### <a name="resolution"></a>Megoldás: 
Hajtsa végre az alábbi lépéseket a probléma.

1. Ellenőrizheti a legújabb kiadás [oldal](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/).
2. Töltse le a telepítési szkriptet (mint például 1.4.2-124):

    ```
    wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.2-124/omsagent-1.4.2-124.universal.x64.sh
    ```

3. Verziófrissítő csomagjai végrehajtásával `sudo sh ./omsagent-*.universal.x64.sh --upgrade`.
