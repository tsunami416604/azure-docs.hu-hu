---
title: Klasszikus riasztási & figyelés frissítése az Azure Monitorban
description: A klasszikus figyelési szolgáltatások és funkciók kivonásának leírása, amely korábban az Azure Portalon a Riasztások (klasszikus) alatt látható.
author: yanivlavi
services: azure-monitor
ms.topic: conceptual
ms.date: 2/7/2019
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: dec4d6824644cabf8b1872da207b8554fee0b3d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659475"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Az Azure Monitor egységes ített riasztási & figyelése felváltja a klasszikus riasztási & figyelést

Az Azure Monitor mostantól egységes, teljes veremfigyelési szolgáltatássá vált, amely mostantól támogatja az "Egy metrika" és az "Egy riasztás" szolgáltatást az erőforrások között; További információt az [új Azure Monitorról szóló blogbejegyzésünkben](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/)talál. Az új Azure-figyelési és riasztási platformok gyorsabbak, okosabbak és bővíthetők – lépést tartva a felhőalapú számítástechnika növekvő kiterjedésével, és összhangban a Microsoft Intelligent Cloud filozófiájával. 

Az új Azure-figyelési és riasztási platform malmára hajtottuk a figyelési és riasztási platformot, amely az Azure-riasztások *klasszikus riasztási* szakaszában található, **és 2019 augusztusáig elavultunk az Azure nyilvános felhőiben.** [Az Azure Government felhőjét](../../azure-government/documentation-government-welcome.md) és az [Azure China 21Vianet-et](https://docs.azure.cn/) ez nem érinti.

> [!NOTE]
> A migrációs eszköz bevezetésének késedelmi ideje miatt a klasszikus riasztások áttelepítésének nyugdíjazási dátumát 2019. június 30-án, az eredetileg bejelentett időponttól [2019. augusztus 31-ig meghosszabbították.](https://azure.microsoft.com/updates/azure-monitor-classic-alerts-retirement-date-extended-to-august-31st-2019/)

 ![Klasszikus riasztás az Azure Portalon](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Javasoljuk, hogy kezdje el a kezdést, és hozza létre újra a riasztásokat az új platformon. Az ügyfelek számára, akik nagy számú riasztást, mi [gördülő ki fázisokban](alerts-understand-migration.md#rollout-phases), egy [önkéntes áttelepítési eszköz](alerts-using-migration-tool.md) áthelyezni a meglévő klasszikus riasztások az új riasztási rendszer megszakítás nélkül, vagy hozzáadott költségek.

> [!IMPORTANT]
> A tevékenységnaplóban létrehozott klasszikus riasztási szabályok nem lesznek elavultak vagy áttelepíthetők. A tevékenységnaplóban létrehozott összes klasszikus riasztási szabály elérhető és használható az új Azure Monitor – Riasztások között. További információt a [Tevékenységnapló-riasztások létrehozása, megtekintése és kezelése az Azure Monitor használatával című](../../azure-monitor/platform/alerts-activity-log.md)témakörben talál. Hasonlóképpen a Service Health riasztásai is elérhetők és használhatók az új Szolgáltatásállapot szakaszban. További információt [a szolgáltatásállapot-értesítésekre vonatkozó értesítésekben](../../azure-monitor/platform/alerts-activity-log-service-notifications.md)talál.

## <a name="unified-metrics-and-alerts-in-application-insights"></a>Egyesített metrikák és riasztások az Application Insightsban

Az Azure Monitor újabb metrikaplatformja mostantól az Application Insightsból érkező figyelést fog leellenőrizni. Ez az áthelyezés azt jelenti, hogy az Application Insights a műveletcsoportokhoz csatlakozik, így sokkal több lehetőség közül választhat, mint az előző e-mail- és webhook-hívások. A riasztások mostantól hanghívásokat, Azure-függvényeket, logikai alkalmazásokat, SMS-eket és ITSM-eszközöket, például ServiceNow- és Automation Runbookokat indíthatnak el. A közel valós idejű figyelés és riasztás, az új platform lehetővé teszi az Application Insights-felhasználók számára, hogy kihasználják ugyanazt a technológiát, amely bekapcsolja a figyelést más Azure-erőforrások között, és alátámasztja a Microsoft-termékek figyelését.

Az alkalmazáselemzési adatok új, egységes figyelése és riasztása a következőket foglalja magában:

- **Application Insights platform metrikák** – amely népszerű előre összeállított metrikák az Application Insights-termékből. További információt ebben a cikkben a [Platform metrikák az application insights az új Azure Monitor.](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics)
- **Az Application Insights rendelkezésre állása és a webes teszt** – amely lehetővé teszi a webalkalmazás vagy kiszolgáló válaszképességének és rendelkezésre állásának felmérését. További információt ebben a cikkben a [rendelkezésre állási tesztek és riasztások az Application Insights az új Azure Monitor.](../../azure-monitor/app/monitor-web-app-availability.md)
- **Application Insights egyéni metrikák** – amely lehetővé teszi, hogy meghatározza és kibocsátja a saját metrikák figyelése és riasztások. További információt ebben a cikkben az [egyéni metrika az application insights az új Azure Monitor.](../../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation)
- **Application Insights-hibaanomáliák (az intelligens észlelés része)** – amely automatikusan értesíti Önt közel valós időben, ha a webalkalmazás a sikertelen HTTP-kérelmek vagy függőségi hívások arányának rendellenes növekedését tapasztalja. További információt az [intelligens észlelés – hibaanomáliák](https://docs.microsoft.com/azure/azure-monitor/app/proactive-failure-diagnostics)használatáról szóló cikktartalmaz.

## <a name="unified-metrics-and-alerts-for-other-azure-resources"></a>Egyesített metrikák és riasztások más Azure-erőforrásokhoz

2018 márciusa óta az Azure-erőforrások riasztási és többdimenziós figyelésének következő generációja már elérhető. Most az újabb metrika platform és a riasztási gyorsabb közel valós idejű képességekkel. Ennél is fontosabb, hogy az újabb metrikaplatform-riasztások részletesebbek, mivel az újabb platform tartalmazza a dimenziók lehetőségét, amelyek lehetővé teszik a szeletelést és a szűrést adott értékkombinációra, állapotra vagy műveletre. Az új Azure Monitor összes riasztásához hasonlóan az újabb metrikariasztások is bővíthetőbbek az ActionGroups használatával – lehetővé téve az értesítések bővítését az e-mailen vagy a webhookon túl SMS-re, Hangra, Azure-funkcióra, Automation Runbookra és egyebekre. További információ: [Metrikariasztások létrehozása, megtekintése és kezelése az Azure Monitor használatával című témakörben.](../../azure-monitor/platform/alerts-metric.md)
Az Azure-erőforrások újabb metrikák a következőképpen érhetők el:

- **Azure Monitor Standard platform metrikák** – amely népszerű előre kitöltött metrikák különböző Azure-szolgáltatások és -termékek. További információt az Azure Monitor és [támogatás metrikariasztásaiaz Azure Monitoron](../../azure-monitor/platform/alerts-metric-overview.md#supported-resource-types-for-metric-alerts) [támogatott metrikákról](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported) szóló cikkben talál.
- **Azure Monitor egyéni metrikák** – amely a felhasználói alapú forrásokból származó metrikák, beleértve az Azure Diagnostics ügynök. További információt az Azure [Monitor egyéni metrikáiról](../../azure-monitor/platform/metrics-custom-overview.md)szóló cikkben talál. Egyéni metrikák használatával a Windows [Azure Diagnosztikai ügynök](../../azure-monitor/platform/collect-custom-metrics-guestos-resource-manager-vm.md) és [az InfluxData Telegraf ügynök](../../azure-monitor/platform/collect-custom-metrics-linux-telegraf.md)által gyűjtött metrikákat is közzéteheti.

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>A klasszikus felügyeleti és riasztási platform kivonása

Ahogy azt korábban említettük, a klasszikus figyelési és riasztási platform jelenleg használható az Azure Portal [riasztások (klasszikus) szakasza](../../azure-monitor/platform/alerts-classic.overview.md) lesz kivonni az elkövetkező hónapokban, mivel azokat az újabb rendszer váltotta fel.
2019. augusztus 31-én megszűnik a régebbi klasszikus monitorozás és riasztás; beleértve a kapcsolódó API-k, az Azure Portal felület és a benne lévő szolgáltatások bezárását. Pontosabban, ezek a funkciók elavultak lesznek:

- Régebbi (klasszikus) metrikák és riasztások az Azure-erőforrások jelenleg elérhető az Azure Portal [riasztások (klasszikus) szakaszában;](../../azure-monitor/platform/alerts-classic.overview.md) [microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules) erőforrásként érhető el
- Régebbi (klasszikus) platform- és egyéni metrikák az Application Insightshoz, valamint riasztási azok a jelenleg elérhető az Azure Portal [Riasztások (klasszikus) szakaszában,](../../azure-monitor/platform/alerts-classic.overview.md) és [elérhető a microsoft.insights/alertrules](https://docs.microsoft.com/rest/api/monitor/alertrules) erőforrásként
- Régebbi (klasszikus) hibaanomáliák riasztási jelenleg elérhető [intelligens észlelési az Azure-portálon belül Application Insights](../../azure-monitor/app/proactive-diagnostics.md) belül; az Azure Portal [Riasztások (klasszikus) szakaszában](../../azure-monitor/platform/alerts-classic.overview.md) konfigurált riasztásokkal

2019 augusztusának végéig minden klasszikus figyelési és riasztási rendszer, beleértve a megfelelő [API-t,](https://msdn.microsoft.com/library/azure/dn931945.aspx) [a PowerShellt,](../../azure-monitor/platform/alerts-classic-portal.md) [a CLI-t](../../azure-monitor/platform/alerts-classic-portal.md), az [Azure Portal-oldalt](../../azure-monitor/platform/alerts-classic-portal.md)és az [Erőforrássablont,](../../azure-monitor/platform/alerts-enable-template.md) elérhető marad. 

2019. augusztus végén az Azure Monitorban:

- Klasszikus figyelési és riasztások szolgáltatás lesz nyugdíjas, és már nem érhető el az új riasztási szabályok létrehozása.
- A 2019 augusztusa után is létező riasztási szabályok (klasszikus) továbbra is végrehajtják és kijelentik a tűzvédelmi értesítéseket, de módosításra nem érhetők el.
- 2019 szeptemberétől kezdődően a klasszikus figyelési & riasztások riasztási szabályait a Microsoft automatikusan áthelyezi az új Azure monitorplatformmegfelelőjére néhány héten átívelő fázisokban. A folyamat zökkenőmentes lesz állásidő nélkül, és az ügyfelek nem lesz veszteség a lefedettség figyelésében.
- Az új riasztási platformra áttelepített riasztási szabályok a korábban megadott figyelési lefedettséget biztosítják, de új rakományokkal indítja el az értesítést. Minden e-mail-cím, webhook-végpont vagy a klasszikus riasztási szabályhoz társított logikai alkalmazás-hivatkozás átvitele az áttelepítéskor lesz átvihető, de előfordulhat, hogy nem megfelelően viselkedik, mivel a riasztási hasznos tartalom eltérő lesz az új platformon.
- Néhány [klasszikus riasztási szabály, amely nem telepíthető át automatikusan,](alerts-understand-migration.md#classic-alert-rules-that-will-not-be-migrated) és a felhasználók manuális beavatkozását igényli, 2020 júniusáig folytatódik.

> [!IMPORTANT]
> A Microsoft Azure Monitor fokozatosan bevezetett [eszköz, amely önkéntesen áttelepíti](alerts-using-migration-tool.md) a klasszikus riasztási szabályokat az új platformra hamarosan. És futtassa erővel az összes klasszikus riasztási szabályok, amelyek még mindig léteznek, és lehet áttelepíteni, kezdve szeptember 2019. Az ügyfeleknek gondoskodniuk kell arról, hogy a klasszikus riasztási szabály hasznos adatait használó automatizálás alkalmazkodjon az [egyesített metrikák és riasztások](#unified-metrics-and-alerts-in-application-insights) által az Application Insightsban vagy az egyesített [metrikákés riasztások más Azure-erőforrásokhoz származó](#unified-metrics-and-alerts-for-other-azure-resources)új hasznos adatok kezeléséhez, a klasszikus riasztási szabályok áttelepítése után. További információ: [Felkészülés a klasszikus riasztási szabály áttelepítésére](alerts-prepare-migration.md)

Ez a cikk folyamatosan frissül az új Azure figyelési & riasztási funkcióval kapcsolatos hivatkozásokkal, & adatokkal, valamint az új Azure Monitor platform bevezetésében segítséget nyújtó eszközök rendelkezésre állásával.

## <a name="pricing-for-migrated-alert-rules"></a>Az áttelepített riasztási szabályok díjszabása

Egy áttelepítési eszközt vezetünk be, amely segít az Azure Monitor [klasszikus riasztásainak](../../azure-monitor/platform/alerts-classic.overview.md) átgrálásában az új riasztások élményébe. Az áttelepített riasztási szabályok és a megfelelő áttelepített műveletcsoportok (e-mail, webhook vagy LogicApp) továbbra is ingyenesek maradnak. A klasszikus riasztások funkciói, beleértve a küszöbérték, az összesítés típusának és az összesítés részletességének szerkesztését, továbbra is ingyenesen elérhetők maradnak az áttelepített riasztási szabállyal. Ha azonban úgy módosítja az áttelepített riasztási szabályt, hogy az új riasztási platform bármely funkcióját, értesítését vagy művelettípusát használja, a megfelelő díj lesz érvényben. A riasztási szabályok és értesítések díjszabásáról az [Azure Monitor díjszabása](https://azure.microsoft.com/pricing/details/monitor/)című témakörben talál további információt.

Az alábbi példák példák olyan esetekre, amikor a riasztási szabály ért el díjat:

- Bármely új (nem áttelepített) riasztási szabály, amely az ingyenes egységeken túl jön létre az új Azure Monitor platformon
- Az Azure Monitor által biztosított ingyenes egységeken túl megőrződött és megőrzött adatok
- Az Application Insights által végrehajtott többtesztes webes tesztek
- Az Azure Monitorban található ingyenes egységeken túl tárolt egyéni metrikák
- Az áttelepített riasztási szabályok, amelyek újabb metrikariasztási funkciók, például gyakoriság, több erőforrás/dimenzió, [dinamikus küszöbértékek,](alerts-dynamic-thresholds.md)erőforrás/jel módosítása és így tovább használatával vannak szerkesztve.
- Az áttelepített műveletcsoportok, amelyek újabb értesítések használatára vannak szerkesztve, vagy művelettípusok, például SMS, Hanghívás és/vagy ITSM-integráció.

## <a name="next-steps"></a>További lépések

* Ismerje meg az [új egyesített Azure Monitort.](../../azure-monitor/overview.md)
* További információ az új [Azure-riasztásokról.](../../azure-monitor/platform/alerts-overview.md)
