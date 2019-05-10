---
title: Adatok megjelenítése az az Azure Time Series Insights – előzetes explorer |} A Microsoft Docs
description: Ez a cikk ismerteti a szolgáltatások és az Azure Time Series Insights – előzetes explorer web app alkalmazásban elérhető beállítások.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 05/03/2019
ms.custom: seodec18
ms.openlocfilehash: 862465a7611f1a2bc65dbb0c49c4de512bd239de
ms.sourcegitcommit: 4891f404c1816ebd247467a12d7789b9a38cee7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2019
ms.locfileid: "65442101"
---
# <a name="visualize-data-in-the-explorer-preview"></a>Adatok megjelenítése az a explorer előzetes verzió

Ez a dokumentum ismerteti a felhasználói felület/UX szolgáltatásokat és az Azure Time Series Insights előzetes verziója felületének [bemutató webalkalmazás](https://insights.timeseries.azure.com/preview/demo). Pontosabban azt tárgyalja, az üzemeltetett minta, felület-testreszabási beállítások és a megadott bemutató navigáláshoz elrendezését.

## <a name="prerequisites"></a>Előfeltételek

Első lépések az Azure Time Series Insights – előzetes explorer, a következőket kell tennie:

* Állítsa be a Time Series Insights környezettel rendelkezik. További információ a kiépítése egy példányt próbálkozzon további a [Azure Time Series Insights – előzetes](./time-series-insights-update-create-environment.md) oktatóanyag.
* [Adja meg az adatok elérése](./time-series-insights-data-access.md) a Time Series Insights környezetet a fiókhoz létrehozott. Parancs saját maga számára hozzáférést is megadhat.
* Adatok leküldése a környezetet a Time Series Insights környezetet eseményforrás hozzáadása:
  * Ismerje meg, [hogyan csatlakozhat egy eseményközpontba](./time-series-insights-how-to-add-an-event-source-eventhub.md)
  * Ismerje meg, [hogyan lehet csatlakozni az IoT hub](./time-series-insights-how-to-add-an-event-source-iothub.md)

## <a name="learn-about-the-preview-explorer"></a>További tudnivalók az előzetes verzió explorer

Az Azure Time Series Insights – előzetes Explorert a következő elemekből áll:

[![A Tallózó nézet](media/v2-update-explorer/explorer-one.png)](media/v2-update-explorer/explorer-one.png#lightbox)

1. <a href="#environment-dropdown">**Környezet panel**</a>: Az Azure TSI-környezetek jeleníti meg.
1. <a href="#navigation-menu">**Navigációs menü**</a>: Lehetővé teszi, hogy közötti váltás a **elemzés** és **modell** oldalakat.
1. <a href="#hierarchy-tree">**Hierarchiafája**</a>: Válassza ki az adott modell és a forrásadatok adatelemeket teszi lehetővé.
1. <a href="#preview-well">**Jól Time series**</a>: Az aktuálisan kiválasztott adatelemet a színkódolás táblázatos formában jeleníti meg.
1. <a href="#preview-chart">**Diagram panel**</a>:  Megjeleníti az aktuális működő diagramot.
1. <a href="#time-editor-panel">**Idősor**</a>:  Módosíthatja a működő azt az időtartományt.
1. <a href="#navigation-panel">**Alkalmazássávon**</a>:  A felhasználó olyan felügyeleti lehetőségeket, például az aktuális bérlő tartalmazza, és lehetővé teszi a téma- és nyelvi beállítások módosítása.

## <a name="environment-dropdown"></a>Környezetre vonatkozó legördülő menü

A környezet legördülő lista összes hozzáfér a Time Series Insights környezetet jelenít meg. A lista tartalmazza a használatalapú környezetek (előzetes verzió) és az S1/S2 környezetek (általános rendelkezésre állás vagy a GA). 

1. Egyszerűen kattintson a lefelé mutató nyíl melletti megjelenített környezetében:

   [![A Vezérlőpult](media/v2-update-explorer/explorer-two.png)](media/v2-update-explorer/explorer-two.png#lightbox)

1. Ezután válassza ki a kívánt környezetre.

## <a name="navigation-menu"></a>Navigációs menü

  [![A navigációs menü](media/v2-update-explorer/explorer-three.png)](media/v2-update-explorer/explorer-three.png#lightbox)

A navigációs menü két nézet közötti kiválasztását teszi lehetővé:

* **Elemezheti**: Lehetővé teszi a diagram és megoldásban gazdag analitikai hajthatja végre az modellezett vagy unmodeled idősoros adatokat.
* **Modell**: Lehetővé teszi új Time Series Insights előzetes verziója típusok, hierarchiákat és példányok leküldése a Time Series Insights-modellben.

## <a name="hierarchy-tree"></a>Hierarchiafája

A hierarchiafája jeleníti meg a kijelölt adatok elemek többek között a modelleket, meghatározott eszközök és érzékelők az eszközökön.

### <a name="model-search-panel"></a>Modell keresése panel

A modell keresés panelen lehetővé teszi keresését és keresse meg az Idősorozat-modell hierarchia az adott időpont sorozat példányai a diagramon megjelenítendő található. Amikor kiválasztja a példányok, kerülnek az aktuális diagram és az adatok is.

  [![A modell keresése panel](media/v2-update-explorer/explorer-four.png)](media/v2-update-explorer/explorer-four.png#lightbox)

### <a name="model-authoring"></a>Modell létrehozásához

Az Azure Time Series Insights előzetes verziója az Idősorozat-modell a teljes körű létrehozási, olvasási, frissítési és törlési (CRUD) műveleteket támogatja.  

* **Time Series modelltípus**: Time Series Insights-típusok engedélyezése meghatározó változókat vagy számítások adatelemzésre képleteket. Azok a egy adott Time Series Insights-példány. A típus lehet egy vagy több változót.
* **Time Series hierarchiája**: A hierarchiák szintekbe az adatok rendszeres szervezetek. Hierarchiák jelzik a Time Series Insights adatait a különböző entitások közötti kapcsolatokat.
* **Time Series modell példány**: Az idősor magukat a példányai. A legtöbb esetben vannak a **DeviceID** vagy **AssetID**, azaz a környezetben az eszköz egyedi azonosítója.

Az Idősorozat-modell kapcsolatos további információkért lásd: [alkalommal sorozat modellek](./time-series-insights-update-tsm.md).

## <a name="preview-well"></a>Előzetes verzió is

A jól jeleníti meg a szolgáltatáspéldány-mezők és egyéb kiválasztott TSI-példányok társított metaadatokat. A jobb oldalon a jelölőnégyzetek segítségével konkrét példányok az aktuális diagram megjelenítése vagy elrejtése. Is eltávolítható adott elemeket az aktuális adatok alapján a vörös kattintva **törlése** (Kuka) vezérlőelem a bal oldali prvku.

  [![Jól az előzetes verzió](media/v2-update-explorer/explorer-five.png)](media/v2-update-explorer/explorer-five.png#lightbox)

Az elrendezését is újrakonfigurálhatja az **elemzés** diagram lap jobb felső sarkában a három pont ikon kiválasztásával:

  [![Telemetria elrendezési beállítások](media/v2-update-explorer/explorer-six.png)](media/v2-update-explorer/explorer-six.png#lightbox)

> [!NOTE]
> A következő üzenetet látja, ha a példány nem rendelkezik semmilyen adatot a kiválasztott időtartományon. A probléma megoldásához, időtartamának növelése, vagy győződjön meg arról, hogy a példány küld-e adatokat.
>
> ![Nincs adat értesítés](media/v2-update-explorer/explorer-seven.png)

## <a name="preview-chart"></a>Előnézeti diagramon

A diagram a vonalakként megjelenítheti a TSI-példányok. A környezet panel adatmodell és idő span Vezérlőpult összecsukódjon a webes szabályozza, hogy a diagram nagyobb kattint.

  [![Előnézet diagram – áttekintés](media/v2-update-explorer/explorer-eight.png)](media/v2-update-explorer/explorer-eight.png#lightbox)

1. **A kijelölt dátumtartományban**: Szabályozza, mely adatok elemek érhetők el a vizualizációt.

1. **Belső dátum tartomány csúszkaeszközt**: A két végpontot vezérlőkkel húzza őket a kívánt időre időtartam.

1. **Idő a tartomány összecsukása vezérlő**: Magasságúra csökken, és az idő span panel szerkesztő fájl.

1. **Vezérlő y tengely formázása**: Váltás a rendelkezésre álló y tengely megjelenítési beállításai:

    * `Default`: Minden sor egy egyedi y tengely rendelkezik.
    * `Stacked`: Lehetővé teszi több sort az azonos y tengelyen zásobníku az y tengely adatok módosítását a kijelölt sor alapján.
    * `Shared`: Az összes y tengely adatok együtt jelenik meg.

1. **Aktuális adatelem**: A jelenleg kijelölt adatelemének és a kapcsolódó részletes adatait.

Tovább részletezhető egy adott adatszelet kattint, a jelenlegi grafikonon egy adatpontra, és húzza a kijelölt terület a végpontra a választott. Kattintson a jobb gombbal a szürke színnel, a kijelölt területen, majd kattintson **nagyítás** a következő képen látható módon:

  [![Előnézet diagram nagyítás](media/v2-update-explorer/explorer-nine.png)](media/v2-update-explorer/explorer-nine.png#lightbox)

Végrehajtása után a **nagyítás** művelet, látni fogja a kiválasztott adatkészlet. Kattintson a Time Series Insights adatait három y tengely megjelenítésének léptetés vezérlő y tengely formázása.

  [![Előnézet diagram y tengely](media/v2-update-explorer/explorer-ten.png)](media/v2-update-explorer/explorer-ten.png#lightbox)

Itt látható egy példa megosztott y tengellyel:

  [![Előzetes verziójú közös y tengely](media/v2-update-explorer/explorer-eleven.png)](media/v2-update-explorer/explorer-eleven.png#lightbox)

## <a name="time-editor-panel"></a>Idő szerkesztő panel

Ha a Time Series Insights előzetes verzió használata során először válassza meg azt az időtartományt. A kiválasztott azt az időtartományt szabályozza az adatkészletet, amely a Time Series Insights előzetes verziója a widgetek esetében adatkezelési érhető el. A következő webes vezérlők kiválasztásakor a működő azt az időtartományt Time Series Insights előzetes verziója érhető el.

  [![Időkiválasztás panel](media/v2-update-explorer/explorer-twelve.png)](media/v2-update-explorer/explorer-twelve.png#lightbox)

1. **Belső dátumtartomány csúszkaeszközt**: A két végpontot vezérlőkkel húzza őket a kívánt időre időtartam. A külső dátumtartomány csúszka vezérlő belső dátumtartomány korlátozza.

1. **Növelése és csökkentése dátum tartomány gombok**: Növelje vagy csökkentse az időtartam számára a kívánt időközt vagy gomb kiválasztásával.

1. **Idő a tartomány összecsukása vezérlő**: A webes vezérlő lehetővé teszi a belső dátumtartomány csúszkaeszközt kivételével az összes vezérlő elrejtése.

1. **Külső dátumtartomány csúszkavezérlő**: A végpont vezérlők segítségével válassza ki a külső dátum tartományt, amely a belső tartomány-ellenőrzési elérhető lesz.

1. **A legördülő dátumtartomány Gyorshivatkozások**: Lehetővé teszi, hogy előre beállított idő span kijelölések, mint például az utolsó között gyorsan válthat **30 perc**, a **elmúlt 12 órában**, vagy egy **egyéni tartomány**. Ez az érték módosítása módosítja a időköz méretű csúszkaeszközt tárgyalt elérhető időköz tartományok is.

1. **Intervallum méretű csúszkaeszközt**: Nagyítás adataikkal időközök azonos időtartomány keresztül teszi lehetővé. Ez a művelet idő nagy szeletei közötti mozgás pontosabb felügyeletét biztosítja. Kisebb, mint egy ezredmásodperc alatt maradnak, így megjelenik az adatok részletes, nagy felbontású darabok szeletek lefelé zökkenőmentes trendeket jelenít meg. A csúszka alapértelmezett kiindulási pontjaként a választását, amely elosztja a megoldás, a lekérdezés sebessége és a granularitási az adatok a legoptimálisabb nézet van beállítva.

1. **Dátumtartomány és a webes vezérlő**: A webes vezérlő is egyszerűen kattintson és válassza ki a kívánt dátum és idő tartományok. A vezérlőelem használatával váltson a különböző időzónák között. Miután módosításokat végez a alkalmazni az aktuális munkaterületen válassza ki **mentése**.

   [![És onnan időkiválasztás panel](media/v2-update-explorer/explorer-thirteen.png)](media/v2-update-explorer/explorer-thirteen.png#lightbox)

## <a name="navigation-panel"></a>Navigációs panel

A Time Series Insights előzetes verziója navigációs panel felső részén a TSI alkalmazás jelenik meg. A következő funkciókat biztosítja.

### <a name="current-session-share-link-control"></a>Aktuális munkamenet megosztási hivatkozás-vezérlő

  [![Megosztás ikon](media/v2-update-explorer/explorer-fifteen.png)](media/v2-update-explorer/explorer-fifteen.png#lightbox)

Válassza ki az új **megosztása** ikonra kattintva egy URL-Címhivatkozás megosztásra a csapatával.

  [![Megosztás a példány URL-címe](media/v2-update-explorer/url-share.png)](media/v2-update-explorer/url-share.png#lightbox)

### <a name="tenant-section"></a>Bérlő szakasz

  [![Bérlő kiválasztása](media/v2-update-explorer/explorer-sixteen.png)](media/v2-update-explorer/explorer-sixteen.png#lightbox)

* A jelenlegi Time Series Insights bejelentkezési fiók adatait jeleníti meg.
* Lehetővé teszi, hogy váltani a Time Series Insights elérhető témák között.
* Lehetővé teszi, hogy az előzetes verzió megtekintéséhez [bemutató webalkalmazás](https://insights.timeseries.azure.com/preview/demo).

### <a name="theme-selection"></a>Témakijelölést

Válassza ki az új témát, kattintson a profil ikonjára a jobb felső sarokban található. Ezután válassza ki **téma módosítása**.

  [![Témakijelölést](media/v2-update-explorer/theme-selection.png)](media/v2-update-explorer/theme-selection.png#lightbox)

> [!TIP]
> Nyelv kiválasztása a profil ikonjára kattintva is érhető el.

Az Azure Time Series Insights előzetes verziója támogatja a két témák:

* **Világos téma**: Az alapértelmezett téma jelenik meg ebben a dokumentumban.
* **Sötét téma**:  Ez a beállítás az Intézőben, itt látható módon:

  [![A kijelölt sötét téma](media/v2-update-explorer/explorer-seventeen.png)](media/v2-update-explorer/explorer-seventeen.png#lightbox)

## <a name="s1s2-environment-controls"></a>S1/S2 környezeti vezérlők

### <a name="preview-terms-panel"></a>Előzetes verzió kifejezések panel

Ez a rész csak meglévő S1/S2-környezetek, amelyek a frissített felhasználói felületén a kezelővel vonatkozik. Érdemes a GA-termék és az előzetes verzió használata. Hozzáadtunk bizonyos funkciói a meglévő felhasználói felületen a frissített Explorerben, de a teljes felhasználói felület is igénybe a már meglévő Time Series Insights explorer környezetet S1/S2.  

A hierarchia helyett látni fogja a Time Series Insights kifejezések panel, ahol megadhatja a lekérdezések a környezetben. Lehetővé teszi az adatok egy predikátum alapján szűrni.

  [![Lekérdezés panel](media/v2-update-explorer/explorer-eighteen.png)](media/v2-update-explorer/explorer-eighteen.png#lightbox)

A Time Series Insights előzetes feltételek szerkesztő panelen a következő paramétereket fogadja:

**Ahol**: A where záradék segítségével gyorsan operandusok használatával az események az alábbi táblázatban felsorolt szűrőt. Ha egy fájlkeresést operandust kiválasztásával a predikátum automatikusan frissül, hogy a keresés alapján. Operand támogatott típusok a következők:

| Művelet | Támogatott típusok   | Megjegyzések |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Double, DateTime, időtartam | |
| `=`, `!=`, `<>` | String, Bool, Double, DateTime, az időtartam, NULL értékű |
| `IN` | String, Bool, Double, DateTime, az időtartam, NULL értékű | Az összes operandusok azonos típusú legyen vagy NULL állandó. |
| `HAS` | Karakterlánc | Csak állandó karakterlánc-literálnak engedélyezett jobb oldalán. Üres karakterlánc, és a NULL nem engedélyezettek. |

Tudjon meg többet a támogatott lekérdezési műveletek és adattípusok olvasásával [Time Series kifejezés (TSX)](https://docs.microsoft.com/rest/api/time-series-insights/preview-tsx).

### <a name="examples-of-where-clauses"></a>Példák a Where záradékban

  [![Ahol záradék példák](media/v2-update-explorer/explorer-nineteen.png)](media/v2-update-explorer/explorer-nineteen.png#lightbox)

**Mérték**: A listából, amely megjeleníti a numerikus oszlopok (**Double típusú értékekkel**) használhat a jelenlegi diagramhoz elemként.

**Felosztási szempont**: A legördülő minden a kategorikus oszlopok (karakterlánc), amely az adatok alapján csoportosíthatja a modell jeleníti meg. Ugyanazt az x tengelyen megtekintéséhez legfeljebb öt feltételeket adhat hozzá. Adja meg a kívánt paramétereket, és válassza ki **Hozzáadás** friss kifejezés hozzáadásához.

  [![Az egyik lekérdezett és szűrt nézet](media/v2-update-explorer/explorer-twenty.png)](media/v2-update-explorer/explorer-twenty.png#lightbox)

Megjelenítése, és a diagram panelen elemek elrejtése a látható ikon kiválasztásával az alábbi képen látható módon. Lekérdezések teljes mértékben eltávolíthatja a piros gombra kattintva **X**.

  [![A lekérdezett és szűrt nézet két](media/v2-update-explorer/explorer-twenty-one.png)](media/v2-update-explorer/explorer-twenty-one.png#lightbox)

## <a name="next-steps"></a>További lépések

- Ismerje meg [tárolási a bejövő és kimenő](./time-series-insights-update-storage-ingress.md) a az Azure Time Series Insights előzetes verziója.

- A Time Series Insights előzetes verziója elolvashassák [adatmodellezés](./time-series-insights-update-tsm.md).

- Ismerje meg, [diagnosztizálása és hibaelhárítása](./time-series-insights-update-how-to-troubleshoot.md) a Time Series Insights-példány.
