---
title: A Azure Backup adatmodelljét Azure Monitor naplózza
description: Ez a cikk a Azure Backup adatok adatmodell-adatainak Azure Monitor naplóit tárgyalja.
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: adigan
ms.openlocfilehash: 6563eefffee0ed8d9ce94c3e0a1e24b0d32314f0
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466157"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Log Analytics adatmodell Azure Backup-adattípushoz

A Log Analytics adatmodell használatával egyéni riasztásokat hozhat létre a Log Analyticsból.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="using-azure-backup-data-model"></a>Azure Backup adatmodell használata

A következő mezőket használhatja az adatmodell részeként vizualizációk, egyéni lekérdezések és irányítópultok létrehozásához a követelmények szerint.

### <a name="alert"></a>Riasztás

Ez a táblázat a riasztással kapcsolatos mezők részleteit tartalmazza.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| AlertUniqueId_s |Text |A generált riasztás egyedi azonosítója |
| AlertType_s |Text |A riasztás típusa, például biztonsági mentés |
| AlertStatus_s |Text |A riasztás állapota, például aktív |
| AlertOccurrenceDateTime_s |Date/Time |A riasztás létrehozásának dátuma és időpontja |
| AlertSeverity_s |Text |A riasztás súlyossága, például kritikus |
|AlertTimeToResolveInMinutes_s    | Number        |A riasztás feloldásához szükséges idő. Üres az aktív riasztásokhoz.         |
|AlertConsolidationStatus_s   |Text         |Annak megállapítása, hogy a riasztás konszolidált riasztás-e, vagy sem         |
|CountOfAlertsConsolidated_s     |Number         |Összevont riasztások száma konszolidált riasztás esetén          |
|AlertRaisedOn_s     |Text         |A riasztást kiváltó entitás típusa         |
|AlertCode_s     |Text         |Riasztás típusának egyedi azonosítására szolgáló kód         |
|RecommendedAction_s   |Text         |A riasztás feloldásához javasolt művelet         |
| EventName_s |Text |Az esemény neve. Always AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |A riasztáshoz társított biztonsági mentési tétel egyedi azonosítója |
| SchemaVersion_s |Text |A séma jelenlegi verziója, például **v2** |
| State_s |Text |A riasztási objektum aktuális állapota, például aktív, törölve |
| BackupManagementType_s |Text |Szolgáltatói típus a biztonsági mentés végrehajtásához, például IaaSVM, fájlmappa, amelyhez ez a riasztás tartozik |
| OperationName |Text |Az aktuális művelet neve, például riasztás |
| Category |Text |A Azure Monitor naplókba leküldett diagnosztikai adatkategória. Always AzureBackupReport |
| Resource |Text |Ez az az erőforrás, amelybe begyűjti az adatokat, Recovery Services tár nevét jeleníti meg |
| ProtectedContainerUniqueId_s |Text |A riasztáshoz társított védett kiszolgáló egyedi azonosítója (ProtectedServerUniqueId_s a v1-ben)|
| VaultUniqueId_s |Text |A riasztáshoz társított védett tároló egyedi azonosítója |
| SourceSystem |Text |Az aktuális adatforrásrendszer – Azure |
| ResourceId |Text |Annak az erőforrásnak az egyedi azonosítója, amelyről az adatokat gyűjti. Például egy Recovery Services tár erőforrás-azonosítója |
| SubscriptionId |Text |Az erőforrás előfizetés-azonosítója (pl. Recovery Services-tároló), amelybe az adatok gyűjtése történik |
| Erőforráscsoport |Text |Erőforrás erőforráscsoport (pl.: Recovery Services-tároló), amelybe az adatok gyűjtése történik |
| ResourceProvider |Text |Erőforrás-szolgáltató, amelybe az adatok gyűjtése történik. Például: Microsoft. Recoveryservices szolgáltatónál |
| Erőforrástípus |Text |Az az erőforrástípus, amelybe az adatok gyűjtése történik. Például: tárolók |

### <a name="backupitem"></a>BackupItem

