---
title: Naplók az Azure Monitorban | Microsoft dokumentumok
description: Ismerteti az Azure Monitor naplóit, amelyek a figyelési adatok speciális elemzéséhez használatosak.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/26/2019
ms.author: bwren
ms.openlocfilehash: 0b288bf6d987b9db682c8d1439879cf6b499f213
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457331"
---
# <a name="logs-in-azure-monitor"></a>Naplók az Azure Monitorban

> [!NOTE]
> Az Azure Monitor által gyűjtött összes adat két alapvető típus, a metrikák és a naplók egyikébe illeszkedik. Ez a cikk a Naplók at ismerteti. Tekintse meg [a metrikák az Azure Monitor](data-platform-metrics.md) részletes leírását metrikák és az Azure Monitor által gyűjtött adatok [figyelése](data-platform.md) a kettő összehasonlítása.

Az Azure Monitor naplói különösen hasznosak a különböző forrásokból származó adatok összetett elemzéséhez. Ez a cikk ismerteti, hogyan naplók vannak felépítve az Azure Monitor, mit tehet az adatokkal, és azonosítja a különböző adatforrások, amelyek adatokat tárolnak a naplókban.

> [!NOTE]
> Fontos megkülönböztetni az Azure Monitor naplók és az Azure-beli naplóadatok forrásait. Például az Azure-beli előfizetési szintű események egy [tevékenységnaplóba](platform-logs-overview.md) kerülnek, amelyet az Azure Monitor menüből tekinthet meg. A legtöbb erőforrás működési adatokat ír egy [erőforrásnaplóba,](platform-logs-overview.md) amelyet különböző helyekre továbbíthat. Az Azure Monitor Logs egy naplóadat-platform, amely tevékenységnaplókat és erőforrásnaplókat, valamint más figyelési adatokat gyűjt, hogy mélyreható elemzést nyújtson az erőforrások teljes készletében.

## <a name="what-are-azure-monitor-logs"></a>Mik azok az Azure Monitor-naplók?

Az Azure Monitor ban naplók különböző típusú adatokat tartalmaznak rekordokba különböző tulajdonságkészletek az egyes típusok. A naplók numerikus értékeket is tartalmazhatnak, például az Azure Monitor metrikákat, de általában részletes leírásokat tartalmazó szöveges adatokat tartalmaznak. A metrikaadatoktól abban különböznek, hogy szerkezetük ben változnak, és gyakran nem rendszeres időközönként gyűjtik őket. Telemetriai adatok, például események és nyomkövetések tárolják az Azure Monitor naplók mellett a teljesítményadatokat, hogy az összes kombinálható elemzéscéljából.

A naplóbejegyzés általános típusa egy esemény, amely et szórványosan gyűjti össze. Az eseményeket egy alkalmazás vagy szolgáltatás hozta létre, és általában elegendő információt tartalmaznak ahhoz, hogy saját maguk is teljes környezetet biztosítsanak. Egy esemény például azt jelezheti, hogy egy adott erőforrást hoztak létre vagy módosítottak, egy új állomást indítottak el a megnövekedett forgalom ra válaszul, vagy hibát észleltek egy alkalmazásban.

 Mivel az adatok formátuma eltérő lehet, az alkalmazások egyéni naplókat hozhatnak létre a szükséges struktúra használatával. Metrika adatok is tárolhatók naplók kombinálni más figyelési adatok trend és egyéb adatok elemzése.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Mire használható az Azure Monitor-naplók?
Az alábbi táblázat felsorolja a naplók azure-figyelőben használható különböző módokon.


