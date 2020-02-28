---
title: VMware Monitoring megoldás a Azure Monitorban | Microsoft Docs
description: Ismerje meg hogyan segíthet a VMware Monitoring megoldás a naplók kezelése és az ESXi-gazdagépek figyeléséhez.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/04/2018
ms.openlocfilehash: c1622ef16155206d779c6d703fc7da568d233e7e
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2020
ms.locfileid: "77664779"
---
# <a name="vmware-monitoring-deprecated-solution-in-azure-monitor"></a>VMware Monitoring (elavult) megoldás a Azure Monitor

![VMware szimbólum](./media/vmware/vmware-symbol.png)

> [!NOTE]
> A VMware Monitoring megoldás elavult.  Ügyfelek, akik már telepítették a megoldás továbbra is használhatja, de a VMware Monitoring nem lehet hozzáadni új munkaterületek.

A Azure Monitor VMware Monitoring megoldása olyan megoldás, amellyel központosított naplózási és figyelési módszert hozhat létre a nagyméretű VMware-naplók számára. Ez a cikk bemutatja, hogyan hibaelhárítása, rögzítése és az ESXi-gazdagépek, a megoldás segítségével egy helyen kezelheti. A megoldás révén láthatja a részletes adatok egy helyen az ESXi gazdagépek. Láthatja a legtöbbször előforduló események száma, állapotát és az ESXi-gazdagép naplóinak keresztül elérhető virtuális gép és az ESXi-gazdagépek trendeket. Elháríthatja, megtekintésével és központosított ESXi-gazdagép naplóinak keresése. És a naplóbeli keresési lekérdezések alapján riasztásokat is létrehozhat.

A megoldás az adatok leküldése egy cél virtuális Gépen, amely rendelkezik a Log Analytics-ügynököket az ESXi-gazdagépről natív syslog funkcióit. Azonban a megoldás nem fájlok írása a cél virtuális Gépen belül syslog be. A Log Analytics-ügynököket port 1514 megnyílik, és ez figyeli. Amint megkapja az adatkérést, a Log Analytics ügynök leküldi az adatAzure Monitorba.

## <a name="install-and-configure-the-solution"></a>Telepítse és konfigurálja a megoldást
A megoldás telepítésekor és konfigurálásakor vegye figyelembe az alábbi információkat.