Ez a táblázat a biztonsági másolati elemek kapcsolódó mezőinek részleteit tartalmazza.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| EventName_s |Text |Az esemény neve. Always AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |A biztonsági másolati tétel egyedi azonosítója |
| BackupItemId_s |Text |A biztonsági másolati elemek azonosítója (ez a mező csak a v1-séma esetében) |
| BackupItemName_s |Text |A biztonságimásolat-tétel neve |
| BackupItemFriendlyName_s |Text |A biztonságimásolat-tétel rövid neve |
| BackupItemType_s |Text |A biztonsági mentési elem típusa, például virtuális gép, fájlmappa |
| BackupItemProtectionState_s |Text |A biztonsági mentési elem védelmi állapota |
| BackupItemAppVersion_s |Text |A biztonsági mentési tétel alkalmazásának verziója |
| ProtectionState_s |Text |A biztonsági másolati elem jelenlegi védelmi állapota, például védett, ProtectionStopped |
| ProtectionGroupName_s |Text | A védelmi csoport neve, amelyben a biztonsági másolati elem védett, az SC DPM és a MABS esetében, ha van ilyen.|
| SecondaryBackupProtectionState_s |Text |Azt jelzi, hogy engedélyezve van-e a másodlagos védelem a biztonsági mentési elemmel kapcsolatban|
| SchemaVersion_s |Text |A séma verziója, például **v2** |
| State_s |Text |A biztonsági mentési elem objektumának állapota, például aktív, törölve |
| BackupManagementType_s |Text |Szolgáltató típusa a biztonsági mentés végrehajtásához, például IaaSVM, fájlmappa, amelyhez ez a biztonsági mentési elem tartozik |
| OperationName |Text |A művelet neve, például BackupItem |
| Category |Text |A Azure Monitor naplókba leküldett diagnosztikai adatkategória. Always AzureBackupReport |
| Resource |Text |Az az erőforrás, amelybe az adatokat gyűjti, például Recovery Services tár neve |
| SourceSystem |Text |Az aktuális adatforrásrendszer – Azure |
| ResourceId |Text |Az összegyűjtött adatok erőforrás-azonosítója, például Recovery Services tár erőforrás-azonosítója |
| SubscriptionId |Text |Az erőforrás előfizetés-azonosítója (pl.:. Recovery Services-tároló) a gyűjtött adatokhoz |
| Erőforráscsoport |Text |Az erőforrás erőforráscsoport (pl.: Recovery Services-tároló) a gyűjtött adatokhoz |
| ResourceProvider |Text |Erőforrás-szolgáltató az összegyűjtött adatokhoz, például: Microsoft. Recoveryservices szolgáltatónál |
| Erőforrástípus |Text |A gyűjtött adatokhoz tartozó erőforrás típusa, például tárolók |

### <a name="backupitemassociation"></a>BackupItemAssociation

Ez a táblázat a különböző entitásokkal rendelkező biztonsági mentési elemek társításával kapcsolatos részleteket tartalmazza.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| EventName_s |Text |Ez a mező az esemény nevét jelöli, mindig AzureBackupCentralReport |  
| BackupItemUniqueId_s |Text |A biztonsági másolati tétel egyedi azonosítója |
| SchemaVersion_s |Text |Ez a mező a séma aktuális verzióját jelöli, **v2** |
| State_s |Text |A biztonsági mentési elem társítási objektumának aktuális állapota, például aktív, törölve |
| BackupManagementType_s |Text |Szolgáltató típusa a biztonsági mentési feladatot végző kiszolgáló számára, például IaaSVM, fájlmappa |
| BackupItemSourceSize_s |Text | A biztonsági mentési tétel előtér-mérete |
| BackupManagementServerUniqueId_s |Text | A biztonságimásolat-felügyeleti kiszolgáló egyedi azonosítására szolgáló mező, ha van ilyen, a biztonsági mentési elem védelme |
| Category |Text |Ez a mező a Log Analytics leküldett diagnosztikai adatkategóriát jelöli, AzureBackupReport |
| OperationName |Text |Ez a mező az aktuális művelet nevét jelöli – BackupItemAssociation |
| Resource |Text |Ez az az erőforrás, amelybe begyűjti az adatokat, Recovery Services tár nevét jeleníti meg |
| ProtectedContainerUniqueId_s |Text |A biztonsági mentési elemmel társított védett kiszolgáló egyedi azonosítója (v1-ben ProtectedServerUniqueId_s) |
| VaultUniqueId_s |Text |A biztonsági mentési tételt tartalmazó tároló egyedi azonosítója |
| SourceSystem |Text |Az aktuális adatforrásrendszer – Azure |
| ResourceId |Text |Az összegyűjtött adatok erőforrás-azonosítója. Például Recovery Services tár erőforrás-azonosítója |
| SubscriptionId |Text |Az erőforrás előfizetés-azonosítója (pl.:. Recovery Services-tároló), amelybe az adatok gyűjtése történik |
| Erőforráscsoport |Text |Az erőforrás erőforráscsoport (pl.: Recovery Services-tároló), amelybe az adatok gyűjtése történik |
| ResourceProvider |Text |Erőforrás-szolgáltató az összegyűjtött adatokhoz, például: Microsoft. Recoveryservices szolgáltatónál |
| Erőforrástípus |Text |Az adatok gyűjtésére szolgáló erőforrás típusa, például tárolók |

