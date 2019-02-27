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
ms.openlocfilehash: 00bdc5ff63e78b0f96b794ca907bc28158e62c62
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56883640"
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
| AlertUniqueId_s |Szöveg |A generált riasztások egyedi azonosítója |
| AlertType_s |Szöveg |Riasztás, ha például biztonsági mentés típusa |
| AlertStatus_s |Szöveg |A riasztás állapotának, például aktív |
| AlertOccurrenceDateTime_s |Dátum/idő |Dátum és idő, a riasztás létrehozásakor |
| AlertSeverity_s |Szöveg |A riasztás súlyosságát, például kritikus |
|AlertTimeToResolveInMinutes_s    | Szám        |Riasztás megoldásához szükséges idő. Aktív riasztások esetén üres.         |
|AlertConsolidationStatus_s   |Szöveg         |Ha a riasztás egy összevont riasztás vagy nem azonosítása         |
|CountOfAlertsConsolidated_s     |Szám         |Ha összevont riasztás összevont riasztások száma          |
|AlertRaisedOn_s     |Szöveg         |A riasztás akkor jön létre az entitás típusa         |
|AlertCode_s     |Szöveg         |A kód egy riasztási típus egyedi azonosításához         |
|RecommendedAction_s   |Szöveg         |A riasztás feloldásához jelenítse javasolt művelet         |
| EventName_s |Szöveg |Az esemény neve. Always AzureBackupCentralReport |
| BackupItemUniqueId_s |Szöveg |A riasztáshoz tartozó biztonsági mentési elem egyedi azonosítója |
| SchemaVersion_s |Szöveg |A séma, például jelenlegi verziója **V2** |
| State_s |Szöveg |A riasztási, például az aktív, a törölt objektum jelenlegi állapota |
| BackupManagementType_s |Szöveg |Szolgáltató típusa végrehajtásához, amelyhez ez a riasztás tartozik, a biztonsági mentés, például IaaSVM fájlmappa |
| OperationName |Szöveg |Az aktuális művelet, például a riasztás neve |
| Kategória |Szöveg |Diagnosztikai adatok az Azure Monitor naplóira leküldve kategóriáját. Always AzureBackupReport |
| Erőforrás |Szöveg |Ez az erőforrás, amelynek adatokat gyűjteni, azt mutatja, hogy a helyreállítási tár neve |
| ProtectedServerUniqueId_s |Szöveg |A védett kiszolgálón, a riasztáshoz tartozó egyedi azonosítója |
| VaultUniqueId_s |Szöveg |A védett tárolóhoz a riasztáshoz tartozó egyedi azonosítója |
| SourceSystem |Szöveg |A forrásrendszerben az aktuális adatok – Azure |
| ResourceId |Szöveg |Arról, hogy mely adatok gyűjtése az erőforrás egyedi azonosítója. Ha például egy Recovery Services vault erőforrás-azonosító |
| SubscriptionId |Szöveg |Az erőforrás (például az előfizetés-azonosító Recovery Services-tároló), amelynek az adatgyűjtés történik |
| ResourceGroup |Szöveg |Az erőforrás (például erőforráscsoport Recovery Services-tároló), amelynek az adatgyűjtés történik |
| ResourceProvider |Szöveg |Erőforrás-szolgáltató, amelynek az adatgyűjtés történik. Ha például a Microsoft.recoveryservices szolgáltatónál |
| ResourceType |Szöveg |Erőforrás típusa, amelynek az adatgyűjtés történik. Ha például a tárolók |

### <a name="backupitem"></a>BackupItem

