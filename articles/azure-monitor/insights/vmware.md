---
title: VMware figyelési megoldás az Azure Monitorban | Microsoft dokumentumok
description: Ismerje meg, hogyan segíthet a VMware monitoring megoldás a naplók kezelésében és az ESXi-állomások figyelésében.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/04/2018
ms.openlocfilehash: c1622ef16155206d779c6d703fc7da568d233e7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77664779"
---
# <a name="vmware-monitoring-deprecated-solution-in-azure-monitor"></a>VMware monitoring (elavult) megoldás az Azure Monitorban

![VMware szimbólum](./media/vmware/vmware-symbol.png)

> [!NOTE]
> A VMware figyelési megoldás elavult.  Azok az ügyfelek, akik már telepítették a megoldást, továbbra is használhatják, de a VMware Monitoring nem adható hozzá új munkaterületekhez.

A VMware figyelési megoldás az Azure Monitor egy olyan megoldás, amely segít létrehozni egy központosított naplózási és figyelési megközelítést a nagy VMware naplók. Ez a cikk azt ismerteti, hogyan háríthatja el, rögzítheti és kezelheti az ESXi-állomásokat egyetlen helyen a megoldás használatával. A megoldással egyetlen helyen láthatja az összes ESXi-állomás részletes adatait. Megtekintheti a virtuális gépek és az ESXi-állomások legfontosabb eseményszámait, állapotát és trendjeit az ESXi gazdanaplókon keresztül. A hibaelhárítást a központosított ESXi állomásnaplók megtekintésével és keresésével teheti meg. És naplókeresési lekérdezések alapján is létrehozhat riasztásokat.

A megoldás az ESXi gazdagép natív syslog funkcióját használja az adatok leküldése a cél virtuális gépre, amely rendelkezik a Log Analytics-ügynökkel. Azonban a megoldás nem ír fájlokat a syslog a cél virtuális gépen belül. A Log Analytics-ügynök megnyitja az 1514-es portot, és ezt figyeli. Miután megkapta az adatokat, a Log Analytics-ügynök leküldéses az adatokat az Azure Monitorba.

## <a name="install-and-configure-the-solution"></a>A megoldás telepítése és konfigurálása
A megoldás telepítésekor és konfigurálásakor vegye figyelembe az alábbi információkat.

