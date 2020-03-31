---
title: Adatmodell az Azure Backup diagnosztikai eseményeihez
description: Ez az adatmodell a diagnosztikai események Log Analytics (LA) számára történő küldésének erőforrás-specifikus módjára hivatkozik.
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 267753ee1647739e36d92b64f50d8a8be87537d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77583384"
---
# <a name="data-model-for-azure-backup-diagnostics-events"></a>Adatmodell az Azure biztonsági mentésdiagnosztikai eseményeihez

## <a name="coreazurebackup"></a>CoreAzureBackup

Ez a táblázat az alapvető biztonsági mentési entitásokról, például a tárolókról és a biztonsági mentési elemekről tartalmaz információkat.

| **Mező**                         | **Adattípus** | **Leírás**                                              |
| --------------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                        | Szöveg          | Az összegyűjtött adatok erőforrás-azonosítója. Például a Recovery Services tároló erőforrás-azonosítója. |
| OperationName                     | Szöveg          | Ez a mező az aktuális művelet nevét jelöli - BackupItem, BackupItemAssociation vagy ProtectedContainer. |
| Kategória                          | Szöveg          | Ez a mező az Azure Monitor-naplókba leadott diagnosztikai adatok kategóriáját jelöli. Például CoreAzureBackup. |
| Ügynökverzió                      | Szöveg          | Az ügynök biztonsági másolatának vagy a védelmi ügynöknek a verziószáma (SC DPM és MABS esetén) |
| AzureBackupAgentVersion           | Szöveg          | Az Azure biztonsági másolat ügynökének verziója a biztonsági másolat kezelőkiszolgálóján |
| AzureDataCenter                   | Szöveg          | Adatközpont, ahol a tároló található                       |
| BackupItemAppVersion              | Szöveg          | A biztonsági másolat elem alkalmazásverziója                       |
| BackupItemFriendlyName            | Szöveg          | A biztonsági másolat elemének rövid neve                             |
| BackupItemName (Biztonsági elemneve)                    | Szöveg          | A biztonsági másolat elemének neve                                      |
| BackupItemProtectionState         | Szöveg          | A biztonsági másolat elem védelmi állapota                          |
| BackupItemFrontEndSize            | Szöveg          | A biztonsági másolat elemének előtér-mérete                            |
| BackupItemType (Biztonsági elemtípusa)                    | Szöveg          | A biztonsági másolat elemének típusa. Például: VM, FileFolder             |
| BackupItemUniqueId                | Szöveg          | A biztonsági másolat elemének egyedi azonosítója                         |
| BackupManagementServerType        | Szöveg          | A biztonságimásolat-kezelő kiszolgáló típusa, mint a MABS, SC DPM     |
| BackupManagementServerUniqueId    | Szöveg          | A Biztonságimásolat-kezelő kiszolgáló egyedi azonosítására szolgáló mező      |
| BackupManagementType (Biztonsági másolat szolgáltatástípusa)              | Szöveg          | Szolgáltató típusa a biztonsági mentést végző kiszolgálóhoz. Például: IaaSVM, FileFolder |
| BackupManagementServerName        | Szöveg          | A biztonságimásolat-kezelő kiszolgáló neve                         |
| BackupManagementServerOSVersion   | Szöveg          | A Biztonságimásolat-kezelő kiszolgáló operációs rendszerének verziója                   |
| BackupManagementServerVersion     | Szöveg          | A biztonságimásolat-kezelő kiszolgáló verziója                      |
| LegújabbRecoveryPointLocation       | Szöveg          | A biztonsági másolat elemének legutóbbi helyreállítási pontjának helye    |
| LegújabbRecoveryPointTime           | DateTime      | A biztonsági másolat elemutolsó helyreállítási pontjának dátuma   |
| LegrégebbiRecoveryPointLocation       | Szöveg          | A biztonsági másolat elem legrégebbi helyreállítási pontjának helye    |
| LegrégebbiRecoveryPointTime           | DateTime      | A biztonsági másolat elemutolsó helyreállítási pontjának dátuma   |
| PolicyUniqueId                    | Szöveg          | Egyedi azonosító a házirend azonosításához                             |
| Védetttároló-barátnév    | Szöveg          | A védett kiszolgáló rövid neve                        |
| Védett containerlocation        | Szöveg          | Azt jelzi, hogy a védett tároló a helyszínen vagy az Azure-ban található-e |
| Védett tárolónév            | Szöveg          | A védett tároló neve                            |
| VédettContainerOSType          | Szöveg          | A védett tároló operációs rendszer ének típusa                          |
| VédettContainerOSVersion       | Szöveg          | A védett tároló operációs rendszerének verziója                        |
| VédettContainerProtectionState | Szöveg          | A védett tároló védelmi állapota                  |
| Védett containertype            | Szöveg          | Azt jelzi, hogy a védett tároló kiszolgáló vagy tároló  |
| Védett ContainerUniqueId        | Szöveg          | Egyedi azonosító, amely a védett tároló azonosítására szolgál a DPM, MABS használatával biztonsági másolatot tartalmazó virtuális gépek kivételével |
| VédettContainerWorkloadtype    | Szöveg          | Biztonsági másolatot kell a védett tároló típusa. Például az IaaSVMContainer |
| ProtectionGroupName               | Szöveg          | Annak a védelmi csoportnak a neve, amelyben a biztonsági másolat elem védett, az SC DPM és a MABS esetében, ha van ilyen. |
| ResourceGroupName                 | Szöveg          | Az erőforrás erőforráscsoportja (például helyreállítási szolgáltatások tárolója) az összegyűjtött adatokhoz |
| Sémaverzió                     | Szöveg          | Ez a mező a séma aktuális verzióját jelöli, **V2** |
| SecondaryBackupProtectionState    | Szöveg          | Azt jelzi, hogy engedélyezve van-e a biztonsági másolat elemének másodlagos védelme  |
| Állapot                             | Szöveg          | A biztonsági másolat elemobjektumának állapota. Például, Aktív, Törölve |
| StorageReplicationType            | Szöveg          | A tárolótároló replikációjának típusa. Például: GeoRedundant |
| SubscriptionId                    | Szöveg          | Annak az erőforrásnak (például helyreállítási szolgáltatások tárolójának) előfizetési azonosítója, amelyhez adatokat gyűjtenek |
| Tárolóneve                         | Szöveg          | A tároló neve                                            |
| VaultCímkék                         | Szöveg          | A tároló erőforrásához társított címkék                    |
| VaultUniqueId                     | Szöveg          | A tároló egyedi azonosítója                             |
| SourceSystem                      | Szöveg          | Az aktuális adatok forrásrendszere - Azure                  |

