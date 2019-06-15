---
title: Az Azure Monitor naplók adatmodell az Azure Backup
description: Ez a cikk előadások az Azure Monitor kapcsolatos adatok modell részletei az Azure Backup adatokat naplózza.
services: backup
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: adigan
ms.openlocfilehash: dd4dad2cc3e541d3b6866c02341161dc1d9e1e6c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61234971"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Log Analytics-adatmodell az Azure biztonsági mentési adatok

A Log Analytics data model használatával hozzon létre egyéni riasztások a Log Analytics.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="using-azure-backup-data-model"></a>Az Azure Backup data modellel

Használhatja az adatmodellt részeként a következő mezőket igény szerint Vizualizációk, az egyéni lekérdezések és az irányítópult létrehozásához.

### <a name="alert"></a>Riasztás

Ez a táblázat részletesen riasztási kapcsolódó mezőket.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| AlertUniqueId_s |Text |A generált riasztások egyedi azonosítója |
| AlertType_s |Text |Riasztás, ha például biztonsági mentés típusa |
| AlertStatus_s |Text |A riasztás állapotának, például aktív |
| AlertOccurrenceDateTime_s |Dátum/idő |Dátum és idő, a riasztás létrehozásakor |
| AlertSeverity_s |Text |A riasztás súlyosságát, például kritikus |
|AlertTimeToResolveInMinutes_s    | Szám        |Riasztás megoldásához szükséges idő. Aktív riasztások esetén üres.         |
|AlertConsolidationStatus_s   |Text         |Ha a riasztás egy összevont riasztás vagy nem azonosítása         |
|CountOfAlertsConsolidated_s     |Szám         |Ha összevont riasztás összevont riasztások száma          |
|AlertRaisedOn_s     |Text         |A riasztás akkor jön létre az entitás típusa         |
|AlertCode_s     |Text         |A kód egy riasztási típus egyedi azonosításához         |
|RecommendedAction_s   |Text         |A riasztás feloldásához jelenítse javasolt művelet         |
| EventName_s |Text |Název události. Always AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |A riasztáshoz tartozó biztonsági mentési elem egyedi azonosítója |
| SchemaVersion_s |Text |A séma, például jelenlegi verziója **V2** |
| State_s |Text |A riasztási, például az aktív, a törölt objektum jelenlegi állapota |
| BackupManagementType_s |Text |Szolgáltató típusa végrehajtásához, amelyhez ez a riasztás tartozik, a biztonsági mentés, például IaaSVM fájlmappa |
| OperationName |Text |Az aktuális művelet, például a riasztás neve |
| Category |Text |Diagnosztikai adatok az Azure Monitor naplóira leküldve kategóriáját. Always AzureBackupReport |
| Resource |Text |Ez az erőforrás, amelynek adatokat gyűjteni, azt mutatja, hogy a helyreállítási tár neve |
| ProtectedServerUniqueId_s |Text |A védett kiszolgálón, a riasztáshoz tartozó egyedi azonosítója |
| VaultUniqueId_s |Text |A védett tárolóhoz a riasztáshoz tartozó egyedi azonosítója |
| SourceSystem |Text |A forrásrendszerben az aktuális adatok – Azure |
| ResourceId |Text |Arról, hogy mely adatok gyűjtése az erőforrás egyedi azonosítója. Ha például egy Recovery Services vault erőforrás-azonosító |
| SubscriptionId |Text |Az erőforrás (például az előfizetés-azonosító Recovery Services-tároló), amelynek az adatgyűjtés történik |
| ResourceGroup |Text |Az erőforrás (például erőforráscsoport Recovery Services-tároló), amelynek az adatgyűjtés történik |
| ResourceProvider |Text |Erőforrás-szolgáltató, amelynek az adatgyűjtés történik. Ha például a Microsoft.recoveryservices szolgáltatónál |
| ResourceType |Text |Erőforrás típusa, amelynek az adatgyűjtés történik. Ha például a tárolók |

### <a name="backupitem"></a>BackupItem

