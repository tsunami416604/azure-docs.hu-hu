---
title: Csatlakozáskezelő (előzetes verzió) | Microsoft Docs
description: Ismerje meg, hogyan figyelheti a hálózati kommunikációt egy elosztott környezetben a kapcsolatkezelő (előzetes verzió) használatával.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
tags: azure-resource-manager
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2020
ms.author: vinigam
ms.custom: mvc
ms.openlocfilehash: 31733abc945fe7c751f786649fb05b753a7c243d
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2020
ms.locfileid: "91408874"
---
# <a name="network-connectivity-monitoring-with-connection-monitor-preview"></a>Hálózati kapcsolat figyelése a kapcsolat figyelője (előzetes verzió)

A kapcsolódási figyelő (előzetes verzió) egységes, végpontok közötti kapcsolatok figyelését teszi lehetővé az Azure Network Watcherban. A kapcsolódási figyelő (előzetes verzió) funkció támogatja a hibrid és az Azure-beli Felhőbeli üzembe helyezést. A Network Watcher eszközöket biztosít az Azure-környezetek kapcsolattal kapcsolatos metrikáinak figyelésére, diagnosztizálására és megtekintésére.

Íme néhány használati eset a Csatlakozáskezelő (előzetes verzió) szolgáltatáshoz:

- Az előtér-webkiszolgáló virtuális gépe egy adatbázis-kiszolgáló virtuális géppel kommunikál egy többrétegű alkalmazásban. Szeretné megtekinteni a két virtuális gép közötti hálózati kapcsolatot.
- Azt szeretné, hogy az USA keleti régiójában lévő virtuális gépek Pingelje a virtuális gépeket az USA középső régiójában, és szeretné összehasonlítani a régiók közötti hálózati késéseket.
- Több helyszíni Office-telephelye is van Seattle-ben, Washingtonban és Ashburn-ben, Virginia államban. Az Office-helyek Microsoft 365 URL-címekhez csatlakoznak. Microsoft 365 URL-címek felhasználói számára hasonlítsa össze a Seattle és a Ashburn közötti késéseket.
- A hibrid alkalmazásnak kapcsolódnia kell egy Azure Storage-végponthoz. A helyszíni hely és az Azure-alkalmazás ugyanahhoz az Azure Storage-végponthoz csatlakozik. Össze szeretné hasonlítani a helyszíni hely késéseit az Azure-alkalmazás késésével.
- Szeretné megtekinteni a helyszíni telepítések és a felhőalapú alkalmazást futtató Azure-beli virtuális gépek közötti kapcsolatot.

