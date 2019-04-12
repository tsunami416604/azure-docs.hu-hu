---
title: Gyakori kérdések – a Network Performance Monitor megoldás az Azure-ban |} A Microsoft Docs
description: Ez a cikk az npm-et az Azure-ban – gyakori kérdések rögzíti. Network Performance monitort (NPM) segítségével, közel valós időben, és keresse meg a hálózatok teljesítményének megfigyelése a teljesítmény szűk hálózati.
services: log-analytics
documentationcenter: ''
author: vinynigam
manager: agummadi
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/12/2018
ms.author: vinynigam
ms.openlocfilehash: 285f29055d0ac9ba656ec828972ef15f4c0d36c7
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/11/2019
ms.locfileid: "59496964"
---
# <a name="network-performance-monitor-solution-faq"></a>Network Performance Monitor megoldás – gyakori kérdések

![Hálózati Teljesítményfigyelő szimbólum](media/network-performance-monitor-faq/npm-symbol.png)

Ez a cikk rögzíti a gyakori kérdések (GYIK) kapcsolatos Network Performance monitort (NPM) az Azure-ban

[Network Performance Monitor](/azure/networking/network-monitoring-overview) egy felhőalapú [hibrid hálózatfigyelés](../../azure-monitor/insights/network-performance-monitor-performance-monitor.md) megoldás, amely segítséget nyújt a hálózati infrastruktúrában különböző pont közötti hálózati teljesítmény figyelése. Emellett segítséget nyújt a hálózati kapcsolat figyeléséhez [szolgáltatások és alkalmazások végpontok](../../azure-monitor/insights/network-performance-monitor-service-connectivity.md) és [Azure ExpressRoute teljesítményének megfigyelése](../../azure-monitor/insights/network-performance-monitor-expressroute.md). 

A Network Performance Monitor észleli a hálózati problémák, például az adatforgalom blackholing, útválasztási hibák és problémák, amelyek a hagyományos hálózati figyelési módszerek nem képes észlelni. A megoldás riasztásokat készít, és értesíti, amikor egy hálózati kapcsolat meghaladja a küszöbértéket. Emellett biztosítja a hálózat teljesítményével kapcsolatos problémák időbeni észlelését és leszűkíti a hiba forrásának helyszínét egy hálózati szegmensre vagy eszközre. 

További információ a különböző funkciók által támogatott [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) online is elérhető.

## <a name="set-up-and-configure-agents"></a>Állítsa be és az ügynökök konfigurálása

### <a name="what-are-the-platform-requirements-for-the-nodes-to-be-used-for-monitoring-by-npm"></a>Mik azok az adott platformok követelményeit, a csomópontok a figyelés npm-et használni?
Az alábbiakban az adott platformok követelményeit, az NPM képességek:

- NPM Teljesítményfigyelő és szolgáltatás Kapcsolatfigyelő képességeket támogatja mind a Windows server (2008 SP1 vagy újabb) és a Windows asztali számítógépek vagy Windows-ügyfél operációs rendszerek (a Windows 10, Windows 8.1, Windows 8 és Windows 7). 
- NPM ExpressRoute-figyelő funkció támogatja a csak a Windows server (2008 SP1 vagy újabb) operációs rendszert.

### <a name="can-i-use-linux-machines-as-monitoring-nodes-in-npm"></a>Linux rendszerű gépek használják az NPM csomópontok figyelése?
Képes figyelni a Linux-alapú csomópontokat használó hálózatok jelenleg privát előzetes verzióban érhető el. Keresse fel a Ügyfélmenedzserével többet. Miután megadta a munkaterület Azonosítóját, hogy lesz lépjen tovább, és a funkció engedélyezéséhez. Linux-ügynökök adja meg a figyelési funkció csak az NPM Teljesítményfigyelő képesség, és nem érhetők el a szolgáltatás Kapcsolatfigyelő és ExpressRoute-figyelő képességei

