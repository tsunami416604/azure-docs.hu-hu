---
title: "Hálózati Teljesítményfigyelő megoldás az Azure-ban |} Microsoft Docs"
description: "Az Azure-ban Teljesítményfigyelő hálózati segítséget nyújt a hálózatok a közel valós idő a teljesítményének figyeléséhez észleli, és keresse meg a hálózati szűk keresztmetszetek."
services: log-analytics
documentationcenter: 
author: abshamsft
manager: carmonm
editor: 
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: abshamsft
ms.openlocfilehash: 399fe552d5c7d9a96cdabc2a1dfafe99635d4a61
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2018
---
# <a name="network-performance-monitor-solution-in-azure"></a>Hálózati Teljesítményfigyelő megoldás az Azure-ban

![Hálózati Teljesítményfigyelő szimbólum](./media/log-analytics-network-performance-monitor/npm-symbol.png)


Hálózati teljesítmény figyelése (NPM) egy hibrid felhő alapú hálózatfigyelési megoldás, amely segítséget nyújt a hálózati infrastruktúra, a figyelő hálózati kapcsolattal szolgáltatás vagy alkalmazás végpontok különböző pontok közötti hálózati teljesítmény figyeléséhez és az Azure ExpressRoute teljesítményének figyeléséhez.  

NPM észleli a hálózati problémák, például a forgalom blackholing útválasztási hibák és problémák hagyományos hálózati figyelési módszerek nem képes azonosítani. A megoldás riasztásokat állít elő, értesíti, amikor a küszöbérték megsérül, a hálózati kapcsolat, időben történő észlelése érdekében a hálózati teljesítményproblémák biztosítja, és egy adott hálózati szegmens vagy az eszköz a probléma forrása localizes. 

NPM három általános képességeket biztosít: 

[Teljesítményfigyelő](log-analytics-network-performance-monitor-performance-monitor.md): felhőalapú telepítések és a helyszíni helyek, a több különböző adatközponthoz és a fiókirodák közötti hálózati kapcsolat figyelése, akkreditált képviseletének kritikus Többrétegű alkalmazások és micro-szolgáltatások. A Teljesítményfigyelő képes észlelni a hálózati problémák, mielőtt a felhasználók panaszkodnak mert.  

[Végpont-figyelő szolgáltatás](log-analytics-network-performance-monitor-service-endpoint.md): figyelheti a kapcsolatot a a felhasználók és a szolgáltatások fontos adatokhoz, annak megállapítása, hogy milyen infrastruktúra az elérési út és a hálózati szűk keresztmetszeteket is megjelenhetnek. Üzemszünet korlátozza a felhasználók előtt tudni, és tekintse meg a hibákat a hálózati elérési út mentén pontos helyét. 

Ez a funkció alapján végezheti el a http, HTTPS, az TCP és az ICMP-alapú tesztek a közel valós idejű vagy hagyományosan a rendelkezésre állási és figyelése a szolgáltatás válaszidő és a csomag veszteséget és késéseket hálózati hozzájárulása. A hálózati topológia térkép különítheti el a hálózati lassulást probléma tesztüzeméhez elvégzett mentén a hálózati elérési út a csomópont a szolgáltatást, a késési adatokat minden egyes ugrásnál azonosításával. Beépített tesztekkel figyelheti a hálózati kapcsolat Office365 és a Dynamics CRM előtti konfigurálása nélkül. Ezzel a lehetőséggel figyelheti meg a hálózati kapcsolat bármely TCP képes végpontra, például webhelyekhez, SaaS, a PaaS alkalmazások, SQL-adatbázisok, stb.  

[Az ExpressRoute-figyelő](log-analytics-network-performance-monitor-expressroute.md): végpontok közötti kapcsolat és a fiókirodák és az Azure-ban közötti teljesítményének figyelése Azure ExpressRoute keresztül.  
 

## <a name="set-up-and-configure"></a>Beállítása és konfigurálása

### <a name="install-and-configure-agents"></a>Telepíteni és konfigurálni az ügynököket 

A legfontosabb folyamatok segítségével telepíthet ügynököket a [Log Analyticshez való csatlakozás Windows számítógépek](log-analytics-windows-agents.md) és [csatlakozás az Operations Manager szolgáltatáshoz](log-analytics-om-agents.md).

### <a name="where-to-install-the-agents"></a>Az ügynökök telepítésének helyéről 

