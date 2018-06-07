---
title: Log Analytics-adatmodell az Azure Backuphoz
description: Ez a cikk beszél Naplóelemzési az modell adatait az Azure biztonságimásolat-adatok.
services: backup
author: JPallavi
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 07/24/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5a2ba1d523e7a6364420302eee095f24fd08eadb
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605773"
---
# <a name="log-analytics-data-model-for-azure-backup-data"></a>Napló Analytics adatmodell Azure biztonságimásolat-adatok
Ez a cikk ismerteti a tárházat a jelentésadatok szolgáltatáshoz használt adatmodell. A adatok modell segítségével hozhat létre egyéni lekérdezéseket, irányítópultok, és a Naplóelemzési felhasználható az. 

## <a name="using-azure-backup-data-model"></a>Azure biztonsági mentési adatok modell segítségével
Az adatmodell részeként a következő mezők hozhat létre a látványelemek, egyéni lekérdezések és irányítópult a követelményeknek.

### <a name="alert"></a>Riasztás
Ez a táblázat ismerteti a riasztási kapcsolódó mezők.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| AlertUniqueId_s |Szöveg |A generált riasztások egyedi azonosítója |
| AlertType_s |Szöveg |A generált riasztások, például biztonsági mentés típusa |
| AlertStatus_s |Szöveg |A riasztásra, például aktív állapota |
| AlertOccurenceDateTime_s |Dátum/idő |Dátum és a riasztás létrehozásának időpontja |
| AlertSeverity_s |Szöveg |A riasztás súlyossága, például kritikus |
| EventName_s |Szöveg |Ez a mező képviseli az esemény nevét, a rendszer mindig AzureBackupCentralReport |
| BackupItemUniqueId_s |Szöveg |A biztonsági mentési, amelyhez ez a riasztás tartozik elem egyedi azonosítója |
| SchemaVersion_s |Szöveg |Ebben a mezőben a séma jelenlegi verziója azt jelzi, hogy az **1-es verzió** |
| State_s |Szöveg |A riasztási, például az aktív, a törölt objektum jelenlegi állapota |
| BackupManagementType_s |Szöveg |A biztonsági mentés, például IaaSVM FileFolder, amelyhez ez a riasztás tartozik végrehajtásához szolgáltató típusa |
| OperationName |Szöveg |Ez a mező képviseli az aktuális művelet - a riasztás neve |
| Kategória |Szöveg |Ez a mező képviseli kategória Naplóelemzési leküldve diagnosztikai adatok, a AzureBackupReport |
| Erőforrás |Szöveg |Ez az erőforrás, amelynek gyűjtenek adatokat, azt mutatja, hogy Recovery Services-tároló neve |
| ProtectedServerUniqueId_s |Szöveg |A védett az egyedi azonosítója, amelyhez ez a riasztás tartozik |
| VaultUniqueId_s |Szöveg |A védett az egyedi azonosítója, amelyhez ez a riasztás tartozik |
| SourceSystem |Szöveg |A forrásrendszerben az aktuális adatok – Azure |
| ResourceId |Szöveg |Ez a mező jelöli erőforrás-azonosító, amelynek gyűjtenek adatokat, azt illusztrálja a Recovery Services-tároló erőforrás-azonosító |
| SubscriptionId |Szöveg |Ez a mező képviseli az erőforrás (RS tároló), amelynek gyűjtenek adatokat előfizetés-azonosító |
| ResourceGroup |Szöveg |Ez a mező képviseli az erőforrás (RS tároló), amelynek gyűjtenek adatokat erőforráscsoport |
| ResourceProvider |Szöveg |Ez a mező képviseli az erőforrás-szolgáltató, amelynek folyik adatgyűjtés - Microsoft.RecoveryServices |
| ResourceType |Szöveg |Ez a mező képviseli az erőforrást, amelyre folyik adatgyűjtés - tárolók típusú |