Ez a táblázat részletesen a biztonsági mentési elem kapcsolatos mezőket.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| EventName_s |Text |Název události. Always AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |A biztonsági mentési elem egyedi azonosítója |
| BackupItemId_s |Text |Biztonsági mentési elem azonosítója |
| BackupItemName_s |Text |Biztonsági másolati elem nevét |
| BackupItemFriendlyName_s |Text |Biztonságimásolat-elem rövid neve |
| BackupItemType_s |Text |Biztonsági mentési elem, például a virtuális gép, fájlmappa típusa |
| ProtectionState_s |Text |A biztonsági másolati elem, például a védett, ProtectionStopped aktuális védelmi állapotát |
| ProtectionGroupName_s |Text | A védelmi csoport biztonságimásolat-elem nevét védett, SC DPM és a MABS, ha van ilyen|
| SecondaryBackupProtectionState_s |Text |Másodlagos védelem engedélyezve van-e a biztonsági mentési elem|
| SchemaVersion_s |Text |Ha például a séma verziója **V2** |
| State_s |Text |A biztonsági másolati elem objektum, például az aktív, a törölt állapota |
| BackupManagementType_s |Text |Szolgáltató típusát, amelyre a biztonsági mentési cikkhez tartozó biztonsági mentési, például IaaSVM fájlmappa végrehajtásához |
| OperationName |Text |A művelet, például BackupItem neve |
| Category |Text |Diagnosztikai adatok az Azure Monitor naplóira leküldve kategóriáját. Always AzureBackupReport |
| Resource |Text |Összegyűjtött adatok erőforrás, például a helyreállítási tár neve |
| SourceSystem |Text |A forrásrendszerben az aktuális adatok – Azure |
| ResourceId |Text |Erőforrás-azonosító a begyűjtött adatok, például Recovery Services-tároló az erőforrás-azonosító |
| SubscriptionId |Text |Az erőforrás előfizetés-azonosító (az ex. Recovery Services-tároló) gyűjtött adatok |
| ResourceGroup |Text |Az erőforrás erőforráscsoportot (az ex. Recovery Services-tároló) gyűjtött adatok |
| ResourceProvider |Text |Erőforrás-szolgáltató adatokat gyűjtenek, például a Microsoft.recoveryservices szolgáltatónál |
| ResourceType |Text |Az erőforrás számára gyűjtött adatok típusától, írja be például, tárolók |

### <a name="backupitemassociation"></a>BackupItemAssociation

Ez a táblázat a különböző entitások biztonsági másolati elem társítások részletesen ismerteti.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| EventName_s |Text |Ez a mező képviseli az esemény nevét, a rendszer mindig AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |A biztonsági másolati elem egyedi azonosítója |
| SchemaVersion_s |Text |Ebben a mezőben azt jelzi, hogy a jelenlegi verzióját, a sémát, hogy az **V2** |
| State_s |Text |A biztonsági másolati elem társítás objektum, például az aktív, a törölt aktuális állapotát |
| BackupManagementType_s |Text |Ennek során a biztonsági mentési feladat, például IaaSVM, fájlmappa kiszolgáló a szolgáltató típusát |
| BackupItemSourceSize_s |Text | A biztonsági másolati elem előtérmérete |
| BackupManagementServerUniqueId_s |Text | A biztonsági mentés felügyeleti kiszolgáló a biztonsági mentési elemek egyedi azonosításához mező védett keresztül, ha van ilyen |
| Category |Text |Ez a mező képviseli a diagnosztikai adatok a Log Analytics szolgáltatásba leküldött kategória, AzureBackupReport |
| OperationName |Text |Ez a mező képviseli az aktuális művelet – BackupItemAssociation neve |
| Resource |Text |Ez az erőforrás, amelynek adatokat gyűjteni, azt mutatja, hogy a helyreállítási tár neve |
| PolicyUniqueId_g |Text |A szabályzathoz társított a biztonságimásolat-elem egyedi azonosítója |
| ProtectedServerUniqueId_s |Text |A védett kiszolgálón, a biztonsági mentési elemhez társított egyedi azonosítója |
| VaultUniqueId_s |Text |A tárolóban, amely tartalmazza a biztonsági másolati elem egyedi azonosítója |
| SourceSystem |Text |A forrásrendszerben az aktuális adatok – Azure |
| ResourceId |Text |A begyűjtött adatok erőforrás-azonosítója. Ha például a helyreállítási tár az erőforrás-azonosító |
| SubscriptionId |Text |Az erőforrás előfizetés-azonosító (az ex. Recovery Services-tároló), amelynek összegyűjtött adatok |
| ResourceGroup |Text |Az erőforrás erőforráscsoportot (az ex. Recovery Services-tároló), amelynek összegyűjtött adatok |
| ResourceProvider |Text |Erőforrás-szolgáltató adatokat gyűjtenek, például a Microsoft.recoveryservices szolgáltatónál |
| ResourceType |Text |Az erőforrástípust a adatokat gyűjtenek, például a tárolók |

