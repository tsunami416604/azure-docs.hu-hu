---
title: Az Azure Monitor dokumentációjának újdonságai
description: A Azure Monitor dokumentációjának frissítése havonta frissül.
ms.subservice: ''
ms.topic: overview
author: bwren
ms.author: bwren
ms.date: 07/08/2020
ms.openlocfilehash: 192219a804365957e9eaa0577019ff18d75861bf
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86498508"
---
# <a name="whats-new-in-azure-monitor-documentation"></a>Az Azure Monitor dokumentációjának újdonságai

Ez a cikk felsorolja Azure Monitor új vagy jelentős mértékben frissített cikkeket. A rendszer minden hónap első hetében frissíti az előző hónapból származó cikkek frissítéseinek befoglalásával.

## <a name="june-2020"></a>2020. június

### <a name="general"></a>Általános kérdések
- [Azure monitor üzembe helyezése](platform/deploy-scale.md) – új cikk.
- [Azure monitor ügyfél által felügyelt kulcs](platform/customer-managed-keys.md) – frissített billingtype tulajdonság. PowerShell-parancsok hozzáadva.

### <a name="agents"></a>Ügynökök
- [Log Analytics Agent áttekintése](platform/log-analytics-agent.md) – a Python 2 követelményének hozzáadása.

### <a name="alerts"></a>Riasztások
- [Riasztási szabályok vagy műveleti szabályok frissítése, ha a célként megadott erőforrás egy másik Azure-régióba kerül át](platform/alerts-resource-move.md) – új cikk.
- [Azure metric-riasztások hibaelhárítása](platform/alerts-troubleshoot-metric.md) – új cikk.
- [Riasztások hibaelhárítása Azure monitor](platform/alerts-troubleshoot-metric.md) – új cikk.
  
### <a name="application-insights"></a>Application Insights
- [Azure Application Insights JavaScript-webalkalmazásokhoz](app/javascript.md) – frissítés a JavaScript SDK-hoz szakasz. Frissített kódrészlet a betöltési hibák jelentéséhez.
- [A BYOS konfigurálása (saját tárhely használata) a Profiler & Snapshot Debugger](app/profiler-bring-your-own-storage.md) – új cikk.
- [Bejövő kérelmek nyomon követése az Azure Application Insights a OpenCensus Pythonban](app/opencensus-python-request.md) – frissített naplózás és konfiguráció a OpenCensus.
- [Élő ASP.net-webalkalmazás figyelése az Azure Application Insights](app/monitor-performance-live-website-now.md) -frissítve decprecation date for Állapotmonitor v1.
- [Node.js szolgáltatások figyelése az Azure Application Insights](app/nodejs.md) – több frissítés, beleértve a áteresztő-verziók és az SDK-konfiguráció áttelepítését
- [Python-alkalmazások figyelése Azure monitor (előzetes verzió)](app/opencensus-python.md) – további szakasz a Azure monitor-exportőrök konfigurálásához.
- [Alkalmazások figyelése programkód módosítása nélkül – automatikus rendszerállapot-Azure Monitor Application Insights](app/codeless-overview.md) – új cikk.
- [Az SDK-Betöltési hiba elhárítása JavaScript-webalkalmazások esetén](app/javascript-sdk-load-failure.md) – új cikk.

### <a name="containers"></a>Tárolók
- [A hibrid Kubernetes-fürthöz tartozó új szakasz figyelésének leállítása](insights/container-insights-optout-hybrid.md) az ív-kompatibilis Kubernetes.
- [Azure arc-kompatibilis Kubernetes-fürt konfigurálása Azure monitor for containers](insights/container-insights-enable-arc-enabled-clusters.md) – új cikk.
- Az [Azure Red Hat OpenShift v4. x konfigurálása Azure monitor for containers esetében](insights/container-insights-azure-redhat4-setup.md) – frissített előfeltételek.
- A [tárolók élő adatainak (előzetes verzió) Azure monitor beállítása](insights/container-insights-livedata-setup.md) – eltávolított Megjegyzés az Azure US government szolgáltatásban nem elérhető funkciókról.

### <a name="insights"></a>Insights
- [Gyakori kérdések – Network Performance monitor megoldás az Azure-ban](insights/network-performance-monitor-faq.md) – ExpressRoute-figyelő – gyakori kérdések.

### <a name="logs"></a>Naplók
- [Az Azure log Analytics-munkaterület törlése és helyreállítása](platform/delete-workspace.md) – PowerShell-parancs hozzáadva. Frissített hibaelhárítás.
- [Log Analytics munkaterületek kezelése Azure monitor](platform/manage-access.md) – példa a nem engedélyezett táblákra a RBAC szakaszban.
- [Azure monitor naplók használatának és költségeinek kezelése](platform/manage-cost-storage.md) – az adatméret számításával kapcsolatos további részletek. Frissült az adatmennyiség-riasztások konfigurálása. Az Azure Sentinel által összegyűjtött biztonsági adatok részletei. Az adatsapka pontosítása.
- [Azure monitor naplókat használhat a Azure Logic apps és a Power automatizáló](platform/logicapp-flow-connector.md) által hozzáadott conection-korlátokkal.

### <a name="metrics"></a>Mérőszámok
- [Azure monitor támogatott mérőszámok erőforrástípus szerint](platform/metrics-supported.md) – frissített SQL Server metrikák.


### <a name="platform-logs"></a>Platformnaplók

- [Resource Manager-sablonok – minták a diagnosztikai beállításokhoz](samples/resource-manager-diagnostic-settings.md) – javítás a műveletnapló diagnosztikai beállításához.
- [Azure-tevékenység naplójának küldése log Analytics munkaterületre Azure Portal](learn/quick-collect-activity-log-portal.md) – új cikk használatával.
- [Azure-tevékenység naplójának küldése log Analytics munkaterületre Azure Resource Manager sablon használatával](learn/quick-collect-activity-log-arm.md) – új cikk.

