---
title: Azure Application Insights for JavaScript webapps
description: Oldalmegtekintési és munkamenetszám, webes ügyféladatok, egyoldalas alkalmazások (SPA) és a használati minták nyomon követése. Kivételeket és teljesítményproblémákat észlelhet a JavaScript weblapokon.
ms.topic: conceptual
author: Dawgfan
ms.author: mmcc
ms.date: 09/20/2019
ms.openlocfilehash: 5414a70180a82be8253dace7d800c90c1ae6a9bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276074"
---
# <a name="application-insights-for-web-pages"></a>Application Insights weblapokhoz

Megismerheti a weblap vagy alkalmazás teljesítményét és használatát. Ha [az Alkalmazáselemzési adatokat](app-insights-overview.md) hozzáadja az oldalparancsfájlhoz, az oldalbetöltések és az AJAX-hívások időzítését, a számlálókat és a böngészőkivételek és az AJAX-hibák, valamint a felhasználók és a munkamenetek számának időzítését kapja meg. Ezek mindegyike szegmentálható lap, ügyfél operációs rendszere és böngészőverziója, földrajzi hely és más dimenziók alapján. Beállíthat riasztásokat a hibaszámokról és a lassú lapbetöltésekről. Ha nyomkövetési hívásokat szúr be a JavaScript-kódba, nyomon követheti a webalkalmazás különböző szolgáltatásainak használati módját is.

Az Application Insights bármely weblappal használható – csak egy rövid JavaScript-kódot kell hozzáadnia. Ha a webszolgáltatás [Java](java-get-started.md) vagy [ASP.NET,](asp-net.md)használhatja a kiszolgálóoldali SDK-k együtt az ügyféloldali JavaScript SDK-hoz, hogy az alkalmazás teljesítményét végponttól végpontig megismerheti.

## <a name="adding-the-javascript-sdk"></a>A JavaScript SDK hozzáadása

