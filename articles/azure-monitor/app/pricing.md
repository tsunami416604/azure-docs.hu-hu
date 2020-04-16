---
title: Az Azure Application Insights használati és költségeinek kezelése | Microsoft dokumentumok
description: Telemetriai kötetek kezelése és a költségek figyelése az Application Insightsban.
ms.topic: conceptual
author: DaleKoetke
ms.author: dalek
ms.date: 11/27/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 0225484de06ae4e595f1dcbcdd520f4e0e4d53f5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405389"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>Használat és költségek kezelése az Application Insights szolgáltatásban

> [!NOTE]
> Ez a cikk ismerteti, hogyan értheti és szabályozhatja az Application Insights költségeit.  Egy kapcsolódó cikk, [a használat figyelése és a becsült költségek](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) ismerteti, hogyan tekintheti meg a használat és a becsült költségek több Azure figyelési funkciók különböző díjszabási modellek.

Az Application Insights mindent megkap, amire szüksége lehet a webes alkalmazások rendelkezésre állásának, teljesítményének és használatának figyeléséhez, függetlenül attól, hogy azok az Azure-ban vagy a helyszíni üzemeltetésben vannak-e. Az Application Insights támogatja a népszerű nyelveket és keretrendszereket, például a .NET, a Java és a Node.js, és integrálja a DevOps-folyamatokkal és -eszközökkel, például az Azure DevOps, a Jira és a PagerDuty. Fontos megérteni, hogy mi határozza meg az alkalmazások figyelésének költségeit. Ebben a cikkben áttekintjük, hogy mi hajtja az alkalmazásfigyelési költségeket, és hogyan figyelheti és szabályozhatja proaktív módon azokat.