**Teljesítményfigyelő:** telepítéséhez legalább az egyik csomópont OMS-ügynököt minden alhálózat, amelyből a más alhálózatokra való hálózati kapcsolat figyelni kívánt csatlakozik.  

A hálózati kapcsolaton figyeléséhez telepítenie kell az ügynökök mindkét végponton, hogy a hivatkozás.  Ha biztos a hálózat topológiáját, telepítse az ügynököt a kritikus fontosságú munkaterhelésekhez, amelyek között a hálózati teljesítmény figyelni kívánt kiszolgálókon. Például a webkiszolgáló és egy futó SQL server közötti hálózati kapcsolat figyelni kívánt, ha egy ügynök telepítése mindkét kiszolgálón. Ügynökök figyelik a hálózati kapcsolat (hivatkozások) állomás nem magukat a gazdagépek között. 

**Végpont Szolgáltatásfigyelő:** telepítése minden egyes csomóponton, amelyből el kívánja a hálózati kapcsolatot a szolgáltatásvégpontot figyelése OMS-ügynököt. Például ha azt tervezi, figyelheti a hálózati kapcsolat az Office365 az office helyről O1, O2 és O3, majd telepítse az OMS-ügynököt legalább egy csomópont minden O1, O2 és O3. 

**Az ExpressRoute-figyelő:** az Azure virtuális hálózat legalább egy OMS-ügynököt, és legalább egy ügynök telepítése a helyi alhálózat, amely csatlakozik a saját ExpressRoute-Társviszony.  

### <a name="configure-oms-agents-for-monitoring"></a>OMS-ügynököt figyelés konfigurálása  

NPM szintetikus tranzakciók használja a forrás és cél ügynökök között a hálózati teljesítmény figyeléséhez. A megoldás TCP ExpressRoute figyelő szolgálja, míg kínál Teljesítményfigyelő és végpont-figyelő szolgáltatás képességeit, a figyelés protokollként TCP és az ICMP közötti választás. Győződjön meg arról, hogy a tűzfal engedélyezi-e az OMS-ügynökök a figyelés kiválasztott protokollal figyeléshez használt közötti kommunikációt.  

**A TCP protokoll:** Ha beállította a TCP protokollt a figyeléshez, nyissa meg az ügynökökhöz Teljesítményfigyelő és ExpressRoute figyelő képességeinek, győződjön meg arról, hogy az ügynökök is összekapcsolására használt tűzfalport. Ehhez az szükséges, akkor paraméterek nélkül EnableRules.ps1 PowerShell-parancsprogram futtatásához rendszergazdai jogosultságokkal rendelkező power shell ablakban.  

A parancsfájl-megoldás által igényelt beállításkulcsokat hoz létre, és létrehozza a Windows tűzfal szabályokat az ügynökök számára a TCP-kapcsolatok létrehozása egymás mellett. A beállításkulcsok hozta létre a parancsfájlt is megadhatja, hogy a naplófájl a hibakeresési naplók és a naplók fájl elérési útját. Az ügynök-kommunikációhoz használt TCP-portot is meghatározza. Ezek a kulcsok értékeit automatikusan beállítja a parancsfájl által, így nem kell manuálisan módosítani ezeket a kulcsokat. A port, megnyílik az 8084. A paraméter portszám a parancsfájl megadásával egyéni portot is használhat. Azonban ugyanazt a portot kell használni az összes számítógépen ahol a parancsfájl futtatása. 

>[!NOTE]
> A parancsfájl a csak a windows tűzfal helyben konfigurálja. Ha egy hálózati tűzfalat, meg kell győződnie arról, hogy azt átengedi a forgalmat a TCP-portot használják NPM szánt 

>[!NOTE]
> Nem kell a EnableRules.ps1 PowerShell parancsfájl futtatásához a szolgáltatási végpont-figyelő 

 

**Az ICMP protokoll** – Ha ICMP választotta, a figyelés protokoll ICMP megbízhatóan használatával a következő tűzfalszabályok engedélyezése: 

 
```
netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
```
 

### <a name="configure-the-solution"></a>A megoldás konfigurálása 

1. A hálózati Teljesítményfigyelő megoldás hozzáadása a munkaterület [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview) vagy ismertetett folyamatot követve [hozzáadni a Naplóelemzési megoldások a megoldások gyűjteményből](log-analytics-add-solutions.md). 
2. Nyissa meg a Naplóelemzési munkaterületet, majd kattintson a a **áttekintése** csempére.  
3. Kattintson a csempe jelenik meg a **hálózati Teljesítményfigyelő** üzenettel *megoldás további konfigurálást igényel*.

    ![NPM csempe](media/log-analytics-network-performance-monitor/npm-config.png)