### <a name="backupmanagementserver"></a>BackupManagementServer

Ez a táblázat a különböző entitásokkal rendelkező biztonsági mentési elemek társításával kapcsolatos részleteket tartalmazza.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
|BackupManagementServerName_s     |Text         |A biztonságimásolat-felügyeleti kiszolgáló neve        |
|AzureBackupAgentVersion_s     |Text         |A Azure Backup ügynök verziója a biztonságimásolat-felügyeleti kiszolgálón          |
|BackupManagementServerVersion_s     |Text         |A biztonságimásolat-felügyeleti kiszolgáló verziója|
|BackupManagementServerOSVersion_s    |Text            |A biztonságimásolat-felügyeleti kiszolgáló operációs rendszerének verziója|
|BackupManagementServerType_s     |Text         |A biztonságimásolat-felügyeleti kiszolgáló típusa, mint MABS, SC DPM|
|BackupManagementServerUniqueId_s     |Text         |A biztonságimásolat-felügyeleti kiszolgáló egyedi azonosítására szolgáló mező       |
| SourceSystem |Text |Az aktuális adatforrásrendszer – Azure |
| ResourceId |Text |Az összegyűjtött adatok erőforrás-azonosítója. Például Recovery Services tár erőforrás-azonosítója |
| SubscriptionId |Text |Az erőforrás előfizetés-azonosítója (pl.:. Recovery Services-tároló), amelybe az adatok gyűjtése történik |
| Erőforráscsoport |Text |Az erőforrás erőforráscsoport (pl.: Recovery Services-tároló), amelybe az adatok gyűjtése történik |
| ResourceProvider |Text |Erőforrás-szolgáltató az összegyűjtött adatokhoz, például: Microsoft. Recoveryservices szolgáltatónál |
| Erőforrástípus |Text |Az adatok gyűjtésére szolgáló erőforrás típusa, például tárolók |

### <a name="job"></a>Feladat