Ez a táblázat részletesen a biztonsági mentési elem kapcsolatos mezőket.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| EventName_s |Szöveg |Az esemény neve. Always AzureBackupCentralReport |  
| BackupItemUniqueId_s |Szöveg |A biztonsági mentési elem egyedi azonosítója |
| BackupItemId_s |Szöveg |Biztonsági mentési elem azonosítója |
| BackupItemName_s |Szöveg |Biztonsági másolati elem nevét |
| BackupItemFriendlyName_s |Szöveg |Biztonságimásolat-elem rövid neve |
| BackupItemType_s |Szöveg |Biztonsági mentési elem, például a virtuális gép, fájlmappa típusa |
| ProtectionState_s |Szöveg |A biztonsági másolati elem, például a védett, ProtectionStopped aktuális védelmi állapotát |
| ProtectionGroupName_s |Szöveg | A védelmi csoport biztonságimásolat-elem nevét védett, SC DPM és a MABS, ha van ilyen|
| SecondaryBackupProtectionState_s |Szöveg |Másodlagos védelem engedélyezve van-e a biztonsági mentési elem|
| SchemaVersion_s |Szöveg |Ha például a séma verziója **V2** |
| State_s |Szöveg |A biztonsági másolati elem objektum, például az aktív, a törölt állapota |
| BackupManagementType_s |Szöveg |Szolgáltató típusát, amelyre a biztonsági mentési cikkhez tartozó biztonsági mentési, például IaaSVM fájlmappa végrehajtásához |
| OperationName |Szöveg |A művelet, például BackupItem neve |
| Kategória |Szöveg |Diagnosztikai adatok az Azure Monitor naplóira leküldve kategóriáját. Always AzureBackupReport |
| Erőforrás |Szöveg |Összegyűjtött adatok erőforrás, például a helyreállítási tár neve |
| SourceSystem |Szöveg |A forrásrendszerben az aktuális adatok – Azure |
| ResourceId |Szöveg |Erőforrás-azonosító a begyűjtött adatok, például Recovery Services-tároló az erőforrás-azonosító |
| SubscriptionId |Szöveg |Az erőforrás előfizetés-azonosító (az ex. Recovery Services-tároló) gyűjtött adatok |
| ResourceGroup |Szöveg |Az erőforrás erőforráscsoportot (az ex. Recovery Services-tároló) gyűjtött adatok |
| ResourceProvider |Szöveg |Erőforrás-szolgáltató adatokat gyűjtenek, például a Microsoft.recoveryservices szolgáltatónál |
| ResourceType |Szöveg |Az erőforrás számára gyűjtött adatok típusától, írja be például, tárolók |

### <a name="backupitemassociation"></a>BackupItemAssociation

Ez a táblázat a különböző entitások biztonsági másolati elem társítások részletesen ismerteti.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| EventName_s |Szöveg |Ez a mező képviseli az esemény nevét, a rendszer mindig AzureBackupCentralReport |  
| BackupItemUniqueId_s |Szöveg |A biztonsági másolati elem egyedi azonosítója |
| SchemaVersion_s |Szöveg |Ebben a mezőben azt jelzi, hogy a jelenlegi verzióját, a sémát, hogy az **V2** |
| State_s |Szöveg |A biztonsági másolati elem társítás objektum, például az aktív, a törölt aktuális állapotát |
| BackupManagementType_s |Szöveg |Ennek során a biztonsági mentési feladat, például IaaSVM, fájlmappa kiszolgáló a szolgáltató típusát |
| BackupItemSourceSize_s |Szöveg | A biztonsági másolati elem előtérmérete |
| BackupManagementServerUniqueId_s |Szöveg | A biztonsági mentés felügyeleti kiszolgáló a biztonsági mentési elemek egyedi azonosításához mező védett keresztül, ha van ilyen |
| Kategória |Szöveg |Ez a mező képviseli a diagnosztikai adatok a Log Analytics szolgáltatásba leküldött kategória, AzureBackupReport |
| OperationName |Szöveg |Ez a mező képviseli az aktuális művelet – BackupItemAssociation neve |
| Erőforrás |Szöveg |Ez az erőforrás, amelynek adatokat gyűjteni, azt mutatja, hogy a helyreállítási tár neve |
| PolicyUniqueId_g |Szöveg |A szabályzathoz társított a biztonságimásolat-elem egyedi azonosítója |
| ProtectedServerUniqueId_s |Szöveg |A védett kiszolgálón, a biztonsági mentési elemhez társított egyedi azonosítója |
| VaultUniqueId_s |Szöveg |A tárolóban, amely tartalmazza a biztonsági másolati elem egyedi azonosítója |
| SourceSystem |Szöveg |A forrásrendszerben az aktuális adatok – Azure |
| ResourceId |Szöveg |A begyűjtött adatok erőforrás-azonosítója. Ha például a helyreállítási tár az erőforrás-azonosító |
| SubscriptionId |Szöveg |Az erőforrás előfizetés-azonosító (az ex. Recovery Services-tároló), amelynek összegyűjtött adatok |
| ResourceGroup |Szöveg |Az erőforrás erőforráscsoportot (az ex. Recovery Services-tároló), amelynek összegyűjtött adatok |
| ResourceProvider |Szöveg |Erőforrás-szolgáltató adatokat gyűjtenek, például a Microsoft.recoveryservices szolgáltatónál |
| ResourceType |Szöveg |Az erőforrástípust a adatokat gyűjtenek, például a tárolók |

