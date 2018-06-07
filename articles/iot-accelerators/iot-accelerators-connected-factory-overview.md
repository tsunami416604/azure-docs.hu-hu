---
title: A csatlakoztatottgyár-alapú megoldás áttekintése – Azure | Microsoft Docs
description: Az Azure IoT csatlakoztatottgyár-alapú megoldásgyorsító leírása.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: 1dbaf4978bd23058d37239ba97bfe8583dc1f2c9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34627090"
---
# <a name="get-started-with-the-connected-factory-solution-accelerator"></a>Bevezetés a csatlakoztatottgyár-alapú megoldásgyorsító használatába

Az Azure [IoT-megoldásgyorsítók][lnk-preconfigured-solutions] több Azure IoT-szolgáltatás kombinálásával nyújtanak végpontok közötti megoldásokat az általános IoT üzleti forgatókönyvek megvalósításához. A *csatlakoztatottgyár-alapú* megoldásgyorsító csatlakozik ipari eszközeihez, és monitorozza azokat. Ez a megoldás az eszközökről származó adatstream elemzésére, ezáltal pedig az üzemeltetés hatékonyságának és nyereségességének növelésére használható.

Ez az oktatóprogram bemutatja, hogyan építheti ki a csatlakoztatottgyár-alapú megoldásgyorsítót. Ezenkívül a megoldásgyorsító alapvető funkcióin is végigvezeti. Ezek közül számos funkcióhoz a megoldás *irányítópultján* keresztül férhet hozzá, amelyet a rendszer a megoldásgyorsító részeként telepít:

![A csatlakoztatottgyár-alapú megoldásgyorsító irányítópultja][img-cf-home]

Az oktatóanyag elvégzéséhez aktív Azure-előfizetésre lesz szüksége.

> [!NOTE]
> Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információ: [Ingyenes Azure-fiók létrehozása][lnk_free_trial].

## <a name="provision-the-solution"></a>A megoldás megvalósítása

1. Jelentkezzen be az azureiotsuite.com címre az Azure-fiókja hitelesítő adataival, majd kattintson a „**+**” elemre egy megoldás létrehozásához.
2. Kattintson a **Kiválasztás** elemre a **Csatlakoztatott gyár** csempén.
3. Adjon meg **Megoldásnevet** a csatlakoztatottgyár-alapú megoldásgyorsítóhoz.
4. Válassza ki a megoldás kiépítéséhez használni kívánt **Előfizetést** és **Régiót**.
5. Kattintson a **Megoldás létrehozása** gombra a kiépítés elkezdéséhez. Ez a folyamat általában több percig tart.

### <a name="while-you-wait-for-the-provisioning-process-to-complete"></a>Amíg a kiépítési folyamat befejeződésére vár

1. Kattintson a megoldás **Kiépítési** állapotát jelző csempére.
2. Megtekintheti a **Kiépítési állapotokat**, miközben az Azure-szolgáltatások telepítése megtörténik az Azure-előfizetésben.
3. A kiépítés befejezése után az állapot **Kész** értékre változik.
4. Kattintson a csempére, és a jobb oldali panelen láthatja a megoldás részleteit.

> [!NOTE]
> Ha problémái vannak a megoldásgyorsító telepítésekor, tekintse meg az [Engedélyek az azureiotsuite.com webhelyen][lnk-permissions] és a [Csatlakoztatott gyár – GYIK](iot-accelerators-faq-cf.md) fejezetet. Ha a problémák továbbra is fennállnak, hozzon létre egy szolgáltatásjegyet a [portálon][lnk-portal].