* Adja hozzá a VMware Monitoring megoldást az előfizetéséhez a [figyelési megoldás telepítése](../insights/solutions.md#install-a-monitoring-solution)című témakörben ismertetett eljárás használatával.

#### <a name="supported-vmware-esxi-hosts"></a>Támogatott VMware ESXi-gazdagépek
a vSphere ESXi-gazdagép egy 5.5-ös, 6.0-s és 6.5-ös

#### <a name="prepare-a-linux-server"></a>Egy Linux-kiszolgáló előkészítése
Hozzon létre egy Linux operációs rendszert a virtuális gép az összes syslog-adatokat fogad az ESXi-gazdagépek. A [log Analytics Linux-ügynök](../learn/quick-collect-linux-computer.md) az ESXi-gazdagép syslog-adatgyűjtési pontja. Több ESXi-gazdagépek segítségével továbbítják a naplókat a egyetlen Linux rendszerű kiszolgálón, az alábbi példában látható módon.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

   ![Syslog folyamat](./media/vmware/diagram.png)

### <a name="configure-syslog-collection"></a>A rendszernaplók gyűjtése konfigurálása
1. Syslog-továbbítás vsphere-beállítása. A syslog-továbbítás beállításával kapcsolatos részletes információkért lásd: [a syslog konfigurálása ESXi 5,0 és újabb rendszereken (2003322)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322). Lépjen az **ESXi-gazdagép konfigurációja** > **szoftverek** > **Speciális beállítások** > **syslog**lehetőségre.
   ![vsphereconfig](./media/vmware/vsphere1.png)  
1. A *syslog. Global. logHost* mezőben adja hozzá a Linux-kiszolgálót és a *1514*-as portszámot. Például `tcp://hostname:1514` vagy `tcp://123.456.789.101:1514`
1. Nyissa meg a syslog ESXi-gazdagép tűzfal. **ESXi-gazdagép konfigurációja** > **szoftver** > **biztonsági profil** > **tűzfal** és nyitott **Tulajdonságok**.  

    ![vspherefw](./media/vmware/vsphere2.png)  

    ![vspherefwproperties](./media/vmware/vsphere3.png)  
1. Ellenőrizze a vSphere-konzolt annak ellenőrzésére, hogy a syslog helyesen van beállítva. Erősítse meg az ESXI-gazdagépen, hogy a **1514** -es port konfigurálva van.
1. Töltse le és telepítse a Linuxhoz készült Log Analytics-ügynököt a Linux-kiszolgálón. További információkért tekintse meg a [Linux rendszerhez készült log Analytics-ügynök dokumentációját](https://github.com/Microsoft/OMS-Agent-for-Linux).
1. A Log Analytics-ügynök Linux van telepítve, keresse fel a /etc/opt/microsoft/omsagent/sysconf/omsagent.d könyvtár és másolása után az vmware_esxi.conf fájlt a /etc/opt/microsoft/omsagent/conf/omsagent.d könyvtárat és a módosítás a tulajdonos csoport és a a fájlhoz tartozó engedélyeket. Például:

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
1. Indítsa újra a Log Analytics-ügynököt Linuxra a `sudo /opt/microsoft/omsagent/bin/service_control restart`futtatásával.
1. Tesztelje a Linux-kiszolgáló és az ESXi-gazdagép közötti kapcsolatot az ESXi-gazdagépen található `nc` parancs használatával. Például:

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

1. A Azure Portal hajtson végre egy log-lekérdezést `VMware_CL`hoz. Amikor Azure Monitor gyűjti a syslog-adatokat, megőrzi a syslog formátumát. A portálon bizonyos mezők rögzítése történik, például az *állomásnév* és a *processname*.  

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
A **VMware** irányítópult nézetében a pengék a következők szerint vannak rendezve:

* Állapot hibaszám
* Események száma a felső gazdagép
* Legtöbbször előforduló események száma
* Virtuálisgép-tevékenységek
* ESXi-gazdagépek Lemezeseményei

![solution1](./media/vmware/solutionview1-1.png)

![solution2](./media/vmware/solutionview1-2.png)

Kattintson a panelre, hogy a panel az adott részletes információkat jelenít meg a Log Analytics keresése panel megnyitásához.

Itt szerkesztheti a napló lekérdezését, hogy az adott dologra vonatkozóan módosítható legyen. A naplók létrehozásával kapcsolatos további információkért lásd: [adatok keresése a Azure monitor található naplók használatával](../log-query/log-query-overview.md).

#### <a name="find-esxi-host-events"></a>ESXi-gazdagép események
Egyetlen ESXi-gazdagép több naplókat, a folyamatok alapján hoz létre. A VMware Monitoring megoldás központosítja azokat, és az események számát foglalja össze. Ez a nézet központi segítségével megismerheti, milyen ESXi-gazdagép rendelkezik nagy mennyiségű esemény, és milyen eseményeket fordulnak elő a leggyakrabban a környezetben.

![esemény](./media/vmware/events.png)

Áthatoló ESXi-gazdagép vagy -eseménytípus kattintva további.

Ha rákattint egy ESXi-gazdagép nevével, a ESXi-gazdagépről információkat tekintjük meg. Ha az esemény típusával szeretné szűkíteni az eredményeket, adja hozzá `“ProcessName_s=EVENT TYPE”` a keresési lekérdezésben. A keresési szűrőben a **processname** is kiválaszthatja. Az az adatokat, amelyek szűkíthető.

![Részletezés](./media/vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Nagy virtuális gép tevékenységek
A virtuális gépek létrehozhatók és törölni minden ESXi-gazdagépen. Hasznos lehet a rendszergazdák azonosítani az ESXi-gazdagép hoz létre, hogy hány virtuális gépet. Hogy a-kapcsolja, segít megérteni, teljesítmény és a kapacitástervezéshez. Virtuális gép tevékenységesemények szerinti nyomon követést elengedhetetlen a környezet kezelése során.

![Részletezés](./media/vmware/vmactivities1.png)

Ha meg szeretné tekinteni a további ESXi gazdagépet virtuális gép létrehozása adatokat, kattintson az ESXi-gazdagép nevével.

![Részletezés](./media/vmware/createvm.png)

#### <a name="common-log-queries"></a>Gyakori naplók lekérdezései
Az egyéb hasznos lekérdezések, amelyek segítségével kezelheti az ESXi-gazdagépek, például magas lemezterület, a tárolóeszközök késése és az elérési út hibát tartalmaz.

![lekérdezés](./media/vmware/queries.png)


#### <a name="save-queries"></a>Lekérdezések mentése
A naplófájlok mentése a Azure Monitor egy szabványos funkciója, amely segítséget nyújt a hasznosnak ítélt lekérdezések megtartásában. Ha olyan lekérdezést hoz létre, amelyet hasznosnak talál, mentse azt a **Kedvencek**elemre kattintva. A mentett lekérdezésekkel könnyedén újra felhasználhatja később a saját [irányítópult](../learn/tutorial-logs-dashboards.md) oldaláról, ahol létrehozhat saját egyéni irányítópultokat is.

![DockerDashboardView](./media/vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Riasztások létrehozása a lekérdezések
Miután létrehozta a lekérdezéseket, érdemes a lekérdezések használata az adott események bekövetkezése esetén riasztást küld. Riasztások létrehozásával kapcsolatos információkért tekintse [meg a log Analytics riasztásait](../platform/alerts-overview.md) . A lekérdezések és az egyéb lekérdezési példák esetében tekintse meg a [VMware Monitor log Analytics blogbejegyzés használatával](https://blogs.technet.microsoft.com/msoms/2016/06/15/monitor-vmware-using-oms-log-analytics) című témakört.

## <a name="frequently-asked-questions"></a>Gyakori kérdések
### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>Mit kell az ESXi a gazdagép beállítást? Milyen hatással legyen benne a jelenlegi környezetemet?
A megoldás a natív ESXi gazdagépet Syslog-továbbítás mechanizmust használ. Nem kell minden olyan további Microsoft-szoftvereket az ESXi-gazdagépen, a naplók rögzítésére. Ennek tartalmaznia kell egy alacsony hatása a meglévő környezetben. Azonban kell beállítani a syslog-továbbítást, amely ESXI-funkciókat.

### <a name="do-i-need-to-restart-my-esxi-host"></a>Van szükségem az ESXi-gazdagép újraindítása?
Nem. Ez a folyamat nem igényel újraindítást. Egyes esetekben vSphere nem megfelelően frissíti a syslog. Ebben az esetben jelentkezzen be az ESXi-gazdagépen, és töltse be újra a syslog. Újra így ez a folyamat nem zavaró a környezetében, indítsa újra a gazdagép nem kell.

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-log-analytics"></a>Növelheti vagy csökkentheti a Log Analytics felé küldött naplózási adatok mennyiségét?
Igen is. A vSphere az ESXi-gazdagép naplózási szint beállításokat is használhatja. A naplók gyűjtése az *információs* szint alapján történik. Ha tehát a virtuális gép létrehozását vagy törlését szeretné naplózni, akkor meg kell őriznie az *információs* szintet a gazdagépen. További információ: [VMware Tudásbázis](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658).

### <a name="why-is-hostd-not-providing-data-to-log-analytics-my-log-setting-is-set-to-info"></a>Miért Hostd nem biztosít adatokat a Log Analytics? A naplózási beállítás értéke adatait.
Hiba történt egy ESXi gazdagépet kijavítva a syslog-időbélyegző. További információ: [VMware Tudásbázis](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202). A megoldás alkalmazása, után Hostd kell működéséhez.

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>Használhatok több ESXi-gazdagépen egyetlen virtuális géphez való omsagent syslog adatokat továbbító?
Igen. Egyetlen virtuális géphez való omsagent továbbítása több ESXi-gazdagépek is rendelkezhet.

### <a name="why-dont-i-see-data-flowing-into-log-analytics"></a>Miért nem látom, hogy a Log Analytics adatoknak?
Több oka lehet:

* Az ESXi-gazdagépen van nem megfelelő adatok leküldése omsagent futó virtuális gép. Ha tesztelni szeretné, hajtsa végre az alábbi lépéseket:

  1. A megerősítéshez jelentkezzen be az ESXi-gazdagépre SSH használatával, és futtassa a következő parancsot: `nc -z ipaddressofVM 1514`

      Ha ez nem jár sikerrel, a speciális konfigurációt a vSphere-beállítások valószínűleg helytelen. A syslog-továbbítás beállításával kapcsolatos információkért lásd: a [syslog-gyűjtemény konfigurálása](#configure-syslog-collection) .
  1. Ha a syslog-port kapcsolata sikeres, de még nem lát semmilyen adatmennyiséget, akkor az ESXi-gazdagépen az alábbi parancs futtatásával töltse be a syslog parancsot: `esxcli system syslog reload`
* A virtuális Gépet a Log Analytics-ügynök nincs megfelelően beállítva. Ennek teszteléséhez hajtsa végre az alábbi lépéseket:

  1. A log Analytics figyeli a 1514 porthoz. A megnyitásának ellenőrzéséhez futtassa a következő parancsot: `netstat -a | grep 1514`
  1. Ekkor meg kell jelennie a port `1514/tcp` megnyitva. Ha nem, ellenőrizze, hogy a omsagent megfelelően van-e telepítve. Ha nem látja a portadatokat, majd a syslog-portjára, nem nyissa meg a virtuális gépen.

    a. Ellenőrizze, hogy a Log Analytics ügynök fut-e `ps -ef | grep oms`használatával. Ha nem fut, indítsa el a folyamatot a parancs futtatásával `sudo /opt/microsoft/omsagent/bin/service_control start`

     b. Nyissa meg az `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf` fájlt.

     c. Győződjön meg arról, hogy a megfelelő felhasználó és Csoport beállítás érvényes, a következőhöz hasonló: `-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`

     d. Ha a fájl nem létezik, vagy a felhasználó és a csoport beállításai nem megfelelőek, végezze el a megfelelő műveletet a [Linux-kiszolgáló előkészítésével](#prepare-a-linux-server).

## <a name="next-steps"></a>Következő lépések
* A részletes VMware-gazdagépek részleteinek megtekintéséhez használjon Log Analytics [naplózási lekérdezéseket](../log-query/log-query-overview.md) .
* [Hozzon létre saját irányítópultokat a](../learn/tutorial-logs-dashboards.md) VMware-gazdagép adataival.
* [Riasztások létrehozása](../platform/alerts-overview.md) , ha adott VMware-gazdagép eseményei történnek.
