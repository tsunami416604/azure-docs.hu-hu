---
title: Gyakran feltett kérdések – Hálózati teljesítményfigyelő megoldás az Azure-ban | Microsoft dokumentumok
description: Ez a cikk az Azure-beli Hálózati teljesítményfigyelővel kapcsolatos gyakori kérdéseket rögzíti. A Hálózati teljesítményfigyelő (NPM) segítségével közel valós időben figyelheti a hálózatok teljesítményét, és észlelheti és megkeresheti a hálózati teljesítménybeli szűk keresztmetszeteket.
ms.subservice: logs
ms.topic: conceptual
author: vinynigam
ms.author: vinigam
ms.date: 10/12/2018
ms.openlocfilehash: 443e4b44633e949dd9bd55df1ec7d18ca93d6e04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79096228"
---
# <a name="network-performance-monitor-solution-faq"></a>Gyakori kérdések a Hálózati teljesítményfigyelő megoldásáról

![Hálózati teljesítményfigyelő szimbólum](media/network-performance-monitor-faq/npm-symbol.png)

Ez a cikk az Azure-ban a Hálózati teljesítményfigyelővel (NPM) kapcsolatos gyakori kérdéseket (GYIK) tartalmazza

[A Network Performance Monitor](/azure/networking/network-monitoring-overview) egy felhőalapú [hibrid hálózatfigyelési](../../azure-monitor/insights/network-performance-monitor-performance-monitor.md) megoldás, amely segít a hálózati infrastruktúra különböző pontjai közötti hálózati teljesítmény figyelésében. Emellett segít a [szolgáltatás- és alkalmazásvégpontokhoz](../../azure-monitor/insights/network-performance-monitor-service-connectivity.md) való hálózati kapcsolat figyelésében, valamint [az Azure ExpressRoute teljesítményének figyelésében.](../../azure-monitor/insights/network-performance-monitor-expressroute.md) 

A Hálózati teljesítményfigyelő észleli az olyan hálózati problémákat, mint a forgalom blackholing, útválasztási hibák és a hagyományos hálózati figyelési módszerek által nem észlelt problémák. A megoldás riasztásokat készít, és értesíti, amikor egy hálózati kapcsolat meghaladja a küszöbértéket. Emellett biztosítja a hálózat teljesítményével kapcsolatos problémák időbeni észlelését és leszűkíti a hiba forrásának helyszínét egy hálózati szegmensre vagy eszközre. 