### <a name="backupitem"></a>BackupItem
Ez a táblázat ismerteti a biztonsági mentési elem kapcsolatos mezők.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| EventName_s |Szöveg |Ez a mező képviseli az esemény nevét, a rendszer mindig AzureBackupCentralReport |  
| BackupItemUniqueId_s |Szöveg |A biztonsági mentési elem egyedi azonosítója |
| BackupItemId_s |Szöveg |Biztonsági mentési elem azonosítója |
| BackupItemName_s |Szöveg |Biztonsági mentési elem neve |
| BackupItemFriendlyName_s |Szöveg |Biztonsági mentési elem rövid neve |
| BackupItemType_s |Szöveg |Biztonsági mentési elem, például a virtuális gép, FileFolder típusa |
| ProtectedServerName_s |Szöveg |Tartozik, mely biztonsági mentési elemet a védett kiszolgáló neve |
| ProtectionState_s |Szöveg |A biztonsági mentési elem, például a védett, a ProtectionStopped aktuális védelmi állapot |
| SchemaVersion_s |Szöveg |Ebben a mezőben a séma jelenlegi verziója azt jelzi, hogy az **1-es verzió** |
| State_s |Szöveg |A biztonsági mentési elem objektum, például az aktív, a törölt aktuális állapotát |
| BackupManagementType_s |Szöveg |A biztonsági mentés, például IaaSVM FileFolder, amelyre a biztonsági mentési cikkhez tartozó végrehajtásához szolgáltató típusa |
| OperationName |Szöveg |Ez a mező képviseli az aktuális művelet - BackupItem neve |
| Kategória |Szöveg |Ez a mező képviseli kategória Naplóelemzési leküldve diagnosztikai adatok, a AzureBackupReport |
| Erőforrás |Szöveg |Ez az erőforrás, amelynek gyűjtenek adatokat, azt mutatja, hogy Recovery Services-tároló neve |
| SourceSystem |Szöveg |A forrásrendszerben az aktuális adatok – Azure |
| ResourceId |Szöveg |Ez a mező jelöli erőforrás-azonosító, amelynek gyűjtenek adatokat, azt illusztrálja a Recovery Services-tároló erőforrás-azonosító |
| SubscriptionId |Szöveg |Ez a mező képviseli az erőforrás (RS tároló), amelynek gyűjtenek adatokat előfizetés-azonosító |
| ResourceGroup |Szöveg |Ez a mező képviseli az erőforrás (RS tároló), amelynek gyűjtenek adatokat erőforráscsoport |
| ResourceProvider |Szöveg |Ez a mező képviseli az erőforrás-szolgáltató, amelynek folyik adatgyűjtés - Microsoft.RecoveryServices |
| ResourceType |Szöveg |Ez a mező képviseli az erőforrást, amelyre folyik adatgyűjtés - tárolók típusú |

### <a name="backupitemassociation"></a>BackupItemAssociation
A táblázat ismerteti a különböző entitásokkal biztonsági mentési elem társításokat.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| EventName_s |Szöveg |Ez a mező képviseli az esemény nevét, a rendszer mindig AzureBackupCentralReport |  
| BackupItemUniqueId_s |Szöveg |A biztonsági mentési elem egyedi azonosítója |
| SchemaVersion_s |Szöveg |Ebben a mezőben a séma jelenlegi verziója azt jelzi, hogy az **1-es verzió** |
| State_s |Szöveg |A biztonsági mentési elem társítás objektum, például az aktív, a törölt aktuális állapotát |
| BackupManagementType_s |Szöveg |A biztonsági mentés, például IaaSVM FileFolder, amelyre a biztonsági mentési cikkhez tartozó végrehajtásához szolgáltató típusa |
| OperationName |Szöveg |Ez a mező képviseli az aktuális művelet - BackupItemAssociation neve |
| Kategória |Szöveg |Ez a mező képviseli kategória Naplóelemzési leküldve diagnosztikai adatok, a AzureBackupReport |
| Erőforrás |Szöveg |Ez az erőforrás, amelynek gyűjtenek adatokat, azt mutatja, hogy Recovery Services-tároló neve |
| PolicyUniqueId_g |Szöveg |A házirend, mely biztonsági mentési elem társítva-egyedi azonosítója |
| ProtectedServerUniqueId_s |Szöveg |A védett kiszolgáló, amelyhez a biztonsági mentési cikkhez tartozó egyedi azonosító |
| VaultUniqueId_s |Szöveg |A tároló, amelyre a biztonsági mentési cikkhez tartozó egyedi azonosító |
| SourceSystem |Szöveg |A forrásrendszerben az aktuális adatok – Azure |
| ResourceId |Szöveg |Ez a mező jelöli erőforrás-azonosító, amelynek gyűjtenek adatokat, azt illusztrálja a Recovery Services-tároló erőforrás-azonosító |
| SubscriptionId |Szöveg |Ez a mező képviseli az erőforrás (RS tároló), amelynek gyűjtenek adatokat előfizetés-azonosító |
| ResourceGroup |Szöveg |Ez a mező képviseli az erőforrás (RS tároló), amelynek gyűjtenek adatokat erőforráscsoport |
| ResourceProvider |Szöveg |Ez a mező képviseli az erőforrás-szolgáltató, amelynek folyik adatgyűjtés - Microsoft.RecoveryServices |
| ResourceType |Szöveg |Ez a mező képviseli az erőforrást, amelyre folyik adatgyűjtés - tárolók típusú |

