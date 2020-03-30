---
title: Azure Log Analytics Linux-ügynök – problémamegoldás | Microsoft dokumentumok
description: Írja le a tüneteket, okokat és a megoldás a leggyakoribb problémák a Log Analytics ügynök Linux hoz az Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/21/2019
ms.openlocfilehash: 24aa3462aef4f719e93d17389ff342084f6c7864
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668757"
---
# <a name="how-to-troubleshoot-issues-with-the-log-analytics-agent-for-linux"></a>A Linuxhoz készült Log Analytics-ügynökkel kapcsolatos hibák elhárítása 

Ez a cikk segítséget nyújt a Linux-naplóhasználatával kapcsolatos hibák elhárításához az Azure Monitorban, és lehetséges megoldásokat javasol azok megoldására.

Ha a lépések egyike sem működik, a következő támogatási csatornák is elérhetők:

* A Premier támogatási előnyökkel rendelkező ügyfelek támogatási kérelmet nyithatnak a [Premier.](https://premier.microsoft.com/)
* Az Azure-támogatási szerződésekkel rendelkező ügyfelek támogatási kérelmet nyithatnak [az Azure Portalon.](https://manage.windowsazure.com/?getsupport=true)
* Diagnosztizálja az OMI-problémákat az [OMI hibaelhárítási útmutatóval.](https://github.com/Microsoft/omi/blob/master/Unix/doc/diagnose-omi-problems.md)
* A [GitHub-probléma fájlja](https://github.com/Microsoft/OMS-Agent-for-Linux/issues).
* Látogasson el a Log Analytics visszajelzési [https://aka.ms/opinsightsfeedback](https://aka.ms/opinsightsfeedback) oldalra a beküldött ötletek és hibák áttekintéséhez, vagy nyújtson be egy újat.  

## <a name="important-log-locations-and-log-collector-tool"></a>Fontos naplóhelyek és naplógyűjtő eszköz

 Fájl | Útvonal
 ---- | -----
 Log Analytics ügynök Linux naplófájlhoz | `/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`
 Log Analytics-ügynök konfigurációs naplófájlja | `/var/opt/microsoft/omsconfig/omsconfig.log`

 Javasoljuk, hogy a naplógyűjtő eszközünksegítségével olvassa be a hibák elhárításához szükséges fontos naplókat, vagy mielőtt github-problémát küldene. Elolvashatja többet az eszközt, és hogyan kell futtatni [itt](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md).

## <a name="important-configuration-files"></a>Fontos konfigurációs fájlok

 Kategória | Fájl helye
 ----- | -----
 Rendszernapló | `/etc/syslog-ng/syslog-ng.conf`vagy `/etc/rsyslog.conf` vagy`/etc/rsyslog.d/95-omsagent.conf`
 Teljesítmény, Nagios, Zabbix, Log Analytics kimenet és általános ügynök | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`
 További konfigurációk | `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.d/*.conf`

 >[!NOTE]
 >A teljesítményszámlálók és a Syslog konfigurációs fájljainak szerkesztése felülíródik, ha a gyűjtemény az Azure Portalon az Azure Portalon a munkaterület [adatmenüjében](../../azure-monitor/platform/agent-data-sources.md#configuring-data-sources) konfigurálva van. Az összes ügyintéző konfigurációjának letiltásához tiltsa le a gyűjteményt a Log Analytics **speciális beállításaiból,** vagy egyetlen ügynök esetén futtassa a következőket:  
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

## <a name="installation-error-codes"></a>Telepítési hibakódok

| Hibakód | Jelentés |
| --- | --- |
| NOT_DEFINED | Mivel a szükséges függőségek nincsenek telepítve, az auoms auditd plugin nem lesz telepítve | Az auoms telepítése nem sikerült, a csomag telepítése auditálva. |
| 2 | Érvénytelen beállítás a rendszerhéj-kötegszámára. Futtatás `sudo sh ./omsagent-*.universal*.sh --help` használathoz |
| 3 | Nincs lehetőség a shell csomagban. Futtatás `sudo sh ./omsagent-*.universal*.sh --help` a használatra. |
| 4 | Érvénytelen vagy érvénytelen proxybeállítások; az omsagent-*rpm*.sh csomagok csak RPM-alapú rendszerekre telepíthetők, az omsagent-*deb*.sh csomagok pedig csak Debian alapú rendszerekre telepíthetők. Javasoljuk, hogy használja az univerzális telepítőt a [legújabb kiadásból.](../../azure-monitor/learn/quick-collect-linux-computer.md#install-the-agent-for-linux) Is áttekinti, hogy ellenőrizze a proxy beállításait. |
| 5 | A rendszerhéj-köteget gyökérként kell végrehajtani, VAGY 403-as hibát adott vissza a bevezetés során. Futtassa `sudo`a parancsot a használatával. |
| 6 | Érvénytelen csomagarchitektúra VAGY 200-as hiba történt a bevezetés során; az omsagent-x64.sh*csomagok csak 64 bites rendszerekre telepíthetők, az omsagent-x86.sh*csomagok pedig csak 32 bites rendszerekre telepíthetők. Töltse le a megfelelő csomagot az architektúrához a [legújabb kiadásból.](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/latest) |
| 17 | Az OMS-csomag telepítése nem sikerült. Tekintse át a parancs kimenetét a gyökérhiba. |
| 19 | Az OMI-csomag telepítése nem sikerült. Tekintse át a parancs kimenetét a gyökérhiba. |
| 20 | Az SCX csomag telepítése nem sikerült. Tekintse át a parancs kimenetét a gyökérhiba. |
| 21 | A szolgáltatókészletek telepítése nem sikerült. Tekintse át a parancs kimenetét a gyökérhiba. |
| 22 | A csomagban lévő csomag telepítése nem sikerült. Tekintse át a parancs kimenetét a gyökérhiba |
| 23 | Az SCX vagy OMI csomag már telepítve van. Használja `--upgrade` ahelyett, `--install` hogy telepítse a shell csomagot. |
| 30 | Belső köteghiba. A [GitHub-probléma](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) fájl a kimenet részleteivel. |
| 55 | Nem támogatott openssl verzió vagy nem tud csatlakozni az Azure Monitor vagy dpkg zárolt vagy hiányzó curl program. |
| 61 | Hiányzó Python ctypes függvénytár. Telepítse a Python ctypes könyvtárat vagy csomagot (python-ctypes). |
| 62 | Hiányzó kátrány program, telepítse kátrány. |
| 63 | Hiányzó sed program, telepítse sed. |
| 64 | Hiányzó göndör program, telepítse göndör. |
| 65 | Hiányzó gpg program, telepítse gpg. |

## <a name="onboarding-error-codes"></a>Bevezetési hibakódok

| Hibakód | Jelentés |
| --- | --- |
| 2 | Érvénytelen beállítás az omsadmin parancsfájlhoz. Futtatás `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` a használatra. |
| 3 | Érvénytelen konfiguráció az omsadmin parancsfájlszámára. Futtatás `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -h` a használatra. |
| 4 | Érvénytelen proxy az omsadmin parancsfájlhoz. Ellenőrizze a proxyt, és tekintse meg [a HTTP-proxy használatának dokumentációját.](log-analytics-agent.md#network-firewall-requirements) |
| 5 | 403-as HTTP-hiba érkezett az Azure Monitortól. A részleteket lásd az omsadmin parancsfájl teljes kimenetében. |
| 6 | Nem 200-as HTTP-hiba érkezett az Azure Monitortól. A részleteket lásd az omsadmin parancsfájl teljes kimenetében. |
| 7 | Nem lehet csatlakozni az Azure Monitorhoz. A részleteket lásd az omsadmin parancsfájl teljes kimenetében. |
| 8 | Hiba történt a Log Analytics-munkaterületre való bevezetéskor. A részleteket lásd az omsadmin parancsfájl teljes kimenetében. |
| 30 | Belső parancsfájlhiba. A [GitHub-probléma](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) fájl a kimenet részleteivel. |
| 31 | Hiba történt az ügynökazonosító létrehozásakor. A [GitHub-probléma](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) fájl a kimenet részleteivel. |
| 32 | Hiba a tanúsítványok létrehozásakor. A részleteket lásd az omsadmin parancsfájl teljes kimenetében. |
| 33 | Hiba történt az omsconfig metakonfigurációjának létrehozásakor. A [GitHub-probléma](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) fájl a kimenet részleteivel. |
| 34 | A metakonfiguráció-létrehozási parancsfájl nincs jelen. Próbálja meg újra `sudo sh /opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key>`a bevezetést a segítségével. |

## <a name="enable-debug-logging"></a>Hibakeresési naplózás engedélyezése
### <a name="oms-output-plugin-debug"></a>OMS kimeneti beépülő modul hibakeresési
 FluentD lehetővé teszi a plugin-specifikus naplózási szintek, amely lehetővé teszi, hogy adja meg a különböző naplószintek bemenetek és kimenetek. Az OMS-kimenet eltérő naplószintjének megadásához szerkessze az általános ügynök konfigurációját a rendszeren. `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`  

 Az OMS kimeneti beépülő modulban a `log_level` konfigurációs `info` `debug`fájl vége előtt módosítsa a tulajdonságot a következőre:

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

A hibakeresési naplózás lehetővé teszi, hogy az Azure Monitorra irányuló kötegelt feltöltéseket típus, adatelemek száma és a küldéshez szükséges idő szerint tekintse meg:

*Példa hibakeresési engedélyezett naplóra:*

```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

### <a name="verbose-output"></a>Részletes kimenet
Az OMS kimeneti beépülő modul használata `stdout`helyett közvetlenül is kimenetelezheti az adatokat, amely a Log Analytics linuxos naplófájlban látható.

A Log Analytics általános ügynök `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`konfigurációs fájlban a alkalmazásban `#` megjegyzésekkel fűzhet hozzá az OMS kimeneti bővítményhez, ha minden sor elé hozzáad egy bővítményt:

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

Az alábbi kimeneti plugin, uncomment a `#` következő szakaszban eltávolításával az első minden sorban:

```
<match **>
  type stdout
</match>
```

## <a name="issue--unable-to-connect-through-proxy-to-azure-monitor"></a>Probléma: Nem lehet proxyn keresztül csatlakozni az Azure Monitorhoz

### <a name="probable-causes"></a>Valószínű okok
* A bevezetés során megadott proxy helytelen volt
* Az Azure Monitor és az Azure Automation szolgáltatás végpontjai nem szerepelnek az adatközpontban 

### <a name="resolution"></a>Megoldás:
1. Reonboard az Azure Monitor a Log Analytics ügynök Linux a `-v` következő parancs használatával a beállítás engedélyezve van. Lehetővé teszi a proxyn keresztül az Azure Monitorhoz csatlakozó ügynök részletes kimenetét. 
`/opt/microsoft/omsagent/bin/omsadmin.sh -w <Workspace ID> -s <Workspace Key> -p <Proxy Conf> -v`

2. Tekintse át a [Proxybeállításainak frissítése](agent-manage.md#update-proxy-settings) című szakaszt, és ellenőrizze, hogy megfelelően konfigurálta-e az ügynököt proxykiszolgálón keresztüli kommunikációra.    
* Ellenőrizze, hogy az alábbi Azure Monitor-végpontok szerepelnek-e a listán:

    |Ügynök erőforrása| Portok | Irány |
    |------|---------|----------|  
    |*.ods.opinsights.azure.com | 443-as port| Bejövő és kimenő |  
    |*.oms.opinsights.azure.com | 443-as port| Bejövő és kimenő |  
    |*.blob.core.windows.net | 443-as port| Bejövő és kimenő |  

    Ha azt tervezi, hogy az Azure Automation hybrid Runbook Worker használatával csatlakozik az Automation szolgáltatáshoz, és regisztráljon az Automation szolgáltatással a runbookok vagy felügyeleti megoldások használatához a környezetben, akkor hozzá kell férnie a portszámhoz és a [Hálózat konfigurálása a hibrid runbook-feldolgozóhoz](../../automation/automation-hybrid-runbook-worker.md#network-planning)című részben leírt URL-címekhez. 

## <a name="issue-you-receive-a-403-error-when-trying-to-onboard"></a>Probléma: 403-as hibaüzenet jelenik meg a fedélzeten

### <a name="probable-causes"></a>Valószínű okok
* A dátum és az idő helytelen a Linux Server kiszolgálón 
* A használt munkaterület-azonosító és munkaterületkulcs nem megfelelő

### <a name="resolution"></a>Megoldás:

1. Ellenőrizze az időt a Linux szerveren a parancs dátumával. Ha az idő +/- 15 percre van az aktuális időtől, akkor a bevezetés sikertelen lesz. A frissítés javításához a Linux-kiszolgáló dátuma és/vagy időzónája. 
2. Ellenőrizze, hogy telepítette-e a Linux-naplóelemzési ügynök legújabb verzióját.  A legújabb verzió most értesíti, ha az időeltérés okozza a bevezetési hibát.
3. Reonboard segítségével helyes munkaterület-azonosító és munkaterület kulcs a telepítési utasításokat követően korábban a cikkben.

## <a name="issue-you-see-a-500-and-404-error-in-the-log-file-right-after-onboarding"></a>Probléma: 500-as és 404-es hiba jelenik meg a naplófájlban közvetlenül a bevezetés után
Ez egy ismert probléma, amely akkor fordul elő, amikor a Linux-adatok feltöltése a Log Analytics munkaterületre. Ez nincs hatással az elküldött adatokra vagy a szolgáltatási élményre.


## <a name="issue-you-see-omiagent-using-100-cpu"></a>Probléma: 100%-os CPU-val látja az omiagent-ot

### <a name="probable-causes"></a>Valószínű okok
A regresszió nss-pem csomag [v1.0.3-5.el7](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html) okozott súlyos teljesítmény probléma, hogy mi már látni jön egy csomó Redhat / Centos 7.x disztribúciók. Ha többet szeretne megtudni a problémáról, olvassa el a következő dokumentációt: Bug [1667121 Teljesítmény regresszió a libcurl](https://bugzilla.redhat.com/show_bug.cgi?id=1667121).

A teljesítményhez kapcsolódó hibák nem mindig fordulnak elő, és nagyon nehéz reprodukálni őket. Ha ilyen problémát tapasztal az omiagenssal kapcsolatban, akkor a parancsfájlt kell használnia omiHighCPUDiagnostics.sh amely egy bizonyos küszöbérték túllépése esetén összegyűjti az omiagent veremnyomatát.

1. A szkript letöltése <br/>
`wget https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/tools/LogCollector/source/omiHighCPUDiagnostics.sh`

2. Diagnosztika futtatása 24 órán keresztül 30%-os CPU-küszöbértékkel <br/>
`bash omiHighCPUDiagnostics.sh --runtime-in-min 1440 --cpu-threshold 30`

3. Callstack lesz dömpingelt omiagent_trace fájlban, Ha azt veszi észre, sok Curl és NSS függvény hívások, kövesse felbontás alábbi lépéseket.

### <a name="resolution-step-by-step"></a>Felbontás (lépésről lépésre)

1. Frissítse az nss-pem csomagot [a v1.0.3-5.el7_6.1](https://centos.pkgs.org/7/centos-x86_64/nss-pem-1.0.3-7.el7.x86_64.rpm.html). <br/>
`sudo yum upgrade nss-pem`

2. Ha nss-pem nem áll rendelkezésre a frissítés (többnyire történik Centos), majd downgrade göndör 7.29.0-46. Ha véletlenül futtatja a "yum update"-et, akkor a curl 7.29.0-51-re frissül, és a probléma újra megfog történni. <br/>
`sudo yum downgrade curl libcurl`

3. Az OMI újraindítása: <br/>
`sudo scxadmin -restart`

## <a name="issue-you-are-not-seeing-any-data-in-the-azure-portal"></a>Probléma: Nem lát semmilyen adatot az Azure Portalon

### <a name="probable-causes"></a>Valószínű okok

- Az Azure Monitorba való bevezetés nem sikerült
- Az Azure Monitorhoz való csatlakozás le van tiltva
- A Log Analytics Linux-adatokért felelős ügynökről készült biztonsági másolatot

### <a name="resolution"></a>Megoldás:
1. Ellenőrizze, hogy az Onboarding Azure Monitor sikeres volt-e, ha ellenőrzi, hogy a következő fájl létezik-e:`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`
2. Reonboard a `omsadmin.sh` parancssori utasítások használatával
3. Ha proxyt használ, olvassa el a korábban megadott proxyfeloldási lépéseket.
4. Bizonyos esetekben, amikor a Log Analytics ügynök linuxos nem tud kommunikálni a szolgáltatással, az ügynök adatait a teljes pufferméretet, amely 50 MB.In some cases when the Log Analytics agent for Linux cannot communicate with the service, data on the agent is queued to the full buffer size, which is 50 MB. Az ügynököt a következő parancs futtatásával kell újraindítani: `/opt/microsoft/omsagent/bin/service_control restart [<workspace id>]`. 

    >[!NOTE]
    >Ezt a problémát az 1.1.0-28-as és újabb verziójú ügynöki verzió javította.


## <a name="issue-you-are-not-seeing-forwarded-syslog-messages"></a>Probléma: Nem lát továbbított Syslog üzeneteket 

### <a name="probable-causes"></a>Valószínű okok
* A Linux-kiszolgálóra alkalmazott konfiguráció nem teszi lehetővé az elküldött létesítmények és/vagy naplószintek gyűjtését
* A Syslog nem megfelelően van továbbítva a Linux-kiszolgálóra
* A másodpercenként továbbított üzenetek száma túl nagy ahhoz, hogy a Linux-naplókezelő ügynök alapkonfigurációja kezelni tudja

### <a name="resolution"></a>Megoldás:
* Ellenőrizze a syslog i Log Analytics-munkaterületének konfigurációját, amely rendelkezik az összes létesítménnyel és a megfelelő naplószinttel. A [Syslog-gyűjtemény konfigurálásának áttekintése az Azure Portalon](../../azure-monitor/platform/data-sources-syslog.md#configure-syslog-in-the-azure-portal)
* Ellenőrizze, hogy a natív syslog`rsyslog`üzenetdémonok ( , `syslog-ng`) képesek-e fogadni a továbbított üzeneteket
* Ellenőrizze a tűzfal beállításait a Syslog kiszolgálón, hogy az üzenetek nincsenek-e letiltva
* Syslog üzenet szimulálása `logger` a Log Analytics szolgáltatásnak parancs használatával
  * `logger -p local0.err "This is my test message"`

## <a name="issue-you-are-receiving-errno-address-already-in-use-in-omsagent-log-file"></a>Probléma: Az omsagent naplófájlban már használatban lévő Errno-címet kapja
Ha az `[error]: unexpected error error_class=Errno::EADDRINUSE error=#<Errno::EADDRINUSE: Address already in use - bind(2) for "127.0.0.1" port 25224>` omsagent.log fájlban látható.

### <a name="probable-causes"></a>Valószínű okok
Ez a hiba azt jelzi, hogy a Linux diagnosztikai bővítmény (LAD) a Log Analytics Linux virtuálisgép-bővítménysel együtt van telepítve, és ugyanazt a portot használja a syslog adatgyűjtéshez, mint omsagent.

### <a name="resolution"></a>Megoldás:
1. Gyökérként hajtsa végre a következő parancsokat (vegye figyelembe, hogy a 25224 egy példa, és lehetséges, hogy a környezetben a LAD által használt másik portszámot lát):

    ```
    /opt/microsoft/omsagent/bin/configure_syslog.sh configure LAD 25229

    sed -i -e 's/25224/25229/' /etc/opt/microsoft/omsagent/LAD/conf/omsagent.d/syslog.conf
    ```

    Ezután meg kell változtatni `rsyslogd` `syslog_ng` a megfelelő vagy konfigurációs fájlt, és módosítsa a LAD-hoz kapcsolódó konfigurációt, hogy írjon a 25229-es portra.

2. Ha a virtuális `rsyslogd`gép fut, a módosítandó fájl a `/etc/rsyslog.d/95-omsagent.conf` `/etc/rsyslog`következő: (ha létezik, más). Ha a virtuális `syslog_ng`gép fut, a módosítandó fájl a következő: `/etc/syslog-ng/syslog-ng.conf`.
3. Indítsa újra `sudo /opt/microsoft/omsagent/bin/service_control restart`az omsagentet .
4. Indítsa újra a syslog szolgáltatást.

## <a name="issue-you-are-unable-to-uninstall-omsagent-using-purge-option"></a>Probléma: Az omsagent nem távolítható el a kiürítési beállítással

### <a name="probable-causes"></a>Valószínű okok

* Linux diagnosztikai bővítmény telepítve van
* A Linux diagnosztikai bővítmény telepítése és eltávolítása megtörtént, de továbbra is hibaüzenet jelenik meg az omsagent mdsd által használt, és nem távolítható el.

### <a name="resolution"></a>Megoldás:
1. Távolítsa el a Linux diagnosztikai bővítményt (LAD).
2. Távolítsa el a Linux diagnosztikai bővítmény fájljait a `/var/lib/waagent/Microsoft.Azure.Diagnostics.LinuxDiagnostic-<version>/` `/var/opt/microsoft/omsagent/LAD/`gépről, ha azok a következő helyen találhatók: és .

## <a name="issue-you-cannot-see-data-any-nagios-data"></a>Probléma: Nem láthatók az adatok nagios adatok 

### <a name="probable-causes"></a>Valószínű okok
* Az Omsagent felhasználónak nincs engedélye a Nagios naplófájlból való olvasásra
* A Nagios forrás és szűrő nem lett kommentálva az omsagent.conf fájlból

### <a name="resolution"></a>Megoldás:
1. Add omsagent felhasználó olvasni Nagios fájlt követve ezeket az [utasításokat](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts).
2. A Log Analytics ügynök linuxos `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`általános konfigurációs fájlban a rendszerben győződjön meg arról, hogy mind a Nagios forrás, **mind** a szűrő nem kommentálta.

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

## <a name="issue-you-are-not-seeing-any-linux-data"></a>Probléma: Nem lát linuxos adatokat 

### <a name="probable-causes"></a>Valószínű okok
* Az Azure Monitorba való bevezetés nem sikerült
* Az Azure Monitorhoz való csatlakozás le van tiltva
* A virtuális gép újraindult
* Az OMI csomagot manuálisan frissítették egy újabb verzióra a Log Analytics ügynök Linux-csomaghoz való telepítéséhez képest
* A DSC erőforrásnaplók *osztálya nem található* hiba a naplófájlban `omsconfig.log`
* Az adatok loganalytics-ügynöke biztonsági másolatot készül a
* DSC naplók *A jelenlegi konfiguráció nem létezik. A Start-DscConfiguration parancs végrehajtása a -Path paraméterrel egy konfigurációs fájl megadásához és az aktuális konfiguráció létrehozásához.* a `omsconfig.log` naplófájlban, de nem `PerformRequiredConfigurationChecks` létezik naplóüzenet a műveletekről.

### <a name="resolution"></a>Megoldás:
1. Telepítse az összes függőséget, például a naplóztatott csomagot.
2. Ellenőrizze, hogy az Azure Monitorra való bevezetés sikeres `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`volt-e, ha ellenőrzi, hogy a következő fájl létezik-e: .  Ha nem, akkor a omsadmin.sh parancssori [utasításokat](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)használva.
4. Ha proxyt használ, ellenőrizze a proxy hibaelhárítási lépéseit.
5. Egyes Azure-disztribúciós rendszerekben az omid OMI-kiszolgáló démon nem indul el a virtuális gép újraindítása után. Ez azt eredményezi, hogy nem látja a Naplózás, a ChangeTracking vagy az UpdateManagement megoldással kapcsolatos adatokat. A megoldás az omi kiszolgáló manuális `sudo /opt/omi/bin/service_control restart`indítása a .
6. Miután az OMI-csomagot manuálisan frissítette egy újabb verzióra, manuálisan újra kell indítani a Log Analytics-ügynök működésének folytatásához. Ez a lépés szükséges néhány olyan distózis esetén, ahol az OMI-kiszolgáló nem indul el automatikusan a frissítés után. Futtassa `sudo /opt/omi/bin/service_control restart` az OMI újraindításához.
7. Ha a DSC *erőforrásosztály nem található* hiba az omsconfig.log fájlban, futtassa a futtassa a futtassa a futtassa a fájlt. `sudo /opt/omi/bin/service_control restart`
8. Bizonyos esetekben, amikor a Log Analytics-ügynök linuxos nem tud beszélni az Azure Monitor, az ügynök adatait a teljes pufferméret: 50 MB.In some cases when the Log Analytics agent for Linux cannot talk to Azure Monitor, data on the agent is backed up to the full buffer size: 50 MB. Az ügynököt a következő parancs `/opt/microsoft/omsagent/bin/service_control restart`futtatásával kell újraindítani.

    >[!NOTE]
    >Ez a probléma az 1.1.0-28-as vagy újabb ügynökverzióban van kijavítva.
    >

* Ha `omsconfig.log` a naplófájl `PerformRequiredConfigurationChecks` nem jelzi, hogy a műveletek rendszeresen futnak a rendszeren, akkor lehet, hogy probléma van a cron feladattal/szolgáltatással. Győződjön meg arról, hogy a cron feladat létezik a alatt. `/etc/cron.d/OMSConsistencyInvoker` Szükség esetén futtassa a következő parancsokat a cron feladat létrehozásához:

    ```
    mkdir -p /etc/cron.d/
    echo "*/15 * * * * omsagent /opt/omi/bin/OMSConsistencyInvoker >/dev/null 2>&1" | sudo tee /etc/cron.d/OMSConsistencyInvoker
    ```

    Ellenőrizze azt is, hogy fut-e a cron szolgáltatás. Használhatja `service cron status` a Debian, Ubuntu, SUSE, vagy `service crond status` RHEL, CentOS, Oracle Linux állapotának ellenőrzésére. Ha a szolgáltatás nem létezik, telepítheti a bináris fájlokat, és a következő módon indíthatja el a szolgáltatást:

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

## <a name="issue-when-configuring-collection-from-the-portal-for-syslog-or-linux-performance-counters-the-settings-are-not-applied"></a>Probléma: A Syslog vagy Linux teljesítményszámlálók portálról történő gyűjteményének konfigurálásakor a beállítások nem lesznek alkalmazva

### <a name="probable-causes"></a>Valószínű okok
* A Linux-naplóelemzési ügynök nem vette fel a legújabb konfigurációt
* A portál módosított beállításai nem voltak alkalmazva

### <a name="resolution"></a>Megoldás:
**Háttér:** `omsconfig` a Log Analytics ügynök Linux konfigurációs ügynök, amely öt percenként új portáloldali konfigurációt keres. Ezt a konfigurációt ezután alkalmazza a Log Analytics ügynök Linux konfigurációs fájlokat található /etc/opt/microsoft/omsagent/conf/omsagent.conf.

* Bizonyos esetekben előfordulhat, hogy a Log Analytics-ügynök Linux konfigurációs ügynök nem tud kommunikálni a portálkonfigurációs szolgáltatás, ami a legújabb konfiguráció nem alkalmazva.
  1. Ellenőrizze, `omsconfig` hogy az ügynök `dpkg --list omsconfig` `rpm -qi omsconfig`telepítve van-e a vagy a futtatásával.  Ha nincs telepítve, telepítse újra a Legújabb verzióját a Log Analytics ügynök Linux.

  2. Ellenőrizze, `omsconfig` hogy az ügynök képes-e kommunikálni `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`az Azure Monitorral a következő parancs futtatásával. Ez a parancs visszaadja a konfigurációt, amelyet az ügynök kap a szolgáltatástól, beleértve a Syslog beállításait, a Linux teljesítményszámlálók és az egyéni naplók. Ha ez a parancs sikertelen, futtassa a következő parancsot. `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'` Ez a parancs kényszeríti az omsconfig ügynök beszélni az Azure Monitor, és a legújabb konfiguráció beolvasása.

## <a name="issue-you-are-not-seeing-any-custom-log-data"></a>Probléma: Nem lát egyéni naplóadatokat 

### <a name="probable-causes"></a>Valószínű okok
* Az Azure Monitorra való bevezetés nem sikerült.
* A **Következő konfiguráció alkalmazása a Linux-kiszolgálóimra** beállítás nincs kiválasztva.
* Az omsconfig nem vette fel a szolgáltatás legújabb egyéni naplókonfigurációját.
* A Linux-felhasználó `omsagent` Log Analytics-ügynöke engedély vagy nem található miatt nem tud hozzáférni az egyéni naplóhoz.  A következő hibák jelenhetnek meg:
 * `[DATETIME] [warn]: file not found. Continuing without tailing it.`
 * `[DATETIME] [error]: file not accessible by omsagent.`
* Ismert probléma a Race Condition rögzített Log Analytics ügynök Linux verzió 1.1.0-217

### <a name="resolution"></a>Megoldás:
1. Ellenőrizze, hogy az Azure Monitorra való bevezetés `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf`sikeres volt-e, ha ellenőrzi, hogy a következő fájl létezik-e: . Ha nem, vagy:  

  1. Reonboard segítségével a omsadmin.sh [parancssori utasításokat](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line).
  2. Az Azure Portal **Speciális beállításai** csoportban győződjön meg arról, hogy a következő konfiguráció alkalmazása **a Linux-kiszolgálóimra** engedélyezett.  

2. Ellenőrizze, `omsconfig` hogy az ügynök képes-e kommunikálni `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'`az Azure Monitorral a következő parancs futtatásával.  Ez a parancs visszaadja a konfigurációt, amelyet az ügynök kap a szolgáltatástól, beleértve a Syslog beállításait, a Linux teljesítményszámlálók és az egyéni naplók. Ha ez a parancs sikertelen, futtassa a következő parancsot. `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py'` Ez a parancs kényszeríti az omsconfig ügynök beszélni az Azure Monitor, és a legújabb konfiguráció beolvasása.

**Háttér:** Ahelyett, hogy a Log Analytics ügynök Linux `root`fut, mint egy `omsagent` kiemelt felhasználó , az ügynök fut, mint a felhasználó. A legtöbb esetben kifejezett engedélyt kell adni ennek a felhasználónak bizonyos fájlok olvasásához. Ha engedélyt `omsagent` szeretne adni a felhasználónak, futtassa a következő parancsokat:

1. A `omsagent` felhasználó hozzáadása adott csoporthoz`sudo usermod -a -G <GROUPNAME> <USERNAME>`
2. Univerzális olvasási hozzáférés megadása a szükséges fájlhoz`sudo chmod -R ugo+rx <FILE DIRECTORY>`

Van egy ismert probléma a versenyfeltétel a Log Analytics ügynök Linux-verzió korábbi, mint 1.1.0-217. A legújabb ügyintézőre való frissítés után futtassa a következő `sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf`parancsot a kimeneti bővítmény legújabb verziójának betalálásához.

## <a name="issue-you-are-trying-to-reonboard-to-a-new-workspace"></a>Probléma: Új munkaterületre szeretne átszállni
Amikor megpróbál egy ügynököt egy új munkaterületre átülenie, a Log Analytics-ügynök konfigurációját meg kell tisztítani a reonboarding előtt. A régi konfiguráció ügynökből való megtisztításához futtassa a shell csomagot`--purge`

```
sudo sh ./omsagent-*.universal.x64.sh --purge
```
Vagy

```
sudo sh ./onboard_agent.sh --purge
```

A reonboard folytatásához, `--purge` miután a

## <a name="log-analytics-agent-extension-in-the-azure-portal-is-marked-with-a-failed-state-provisioning-failed"></a>A Log Analytics-ügynök bővítmény az Azure Portalon hibás állapottal van megjelölve: A kiépítés sikertelen

### <a name="probable-causes"></a>Valószínű okok
* A Log Analytics-ügynök el lett távolítva az operációs rendszerből
* A Log Analytics-ügynökszolgáltatás leállt, le van tiltva vagy nincs konfigurálva

### <a name="resolution"></a>Megoldás: 
A probléma kijavításához hajtsa végre az alábbi lépéseket.
1. Bővítmény eltávolítása az Azure Portalról.
2. Telepítse az ügynököt az [utasításoknak](../../azure-monitor/learn/quick-collect-linux-computer.md)megfelelően.
3. Indítsa újra az ügynököt `sudo /opt/microsoft/omsagent/bin/service_control restart`a következő parancs futtatásával: .
* Várjon néhány percet, és a kiépítési állapot **a kiépítés sikeresen módosult.**


## <a name="issue-the-log-analytics-agent-upgrade-on-demand"></a>Probléma: A Log Analytics-ügynök igény szerinti frissítése

### <a name="probable-causes"></a>Valószínű okok

A Log Analytics-ügynök csomagok a gazdagép elavultak.

### <a name="resolution"></a>Megoldás: 
A probléma kijavításához hajtsa végre az alábbi lépéseket.

1. Ellenőrizze a legújabb kiadás az [oldalon](https://github.com/Microsoft/OMS-Agent-for-Linux/releases/).
2. Letöltés telepítési szkript (1.4.2-124 példa verzió):

    ```
    wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/OMSAgent_GA_v1.4.2-124/omsagent-1.4.2-124.universal.x64.sh
    ```

3. Frissítse a csomagokat `sudo sh ./omsagent-*.universal.x64.sh --upgrade`a végrehajtásával.