Ez a táblázat a feladatokkal kapcsolatos mezők részleteit tartalmazza.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| EventName_s |Text |Az esemény neve. Always AzureBackupCentralReport |
| BackupItemUniqueId_s |Text |A biztonsági másolati tétel egyedi azonosítója |
| SchemaVersion_s |Text |A séma verziója, például **v2** |
| State_s |Text |A feladatütemezés aktuális állapota, például aktív, törölve |
| BackupManagementType_s |Text |Szolgáltató típusa a biztonsági mentési feladatot végző kiszolgáló számára, például IaaSVM, fájlmappa |
| OperationName |Text |Ez a mező az aktuális művelet nevét jelöli – feladat |
| Category |Text |Ez a mező a Azure Monitor naplókba leküldett diagnosztikai adatkategóriákat jelöli, AzureBackupReport |
| Resource |Text |Ez az az erőforrás, amelybe begyűjti az adatokat, Recovery Services tár nevét jeleníti meg |
| ProtectedServerUniqueId_s |Text |A feladathoz társított védett kiszolgáló egyedi azonosítója |
| ProtectedContainerUniqueId_s |Text | A feladatot futtató védett tároló azonosítására szolgáló egyedi azonosító |
| VaultUniqueId_s |Text |A védett tároló egyedi azonosítója |
| JobOperation_s |Text |A művelet, amelynek a feladata fut, például biztonsági mentés, visszaállítás, biztonsági mentés konfigurálása |
| JobStatus_s |Text |A Befejezett feladatok állapota, például befejezett, sikertelen |
| JobFailureCode_s |Text |Hiba történt a hibakód karakterlánca miatt, mert a művelet sikertelen volt |
| JobStartDateTime_s |Date/Time |A feladatok futtatásának dátuma és időpontja |
| BackupStorageDestination_s |Text |A biztonsági mentési tár célja, például felhő, lemez  |
| AdHocOrScheduledJob_s |Text | Mező, amely megadja, hogy a feladattípus ad hoc vagy ütemezett |
| JobDurationInSecs_s | Number |Feladatok teljes időtartama másodpercben |
| DataTransferredInMB_s | Number |A feladatokhoz tartozó MB-ban továbbított adatok|
| JobUniqueId_g |Text |A feladatot azonosító egyedi azonosító |
| RecoveryJobDestination_s |Text | A helyreállítási feladatok célja, amelyben az adatok helyreállítása történik |
| RecoveryJobRPDateTime_s |Datetime | A helyreállított helyreállítási pont létrehozásának dátuma és időpontja |
| RecoveryJobRPLocation_s |Text | A helyreállított helyreállítási pont tárolási helye|
| SourceSystem |Text |Az aktuális adatforrásrendszer – Azure |
| ResourceId |Text |Az összegyűjtött adatok erőforrás-azonosítója. Például Recovery Services tár erőforrás-azonosítója|
| SubscriptionId |Text |Az erőforrás előfizetés-azonosítója (pl. Recovery Services-tároló), amelybe az adatok gyűjtése történik |
| Erőforráscsoport |Text |Erőforrás erőforráscsoport (pl.: Recovery Services-tároló), amelybe az adatok gyűjtése történik |
| ResourceProvider |Text |Erőforrás-szolgáltató, amelybe az adatok gyűjtése történik. Például: Microsoft. Recoveryservices szolgáltatónál |
| Erőforrástípus |Text |Az az erőforrástípus, amelybe az adatok gyűjtése történik. Például: tárolók |

### <a name="policy"></a>Szabályzat

Ez a táblázat a házirendekkel kapcsolatos mezőkről tartalmaz információkat.