#### <a name="new-and-updated-articles-from-restructure-and-consolidation-of-platform-log-content"></a>Új és frissített cikkek a platform log-tartalmak újrastrukturálása és összevonása terén
- [Azure-beli erőforrás-naplók archiválása a Storage-fiókba](./platform/resource-logs.md#send-to-azure-storage)
- [Azure Activity log esemény sémája](platform/activity-log-schema.md)
- [Azure-tevékenység naplója](platform/activity-log.md)
- [CLI-minták Azure Monitor](samples/cli-samples.md)
- [PowerShell-minták Azure Monitor](samples/powershell-samples.md)
- [Azure monitoring REST API útmutató](platform/rest-api-walkthrough.md)
- [Az Azure Resource naplózza a támogatott szolgáltatásokat és sémákat](./platform/resource-logs-schema.md)
- [Azure-erőforrás-naplók](platform/resource-logs.md)
- [Azure-beli tevékenységek naplójának összegyűjtése és elemzése Azure Monitor](./platform/activity-log.md)
- [Azure-beli erőforrás-naplók gyűjtése Log Analytics munkaterületen](./platform/resource-logs.md#send-to-log-analytics-workspace)
- [Diagnosztikai beállítások létrehozása a platform naplófájljainak és metrikáinak különböző célhelyekre küldéséhez](platform/diagnostic-settings.md)
- [Az Azure-tevékenység naplójának exportálása](./platform/activity-log.md#legacy-collection-methods)
- [Az Azure platform naplófájljainak áttekintése](platform/platform-logs-overview.md)
- [Azure platform-naplók továbbítása egy Event hubhoz](./platform/resource-logs.md#send-to-azure-event-hubs)
- [Azure-Tevékenységnaplók eseményeinek megtekintése Azure Monitor](./platform/activity-log.md#view-the-activity-log)

### <a name="virtual-machines"></a>Virtuális gépek
- [Engedélyezze a Azure Portal](insights/vminsights-enable-single-vm.md) -frissítve Azure monitor for VMS az Azure arc befoglalását.
- Az Azure arc befoglalásának [engedélyezése Azure monitor for VMS áttekintése](insights/vminsights-enable-overview.md) – frissítve.
- [Mi az Azure Monitor for VMs?](insights/vminsights-overview.md) -Frissítve az Azure arc befoglalásával.


### <a name="visualizations"></a>Vizualizációk
- [Azure monitor munkafüzetek adatforrásai](platform/workbooks-data-sources.md) – a riasztások és az egyéni végpontok szakasz.
- [Hibaelhárítás Azure monitor munkafüzet-alapú bepillantást](insights/troubleshoot-workbooks.md) – új cikk.
- [Log Analytics irányítópult-vizualizációk frissítése](log-query/dashboard-upgrade.md) – új cikk.



## <a name="may-2020"></a>2020. május

### <a name="general"></a>Általános kérdések

- [Azure monitor GYIK](faq.md) – további szakasz a mérőszámokhoz.
- [Azure monitor ügyfél által felügyelt kulcs](platform/customer-managed-keys.md) – az általános elérhetőségre való felkészülés különböző változásai.
- [Beépített szabályzat-definíciók a Azure monitor](samples/policy-samples.md) új cikkhez.
- [Felhasználói tulajdonban lévő Storage-fiókok a naplók](platform/private-storage.md) betöltéséhez – új cikk.
- [A Azure monitor-naplók használatának és költségeinek kezelése – a](platform/manage-cost-storage.md) fürt arányos számlázással bővült.
- Az [Azure Private link használatával biztonságosan csatlakoztathatja a hálózatokat Azure monitor](platform/private-link-security.md) új cikkhez.


#### <a name="new-resource-manager-template-samples"></a>Új Resource Manager-sablonok – minták 
- [Resource Manager-sablonok – minták a Azure Monitor](samples/resource-manager-samples.md)
- [Resource Manager-sablonok – minták a műveleti csoportokhoz](samples/resource-manager-action-groups.md)
- [Resource Manager-sablonok – minták az ügynökökhöz](samples/resource-manager-agent.md)
- [Resource Manager-sablonok minták a tárolók Azure Monitoréhez](samples/resource-manager-container-insights.md)
- [Resource Manager-sablonok – minták a Azure Monitor for VMs](samples/resource-manager-vminsights.md)
- [Resource Manager-sablonok – minták a diagnosztikai beállításokhoz](samples/resource-manager-diagnostic-settings.md)
- [Resource Manager-sablonok – minták Log Analytics munkaterületekhez](samples/resource-manager-workspace.md)
- [Resource Manager-sablonok – minták a naplók lekérdezéséhez](samples/resource-manager-log-queries.md)
- [Resource Manager-sablonok – minták a naplózási lekérdezés riasztási szabályaihoz](samples/resource-manager-alerts-log.md)
- [Resource Manager-sablonok – minták metrikus riasztási szabályokhoz](samples/resource-manager-alerts-metric.md)
- [Resource Manager-sablonok – minták a munkafüzetek számára](samples/resource-manager-workbooks.md)

### <a name="agents"></a>Ügynökök
- [Telepítse és konfigurálja a Windows Azure Diagnostics bővítményt (wad)](platform/diagnostics-extension-windows-install.md) – további részletek a diagnosztika konfigurálásáról.
- [Log Analytics Agent áttekintése](platform/log-analytics-agent.md) – támogatott Linux-verziók hozzáadása.

### <a name="application-insights"></a>Application Insights

- [Azure functions futó alkalmazások figyelése Application Insights-Azure monitor](app/monitor-functions.md) -új cikkben.
- [Node.js szolgáltatások figyelése az Azure Application Insights](app/nodejs.md) -általános frissítésekkel, beleértve az előző verziókról való áttelepítés új szakaszát.
- Az [Application Insights által használt IP-címek](app/ip-addresses.md) és a log Analytics – a webhookok és az USA kormányzati szervei által hozzáadott IP-címek.
- [Alkalmazások figyelése az Azure Kubernetes szolgáltatásban (ak) Application Insights-Azure monitor](app/kubernetes-codeless.md) -új cikkel.
- A .NET által hozzáadott [adatokkal kapcsolatos hibák elhárítása](app/asp-net-troubleshoot-no-data.md) – a naplók a DotNet-tracevel való összegyűjtésével kapcsolatos Application Insights.
- [Alkalmazás-változási elemzés használata a Azure monitorban webes alkalmazások problémáinak kereséséhez](app/change-analysis.md) – több frissítés a Change Analysis funkcióban.

#### <a name="new-and-updated-articles-for-preview-of-workspace-based-applications"></a>Új és frissített cikkek a munkaterület-alapú alkalmazások előzetes verziójához
- [Azure Monitor Application Insights munkaterület-alapú erőforrás-sémája](app/apm-tables.md)
- [Új Azure Monitor Application Insights munkaterület-alapú erőforrás létrehozása](app/create-workspace-resource.md)
- [alkalmazás () kifejezés Azure Monitor log lekérdezésekben](log-query/app-expression.md)
- [A lekérdezés hatókörének naplózása Azure Monitor Log Analytics](log-query/scope.md)
- [Erőforrások lekérdezése Azure Monitor](log-query/cross-workspace-query.md)
- [Azure Monitor naplóbejegyzések szabványos tulajdonságai](platform/log-standard-properties.md)
- [Azure Monitor naplók szerkezete](log-query/logs-structure.md)





### <a name="containers"></a>Tárolók
- A [tárolók Azure monitorának engedélyezése](insights/container-insights-onboard.md) – frissített tűzfal-konfigurációs tábla.
- A [tárolók Azure monitorának frissítése a metrikák](insights/container-insights-update-metrics.md) számára – frissítés a felügyelt identitások használatához a metrikák összegyűjtéséhez.
- [A tárolók Azure monitor figyelési díja](insights/container-insights-cost.md) – új cikk.
- [Azure monitor beállítása a tárolók élő adatához (előzetes verzió)](insights/container-insights-livedata-setup.md) – az új fürt szerepkör-kötés támogatása.

### <a name="insights"></a>Insights
- [Azure monitor az Azure cache for Redis (előzetes verzió)](insights/redis-cache-insights-overview.md) – új cikk.
- [Key Vault figyelése Azure monitor for Key Vault (előzetes verzió)](./insights/key-vault-insights-overview.md) – új cikk.

### <a name="logs"></a>Naplók
- [Hozzon létre & konfigurálja a log Analyticst a PowerShell](platform/powershell-workspace-configuration.md) által hozzáadott hibaelhárítási szakaszban.
- [Hozzon létre egy log Analytics munkaterületet a Azure Portal](learn/quick-create-workspace.md) által felvett hibaelhárítás szakaszban.
- [Log Analytics munkaterület létrehozása az Azure CLI](learn/quick-create-workspace-cli.md) -vel – új hibaelhárítási szakasz.
- Az [Azure log Analytics munkaterület törlése és helyreállítása](platform/delete-workspace.md) – frissített információk a törölt munkaterület helyreállításáról.
- [Függvények Azure monitor log lekérdezésekben](log-query/functions.md) – eltávolított Megjegyzés a függvények nem tartalmazó függvényekről.
- [Azure monitor naplók szerkezete – a](log-query/logs-structure.md) Application Insights táblázatra vonatkozó egyértelmű tulajdonságok leírása.
- [Használjon Azure monitor naplókat a Azure Logic apps és a Power Automate](platform/logicapp-flow-connector.md) – hozzáadási korlátok szakaszban.
- [A PowerShell használatával hozzon létre és konfiguráljon egy log Analytics munkaterület](platform/powershell-workspace-configuration.md) által hozzáadott hibaelhárítás szakaszt.


### <a name="metrics"></a>Mérőszámok
- [Azure monitor támogatott mérőszámok erőforrástípus szerint](platform/metrics-supported.md) – egyértelmű vendég metrikák és metrikák útválasztása. 

### <a name="solutions"></a>Megoldások
- [Optimalizálja Active Directory-környezetét Azure monitor](insights/ad-assessment.md) által támogatott verziókhoz hozzáadott Windows Server 2019-es verzióval.
- [Optimalizálja SQL Server-környezetét a Azure monitor](insights/sql-assessment.md) által támogatott SQL Server-verziókhoz.


### <a name="virtual-machines"></a>Virtuális gépek
- A [Azure monitor for VMS áttekintésének engedélyezése](insights/vminsights-enable-overview.md) – az Ubuntu Server támogatott verzióihoz lett hozzáadva. Log Analytics munkaterület által támogatott régiók lettek hozzáadva.
- A [teljesítmény diagramon való Azure monitor for VMS](insights/vminsights-performance.md) hozzáadásával kapcsolatos korlátozások szakasz a nem elérhető metrikák esetében.

### <a name="visualizations"></a>Vizualizációk
- [Azure monitor munkafüzetek és Azure Resource Manager sablonok](platform/workbooks-automate.md) – a Resource Manager-frissítés hozzáadva egy munkafüzet-sablon telepítéséhez.
- [Azure monitor munkafüzetek csoportjai](platform/workbooks-groups.md) – új cikk.
- [Azure monitor munkafüzetek – JSON-adatátalakítás JSONPath](platform/workbooks-jsonpath.md) – új cikk.


## <a name="april-2020"></a>2020. április

### <a name="general"></a>Általános kérdések

- A [Azure monitor ügyfél által felügyelt kulcshoz](platform/customer-managed-keys.md) hozzáadott szakasz az aszinkron műveletekhez
- [Log Analytics munkaterületek kezelése Azure monitor](platform/manage-access.md) – frissített egyéni naplók szakaszban.

### <a name="alerts"></a>Riasztások

- [Azure monitor riasztásokra vonatkozó műveleti szabályok](platform/alerts-action-rules.md) – videó hozzáadva.
- [A riasztások és az értesítések monitorozásának áttekintése az Azure-ban](platform/alerts-overview.md) – videó hozzáadva.

### <a name="application-insights"></a>Application Insights

- [Az Azure Application Insights-ban elérhető alkalmazás-hozzárendelés](app/app-map.md) – a Java-ügynökhöz hozzáadott Felhőbeli szerepkörök nevei.
- [Azure Application Insights .net-ügynök API](app/status-monitor-v2-api-reference.md) -referenciája – összevont API-hivatkozás.
- Az [Application Insights és log Analytics által használt IP-címek](app/ip-addresses.md) az alkalmazás-elemzések és az log Analytics API-k, a műveleti csoport webhookok, az Azure USA kormánya számára.
- [Java-alkalmazások figyelése bárhol](app/java-standalone-config.md) – új cikk.
- [Java-alkalmazások figyelése bármilyen környezetben](app/java-in-process-agent.md) – új cikk.
- [Bármilyen környezetben futó Java-alkalmazások figyelése](app/java-standalone-arguments.md) – új cikk.
- [Helyszínen futó Java-alkalmazások figyelése](app/java-on-premises.md) – új cikk.
- [Application Insights eltávolítása a Visual Studióban](app/remove-application-insights.md) – új cikk.
- [Telemetria-mintavételezés az Azure Application Insightsban](app/sampling.md) – javítás a Pythonban rögzített sebességű mintában.

### <a name="containers"></a>Tárolók

- Az [Azure Red Hat OpenShift v4. x konfigurálása Azure monitor for containers szolgáltatással](insights/container-insights-azure-redhat4-setup.md) – új cikk.
- [ServiceNow-szinkronizálási problémák manuális javítása](platform/itsmc-resync-servicenow.md) – új cikk.
- [Az Azure és a Red Hat OpenShift v4-fürt figyelésének leállítása](insights/container-insights-optout-openshift-v4.md) – új cikk.
- [Az Azure Red Hat OpenShift v3-fürt figyelésének leállítása](insights/container-insights-optout-openshift-v3.md) – új cikk.
- [A hibrid Kubernetes-fürt figyelésének leállítása](insights/container-insights-optout-hybrid.md) – új cikk.

### <a name="insights"></a>Insights

- [Azure Key vaultok figyelése Azure monitor a Key vaultok számára (előzetes verzió)](insights/key-vault-insights-overview.md) – új cikk.

### <a name="logs"></a>Naplók

- [Azure monitor szolgáltatás korlátai](service-limits.md) – a felhasználói lekérdezések szabályozása.
- [Azure monitor naplók használatának és költségeinek kezelése](platform/manage-cost-storage.md) – a naplókhoz hozzáadott számlázás. Kusto-lekérdezés hozzáadva, amely lehetővé teszi az ügyfelek számára a csomópontok közötti örökölt díjszabási szintet annak megállapítására, hogy a felhasználóknak GB vagy Capacity foglalási szinten kell áttérniük.

### <a name="metrics"></a>Mérőszámok

- [Az Azure Metrikaböngésző](platform/metrics-charts.md) által hozzáadott összesítési szakasz speciális funkciói.

### <a name="workbooks"></a>Munkafüzetek

- [Azure monitor munkafüzetek és Azure Resource Manager sablonok](platform/workbooks-automate.md) – a munkafüzet-sablonok üzembe helyezéséhez hozzáadott Resource Manager-sablon.

## <a name="march-2020"></a>2020. március

### <a name="general"></a>Általános kérdések

- [Azure monitor áttekintése](overview.md) – Azure monitor áttekintő videó hozzáadva.
- [Azure monitor ügyfél által felügyelt kulcs konfigurációjának](platform/customer-managed-keys.md) általános frissítései.
- [Azure monitor adathivatkozás](/azure/azure-monitor/reference/) – új webhely.

### <a name="alerts"></a>Riasztások

- [Műveletnapló-riasztások létrehozása, megtekintése és kezelése Azure monitor – a](platform/alerts-activity-log.md) Resource Manager-sablonok további magyarázata.
- [Ismerje meg, hogyan működnek a metrikus riasztások Azure Monitorban.](platform/alerts-metric-overview.md) -Frissítve a kormányzati támogatáshoz.
- [Hibaelhárítás Azure monitor riasztások és értesítések](platform/alerts-troubleshoot.md) – új cikk.

### <a name="application-insights"></a>Application Insights

- [Azure Application Insights automatizálása a PowerShell](app/powershell.md) által hozzáadott ARMClient-példákkal.
- [Telemetria folyamatos exportálása a Application Insightsból](app/export-telemetry.md) – tábla hozzáadása az exportálási struktúra részleteivel.
- [Snapshot Debugger engedélyezése a .NET-alkalmazásokhoz Azure app Service](app/snapshot-debugger-appservice.md) – hozzáadott Resource Manager-sablon – példa.
- [Az Azure Application Insights-használat és-költségek kezelése](app/pricing.md) az adatkorláttal kapcsolatos riasztásokkal kapcsolatban.
- [Python-alkalmazások figyelése Azure monitor (előzetes verzió)](app/opencensus-python.md) – szabványos metrikák hozzáadása.
- [Forrás-hozzárendelési támogatás JavaScript-alkalmazásokhoz – Azure Monitor Application Insights](app/source-map-support.md) – új cikk.

### <a name="containers"></a>Tárolók

- [Azure monitor GYIK](faq.md) – Azure monitor tárolók frissítése.
- [GPU-figyelés konfigurálása a Azure monitor for containers](insights/container-insights-gpu-monitoring.md) – új cikk.

### <a name="insights"></a>Insights

- [Office 365 felügyeleti megoldás az Azure-ban](insights/solution-office-365.md) – frissített elavulás dátuma.

### <a name="logs"></a>Naplók

- A Azure Monitor által hozzáadott CPU-feltételhez tartozó [napló-lekérdezések optimalizálása](log-query/query-optimization.md) az XML-és JSON-elemzésekhez.
- [Az Azure log Analytics munkaterület törlése és helyreállítása](platform/delete-workspace.md) – hibaelhárítás.
- [Azure monitor naplók használata Azure Logic apps és energiagazdálkodási automatizálással](platform/logicapp-flow-connector.md) – frissítve az új Azure monitor-összekötőhöz.

### <a name="metrics"></a>Mérőszámok

- [A lemez metrikáinak elavulása a Azure Portal](platform/portal-disk-metrics-deprecation.md) új cikkben.
- [Oktatóanyag – mérőszámok diagramjának létrehozása Azure monitor](learn/tutorial-metrics-explorer.md) által hozzáadott videóban.

### <a name="platform-logs"></a>Platformnaplók

- [Összegyűjtheti és elemezheti az Azure-beli tevékenység naplóját Azure monitor](./platform/activity-log.md) – újraírhatja, hogy jobban megmagyarázza a begyűjtési tevékenység naplójának diagnosztikai beállításait.

### <a name="virtual-machines"></a>Virtuális gépek

- [Azure-beli virtuális gépek figyelése Azure monitor](insights/monitor-vm-azure.md) -új cikkel.
- Gyors útmutató: az Azure-beli [virtuális gépek Azure monitor](learn/quick-monitor-azure-vm.md) frissítéssel való figyelése Azure monitor for VMS hozzáadásához.
- [Riasztások Azure monitor for VMS](insights/vminsights-alerts.md) – új cikk.
- [Azure monitor for VMS áttekintés](insights/vminsights-enable-overview.md) – az ügynök letöltési hivatkozásainak frissítése.

Általánosan elérhető általános frissítések Azure Monitor for VMs

- [Mi az Azure Monitor for VMs?](insights/vminsights-overview.md)
- [Azure Monitor for VMs (GA) – gyakori kérdések](insights/vminsights-ga-release-faq.md) 
- [Azure Monitor for VMs engedélyezése Azure Policy használatával](insights/vminsights-enable-at-scale-policy.md) 
- [A teljesítmény diagram Azure Monitor for VMs](insights/vminsights-performance.md)
- [Naplók lekérdezése Azure Monitor for VMs](insights/vminsights-log-search.md)
- [Alkalmazás függőségeinek megtekintése Azure Monitor for VMs](insights/vminsights-maps.md) 

### <a name="visualizations"></a>Vizualizációk

- [Adatok megjelenítése Azure monitorról](visualizations.md) – frissítve a View Designer tervezett elavulása megjegyzéssel.

## <a name="february-2020"></a>2020. február

### <a name="agents"></a>Ügynökök

Több frissítés a diagnosztikai bővítmény tartalmának újraírása részeként.

- [Az Azure monitoring-ügynökök áttekintése](platform/agents-overview.md) – átstrukturált táblázatok az egyes ügynökök egyedi funkcióinak jobb tisztázásához.
- [Azure Diagnostics bővítmény áttekintése](platform/diagnostics-extension-overview.md) – teljes újraírás.
- A [blob Storage használata az IIS-hez és a Table Storage](platform/diagnostics-extension-logs.md) -hoz a frissítéshez és a tisztasághoz Azure monitor általános újraírással kapcsolatos eseményekhez.
- [Telepítse és konfigurálja a Windows Azure Diagnostics bővítményt (wad)](platform/diagnostics-extension-windows-install.md) – új cikk. 
- [Windows Diagnostics bővítmény sémája](platform/diagnostics-extension-schema-windows.md) – átrendezve.
- [Adatok küldése a Windows Azure Diagnostics bővítményből az azure Event Hubsba](platform/diagnostics-extension-stream-event-hubs.md) – teljes mértékben újraírható és frissítve.
- [Diagnosztikai adatgyűjtés tárolása és megtekintése az Azure Storage-ban](../cloud-services/diagnostics-extension-to-storage.md) – teljes mértékben újraírható és frissíthető.
- [Log Analytics virtuálisgép-bővítmény a Windowshoz](../virtual-machines/extensions/oms-windows.md) – jobban tisztázza log Analytics ügynökkel való kapcsolatot.
- [Azure monitor virtuálisgép-bővítmény a Linux](../virtual-machines/extensions/oms-linux.md) rendszerhez – jobban tisztázza log Analytics-ügynökkel való kapcsolatot.

### <a name="application-insights"></a>Application Insights

- [A kapcsolatok karakterláncai az Azure Application Insightsban](app/sdk-connection-string.md) – új cikk.

### <a name="insights-and-solutions"></a>Bepillantást és megoldásokat

#### <a name="azure-monitor-for-containers"></a>Azure Monitor tárolókhoz

- [Azure Active Directory integrálása az Azure Kubernetes szolgáltatással](../aks/azure-ad-integration-cli.md) – vegye figyelembe, hogy egy ügyfélalkalmazás létrehozásával támogatja a RBAC-kompatibilis fürtöt a tárolók Azure monitor támogatásához.

#### <a name="azure-monitor-for-vms"></a>Azure Monitor virtuális gépekhez

- [Azure monitor for VMS (GA) gyakori kérdések](insights/vminsights-ga-release-faq.md) – a teljesítményadatok tárolási módjának módosítása.

#### <a name="office-365"></a>Office 365

- [Office 365 felügyeleti megoldás az Azure-ban](insights/solution-office-365.md) – frissített elavulás dátuma.


### <a name="logs"></a>Naplók

- Azure Monitor-új cikkben [található naplók optimalizálása](log-query/query-optimization.md) .
- [Azure monitor naplók használatának és költségeinek kezelése](platform/manage-cost-storage.md) – továbbfejlesztett minta-lekérdezések, amelyek segítenek a használat megértésében.

### <a name="metrics"></a>Mérőszámok

- [Azure monitor a platform metrikái exportálhatók a diagnosztikai beállítások](platform/metrics-supported-export-diagnostic-settings.md) – hozzáadott szakasz a nullák és a nulla értékek viselkedésének változása esetén.

### <a name="visualizations"></a>Vizualizációk

Több új cikk a Designer for munkafüzetek átalakítási útmutatójában.

- [Azure monitor a Designer és a munkafüzetek közötti áttérési útmutató](platform/view-designer-conversion-overview.md) – új cikk.
- [Azure monitor tervező átalakítása munkafüzetekbe konvertálási beállítások](platform/view-designer-conversion-options.md) – új cikk.
- [Azure monitor megtekintheti a tervezőt munkafüzetekhez csempe-konverziók](platform/view-designer-conversion-tiles.md) – új cikk.
- [Azure monitor megtekintheti a tervezőt a munkafüzetek átalakításának összegzése és a hozzáférés](platform/view-designer-conversion-access.md) – új cikk alapján.
- [Azure monitor tervező átalakítása munkafüzetekhez gyakori feladatok konvertálása](platform/view-designer-conversion-tasks.md) – új cikk.
- [Azure monitor tervező átalakítása munkafüzetek átalakítására – példák](platform/view-designer-conversion-examples.md) – új cikk.

## <a name="january-2020"></a>2020. január

### <a name="general"></a>Általános kérdések

- [Mi figyeli a Azure Monitor?](monitor-reference.md) – Új cikk.

### <a name="agents"></a>Ügynökök

- [A naplófájlok adatainak összegyűjtése az Azure log Analytics agenttel – a](platform/log-analytics-agent.md) hálózati tűzfalra vonatkozó követelmények tábla frissítése.

### <a name="alerts"></a>Riasztások

- [Műveleti csoportok létrehozása és kezelése a Azure Portalban](platform/action-groups.md) – a már nem szükséges v2-függvények eltávolításának beállítása.
- [Metrikai riasztás létrehozása Resource Manager-sablonnal](platform/alerts-metric-create-templates.md) – példa a *ignoreDataBefore* paraméterhez.  A többszörös feltételi szabályokkal kapcsolatos korlátozásokat adott meg.
- [Log Analytics riasztás használata REST API](platform/api-alerts.md) – JSON-példa kijavítva.

### <a name="application-insights"></a>Application Insights

- Az [Application Insights által használt IP-címek és a log Analytics](app/ip-addresses.md) – a rendelkezésre állási teszt szakasz frissítése a bejövő portszabály hozzáadásával az Azure hálózati biztonsági csoportokat használó forgalom engedélyezéséhez.
- [Az Azure Application Insights Profiler kapcsolatos problémák elhárítása](app/profiler-troubleshooting.md) – frissített általános hibaelhárítás.
- [Telemetria mintavételezés az Azure Application Insightsban](app/sampling.md) – frissített és átstrukturált, hogy javítsa az olvashatóságot az ügyfelek visszajelzései alapján.

### <a name="data-security"></a>Adatbiztonság

- [Azure monitor ügyfél által felügyelt kulcs konfigurálása](platform/customer-managed-keys.md) – új cikk.

### <a name="insights-and-solutions"></a>Bepillantást és megoldásokat

#### <a name="azure-monitor-for-containers"></a>Azure Monitor tárolókhoz

- [Azure monitor konfigurálása a containers Agent adatgyűjtéshez](insights/container-insights-agent-config.md) – további részletek az ügynök frissítéséhez az Azure Red Hat OpenShift, és további információkkal bővült az ügynök verziófrissítésének módszerei.
- [Teljesítmény-riasztások létrehozása a tárolók Azure monitor számára](insights/container-insights-alerts.md) – felülvizsgált információk és frissített lépések a munkaterületen tárolt teljesítményadatok riasztásának létrehozásához munkaterületen belüli riasztások használatával.
- [Kubernetes-figyelés a Azure monitor for containers szolgáltatással](insights/container-insights-analyze.md) – a Windows Kubernetes-fürtök támogatásáról szóló áttekintő cikk és a elemzésre vonatkozó cikk frissítése.
- [Azure Red Hat OpenShift-fürtök konfigurálása az Azure monitor for containers](insights/container-insights-azure-redhat-setup.md) szolgáltatással – további részletek az ügynök frissítéséhez az Azure Red Hat OpenShift, és további információkkal bővült az ügynök verziófrissítésének módszerei.
- [Hibrid Kubernetes-fürtök konfigurálása Azure monitor for containers](insights/container-insights-hybrid-setup.md) használatával – frissítve a biztonságos port hozzáadásának támogatásával: a 10250 a Kubelet cAdvisor.
- [A containers agent Azure monitor kezelése – az](insights/container-insights-manage-agent.md) Azure Red Hat OpenShift és a metrikák leselejtezésének a viselkedésével és konfigurálásával kapcsolatos frissített részletek a más típusú Kubernetes-fürtökhöz képest.
- [Azure monitor konfigurálása a containers Prometheus-integrációhoz](insights/container-insights-prometheus-integration.md) – frissített részletek az Azure Red Hat OpenShift származó metrika-leselejtezési viselkedéssel és konfigurációval kapcsolatban, más típusú Kubernetes-fürtökhöz képest.
- A [tárolók Azure monitorának frissítése a metrikák számára](insights/container-insights-update-metrics.md) – frissített részletek az Azure Red Hat OpenShift a metrika-lekaparás működésével és konfigurálásával, valamint más típusú Kubernetes-fürtökhöz képest.

#### <a name="azure-monitor-for-vms"></a>Azure Monitor virtuális gépekhez

- [Azure monitor for VMS (GA) – gyakori kérdések](insights/vminsights-ga-release-faq.md) – további információk a munkaterület és az ügynökök új verzióra való frissítéséről.

#### <a name="office-365"></a>Office 365

- [Office 365 felügyeleti megoldás az Azure-ban](insights/solution-office-365.md) – további részletek és gyakori kérdések az Office 365-megoldás Azure sentinelben való áttelepítéséről. A bevezetési szakasz el lett távolítva.

### <a name="logs"></a>Naplók

- [Log Analytics munkaterületek kezelése Azure monitor](platform/manage-access.md) – nem műveletekre vonatkozó frissítések.
- [Azure monitor naplók használatának és költségeinek kezelése](platform/manage-cost-storage.md) – az adatmennyiség kiszámításának pontosítása a díjszabási modell szakaszban.
- [Azure Resource Manager-sablonokkal létrehozhat és konfigurálhat egy log Analytics-munkaterületet](platform/template-workspace-configuration.md) frissített sablont új díjszabási szintek használatával.

### <a name="platform-logs"></a>Platformnaplók

- [Az Azure-tevékenység naplójának összegyűjtése diagnosztikai beállításokkal – Azure monitor](./platform/activity-log.md) – további információ a módosult tulajdonságokkal kapcsolatban.
- [Exportálja az Azure-tevékenység naplóját](./platform/activity-log.md#legacy-collection-methods) – frissítve a felhasználói felület változásaihoz. 

## <a name="december-2019"></a>2019. december

### <a name="agents"></a>Ügynökök

- [Linux rendszerű számítógépek Összekapcsolásának Azure monitor](platform/agent-linux.md) – új cikk.

### <a name="alerts"></a>Riasztások

- [Metrikus riasztás létrehozása Resource Manager-sablonnal](platform/alerts-metric-create-templates.md) – példa az egyéni metrikára.
- Dinamikus [küszöbértékekkel rendelkező riasztások létrehozása Azure monitor](platform/alerts-dynamic-thresholds.md) – hozzáadott szakasz a dinamikus küszöbértékű diagramok értelmezéséhez.
- [A riasztások és az értesítések monitorozásának áttekintése az Azure-ban](platform/alerts-overview.md) – frissített Resource Graph-lekérdezés.
- A [metrikus riasztások támogatott erőforrásai Azure monitor](platform/alerts-metric-near-real-time.md) – a metrikák és a dimenziók frissítése támogatott.
- [Váltás az örökölt log Analytics riasztások API-ból az új Azure-riasztások API-ba](platform/alerts-log-api-switch.md) – Megjegyzés hozzáadva a módosított riasztás nevéhez.
- [Ismerje meg, hogyan működnek a metrikus riasztások Azure Monitorban.](platform/alerts-metric-overview.md) – Támogatott erőforrástípusok hozzáadása a nagy léptékű figyeléshez.

### <a name="application-insights"></a>Application Insights

- [Application Insights a Worker Service-alkalmazásokhoz (nem HTTP-alkalmazásokhoz)](app/worker-service.md) – az alapértelmezett naplózási szint hozzáadva a C#-kódhoz. Frissített csomag-hivatkozási verzió.
- [ApplicationInsights.config hivatkozás – Azure](app/configuration-with-applicationinsights-config.md) által frissített mintakód.
- [Az Azure Application Insights automatizálása a PowerShell](app/powershell.md) használatával – frissítés Resource Manager-sablonra.
- [Azure Monitor Application Insights NuGet-csomagok](app/nuget.md) – frissített csomag verziószáma.
- [Hozzon létre egy új Azure Application Insights-erőforrást](app/create-new-resource.md) , amely a globálisan egyedi névvel lett hozzáadva.
- [Diagnosztizálás élő metrikastream – Azure Application Insights](app/live-stream.md) – frissített ASP.net Core SDK-verzióra vonatkozó követelmény.
- [Application Insights](app/eventcounters.md) – frissített kategória és tábla customMetrics.
- [Ismerje meg a Java-nyomkövetési naplókat az Azure Application Insights](app/java-trace-logs.md) – a Java-ügynök naplózási küszöbértékének konfigurációját.
- Az [Application Insights által használt IP-címek és a log Analytics](app/ip-addresses.md) a élő METRIKASTREAM frissített IP-címei.
- Az [Azure app Services teljesítményének figyelése](app/azure-web-apps.md) – a ASP.net Core 3,0 támogatásának támogatása. 
- [Python-alkalmazások figyelése Azure monitor (előzetes verzió)](app/opencensus-python.md) – a OpenCensus Python-séma az Azure-ba való hozzárendelésének pontosítása. Séma figyelése
- [Kibocsátási megjegyzések az Azure Application Insightshoz](app/release-notes.md) – megjegyzések a régebbi kiadásokhoz.
- [Telemetria-csatornák Az Azure-ban Application Insights](app/telemetry-channels.md) – az elvetett adatmennyiségnek az elveszett kapcsolat hosszabb időszaka során történő frissített időtartama.
- [Telemetria mintavételezés az Azure Application Insightsban](app/sampling.md) – javított kódrészlet az egyéni TelemetryInitializer.
- A [Java-webprojektek Application Insightsának hibáinak megoldása](app/java-troubleshoot.md) – eltávolított utasítás, amely nem támogatja a függőségi gyűjteményt a JDK 9-ben.

### <a name="insights-and-solutions"></a>Bepillantást és megoldásokat

- [Azure monitor a tárolók gyakori kérdéseivel](./faq.md) kapcsolatban – kérdésekkel bővült a képek és a nevek mezői.
- [Azure SQL Analytics megoldás a Azure monitor](insights/azure-sql.md) -frissítve adatbázisban vár felügyelt példányok támogatása.
- [Azure monitor konfigurálása a containers Agent adatgyűjtéshez](insights/container-insights-agent-config.md) – hozzáadott beállítás a enrich_container_logshoz.
- [Hibrid Kubernetes-fürtök konfigurálása Azure monitor for containers](insights/container-insights-hybrid-setup.md) – hibaelhárítás szakasz.
- [Active Directory replikáció állapotának figyelése Azure monitor](insights/ad-replication-status.md) -.NET-keretrendszerre vonatkozó előfeltétel-frissítéssel.
- [Network Performance monitor megoldás az Azure-ban](insights/network-performance-monitor.md) – támogatott régiók.
- [A Active Directory-környezet optimalizálása Azure monitor](insights/ad-assessment.md) -.NET-keretrendszer előfeltételeinek frissítésével.
- [A SQL Server-környezet optimalizálása Azure monitor](insights/sql-assessment.md) -.NET-keretrendszer előfeltételeinek frissítésével.
- [Optimalizálja System Center Operations Manager környezetét az Azure log Analytics](insights/scom-assessment.md) -.NET-keretrendszer előfeltételeinek frissítésével.
- [Támogatott kapcsolatok az Azure Log Analytics it-szolgáltatásmenedzsmenti csatolóával](platform/itsmc-connections.md) – új York-i előfeltétel-ügyfél-azonosító és az ügyfél titkos kulcsa.

### <a name="logs"></a>Naplók

- [Az Azure log Analytics munkaterület törlése és helyreállítása](platform/delete-workspace.md) – PowerShell-metódus hozzáadva.
- [Megtervezheti a Azure monitor naplók üzembe helyezését](platform/design-logs-deployment.md) – a munkaterületek terhelési arányának növelését.

### <a name="metrics"></a>Mérőszámok

- [Azure monitor a platform metrikái exportálhatók a diagnosztikai beállítások használatával](platform/metrics-supported-export-diagnostic-settings.md) – új cikk.

### <a name="platform-logs"></a>Platformnaplók

Több cikk is frissült a tartalom átszervezésének részeként a platform naplóiban a diagnosztikai beállítások használatával a műveletnapló konfigurálására szolgáló új funkció alapján.

- [Azure-beli erőforrás-naplók archiválása a Storage-fiókba](./platform/resource-logs.md#send-to-azure-storage)
- [Azure Activity log esemény sémája](platform/activity-log-schema.md)
- [Azure Monitor szolgáltatási korlátok](service-limits.md)
- [Azure-beli tevékenység-naplók összegyűjtése és elemzése Log Analytics munkaterületen](./platform/activity-log.md)
- [Az Azure-tevékenység naplójának összegyűjtése diagnosztikai beállításokkal (előzetes verzió) – Azure Monitor](./platform/activity-log.md)
- [Azure-beli tevékenységek naplóinak begyűjtése egy Log Analytics munkaterületre az Azure-bérlők között](platform/activity-log-collect-tenants.md)
- [Azure-beli erőforrás-naplók gyűjtése Log Analytics munkaterületen](./platform/resource-logs.md#send-to-log-analytics-workspace)
- [Diagnosztikai beállítás létrehozása az Azure-ban Resource Manager-sablon használatával](platform/diagnostic-settings-template.md)
- [Diagnosztikai beállítás létrehozása naplók és metrikák gyűjtéséhez az Azure-ban](platform/diagnostic-settings.md)
- [Az Azure-tevékenység naplójának exportálása](./platform/activity-log.md#legacy-collection-methods)
- [Az Azure platform naplófájljainak áttekintése](platform/platform-logs-overview.md)
- [Azure monitoring-adatstreamek továbbítása az Event hub szolgáltatásba](platform/stream-monitoring-data-event-hubs.md)
- [Azure platform-naplók továbbítása egy Event hubhoz](./platform/resource-logs.md#send-to-azure-event-hubs)

### <a name="quickstarts-and-tutorials"></a>Rövid útmutatók és oktatóanyagok

- [Metrikai diagram létrehozása Azure monitor](learn/tutorial-metrics-explorer.md) – új cikkben.
- [Erőforrás-naplók összegyűjtése Azure-erőforrásokból és Azure monitor](learn/tutorial-resource-logs.md) -új cikk elemzése.
- [Azure-erőforrás figyelése Azure monitor](learn/quick-monitor-azure-resource.md) -új cikkel.
   
## <a name="next-steps"></a>Következő lépések

- Ha szeretne hozzájárulni Azure Monitor dokumentációhoz, tekintse meg a [docs közreműködői útmutatóját](/contribute/).
