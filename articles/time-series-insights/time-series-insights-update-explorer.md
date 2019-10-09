---
title: Adatok megjelenítése az az Azure Time Series Insights – előzetes explorer |} A Microsoft Docs
description: Ez a cikk ismerteti a szolgáltatások és az Azure Time Series Insights – előzetes explorer web app alkalmazásban elérhető beállítások.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/07/2019
ms.custom: seodec18
ms.openlocfilehash: a0d8f7cdace8d3e810dfcda6e54df5823840307c
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72034326"
---
# <a name="visualize-data-in-the-explorer-preview"></a>Adatok megjelenítése az a explorer előzetes verzió

Ez a dokumentum a Azure Time Series Insights Preview [bemutató webalkalmazás](https://insights.timeseries.azure.com/preview/demo)felhasználói felületét és felhasználói élmény funkcióit és felületét ismerteti. Pontosabban ismerteti az üzemeltetett minta elrendezését, az interfész testreszabási lehetőségeit, valamint a megadott bemutatón keresztül történő navigálást.

## <a name="prerequisites"></a>Előfeltételek

A Azure Time Series Insights Preview Explorer megkezdéséhez a következőket kell tennie:

* Állítsa be a Time Series Insights környezettel rendelkezik. Ha többet szeretne megtudni egy példány üzembe helyezéséről, próbálkozzon a [Azure Time Series Insights előzetes](./time-series-insights-update-create-environment.md) verziójának oktatóanyagával.
* Adja meg a fiókhoz létrehozott Time Series Insights-környezet [adathozzáférését](./time-series-insights-data-access.md) . Parancs saját maga számára hozzáférést is megadhat.
* Adjon hozzá egy eseményforrást a Time Series Insights-környezethez, és küldje el az adatforrást a környezetnek:
  * Útmutató az [Event hub-hoz való kapcsolódáshoz](./time-series-insights-how-to-add-an-event-source-eventhub.md).
  * Ismerje meg, [hogyan csatlakozhat egy IoT hubhoz](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="learn-about-the-preview-explorer"></a>Tudnivalók az előnézeti tallózóról

Az Azure Time Series Insights – előzetes Explorert a következő elemekből áll:

[@no__t – 1a Explorer nézet](media/v2-update-explorer/explorer-one.png)](media/v2-update-explorer/explorer-one.png#lightbox)

- <a href="#environment-drop-down-list">Környezeti panel</a>: Megjeleníti Azure Time Series Insights környezeteit.
- <a href="#navigation-menu">Navigációs menü</a>: Használja az **elemzés** és a **modell** oldalai közötti váltásra.
- <a href="#hierarchy-tree">Hierarchia fája</a>: Használatával kiválaszthatja a diagramba felvenni kívánt modellt és adatelemeket.
- <a href="#preview-well">Idősorozatok</a>: Megjeleníti a jelenleg kijelölt adatelemeket táblázatos formátumban, színkódolással.
- <a href="#preview-chart">Diagram panel</a>: Megjeleníti az aktuális munkadiagramot.
- <a href="#time-editor-panel">Idősor</a>: Használatával módosíthatja a munkaidő-tartományát.
- <a href="#navigation-panel">Alkalmazás sávja</a>: A felhasználói felügyeleti lehetőségeket, például az aktuális bérlőt tartalmazza. A téma-és nyelvi beállítások módosítására használhatja.

## <a name="environment-drop-down-list"></a>Környezet legördülő lista

A környezet legördülő lista megjeleníti az összes Time Series Insights környezetét, amelyhez hozzáfér. A lista utólagos elszámolású környezeteket tartalmaz, például a Time Series Insights előzetes verzióját. A lista S1/S2 környezeteket is tartalmaz, amelyek általánosan elérhetők.

1. Válassza ki a megjelenített környezet melletti legördülő nyilat.

   [@no__t – 1a Vezérlőpult](media/v2-update-explorer/explorer-two.png)](media/v2-update-explorer/explorer-two.png#lightbox)

1. Ezután válassza ki a kívánt környezetet.

## <a name="navigation-menu"></a>Navigációs menü

  [@no__t – 1a navigációs menü](media/v2-update-explorer/explorer-three.png)](media/v2-update-explorer/explorer-three.png#lightbox)

A navigációs menüben választhat két nézet közül:

* **Elemzés**: Felhasználhatja a modellezett vagy nem modellezett idősorozat-adatok részletes elemzését.
* **Modell**: Használatával leküldheti az új Time Series Insights előnézeti típusokat, hierarchiákat és példányokat a Time Series Insights-modellbe.

## <a name="hierarchy-tree"></a>Hierarchia fája

A hierarchia fastruktúrája megjeleníti a kiválasztott adatelemeket, amelyek modelleket, adott eszközöket és érzékelőket tartalmaznak az eszközökön.

### <a name="model-search-panel"></a>Modell keresési panelje

A modell keresési paneljén könnyedén keresheti meg és navigálhatja az idősorozat-modell hierarchiáját, hogy megkeresse a diagramon megjeleníteni kívánt idősorozat-példányokat. A példányok kiválasztása után a rendszer hozzáadja az aktuális diagramhoz és az adategységhez is.

  [@no__t – 1a modell keresési panelje](media/v2-update-explorer/explorer-four.png)](media/v2-update-explorer/explorer-four.png#lightbox)

### <a name="model-authoring"></a>Modell készítése

A Azure Time Series Insights előzetes verziója támogatja a teljes létrehozási, olvasási, frissítési és törlési (szifilisz) műveleteket az idősorozat-modellben.

* **Idősorozat-modell típusa**: A számítások végrehajtásához Time Series Insights típusokat használhat változók vagy képletek definiálásához. Egy adott Time Series Insights-példánnyal vannak társítva. A típus lehet egy vagy több változót.
* **Idősorozat-modell hierarchiája**: A hierarchiák az Ön adatainak szisztematikus szervezetei. Hierarchiák jelzik a Time Series Insights adatait a különböző entitások közötti kapcsolatokat.
* **Idősorozat-modell példánya**: A példányok maguk az idősorozatok. A legtöbb esetben ezek a **DeviceID** vagy a **assetid**, amely az eszköz egyedi azonosítója a környezetben.

Az Idősorozat-modell kapcsolatos további információkért lásd: [alkalommal sorozat modellek](./time-series-insights-update-tsm.md).

## <a name="preview-well"></a>Előzetes verzió

A jól megjeleníti a példányok mezőit és a kijelölt Time Series Insights példányokhoz tartozó egyéb metaadatokat. A jobb oldalon található jelölőnégyzetek bejelölésével elrejtheti vagy megjelenítheti az aktuális diagram adott példányait. A jelenlegi adatokból is eltávolíthatja a konkrét adatelemeket, ha kiválasztja a vörös **Törlés** (kuka) vezérlőt az elem bal oldalán.

  [@no__t – 1a előzetes verzió](media/v2-update-explorer/explorer-five.png)](media/v2-update-explorer/explorer-five.png#lightbox)

**Az elemzési diagram** elrendezésének újrakonfigurálásához válassza a jobb felső sarokban található ellipszisek ikont:

  [@no__t – 1Telemetry elrendezési beállításai](media/v2-update-explorer/explorer-six.png)](media/v2-update-explorer/explorer-six.png#lightbox)

> [!NOTE]
> Ha a következő üzenet jelenik meg, a példány nem rendelkezik a kiválasztott időtartományon belül semmilyen adattal. A probléma megoldásához növelje az időtartamot, vagy győződjön meg arról, hogy a példány az adattovábbítást hajtja végre.
>
> ![Nincs értesítés](media/v2-update-explorer/explorer-seven.png)

## <a name="preview-chart"></a>Előnézeti diagram

A diagramon Time Series Insights-példányokat is megjeleníthet vonalakként. A diagram méretének növeléséhez kiválaszthatja a környezeti panelt, az adatmodellt és az időtartomány-alapú vezérlőpultot.

  [@no__t – 1Preview diagram – áttekintés](media/v2-update-explorer/explorer-eight.png)](media/v2-update-explorer/explorer-eight.png#lightbox)

- **Kiválasztott dátumtartomány**: Meghatározza, hogy mely adatelemek érhetők el a vizualizációhoz.

- **Belső dátumtartomány csúszka eszköz**: A két végpont vezérlőelem használatával húzza őket a kívánt időtartományra.

- **Időtartomány összeomlásának vezérlése**: Összecsukja és kibővíti az időtartomány panel-szerkesztőt.

- **Y tengely formátumának vezérlője**: Váltás a rendelkezésre álló y tengelyes nézet beállításain keresztül:

    * `Default`: Mindegyik sorban külön y tengely szerepel.
    * `Stacked`: A használatával több sort is halmozhat ugyanazon y tengelyen, és az y tengely adatváltozása a kiválasztott vonal alapján változik.
    * `Shared`: Az y tengelyen megjelenített összes érték együttesen jelenik meg.

- **Aktuális adatelem**: A jelenleg kijelölt adatelem és a hozzá tartozó részletek.

Egy adott adatszelet további részletezéséhez kattintson a bal gombbal egy adatpontra az aktuális gráfon, majd húzza a kijelölt területét a választott végpontra. Kattintson a jobb gombbal a szürke elemre, és válassza a **Nagyítás**lehetőséget, ahogy az a következő képen látható:

  [@no__t – 1Preview diagram nagyítása](media/v2-update-explorer/explorer-nine.png)](media/v2-update-explorer/explorer-nine.png#lightbox)

A **nagyítási** művelet elvégzése után megjelenik a kiválasztott adathalmaz. Válassza ki az y tengely formátuma vezérlőelemet, hogy a Time Series Insights-adatainak három y tengelyes ábrázolásával legyen átirányítva.

  [@no__t – 1Preview diagram y tengelye](media/v2-update-explorer/explorer-ten.png)](media/v2-update-explorer/explorer-ten.png#lightbox)

Itt láthatja a megosztott Y tengelyek példáját:

  [@no__t – megosztott Y tengelyek 1Preview](media/v2-update-explorer/explorer-eleven.png)](media/v2-update-explorer/explorer-eleven.png#lightbox)

## <a name="time-editor-panel"></a>Időszerkesztő panel

Ha a Time Series Insights előzetes verzió használata során először válassza meg azt az időtartományt. A kiválasztott időtartomány szabályozza a Time Series Insights Preview widgetekkel való manipulációhoz rendelkezésre álló adatkészletet. A következő webes vezérlők Time Series Insights előzetes verzióban érhetők el a munkaidő-tartomány kiválasztásához:

  [@no__t – 1Time kiválasztási panel](media/v2-update-explorer/explorer-twelve.png)](media/v2-update-explorer/explorer-twelve.png#lightbox)

1. **Belső dátumtartomány csúszka eszköz**: A két végpont vezérlőelem használatával húzza őket a kívánt időtartományra. Ezt a belső dátumtartományt a külső dátumtartomány csúszka vezérlőeleme korlátozza.

1. **Dátumtartomány gombjainak növelése és csökkentése**: Növelje vagy csökkentse az időtartamot úgy, hogy a kívánt intervallumra kattint.

1. **Időtartomány összeomlásának vezérlése**: Ez a webes vezérlés lehetővé teszi az összes vezérlő elrejtését, kivéve a belső dátumtartomány csúszka eszközét.

1. **Külső dátumtartomány csúszkájának vezérlője**: A végpont vezérlőelemekkel válassza ki a külső dátumtartományt, amely elérhető lesz a belső dátumtartomány vezérlőelemnél.

1. **Gyors időpontok dátum-tartomány legördülő listája**: Ezzel gyorsan válthat az előre beállított időtartományok kiválasztásai között, például az elmúlt **30 percben**, az **utolsó 12 órában**vagy egy **Egyéni tartományon**. Ez az érték módosítása módosítja a időköz méretű csúszkaeszközt tárgyalt elérhető időköz tartományok is.

1. **Intervallum-méret csúszka eszköz**: Ezzel az időtartammal nagyíthatja és kicsinyítheti az intervallumokat. Ez a művelet idő nagy szeletei közötti mozgás pontosabb felügyeletét biztosítja. A simított trendeket a szeletek számára a kis méretűre (ezredmásodpercben) jeleníti meg. A segítségével részletes, nagy felbontású adatmennyiségeket tekinthet meg az adatairól. A csúszka alapértelmezett kiindulási pontjaként a választását, amely elosztja a megoldás, a lekérdezés sebessége és a granularitási az adatok a legoptimálisabb nézet van beállítva.

1. **Dátumtartomány és a között a webes vezérlő**: Ezzel a webes vezérlővel egyszerűen kiválaszthatja a kívánt dátum-és időtartományokat. A vezérlőelem használatával váltson a különböző időzónák között. Miután elvégezte a módosítások alkalmazását az aktuális munkaterületre, válassza a **Mentés**lehetőséget.

   [@no__t – 1To és a kiválasztási panel](media/v2-update-explorer/explorer-thirteen.png)](media/v2-update-explorer/explorer-thirteen.png#lightbox)

## <a name="navigation-panel"></a>Navigációs panel

Megjelenik a Time Series Insights előnézet navigációs panel a Time Series Insights alkalmazás tetején. A következő funkciókat biztosítja.

### <a name="current-session-share-link-control"></a>Aktuális munkamenet megosztási hivatkozás-vezérlő

  [@no__t – 1Share ikon](media/v2-update-explorer/explorer-fifteen.png)](media/v2-update-explorer/explorer-fifteen.png#lightbox)

Az új **megosztás** ikon kiválasztásával megoszthatja az URL-hivatkozást a csapatával.

  [@no__t – 1Share a példány URL-címét](media/v2-update-explorer/url-share.png)](media/v2-update-explorer/url-share.png#lightbox)

### <a name="tenant-section"></a>Bérlő szakasz

  [@no__t – 1Tenant kiválasztása](media/v2-update-explorer/explorer-sixteen.png)](media/v2-update-explorer/explorer-sixteen.png#lightbox)

* Megjeleníti az aktuális Time Series Insights bejelentkezési fiókjának adatait.
* Használja az elérhető Time Series Insights témák közötti váltásra.
* Ezzel a szolgáltatással megtekintheti az előzetes [demó webalkalmazást](https://insights.timeseries.azure.com/preview/demo).

### <a name="theme-selection"></a>Témakijelölést

Új téma kiválasztásához válassza a jobb felső sarokban található profil ikont. Ezután válassza a **téma módosítása**lehetőséget.

  [@no__t – 1Theme kiválasztása](media/v2-update-explorer/theme-selection.png)](media/v2-update-explorer/theme-selection.png#lightbox)

> [!TIP]
> A nyelv kiválasztása a profil ikonjának kiválasztásával is elérhető.

Az Azure Time Series Insights előzetes verziója támogatja a két témák:

* **Világos téma**: Az ebben a dokumentumban látható alapértelmezett téma.
* **Sötét téma**: Az itt látható módon jeleníti meg a Explorert:

  [@no__t – sötét 1Selected téma](media/v2-update-explorer/explorer-seventeen.png)](media/v2-update-explorer/explorer-seventeen.png#lightbox)

## <a name="s1s2-environment-controls"></a>S1/S2 környezeti vezérlők

### <a name="preview-terms-panel"></a>Előnézet feltételei panel

Ez a rész csak meglévő S1/S2-környezetek, amelyek a frissített felhasználói felületén a kezelővel vonatkozik. Előfordulhat, hogy az általánosan elérhető termék és az előzetes verzió együttes használatát szeretné használni. Hozzáadtunk bizonyos funkciói a meglévő felhasználói felületen a frissített Explorerben, de a teljes felhasználói felület is igénybe a már meglévő Time Series Insights explorer környezetet S1/S2. 

A hierarchia helyett a Time Series Insights feltételek panel jelenik meg, ahol lekérdezéseket határozhat meg a környezetben. Használatával szűrheti az adatait egy predikátum alapján.

  [@no__t – 1Where lekérdezési panel](media/v2-update-explorer/explorer-eighteen.png)](media/v2-update-explorer/explorer-eighteen.png#lightbox)

A Time Series Insights előzetes feltételek szerkesztő panelen a következő paramétereket fogadja:

**Hol**: A WHERE záradék használatával gyorsan szűrheti az eseményeket az alábbi táblázatban felsorolt operandusok használatával. Ha egy fájlkeresést operandust kiválasztásával a predikátum automatikusan frissül, hogy a keresés alapján. Operand támogatott típusok a következők:

| Művelet | Támogatott típusok   | Megjegyzések |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, időtartam | |
| `=`, `!=`, `<>` | String, Bool, Double, DateTime, az időtartam, NULL értékű |
| `IN` | String, Bool, Double, DateTime, az időtartam, NULL értékű | Az összes operandusok azonos típusú legyen vagy NULL állandó. |
| `HAS` | Sztring | A jobb oldalon csak konstans karakterlánc-literálok engedélyezettek. Üres karakterlánc és NULL érték nem engedélyezett. |

A támogatott lekérdezési műveletekkel és adattípusokkal kapcsolatos további tudnivalókért lásd a [Time Series kifejezés (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx)című témakört.

### <a name="examples-of-where-clauses"></a>Példák a WHERE záradékokra

  [@no__t – 1Where záradék – példák](media/v2-update-explorer/explorer-nineteen.png)](media/v2-update-explorer/explorer-nineteen.png#lightbox)

**Mérték**: Egy legördülő lista, amely az aktuális diagram elemeiként használható numerikus oszlopokat (**Double**) jeleníti meg.

**Felosztás**: Ez a legördülő lista megjeleníti a modellben található összes elérhető kategorikus oszlopot (karakterláncot), amely alapján csoportosíthatja adatait. Akár öt kifejezést is hozzáadhat ugyanazon az x tengelyen. Adja meg a kívánt paramétereket, majd a **Hozzáadás** gombra kattintva vegyen fel egy friss kifejezést.

  [@no__t – 1Queried és szűrt nézet](media/v2-update-explorer/explorer-twenty.png)](media/v2-update-explorer/explorer-twenty.png#lightbox)

A diagram panel elemeinek megjelenítéséhez és elrejtéséhez válassza a látható ikont, ahogy az alábbi képen is látható. A lekérdezések teljes eltávolításához válassza a piros **X**elemet.

  [@no__t – 1Queried és szűrt nézet – két](media/v2-update-explorer/explorer-twenty-one.png)](media/v2-update-explorer/explorer-twenty-one.png#lightbox)

## <a name="next-steps"></a>További lépések

- Tudnivalók a [tárolásról és a bejövő](./time-series-insights-update-storage-ingress.md) forgalomról a Azure Time Series Insights előzetes verziójában.
- Olvassa el az [adatmodellezés](./time-series-insights-update-tsm.md)Time Series Insights előnézeti dokumentumát.
- Ismerje meg [, hogyan diagnosztizálhatja és elháríthatja](./time-series-insights-update-how-to-troubleshoot.md) a Time Series Insights-példányát.
