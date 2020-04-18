---
title: A Helyreállítási szolgáltatások tárolóinak diagnosztikai beállításainak használata
description: Ez a cikk ismerteti, hogyan használhatja a régi és az új diagnosztikai események az Azure Backup.
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: f11c9d2a5b48b9cd27ac6e6f8a00eb5ac0ac7a9d
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617312"
---
# <a name="use-diagnostics-settings-for-recovery-services-vaults"></a>A Helyreállítási szolgáltatások tárolóinak diagnosztikai beállításainak használata

Az Azure Backup diagnosztikai eseményeket küld, amelyek összegyűjthetők és felhasználhatók elemzés, riasztás és jelentéskészítés céljából.

A Recovery Services-tároló diagnosztikai beállításait az Azure Portalon keresztül konfigurálhatja a tárolóba, és a Diagnosztikai beállítások lehetőséget választva **állíthatja**be. A + Diagnosztikai beállítás hozzáadása lehetőség **kiválasztásával** egy vagy több diagnosztikai eseményt küldhet egy tárfiókba, egy eseményközpontba vagy egy Log Analytics-munkaterületre.

![Diagnosztikai beállítások ablaktábla](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Az Azure Backup-felhasználók számára elérhető diagnosztikai események

Az Azure Backup a következő diagnosztikai eseményeket biztosítja. Minden esemény részletes adatokat szolgáltat a biztonsági mentéssel kapcsolatos összetevők adott készletéről:

* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolicy
* AddonAzureBackupTárhely

További információ: [Data Model for Azure Backup diagnostics events](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model).

Ezek az események adatait lehet küldeni egy tárfiók, a Log Analytics-munkaterület vagy egy eseményközpont. Ha ezeket az adatokat egy Log Analytics-munkaterületre küldi, válassza ki az **Erőforrás-specifikus** **kapcsolót** a Diagnosztikai beállítások képernyőn. További információt a következő szakaszokban talál.

## <a name="use-diagnostics-settings-with-log-analytics"></a>Diagnosztikai beállítások használata a Log Analytics szolgáltatással

Most már használhatja az Azure Backup küldhettároló diagnosztikai adatokat dedikált Log Analytics-táblák biztonsági mentésre. Ezeket a táblákat [erőforrás-specifikus tábláknak nevezzük.](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific)

A tároló diagnosztikai adatainak küldése a Log Analytics szolgáltatásba:

1. Nyissa meg a trezort, és válassza a **Diagnosztikai beállítások lehetőséget.** Válassza **a + Diagnosztikai beállítás hozzáadása lehetőséget**.
1. Adjon nevet a diagnosztikai beállításnak.
1. Jelölje be a **Küldés a loganalytics-be** jelölőnégyzetet, és jelöljön ki egy Log Analytics-munkaterületet.
1. Válassza **az Erőforrás-specifikus lehetőséget** a váltóban, és válassza ki a következő hat eseményt: **CoreAzureBackup**, **AddonAzureBackupJobs**, **AddonAzureBackupAlerts**, **AddonAzureBackupPolicy**, **AddonAzureBackupStorage**és **AddonAzureBackupedInstance**.
1. Kattintson a **Mentés** gombra.

   ![Erőforrás-specifikus mód](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

Miután az adatok a Log Analytics munkaterületre kerülnek, az egyes eseményekhez dedikált táblák jönnek létre a munkaterületen. A táblák bármelyikét közvetlenül lekérdezheti. Szükség esetén illesztéseket vagy egyesítéseket is végrehajthat a táblák között.

> [!IMPORTANT]
> A hat esemény, nevezetesen a CoreAzureBackup, az AddonAzureBackupJobs, az AddonAzureBackupAlerts, az AddonAzureBackupPolicy, az AddonAzureBackupStorage és az AddonAzureBackupEdInstance *csak* a [biztonsági mentési](https://docs.microsoft.com/azure/backup/configure-reports)jelentésekerőforrás-specifikus módjában támogatott. *Ha azure diagnosztikai módban próbál adatokat küldeni a hat eseményhez, a biztonsági mentési jelentésekben nem lesznek láthatók adatok.*

## <a name="legacy-event"></a>Örökölt esemény

Hagyományosan a tároló biztonsági mentéssel kapcsolatos diagnosztikai adatait egyetlen AzureBackupReport nevű esemény tartalmazta. Az itt leírt hat esemény lényegében az AzureBackupReport ban található összes adat lebontása. 

Jelenleg továbbra is támogatja az AzureBackupReport esemény visszamenőleges kompatibilitás esetén, ahol a felhasználók már létező egyéni lekérdezések ebben az eseményben. Ilyenek például az egyéni naplóriasztások és az egyéni vizualizációk. *Javasoljuk, hogy a lehető leghamarabb lépjen át az [új eseményekre.](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#diagnostics-events-available-for-azure-backup-users) * Az új események:

- A naplólekérdezésekben sokkal könnyebbé teheti az adatokkal való munkát.
- A sémák és azok szerkezetének jobb felderíthetőségét biztosítja.
- A teljesítmény javítása a betöltési késés és a lekérdezési idő között. 

*Az örökölt esemény az Azure diagnosztikai módban végül elavult. Az új események kiválasztása segíthet elkerülni az összetett áttelepítéseket egy későbbi időpontban.* A Log Analytics szolgáltatást használó [jelentéskészítő megoldásunk](https://docs.microsoft.com/azure/backup/configure-reports) leállítja az örökölt eseményből származó adatok támogatását is.

### <a name="steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace"></a>A Log Analytics-munkaterület új diagnosztikai beállításaira való áttérés lépései

1. Az örökölt esemény és az azokhoz tartozó előfizetések használatával azonosíthatja, hogy mely tárolók küldenek adatokat a Log Analytics-munkaterületekre. Futtassa a következő munkaterületek et a tárolók és előfizetések azonosításához.

    ````Kusto
    let RangeStart = startofday(ago(3d));
    let VaultUnderAzureDiagnostics = (){
        AzureDiagnostics
        | where TimeGenerated >= RangeStart | where Category == "AzureBackupReport" and OperationName == "Vault" and SchemaVersion_s == "V2"
        | summarize arg_max(TimeGenerated, *) by ResourceId    
        | project ResourceId, Category};
    let VaultUnderResourceSpecific = (){
        CoreAzureBackup
        | where TimeGenerated >= RangeStart | where OperationName == "Vault" 
        | summarize arg_max(TimeGenerated, *) by ResourceId
        | project ResourceId, Category};
        // Some Workspaces will not have AzureDiagnostics Table, hence you need to use isFuzzy
    let CombinedVaultTable = (){
        CombinedTable | union isfuzzy = true 
        (VaultUnderAzureDiagnostics() ),
        (VaultUnderResourceSpecific() )
        | distinct ResourceId, Category};
    CombinedVaultTable | where Category == "AzureBackupReport"
    | join kind = leftanti ( 
    CombinedVaultTable | where Category == "CoreAzureBackup"
    ) on ResourceId
    | parse ResourceId with * "SUBSCRIPTIONS/" SubscriptionId:string "/RESOURCEGROUPS" * "MICROSOFT.RECOVERYSERVICES/VAULTS/" VaultName:string
    | project ResourceId, SubscriptionId, VaultName
    ````

1. Az Azure Backup [beépített Azure-szabályzatával](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics) új diagnosztikai beállítást adhat hozzá egy adott hatókör összes tárolójának. Ez a házirend új diagnosztikai beállítást ad hozzá a tárolókhoz, amelyek vagy nem rendelkeznek diagnosztikai beállítással, vagy csak egy örökölt diagnosztikai beállítással rendelkeznek. Ez a szabályzat egyszerre egy teljes előfizetéshez vagy erőforráscsoporthoz rendelhető hozzá. Tulajdonosi hozzáféréssel kell rendelkeznie minden olyan előfizetéshez, amelyhez a szabályzat hozzá van rendelve.

Előfordulhat, hogy külön diagnosztikai beállításokat az AzureBackupReport és a hat új események, amíg az összes egyéni lekérdezések az új táblák ból származó adatok használatára. Az alábbi képen egy olyan tároló, amely két diagnosztikai beállításokat mutat be. Az első **beállítás1,** egy AzureBackupReport-esemény adatait küldi el egy Log Analytics-munkaterületre Az Azure diagnosztikai módban. A második beállítás, **a Setting2**, a hat új Azure Backup-esemény adatait küldi el egy Log Analytics-munkaterületre az erőforrás-specifikus módban.

![Két beállítás](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> Az AzureBackupReport esemény *csak* Az Azure diagnosztikai módban támogatott. *Ha erőforrás-specifikus módban próbál adatokat küldeni az eseményhez, nem fog adata a Log Analytics-munkaterületre.*

> [!NOTE]
> Az **Azure-diagnosztika** vagy **az Erőforrás-specifikus** kapcsoló csak akkor jelenik meg, ha a felhasználó a **Küldés a Log Analytics szolgáltatásba lehetőséget választja.** Adatok küldése egy tárfiókba vagy egy eseményközpont, a felhasználó kiválasztja a szükséges célt, és bejelöli a jelölőnégyzetek bármelyik kívánt események, további bemenetek nélkül. Ismét azt javasoljuk, hogy ne válassza ki az örökölt esemény AzureBackupReport megy előre.

## <a name="send-azure-site-recovery-events-to-log-analytics"></a>Az Azure Site Recovery események küldése a Log Analytics szolgáltatásba

Az Azure Backup és az Azure Site Recovery események küldése ugyanabból a Recovery Services-tárolóból. Az Azure Site Recovery jelenleg nem érhető el erőforrás-specifikus táblák. Azok a felhasználók, akik az Azure Site Recovery-eseményeket a Log Analytics szolgáltatásba szeretnék küldeni, *csak*az Azure diagnosztikai mód használatára vannak irányítva, ahogy az a képen látható. *Az Azure Site Recovery események erőforrás-specifikus módjának kiválasztása megakadályozza a szükséges adatok elküldését a Log Analytics munkaterületre.*

![Hely-helyreállítási események](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

Összegezve:

* Ha már rendelkezik a Log Analytics-diagnosztika beállításával az Azure Diagnostics szolgáltatással, és ezen felül írott egyéni lekérdezéseket írt, őrizheti meg a *beállítást,* amíg át nem telepíti a lekérdezéseket az új események ből származó adatok használatához.
* Ha azt is szeretné, hogy új táblákba szeretne beszállni, az általunk ajánlottan hozzon létre egy **új** diagnosztikai beállítást, válassza **az Erőforrás-specifikus**lehetőséget, és válassza ki a hat új eseményt.
* Ha jelenleg az Azure Site Recovery-eseményeket küldi a Log Analytics szolgáltatásba, *ne* válassza ki ezekhez az eseményekhez az erőforrás-specifikus módot. Ellenkező esetben ezek az események adatai nem áramlanak a Log Analytics-munkaterületre. Ehelyett hozzon létre egy további diagnosztikai beállítást, válassza az **Azure-diagnosztika**lehetőséget, és válassza ki a megfelelő Azure Site Recovery-eseményeket.

Az alábbi képen egy olyan felhasználó példája látható, aki három diagnosztikai beállítással rendelkezik egy tárolóhoz. Az első **beállítás1**beállítás , adatokat küld egy AzureBackupReport esemény egy Log Analytics munkaterület Azure diagnosztikai módban. A második beállítás, **a Setting2**, adatokat küld a hat új Azure Backup-események egy Log Analytics-munkaterület az erőforrás-specifikus módban. A harmadik beállítás, **a Setting3,** adatokat küld az Azure Site Recovery események egy Log Analytics munkaterület Azure diagnosztikai módban.

![Három beállítás](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>További lépések

[Ismerje meg a Log Analytics adatmodelljét a diagnosztikai eseményekhez](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)
