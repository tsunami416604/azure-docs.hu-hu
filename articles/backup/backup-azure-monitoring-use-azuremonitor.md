---
title: 'Azure Backup: a Azure Backup figyelése Azure Monitor'
description: Figyelje Azure Backup munkaterheléseket, és hozzon létre egyéni riasztásokat Azure Monitor használatával.
ms.reviewer: pullabhk
author: dcurwin
manager: carmonm
keywords: Log Analytics; Azure Backup; Riasztások Diagnosztikai beállítások; Műveleti csoportok
ms.service: backup
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: dacurwin
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: e8f162b9dc84b863c54d92313307ef4abca76ebb
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74074236"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>A monitor méretezése Azure Monitor használatával

A Azure Backup [beépített figyelési és riasztási képességeket](backup-azure-monitoring-built-in-monitor.md) biztosít egy Recovery Services-tárolóban. Ezek a képességek további felügyeleti infrastruktúra nélkül érhetők el. Ez a beépített szolgáltatás azonban a következő esetekben korlátozott:

- Ha több Recovery Services-tároló adatait figyeli az előfizetések között
- Ha az előnyben részesített értesítési csatorna *nem* e-mail-cím
- Ha a felhasználók további forgatókönyvekhez szeretne riasztásokat kapni
- Ha olyan helyszíni összetevőtől szeretné megtekinteni az információkat, mint például a System Center Data Protection Manager az Azure-ban, a portál nem jelenik meg a [**biztonsági mentési feladatokban**](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) vagy a [**biztonsági mentési riasztásokban**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)

## <a name="using-log-analytics-workspace"></a>Log Analytics munkaterület használata

> [!NOTE]
> Az Azure virtuális gépek biztonsági mentései, a Azure Backup ügynök, a System Center Data Protection Manager, az Azure-beli virtuális gépek SQL-biztonsági mentései, valamint a Azure Files megosztási biztonsági mentések a Log Analytics munkaterületre állnak a diagnosztikai beállítások segítségével. A Microsoft Azure Backup-kiszolgáló (MABS) támogatását hamarosan felveszi

A skála figyeléséhez vagy jelentéséhez két Azure-szolgáltatás képességeire van szükség. A *diagnosztikai beállítások* több Azure Resource Manager erőforrás adatait küldik egy másik erőforrásnak. A *log Analytics* olyan egyéni riasztásokat hoz létre, amelyekben a műveleti csoportok más értesítési csatornákat is meghatározhatnak.

A következő részekben részletesen ismertetjük, hogyan használhatók a Log Analytics a Azure Backup a méretekben való figyelésére.

### <a name="configure-diagnostic-settings"></a>Diagnosztikai beállítások konfigurálása

Azure Resource Manager erőforrásokat, például a Recovery Services-tárolót, rögzítse az ütemezett műveletekkel és a felhasználó által aktivált műveletekkel kapcsolatos információkat diagnosztikai adatként.

A figyelés szakaszban válassza a **diagnosztikai beállítások** elemet, és adja meg a Recovery Services tároló diagnosztikai adatcélját.

![A Recovery Services tároló diagnosztikai beállítása, Log Analytics](media/backup-azure-monitoring-laworkspace/rs-vault-diagnostic-setting.png)

Egy Log Analytics munkaterületet egy másik előfizetésből is megcélozhat. Ha egyetlen helyen szeretné figyelni a tárolókat az előfizetések között, válassza ki ugyanazt a Log Analytics munkaterületet több Recovery Services-tárolóhoz. Ha a Log Analytics munkaterülethez Azure Backup kapcsolódó összes információt szeretné csatornára irányítani, válassza a **AzureBackupReport** lehetőséget a naplóként.

