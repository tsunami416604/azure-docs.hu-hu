---
title: Hálózati Teljesítményfigyelő megoldás az Azure-ban |} Microsoft Docs
description: Hálózati teljesítmény figyelése az Azure-ban a hálózatok, a közel valós idejű észlelésére, és keresse meg a hálózati szűk keresztmetszetek teljesítményének figyeléséhez nyújt segítséget.
services: log-analytics
documentationcenter: ''
author: abshamsft
manager: carmonm
editor: ''
ms.assetid: 5b9c9c83-3435-488c-b4f6-7653003ae18a
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2018
ms.author: abshamsft
ms.openlocfilehash: 6e81f2cdd0cd3f62d93c85c1a073e0b9df542ec7
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="network-performance-monitor-solution-in-azure"></a>Hálózati Teljesítményfigyelő megoldás az Azure-ban

![Hálózati Teljesítményfigyelő szimbólum](./media/log-analytics-network-performance-monitor/npm-symbol.png)


Hálózati teljesítmény figyelőjének értéke a hibrid felhő alapú hálózatfigyelési megoldás, amely segítséget nyújt a hálózati infrastruktúra különböző pontok közötti hálózati teljesítmény figyelése. Emellett segít figyelemmel hálózati kapcsolattal szolgáltatásba és alkalmazási végpontokat és a figyelő Azure ExpressRoute teljesítményét. 

Hálózati teljesítmény a figyelő észleli, például a forgalom blackholing útválasztási hibák és problémák, amelyek hagyományos hálózati figyelési módszerek nem észleli a hálózati problémák. A megoldás riasztásokat állít elő, és értesítést küld, ha a küszöb a hálózati kapcsolat megsértése. Biztosítja a időben történő észlelése érdekében a hálózati teljesítményproblémák és a localizes egy adott hálózati szegmens vagy az eszköz a probléma forrása. 

Hálózati Teljesítményfigyelő három széleskörű lehetőségeket nyújt: 

* [Teljesítményfigyelő](log-analytics-network-performance-monitor-performance-monitor.md):-alapú telepítések és a helyszíni helyek közötti hálózati kapcsolat figyelheti több adatközpontok, és a fiókirodák és kritikus fontosságú a többrétegű alkalmazások vagy mikroszolgáltatások létrehozására. A Teljesítményfigyelő képes észlelni a hálózati problémák, mielőtt a felhasználók panaszkodnak mert.

* [Végpont-figyelő szolgáltatás](log-analytics-network-performance-monitor-service-endpoint.md): figyelheti a kapcsolatot a felhasználók a szolgáltatások fontos adatokhoz, annak megállapítása, hogy milyen infrastruktúra az elérési út és azonosítása, amelyen a hálózati szűk keresztmetszeteket történik. Valamilyen okból kimaradás tudni a felhasználók előtt, és tekintse meg a problémák pontos helyét a hálózati elérési út mentén. 

    Ez a funkció segítségével HTTP, HTTPS, TCP és az ICMP figyelése a közel valós idejű vagy hagyományosan a rendelkezésre állás és a szolgáltatás válaszidő alapján tesztek kerülnek végrehajtásra. A csomag veszteséget és késéseket hálózati hozzájárulása is figyelheti. A hálózati topológia térkép hálózati lassulást különítheti el. A probléma tesztüzeméhez elvégzett mentén a hálózati elérési út a csomópont a szolgáltatást, a késési adatokat minden egyes ugrásnál azonosíthatja. Beépített tesztek segítségével figyelheti a hálózati kapcsolat az Office 365 és a Dynamics CRM minden előzetes beállítás nélkül. Ezzel a lehetőséggel figyelheti bármely TCP-kompatibilis végponthoz, például webhelyekhez, SaaS-alkalmazásokhoz, PaaS alkalmazások és SQL-adatbázisok hálózati kapcsolat.

* [Az ExpressRoute-figyelő](log-analytics-network-performance-monitor-expressroute.md): végpontok közötti kapcsolat és a fiókirodák és az Azure-ban közötti teljesítményének figyelése Azure ExpressRoute keresztül.  

