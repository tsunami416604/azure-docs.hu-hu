---
title: A Naplóelemzési VMware figyelésére szolgáló megoldás |} Microsoft Docs
description: További tudnivalók a VMware figyelésére szolgáló megoldás hogyan segíthet naplóinak kezeléséhez és az ESXi-gazdagépek figyelésére.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: 16516639-cc1e-465c-a22f-022f3be297f1
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: magoedte
ms.openlocfilehash: 77326832f42cc1ef74ae7a380f4e38d3c67d17b7
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33775111"
---
# <a name="vmware-monitoring-preview-solution-in-log-analytics"></a>A Naplóelemzési megoldás VMware figyelése (előzetes verzió)

![VMware szimbólum](./media/log-analytics-vmware/vmware-symbol.png)

> [!NOTE]
> A VMware figyelésére szolgáló megoldás elavult.  Az ügyfelek, akik már telepítették a megoldás továbbra is használhatja, de a VMware-figyelés nem adható hozzá a bármely új munkaterületekhez való.

A VMware figyelésére szolgáló megoldás a Naplóelemzési olyan megoldás, amely segítségével hozhat létre a központi naplózás és a nagy VMware-naplók figyelési megközelítést. Ez a cikk ismerteti, hogyan hibaelhárítása, rögzítése és az ESXi-gazdagépek a segítségével egy helyen kezelheti. A megoldás részletes adatok egyetlen helyen megvalósítható az ESXi gazdagépek látható. Felső esemény számát, állapotát és az ESXi-gazdagép naplók keresztül elérhető virtuális gép és az ESXi gazdagépek trendek tekintheti meg. Elháríthatja az megtekintésével és központosított ESXi-gazdagép naplók keresése. És riasztások napló keresési lekérdezések alapján is létrehozhat.

A megoldás az ESXi-állomáson, elküldik az adatokat egy olyan cél virtuális gép, amelyen OMS-ügynök natív syslog funkcióit használja. Azonban a megoldás nem fájlok írására syslog a cél virtuális gép be. Az OMS-ügynököt 1514 portot nyit meg, és ez figyeli. Az adatok kap, miután az OMS-ügynököt a Naplóelemzési leküldéses értesítések az adatokat.

## <a name="install-and-configure-the-solution"></a>Telepítse és konfigurálja a megoldást
Az alábbi információk segítségével telepítse és konfigurálja a megoldást.

