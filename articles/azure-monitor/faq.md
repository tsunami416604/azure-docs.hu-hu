---
title: Azure Monitor GYIK | Microsoft Docs
description: Válaszok a Azure Monitorekkel kapcsolatos gyakori kérdésekre.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/23/2020
ms.openlocfilehash: 9f377f93ab8fef2c1ad713da6fcd6c6f14107c3f
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76986818"
---
# <a name="azure-monitor-frequently-asked-questions"></a>Azure Monitor gyakori kérdések

A Microsoft gyakori kérdései a Azure Monitorkal kapcsolatos gyakori kérdések listája.

## <a name="general"></a>Általános

### <a name="what-is-azure-monitor"></a>Mi az Azure Monitor?
[Azure monitor](overview.md) egy Azure-szolgáltatás, amely teljesítmény-és rendelkezésre állási monitorozást biztosít az Azure-ban, más felhőalapú környezetekben vagy helyszíni környezetben futó alkalmazások és szolgáltatások számára. Azure Monitor a különböző forrásokból származó adatokat egy közös adatplatformba gyűjti, ahol elemezni lehet a trendeket és a rendellenességeket. A Azure Monitor gazdag funkciói segítenek gyorsan azonosítani és reagálni azokra a kritikus helyzetekre, amelyek hatással lehetnek az alkalmazásra.

### <a name="whats-the-difference-between-azure-monitor-log-analytics-and-application-insights"></a>Mi a különbség a Azure Monitor, a Log Analytics és a Application Insights között?
Szeptember 2018-án a Microsoft kombinált Azure Monitor, Log Analytics és Application Insights egyetlen szolgáltatásba, amely hatékony, teljes körű monitorozást tesz lehetővé az alkalmazások és a rájuk támaszkodó összetevők számára. A Log Analytics és Application Insights funkciói nem változtak, bár egyes funkciók a Azure Monitorre lettek állítva, hogy jobban tükrözzék az új hatókört. A Log Analytics naplózási adatmotorja és lekérdezési nyelve már Azure Monitor naplók néven is ismert. Lásd: [Azure monitor terminológiai frissítések](terminology.md).

