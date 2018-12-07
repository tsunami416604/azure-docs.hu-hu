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
ms.date: 03/14/2017
ms.author: mbullwin
ms.openlocfilehash: 937cbc1346ce41670270b911e474e900f406a93b
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52993615"
---
# <a name="application-insights-for-web-pages"></a>Application Insights weblapokhoz
Megismerheti a weblap vagy alkalmazás teljesítményét és használatát. Ha a Visual Studio [Application Insightst](app-insights-overview.md) a lap szkriptjeihez adja, megkapja az oldalbetöltések és AJAX-hívások időzítését, a böngészőkivételek és az AJAX-hibák számát és részleteit, valamint a felhasználók és munkamenetek számát. Ezek mindegyike szegmentálható lap, ügyfél operációs rendszere és böngészőverziója, földrajzi hely és más dimenziók alapján. Beállíthat riasztásokat a hibaszámokról és a lassú lapbetöltésekről. Ha nyomkövetési hívásokat szúr be a JavaScript-kódba, nyomon követheti a webalkalmazás különböző szolgáltatásainak használati módját is.

Az Application Insights bármely weblappal használható – csak egy rövid JavaScript-kódot kell hozzáadnia. Ha a webszolgáltatása [Java](app-insights-java-get-started.md) vagy [ASP.NET](app-insights-asp-net.md), telemetriát integrálhat a kiszolgálóról és az ügyfelekről.

![A portal.azure.com címen nyissa meg az alkalmazás erőforrását, és kattintson a Böngésző elemre](./media/app-insights-javascript/03.png)

