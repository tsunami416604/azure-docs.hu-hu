---
title: Diagnosztikai beállítások használata a Recovery Services-tárolókhoz
description: A régi és az új diagnosztikai események azure backup-hoz való használatát ismertető cikk
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: d10bedf3818559971eff12624152d0e797f6c3cc
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672786"
---
# <a name="using-diagnostics-settings-for-recovery-services-vaults"></a>Diagnosztikai beállítások használata Recovery Services-tárolókhoz

Az Azure Backup diagnosztikai eseményeket küld, amelyek összegyűjthetők és felhasználhatók elemzés, riasztás és jelentéskészítés céljából. 

A helyreállítási szolgáltatások trezordiagnosztikai beállításait az Azure Portalon keresztül konfigurálhatja, ha a tárolóra navigál, és a **Diagnosztikai beállítások** menüelemre kattint. A **+ Diagnosztikai beállítás hozzáadása** elemre kattintva egy vagy több diagnosztikai eseményt küldhet egy tárfiókba, az Eseményközpontba vagy a Log Analytics (LA) munkaterületre.

![Diagnosztikai beállítások panel](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Az Azure biztonsági mentést lehetővé tőinek elérhető diagnosztikai eseményei

Az Azure Backup a következő diagnosztikai eseményeket biztosítja, amelyek mindegyike részletes adatokat biztosít a biztonsági mentéssel kapcsolatos összetevők adott készletéről:

* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolicy
* AddonAzureBackupTárhely

[Adatmodell az Azure biztonsági mentésdiagnosztikai eseményeihez](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)

Ezek az események adatait lehet küldeni egy tárfiók, LA munkaterület, vagy egy Event Hub. Ha ezeket az adatokat la-munkaterületre küldi, ki kell választania az **Erőforrás-specifikus** kapcsolót a **Diagnosztika beállítása** képernyőn (további információt az alábbi szakaszokban talál).

## <a name="using-diagnostics-settings-with-log-analytics-la"></a>Diagnosztikai beállítások használata a Log Analytics szolgáltatással (LA)

Az Azure Log Analytics-ütemtervhez igazodva az Azure Backup most már lehetővé teszi, hogy a tárolódiagnosztikai adatokat dedikált LA-táblákbiztonsági célokra küldje el. Ezeket [erőforrás-specifikus tábláknak nevezzük.](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace#resource-specific)

A trezor diagnosztikai adatainak la-be való elküldése:

1.    Keresse meg a trezort, és kattintson a **Diagnosztikai beállítások gombra.** Kattintson **a + Diagnosztikai beállítás hozzáadása gombra.**
2.    Adjon nevet a Diagnosztika beállításnak.
3.    Jelölje be a **Küldés a Log Analytics szolgáltatásba** jelölőnégyzetet, és válasszon egy Log Analytics-munkaterületet.
4.    Válassza **az Erőforrás-specifikus lehetőséget** a váltóban, és ellenőrizze a következő hat eseményt : **CoreAzureBackup**, **AddonAzureBackupAlerts**, **AddonAzureBackupedInstance**, **AddonAzureBackupJobs**, **AddonAzureBackupPolicy**és **AddonAzureBackupStorage**.
5.    Kattintson a **Mentés gombra.**

![Erőforrás-specifikus mód](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

Amint az adatok beáramlanak az LA-munkaterületre, az egyes eseményekhez dedikált táblák jönnek létre a munkaterületen. Ezen táblák bármelyikét közvetlenül lekérdezheti, és szükség esetén egyesítheti vagy egyesítheti ezeket a táblákat.

> [!IMPORTANT]
> A fenti hat esemény, nevezetesen a CoreAzureBackup, az AddonAzureBackupAlerts, az AddonAzureBackupedInstance, az AddonAzureBackupJobs, az AddonAzureBackupPolicy és az AddonAzureBackupStorage **csak** erőforrás-specifikus módban támogatott a [biztonsági másolatkészítési jelentésekben.](https://docs.microsoft.com/azure/backup/configure-reports) **Kérjük, vegye figyelembe, hogy ha az Azure diagnosztikai módban megpróbál adatokat küldeni a hat eseményhez, a biztonsági mentési jelentésekben nem lesznek láthatók adatok.**

## <a name="legacy-event"></a>Örökölt esemény

Hagyományosan a tároló összes biztonsági mentéssel kapcsolatos diagnosztikai adata egyetlen eseményben található, amelyet "AzureBackupReport" nevű esemény tartalmaz. A fent leírt hat esemény lényegében az AzureBackupReport ban található összes adat lebontása. 

Jelenleg továbbra is támogatjuk az AzureBackupReport eseményt a visszamenőleges kompatibilitás érdekében, azokban az esetekben, amikor a felhasználók már rendelkeznek egyéni lekérdezésekkel ezen az eseményen, például egyéni naplóriasztások, egyéni vizualizációk stb. Azonban **azt javasoljuk, hogy az [új eseményekre](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events#diagnostics-events-available-for-azure-backup-users) való mielőbb idot,** mivel ez sokkal könnyebbé teszi az adatok használatát a naplólekérdezésekben, jobb felderíthetőséget biztosít a sémák és azok szerkezete, javítja a teljesítményt mind a betöltési késés, mind a lekérdezési idő között. 

**Az örökölt esemény az Azure Diagnosztika módban végül elavulttá válik, és így az új események kiválasztása segíthet elkerülni az összetett áttelepítéseket egy későbbi időpontban.** A Log Analytics szolgáltatást használó [jelentéskészítő megoldásunk](https://docs.microsoft.com/azure/backup/configure-reports) leállítja az örökölt eseményből származó adatok támogatását is.

### <a name="steps-to-move-to-new-diagnostics-settings-to-log-analytics-workspace"></a>Az új diagnosztikai beállításokra való áttérés lépései (a Log Analytics munkaterületre)

1. Azonosítsa, hogy mely tárolók küldenek adatokat a Log Analytics-munkaterület(ek)nek az örökölt esemény használatával, és az előfizetéseket, amelyekhez tartoznak. Az alábbi munkaterületek futtatásával azonosíthatja ezeket a tárolókat és előfizetéseket:

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

2. Az Azure Backup [beépített Azure-szabályzatával](https://docs.microsoft.com/azure/backup/azure-policy-configure-diagnostics) új diagnosztikai beállítást adhat hozzá egy adott hatókör összes tárolójának. Ez a házirend új diagnosztikai beállítást ad hozzá azokhoz a tárolókhoz, amelyek nem rendelkeznek diagnosztikai beállítással (vagy) csak egy örökölt diagnosztikai beállítással rendelkeznek. Ez a szabályzat egyszerre egy teljes előfizetéshez vagy erőforráscsoporthoz rendelhető hozzá. Vegye figyelembe, hogy minden olyan előfizetéshez " tulajdonosi" hozzáférésre lesz szüksége, amelyhez a szabályzat hozzá van rendelve.

Dönthet úgy, hogy külön diagnosztikai beállításokat azureBackupreport és a hat új események, amíg az összes egyéni lekérdezések az új táblák ból származó adatok használatára. Az alábbi képen egy olyan tároló látható, amelynek két diagnosztikai beállítása van. Az első **beállítás1 az** AzureBackupReport-esemény adatait küldi egy LA-munkaterületre AzureDiagnostics módban. A második beállítás, **a Setting2** a hat új Azure Backup-esemény adatait küldi el egy LA-munkaterületre erőforrás-specifikus módban.

![Két beállítás](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> Az AzureBackupReport esemény **csak** Azure diagnosztikai módban támogatott. **Kérjük, vegye figyelembe, hogy ha erőforrás-specifikus módban próbál adatokat küldeni az eseményhez, nem fog adatáramlani a LA-munkaterületre.**

> [!NOTE]
> Az Azure Diagnostics / Resource Specific kapcsoló csak akkor jelenik meg, ha a felhasználó ellenőrzi a **Küldés a Log Analytics szolgáltatásba**című fájlt. Adatok küldése egy tárfiókba vagy egy Event Hub, a felhasználó egyszerűen válassza ki a kívánt célt, és ellenőrizze a kívánt eseményeket, anélkül, hogy további bemenetek. Ismét ajánlott, hogy ne válassza ki az örökölt esemény AzureBackupReport, előrehaladva.

## <a name="users-sending-azure-site-recovery-events-to-log-analytics-la"></a>Az Azure-webhely-helyreállítási eseményeket küldő felhasználók a Log Analytics (LA) szolgáltatásba

Az Azure Backup és az Azure site recovery események ugyanattól a Recovery Services Vaultból kerülnek elküldésre. Mivel az Azure Site Recovery jelenleg nincs beszervezve az erőforrás-specifikus táblákba, az Azure Site Recovery Events la-be küldeni kívánt felhasználók **csak** az Azure Diagnosztikai mód használatára irányulnak (lásd az alábbi képet). **Ha az Azure site recovery-eseményekhez erőforrás-specifikus módot választ, a rendszer megakadályozza a szükséges adatok küldését az LA-munkaterületre.**

![Hely-helyreállítási események](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

Összefoglalva a fenti árnyalatok:

* Ha már rendelkezik LA diagnosztika beállítása az Azure Diagnostics és a írt egyéni lekérdezések tetején, tartsa meg a beállítást **érintetlenül,** amíg át nem telepíti a lekérdezéseket az új események adatainak felhasználásához.
* Ha új táblákba is szeretne beszállni (az ajánlott módon), hozzon létre egy **új** diagnosztikai beállítást, válassza az **Erőforrás-specifikus** lehetőséget, és válassza ki a fent megadott hat új eseményt.
* Ha jelenleg az Azure Site Recovery események et küld la, **ne** válassza erőforrás-specifikus mód ezeket az eseményeket, különben az ilyen események adatai nem áramlik a LA-munkaterületre. Ehelyett hozzon létre egy **további diagnosztikai beállítást,** válassza az **Azure Diagnosztika**lehetőséget, és válassza ki a megfelelő Azure Site Recovery-eseményeket.

Az alábbi képen egy példa egy felhasználó három diagnosztikai beállítások at a tároló. Az első **beállítás1 nevű** adatokat küld az AzureBackupReport eseményegy LA-munkaterület i AzureDiagnostics módban. A második beállítás, **a Setting2** adatokat küld a hat új Azure Backup-eseményről egy LA-munkaterületre erőforrás-specifikus módban. A harmadik beállítás, **a Setting3,** adatokat küld az Azure Site Recovery események egy LA-munkaterület i Azure diagnosztikai módban.

![Három beállítás](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>További lépések

[Ismerje meg a diagnosztikai események naplóelemzési adatmodelljét](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)
