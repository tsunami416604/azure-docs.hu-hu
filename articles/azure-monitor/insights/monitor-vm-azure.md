---
title: Az Azure virtuális gépek figyelése az Azure Monitorsegítségével
description: Bemutatja, hogyan gyűjthet és elemezhet figyelési adatokat az Azure-beli virtuális gépekről az Azure Monitor használatával.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: 2cb53d0c88d8c29da2bd8bf52d6536555d56c76e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283939"
---
# <a name="monitoring-azure-virtual-machines-with-azure-monitor"></a>Az Azure virtuális gépek figyelése az Azure Monitorsegítségével
Ez a cikk bemutatja, hogyan használhatja az Azure Monitor segítségével az Azure virtuális gépekről származó adatok gyűjtésére és elemzésére az állapotuk megőrzése érdekében. Virtuális gépek is figyelhető a rendelkezésre állás és a teljesítmény az Azure Monitor, mint bármely [más Azure-erőforrás,](monitor-azure-resource.md)de ezek egyedimás erőforrások, mivel a vendég operációs és rendszer és a benne futó számítási feladatok is. 

> [!NOTE]
> Ez a cikk teljes áttekintést nyújt a virtuális gépek figyelésének fogalmait és lehetőségeit az Azure Monitorban. A virtuális gépek gyors figyeléséhez anélkül, hogy az alapul szolgáló fogalmakra összpontosítana, olvassa el a [Rövid útmutató: Egy Azure virtuális gép figyelése](../learn/quick-monitor-azure-vm.md)az Azure Monitor segítségével című témakört.


## <a name="differences-from-other-azure-resources"></a>Különbségek más Azure-erőforrásokkal szemben
[Az Azure-erőforrások figyelése](monitor-azure-resource.md) az Azure Monitor segítségével ismerteti az Azure-erőforrások által generált figyelési adatokat, és hogyan használhatja az Azure Monitor funkcióit az adatok elemzésére és riasztására. Az Azure virtuális gépekről ugyanazokat a figyelési adatokat gyűjtheti össze, és a következő különbségeket használhatja:

- [A platformmetrikák](../platform/data-platform-metrics.md) gyűjtése automatikusan történik a virtuális gépekhez, de csak a [virtuálisgép-gazdagép hez.](#monitoring-data) A vendég operációs rendszer teljesítményadatainak gyűjtéséhez ügynökre van szükség. 
- A virtuális gépek nem hoznak létre [olyan erőforrásnaplókat,](../platform/platform-logs-overview.md) amelyek betekintést nyújtanak az Azure-erőforrásokon belül végrehajtott műveletekbe. A vendég operációs rendszerből naplóadatok gyűjtésére egy ügynök segítségével gyűjthet naplóadatokat.
- [Diagnosztikai beállításokat](../platform/diagnostic-settings.md) hozhat létre egy virtuális gép hez, hogy platformmetrikákat küldjön más célhelyekre, például a tároló- és eseményközpontokba, de ezeket a diagnosztikai beállításokat nem konfigurálhatja az Azure Portalon. 

## <a name="monitoring-data"></a>Adatok monitorozása
Virtuális gépek az Azure-ban az Azure-ban [naplókat](../platform/data-platform-logs.md) és [metrikákat](../platform/data-platform-metrics.md) hoz létre a következő ábrán látható.

![Áttekintés](media/monitor-vm-azure/logs-metrics.png)


### <a name="virtual-machine-host"></a>Virtuális gép gazdagép-állomása
Az Azure-ban lévő virtuális gépek a következő adatokat generálják a virtuális gép gazdagépe számára, ugyanúgy, mint a [figyelési adatokban](monitor-azure-resource.md#monitoring-data)ismertetett többi Azure-erőforrás.

- [Platform metrikák](../platform/data-platform-metrics.md) – Numerikus értékek, amelyek automatikusan gyűjtik rendszeres időközönként, és írja le egy erőforrás egy adott időpontban néhány szempontból. A platformmetrikák a virtuális gép gazdagép, de a diagnosztikai bővítmény a vendég operációs rendszer metrikák gyűjtéséhez.
- [Tevékenységnapló](../platform/platform-logs-overview.md) – betekintést nyújt az egyes Azure-erőforrások egyes Azure-erőforrások on az előfizetés külső (a felügyeleti sík) műveleteket. Virtuális gép esetében ez olyan információkat tartalmaz, mint például az indítás a kezdetektől és a konfigurációs módosítások.


### <a name="guest-operating-system"></a>Vendég operációs rendszer
Adatok gyűjtése a virtuális gép vendég operációs rendszer, szüksége van egy ügynök, amely helyileg fut minden virtuális gépen, és adatokat küld az Azure Monitor. Az Azure Monitorhoz több ügynök is elérhető, amelyek mindegyike különböző adatokat gyűjt, és különböző helyekre ír adatokat. A különböző ügynökök részletes összehasonlítását [az Azure Monitor-ügynökök áttekintése](../platform/agents-overview.md)című témakörben tekintheti meg. 

- [Log Analytics-ügynök](../platform/agents-overview.md#log-analytics-agent) – Elérhető virtuális gépek az Azure-ban, más felhőbeli környezetekben, és a helyszíni. Adatokat gyűjt az Azure Monitor naplók. Támogatja az Azure Monitor virtuális gépek és figyelési megoldások. Ez ugyanaz az ügynök, amelyet a System Center Operations Manager használ.
- [Függőségi ügynök](../platform/agents-overview.md#dependency-agent) – adatokat gyűjt a virtuális gépen futó folyamatokról és azok függőségeiről. A Log Analytics-ügynökre támaszkodva továbbítja az adatokat az Azure-ba, és támogatja az Azure Monitor virtuális gépekhez, szolgáltatástérképés Wire Data 2.0-s megoldásait.
- [Azure Diagnosztikai bővítmény](../platform/agents-overview.md#azure-diagnostics-extension) – Csak az Azure Monitor virtuális gépekhez érhető el. Adatokat gyűjthet több helyre, de elsősorban a vendég teljesítményadatok gyűjtésére az Azure Monitor metrikák a Windows virtuális gépek.
- [Telegraf ügynök](../platform/collect-custom-metrics-linux-telegraf.md) – Teljesítményadatok gyűjtése linuxos virtuális gépekről az Azure Monitor metrikákba.


## <a name="configuration-requirements"></a>Konfigurációs követelmények
Ahhoz, hogy az Azure Monitor összes szolgáltatását egy virtuális gép figyeléséhez, a figyelési adatokat a virtuális gép gazdagép és a vendég operációs rendszer az [Azure Monitor metrikák](../platform/data-platform-logs.md) és [az Azure Monitor naplók.](../platform/data-platform-logs.md) Az alábbi táblázat a gyűjtemény engedélyezéséhez végrehajtandó konfigurációt sorolja fel. Dönthet úgy, hogy nem hajtja végre az összes lépést az adott követelményektől függően.

| Konfigurációs lépés | Végrehajtott műveletek | Engedélyezett funkciók |
|:---|:---|:---|
| Nincs konfiguráció | - A gazdaplatform metrikáinak gyűjtött gazdafelület-mérőszámok.<br>- Aktivitási napló gyűjtve. | - Metrika felfedező a gazdagépnek.<br>- Metrikák riasztások a gazdagép.<br>- Tevékenységnapló-riasztások. |
| [Az Azure-figyelő engedélyezése virtuális gépekhez](#enable-azure-monitor-for-vms) | - Log Analytics ügynök telepítve.<br>- Függőségi ügynök telepítve.<br>- Vendég teljesítmény adatokat gyűjtött naplók.<br>- Folyamat és a függőség i részletek gyűjtött naplók. | - Teljesítmény grafikonok és munkafüzetek a vendég teljesítmény adatait.<br>- Napló lekérdezések vendég teljesítmény adatait.<br>- Naplózza a vendég teljesítményadataira vonatkozó riasztásokat.<br>- Függőségi térkép. |
| [Telepítse a diagnosztikai bővítményt és a telegraf ügynököt](#enable-diagnostics-extension-and-telegraf-agent) | - A vendégek teljesítményadatait a Metrics-nek gyűjtjük. | - Metrika felfedező a vendég.<br>- Metrikák riasztások vendég.  |
| [A Log Analytics munkaterületének konfigurálása](#configure-log-analytics-workspace) | - A vendégektől gyűjtött események. | - Napló lekérdezések vendég események.<br>- A vendégeseményekre vonatkozó riasztások naplózása. |
| [Diagnosztikai beállítás létrehozása a virtuális géphez](#collect-platform-metrics-and-activity-log) | - A naplókba gyűjtött platformmutatók.<br>- A naplókba gyűjtött tevékenységnapló. | - Loq lekérdezések gazdamutatók.<br>- Naplózza a gazdamutatók riasztásait.<br>- Naplólekérdezések a tevékenységnaplóhoz.

A konfigurációs lépések mindegyikét a következő szakaszok ismertetik.

### <a name="enable-azure-monitor-for-vms"></a>Az Azure-figyelő engedélyezése virtuális gépekhez
[Az Azure Monitor virtuális gépekhez](vminsights-overview.md) egy [betekintést](insights-overview.md) az Azure Monitor, amely az elsődleges eszköz a virtuális gépek figyelése az Azure Monitorban. A következő további értéket biztosítja a szabványos Azure Monitor-funkciókkal szemben.

- Egyszerűsített bevezetési A Log Analytics-ügynök és a függőségi ügynök a virtuális gép vendég operációs rendszer figyelése és a számítási feladatok lehetővé tétele érdekében. 
- Előre meghatározott felkapott teljesítménydiagramok és munkafüzetek, amelyek lehetővé teszik a virtuális gép vendég operációs rendszerének alapvető teljesítménymutatóinak elemzését.
- Függőségi térkép, amely megjeleníti az egyes virtuális gépeken futó folyamatokat, valamint az összekapcsolt összetevőket más gépekkel és külső forrásokkal.

![Azure Monitor virtuális gépekhez](media/monitor-vm-azure/vminsights-01.png)

![Azure Monitor virtuális gépekhez](media/monitor-vm-azure/vminsights-02.png)


Engedélyezze az Azure Monitor virtuális gépekhez az Azure Portal virtuálisgép-menüjében található **Insights** beállításból. A részleteket és más konfigurációs módszereket az [Azure Monitor virtuális gépekhez való engedélyezése című témakörben](vminsights-enable-overview.md) találja.

![Az Azure-figyelő engedélyezése virtuális gépekhez](media/monitor-vm-azure/enable-vminsights.png)

### <a name="configure-log-analytics-workspace"></a>A Log Analytics munkaterületének konfigurálása
Az Azure Monitor szolgáltatás a virtuális gépekhez által használt Log Analytics-ügynök adatokat küld a [Log Analytics-munkaterületre.](../platform/data-platform-logs.md#how-is-data-in-azure-monitor-logs-structured) A Log Analytics-munkaterület konfigurálásával engedélyezheti további teljesítményadatok, események és egyéb figyelési adatok gyűjtését az ügynöktől. Csak egyszer kell konfigurálni, mivel a munkaterülethez csatlakozó bármely ügynök automatikusan letölti a konfigurációt, és azonnal elkezdi a meghatározott adatok gyűjtését. 

A munkaterület konfigurációját közvetlenül az Azure Monitor virtuális gépekhez szolgáltatásából érheti el, ha az Első lépések között kiválasztja a **Munkaterület-konfigurációt.** **Get Started** Kattintson a munkaterület nevére a menü megnyitásához.

![Munkaterület konfigurálása](media/monitor-vm-azure/workspace-configuration.png)

Válassza a munkaterület menü **Speciális beállítások parancsát,** majd az **Adatok** lehetőséget az adatforrások konfigurálásához. Windows-ügynökök esetén válassza a **Windows eseménynaplók lehetőséget,** és adjon hozzá olyan gyakori eseménynaplókat, mint a *Rendszer* és *az alkalmazás*. Linux ügynökök esetén válassza a **Syslog** lehetőséget, és adjon hozzá közös létesítményeket, például *a kern* t és *a démont.* Tekintse meg [az Ügynök adatforrásait az Azure Monitorban](../platform/agent-data-sources.md) az elérhető adatforrások listáját és a konfigurálásuk részleteit. 

![Események konfigurálása](media/monitor-vm-azure/configure-events.png)


> [!NOTE]
> Beállíthatja, hogy a teljesítményszámlálók kell gyűjteni a munkaterület konfigurációját, de ez lehet redundáns az Azure Monitor virtuális gépek által gyűjtött teljesítményszámlálók. Az Azure Monitor virtuális gépekhez gyűjti a leggyakoribb számlálók gyakorisággal percenként egyszer. Csak konfigurálja a munkaterület által gyűjtendő teljesítményszámlálókat, ha olyan számlálókat szeretne gyűjteni, amelyeket még nem gyűjtött az Azure Monitor virtuális gépekhez, vagy ha meglévő lekérdezései vannak teljesítményadatok használatával.


### <a name="enable-diagnostics-extension-and-telegraf-agent"></a>Diagnosztika bővítményének és Telegraf-ügynöknek engedélyezése
A virtuális gépekhez készült Azure Monitor a Log Analytics-ügynökön alapul, amely adatokat gyűjt egy Log Analytics-munkaterületre. Ez az [Azure Monitor több funkcióját](../platform/data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) támogatja, például a [naplólekérdezéseket](../log-query/log-query-overview.md), [a naplóriasztásokat](../platform/alerts-log.md)és [a munkafüzeteket.](../platform/workbooks-overview.md) A [diagnosztikai bővítmény](../platform/diagnostics-extension-overview.md) teljesítményadatokat gyűjt a Windows virtuális gépek vendég operációs rendszeréről az Azure Storage-ba, és szükség esetén teljesítményadatokat küld az Azure Monitor [metrics-nek.](../platform/data-platform-metrics.md) Linuxos virtuális gépek esetén a [Telegraf-ügynöknek](../platform/collect-custom-metrics-linux-telegraf.md) adatokat kell küldenie az Azure Metrics számára.  Ez lehetővé teszi az Azure Monitor egyéb funkcióit, például a [metrikakezelőt](../platform/metrics-getting-started.md) és [a metrikákriasztást.](../platform/alerts-metric.md) A diagnosztikai bővítményt úgy is konfigurálhatja, hogy az Azure Event Hubs használatával eseményeket és teljesítményadatokat küldjön az Azure Monitoron kívülre.

Telepítse a diagnosztikai bővítmény egyetlen Windows virtuális gép az Azure Portalon a **diagnosztikai beállítás** i a virtuális gép menüben. Válassza ki a lehetőséget, hogy az **Azure Monitor** a **Fogadók** lapon. Ha több virtuális géphez is sablonból vagy parancssorból szeretné engedélyezni a bővítményt, olvassa el a [Telepítés és konfigurálás](../platform/diagnostics-extension-overview.md#installation-and-configuration)című témakört. A Log Analytics-ügynökkel ellentétben az összegyűjtött adatok at a bővítmény konfigurációjában határozzák meg az egyes virtuális gépeken.

![Diagnosztikai beállítás](media/monitor-vm-azure/diagnostic-setting.png)

A Telegraf ügynökök Linuxos virtuális gépeken történő konfigurálásával kapcsolatos részletekért lásd a Telegraf telepítése [és konfigurálása](../platform/collect-custom-metrics-linux-telegraf.md#install-and-configure-telegraf) című témakört. A **Diagnosztikai beállítás** menü beállítás linuxos, de csak lehetővé teszi, hogy adatokat küldjön az Azure storage-ba.

### <a name="collect-platform-metrics-and-activity-log"></a>Platformmérő számok és tevékenységnapló gyűjtése
Megtekintheti a platform metrikák és az egyes virtuálisgép-gazdagépek az Azure Portalon gyűjtött tevékenységnaplót. Gyűjtse össze ezeket az adatokat ugyanabba a Log Analytics-munkaterületre, mint az Azure Monitor virtuális gépekelemzéséhez a virtuális géphez gyűjtött egyéb figyelési adatokkal. Ez a gyűjtemény [diagnosztikai beállítással](../platform/diagnostic-settings.md)van konfigurálva. Gyűjtse össze a tevékenységnaplót [az előfizetés diagnosztikai beállításával.](../platform/diagnostic-settings.md#create-diagnostic-settings-in-azure-portal)

Platformmetrikák gyűjtése a virtuális gép diagnosztikai beállításával. Más Azure-erőforrásokkal ellentétben nem hozhat létre diagnosztikai beállítást egy virtuális géphez az Azure Portalon, de más módszert kell [használnia.](../platform/diagnostic-settings.md#create-diagnostic-settings-using-powershell) Az alábbi példák bemutatják, hogyan gyűjthetmetrikákat egy virtuális gép powershell és CLI használatával.

```powershell
Set-AzDiagnosticSetting -Name vm-diagnostics -ResourceId "/subscriptions/monitor diagnostic-settings create \
xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" -Enabled $true -MetricCategory AllMetrics -workspaceId "/subscriptions/monitor diagnostic-settings create \
xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace"
```

```CLI
az monitor diagnostic-settings create \
--name VM-Diagnostics 
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/my-resource-group/providers/microsoft.operationalinsights/workspaces/my-workspace

```

## <a name="monitoring-in-the-azure-portal"></a>Figyelés az Azure Portalon 
Miután konfigurálta a figyelési adatok gyűjtését egy virtuális géphez, több lehetősége is van az Azure Portalon való elérésére:

- Az **Azure Monitor** menüsegítségével az összes figyelt erőforrásból származó adatok eléréséhez. 
- Használja az Azure Monitor virtuális gépek figyelése készletek nagy méretekben.
- Egyetlen virtuális gép adatait elemezheti az Azure Portal menüjéből. Az alábbi táblázat a virtuális gépek menüjének figyelésére szolgáló különböző lehetőségeket sorolja fel.

![Figyelés az Azure Portalon](media/monitor-vm-azure/monitor-menu.png)

| Menü beállítás | Leírás |
|:---|:---|
| Áttekintés | A virtuálisgép-állomás [platformmetrikáit](../platform/data-platform-metrics.md) jeleníti meg. Kattintson egy grafikonra, hogy ezekkel az adatokkal dolgozva [a metrikakezelőben.](../platform/metrics-getting-started.md) |
| Tevékenységnapló | [Az](../platform/activity-log-view.md) aktuális virtuális gépre szűrt tevékenységnapló-bejegyzések. |
| Insights | Megnyitja [az Azure Monitor virtuális gépek a](../insights/vminsights-overview.md) térkép az aktuális virtuális gép kijelölt. |
| Riasztások | Az aktuális virtuális gép [riasztásait](../platform/alerts-overview.md) tekinti meg.  |
| Mérőszámok | Nyissa meg [a metrikakezelőt](../platform/metrics-getting-started.md) úgy, hogy a hatókör az aktuális virtuális gépre van beállítva. |
| Diagnosztikai beállítások | Az aktuális virtuális gép [diagnosztikai bővítményének](../platform/diagnostics-extension-overview.md) engedélyezése és konfigurálása. |
| Tanácsadói ajánlások | Javaslatok az aktuális virtuális géphez az [Azure Advisortól.](/azure/advisor/) |
| Naplók | Nyissa meg a [Log Analytics](../log-query/log-query-overview.md#what-is-log-analytics) [hatókörét](../log-query/scope.md) az aktuális virtuális gépre állítva. |
| Kapcsolatfigyelő | Nyissa meg [a Hálózatfigyelő kapcsolatfigyelőjének figyelője](../../network-watcher/connection-monitor-preview.md) az aktuális virtuális gép és más virtuális gépek közötti kapcsolatok figyeléséhez. |


## <a name="analyzing-metric-data"></a>Metrikaadatok elemzése
A metrikák kezelője segítségével elemezheti a virtuális gépek metrikák metrikák megnyitásával **metrikák** megnyitásával. Az [azure metrics-kezelő használatának megkezdéséről](../platform/metrics-getting-started.md) az eszköz használatával kapcsolatos részletekről. 

A virtuális gépek két névteret használnak metrikákhoz:

| Névtér | Leírás |
|:---|:---|
| Virtuálisgép-gazda | Host metrikák automatikusan gyűjtött az összes Azure virtuális gépek. A mérőszámok részletes listája a [Microsoft.Compute/virtualMachines oldalon.](../platform/metrics-supported.md#microsoftcomputevirtualmachines) |
| Virtuális gép vendége | Vendég operációs rendszer metrikák gyűjtött virtuális gépek diagnosztikai bővítmény telepítve van, és úgy van beállítva, hogy küldjön az Azure Monitor fogadó. |

![Mérőszámok](media/monitor-vm-azure/metrics.png)

## <a name="analyzing-log-data"></a>Naplóadatok elemzése
Az Azure virtuális gépek a következő adatokat gyűjti az Azure Monitor naplók. 

Az Azure Monitor virtuális gépekhez lehetővé teszi egy előre meghatározott teljesítményszámlálók, amelyek az *InsightsMetrics* tábla gyűjteménye. Ez ugyanaz a tábla, amelyet az [Azure Monitor tárolókhoz](container-insights-overview.md)használ. 

| Adatforrás | Követelmények | Táblák |
|:---|:---|:---|
| Azure Monitor virtuális gépekhez | Engedélyezze az egyes virtuális gépeken. | InsightsMetrics<br>VMBoundPort között<br>VMComputer<br>Virtuális gépkapcsolat<br>Virtuális gép feldolgozása<br>A részletekért [tekintse meg a naplók lekérdezése az Azure Monitor virtuális gépekhez.](vminsights-log-search.md) |
| Tevékenységnapló | Az előfizetés diagnosztikai beállítása. | AzureActivity |
| Állomás metrikák | A virtuális gép diagnosztikai beállítása. | AzureMetrics |
| A vendég operációs rendszer adatforrásai | Engedélyezze a Log Analytics-ügynököt, és konfigurálja az adatforrásokat. | Tekintse meg az egyes adatforrások dokumentációját. |


> [!NOTE]
> A Log Analytics-ügynök által gyűjtött teljesítményadatok a *Perf-táblába* írnak, míg az Azure Monitor virtuális gépek az InsightsMetrics táblába *gyűjti azokat.* Ezek ugyanazok az adatok, de a táblák szerkezete eltérő. Ha a *Perf*alapú meglévő lekérdezésekkel rendelkezik, a rendszer újra kell írnia az *InsightsMetrics*használatához.


## <a name="alerts"></a>Riasztások
[Riasztások az](../platform/alerts-overview.md) Azure Monitor proaktív módon értesíti Önt, ha fontos feltételek találhatók a figyelési adatokat, és potenciálisan indít egy műveletet, például egy logikai alkalmazás indítása vagy egy webhook hívása. A riasztási szabályok határozzák meg a riasztás létrehozásának időpontjára használt logikát. Az Azure Monitor gyűjti a riasztási szabályok által használt adatokat, de szabályokat kell létrehoznia a riasztási feltételek meghatározásához az Azure-előfizetésben.

A következő szakaszok ismertetik a riasztási szabályok típusait és az egyes használatukra vonatkozó javaslatokat. Ez a javaslat a riasztási szabály típusának funkcióin és költségén alapul. A riasztások díjszabásáról az [Azure Monitor díjszabása](https://azure.microsoft.com/pricing/details/monitor/).


### <a name="activity-log-alert-rules"></a>Tevékenységnapló-riasztási szabályok
[A tevékenységnapló riasztási szabályai](../platform/alerts-activity-log.md) akkor jelennek meg, ha a tevékenységnaplóban egy adott feltételeknek megfelelő bejegyzést hoznak létre. Nincs költség, így kell az első választás, ha a szükséges logika a tevékenységnaplóban van. 

A tevékenységnapló-riasztások célerőforrása lehet egy adott virtuális gép, az erőforráscsoport összes virtuális gépe vagy az előfizetés összes virtuális gépe.

Például hozzon létre egy riasztást, ha egy kritikus virtuális gép leáll kiválasztásával a *Kikapcsolás virtuális gép* a jel nevét.

![Tevékenységnapló riasztása](media/monitor-vm-azure/activity-log-alert.png)


### <a name="metric-alert-rules"></a>Metrika riasztási szabályai
[Metrika riasztási szabályok](../platform/alerts-metric.md) akkor tűz, ha egy metrika értéke meghaladja a küszöbértéket. Megadhat egy adott küszöbértéket, vagy engedélyezheti, hogy az Azure Monitor dinamikusan határozzon meg egy küszöbértéket az előzményadatok alapján.  Metrikariasztások at, amikor csak lehetséges, metrikaadatokkal, mivel azok olcsóbbak, és jobban reagálnak, mint a naplóriasztási szabályok. Ők is állapotalapú jelenti, hogy megoldja magukat, amikor a metrika alá csökken a küszöbérték alá.

A tevékenységnapló-riasztások célerőforrása lehet egy adott virtuális gép vagy egy erőforráscsoport összes virtuális gépe.

Ha például riasztást szeretne létrehozni, ha egy virtuális gép processzora meghaladja az adott értéket, hozzon létre egy metrikariasztási szabályt *a Százalék PROCESSZTa* jeltípusként. Állítson be egy adott küszöbértéket, vagy engedélyezze az Azure Monitornak a dinamikus küszöbérték beállítását. 

![Metrikariasztás](media/monitor-vm-azure/metric-alert.png)

### <a name="log-alerts"></a>Naplóriasztások
[A naplóriasztási szabályok](../platform/alerts-log.md) akkor jelennek meg, ha az ütemezett naplólekérdezés eredménye megfelel bizonyos feltételeknek. A naplólekérdezési riasztások a legdrágábbak és a legkevésbé reagálnak a riasztási szabályokra, de a legkülönbözőbb adatokhoz férnek hozzá, és összetett logikát hajthatnak végre, amelyet a többi riasztási szabály nem hajtvégre. 

A naplólekérdezés célerőforrása egy Log Analytics-munkaterület. A lekérdezés adott számítógépeinek szűrése.

Például hozzon létre egy riasztást, amely ellenőrzi, ha egy adott erőforráscsoport ban egy virtuális gép offline állapotban van, használja a következő lekérdezést, amely visszaadja a rekordot minden számítógép, amely kihagyott egy szívverés az elmúlt tíz percben. Használjon 1-es küszöbértéket, amely akkor aktiválódik, ha legalább egy számítógép szívverése hiányzik.

```kusto
Heartbeat
| where TimeGenerated < ago(10m)
| where ResourceGroup == "my-resource-group"
| summarize max(TimeGenerated) by Computer
```

![Naplóriasztás](media/monitor-vm-azure/log-alert-01.png)

Ha az előfizetés bármely Windows virtuális gépén túl sok sikertelen bejelentkezés történt, használjon riasztást, amely az elmúlt egy órában minden sikertelen bejelentkezési eseményhez visszaad egy rekordot. Használja a sikertelen bejelentkezések által engedélyezett számú küszöbértéket. 

```kusto
Event
| where TimeGenerated < ago(1hr)
| where EventID == 4625
```

![Naplóriasztás](media/monitor-vm-azure/log-alert-02.png)


## <a name="system-center-operations-manager"></a>System Center Operations Manager
A System Center Operations Manager (SCOM) részletesen figyeli a virtuális gépeken lévő számítási feladatokat. A figyelési platformok és a különböző megvalósítási stratégiák összehasonlítását a [felhőfigyelési útmutatóban](https://docs.microsoft.com/azure/cloud-adoption-framework/manage/monitor/) talál.

Ha rendelkezik egy meglévő SCOM-környezettel, amelyet továbbra is használni kíván, integrálhatja azt az Azure Monitorral, hogy további funkciókat biztosítson. Az Azure Monitor által használt Log Analytics-ügynök megegyezik az SCOM-hoz használt, így figyelt virtuális gépek adatokat küldeni mindkettőnek. Továbbra is hozzá kell adnia az ügynököt az Azure Monitor virtuális gépekhez, és konfigurálnia kell a munkaterületet, hogy további adatokat gyűjtsön a fent megadottak szerint, de a virtuális gépek módosítás nélkül továbbra is futtathatják meglévő felügyeleti csomagjaikat egy SCOM-környezetben.

Az Azure Monitor funkciói, amelyek egy meglévő SCOM-funkciót bővítnek, a következők:

- A Log Analytics segítségével interaktívan elemezheti a napló- és teljesítményadatokat.
- A naplóriasztások segítségével riasztási feltételeket határozhatsz meg több virtuális gépen, és olyan hosszú távú trendeket használhat, amelyek nem lehetségesek az SCOM-riasztások használatával.   

A meglévő SCOM-felügyeleti csoport nak a Log Analytics-munkaterülethez való csatlakoztatásával kapcsolatos részletekért tekintse meg az [Operations Manager csatlakoztatása az Azure Monitorhoz.](../platform/om-agents.md)


## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan elemezheti az adatokat az Azure Monitor naplóiban naplólekérdezések használatával.](../log-query/get-started-queries.md)
* [Ismerje meg a riasztások at metrikák és naplók az Azure Monitorban.](../platform/alerts-overview.md)

