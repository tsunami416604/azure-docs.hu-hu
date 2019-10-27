---
title: A használat és a becsült költségek figyelése Azure Monitor
description: A Azure Monitor használati és becsült költségek oldal használatának folyamatának áttekintése
author: dalekoetke
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: 56dd58afa49296ab097dfd8a6560a7191ac8c644
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72932031"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>A használat és a becsült költségek figyelése Azure Monitor

> [!NOTE]
> Ez a cikk bemutatja, hogyan tekintheti meg a használati és becsült költségeket több Azure-figyelési szolgáltatás között. A Azure Monitor adott összetevőivel kapcsolatos kapcsolódó cikkek a következők:
> - A [használat és a költségek kezelése Azure monitor naplókkal](manage-cost-storage.md) : az adatmegőrzési időszak módosításával, valamint az adatfelhasználás elemzésével és riasztásával kapcsolatos költségek szabályozása.
> - [A Application Insights használatának és költségeinek kezelése](../../azure-monitor/app/pricing.md) a Application Insights adatfelhasználásának elemzését ismerteti.

## <a name="azure-monitor-pricing-model"></a>Azure Monitor díjszabási modell

Az alapszintű Azure Monitor számlázási modell egy felhőalapú, fogyasztáson alapuló díjszabás ("utólagos elszámolású"). A fizetés használat alapján történik. A [riasztások, metrikák, értesítések](https://azure.microsoft.com/pricing/details/monitor/), [log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) és [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/)díjszabási adatai. 

A naplózási adatokra vonatkozó utólagos elszámolású modellen kívül a Log Analytics kapacitási foglalásokkal rendelkezik, amelyek lehetővé teszik, hogy az utólagos elszámolású díjszabáshoz képest akár 25%-ot is mentsen. A kapacitás foglalásának díjszabása lehetővé teszi, hogy a foglalást 100 GB/nap után vásárolja meg. A foglalási szint feletti használati díjakat az utólagos elszámolású díjszabás szerint számítjuk fel. [További](https://azure.microsoft.com/pricing/details/monitor/) információ a kapacitás foglalásának díjszabásáról.

Egyes ügyfelek hozzáférhetnek a [régi log Analytics díjszabási szintjeihez](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers) és az [örökölt vállalati Application Insights díjszabási szintjéhez](https://docs.microsoft.com/azure/azure-monitor/app/pricing#legacy-enterprise-per-node-pricing-tier). 

## <a name="understanding-your-azure-monitor-costs"></a>A Azure Monitor költségeinek megismerése

A költségek megértéséhez két fázis van. Az első a figyelési megoldás Azure Monitorének megfontolása. 

### <a name="estimating-the-costs-to-manage-your-environment"></a>A környezet kezelésével kapcsolatos költségek becslése

Ha még nem használ Azure Monitor naplókat, a [Azure monitor árképzési számológép](https://azure.microsoft.com/pricing/calculator/?service=monitor) használatával megbecsülheti a Azure monitor használatának költségeit. Először írja be a "Azure Monitor" kifejezést a keresőmezőbe, és kattintson az eredményül kapott Azure Monitor csempére. Görgessen le az oldalról Azure Monitorre, és válassza ki az egyik lehetőséget a típus legördülő listából:

- Metrikák lekérdezései és riasztásai  
- Log Analytics
- Application Insights 

Ezek mindegyikében a díjszabási kalkulátor a várt kihasználtság alapján a várható költségek becslését fogja segíteni. 

Log Analytics például megadhatja a virtuális gépek számát és az egyes virtuális gépekről begyűjteni kívánt GB-nyi adatot. Egy tipikus Azure-beli virtuális gépről általában 1 – 3 GB adathónapot kell betölteni. Ha már kiértékeli Azure Monitor naplókat, saját környezetében is használhatja az adatstatisztikát. A [figyelt virtuális gépek számának](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) és a [munkaterület által betöltött adatmennyiségnek](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)a meghatározását alább találja. 

A Application Insightshoz hasonlóan, ha engedélyezi az "adatmennyiség becslése az alkalmazási tevékenység alapján" funkciót, az alkalmazáshoz (havi kérések havonta és a havonta megjelenő nézetekhez) is megadhatja a bemeneti adatokat, ha az ügyféloldali telemetria-t gyűjt. ezt követően pedig a Számológép a hasonló alkalmazások által összegyűjtött adatmennyiség középértékét és 90%-os százalékos értékét fogja megállapítani. Ezeknek az alkalmazásoknak természetesen a Application Insights konfiguráció tartományát (például néhány alapértelmezett mintavételezéssel, néhány nem mintavételezéssel stb.) kell kiterjedniük, így továbbra is szabályozhatja, hogy milyen mennyiségű adatot vesz igénybe a medián szint alatt, a mintavételezést használva. Ez azonban egy kiindulási pont, amelyből megismerheti, hogy milyen más, hasonló ügyfelek látják. [További](https://docs.microsoft.com/azure/azure-monitor/app/pricing#estimating-the-costs-to-manage-your-application) információ a Application Insights költségeinek becsléséről.

### <a name="understanding-your-usage-and-estimated-costs"></a>A használat és a becsült költségek megismerése

Fontos megérteni és nyomon követni a használatot a Azure Monitor használata után, és számos eszköz áll rendelkezésére. 

Az Azure nagyszerű hasznos funkciókat biztosít a [Azure Cost Management + számlázási](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) központban. A **Azure Cost Management + számlázási** központ megnyitása után kattintson a **Cost Management** elemre, és válassza ki a [hatókört](https://docs.microsoft.com/azure/cost-management/understand-work-scopes) (a vizsgálni kívánt erőforrások készletét). 

Ezután az elmúlt 30 nap Azure Monitor költségeinek megtekintéséhez kattintson a "halmozott költségek" csempére, válassza az "elmúlt 30 nap" lehetőséget a relatív dátumok szakaszban, és adjon hozzá egy szűrőt, amely kiválasztja a szolgáltatás nevét:

1. Azure Monitor
2. Application Insights
3. Log Analytics
4. Insight and Analytics

Ez a következő nézetet eredményezi:

![Képernyőkép Azure Cost Management](./media/usage-estimated-costs/010.png)

Innen részletesen bemutathatja ezt a halmozott ár összegzését, hogy minél finomabb részleteket kapjon a "Cost by Resource" nézetben. 

A használat mélyebb megismerése a használatnak [Az Azure Portalról való letöltésével](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal)is megszerezhető. A letöltött számolótáblában naponta megtekintheti az Azure-erőforrások használatának számát. Ebben az Excel-táblázatban a Application Insights-erőforrásokkal való használat első szűréssel megtalálhatók a "Application Insights" és a "Log Analytics" megjelenítéséhez, majd hozzá kell adni egy szűrőt a "példány azonosítója" oszlophoz, amely "tartalmaz Microsoft. bepillantások/összetevők ".  A legtöbb Application Insights-használat a Log Analytics mérőszám-kategóriával rendelkező fogyasztásmérőn szerepel, mivel az összes Azure Monitor összetevőhöz egyetlen naplós háttér tartozik.  A rendszer csak a régi árképzési szinteken és a többlépéses webes tesztek Application Insights erőforrásait jeleníti meg Application Insights fogyasztásmérő-kategóriája alapján.  A használat megjelenik a "felhasznált mennyiség" oszlopban, és az egyes bejegyzések egysége a "mértékegység" oszlopban látható.  További részleteket a [Microsoft Azure számla megismeréséhez](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)talál. 

> [!NOTE]
> A **Cost Management** használata a **Azure Cost Management + számlázási** központban az előnyben részesített megközelítés a figyelési költségek széles körű megismerése érdekében.  A **használat és a becsült költségek** a [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understand-your-usage-and-estimate-costs) és a [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/pricing#understand-your-usage-and-estimate-costs) számára egyaránt lehetővé teszik a Azure monitor egyes részeinek mélyebb elemzését. 

Egy másik lehetőség a Azure Monitor használatának megtekintésére a figyelő központ **használati és becsült költségek** lapja. Ez az alapvető figyelési funkciók, például a [riasztások, a metrikák](https://azure.microsoft.com/pricing/details/monitor/), az értesítések, az [Azure log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)és az [Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/)használatát mutatja be. A 2018. április előtt elérhető díjszabási csomaggal rendelkező ügyfelek esetében az elemzések és az elemzési ajánlat keretében vásárolt Log Analytics használat is szerepel.

Ezen a lapon a felhasználók megtekinthetik az elmúlt 31 napra vonatkozó erőforrás-használatot, az előfizetések összesített száma alapján. `Drill-ins` a 31 napos időszakon belüli használati trendeket mutatják. Ehhez a becsléshez nagy mennyiségű adatra van szükség, ezért kérjük, legyen türelemmel az oldal betöltésekor.

Ez a példa a figyelési használatot és az eredményül kapott költségek becslését mutatja be:

![A használati és becsült költségek portál képernyőképe](./media/usage-estimated-costs/001.png)

Válassza ki a havi használati oszlop hivatkozását egy olyan diagram megnyitásához, amely az elmúlt 31 napos időszakban a használati trendeket mutatja: 

![Node Bar-diagramhoz tartozó képernyőkép](./media/usage-estimated-costs/002.png)

## <a name="operations-management-suite-subscription-entitlements"></a>Az Operations Management Suite előfizetési jogosultságai

Azok az ügyfelek, akik megvásárolták a Microsoft Operations Management Suite E1-et és az E2-t, a [log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) és a [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing)esetében a csomóponton belüli adatfeldolgozási jogosultságok A jogosultságok Log Analytics-munkaterületekhez vagy Application Insights erőforrásokhoz való fogadásához egy adott előfizetésben: 

- Log Analytics munkaterületek esetében a "node (OMS)" árképzési szintet kell használni.
- Application Insights erőforrásoknak a "vállalati" árképzési szintet kell használniuk.

A szervezet által megvásárolt csomag csomópontjainak számától függően érdemes lehet egy előfizetést áthelyezni egy utólagos elszámolású (GB-os) díjszabási rétegre, de ez gondos figyelmet igényel.

> [!WARNING]
> Ha a szervezet megvásárolta az E1 és az E2 Microsoft Operations Management Suite, általában a "csomópontos (OMS)" díjszabási szinten tartja a Log Analytics-munkaterületeket, és a "vállalati" díjszabási szinten a Application Insights erőforrásait. 
>

