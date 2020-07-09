---
title: Adatmegjelenítés az előnézeti Explorerben – Azure Time Series Insights | Microsoft Docs
description: Ismerkedjen meg a Azure Time Series Insights Preview Explorerben elérhető funkciókkal és lehetőségekkel.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: seodec18
ms.openlocfilehash: 06f235b7f1c20681beee1f5be4c18de60d53a690
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86037307"
---
# <a name="azure-time-series-insights-preview-explorer"></a>Azure Time Series Insights Preview Explorer

Ez a cikk a Azure Time Series Insights Preview [bemutató webalkalmazásban](https://insights.timeseries.azure.com/preview/demo)elérhető különböző funkciókat és lehetőségeket ismerteti.

## <a name="prerequisites"></a>Előfeltételek

A Azure Time Series Insights Preview Explorer megkezdéséhez a következőket kell tennie:

* Time Series Insights-környezet kiépítve. További információ a példányok kiépítési módjáról: [Azure Time Series Insights előzetes](./time-series-insights-update-create-environment.md) verziójának oktatóanyaga.
* Adja meg a fiókhoz létrehozott Time Series Insights-környezet [adathozzáférését](./time-series-insights-data-access.md) . Mások számára is biztosíthat hozzáférést.
* Adjon hozzá egy eseményforrást a Time Series Insights-környezethez, és küldje el az adatforrást a környezetnek:
  * További információ az [Event hub-hoz való kapcsolódásról](./time-series-insights-how-to-add-an-event-source-eventhub.md) 
  * További információ az [IoT hub-hoz való kapcsolódásról](./time-series-insights-how-to-add-an-event-source-iothub.md)

## <a name="explore-the-time-series-insights-preview-explorer"></a>Ismerkedés a Time Series Insights Preview Explorerrel

A Azure Time Series Insights Preview Explorer a következő hét elemből áll:

[![A Time Series Insights Preview Explorer áttekintése](media/v2-update-explorer/preview-explorer-overview.png)](media/v2-update-explorer/preview-explorer-overview.png#lightbox)

1. [Környezeti panel](#1-environment-panel): megjeleníti az összes Azure Time Series Insights környezetét.
1. [Navigációs sáv](#2-navigation-bar): lehetővé teszi az **elemzés** és a **modell** oldalai közötti váltást.
1. [Hierarchia és keresési panel](#3-hierarchy-tree-and-search-panel): lehetővé teszi, hogy kiválassza és megkeresse a diagramba felvenni kívánt adatelemeket.
1. [Idősorozatok](#4-time-series-well): a jelenleg kijelölt adatelemek megjelenítése.
1. [Diagram panel](#5-chart-panel): az aktuális munkadiagramot jeleníti meg.
1. [Idősor](#6-time-editor-panel): lehetővé teszi a munkaidő-tartomány módosítását.
1. [Alkalmazás-sáv](#7-app-bar): a felhasználói felügyeleti beállításokat (például az aktuális bérlőt) tartalmazza, és lehetővé teszi a módosítását és a nyelvi beállításokat.


## <a name="1-environment-panel"></a>1. környezeti panel

A környezet panel megjeleníti az összes Time Series Insights környezetét, amelyhez hozzáfér. A lista az utólagos elszámolású (előzetes verzió) környezeteket, valamint az S1/S2 környezeteket (általánosan elérhető) tartalmazza. Egyszerűen válassza ki a használni kívánt Time Series Insights-környezetet, amelyet azonnal el szeretne végezni.

1. Válassza ki a megjelenített környezet melletti legördülő nyilat.

   [![Környezeti panel](media/v2-update-explorer/environment-panel.png)](media/v2-update-explorer/environment-panel.png#lightbox)

1. Ezután válassza ki a kívánt környezetet.

## <a name="2-navigation-bar"></a>2. navigációs sáv

  [![A navigációs sáv](media/v2-update-explorer/tsi-preview-navigation-bar.png)](media/v2-update-explorer/tsi-preview-navigation-bar.png#lightbox)

Két nézet között a navigációs sáv használatával választhat:

* **Elemzés**: használja a táblázatos és a részletes elemzések elvégzésére a modellezett vagy nem modellezett idősorozat-adatokon.
* **Modell**: ezzel a paranccsal leküldheti az új Time Series Insights előnézeti típusokat, hierarchiákat és példányokat a Time Series Insights-modellbe.

### <a name="model-authoring"></a>Modell készítése

A Azure Time Series Insights előzetes verziója támogatja a teljes létrehozási, olvasási, frissítési és törlési (szifilisz) műveleteket az idősorozat-modellben.

[![A modell keresési panelje](media/v2-update-explorer/model-search-panel.png)](media/v2-update-explorer/model-search-panel.png#lightbox)

* **Idősorozat-modell típusa**: Time Series Insights típusokkal adhat meg változókat vagy képleteket a számítások végrehajtásához. Egy adott Time Series Insights-példánnyal vannak társítva. Egy típus egy vagy több változóval is rendelkezhet.
* **Idősorozat-modell hierarchiája**: a hierarchiák az adataik szisztematikus szervezetei. A hierarchiák ábrázolják a Time Series Insights adataiban lévő különböző entitások közötti kapcsolatokat.
* **Idősorozat-modell példány**: a példányok maguk az idősorozatok. A legtöbb esetben ezek a **DeviceID** vagy a **assetid**, amely az eszköz egyedi azonosítója a környezetben.

Az idősorozat modelljével kapcsolatos további információkért olvassa el a [Times Series-modelleket](./concepts-model-overview.md).

## <a name="3-hierarchy-tree-and-search-panel"></a>3. hierarchia faszerkezete és keresési panelje

A hierarchia faszerkezetének és a keresési paneljének segítségével könnyedén keresheti meg és navigálhatja az [Idősorozat-modell](./concepts-model-overview.md) hierarchiáját, hogy megkeresse a diagramon megjeleníteni kívánt idősorozat-példányokat. Ha kiválasztja a példányokat, azok nem csak az aktuális diagramhoz lesznek hozzáadva, hanem az adatterülethez is hozzáadódnak. 

[![Hierarchia faszerkezete és keresési panelje](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png)](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png#lightbox)

A keresési eredmények ablaktábla azt is lehetővé teszi, hogy megtekintse az eredményeket egy hierarchia nézetben vagy egy listanézet segítségével, így könnyen megtalálhatja a megjeleníteni kívánt példányokat.
 
## <a name="4-time-series-well"></a>4. idősorozat – Well

A jól megjeleníti a példányok mezőit és a kijelölt Time Series Insights példányokhoz tartozó egyéb metaadatokat. A jobb oldalon található jelölőnégyzetek bejelölésével elrejtheti vagy megjelenítheti az aktuális diagram adott példányait. 

  [![Az előnézet is](media/v2-update-explorer/tsi-preview-ui-explorer-well.png)](media/v2-update-explorer/tsi-preview-ui-explorer-well.png#lightbox)

A jelenlegi adatokból is eltávolíthat bizonyos adatelemeket, ha kiválasztja a vörös **Törlés** (kuka) vezérlőt az elem bal oldalán. A jól szabályozza azt is, hogy az egyes elemek hogyan jelenjenek meg a diagramon. Dönthet úgy, hogy felveszi a min/max árnyékot, az adatpontokat, átváltja az elemet, és láthatóvá teszi a példányt egy lépcsőzetes módon. 

Emellett a feltárások vezérlőelem lehetővé teszi, hogy könnyedén hozza létre az időeltolódásokat és a scatter-ábrákat.  

  [![Kiváló elrendezési beállítások](media/v2-update-explorer/well-layout-options.png)](media/v2-update-explorer/well-layout-options.png#lightbox)

> [!NOTE]
> Ha a következő üzenet jelenik meg, a példány nem rendelkezik a kiválasztott időtartományon belül semmilyen adattal. A probléma megoldásához növelje az időtartamot, vagy győződjön meg arról, hogy a példány az adattovábbítást hajtja végre.
>
> ![Nincs értesítés](media/v2-update-explorer/tsi-preview-no-data-warning.png)

## <a name="5-chart-panel"></a>5. diagram panel

A diagram lehetővé teszi, hogy a Time Series-példányokat vonalakként jelenítse meg. A diagram méretének növeléséhez a webes vezérlőkre kattintva összecsukhatja a környezeti panelt, az adatmodellt és az időtartományt. 

  [![Előnézet diagram – áttekintés](media/v2-update-explorer/tsi-preview-chart-overview.png)](media/v2-update-explorer/tsi-preview-chart-overview.png#lightbox)

1. **Diagram típusa**: meghatározza, hogy mely adatelemek érhetők el a vizualizációhoz.

1. **Intervallum mérete**: az intervallum mérete csúszka eszköz lehetővé teszi az intervallumok közötti nagyítást és kicsinyítést az adott időtartományon belül. Ez precízebb vezérlést biztosít a nagy szeletek közötti mozgáshoz, amely az ezredmásodpercben lévő, kis méretű szeleteket mutatja, és lehetővé teszi a részletes, nagy felbontású adatmennyiségek áttekintését. A csúszka alapértelmezett kiindulási pontja a kiválasztott adatok legoptimálisabb nézete lesz. az elosztás feloldása, a lekérdezés sebessége és a részletesség.

1. **Nagyítás és pásztázás**: válassza ezt a vezérlőt a diagram nagyításához és pásztázásához.

1. **Y tengely vezérlőelem**: az elérhető y tengelyes nézet beállításai:

    * `Stacked`: Minden sorban külön Y tengely szerepel.
    * `Overlap`: Több, ugyanazon Y tengelyen lévő sor halmozásához használja az Y tengely adatváltozását a kiválasztott vonal alapján.
    * `Shared`: Az Y tengely összes adathalmaza együtt jelenik meg.

1. **Jelölő elem**: az aktuálisan kijelölt adatelem és a hozzájuk tartozó részletek.

További részletezést adhat egy adott adatszelethez, ha az egér **bal oldali gombjával kattint** egy adatpontra, és lenyomja az egeret, majd a kijelölt területre húzza a kívánt végpontot. **Kattintson a jobb gombbal** a kék, a kijelölt területen, majd válassza a **Nagyítás** lehetőséget az alább látható módon. A kiválasztott TimeSpan is megtekintheti és letöltheti a telemetria eseményeket.

  [![Előnézet diagram nagyítása](media/v2-update-explorer/preview-chart-zoom.png)](media/v2-update-explorer/preview-chart-zoom.png#lightbox)

A **nagyítási** művelet elvégzése után a kiválasztott adatkészlet jelenik meg. Válassza ki a formátum vezérlőelemet, hogy a Time Series Insights-adatainak három y tengelyes ábrázolását adja át.

  [![Előnézeti diagram y tengelye](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png)](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png#lightbox)

Íme egy példa az **átfedésben lévő diagramra** :

  [![Átfedésben lévő diagram beállítása](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png)](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png#lightbox)

A **További műveletek** gomb kibontja a **Letöltés CSV-ként**való megjelenítését, a **Power BIhoz való kapcsolódást**, a **diagram adatai táblázatként**való megjelenítését és a **nyers események beállításainak megismerését** .

  [![További műveletek lehetőség](media/v2-update-explorer/more-actions-icon.png)](media/v2-update-explorer/more-actions-icon.png#lightbox)

További információ a [Time Series Insights natív Power bi-összekötőhöz](concepts-power-bi.md) **való csatlakozás Power bi** lehetőségről.

## <a name="6-time-editor-panel"></a>6. időszerkesztő panel

Time Series Insights használatakor először ki kell választania egy időtartományt. A kiválasztott időtartomány szabályozza a Time Series Insights Update widgetekkel való manipulációhoz rendelkezésre álló adatkészletet.

  [![Időkiválasztás panel](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png)](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png#lightbox)

> [!TIP]
> Az idősor egy része sárga vagy narancssárga színnel van kiemelve, hogy jelezze a meleg tárban elérhető adatmennyiséget.

A következő webes vezérlők érhetők el a Time Series Insights frissítésében a munkaidő-tartomány kiválasztásához. 

  [![Kutatási és ellenőrzési lehetőségek](media/v2-update-explorer/exploration-well-control.png)](media/v2-update-explorer/exploration-well-control.png#lightbox)

1. **Belső dátumtartomány csúszka vezérlőelem**: használja a két végpont vezérlőelemet a kívánt időtartományra húzva. Ezt a belső dátumtartományt a külső dátumtartomány csúszka vezérlőeleme korlátozza.

1. **Dátumtartomány gombjának növelése és csökkentése**: növelje vagy csökkentse az időtartományt úgy, hogy kijelöli a kívánt időszakra vonatkozó gombot.

1. Időtartomány **összeomlásának vezérlése**: Ez a webes vezérlőelem lehetővé teszi az összes vezérlő elrejtését, kivéve a belső dátumtartomány csúszka eszközét.

1. **Külső dátumtartomány csúszka vezérlő**: a végpont vezérlőelemekkel kiválaszthatja a külső dátumtartományt, amely a belső dátumtartomány vezérlőelemhez lesz elérhető.

1. **Időtartomány csúszka vezérlő**: Ezzel gyorsan válthat az előre beállított időtartományok kiválasztásai között, például az elmúlt **30 percben**, az **utolsó 12 órában**vagy egy **Egyéni tartományon**. Az érték módosítása az intervallum-méret csúszka eszközben tárgyalt elérhető intervallum-tartományokat is megváltoztatja.

   [![A és a kiválasztása a kiválasztási panelen](media/v2-update-explorer/to-and-from-element.png)](media/v2-update-explorer/to-and-from-element.png#lightbox)

## <a name="7-app-bar"></a>7. app-sáv

Megjelenik a Time Series Insights előnézet navigációs panel a Time Series Insights alkalmazás tetején. A következő funkciókat biztosítja:

### <a name="current-session-share-link-control"></a>Munkamenet jelenlegi megosztási hivatkozásának vezérlője

  [![Megosztás ikon](media/v2-update-explorer/tsi-preview-explorer-share-icon.png)](media/v2-update-explorer/tsi-preview-explorer-share-icon.png#lightbox)

Az új **megosztás** ikon kiválasztásával megoszthatja az URL-hivatkozást a csapatával.

  [![Példány URL-címének megosztása](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png)](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png#lightbox)

### <a name="tenant-section"></a>Bérlő szakasz

  [![Bérlő kiválasztása](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png)](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png#lightbox)

* Megjeleníti az aktuális Time Series Insights bejelentkezési fiókjának adatait.
* Használja az elérhető Time Series Insights témák közötti váltásra.
* Ezzel a szolgáltatással megtekintheti az előzetes [demó webalkalmazást](https://insights.timeseries.azure.com/preview/demo).

### <a name="theme-selection"></a>Téma kiválasztása

Új téma kiválasztásához válassza a jobb felső sarokban található profil ikont. Ezután válassza a **téma módosítása**lehetőséget.

  [![Téma kiválasztása](media/v2-update-explorer/tsi-preview-theme-selection.png)](media/v2-update-explorer/tsi-preview-theme-selection.png#lightbox)

> [!TIP]
> A nyelv kiválasztása a profil ikonjának kiválasztásával is elérhető.

Azure Time Series Insights előzetes verzió két témát támogat:

* **Világos téma**: az alapértelmezett téma jelenik meg a dokumentumban.
* **Sötét téma**: a kezelőt az itt látható módon jeleníti meg:

  [![Kiválasztott sötét téma](media/v2-update-explorer/tsi-preview-dark-theme-selected.png)](media/v2-update-explorer/tsi-preview-dark-theme-selected.png#lightbox)

## <a name="s1s2-environment-controls"></a>S1/S2 környezeti vezérlők

### <a name="preview-terms-panel"></a>Előnézet feltételei panel

Ez a szakasz csak olyan meglévő S1/S2 környezetekre vonatkozik, amelyek a frissített felhasználói felületen használják a Explorert. Előfordulhat, hogy az általánosan elérhető termék és az előzetes verzió együttes használatát szeretné használni. Felvettünk néhány funkciót a meglévő felhasználói felületről a frissített Explorerbe, de az S1/S2 környezet teljes felhasználói felületi élményét a meglévő Time Series Insights Explorerben is elérheti. 

A hierarchia helyett megjelenik a Time Series Insights feltételek panel. A feltételek panel lehetővé teszi lekérdezések definiálását a környezetben. Az adathalmaz alapján is szűrheti az adatszűrést.

  [![A lekérdezési panel helye](media/v2-update-explorer/s1-s2-preview-query.png)](media/v2-update-explorer/s1-s2-preview-query.png#lightbox)

A Time Series Insights előzetes verziójának használati feltételeinek szerkesztő panelje a következő paramétereket veszi figyelembe:

**Hol**: a WHERE záradék használatával gyorsan szűrheti az eseményeket az alábbi táblázatban felsorolt operandusok használatával. Ha egy operandus kiválasztásával végez keresést, a rendszer automatikusan frissíti a predikátumot a keresés alapján. A támogatott operandusok típusai a következők:

| Művelet | Támogatott típusok   | Jegyzetek |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | Karakterlánc, bool, Double, DateTime, TimeSpan, NULL |
| `IN` | Karakterlánc, bool, Double, DateTime, TimeSpan, NULL | Az összes operandusnak azonos típusúnak vagy NULL konstansnak kell lennie. |
| `HAS` | Sztring | A jobb oldalon csak konstans karakterlánc-literálok engedélyezettek. Üres karakterlánc és NULL érték nem engedélyezett. |

Ha többet szeretne megtudni a támogatott lekérdezési műveletekről és az adattípusokról, olvassa el a [Time Series kifejezés (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)című témakört.

### <a name="examples-of-where-clauses"></a>Példák a WHERE záradékokra

  [![WHERE záradék – példák](media/v2-update-explorer/tsi-preview-example-queries.png)](media/v2-update-explorer/tsi-preview-example-queries.png#lightbox)

**Mérték**: egy legördülő lista, amely az aktuális diagram elemeiként használható numerikus oszlopokat (**Double**) jeleníti meg.

**Felosztás**: Ez a legördülő lista megjeleníti a modellben található összes rendelkezésre álló kategorikus oszlopot (karakterláncot), amely alapján csoportosíthatja adatait. Akár öt kifejezést is hozzáadhat ugyanazon az x tengelyen. Adja meg a kívánt paramétereket, majd a **Hozzáadás** gombra kattintva vegyen fel egy friss kifejezést.

  [![Lekérdezés és szűrt nézet](media/v2-update-explorer/s1-s2-preview-filtered-view.png)](media/v2-update-explorer/s1-s2-preview-filtered-view.png#lightbox)

A diagram panel elemeinek megjelenítéséhez és elrejtéséhez válassza a látható ikont, ahogy az alábbi képen is látható. A lekérdezések teljes eltávolításához válassza a piros **X**elemet.

  [![Lekérdezett és szűrt lehetőség megszakítása](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png)](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png#lightbox)

## <a name="next-steps"></a>További lépések

- Tudnivalók az [adatfeldolgozásról](./concepts-ingestion-overview.md) a környezetbe.

- Tekintse meg a cikket a [Storage](concepts-storage.md) -ban

- Olvassa el az [adatmodellezés](./concepts-model-overview.md)Time Series Insights előnézeti dokumentumát.

- Ismerje meg [, hogyan diagnosztizálhatja és elháríthatja](./time-series-insights-update-how-to-troubleshoot.md) a Time Series Insights-példányát.
