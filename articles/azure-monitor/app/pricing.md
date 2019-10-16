---
title: Az Azure Application Insights használatának és költségeinek kezelése | Microsoft Docs
description: Telemetria-kötetek kezelése és a költségek figyelése Application Insightsban.
services: application-insights
documentationcenter: ''
author: DaleKoetke
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 10/03/2019
ms.author: dalek
ms.openlocfilehash: 992bc7acbb02dcdbc33d00349ddc7ae821f3ed01
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329453"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>A Application Insights használatának és költségeinek kezelése

> [!NOTE]
> Ez a cikk a Application Insights költségeinek megismerését és szabályozását ismerteti.  A kapcsolódó cikkek, a [monitorozási használat és a becsült költségek](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) azt írják le, hogyan lehet megtekinteni a használati és becsült költségeket több Azure-figyelési funkció között különböző díjszabási modellekhez.

Application Insights úgy lett kialakítva, hogy a webalkalmazások rendelkezésre állásának, teljesítményének és használatának nyomon követéséhez szükséges mindent meg lehessen figyelni, függetlenül attól, hogy az Azure-ban vagy a helyszínen vannak tárolva. Application Insights támogatja a népszerű nyelveket és keretrendszerek, például a .NET, a Java és a Node. js használatát, és integrálható a DevOps-folyamatokkal és-eszközökkel, például az Azure DevOps, a JIRA és a PagerDuty. Fontos tisztában lenni azzal, hogy mi határozza meg az alkalmazások monitorozásának költségeit. Ebben a cikkben áttekintjük, hogy mi vezet az alkalmazás figyelési költségeihez, és hogyan lehet proaktívan figyelni és vezérelni azokat.

Ha kérdése van a Application Insights díjszabásával kapcsolatban, tegye fel kérdéseit a [fórumunkat](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights&filter=alltypes&sort=lastpostdesc).

## <a name="pricing-model"></a>Díjszabási modell

Az [Azure Application Insights][start] díjszabása egy **utólagos** elszámolású modell, amely a betöltött adatmennyiségen és opcionálisan a hosszú adatmegőrzésen alapul. Minden Application Insights erőforrás külön szolgáltatásként lesz felszámítva, és hozzájárul az Azure-előfizetéshez tartozó számlához. Az adatmennyiséget a rendszer az alkalmazásból Application Insights által fogadott, tömörítetlen JSON-adatcsomag méretének megfelelően méri. A [élő metrikastream](../../azure-monitor/app/live-stream.md)használatához nincs adatmennyiség.

A [többlépéses webes tesztek](../../azure-monitor/app/availability-multistep.md) felár ellenében merülhetnek fel. A többlépéses webes tesztek olyan webes tesztek, amelyek műveletek sorozatát hajtják végre. Egyetlen oldal *pingelési tesztei* esetében nincs külön díj. A ping tesztekből és a többlépéses tesztekből származó telemetria az alkalmazás más telemetria azonos módon kell fizetni.

## <a name="estimating-the-costs-to-manage-your-application"></a>Az alkalmazás kezelésével kapcsolatos költségek becslése 

