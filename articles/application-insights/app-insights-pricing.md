---
title: Használat és költségek kezelése az Azure Application Insights |} A Microsoft Docs
description: Telemetria kötetek kezelése és figyelése az Application Insights költségeit.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.reviewer: Dale.Koetke
ms.date: 12/21/2018
ms.author: mbullwin
ms.openlocfilehash: a65db0dd21c2a5f85b1818f02031f1fb08ac564b
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53969913"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>Használat és költségek kezelése az Application Insights

> [!NOTE]
> Ez a cikk ismerteti, hogyan kell Application Insights adathasználat elemzése.  További kapcsolódó információt a következő cikkekben talál.
> - [Használat és becsült költségek figyelése](../monitoring-and-diagnostics/monitoring-usage-and-estimated-costs.md) ismerteti, hogyan lehet megtekinteni a használati és becsült költségek figyelési funkciók eltérő díjszabási modelleket a több Azure-ban. Emellett bemutatja, hogyan lehet módosítani a díjszabási modellt.

Ha az Application Insights díjszabásával kapcsolatban kérdése van, akkor is felteheti a [fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights).

## <a name="pricing-model"></a>Díjszabási modell

A díjszabás [Azure Application Insights] [ start] betöltött adatmennyiség alapján. Minden Application Insights-erőforrást különálló szolgáltatásként kell fizetnie, és hozzájárul a az Azure-előfizetéshez tartozó számla.

### <a name="data-volume-details"></a>Adatmennyiség részleteivel

* Adatmennyiség a telemetria az Application Insights által fogadott bájtok száma. Adatmennyiség az Application Insights által az alkalmazásból fogadott tömörítetlen JSON adatcsomag méretének mértékegysége. A [Analytics importált táblázatos adatok](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import), adatmennyiség mérjük, az Application Insightsnak elküldött fájlok tömörítetlen mérete.
* Az alkalmazás mennyiségi díjat most egy új rendszergazdát nevű jelentett **adatbetöltés** 2018. április. Ez az új fogyasztásmérő figyelési technológiák, például alkalmazások Insights és a Log Analytics között lehetnek megosztva, és jelenleg a szolgáltatás neve alatt **Log Analytics**. 
* [Élő metrikák Stream](app-insights-live-stream.md) adatok célokra díjszabás után nem kell fizetnie.

Jelenlegi díjak a pénznem és a régiót, lásd: [Application Insights díjszabásával][pricing].

### <a name="multi-step-web-tests"></a>Többlépéses webes tesztek

