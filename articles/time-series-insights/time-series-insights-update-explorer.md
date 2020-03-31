---
title: Adatok megjelenítése az Előzetes verzió kezelőjében – Azure Time Series Insights | Microsoft dokumentumok
description: Ismerje meg az Azure Time Series Insights előzetes verziójával kapcsolatos explorerben elérhető funkciókat és lehetőségeket.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/07/2020
ms.custom: seodec18
ms.openlocfilehash: c117510749abcf997e414371faa6dea1dc79ea7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75861761"
---
# <a name="azure-time-series-insights-preview-explorer"></a>Az Azure Time Series Insights előzetes verziójának kezelője

Ez a cikk ismerteti az Azure Time Series Insights Preview [bemutató webes alkalmazáson](https://insights.timeseries.azure.com/preview/demo)belül elérhető különböző funkciókat és lehetőségeket.

## <a name="prerequisites"></a>Előfeltételek

Az Azure Time Series Insights előzetes verziókezelőjének első lépéseihez a következőket kell tennie:

* Egy Time Series Insights-környezet kiépített. További információ egy példány kiépítéséről az [Azure Time Series Insights előzetes verziójának](./time-series-insights-update-create-environment.md) oktatóanyagának olvasásával.
* [Adatelérés biztosítása](./time-series-insights-data-access.md) a fiókhoz létrehozott Time Series Insights-környezethez. Hozzáférést biztosíthat másokhoz és önmagához is.
* Adjon hozzá egy eseményforrást a Time Series Insights-környezethez az adatok környezetbe küldése érdekében:
  * További információ [az eseményközponthoz való csatlakozásról](./time-series-insights-how-to-add-an-event-source-eventhub.md) 
  * [Ismerje meg, hogyan csatlakozhat egy IoT-központhoz](./time-series-insights-how-to-add-an-event-source-iothub.md)

## <a name="explore-the-time-series-insights-preview-explorer"></a>Fedezze fel a Time Series Insights előzetes verziókezelőt

Az Azure Time Series Insights előzetes verziójú felfedezője a következő hét elemből áll:

[![A Time Series Insights előzetes kezelője – áttekintés](media/v2-update-explorer/preview-explorer-overview.png)](media/v2-update-explorer/preview-explorer-overview.png#lightbox)

1. [Környezet panel:](#1-environment-panel)Megjeleníti az összes Azure Time Series Insights-környezetben.
1. [Navigációs sáv:](#2-navigation-bar)Lehetővé teszi az **Elemzés** és a **Modell** lap közötti váltást.
1. [Hierarchiafa és keresőpanel](#3-hierarchy-tree-and-search-panel): Lehetővé teszi a ábrázolandó adatelemek kijelölését és keresését.
1. [Idősorozatok jól](#4-time-series-well): Az összes aktuálisan kijelölt adatelem megjelenítése.
1. [Diagram panel](#5-chart-panel): Az aktuális munkadiagram megjelenítése.
1. [Idővonal](#6-time-editor-panel): Lehetővé teszi a munkaidő módosítását.
1. [Alkalmazáspult:](#7-app-bar)Tartalmazza a felhasználói felügyeleti beállításokat (például a jelenlegi bérlőt), és lehetővé teszi azok és a nyelvi beállítások módosítását.


## <a name="1-environment-panel"></a>1. Környezetvédelmi panel

A környezet panelen megjelenik az összes Time Series Insights-környezet, amelyhez hozzáférése van. A lista tartalmazza a kiosztó-ki-kinézet (Előzetes verzió) környezeteket, valamint az S1/S2 környezeteket (Általános elérhetőség). Egyszerűen válassza ki a Time Series Insights környezetben szeretné használni, hogy azonnal kell venni ott.

1. Jelölje ki a megjelenített környezet melletti legördülő nyilat.

   [![Környezet panel](media/v2-update-explorer/environment-panel.png)](media/v2-update-explorer/environment-panel.png#lightbox)

1. Ezután válassza ki a kívánt környezetet.

## <a name="2-navigation-bar"></a>2. Navigációs sáv

  [![A navigációs sáv](media/v2-update-explorer/tsi-preview-navigation-bar.png)](media/v2-update-explorer/tsi-preview-navigation-bar.png#lightbox)

A navigációs sávsegítségével két nézet közül választhat:

* **Elemzés**: Segítségével diagramra és gazdag elemzéseket végezhet a modellező vagy nem modellező idősorozat-adatokon.
* **Modell:** Ezzel leküldéses új Time Series Insights előzetes típusok, hierarchiák és példányok a Time Series Insights modell.

### <a name="model-authoring"></a>Modell készítése

Az Azure Time Series Insights előzetes verzió támogatja a teljes létrehozási, olvasási, frissítési és törlési (CRUD) műveleteket a Time Series modellen.

[![A modell keresőpanelje](media/v2-update-explorer/model-search-panel.png)](media/v2-update-explorer/model-search-panel.png#lightbox)

* **Idősorozat-modell típusa:** A Time Series Insights-típusok segítségével változókat vagy képleteket definiálhat számításokhoz. Egy adott Time Series Insights-példához vannak társítva. Egy típusnak lehet egy vagy több változója.
* **Idősorozat-modell hierarchia:** A hierarchiák az adatok szisztematikus szervezetei. A hierarchiák a Különböző entitások közötti kapcsolatokat ábrázolják a Time Series Insights-adatokban.
* **Idősorozat-modell példány:** A példányok maguk az idősorok. A legtöbb esetben ezek a **DeviceID** vagy **AssetID,** amely a környezetben lévő eszköz egyedi azonosítója.

Ha többet szeretne megtudni a Time Series modellről, olvassa el a [Times Series Models című itt- és](./time-series-insights-update-tsm.md)

## <a name="3-hierarchy-tree-and-search-panel"></a>3. Hierarchia fa és keresőpanel

A hierarchiafa és a keresőpanel lehetővé teszi a [Time Series Model](./time-series-insights-update-tsm.md) hierarchia egyszerű keresését és navigálást, hogy megtalálja a diagramon megjeleníteni kívánt idősorozat-példányokat. A példányok kiválasztásakor azok nem csak az aktuális diagramba kerülnek, hanem jól is hozzáadódnak az adatokhoz. 

[![Hierarchiafa és keresőpanel](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png)](media/v2-update-explorer/tsi-preview-explorer-hierarchy-search.png#lightbox)

A keresési eredmények ablaktáblán hierarchianézetben vagy listanézetben is megtekintheti az eredményeket, így könnyen megtalálhatja a megjeleníteni kívánt példányokat.
 
## <a name="4-time-series-well"></a>4. Idősorok is

A jól megjeleníti a példánymezőket és a kiválasztott Time Series Insights-példányokhoz társított egyéb metaadatokat. A jobb oldalon lévő jelölőnégyzeteket bejelölve elrejtheti vagy megjelenítheti az aktuális diagram egyes példányait. 

  [![Az előnézet jól](media/v2-update-explorer/tsi-preview-ui-explorer-well.png)](media/v2-update-explorer/tsi-preview-ui-explorer-well.png#lightbox)

Az elem bal oldalán lévő piros **Törlés** (kuka) vezérlő kiválasztásával eltávolíthat bizonyos adatelemeket az aktuális adatokból. A kút azt is lehetővé teszi, hogy szabályozhatja, hogy az egyes elemek hogyan jelenjenek meg a diagramon. Választhat, hogy hozzá min / max árnyékok, adatpontok, shift az elem időben, és láthatóvá a példány egy lépcsős módon. 

Ezenkívül a Feltárásvezérlő lehetővé teszi az időeltolódások és a szórási telkek egyszerű létrehozását.  

  [![Jól elrendezési lehetőségek](media/v2-update-explorer/well-layout-options.png)](media/v2-update-explorer/well-layout-options.png#lightbox)

> [!NOTE]
> Ha a következő üzenet jelenik meg, a példány nem rendelkezik adatokkal a kiválasztott időtartam alatt. A probléma megoldásához növelje az időtartamot, vagy erősítse meg, hogy a példány adatokat ad le.
>
> ![Nincs adatértesítés](media/v2-update-explorer/tsi-preview-no-data-warning.png)

## <a name="5-chart-panel"></a>5. Diagram panel

A diagram lehetővé teszi az idősorozat-példányok sorokként való megjelenítését. A környezeti panelt, az adatmodellt és az időtartamot szabályozó panelt a webes vezérlőkre kattintva összecsukhatja. 

  [![Diagram előnézete – áttekintés](media/v2-update-explorer/tsi-preview-chart-overview.png)](media/v2-update-explorer/tsi-preview-chart-overview.png#lightbox)

1. **Diagram típusa**: Azt szabályozza, hogy mely adatelemek érhetők el a vizualizációhoz.

1. **Intervallumméret**: Az Intervallumméret csúszka lehetővé teszi az intervallumok nagyítását és kicsinyítését ugyanebben az időszakban. Ez pontosabb annektála a nagy időszeletek közötti mozgást, amely a sima trendeket mutatja le a milliszekundumos méretű szeletekig, lehetővé téve az adatok részletes, nagy felbontású vágásainak áttekintését. A csúszka alapértelmezett kezdőpontja a kiválasztott adatok legoptimálisabb nézeteként van beállítva; kiegyensúlyozó felbontás, lekérdezési sebesség és részletesség.

1. **Nagyítás és pásztázás:** Ezzel a vezérlővel nagyíthatja és pásztázhatja a diagramot.

1. **Y tengely vezérlése**: Váltás a rendelkezésre álló y-tengely nézetbeállításai között:

    * `Stacked`: Minden vonalnak egyedi Y tengelye van.
    * `Overlap`: Több sor egymásra rakható ugyanazon az Y tengelyen, és az Y tengely adatai a kijelölt vonal alapján változnak.
    * `Shared`: Az összes Y tengelyalakú adat együtt jelenik meg.

1. **Jelölőelem**: Az aktuálisan kijelölt adatelem és a hozzá tartozó részletek.

Egy adott adatszeletet úgy részletezhet, hogy bal gombbal az aktuális diagram egyik **adatpontjára kattint,** miközben lenyomva tartja az egeret, majd a kijelölt területet a kívánt végpontra húzza. **Kattintson** a jobb gombbal a kék, kijelölt területre, majd válassza a **Nagyítás** parancsot az alábbi módon. Megtekintheti és letöltheti a telemetriai eseményeket a kiválasztott időtartományban.

  [![Diagram nagyításának előnézete](media/v2-update-explorer/preview-chart-zoom.png)](media/v2-update-explorer/preview-chart-zoom.png#lightbox)

A **Nagyítás** művelet végrehajtása után megjelenik a kijelölt adatkészlet. Válassza ki azt a formátumvezérlőt, amely a Time Series Insights-adatok három y-tengely-ábrázolását szeretné végigmenni.

  [![A diagram y tengelyének előnézete](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png)](media/v2-update-explorer/tsi-preview-explorer-standard-chart.png#lightbox)

Itt egy példa egy **egymást átfedő diagramra** a következő:

  [![Egymást átfedő diagrambeállítás](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png)](media/v2-update-explorer/tsi-preview-explorer-overlapping-chart.png#lightbox)

A **További műveletek** gomb kibővülve jelenik meg a Letöltés **CSV-ként**, Csatlakozás a Power **BI-hoz**, **Diagramadatok megjelenítése táblázatként**és **A nyers események felfedezése** beállításhoz.

  [![További műveletek lehetőség](media/v2-update-explorer/more-actions-icon.png)](media/v2-update-explorer/more-actions-icon.png#lightbox)

További információ a Csatlakozás a **Power BI-hoz** beállításról a [Time Series Insights natív Power BI-összekötőjében.](concepts-power-bi.md)

## <a name="6-time-editor-panel"></a>6. Idő szerkesztő panel

Amikor a Time Series Insights-szal dolgozik, először kiválaszt egy időtartamot. A kiválasztott időtartam szabályozza a Time Series Insights frissítési widgetekkel manipuláláscéljából elérhető adatkészletet.

  [![Időkiválasztás panel](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png)](media/v2-update-explorer/tsi-preview-explorer-timeline-element.png#lightbox)

> [!TIP]
> Az idővonal egy része borostyán vagy narancssárga színnel van kiemelve, jelezve a meleg tárban elérhető adatok tartományát.

A következő webes vezérlők érhetők el a Time Series Insights frissítéskiválasztásához a munkaidő-span. 

  [![Feltárási kút ellenőrzés](media/v2-update-explorer/exploration-well-control.png)](media/v2-update-explorer/exploration-well-control.png#lightbox)

1. **Belső dátumtartomány csúszka vezérlő:** A két végpontvezérlőt úgy használhatja, hogy a kívánt időtartamon keresztül húzza őket. Ezt a belső dátumtartományt a külső dátumtartomány csúszka vezérlője korlátozza.

1. **Dátumtartomány gombjainak növelése és csökkentése:** Az időtartam növelése vagy csökkentése a kívánt intervallum hoz való bármelyik gomb kiválasztásával.

1. **Időtartomány-összecsukás vezérlő:** Ez a webes vezérlő lehetővé teszi az összes vezérlő elrejtését, kivéve a belső dátumtartomány csúszka eszközt.

1. **Külső dátumtartomány csúszka vezérlője**: A végpontvezérlőelemek segítségével válassza ki a külső dátumtartományt, amely elérhető lesz a belső dátumtartomány-vezérlőhöz.

1. **Időtartomány csúszka vezérlő:** Ezzel gyorsan válthat az előre beállított időtartam-beállítások, például az utolsó **30 perc**, az **utolsó 12 óra**vagy egy egyéni **tartomány**között. Az érték módosítása az intervallumméretű csúszka eszközben tárgyalt rendelkezésre álló intervallumtartományokat is módosítja.

   [![Kijelölt panel és kijelölés](media/v2-update-explorer/to-and-from-element.png)](media/v2-update-explorer/to-and-from-element.png#lightbox)

## <a name="7-app-bar"></a>7. Alkalmazáspult

A Time Series Insights előzetes navigációs panelje a Time Series Insights alkalmazás tetején jelenik meg. A következő funkciókat biztosítja:

### <a name="current-session-share-link-control"></a>Aktuális munkamenet-megosztási hivatkozás vezérlő

  [![Megosztás ikon](media/v2-update-explorer/tsi-preview-explorer-share-icon.png)](media/v2-update-explorer/tsi-preview-explorer-share-icon.png#lightbox)

Az új **Megosztás** ikonra kattintva megoszthatja az URL-hivatkozást a csapatával.

  [![A példány URL-címének megosztása](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png)](media/v2-update-explorer/tsi-preview-explorer-share-your-view.png#lightbox)

### <a name="tenant-section"></a>Bérlői szakasz

  [![Bérlő kiválasztása](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png)](media/v2-update-explorer/tsi-preview-explorer-tenant-selection.png#lightbox)

* Megjeleníti az aktuális Time Series Insights bejelentkezési fiók adatait.
* Segítségével válthat a rendelkezésre álló Time Series Insights-témák között.
* Ezzel megtekintheti az Előnézet [bemutató webalkalmazást.](https://insights.timeseries.azure.com/preview/demo)

### <a name="theme-selection"></a>Téma kiválasztása

Új téma kiválasztásához válassza ki a jobb felső sarokban található profilikont. Ezután válassza **a Téma módosítása**lehetőséget .

  [![Téma kiválasztása](media/v2-update-explorer/tsi-preview-theme-selection.png)](media/v2-update-explorer/tsi-preview-theme-selection.png#lightbox)

> [!TIP]
> A nyelvválasztás a profil ikonjának kiválasztásával is elérhető.

Az Azure Time Series Insights előzetes verzió két témát támogat:

* **Világos téma**: A dokumentumban megjelenő alapértelmezett téma.
* **Sötét téma**: Teszi a felfedező, ahogy itt látható:

  [![Kijelölt sötét téma](media/v2-update-explorer/tsi-preview-dark-theme-selected.png)](media/v2-update-explorer/tsi-preview-dark-theme-selected.png#lightbox)

## <a name="s1s2-environment-controls"></a>S1/S2 környezetvezérlők

### <a name="preview-terms-panel"></a>Kifejezések megtekintése panel

Ez a szakasz csak azokra a meglévő S1/S2 környezetekre vonatkozik, amelyek a frissített felhasználói felületen próbálják használni az explorert. Előfordulhat, hogy az általánosan elérhető terméket és az előzetes verziót együtt szeretné használni. A meglévő felhasználói felülettől a frissített intézőhöz hozzáadottunk néhány funkciót, de a Time Series Insights-kezelőben beszerezheti az S1/S2 környezet teljes felhasználói felületi élményét. 

A hierarchia helyett a Time Series Insights feltételek panel jelenik meg. A feltételek panel lehetővé teszi a lekérdezések definiálását a környezetben. Segítségével is szűrheti az adatokat egy predikátum alapján.

  [![Ahol a lekérdezés panel](media/v2-update-explorer/s1-s2-preview-query.png)](media/v2-update-explorer/s1-s2-preview-query.png#lightbox)

A Time Series Insights előzetes kifejezésszerkesztő panela a következő paramétereket veszi figyelembe:

**Hol:** Használja a where záradékot az események gyors szűréséhez az alábbi táblázatban felsorolt operanduskészlet használatával. Ha egy operandus kiválasztásával végez keresést, az állítmány automatikusan frissül a keresés alapján. A támogatott operandstípusok a következők:

| Művelet | Támogatott típusok   | Megjegyzések |
| --- | --- | --- |
| `<`, `>`, `<=`, `>=` | Dupla, DateTime, TimeSpan | |
| `=`, `!=`, `<>` | Karakterlánc, Bool, Dupla, DateTime, TimeSpan, NULL |
| `IN` | Karakterlánc, Bool, Dupla, DateTime, TimeSpan, NULL | Minden operandusnak azonos típusúnak vagy NULL állandónak kell lennie. |
| `HAS` | Sztring | A jobb oldalon csak állandó karakterlánc-konstansok megengedettek. Üres karakterlánc és NULL nem engedélyezett. |

Ha többet szeretne megtudni a támogatott lekérdezési műveletekről és adattípusokról, olvassa el [a Time Series Expression (TSX) című szöveget.](https://docs.microsoft.com/rest/api/time-series-insights/preview#time-series-expression-and-syntax)

### <a name="examples-of-where-clauses"></a>Példák arra, hogy hol vannak a záradékok

  [![Ahol a záradék példái](media/v2-update-explorer/tsi-preview-example-queries.png)](media/v2-update-explorer/tsi-preview-example-queries.png#lightbox)

**Mérték:** Legördülő lista, amely az aktuális diagram elemeiként használható összes numerikus oszlopot (**páros**) jeleníti meg.

**Felosztás:** Ez a legördülő lista a modellben az összes olyan kategorikus oszlopot (karakterláncot) jeleníti meg, amelyek szerint csoportosíthatja az adatokat. Legfeljebb öt kifejezést adhat hozzá, hogy ugyanabban az x tengelyen jelenjen meg. Adja meg a kívánt paramétereket, majd válassza **a Hozzáadás** lehetőséget a friss kifejezés hozzáadásához.

  [![Lekérdezett és szűrt nézet egy](media/v2-update-explorer/s1-s2-preview-filtered-view.png)](media/v2-update-explorer/s1-s2-preview-filtered-view.png#lightbox)

A diagrampanel elemeit a látható ikon kiválasztásával jelenítheti meg és rejtheti el, ahogy az az alábbi képen látható. A lekérdezések teljes eltávolításához jelölje ki a piros **X**lehetőséget.

  [![Lekérdezett és szűrt beállítás visszavonása](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png)](media/v2-update-explorer/s1-s2-preview-filtered-view-cancel.png#lightbox)

## <a name="next-steps"></a>További lépések

- Ismerje meg a tárolás és a [bejövő forgalom](./time-series-insights-update-storage-ingress.md) az Azure Time Series Insights előzetes verzióban.

- Olvassa el a Time Series Insights előzetes dokumentum [adatmodellezés](./time-series-insights-update-tsm.md).

- További információ a Time Series Insights-példány [diagnosztizálásáról és hibaelhárításáról.](./time-series-insights-update-how-to-troubleshoot.md)
