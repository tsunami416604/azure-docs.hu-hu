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
ms.openlocfilehash: 42c54509cedf48e9ebabb5b50865ed56e54bee05
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="manage-pricing-and-data-volume-in-application-insights"></a>Az Application Insightsban tarifa- és adatok kötet kezelése

Az árképzés [Azure Application Insights] [ start] adatmennyiség alkalmazásonként alapul. Minden egyes Application Insights-erőforrás külön szolgáltatás fel van töltve, és hozzájárul az Azure-előfizetéshez tartozó számlázási.

Nincsenek két árképzési tervek. Az alapértelmezett terv Basic, amely tartalmazza az összes funkciót a vállalati terv elsősorban az adatok okozhatnak a köteten nincs hozzáadása költség és váltók neve. Ha az Operations Management Suite használ, úgy kell dönthet, a vállalati terv, amely egy csomópontonként díjat számítanak naponta adatok támogatás együtt, és majd díjat számítanak a belefoglalt támogatás fent okozhatnak adatok.

Ha árképzés működésével kapcsolatos az Application Insights kérdése van, nyugodtan fel a kérdést a [fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights).

## <a name="the-price-plans"></a>Az árlista csomagok

Tekintse meg a [árképzést ismertető oldalra Application Insights] [ pricing] aktuális árak a pénznem és régióban.

### <a name="basic-plan"></a>Alapszintű

Az alapszintű csomag az alapértelmezett beállítás, ha egy új Application Insights-erőforrás jön létre, és az Operations Management Suite előfizetői kivételével valamennyi ügyfél számára optimális.