* A VMware figyelésére szolgáló megoldás hozzáadása az előfizetéshez ismertetett eljárással [felügyeleti megoldás hozzáadása](log-analytics-add-solutions.md#add-a-management-solution).

#### <a name="supported-vmware-esxi-hosts"></a>Támogatott VMware ESXi-gazdagépek
a vSphere ESXi-állomáson, 5.5, 6.0 és 6.5

#### <a name="prepare-a-linux-server"></a>Linux-kiszolgáló előkészítése
Hozzon létre egy Linux operációs rendszer virtuális gép összes syslog-adatot ESXi gazdagépek számára. A [OMS Linux-ügynök](log-analytics-linux-agents.md) összes ESXi állomás syslog-adatot gyűjtemény pontja. Több ESXi-gazdagépek továbbítani a naplók egy Linux-kiszolgálóhoz, az alábbi példában látható módon használhatja.  

   ![Syslog folyamat](./media/log-analytics-vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Syslog gyűjtemény konfigurálása
1. A VSphere syslog-továbbító beállítása. Syslog-továbbító beállítása segítségével részletes információkért lásd: [syslog ESXi 5.0-s és újabb rendszer (2003322) konfigurálása](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322). Ugrás a **ESXi-állomáson konfigurációs** > **szoftver** > **speciális beállítások** > **Syslog**.
   ![vsphereconfig](./media/log-analytics-vmware/vsphere1.png)  
2. Az a *Syslog.global.logHost* mezőben, adja hozzá a Linux-kiszolgálóra és a portszám *1514*. Például `tcp://hostname:1514` vagy `tcp://123.456.789.101:1514`
3. A syslog ESXi állomás tűzfal megnyitásához. **A gazdagép-konfigurálás ESXi** > **szoftver** > **biztonsági profil** > **tűzfal** , és nyissa meg a **Tulajdonságok**.  

    ![vspherefw](./media/log-analytics-vmware/vsphere2.png)  

    ![vspherefwproperties](./media/log-analytics-vmware/vsphere3.png)  
4. Ellenőrizze a vSphere konzolt annak ellenőrzéséhez, hogy a syslog helyesen van beállítva. Erősítse meg a az ESXI-állomáson, hogy a port **1514** van konfigurálva.
5. Töltse le, és az OMS-ügynök telepítése Linux a Linux-kiszolgálón. További információkért lásd: a [Linux az OMS-ügynököt dokumentációjában](https://github.com/Microsoft/OMS-Agent-for-Linux).
6. Linux az OMS-ügynök telepítése után nyissa meg a /etc/opt/microsoft/omsagent/sysconf/omsagent.d könyvtárba, és másolja a vmware_esxi.conf fájlt a /etc/opt/microsoft/omsagent/conf/omsagent.d directory és a módosítás a tulajdonost/csoport és a fájl engedélyeit. Példa:

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
7. Indítsa újra az OMS-ügynököt Linux futtatásával `sudo /opt/microsoft/omsagent/bin/service_control restart`.
8. A Linux-kiszolgálóra és az ESXi-állomáson közötti kapcsolat tesztelése a `nc` parancsot az ESXi-állomáson. Példa:

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

9. Az Azure-portálon végrehajtani a napló keresése `VMware_CL`. A Naplóelemzési a syslog-adatokat gyűjt, a syslog formátumba megmaradnak. A portál egyes területeken a rendszer rögzíti, például a *állomásnév* és *Folyamatnév*.  

    ![type](./media/log-analytics-vmware/type.png)  

    Ha a napló keresési eredmények megtekintése a fenti kép hasonló, a korábban a megoldás VMware figyelési irányítópult használata beállította.  

## <a name="vmware-data-collection-details"></a>VMware az gyűjtemény adatait
A VMware figyelésére szolgáló megoldás különböző metrikák és a napló teljesítményadatokat gyűjt a OMS-ügynököt használ, Linux, amelyeken engedélyezve ESXi-gazdagépek.

A következő táblázat adatgyűjtési módszerek és egyéb adatok gyűjtése hogyan részleteit.

| Platform | Linux OMS-ügynök | SCOM-ügynököt | Azure Storage | SCOM szükséges? | Felügyeleti csoport keresztül küldött SCOM ügynök adatok | Gyűjtemény gyakorisága |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |&#8226; |  |  |  |  |át 3 percenként |

Az alábbi táblázatban példák a VMware figyelésére szolgáló megoldás által gyűjtött adatok mezők megjelenítése:

| Mező neve | leírás |
| --- | --- |
| Device_s |VMware-tárolóeszközök |
| ESXIFailure_s |Hiba típusa |
| EventTime_t |idő, amikor esemény történt |
| HostName_s |ESXi-gazdagép neve |
| Operation_s |Hozzon létre virtuális Gépet vagy virtuális gép törlése |
| ProcessName_s |esemény neve |
| ResourceId_s |a VMware gazdagép neve |
| ResourceLocation_s |VMware |
| ResourceName_s |VMware |
| ResourceType_s |Hyper-V |
| SCSIStatus_s |VMware SCSI-állapot |
| SyslogMessage_s |Syslog-adatot |
| UserName_s |felhasználó létrehozása vagy törlése a virtuális gép |
| VMName_s |a virtuális gép neve |
| Computer |számítógép |
| TimeGenerated |az adatok készítésének ideje |
| DataCenter_s |VMware datacenter |
| StorageLatency_s |tárolási késés (ms) |

## <a name="vmware-monitoring-solution-overview"></a>VMware-figyelés megoldás áttekintése
A Naplóelemzési munkaterület a VMware csempe jelenik meg. Az esetleges hibákat magas szintű áttekintést nyújt a. Kattintson a csempére, amikor belép egy irányítópult-nézet.

![Mozaik elrendezés](./media/log-analytics-vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>Keresse meg az irányítópult-nézet
Az a **VMware** irányítópult-nézethez paneleken szerint vannak rendszerezve:

* Hibaszámláló állapota
* Esemény felső állomások számolják
* Első esemény száma
* Virtuálisgép-tevékenységek
* ESXi-állomáson, lemez-események

![solution1](./media/log-analytics-vmware/solutionview1-1.png)

![solution2](./media/log-analytics-vmware/solutionview1-2.png)

Kattintson a panelre, amely megfelel a panel részletes információit jeleníti meg a Naplóelemzési a Keresés ablaktábla megnyitása.

Itt szerkesztheti a keresési lekérdezés történő valamilyen konkrét módosíthatja azt. További részletek a naplóban keresések létrehozása: [található adatokat, és napló kereséseket a Naplóelemzési](log-analytics-log-searches.md).

#### <a name="find-esxi-host-events"></a>Olyan esemény megkeresése ESXi állomás
Egyetlen ESXi-gazdagép több naplókat, a folyamatok alapján hoz létre. A VMware figyelésére szolgáló megoldás központosítja azokat, és az esemény számát foglalja össze. Ez a nézet központi segítségével megismerheti, hogy mely ESXi-állomáson van egy nagy mennyiségű esemény, és milyen eseményeket fordulnak elő a leggyakrabban a környezetben.

![esemény](./media/log-analytics-vmware/events.png)

Megtekintheti az ESXi-állomáson, vagy egy eseménytípust kattintva további.

Ha egy ESXi állomásnév gombra kattint, meg adott ESXi-állomáson, adatait is megtekintheti. Ha azt szeretné, az eseménytípus eredmények szűkítéséhez, vegye fel `“ProcessName_s=EVENT TYPE”` a keresési lekérdezés. Kiválaszthatja **Folyamatnév** a keresési szűrő. Az az adatokat, amelyek szűkíthető.

![Részletezés](./media/log-analytics-vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Nagy méretű tevékenységek keresése
A virtuális gépek hozható létre és bármely ESXi-állomáson, a rendszer törölheti. Akkor célszerű a rendszergazda létrehoz egy ESXi-állomáson, hány virtuális gépek azonosításához. Adott szolgálna, segít megérteni a teljesítmény és kapacitás-tervezési. Virtuális gép tevékenységesemények szerinti nyomon követést elengedhetetlen a környezet kezeléséhez.

![Részletezés](./media/log-analytics-vmware/vmactivities1.png)

Ha meg szeretné tekinteni a további ESXi virtuális gép létrehozása adatok tárolására, kattintson az ESXi-állomás neve.

![Részletezés](./media/log-analytics-vmware/createvm.png)

#### <a name="common-search-queries"></a>Közös keresési lekérdezések
A megoldás tartalmaz további hasznos lekérdezések, amelyek segítségével kezelheti az ESXi-gazdagépek például magas tárolóhelyet, a tárolóeszközök késése és elérési hiba.

[!INCLUDE[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

![lekérdezés](./media/log-analytics-vmware/queries.png)


#### <a name="save-queries"></a>Lekérdezések mentése
Keresési lekérdezések mentése a Naplóelemzési szabványos szolgáltatása, és segítenek megőrizni, amely hasznos talált lekérdezéseket. Miután létrehozott egy lekérdezést, amely akkor hasznosak, mentse kattintva a **Kedvencek**. Egy korábban mentett lekérdezés lehetővé teszi, hogy könnyen újra felhasználhatja később a [saját irányítópult](log-analytics-dashboards.md) lap, ahol a saját egyéni irányítópultok hozhatók létre.

![DockerDashboardView](./media/log-analytics-vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Riasztások lekérdezések létrehozása
Miután létrehozta a lekérdezéseket, előfordulhat, hogy használni kívánt lekérdezések adott események bekövetkezése esetén riasztást küld. Lásd: [Naplóelemzési riasztások](log-analytics-alerts.md) riasztások létrehozásával kapcsolatos információkat. Kapcsolatos riasztások lekérdezéseket és egyéb lekérdezés példák, tekintse meg a [figyelő VMware használatával Naplóelemzési](https://blogs.technet.microsoft.com/msoms/2016/06/15/monitor-vmware-using-oms-log-analytics) blogbejegyzést.

## <a name="frequently-asked-questions"></a>Gyakori kérdések
### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>Mit kell az ESXi gazdagép-beállítást? Milyen hatással legyen benne az aktuális környezetben?
A megoldás a natív ESXi állomás Syslog mechanizmus továbbítási használ. Nincs szükség semmilyen további Microsoft-szoftverek a ESXi-állomáson, rögzítheti a naplókat. Nem rendelkezhet egy kis hatása a meglévő környezetben. Azonban a syslog-továbbítást, amely ESXI funkciót be kell.

### <a name="do-i-need-to-restart-my-esxi-host"></a>Indítsa újra a ESXi-állomáson kell?
Nem. Ez a folyamat nem igényel újraindítást. Egyes esetekben vSphere megfelelően frissíti a syslog. Ebben az esetben jelentkezzen be az ESXi-állomáson, és töltse be újra a syslog. Ebben az esetben nem kell, ez a folyamat nem őket a környezetben, indítsa újra a gazdagépet.

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-log-analytics"></a>Növeli vagy csökkenti a Naplóelemzési küldött napló adatok mennyiségét?
Igen is. A vSphere az ESXi-gazdagép naplózási szint beállításokat is használhatja. Naplógyűjtést alapul a *információ* szintjét. Ezért, ha a virtuális gép létrehozásakor vagy törlésekor naplózni kívánt, szeretne rögzíteni a *info* Hostd szinten. További információkért lásd: a [VMware Tudásbázis](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658).

### <a name="why-is-hostd-not-providing-data-to-log-analytics-my-log-setting-is-set-to-info"></a>Miért Hostd nem biztosít adatokat a Log Analytics? A napló adatokhoz van állítva.
Az a syslog időbélyegzési egy ESXi állomás hiba történt. További információkért lásd: a [VMware Tudásbázis](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202). A megoldás telepítése után, általában Hostd kell működni.

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>Rendelkezhet több ESXi-gazdagépek egyetlen virtuális gép omsagent és továbbította a syslog-adatot?
Igen. Egyetlen virtuális gép és omsagent küldenek több ESXi-gazdagépek is.

### <a name="why-dont-i-see-data-flowing-into-log-analytics"></a>Miért nem látom, hogy a Naplóelemzési áramló adatokat?
Több oka is lehet:

* Az ESXi-állomáson, nem megfelelően küldését adatokat a virtuális gép futó omsagent. Hajtsa végre a következő lépéseket:

  1. Győződjön meg arról, hogy jelentkezzen be az ssh használatával ESXi-állomáson, és futtassa a következő parancsot: `nc -z ipaddressofVM 1514`

      Ha ez nem sikeres, a speciális konfigurációs beállítások vSphere valószínűleg nem szünteti meg. Lásd: [syslog gyűjtemény konfigurálása](#configure-syslog-collection) az ESXi-állomáson, a syslog-továbbítási beállításával kapcsolatos információk.
  2. Ha syslog port kapcsolat sikeres, de még nem lát adatokat, majd töltse be újra az ESXi-állomáson syslog használatával ssh a következő parancsot: ` esxcli system syslog reload`
* Az OMS-ügynököt a virtuális gép helytelenül van beállítva. Ennek teszteléséhez a következő lépésekkel:

  1. A port 1514 Naplóelemzési figyeli. Győződjön meg arról, hogy meg nyitva, futtassa a következő parancsot: `netstat -a | grep 1514`
  2. Megtekintheti az port `1514/tcp` megnyitásához. Ha nem így tesz, győződjön meg arról, hogy a omsagent megfelelően van-e telepítve. Ha nem látja a portinformációkat, majd a syslog-portjára nincs megnyitva a virtuális Gépen.

    a. Győződjön meg arról, hogy fut-e az OMS-ügynököt használatával `ps -ef | grep oms`. Ha nem fut, a folyamat elindításához futtassa a parancsot ` sudo /opt/microsoft/omsagent/bin/service_control start`

    b. Nyissa meg az `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf` fájlt.

    c. Győződjön meg arról, hogy a megfelelő felhasználói és a tárolócsoport-beállítás érvényes, hasonló: `-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`

    d. Ha a fájl nem létezik, vagy a felhasználó és csoport beállítás nem megfelelő, intézkedéseket által [egy Linux-kiszolgálót elő kell készíteni](#prepare-a-linux-server).

## <a name="next-steps"></a>További lépések
* Használjon [napló keresések](log-analytics-log-searches.md) adatokat tároló Naplóelemzési részletes VMware megtekintéséhez.
* [Hozzon létre egy saját irányítópultok](log-analytics-dashboards.md) VMware gazdagép adatok jelennek meg.
* [Hozzon létre a riasztások](log-analytics-alerts.md) amikor adott VMware gazdagép esemény következik be.