### <a name="job"></a>Feladat
Ez a táblázat ismerteti a feladathoz kapcsolódó mezőket.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| EventName_s |Szöveg |Ez a mező képviseli az esemény nevét, a rendszer mindig AzureBackupCentralReport |
| BackupItemUniqueId_s |Szöveg |A biztonsági mentési, amelyhez ez a feladat tartozik elem egyedi azonosítója |
| SchemaVersion_s |Szöveg |Ebben a mezőben a séma jelenlegi verziója azt jelzi, hogy az **1-es verzió** |
| State_s |Szöveg |A feladat objektum, például az aktív, a törölt aktuális állapotát |
| BackupManagementType_s |Szöveg |A biztonsági mentés, például IaaSVM FileFolder, amelyhez ez a feladat tartozik végrehajtásához szolgáltató típusa |
| OperationName |Szöveg |Ez a mező képviseli az aktuális művelet - name feladat |
| Kategória |Szöveg |Ez a mező képviseli kategória Naplóelemzési leküldve diagnosztikai adatok, a AzureBackupReport |
| Erőforrás |Szöveg |Ez az erőforrás, amelynek gyűjtenek adatokat, azt mutatja, hogy Recovery Services-tároló neve |
| ProtectedServerUniqueId_s |Szöveg |A védett az egyedi azonosítója, amelyhez ez a feladat tartozik |
| VaultUniqueId_s |Szöveg |A védett az egyedi azonosítója, amelyhez ez a feladat tartozik |
| JobOperation_s |Szöveg |A művelet, amelynek feladat futtatásakor például biztonsági mentése, visszaállítása, Backup konfigurálása |
| JobStatus_s |Szöveg |A befejezett feladatának állapotát, például befejezve, sikertelen |
| JobFailureCode_s |Szöveg |Hiba a kód karakterlánc miatt, amelyet feladat hiba történt |
| JobStartDateTime_s |Dátum/idő |Dátum és idő elindított futó feladat |
| BackupStorageDestination_s |Szöveg |Biztonsági másolatok tárolásának, például felhő, lemez és  |
| JobDurationInSecs_s | Szám |Feladatok teljes időtartama másodpercben |
| DataTransferredInMB_s | Szám |A feladat MB-ban átvitt adatok|
| JobUniqueId_g |Szöveg |A feladat azonosításához egyedi azonosítója |
| SourceSystem |Szöveg |A forrásrendszerben az aktuális adatok – Azure |
| ResourceId |Szöveg |Ez a mező jelöli erőforrás-azonosító, amelynek gyűjtenek adatokat, azt illusztrálja a Recovery Services-tároló erőforrás-azonosító |
| SubscriptionId |Szöveg |Ez a mező képviseli az erőforrás (RS tároló), amelynek gyűjtenek adatokat előfizetés-azonosító |
| ResourceGroup |Szöveg |Ez a mező képviseli az erőforrás (RS tároló), amelynek gyűjtenek adatokat erőforráscsoport |
| ResourceProvider |Szöveg |Ez a mező képviseli az erőforrás-szolgáltató, amelynek folyik adatgyűjtés - Microsoft.RecoveryServices |
| ResourceType |Szöveg |Ez a mező képviseli az erőforrást, amelyre folyik adatgyűjtés - tárolók típusú |

