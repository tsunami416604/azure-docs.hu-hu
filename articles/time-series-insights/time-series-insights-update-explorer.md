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
ms.date: 12/03/2018
ms.openlocfilehash: 3a2958d838f0646ac030421b3785fc11a1466dd6
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53086725"
---
# <a name="visualize-data-in-the-explorer-preview"></a>Adatok megjelenítése az a explorer előzetes verzió

Ez a cikk azt ismerteti, lehetőségek és beállítások az Azure Time Series Insights előzetes verziója elérhető [explorer webalkalmazás](https://insights.timeseries.azure.com/preview/samples).

## <a name="prerequisites"></a>Előfeltételek

Mielőtt használná az Azure Time Series Insights – előzetes explorer, a következőket kell tennie:

* Állítsa be a Time Series Insights környezettel rendelkezik. További információkért lásd: [oktatóanyag: Azure Time Series Insights – előzetes](./time-series-insights-update-create-environment.md).
* A fiók a Time Series Insights-környezetet, Ön által létrehozott adatok hozzáférést biztosítanak. Parancs saját maga számára hozzáférést is megadhat.
* Eseményforrás hozzáadása a Time Series Insights környezetet az adatok leküldéséhez a környezetet.

## <a name="learn-about-the-azure-time-series-insights-preview-explorer"></a>További tudnivalók az Azure Time Series Insights – előzetes explorer

  ![Explorer egy az egyhez][1]

Az Azure Time Series Insights – előzetes Explorert a következő elemekből áll:

* **Navigációs sáv**: lehetővé teszi, hogy analytics és a modell lapok közötti váltáshoz.
* **Hierarchiafája**: lehetővé teszi a forrásadatok adott adatelem választja.
* **Jól Time series**: Megjeleníti az aktuálisan kiválasztott adatelemet.
* **Diagram panel**: az aktuális működő diagramot jelenít meg.
* **Idősor**: módosíthatja a működő azt az időtartományt.
* **Alkalmazássávon**: a felhasználó olyan felügyeleti lehetőségeket, például az aktuális bérlő tartalmazza, és lehetővé teszi a téma- és nyelvi beállítások módosítása.

## <a name="time-series-insights-preview-environment-panel"></a>Time Series Insights előzetes környezet panel

A környezet panel megjeleníti az összes hozzáfér a Time Series Insights környezetet. A lista tartalmazza az utólagos elszámolású környezetek előzetes verziója és az S1/S2 környezetek (elérhetővé tétel GA). Egyszerűen kattintson a használni kívánt Time Series Insights-környezet.

  ![a második Explorer][2]

## <a name="time-series-insights-preview-navigation-menu"></a>Time Series Insights előzetes verziója navigációs menü

  ![Explorer-három][3]

A navigációs menüben válthat a Time Series Insights-alkalmazások között:

* **Elemezheti**: lehetővé teszi a diagram és megoldásban gazdag analitikai hajthatja végre az modellezett vagy unmodeled idősoros adatokat.

* **Modell**: lehetővé teszi a Time Series Insights előzetes verziója új típusú, hierarchiákat és példányok leküldése a Time Series Insights-modellben.

## <a name="time-series-insights-preview-model-authoring"></a>Time Series Insights előzetes verziója modell létrehozásához

Az ehhez az alkalmazáshoz az Idősorozat-modell létrehozási, olvasási, frissítési és törlési (CRUD) műveleteket hajthat végre.  

* **Time Series modelltípus**: a Time Series Insights típusok meghatározó változókat vagy számítások adatelemzésre képletek engedélyezése. Azok a egy adott Time Series Insights-példány. A típus lehet egy vagy több változót.
* **Time Series hierarchiája**: a hierarchiák szintekbe az adatok rendszeres szervezetek. Hierarchiák jelzik a Time Series Insights adatait a különböző entitások közötti kapcsolatokat.
* **Time Series modell példány**: példányai az idősor magukat. A legtöbb esetben a DeviceID vagy AssetID, amely a környezetben az eszköz egyedi azonosítója.

Az Idősorozat-modell kapcsolatos további információkért lásd: [alkalommal sorozat modellek](./time-series-insights-update-tsm.md).

## <a name="time-series-insights-preview-model-search-panel"></a>Time Series Insights előzetes verziója modell keresése panel

A modell keresés panelen lehetővé teszi keresését és keresse meg az Idősorozat-modell hierarchia az adott időpont sorozat példányai a diagramon megjelenítendő található. Amikor kiválasztja a példányok, kerülnek az aktuális diagram és az adatok is.

  ![negyedik Explorer][4]

## <a name="time-series-insights-preview-well"></a>Jól Time Series Insights előzetes verziója

A jól szolgáltatáspéldány-mezők és más metaadatokat adott sorozat példányai társított jeleníti meg. Jobb oldalán található jelölőnégyzetek segítségével konkrét példányok az aktuális diagram megjelenítése vagy elrejtése. A piros x vezérlő az elem jobb kattintással is az aktuális adatok alapján is eltávolítása meghatározott elemek.

  ![Explorer-öt][5]

A telemetriai adatok panel egy elem jobb függőleges nézet is található az adatok lekérése is kiemelése.

  ![Explorer-hat][6]

> [!NOTE]
> A következő üzenetet látja, ha a példány nem rendelkezik semmilyen adatot a kiválasztott időtartományon. A probléma megoldásához, időtartamának növelése, vagy győződjön meg arról, hogy a példány küld-e adatokat.
>
> ![Explorer-hét][7]

## <a name="time-series-insights-preview-chart"></a>Time Series Insights előzetes verziója diagram

A diagram megjelenítheti a vonalakként idő sorozat példányai. A környezet panel adatmodell és idő span Vezérlőpult összecsukódjon a webes szabályozza, hogy a diagram nagyobb kattint.

  ![Explorer-nyolc][8]

1. **A kijelölt dátumtartományban**: szabályozza, mely adatok elemek érhetők el a vizualizációt.

1. **Belső dátum tartomány csúszkaeszközt**: a két végpontot vezérlők használata húzza őket a kívánt időre időtartam.

1. **Idő a tartomány összecsukása vezérlő**: összecsukása és kibővíti az idő span panel szerkesztő.

1. **Vezérlő y tengely formázása**: Váltás az elérhető y tengely megjelenítési beállításai:

    * `Default`: Minden sor egy egyedi y tengely rendelkezik.
    * `Stacked`: Lehetővé teszi több sort az azonos y tengelyen zásobníku az y tengely adatok módosítását a kijelölt sor alapján.
    * `Shared`: Az összes y tengely adatok együtt jelenik meg.

1. **Aktuális adatelem**: A jelenleg kijelölt adatelemének és a kapcsolódó részletes adatait.

Tovább részletezhető egy adott adatszelet kattint, a jelenlegi grafikonon egy adatpontra, és húzza a kijelölt terület a végpontra a választott. Kattintson a jobb gombbal a szürke színnel, a kijelölt területen, majd kattintson a nagyítás, a következő képen látható módon:

  ![Explorer-kilenc][9]

A Nagyítás műveletet hajt végre, miután megjelenik a kiválasztott adatkészlet. Kattintson a Time Series Insights adatait három y tengely megjelenítésének léptetés vezérlő y tengely formázása.

  ![Explorer-tíz][10]

Itt látható egy példa megosztott y tengellyel:

  ![Explorer tizenegy][11]

## <a name="time-series-insights-preview-time-editor-panel"></a>Time Series Insights előzetes verziója idő szerkesztő panel

Ha a Time Series Insights előzetes verzió használata során először válassza meg azt az időtartományt. A kiválasztott azt az időtartományt szabályozza az adatkészletet, amely a Time Series Insights előzetes verziója a widgetek esetében adatkezelési érhető el. A következő webes vezérlők kiválasztásakor a működő azt az időtartományt Time Series Insights előzetes verziója érhető el.

  ![Explorer-tizenkét][12]

1. **Belső dátumtartomány csúszkaeszközt**: a két végpontot vezérlők használata húzza őket a kívánt időre időtartam. A külső dátumtartomány csúszka vezérlő belső dátumtartomány korlátozza.

1. **Növelése és csökkentése dátum tartomány gombok**: növekedést vagy csökkenést az idő az az időtartam alatt azt szeretné, vagy a gomb kiválasztásával span.

1. **Idő a tartomány összecsukása vezérlő**: A webes vezérlő lehetővé teszi a belső dátumtartomány csúszkaeszközt kivételével az összes vezérlő elrejtése.

1. **Külső dátumtartomány csúszkavezérlő**: a végpont-vezérlők használatával válassza ki a külső dátum tartományt, amely a belső tartomány-ellenőrzési elérhető lesz.

1. **Gyorshivatkozások dátum tartomány legördülő**: lehetővé teszi, hogy gyorsan válthat előre beállított idő span beállításokat is, például az elmúlt 30 percben, az elmúlt 12 órában vagy egy egyéni tartomány között. Ez az érték módosítása módosítja a időköz méretű csúszkaeszközt tárgyalt elérhető időköz tartományok is.

1. **Intervallum méretű csúszkaeszközt**: lehetővé teszi, hogy azonos keresztül adataikkal időközök nagyítás időtartományon. Ez a művelet idő nagy szeletei közötti mozgás pontosabb felügyeletét biztosítja. Kisebb, mint egy ezredmásodperc alatt maradnak, így megjelenik az adatok részletes, nagy felbontású darabok szeletek lefelé zökkenőmentes trendeket jelenít meg. A csúszka alapértelmezett kiindulási pontjaként a választását, amely elosztja a megoldás, a lekérdezés sebessége és a granularitási az adatok a legoptimálisabb nézet van beállítva.

1. **Dátumtartomány és a webes vezérlő**: a webes vezérlő is egyszerűen kattintson és válassza ki a kívánt dátum és idő tartományok. A vezérlőelem használatával váltson a különböző időzónák között. Miután módosításokat végez a alkalmazni az aktuális munkaterületen válassza ki **mentése**.

  ![Explorer tizenhárom][13]

## <a name="time-series-insights-preview-navigation-panel"></a>Time Series Insights előzetes verziója navigációs panel

A Time Series Insights előzetes verziója navigációs panelen a következő szolgáltatásokat nyújtja:

  ![Explorer-tizennégy][14]

### <a name="current-session-share-link-control"></a>Aktuális munkamenet megosztási hivatkozás-vezérlő

  ![Explorer-tizenöt][15]

Jelölje ki a kapcsolat webalkalmazás (kiemelve) mentéséhez, vagy az Azure Time Series Insights működő munkamenet megosztani egy URL-címet generálni tartalmazó:

* Jelenleg kijelölt időtartományban
* Kijelölt intervallumának mérete
* Kijelölt adatok jól

### <a name="tenant-section"></a>Bérlő szakasz

  ![Explorer-tizenhat][16]

* A jelenlegi Time Series Insights bejelentkezési fiók adatait jeleníti meg.
* Lehetővé teszi, hogy váltani a Time Series Insights elérhető témák között.

### <a name="theme-selection"></a>Témakijelölést

Az Azure Time Series Insights előzetes verziója támogatja a két témák:

* **Világos téma**: az alapértelmezett téma jelenik meg ebben a dokumentumban.
* **Sötét téma**: Ez a beállítás a explorer itt látható módon:

  ![Explorer-seventeen][17]

Itt is módosíthatja a támogatott nyelvek között.

## <a name="s1s2-environment-controls"></a>S1/S2 környezeti vezérlők

### <a name="time-series-insights-preview-terms-panel"></a>Time Series Insights előzetes verziója kifejezések panel

Ez a rész csak meglévő S1/S2-környezetek, amelyek a frissített felhasználói felületén a kezelővel vonatkozik. Érdemes a GA-termék és az előzetes verzió használata. Hozzáadtunk bizonyos funkciói a meglévő felhasználói felületen a frissített Explorerben, de a teljes felhasználói felület is igénybe a már meglévő Time Series Insights explorer környezetet S1/S2.  

A hierarchia helyett látni fogja a Time Series Insights kifejezések panel, ahol megadhatja a lekérdezések a környezetben. Lehetővé teszi az adatok egy predikátum alapján szűrni.

  ![Explorer-tizennyolc][18]

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

  ![Explorer tizenkilenc][19]

**Mérték**: A legördülő megjeleníti az összes numerikus oszlopai (**Double típusú értékekkel**) a jelenlegi diagramhoz elemeket is használni.

**Felosztási szempont**: A legördülő oszlopokat jelenít meg minden a rendelkezésre álló kategorikus (karakterlánc) a modellt, amely az adatok alapján csoportosíthatja. Ugyanazt az x tengelyen megtekintéséhez legfeljebb öt feltételeket adhat hozzá. Adja meg a kívánt paramétereket, és válassza ki **Hozzáadás** friss kifejezés hozzáadásához.

  ![Explorer-erdőtopológia][20]

Megjelenítése, és a diagram panelen elemek elrejtése a látható ikon kiválasztásával az alábbi képen látható módon. Lekérdezések teljes mértékben eltávolíthatja a piros gombra kattintva **x**.

  ![Explorer-erdőtopológia-egy][21]

## <a name="next-steps"></a>További lépések

Lásd az alábbi cikkeket:
* [Az Azure Time Series Insights előzetes verziója storage és a bejövő forgalom](./time-series-insights-update-storage-ingress.md)
* [Adatmodellezés](./time-series-insights-update-tsm.md)
* [Diagnosztizálása és hibaelhárítása](./time-series-insights-update-how-to-troubleshoot.md)

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