| Mező | Adattípus | Alkalmazható verziók | Leírás |
| --- | --- | --- | --- |
| EventName_s |Text ||Ez a mező az esemény nevét jelöli, mindig AzureBackupCentralReport |
| SchemaVersion_s |Text ||Ez a mező a séma aktuális verzióját jelöli, **v2** |
| State_s |Text ||A házirend-objektum aktuális állapota, például aktív, törölve |
| BackupManagementType_s |Text ||Szolgáltató típusa a biztonsági mentési feladatot végző kiszolgáló számára, például IaaSVM, fájlmappa |
| OperationName |Text ||Ez a mező az aktuális műveleti szabályzat nevét jelöli. |
| Category |Text ||Ez a mező a Azure Monitor naplókba leküldett diagnosztikai adatkategóriákat jelöli, AzureBackupReport |
| Resource |Text ||Ez az az erőforrás, amelybe begyűjti az adatokat, Recovery Services tár nevét jeleníti meg |
| PolicyUniqueId_g |Text ||A szabályzat azonosítására szolgáló egyedi azonosító |
| PolicyName_s |Text ||A megadott házirend neve |
| BackupFrequency_s |Text ||A biztonsági másolatok futtatásának gyakorisága (például naponta, hetente) |
| BackupTimes_s |Text ||A biztonsági másolatok ütemezésének dátuma és időpontja |
| BackupDaysOfTheWeek_s |Text ||A hét azon napjai, amikor a biztonsági mentések ütemezése megtörtént |
| RetentionDuration_s |Egész szám ||Konfigurált biztonsági másolatok megőrzési időtartama |
| DailyRetentionDuration_s |Egész szám ||A beállított biztonsági másolatok teljes megőrzési időtartama (nap) |
| DailyRetentionTimes_s |Text ||A napi megőrzés konfigurálásának dátuma és időpontja |
| WeeklyRetentionDuration_s |Tizedes tört szám ||Heti megőrzési időtartam összesen hetekben a konfigurált biztonsági másolatok esetében |
| WeeklyRetentionTimes_s |Text ||A heti megőrzés konfigurálásának dátuma és időpontja |
| WeeklyRetentionDaysOfTheWeek_s |Text ||A heti megőrzéshez kiválasztott hét napjai |
| MonthlyRetentionDuration_s |Tizedes tört szám ||A beállított biztonsági másolatok teljes megőrzési időtartama (hónap) |
| MonthlyRetentionTimes_s |Text ||A havi megőrzés konfigurálásának dátuma és időpontja |
| MonthlyRetentionFormat_s |Text ||A havi megőrzés konfigurációjának típusa, például naponta, hetente, hetente |
| MonthlyRetentionDaysOfTheWeek_s |Text ||A havi megőrzésre kiválasztott hét napjai |
| MonthlyRetentionWeeksOfTheMonth_s |Text ||A hónap hete, amikor a havi megőrzés konfigurálva van, például: első, utolsó stb. |
| YearlyRetentionDuration_s |Tizedes tört szám ||Teljes megőrzési időtartam években a konfigurált biztonsági másolatok esetében |
| YearlyRetentionTimes_s |Text ||Az éves adatmegőrzés konfigurálásának dátuma és időpontja |
| YearlyRetentionMonthsOfTheYear_s |Text ||Az év hónapos megőrzésre kiválasztott hónapja |
| YearlyRetentionFormat_s |Text ||Az évenkénti megőrzéshez szükséges konfiguráció típusa, például naponta, hetente, hetente | |
| YearlyRetentionDaysOfTheMonth_s |Text ||Az éves megőrzéshez kiválasztott hónap dátuma |
| SynchronisationFrequencyPerDay_s |Egész szám |v2|Napok száma egy nap során a rendszer az SC DPM és a MABS esetében szinkronizálja a fájlok biztonsági mentését |
| DiffBackupFormat_s |Text |v2|Az SQL-alapú különbözeti biztonsági másolatok formátuma az Azure virtuális gép biztonsági mentésében |
| DiffBackupTime_s |Time |v2|Az SQL Azure-beli virtuális gépek biztonsági mentésének ideje|
| DiffBackupRetentionDuration_s |Tizedes tört szám |v2|Az SQL Azure-beli virtuális gépek biztonsági mentésének megőrzési időtartama|
| LogBackupFrequency_s |Tizedes tört szám |v2|SQL-naplók biztonsági másolatainak gyakorisága|
| LogBackupRetentionDuration_s |Tizedes tört szám |v2|Az SQL Azure-beli virtuális gép biztonsági mentésében tárolt biztonsági másolatok megőrzési időtartama|
| DiffBackupDaysofTheWeek_s |Text |v2|A hét napjai az SQL-különbözeti biztonsági mentésekhez az Azure-beli virtuális gép biztonsági mentésében|
| SourceSystem |Text ||Az aktuális adatforrásrendszer – Azure |
| ResourceId |Text ||Az összegyűjtött adatok erőforrás-azonosítója. Például Recovery Services tár erőforrás-azonosítója |
| SubscriptionId |Text ||Az erőforrás előfizetés-azonosítója (pl. Recovery Services-tároló), amelybe az adatok gyűjtése történik |
| Erőforráscsoport |Text ||Erőforrás erőforráscsoport (pl.: Recovery Services-tároló), amelybe az adatok gyűjtése történik |
| ResourceProvider |Text ||Erőforrás-szolgáltató, amelybe az adatok gyűjtése történik. Például: Microsoft. Recoveryservices szolgáltatónál |
| Erőforrástípus |Text ||Az az erőforrástípus, amelybe az adatok gyűjtése történik. Például: tárolók |

### <a name="policyassociation"></a>PolicyAssociation

Ez a táblázat a különböző entitásokkal rendelkező házirend-társítások részleteit tartalmazza.