### <a name="backupmanagementserver"></a>BackupManagementServer

Ez a táblázat a különböző entitások biztonsági másolati elem társítások részletesen ismerteti.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
|BackupManagementServerName_s     |Szöveg         |A biztonságimásolat-felügyeleti kiszolgáló neve        |
|AzureBackupAgentVersion_s     |Szöveg         |A biztonságimásolat-felügyeleti kiszolgálón az Azure Backup-ügynök verziója          |
|BackupManagementServerVersion_s     |Szöveg         |A biztonságimásolat-felügyeleti kiszolgáló verziója|
|BackupManagementServerOSVersion_s    |Szöveg            |A biztonsági mentés felügyeleti kiszolgáló operációsrendszer-verzió|
|BackupManagementServerType_s     |Szöveg         |A biztonságimásolat-felügyeleti kiszolgáló, mint a MABS, SC DPM típusa|
|BackupManagementServerUniqueId_s     |Szöveg         |A biztonsági mentés felügyeleti kiszolgáló egyedi azonosításához mező       |
| SourceSystem |Szöveg |A forrásrendszerben az aktuális adatok – Azure |
| ResourceId |Szöveg |A begyűjtött adatok erőforrás-azonosítója. Ha például a helyreállítási tár az erőforrás-azonosító |
| SubscriptionId |Szöveg |Az erőforrás előfizetés-azonosító (az ex. Recovery Services-tároló), amelynek összegyűjtött adatok |
| ResourceGroup |Szöveg |Az erőforrás erőforráscsoportot (az ex. Recovery Services-tároló), amelynek összegyűjtött adatok |
| ResourceProvider |Szöveg |Erőforrás-szolgáltató adatokat gyűjtenek, például a Microsoft.recoveryservices szolgáltatónál |
| ResourceType |Szöveg |Az erőforrástípust a adatokat gyűjtenek, például a tárolók |

### <a name="job"></a>Feladat

