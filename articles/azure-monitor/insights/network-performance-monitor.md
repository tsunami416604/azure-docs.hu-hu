---
title: Hálózati teljesítményfigyelő megoldás az Azure-ban | Microsoft dokumentumok
description: Az Azure Hálózati teljesítményfigyelője segítségével közel valós időben figyelheti a hálózatok teljesítményét a hálózati teljesítménybeli szűk keresztmetszetek észleléséhez és megkereséséhez.
ms.subservice: logs
ms.topic: conceptual
author: vinynigam
ms.author: vinigam
ms.date: 02/20/2018
ms.openlocfilehash: 9660e87f3ee4e1c1c6a270f14928fdd111664e66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480878"
---
# <a name="network-performance-monitor-solution-in-azure"></a>Hálózati teljesítményfigyelő megoldás az Azure-ban

![Hálózati teljesítményfigyelő szimbólum](./media/network-performance-monitor/npm-symbol.png)


A Network Performance Monitor egy felhőalapú hibrid hálózatfigyelési megoldás, amely segít a hálózati infrastruktúra különböző pontjai közötti hálózati teljesítmény figyelésében. Emellett monitorozhatja a hálózat szolgáltatással és az alkalmazás végpontjaival való kapcsolatát is, illetve az Azure ExpressRoute teljesítményét is figyelheti. 

A Hálózati teljesítményfigyelő észleli az olyan hálózati problémákat, mint a forgalom blackholing, útválasztási hibák és a hagyományos hálózati figyelési módszerek által nem észlelt problémák. A megoldás riasztásokat készít, és értesíti, amikor egy hálózati kapcsolat meghaladja a küszöbértéket. Emellett biztosítja a hálózat teljesítményével kapcsolatos problémák időbeni észlelését és leszűkíti a hiba forrásának helyszínét egy hálózati szegmensre vagy eszközre. 

A Hálózati teljesítményfigyelő három átfogó funkciót kínál: 

* [Teljesítményfigyelő:](network-performance-monitor-performance-monitor.md)Figyelheti a hálózati kapcsolatot a felhőalapú központi telepítések és a helyszíni helyszínek, több adatközpont, fiókirodák és kritikus fontosságú többszintű alkalmazások vagy mikroszolgáltatások között. A Teljesítményfigyelő segítségével még a felhasználók panasza előtt észlelheti a hálózati problémákat.

* [Szolgáltatáskapcsolat-figyelő:](network-performance-monitor-service-connectivity.md)Figyelheti a felhasználók és a számára fontos szolgáltatások közötti kapcsolatot, meghatározhatja, hogy milyen infrastruktúra található az elérési úton, és meghatározhatja, hogy hol jelentkeznek a hálózati szűk keresztmetszetek. A felhasználók előtt megismerheti a kimaradásokat, és megtekintheti a problémák pontos helyét a hálózati útvonal mentén. 

    Ez a funkció segít a HTTP, HTTPS, TCP és ICMP alapú tesztek végrehajtásában a szolgáltatás rendelkezésre állásának és válaszidejének közel valós idejű figyeléséhez. Azt is figyelheti a hálózati hozzájárulás a csomagvesztés és a késés. A hálózati topológia térképsegítségével elkülönítheti a hálózati lassulásokat. Azonosíthatja azokat a problémás helyeket, amelyek a csomóponttól a szolgáltatásig a hálózati útvonal mentén fordulnak elő, és az egyes ugrások késési adatait. A beépített tesztekkel előzetes konfiguráció nélkül figyelheti az Office 365-tel és a Dynamics CRM rendszerrel való hálózati kapcsolatot. Ezzel a funkcióval figyelheti a hálózati kapcsolatot bármely TCP-képes végponthoz, például webhelyekhez, SaaS-alkalmazásokhoz, PaaS-alkalmazásokhoz és SQL-adatbázisokhoz.

