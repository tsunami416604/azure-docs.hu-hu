---
title: Árak és adatmennyiségek kezelése az Azure Application Insights |} A Microsoft Docs
description: Telemetria kötetek kezelése és figyelése az Application Insights költségeit.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: Dale.Koetke
ms.date: 08/11/2018
ms.author: mbullwin
ms.openlocfilehash: a81cb9041b905cfb00183981036116fbc61f376a
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "53000872"
---
# <a name="manage-pricing-and-data-volume-in-application-insights"></a>Az Application Insights árak és adatmennyiségek kezelése

> [!NOTE]
> Ez a cikk ismerteti, hogyan kell Application Insights adathasználat elemzése.  További kapcsolódó információt a következő cikkekben talál.
> - [Használat és becsült költségek figyelése](../monitoring-and-diagnostics/monitoring-usage-and-estimated-costs.md) ismerteti, hogyan lehet megtekinteni a használati és becsült költségek figyelési funkciók eltérő díjszabási modelleket a több Azure-ban. Emellett bemutatja, hogyan lehet módosítani a díjszabási modellt.

A díjszabás [Azure Application Insights] [ start] alkalmazásonként adatmennyiség alapján. Minden Application Insights-erőforrást különálló szolgáltatásként kell fizetnie, és hozzájárul a az Azure-előfizetéshez tartozó számla.

Az Application Insights két díjcsomagok rendelkezik: alapszintű és vállalati. Az alapszintű árképzési csomag található alapértelmezett csomagjában. Tartalmazza az összes vállalati csomag szolgáltatásai, további költségek nélkül. A Basic csomag számlák elsősorban a, betöltött adatok mennyiségét. 

A vállalati csomag csomópontonkénti használati díj tartozik, és minden csomópont kap egy napi adatkeret. A vállalati díjszabási, díjkötelesek a csomagban foglalt adatkeret felett betöltött adatokért. Ha az Operations Management Suite használja, a vállalati csomagot kell választania. 

Ha az Application Insights díjszabásával kapcsolatban kérdése van, akkor is felteheti a [fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights).

## <a name="pricing-plans"></a>Díjszabások

Jelenlegi díjak a pénznem és a régiót, lásd: [Application Insights díjszabásával][pricing].

> [!NOTE]
> A 2018 április hogy [bevezetett](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) egy új díjszabási modellben az Azure monitoring. Ez a modell között a szolgáltatások teljes portfólióját fogad el egy egyszerű "használatalapú" modellt. Tudjon meg többet a [új díjszabási modell](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), hogy a [ebben a modellben való áttérés a következmények felmérésében](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model) a használati minták alapján és [hogyan választható, az új modell](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model).

### <a name="basic-plan"></a>Alapszintű csomag

Az alapszintű csomag az alapérték tarifacsomagot, ha egy új Application Insights-erőforrás jön létre. Az alapszintű csomag az optimális, kivéve azokat, aki rendelkezik az Operations Management Suite-előfizetéssel minden ügyfél számára.