Ez a táblázat részletesen a feladathoz kapcsolódó mezőket.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| EventName_s |Szöveg |Az esemény neve. Always AzureBackupCentralReport |
| BackupItemUniqueId_s |Szöveg |A biztonsági mentési elem egyedi azonosítója |
| SchemaVersion_s |Szöveg |Ha például a séma verziója **V2** |
| State_s |Szöveg |A feladatobjektumot, például az aktív, a törölt aktuális állapotát |
| BackupManagementType_s |Szöveg |Ennek során a biztonsági mentési feladat, például IaaSVM, fájlmappa kiszolgáló a szolgáltató típusát |
| OperationName |Szöveg |Ez a mező annak neve, az aktuális művelet - feladat |
| Kategória |Szöveg |Ez a mező képviseli a diagnosztikai adatok az Azure Monitor naplóira leküldve kategória, AzureBackupReport |
| Erőforrás |Szöveg |Ez az erőforrás, amelynek adatokat gyűjteni, azt mutatja, hogy a helyreállítási tár neve |
| ProtectedServerUniqueId_s |Szöveg |A védett kiszolgáló egyedi azonosítója a feladathoz hozzárendelt |
| ProtectedContainerUniqueId_s |Szöveg | A feladat futtatásakor a védett tároló azonosításához használatos egyedi azonosító |
| VaultUniqueId_s |Szöveg |A védett tároló egyedi azonosítója |
| JobOperation_s |Szöveg |A művelet, amelynek feladat futtatása például biztonsági mentés, visszaállítás, a Backup konfigurálása |
| JobStatus_s |Szöveg |A befejezett feladat, például befejezve, sikertelen állapota |
| JobFailureCode_s |Szöveg |Sikertelen hibakód karakterláncát miatt, amely feladat hiba történt |
| JobStartDateTime_s |Dátum/idő |Dátum és idő elindított futó feladat |
| BackupStorageDestination_s |Szöveg |A biztonsági mentési tár, például felhőben lemez rendeltetési  |
| AdHocOrScheduledJob_s |Szöveg | A mezőben adhatja meg, ha a feladat alkalmi vagy ütemezett |
| JobDurationInSecs_s | Szám |Feladatok teljes időtartama (másodpercben) |
| DataTransferredInMB_s | Szám |A feladat MB-ban átvitt adatok|
| JobUniqueId_g |Szöveg |A feladat azonosításához használatos egyedi azonosító |
| RecoveryJobDestination_s |Szöveg | Ahol a állítja helyre a rendszer egy helyreállítási feladat cél |
| RecoveryJobRPDateTime_s |DateTime | A dátum, a helyreállítási pontot, amelyik a helyreállítandó létrehozásának ideje |
| RecoveryJobRPLocation_s |Szöveg | A helyreállítási pontot, amelyik a helyreállítandó tárolásának helyét|
| SourceSystem |Szöveg |A forrásrendszerben az aktuális adatok – Azure |
| ResourceId |Szöveg |A begyűjtött adatok erőforrás-azonosítója. Ha például a helyreállítási tár az erőforrás-azonosító|
| SubscriptionId |Szöveg |Az erőforrás (például az előfizetés-azonosító Recovery Services-tároló), amelynek az adatgyűjtés történik |
| ResourceGroup |Szöveg |Az erőforrás (például erőforráscsoport Recovery Services-tároló), amelynek az adatgyűjtés történik |
| ResourceProvider |Szöveg |Erőforrás-szolgáltató, amelynek az adatgyűjtés történik. Ha például a Microsoft.recoveryservices szolgáltatónál |
| ResourceType |Szöveg |Erőforrás típusa, amelynek az adatgyűjtés történik. Ha például a tárolók |

### <a name="policy"></a>Szabályzat