### <a name="backupmanagementserver"></a>BackupManagementServer

Ez a táblázat a különböző entitások biztonsági másolati elem társítások részletesen ismerteti.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
|BackupManagementServerName_s     |Text         |A biztonságimásolat-felügyeleti kiszolgáló neve        |
|AzureBackupAgentVersion_s     |Text         |A biztonságimásolat-felügyeleti kiszolgálón az Azure Backup-ügynök verziója          |
|BackupManagementServerVersion_s     |Text         |A biztonságimásolat-felügyeleti kiszolgáló verziója|
|BackupManagementServerOSVersion_s    |Text            |A biztonsági mentés felügyeleti kiszolgáló operációsrendszer-verzió|
|BackupManagementServerType_s     |Text         |A biztonságimásolat-felügyeleti kiszolgáló, mint a MABS, SC DPM típusa|
|BackupManagementServerUniqueId_s     |Text         |A biztonsági mentés felügyeleti kiszolgáló egyedi azonosításához mező       |
| SourceSystem |Text |A forrásrendszerben az aktuális adatok – Azure |
| ResourceId |Text |A begyűjtött adatok erőforrás-azonosítója. Ha például a helyreállítási tár az erőforrás-azonosító |
| SubscriptionId |Text |Az erőforrás előfizetés-azonosító (az ex. Recovery Services-tároló), amelynek összegyűjtött adatok |
| ResourceGroup |Text |Az erőforrás erőforráscsoportot (az ex. Recovery Services-tároló), amelynek összegyűjtött adatok |
| ResourceProvider |Text |Erőforrás-szolgáltató adatokat gyűjtenek, például a Microsoft.recoveryservices szolgáltatónál |
| ResourceType |Text |Az erőforrástípust a adatokat gyűjtenek, például a tárolók |

### <a name="job"></a>Feladat