3. Az a **telepítő** lapon látható arra, hogy telepíti a OMS-ügynököt, és a figyelő az ügynökök konfigurálása a **általános beállítások** nézet. Ahogy fent, ha korábban már telepített és konfigurált OMS-ügynököt, majd kattintson a a **telepítő** nézet a funkció érdekli használatával konfigurálásához.  

    **A figyelő a teljesítmény nézet** -válassza ki, milyen protokollt kell használni az alapértelmezett szintetikus tranzakciók teljesítményének figyelése szabály és kattintson a Mentés, és továbbra is. A protokoll kiválasztása csak tárolja a rendszer által létrehozott alapértelmezett szabály, és meg kell adnia a protokoll minden alkalommal, amikor olyan szabályt hoz létre a Teljesítményfigyelő explicit módon. Mindig az alapértelmezett szabály beállításokat (Ez akkor jelenik meg, a nap-0 konfiguráció befejezése után) a Teljesítményfigyelő lapján áthelyezése, és később módosítható a protokoll. Abban az esetben, ha nem a rPerfomance figyelő képesség érdekli, letilthatja az alapértelmezett szabály az a Teljesítményfigyelő lapon alapértelmezett szabály beállításai közül. 

    ![NPM konfiguráció](media/log-analytics-network-performance-monitor/npm-synthetic-transactions.png)
    
    **Szolgáltatás végpont-figyelő nézet** -a képességet biztosít a beépített előre konfigurált vizsgálatok az ügynököket Office365 és Dynamcis365 hálózati kapcsolatok figyelésére. Válassza ki, hogy érdekli őket melletti jelölőnégyzet bejelölésével figyelési Office365 és Dynamcis365 szolgáltatásokat. Válassza ki az ügynökök, amelyből el kívánja figyeléséhez, az ügynökök hozzáadása gombra kattintva. Ha nem szeretné használni ezt a lehetőséget, vagy később állíthat be, ha szeretné, hagyja ki ezt, majd közvetlenül a **mentése** és **Folytatás** nem választott semmit.  

    ![NPM konfiguráció](media/log-analytics-network-performance-monitor/npm-service-endpoint-monitor.png)

    **Az ExpressRoute-figyelő nézet** -kattintson a a **felderítése most** gomb az Azure-előfizetés a Vnetek csatlakozó személyes esetében a Naplóelemzési munkaterület kapcsolódó összes ExpressRoute felderítéséhez.  


    >[!NOTE] 
    > A megoldás jelenleg csak az ExpressRoute magánhálózati társviszony deríti fel. 

    >[!NOTE] 
    > Csak azokat magánhálózati társviszony felderített a Naplóelemzési munkaterület kapcsolódik az előfizetéshez tartozó Vnetek csatlakozik. Ha a ExpressRoute kívül az előfizetés, a munkaterület kapcsolódó Vnetek csatlakozik, szeretné előfizetésekben Naplóelemzési munkaterület létrehozása és az NPM segítségével figyelheti ezek esetében. 

    ![NPM konfiguráció](media/log-analytics-network-performance-monitor/npm-express-route.png)

    Ha a felderítés befejeződött, a felderített magánhálózati társviszony egy táblázatban láthatók.  

    ![NPM konfiguráció](media/log-analytics-network-performance-monitor/npm-private-peerings.png)
    
    A figyelési ezek esetében az vannak kezdetben letiltott állapotú. Kattintson az egyes társviszony megváltozása figyelését, és azok esetében a jobb oldalon (jobb oldal) részletes nézete a figyelés konfigurálása.  Kattintson a Mentés gombra kattintva mentse a konfigurációt. Lásd: [konfigurálása ExpressRoute figyelési]() további.  

    A telepítés befejezése után, az adatok feltöltése egy órát vesz igénybe 30 perc. A megoldás az adatok összesítésének a hálózatról, amíg megjelenik *megoldás további konfigurálást igényel* a NPM – Áttekintés lapon. Miután az adatok gyűjtése és indexelt, a áttekintése csempe módosításokat, és a figyelmeztet, a hálózat állapotának összegzését. Ezután igény a csomópontokat, amelyeken OMS-ügynök telepítve van a, valamint az alhálózatok felderítése a környezetében a figyelési szerkesztése 

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>Alhálózatok és a csomópontok figyelési beállításainak módosítása 