Ez a táblázat részletesen házirendekhez kapcsolódó mezőket.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| EventName_s |Szöveg |Ez a mező képviseli az esemény nevét, a rendszer mindig AzureBackupCentralReport |
| SchemaVersion_s |Szöveg |Ebben a mezőben azt jelzi, hogy a jelenlegi verzióját, a sémát, hogy az **V2** |
| State_s |Szöveg |A csoportházirend-objektum, például az aktív, a törölt aktuális állapotát |
| BackupManagementType_s |Szöveg |Ennek során a biztonsági mentési feladat, például IaaSVM, fájlmappa kiszolgáló a szolgáltató típusát |
| OperationName |Szöveg |Ez a mező képviseli az aktuális művelet - házirend neve |
| Kategória |Szöveg |Ez a mező képviseli a diagnosztikai adatok az Azure Monitor naplóira leküldve kategória, AzureBackupReport |
| Erőforrás |Szöveg |Ez az erőforrás, amelynek adatokat gyűjteni, azt mutatja, hogy a helyreállítási tár neve |
| PolicyUniqueId_g |Szöveg |Azonosíthatja a szabályzat egyedi azonosítója |
| PolicyName_s |Szöveg |A megadott házirend neve |
| BackupFrequency_s |Szöveg |Amellyel biztonsági mentések futnak, például, napi, heti gyakoriság |
| BackupTimes_s |Szöveg |Dátum és idő, amikor a biztonsági mentés van ütemezve |
| BackupDaysOfTheWeek_s |Szöveg |Ha biztonsági mentések ütemezett napjai |
| RetentionDuration_s |Egész szám |A konfigurált biztonsági mentés megőrzési időtartama |
| DailyRetentionDuration_s |Egész szám |Teljes megőrzés időtartama napokban megadva a konfigurált biztonsági mentés |
| DailyRetentionTimes_s |Szöveg |Dátum és idő, amikor napi megőrzés konfigurálása |
| WeeklyRetentionDuration_s |Tizedes tört szám |Teljes hetes, a konfigurált biztonsági mentés heti megőrzési időtartama |
| WeeklyRetentionTimes_s |Szöveg |Dátum és idő, ha a heti megőrzési van konfigurálva |
| WeeklyRetentionDaysOfTheWeek_s |Szöveg |Heti megőrzési kijelölve a hét napjai |
| MonthlyRetentionDuration_s |Tizedes tört szám |Hónap konfigurált biztonsági mentés a teljes megőrzési időtartama |
| MonthlyRetentionTimes_s |Szöveg |Dátum és idő, ha a havi megőrzési van konfigurálva |
| MonthlyRetentionFormat_s |Szöveg |A havi megőrzési, például a napi szintű, hetente alapú hét naponta konfigurációtípust |
| MonthlyRetentionDaysOfTheWeek_s |Szöveg |Havi megőrzési kijelölve a hét napjai |
| MonthlyRetentionWeeksOfTheMonth_s |Szöveg |Hét, a hónap, ha a havi megőrzési van konfigurálva, például First, Last stb. |
| YearlyRetentionDuration_s |Tizedes tört szám |Teljes megőrzési időtartam a konfigurált biztonsági mentés éves |
| YearlyRetentionTimes_s |Szöveg |Dátum és idő, amikor éves megőrzési van konfigurálva |
| YearlyRetentionMonthsOfTheYear_s |Szöveg |Az év hónapjai éves megőrzési kiválasztva |
| YearlyRetentionFormat_s |Szöveg |Az éves megőrzési, például a napi szintű, hetente alapú hét naponta konfigurációtípust |
| YearlyRetentionDaysOfTheMonth_s |Szöveg |A hónap, éves megőrzési kijelölt dátumok |
| SynchronisationFrequencyPerDay_s |Egész szám |v2|A biztonsági mentés szinkronizálása SC DPM és a MABS nap száma |
| DiffBackupFormat_s |Szöveg |v2|Különbségi biztonsági másolatok formátuma az SQL Azure virtuális gép biztonsági mentése |
| DiffBackupTime_s |Time |v2|Az SQL-lel az Azure VM Backup különbözeti biztonsági mentések idejét|
| DiffBackupRetentionDuration_s |Tizedes tört szám |v2|Az SQL-lel az Azure VM Backup különbségi biztonsági másolatok megőrzési időtartama|
| LogBackupFrequency_s |Tizedes tört szám |v2|Az SQL a Naplóalapú biztonsági mentések gyakoriságát|
| LogBackupRetentionDuration_s |Tizedes tört szám |v2|Az SQL-lel az Azure virtuális gépek biztonsági mentését a Naplóalapú biztonsági mentések megőrzési időtartama|
| DiffBackupDaysofTheWeek_s |Szöveg |v2|A használt SQL-lel az Azure VM Backup különbségi biztonsági másolatok hét napjai|
| SourceSystem |Szöveg |A forrásrendszerben az aktuális adatok – Azure |
| ResourceId |Szöveg |A begyűjtött adatok erőforrás-azonosítója. Ha például a helyreállítási tár az erőforrás-azonosító |
| SubscriptionId |Szöveg |Az erőforrás (például az előfizetés-azonosító Recovery Services-tároló), amelynek az adatgyűjtés történik |
| ResourceGroup |Szöveg |Az erőforrás (például erőforráscsoport Recovery Services-tároló), amelynek az adatgyűjtés történik |
| ResourceProvider |Szöveg |Erőforrás-szolgáltató, amelynek az adatgyűjtés történik. Ha például a Microsoft.recoveryservices szolgáltatónál |
| ResourceType |Szöveg |Erőforrás típusa, amelynek az adatgyűjtés történik. Ha például a tárolók |

### <a name="policyassociation"></a>PolicyAssociation

