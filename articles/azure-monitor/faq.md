---
title: Azure Monitor – gyakori kérdések | Microsoft dokumentumok
description: Válaszok az Azure Monitorral kapcsolatos gyakori kérdésekre.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/26/2020
ms.openlocfilehash: 777e4e1f8fdd05345d949fe8c78b4a5b1953b8b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298249"
---
# <a name="azure-monitor-frequently-asked-questions"></a>Azure Monitor – gyakori kérdések

Ez a Microsoft GYIK az Azure Monitorral kapcsolatos gyakori kérdések listája.

## <a name="general"></a>Általános kérdések

### <a name="what-is-azure-monitor"></a>Mi az Azure Monitor?
Az [Azure Monitor](overview.md) egy azure-beli szolgáltatás, amely az Azure-ban, más felhőkörnyezetekben vagy a helyszínen lévő alkalmazások és szolgáltatások teljesítmény- és rendelkezésre állásfigyelését biztosítja. Az Azure Monitor több forrásból gyűjt adatokat egy közös adatplatformba, ahol elemezhetőak a trendek és az anomáliák számára. Az Azure Monitor gazdag funkciói segítséget nyújtanak az alkalmazásra esetleg érintő kritikus helyzetek gyors azonosításában és megválaszolásában.

### <a name="whats-the-difference-between-azure-monitor-log-analytics-and-application-insights"></a>Mi a különbség az Azure Monitor, a Log Analytics és az Application Insights között?
2018 szeptemberében a Microsoft egyetlen szolgáltatásba egyesítette az Azure Monitort, a Log Analytics-et és az Application Insightsot, hogy hatékony, teljes körű figyelést biztosítson az alkalmazások és az általuk megbízhat összetevők számára. A Log Analytics és az Application Insights szolgáltatásai nem változtak, bár egyes funkciók átlettek újnéven az Azure Monitorra annak érdekében, hogy jobban tükrözzék az új hatókörüket. A log adatmotor és a lekérdezési nyelv a Log Analytics most már nevezik Azure Monitor naplók. Lásd: [Az Azure Monitor terminológiai frissítései.](terminology.md)

