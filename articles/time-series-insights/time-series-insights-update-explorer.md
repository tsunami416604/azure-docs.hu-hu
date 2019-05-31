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
ms.date: 05/03/2019
ms.custom: seodec18
ms.openlocfilehash: c4f3053063ce33d2777387da2c53effd61b05f1a
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399864"
---
# <a name="visualize-data-in-the-explorer-preview"></a>Adatok megjelenítése az a explorer előzetes verzió

Ez a dokumentum ismerteti a felhasználói felület és felhasználói élményt javító szolgáltatások és az Azure Time Series Insights előzetes verziója felületének [bemutató webalkalmazás](https://insights.timeseries.azure.com/preview/demo). Pontosabban azt tárgyalja, az üzemeltetett minta, felület-testreszabási beállítások és a megadott bemutató navigáláshoz elrendezését.

## <a name="prerequisites"></a>Előfeltételek

Első lépések az Azure Time Series Insights – előzetes explorer, a következőket kell tennie:

* Állítsa be a Time Series Insights környezettel rendelkezik. További információ a kiépítése példányát, próbálja meg a [Azure Time Series Insights – előzetes](./time-series-insights-update-create-environment.md) oktatóanyag.
* [Adja meg az adatok elérése](./time-series-insights-data-access.md) a Time Series Insights környezetet a fiókhoz létrehozott. Parancs saját maga számára hozzáférést is megadhat.
* Adatok leküldése a környezetet a Time Series Insights környezetet eseményforrás hozzáadása:
  * Ismerje meg, [hogyan csatlakozhat egy eseményközpontba](./time-series-insights-how-to-add-an-event-source-eventhub.md).
  * Ismerje meg, [hogyan lehet csatlakozni az IoT hub](./time-series-insights-how-to-add-an-event-source-iothub.md).

## <a name="learn-about-the-preview-explorer"></a>További tudnivalók az előzetes verzió explorer

Az Azure Time Series Insights – előzetes Explorert a következő elemekből áll:

[![A Tallózó nézet](media/v2-update-explorer/explorer-one.png)](media/v2-update-explorer/explorer-one.png#lightbox)

- <a href="#environment-drop-down-list">Környezet panel</a>: Az Azure Time Series Insights-környezetek jeleníti meg.
- <a href="#navigation-menu">Navigációs menü</a>: Amellyel váltani a **elemzés** és **modell** oldalakat.
- <a href="#hierarchy-tree">Hierarchiafája</a>: Használhatja a forrásadatok adott modell- és az elemek kiválasztásával.
- <a href="#preview-well">Jól Time series</a>: Az aktuálisan kiválasztott adatelemet a színkódok táblázatos formában jeleníti meg.
- <a href="#preview-chart">Diagram panel</a>: Megjeleníti az aktuális működő diagramot.
- <a href="#time-editor-panel">Idősor</a>: Használhatja a működő időtartam módosítása.
- <a href="#navigation-panel">Alkalmazássávon</a>: A felhasználó olyan felügyeleti lehetőségeket, például az aktuális bérlő tartalmazza. Téma- és nyelvi beállítások módosításához használhatja azt.

## <a name="environment-drop-down-list"></a>Környezet legördülő lista

A környezet legördülő lista összes hozzáfér a Time Series Insights környezetet jeleníti meg. A lista tartalmazza az utólagos elszámolású környezetekben, például a Time Series Insights előzetes verziója. A lista az S1/S2 környezeteket, amelyek mindenki számára elérhető is tartalmazza.

1. Válassza ki a megjelenített környezet melletti legördülő nyílra.

   [![A Vezérlőpult](media/v2-update-explorer/explorer-two.png)](media/v2-update-explorer/explorer-two.png#lightbox)

1. Ezután válassza ki a kívánt környezetre.

## <a name="navigation-menu"></a>Navigációs menü

  [![A navigációs menü](media/v2-update-explorer/explorer-three.png)](media/v2-update-explorer/explorer-three.png#lightbox)

A navigációs menü segítségével válassza ki a két nézetet között:

* **Elemezheti**: Ezzel a diagram és megoldásban gazdag analitikai hajthatja végre az modellezett vagy unmodeled idősoros adatokat.
* **Modell**: Vele Time Series Insights előzetes verziója új típusú, hierarchiákat és példányok leküldése a Time Series Insights-modellben.

## <a name="hierarchy-tree"></a>Hierarchiafája

A hierarchiafája jeleníti meg a kijelölt adatok elemek, amelyek modellek, meghatározott eszközök és érzékelők az eszközökön.

### <a name="model-search-panel"></a>Modell keresése panel

A modell keresése panel segítségével keresését és keresse meg az Idősorozat-modell hierarchia az adott időpont sorozat példányai a diagramon megjelenítendő található. Miután kiválasztotta a példányok, a rendszer hozzáadja azt az aktuális diagram és az adatok is.

  [![A modell keresése panel](media/v2-update-explorer/explorer-four.png)](media/v2-update-explorer/explorer-four.png#lightbox)

### <a name="model-authoring"></a>Modell létrehozásához

Az Azure Time Series Insights előzetes verziója támogatja a teljes létrehozása, olvasása, frissítése és törlése az Idősorozat-modell (CRUD) műveleteket.

* **Time Series modelltípus**: A Time Series Insights-típusok segítségével megadhatja a változókat vagy számítások adatelemzésre képleteket. Még egy adott Time Series Insights-példány társítva. A típus lehet egy vagy több változót.
* **Time Series hierarchiája**: A hierarchiák szintekbe az adatok rendszeres szervezetek. Hierarchiák jelzik a Time Series Insights adatait a különböző entitások közötti kapcsolatokat.
* **Time Series modell példány**: Az idősor magukat a példányai. Azok az esetek többségében a **DeviceID** vagy **AssetID**, azaz a környezetben az eszköz egyedi azonosítója.

Az Idősorozat-modell kapcsolatos további információkért lásd: [alkalommal sorozat modellek](./time-series-insights-update-tsm.md).

## <a name="preview-well"></a>Előzetes verzió is

A jól szolgáltatáspéldány-mezők és más metaadatokat adott Time Series Insights-példányokat társított jeleníti meg. Válassza a jobb oldalon a jelölőnégyzeteket, elrejtése vagy megjelenítése az aktuális diagram konkrét példányok is. Valamint eltávolíthatja adott adatelem az aktuális adatok alapján is a vörös kiválasztásával **törlése** (Kuka) vezérlőelem bal oldalán, az elem.

  [![Jól az előzetes verzió](media/v2-update-explorer/explorer-five.png)](media/v2-update-explorer/explorer-five.png#lightbox)

Elrendezését újrakonfigurálása a **elemzés** diagram oldalon, a jobb felső sarokban a három pont ikon kiválasztása:

  [![Telemetria elrendezési beállítások](media/v2-update-explorer/explorer-six.png)](media/v2-update-explorer/explorer-six.png#lightbox)

> [!NOTE]
> Ha a következő üzenet jelenik meg, a példány semmilyen adatot nem rendelkezik kiválasztott időtartományon. A probléma megoldásához, növelje az azt az időtartományt, vagy győződjön meg arról, hogy a példány küld-e adatokat.
>
> ![Nincs adat értesítés](media/v2-update-explorer/explorer-seven.png)

## <a name="preview-chart"></a>Előnézeti diagramon

A diagram a vonalakként megjelenítheti a Time Series Insights-példány. A környezet panel adatmodell és idő span Vezérlőpult is összecsukása a webes szabályozza, hogy a diagram nagyobb kiválasztásával.

  [![Előnézet diagram – áttekintés](media/v2-update-explorer/explorer-eight.png)](media/v2-update-explorer/explorer-eight.png#lightbox)

- **A kijelölt dátumtartományban**: Szabályozza, mely adatok elemek érhetők el a vizualizációt.

- **Belső dátum tartomány csúszkaeszközt**: A két végpontot vezérlőkkel keresztül szeretné azt az időtartományt a felvételhez.

- **Idő a tartomány összecsukása vezérlő**: Magasságúra csökken, és az idő span panel szerkesztő fájl.

- **Vezérlő y tengely formázása**: Váltás a rendelkezésre álló y tengely megjelenítési beállításai:

    * `Default`: Minden sor egy egyedi y tengely rendelkezik.
    * `Stacked`: Használhatja az azonos y tengelyen több sor zásobníku az y tengely adatok módosítását a kijelölt sor alapján.
    * `Shared`: Az összes y tengely adatok együtt jelenik meg.

- **Aktuális adatelem**: A jelenleg kijelölt adatelemének és a kapcsolódó részletes adatait.

Részletes további be egy adott adatszelet, kattintson a bal gombbal egy adatpontra a diagram a, és húzza a kijelölt terület, a tetszőleges végpontot. Kattintson a jobb gombbal a kijelölt szürke területet, és válassza ki **nagyítás**, a következő képen látható módon:

  [![Előnézet diagram nagyítás](media/v2-update-explorer/explorer-nine.png)](media/v2-update-explorer/explorer-nine.png#lightbox)

Miután elvégezte a **nagyítás** művelet, megjelenik a kiválasztott adatokat, állítsa be. Válassza ki a Time Series Insights adatait három y tengely megjelenítésének léptetés vezérlő y tengely formázása.

  [![Előnézet diagram y tengely](media/v2-update-explorer/explorer-ten.png)](media/v2-update-explorer/explorer-ten.png#lightbox)

Itt látható egy példa megosztott Y tengely:

  [![Előzetes verziójú közös Y-tengely](media/v2-update-explorer/explorer-eleven.png)](media/v2-update-explorer/explorer-eleven.png#lightbox)

## <a name="time-editor-panel"></a>Idő szerkesztő panel

Ha a Time Series Insights előzetes verzió használata során először válassza meg azt az időtartományt. A kiválasztott időtartam azt szabályozza, az adatkészlet, amely a Time Series Insights előzetes verziója a widgetek esetében adatkezelési érhető el. A következő webes vezérlők érhetők el a Time Series Insights előzetes verziója kiválasztásakor a működő időtartama:

  [![Időkiválasztás panel](media/v2-update-explorer/explorer-twelve.png)](media/v2-update-explorer/explorer-twelve.png#lightbox)

1. **Belső dátum tartomány csúszkaeszközt**: A két végpontot vezérlőkkel keresztül szeretné azt az időtartományt a felvételhez. A belső dátumtartomány korlátozza a külső dátum tartomány csúszka vezérlő.

1. **Növelése és csökkentése dátum tartomány gombok**: Növelje vagy csökkentse az időtartam számára a kívánt időközt vagy gomb kiválasztásával.

1. **Idő a tartomány összecsukása vezérlő**: A webes vezérlő lehetővé teszi a belső dátum tartomány csúszkaeszközt kivételével az összes vezérlő elrejtése.

1. **Külső dátum tartomány csúszkavezérlő**: A végpont vezérlők segítségével válassza ki a külső dátumtartományt, amely elérhető a belső tartomány dátumvezérlő lesz.

1. **Gyorshivatkozások dátumtartomány legördülő lista**: Segítségével gyorsan válthat át előre beállított idő span beállításokat is, például az utolsó **30 perc**, a **elmúlt 12 órában**, vagy egy **egyéni tartomány**. Ez az érték módosítása módosítja a időköz méretű csúszkaeszközt tárgyalt elérhető időköz tartományok is.

1. **Intervallum méretű csúszkaeszközt**: Használhatja a Nagyítás adataikkal időközök keresztül a ugyanazt azt az időtartományt. Ez a művelet idő nagy szeletei közötti mozgás pontosabb felügyeletét biztosítja. Csak egy ezredmásodpercnél kisebb szeletek zökkenőmentes trendeket jelenít meg. Használhatja az adatok részletes, nagy felbontású darabok megtekintéséhez. A csúszka alapértelmezett kiindulási pontjaként a választását, amely elosztja a megoldás, a lekérdezés sebessége és a granularitási az adatok a legoptimálisabb nézet van beállítva.

1. **Időtartomány-vezérlő, és a webes dátum**: A webes vezérlőelem egyszerűen kiválaszthatja a kívánt dátum és idő tartományokat. A vezérlőelem használatával váltson a különböző időzónák között. A alkalmazni a jelenlegi munkaterületre történő módosítása után, válassza ki a **mentése**.

   [![És onnan időkiválasztás panel](media/v2-update-explorer/explorer-thirteen.png)](media/v2-update-explorer/explorer-thirteen.png#lightbox)

## <a name="navigation-panel"></a>Navigációs panel

A Time Series Insights előzetes verziója navigációs panel felső részén a Time Series Insights alkalmazás jelenik meg. A következő funkciókat biztosítja.

### <a name="current-session-share-link-control"></a>Aktuális munkamenet megosztási hivatkozás-vezérlő

  [![Megosztás ikon](media/v2-update-explorer/explorer-fifteen.png)](media/v2-update-explorer/explorer-fifteen.png#lightbox)

Válassza ki az új **megosztása** ikonra kattintva egy URL-Címhivatkozás megosztásra a csapatával.

  [![Megosztás a példány URL-címe](media/v2-update-explorer/url-share.png)](media/v2-update-explorer/url-share.png#lightbox)

### <a name="tenant-section"></a>Bérlő szakasz

  [![Bérlő kiválasztása](media/v2-update-explorer/explorer-sixteen.png)](media/v2-update-explorer/explorer-sixteen.png#lightbox)

* A Time Series Insights aktuális bejelentkezési fiók információit jeleníti meg.
* Ezzel a váltani a Time Series Insights elérhető témák között.
* Ezzel az előzetes verzió megtekintéséhez [bemutató webalkalmazás](https://insights.timeseries.azure.com/preview/demo).

### <a name="theme-selection"></a>Témakijelölést

Válassza ki az új témát, jelölje be a profil ikonjára a jobb felső sarokban található. Ezután válassza ki **téma módosítása**.

  [![Témakijelölést](media/v2-update-explorer/theme-selection.png)](media/v2-update-explorer/theme-selection.png#lightbox)

> [!TIP]
> Nyelv kiválasztása a profil ikonjára kattintva is érhető el.

Az Azure Time Series Insights előzetes verziója támogatja a két témák:

* **Világos téma**: Az alapértelmezett téma jelenik meg ebben a dokumentumban.
* **Sötét téma**: Ez a beállítás az Intézőben, itt látható módon:

  [![A kijelölt sötét téma](media/v2-update-explorer/explorer-seventeen.png)](media/v2-update-explorer/explorer-seventeen.png#lightbox)

## <a name="s1s2-environment-controls"></a>S1/S2 környezeti vezérlők

### <a name="preview-terms-panel"></a>Előzetes verzió kifejezések panel

Ez a rész csak meglévő S1/S2-környezetek, amelyek a frissített felhasználói felületén a kezelővel vonatkozik. Előfordulhat, hogy szeretné az általánosan elérhető termékek és az előzetes verzió használata. Hozzáadtunk bizonyos funkciói a meglévő felhasználói felületen a frissített Explorerben, de a teljes felhasználói felület is igénybe a már meglévő Time Series Insights explorer környezetet S1/S2. 

Helyett a hierarchiában lásd: a Time Series Insights kifejezések panel, ahol megadhatja a lekérdezések a környezetben. Az adatok alapján egy predikátum szűréséhez használja azt.

  [![Lekérdezés panel](media/v2-update-explorer/explorer-eighteen.png)](media/v2-update-explorer/explorer-eighteen.png#lightbox)

A Time Series Insights előzetes feltételek szerkesztő panelen a következő paramétereket fogadja:

**Ahol**: Használja a where záradék szűrését operandusok készletét használatával az események az alábbi táblázatban felsorolt. Ha egy fájlkeresést operandust kiválasztásával a predikátum automatikusan frissül, hogy a keresés alapján. Operand támogatott típusok a következők:

| Művelet | Támogatott típusok   | Megjegyzések |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, időtartam | |
| `=`, `!=`, `<>` | String, Bool, Double, DateTime, az időtartam, NULL értékű |
| `IN` | String, Bool, Double, DateTime, az időtartam, NULL értékű | Az összes operandusok azonos típusú legyen vagy NULL állandó. |
| `HAS` | String | Csak állandó karakterlánc-literálnak engedélyezettek a jobb oldalon. Nem engedélyezett üres karakterlánc, és NULL. |

Támogatott lekérdezési műveletek és az adattípusok kapcsolatos további tudnivalókért lásd: [Time Series kifejezés (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx).

### <a name="examples-of-where-clauses"></a>Példák a where záradékban

  [![Ahol záradék példák](media/v2-update-explorer/explorer-nineteen.png)](media/v2-update-explorer/explorer-nineteen.png#lightbox)

**Mérték**: A legördülő listából válassza ki, amely megjeleníti a numerikus oszlopok (**Double típusú értékekkel**) használhatja a jelenlegi diagramhoz elemként.

**Felosztási szempont**: A legördülő listából válassza ki az összes a kategorikus oszlopok (karakterláncokat) a modellt, amely az adatok alapján csoportosíthatja jeleníti meg. Ugyanazt az x tengelyen megtekintéséhez legfeljebb öt feltételeket adhat hozzá. Adja meg a paramétereket, és válassza **Hozzáadás** friss kifejezés hozzáadásához.

  [![Az egyik lekérdezett és szűrt nézet](media/v2-update-explorer/explorer-twenty.png)](media/v2-update-explorer/explorer-twenty.png#lightbox)

Is megjelenítése és elrejtése a látható ikon kiválasztásával a diagram panelen elemek, a következő képen látható módon. Lekérdezések teljes eltávolításához jelölje ki a piros **X**.

  [![A lekérdezett és szűrt nézet két](media/v2-update-explorer/explorer-twenty-one.png)](media/v2-update-explorer/explorer-twenty-one.png#lightbox)

## <a name="next-steps"></a>További lépések

- Ismerje meg [tárolási a bejövő és kimenő](./time-series-insights-update-storage-ingress.md) a az Azure Time Series Insights előzetes verziója.
- A Time Series Insights előzetes verziója elolvashassák [adatmodellezés](./time-series-insights-update-tsm.md).
- Ismerje meg, [diagnosztizálása és hibaelhárítása](./time-series-insights-update-how-to-troubleshoot.md) a Time Series Insights-példány.