Ez a táblázat a különböző entitások szabályzattársításait részletesen ismerteti.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| EventName_s |Szöveg |Ez a mező képviseli az esemény nevét, a rendszer mindig AzureBackupCentralReport |
| SchemaVersion_s |Szöveg |Ebben a mezőben azt jelzi, hogy a jelenlegi verzióját, a sémát, hogy az **V2** |
| State_s |Szöveg |A csoportházirend-objektum, például az aktív, a törölt aktuális állapotát |
| BackupManagementType_s |Szöveg |Ennek során a biztonsági mentési feladat, például IaaSVM, fájlmappa kiszolgáló a szolgáltató típusát |
| OperationName |Szöveg |Ez a mező képviseli az aktuális művelet – PolicyAssociation neve |
| Kategória |Szöveg |Ez a mező képviseli a diagnosztikai adatok az Azure Monitor naplóira leküldve kategória, AzureBackupReport |
| Erőforrás |Szöveg |Ez az erőforrás, amelynek adatokat gyűjteni, azt mutatja, hogy a helyreállítási tár neve |
| PolicyUniqueId_g |Szöveg |Azonosíthatja a szabályzat egyedi azonosítója |
| VaultUniqueId_s |Szöveg |A tárolóban, amelyhez ez a szabályzat tartozik, az egyedi azonosítója |
| BackupManagementServerUniqueId_s |Szöveg |v2 |A biztonsági mentés felügyeleti kiszolgáló a biztonsági mentési elemek egyedi azonosításához mező védett keresztül, ha van ilyen        |
| SourceSystem |Szöveg |A forrásrendszerben az aktuális adatok – Azure |
| ResourceId |Szöveg |A begyűjtött adatok erőforrás-azonosítója. Ha például a helyreállítási tár az erőforrás-azonosító |
| SubscriptionId |Szöveg |Az erőforrás (például az előfizetés-azonosító Recovery Services-tároló), amelynek az adatgyűjtés történik |
| ResourceGroup |Szöveg |Az erőforrás (például erőforráscsoport Recovery Services-tároló), amelynek az adatgyűjtés történik |
| ResourceProvider |Szöveg |Erőforrás-szolgáltató, amelynek az adatgyűjtés történik. Ha például a Microsoft.recoveryservices szolgáltatónál |
| ResourceType |Szöveg |Erőforrás típusa, amelynek az adatgyűjtés történik. Ha például a tárolók |

### <a name="protected-container"></a>Védett tároló

Ez a táblázat védett tárolók kapcsolatos alapvető mezőket is tartalmazza. (A ProtectedServer volt a v1-ben)

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Szöveg | A mező egy védett tároló egyedi azonosításához |
| ProtectedContainerOSType_s |Szöveg |A védett tároló operációs rendszer típusa |
| ProtectedContainerOSVersion_s |Szöveg |A védett tároló operációsrendszer-verzió |
| AgentVersion_s |Szöveg |Az ügynök biztonsági mentési vagy a védelmi ügynök (esetén SC DPM és a MABS) verziószáma |
| BackupManagementType_s |Szöveg |A biztonsági mentés például IaaSVM fájlmappa szolgáltató típusa |
| EntityState_s |Szöveg |Aktív, a törölt például a védett kiszolgáló objektum aktuális állapotát |
| ProtectedContainerFriendlyName_s |Szöveg |Védett kiszolgáló rövid neve |
| ProtectedContainerName_s |Szöveg |A védett tároló nevét. |
| ProtectedContainerWorkloadType_s |Szöveg |A védett tároló típusú biztonsági mentés például IaaSVMContainer |
| ProtectedContainerLocation_s |Szöveg |Hogy a védett tároló a helyszínen vagy az Azure-ban |
| ProtectedContainerType_s |Szöveg |A védett tároló-e a kiszolgálót, vagy egy tároló |

### <a name="storage"></a>Storage

