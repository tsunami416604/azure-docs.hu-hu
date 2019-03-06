---
title: 'Azure Backup: Az Azure Backup monitorozása az Azure monitorral'
description: Az Azure Backup számítási feladatok monitorozása és az Azure Monitor használatával egyéni riasztások létrehozása
services: backup
author: pvrk
manager: shivamg
keywords: A log Analytics; Az Azure Backup; Riasztások; Diagnosztikai beállítások; Műveletcsoportok
ms.service: backup
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: pullabhk
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: 7daf76605d40495d6c9cb5076f0403a78819adfc
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57439163"
---
# <a name="monitoring-at-scale-using-azure-monitor"></a>Az Azure Monitor használatával nagy mennyiségű figyelése

A [beépített figyelési és riasztási cikk](backup-azure-monitoring-built-in-monitor.md) felsorolt a monitorozási és riasztási funkciókat az egy közös Recovery services-tároló, és amely minden olyan további felügyeleti infrastruktúra nélkül érhető el. A beépített szolgáltatás azonban a következő esetekben korlátozódik.

- Több RS tárolóból származó adatok figyelése több előfizetés között
- Ha e-mailek nem az előnyben részesített értesítési csatorna
- Ha a felhasználók meg szeretnék további forgatókönyvek esetén küldjön riasztást
- Ha szeretné a helyszíni összetevő, például a System Center DPM (SC-DPM) adatainak megtekintése az Azure-ban, amely nem látható [biztonsági mentési feladatok](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) vagy [biztonsági mentési riasztások](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault) portálon.

## <a name="using-log-analytics-workspace"></a>Log Analytics-munkaterület használata

> [!NOTE]
> Az Azure virtuális gép biztonsági mentéseinek, MAB-ügynök, a System Center DPM (SC-DPM) van alatt folyamatosan a Log Analytics-munkaterületre diagnosztikai beállításokon keresztül. Az SQL biztonsági másolatokat az Azure-beli virtuális gépeken, az Azure fájlmegosztások biztonsági másolatainak, a Microsoft Azure Backup Server (MABS) támogatása hamarosan elérhető lesz.

Azt is két Azure-szolgáltatások - funkcióit kihasználva az **diagnosztikai beállítások** (adatokat lehet küldeni a több Azure Resource Manager-erőforrások egy másik erőforrás) és **Log Analytics** (LA - létrehozásához egyéni riasztások, ahol megadhatja a más értesítési csatornákra Műveletcsoportok használatával) figyelés ipari méretekben. A következő szakaszokban a LA használatát az Azure Backup monitorozásához ipari méretekben.

### <a name="configuring-diagnostic-settings"></a>Diagnosztikai beállítások konfigurálása

Egy Azure Resource Manager-erőforrás, például az Azure Recovery services-tároló az összes lehetséges kapcsolatos adatokat rögzíti az ütemezett műveletek és a felhasználó által aktivált műveletek diagnosztikai adatokat. Kattintson a "diagnosztikai beállítások" a figyelés szakaszban, és adja meg a cél az RS tár diagnosztikai adatokat.

![RS tár diagnosztikai beállítás LA célként](media/backup-azure-monitoring-laworkspace/rs-vault-diagnostic-setting.png)

Kiválaszthatja egy LA munkaterület célként egy másik előfizetésből. *A azonos LA munkaterülete több RS tárolók kiválasztásával tárolók előfizetésekben egyetlen helyen figyelheti.* Válassza ki a "AzureBackupReport", a naplót a csatorna összes az Azure Backup kapcsolatos adatokat az LA munkaterületre.

