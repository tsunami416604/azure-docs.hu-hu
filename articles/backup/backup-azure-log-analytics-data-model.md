---
title: Log Analytics-adatmodell az Azure Backuphoz
description: Ez a cikk ismerteti a Log Analytics data modell részletei az Azure biztonsági mentési adatok.
services: backup
author: adiganmsft
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 07/24/2017
ms.author: adigan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f4ecc87a0a7a0c74b02b72164fe129daa6530ea2
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52877581"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Log Analytics-adatmodell az Azure biztonsági mentési adatok
A Log Analytics data model használatával jelentéseket készíthet. Az adatmodellel egyéni lekérdezéseket és irányítópultokat hozhat létre, vagy testre szabhatja az Azure biztonsági mentési adatok, tetszés.

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
| EventName_s |Szöveg |Az esemény neve. Mindig AzureBackupCentralReport |
| BackupItemUniqueId_s |Szöveg |A riasztáshoz tartozó biztonsági mentési elem egyedi azonosítója |
| SchemaVersion_s |Szöveg |A séma, például jelenlegi verziója **V1** |
| State_s |Szöveg |A riasztási, például az aktív, a törölt objektum jelenlegi állapota |
| BackupManagementType_s |Szöveg |Szolgáltató típusa végrehajtásához, amelyhez ez a riasztás tartozik, a biztonsági mentés, például IaaSVM fájlmappa |
| OperationName |Szöveg |Az aktuális művelet, például a riasztás neve |
| Kategória |Szöveg |Diagnosztikai adatok a Log Analytics szolgáltatásba leküldött kategóriáját. Mindig AzureBackupReport |
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
| EventName_s |Szöveg |Az esemény neve. Mindig AzureBackupCentralReport |  
| BackupItemUniqueId_s |Szöveg |A biztonsági mentési elem egyedi azonosítója |
| BackupItemId_s |Szöveg |Biztonsági mentési elem azonosítója |
| BackupItemName_s |Szöveg |Biztonsági másolati elem nevét |
| BackupItemFriendlyName_s |Szöveg |Biztonságimásolat-elem rövid neve |
| BackupItemType_s |Szöveg |Biztonsági mentési elem, például a virtuális gép, fájlmappa típusa |
| ProtectedServerName_s |Szöveg |Védett kiszolgáló melyik biztonsági másolati elem nevét tartozik |
| ProtectionState_s |Szöveg |A biztonsági másolati elem, például a védett, ProtectionStopped aktuális védelmi állapotát |
| SchemaVersion_s |Szöveg |Ha például a séma verziója **V1** |
| State_s |Szöveg |A biztonsági másolati elem objektum, például az aktív, a törölt állapota |
| BackupManagementType_s |Szöveg |Szolgáltató típusát, amelyre a biztonsági mentési cikkhez tartozó biztonsági mentési, például IaaSVM fájlmappa végrehajtásához |
| OperationName |Szöveg |A művelet, például BackupItem neve |
| Kategória |Szöveg |Diagnosztikai adatok a Log Analytics szolgáltatásba leküldött kategóriáját. Mindig AzureBackupReport |
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
| SchemaVersion_s |Szöveg |Ebben a mezőben azt jelzi, hogy a jelenlegi verzióját, a sémát, hogy az **V1** |
| State_s |Szöveg |A biztonsági másolati elem társítás objektum, például az aktív, a törölt aktuális állapotát |
| BackupManagementType_s |Szöveg |Ennek során a biztonsági mentési feladat, például IaaSVM, fájlmappa kiszolgáló a szolgáltató típusát |
| OperationName |Szöveg |Ez a mező képviseli az aktuális művelet – BackupItemAssociation neve |
| Kategória |Szöveg |Ez a mező képviseli a diagnosztikai adatok a Log Analytics szolgáltatásba leküldött kategória, AzureBackupReport |
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