### <a name="policy"></a>Szabályzat
Ez a táblázat-házirendekkel kapcsolatos mezők ismerteti.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| EventName_s |Szöveg |Ez a mező képviseli az esemény nevét, a rendszer mindig AzureBackupCentralReport |
| SchemaVersion_s |Szöveg |Ebben a mezőben a séma jelenlegi verziója azt jelzi, hogy az **1-es verzió** |
| State_s |Szöveg |A csoportházirend-objektum, például az aktív, a törölt aktuális állapota |
| BackupManagementType_s |Szöveg |A biztonsági mentés, például IaaSVM FileFolder, amelyhez ez a házirend tartozik végrehajtásához szolgáltató típusa |
| OperationName |Szöveg |Ez a mező képviseli az aktuális művelet - házirend neve |
| Kategória |Szöveg |Ez a mező képviseli kategória Naplóelemzési leküldve diagnosztikai adatok, a AzureBackupReport |
| Erőforrás |Szöveg |Ez az erőforrás, amelynek gyűjtenek adatokat, azt mutatja, hogy Recovery Services-tároló neve |
| PolicyUniqueId_g |Szöveg |A házirend-egyedi azonosítója |
| PolicyName_s |Szöveg |A megadott házirend neve |
| BackupFrequency_s |Szöveg |A gyakoriság, amellyel biztonsági mentések futnak, például, naponta, hetente |
| BackupTimes_s |Szöveg |Dátum és idő, amikor a biztonsági mentés van ütemezve |
| BackupDaysOfTheWeek_s |Szöveg |Ha biztonsági mentések ütemezett napjai |
| RetentionDuration_s |Egész szám |Konfigurált biztonsági mentések megőrzési időtartama |
| DailyRetentionDuration_s |Egész szám |Teljes megőrzés időtartama napokban megadva beállított biztonsági mentés |
| DailyRetentionTimes_s |Szöveg |Dátum és idő, amikor napi megőrzési konfigurálása |
| WeeklyRetentionDuration_s |Egész szám |A konfigurált biztonsági mentésekhez hét teljes heti megőrzési időtartama |
| WeeklyRetentionTimes_s |Szöveg |Dátum és idő, ha a heti megőrzési van konfigurálva |
| WeeklyRetentionDaysOfTheWeek_s |Szöveg |Heti megőrzési napokat a hét kijelölt |
| MonthlyRetentionDuration_s |Egész szám |A konfigurált biztonsági mentésekhez hónapban teljes megőrzési időtartama |
| MonthlyRetentionTimes_s |Szöveg |Dátum és idő, amikor a havi megőrzési van konfigurálva |
| MonthlyRetentionFormat_s |Szöveg |Havi megőrzési, például meghatározásával, hetente alapján hét nap napi konfigurációjának típusa |
| MonthlyRetentionDaysOfTheWeek_s |Szöveg |Havi megőrzési napokat a hét kijelölt |
| MonthlyRetentionWeeksOfTheMonth_s |Szöveg |Hét a hónapon belül, ha havi megőrzési van konfigurálva, például First, Last stb. |
| YearlyRetentionDuration_s |Egész szám |A konfigurált biztonsági mentésekhez évben teljes megőrzési időtartama |
| YearlyRetentionTimes_s |Szöveg |Dátum és idő, amikor éves megőrzési van konfigurálva |
| YearlyRetentionMonthsOfTheYear_s |Szöveg |Az év hónapja kiválasztott éves megőrzési |
| YearlyRetentionFormat_s |Szöveg |A konfigurációban az éves megőrzési, például napi meghatározásával, hetente alapján hét nap |
| YearlyRetentionDaysOfTheMonth_s |Szöveg |A hónap éves megőrzési a kijelölt dátumok |
| SourceSystem |Szöveg |A forrásrendszerben az aktuális adatok – Azure |
| ResourceId |Szöveg |Ez a mező jelöli erőforrás-azonosító, amelynek gyűjtenek adatokat, azt illusztrálja a Recovery Services-tároló erőforrás-azonosító |
| SubscriptionId |Szöveg |Ez a mező képviseli az erőforrás (RS tároló), amelynek gyűjtenek adatokat előfizetés-azonosító |
| ResourceGroup |Szöveg |Ez a mező képviseli az erőforrás (RS tároló), amelynek gyűjtenek adatokat erőforráscsoport |
| ResourceProvider |Szöveg |Ez a mező képviseli az erőforrás-szolgáltató, amelynek folyik adatgyűjtés - Microsoft.RecoveryServices |
| ResourceType |Szöveg |Ez a mező képviseli az erőforrást, amelyre folyik adatgyűjtés - tárolók típusú |

