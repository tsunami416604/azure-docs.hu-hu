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
ms.openlocfilehash: f2539d5250ff436a720fe10f748f40db29b0ee25
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54118523"
---
# <a name="usage-analysis-with-application-insights"></a>Használatelemzés az Application Insights szolgáltatással

A webes vagy mobilalkalmazásának szolgáltatásaitól legnépszerűbb? Tegye a felhasználók a kitűzött célokat az alkalmazással? Hajtsa végre, drop adott időpontokban, és tegye vissza később?  [Az Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) segítséget nyújt a felhasználók miként használják alkalmazását hatékony elemzéseket készíthet. Minden alkalommal, amikor frissíti az alkalmazást, mérje fel arról, hogy működik a felhasználók számára. Ezt a tudást teheti az adatvezérelt döntések kapcsolatban a következő fejlesztési ciklusokat.

## <a name="send-telemetry-from-your-app"></a>Telemetria küldése az alkalmazásból

A legjobb élményt az Application Insights telepítésével az alkalmazáskód kiszolgáló, mind a weblapok kapjuk meg. Az alkalmazás ügyfél- és összetevői telemetriát küldjön az Azure Portalon elemzés céljából.

1. **Kiszolgálóoldali kódban:** A megfelelő moduljának telepítése a [ASP.NET](../../azure-monitor/app/asp-net.md), [Azure](../../azure-monitor/app/app-insights-overview.md), [Java](../../azure-monitor/app/java-get-started.md), [Node.js](../../azure-monitor/app/nodejs.md), vagy [más](../../azure-monitor/app/platforms.md) alkalmazás.

    * *Nem szeretné telepíteni a kiszolgálói kód? Csak [hozzon létre egy Azure Application Insights-erőforrást](../../azure-monitor/app/create-new-resource.md ).*

2. **Weblap-kódot:** Adja hozzá a következő szkriptet a weblapra a Bezárás előtt ``</head>``. Cserélje le a megfelelő értéket, az Application Insights-erőforrás kialakítási kulcsát:

   ```javascript
      <script type="text/javascript">
        var appInsights=window.appInsights||function(a){
            function b(a){c[a]=function(){var b=arguments;c.queue.push(function(){c[a].apply(c,b)})}}var c={config:a},d=document,e=window;setTimeout(function(){var b=d.createElement("script");b.src=a.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js",d.getElementsByTagName("script")[0].parentNode.appendChild(b)});try{c.cookie=d.cookie}catch(a){}c.queue=[];for(var f=["Event","Exception","Metric","PageView","Trace","Dependency"];f.length;)b("track"+f.pop());if(b("setAuthenticatedUserContext"),b("clearAuthenticatedUserContext"),b("startTrackEvent"),b("stopTrackEvent"),b("startTrackPage"),b("stopTrackPage"),b("flush"),!a.disableExceptionTracking){f="onerror",b("_"+f);var g=e[f];e[f]=function(a,b,d,e,h){var i=g&&g(a,b,d,e,h);return!0!==i&&c["_"+f](a,b,d,e,h),i}}return c
        }({
            instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx"
        });
        
        window.appInsights=appInsights,appInsights.queue&&0===appInsights.queue.length&&appInsights.trackPageView();
    </script>
    ```
    A webhelyek monitorozásával kapcsolatos speciális konfigurációkról a [JavaScript SDK API-referenciáiban](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md) olvashat.

3. **Mobilalkalmazás-kód:** Az App Center SDK segítségével eseményeinek gyűjtése az alkalmazásból, majd elküldi ezeket az eseményeket az Application Insights által végzett elemzéshez [ezt az útmutatót](../../azure-monitor/learn/mobile-center-quickstart.md).

4. **Máris:** A projekt hibakeresési módban futtatja a néhány percet, és keresse meg az Application Insights áttekintő paneljén eredményez.

    Tegye közzé az alkalmazást, az alkalmazás teljesítményének figyeléséhez, és ismerje meg, mivel foglalkoznak az a felhasználók az alkalmazását.

## <a name="include-user-and-session-id-in-your-telemetry"></a>Tartalmazza a telemetria felhasználó és a munkamenet-Azonosítót
Az Application Insights nyomon követheti a felhasználók idővel, megköveteli a azonosítani tudja azokat. Az események eszköz az egyetlen használati eszköz, amely nem igényel felhasználói azonosító vagy egy munkamenet-azonosítót.

Felhasználó és a munkamenet-azonosítók használatával küldésének megkezdése [Ez a folyamat](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context).

