---
title: Az Azure Monitor naplózza az adatmodellt
description: Ebben a cikkben az Azure Monitor Log Analytics adatmodell részleteit az Azure Backup adatok.
ms.topic: conceptual
ms.date: 02/26/2019
ms.openlocfilehash: d14634c5e317682462e77e0549f064c75059f15c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77586376"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Log Analytics-adatmodell az Azure biztonsági mentési adataihoz

A Log Analytics-adatmodell használatával egyéni riasztásokat hozhat létre a Log Analytics szolgáltatásból.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
>
> Ez az adatmodell az Azure diagnosztikai módja diagnosztikai események küldése a Log Analytics (LA) küldése. Az új erőforrás-specifikus mód adatmodelljének megismeréséhez tekintse meg a következő cikket: [Adatmodell az Azure biztonsági mentésdiagnosztikai eseményekhez](https://docs.microsoft.com/azure/backup/backup-azure-reports-data-model)

## <a name="using-azure-backup-data-model"></a>Az Azure Backup adatmodell használata

Az adatmodell részeként megadott alábbi mezők segítségével a követelményeknek megfelelően vizualizációkat, egyéni lekérdezéseket és irányítópultot hozhat létre.

### <a name="alert"></a>Riasztás

Ez a tábla a riasztásokkal kapcsolatos mezők részleteit tartalmazza.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| AlertUniqueId_s |Szöveg |A létrehozott riasztás egyedi azonosítója |
| AlertType_s |Szöveg |A riasztás típusa, például Biztonsági másolat |
| AlertStatus_s |Szöveg |A riasztás állapota, például Aktív |
| AlertOccurrenceDateTime_s |Dátum és idő |A riasztás létrehozásának dátuma és időpontja |
| AlertSeverity_s |Szöveg |A riasztás súlyossága, például kritikus |
|AlertTimeToResolveInMinutes_s    | Szám        |A riasztás feloldásához szükséges idő. Üres az aktív riasztásokhoz.         |
|AlertConsolidationStatus_s   |Szöveg         |Annak megállapítása, hogy a riasztás összevont riasztás-e vagy sem         |
|CountOfAlertsConsolidated_s     |Szám         |Összesített riasztások száma, ha összevont riasztásról van szó          |
|AlertRaisedOn_s     |Szöveg         |Az entitás típusa, amelyen a riasztás megjelenik         |
|AlertCode_s     |Szöveg         |Riasztástípus egyedi azonosítására szolgáló kód         |
|RecommendedAction_s   |Szöveg         |A riasztás feloldásához ajánlott művelet         |
| EventName_s |Szöveg |Az esemény neve. Mindig AzureBackupCentralReport |
| BackupItemUniqueId_s |Szöveg |A riasztáshoz társított biztonsági másolat elem egyedi azonosítója |
| SchemaVersion_s |Szöveg |A séma jelenlegi verziója, például **V2** |
| State_s |Szöveg |A riasztási objektum aktuális állapota, például Aktív, Törölve |
| BackupManagementType_s |Szöveg |Szolgáltató típusa a biztonsági mentés végrehajtásához, például IaaSVM, FileFolder, amelyhez ez a riasztás tartozik |
| OperationName |Szöveg |Az aktuális művelet neve, például Riasztás |
| Kategória |Szöveg |Az Azure Monitor-naplókba leadott diagnosztikai adatok kategóriája. Mindig AzureBackupReport |
| Erőforrás |Szöveg |Ez az az erőforrás, amelyhez adatokat gyűjtenek, azt mutatja, recovery services tároló neve |
| ProtectedContainerUniqueId_s |Szöveg |A riasztáshoz társított védett kiszolgáló egyedi azonosítója (ProtectedServerUniqueId_s a V1-ben)|
| VaultUniqueId_s |Szöveg |A riasztáshoz társított védett tároló egyedi azonosítója |
| SourceSystem |Szöveg |Az aktuális adatok forrásrendszere - Azure |
| ResourceId |Szöveg |Annak az erőforrásnak az egyedi azonosítója, amelyről az adatok gyűjtése történik. Például egy Recovery Services-tároló erőforrásazonosítója |
| SubscriptionId |Szöveg |Az erőforrás előfizetési azonosítója (pl. Helyreállítási szolgáltatások tárolója), amelyhez adatokat gyűjtenek |
| ResourceGroup |Szöveg |Az erőforrás erőforráscsoportja (pl. Helyreállítási szolgáltatások tárolója), amelyhez adatokat gyűjtenek |
| ResourceProvider |Szöveg |Az az erőforrás-szolgáltató, amelyhez adatokat gyűjtenek. Például a Microsoft.RecoveryServices |
| ResourceType |Szöveg |Az az erőforrástípus, amelyhez adatokat gyűjtenek. Például a Vaults |

### <a name="backupitem"></a>Biztonsági elem

Ez a tábla a biztonsági másolathoz kapcsolódó mezők részleteit tartalmazza.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| EventName_s |Szöveg |Az esemény neve. Mindig AzureBackupCentralReport |  
| BackupItemUniqueId_s |Szöveg |A biztonsági másolat elemének egyedi azonosítója |
| BackupItemId_s |Szöveg |A biztonsági másolat elemének azonosítója (ez a mező csak az 1- es számú sémához szolgál) |
| BackupItemName_s |Szöveg |Biztonsági másolat elemének neve |
| BackupItemFriendlyName_s |Szöveg |A biztonsági másolat elemének rövid neve |
| BackupItemType_s |Szöveg |A biztonsági másolat típusa, például virtuális gép, FileFolder |
| BackupItemProtectionState_s |Szöveg |A biztonsági másolat elem védelmi állapota |
| BackupItemAppVersion_s |Szöveg |A biztonsági másolat elem alkalmazásverziója |
| ProtectionState_s |Szöveg |A biztonsági másolat elemének jelenlegi védelmi állapota, például: Védett, ProtectionStopped |
| ProtectionGroupName_s |Szöveg | Annak a védelmi csoportnak a neve, amelyben a biztonsági másolat elem védett, az SC DPM és a MABS esetében, ha van ilyen.|
| SecondaryBackupProtectionState_s |Szöveg |Azt jelzi, hogy engedélyezve van-e a biztonsági másolat elemének másodlagos védelme|
| SchemaVersion_s |Szöveg |A séma verziója, például **V2** |
| State_s |Szöveg |A biztonsági másolat elemobjektumának állapota, például Aktív, Törölve |
| BackupManagementType_s |Szöveg |Szolgáltató típusa a biztonsági mentés végrehajtásához, például IaaSVM, FileFolder, amelyhez ez a biztonsági másolat elem tartozik |
| OperationName |Szöveg |A művelet neve, például BackupItem |
| Kategória |Szöveg |Az Azure Monitor-naplókba leadott diagnosztikai adatok kategóriája. Mindig AzureBackupReport |
| Erőforrás |Szöveg |Az az erőforrás, amelyhez adatokat gyűjtenek, például a Recovery Services-tároló neve |
| SourceSystem |Szöveg |Az aktuális adatok forrásrendszere - Azure |
| ResourceId |Szöveg |Az összegyűjtött adatok erőforrásazonosítója, például a Recovery Services-tároló erőforrásazonosítója |
| SubscriptionId |Szöveg |Az erőforrás előfizetési azonosítója (pl. Recovery Services-tároló) az összegyűjtött adatokhoz |
| ResourceGroup |Szöveg |Az erőforrás erőforráscsoportja (pl. Recovery Services-tároló) az összegyűjtött adatokhoz |
| ResourceProvider |Szöveg |Az összegyűjtött adatok erőforrás-szolgáltatója, például Microsoft.RecoveryServices |
| ResourceType |Szöveg |Az összegyűjtött adatok erőforrásának típusa, például tárolók |

### <a name="backupitemassociation"></a>BackupItemAssociation

Ez a táblázat a biztonsági másolat elemek és a különböző entitások társításának részleteit tartalmazza.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| EventName_s |Szöveg |Ez a mező az esemény nevét jelöli, mindig az AzureBackupCentralReport |  
| BackupItemUniqueId_s |Szöveg |A biztonsági másolat elemének egyedi azonosítója |
| SchemaVersion_s |Szöveg |Ez a mező a séma aktuális verzióját jelöli, **v2-es** |
| State_s |Szöveg |A biztonsági másolat elemtársítási objektum aktuális állapota, például Aktív, Törölve |
| BackupManagementType_s |Szöveg |Szolgáltató típusa a biztonsági mentési feladatot végzett kiszolgálóhoz, például IaaSVM, FileFolder |
| BackupItemSourceSize_s |Szöveg | A biztonsági másolat elemének előtér-mérete |
| BackupManagementServerUniqueId_s |Szöveg | A biztonsági másolat kezelőkiszolgáló egyedi azonosítására szolgáló mező, amelyen keresztül a biztonsági másolat készítő elem védett, adott esetben |
| Kategória |Szöveg |Ez a mező a Log Analytics szolgáltatásba leadott diagnosztikai adatok kategóriáját jelöli, az AzureBackupReport |
| OperationName |Szöveg |Ez a mező az aktuális művelet nevét jelöli - BackupItemAssociation |
| Erőforrás |Szöveg |Ez az az erőforrás, amelyhez adatokat gyűjtenek, azt mutatja, recovery services tároló neve |
| ProtectedContainerUniqueId_s |Szöveg |A biztonsági másolat eleméhez társított védett kiszolgáló egyedi azonosítója (ProtectedServerUniqueId_s a V1-ben) |
| VaultUniqueId_s |Szöveg |A biztonsági másolat elemet tartalmazó tároló egyedi azonosítója |
| SourceSystem |Szöveg |Az aktuális adatok forrásrendszere - Azure |
| ResourceId |Szöveg |Az összegyűjtött adatok erőforrás-azonosítója. Például a Recovery Services tároló erőforrásazonosítója |
| SubscriptionId |Szöveg |Az erőforrás előfizetési azonosítója (pl. Recovery Services-tároló), amelyhez adatokat gyűjtenek |
| ResourceGroup |Szöveg |Az erőforrás erőforráscsoportja (pl. Recovery Services-tároló), amelyhez adatokat gyűjtenek |
| ResourceProvider |Szöveg |Az összegyűjtött adatok erőforrás-szolgáltatója, például Microsoft.RecoveryServices |
| ResourceType |Szöveg |Az adatok erőforrásának típusa történik, például a Tárolók |

### <a name="backupmanagementserver"></a>BackupManagementServer

Ez a táblázat a biztonsági másolat elemek és a különböző entitások társításának részleteit tartalmazza.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
|BackupManagementServerName_s     |Szöveg         |A biztonságimásolat-kezelő kiszolgáló neve        |
|AzureBackupAgentVersion_s     |Szöveg         |Az Azure biztonsági másolat ügynökének verziója a biztonsági másolat kezelőkiszolgálóján          |
|BackupManagementServerVersion_s     |Szöveg         |A biztonságimásolat-kezelő kiszolgáló verziója|
|BackupManagementServerOSVersion_s    |Szöveg            |A Biztonságimásolat-kezelő kiszolgáló operációs rendszerének verziója|
|BackupManagementServerType_s     |Szöveg         |A biztonságimásolat-kezelő kiszolgáló típusa MABS, SC DPM|
|BackupManagementServerUniqueId_s     |Szöveg         |A Biztonságimásolat-kezelő kiszolgáló egyedi azonosítására szolgáló mező       |
| SourceSystem |Szöveg |Az aktuális adatok forrásrendszere - Azure |
| ResourceId |Szöveg |Az összegyűjtött adatok erőforrás-azonosítója. Például a Recovery Services tároló erőforrásazonosítója |
| SubscriptionId |Szöveg |Az erőforrás előfizetési azonosítója (pl. Recovery Services-tároló), amelyhez adatokat gyűjtenek |
| ResourceGroup |Szöveg |Az erőforrás erőforráscsoportja (pl. Recovery Services-tároló), amelyhez adatokat gyűjtenek |
| ResourceProvider |Szöveg |Az összegyűjtött adatok erőforrás-szolgáltatója, például Microsoft.RecoveryServices |
| ResourceType |Szöveg |Az adatok erőforrásának típusa történik, például a Tárolók |

### <a name="job"></a>Feladat

Ez a tábla a feladattal kapcsolatos mezők részleteit tartalmazza.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| EventName_s |Szöveg |Az esemény neve. Mindig AzureBackupCentralReport |
| BackupItemUniqueId_s |Szöveg |A biztonsági másolat elemének egyedi azonosítója |
| SchemaVersion_s |Szöveg |A séma verziója, például **V2** |
| State_s |Szöveg |A feladatobjektum aktuális állapota, például Aktív, Törölve |
| BackupManagementType_s |Szöveg |Szolgáltató típusa a biztonsági mentési feladatot végzett kiszolgálóhoz, például IaaSVM, FileFolder |
| OperationName |Szöveg |Ez a mező az aktuális művelet nevét jelöli - Projekt |
| Kategória |Szöveg |Ez a mező az Azure Monitor-naplókba leadott diagnosztikai adatok kategóriáját jelöli, ez az AzureBackupReport |
| Erőforrás |Szöveg |Ez az az erőforrás, amelyhez adatokat gyűjtenek, azt mutatja, recovery services tároló neve |
| ProtectedServerUniqueId_s |Szöveg |A feladatot társított védett kiszolgáló egyedi azonosítója |
| ProtectedContainerUniqueId_s |Szöveg | Egyedi azonosító a feladat által futtatott védett tároló azonosítására |
| VaultUniqueId_s |Szöveg |A védett tároló egyedi azonosítója |
| JobOperation_s |Szöveg |Az a művelet, amelyhez a feladat fut, például Biztonsági mentés, Visszaállítás, Biztonsági másolat konfigurálása |
| JobStatus_s |Szöveg |A befejezett feladat állapota, például Befejezett, Sikertelen |
| JobFailureCode_s |Szöveg |Hibakód-karakterlánc, amely miatt hiba történt |
| JobStartDateTime_s |Dátum és idő |A feladat futásának dátuma és időpontja |
| BackupStorageDestination_s |Szöveg |A biztonsági mentési tároló célja, például felhő, lemez  |
| AdHocOrScheduledJob_s |Szöveg | Mező annak megadásához, hogy a feladat ad hoc vagy ütemezett-e |
| JobDurationInSecs_s | Szám |A feladat teljes időtartama másodpercben |
| DataTransferredInMB_s | Szám |Mb-ban továbbított adatok ehhez a feladathoz|
| JobUniqueId_g |Szöveg |Egyedi azonosító a feladat azonosításához |
| RecoveryJobDestination_s |Szöveg | A helyreállítási feladat célja, ahol az adatok helyreállnak |
| RecoveryJobRPDateTime_s |DateTime | A visszaszerzési pont létrehozásának dátuma, időpontja |
| RecoveryJobRPLocation_s |Szöveg | Az a hely, ahol a helyreállító pontot tárolják|
| SourceSystem |Szöveg |Az aktuális adatok forrásrendszere - Azure |
| ResourceId |Szöveg |Az összegyűjtött adatok erőforrás-azonosítója. Például a Recovery Services tároló erőforrásazonosítója|
| SubscriptionId |Szöveg |Az erőforrás előfizetési azonosítója (pl. Helyreállítási szolgáltatások tárolója), amelyhez adatokat gyűjtenek |
| ResourceGroup |Szöveg |Az erőforrás erőforráscsoportja (pl. Helyreállítási szolgáltatások tárolója), amelyhez adatokat gyűjtenek |
| ResourceProvider |Szöveg |Az az erőforrás-szolgáltató, amelyhez adatokat gyűjtenek. Például a Microsoft.RecoveryServices |
| ResourceType |Szöveg |Az az erőforrástípus, amelyhez adatokat gyűjtenek. Például a Vaults |

### <a name="policy"></a>Szabályzat

Ez a tábla a házirenddel kapcsolatos mezők részleteit tartalmazza.

| Mező | Adattípus | Alkalmazható verziók | Leírás |
| --- | --- | --- | --- |
| EventName_s |Szöveg ||Ez a mező az esemény nevét jelöli, mindig az AzureBackupCentralReport |
| SchemaVersion_s |Szöveg ||Ez a mező a séma aktuális verzióját jelöli, **v2-es** |
| State_s |Szöveg ||A házirendobjektum aktuális állapota, például Aktív, Törölve |
| BackupManagementType_s |Szöveg ||Szolgáltató típusa a biztonsági mentési feladatot végzett kiszolgálóhoz, például IaaSVM, FileFolder |
| OperationName |Szöveg ||Ez a mező az aktuális művelet nevét jelöli - Házirend |
| Kategória |Szöveg ||Ez a mező az Azure Monitor-naplókba leadott diagnosztikai adatok kategóriáját jelöli, ez az AzureBackupReport |
| Erőforrás |Szöveg ||Ez az az erőforrás, amelyhez adatokat gyűjtenek, azt mutatja, recovery services tároló neve |
| PolicyUniqueId_g |Szöveg ||Egyedi azonosító a házirend azonosításához |
| PolicyName_s |Szöveg ||A megadott házirend neve |
| BackupFrequency_s |Szöveg ||A biztonsági mentések futtatásának gyakorisága, például napi, heti |
| BackupTimes_s |Szöveg ||A biztonsági mentések ütemezésének dátuma és időpontja |
| BackupDaysOfTheWeek_s |Szöveg ||A hét azon napjai, amikor a biztonsági mentések ütemezve vannak |
| RetentionDuration_s |Egész szám ||A konfigurált biztonsági mentések megőrzési időtartama |
| DailyRetentionDuration_s |Egész szám ||A konfigurált biztonsági mentések teljes megőrzési időtartama napokban |
| DailyRetentionTimes_s |Szöveg ||A napi adatmegőrzés konfigurálásának dátuma és időpontja |
| WeeklyRetentionDuration_s |Tizedes tört ||A beállított biztonsági mentések heti megőrzési időtartama hetekben |
| WeeklyRetentionTimes_s |Szöveg ||A heti adatmegőrzés konfigurálásának dátuma és időpontja |
| WeeklyRetentionDaysOfTheWeek_s |Szöveg ||A heti megőrzésre kiválasztott hét napjai |
| MonthlyRetentionDuration_s |Tizedes tört ||A konfigurált biztonsági mentések teljes megőrzési időtartama hónapokban |
| MonthlyRetentionTimes_s |Szöveg ||A havi adatmegőrzés konfigurálásának dátuma és időpontja |
| MonthlyRetentionFormat_s |Szöveg ||A havi megőrzés konfigurációjának típusa, például napi napi, heti heti heti |
| MonthlyRetentionDaysOfTheWeek_s |Szöveg ||A havi megőrzésre kiválasztott hét napjai |
| MonthlyRetentionWeeksOfTheMonth_s |Szöveg ||A havi megőrzés konfigurálásának hónapjai, például: Első, Utolsó stb. |
| YearlyRetentionDuration_s |Tizedes tört ||A konfigurált biztonsági mentések teljes megőrzési időtartama években |
| YearlyRetentionTimes_s |Szöveg ||Az éves adatmegőrzés konfigurálásának dátuma és időpontja |
| YearlyRetentionMonthsOfTheYear_s |Szöveg ||Az éves megőrzésre kiválasztott év hónapjai |
| YearlyRetentionFormat_s |Szöveg ||Az éves adatmegőrzéskonfiguráció típusa, például napi napi, heti heti heti heti | |
| YearlyRetentionDaysOfTheMonth_s |Szöveg ||Az éves adatmegőrzéshez kiválasztott hónap dátumai |
| SynchronisationFrequencyPerDay_s |Egész szám |v2|Az SC DPM és mabs fájlbiztonsági mentések szinkronizálásának száma a nap folyamán |
| DiffBackupFormat_s |Szöveg |v2|Az SQL különbözeti biztonsági másolatainak formátuma az Azure virtuális gép biztonsági mentésében |
| DiffBackupTime_s |Time |v2|Az SQL különbözeti biztonsági mentéseinek ideje az Azure virtuális gép biztonsági mentésében|
| DiffBackupRetentionDuration_s |Tizedes tört |v2|Az SQL különbözeti biztonsági másolatainak megőrzési időtartama az Azure virtuális gép biztonsági mentésében|
| LogBackupFrequency_s |Tizedes tört |v2|Az SQL naplóbiztonsági mentésének gyakorisága|
| LogBackupRetentionDuration_s |Tizedes tört |v2|Az SQL-biztonsági mentés naplózási biztonsági másolatainak megőrzési időtartama az Azure virtuális gép biztonsági mentésében|
| DiffBackupDaysofTheWeek_s |Szöveg |v2|A hét napjai az SQL különbözeti biztonsági mentéseiszámára az Azure virtuális gép biztonsági mentésében|
| SourceSystem |Szöveg ||Az aktuális adatok forrásrendszere - Azure |
| ResourceId |Szöveg ||Az összegyűjtött adatok erőforrás-azonosítója. Például a Recovery Services tároló erőforrásazonosítója |
| SubscriptionId |Szöveg ||Az erőforrás előfizetési azonosítója (pl. Helyreállítási szolgáltatások tárolója), amelyhez adatokat gyűjtenek |
| ResourceGroup |Szöveg ||Az erőforrás erőforráscsoportja (pl. Helyreállítási szolgáltatások tárolója), amelyhez adatokat gyűjtenek |
| ResourceProvider |Szöveg ||Az az erőforrás-szolgáltató, amelyhez adatokat gyűjtenek. Például a Microsoft.RecoveryServices |
| ResourceType |Szöveg ||Az az erőforrástípus, amelyhez adatokat gyűjtenek. Például a Vaults |

### <a name="policyassociation"></a>PolicyAssociation (Politikai Társítás)

Ez a táblázat a különböző entitásokkal való házirend-társításokról nyújt részletes adatokat.

| Mező | Adattípus | Alkalmazható verziók | Leírás |
| --- | --- | --- | --- |
| EventName_s |Szöveg ||Ez a mező az esemény nevét jelöli, mindig az AzureBackupCentralReport |
| SchemaVersion_s |Szöveg ||Ez a mező a séma aktuális verzióját jelöli, **v2-es** |
| State_s |Szöveg ||A házirendobjektum aktuális állapota, például Aktív, Törölve |
| BackupManagementType_s |Szöveg ||Szolgáltató típusa a biztonsági mentési feladatot végzett kiszolgálóhoz, például IaaSVM, FileFolder |
| OperationName |Szöveg ||Ez a mező az aktuális művelet nevét jelöli - PolicyAssociation |
| Kategória |Szöveg ||Ez a mező az Azure Monitor-naplókba leadott diagnosztikai adatok kategóriáját jelöli, ez az AzureBackupReport |
| Erőforrás |Szöveg ||Ez az az erőforrás, amelyhez adatokat gyűjtenek, azt mutatja, recovery services tároló neve |
| PolicyUniqueId_g |Szöveg ||Egyedi azonosító a házirend azonosításához |
| VaultUniqueId_s |Szöveg ||Annak a tárolónak az egyedi azonosítója, amelyhez ez a házirend tartozik |
| BackupManagementServerUniqueId_s |Szöveg |v2 |A biztonsági másolat kezelőkiszolgáló egyedi azonosítására szolgáló mező, amelyen keresztül a biztonsági másolat készítő elem védett, adott esetben        |
| SourceSystem |Szöveg ||Az aktuális adatok forrásrendszere - Azure |
| ResourceId |Szöveg ||Az összegyűjtött adatok erőforrás-azonosítója. Például a Recovery Services tároló erőforrásazonosítója |
| SubscriptionId |Szöveg ||Az erőforrás előfizetési azonosítója (pl. Helyreállítási szolgáltatások tárolója), amelyhez adatokat gyűjtenek |
| ResourceGroup |Szöveg ||Az erőforrás erőforráscsoportja (pl. Helyreállítási szolgáltatások tárolója), amelyhez adatokat gyűjtenek |
| ResourceProvider |Szöveg ||Az az erőforrás-szolgáltató, amelyhez adatokat gyűjtenek. Például a Microsoft.RecoveryServices |
| ResourceType |Szöveg ||Az az erőforrástípus, amelyhez adatokat gyűjtenek. Például a Vaults |

### <a name="protected-container"></a>Védett tároló

Ez a tábla a védett tárolók alapvető mezőit tartalmazza. (Védettkiszolgáló volt a v1-ben)

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| ProtectedContainerUniqueId_s |Szöveg | Védett tároló egyedi azonosítására szolgáló mező |
| ProtectedContainerOSType_s |Szöveg |A védett tároló operációs rendszer ének típusa |
| ProtectedContainerOSVersion_s |Szöveg |A védett tároló operációs rendszerének verziója |
| AgentVersion_s |Szöveg |Az ügynök biztonsági másolatának vagy a védelmi ügynöknek a verziószáma (SC DPM és MABS esetén) |
| BackupManagementType_s |Szöveg |Szolgáltató típusa a biztonsági mentés végrehajtásához. Például: IaaSVM, FileFolder |
| EntityState_s |Szöveg |A védett kiszolgálóobjektum jelenlegi állapota. Például, Aktív, Törölve |
| ProtectedContainerFriendlyName_s |Szöveg |A védett kiszolgáló rövid neve |
| ProtectedContainerName_s |Szöveg |A védett tároló neve |
| ProtectedContainerWorkloadType_s |Szöveg |Biztonsági másolatot kell a védett tároló típusa. Például az IaaSVMContainer |
| ProtectedContainerLocation_s |Szöveg |Azt jelzi, hogy a védett tároló a helyszínen vagy az Azure-ban található-e |
| ProtectedContainerType_s |Szöveg |Azt jelzi, hogy a védett tároló kiszolgáló vagy tároló |
| ProtectedContainerProtectionState_s"  |Szöveg |A védett tároló védelmi állapota |

### <a name="storage"></a>Storage

Ez a tábla a tárolással kapcsolatos mezők részleteit tartalmazza.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| CloudStorageInBytes_s |Tizedes tört |A biztonsági mentések által használt felhőalapú biztonsági mentési tároló, a legújabb érték alapján számítva (ez a mező csak v1 sémához használható)|
| ProtectedInstances_s |Tizedes tört |Az előtér-tárolás számlázásban történő kiszámításához használt védett példányok száma, a legújabb érték alapján számítva |
| EventName_s |Szöveg |Ez a mező az esemény nevét jelöli, mindig az AzureBackupCentralReport |
| SchemaVersion_s |Szöveg |Ez a mező a séma aktuális verzióját jelöli, **v2-es** |
| State_s |Szöveg |A tárolóobjektum aktuális állapota, például Aktív, Törölve |
| BackupManagementType_s |Szöveg |Szolgáltató típusa a biztonsági mentési feladatot végzett kiszolgálóhoz, például IaaSVM, FileFolder |
| OperationName |Szöveg |Ez a mező az aktuális művelet nevét jelöli - Tárolás |
| Kategória |Szöveg |Ez a mező az Azure Monitor-naplókba leadott diagnosztikai adatok kategóriáját jelöli, ez az AzureBackupReport |
| Erőforrás |Szöveg |Ez az az erőforrás, amelyhez adatokat gyűjtenek, azt mutatja, recovery services tároló neve |
| ProtectedServerUniqueId_s |Szöveg |Annak a védett kiszolgálónak az egyedi azonosítója, amelynek a tárolóját kiszámítják |
| VaultUniqueId_s |Szöveg |A tároló egyedi azonosítója a tárolóhoz számításra kerül |
| SourceSystem |Szöveg |Az aktuális adatok forrásrendszere - Azure |
| ResourceId |Szöveg |Az összegyűjtött adatok erőforrás-azonosítója. Például a Recovery Services tároló erőforrásazonosítója |
| SubscriptionId |Szöveg |Az erőforrás előfizetési azonosítója (pl. Helyreállítási szolgáltatások tárolója), amelyhez adatokat gyűjtenek |
| ResourceGroup |Szöveg |Az erőforrás erőforráscsoportja (pl. Helyreállítási szolgáltatások tárolója), amelyhez adatokat gyűjtenek |
| ResourceProvider |Szöveg |Az az erőforrás-szolgáltató, amelyhez adatokat gyűjtenek. Például a Microsoft.RecoveryServices |
| ResourceType |Szöveg |Az az erőforrástípus, amelyhez adatokat gyűjtenek. Például a Vaults |
| StorageUniqueId_s |Szöveg |A tárolási entitás azonosítására használt egyedi azonosító |
| StorageType_s |Szöveg |A tárhely típusa, például felhő, kötet, lemez |
| StorageName_s |Szöveg |A tárolóentitás neve, például E:\ |
| StorageTotalSizeInGBs_s |Szöveg |A tárterület teljes mérete GB-ban, a tárolóentitás által fogyasztva|

### <a name="storageassociation"></a>StorageAssociation (Tárolástársítás)

Ez a tábla a tárolást más entitásokkal összekötő alapvető tárolóval kapcsolatos mezőket tartalmazza.

| Mező | Adattípus | Leírás |
| --- | --- |  --- |
| StorageUniqueId_s |Szöveg |A tárolási entitás azonosítására használt egyedi azonosító |
| SchemaVersion_s |Szöveg |Ez a mező a séma aktuális verzióját jelöli, **v2-es** |
| BackupItemUniqueId_s |Szöveg |A tárolóentitáshoz kapcsolódó biztonsági másolat elemének azonosítására használt egyedi azonosító |
| BackupManagementServerUniqueId_s |Szöveg |A tárolóentitáshoz kapcsolódó biztonságimentési felügyeleti kiszolgáló azonosítására használt egyedi azonosító|
| VaultUniqueId_s |Szöveg |A tárolóhoz kapcsolódó tároló azonosítására használt egyedi azonosító|
| StorageConsumedInMBs_s |Szám|A megfelelő biztonsági másolat elem által a megfelelő tárolóban felhasznált tárhely mérete |
| StorageAllocatedInMBs_s |Szám |A megfelelő biztonságimásolat-elem által a Lemez típusú megfelelő tárolóban lefoglalt tárterület mérete|

### <a name="vault"></a>Tároló

Ez a táblázat a tárolóval kapcsolatos mezők részleteit tartalmazza.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| EventName_s |Szöveg |Ez a mező az esemény nevét jelöli, mindig az AzureBackupCentralReport |
| SchemaVersion_s |Szöveg |Ez a mező a séma aktuális verzióját jelöli, **v2-es** |
| State_s |Szöveg |A tárolóobjektum aktuális állapota, például Aktív, Törölve |
| OperationName |Szöveg |Ez a mező az aktuális művelet nevét jelöli - Vault |
| Kategória |Szöveg |Ez a mező az Azure Monitor-naplókba leadott diagnosztikai adatok kategóriáját jelöli, ez az AzureBackupReport |
| Erőforrás |Szöveg |Ez az az erőforrás, amelyhez adatokat gyűjtenek, azt mutatja, recovery services tároló neve |
| VaultUniqueId_s |Szöveg |A tároló egyedi azonosítója |
| VaultName_s |Szöveg |A tároló neve |
| AzureDataCenter_s |Szöveg |Adatközpont, ahol a tároló található |
| StorageReplicationType_s |Szöveg |A tároló tárolóreplikációjának típusa, például GeoRedundant |
| SourceSystem |Szöveg |Az aktuális adatok forrásrendszere - Azure |
| ResourceId |Szöveg |Az összegyűjtött adatok erőforrás-azonosítója. Például a Recovery Services tároló erőforrásazonosítója |
| SubscriptionId |Szöveg |Az erőforrás előfizetési azonosítója (pl. Helyreállítási szolgáltatások tárolója), amelyhez adatokat gyűjtenek |
| ResourceGroup |Szöveg |Az erőforrás erőforráscsoportja (pl. Helyreállítási szolgáltatások tárolója), amelyhez adatokat gyűjtenek |
| ResourceProvider |Szöveg |Az az erőforrás-szolgáltató, amelyhez adatokat gyűjtenek. Például a Microsoft.RecoveryServices |
| ResourceType |Szöveg |Az az erőforrástípus, amelyhez adatokat gyűjtenek. Például a Vaults |

### <a name="backup-management-server"></a>Biztonságimásolat-kezelő kiszolgáló

Ez a tábla a Biztonságimásolat-kezelő kiszolgálók alapvető mezőit tartalmazza.

|Mező  |Adattípus  | Leírás  |
|---------|---------|----------|
|BackupManagementServerName_s     |Szöveg         |A biztonságimásolat-kezelő kiszolgáló neve        |
|AzureBackupAgentVersion_s     |Szöveg         |Az Azure biztonsági másolat ügynökének verziója a biztonsági másolat kezelőkiszolgálóján          |
|BackupManagementServerVersion_s     |Szöveg         |A biztonságimásolat-kezelő kiszolgáló verziója|
|BackupManagementServerOSVersion_s     |Szöveg            |A Biztonságimásolat-kezelő kiszolgáló operációs rendszerének verziója|
|BackupManagementServerType_s     |Szöveg         |A biztonságimásolat-kezelő kiszolgáló típusa MABS, SC DPM|
|BackupManagementServerUniqueId_s     |Szöveg         |A Biztonságimásolat-kezelő kiszolgáló egyedi azonosítására szolgáló mező       |

### <a name="preferredworkloadonvolume"></a>PreferredWorkloadOnVolume

Ez a táblázat a kötethez társított munkaterhelés(ek)et határozza meg.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| StorageUniqueId_s |Szöveg |A tárolási entitás azonosítására használt egyedi azonosító |
| BackupItemType_s |Szöveg |Azok a munkaterhelések, amelyekesetében ez a kötet az előnyben részesített tároló|

### <a name="protectedinstance"></a>Védett példány

Ez a tábla alapvető védett példányokkal kapcsolatos mezőket tartalmaz.

| Mező | Adattípus |Alkalmazható verziók | Leírás |
| --- | --- | --- | --- |
| BackupItemUniqueId_s |Szöveg |v2|Egyedi azonosító a DPM, MABS használatával biztonsági másolat készítésével biztonsági másolat készítő elem azonosítására|
| ProtectedContainerUniqueId_s |Szöveg |v2|Egyedi azonosító, amely a védett tároló azonosítására szolgál a DPM, MABS használatával biztonsági másolatot tartalmazó virtuális gépek kivételével|
| ProtectedInstanceCount_s |Szöveg |v2|A társított biztonsági másolat elemének vagy védett tárolójának védett példányainak száma az adott dátum-időpontban|

### <a name="recoverypoint"></a>RecoveryPoint

Ez a tábla a helyreállítási ponttal kapcsolatos alapvető mezőket tartalmazza.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| BackupItemUniqueId_s |Szöveg |Egyedi azonosító a DPM, MABS használatával biztonsági másolat készítésével biztonsági másolat készítő elem azonosítására|
| OldestRecoveryPointTime_s |Szöveg |A biztonsági másolat elem legrégebbi helyreállítási pontjának dátuma|
| OldestRecoveryPointLocation_s |Szöveg |A biztonsági másolat elem legrégebbi helyreállítási pontjának helye|
| LatestRecoveryPointTime_s |Szöveg |A biztonsági másolat elemutolsó helyreállítási pontjának dátuma|
| LatestRecoveryPointLocation_s |Szöveg |A biztonsági másolat elemének legutóbbi helyreállítási pontjának helye|

## <a name="sample-kusto-queries"></a>Minta Kusto-lekérdezések

Az alábbiakban néhány minta látható, amelyek segítségével lekérdezéseket írhat az Azure Biztonsági mentési adatokra, amelyek az Azure Diagnosztika táblában találhatók:

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

- Az összes sikeres Azure Virtuálisgép-biztonsági mentési feladat

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

- Az összes sikeres SQL-naplóbiztonsági mentési feladat

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

- Az összes sikeres Azure Backup ügynök-feladat

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
    
## <a name="next-steps"></a>További lépések

Miután áttekintette az adatmodellt, [megkezdheti az egyéni lekérdezések létrehozását](../azure-monitor/learn/tutorial-logs-dashboards.md) az Azure Monitor naplóiban a saját irányítópult létrehozásához.