### <a name="job"></a>Feladat
Ez a táblázat részletesen a feladathoz kapcsolódó mezőket.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| EventName_s |Szöveg |Az esemény neve. Mindig AzureBackupCentralReport |
| BackupItemUniqueId_s |Szöveg |A biztonsági mentési elem egyedi azonosítója |
| SchemaVersion_s |Szöveg |Ha például a séma verziója **V1** |
| State_s |Szöveg |A feladatobjektumot, például az aktív, a törölt aktuális állapotát |
| BackupManagementType_s |Szöveg |Ennek során a biztonsági mentési feladat, például IaaSVM, fájlmappa kiszolgáló a szolgáltató típusát |
| OperationName |Szöveg |Ez a mező annak neve, az aktuális művelet - feladat |
| Kategória |Szöveg |Ez a mező képviseli a diagnosztikai adatok a Log Analytics szolgáltatásba leküldött kategória, AzureBackupReport |
| Erőforrás |Szöveg |Ez az erőforrás, amelynek adatokat gyűjteni, azt mutatja, hogy a helyreállítási tár neve |
| ProtectedServerUniqueId_s |Szöveg |A védett kiszolgáló egyedi azonosítója a feladathoz hozzárendelt |
| VaultUniqueId_s |Szöveg |A védett tároló egyedi azonosítója |
| JobOperation_s |Szöveg |A művelet, amelynek feladat futtatása például biztonsági mentés, visszaállítás, a Backup konfigurálása |
| JobStatus_s |Szöveg |A befejezett feladat, például befejezve, sikertelen állapota |
| JobFailureCode_s |Szöveg |Sikertelen hibakód karakterláncát miatt, amely feladat hiba történt |
| JobStartDateTime_s |Dátum/idő |Dátum és idő elindított futó feladat |
| BackupStorageDestination_s |Szöveg |A biztonsági mentési tár, például felhőben lemez rendeltetési  |
| JobDurationInSecs_s | Szám |Feladatok teljes időtartama (másodpercben) |
| DataTransferredInMB_s | Szám |A feladat MB-ban átvitt adatok|
| JobUniqueId_g |Szöveg |A feladat azonosításához használatos egyedi azonosító |
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
| SchemaVersion_s |Szöveg |Ebben a mezőben azt jelzi, hogy a jelenlegi verzióját, a sémát, hogy az **V1** |
| State_s |Szöveg |A csoportházirend-objektum, például az aktív, a törölt aktuális állapotát |
| BackupManagementType_s |Szöveg |Ennek során a biztonsági mentési feladat, például IaaSVM, fájlmappa kiszolgáló a szolgáltató típusát |
| OperationName |Szöveg |Ez a mező képviseli az aktuális művelet - házirend neve |
| Kategória |Szöveg |Ez a mező képviseli a diagnosztikai adatok a Log Analytics szolgáltatásba leküldött kategória, AzureBackupReport |
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
| SchemaVersion_s |Szöveg |Ebben a mezőben azt jelzi, hogy a jelenlegi verzióját, a sémát, hogy az **V1** |
| State_s |Szöveg |A csoportházirend-objektum, például az aktív, a törölt aktuális állapotát |
| BackupManagementType_s |Szöveg |Ennek során a biztonsági mentési feladat, például IaaSVM, fájlmappa kiszolgáló a szolgáltató típusát |
| OperationName |Szöveg |Ez a mező képviseli az aktuális művelet – PolicyAssociation neve |
| Kategória |Szöveg |Ez a mező képviseli a diagnosztikai adatok a Log Analytics szolgáltatásba leküldött kategória, AzureBackupReport |
| Erőforrás |Szöveg |Ez az erőforrás, amelynek adatokat gyűjteni, azt mutatja, hogy a helyreállítási tár neve |
| PolicyUniqueId_g |Szöveg |Azonosíthatja a szabályzat egyedi azonosítója |
| VaultUniqueId_s |Szöveg |A tárolóban, amelyhez ez a szabályzat tartozik, az egyedi azonosítója |
| SourceSystem |Szöveg |A forrásrendszerben az aktuális adatok – Azure |
| ResourceId |Szöveg |A begyűjtött adatok erőforrás-azonosítója. Ha például a helyreállítási tár az erőforrás-azonosító |
| SubscriptionId |Szöveg |Az erőforrás (például az előfizetés-azonosító Recovery Services-tároló), amelynek az adatgyűjtés történik |
| ResourceGroup |Szöveg |Az erőforrás (például erőforráscsoport Recovery Services-tároló), amelynek az adatgyűjtés történik |
| ResourceProvider |Szöveg |Erőforrás-szolgáltató, amelynek az adatgyűjtés történik. Ha például a Microsoft.recoveryservices szolgáltatónál |
| ResourceType |Szöveg |Erőforrás típusa, amelynek az adatgyűjtés történik. Ha például a tárolók |