### <a name="policyassociation"></a>PolicyAssociation
Ez a táblázat ismerteti a különböző entitásokkal házirendtársításait részleteit.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| EventName_s |Szöveg |Ez a mező képviseli az esemény nevét, a rendszer mindig AzureBackupCentralReport |
| SchemaVersion_s |Szöveg |Ebben a mezőben a séma jelenlegi verziója azt jelzi, hogy az **1-es verzió** |
| State_s |Szöveg |A csoportházirend-objektum, például az aktív, a törölt aktuális állapota |
| BackupManagementType_s |Szöveg |A biztonsági mentés például IaaSVM, amelybe ez a házirend tartozik FileFolder szolgáltató típusa |
| OperationName |Szöveg |Ez a mező képviseli az aktuális művelet - PolicyAssociation neve |
| Kategória |Szöveg |Ez a mező képviseli kategória Naplóelemzési leküldve diagnosztikai adatok, a AzureBackupReport |
| Erőforrás |Szöveg |Ez az erőforrás, amelynek gyűjtenek adatokat, azt mutatja, hogy Recovery Services-tároló neve |
| PolicyUniqueId_g |Szöveg |A házirend-egyedi azonosítója |
| VaultUniqueId_s |Szöveg |A tárolóhoz, amelybe ez a házirend tartozik egyedi azonosítója |
| SourceSystem |Szöveg |A forrásrendszerben az aktuális adatok – Azure |
| ResourceId |Szöveg |Ez a mező jelöli erőforrás-azonosító, amelynek gyűjtenek adatokat, azt illusztrálja a Recovery Services-tároló erőforrás-azonosító |
| SubscriptionId |Szöveg |Ez a mező képviseli az erőforrás (RS tároló), amelynek gyűjtenek adatokat előfizetés-azonosító |
| ResourceGroup |Szöveg |Ez a mező képviseli az erőforrás (RS tároló), amelynek gyűjtenek adatokat erőforráscsoport |
| ResourceProvider |Szöveg |Ez a mező képviseli az erőforrás-szolgáltató, amelynek folyik adatgyűjtés - Microsoft.RecoveryServices |
| ResourceType |Szöveg |Ez a mező képviseli az erőforrást, amelyre folyik adatgyűjtés - tárolók típusú |

### <a name="protectedserver"></a>ProtectedServer
Ez a táblázat ismerteti a védett kiszolgálóval kapcsolatos mezők.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| EventName_s |Szöveg |Ez a mező képviseli az esemény nevét, a rendszer mindig AzureBackupCentralReport |
| ProtectedServerName_s |Szöveg |Védett kiszolgáló neve |
| SchemaVersion_s |Szöveg |Ebben a mezőben a séma jelenlegi verziója azt jelzi, hogy az **1-es verzió** |
| State_s |Szöveg |A védett kiszolgáló objektum, például az aktív, a törölt aktuális állapota |
| BackupManagementType_s |Szöveg |A biztonsági mentés például IaaSVM, amelybe a védett kiszolgáló tartozik FileFolder szolgáltató típusa |
| OperationName |Szöveg |Ez a mező képviseli az aktuális művelet - ProtectedServer neve |
| Kategória |Szöveg |Ez a mező képviseli kategória Naplóelemzési leküldve diagnosztikai adatok, a AzureBackupReport |
| Erőforrás |Szöveg |Ez az erőforrás, amelynek gyűjtenek adatokat, azt mutatja, hogy Recovery Services-tároló neve |
| ProtectedServerUniqueId_s |Szöveg |A védett kiszolgáló egyedi azonosítója |
| RegisteredContainerId_s |Szöveg |A biztonsági mentéshez regisztrált tároló azonosító |
| ProtectedServerType_s |Szöveg |Például Windows mentésbe a védett kiszolgáló típusa |
| ProtectedServerFriendlyName_s |Szöveg |Védett kiszolgáló rövid neve |
| AzureBackupAgentVersion_s |Szöveg |Biztonsági másolat verzióját ügynök verziószáma |
| SourceSystem |Szöveg |A forrásrendszerben az aktuális adatok – Azure |
| ResourceId |Szöveg |Ez a mező jelöli erőforrás-azonosító, amelynek gyűjtenek adatokat, azt illusztrálja a Recovery Services-tároló erőforrás-azonosító |
| SubscriptionId |Szöveg |Ez a mező képviseli az erőforrás (RS tároló), amelynek gyűjtenek adatokat előfizetés-azonosító |
| ResourceGroup |Szöveg |Ez a mező képviseli az erőforrás (RS tároló), amelynek gyűjtenek adatokat erőforráscsoport |
| ResourceProvider |Szöveg |Ez a mező képviseli az erőforrás-szolgáltató, amelynek folyik adatgyűjtés - Microsoft.RecoveryServices |
| ResourceType |Szöveg |Ez a mező képviseli az erőforrást, amelyre folyik adatgyűjtés - tárolók típusú |

