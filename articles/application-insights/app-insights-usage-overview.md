---
title: "Használatelemzés az Azure Application insights szolgáltatással |} Microsoft docs"
description: "Ismerje meg, a felhasználók és a dolgukat az alkalmazással."
services: application-insights
documentationcenter: 
author: botatoes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 10/10/2017
ms.author: mbullwin
ms.openlocfilehash: 57d9ebc5a9689a6f1d48464aa20ffdc7fa61b00f
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="usage-analysis-with-application-insights"></a>Használatelemzés az Application Insights szolgáltatással

A web-vagy mobilalkalmazás mely funkciók érhetők a legnépszerűbb? Hajtsa végre a felhasználók a kitűzött célokat a alkalmazással? Tegye azokat dobja el adott pontokon, és tegye ezeket térjen vissza később?  [Az Azure Application Insights](app-insights-overview.md) használatának az alkalmazás hatékony betekintést nyújt segítséget. Minden alkalommal, amikor az alkalmazást frissíti, felmérheti, milyen jól működik a felhasználók számára. A Tudásbázis következő fejlesztési ciklusokkal kapcsolatos döntések adatvezérelt végezheti el.

## <a name="send-telemetry-from-your-app"></a>Telemetriai adatokat küldhet az alkalmazásból

A legjobb élményt egységekre Application Insights telepítésével, az alkalmazás server kódját, mind a weblapokon. Az alkalmazás ügyfél- és összetevők vissza az Azure-portál a elemzés a telemetriai adatokat küldhet.

1. **Kiszolgálóoldali kódban:** telepítse a megfelelő modulját a [ASP.NET](app-insights-asp-net.md), [Azure](app-insights-azure.md), [Java](app-insights-java-get-started.md), [Node.js](app-insights-nodejs.md), vagy [más](app-insights-platforms.md) alkalmazást.

    * *Nem szeretné telepíteni a kiszolgálóoldali kódban? Csak [hozzon létre egy Azure Application Insights-erőforrást](app-insights-create-new-resource.md).*

