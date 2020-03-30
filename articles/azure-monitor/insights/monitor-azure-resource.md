---
title: Az Azure-erőforrások figyelése az Azure Monitorsegítségével | Microsoft dokumentumok
description: Bemutatja, hogyan gyűjtheti és elemezheti az Azure-beli erőforrásokból származó figyelési adatokat az Azure Monitor használatával.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: 01d188e0e39888297ff8d6a57129a3a17e1654fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249268"
---
# <a name="monitoring-azure-resources-with-azure-monitor"></a>Az Azure-erőforrások figyelése az Azure Monitorsegítségével
Ha az Azure-erőforrásokra támaszkodó kritikus fontosságú alkalmazásokkal és üzleti folyamatokkal rendelkezik, figyelni szeretné ezeket az erőforrásokat azok rendelkezésre állása, teljesítménye és működése szempontjából. Ez a cikk ismerteti az Azure-erőforrások által létrehozott figyelési adatokat, és hogyan használhatja az Azure Monitor szolgáltatásait az adatok elemzéséhez és riasztásához.

> [!IMPORTANT]
> Ez a cikk az Azure Monitort használó összes Azure-szolgáltatásra vonatkozik. Számítási erőforrások, beleértve a virtuális gépek és az App Service, az itt leírt figyelési adatokat generál, de egy vendég operációs rendszer, amely szintén naplókat és metrikákat is generálhat. Az adatok gyűjtésének és elemzésének részleteiről a szolgáltatások figyelési dokumentációjában olvashat.

## <a name="what-is-azure-monitor"></a>Mi az Azure Monitor?
Az Azure Monitor egy teljes körű figyelési szolgáltatás az Azure-ban, amely az Azure-erőforrások figyelésére szolgáló funkciók teljes készletét biztosítja a más felhőkben és a helyszíni erőforrásokon kívül. Az [Azure Monitor adatplatform](../platform/data-platform.md) adatokat gyűjt [a naplók](../platform/data-platform-logs.md) és [metrikák,](../platform/data-platform-metrics.md) ahol együtt elemezhetők a figyelési eszközök teljes készletével a következő szakaszokban leírtak szerint.

