---
title: Naplók az Azure Monitor |} A Microsoft Docs
description: Az Azure Monitor-naplók figyelési adatok speciális elemzésekre szolgáló használt ismerteti.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: monitoring
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 0203/26/2019
ms.author: bwren
ms.openlocfilehash: 59213c5391b5b652eeead05c4a5af761571fcece
ms.sourcegitcommit: 43b85f28abcacf30c59ae64725eecaa3b7eb561a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59360625"
---
# <a name="logs-in-azure-monitor"></a>Naplók az Azure Monitorban

> [!NOTE]
> Az Azure Monitor által gyűjtött összes adat megfelel a két alapvető típus, metrikák és naplók egyikébe. Ez a cikk ismerteti a naplókat. Tekintse meg [metrikák az Azure monitorban](data-platform-metrics.md) metrikák és a egy részletes leírása a [figyelési adatokat gyűjtött az Azure Monitor](data-platform.md) a két összehasonlítását.

Az Azure Monitor-naplók különösen hasznosak különböző forrásokból származó adatok közötti összetett elemzés végrehajtásához. Ez a cikk bemutatja, hogyan struktúrája az Azure Monitor, mire képes az adatokat, a naplókat, és azonosítja a különböző adatforrások, amelyek az adatok tárolása a naplók.

> [!NOTE]
> Fontos, az Azure Monitor naplóira és források az Azure-ban naplóadatok megkülönböztetésére. Előfizetés hibaszintű eseményeket az Azure-ban írt, például egy [tevékenységnapló](activity-logs-overview.md) , amely az Azure Monitor menüből is megtekintheti. A legtöbb erőforrást fog kiírni, hogy működési információkat egy [diagnosztikai napló](diagnostic-logs-overview.md) , amelyek különböző helyen is továbbíthatja. Az Azure Monitor naplóira egy log adatplatform, amely összegyűjti a tevékenységnaplókat, és diagnosztikai naplókat és más figyelési adatokhoz biztosítsa a teljes erőforráskészletét lecserélhetik részletes elemzést.

## <a name="what-are-azure-monitor-logs"></a>Mik az Azure Monitor naplóira?

Az Azure Monitor-naplók tartalmaznak különböző típusú adatokkal rendelkező különböző tulajdonságokat az egyes rekordok vannak rendezve. Naplók például az Azure Monitor-metrikák numerikus értékeket tartalmaz, de rendszerint a szöveges adatok részletes leírását tartalmazza. További eltéréseikről metrikaadatokat, hogy azok szerkezetét eltérő lehet, és gyakran nem gyűjtött rendszeres időközönként. Telemetriai adatokat – például az események és nyomok tárolja az Azure Monitor naplóira mellett ügynökteljesítmény-adatokat, hogy azt is, hogy az összes kombinálhatók elemzés céljából.

A naplóbejegyzés gyakori típus egy esemény, amely időnként gyűjtött. Egy alkalmazás vagy szolgáltatás által létrehozott eseményeket, és általában a teljes kontextusba saját elegendő információt tartalmaznak. Például az esemény azt jelzi, hogy egy adott erőforrás létrejött, és módosíthatók, megnövekedett forgalmának indított új gazdagépre, vagy hiba történt az alkalmazások.

 Mivel az adatok formátumát változhat, alkalmazások segítségével hozhat létre egyéni naplók szükségük van a struktúrát. Metrikaadatok akár kombinálhatja őket más figyelési adatokhoz trendelemzés és egyéb adatok elemzése a naplók tárolhatók.


## <a name="what-can-you-do-with-azure-monitor-logs"></a>Felhasználási lehetőségei az Azure Monitor naplóira?
Az alábbi táblázat a különböző módon, hogy használhatja az Azure monitorban naplókat.


