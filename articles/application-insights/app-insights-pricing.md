---
title: Tarifa- és adatok kötet kezelése az Azure Application Insights |} Microsoft Docs
description: Telemetria kötetek kezelése, és figyelje az Application Insightsban költségeket.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: mbullwin
ms.openlocfilehash: 2c06c2220d3a3ed0a27b4f0febb4de95b2137ddc
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="manage-pricing-and-data-volume-in-application-insights"></a>Az Application Insightsban tarifa- és adatok kötet kezelése

Az árképzés [Azure Application Insights] [ start] adatmennyiség alkalmazásonként alapul. Minden egyes Application Insights-erőforrás külön szolgáltatás fel van töltve, és hozzájárul az Azure-előfizetéshez tartozó számlázási.

Az Application Insights van két árképzési tervek: Basic és Enterprise. Az alapvető tarifacsomagot az alapértelmezett terv. Ez magában foglalja az összes vállalati terv funkciót, minden további költség nélkül. Az alapszintű csomag váltók elsősorban okozhatnak az adatokat a köteten. 

A vállalati tervben szereplő csomópontonként járnak, és minden csomópont kap egy napi adatok támogatás. A vállalat díjszabási csomaggal van szó, a fent a belefoglalt támogatás okozhatnak adatokat. Az Operations Management Suite használja, ha a vállalati terv kell kiválasztani. 

Ha árképzés működésével kapcsolatos az Application Insights kérdése van, vannak, beküldheti fel kérdést az [fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights).

## <a name="pricing-plans"></a>Díjszabások

Lásd: az a régió és aktuális árak [Application Insights árképzési][pricing].

> [!NOTE]
> A április 2018 azt [bevezetett](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) Azure figyelési új árképzési modellt. Ez a modell egy egyszerű "használatalapú" modell a teljes kaphat a szolgáltatások figyelése fogad el. További információ a [új árképzési modellt](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), hogy a [ebben a modellben való áthelyezése hatásának értékelése](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model) a használati minták alapján és [az új modell programba történő](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model).

### <a name="basic-plan"></a>Alapszintű

Az alapszintű csomag az alapérték árképzési terv, amikor létrejön egy új Application Insights-erőforrást. Az alapszintű csomag el legoptimálisabban az összes ügyfél számára, kivéve azokat, aki rendelkezik az Operations Management Suite-előfizetéssel.

* Az alapszintű csomag által adatmennyiség van szó. Adatmennyiség telemetriai adatot az Application Insights által fogadott bájtok száma. 
    
    A tömörítetlen JSON adatok csomag érkezett az Application Insights által az alkalmazás adatainak mennyisége kell mérni.

    A [Analytics importált táblázatos adatok](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import), adatmennyiség Application insights szolgáltatásnak elküldött fájlok tömörítetlen méretének mérése történik.