| Mező | Adattípus | Alkalmazható verziók | Leírás |
| --- | --- | --- | --- |
| EventName_s |Text ||Ez a mező az esemény nevét jelöli, mindig AzureBackupCentralReport |
| SchemaVersion_s |Text ||Ez a mező a séma aktuális verzióját jelöli, **v2** |
| State_s |Text ||A házirend-objektum aktuális állapota, például aktív, törölve |
| BackupManagementType_s |Text ||Szolgáltató típusa a biztonsági mentési feladatot végző kiszolgáló számára, például IaaSVM, fájlmappa |
| OperationName |Text ||Ez a mező az aktuális művelet nevét jelöli – PolicyAssociation |
| Category |Text ||Ez a mező a Azure Monitor naplókba leküldett diagnosztikai adatkategóriákat jelöli, AzureBackupReport |
| Resource |Text ||Ez az az erőforrás, amelybe begyűjti az adatokat, Recovery Services tár nevét jeleníti meg |
| PolicyUniqueId_g |Text ||A szabályzat azonosítására szolgáló egyedi azonosító |
| VaultUniqueId_s |Text ||Azon tár egyedi azonosítója, amelyhez ez a szabályzat tartozik |
| BackupManagementServerUniqueId_s |Text |v2 |A biztonságimásolat-felügyeleti kiszolgáló egyedi azonosítására szolgáló mező, ha van ilyen, a biztonsági mentési elem védelme        |
| SourceSystem |Text ||Az aktuális adatforrásrendszer – Azure |
| ResourceId |Text ||Az összegyűjtött adatok erőforrás-azonosítója. Például Recovery Services tár erőforrás-azonosítója |
| SubscriptionId |Text ||Az erőforrás előfizetés-azonosítója (pl. Recovery Services-tároló), amelybe az adatok gyűjtése történik |
| Erőforráscsoport |Text ||Erőforrás erőforráscsoport (pl.: Recovery Services-tároló), amelybe az adatok gyűjtése történik |
| ResourceProvider |Text ||Erőforrás-szolgáltató, amelybe az adatok gyűjtése történik. Például: Microsoft. Recoveryservices szolgáltatónál |
| Erőforrástípus |Text ||Az az erőforrástípus, amelybe az adatok gyűjtése történik. Például: tárolók |

### <a name="protected-container"></a>Védett tároló

Ez a táblázat a védett tárolók alapszintű mezőit tartalmazza. (ProtectedServer volt a v1-ben)

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Text | Védett tároló egyedi azonosítására szolgáló mező |
| ProtectedContainerOSType_s |Text |A védett tároló operációs rendszerének típusa |
| ProtectedContainerOSVersion_s |Text |A védett tároló operációs rendszerének verziója |
| AgentVersion_s |Text |Az ügynök biztonsági mentési vagy védelmi ügynökének verziószáma (SC DPM és MABS esetén) |
| BackupManagementType_s |Text |A biztonsági mentést végző szolgáltató típusa. Például: IaaSVM, fájlmappa |
| EntityState_s |Text |A védett kiszolgáló objektum jelenlegi állapota. Például: aktív, törölve |
| ProtectedContainerFriendlyName_s |Text |A védett kiszolgáló rövid neve |
| ProtectedContainerName_s |Text |A védett tároló neve |
| ProtectedContainerWorkloadType_s |Text |A védett tároló biztonsági mentésének típusa. Például: IaaSVMContainer |
| ProtectedContainerLocation_s |Text |A védett tároló a helyszínen vagy az Azure-ban található-e |
| ProtectedContainerType_s |Text |Azt jelzi, hogy a védett tároló kiszolgáló vagy tároló-e |
| ProtectedContainerProtectionState_s’  |Text |A védett tároló védelmi állapota |

### <a name="storage"></a>Storage

