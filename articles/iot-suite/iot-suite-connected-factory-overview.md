---
title: "Az Azure IoT Suite csatlakoztatott gyár áttekintése | Microsoft Docs"
description: "Az Azure IoT Suite előre konfigurált csatlakoztatott gyár megoldásának ismertetése."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-suite
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/24/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: 7c70129ebf4bacc28400cf68418bf898e4e646d2
ms.lasthandoff: 04/25/2017


---
# <a name="get-started-with-the-connected-factory-preconfigured-solution"></a>Az előre konfigurált csatlakoztatott gyár első lépései

## <a name="introduction"></a>Bevezetés

Az Azure IoT Suite [előre konfigurált megoldások][lnk-preconfigured-solutions] több Azure IoT-szolgáltatást kombinálnak, hogy általános IoT üzleti forgatókönyveket megvalósító végpontok közötti megoldásokat nyújtsanak. Az előre konfigurált *csatlakoztatott gyár* megoldás csatlakozik ipari eszközeihez, és megfigyeli azokat. Ez a megoldás az eszközökről származó adatstream elemzésére, ezáltal pedig az üzemeltetés hatékonyságának és nyereségességének növelésére használható.

Az oktatóprogram bemutatja, hogyan építheti ki az előre konfigurált csatlakoztatott gyár megoldást. Valamint az előre konfigurált megoldás alapvető funkcióin is végigvezeti. Ezek közül számos funkcióhoz a megoldás *irányítópultján* keresztül férhet hozzá, amelyet a rendszer az előre konfigurált megoldás részeként telepít:

![Az előre konfigurált csatlakoztatott gyár megoldás irányítópultja][img-cf-home]

Az oktatóanyag elvégzéséhez aktív Azure-előfizetésre lesz szüksége.

> [!NOTE]
> Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információ: [Ingyenes Azure-fiók létrehozása][lnk_free_trial].
> 
> 

## <a name="provision-the-solution"></a>A megoldás megvalósítása

1. Jelentkezzen be az azureiotsuite.com címre az Azure-fiókja hitelesítő adataival, majd kattintson a „**+**” elemre egy megoldás létrehozásához.
2. Kattintson a **Kiválasztás** elemre a **Csatlakoztatott gyár** csempén.
3. Adja meg a **Megoldásnevet** az előre konfigurált távoli figyelési megoldáshoz.
4. Válassza ki a megoldás kiépítéséhez használni kívánt **Előfizetést** és **Régiót**.
5. Kattintson a **Megoldás létrehozása** gombra a kiépítés elkezdéséhez. Ez a folyamat általában több percig tart.

### <a name="while-you-wait-for-the-provisioning-process-to-complete"></a>Amíg a kiépítési folyamat befejeződésére vár

1. Kattintson a megoldás **Kiépítési** állapotát jelző csempére.
2. Megtekintheti a **Kiépítési állapotokat**, miközben az Azure-szolgáltatások telepítése megtörténik az Azure-előfizetésben.
3. A kiépítés befejezése után az állapot **Kész** értékre változik.
4. Kattintson a csempére, és a jobb oldali panelen láthatja a megoldás részleteit.

> [!NOTE]
> Ha problémái vannak az előre konfigurált megoldás telepítésekor, tekintse meg az [Engedélyek az azureiotsuite.com webhelyen][lnk-permissions] és a [GYIK][lnk-faq] fejezetet. Ha a problémák továbbra is fennállnak, hozzon létre egy szolgáltatásjegyet a [portálon][lnk-portal].

