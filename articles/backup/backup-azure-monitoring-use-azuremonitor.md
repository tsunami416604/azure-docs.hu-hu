---
title: 'Azure Backup: Az Azure Backup monitorozása az Azure monitorral'
description: Az Azure Backup számítási feladatok monitorozása és egyéni riasztásokat is létrehozhat az Azure Monitor használatával.
services: backup
author: pvrk
manager: shivamg
keywords: A log Analytics; Az Azure Backup; Riasztások; Diagnosztikai beállítások; Műveletcsoportok
ms.service: backup
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: pullabhk
ms.assetid: 01169af5-7eb0-4cb0-bbdb-c58ac71bf48b
ms.openlocfilehash: e2d4a235737789f2f5852c00218427613db3d558
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786319"
---
# <a name="monitor-at-scale-by-using-azure-monitor"></a>Az Azure Monitor használatával nagy mennyiségű figyelése

Az Azure Backup kínál [beépített figyelési és riasztási funkciókat](backup-azure-monitoring-built-in-monitor.md) a Recovery Services-tárolóba. Ezek a képességek a további felügyeleti infrastruktúrák nélkül érhetők el. Azonban ez beépített szolgáltatás korlátozza a következő esetekben:

- Ha az adatokat több Recovery Services-tárolók figyelése előfizetések
- Ha az előnyben részesített értesítési csatorna *nem* e-mailben
- További forgatókönyvek szeretnék riasztások
- Ha meg szeretné tekinteni egy helyszíni összetevő, például a System Center Data Protection Manager adatait az Azure-ban, amely a portál nem jelenik meg a [ **biztonsági mentési feladatok** ](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault) vagy [  **Biztonsági mentési riasztások**](backup-azure-monitoring-built-in-monitor.md#backup-alerts-in-recovery-services-vault)

## <a name="using-log-analytics-workspace"></a>A Log Analytics-munkaterület használata

> [!NOTE]
> Azure virtuális gép biztonsági mentéseinek, az Azure Backup-ügynök, System Center Data Protection Manager, Azure virtuális gépeken futó SQL biztonsági másolatainak és az Azure Files-fájlmegosztások biztonsági másolatainak adatait a Log Analytics-munkaterületre diagnosztikai beállításokon keresztül van folyamatosan. 

Nagy mennyiségű monitorozásához kell két Azure-szolgáltatások képességeit. *Diagnosztikai beállítások* adatokat küldeni a több Azure Resource Manager-erőforrások egy másik erőforrás. *Log Analytics* állít elő, ha más értesítési csatornákra meghatározásához használhatja Műveletcsoportok egyéni riasztások. 

A következő szakaszok bemutatják, hogyan Log Analytics használatához az Azure Backup monitorozásához ipari méretekben.

### <a name="configure-diagnostic-settings"></a>Diagnosztikai beállítások konfigurálása

Azure Resource Manager-erőforrások, például a Recovery Services-tároló, jegyezze fel a diagnosztikai adatok, az ütemezett műveletek és a felhasználó által aktivált műveletek kapcsolatos információkat. 

A figyelés területen válassza ki a **diagnosztikai beállítások** , és adja meg a cél a Recovery Services-tároló diagnosztikai adatokat.

![A Recovery Services-tároló diagnosztikai beállítás, a Log Analytics célzó](media/backup-azure-monitoring-laworkspace/rs-vault-diagnostic-setting.png)

A Log Analytics-munkaterületet egy másik előfizetésből célba. Tárolók figyelése egyetlen helyen történő előfizetések között, válassza ki ugyanazt a Log Analytics-munkaterület több Recovery Services-tárolók számára. Válassza ki a channel az Azure Backup a Log Analytics-munkaterülethez kapcsolódó összes információt, **AzureBackupReport** bejelentkezési.

> [!IMPORTANT]
> Miután elvégezte a konfigurációt, meg kell Várjon 24 órát a kezdeti adatok leküldéses befejezéséhez. Miután, amely a kezdeti adatok leküldéses, a minden eseményt leküld-e, ez a cikk későbbi részében leírtak szerint a [gyakorisága szakasz](#diagnostic-data-update-frequency).

### <a name="deploy-a-solution-to-the-log-analytics-workspace"></a>A Log Analytics-munkaterületet a megoldás üzembe helyezése

Miután az adatok a Log Analytics-munkaterületen belül [GitHub-sablon üzembe helyezése](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) megjelenítheti az adatokat a Log Analytics. Helyesen azonosítani a munkaterületen, ellenőrizze, hogy meg neki az ugyanabban az erőforráscsoportban, a munkaterület neve és a munkaterület helye. Ez a sablon telepítése a munkaterületen.

> [!NOTE]
> Riasztások, a biztonsági mentési feladatok vagy a visszaállítási feladat nem rendelkezik a Log Analytics-munkaterület, láthatja a portálon egy "BadArgumentError" hibakód. A hiba figyelmen kívül a megoldás használatának folytatásához. Után elindul a vonatkozó adatok tárfiókba kerülnek a munkaterületre, a Vizualizációk azonos fogja tartalmazni, és a hibaüzenet többé nem jelenik meg.

### <a name="view-azure-backup-data-by-using-log-analytics"></a>Az Azure Backup-adatok megtekintése a Log Analytics használatával

A sablon telepítése után az Azure Backup monitoring megoldás jelennek meg a munkaterület összefoglaló régióban. Nyissa meg az összesítést, kövesse az elérési utak:

- **Az Azure Monitor**: Az a **Insights** szakaszban jelölje be **további** és válassza ki a megfelelő munkaterületet.
- **Log Analytics-munkaterületek**: Válassza ki a megfelelő munkaterületet, majd a **általános**válassza **munkaterület összefoglalás**.

![A Log Analytics-figyelési csempe](media/backup-azure-monitoring-laworkspace/la-azurebackup-azuremonitor-tile.png)

A figyelési csempe kiválasztásakor a Tervező sablon egy sorozatát gráfok kapcsolatos alapvető figyelési adatok az Azure Backup megnyílik. Íme néhány az ábrán láthatja:

* Az összes biztonsági mentési feladatok

   ![Log Analytics diagramokon biztonsági mentési feladatok](media/backup-azure-monitoring-laworkspace/la-azurebackup-allbackupjobs.png)

* Állítsa vissza a feladatokat

   ![Log Analytics graph a visszaállítási feladat](media/backup-azure-monitoring-laworkspace/la-azurebackup-restorejobs.png)

* Az Azure-erőforrások beépített Azure biztonsági mentési riasztások

   ![Log Analytics graph beépített Azure biztonsági mentési riasztások az Azure-erőforrásokhoz](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts.png)

* A helyszíni erőforrások beépített Azure biztonsági mentési riasztások

   ![Log Analytics graph a helyszíni erőforrások beépített Azure biztonsági mentési riasztások](media/backup-azure-monitoring-laworkspace/la-azurebackup-activealerts-onprem.png)

* Aktív adatforrásokat

   ![Log Analytics graph aktív készül entitások esetében](media/backup-azure-monitoring-laworkspace/la-azurebackup-activedatasources.png)

* Recovery Services-tároló felhőalapú tárolás

   ![A Recovery Services vault felhőalapú log Analytics graph](media/backup-azure-monitoring-laworkspace/la-azurebackup-cloudstorage-in-gb.png)

A grafikonon a sablonhoz. A gráfok szerkesztheti, vagy adjon hozzá további diagramokat, ha szeretne.

> [!IMPORTANT]
> Ha a sablon üzembe helyezéséhez lényegében egy írásvédettségi zárolása hoz létre. Szerkesztheti, és a sablon mentéséhez, távolítsa el a zárolást kell. Távolíthatja el a zárolást a **beállítások** a Log Analytics-munkaterület szakaszában, az a **zárolja** ablaktáblán.

### <a name="create-alerts-by-using-log-analytics"></a>Riasztások létrehozása Log Analytics használatával

Az Azure Monitor létrehozhat saját riasztások a Log Analytics-munkaterületen. A munkaterületen használja *Azure action groups általi* jelölje be az előnyben részesített értesítési mechanizmus. 

> [!IMPORTANT]
> Információk a költséghatékonyság, ez a lekérdezés: [díjszabás az Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).

Jelölje ki a megnyitni a grafikonok a **naplók** szakaszában a Log Analytics-munkaterületet. Az a **naplók** részen szerkessze a lekérdezéseket, és riasztások létrehozása a őket.

![Riasztás létrehozása a Log Analytics-munkaterületen](media/backup-azure-monitoring-laworkspace/la-azurebackup-customalerts.png)

Ha bejelöli **Új riasztási szabály**, megnyílik az Azure Monitor riasztás-létrehozási oldalon, a következő képen látható módon. Itt az erőforrás már meg van jelölve, a Log Analytics-munkaterületet, és a műveleti csoport integrálása.

![A Log Analytics-riasztás-létrehozási oldalon](media/backup-azure-monitoring-laworkspace/inkedla-azurebackup-createalert.jpg)

#### <a name="alert-condition"></a>Riasztási feltétel

A riasztás meghatározó jellemzője a riasztást kiváltó feltétel. Válassza ki **feltétel** automatikusan betölteni a Kusto-lekérdezés a **naplók** lapon az alábbi képen látható módon. Itt szerkesztheti a feltételt, hogy illeszkedjen az igényeihez. További információkért lásd: [minta Kusto-lekérdezés](#sample-kusto-queries).

![A riasztási állapot beállítása](media/backup-azure-monitoring-laworkspace/la-azurebackup-alertlogic.png)

Ha szükséges, módosíthatja a Kusto-lekérdezés. Válasszon egy küszöbértéket, időszak és gyakoriságát. A küszöbérték határozza meg, ha a riasztást generál. Az időszak, amelyben a lekérdezés futtatásakor eltelt időszakot. Például ha a küszöb értéke nagyobb, mint 0, az időszak érték 5 perc, és gyakoriság 5 perc, majd a szabály fut a lekérdezés 5 percenként, tekintse át az előző 5 perc. Ha az eredmények száma 0-nál nagyobbnak, értesítést kap a kiválasztott művelet csoporton keresztül.

#### <a name="alert-action-groups"></a>Riasztási Műveletcsoportok

Műveletcsoport használatával adjon meg egy értesítési csatornát. Az elérhető értesítési mechanizmusok alatt megtekintéséhez **Műveletcsoportok**válassza **hozzon létre új**.

![A "Csoport hozzáadása művelet" ablakban elérhető értesítési mechanizmusok](media/backup-azure-monitoring-laworkspace/LA-AzureBackup-ActionGroup.png)

Képes kielégíteni, minden figyelmeztetések és monitorozás a Log Analytics önálló követelmények, vagy használhatja a Log Analytics beépített értesítéseket kiegészítésére.

További információkért lásd: [létrehozása, megtekintése, és a riasztások kezelése az Azure Monitor használatával](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) és [létrehozása és kezelése az Azure Portalon Műveletcsoportok](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups).

### <a name="sample-kusto-queries"></a>Kusto mintalekérdezések

Az alapértelmezett diagram Kusto-lekérdezés biztosítanak, amelyre hozhat létre riasztásokat alapvető forgatókönyv. A lekérdezések az adatok beolvasásához a kapni kívánt módosíthatja is. Illessze be a következő minta Kusto lekérdezései a **naplók** lapon, majd hozza létre az értesítések a lekérdezésekre:

* Az összes sikeres biztonsági mentési feladatok

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Completed"
    ````
    
* Összes sikertelen volt a biztonsági mentési feladatok

    ````Kusto
    AzureDiagnostics
    | where Category == "AzureBackupReport"
    | where SchemaVersion_s == "V2"
    | where OperationName == "Job" and JobOperation_s == "Backup"
    | where JobStatus_s == "Failed"
    ````
    
* Az összes sikeres Azure virtuális gép biztonsági mentési feladatok

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

* Az összes sikeres SQL napló biztonsági mentési feladatok

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

* Az összes sikeres Azure Backup ügynök feladatok

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

A diagnosztikai adatokat a tárolóból a Log Analytics-munkaterületet, és némi késés, a folyamatosan. Minden esemény érkezik meg a Log Analytics-munkaterület *20-30 percig* után, a Recovery Services-tárolóból leküldésekor. Az alábbiakban további részleteit a késés:

- Összes megoldás között a biztonsági mentési szolgáltatás beépített riasztások leküld, amint jönnek létre. Ezért ezek általában jelenik meg a Log Analytics-munkaterület 20 – 30 perc múlva.
- Összes megoldás között ad-hoc biztonsági mentési feladatok és a visszaállítási feladat leküld, amint azok *Befejezés*.
- SQL biztonsági mentési kivételével az összes megoldásainak ütemezett biztonsági mentési feladatot, amint azok leküld *Befejezés*.
- SQL biztonsági mentés mivel naplóalapú biztonsági mentések fordulhat elő, 15 percenként kapcsolatos összes a befejezett ütemezett biztonsági mentési feladatot, beleértve a naplókat, kötegelni és a leküldött 6 óránként.
- Összes megoldás között akkor egyéb információkat, például a biztonsági másolati elem, házirend, a helyreállítási pontok, tárolási és így tovább leküldött legalább *naponta egyszer.*
- (Például a házirend módosítása, vagy a házirend szerkesztése) a biztonsági mentési konfiguráció módosítása elindítja egy leküldéses minden kapcsolódó biztonsági mentési adatok.

## <a name="using-the-recovery-services-vaults-activity-logs"></a>A Recovery Services-tároló tevékenységeket tartalmazó naplók használatával

> [!CAUTION]
> Az alábbi lépéseket csak a alkalmazni *Azure virtuális gép biztonsági mentéseinek.* Ezeket a lépéseket nem használható megoldásokra, például az Azure Backup-ügynök, az SQL biztonsági mentések belül az Azure-ban vagy az Azure Files.

A Tevékenységnaplók segítségével is eseményeket, mint például a biztonsági mentés sikeres értesítést kaphat. A kezdéshez kövesse az alábbi lépéseket:

1. Jelentkezzen be az Azure Portalra.
1. Nyissa meg a megfelelő Recovery Services-tárolót. 
1. A tároló tulajdonságai között nyissa meg a **tevékenységnapló** szakaszban.

Azonosíthatja a megfelelő naplót, és hozzon létre egy riasztást:

1. Győződjön meg arról, hogy a sikeres biztonsági mentések Tevékenységnaplók küldtük úgy, hogy alkalmazza a szűrőket, az alábbi képen látható. Módosítsa a **Timespan** rekordok megtekintéséhez szükséges értéket.

   ![Szűrés az Azure virtuális gép biztonsági mentéseinek tevékenységeket tartalmazó naplók keresése](media/backup-azure-monitoring-laworkspace/activitylogs-azurebackup-vmbackups.png)

1. A művelet nevét kijelölve megtekintheti a releváns adatokat.
1. Válassza ki **Új riasztási szabály** megnyitásához a **létrehozás szabály** lapot. 
1. A lépéseket követve hozzon létre egy riasztást [létrehozása, megtekintése és a tevékenységnapló-riasztások kezelése az Azure Monitor használatával](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log).

   ![Új riasztási szabály](media/backup-azure-monitoring-laworkspace/new-alert-rule.png)

Az erőforrás Íme a Recovery Services-tároló magát. Az összes, amelyben a Tevékenységnaplókban értesíteni szeretné a tárolók meg kell ismételnie ugyanazokat a lépéseket. A feltétel nem kell egy küszöbértéket, ponttal vagy gyakorisága, mert ez a riasztás eseményeken alapul. Amint a megfelelő tevékenységnapló jön létre, akkor a riasztás akkor jön létre.

## <a name="using-log-analytics-to-monitor-at-scale"></a>A Log Analytics használatával nagy mennyiségű figyelése

A vizsgálati naplók és a Log Analytics-munkaterületek az Azure monitorban létrehozott összes riasztás tekintheti meg. Egyszerűen nyissa meg a **riasztások** a bal oldali panelen.

Bár a Tevékenységnaplókban értesítéseket kap, erősen ajánlott helyett Tevékenységnaplók Log Analytics használatával nagy mennyiségű figyelésre. Következő az oka:

- **Korlátozott esetekben**: Tevékenységnaplókban értesítések csak az Azure virtuális gép biztonsági mentéseinek érvényesek. Az értesítések kell állítania minden Recovery Services-tároló.
- **Definíció fér**: Az ütemezett biztonsági mentési művelet nem fér el a tevékenységeket tartalmazó naplók a legújabb definíció. Ehelyett igazodik a [diagnosztikai naplók](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview#what-you-can-do-with-diagnostic-logs). Ez a kiosztás nem várt hatások okoz, ha az adatokat, a tevékenység áthaladó adatátvitelen csatorna változások naplózása.
- **A tevékenység naplózási csatornába problémáinak**: A Recovery Services-tárolókra tevékenységi naplóit, hogy a rendszer folyamatosan, az Azure Backup hajtsa végre az új modell. Sajnos ez a módosítás érinti az Azure Government, Azure Germany és Azure China 21Vianet Tevékenységnaplók generációja. A riasztások nem indul el, ha a felhasználók ezen felhőalapú szolgáltatások hoz létre, vagy bármely riasztások tevékenységnaplókból beállítása az Azure Monitor. Emellett minden nyilvános Azure-régióban, ha egy felhasználó a [gyűjti a Recovery Services-Tevékenységnaplók Log Analytics-munkaterület be](https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs), ezek a naplók nem jelennek meg.

Log Analytics-munkaterület használata a monitorozási és riasztási az Azure Backup által védett munkaterhelések nagy számú.

## <a name="next-steps"></a>További lépések

Egyéni lekérdezések létrehozásához lásd: [Naplóelemzési adatmodell](backup-azure-log-analytics-data-model.md).