Ez a táblázat a Storage szolgáltatással kapcsolatos mezők részleteit tartalmazza.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| CloudStorageInBytes_s |Tizedes tört szám |Biztonsági másolatok által használt Felhőbeli biztonsági mentési tár, amely a legutóbbi érték alapján lett kiszámítva (ez a mező csak a v1-séma esetében)|
| ProtectedInstances_s |Tizedes tört szám |A előtér-tároló kiszámításához használt védett példányok száma a számlázásban, a legutóbbi érték alapján számítva |
| EventName_s |Text |Ez a mező az esemény nevét jelöli, mindig AzureBackupCentralReport |
| SchemaVersion_s |Text |Ez a mező a séma aktuális verzióját jelöli, **v2** |
| State_s |Text |A tárolási objektum aktuális állapota, például aktív, törölve |
| BackupManagementType_s |Text |Szolgáltató típusa a biztonsági mentési feladatot végző kiszolgáló számára, például IaaSVM, fájlmappa |
| OperationName |Text |Ez a mező a jelenlegi művelet – tár nevét jelöli. |
| Category |Text |Ez a mező a Azure Monitor naplókba leküldett diagnosztikai adatkategóriákat jelöli, AzureBackupReport |
| Resource |Text |Ez az az erőforrás, amelybe begyűjti az adatokat, Recovery Services tár nevét jeleníti meg |
| ProtectedServerUniqueId_s |Text |Azon védett kiszolgáló egyedi azonosítója, amelyhez a tárterületet ki kell számítani |
| VaultUniqueId_s |Text |A tár tárolójának egyedi azonosítója kiszámítva |
| SourceSystem |Text |Az aktuális adatforrásrendszer – Azure |
| ResourceId |Text |Az összegyűjtött adatok erőforrás-azonosítója. Például Recovery Services tár erőforrás-azonosítója |
| SubscriptionId |Text |Az erőforrás előfizetés-azonosítója (pl. Recovery Services-tároló), amelybe az adatok gyűjtése történik |
| Erőforráscsoport |Text |Erőforrás erőforráscsoport (pl.: Recovery Services-tároló), amelybe az adatok gyűjtése történik |
| ResourceProvider |Text |Erőforrás-szolgáltató, amelybe az adatok gyűjtése történik. Például: Microsoft. Recoveryservices szolgáltatónál |
| Erőforrástípus |Text |Az az erőforrástípus, amelybe az adatok gyűjtése történik. Például: tárolók |
| StorageUniqueId_s |Text |A tárolási entitás azonosítására használt egyedi azonosító |
| StorageType_s |Text |Tárolási típus, például felhő, kötet, lemez |
| StorageName_s |Text |A tárolási entitás neve, például E:\ |
| StorageTotalSizeInGBs_s |Text |A Storage-entitás által felhasznált tárterület teljes mérete GB-ban|

### <a name="storageassociation"></a>StorageAssociation

Ez a táblázat a tárterületet más entitásokhoz összekötő alapszintű tárterülettel kapcsolatos mezőket tartalmaz.

| Mező | Adattípus | Leírás |
| --- | --- |  --- |
| StorageUniqueId_s |Text |A tárolási entitás azonosítására használt egyedi azonosító |
| SchemaVersion_s |Text |Ez a mező a séma aktuális verzióját jelöli, **v2** |
| BackupItemUniqueId_s |Text |A tárolási entitáshoz kapcsolódó biztonsági mentési elem azonosítására használt egyedi azonosító |
| BackupManagementServerUniqueId_s |Text |A tárolási entitáshoz kapcsolódó biztonságimásolat-felügyeleti kiszolgáló azonosítására használt egyedi azonosító|
| VaultUniqueId_s |Text |A tárolási entitáshoz kapcsolódó tár azonosítására használt egyedi azonosító|
| StorageConsumedInMBs_s |Number|A megfelelő tároló biztonsági mentési eleme által felhasznált tárterület mérete |
| StorageAllocatedInMBs_s |Number |A megfelelő biztonsági mentési tétel által lefoglalt tárterület a lemez típusú megfelelő tárolóban|

### <a name="vault"></a>Tár

Ez a táblázat a tárolóval kapcsolatos mezők részleteit tartalmazza.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| EventName_s |Text |Ez a mező az esemény nevét jelöli, mindig AzureBackupCentralReport |
| SchemaVersion_s |Text |Ez a mező a séma aktuális verzióját jelöli, **v2** |
| State_s |Text |A tár objektum aktuális állapota, például aktív, törölve |
| OperationName |Text |Ez a mező az aktuális művelet (Vault) nevét jelöli. |
| Category |Text |Ez a mező a Azure Monitor naplókba leküldett diagnosztikai adatkategóriákat jelöli, AzureBackupReport |
| Resource |Text |Ez az az erőforrás, amelybe begyűjti az adatokat, Recovery Services tár nevét jeleníti meg |
| VaultUniqueId_s |Text |A tár egyedi azonosítója |
| VaultName_s |Text |A tár neve |
| AzureDataCenter_s |Text |Az adatközpont, ahol a tár található |
| StorageReplicationType_s |Text |A tár tárolási replikálásának típusa, például GeoRedundant |
| SourceSystem |Text |Az aktuális adatforrásrendszer – Azure |
| ResourceId |Text |Az összegyűjtött adatok erőforrás-azonosítója. Például Recovery Services tár erőforrás-azonosítója |
| SubscriptionId |Text |Az erőforrás előfizetés-azonosítója (pl. Recovery Services-tároló), amelybe az adatok gyűjtése történik |
| Erőforráscsoport |Text |Erőforrás erőforráscsoport (pl.: Recovery Services-tároló), amelybe az adatok gyűjtése történik |
| ResourceProvider |Text |Erőforrás-szolgáltató, amelybe az adatok gyűjtése történik. Például: Microsoft. Recoveryservices szolgáltatónál |
| Erőforrástípus |Text |Az az erőforrástípus, amelybe az adatok gyűjtése történik. Például: tárolók |

