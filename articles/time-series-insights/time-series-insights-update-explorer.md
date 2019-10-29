---
title: Adatmegjelenítés a Azure Time Series Insights Preview Explorerben | Microsoft Docs
description: Ez a cikk a Azure Time Series Insights Preview Explorer webalkalmazásban elérhető funkciókat és lehetőségeket ismerteti.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/07/2019
ms.custom: seodec18
ms.openlocfilehash: 3f6c03ace13b9b6cb1bda004799502e1f382051d
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72989958"
---
# <a name="visualize-data-in-the-explorer-preview"></a>Adatok vizualizációja az Explorer előzetes verziójában

Ez a dokumentum a Azure Time Series Insights Preview [bemutató webalkalmazás](https://insights.timeseries.azure.com/preview/demo)felhasználói felületét és felhasználói élmény funkcióit és felületét ismerteti. Pontosabban ismerteti az üzemeltetett minta elrendezését, az interfész testreszabási lehetőségeit, valamint a megadott bemutatón keresztül történő navigálást.

## <a name="prerequisites"></a>Előfeltételek

A Azure Time Series Insights Preview Explorer megkezdéséhez a következőket kell tennie:

* Time Series Insights környezet beállítása. Ha többet szeretne megtudni egy példány üzembe helyezéséről, próbálkozzon a [Azure Time Series Insights előzetes](./time-series-insights-update-create-environment.md) verziójának oktatóanyagával.
* Adja meg a fiókhoz létrehozott Time Series Insights-környezet [adathozzáférését](./time-series-insights-data-access.md) . Mások számára is biztosíthat hozzáférést.
* Adjon hozzá egy eseményforrást a Time Series Insights-környezethez, és küldje el az adatforrást a környezetnek:
  * Útmutató az [Event hub-hoz való kapcsolódáshoz](./time-series-insights-how-to-add-an-event-source-eventhub.md).
  * Ismerje meg, [hogyan csatlakozhat egy IoT hubhoz](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="learn-about-the-preview-explorer"></a>Tudnivalók az előnézeti tallózóról

A Azure Time Series Insights Preview Explorer a következő elemekből áll:

[a tallózó nézet![](media/v2-update-explorer/explorer-one.png)](media/v2-update-explorer/explorer-one.png#lightbox)

- <a href="#environment-drop-down-list">Környezeti panel</a>: megjeleníti a Azure Time Series Insights környezeteit.
- <a href="#navigation-menu">Navigációs menü</a>: használja az **elemzés** és a **modell** oldalai közötti váltáshoz.
- <a href="#hierarchy-tree">Hierarchia fája</a>: ezzel a beállítással kiválaszthatja a diagramba felvenni kívánt modellt és adatelemeket.
- <a href="#preview-well">Idősorozatok</a>: a jelenleg kijelölt adatelemeket táblázatos formátumban jeleníti meg színkódolással.
- <a href="#preview-chart">Diagram panel</a>: az aktuális munkadiagramot jeleníti meg.
- <a href="#time-editor-panel">Idősor</a>: ezzel a beállítással módosíthatja a munkaidő-tartományát.
- <a href="#navigation-panel">App Bar</a>: a felhasználói felügyeleti lehetőségeket tartalmazza, például az aktuális bérlőt. A téma-és nyelvi beállítások módosítására használhatja.

## <a name="environment-drop-down-list"></a>Környezet legördülő lista

A környezet legördülő lista megjeleníti az összes Time Series Insights környezetét, amelyhez hozzáfér. A lista utólagos elszámolású környezeteket tartalmaz, például a Time Series Insights előzetes verzióját. A lista S1/S2 környezeteket is tartalmaz, amelyek általánosan elérhetők.

1. Válassza ki a megjelenített környezet melletti legördülő nyilat.

   [a Vezérlőpult![](media/v2-update-explorer/explorer-two.png)](media/v2-update-explorer/explorer-two.png#lightbox)

1. Ezután válassza ki a kívánt környezetet.

## <a name="navigation-menu"></a>Navigációs menü

  [a navigációs menü![](media/v2-update-explorer/explorer-three.png)](media/v2-update-explorer/explorer-three.png#lightbox)

A navigációs menüben választhat két nézet közül:

* **Elemzés**: használja a táblázatos és a részletes elemzések elvégzésére a modellezett vagy nem modellezett idősorozat-adatokon.
* **Modell**: ezzel a paranccsal leküldheti az új Time Series Insights előnézeti típusokat, hierarchiákat és példányokat a Time Series Insights-modellbe.

## <a name="hierarchy-tree"></a>Hierarchia fája

A hierarchia fastruktúrája megjeleníti a kiválasztott adatelemeket, amelyek modelleket, adott eszközöket és érzékelőket tartalmaznak az eszközökön.

### <a name="model-search-panel"></a>Modell keresési panelje

A modell keresési paneljén könnyedén keresheti meg és navigálhatja az idősorozat-modell hierarchiáját, hogy megkeresse a diagramon megjeleníteni kívánt idősorozat-példányokat. A példányok kiválasztása után a rendszer hozzáadja az aktuális diagramhoz és az adategységhez is.

  [a modell keresési paneljének![](media/v2-update-explorer/explorer-four.png)](media/v2-update-explorer/explorer-four.png#lightbox)

### <a name="model-authoring"></a>Modell készítése

A Azure Time Series Insights előzetes verziója támogatja a teljes létrehozási, olvasási, frissítési és törlési (szifilisz) műveleteket az idősorozat-modellben.

* **Idősorozat-modell típusa**: Time Series Insights típusokkal adhat meg változókat vagy képleteket a számítások végrehajtásához. Egy adott Time Series Insights-példánnyal vannak társítva. Egy típus egy vagy több változóval is rendelkezhet.
* **Idősorozat-modell hierarchiája**: a hierarchiák az adataik szisztematikus szervezetei. A hierarchiák ábrázolják a Time Series Insights adataiban lévő különböző entitások közötti kapcsolatokat.
* **Idősorozat-modell példány**: a példányok maguk az idősorozatok. A legtöbb esetben ezek a **DeviceID** vagy a **assetid**, amely az eszköz egyedi azonosítója a környezetben.

Az idősorozat modelljével kapcsolatos további információkért lásd: [Times Series-modellek](./time-series-insights-update-tsm.md).

## <a name="preview-well"></a>Előzetes verzió

A jól megjeleníti a példányok mezőit és a kijelölt Time Series Insights példányokhoz tartozó egyéb metaadatokat. A jobb oldalon található jelölőnégyzetek bejelölésével elrejtheti vagy megjelenítheti az aktuális diagram adott példányait. A jelenlegi adatokból is eltávolíthatja a konkrét adatelemeket, ha kiválasztja a vörös **Törlés** (kuka) vezérlőt az elem bal oldalán.

  [![az előnézet is](media/v2-update-explorer/explorer-five.png)](media/v2-update-explorer/explorer-five.png#lightbox)

**Az elemzési diagram** elrendezésének újrakonfigurálásához válassza a jobb felső sarokban található ellipszisek ikont:

  [![telemetria elrendezési beállításai](media/v2-update-explorer/explorer-six.png)](media/v2-update-explorer/explorer-six.png#lightbox)

> [!NOTE]
> Ha a következő üzenet jelenik meg, a példány nem rendelkezik a kiválasztott időtartományon belül semmilyen adattal. A probléma megoldásához növelje az időtartamot, vagy győződjön meg arról, hogy a példány az adattovábbítást hajtja végre.
>
> ![Nincs értesítés](media/v2-update-explorer/explorer-seven.png)

## <a name="preview-chart"></a>Előnézeti diagram

A diagramon Time Series Insights-példányokat is megjeleníthet vonalakként. A diagram méretének növeléséhez kiválaszthatja a környezeti panelt, az adatmodellt és az időtartomány-alapú vezérlőpultot.

  [![az előnézeti diagram áttekintése](media/v2-update-explorer/explorer-eight.png)](media/v2-update-explorer/explorer-eight.png#lightbox)

- **Kiválasztott dátumtartomány**: meghatározza, hogy mely adatelemek érhetők el a vizualizációhoz.

- **Belső dátumtartomány csúszka eszköz**: a két végpont vezérlőelem használatával húzza őket a kívánt időtartományra.

- **Időtartomány összeomlásának szabályozása**: Összecsukja és kibővíti az időtartomány panel-szerkesztőt.

- **Y tengely formátumának vezérlője**: az elérhető y tengelyes nézet beállításai:

    * `Default`: minden sorban külön y tengely szerepel.
    * `Stacked`: a használatával több sort halmozhat fel ugyanazon az y tengelyen, és az y tengely adatváltozása a kiválasztott vonal alapján változik.
    * `Shared`: az y tengely összes adathalmaza együtt jelenik meg.

- **Aktuális adatelem**: a jelenleg kijelölt adatelem és a hozzá tartozó részletek.

Egy adott adatszelet további részletezéséhez kattintson a bal gombbal egy adatpontra az aktuális gráfon, majd húzza a kijelölt területét a választott végpontra. Kattintson a jobb gombbal a szürke elemre, és válassza a **Nagyítás**lehetőséget, ahogy az a következő képen látható:

  [![előnézeti diagram nagyítása](media/v2-update-explorer/explorer-nine.png)](media/v2-update-explorer/explorer-nine.png#lightbox)

A **nagyítási** művelet elvégzése után megjelenik a kiválasztott adathalmaz. Válassza ki az y tengely formátuma vezérlőelemet, hogy a Time Series Insights-adatainak három y tengelyes ábrázolásával legyen átirányítva.

  [![előnézeti diagram y tengelye](media/v2-update-explorer/explorer-ten.png)](media/v2-update-explorer/explorer-ten.png#lightbox)

Itt láthatja a megosztott Y tengelyek példáját:

  [![előnézet megosztott Y tengelye](media/v2-update-explorer/explorer-eleven.png)](media/v2-update-explorer/explorer-eleven.png#lightbox)

## <a name="time-editor-panel"></a>Időszerkesztő panel

Time Series Insights előzetes verzió használatakor először ki kell választania egy időtartományt. A kiválasztott időtartomány szabályozza a Time Series Insights Preview widgetekkel való manipulációhoz rendelkezésre álló adatkészletet. A következő webes vezérlők Time Series Insights előzetes verzióban érhetők el a munkaidő-tartomány kiválasztásához:

  [![idő kiválasztása panel](media/v2-update-explorer/explorer-twelve.png)](media/v2-update-explorer/explorer-twelve.png#lightbox)

1. **Belső dátumtartomány csúszka eszköz**: a két végpont vezérlőelem használatával húzza őket a kívánt időtartományra. Ezt a belső dátumtartományt a külső dátumtartomány csúszka vezérlőeleme korlátozza.

1. **Dátumtartomány gombjának növelése és csökkentése**: növelje vagy csökkentse az időtartományt úgy, hogy kijelöli a kívánt időszakra vonatkozó gombot.

1. Időtartomány **összeomlásának vezérlése**: Ez a webes vezérlőelem lehetővé teszi az összes vezérlő elrejtését, kivéve a belső dátumtartomány csúszka eszközét.

1. **Külső dátumtartomány csúszka vezérlő**: a végpont vezérlőelemekkel kiválaszthatja a külső dátumtartományt, amely a belső dátumtartomány vezérlőelemhez lesz elérhető.

1. **Gyors időpontok dátum-tartomány legördülő listája**: ezzel a beállítással gyorsan válthat az előre beállított időtartamok között, például az elmúlt **30 percben**, az **utolsó 12 órában**vagy egy **Egyéni tartományon**. Az érték módosítása az intervallum-méret csúszka eszközben tárgyalt elérhető intervallum-tartományokat is megváltoztatja.

1. **Intervallum-méret csúszka eszköz**: ezzel a beállítással az adott időkereten belül és kívül is nagyíthatja az intervallumokat. Ez a művelet a nagy szeletek közötti mozgás pontosabb szabályozását teszi lehetővé. A simított trendeket a szeletek számára a kis méretűre (ezredmásodpercben) jeleníti meg. A segítségével részletes, nagy felbontású adatmennyiségeket tekinthet meg az adatairól. A csúszka alapértelmezett kiindulási pontja a kiválasztott adatok legoptimálisabb nézete, amely egyensúlyt teremt a felbontás, a lekérdezés sebessége és a részletesség szempontjából.

1. Dátumtartomány **és a között a webes vezérlő**: ezzel a webvezérlővel egyszerűen kiválaszthatja a kívánt dátum-és időtartományokat. A vezérlőelemet a különböző időzónák közötti váltásra is használhatja. Miután elvégezte a módosítások alkalmazását az aktuális munkaterületre, válassza a **Mentés**lehetőséget.

   [![a kiválasztási panelen](media/v2-update-explorer/explorer-thirteen.png)](media/v2-update-explorer/explorer-thirteen.png#lightbox)

## <a name="navigation-panel"></a>Navigációs panel

Megjelenik a Time Series Insights előnézet navigációs panel a Time Series Insights alkalmazás tetején. A következő funkciókat biztosítja:

### <a name="current-session-share-link-control"></a>Munkamenet jelenlegi megosztási hivatkozásának vezérlője

  [![megosztás ikon](media/v2-update-explorer/explorer-fifteen.png)](media/v2-update-explorer/explorer-fifteen.png#lightbox)

Az új **megosztás** ikon kiválasztásával megoszthatja az URL-hivatkozást a csapatával.

  [a példány URL-címének![megosztása](media/v2-update-explorer/url-share.png)](media/v2-update-explorer/url-share.png#lightbox)

### <a name="tenant-section"></a>Bérlő szakasz

  [![bérlő kiválasztása](media/v2-update-explorer/explorer-sixteen.png)](media/v2-update-explorer/explorer-sixteen.png#lightbox)

* Megjeleníti az aktuális Time Series Insights bejelentkezési fiókjának adatait.
* Használja az elérhető Time Series Insights témák közötti váltásra.
* Ezzel a szolgáltatással megtekintheti az előzetes [demó webalkalmazást](https://insights.timeseries.azure.com/preview/demo).

### <a name="theme-selection"></a>Téma kiválasztása

Új téma kiválasztásához válassza a jobb felső sarokban található profil ikont. Ezután válassza a **téma módosítása**lehetőséget.

  [![téma kiválasztása](media/v2-update-explorer/theme-selection.png)](media/v2-update-explorer/theme-selection.png#lightbox)

> [!TIP]
> A nyelv kiválasztása a profil ikonjának kiválasztásával is elérhető.

Azure Time Series Insights előzetes verzió két témát támogat:

* **Világos téma**: az alapértelmezett téma jelenik meg a dokumentumban.
* **Sötét téma**: a kezelőt az itt látható módon jeleníti meg:

  [![kiválasztott sötét téma](media/v2-update-explorer/explorer-seventeen.png)](media/v2-update-explorer/explorer-seventeen.png#lightbox)

## <a name="s1s2-environment-controls"></a>S1/S2 környezeti vezérlők

### <a name="preview-terms-panel"></a>Előnézet feltételei panel

Ez a szakasz csak olyan meglévő S1/S2 környezetekre vonatkozik, amelyek a frissített felhasználói felületen használják a Explorert. Előfordulhat, hogy az általánosan elérhető termék és az előzetes verzió együttes használatát szeretné használni. Felvettünk néhány funkciót a meglévő felhasználói felületről a frissített Explorerbe, de az S1/S2 környezet teljes felhasználói felületi élményét a meglévő Time Series Insights Explorerben is elérheti. 

A hierarchia helyett a Time Series Insights feltételek panel jelenik meg, ahol lekérdezéseket határozhat meg a környezetben. Használatával szűrheti az adatait egy predikátum alapján.

  [![a lekérdezési panel](media/v2-update-explorer/explorer-eighteen.png)](media/v2-update-explorer/explorer-eighteen.png#lightbox)

A Time Series Insights előzetes verziójának használati feltételeinek szerkesztő panelje a következő paramétereket veszi figyelembe:

**Hol**: a WHERE záradék használatával gyorsan szűrheti az eseményeket az alábbi táblázatban felsorolt operandusok használatával. Ha egy operandus kiválasztásával végez keresést, a rendszer automatikusan frissíti a predikátumot a keresés alapján. A támogatott operandusok típusai a következők:

| Művelet | Támogatott típusok   | Megjegyzések |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | Karakterlánc, bool, Double, DateTime, TimeSpan, NULL |
| `IN` | Karakterlánc, bool, Double, DateTime, TimeSpan, NULL | Az összes operandusnak azonos típusúnak vagy NULL konstansnak kell lennie. |
| `HAS` | Sztring | A jobb oldalon csak konstans karakterlánc-literálok engedélyezettek. Üres karakterlánc és NULL érték nem engedélyezett. |

A támogatott lekérdezési műveletekkel és adattípusokkal kapcsolatos további tudnivalókért lásd a [Time Series kifejezés (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx)című témakört.

### <a name="examples-of-where-clauses"></a>Példák a WHERE záradékokra

  [Példák a![where záradékra](media/v2-update-explorer/explorer-nineteen.png)](media/v2-update-explorer/explorer-nineteen.png#lightbox)

**Mérték**: egy legördülő lista, amely az aktuális diagram elemeiként használható numerikus oszlopokat (**Double**) jeleníti meg.

**Felosztás**: Ez a legördülő lista megjeleníti a modellben található összes rendelkezésre álló kategorikus oszlopot (karakterláncot), amely alapján csoportosíthatja adatait. Akár öt kifejezést is hozzáadhat ugyanazon az x tengelyen. Adja meg a kívánt paramétereket, majd a **Hozzáadás** gombra kattintva vegyen fel egy friss kifejezést.

  [![lekérdezés és szűrt nézet](media/v2-update-explorer/explorer-twenty.png)](media/v2-update-explorer/explorer-twenty.png#lightbox)

A diagram panel elemeinek megjelenítéséhez és elrejtéséhez válassza a látható ikont, ahogy az alábbi képen is látható. A lekérdezések teljes eltávolításához válassza a piros **X**elemet.

  [![lekérdezés és szűrt nézet](media/v2-update-explorer/explorer-twenty-one.png)](media/v2-update-explorer/explorer-twenty-one.png#lightbox)

## <a name="next-steps"></a>Következő lépések

- Tudnivalók a [tárolásról és a bejövő](./time-series-insights-update-storage-ingress.md) forgalomról a Azure Time Series Insights előzetes verziójában.
- Olvassa el az [adatmodellezés](./time-series-insights-update-tsm.md)Time Series Insights előnézeti dokumentumát.
- Ismerje meg [, hogyan diagnosztizálhatja és elháríthatja](./time-series-insights-update-how-to-troubleshoot.md) a Time Series Insights-példányát.
