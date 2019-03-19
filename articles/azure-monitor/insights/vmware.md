---
title: VMware Monitoring megoldás az Azure Monitor |} A Microsoft Docs
description: Ismerje meg hogyan segíthet a VMware Monitoring megoldás a naplók kezelése és az ESXi-gazdagépek figyeléséhez.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 16516639-cc1e-465c-a22f-022f3be297f1
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: magoedte
ms.openlocfilehash: ece6c7048100a8204bfc067d9d57854b1d83c9b6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58074913"
---
# <a name="vmware-monitoring-deprecated-solution-in-azure-monitor"></a>VMware Monitoring (elavult) a megoldás az Azure monitorban

![VMware szimbólum](./media/vmware/vmware-symbol.png)

> [!NOTE]
> A VMware Monitoring megoldás elavult.  Ügyfelek, akik már telepítették a megoldás továbbra is használhatja, de a VMware Monitoring nem lehet hozzáadni új munkaterületek.

A VMware Monitoring megoldás az Azure monitorban olyan megoldás, amely segít a központi naplózást és figyelést nagy méretű VMware-naplók létrehozása. Ez a cikk bemutatja, hogyan hibaelhárítása, rögzítése és az ESXi-gazdagépek, a megoldás segítségével egy helyen kezelheti. A megoldás révén láthatja a részletes adatok egy helyen az ESXi gazdagépek. Láthatja a legtöbbször előforduló események száma, állapotát és az ESXi-gazdagép naplóinak keresztül elérhető virtuális gép és az ESXi-gazdagépek trendeket. Elháríthatja, megtekintésével és központosított ESXi-gazdagép naplóinak keresése. És a naplóbeli keresési lekérdezések alapján riasztásokat is létrehozhat.

A megoldás az adatok leküldése egy cél virtuális Gépen, amely rendelkezik a Log Analytics-ügynököket az ESXi-gazdagépről natív syslog funkcióit. Azonban a megoldás nem fájlok írása a cél virtuális Gépen belül syslog be. A Log Analytics-ügynököket port 1514 megnyílik, és ez figyeli. Az adatok kap, miután a Log Analytics-ügynököket leküldi az adatokat az Azure Monitor szolgáltatásba.

## <a name="install-and-configure-the-solution"></a>Telepítse és konfigurálja a megoldást
A megoldás telepítésekor és konfigurálásakor vegye figyelembe az alábbi információkat.