Ha még nem használja az Application Insights-t, a [Azure monitor árképzési számológép](https://azure.microsoft.com/pricing/calculator/?service=monitor) használatával megbecsülheti a Application Insights használatának költségeit. Először írja be a "Azure Monitor" kifejezést a keresőmezőbe, és kattintson az eredményül kapott Azure Monitor csempére. Görgessen le az oldalról Azure Monitorre, majd válassza a legördülő menüből a Application Insights lehetőséget.  Itt adhatja meg, hogy hány GB-nyi adatot szeretne gyűjteni havonta, így a kérdés az, hogy mennyi adat fog Application Insights gyűjteni az alkalmazás figyelésére. 

Ennek a megoldásnak két megközelítése van: az alapértelmezett monitorozás és az adaptív mintavételezés használata, amely a ASP.NET SDK-ban érhető el, vagy estimtate az adatfeldolgozást a hasonló ügyfelek által látott likley alapján. 

### <a name="data-collection-when-using-sampling"></a>Adatgyűjtés mintavételezéskor

A ASP.NET SDK [adaptív mintavételezésével](https://docs.microsoft.com/azure/azure-monitor/app/sampling#adaptive-sampling-in-your-aspnetaspnet-core-web-applications)az adatmennyiség automatikusan módosul, hogy az alapértelmezett Application Insights figyeléshez megadott maximális adatforgalomon belül maradjon. Ha az alkalmazás alacsony telemetria (például hibakeresés vagy alacsony kihasználtság miatt) hoz létre, akkor a mintavételi processzor nem távolítja el az elemeket, feltéve, hogy a kötet nem éri el a beállított események másodpercenkénti szintjét. A nagy mennyiségű alkalmazás esetében az 5 eseménynél az alapértelmezett küszöbérték másodpercenként az adaptív mintavételezés a napi események számát 432 000-re korlátozza. Az 1 KB-os átlagos esemény-méretet használva ez az alkalmazást üzemeltető csomópontok 31 napos telemetria 13,4 GB-os, a mintavételezést pedig az egyes csomópontok esetében a helyi gépen végezheti el. 

Olyan SDK-k esetében, amelyek nem támogatják az adaptív mintavételezést, olyan betöltési [mintavételezést](https://docs.microsoft.com/azure/azure-monitor/app/sampling#ingestion-sampling) is alkalmazhat, amely a Application Insights az adatmennyiséget a megőrizni kívánt receved alapján, vagy a [ASP.NET, a ASP.net Core és a Java esetében rögzített sebességű mintavételezéssel. webhelyek](https://docs.microsoft.com/azure/azure-monitor/app/sampling#fixed-rate-sampling-for-aspnet-aspnet-core-and-java-websites) a webkiszolgálóról és a webböngészőkből eljuttatott forgalom csökkentéséhez

### <a name="learn-from-what-similar-customers-collect"></a>Ismerje meg, milyen hasonló ügyfelek gyűjtenek

Ha a Application Insights Azure monitoring díjszabási számológépében engedélyezi az "adatmennyiség becslése az alkalmazási tevékenység alapján" funkciót, akkor az alkalmazáshoz tartozó bemeneti adatokat (havi kérelmek száma havonta és a havonta megjelenő nézeteket) is megadhatja, amennyiben Gyűjtse össze az ügyféloldali telemetria), majd a számológép közli a hasonló alkalmazások által összegyűjtött adatmennyiség középértékét és kilencven százalékát. Természetesen ezek a elküldéséhez kiterjedhetnek Application Insights konfiguráció tartományára (például néhány alapértelmezett [mintavételezéssel](../../azure-monitor/app/sampling.md), némelyikük nem tartalmaz mintavételezést stb.), így továbbra is szabályozhatja, hogy az Ön által használt adatmennyiség csökkenthető legyen a medián szint alatt, a mintavételezés használatával. Ez azonban egy kiindulási pont, amelyből megismerheti, hogy milyen más, hasonló ügyfelek látják. 

## <a name="understand-your-usage-and-estimate-costs"></a>A használati és becsült költségek megismerése

A Application Insights segítségével könnyen megismerheti, hogy milyen költségek várhatók a legutóbbi használati szokások alapján. Első lépésként a Azure Portal Application Insights erőforráshoz lépjen a **használati és becsült költségek** lapra: 

![Díjszabás kiválasztása](./media/pricing/pricing-001.png)

A. Tekintse át az adatmennyiséget a hónapban. Ebbe beletartozik az összes kapott és megőrzött adat (a [mintavételezés](../../azure-monitor/app/sampling.md)után) a kiszolgálóról és az ügyfélalkalmazások és a rendelkezésre állási tesztek közül.  
B. A [többlépéses webes tesztekért](../../azure-monitor/app/availability-multistep.md)külön díjat kell fizetni. (Ez nem tartalmazza az egyszerű rendelkezésre állási teszteket, amelyek az adatmennyiség-feltöltés részét képezik.)  
C. Az elmúlt hónap adatmennyiség-trendjeinek megtekintése.  
D. Adatfeldolgozási [mintavételezés](../../azure-monitor/app/sampling.md)engedélyezése.   
E. Állítsa be a napi adatmennyiség korlátját.  

(Vegye figyelembe, hogy az ebben a cikkben szereplő képernyőképeken megjelenő összes ár csak példaként szolgál. A pénznem és a régió aktuális áraival kapcsolatban lásd: [Application Insights díjszabása][pricing].)

A Application Insights használatának mélyebb vizsgálatához nyissa meg a **metrikák** lapot, adja hozzá az "adatpont kötete" nevű metrikát, majd válassza a *felosztás alkalmazása* lehetőséget az adatok "telemetria-elem típusa" szerinti felosztásához. 

Application Insights díjak hozzáadódnak az Azure-számlához. Az Azure-számlázás részleteit a Azure Portal **Számlázási** szakaszában vagy az [Azure számlázási portálján](https://account.windowsazure.com/Subscriptions)tekintheti meg. 

![A bal oldali menüben válassza a számlázás lehetőséget.](./media/pricing/02-billing.png)

## <a name="viewing-application-insights-usage-on-your-azure-bill"></a>Application Insights használatának megtekintése az Azure-számlán 

Az Azure nagyszerű hasznos funkciókat biztosít a [Azure Cost Management + számlázási](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) központban. A "Cost Analysis" funkció például lehetővé teszi az Azure-erőforrások költségeinek megtekintését. Ha az erőforrás típusa alapján (a Microsoft. ininsights/Components for Application Insights) szűrőt ad hozzá, akkor a költségek nyomon követését is lehetővé teszi.

A használatról további ismereteket kaphat, ha [letölti a használatot az Azure Portalról](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal). A letöltött számolótáblában naponta megtekintheti az Azure-erőforrások használatának számát. Ebben az Excel-táblázatban a Application Insights-erőforrásokkal való használat első szűréssel megtalálhatók a "Application Insights" és a "Log Analytics" megjelenítéséhez, majd hozzá kell adni egy szűrőt a "példány azonosítója" oszlophoz, amely "tartalmaz Microsoft. bepillantások/összetevők ".  A legtöbb Application Insights-használat a Log Analytics mérőszám-kategóriával rendelkező fogyasztásmérőn szerepel, mivel az összes Azure Monitor összetevőhöz egyetlen naplós háttér tartozik.  A rendszer csak a régi árképzési szinteken és a többlépéses webes tesztek Application Insights erőforrásait jeleníti meg Application Insights fogyasztásmérő-kategóriája alapján.  A használat megjelenik a "felhasznált mennyiség" oszlopban, és az egyes bejegyzések egysége a "mértékegység" oszlopban látható.  További részleteket a [Microsoft Azure számla megismeréséhez](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)talál. 

## <a name="managing-your-data-volume"></a>Adatmennyiség kezelése 

Az alkalmazás által küldött adatok mennyiségének megismeréséhez a következőket teheti:

* A napi adatmennyiség diagram megjelenítéséhez nyissa meg a **használati és becsült költségek** ablaktáblát. 
* A Metrikaböngészőban adjon hozzá egy új diagramot. A diagram metrikájának kiválasztásához válassza az **adatpont kötete**elemet. Kapcsolja be a **csoportosítást**, majd az **adattípus**szerint csoportosítsa az értéket.
* Használja a `systemEvents` adattípust. Például az elmúlt nap során betöltött adatmennyiség megtekintéséhez a lekérdezés a következő lesz:

```kusto
systemEvents 
| where timestamp >= ago(1d)
| where type == "Billing" 
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes)
```

Ez a lekérdezés egy [Azure log-riasztásban](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log) használható az adatkötetek riasztásának beállításához. 

A küldött adatmennyiség háromféle módon kezelhető:

* **Mintavételezés**: a mintavétel segítségével csökkentheti a kiszolgálóról és az ügyfélalkalmazások által eljuttatott telemetria mennyiségét, a metrikák minimális torzításával. A mintavétel az elsődleges eszköz, amellyel beállíthatja az elküldött adatmennyiséget. További információ a [mintavételezési funkciókról](../../azure-monitor/app/sampling.md).
 
* **Napi korlát**: Ha Application Insights erőforrást hoz létre a Azure Portal, a napi korlát 100 GB/nap lesz. Ha Application Insights-erőforrást hoz létre a Visual Studióban, az alapértelmezett érték kicsi (csak 32,3 MB/nap). A napi korlát alapértelmezett értéke a tesztelés megkönnyítésére szolgál. A rendszer azt a célt szolgálja, hogy a felhasználó a napi korlátot az alkalmazás éles környezetben való üzembe helyezése előtt emelje fel. 

    A maximális korlát 1 000 GB/nap, ha nagy forgalmú alkalmazás esetén magasabb maximális értéket kér. 
    
    A napi korláttal kapcsolatos figyelmeztetési e-maileket a rendszer a következő szerepkörökhöz tartozó fiókra küldi el a Application Insights erőforráshoz: "ServiceAdmin", "AccountAdmin", "társtulajdonosa", "Owner".

    A napi korlát beállításakor legyen körültekintő. Az Ön szándéka, hogy *Soha ne nyomja meg a napi korlátot*. Ha eléri a napi korlátot, a nap hátralevő részében elveszíti az adatait, és nem tudja figyelni az alkalmazást. A napi korlát módosításához használja a **napi mennyiségi korlátot** . Ezt a beállítást a **használati és becsült költségek** ablaktáblán érheti el (a cikk későbbi részében részletesebben ismertetjük).
    
    Eltávolítjuk a korlátozást olyan előfizetési típusoknál, amelyek olyan Kredittel rendelkeznek, amely nem használható Application Insightshoz. Korábban, ha az előfizetés költségkerettel rendelkezik, a napi korlátot tartalmazó párbeszédablak útmutatást tartalmaz a költségkeret eltávolításához, és lehetővé teszi, hogy a napi korlát 32,3 MB/nap-nál nagyobb legyen.
    
* **Szabályozás**: a szabályozás korlátozza az adatátviteli sebességet másodpercenként 32 000 eseményre, átlagosan 1 percnél nagyobb kialakítási kulcsra. Az alkalmazás által küldött adatmennyiség percenként kerül értékelésre. Ha az érték meghaladja a percben mért másodpercenkénti arányt, a kiszolgáló elutasítja a kérelmeket. Az SDK pufferbe írja az adathalmazt, majd megpróbálja újból elküldeni. Több percet is igénybe vehet. Ha az alkalmazás a sávszélesség-szabályozásnál nagyobb mértékben küld adatokat, a rendszer bizonyos adatokat eldob. (A ASP.NET, a Java és a JavaScript SDK-k megpróbálnak ily módon újraküldeni az adatküldést. más SDK-k egyszerűen elhúzhatja a szabályozott adatátvitelt.) Ha a szabályozás bekövetkezik, a rendszer figyelmeztetést küld arról, hogy ez bekövetkezett.

## <a name="reduce-your-data-volume"></a>Csökkentse adatmennyiségét

Az alábbi műveleteket végezheti el az adatmennyiség csökkentése érdekében:

* [Mintavétel](../../azure-monitor/app/sampling.md)használata. Ez a technológia csökkenti az adatforgalom mértékét a metrikák eldöntése nélkül. Nem veszíti el a keresést a kapcsolódó elemek között. A kiszolgálói alkalmazásokban a mintavételezés automatikusan működik.
* [Korlátozza az Ajax-hívások számát, amely](../../azure-monitor/app/javascript.md#configuration) minden egyes oldalon megjeleníthető, vagy kikapcsolhatja az Ajax-jelentéskészítést.
* [Szerkessze a ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) fájlt a nem szükséges gyűjteményi modulok kikapcsolásához. Dönthet például úgy, hogy a teljesítményszámlálók vagy a függőségi adat nem elengedhetetlen.
* Ossza szét a telemetria a különböző rendszerállapot-kulcsok között. 
* Előre összevont mérőszámok. Ha a TrackMetric-hívásokat az alkalmazásban helyezi üzembe, csökkentheti a forgalmat a mérések egy kötegének átlagát és szórását is elfogadó túlterhelés használatával. Vagy használhatja az [Összesítés előtti csomagot](https://www.myget.org/gallery/applicationinsights-sdk-labs)is.

## <a name="manage-your-maximum-daily-data-volume"></a>A maximális napi adatmennyiség kezelése

A napi mennyiségi korlátot használhatja a gyűjtött adatok korlátozására. Ha azonban a korlát teljesül, az alkalmazásból a nap hátralevő részében elérkező összes telemetria elvesztése történik. *Nem tanácsos* az alkalmazásnak a napi korlátot megtalálnia. Az alkalmazás állapota és teljesítménye nem követhető nyomon, miután elérte a napi korlátot.

A napi mennyiségi korlát használata helyett [mintavételezéssel](../../azure-monitor/app/sampling.md) hangolja be az adatmennyiséget a kívánt szintre. Ezt követően a napi korlátot csak akkor használja, ha az alkalmazás váratlanul megkezdi a sokkal nagyobb mennyiségű telemetria küldését.

### <a name="identify-what-daily-data-limit-to-define"></a>A definiálni kívánt napi adatkorlát meghatározása

Tekintse át Application Insights használati és becsült költségét, és Ismerje meg az adatfeldolgozási trendet, valamint azt, hogy mi a napi mennyiségi korlát. Körültekintően kell fontolóra venni, mert a korlát elérésekor nem fogja tudni figyelni az erőforrásokat. 

### <a name="set-the-daily-cap"></a>A napi korlát beállítása

A napi korlát módosításához a Application Insights erőforrás **Konfigurálás** szakaszában a **használat és a becsült költségek** lapon válassza a **napi korlát**lehetőséget.

![A napi telemetria mennyiségi korlátjának módosítása](./media/pricing/pricing-003.png)

A [napi korlát Azure Resource Manageron keresztüli módosításához](../../azure-monitor/app/powershell.md)a módosítandó tulajdonság a `dailyQuota`.  Azure Resource Manager a `dailyQuotaResetTime` és a napi korlát `warningThreshold` értéket is beállíthatja. 

## <a name="sampling"></a>Mintavétel
A [mintavétel](../../azure-monitor/app/sampling.md) olyan módszer, amely csökkenti a telemetria az alkalmazásba való küldésének mértékét, miközben megőrzi a kapcsolódó események keresésének lehetőségét a diagnosztikai keresések során. Megőrzi a helyes események számát is.

A mintavétel hatékony módszert jelent a költségek csökkentéséhez és a havi kvótán belüli tartózkodáshoz. A mintavételi algoritmus megőrzi a kapcsolódó telemetria, így például a keresés használatakor megkeresheti az adott kivételhez kapcsolódó kérelmet. Az algoritmus emellett megőrzi a helyes számadatokat, így a megfelelő értékeket látja a mérőszám-kezelőben a kérelmek díjszabása, a kivételek és az egyéb darabszámok tekintetében.

A mintavételnek többféle formája van.

* Az [adaptív mintavételezés](../../azure-monitor/app/sampling.md) a ASP.net SDK alapértelmezett értéke. Az adaptív mintavételezés automatikusan igazodik az alkalmazás által küldött telemetria. Automatikusan működik az SDK-ban a webalkalmazásban, így csökken a telemetria-forgalom a hálózaton. 
* A betöltési *mintavételezés* olyan alternatíva, amely arra a pontra működik, ahol az alkalmazás telemetria belép a Application Insights szolgáltatásba. A betöltési mintavételezés nem befolyásolja az alkalmazásból eljuttatott telemetria mennyiségét, de csökkenti a szolgáltatás által megőrzött kötetet. A betöltési mintavételezés használatával csökkentheti a telemetria által a böngészőkből és más SDK-k által használt kvótát.

A betöltési mintavételezés beállításához lépjen a **díjszabás** panelre:

![A kvóta és díjszabás ablaktáblán válassza ki a minták csempét, majd válasszon ki egy mintavételi frakciót](./media/pricing/pricing-004.png)

> [!WARNING]
> Az **adatmintavételezési** panel csak a betöltési mintavételezés értékét vezérli. Nem tükrözi az alkalmazásban az Application Insights SDK által alkalmazott mintavételi sebességet. Ha a bejövő telemetria már meg van határozva az SDK-ban, a rendszer nem alkalmazza a betöltési mintavételezést.
>

Ha a tényleges mintavételezési sebességet szeretné felderíteni, függetlenül attól, hogy hol lett alkalmazva, használjon egy [elemzési lekérdezést](analytics.md). A lekérdezés így néz ki:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

Az egyes megőrzött rekordokban a `itemCount` érték azt jelzi, hogy hány eredeti rekordot képvisel. A korábbi elvetett rekordok száma 1. 

## <a name="change-the-data-retention-period"></a>Az adatmegőrzési időszak módosítása

Application Insights erőforrások alapértelmezett megőrzése 90 nap. Minden Application Insights erőforráshoz különböző megőrzési időszakok választhatók ki. A rendelkezésre álló adatmegőrzési időszakok teljes készlete 30, 60, 90, 120, 180, 270, 365, 550 vagy 730 nap. 

Az adatmegőrzés módosításához a Application Insights erőforrásból lépjen a **használati és becsült költségek** lapra, és válassza ki az **adatmegőrzési** beállítást:

![A napi telemetria mennyiségi korlátjának módosítása](./media/pricing/pricing-005.png)

A megőrzés a [PowerShell használatával is beállítható](https://docs.microsoft.com/azure/azure-monitor/app/powershell#set-the-data-retention) a `retentionInDays` paraméterrel programozott módon. Emellett, ha az adatmegőrzést 30 napra állítja be, a `immediatePurgeDataOn30Days` paraméterrel azonnal törölheti a régebbi adatok törlését, ami a megfelelőséggel kapcsolatos forgatókönyvek esetében hasznos lehet. Ez a kiürítési funkció csak Azure Resource Manageron keresztül érhető el, és rendkívül körültekintően használható. 

Ha a számlázás a 2019. december elején megtartja a hosszabb adatmegőrzést, a 90 napnál hosszabb ideig tartott adatok számlázása ugyanaz, mint a jelenleg az Azure Log Analytics adatmegőrzési szolgáltatás díja. További információt a [Azure monitor díjszabását ismertető oldalon](https://azure.microsoft.com/pricing/details/monitor/)olvashat. A [javaslathoz való szavazással](https://feedback.azure.com/forums/357324-azure-monitor-application-insights/suggestions/17454031)naprakész maradhat a változó adatmegőrzési folyamaton. 

## <a name="data-transfer-charges-using-application-insights"></a>Adatátviteli díjak az Application Insights használatával

Az adatok Application Insights való küldése adatsávszélességi díjat eredményezhet. Az [Azure sávszélesség-díjszabási oldalán](https://azure.microsoft.com/pricing/details/bandwidth/)leírtak szerint a két régióban található Azure-szolgáltatások közötti adatforgalom a normál díjszabás szerint kifelé irányuló kimenő adatforgalom. A bejövő adatforgalom ingyenes. Ez a díj azonban nagyon kicsi (néhány%) a Application Insights naplózási adatfeldolgozás költségeihez képest. Ennek következtében a Log Analytics költségeinek szabályozása a betöltött adatmennyiségre összpontosíthat, és útmutatást nyújtunk ennek [megértéséhez.](https://docs.microsoft.com/azure/azure-monitor/app/pricing#managing-your-data-volume)   

## <a name="limits-summary"></a>Korlátok összegzése

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>Napi Cap-beli e-mailek letiltása

A napi mennyiségi korláttal rendelkező e-mailek letiltásához a Application Insights erőforrásának **Konfigurálás** szakaszában, a **használat és a becsült költségek** panelen válassza a **napi korlát**lehetőséget. A rendszer elküldi az e-mailek küldését, amikor eléri a korlátot, valamint ha elérte az állítható figyelmeztetési szintet. Ha le szeretné tiltani az összes napi korlátot a kötethez kapcsolódó e-maileket, törölje mindkét négyzet jelölését.

## <a name="legacy-enterprise-per-node-pricing-tier"></a>Örökölt nagyvállalati (node) árképzési csomag

Az Azure Application Insights korai alkalmazói számára még két lehetséges árképzési szint létezik: alapszintű és vállalati. Az alapszintű díjszabás megegyezik a fentiekben leírtak szerint, és az alapértelmezett szint. Minden nagyvállalati szintű szolgáltatást magában foglal, díjmentesen. Az alapszintű csomag elsősorban a betöltött adatok mennyiségét számlázza. 

> [!NOTE]
> Ezek az örökölt árképzési szintek át lettek nevezve. A nagyvállalati díjszabási szint mostantól **csomópontként** van meghívva, és az alapszintű díjszabás mostantól **GB-ra**van meghívva. A rendszer ezeket az új neveket használja a Azure Portal.  

A per node (korábban nagyvállalati) szinten egy csomópontos díjat számítunk fel, és minden egyes csomópont napi adatmennyiséget kap. A per Node díjszabási szinten a befoglalt mennyiség fölött betöltött adatért kell fizetnie. Ha az Operations Management Suite-t használja, válassza ki a csomópontok közötti szintet. 

A pénznem és a régió aktuális áraiért lásd: [Application Insights díjszabása](https://azure.microsoft.com/pricing/details/application-insights/).

> [!NOTE]
> A 2018-es verzióban egy új díjszabási modellt [vezettünk be](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) az Azure monitoring szolgáltatáshoz. Ez a modell egy egyszerű "utólagos elszámolású" modellt alkalmaz a figyelési szolgáltatások teljes portfóliójában. További információ az [új díjszabási modellről](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), a [modellre való áttérés következményeinek felmérése](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model) a használati minták alapján, valamint [az új modell használatának módja](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model)

### <a name="per-node-tier-and-operations-management-suite-subscription-entitlements"></a>/Csomópontok és az Operations Management Suite előfizetési jogosultságai

Azok az ügyfelek, akik az Operations Management Suite E1-es és E2-es vásárlási csomaggal rendelkeznek, a [korábban bejelentettnél](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/)további díj nélkül kaphatnak Application Insightst további szolgáltatásként. Pontosabban, az Operations Management Suite E1 és E2 minden egysége jogosultságot tartalmaz a Application Insights egy csomópontjára. Az egyes Application Insights csomópontok naponta akár 200 MB-nyi adatot foglalnak magukban (a Log Analytics adatfeldolgozástól elkülönítve), a 90 napos adatmegőrzéssel együtt, többletköltség nélkül. A szintet a cikk későbbi részében részletesebben ismertetjük. 

Mivel ez a csomag csak az Operations Management Suite-előfizetéssel rendelkező ügyfelekre alkalmazható, az Operations Management Suite-előfizetéssel nem rendelkező ügyfeleknek nem jelennek meg a csomag kiválasztására szolgáló lehetőség.

> [!NOTE]
> A jogosultság beszerzése érdekében a Application Insights-erőforrásoknak a Node díjszabási szinten kell lenniük. Ez a jogosultság csak csomópontok esetén érvényes. A GB-onként Application Insights erőforrások nem vesznek igénybe semmilyen előnyt. Ez a jogosultság nem látható a **használati és becsült költség** ablaktáblán látható becsült költségek között. Emellett, ha az előfizetést az új Azure monitoring díjszabási modellre helyezi át a 2018 áprilisában, a GB-os szintet az egyetlen rendelkezésre álló csomag. Az előfizetés az új Azure monitoring díjszabási modellre való áthelyezése nem ajánlott, ha Operations Management Suite-előfizetéssel rendelkezik.

### <a name="how-the-per-node-tier-works"></a>A/csomópontok szintjeinek működése

* Minden olyan csomópontért fizetnie kell, amely telemetria küld a Node szinten lévő összes alkalmazás számára.
  * A *csomópont* egy fizikai vagy virtuális kiszolgáló, illetve az alkalmazást üzemeltető platform-szolgáltatás szerepkör-példány.
  * A fejlesztői gépek, az ügyféloldali böngészők és a mobileszközök nem számítanak csomópontnak.
  * Ha az alkalmazás több olyan összetevővel is rendelkezik, amelyek telemetria küldenek, például egy webszolgáltatást és egy háttérbeli munkavégzőt, akkor az összetevőket külön kell megszámolni.
  * A [élő metrikastream](../../azure-monitor/app/live-stream.md) -adatértékek nem számítanak fel díjszabási célokra. Egy előfizetésben a díjak felhasználónként, nem pedig alkalmazásként jelennek meg. Ha öt olyan csomópontja van, amely 12 alkalmazás telemetria küld, a díj öt csomópontra van felszámítva.
* Bár a díjak havonta vannak feltüntetve, csak olyan órára kell fizetnie, amikor egy csomópont telemetria küld egy alkalmazásból. Az óradíj a 744-as számú (az órák száma 31 napos hónapban).
* A rendszer naponta 200 MB adatmennyiség-lefoglalást kap minden észlelt csomóponthoz (óránkénti részletességgel). A fel nem használt adatfoglalás nem egy napról a másikra történik.
  * Ha a felhasználónkénti díjszabási szintet választja, az egyes előfizetések napi adatmennyiséget szereznek az adott előfizetés Application Insights erőforrásaira telemetria küldő csomópontok száma alapján. Tehát ha öt olyan csomópontja van, amely egész nap küldi az adatküldést, akkor az adott előfizetéshez tartozó összes Application Insights esetében 1 GB-os készletezett támogatással fog rendelkezni. Nem számít, hogy egyes csomópontok több, mint más csomópontot küldenek, mert az összes csomóponton meg van osztva a tartalmazott információ. Ha egy adott napon a Application Insights-erőforrások több, mint az előfizetéshez tartozó napi adatfoglalásban foglalt adatmennyiséget kapnak, akkor a GB-nál nagyobb adatforgalmi díjak érvényesek. 
  * A napi adatmennyiség kiszámítása a nap folyamán (UTC szerint), az egyes csomópontok által küldött telemetria pedig 24 – 200 MB-kal elosztva történik. Tehát ha négy olyan csomópontja van, amely telemetria küld a nap 24 órájában, az adott napra vonatkozó adatmennyiség ((4 &#215; 15)/24) &#215; 200 MB = 500 MB. A (z) 2,30 USD/GB értékű adatmennyiség esetén a díj 1,15 USD, ha a csomópontok naponta 1 GB adat küldését küldik.
  * A csomópontok közötti napi támogatás nincs megosztva olyan alkalmazásokkal, amelyekhez GB-nyi szintet választott. A fel nem használt pótlékot nem a napi naptól számítva. 

### <a name="examples-of-how-to-determine-distinct-node-count"></a>Példák a különböző csomópontok számának meghatározására

| Alkalmazási helyzet                               | Csomópontok napi száma összesen |
|:---------------------------------------|:----------------:|
| 1 alkalmazás 3 Azure App Service példány és 1 virtuális kiszolgáló használatával | 4 |
| 3 alkalmazás 2 virtuális gépen; az alkalmazások Application Insights erőforrásai ugyanahhoz az előfizetéshez tartoznak, és a csomóponti szinten | 2 | 
| 4 olyan alkalmazás, amelynek alkalmazásaiban az alkalmazások erőforrásai ugyanabban az előfizetésben találhatók; minden, 2 példányt futtató alkalmazás 16 óra alatt, illetve 4 példányban 8 csúcsidőben | 13,33 | 
| A Cloud Services 1 feldolgozói szerepkörrel és 1 webes szerepkörrel rendelkezik, amelyek mindegyike 2 példányt futtat | 4 | 
| Egy 5 csomópontos Azure Service Fabric-fürt, amely 50-es szolgáltatást futtat; minden 3 példányt futtató szolgáltatás | 5|

* A csomópontok pontos számlálása attól függ, hogy az alkalmazás melyik Application Insights SDK-t használja. 
  * Az SDK 2,2-es és újabb verzióiban mind a Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) -t, mind a [web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) -t minden egyes alkalmazás-gazdagép csomópontként jelenti. Ilyenek például a fizikai kiszolgáló és a virtuálisgép-gazdagépek számítógépneve, vagy a Cloud Services-példány neve.  Az egyetlen kivétel egy olyan alkalmazás, amely csak a [.net Core](https://dotnet.github.io/) -t és a Application INSIGHTS Core SDK-t használja. Ebben az esetben csak egy csomópontot kell jelenteni az összes gazdagépről, mert az állomásnév nem érhető el. 
  * Az SDK korábbi verzióihoz a [web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) az újabb SDK-verzióhoz hasonlóan viselkedik, de az [alapszintű SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) csak egy csomópontot jelent, az alkalmazás gazdagépek számától függetlenül. 
  * Ha az alkalmazás az SDK-val állítja be a **roleInstance** egyéni értékre, alapértelmezés szerint ugyanazt az értéket használja a csomópontok számának meghatározásához. 
  * Ha egy új SDK-verziót használ az ügyfélgépekről vagy mobileszközökön futtatott alkalmazásokkal, a csomópontok száma nagyon nagy mennyiségű (a nagyszámú ügyfélszámítógép vagy a mobileszközök miatt) lehet. 

## <a name="automation"></a>Automation

Írhat egy parancsfájlt az árképzési csomag beállításához az Azure Erőforrás-kezelés használatával. [További tudnivalókat itt talál](powershell.md#price).


## <a name="next-steps"></a>Következő lépések

* [Mintavételezés](../../azure-monitor/app/sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ../../azure-monitor/app/app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/