Hiányol bizonyos részleteket a megoldásával kapcsolatban? A [felhasználói visszajelzési webhelyen](https://feedback.azure.com/forums/321918-azure-iot) elküldheti a szolgáltatásokkal kapcsolatos javaslatait.

## <a name="scenario-overview"></a>Forgatókönyv áttekintése

Amikor üzembe helyezi az előre konfigurált csatlakoztatott gyár megoldást, az előre fel van töltve olyan erőforrásokkal, amelyekkel elvégezhető egy általános ipari forgatókönyv. Ebben a forgatókönyvben a megoldáshoz csatlakozó több üzem ad le jelentést a teljes eszközhatékonyság (overall equipment efficiency, OEE) és a fő teljesítménymutatók (KPI) kiszámításához szükséges adatértékeket. A következő forgatókönyvek bemutatják, hogyan végezheti el a következőket:

* Gyárakra, gyártósorokra, állomásokra vonatkozó OEE- és KPI-értékek figyelése
* Az eszközök által létrehozott telemetriaadatok elemzése az Azure Time Series Insights segítségével
* Reagálás a riasztásokra a problémák felszámolása érdekében

A forgatókönyv fő előnye, hogy az összes műveletet távolról végezheti el, a megoldás irányítópultjáról. Nincs szüksége hozzá az eszközök fizikai címére.

## <a name="view-the-solution-dashboard"></a>A megoldás irányítópultjának megtekintése

A megoldás irányítópultján kezelheti az üzembe helyezett megoldást. Az irányítópult egy globális gyárkonfiguráció hierarchikus megjelenítése, amelyen megtekintheti például az OEE-ket és KPI-ket, és új csomópontokat tehet közzé a telemetriához és műveleti riasztásokhoz.

1. Amikor a kiépítés befejeződött, és az előre konfigurált megoldás csempéje **Kész** állapotot jelez, válassza az **Indítás** gombot a csatlakoztatott gyár portál új lapon való megnyitásához.

    ![Az előre konfigurált megoldás indítása][img-launch-solution]

1. Alapértelmezés szerint a megoldás portálja az *irányítópultot* jeleníti meg. Az oldal bal oldali menüjével a portál más területeire navigálhat.

    ![Az előre konfigurált csatlakoztatott gyár megoldás irányítópultja][cf-img-menu]

Az irányítópult az alábbi információkat jeleníti meg:

* A **Factory list** (Gyárak listája) panelt, amelyen ellenőrizhető a megoldás állapota, helye és aktuális termelési konfigurációja. A megoldás első futtatásakor már rendelkezésre áll néhány szimulált eszköz. A gyártósor-szimuláció három valós OPC UA-kiszolgálót tartalmaz gyártósoronként, amelyek szimulált feladatokat hajtanak végre és adatokat osztanak meg. 
* A megoldáshoz csatlakoztatott egyes eszközök helyét megjelenítő **térképet**. A megoldás a Bing Maps API-val képes megjeleníteni az információkat a térképen. Ha az előfizetéshez engedélyezve van a Bing Maps Enterprise API használata, a rendszer automatikusan ezt használja. Ha nincs engedélyezve, a [gyakori kérdésekből][lnk-faq] megtudhatja, hogyan teheti dinamikussá a térképet.
* Egy **Alerts** (Riasztások) panelt, amely riasztásokat jelenít meg, ha egy telemetria- vagy OEE/KPI-érték meghalad egy adott küszöbértéket.
* Egy **Overall Equipment Efficiency** (Teljes eszközhatékonyság) panelt, amely a teljes vállalat vagy a megtekintett gyár/gyártósor/állomás OEE-értékeit jeleníti meg. Az érték az állomásnézettől a vállalati szintig összesítve van. Az OEE értéke és annak összetevői részletesebben is elemezhetők.
* Egy **Key Performance Indicators** (Fő teljesítménymutatók) panelt, amely a teljes vállalat vagy a megtekintett gyár/gyártósor/állomás által előállított egységeket és felhasznált energiát mutatja. Az értékek az állomásnézettől a vállalati szintig összesítve vannak.

## <a name="view-factories"></a>Üzemek megtekintése

A *Factories* (Üzemek) panel a megoldás részét képező összes üzem földrajzi helyét, állapotát és aktuális termelési konfigurációját megjeleníti. A helyek listájából átléphet a megoldáshierarchia más szintjeire. A lista sorai hivatkozások, amelyek az adott helyen található gyártósorok részletes adataira mutatnak, amelyek segítenek feltárni a gyártósorok minden részletét, egészen az állomásszintű nézetig. A listán szűrőt is alkalmazhat.

![Előre konfigurált csatlakoztatott gyár megoldás – gyárak][cf-img-factories] 

1. A **Factory panel** a megoldás részét képező gyárak listáját tartalmazza.

2. A gyárlistán először a kiépítési folyamat által létrehozott hat gyár látható. Hozzáadhat további szimulált eszközöket és fizikai eszközöket is a megoldáshoz.

3. Valamely gyár részleteinek megtekintéséhez kattintson az adott sorra a gyárlistában.

4. Valamely gyártósor részleteinek megtekintéséhez kattintson az adott sorra a listában.

5. Valamely gyártósor állomásához tartozó közzétett OPC UA-csomópontok megtekintéséhez kattintson a vonatkozó sorra a listában.

6. Valamely állomás adott csomópontjára vonatkozó részletek megtekintéséhez kattintson az adott sorra a listában. Ez a művelet elindítja a helyi panelt a Time Series Insights-vizualizációkkal. A diagramokra kattintva részletesebb elemzéseket is végezhet a Time Series Insights Explorer környezetében.

## <a name="view-map"></a>Térkép megtekintése

Ha előfizetésével hozzá tud férni a Bing Maps API-hoz, a *Factories* (Üzemek) térkép a megoldás részét képező összes üzem földrajzi helyét és állapotát megjeleníti. A térképen megjelenített helyekre kattintva feltárhatja az adott hely részleteit.

![Előre konfigurált csatlakoztatott gyár megoldás – térkép][cf-img-map]

## <a name="view-alerts"></a>Riasztások megtekintése

Az **Alert** (Riasztások) előzménypanel olyan riasztásokat jelenít meg, amelyek arra figyelmeztetnek, ha egy jelentett érték vagy egy kiszámított OEE/KPI meghaladja a hozzá beállított határértéket. A panel a hierarchia minden szintjére vonatkozó riasztásokat megjeleníti, az állomásszinttől egészen a globális nézetig. A riasztások a riasztás leírását, dátumát és időpontját, helyét és előfordulásainak számát tartalmazzák. A Time Series Insights-adatok segítségével mélyebb bepillantást nyerhet a riasztást kiváltó adatokba. Ahol lehetséges, a riasztások Time Series Insights-adatait a rendszer vizualizálja. Ha Ön rendszergazda, a riasztásokon a következő alapértelmezett műveleteket hajthatja végre:

* Lezárhatja a riasztást.
* Nyugtázhatja a riasztást.

Igény szerint összetettebb műveleteket is végrehajthat. Például egy szerelvény nyomási OPC UA-csomópontja esetében a következőket teheti:

* Betölthet egy weboldalt egy új böngészőablakban, amelyen támogatási információkat jeleníthet meg.
* Meghívhat egy OPC UA-metódust az eszközön a riasztás okának elhárítása érdekében.
* Felfüggesztheti az alapértelmezett műveletek rendelkezésre állását.

    ![Előre konfigurált csatlakoztatott gyár megoldás – riasztások][cf-img-alerts]

> [!NOTE]
> Ezek a riasztások az előre konfigurált megoldás egy konfigurációs fájljában megadott szabályok alapján jönnek létre. Ezek a szabályok riasztásokat hoznak létre, ha az OEE- vagy KPI-értékek, vagy az OPC UA-csomópont értékei meghaladják a beállított küszöbértékeket.

1. Az **Alerts panel** (Riasztások) a jelen megoldásban létrehozott riasztásokat mutatja.

2. Egy riasztás részleteinek megtekintéséhez kattintson az adott riasztásra a riasztások panelén.

3. A riasztás adatainak további elemzéséhez kattintson a diagramra a riasztások panelén a Time Series Insights Explorer környezetének megnyitásához.

4. A riasztások kezeléséhez több művelet is elérhető a riasztások panelén. Válassza ki a megfelelő lehetőséget, és kattintson a műveletet végrehajtó parancsgombra.

## <a name="view-overall-equipment-efficiency"></a>A teljes eszközhatékonyság megtekintése

Az OEE a gyártási folyamat hatékonyságát osztályozza a gyártáshoz kapcsolódó főbb működési paraméterek alapján. Az OEE egy iparági szabvány mérőszám, amely a rendelkezésre állás, a teljesítmény és a minőség besorolásainak szorzata: OEE = rendelkezésre állás x teljesítmény x minőség.

![Előre konfigurált csatlakoztatott gyár megoldás – OEE][cf-img-oee]

1. A hierarchia valamely szintjére vonatkozó OEE megtekintéséhez lépjen az adott nézetre. A nézet OEE mutatója megjelenik a panelen, azon elemekkel együtt, amelyek összesen kiteszik az OEE százalékos értékét.

2. Az OEE mélyebb elemzéséhez a hierarchiaadatok valamelyik szintjén kattintson az OEE, a rendelkezésre állás, a teljesítmény vagy a minőség százalékos értékére. Megjelenik egy Time Series Insights-vizualizációkat tartalmazó helyi panel, amelyek az elmúlt óra, az elmúlt 24 óra és az elmúlt 7 nap adatait mutatják.

    ![Előre konfigurált csatlakoztatott gyár megoldás – TSI-vizualizáció][cf-img-tsi-visualization]

3. A riasztás adatainak további elemzéséhez kattintson a diagramra a riasztások panelén. Ez a művelet megnyitja a Time Series Insights Explorer környezetet.

    ![Előre konfigurált csatlakoztatott gyár megoldás – TSI Explorer][cf-img-tsi-explorer]

## <a name="view-key-performance-indicators"></a>Fő teljesítménymutatók megtekintése

A megoldás két fő teljesítménymutatót biztosít: *egységek száma óránként* és *felhasznált energia (kWh)*.

![Előre konfigurált csatlakoztatott gyár megoldás – KPI][cf-img-kpi]

1. A hierarchia valamely szintjére vonatkozó óránkénti egységszám vagy felhasznált energia megtekintéséhez lépjen az adott nézetre. Az óránkénti egységszám és a felhasznált energia megjelenik a panelen.

2. Az óránkénti egységszám vagy a felhasznált energia mélyebb elemzéséhez a hierarchiaadatok valamelyik szintjén kattintson a legyártott egységek vagy a felhasznált energia mutatójára a **Fő teljesítménymutatók** panelén. Megjelenik egy Time Series Insights-vizualizációkat tartalmazó helyi panel, amelyek az elmúlt óra, az elmúlt 24 óra és az elmúlt 7 nap adatait mutatják.

## <a name="scenario-review"></a>Forgatókönyv áttekintése

Ebben a forgatókönyvben megfigyelte az üzemek OEE és KPI mutatóit az irányítópulton. Ezután a Time Series Insights használatával további információkhoz jutott, hogy az OEE-k és KPI-k telemetriaadatainak részletesebb feltárásával segíthesse elő a rendellenességek észlelését. A riasztási panelen megtekintette az üzemekben jelentkező problémákat, és a rendelkezésre műveletekkel orvosolta a riasztásokat.

## <a name="other-features"></a>Egyéb jellemzők

A következő szakaszokban az előre konfigurált csatlakoztatott gyár megoldás néhány egyéb jellemzőjéről lesz szó, amelyek nem szerepeltek az előző forgatókönyvben.

## <a name="apply-filters"></a>Szűrők alkalmazása

1. Kattintson a **sávnyílra** a gyárhelyek vagy a riasztások panelén elérhető szűrők listájának megjelenítéséhez.

2. Megjelenik a szűrők panelje. 

    ![Előre konfigurált csatlakoztatott gyár megoldás – szűrők][cf-img-alert-filter]

3. Válassza ki a kívánt szűrőt. Igény szerint szabad szöveget is megadhat a szűrőmezőkben.

4. A rendszer működésbe lépteti a szűrőt. A szűrő állapota az irányítópulton is megjeleníthető egy tölcsér ikonon keresztül, amely az üzemek és a riasztások tábláiban is elérhető.

    ![Előre konfigurált csatlakoztatott gyár megoldás – szűrők][cf-img-alert-filter-funnel]

    > [!NOTE]
    > Az aktív szűrők nincsenek hatással a megjelenített OEE és KPI mutatókra, csupán a listák tartalmát szűkítik.

5. A szűrő törléséhez kattintson a tölcsér ikonra, majd a szűrőre a szűrő helyi panelén. Ekkor az **All** (Összes) kifejezés jelenik meg az üzemek és a riasztások tábláiban.

## <a name="browse-an-opc-ua-server"></a>OPC UA-kiszolgáló tallózása

Az előre konfigurált megoldás üzembe helyezésekor automatikusan sor kerül a szimulált OPC UA-kiszolgálók kiosztására, amelyek a megoldás böngészőjében tallózhatóak. Ezek a kiszolgálók *szimulált OPC UA-kiszolgálók*. A szimulált kiszolgálók megkönnyítik az előre konfigurált megoldással történő kísérletezést, anélkül, hogy valódi, fizikai kiszolgálók üzembe helyezésére lenne szükség. Valós OPC UA-kiszolgálóknak a megoldáshoz történő csatlakoztatásáról [az OPC UA-eszköz előre konfigurált csatlakoztatott gyár megoldáshoz történő csatlakoztatásával][lnk-connect-cf] foglalkozó oktatóanyagban olvashat.

1. Kattintson a **gyár ikonra** az irányítópult navigációs sávján.

    ![Előre konfigurált csatlakoztatott gyár megoldás – kiszolgálótallózó][cf-img-server-browser]

2. Válasszon egyet a kiszolgálók közül az előre konfigurált listában. A lista az előre konfigurált megoldásban üzembe helyezett kiszolgálókat mutatja.

    ![Előre konfigurált csatlakoztatott gyár megoldás – kiválasztott kiszolgálók][cf-img-server-choice]

3. Kattintson a **Connect** (Csatlakozás) gombra. Megjelenik egy biztonsági párbeszédablak. A szimuláció esetében biztonságosan kattinthat a **Proceed** (Folytatás) gombra.

4. Kattintson bármely csomópontra a kiszolgálófán a kibontásához. A telemetriaadatokat közzétevő csomópontokat egy pipa jelöli.

    ![Előre konfigurált csatlakoztatott gyár megoldás – kiszolgálófa][cf-img-server-tree]

5. Egy csomópont olvasásához, írásához, közzétételéhez vagy meghívásához kattintson jobb gombbal az adott elemre. Az elérhető műveletek a jogosultságaitól és a csomópont attribútumaitól függnek. Az olvasási lehetőség egy helyi panelt jelenít meg, amelyen az adott csomópont értéke látható. Az írási lehetőség egy olyan helyi panelt jelenít meg, amelyen új értéket adhat meg. A hívási lehetőség egy csomópontot jelenít meg, amelyen megadhatja a hívás paramétereit.

## <a name="publish-a-node"></a>Csomópont közzététele

Ha egy *szimulált OPC UA-kiszolgálóhoz* tallóz, új csomópontokat is közzétehet, ha szeretne. Ezeknek a csomópontoknak a telemetriáját is elemezheti a megoldásban. Ezek a *szimulált OPC UA-kiszolgálók* megkönnyítik az előre konfigurált megoldással történő kísérletezést, anélkül, hogy valódi, fizikai eszközök üzembe helyezésére lenne szükség.

1. Lépjen egy közzétenni kívánt csomópontra az OPC UA-kiszolgáló tallózási fáján.

2. Kattintson a jobb gombbal a csomópontra.

3. Válassza a **Publish** (Közzététel) lehetőséget.

    ![Csatlakoztatott gyár – csomópont közzététele][cf-img-publish-node]

4. Megjelenik egy helyi panel, amely tájékoztatja, hogy a közzététel sikeres volt. A csomópont megjelenik az állomásszinten, mellette egy pipával.

    ![Előre konfigurált csatlakoztatott gyár megoldás – sikeres közzététel][cf-img-publish-success]

## <a name="command-and-control"></a>Parancs és vezérlés

A csatlakoztatott gyár segítségével közvetlenül a felhőből irányíthatja és felügyelheti ipari eszközeit. A szolgáltatással az eszközök által létrehozott riasztásokra is reagálhat, például parancsokat küldhet a felhőből egy eszközre. A rendelkezésre álló parancsokat az OPC UA-kiszolgáló tallózási fájának **StationCommands** csomópontján találja. Ebben a forgatókönyvben megnyit egy nyomáskiegyenlítő szelepet egy müncheni gyártósor összeszerelő állomásán. Az irányítási és felügyeleti funkciók használatához **Rendszergazda** szerepkörrel kell rendelkeznie az előre konfigurált megoldás üzemelő példányán.

1. Lépjen a **StationCommands** csomópontra az OPC UA-kiszolgáló tallózási fáján.

2. Válassza ki a használni kívánt parancsot.

3. Kattintson a jobb gombbal a csomópontra.

4. Válassza a **Call** (Hívás) parancsot.

    ![Előre konfigurált csatlakoztatott gyár megoldás – hívás parancs][cf-img-call-command]

5. Megjelenik egy helyi panel, amely tájékoztatja a meghívni készült metódusról, valamint az esetleges vonatkozó paraméterek részleteiről.

6. Válassza a **Call** (Hívás) parancsot.

    ![Előre konfigurált csatlakoztatott gyár megoldás – hívás helyi panelje][cf-img-call-context]

7. A helyi panel frissül, és tájékoztatja róla, ha a metódus meghívása sikeres volt. A hívás sikerességét ellenőrizheti a hívás eredményeképpen frissített nyomásmérő csomópont értékének leolvasásával.

    ![Előre konfigurált csatlakoztatott gyár megoldás – sikeres hívás][cf-img-call-success]


## <a name="behind-the-scenes"></a>A színfalak mögött

Előre konfigurált megoldás üzembe helyezésekor az üzembehelyezési folyamat több erőforrást hoz létre a kiválasztott Azure-előfizetésben. Ezeket az erőforrásokat az Azure [Portalon][lnk-portal] tekintheti meg. Az üzembe helyezési folyamat létrehoz egy **erőforráscsoportot** az előre konfigurált megoldáshoz kiválasztott néven alapuló névvel:

![Előre konfigurált megoldás az Azure Portalon][img-cf-portal]

Az egyes erőforrások beállításainak megtekintéséhez válassza ki az erőforrást az erőforráscsoport erőforráslistájában.

Megtekintheti az előre konfigurált megoldás forráskódját is. Az előre konfigurált csatlakoztatott gyár megoldás forráskódja az [azure-iot-connected-factory][lnk-cfgithub] GitHub-adattárban található:

Amikor elkészült, törölheti az előre konfigurált megoldást az Azure-előfizetésből az [azureiotsuite.com][lnk-azureiotsuite] webhelyen. Így könnyedén törölheti az előre konfigurált megoldás létrehozásakor megkapott összes erőforrást.

> [!NOTE]
> Ahhoz, hogy biztosan törölje az előre konfigurált megoldáshoz kapcsolódó összes elemet, törölje a megoldást az [azureiotsuite.com][lnk-azureiotsuite] webhelyről. Ne törölje az erőforráscsoportot a portálon.

## <a name="next-steps"></a>Következő lépések

Most, hogy üzembe helyezett egy működő előre konfigurált megoldást, a következő cikkek elolvasásával folytathatja az ismerkedést az IoT Suite használatával:

* [Előre konfigurált csatlakoztatott gyár megoldás – bemutató][lnk-rm-walkthrough]
* [Az eszköz csatlakoztatása az előre konfigurált csatlakoztatott gyár megoldáshoz][lnk-connect-cf]
* [Engedélyek az azureiotsuite.com webhelyen][lnk-permissions]

[img-cf-home]:media/iot-suite-connected-factory-overview/cf-dashboard.png
[img-launch-solution]: media/iot-suite-connected-factory-overview/launch-cf.png
[cf-img-menu]: media/iot-suite-connected-factory-overview/cf-dashboard-menu.png
[cf-img-factories]:media/iot-suite-connected-factory-overview/cf-dashboard-factory.png
[cf-img-map]:media/iot-suite-connected-factory-overview/cf-dashboard-map.png
[cf-img-alerts]:media/iot-suite-connected-factory-overview/cf-dashboard-alerts.png
[cf-img-oee]:media/iot-suite-connected-factory-overview/cf-dashboard-oee.png
[cf-img-kpi]:media/iot-suite-connected-factory-overview/cf-dashboard-kpi.png
[cf-img-tsi-visualization]:media/iot-suite-connected-factory-overview/cf-dashboard-tsi.png
[cf-img-tsi-explorer]:media/iot-suite-connected-factory-overview/tsi-explorer.png
[cf-img-server-browser]: media/iot-suite-connected-factory-overview/cf-dashboard-browser.png
[cf-img-server-choice]: media/iot-suite-connected-factory-overview/cf-select-server.png
[cf-img-server-tree]:media/iot-suite-connected-factory-overview/cf-server-tree.png
[cf-img-publish-node]:media/iot-suite-connected-factory-overview/cf-publish-node1.png
[cf-img-publish-success]:media/iot-suite-connected-factory-overview/cf-publish-success.png
[cf-img-call-command]:media/iot-suite-connected-factory-overview/cf-command-and-control-call.png
[cf-img-call-context]:media/iot-suite-connected-factory-overview/cf-command-and-control-call-button.png
[cf-img-call-success]:media/iot-suite-connected-factory-overview/cf-command-and-control-succeed.png
[img-cf-portal]:media/iot-suite-connected-factory-overview/cf-resource-group.png
[cf-img-alert-filter]:media/iot-suite-connected-factory-overview/cf-filter.png
[cf-img-alert-filter-funnel]:media/iot-suite-connected-factory-overview/cf-filter-funnel.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-portal]: http://portal.azure.com/
[lnk-cfgithub]: https://github.com/Azure/azure-iot-connected-factory
[lnk-rm-walkthrough]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-connect-cf]: iot-suite-connected-factory-gateway-deployment.md
[lnk-permissions]: iot-suite-permissions.md
[lnk-faq]: iot-suite-faq.md