Ha kérdése van azzal kapcsolatban, hogyan működik az árak [forum](https://social.msdn.microsoft.com/Forums/home?forum=ApplicationInsights&filter=alltypes&sort=lastpostdesc)az Application Insights esetében, akkor feltehet egy kérdést a fórumunkban.

## <a name="pricing-model"></a>Díjszabási modell

Az Azure [Application Insights][start] díjszabása egy használatra szánt **fizetési modell,** amely a bevitt adatmennyiségen alapul, és opcionálisan hosszabb adatmegőrzési. Minden Egyes Application Insights-erőforrás külön szolgáltatásként kerül felszámolásra, és hozzájárul az Azure-előfizetés számlájához. Az adatmennyiséget az Application Insights által az alkalmazásból kapott tömörítetlen JSON-adatcsomag mérete alapján méri a rendszer. Az [élő metrikák streamjének](../../azure-monitor/app/live-stream.md)használatáért nem számítunk fel adatmennyiséget.

[A többlépéses webes tesztek](../../azure-monitor/app/availability-multistep.md) további díjat vonnak maga után. A többlépéses webes tesztek olyan webes tesztek, amelyek műveletek sorozatát hajtják végre. Egyetlen oldal *pingtesztjeiért* nem számítunk fel külön díjat. A pingtesztekből és a többlépéses tesztekből származó telemetriai adatok at az alkalmazás más telemetriai adataival megegyezik.

Az Application Insights beállítás [egyéni metrikadimenziók riasztási engedélyezése](https://docs.microsoft.com/azure/azure-monitor/app/pre-aggregated-metrics-log-metrics#custom-metrics-dimensions-and-pre-aggregation) is okozhat további költségeket, mert ez további előzetes összesítési metrikák létrehozását eredményezheti. [További információ](https://docs.microsoft.com/azure/azure-monitor/app/pre-aggregated-metrics-log-metrics) a naplóalapú és az előre összesített metrikákról az Application Insightsban, valamint az Azure Monitor egyéni metrikák [díjszabásáról.](https://azure.microsoft.com/pricing/details/monitor/)

## <a name="estimating-the-costs-to-manage-your-application"></a>Az alkalmazás kezelésével járandó költségek becslése

Ha még nem használja az Application Insights, használhatja az [Azure Monitor díjszabási kalkulátor](https://azure.microsoft.com/pricing/calculator/?service=monitor) az Application Insights használatának költségének becsléséhez. Kezdje azzal, hogy beírja az "Azure Monitor" kifejezést a keresőmezőbe, és az eredményül kapott Azure Monitor csempére kattint. Görgessen le a lapon az Azure Monitor, és válassza az Application Insights a típus legördülő menüben.  Itt adhatja meg, hogy várhatóan hány GB-nyi adatot gyűjt hetente, így a kérdés az, hogy az Application Insights mennyi adatot gyűjt az alkalmazás figyelése során.

Ennek kezelésére két módszer létezik: az alapértelmezett figyelés és az adaptív mintavételezés használata, amely az SDK ASP.NET érhető el, vagy a valószínű adatbetöltés becslése a többi hasonló ügyfél által látott alapján.

### <a name="data-collection-when-using-sampling"></a>Adatgyűjtés mintavétel használata esetén

Az SDK ASP.NET [adaptív mintavételezésével](sampling.md#adaptive-sampling)az adatmennyiség automatikusan módosul, hogy az alapértelmezett Application Insights-figyelési forgalom egy megadott maximális forgalmi sebességen belül maradjon. Ha az alkalmazás kis mennyiségű telemetriai adatokat hoz létre, például hibakereséskor vagy alacsony használat miatt, a mintavételi processzor nem fogja eldobni az elemeket, amíg a kötet a másodpercenkénti szint alatt van. Egy nagy mennyiségű alkalmazás esetén az alapértelmezett küszöbérték öt esemény másodpercenként, adaptív mintavételi korlátozza a napi események száma 432 000. Egy tipikus átlagos eseménymérete 1 KB, ez megfelel a 13,4 GB telemetriai adatok 31 napos hónaponként csomópontonként az alkalmazás (mivel a mintavételi történik helyi minden csomópont.) 

Az adaptív mintavételezést nem támogató SDK-k esetében használhat [betöltési mintavételezést,](https://docs.microsoft.com/azure/azure-monitor/app/sampling#ingestion-sampling)amely mintákat alkalmazhat, amikor az Application Insights az adatok megőrzésre kerülésének százalékos aránya alapján érkezik, vagy [rögzített sebességű mintavételezést ASP.NET, ASP.NET Core és Java webhelyekhez](sampling.md#fixed-rate-sampling) a webkiszolgálóról és a webböngészőkből küldött forgalom csökkentése érdekében.

### <a name="learn-from-what-similar-customers-collect"></a>Tanuljon ahasonló ügyfelek által gyűjtött adatokból

Az Azure Monitoring díjszabásk kalkulátor az Application Insights, ha engedélyezi a "Becsült adatmennyiség az alkalmazás tevékenysége alapján" funkciót, az alkalmazással kapcsolatos bemeneteket (havonta és oldalmegtekintések havonta, abban az esetben, ha ügyféloldali telemetriai adatokat gyűjt), majd a számológép megmondja, hogy a hasonló alkalmazások által gyűjtött adatok medián és 90. percentilis mennyisége. Ezek az alkalmazások az Application Insights-konfiguráció tartományát ölelik fel (például néhány alapértelmezett [mintavételezéssel](../../azure-monitor/app/sampling.md)rendelkezik, néhánynak nincs mintavételezése stb.), így továbbra is rendelkezik a vezérlővel, hogy csökkentse a betöltési adatok mennyiségét messze a medián szint alatt mintavételezéssel. De ez egy kiindulási pont, hogy megértsük, milyen más, hasonló ügyfelek látnak.

## <a name="understand-your-usage-and-estimate-costs"></a>A használati és becslési költségek megismerése

Az Application Insights segítségével könnyen érthető, hogy a költségek valószínűleg a legutóbbi használati minták alapján. Első lépésekhez az Azure Portalon az Application Insights-erőforráshoz nyissa meg a **Használati és becsült költségek** lapot:

![Díjszabás kiválasztása](./media/pricing/pricing-001.png)

A. Tekintse át az adott hónap adatkötetét. Ez magában foglalja a kiszolgálóról és az ügyfélalkalmazásokból, valamint a rendelkezésre állási tesztekből kapott és megőrzött összes adatot (mintavétel [után).](../../azure-monitor/app/sampling.md)  
B. Külön díjat számítanak fel a [többlépéses webes tesztekhez.](../../azure-monitor/app/availability-multistep.md) (Ez nem tartalmazza az egyszerű rendelkezésre állási teszteket, amelyek szerepelnek az adatmennyiség díjában.)  
C. Az elmúlt hónap adatmennyiség-tendenciáinak megtekintése.  
D. Engedélyezze az adatok betöltési [mintavételezését.](../../azure-monitor/app/sampling.md)
E. Állítsa be a napi adatmennyiség-plafont.  

(Ne feledje, hogy az ebben a cikkben szereplő képernyőképeken megjelenő árak csak például célokat szolgálnak. A pénznemben és a régióban lévő aktuális árakról az [Application Insights díjszabása][pricing].)

Az Application Insights-használat mélyebb vizsgálatához nyissa meg a **Metrikák** lapot, adja hozzá az "Adatpont-kötet" nevű metrikát, majd válassza a *Felosztás alkalmazása* lehetőséget az adatok "Telemetriai elemtípus" szerint történő felosztásához.

Az Application Insights-díjak hozzáadódnak az Azure-számlához. Az Azure-számla részleteit az Azure Portal **Számlázás** szakaszában vagy az [Azure számlázási portálján](https://account.windowsazure.com/Subscriptions)láthatja.

![A bal oldali menüben válassza a Számlázás lehetőséget](./media/pricing/02-billing.png)

### <a name="using-data-volume-metrics"></a>Adatmennyiség-mérőszámok használata
<a id="understanding-ingested-data-volume"></a>

Ha többet szeretne megtudni az adatkötetekről, válassza ki az Application Insights-erőforrás **metrikák beállítását,** vegyen fel egy új diagramot. A diagrammérő mutató **naplóalapú metrikái csoportban**válassza az **Adatpont-kötet lehetőséget.** Kattintson **a Felosztás alkalmazása gombra,** és ** `Telemetryitem` típus**szerint válassza a csoportot.

![Az adatmennyiség vizsgálata metrikák használatával](./media/pricing/10-billing.png)

### <a name="queries-to-understand-data-volume-details"></a>Lekérdezések az adatkötet részleteinek megértéséhez

Az Application Insights adatköteteinek vizsgálata két módszer. Az első a `systemEvents` táblázatban szereplő összesített adatokat, a második pedig a `_BilledSize` tulajdonságot használja, amely minden egyes bevitt eseményen elérhető.

#### <a name="using-aggregated-data-volume-information"></a>Összesített adatmennyiség-információk használata

A `systemEvents` táblával például megtekintheti a lekérdezéssel az elmúlt 24 órában betöltött adatkötetet:

```kusto
systemEvents
| where timestamp >= ago(24h)
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes)
```

Vagy az adatmennyiség (bájtban) adattípus on-át kénti diagramjának megtekintéséhez használhatja a következőket:

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes) by BillingTelemetryType, bin(timestamp, 1d) | render barchart  
```

Vegye figyelembe, hogy ez a lekérdezés használható egy [Azure Log Alert](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log) adatkötetek riasztási beállítása.  

Ha többet szeretne megtudni a telemetriai adatok változásairól, a lekérdezés használatával típusonként lekérdezhetjük az események számát:

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| summarize count() by BillingTelemetryType, bin(timestamp, 1d)
| render barchart  
```

#### <a name="using-data-size-per-event-information"></a>Adatméret használata eseményadatokonként

Ha további részleteket szeretne megtudni az adatkötetek `_BilledSize` forrásáról, használhatja az egyes bevitt eseményeken található tulajdonságot.

Ha például azt szeretnénk, hogy mely műveletek generálják a legtöbb `_BilledSize` adatkötetet az elmúlt 30 napban, összegezhetjük az összes függőségi eseményt:

```kusto
dependencies
| where timestamp >= startofday(ago(30d))
| summarize sum(_BilledSize) by operation_Name
| render barchart  
```

## <a name="viewing-application-insights-usage-on-your-azure-bill"></a>Az Application Insights-használat megtekintése az Azure-számlán

Az Azure számos hasznos funkciót biztosít az [Azure Cost Management + Billing](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) hubon. Például a "Költségelemzés" funkció lehetővé teszi, hogy az Azure-erőforrásokra fordított kiadások megtekintéséhez. Ha erőforrástípus szerint szűrőt ad hozzá (a microsoft.insights/components for Application Insights) segítségével nyomon követheti a kiadásokat.

A használat jobb megértése az [Azure Portalról való letöltéssel](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal)érhető el.
A letöltött számolótáblában láthatja az Azure-erőforrásonkénti napi használatot. Ebben az Excel-számolótáblában az Application Insights-erőforrások használatának megtalálható a "Mérőkategória" oszlop "Application Insights" és a "Log Analytics" megjelenítéséhez történő első szűréssel, majd egy szűrő hozzáadásával a "Példányazonosító" oszlopban, amely "tartalmazza a microsoft.insights/components" oszlopot.  A legtöbb Application Insights-használat a Log Analytics mérőműszer-kategóriájával történik, mivel az Azure Monitor összes összetevőjéhez egyetlen napló-háttérrendszer tartozik.  Csak az Application Insights-erőforrások örökölt tarifacsomagok és többlépéses webes tesztek jelentik a meter kategóriája Application Insights.  A felhasználás a "Felhasznált mennyiség" oszlopban, az egyes bejegyzések egysége pedig a "Mértékegység" oszlopban látható.  További részletek a [Microsoft Azure-számlának megértéséhez](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)érhetők el.

## <a name="managing-your-data-volume"></a>Az adatmennyiség kezelése

A küldött adatok mennyisége a következő módszerekkel kezelhető:

* **Mintavételezés:** Mintavételsegítségével csökkentheti a kiszolgálóról és az ügyfélalkalmazásokból küldött telemetriai adatok, minimális metrikák torzítása. A mintavételezés az elsődleges eszköz, amellyel beállíthatja az elküldött adatok mennyiségét. További információ a [mintavételi funkciókról.](../../azure-monitor/app/sampling.md)

* **Limit Ajax hívások**: [Korlátozhatja a számát Ajax hívások at lehet jelenteni](../../azure-monitor/app/javascript.md#configuration) minden oldalnézetben, vagy kapcsolja ki Ajax jelentés.

* **Tiltsa le a szükségtelen modulokat:** [Az ApplicationInsights.config fájl szerkesztésével](../../azure-monitor/app/configuration-with-applicationinsights-config.md) kikapcsolhatja a szükségtelen üleceket, amelyekre nincs szüksége. Dönthet például úgy, hogy a teljesítményszámlálók vagy a függőségi adatok nem elengedhetetlenek.

* **Előre összesített metrikák:** Ha hívásokat tesz a TrackMetric alkalmazásba, csökkentheti a forgalmat a túlterhelés használatával, amely elfogadja a mérések egy kötegének átlagának és szórásának számítását. Vagy használhat [előösszesítési csomagot](https://www.myget.org/gallery/applicationinsights-sdk-labs)is.
 
* **Napi korlát:** Amikor létrehoz egy Application Insights-erőforrást az Azure Portalon, a napi korlát 100 GB/nap. Amikor létrehoz egy Application Insights-erőforrást a Visual Studióban, az alapértelmezett érték kicsi (csak 32,3 MB/nap). A napi felső korlát alapértelmezett beállítása a tesztelés megkönnyítése. Célja, hogy a felhasználó növeli a napi korlátot, mielőtt az alkalmazást éles környezetbe helyezne. 

    A maximális korlát 1000 GB/nap, kivéve, ha magasabb maximális értéket kér egy nagy forgalmú alkalmazáshoz.
    
    A napi korlátra vonatkozó figyelmeztető e-maileket a rendszer az Application Insights-erőforrás ezen szerepköreinek tagjainak küldi el: "ServiceAdmin", "AccountAdmin", "CoAdmin", "Owner".

    A napi kupak beállításakor óvatosan állítsa be a kupakot. A szándék az, hogy *soha nem nyomja meg a napi sapkát*. Ha eléri a napi korlátot, a nap hátralévő részében adatokat veszít, és nem tudja figyelni az alkalmazást. A napi korlát módosításához használja a **Napi hangerőkorlát** lehetőséget. Ezt a beállítást a **Használati és becsült költségek** ablaktáblában érheti el (ezt a cikk későbbi részében részletesebben ismertetjük).
    
    Eltávolítottuk a korlátozást egyes előfizetési típusok, amelyek hitel, amely nem használható az Application Insights. Korábban, ha az előfizetés rendelkezik egy költési korlátot, a napi korlát párbeszédablak utasításokat, hogy távolítsa el a költségkeretet, és lehetővé teszi a napi korlát ot kell emelni túl 32,3 MB /nap.
    
* **Szabályozás:** A szabályozás az adatátviteli sebességet másodpercenként 32 000 eseményre korlátozza, amely műszerezési kulcsonként átlagosan 1 perc felett van. Az alkalmazás által küldött adatok mennyiségét percenként értékeli a rendszer. Ha meghaladja a perc alatt átlagban lévő másodpercenkénti sebességet, a kiszolgáló visszautasít bizonyos kéréseket. Az SDK puffereli az adatokat, majd megpróbálja újraelküldeni. Több perc alatt túlfeszültség terjed ki. Ha az alkalmazás következetesen több, mint a sávszélesség-szabályozási sebesség, bizonyos adatok ellesznek dobva. (A ASP.NET, a Java és a JavaScript SDK-k így próbálják meg újraküldeni az adatokat; más SDK-k egyszerűen eldobhatják a szabályozott adatokat.) Ha szabályozás történik, egy értesítési figyelmeztetés figyelmezteti, hogy ez történt.

## <a name="manage-your-maximum-daily-data-volume"></a>A maximális napi adatmennyiség kezelése

A napi kötetkorlát segítségével korlátozhatja az összegyűjtött adatokat. Azonban ha a korlát teljesül, az alkalmazásból a nap hátralévő részében küldött összes telemetriai adat elvesztése következik be. Nem *tanácsos,* hogy az alkalmazás megüt a napi kupakot. Nem tudja nyomon követni az alkalmazás állapotát és teljesítményét, miután elérte a napi korlátot.

A napi kötetkorlát használata helyett [a mintavételezés](../../azure-monitor/app/sampling.md) segítségével állítsa be az adatmennyiséget a kívánt szintre. Ezután használja a napi korlátot csak "végső megoldásként" abban az esetben, ha az alkalmazás váratlanul sokkal nagyobb mennyiségű telemetriai adatokat kezd küldeni.

### <a name="identify-what-daily-data-limit-to-define"></a>Határozza meg, hogy milyen napi adatkorlátot kell meghatároznia

Tekintse át az Application Insights-használat ot és a becsült költségeket az adatbetöltési trend és a meghatározandó napi mennyiségi korlát megértéséhez. Óvatosan kell figyelembe venni, mivel a korlát elérése után nem tudja figyelni az erőforrásokat.

### <a name="set-the-daily-cap"></a>A napi korlát beállítása

A napi korlát módosításához az Application Insights-erőforrás **Konfigurálás** szakaszában a **Használati és becsült költségek** lapon válassza a Napi **korlát**lehetőséget.

![A napi telemetriai kötetkorlát beállítása](./media/pricing/pricing-003.png)

Ha módosítani szeretné [a napi korlátot az Azure Resource Manager en keresztül,](../../azure-monitor/app/powershell.md)a tulajdonságot módosítani kell a `dailyQuota`.  Az Azure Resource Manager segítségével `dailyQuotaResetTime` beállíthatja a `warningThreshold`és a napi korlátot is.

### <a name="create-alerts-for-the-daily-cap"></a>Riasztások létrehozása a napi korláthoz

Az Application Insights napi kupakja létrehoz egy eseményt az Azure-tevékenységnaplóban, amikor a bevitt adatkötetek elérik a figyelmeztetési szintet vagy a napi felső korlátot.  Ezek [a tevékenységnapló-események alapján riasztást](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log#create-with-the-azure-portal)hozhat létre. Ezeknek az eseményeknek a jelnevei a következők:

* Az Application Insights összetevő napi korlátfigyelmeztetési küszöbértéke elérte

* Az Application Insights összetevő napi felső határa elérte

## <a name="sampling"></a>Mintavételezés
[Mintavételezés](../../azure-monitor/app/sampling.md) egy olyan módszer, amely csökkenti a telemetriai adatok küldésének sebességét az alkalmazásba, miközben megőrzi a kapcsolódó események keresésének lehetőségét a diagnosztikai keresések során. A megfelelő eseményszámokat is megtartja.

A mintavétel hatékony módja a díjak csökkentésének és a havi kvótán belüli tartózkodásnak. A mintavételi algoritmus megtartja a kapcsolódó elemek telemetriai adatok, így például a Keresés használatakor, megkeresheti az adott kivételhez kapcsolódó kérelmet. Az algoritmus is megtartja a helyes számokat, így a metrikakezelőben a megfelelő értékeket látja a kérelemsebesség, a kivételi arányok és más számok esetében.

A mintavételnek számos formája létezik.

* [Az adaptív mintavételezés](../../azure-monitor/app/sampling.md) az ASP.NET SDK alapértelmezett. Az adaptív mintavételezés automatikusan igazodik az alkalmazás által küldött telemetriai adatok mennyiségéhez. Automatikusan működik az SDK-ban a webalkalmazásban, így a telemetriai forgalom a hálózaton csökken. 
* *A betöltési mintavételegy* másik lehetőség, amely azon a ponton működik, ahol az alkalmazástelemetria belép az Application Insights szolgáltatásba. A betöltési mintavételezés nincs hatással az alkalmazásból küldött telemetriai adatok mennyiségére, de csökkenti a szolgáltatás által megőrzött kötetet. A betöltési mintavételezés segítségével csökkentheti a böngészőkből és más SDK-kból származó telemetriai adatok által használt kvótát.

A betöltési mintavételezés beállításához nyissa meg a **Díjszabás iablakot:**

![A Kvóta- és árképzési ablaktáblán válassza ki a Minták csempét, majd válasszon egy mintavételi hányadot](./media/pricing/pricing-004.png)

> [!WARNING]
> Az **adatmintavételi** ablaktábla csak a betöltési mintavétel értékét szabályozza. Nem tükrözi az Application Insights SDK által az alkalmazásban alkalmazott mintavételi arányt. Ha a bejövő telemetriai adatok már mintavételezett az SDK-ban, a betöltési mintavételi nem lesz alkalmazva.
>

A tényleges mintavételi arány felderítéséhez – függetlenül attól, hogy hol alkalmazták – használjon [Analytics-lekérdezést.](analytics.md) A lekérdezés így néz ki:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

Az egyes megőrzött `itemCount` bejegyzésekben az eredeti bejegyzések számát jelzi. Ez egyenlő 1 + a korábbi eldobott rekordok száma.

## <a name="change-the-data-retention-period"></a>Az adatmegőrzési időtartam módosítása

Az Application Insights-erőforrások alapértelmezett megőrzése 90 nap. Az egyes Application Insights-erőforrásokhoz különböző megőrzési időszakok választhatók ki. A rendelkezésre álló megőrzési időszakok teljes készlete 30, 60, 90, 120, 180, 270, 365, 550 vagy 730 nap.

Az adatmegőrzés módosításához az Application Insights-erőforrásból lépjen a **Használati és becsült költségek** lapra, és válassza az **Adatmegőrzési** lehetőséget:

![A napi telemetriai kötetkorlát beállítása](./media/pricing/pricing-005.png)

A megőrzés csökkentése kor van egy több napos türelmi időszak a legrégebbi adatok eltávolítása előtt.

A megőrzési is [beállítható programozott módon a PowerShell](powershell.md#set-the-data-retention) a `retentionInDays` paraméter használatával. Ha az adatmegőrzést 30 napra állítja be, a paraméter `immediatePurgeDataOn30Days` használatával azonnal kiürítheti a régebbi adatokat, ami hasznos lehet a megfelelőségi forgatókönyvek esetén. Ez a tisztítási funkció csak az Azure Resource Manager en keresztül érhető el, és rendkívül óvatosan kell használni. Az adatkötet-korlát napi visszaállítási ideje konfigurálható az `dailyQuotaResetTime` Azure Resource Manager használatával a paraméter beállításához.

## <a name="data-transfer-charges-using-application-insights"></a>Adatátviteli díjak az Application Insights használatával

Adatok küldése az Application Insights előfordulhat, hogy az adatok sávszélessége díjakat. Az Azure [Bandwidth díjszabási lapján](https://azure.microsoft.com/pricing/details/bandwidth/)leírtak szerint az Azure-szolgáltatások közötti adatátvitel két régióban található, a normál adatátviteli díj ellenében felszámított kimenő adatátvitel. A bejövő adatátvitel ingyenes. Ez a díj azonban nagyon kicsi (néhány%) az Application Insights naplóadatok betöltésének költségeihez képest. Ennek következtében a Log Analytics költségeinek szabályozásának a bevitt adatmennyiségre [here](https://docs.microsoft.com/azure/azure-monitor/app/pricing#managing-your-data-volume)kell összpontosítania, és útmutatást nyújtunk ennek megértéséhez.

## <a name="limits-summary"></a>Korlátok összegzése

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>Napi korlátú e-mailek letiltása

A napi kötetkorlát e-mailjeinek letiltásához az Application Insights-erőforrás **Konfigurálás** szakaszában a **Használati és becsült költségek** ablaktáblában válassza a Napi **korlát**lehetőséget. A felső korlát elérésekor, valamint a beállítható figyelmeztetési szint elérésekor az e-mail küldésére is vannak beállítások. Ha le szeretné tiltani az összes napi korlátozási hangerővel kapcsolatos e-mailt, törölje mindkét négyzetet.

## <a name="legacy-enterprise-per-node-pricing-tier"></a>Örökölt vállalati (csomópontonkénti) tarifacsomag

Az Azure Application Insights korai alkalmazói számára még mindig két lehetséges tarifacsomag létezik: alapszintű és nagyvállalati. Az alapszintű tarifacsomag megegyezik a fent leírt, és az alapértelmezett szint. Ez magában foglalja az összes vállalati szintű funkciók, további költségek nélkül. Az alapszintű szint elsősorban a bevitt adatok mennyiségére számlázza.

> [!NOTE]
> Ezeket az örökölt tarifacsomagokat átnevezték. A vállalati tarifacsomag neve **mostantól csomópontonként,** az alapszintű tarifacsomag neve pedig **GB-onként**történik. Ezek az új nevek az alábbiakban és az Azure Portalon használatosak.  

A csomópontonkénti (korábban Enterprise) szint csomópontonkénti díjsal rendelkezik, és minden csomópont napi adatkeretet kap. A csomópontonkénti tarifacsomagban a benne foglalt engedmény felett betöltött adatokért díjat számítunk fel. Ha az Operations Management Suite, válassza a csomópontonkénti szint.

A pénznemben és a régióban lévő aktuális árakról az [Application Insights díjszabása](https://azure.microsoft.com/pricing/details/application-insights/).

> [!NOTE]
> 2018 áprilisában új díjszabási [modellt vezettünk be](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) az Azure figyeléséhez. Ez a modell egy egyszerű "felosztó-kiosztó" modellt alkalmaz a felügyeleti szolgáltatások teljes portfóliójában. Tudjon meg többet az [új díjszabási modellről,](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs)hogyan [mérheti fel a modellre való áttérés hatását a](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#understanding-your-azure-monitor-costs) használati minták alapján, és hogyan [iratkozhat fel az új modellre](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#azure-monitor-pricing-model)

### <a name="per-node-tier-and-operations-management-suite-subscription-entitlements"></a>Csomópontonkénti szint és Operations Management Suite előfizetési jogosultságok

Azok az ügyfelek, akik az Operations Management Suite E1 és e2 csomagot vásárolják, a korábban bejelentett további költségek nélkül további összetevőként kaphatják meg az Application Insights csomópontonkénti [összetevőit.](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/) Pontosabban az Operations Management Suite E1 és E2 minden egyes egysége jogosultságot tartalmaz az Application Insights csomópontonkénti réteg egy csomópontra vonatkozó egy csomópontjára. Minden Egyes Application Insights-csomópont naponta legfeljebb 200 MB-nyi adatot tartalmaz (a Log Analytics-adatok betöltésétől elkülönítve), 90 napos adatmegőrzéssel, további költségek nélkül. A réteg a cikk későbbi részében részletesebben ismertetjük.

Mivel ez a szint csak az Operations Management Suite-előfizetéssel rendelkező ügyfelekre vonatkozik, az Operations Management Suite-előfizetéssel nem rendelkező ügyfelek nem látják a csomag kiválasztásának lehetőségét.

> [!NOTE]
> Győződjön meg arról, hogy ezt a jogosultságot kap, az Application Insights-erőforrások at kell a csomópontonkénti tarifacsomag. Ez a jogosultság csak csomópontként érvényes. Az Application Insights-erőforrások a GB-szinten nem valósítanak meg semmilyen előnyt. Ez a jogosultság nem látható a használat és a **becsült költség** ablaktáblában látható becsült költségek között. Továbbá, ha 2018 áprilisában áthelyez egy előfizetést az új Azure figyelési díjszabási modellre, a GB-onkénti szint az egyetlen elérhető szint. Az előfizetés áthelyezése az új Azure figyelési díjszabási modellre nem ajánlott, ha Operations Management Suite-előfizetéssel rendelkezik.

### <a name="how-the-per-node-tier-works"></a>A csomópontonkénti szint működése

* Minden olyan csomópontért fizetnie kell, amely telemetriai adatokat küld a csomópontonkénti rétegben lévő alkalmazásokhoz.
  * A *csomópont* egy fizikai vagy virtuális kiszolgálógép vagy egy platform-szolgáltatásként szerepkörpéldány, amely az alkalmazást üzemelteti.
  * A fejlesztőgépek, az ügyfélböngészők és a mobileszközök nem számítanak csomópontnak.
  * Ha az alkalmazás több összetevőt, amelyek telemetriai adatokat küldenek, például egy webszolgáltatás és egy háttér-feldolgozó, az összetevők külön-külön számítják.
  * [Az élő metrikák streamelési](../../azure-monitor/app/live-stream.md) adatai nem számítanak bele árképzési célokra. Az előfizetésben a díjak csomópontonként, nem pedig alkalmazásonként értik a díjakat. Ha öt csomópont, amely telemetriai adatokat küld 12 alkalmazások, a díj öt csomópont.
* Bár a díjakat havonta adják meg, csak olyan órákért számítunk fel díjat, amikor egy csomópont telemetriát küld egy alkalmazásból. Az óránkénti díj az idézett havi díj osztva 744 (az órák száma egy 31 napos hónap).
* Az adatmennyiség napi 200 MB-os lefoglalása minden észlelt csomóponthoz meg van adva (óránkénti részletességgel). A fel nem használt adatallokáció nem kerül át egyik napról a másikra.
  * Ha úgy dönt, a csomópontonkénti tarifacsomag, minden előfizetés kap egy napi adatkeret et a csomópontok száma, amely telemetriai adatokat küld az Application Insights-erőforrások az adott előfizetésben. Így ha öt csomópont, amely egész nap küld adatokat, akkor egy 1 GB-os összevont keret 1 GB-os az adott előfizetésben lévő összes Application Insights-erőforrásra. Nem számít, ha bizonyos csomópontok több adatot küld, mint más csomópontok, mert a mellékelt adatok meg vannak osztva az összes csomópont között. Ha egy adott napon az Application Insights-erőforrások több adatot kapnak, mint amennyit az előfizetés napi adatallokációja tartalmaz, a GB-onkénti keretterhelések érvényesek. 
  * A napi adatkeret kiszámítása a nap óráinak száma (UTC használatával), hogy minden csomópont telemetriát küld osztva 24 szorozva 200 MB-mal. Így ha négy csomópont, amely elküldi a telemetriai adatok at 15 a 24 óra a nap, a mellékelt adatok az adott napra lenne ((4 &#215; 15) / 24) &#215; 200 MB = 500 MB. Az adatok túllépéséért 2,30 USD/GB áron a díj 1,15 USD lenne, ha a csomópontok 1 GB adatot küldenek aznap.
  * A csomópontonkénti napidíj nem lesz megosztva azokkal az alkalmazásokkal, amelyekhez a GB-onkénti szintet választotta. A fel nem használt juttatást nem viszik át napról napra.

### <a name="examples-of-how-to-determine-distinct-node-count"></a>Példák a különböző csomópontok számának meghatározására

| Forgatókönyv                               | Napi csomópontok teljes száma |
|:---------------------------------------|:----------------:|
| 1 alkalmazás 3 Azure App Service-példány és 1 virtuális kiszolgáló használatával | 4 |
| 3 alkalmazás fut 2 virtuális gépen; az Application Insights-erőforrások ezekhez az alkalmazásokhoz ugyanabban az előfizetésben és a csomópontonkénti rétegben találhatók | 2 | 
| 4 alkalmazás, amelynek Applications Insights-erőforrásai ugyanabban az előfizetésben vannak; minden egyes alkalmazás 2 példányt futtat 16 csúcsidőn kívül, és 4 példányt 8 csúcsidőben | 13.33 |
| Felhőszolgáltatások 1 feldolgozói szerepkörrel és 1 webes szerepkörrel, amelyek mindegyike 2 példányt futtat | 4 | 
| 5 csomós Azure Service Fabric-fürt 50 mikroszolgáltatást futtató; minden egyes mikroszolgáltatás 3 példányt futtat | 5|

* A pontos csomópontszámlálás attól függ, hogy melyik Application Insights SDK-t használja az alkalmazás. 
  * Az SDK 2.2-es és újabb verzióiban az Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) és a [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) is jelenti az egyes alkalmazásgazdanokokat csomópontként. Ilyenek például a számítógép neve a fizikai kiszolgáló és a virtuális gép gazdagépek vagy a példány nevét a felhőszolgáltatások.  Az egyetlen kivétel egy olyan alkalmazás, amely csak a .NET Core és az Application Insights Core SDK.The only exception is an application that uses only the [.NET Core](https://dotnet.github.io/) and the Application Insights Core SDK. Ebben az esetben csak egy csomópont ot jelent a program az összes állomáshoz, mert az állomásnév nem érhető el.
  * Az SDK korábbi verzióiban a [Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) úgy viselkedik, mint az újabb SDK-verziók, de a [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) csak egy csomópontot jelent, függetlenül az alkalmazásgazdagépek számától.
  * Ha az alkalmazás az SDK-t használja a **roleInstance** egyéni értékbeállításához, alapértelmezés szerint ugyanazt az értéket használja a csomópontszám meghatározásához.
  * Ha egy új SDK-verziót használ egy olyan alkalmazással, amely ügyfélgépekről vagy mobileszközökről fut, a csomópontszám nagy számot adhat vissza (az ügyfélgépek vagy mobileszközök nagy száma miatt).

## <a name="automation"></a>Automation

Írhat egy parancsfájlt a tarifacsomag beállításához az Azure Resource Management használatával. [További tudnivalókat itt talál](powershell.md#price).

## <a name="next-steps"></a>További lépések

* [Mintavételezés](../../azure-monitor/app/sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ../../azure-monitor/app/app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/