1. Először szüksége van egy Application Insights-erőforrásra. Ha még nem rendelkezik erőforrás- és műszerezési kulccsal, kövesse az [Új erőforrás létrehozása című útmutatót.](create-new-resource.md)
2. Másolja a instrumentation kulcsot az erőforrásból, ahol a JavaScript-telemetriai adatokat el szeretné küldeni.
3. Adja hozzá az Application Insights JavaScript SDK-t a weboldalhoz vagy az alkalmazáshoz az alábbi két lehetőség egyikén:
    * [npm beállítása](#npm-based-setup)
    * [JavaScript-kódrészlet](#snippet-based-setup)

> [!IMPORTANT]
> Csak egy módszert használjon a JavaScript SDK hozzáadásához az alkalmazáshoz. Ha az NPM telepítőjét használja, ne használja a kódrészletet, és fordítva.

> [!NOTE]
> Az NPM telepítője a JavaScript SDK-t a projekttől való függőségként telepíti, lehetővé téve az IntelliSense-t, míg a kódrészlet futásidőben lekéri az SDK-t. Mindkettő ugyanazokat a funkciókat támogatja. Bármennyire, előhívó ki vágy több szokás esemény és alakzat általában választ részére NPM Beállít rövid enthe a felhasználók látszó részére gyors lehetővé tevő -ból kívül- a- doboz pókháló analitika választ részére a Részlet.

### <a name="npm-based-setup"></a>npm alapú beállítás

```js
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView(); // Manually call trackPageView to establish the current user/session/pageview
```

### <a name="snippet-based-setup"></a>Kódrészlet alapú beállítás

Ha az alkalmazás nem használja az npm-et, közvetlenül beállíthatja a weblapokat az Application Insights segítségével, ha ezt a kódrészletet az egyes oldalak tetejére illeszti be. Lehetőleg, ez legyen az első `<head>` parancsfájl a szakaszban, hogy figyelemmel kísérheti az esetleges problémákat az összes függőségek. Ha blazor server alkalmazást használ, adja hozzá a kódrészletet a fájl `_Host.cshtml` tetején a `<head>` szakaszban.

```html
<script type="text/javascript">
var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(n){var o={config:n,initialize:!0},t=document,e=window,i="script";setTimeout(function(){var e=t.createElement(i);e.src=n.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",t.getElementsByTagName(i)[0].parentNode.appendChild(e)});try{o.cookie=t.cookie}catch(e){}function a(n){o[n]=function(){var e=arguments;o.queue.push(function(){o[n].apply(o,e)})}}o.queue=[],o.version=2;for(var s=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];s.length;)a("track"+s.pop());var r="Track",c=r+"Page";a("start"+c),a("stop"+c);var u=r+"Event";if(a("start"+u),a("stop"+u),a("addTelemetryInitializer"),a("setAuthenticatedUserContext"),a("clearAuthenticatedUserContext"),a("flush"),o.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4},!(!0===n.disableExceptionTracking||n.extensionConfig&&n.extensionConfig.ApplicationInsightsAnalytics&&!0===n.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){a("_"+(s="onerror"));var p=e[s];e[s]=function(e,n,t,i,a){var r=p&&p(e,n,t,i,a);return!0!==r&&o["_"+s]({message:e,url:n,lineNumber:t,columnNumber:i,error:a}),r},n.autoExceptionInstrumented=!0}return o}(
{
  instrumentationKey:"INSTRUMENTATION_KEY"
}
);(window[aiName]=aisdk).queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="sending-telemetry-to-the-azure-portal"></a>Telemetriai adatok küldése az Azure Portalra

Alapértelmezés szerint az Application Insights JavaScript SDK automatikusan gyűjt számos telemetriai elemetriai elemeket, amelyek hasznosak az alkalmazás állapotának meghatározásában és az alapul szolgáló felhasználói élmény. Ezek a következők:

- **Nem fogott kivételek** az alkalmazásban, beleértve a
    - Veremnyomkövetés
    - A kivétel részletei és a hibát kísérő üzenet
    - Sor & a hiba oszlopszáma
    - URL, ahol hiba merült fel
- Az alkalmazás **XHR** és **Fetch** által küldött **hálózati függőségi kérések** (a lehívási gyűjtemény alapértelmezés szerint le van tiltva) kéréseket tartalmaznak,
    - Függőségi forrás URL-címe
    - Parancs & A függőség kéréséhez használt metódus
    - A kérelem időtartama
    - A kérelem eredménykódja és sikeres állapota
    - A kérést beküldő felhasználó azonosítója (ha van ilyen)
    - Korrelációs környezet (ha van ilyen), ha a kérelem
- **Felhasználói adatok** (például hely, hálózat, IP)
- **Eszközadatok** (például böngésző, operációs rendszer, verzió, nyelv, felbontás, modell)
- **A munkamenetadatokat**

### <a name="telemetry-initializers"></a>Telemetriai inicializálók
Telemetriai inicializálók segítségével módosíthatja az összegyűjtött telemetriai adatok tartalmát, mielőtt a felhasználó böngészőjéből küldött. Arra is használhatók, hogy megakadályozzák bizonyos telemetriai adatok küldését a visszatéréssel. `false` Több telemetriai inicializálók is hozzáadhatók az Application Insights-példányhoz, és azok azok hozzáadása sorrendben.

A bemeneti `addTelemetryInitializer` argumentum egy visszahívás, amely [`ITelemetryItem`](https://github.com/microsoft/ApplicationInsights-JS/blob/master/API-reference.md#addTelemetryInitializer) argumentumként veszi fel a argumentumot, és `boolean` a vagyat `void`adja vissza. Ha `false`visszatér, a telemetriai elem nem küldi el, különben folytatja a következő telemetriai inicializáló, ha van ilyen, vagy elküldi a telemetriai gyűjtemény végpont.

Példa telemetriai inicializálók használatára:
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
A legtöbb konfigurációs mező neve úgy van elnevezve, hogy alapértelmezés szerint hamis legyen. A kivételével minden `instrumentationKey`mező nem kötelező.

| Név | Alapértelmezett | Leírás |
|------|---------|-------------|
| instrumentationKey | null | **Szükséges**<br>Az Azure Portalról beszerzett instrumentation kulcs. |
| accountId | null | Nem kötelező fiókazonosító, ha az alkalmazás fiókokba csoportosítja a felhasználókat. Nincsszóköz, vessző, pontosvessző, egyenlő vagy függőleges sáv |
| sessionRenewMs | 1800000 | A rendszer naplóz egy munkamenetet, ha a felhasználó ennyi ideig inaktív ezredmásodpercben. Az alapértelmezett érték 30 perc |
| sessionExpirationMs | 86400000 | A munkamenet et a rendszer naplózza, ha ennyi ideig ezredmásodpercben folytatódott. Az alapértelmezett érték 24 óra |
| maxBatchSizeInBájt | 10000 | Telemetriai köteg maximális mérete. Ha egy köteg túllépi ezt a korlátot, a rendszer azonnal elküldi, és új köteget indít el |
| maxBatchInterval | 15 000 | Mennyi ideig kötegelt telemetria iküldés előtt (ezredmásodperc) |
| disableExceptionTracking | hamis | Ha ez igaz, a kivételek nem automatikusan begyűjtött. Az alapértelmezett érték a false (hamis). |
| disableTelemetry | hamis | Ha igaz, telemetriai adatok nem gyűjti vagy küldi. Az alapértelmezett érték a false (hamis). |
| enableDebug | hamis | Ha igaz, a **belső** hibakeresési adatok kivételként kerülnek elő, **nem pedig** naplózva, függetlenül az SDK naplózási beállításaitól. Az alapértelmezett érték a false (hamis). <br>***Megjegyzés:*** A beállítás engedélyezése belső hiba esetén eldobott telemetriai adatokat eredményez. Ez hasznos lehet a konfigurációval vagy az SDK használatával kapcsolatos problémák gyors azonosításához. Ha hibakeresés közben nem szeretné elveszíteni a telemetriai adatokat, fontolja meg a használatát, `consoleLoggingLevel` vagy `telemetryLoggingLevel` ne a. `enableDebug` |
| naplózásLevelConsole | 0 | Naplózza a **konzolhoz** a belső Application Insights-hibákat. <br>0: ki, <br>1: Csak kritikus hibák, <br>2: Minden (hibák & figyelmeztetések) |
| naplózásLevelTelemetria | 1 | **Belső** Application Insights-hibákat küld telemetriaként. <br>0: ki, <br>1: Csak kritikus hibák, <br>2: Minden (hibák & figyelmeztetések) |
| diagnosticLogInterval | 10000 | (belső) Lekérdezési időköz (ms-ban) a belső naplózási várólistához |
| samplingSzázalék | 100 | Az elküldött események százalékos aránya. Az alapértelmezett érték 100, ami azt jelenti, hogy az összes esemény elküldésre kerül. Ezt akkor állítsa be, ha meg szeretné őrizni az adatkorlátot a nagyméretű alkalmazásokhoz. |
| autoTrackPageVisitTime | hamis | Ha ez igaz, az oldalnézetben az előző instrumentált oldal megtekintési idejét nyomon követi a rendszer, és telemetriai adatokként küldi el, és új időzítőindul az aktuális oldalmegtekintéshez. Az alapértelmezett érték a false (hamis). |
| disableAjaxTracking | hamis | Ha ez igaz, ajax hívások nem automatikusan begyűjtött. Az alapértelmezett érték a false (hamis). |
| disableFetchTracking | igaz | Ha igaz, a lehívási kérelmek nem lesznek automatikusan begyűjtve. Az alapértelmezett érték igaz |
| felülírjaAz Oldalnézetidőtartam | hamis | Ha igaz, a trackPageView alapértelmezett viselkedése úgy változik, hogy a trackPageView megnevezésekekesetén rögzítse az oldalnézet időközének végét. Ha hamis és egyéni időtartam nem biztosított a TrackPageView számára, az oldalnézet teljesítményét a navigációs időzítés API-val számítja ki a rendszer. Az alapértelmezett érték a false (hamis). |
| maxAjaxCallsPerView nézet | 500 | Alapértelmezett 500 - szabályozza, hogy hány Ajax hívások lesz nek figyelése oldalanként nézetben. Állítsa -1-re az összes (korlátlan) Ajax hívás figyeléséhez az oldalon. |
| disableDataLossAnalysis | igaz | Ha hamis, belső telemetriai küldő pufferek lesz nek ellenőrzött indításkor az elemek még el nem küldött. |
| disableCorrelationHeaders | hamis | Ha hamis, az SDK két fejlécet ("Kérésazonosító" és "Kérés-környezet") ad hozzá az összes függőségi kérelemhez, hogy korreláljon a kiszolgálói oldalon lévő megfelelő kérésekkel. Az alapértelmezett érték a false (hamis). |
| correlationHeaderExcludedDomains |  | Korrelációs fejlécek letiltása adott tartományokban |
| correlationHeaderDomains |  | Korrelációs fejlécek engedélyezése adott tartományokhoz |
| disableFlushOnBeforeunload | hamis | Alapértelmezett hamis. Ha igaz, a kiürítési metódus nem lesz meghívva, amikor az onBeforeUnload eseményindítók |
| enableSessionStorageBuffer | igaz | Alapértelmezett érték igaz. Ha igaz, a puffer az összes el nem küldött telemetriai adatokat a munkamenet-tárolótárolja. A puffer visszaáll az oldal betöltésekor |
| isCookieUseDisabled | hamis | Alapértelmezett hamis. Ha ez igaz, az SDK nem tárol és nem olvas el adatokat a cookie-kból.|
| cookieDomain | null | Egyéni cookie-tartomány. Ez akkor hasznos, ha az Application Insights-cookie-kat altartományok között szeretné megosztani. |
| isRetryDisabled | hamis | Alapértelmezett hamis. Ha hamis, próbálkozzon újra 206 (részleges sikeres), 408 (időtúloldalon), 429 (túl sok kérés), 500 (belső kiszolgálóhiba), 503 (szolgáltatás nem érhető el) és 0 (offline, csak akkor, ha észlelt) |
| isStorageUseDisabled | hamis | Ha ez igaz, az SDK nem tárolja vagy olvassa be a helyi és munkamenet-tárolóból származó adatokat. Az alapértelmezett érték a false (hamis). |
| isBeaconApiDisabled | igaz | Ha hamis, az SDK elküldi az összes telemetriai adatokat a [Beacon API használatával](https://www.w3.org/TR/beacon) |
| onunloadDisableBeacon | hamis | Alapértelmezett hamis. amikor a lap zárva van, az SDK elküldi az összes fennmaradó telemetriát a [Beacon API](https://www.w3.org/TR/beacon) |
| sdkExtension | null | Beállítja az SDK-bővítmény nevét. Csak betűrendes karakterek engedélyezettek. A bővítmény név előtagjaként hozzáadódik az "ai.internal.sdkVersion" címkéhez (például 'ext_javascript:2.0.0'). Az alapértelmezett érték null. |
| isBrowserLinkTrackingEnabled | hamis | Az alapértelmezett érték a false (hamis). Ha ez igaz, az SDK nyomon követi az összes [böngészőkapcsolati](https://docs.microsoft.com/aspnet/core/client-side/using-browserlink) kérelmet. |
| appId | null | Az AppId az ÜGYFÉLoldali kérelmekkel az ügyféloldali kérelmekkel az ÜGYFÉLoldali függőségek közötti korrelációra szolgál. Ha a Beacon API engedélyezve van, nem használható automatikusan, de manuálisan is beállítható a konfigurációban. Az alapértelmezett érték null értékű |
| enableCorsKorreláció | hamis | Ha ez igaz, az SDK két fejlécet ("Kérésazonosító" és "Kérés-környezet") ad hozzá az összes CORS-kérelemhez, hogy korrelálja a kimenő AJAX-függőségeket a kiszolgálói oldalon lévő megfelelő kérésekkel. Az alapértelmezett érték hamis |
| namePrefix | Meghatározatlan | Nem kötelező érték, amely a localStorage és a cookie-név névbejegyzéseként lesz használva.
| enableAutoRouteTracking | hamis | Az útvonalváltozások automatikus nyomon követése az egyoldalas alkalmazásokban (SPA). Ha igaz, minden útvonalmódosítás új oldalmegtekintést küld az Application Insightsnak. A kivonatoló`example.com/foo#bar`útvonal változásait ( ) a kapcsolat új oldalnézetként is rögzíti.
| enableRequestHeaderTracking | hamis | Ha igaz, az AJAX & Fetch request headers nyomon követése, az alapértelmezett érték hamis.
| enableResponseHeaderTracking | hamis | Ha igaz, az AJAX & Fetch kérés válaszfejléceit követi nyomon a rendszer, az alapértelmezett érték hamis.
| distributedTracingMode | `DistributedTracingModes.AI` | Beállítja az elosztott nyomkövetési módot. Ha AI_AND_W3C mód vagy a W3C mód van beállítva, a W3C nyomkövetési környezet fejlécei (traceparent/tracestate) jönnek létre, és minden kimenő kérelemben benne lesznek. AI_AND_W3C az Application Insights instrumentált szolgáltatásaival való visszakompatibilitáshoz van biztosítva.

## <a name="single-page-applications"></a>Egyoldalas alkalmazások

Alapértelmezés szerint ez az SDK **nem** fogja kezelni az egyoldalas alkalmazásokban előforduló állapotalapú útvonal-módosításokat. Ha engedélyezni szeretné az egyoldalas alkalmazás automatikus `enableAutoRouteTracking: true` útvonalváltozás-követését, hozzáadhatja a beállítási konfigurációhoz.

Jelenleg egy külön [React plugint](#react-extensions) kínálunk, amelyet ezzel az SDK-val inicializálhat. Emellett végrehajtja az útvonalváltozások nyomon követését, valamint [összegyűjti az egyéb React specifikus telemetriai adatokat.](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)

## <a name="react-extensions"></a>Reagáló bővítmények

| Bővítmények |
|---------------|
| [Reagál](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-js/README.md)|
| [Reagálni natív](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/extensions/applicationinsights-react-native/README.md)|

## <a name="explore-browserclient-side-data"></a>Böngésző-/ügyféloldali adatok feltárása

A böngésző/ügyféloldali adatok **megtekinthetők a Metrikák** oldalon, és hozzáadva az Önt érdeklő egyéni mutatókat:

![](./media/javascript/page-view-load-time.png)

Az adatokat a JavaScript SDK-ból is megtekintheti a portál böngészőfelületén keresztül.

Válassza a **Böngésző,** majd **a Hibák** vagy a **Teljesítmény**lehetőséget.

![](./media/javascript/browser.png)

### <a name="performance"></a>Teljesítmény

![](./media/javascript/performance-operations.png)

### <a name="dependencies"></a>Függőségek

![](./media/javascript/performance-dependencies.png)

### <a name="analytics"></a>Elemzés

A JavaScript SDK által gyűjtött telemetriai adatok lekérdezéséhez válassza a **Megtekintés naplókban (Analytics)** gombot. A ( `where` a `client_Type == "Browser"`) utasítás hozzáadásával csak a JavaScript SDK-ból származó adatok jelennek meg, és a kiszolgálóoldali telemetriai adatokat más SDK-k által gyűjtött adatok ki lesznek zárva.
 
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

### <a name="source-map-support"></a>Forrástérkép támogatása

A kivételtelemetria minified hívásvermét az Azure Portalon nem lehet mininmifikálni. A Kivétel részletei panelen lévő összes meglévő integráció együtt fog működni az újonnan nem maminizált hívásveremmel.

#### <a name="link-to-blob-storage-account"></a>Hivatkozás blobtár-fiókra

Az Application Insights-erőforrást a saját Azure Blob Storage-tárolóhoz kapcsolhatja, így automatikusan csökkentheti a híváshalmok automatikus leválasztását. Első lépésekhez olvassa el az [automatikus forrástérkép-támogatás](./source-map-support.md).

### <a name="drag-and-drop"></a>Húzás

1. Válasszon ki egy kivételtelemetriai elemet elemet elemet az Azure Portalon a "Végpontok között tranzakció részletei" megtekintéséhez.
2. Azonosítsa, hogy mely forrásleképezések felelnek meg ennek a hívási veremnek. A forrástérképnek meg kell egyeznie a veremkeret forrásfájljával, de`.map`
3. Húzza a forrásleképezéseket a híváshalomra az Azure Portalon![](https://i.imgur.com/Efue9nU.gif)

### <a name="application-insights-web-basic"></a>Application Insights Web Basic

A könnyű élmény érdekében ehelyett telepítheti az Application Insights alapverzióját
```
npm i --save @microsoft/applicationinsights-web-basic
```
Ez a verzió jön a csupasz minimális számú funkciók és funkciók, és támaszkodik, hogy létrejöjjön, ahogy jónak látja. Például nem végez automatikus begyűjtést (fel nem fogott kivételek, AJAX stb.). Az API-k küldeni bizonyos telemetriai típusok, mint például `trackTrace`a , `trackException`, stb, nem szerepelnek ebben a verzióban, így meg kell adnia a saját burkoló. Az egyetlen elérhető API `track`a . A [minta](https://github.com/Azure-Samples/applicationinsights-web-sample1/blob/master/testlightsku.html) itt található.

## <a name="examples"></a>Példák

Futó példák, lásd: [Application Insights JavaScript SDK-minták](https://github.com/topics/applicationinsights-js-demo)

## <a name="upgrading-from-the-old-version-of-application-insights"></a>Frissítés az Application Insights régi verziójáról

Az SDK V2 verziójának módosításai:
- A jobb API-aláírások érdekében néhány API-hívás, például a trackPageView és a trackException, frissült. Az Internet Explorer 8-as és korábbi verzióiban nem fut.
- A telemetriai boríték mezőnév és struktúra változások miatt adatséma frissítéseket.
- `context.operation` Áthelyezve `context.telemetryTrace`a ikonra. Néhány mező is módosult (`operation.id` --> `telemetryTrace.traceID`).
  - Az aktuális oldalmegtekintési azonosító manuális frissítéséhez (például `appInsights.properties.context.telemetryTrace.traceID = Util.generateW3CId()`spa-alkalmazásokban) használja a használatát.
    > [!NOTE]
    > A nyomkövetési azonosító egyedi véti, ahol korábban használt, `Util.newId()`most használja a használatát. `Util.generateW3CId()` Végül mindkettő a műveleti azonosító.

Ha az aktuális application insights PRODUCTION SDK (1.0.20) használatával, és szeretné látni, hogy az új SDK működik futásidőben, frissítse az URL-címet az aktuális SDK betöltési forgatókönyvtől függően.

- Letöltés CDN-en keresztül: Frissítse a kódrészletet, amelyet jelenleg a következő URL-címre mutat:
   ```
   "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
   ```

- npm forgatókönyv: `downloadAndSetup` Hívja meg a teljes ApplicationInsights parancsfájl letöltését a CDN-ről, és inicializálja a műszerezési kulccsal:

   ```ts
   appInsights.downloadAndSetup({
     instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx",
     url: "https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js"
     });
   ```

Tesztelje a belső környezetben, hogy ellenőrizze a figyelés telemetriai működik a várt módon. Ha minden működik, frissítse az API-aláírások megfelelően SDK V2-verzió, és üzembe helyezése az éles környezetben.

## <a name="sdk-performanceoverhead"></a>SDK-teljesítmény/terhelés

Mindössze 25 KB-os tömörített, és mindössze ~ 15 ms inicializálása, Application Insights hozzáteszi, elhanyagolható mennyiségű betöltési idő a webhelyére. A kódrészlet használatával a tár minimális összetevői gyorsan betöltődnek. Addig is, a teljes script letölthető a háttérben.

Miközben a parancsfájl a CDN-ről töltődik le, az oldal minden nyomon követése várólistára kerül. Miután a letöltött parancsfájl aszinkron inicializálása befejeződött, a várólistára helyezett összes esemény nyomon követi. Ennek eredményeképpen az oldal teljes életciklusa során nem veszít telemetriai adatokat. Ez a beállítási folyamat zökkenőmentes elemzési rendszert biztosít az oldal számára, amely a felhasználók számára láthatatlan.

> Összegzés:
> - **25 KB-os** tömörített
> - **15 ms** teljes inicializálási idő
> - **Az** oldal életciklusa során elmaradt nulla követési idő

## <a name="browser-support"></a>Böngészőtámogatás

![Chrome](https://raw.githubusercontent.com/alrra/browser-logos/master/src/chrome/chrome_48x48.png) | ![Firefox](https://raw.githubusercontent.com/alrra/browser-logos/master/src/firefox/firefox_48x48.png) | ![IE](https://raw.githubusercontent.com/alrra/browser-logos/master/src/edge/edge_48x48.png) | ![Opera](https://raw.githubusercontent.com/alrra/browser-logos/master/src/opera/opera_48x48.png) | ![Safari](https://raw.githubusercontent.com/alrra/browser-logos/master/src/safari/safari_48x48.png)
--- | --- | --- | --- | --- |
A Chrome legújabb ✔ |  Firefox legújabb ✔ | IE 9+ & Edge ✔ | Opera Legújabb ✔ | Safari Legújabb ✔ |

## <a name="open-source-sdk"></a>Nyílt forráskódú SDK

Az Application Insights JavaScript SDK nyílt forráskódú a forráskód megtekintéséhez vagy a projekthez való hozzájáruláshoz, látogasson el a [hivatalos GitHub-tárházba.](https://github.com/Microsoft/ApplicationInsights-JS)

## <a name="next-steps"></a><a name="next"></a>Következő lépések
* [Használat követése](usage-overview.md)
* [Egyéni események és a mérőszámok](api-custom-events-metrics.md)
* [Összeállítás, mérés, tanulás](usage-overview.md)
