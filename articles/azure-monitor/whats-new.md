---
title: Az Azure Monitor dokumentációjának újdonságai
description: Az Azure Monitor dokumentációjának jelentős frissítései havonta frissülnek.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 03/05/2020
ms.openlocfilehash: a2e6d2a459a6713aa9372496fc3a933c0a886ed9
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802604"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Az Azure Monitor dokumentációjának újdonságai?
Ez a cikk az Azure Monitor azokat a cikkeket sorolja fel, amelyek újak vagy jelentősen frissültek. Minden hónap első hetében frissül, hogy tartalmazza az előző hónap cikkfrissítéseit.

## <a name="march-2020"></a>2020. március

### <a name="general"></a>Általános kérdések
- [Az Azure Monitor áttekintése](overview.md) – Hozzáadott Azure Monitor áttekintő videó.
- [Az Azure Monitor ügyféláltal felügyelt kulcskonfigurációja](platform/customer-managed-keys.md) – Általános frissítések.
- [Azure Monitor-adatok hivatkozás](/azure/azure-monitor/reference) – Új webhely.

### <a name="alerts"></a>Riasztások

- [Tevékenységnapló-riasztások létrehozása, megtekintése és kezelése az Azure Monitorban](platform/alerts-activity-log.md) – Az Erőforrás-kezelő sablon további magyarázata.
- [Ismerje meg, hogyan működnek a metrikariasztások az Azure Monitorban.](platform/alerts-metric-overview.md) - Kormányzati támogatásért frissítve.
- [Az Azure Monitor riasztásainak és értesítéseinek hibaelhárítása](platform/alerts-troubleshoot.md) – Új cikk

### <a name="application-insights"></a>Application Insights
- [Automatizálja az Azure Application Insights-ot a PowerShell segítségével](app/powershell.md) – ARMClient-példákat adott hozzá.
- [Telemetriai adatok folyamatos exportálása az Application Insightsból](app/export-telemetry.md) – Az exportálási struktúra részleteivel rendelkező tábla hozzáadása.
- [Snapshot Debugger engedélyezése a .NET-alkalmazásokhoz az Azure App Service-ben](app/snapshot-debugger-appservice.md) – Például hozzáadott erőforrás-kezelő sablon.
- [Az Azure Application Insights használati és költségeinek kezelése](app/pricing.md) – Az adatkorlát-riasztással kapcsolatos információk hozzáadása.
- [Python-alkalmazások figyelése az Azure Monitor (előzetes verzió)](app/opencensus-python.md) – Hozzáadott szabványos metrikák.
- [JavaScript-alkalmazások forrástérkép-támogatása – Azure Monitor Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/source-map-support) – Új cikk.


### <a name="containers"></a>Containers
- [Az Azure Monitor –](faq.md) Az Azure Monitor frissítése tárolókhoz.
- [GPU-figyelés konfigurálása az Azure Monitor tárolókkal](insights/container-insights-gpu-monitoring.md) – Új cikk

### <a name="insights"></a>Insights
- [Office 365 felügyeleti megoldás az Azure-ban](insights/solution-office-365.md) – Frissült az eprekation dátuma.

### <a name="logs"></a>Naplók
- [Naplólekérdezések optimalizálása az Azure Monitorban](log-query/query-optimization.md) – Hozzáadott CPU-feltétel XML- és JSON-elemzéshez.
- [Az Azure Log Analytics-munkaterület törlése és helyreállítása](platform/delete-workspace.md) – Hozzáadott hibaelhárítás.
- [Azure Monitor naplók azure logic apps és a Power Automate](platform/logicapp-flow-connector.md) – Frissítve az új Azure Monitor-összekötő.