2. **Weblap kód:** nyissa meg a [Azure-portálon](https://portal.azure.com), és nyissa meg az Application Insights-erőforrást az alkalmazáshoz, majd nyissa meg a **első lépések > figyelése és diagnosztizálása ügyféloldali**. 

    ![Másolja a parancsfájlt a fő weblap head.](./media/app-insights-usage-overview/02-monitor-web-page.png)

3. **Mobilalkalmazás-kódot:** a Mobile Center SDK segítségével események gyűjtése az alkalmazásból, majd elküldi ezeket az eseményeket az Application Insights analízis [az útmutató követése](app-insights-mobile-center-quickstart.md).

4. **Telemetriai adatokat:** futtassa a projekt hibakeresési módban néhány percig, és keresse meg az Application Insights – áttekintés paneljén eredményez.

    Tegye közzé az alkalmazást, az az alkalmazás teljesítményének figyelése, és kideríti, hogy a felhasználók tevékenységeit az alkalmazással.

## <a name="include-user-and-session-id-in-your-telemetry"></a>Tartalmazza a felhasználó és a munkamenet-Azonosítót a telemetria
Felhasználók nyomon követése adott idő alatt, az Application Insights előírja, hogy azonosítani tudja azokat. Az események eszköz a csak használati eszköz, amely nem igényel felhasználói azonosító vagy munkamenet-azonosítót.

Felhasználó és a munkamenet-azonosítók használatával küldésének megkezdése [Ez a folyamat](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context).

## <a name="explore-usage-demographics-and-statistics"></a>Megismerkedhet a használati demográfiai adatok és statisztikák
Ismerje meg az alkalmazás használatakor a személyek, azok még legtöbb érdekli, ahol a felhasználók találhatók oldalak, milyen böngészők és operációs rendszerek használnak. 

A felhasználók és a munkamenetek jelentések szűrje az adatokat lapok vagy egyéni események, és szegmentálja őket például hely, a környezet és a lap tulajdonságai. A saját szűrőt is hozzáadhat.

![Felhasználók](./media/app-insights-usage-overview/users.png)  

A jobb oldali insights mutasson, hogy érdekes szabályszerűségeket készletében lévő adatok.  

* A **felhasználók** jelentés megjeleníti a kiválasztott időtartamon belül a lapok hozzáférő egyedi felhasználók számát. A web Apps a felhasználók számlálási cookie-k használatával. Ha valaki hozzáfér a webhelyet a különböző böngészők vagy ügyfélszámítógépre, vagy törli a cookie-k, majd azok megszámlálandó egynél többször.
* A **munkamenetek** jelentés megszámlálása felhasználói munkamenetek, amelyek hozzáférhetnek a webhelyhez. A munkamenet a felhasználó megszakította a több mint fél óra inaktivitási tevékenység belül.

[További információ a felhasználók, a munkamenetek és az események eszközök](app-insights-usage-segmentation.md)  

## <a name="page-views"></a>Lapmegtekintések

A használati paneljén kattintson a lapmegtekintések csempén a legnépszerűbb lap részletes információkat:

![Áttekintés paneljén kattintson a lap nézetek diagram](./media/app-insights-usage-overview/05-games.png)

A fenti példa egy játékok webhelyről. A diagramok azonnal láthatja:

* Használati az elmúlt héten nem javult. Lehet, hogy meg kell fontolnunk a optimalizálás érdekében?
* Tenisz a legnépszerűbb játékok lapon. Ez a lap további fejlesztései lehetővé összpontosítanak.
* Átlagosan felhasználók által felkeresett a tenisz lap készül háromszor hetente. (Nincsenek felhasználók mint három-szer több munkamenetek.)
* A legtöbb felhasználó keresse fel a webhelyet, az USA működő héten, és a munkaidő alatt. Lehet, hogy a "gyors elrejtése" gombra kell nyújtunk a weblapon.
* A [jegyzetek](app-insights-annotations.md) a diagram megjelenítése, ha telepítve vannak-e az új verziókat a webhely. A legutóbbi központi telepítések egyike észrevehető hatást használata.

Mi történik, ha vizsgálni kívánt részletesen, például egy egyéni tulajdonság, a lap nézet telemetriai adatokat küldi el a hely által a felosztás webhelyekhez forgalom?

1. Nyissa meg a **események** eszköz az Application Insights-erőforrás menüjében. Ez az eszköz lehetővé teszi, hogy hány Lapmegtekintések és egyéni események az alkalmazásból, a különböző szűrési, cohorting és Szegmentálás beállítások alapján küldött elemezheti.
2. A "Ki használt" legördülő listában jelölje ki "Any lap nézet".
3. A "Által megosztott" legördülő listában válassza ki ossza fel a lap nézet telemetriai egy tulajdonságot.

## <a name="retention---how-many-users-come-back"></a>Megőrzési - hány felhasználó térjen vissza?

Megőrzési segítségével megismerheti, hogy milyen gyakran térjen vissza a felhasználók az alkalmazással, a felhasználók által végrehajtott bizonyos üzleti művelet során egy bizonyos idő gyűjtőben cohorts alapján. 

- Milyen funkciók miatt a felhasználók számára, térjen vissza több, mint a többire ismertetése 
- Űrlap feltételezéseket valós felhasználói adatok alapján 
- Annak megállapítása, hogy megőrzési a termékkel kapcsolatos probléma 

![Megőrzés](./media/app-insights-usage-overview/retention.png) 

A megőrzési vezérlők felül lehetővé teszik meg adott események és időtartomány megőrzési kiszámításához. A diagram a középső által az időtartomány lett megadva az összes megőrzési százalékos vizuális ábrázolását biztosítja. A grafikon alján egy adott időszakra vonatkozó egyéni megőrzési jelöli. Részletesség ilyen szintje lehetővé teszi, hogy a felhasználók tevékenységeit, és milyen hatással lehetnek a részletesebb lépésköz adatszolgáltató felhasználók megértését.  

[További információ a megőrzési eszköz](app-insights-usage-retention.md)

## <a name="custom-business-events"></a>Egyéni üzleti események

Ahhoz, hogy pontosan ismeri a felhasználók mit az alkalmazással, akkor célszerű beszúrása sornyi kód egyéni események naplózása. Ezeket az eseményeket is nyomon semmit részletes felhasználói műveletek, például adott gombokkal, például vásárol, vagy egy játékot győzelmével több jelentős üzleti eseményeket. 

Bár bizonyos esetekben a lapmegtekintések hasznos események jelenthet, nem igaz általában. A felhasználó megnyithatja a termék oldalát a termék vásárlás nélkül. 

Az adott üzleti eseményeket a webhelyen keresztül is diagram a felhasználók folyamatban van. Megtudhatja, a beállítások a különböző lehetőségek közül, és beállíthatja, amennyiben azok dobja el vagy megszüntetésekor nehézségekbe. Ennek az információnak a tehet a fejlesztési várakozó fájlok számát a prioritásokkal kapcsolatos kérdésekre vonatkozó döntések.

Az alkalmazás ügyféloldali bejelentkezhet események:

```JavaScript

    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

Vagy a kiszolgáló oldalán:

```C#
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

Ezeket az eseményeket, hogy szűrje, vagy ossza fel az eseményeket, amikor a portál vizsgálhatja csatolhat tulajdonságértékek. Emellett minden esemény, például a névtelen felhasználói Azonosítóját, amely lehetővé teszi egy adott felhasználó-tevékenységek sorrendjének követéséhez tulajdonságok szabványos készletét van csatolva.

További információ [egyéni események](app-insights-api-custom-events-metrics.md#trackevent) és [tulajdonságok](app-insights-api-custom-events-metrics.md#properties).

### <a name="slice-and-dice-events"></a>Szeletelésére és feldarabolására használnak események

A felhasználók, a munkamenetek és az események eszközök akkor is részletekbe menően felhasználó, az esemény neve és a Tulajdonságok egyéni események.
![Felhasználók](./media/app-insights-usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>A telemetriai adatok és az alkalmazás tervezése

Az alkalmazás egyes szolgáltatások tervez, vegye figyelembe hogyan kívánja a sikeresség felméréséhez a felhasználóival. Döntse el, milyen rögzítendő az üzleti eseményeket, és a követési ezeket az eseményeket az alkalmazásba hívásokat a kezdetektől kódot.

## <a name="a--b-testing"></a>A |} B tesztelés
Ha nem tudja, melyik variant egy szolgáltatás több sikeres lesz, a kiadási helyezni őket, így minden elérhető különböző felhasználók. Az egyes alkalmazása sikerének mérésében, és anélkül helyezhet át egy egységes verziót.

Ezzel a módszerrel a csatol különböző tulajdonságértékek minden verzió az alkalmazás által küldött összes telemetriai. Azt az aktív TelemetryContext a tulajdonságok megadásával teheti meg. Minden telemetriai üzenet, amely elküldi az alkalmazás - nem csak az egyéni üzenetek, de a szabványos telemetriai ezek alapértelmezett tulajdonságokkal is bővül.

Az Application Insights portálon szűréséhez, és ossza fel a tulajdonságértékek esetén, hogy különböző hasonlítsa össze az adatokat.

Ehhez a [állítson be egy telemetriai inicializáló](app-insights-api-filtering-sampling.md##add-properties-itelemetryinitializer):

```C#


    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize (ITelemetry telemetry)
        {
            telemetry.Properties["AppVersion"] = "v2.1";
        }
    }
```

A webes alkalmazás inicializáló Global.asax.cs például:

```C#

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```

Minden új TelemetryClients automatikusan hozzáadja a megadott tulajdonság értéke. Egyéni telemetriai események felülbírálhatja az alapértelmezett értékeket.

## <a name="next-steps"></a>Következő lépések
   - [Felhasználók, munkamenetek, események](app-insights-usage-segmentation.md)
   - [Tölcsérek](usage-funnels.md)
   - [Megőrzés](app-insights-usage-retention.md)
   - [Felhasználói folyamatok](app-insights-usage-flows.md)
   - [Munkafüzetek](app-insights-usage-workbooks.md)
   - [Felhasználói környezet hozzáadása](app-insights-usage-send-user-context.md)