## <a name="addonazurebackupalerts"></a>AddonAzureBackupAlerts

Ez a tábla a riasztásokkal kapcsolatos mezők részleteit tartalmazza.

| **Mező**                      | **Adattípus** | **Leírás**                                              |
| :----------------------------- | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Szöveg          | Annak az erőforrásnak az egyedi azonosítója, amelyről az adatok gyűjtése történik. Például egy Recovery Services-tároló erőforrásazonosítója |
| OperationName                  | Szöveg          | Az aktuális művelet neve. Például, Riasztás            |
| Kategória                       | Szöveg          | Az Azure Monitor-naplókba leadott diagnosztikai adatok kategóriája – AddonAzureBackupAlerts |
| AlertCode (Riasztáskód)                      | Szöveg          | Riasztástípus egyedi azonosítására szolgáló kód                     |
| AlertConsolidationStatus       | Szöveg          | Annak megállapítása, hogy a riasztás összevont riasztás-e vagy sem         |
| AlertOccurrenceDateTime        | DateTime      | A riasztás létrehozásának dátuma és időpontja                     |
| AlertRaisedOn                  | Szöveg          | Az entitás típusa, amelyen a riasztás megjelenik                        |
| Riasztássúlyossága                  | Szöveg          | A riasztás súlyossága. Például a kritikus                 |
| AlertStatus (Riasztási állapot)                    | Szöveg          | A riasztás állapota. Például az Aktív                     |
| AlertTimetoResolveInPerc    | Szám        | A riasztás feloldásához szükséges idő. Üres az aktív riasztásokhoz.     |
| Riasztástípusa                      | Szöveg          | A riasztás típusa. Például a Biztonsági mentés                           |
| AlertUniqueId azonosító                  | Szöveg          | A létrehozott riasztás egyedi azonosítója                    |
| BackupItemUniqueId             | Szöveg          | A riasztáshoz társított biztonsági másolat elem egyedi azonosítója |
| BackupManagementServerUniqueId | Szöveg          | A biztonsági másolat kezelőkiszolgáló egyedi azonosítására szolgáló mező, amelyen keresztül a biztonsági másolat készítő elem védett, adott esetben |
| BackupManagementType (Biztonsági másolat szolgáltatástípusa)           | Szöveg          | Szolgáltató típusa a biztonsági mentési feladatot végzett kiszolgálóhoz, például IaaSVM, FileFolder |
| CountOfAlertsKonszolidált      | Szám        | Összesített riasztások száma, ha összevont riasztásról van szó  |
| Védett ContainerUniqueId     | Szöveg          | A riasztáshoz társított védett kiszolgáló egyedi azonosítója |
| Ajánlott művelet              | Szöveg          | A riasztás feloldásához ajánlott művelet                      |
| Sémaverzió                  | Szöveg          | A séma jelenlegi verziója, például **V2**            |
| Állapot                          | Szöveg          | A riasztási objektum aktuális állapota, például Aktív, Törölve |
| StorageUniqueId                | Szöveg          | A tárolási entitás azonosítására használt egyedi azonosító                |
| VaultUniqueId                  | Szöveg          | A riasztáshoz kapcsolódó tároló azonosítására használt egyedi azonosító    |
| SourceSystem                   | Szöveg          | Az aktuális adatok forrásrendszere - Azure                    |