* [ExpressRoute-figyelő:](network-performance-monitor-expressroute.md)Az Azure ExpressRoute-on keresztül figyelheti a fiókirodák és az Azure közötti végpontok közötti kapcsolatot és teljesítményt.  

A [Hálózati teljesítményfigyelő](https://docs.microsoft.com/azure/networking/network-monitoring-overview) által támogatott különböző funkciókról további információ érhető el az interneten.
 
## <a name="supported-regions"></a>Támogatott régiók
Az NPM a világ bármely részén, a következő régiók egyikében üzemeltetett munkaterületről figyelheti a hálózatok és alkalmazások közötti kapcsolatot:
* Észak-Európa
* Nyugat-Európa
* Közép-Franciaország
* Közép-Kanada
* USA nyugati régiója
* USA nyugati középső régiója
* USA északi középső régiója
* USA déli középső régiója
* USA középső régiója
* USA keleti régiója
* USA 2. keleti régiója
* USA nyugati régiója, 2.
* Kelet-Japán
* Délkelet-Ázsia
* Délkelet-Ausztrália
* Ausztrália középső régiója
* Kelet-Ausztrália
* Dél-Egyesült Királyság
* Kelet-Ázsia
* Dél-Korea középső régiója
* Közép-India
* Az Egyesült Államok kormánya Virginia
* Kína Keleti 2


Az ExpressRoute-figyelő támogatott régióinak listája a [dokumentációban](https://docs.microsoft.com/azure/expressroute/how-to-npm?utm_swu=8117)található.


## <a name="set-up-and-configure"></a>Beállítás és konfigurálás

### <a name="install-and-configure-agents"></a>Ügynökök telepítése és konfigurálása 

Az alapvető folyamatok segítségével ügynököket telepíthet a [Windows-számítógépek csatlakoztatásához az Azure Monitorhoz](../platform/agent-windows.md) és [az Operations Manager csatlakoztatásához az Azure Monitorhoz.](../platform/om-agents.md)

### <a name="where-to-install-the-agents"></a>Az ügynökök telepítési helye 

* **Teljesítményfigyelő:** Telepítse a Log Analytics-ügynököket legalább egy olyan csomópontra, amely minden olyan alhálózathoz csatlakozik, amelyről figyelni szeretné a hálózati kapcsolatot más alhálózatokhoz.

    A hálózati kapcsolat figyeléséhez telepítse az ügyintézőket a kapcsolat mindkét végpontján. Ha nem biztos a hálózat topológiájában, telepítse az ügynököket olyan kiszolgálókra, amelyek között kritikus számítási feladatok vannak, amelyek között figyelni szeretné a hálózati teljesítményt. Ha például figyelni szeretné a webkiszolgáló és az SQL-t futtató kiszolgáló közötti hálózati kapcsolatot, telepítsen egy ügynököt mindkét kiszolgálóra. Az ügynökök az állomások közötti hálózati kapcsolatot (kapcsolatokat) figyelik, nem magukat az állomásokat. 

* **Szolgáltatáskapcsolat figyelője:** Telepítsen egy Log Analytics-ügynököt minden olyan csomópontra, amelyről figyelni szeretné a hálózati kapcsolatot a szolgáltatás végpontjával. Példaként, ha az O1, O2 és O3 feliratú irodai webhelyekről szeretné figyelni az Office 365-höz való hálózati kapcsolatot. Telepítse a Log Analytics-ügynököt legalább egy csomópontra az O1, O2 és O3 rendszerben. 

* **ExpressRoute-figyelő:** Telepítsen legalább egy Log Analytics-ügynököt az Azure virtuális hálózatába. Is telepítse legalább egy ügynök a helyszíni alhálózat, amely az ExpressRoute privát társviszony-létesítési keresztül csatlakozik.  

### <a name="configure-log-analytics-agents-for-monitoring"></a>Log Analytics-ügynökök konfigurálása figyeléshez 

A Hálózati teljesítményfigyelő szintetikus tranzakciókat használ a forrás- és célügynökök közötti hálózati teljesítmény figyelésére. A Teljesítményfigyelő és a Szolgáltatáskapcsolat-figyelő képességeinek figyelési protokolljaként választhat a TCP és az ICMP között. Csak a TCP érhető el az ExpressRoute-figyelő figyelési protokolljaként. Győződjön meg arról, hogy a tűzfal lehetővé teszi a kommunikációt a Log Analytics ügynökök a választott protokoll figyeléséhez. 

* **TCP protokoll:** Ha a TCP protokollt választja figyelési protokollként, nyissa meg a hálózati teljesítményfigyelőhöz és az ExpressRoute-figyelőhöz használt ügynökök tűzfalportját, és győződjön meg arról, hogy az ügynökök csatlakozhatnak egymáshoz. A port megnyitásához futtassa az [EnableRules.ps1](https://aka.ms/npmpowershellscript) PowerShell-parancsfájlt rendszergazdai jogosultságokkal rendelkező PowerShell-ablakban megadott paraméterek nélkül.

    A parancsfájl létrehozza a megoldás által igényelt beállításkulcsokat. Windows tűzfal-szabályokat is létrehoz, amelyek lehetővé teszik az ügynökök számára, hogy TCP-kapcsolatokat hozzanak létre egymással. A parancsfájl által létrehozott beállításkulcsok határozzák meg, hogy naplózzák-e a hibakeresési naplókat és a naplófájl elérési útját. A parancsfájl határozza meg a kommunikációhoz használt TCP-ügynök portját is. A kulcsok értékeit a parancsfájl automatikusan beállítja. Ne módosítsa manuálisan ezeket a kulcsokat. A megnyitott port alapértelmezés szerint 8084. Egyéni portot úgy is használhat, hogy megadja a portNumber paramétert a parancsfájlnak. Használja ugyanazt a portot minden olyan számítógépen, ahol a parancsfájl fut. 

    >[!NOTE]
    > A parancsfájl csak a Windows tűzfalat konfigurálja helyileg. Ha hálózati tűzfallal rendelkezik, győződjön meg arról, hogy engedélyezi a Hálózati teljesítményfigyelő által használt TCP-portra szánt forgalmat.

    >[!NOTE]
    > Nem kell futtatnia az [EnableRules.ps1](https://aka.ms/npmpowershellscript ) PowerShell-parancsfájlt a Service Connectivity Monitorhoz.

    

* **ICMP protokoll:** Ha az ICMP protokollt választja a figyeléshez, engedélyezze a következő tűzfalszabályokat az ICMP megbízható kihasználásához:
    
   ```
   netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
   ```
 

### <a name="configure-the-solution"></a>A megoldás konfigurálása 

1. Adja hozzá a Hálózati teljesítményfigyelő megoldást a munkaterülethez az [Azure piactérről.](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview) Az [Azure Monitor-megoldások hozzáadása a Megoldások galériából](../../azure-monitor/insights/solutions.md)című albumban ismertetett folyamatot is használhatja. 
2. Nyissa meg a Log Analytics-munkaterületet, és válassza az **Áttekintés csempét.** 
3. Válassza ki a **Hálózati teljesítményfigyelő** csempét a *Megoldás további konfigurációt igényel.*

   ![Hálózati teljesítményfigyelő csempe](media/network-performance-monitor/npm-config.png)

4. A Telepítés lapon **láthatja** a Log Analytics-ügynökök telepítésének lehetőségét, és konfigurálhatja az ügynököket a figyeléshez a **Közös beállítások** nézetben. Ahogy korábban kifejtették, ha telepítette és konfigurálta a Log Analytics-ügynököket, válassza ki a **telepítési** nézetet a használni kívánt képesség konfigurálásához. 

   **Teljesítményfigyelő**: Válassza ki a szintetikus tranzakciókhoz használandó protokollt az **Alapértelmezett** teljesítményfigyelő szabályban, majd kattintson **a Mentés & folytatása gombra.** Ez a protokollválasztás csak a rendszer által létrehozott alapértelmezett szabályra vonatkozik. A teljesítményfigyelő szabály minden egyes létrehozásakor ki kell választania a protokollt. A **Teljesítményfigyelő** lapon bármikor átléphet az **Alapértelmezett** szabálybeállításokra (a 0. nap konfigurációjának befejezése után jelenik meg), és később módosíthatja a protokollt. Ha nem szeretné, hogy a Teljesítményfigyelő funkció jelensön meg, letilthatja az alapértelmezett szabályt a **Teljesítményfigyelő** lap **Alapértelmezett** szabálybeállításai között.

   ![Teljesítményfigyelő nézet](media/network-performance-monitor/npm-synthetic-transactions.png)
    
   **Szolgáltatáskapcsolat-figyelő:** Ez a funkció beépített, előre konfigurált teszteket biztosít az Office 365-höz és a Dynamics 365-höz való hálózati kapcsolat figyeléséhez az ügynököktől. Válassza ki a figyelni kívánt Office 365- és Dynamics 365-szolgáltatásokat a mellettük lévő jelölőnégyzetek bejelölésével. Az ügynökök kiválasztásához válassza az **Ügynökök hozzáadása**lehetőséget. Ha nem szeretné használni ezt a funkciót, vagy később szeretné beállítani, ne válasszon semmit, és válassza **a Mentés & folytatás lehetőséget.**

   ![Szolgáltatáskapcsolat figyelője nézet](media/network-performance-monitor/npm-service-endpoint-monitor.png)

   **ExpressRoute-figyelő:** Válassza a **Discover Now** lehetőséget az összes ExpressRoute-alapú privát társviszony-létesítésfelderítéséhez, amely az Azure-előfizetésben lévő virtuális hálózatokhoz csatlakozik, és ehhez a Log Analytics-munkaterülethez kapcsolódik. 

   ![ExpressRoute-figyelő nézet](media/network-performance-monitor/npm-express-route.png)

   A felderítés befejezése után a felderített áramkörök és társviszony-létesítések egy táblázatban jelennek meg. 

   ![A Hálózati teljesítményfigyelő konfigurációja lap](media/network-performance-monitor/npm-private-peerings.png)
    
Ezek az áramkörök és társviszony-létesítések figyelése kezdetben letiltott állapotban van. Jelölje ki a figyelni kívánt erőforrásokat, és konfigurálja a figyelést a jobb oldali részletek nézetből. A konfiguráció mentéséhez válassza a **Mentés** gombot. További információ: "ExpressRoute figyelés konfigurálása" című cikk. 

A telepítés befejezése után az adatok feltöltése 30 percés egy óra között tart. Amíg a megoldás összesíti a hálózatról származó adatokat, a *Megoldás további konfigurációt igényel* a Hálózatteljesítmény-figyelő **áttekintése** csempén. Az adatok gyűjtése és indexelése után az **Áttekintő** csempe megváltozik, és összegzésben tájékoztatja a hálózat állapotáról. Ezután szerkesztheti a loganalytics-ügynököket telepíteni kívánt csomópontok, valamint a környezetből felderített alhálózatok figyelését.

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>Alhálózatok és -csomópontok figyelési beállításainak szerkesztése 

Minden olyan alhálózat, amelyen legalább egy ügynök van telepítve, megjelenik a konfigurációs lap **Alhálózatok** lapján. 


Adott alhálózatok figyelésének engedélyezése vagy letiltása:

1. Jelölje be az alhálózati azonosító melletti **jelölőnégyzetet,** vagy törölje belőle a jelet. Ezután győződjön meg arról, hogy **a Use for Monitoring** lehetőség van kiválasztva vagy törlődik. Több alhálózatot is kijelölhet vagy törölhet. Ha le van tiltva, az alhálózatok nem figyelhetők, és az ügynökök frissülnek, hogy leálljanak a többi ügyintéző pingelésében. 
2. Válassza ki azokat a csomópontokat, amelyeket egy adott alhálózatban figyelni szeretne. Jelölje ki az alhálózatot a listából, és helyezze át a szükséges csomópontokat a nem figyelt és figyelt csomópontokat tartalmazó listák között. Az alhálózathoz egyéni leírást adhat hozzá.
3. A konfiguráció mentéséhez válassza a **Mentés** gombot. 

#### <a name="choose-nodes-to-monitor"></a>A figyelni kívánt csomópontok kiválasztása

Az összes olyan csomópont, amelyen egy ügynök telepítve van, a **Csomópontok** lapon jelenik meg. 

1. Jelölje ki vagy törölje a figyelést figyelni vagy leállítani kívánt csomópontokat. 
2. Válassza **a Használat a figyeléshez**lehetőséget, vagy törölje azt. 
3. Kattintson a **Mentés** gombra. 


Konfigurálja a kívánt képességeket:

- [Teljesítménymonitorozás](network-performance-monitor-performance-monitor.md#configuration)
- [Szolgáltatáskapcsolati monitor](network-performance-monitor-performance-monitor.md#configuration)
- [ExpressRoute-figyelő](network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>Adatgyűjtésrészletei
A veszteséges és késési adatok gyűjtéséhez a Hálózati teljesítményfigyelő TCP SYN-SYNACK-ACK kézfogási csomagokat használ, amikor a TCP protokollt választja. A Hálózati teljesítményfigyelő az ICMP ECHO ICMP ECHO REPLY protokollt használja, amikor az ICMP protokollt választja. A nyomkövetési útvonal a topológia információk leválasztására is szolgál.

Az alábbi táblázat az adatgyűjtési módszereket és a Hálózati teljesítményfigyelő adatgyűjtésének egyéb részleteit mutatja be.

| Platform | Közvetlen ügynök | System Center Operations Manager ügynök | Azure Storage | A műveleti vezetőre van szükség? | Az Operations Manager ügynöke által küldött adatok a felügyeleti csoporton keresztül | A gyűjtés gyakorisága |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |TCP kézfogás/ICMP ECHO üzenetek 5 másodpercenként, 3 percenként küldött adatok |
 

 
A megoldás szintetikus tranzakciókat használ a hálózat állapotának felméréséhez. A hálózati hálózatok különböző pontjain telepített Log Analytics-ügynökök tcp-csomagokat vagy ICMP Echo-t cserélnek egymással. Az, hogy az ügynökök TCP-csomagokat vagy ICMP Echo-t használnak-e, a figyelésre kiválasztott protokolltól függ. A folyamat során az ügynökök megtanulják az oda-vissza időt és a csomagvesztést, ha van ilyen. Rendszeres időközönként minden ügynök is végez nyomkövetési útvonalat más ügynökök, hogy megtalálja a különböző útvonalaka a hálózatban, hogy tesztelni kell. Ezen adatok használatával az ügynökök levezethetik a hálózati késést és a csomagvesztési adatokat. A teszteket öt másodpercenként megismételjük. Az adatokat az ügynökök körülbelül három percig összesítik, mielőtt feltöltenék azokat az Azure Monitor Log Analytics-munkaterületére.



>[!NOTE]
> Bár az ügynökök gyakran kommunikálnak egymással, nem generálnak jelentős hálózati forgalmat a tesztek végrehajtása közben. Az ügynökök csak a TCP SYN-SYNACK-ACK kézfogáscsomagokra támaszkodnak a veszteség és a késés meghatározásához. Adatcsomagok cseréje nem történt meg. A folyamat során az ügynökök csak szükség esetén kommunikálnak egymással. Az ügynök kommunikációs topológia van optimalizálva, hogy csökkentse a hálózati forgalmat.

## <a name="use-the-solution"></a>Használja a megoldást 

### <a name="network-performance-monitor-overview-tile"></a>A Hálózatteljesítmény figyelőjének áttekintő csempéje 

A Hálózati teljesítményfigyelő megoldás engedélyezése után az **Áttekintés** lap megoldáscsempéje gyors áttekintést nyújt a hálózat állapotáról. 

 ![A Hálózatteljesítmény figyelőjének áttekintő csempéje](media/network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>A Hálózati teljesítményfigyelő irányítópultja 

* **Legnépszerűbb hálózati állapotesemények:** Ez az oldal a rendszerben a legutóbbi állapotesemények és riasztások listáját, valamint az események aktívvá vált időit tartalmazza. Egy állapotesemény vagy riasztás jön létre, ha a kiválasztott metrika értéke (veszteség, késés, válaszidő vagy sávszélesség-kihasználtság) a figyelési szabály meghaladja a küszöbértéket. 

* **ExpressRoute-figyelő:** Ez a lap a megoldás által figyelt különböző ExpressRoute-társviszony-létesítési kapcsolatok állapot-összegzéseit tartalmazza. A **Topológia** csempe a hálózaton figyelt ExpressRoute-áramkörökön keresztüli hálózati elérési utak számát mutatja. Válassza ezt a csempét a **Topológia** nézet megtekintéséhez.

* **Szolgáltatáskapcsolat figyelője:** Ez az oldal a létrehozott különböző tesztek állapotösszegzéseit tartalmazza. A **Topológia** csempe a figyelt végpontok számát mutatja. Válassza ezt a csempét a **Topológia** nézet megtekintéséhez.

* **Teljesítményfigyelő**: Ez a lap a megoldás által figyelt **hálózati** és **alhálózati** kapcsolatok állapot-összefoglalóit tartalmazza. A **Topológia** csempe a hálózatban figyelt hálózati útvonalak számát mutatja. Válassza ezt a csempét a **Topológia** nézet megtekintéséhez. 

* **Gyakori lekérdezések:** Ez a lap olyan keresési lekérdezéseket tartalmaz, amelyek közvetlenül lekérik a nyers hálózati figyelési adatokat. Ezeket a lekérdezéseket kiindulási pontként használhatja a testreszabott jelentésekhez saját lekérdezések létrehozásához. 

   ![A Hálózati teljesítményfigyelő irányítópultja](media/network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>Részletezés mélységért 

A megoldás irányítópultján különböző hivatkozásokat választhat ki, hogy mélyebbre ásson bármilyen érdeklődési területen. Ha például egy riasztás vagy egy nem sérült hálózati kapcsolat jelenik meg az irányítópulton, válassza ki, hogy vizsgálja meg tovább. A lap felsorolja az adott hálózati kapcsolat hoz szükséges összes alhálózati kapcsolatot. Láthatja az egyes alhálózati kapcsolatok elvesztését, késését és állapotát. Gyorsan megtudhatja, hogy melyik alhálózati kapcsolat okoz problémákat. Válassza **a Csomópontcsatolások megtekintése** lehetőséget a nem megfelelő állapotú alhálózati kapcsolat összes csomópontkapcsolatának megtekintéséhez. Ezután megtekintheti az egyes csomópont-csomópont kapcsolatokat, és megkeresheti a nem megfelelő csomópontkapcsolatokat. 

Válassza **a Topológia megtekintése** lehetőséget a forrás- és célcsomópontok közötti útvonalak ugrásról ugrásra történő topológiájának megtekintéséhez. A nem megfelelő útvonalak pirosan jelennek meg. Megtekintheti az egyes ugrások által okozott késést, így gyorsan azonosíthatja a problémát a hálózat egy adott részén.

 

### <a name="network-state-recorder-control"></a>Hálózati állapotfelvevő vezérlése

Minden nézet egy pillanatképet jelenít meg a hálózat állapotáról egy adott időpontban. Alapértelmezés szerint a legutóbbi állapot jelenik meg. A lap tetején lévő sáv mutatja azt az időpontot, amikor az állapot megjelenik. Ha egy korábbi pillanatképet szeretne megtekinteni a hálózat állapotáról, válassza a **Műveletek**lehetőséget. Az automatikus frissítést bármely lapon engedélyezheti vagy letilthatja a legújabb állapot megtekintése közben. 

 ![Hálózati állapotrögzítő](media/network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>Trenddiagramok 

Minden szinten, amely leásás, láthatja a trend a vonatkozó metrika. Ez lehet veszteség, késés, válaszidő vagy sávszélesség-kihasználtság. A trend időintervallumának módosításához használja a diagram tetején lévő időszabályozót. 

A trenddiagramok egy teljesítménymutató teljesítményének előzményperspektíváját mutatják. Egyes hálózati problémák átmeneti jellegűek, és nehéz elkapni, ha csak a hálózat aktuális állapotát nézzük. A problémák gyorsan felszínre léphetnek, és eltűnhetnek, mielőtt bárki észrevenné, csak egy későbbi időpontban jelennek meg újra. Az ilyen átmeneti problémák az alkalmazásgazdák számára is nehézségekbe ütközhetnek. A problémák gyakran megmagyarázhatatlan növekedésként jelennek meg az alkalmazás válaszidejének, még akkor is, ha úgy tűnik, hogy az összes alkalmazás-összetevő zökkenőmentesen fut. 

Az ilyen típusú problémákat könnyedén észlelheti, ha megnézi a trenddiagramot. A probléma a hálózati késés vagy a csomagvesztés hirtelen csúcsaként jelenik meg. A probléma kivizsgálásához használja a Hálózati állapotjegyző vezérlőt a hálózati pillanatkép és a topoológia megtekintéséhez az adott időpontban, amikor a probléma történt.

 
![Trenddiagramok](media/network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>Topológia térkép 

A Hálózati teljesítményfigyelő egy interaktív topológiatérképen jeleníti meg a forrás és a célvégpont közötti útvonalak ugrásról ugrásra topológiáját. A topológiatérkép megtekintéséhez válassza a **Topológia** csempét a megoldás irányítópultján. A leásólapokon a View topology hivatkozást is **kiválaszthatja.** 

A topológiatérkép azt mutatja, hogy hány útvonal van a forrás és a cél között, és milyen útvonalakat vesznek igénybe az adatcsomagok. Az egyes hálózati ugrások által megadott késés is látható. Az összes olyan elérési út, amelynek teljes elérési útja a küszöbérték felett van (a megfelelő figyelési szabályban van beállítva) piros színnel jelenik meg. 

Amikor kijelöl egy csomópontot, vagy fölé viszi az egérmutatót a topológiatérképen, megjelennek a csomópont tulajdonságai, például a teljes tartománynév és az IP-cím. Válassza ki az ugrást az IP-cím megtekintéséhez. Azonosíthatja a problémás hálózati ugrást, ha észreveheti a késést, amelyet hozzájárul. Adott útvonalak szűréséhez használja az összecsukható műveletpanel szűrőit. A hálózati topológiák egyszerűsítése érdekében a műveletpanel csúszkájával rejtse el a köztes ugrásokat. Az egérgörgővel nagyíthat vagy kicsinyíthet a topológiatérképen. 

A térképen látható topológia a 3. 

 
![Topológia térkép](media/network-performance-monitor/topology-map.png)
 

## <a name="log-queries-in-azure-monitor"></a>Lekérdezések naplózása az Azure Monitorban

A Hálózati teljesítményfigyelő irányítópultján és leásólapjain keresztül grafikusan elérhető összes adat natív módon is elérhető a [naplólekérdezésekben.](../log-query/log-query-overview.md) Interaktív elemzést végezhet a tárházban lévő adatokról, és korrelálhatja a különböző forrásokból származó adatokat. Egyéni riasztásokat és nézeteket is létrehozhat, és exportálhatja az adatokat az Excelbe, a Power BI-ba vagy egy megosztható hivatkozásba. Az irányítópult **gyakori lekérdezések** területe néhány hasznos lekérdezést is használ kiindulási pontként saját lekérdezések és jelentések létrehozásához. 

## <a name="alerts"></a>Riasztások

A Hálózati teljesítményfigyelő az [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-unified-alerts)riasztási képességeit használja.

Ez azt jelenti, hogy minden értesítés [kezelése műveletcsoportok](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)használatával.  

Ha Ön NPM-felhasználó, aki riasztást hoz létre a Log Analytics szolgáltatással: 
1. Megjelenik egy hivatkozás, amely átirányítja az Azure Portalra. Kattintson rá a portál eléréséhez.
2. Kattintson a Hálózati teljesítményfigyelő megoldáscsempére. 
3. Keresse meg a Konfigurálás.  
4. Válassza ki azt a tesztet, amelyről riasztást szeretne létrehozni, és kövesse az alábbi lépéseket.

Ha Ön NPM-felhasználó, aki riasztást hoz létre az Azure Portalon keresztül:  
1. Választhat, hogy közvetlenül adja meg az e-mailcímét, vagy létrehozhat riasztásokat műveletcsoportokon keresztül.
2. Ha úgy dönt, hogy közvetlenül adja meg az e-mail címét, létrejön egy **NPM Email ActionGroup** nevű műveletcsoport, és az e-mail azonosító hozzáadódik az adott műveletcsoporthoz.
3. Ha úgy dönt, hogy műveletcsoportokat használ, ki kell választania egy korábban létrehozott műveletcsoportot. Itt megtudhatja, hogyan hozhat létre [műveletcsoportot.](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups#create-an-action-group-by-using-the-azure-portal) 
4. A riasztás sikeres létrehozása után a Riasztások kezelése hivatkozással kezelheti a riasztásokat. 

Minden alkalommal, amikor létrehoz egy riasztást, Az NPM létrehoz egy lekérdezésalapú naplóriasztási szabályt az Azure Monitorban. Ez a lekérdezés alapértelmezés szerint 5 percenként aktiválódik. Az Azure-figyelő nem számít fel díjat az első 250 naplóriasztási szabályért, és a 250-es naplóriasztási szabályok korlátja feletti riasztási szabályokat a riasztások díjszabása szerint számlázunk az [Azure Monitor díjszabási lapján.](https://azure.microsoft.com/pricing/details/monitor/)
Az értesítések díja külön-külön kerül felszámításra [az Azure Monitor díjszabási lapján.](https://azure.microsoft.com/pricing/details/monitor/)


## <a name="pricing"></a>Díjszabás

Az árképzésre vonatkozó információk [online](network-performance-monitor-pricing-faq.md)érhetők el.

## <a name="provide-feedback"></a>Visszajelzés küldése 

* **UserVoice:** A Hálózati teljesítményfigyelő funkcióival kapcsolatos ötleteit közzéteheti, amelyeken azt szeretné, hogy dolgozzunk. Látogasson el a [UserVoice oldalra](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring). 

* **Csatlakozzon kohorszunkhoz:** Mindig is szeretnénk, ha új ügyfelek csatlakoznának a kohorszunkhoz. Ennek részeként korai hozzáférést kap az új funkciókhoz, és lehetőséget kap arra, hogy segítsen javítani a Hálózati teljesítményfigyelőt. Ha szeretne csatlakozni, töltse ki ezt a [gyors felmérést](https://aka.ms/npmcohort). 

## <a name="next-steps"></a>További lépések 
További információ a [Teljesítményfigyelőről,](network-performance-monitor-performance-monitor.md) [a Szolgáltatáskapcsolat-figyelőről](network-performance-monitor-performance-monitor.md)és az [ExpressRoute-figyelőről.](network-performance-monitor-expressroute.md) 