Ez a táblázat részletesen a feladathoz kapcsolódó mezőket.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| EventName_s |Text |Název události. Always AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |A biztonsági mentési elem egyedi azonosítója |
| SchemaVersion_s |Text |Ha például a séma verziója **V2** |
| State_s |Text |A feladatobjektumot, például az aktív, a törölt aktuális állapotát |
| BackupManagementType_s |Text |Ennek során a biztonsági mentési feladat, például IaaSVM, fájlmappa kiszolgáló a szolgáltató típusát |
| OperationName |Text |Ez a mező annak neve, az aktuális művelet - feladat |
| Category |Text |Ez a mező képviseli a diagnosztikai adatok az Azure Monitor naplóira leküldve kategória, AzureBackupReport |
| Resource |Text |Ez az erőforrás, amelynek adatokat gyűjteni, azt mutatja, hogy a helyreállítási tár neve |
| ProtectedServerUniqueId_s |Text |A védett kiszolgáló egyedi azonosítója a feladathoz hozzárendelt |
| ProtectedContainerUniqueId_s |Text | A feladat futtatásakor a védett tároló azonosításához használatos egyedi azonosító |
| VaultUniqueId_s |Text |A védett tároló egyedi azonosítója |
| JobOperation_s |Text |A művelet, amelynek feladat futtatása például biztonsági mentés, visszaállítás, a Backup konfigurálása |
| JobStatus_s |Text |A befejezett feladat, például befejezve, sikertelen állapota |
| JobFailureCode_s |Text |Sikertelen hibakód karakterláncát miatt, amely feladat hiba történt |
| JobStartDateTime_s |Dátum/idő |Dátum és idő elindított futó feladat |
| BackupStorageDestination_s |Text |A biztonsági mentési tár, például felhőben lemez rendeltetési  |
| AdHocOrScheduledJob_s |Text | A mezőben adhatja meg, ha a feladat alkalmi vagy ütemezett |
| JobDurationInSecs_s | Szám |Feladatok teljes időtartama (másodpercben) |
| DataTransferredInMB_s | Szám |A feladat MB-ban átvitt adatok|
| JobUniqueId_g |Text |A feladat azonosításához használatos egyedi azonosító |
| RecoveryJobDestination_s |Text | Ahol a állítja helyre a rendszer egy helyreállítási feladat cél |
| RecoveryJobRPDateTime_s |DateTime | A dátum, a helyreállítási pontot, amelyik a helyreállítandó létrehozásának ideje |
| RecoveryJobRPLocation_s |Text | A helyreállítási pontot, amelyik a helyreállítandó tárolásának helyét|
| SourceSystem |Text |A forrásrendszerben az aktuális adatok – Azure |
| ResourceId |Text |A begyűjtött adatok erőforrás-azonosítója. Ha például a helyreállítási tár az erőforrás-azonosító|
| SubscriptionId |Text |Az erőforrás (például az előfizetés-azonosító Recovery Services-tároló), amelynek az adatgyűjtés történik |
| ResourceGroup |Text |Az erőforrás (például erőforráscsoport Recovery Services-tároló), amelynek az adatgyűjtés történik |
| ResourceProvider |Text |Erőforrás-szolgáltató, amelynek az adatgyűjtés történik. Ha például a Microsoft.recoveryservices szolgáltatónál |
| ResourceType |Text |Erőforrás típusa, amelynek az adatgyűjtés történik. Ha például a tárolók |

### <a name="policy"></a>Szabályzat

Ez a táblázat részletesen házirendekhez kapcsolódó mezőket.