* Az alapszintű csomaggal, a számlázás a adatmennyiség alapján. Adatmennyiség a telemetria az Application Insights által fogadott bájtok száma. Adatmennyiség az Application Insights által az alkalmazásból fogadott tömörítetlen JSON adatcsomag méretének mértékegysége. A [Analytics importált táblázatos adatok](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import), adatmennyiség mérjük, az Application Insightsnak elküldött fájlok tömörítetlen mérete.
* Az alkalmazás mennyiségi díjat most egy új rendszergazdát nevű jelentett **adatbetöltés** 2018. április. Ez az új fogyasztásmérő figyelési technológiák, például alkalmazások Insights és a Log Analytics között lehetnek megosztva, és jelenleg a szolgáltatás neve alatt **App Services** (és hamarosan módosítása a következőre **Log Analytics**). 
* [Élő metrikák Stream](app-insights-live-stream.md) adatok célokra díjszabás után nem kell fizetnie.
* [A folyamatos exportálás](app-insights-export-telemetry.md) és a [Azure Log Analytics-összekötő](https://go.microsoft.com/fwlink/?LinkId=833039&amp;clcid=0x409) 2018. április az alapszintű csomaggal külön díjfizetés nélkül érhető el.

### <a name="enterprise-plan-and-operations-management-suite-subscription-entitlements"></a>Vállalati csomag és az Operations Management Suite-előfizetés jogosultságai

Az Operations Management Suite E1 és E2 megvásárló ügyfeleinknek beszerezheti a további költségek nélkül, további alkatrészek, Application Insights vállalati [azt korábban bejelentettük](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/). Az Operations Management Suite E1 és E2 minden egysége, egy csomópontjához az Application Insights vállalati csomag magában foglalja. Minden Application Insights-csomópont akár 200 MB / nap (a Log Analytics-adatbetöltés külön), a további költségek nélkül 90 napos adatmegőrzés betöltött adatokat tartalmaz. A terv leírását a cikk későbbi részében részletesebben. 

Mivel ez a csomag csak az Operations Management Suite-előfizetéssel rendelkező ügyfelek számára megfelelő, az ügyfelek, akik nem rendelkezik az Operations Management Suite-előfizetéssel nem jelenik meg ebben a csomagban lehetőség.

> [!NOTE]
> Győződjön meg arról, hogy ezt a jogosultságot kap, az Application Insights-erőforrások a vállalati díjszabási kell lennie. Ez a jogosultság csak csomópontként vonatkozik. Application Insights-erőforrások az alapszintű csomagban nem is tud semmilyen. Ez a jogosultság nem látható a becsült költségek, ahogyan az a **használat és becsült költségek** ablaktáblán. Ha egy előfizetést a díjszabási modell monitorozás 2018 április az új Azure-bA helyezi át, az alapszintű csomaggal is a rendelkezésre álló csak terv. Előfizetés áthelyezése az új Azure monitorozási díjszabási modell nem javasolt, ha egy Operations Management Suite-előfizetéssel rendelkezik.

Vállalati csomaggal kapcsolatos további információkért lásd: [Enterprise díjszabás](app-insights-pricing-enterprise-details.md).

### <a name="multi-step-web-tests"></a>Többlépéses webes tesztek

[Többlépéses webes tesztek](app-insights-monitor-web-app-availability.md#multi-step-web-tests) külön díjat számítunk fel. Többlépéses webes tesztek olyan webes teszteket, amely egy műveletsorozatot végrehajtani.

Semmilyen külön díjat nem az *ping tesztek* egy oldal. A ping-vizsgálatok és a többlépéses teszt telemetriai ugyanaz, mint más az alkalmazásából származó telemetriai adatok kell fizetnie.

## <a name="review-pricing-plans-and-estimate-costs"></a>Tekintse át a díjszabási csomagokat és a becsült költség

Az Application Insights megkönnyíti a díjcsomag elérhető ismertetése, és milyen költségeit valószínűleg legutóbbi használati minták kell alapulnia. Az Azure Portalon, az Application Insights-erőforrást a kezdéshez nyissa meg a **felhasználás és becsült költségek** panelen:

![Díjszabás kiválasztása](./media/app-insights-pricing/pricing-001.png)

A. Tekintse át a továbbított adatmennyiség hónapban. Ez magában foglalja a fogadott és megőrzött adatok (után bármely [mintavételi](app-insights-sampling.md)) az a kiszolgáló és az ügyfélalkalmazások, illetve az rendelkezésre állási tesztet.  
B. Kérés érkezett egy külön díjat számítunk fel [többlépéses webes tesztek](app-insights-monitor-web-app-availability.md#multi-step-web-tests). (Egyszerű rendelkezésre állási teszteket, amelyek szerepelnek a mennyiségi adatok díj nem tartalmazza a.)  
C. Adatok mennyiségi trendek megjelenítése az elmúlt hónapban.  
D. Engedélyezze az adatbetöltés [mintavételi](app-insights-sampling.md).   
E. Állítsa be a napi adatmennyiségre.  

Application Insights díjak az Azure-elszámolások kerülnek. A számlázás az Azure részleteit láthatja a **számlázási** az Azure Portalon, vagy a szakasz a [Azure számlázási portálján](https://account.windowsazure.com/Subscriptions). 

![A bal oldali menüben válassza a számlázás](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>Adatsebesség
A küldött adatok mennyisége korlátozott három módon:

* **Mintavételi**: mintavételi segítségével csökkentheti a kiszolgáló és az ügyfél alkalmazásoknak, a mérőszámok minimális torzulást által küldött telemetriát. Az elsődleges eszköz, amellyel küldött adatok mennyisége finomhangolása adatokra mintavétel alkalmazva. Tudjon meg többet [szolgáltatások mintavétel](app-insights-sampling.md). 
* **Maximális napi adatmennyiséget**: Application Insights-erőforrás létrehozásakor az Azure Portalon a napi korlát 100 GB-os napi értékre van-e állítva. A Visual Studio Application Insights-erőforrás létrehozásakor az alapértelmezett érték a kicsi (csak 32,3 MB/nap). A napi korlát alapértelmezett értéke a tesztelés elősegítése érdekében. Célja, hogy a felhasználó az alkalmazás éles környezetbe való üzembe helyezés előtt kiváltja az a maximális napi adatmennyiséget. 

    A maximális korlát 1000 GB/nap, kivéve, ha nagy forgalmú alkalmazásokhoz magasabb maximális kér. 

    Körültekintően járjon el a napi korlát beállításakor. A leképezés kell lennie a *soha nem eléri a maximális napi adatmennyiséget*. Ha eléri a maximális napi adatmennyiséget, elveszíti a nap fennmaradó részében adatait, és nem figyelheti az alkalmazást. A napi korlát módosításához használja a **napi mennyiségi korlát** lehetőséget. Ezt a lehetőséget is elérheti a **felhasználás és becsült költségek** (ezt részletesen ismerteti a cikk későbbi részében részletesebben) panelen.
    A korlátozás némi kreditjeiket, amely nem használható az Application Insights típusú előfizetésessel is eltávolítottuk. Korábban Ha az előfizetés a költségkeret van, a napi korlát párbeszédpanel utasításokat követve el kell távolítania a költségkeretet, és engedélyezheti a napi korlátot túli 32,3 MB/nap hatványra emelendő rendelkezik.
* **Szabályozás**: 32 000 esemény / másodperc, hogy az adatokra vonatkozó szabályozási korlátait átlagolt kialakítási kulcsonként több mint 1 perce.

*Mi történik, ha az alkalmazás túllépi a sávszélesség-szabályozási arány?*

* Az alkalmazás által küldött adatok mennyisége percenként adatokon. Ha az percalapú átlagolva másodpercenkénti aránya túllépi, a kiszolgáló megtagadja bizonyos kérelmek. Az SDK puffereli az adatokat, és ezután próbálja meg újból elküldeni. Terjeszti ki a tároló több perc. Az alkalmazás rendszeresen több, mint a sávszélesség-szabályozási arány, adatokat küld, ha bizonyos adatok ki lesznek hagyva. (Az ASP.NET, Java és JavaScript SDK-k próbálja meg újra elküldeni az adatokat ezzel a módszerrel, más SDK-k előfordulhat, hogy egyszerűen dobja el a szabályozott adatokat.) Szabályozás akkor fordul elő, ha egy értesítési figyelmeztetés figyelmezteti, hogy ez történt.

*Honnan tudhatom, hogy az alkalmazás által küldött adatok mennyiségét?*

Tekintse meg az alkalmazás által küldött adatok mennyiségét, használhatja az alábbi lehetőségek közül:

* Nyissa meg a **használat és becsült költségek** megtekintéséhez a napi mennyiségi Diagram ablaktábla. 
* A Metrikaböngészőben új diagram hozzáadásához. Válassza ki a diagram metrika **adatpontok mennyisége**. Kapcsolja be a **csoportosítási**, és ezután csoportosítási **adattípus**.

## <a name="reduce-your-data-rate"></a>Csökkentse az arány
Az alábbiakban néhány műveletet is végezhet a továbbított adatmennyiség csökkentése érdekében:

* Használat [mintavételi](app-insights-sampling.md). Ez a technológia csökkenti az arány nélkül eltorzítják a metrikákat. Ne veszítse el a keresés kapcsolódó elemek között lehetővé teszi. A kiszolgáló-alkalmazások a mintavételi automatikusan működik.
* [Jelenteni lehet Ajax-hívások számának korlátozása](app-insights-javascript.md#detailed-configuration) minden lapmegtekintés, vagy kapcsoló Ajax reporting ki.
* [Szerkessze az applicationinsights.config fájlt](app-insights-configuration-with-applicationinsights-config.md) tiltsa le az adatgyűjtő modulok, amelyeket nem kell. Például dönthet úgy, hogy a teljesítményszámlálók vagy a függőségi adatok legyenek inessential.
* Ossza fel a telemetriai adatokat, többek között a különböző kialakítási kulcsokat. 
* Előre összesített metrikái. Ha TrackMetric hívásainak helyezi az alkalmazást, a túlterhelés, amely az átlagos számítási és a szórást mérések a Batch használatával is csökkenthető a forgalom. Vagy használhat egy [előre összesítése csomag](https://www.myget.org/gallery/applicationinsights-sdk-labs).

## <a name="manage-the-maximum-daily-data-volume"></a>A maximális napi adatmennyiség kezelése

A napi mennyiségi korlát segítségével gyűjtött adatok körét. Azonban ha teljesül a korlátot, a nap fennmaradó az alkalmazás által küldött összes telemetriai adat elvesztése következik be. Ez *nem tanácsos* szeretné, hogy az alkalmazás eléri a maximális napi adatmennyiséget. Ön nem tudja nyomon követni állapotát és teljesítményét az alkalmazás után eléri a maximális napi adatmennyiséget.

A napi mennyiségi korlát ahelyett, [mintavételi](app-insights-sampling.md) finomhangolása az adatmennyiség a kívánt szintre. Ezután használja a napi korlát csak "végső megoldásként" abban az esetben, ha az alkalmazás váratlanul elkezdi sokkal nagyobb mennyiségű telemetria küldése.

Változik a maximális napi adatmennyiséget, a **konfigurálása** szakaszában az Application Insights-erőforrást, a a **felhasználás és becsült költségek** ablaktáblán válassza **napi korlát**.

![A telemetriai adatok napi mennyiségi korlát beállítása](./media/app-insights-pricing/pricing-003.png)

## <a name="sampling"></a>Mintavételezés
[Mintavételi](app-insights-sampling.md) csökkenti a sebesség, amellyel telemetriája el lesz küldve az alkalmazáshoz, miközben továbbra is kereshet a kapcsolódó események során diagnosztikai kereséseket módot. Is megőrzik a megfelelő események számát.

Mintavétel alkalmazva. Csökkentse a díjak, és megelőzheti a havi kvóta hatékony módszert. A mintavételezési algoritmus megtartja a kapcsolódó elemek telemetria tehát, például keresési használatakor talál egy adott kivétel kapcsolódó a kérést. Az algoritmus is őrzi meg a helyes számát, így a megfelelő értékeket a Metrikaböngésző kérelemarányok, kivétel díjszabás és egyéb száma látható.

Nincsenek mintavételi több űrlap.

* [Az adaptív mintavételezési](app-insights-sampling.md) az ASP.NET SDK az alapértelmezett érték. Az adaptív mintavételezési automatikusan igazodik az alkalmazás által küldött telemetriai adatok mennyisége. Működést automatikusan a webalkalmazás az SDK-t, hogy a hálózati forgalom telemetriai csökken. 
* *Betöltési mintavételt* alternatív megoldás, amely a ponton az alkalmazásából származó telemetriai adatok kerül, ahol az Application Insights szolgáltatás működik. Betöltési mintavételt az alkalmazásból küldött telemetriai adatok mennyisége nincs hatással, de a kötetet, a szolgáltatás által megőrzött csökkenti. A használati adatok gyűjtése a böngészők és más SDK-k által használt kvóta csökkentése érdekében használhatja a betöltési mintavételt.

Állítsa be a betöltési mintavételt, lépjen a **díjszabási** panelen:

![A kvóta és díjszabás panel válassza a mintákat, és kattintson a mintavételi tört](./media/app-insights-pricing/pricing-004.png)

> [!WARNING]
> A **adat-mintavételezés** ablaktáblán azt szabályozza, csak a betöltési mintavételt értéket. A mintavételi ráta az alkalmazott az Application Insights SDK az alkalmazás nem is. Ha a bejövő telemetriát már az SDK felhasználásához mintavételezett, nem alkalmazza a betöltési mintavételt.
>

Fedezze fel a tényleges mintavételi ráta, függetlenül attól, hogy, ahol már alkalmazva van, használja az [elemzési lekérdezés](app-insights-analytics.md). A lekérdezés a következőhöz hasonló:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

Az egyes őrzi meg a rekord, `itemCount` azt jelzi, hogy ez a jelölő eredeti rekordok száma. 1 + előző elvetett rekordok száma egyenlő legyen. 

## <a name="automation"></a>Automation

A parancsfájl a tarifacsomag beállítása az Azure Resource Management használatával írhat. [További tudnivalókat itt talál](app-insights-powershell.md#price).

## <a name="limits-summary"></a>Korlátozások összegzése

[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>Napi korlát e-mailek letiltása

A napi mennyiségi korlát e-mailek, a letiltásához a **konfigurálása** szakaszában az Application Insights-erőforrást, a a **felhasználás és becsült költségek** ablaktáblán válassza **napi korlát** . E-mail küldése, ha a korlát elérése esetén egy állítható figyelmeztetési szint elérésekor, valamint a beállításokat a rendszer. Ha letiltja az összes napi korlát kötettel kapcsolatos, e-mailek törölje mindkét mezőbe.

## <a name="next-steps"></a>További lépések

* [Mintavételezés](app-insights-sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/