* Az alapszintű csomag, az adatok kötetenként van szó: a telemetriai adatok az Application Insights által fogadott bájtok száma. Az Application Insights által kapott az alkalmazás tömörítetlen JSON adatcsomag méretének adatmennyiség mérése történik.
A [Analytics importált adatok](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import), adatmennyiség mérik, mert a tömörítetlen Application insights szolgáltatásnak elküldött fájlok mérete.
* [Élő Stream metrikák](app-insights-live-stream.md) adatok díjszabási célra nem számítanak.
* [A folyamatos exportálás](app-insights-export-telemetry.md) és a [Naplóelemzési összekötő](https://go.microsoft.com/fwlink/?LinkId=833039&amp;clcid=0x409) érhetők el minden további költség nélkül. április 2018 frissítésétől az alapszintű csomag.

### <a name="enterprise-plan-and-operations-management-suite-subscription-entitlements"></a>Vállalati terv és az Operations Management Suite előfizetés jogosultságok

Az ügyfelek, akik megvásárolják a Microsoft Operations Management Suite E1 és E2 kérhető le a Application Insights vállalati további ingyenes további összetevőként [korábban bejelentette](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/). Az Operations Management Suite E1 és E2 tárolóegységekhez konkrétan az Application Insights a vállalati terv 1 csomópont jogosultságot. Alább leírtak részletesebben minden Application Insights csomópont tartalmazza az adatok naponta (elkülönül Naplóelemzési adatfeldolgozást), az adatok 90 napos megőrzési további költségek nélkül okozhatnak legfeljebb 200 MB. Ez a tulajdonság csak az Operations Management Suite-előfizetéssel rendelkező felhasználók alkalmazható, mert az előfizetés nélkül ügyfelek nem fogják látni a egy lehetőség, hogy válassza ki a tervet.

> [!NOTE]
> Győződjön meg arról, hogy ezt a jogosultságot kap, kell rendelkeznie az Application Insights-erőforrások vállalati árképzési terv. Ez a jogosultság lesz csak csomópontokat, az alapszintű csomag az Application Insights-erőforrások nem valósíthat meg hasznos. Vegye figyelembe, hogy ez a jogosultság nem látható lesz a becsült költség jelenik meg a *használati és a becsült költség* lap. Is ha előfizetés az új Azure árképzési modelljének előnyei. április 2018 figyelési helyezi át, az alapszintű csomag nem érhető el egyetlen terv, ha az Operations Management Suite-előfizetéssel rendelkezik nem javasoljuk.

További információk a vállalati terven látogasson el a [vállalati árképzési részleteit megjelenítő oldalra.](app-insights-pricing-enterprise-details.md)

### <a name="multi-step-web-tests"></a>Többlépéses webes tesztek

Nincs olyan kiegészítő díjat [többlépéses webteszt](app-insights-monitor-web-app-availability.md#multi-step-web-tests). A webes tesztjeinek használatát, amely egy műveletsorozatot végre vonatkozik.

Nincs "ping-vizsgálatok" egy oldal külön díjmentes. A többlépéses tesztek és ping-vizsgálatok telemetriai fel van töltve, és egyéb telemetriai az alkalmazásból.

## <a name="review-pricing-plans-and-estimate-costs"></a>Tekintse át a csomagok és a becsült költség díjszabása

Az Application Insights megkönnyíti az elérhető árképzési csomagok, és milyen költségeket valószínűleg alapulhat legutóbbi használati szokások megismerése. Megnyitásával indítsa el a **használati és a becsült költség** lapján az Application Insights-erőforrást az Azure-portálon:

![Tarifacsomag kiválasztása](./media/app-insights-pricing/pricing-001.png)

**a.** Tekintse át a adatmennyiség az adott hónap. Ez magában foglalja a fogadott és megőrzi minden adat (után bármely [mintavételi](app-insights-sampling.md) a kiszolgáló és az ügyfélalkalmazások, és rendelkezésreállás figyelésére szolgáló tesztek.

**b.** Egy külön kell fizetni történik [többlépéses webteszt](app-insights-monitor-web-app-availability.md#multi-step-web-tests). (Ebbe nem számítanak bele egyszerű rendelkezésreállás figyelésére szolgáló tesztek, amelyek szerepelnek az adatok kötet kell fizetni.)

**c.** Adatok kötet tendenciák megtekintése az elmúlt hónapban.

**d.** Engedélyezze az adatfeldolgozást [mintavételi.](app-insights-sampling.md) 

**e.** Konfigurálja a napi adatok kötet kap.

Application Insights költségek az Azure számlázásának hozzáadódnak. A számlázási szakaszban az Azure portál vagy a számlázás az Azure részleteit láthatja a [Azure Billing Portal](https://account.windowsazure.com/Subscriptions). 

![Az oldalsó menüben válassza ki a számlázási.](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>Átviteli sebesség
A kötet küldött adatok korlátozódik három módja van:

* **Mintavételi:** Ez az eljárás használható a kiszolgáló és az ügyfél alkalmazásoknak, a minimális torzulásának metrikák küldött telemetriai adatok mennyiségének csökkentésére. Ez az adatmennyiség hangolására, hogy az elsődleges eszköz. További információ [szolgáltatások mintavétel](app-insights-sampling.md). 
* **Napi cap:** Ha létrehozása az Application Insights-erőforrást az Azure portálról ennek értéke 100 GB/nap. A Visual Studio Application Insights-erőforrás létrehozásakor alapértelmezés szerint csak a tesztelés elősegítése készült kis (csak 32,3 megabájt MB/nap). Ebben az esetben célja, hogy a felhasználó emeli a napi kap éles környezetben az alkalmazás telepítése előtt. A maximális kap kért nagy forgalmú alkalmazások magasabb legfeljebb 1000 GB/nap esetén. Körültekintően járjon el a napi kap beállításakor a leképezés kell lennie **soha nem az, hogy a napi kap találati**, mert fog majd adatvesztés, a nap hátralevő és kell az alkalmazás figyelése nem lehetséges. Ha módosítani szeretné azt, a napi kötet cap beállítást használja, a használat és a becsült költség lap (lásd alább) kapcsolódó. Néhány előfizetés típusú jóváírás, amely nem használható az Application Insights rendelkező korlátozását eltávolította azt. Korábban Ha az előfizetésnek a költségkeret maximumát, napi cap párbeszédpanel lesz utasításokat távolítsa el, és a napi kap túl 32,3 megabájt MB/nap felmerülő engedélyezésével.
* **Sávszélesség-szabályozás:** ez korlátozza az egy második, átlagolt több mint 1 perc alatt 32000 események sebesség.

*Mi történik, ha az alkalmazás meghaladja a szabályozási értéket?*

* Tartalmazza az alkalmazás által percenként megfelelőségét ellenőrizni kell. Ha a percre átlagolva másodpercenkénti aránya meghaladja, a kiszolgáló visszautasítja bizonyos kérelmek. Az SDK puffereli, az adatokat, és megkísérli újraküldéséhez megugrását terjednek több perc. Az alkalmazás rendszeresen küld adatokat a fent a szabályozási értéket, ha egyes adatok ki lesznek hagyva. (Az ASP.NET, Java és JavaScript SDK-k megpróbálja újra elküldeni, így; Csomagjától előfordulhat, hogy egyszerűen az vetett szabályozott adatokat.) Sávszélesség-szabályozás történik, akkor megjelenik egy értesítés, figyelmeztetés, hogy ez történik.

*Honnan tudhatom, hogy mennyi adatot küld alkalmazásom?*

* Nyissa meg a **használati és a becsült költség** lapot, melyen megtekintheti a napi adatok kötet diagram. 
* A Metrikaböngészőben, új diagram hozzáadása, és válassza ki **adatpontok mennyisége** a metrika szerint. Csoportosítás váltson, és szerint kell csoportosítani a **adattípus**.

## <a name="to-reduce-your-data-rate"></a>Az arány csökkentése érdekében
A következőkben az adatmennyiség csökkentése érdekében teheti:

* Használjon [mintavételi](app-insights-sampling.md). Ez a technológia csökkenti az átviteli sebesség döntés a metrikákat, és keresse meg a keresési kapcsolódó elemek között olyan megszakítása nélkül. Kiszolgálói alkalmazások esetében működik automatikusan.
* [Korlátozza az jelenthetők Ajax-hívások számát](app-insights-javascript.md#detailed-configuration) minden lap nézetben vagy kapcsoló Ajax reporting ki.
* Kapcsolja ki a gyűjtemény modulok által a felesleges [ApplicationInsights.config szerkesztése](app-insights-configuration-with-applicationinsights-config.md). Például dönthet úgy, hogy teljesítményszámlálókkal és a függőségi adatokat inessential.
* Ossza fel a telemetria bekapcsolásával instrumentation kulcsok külön. 
* Előre összesített metrikákat. TrackMetric hívások vezettek az alkalmazásban, ha forgalmat csökkentheti a túlterhelést, amely fogadja a átlagának kiszámítása és a mérési köteg szórása használatával. Vagy használhat egy [előre összesítése csomag](https://www.myget.org/gallery/applicationinsights-sdk-labs).

## <a name="managing-the-maximum-daily-data-volume"></a>A maximális napi adatmennyiség kezelése

A napi kötet kap használatával korlátozható az összegyűjtött adatokat, de a kap teljesülése esetén, a nap hátralevő az alkalmazás által küldött összes telemetriai adat adatvesztést okoz. Az **nem ajánlott** szeretné, hogy az alkalmazás elérte a napi kap, mivel Ön nem tudja követni a állapotának és az alkalmazás teljesítményének után azt talált.

Ehelyett használjon [mintavételi](app-insights-sampling.md) finomhangolhatják a szintre adatmennyiség kívánja, majd használja a napi kap csak "utolsó lehetőségként", abban az esetben, ha az alkalmazás indítása telemetriai sokkal nagyobb mennyiségű váratlanul küldésekor.

Váltás a napi kap, a konfigurálási csoportban az Application Insights-erőforrás a **használati és a becsült költség** lapján kattintson **napi maximális**.

![A napi telemetriai kötet kap beállítása](./media/app-insights-pricing/pricing-003.png)

## <a name="sampling"></a>Mintavételezés
[A mintavételi](app-insights-sampling.md) egy metódust, így csökkentve a telemetriai van küldésének sebessége az alkalmazásban, ugyanakkor megmaradnak helyreállításra a kapcsolódó események megkeresését diagnosztikai keresések során, és továbbra is írásmódja megfelelő esemény száma.

Mintavételi hatékonyan csökkentheti a költségeket és a havi kvótán belül maradnak. A mintavételi algoritmus megőrzi telemetriai adatokat a kapcsolódó elemeket, hogy az, például a kereséssel, található egy adott kivételhez kapcsolódó a kérést. Az algoritmus is megfelelő számát, őrzi meg, hogy a kérelem sebességét, a kivétel díjszabás és egyéb számok a megfelelő értéke metrika Explorer látható.

Nincsenek mintavételi több űrlap.

* [Adaptív mintavételi](app-insights-sampling.md) az ASP.NET SDK-ban, amely automatikusan igazodni fog a kötet telemetriai adatot az alkalmazás által az alapértelmezett beállítás. Működést automatikusan az SDK-t a webalkalmazás, így csökken a telemetriai adatokat a hálózati forgalom. 
* *Adatfeldolgozást mintavételi* alternatív megoldás, amely működik, ahol az alkalmazásból telemetriai belép az Application Insights szolgáltatással. Ez nincs hatással az alkalmazásból küldött telemetriai adatok mennyiségét, de ez csökkenti a szolgáltatás megőrzi. Használhatja a kvóta telemetriai böngészők és egyéb SDK-k által használt csökkentése érdekében.

Adatfeldolgozást mintavételi beállítása, állítsa be a vezérlő az árazás párbeszédpanelen:

![A kvóta és árképzési párbeszédpanel kattintson a minták csempéjére, és válassza a mintavételi töredéke alatt.](./media/app-insights-pricing/pricing-004.png)

> [!WARNING]
> Az adatok mintavételi párbeszédpanel csak adatfeldolgozást mintavételi érték határozza meg. A mintavételi ráta alkalmazott az Application Insights SDK az alkalmazás által nem is. Ha a bejövő telemetriai már mintavételezett: az SDK-t, az adatfeldolgozást mintavételi nem lesz alkalmazva.
>

Annak megállapításához, a tényleges mintavételi ráta, függetlenül attól, hol van érvényben, használjon egy [Analytics lekérdezési](app-insights-analytics.md) Ez például:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

Az egyes megőrzi a rekord, `itemCount` azt jelzi, amely azt jelöli, eredeti rekordok száma egyenlő 1 + az előző elvetett rekordok száma. 

## <a name="automation"></a>Automatizálás

Írhat egy parancsfájlt a ár terv beállítása Azure Resource Manager használatával. [További tudnivalókat itt talál](app-insights-powershell.md#price).

## <a name="limits-summary"></a>Korlátozások összegzése

[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

## <a name="next-steps"></a>További lépések

* [Mintavételezés](app-insights-sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/