* [Élő Stream metrikák](app-insights-live-stream.md) adatok díjszabási célra nem számítanak.
* [A folyamatos exportálás](app-insights-export-telemetry.md) és a [Azure Naplóelemzés összekötő](https://go.microsoft.com/fwlink/?LinkId=833039&amp;clcid=0x409) érhetők el külön díjfizetés nélkül az alapszintű csomag április 2018 től.

### <a name="enterprise-plan-and-operations-management-suite-subscription-entitlements"></a>Vállalati terv és az Operations Management Suite előfizetés jogosultságok

Az ügyfelek, akik beszerzési Operations Management Suite E1 és E2 Application Insights vállalati kaphat további ingyenes további összetevőként [korábban bejelentette](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/). Az Operations Management Suite E1 és E2 tárolóegységekhez konkrétan az Application Insights vállalati terv egy csomópont jogosultságot. Minden Application Insights csomópont tartalmazza az adatok naponta (elkülönül Naplóelemzési adatfeldolgozást), az adatok 90 napos megőrzési további költségek nélkül okozhatnak legfeljebb 200 MB. A terv a cikk későbbi részében részletesebben ismerteti. 

Mivel ez a csomag csak az Operations Management Suite-előfizetéssel rendelkező felhasználók használható, a felhasználóknak, akik nem rendelkezik az Operations Management Suite-előfizetéssel nem lát olyan lehetőséget, jelölje be ezt a tervet.

> [!NOTE]
> Győződjön meg arról, hogy ezt a jogosultságot kap, az Application Insights-erőforrások árképzési terv a vállalaton belül kell lennie. Ez a jogosultság csak csomópontként vonatkozik. Az alapszintű csomag az Application Insights-erőforrások hasznos figyelmen kívül hagyja. Ez a jogosultság nem látható a látható becsült költségeket a **használati és a becsült költség** ablaktáblán. Ha az új árképzési modellt a megfigyelést. április 2018 Azure előfizetés helyezi át, az alapszintű csomag is elérhető csak terv. Előfizetés áthelyezése az új Azure árképzési modellt figyelés nem javasolt, ha az Operations Management Suite-előfizetéssel rendelkezik.

A vállalati terv kapcsolatos további információkért lásd: [díjszabása vállalati](app-insights-pricing-enterprise-details.md).

### <a name="multi-step-web-tests"></a>Többlépéses webes tesztek

[Több lépés webteszt](app-insights-monitor-web-app-availability.md#multi-step-web-tests) fel Önnek egy kell külön fizetni. Több lépés webes tesztjeinek használatát olyan webes tesztjeinek használatát, amely egy műveletsorozatot végrehajtani.

A különálló ingyenesek *ping-vizsgálatok* egy oldal. A ping-vizsgálatok és többlépéses tesztek telemetriai díjfizetéssel ugyanaz, mint más telemetriai az alkalmazásból.

## <a name="review-pricing-plans-and-estimate-costs"></a>Tekintse át a csomagok és a becsült költség díjszabása

Az Application Insights megkönnyíti, hogy az elérhető árképzési csomagok megértéséhez, és milyen a költségek valószínűleg legutóbbi használati minták alapján. Ismerkedés az Azure portálon, az Application Insights-erőforrást, keresse fel a **használati és a becsült költség** panelen:

![Tarifacsomag kiválasztása](./media/app-insights-pricing/pricing-001.png)

A. Tekintse át a adatmennyiség az adott hónap. Ez magában foglalja az adatok által fogadott és maradnak (után bármely [mintavételi](app-insights-sampling.md)) a kiszolgáló és az ügyfélalkalmazások, és rendelkezésreállás figyelésére szolgáló tesztek.  
B. Egy külön kell fizetni történik [többlépéses webteszt](app-insights-monitor-web-app-availability.md#multi-step-web-tests). (Ebbe nem számítanak bele egyszerű rendelkezésreállás figyelésére szolgáló tesztek, amelyek szerepelnek az adatok kötet kell fizetni.)  
C. Adatok kötet tendenciák megtekintése az elmúlt hónapban.  
D. Engedélyezze az adatfeldolgozást [mintavételi](app-insights-sampling.md).   
E. Állítsa be a napi adatok kötet kap.  

Application Insights költségek az Azure számlázásának hozzáadódnak. A számlázás az Azure részleteit láthatja a **számlázási** szakaszban az Azure-portálon, vagy a a [Azure számlázási portálon](https://account.windowsazure.com/Subscriptions). 

![A bal oldali menüben válassza ki a számlázás](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>Átviteli sebesség
Küldött adatok mennyisége három módon van korlátozva:

* **A mintavételi**: mintavételi használhatja a kiszolgáló és az ügyfél alkalmazásoknak, a minimális torzulásának metrikák által küldött telemetriai adatok mennyiségének csökkentésére. Mintavételi küldött adatok mennyisége hangolására használható elsődleges eszköz. További információ [szolgáltatások mintavétel](app-insights-sampling.md). 
* **Napi maximális**: az Application Insights-erőforrást az Azure-portálon való létrehozásakor a napi maximális értéke 100 GB/nap. A Visual Studio Application Insights-erőforrást hoz létre, amikor az alapértelmezett érték kis (csak 32,3 megabájt MB/nap). A napi cap alapértelmezett értéke a tesztelés elősegítése érdekében. Célja, hogy a felhasználó emeli a napi kap éles környezetben az alkalmazás telepítése előtt. 

    A maximális kap kér egy nagy forgalmú alkalmazáshoz magasabb legfeljebb 1000 GB/nap esetén. 

    Körültekintően járjon el a napi kap beállításakor. A cél kell *soha nem elérte a napi kap*. Elérte a napi kap, ha a nap hátralevő adatok elvesznek, és nem figyelheti az alkalmazást. A napi kap módosításához használja a **napi kötet cap** lehetőséget. Érheti el ezt a lehetőséget a **használati és a becsült költség** (Ez leírása a cikk későbbi részében részletesebben) panelen.
    Néhány előfizetés típust, amely nem használható az Application Insights jóváírás rendelkező korlátozását eltávolította azt. Korábban Ha az előfizetésnek a költségkeret maximumát, a napi cap párbeszédpanelnek távolítsa el a költségkeret maximumát, és engedélyezze a napi kap túl 32,3 megabájt MB/nap felmerülő vonatkozó utasításokat.
* **Sávszélesség-szabályozás**: szabályozás korlátok 32000 események másodpercenkénti, hogy az adatátviteli sebesség átlagosan több mint 1 perc.

*Mi történik, ha az alkalmazás meghaladja a szabályozási értéket?*

* Tartalmazza az alkalmazás által percenként megfelelőségét ellenőrizni kell. Ha a percre átlagolva másodpercenkénti aránya meghaladja, a kiszolgáló visszautasítja bizonyos kérelmek. Az SDK puffereli, az adatokat, és próbálja meg újból elküldeni. Hogy terjeszti ki megugrását, néhány perc. Ha az alkalmazás következetesen küld adatokat a több, mint a szabályozási értéket, bizonyos adatok ki lesznek hagyva. (Az ASP.NET, Java és JavaScript SDK-k próbálja meg újra elküldeni az adatok így; Csomagjától előfordulhat, hogy egyszerűen az vetett szabályozott adatokat.) Sávszélesség-szabályozás akkor fordul elő, ha egy értesítési figyelmeztető riasztást küld arról, hogy ez történt.

*Honnan tudhatom, hogy mennyi adatot küld alkalmazásom?*

Mennyi adatot küld az alkalmazás megtekintéséhez használhatja az alábbi lehetőségek közül:

* Lépjen a **használati és a becsült költség** ablaktáblában tekintse meg az adatok napi kötet diagram. 
* A Metrikaböngészőben új diagram hozzáadása. Válassza ki a diagram metrika **adatpontok mennyisége**. Kapcsolja be a **csoportosítási**, és ezután csoportosítási **adattípus**.

## <a name="reduce-your-data-rate"></a>Csökkentse az arány
A következőkben az adatmennyiség csökkentése érdekében teheti:

* Használjon [mintavételi](app-insights-sampling.md). Ez a technológia csökkenti az arány nélkül a metrikákat döntés. Keresse meg a keresési kapcsolódó elemek között olyan őrizze meg. Mintavételi kiszolgáló alkalmazások automatikusan működik.
* [Korlátozza az jelenthetők Ajax-hívások számát](app-insights-javascript.md#detailed-configuration) minden lap nézetben vagy kapcsoló Ajax reporting ki.
* [Szerkessze az ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) nincs szükség gyűjtemény modulok kikapcsolása. Például dönthet úgy, hogy teljesítményszámlálókkal és a függőségi adatokat inessential.
* Ossza fel a telemetriai külön instrumentation kulcsok között. 
* Előre összesített metrikákat. TrackMetric hívások be az alkalmazást, ha forgalmat csökkentheti a túlterhelést, amely fogadja a átlagának kiszámítása és a mérési köteg szórása használatával. Vagy használhat egy [előre összesítése csomag](https://www.myget.org/gallery/applicationinsights-sdk-labs).

## <a name="manage-the-maximum-daily-data-volume"></a>A maximális napi adatmennyiség kezelése

A napi kötet kap használatával korlátozható az összegyűjtött adatokat. Azonban a kap teljesülése esetén, a nap hátralevő az alkalmazás által küldött összes telemetriai adat adatvesztést következik be. Az *nem ajánlott* szeretné, hogy az alkalmazás elérte a napi kap. Nyomon követése nem állapotának és az alkalmazás teljesítményének után eléri a napi kap.

A napi kötet kap helyett használja [mintavételi](app-insights-sampling.md) hangolására adatmennyiség a kívánt szintre. Ezután használja a napi kap csak "utolsó lehetőségként" abban az esetben, ha az alkalmazás váratlanul megkezdi sok nagyobb mennyiségű telemetriai adatok küldését.

Megváltozott a napi kap, a **konfigurálása** szakasza az Application Insights-erőforrást, a a **használati és a becsült költség** ablaktáblán válassza előbb **napi maximális**.

![A napi telemetriai kötet kap beállítása](./media/app-insights-pricing/pricing-003.png)

## <a name="sampling"></a>Mintavételezés
[A mintavételi](app-insights-sampling.md) csökkenti a telemetriai adatok van küldésének sebessége az alkalmazáshoz, megtartja a kapcsolódó események megkeresését diagnosztikai keresések során módot. Is megőrzik a megfelelő események száma.

Mintavételi hatékonyan csökkentheti a költségeket és a havi kvótán belül maradnak. A mintavételi algoritmus így megtartja a kapcsolódó elemek telemetriai adatot, például a kereséssel, található egy adott kivételhez kapcsolódó a kérést. Az algoritmus is megőrzi megfelelő számát, ezért az kérelem sebességét, a kivétel díjszabás és egyéb számok a megfelelő értékeit metrika Explorer.

Nincsenek mintavételi több űrlap.

* [Adaptív mintavételi](app-insights-sampling.md) az alapértelmezett beállítás az ASP.NET SDK-ban. Adaptív mintavételi automatikusan igazodni fog a telemetriai adatokból, hogy az alkalmazás küld mennyiségét. Működést automatikusan az SDK-t a webalkalmazás, hogy a hálózati forgalom telemetriai csökken. 
* *Adatfeldolgozást mintavételi* alternatív megoldás, amely működik, ahol az alkalmazásból telemetriai belép az Application Insights szolgáltatással. Adatfeldolgozást mintavételi nincs hatással az alkalmazásból küldött telemetriai adatok mennyiségét, de csökkenti a kötetet, amelyet a szolgáltatás megőrzi. Adatfeldolgozást mintavételi segítségével csökkenthető a telemetriai böngészők és egyéb SDK-k által használt kvótát.

Állítsa be az adatfeldolgozást mintavételi, keresse fel a **árazás** panelen:

![A kvóta és árképzési ablaktáblán, a minták csempe, majd válassza ki és mintavételi töredéke alatt](./media/app-insights-pricing/pricing-004.png)

> [!WARNING]
> A **adat-mintavételezésre** ablaktábla csak az adatfeldolgozást mintavételi érték szabályozza. A mintavételi ráta alkalmazott az Application Insights SDK az alkalmazás által nem is. Ha a bejövő telemetriai már mintavételezett az SDK-ban, adatfeldolgozást mintavételi nincs alkalmazva.
>

A tényleges mintavételi ráta, függetlenül attól, ahol azt van érvényben, felderítéséhez használni egy [Analytics lekérdezési](app-insights-analytics.md). A lekérdezés így néz ki:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

Az egyes megőrzi a rekord, `itemCount` azt jelzi, hogy ezt az eredeti rekordok száma. Ez megegyezik 1 + az előző elvetett rekordok száma. 

## <a name="automation"></a>Automatizálás

A parancsfájl az ár terv beállítása az Azure Resource Manager használatával írhat. [További tudnivalókat itt talál](app-insights-powershell.md#price).

## <a name="limits-summary"></a>Korlátozások összegzése

[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

## <a name="next-steps"></a>További lépések

* [Mintavételezés](app-insights-sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/