### <a name="what-are-the-size-requirements-of-the-nodes-to-be-used-for-monitoring-by-npm"></a>Mik a méretét a csomópontok figyeléshez az NPM által használandó?
A csomópont virtuális gépek, hálózatok figyelése az NPM-megoldást futtatja, a csomópontok legalább 500 MB memória és a egy magot kell rendelkeznie. Nem kell különálló csomópontok használata futtatásához npm-et. A megoldás, amely rendelkezik a rajta futó számítási feladatoktól csomópontján futtathatja. A megoldás a teszi, hogy a monitorozási folyamat leállítása, abban az esetben azt használja, amely több, mint 5 %-ot tartalmaz.

### <a name="to-use-npm-should-i-connect-my-nodes-as-direct-agent-or-through-system-center-operations-manager"></a>Npm-et használja, hogy csatlakozhatok saját csomópontok közvetlen ügynök vagy a System Center Operations Manager használatával?
A Teljesítményfigyelő és a szolgáltatás Kapcsolatfigyelő képességeket is támogatja a csomópontok [közvetlen ügynökök csatlakoztatott](../../azure-monitor/platform/agent-windows.md) , valamint [szolgáltatást az Operations Manageren keresztül csatlakozó](../../azure-monitor/platform/om-agents.md).

Az ExpressRoute-figyelő szolgáltatás az Azure-csomópontok csatlakoznia kell a közvetlen ügynökök, csak. Az Operations Manager keresztül vannak csatlakoztatva az Azure csomópontok nem támogatottak. Helyszíni csomópontokat a közvetlen ügynökök, valamint az Operations Manager csatlakoztatott csomópontok ExpressRoute-kapcsolatcsoport figyelésére használhatók.

### <a name="which-protocol-among-tcp-and-icmp-should-be-chosen-for-monitoring"></a>Többek között a TCP és az ICMP protokoll figyelésre kell választani?
Ha a hálózat, Windows server-alapú csomópontokat használ, javasoljuk, TCP figyelési protokollként használhatja, mivel ez nagyobb pontosságot biztosít. 

ICMP használata ajánlott Windows asztali számítógépek vagy Windows-ügyfél operációs rendszer-alapú csomópontokat. Ezen a platformon nem engedélyezi a TCP-adatok a nyers sockets, az NPM segítségével hálózati topológia felderítése lesz elküldve.

