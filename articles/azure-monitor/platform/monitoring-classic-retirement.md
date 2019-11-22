---
title: A klasszikus riasztások & figyelésének frissítése Azure Monitor
description: A klasszikus figyelési szolgáltatások és funkciók kivonásának leírása, amely korábban a riasztások (klasszikus) alatt Azure Portal látható.
author: yanivlavi
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 2/7/2019
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: f4639d46a796924b4fe1dcbb46735abea128388f
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74307088"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Az egyesített riasztási & figyelése Azure Monitor helyettesíti a klasszikus riasztási & figyelését

Azure Monitor mostantól egy egységes, teljes verem-figyelési szolgáltatás lesz, amely mostantól támogatja az "egy metrika" és az "egy riasztások" használatát az erőforrások között. További információkért tekintse meg az [új Azure monitor blogbejegyzését](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/). Az új Azure monitoring-és riasztási platformok gyorsabb, intelligensebb és bővíthetők, így folyamatosan bővülnek a Felhőbeli számítástechnika és a Microsoft intelligens felhőalapú filozófiája. 

Az Azure-beli új monitorozási és riasztási platformmal az Azure-riasztások *klasszikus riasztások* című szakaszában található "klasszikus" figyelési és riasztási platformot fogjuk kivonásra, amely az **Azure-beli nyilvános felhőkben augusztus 2019**-én megszűnik. [Azure Government a felhő](../../azure-government/documentation-government-welcome.md) és az [Azure China 21Vianet](https://docs.azure.cn/) nem lesz hatással.

> [!NOTE]
> Az áttelepítési eszköz késése miatt a klasszikus riasztások áttelepítésének lejárati dátuma [2019 augusztus 31-ig](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/) , az eredetileg bejelentett, 2019. június 30-ig.

 ![Klasszikus riasztás a Azure Portalban](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Javasoljuk, hogy kezdje el és hozza létre újra a riasztásokat az új platformon. A nagyszámú riasztással rendelkező ügyfelek esetében a [fázisokban](alerts-understand-migration.md#rollout-phases)egy [önkéntes áttelepítési eszköz](alerts-using-migration-tool.md) használható a meglévő klasszikus riasztások új riasztási rendszerbe való áthelyezésére, megszakítás vagy hozzáadott költségek nélkül.

> [!IMPORTANT]
> A tevékenység naplójában létrehozott klasszikus riasztási szabályok nem lesznek elavultak vagy migrálva. A tevékenység naplójában létrehozott klasszikus riasztási szabályok az új Azure Monitor-riasztásokból is elérhetők és használhatók. További információ: a [műveletnapló riasztások létrehozása, megtekintése és kezelése Azure monitor használatával](../../azure-monitor/platform/alerts-activity-log.md). Hasonlóképpen, a Service Health található riasztások elérhetők és használhatók az új Service Health szakaszból. Részletekért lásd: [riasztások a szolgáltatás állapotáról szóló értesítésekről](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).

## <a name="unified-metrics-and-alerts-in-application-insights"></a>Egyesített mérőszámok és riasztások a Application Insights

A Azure Monitor újabb metrikai platformja mostantól a Application Insightstól érkező energiagazdálkodást is figyeli. Ez azt jelenti, hogy Application Insights a műveleti csoportokba fog csatlakozni, ami sokkal több lehetőséget tesz lehetővé, mint csupán az előző e-mail-és webhook-hívások. A riasztások mostantól elindíthatnak hanghívásokat, Azure Functions, Logic Apps, SMS és ITSM eszközöket, például a ServiceNow és az Automation Runbookok. A közel valós idejű figyeléssel és riasztással az új platform lehetővé teszi, hogy Application Insights felhasználók ugyanazt a technológiát használják a többi Azure-erőforráshoz, és a Microsoft-termékek figyelését.

A Application Insights új egységes figyelési és riasztási feladatai a következőket foglalják magukban:

- **Application Insights platform metrikák** – amely a Application Insights termék népszerű, előre összeépített metrikáit biztosítja. További információkért tekintse meg az [új Azure Monitor Application Insights platform metrikáinak](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics)használatát ismertető cikket.
- **Application Insights rendelkezésre állás és webes teszt** – lehetővé teszi a webalkalmazás vagy-kiszolgáló válaszadásának és rendelkezésre állásának felmérését. További információkért tekintse meg ezt a cikket a [rendelkezésre állási tesztek és riasztások használatáról az új Azure Monitor Application Insights](../../azure-monitor/app/monitor-web-app-availability.md).
- **Application Insights egyéni metrikák** – lehetővé teszi saját mérőszámok meghatározását és kibocsátását a figyeléshez és a riasztásokhoz. További információkért tekintse meg ezt a cikket, [amely az egyéni metrika használatát ismerteti Application Insights új Azure monitor](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).
- **Application Insights hibák rendellenességei (az intelligens észlelés része)** – amely automatikusan értesíti Önt közel valós időben, ha a webalkalmazás a sikertelen HTTP-kérelmek vagy függőségi hívások gyakoriságának rendellenes növekedését tapasztalja. További információkért tekintse meg ezt a cikket az [intelligens észlelés – meghibásodási rendellenességek](https://docs.microsoft.com/azure/azure-monitor/app/proactive-failure-diagnostics)használata című cikkben.

## <a name="unified-metrics-and-alerts-for-other-azure-resources"></a>Egyesített mérőszámok és riasztások más Azure-erőforrásokhoz

Március 2018 óta a riasztások és az Azure-erőforrások többdimenziós monitorozásának következő generációja rendelkezésre áll. Az újabb metrikus platform és a riasztások mostantól a közel valós idejű funkciókkal is gyorsabbak. Ennél is fontosabb, hogy az újabb mérőszámok platformjának riasztásai részletesebbek, mivel az újabb platform magában foglalja a méretek lehetőségét, ami lehetővé teszi, hogy egy adott érték kombinációjának, feltételének vagy műveletnek a szeletét és szűrését adja meg. Az új Azure Monitorban található összes riasztáshoz hasonlóan az újabb metrikai riasztások bővíthetők a ActionGroups használatával, így az értesítések kiterjeszthetők az e-mailek, a webhookok és az SMS, a hang, az Azure Function, az Automation Runbook és egyebek között. További információ: [metrikus riasztások létrehozása, megtekintése és kezelése Azure monitor használatával](../../azure-monitor/platform/alerts-metric.md).
Az Azure-erőforrások újabb mérőszámai az alábbiak szerint érhetők el:

- **Standard szintű platform-metrikák Azure monitor** , amely a különböző Azure-szolgáltatásokból és-termékekből származó népszerű előre feltöltött mérőszámokat biztosít. További információkért tekintse meg a [Azure monitor támogatott mérőszámait](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported) , és [Azure monitor a metrikai riasztások támogatását](../../azure-monitor/platform/alerts-metric-overview.md#supported-resource-types-for-metric-alerts)ismertető cikket.
- **Azure monitor egyéni metrikák** – amely a felhasználó által vezérelt forrásokból, például a Azure Diagnostics-ügynökből származó mérőszámokat biztosít. További információkért tekintse meg ezt a cikket a [Azure monitor egyéni metrikák](../../azure-monitor/platform/metrics-custom-overview.md)témakörében. Egyéni metrikák használatával közzéteheti a [Windows Azure Diagnostics Agent](../../azure-monitor/platform/collect-custom-metrics-guestos-resource-manager-vm.md) és a [InfluxData-Graf ügynök](../../azure-monitor/platform/collect-custom-metrics-linux-telegraf.md)által gyűjtött mérőszámokat is.

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>A klasszikus monitorozási és riasztási platform kivonulása

Ahogy azt korábban már említettük, a klasszikus monitorozási és riasztási platform jelenleg a Azure Portal [riasztások (klasszikus) szakasza](../../azure-monitor/platform/alerts-classic.overview.md) alapján megszűnik az elkövetkező hónapokban, mivel azokat az újabb rendszer váltotta fel.
A régebbi klasszikus monitorozási és riasztási funkciók a 2019. augusztus 31-én megszűnnek. beleértve a kapcsolódó API-k, Azure Portal felület és szolgáltatások bezárását is. Ezek a szolgáltatások elavultak lesznek:

- Régebbi (klasszikus) mérőszámok és riasztások az Azure-erőforrásokról, amelyek jelenleg a Azure Portal [riasztások (klasszikus) szakaszán](../../azure-monitor/platform/alerts-classic.overview.md) keresztül elérhetők. elérhető [Microsoft. ininsights/alertrules-](https://docs.microsoft.com/rest/api/monitor/alertrules) erőforrásként
- A régebbi (klasszikus) platform és a Application Insights egyéni metrikái, valamint a Azure Portal [riasztások (klasszikus) szakasza](../../azure-monitor/platform/alerts-classic.overview.md) által jelenleg elérhetővé vált riasztások, valamint a [Microsoft. alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules) erőforrás
- A régebbi (klasszikus) meghibásodási rendellenességek riasztása jelenleg a Azure Portal [Application Insightson belüli intelligens észlelésként](../../azure-monitor/app/proactive-diagnostics.md) érhető el; a Azure Portal [riasztások (klasszikus) szakaszában](../../azure-monitor/platform/alerts-classic.overview.md) látható riasztások vannak konfigurálva

A klasszikus figyelési és riasztási rendszerek, beleértve a megfelelő [API](https://msdn.microsoft.com/library/azure/dn931945.aspx)-t, a [PowerShellt](../../azure-monitor/platform/alerts-classic-portal.md), a [CLI](../../azure-monitor/platform/alerts-classic-portal.md)-t, a [Azure Portal lapot](../../azure-monitor/platform/alerts-classic-portal.md)és az [erőforrás-sablont](../../azure-monitor/platform/alerts-enable-template.md) , a 2019 augusztus végéig továbbra is használható 

2019 augusztus végén Azure Monitor:

- A klasszikus figyelési és riasztási szolgáltatás megszűnik, és az új riasztási szabályok létrehozására már nem lesz elérhető.
- Minden olyan riasztási szabály, amely továbbra is létezik a riasztásokban (klasszikus), az 2019. augusztus után továbbra is végrehajtja és elküldi az értesítéseket, de nem érhető el módosításra.
- 2019. szeptembertől kezdődően a klasszikus monitorozási & riasztásokat, amelyek áttelepíthetők, a Microsoft automatikusan áthelyezi az új Azure monitor platformon a néhány héten átívelő fázisokra. A folyamat zökkenőmentesen, leállás nélkül, és az ügyfelek nem fognak csökkenni a figyelési lefedettségben.
- Az új riasztási platformra migrált riasztási szabályok az előzőekben leírtak szerint figyelik a figyelési lefedettséget, de az új adattartalommal kapcsolatos értesítéseket is A rendszer áttelepíti a klasszikus riasztási szabályhoz társított e-mail-címeket, webhook-végpontokat vagy logikai alkalmazásokra mutató hivatkozásokat, de előfordulhat, hogy az új platformon nem működik megfelelően a riasztási adattartalom.
- Egyes [klasszikus riasztási szabályok, amelyek nem telepíthetők át automatikusan](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated) , és manuális beavatkozást igényelnek a felhasználóktól, a 2020-ig maradnak.

> [!IMPORTANT]
> A Microsoft Azure figyelő a fázisok eszközben a klasszikus riasztási szabályoknak az új platformra történő [önkéntes áttelepítését](alerts-using-migration-tool.md) végzi. És futtassa a Force utasítással minden olyan klasszikus riasztási szabályt, amely még létezik és áttelepíthető, a 2019. szeptembertől kezdődően. Az ügyfeleknek biztosítaniuk kell, hogy a klasszikus riasztási szabályokhoz használt Automation-adattartalmat a rendszer a klasszikus riasztási szabályok áttelepítése után az [egyesített metrikák és riasztások Application Insights](#unified-metrics-and-alerts-in-application-insights) , illetve az [egyesített Mérőszámokban és riasztásokkal](#unified-metrics-and-alerts-for-other-azure-resources)való kezeléséhez igazítsa. További információ: [felkészülés a klasszikus riasztási szabályok áttelepítésére](alerts-prepare-migration.md)

Ez a cikk folyamatosan frissül az új Azure Monitoring & riasztási funkciókkal & részletekkel, valamint az új Azure Monitor platform bevezetését segítő eszközök rendelkezésre állásával.

## <a name="pricing-for-migrated-alert-rules"></a>Az áttelepített riasztási szabályok díjszabása

Áttelepítési eszközt vezetünk be, amely segít áttelepíteni a Azure Monitor [klasszikus riasztásokat](../../azure-monitor/platform/alerts-classic.overview.md) az új riasztások felületére. A migrált riasztási szabályok és a megfelelő migrált műveletcsoportok (e-mail, webhook vagy LogicApp) továbbra is ingyenesek maradnak. A klasszikus értesítések funkciói – így a küszöbnek, az összesítéstípusnak és az összesítés részletességének szerkesztése – továbbra is ingyenesen elérhető marad a migrált riasztási szabállyal. Azonban ha úgy szerkeszti a migrált riasztási szabályt, hogy az az új riasztási platform funkcióit, értesítéseit vagy művelettípusait alkalmazza, az díjat von maga után. További információ a riasztási szabályok és értesítések díjszabásáról: [Azure monitor díjszabása](https://azure.microsoft.com/pricing/details/monitor/).

Íme néhány példa a riasztási szabályhoz tartozó díjakra:

- Új (nem migrált),ingyenes egységeken kívül létrehozott riasztási szabály az új Azure Monitor platformon
- Az ingyenes egységeken kívül betöltött és megőrzött adatok az Azure Monitorban
- Többtesztes webtesztek az Application Insightsban
- Az ingyenes egységeken kívül tárolt egyéni metrikák az Azure Monitorban
- Minden olyan áttelepített riasztási szabály, amely újabb metrikus riasztási funkciók, például gyakoriság, több erőforrás/dimenzió, [dinamikus küszöbértékek](alerts-dynamic-thresholds.md), erőforrás/jel módosítása stb. használatára van módosítva.
- Minden olyan áttelepített műveleti csoport, amely újabb értesítések használatára van szerkesztve, vagy olyan típusú műveleteket, mint például az SMS, a hanghívás és/vagy a ITSM-integráció.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg az [új egységesített Azure monitor](../../azure-monitor/overview.md).
* További információ az új [Azure-riasztásokról](../../azure-monitor/platform/alerts-overview.md).