Az előzetes verzióban a kapcsolati figyelő a két funkció közül a legjobbat ötvözi: a Network Watcher- [kapcsolat figyelője](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview#monitor-communication-between-a-virtual-machine-and-an-endpoint) szolgáltatás és a Network Performance monitor (NPM) [szolgáltatás kapcsolódási figyelője](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-service-connectivity), a [ExpressRoute figyelése](https://docs.microsoft.com/azure/expressroute/how-to-npm)és a [Teljesítményfigyelő](https://docs.microsoft.com/azure/azure-monitor/insights/network-performance-monitor-performance-monitor) funkció.

Íme néhány előny a Csatlakozáskezelő (előzetes verzió) szolgáltatáshoz:

* Egységes, intuitív élmény az Azure-hoz és a hibrid monitorozási igényekhez
* Régiók közötti, több-munkaterület közötti kapcsolat figyelése
* Nagyobb Szondázási gyakoriságok és a hálózati teljesítmény jobb láthatósága
* Gyorsabb riasztás a hibrid üzembe helyezésekhez
* HTTP-, TCP-és ICMP-alapú kapcsolatok ellenőrzésének támogatása 
* Metrikák és Log Analytics támogatás az Azure és a nem Azure-beli tesztelési beállításokhoz

![Ábra, amely bemutatja, hogyan kommunikál a kapcsolatkezelő az Azure-beli virtuális gépekkel, a nem Azure-beli gazdagépekkel,-végpontokkal és az adattárolási hellyel](./media/connection-monitor-2-preview/hero-graphic.png)

A Csatlakozáskezelő (előzetes verzió) figyeléshez való használatának megkezdéséhez kövesse az alábbi lépéseket: 

1. Figyelési ügynökök telepítése.
1. Network Watcher engedélyezése az előfizetésben.
1. Hozzon létre egy összekötő-figyelőt.
1. Az adatelemzés és a riasztások beállítása.
1. A hálózatban felmerülő problémák diagnosztizálása.

A következő szakaszokban részletesen ismertetjük ezeket a lépéseket.

## <a name="install-monitoring-agents"></a>Figyelési ügynökök telepítése

A kapcsolati figyelő egyszerűsített végrehajtható fájlokra támaszkodik a kapcsolati ellenőrzések futtatásához.  Az Azure-környezetek és a helyszíni környezetek közötti kapcsolati ellenőrzéseket is támogatja. A használt végrehajtható fájl attól függ, hogy a virtuális gép az Azure-ban vagy a helyszínen van-e tárolva.

### <a name="agents-for-azure-virtual-machines"></a>Az Azure Virtual Machines-ügynökök

Ahhoz, hogy a Csatlakozáskezelő felismerje az Azure-beli virtuális gépeket megfigyelési forrásként, telepítse a Network Watcher-ügynök virtuálisgép-bővítményét. Ezt a bővítményt *Network Watcher bővítménynek*is nevezzük. Az Azure Virtual Machines használatához a bővítménynek teljes körű monitorozást és egyéb speciális funkciókat kell elindítania. 

A [virtuális gép létrehozásakor](https://docs.microsoft.com/azure/network-watcher/connection-monitor#create-the-first-vm)a Network Watcher bővítményt is telepítheti. A [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux) és a [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows)Network Watcher bővítményét külön is telepítheti, konfigurálhatja és elháríthatja.

A hálózati biztonsági csoport (NSG) vagy a tűzfal szabályai letilthatják a forrás és a cél közötti kommunikációt. A Csatlakozáskezelő észleli ezt a problémát, és diagnosztikai üzenetként jeleníti meg a topológiában. A kapcsolat figyelésének engedélyezéséhez győződjön meg arról, hogy a NSG és a tűzfalszabályok engedélyezik a TCP-vagy ICMP-csomagok használatát a forrás és a cél között.

### <a name="agents-for-on-premises-machines"></a>Helyszíni gépek ügynökei

Ahhoz, hogy a Csatlakozáskezelő felismerje a helyszíni gépeket a figyeléshez, telepítse a Log Analytics ügynököt a gépekre. Ezután engedélyezze a Network Performance Monitor megoldást. Ezek az ügynökök Log Analytics munkaterületekhez vannak társítva, ezért be kell állítania a munkaterület-azonosítót és az elsődleges kulcsot, mielőtt az ügynökök el tudják indítani a figyelést.

A Windows rendszerű gépek Log Analytics ügynökének telepítéséhez lásd: [Azure monitor virtuálisgép-bővítmény a Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows)rendszerhez.

Ha az elérési út tartalmazza a tűzfalakat vagy a hálózati virtuális berendezéseket (NVA), akkor győződjön meg arról, hogy a célhely elérhető.

## <a name="enable-network-watcher-on-your-subscription"></a>Network Watcher engedélyezése az előfizetésen

A virtuális hálózattal rendelkező összes előfizetés engedélyezve van a Network Watcher. Amikor létrehoz egy virtuális hálózatot az előfizetésében, Network Watcher automatikusan engedélyezve lesz a virtuális hálózat régiójában és előfizetésében. Ez az automatikus engedélyezés nem befolyásolja az erőforrásokat, vagy díjköteles. Győződjön meg arról, hogy a Network Watcher nincs explicit módon letiltva az előfizetésen. 

További információ: [Network Watcher engedélyezése](https://docs.microsoft.com/azure/network-watcher/network-watcher-create).

## <a name="create-a-connection-monitor"></a>Kapcsolatfigyelő létrehozása 

A kapcsolati figyelő rendszeres időközönként figyeli a kommunikációt. Tájékoztatja Önt a Elérhetőség és a késés változásairól. A forrás-és a cél végpontok közötti aktuális és korábbi hálózati topológiát is megtekintheti.

A források lehetnek olyan Azure-beli virtuális gépek vagy helyszíni számítógépek, amelyeken telepítve van egy felügyeleti ügynök. A cél végpontok lehetnek Microsoft 365 URL-címek, Dynamics 365 URL-címek, egyéni URL-címek, Azure-beli virtuális gépek erőforrás-azonosítói, IPv4, IPv6, FQDN vagy bármely tartománynév.

### <a name="access-connection-monitor-preview"></a>Elérési kapcsolat figyelője (előzetes verzió)

1. A Azure Portal kezdőlapján válassza a **Network Watcher**lehetőséget.
1. A bal oldalon a **figyelés** szakaszban válassza a **Csatlakozáskezelő (előzetes verzió)** lehetőséget.
1. Megjelenik az összes, a Csatlakozáskezelő (előzetes verzió) szolgáltatásban létrehozott figyelő. A kapcsolódási figyelő klasszikus felhasználói felületén létrehozott kapcsolódási figyelők megtekintéséhez lépjen a **Csatlakozáskezelő** lapra.
    
  :::image type="content" source="./media/connection-monitor-2-preview/cm-resource-view.png" alt-text="A Csatlakozáskezelő (előzetes verzió) szolgáltatásban létrehozott kapcsolatok figyelőit bemutató képernyőfelvétel" lightbox="./media/connection-monitor-2-preview/cm-resource-view.png":::

### <a name="create-a-connection-monitor"></a>Kapcsolatfigyelő létrehozása

A Csatlakozáskezelő (előzetes verzió) szolgáltatásban létrehozott kapcsolódó figyelőkben a helyszíni és az Azure-beli virtuális gépeket is felveheti forrásként. Ezek a kapcsolati figyelők a végpontokhoz való kapcsolódás figyelésére is használhatók. A végpontok lehetnek az Azure-ban vagy bármely más URL-címen vagy IP-címen.

A Csatlakozáskezelő (előzetes verzió) a következő entitásokat tartalmazza:

* **Csatlakozáskezelő erőforrás** – régióra jellemző Azure-erőforrás. A következő entitások mindegyike egy Csatlakozáskezelő erőforrás tulajdonságait képezi.
* **Endpoint (végpont** ) – a kapcsolati ellenőrzésekben részt vevő forrás vagy cél. Ilyen végpontok például az Azure-beli virtuális gépek, a helyszíni ügynökök, az URL-címek és az IP-címek.
* **Teszt konfiguráció** – egy adott protokollra jellemző konfiguráció. A választott protokoll alapján megadhatja a portot, a küszöbértékeket, a tesztelési gyakoriságot és az egyéb paramétereket.
* **Test Group (tesztelési csoport** ) – a forrás-végpontokat, a célként megadott végpontokat és a tesztelési konfigurációkat tartalmazó csoport. Egy Csatlakozáskezelő több tesztelési csoportot is tartalmazhat.
* **Test (teszt** ) – a forrás-végpont, a cél végpont és a tesztelési konfiguráció kombinációja. A teszt a legrészletesebben használt szint, amelyen a figyelési adatokat elérhetővé kell tennie. A figyelési adatmennyiség magában foglalja a sikertelen ellenőrzések százalékos arányát és az oda-és visszaút idejét (RTT).

 ![A kapcsolati figyelőt bemutató diagram, a tesztelési csoportok és tesztek közötti kapcsolat meghatározása](./media/connection-monitor-2-preview/cm-tg-2.png)

A [Azure Portal](connection-monitor-preview-create-using-portal.md) vagy a [ARMClient](connection-monitor-preview-create-using-arm-client.md) használatával létrehozhat egy ügyfélkapcsolat-figyelő előzetes verzióját

A tesztelési csoportba felvett összes forrás, cél és tesztelési konfiguráció az egyes tesztekre bontva kerül. Íme egy példa arra, hogyan oszlanak meg a források és a célhelyek:

* Tesztelési Csoport: TG1
* Források: 3 (A, B, C)
* Célhelyek: 2 (D, E)
* Tesztelési konfigurációk: 2 (config 1, 2. konfiguráció)
* Létrehozott tesztek összesen: 12

| Teszt száma | Forrás | Cél | Konfiguráció tesztelése |
| --- | --- | --- | --- |
| 1 | A | T | 1. konfiguráció |
| 2 | A | T | 2. konfiguráció |
| 3 | A | E | 1. konfiguráció |
| 4 | A | E | 2. konfiguráció |
| 5 | B | T | 1. konfiguráció |
| 6 | B | T | 2. konfiguráció |
| 7 | B | E | 1. konfiguráció |
| 8 | B | E | 2. konfiguráció |
| 9 | C# | T | 1. konfiguráció |
| 10 | C# | T | 2. konfiguráció |
| 11 | C# | E | 1. konfiguráció |
| 12 | C# | E | 2. konfiguráció |

### <a name="scale-limits"></a>Skálázási korlátok

A hálózati figyelők a következő skálázási korlátokkal rendelkeznek:

* Az előfizetések maximális száma régiónként: 100
* Tesztelési csoportok maximális száma kapcsolatonként: 20
* Maximális források és célhelyek kapcsolatonként: 100
* A tesztelési konfigurációk maximális száma kapcsolatonként: 
    * 20 ARMClient keresztül
    * 2 a Azure Portal keresztül

## <a name="analyze-monitoring-data-and-set-alerts"></a>Figyelési adatelemzés és riasztások beállítása

A kapcsolati figyelő létrehozása után a források a tesztelési konfiguráció alapján ellenőrizhetik a helyekhez való kapcsolódást.

### <a name="checks-in-a-test"></a>Tesztek ellenőrzése

A tesztelési konfigurációban kiválasztott protokoll alapján a kapcsolat figyelője (előzetes verzió) a forrás-cél párokhoz tartozó ellenőrzések sorozatát futtatja. Az ellenőrzések a választott tesztelési gyakoriságnak megfelelően futnak.

Ha HTTP-t használ, a szolgáltatás kiszámítja, hogy hány HTTP-válasz érkezett vissza egy érvényes válasz kódját. Az érvényes válasz kódokat a PowerShell és a parancssori felület használatával lehet beállítani. Az eredmény meghatározza a sikertelen ellenőrzések százalékos arányát. A RTT kiszámításához a szolgáltatás a HTTP-hívás és a válasz közötti időt méri.

Ha a TCP vagy az ICMP protokollt használja, a szolgáltatás kiszámítja a csomagok elvesztésének százalékos arányát a sikertelen ellenőrzések százalékos arányának megállapításához. A RTT kiszámításához a szolgáltatás megméri az elküldött csomagok nyugtájának (ACK) fogadására tett időt. Ha engedélyezte a traceroute-adatelemzést a hálózati tesztek számára, a helyszíni hálózatra vonatkozó ugrások közötti adatvesztést és késést láthatja.

### <a name="states-of-a-test"></a>Teszt állapota

A csekkek által visszaadott adathalmazok alapján a tesztek a következő állapotokkal rendelkezhetnek:

* **Pass** – a sikertelen ellenőrzések és RTT százalékos arányának tényleges értékei a megadott küszöbértékeken belül vannak.
* **Fail (sikertelen** ) – a sikertelen ellenőrzések százalékos arányának tényleges értékei, vagy a RTT túllépte a megadott küszöbértéket. Ha nem ad meg küszöbértéket, akkor a teszt meghiúsul, ha a sikertelen ellenőrzések százalékaránya 100.
* **Figyelmeztetés** – 
     * Ha a küszöbérték meg van adva, és a Csatlakozáskezelő (előzetes verzió) megállapítja, hogy az ellenőrzések aránya a küszöbérték 80%-ánál nagyobb, akkor a teszt figyelmeztetésként van megjelölve.
     * A megadott küszöbértékek hiányában a Csatlakozáskezelő (előzetes verzió) automatikusan hozzárendel egy küszöbértéket. Ha túllépi a küszöbértéket, a teszt állapota figyelmeztetésre változik.A TCP-vagy ICMP-tesztek esetében a küszöbérték 750msec. Sikertelen ellenőrzések esetén a küszöbérték 10%. 
* **Meghatározatlan**   – A Log Analytics munkaterületen nincsenek adathalmazok.Metrikák keresése 
* **Nem fut**   – Letiltva a test Group letiltásával  

### <a name="data-collection-analysis-and-alerts"></a>Adatgyűjtés, elemzés és riasztások

A Csatlakozáskezelő (előzetes verzió) által gyűjtött adatokat a Log Analytics munkaterületen tárolja a rendszer. Ezt a munkaterületet a kapcsolódási figyelő létrehozásakor állíthatja be. 

A figyelési adatok Azure Monitor Metrikákban is elérhetők. A figyelési adatait a kívánt ideig a Log Analytics használatával is megőrizheti. Alapértelmezés szerint a Azure Monitor csak 30 napig tárolja a metrikákat. 

[Metrikus riasztásokat is beállíthat az adatokra vonatkozóan](https://azure.microsoft.com/blog/monitor-at-scale-in-azure-monitor-with-multi-resource-metric-alerts/).

#### <a name="monitoring-dashboards"></a>Irányítópultok figyelése

A figyelési irányítópultokon megtekintheti az előfizetések, a régiók, az időbélyegek, a források és a célhelyek számára elérhető kapcsolati figyelők listáját.

Ha Network Watcherról nyit meg a kapcsolódási figyelőt (előzetes verzió), az adatok az alábbiak szerint jeleníthetők meg:

* **Csatlakozáskezelő** – az előfizetésekhez, a régiókhoz, az időbélyegekhez, a forrásokhoz és a célhelyekhez létrehozott összes összekötő-figyelő listája. Ez a nézet az alapértelmezett.
* **Tesztelési csoportok** – az előfizetésekhez, a régiókhoz, az időbélyegekhez, a forrásokhoz és a célhelyekhez létrehozott összes tesztelési csoport listája. Ezeket a tesztelési csoportokat nem a kapcsolatok figyelői szűrik.
* **Test** – az előfizetésekhez, a régiókhoz, az időbélyegekhez, a forrásokhoz és a célhelyekhez futtatott tesztek listája. Ezek a tesztek nem a kapcsolatok figyelője vagy a tesztelési csoportok szerint vannak szűrve.

Az alábbi ábrán a három adatnézetet az 1. nyíl jelzi.

Az irányítópulton kibonthatja az egyes kapcsolati figyelőket a tesztelési csoportok megjelenítéséhez. Ezután az egyes tesztelési csoportok kibontásával megtekintheti a rajta futó teszteket. 

A listát a következő alapján szűrheti:

* **Legfelső szintű szűrők** – keresési lista szöveg szerint, entitás típusa (Csatlakozáskezelő, tesztelési csoport vagy teszt) időbélyeg és hatókör. A hatókör magában foglalja az előfizetéseket, a régiókat, a forrásokat és a rendeltetési típusokat. Az alábbi képen az 1. mező jelenik meg.
* **Állapot-alapú szűrők** – a Csatlakozáskezelő, a test Group vagy a test állapota alapján szűrhet. Lásd a 2. négyzetet az alábbi képen.
* **Riasztáson alapuló szűrő** – a figyelő erőforráson alapuló riasztások szűrése. Az alábbi képen látható 3. mező.

  :::image type="content" source="./media/connection-monitor-2-preview/cm-view.png" alt-text="A kapcsolódási figyelők, a tesztelési csoportok és a tesztek nézeteinek szűrését bemutató képernyőkép (előzetes verzió)" lightbox="./media/connection-monitor-2-preview/cm-view.png":::
    
Ha például meg szeretné tekinteni az összes tesztet a 10.192.64.56 (előzetes verzió), ahol a forrás IP-címe a következő:
1. Módosítsa a nézetet a **tesztre**.
1. A Keresés mezőbe írja be a következőt: *10.192.64.56*
1. A legfelső szintű szűrő **hatókörében** válassza a **források**elemet.

Csak a sikertelen tesztek megjelenítése a 10.192.64.56 (előzetes verzió), ahol a forrás IP-címe a következő:
1. Módosítsa a nézetet a **tesztre**.
1. Az állapot-alapú szűrő esetében válassza a **sikertelen**lehetőséget.
1. A Keresés mezőbe írja be a következőt: *10.192.64.56*
1. A legfelső szintű szűrő **hatókörében** válassza a **források**elemet.

Csak a sikertelen tesztek megjelenítése a outlook.office365.com (előzetes verzió), ahol a cél a következő:
1. Váltás a nézetre a **teszteléshez**.
1. Az állapot-alapú szűrő esetében válassza a **sikertelen**lehetőséget.
1. A Keresés mezőbe írja be a *Outlook.office365.com*
1. A legfelső szintű szűrő **hatókörében** válassza a **Célhelyek**lehetőséget.
  
  :::image type="content" source="./media/connection-monitor-2-preview/tests-view.png" alt-text="Képernyőfelvétel: a szűrt nézetet megjelenítő nézet, amely csak a Outlook.Office365.com célhelyének sikertelen teszteit jeleníti meg" lightbox="./media/connection-monitor-2-preview/tests-view.png":::

Ha tudni szeretné, hogy a Csatlakozáskezelő vagy a tesztelési csoport vagy a teszt meghibásodása miért nem sikerült, kattintson az OK nevű oszlopra.  Ez azt jelzi, hogy a (z)%-os vagy RTT sikertelen ellenőrzések milyen küszöbértéket észleltek, és kapcsolódó diagnosztikai üzenetek
  
  :::image type="content" source="./media/connection-monitor-2-preview/cm-reason-of-failure.png" alt-text="A Csatlakozáskezelő, tesztelési vagy tesztelési csoport meghibásodásának okát bemutató képernyőkép" lightbox="./media/connection-monitor-2-preview/cm-reason-of-failure.png":::
    
A RTT trendjeinek és a sikertelen ellenőrzések százalékos arányának megtekintéséhez:
1. Válassza ki a vizsgálni kívánt kapcsolódási figyelőt.

    :::image type="content" source="./media/connection-monitor-2-preview/cm-drill-landing.png" alt-text="A Csatlakozáskezelő metrikáit bemutató képernyőkép, amelyet a test Group megjelenít" lightbox="./media/connection-monitor-2-preview/cm-drill-landing.png":::

1. A következő részeket fogja látni  
    1. Essentials – a kiválasztott figyelő erőforrás-specifikus tulajdonságai 
    1. Összegzése 
        1. Összesített trendvonalak a RTT és a sikertelen ellenőrzések százalékos arányához a Csatlakozáskezelő összes tesztje esetében. Megadhat egy adott időpontot a részletek megtekintéséhez.
        1. Az első 5 – a tesztelési csoportok, a források és a célhelyek között a sikertelen ellenőrzések RTT vagy százaléka alapján. 
    1. A tesztelési csoportok, a források, a célhelyek és a tesztelési konfigurációk lapjai – a Csatlakozáskezelő tesztelési csoportjait, forrásait vagy célhelyeit sorolja fel. A tesztek ellenőrzése nem sikerült, az összesített RTT és az ellenőrzések sikertelenek voltak% érték.  Az adatgyűjtés ideje alatt is visszatérhet. 
    1. Problémák – a kapcsolati figyelő egyes teszteléseinak ugrási szintű problémái. 

    :::image type="content" source="./media/connection-monitor-2-preview/cm-drill-landing-2.png" alt-text="A kapcsolati figyelő metrikáit ábrázoló képernyőkép, a 2. rész tesztelési csoport" lightbox="./media/connection-monitor-2-preview/cm-drill-landing-2.png":::

1. képes vagy
    * Kattintson az összes teszt megtekintése lehetőségre a kapcsolódási figyelő összes tesztének megtekintéséhez
    * Kattintson az összes tesztelési csoport megtekintése, konfigurációk tesztelése, források és célhelyek elemre, hogy megtekinthesse a részleteket. 
    * Válasszon ki egy tesztelési csoportot, ellenőrizze a konfigurációt, a forrást vagy a célhelyet – az entitás összes tesztjét megtekintheti.

1. Az összes teszt nézetből a következőket teheti:
    * Válassza a tesztek lehetőséget, majd kattintson az összehasonlítás gombra.
    
    :::image type="content" source="./media/connection-monitor-2-preview/cm-compare-test.png" alt-text="2 teszt összehasonlítását bemutató képernyőkép" lightbox="./media/connection-monitor-2-preview/cm-compare-test.png":::
    
    * A fürt használatával bontsa ki az összetett erőforrásokat, például a VNET, az alhálózatokat a gyermek erőforrásaihoz
    * Az összes teszt topológiájának megtekintése a topológia lehetőségre kattintva.

A RTT trendjeinek és a tesztelési csoport sikertelen ellenőrzésének százalékos arányának megtekintéséhez:
1. Válassza ki a vizsgálni kívánt tesztelési csoportot. 
1. A következőhöz hasonlóan fog megjelenni: a kapcsolatkezelő – Essentials, Summary, Table for test groups, sources, Destinations és test konfigurációk. Navigáljon hozzájuk, mint a kapcsolatok figyelője

A RTT trendjeinek és a tesztek sikertelen ellenőrzésének százalékos arányának megtekintéséhez:
1. Válassza ki a vizsgálni kívánt tesztet. A rendszer a hálózati topológiát és a végpontok közötti trend-diagramokat fogja látni a következő ellenőrzésekhez: nem sikerült% és a kerekítési idő. Az azonosított problémák megjelenítéséhez a topológiában válassza ki az ugrást az elérési úton. (Ezek a komló Azure-erőforrások.) Ez a funkció jelenleg nem érhető el a helyszíni hálózatokhoz

  :::image type="content" source="./media/connection-monitor-2-preview/cm-test-topology.png" alt-text="Egy teszt topológiai nézetét bemutató képernyőkép" lightbox="./media/connection-monitor-2-preview/cm-test-topology.png":::

#### <a name="log-queries-in-log-analytics"></a>Lekérdezések naplózása Log Analytics

A Log Analytics használatával egyéni nézeteket hozhat létre a figyelési adatairól. A felhasználói felület által megjelenített összes adatok forrása Log Analytics. Interaktív módon elemezheti az adattárat. A Agent Health vagy más, Log Analyticson alapuló megoldások adatainak összekapcsolása. Exportálja az Excelbe vagy Power BIba, vagy hozzon létre egy megosztható hivatkozást.

#### <a name="metrics-in-azure-monitor"></a>Metrikák az Azure Monitorban

A Csatlakozáskezelő (előzetes verzió) felhasználói felülete előtt létrehozott AverageRoundtripMs a következő négy metrika érhető el:% Probes sikertelen,, ChecksFailedPercent (előzetes) és RoundTripTimeMs (előzetes verzió). A Csatlakozáskezelő (előzetes verzió) szolgáltatásban létrehozott hálózati figyelők esetében az adatok csak a *(előzetes)* címkével megjelölt mérőszámokhoz érhetők el.

  :::image type="content" source="./media/connection-monitor-2-preview/monitor-metrics.png" alt-text="A Csatlakozáskezelő metrikáit bemutató képernyőkép (előzetes verzió)" lightbox="./media/connection-monitor-2-preview/monitor-metrics.png":::

Metrikák használata esetén állítsa be az erőforrástípust a Microsoft. Network/networkWatchers/connectionMonitors

| Metrika | Megjelenített név | Egység | Összesítés típusa | Leírás | Dimenziók |
| --- | --- | --- | --- | --- | --- |
| ProbesFailedPercent | %-Os mintavétel sikertelen | Százalék | Átlag | A kapcsolat figyelési mintavételének százalékos aránya meghiúsult. | Nincsenek méretek |
| AverageRoundtripMs | Átlagos menetidő (MS) | Ezredmásodpercben | Átlag | A forrás és a cél között eljuttatott kapcsolat-figyelési mintavételek átlagos hálózati RTT. |             Nincsenek méretek |
| ChecksFailedPercent (előzetes verzió) | %-Os ellenőrzés sikertelen (előzetes verzió) | Százalék | Átlag | A teszt sikertelen ellenőrzésének százalékos aránya. | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>Sourceresourceid azonosítónak <br>SourceType <br>Protokoll <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>Region |
| RoundTripTimeMs (előzetes verzió) | Oda-és visszaút időpontja (MS) (előzetes verzió) | Ezredmásodpercben | Átlag | A forrás és a cél között továbbított ellenőrzések RTT. Ez az érték nem átlag. | ConnectionMonitorResourceId <br>SourceAddress <br>SourceName <br>Sourceresourceid azonosítónak <br>SourceType <br>Protokoll <br>DestinationAddress <br>DestinationName <br>DestinationResourceId <br>DestinationType <br>DestinationPort <br>TestGroupName <br>TestConfigurationName <br>Region |

#### <a name="metric-based-alerts-for-connection-monitor"></a>Metrika-alapú riasztások a kapcsolatok figyelője számára

A következő módszerekkel metrikai riasztásokat hozhat létre a kapcsolatok figyelője esetén 

1. A csatlakozás figyelője (előzetes verzió), a Csatlakozáskezelő létrehozása során [Azure Portal használatával](connection-monitor-preview-create-using-portal.md#) 
1. A kapcsolatkezelő (előzetes verzió), a "riasztások konfigurálása" használatával az irányítópulton 
1. Azure Monitor – riasztás létrehozása a Azure Monitorban: 
    1. Válassza ki a Csatlakozáskezelő (előzetes verzió) eszközben létrehozott Csatlakozáskezelő-erőforrást.
    1. Győződjön meg arról, hogy a **metrika** a Csatlakozáskezelő jel típusaként jelenik meg.
    1. A **feltétel hozzáadása**területen a **jel neveként**válassza a **ChecksFailedPercent (előzetes verzió)** vagy a **RoundTripTimeMs (előzetes verzió)** lehetőséget.
    1. A **jel típusa**beállításnál válassza a **metrikák**lehetőséget. Válassza például a **ChecksFailedPercent (előzetes verzió)** lehetőséget.
    1. Megjelenik a metrika összes dimenziója. Válassza ki a dimenzió nevét és a dimenzió értékét. Válassza például a **forrás címe** lehetőséget, majd adja meg a Csatlakozáskezelő bármelyik FORRÁSÁNAK IP-címét.
    1. A **riasztási logikában**adja meg a következő adatokat:
        * **Feltétel típusa**: **statikus**.
        * **Feltétel** és **küszöbérték**.
        * **Összesítés részletessége és a kiértékelés gyakorisága: a**Csatlakozáskezelő (előzetes verzió) minden percben frissíti az adatokat.
    1. A **műveletek**területen válassza ki a műveleti csoportot.
    1. Adja meg a riasztás részleteit.
    1. Hozzon létre egy riasztási szabályt.

  :::image type="content" source="./media/connection-monitor-2-preview/mdm-alerts.jpg" alt-text="Képernyőfelvétel a Azure Monitor szabály létrehozása területén. A forrás címe és a forrás végpontjának neve kiemelve" lightbox="./media/connection-monitor-2-preview/mdm-alerts.jpg":::

## <a name="diagnose-issues-in-your-network"></a>A hálózatban felmerülő problémák diagnosztizálása

A Csatlakozáskezelő (előzetes verzió) segítségével diagnosztizálhatja a kapcsolatok figyelője és a hálózat hibáit. A hibrid hálózattal kapcsolatos problémákat a korábban telepített Log Analytics ügynökök észlelik. Az Azure-beli problémákat a Network Watcher bővítmény észleli. 

A hálózati topológiában megtekintheti az Azure-hálózatban felmerülő problémákat.

Azokon a hálózatokon, amelyek forrásai a helyszíni virtuális gépek, a következő problémák észlelhetők:

* A kérelem időkorlátja lejárt.
* A végpontot nem oldja fel a DNS – ideiglenes vagy állandó. Érvénytelen URL-cím.
* Nem található gazdagép.
* A forrás nem tud kapcsolódni a célhelyhez. A cél nem érhető el az ICMP protokollon keresztül.
* Tanúsítványokkal kapcsolatos problémák: 
    * Az ügynök hitelesítéséhez szükséges ügyféltanúsítvány. 
    * A tanúsítvány-áthelyezési lista nem érhető el. 
    * A végpont állomásneve nem felel meg a tanúsítvány tulajdonosának vagy a tulajdonos alternatív nevének. 
    * A főtanúsítvány hiányzik a forrás helyi számítógépének megbízható hitelesítésszolgáltatók tárolójából. 
    * Az SSL-tanúsítvány lejárt, érvénytelen, visszavont vagy nem kompatibilis.

Az Azure-beli virtuális gépeket forrásként szolgáló hálózatok esetében a következő problémák észlelhetők:

* Ügynökkel kapcsolatos problémák:
    * Az ügynök leállt.
    * Sikertelen DNS-feloldás.
    * Nincs figyelő alkalmazás vagy figyelő a célport számára.
    * A szoftvercsatorna nem nyitható meg.
* VIRTUÁLIS gépek állapotával kapcsolatos problémák: 
    * Indítás
    * Leállítás
    * Leállítva
    * Felszabadítás
    * Felszabadítva
    * Újraindítás
    * Nincs lefoglalva
* Hiányzik az ARP-tábla bejegyzése.
* A forgalmat a helyi tűzfal problémái vagy a NSG-szabályok miatt blokkolta a rendszer.
* Virtuális hálózati átjáróval kapcsolatos problémák: 
    * Hiányzó útvonalak.
    * A két átjáró közötti alagút le van választva vagy hiányzik.
    * A második átjáró nem található az alagútban.
    * Nem található egyenrangú információ.
* Az útvonal hiányzik a Microsoft Edge-ben.
* A forgalom a rendszerútvonalak vagy a UDR miatt leállt.
* A BGP nincs engedélyezve az átjáró-kapcsolatban.
* A DIP-mintavétel le van töltve a terheléselosztó számára.

## <a name="next-steps"></a>Következő lépések
    
   * Ismerje meg [, hogyan hozhatja létre a kapcsolódási figyelőt (előzetes verzió) a Azure Portal használatával](connection-monitor-preview-create-using-portal.md)  
   * Megtudhatja [, hogyan hozhat létre ARMClient használatával a kapcsolódási figyelőt (előzetes verzió)](connection-monitor-preview-create-using-arm-client.md)  
