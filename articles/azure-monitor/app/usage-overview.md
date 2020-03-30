---
title: Használati elemzés az Azure Application Insights szolgáltatással | Microsoft-dokumentumok
description: Ismerje meg a felhasználókat és az alkalmazással való kapcsolatukat.
ms.topic: conceptual
ms.date: 03/25/2019
ms.openlocfilehash: e964b1b5b9d5500f2d9f24ed765299389e6dbbb9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283956"
---
# <a name="usage-analysis-with-application-insights"></a>Használatelemzés az Application Insights szolgáltatással

A web- vagy mobilalkalmazás mely funkciói a legnépszerűbbek? A felhasználók elérik céljaikat az alkalmazással? Kiesnek bizonyos pontokon, és később térnek vissza?  [Az Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) segítségével hatékony betekintést nyerhet abba, hogyan használják az emberek az alkalmazást. Minden alkalommal, amikor frissíti az alkalmazást, felmérheti, hogy mennyire működik jól a felhasználók számára. Ezzel a tudással adatvezérelt döntéseket hozhat a következő fejlesztési ciklusairól.

## <a name="send-telemetry-from-your-app"></a>Telemetriai adatok küldése az alkalmazásból

A legjobb élmény az Application Insights telepítése az alkalmazáskiszolgáló kódjában és a weblapokon. Az alkalmazás ügyfél- és kiszolgáló-összetevői telemetriai adatokat küldenek vissza az Azure Portalra elemzésre.

1. **Kiszolgáló kódja:** Telepítse a megfelelő modult a [ASP.NET,](../../azure-monitor/app/asp-net.md) [az Azure,](../../azure-monitor/app/app-insights-overview.md) [a Java,](../../azure-monitor/app/java-get-started.md) [a Node.js](../../azure-monitor/app/nodejs.md)vagy [más](../../azure-monitor/app/platforms.md) alkalmazáshoz.

    * *Nem szeretné telepíteni a kiszolgálókódot? Csak [hozzon létre egy Azure Application Insights-erőforrást.](../../azure-monitor/app/create-new-resource.md )*