- [Mire használható az Azure Monitor metrikák?](../platform/data-platform-metrics.md#what-can-you-do-with-azure-monitor-metrics)
- [Mire használható az Azure Monitor-naplók?](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs)

Amint létrehoz egy Azure-erőforrást, az Azure Monitor engedélyezve van, és elkezdi gyűjteni a metrikákat és a tevékenységnaplókat, amelyeket [az Azure Portalon tekinthet meg és](#monitoring-in-the-azure-portal)elemezhet. Néhány konfigurációval további figyelési adatokat gyűjthet, és további funkciókat engedélyezhet. A konfigurációs követelményekkel kapcsolatos részletekért tekintse meg az alábbi [figyelési](#monitoring-data) adatokat.


## <a name="costs-associated-with-monitoring"></a>A nyomon követéshez kapcsolódó költségek
Alapértelmezés szerint gyűjtött figyelési adatok elemzésének nincs költsége. Ez a következőket foglalja magában:

- Platformmérő számok gyűjtése és elemzése a metrikák kezelőjével.
- Tevékenységnapló gyűjtése és elemzése az Azure Portalon.
- Tevékenységnapló-riasztási szabály létrehozása.

Az Azure Monitor nem jár a naplók és metrikák gyűjtésére és exportálására, de a célhoz kapcsolódó költségek is előfordulhatnak:

- Az adatok betöltésével és megőrzésével kapcsolatos költségek a Log Analytics-munkaterületen naplók és metrikák gyűjtésekor. Lásd: [Az Azure Monitor díjszabása a Log Analytics.](https://azure.microsoft.com/pricing/details/monitor/)
- Az adatok tárolásával kapcsolatos költségek, amikor naplókat és metrikákat gyűjt egy Azure-tárfiókba. Lásd: [Azure Storage-díjszabás a blob storage.](https://azure.microsoft.com/pricing/details/storage/blobs/)
- Az eseményközpont-streameléssel kapcsolatos költségek a naplók és metrikák Azure Event Hubs-ba történő továbbításakor. Lásd: [Az Azure Event Hubs díjszabása.](https://azure.microsoft.com/pricing/details/event-hubs/)

Előfordulhat, hogy az Azure Monitor a következőkhöz kapcsolódó költségek. Lásd: [Az Azure Monitor díjszabása:](https://azure.microsoft.com/pricing/details/monitor/)

- Naplólekérdezés futtatása.
- Metrika vagy naplólekérdezési riasztási szabály létrehozása.
- Értesítés küldése bármely riasztási szabályról.
- Metrikák elérése API-n keresztül.

## <a name="monitoring-data"></a>Adatok monitorozása
Az Azure-ban lévő erőforrások [naplókat](../platform/data-platform-logs.md) és [metrikákat](../platform/data-platform-metrics.md) hoznak létre az alábbi ábrán. Tekintse meg az egyes Azure-szolgáltatások dokumentációját az általuk létrehozott konkrét adatokat és az általuk biztosított további megoldásokat vagy elemzéseket.

![Áttekintés](media/monitor-azure-resource/logs-metrics.png)



- [Platform metrikák](../platform/data-platform-metrics.md) – Numerikus értékek, amelyek automatikusan gyűjtik rendszeres időközönként, és írja le egy erőforrás egy adott időpontban néhány szempontból. 
- [Erőforrás-naplók](../platform/platform-logs-overview.md) – Az Azure-erőforráson (az adatsíkon) végrehajtott műveletek betekintése, például egy Key Vault titkos kulcsának beszerzése vagy egy adatbázisra vonatkozó kérés. Az erőforrásnaplók tartalma és szerkezete az Azure-szolgáltatástól és az erőforrástípustól függően változik.
- [Tevékenységnapló](../platform/platform-logs-overview.md) – Betekintést nyújt az egyes Azure-erőforrások egyes Azure-erőforrások on az előfizetés ben kívülről (a felügyeleti sík), például egy új erőforrás létrehozása vagy egy virtuális gép indítása. Ez az információ arról, hogy mit, ki és mikor az előfizetésben lévő erőforrásokon végzett írási műveletek (PUT, POST, DELETE) esetében.


## <a name="configuration-requirements"></a>Konfigurációs követelmények

### <a name="configure-monitoring"></a>Figyelés konfigurálása
Egyes figyelési adatok automatikusan gyűjtik, de előfordulhat, hogy bizonyos konfigurációt kell végrehajtania a követelményektől függően. A figyelési adatok egyes típusára vonatkozó konkrét információkat az alábbi információkban talál.

- [Platform metrikák](../platform/data-platform-metrics.md) – Platform metrikák automatikusan gyűjtik az [Azure Monitor metrikák](../platform/data-platform-metrics.md) konfiguráció nélkül szükséges. Hozzon létre egy diagnosztikai beállítást, amelyből bejegyzéseket küldhet az Azure Monitor naplókba, vagy továbbítheti őket az Azure-on kívülre.
- [Erőforrás-naplók](../platform/platform-logs-overview.md) – Az erőforrásnaplókat automatikusan létrehozza az Azure-erőforrások, de nem gyűjti diagnosztikai beállítás nélkül.  Hozzon létre egy diagnosztikai beállítást, amelyből bejegyzéseket küldhet az Azure Monitor naplókba, vagy továbbítheti őket az Azure-on kívülre.
- [Tevékenységnapló](../platform/platform-logs-overview.md) – A tevékenységnapló automatikusan gyűjti a konfiguráció nélkül szükséges, és megtekinthető az Azure Portalon. Hozzon létre egy diagnosztikai beállítást az Azure Monitor naplókba való másoláshoz vagy az Azure-on kívüli továbbításhoz.

### <a name="log-analytics-workspace"></a>Log Analytics-munkaterület
Az Azure Monitor-naplókba történő adatgyűjtéshez Log Analytics-munkaterület szükséges. Új munkaterület létrehozásával gyorsan megkezdheti a szolgáltatás figyelését, de előfordulhat, hogy egy olyan munkaterület használatával, amely más szolgáltatásokból gyűjt adatokat. A munkaterület létrehozásával és az [Azure Monitor-naplók üzembe helyezésének tervezésével](../platform/design-logs-deployment.md) kapcsolatos részletekért tekintse [meg a Log Analytics-munkaterület létrehozása az Azure Portalon](../learn/quick-create-workspace.md) című témakört. Ha egy meglévő munkaterületet használ a szervezetben, akkor megfelelő engedélyekre lesz szüksége a Hozzáférés kezelése az [Azure Monitorban a naplóadatokhoz és a munkaterületekhez való hozzáférés kezelése](../platform/manage-access.md)című részben leírtak szerint. 





## <a name="diagnostic-settings"></a>Diagnosztikai beállítások
A diagnosztikai beállítások határozzák meg, hogy egy adott erőforrás erőforrás-naplókat és -metrikákat kell küldeni. A lehetséges úti célok a következők:

- [A Log Analytics munkaterület,](../platform/resource-logs-collect-workspace.md) amely lehetővé teszi az adatok elemzését az Azure Monitor által gyűjtött egyéb figyelési adatokkal hatékony naplólekérdezések használatával, valamint az Azure Monitor egyéb funkcióinak, például a naplóriasztásoknak és a vizualizációknak a kihasználásához. 
- [Eseményközpontok](../platform/resource-logs-stream-event-hubs.md) az adatok külső rendszerekre, például külső SIEM-ekre és más naplóelemzési megoldásokra való streameléséhez. 
- [Azure storage-fiók,](../platform/resource-logs-collect-storage.md) amely hasznos naplózás, statikus elemzés vagy biztonsági mentés.

Kövesse a Diagnosztikai beállítás létrehozása című eljárást a [platformnaplók és metrikák azure-beli gyűjtéséhez](../platform/diagnostic-settings.md) és kezeléséhez diagnosztikai beállítások létrehozásához és kezeléséhez az Azure Portalon keresztül. Lásd: [Diagnosztikai beállítás létrehozása az Azure-ban egy Resource Manager-sablon használatával](../platform/diagnostic-settings-template.md) definiálja őket egy sablonban, és engedélyezi a teljes figyelést egy erőforrás hozásakor.


## <a name="monitoring-in-the-azure-portal"></a>Figyelés az Azure Portalon
 A legtöbb Azure-erőforrás figyelési adataihoz hozzáférhet az Azure Portalon található erőforrás menüjéből. Ez hozzáférést biztosít egyetlen erőforrás adataihoz a szabványos Azure Monitor-eszközök használatával. Egyes Azure-szolgáltatások különböző lehetőségeket biztosítanak, ezért további információkért tájékozódhat az adott szolgáltatás dokumentációjában. Az **Azure Monitor** menüsegítségével elemezheti az összes figyelt erőforrás adatait. 

### <a name="overview"></a>Áttekintés
Számos szolgáltatás az **áttekintő** lapon figyeli az adatokat, így rövid áttekintést kap a működésükről. Ez általában az Azure Monitor metrikákban tárolt platformmetrikák egy részhalmazán alapul. Egyéb figyelési lehetőségek általában elérhető a **szolgáltatások figyelési** szakaszában. Menü.

![Áttekintő lap](media/monitor-azure-resource/overview-page.png)


### <a name="insights-and-solutions"></a>Betekintések és megoldások 
Egyes szolgáltatások az Azure Monitor szabványos funkcióin túl is biztosítanak eszközöket. [Az Insights](../insights/insights-overview.md) az Azure Monitor adatplatformjára és szabványos szolgáltatásaira épülő, személyre szabott figyelési élményt nyújt. [A megoldások](../insights/solutions.md) előre definiált figyelési logikát biztosítanak az Azure Monitor naplókra épülő. 

Ha egy szolgáltatás rendelkezik egy Azure Monitor-insight, elérheti azt az egyes erőforrások menüjében **figyelése.** Az **Azure Monitor** menüből elérheti az összes betekintést és megoldást.

![Insights](media/monitor-azure-resource/insights.png)

### <a name="metrics"></a>Mérőszámok
A metrikák elemzése az Azure Portalon [metrikák explorer,](../platform/metrics-getting-started.md) amely elérhető a **Metrikák** menüelem a legtöbb szolgáltatás. Ez az eszköz lehetővé teszi, hogy az egyes mutatókkal dolgozzon, vagy több et kombináljon a korrelációk és trendek azonosításához. 

- Az [Azure Metrics Explorer használatának](../platform/metrics-getting-started.md) alapjairól.
- Lásd: [Az Azure Metrics Explorer speciális funkciói,](../platform/metrics-charts.md) például a metrikák kezelőjének speciális funkciói, például több metrika használata, szűrők alkalmazása és felosztása.

![Mérőszámok](media/monitor-azure-resource/metrics.png)


### <a name="activity-log"></a>Tevékenységnapló 
Bejegyzések megtekintése a tevékenységnaplóban az Azure Portalon a kezdeti szűrő beállítása az aktuális erőforrás. Másolja a tevékenységnaplót egy Log Analytics-munkaterületre, hogy hozzáférjen a naplólekérdezésekben és munkafüzetekben való használatához. 

- [Tekintse meg az Azure-tevékenységnapló-események megtekintése és beolvasása](../platform/activity-log-view.md) a tevékenységnapló megtekintésével és a bejegyzések különböző módszerekkel történő lekérésével kapcsolatos részleteket.
- Tekintse meg az Azure-szolgáltatás dokumentációját a naplózott eseményekről.

![Tevékenységnapló](media/monitor-azure-resource/activity-log.png)

### <a name="azure-monitor-logs"></a>Azure Monitor-naplók
Az Azure Monitor Logs egy hatékony lekérdezési eszközzel összesíti a naplókat és a metrikákat több szolgáltatásból és más adatforrásokból. A fentiekben leírtak szerint hozzon létre egy diagnosztikai beállítást a platformmetrikák, a tevékenységnapló és az erőforrásnaplók összegyűjtéséhez az Azure Monitor Log Analytics-munkaterületére.

[A Log Analytics](../log-query/get-started-portal.md) lehetővé teszi [a naplólekérdezésekkel](../log-query/log-query-overview.md)való munkát, amely az Azure Monitor hatékony funkciója, amely lehetővé teszi a naplóadatok speciális elemzését egy teljes értékű lekérdezési nyelv használatával. Nyissa meg a Naplóanalytics **a Naplók** a **Figyelés** menüben egy Azure-erőforrás dolgozni naplólekérdezések az erőforrást használva a [lekérdezéshatókör.](../log-query/scope.md#query-scope) Ez lehetővé teszi az adatok elemzését több tábla között csak az adott erőforráshoz. Az Azure Monitor **menünaplói** használatával az összes erőforrás naplóihoz férhet hozzá. 

- Az [Azure Monitor naplólekérdezéseinek első lépései](../log-query/get-started-queries.md) című témakörben a naplólekérdezések írásához használt lekérdezési nyelv használatával kapcsolatos oktatóanyagról.
- Tekintse [meg az Azure-erőforrások naplóinak gyűjtése a Log Analytics-munkaterület az Azure Monitor](../platform/resource-logs-collect-workspace.md) ban az Erőforrás-naplók gyűjtése az Azure Monitor naplók és a részleteket, hogyan érheti el őket a lekérdezésben.
- [Lásd: Gyűjtési mód](../platform/resource-logs-collect-workspace.md#resource-log-collection-mode) az erőforrásnapló-adatok azure-figyelőnaplókban való strukturálásának magyarázatát.
- Tekintse meg az egyes Azure-szolgáltatások dokumentációját az Azure Monitor-naplók ban az asztalán található részletekért.

![Naplók](media/monitor-azure-resource/logs.png)

## <a name="monitoring-from-command-line"></a>Figyelés parancssorból
Az erőforrásból gyűjtött figyelési adatokat parancssorból érheti el, vagy parancsfájlba foglalhatja az [Azure PowerShell](/powershell/azure/) vagy az [Azure parancssori felület használatával.](/cli/azure/) 

- Lásd: [CLI metrika-referencia](/cli/azure/monitor/metrics) a CLI metrikaadatainak eléréséhez.
- Lásd: [CLI Log Analytics-hivatkozás](/cli/azure/ext/log-analytics/monitor/log-analytics) az Azure Monitor Naplók adatainak a CLI-ből származó naplólekérdezés használatával való eléréséhez.
- Tekintse meg [az Azure PowerShell metrikák hivatkozás](/powershell/module/azurerm.insights/get-azurermmetric) i metrikaadatok azure PowerShell eléréséhez.
- Tekintse meg [az Azure PowerShell naplólekérdezési hivatkozás](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery) t az Azure Monitor naplók adatainak azure PowerShell-ből származó naplólekérdezés használatával való eléréséhez.

## <a name="monitoring-from-rest-api"></a>Figyelés a REST API-ból
Az erőforrásból gyűjtött adatok figyelése egy REST API-t használó egyéni alkalmazásban.

- Tekintse meg [az Azure Monitoring REST API-forgatókönyv](../platform/rest-api-walkthrough.md) az Azure Monitor REST API-ból származó metrikák elérésével kapcsolatos részletekért.
- Tekintse meg [az Azure Log Analytics REST API-t](https://dev.loganalytics.io/) az Azure Monitor Naplók adatainak az Azure PowerShellből származó naplólekérdezés használatával történő eléréséről.

## <a name="alerts"></a>Riasztások
[A riasztások](../platform/alerts-overview.md) proaktív módon értesítik Önt, és potenciálisan intézkednek, ha fontos feltételek találhatók a figyelési adatokban. Hozzon létre egy riasztási szabályt, amely meghatározza a riasztás célját, a riasztás létrehozásának feltételeit és a válaszként végrehajtandó műveleteket.

Különböző típusú figyelési adatok különböző típusú riasztási szabályok.

- [Tevékenységnapló-riasztás](../platform/alerts-activity-log.md) – Riasztás létrehozása, ha egy adott feltételeknek megfelelő bejegyzés jön létre a tevékenységnaplóban. Ez lehetővé teszi, hogy értesítést kapjon például egy adott típusú erőforrás létrehozásáról, vagy ha a konfiguráció módosítása sikertelen.
- [Metrikariasztás](../platform/alerts-metric.md) – Hozzon létre egy riasztást, ha egy metrika értéke meghaladja az adott küszöbértéket. A metrikariasztások jobban reagálnak, mint más riasztások, és automatikusan feloldhatók a probléma kijavításakor.
- [Naplólekérdezési riasztás](../platform/alerts-log.md) – Rendszeres időközönként naplólekérdezés futtatása, és riasztás létrehozása, ha egy adott feltétel található. Ez lehetővé teszi, hogy összetett elemzést végezzen több adathalmazon és .

Az erőforrás menüjéből származó **riasztások** segítségével megtekintheti a riasztásokat, és kezelheti az adott erőforrásra vonatkozó riasztási szabályokat. Csak a tevékenységnapló-riasztások és a metrikariasztások használják az egyes Azure-erőforrásokat célként. A naplólekérdezési riasztások a Log Analytics-munkaterületet használják célként, és olyan lekérdezésen alapulnak, amely hozzáférhet az adott munkaterületen tárolt naplókhoz. Az Azure Monitor menüben megtekintheti és kezelheti az összes erőforrásriasztásait, valamint kezelheti a naplólekérdezési riasztási szabályokat.

- A fenti különböző típusú riasztásokat a riasztási szabályok létrehozásával kapcsolatos részletekért tekintse meg.
- [A műveletcsoportok létrehozása és kezelése az Azure Portalon](../platform/action-groups.md) című témakörben talál további információt egy olyan műveletcsoport létrehozásáról, amely lehetővé teszi a riasztásokra adott válaszok kezelését.



## <a name="next-steps"></a>További lépések

* A különböző Azure-szolgáltatások erőforrás-naplóinak részletes adatait lásd: [Támogatott szolgáltatások, sémák és kategóriák az Azure Resource Logs](../platform/diagnostic-logs-schema.md) számára.  
