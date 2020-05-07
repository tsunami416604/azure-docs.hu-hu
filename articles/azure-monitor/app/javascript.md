---
title: Azure-Application Insights JavaScript-alapú webalkalmazásokhoz
description: Megtekintheti az oldal nézetét és a munkamenetek számát, a webes ügyfél adatait, az egyoldalas alkalmazásokat (SPA) és a használati mintákat. Kivételeket és teljesítményproblémákat észlelhet a JavaScript weblapokon.
ms.topic: conceptual
author: Dawgfan
ms.author: mmcc
ms.date: 09/20/2019
ms.openlocfilehash: 50ce0d57ec7395c69bf65e41b67f0cb005a43cb8
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82854977"
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
> A JavaScript SDK-t csak egy módszer használatával adhatja hozzá az alkalmazáshoz. Ha a NPM telepítőjét használja, ne használja a kódrészletet, és fordítva.

> [!NOTE]
> A NPM telepítője a JavaScript SDK-t a projekttől való függőségként telepíti, az IntelliSense engedélyezésével, míg a kódrészlet futásidőben beolvassa az SDK-t. Mindkettő támogatja ugyanazt a funkciót. Azok a fejlesztők azonban, akik több egyéni eseményt és konfigurációt kívánnak használni, általában a NPM beállítását választják, míg a felhasználóknak a beépített webes elemzések gyors engedélyezését keresik.

### <a name="npm-based-setup"></a>NPM-alapú telepítés

```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView(); // Manually call trackPageView to establish the current user/session/pageview
```

### <a name="snippet-based-setup"></a>Kódrészlet-alapú telepítés

Ha az alkalmazás nem használja az NPM-t, akkor közvetlenül a weblapjait az egyes lapok tetején található kódrészlettel Application Insightshatja. Lehetséges, hogy a `<head>` szakasz első szkriptje legyen, amely képes figyelni az összes függőségével kapcsolatos esetleges problémákat. Ha a Blazer Server alkalmazást használja, adja hozzá a kódrészletet a (z) `_Host.cshtml` `<head>` szakaszban található fájl elejéhez.

```html
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(n){var o={config:n,initialize:!0},t=document,e=window,i="script";setTimeout(function(){var e=t.createElement(i);e.src=n.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",t.getElementsByTagName(i)[0].parentNode.appendChild(e)});try{o.cookie=t.cookie}catch(e){}function a(n){o[n]=function(){var e=arguments;o.queue.push(function(){o[n].apply(o,e)})}}o.queue=[],o.version=2;for(var s=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];s.length;)a("track"+s.pop());var r="Track",c=r+"Page";a("start"+c),a("stop"+c);var u=r+"Event";if(a("start"+u),a("stop"+u),a("addTelemetryInitializer"),a("setAuthenticatedUserContext"),a("clearAuthenticatedUserContext"),a("flush"),o.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===n.disableExceptionTracking||n.extensionConfig&&n.extensionConfig.ApplicationInsightsAnalytics&&!0===n.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){a("_"+(s="onerror"));var p=e[s];e[s]=function(e,n,t,i,a){var r=p&&p(e,n,t,i,a);return!0!==r&&o["_"+s]({message:e,url:n,lineNumber:t,columnNumber:i,error:a}),r},n.autoExceptionInstrumented=!0}return o}(
{
  instrumentationKey:"INSTRUMENTATION_KEY"
}
);(window[aiName]=aisdk).queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
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
- **Eszköz adatai** (például böngésző, operációs rendszer, verzió, nyelv, modell)
- **A munkamenetadatokat**

### <a name="telemetry-initializers"></a>Telemetria inicializálók
A telemetria inicializálók segítségével módosítható a begyűjtött telemetria tartalma, mielőtt a felhasználó böngészőjéből elküldjék. Azt is felhasználhatják, hogy bizonyos telemetria ne küldjön vissza `false`. Több telemetria-inicializáló is felvehető a Application Insights-példányba, és ezeket a rendszer a hozzáadásuk sorrendjében hajtja végre.

A bemenet argumentuma `addTelemetryInitializer` egy olyan visszahívás, amely [`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API-reference.md#addTelemetryInitializer) argumentumként fogadja el a függvényt, és visszaadja a vagy `boolean` `void`a értéket. Ha visszatér `false`, a rendszer nem küldi el az telemetria elemet, ellenkező esetben a következő telemetria-inicializálást (ha van ilyen), vagy pedig a telemetria-gyűjtemény végpontjának küldi el.

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