Ehhez egy [Microsoft Azure](https://azure.com)-előfizetésre van szüksége. Ha a csapata szervezeti előfizetéssel rendelkezik, kérje meg a tulajdonost, hogy adjon hozzá Microsoft-fiókot.

## <a name="set-up-application-insights-for-your-web-page"></a>Az Application Insights beállítása a weboldalához
A következőképpen adja hozzá a betöltő kódrészletet a weboldalakhoz.

### <a name="open-or-create-application-insights-resource"></a>Application Insights-erőforrás megnyitása vagy létrehozása
Az Application Insights-erőforrásban jelennek meg a lap teljesítményével és használatával kapcsolatos adatok. 

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

Ha már beállította a figyelést az alkalmazás kiszolgálói oldalára, már rendelkezik egy erőforrással:

![Válassza a Tallózás, Fejlesztői szolgáltatások, Application Insights lehetőséget.](./media/app-insights-javascript/01-find.png)

Ha nincs erőforrása, hozza létre azt:

![Válassza az Új, Fejlesztői szolgáltatások, Application Insights lehetőséget.](./media/app-insights-javascript/01-create.png)

*Máris kérdései vannak?* [További információk erőforrások létrehozásáról](app-insights-create-new-resource.md).

### <a name="add-the-sdk-script-to-your-app-or-web-pages"></a>Az SDK-szkript hozzáadása az alkalmazáshoz vagy weblapokhoz

```HTML
<!-- 
To collect user behavior analytics about your application, 
insert the following script into each page you want to track.
Place this code immediately before the closing </head> tag,
and before any other scripts. Your first data will appear 
automatically in just a few seconds.
-->
<script type="text/javascript">
var appInsights=window.appInsights||function(a){
  function b(a){c[a]=function(){var b=arguments;c.queue.push(function(){c[a].apply(c,b)})}}var c={config:a},d=document,e=window;setTimeout(function(){var b=d.createElement("script");b.src=a.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js",d.getElementsByTagName("script")[0].parentNode.appendChild(b)});try{c.cookie=d.cookie}catch(a){}c.queue=[];for(var f=["Event","Exception","Metric","PageView","Trace","Dependency"];f.length;)b("track"+f.pop());if(b("setAuthenticatedUserContext"),b("clearAuthenticatedUserContext"),b("startTrackEvent"),b("stopTrackEvent"),b("startTrackPage"),b("stopTrackPage"),b("flush"),!a.disableExceptionTracking){f="onerror",b("_"+f);var g=e[f];e[f]=function(a,b,d,e,h){var i=g&&g(a,b,d,e,h);return!0!==i&&c["_"+f](a,b,d,e,h),i}}return c
  }({
      instrumentationKey:"<your instrumentation key>"
  });
  
window.appInsights=appInsights,appInsights.queue&&0===appInsights.queue.length&&appInsights.trackPageView();
</script>
```

Szúrja be a szkriptet minden olyan lap `</head>` címkéje elé, amelyet nyomon szeretne követni. Ha a webhelye mesterlappal rendelkezik, ide helyezheti a szkriptet. Példa:

* Egy ASP.NET MVC-projektben a következő helyre helyezné a szkriptet: `View\Shared\_Layout.cshtml`
* Egy SharePoint-helyen, a vezérlőpulton nyissa meg a [Hely beállításai / Mesterlap](app-insights-sharepoint.md) elemet.

A szkript tartalmazza a kialakítási kulcsot, amely az adatokat az Application Insights-erőforrásra irányítja. 

([A szkript részletesebb magyarázata.](https://apmtips.com/blog/2015/03/18/javascript-snippet-explained/))

## <a name="detailed-configuration"></a>Részletes konfiguráció
Több [paramétert](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config) beállíthat, de a legtöbb esetben erre nincs szükség. Letilthatja vagy korlátozhatja például az oldalmegtekintésenként jelentett Ajax-hívások számát (a forgalom csökkentése érdekében). Vagy beállíthatja a hibakeresési módot, hogy a telemetria gyorsan haladjon át a folyamaton, kötegkészítés nélkül.

Ezen paraméterek beállításához keresse meg ezt a sort a kódrészletben, és adjon hozzá utána vesszővel elválasztott elemeket:

    })({
      instrumentationKey: "..."
      // Insert here
    });

Az [elérhető paraméterek](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config) közé a következők tartoznak:

    // Send telemetry immediately without batching.
    // Remember to remove this when no longer required, as it
    // can affect browser performance.
    enableDebug: boolean,

    // Don't log browser exceptions.
    disableExceptionTracking: boolean,

    // Don't log ajax calls.
    disableAjaxTracking: boolean,

    // Limit number of Ajax calls logged, to reduce traffic.
    maxAjaxCallsPerView: 10, // default is 500

    // Time page load up to execution of first trackPageView().
    overridePageViewDuration: boolean,

    // Set dynamically for an authenticated user.
    accountId: string,

## <a name="run"></a>Az alkalmazás futtatása
Futtassa a webalkalmazást, használja egy ideig telemetria létrehozásához, és várjon néhány másodpercig. Ezt futtathatja a fejlesztési számítógépén az **F5** billentyűvel, vagy közzéteheti, és hagyhatja, hogy a felhasználók játsszanak vele.

Ha ellenőrizni szeretné a webalkalmazás által az Application Insightsnak küldött telemetriát, használja a böngésző hibakereső eszközeit (számos böngészőben az **F12** billentyű). Az adatokat a dc.services.visualstudio.com címre küldi a rendszer.

## <a name="explore-your-browser-performance-data"></a>A böngésző teljesítményadatainak feltárása
Nyissa meg a Böngésző panelt a felhasználók böngészőinek összesített teljesítményadatainak megjelenítéséhez.

![A portal.azure.com címen nyissa meg az alkalmazás erőforrását, és kattintson a Beállítások, Böngésző lehetőségre](./media/app-insights-javascript/03.png)

Még nincsenek adatok? Kattintson a **Frissítés** gombra a lap tetején. Még mindig semmi? Lásd: [Hibaelhárítás](app-insights-troubleshoot-faq.md).

A Böngésző panel [Metrikaböngésző panel](app-insights-metrics-explorer.md) előre beállított szűrőkkel és diagramkijelölésekkel. Ha szeretné, szerkesztheti az időtartomány, a szűrők és a diagram konfigurációját, és kedvencként mentheti az eredményt. Kattintson az **Alapértelmezések visszaállítása** gombra, hogy visszaállítsa az eredeti panelkonfigurációt.

## <a name="page-load-performance"></a>Lapbetöltés teljesítménye
Felül található a lapbetöltési idők szegmentált diagramja. A diagram teljes magassága jelzi a lapok betöltésének és megjelenítésének átlagos idejét az alkalmazásából a felhasználók böngészőiben. Az idő mérése onnan kezdődik, amikor a böngésző elküldi a kezdeti HTTP-kérelmet, és addig tart, amíg az összes szinkron betöltési esemény fel lett dolgozva, beleértve az elrendezést és a futó szkripteket. Ebben nem tartoznak bele az aszinkron feladatok, például a kijelzők betöltése az AJAX hívásokból.

A diagram a teljes lapbetöltési időt a [W3C által meghatározott standard időzítésekre](https://www.w3.org/TR/navigation-timing/#processing-model) szegmentálja. 

![](./media/app-insights-javascript/08-client-split.png)

Vegye figyelembe, hogy a *hálózati csatlakozási* idő gyakran kevesebb a vártnál, mert ez a böngészőből a kiszolgálóra irányuló összes kérelem átlaga. Számos egyéni kérés csatlakozási ideje 0, mert már van aktív kapcsolat a kiszolgálóval.

### <a name="slow-loading"></a>Lassú a betöltés?
A lassú lapbetöltések a felhasználók elégedetlenségének fő forrásai. Ha a diagram lassú lapbetöltéseket jelez, könnyű diagnosztikai vizsgálatot végezni.

A diagram az alkalmazás összes lapbetöltésének átlagát mutatja. Ha látni szeretné, hogy a probléma csak adott oldalakra korlátozott-e, tekintse meg a panel további részét, ahol az oldalak URL-címe alapján szegmentált rács található:

![](./media/app-insights-javascript/09-page-perf.png)

Figyelje meg a lapmegtekintések számát és a szórást. Ha az oldalszám nagyon alacsony, akkor a hiba nem nagyon érinti a felhasználókat. A magas szórás (amelynek értéke az átlaghoz hasonló) sok változást jelez az egyes mérések között.

**Nagyítsa ki az egyik URL-címet és az egyik lapmegtekintést.** Kattintson valamely oldalnévre a csak az adott URL-címre szűrt böngésződiagramok paneljének megtekintéséhez, majd kattintson egy lapmegtekintés-példányra.

![](./media/app-insights-javascript/35.png)

Kattintson a `...` gombra az esemény tulajdonságainak teljes listájáért, vagy vizsgálja meg az Ajax hívásokat és kapcsolódó eseményeket. A lassú Ajax hívások hatással lehetnek a teljes lapbetöltési időre, ha szinkron állapotúak. A kapcsolódó események közé tartoznak ugyanazon URL kiszolgálókérelmei (ha beállította az Application Insights szolgáltatást a webkiszolgálóján).

**Lapteljesítmény az idő függvényében.** A Böngészők panelen módosítsa a Lapmegtekintés betöltési ideje rácsot vonaldiagrammá, hogy lássa, hogy adott időkben voltak-e csúcsok:

![Kattintson a rács fejlécére, és válasszon egy új diagramtípust](./media/app-insights-javascript/10-page-perf-area.png)

**Szegmentáljon más dimenziók alapján.** Lehet, hogy a lapjai lassabban töltődnek be egy adott böngészőben, ügyfél operációs rendszeren vagy felhasználói helyen? Adjon hozzá egy új diagramot, és kísérletezzen a **Csoportosítás szempontja** dimenzióval.

![](./media/app-insights-javascript/21.png)

## <a name="ajax-performance"></a>AJAX teljesítménye
Győződjön meg arról, hogy a weblapjain lévő összes AJAX hívás teljesítménye megfelelő. Gyakran ezekkel töltik fel a lapja egyes részeit aszinkron módon. Lehet, hogy a teljes lap gyorsan betöltődik, de a felhasználókat zavarhatja, ha üres kijelzőket látnak, és várnak, hogy adatok jelenjenek meg ezekben.

A weblapjáról végzett AJAX hívások a Böngészők panelen függőségekként jelennek meg.

Összegző diagramok találhatók a panel felső részén:

![](./media/app-insights-javascript/31.png)

és részletes rácsok lejjebb:

![](./media/app-insights-javascript/33.png)

Kattintson valamelyik sorra a részletekért.

> [!NOTE]
> Ha törli a Böngészők szűrőt a panelen, a kiszolgáló és az AJAX-függőségei is belekerülnek ezekbe a diagramokba. Kattintson az Alapértelmezések visszaállítása gombra a szűrő újrakonfigurálásához.
> 
> 

**A sikertelen Ajax hívások részletezéséért** görgessen le a Függőséghibák rácsra, majd kattintson egy sorra az adott példányok megtekintéséhez.

![](./media/app-insights-javascript/37.png)


Kattintson a `...` gombra az Ajax hívások teljes telemetriájáért.

### <a name="no-ajax-calls-reported"></a>Nincsenek Ajax hívások jelentve?
Az Ajax-hívások közé tartoznak a weblapja szkriptjéről végzett HTTP/HTTPS-hívások. Ha nem látja őket a jelentésben, ellenőrizze, hogy a kódrészlet nem adja-e meg a `disableAjaxTracking` vagy a `maxAjaxCallsPerView` [paramétert](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config).

## <a name="browser-exceptions"></a>Böngészőkivételek
A Böngészők panelen található egy kivételek összegző diagram, valamint a panelen lejjebb a kivételtípusok rácsa.

![](./media/app-insights-javascript/39.png)

Ha nem látja a böngészőkivételeket a jelentésben, ellenőrizze, hogy a kódrészlet nem adja-e meg a `disableExceptionTracking` [paramétert](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config).

## <a name="inspect-individual-page-view-events"></a>Egyéni lapmegtekintési események vizsgálata

Általában az Application Insights elemzi a lapmegtekintési telemetriát, és Ön csak összegző jelentéseket tekinthet meg, amelyek az összes felhasználó átlagát tartalmazzák. De a hibakereséshez az egyes lapmegtekintési eseményeket is megtekintheti.

A Diagnosztikai keresés panelen állítsa a Szűrők elemet Lapmegtekintés értékre.

![](./media/app-insights-javascript/12-search-pages.png)

Válassza ki valamelyik eseményt további részletek megtekintéséhez. A részletek lapján kattintson a „...” gombra további részletek megtekintéséhez.

> [!NOTE]
> Ha a [Keresés](app-insights-diagnostic-search.md) lehetőséget használja, vegye figyelembe, hogy teljes szavakat kell megadnia: a „Tudni” és a „valók” nem felel meg a „Tudnivalók” kifejezésnek.
> 
> 

Használhatja a nagy teljesítményű [Log Analytics lekérdezési nyelvet](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-tour#browser-timings-table) is a lapmegtekintések kereséséhez.

### <a name="page-view-properties"></a>Lapmegtekintési tulajdonságok
* **Lapmegtekintés időtartama** 
  
  * Alapértelmezés szerint a lap betöltéséhez szükséges idő, az ügyfél kérelmétől a teljes betöltésig (beleértve a kiegészítő fájlokat, az aszinkron feladatok, például az Ajax hívások nélkül). 
  * Ha a `overridePageViewDuration` van beállítva a [lapkonfigurációban](#detailed-configuration), az ügyfél kérelme és az első `trackPageView` végrehajtása közötti időköz. Ha elmozdította a trackPageView elemet a szokásos pozíciójából a szkript inicializálása után, az más értéket fog jelölni.
  * Ha az `overridePageViewDuration` be van állítva, és egy időtartam-argumentum van megadva a `trackPageView()` hívásban, akkor ehelyett az argumentum értékét használja a rendszer. 

## <a name="custom-page-counts"></a>Egyéni lapok száma
Alapértelmezés szerint mindig lapszámlálás történik, amikor új lap töltődik egy ügyfélböngészőbe.  De lehet, hogy meg szeretné számlálni a további lapmegtekintéseket is. Egy lap tartalma például füleken jelenhet meg, és lehet, hogy számlálni szeretne, amikor a felhasználó fület vált. Vagy lehet, hogy a lap JavaScript kódja új tartalmat tölt be a böngésző URL-címének módosítása nélkül.

Szúrjon be egy ehhez hasonló JavaScript hívást az ügyfélkód megfelelő pontján:

    appInsights.trackPageView(myPageName);

A lap neve ugyanazokat a karaktereket tartalmazhatja, mint egy URL-cím, de a rendszer a „#” vagy „?” utáni karaktereket figyelmen kívül hagyja.

## <a name="usage-tracking"></a>Használatkövetés
Meg szeretné tudni, hogyan használják a felhasználók az alkalmazását?

* [Ismerje meg a felhasználói viselkedés elemzési eszközök](app-insights-usage-overview.md)
* [Információk az egyéni eseményekről és a mérőszám API-ról](app-insights-api-custom-events-metrics.md).

## <a name="video"></a>Videó


> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]



## <a name="next"></a> Következő lépések
* [Használat követése](app-insights-usage-overview.md)
* [Egyéni események és a mérőszámok](app-insights-api-custom-events-metrics.md)
* [Összeállítás, mérés, tanulás](app-insights-usage-overview.md)

