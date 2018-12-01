---
title: Használatelemzés az Azure Application Insights |} A Microsoft docs
description: Ismerje meg a felhasználók számára, és mit tesznek azt az alkalmazást.
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/10/2017
ms.pm_owner: daviste;NumberByColors
ms.reviewer: mbullwin
ms.author: daviste
ms.openlocfilehash: bc80bfacf6896161fae03b9ed93f5fe22e13ada2
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52725284"
---
# <a name="usage-analysis-with-application-insights"></a>Használatelemzés az Application Insights szolgáltatással

A webes vagy mobilalkalmazásának szolgáltatásaitól legnépszerűbb? Tegye a felhasználók a kitűzött célokat az alkalmazással? Hajtsa végre, drop adott időpontokban, és tegye vissza később?  [Az Azure Application Insights](app-insights-overview.md) segítséget nyújt a felhasználók miként használják alkalmazását hatékony elemzéseket készíthet. Minden alkalommal, amikor frissíti az alkalmazást, mérje fel arról, hogy működik a felhasználók számára. Ezt a tudást teheti az adatvezérelt döntések kapcsolatban a következő fejlesztési ciklusokat.

## <a name="send-telemetry-from-your-app"></a>Telemetria küldése az alkalmazásból

A legjobb élményt az Application Insights telepítésével az alkalmazáskód kiszolgáló, mind a weblapok kapjuk meg. Az alkalmazás ügyfél- és összetevői telemetriát küldjön az Azure Portalon elemzés céljából.

1. **Kiszolgálói kód:** az megfelelő moduljának telepítése a [ASP.NET](app-insights-asp-net.md), [Azure](app-insights-overview.md), [Java](app-insights-java-get-started.md), [Node.js](app-insights-nodejs.md), vagy [más](app-insights-platforms.md) alkalmazást.

    * *Nem szeretné telepíteni a kiszolgálói kód? Csak [hozzon létre egy Azure Application Insights-erőforrást](app-insights-create-new-resource.md).*

