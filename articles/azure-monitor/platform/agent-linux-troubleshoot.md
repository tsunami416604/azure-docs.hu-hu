---
title: Az Azure Log Analytics Linux-ügynök hibaelhárítása |} A Microsoft Docs
description: A Azure Monitor Log Analytics-ügynökével kapcsolatos leggyakoribb problémák tüneteinek, okainak és megoldásának leírása.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 11/21/2019
ms.openlocfilehash: ca0fcd3b68722d44fc285b2dff52b560c591d0be
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74306541"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-linux"></a>A Linuxhoz készült Log Analytics-ügynök hibáinak elhárítása 

Ez a cikk segítséget nyújt a Linux rendszerhez készült Log Analytics-ügynökkel kapcsolatos hibák elhárításához Azure Monitorban, és javaslatot tesz a lehetséges megoldásokra.

Ha a fentiek egyikével sem működik, a következő támogatási csatornákat is elérhetők:

* A Premier szintű támogatási csomaggal rendelkező ügyfelek a [Premier](https://premier.microsoft.com/)szintű támogatási kérést is megnyithatják.
* Az Azure-támogatási szerződéssel rendelkező ügyfelek támogatási kérelmet is megnyithatnak [a Azure Portal](https://manage.windowsazure.com/?getsupport=true).
* A kisegítő problémák diagnosztizálásával kapcsolatos [hibaelhárítási útmutatót](https://github.com/Microsoft/omi/blob/master/Unix/doc/diagnose-omi-problems.md).
* Fájl [GitHub-probléma](https://github.com/Microsoft/OMS-Agent-for-Linux/issues).
* Látogasson el a Log Analytics visszajelzési oldalra, ahol áttekintheti az elküldött ötleteket és hibákat [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback) vagy újat is megtudhat.  

## <a name="important-log-locations-and-log-collector-tool"></a>Fontos a naplók helye és a naplógyűjtő eszköz

 Fájl | Útvonal
 ---- | -----
 Log Analytics-ügynök Linux naplófájl | `/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
 Log Analytics az ügynök konfigurációs naplófájl | `/var/opt/microsoft/omsconfig/omsconfig.log`

 Azt javasoljuk, hogy a napló gyűjtő eszköz használatával lekérheti az fontos naplók hibaelhárítási vagy egy GitHub-problémát elküldése előtt. Az eszközről és annak futtatásáról [itt](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)olvashat bővebben.

## <a name="important-configuration-files"></a>Fontos konfigurációs fájlok

 Kategória | Fájl helye
 ----- | -----
 Rendszernapló | `/etc/syslog-ng/syslog-ng.conf` vagy `/etc/rsyslog.conf` vagy `/etc/rsyslog.d/95-omsagent.conf`
 Teljesítmény, Nagios, Zabbix, a Log Analytics-kimenet és az általános ügynök | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`
 További beállítások | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/*.conf`

 >[!NOTE]
 >A teljesítményszámlálók és a syslog konfigurációs fájljainak szerkesztése felül van írva, ha a gyűjtemény az [adatok menüben log Analytics speciális beállítások](../../azure-monitor/platform/agent-data-sources.md#configuring-data-sources) elemre van állítva a munkaterület Azure Portal. Az összes ügynök konfigurációjának letiltásához tiltsa le a gyűjteményt Log Analytics **Speciális beállítások** közül, vagy egyetlen ügynöknél futtassa a következőt:  
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

## <a name="installation-error-codes"></a>Telepítési hibakódok

| Hibakód | Jelentés |
| --- | --- |
| NOT_DEFINED | Nincsenek telepítve a szükséges függőséget, mert a auoms auditd beépülő modul nem lesz telepítve | Nem sikerült, auoms telepítés csomag auditd. |
| 2 | Érvénytelen paraméter a rendszerhéj-csomag számára biztosított. `sudo sh ./omsagent-*.universal*.sh --help` futtatása a használathoz |
| 3 | Nincs lehetőség a rendszerhéj-csomag számára biztosított. `sudo sh ./omsagent-*.universal*.sh --help` futtatása a használathoz. |
| 4 | Érvénytelen a csomag típusa vagy a proxybeállítások érvénytelenek; omsagent –*rpm*. sh csomagok csak rpm-alapú rendszereken telepíthetők, és a omsagent-*deb*. sh csomagok csak a Debian-alapú rendszereken telepíthetők. Javasoljuk, hogy a [legújabb kiadásban](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux)használja az univerzális telepítőt. Tekintse át a proxybeállítások ellenőrzését is. |
| 5 | A rendszerhéj-csomagot kell végrehajtani a legfelső szintű vagy 403-as hibát adott vissza az előkészítés során. Futtassa a parancsot a `sudo`használatával. |
| 6 | Érvénytelen a csomag architektúrája, vagy hiba történt a bevezetéskor visszaadott 200-es hiba miatt. a omsagent-*x64.sh csomagok csak 64 bites rendszerekre telepíthetők, és a omsagent-* x86.sh csomagok csak 32 bites rendszereken telepíthetők. Töltse le a megfelelő csomagot az architektúrához a [legújabb kiadásban](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/latest). |
| 17 | OMS-csomag telepítése nem sikerült. Nézze át a parancs kimenete a legfelső szintű hiba. |
| 19 | OMI csomag telepítése nem sikerült. Nézze át a parancs kimenete a legfelső szintű hiba. |
| 20 | Az SCX-csomag telepítése nem sikerült. Nézze át a parancs kimenete a legfelső szintű hiba. |
| 21 | Szolgáltató kits telepítése nem sikerült. Nézze át a parancs kimenete a legfelső szintű hiba. |
| 22 | Csomagolt csomag telepítése nem sikerült. Nézze át a legfelső szintű hiba a parancs kimenete |
| 23 | Az SCX vagy OMI a csomag már telepítve van. A rendszerhéj-csomag telepítéséhez `--install` helyett használja a `--upgrade`. |
| 30 | A csomag belső hiba történt. A kimenetből származó részleteket tartalmazó [GitHub-probléma](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) . |
| 55 | Nem támogatott OpenSSL-verzió, vagy nem lehet kapcsolódni Azure Monitor vagy a dpkg-hoz zárolt vagy hiányzó curl program. |
| 61 | Hiányzó Python ctypes könyvtár. Telepítse a Python ctypes függvénytár vagy csomag (python-ctypes). |
| 62 | Hiányzó tar program, telepítés tar. |
| 63 | Hiányzó csökkentésének programot, a telepítés sed. |
| 64 | Hiányzik a curl program, telepítse a curl. |
| 65 | Hiányzó gpg program, telepítés gpg. |

## <a name="onboarding-error-codes"></a>Bevezetési hibakódok

| Hibakód | Jelentés |
| --- | --- |
| 2 | Érvénytelen paraméter a omsadmin parancsfájl számára megadott. `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` futtatása a használathoz. |
| 3 | Érvénytelen konfiguráció omsadmin parancsfájl a megadott. `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` futtatása a használathoz. |
| 4 | Érvénytelen proxy omsadmin parancsfájl a megadott. Ellenőrizze a proxyt, és tekintse [meg a http-proxy használatára vonatkozó dokumentációt](log-analytics-agent.md#network-firewall-requirements). |
| 5 | 403 HTTP-hiba érkezett a következőtől: Azure Monitor. A részletekért omsadmin parancsfájl teljes kimenet megtekintéséhez. |
| 6 | Nem 200 HTTP-hiba érkezett a következőtől: Azure Monitor. A részletekért omsadmin parancsfájl teljes kimenet megtekintéséhez. |
| 7 | Nem lehet csatlakozni a Azure Monitorhoz. A részletekért omsadmin parancsfájl teljes kimenet megtekintéséhez. |
| 8 | Hiba történt a bevezetési Log Analytics-munkaterülethez. A részletekért omsadmin parancsfájl teljes kimenet megtekintéséhez. |
| 30 | Belső hiba a parancsfájlt. A kimenetből származó részleteket tartalmazó [GitHub-probléma](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) . |
| 31 | Hiba történt az ügynök létrehozni azonosítóját. A kimenetből származó részleteket tartalmazó [GitHub-probléma](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) . |
| 32 | Hiba történt a tanúsítványok létrehozásához. A részletekért omsadmin parancsfájl teljes kimenet megtekintéséhez. |
| 33 | Hiba történt a omsconfig metaconfiguration létrehozása. A kimenetből származó részleteket tartalmazó [GitHub-probléma](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) . |
| 34 | Metaconfiguration generációs parancsprogram nem található. Próbálkozzon újra a bevezetéssel `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key>`ával. |

## <a name="enable-debug-logging"></a>A hibakeresési naplózást engedélyező
### <a name="oms-output-plugin-debug"></a>OMS kimeneti beépülő modul hibakeresése
 FluentD beépülő modul-specifikus naplózási szintek így adja meg a bemenetek és kimenetek különböző naplózási szintek lehetővé teszi. Ha másik naplózási szintet szeretne megadni a OMS kimenetéhez, szerkessze az általános ügynök konfigurációját a következő helyen: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`.  

 A OMS kimeneti beépülő modulban a konfigurációs fájl vége előtt módosítsa a `log_level` tulajdonságot `info`ról `debug`ra:

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

A hibakeresési naplózás lehetővé teszi a kötegelt feltöltések megtekintését Azure Monitor típus, az adatelemek száma és a küldési idő szerint elválasztva:

*Példa hibakeresést támogató naplóra:*

```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

### <a name="verbose-output"></a>Részletes kimenet
A OMS kimeneti beépülő modul használata helyett közvetlenül a `stdout`is kiküldheti az adatelemeket, amelyek láthatók a Log Analytics-ügynök Linux-naplófájlban.

Az Log Analytics általános ügynök konfigurációs fájljának `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`címen, az egyes sorok elé `#` hozzáadásával vegye fel a OMS kimeneti beépülő modult:

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

A kimeneti beépülő modul alatt törölje a következő szakasz megjegyzését az egyes sorok előtt található `#` eltávolításával:

```
<match **>
  type stdout
</match>
```

## <a name="issue--unable-to-connect-through-proxy-to-azure-monitor"></a>Probléma: nem lehet csatlakozni a proxyn keresztül Azure Monitor

### <a name="probable-causes"></a>Lehetséges okok
* A proxy, az előkészítés során megadott helytelen volt.
* Az adatközpontban nem szerepel a Azure Monitor és az Azure Automation szolgáltatás végpontja 

### <a name="resolution"></a>Megoldás:
1. A következő `-v` parancs futtatásával Azure Monitor a Linuxra Log Analytics-ügynökkel. Lehetővé teszi az ügynök részletes kimenetét, amely a proxyn keresztül csatlakozik a Azure Monitor. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key> -p <Proxy Conf> -v`

2. Tekintse át a [Proxybeállítások frissítése](agent-manage.md#update-proxy-settings) szakaszt, és ellenőrizze, hogy megfelelően konfigurálta-e az ügynököt egy proxykiszolgálón keresztül történő kommunikációhoz.    
* Ellenőrizze, hogy az alábbi Azure Monitor-végpontok engedélyezve vannak-e:

    |Ügynök erőforrása| Portok | Irány |
    |------|---------|----------|  
    |*.ods.opinsights.azure.com | 443-as port| Bejövő és kimenő |  
    |*.oms.opinsights.azure.com | 443-as port| Bejövő és kimenő |  
    |*.blob.core.windows.net | 443-as port| Bejövő és kimenő |  

    Ha azt tervezi, hogy a Azure Automation Hybrid Runbook Worker használatával csatlakozik az Automation szolgáltatáshoz, és regisztrálja az runbookok-vagy felügyeleti megoldásokat a környezetben, hozzá kell férnie a portszámhoz és a [hálózat konfigurálása a hibrid Runbook-feldolgozóhoz](../../automation/automation-hybrid-runbook-worker.md#network-planning)című témakörben leírt URL-címekhez. 

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Probléma: 403-as hibaüzenetet kap előkészítése tett kísérlet során

### <a name="probable-causes"></a>Lehetséges okok
* Dátum és idő nem megfelelő Linux-kiszolgálón 
* Munkaterület-Azonosítót és a használt munkaterület kulcsa nem megfelelőek

### <a name="resolution"></a>Megoldás:

1. Ellenőrizze az idő a Linux-kiszolgálón, a parancs dátummal. Ha az idő +/-aktuális időtől számítva 15 percig, majd előkészítés sikertelen lesz. Megfelelő ez frissítse a dátumot és/vagy a Linux-kiszolgáló időzónáját. 
2. Ellenőrizze, hogy telepítette a Linuxhoz készült Log Analytics-ügynök legújabb verzióját.  A legújabb verzióra most már értesítést küld, ha időeltérési a bevezetési hibát okozó.
3. Használja a megfelelő munkaterület-Azonosítót és a cikk korábbi telepítési utasításai Munkaterületkulcsot Reonboard.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Probléma: Egy 500 és 404-es hibaüzenetet látja a naplófájlban közvetlenül az előkészítés után
Ez az egy ismert probléma, amely akkor fordul elő, a Log Analytics-munkaterület az első Linux adatok feltöltése. Ez nem befolyásolja a adat rangsorát elküldött vagy service felhasználói élményt.


## <a name="issue-you-see-omiagent-using-100-cpu"></a>Probléma: a omiagent a 100%-os CPU használatával jelenik meg

### <a name="probable-causes"></a>Lehetséges okok
Az NSS-PEM-csomag [v 1.0.3 -5. el7](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html) -beli regressziója súlyos teljesítménnyel kapcsolatos problémát okozott, melyet a vörös hajú/CentOS 7. x disztribúcióban láttunk. A probléma további megismeréséhez tekintse meg a következő dokumentációt: hiba [1667121 teljesítmény-regresszió a libcurl-ben](https://bugzilla.redhat.com/show_bug.cgi?id=1667121).

A teljesítménnyel kapcsolatos hibák nem minden időben történnek, és nagyon nehéz reprodukálni őket. Ha ilyen problémát tapasztal a omiagent, használja a szkript omiHighCPUDiagnostics.sh, amely összegyűjti a omiagent verem-nyomkövetését, amikor egy bizonyos küszöbértéket meghalad.

1. A parancsfájl letöltése <br/>
`wget https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/tools/LogCollector/source/omiHighCPUDiagnostics.sh`

2. Diagnosztika futtatása 24 órán át 30%-os CPU-küszöbértékkel <br/>
`bash omiHighCPUDiagnostics.sh --runtime-in-min 1440 --cpu-threshold 30`

3. A hívási verem omiagent_trace fájlban lesz kiszámítva, ha sok curl és NSS függvény hívása jelenik meg, kövesse az alábbi megoldási lépéseket.

### <a name="resolution-step-by-step"></a>Megoldás (lépésről lépésre)

1. Frissítse az NSS-PEM csomagot a következőre: [v 1.0.3-5. el7_6.1](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html). <br/>
`sudo yum upgrade nss-pem`

2. Ha az NSS-PEM nem érhető el a frissítéshez (többnyire a CentOS-on történik), akkor a curl a 7.29.0-46 értékre vált. Ha tévedésben futtatja a "yum update" parancsot, akkor a curl a 7.29.0-51-re frissül, és a probléma újra megtörténik. <br/>
`sudo yum downgrade curl libcurl`

3. Újraindítási a (z): <br/>
`sudo scxadmin -restart`

## <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>Probléma: Nem jelennek meg adatok az Azure Portalon

### <a name="probable-causes"></a>Lehetséges okok

- A Azure Monitor bevezetése nem sikerült
- A Azure Monitorhoz való kapcsolódás le van tiltva
- Log Analytics-ügynököket a Linux-adatok biztonsági mentése

### <a name="resolution"></a>Megoldás:
1. Ellenőrizze, hogy sikeres volt-e a bevezetési Azure Monitor a következő fájl létezésének ellenőrzésével: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. A `omsadmin.sh` parancssori utasítások használatával
3. Ha proxyt használ, tekintse meg a korábban megadott proxy megoldási lépések.
4. Bizonyos esetekben amikor a Linuxhoz készült Log Analytics-ügynök nem tud kommunikálni a szolgáltatás adatai az ügynökön várólistára van állítva a teljes puffert méretét, amely 50 MB-ot. Az ügynököt újra kell indítani a következő parancs futtatásával: `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`. 

    >[!NOTE]
    >Az ügynök verziója 1.1.0-28 és újabb verzióiban ez a problémát megoldottuk.


## <a name="issue-you-are-not-seeing-forwarded-syslog-messages"></a>Probléma: Nem láthatóak a továbbított Syslog-üzeneteket 

### <a name="probable-causes"></a>Lehetséges okok
* A Linux-kiszolgálón a alkalmazni a konfigurációt nem teszi lehetővé az elküldött létesítmények és/vagy a naplózási szintek gyűjteménye
* Syslog nem lesznek továbbítva megfelelően a a Linux-kiszolgálón
* A másodpercenként továbbított üzenetek száma túl nagyok, a Log Analytics-ügynököket kezelése Linux alap konfigurációjának

### <a name="resolution"></a>Megoldás:
* Ellenőrizze a konfiguráció a Log Analytics-munkaterületen a Syslog rendelkezik minden eszközt és a megfelelő naplózási szintek. [A syslog-gyűjtemény konfigurálásának áttekintése a Azure Portal](../../azure-monitor/platform/data-sources-syslog.md#configure-syslog-in-the-azure-portal)
* Ellenőrizze, hogy a natív syslog-üzenetküldési démonok (`rsyslog`, `syslog-ng`) képesek-e fogadni a továbbított üzeneteket
* Ellenőrizze a tűzfalbeállításokat a Syslog-kiszolgálón, győződjön meg arról, hogy üzeneteket nem akadályozza
* Syslog-üzenet szimulálása Log Analytics `logger` parancs használatával
  * `logger -p local0.err "This is my test message"`

## <a name="issue-you-are-receiving-errno-address-already-in-use-in-omsagent-log-file"></a>Probléma: Azért küldtük Önnek, Errno cím már használatban lévő omsagent naplófájl
Ha `[error]: unexpected error error_class=Errno::EADDRINUSE error=#<Errno::EADDRINUSE: Address already in use - bind(2) for "127.0.0.1" port 25224>` jelenik meg a omsagent. log naplófájlban.

### <a name="probable-causes"></a>Lehetséges okok
Ez a hiba azt jelzi, hogy a Linux diagnosztikai bővítmény (LAD) párhuzamosan lesz a Log Analytics Linux Virtuálisgép-bővítmény telepítve van, és a syslog-gyűjtemény, omsagent ugyanazt a portot használja.

### <a name="resolution"></a>Megoldás:
1. Gyökér szintű hajtsa végre a következő parancsokat (25224 példaként, lehetséges, hogy a környezetben látja LAD által használt port számát. megjegyzés):

    ```
    /opt/microsoft/omsagent/bin/configure_syslog.sh configure LAD 25229

    sed -i -e 's/25224/25229/' /etc/opt/microsoft/omsagent/LAD/conf/omsagent.d/syslog.conf
    ```

    Ezután szerkesztenie kell a helyes `rsyslogd` vagy `syslog_ng` konfigurációs fájlt, és módosítania kell a LAD-vel kapcsolatos konfigurációt az 25229-as portra való íráshoz.

2. Ha a virtuális gép `rsyslogd`fut, a módosítandó fájl a következő lesz: `/etc/rsyslog.d/95-omsagent.conf` (ha létezik, más `/etc/rsyslog`). Ha a virtuális gép `syslog_ng`fut, a módosítandó fájl a következő: `/etc/syslog-ng/syslog-ng.conf`.
3. Indítsa újra a omsagent `sudo /opt/microsoft/omsagent/bin/service_control restart`.
4. Indítsa újra a syslog-szolgáltatást.

## <a name="issue-you-are-unable-to-uninstall-omsagent-using-purge-option"></a>Probléma: Nem tudja végleges törlése lehetőséggel omsagent eltávolítása

### <a name="probable-causes"></a>Lehetséges okok

* Linux diagnosztikai bővítmény telepítve van
* Linux diagnosztikai bővítmény telepítése és eltávolítása, de továbbra sem mdsd által használt omsagent kapcsolatos hibát, és nem távolítható el.

### <a name="resolution"></a>Megoldás:
1. Távolítsa el a Linux diagnosztikai bővítmény (LAD).
2. Távolítsa el a Linux diagnosztikai bővítmény fájljait a gépről, ha azok a következő helyen találhatók: `/var/lib/waagent/Microsoft.Azure.Diagnostics.LinuxDiagnostic-<version>/` és `/var/opt/microsoft/omsagent/LAD/`.

## <a name="issue-you-cannot-see-data-any-nagios-data"></a>Probléma: Nem látható adatok Nagios adatok 

### <a name="probable-causes"></a>Lehetséges okok
* Omsagent felhasználó nem rendelkezik engedélyekkel Nagios log fájl
* Nagios-forrás- és a szűrő nem lettek uncommented omsagent.conf fájlból

### <a name="resolution"></a>Megoldás:
1. Adja hozzá a omsagent-felhasználót a Nagios-fájl olvasásához a következő [utasítások](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts)követésével.
2. A Linux általános konfigurációs fájljának Log Analytics-ügynöke `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`címen ellenőrizze, **hogy a** Nagios forrása és a szűrő sincs-e kimondva.

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
* A Azure Monitor bevezetése nem sikerült
* A Azure Monitorhoz való kapcsolódás le van tiltva
* Virtuális gép újra lett indítva.
* OMI csomagot manuálisan frissített egy újabb verzióra, mi telepítette a Log Analytics-ügynököket a Linux-csomag képest
* A DSC-erőforrás naplófájljainak *osztálya nem található a következő* hiba miatt `omsconfig.log` naplófájlban:
* Log Analytics-ügynököket az adatok biztonsági mentése
* A DSC-naplók *jelenlegi konfigurációja nem létezik. Futtassa a Start-DscConfiguration parancsot a-Path paraméterrel egy konfigurációs fájl megadásához, és először hozzon létre egy aktuális konfigurációt.* `omsconfig.log` naplófájlban, de nem létezik naplófájl a `PerformRequiredConfigurationChecks` műveletekről.

### <a name="resolution"></a>Megoldás:
1. Telepítse az összes függőségét, például auditd csomagot.
2. Ellenőrizze, hogy sikerült-e bevezetést Azure Monitor a következő fájl létezésének ellenőrzésével: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`.  Ha nem, akkor a omsadmin.sh parancssori [utasítások](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)használatával.
4. Ha proxyt használ, ellenőrizze a proxy hibaelhárítási lépéseket.
5. Egyes Azure terjesztési rendszerekben omid OMI kiszolgáló démon nem indul el a virtuális gép újraindítása után. Nem jelennek meg a naplózási, Change Tracking vagy UpdateManagement megoldás kapcsolatos adatokat eredményez. A megkerülő megoldás, ha a `sudo /opt/omi/bin/service_control restart`futtatásával manuálisan szeretné elindítani a a következőt:.
6. OMI csomag manuális frissítése újabb verzióra, után rendelkezik Log Analytics-ügynököket, hogy tovább működjenek, manuálisan újra kell indítani. Ebben a lépésben szükség néhány disztribúciókhoz ahol OMI-kiszolgáló nem indul el automatikusan az átalakítás után. `sudo /opt/omi/bin/service_control restart` futtatásával indítsa újra a következőt:.
7. Ha a DSC-erőforrás *osztály nem található* a omsconfig. log naplófájlban, futtassa a `sudo /opt/omi/bin/service_control restart`.
8. Bizonyos esetekben, ha a Linux Log Analytics-ügynöke nem tud kommunikálni Azure Monitor, az ügynökön lévő adat a teljes puffer méretére lesz mentve: 50 MB. Az ügynököt újra kell indítani a következő parancs `/opt/microsoft/omsagent/bin/service_control restart`futtatásával.

    >[!NOTE]
    >Ez a probléma kijavítása ügynök verziója 1.1.0-28 vagy újabb verzió
    >

* Ha `omsconfig.log` naplófájl nem jelzi, hogy a rendszeren rendszeresen futnak `PerformRequiredConfigurationChecks` műveletek, a cron feladat/szolgáltatás problémája lehet. Győződjön meg arról, hogy a cron-feladatot a `/etc/cron.d/OMSConsistencyInvoker`alatt található. Ha szükséges, futtassa az alábbi parancsokat a cron feladat létrehozásához:

    ```
    mkdir -p /etc/cron.d/
    echo "*/15 * * * * omsagent /opt/omi/bin/OMSConsistencyInvoker >/dev/null 2>&1" | sudo tee /etc/cron.d/OMSConsistencyInvoker
    ```

    Ügyeljen arra, hogy fut a cron-szolgáltatás. A `service cron status`t a Debian, az Ubuntu, a SUSE vagy a `service crond status` RHEL, CentOS, Oracle Linux a szolgáltatás állapotának vizsgálatára használhatja. Ha a szolgáltatás nem létezik, a bináris fájljainak telepítéséhez, és indítsa el a szolgáltatást az alábbiak használatával:

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

## <a name="issue-when-configuring-collection-from-the-portal-for-syslog-or-linux-performance-counters-the-settings-are-not-applied"></a>Probléma: A portálon a Syslog- vagy Linux-teljesítményszámlálók gyűjtése konfigurálásakor a beállítások nem lesznek alkalmazva

### <a name="probable-causes"></a>Lehetséges okok
* A Linuxhoz készült Log Analytics-ügynök még nem követi a legújabb konfigurációt
* A rendszer nem alkalmazta a módosított beállítások a portálon

### <a name="resolution"></a>Megoldás:
**Háttér:** az `omsconfig` a Linux rendszerhez készült log Analytics-ügynök, amely öt percenként keresi az új portál-oldali konfigurációt. Ez a konfiguráció alkalmazva lesz a Log Analytics-ügynököket a Linux-konfigurációs fájlok /etc/opt/microsoft/omsagent/conf/omsagent.conf helyen található.

* Bizonyos esetekben a Log Analytics-ügynök Linux konfigurációja ügynökhöz nem tud kommunikálni a portál konfigurációs szolgáltatás nem alkalmazza, a legújabb konfigurációt eredményez.
  1. `dpkg --list omsconfig` vagy `rpm -qi omsconfig`futtatásával győződjön meg arról, hogy a `omsconfig` ügynök telepítve van.  Ha nincs telepítve, telepítse újra a Linuxhoz készült Log Analytics-ügynök legújabb verzióját.

  2. A következő parancs `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`futtatásával győződjön meg arról, hogy a `omsconfig` ügynök tud kommunikálni az Azure Monitorval. Ez a parancs visszaadja a konfiguráció az ügynök kap a szolgáltatástól, beleértve a Syslog beállításai, Linux-teljesítményszámlálók és egyéni naplókat. Ha a parancs végrehajtása sikertelen, futtassa a következő parancsot `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'`. Ez a parancs arra kényszeríti a omsconfig-ügynököt, hogy beszéljen Azure Monitor és beolvassa a legújabb konfigurációt.

## <a name="issue-you-are-not-seeing-any-custom-log-data"></a>Probléma: Nem jelennek meg minden olyan egyéni naplózási adatok 

### <a name="probable-causes"></a>Lehetséges okok
* A Azure Monitor bevezetése nem sikerült.
* A beállítás **a következő konfigurációt alkalmazza a Linux-kiszolgálókra,** nem lett kiválasztva.
* omsconfig rendelkezik nem követi a legújabb egyéni naplózási konfigurációt a szolgáltatásból.
* A Linux felhasználói `omsagent` Log Analytics ügynöke nem tud hozzáférni az egyéni naplóhoz engedélyek miatt vagy nem található.  Hibák a következők jelenhetnek meg:
 * `[DATETIME] [warn]: file not found. Continuing without tailing it.`
 * `[DATETIME] [error]: file not accessible by omsagent.`
* Ismert problémája javítva a Log Analytics-ügynököket a Linux-verzió 1.1.0-217 versenyhelyzet

### <a name="resolution"></a>Megoldás:
1. Ellenőrizze, hogy a bevezetési Azure Monitor sikeres volt-e a következő fájl létezésének ellenőrzésével: `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`. Ha nem, vagy:  

  1. A omsadmin.sh parancssori utasítások használatával történő [újratelepítésre](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line).
  2. A Azure Portal **Speciális beállítások** területén ellenőrizze, hogy engedélyezve van-e a **következő konfiguráció alkalmazása a Linux-kiszolgálókon** beállítás.  

2. A következő parancs `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`futtatásával győződjön meg arról, hogy a `omsconfig` ügynök tud kommunikálni az Azure Monitorval.  Ez a parancs visszaadja a konfiguráció az ügynök kap a szolgáltatástól, beleértve a Syslog beállításai, Linux-teljesítményszámlálók és egyéni naplókat. Ha a parancs végrehajtása sikertelen, futtassa a következő parancsot `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py`. Ez a parancs arra kényszeríti a omsconfig-ügynököt, hogy beszéljen Azure Monitor és beolvassa a legújabb konfigurációt.

**Háttér:** Ahelyett, hogy a Linux Log Analytics ügynöke rendszerjogosultságú felhasználói `root`ként fut, az ügynök `omsagent` felhasználóként fut. A legtöbb esetben kifejezett engedélyt kell adni a felhasználónak ahhoz, hogy bizonyos fájlokat kell olvasni. Ahhoz, hogy engedélyt adjon `omsagent` felhasználónak, futtassa a következő parancsokat:

1. Adja hozzá a `omsagent` felhasználót egy adott csoporthoz `sudo usermod -a -G <GROUPNAME> <USERNAME>`
2. Univerzális olvasási hozzáférés biztosítása a szükséges fájlhoz `sudo chmod -R ugo+rx <FILE DIRECTORY>`

Nincs Linux-verzió 1.1.0-217-nél korábbi Log Analytics-ügynökkel rendelkező versenyhelyzet ismert problémái. A legújabb ügynökre való frissítés után futtassa a következő parancsot a kimeneti beépülő modul `sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`legújabb verziójának lekéréséhez.

## <a name="issue-you-are-trying-to-reonboard-to-a-new-workspace"></a>A probléma leírása: Egy új munkaterületet reonboard próbált
Amikor egy új munkaterületet ügynököt próbál reonboard, a Log Analytics-ügynök konfigurációjának reonboarding előtt törölni kell. Az ügynöktől a régi konfiguráció törléséhez futtassa a rendszerhéj-csomagot `--purge`

```
sudo sh ./omsagent-*.universal.x64.sh --purge
```
Vagy

```
sudo sh ./onboard_agent.sh --purge
```

Az `--purge` lehetőség használata után folytathatja a bevezetést.

## <a name="log-analytics-agent-extension-in-the-azure-portal-is-marked-with-a-failed-state-provisioning-failed"></a>Log Analytics-ügynök bővítmény az Azure Portalon a hibás állapotban van megjelölve: nem sikerült létrehozni

### <a name="probable-causes"></a>Lehetséges okok
* Log Analytics-ügynököket el lett távolítva az operációs rendszer
* Log Analytics-ügynök szolgáltatás nem működik, le van tiltva, vagy nincs konfigurálva

### <a name="resolution"></a>Megoldás: 
Hajtsa végre az alábbi lépéseket a probléma.
1. Távolítsa el a bővítményt az Azure Portalról.
2. Telepítse az ügynököt az [utasításokat](../../azure-monitor/learn/quick-collect-linux-computer.md)követve.
3. Indítsa újra az ügynököt a következő parancs futtatásával: `sudo /opt/microsoft/omsagent/bin/service_control restart`.
* Várjon néhány percet, és a kiépítés állapota **sikeresen kiépítve**értékre változik.


## <a name="issue-the-log-analytics-agent-upgrade-on-demand"></a>Probléma: A Log Analytics-ügynökök frissítésének igény szerinti

### <a name="probable-causes"></a>Lehetséges okok

A Log Analytics ügynökcsomagokat telepíti a gazdagépen elavultak.

### <a name="resolution"></a>Megoldás: 
Hajtsa végre az alábbi lépéseket a probléma.

1. Keresse meg a [lapon](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/)a legújabb verziót.
2. Töltse le a telepítési szkriptet (mint például 1.4.2-124):

    ```
    wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.2-124/omsagent-1.4.2-124.universal.x64.sh
    ```

3. `sudo sh ./omsagent-*.universal.x64.sh --upgrade`futtatásával frissítse a csomagokat.