|  |  |
|:---|:---|
| Elemzés | Használat [Log Analytics](../log-query/get-started-portal.md) írni az Azure Portalon [lekérdezések naplózását](../log-query/log-query-overview.md) és interaktív módon elemezheti a naplófájlok adatait az adatkezelő hatékony elemzési motor használatával.<br>Használja a [Application Insights analytics konzoljának](../app/analytics.md) log lekérdezéseket írni, és interaktív módon elemezheti a naplófájlok adatait az Application Insights az Azure Portalon. |
| Vizualizáció | Lekérdezési eredmények jelenik meg a táblázatokat vagy diagramokat való rögzítése egy [Azure irányítópultján](../../azure-portal/azure-portal-dashboards.md).<br>Hozzon létre egy [munkafüzet](../app/usage-workbooks.md) egy interaktív jelentésben szereplő adatok több példányban egyesítenie. <br>A lekérdezés eredményeinek exportálására [Power BI](powerbi.md) különböző Vizualizációk használata és megosztása Azure-on kívüli felhasználókkal.<br>A lekérdezés eredményeinek exportálására [Grafana](grafana-plugin.md) annak dashboarding használatát, és más adatforrásokkal együtt.|
| Riasztás | Konfigurálja egy [riasztási szabály](alerts-log.md) , amely elküld egy értesítést vagy veszi [művelet automatikus](action-groups.md) mikor a a lekérdezés eredménye megfelel-e egy adott eredményt.<br>Konfigurálja a [a metrikaalapú riasztási szabály](alerts-metric-logs.md) az egyes log adatnaplók mérőszámokként ki kell olvasni. |
| Lekérés | A parancssor használatával a lekérdezés eredményeihez elérését [Azure CLI-vel](/cli/azure/ext/log-analytics/monitor/log-analytics).<br>A parancssor használatával a lekérdezés eredményeihez elérését [PowerShell-parancsmagok](https://docs.microsoft.com/powershell/module/az.operationalinsights).<br>Egy egyéni alkalmazást a lekérdezés eredményeihez elérését [REST API-val](https://dev.loganalytics.io/). |
| Exportálás | Naplóadatok lekérése, és másolja azt egy külső helyre a munkafolyamat létrehozása [Logic Apps](~/articles/logic-apps/index.yml). |


## <a name="how-is-data-in-azure-monitor-logs-structured"></a>Hogyan alakul az Azure Monitor naplóira strukturált adatok?
Az Azure Monitor naplóira által gyűjtött adatokat tárolja egy [Log Analytics-munkaterület](../platform/manage-access.md). Is [több munkaterületet létrehozni](manage-access.md#determine-the-number-of-workspaces-you-need) más-más részhalmazához Teljesítménynapló-adatok kezeléséhez az előfizetésben. Az egyes munkaterületeken, hogy minden egyes tárolja-e egy adott adatforrásból származó több táblát tartalmaz. Amíg az összes tábla megosztása [néhány gyakori tulajdonságok](log-standard-properties.md), minden tárolt adatok típusától függően egy egyedi tulajdonságkészlettel rendelkezik. Egy új munkaterületet fog rendelkezni a táblák szabványos készletét, és több tábla hozzáadódik a különböző figyelési megoldások és más szolgáltatások, amelyek a munkaterület írni.

Az Application Insights naplóadatait ugyanazt a Log Analytics motort használja, mint a munkaterületek, de minden figyelt alkalmazáshoz külön tárolja. Minden alkalmazás rendelkezik egy standard táblákat, amely tárolja az adatok, például az alkalmazás kérelmeket, kivételeket és lapmegtekintéseket.

Naplólekérdezések lesz a Log Analytics-munkaterület használati adatait, vagy egy Application Insights alkalmazást. Használhat egy [erőforrások közötti lekérdezési](../log-query/cross-workspace-query.md) alkalmazás adatok és más naplózási adatok elemzéséhez, vagy a lekérdezések, beleértve a több munkaterületek vagy alkalmazások létrehozása.

![Munkaterületek](media/data-platform-logs/workspaces.png)

## <a name="log-queries"></a>Naplólekérdezések
Az Azure Monitor naplóira adatok lekérésére használatával egy [naplólekérdezés](../log-query/log-query-overview.md) -készült a [Kusto-lekérdezés nyelvi](../log-query/get-started-queries.md), amely lehetővé teszi, hogy gyorsan lekérni, konszolidálhatja és elemezheti az összegyűjtött adatokat. Használat [Log Analytics](../log-query/portals.md) fejlesztésére és tesztelésére log lekérdezések az Azure Portalon. Lehetővé teszi, hogy az eredmények interaktív használatához, vagy egy irányítópultot, megtekintheti őket más vizualizációkat rögzítheti őket.

![Log Analytics](media/data-platform-logs/log-analytics.png)

Nyissa meg [az Application Insights szolgáltatásból a Log Analytics](../app/analytics.md) Application Insights-adatok elemzéséhez.

![Application Insights-elemzési](media/data-platform-logs/app-insights-analytics.png)

Naplóadatok használatával is lekérhet a [Log Analytics API](https://dev.loganalytics.io/documentation/overview) és a [Application Insights REST API](https://dev.applicationinsights.io/documentation/overview).


## <a name="sources-of-azure-monitor-logs"></a>Azure Monitor naplóira forrásai
Az Azure Monitor naplóadatokat gyűjthet, különböző forrásokból is Azure-ban és a helyszíni erőforrásokhoz. Az alábbi táblázatok sorolják fel a különböző adatforrások különböző erőforrásokból, amely adatokat írni az Azure Monitor naplóira érhető el. Az összes szükséges konfigurálási részleteket mutató hivatkozás mindegyik rendelkezik.

### <a name="azure-tenant-and-subscription"></a>Azure-bérlőt és -előfizetés

| Adatok | Leírás |
|:---|:---|
| Az Azure Active Directory-naplók | Minden könyvtár diagnosztikai beállítások segítségével konfigurálható. Lásd: [integrálás az Azure AD-naplók az Azure Monitor naplóira](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).  |
| Tevékenységnaplók | Alapértelmezés szerint külön-külön tárolja, és közel valós idejű riasztásokhoz használható. Telepítse az Activity Log Analytics megoldás írni a Log Analytics-munkaterületet. Lásd: [összegyűjteni és elemezni az Azure-Tevékenységnaplók Log Analytics](collect-activity-logs.md). |

### <a name="azure-resources"></a>Azure-erőforrások

| Adatok | Leírás |
|:---|:---|
| Erőforrás-diagnosztika | Diagnosztikai adatok, beleértve a Log Analytics-munkaterület mérőszámok írni diagnosztikai beállításainak konfigurálása. Lásd: [a Log Analytics az Azure diagnosztikai naplók Stream](diagnostic-logs-stream-log-store.md). |
| Monitorozási megoldások | Figyelési megoldások, a Log Analytics-munkaterületen gyűjtött adatok írása. Lásd: [felügyeleti megoldások az Azure-beli adatok gyűjtemény adatait](../insights/solutions-inventory.md) megoldások listáját. Lásd: [figyelési megoldások az Azure monitorban](../insights/solutions.md) telepítése és a megoldásokkal kapcsolatos részletekért. |
| Mérőszámok | Az Azure Monitor erőforrások platform mérőszámok küldése a hosszabb ideig megőrzi a napló adatokat, és összetett elemzéseket végezhet egyéb adattípusok használata a Log Analytics-munkaterület az a [Kusto-lekérdezés nyelvi](/azure/kusto/query/). Lásd: [a Log Analytics az Azure diagnosztikai naplók Stream](diagnostic-logs-stream-log-store.md). |
| Azure Table Storage | Adatok gyűjtése az Azure storage-ból, ahol az egyes Azure-erőforrások írási figyelési adatok. Lásd: [az IIS és az Azure table storage-események Log Analytics szolgáltatással az Azure blob storage](azure-storage-iis-table.md). |

### <a name="virtual-machines"></a>Virtuális gépek

| Adatok | Leírás |
|:---|:---|
|  Ügynökök adatforrásai | Adatforrások gyűjtött [Windows](agent-windows.md) és [Linux](../learn/quick-collect-linux-computer.md) ügynökök események, teljesítményadatok és egyéni naplók tartalmaznak. Lásd: [ügynök adatforrások az Azure monitorban](data-sources.md) adatforrások és konfigurációs információt listáját. |
| Monitorozási megoldások | Figyelési megoldások ír adatokat, azok gyűjteni az ügynökök a Log Analytics-munkaterületet. Lásd: [felügyeleti megoldások az Azure-beli adatok gyűjtemény adatait](../insights/solutions-inventory.md) megoldások listáját. Lásd: [figyelési megoldások az Azure monitorban](../insights/solutions.md) telepítése és a megoldásokkal kapcsolatos részletekért. |
| System Center Operations Manager | Az Operations Manager felügyeleti csoport csatlakozni az Azure Monitor az esemény-és teljesítményadatokat gyűjteni a helyi ügynökök naplókat. Lásd: [Operations Manager csatlakoztatása a Log Analytics](om-agents.md) kapcsolatos részleteket ebben a konfigurációban. |


### <a name="applications"></a>Alkalmazások

| Adatok | Leírás |
|:---|:---|
| Kérések és kivételek | Alkalmazásokra irányuló kérések és kivételek részletes adatait a rendszer a _kérelmek_, _Oldalmegtekintések_, és _kivételek_ táblákat. Hívások [külső összetevők](../app/asp-net-dependencies.md) szerepelnek a _függőségek_ tábla. |
| Használat és a teljesítmény | Az alkalmazás teljesítmény érhető el a _kérelmek_, _browserTimings_ és _performanceCounters_ táblákat. Az adatok [egyéni metrikákat](../app/api-custom-events-metrics.md#trackevent) szerepel a _customMetrics_ tábla.|
| A nyomkövetési adatok | Való [elosztott nyomkövetési](../app/distributed-tracing.md) vannak tárolva a _nyomkövetések_ tábla. |
| Rendelkezésre állási tesztek | Az összefoglaló adatokat [rendelkezésre állási tesztek](../app/monitor-web-app-availability.md) tárolja a _availabilityResults_ tábla. Ezek a tesztek részletes adatait külön storage és Application Insights az Azure Portalon elérhető. |

### <a name="insights"></a>Insights

| Adatok | Leírás |
|:---|:---|
| Az Azure Monitor for containers szolgáltatásban | Leltár-és teljesítményadatok által gyűjtött [-tárolókhoz az Azure Monitor](../insights/container-insights-overview.md). Lásd: [tároló adatgyűjtési részletei](../insights/container-insights-analyze.md#container-data-collection-details) a táblák listáját. |
| Azure Monitor virtuális gépekhez | Térkép-és teljesítményadatok által gyűjtött [-beli virtuális gépek az Azure Monitor](../insights/vminsights-overview.md). Lásd: [naplók az Azure Monitor lekérdezni a virtuális gépek](../insights/vminsights-log-search.md) kapcsolatos részleteket az adatok lekérdezéséhez. |

### <a name="custom"></a>Egyéni 

| Adatok | Leírás |
|:---|:---|
| REST API | Írhat adatokat a Log Analytics-munkaterület bármely REST-ügyféllel. Lásd: [küldési naplóadatokat az Azure monitornak a HTTP-adatgyűjtő API-](data-collector-api.md) részleteiről.
| Logikai alkalmazás | Log Analytics-munkaterület az a logikai alkalmazás munkafolyamat bármely adatokat írni az **Azure Log Analytics Data Collector** művelet. |

### <a name="security"></a>Biztonság

| Adatok | Leírás |
|:---|:---|
| Azure Security Center | [Az Azure Security Center](/azure/security-center/) tárolja az adatokat a Log Analytics-munkaterület, ahol azok elemezhetők más naplózott adatokat összegyűjtő. Lásd: [az adatgyűjtést az Azure Security Centerben](../../security-center/security-center-enable-data-collection.md) munkaterület-konfiguráció részleteiért. |
| Azure Sentinel | [Az Azure Sentinel](/azure/sentinel/) adatforrásokból származó adatokat a Log Analytics-munkaterületen tárolja. Lásd: [adatforrások csatlakoztatása](/azure/sentinel/connect-data-sources).  |


## <a name="next-steps"></a>További lépések

- Tudjon meg többet a [Azure Monitor adatplatform](data-platform.md).
- Ismerje meg [metrikák az Azure monitorban](data-platform-metrics.md).
- További információ a [monitorozási adatok elérhető](data-sources.md) a különböző erőforrásokat az Azure-ban.