### <a name="backup-management-server"></a>Biztonságimásolat-felügyeleti kiszolgáló

Ez a táblázat a biztonságimásolat-felügyeleti kiszolgálók alapszintű mezőit tartalmazza.

|Mező  |Adattípus  | Leírás  |
|---------|---------|----------|
|BackupManagmentServerName_s     |Text         |A biztonságimásolat-felügyeleti kiszolgáló neve        |
|AzureBackupAgentVersion_s     |Text         |A Azure Backup ügynök verziója a biztonságimásolat-felügyeleti kiszolgálón          |
|BackupManagmentServerVersion_s     |Text         |A biztonságimásolat-felügyeleti kiszolgáló verziója|
|BackupManagmentServerOSVersion_s     |Text            |A biztonságimásolat-felügyeleti kiszolgáló operációs rendszerének verziója|
|BackupManagementServerType_s     |Text         |A biztonságimásolat-felügyeleti kiszolgáló típusa, mint MABS, SC DPM|
|BackupManagmentServerUniqueId_s     |Text         |A biztonságimásolat-felügyeleti kiszolgáló egyedi azonosítására szolgáló mező       |

### <a name="preferredworkloadonvolume"></a>PreferredWorkloadOnVolume

Ez a tábla meghatározza, hogy egy kötet milyen munkaterheléshez van társítva.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| StorageUniqueId_s |Text |A tárolási entitás azonosítására használt egyedi azonosító |
| BackupItemType_s |Text |Azok a munkaterhelések, amelyekhez ez a kötet az előnyben részesített tároló|

### <a name="protectedinstance"></a>ProtectedInstance

Ez a táblázat az alapszintű védett példányokkal kapcsolatos mezőket tartalmazza.

| Mező | Adattípus |Alkalmazható verziók | Leírás |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Text |v2|Egyedi azonosító, amely a DPM, MABS használatával biztonsági mentést végző virtuális gépek biztonsági mentési elemének azonosítására szolgál.|
| ProtectedContainerUniqueId_s |Text |v2|Egyedi azonosító, amely a védett tároló azonosítására szolgál a DPM, a MABS-t használó virtuális gépek kivételével.|
| ProtectedInstanceCount_s |Text |v2|A társított biztonsági másolati elemhez vagy a védett tárolóhoz tartozó védett példányok száma az adott napon és időpontban|

### <a name="recoverypoint"></a>RecoveryPoint

Ez a tábla a helyreállítási pontok alapszintű kapcsolódó mezőit tartalmazza.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| BackupItemUniqueId_s |Text |Egyedi azonosító, amely a DPM, MABS használatával biztonsági mentést végző virtuális gépek biztonsági mentési elemének azonosítására szolgál.|
| OldestRecoveryPointTime_s |Text |A biztonsági mentési tétel legrégebbi helyreállítási pontjának dátuma|
| OldestRecoveryPointLocation_s |Text |A biztonsági mentési tétel legrégebbi helyreállítási pontjának helye|
| LatestRecoveryPointTime_s |Text |A biztonsági mentési tétel legutóbbi helyreállítási pontjának dátuma|
| LatestRecoveryPointLocation_s |Text |A biztonsági mentési tétel legutóbbi helyreállítási pontjának helye|

## <a name="next-steps"></a>További lépések

Az adatmodell áttekintése után megkezdheti az [Egyéni lekérdezések létrehozását](../azure-monitor/learn/tutorial-logs-dashboards.md) Azure monitor naplókban a saját irányítópultjának létrehozásához.