### <a name="what-does-azure-monitor-cost"></a>Mennyibe kerül az Azure Monitor?
Az Azure Monitor automatikusan engedélyezett, például a metrikák és a tevékenységnaplók gyűjteménye díjmentesen biztosított. Más funkciók, például a naplólekérdezések és a riasztások költségei is járnak. Tekintse meg az [Azure Monitor díjszabási oldalon](https://azure.microsoft.com/pricing/details/monitor/) részletes díjszabási információkat.

### <a name="how-do-i-enable-azure-monitor"></a>Hogyan engedélyezhetem az Azure Monitort?
Az Azure Monitor abban a pillanatban engedélyezve van, amikor új Azure-előfizetést hoz létre, és [a tevékenységnapló](platform/activity-logs-overview.md) és a platform [metrikái](platform/data-platform-metrics.md) automatikusan összegyűjtésre kerülnek. [Diagnosztikai beállításokat](platform/diagnostic-settings.md) hozhat létre az Azure-erőforrások működésével kapcsolatos részletesebb információk összegyűjtéséhez, és [figyelési megoldások](insights/solutions.md) és [elemzések](insights/insights-overview.md) hozzáadásával további elemzéseket biztosítaz egyes szolgáltatások összegyűjtött adatairól. 

### <a name="how-do-i-access-azure-monitor"></a>Hogyan érhetem el az Azure Monitort?
Az Azure Monitor összes funkcióját és adatát elérheti az Azure Portal **Monitor** menüjéből. A **figyelési** szakasz a menü különböző Azure-szolgáltatások hozzáférést biztosít ugyanazokhoz az eszközökhöz egy adott erőforrásra szűrt adatokkal. Az Azure Monitor-adatok a CLI, a PowerShell és a REST API használatával is elérhetők a különböző forgatókönyvek számára.

### <a name="is-there-an-on-premises-version-of-azure-monitor"></a>Van az Azure Monitor helyszíni verziója?
Nem. Az Azure Monitor egy méretezhető felhőszolgáltatás, amely nagy mennyiségű adatot dolgoz fel és tárol, bár az Azure Monitor figyelheti a helyszíni és más felhőkben lévő erőforrásokat.

### <a name="can-azure-monitor-monitor-on-premises-resources"></a>Az Azure Monitor figyelheti a helyszíni erőforrásokat?
Igen, az Azure-erőforrásokból származó figyelési adatok gyűjtése mellett az Azure Monitor adatokat gyűjthet a virtuális gépekről és alkalmazásokból más felhőkben és a helyszínen. Lásd: [Az Azure Monitor figyelési adatainak forrásai.](platform/data-sources.md)

### <a name="does-azure-monitor-integrate-with-system-center-operations-manager"></a>Integrálja az Azure Monitort a System Center Operations Managerrel?
A meglévő System Center Operations Manager felügyeleti csoport az Azure Monitorhoz csatlakoztatva adatokat gyűjthet az ügynököktől az Azure Monitor naplókba. Ez lehetővé teszi, hogy naplólekérdezések és megoldás segítségével elemezze az ügynököktől gyűjtött adatokat. Beállíthatja a meglévő System Center Operations Manager-ügynököket is, hogy közvetlenül az Azure Monitornak küldjenek adatokat. Lásd: [Operations Manager csatlakoztatása az Azure Monitorhoz.](platform/om-agents.md)

### <a name="what-ip-addresses-does-azure-monitor-use"></a>Milyen IP-címeket használ az Azure Monitor?
Tekintse meg [az Application Insights és a Log Analytics által használt IP-címeket](app/ip-addresses.md) az ügynökök és más külső erőforrások eléréséhez szükséges IP-címek és portok felsorolására. 

## <a name="monitoring-data"></a>Adatok monitorozása

### <a name="where-does-azure-monitor-get-its-data"></a>Honnan szerzi be az Azure Monitor az adatait?
Az Azure Monitor különböző forrásokból gyűjt adatokat, például az Azure platformról és az erőforrásokból, az egyéni alkalmazásokból és a virtuális gépeken futó ügynökökből származó naplókat és metrikákat. Más szolgáltatások, például az Azure Security Center és a Network Watcher adatokat gyűjtenek egy Log Analytics-munkaterületre, így elemezhetők az Azure Monitor adataival. Egyéni adatokat is küldhet az Azure Monitornak a REST API-val a naplókhoz vagy a metrikákhoz. Lásd: [Az Azure Monitor figyelési adatainak forrásai.](platform/data-sources.md)

### <a name="what-data-is-collected-by-azure-monitor"></a>Milyen adatokat gyűjt az Azure Monitor? 
Az Azure Monitor különböző forrásokból gyűjtadatokat [naplókba](platform/data-platform-logs.md) vagy [metrikákba.](platform/data-platform-metrics.md) Minden adattípusnak megvannak a maga relatív előnyei, és mindegyik támogatja az Azure Monitor egy adott szolgáltatáskészletét. Minden Azure-előfizetéshez egyetlen metrika-adatbázis található, miközben több Log Analytics-munkaterületet is létrehozhat a naplók összegyűjtéséhez a követelményektől függően. Lásd: [Azure Monitor adatplatform.](platform/data-platform.md)

### <a name="is-there-a-maximum-amount-of-data-that-i-can-collect-in-azure-monitor"></a>Van-e maximális mennyiségű adat, amelyet összegyűjthetek az Azure Monitorban?
Nincs korlátozva a metrikus adatok gyűjthető, de ezek az adatok tárolása legfeljebb 93 napig. Lásd: [Metrikák megőrzése](platform/data-platform-metrics.md#retention-of-metrics). Nincs korlátozva a naplóadatok gyűjtése, de ez hatással lehet a log analytics-munkaterület által választott tarifacsomag. Lásd [az árképzés részleteit](https://azure.microsoft.com/pricing/details/monitor/).

### <a name="how-do-i-access-data-collected-by-azure-monitor"></a>Hogyan érhetem el az Azure Monitor által gyűjtött adatokat?
Az elemzések és a megoldások egyéni élményt nyújtanak az Azure Monitorban tárolt adatokkal való munkához. A kusto-lekérdezési nyelv (KQL) nyelven írt naplólekérdezés használatával közvetlenül dolgozhat a naplóadatokkal. Az Azure Portalon lekérdezéseket írhat és futtathat, és interaktívan elemezheti az adatokat a Log Analytics használatával. A metrikák elemzése az Azure Portalon a Metrikák Intézővel. Lásd: [Naplóadatok elemzése az Azure Monitorban](log-query/log-query-overview.md) és [Az Azure Metrics Explorer első lépései](platform/metrics-getting-started.md)című témakört.

## <a name="solutions-and-insights"></a>Megoldások és betekintések

### <a name="what-is-an-insight-in-azure-monitor"></a>Mi az azure monitorbe adott betekintés?
Az insights személyre szabott figyelési élményt nyújt az adott Azure-szolgáltatásokhoz. Ugyanazokat a metrikákat és naplókat használják, mint az Azure Monitor más funkciói, de további adatokat gyűjthetnek, és egyedi élményt nyújthatnak az Azure Portalon. Lásd: [Insights az Azure Monitorban.](insights/insights-overview.md)

Az Azure Portalon az insights megtekintéséhez tekintse meg a **Figyelő** menü **Insights** szakaszát vagy a szolgáltatás **menüfigyelés** szakaszát.

### <a name="what-is-a-solution-in-azure-monitor"></a>Mi a megoldás az Azure Monitorban?
A figyelési megoldások egy adott alkalmazás vagy szolgáltatás Azure Monitor-funkciók on alapuló, logikával csomagolt készletei. Naplóadatokat gyűjtenek az Azure Monitorban, és az Azure Portalon közös felhasználói felület használatával naplólekérdezéseket és nézeteket biztosítanak az elemzéshez. Lásd: [Figyelési megoldások az Azure Monitorban.](insights/solutions.md)

Az Azure Portalon a megoldások megtekintéséhez kattintson a Figyelő menü **Egyebek** szakaszában az **Egyebek** szakasz a Tovább **gombra.** Kattintson a **Hozzáadás** gombra, ha további megoldásokat szeretne hozzáadni a munkaterülethez.

## <a name="logs"></a>Naplók

### <a name="whats-the-difference-between-azure-monitor-logs-and-azure-data-explorer"></a>Mi a különbség az Azure Monitor-naplók és az Azure Data Explorer között?
Az Azure Adatkezelő egy gyors és hatékonyan skálázható adatáttekintési szolgáltatás napló- és telemetriaadatokhoz. Az Azure Monitor Naplók épül az Azure Data Explorer, és ugyanazt a Kusto lekérdezési nyelv (KQL) néhány kisebb különbség. Lásd: [Az Azure Monitor naplólekérdezési nyelvi különbségei.](log-query/data-explorer-difference.md)

### <a name="how-do-i-retrieve-log-data"></a>Hogyan kérhetem le a naplóadatokat?
Minden adat beolvasása a Log Analytics munkaterület segítségével egy naplólekérdezés tanusta lekérdezési nyelv (KQL) használatával. Írhat saját lekérdezéseket, vagy használhat olyan megoldásokat és elemzéseket, amelyek naplólekérdezéseket tartalmaznak egy adott alkalmazáshoz vagy szolgáltatáshoz. Lásd: [A naplólekérdezések áttekintése az Azure Monitorban.](log-query/log-query-overview.md)

### <a name="what-is-a-log-analytics-workspace"></a>Mi a Log Analytics-munkaterület?
Az Azure Monitor által gyűjtött összes naplóadat egy Log Analytics-munkaterületen tárolódik. A munkaterület lényegében egy tároló, ahol a naplóadatok at különböző forrásokból gyűjtik. Előfordulhat, hogy egyetlen Log Analytics-munkaterülettel rendelkezik az összes figyelési adathoz, vagy több munkaterületre vonatkozó követelményekkel rendelkezik. Lásd: [Az Azure Monitor-naplók központi telepítésének tervezése.](platform/design-logs-deployment.md)

### <a name="can-you-move-an-existing-log-analytics-workspace-to-another-azure-subscription"></a>Áthelyezhet egy meglévő Log Analytics-munkaterületet egy másik Azure-előfizetésbe?
A munkaterületet áthelyezheti az erőforráscsoportok vagy előfizetések között, de nem egy másik régióba. Lásd: [A Naplóelemzés munkaterületének áthelyezése különböző előfizetésre vagy erőforráscsoportra.](platform/move-workspace.md)

### <a name="why-cant-i-see-query-explorer-and-save-buttons-in-log-analytics"></a>Miért nem látom a Lekérdezéskezelő t és a Mentés gombokat a Log Analytics szolgáltatásban?

**A Lekérdezéskezelő**, **a Mentés** és **az Új riasztási szabály** gombjai nem érhetők el, ha a [lekérdezéshatókör](log-query/scope.md) egy adott erőforrásra van beállítva. Riasztások létrehozásához, a lekérdezés mentéséhez vagy betöltéséhez a Log Analytics hatókörét egy munkaterületre kell hatókörbe tenni. A Log Analytics munkaterületi környezetben való megnyitásához válassza az **Azure Monitor** menü **Naplók parancsát.** Az utoljára használt munkaterület van kiválasztva, de bármely más munkaterületet kijelölhet. Lásd: [Lekérdezéshatókör és időtartomány naplózása az Azure Monitor loganalytics szolgáltatásában](log-query/scope.md)

### <a name="why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-when-opening-log-analytics-from-a-vm"></a>Miért jelenik meg a hiba: "Regisztrálja az erőforrás-szolgáltató "Microsoft.Insights" az előfizetés, hogy ezt a lekérdezést" megnyitásakor Log Analytics egy virtuális gép? 
Számos erőforrás-szolgáltató automatikusan regisztrálva van, de előfordulhat, hogy manuálisan kell regisztrálnia néhány erőforrás-szolgáltatót. A regisztráció hatóköre mindig az előfizetés. További információ: [Erőforrás-szolgáltatók és típusaik](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

### <a name="why-am-i-am-getting-no-access-error-message-when-opening-log-analytics-from-a-vm"></a>Miért nem kapok hozzáférési hibaüzenetet, amikor a Log Analytics-et virtuális gépről nyitom meg? 
A virtuális gép naplók megtekintéséhez olvasási engedéllyel kell rendelkeznie a virtuális gép naplókat tároló munkaterületek számára. Ezekben az esetekben a rendszergazdának meg kell adnia önnek az Azure-beli engedélyeket.

## <a name="alerts"></a>Riasztások

### <a name="what-is-an-alert-in-azure-monitor"></a>Mi az azure-figyelő riasztása?
Riasztások proaktív módon értesíti Önt, ha fontos feltételek találhatók a figyelési adatokat. Lehetővé teszik a problémák azonosítását és kezelését, mielőtt a rendszer felhasználói észrevennék őket. Többféle riasztás létezik:

- Metrika – A metrika értéke meghaladja a küszöbértéket.
- Naplólekérdezés – A naplólekérdezés eredményei megfelelnek a megadott feltételeknek.
- Tevékenységnapló – A tevékenységnapló eseménye megfelel a megadott feltételeknek.
- Webes teszt – A rendelkezésre állási teszt eredményei megfelelnek a meghatározott feltételeknek.


A [Microsoft Azure riasztásainak áttekintése](platform/alerts-overview.md)című témakörben olvashat.


### <a name="what-is-an-action-group"></a>Mi az a műveletcsoport?
A műveletcsoport olyan értesítések és műveletek gyűjteménye, amelyeket egy riasztás aktiválhat. Több riasztás egyetlen műveletcsoportot is használhat, amely lehetővé teszi az értesítések és műveletek gyakori készleteinek kihasználását. Lásd: [Műveletcsoportok létrehozása és kezelése az Azure Portalon.](platform/action-groups.md)


### <a name="what-is-an-action-rule"></a>Mi az a műveletszabály?
A műveletszabály lehetővé teszi egy bizonyos feltételeknek megfelelő riasztáskészlet viselkedésének módosítását. Ez lehetővé teszi, hogy olyan követelményeket hajtson végre, mint a riasztási műveletek letiltása a karbantartási időszakban. Egy műveletcsoportot is alkalmazhat egy riasztáskészletre ahelyett, hogy közvetlenül a riasztási szabályokra alkalmazna. Lásd: [Műveletszabályok](platform/alerts-action-rules.md).

## <a name="agents"></a>Ügynökök

### <a name="does-azure-monitor-require-an-agent"></a>Az Azure Monitornak szüksége van ügynökre?
Az ügynök csak az operációs rendszerből és a virtuális gépek ben lévő számítási feladatokból szükséges adatok gyűjtéséhez szükséges. A virtuális gépek az Azure-ban, egy másik felhőkörnyezetben vagy a helyszínen is elhelyezhetők. Tekintse [meg az Azure Monitor-ügynökök áttekintése című témakört.](platform/agents-overview.md)


### <a name="whats-the-difference-between-the-azure-monitor-agents"></a>Mi a különbség az Azure Monitor-ügynökök között?
Az Azure Diagnostic bővítmény az Azure virtuális gépekhez, és adatokat gyűjt az Azure Monitor Metrics, az Azure Storage és az Azure Event Hubs. A Log Analytics-ügynök az Azure-ban, egy másik felhőbeli környezetben vagy a helyszínen lévő virtuális gépekhez készült, és adatokat gyűjt az Azure Monitor Naplók ba. A függőségi ügynök megköveteli a Log Analytics-ügynök és az összegyűjtött folyamat részleteit és függőségek. Tekintse [meg az Azure Monitor-ügynökök áttekintése című témakört.](platform/agents-overview.md)


### <a name="does-my-agent-traffic-use-my-expressroute-connection"></a>Az ügynökforgalom az ExpressRoute-kapcsolatomat használja?
Az Azure Monitornak irányuló forgalom a Microsoft peering ExpressRoute-áramkört használja. Az [ExpressRoute-forgalom](../expressroute/expressroute-faqs.md#supported-services) különböző típusainak leírását az ExpressRoute dokumentációjában találja. 

### <a name="how-can-i-confirm-that-the-log-analytics-agent-is-able-to-communicate-with-azure-monitor"></a>Hogyan győződhetek meg arról, hogy a Log Analytics-ügynök képes-e kommunikálni az Azure Monitorral?
Az ügynökszámítógép Vezérlőpultján válassza a **Biztonsági & beállítások**lehetőséget , a Microsoft Monitoring **Agent** lehetőséget. Az **Azure Log Analytics (OMS)** lapon egy zöld pipa ikon megerősíti, hogy az ügynök képes kommunikálni az Azure Monitor. A sárga figyelmeztető ikon azt jelenti, hogy az ügynöknek problémái vannak. Ennek egyik gyakori oka, hogy a **Microsoft Monitoring Agent** szolgáltatás leállt. A szolgáltatás vezérlőjének használatával indítsa újra a szolgáltatást.

### <a name="how-do-i-stop-the-log-analytics-agent-from-communicating-with-azure-monitor"></a>Hogyan akadályozhatom meg, hogy a Log Analytics-ügynök kommunikáljon az Azure Monitorral?
A Közvetlenül a Log Analytics szolgáltatáshoz kapcsolódó ügynökök esetén nyissa meg a Vezérlőpultot, és válassza a **Biztonsági & beállítások**, a Microsoft Monitoring **Agent**lehetőséget. Az **Azure Log Analytics (OMS)** lapon távolítsa el az összes felsorolt munkaterületek. A System Center Operations Manager alkalmazásban távolítsa el a számítógépet a Log Analytics által felügyelt számítógépek listájáról. Az Operations Manager frissíti az ügynök konfigurációját, hogy a továbbiakban ne jelentsen a Log Analytics-nek. 

### <a name="how-much-data-is-sent-per-agent"></a>Mennyi adat küldése ügynökenként?
Az ügynökenként küldött adatok mennyisége a következőktől függ:

* Az Ön által engedélyezett megoldások
* Az összegyűjtött naplók és teljesítményszámlálók száma
* A naplókban lévő adatok mennyisége

A részletekért tekintse [meg a Használat és a költségek kezelése az Azure Monitor naplókkal.](platform/manage-cost-storage.md)

A WireData-ügynök futtatására képes számítógépek esetében a következő lekérdezéssel megtekintheti, hogy mennyi adatot küld a rendszer:

```Kusto
WireData
| where ProcessName == "C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe" 
| where Direction == "Outbound" 
| summarize sum(TotalBytes) by Computer 
```

### <a name="how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-azure-monitor"></a>Mekkora hálózati sávszélességet használ a Microsoft Management Agent (MMA) az Azure Monitornak történő adatküldéskor?
A sávszélesség az elküldött adatok mennyiségének függvénye. Az adatok tömörítése a hálózaton keresztül történő küldés során történik.


### <a name="how-can-i-be-notified-when-data-collection-from-the-log-analytics-agent-stops"></a>Hogyan kaphatok értesítést, ha a Log Analytics-ügynöktől származó adatgyűjtés leáll?

Használja az [új naplóriasztás létrehozása](platform/alerts-metric.md) című részben leírt lépéseket, amelyekértesítést kapnak az adatgyűjtés leállításakor. A riasztási szabályhoz a következő beállításokat használja:

- **Riasztási feltétel megadása:** Adja meg a Log Analytics-munkaterületet erőforrás-célként.
- **Riasztási feltételek** 
   - **Jel neve**: *Egyéni naplókeresés*
   - **Keresési lekérdezés**:`Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **Riasztási logika**: *Az eredmények száma* **alapján** , **A feltétel** *nagyobb, mint*, **Küszöbérték** *0*
   - **Értékelés:** **Időszak (percben)** *30*, **Gyakoriság (percben)** *10*
- **Riasztás részleteinek megadása** 
   - **Név**: *Az adatgyűjtés leállt*
   - **Súlyosság**: *Figyelmeztetés*

Adjon meg egy meglévő vagy új [műveletcsoportot,](platform/action-groups.md) hogy amikor a naplóriasztás feltételeknek felel meg, értesítést kapjon, ha 15 percnél tovább hiányzik egy szívverés.


### <a name="what-are-the-firewall-requirements-for-azure-monitor-agents"></a>Milyen tűzfalkövetelmények vonatkoznak az Azure Monitor-ügynökökre?
A [tűzfalkövetelményekkel](platform/log-analytics-agent.md#network-firewall-requirements)kapcsolatos részleteket a Hálózati tűzfal követelményeiben találja.


## <a name="visualizations"></a>Vizualizációk

### <a name="why-cant-i-see-view-designer"></a>Miért nem látom a Tervező megtekintése?

A View Designer csak a Közreműködői engedélyekkel vagy magasabb szintű felhasználók számára érhető el a Log Analytics-munkaterületen.

## <a name="application-insights"></a>Application Insights

### <a name="configuration-problems"></a>Konfigurációs problémák
*Gondjaim vannak a következők beállításával:*

* [.NET-alkalmazás](app/asp-net-troubleshoot-no-data.md)
* [Már futó alkalmazás figyelése](app/monitor-performance-live-website-now.md#troubleshoot)
* [Azure-diagnosztika](platform/diagnostics-extension-to-application-insights.md)
* [Java webalkalmazások](app/java-troubleshoot.md)

*Nem kapok adatokat a szerveremről*

* [Tűzfal-kivételek beállítása](app/ip-addresses.md)
* [ASP.NET kiszolgáló beállítása](app/monitor-performance-live-website-now.md)
* [Java-kiszolgáló beállítása](app/java-agent.md)

### <a name="can-i-use-application-insights-with-"></a>Használhatom az Application Insightsot a ...?

* [Webalkalmazások egy IIS-kiszolgálón az Azure Virtuálisgép- vagy az Azure virtuálisgép-méretezési csoportban](app/azure-vm-vmss-apps.md)
* [Webalkalmazások Az IIS-kiszolgálón – helyszíni vagy virtuális gép](app/asp-net.md)
* [Java-webalkalmazások](app/java-get-started.md)
* [Node.js-alkalmazások](app/nodejs.md)
* [Webalkalmazások az Azure-ban](app/azure-web-apps.md)
* [Felhőszolgáltatások az Azure-ban](app/cloudservices.md)
* [A Dockerben futó alkalmazáskiszolgálók](app/docker.md)
* [Egyoldalas webalkalmazások](app/javascript.md)
* [Sharepoint](app/sharepoint.md)
* [Asztali Windows-alkalmazás](app/windows-desktop.md)
* [Más platformok](app/platforms.md)

### <a name="is-it-free"></a>Ingyenes?

Igen, kísérleti használatra. Az alapdíjcsomagban a jelentkezése minden hónapban ingyenesen küldhet bizonyos adatkeretet. Az ingyenes juttatás elég nagy ahhoz, hogy fedezze a fejlesztést, és egy alkalmazást közzétesz néhány felhasználó számára. Beállíthatja a korlátot, hogy megakadályozza a megadott adatmennyiségnél nagyobb mennyiségű adat feldolgozását.

A gb nagyobb mennyiségű telemetriai adatokat számít fel. Adunk néhány tippet, hogyan lehet [korlátozni a díjakat](app/pricing.md).

Az Enterprise terv díjat számít fel minden egyes nap, amikor minden webkiszolgáló-csomópont telemetriai adatokat küld. Ez akkor alkalmas, ha azt szeretné, hogy a folyamatos export nagy léptékben.

[Olvassa el az árképzési tervet](https://azure.microsoft.com/pricing/details/application-insights/).

### <a name="how-much-does-it-cost"></a>Mennyibe kerül?

* Nyissa **meg** a Használati és becsült költségek lapot egy Application Insights-erőforrásban. Van egy diagram a legutóbbi használatról. Ha szeretné, beállíthat adatkötet-korlátot.
* Nyissa meg az [Azure Billing panelt,](https://portal.azure.com/#blade/Microsoft_Azure_Billing/BillingBlade/Overview) hogy a számlákat az összes erőforrásközött láthassa.

### <a name="what-does-application-insights-modify-in-my-project"></a><a name="q14"></a>Mit módosít az Application Insights a projektben?
A részletek a projekt típusától függenek. Webes alkalmazás esetén:

* Hozzáadja ezeket a fájlokat a projekthez:
  * ApplicationInsights.config
  * ai.js
* Telepíti a következő NuGet csomagokat:
  * *Application Insights API* – az alapvető API
  * *Application Insights API webes alkalmazásokhoz* – telemetriai adatok küldésére szolgál a kiszolgálóról
  * *Application Insights API JavaScript-alkalmazásokhoz* – telemetriai adatok küldésére szolgál az ügyféltől
* A csomagok a következő szerelvényeket tartalmazzák:
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* Elemek beszúrása a következő be:
  * Web.config
  * csomagok.config
* (Csak új projektek – ha [egy meglévő projekthez adja hozzá az Application Insights ot,][start]ezt manuálisan kell elvégeznie.) Kódrészleteket szúr be az ügyfél- és kiszolgálókódba, hogy inicializálja őket az Application Insights erőforrásazonosítóval. Egy MVC alkalmazásban például a program kódot illeszt be a\_mesterlap nézeteibe/megosztott/Layout.cshtml

### <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Hogyan frissíthetek a régebbi SDK-verziókról?
Tekintse meg az SDK-hoz tartozó, az alkalmazástípusnak megfelelő [kibocsátási megjegyzéseket.](app/release-notes.md)

### <a name="how-can-i-change-which-azure-resource-my-project-sends-data-to"></a><a name="update"></a>Hogyan módosíthatom, hogy a projektem melyik Azure-erőforrásnak küld adatokat?
A Megoldáskezelőben kattintson a jobb gombbal, `ApplicationInsights.config` és válassza az Application Insights frissítése **parancsot.** Az adatokat elküldheti egy meglévő vagy új azure-beli erőforrásnak. A frissítő varázsló módosítja a instrumentation kulcsot az ApplicationInsights.config fájlban, amely meghatározza, hogy a kiszolgáló SDK-ja hol küldi az adatokat. Ha nem szünteti meg az "Összes frissítése" jelölőnégyzet jelölését, akkor az is módosítja a kulcsot, ahol megjelenik a weblapokon.

### <a name="what-is-status-monitor"></a>Mi az Állapotfigyelő?

Asztali alkalmazás, amelyet az IIS-webkiszolgálón használhat az Application Insights webalkalmazásokban való konfigurálásához. Nem gyűjttetési telemetriai adatokat: leállíthatja, ha nem konfigurálegy alkalmazást. 

[További információ](app/monitor-performance-live-website-now.md#questions).

### <a name="what-telemetry-is-collected-by-application-insights"></a>Milyen telemetriai adatokat gyűjt az Application Insights?

Kiszolgálói webalkalmazásokból:

* HTTP-kérések
* [Függőségek](app/asp-net-dependencies.md). Hívások: SQL adatbázisok; HTTP-hívások külső szolgáltatásokhoz; Azure Cosmos DB, tábla, blob storage és a várólista. 
* [Kivételek](app/asp-net-exceptions.md) és veremnyomkövetések.
* [Teljesítményszámlálók](app/performance-counters.md) – Ha [állapotfigyelőt,](app/monitor-performance-live-website-now.md) [Az App Services Azure figyelését,](app/azure-web-apps.md)a virtuális gép vagy a virtuális gép [méretezésének azure-figyelését](app/azure-vm-vmss-apps.md)vagy az [Application Insights összegyűjtött íróját](app/java-collectd.md)használja.
* [Egyéni események és a kódolandó mutatók.](app/api-custom-events-metrics.md)
* [Nyomkövetési naplók,](app/asp-net-trace-logs.md) ha konfigurálja a megfelelő gyűjtőt.

[Ügyfélweblapokról:](app/javascript.md)

* [Oldalmegtekintések száma](app/usage-overview.md)
* [AJAX hívások](app/asp-net-dependencies.md) Futó parancsfájlból érkező kérelmek.
* Oldalnézet betöltési adatai
* A felhasználók és a munkamenetek száma
* [Hitelesített felhasználói azonosítók](app/api-custom-events-metrics.md#authenticated-users)

Más forrásokból, ha konfigurálja őket:

* [Azure-diagnosztika](platform/diagnostics-extension-to-application-insights.md)
* [Importálás az Analytics szolgáltatásba](platform/data-collector-api.md)
* [Log Analytics](platform/data-collector-api.md)
* [LogStash](platform/data-collector-api.md)

### <a name="can-i-filter-out-or-modify-some-telemetry"></a>Kiszűrhetem vagy módosíthatom a telemetriai adatokat?

Igen, a szerveren lehet írni:

* Telemetriai processzor szűrése vagy hozzáadása tulajdonságok a kiválasztott telemetriai elemeket, mielőtt azok az alkalmazásból való elküldésük előtt.
* Telemetriai inicializáló a telemetriai elemek tulajdonságainak hozzáadásához.

További információ a [ASP.NET](app/api-filtering-sampling.md) vagy a [Java.](app/java-filter-telemetry.md)

### <a name="how-are-city-countryregion-and-other-geo-location-data-calculated"></a>Hogyan történik a város, az ország/régió és az egyéb földrajzi helyadatok kiszámítása?

A [GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/)használatával keressük meg a webes ügyfél IP-címét (IPv4 vagy IPv6).

* Böngésző telemetriai adatok: Gyűjtjük a feladó IP-címét.
* Kiszolgálótelemetria: Az Application Insights modul gyűjti az ügyfél IP-címét. A bevan `X-Forwarded-For` állítva, nem kerül összegyűjtésre.
* Ha többet szeretne megtudni arról, hogyan gyűjtik az IP-cím- és helymeghatározási adatokat az Application Insightsban, olvassa el ezt a [cikket.](https://docs.microsoft.com/azure/azure-monitor/app/ip-collection)


Beállíthatja, `ClientIpHeaderTelemetryInitializer` hogy az IP-címet egy másik fejlécből vegye át. Egyes rendszerekben például egy proxy, terheléselosztó vagy CDN mozgatja a rendszerbe. `X-Originating-IP` [További információ](https://apmtips.com/blog/2016/07/05/client-ip-address/).

A [Power BI segítségével](app/export-power-bi.md ) megjelenítheti a kérelem telemetriáját egy térképen.


### <a name="how-long-is-data-retained-in-the-portal-is-it-secure"></a><a name="data"></a>Mennyi ideig őrzi meg az adatokat a portálon? Biztonságos?
Tekintse meg az [adatmegőrzést és az adatvédelmet.][data]

### <a name="what-happens-to-application-insights-telemetry-when-a-server-or-device-loses-connection-with-azure"></a>Mi történik az Application Insight telemetriai adataival, ha egy kiszolgáló vagy eszköz megszakad az Azure-ral?

Minden SDK-nk, beleértve a webes SDK-t is, magában foglalja a "megbízható szállítást" vagy a "robusztus szállítást". Amikor a kiszolgáló vagy az eszköz megszakad az Azure-ral való kapcsolata, a telemetriai adatok [helyileg tárolódnak a fájlrendszerben](https://docs.microsoft.com/azure/azure-monitor/app/data-retention-privacy#does-the-sdk-create-temporary-local-storage) (Server SDK) vagy a HTML5 Session Storage (Web SDK) rendszerben. Az SDK rendszeres időközönként újra elküldi ezt a telemetriai adatokat, amíg a betöltési szolgáltatás úgy véli, hogy "elavult" (48 óra naplók, 30 perc metrikák). Az elavult telemetriai adatok el leszek dobva. Bizonyos esetekben, például ha a helyi tároló megtelt, az újrapróbálkozás nem történik meg.


### <a name="could-personal-data-be-sent-in-the-telemetry"></a>Lehet személyes adatokat küldeni a telemetriai adatokat?

Ez akkor lehetséges, ha a kód ilyen adatokat küld. Ez akkor is előfordulhat, ha a veremnyomkövetésváltozói személyes adatokat tartalmaznak. A fejlesztőcsapatnak kockázatértékelést kell végeznie a személyes adatok megfelelő kezelése érdekében. [További információ az adatmegőrzésről és az adatvédelemről.](app/data-retention-privacy.md)

**Az** ügyfél webcímének minden oktettje mindig 0-ra van állítva a földrajzi helyattribútumok felkeresése után.

### <a name="my-instrumentation-key-is-visible-in-my-web-page-source"></a>Saját Instrumentation kulcs látható az én weboldal forrása. 

* Ez bevett gyakorlat a felügyeleti megoldások ban.
* Nem használható az adatok ellopására.
* Ezt fel lehet használni az adatok elferdítésére vagy riasztások indítására.
* Nem hallottuk, hogy bármely ügyfélnek ilyen problémái voltak.

Ön ilyenkor az alábbiakat teheti:

* Két külön Instrumentation kulcsok (külön Application Insights-erőforrások) az ügyfél- és kiszolgálóadatok. Vagy
* Írjon egy proxyt, amely fut a kiszolgálón, és a webes ügyfél adatokat küldeni, hogy a proxy.

### <a name="how-do-i-see-post-data-in-diagnostic-search"></a><a name="post"></a>Hogyan láthatom a POST-adatokat a diagnosztikai keresésben?
Nem naplózzuk automatikusan a POST-adatokat, de használhatja a TrackTrace-hívást: tegye az adatokat az üzenet paraméterbe. Ez hosszabb méretkorláttal rendelkezik, mint a karakterlánc-tulajdonságokra vonatkozó korlátok, bár nem szűrhető rajta.

### <a name="should-i-use-single-or-multiple-application-insights-resources"></a>Érdemes egy vagy több Application Insights-erőforrást használni?

Egyetlen erőforrás használata egyetlen üzleti rendszer összes összetevőjéhez vagy szerepköréhez. Külön erőforrásokat használhat a fejlesztési, tesztelési és kiadási verziókhoz, valamint a független alkalmazásokhoz.

* [Lásd a vitát itt](app/separate-resources.md)
* [Példa – felhőszolgáltatás feldolgozói és webes szerepkörökkel](app/cloudservices.md)

### <a name="how-do-i-dynamically-change-the-instrumentation-key"></a>Hogyan tudom dinamikusan megváltoztatni a műszerezési kulcsot?

* [Vita itt](app/separate-resources.md)
* [Példa – felhőszolgáltatás feldolgozói és webes szerepkörökkel](app/cloudservices.md)

### <a name="what-are-the-user-and-session-counts"></a>Mik a felhasználó és a munkamenet számít?

* A JavaScript SDK beállít egy felhasználói cookie-t a webes ügyfélen, hogy azonosítsa a visszatérő felhasználókat, és egy munkamenet-cookie-t a tevékenységek csoportosításához.
* Ha nincs ügyféloldali parancsfájl, [a kiszolgálón is beállíthat cookie-kat.](https://apmtips.com/blog/2016/07/09/tracking-users-in-api-apps/)
* Ha egy valódi felhasználó különböző böngészőkben használja a webhelyet, vagy privát/inkognitóböngészést használ, vagy különböző gépeket használ, akkor többször is megszámolja őket.
* Ha azonosítani szeretné a bejelentkezett felhasználót a gépek és a böngészők között, adjon hozzá egy hívást a [SetAuthenticatedUserContext()](app/api-custom-events-metrics.md#authenticated-users)beállításhoz.

### <a name="have-i-enabled-everything-in-application-insights"></a><a name="q17"></a>Engedélyeztem mindent az Application Insightsban?
| Mit kell látnia | Hogyan juthat el | Miért akarod? |
| --- | --- | --- |
| Rendelkezésre állási diagramok |[Webes tesztek](app/monitor-web-app-availability.md) |Tudja, hogy a webalkalmazás fel |
| Szerver app perf: válaszidő, ... |[Alkalmazáselemzési adatok hozzáadása a projekthez](app/asp-net.md) vagy [AI-állapotfigyelő telepítése a kiszolgálón](app/monitor-performance-live-website-now.md) (vagy saját kód írása a [függőségek nyomon követéséhez)](app/api-custom-events-metrics.md#trackdependency) |Perf problémák észlelése |
| Függőségi telemetria |[AI-állapotfigyelő telepítése a kiszolgálóra](app/monitor-performance-live-website-now.md) |Adatbázisokkal vagy más külső összetevőkkel kapcsolatos problémák diagnosztizálása |
| Veremnyomkövetések leképezése kivételekből |[TrackException hívások beszúrása a kódba](app/asp-net-exceptions.md) (de néhány automatikusan jelentkezik) |Kivételek észlelése és diagnosztizálása |
| Keresési napló nyomkövetései |[Naplózási adapter hozzáadása](app/asp-net-trace-logs.md) |Kivételek diagnosztizálása, perverz problémák |
| Az ügyfélhasználat alapjai: oldalmegtekintések, munkamenetek, ... |[JavaScript inicializáló a weboldalakon](app/javascript.md) |Használatelemzés |
| Ügyfél egyéni mutatói |[Hívások nyomon követése a weboldalakon](app/api-custom-events-metrics.md) |A felhasználói élmény fokozása |
| Kiszolgáló egyéni mutatói |[Hívások nyomon követése a kiszolgálón](app/api-custom-events-metrics.md) |Üzleti intelligencia |

### <a name="why-are-the-counts-in-search-and-metrics-charts-unequal"></a>Miért egyenlőtlenek a keresések és a mérőszámok diagramjainak számai?

[Mintavételi](app/sampling.md) csökkenti a telemetriai elemek (kérések, egyéni események stb.) ténylegesen küldött az alkalmazásból a portálra. A Keresés ben láthatja a ténylegesen beérkezett elemek számát. Az események számát megjelenítő metrikadiagramokban láthatja az eredeti események számát. 

Minden egyes továbbított elem egy `itemCount` tulajdonságot hordoz, amely megmutatja, hogy az elem hány eredeti eseményt jelent. A működés közbeni mintavételezések megfigyeléséhez futtathatja ezt a lekérdezést az Analytics szolgáltatásban:

```
    requests | summarize original_events = sum(itemCount), transmitted_events = count()
```


### <a name="automation"></a>Automation

#### <a name="configuring-application-insights"></a>Az Application Insights konfigurálása

[PowerShell-parancsfájlokat az](app/powershell.md) Azure Resource Monitor használatával a következőkre írhat:

* Hozzon létre és frissítse az Application Insights-erőforrásokat.
* Állítsa be az árképzési tervet.
* Szerezd meg a műszerkulcsot.
* Adjon hozzá egy metrikariasztást.
* Adjon hozzá egy rendelkezésre állási tesztet.

Nem állíthat be metrikakezelő jelentést, és nem állíthat be folyamatos exportálást.

#### <a name="querying-the-telemetry"></a>A telemetria lekérdezése

A [REST API segítségével](https://dev.applicationinsights.io/) [analytics-lekérdezéseket](app/analytics.md) futtat.

### <a name="how-can-i-set-an-alert-on-an-event"></a>Hogyan állíthatok be riasztást egy eseményről?

Az Azure-riasztások csak a metrikákon. Hozzon létre egy egyéni metrikát, amely átlépi az értékküszöböt, amikor az esemény bekövetkezik. Ezután állítson be egy riasztást a metrika. Értesítést kap, ha a metrika mindkét irányban átlépi a küszöbértéket; nem kap értesítést, amíg az első átkelés, nem számít, hogy a kezdeti érték magas vagy alacsony; mindig van egy késés néhány perc.

### <a name="are-there-data-transfer-charges-between-an-azure-web-app-and-application-insights"></a>Vannak-e adatátviteli díjak egy Azure-webalkalmazás és az Application Insights között?

* Ha az Azure-webapp üzemelteti egy adatközpontban, ahol van egy Application Insights-gyűjtemény végpont, nincs díj. 
* Ha nincs gyűjteményvégpont a gazdagép adatközpontjában, akkor az alkalmazás telemetriai adatai [azure-beli kimenő díjakat](https://azure.microsoft.com/pricing/details/bandwidth/)vonnak maga után.

Ez nem függ attól, hogy az Application Insights-erőforrás hol található. Ez csak a végpontok eloszlásáttól függ.

### <a name="can-i-send-telemetry-to-the-application-insights-portal"></a>Küldhetek telemetriai adatokat az Application Insights-portálra?

Javasoljuk, hogy használja az SDK-kat és az [SDK API-t.](app/api-custom-events-metrics.md) Vannak változatai az SDK különböző [platformokon](app/platforms.md). Ezek az SDK-k pufferelést, tömörítést, szabályozást, újrapróbálkozásokat és így tovább kezelik. A [betöltési séma](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/develop/Schema/PublicSchema) és a [végpontprotokoll](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/ENDPOINT-PROTOCOL.md) azonban nyilvános.

### <a name="can-i-monitor-an-intranet-web-server"></a>Figyelhetek intranetes webkiszolgálót?

Igen, de engedélyeznie kell a szolgáltatásokba irányuló forgalmat tűzfalkivételekkel vagy proxyátirányításokkal.
- QuickPulse (Gyorspulzus)`https://rt.services.visualstudio.com:443` 
- ApplicationIdProvider alkalmazásszolgáltató`https://dc.services.visualstudio.com:443` 
- TelemettryChannel`https://dc.services.visualstudio.com:443` 


Tekintse át a szolgáltatások és IP-címek teljes listáját [itt.](app/ip-addresses.md)

#### <a name="firewall-exception"></a>Tűzfal kivétel

Engedélyezze a webkiszolgáló nak, hogy telemetriát küldjön a végpontjainknak. 

#### <a name="gateway-redirect"></a>Átjáró átirányítása

Irányítsa át a kiszolgálóról az intraneten lévő átjáróra irányuló forgalmat a végpontok felülírásával a konfigurációban. Ha ezek az "Endpoint" tulajdonságok nem találhatók meg a konfigurációban, ezek az osztályok az ApplicationInsights.config példában alább látható alapértelmezett értékeket fogják használni. 

Az átjáró nak a végpont alapcímére kell irányítania a forgalmat. A konfigurációban cserélje le `http://<your.gateway.address>/<relative path>`az alapértelmezett értékeket a programra.


##### <a name="example-applicationinsightsconfig-with-default-endpoints"></a>Példa Az ApplicationInsights.config alapértelmezett végpontokkal:
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
> Az ApplicationIdProvider a 2.6.0-s verziótól érhető el.



#### <a name="proxy-passthrough"></a>Proxy áthaladás

A proxy-áthaladás egy számítógépszintű vagy alkalmazásszintű proxy konfigurálásával érhető el.
További információt a dotnet DefaultProxy című cikkében [talál.](https://docs.microsoft.com/dotnet/framework/configure-apps/file-schema/network/defaultproxy-element-network-settings)
 
 Példa Web.config:
 ```xml
<system.net>
    <defaultProxy>
      <proxy proxyaddress="http://xx.xx.xx.xx:yyyy" bypassonlocal="true"/>
    </defaultProxy>
</system.net>
```
 

### <a name="can-i-run-availability-web-tests-on-an-intranet-server"></a>Futtatható a rendelkezésre állási webtesztek intranetes kiszolgálón?

Webes [tesztjeink](app/monitor-web-app-availability.md) olyan jelenléti pontokon futnak, amelyeket világszerte terjesztenek. Két megoldás létezik:

* Tűzfal ajtó - A kérelmek engedélyezése a kiszolgálóra [a webes tesztügynökök hosszú és módosítható listájáról.](app/ip-addresses.md)
* Saját kódot írhat, hogy rendszeres kéréseket küldjön a kiszolgálónak az intraneten belülről. Erre a célra futtathatja a Visual Studio webes tesztjeit. A tesztelő elküldheti az eredményeket az Application Insights nak a TrackAvailability() API használatával.

### <a name="how-long-does-it-take-for-telemetry-to-be-collected"></a>Mennyi ideig tart a telemetriai adatok gyűjtése?

A legtöbb Application Insights-adat késése 5 perc alatt. Egyes adatok hosszabb időt vehetnek igénybe; általában nagyobb naplófájlok. További információt az [Application Insights SLA](https://azure.microsoft.com/support/legal/sla/application-insights/v1_2/)című témakörben talál.



<!--Link references-->

[data]: app/data-retention-privacy.md
[platforms]: app/platforms.md
[start]: app/app-insights-overview.md
[windows]: app/app-insights-windows-get-started.md


## <a name="azure-monitor-for-containers"></a>Azure Monitor tárolókhoz

Ez a Microsoft GYIK a tárolók Azure Monitorjával kapcsolatos gyakori kérdések listája. Ha bármilyen további kérdése van a megoldással kapcsolatban, látogasson el a [vitafórumra,](https://feedback.azure.com/forums/34192--general-feedback) és tegye fel kérdéseit. Amikor egy kérdést gyakran felteszünk, hozzáadjuk ehhez a cikkhez, hogy gyorsan és egyszerűen megtalálható legyen.

### <a name="what-does-other-processes-represent-under-the-node-view"></a>Mit *jelentenek az Egyéb folyamatok* a Csomópont nézetben?

**Más folyamatok** célja, hogy segítsen egyértelműen megérteni a csomóponton a magas erőforrás-használat kiváltó okait. Ez lehetővé teszi a konténeres folyamatok és a nem konténeres folyamatok közötti használat megkülönböztetését.

Mik ezek **az egyéb folyamatok?** 

Ezek nem tárolóba helyezett folyamatok, amelyek a csomóponton futnak.  

Hogy számoljuk ezt ki?

**Egyéb folyamatok** = *Teljes használat a CAdvisor-használatból* - *konténeres folyamatból*

Az **egyéb folyamatok a következőket tartalmazzák:**

- Saját irányítású vagy felügyelt Kubernetes nem konténeres folyamatok 

- Tárolófutási idő folyamatok  

- Kubelet  

- A csomóponton futó rendszerfolyamatok 

- A csomóponthardveren vagy virtuális gépen futó egyéb, nem Kubernetes-számítási feladatok 

### <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>A ContainerLog tábla lekérdezésekénnél nem jelennek meg a Kép és a Név tulajdonságértékek.

Az ügynök verziójában ciprod12042019 és újabb, alapértelmezés szerint ez a két tulajdonság nem töltődik fel minden naplósorban, hogy minimalizálja a naplóadatok kal kapcsolatos költségeket. Két lehetőség van a tábla lekérdezésére, amelyek ezeket a tulajdonságokat tartalmazzák az értékeikkel:

#### <a name="option-1"></a>1. lehetőség 

Csatlakozzon más táblákhoz, hogy ezeket a tulajdonságértékeket felvesse az eredményekbe.

Módosítsa a lekérdezéseket úgy, hogy ```ContainerInventory``` azok tartalmazzák a táblából származó Image és ImageTag tulajdonságokat a ContainerID tulajdonsághoz való csatlakozással. A KubepodInventory tábla ContaineName ```ContainerLog``` mezőjéből a Name tulajdonságot (ahogy az korábban megjelent a táblában) a ContainerID tulajdonsághoz való csatlakozással is felveheti. Ez az ajánlott beállítás.

A következő példa egy részletes mintalekérdezés, amely bemutatja, hogyan lehet ezeket a mezőértékeket illesztésekkel beszerezni.

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

#### <a name="option-2"></a>2. lehetőség

Engedélyezze újra a gyűjteményt ezekhez a tulajdonságokhoz minden tárolónapló-sorhoz.

Ha az első beállítás nem megfelelő a lekérdezési módosítások miatt, újra engedélyezheti a ```log_collection_settings.enrich_container_logs``` mezők gyűjtését, ha engedélyezi a beállítást az ügynök konfigurációs leképezésében az [adatgyűjtés konfigurációs beállításaiban](insights/container-insights-agent-config.md)leírtak szerint.

> [!NOTE]
> A második beállítás nem ajánlott az 50-nél több csomópontot tartalmazó nagy fürtöknél, mivel a dúsítás végrehajtásához api-kiszolgálóhívásokat hoz létre a fürt minden csomópontjáról. Ez a beállítás minden összegyűjtött naplósor adatméretét is növeli.

### <a name="can-i-view-metrics-collected-in-grafana"></a>Megtekinthetem a Grafana-ban összegyűjtött mutatókat?

Az Azure Monitor tárolók támogatja a Grafana irányítópultokon a Log Analytics-munkaterületen tárolt metrikák megtekintését. A kezdéshez egy sablont is betölthet a Grafana [irányítópult-tárházáról,](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker) és hivatkozást nyújtunk be, amely segítséget nyújt a figyelt fürtök további adatainak lekérdezéséhez az egyéni Grafana-irányítópultokon való megjelenítéshez. 

### <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>Figyelhetem az AKS-motorfürtömet az Azure Monitor tárolókkal?

Az Azure Monitor a tárolók támogatja a figyeléstároló-számítási feladatok üzembe helyezett AKS-motor (korábbi nevén ACS-motor) fürt(ek) az Azure-ban üzemeltetett. További részletekért és a forgatókönyv figyelésének engedélyezéséhez szükséges lépések áttekintéséről az [Azure Monitor használata az AKS-engine tárolókhoz című témakörben](https://github.com/microsoft/OMS-docker/tree/aks-engine)olvashat.

### <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Miért nem látom az adatokat a Log Analytics-munkaterületemen?

Ha nem látja az adatokat a Log Analytics munkaterületen egy bizonyos időpontban mindennapi, lehet, hogy elérte az alapértelmezett 500 MB-os korlátot, vagy a napi korlátot, hogy szabályozza a napi adatok gyűjtésére. Ha a napkorra vonatkozó korlát teljesül, az adatgyűjtés csak a következő napon áll le és folytatódik. Az adathasználat és a várt használati minták alapján egy másik tarifacsomagra való frissítésmegtekintéséről az [Adathasználat és a költségek naplózása](platform/manage-cost-storage.md)című témakörben található. 

### <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>Mik a container-állapotok a ContainerInventory táblában vannak megadva?

A ContainerInventory tábla a leállított és a futó tárolókra vonatkozó információkat tartalmazza. A táblát az ügynökön belüli munkafolyamat tölti fel, amely lekérdezi a docker-t az összes tárolóra (futás és leállítva), és továbbítja az adatokat a Log Analytics-munkaterületről.
 
### <a name="how-do-i-resolve-missing-subscription-registration-error"></a>Hogyan oldhatom meg *a hiányzó előfizetés-regisztrációs* hibát?

Ha a **Microsoft.OperationsManagement számára hiányzó előfizetés-regisztráció**hibaüzenetjelenik meg, a **microsoft.OperationsManagement** erőforrás-szolgáltató regisztrálásával oldhatja meg azt abban az előfizetésben, amelyen a munkaterület definiálva van. Ennek dokumentációja [itt](../azure-resource-manager/templates/error-register-resource-provider.md)található.

### <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>Támogatja az RBAC-kompatibilis AKS-fürtöket?

A tárolófigyelési megoldás nem támogatja az RBAC-ot, de az Azure Monitor tárolókhoz támogatja. Előfordulhat, hogy a megoldás részleteit tartalmazó lap nem jeleníti meg a megfelelő információkat a panelekben, amelyek a fürtök adatait jelenítik meg.

### <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Hogyan engedélyezhetem a naplógyűjtést a kube-rendszer névterében lévő tárolókhoz a Helmen keresztül?

A kube-system névtértárolóiból származó naplógyűjtemény alapértelmezés szerint le van tiltva. A naplógyűjtés az omsagent környezeti változójának beállításával engedélyezhető. További információkért tekintse meg az [Azure-figyelő a github-tárolók](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) lapján. 

### <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>Hogyan frissíthetem az omsagentet a legújabb verzióra?

Az ügynök frissítésének módjáról az [Ügynökkezelés](insights/container-insights-manage-agent.md)című témakörben olvashat.

### <a name="how-do-i-enable-multi-line-logging"></a>Hogyan engedélyezhetem a többsoros naplózást?

Jelenleg az Azure Monitor tárolók nem támogatja a többsoros naplózást, de vannak olyan megoldások, amelyek rendelkezésre állnak. Beállíthatja az összes szolgáltatást, hogy JSON formátumban írjon, majd a Docker/Moby egyetlen sorként írja őket.

A naplót például JSON-objektumként csomagolhatja, amint az alábbi példában látható egy minta node.js alkalmazásesetében:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Ezek az adatok a következő példához fognak hasonlítani az Azure Monitorban a naplóklekérdezéskor:

```
LogEntry : ({"Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

A probléma részletes áttekintését a következő [GitHub-hivatkozásból](https://github.com/moby/moby/issues/22920)tekintheti meg.

### <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>Hogyan oldhatom meg az Azure AD-hibákat, amikor engedélyezem az élő naplókat? 

A következő hiba jelenhet meg: **A kérelemben megadott válasz URL-cím nem egyezik meg\>az alkalmazáshoz beállított válasz URL-címekkel: "<alkalmazásazonosító "**. A megoldás megoldása megtalálható a [cikkben Hogyan tekintheti meg a tárolóadatokat valós időben az Azure Monitor tárolókhoz.](insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication) 

### <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>Miért nem tudom frissíteni a fürtöt a bevezetés után?

Ha miután engedélyezi az Azure Monitor tárait egy AKS-fürthöz, törli azt a Log Analytics-munkaterületet, amelynek a fürt az adatokat küldte, amikor megpróbálja frissíteni a fürtöt, sikertelen lesz. A munka kerülő megoldásához le kell tiltania a figyelést, majd újra engedélyeznie kell, hogy az előfizetésben egy másik érvényes munkaterületre hivatkozva hivatkozzon. Amikor ismét megpróbálja végrehajtani a fürtfrissítést, annak fel kell dolgoznia, és sikeresen el kell végeznie.  

### <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>Mely portokat és tartományokat kell megnyitnom/engedélyezési listát kell nyitnom az ügyintézőhöz?

Tekintse meg a [hálózati tűzfal követelményeit](insights/container-insights-onboard.md#network-firewall-requirements) a proxy- és tűzfal-konfigurációs adatokhoz, amelyek az Azure, az Azure US Government és az Azure China 21Vianet felhők használatával szükségesek a tárolóügynökhöz.

## <a name="azure-monitor-for-vms"></a>Azure Monitor virtuális gépekhez
Ez a Microsoft GYIK az Azure Monitor szolgáltatás virtuális gépekhez szolgáltatásával kapcsolatos gyakori kérdések listája. Ha bármilyen további kérdése van a megoldással kapcsolatban, látogasson el a [vitafórumra,](https://feedback.azure.com/forums/34192--general-feedback) és tegye fel kérdéseit. Amikor egy kérdést gyakran felteszünk, hozzáadjuk ehhez a cikkhez, hogy gyorsan és egyszerűen megtalálható legyen.

### <a name="can-i-onboard-to-an-existing-workspace"></a>Bevihető egy meglévő munkaterületre?
Ha a virtuális gépek már csatlakoznak egy Log Analytics-munkaterülethez, továbbra is használhatja ezt a munkaterületet, amikor az Azure Monitor virtuális gépekhez kerül, feltéve, hogy az [az itt](insights/vminsights-enable-overview.md#prerequisites)felsorolt támogatott régiók egyikében található.


### <a name="can-i-onboard-to-a-new-workspace"></a>Bevihetek egy új munkaterületre? 
Ha a virtuális gépek jelenleg nem csatlakozik egy meglévő Log Analytics-munkaterület, létre kell hoznia egy új munkaterületet az adatok tárolására. Új alapértelmezett munkaterület létrehozása automatikusan történik, ha konfigurálja az azure-beli virtuális gépek egyetlen Azure-figyelő az Azure Portalon keresztül.

Ha úgy dönt, hogy a parancsfájl-alapú metódust használja, ezeket a lépéseket az Azure PowerShell vagy a Resource Manager sabloncikk ismerteti az [Azure-figyelő szolgáltatáshoz az Azure PowerShell vagy a Resource Manager sablon](insights/vminsights-enable-at-scale-powershell.md) cikk. 

### <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>Mit tegyek, ha a virtuális gép már jelentést tesz egy meglévő munkaterületre?
Ha már gyűjt adatokat a virtuális gépekről, lehet, hogy már konfigurálta, hogy adatokat jelentsen egy meglévő Log Analytics-munkaterületnek.  Mindaddig, amíg ez a munkaterület az egyik támogatott régióban van, engedélyezheti az Azure Monitor virtuális gépek, hogy a már meglévő munkaterületet.  Ha a már használt munkaterület nem a támogatott régiók egyikében található, akkor jelenleg nem lesz képes az Azure Monitor virtuális gépekhez való be- és beszállni.  Aktívan dolgozunk a további régiók támogatásén.


### <a name="why-did-my-vm-fail-to-onboard"></a>Miért nem sikerült a virtuális gépemnek a fedélzeten dolgoznia?
Amikor egy Azure-virtuális gép az Azure Portalon, a következő lépések következnek be:

* Ha ez a beállítás be van jelölve, létrejön egy alapértelmezett Log Analytics-munkaterület.
* A Log Analytics-ügynök az Azure virtuális gépeken egy virtuális gép bővítmény használatával, ha megállapítást nyer, hogy szükség van.  
* Az Azure Monitor virtuális gépek leképezési függőségi ügynök az Azure-beli virtuális gépek en egy bővítmény használatával, ha úgy határozza meg, hogy szükség van. 

A fedélzeti folyamat során ellenőrizzük a fentiek állapotát, hogy visszaküldjük önnek az értesítési állapotot a portálon. A munkaterület és az ügynök telepítése általában 5–10 percet vesz igénybe. A figyelési adatok megtekintése a portálon további 5-10 percet vesz igénybe.  

Ha elindította a bevezetést, és olyan üzeneteket lát, amelyek jelzik, hogy a virtuális gépnek be kell szállnia, a virtuális gép a folyamat befejezéséhez legfeljebb 30 percet kell hagynia. 


### <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Nem látok néhány vagy bármilyen adatot a virtuális gép teljesítménydiagramjain
Teljesítménydiagramjaink frissültek az *InsightsMetrics* táblázatban tárolt adatok használatával.  Az adatok megtekintéséhez ezekben a diagramokban frissítenie kell az új Virtuálisgép-elemzési megoldás használatához.  További információkért olvassa el a [GA GYIK-et.](insights/vminsights-ga-release-faq.md)

Ha nem látja a teljesítményadatokat a lemeztáblában vagy néhány teljesítménydiagramon, akkor előfordulhat, hogy a teljesítményszámlálók nincsenek konfigurálva a munkaterületen. A feloldáshoz futtassa a következő [PowerShell-parancsfájlt.](insights/vminsights-enable-at-scale-powershell.md#enable-with-powershell)


### <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Miben különbözik az Azure Monitor szolgáltatás a virtuális gépek leképezési funkciójától a Szolgáltatástérképtől?
Az Azure Monitor szolgáltatás a virtuális gépek leképezése szolgáltatás alapja a Szolgáltatástérkép, de a következő különbségek:

* A Map nézet érhető el a virtuális gép panelen és az Azure Monitor virtuális gépek az Azure Monitor alatt érhető el.
* A térkép kapcsolatai most már kattinthatók, és a kapcsolatmetrikus adatok nézetét jelenítik meg a kiválasztott kapcsolat oldalsó panelén.
* Van egy új API, amely a térképek létrehozásához használható az összetettebb térképek jobb támogatása érdekében.
* Figyelt virtuális gépek most már szerepelnek az ügyfélcsoport csomópont, és a fánk diagram mutatja a figyelt és nem figyelt virtuális gépek a csoportban.  Azt is fel lehet használni, hogy szűrje a gépek listáját, ha a csoport ki van bontva.
* Figyelt virtuális gépek most már szerepelnek a kiszolgálóport csoport csomópontok, és a fánk diagram mutatja a figyelt és nem figyelt gépek a csoportban.  Azt is fel lehet használni, hogy szűrje a gépek listáját, ha a csoport ki van bontva.
* A térképstílus frissült, hogy jobban konzisztenslegyen az Alkalmazáselemzési adatokból származó Alkalmazástérképpel.
* Az oldalsó panelek frissültek, és nem rendelkeznek a Szolgáltatástérkép - Frissítéskezelés, Változáskövetés, Biztonság és Ügyfélszolgálat által támogatott integrációs szolgáltatások teljes készletével. 
* A csoportok és gépek leképezésének lehetősége frissült, és most már támogatja az előfizetéseket, az erőforráscsoportokat, az Azure virtuálisgép-méretezési csoportokat és a felhőszolgáltatásokat.
* Új szolgáltatásleképezési gépcsoportokat nem hozhat létre az Azure Monitor szolgáltatás virtuális gépek leképezése szolgáltatásban.  

### <a name="why-do-my-performance-charts-show-dotted-lines"></a>Miért jelennek meg pontozott vonalak a teljesítménydiagramokon?
Ennek több oka is lehet.  Azokban az esetekben, ahol az adatgyűjtésben hézag van, a vonalakat pontozottként ábrázoljuk.  Ha módosította az engedélyezett teljesítményszámlálók adatmintavételi gyakoriságát (az alapértelmezett beállítás az adatok 60 másodpercenként történő gyűjtése), akkor a diagramon pontozott vonalak láthatók, ha szűk időtartományt választ a diagramhoz, és a mintavételi gyakoriság kisebb, mint a diagramon használt gyűjtőméret (például a mintavételi gyakoriság 10 percenként, és a diagramon minden egyes vödör 5 perc).  Ha szélesebb időtartományt választ a megtekintéshez, a diagramvonalak nak folytonos vonalakként kell megjelenniük, nem pedig pontként ebben az esetben.

### <a name="are-groups-supported-with-azure-monitor-for-vms"></a>A csoportok támogatottak az Azure Monitor virtuális gépekhez?
Igen, miután telepítette a függőségi ügynök adatokat gyűjtünk a virtuális gépekről az előfizetés, az erőforráscsoport, a virtuálisgép-méretezési készletek és a felhőszolgáltatások alapján csoportok megjelenítéséhez.  Ha szolgáltatástérképet használt, és gépcsoportokat hozott létre, ezek is megjelennek.  A számítógépcsoportok akkor is megjelennek a csoportszűrőben, ha a megtekintett munkaterülethez hozta létre őket. 

### <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Hogyan láthatom a 95.
Alapértelmezés szerint a lista úgy van rendezve, hogy a kiválasztott metrika 95.  A diagramra kattintva megnyílik a **Felső N lista** nézet a megfelelő mutatóval.

### <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>Hogyan kezeli a Map funkció a különböző virtuális hálózatok és alhálózatok duplikált IP-k kezelésére?
Ha az IP-tartományokat virtuális gépekkel vagy az Azure virtuálisgép-méretezési készletekkel duplikálja az alhálózatok és a virtuális hálózatok között, előfordulhat, hogy az Azure Monitor szolgáltatás virtuálisgépek leképezése helytelen információkat jelenít meg. Ez egy ismert probléma, és vizsgáljuk a lehetőségeket, hogy javítsa ezt a tapasztalatot.

### <a name="does-map-feature-support-ipv6"></a>Támogatja a Map szolgáltatás az IPv6-ot?
A térképfunkció jelenleg csak az IPv4 protokollt támogatja, és az IPv6 támogatását vizsgáljuk. Az IPv6-on belül bújtatott IPv4-et is támogatjuk.

### <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>Amikor betöltök egy térképet egy erőforráscsoporthoz vagy más nagy csoporthoz, a térkép nehezen tekinthető meg
Bár a nagy és összetett konfigurációk kezeléséhez javítottuk a Map-et, rájövünk, hogy a térkép sok csomó csomó csomóponttal, kapcsolattal és fürtként működő csomóval rendelkezhet.  Elkötelezettek vagyunk a mellett, hogy tovább növeljük a méretezhetőség növelését.   

### <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>Miért néz ki a Teljesítmény lapon a hálózati diagram, mint az Azure virtuális gép áttekintése lapon lévő hálózati diagram?

Az Azure virtuális gép áttekintő lapja a vendég virtuális gép ben végzett tevékenység mérése alapján jeleníti meg a diagramokat.  Az Azure virtuális gép áttekintése a hálózati diagram, csak a hálózati forgalom, amely a számlázási lesz.  Ez nem foglalja magában a virtuális hálózatok közötti forgalmat.  Az Azure Monitor virtuális gépekhez megjelenített adatok és diagramok a vendég virtuális gép adataira épülnek, és a hálózati diagram megjeleníti az összes TCP/IP-forgalmat, amely az adott virtuális gép felé és kimenő, beleértve a virtuális hálózatokat is.

### <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>Hogyan mérik a válaszidőt a VMConnection-ben tárolt és a kapcsolatpanelen és a munkafüzetekben?

A válaszidő közelítés. Mivel nem instrument a kódot az alkalmazás, nem igazán tudom, mikor kezdődik a kérelem, és mikor érkezik a válasz. Ehelyett megfigyeljük, hogy a kapcsolaton keresztül küldött adatok, majd a kapcsolaton lévő adatok visszatérnek. Ügynökünk nyomon követi ezeket a küldéseket és fogadásokat, és megpróbálja párosítani őket: a küldések sorozatát, majd a fogadások sorozatát kérés/válasz párként értelmezi. A műveletek közötti időzítés a válaszidő. Ez magában foglalja a hálózati késést és a kiszolgáló feldolgozási idejét.

Ez a közelítés jól működik a kérelmeken/válaszokon alapuló protokollok esetében: egyetlen kérelem jelenik meg a kapcsolaton, és egyetlen válasz érkezik. Ez a helyzet a HTTP(S) (nélkül csővezeték), de nem teljesülnek más protokollok.

### <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Vannak-e korlátozások, ha én vagyok a Log Analytics ingyenes díjszabási csomag?
Ha az Azure Monitort az *ingyenes* díjszabási szint használatával konfigurálta a Log Analytics-munkaterülettel, az Azure Monitor for VMs Map funkció csak öt, a munkaterülethez csatlakoztatott csatlakoztatott gépet támogat. Ha öt virtuális gép csatlakozik egy szabad munkaterülethez, leválasztja az egyik virtuális gépet, majd később csatlakozik egy új virtuális géphez, az új virtuális gép nem figyelhető, és megjelenik a Térkép lapon.  

Ebben a feltételben a rendszer megkérdezi a **Kipróbálás most** lehetőséget, amikor megnyitja a virtuális gép, és válassza **insights** a bal oldali ablaktáblában, még akkor is, ha már telepítve van a virtuális gép.  Azonban nem kéri a beállításokat, mint általában akkor fordul na be, ha ez a virtuális gép nem volt berakva az Azure Monitor virtuális gépekhez. 


## <a name="next-steps"></a>További lépések
Ha a kérdésére itt nem kap választ, további kérdéseket és válaszokat az alábbi fórumokon talál.

- [Log Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights)
- [Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)

Az Azure Monitorral kapcsolatos általános visszajelzésért látogasson el a [visszajelzési fórumra.](https://feedback.azure.com/forums/34192--general-feedback)