### <a name="what-does-azure-monitor-cost"></a>Mit jelent a Azure Monitor díja?
A Azure Monitor automatikusan engedélyezett szolgáltatásai, például a metrikák és a tevékenységek naplóinak gyűjtése díjmentes. Más szolgáltatásokkal, például a naplók lekérdezésével és a riasztásokkal kapcsolatos költségeket is tartalmaz. A részletes díjszabási információkért tekintse meg a [Azure monitor díjszabási oldalát](https://azure.microsoft.com/pricing/details/monitor/) .

### <a name="how-do-i-enable-azure-monitor"></a>Hogyan az Azure Monitor engedélyezése?
Azure Monitor engedélyezve van az új Azure-előfizetés létrehozásakor, valamint a [tevékenység naplójának](platform/activity-logs-overview.md) és a platform [metrikáinak](platform/data-platform-metrics.md) automatikus gyűjtése. [Diagnosztikai beállításokat](platform/diagnostic-settings.md) hozhat létre az Azure-erőforrások működésével kapcsolatos részletesebb információk gyűjtéséhez, valamint a [figyelési megoldások](insights/solutions.md) és elemzések hozzáadásához [, hogy](insights/insights-overview.md) további elemzéseket nyújtson az egyes szolgáltatások összegyűjtött adatairól. 

### <a name="how-do-i-access-azure-monitor"></a>Hogyan a hozzáférési Azure Monitor?
A Azure Portal **figyelés** menüjében található összes Azure monitor funkció és az adatok elérése. A különböző Azure-szolgáltatások menüjének **figyelés** szakasza ugyanazokat az eszközöket biztosítja, mint az adott erőforráshoz szűrt adatok. Azure Monitor adatok a CLI, a PowerShell és a REST API használatával számos különböző forgatókönyvhöz is elérhetők.

### <a name="is-there-an-on-premises-version-of-azure-monitor"></a>Van Azure Monitor helyszíni verziója?
Nem. A Azure Monitor egy skálázható felhőalapú szolgáltatás, amely nagy mennyiségű adattal dolgoz fel és tárol, bár a Azure Monitor a helyszínen és más felhőkben lévő erőforrásokat is képes figyelni.

### <a name="can-azure-monitor-monitor-on-premises-resources"></a>Képes Azure Monitor figyelni a helyszíni erőforrásokat?
Igen, az Azure-erőforrásokból származó monitorozási adatok gyűjtése mellett Azure Monitor adatokat gyűjthet a virtuális gépekről és az egyéb felhőben és a helyszínen lévő alkalmazásokból is. Tekintse [meg Azure monitor figyelési adatforrásait](platform/data-sources.md).

### <a name="does-azure-monitor-integrate-with-system-center-operations-manager"></a>Azure Monitor integrálódik a System Center Operations Manager?
A meglévő System Center Operations Manager felügyeleti csoport összekapcsolásával Azure Monitorheti az ügynököktől származó adatok Azure Monitor naplókba való gyűjtését. Ez lehetővé teszi a naplók és a megoldás használatát az ügynököktől gyűjtött adatok elemzéséhez. A meglévő System Center Operations Manager ügynököket úgy is konfigurálhatja, hogy közvetlenül a Azure Monitor küldje el az adatküldést. Lásd: [Operations Manager kapcsolódása Azure monitorhoz](platform/om-agents.md).

### <a name="what-ip-addresses-does-azure-monitor-use"></a>Milyen IP-címeket Azure Monitor használni?
Tekintse meg az [Application Insights által használt IP-címeket, és log Analytics](app/ip-addresses.md) az ügynökökhöz és más külső erőforrásokhoz szükséges IP-címek és portok listáját a Azure monitor eléréséhez. 

## <a name="monitoring-data"></a>Adatok monitorozása

### <a name="where-does-azure-monitor-get-its-data"></a>Hol Azure Monitor lekérni az adatgyűjtést?
A Azure Monitor különböző forrásokból gyűjt adatokat, beleértve az Azure platformról és erőforrásokból, az egyéni alkalmazásokból és a virtuális gépeken futó ügynököktől származó naplókat és mérőszámokat. Más szolgáltatások, például a Azure Security Center és a Network Watcher adatokat gyűjtenek egy Log Analytics munkaterületen, így Azure Monitor adatokkal elemezhetők. A naplók és a metrikák REST API használatával egyéni adatokat is küldhet Azure Monitor. Tekintse [meg Azure monitor figyelési adatforrásait](platform/data-sources.md).

### <a name="what-data-is-collected-by-azure-monitor"></a>Milyen adatokat gyűjtenek a Azure Monitor? 
Azure Monitor a különböző forrásokból származó adatokat [naplókba](platform/data-platform-logs.md) vagy [metrikába](platform/data-platform-metrics.md)gyűjti. Az egyes adattípusok viszonylagos előnyökkel rendelkeznek, és mindegyikük a Azure Monitor szolgáltatásainak egy adott készletét támogatja. Minden Azure-előfizetéshez egyetlen metrikai adatbázis van, míg a követelményektől függően több Log Analytics munkaterületet is létrehozhat a naplók gyűjtéséhez. Lásd: [Azure monitor adatplatform](platform/data-platform.md).

### <a name="is-there-a-maximum-amount-of-data-that-i-can-collect-in-azure-monitor"></a>Van-e a Azure Monitor összegyűjtött adatok maximális mennyisége?
A begyűjthető metrikus adatok mennyisége nincs korlátozva, de ezeket az adatokat legfeljebb 93 napig tároljuk. Lásd: [mérőszámok megőrzése](platform/data-platform-metrics.md#retention-of-metrics). Az összegyűjtött naplózási adatok mennyisége nincs korlátozva, de a Log Analytics munkaterülethez választott díjszabási szinten is hatással lehet. Tekintse meg a [díjszabás részleteit](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="how-do-i-access-data-collected-by-azure-monitor"></a>Hogyan Azure Monitor által gyűjtött adatokhoz való hozzáférést?
Az elemzések és megoldások egyéni felhasználói élményt biztosítanak a Azure Monitor tárolt adatkezelési feladatok elvégzéséhez. A Kusto lekérdezési nyelvben (KQL) írt napló lekérdezéssel közvetlenül is dolgozhat a naplózási adataival. A Azure Portalban lekérdezéseket írhat és futtathat, és interaktív módon elemezheti az adatelemzést Log Analytics használatával. A Azure Portal metrikáinak elemzése a Metrikaböngésző. Lásd: a [naplózási információk elemzése Azure monitor](log-query/log-query-overview.md) és [Az Azure Metrikaböngésző első lépései](platform/metrics-getting-started.md).





## <a name="solutions-and-insights"></a>Megoldások és bepillantást nyerhet

### <a name="what-is-an-insight-in-azure-monitor"></a>Mi a Azure Monitor betekintése?
Az adatok testreszabott figyelési élményt nyújtanak az adott Azure-szolgáltatásokhoz. Ugyanazokat a metrikákat és naplókat használják, mint a Azure Monitor egyéb funkciói, de további adatokat gyűjthetnek, és egyedi felhasználói élményt nyújthatnak a Azure Portal. Tekintse [meg Azure monitor](insights/insights-overview.md).

A Azure Portalban megjelenő információk megtekintéséhez tekintse meg **a szolgáltatás** menüjének **figyelés** menüjének és **figyelés** szakaszának áttekintés szakaszát.

### <a name="what-is-a-solution-in-azure-monitor"></a>Mi az a megoldás a Azure Monitor?
A figyelési megoldások egy adott alkalmazás vagy szolgáltatás Azure Monitor szolgáltatások alapján történő figyelésére szolgáló, csomagolt logikai készletek. A naplózási adatokat Azure Monitorba gyűjtik, és a Azure Portal gyakori felhasználói felületén keresztül nyújtanak naplózási lekérdezéseket és nézeteket az elemzéshez. Lásd: [Azure monitor figyelési megoldásai](insights/solutions.md).

Ha meg szeretné tekinteni a megoldásokat a Azure Portalban, kattintson **a** **figyelés** menü áttekintések szakaszában található **továbbiak** elemre. Kattintson a **Hozzáadás** gombra további megoldások hozzáadásához a munkaterülethez.






## <a name="logs"></a>Naplók

### <a name="whats-the-difference-between-azure-monitor-logs-and-azure-data-explorer"></a>Mi a különbség a Azure Monitor naplók és az Azure Adatkezelő között?
Az Azure Data Explorer egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. Azure Monitor naplók az Azure Adatkezelőra épülnek, és ugyanazokat a Kusto-lekérdezési nyelvet (KQL) használják néhány kisebb eltéréssel. Lásd: [Azure monitor a naplózási lekérdezés nyelvi különbségeit](log-query/data-explorer-difference.md).

### <a name="how-do-i-retrieve-log-data"></a>Hogyan beolvasni az adatnaplót?
Az összes adatok beolvasása egy Log Analytics munkaterületről a Kusto Query Language (KQL) használatával írt napló lekérdezés használatával. Írhat saját lekérdezéseket, vagy használhat olyan megoldásokat és bepillantást, amelyek egy adott alkalmazáshoz vagy szolgáltatáshoz tartozó naplózási lekérdezéseket tartalmaznak. Lásd: [Azure monitorban található naplók áttekintése](log-query/log-query-overview.md).

### <a name="what-is-a-log-analytics-workspace"></a>Mi az a Log Analytics-munkaterület?
A Azure Monitor által gyűjtött összes naplózási adatokat egy Log Analytics munkaterületen tárolja a rendszer. A munkaterület lényegében egy olyan tároló, amelyben a naplózási adatokat különböző forrásokból gyűjti a rendszer. Lehet, hogy az összes figyelési adathoz egyetlen Log Analytics munkaterület tartozik, vagy több munkaterületre vonatkozó követelmények is lehetnek. Lásd: [a Azure monitor naplók üzembe helyezésének megtervezése](platform/design-logs-deployment.md).

### <a name="can-you-move-an-existing-log-analytics-workspace-to-another-azure-subscription"></a>Át lehet helyezni egy meglévő Log Analytics munkaterületet egy másik Azure-előfizetésbe?
Áthelyezheti a munkaterületet erőforráscsoportok vagy előfizetések között, de nem egy másik régióba. Lásd: [log Analytics munkaterület áthelyezése másik előfizetésre vagy erőforráscsoporthoz](platform/move-workspace.md).

### <a name="why-cant-i-see-query-explorer-and-save-buttons-in-log-analytics"></a>Miért nem látom a Query Explorer és a Save gombokat a Log Analyticsban?

A **lekérdezési tallózó**, a **Mentés** és az **új riasztási szabály** gomb nem érhető el, ha a [lekérdezési hatókör](log-query/scope.md) egy adott erőforrásra van beállítva. Riasztások létrehozásához, illetve egy lekérdezés mentéséhez vagy betöltéséhez Log Analytics hatókörét egy munkaterületre kell korlátozni. Ha Log Analytics szeretne megnyitni a munkaterület környezetében, válassza a **Azure monitor** menüjének **naplók** elemét. A legutóbb használt munkaterület van kiválasztva, de más munkaterületet is kijelölhet. Lásd: [a naplózási lekérdezés hatóköre és időbeli tartománya Azure Monitor log Analytics](log-query/scope.md)

### <a name="why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-when-opening-log-analytics-from-a-vm"></a>Miért kapok hibaüzenetet: "az erőforrás-szolgáltató regisztrálása a Microsoft. reinsights" ehhez az előfizetéshez, hogy engedélyezze ezt a lekérdezést a virtuális gép Log Analytics megnyitásakor? 
Számos erőforrás-szolgáltató automatikusan regisztrálva van, de előfordulhat, hogy manuálisan kell regisztrálnia néhány erőforrás-szolgáltatót. A regisztráció hatóköre mindig az előfizetés. További információ: [Erőforrás-szolgáltatók és típusaik](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

### <a name="why-am-i-am-getting-no-access-error-message-when-opening-log-analytics-from-a-vm"></a>Miért nem kapok hibaüzenetet a Log Analytics virtuális gépről való megnyitásakor? 
A virtuális gépek naplófájljainak megtekintéséhez olvasási engedéllyel kell rendelkeznie a virtuálisgép-naplókat tároló munkaterületekhez. Ezekben az esetekben a rendszergazdának Önnek kell megadnia Önnek az Azure-beli engedélyeket.




## <a name="alerts"></a>Értesítések

### <a name="what-is-an-alert-in-azure-monitor"></a>Mi a riasztás a Azure Monitorban?
A riasztások proaktívan értesítik Önt, ha fontos feltételek találhatók a megfigyelési adataiban. Lehetővé teszik a problémák azonosítását és megcímzését, mielőtt a felhasználók a rendszerértesítéseket. Több fajta riasztás létezik:

- Metrika – a metrika értéke meghaladja a küszöbértéket.
- Napló lekérdezése – a naplózási lekérdezés eredményei megfelelnek a megadott feltételeknek.
- A tevékenység naplója – a műveletnapló eseménye megfelel a megadott feltételeknek.
- Webteszt – a rendelkezésre állási teszt által meghatározott feltételeknek megfelelő eredmények.


Lásd: [Microsoft Azure riasztások áttekintése](platform/alerts-overview.md).


### <a name="what-is-an-action-group"></a>Mi az a műveleti csoport?
A műveleti csoport a riasztások által aktiválható értesítések és műveletek gyűjteménye. Több riasztás is használhat egyetlen műveleti csoportot, amely lehetővé teszi az értesítések és műveletek közös készletének kihasználása. Lásd: [műveleti csoportok létrehozása és kezelése a Azure Portalban](platform/action-groups.md).


### <a name="what-is-an-action-rule"></a>Mi az a műveleti szabály?
A műveleti szabályok lehetővé teszik egy adott feltételnek megfelelő riasztások viselkedésének módosítását. Ez lehetővé teszi az ilyen követelmények elvégzését a riasztási műveletek letiltásával a karbantartási időszakokban. Egy műveleti csoportot riasztási csoportra is alkalmazhat, ahelyett, hogy közvetlenül a riasztási szabályokra alkalmazná őket. Lásd: [műveleti szabályok](platform/alerts-action-rules.md).


## <a name="agents"></a>Ügynökök

### <a name="does-azure-monitor-require-an-agent"></a>Szükség van Azure Monitor ügynökre?
Az ügynököknek csak az operációs rendszer és a virtuális gépek munkaterhelései adatainak gyűjtésére van szükségük. A virtuális gépek az Azure-ban, egy másik Felhőbeli környezetben vagy a helyszínen is megtalálhatók. Lásd: [a Azure monitor ügynökök áttekintése](platform/agents-overview.md).


### <a name="whats-the-difference-between-the-azure-monitor-agents"></a>Mi a különbség a Azure Monitor-ügynökök között?
Az Azure diagnosztikai bővítmény az Azure Virtual Machines szolgáltatáshoz kapcsolódik, és adatokat gyűjt Azure Monitor metrikák, az Azure Storage és az Azure Event Hubs számára. A Log Analytics-ügynök az Azure-beli virtuális gépek, egy másik felhőalapú környezet vagy a helyszíni, és adatokat gyűjt Azure Monitor naplókhoz. A függőségi ügynök a Log Analytics ügynök és az összegyűjtött folyamat részleteit és függőségeit igényli. Lásd: [a Azure monitor ügynökök áttekintése](platform/agents-overview.md).


### <a name="does-my-agent-traffic-use-my-expressroute-connection"></a>Használ-e az ügynöki forgalom az ExpressRoute-kapcsolatokat?
A Azure Monitor felé irányuló forgalom a Microsoft peering ExpressRoute áramkört használja. A különböző típusú ExpressRoute-forgalom leírását a [ExpressRoute dokumentációjában](../expressroute/expressroute-faqs.md#supported-services) találja. 

### <a name="how-can-i-confirm-that-the-log-analytics-agent-is-able-to-communicate-with-azure-monitor"></a>Hogyan ellenőrizhető, hogy az Log Analytics ügynök képes-e kommunikálni a Azure Monitorokkal?
Az ügynök számítógépének Vezérlőpultján válassza a **biztonsági & beállítások**, **Microsoft monitoring Agent** lehetőséget. Az **Azure log Analytics (OMS)** lapon a zöld pipa ikon megerősíti, hogy az ügynök képes kommunikálni a Azure monitorokkal. A sárga figyelmeztető ikon azt jelzi, hogy az ügynök problémába ütközik. Ennek egyik gyakori oka, hogy a **Microsoft monitoring Agent** szolgáltatás leállt. A Service Control Manager használatával indítsa újra a szolgáltatást.

### <a name="how-do-i-stop-the-log-analytics-agent-from-communicating-with-azure-monitor"></a>Hogyan leállítani a Log Analytics-ügynököt a Azure Monitorsal folytatott kommunikációhoz?
Log Analytics közvetlenül csatlakozó ügynökökhöz nyissa meg a Vezérlőpultot, és válassza a **biztonsági & beállítások**, majd a **Microsoft monitoring Agent**lehetőséget. Az **Azure log Analytics (OMS)** lapon távolítsa el a felsorolt munkaterületeket. A System Center Operations Manager távolítsa el a számítógépet a Log Analytics felügyelt számítógépek listából. Operations Manager frissíti az ügynök konfigurációját, hogy a továbbiakban ne jelentsen Log Analytics. 

### <a name="how-much-data-is-sent-per-agent"></a>Mennyibe kerül az adatküldés/ügynök?
Az ügynökök által elküldett adatok mennyisége a következőktől függ:

* Az engedélyezett megoldások
* A begyűjtött naplók és teljesítményszámlálók száma
* A naplókban tárolt adatmennyiség

A részletekért lásd: [a használat és a költségek kezelése Azure monitor naplókkal](platform/manage-cost-storage.md) .

A WireData-ügynököt futtató számítógépek esetében a következő lekérdezéssel tekintheti meg az adatküldés mennyiségét:

```Kusto
WireData
| where ProcessName == "C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe" 
| where Direction == "Outbound" 
| summarize sum(TotalBytes) by Computer 
```

### <a name="how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-azure-monitor"></a>Mennyi hálózati sávszélességet használ a Microsoft Management agent (MMA), amikor adatokat küld a Azure Monitor?
A sávszélesség a továbbított adatmennyiség függvénye. Az adat tömörítve van, mert a hálózaton keresztül küldi el a rendszer.


### <a name="how-can-i-be-notified-when-data-collection-from-the-log-analytics-agent-stops"></a>Hogyan lehet értesítést kapni, ha a Log Analytics ügynökből származó adatgyűjtés leáll?

A következő témakörben ismertetett lépéseket követve értesülhet arról, hogy az adatgyűjtés Mikor leáll: [új napló létrehozása riasztás](platform/alerts-metric.md) . Használja a következő beállításokat a riasztási szabályhoz:

- **Riasztási feltétel meghatározása**: adja meg az log Analytics munkaterületet erőforrás-célként.
- **Riasztási feltételek** 
   - **Jel neve**: *egyéni naplók keresése*
   - **Keresési lekérdezés**: `Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **Riasztási logika**: az *eredmények száma*, a **küszöbértéknél** *nagyobb*érték **alapján** *0*
   - **Értékelés alapja**: **időtartam (percben)** *30*, **gyakoriság (perc)** *10*
- **Riasztás részleteinek meghatározása** 
   - **Név**: *az adatgyűjtés leállt*
   - **Súlyosság**: *Figyelmeztetés*

Válasszon egy meglévő vagy új [műveleti csoportot](platform/action-groups.md) , hogy ha a naplózási riasztás megfelel a feltételeknek, értesítést kap, ha 15 percnél hosszabb szívverés van megadva.


### <a name="what-are-the-firewall-requirements-for-azure-monitor-agents"></a>Mik a Azure Monitor ügynökökre vonatkozó tűzfalszabályok?
A tűzfalra vonatkozó követelmények részleteiért lásd a [hálózati tűzfalakra vonatkozó követelményeket](platform/log-analytics-agent.md#network-firewall-requirements).


## <a name="visualizations"></a>Vizualizációk

### <a name="why-cant-i-cant-see-view-designer"></a>Miért nem látható a Tervező nézet?

A tervező csak közreműködői engedélyekkel rendelkező felhasználók számára érhető el a Log Analytics munkaterületen.


## <a name="application-insights"></a>Application Insights

### <a name="configuration-problems"></a>Konfigurációs problémák
*Nem sikerül beállítani a következőket:*

* [.NET-alkalmazás](app/asp-net-troubleshoot-no-data.md)
* [Már futó alkalmazás figyelése](app/monitor-performance-live-website-now.md#troubleshoot)
* [Azure-diagnosztika](platform/diagnostics-extension-to-application-insights.md)
* [Java webalkalmazások](app/java-troubleshoot.md)

*Nem kapok adatok a kiszolgálóról*

* [Tűzfal-kivételek beállítása](app/ip-addresses.md)
* [ASP.NET-kiszolgáló beállítása](app/monitor-performance-live-website-now.md)
* [Java-kiszolgáló beállítása](app/java-agent.md)

### <a name="can-i-use-application-insights-with-"></a>Használhatom a Application Insights...?

* [Webalkalmazások egy Azure-beli virtuális gépen vagy Azure-beli virtuálisgép-méretezési csoporton belüli IIS-kiszolgálón](app/azure-vm-vmss-apps.md)
* [Webalkalmazások egy helyi IIS-kiszolgálón vagy egy virtuális gépen](app/asp-net.md)
* [Java-webalkalmazások](app/java-get-started.md)
* [Node.js-alkalmazások](app/nodejs.md)
* [Webalkalmazások az Azure-ban](app/azure-web-apps.md)
* [Cloud Services az Azure-ban](app/cloudservices.md)
* [A Docker-ben futó alkalmazás-kiszolgálók](app/docker.md)
* [Egyoldalas webalkalmazások](app/javascript.md)
* [SharePoint](app/sharepoint.md)
* [Windows asztali alkalmazás](app/windows-desktop.md)
* [Más platformok](app/platforms.md)

### <a name="is-it-free"></a>Ingyenes?

Igen, kísérleti felhasználásra. Az alapszintű díjszabás keretében az alkalmazás minden hónapban díjmentesen küldhet bizonyos adatmennyiséget. Az ingyenes juttatás elég nagy a fejlesztéshez, és egy alkalmazás közzététele kis számú felhasználó számára. Beállíthat egy korlátot, amely megakadályozza a megadott mennyiségű adatok feldolgozását.

A GB-nál nagyobb mennyiségű telemetria számítunk fel. Néhány tippet adunk a [díjak korlátozására](app/pricing.md).

A nagyvállalati csomag minden nap esetében díjat számít fel, amelyet minden egyes webkiszolgáló-csomópont telemetria küld. Megfelelő, ha a folyamatos exportálást nagy léptékben kívánja használni.

[Olvassa el a díjszabási tervet](https://azure.microsoft.com/pricing/details/application-insights/).

### <a name="how-much-does-it-cost"></a>Mennyibe kerül?

* Nyissa meg a **használati és becsült költségek lapot** egy Application Insights erőforrásban. Van egy diagram a közelmúltbeli használatról. Ha kívánja, beállíthatja az adatmennyiség korlátját.
* Nyissa meg az [Azure számlázási](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) paneljét, és tekintse meg a számlákat az összes erőforráson.

### <a name="q14"></a>Mit Application Insights módosítani a projektben?
A részletek a projekt típusától függenek. Webalkalmazások esetén:

* Hozzáadja ezeket a fájlokat a projekthez:
  * ApplicationInsights.config
  * ai. js
* A következő NuGet-csomagokat telepíti:
  * *Application INSIGHTS API* – a Core API
  * *Application INSIGHTS API webalkalmazásokhoz* – telemetria küldésére szolgál a kiszolgálóról
  * *Application INSIGHTS API JavaScript-alkalmazásokhoz* – telemetria küldésére szolgál az ügyféltől
* A csomagok közé tartoznak a következő szerelvények:
  * Microsoft. ApplicationInsights
  * Microsoft. ApplicationInsights. platform
* Elemek beszúrása a következőkbe:
  * Web.config
  * packages. config
* (Csak új projektek – ha [Application Insightst ad hozzá egy meglévő projekthez][start], ezt manuálisan kell elvégeznie.) Kódrészleteket szúr be az ügyfél és a kiszolgáló kódjába az Application Insights erőforrás-AZONOSÍTÓval való inicializáláshoz. Egy MVC-alkalmazásban például a rendszer beszúrja a kódot a mesterlap-nézetbe, illetve a megosztott/\_elrendezésbe. cshtml

### <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Hogyan frissíteni a régebbi SDK-verziókról?
Tekintse meg az SDK [kibocsátási megjegyzéseit](app/release-notes.md) , amelyek megfelelnek az adott alkalmazás típusának.

### <a name="update"></a>Hogyan változtathatom meg, hogy a projekt melyik Azure-erőforráshoz küld adatokat?
A Megoldáskezelő kattintson a jobb gombbal az `ApplicationInsights.config` elemre, majd válassza a **frissítés Application Insights**lehetőséget. Az Azure-ban egy meglévő vagy új erőforráshoz is elküldheti az adott adatforrást. A frissítési varázsló megváltoztatja a kialakítási kulcsot a ApplicationInsights. config fájlban, amely meghatározza, hogy a kiszolgáló SDK hogyan küldje el az adatokat. Ha kijelöli az "összes frissítése" lehetőséget, akkor az azt is megváltoztatja, hogy a kulcs hol jelenik meg a weblapok között.

### <a name="what-is-status-monitor"></a>Mi az Állapotfigyelő?

Egy asztali alkalmazás, amelyet az IIS-webkiszolgálóban használhat a Application Insights webalkalmazásokban való konfigurálásához. Nem gyűjt telemetria: leállíthatja, ha nem konfigurál egy alkalmazást. 

[További információk](app/monitor-performance-live-website-now.md#questions).

### <a name="what-telemetry-is-collected-by-application-insights"></a>Milyen telemetria gyűjtenek Application Insights?

A Server Web appsből:

* HTTP-kérések
* [Függőségek](app/asp-net-dependencies.md). Hívások: SQL-adatbázisok; HTTP-hívások külső szolgáltatásokhoz; Azure Cosmos DB, tábla, blob Storage és üzenetsor. 
* [Kivételek](app/asp-net-exceptions.md) és verem-Nyomkövetések.
* [Teljesítményszámlálók](app/performance-counters.md) – ha [Állapotmonitort](app/monitor-performance-live-website-now.md)használ, a [app Services Azure-figyelést](app/azure-web-apps.md), a [virtuális gép vagy a virtuálisgép-méretezési csoport azure-figyelését](app/azure-vm-vmss-apps.md), vagy a [Application Insights gyűjtött író](app/java-collectd.md).
* [Egyéni események és mérőszámok](app/api-custom-events-metrics.md) .
* [Nyomkövetési naplók](app/asp-net-trace-logs.md) , ha a megfelelő gyűjtőt konfigurálja.

Az [ügyfél weblapjairól](app/javascript.md):

* [Oldalmegtekintések száma](app/usage-overview.md)
* [Ajax-hívások](app/asp-net-dependencies.md) Futó parancsfájlból végrehajtott kérelmek.
* Az oldal nézet betöltési adatkészlete
* Felhasználók és munkamenetek száma
* [Hitelesített felhasználói azonosítók](app/api-custom-events-metrics.md#authenticated-users)

Más forrásokból, ha konfigurálja őket:

* [Azure-diagnosztika](platform/diagnostics-extension-to-application-insights.md)
* [Importálás az Analytics szolgáltatásba](platform/data-collector-api.md)
* [Log Analytics](platform/data-collector-api.md)
* [Logstash](platform/data-collector-api.md)

### <a name="can-i-filter-out-or-modify-some-telemetry"></a>Kiszűrhetők vagy módosíthatok néhány telemetria?

Igen, a kiszolgálón írhat:

* A telemetria a kiválasztott telemetria-elemekhez tartozó tulajdonságokat szűrheti vagy adja hozzá az alkalmazásból való elküldése előtt.
* A telemetria inicializáló tulajdonságot adhat hozzá a telemetria összes eleméhez.

További információ a [ASP.net](app/api-filtering-sampling.md) és a [Java](app/java-filter-telemetry.md)-ról.

### <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>Hogyan számítják ki a város, az ország/régió és az egyéb földrajzi hely adatértékét?

A [GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/)használatával megkeresjük a webes ügyfél IP-címét (IPv4 vagy IPv6).

* Böngésző telemetria: összegyűjtjük a küldő IP-címét.
* Kiszolgáló telemetria: a Application Insights modul gyűjti az ügyfél IP-címét. Ha `X-Forwarded-For` van beállítva, a rendszer nem gyűjti.
* Ha többet szeretne megtudni arról, hogy az IP-cím és a térinformatikai adatok hogyan kerülnek gyűjtésre Application Insights tekintse meg ezt a [cikket](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection).


A `ClientIpHeaderTelemetryInitializer` úgy is beállíthatja, hogy az IP-címet egy másik fejlécből hajtsa végre. Egyes rendszerekben például egy proxy, terheléselosztó vagy CDN áthelyezi a `X-Originating-IP`ba. [További információk](https://apmtips.com/blog/2016/07/05/client-ip-address/).

A [Power bi](app/export-power-bi.md ) segítségével megjelenítheti a kérések telemetria egy térképen.


### <a name="data"></a>Mennyi ideig tartanak a portálon tárolt adat? Biztonságos?
Vessen egy pillantást az [adatok megőrzésére és az adatvédelemre][data].

### <a name="what-happens-to-application-insights-telemetry-when-a-server-or-device-loses-connection-with-azure"></a>Mi történik az alkalmazás-betekintési telemetria, ha egy kiszolgáló vagy eszköz elveszti az Azure-ral való kapcsolódást?

Az összes SDK, beleértve a web SDK-t is, "megbízható átvitel" vagy "robusztus szállítás". Ha a kiszolgáló vagy az eszköz elveszti a kapcsolatot az Azure-val, a telemetria [a fájlrendszeren](https://docs.microsoft.com/azure/azure-monitor/app/data-retention-privacy#does-the-sdk-create-temporary-local-storage) (Server SDK-k) vagy a HTML5 munkamenet-tárolóban (web SDK) helyileg tárolja a rendszer. Az SDK rendszeres időközönként újra megpróbálja elküldeni ezt a telemetria, amíg a betöltési szolgáltatás nem tekinti az "elavult" (48 órás naplók, 30 perc a metrikák esetében). A rendszer elveti az elavult telemetria. Bizonyos esetekben, például ha a helyi tárterület megtelt, a rendszer nem hajtja végre az újrapróbálkozást.


### <a name="could-personal-data-be-sent-in-the-telemetry"></a>A személyes adatküldés a telemetria?

Ez akkor lehetséges, ha a kód ilyen adatokat küld. Akkor is előfordulhat, ha a stack-nyomkövetésben szereplő változók személyes adatba tartoznak. A fejlesztési csapatnak kockázatértékelést kell végeznie, hogy a személyes adatai megfelelően legyenek kezelve. [További információ az adatmegőrzésről és az adatvédelemről](app/data-retention-privacy.md).

Az ügyfél-webcímek **minden** oktettje mindig 0-ra van állítva a földrajzi hely attribútumainak megvizsgálása után.

### <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>A saját kialakítási kulcs látható a saját weblap forrásában. 

* Ez a figyelési megoldások általános gyakorlata.
* Nem használható az adatai ellopására.
* Felhasználhatja az adatait, vagy riasztásokat indíthat.
* Nem tudtuk meghallgatni, hogy minden ügyfélnek ilyen problémája volt.

A következőket teheti:

* Az ügyfél-és a kiszolgálói adatforrásokhoz két különálló rendszerkulcs (külön Application Insights erőforrás) használata szükséges. vagy
* Írjon be egy proxyt, amely a kiszolgálón fut, és hogy a webes ügyfél az adott proxyn keresztül küldje el az adatait.

### <a name="post"></a>Hogyan lásd: az adatposta a diagnosztikai keresésben?
A rendszer nem naplózza az adatpostát, de TrackTrace hívást is használhat: az üzenet paraméterében elhelyezheti az adatbevitelt. Ez hosszabb mérethatárt, mint a karakterlánc-tulajdonságok korlátai, de nem szűrheti.

### <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Egy vagy több Application Insights-erőforrást használok?

Egyetlen erőforrás használata egyetlen üzleti rendszeren lévő összes összetevőhöz vagy szerepkörhöz. Külön erőforrásokat használhat a fejlesztési, tesztelési és kiadási verziókhoz, valamint a független alkalmazásokhoz.

* [Itt tekintheti meg a vitát](app/separate-resources.md)
* [Példa – felhőalapú szolgáltatás munkavégző és webes szerepkörökkel](app/cloudservices.md)

### <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Hogyan dinamikusan módosítja a kialakítási kulcsot?

* [Vitafórum](app/separate-resources.md)
* [Példa – felhőalapú szolgáltatás munkavégző és webes szerepkörökkel](app/cloudservices.md)

### <a name="what-are-the-user-and-session-counts"></a>Mik a felhasználók és a munkamenetek száma?

* A JavaScript SDK beállítja a felhasználói cookie-t a webes ügyfélen, a visszatérő felhasználók azonosítására, valamint egy munkamenet-cookie-t a tevékenységek csoportosítására.
* Ha nincs ügyféloldali parancsfájl, beállíthatja [a cookie-kat a kiszolgálón](https://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/).
* Ha egy valós felhasználó különböző böngészőkben használja a webhelyét, vagy ha privát vagy inkognitóbani böngészést vagy különböző gépeket használ, a rendszer egynél többször veszi fel őket.
* A bejelentkezett felhasználók számítógépek és böngészők közötti azonosításához vegyen fel egy hívást a [setAuthenticatedUserContext ()](app/api-custom-events-metrics.md#authenticated-users)szolgáltatásba.

### <a name="q17"></a>Engedélyeztem mindent Application Insights?
| Mit kell látnia? | Útmutató | Miért szeretné |
| --- | --- | --- |
| Rendelkezésre állási diagramok |[Webes tesztek](app/monitor-web-app-availability.md) |Ismerje meg a webalkalmazását |
| Server app Perf: válaszidő,... |[Application Insights hozzáadása a projekthez](app/asp-net.md) vagy [AI-Állapotmonitor telepítése a kiszolgálón](app/monitor-performance-live-website-now.md) (vagy saját kód írása a [függőségek nyomon követéséhez](app/api-custom-events-metrics.md#trackdependency)) |A Teljesítményfigyelő hibáinak észlelése |
| Függőségi telemetria |[AI-Állapotmonitor telepítése a kiszolgálón](app/monitor-performance-live-website-now.md) |Az adatbázisokkal vagy más külső összetevőkkel kapcsolatos problémák diagnosztizálása |
| Verem nyomkövetésének lekérése kivételekről |[TrackException-hívások beszúrása a kódban](app/asp-net-exceptions.md) (de néhányat automatikusan kell jelenteni) |Kivételek észlelése és diagnosztizálása |
| Keresési naplók nyomkövetése |[Naplózási adapter hozzáadása](app/asp-net-trace-logs.md) |Kivételek diagnosztizálása, Perf-problémák |
| Az ügyfél használatának alapjai: oldalletöltések, munkamenetek,... |[JavaScript-inicializálás a weblapokon](app/javascript.md) |Használatelemzés |
| Ügyfél egyéni metrikái |[Hívások követése a weblapokon](app/api-custom-events-metrics.md) |A felhasználói élmény fejlesztése |
| Kiszolgáló egyéni metrikái |[Hívások követése a kiszolgálón](app/api-custom-events-metrics.md) |Üzleti intelligencia |

### <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Miért nem egyenlő a keresési és a metrikák diagramjai?

A [mintavétel](app/sampling.md) csökkenti az alkalmazásból a portálra ténylegesen küldött telemetria elemek (kérelmek, egyéni események stb.) számát. A keresés területen láthatja a ténylegesen fogadott elemek számát. Az események számát megjelenítő mérőszám-diagramoknál megtekintheti a bekövetkezett eredeti események számát. 

Minden továbbított tétel egy `itemCount` tulajdonságot jelenít meg, amely azt mutatja, hogy hány eredeti eseményt képvisel az adott tétel. A mintavétel működés közbeni megfigyeléséhez futtassa ezt a lekérdezést az Analyticsben:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


### <a name="automation"></a>Automation

#### <a name="configuring-application-insights"></a>Application Insights konfigurálása

A PowerShell-szkripteket az Azure erőforrás-figyelő használatával is [megírhatja](app/powershell.md) :

* Application Insights erőforrások létrehozása és frissítése.
* Állítsa be a díjszabási tervet.
* Szerezze be a kialakítási kulcsot.
* Metrikai riasztás hozzáadása.
* Adja meg a rendelkezésre állási tesztet.

Nem állítható be metrikai Explorer-jelentés, vagy nem állítható be folyamatos exportálás.

#### <a name="querying-the-telemetry"></a>A telemetria lekérdezése

[Analytics](app/analytics.md) -lekérdezések futtatásához használja a [REST API](https://dev.applicationinsights.io/) .

### <a name="how-can-i-set-an-alert-on-an-event"></a>Hogyan állíthatok be riasztást eseményre?

Az Azure-riasztások csak mérőszámokon alapulnak. Hozzon létre egy egyéni mérőszámot, amely egy érték küszöbértékét adja meg, amikor az esemény bekövetkezik. Ezután állítson be egy riasztást a metrikán. Értesítést kap, ha a metrika mindkét irányban átlépi a küszöbértéket; nem kap értesítést az első átlépésig, függetlenül attól, hogy a kezdeti érték magas vagy alacsony; a késések mindig néhány percet vesznek igénybe.

### <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Vannak adatátviteli díjak az Azure-webalkalmazások és a Application Insights között?

* Ha az Azure-webalkalmazás egy olyan adatközpontban található, amelyben Application Insights gyűjtemény végpontja található, díjmentesen használható. 
* Ha nem található gyűjteményi végpont a gazdagép adatközpontjában, akkor az alkalmazás telemetria az Azure-beli [kimenő díjakat](https://azure.microsoft.com/pricing/details/bandwidth/)is felmerül.

Ez nem függ attól, hogy hol található a Application Insights-erőforrás. Csak a végpontok eloszlása függ.

### <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Küldhetek telemetria az Application Insights portálra?

Javasoljuk, hogy használja az SDK-kat, és használja az [SDK API](app/api-custom-events-metrics.md)-t. Különböző [platformokon](app/platforms.md)léteznek az SDK különféle változatai. Ezek az SDK-k pufferelést, tömörítést, szabályozást, újrapróbálkozást és így tovább kezelik. A betöltési [séma](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) és a [végpont protokoll](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) azonban nyilvános.

### <a name="can-i-monitor-an-intranet-web-server"></a>Nyomon követhető az intranetes webkiszolgáló?

Igen, de engedélyeznie kell a szolgáltatásoknak a tűzfal-kivételek vagy a proxy-átirányítások által nyújtott forgalmat.
- QuickPulse `https://rt.services.visualstudio.com:443` 
- ApplicationIdProvider `https://dc.services.visualstudio.com:443` 
- TelemetryChannel `https://dc.services.visualstudio.com:443` 


Tekintse át a szolgáltatások és IP-címek teljes listáját [itt](app/ip-addresses.md).

#### <a name="firewall-exception"></a>Tűzfal-kivétel

Lehetővé teszi, hogy a webkiszolgáló telemetria küldjön a végpontoknak. 

#### <a name="gateway-redirect"></a>Átjáró átirányítása

Átirányíthatja a forgalmat a kiszolgálóról az intranetes átjáróra a konfigurációban található végpontok felülírásával. Ha ezek a "végpont" tulajdonságok nem szerepelnek a konfigurációban, ezek az osztályok az alapértelmezett értékeket fogják használni az alábbi példában látható ApplicationInsights. config fájlban. 

Az átjárónak a végpont alapcímeihez kell irányítani a forgalmat. A konfigurációban cserélje le az alapértelmezett értékeket `http://<your.gateway.address>/<relative path>`ra.


##### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>Példa ApplicationInsights. config alapértelmezett végpontokkal:
```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://rt.services.visualstudio.com/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.services.visualstudio.com/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.services.visualstudio.com/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

> [!NOTE]
> A ApplicationIdProvider a v 2.6.0-től kezdődően érhető el.



#### <a name="proxy-passthrough"></a>Proxy átengedése

A proxy továbbítása a gépi vagy az alkalmazás szintű proxy konfigurálásával érhető el.
További információ: a [DefaultProxy](https://docs.microsoft.com/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings)-ra vonatkozó DotNet-cikk.
 
 Példa web. config:
 ```xml
<system.net>
    <defaultProxy>
      <proxy proxyaddress="http://xx.xx.xx.xx:yyyy" bypassonlocal="true"/>
    </defaultProxy>
</system.net>
```
 

### <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Futtathatok rendelkezésre állási webes teszteket egy intranetes kiszolgálón?

A [webes tesztek](app/monitor-web-app-availability.md) a világ minden részén elosztott, jelenléti pontokon futnak. Két megoldás létezik:

* Tűzfal ajtaja – engedélyezze a kérelmeket [a kiszolgálónak a webes tesztek hosszú és módosítható listájáról](app/ip-addresses.md).
* Saját kód megírásával rendszeres kérelmeket küldhet a kiszolgálónak az intraneten belülről. A Visual Studio webes tesztek erre a célra is futtathatók. A Tester a TrackAvailability () API használatával küldheti el az eredményeket Application Insights.

### <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>Mennyi időt vesz igénybe a telemetria gyűjtése?

A legtöbb Application Insights adat 5 perces késéssel rendelkezik. Egyes adatsorok hosszabb időt vehetnek igénybe; általában nagyobb naplófájlok. További információ: [Application INSIGHTS SLA](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/).



<!--Link references-->

[data]: app/data-retention-privacy.md
[platforms]: app/platforms.md
[start]: app/app-insights-overview.md
[windows]: app/app-insights-windows-get-started.md



## <a name="azure-monitor-for-containers"></a>Azure Monitor tárolókhoz

Ez a Microsoft gyakori kérdések listája a Azure Monitor for containers szolgáltatással kapcsolatos gyakori kérdésekre mutat. Ha további kérdései vannak a megoldással kapcsolatban, látogasson el a [vitafórumra](https://feedback.azure.com/forums/34192--general-feedback) , és tegye fel kérdéseit. Ha egy kérdést gyakran megkérdeznek, azt a cikkhez adja hozzá, hogy gyorsan és könnyen elérhető legyen.

### <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>Nem látom a ContainerLog tábla lekérdezése során kitöltött képek és nevek tulajdonság értékét.

Az ügynök verziójának ciprod12042019 és újabb verzióiban alapértelmezés szerint ez a két tulajdonság nem töltődik fel minden egyes naplófájlhoz, hogy csökkentse a begyűjtött naplózási adatokhoz felmerülő költségeket. Az alábbi két lehetőség közül választhat a tulajdonságokat tartalmazó tábla lekérdezéséhez:

#### <a name="option-1"></a>1\. lehetőség 

Csatlakozás más táblákhoz, hogy ezek a tulajdonságértékek szerepeljenek az eredmények között.

Módosítsa a lekérdezéseket úgy, hogy a ContainerID tulajdonsághoz való csatlakozással a rendszerkép és a ImageTag tulajdonságokat is tartalmazza a ```ContainerInventory``` táblából. A name ```ContainerLog``` (név) tulajdonságot belefoglalhatja a KubepodInventory tábla ContaineName mezőjéből a ContainerID tulajdonsághoz való csatlakozással. Ez az ajánlott lehetőség.

A következő példa egy példaként szolgáló részletes lekérdezést tartalmaz, amely ismerteti, hogyan lehet beolvasni ezeket a mezőértékeket az illesztésekkel.

```
//lets say we are querying an hour worth of logs
let startTime = ago(1h);
let endTime = now();
//below gets the latest Image & ImageTag for every containerID, during the time window
let ContainerInv = ContainerInventory | where TimeGenerated >= startTime and TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID, Image, ImageTag | project-away TimeGenerated | project ContainerID1=ContainerID, Image1=Image ,ImageTag1=ImageTag;
//below gets the latest Name for every containerID, during the time window
let KubePodInv  = KubePodInventory | where ContainerID != "" | where TimeGenerated >= startTime | where TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID2 = ContainerID, Name1=ContainerName | project ContainerID2 , Name1;
//now join the above 2 to get a 'jointed table' that has name, image & imagetag. Outer left is safer in-case there are no kubepod records are if they are latent
let ContainerData = ContainerInv | join kind=leftouter (KubePodInv) on $left.ContainerID1 == $right.ContainerID2;
//now join ContainerLog table with the 'jointed table' above and project-away redundant fields/columns and rename columns that were re-written
//Outer left is safer so you dont lose logs even if we cannot find container metadata for loglines (due to latency, time skew between data types etc...)
ContainerLog
| where TimeGenerated >= startTime and TimeGenerated < endTime 
| join kind= leftouter (
   ContainerData
) on $left.ContainerID == $right.ContainerID2 | project-away ContainerID1, ContainerID2, Name, Image, ImageTag | project-rename Name = Name1, Image=Image1, ImageTag=ImageTag1 

```

#### <a name="option-2"></a>2\. lehetőség

Engedélyezze újra a gyűjteményt ezen tulajdonságok esetében minden egyes tároló-naplófájlhoz.

Ha az első lehetőség nem alkalmas a lekérdezési változások miatt, a mezők begyűjtését újra engedélyezheti, ha engedélyezi a ```log_collection_settings.enrich_container_logs``` az ügynök konfigurációs térképén, az [adatgyűjtés konfigurációs beállításai](insights/container-insights-agent-config.md)részben leírtak szerint.

> [!NOTE]
> A második lehetőség nem ajánlott olyan nagyméretű fürtök esetében, amelyek több mint 50 csomóponttal rendelkeznek, mert az API-kiszolgáló hívásokat kezdeményez a fürt minden csomópontján, hogy elvégezze ezt a dúsítást. Ez a beállítás az adatok méretét is növeli minden összegyűjtött naplófájl esetében.

### <a name="can-i-view-metrics-collected-in-grafana"></a>Megtekinthetem a Grafana összegyűjtött mérőszámokat?

A tárolók Azure Monitor támogatja a Grafana-irányítópultokon a Log Analytics munkaterületen tárolt mérőszámok megtekintését. A Grafana [irányítópult-tárházból](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) letölthető sablon segítségével elsajátíthatja az első lépéseket, és megtudhatja, hogyan kérdezheti le a figyelt fürtök további adatait az egyéni Grafana-irányítópultok megjelenítéséhez. 

### <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>Megfigyelhető az AK-motor fürtje Azure Monitor for containers használatával?

A tárolók Azure Monitor támogatja az Azure-ban üzemeltetett, AK-motor (korábbi nevén ACS-motor) fürt (ek) ben üzembe helyezett, a tároló munkaterheléseit. További részletek és áttekintés a forgatókönyv figyelésének engedélyezéséhez szükséges lépésekről: a [Azure monitor használata a tárolók számára az AK-motorhoz](https://github.com/microsoft/OMS-docker/tree/aks-engine).

### <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Miért nem láthatók a Log Analytics munkaterület adatai?

Ha a Log Analytics munkaterületen nem tud adatokat látni a mindennapi időpontokban, előfordulhat, hogy elérte az alapértelmezett 500 MB-os korlátot, vagy a naponta begyűjthető adatok mennyiségének szabályozására megadott napi korlátot. Ha a napi korlát teljesül, az adatgyűjtés csak a következő napon leáll, és folytatja a műveletet. Tekintse át az adatfelhasználást, és frissítsen egy másik díjszabási csomagra a várt használati szokások alapján: az [adatok használatának és költségének naplózása](platform/manage-cost-storage.md). 

### <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Mik a ContainerInventory táblában megadott tárolók állapotai?

A ContainerInventory tábla a leállított és futó tárolókkal kapcsolatos információkat tartalmaz. A tábla az ügynökön belüli munkafolyamattal van feltöltve, amely lekérdezi a Docker-t az összes tárolónál (futó és leállított), és továbbítja az adatokat a Log Analytics munkaterületen.
 
### <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Hogyan a *hiányzó előfizetés-regisztrációs* hiba elhárítása?

Ha a **Microsoft. OperationsManagement előfizetés-regisztrációja hiányzik**, akkor a Microsoft. OperationsManagement erőforrás-szolgáltató regisztrálása az előfizetésben, ahol a munkaterület meg van adva, a **Microsoft.** . Ennek a dokumentációja [itt](../azure-resource-manager/templates/error-register-resource-provider.md)található.

### <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>Támogatja a RBAC-kompatibilis AK-fürtöket?

A tároló-figyelési megoldás nem támogatja a RBAC, de a tárolók esetében Azure Monitor is támogatott. Előfordulhat, hogy a megoldás részleteit tartalmazó lap nem jeleníti meg a megfelelő információkat a fürtök adatait megjelenítő pengék között.

### <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Hogyan lehetővé teszi a naplók gyűjtését a Kube-System névtérben a Helm használatával?

Alapértelmezés szerint le van tiltva a Kube-rendszernévtérben lévő tárolók naplójának gyűjteménye. A omsagent egy környezeti változó beállításával engedélyezhető a naplók gyűjteménye. További információ: [Azure monitor for containers](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) GitHub oldal. 

### <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Hogyan frissíteni a omsagent a legújabb kiadású verzióra?

Az ügynök frissítésének megismeréséhez tekintse meg az [ügynökök kezelése](insights/container-insights-manage-agent.md)című témakört.

### <a name="how-do-i-enable-multi-line-logging"></a>Hogyan a többsoros naplózás engedélyezése?

A tárolók számára jelenleg Azure Monitor nem támogatja a többsoros naplózást, de vannak elérhető áthidaló megoldások. Az összes szolgáltatást JSON formátumban is konfigurálhatja, majd a Docker/Moby egyetlen sorba írja azokat.

Például becsomagolhatja a naplót JSON-objektumként, ahogy az alábbi példában is látható egy példa Node. js-alkalmazáshoz:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Az alábbi példához hasonlóan a naplók esetében a következő példa jelenik meg Azure Monitorban:

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

A probléma részletes megtekintéséhez tekintse meg a következő GitHub- [hivatkozást](https://github.com/moby/moby/issues/22920).

### <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Az élő naplók engedélyezésekor Hogyan az Azure AD-hibák elhárítása? 

A következő hibaüzenet jelenhet meg: a **kérelemben megadott válasz URL-cím nem egyezik az alkalmazáshoz konfigurált válasz URL-címekkel: "< Application ID\>"** . A megoldás megoldása a következő cikkben található: a [tárolók információinak valós idejű megtekintése Azure monitor a tárolók](insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication)használatával. 

### <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Miért nem tudom frissíteni a fürtöt a bevezetést követően?

Ha egy AK-fürthöz engedélyezte a Azure Monitort a tárolók számára, akkor törölje azt a Log Analytics-munkaterületet, amelyhez a fürt az adatokat küldi, amikor a fürt frissítésére tett kísérlet során sikertelen lesz. Ennek megkerüléséhez le kell tiltania a figyelést, majd újra engedélyeznie kell, hogy az előfizetés egy másik érvényes munkaterületre hivatkozik. Ha újra megpróbálja végrehajtani a fürt frissítését, akkor a folyamatnak sikeresen fel kell dolgoznia és el kell végeznie a műveletet.  

### <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>Mely portokra és tartományokra van szükségem az ügynök megnyitásához/engedélyezési listához?

Tekintse meg a [hálózati tűzfalra vonatkozó követelményeket](insights/container-insights-onboard.md#network-firewall-requirements) a proxyra és a tűzfalra vonatkozó konfigurációs információkhoz, amelyek a tároló ügynökhöz szükségesek az Azure, az Azure US government és az Azure China 21Vianet-felhők esetében.

## <a name="azure-monitor-for-vms-preview"></a>Azure Monitor for VMs (előzetes verzió)
A Microsoft gyakori kérdései a Azure Monitor for VMskal kapcsolatos gyakori kérdések listája. Ha további kérdései vannak a megoldással kapcsolatban, látogasson el a [vitafórumra](https://feedback.azure.com/forums/34192--general-feedback) , és tegye fel kérdéseit. Ha egy kérdést gyakran megkérdeznek, azt a cikkhez adja hozzá, hogy gyorsan és könnyen elérhető legyen.

### <a name="can-i-onboard-to-an-existing-workspace"></a>Bejelentkezhetek egy meglévő munkaterületre?
Ha a virtuális gépek már csatlakoznak egy Log Analytics-munkaterülethez, akkor továbbra is használhatja ezt a munkaterületet Azure Monitor for VMsba való bevezetéskor, amennyiben az [itt](insights/vminsights-enable-overview.md#prerequisites)felsorolt támogatott régiók egyikében található.

A bevezetéskor a munkaterülethez olyan teljesítményszámlálók vannak konfigurálva, amelyek az összes, a munkaterületnek adatokat jelentő virtuális gépet begyűjtik, hogy a megjelenítéshez és az elemzéshez Azure Monitor for VMs az adatokat.  Ennek eredményeképpen a kiválasztott munkaterülethez csatlakoztatott összes virtuális gép teljesítményadatait fogja látni.  Az állapot és a leképezés funkció csak a bevezetéshez megadott virtuális gépek esetében engedélyezett.

A teljesítményszámlálók engedélyezésével kapcsolatos további információkért tekintse meg az [Engedélyezés áttekintését ismertető](insights/vminsights-enable-overview.md#performance-counters-enabled) cikket.

### <a name="can-i-onboard-to-a-new-workspace"></a>Bejelentkezhetek egy új munkaterületre? 
Ha a virtuális gépek jelenleg nem kapcsolódnak meglévő Log Analytics-munkaterülethez, létre kell hoznia egy új munkaterületet az adatai tárolásához. Az új alapértelmezett munkaterület létrehozása automatikusan történik, ha egyetlen Azure-beli virtuális gépet állít be Azure Monitor for VMs a Azure Portal keresztül.

Ha úgy dönt, hogy a parancsfájl-alapú módszert használja, ezeket a lépéseket a [Azure PowerShell vagy Resource Manager-sablon használatával foglalkozó Azure monitor for VMS engedélyezése (előzetes verzió)](insights/vminsights-enable-at-scale-powershell.md) című cikk tartalmazza. 

### <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>Mi a teendő, ha a virtuális gép már jelent egy meglévő munkaterületet?
Ha már begyűjti az adatait a virtuális gépekről, lehetséges, hogy már konfigurálta az adatok jelentését egy meglévő Log Analytics-munkaterületre.  Ha a munkaterület az egyik támogatott régióban található, akkor a Azure Monitor for VMst engedélyezheti a meglévő munkaterületen.  Ha a már használt munkaterület nem a támogatott régiókban található, akkor a Azure Monitor for VMs jelenleg nem fog tudni bejelentkezni.  Aktívan dolgozunk a további régiók támogatásán.

>[!NOTE]
>A munkaterülethez tartozó összes virtuális gépet érintő teljesítményszámlálókat a munkaterülethez konfiguráljuk, függetlenül attól, hogy Azure Monitor for VMsbe kívánja-e bevezetni őket. A munkaterülethez tartozó teljesítményszámlálók konfigurálásával kapcsolatos további információkért tekintse meg a [dokumentációt](platform/data-sources-performance-counters.md). A Azure Monitor for VMshoz konfigurált számlálókkal kapcsolatos információkért tekintse meg a [Azure monitor for VMS engedélyezése](insights/vminsights-enable-overview.md#performance-counters-enabled) című cikket.  

### <a name="why-did-my-vm-fail-to-onboard"></a>Miért nem sikerült bejelentkezni a virtuális gépre?
Amikor Azure-beli virtuális gépet telepít a Azure Portalból, a következő lépések történnek:

* Ha ez a beállítás be van jelölve, egy alapértelmezett Log Analytics munkaterület jön létre.
* A teljesítményszámlálók konfigurálva vannak a kiválasztott munkaterülethez. Ha ez a lépés meghiúsul, láthatja, hogy a teljesítmény-diagramok és a táblák némelyike nem jeleníti meg a bekészített virtuális gép adatait. Ezt az [itt](insights/vminsights-enable-at-scale-powershell.md#enable-performance-counters)leírt PowerShell-szkript futtatásával javíthatja.
* A Log Analytics ügynök virtuálisgép-bővítmény használatával települ az Azure-beli virtuális gépekre, ha azt meg kell határozni.  
* A Azure Monitor for VMs Map-függőségi ügynök az Azure-beli virtuális gépeken bővítmény használatával van telepítve, ha azt meg kell határozni.  
* Azure Monitor az állapotfigyelő funkciót támogató összetevők konfigurálása szükséges, és a virtuális gép az állapotadatok jelentésére van konfigurálva.

A bevezetési folyamat során a fent ismertetett állapotot vizsgáljuk, hogy az értesítési állapotot a portálon vissza lehessen adni. A munkaterület és az ügynök telepítésének konfigurálása általában 5 – 10 percet vesz igénybe. A figyelési és egészségvédelmi információk megtekintése a portálon további 5 – 10 percet vesz igénybe.  

Ha kezdeményezte a bevezetést, és megtekinti, hogy a virtuális gépet be kell-e állítani, akkor akár 30 percet is igénybe vehet, amíg a virtuális gép elvégezheti a folyamatot. 

### <a name="i-only-enabled-azure-monitor-for-vms-why-do-i-see-all-my-vms-monitored-by-the-health-feature"></a>Csak a Azure Monitor for VMs Engedélyeztem, Miért látok minden virtuális gépet az állapotfigyelő szolgáltatás által figyelt állapottal?
Az állapotfigyelő szolgáltatás minden olyan virtuális gép esetében engedélyezve van, amely a Log Analytics munkaterülethez van csatlakoztatva, még akkor is, ha a művelet egyetlen virtuális gépre van kezdeményezve.

### <a name="can-i-modify-the-schedule-for-when-health-criteria-evaluates-a-condition"></a>Módosíthatom azt az időpontot, amikor az állapot feltételei kiértékelik a feltételt?
Nem, az időtartam és az állapot gyakorisága nem módosítható ezzel a kiadással. 

### <a name="can-i-disable-health-criteria-for-a-condition-i-dont-need-to-monitor"></a>Letilthatom az állapotra vonatkozó feltételeket olyan feltételnél, amelyet nem kell figyelni?
Ebben a kiadásban nem lehet letiltani az állapotra vonatkozó feltételeket.

### <a name="are-the-health-alert-severities-configurable"></a>Konfigurálható az állapot-riasztási tartomány?  
Az állapot riasztásának súlyossága nem módosítható, csak engedélyezhető vagy letiltható. Emellett néhány riasztási tagság az állapot feltételei alapján frissül. 

### <a name="if-i-reconfigure-the-settings-of-a-particular-health-criteria-can-it-be-scoped-to-a-specific-instance"></a>Ha újrakonfiguráltam egy adott állapotra vonatkozó feltétel beállításait, hatóköre alkalmazható egy adott példányra?  
Ha módosítja az állapot-feltétel példányának bármelyik beállítását, akkor az Azure-beli virtuális gépen található azonos típusú összes állapotra vonatkozó feltétel módosítva lesz. Ha például a lemez szabad területének állapotára vonatkozó feltétel példánya a C logikai lemeznek felel meg, akkor ez a küszöbérték az azonos virtuális géphez felderített és felügyelt összes többi logikai lemezre vonatkozik.

### <a name="does-the-health-feature-monitor-logical-processors-and-cores"></a>Figyeli az állapotfigyelő szolgáltatás a logikai processzorokat és a magokat?
Nem, az egyes processzorok és a logikai processzorok szintjének állapotára vonatkozó feltételek nem szerepelnek a Windowsban, a rendszer alapértelmezés szerint csak a teljes CPU-kihasználtságot figyeli, hogy az Azure-beli virtuális gép számára elérhető logikai processzorok száma alapján hatékonyan kiértékelje a processzor terhelését. 

### <a name="are-all-health-criteria-thresholds-configurable"></a>Az összes állapotra vonatkozó feltétel küszöbértéke konfigurálható?  
A Windows rendszerű virtuális gépeket megcélozó állapotra vonatkozó feltételek küszöbértékei nem módosíthatók, mert az állapotuk a *Futtatás* vagy az *elérhető*állapotra van állítva. Amikor lekérdezi az állapotot a [munkaterhelés-figyelő API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)-ból, a **LessThan** vagy a **GreaterThan** *ÖsszehasonlítóOperátor* értékét a szolgáltatás vagy az entitás esetében a **4-es** *értékkel* jeleníti meg, ha:
   - A DNS-ügyfél Service Health – a szolgáltatás nem fut. 
   - DHCP-ügyfélszolgáltatás állapota – a szolgáltatás nem fut. 
   - RPC Service Health – a szolgáltatás nem fut. 
   - A Windows tűzfal szolgáltatás állapota – a szolgáltatás nem fut.
   - A Windows Eseménynapló szolgáltatás állapota – a szolgáltatás nem fut. 
   - Kiszolgáló szolgáltatás állapota – a szolgáltatás nem fut. 
   - A Windows távfelügyeleti szolgáltatás állapota – a szolgáltatás nem fut. 
   - Fájlrendszer hibája vagy sérülése – a logikai lemez nem érhető el.

A következő linuxos állapotra vonatkozó feltételek küszöbértékei nem módosíthatók, mert az állapotuk már *igaz*értékre van állítva. Az állapot a **LessThan** és az **1** . *küszöbértéket* tartalmazó *ÖsszehasonlítóOperátor* jeleníti meg, amikor a rendszer az entitáshoz tartozó munkaterhelés-figyelési API-ból kérdezi le, a környezettől függően:
   - Logikai lemez állapota – a logikai lemez nem online/elérhető
   - Lemez állapota – a lemez nem online állapotú/elérhető
   - Hálózati adapter állapota – a hálózati adapter le van tiltva

### <a name="how-do-i-modify-alerts-that-are-included-with-the-health-feature"></a>Hogyan módosítja a Health szolgáltatásban található riasztásokat?
Az egyes állapot-kritériumokhoz definiált riasztási szabályok nem jelennek meg a Azure Portal. Csak a [munkaterhelés-figyelő API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)-ban engedélyezheti vagy tilthatja le az állapot riasztási szabályait. A Azure Portalban nem rendelhet [Azure monitor műveleti csoportot](platform/action-groups.md) az állapottal kapcsolatos riasztásokhoz. Az értesítési beállítások API-val csak akkor állítható be, ha egy adott állapotra figyelmeztető riasztást indít el. Jelenleg a virtuális gépekhez hozzárendelheti a műveleti csoportokat, hogy az összes, a virtuális gépen indított *állapottal kapcsolatos riasztás* ugyanazzal a műveleti csoporttal legyen elindítva. A hagyományos Azure-riasztásokkal ellentétben az egyes állapot-riasztási szabályokhoz nem tartoznak külön műveleti csoportok. Emellett csak az e-mailek vagy SMS-értesítések küldésére konfigurált műveleti csoportok támogatottak az állapot-riasztások aktiválásakor. 

### <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Nem látok olyan információt, amely a virtuális gép teljesítmény-diagramjaiban található
Ha nem látja a teljesítményadatokat a lemez táblában vagy egyes teljesítmény-diagramoknál, akkor előfordulhat, hogy a teljesítményszámlálók nem konfigurálhatók a munkaterületen. A megoldáshoz futtassa a következő [PowerShell-szkriptet](insights/vminsights-enable-at-scale-powershell.md#enable-with-powershell).

### <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Miben különbözik az Azure Monitor for VMs Map funkció a Service Maptól?
Az Azure Monitor for VMs-leképezési funkció Service Mapon alapul, de a következő különbségek vannak:

* A Térkép nézet a virtuális gép paneljéről, illetve a Azure Monitor alatt Azure Monitor for VMs érhető el.
* A térképen lévő kapcsolatok most már rákattintanak, és megjelenítik a kapcsolat metrikájának adatait a kijelölt kapcsolat oldalsó paneljén.
* Létezik egy új API, amely a térképek létrehozásához használható összetettebb térképek jobb támogatásához.
* A figyelt virtuális gépek már szerepelnek az ügyféloldali csoport csomópontban, és a fánk diagramon látható a csoportban lévő figyelt vs nem figyelt virtuális gépek aránya.  A csoport kibontásakor a számítógépek listájának szűrésére is használható.
* A figyelt virtuális gépek már szerepelnek a kiszolgáló portjának csomópontjaiban, és a fánk diagramon a figyelt és a nem figyelt gépek aránya látható a csoportban.  A csoport kibontásakor a számítógépek listájának szűrésére is használható.
* A Térkép stílusa úgy lett frissítve, hogy jobban konzisztens legyen az Application-adatokból származó app Map szolgáltatással.
* Az oldalsó panelek frissítve lettek, és nem rendelkeznek a Service Map-Update Management, a Change Tracking, a Security és a Service Desk által támogatott teljes integrációs készlettel. 
* A csoportok és gépek leképezésre való kiválasztásának lehetősége frissítve lett, és mostantól támogatja az előfizetéseket, az erőforráscsoportok, az Azure virtuálisgép-méretezési csoportokat és a Cloud Services szolgáltatást.
* Az új Service Map számítógépcsoportok nem hozhatók létre a Azure Monitor for VMs Map szolgáltatásban.  

### <a name="why-do-my-performance-charts-show-dotted-lines"></a>Miért mutatnak pontozott vonalakat a teljesítmény-diagramok?
Ez néhány ok miatt fordulhat elő.  Abban az esetben, ha az adatgyűjtésben hézag van, a vonalakat pontozott értékre ábrázoljuk.  Ha módosította az adatok mintavételi gyakoriságát az engedélyezett teljesítményszámlálók esetében (az alapértelmezett beállítás az adatok összegyűjtése 60 másodpercenként), akkor a diagramon szaggatott vonalakat láthat, ha a diagramhoz a keskeny időtartományt választja, és a mintavételi gyakoriság kisebb, mint a diagramon használt gyűjtő mérete (például a mintavételezési gyakoriság 10 percenként, a diagram minden gyűjtője pedig 5 perc).  Ha szélesebb időtartományt szeretne megtekinteni, akkor a diagram sorai nem pontokként, hanem folytonos vonalakként jelennek meg.

### <a name="are-groups-supported-with-azure-monitor-for-vms"></a>Támogatottak-e a csoportok Azure Monitor for VMs?
Igen, a függőségi ügynök telepítése után adatokat gyűjtünk a virtuális gépekről az előfizetés, az erőforráscsoport, a virtuálisgép-méretezési csoportok és a Cloud Services alapján.  Ha már használta a Service Map és létrehozott számítógép-csoportokat, ezek is megjelennek.  A számítógépcsoportok akkor is megjelennek a csoportok szűrőben, ha létrehozta őket a megtekintett munkaterülethez. 

### <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Hogyan tekintse meg a 95. percentilis-sor az összesített teljesítményű diagramokon való vezetésének részleteit?
Alapértelmezés szerint a lista úgy van rendezve, hogy megjelenítse azokat a virtuális gépeket, amelyek a 95. percentilis legmagasabb értékkel rendelkeznek a kiválasztott metrika esetében, kivéve a rendelkezésre álló memória diagramot, amely az 5. percentilis legalacsonyabb értékkel rendelkező gépeket jeleníti meg.  A diagramra kattintva megnyílik a **legfelső N listanézet** nézet a megfelelő metrika kiválasztásával.

### <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>Hogyan kezeli a Map szolgáltatás a duplikált IP-címeket különböző virtuális hálózatok és alhálózatokon?
Ha az IP-tartományokat virtuális gépek vagy Azure virtuálisgép-méretezési csoportok között duplikálja az alhálózatok és a virtuális hálózatok között, akkor Azure Monitor for VMs leképezés helytelen információk megjelenítésére vezethet. Ez egy ismert probléma, amely a tapasztalatok fejlesztését vizsgálja.

### <a name="does-map-feature-support-ipv6"></a>Támogatja az IPv6 a Map funkciót?
A Térkép funkció jelenleg csak az IPv4-t támogatja, és az IPv6 támogatását vizsgáljuk. Az IPv6-on belül bújtatott IPv4-t is támogatja.

### <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>Egy erőforráscsoport vagy más nagyméretű csoport térképének betöltésekor nehéz megtekinteni a térképet
Habár a nagy és összetett konfigurációk kezelésére tettük elérhetővé a térképet, rájövünk, hogy a térképen számos csomópont, kapcsolat és csomópont működik fürtként.  Elkötelezettek vagyunk a skálázhatóság növelésének támogatásával.   

### <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>Miért különbözik a hálózati diagram a teljesítmény lapon, mint a hálózati diagram az Azure-beli virtuális gépek áttekintő oldalán?

Az Azure-beli virtuális gépek áttekintő lapja diagramokat jelenít meg a vendég virtuális gép tevékenységének mérése alapján.  Az Azure-beli virtuális gépek áttekintő hálózati diagramja csak a számlázásra kerülő hálózati forgalmat jeleníti meg.  Ez nem tartalmazza a virtuális hálózatok közötti forgalmat.  A Azure Monitor for VMs számára megjelenített adatok és diagramok a vendég virtuális gépről származó adatokon alapulnak, és a hálózati diagram megjeleníti az összes bejövő és kimenő TCP/IP-forgalmat, beleértve a virtuális gépek közötti hálózatot is.

### <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>Hogyan mérjük a válaszidő a VMConnection tárolt és a kapcsolatok paneljén és a munkafüzetek között?

A válaszidő egy közelítés. Mivel nem használjuk az alkalmazás kódját, nem igazán tudjuk, hogy mikor kezdődik a kérelem, és mikor érkezik a válasz. Ehelyett megfigyeljük, hogy a rendszer egy kapcsolatban küldi az adatküldést, majd az adott kapcsolatban visszaérkező adatforrásokat. Az ügynök nyomon követi ezeket a küldési és fogadási kísérleteket, és párosítja őket: az elküldött sorok sorozata, amelyet a fogadások sorozata, a kérelem/válasz pároknak kell értelmezni. A műveletek közötti időzítés a válaszidő. Ez magában foglalja a hálózati késést és a kiszolgáló feldolgozási idejét is.

Ez a közelítés a kérelmek/válaszok alapjául szolgáló protokollok esetében jól működik: egyetlen kérelem érkezik a hálózatra, és egyetlen válasz érkezik. Ez a HTTP (S) esetén (csővezeték nélkül), de más protokollok esetében nem teljesül.

### <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>A korlátozások vonatkoznak a Log Analytics ingyenes díjszabási csomagra?
Ha az *ingyenes* díjszabási csomaggal konfigurálta a Azure monitort egy log Analytics munkaterülettel, Azure monitor for VMS a Térkép funkció csak a munkaterülethez csatlakozó öt csatlakoztatott gépet fogja támogatni. Ha az ingyenes munkaterülethez öt virtuális gép csatlakozik, az egyik virtuális gép leválasztását követően később egy új virtuális gépet csatlakoztat, az új virtuális gép nem lesz figyelve, és a Térkép oldalon jelenik meg.  

Ebben az esetben a rendszer megkéri a **kipróbálás most** lehetőséget, amikor megnyitja a virtuális gépet, és kiválasztja az adatok **(előzetes verzió)** elemet a bal oldali ablaktáblán, még azután is, hogy már telepítette a virtuális gépre.  Azonban nem kell megadnia a beállításokat, mivel ez általában akkor fordul elő, ha a virtuális gép nem lett előkészítve a Azure Monitor for VMs. 


## <a name="next-steps"></a>Következő lépések
Ha a kérdés itt nem válaszol, további kérdéseit és válaszait a következő fórumokon tekintheti meg.

- [Log Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights)
- [Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)

Ha Azure Monitor általános visszajelzést szeretne kapni, látogasson el a [visszajelzési fórumra](https://feedback.azure.com/forums/34192--general-feedback).
