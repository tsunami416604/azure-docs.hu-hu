---
title: Használat figyelése és becsült költségek az Azure Monitorban
description: Az Azure Monitor használatának és becsült költségekoldalának használatának áttekintése
author: dalekoetke
services: azure-monitor
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: 22c7243cd966f458610c2abc67ed5540b37357b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658815"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>Használat figyelése és becsült költségek az Azure Monitorban

> [!NOTE]
> Ez a cikk ismerteti, hogyan tekintheti meg a használat és a becsült költségek több Azure figyelési funkciók. Az Azure Monitor egyes összetevőihez kapcsolódó cikkek a következők:
> - [A használat és a költségek kezelése az Azure Monitor Naplók](manage-cost-storage.md) segítségével ismerteti, hogyan szabályozhatja a költségeket az adatmegőrzési időszak módosításával, és hogyan elemezheti és figyelmeztetheti az adathasználatot.
> - [Az Application Insights használati és költségeinek kezelése](../../azure-monitor/app/pricing.md) ismerteti az adatok használatának elemzését az Application Insightsban.

## <a name="azure-monitor-pricing-model"></a>Az Azure Monitor díjszabási modellje

Az Azure Monitor alapvető számlázási modellje egy felhőbarát, fogyasztásalapú díjszabás ("Használatalapú fizetés"). A fizetés használat alapján történik. Az árképzési részletek [a riasztásokhoz, a mérőszámokhoz, az értesítésekhez](https://azure.microsoft.com/pricing/details/monitor/), a [Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) és az Application Insights [szolgáltatáshoz](https://azure.microsoft.com/pricing/details/application-insights/)érhetők el. 

A naplóadatok üzembe fizetési alapú modellje mellett a Log Analytics kapacitásfoglalásokkal is rendelkezik, amelyek lehetővé teszik, hogy akár 25%-ot is mentsen a kiosztó nasi árhoz képest. A kapacitásfoglalás díjszabása lehetővé teszi, hogy 100 GB/nap-tól kezdődőfoglalást vásároljon. A foglalási szint feletti használatot a használatalapú fizetés díja alapján számlázunk. [További információ](https://azure.microsoft.com/pricing/details/monitor/) a kapacitásfoglalás díjszabásáról.

Egyes ügyfelek hozzáférhetnek a [korábbi Log Analytics-díjszabási szintekhez](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#legacy-pricing-tiers) és a [régebbi Enterprise Application Insights-díjszabási szinthez.](https://docs.microsoft.com/azure/azure-monitor/app/pricing#legacy-enterprise-per-node-pricing-tier) 

## <a name="understanding-your-azure-monitor-costs"></a>Az Azure Monitor költségeinek ismertetése

A költségek megértésének két szakasza van. Az első az, ha az Azure Monitort tekinti figyelési megoldásnak. 

### <a name="estimating-the-costs-to-manage-your-environment"></a>A környezet kezelési költségeinek becslése

Ha még nem használja az Azure Monitor naplók, használhatja az [Azure Monitor díjkalkulátor](https://azure.microsoft.com/pricing/calculator/?service=monitor) az Azure Monitor használatának költségének becsléséhez. Kezdje azzal, hogy beírja az "Azure Monitor" kifejezést a keresőmezőbe, és az eredményül kapott Azure Monitor csempére kattint. Görgessen le a lapon az Azure Monitorhoz, és válasszon egyet a Típus legördülő menüből:

- Metrikák lekérdezések és riasztások  
- Log Analytics
- Application Insights

Ezek mindegyikében az árképzési kalkulátor segít megbecsülni a várható költségeket a várható kihasználtság alapján.

A Log Analytics segítségével például megadhatja a virtuális gépek számát és az egyes virtuális gépektől várhatóan gyűjtött adatok GB-ját. Általában 1 GB-3 GB-os adathónap egy tipikus Azure virtuális gépről kerül betöltésre. Ha már kiértékeli az Azure Monitor naplók már, használhatja az adatok statisztikáit a saját környezetében. Az alábbiakban megtudhatja, hogyan határozhatja meg a [figyelt virtuális gépek számát](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) és a munkaterület által [betöltésalatt álló adatok mennyiségét.](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)

Hasonlóképpen az Application Insights esetében, ha engedélyezi az "Adatmennyiség becslése az alkalmazástevékenység alapján" funkciót, az alkalmazással kapcsolatos bemeneteket adhat meg (havi kérelmek és havonta oldalmegtekintések, amennyiben ügyféloldali telemetriai adatokat gyűjt), majd a számológép megmondja a hasonló alkalmazások által gyűjtött adatok medián és 90. Ezek az alkalmazások az Application Insights-konfiguráció tartományát ölelik fel (például néhány alapértelmezett mintavételezéssel rendelkezik, néhánynak nincs mintavételezése stb.), így továbbra is rendelkezik a vezérlővel, hogy csökkentse a betöltési adatok mennyiségét messze a medián szint alatt mintavételezés használatával. De ez egy kiindulási pont, hogy megértsük, milyen más, hasonló ügyfelek látnak. [További információ](https://docs.microsoft.com/azure/azure-monitor/app/pricing#estimating-the-costs-to-manage-your-application) az Application Insights költségeinek becsléséről.

### <a name="understanding-your-usage-and-estimated-costs"></a>A használat és a becsült költségek ismertetése

Fontos, hogy az Azure Monitor használatával egyszer megértse és nyomon kövesse a használatot, és ehhez számos eszköz áll elő. 

Az Azure számos hasznos funkciót biztosít az [Azure Cost Management + Billing](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) hubon. Az **Azure Cost Management + Billing** hub megnyitása után kattintson a **Költségkezelés** elemre, és válassza ki a [hatókört](https://docs.microsoft.com/azure/cost-management/understand-work-scopes) (a vizsgálandó erőforrások készletét). 

Ezután az Azure Monitor elmúlt 30 nap költségeinek megtekintéséhez kattintson a **Napi költségek** csempére, válassza az "Utolsó 30 nap" lehetőséget a Relatív dátumok csoportban, és adjon hozzá egy szűrőt, amely kiválasztja a szolgáltatás neveket:

1. Azure Monitor
2. Application Insights
3. Log Analytics
4. Betekintések és elemzés

Ez olyan nézetet eredményez, mint például:

![Az Azure Cost Management képernyőképe](./media/usage-estimated-costs/010.png)

Itt részletezheti a halmozott költségösszegzést, hogy a "Költség erőforrásszerint" nézetben a finomabb részleteket kapja meg. Az aktuális tarifacsomagok ban az Azure-napló adatait ugyanazon a mérőszámon számítják fel, függetlenül attól, hogy a Log Analytics vagy az Application Insights származik-e. Ha el szeretné különválasztani a költségeket a Log Analytics vagy az Application Insights-használattól, hozzáadhat egy szűrőt az **Erőforrástípusra.** Az Application Insights összes költségének megtekintéséhez az erőforrástípusra "microsoft.insights/components" lesz, a Log Analytics-költségek esetében pedig az Erőforrástípusát a "microsoft.operationalinsights/workspaces" szóra. 

A használat további részletei az [Azure Portalról való letöltéssel](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal)érhetők el. A letöltött számolótáblában láthatja az Azure-erőforrásonkénti napi használatot. Ebben az Excel-számolótáblában az Application Insights-erőforrások használatának megtalálható a "Mérőkategória" oszlop "Application Insights" és a "Log Analytics" megjelenítéséhez történő első szűréssel, majd egy szűrő hozzáadásával a "Példányazonosító" oszlopban, amely "tartalmazza a microsoft.insights/components" oszlopot.  A legtöbb Application Insights-használat a Log Analytics mérőműszer-kategóriájával történik, mivel az Azure Monitor összes összetevőjéhez egyetlen napló-háttérrendszer tartozik.  Csak az Application Insights-erőforrások örökölt tarifacsomagok és többlépéses webes tesztek jelentik a meter kategóriája Application Insights.  A felhasználás a "Felhasznált mennyiség" oszlopban, az egyes bejegyzések egysége pedig a "Mértékegység" oszlopban látható.  További részletek a [Microsoft Azure-számlának megértéséhez](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)érhetők el. 

> [!NOTE]
> A **Cost Management** használata az Azure Cost Management + **Billing** hub az előnyben részesített megközelítés a figyelési költségek széles körű megértéséhez.  A [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understand-your-usage-and-estimate-costs) és az [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/pricing#understand-your-usage-and-estimate-costs) használati és becsült **költségek** kelkapcsolatos felhasználói élménye mélyebb betekintést nyújt az Azure Monitor egyes részeibe.

Egy másik lehetőség az Azure Monitor használatának megtekintésére a Használat és a **becsült költségek** lap a Figyelő hub. Ez az alapvető figyelési funkciók, például a [riasztás, a metrikák, az értesítések,](https://azure.microsoft.com/pricing/details/monitor/)az [Azure Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)és az Azure Application [Insights](https://azure.microsoft.com/pricing/details/application-insights/)használatát mutatja. A 2018 áprilisa előtt elérhető díjcsomagokkal kapcsolatos ügyfelek számára ez magában foglalja az Insights and Analytics ajánlaton keresztül vásárolt Log Analytics-használatot is.

Ezen a lapon a felhasználók megtekinthetik az elmúlt 31 nap erőforrás-felhasználását, előfizetésenként összesítve. `Drill-ins`31 napos időszak használati trendjeit. Sok adat kell, hogy jöjjön össze ezt a becslést, ezért kérjük, legyen türelemmel, mint az oldal betöltődik.

Ez a példa a használat figyelését és az ebből eredő költségek becslését mutatja be:

![Használati és becsült költségek portál képernyőképe](./media/usage-estimated-costs/001.png)

A havi használati oszlopban lévő hivatkozással megnyithat egy diagramot, amely az elmúlt 31 napos időszak használati trendjeit mutatja: 

![Csomópontsávdiagramonkénti képernyőkép](./media/usage-estimated-costs/002.png)

## <a name="operations-management-suite-subscription-entitlements"></a>Operations Management Suite előfizetési jogosultságok

Azok az ügyfelek, akik megvásárolták a Microsoft Operations Management Suite E1 és E2 csomagot, jogosultak a [Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) és az Application Insights csomópontonkénti adatbetöltési [jogosultságaira.](https://docs.microsoft.com/azure/application-insights/app-insights-pricing) A Log Analytics-munkaterületek vagy az Application Insights-erőforrások jogosultságainak fogadása egy adott előfizetésben: 

- A Log Analytics-munkaterületeinek a "csomópontonkénti (OMS)" tarifacsomagot kell használniuk.
- Az Application Insights-erőforrásoknak az "Enterprise" tarifacsomagot kell használniuk.

Attól függően, hogy a szervezet által megvásárolt csomag csomópontjainak száma, egyes előfizetések áthelyezése a pay-as-you-go (GB-onként) tarifacsomag előnyös lehet, de ez gondos megfontolást igényel.

> [!WARNING]
> Ha a szervezet rendelkezik a microsoftos operations management suite E1 és E2 jelenlegi rendszerű rendszerével, általában a legjobb, ha a Log Analytics-munkaterületeket a "csomópontonkénti (OMS)" tarifaszinten, az Application Insights-erőforrásokat pedig az "Enterprise" tarifacsomagban tartja. 
>