> [!IMPORTANT]
> A konfiguráció befejezését követően 24 órát kell várnia a kezdeti leküldések befejezésére. A kezdeti adatküldés után a rendszer az összes eseményt leküldi a cikk későbbi részében leírtak szerint a [gyakoriság szakaszban](#diagnostic-data-update-frequency).

### <a name="deploy-a-solution-to-the-log-analytics-workspace"></a>Megoldás üzembe helyezése a Log Analytics munkaterületen

> [!IMPORTANT]
> Megjelent egy frissített, több nézetből álló [sablon](https://azure.microsoft.com/resources/templates/101-backup-la-reporting/) a Azure Backup-ben végzett La-alapú figyeléshez és jelentéskészítéshez. Vegye figyelembe, hogy a [korábbi megoldást](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) használó felhasználók továbbra is látni fogják a munkaterületeken, még az új megoldás telepítése után is. A régi megoldás azonban bizonyos kisebb sémák módosítása miatt pontatlan eredményeket biztosíthat. Ezért a felhasználóknak telepíteniük kell az új sablont.

Miután az adatLog Analytics munkaterületen belül található, [helyezzen üzembe egy GitHub-sablont](https://azure.microsoft.com/resources/templates/101-backup-la-reporting/) , hogy a rendszer log Analytics az adatmegjelenítéshez. A munkaterület megfelelő azonosításához győződjön meg arról, hogy ugyanazt az erőforráscsoportot, a munkaterület nevét és a munkaterület helyét adja meg. Ezután telepítse ezt a sablont a munkaterületre.

### <a name="view-azure-backup-data-by-using-log-analytics"></a>Azure Backup adatai megtekintése Log Analytics használatával

A sablon üzembe helyezése után a Azure Backup figyelésére és jelentéskészítésére szolgáló megoldás a munkaterület összefoglaló régiójában jelenik meg. Az összegzéshez kövesse az alábbi elérési utak egyikét:

- **Azure monitor**: a **bepillantások** szakaszban válassza a **továbbiak** lehetőséget, majd válassza ki a megfelelő munkaterületet.
- **Log Analytics munkaterületek**: válassza ki a megfelelő munkaterületet, majd az **általános**területen válassza a **munkaterület összegzése**elemet.

![A Log Analytics monitorozási és jelentéskészítési csempéi](media/backup-azure-monitoring-laworkspace/la-azurebackup-overview-dashboard.png)

Az áttekintő csempék bármelyikét kiválasztva további információkat jeleníthet meg. Íme néhány jelentés, amelyet látni fog:

- Nem naplózott biztonsági mentési feladatok

   ![Log Analytics gráfok a biztonsági mentési feladatokhoz](media/backup-azure-monitoring-laworkspace/la-azurebackup-backupjobsnonlog.png)

- Azure-erőforrások biztonsági mentésével kapcsolatos riasztások

   ![Log Analytics gráf a visszaállítási feladatokhoz](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertsazure.png)

Hasonlóképpen, ha a többi csempére kattint, megtekintheti a visszaállítási feladatok, a felhőalapú tárolás, a biztonsági mentési elemek, a helyszíni erőforrások biztonsági mentésével kapcsolatos riasztások és a biztonsági mentési feladatok naplóját.

Ezek a diagramok a sablonnal együtt érhetők el. A diagramokat szerkesztheti, vagy további gráfokat is hozzáadhat, ha szükséges.

### <a name="create-alerts-by-using-log-analytics"></a>Riasztások létrehozása Log Analytics használatával

Azure Monitor a saját riasztásokat a Log Analytics munkaterületen hozhatja létre. A munkaterületen Azure- *műveleti csoportokat* használ a kívánt értesítési mechanizmus kiválasztásához.

> [!IMPORTANT]
> További információ a lekérdezés létrehozásának költségéről: [Azure monitor díjszabása](https://azure.microsoft.com/pricing/details/monitor/).

Válassza ki bármelyik gráfot a Log Analytics munkaterület **naplók** szakaszának megnyitásához. A **naplók** szakaszban szerkessze a lekérdezéseket, és hozzon létre riasztásokat.

![Riasztás létrehozása Log Analytics munkaterületen](media/backup-azure-monitoring-laworkspace/la-azurebackup-customalerts.png)

Ha **új riasztási szabályt**választ, megnyílik a Azure monitor riasztás létrehozása lap, ahogy az alábbi képen is látható. Itt az erőforrás már meg van jelölve Log Analytics munkaterületként, és a műveleti csoport integrációja van megadva.

![Az Log Analytics riasztás – létrehozási oldal](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>Riasztási feltétel

A riasztások meghatározó jellemzője az aktiválási feltétel. Válassza ki a **feltételt** a Kusto-lekérdezés automatikus betöltéséhez a **naplók** lapon az alábbi ábrán látható módon. Itt szerkesztheti a feltételt, hogy megfeleljen az igényeinek. További információ: [Sample Kusto-lekérdezések](#sample-kusto-queries).

![Riasztási feltétel beállítása](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

Ha szükséges, szerkesztheti a Kusto-lekérdezést. Válassza ki a küszöbértéket, az időszakot és a gyakoriságot. A küszöbérték határozza meg, hogy a riasztás Mikor kerül kiemelésre. Az időszak az az időablak, amelyben a lekérdezés fut. Ha például a küszöbérték nagyobb nullánál, az időszak 5 perc, a gyakoriság pedig 5 perc, a szabály 5 percenként futtatja a lekérdezést, és az előző 5 percet tekinti át. Ha az eredmények száma nagyobb, mint 0, a rendszer értesítést küld a kiválasztott műveleti csoporton.

#### <a name="alert-action-groups"></a>Riasztási műveleti csoportok

Az értesítési csatorna megadásához használjon műveleti csoportot. Az elérhető értesítési mechanizmusok megjelenítéséhez a **műveleti csoportok**területen válassza az **új létrehozása**lehetőséget.

![Elérhető értesítési mechanizmusok a "műveleti csoport hozzáadása" ablakban](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

Az összes riasztási és figyelési követelményt Log Analytics egyedül is kielégítheti, vagy a Log Analytics használatával kiegészítheti a beépített értesítéseket.

További információ: a [naplók létrehozása, megtekintése és kezelése Azure monitor használatával,](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) valamint [műveleti csoportok létrehozása és kezelése a Azure Portalban](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups).

### <a name="sample-kusto-queries"></a>Példa Kusto-lekérdezésekre

Az alapértelmezett diagramok olyan alapszintű forgatókönyvekhez biztosítanak Kusto-lekérdezéseket, amelyeken riasztásokat hozhat létre. A lekérdezéseket úgy is módosíthatja, hogy megkapják azokat az adatkéréseket, amelyekről riasztást szeretne kapni. Illessze be a következő minta Kusto-lekérdezéseket a **naplók** lapra, majd hozzon létre riasztásokat a lekérdezéseken:

- Minden sikeres biztonsági mentési feladat

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Completed"
    ````

- Az összes sikertelen biztonsági mentési feladat

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Failed"
    ````

- Minden sikeres Azure-beli virtuális gép biztonsági mentési feladata

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "VM" and BackupManagementType_s == "IaaSVM"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

- Az összes sikeres SQL-napló biztonsági mentési feladata

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s == "Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "SQLDataBase" and BackupManagementType_s == "AzureWorkload"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

- Az összes sikeres Azure Backup ügynök feladatai

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | extend JobOperationSubType_s = columnifexists("JobOperationSubType_s", "")
    | where OperationName == "Job" and JobOperation_s == "Backup" and JobStatus_s == "Completed" and JobOperationSubType_s != "Log" and JobOperationSubType_s != "Recovery point_Log"
    | join kind=inner
    (
        AzureDiagnostics
        | where Category == "AzureBackupReport"
        | where OperationName == "BackupItem"
        | where SchemaVersion_s == "V2"
        | where BackupItemType_s == "FileFolder" and BackupManagementType_s == "MAB"
        | distinct BackupItemUniqueId_s, BackupItemFriendlyName_s
        | project BackupItemUniqueId_s , BackupItemFriendlyName_s
    )
    on BackupItemUniqueId_s
    | extend Vault= Resource
    | project-away Resource
    ````

### <a name="diagnostic-data-update-frequency"></a>Diagnosztikai Adatfrissítés gyakorisága

A tárolóból származó diagnosztikai adatok bekerülnek a Log Analytics munkaterületre, és némi késéssel. Minden esemény *20 – 30 perccel* a log Analytics munkaterületen érkezik, miután leküldte a Recovery Services-tárolóból. További részletek a lag-ról:

- Az összes megoldás esetében a biztonsági mentési szolgáltatás beépített riasztásait azonnal leküldi a rendszer a létrehozásuk után. Így általában 20 – 30 perc múlva jelennek meg az Log Analytics munkaterületen.
- Minden megoldásban az igény szerinti biztonsági mentési feladatok és a visszaállítási feladatok a *befejezésük*után azonnal leküldve lesznek.
- Az SQL Backup kivételével az összes megoldás esetében az ütemezett biztonsági mentési feladatok a *befejezésük*után azonnal leküldve lesznek.
- Az SQL Backup szolgáltatásban, mivel a naplók biztonsági mentései 15 percenként fordulnak elő, az összes befejezett ütemezett biztonsági mentési feladatra vonatkozó információ, beleértve a naplókat, a kötegbe kerül, és 6 óránként küldi el azokat.
- Minden megoldásban, például a biztonsági másolati elem, a házirend, a helyreállítási pontok, a tárterület és így tovább, *naponta legalább egyszer* leküldjük.
- A biztonsági mentési konfiguráció (például a házirend módosítása vagy a szerkesztési szabályzat) változása elindítja az összes kapcsolódó biztonsági mentési információt.

## <a name="using-the-recovery-services-vaults-activity-logs"></a>Az Recovery Services-tár tevékenység-naplófájljainak használata

> [!CAUTION]
> Az alábbi lépések csak az Azure-beli *virtuális gépek biztonsági mentésére vonatkoznak.* Ezeket a lépéseket nem használhatja olyan megoldásokhoz, mint például az Azure Backup ügynök, az Azure-ban SQL-alapú biztonsági másolatok vagy Azure Files.

A tevékenység-naplók segítségével értesítéseket kaphat az eseményekről, például a biztonsági mentés sikerességéről. A kezdéshez kövesse az alábbi lépéseket:

1. Jelentkezzen be a Azure Portalba.
1. Nyissa meg a megfelelő Recovery Services-tárolót.
1. A tároló tulajdonságai között nyissa meg a **tevékenység napló** szakaszt.

A megfelelő napló azonosítása és riasztás létrehozása:

1. A következő képen látható szűrők alkalmazásával ellenőrizze, hogy a sikeres biztonsági mentésekhez kapott-e tevékenység naplókat. Módosítsa a **TimeSpan** értékét szükség szerint a rekordok megtekintéséhez.

   ![Az Azure-beli virtuális gépek biztonsági másolatainak keresésére szolgáló szűrési műveletek](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

1. A megfelelő részletek megtekintéséhez válassza ki a művelet nevét.
1. Válassza az **új riasztási szabály** lehetőséget a **szabály létrehozása** lap megnyitásához.
1. Hozzon létre egy riasztást a [műveletnapló riasztások létrehozása, megtekintése és kezelése Azure monitor használatával](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)című témakör lépéseit követve.

   ![Új riasztási szabály](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

Itt az erőforrás maga a Recovery Services-tároló. Ismételje meg ugyanezeket a lépéseket minden olyan tárolónál, amelyben a tevékenységek naplóján keresztül szeretne értesítést kapni. A feltétel nem tartalmaz küszöbértéket, időszakot vagy gyakoriságot, mert ez a riasztás események alapján történik. A kapcsolódó tevékenység naplójának létrehozása után a rendszer riasztást küld.

## <a name="using-log-analytics-to-monitor-at-scale"></a>Log Analytics használata a nagy léptékű figyeléshez

Megtekintheti a tevékenységek naplóiból létrehozott összes riasztást és Log Analytics munkaterületeket Azure Monitor. Csak nyissa meg a **riasztások** panelt a bal oldalon.

Bár a tevékenység-naplókon keresztül kaphat értesítéseket, javasoljuk, hogy használja a Log Analyticst, és ne a tevékenység naplóit, hanem a nagy léptékű monitorozást. Ezért:

- **Korlátozott forgatókönyvek**: a tevékenység-naplókon keresztüli értesítések csak az Azure-beli virtuális gépek biztonsági mentésére vonatkoznak. Az értesítéseket minden Recovery Services-tárolóhoz be kell állítani.
- **Definíciós igazítás**: az ütemezett biztonsági mentési tevékenység nem felel meg a tevékenységek naplófájljainak legújabb definíciójának. Ehelyett az [erőforrás-naplókhoz](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#what-you-can-do-with-resource-logs-in-a-workspace)igazodik. Ez az igazítás váratlan hatásokat okoz, ha a tevékenység naplójának csatornáján áthaladó adat megváltozik.
- **Problémák a tevékenység naplójának csatornájában**: Recovery Services-tárolókban, a Azure Backup bepumpált tevékenység-naplók egy új modellt követnek. Ez a változás sajnos a Azure Government, az Azure Germany és az Azure China 21Vianet tevékenység-naplófájljainak előállítására is hatással van. Ha a felhőalapú szolgáltatások felhasználói a Azure Monitorban lévő tevékenységi naplókból hoznak létre vagy konfigurálnak riasztásokat, a riasztások nem aktiválódnak. Továbbá, ha a felhasználó az összes Azure-beli nyilvános régióban [begyűjti Recovery Services tevékenység naplóit egy log Analytics munkaterületre](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs), akkor ezek a naplók nem jelennek meg.

A Azure Backup által védett munkaterhelések esetében használjon Log Analytics munkaterületet a nagy léptékű figyeléshez és riasztáshoz.

## <a name="next-steps"></a>További lépések

Egyéni lekérdezések létrehozásához tekintse meg az [log Analytics adatmodellt](backup-azure-log-analytics-data-model.md).