| Mező | Adattípus | Verzió érvényes | Leírás |
| --- | --- | --- | --- |
| EventName_s |Text ||Ez a mező képviseli az esemény nevét, a rendszer mindig AzureBackupCentralReport |
| SchemaVersion_s |Text ||Ebben a mezőben azt jelzi, hogy a jelenlegi verzióját, a sémát, hogy az **V2** |
| State_s |Text ||A csoportházirend-objektum, például az aktív, a törölt aktuális állapotát |
| BackupManagementType_s |Text ||Ennek során a biztonsági mentési feladat, például IaaSVM, fájlmappa kiszolgáló a szolgáltató típusát |
| OperationName |Text ||Ez a mező képviseli az aktuális művelet - házirend neve |
| Category |Text ||Ez a mező képviseli a diagnosztikai adatok az Azure Monitor naplóira leküldve kategória, AzureBackupReport |
| Resource |Text ||Ez az erőforrás, amelynek adatokat gyűjteni, azt mutatja, hogy a helyreállítási tár neve |
| PolicyUniqueId_g |Text ||Azonosíthatja a szabályzat egyedi azonosítója |
| PolicyName_s |Text ||A megadott házirend neve |
| BackupFrequency_s |Text ||Amellyel biztonsági mentések futnak, például, napi, heti gyakoriság |
| BackupTimes_s |Text ||Dátum és idő, amikor a biztonsági mentés van ütemezve |
| BackupDaysOfTheWeek_s |Text ||Ha biztonsági mentések ütemezett napjai |
| RetentionDuration_s |Egész szám ||A konfigurált biztonsági mentés megőrzési időtartama |
| DailyRetentionDuration_s |Egész szám ||Teljes megőrzés időtartama napokban megadva a konfigurált biztonsági mentés |
| DailyRetentionTimes_s |Text ||Dátum és idő, amikor napi megőrzés konfigurálása |
| WeeklyRetentionDuration_s |Tizedes tört szám ||Teljes hetes, a konfigurált biztonsági mentés heti megőrzési időtartama |
| WeeklyRetentionTimes_s |Text ||Dátum és idő, ha a heti megőrzési van konfigurálva |
| WeeklyRetentionDaysOfTheWeek_s |Text ||Heti megőrzési kijelölve a hét napjai |
| MonthlyRetentionDuration_s |Tizedes tört szám ||Hónap konfigurált biztonsági mentés a teljes megőrzési időtartama |
| MonthlyRetentionTimes_s |Text ||Dátum és idő, ha a havi megőrzési van konfigurálva |
| MonthlyRetentionFormat_s |Text ||A havi megőrzési, például a napi szintű, hetente alapú hét naponta konfigurációtípust |
| MonthlyRetentionDaysOfTheWeek_s |Text ||Havi megőrzési kijelölve a hét napjai |
| MonthlyRetentionWeeksOfTheMonth_s |Text ||Hét, a hónap, ha a havi megőrzési van konfigurálva, például First, Last stb. |
| YearlyRetentionDuration_s |Tizedes tört szám ||Teljes megőrzési időtartam a konfigurált biztonsági mentés éves |
| YearlyRetentionTimes_s |Text ||Dátum és idő, amikor éves megőrzési van konfigurálva |
| YearlyRetentionMonthsOfTheYear_s |Text ||Az év hónapjai éves megőrzési kiválasztva |
| YearlyRetentionFormat_s |Text ||Az éves megőrzési, például a napi szintű, hetente alapú hét naponta konfigurációtípust | |
| YearlyRetentionDaysOfTheMonth_s |Text ||A hónap, éves megőrzési kijelölt dátumok |
| SynchronisationFrequencyPerDay_s |Egész szám |v2|A biztonsági mentés szinkronizálása SC DPM és a MABS nap száma |
| DiffBackupFormat_s |Text |v2|Különbségi biztonsági másolatok formátuma az SQL Azure virtuális gép biztonsági mentése |
| DiffBackupTime_s |Time |v2|Az SQL-lel az Azure VM Backup különbözeti biztonsági mentések idejét|
| DiffBackupRetentionDuration_s |Tizedes tört szám |v2|Az SQL-lel az Azure VM Backup különbségi biztonsági másolatok megőrzési időtartama|
| LogBackupFrequency_s |Tizedes tört szám |v2|Az SQL a Naplóalapú biztonsági mentések gyakoriságát|
| LogBackupRetentionDuration_s |Tizedes tört szám |v2|Az SQL-lel az Azure virtuális gépek biztonsági mentését a Naplóalapú biztonsági mentések megőrzési időtartama|
| DiffBackupDaysofTheWeek_s |Text |v2|A használt SQL-lel az Azure VM Backup különbségi biztonsági másolatok hét napjai|
| SourceSystem |Text ||A forrásrendszerben az aktuális adatok – Azure |
| ResourceId |Text ||A begyűjtött adatok erőforrás-azonosítója. Ha például a helyreállítási tár az erőforrás-azonosító |
| SubscriptionId |Text ||Az erőforrás (például az előfizetés-azonosító Recovery Services-tároló), amelynek az adatgyűjtés történik |
| ResourceGroup |Text ||Az erőforrás (például erőforráscsoport Recovery Services-tároló), amelynek az adatgyűjtés történik |
| ResourceProvider |Text ||Erőforrás-szolgáltató, amelynek az adatgyűjtés történik. Ha például a Microsoft.recoveryservices szolgáltatónál |
| ResourceType |Text ||Erőforrás típusa, amelynek az adatgyűjtés történik. Ha például a tárolók |

### <a name="policyassociation"></a>PolicyAssociation

Ez a táblázat a különböző entitások szabályzattársításait részletesen ismerteti.

