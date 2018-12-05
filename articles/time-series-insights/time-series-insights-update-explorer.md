---
title: Adatok megjelenítése az Azure Time Series Insights explorer frissítés |} A Microsoft Docs
description: Az Azure Time Series Insights explorer frissítése
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: 48f3be2de2f23cef311994c603d50544b5d13a6e
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52879782"
---
# <a name="visualize-data-in-the-explorer-preview"></a>Az explorer (előzetes verzió) az adatok megjelenítése

Ez a cikk azt ismerteti, lehetőségek és beállítások az Azure Time Series Insights (előzetes verzió) belül elérhető [explorer webalkalmazás](https://insights.timeseries.azure.com/preview/samples).

## <a name="prerequisites"></a>Előfeltételek

Mielőtt használná az Azure Time Series Insights (előzetes verzió) explorer, a következőket kell tennie:

* Egy Time Series Insights-környezet üzembe helyezett rendelkezik. További információ a kiépítése itt egy Time Series Insights-környezetet.
* A Time Series Insights környezetet hozott létre a fiókhoz tartozó adatok hozzáférést biztosítanak. Hozzáférést lehet biztosítani másoknak, valamint saját magának.
* Eseményforrás hozzáadása a Time Series Insights környezetet az adatok leküldéséhez a környezetet.

## <a name="learn-about-the-azure-time-series-insights-preview-explorer"></a>További tudnivalók az Azure Time Series Insights (előzetes verzió) explorer

  ![Explorer egy az egyhez][1]

Az Azure Time Series Insights (előzetes verzió) explorer az alábbi hét elemeket van osztva:

1. Navigációs sáv TSI (előzetes verzió) lehetővé teszi elemzések és a modell lapok közötti váltáshoz.
1. Hierarchiafája TSI (előzetes verzió) válassza ki a forrásadatok adott adatelemeket.
1. TSI (előzetes verzió) a time series jól látható a jelenleg kijelölt összes adatelemeket.
1. A TSI (előzetes verzió) diagram panel megjeleníti az aktuális működő diagram.
1. A TSI (előzetes verzió) idősor lehetővé teszi a működő azt az időtartományt módosítását.
1. A TSI (előzetes verzió) alkalmazássávon tartalmazza a felhasználói beállítások (például az aktuális bérlő), és lehetővé teszi, hogy a téma- és nyelvi beállítások módosítása.

## <a name="tsi-preview-environment-panel"></a>A TSI (előzetes verzió) környezet panel

A környezet panel hozzáfér a TSI környezetek jeleníti meg. Ez magában foglalja használatalapú környezetek (előzetes verzió) listája, valamint S1/S2 környezetek (elérhetővé tétel GA). Egyszerűen kattintson a használni kívánt TSI-környezetet.

  ![a második Explorer][2]

## <a name="time-series-insights-preview-navigation-menu"></a>Time Series Insights (előzetes verzió) navigációs menü

  ![Explorer-három][3]

A navigációs menü lehetővé teszi a TSI-alkalmazások közötti váltás:

* Elemzése – lehetővé teszi, hogy a diagram & megoldásban gazdag analitikai hajthatja végre az modellezett vagy unmodeled idősoros adatokat.

* Modell - lehetővé teszi, hogy új TSI frissítéstípusok, hierarchiákat és példányok leküldése a TSI-modellben.

## <a name="time-series-insights-update-model-authoring"></a>A Time Series Insights frissítése modell létrehozásához

Ez az alkalmazás teszi lehetővé az Idősorozat-modell CRUD-műveletek végrehajtásához.  

* TSM típusa – TSI-típusok engedélyezése meghatározó változókat vagy számítások adatelemzésre képleteket, azok a megadott TSI-példány. A típus lehet egy vagy több változót.
* TSM hierarchia - hierarchiák az adatok rendszeres szervezetek. Hierarchiák a TSI-adatait a különböző entitások közötti kapcsolatokat ábrázoló.
* TSM instance - példány az idősor maguk is. A legtöbb esetben ez lesz a **DeviceID** vagy **AssetID**, azaz a környezetben az eszköz egyedi azonosítója.

A TSM kapcsolatos további információkért olvassa el [alkalommal sorozat modellek](./time-series-insights-update-tsm.md).

## <a name="time-series-insights-preview-model-search-panel"></a>Time Series Insights (előzetes verzió) modell keresési Panel

A modell keresés panelen keresését és keresse meg a TSM hierarchiában található a megadott idő sorozat példányai a diagramon megjelenítendő teszi lehetővé. Amikor kiválasztja a példányok, nem csak hozzá az aktuális diagram, de is bekerülnek az adatok is.

  ![negyedik Explorer][4]

## <a name="time-series-insights-preview-well"></a>Idő Series Insights (előzetes verzió) jól

A jól szolgáltatáspéldány-mezők és más metaadatokat adott sorozat példányai társított jeleníti meg. Jobb oldalán a jelölőnégyzetek lehetővé teszik az aktuális diagram konkrét példányok megjelenítése vagy elrejtése. A piros x vezérlő az elem jobb kattintással is az aktuális adatok alapján is eltávolítása meghatározott elemek.

  ![Explorer-öt][5]

A telemetriai adatok panel egy elem jobb függőleges nézet is található az adatok lekérése is kiemelése.

  ![Explorer-hat][6]

> [!NOTE]
> A következő ikon jelenik meg, ha a példány nem rendelkezik semmilyen adatot a kiválasztott időtartam során.
> Oldja meg, növelheti a kiválasztott időtartomány, illetve győződjön meg arról, hogy a példány küld-e adatokat.

  ![Explorer-hét][7]

## <a name="time-series-insights-preview-chart"></a>Idősorozat-Insights (előzetes verzió) diagram

A diagram idő sorozat példányai ugyanitt sorok teszi lehetővé. A környezet panel adatmodell és idő span Vezérlőpult összecsukódjon a webes szabályozza, hogy a diagram nagyobb kattint.

  ![Explorer-nyolc][8]

1. **A kijelölt dátumtartományban** – a kijelölt dátumtartományban a diagram panel, a vezérlők, mely adatelem Vizualizáció elérhető lesz.

1. **Belső dátum tartomány csúszkaeszközt** – kattintva a két végpontot vezérlőket használja, és húzza őket a kívánt idővel span.

1. **Idő a tartomány összecsukása vezérlő** – a vezérlő összecsukása és kibővíti az idő span panel szerkesztő.

1. **Vezérlő y tengely formázása** – kattintson erre a vezérlőre elérhető y tengely megjelenítési beállítások közötti váltáshoz. Az y tengely nézetben elérhető lehetőségek a következők:

    * `Default`  – minden sorban van egy egyéni y tengely
    * `Stacked` – Ez lehetővé teszi, hogy több sorban található azonos y zásobníku, az y tengely adatok módosítása a kijelölt sor alapján
    * `Shared` – Az összes y tengely adatok együtt jelenik meg

1. **Aktuális adatelem** – a jelenleg kijelölt adatelemének és a kapcsolódó részletes adatait.

További részletesen is egy adott adatszelet által kattint, a jelenlegi grafikonon egy adatpont közben tartsa lenyomva az egérgombot, és húzza a kijelölt terület, a tetszőleges végpontot. Kattintson a jobb gombbal a szürke színnel, a kijelölt területen, majd kattintson a nagyítás, lent látható módon. További lehetőségek:

  ![Explorer-kilenc][9]

A Nagyítás művelet végrehajtása, után most már megjelenik a kiválasztott adatkészlet. Kattintson a vezérlő az y tengely formázása a TSI-adatok három különböző y tengely megjelenítésének közötti váltáshoz.

  ![Explorer-tíz][10]

Itt látható egy példa egy megosztott y tengellyel.

  ![Explorer tizenegy][11]

## <a name="time-series-insights-preview-time-editor-panel"></a>Time Series Insights (előzetes verzió) idő szerkesztő Panel

Ha a TSI dolgozik, először kiválaszthatja meg azt az időtartományt. A kiválasztott azt az időtartományt szabályozza az adatkészlet, amely a TSI a frissítés widgetek esetében adatkezelési érhető el. A következő webes vezérlők kiválasztásakor a működő azt az időtartományt a TSI frissítés érhető el.

  ![Explorer-tizenkét][12]

1. **Belső dátumtartomány csúszkaeszközt** – kattintva a két végpontot vezérlőket használja, és húzza őket a kívánt idővel span. A "belső" dátumtartomány szerint a "Külső dátum" tartomány csúszkavezérlő alább felsorolt fog lehet korlátozott.

1. Növelése és csökkentése tartomány gombok ** állapotú - növelését, vagy bármelyik gombra kattint a kívánt alatt az időtartam csökkentéséhez.

1. **Idő a tartomány összecsukása vezérlő** – a webes vezérlő lehetővé teszi, hogy a belső dátum tartomány csúszkaeszközt kivételével az összes vezérlő elrejtése.

1. **Külső dátumtartomány csúszkavezérlő** -végpont vezérlők használatával válassza ki a külső dátumtartományt, amely a "Belső dátum" időtartomány-vezérlő elérhető lesz.

1. **Gyorshivatkozások dátum tartomány legördülő** – lehetővé teszi az előre beállított idő span beállításokat, például az elmúlt 30 percben, az elmúlt 12 órában, egyéni tartomány között. Ez az érték módosítása módosítja a rendelkezésre álló időköz tartományokat, lásd az alábbiakban az intervallum mérete csúszka eszközben is.

1. **Intervallum méretű csúszkaeszközt** – az intervallum mérete csúszkaeszközt lehetővé teszi nagyítás adataikkal időközök keresztül a ugyanazt azt az időtartományt. Ez biztosítja, hogy idő nagy webterületek trendeket jelenít meg az zökkenőmentes szeletek le legyen az ezredmásodperc alatt maradnak, így megjelenik az adatok részletes, nagy felbontású darabok közötti mozgás még pontosabb irányítását. A csúszka alapértelmezett kiindulási pontjaként a kijelölésből; az adatok a legoptimálisabb nézet van beállítva terheléselosztási megoldás, a lekérdezés sebessége és a granularitási.

1. **Dátumtartomány és a webes vezérlő** – egyszerűen kattintson és válassza ki a kívánt dátum és idő címtartományok webes vezérlőhöz. A vezérlőelem használatával váltson a különböző időzónák között. Miután elvégezte a módosításokat, szeretné-e alkalmazni az aktuális munkaterületen kattintson a Mentés gombra.

  ![Explorer tizenhárom][13]

## <a name="time-series-insights-preview-navigation-panel"></a>Time Series Insights (előzetes verzió) navigációs panel

A TSI frissítés navigációs panelen a következő szolgáltatásokat nyújtja:

  ![Explorer-tizennégy][14]

### <a name="current-session-share-link-control"></a>Aktuális munkamenet megosztási hivatkozás-vezérlő

  ![Explorer-tizenöt][15]

Kattintson a hivatkozás bekarikázott webes vezérlő mentéséhez, vagy ossza meg az aktuális Azure TSI munkafolyamat, amely tartalmaz egy URL-címet generálni:

* Jelenleg kijelölt időtartományban
* Kijelölt intervallumának mérete
* Kijelölt adatok jól

### <a name="tenant-section"></a>Bérlő szakasz

  ![Explorer-tizenhat][16]

* A jelenlegi TSI bejelentkezési fiók adatait jeleníti meg
* Lehetővé teszi, hogy a rendelkezésre álló TSI témák közötti váltás.

### <a name="theme-selection"></a>Témakijelölést

Az Azure TSI (előzetes verzió) két témák támogatja:

* **Világos téma**: Ez az alapértelmezett téma jelenik meg ebben a dokumentumban.
* **Sötét téma**: Ez a beállítás be egy sötét téma a explorer rendereli a lent látható módon:

  ![Explorer-seventeen][17]

Itt is módosíthatja a támogatott nyelvek között.

## <a name="s1s2-environment-controls"></a>S1/S2 környezeti vezérlők

### <a name="time-series-insights-preview-terms-panel"></a>Time Series Insights (előzetes verzió) kifejezések Panel

Ez a szakasz csak a frissített felhasználói felületén a kezelővel próbál meglévő S1/S2 környezet vonatkozik. Ehhez használja a végleges verzió termék és frissítése (előzetes verzió) egy másik együtt érdemes. A Microsoft bizonyos funkciói a meglévő felhasználói felületről történő hozzáadása a frissített explorer, de tudja, a meglévő TSI Explorerben S1/S2 környezetben mindig megtekintheti a teljes felhasználói felület.  

A hierarchia helyett a TSI-kifejezések panel jelenik meg. Ez az, ahol megadhatja a lekérdezések a környezetben. Ez teszi lehetővé az adatok alapján egy predikátum szűréséhez.

  ![Explorer-tizennyolc][18]

A TSI (előzetes verzió) kifejezések szerkesztő Panel a következő paramétereket fogadja.

**Ahol**: A where záradék lehetővé teszi, hogy szűrését az eseményeket, az alább felsorolt operandusok használatával. Keresés kiválasztása/kattintva végrehajtása, ha a predikátum lesz alapján automatikusan frissülnek, hogy a keresés. Operand támogatott típusok a következők:

| Művelet | Támogatott típusok   | Megjegyzések |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` |    Double, DateTime, időtartam  | |
| `=`, `!=`, `<>`   | String, Bool, Double, DateTime, az időtartam, NULL értékű    |
| `IN` |    String, Bool, Double, DateTime, az időtartam, NULL értékű |    Az összes operandusok azonos típusú legyen vagy NULL állandó. |
| `HAS` |   Karakterlánc |    Csak állandó karakterlánc-literálnak engedélyezett jobb oldalán. Üres karakterlánc, és a NULL nem engedélyezettek. |

### <a name="examples-of-where-clauses"></a>Példák a Where záradékban

  ![Explorer tizenkilenc][19]

**Mérték**: A legördülő lista látható összes numerikus oszlopot (**Double típusú értékekkel**) a jelenlegi diagramhoz elemeket is használni.

**Felosztási szempont**: A legördülő lista megjeleníti a kategorikus (karakterlánc) oszlopok modellben szereplő összes a érhető el, használható az adatok által. Ugyanazt az x tengelyen megtekintéséhez legfeljebb öt feltételeket adhat hozzá. Adja meg a kívánt paramétereket, majd a **Hozzáadás** friss kifejezés hozzáadása gombra.

  ![Explorer-erdőtopológia][20]

Kattintva látható ikonra, ahogy az alábbi elemek a diagram panelről megjelenítése és elrejtése. Lekérdezések teljes mértékben eltávolíthatja a piros gombra kattintva `X` alább látható.

  ![Explorer-erdőtopológia-egy][21]

## <a name="next-steps"></a>További lépések

Olvassa el a [Azure TSI (előzetes verzió) tárolási a bejövő és kimenő](./time-series-insights-update-storage-ingress.md).

További információ [adatmodellezés](./time-series-insights-update-tsm.md).

Ismerje meg [felismerése és hibaelhárítási](./time-series-insights-update-how-to-troubleshoot.md).

<!-- Images -->
[1]: media/v2-update-explorer/explorer-one.png
[2]: media/v2-update-explorer/explorer-two.png
[3]: media/v2-update-explorer/explorer-three.png
[4]: media/v2-update-explorer/explorer-four.png
[5]: media/v2-update-explorer/explorer-five.png
[6]: media/v2-update-explorer/explorer-six.png
[7]: media/v2-update-explorer/explorer-seven.png
[8]: media/v2-update-explorer/explorer-eight.png
[9]: media/v2-update-explorer/explorer-nine.png
[10]: media/v2-update-explorer/explorer-ten.png
[11]: media/v2-update-explorer/explorer-eleven.png
[12]: media/v2-update-explorer/explorer-twelve.png
[13]: media/v2-update-explorer/explorer-thirteen.png
[14]: media/v2-update-explorer/explorer-fourteen.png
[15]: media/v2-update-explorer/explorer-fifteen.png
[16]: media/v2-update-explorer/explorer-sixteen.png
[17]: media/v2-update-explorer/explorer-seventeen.png
[18]: media/v2-update-explorer/explorer-eighteen.png
[19]: media/v2-update-explorer/explorer-nineteen.png
[20]: media/v2-update-explorer/explorer-twenty.png
[21]: media/v2-update-explorer/explorer-twenty-one.png