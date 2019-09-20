---
title: Application Insights JavaScript-webappokhoz | Microsoft Docs
description: Lekérheti a lapmegtekintések és a munkamenetek számát, a webes ügyfél adatait, és nyomon követheti a használati mintákat. Kivételeket és teljesítményproblémákat észlelhet a JavaScript weblapokon.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 3b710d09-6ab4-4004-b26a-4fa840039500
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: mbullwin
ms.openlocfilehash: 21a68c1daa3c7a2ab6689a72e23100be7582de1e
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71162188"
---
# <a name="application-insights-for-web-pages"></a>Application Insights weblapokhoz

Megismerheti a weblap vagy alkalmazás teljesítményét és használatát. Ha [Application Insightst](app-insights-overview.md) ad hozzá az oldal-parancsfájlhoz, az oldal betöltésének és az Ajax-hívásoknak, a számoknak, valamint a böngészőbeli kivételek és az Ajax-hibák részleteinek, valamint a felhasználók és a munkamenetek számát is megtalálhatja. Ezek mindegyike szegmentálható lap, ügyfél operációs rendszere és böngészőverziója, földrajzi hely és más dimenziók alapján. Beállíthat riasztásokat a hibaszámokról és a lassú lapbetöltésekről. Ha nyomkövetési hívásokat szúr be a JavaScript-kódba, nyomon követheti a webalkalmazás különböző szolgáltatásainak használati módját is.

Az Application Insights bármely weblappal használható – csak egy rövid JavaScript-kódot kell hozzáadnia. Ha a webszolgáltatása [Java](java-get-started.md) vagy [ASP.net](asp-net.md), az ügyféloldali JavaScript SDK-val együtt használhatja a kiszolgálóoldali SDK-kat az alkalmazás teljesítményének teljes körű megismeréséhez.

## <a name="adding-the-javascript-sdk"></a>A JavaScript SDK hozzáadása