Hiányol bizonyos részleteket a megoldásával kapcsolatban? A [felhasználói visszajelzési webhelyen](https://feedback.azure.com/forums/321918-azure-iot) elküldheti a szolgáltatásokkal kapcsolatos javaslatait.

## <a name="scenario-overview"></a>Forgatókönyv áttekintése

Amikor üzembe helyezi a csatlakoztatottgyár-alapú megoldásgyorsítót, az előre fel lesz töltve olyan erőforrásokkal, amelyekkel végighaladhat egy általános ipari forgatókönyvön. Ebben a forgatókönyvben a megoldáshoz csatlakozó több üzem ad le jelentést a teljes eszközhatékonyság (overall equipment efficiency, OEE) és a fő teljesítménymutatók (KPI) kiszámításához szükséges adatértékeket. A következő forgatókönyvek bemutatják, hogyan végezheti el a következőket:

* Gyárakra, gyártósorokra, állomásokra vonatkozó OEE- és KPI-értékek figyelése
* Az eszközök által létrehozott telemetriaadatok elemzése az Azure Time Series Insights segítségével
* Reagálás a riasztásokra a problémák felszámolása érdekében

A forgatókönyv fő előnye, hogy az összes műveletet távolról végezheti el, a megoldás irányítópultjáról. Nincs szüksége hozzá az eszközök fizikai címére.

## <a name="view-the-solution-dashboard"></a>A megoldás irányítópultjának megtekintése

A megoldás irányítópultján kezelheti az üzembe helyezett megoldást. Az irányítópult egy globális gyárkonfiguráció hierarchikus megjelenítése, amelyen megtekintheti például az OEE-ket és KPI-ket, és új csomópontokat tehet közzé a telemetriához és műveleti riasztásokhoz.

1. Amikor a kiépítés befejeződött, és a megoldásgyorsító csempéje **Kész** állapotot jelez, válassza az **Indítás** gombot a csatlakoztatott gyár megoldásportál új lapon való megnyitásához.

    ![A megoldásgyorsító indítása][img-launch-solution]

1. Alapértelmezés szerint a megoldás portálja az *irányítópultot* jeleníti meg. A portál más területeire az oldal bal oldali menüjével navigálhat.

    ![A csatlakoztatottgyár-alapú megoldásgyorsító irányítópultja][cf-img-menu]

Az irányítópult az alábbi információkat jeleníti meg:

* A **Gyári telephelyek** panelt, amelyen ellenőrizhető a megoldás állapota, helye és aktuális termelési konfigurációja. A megoldás első futtatásakor már rendelkezésre áll néhány szimulált eszköz. A gyártósor-szimuláció három valós OPC UA-kiszolgálót tartalmaz gyártósoronként, amelyek szimulált feladatokat hajtanak végre és adatokat osztanak meg. Az OPC UA architektúráról a [Csatlakoztatott gyár – GYIK](iot-accelerators-faq-cf.md) fejezetben talál további információt.
* A megoldáshoz csatlakoztatott egyes eszközök helyét megjelenítő **térképet**. A megoldás a Bing Maps API-val képes megjeleníteni az információkat a térképen. Ha az előfizetéshez engedélyezve van a Bing Maps Enterprise API használata, a rendszer automatikusan ezt használja. Ha nincs engedélyezve, a [gyakori kérdésekből][lnk-faq] megtudhatja, hogyan teheti dinamikussá a térképet.
* Egy **Riasztások** panelt, amely riasztásokat jelenít meg, ha egy telemetria- vagy OEE/KPI-érték meghalad egy adott küszöbértéket.
* Egy **Overall Equipment Efficiency** (Teljes eszközhatékonyság) panelt, amely a teljes vállalat vagy a megtekintett gyár/gyártósor/állomás OEE-értékeit jeleníti meg. Az érték az állomásnézettől a vállalati szintig összesítve van. Az OEE értéke és annak összetevői részletesebben is elemezhetők.
* Egy **Key Performance Indicators** (Fő teljesítménymutatók) panelt, amely a teljes vállalat vagy a megtekintett gyár/gyártósor/állomás által előállított egységeket és felhasznált energiát mutatja. Az értékek az állomásnézettől a vállalati szintig összesítve vannak.

## <a name="view-factories"></a>Üzemek megtekintése

A *Gyári telephelyek* panel a megoldás részét képező összes üzem földrajzi helyét, állapotát és aktuális termelési konfigurációját megjeleníti. A helyek listájából átléphet a megoldáshierarchia más szintjeire. A lista sorai hivatkozások, amelyek az adott helyen található gyártósorok részletes adataira mutatnak, amelyek segítenek feltárni a gyártósorok minden részletét, egészen az állomásszintű nézetig. A listán szűrőt is alkalmazhat.

![A csatlakoztatottgyár-alapú megoldásgyorsító gyárai][cf-img-factories]

1. A **Factory panel** a megoldás részét képező gyárak listáját tartalmazza.

2. A gyárlistán először a kiépítési folyamat által létrehozott hat gyár látható. Hozzáadhat további szimulált eszközöket és fizikai eszközöket is a megoldáshoz.

3. Valamely gyár részleteinek megtekintéséhez kattintson az adott sorra a gyárlistában.

4. Valamely gyártósor részleteinek megtekintéséhez kattintson az adott sorra a listában.

5. Valamely gyártósor állomásához tartozó közzétett OPC UA-csomópontok megtekintéséhez kattintson a vonatkozó sorra a listában.

6. Valamely állomás adott csomópontjára vonatkozó részletek megtekintéséhez kattintson az adott sorra a listában. Ez a művelet elindítja a helyi panelt a Time Series Insights-vizualizációkkal. A diagramokra kattintva részletesebb elemzéseket is végezhet a Time Series Insights Explorer környezetében.

## <a name="view-map"></a>Térkép megtekintése

Ha előfizetésével hozzá tud férni a Bing Maps API-hoz, a *Factories* (Üzemek) térkép a megoldás részét képező összes üzem földrajzi helyét és állapotát megjeleníti. Az adott hely részleteit a térképen megjelenített helyekre kattintva tárhatja fel.

![A csatlakoztatottgyár-alapú megoldásgyorsító térképe][cf-img-map]

## <a name="view-alarms"></a>Riasztások megtekintése

A **Riasztások** panel olyan riasztásokat jelenít meg, amelyek arra figyelmeztetnek, ha egy jelentett érték vagy egy kiszámított OEE/KPI meghaladja a hozzá beállított határértéket. A panel a hierarchia összes szintjére vonatkozó riasztásokat megjeleníti, az állomásszinttől egészen a globális nézetig. A riasztások a riasztás leírását, dátumát és időpontját, helyét és előfordulásainak számát tartalmazzák. A Time Series Insights-adatok segítségével mélyebb bepillantást nyerhet a riasztást kiváltó adatokba. Ahol lehetséges, a riasztások Time Series Insights-adatait a rendszer vizualizálja. Ha Ön rendszergazda, a riasztásokon a következő alapértelmezett műveleteket hajthatja végre:

* Lezárhatja a riasztást.
* Nyugtázhatja a riasztást.

Igény szerint összetettebb műveleteket is végrehajthat. Például egy szerelvény nyomási OPC UA-csomópontja esetében a következőket teheti:

* Támogatási információkat jeleníthet meg egy weboldalon egy új böngészőablakban.
* A riasztás okának elhárítása érdekében meghívhat egy OPC UA-metódust az eszközön.
* Felfüggesztheti az alapértelmezett műveletek rendelkezésre állását.

    ![A csatlakoztatottgyár-alapú megoldásgyorsító riasztásai][cf-img-alerts]

> [!NOTE]
> Ezek a riasztások a megoldásgyorsító egy konfigurációs fájljában megadott szabályok alapján jönnek létre. Ezek a szabályok riasztásokat hoznak létre, ha az OEE- vagy KPI-értékek, vagy az OPC UA-csomópont értékei meghaladják a beállított küszöbértékeket.

1. Az **Riasztások panel** a jelen megoldásban létrehozott riasztásokat mutatja.

2. Egy riasztás részleteinek megtekintéséhez kattintson az adott riasztásra a riasztások panelén.

3. A riasztás adatainak további elemzéséhez kattintson a diagramra a riasztások panelén a Time Series Insights Explorer környezetének megnyitásához.

4. A riasztások kezeléséhez több művelet is elérhető a riasztások panelén. Válassza ki a megfelelő lehetőséget, és kattintson a műveletet végrehajtó parancsgombra.

## <a name="view-overall-equipment-efficiency"></a>A teljes eszközhatékonyság megtekintése

Az OEE a gyártási folyamat hatékonyságát osztályozza a gyártáshoz kapcsolódó főbb működési paraméterek alapján. Az OEE egy iparági szabvány mérőszám, amely a rendelkezésre állás, a teljesítmény és a minőség besorolásainak szorzata: OEE = rendelkezésre állás x teljesítmény x minőség.

![A csatlakoztatottgyár-alapú megoldásgyorsító OEE-je][cf-img-oee]

1. A hierarchia valamely szintjére vonatkozó OEE megtekintéséhez lépjen az adott nézetre. A nézet OEE mutatója megjelenik a panelen, azon elemekkel együtt, amelyek összesen kiteszik az OEE százalékos értékét.

2. Az OEE mélyebb elemzéséhez a hierarchiaadatok valamelyik szintjén kattintson az OEE, a rendelkezésre állás, a teljesítmény vagy a minőség százalékos értékére. Megjelenik egy Time Series Insights-vizualizációkat tartalmazó helyi panel, amelyek az elmúlt óra, az elmúlt 24 óra és az elmúlt 7 nap adatait mutatják.

    ![A csatlakoztatottgyár-alapú megoldásgyorsító TSI-vizualizációja][cf-img-tsi-visualization]

3. A riasztás adatainak további elemzéséhez kattintson a diagramra a riasztások panelén. Ez a művelet megnyitja a Time Series Insights Explorer környezetet.

    ![A csatlakoztatottgyár-alapú megoldásgyorsító TSI Explorere][cf-img-tsi-explorer]

## <a name="view-key-performance-indicators"></a>Fő teljesítménymutatók megtekintése

A megoldás két fő teljesítménymutatót biztosít: *egységek száma óránként* és *felhasznált energia (kWh)*.

![A csatlakoztatottgyár-alapú megoldásgyorsító KPI-je][cf-img-kpi]

1. A hierarchia valamely szintjére vonatkozó óránkénti egységszám vagy felhasznált energia megtekintéséhez lépjen az adott nézetre. Az óránkénti egységszám és a felhasznált energia megjelenik a panelen.

2. Az óránkénti egységszám vagy a felhasznált energia elemzéséhez a hierarchia valamely szintjén kattintson a mutatóra a **Fő teljesítménymutatók** panelen. Megjelenik egy Time Series Insights-vizualizációkat tartalmazó helyi panel, amelyek az elmúlt óra, az elmúlt 24 óra és az elmúlt 7 nap adatait mutatják.

## <a name="scenario-review"></a>Forgatókönyv áttekintése

Ebben a forgatókönyvben megfigyelte az üzemek OEE és KPI mutatóit az irányítópulton. Ezután a Time Series Insights használatával további információkhoz jutott, hogy az OEE-k és KPI-k telemetriaadatainak részletesebb feltárásával segíthesse elő a rendellenességek észlelését. A riasztási panelen megtekintette az üzemekben jelentkező problémákat, és a rendelkezésre műveletekkel orvosolta a riasztásokat.

## <a name="other-features"></a>Egyéb jellemzők

A következő szakaszokban a csatlakoztatottgyár-alapú megoldás néhány egyéb jellemzőjéről lesz szó, amelyek nem szerepeltek az előző forgatókönyvben.

## <a name="apply-filters"></a>Szűrők alkalmazása

1. Kattintson a **tölcsérre** a gyári telephelyek vagy a riasztások panelén elérhető szűrők listájának megjelenítéséhez.

2. Megjelenik a szűrők panelje.

    ![A csatlakoztatottgyár-alapú megoldásgyorsító szűrői][cf-img-alert-filter]

3. Válassza ki a kívánt szűrőt. A szűrőmezőkben szabad szöveget is megadhat.

4. A rendszer működésbe lépteti a szűrőt. A szűrő állapota az irányítópulton is megjeleníthető egy tölcsér ikonon keresztül, amely az üzemek és a riasztások táblázataiban is elérhető.

    ![A csatlakoztatottgyár-alapú megoldásgyorsító szűrői][cf-img-alert-filter-funnel]

    > [!NOTE]
    > Az aktív szűrők nincsenek hatással a megjelenített OEE és KPI mutatókra, csupán a listák tartalmát szűkítik.

5. A szűrő törléséhez kattintson a tölcsér ikonra, majd a szűrőre a szűrő helyi panelén. Ekkor az **Összes** kifejezés jelenik meg az üzemek és a riasztások tábláiban.

## <a name="browse-an-opc-ua-server"></a>OPC UA-kiszolgáló tallózása

A megoldásgyorsító üzembe helyezésekor automatikusan sor kerül a szimulált OPC UA-kiszolgálók kiosztására, amelyek a megoldás böngészőjében tallózhatók. Ezek a kiszolgálók *szimulált OPC UA-kiszolgálók*. A szimulált kiszolgálók megkönnyítik a megoldásgyorsítóval történő kísérletezést, anélkül, hogy valódi, fizikai kiszolgálók üzembe helyezésére lenne szükség. Valós OPC UA-kiszolgálóknak a megoldáshoz történő csatlakoztatásáról [az OPC UA-eszköz csatlakoztatottgyár-alapú megoldásgyorsítóhoz történő csatlakoztatásával][lnk-connect-cf] foglalkozó oktatóanyagban olvashat.

1. Kattintson a **böngésző ikonra** az irányítópult navigációs sávján.

    ![A csatlakoztatottgyár-alapú megoldásgyorsító kiszolgálótallózója][cf-img-server-browser]

2. Válasszon egyet a kiszolgálók közül az előre konfigurált listában. A lista a megoldásgyorsítóban üzembe helyezett kiszolgálókat mutatja.

    ![A csatlakoztatottgyár-alapú megoldásgyorsító kiszolgálóinak kiválasztása][cf-img-server-choice]

3. Kattintson a **Connect** (Csatlakozás) gombra. Megjelenik egy biztonsági párbeszédablak. A szimuláció esetében biztonságosan kattinthat a **Proceed** (Folytatás) gombra.

4. Kattintson bármely csomópontra a kiszolgálófán a kibontásához. A telemetriaadatokat közzétevő csomópontokat egy pipa jelöli.

    ![A csatlakoztatottgyár-alapú megoldásgyorsító kiszolgálófája][cf-img-server-tree]

5. Egy csomópont olvasásához, írásához, közzétételéhez vagy meghívásához kattintson jobb gombbal az adott elemre. Az elérhető műveletek a jogosultságaitól és a csomópont attribútumaitól függnek. Az olvasási lehetőség egy helyi panelt jelenít meg, amelyen az adott csomópont értéke látható. Az írási lehetőség egy olyan helyi panelt jelenít meg, amelyen új értéket adhat meg. A hívási lehetőség egy csomópontot jelenít meg, amelyen megadhatja a hívás paramétereit.

## <a name="publish-a-node"></a>Csomópont közzététele

Ha egy *szimulált OPC UA-kiszolgálóhoz* tallóz, új csomópontokat is közzétehet, ha szeretne. Ezeknek a csomópontoknak a telemetriáját is elemezheti a megoldásban. Ezek a *szimulált OPC UA-kiszolgálók* megkönnyítik a megoldásgyorsítóval történő kísérletezést, anélkül, hogy valódi, fizikai eszközök üzembe helyezésére lenne szükség.

1. Lépjen egy közzétenni kívánt csomópontra az OPC UA-kiszolgáló tallózási fáján.

2. Kattintson a jobb gombbal a csomópontra.

3. Válassza a **Publish** (Közzététel) lehetőséget.

    ![A csatlakoztatott gyár közzétesz egy csomópontot][cf-img-publish-node]

4. Megjelenik egy helyi panel, amely tájékoztatja, hogy a közzététel sikeres volt. A csomópont megjelenik az állomásszinten, mellette egy pipával.

    ![Előre konfigurált csatlakoztatott gyár sikeres közzététele][cf-img-publish-success]

## <a name="command-and-control"></a>Parancs és vezérlés

A csatlakoztatott gyár segítségével közvetlenül a felhőből irányíthatja és felügyelheti ipari eszközeit. A szolgáltatással az eszközök által létrehozott riasztásokra is reagálhat, például parancsokat küldhet a felhőből egy eszközre. A rendelkezésre álló parancsokat az OPC UA-kiszolgáló tallózási fájának **StationCommands** csomópontján találja. Ebben a forgatókönyvben megnyit egy nyomáskiegyenlítő szelepet egy müncheni gyártósor összeszerelő állomásán. Az irányítási és felügyeleti funkciók használatához **Rendszergazda** szerepkörrel kell rendelkeznie a megoldásgyorsító üzemelő példányán.

1. Lépjen a **StationCommands** csomópontra az OPC UA-kiszolgáló tallózási fáján.

2. Válassza ki a használni kívánt parancsot.

3. Kattintson a jobb gombbal a csomópontra.

4. Válassza a **Call** (Hívás) parancsot.

    ![A csatlakoztatottgyár-alapú megoldásgyorsító hívás parancsa][cf-img-call-command]

5. Megjelenik egy helyi panel, amely tájékoztatja a meghívni készült metódusról, valamint az esetleges vonatkozó paraméterek részleteiről.

6. Válassza a **Call** (Hívás) parancsot.

    ![A csatlakoztatottgyár-alapú megoldásgyorsító hívási helyei panelje][cf-img-call-context]

7. A helyi panel frissül, és tájékoztatja róla, ha a metódus meghívása sikeres volt. A hívás sikerességét ellenőrizheti a hívás eredményeképpen frissített nyomásmérő csomópont értékének leolvasásával.

    ![A csatlakoztatottgyár-alapú megoldásgyorsító sikeres hívása][cf-img-call-success]

## <a name="behind-the-scenes"></a>A színfalak mögött

Megoldásgyorsító üzembe helyezésekor az üzembehelyezési folyamat több erőforrást hoz létre a kiválasztott Azure-előfizetésben. Ezeket az erőforrásokat az Azure [Portalon][lnk-portal] tekintheti meg. Az üzembehelyezési folyamat létrehoz egy **erőforráscsoportot** a megoldásgyorsítóhoz kiválasztott néven alapuló névvel:

![Megoldásgyorsító az Azure Portalon][img-cf-portal]

Az egyes erőforrások beállításainak megtekintéséhez válassza ki az erőforrást az erőforráscsoport erőforráslistájában.

Megtekintheti a megoldásgyorsító forráskódját is. A csatlakoztatottgyár-alapú megoldásgyorsító forráskódja az [azure-iot-connected-factory][lnk-cfgithub] GitHub-adattárban található:

Amikor elkészült, törölheti a megoldásgyorsítót az Azure-előfizetésből az [azureiotsuite.com][lnk-azureiotsuite] webhelyen. Így könnyedén törölheti a megoldásgyorsító létrehozásakor megkapott összes erőforrást.

> [!NOTE]
> Ahhoz, hogy biztosan törölje a megoldásgyorsítóhoz kapcsolódó összes elemet, törölje a megoldást az [azureiotsuite.com][lnk-azureiotsuite] webhelyről. Ne törölje az erőforráscsoportot a portálon.

## <a name="next-steps"></a>További lépések

Most, hogy üzembe helyezett egy működő megoldásgyorsítót, a következő cikkek elolvasásával folytathatja az ismerkedést az IoT-megoldásgyorsítók használatával:

* [A csatlakoztatottgyár-alapú megoldásgyorsító bemutatója][lnk-rm-walkthrough]
* [Az eszköz csatlakoztatása a csatlakoztatottgyár-alapú megoldásgyorsítóhoz][lnk-connect-cf]
* [Engedélyek az azureiotsuite.com webhelyen][lnk-permissions]

[img-cf-home]:media/iot-accelerators-connected-factory-overview/cf-dashboard.png
[img-launch-solution]: media/iot-accelerators-connected-factory-overview/launch-cf.png
[cf-img-menu]: media/iot-accelerators-connected-factory-overview/cf-dashboard-menu.png
[cf-img-factories]:media/iot-accelerators-connected-factory-overview/cf-dashboard-factory.png
[cf-img-map]:media/iot-accelerators-connected-factory-overview/cf-dashboard-map.png
[cf-img-alerts]:media/iot-accelerators-connected-factory-overview/cf-dashboard-alerts.png
[cf-img-oee]:media/iot-accelerators-connected-factory-overview/cf-dashboard-oee.png
[cf-img-kpi]:media/iot-accelerators-connected-factory-overview/cf-dashboard-kpi.png
[cf-img-tsi-visualization]:media/iot-accelerators-connected-factory-overview/cf-dashboard-tsi.png
[cf-img-tsi-explorer]:media/iot-accelerators-connected-factory-overview/tsi-explorer.png
[cf-img-server-browser]: media/iot-accelerators-connected-factory-overview/cf-dashboard-browser.png
[cf-img-server-choice]: media/iot-accelerators-connected-factory-overview/cf-select-server.png
[cf-img-server-tree]:media/iot-accelerators-connected-factory-overview/cf-server-tree.png
[cf-img-publish-node]:media/iot-accelerators-connected-factory-overview/cf-publish-node1.png
[cf-img-publish-success]:media/iot-accelerators-connected-factory-overview/cf-publish-success.png
[cf-img-call-command]:media/iot-accelerators-connected-factory-overview/cf-command-and-control-call.png
[cf-img-call-context]:media/iot-accelerators-connected-factory-overview/cf-command-and-control-call-button.png
[cf-img-call-success]:media/iot-accelerators-connected-factory-overview/cf-command-and-control-succeed.png
[img-cf-portal]:media/iot-accelerators-connected-factory-overview/cf-resource-group.png
[cf-img-alert-filter]:media/iot-accelerators-connected-factory-overview/cf-filter.png
[cf-img-alert-filter-funnel]:media/iot-accelerators-connected-factory-overview/cf-filter-funnel.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]:iot-accelerators-what-are-solution-accelerators.md
[lnk-azureiotsuite]: https://www.azureiotsolutions.com
[lnk-portal]: http://portal.azure.com/
[lnk-cfgithub]: https://github.com/Azure/azure-iot-connected-factory
[lnk-rm-walkthrough]:iot-accelerators-connected-factory-sample-walkthrough.md
[lnk-connect-cf]:iot-accelerators-connected-factory-gateway-deployment.md
[lnk-permissions]: iot-accelerators-permissions.md
[lnk-faq]: iot-accelerators-faq.md