A table storage szolgáltatással kapcsolatos mezők részletesen ismerteti.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| CloudStorageInBytes_s |Tizedes tört szám |Felhőalapú biztonsági mentési tár biztonsági mentések, számított által használt legújabb érték alapján |
| ProtectedInstances_s |Tizedes tört szám |Előtérbeli tár számlázási, számított alapján legújabb érték kiszámításához használt védett példányok száma |
| EventName_s |Szöveg |Ez a mező képviseli az esemény nevét, a rendszer mindig AzureBackupCentralReport |
| SchemaVersion_s |Szöveg |Ebben a mezőben azt jelzi, hogy a jelenlegi verzióját, a sémát, hogy az **V2** |
| State_s |Szöveg |A tárolási objektum, például az aktív, a törölt aktuális állapotát |
| BackupManagementType_s |Szöveg |Ennek során a biztonsági mentési feladat, például IaaSVM, fájlmappa kiszolgáló a szolgáltató típusát |
| OperationName |Szöveg |Ez a mező képviseli az aktuális művelet – a tároló neve |
| Kategória |Szöveg |Ez a mező képviseli a diagnosztikai adatok az Azure Monitor naplóira leküldve kategória, AzureBackupReport |
| Erőforrás |Szöveg |Ez az erőforrás, amelynek adatokat gyűjteni, azt mutatja, hogy a helyreállítási tár neve |
| ProtectedServerUniqueId_s |Szöveg |A védett kiszolgálón, amelyhez tárolási számította egyedi azonosítója |
| VaultUniqueId_s |Szöveg |Egyedi azonosítója a Storage-tároló kiszámítása |
| SourceSystem |Szöveg |A forrásrendszerben az aktuális adatok – Azure |
| ResourceId |Szöveg |A begyűjtött adatok erőforrás-azonosítója. Ha például a helyreállítási tár az erőforrás-azonosító |
| SubscriptionId |Szöveg |Az erőforrás (például az előfizetés-azonosító Recovery Services-tároló), amelynek az adatgyűjtés történik |
| ResourceGroup |Szöveg |Az erőforrás (például erőforráscsoport Recovery Services-tároló), amelynek az adatgyűjtés történik |
| ResourceProvider |Szöveg |Erőforrás-szolgáltató, amelynek az adatgyűjtés történik. Ha például a Microsoft.recoveryservices szolgáltatónál |
| ResourceType |Szöveg |Erőforrás típusa, amelynek az adatgyűjtés történik. Ha például a tárolók |

### <a name="storageassociation"></a>StorageAssociation

Ez a táblázat tárolási csatlakozik más entitások alapszintű storage szolgáltatással kapcsolatos mezőket is tartalmazza.

| Mező | Adattípus | Leírás |
| --- | --- |  --- |
| StorageUniqueId_s |Szöveg |A storage entitás azonosítására használt egyedi azonosító |
| SchemaVersion_s |Szöveg |Ebben a mezőben azt jelzi, hogy a jelenlegi verzióját, a sémát, hogy az **V2** |
| BackupItemUniqueId_s |Szöveg |A biztonsági másolati elem a tároló entitáshoz kapcsolódó azonosítására használt egyedi azonosító |
| BackupManagementServerUniqueId_s |Szöveg |A biztonságimásolat-felügyeleti kiszolgáló, a storage entitáshoz kapcsolódó azonosítására használt egyedi azonosító|
| VaultUniqueId_s |Szöveg |A tárolóban, a storage entitáshoz kapcsolódó azonosítására használt egyedi azonosító|
| StorageConsumedInMBs_s |Szám|A megfelelő biztonsági mentési elemet a megfelelő tárolási által felhasznált tárterület mérete |
| StorageAllocatedInMBs_s |Szám |A megfelelő biztonsági mentési elemet a megfelelő tárolási típusú lemez által lefoglalt tárhely mérete|

### <a name="vault"></a>Tároló

Ez a táblázat részletesen tároló kapcsolatos mezőket.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| EventName_s |Szöveg |Ez a mező képviseli az esemény nevét, a rendszer mindig AzureBackupCentralReport |
| SchemaVersion_s |Szöveg |Ebben a mezőben azt jelzi, hogy a jelenlegi verzióját, a sémát, hogy az **V2** |
| State_s |Szöveg |A tár objektum, például az aktív, a törölt aktuális állapotát |
| OperationName |Szöveg |Ez a mező képviseli az aktuális művelet - tár neve |
| Kategória |Szöveg |Ez a mező képviseli a diagnosztikai adatok az Azure Monitor naplóira leküldve kategória, AzureBackupReport |
| Erőforrás |Szöveg |Ez az erőforrás, amelynek adatokat gyűjteni, azt mutatja, hogy a helyreállítási tár neve |
| VaultUniqueId_s |Szöveg |A tároló egyedi azonosítója |
| VaultName_s |Szöveg |A tároló neve |
| AzureDataCenter_s |Szöveg |Adatközpont, ahol a tároló megtalálható |
| StorageReplicationType_s |Szöveg |A tárolóhoz, például GeoRedundant tárolóreplikáció típusa |
| SourceSystem |Szöveg |A forrásrendszerben az aktuális adatok – Azure |
| ResourceId |Szöveg |A begyűjtött adatok erőforrás-azonosítója. Ha például a helyreállítási tár az erőforrás-azonosító |
| SubscriptionId |Szöveg |Az erőforrás (például az előfizetés-azonosító Recovery Services-tároló), amelynek az adatgyűjtés történik |
| ResourceGroup |Szöveg |Az erőforrás (például erőforráscsoport Recovery Services-tároló), amelynek az adatgyűjtés történik |
| ResourceProvider |Szöveg |Erőforrás-szolgáltató, amelynek az adatgyűjtés történik. Ha például a Microsoft.recoveryservices szolgáltatónál |
| ResourceType |Szöveg |Erőforrás típusa, amelynek az adatgyűjtés történik. Ha például a tárolók |