| Mező | Adattípus | Verzió érvényes | Leírás |
| --- | --- | --- | --- |
| EventName_s |Text ||Ez a mező képviseli az esemény nevét, a rendszer mindig AzureBackupCentralReport |
| SchemaVersion_s |Text ||Ebben a mezőben azt jelzi, hogy a jelenlegi verzióját, a sémát, hogy az **V2** |
| State_s |Text ||A csoportházirend-objektum, például az aktív, a törölt aktuális állapotát |
| BackupManagementType_s |Text ||Ennek során a biztonsági mentési feladat, például IaaSVM, fájlmappa kiszolgáló a szolgáltató típusát |
| OperationName |Text ||Ez a mező képviseli az aktuális művelet – PolicyAssociation neve |
| Category |Text ||Ez a mező képviseli a diagnosztikai adatok az Azure Monitor naplóira leküldve kategória, AzureBackupReport |
| Resource |Text ||Ez az erőforrás, amelynek adatokat gyűjteni, azt mutatja, hogy a helyreállítási tár neve |
| PolicyUniqueId_g |Text ||Azonosíthatja a szabályzat egyedi azonosítója |
| VaultUniqueId_s |Text ||A tárolóban, amelyhez ez a szabályzat tartozik, az egyedi azonosítója |
| BackupManagementServerUniqueId_s |Text |v2 |A biztonsági mentés felügyeleti kiszolgáló a biztonsági mentési elemek egyedi azonosításához mező védett keresztül, ha van ilyen        |
| SourceSystem |Text ||A forrásrendszerben az aktuális adatok – Azure |
| ResourceId |Text ||A begyűjtött adatok erőforrás-azonosítója. Ha például a helyreállítási tár az erőforrás-azonosító |
| SubscriptionId |Text ||Az erőforrás (például az előfizetés-azonosító Recovery Services-tároló), amelynek az adatgyűjtés történik |
| ResourceGroup |Text ||Az erőforrás (például erőforráscsoport Recovery Services-tároló), amelynek az adatgyűjtés történik |
| ResourceProvider |Text ||Erőforrás-szolgáltató, amelynek az adatgyűjtés történik. Ha például a Microsoft.recoveryservices szolgáltatónál |
| ResourceType |Text ||Erőforrás típusa, amelynek az adatgyűjtés történik. Ha például a tárolók |

### <a name="protected-container"></a>Védett tároló

Ez a táblázat védett tárolók kapcsolatos alapvető mezőket is tartalmazza. (A ProtectedServer volt a v1-ben)

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Text | A mező egy védett tároló egyedi azonosításához |
| ProtectedContainerOSType_s |Text |A védett tároló operációs rendszer típusa |
| ProtectedContainerOSVersion_s |Text |A védett tároló operációsrendszer-verzió |
| AgentVersion_s |Text |Az ügynök biztonsági mentési vagy a védelmi ügynök (esetén SC DPM és a MABS) verziószáma |
| BackupManagementType_s |Text |A biztonsági mentés például IaaSVM fájlmappa szolgáltató típusa |
| EntityState_s |Text |Aktív, a törölt például a védett kiszolgáló objektum aktuális állapotát |
| ProtectedContainerFriendlyName_s |Text |Védett kiszolgáló rövid neve |
| ProtectedContainerName_s |Text |A védett tároló nevét. |
| ProtectedContainerWorkloadType_s |Text |A védett tároló típusú biztonsági mentés például IaaSVMContainer |
| ProtectedContainerLocation_s |Text |Hogy a védett tároló a helyszínen vagy az Azure-ban |
| ProtectedContainerType_s |Text |A védett tároló-e a kiszolgálót, vagy egy tároló |

### <a name="storage"></a>Storage