Legalább egy ügynök van telepítve az összes alhálózatot a lap alhálózatai lapján találhatók. 


Engedélyezheti vagy tilthatja le az adott alhálózatai megfigyelése 

1. Válassza ki, vagy törölje a jelölését a **produkáló alhálózati azonosító** és ellenőrizze, hogy **figyelés használható** kijelölt vagy üres, szükség. Válassza ki, vagy törölje a több alhálózattal. Le van tiltva, az alhálózatok nem, az ügynökök frissítve lett, hogy állítsa le a pingelés más ügynökök figyeli. 
2. Válassza ki az alhálózat kiválasztásával a listából, és a szükséges csomópontok a nem figyelt és a felügyelt csomópontok tartalmazó listák közötti áthelyezése egy bizonyos alhálózat a figyelni kívánt csomópontot. Hozzáadhat egy **egyéni leírást** az alhálózat. 
3. Kattintson a **mentése** a konfiguráció mentéséhez. 

#### <a name="choose-nodes-to-monitor"></a>Válassza ki a csomópontok figyelése

A telepített ügynökkel rendelkező csomópontok találhatók a **csomópontok** fülre. 

1. Válassza ki, vagy törölje a figyelése, vagy állítsa le a figyelési kívánt csomópontokat. 
2. Kattintson a **figyelés használható**, vagy törölje a jelölést, szükség szerint. 
3. Kattintson a **Save** (Mentés) gombra. 