### <a name="metrics"></a>Mérőszámok
- [Lemez metrikák epercia az Azure Portalon](https://docs.microsoft.com/azure/azure-monitor/platform/portal-disk-metrics-deprecation) – Új cikk.
- [Oktatóanyag – Metrikadiagram létrehozása az Azure Monitorban](learn/tutorial-metrics-explorer.md) – Hozzáadott videó.

### <a name="platform-logs"></a>Platformnaplók
- [Az Azure-tevékenységnapló gyűjtése és elemzése az Azure Monitorban](platform/activity-log-collect.md) – Újraírás a tevékenységnapló diagnosztikai beállításokkal történő gyűjtésének és elemzésének jobb magyarázata érdekében.

### <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)
- [Az Azure virtuális gépei figyelése az Azure Monitor](insights/monitor-vm-azure.md) – Új cikk segítségével.
- [Rövid útmutató: Az Azure virtuális gépek figyelése az Azure Monitorlal](learn/quick-monitor-azure-vm.md) – Frissítve az Azure Monitor virtuális gépekhez való hozzáadásához.
- [Riasztások az Azure Monitor virtuális gépekhez](insights/vminsights-alerts.md) – Új cikk
- [Az Azure Monitor engedélyezése virtuális gépekhez –](insights/vminsights-enable-overview.md) Frissített ügynök letöltési hivatkozások.


Általános frissítések az Azure Monitor virtuális gépekhez szolgáltatásának általános elérhetőségéhez
- [Mi az Azure Monitor virtuális gépekhez?](insights/vminsights-overview.md)
- [Az Azure Monitor virtuális gépekhez (GA) gyakori kérdések](insights/vminsights-ga-release-faq.md) 
- [Az Azure Monitor engedélyezése virtuális gépekhez az Azure Policy használatával](insights/vminsights-enable-at-scale-policy.md) 
- [Teljesítménydiagram az Azure Monitor szolgáltatással a virtuális gépekhez](insights/vminsights-performance.md)
- [Naplók lekérdezése az Azure Monitor szolgáltatásból virtuális gépekhez](insights/vminsights-log-search.md)
- [Az alkalmazások függőségének megtekintése a virtuális gépekHez tartozó Azure Monitor szolgáltatással](insights/vminsights-maps.md) 


### <a name="visualizations"></a>Vizualizációk
- [Az Azure Monitor adatainak megjelenítése](visualizations.md) – Frissítve a View Designer tervezett eprecációjának megjegyzéséhez.





## <a name="february-2020"></a>2020. február

### <a name="agents"></a>Ügynökök
Több frissítés a diagnosztikai bővítmény tartalmának újraírása részeként.

- [Az Azure figyelési ügynökök áttekintése](platform/agents-overview.md) – Átstrukturált táblák, hogy jobban tisztázza az egyes ügynökök egyedi funkcióit.
- [Azure Diagnostics bővítmény áttekintése](platform/diagnostics-extension-overview.md) – Teljes újraírás.
- [Blob storage az IIS és a table storage események az Azure Monitor](platform/diagnostics-extension-logs.md) – Általános átírás a frissítés és az egyértelműség érdekében.
- [Telepítse és konfigurálja a Windows Azure diagnosztikai bővítményt (WAD)](platform/diagnostics-extension-windows-install.md) – Új cikk. 
- [Windows diagnosztikai bővítményséma](platform/diagnostics-extension-schema-windows.md) – átszervezve.
- [Adatok küldése a Windows Azure diagnosztikai bővítményből az Azure Event Hubs-ba](platform/diagnostics-extension-stream-event-hubs.md) – teljesen átírt és frissített.
- [Diagnosztikai adatok tárolása és megtekintése az Azure Storage-ban](/azure/cloud-services/diagnostics-extension-to-storage) – teljesen átírt és frissített.
- [Log Analytics virtuálisgép-bővítmény windowshoz](../virtual-machines/extensions/oms-windows.md) – Jobban tisztázza a kapcsolatot a Log Analytics-ügynökkel.
- [Azure Monitor virtuálisgép-bővítmény Linuxhoz](../virtual-machines/extensions/oms-linux.md) – Jobban tisztázza a kapcsolatot a Log Analytics-ügynökkel.




### <a name="application-insights"></a>Application Insights
- [Kapcsolati karakterláncok az Azure Application Insights](app/sdk-connection-string.md) – Új cikk.

### <a name="insights-and-solutions"></a>Betekintések és megoldások

#### <a name="azure-monitor-for-containers"></a>Azure Monitor tárolókhoz
- [Integrálja az Azure Active Directoryt az Azure Kubernetes-szolgáltatással](../aks/azure-ad-integration.md) – Hozzáadott megjegyzés egy ügyfélalkalmazás létrehozásához az RBAC-kompatibilis fürt támogatásához az Azure Monitor tárolók hoz.

#### <a name="azure-monitor-for-vms"></a>Azure Monitor virtuális gépekhez
- [Az Azure Monitor virtuális gépek (GA) gyakran ismételt kérdések](insights/vminsights-ga-release-faq.md) – A teljesítményadatok tárolási módjának módosítása.

#### <a name="office-365"></a>Office 365
- [Office 365 felügyeleti megoldás az Azure-ban](insights/solution-office-365.md) – Frissült az eprekation dátuma.


### <a name="logs"></a>Naplók
- [Naplólekérdezések optimalizálása az Azure Monitor](log-query/query-optimization.md) – Új cikk.
- [Az Azure Monitor-naplók használatának és költségeinek kezelése](platform/manage-cost-storage.md) – Továbbfejlesztett mintalekérdezések a használat megértéséhez.

### <a name="metrics"></a>Mérőszámok
- [Az Azure Monitor platformmetrikák exportálható diagnosztikai beállítások –](platform/metrics-supported-export-diagnostic-settings.md) Hozzáadott szakasz a változás a viselkedés a null és nulla értékek.


### <a name="visualizations"></a>Vizualizációk
Több új cikk a tervező és a munkafüzetek konverziós útmutatójának megtekintéséhez.

- [Az Azure Monitor nézet tervezője a munkafüzetek átmeneti útmutató](platform/view-designer-conversion-overview.md) – Új cikk.
- [Az Azure Monitor nézet tervezője a munkafüzetek konvertálási beállításai](platform/view-designer-conversion-options.md) – Új cikk.
- [Az Azure Monitor nézet tervezője a munkafüzetek csempe konverziók](platform/view-designer-conversion-tiles.md) – Új cikk.
- [Az Azure Monitor nézet tervezője a munkafüzetek konverziós összegzése és elérése](platform/view-designer-conversion-access.md) – Új cikk.
- [Az Azure Monitor nézet tervezője a munkafüzetek konvertálásának gyakori feladatai](platform/view-designer-conversion-tasks.md) – Új cikk.
- [Az Azure Monitor nézet tervezője a munkafüzetek konvertálási példái](platform/view-designer-conversion-examples.md) – Új cikk.




## <a name="january-2020"></a>2020. január

### <a name="general"></a>Általános kérdések
- [Mit figyel az Azure Monitor?](monitor-reference.md) - Új cikk.

### <a name="agents"></a>Ügynökök
- [Naplóadatok gyűjtése az Azure Log Analytics-ügynökkel](platform/log-analytics-agent.md) – Frissített hálózati tűzfalkövetelmények tábla.


### <a name="alerts"></a>Riasztások
- [Műveletcsoportok létrehozása és kezelése az Azure Portalon](platform/action-groups.md) – A v2-funkciók, amelyek már nem szükséges beállítás.
- [Metrikariasztás létrehozása Erőforrás-kezelő sablonnal](platform/alerts-metric-create-templates.md) – Hozzáadott példa az *ignoreDataBefore* paraméterhez.  A többfeltételes szabályokkal kapcsolatos korlátozások hozzáadása.
- [A Log Analytics Alert REST API használatával](platform/api-alerts.md) – JSON-példa javítva.


### <a name="application-insights"></a>Application Insights
- Az Application Insights és a [Log Analytics által használt IP-címek](app/ip-addresses.md) – Frissítette a rendelkezésre állási teszt szakaszt, hogyan adhat hozzá egy bejövő portszabályt az Azure network security csoportok használatával történő forgalom engedélyezéséhez.
- [Az Azure Application Insights Profiler –](app/profiler-troubleshooting.md) Frissített általános hibaelhárítási problémák elhárítása.
- [Telemetriai mintavételezés az Azure Application Insights –](app/sampling.md) frissítve és átszervezve, hogy javítsa az olvashatóságot az ügyfelek visszajelzései alapján.


### <a name="data-security"></a>Adatbiztonság
- [Az Azure Monitor ügyféláltal felügyelt kulcskonfiguráció](platform/customer-managed-keys.md) – Új cikk.

### <a name="insights-and-solutions"></a>Betekintések és megoldások

#### <a name="azure-monitor-for-containers"></a>Azure Monitor tárolókhoz
- [Konfigurálja az Azure Monitortárolók ügynökének adatgyűjtését](insights/container-insights-agent-config.md) – Részletek hozzáadása az Ügynök frissítéséhez az Azure Red Hat OpenShift en, és további információkat adott hozzá az ügynök frissítésének módszereinek megkülönböztetéséhez.
- [Hozzon létre teljesítményriasztásokat az Azure Monitor hoz létre a tárolókhoz](insights/container-insights-alerts.md) – Módosított információk és frissített lépések a munkaterületi környezetben tárolt teljesítményadatokra vonatkozó riasztás létrehozásához munkaterület-környezetriasztások használatával.
- [Kubernetes figyelés az Azure Monitor tárolók –](insights/container-insights-analyze.md) Frissítette mind az áttekintő cikket, és az elemzési cikk a Windows Kubernetes-fürtök támogatásáról.
- [Konfigurálja az Azure Red Hat OpenShift-fürtöket](insights/container-insights-azure-redhat-setup.md) az Azure Monitor tárolókkal – Részletek hozzáadása az Ügynök frissítéséhez az Azure Red Hat OpenShift en, és további információkkal egészítheti ki az ügynök frissítésének módszereit.
- [Konfigurálja a hibrid Kubernetes-fürtöket az Azure Monitortárolókkal](insights/container-insights-hybrid-setup.md) – Frissítve, hogy tükrözze a biztonságos port:10250 a Kubelet cAdvisorral nyújtott további támogatást.
- [Az Azure Monitor tárolók ügynökének kezelése](insights/container-insights-manage-agent.md) – Az Azure Red Hat OpenShift metrikakapachasználatával kapcsolatos részletek frissítése más típusú Kubernetes-fürtökhöz képest.
- [Konfigurálja az Azure Monitort a Prometheus-integráció tárolóihoz](insights/container-insights-prometheus-integration.md) – Frissített részletek a viselkedéssel és a metrikakaparás konfigurációjával kapcsolatban az Azure Red Hat OpenShift-el más típusú Kubernetes-fürtökhöz képest.
- [Az Azure Monitor frissítése metrikák tárolóihoz](insights/container-insights-update-metrics.md) – A metrikakaparás sal kapcsolatos részletek frissítése az Azure Red Hat OpenShift szolgáltatással más típusú Kubernetes-fürtökhöz képest.


#### <a name="azure-monitor-for-vms"></a>Azure Monitor virtuális gépekhez
- [Az Azure Monitor virtuális gépek (GA) gyakran ismételt kérdések](insights/vminsights-ga-release-faq.md) – Hozzáadott információk a munkaterület és az ügynökök frissítése az új verzióra.

#### <a name="office-365"></a>Office 365
- [Office 365 felügyeleti megoldás az Azure-ban](insights/solution-office-365.md) – Részletek hozzáadása és gyakori kérdések az Office 365-megoldásba való áttérésről az Azure Sentinelben. Eltávolítás onboarding szakasz.



### <a name="logs"></a>Naplók
- [A Log Analytics-munkaterületek kezelése az Azure Monitorban](platform/manage-access.md) – A nem műveletek frissítései.
- [Az Azure Monitor-naplók használati és költségeinek kezelése](platform/manage-cost-storage.md) – Az adatmennyiség kiszámításának további pontosítása a Díjszabási modell szakaszban.
- [Az Azure Resource Manager-sablonok használatával hozzon létre és konfiguráljon egy Log Analytics-munkaterületet](platform/template-workspace-configuration.md) – Frissített sablon új tarifacsomagokkal.


### <a name="platform-logs"></a>Platformnaplók
- [Az Azure-tevékenységnapló gyűjtése diagnosztikai beállításokkal - Azure Monitor](platform/diagnostic-settings-legacy.md) - További információ a megváltozott tulajdonságokról.
- [Exportálja az Azure-tevékenységnaplót](platform/activity-log-export.md) – a felhasználói felület módosításai miatt frissítve. 





## <a name="december-2019"></a>2019. december

### <a name="agents"></a>Ügynökök
- [Linux-számítógépek csatlakoztatása az Azure Monitorhoz](platform/agent-linux.md) – Új cikk.

### <a name="alerts"></a>Riasztások
- [Metrikariasztás létrehozása Erőforrás-kezelő sablonnal](platform/alerts-metric-create-templates.md) – Hozzáadott példa egyéni metrikához.
- [Riasztások létrehozása dinamikus küszöbértékekkel az Azure Monitorban](platform/alerts-dynamic-thresholds.md) – A dinamikus küszöbérték-diagramok értelmezésére vonatkozó szakasz hozzáadva.
- [A riasztási és értesítésfigyelés áttekintése az Azure-ban](platform/alerts-overview.md) – Frissített Erőforrásgráf-lekérdezés.
- [Metrikariasztások támogatott erőforrások az Azure Monitorban](platform/alerts-metric-near-real-time.md) – A metrikák és a támogatott dimenziók frissítése.
- [Váltás az örökölt Log Analytics-riasztások API-ról az új Azure Alerts API-ra](platform/alerts-log-api-switch.md) – A módosított riasztási névhez hozzáadott megjegyzés.
- [Ismerje meg, hogyan működnek a metrikariasztások az Azure Monitorban.](platform/alerts-metric-overview.md) - Hozzáadott támogatott erőforrás-típusok figyelése nagy méretekben.

### <a name="application-insights"></a>Application Insights
- [Application Insights a Worker Service-alkalmazások (nem HTTP-alkalmazások)](app/worker-service.md) – Hozzáadott alapértelmezett naplózási szint A C# kódot. Frissített csomagreferencia-verzió.
- [ApplicationInsights.config reference - Azure](app/configuration-with-applicationinsights-config.md) – Frissített mintakód.
- [Automatizálhatja az Azure Application Insightsot a PowerShell használatával](app/powershell.md) – Az Erőforrás-kezelő sablon frissítése.
- [Azure Monitor Application Insights NuGet csomagok](app/nuget.md) – Frissített csomagverziók.
- [Hozzon létre egy új Azure Application Insights-erőforrást](app/create-new-resource.md) – A globálisan egyedi névhez hozzáadott megjegyzés.
- [Diagnosztizálás a Live Metrics Stream - Azure Application Insights](app/live-stream.md) – Frissítve ASP.NET Core SDK-verzió követelmény.
- [Eseményszámlálók az Application Insights –](app/eventcounters.md) frissített kategória és tábla customMetrics.
- [Fedezze fel a Java-nyomkövetési naplókat az Azure Application Insightsban](app/java-trace-logs.md) – A Java-ügynök naplózási küszöbértékének hozzáadott konfigurációja.
- [Az Application Insights és a Log Analytics által használt IP-címek](app/ip-addresses.md) – Az élő metrikák streamjének frissített IP-címei.
- [Az Azure-alkalmazások szolgáltatásainak teljesítményének figyelése](app/azure-web-apps.md) – A ASP.NET Core 3.0 támogatása. 
- [Python-alkalmazások figyelése az Azure Monitor (előzetes verzió)](app/opencensus-python.md) – Az Azure-hoz való OpenCensus Python-séma leképezésének tisztázása hozzáadva. Séma figyelése
- [Kibocsátási megjegyzések az Azure Application Insights](app/release-notes.md) – Hozzáadott megjegyzések a régebbi kiadásokhoz.
- [Telemetriai csatornák az Azure Application Insights](app/telemetry-channels.md) – frissített időtartama elvetett adatok hosszabb ideig megszakadt kapcsolat.
- [Telemetriai mintavételezés az Azure Application Insights](app/sampling.md) – javított kódrészlet egyéni Telemetriai initializer.
- [Alkalmazáselemzési adatok hibaelhárítása Java webprojektben](app/java-troubleshoot.md) – Eltávolított utasítás arról, hogy nem támogatja a függőségi gyűjteményt a JDK 9-ben.

### <a name="insights-and-solutions"></a>Betekintések és megoldások
- [Azure Monitor tárolók gyakori kérdések](insights/container-insights-faq.md) – Hozzáadott kérdés a kép és a név mezőket.
- [Azure SQL Analytics-megoldás az Azure Monitorban](insights/azure-sql.md) – A frissített adatbázis a felügyelt példány okait várja.
- [Konfigurálja az Azure Monitor tárolók ügynök adatgyűjtés](insights/container-insights-agent-config.md) – Hozzáadott beállítás enrich_container_logs.
- [Konfigurálja a hibrid Kubernetes-fürtöket az Azure Monitor tárolókhoz](insights/container-insights-hybrid-setup.md) – Hozzáadott hibaelhárítási szakasz.
- [Az Active Directory replikációs állapotának figyelése az Azure Monitor](insights/ad-replication-status.md) – a .
- [Hálózati teljesítményfigyelő megoldás az Azure-ban](insights/network-performance-monitor.md) – Támogatott régiók hozzáadása.
- [Optimalizálja az Active Directory-környezetet az Azure Monitor](insights/ad-assessment.md) – a .
- [Optimalizálja sql server környezetét az Azure Monitor](insights/sql-assessment.md) – a .
- [Optimalizálja a System Center Operations Manager környezetét az Azure Log Analytics](insights/scom-assessment.md) – .
- [Támogatott kapcsolatok it service management összekötő vel az Azure Log Analytics –](platform/itsmc-connections.md) New York-ot adta hozzá az előfeltételként szolgáló ügyfélazonosítóhoz és ügyféltitokhoz.

### <a name="logs"></a>Naplók
- [Törölje és helyreállítsa az Azure Log Analytics-munkaterületet](platform/delete-workspace.md) – Hozzáadott PowerShell-metódus.
- [Az Azure Monitor naplók központi telepítésének tervezése](platform/design-logs-deployment.md) – a munkaterület betöltési aránya nőtt.

### <a name="metrics"></a>Mérőszámok
- [Az Azure Monitor platformmetrikák exportálható diagnosztikai beállítások –](platform/metrics-supported-export-diagnostic-settings.md) új cikk.

### <a name="platform-logs"></a>Platformnaplók
Több cikk frissítve a tartalom átstrukturálásának részeként a platformnaplókhoz a tevékenységnapló diagnosztikai beállításokkal történő konfigurálásához szükséges új funkció alapján.

- [Az Azure-erőforrásnaplók archiválása tárfiókba](platform/resource-logs-collect-storage.md)
- [Az Azure-tevékenységnapló eseménysémája](platform/activity-log-schema.md)
- [Az Azure Monitor szolgáltatási korlátai](service-limits.md)
- [Azure-tevékenységnaplók gyűjtése és elemzése a Log Analytics-munkaterületen](platform/activity-log-collect.md)
- [Azure-tevékenységnapló gyűjtése diagnosztikai beállításokkal (előzetes verzió) – Azure Monitor](platform/diagnostic-settings-legacy.md)
- [Azure-tevékenységnaplók gyűjtése egy Log Analytics-munkaterületre az Azure-bérlők között](platform/activity-log-collect-tenants.md)
- [Azure-erőforrásnaplók gyűjtése a Log Analytics-munkaterületen](platform/resource-logs-collect-workspace.md)
- [Diagnosztikai beállítás létrehozása az Azure-ban az Erőforrás-kezelő sablon használatával](platform/diagnostic-settings-template.md)
- [Diagnosztikai beállítás létrehozása naplók és metrikák gyűjtéséhez az Azure-ban](platform/diagnostic-settings.md)
- [Az Azure-tevékenységnapló exportálása](platform/activity-log-export.md)
- [Az Azure platformnaplók áttekintése](platform/platform-logs-overview.md)
- [Az Azure figyelési adatainak streamelése az eseményközpontba](platform/stream-monitoring-data-event-hubs.md)
- [Az Azure platformnaplóinak streamelése egy eseményközpontba](platform/resource-logs-stream-event-hubs.md)

### <a name="quickstarts-and-tutorials"></a>Rövid útmutatók és oktatóanyagok

- [Metrikadiagram létrehozása az Azure Monitor](learn/tutorial-metrics-explorer.md) – Új cikkben.
- [Erőforrásnaplók gyűjtése egy Azure-erőforrásból, és elemezze az Azure Monitor](learn/tutorial-resource-logs.md) – Új cikk.
- [Azure-erőforrás figyelése az Azure Monitor](learn/quick-monitor-azure-resource.md) – Új cikk.
   
## <a name="next-steps"></a>További lépések

- Ha szeretne hozzájárulni az Azure Monitor dokumentációjához, olvassa el a [Közreműködő dokumentuma című témakört.](https://docs.microsoft.com/contribute/)