### <a name="protectedserverassociation"></a>ProtectedServerAssociation
A táblázat tartalmazza a védett kiszolgáló társítások más entitásokkal adatait.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| EventName_s |Szöveg |Ez a mező képviseli az esemény nevét, a rendszer mindig AzureBackupCentralReport |
| SchemaVersion_s |Szöveg |Ebben a mezőben a séma jelenlegi verziója azt jelzi, hogy az **1-es verzió** |
| State_s |Szöveg |A védett kiszolgáló társítás objektum, például az aktív, a törölt aktuális állapotát |
| BackupManagementType_s |Szöveg |A biztonsági mentés, például IaaSVM FileFolder, amelyhez a védett kiszolgáló tartozik végrehajtásához szolgáltató típusa |
| OperationName |Szöveg |Ez a mező képviseli az aktuális művelet - ProtectedServerAssociation neve |
| Kategória |Szöveg |Ez a mező képviseli kategória Naplóelemzési leküldve diagnosztikai adatok, a AzureBackupReport |
| Erőforrás |Szöveg |Ez az erőforrás, amelynek gyűjtenek adatokat, azt mutatja, hogy Recovery Services-tároló neve |
| ProtectedServerUniqueId_s |Szöveg |A védett kiszolgáló egyedi azonosítója |
| VaultUniqueId_s |Szöveg |A tárolóhoz, amelybe a védett kiszolgáló tartozik egyedi azonosítója |
| SourceSystem |Szöveg |A forrásrendszerben az aktuális adatok – Azure |
| ResourceId |Szöveg |Ez a mező jelöli erőforrás-azonosító, amelynek gyűjtenek adatokat, azt illusztrálja a Recovery Services-tároló erőforrás-azonosító |
| SubscriptionId |Szöveg |Ez a mező képviseli az erőforrás (RS tároló), amelynek gyűjtenek adatokat előfizetés-azonosító |
| ResourceGroup |Szöveg |Ez a mező képviseli az erőforrás (RS tároló), amelynek gyűjtenek adatokat erőforráscsoport |
| ResourceProvider |Szöveg |Ez a mező képviseli az erőforrás-szolgáltató, amelynek folyik adatgyűjtés - Microsoft.RecoveryServices |
| ResourceType |Szöveg |Ez a mező képviseli az erőforrást, amelyre folyik adatgyűjtés - tárolók típusú |