## <a name="addonazurebackupprotectedinstance"></a>AddonAzureBackupProtectedInstance

Ez a tábla alapvető védett példányokkal kapcsolatos mezőket tartalmaz.

| **Mező**                      | **Adattípus** | **Leírás**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Szöveg          | Annak az erőforrásnak az egyedi azonosítója, amelyről az adatok gyűjtése történik. Például egy Recovery Services-tároló erőforrásazonosítója |
| OperationName                  | Szöveg          | A művelet neve, például Védett példány         |
| Kategória                       | Szöveg          | Az Azure Monitor-naplókba leadott diagnosztikai adatok kategóriája – AddonAzureBackupProtectedInstance |
| BackupItemUniqueId             | Szöveg          | A biztonsági másolat elemének egyedi azonosítója                                 |
| BackupManagementServerUniqueId | Szöveg          | A biztonsági másolat kezelőkiszolgáló egyedi azonosítására szolgáló mező, amelyen keresztül a biztonsági másolat készítő elem védett, adott esetben |
| BackupManagementType (Biztonsági másolat szolgáltatástípusa)           | Szöveg          | Szolgáltató típusa a biztonsági mentési feladatot végzett kiszolgálóhoz, például IaaSVM, FileFolder |
| Védett ContainerUniqueId     | Szöveg          | Egyedi azonosító a feladat által futtatott védett tároló azonosítására |
| Védett instancecount         | Szöveg          | A társított biztonsági másolat elemének vagy védett tárolójának védett példányainak száma az adott dátum-időpontban |
| Sémaverzió                  | Szöveg          | A séma jelenlegi verziója, például **V2**            |
| Állapot                          | Szöveg          | A biztonsági másolat elemobjektumának állapota, például Aktív, Törölve |
| VaultUniqueId                  | Szöveg          | A védett példányhoz társított védett tároló egyedi azonosítója |
| SourceSystem                   | Szöveg          | Az aktuális adatok forrásrendszere - Azure                    |

## <a name="addonazurebackupjobs"></a>AddonAzureBackupJobs

Ez a tábla a feladattal kapcsolatos mezők részleteit tartalmazza.