Adja meg a érdekli capability(s): 
- Konfigurálása [Teljesítményfigyelő](log-analytics-network-performance-monitor-performance-monitor.md#configuration)
- Konfigurálása [szolgáltatás végpont-figyelő](log-analytics-network-performance-monitor-performance-monitor.md#configuration)
- Configure [ExpressRoute Monitor](log-analytics-network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>Az gyűjtemény adatait
Hálózati Teljesítményfigyelő TCP SZIN-SYNACK-Nyugtázási kézfogás csomagok TCP van kiválasztva, és az ICMP ECHO ICMP ECHO REPLY használja, amikor az ICMP protokollt van kiválasztva veszteséget és késéseket kapcsolatos információk összegyűjtéséhez. Traceroute is használt topológia lekérése.

A következő táblázat adatgyűjtési módszerek és egyéb adatok gyűjtése hogyan hálózati Teljesítményfigyelő részleteit.

| Platform | Közvetlen ügynök | SCOM-ügynököt | Azure Storage | SCOM szükséges? | Felügyeleti csoport keresztül küldött SCOM ügynök adatok | Gyűjtemény gyakorisága |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |TCP kézfogások/ICMP ECHO üzenetek 5 másodpercentként adatok küldése át 3 percenként |
 

 
A megoldás szintetikus tranzakciók használja fel a hálózat állapotát. OMS-ügynök telepítve különböző pontjain a hálózati exchange TCP- vagy ICMP Echo (attól függően, hogy a protokoll jelölni figyelésre) egymással. A folyamat során ügynökök üzenetváltási időt és a csomag elvesztését, megtudhatja, ha van ilyen. Minden ügynök rendszeres időközönként is végez a különböző útvonalakat, meg kell vizsgálni a hálózaton található más ügynökök egy nyomkövetési útvonalat. Ezen adatok használatával, az ügynökök is kikövetkeztetni a hálózati késés és a csomag bontásban. A tesztek összes öt másodpercenként, és az adatok szerint van összesítve három perces időtartamra az ügynökök a Naplóelemzés szolgáltatáshoz való feltöltés előtt meg kell ismételni. 



>[!NOTE]
> Ügynökök gyakran egymással kommunikálnak, de azok nem hoznak létre jelentős hálózati forgalmat a tesztek végrehajtása közben. Ügynökök csak a TCP SZIN-SYNACK-Nyugtázási kézfogás csomagok a veszteséget és késéseket – nincsenek csomagok továbbított adatok meghatározásához támaszkodnak. A folyamat során ügynökök kommunikálnak egymással csak szükség esetén, és az ügynök kommunikációs topológiát arra optimalizálták, hálózati forgalom csökkentése érdekében.

## <a name="using-the-solution"></a>A megoldás használata 

### <a name="npm-overview-tile"></a>NPM áttekintés csempe 

Miután engedélyezte a hálózati Teljesítményfigyelő megoldás, a megoldás csempe Áttekintés lap gyors áttekintést nyújt az a hálózat állapotát. 

 ![NPM áttekintés csempe](media/log-analytics-network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>Hálózati teljesítmény figyelő irányítópult 

A **felső hálózati állapotával kapcsolatos események** lap felsorolja legutóbbi állapotával kapcsolatos események és riasztások a rendszer és az idő óta módosították, hogy az esemény aktív. A rendszerállapot-esemény vagy riasztás jön létre, amikor a figyelési szabály (adatvesztés, késés, válaszidő vagy sávszélesség-használatot) kiválasztott metrika értéke meghaladja a küszöbértéket. 

A **Teljesítményfigyelő** lap biztosít a hálózati kapcsolat állapotának összegzését, és alhálózat hivatkozásokat tartalmaz a megoldás általi megfigyelés alatt áll. A topológia csempe tájékoztatja a hálózaton a figyelt hálózati elérési utak száma. Közvetlenül erre a csempére kattintva nyit meg a topológia e nézetében. 

A **végpont-figyelő szolgáltatás** lap biztosít a különböző tesztek létrehozott állapotának összegzését. A topológia csempe tájékoztatja a figyelt végpontok száma. Közvetlenül erre a csempére kattintva nyit meg a topológia e nézetében.

A **ExpressRoute figyelő** lap biztosít a különböző társviszony-létesítési ExpressRoute kapcsolattal a megoldás által figyelt állapotának összegzését. A topológia csempe tájékoztatja a ExpressRoute circuit(s) figyelt a hálózaton keresztül a hálózati elérési utak száma. Közvetlenül erre a csempére kattintva nyit meg a topológia e nézetében.

A **közös lekérdezések** tartalmazza, amely a nyers hálózati figyelési közvetlenül az adatok lehívása keresési lekérdezések. Ezeket a lekérdezéseket kiindulási pontként használható a saját testreszabott jelentéskészítéshez lekérdezések létrehozásáról. 

![NPM irányítópult](media/log-analytics-network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>a mélység részletekbe menően tárhatják 

Kattintson a megoldás irányítópultja részletezést különböző mutató hivatkozások be bármely területekre érdeklő mélyebb le. Például amikor megjelenik egy figyelmeztetés vagy egy nem megfelelő állapotú hálózati kapcsolat megjelenjen az irányítópulton, kattinthat, hogy további vizsgálat. Megnyílik egy oldal, amely tartalmazza az adott hálózati kapcsolat minden alhálózati kapcsolat. Minden alhálózati kapcsolat, a késés és egészségügyi állapotát tekintheti meg, és akkor gyorsan megtudhatja, milyen alhálózati kapcsolat okozza a problémát. Ezután kattintson **csomóponti kapcsolat megtekintése** a nem megfelelő állapotú alhálózati kapcsolat csomópont hivatkozások megtekintéséhez. Ezután tekintse meg az egyes csomópontok hivatkozásokat, és a nem megfelelő állapotú csomóponti kapcsolat található. 

Kattinthat **nézet topológia** az útvonalakat a forrás és cél csomópontok között a Ugrás-hop topológiája megtekintéséhez. A nem megfelelő állapotú útvonalak piros is látható, és megtekintheti az egyes ugrások által közzétett, így gyorsan azonosíthatja a problémát, hogy egy adott részének a hálózati késés. 

 

### <a name="network-state-recorder"></a>Hálózati állapotát rögzítő 

Minden a nézet jeleníti meg, időben pillanatképet a hálózat állapotát az adott helyen. Alapértelmezés szerint a legutóbbi állapot jelenik meg. Az a lap tetején látható a pont, amelynek az állapot jelenik meg időben. Ha szeretné visszamehetnek az időben, és a hálózati állapotfigyelő pillanatképe megtekintéséhez kattintson a műveletek sávján. Választhatja azt is, engedélyezni vagy letiltani a lapok automatikus frissítésének, amíg a legfrissebb állapotának megtekintése. 

 ![Hálózati állapotát rögzítő](media/log-analytics-network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>Trend diagramok 

Akkor is részletekbe menően tárhatják minden szinten alkalmazható mérőszám – veszteségeit, késéseit, válaszideje, sávszélesség-használatot a trend tekintheti meg. A vezérlő tetején található a diagram használatával módosíthatja a időtartam alatt a trend. 

Trend diagramok egy korábbi perspektíva teljesítménye metrika teljesítményének megjelenítése. Egyes hálózati probléma átmeneti jellegű, és dolgozza fel a hálózat aktuális állapotának megtekintésével csak nehéz lenne. Ez az az oka a problémák gyorsan surface eltűnnek, mielőtt bárki megjegyzések csak olyan időpontra később újra. Ilyen átmeneti probléma is nehézkes lehet alkalmazás-rendszergazdák számára, mert azok gyakran felület ad ki a az alkalmazás válaszideje, még akkor is, ha az összes alkalmazás-összetevők a jelek szerint zökkenőmentes megtévesztő növekedése. 

Ha megnézi a trend diagram, ahol a hiba jelenik meg a hálózati késés és a csomagveszteség hirtelen csúcs könnyen képes észlelni az ilyen típusú problémák. Majd megtekinteni a hálózati pillanatkép és a pont topológiáját időben, ha a probléma történt volna a hálózat állapotát rögzítő segítségével vizsgálja meg a problémát. 

 
![Trend diagramok](media/log-analytics-network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>Topológia térkép 

NPM között a forrás- és végpont egy interaktív topológia-térképként útvonalak a Ugrás-hop topológiája látható. Kattintva megtekintheti a topológia leképezés a **topológia** a megoldás irányítópulton vagy kattintson a csempére **nézet topológia** hivatkozás a részletes oldalakon.  

A topológia térképen jeleníti meg, hány útvonalak között a forrás és cél- és mit kell tenni az adatcsomagok elérési utak. Az egyes hálózati ugrások által közzétett késleltetés akkor is látható. Amelyek a teljes elérési útja várakozási esetében meghaladja a küszöbértéket (a megfelelő figyelési szabály beállítani) összes elérési utat piros láthatók.  

Gombra a csomópont vagy rámutatáskor felett a topológia-térképként a teljes tartománynév és IP-címnek a csomópont tulajdonságai jelennek meg. Kattintson egy ugrás IP-címének megtekintéséhez. A problémás hálózati ugrások észre a által közzétett éppen a várakozási alapján azonosíthatja. Ha szeretné, adott útvonalak szűrésére összecsukható műveleti ablaktábláján a szűrők segítségével. A hálózati topológiák egyszerűsítheti a csúszkával műveleti ablaktábláján ugrások elrejtésével is. Nagyítás növelésére, de az egér kerekének használatával a topológia térkép ki. 

Vegye figyelembe, hogy a topológia látható módon a térkép 3 rétegbeli topológia, és nem tartalmazza 2. rétegbeli eszközök és kapcsolatok. 

 
![Topológia térkép](media/log-analytics-network-performance-monitor/topology-map.png)
 

## <a name="log-analytics-search"></a>Napló Analytics keresése 

Minden adat, amely az NPM irányítópulton keresztül történő grafikusan kitett és leásási lapok is érhető el natív módon [Naplóelemzési keresési](log-analytics-log-search-new.md). Akkor is hajtsa végre az adatok interaktív elemzések elvégzéséhez a tárházban, különböző forrásokból származó adatok összefüggéseket, hozzon létre egyéni riasztások, egyéni nézeteket hozhat létre és az adatok exportálása Excel-, Power BI, vagy egy megosztható hivatkozást. A közös lekérdezések az irányítópult tartalmaz néhány hasznos lekérdezést, amely kiindulási pontként használható a saját lekérdezések és jelentések létrehozása. 

 

## <a name="provide-feedback"></a>Visszajelzés küldése 

**UserVoice** -ötleteit azt szeretné, hogy működik a hálózati Teljesítményfigyelő szolgáltatások vannak, beküldheti. Látogasson el a [UserVoice lap](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring). 

**Csatlakozás a kohorszok** -még mindig a kohorszok csatlakozás új ügyfelek rendelkező iránt érdeklődik. Része, akkor új szolgáltatások korai eléréséhez és a Hálózatfigyelő teljesítményének javítása érdekében. Érdekli való csatlakozás, ha kitöltés kibővített ez [gyors felmérés](https://aka.ms/npmcohort). 

## <a name="next-steps"></a>További lépések 
- További információ [Teljesítményfigyelő](log-analytics-network-performance-monitor-performance-monitor.md), [végpont-figyelő szolgáltatás](log-analytics-network-performance-monitor-performance-monitor.md), és [ExpressRoute figyelő](log-analytics-network-performance-monitor-expressroute.md). 