További információ a különböző képességeket által támogatott [hálózati Teljesítményfigyelő](https://docs.microsoft.com/azure/networking/network-monitoring-overview) online is elérhető.
 
## <a name="supported-regions"></a>Régiók
NPM figyelhető a hálózatok és az alkalmazások bármely részén a világ, egy olyan munkaterületről, amely egy, a következő régiók közötti kapcsolat:
* Nyugat-Európa
* USA nyugati középső régiója
* USA keleti régiója
* Délkelet-Ázsia
* Dél-kelet-Ausztrália
* Dél-UK
* Egyesült Államokbeli kormányzati Virginia

ExpressRoute-figyelő érhető el a támogatott régiók listáját a [dokumentáció](https://docs.microsoft.com/azure/expressroute/how-to-npm?utm_swu=8117#regions).


## <a name="set-up-and-configure"></a>Beállítása és konfigurálása

### <a name="install-and-configure-agents"></a>Telepíteni és konfigurálni az ügynököket 

A legfontosabb folyamatok segítségével telepíthet ügynököket a [csatlakozás Windows rendszerű számítógépeken az Azure Naplóelemzés szolgáltatáshoz](log-analytics-windows-agents.md) és [csatlakozás az Operations Manager szolgáltatáshoz](log-analytics-om-agents.md).

### <a name="where-to-install-the-agents"></a>Az ügynökök telepítésének helyéről 

* **Teljesítményfigyelő**: legalább egy csomópont telepítése az Operations Management Suite-ügynököket minden alhálózat, amelyből a más alhálózatokra való hálózati kapcsolat figyelni kívánt csatlakozik.

    Ha figyelni szeretné a hálózati kapcsolat, telepíthet ügynököket a mindkét, hogy a kapcsolat végpontja. Ha bizonytalan a hálózat topológiáját, telepítse az ügynököt a kritikus fontosságú munkaterhelésekhez, amelyek között a hálózati teljesítmény figyelni kívánt kiszolgálókon. Például a hálózati kapcsolatot a webkiszolgáló és az SQL-t futtató figyelni kívánt, ha egy ügynök telepítése mindkét kiszolgálón. Ügynökök figyelik a hálózati kapcsolat (hivatkozások) állomások, nem magukat a gazdagépek között. 

* **Végpont-figyelő szolgáltatás**: az Operations Management Suite-ügynök telepítése minden egyes csomóponton, amelyből el kívánja a hálózati kapcsolatot a szolgáltatásvégpontot figyelése. Példa: Ha kívánja figyelni hálózati kapcsolat az Office 365 a office helyekről O1, O2 és O3 címkével. Telepítse az Operations Management Suite ügynököt legalább egy csomópont minden O1, O2 és O3. 

* **Az ExpressRoute-figyelő**: legalább egy Operations Management Suite-ügynök telepítése az Azure virtuális hálózaton. Legalább egy ügynök is telepítése a helyi alhálózat, amely ExpressRoute magánhálózati társviszony-létesítés keresztül csatlakozik az.  

### <a name="configure-operations-management-suite-agents-for-monitoring"></a>Figyelés az Operations Management Suite-ügynökök konfigurálása 

Hálózati teljesítmény figyelése szintetikus tranzakciók használja a forrás és cél ügynökök között a hálózati teljesítmény figyeléséhez. Választhat TCP és az ICMP protokollt Teljesítményfigyelő- és végpont-figyelő szolgáltatás képességek a figyelés. TCP ExpressRoute figyelő szolgál. Győződjön meg arról, hogy a tűzfal engedélyezi-e az Operations Management Suite-ügynökök úgy dönt, a protokollal figyeléshez használt közötti kommunikációt. 

* **A TCP protokoll**: Ha úgy dönt, TCP protokollt a figyeléshez, nyissa meg az ügynökökhöz, győződjön meg arról, hogy az ügynökök kapcsolódhatnak egymáshoz hálózati Teljesítményfigyelő és ExpressRoute figyelő használt tűzfalport. Nyissa meg a portot, a PowerShell-parancsprogrammal a EnableRules.ps1 paraméterek nélkül egy PowerShell-ablakban rendszergazdai jogosultságokkal.

    A parancsfájl-megoldás által igényelt beállításkulcsokat hoz létre. A Windows tűzfal-szabályokat az ügynökök számára a TCP-kapcsolatok létrehozása egymás mellett is létrehoz. A beállításkulcsok hozta létre a parancsfájl adja meg, hogy a naplófájl a hibakeresési naplók és a naplók fájl elérési útját. A parancsfájl az ügynök-kommunikációhoz használt TCP-portot is meghatároz. Ezek a kulcsok értékeit automatikusan a parancsfájl által vannak állítva. Ezek a kulcsok manuális ne módosítsa. A port, megnyílik az 8084. A paraméter portszám a parancsfájl megadásával egyéni portot is használhat. Használhatja ugyanazt a portot a számítógépeken, ahol a parancsfájl futtatása. 

    >[!NOTE]
    > A parancsfájl a csak a Windows tűzfal helyben konfigurálja. Ha a hálózati tűzfal, győződjön meg arról, hogy lehetővé teszi a hálózati teljesítményt figyelő által használt TCP-portot adatforgalmat.

    >[!NOTE]
    > Nem kell EnableRules.ps1 PowerShell-parancsprogram futtatásához a végpont Szolgáltatásfigyelő szolgáltatásra.

    

* **Az ICMP protokoll**: Ha ICMP protokollt a figyeléshez, a következő tűzfalszabályokat megbízhatóan használatára az ICMP engedélyezése:
    
   ```
   netsh advfirewall firewall add rule name="NPMDICMPV4Echo" protocol="icmpv4:8,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6Echo" protocol="icmpv6:128,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4DestinationUnreachable" protocol="icmpv4:3,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6DestinationUnreachable" protocol="icmpv6:1,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV4TimeExceeded" protocol="icmpv4:11,any" dir=in action=allow 
   netsh advfirewall firewall add rule name="NPMDICMPV6TimeExceeded" protocol="icmpv6:3,any" dir=in action=allow 
   ```
 

### <a name="configure-the-solution"></a>A megoldás konfigurálása 

1. A hálózati Teljesítményfigyelő megoldás hozzáadása a munkaterületen, a [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.NetworkMonitoringOMS?tab=Overview). Az ismertetett folyamatot is használható [hozzáadni a Naplóelemzési megoldások a megoldások gyűjteményből](log-analytics-add-solutions.md). 
2. Nyissa meg a Naplóelemzési munkaterületet, és válassza ki a **áttekintése** csempére. 
3. Válassza ki a **hálózati Teljesítményfigyelő** üzenettel csempe *megoldás további konfigurálást igényel*.

   ![Hálózati teljesítmény figyelése csempe](media/log-analytics-network-performance-monitor/npm-config.png)

4. Az a **telepítő** lapon megjelenik a lehetőség, hogy telepítse az Operations Management Suite-ügynököket és a figyelő az ügynökök konfigurálása a **általános beállítások** nézet. Amint azt korábban, ha a telepített és konfigurált Operations Management Suite ügynökök, válassza ki a **telepítő** nézetre, és konfigurálja a funkció szeretne használni. 

   **Teljesítményfigyelő**: válassza ki a szintetikus tranzakciók használni kívánt protokollt a **alapértelmezett** Teljesítményfigyelő szabályt, majd válassza **mentés, és továbbra is**. Ez a protokoll-beállítás csak a rendszer által létrehozott alapértelmezett szabály tartalmazza. Válassza ki a protokoll minden alkalommal, amikor olyan szabályt hoz létre a Teljesítményfigyelő explicit módon kell. Mindig átválthat a **alapértelmezett** beállítások szabályok a **Teljesítményfigyelő** (megjelenik a nap-0 konfiguráció befejezése után) fülre, és módosítsa a protokoll később. Ha nem szeretné a rPerfomance figyelő funkció, az alapértelmezett szabály letilthatja a **alapértelmezett** beállítások szabályok a **Teljesítményfigyelő** fülre.

   ![A figyelő a teljesítmény nézet](media/log-analytics-network-performance-monitor/npm-synthetic-transactions.png)
    
   **Végpont-figyelő szolgáltatás**: A képességet biztosít a beépített előre konfigurált vizsgálatok az ügynökök és az Office 365 Dynamics 365 hálózati kapcsolatok figyelésére. Válassza ki, amelyek mellett a jelölőnégyzetek bejelölésével figyelni kívánt Office 365 és Dynamics 365 szolgáltatásokat. A figyelni kívánt ügynökök kiválasztásához jelölje ki a **hozzáadása ügynökök**. Ha nem szeretné a kívánt és a segítségével később állíthat be, ne válassza semmit, majd **mentés, és továbbra is**.

   ![Szolgáltatási végpont-figyelő nézet](media/log-analytics-network-performance-monitor/npm-service-endpoint-monitor.png)

   **ExpressRoute-figyelő**: válasszon **felderítése most** titkos esetében, amelyek a virtuális hálózatok az Azure-előfizetés csatlakoznak a Naplóelemzési munkaterület kapcsolódó összes ExpressRoute felderítéséhez. 

   >[!NOTE] 
   > A megoldás jelenleg csak az ExpressRoute magánhálózati társviszony deríti fel. 

   >[!NOTE] 
   > Csak a Naplóelemzési munkaterület kapcsolódik az előfizetéshez tartozó virtuális hálózathoz csatlakozó magán társviszony felderítése. Ha ExpressRoute kívül az előfizetés, a munkaterület kapcsolódó virtuális hálózathoz van csatlakoztatva, a Naplóelemzési munkaterület létrehozása előfizetésekben. Hálózati Teljesítményfigyelő használatával figyelheti ezek esetében.

   ![Az ExpressRoute-figyelő nézet](media/log-analytics-network-performance-monitor/npm-express-route.png)

   A felderítés után egy táblázatban láthatók a felderített magánhálózati társviszony. 

   ![Hálózati teljesítmény figyelési konfiguráció lap](media/log-analytics-network-performance-monitor/npm-private-peerings.png)
    
A figyelési ezek esetében az kezdetben állapotban van letiltva. Válassza ki, minden egyes társviszony-létesítést, amely segítségével nyomon követni kívánt, és azokat a Részletek nézetben a jobb oldali figyelését. Válassza ki **mentése** a konfiguráció mentéséhez. További tudnivalókért tekintse meg a "Konfigurálása ExpressRoute-figyelés" cikket. 

Miután a telepítés befejezését követően az adatok feltöltése egy órát vesz igénybe 30 perc. A megoldás összesíti az adatokat a hálózatról, amíg a következő üzenet jelenik-e *megoldás további konfigurálást igényel* a hálózati teljesítményt figyelő **áttekintése** csempére. Miután az adatok gyűjtése és indexelt, a **áttekintése** csempe változik, és figyelmeztet, a hálózat állapotát, az összefoglaló. Majd módosíthatja a figyelést a csomópontok, mely Operations Management Suite az ügynökök telepítve vannak, valamint az alhálózatok felderítése a környezetében.

#### <a name="edit-monitoring-settings-for-subnets-and-nodes"></a>Alhálózatok és a csomópontok figyelési beállításainak módosítása 

Legalább egy ügynököt futtató összes alhálózat szerepel a **alhálózatai** lapon, a konfiguráció lapon. 


Engedélyezze, vagy tiltsa le a meghatározott alhálózatokra figyelését:

1. Válassza ki, vagy a törölje a jelet a jelölőnégyzetből a **produkáló alhálózati azonosító**. Ezután ellenőrizze, hogy **figyelés használható** kijelölt vagy üres, szükség. Válassza ki, vagy törölje a több alhálózattal. Le van tiltva, ha alhálózatai nem figyeli, és az ügynökök frissítése más ügynökök pingelés leállítására. 
2. Válassza ki a csomópontok egy bizonyos alhálózat a figyelni kívánt. Az alhálózat válasszon a listából, és helyezze át a szükséges csomópontok között a nem figyelt és a felügyelt csomópontok tartalmazó listákat. Az alhálózat egy egyéni leírást adhat hozzá.
3. Válassza ki **mentése** a konfiguráció mentéséhez. 

#### <a name="choose-nodes-to-monitor"></a>Válassza ki a csomópontok figyelése

A csomópontok, amelyek rendelkeznek egy telepített ügynök szerepel a **csomópontok** fülre. 

1. Válassza ki, vagy törölje a figyelése, vagy állítsa le a figyelési kívánt csomópontokat. 
2. Válassza ki **figyelés használható**, vagy törölje a jelölést, szükség szerint. 
3. Kattintson a **Mentés** gombra. 


Adja meg a kívánt lehetőségeket:

- [Teljesítményfigyelő](log-analytics-network-performance-monitor-performance-monitor.md#configuration)
- [Szolgáltatási végpont-figyelő](log-analytics-network-performance-monitor-performance-monitor.md#configuration)
- [ExpressRoute Monitor](log-analytics-network-performance-monitor-expressroute.md#configuration)

 

## <a name="data-collection-details"></a>Az gyűjtemény adatait
Veszteséget és késéseket céghez, hálózati Teljesítményfigyelő TCP SZIN-SYNACK-Nyugtázási kézfogás csomagokat használ, ha úgy dönt, hogy a TCP protokollt. Hálózati teljesítmény figyelő az ICMP ECHO ICMP ECHO REPLY, ha úgy dönt, hogy az ICMP protokollt. Nyomkövetést is használt topológia lekérése.

A következő táblázat adatgyűjtési módszerek és egyéb adatok gyűjtése hogyan hálózati Teljesítményfigyelő részleteit.

| Platform | Közvetlen ügynök | A System Center Operations Manager-ügynök | Azure Storage | Az Operations Manager szükséges? | Az Operations Manager ügynök adatait a felügyeleti csoport keresztül küldött | A gyűjtés gyakorisága |
| --- | --- | --- | --- | --- | --- | --- |
| Windows | &#8226; | &#8226; |  |  |  |TCP kézfogások/ICMP ECHO üzenetek 5 másodpercentként adatok küldése át 3 percenként |
 

 
A megoldás szintetikus tranzakciók használja fel a hálózat állapotát. Operations Management Suite-ügynökök telepítése különböző pontjain a hálózati exchange TCP- vagy ICMP Echo egymással. A figyeléshez kijelölt protokoll függ, hogy használja-e az ügynökök a TCP- vagy ICMP Echo. A folyamat során ügynökök üzenetváltási időt és a csomag elvesztését, megtudhatja, ha van ilyen. Minden ügynök rendszeres időközönként is végez a különböző útvonalakat, meg kell vizsgálni a hálózaton található más ügynökök egy nyomkövetési útvonalat. Ezen adatok használatával, az ügynökök is kikövetkeztetni a hálózati késés és a csomag bontásban. A tesztek öt másodpercenként ismétlődjenek. Adatok szerint van összesítve körülbelül 3 percig az ügynökök szolgáltatásba a Naplóelemzés szolgáltatáshoz való feltöltés előtt.



>[!NOTE]
> Ügynökök gyakran egymással kommunikálnak, de azok nem jelentős hálózati forgalmat a tesztek végrehajtása közben. Ügynökök csak a TCP SZIN-SYNACK-Nyugtázási kézfogás csomagok meghatározásához a veszteséget és késéseket támaszkodnak. Nincs adatcsomagok megtörténik. A folyamat során ügynökök kommunikálnak egymással csak szükség esetén. Az ügynök kommunikációs topológiát arra optimalizálták, hálózati forgalom csökkentése érdekében.

## <a name="use-the-solution"></a>A megoldás használja 

### <a name="network-performance-monitor-overview-tile"></a>Hálózati teljesítmény – áttekintés csempe 

Miután engedélyezte a hálózati Teljesítményfigyelő megoldás, a megoldás csempét a **áttekintése** lap gyors áttekintést nyújt a hálózati állapotát. 

 ![Hálózati teljesítmény – áttekintés csempe](media/log-analytics-network-performance-monitor/npm-overview-tile.png)

### <a name="network-performance-monitor-dashboard"></a>Hálózati teljesítmény figyelő irányítópult 

* **Hálózati állapotával kapcsolatos események felső**: ezen a lapon felsorolja a legutóbbi állapotával kapcsolatos események és riasztások tartalmazzák a rendszer és az idő óta az események aktív volt. A rendszerállapot-esemény vagy riasztás jön létre, amikor a figyelési szabály (adatvesztés, késés, válaszidő vagy sávszélesség-használatot) kiválasztott metrika értéke meghaladja a küszöbértéket. 

* **Az ExpressRoute-figyelő**: ezen a lapon állapot-összefoglalók a különböző ExpressRoute-társviszony-létesítési kapcsolatokhoz a megoldást kínál figyeli. A **topológia** csempe a hálózaton keresztül figyelt ExpressRoute-Kapcsolatcsoportok hálózati elérési utak számát jeleníti meg. Ez a csempe kattintva válassza ki a **topológia** nézet.

* **Végpont-figyelő szolgáltatás**: ezen a lapon a különböző tesztek létrehozott állapot-összefoglalók biztosít. A **topológia** csempe végpontok, amelyek figyelése számát jeleníti meg. Ez a csempe kattintva válassza ki a **topológia** nézet.

* **Teljesítményfigyelő**: ezen a lapon állapotfigyelő összefoglalóinak biztosít a **hálózati** hivatkozásokat és **alhálózat** hivatkozásokat tartalmaz, amelyek a megoldás figyeli. A **topológia** csempe figyelt hálózati elérési utak számát jeleníti meg a hálózaton. Ez a csempe kattintva válassza ki a **topológia** nézet. 

* **Általános lekérdezések**: Ez a lap tartalmaz, amely a nyers hálózati figyelési közvetlenül az adatok lehívása keresési lekérdezések. Ezeket a lekérdezéseket használhatja kiindulási pontként a saját testreszabott jelentéskészítéshez lekérdezések létrehozása. 

   ![Hálózati teljesítmény figyelő irányítópult](media/log-analytics-network-performance-monitor/npm-dashboard.png)

 

### <a name="drill-down-for-depth"></a>a mélység részletekbe menően tárhatják 

Kiválaszthatja, hogy a megoldás irányítópultja részletezést különböző mutató hivatkozások be bármely területekre érdeklő mélyebb le. Amikor megjelenik egy figyelmeztetés vagy egy nem megfelelő állapotú hálózati kapcsolat megjelenjen az irányítópulton, válassza ki például úgy, hogy további vizsgálat. A lap felsorolja az adott hálózati kapcsolathoz tartozó alhálózati kapcsolat. Megtekintheti az adatvesztés, a késés és a minden alhálózati kapcsolat állapotát. Gyorsan talál problémákat okoz, mely alhálózat-hivatkozást. Válassza ki **csomóponti kapcsolat megtekintése** a nem megfelelő állapotú alhálózati kapcsolat csomópont hivatkozások megtekintéséhez. Ezután tekintse meg az egyes csomópontok hivatkozásokat, és a nem megfelelő állapotú csomóponti kapcsolat található. 

Válassza ki **nézet topológia** az útvonalakat a forrás és cél csomópontok között a Ugrás-hop topológiája megtekintéséhez. A nem megfelelő állapotú útvonalak piros jelennek meg. Az egyes ugrások által közzétett, így gyorsan azonosíthatja a problémát, hogy egy adott részének a hálózati késés tekintheti meg.

 

### <a name="network-state-recorder-control"></a>Hálózati állapotát rögzítő vezérlő

Minden a nézet jeleníti meg, időben pillanatképet a hálózat állapotát az adott helyen. Alapértelmezés szerint a legutóbbi állapot jelenik meg. Az a lap tetején látható a pont, amelynek az állapot jelenik meg időben. Válassza ki, ha a hálózati állapotfigyelő pillanatképe előző egyszerre, **műveletek**. Is engedélyezheti vagy letilthatja a lapok automatikus frissítésének, amíg a legfrissebb állapotának megtekintése. 

 ![Hálózati állapotát rögzítő](media/log-analytics-network-performance-monitor/network-state-recorder.png)

 

### <a name="trend-charts"></a>Trend diagramok 

Akkor is részletekbe menően tárhatják egyes szintjein a megfelelő metrika trendjét tekintheti meg. Lehet, hogy adatvesztés, a késés, a válaszidő vagy a sávszélesség-használatot. A időtartam alatt a trend módosításához használja a vezérlő tetején található a diagram. 

Trend diagramok egy korábbi perspektíva teljesítménye metrika teljesítményének megjelenítése. Egyes hálózati probléma átmeneti jellegű és nehezen dolgozza fel a hálózat csak a jelenlegi állapotának megtekintésével. Problémák gyors surface, és eltűnnek, mielőtt bárki megjegyzések csak olyan időpontra később újra. Ilyen átmeneti probléma is nehézségekbe ütközhet alkalmazás-rendszergazdák számára. A probléma gyakran jelenik meg a megtévesztő növekedése az alkalmazás válaszideje, még ha az összes alkalmazás-összetevők a jelek szerint zökkenőmentes. 

Trend diagram megtekintésével könnyen képes észlelni az ilyen típusú problémákra. A probléma hirtelen csúcs hálózati késés és a csomagveszteség jelenik meg. Vizsgálja meg a problémát, ha a probléma előfordulásakor hálózati pillanatkép és külön topológia megtekintése a hálózati állapotát rögzítő vezérlővel.

 
![Trend diagramok](media/log-analytics-network-performance-monitor/trend-charts.png)
 

### <a name="topology-map"></a>Topológia térkép 

Teljesítményfigyelő hálózati útvonalak a forrás- és végpont egy interaktív topológia-térképként között a Ugrás-hop topológiája látható. A topológia térkép megtekintéséhez jelölje ki a **topológia** a megoldás irányítópultja csempét. Is kiválaszthat a **nézet topológia** hivatkozás a részletes oldalakon. 

A topológia térképen jeleníti meg, hány útvonalak között a forrás és cél- és mit kell tenni az adatcsomagok elérési utak. Az egyes hálózati ugrások által közzétett késleltetés akkor is látható. Amelyek a teljes elérési útja várakozási esetében meghaladja a küszöbértéket (a megfelelő figyelési szabály beállítani) összes elérési utat piros láthatók. 

Amikor kijelöl egy csomópont vagy rámutatáskor felett a topológia-térképként, a csomópont tulajdonságait, például teljes tartománynév és IP-cím látható. Válassza ki a ugrás IP-címének megtekintéséhez. A problémás hálózati ugrások azonosíthatja a várakozási részt vesz észre. Adott útvonalak szűréséhez használja a szűrők összecsukható műveleti ablaktábláján. A hálózati topológiák leegyszerűsítése elrejtése az ugrások műveleti ablaktábláján a csúszka használatával. Nagyítás, vagy a topológia térkép kicsinyítheti az egér kerekének használatával. 

A topológia látható módon a térkép 3 rétegbeli topológia, és nem tartalmazza 2. rétegbeli eszközök és kapcsolatok. 

 
![Topológia térkép](media/log-analytics-network-performance-monitor/topology-map.png)
 

## <a name="log-analytics-search"></a>A Naplóelemzési keresése 

Minden adat, amely grafikusan elérhetővé a hálózati Teljesítményfigyelő irányítópulton keresztül történő és leásási oldalon érhető el natív módon [Naplóelemzési keresési](log-analytics-log-search-new.md). Hajtsa végre az adatok interaktív elemzések elvégzéséhez a tárházban, és a különböző forrásokból származó adatok összefüggéseket. Is hozhat létre egyéni riasztások és a nézetek és az adatok exportálása az Excel, a Power bi-ban vagy a megosztható hivatkozást. A **közös lekérdezések** az irányítópult tartalmaz néhány hasznos lekérdezések, amelyek a saját lekérdezések és jelentések használhatja kiindulási pontként. 

##<a name="pricing"></a>Díjszabás

Az árakkal kapcsolatos információkat [online](log-analytics-network-performance-monitor-pricing-faq.md).

## <a name="provide-feedback"></a>Visszajelzés küldése 

* **UserVoice:** azt szeretné, hogy működik a hálózati Teljesítményfigyelő szolgáltatások ötleteit vannak, beküldheti. Látogasson el a [UserVoice lap](https://feedback.azure.com/forums/267889-log-analytics/category/188146-network-monitoring). 

* **Csatlakozás a kohorszok:** még mindig a kohorszok csatlakozás új ügyfelek rendelkező iránt érdeklődik. Részeként azt hogy korai eléréséhez új szolgáltatásokat és Hálózatfigyelő teljesítményének javítása érdekében lehetőséget. Ha érdekli való csatlakozás, töltse ki a [gyors felmérés](https://aka.ms/npmcohort). 

## <a name="next-steps"></a>További lépések 
További információ [Teljesítményfigyelő](log-analytics-network-performance-monitor-performance-monitor.md), [végpont-figyelő szolgáltatás](log-analytics-network-performance-monitor-performance-monitor.md), és [ExpressRoute figyelő](log-analytics-network-performance-monitor-expressroute.md). 