> [!IMPORTANT]
> A konfiguráció befejezését követően kell várja meg a 24 órát, hogy a kezdeti adatok leküldéses végrehajtásához. Ezt követően minden, az eseményt leküld, említetteknek megfelelően az [gyakorisága szakasz](#diagnostic-data-update-frequency).

### <a name="deploying-solution-to-log-analytics-workspace"></a>A Log Analytics-munkaterületet a megoldás üzembe helyezése

Amint az adatok LA munkaterületen belül [github-sablon üzembe helyezése](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) alakzatot LA adatok vizualizálásához. Ellenőrizze, hogy megkapja az ugyanabban az erőforráscsoportban, munkaterület neve, és munkaterület helye megfelelően azonosíthatja a munkaterületet, és ez a sablon telepíthető.

### <a name="view-azure-backup-data-using-log-analytics-la"></a>Log Analytics (LA) használatával az Azure Backup-adatok megtekintése

A sablon üzembe helyezése után az Azure Backup monitoring megoldás jelennek meg a munkaterület összefoglaló régióban. Ön is a keresztül haladnak át

- Az Azure Monitor -> "Több" a "Insights" szakaszban, és válassza ki a megfelelő munkaterületet, vagy
- Log Analytics-munkaterületek, -> Válassza ki a megfelelő munkaterületet (Workspace összegzése) -> Általános beállítások mellett.

![AzureBackupLAMonitoringTile](media/backup-azure-monitoring-laworkspace/la-azurebackup-azuremonitor-tile.png)

A csempékre kattintva a gráfok tudnivalók az Azure Backup, mint például az alapszintű figyelési adatok több megnyitja a Tervező sablont

#### <a name="all-backup-jobs"></a>Az összes biztonsági mentési feladatok

![LABackupJobs](media/backup-azure-monitoring-laworkspace/la-azurebackup-allbackupjobs.png)

#### <a name="restore-jobs"></a>Állítsa vissza a feladatokat

![LARestoreJobs](media/backup-azure-monitoring-laworkspace/la-azurebackup-restorejobs.png)

#### <a name="inbuilt-azure-backup-alerts-for-azure-resources"></a>Az Azure-erőforrások a beépített Azure biztonsági mentési riasztások

![LAInbuiltAzureBackupAlertsForAzureResources](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts.png)

#### <a name="inbuilt-azure-backup-alerts-for-on-prem-resources"></a>A beépített Azure Backup-riasztások a helyszíni erőforrásokhoz

![LAInbuiltAzureBackupAlertsForOnPremResources](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts-onprem.png)

#### <a name="active-datasources"></a>Aktív adatforrások

![LAActiveBackedUpEntities](media/backup-azure-monitoring-laworkspace/la-azurebackup-activedatasources.png)

#### <a name="rs-vault-cloud-storage"></a>RS Vault Cloud Storage

![LARSVaultCloudStorage](media/backup-azure-monitoring-laworkspace/la-azurebackup-cloudstorage-in-gb.png)

A fenti grafikonon a sablonhoz, és az ügyfél Szerkesztés/felvehet további diagramjait.

> [!IMPORTANT]
> Ha a sablon üzembe helyezéséhez lényegében egy írásvédettségi zárolása hoz létre, és el kell távolítania, hogy a sablon szerkesztése és mentése. Távolítsa el a zárolást, keresse meg a Log Analytics-munkaterület a "Beállítások" szakaszban a "Zárolását" panelen.

### <a name="create-alerts-using-log-analytics"></a>Hozzon létre a riasztások a Log Analytics használatával

Az Azure Monitor lehetővé teszi a felhasználóknak a saját riasztások létrehozása a LA munkaterületről, ahol *kihasználhatja az Azure Műveletcsoportok jelölje be az előnyben részesített értesítési mechanizmus*. Kattintson a fenti nyissa meg a naplók"szakaszban LA munkaterület grafikonok valamelyik ***Ön is a lekérdezések létrehozására és szerkesztésére szolgáló platformképességeivel riasztások***.

![LACreateAlerts](media/backup-azure-monitoring-laworkspace/la-azurebackup-customalerts.png)

Kattintson a "Új riasztási szabály" jelennek meg a riasztás létrehozása az Azure Monitor képernyő nyílik meg.

Ahogy alább is észleli, az erőforrás már meg van jelölve, a LA munkaterület és a műveleti csoport integrálása.

![LAAzureBackupCreateAlert](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

> [!IMPORTANT]
> Vegye figyelembe, hogy ez a lekérdezés létrehozására vonatkozó ár hatását adott [Itt](https://azure.microsoft.com/pricing/details/monitor/).

#### <a name="alert-condition"></a>Riasztási feltétel

A fontos szempontja a riasztást kiváltó feltétel, a riasztás. Kattintson a "Feltétel" automatikusan betölti a "Logs" képernyőn, ahogy az alábbi Kusto-lekérdezés, és azokat az adott forgatókönyvnek megfelelően. Néhány példa Kusto-lekérdezés tartalmazza a [az alábbi szakasz](#sample-kusto-queries).

![LAAzureBackupAlertCondition](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

A Kusto-lekérdezés szerkesztése, ha szükséges, válassza ki a megfelelő küszöbértéket (amely fog döntse el, ha a riasztás el lesz indítva), a jobb oldali időszak (időablak, amelynek a lekérdezés futtatása), és a gyakoriság. A példa: A példa: Ha a küszöb értéke nagyobb, mint 0, az időszak érték 5 perc és gyakoriság 5 perc, majd a szabály lefordított "A lekérdezés futtatásához az utolsó 5 percen át 5 percenként, és ha az eredmények száma 0-nál nagyobbnak, értesítés küldése a kiválasztott műveletcsoport"

#### <a name="action-group-integration"></a>Műveleti csoport integrálása

Műveletcsoportok adja meg a felhasználó az értesítési csatornákat. Kattintson a "Új létrehozása" a "Műveletcsoportok" szakasz értesítési mechanizmusok elérhető listáját tartalmazza.

![LAAzureBackupNewActionGroup](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

Tudjon meg többet a [LA munkaterület riasztásai](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) és [Műveletcsoportok](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) az Azure Monitor dokumentációja.

Ezért minden figyelmeztetések és monitorozás önálló analyticsből követelmények kielégítése, vagy egy kiegészítő technika az értesítés a beépített mechanizmusok szeretné használni.

### <a name="sample-kusto-queries"></a>Kusto mintalekérdezések

Az alapértelmezett diagram adna meg Kusto-lekérdezés alapszintű alkalmazási helyzetek, amikor hozhat létre riasztásokat. Emellett módosíthatja az a riasztások a kívánt adatokat. Itt biztosítunk néhány Kusto mintalekérdezések, hogy Ön is "Logs" ablakban illessze be, és ezután hozzon létre egy riasztást a lekérdezésre.

#### <a name="all-successful-backup-jobs"></a>Az összes sikeres biztonsági mentési feladatok

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| where OperationName == "Job" and JobOperation_s == "Backup"
| where JobStatus_s == "Completed"
````

#### <a name="all-failed-backup-jobs"></a>Összes sikertelen volt a biztonsági mentési feladatok

````Kusto
AzureDiagnostics
| where Category == "AzureBackupReport"
| where SchemaVersion_s == "V2"
| where OperationName == "Job" and JobOperation_s == "Backup"
| where JobStatus_s == "Failed"
````

#### <a name="all-successful-azure-vm-backup-jobs"></a>Az összes sikeres Azure virtuális gép biztonsági mentési feladatok

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

#### <a name="all-successful-sql-log-backup-jobs"></a>Az összes sikeres SQL-napló biztonsági mentési feladatok

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

#### <a name="all-successful-mab-agent-backup-jobs"></a>Az összes sikeres MAB-ügynök biztonsági mentési feladatok

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

### <a name="diagnostic-data-update-frequency"></a>Diagnosztikai adatok frissítési gyakoriság

A diagnosztikai adatokat a tárolóból az LA munkaterület némi késés, a folyamatosan. Minden esemény érkezik, a LA munkaterületet ***20-30 perc után azt a RS tárolóból leküldéssel késéssel.***

- A biztonsági mentési szolgáltatás beépített riasztások (az összes megoldások) közötti leküld, amint a rendszer hozza létre őket. Ami azt jelenti, hogy általában jelennek meg a LA munkaterületen késés 20-30 perc után.
- Ad hoc ad hoc biztonsági mentési feladatok és a visszaállítási feladat (között az összes megoldáshoz), amint azok leküldött ***végezhető el***.
- Az ütemezett biztonsági mentési feladatok (az SQL biztonsági mentése) kivételével az összes megoldásaiból származó, amint azok leküld ***végezhető el***.
- SQL biztonsági mentés, mivel rendelkezhet naplóalapú biztonsági mentések, hogy az összes a befejezett ütemezett biztonsági mentési feladatot, beleértve a naplókat, 15 percenként az információt kötegelni és leküldött 6 óránként.
- Minden egyéb információkat, például a biztonsági másolati elem, a szabályzat, a helyreállítási pontok, a storage stb. minden megoldások közötti leküld **legalább naponta egyszer.**
- Például megváltoztathatja a szabályzatot, stb. a házirend szerkesztése a biztonsági mentési konfiguráció módosítása elindítja egy leküldéses minden kapcsolódó biztonsági mentési adatok.

## <a name="using-rs-vaults-activity-logs"></a>Tevékenységnaplók RS Vault használatával

A Tevékenységnaplók segítségével is eseményeket, mint például a biztonsági mentés sikeres értesítést kaphat.

> [!CAUTION]
> **Ne feledje, ez a tulajdonság csak az Azure virtuális gép biztonsági mentéseinek vonatkozik.** Nem használhat ez megoldásait az Azure Backup szolgáltatás ügynökének, például az SQL biztonsági mentések, Azure, Azure-fájlok stb.

### <a name="sign-in-into-azure-portal"></a>Jelentkezzen be az Azure Portalon

Jelentkezzen be az Azure Portalra, és folytassa a megfelelő Azure Recovery Services-tárolót, és kattintson a Tulajdonságok "Activity log" című szakaszában.

### <a name="identify-appropriate-log-and-create-alert"></a>Azonosítsa a megfelelő naplót, és a riasztás létrehozása

A alkalmazni a szűrőket, győződjön meg arról, hogy azért küldtük Önnek, a sikeres biztonsági mentések Tevékenységnaplók az alábbi képen is látható. Az időtartomány ennek megfelelően módosítsa rekordok megtekintéséhez.

![Az Azure virtuális gép biztonsági mentéseinek tevékenységeket tartalmazó naplók](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

Kattintson a "JSON" szegmens további részletekért és a másolás-beillesztés azt egy szövegszerkesztőben az alakzatot tekinti meg. Ekkor megjelenik a tároló részleteit, és az elem, amely kiváltotta a tevékenység naplózása, a biztonsági másolati elem.

Kattintson a "Tevékenységnapló-riasztás hozzáadása" az összes napló riasztásokat generálni.

Kattintson a "Hozzáadás tevékenységnapló-riasztás" fent látható, és megnyílik a riasztás létrehozásakor képernyő, amely hasonló riasztás létrehozása képernyő [fent ismertetett](#create-alerts-using-log-analytics).

Az RS vault Íme az erőforrást, és ezért meg kell ismételni ugyanaz a művelet minden, a használni kívánt értesítési keresztül tevékenységeket tartalmazó naplók tárolók. A feltétel nem fog minden olyan küszöbértéket, időszak, gyakorisága, mivel ez az esemény-alapú riasztás. Amint a megfelelő tevékenységnapló jön létre, a riasztás aktiválódik.

## <a name="recommendation"></a>Ajánlás

***Tevékenységnaplók alapján létrehozott összes riasztás és LA munkaterületek lehet megtekinteni az Azure monitorban az "Értesítések" a bal oldali panelen.***

Az értesítés útján tevékenységeket tartalmazó naplók is használható, amíg ***Azure Backup szolgáltatás nyomatékosan javasolja LA használja a következő okok miatt a méretezési csoport és a nem Tevékenységnaplók figyelés***.

- **Korlátozott alkalmazási helyzetek:** Csak az Azure virtuális gép biztonsági mentéseinek érvényes, és meg kell ismételni minden RS-tároló.
- **Szélességhez definíciója:** Az ütemezett biztonsági mentési művelet nem fér el a tevékenységeket tartalmazó naplók a legújabb definíció- és igazodik a [diagnosztikai naplók](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview#what-are-azure-monitor-diagnostic-logs). Az érdeklődő váratlan hatásra, az adatok szivattyúzó tevékenység log csatornán keresztül, az alább hivatkozott módosítás esetén.
- **Tevékenység naplózási csatornába kapcsolatos problémák:** A Recovery Services-tárolók az Azure Backup műveletnaplóinak főfenékvízcsövön új modell áthelyeztük. Sajnos az áthelyezés hatással vannak a független Felhőkben Azure vizsgálati naplók létrehozása. Azure szuverén felhő felhasználók létrehozása vagy nincs konfigurálva az Azure monitoron keresztül Tevékenységnaplók riasztásokkal, ha, akkor nem aktiválódik. Emellett minden nyilvános Azure-régióban, ha egy felhasználó gyűjti Recovery Services-Tevékenységnaplók egy Log Analytics-munkaterületre említettek szerint a [Itt](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs), ezek a naplók még nem jelent.

Ezért ajánlott használni Log Analytics-munkaterületet, és a riasztásra, az Azure biztonsági mentés a védett munkaterhelések.

## <a name="next-steps"></a>További lépések

- Tekintse meg [Log analytics-adatmodell](backup-azure-log-analytics-data-model.md) egyéni lekérdezések létrehozása.