[Többlépéses webes tesztek](../azure-monitor/app/monitor-web-app-availability.md#multi-step-web-tests) külön díjat számítunk fel. Többlépéses webes tesztek olyan webes teszteket, amely egy műveletsorozatot végrehajtani.

Semmilyen külön díjat nem az *ping tesztek* egy oldal. A ping-vizsgálatok és a többlépéses teszt telemetriai ugyanaz, mint más az alkalmazásából származó telemetriai adatok kell fizetnie.

## <a name="review-usage-and-estimate-costs"></a>Tekintse át a használat és becsült költség

Az Application Insights megkönnyíti a megismeréséhez költségeit valószínűleg a legutóbbi használati minták alapján lehet. Az Azure Portalon, az Application Insights-erőforrást a kezdéshez nyissa meg a **felhasználás és becsült költségek** oldalon:

![Díjszabás kiválasztása](./media/app-insights-pricing/pricing-001.png)

A. Tekintse át a továbbított adatmennyiség hónapban. Ez magában foglalja a fogadott és megőrzött adatok (után bármely [mintavételi](app-insights-sampling.md)) az a kiszolgáló és az ügyfélalkalmazások, illetve az rendelkezésre állási tesztet.  
B. Kérés érkezett egy külön díjat számítunk fel [többlépéses webes tesztek](../azure-monitor/app/monitor-web-app-availability.md#multi-step-web-tests). (Egyszerű rendelkezésre állási teszteket, amelyek szerepelnek a mennyiségi adatok díj nem tartalmazza a.)  
C. Adatok mennyiségi trendek megjelenítése az elmúlt hónapban.  
D. Engedélyezze az adatbetöltés [mintavételi](app-insights-sampling.md).   
E. Állítsa be a napi adatmennyiségre.  

Így vizsgálja meg az Application Insights – használat, nyissa meg a **metrikák** lapon adja hozzá a metrika elnevezett "pont adatmennyiség", és válassza ki a *alkalmazni a felosztás* lehetőség szerint a "telemetrikus adatok felosztása elem típusa". 

Application Insights díjak az Azure-elszámolások kerülnek. A számlázás az Azure részleteit láthatja a **számlázási** az Azure Portalon, vagy a szakasz a [Azure számlázási portálján](https://account.windowsazure.com/Subscriptions). 

![A bal oldali menüben válassza a számlázás](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>Adatsebesség
A küldött adatok mennyisége korlátozott három módon:

* **Mintavételi**: Mintavételi segítségével csökkentheti a kiszolgáló és az ügyfélalkalmazások, a mérőszámok minimális torzulást által küldött telemetriát. Az elsődleges eszköz, amellyel küldött adatok mennyisége finomhangolása adatokra mintavétel alkalmazva. Tudjon meg többet [szolgáltatások mintavétel](app-insights-sampling.md). 
* **Maximális napi adatmennyiséget**: Application Insights-erőforrás létrehozásakor az Azure Portalon a napi maximális értéke 100 GB/nap. A Visual Studio Application Insights-erőforrás létrehozásakor az alapértelmezett érték a kicsi (csak 32,3 MB/nap). A napi korlát alapértelmezett értéke a tesztelés elősegítése érdekében. Célja, hogy a felhasználó az alkalmazás éles környezetbe való üzembe helyezés előtt kiváltja az a maximális napi adatmennyiséget. 

    A maximális korlát 1000 GB/nap, kivéve, ha nagy forgalmú alkalmazásokhoz magasabb maximális kér. 

    Körültekintően járjon el a napi korlát beállításakor. A leképezés kell lennie a *soha nem eléri a maximális napi adatmennyiséget*. Ha eléri a maximális napi adatmennyiséget, elveszíti a nap fennmaradó részében adatait, és nem figyelheti az alkalmazást. A napi korlát módosításához használja a **napi mennyiségi korlát** lehetőséget. Ezt a lehetőséget is elérheti a **felhasználás és becsült költségek** (ezt részletesen ismerteti a cikk későbbi részében részletesebben) panelen.
    A korlátozás némi kreditjeiket, amely nem használható az Application Insights típusú előfizetésessel is eltávolítottuk. Korábban Ha az előfizetés a költségkeret van, a napi korlát párbeszédpanel utasításokat követve el kell távolítania a költségkeretet, és engedélyezheti a napi korlátot túli 32,3 MB/nap hatványra emelendő rendelkezik.
* **Szabályozás**: Szabályozási korlátait a adatokra vonatkozó 32 000 esemény / másodperc, hogy átlagolni kialakítási kulcsonként több mint 1 perce.

*Mi történik, ha az alkalmazás túllépi a sávszélesség-szabályozási arány?*

* Az alkalmazás által küldött adatok mennyisége percenként adatokon. Ha az percalapú átlagolva másodpercenkénti aránya túllépi, a kiszolgáló megtagadja bizonyos kérelmek. Az SDK puffereli az adatokat, és ezután próbálja meg újból elküldeni. Terjeszti ki a tároló több perc. Az alkalmazás rendszeresen több, mint a sávszélesség-szabályozási arány, adatokat küld, ha bizonyos adatok ki lesznek hagyva. (Az ASP.NET, Java és JavaScript SDK-k próbálja meg újra elküldeni az adatokat ezzel a módszerrel, más SDK-k előfordulhat, hogy egyszerűen dobja el a szabályozott adatokat.) Szabályozás akkor fordul elő, ha egy értesítési figyelmeztetés figyelmezteti, hogy ez történt.

*Honnan tudhatom, hogy az alkalmazás által küldött adatok mennyiségét?*

Tekintse meg az alkalmazás által küldött adatok mennyiségét, használhatja az alábbi lehetőségek közül:

* Nyissa meg a **használat és becsült költségek** megtekintéséhez a napi mennyiségi Diagram ablaktábla. 
* A Metrikaböngészőben új diagram hozzáadásához. Válassza ki a diagram metrika **adatpontok mennyisége**. Kapcsolja be a **csoportosítási**, és ezután csoportosítási **adattípus**.

## <a name="reduce-your-data-rate"></a>Csökkentse az arány
Az alábbiakban néhány műveletet is végezhet a továbbított adatmennyiség csökkentése érdekében:

* Használat [mintavételi](app-insights-sampling.md). Ez a technológia csökkenti az arány nélkül eltorzítják a metrikákat. Ne veszítse el a keresés kapcsolódó elemek között lehetővé teszi. A kiszolgáló-alkalmazások a mintavételi automatikusan működik.
* [Jelenteni lehet Ajax-hívások számának korlátozása](../azure-monitor/app/javascript.md#detailed-configuration) minden lapmegtekintés, vagy kapcsoló Ajax reporting ki.
* [Szerkessze az applicationinsights.config fájlt](../azure-monitor/app/configuration-with-applicationinsights-config.md) tiltsa le az adatgyűjtő modulok, amelyeket nem kell. Például dönthet úgy, hogy a teljesítményszámlálók vagy a függőségi adatok legyenek inessential.
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

## <a name="legacy-enterprise-pricing-plan"></a>Örökölt nagyvállalati díjszabási csomag

Az Azure Application Insights korai felhasználók tesztelik, továbbra is vannak két lehetséges két tarifacsomagjairól készült: Alapszintű és vállalati. Az alapszintű árképzési csomag megegyezik a fent leírt, és az alapértelmezett csomag. Tartalmazza az összes vállalati csomag szolgáltatásai, további költségek nélkül. A Basic csomag számlák elsősorban a, betöltött adatok mennyiségét. 

A vállalati csomag csomópontonkénti használati díj tartozik, és minden csomópont kap egy napi adatkeret. A vállalati díjszabási, díjkötelesek a csomagban foglalt adatkeret felett betöltött adatokért. Ha az Operations Management Suite használ, a vállalati csomagot kell választania. 

Jelenlegi díjak a pénznem és a régiót, lásd: [Application Insights díjszabásával](https://azure.microsoft.com/pricing/details/application-insights/).

> [!NOTE]
> A 2018 április hogy [bevezetett](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) egy új díjszabási modellben az Azure monitoring. Ez a modell között a szolgáltatások teljes portfólióját fogad el egy egyszerű "használatalapú" modellt. Tudjon meg többet a [új díjszabási modell](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), hogy a [ebben a modellben való áttérés a következmények felmérésében](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model) a használati minták alapján és [hogyan választható, az új modell](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model)

### <a name="enterprise-plan-and-operations-management-suite-subscription-entitlements"></a>Vállalati csomag és az Operations Management Suite-előfizetés jogosultságai

Az Operations Management Suite E1 és E2 megvásárló ügyfeleinknek beszerezheti a további költségek nélkül, további alkatrészek, Application Insights vállalati [azt korábban bejelentettük](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/). Az Operations Management Suite E1 és E2 minden egysége, egy csomópontjához az Application Insights vállalati csomag magában foglalja. Minden Application Insights-csomópont akár 200 MB / nap (a Log Analytics-adatbetöltés külön), a további költségek nélkül 90 napos adatmegőrzés betöltött adatokat tartalmaz. A terv leírását a cikk későbbi részében részletesebben. 

Mivel ez a csomag csak az Operations Management Suite-előfizetéssel rendelkező ügyfelek számára megfelelő, az ügyfelek, akik nem rendelkezik az Operations Management Suite-előfizetéssel nem jelenik meg ebben a csomagban lehetőség.

> [!NOTE]
> Győződjön meg arról, hogy ezt a jogosultságot kap, az Application Insights-erőforrások a vállalati díjszabási kell lennie. Ez a jogosultság csak csomópontként vonatkozik. Application Insights-erőforrások az alapszintű csomagban nem is tud semmilyen. Ez a jogosultság nem látható a becsült költségek, ahogyan az a **használat és becsült költségek** ablaktáblán. Ha egy előfizetést a díjszabási modell monitorozás 2018 április az új Azure-bA helyezi át, az alapszintű csomaggal is a rendelkezésre álló csak terv. Előfizetés áthelyezése az új Azure monitorozási díjszabási modell nem javasolt, ha egy Operations Management Suite-előfizetéssel rendelkezik.

### <a name="how-the-enterprise-plan-works"></a>A vállalati csomag működése

* Minden egyes csomópont, amely az alkalmazások telemetriai adatokat küld a nagyvállalati csomaghoz kell fizetnie.
 * A *csomópont* egy fizikai vagy virtuális gép vagy a platform--szolgáltatásként szerepkörpéldány, amelyen az alkalmazást.
 * Fejlesztői gépek, az ügyfelek böngészőin és a mobileszközök nem számítanak csomópontnak is.
 * Ha az alkalmazás által küldött telemetriai adatok, például egy webszolgáltatás és a egy háttérbeli feldolgozó több összetevőt az összetevőket külön-külön számoljuk.
 * [Élő metrikák Stream](app-insights-live-stream.md) adatok célokra díjszabás után nem kell fizetnie. Az előfizetéshez a költségek csomópontonként, nem az egyes alkalmazások vannak. Ha 12 a telemetriai adatokat küldő öt csomóponttal rendelkezik alkalmazások, a díj az öt csomópont van.
* Bár a havi díjak korlátozott kell fizetnie, csak az adott órán belül, ahol egy csomópont telemetriai adatokat küld egy alkalmazásból. A óradíjat számítunk fel a határolójeles havi díj 744 (31 napos hónap órák száma) elosztva.
* Egy kötet adatelosztás 200 MB / nap van megadva, az egyes csomópontok (az óránkénti részletességgel) észlelt. Nem használt adatok foglalási nem veszi át egy napot a következő.
 * Ha a vállalati díjszabási, az egyes előfizetésekhez beolvasása egy napi adatkeret meg, hogy telemetriát küldjön az Application Insights-erőforrások, az adott előfizetésben csomópontok száma alapján. Tehát ha öt csomópont által küldött adatokat minden nap, fog összevont kerete 1 GB-os alkalmazandó az összes Application Insights-erőforrások, az adott előfizetésben. Nem számít, hogy egyes csomópontok más csomópontoknál több adatot küldeni, mert a szolgáltatási keretbe foglalt adatmennyiség megoszlik az összes csomópont. Ha az adott napon, az Application Insights-erőforrások kapja meg több adatot tartalmaz a napi szintű adatelosztás ehhez az előfizetéshez, a kereten túli GB-onkénti díjak érvényesek. 
 * A napi adatkeretet számítjuk ki, hogy a nap (UTC használatával) órák száma, hogy egyes csomópontok 200 MB-TAL megszorozza 24 osztva telemetriát küld-e. Tehát ha négy csomóponton, a nap 24 órás 15 telemetriai adatokat küldő, a szolgáltatási keretbe foglalt adatmennyiség az adott napra lenne ((4 &#215; 15) / 24) &#215; 200 MB = 500 MB. Az adatok keretet GB-onként 2.30-as USD díj ellenében a díjat, ha a csomópontok 1 GB adat küldése adott napon lenne 1,15 USD-t.
 * A vállalati csomag napi adatkeret ne oszthassák meg olyan alkalmazások, amelynek az alapszintű csomagot választotta. A fel nem használt juttatás nem vihetők el egymástól. 

### <a name="examples-of-how-to-determine-distinct-node-count"></a>Példák a különálló csomópontok száma meghatározása

| Forgatókönyv                               | Teljes napi csomópontok száma |
|:---------------------------------------|:----------------:|
| 1 alkalmazás 3 Azure App Service-példányt és 1 virtuális kiszolgáló használatával | 4 |
| 2 virtuális gépen; 3 alkalmazásokról ezekhez az alkalmazásokhoz az Application Insights-erőforrások a következők ugyanahhoz az előfizetéshez, és a nagyvállalati csomaghoz | 2 | 
| 4 alkalmazások, amelyek Applications Insights-erőforrások ugyanabban az előfizetésben; vannak minden egyes 16 csúcsidőn 2 példány, és 8 csúcsidőben 4 példányok futó alkalmazás | 13.33 | 
| 1 feldolgozói szerepkör és 1 webes szerepkör, minden egyes 2 példánya fut a cloud services | 4 | 
| Az Azure Service Fabric-fürt 5 csomópontos 50 mikroszolgáltatások; fut Mindegyik mikroszolgáltatás 3 példánya fut | 5|

* A pontos csomópontok számában attól függ, mely az Application Insights SDK az alkalmazás használatával. 
  * Az SDK 2.2 és újabb verziók, az Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) és a [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) jelentés minden egyes csomópontot gazda. Például a fizikai kiszolgáló és a Virtuálisgép-gazdagépek a számítógép nevét vagy a példány nevét, a cloud services.  Az egyetlen kivétel, csak használó alkalmazás a [.NET Core](https://dotnet.github.io/) és az Application Insights Core SDK-t. Ebben az esetben csak egy csomópont jelentett minden gazdagép esetén, mert az állomás neve nem érhető el. 
  * Az SDK korábbi verziói a [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) viselkedik az SDK újabb verziók, de a [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) jelentések csak egy csomópont, alkalmazás-gazdagépekkel számától függetlenül. 
  * Ha az alkalmazás az SDK-t beállítani **roleInstance** egyéni értékké, alapértelmezés szerint, hogy ugyanazt az értéket meghatározására szolgál csomópontok száma. 
  * Ha egy új SDK-verziót használja, hogy fut az ügyfélgépek és a mobileszközök, a csomópontok száma egy számot, amely nagyon nagy (miatt az ügyfélgépek és a mobileszközök nagy számú) előfordulhat, hogy vissza. 

## <a name="next-steps"></a>További lépések

* [Mintavételezés](app-insights-sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/