| Name | Alapértelmezett | Leírás |
|------|---------|-------------|
| instrumentationKey | null | **Szükséges**<br>A Azure Portaltól beszerzett kialakítási kulcs. |
| accountId | null | Egy nem kötelező fiókazonosító, ha az alkalmazás a felhasználókat fiókokba csoportosítja. Nincsenek szóközök, vesszők, pontosvesszők, egyenlők vagy függőleges sávok |
| sessionRenewalMs | 1800000 | A rendszer naplózza a munkamenetet, ha a felhasználó ennél az időtartamnál ezredmásodpercben inaktív. Az alapértelmezett érték 30 perc |
| sessionExpirationMs | 86400000 | A rendszer naplózza a munkamenetet, ha ez az időtartam ezredmásodpercben továbbra is fennáll. Az alapértelmezett érték 24 óra |
| maxBatchSizeInBytes | 10000 | A telemetria-köteg maximális mérete. Ha egy köteg túllépi ezt a korlátot, a rendszer azonnal elküldi, és új köteget indít el |
| maxBatchInterval | 15 000 | Mennyi ideig kell a Batch telemetria a küldés előtt (ezredmásodperc) |
| disableExceptionTracking | hamis | Ha az értéke igaz, a rendszer nem gyűjti össze a kivételeket. Az alapértelmezett érték a false (hamis). |
| disableTelemetry | hamis | Ha az értéke igaz, a rendszer nem gyűjti és nem továbbítja a telemetria. Az alapértelmezett érték a false (hamis). |
| enableDebug | hamis | Ha az értéke igaz, a rendszer az SDK-naplózási beállításoktól függetlenül kivételt okoz a **belső** hibakeresési adatvesztés **helyett** . Az alapértelmezett érték a false (hamis). <br>***Megjegyzés:*** Ha ez a beállítás engedélyezve van, a rendszer elveti a telemetria, amikor belső hiba történik. Ez hasznos lehet a konfigurációval vagy az SDK használatával kapcsolatos problémák gyors azonosításához. Ha nem kívánja elveszíteni a telemetria a hibakeresés során, `consoleLoggingLevel` érdemes `telemetryLoggingLevel` lehet a `enableDebug`vagy a helyett használni. |
| loggingLevelConsole | 0 | **Belső** Application Insights hibák naplózása a konzolon. <br>0: kikapcsolva, <br>1: csak kritikus hibák, <br>2: minden (hibák & figyelmeztetés) |
| loggingLevelTelemetry | 1 | **Belső** Application Insights hibákat küld a telemetria. <br>0: kikapcsolva, <br>1: csak kritikus hibák, <br>2: minden (hibák & figyelmeztetés) |
| diagnosticLogInterval | 10000 | belső A belső naplózási várólista lekérdezési időköze (MS) |
| samplingPercentage | 100 | Az elküldeni kívánt események százalékos aránya. Az alapértelmezett érték 100, ami azt jelenti, hogy az összes esemény el lesz küldve. Állítsa be ezt, ha nagy méretű alkalmazásokhoz szeretné megőrizni az adatkorlátot. |
| autoTrackPageVisitTime | hamis | Ha az értéke igaz, a rendszer nyomon követi az előző műszeres lap megtekintési idejét, és telemetria, és új időzítőt indít el az aktuális oldalmegtekintéshez. Az alapértelmezett érték a false (hamis). |
| disableAjaxTracking | hamis | Ha az értéke igaz, a rendszer nem gyűjti az Ajax-hívásokat. Az alapértelmezett érték a false (hamis). |
| disableFetchTracking | igaz | Ha az értéke igaz, a rendszer nem gyűjti be a kérelmeket. Az alapértelmezett érték TRUE (igaz) |
| overridePageViewDuration | hamis | Ha az értéke TRUE (igaz), a rendszer a trackPageView alapértelmezett viselkedését az oldal nézet időtartamának végére rögzíti, amikor a trackPageView hívása történik. Ha hamis, és nem ad meg egyéni időtartamot a trackPageView, a rendszer a navigációs időzítési API használatával számítja ki az oldal nézetének teljesítményét. Az alapértelmezett érték a false (hamis). |
| maxAjaxCallsPerView | 500 | Alapértelmezett 500 – meghatározza, hogy hány AJAX-hívást fog figyelni az oldal nézetében. Az-1 értékre állítva az összes (korlátlan) AJAX-hívást a lapon figyelheti. |
| disableDataLossAnalysis | igaz | Hamis érték esetén a rendszer a belső telemetria-küldő puffereket a még el nem küldött elemek indításakor ellenőrzi. |
| disableCorrelationHeaders | hamis | Hamis érték esetén az SDK két fejlécet ("Request-id" és "kérés-környezet") hoz létre az összes függőségi kérelemhez, hogy azok összekapcsolják őket a kiszolgálói oldalon található megfelelő kérelmekkel. Az alapértelmezett érték a false (hamis). |
| correlationHeaderExcludedDomains |  | A korrelációs fejlécek letiltása adott tartományokban |
| correlationHeaderDomains |  | Korrelációs fejlécek engedélyezése adott tartományokhoz |
| disableFlushOnBeforeUnload | hamis | Alapértelmezett hamis érték. Ha az értéke TRUE (igaz), a flush metódus nem lesz meghívva, ha a onBeforeUnload esemény-eseményindítók |
| enableSessionStorageBuffer | igaz | Alapértelmezett érték: true (igaz). Ha az értéke igaz, a rendszer az összes el nem juttatott telemetria rendelkező puffert tárolja a munkamenet-tárolóban. A rendszer visszaállítja a puffert az oldal betöltésekor |
| isCookieUseDisabled | hamis | Alapértelmezett hamis érték. Ha az érték TRUE (igaz), az SDK nem tárol és nem olvas be semmilyen cookie-t.|
| cookieDomain | null | Egyéni cookie-tartomány. Ez akkor hasznos, ha Application Insights cookie-kat szeretne megosztani altartományokon keresztül. |
| isRetryDisabled | hamis | Alapértelmezett hamis érték. Ha hamis, próbálkozzon újra 206 (részleges siker), 408 (időtúllépés), 429 (túl sok kérés), 500 (belső kiszolgálóhiba), 503 (a szolgáltatás nem érhető el) és 0 (offline, csak ha észlelhető) |
| isStorageUseDisabled | hamis | Ha az érték TRUE (igaz), az SDK nem tárolja és nem olvassa be a helyi és munkamenet-tárolóból származó összes adatforrást. Az alapértelmezett érték a false (hamis). |
| isBeaconApiDisabled | igaz | Ha hamis, az SDK az összes telemetria elküldi a [Beacon API](https://www.w3.org/TR/beacon) használatával |
| onunloadDisableBeacon | hamis | Alapértelmezett hamis érték. Ha a TAB be van zárva, az SDK az összes fennmaradó telemetria elküldi a [Beacon API](https://www.w3.org/TR/beacon) használatával. |
| sdkExtension | null | Beállítja az SDK-bővítmény nevét. Csak alfabetikus karakterek engedélyezettek. A bővítmény neve előtagként szerepel az "Ai. internal. sdkVersion" címkében (például "ext_javascript: 2.0.0"). Az alapértelmezett érték null. |
| isBrowserLinkTrackingEnabled | hamis | Az alapértelmezett érték a false (hamis). Ha az érték TRUE (igaz), az SDK nyomon fogja követni az összes [böngészőbeli hivatkozás](https://docs.microsoft.com/aspnet/core/client-side/using-browserlink) kérését. |
| appId | null | A AppId az AJAX-függőségek közötti korrelációt használja a kiszolgálóoldali kérelmekkel az ügyfélen. Ha a Beacon API engedélyezve van, nem használható automatikusan, de manuálisan is beállítható a konfigurációban. Az alapértelmezett érték null |
| enableCorsCorrelation | hamis | Ha az értéke igaz, az SDK két fejlécet ("Request-id" és "Request-Context") ad hozzá az összes CORS-kérelemhez a kimenő AJAX-függőségek összekapcsolásához a kiszolgálói oldalon található megfelelő kérelmekkel. Az alapértelmezett érték false (hamis) |
| namePrefix | nem definiált | Egy nem kötelezően megadandó érték, amely a localStorage és a cookie neveként a Postfix nevet fogja használni.
| enableAutoRouteTracking | hamis | Az útvonalak változásainak automatikus követése egyoldalas alkalmazásokban (SPA). Ha az érték TRUE (igaz), akkor minden útvonal változása egy új oldalmegtekintést küld Application Insightsnak. A kivonatoló útvonalak változásai`example.com/foo#bar`() új oldalletöltésekként is rögzítve lesznek.
| enableRequestHeaderTracking | hamis | Igaz értéke esetén az AJAX & a lekérési kérelmek fejlécének nyomon követése, az alapértelmezett érték a false.
| enableResponseHeaderTracking | hamis | Igaz értéke esetén a rendszer az AJAX & beolvasási kérelem válaszának fejléceit nyomon követi, az alapértelmezett érték a false.
| distributedTracingMode | `DistributedTracingModes.AI` | Beállítja az elosztott nyomkövetési módot. Ha AI_AND_W3C mód vagy W3C mód van beállítva, a rendszer a W3C nyomkövetési környezet fejléceit (traceparent/tracestate) hozza létre és tartalmazza az összes kimenő kérelemben. AI_AND_W3C biztosítva a visszamenőleges kompatibilitáshoz bármely örökölt Application Insights által biztosított szolgáltatással.

## <a name="single-page-applications"></a>Egyoldalas alkalmazások

Alapértelmezés szerint ez az SDK **nem** fogja kezelni az egyoldalas alkalmazásokban megjelenő, az állapot-alapú útvonalak módosítását. Az egyoldalas alkalmazás automatikus útvonal-módosítási nyomon követésének engedélyezéséhez `enableAutoRouteTracking: true` hozzáadhat a telepítési konfigurációhoz.

Jelenleg egy külön [reakciós beépülő modult](#react-extensions)is kínálunk, amelyet az SDK-val inicializálhat. Emellett az útvonal-változások nyomon követését is végrehajtja az Ön számára, valamint [más reagáló specifikus telemetria](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)is gyűjthet.

> [!NOTE]
> Csak `enableAutoRouteTracking: true` akkor használja, ha **nem** az reagáló beépülő modult használja. Mindkettő képes az útvonal változásakor új oldalmegtekintések küldésére. Ha mindkettő engedélyezve van, a rendszer duplikált oldalmegtekintéseket küldhet.

## <a name="configuration-autotrackpagevisittime"></a>Konfiguráció: autoTrackPageVisitTime

A beállítás `autoTrackPageVisitTime: true`szerint a felhasználó által az egyes lapokon töltött idő nyomon követhető. Minden egyes új Oldalmegtekintésnél az *előző* oldalon eltöltött felhasználói időtartamot [Egyéni metrikaként](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-custom-overview) kell elküldeni `PageVisitTime`. Ez az egyéni metrika a [Metrikaböngésző](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) "napló alapú metrika" néven látható.

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

A JavaScript SDK által gyűjtött telemetria lekéréséhez válassza a **megtekintés a naplókban (elemzés)** gombot. Egy `where` utasítás `client_Type == "Browser"`hozzáadásával csak a JavaScript SDK-ból és más SDK-k által gyűjtött kiszolgálóoldali telemetria fog adatokat kizárni.
 
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

A kivétel telemetria minified hívási verem lehet unminified a Azure Portal. A kivétel részletei panelen lévő összes meglévő integráció az újonnan unminified hívási verem fog működni.

#### <a name="link-to-blob-storage-account"></a>BLOB Storage-fiókra mutató hivatkozás

A Application Insights-erőforrást saját Azure Blob Storage-tárolóhoz csatolhatja, hogy automatikusan felhasználja a hívási veremet. Az első lépésekhez tekintse meg az [automatikus forrás-hozzárendelés támogatását](./source-map-support.md)ismertető témakört.

### <a name="drag-and-drop"></a>Drag and drop

1. Válasszon ki egy kivételt telemetria elemet a Azure Portal a "végpontok közötti tranzakció részletei" megtekintéséhez.
2. Azonosítsa, hogy mely forrás-térképek felelnek meg ehhez a hívási veremnek. A forrás-hozzárendelésnek egyeznie kell egy verem-keret forrásfájljait, de utótagja`.map`
3. Húzza át a forrást a hívási verembe a Azure Portal![](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Application Insights webes alapszintű

A könnyű használhatóság érdekében telepítheti az Application Insights alapszintű verzióját is
```
npm i --save @microsoft/applicationinsights-web-basic
```
Ez a verzió a szolgáltatások és funkciók minimális számát tartalmazza, és arra támaszkodik, hogy felépítse azt, ahogy jónak látja. Például nem hajt végre autocollectiont (nem kezelt kivételeket, AJAX-t stb.). Az egyes telemetria-típusok ( `trackTrace` `trackException`például stb.) küldésére szolgáló API-k nem szerepelnek ebben a verzióban, ezért meg kell adnia a saját burkolóját. Az egyetlen elérhető API `track`. Itt található egy [minta](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html) .

## <a name="examples"></a>Példák

A futtatható-példákat lásd: [Application Insights JavaScript SDK-minták](https://github.com/topics/applicationinsights-js-demo)

## <a name="upgrading-from-the-old-version-of-application-insights"></a>Frissítés a Application Insights korábbi verziójáról

Az SDK v2 verziójában feltört változások:
- A jobb API-aláírások engedélyezéséhez néhány API-hívás (például a trackPageView és a trackException) frissült. Az Internet Explorer 8 és a böngésző korábbi verziói nem támogatottak.
- Az telemetria-borítékban az Adatséma frissítései miatt a mező neve és szerkezete módosul.
- Áthelyezve a `context.operation` `context.telemetryTrace`következő helyre:. Egyes mezők is módosultak (`operation.id` --> `telemetryTrace.traceID`).
  - Az aktuális oldalmegtekintési azonosító (például a SPA-alkalmazások) manuális frissítéséhez használja `appInsights.properties.context.telemetryTrace.traceID = Util.generateW3CId()`a következőt:.
    > [!NOTE]
    > A nyomkövetési azonosító egyediként való megtartásához, `Util.newId()`ahol korábban már `Util.generateW3CId()`használta, használja a következőt:. Mindkét esetben végül a művelet azonosítója jelenik meg.

Ha az aktuális Application bepillantást előállító SDK-t (1.0.20) használja, és szeretné megtekinteni, hogy az új SDK működik-e futásidőben, frissítse az URL-címet az aktuális SDK-betöltési forgatókönyvtől függően.

- Letöltés CDN-forgatókönyv használatával: frissítse a jelenleg használt kódrészletet a következő URL-címre való Rámutatás céljából:
   ```
   "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
   ```

- NPM-forgatókönyv: `downloadAndSetup` meghívja a teljes ApplicationInsights-szkript letöltését a CDN-ből, és inicializálja a kialakítási kulccsal:

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

> Összegzés:
> - **25 KB** tömörített
> - **15 MS** teljes inicializálási idő
> - A lap életciklusa során kimaradt **nulla** követés

## <a name="browser-support"></a>Böngészőtámogatás

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Operát](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
A Chrome legújabb ✔ |  Firefox legújabb ✔ | IE 9 + & Edge ✔ | Az Opera legújabb ✔ | A Safari legújabb ✔ |

## <a name="open-source-sdk"></a>Nyílt forráskódú SDK

A Application Insights JavaScript SDK nyílt forráskódú a forráskód megtekintéséhez vagy a projekthez való hozzájáruláshoz, látogasson el a [hivatalos GitHub-tárházba](https://github.com/Microsoft/ApplicationInsights-JS).

## <a name="next-steps"></a><a name="next"></a>További lépések
* [Használat követése](usage-overview.md)
* [Egyéni események és a mérőszámok](api-custom-events-metrics.md)
* [Összeállítás, mérés, tanulás](usage-overview.md)