A [Hálózati teljesítményfigyelő](https://docs.microsoft.com/azure/networking/network-monitoring-overview) által támogatott különböző funkciókról további információ érhető el az interneten.

## <a name="set-up-and-configure-agents"></a>Ügynökök beállítása és konfigurálása

### <a name="what-are-the-platform-requirements-for-the-nodes-to-be-used-for-monitoring-by-npm"></a>Milyen platformkövetelmények vonatkoznak az NPM általfigyelandó csomópontokra?
Az alábbiakban felsoroljuk az NPM különböző képességeinek platformkövetelményeit:

- Az NPM Teljesítményfigyelő és szolgáltatáskapcsolat-figyelő képességei a Windows server és a Windows asztali/ügyféloperációs rendszereket egyaránt támogatják. A Windows server operációs rendszer támogatott verziói a 2008 SP1 vagy újabb verziók. A támogatott Windows asztali/ügyfélverziók a Következők: Windows 10, Windows 8.1, Windows 8 és Windows 7. 
- Az NPM ExpressRoute-figyelő funkciója csak a Windows server (2008 SP1 vagy újabb) operációs rendszert támogatja.

### <a name="can-i-use-linux-machines-as-monitoring-nodes-in-npm"></a>Használhatom a Linux-gépeket figyelési csomópontként az NPM-ben?
A Linux-alapú csomópontok használatával a hálózatok figyelésének képessége jelenleg előzetes verzióban érhető el. További tudnivalókért fordulj on-a Account Manager hez. A Linux-ügynökök csak az NPM Teljesítményfigyelő képességéhez biztosítanak figyelési képességet, és nem érhetők el a Szolgáltatáskapcsolat-figyelő és az ExpressRoute-figyelő képességeihez

### <a name="what-are-the-size-requirements-of-the-nodes-to-be-used-for-monitoring-by-npm"></a>Milyen méretkövetelmények vonatkoznak az NPM általfigyelandó csomópontokra?
Az NPM-megoldás csomópontokon futó virtuális gépeken a hálózatok figyeléséhez a csomópontoknak legalább 500 MB memóriával és egy maggal kell rendelkezniük. Az NPM futtatásához nem kell külön csomópontokat használnia. A megoldás futtatható csomópontokon, amelyek más számítási feladatok futnak rajta. A megoldás képes leállítani a figyelési folyamatot, ha több mint 5% CPU-t használ.

### <a name="to-use-npm-should-i-connect-my-nodes-as-direct-agent-or-through-system-center-operations-manager"></a>Az NPM használatához közvetlen ügynökként vagy a System Center Operations Manager en keresztül csatlakoztassam a csomópontjaimat?
Mind a Teljesítményfigyelő, mind a Szolgáltatáskapcsolat-figyelő képességei támogatják a [közvetlen ügynökként összekapcsolt](../../azure-monitor/platform/agent-windows.md) és az [Operations Manager en keresztül csatlakoztatott csomópontokat.](../../azure-monitor/platform/om-agents.md)

ExpressRoute-figyelő képesség esetén az Azure-csomópontokcsak közvetlen ügynökként kell csatlakozniuk. Az Operations Manager en keresztül csatlakoztatott Azure-csomópontok nem támogatottak. A helyszíni csomópontok esetében a közvetlen ügynökként és az Operations Manageren keresztül csatlakoztatott csomópontok támogatják az ExpressRoute-kapcsolat figyelését.

### <a name="which-protocol-among-tcp-and-icmp-should-be-chosen-for-monitoring"></a>Melyik protokollt kell kiválasztani a TCP és az ICMP között a monitorozáshoz?
Ha a hálózatot Windows kiszolgálóalapú csomópontokkal figyeli, javasoljuk, hogy a TCP protokollt használja figyelési protokollként, mivel az nagyobb pontosságot biztosít. 

Az ICMP használata Windows asztali/ügyféloperációs rendszeralapú csomópontokhoz ajánlott. Ez a platform nem teszi lehetővé a TCP-adatok nyers szoftvercsatornákon keresztüli küldését, amelyet az NPM a hálózati topológia felderítésére használ.

További részleteket az egyes protokollok relatív előnyeiről [itt kaphat.](../../azure-monitor/insights/network-performance-monitor-performance-monitor.md#choose-the-protocol)

### <a name="how-can-i-configure-a-node-to-support-monitoring-using-tcp-protocol"></a>Hogyan konfigurálhatok egy csomópontot a TCP protokoll használatával történő figyelés támogatására?
A csomópont számára a TCP protokoll használatával történő figyelés támogatása: 
* Győződjön meg arról, hogy a csomópont platformja Windows Server (2008 SP1 vagy újabb).
* Futtassa [az EnableRules.ps1](https://aka.ms/npmpowershellscript) Powershell parancsfájlt a csomóponton. További részleteket az [utasításokban](../../azure-monitor/insights/network-performance-monitor.md#configure-log-analytics-agents-for-monitoring) talál.


### <a name="how-can-i-change-the-tcp-port-being-used-by-npm-for-monitoring"></a>Hogyan módosíthatom az NPM által a figyeléshez használt TCP-portot?
Az [EnableRules.ps1](https://aka.ms/npmpowershellscript) parancsfájl futtatásával módosíthatja az NPM által a figyeléshez használt TCP-portot. Meg kell adnia a paraméterként használni kívánt portszámot. Ha például engedélyezni szeretné a TCP protokollt a 8060-as porton, futtassa a futtassa a futtassa a t. `EnableRules.ps1 8060` Győződjön meg arról, hogy ugyanazt a TCP-portot használja a figyeléshez használt összes csomóponton.

A parancsfájl csak a Windows tűzfalat konfigurálja helyileg. Ha hálózati tűzfal- vagy hálózati biztonsági csoportra (NSG) vonatkozó szabályokkal rendelkezik, győződjön meg arról, hogy engedélyezik az NPM által használt TCP-portra szánt forgalmat.

### <a name="how-many-agents-should-i-use"></a>Hány ügynököt használjak?
Minden figyelni kívánt alhálózathoz legalább egy ügynököt kell használnia.

### <a name="what-is-the-maximum-number-of-agents-i-can-use-or-i-see-error--youve-reached-your-configuration-limit"></a>Mi a maximális számú ügynökök tudom használni, vagy látom hiba ".... elérte a konfigurációs korlátot"?
Az NPM munkaterületenként 5000 IP-re korlátozza az IP-k számát. Ha egy csomópont iPv4- és IPv6-címekkel is rendelkezik, ez az adott csomópont 2 IP-címének számít. Ezért ez az 5000 IP-re vonatkozó korlát határozná meg az ügynökök számára vonatkozó felső határt. Az Inaktív ügynökök törölhetők az NPM >> Konfigurálás csoportjának Csomópontjai lapjáról. Az NPM az ügynöket üzemeltető virtuális géphez valaha is hozzárendelt ip-címeket is megőrzi, és mindegyik külön IP-címnek számít, amely hozzájárul az 5000 IP-cím felső határához. A munkaterület IP-szolgáltatóinak felszabadításához a Csomópontok lapon törölheti a nem használt IP-ket.

## <a name="monitoring"></a>Figyelés

### <a name="how-are-loss-and-latency-calculated"></a>Hogyan számítják ki a veszteséget és a késést?
A forrásügynökök vagy TCP SYN-kérelmeket (ha a TCP-t választják a figyelési protokollként) vagy ICMP ECHO-kérelmeket (ha az ICMP-t választják a figyelésprotokollként) rendszeres időközönként küldik a cél IP-címnek annak biztosítása érdekében, hogy a forrás-cél IP-cím közötti összes elérési út kombináció takarja. A fogadott csomagok és a csomag-oda-vissza utazási idő százalékos arányát mérik az egyes útvonalak veszteségének és késésének kiszámításához. Ezeket az adatokat a lekérdezési időköz és az összes elérési út összesített értékek lekéri az IP-kombináció az adott lekérdezési időköz.

### <a name="with-what-frequency-does-the-source-agent-send-packets-to-the-destination-for-monitoring"></a>Milyen gyakorisággal küld a forrásügynök csomagokat a célhelyre figyelésre?
A Teljesítményfigyelő és az ExpressRoute-figyelő képességeinek a forrás 5 másodpercenként küldi a csomagokat, és rögzíti a hálózati méréseket. Ezeket az adatokat egy 3 perces lekérdezési időközön keresztül összesíti a veszteség és a késés átlagos és csúcsértékeinek kiszámításához. A Szolgáltatáskapcsolat-figyelő képesség esetében a csomagok hálózati mérésre való küldésének gyakoriságát a felhasználó által az adott teszthez a teszt konfigurálása során megadott gyakoriság határozza meg.

### <a name="how-many-packets-are-sent-for-monitoring"></a>Hány csomagot küld a rendszer figyelésre?
A forrásügynök által a céllekérdezésbe küldött csomagok száma adaptív, és a saját algoritmusunk határozza meg, amely különböző lehet a különböző hálózati topológiák esetében. A forrás-cél IP-kombináció közötti hálózati útvonalak száma több az elküldött csomagok száma. A rendszer biztosítja, hogy a forrás-cél IP-kombináció közötti összes elérési utat lefedje a rendszer.

### <a name="how-does-npm-discover-network-topology-between-source-and-destination"></a>Hogyan deríti fel az NPM a hálózati topológiát a forrás és a cél között?
Az NPM a Traceroute-on alapuló saját algoritmust használ a forrás és a cél közötti összes útvonal és ugrás felderítésére.

### <a name="does-npm-provide-routing-and-switching-level-info"></a>Az NPM biztosítja az útválasztási és kapcsolási szint adatait 
Bár az NPM képes észlelni a forrásügynök és a cél közötti összes lehetséges útvonalat, nem biztosít betekintést abba, hogy az adott számítási feladatok által küldött csomagok melyik útvonalat vitték. A megoldás segítségével azonosíthatja az elérési utakat és az alapul szolgáló hálózati ugrásokat, amelyek a vártnál több késést adnak hozzá.

### <a name="why-are-some-of-the-paths-unhealthy"></a>Miért nem megfelelőek az elérési utak?
A forrás- és a célIP-ek között különböző hálózati elérési utak létezhetnek, és minden elérési út nak eltérő veszteség- és késésértéke lehet. Az NPM azokat az elérési utakat nem kifogástalanállapotúként jelöli meg (piros színnel jelölve), amelyek nél a veszteség és/vagy késés értéke nagyobb, mint a figyelési konfigurációban beállított küszöbérték.

### <a name="what-does-a-hop-in-red-color-signify-in-the-network-topology-map"></a>Mit jelent a piros színű ugrás a hálózati topológia térképen?
Ha egy ugrás piros, azt jelzi, hogy legalább egy nem megfelelő elérési út része. Az NPM csak az elérési utakat jelöli meg nem kifogástalanállapotúként, nem különíti el az egyes útvonalak állapotát. A problémás ugrások azonosításához megtekintheti az ugrásról ugrásra ugrásra késést, és elkülönítheti a vártnál több késést hozzáadó késést.

### <a name="how-does-fault-localization-in-performance-monitor-work"></a>Hogyan működik a hibahovanodás a Teljesítményfigyelőben?
Az NPM valószínűségi mechanizmust használ a hibavalószínűségek hozzárendelésére minden egyes hálózati útvonalhoz, hálózati szegmenshez és az összetevők hálózati ugrásaihoz a nem megfelelő állapotú elérési utak száma alapján. Ahogy a hálózati szegmensek és ugrások egyre több nem megfelelő elérési út részévé válnak, a hozzájuk társított hibavalószínűség nő. Ez az algoritmus akkor működik a legjobban, ha sok csomópont van, amelyekhez az NPM-ügynök csatlakozik egymáshoz, mivel ez növeli a hibavalószínűségek kiszámításához szükséges adatpontokat.

### <a name="how-can-i-create-alerts-in-npm"></a>Hogyan hozhatok létre riasztásokat az NPM-ben?
A [részletes útmutatást a dokumentáció figyelmeztetésekkel kapcsolatos szakaszában](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor#alerts) találja.

### <a name="what-are-the-default-log-analytics-queries-for-alerts"></a>Melyek a riasztások alapértelmezett Log Analytics-lekérdezései
Teljesítményfigyelő lekérdezés

    NetworkMonitoring 
     | where (SubType == "SubNetwork" or SubType == "NetworkPath") 
     | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy") and RuleName == "<<your rule name>>"
    
Szolgáltatáskapcsolat figyelőlekérdezése

    NetworkMonitoring                 
     | where (SubType == "EndpointHealth" or SubType == "EndpointPath")
     | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or ServiceResponseHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy") and TestName == "<<your test name>>"
    
ExpressRoute-figyelő lekérdezései: Áramkörök lekérdezés

    NetworkMonitoring
    | where (SubType == "ERCircuitTotalUtilization") and (UtilizationHealthState == "Unhealthy") and CircuitResourceId == "<<your circuit resource ID>>"

Magánhálózati társviszony-létesítés

    NetworkMonitoring 
     | where (SubType == "ExpressRoutePeering" or SubType == "ERVNetConnectionUtilization" or SubType == "ExpressRoutePath")   
    | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitName == "<<your circuit name>>" and VirtualNetwork == "<<vnet name>>"

Microsoft társviszony-létesítés

    NetworkMonitoring 
     | where (SubType == "ExpressRoutePeering" or SubType == "ERMSPeeringUtilization" or SubType == "ExpressRoutePath")
    | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and CircuitName == ""<<your circuit name>>" and PeeringType == "MicrosoftPeering"

Gyakori lekérdezés   

    NetworkMonitoring
    | where (SubType == "ExpressRoutePeering" or SubType == "ERVNetConnectionUtilization" or SubType == "ERMSPeeringUtilization" or SubType == "ExpressRoutePath")
    | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") 

### <a name="can-npm-monitor-routers-and-servers-as-individual-devices"></a>Az NPM figyelheti az útválasztókat és a kiszolgálókat egyedi eszközként?
Az NPM csak az alapul szolgáló hálózati ugrások (kapcsolók, útválasztók, kiszolgálók stb.) IP- és állomásnevét azonosítja a forrás- és célIP-cím között. Azt is azonosítja a késés között azonosított ugrások. Nem egyénileg figyeli ezeket az alapul szolgáló ugrásokat.

### <a name="can-npm-be-used-to-monitor-network-connectivity-between-azure-and-aws"></a>Használható az NPM az Azure és az AWS közötti hálózati kapcsolat figyelésére?
Igen. A részleteket az Azure, az [AWS és a helyszíni hálózatok npm használatával című](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/) cikkben olvashat.

### <a name="is-the-expressroute-bandwidth-usage-incoming-or-outgoing"></a>Bejövő vagy kimenő az ExpressRoute sávszélesség-használat?
A sávszélesség-használat a bejövő és kimenő sávszélesség összege. Bit/mp-ben van kifejezve.

### <a name="can-we-get-incoming-and-outgoing-bandwidth-information-for-the-expressroute"></a>Kaphatunk bejövő és kimenő sávszélesség-információkat az ExpressRoute-hoz?
Az elsődleges és a másodlagos sávszélesség bejövő és kimenő értékei rögzíthetők.

Az MS társviszony-létesítési szintre vonatkozó információkhoz használja az alábbi lekérdezést a Naplókeresés ben

    NetworkMonitoring 
     | where SubType == "ERMSPeeringUtilization"
     | project  CircuitName,PeeringName,PrimaryBytesInPerSecond,PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond
    
A privát társviszony-létesítési szinttel kapcsolatos információkhoz használja az alábbi lekérdezést a Naplókeresés ben

    NetworkMonitoring 
     | where SubType == "ERVNetConnectionUtilization"
     | project  CircuitName,PeeringName,PrimaryBytesInPerSecond,PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond
  
Az áramköri szintű információkért használja az alábbi lekérdezést a Naplókeresés ben

    NetworkMonitoring 
        | where SubType == "ERCircuitTotalUtilization"
        | project CircuitName, PrimaryBytesInPerSecond, PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond

### <a name="which-regions-are-supported-for-npms-performance-monitor"></a>Mely régiók támogatottak az NPM teljesítményfigyelője számára?
Az NPM a világ bármely részén, a [támogatott régiók](../../azure-monitor/insights/network-performance-monitor.md#supported-regions) egyikében üzemeltetett munkaterületről képes figyelni a hálózatok közötti kapcsolatot

### <a name="which-regions-are-supported-for-npms-service-connectivity-monitor"></a>Mely régiók támogatottak az NPM szolgáltatáskapcsolat-figyelőjében?
Az NPM a világ bármely részén, a [támogatott régiók](../../azure-monitor/insights/network-performance-monitor.md#supported-regions) egyikében üzemeltetett munkaterületről figyelheti a szolgáltatásokkal való kapcsolatot

### <a name="which-regions-are-supported-for-npms-expressroute-monitor"></a>Mely régiók támogatottak az NPM ExpressRoute-figyelőjében?
Az NPM figyelheti az ExpressRoute-áramkörökbármely Azure-régióban található. Az NPM-hez való alaplaphoz olyan Log Analytics-munkaterületre van szükség, amelyet a támogatott régiók egyikében kell [üzemeltetni.](/azure/expressroute/how-to-npm)

## <a name="troubleshoot"></a>Hibaelhárítás

### <a name="why-are-some-of-the-hops-marked-as-unidentified-in-the-network-topology-view"></a>Miért van néhány ugrás azonosítatlanként megjelölve a hálózati topológia nézetben?
Az NPM a traceroute módosított verzióját használja a topológia felderítéséhez a forrásügynöktől a célig. Egy azonosítatlan ugrás azt jelzi, hogy a hálózati ugrás nem válaszolt a forrásügynök traceroute-kérelmére. Ha három egymást követő hálózati ugrás nem válaszol az ügynök nyomkövetési útvonalára, a megoldás azonosítatlanként jelöli meg a nem válaszoló ugrásokat, és nem próbál meg több ugrást észlelni.

Előfordulhat, hogy egy ugrás nem válaszol egy nyomkövetési útvonalra az alábbi esetek közül egy vagy több esetén:

* Az útválasztók úgy vannak beállítva, hogy ne fedjék fel identitásukat.
* A hálózati eszközök nem engedélyezik ICMP_TTL_EXCEEDED forgalmat.
* A tűzfal blokkolja a hálózati eszköz ICMP_TTL_EXCEEDED válaszát.

Ha a végpontok bármelyike az Azure-ban található, traceroute azonosítatlan ugrásokat jelenít meg, mivel az Azure Infrastructure nem fedi fel a traceroute identitását. 

### <a name="i-get-alerts-for-unhealthy-tests-but-i-do-not-see-the-high-values-in-npms-loss-and-latency-graph-how-do-i-check-what-is-unhealthy"></a>Riasztást kapok a nem megfelelő állapotú tesztekhez, de nem látom a magas értékeket az NPM veszteség- és késleltetési grafikonjában. Hogyan ellenőrizhetem, hogy mi nem megfelelő?
Az NPM riasztást küld, ha a forrás és a cél közötti végpontok közötti késés átlépi a köztük lévő útvonalak küszöbértékét. Egyes hálózatok több útvonala is kapcsolódik ugyanahhoz a forráshoz és célhoz. Az NPM riasztást küld, amely szerint minden elérési út nem kifogástalan. A grafikonokon látható veszteség és késés az összes útvonal átlagos értéke, ezért előfordulhat, hogy nem mutatja egyetlen görbe pontos értékét. Ha meg szeretné tudni, hogy hol törta meg a küszöbértéket, keresse meg a riasztás "SubType" oszlopát. Ha a problémát egy elérési út okozza, a SubType érték a NetworkPath (teljesítményfigyelő tesztekhez), az EndpointPath (a Szolgáltatáskapcsolat-figyelő tesztekhez) és az ExpressRoutePath (ExpressRotue figyelőtesztekhez). 

A megkeresni kívánt mintalekérdezés elérési útja nem kifogástalan:

    NetworkMonitoring 
    | where ( SubType == "ExpressRoutePath")
    | where (LossHealthState == "Unhealthy" or LatencyHealthState == "Unhealthy" or UtilizationHealthState == "Unhealthy") and          CircuitResourceID =="<your ER circuit ID>" and ConnectionResourceId == "<your ER connection resource id>"
    | project SubType, LossHealthState, LatencyHealthState, MedianLatency 

### <a name="why-does-my-test-show-unhealthy-but-the-topology-does-not"></a>Miért nem kifogástalan a tesztem, de a topológia nem 
Az NPM különböző időközönként figyeli a végpontok közötti veszteséget, késést és topológiát. A veszteséget és a késést 5 másodpercenként egyszer mérik, és hárompercenként összesítik (a Teljesítményfigyelő és az Expressz útvonal-figyelő esetében), míg a topológiát a traceroute 10 percenként egyszer történő kiszámítása korként számítja ki. Például 3:44 és 4:04 között a topológia háromszor frissíthető (3:44, 3:54, 4:04), de a veszteség és a késés körülbelül hétalkalommal frissül (3:44, 3:47, 3:50, 3:53, 3:56, 3:59, 4:02). A 3:54-kor létrehozott topológia a 3:56, 3:59 és 4:02-kor kiszámított veszteség és késés miatt jelenik meg. Tegyük fel, hogy riasztást kap arról, hogy az ER-áramkör 3:59-kor nem kifogástalan állapotú. Jelentkezzen be az NPM-be, és próbálja meg a topológia idejét 3:59-re állítani. Az NPM 3:54-kor hozza létre a topológiát. A hálózat utolsó ismert topológiájának megértéséhez hasonlítsa össze az Időfeldolgozott mezőket (a veszteség és a késés kiszámításának időpontjában) és a TracerouteCompletedTime(a topológia számításának időpontja). 

### <a name="what-is-the-difference-between-the-fields-e2emedianlatency-and-avghoplatencylist-in-the-networkmonitoring-table"></a>Mi a különbség az E2EMedianLatency és az AvgHopLatencyList mezők között a NetworkMonitoring táblában?
Az E2EMedianLatency a tcp ping tesztek eredményeinek összesítése után hárompercenként frissített késés, míg az AvgHopLatencyList 10 percenként frissül a traceroute alapján. Az E2EMedianLatency kiszámításának pontos időpontjának megértéséhez használja a TimeProcessed mezőt. A traceroute befejezésének és frissítésének pontos időpontjához használja a TracerouteCompletedTime mezőt.

### <a name="why-does-hop-by-hop-latency-numbers-differ-from-hoplatencyvalues"></a>Miért különböznek az ugrásról ugrásra leskelődési számok a HopLatencyValues értékektől? 
A HopLatencyValues a végpont forrása.
Például: Ugrások - A,B, C. AvgHopLatency - 10,15,20. Ez azt jelenti, forrás A késés = 10, forrás B késés = 15 és a forrás a C késés 20. A felhasználói felület kiszámítja az A-B ugrás késleltetést 5-tel a topológiában

### <a name="the-solution-shows-100-loss-but-there-is-connectivity-between-the-source-and-destination"></a>A megoldás 100%-os veszteséget mutat, de van kapcsolat a forrás és a cél között
Ez akkor fordulhat elő, ha a gazdatűzfal vagy a köztes tűzfal (hálózati tűzfal vagy Az Azure NSG) blokkolja a forrásügynök és az NPM által figyelésre használt porton keresztüli cél közötti kommunikációt (alapértelmezés szerint a port 8084, kivéve, ha a port ügyfél megváltoztatta ezt).

* Annak ellenőrzéséhez, hogy a gazdatűzfal nem blokkolja-e a kommunikációt a szükséges porton, tekintse meg a forrás- és célcsomópontok állapotát a következő nézetből: Hálózati teljesítményfigyelő -> konfiguráció -> csomópontok. 
  Ha nem megfelelőállapotban vannak, tekintse meg az utasításokat, és tegye meg a szükséges lépéseket. Ha a csomópontok kifogástalanok, lépjen a b lépésre. az alábbiakban.
* Annak ellenőrzéséhez, hogy egy köztes hálózati tűzfal vagy az Azure NSG nem blokkolja a kommunikációt a szükséges porton, használja a külső psping segédprogramot az alábbi utasítások használatával:
  * psping segédprogram letölthető [itt](https://technet.microsoft.com/sysinternals/psping.aspx) 
  * Futtassa a következő parancsot a forráscsomópontról.
    * psping -n \<15 célcsomópont\>IPAddress :portNumber Alapértelmezés szerint az NPM 8084-es portot használ. Abban az esetben, ha ezt kifejezetten módosította az EnableRules.ps1 parancsfájl használatával, adja meg a használt egyéni portszámot). Ez egy ping az Azure-gépről a helyszíni
* Ellenőrizze, hogy a pingelések sikeresek-e. Ha nem, akkor azt jelzi, hogy egy köztes hálózati tűzfal vagy az Azure NSG blokkolja a forgalmat ezen a porton.
* Most futtassa a parancsot a célcsomópontról a forráscsomópont IP-címére.


### <a name="there-is-loss-from-node-a-to-b-but-not-from-node-b-to-a-why"></a>Az A csomópontból B csomópontba veszteség van, de a B csomópontból nem. miért?
Mivel az A és B közötti hálózati útvonalak eltérhetnek a B és A közötti hálózati útvonalaktól, a veszteség és a késés különböző értékei figyelhetők meg.

### <a name="why-are-all-my-expressroute-circuits-and-peering-connections-not-being-discovered"></a>Miért nem derítik fel az összes ExpressRoute-kapcsolatomat és társviszony-létesítési kapcsolatomat?
Az NPM mostantól felderíti az ExpressRoute-áramköröket és társviszony-létesítési kapcsolatokat minden olyan előfizetésben, amelyhez a felhasználó hozzáfér. Válassza ki az összes olyan előfizetést, amelyhez az Expressz útvonal-erőforrások kapcsolódnak, és engedélyezze az egyes felderített erőforrások figyelését. Az NPM megkeresi a kapcsolatobjektumokat a privát társviszony-létesítés felderítésekénekéneksorán, ezért ellenőrizze, hogy a társviszony-létesítéshez tartozik-e virtuális hálózat.

### <a name="the-er-monitor-capability-has-a-diagnostic-message-traffic-is-not-passing-through-any-circuit-what-does-that-mean"></a>Az ER-figyelő képesség diagnosztikai üzenettel rendelkezik: "A forgalom nem halad át semmilyen áramkörön". Ez mit jelent?

Lehet, hogy van egy forgatókönyv, ahol a helyszíni és az Azure-csomópontok között kifogástalan kapcsolat van, de a forgalom nem megy át az NPM által figyelt ExpressRoute-áramkörön. 

Ez a következő esetekben fordulhat elő:

* A er áramkör leállt.
* Az útvonalszűrők úgy vannak konfigurálva, hogy elsőbbséget biztosítsanak más útvonalaknak (például VPN-kapcsolatnak vagy más ExpressRoute-kapcsolatnak) a tervezett ExpressRoute-áramkörhöz képest. 
* A helyszíni és az Azure-csomópontok kiválasztott figyelése az ExpressRoute-kapcsolat a figyelési konfigurációban, nem rendelkeznek egymással a kívánt ExpressRoute-kapcsolat on the intended ExpressRoute-kapcsolat. Győződjön meg arról, hogy a figyelni kívánt ExpressRoute-kapcsolaton keresztül megfelelő csomópontokat választott egymáshoz.

### <a name="while-configuring-monitoring-of-my-expressroute-circuit-the-azure-nodes-are-not-being-detected"></a>Az ExpressRoute-kapcsolatfigyelés konfigurálása közben az Azure-csomópontok nem észlelhetők.
Ez akkor fordulhat elő, ha az Azure-csomópontok az Operations Manager en keresztül csatlakoznak. Az ExpressRoute-figyelő képesség csak azokat az Azure-csomópontokat támogatja, amelyek közvetlen ügynökként kapcsolódnak.

### <a name="i-cannot-discover-by-expressroute-circuits-in-the-oms-portal"></a>Nem tudok ExpressRoute-áramkörök segítségével felfedezni az OMS-portálon
Bár az NPM az Azure Portalon és az OMS-portálon is használható, az ExpressRoute-figyelőben lévő körfelderítés csak az Azure Portalon keresztül működik. Miután az azure-portálon keresztül felderített ék az áramköröket, a két portál valamelyikében használhatja a funkciót. 

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-network-loss-as-well-as-latency-are-shown-as-na"></a>A Szolgáltatáskapcsolat-figyelő képességében a szolgáltatás válaszideje, a hálózati veszteség és a késés NA-ként jelenik meg
Ez akkor fordulhat elő, ha egy vagy több igaz:

* A szolgáltatás nak vége.
* A szolgáltatáshálózati kapcsolat ellenőrzésére használt csomópont nem működik.
* A tesztkonfigurációban megadott cél helytelen.
* A csomópont nem rendelkezik hálózati kapcsolattal.

### <a name="in-the-service-connectivity-monitor-capability-a-valid-service-response-time-is-shown-but-network-loss-as-well-as-latency-are-shown-as-na"></a>A Szolgáltatáskapcsolat-figyelő ben érvényes szolgáltatásválaszidő jelenik meg, de a hálózati veszteség és a késés NA-ként jelenik meg.
 Ez akkor fordulhat elő, ha egy vagy több igaz:

* Ha a szolgáltatás hálózati kapcsolatának ellenőrzésére használt csomópont egy Windows-ügyfélszámítógép, vagy a célszolgáltatás blokkolja az ICMP-kérelmeket, vagy egy hálózati tűzfal blokkolja a csomópontból származó ICMP-kérelmeket.
* A Hálózati mérések végrehajtása jelölőnégyzet üres a tesztkonfigurációban.

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-is-na-but-network-loss-as-well-as-latency-are-valid"></a>A Szolgáltatáskapcsolat-figyelő képességben a szolgáltatás válaszideje NA, de a hálózati veszteség és a késés érvényes
Ez akkor fordulhat elő, ha a célszolgáltatás nem webalkalmazás, de a teszt webes tesztként van konfigurálva. A tesztkonfiguráció szerkesztése, és válassza a teszttípusát hálózatként a web helyett.

## <a name="miscellaneous"></a>Egyéb

### <a name="is-there-a-performance-impact-on-the-node-being-used-for-monitoring"></a>Van-e teljesítményhatása a figyeléshez használt csomópontra?
Az NPM-folyamat úgy van beállítva, hogy leálljon, ha a gazdaprocesszor-erőforrások több mint 5%-át használja. Ez biztosítja, hogy a csomópontok at a szokásos számítási feladatok a teljesítmény befolyásolása nélkül továbbra is használhatja.

### <a name="does-npm-edit-firewall-rules-for-monitoring"></a>Szerkeszti az NPM a figyelési tűzfalszabályokat?
Az NPM csak egy helyi Windows tűzfalszabályt hoz létre azon a csomóponton, amelyen az EnableRules.ps1 Powershell parancsfájl fut, hogy az ügynökök tcp-kapcsolatokat hozzanak létre egymással a megadott porton. A megoldás nem módosítja a hálózati tűzfalat vagy a hálózati biztonsági csoport (NSG) szabályait.

### <a name="how-can-i-check-the-health-of-the-nodes-being-used-for-monitoring"></a>Hogyan ellenőrizhetem a figyeléshez használt csomópontok állapotát?
A figyeléshez használt csomópontok állapotát a következő nézetből tekintheti meg: Hálózati teljesítményfigyelő -> Konfiguráció -> csomópontok. Ha egy csomópont nem kifogástalan állapotú, megtekintheti a hiba részleteit, és végrehajthatja a javasolt műveletet.

### <a name="can-npm-report-latency-numbers-in-microseconds"></a>Az NPM jelentheti a késésszámokat mikroszekundumokban?
Az NPM a késésszámokat a felhasználói felületen és ezredmásodpercben kerekíti. Ugyanezeket az adatokat nagyobb részletességgel (néha akár négy tizedesjegypontossággal) tárolják.

## <a name="next-steps"></a>További lépések

- A Hálózati teljesítményfigyelőről az [Azure Hálózati teljesítményfigyelő megoldására](../../azure-monitor/insights/network-performance-monitor.md)hivatkozva olvashat bővebben.