| **Mező**                      | **Adattípus** | **Leírás**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Szöveg          | Az összegyűjtött adatok erőforrás-azonosítója. Például a Recovery Services tároló erőforrásazonosítója |
| OperationName                  | Szöveg          | Ez a mező az aktuális művelet nevét jelöli - Projekt    |
| Kategória                       | Szöveg          | Ez a mező az Azure Monitor-naplókba leadott diagnosztikai adatok kategóriáját jelöli - AddonAzureBackupJobs |
| AdhocOrScheduledJob            | Szöveg          | Mező annak megadásához, hogy a feladat ad hoc vagy ütemezett-e           |
| BackupItemUniqueId             | Szöveg          | A tárolóentitáshoz kapcsolódó biztonsági másolat elemének azonosítására használt egyedi azonosító |
| BackupManagementServerUniqueId | Szöveg          | A tárolóentitáshoz kapcsolódó biztonságimentési felügyeleti kiszolgáló azonosítására használt egyedi azonosító |
| BackupManagementType (Biztonsági másolat szolgáltatástípusa)           | Szöveg          | Szolgáltató típusa a biztonsági mentés végrehajtásához, például IaaSVM, FileFolder, amelyhez ez a riasztás tartozik |
| DataTransferredInMB            | Szám        | Mb-ban továbbított adatok ehhez a feladathoz                          |
| JobDurationInSecs              | Szám        | A feladat teljes időtartama másodpercben                                |
| Feladathibakód                 | Szöveg          | Hibakód-karakterlánc, amely miatt hiba történt    |
| Feladatüzemeltetés                   | Szöveg          | Az a művelet, amelyhez a feladat fut, például Biztonsági mentés, Visszaállítás, Biztonsági másolat konfigurálása |
| JobOperationSubType típus            | Szöveg          | A feladatművelet altípusa. Például a "Log", a Napló biztonsági mentési feladat esetén |
| JobStartDateTime               | DateTime      | A feladat futásának dátuma és időpontja                       |
| Feladat állapota                      | Szöveg          | A befejezett feladat állapota, például Befejezett, Sikertelen   |
| Feladat-uniqueid                    | Szöveg          | Egyedi azonosító a feladat azonosításához                                |
| Védett ContainerUniqueId     | Szöveg          | A riasztáshoz társított védett kiszolgáló egyedi azonosítója |
| RecoveryJobDestination         | Szöveg          | A helyreállítási feladat célja, ahol az adatok helyreállnak   |
| RecoveryJobRPDateTime          | DateTime      | A helyreállítás időpontja, amelynek helyreállítása folyamatban van |
| RecoveryJobLocation            | Szöveg          | Az a hely, ahol a helyreállító pontot tárolják |
| RecoveryLocationType           | Szöveg          | A helyreállítási hely típusa                                |
| Sémaverzió                  | Szöveg          | A séma jelenlegi verziója, például **V2**            |
| Állapot                          | Szöveg          | A riasztási objektum aktuális állapota, például Aktív, Törölve |
| VaultUniqueId                  | Szöveg          | A riasztáshoz társított védett tároló egyedi azonosítója |
| SourceSystem                   | Szöveg          | Az aktuális adatok forrásrendszere - Azure                    |

## <a name="addonazurebackuppolicy"></a>AddonAzureBackupPolicy

Ez a tábla a házirenddel kapcsolatos mezők részleteit tartalmazza.