További részleteket az egyes protokoll relatív előnyeit is megjeleníthet [Itt](../../azure-monitor/insights/network-performance-monitor-performance-monitor.md#choose-the-protocol).

### <a name="how-can-i-configure-a-node-to-support-monitoring-using-tcp-protocol"></a>Hogyan konfigurálhatok egy csomópont támogatja a figyelésre TCP protokoll használatával?
A csomópont figyelésre TCP protokoll használatával támogatja: 
* Győződjön meg arról, hogy a csomópont-platform a Windows Server (2008 SP1 vagy újabb).
* Futtatás [EnableRules.ps1](https://aka.ms/npmpowershellscript) Powershell-parancsfájl a csomóponton. Lásd: [utasításokat](../../azure-monitor/insights/network-performance-monitor.md#configure-log-analytics-agents-for-monitoring) további részletekért.


### <a name="how-can-i-change-the-tcp-port-being-used-by-npm-for-monitoring"></a>Hogyan módosíthatom a figyeléshez az NPM által használt TCP-portot?
Módosíthatja a figyeléshez, futtassa az NPM által használt TCP-port a [EnableRules.ps1](https://aka.ms/npmpowershellscript) parancsfájlt. A paramétert használni kívánt portszámot kell adnia. Például ahhoz, hogy a TCP-port 8060, futtassa `EnableRules.ps1 8060`. Győződjön meg arról, hogy minden monitorozásra szolgáló csomóponton ugyanazt a TCP-portot használja.

A szkript kizárólag Windows tűzfal helyben konfigurálja. Ha tűzfal vagy a hálózati biztonsági csoport (NSG) szabályai, győződjön meg arról, hogy azok engedélyezik-e az NPM által használt TCP-port felé tartó forgalmat.

### <a name="how-many-agents-should-i-use"></a>Hány ügynök használjam?
Legalább egy ügynököt, amely a figyelni kívánt minden egyes alhálózathoz használjon.

### <a name="what-is-the-maximum-number-of-agents-i-can-use-or-i-see-error--you-have-reached-your-configuration-limit"></a>Mi az ügynökök is használhatok maximális számát, vagy hibát "… elérte a konfigurációs korlátot"?
Az NPM Munkaállomásonként 5000-es IP-címek IP-címek számát korlátozza. Ha egy csomópont IPv4 és IPv6-címeket is rendelkezik, ez számítanak 2 IP-címek a csomópontra vonatkozóan. Ezért ezt a korlátot, az 5000-es IP-címek akkor döntse el, az ügynökök számának felső korlátja. NPM csomópontok lapján törölheti az inaktív ügynököt >> konfigurálása. Npm-et is fenntartják, minden IP-címekről felhasználónevéhez rendelt minden eddiginél a virtuális Gépet üzemeltető az ügynököt, és ezek szintén számít, hogy felső korlátja 5000-es IP-címek külön IP-címek hozzájáruló előzményeit. Szabadítson fel IP-címek a munkaterülethez, használhatja a csomópontok lap törli az IP-címek, amelyek nem használja.

## <a name="monitoring"></a>Figyelés

### <a name="how-are-loss-and-latency-calculated"></a>Kimaradások és késések kiszámítási módját
Forrás ügynökök vagy TCP SZIN kérelmek (Ha a TCP protokoll figyeléséhez van kiválasztva) és küldhet az ICMP ECHO kéréseket (ha az ICMP protokoll figyeléséhez van kiválasztva) cél IP-cím annak érdekében, hogy a forrás-cél IP-cím közötti összes elérési utat rendszeres időközönként Kombinált terjed ki. A fogadott csomagok és a csomag üzenetváltási idő aránya alapján számítja ki a veszteség és mindegyik elérési út késését mérjük. Ezeket az adatokat összesített értéket jelenít meg a lekérdezési időköz és az összesített értékekre, veszteségének és késleltetésének beolvasni az IP-kombinációt, az adott lekérdezési időközének összes útvonal.

### <a name="with-what-frequency-does-the-source-agent-send-packets-to-the-destination-for-monitoring"></a>Milyen gyakorisággal nem a forrásügynök csomagok küldése a célhelyre figyelés?
A Teljesítményfigyelő és ExpressRoute-figyelő képességei a forrás 5 másodpercentként csomagokat küld, és rögzíti a hálózati mérések. Ezeket az adatokat összesített értéket jelenít meg a 3 perces lekérdezési időköz kiszámításához a veszteséget és késéseket átlagos és kiugró kihasználtsággal értékeit. A szolgáltatás Kapcsolatfigyelő képesség a gyakoriságát, a hálózati mérést a csomagok küldését a gyakoriság az adott teszt konfigurálása a vizsgálat során a felhasználó által megadott határozza meg.

### <a name="how-many-packets-are-sent-for-monitoring"></a>Hány csomagok küldése a figyelést?
A lekérdezés a cél a forrás-ügynök által küldött csomagok száma adaptív és, amelyekről a szellemi tulajdont képező algoritmus, amely különböző hálózati topológiák esetén eltérők lehetnek. Hálózati elérési utak között a forrás-cél IP-kombinációt, több további száma küldött csomagok száma. A rendszer gondoskodik arról, hogy terjed ki, hogy a forrás-cél IP-kombinációt közötti összes elérési utat.

### <a name="how-does-npm-discover-network-topology-between-source-and-destination"></a>Hogyan npm-et forrás és cél közötti hálózati topológia felderítése?
NPM Traceroute alapján szellemi tulajdont képező algoritmust használ, az elérési utak és útválasztók ugrásainak forrás és cél között.

### <a name="does-npm-provide-routing-and-switching-level-info"></a>Adja meg az Útválasztás és váltása szint info NPM 
Npm-et a forrás-ügynök és a cél közötti összes lehetséges útvonal képes észlelni, bár nem biztosít, amelybe a meghatározott számítási feladatok által küldött csomagokat útvonal hibaállapota láthatóságát. A megoldás segítségével azonosíthatja a útvonalak és az alapul szolgáló hálózati ugrásra, amely ad hozzá, a vártnál több késés.

### <a name="why-are-some-of-the-paths-unhealthy"></a>Miért vannak a elérési utak közül néhány nem megfelelő állapotú?
Különböző hálózati elérési út a forrás és cél IP-címek között is létezik, és mindegyik elérési út egy másik értéket a veszteséget és késéseket is rendelkezhet. Az NPM jelöli meg ezen elérési utakat nem megfelelő (piros színt hozzáfűzni) számára, amely az értékeket, adatveszteség és/vagy a késés nagyobb, mint a megfelelő küszöbértéket állíthat be a felügyeleti konfigurációs.

### <a name="what-does-a-hop-in-red-color-signify-in-the-network-topology-map"></a>Mi nem egy piros színnel Ugrás jelölésére a hálózati topológia térképen?
Ha egy Ugrás a piros, érték azt jelenti, hogy-e legalább egy nem jó elérési út része. Az NPM csak az elérési utak állapota nem megfelelőként jelöli meg, az egyes elérési út állapotát nem őket. A feladatátvételnek ugrások azonosítására, az Ugrás ugrásra felépülő késés tekintheti meg és elkülönítheti a kapcsolatok hozzáadása több, mint a várható késés.

### <a name="how-does-fault-localization-in-performance-monitor-work"></a>Hogyan működik a hibák megkeresése a Teljesítményfigyelőben?
NPM valószínűségi mechanizmust alkalmaz a tartalék-valószínűségek hozzárendelése minden egyes hálózati elérési utat, a hálózati szegmenst, és a egy részét képezik a különböző hálózati útválasztók ugrásainak nem jó elérési utak száma alapján. A hálózati szegmensek és útválasztók ugrásainak nem jó elérési utak száma további részét képezik, a hozzájuk társított tartalék-valószínűségi növekszik. Ez az algoritmus akkor működik a legjobban, ha sok csomóponttal rendelkezik, ez növeli a tartalék valószínűségek kiszámításához az adatpontok egymáshoz kapcsolódó NPM-ügynökkel.

### <a name="how-can-i-create-alerts-in-npm"></a>Hogyan hozhatok létre riasztásokat az npm-et?
Tekintse meg [riasztások szakaszban a dokumentáció](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor#alerts) részletes útmutatásait.

### <a name="can-npm-monitor-routers-and-servers-as-individual-devices"></a>Az NPM figyelheti az útválasztók és a kiszolgálók az egyes eszközök?
NPM csak azonosítja az alapul szolgáló közötti hálózati ugrásokat (kapcsolók, útválasztók, kiszolgálók, stb.) a forrás és cél IP-címek IP-cím és a gazdagép nevét. Az alábbi azonosított útválasztók ugrásainak a késés is azonosítja. Ezek az alapul szolgáló ugrások külön-külön nem figyeli.

### <a name="can-npm-be-used-to-monitor-network-connectivity-between-azure-and-aws"></a>Az NPM segítségével figyelheti az Azure és AWS közötti hálózati kapcsolat?
Igen. A cikkben [Monitor Azure, az AWS és az NPM segítségével a helyszíni hálózatok](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/) részleteiről.

### <a name="is-the-expressroute-bandwidth-usage-incoming-or-outgoing"></a>Az ExpressRoute sávszélesség-használat, a bejövő vagy kimenő?
A sávszélesség-használat a teljes bejövő és kimenő sávszélesség. Bit/mp mértékegysége.

### <a name="can-we-get-incoming-and-outgoing-bandwidth-information-for-the-expressroute"></a>A Microsoft információkat szerezhet a bejövő és kimenő sávszélesség az expressroute-hoz?
A bejövő és kimenő értékeit az elsődleges és a másodlagos sávszélesség rögzíthetők.

Társviszony-létesítési szintű információkért kövesse az alábbiakban említett lekérdezés a Naplókeresésben

    NetworkMonitoring 
    | where SubType == "ExpressRoutePeeringUtilization"
    | project CircuitName,PeeringName,PrimaryBytesInPerSecond,PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond
  
Kapcsolatcsoport szintű információkért kövesse az alábbiakban említett lekérdezés 

    NetworkMonitoring 
    | where SubType == "ExpressRouteCircuitUtilization"
    | project CircuitName,PrimaryBytesInPerSecond, PrimaryBytesOutPerSecond,SecondaryBytesInPerSecond,SecondaryBytesOutPerSecond

### <a name="which-regions-are-supported-for-npms-performance-monitor"></a>Mely régiók támogatottak az NPM Teljesítményfigyelő?
Az NPM figyelheti egy munkaterületről egy üzemeltetett, a világ bármely részén hálózatok közötti kapcsolat a [támogatott régiók](../../azure-monitor/insights/network-performance-monitor.md#supported-regions)

### <a name="which-regions-are-supported-for-npms-service-connectivity-monitor"></a>Mely régiók támogatottak az NPM szolgáltatás Kapcsolatfigyelő?
Az NPM figyelheti csatlakozási szolgáltatások a világ bármely részén egy munkaterületről egy üzemeltetett a [támogatott régiók](../../azure-monitor/insights/network-performance-monitor.md#supported-regions)

### <a name="which-regions-are-supported-for-npms-expressroute-monitor"></a>Mely régiók támogatottak az NPM ExpressRoute-figyelő?
Az NPM figyelheti az ExpressRoute-Kapcsolatcsoportok bármely Azure-régióban található. Való előkészítésre NPM, szüksége lesz a Log Analytics-munkaterület, amely az egyik kell elhelyezni a [támogatott régiók](/azure/expressroute/how-to-npm)

## <a name="troubleshoot"></a>Hibaelhárítás

### <a name="why-are-some-of-the-hops-marked-as-unidentified-in-the-network-topology-view"></a>Miért vannak egyes az útválasztók ugrásainak a hálózati topológia e nézetében az azonosítatlan megjelölve?
Npm-et használ a traceroute módosított változatát feltérképezi a forrás-ügynöktől a célhelyre. Az azonosítatlan Ugrás jelöli, hogy a hálózati Ugrás a forrásügynök traceroute kérelem nem válaszolt. Ha 3 egymást követő hálózati útválasztók ugrásainak traceroute az ügynök nem válaszol, a megoldás jelöli meg a nem válaszoló ugrások azonosítatlan, és nem próbál további ugrásai felderítéséhez.

Hop előfordulhat, hogy egy vagy több, a traceroute válaszol az alábbi forgatókönyvek:

* Az útválasztók beállított ne fedjenek-azonosítót fog kérni.
* A hálózati eszközök nem ICMP_TTL_EXCEEDED forgalmat engedélyezi.
* Egy tűzfal blokkolja a hálózati eszköz ICMP_TTL_EXCEEDED válaszát.

### <a name="the-solution-shows-100-loss-but-there-is-connectivity-between-the-source-and-destination"></a>A megoldás bemutatja, 100 %-os adatvesztés, de a forrás és cél közötti kapcsolat
Ez akkor fordulhat elő, ha a gazdagép tűzfal vagy a (hálózati tűzfal vagy Azure NSG-t) a köztes tűzfal blokkolja a forrásügynök és a cél közötti kommunikáció a figyeléshez az NPM által használt porton keresztül (alapértelmezés szerint a port meg 8084, kivéve, ha a ügyfél változott ez).

* Győződjön meg arról, hogy a gazdagép tűzfal nem blokkolja-e a szükséges porton a kommunikációt, tekintse meg a következő nézetet a forrás és cél csomópontok állapotát: A Network Performance Monitor -> Konfiguráció -> a csomópontokon. 
  Ha nem megfelelő állapotú, a vonatkozó utasítások megtekintése és korrekciós műveletek. Ha a csomópontok kifogástalan, helyezze át a b lépésben. az alábbiakban.
* Győződjön meg arról, hogy egy köztes tűzfal- vagy Azure NSG-t nem blokkolja-e a szükséges porton a kommunikációt, használja a külső PsPing segédprogram használatával az alábbi utasítások végrehajtásával:
  * a psping eszköz érhető el letöltésre [Itt](https://technet.microsoft.com/sysinternals/psping.aspx) 
  * Futtassa a következő parancsot a forrás-csomópontból.
    * a psping -n 15 \<célcsomópont IP-cím\>: portszám NPM alapértelmezés szerint 8084 portot használja. Az esetben, explicit módon megváltozott ennek az EnableRules.ps1 parancsfájl használatával, adja meg az egyéni portszám használata). Ez az a helyszínen az Azure-beli gép a pingelés
* Ellenőrizze, hogy sikeres-e a pingelésre. Ha nem, akkor azt jelzi, hogy egy köztes tűzfal- vagy Azure NSG blokkolja a forgalmat ezen a porton.
* Most futtassa a parancsot a célcsomóponton, és a forrás IP-Címét.


### <a name="there-is-loss-from-node-a-to-b-but-not-from-node-b-to-a-why"></a>Nincs adatvesztés csomópont A, b, azonban nem. a B csomópont miért?
Mivel a hálózati elérési utak között, A b eltérhetnek a hálózati elérési utak között A, B, veszteségének és késleltetésének eltérő értékeket figyelhető.

### <a name="why-are-all-my-expressroute-circuits-and-peering-connections-not-being-discovered"></a>Miért vannak saját ExpressRoute-Kapcsolatcsoportok és a társviszony-kapcsolatok nem észlelhető?
Az NPM mostantól felderíti az ExpressRoute-Kapcsolatcsoportok és társviszony-kapcsolatok az összes előfizetést, amelyhez a felhasználónak hozzáférése van. Válassza ki az összes olyan előfizetést, az Express Route-erőforrások kapcsolódnak, és engedélyezze a monitorozást az összes felderített erőforrást. NPM keresi a kapcsolat objektumokat, ha a felderítése, a privát társviszony-létesítés, ezért ellenőrizze, hogy egy virtuális hálózathoz társítva a társviszony-létesítés.

### <a name="the-er-monitor-capability-has-a-diagnostic-message-traffic-is-not-passing-through-any-circuit-what-does-that-mean"></a>A figyelő ER funkcióra diagnosztikai üzenet "Nem továbbítja forgalmat bármilyen kapcsolatcsoport". What does that mean? (Megfelel-e a Microsoft Azure a jogszabályi előírásoknak? Mit kell érteni ez alatt?)

Egy forgatókönyv, ahol van-e megfelelő kapcsolat a helyszíni között és az Azure-csomópontok, de a forgalom nem fogja az NPM által figyelendő konfigurálása ExpressRoute-kapcsolatcsoport keresztül lehet. 

Ez akkor fordulhat elő, ha:

* Az ER-kapcsolatcsoport nem működik.
* Az útvonalszűrők oly módon, hogy a többi útvonal (például egy VPN-kapcsolat vagy egy másik ExpressRoute-kapcsolatcsoport) biztosítanak prioritású vannak konfigurálva, a megfelelő ExpressRoute-kapcsolatcsoport keresztül. 
* A helyszíni és az Azure-figyelés az ExpressRoute-kapcsolatcsoport a figyelési konfigurációval, a kiválasztott csomópontok nem rendelkezik a megfelelő ExpressRoute-kapcsolatcsoport keresztül egymással való kapcsolatot. Győződjön meg arról, hogy helyes-e csomópontokat, amelyeken keresztül az ExpressRoute-kapcsolatcsoport figyelni szeretne egymással való kapcsolatot választotta.

### <a name="while-configuring-monitoring-of-my-expressroute-circuit-the-azure-nodes-are-not-being-detected"></a>A figyelést, saját ExpressRoute-kapcsolatcsoportot, miközben az Azure-csomópontok nem folyamatban észlelt.
Ez akkor fordulhat elő, ha az Azure-csomópontok csatlakoznak a szolgáltatást az Operations Manageren keresztül. Az ExpressRoute-figyelő funkció csak azokat az Azure csatlakoztatott csomópontokat, a közvetlen ügynökök támogatja.

### <a name="i-cannot-discover-by-expressroute-circuits-in-the-oms-portal"></a>Az OMS-portálon az ExpressRoute-Kapcsolatcsoportok nem tudok nem képesek felderíteni
Az NPM használhatók, mind az Azure Portalon, valamint az OMS-portálon, azonban az ExpressRoute-figyelő képesség a kapcsolatcsoport felderítése működik, csak az Azure Portalon keresztül. A Kapcsolatcsoportok az Azure Portalon a felderítésüket követően akár a két különböző portál érhető majd használhatja a funkció. 

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-network-loss-as-well-as-latency-are-shown-as-na"></a>A szolgáltatás Kapcsolatfigyelő funkció a a szolgáltatás válaszideje, hálózati adatveszteség, valamint késéssel jelennek meg NA
Ez akkor fordulhat elő, ha egy vagy több igaz:

* A szolgáltatás leállt.
* A hálózati kapcsolat és a szolgáltatás ellenőrzésére használt csomópont nem működik.
* A cél a vizsgálati konfigurációjában megadott helytelen.
* A csomópont nem rendelkezik hálózati kapcsolattal.

### <a name="in-the-service-connectivity-monitor-capability-a-valid-service-response-time-is-shown-but-network-loss-as-well-as-latency-are-shown-as-na"></a>A szolgáltatás Kapcsolatfigyelő funkció a jelenik meg egy érvényes szolgáltatás válaszideje, de a hálózati veszteséget, valamint a késés NA jelennek meg
 Ez akkor fordulhat elő, ha egy vagy több igaz:

* Ha a hálózati kapcsolat és a szolgáltatás ellenőrzésére használt csomópontot egy Windows-ügyfélgép, vagy a célszolgáltatás blokkolja a kérelmeket az ICMP, vagy a hálózati tűzfal blokkolja a csomópont az ICMP-kérelmekkel.
* Hálózati mérések végrehajtása jelölőnégyzetét, akkor üres a vizsgálati konfigurációjában.

### <a name="in-the-service-connectivity-monitor-capability-the-service-response-time-is-na-but-network-loss-as-well-as-latency-are-valid"></a>A szolgáltatás Kapcsolatfigyelő képesség a szolgáltatás válaszideje NA de hálózati adatveszteség, valamint a késés érvényesek
Ez akkor fordulhat elő, ha a célként megadott szolgáltatás nem egy webalkalmazást, de a teszt van konfigurálva, egy webes tesztet. A test-konfiguráció szerkesztése, és válassza ki a tesztelési hálózati webes helyett.

## <a name="miscellaneous"></a>Egyéb rendelkezések

### <a name="is-there-a-performance-impact-on-the-node-being-used-for-monitoring"></a>Van a teljesítményre gyakorolt hatás monitorozásra szolgáló csomóponton?
Az NPM-folyamat leáll, ha azt használja a gazdagép CPU-erőforrások több mint 5 %-át van konfigurálva. Ez azért szükséges, hogy továbbra is használhatja a csomópontok szokásos számítási feladatai anélkül, hogy befolyásolná a teljesítményt.

### <a name="does-npm-edit-firewall-rules-for-monitoring"></a>Nem NPM ellenőrzésére vonatkozó tűzfalszabályok szerkeszteni?
Npm-et a csomóponton, amelyen az EnableRules.ps1 Powershell-szkriptet, hogy az ügynökök TCP-kapcsolatok létrehozása egymással a megadott porton fut csak egy helyi Windows tűzfal szabályt hoz létre. A megoldás minden olyan tűzfal vagy a hálózati biztonsági csoport (NSG) szabályai nem módosítja.

### <a name="how-can-i-check-the-health-of-the-nodes-being-used-for-monitoring"></a>Hogyan tudom ellenőrizni a figyeléshez használt csomópontok állapotát?
A következő nézetet a figyeléshez használt csomópontok állapotát tekintheti meg: A Network Performance Monitor -> Konfiguráció -> a csomópontokon. Ha egy csomópont állapota nem megfelelő, a hiba részleteinek megtekintéséhez, és hajtsa végre a javasolt műveletet.

### <a name="can-npm-report-latency-numbers-in-microseconds"></a>Jelenthetik NPM késésű mikroszekundumban?
Npm-et a felhasználói felületen és ezredmásodperc késés számok lefelé kerekít. Ugyanazokat az adatokat, egy nagyobb részletességgel (néha legfeljebb négy tizedesjegyek) tárolja.

## <a name="next-steps"></a>További lépések

- További információ a Network Performance Monitor történő hivatkozással [Network Performance Monitor megoldás az Azure-ban](../../azure-monitor/insights/network-performance-monitor.md).