1. Először Application Insights erőforrásra van szüksége. Ha még nem rendelkezik erőforrás-és kialakítási kulccsal, kövesse az [új erőforrás létrehozása című témakört](create-new-resource.md).
2. Másolja a kialakítási kulcsot abból az erőforrásból, ahová a JavaScript-telemetria elküldeni kívánja.
3. Adja hozzá a Application Insights JavaScript SDK-t a weboldalához vagy az alkalmazáshoz az alábbi két lehetőség egyikének használatával:
    * [NPM-telepítő](#npm-based-setup)
    * [JavaScript-kódrészlet](#snippet-based-setup)

> [!IMPORTANT]
> Az alábbi módszerek egyikét kell használnia ahhoz, hogy hozzáadja a Application Insights JavaScript SDK-t az alkalmazáshoz. Ha a NPM-alapú telepítést használja, ne használja a kódrészlet-alapú telepítőt. Ugyanez vonatkozik a fordított forgatókönyvre, ha a kódrészlet-alapú megközelítést használja, ne használja a NPM-alapú telepítőt is. 

### <a name="npm-based-setup"></a>NPM-alapú telepítés

```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
```

### <a name="snippet-based-setup"></a>Kódrészlet-alapú telepítés

Ha az alkalmazás nem használja az NPM-t, akkor közvetlenül a weblapjait az egyes lapok tetején található kódrészlettel Application Insightshatja. Lehetséges, hogy a `<head>` szakasz első szkriptje legyen, amely képes figyelni az összes függőségével kapcsolatos esetleges problémákat.

```html
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
{
  instrumentationKey:"INSTRUMENTATION_KEY"
}
);window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="sending-telemetry-to-the-azure-portal"></a>Telemetria küldése a Azure Portalba

Alapértelmezés szerint a Application Insights JavaScript SDK autogyűjt számos telemetria elemet, amelyek hasznosak lehetnek az alkalmazás állapotának meghatározásához és a mögöttes felhasználói élményhez. Ezek a következők:

- Nem **kezelt kivételek** az alkalmazásban, beleértve a következő információkat:
    - Verem nyomkövetése
    - Kivétel részletei és a hibát kísérő üzenet
    - Hiba a sorban & oszlop száma
    - A hiba előfordulásának URL-címe
- Az **alkalmazás által** használt **hálózati függőségi kérelmek** és a **beolvasás** (a beolvasási gyűjtemény alapértelmezés szerint le van tiltva) kérelmek, információk befoglalása
    - A függőség forrásának URL-címe
    - A függőség igénylésére szolgáló parancs & metódus
    - A kérelem időtartama
    - Az eredmény kódja és a kérelem sikerességi állapota
    - A kérelmet készítő felhasználó azonosítója (ha van)
    - Korrelációs környezet (ha van), ahol a kérelem készül
- **Felhasználói adatok** (például hely, hálózat, IP)
- **Eszköz adatai** (például böngésző, operációs rendszer, verzió, nyelv, megoldás, modell)
- **Munkamenet-információk**

### <a name="telemetry-initializers"></a>Telemetria inicializálók
A telemetria inicializálók segítségével módosítható a begyűjtött telemetria tartalma, mielőtt a felhasználó böngészőjéből elküldjék. Azt is felhasználhatják, hogy bizonyos telemetria ne küldjön vissza `false`. Több telemetria-inicializáló is felvehető a Application Insights-példányba, és ezeket a rendszer a hozzáadásuk sorrendjében hajtja végre.

A bemenet argumentuma `addTelemetryInitializer` egy olyan visszahívás, amely [`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API.md#addTelemetryInitializer) argumentumként fogadja el a függvényt `void`, és visszaadja a vagy a `boolean` értéket. Ha visszatér `false`, a rendszer nem küldi el az telemetria elemet, ellenkező esetben a következő telemetria-inicializálást (ha van ilyen), vagy pedig a telemetria-gyűjtemény végpontjának küldi el.

Példa telemetria inicializálók használatára:
```ts
var telemetryInitializer = (envelope) => {
  envelope.data.someField = 'This item passed through my telemetry initializer';
};
appInsights.addTelemetryInitializer(telemetryInitializer);
appInsights.trackTrace({message: 'This message will use a telemetry initializer'});

appInsights.addTelemetryInitializer(() => false); // Nothing is sent after this is executed
appInsights.trackTrace({message: 'this message will not be sent'}); // Not sent
```
## <a name="configuration"></a>Konfiguráció
A legtöbb konfigurációs mező neve úgy van elnevezve, hogy a hamis értékre legyenek kiválasztva. Az összes mező megadása nem kötelező `instrumentationKey`, kivéve a következőt:.

| Name (Név) | Alapértelmezett | Leírás |
|------|---------|-------------|
| InstrumentationKey | null | **Kötelező**<br>A Azure Portaltól beszerzett kialakítási kulcs. |
| accountId | null | Egy nem kötelező fiókazonosító, ha az alkalmazás a felhasználókat fiókokba csoportosítja. Nincsenek szóközök, vesszők, pontosvesszők, egyenlők vagy függőleges sávok |
| sessionRenewalMs | 1800000 | A rendszer naplózza a munkamenetet, ha a felhasználó ennél az időtartamnál ezredmásodpercben inaktív. Az alapértelmezett érték 30 perc |
| sessionExpirationMs | 86400000 | A rendszer naplózza a munkamenetet, ha ez az időtartam ezredmásodpercben továbbra is fennáll. Az alapértelmezett érték 24 óra |
| maxBatchSizeInBytes | 10000 | A telemetria-köteg maximális mérete. Ha egy köteg túllépi ezt a korlátot, a rendszer azonnal elküldi, és új köteget indít el |
| maxBatchInterval | 15 000 | Mennyi ideig kell a Batch telemetria a küldés előtt (ezredmásodperc) |
| disableExceptionTracking | false | Ha az értéke igaz, a rendszer nem gyűjti össze a kivételeket. Az alapértelmezett érték a false (hamis). |
| disableTelemetry | false | Ha az értéke igaz, a rendszer nem gyűjti és nem továbbítja a telemetria. Az alapértelmezett érték a false (hamis). |
| enableDebug | false | Ha az értéke igaz, a rendszer az SDK-naplózási beállításoktól függetlenül kivételt okoz a **belső** hibakeresési adatvesztés **helyett** . Az alapértelmezett érték a false (hamis). <br>***Megjegyzés:*** Ha ez a beállítás engedélyezve van, a rendszer elveti a telemetria, amikor belső hiba történik. Ez hasznos lehet a konfigurációval vagy az SDK használatával kapcsolatos problémák gyors azonosításához. Ha nem kívánja elveszíteni a telemetria a hibakeresés során, `consoleLoggingLevel` érdemes `telemetryLoggingLevel` lehet a `enableDebug`vagy a helyett használni. |
| loggingLevelConsole | 0 | **Belső** Application Insights hibák naplózása a konzolon. <br>0: kikapcsolva, <br>1: Csak kritikus hibák, <br>2: Mindent (hibák & figyelmeztetés) |
| loggingLevelTelemetry | 1 | **Belső** Application Insights hibákat küld a telemetria. <br>0: kikapcsolva, <br>1: Csak kritikus hibák, <br>2: Mindent (hibák & figyelmeztetés) |
| diagnosticLogInterval | 10000 | belső A belső naplózási várólista lekérdezési időköze (MS) |
| samplingPercentage | 100 | Az elküldeni kívánt események százalékos aránya. Az alapértelmezett érték 100, ami azt jelenti, hogy az összes esemény el lesz küldve. Állítsa be ezt, ha nagy méretű alkalmazásokhoz szeretné megőrizni az adatkorlátot. |
| autoTrackPageVisitTime | false | Ha az értéke igaz, a rendszer nyomon követi az előző műszeres lap megtekintési idejét, és telemetria, és új időzítőt indít el az aktuális oldalmegtekintéshez. Az alapértelmezett érték a false (hamis). |
| disableAjaxTracking | false | Ha az értéke igaz, a rendszer nem gyűjti az Ajax-hívásokat. Az alapértelmezett érték a false (hamis). |
| disableFetchTracking | true | Ha az értéke igaz, a rendszer nem gyűjti be a kérelmeket. Az alapértelmezett érték TRUE (igaz) |
| overridePageViewDuration | false | Ha az értéke TRUE (igaz), a rendszer a trackPageView alapértelmezett viselkedését az oldal nézet időtartamának végére rögzíti, amikor a trackPageView hívása történik. Ha hamis, és nem ad meg egyéni időtartamot a trackPageView, a rendszer a navigációs időzítési API használatával számítja ki az oldal nézetének teljesítményét. Az alapértelmezett érték a false (hamis). |
| maxAjaxCallsPerView | 500 | Alapértelmezett 500 – meghatározza, hogy hány AJAX-hívást fog figyelni az oldal nézetében. Az-1 értékre állítva az összes (korlátlan) AJAX-hívást a lapon figyelheti. |
| disableDataLossAnalysis | true | Hamis érték esetén a rendszer a belső telemetria-küldő puffereket a még el nem küldött elemek indításakor ellenőrzi. |
| disableCorrelationHeaders | false | Hamis érték esetén az SDK két fejlécet ("Request-id" és "kérés-környezet") hoz létre az összes függőségi kérelemhez, hogy azok összekapcsolják őket a kiszolgálói oldalon található megfelelő kérelmekkel. Az alapértelmezett érték a false (hamis). |
| correlationHeaderExcludedDomains |  | A korrelációs fejlécek letiltása adott tartományokban |
| correlationHeaderDomains |  | Korrelációs fejlécek engedélyezése adott tartományokhoz |
| disableFlushOnBeforeUnload | false | Alapértelmezett hamis érték. Ha az értéke TRUE (igaz), a flush metódus nem lesz meghívva, ha a onBeforeUnload esemény-eseményindítók |
| enableSessionStorageBuffer | true | Alapértelmezett érték: true (igaz). Ha az értéke igaz, a rendszer az összes el nem juttatott telemetria rendelkező puffert tárolja a munkamenet-tárolóban. A rendszer visszaállítja a puffert az oldal betöltésekor |
| isCookieUseDisabled | false | Alapértelmezett hamis érték. Ha az érték TRUE (igaz), az SDK nem tárol és nem olvas be semmilyen cookie-t.|
| cookieDomain | null | Egyéni cookie-tartomány. Ez akkor hasznos, ha Application Insights cookie-kat szeretne megosztani altartományokon keresztül. |
| isRetryDisabled | false | Alapértelmezett hamis érték. Ha hamis, próbálkozzon újra 206 (részleges siker), 408 (időtúllépés), 429 (túl sok kérés), 500 (belső kiszolgálóhiba), 503 (a szolgáltatás nem érhető el) és 0 (offline, csak ha észlelhető) |
| isStorageUseDisabled | false | Ha az érték TRUE (igaz), az SDK nem tárolja és nem olvassa be a helyi és munkamenet-tárolóból származó összes adatforrást. Az alapértelmezett érték a false (hamis). |
| isBeaconApiDisabled | true | Ha hamis, az SDK az összes telemetria elküldi a [Beacon API](https://www.w3.org/TR/beacon) használatával |
| onunloadDisableBeacon | false | Alapértelmezett hamis érték. Ha a TAB be van zárva, az SDK az összes fennmaradó telemetria elküldi a [Beacon API](https://www.w3.org/TR/beacon) használatával. |
| sdkExtension | null | Beállítja az SDK-bővítmény nevét. Csak alfabetikus karakterek engedélyezettek. A bővítmény neve előtagként szerepel az "Ai. internal. sdkVersion" címkében (például "ext_javascript: 2.0.0"). Az alapértelmezett érték null. |
| isBrowserLinkTrackingEnabled | false | Az alapértelmezett érték a false (hamis). Ha az érték TRUE (igaz), az SDK nyomon fogja követni az összes [böngészőbeli hivatkozás](https://docs.microsoft.com/aspnet/core/client-side/using-browserlink) kérését. |
| appId | null | A AppId az AJAX-függőségek közötti korrelációt használja a kiszolgálóoldali kérelmekkel az ügyfélen. Ha a Beacon API engedélyezve van, nem használható automatikusan, de manuálisan is beállítható a konfigurációban. Az alapértelmezett érték null |
| enableCorsCorrelation | false | Ha az értéke igaz, az SDK két fejlécet ("Request-id" és "Request-Context") ad hozzá az összes CORS-kérelemhez a kimenő AJAX-függőségek összekapcsolásához a kiszolgálói oldalon található megfelelő kérelmekkel. Az alapértelmezett érték false (hamis) |
| namePrefix | nem definiált | Egy nem kötelezően megadandó érték, amely a localStorage és a cookie neveként a Postfix nevet fogja használni.
| enableAutoRouteTracking | false | Az útvonalak változásainak automatikus követése egyoldalas alkalmazásokban (SPA). Ha az érték TRUE (igaz), akkor minden útvonal változása egy új oldalmegtekintést küld Application Insightsnak. A kivonatoló útvonalak változásai`example.com/foo#bar`() új oldalletöltésekként is rögzítve lesznek.
| enableRequestHeaderTracking | false | Igaz értéke esetén az AJAX & a lekérési kérelmek fejlécének nyomon követése, az alapértelmezett érték a false.
| enableResponseHeaderTracking | false | Igaz értéke esetén a rendszer az AJAX & beolvasási kérelem válaszának fejléceit nyomon követi, az alapértelmezett érték a false.
| distributedTracingMode | `DistributedTracingModes.AI` | Beállítja az elosztott nyomkövetési módot. Ha a AI_AND_W3C mód vagy a W3C mód be van állítva, a rendszer a W3C nyomkövetési környezet fejléceit (traceparent/tracestate) hozza létre és tartalmazza az összes kimenő kérelemben. A AI_AND_W3C a visszamenőleges kompatibilitást biztosít bármely örökölt Application Insights által biztosított szolgáltatással.

## <a name="single-page-applications"></a>Egyoldalas alkalmazások

Alapértelmezés szerint ez az SDK **nem** fogja kezelni az egyoldalas alkalmazásokban megjelenő, az állapot-alapú útvonalak módosítását. Az egyoldalas alkalmazás automatikus útvonal-módosítási nyomon követésének engedélyezéséhez `enableAutoRouteTracking: true` hozzáadhat a telepítési konfigurációhoz.

Jelenleg egy külön, az SDK-val inicializálható [beépülő modult](#react-extensions) is kínálunk. Emellett az útvonal-változások nyomon követését is végrehajtja az Ön számára, valamint [más reagáló specifikus telemetria](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)is gyűjthet.

## <a name="react-extensions"></a>Reakciós bővítmények

| Bővítmények |
|---------------|
| [Reagálni](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)|
| [Natív reagálás](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-native/README.md)|

## <a name="explore-browserclient-side-data"></a>Böngésző-és ügyféloldali adat megismerése

A böngésző-és ügyféloldali adatokat a **metrikák** segítségével tekintheti meg, és hozzáadhatja az Önt érdeklő egyéni metrikákat: 

![](./media/javascript/page-view-load-time.png)

Az adatait a JavaScript SDK-ból is megtekintheti a portálon elérhető böngésző használatával.

Válassza a **böngésző** lehetőséget, majd válassza a **hibák** vagy a **teljesítmény**lehetőséget.

![](./media/javascript/browser.png)

### <a name="performance"></a>Teljesítmény 

![](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>Függőségek

![](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>Elemzés 

A JavaScript SDK által gyűjtött telemetria lekéréséhez válassza a **megtekintés a naplókban (elemzés)** gombot. `where` Egy`client_Type == "Browser"`utasítás hozzáadásával csak a JavaScript SDK-ból és más SDK-k által gyűjtött kiszolgálóoldali telemetria fog adatokat kizárni.
 
```kusto
// average pageView duration by name
let timeGrain=5m;
let dataset=pageViews
// additional filters can be applied here
| where timestamp > ago(1d)
| where client_Type == "Browser" ;
// calculate average pageView duration for all pageViews
dataset
| summarize avg(duration) by bin(timestamp, timeGrain)
| extend pageView='Overall'
// render result in a chart
| render timechart
```

### <a name="source-map-support"></a>Forrás-hozzárendelés támogatása

A kivétel telemetria minified hívási verem lehet unminified a Azure Portal. A kivétel részletei panelen lévő összes meglévő integráció az újonnan unminified hívási verem fog működni. A forrás-hozzárendelési leképezés eltávolítása a meglévő és a jövőbeli JS SDK-kat (+ node. JS) is támogatja, így nem kell frissítenie az SDK verzióját. A nem minified hívási verem megtekintéséhez
1. Válasszon ki egy kivételt telemetria elemet a Azure Portal a "végpontok közötti tranzakció részletei" megtekintéséhez.
2. Azonosítsa, hogy mely forrás-térképek felelnek meg ehhez a hívási veremnek. A forrás-hozzárendelésnek egyeznie kell egy verem-keret forrásfájljait, de utótagja`.map`
3. Húzza át a forrást a hívási verembe a Azure Portal![](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Application Insights webes alapszintű

A könnyű használhatóság érdekében telepítheti az Application Insights alapszintű verzióját is
```
npm i --save @microsoft/applicationinsights-web-basic
```
Ez a verzió a szolgáltatások és funkciók minimális számát tartalmazza, és arra támaszkodik, hogy felépítse azt, ahogy jónak látja. Például nem hajt végre autocollectiont (nem kezelt kivételeket, AJAX-t stb.). Az egyes telemetria-típusok ( `trackTrace` `trackException`például stb.) küldésére szolgáló API-k nem szerepelnek ebben a verzióban, ezért meg kell adnia a saját burkolóját. Az egyetlen elérhető `track`API. Itt található egy [minta](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html) .

## <a name="examples"></a>Példák

A futtatható-példákat lásd: [Application Insights JavaScript SDK-minták](https://github.com/topics/applicationinsights-js-demo)

## <a name="upgrading-from-the-old-version-of-application-insights"></a>Frissítés a Application Insights korábbi verziójáról

Az SDK v2 verziójában feltört változások:
- A jobb API-aláírások engedélyezéséhez néhány API-hívás, például a trackPageView, a trackException frissítése megtörtént. A böngésző IE8-ban vagy alacsonyabb verzióban való futtatása nem támogatott.
- Az telemetria-borítékban az Adatséma frissítései miatt a mező neve és szerkezete módosul.
- Áthelyezve a `context.operation` `context.telemetryTrace`következő helyre:. Egyes mezők is módosultak (`operation.id`) --> `telemetryTrace.traceID`
  - Ha manuálisan szeretné frissíteni az aktuális oldalmegtekintési azonosítót (például a SPA-alkalmazásokban), ezt a következővel végezheti el:`appInsights.properties.context.telemetryTrace.traceID = Util.newId()`

Ha az aktuális Application bepillantást előállító SDK-t (1.0.20) használja, és szeretné megtekinteni, hogy az új SDK működik-e futásidőben, frissítse az URL-címet az aktuális SDK-betöltési forgatókönyvtől függően.

- Letöltés CDN-forgatókönyv használatával: Frissítse a jelenleg használt kódrészletet a következő URL-címre való Rámutatás céljából:
   ```
   "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
   ```

- NPM forgatókönyv: Hívja `downloadAndSetup` meg a teljes ApplicationInsights-szkript letöltését a CDN-ből, és inicializálja a kialakítási kulccsal:

   ```ts
   appInsights.downloadAndSetup({
     instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
     url: "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
     });
   ```

Tesztelés belső környezetben a figyelés telemetria ellenőrzéséhez a várt módon működik. Ha minden működik, frissítse API-aláírásait az SDK v2 verziójára, és telepítse az üzemi környezetekben.

## <a name="sdk-performanceoverhead"></a>SDK-teljesítmény/terhelés

Ha mindössze 25 KB-os tömörített, és csak ~ 15 MS-ot szeretne inicializálni, a Application Insights elhanyagolható mennyiségű loadtime hoz létre a webhelyhez. A kódrészlet használatával a könyvtár minimális összetevői gyorsan betöltődik. Addig a teljes szkript le lesz töltve a háttérben.

A parancsfájl a CDN-ből való letöltését követően az oldal összes nyomon követését várólistára helyezi. Miután a letöltött parancsfájl aszinkron módon inicializálja az inicializálást, a rendszer a várólistára helyezett összes eseményt nyomon követte. Ennek eredményeképpen az oldal teljes életciklusa során semmilyen telemetria nem fog elveszíteni. Ez a telepítési folyamat zökkenőmentes analitikai rendszerrel rendelkező oldalt biztosít a felhasználók számára.

> Összefoglalás:
> - **25 KB** tömörített
> - **15 MS** teljes inicializálási idő
> - A lap életciklusa során kimaradt **nulla** követés

## <a name="browser-support"></a>Böngésző-támogatás

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Opera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
Legújabb ✔ | Legújabb ✔ | 9 + ✔ | Legújabb ✔ | Legújabb ✔ |

## <a name="open-source-sdk"></a>Open-source SDK

A Application Insights JavaScript SDK nyílt forráskódú a forráskód megtekintéséhez vagy a projekthez való hozzájáruláshoz, látogasson el a [hivatalos GitHub-tárházba](https://github.com/Microsoft/ApplicationInsights-JS).

## <a name="next"></a> Következő lépések
* [Használat követése](usage-overview.md)
* [Egyéni események és a mérőszámok](api-custom-events-metrics.md)
* [Összeállítás, mérés, tanulás](usage-overview.md)