## <a name="explore-usage-demographics-and-statistics"></a>Ismerkedés a demográfiai használat és statisztikák
Ismerje meg, amikor a felhasználók használják az alkalmazást, azok leginkább érdeklő, hol találhatók a felhasználók milyen lapok, mely böngészőket és operációs rendszereket használnak. 

A felhasználók és munkamenetek jelentések oldalak vagy egyéni események az adatok szűrése és szegmentálása azok tulajdonságok, mint például hely, a környezet és a lap által. A saját szűrőket is hozzáadhat.

![Felhasználók](./media/usage-overview/users.png)  

A jobb oldali insights érdekes szabályszerűségeket az adatkészlet mutasson.  

* A **felhasználók** jelentés száma a kiválasztott időtartamon belül a lapok használó egyedi felhasználók számát. A web apps a felhasználók a cookie-k használatával bájtjai számítanak. Ha valaki hozzáfér a hely különböző böngészők vagy az ügyfélgépek, vagy törli a cookie-kat, majd azok beleszámítanak egynél többször.
* A **munkamenetek** jelentés számolja meg a felhasználói munkamenetek, amelyek hozzáférhetnek a webhelyhez. A munkamenet a felhasználó megszakította a több mint fél óra inaktivitási tevékenysége bizonyos.

[További információ a felhasználók, munkamenetek és események eszközök](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>Megőrzés – hány felhasználók visszatérő használóivá?

Adatmegőrzési segítségével megismerheti, milyen gyakran adja vissza a felhasználók használni az alkalmazást, a felhasználók által végrehajtott bizonyos üzleti művelet során egy bizonyos idő gyűjtő kohorszok alapján. 

- Milyen funkciók felhasználóktól származnak, vissza több, mint a többi ismertetése 
- Űrlap feltételezéseket valós felhasználói adatok alapján 
- Ellenőrizze, hogy megőrzési a termékkel kapcsolatos probléma 

![Megőrzés](./media/usage-overview/retention.png) 

Felül megőrzési vezérlők lehetővé teszik adott események és időtartomány alapján számítja ki a megőrzési határozhatja meg. A grafikon a középső vizuális megjelenítését, a teljes megőrzési százalékos aránya a megadott időtartományban biztosít. A diagram alján egyéni megőrzési jelöli egy adott időszakban. A részletesség ilyen szintje lehetővé teszi a felhasználók mire, és milyen hatással lehet egy részletesebb granularitási vracející felhasználójához.  

[További információ a megtartási eszköz](usage-retention.md)

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

Tudjon meg többet [egyéni események](../../azure-monitor/app/api-custom-events-metrics.md#trackevent) és [tulajdonságok](../../azure-monitor/app/api-custom-events-metrics.md#properties).

### <a name="slice-and-dice-events"></a>Szeletelésére és feldarabolására használnak események

A felhasználók, munkamenetek és események Tools szeletelni, és a felhasználó, esemény neve és tulajdonságai egyéni események menően.
![Felhasználók](./media/usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>Az alkalmazás a telemetriai adatok megtervezése

Tervezésekor az alkalmazás egyes szolgáltatásokhoz, fontolja meg, hogyan kívánja a sikeresség a felhasználóival. Döntse el, milyen üzleti eseményeket rögzíteni kell, és a követési események az alkalmazásba hívásainak a kezdetektől kódot.

## <a name="a--b-testing"></a>A |} B tesztelés
Ha nem tudja, melyik változatot egy szolgáltatás több sikeres lesz, kiadási őket, így minden elérhető különböző felhasználók. Az egyes sikerének mérését, és helyezze át egy egyesített verzió.

Ezzel a technikával a csatlakoztat különböző tulajdonságértékek minden egyes verziója, az alkalmazás által küldött telemetriát. Amely az aktív TelemetryContext a tulajdonságok megadásával teheti meg. Minden telemetriai üzenetet küld az alkalmazás - nem csak az egyéni üzenetek, de a normál telemetriai alapértelmezett ezeket a tulajdonságokat kerülnek.

Az Application Insights portálon szűrése, és az adatok a tulajdonság értékekhez, úgy, hogy a különböző verziók összehasonlítása felosztása.

Ehhez [beállítása egy telemetriainicializáló](../../azure-monitor/app/api-filtering-sampling.md##add-properties-itelemetryinitializer):

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
   - [Felhasználók, munkamenetek, események](usage-segmentation.md)
   - [Tölcsérek](usage-funnels.md)
   - [Megőrzés](usage-retention.md)
   - [Felhasználói folyamatok](usage-flows.md)
   - [Munkafüzetek](../../azure-monitor/app/usage-workbooks.md)
   - [Adja hozzá a felhasználói környezet](usage-send-user-context.md)