### <a name="protectedserver"></a>ProtectedServer
Ez a táblázat részletesen a védett kiszolgáló kapcsolatos mezőket.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| EventName_s |Szöveg |Ez a mező képviseli az esemény nevét, a rendszer mindig AzureBackupCentralReport |
| ProtectedServerName_s |Szöveg |Védett kiszolgáló neve |
| SchemaVersion_s |Szöveg |Ebben a mezőben azt jelzi, hogy a jelenlegi verzióját, a sémát, hogy az **V1** |
| State_s |Szöveg |A védett kiszolgáló objektum, például az aktív, a törölt aktuális állapotát |
| BackupManagementType_s |Szöveg |Ennek során a biztonsági mentési feladat, például IaaSVM, fájlmappa kiszolgáló a szolgáltató típusát |
| OperationName |Szöveg |Ez a mező képviseli az aktuális művelet - ProtectedServer neve |
| Kategória |Szöveg |Ez a mező képviseli a diagnosztikai adatok a Log Analytics szolgáltatásba leküldött kategória, AzureBackupReport |
| Erőforrás |Szöveg |Ez az erőforrás, amelynek adatokat gyűjteni, azt mutatja, hogy a helyreállítási tár neve |
| ProtectedServerUniqueId_s |Szöveg |A védett kiszolgáló egyedi azonosítója |
| RegisteredContainerId_s |Szöveg |Regisztrálva a biztonsági mentési tároló azonosítója |
| ProtectedServerType_s |Szöveg |Védett kiszolgálók, például Windows típusa |
| ProtectedServerFriendlyName_s |Szöveg |Védett kiszolgáló rövid neve |
| AzureBackupAgentVersion_s |Szöveg |Biztonsági másolat verzióját az ügynök verziószáma |
| SourceSystem |Szöveg |A forrásrendszerben az aktuális adatok – Azure |
| ResourceId |Szöveg |A begyűjtött adatok erőforrás-azonosítója. Ha például a helyreállítási tár az erőforrás-azonosító |
| SubscriptionId |Szöveg |Az erőforrás (például az előfizetés-azonosító Recovery Services-tároló), amelynek az adatgyűjtés történik |
| ResourceGroup |Szöveg |Az erőforrás (például erőforráscsoport Recovery Services-tároló), amelynek az adatgyűjtés történik |
| ResourceProvider |Szöveg |Erőforrás-szolgáltató, amelynek az adatgyűjtés történik. Ha például a Microsoft.recoveryservices szolgáltatónál |
| ResourceType |Szöveg |Erőforrás típusa, amelynek az adatgyűjtés történik. Ha például a tárolók |

### <a name="protectedserverassociation"></a>ProtectedServerAssociation
Ez a táblázat a védett kiszolgáló társítások más entitásokkal részletesen ismerteti.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| EventName_s |Szöveg |Ez a mező képviseli az esemény nevét, a rendszer mindig AzureBackupCentralReport |
| SchemaVersion_s |Szöveg |Ebben a mezőben azt jelzi, hogy a jelenlegi verzióját, a sémát, hogy az **V1** |
| State_s |Szöveg |A védett kiszolgáló társítás objektum, például az aktív, a törölt aktuális állapotát |
| BackupManagementType_s |Szöveg |Ennek során a biztonsági mentési feladat, például IaaSVM, fájlmappa kiszolgáló a szolgáltató típusát |
| OperationName |Szöveg |Ez a mező képviseli az aktuális művelet – ProtectedServerAssociation neve |
| Kategória |Szöveg |Ez a mező képviseli a diagnosztikai adatok a Log Analytics szolgáltatásba leküldött kategória, AzureBackupReport |
| Erőforrás |Szöveg |Ez az erőforrás, amelynek adatokat gyűjteni, azt mutatja, hogy a helyreállítási tár neve |
| ProtectedServerUniqueId_s |Szöveg |A védett kiszolgáló egyedi azonosítója |
| VaultUniqueId_s |Szöveg |A tárolóban, amelyhez a védett kiszolgáló tartozik egyedi azonosítója |
| SourceSystem |Szöveg |A forrásrendszerben az aktuális adatok – Azure |
| ResourceId |Szöveg |A begyűjtött adatok erőforrás-azonosítója. Ha például a helyreállítási tár az erőforrás-azonosító |
| SubscriptionId |Szöveg |Az erőforrás (például az előfizetés-azonosító Recovery Services-tároló), amelynek az adatgyűjtés történik |
| ResourceGroup |Szöveg |Az erőforrás (például erőforráscsoport Recovery Services-tároló), amelynek az adatgyűjtés történik |
| ResourceProvider |Szöveg |Erőforrás-szolgáltató, amelynek az adatgyűjtés történik. Ha például a Microsoft.recoveryservices szolgáltatónál |
| ResourceType |Szöveg |Erőforrás típusa, amelynek az adatgyűjtés történik. Ha például a tárolók |