2. **Weblapkód:** nyissa meg a [az Azure portal](https://portal.azure.com), nyissa meg az Application Insights-erőforrást az alkalmazáshoz, és nyissa meg **első lépések > figyelése és diagnosztizálása ügyféloldali**. 

    ![Másolja a szkriptet a fő weblaphoz vezetője.](./media/app-insights-usage-overview/02-monitor-web-page.png)

3. **Mobilalkalmazás-kód:** az App Center SDK segítségével eseményeinek gyűjtése az alkalmazásból, majd elküldi ezeket az eseményeket az Application Insights által végzett elemzéshez [ezt az útmutatót](app-insights-mobile-center-quickstart.md).

4. **Telemetriai adatokat:** hibakeresési módban a projekt futtatása néhány percet, és keresse meg az Application Insights áttekintő paneljén eredményez.

    Tegye közzé az alkalmazást, az alkalmazás teljesítményének figyeléséhez, és ismerje meg, mivel foglalkoznak az a felhasználók az alkalmazását.

## <a name="include-user-and-session-id-in-your-telemetry"></a>Tartalmazza a telemetria felhasználó és a munkamenet-Azonosítót
Az Application Insights nyomon követheti a felhasználók idővel, megköveteli a azonosítani tudja azokat. Az események eszköz az egyetlen használati eszköz, amely nem igényel felhasználói azonosító vagy egy munkamenet-azonosítót.

Felhasználó és a munkamenet-azonosítók használatával küldésének megkezdése [Ez a folyamat](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context).

## <a name="explore-usage-demographics-and-statistics"></a>Ismerkedés a demográfiai használat és statisztikák
Ismerje meg, amikor a felhasználók használják az alkalmazást, azok leginkább érdeklő, hol találhatók a felhasználók milyen lapok, mely böngészőket és operációs rendszereket használnak. 

A felhasználók és munkamenetek jelentések oldalak vagy egyéni események az adatok szűrése és szegmentálása azok tulajdonságok, mint például hely, a környezet és a lap által. A saját szűrőket is hozzáadhat.

![Felhasználók](./media/app-insights-usage-overview/users.png)  

A jobb oldali insights érdekes szabályszerűségeket az adatkészlet mutasson.  

* A **felhasználók** jelentés száma a kiválasztott időtartamon belül a lapok használó egyedi felhasználók számát. A web apps a felhasználók a cookie-k használatával bájtjai számítanak. Ha valaki hozzáfér a hely különböző böngészők vagy az ügyfélgépek, vagy törli a cookie-kat, majd azok beleszámítanak egynél többször.
* A **munkamenetek** jelentés számolja meg a felhasználói munkamenetek, amelyek hozzáférhetnek a webhelyhez. A munkamenet a felhasználó megszakította a több mint fél óra inaktivitási tevékenysége bizonyos.

[További információ a felhasználók, munkamenetek és események eszközök](app-insights-usage-segmentation.md)  

## <a name="page-views"></a>Lapmegtekintések

A használati panelen kattintson a legnépszerűbb lap bontásban beolvasni a lapmegtekintések csempe keresztül:

![Az Áttekintés panelen kattintson a lap nézetek diagram](./media/app-insights-usage-overview/05-games.png)

A fenti példa a játékok webhelyről. A diagramok és azt azonnal tekintheti meg:

* Használat az elmúlt héten nem javult. Talán azt kell gondolniuk a keresés keresőmotor-optimalizálás?
* Tenisz a legnépszerűbb játékok lapon. Koncentráljunk a ezen a lapon további fejlesztései.
* Az átlagos felhasználók oldalán tenisz körülbelül három alkalommal hetente. (Nincsenek, mint a felhasználók körülbelül három alkalommal további előadások.)
* A legtöbb felhasználó keresse fel a webhelyet, az Egyesült Államok működő hétre esik-e, és a munkaidő alatt. Például a "gyorsan elrejtése" gombra kell biztosítunk a weblapon.
* A [jegyzetek](app-insights-annotations.md) a diagram megjelenítése, amikor új verziókat a webhely üzembe helyezése. A legutóbbi telepítéseket egyike észrevehető hatást a használat.

Mi történik, ha vizsgálni kívánt a forgalmának kiszolgálására részletesebb leírását – például a felosztás a lapmegtekintési telemetriát küld a hely egy egyéni tulajdonság szerint?

1. Nyissa meg a **események** eszközt az Application Insights-erőforrás menüben. Ez az eszköz hány lapmegtekintés és egyéni eseményeket az alkalmazásból, számos szűrési, cohorting és Szegmentálás lehetőség alapján küldött elemzését teszi lehetővé.
2. A "Ki használt" legördülő listában válassza ki a "Bármely lapmegtekintés".
3. A "Split szerint" legördülő listában válassza ki a lapmegtekintési telemetriát felosztása egy tulajdonságot.

## <a name="retention---how-many-users-come-back"></a>Megőrzés – hány felhasználók visszatérő használóivá?

Adatmegőrzési segítségével megismerheti, milyen gyakran adja vissza a felhasználók használni az alkalmazást, a felhasználók által végrehajtott bizonyos üzleti művelet során egy bizonyos idő gyűjtő kohorszok alapján. 

- Milyen funkciók felhasználóktól származnak, vissza több, mint a többi ismertetése 
- Űrlap feltételezéseket valós felhasználói adatok alapján 
- Ellenőrizze, hogy megőrzési a termékkel kapcsolatos probléma 

![Megőrzés](./media/app-insights-usage-overview/retention.png) 

Felül megőrzési vezérlők lehetővé teszik adott események és időtartomány alapján számítja ki a megőrzési határozhatja meg. A grafikon a középső vizuális megjelenítését, a teljes megőrzési százalékos aránya a megadott időtartományban biztosít. A diagram alján egyéni megőrzési jelöli egy adott időszakban. A részletesség ilyen szintje lehetővé teszi a felhasználók mire, és milyen hatással lehet egy részletesebb granularitási vracející felhasználójához.  

[További információ a megtartási eszköz](app-insights-usage-retention.md)

## <a name="custom-business-events"></a>Egyéni üzleti eseményeket

Mit a felhasználók az alkalmazását egyértelművé lekéréséhez hasznos beszúrása kódsorokat egyéni eseményeket. Ezek az események követheti nyomon a részletes felhasználói műveletek, például az adott gombokkal, a jelentősebb üzleti eseményeket, mint például a vásárlás vagy a játék lettek a winning semmit. 

Bár bizonyos esetekben a lapmegtekintések hasznos események hozhat létre, nem igaz általában. Egy felhasználó egy termékoldalán nyissa meg a termék vásárlása nélkül is. 

Az adott üzleti eseményeket a folyamat a felhasználók diagram is a webhelyen keresztül. Meg is preferenciáik különböző lehetőségeket talál, és, ha azok a drop ki vagy nehézségekbe. Ezt a tudást a teheti a fejlesztési mappájában várakozó fájlok számát a prioritásokkal kapcsolatos megalapozott döntéseket hozhasson.

Események az alkalmazás az ügyféloldalról rögzíthető:

```JavaScript

    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

Vagy a kiszolgáló oldalán:

```csharp
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

Csatlakoztathat tulajdonságértékek ezeket az eseményeket, hogy szűrhető és az eseményeket, amikor a portálon ellenőrizze őket osztani. Emellett egy szabványos tulajdonságkészlettel minden egyes esemény, például a névtelen felhasználó azonosítója, amely lehetővé teszi, hogy követni a tevékenységek egy egyéni felhasználó csatolva van.

Tudjon meg többet [egyéni események](app-insights-api-custom-events-metrics.md#trackevent) és [tulajdonságok](app-insights-api-custom-events-metrics.md#properties).

### <a name="slice-and-dice-events"></a>Szeletelésére és feldarabolására használnak események

A felhasználók, munkamenetek és események Tools szeletelni, és a felhasználó, esemény neve és tulajdonságai egyéni események menően.
![Felhasználók](./media/app-insights-usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>Az alkalmazás a telemetriai adatok megtervezése

Tervezésekor az alkalmazás egyes szolgáltatásokhoz, fontolja meg, hogyan kívánja a sikeresség a felhasználóival. Döntse el, milyen üzleti eseményeket rögzíteni kell, és a követési események az alkalmazásba hívásainak a kezdetektől kódot.

## <a name="a--b-testing"></a>A |} B tesztelés
Ha nem tudja, melyik változatot egy szolgáltatás több sikeres lesz, kiadási őket, így minden elérhető különböző felhasználók. Az egyes sikerének mérését, és helyezze át egy egyesített verzió.

Ezzel a technikával a csatlakoztat különböző tulajdonságértékek minden egyes verziója, az alkalmazás által küldött telemetriát. Amely az aktív TelemetryContext a tulajdonságok megadásával teheti meg. Minden telemetriai üzenetet küld az alkalmazás - nem csak az egyéni üzenetek, de a normál telemetriai alapértelmezett ezeket a tulajdonságokat kerülnek.

Az Application Insights portálon szűrése, és az adatok a tulajdonság értékekhez, úgy, hogy a különböző verziók összehasonlítása felosztása.

Ehhez [beállítása egy telemetriainicializáló](app-insights-api-filtering-sampling.md##add-properties-itelemetryinitializer):

```csharp


    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize (ITelemetry telemetry)
        {
            telemetry.Properties["AppVersion"] = "v2.1";
        }
    }
```

A webes alkalmazást inicializáló Global.asax.cs például:

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```

Minden új TelemetryClients automatikusan hozzáadja a megadott tulajdonság értéke. Egyéni telemetriaeseményeknek felülbírálhatja az alapértelmezett értékeket.

## <a name="next-steps"></a>További lépések
   - [Felhasználók, munkamenetek, események](app-insights-usage-segmentation.md)
   - [Tölcsérek](usage-funnels.md)
   - [Megőrzés](app-insights-usage-retention.md)
   - [Felhasználói folyamatok](app-insights-usage-flows.md)
   - [Munkafüzetek](app-insights-usage-workbooks.md)
   - [Adja hozzá a felhasználói környezet](app-insights-usage-send-user-context.md)