### <a name="storage"></a>Storage
Ez a táblázat ismerteti a tárolással kapcsolatos mezők.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| CloudStorageInBytes_s |Egész szám |Biztonsági mentések, számított által használt biztonsági mentési felhőtárhelyére legújabb érték alapján. |
| ProtectedInstances_s |Egész szám |Előtérbeli tárolási számlázási, számított alapján legújabb értékének kiszámítására használt védett példányok száma |
| EventName_s |Szöveg |Ez a mező képviseli az esemény nevét, a rendszer mindig AzureBackupCentralReport |
| SchemaVersion_s |Szöveg |Ebben a mezőben a séma jelenlegi verziója azt jelzi, hogy az **1-es verzió** |
| State_s |Szöveg |A tárolási objektum, például az aktív, a törölt aktuális állapota |
| BackupManagementType_s |Szöveg |A biztonsági mentés, például IaaSVM FileFolder, amelyhez ez a tároló tartozik végrehajtásához szolgáltató típusa |
| OperationName |Szöveg |Ez a mező képviseli az aktuális művelet - tároló neve |
| Kategória |Szöveg |Ez a mező képviseli kategória Naplóelemzési leküldve diagnosztikai adatok, a AzureBackupReport |
| Erőforrás |Szöveg |Ez az erőforrás, amelynek gyűjtenek adatokat, azt mutatja, hogy Recovery Services-tároló neve |
| ProtectedServerUniqueId_s |Szöveg |A védett kiszolgáló, amelyhez tároló kiszámítása egyedi azonosítója |
| VaultUniqueId_s |Szöveg |Egyedi azonosító tárolási tároló kiszámítása |
| SourceSystem |Szöveg |A forrásrendszerben az aktuális adatok – Azure |
| ResourceId |Szöveg |Ez a mező jelöli erőforrás-azonosító, amelynek gyűjtenek adatokat, azt illusztrálja a Recovery Services-tároló erőforrás-azonosító |
| SubscriptionId |Szöveg |Ez a mező képviseli az erőforrás (RS tároló), amelynek gyűjtenek adatokat előfizetés-azonosító |
| ResourceGroup |Szöveg |Ez a mező képviseli az erőforrás (RS tároló), amelynek gyűjtenek adatokat erőforráscsoport |
| ResourceProvider |Szöveg |Ez a mező képviseli az erőforrás-szolgáltató, amelynek folyik adatgyűjtés - Microsoft.RecoveryServices |
| ResourceType |Szöveg |Ez a mező representse írja be a tárolók, amelynek folyik adatgyűjtés - erőforrás |

### <a name="vault"></a>Tároló
Ez a táblázat ismerteti a tárolóval kapcsolatos mezők.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| EventName_s |Szöveg |Ez a mező képviseli az esemény nevét, a rendszer mindig AzureBackupCentralReport |
| SchemaVersion_s |Szöveg |Ebben a mezőben a séma jelenlegi verziója azt jelzi, hogy az **1-es verzió** |
| State_s |Szöveg |A tároló objektum, például az aktív, a törölt aktuális állapotát |
| OperationName |Szöveg |Ez a mező képviseli az aktuális művelet - tároló neve |
| Kategória |Szöveg |Ez a mező képviseli kategória Naplóelemzési leküldve diagnosztikai adatok, a AzureBackupReport |
| Erőforrás |Szöveg |Ez az erőforrás, amelynek gyűjtenek adatokat, azt mutatja, hogy Recovery Services-tároló neve |
| VaultUniqueId_s |Szöveg |A tároló egyedi azonosítója |
| VaultName_s |Szöveg |A tároló neve |
| AzureDataCenter_s |Szöveg |Az Adatközpont, ahol a tárolóban |
| StorageReplicationType_s |Szöveg |A tároló, például GeoRedundant tárolóreplikálást típusa |
| SourceSystem |Szöveg |A forrásrendszerben az aktuális adatok – Azure |
| ResourceId |Szöveg |Ez a mező jelöli erőforrás-azonosító, amelynek gyűjtenek adatokat, azt illusztrálja a Recovery Services-tároló erőforrás-azonosító |
| SubscriptionId |Szöveg |Ez a mező képviseli az erőforrás (RS tároló), amelynek gyűjtenek adatokat előfizetés-azonosító |
| ResourceGroup |Szöveg |Ez a mező képviseli az erőforrás (RS tároló), amelynek gyűjtenek adatokat erőforráscsoport |
| ResourceProvider |Szöveg |Ez a mező képviseli az erőforrás-szolgáltató, amelynek folyik adatgyűjtés - Microsoft.RecoveryServices |
| ResourceType |Szöveg |Ez a mező képviseli az erőforrást, amelyre folyik adatgyűjtés - tárolók típusú |

## <a name="next-steps"></a>További lépések
Tekintse át az adatokat az adatmodellbe az Azure Backup-jelentések készítéséhez, után megkezdheti [irányítópult létrehozása](../log-analytics/log-analytics-dashboards.md) a Naplóelemzési.