| **Mező**                       | **Adattípus**  | **Leírás**                                              |
| ------------------------------- | -------------- | ------------------------------------------------------------ |
| ResourceId                      | Szöveg           | Annak az erőforrásnak az egyedi azonosítója, amelyről az adatok gyűjtése történik. Például egy Recovery Services-tároló erőforrásazonosítója |
| OperationName                   | Szöveg           | A művelet neve, például Házirend vagy PolicyAssociation |
| Kategória                        | Szöveg           | Az Azure Monitor-naplókba leadott diagnosztikai adatok kategóriája – AddonAzureBackupPolicy |
| BackupDaysOfTheWeek             | Szöveg           | A hét azon napjai, amikor a biztonsági mentések ütemezve vannak            |
| BackupFrequency (Biztonsági másolat gyakorisága)                 | Szöveg           | A biztonsági mentések futtatásának gyakorisága. Például, napi, heti |
| BackupManagementType (Biztonsági másolat szolgáltatástípusa)            | Szöveg           | Szolgáltató típusa a biztonsági mentést végző kiszolgálóhoz. Például: IaaSVM, FileFolder |
| BackupManagementServerUniqueId  | Szöveg           | A biztonsági másolat kezelőkiszolgáló egyedi azonosítására szolgáló mező, amelyen keresztül a biztonsági másolat készítő elem védett, adott esetben |
| BackupTimes (Biztonsági mentési idők)                     | Szöveg           | A biztonsági mentések ütemezésének dátuma és időpontja                     |
| DailyRetentionDuration          | Egész szám   | A konfigurált biztonsági mentések teljes megőrzési időtartama napokban      |
| DailyRetentionTimes             | Szöveg           | A napi adatmegőrzés konfigurálásának dátuma és időpontja            |
| DiffBackupdaysOfTheWeek         | Szöveg           | A hét napjai az SQL különbözeti biztonsági mentéseiszámára az Azure virtuális gép biztonsági mentésében |
| DiffBackupFormátum                | Szöveg           | Az SQL különbözeti biztonsági másolatainak formátuma az Azure virtuális gép biztonsági mentésében   |
| DiffBackupRetentionDuration     | Tizedes tört | Az SQL különbözeti biztonsági másolatainak megőrzési időtartama az Azure virtuális gép biztonsági mentésében |
| DiffBackupTime                  | Time           | Az SQL különbözeti biztonsági mentéseinek ideje az Azure virtuális gép biztonsági mentésében     |
| LogBackupgyakoriság              | Tizedes tört | Az SQL naplóbiztonsági mentésének gyakorisága                            |
| LogBackupRetentionDuration      | Tizedes tört | Az SQL-biztonsági mentés naplózási biztonsági másolatainak megőrzési időtartama az Azure virtuális gép biztonsági mentésében |
| Havi megőrzési napokAhónap  | Szöveg           | A havi megőrzés konfigurálásának hónapja.  Például: Első, Utolsó stb. |
| MonthlyRetentiondaysOfTheWeek (A hét havi megőrzése)   | Szöveg           | A havi megőrzésre kiválasztott hét napjai              |
| Havimegőrzési időtartam        | Szöveg           | A konfigurált biztonsági mentések teljes megőrzési időtartama hónapokban    |
| MonthlyRetentionFormat          | Szöveg           | A havi megőrzés konfigurációjának típusa. Például, napi napi alapú, heti heti alapú |
| HavimegőrzésI idők           | Szöveg           | A havi adatmegőrzés konfigurálásának dátuma és időpontja           |
| Havi megőrzési hétahónap | Szöveg           | A havi megőrzés konfigurálásának hónapja.   Például: Első, Utolsó stb. |
| PolicyName                      | Szöveg           | A megadott házirend neve                                   |
| PolicyUniqueId                  | Szöveg           | Egyedi azonosító a házirend azonosításához                             |
| PolicyTimeZone (Házirend-időzóna)                  | Szöveg           | Az az időzóna, amelyben a házirend-időmezők meg vannak adva a naplókban |
| RetentionDuration (Megőrzési időtartam)               | Szöveg           | A konfigurált biztonsági mentések megőrzési időtartama                    |
| RetentionType (Megtontípus)                   | Szöveg           | Megőrzés típusa                                            |
| Sémaverzió                   | Szöveg           | Ez a mező a séma aktuális verzióját jelöli, **v2-es** |
| Állapot                           | Szöveg           | A házirendobjektum jelenlegi állapota. Például, Aktív, Törölve |
| Szinkronizálásgyakoriság-pernap  | Egész szám   | Az SC DPM és mabs fájlbiztonsági mentések szinkronizálásának száma a nap folyamán |
| VaultUniqueId                   | Szöveg           | Annak a tárolónak az egyedi azonosítója, amelyhez ez a házirend tartozik          |
| WeeklyRetentiondaysOfTheWeek    | Szöveg           | A heti megőrzésre kiválasztott hét napjai               |
| HetiMegőrzésI időtartam         | Tizedes tört | A beállított biztonsági mentések heti megőrzési időtartama hetekben |
| HetiMegőrzésI idők            | Szöveg           | A heti adatmegőrzés konfigurálásának dátuma és időpontja            |
| YearlyRetentionDaysOfTheMonth   | Szöveg           | Az éves adatmegőrzéshez kiválasztott hónap dátumai             |
| YearlyRetentionDaysOfTheWeek    | Szöveg           | Az éves adatmegőrzéshez kiválasztott hét napjai               |
| Évesmegőrzésidőtartam         | Tizedes tört | A konfigurált biztonsági mentések teljes megőrzési időtartama években     |
| Éves megőrzési formátum           | Szöveg           | Az éves adatmegőrzéskonfiguráció típusa, például napi napi, heti heti heti heti |
| YearlyRetentionMonthsOfTheYear  | Szöveg           | Az éves megőrzésre kiválasztott év hónapjai             |
| Éves megőrzési idő            | Szöveg           | Az éves adatmegőrzés konfigurálásának dátuma és időpontja            |
| YearlyRetentionWeeksOfTheMonth  | Szöveg           | Az éves adatmegőrzésre kiválasztott hónap hetei             |
| SourceSystem                    | Szöveg           | Az aktuális adatok forrásrendszere - Azure                    |