A table storage szolgáltatással kapcsolatos mezők részletesen ismerteti.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| CloudStorageInBytes_s |Tizedes tört szám |Felhőalapú biztonsági mentési tár biztonsági mentések, számított által használt legújabb érték alapján |
| ProtectedInstances_s |Tizedes tört szám |Előtérbeli tár számlázási, számított alapján legújabb érték kiszámításához használt védett példányok száma |
| EventName_s |Text |Ez a mező képviseli az esemény nevét, a rendszer mindig AzureBackupCentralReport |
| SchemaVersion_s |Text |Ebben a mezőben azt jelzi, hogy a jelenlegi verzióját, a sémát, hogy az **V2** |
| State_s |Text |A tárolási objektum, például az aktív, a törölt aktuális állapotát |
| BackupManagementType_s |Text |Ennek során a biztonsági mentési feladat, például IaaSVM, fájlmappa kiszolgáló a szolgáltató típusát |
| OperationName |Text |Ez a mező képviseli az aktuális művelet – a tároló neve |
| Category |Text |Ez a mező képviseli a diagnosztikai adatok az Azure Monitor naplóira leküldve kategória, AzureBackupReport |
| Resource |Text |Ez az erőforrás, amelynek adatokat gyűjteni, azt mutatja, hogy a helyreállítási tár neve |
| ProtectedServerUniqueId_s |Text |A védett kiszolgálón, amelyhez tárolási számította egyedi azonosítója |
| VaultUniqueId_s |Text |Egyedi azonosítója a Storage-tároló kiszámítása |
| SourceSystem |Text |A forrásrendszerben az aktuális adatok – Azure |
| ResourceId |Text |A begyűjtött adatok erőforrás-azonosítója. Ha például a helyreállítási tár az erőforrás-azonosító |
| SubscriptionId |Text |Az erőforrás (például az előfizetés-azonosító Recovery Services-tároló), amelynek az adatgyűjtés történik |
| ResourceGroup |Text |Az erőforrás (például erőforráscsoport Recovery Services-tároló), amelynek az adatgyűjtés történik |
| ResourceProvider |Text |Erőforrás-szolgáltató, amelynek az adatgyűjtés történik. Ha például a Microsoft.recoveryservices szolgáltatónál |
| ResourceType |Text |Erőforrás típusa, amelynek az adatgyűjtés történik. Ha például a tárolók |

### <a name="storageassociation"></a>StorageAssociation

Ez a táblázat tárolási csatlakozik más entitások alapszintű storage szolgáltatással kapcsolatos mezőket is tartalmazza.

| Mező | Adattípus | Leírás |
| --- | --- |  --- |
| StorageUniqueId_s |Text |A storage entitás azonosítására használt egyedi azonosító |
| SchemaVersion_s |Text |Ebben a mezőben azt jelzi, hogy a jelenlegi verzióját, a sémát, hogy az **V2** |
| BackupItemUniqueId_s |Text |A biztonsági másolati elem a tároló entitáshoz kapcsolódó azonosítására használt egyedi azonosító |
| BackupManagementServerUniqueId_s |Text |A biztonságimásolat-felügyeleti kiszolgáló, a storage entitáshoz kapcsolódó azonosítására használt egyedi azonosító|
| VaultUniqueId_s |Text |A tárolóban, a storage entitáshoz kapcsolódó azonosítására használt egyedi azonosító|
| StorageConsumedInMBs_s |Szám|A megfelelő biztonsági mentési elemet a megfelelő tárolási által felhasznált tárterület mérete |
| StorageAllocatedInMBs_s |Szám |A megfelelő biztonsági mentési elemet a megfelelő tárolási típusú lemez által lefoglalt tárhely mérete|

### <a name="vault"></a>Tároló

Ez a táblázat részletesen tároló kapcsolatos mezőket.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| EventName_s |Text |Ez a mező képviseli az esemény nevét, a rendszer mindig AzureBackupCentralReport |
| SchemaVersion_s |Text |Ebben a mezőben azt jelzi, hogy a jelenlegi verzióját, a sémát, hogy az **V2** |
| State_s |Text |A tár objektum, például az aktív, a törölt aktuális állapotát |
| OperationName |Text |Ez a mező képviseli az aktuális művelet - tár neve |
| Category |Text |Ez a mező képviseli a diagnosztikai adatok az Azure Monitor naplóira leküldve kategória, AzureBackupReport |
| Resource |Text |Ez az erőforrás, amelynek adatokat gyűjteni, azt mutatja, hogy a helyreállítási tár neve |
| VaultUniqueId_s |Text |A tároló egyedi azonosítója |
| VaultName_s |Text |A tároló neve |
| AzureDataCenter_s |Text |Adatközpont, ahol a tároló megtalálható |
| StorageReplicationType_s |Text |A tárolóhoz, például GeoRedundant tárolóreplikáció típusa |
| SourceSystem |Text |A forrásrendszerben az aktuális adatok – Azure |
| ResourceId |Text |A begyűjtött adatok erőforrás-azonosítója. Ha például a helyreállítási tár az erőforrás-azonosító |
| SubscriptionId |Text |Az erőforrás (például az előfizetés-azonosító Recovery Services-tároló), amelynek az adatgyűjtés történik |
| ResourceGroup |Text |Az erőforrás (például erőforráscsoport Recovery Services-tároló), amelynek az adatgyűjtés történik |
| ResourceProvider |Text |Erőforrás-szolgáltató, amelynek az adatgyűjtés történik. Ha például a Microsoft.recoveryservices szolgáltatónál |
| ResourceType |Text |Erőforrás típusa, amelynek az adatgyűjtés történik. Ha például a tárolók |