### <a name="backup-management-server"></a>Biztonságimásolat-felügyeleti kiszolgáló

Ez a táblázat biztonságimásolat-felügyeleti kiszolgálók kapcsolatos alapvető mezőket is tartalmazza.

|Mező  |Adattípus  | Leírás  |
|---------|---------|----------|
|BackupManagmentServerName_s     |Szöveg         |A biztonságimásolat-felügyeleti kiszolgáló neve        |
|AzureBackupAgentVersion_s     |Szöveg         |A biztonságimásolat-felügyeleti kiszolgálón az Azure Backup-ügynök verziója          |
|BackupManagmentServerVersion_s     |Szöveg         |A biztonságimásolat-felügyeleti kiszolgáló verziója|
|BackupManagmentServerOSVersion_s     |Szöveg            |A biztonsági mentés felügyeleti kiszolgáló operációsrendszer-verzió|
|BackupManagementServerType_s     |Szöveg         |A biztonságimásolat-felügyeleti kiszolgáló, mint a MABS, SC DPM típusa|
|BackupManagmentServerUniqueId_s     |Szöveg         |A biztonsági mentés felügyeleti kiszolgáló egyedi azonosításához mező       |

### <a name="preferredworkloadonvolume"></a>PreferredWorkloadOnVolume

Ez a táblázat megadja a workload(s) kötet társítva.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| StorageUniqueId_s |Szöveg |A storage entitás azonosítására használt egyedi azonosító |
| BackupItemType_s |Szöveg |A számítási feladatokat, amelyek a kötet esetében az előnyben részesített tárhelyet|

### <a name="protectedinstance"></a>ProtectedInstance

Ez a táblázat a védett példányok alapszintű kapcsolódó mezők.

| Mező | Adattípus |Verzió érvényes | Leírás |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Szöveg |v2|Egyedi azonosítója a biztonságimásolat-elem azonosítja a virtuális gépek biztonsági mentése a DPM, a MABS használatával|
| ProtectedContainerUniqueId_s |Szöveg |v2|Mindent, csak a virtuális gépek számára a védett tároló azonosítására használt egyedi azonosító biztonsági mentése a DPM, a MABS használatával|
| ProtectedInstanceCount_s |Szöveg |v2|Száma a védett példányok a társított biztonsági mentési elem vagy védett tároló az adott dátum-idő|

### <a name="recoverypoint"></a>RecoveryPoint

Ez a táblázat egyszerű helyreállítási pont kapcsolódó mezőket.

| Mező | Adattípus | Leírás |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Szöveg |Egyedi azonosítója a biztonságimásolat-elem azonosítja a virtuális gépek biztonsági mentése a DPM, a MABS használatával|
| OldestRecoveryPointTime_s |Szöveg |A biztonsági mentési elem a legrégebbi helyreállítási pont időpontja|
| OldestRecoveryPointLocation_s |Szöveg |A biztonsági mentési elem a legrégebbi helyreállítási pont helyét|
| LatestRecoveryPointTime_s |Szöveg |A biztonsági mentési elem a legutóbbi helyreállítási pont időpontja|
| LatestRecoveryPointLocation_s |Szöveg |A legutóbbi helyreállítási pontot a biztonsági mentési elem helye|

## <a name="next-steps"></a>További lépések

Megkezdése után tekintse át az adatmodellbe, [egyéni lekérdezések létrehozása](../azure-monitor/learn/tutorial-logs-dashboards.md) az Azure Monitor naplóira hozhat létre saját irányítópultját.