2. **Weblap kódja:** A következő parancsfájl hozzáadása a weblaphoz a bezárás ``</head>``előtt . Cserélje le a instrumentation kulcsot az Application Insights-erőforrás megfelelő értékére:
    
    ```html
    <script type="text/javascript">
    var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
    {
      instrumentationKey:"INSTRUMENTATION_KEY"
    }
    );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
    </script>
    ```

    Ha többet szeretne megtudni a webhelyek figyeléséhez, olvassa el a [JavaScript SDK referenciacikket.](https://docs.microsoft.com/azure/azure-monitor/app/javascript)

3. **Mobilalkalmazás-kód:** Az App Center SDK segítségével gyűjtsön eseményeket az alkalmazásból, majd küldje el ezeknek az eseményeknek a másolatát az Application Insightsnak elemzésre [az útmutató követésével.](../../azure-monitor/learn/mobile-center-quickstart.md)

4. **Telemetriai adatok beszerezni:** Futtassa a projektet hibakeresési módban néhány percig, majd keresse meg az eredményeket az Application Insights Áttekintés panelen.

    Az alkalmazás közzététele az alkalmazás teljesítményének figyeléséhez és annak kiderítéséhez, hogy a felhasználók mit csinálnak az alkalmazással.

## <a name="include-user-and-session-id-in-your-telemetry"></a>Felhasználói és munkamenet-azonosító felvétele a telemetriai adatokba
A felhasználók idővel történő nyomon követéséhez az Application Insights megköveteli, hogy azonosítsa őket. Az Események eszköz az egyetlen olyan használati eszköz, amelyhez nincs szükség felhasználói azonosítóra vagy munkamenet-azonosítóra.

Ezzel a [folyamattal](https://docs.microsoft.com/azure/application-insights/app-insights-usage-send-user-context)kezdje el el küldeni a felhasználói és munkamenet-azonosítókat.

## <a name="explore-usage-demographics-and-statistics"></a>A használati demográfiai adatok és statisztikák felfedezése
Megtudhatja, hogy az emberek mikor használják az alkalmazást, milyen oldalak érdeklik őket a legjobban, hol találhatók a felhasználók, milyen böngészőket és operációs rendszereket használnak. 

A Felhasználók és munkamenetek jelentések oldalak vagy egyéni események szerint szűrik az adatokat, és olyan tulajdonságok szerint szegmentálják őket, mint a hely, a környezet és az oldal. Saját szűrőket is hozzáadhat.

![Felhasználók](./media/usage-overview/users.png)  

A megfelelő információk érdekes mintákat mutatnak ki az adathalmazban.  

* A **Felhasználók** jelentés megszámolja azoknak az egyedi felhasználóknak a számát, akik a kiválasztott időszakokban férnek hozzá az oldalakhoz. A webes alkalmazások esetében a felhasználók cookie-k segítségével számítanak. Ha valaki különböző böngészőkkel vagy ügyfélgépekkel fér hozzá a webhelyéhez, vagy törli a cookie-kat, akkor a rendszer többször is megszámolja.
* A **Munkamenetek** jelentés megszámolja a webhelyhez hozzáférő felhasználói munkamenetek számát. A munkamenet a felhasználó tevékenységi időszaka, amelyet fél óránál hosszabb inaktivitással szakítanak meg.

[További információk a Felhasználók, munkamenetek és események eszközökről](usage-segmentation.md)  

## <a name="retention---how-many-users-come-back"></a>Megőrzés - hány felhasználó jön vissza?

Az adatmegőrzés segít megérteni, hogy a felhasználók milyen gyakran térnek vissza az alkalmazásuk használatához, olyan felhasználók kohorszai alapján, akik bizonyos üzleti műveleteket hajtottak végre egy bizonyos időalatt. 

- Ismerje meg, hogy milyen funkciók okozzák a felhasználók at, hogy többet jöjjenek vissza, mint mások 
- Valós felhasználói adatokon alapuló hipotézisek űrlapja 
- Annak megállapítása, hogy a megőrzés problémát jelent-e a termékben 

![Megőrzés](./media/usage-overview/retention.png) 

A megőrzési vezérlők felül lehetővé teszi, hogy meghatározott események és időtartomány kiszámításához megőrzési. A középső grafikon a teljes megőrzési százalék vizuális ábrázolását adja meg a megadott időtartomány szerint. Az alsó grafikon egy adott időszakban egyedi megőrzést jelöl. Ez a részletességi szint lehetővé teszi annak megértését, hogy a felhasználók mit csinálnak, és milyen hatással lehetnek a visszatérő felhasználókra egy részletesebb részletességgel.  

[További információk a Megőrzés eszközről](usage-retention.md)

## <a name="custom-business-events"></a>Egyéni üzleti események

Ahhoz, hogy világosan megismerhesd, mit csinálnak a felhasználók az alkalmazással, érdemes kódsorokat beszúrni az egyéni események naplózásához. Ezek az események a részletes felhasználói műveletektől kezdve, például bizonyos gombokra való kattintástól a fontosabb üzleti eseményekig, például a vásárlásig vagy a játék megnyerésén át bármire képesek. 

Bár bizonyos esetekben az oldalmegtekintések hasznos eseményeket jelenthetnek, ez általában nem igaz. A felhasználó a termék megvásárlása nélkül is megnyithat egy termékoldalt. 

Adott üzleti eseményekkel a felhasználók előrehaladását a webhelyen keresztül ábrázolhatja. Megtudhatja, hogy a preferenciák a különböző lehetőségeket, és ha lemorzsolódik, vagy nehézségekbe ütközik. Ezzel a tudással megalapozott döntéseket hozhat a fejlesztési hátralék prioritásairól.

Az események az alkalmazás ügyfélfelőli oldaláról naplózhatók:

```JavaScript

    appInsights.trackEvent("ExpandDetailTab", {DetailTab: tabName});
```

Vagy a szerver oldalon:

```csharp
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("CreatedAccount", new Dictionary<string,string> {"AccountType":account.Type}, null);
    ...
    tc.TrackEvent("AddedItemToCart", new Dictionary<string,string> {"Item":item.Name}, null);
    ...
    tc.TrackEvent("CompletedPurchase");
```

Ezekhez az eseményekhez tulajdonságértékeket csatolhat, így szűrheti vagy feloszthatja az eseményeket, amikor megvizsgálja őket a portálon. Emellett minden eseményhez egy szabványos tulajdonságkészlet van csatolva, például a névtelen felhasználói azonosítóhoz, amely lehetővé teszi az egyes felhasználók tevékenységsorozatának nyomon követését.

További információ az [egyéni eseményekről](../../azure-monitor/app/api-custom-events-metrics.md#trackevent) és [tulajdonságokról.](../../azure-monitor/app/api-custom-events-metrics.md#properties)

### <a name="slice-and-dice-events"></a>Szeletelési és kockaesemények

A Felhasználók, munkamenetek és események eszközökben az egyéni eseményeket felhasználó, eseménynév és tulajdonságok szerint szeletelheti és kockázhatja.
![Felhasználók](./media/usage-overview/users.png)  
  
## <a name="design-the-telemetry-with-the-app"></a>A telemetria imitomára való megtervezése az alkalmazással

Az alkalmazás egyes funkcióinak tervezésekor gondolja át, hogyan fogja mérni a felhasználókkal való sikeres működését. Döntse el, hogy milyen üzleti eseményeket kell rögzítenie, és a kezdetektől fogva kódolja az ilyen események nyomon követési hívásait az alkalmazásba.

## <a name="a--b-testing"></a>A | B tesztelés
Ha nem tudja, hogy egy szolgáltatás melyik változata lesz sikeresebb, engedje el mindkettőt, így mindegyik elérhető a különböző felhasználók számára. Mérje meg mindegyik sikeresse, majd lépjen át egy egységes verzióra.

Ehhez a módszerhez különböző tulajdonságértékeket csatolhat az alkalmazás egyes verziói által küldött összes telemetriai értékhez. Ezt az aktív TelemetryContext tulajdonságok definiálásával teheti meg. Ezek az alapértelmezett tulajdonságok minden telemetriai üzenethez hozzáadódnak, amelyet az alkalmazás küld – nem csak az egyéni üzenetekhez, hanem a szabványos telemetriai adatokhoz is.

Az Application Insights portálon szűrje és ossza fel az adatokat a tulajdonságértékek, a különböző verziók összehasonlítása érdekében.

Ehhez [állítson be egy telemetriai inicializálót:](../../azure-monitor/app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)

**ASP.NET-alkalmazások**

```csharp
    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize(ITelemetry item)
            {
                var itemProperties = item as ISupportProperties;
                if (itemProperties != null && !itemProperties.Properties.ContainsKey("AppVersion"))
                {
                    itemProperties.Properties["AppVersion"] = "v2.1";
                }
            }
    }
```

A webes app initializer, mint például Global.asax.cs:

```csharp

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
         .Add(new MyTelemetryInitializer());
    }
```

**ASP.NET Core-alkalmazások**

> [!NOTE]
> Initializer hozzáadása `ApplicationInsights.config` használatával `TelemetryConfiguration.Active` vagy használatával nem érvényes ASP.NET Core alkalmazások. 

Az [ASP.NET core](asp-net-core.md#adding-telemetryinitializers) alkalmazások `TelemetryInitializer` hozzáadása egy új történik hozzáadásával, hogy a függőségi injektálás tároló, az alábbiak szerint. Ez történik `ConfigureServices` a módszer `Startup.cs` az osztályban.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, MyTelemetryInitializer>();
}
```

Minden új Telemetriai-ügyfél automatikusan hozzáadja a megadott tulajdonságértéket. Az egyes telemetriai események felülbírálhatják az alapértelmezett értékeket.

## <a name="next-steps"></a>További lépések
   - [Felhasználók, munkamenetek, események](usage-segmentation.md)
   - [Tölcsérek](usage-funnels.md)
   - [Megőrzés](usage-retention.md)
   - [Felhasználói folyamatok](usage-flows.md)
   - [Munkafüzetek](../../azure-monitor/app/usage-workbooks.md)
   - [Felhasználói környezet hozzáadása](usage-send-user-context.md)
