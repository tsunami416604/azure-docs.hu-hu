A virtuális gépek figyeléséhez gyűjtésére, megtekintése és elemzése diagnosztikai sok lehetőségek előnyeit, és adatok naplózása. Ehhez az egyszerű [figyelési](../articles/monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) a virtuális gép is használhatja az Áttekintés képernyő a virtuális gép az Azure portálon. Használhat [bővítmények](../articles/virtual-machines/windows/extensions-features.md) diagnosztika konfigurálása a virtuális gépeken további metrika adatok gyűjtéséért felelős ügyfélfeladatot. Is használhatja további figyelési beállítások, például a [Application Insights](../articles/application-insights/app-insights-overview.md) és [Naplóelemzési](../articles/log-analytics/log-analytics-overview.md).

## <a name="diagnostics-and-metrics"></a>Diagnosztika és metrikák 

Állítsa be, és figyelheti a gyűjteménye [diagnosztikai adatainak](https://docs.microsoft.com/cli/azure/vm/diagnostics) használatával [metrikák](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md) az Azure-portálon, az Azure parancssori felület, Azure PowerShell és programozási alkalmazások alkalmazásprogramozási felületek (API). Megteheti például a következőt:

- **Tekintse át az alapvető metrikákat a virtuális gép számára.** Az Azure portál áttekintése képernyőjén a feltüntetett alapvető metrikákat közé tartozik a CPU-használat, a hálózathasználatot, a lemez bájt, és a lemez műveletek másodpercenkénti száma összesen.

- **Rendszerindítási diagnosztika gyűjtésének engedélyezése és megtekintése, az Azure-portál használatával.** Amikor a saját kép Azure vagy akár egy platform lemezképet rendszerindítást, miért egy virtuális gép nem indítható állapotba lekérdezi számos oka lehet. Könnyen engedélyezheti rendszerindítási diagnosztika a virtuális gépek létrehozásakor kattintva **engedélyezve** vonatkozó rendszerindítási diagnosztika a figyelés szakaszban, a Beállítások képernyő.

    Rendszerindító virtuális gépeket, mert a rendszerindítási diagnosztikai ügynök rendszerindító kimeneti rögzíti, és azt az Azure storage tárolja. Ezeket az adatokat a virtuális gép rendszerindító probléma megoldásához használható. Rendszerindítási diagnosztika nem automatikusan engedélyezve vannak a virtuális gépek létrehozásakor, a parancssori eszközök. Ahhoz, hogy a rendszerindítási diagnosztika, egy tárfiókot kell létrehozni rendszerindító naplók tárolásához. Ha engedélyezi az Azure-portálon a rendszerindítási diagnosztikát, a storage-fiók automatikusan létrejön.

    Ha nem engedélyezi a rendszerindítási diagnosztika, a virtuális gép létrehozásakor, mindig engedélyezheti azt később használatával [Azure CLI](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics), [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmbootdiagnostics), vagy egy [Azure Resource Manager sablon](../articles/virtual-machines/windows/extensions-diagnostics-template.md).

- **A vendég operációs rendszer diagnosztikai adatok gyűjtésének engedélyezése.** Amikor létrehoz egy virtuális Gépet, lehetősége van a vendég operációs rendszer diagnosztika engedélyezéséhez beállítások képernyőn. Ha engedélyezte a diagnosztikai adatgyűjtés, a [IaaSDiagnostics bővítmény Linux](../articles/virtual-machines/linux/diagnostic-extension.md) vagy a [IaaSDiagnostics bővítmény Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md) bekerül a virtuális Gépet, amely lehetővé teszi további gyűjtése lemez, a Processzor és memória adatok.

    A gyűjtött diagnosztikai adatokat használ, a a virtuális gépek automatikus skálázás állíthatja be. Beállíthatja úgy is bejegyzéseit, amelyek az adatok tárolásához és értesítések beállítása lehetővé teszi, hogy ha a teljesítmény nem észlel.

## <a name="alerts"></a>Riasztások

Létrehozhat [riasztások](../articles/monitoring-and-diagnostics/monitoring-overview-alerts.md) specifikus teljesítménymutatók alapján. Így is riasztást kapcsolatos problémák például átlagos CPU-használat meghaladja a meghatározott küszöbérték, vagy a rendelkezésre álló szabad lemezterületet bizonyos alá süllyed. Riasztások konfigurálható a [Azure-portálon](../articles/monitoring-and-diagnostics/insights-alerts-portal.md)használatával [Azure PowerShell](../articles/monitoring-and-diagnostics/insights-alerts-powershell.md), vagy a [Azure CLI](../articles/monitoring-and-diagnostics/insights-alerts-command-line-interface.md).

## <a name="azure-service-health"></a>Azure Service Health

[Az Azure szolgáltatás állapota](../articles/service-health/service-health-overview.md) személyre szabott segítséget és támogatást biztosít, ha az Azure-szolgáltatásokkal kapcsolatos hibákat hatása a felhasználókra, és segít felkészülni jövőbeli a tervezett karbantartások. Az Azure szolgáltatás állapotát, és a csoportok használatával a megcélzott és a rugalmas értesítések riasztást küld.

## <a name="azure-resource-health"></a>Azure Resource Health

[Az Azure Resource health](../articles/service-health/resource-health-overview.md) segít diagnosztizálni és Azure hibát az erőforrások hatással van. Ha segítségre van szüksége. Tájékoztatja az erőforrásai aktuális és korábbi állapotáról, és segít a problémák kezelésében. A Resource Health műszaki támogatást nyújt, ha segítségre van szüksége az Azure szolgáltatásait érintő problémákkal kapcsolatban.

## <a name="logs"></a>Logs

A [Azure tevékenységnapló](../articles/monitoring-and-diagnostics/monitoring-overview-activity-logs.md) van egy előfizetési napló, amely történt az Azure-előfizetés szintű események betekintést nyújt. A napló egy tartományt az Azure Resource Manager működési adatokat a frissítésekre a szolgáltatás állapotával kapcsolatos események adatait tartalmazza. Tevékenységnapló kattintson a napló megtekintéséhez a virtuális gép az Azure portálon.

A műveletnapló rendelkező a lehetőségek a következők:

- Hozzon létre egy [tevékenységnapló esemény riasztás](../articles/monitoring-and-diagnostics/monitoring-overview-activity-logs.md).
- [Az Eseményközpontok felé adatfolyamként](../articles/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs.md) egy külső szolgáltatás vagy az egyéni elemzési megoldások, például a Power bi szempontjából.
- A Power bi használatával elemezze a [Power bi tartalomcsomag](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
- [Mentse a tárfiók](../articles/monitoring-and-diagnostics/monitoring-archive-activity-log.md) archív vagy manuális ellenőrzést. Megadhatja, hogy a megőrzési időtartama (napokban) a napló profilt használ.

Emellett tevékenység naplóadatokat használatával [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/), a [Azure CLI](https://docs.microsoft.com/cli/azure/monitor), vagy [figyelő REST API-k](https://docs.microsoft.com/rest/api/monitor/).

[Az Azure diagnosztikai naplók](../articles/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) gazdag, gyakori a működésével kapcsolatos adatokat biztosít a virtuális gép által kibocsátott naplók. Diagnosztikai naplók eltérnek a műveletnapló alapján információkat a virtuális Gépen belül végrehajtott műveletek megadásával.

A diagnosztikai naplókat a lehetőségek a következők:

- [Mentse azokat egy tárfiókot](../articles/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md) naplózási vagy manuális ellenőrzést. Megadhatja, hogy a megőrzési időt (napokban) erőforrás diagnosztikai beállításait.
- [Az adatfolyamot őket az Event Hubs a](../articles/monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md) egy külső szolgáltatás vagy az egyéni elemzési megoldások, például a Power bi szempontjából.
- Elemezheti őket a [OMS Naplóelemzési](../articles/log-analytics/log-analytics-azure-storage.md).

## <a name="advanced-monitoring"></a>Speciális figyelés

- [Az Operations Management Suite (OMS)](https://docs.microsoft.com/azure/operations-management-suite/) figyelési riasztási és riasztási szervizelési képességeket biztosít a felhő között, és a helyszíni eszközök. Egy bővítmény telepítheti egy [Linux virtuális gép](../articles/virtual-machines/linux/extensions-oms.md) vagy egy [Windows virtuális gép](../articles/virtual-machines/windows/extensions-oms.md) , amely az OMS-ügynököt telepít, és regisztrálja a virtuális gép egy meglévő OMS-munkaterület be.

- [Naplófájl Analytics](../articles/log-analytics/log-analytics-overview.md) szolgáltatás a OMS, amely figyeli a felhőalapú és helyszíni környezetek karbantartásához azok rendelkezésre állását és teljesítményét. A felhőben és a helyszíni környezetben található erőforrások által létrehozott, valamint egyéb figyelési eszközök által biztosított adatokat gyűjtésével biztosítsa elemzést több forráson.

    A Windows és Linux virtuális gépek az ajánlott módszer a naplók és a metrikák gyűjtéséhez van a Naplóelemzési ügynök telepítésével. A Naplóelemzési ügynök telepítése a virtuális gép legkönnyebben keresztül a [napló Analytics Virtuálisgép-bővítmény](../articles/log-analytics/log-analytics-azure-vm-extension.md). A bővítmény használatával egyszerűbbé teszi a telepítési folyamat, és automatikusan konfigurálja az adatokat küldeni a Naplóelemzési munkaterület, melyet a az ügynök. Az ügynök is frissítése automatikusan történik, győződjön meg arról, hogy rendelkezik-e a legújabb funkcióit és javításokat.

- [Hálózati figyelő](../articles/network-watcher/network-watcher-monitoring-overview.md) lehetővé teszi a figyelheti a virtuális gép és a kapcsolódó erőforrások az, hogy a hálózati kapcsolatát. A hálózati figyelő ügynök bővítmény telepítheti egy [Linux virtuális gép](../articles/virtual-machines/linux/extensions-nwa.md) vagy egy [Windows virtuális gép](../articles/virtual-machines/windows/extensions-nwa.md).

## <a name="next-steps"></a>Következő lépések
- Végezze el a lépéseket [figyelheti a Windows rendszerű virtuális gép az Azure PowerShell](../articles/virtual-machines/windows/tutorial-monitoring.md) vagy [figyelése az Azure parancssori Felülettel rendelkező Linux virtuális gép](../articles/virtual-machines/linux/tutorial-monitoring.md).
- További információk az ajánlott eljárásokat körül [megfigyelési és diagnosztikai](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).