|  |  |
|:---|:---|
| Elemzés | Az Azure [Portalon](../log-query/get-started-portal.md) található Log Analytics használatával [naplólekérdezéseket](../log-query/log-query-overview.md) írhat, és interaktívan elemezheti a naplóadatokat a hatékony Adatkezelő-elemző motor használatával.<br>Az [Azure Insights-elemzési konzol](../app/analytics.md) az Azure Portalon naplólekérdezések írása és interaktív analizátor az Application Insights-ból származó naplóadatok interaktív elemzéséhez. |
| Vizualizáció | Rögzítse a táblákként vagy diagramokként megjelenített lekérdezési eredményeket egy [Azure-irányítópulton.](../../azure-portal/azure-portal-dashboards.md)<br>Hozzon létre [egy munkafüzetet,](../app/usage-workbooks.md) amelyet interaktív jelentésben több adathalmazsal kombinálhat. <br>A lekérdezés eredményeit exportálja a [Power BI-ba,](powerbi.md) hogy különböző vizualizációkat használjon, és az Azure-on kívüli felhasználókkal is megoszthassa azokat.<br>Exportálja a lekérdezés eredményeit a [Grafanába,](grafana-plugin.md) hogy kihasználja az irányítópultok és más adatforrásokkal való kombinálását.|
| Riasztás | Állítson be egy [naplóriasztási szabályt,](alerts-log.md) amely értesítést küld, vagy [automatikus műveletet](action-groups.md) tesz meg, ha a lekérdezés eredménye egy adott eredménynek megfelel.<br>[Metrikariasztási szabály](alerts-metric-logs.md) konfigurálása bizonyos naplóadat-naplók metrikákként kinyert. |
| Elhoz | Az [Azure CLI](/cli/azure/ext/log-analytics/monitor/log-analytics)parancssorból származó naplólekérdezési eredmények elérése.<br>A naplólekérdezés eredményeinek elérése parancssorból [PowerShell-parancsmagok](https://docs.microsoft.com/powershell/module/az.operationalinsights)használatával.<br>A [REST API](https://dev.loganalytics.io/)használatával egyéni alkalmazás naplólekérdezési eredményeinek elérése. |
| Exportálás | Munkafolyamatlétrehozása a naplóadatok lekéréséhez és külső helyre másolásához [a Logic Apps](~/articles/logic-apps/index.yml)használatával. |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>Hogyan épülnek fel az adatok az Azure Monitor-naplókban?
Az Azure Monitor naplók által gyűjtött adatokat egy [Log Analytics-munkaterület](../platform/design-logs-deployment.md)tárolja. Minden munkaterület több táblát tartalmaz, amelyek mindegyike egy adott forrásból származó adatokat tárol. Bár minden tábla közös [tulajdonságokkal](log-standard-properties.md)rendelkezik, mindegyik egyedi tulajdonságkészlettel rendelkezik az általa használt adatok típusától függően. Az új munkaterület szabványos táblákkal fog rendelkezni, és további táblákat adhozzá a különböző figyelési megoldások és más szolgáltatások, amelyek a munkaterületre írnak.

Az Application Insights adatainak naplózása ugyanazt a Log Analytics-motort használja, mint a munkaterületek, de az egyes figyelt alkalmazások külön-külön tárolják. Minden alkalmazás rendelkezik egy szabványos táblakészlettel az adatok, például az alkalmazáskérelmek, kivételek és oldalnézetek tárolására.

A naplólekérdezések vagy egy Log Analytics-munkaterületről vagy egy Application Insights-alkalmazásból származó adatokat használnak. Az erőforrások [közötti lekérdezéssel](../log-query/cross-workspace-query.md) elemezheti az alkalmazásadatokat más naplóadatokkal együtt, vagy lekérdezéseket hozhat létre, beleértve több munkaterületet vagy alkalmazást is.

![Munkaterületek](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>Naplólekérdezések
Az Azure Monitor naplók ban lévő adatok a [Kusto lekérdezési nyelvvel](../log-query/get-started-queries.md)írt [naplólekérdezés](../log-query/log-query-overview.md) használatával történik, amely lehetővé teszi az összegyűjtött adatok gyors beolvasását, összesítését és elemzését. A [Log Analytics](../log-query/portals.md) használatával naplólekérdezéseket írhat és vethet az Azure Portalon. Lehetővé teszi, hogy interaktív módon dolgozzon az eredményekkel, vagy rögzítse őket egy irányítópulton, hogy más vizualizációkkal megtekinthesse őket.

![Log Analytics](media/data-platform-logs/log-analytics.png)

Nyissa [meg a Log Analytics-et az Application Insights ból](../app/analytics.md) az Application Insights-adatok elemzéséhez.

![Application Insights-statisztika](media/data-platform-logs/app-insights-analytics.png)

A naplóadatokat a [Log Analytics API](https://dev.loganalytics.io/documentation/overview) és az Application Insights REST [API](https://dev.applicationinsights.io/documentation/overview)használatával is lekérheti.


## <a name="sources-of-azure-monitor-logs"></a>Az Azure Figyelő naplók forrásai
Az Azure Monitor különböző forrásokból gyűjthet naplóadatokat az Azure-on belül és a helyszíni erőforrásokból is. Az alábbi táblázatok az Azure Monitor naplókba adatokat író különböző erőforrásokból elérhető különböző adatforrásokat sorolják fel. Mindegyik rendelkezik egy hivatkozást a szükséges konfiguráció részleteire.

### <a name="azure-tenant-and-subscription"></a>Azure-bérlő és előfizetés

| Adatok | Leírás |
|:---|:---|
| Az Azure Active Directory naplózási naplói | Az egyes könyvtárak diagnosztikai beállításai val konfigurálva. Lásd: [Az Azure AD-naplók integrálása az Azure Monitor-naplókkal.](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)  |
| Tevékenységnaplók | Alapértelmezés szerint külön tárolva, és közel valós idejű riasztásokhoz használható. Telepítse a Tevékenységnapló-elemzési megoldást a Log Analytics-munkaterületre való íráshoz. Lásd: [Azure-tevékenységnaplók gyűjtése és elemzése a Log Analytics szolgáltatásban.](activity-log-collect.md) |

### <a name="azure-resources"></a>Azure-erőforrások

| Adatok | Leírás |
|:---|:---|
| Erőforrás-diagnosztika | Diagnosztikai beállítások konfigurálása a diagnosztikai adatokba való íráshoz, beleértve a log analytics-munkaterületre vonatkozó metrikákat is. Lásd: [Stream Azure-erőforrásnaplók a Log Analytics szolgáltatásba.](resource-logs-collect-workspace.md) |
| Monitorozási megoldások | A figyelési megoldások a Log Analytics-munkaterületükre írják az összegyűjtött adatokat. A megoldások listájáért tekintse meg [az Azure-beli felügyeleti megoldások adatgyűjtési részleteit.](../insights/solutions-inventory.md) A megoldások telepítésével és használatával kapcsolatos részletekért tekintse meg [az Azure Monitor figyelési megoldásainak](../insights/solutions.md) figyelését. |
| Mérőszámok | Az Azure Monitor-erőforrások platformmetrikáinak küldése a Log Analytics-munkaterületre a naplóadatok hosszabb ideig történő megőrzéséhez és a [Kusto lekérdezési nyelv](/azure/kusto/query/)használatával más adattípusokkal végzett összetett elemzés elvégzéséhez. Lásd: [Stream Azure-erőforrásnaplók a Log Analytics szolgáltatásba.](resource-logs-collect-storage.md) |
| Azure Table Storage | Adatokgyűjtése az Azure storage-ból, ahol egyes Azure-erőforrások figyelési adatokat írnak. Lásd: [Azure blob storage használata az IIS és az Azure table storage események log analytics.](diagnostics-extension-logs.md) |

### <a name="virtual-machines"></a>Virtuális gépek

| Adatok | Leírás |
|:---|:---|
|  Ügynökök adatforrásai | A [Windows](agent-windows.md) és [Linux](../learn/quick-collect-linux-computer.md) ügynököktől gyűjtött adatforrások eseményeket, teljesítményadatokat és egyéni naplókat tartalmaznak. Tekintse meg [az Ügynök adatforrások az Azure Monitor](data-sources.md) ban az adatforrások listáját és a konfiguráció részleteit. |
| Monitorozási megoldások | A figyelési megoldások az ügynököktől a Log Analytics-munkaterületükre gyűjtött adatokat írják. A megoldások listájáért tekintse meg [az Azure-beli felügyeleti megoldások adatgyűjtési részleteit.](../insights/solutions-inventory.md) A megoldások telepítésével és használatával kapcsolatos részletekért tekintse meg [az Azure Monitor figyelési megoldásainak](../insights/solutions.md) figyelését. |
| System Center Operations Manager | Az Operations Manager felügyeleti csoportjához csatlakoztatva az Azure Monitorhoz, hogy a helyszíni ügynököktől esemény- és teljesítményadatokat gyűjtsön a naplókba. Ebben a konfigurációban a [Connect Operations Manager to Log Analytics(Csatlakoztatása a Log Analyticsszolgáltatáshoz)](om-agents.md) témakörben talál. |


### <a name="applications"></a>Alkalmazások

| Adatok | Leírás |
|:---|:---|
| Kérések és kivételek | Az alkalmazáskérelmekre és -kivételekre vonatkozó részletes adatok a _kérelmekben_, _a lapnézetekben_és a _kivételek_ tábláiban találhatók. A [külső összetevők hívásai](../app/asp-net-dependencies.md) a _függőségek_ táblában találhatók. |
| Használat és teljesítmény | Az alkalmazás teljesítménye a _kérelmekben_, _böngészőkidőzítések_ és _performanceCounters_ táblákban érhető el. Az [egyéni metrikák](../app/api-custom-events-metrics.md#trackevent) adatai az _egyéniMetrikák_ táblázatban vannak.|
| Adatok nyomon követése | Az [elosztott nyomkövetés](../app/distributed-tracing.md) ből származó eredmények a _nyomkövetési_ táblázatban tárolódnak. |
| Rendelkezésre állási tesztek | A [rendelkezésre állási tesztekből](../app/monitor-web-app-availability.md) származó összesítő adatok az _availabilityResults_ táblában tárolódnak. Ezekből a tesztekből származó részletes adatok külön tárolóban találhatók, és az Azure-portálon az Application Insightsból érhetők el. |

### <a name="insights"></a>Insights

| Adatok | Leírás |
|:---|:---|
| Azure Monitor tárolókhoz | Az Azure Monitor által a [tárolókhoz](../insights/container-insights-overview.md)gyűjtött leltár- és teljesítményadatok. A táblák listáját a [Tároló adatgyűjtési részletei című témakörben](../insights/container-insights-log-search.md#container-records) találja. |
| Azure Monitor virtuális gépekhez | Az Azure Monitor virtuális gépekhez való [leképezési](../insights/vminsights-overview.md)és teljesítményadatait. Az adatok lekérdezésével kapcsolatos részletekért olvassa el A naplók lekérdezése az [Azure Monitor virtuális gépekhez.](../insights/vminsights-log-search.md) |

### <a name="custom"></a>Egyéni 

| Adatok | Leírás |
|:---|:---|
| REST API | Adatok írása a Log Analytics munkaterület bármely REST-ügyfél. A részleteket az [Azure-adatgyűjtő API-val naplóadatok küldése az Azure-figyelőnek](data-collector-api.md) című témakörben találja.
| Logikai alkalmazás | Az **Azure Log Analytics-adatgyűjtő-művelettel** bármilyen adatot írhat egy Log Analytics-munkaterületre egy logikai alkalmazás munkafolyamatából. |

### <a name="security"></a>Biztonság

| Adatok | Leírás |
|:---|:---|
| Azure Security Center | [Az Azure Security Center](/azure/security-center/) tárolja az adatokat, amelyeket gyűjt a Log Analytics munkaterület, ahol elemezhető más naplóadatokkal. A munkaterület konfigurációjának részleteiről az [Azure Security Center adatgyűjtése.](../../security-center/security-center-enable-data-collection.md) |
| Azure Sentinel | [Az Azure Sentinel](/azure/sentinel/) az adatforrásokból származó adatokat egy Log Analytics-munkaterületre tárolja. Lásd: [Adatforrások csatlakoztatása](/azure/sentinel/connect-data-sources).  |


## <a name="next-steps"></a>További lépések

- További információ az [Azure Monitor adatplatformjáról.](data-platform.md)
- Ismerje meg [a metrikák at az Azure Monitorban.](data-platform-metrics.md)
- Ismerje meg a [figyelési adatok at az](data-sources.md) Azure-ban rendelkezésre álló különböző erőforrások.