### <a name="storage"></a>Storage
A table storage szolgáltatással kapcsolatos mezők részletesen ismerteti.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| CloudStorageInBytes_s |Tizedes tört szám |Felhőalapú biztonsági mentési tár biztonsági mentések, számított által használt legújabb érték alapján |
| ProtectedInstances_s |Tizedes tört szám |Előtérbeli tár számlázási, számított alapján legújabb érték kiszámításához használt védett példányok száma |
| EventName_s |Szöveg |Ez a mező képviseli az esemény nevét, a rendszer mindig AzureBackupCentralReport |
| SchemaVersion_s |Szöveg |Ebben a mezőben azt jelzi, hogy a jelenlegi verzióját, a sémát, hogy az **V1** |
| State_s |Szöveg |A tárolási objektum, például az aktív, a törölt aktuális állapotát |
| BackupManagementType_s |Szöveg |Ennek során a biztonsági mentési feladat, például IaaSVM, fájlmappa kiszolgáló a szolgáltató típusát |
| OperationName |Szöveg |Ez a mező képviseli az aktuális művelet – a tároló neve |
| Kategória |Szöveg |Ez a mező képviseli a diagnosztikai adatok a Log Analytics szolgáltatásba leküldött kategória, AzureBackupReport |
| Erőforrás |Szöveg |Ez az erőforrás, amelynek adatokat gyűjteni, azt mutatja, hogy a helyreállítási tár neve |
| ProtectedServerUniqueId_s |Szöveg |A védett kiszolgálón, amelyhez tárolási számította egyedi azonosítója |
| VaultUniqueId_s |Szöveg |Egyedi azonosítója a Storage-tároló kiszámítása |
| SourceSystem |Szöveg |A forrásrendszerben az aktuális adatok – Azure |
| ResourceId |Szöveg |A begyűjtött adatok erőforrás-azonosítója. Ha például a helyreállítási tár az erőforrás-azonosító |
| SubscriptionId |Szöveg |Az erőforrás (például az előfizetés-azonosító Recovery Services-tároló), amelynek az adatgyűjtés történik |
| ResourceGroup |Szöveg |Az erőforrás (például erőforráscsoport Recovery Services-tároló), amelynek az adatgyűjtés történik |
| ResourceProvider |Szöveg |Erőforrás-szolgáltató, amelynek az adatgyűjtés történik. Ha például a Microsoft.recoveryservices szolgáltatónál |
| ResourceType |Szöveg |Erőforrás típusa, amelynek az adatgyűjtés történik. Ha például a tárolók |

### <a name="vault"></a>Tároló
Ez a táblázat részletesen tároló kapcsolatos mezőket.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| EventName_s |Szöveg |Ez a mező képviseli az esemény nevét, a rendszer mindig AzureBackupCentralReport |
| SchemaVersion_s |Szöveg |Ebben a mezőben azt jelzi, hogy a jelenlegi verzióját, a sémát, hogy az **V1** |
| State_s |Szöveg |A tár objektum, például az aktív, a törölt aktuális állapotát |
| OperationName |Szöveg |Ez a mező képviseli az aktuális művelet - tár neve |
| Kategória |Szöveg |Ez a mező képviseli a diagnosztikai adatok a Log Analytics szolgáltatásba leküldött kategória, AzureBackupReport |
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

## <a name="next-steps"></a>További lépések
Megkezdése után tekintse át az adatmodell az Azure Backup-jelentések létrehozása, [irányítópult létrehozása](../azure-monitor/platform/dashboards.md) a Log Analyticsben.