### <a name="backup-management-server"></a>Biztonságimásolat-felügyeleti kiszolgáló

Ez a táblázat biztonságimásolat-felügyeleti kiszolgálók kapcsolatos alapvető mezőket is tartalmazza.

|Mező  |Adattípus  | Leírás  |
|---------|---------|----------|
|BackupManagmentServerName_s     |Text         |A biztonságimásolat-felügyeleti kiszolgáló neve        |
|AzureBackupAgentVersion_s     |Text         |A biztonságimásolat-felügyeleti kiszolgálón az Azure Backup-ügynök verziója          |
|BackupManagmentServerVersion_s     |Text         |A biztonságimásolat-felügyeleti kiszolgáló verziója|
|BackupManagmentServerOSVersion_s     |Text            |A biztonsági mentés felügyeleti kiszolgáló operációsrendszer-verzió|
|BackupManagementServerType_s     |Text         |A biztonságimásolat-felügyeleti kiszolgáló, mint a MABS, SC DPM típusa|
|BackupManagmentServerUniqueId_s     |Text         |A biztonsági mentés felügyeleti kiszolgáló egyedi azonosításához mező       |

### <a name="preferredworkloadonvolume"></a>PreferredWorkloadOnVolume

Ez a táblázat megadja a workload(s) kötet társítva.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| StorageUniqueId_s |Text |A storage entitás azonosítására használt egyedi azonosító |
| BackupItemType_s |Text |A számítási feladatokat, amelyek a kötet esetében az előnyben részesített tárhelyet|

### <a name="protectedinstance"></a>ProtectedInstance

Ez a táblázat a védett példányok alapszintű kapcsolódó mezők.

| Mező | Adattípus |Verzió érvényes | Leírás |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Text |v2|Egyedi azonosítója a biztonságimásolat-elem azonosítja a virtuális gépek biztonsági mentése a DPM, a MABS használatával|
| ProtectedContainerUniqueId_s |Text |v2|Mindent, csak a virtuális gépek számára a védett tároló azonosítására használt egyedi azonosító biztonsági mentése a DPM, a MABS használatával|
| ProtectedInstanceCount_s |Text |v2|Száma a védett példányok a társított biztonsági mentési elem vagy védett tároló az adott dátum-idő|

### <a name="recoverypoint"></a>RecoveryPoint

Ez a táblázat egyszerű helyreállítási pont kapcsolódó mezőket.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| BackupItemUniqueId_s |Text |Egyedi azonosítója a biztonságimásolat-elem azonosítja a virtuális gépek biztonsági mentése a DPM, a MABS használatával|
| OldestRecoveryPointTime_s |Text |A biztonsági mentési elem a legrégebbi helyreállítási pont időpontja|
| OldestRecoveryPointLocation_s |Text |A biztonsági mentési elem a legrégebbi helyreállítási pont helyét|
| LatestRecoveryPointTime_s |Text |A biztonsági mentési elem a legutóbbi helyreállítási pont időpontja|
| LatestRecoveryPointLocation_s |Text |A legutóbbi helyreállítási pontot a biztonsági mentési elem helye|

## <a name="next-steps"></a>További lépések

Megkezdése után tekintse át az adatmodellbe, [egyéni lekérdezések létrehozása](../azure-monitor/learn/tutorial-logs-dashboards.md) az Azure Monitor naplóira hozhat létre saját irányítópultját.