## <a name="addonazurebackupstorage"></a>AddonAzureBackupTárhely

Ez a tábla a tárolással kapcsolatos mezők részleteit tartalmazza.

| **Mező**                      | **Adattípus** | **Leírás**                                              |
| ------------------------------ | ------------- | ------------------------------------------------------------ |
| ResourceId                     | Szöveg          | Az összegyűjtött adatok erőforrás-azonosítója. Például a Recovery Services tároló erőforrásazonosítója |
| OperationName                  | Szöveg          | Ez a mező az aktuális művelet nevét jelöli - Storage vagy StorageAssociation |
| Kategória                       | Szöveg          | Ez a mező az Azure Monitor-naplókba leadott diagnosztikai adatok kategóriáját jelöli - AddonAzureBackupStorage |
| BackupItemUniqueId             | Szöveg          | Egyedi azonosító a DPM, MABS használatával biztonsági másolat készítésével biztonsági másolat készítő elem azonosítására |
| BackupManagementServerUniqueId | Szöveg          | A biztonsági másolat kezelőkiszolgáló egyedi azonosítására szolgáló mező, amelyen keresztül a biztonsági másolat készítő elem védett, adott esetben |
| BackupManagementType (Biztonsági másolat szolgáltatástípusa)           | Szöveg          | Szolgáltató típusa a biztonsági mentést végző kiszolgálóhoz. Például: IaaSVM, FileFolder |
| PreferredWorkloadOnVolume      | Szöveg          | Számítási feladatok, amelyeknél ez a kötet az előnyben részesített tároló      |
| Védett ContainerUniqueId     | Szöveg          | A riasztáshoz társított védett kiszolgáló egyedi azonosítója |
| Sémaverzió                  | Szöveg          | A séma verziója. **Például: V2**                   |
| Állapot                          | Szöveg          | A biztonsági másolat elemobjektumának állapota. Például, Aktív, Törölve |
| StorageAllocatedInMB-k          | Szám        | A megfelelő biztonságimásolat-elem által a Lemez típusú megfelelő tárolóban lefoglalt tárterület mérete |
| StorageConsumedInMBs           | Szám        | A megfelelő biztonsági másolat elem által a megfelelő tárolóban felhasznált tárhely mérete |
| StorageName (Tárolóneve)                    | Szöveg          | A tárolási entitás neve. Például, E:\                      |
| StorageTotalSizeInGB-k          | Szöveg          | A tárterület teljes mérete GB-ban, a tárolóentitás által fogyasztva     |
| StorageType típusú                    | Szöveg          | A tárhely típusa, például felhő, kötet, lemez             |
| StorageUniqueId                | Szöveg          | A tárolási entitás azonosítására használt egyedi azonosító                |
| VaultUniqueId                  | Szöveg          | A tárolóhoz kapcsolódó tároló azonosítására használt egyedi azonosító |
| Kötetbarát név             | Szöveg          | A tárolókötet rövid neve                          |
| SourceSystem                   | Szöveg          | Az aktuális adatok forrásrendszere - Azure                    |

## <a name="next-steps"></a>További lépések

- [További információ a diagnosztikai adatok loganalytics-be küldéséről](https://docs.microsoft.com/azure/backup/backup-azure-diagnostic-events)
- [További információ a lekérdezések írásáról az Erőforrás-specifikus táblákra](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor#sample-kusto-queries)