* A VMware Monitoring megoldás hozzáadása az előfizetésben az ismertetett folyamatot [figyelési megoldás telepítése](../insights/solutions.md#install-a-monitoring-solution).

#### <a name="supported-vmware-esxi-hosts"></a>Támogatott VMware ESXi-gazdagépek
a vSphere ESXi-gazdagép egy 5.5-ös, 6.0-s és 6.5-ös

#### <a name="prepare-a-linux-server"></a>Egy Linux-kiszolgáló előkészítése
Hozzon létre egy Linux operációs rendszert a virtuális gép az összes syslog-adatokat fogad az ESXi-gazdagépek. A [Log Analytics Linux-ügynök](../learn/quick-collect-linux-computer.md) minden ESXi-gazdagép rendszernaplóadatokat gyűjtemény pontja. Több ESXi-gazdagépek segítségével továbbítják a naplókat a egyetlen Linux rendszerű kiszolgálón, az alábbi példában látható módon.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

   ![Syslog folyamat](./media/vmware/diagram.png)

### <a name="configure-syslog-collection"></a>A rendszernaplók gyűjtése konfigurálása
1. Syslog-továbbítás vsphere-beállítása. Állítsa be a syslog-továbbítás segítségével részletes információkért lásd: [ESXi 5.0-s és újabb (2003322) syslog konfigurálása](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322). Lépjen a **ESXi-gazdagép konfigurációs** > **szoftver** > **speciális beállítások** > **Syslog**.
   ![vsphereconfig](./media/vmware/vsphere1.png)  
1. Az a *Syslog.global.logHost* adja hozzá a Linux-kiszolgálóra, és a portszám *1514*. Ha például `tcp://hostname:1514` vagy `tcp://123.456.789.101:1514`
1. Nyissa meg a syslog ESXi-gazdagép tűzfal. **ESXi-gazdagép konfigurációs** > **szoftver** > **biztonsági profil** > **tűzfal** , és nyissa meg a **Tulajdonságok**.  

    ![vspherefw](./media/vmware/vsphere2.png)  

    ![vspherefwproperties](./media/vmware/vsphere3.png)  
1. Ellenőrizze a vSphere-konzolt annak ellenőrzésére, hogy a syslog helyesen van beállítva. Erősítse meg az ESXI-gazdagépen erre a portra **1514** van konfigurálva.
1. Töltse le és telepítse a Linuxhoz készült Log Analytics-ügynököt a Linux-kiszolgálón. További információkért lásd: a [Linux-ügynök a Log Analytics dokumentációja](https://github.com/Microsoft/OMS-Agent-for-Linux).
1. A Log Analytics-ügynök Linux van telepítve, keresse fel a /etc/opt/microsoft/omsagent/sysconf/omsagent.d könyvtár és másolása után az vmware_esxi.conf fájlt a /etc/opt/microsoft/omsagent/conf/omsagent.d könyvtárat és a módosítás a tulajdonos csoport és a a fájlhoz tartozó engedélyeket. Példa:

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
1. Indítsa újra a Linuxhoz készült Log Analytics-ügynök futtatásával `sudo /opt/microsoft/omsagent/bin/service_control restart`.
1. A Linux-kiszolgálón és az ESXi-gazdagép közötti kapcsolat teszteléséhez használja a `nc` parancsot az ESXi-gazdagépen. Példa:

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

1. Az Azure Portalon, a napló lekérdezés végrehajtása `VMware_CL`. Az Azure Monitor a syslog-adatokat gyűjti össze, a syslog formátumba megmaradnak. A portálon az egyes területeken rögzítve lesznek például *állomásnév* és *ProcessName*.  

    ![type](./media/vmware/type.png)  

    Ha a napló keresési eredmények megtekintése a fenti képen láthatóhoz hasonló, beállíthat már használja a VMware Monitoring megoldás irányítópultján.  

## <a name="vmware-data-collection-details"></a>VMware-adatok gyűjtemény részletei
A VMware Monitoring megoldás különböző mérőszámokban és naplófájlokban teljesítményadatokat gyűjt a Log Analytics-ügynökök használatával Linux rendszeren, hogy engedélyezte az ESXi-gazdagépek.

Az alábbi táblázat adatgyűjtési módszerek és egyéb adatok gyűjtése hogyan részleteit.

| Platform | A Linuxhoz készült log Analytics-ügynök | SCOM-ügynök | Azure Storage | Az SCOM szükséges? | Az SCOM agent adatküldés felügyeleticsoport-n keresztül | Gyűjtés gyakorisága |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |&#8226; |  |  |  |  |át 3 percenként |

Az alábbi táblázatban példák a VMware Monitoring megoldás által összegyűjtött adatok mezők megjelenítése:

| mezőnév | leírás |
| --- | --- |
| Device_s |VMware-tárolóeszközök |
| ESXIFailure_s |Hiba típusa |
| EventTime_t |esemény fellépésének ideje |
| HostName_s |ESXi-gazdagép neve |
| Operation_s |Hozzon létre virtuális Gépet vagy virtuális gép törlése |
| ProcessName_s |Esemény neve |
| ResourceId_s |a VMware gazdagép neve |
| ResourceLocation_s |VMware |
| ResourceName_s |VMware |
| ResourceType_s |Hyper-V |
| SCSIStatus_s |VMware-SCSI-állapot |
| SyslogMessage_s |Syslog-adat |
| UserName_s |felhasználó, aki létrehozott vagy virtuális gép törlése |
| VMName_s |a virtuális gép neve |
| Computer |gazdaszámítógép |
| TimeGenerated |idő az adatok jött létre. |
| DataCenter_s |VMware-datacenter |
| StorageLatency_s |tárolási késés (ms) |

## <a name="vmware-monitoring-solution-overview"></a>VMware Monitoring megoldás áttekintése
A VMware-csempe jelenik meg a Log Analytics-munkaterületre. Biztosít egy átfogó képet kapjon az esetleges hibákat. Amikor a csempére kattint, akkor lépnek irányítópult-nézet.

![csempéző](./media/vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>Keresse meg az irányítópult-nézet
Az a **VMware** irányítópult-nézet, a többi panelen szerint vannak rendszerezve:

* Állapot hibaszám
* Események száma a felső gazdagép
* Legtöbbször előforduló események száma
* Virtuálisgép-tevékenységek
* ESXi-gazdagépek Lemezeseményei

![solution1](./media/vmware/solutionview1-1.png)

![solution2](./media/vmware/solutionview1-2.png)

Kattintson a panelre, hogy a panel az adott részletes információkat jelenít meg a Log Analytics keresése panel megnyitásához.

Itt szerkesztheti a naplólekérdezés esetében valamilyen konkrét módosíthat. További log lekérdezések létrehozásával kapcsolatos információkért lásd: [megtalálta az adatokat az Azure Monitor log-lekérdezésekkel](../log-query/log-query-overview.md).

#### <a name="find-esxi-host-events"></a>ESXi-gazdagép események
Egyetlen ESXi-gazdagép több naplókat, a folyamatok alapján hoz létre. A VMware Monitoring megoldás központosítja azokat, és az események számát foglalja össze. Ez a nézet központi segítségével megismerheti, milyen ESXi-gazdagép rendelkezik nagy mennyiségű esemény, és milyen eseményeket fordulnak elő a leggyakrabban a környezetben.

![esemény](./media/vmware/events.png)

Áthatoló ESXi-gazdagép vagy -eseménytípus kattintva további.

Ha rákattint egy ESXi-gazdagép nevével, a ESXi-gazdagépről információkat tekintjük meg. Ha szeretné az esemény típusa az eredmények szűkítéséhez, vegye fel `“ProcessName_s=EVENT TYPE”` a keresési lekérdezésben. Választhat **ProcessName** keresési szűrő. Az az adatokat, amelyek szűkíthető.

![Részletezés](./media/vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Nagy virtuális gép tevékenységek
A virtuális gépek létrehozhatók és törölni minden ESXi-gazdagépen. Hasznos lehet a rendszergazdák azonosítani az ESXi-gazdagép hoz létre, hogy hány virtuális gépet. Hogy a-kapcsolja, segít megérteni, teljesítmény és a kapacitástervezéshez. Virtuális gép tevékenységesemények szerinti nyomon követést elengedhetetlen a környezet kezelése során.

![Részletezés](./media/vmware/vmactivities1.png)

Ha meg szeretné tekinteni a további ESXi gazdagépet virtuális gép létrehozása adatokat, kattintson az ESXi-gazdagép nevével.

![Részletezés](./media/vmware/createvm.png)

#### <a name="common-log-queries"></a>Általános naplófájl-lekérdezések
Az egyéb hasznos lekérdezések, amelyek segítségével kezelheti az ESXi-gazdagépek, például magas lemezterület, a tárolóeszközök késése és az elérési út hibát tartalmaz.

![lekérdezés](./media/vmware/queries.png)


#### <a name="save-queries"></a>Lekérdezések mentése
Napló lekérdezéseket elmenti egy standard szintű funkció az Azure monitorban, és segít megőrizni, amely hasznos talált lekérdezéseket. Miután létrehozott egy lekérdezést, amely akkor hasznosak, mentse kattintva a **Kedvencek**. Egy korábban mentett lekérdezés teszi lehetővé egyszerűen újból felhasználhatja később a [saját irányítópult](../learn/tutorial-logs-dashboards.md) oldal, ahol a saját egyéni irányítópultokat hozhat létre.

![DockerDashboardView](./media/vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Riasztások létrehozása a lekérdezések
Miután létrehozta a lekérdezéseket, érdemes a lekérdezések használata az adott események bekövetkezése esetén riasztást küld. Lásd: [riasztások a Log Analyticsben](../platform/alerts-overview.md) riasztások létrehozásával kapcsolatos információkat. Riasztási lekérdezések és egyéb példák a lekérdezésekre vonatkozó példákért lásd a [Log Analytics szolgáltatást figyelő VMware](https://blogs.technet.microsoft.com/msoms/2016/06/15/monitor-vmware-using-oms-log-analytics) blogbejegyzést.

## <a name="frequently-asked-questions"></a>Gyakori kérdések
### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>Mit kell az ESXi a gazdagép beállítást? Milyen hatással legyen benne a jelenlegi környezetemet?
A megoldás a natív ESXi gazdagépet Syslog-továbbítás mechanizmust használ. Nem kell minden olyan további Microsoft-szoftvereket az ESXi-gazdagépen, a naplók rögzítésére. Ennek tartalmaznia kell egy alacsony hatása a meglévő környezetben. Azonban kell beállítani a syslog-továbbítást, amely ESXI-funkciókat.

### <a name="do-i-need-to-restart-my-esxi-host"></a>Van szükségem az ESXi-gazdagép újraindítása?
Nem. Ez a folyamat nem igényel újraindítást. Egyes esetekben vSphere nem megfelelően frissíti a syslog. Ebben az esetben jelentkezzen be az ESXi-gazdagépen, és töltse be újra a syslog. Újra így ez a folyamat nem zavaró a környezetében, indítsa újra a gazdagép nem kell.

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-log-analytics"></a>Növelheti vagy csökkentheti a Log Analytics felé küldött naplózási adatok mennyiségét?
Igen is. A vSphere az ESXi-gazdagép naplózási szint beállításokat is használhatja. Az Erőforrásnapló-gyűjtés alapul a *info* szintjét. Tehát ha azt szeretné, virtuális gép létrehozásakor vagy törlésekor naplózását, kell tartani a *info* Hostd szinten. További információkért lásd: a [VMware Tudásbázis](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658).

### <a name="why-is-hostd-not-providing-data-to-log-analytics-my-log-setting-is-set-to-info"></a>Miért Hostd nem biztosít adatokat a Log Analytics? A naplózási beállítás értéke adatait.
Hiba történt egy ESXi gazdagépet kijavítva a syslog-időbélyegző. További információkért lásd: a [VMware Tudásbázis](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202). A megoldás alkalmazása, után Hostd kell működéséhez.

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>Használhatok több ESXi-gazdagépen egyetlen virtuális géphez való omsagent syslog adatokat továbbító?
Igen. Egyetlen virtuális géphez való omsagent továbbítása több ESXi-gazdagépek is rendelkezhet.

### <a name="why-dont-i-see-data-flowing-into-log-analytics"></a>Miért nem látom, hogy a Log Analytics adatoknak?
Több oka lehet:

* Az ESXi-gazdagépen van nem megfelelő adatok leküldése omsagent futó virtuális gép. Ha tesztelni szeretné, hajtsa végre az alábbi lépéseket:

  1. Győződjön meg arról, hogy jelentkezzen be az ssh használata a ESXi-gazdagépen, és futtassa a következő parancsot: `nc -z ipaddressofVM 1514`

      Ha ez nem jár sikerrel, a speciális konfigurációt a vSphere-beállítások valószínűleg helytelen. Lásd: [konfigurálása a rendszernaplók gyűjtése](#configure-syslog-collection) az ESXi-gazdagépen, a syslog-továbbítás beállításával kapcsolatos információkat.
  1. Ha syslog-port kapcsolat létrejött, de nem is jelennek meg adatok, majd töltse be újra az ESXi-gazdagépen a syslog szerint az ssh használata a következő paranccsal: ` esxcli system syslog reload`
* A virtuális Gépet a Log Analytics-ügynök nincs megfelelően beállítva. Ennek teszteléséhez hajtsa végre az alábbi lépéseket:

  1. A log Analytics figyeli a 1514 porthoz. Győződjön meg arról, hogy meg nyitva, futtassa a következő parancsot: `netstat -a | grep 1514`
  1. Megtekintheti az port `1514/tcp` megnyitásához. Ha nem, ellenőrizze, hogy a omsagent megfelelően van-e telepítve. Ha nem látja a portadatokat, majd a syslog-portjára, nem nyissa meg a virtuális gépen.

     a. Győződjön meg arról, hogy a Log Analytics-ügynök fut-e a `ps -ef | grep oms`. Ha nem fut, a folyamat elindításához futtassa a parancsot ` sudo /opt/microsoft/omsagent/bin/service_control start`

     b. Nyissa meg az `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf` fájlt.

     c. Győződjön meg arról, hogy a megfelelő felhasználó és csoport beállítás érvényes, hasonló: `-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`

     d. Ha a fájl nem létezik vagy nem megfelelő, a felhasználó és csoport beállításának intézkedéseket által [egy Linux-kiszolgáló előkészítése](#prepare-a-linux-server).

## <a name="next-steps"></a>További lépések
* Használat [lekérdezések naplózását](../log-query/log-query-overview.md) adatokat tárolni a Log Analytics részletes VMware megtekintéséhez.
* [Saját irányítópult létrehozásával](../learn/tutorial-logs-dashboards.md) VMware gazdagép adatainak megjelenítése.
* [Riasztások létrehozása](../platform/alerts-overview.md) amikor adott VMware gazdagép események történnek.