* Adja hozzá a VMware figyelési megoldás át az előfizetést a [Felügyeleti megoldás telepítése](../insights/solutions.md#install-a-monitoring-solution)című témakörben leírt folyamat tal.

#### <a name="supported-vmware-esxi-hosts"></a>Támogatott VMware ESXi állomások
vSphere ESXi host 5.5, 6.0 és 6.5

#### <a name="prepare-a-linux-server"></a>Linux-kiszolgáló előkészítése
Hozzon létre egy Linux operációs rendszer virtuális gép fogadásához az összes syslog adatokat az ESXi házigazdák. A [Log Analytics Linux-ügynök](../learn/quick-collect-linux-computer.md) az összes ESXi gazdarendszer syslog-adat gyűjtőpontja. Több ESXi-állomás segítségével továbbíthatja a naplókat egyetlen Linux-kiszolgálóra, ahogy az a következő példában is.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

   ![syslog áramlás](./media/vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Syslog-gyűjtemény konfigurálása
1. Állítsa be a syslog forwarding-ot a VSphere-hez. A syslog forwarding beállításának elősegítéséről az [ESXi 5.0 és újabb verziók (2003322) beállításáról](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322)nyújt be részletes információt. Tovább az **ESXi Host Configuration** > **Software** > **Advanced Settings** > **Syslog**.
   ![vsphereconfig között](./media/vmware/vsphere1.png)  
1. A *Syslog.global.logHost* mezőben adja hozzá a Linux-kiszolgálót és az *1514-es*portszámot. Példa: `tcp://hostname:1514` vagy `tcp://123.456.789.101:1514`
1. Nyissa meg az ESXi gazdatűzfalat a syslog számára. **ESXi Host Konfigurációs** > **szoftver** > biztonsági**profil** > **tűzfal** és a nyitott **tulajdonságok**.  

    ![vspherefw között](./media/vmware/vsphere2.png)  

    ![vspherefwproperties (vspherefwproperties)](./media/vmware/vsphere3.png)  
1. Ellenőrizze a vSphere konzolon, hogy a syslog megfelelően van-e beállítva. Ellenőrizze az ESXI gazdagépen, hogy az **1514-es** port be van állítva.
1. Töltse le és telepítse a Log Analytics ügynök Linux a Linux-kiszolgálón. További információt a [Linuxos Log Analytics-ügynök dokumentációja című](https://github.com/Microsoft/OMS-Agent-for-Linux)témakörben talál.
1. A Linuxalapú Log Analytics-ügynök telepítése után nyissa meg az /etc/opt/microsoft/omsagent/sysconf/omsagent.d könyvtárat, és másolja a vmware_esxi.conf fájlt az /etc/opt/microsoft/omsagent/conf/omsagent.d könyvtárba, és módosítsa a fájl tulajdonosát/csoportját és engedélyeit. Példa:

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
1. Indítsa újra a Log Analytics `sudo /opt/microsoft/omsagent/bin/service_control restart`ügynököt Linuxra a futtatásával.
1. Tesztelje a kapcsolatot a Linux szerver és `nc` az ESXi állomás között az ESXi Host parancsával. Példa:

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

1. Az Azure Portalon naplólekérdezést `VMware_CL`hajtson végre a számára. Amikor az Azure Monitor gyűjti a syslog adatokat, megőrzi a syslog formátumban. A portálon a program bizonyos mezőket rögzít, például *az Állomásnevet* és *a Folyamatnév mezőt.*  

    ![type](./media/vmware/type.png)  

    Ha a nézetnapló keresési eredményei hasonlóak a fenti képhez, akkor a VMware figyelési megoldás irányítópultját használja.  

## <a name="vmware-data-collection-details"></a>A VMware adatgyűjtésének részletei
A VMware monitoring megoldás különböző teljesítmény-mérőszámokat és naplóadatokat gyűjt az ESXi-állomásoktól az Ön által engedélyezett Linux-logAnalytics-ügynökök használatával.

Az alábbi táblázat az adatgyűjtési módszereket és az adatgyűjtés egyéb részleteit mutatja be.

| 
    platform
   | Log Analytics ügynök Linuxra | SCOM ügynök | Azure Storage | SCOM szükséges? | A felügyeleti csoporton keresztül küldött SCOM-ügynökadatok | gyűjtés gyakorisága |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |&#8226; |  |  |  |  |3 percenként |

Az alábbi táblázat példákat mutat be a VMware figyelési megoldása által gyűjtött adatmezőkre:

| mező neve | leírás |
| --- | --- |
| Device_s |VMware tárolóeszközök |
| ESXIFailure_s |hibatípusok |
| EventTime_t |esemény bekövetkezésének időpontja |
| HostName_s |ESXi állomásnév |
| Operation_s |virtuális gép létrehozása vagy virtuális gép törlése |
| ProcessName_s |esemény neve |
| ResourceId_s |A VMware-állomás neve |
| ResourceLocation_s |VMware |
| ResourceName_s |VMware |
| ResourceType_s |Hyper-V |
| SCSIStatus_s |VMware SCSI állapota |
| SyslogMessage_s |Syslog-adatok |
| UserName_s |felhasználó, aki virtuális gép létrehozása vagy törlése |
| VMName_s |a virtuális gép neve |
| Computer |gazdaszámítógép |
| TimeGenerated |az adatok létrehozásának időpontja |
| DataCenter_s |VMware adatközpont |
| StorageLatency_s |tárolási késés (ms) |

## <a name="vmware-monitoring-solution-overview"></a>VMware figyelési megoldás – áttekintés
A VMware csempe megjelenik a Log Analytics-munkaterületen. Magas szintű képet nyújt a hibákról. Amikor a csempére kattint, irányítópult-nézetbe lép.

![csempéző](./media/vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>Navigálás az irányítópult nézetben
A **VMware** irányítópult nézetben a blade-ek a következők szerint vannak rendezve:

* Hibaállapot-szám
* Legnépszerűbb állomás eseményszám szerint
* A legfontosabb események száma
* Virtuális gép tevékenységei
* ESXi host lemez események

![megoldás1](./media/vmware/solutionview1-1.png)

![megoldás2](./media/vmware/solutionview1-2.png)

Kattintson bármelyik panelre a Log Analytics keresési ablaktábla megnyitásához, amely a panelre vonatkozó részletes információkat jeleníti meg.

Itt szerkesztheti a naplólekérdezést, hogy valami konkrétat módosítson. A naplólekérdezések létrehozásáról az [Adatok keresése naplólekérdezések használatával az Azure Monitorban.](../log-query/log-query-overview.md)

#### <a name="find-esxi-host-events"></a>ESXi gazdaesemények keresése
Egyetlen ESXi-állomás több naplót hoz létre a folyamataik alapján. A VMware figyelési megoldás központosítja őket, és összefoglalja az eseményszámait. Ez a központosított nézet segít megérteni, hogy melyik ESXi gazdagép rendelkezik nagy mennyiségű eseménysel, és milyen események fordulnak elő a leggyakrabban a környezetben.

![Esemény](./media/vmware/events.png)

Tovább fúrhat egy ESXi-állomásra vagy eseménytípusra kattintva.

Amikor egy ESXi állomásnévre kattint, az adott ESXi állomás adatait tekinti meg. Ha az eseménytípussal szeretné szűkíteni `“ProcessName_s=EVENT TYPE”` a találatokat, vegye fel a lekérdezésbe. A keresési szűrőben kiválaszthatja a **Folyamatnév** lehetőséget. Ez leszűkíti az információt.

![Fúró](./media/vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Magas virtuálisgép-tevékenységek keresése
A virtuális gép bármely ESXi-állomáson létrehozható és törölhető. A rendszergazda számára hasznos lehet annak meghatározása, hogy hány virtuális gépet hoz létre egy ESXi-állomás. Ez viszont segít megérteni a teljesítményt és a kapacitástervezést. A virtuális gép tevékenységi eseményeinek nyomon követése elengedhetetlen a környezet kezeléséhez.

![Fúró](./media/vmware/vmactivities1.png)

Ha további ESXi gazdagép virtuális gép létrehozási adatait szeretné látni, kattintson egy ESXi állomásnévre.

![Fúró](./media/vmware/createvm.png)

#### <a name="common-log-queries"></a>Gyakori naplólekérdezések
A megoldás más hasznos lekérdezéseket is tartalmaz, amelyek segítségével kezelheti az ESXi-állomásokat, például a magas tárhelyet, a tárolási késést és az elérési út hibáját.

![Lekérdezések](./media/vmware/queries.png)


#### <a name="save-queries"></a>Lekérdezések mentése
A naplólekérdezések mentése az Azure Monitor általános szolgáltatása, és segítséget nyújt a hasznosnak talált lekérdezések megőrzésében. Miután létrehozott egy hasznosnak talált lekérdezést, mentse azt a **Kedvencek**gombra kattintva. A mentett lekérdezések segítségével később egyszerűen újra felhasználhatja azt a [Saját irányítópult](../learn/tutorial-logs-dashboards.md) lapon, ahol saját egyéni irányítópultokat hozhat létre.

![DockerDashboardNézet](./media/vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Értesítések létrehozása lekérdezésekből
Miután létrehozta a lekérdezéseket, érdemes lehet a lekérdezések segítségével figyelmeztetni, ha bizonyos események bekövetkeznek. A riasztások létrehozásáról a [Log Analytics riasztásai](../platform/alerts-overview.md) című témakörben talál információt. Példák a riasztási lekérdezések és egyéb lekérdezési példák, tekintse meg a [VMware figyelése a Log Analytics](https://blogs.technet.microsoft.com/msoms/2016/06/15/monitor-vmware-using-oms-log-analytics) blogbejegyzést.

## <a name="frequently-asked-questions"></a>Gyakori kérdések
### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>Mit kell tennem az ESXi gazdagép beállításával? Milyen hatással lesz ez a jelenlegi környezetemre?
A megoldás a natív ESXi Host Syslog továbbítási mechanizmust használja. A naplók rögzítéséhez nincs szükség további Microsoft-szoftverekre az ESXi Host szolgáltatásban. A meglévő környezetre gyakorolt hatása alacsony lehet. Azonban be kell állítania a syslog forwarding-ot, amely az ESXI funkció.

### <a name="do-i-need-to-restart-my-esxi-host"></a>Újra kell indítanom az ESXi gazdagépemet?
Nem. Ez a folyamat nem igényel újraindítást. Néha a vSphere nem frissíti megfelelően a syslog-ot. Ebben az esetben jelentkezzen be az ESXi állomásra, és töltse be újra a syslog-ot. Ismét nem kell újraindítania a gazdagép, így ez a folyamat nem zavarja a környezetet.

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-log-analytics"></a>Növelhetem vagy csökkenthetem a Log Analytics szolgáltatásba küldött naplóadatok mennyiségét?
De igen, megteheted. Használhatja az ESXi host log szint beállításait vSphere. A naplógyűjtés az *információs* szinten alapul. Így ha azt szeretné, hogy a virtuális gép létrehozása vagy törlése, meg kell tartani a *hostd információs* szintet. További információt a [VMware Tudásbázisban talál.](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658)

### <a name="why-is-hostd-not-providing-data-to-log-analytics-my-log-setting-is-set-to-info"></a>Miért nem ad meg a Hostd adatokat a Log Analytics szolgáltatásnak? A naplóbeállítás info beállításra van állítva.
Volt egy ESXi host hiba a syslog időbélyeg. További információt a [VMware Tudásbázisban talál.](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202) A megoldás alkalmazása után a Hostd nak megfelelően kell működnie.

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>Lehet több ESXi gazdagép továbbítja a syslog adatokat egyetlen virtuális gépomon omsagent?
Igen. Több ESXi-állomás továbbítása egyetlen virtuális gépomsa.

### <a name="why-dont-i-see-data-flowing-into-log-analytics"></a>Miért nem látom az adatok at a Log Analytics?
Ennek több oka is lehet:

* Az ESXi gazdagép nem megfelelően lenyomja az adatokat a virtuális gép futó omsagent. A teszteléshez hajtsa végre az alábbi lépéseket:

  1. A megerősítéshez jelentkezzen be az ESXi állomásra az ssh használatával, és futtassa a következő parancsot:`nc -z ipaddressofVM 1514`

      Ha ez nem sikerül, a speciális konfigurációban lévő vSphere-beállítások valószínűleg nem megfelelőek. A [syslog-gyűjtemény konfigurálása](#configure-syslog-collection) című témakörben talál további információt az ESXi gazdagép syslog továbbításhoz való beállításáról.
  1. Ha a syslog port kapcsolata sikeres, de még mindig nem lát adatokat, majd töltse be újra a syslog-ot az ESXi gazdagépen az ssh használatával a következő parancs futtatásához:`esxcli system syslog reload`
* A virtuális gép a Log Analytics-ügynök nincs megfelelően beállítva. A teszteléshez hajtsa végre az alábbi lépéseket:

  1. A Log Analytics az 1514-es portot figyeli. Annak ellenőrzéséhez, hogy meg van-e nyitva, futtassa a következő parancsot:`netstat -a | grep 1514`
  1. Látnia kellene, hogy a port `1514/tcp` nyitva van. Ha nem, ellenőrizze, hogy az omsagent megfelelően van-e telepítve. Ha nem látja a port adatait, majd a syslog port nincs megnyitva a virtuális gép.

    a. Ellenőrizze, hogy a Log Analytics-ügynök fut-e a használatával. `ps -ef | grep oms` Ha nem fut, indítsa el a folyamatot a parancs futtatásával`sudo /opt/microsoft/omsagent/bin/service_control start`

     b. Nyissa meg az `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf` fájlt.

     c. Ellenőrizze, hogy a megfelelő felhasználói és csoportbeállítás érvényes-e, hasonlóan a következőkhöz:`-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`

     d. Ha a fájl nem létezik, vagy a felhasználó és a csoport beállítása hibás, a [Linux-kiszolgáló előkészítése](#prepare-a-linux-server)segítségével tegye meg a korrekciós intézkedéseket.

## <a name="next-steps"></a>További lépések
* A Log Analytics [naplólekérdezései](../log-query/log-query-overview.md) segítségével részletes VMware-gazdaadatokat tekinthet meg.
* [Hozzon létre saját irányítópultokat a](../learn/tutorial-logs-dashboards.md) VMware gazdaadatait megjelenítő irányítópultokon.
* [Hozzon létre riasztásokat,](../platform/alerts-overview.md) ha adott VMware gazdagép események fordulnak elő.
