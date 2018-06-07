---
title: Az Azure Backup adatmodell
description: Ez a cikk a Power bi-ban az modell adatait az Azure Backup jelentések beszél.
services: backup
author: JPallavi
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 06/26/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a17e011452f9b87c1201cea12f394a9cdd18e54b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34606222"
---
# <a name="data-model-for-azure-backup-reports"></a>Adatmodell az Azure Backup-jelentésekhez
Ez a cikk ismerteti az Azure Backup-jelentések készítéséhez használt Power BI-adatmodell. Az adatmodell használ, meglévő jelentéseket a megfelelő mezőket alapján szűrheti, és több tábla és a modell mező fontosabb, létrehozhatja a saját jelentéseit. 

## <a name="creating-new-reports-in-power-bi"></a>Új jelentések létrehozását a Power bi-ban
A Power BI használatával, amely is testreszabási funkciókat is biztosítanak [az adatmodell használatával jelentéseket készíthet](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/).

## <a name="using-azure-backup-data-model"></a>Azure biztonsági mentési adatok modell segítségével
Az adatmodell részeként a következő mezők használatával jelentéseket készíthet, és a meglévő jelentések testreszabása.

### <a name="alert"></a>Riasztás
Ez a táblázat alapvető mezők és az aggregációhoz biztosítanak riasztási kapcsolódó mezőket.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| #AlertsCreatedInPeriod |Egész szám |Kiválasztott időszak létrehozott riasztások számát |
| % ActiveAlertsCreatedInPeriod |Százalék |Aktív riasztások a kiválasztott időszak aránya |
| % CriticalAlertsCreatedInPeriod |Százalék |A kijelölt időszakra vonatkozó kritikus riasztás százalékos aránya |
| AlertOccurenceDate |Dátum |Riasztás létrehozásának dátuma |
| AlertSeverity |Szöveg |Például: kritikus riasztás súlyossága |
| AlertStatus |Szöveg |A riasztás például aktív állapota |
| AlertType |Szöveg |A generált riasztások, például a biztonsági mentés típusa |
| AlertUniqueId |Szöveg |A generált riasztások egyedi azonosítója |
| AsOnDateTime |Dátum/idő |A kijelölt sor a legutóbbi frissítési időpont |
| AvgResolutionTimeInMinsForAlertsCreatedInPeriod |Egész szám |Átlagos ideje (percben) a kijelölt időszakra vonatkozóan a riasztás feloldásához |
| EntityState |Szöveg |Aktív, a törölt például riasztási objektum aktuális állapota |

### <a name="backup-item"></a>Biztonsági mentési elem
Ez a táblázat különböző biztonsági mentési elem kapcsolatos mezők alapvető mezők és az aggregációhoz biztosítanak.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| #BackupItems |Egész szám |Biztonsági mentési elemek száma. |
| #UnprotectedBackupItems |Egész szám |A védelem leállt, vagy biztonsági mentések, de a biztonsági mentések nem indult el a beállított biztonsági mentési elemek száma.|
| AsOnDateTime |Dátum/idő |A kijelölt sor a legutóbbi frissítési időpont |
| BackupItemFriendlyName |Szöveg |Biztonsági mentési elem rövid neve |
| BackupItemId |Szöveg |Biztonsági mentési elem azonosítója |
| BackupItemName |Szöveg |Biztonsági mentési elem neve |
| BackupItemType |Szöveg |Biztonsági mentési elem például VM FileFolder típusa |
| EntityState |Szöveg |A biztonsági mentési elem objektum aktív, a törölt például aktuális állapota |
| LastBackupDateTime |Dátum/idő |Utolsó biztonsági mentés idején a kijelölt elem biztonsági mentése |
| LastBackupState |Szöveg |A kijelölt elem biztonsági mentése sikeres, sikertelen például utolsó biztonsági mentés állapota |
| LastSuccessfulBackupDateTime |Dátum/idő |Utolsó sikeres biztonsági másolat készítésének idején a kijelölt elem biztonsági mentése |
| ProtectionState |Szöveg |Védett, ProtectionStopped például a biztonsági mentési elem aktuális védelmi állapot |

### <a name="calendar"></a>Naptár
Ez a táblázat ismerteti a naptár kapcsolatos mezők.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| Dátum |Dátum |Az adatok szűrése kiválasztott dátum |
| DateKey |Szöveg |Minden dátum elem egyedi kulcsa |
| DayDiff |Egész szám |Például a nap-szűrési adatok különbség, 0 azt jelzi, az aktuális napra vonatkozó adatokat, -1 érték azt jelzi, előző egy nap adatait, 0 és a -1 jelzik adatok jelenlegi és korábbi nap  |
| Hónap |Szöveg |Hónap a kijelölt adatok szűréséhez év, hónap első napján kezdődik, és 31 napos vége |
| MonthDate | Dátum |A dátum a hónap, amikor a hónap ér véget, a kiválasztott adatok szűrése |
| MonthDiff |Egész szám |Például a szűrési adatok hónap különbség, 0 jelzi az aktuális hónap adatait, -1 érték azt jelzi, az előző hónap adatok, 0 és a -1 jelzik adatok jelenlegi és előző hónap |
| Hét |Szöveg |A hét kijelölt adatok, szűrési hét kezdőnapja vasárnap és szombat vége |
| WeekDate |Dátum |Dátum a hét, amikor a hét ér véget, a kiválasztott adatok szűrése |
| WeekDiff |Egész szám |Például a szűrési adatok hét különbség, 0 azt jelzi, aktuális hét adatok, -1 érték azt jelzi, előző hét adatok, 0 és a -1 jelzik adatok aktuális és az előző hét |
| Év |Szöveg |Kijelölt adatok szűréséhez naptári év |
| YearDate |Dátum |A dátum az év, amikor év ér véget, a kiválasztott adatok szűrése |

### <a name="job"></a>Feladat
Ez a táblázat alapvető mezők és az aggregációhoz biztosítanak feladathoz kapcsolódó mezőket.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| #JobsCreatedInPeriod |Egész szám |A kijelölt időszakban létrehozott feladatok száma |
| % FailuresForJobsCreatedInPeriod |Százalék |Százalék a kijelölt időszakban a teljes feladat-hibák |
| 80thPercentileDataTransferredInMBForBackupJobsCreatedInPeriod |Egész szám |MB az átvitt adatok 80 PERCENTILIS **biztonsági mentési** a kijelölt időszakban létrehozott feladatok |
| AsOnDateTime |Dátum/idő |A kijelölt sor a legutóbbi frissítési időpont |
| AvgBackupDurationInMinsForJobsCreatedInPeriod |Egész szám |Átlagos idő (percben) a **befejezett biztonsági mentés** kijelölt időszakban létrehozott feladatok |
| AvgRestoreDurationInMinsForJobsCreatedInPeriod |Egész szám |Átlagos idő (percben) a **visszaállítás befejeződött** kijelölt időszakban létrehozott feladatok |
| BackupStorageDestination |Szöveg |Biztonsági másolatok tárolásának például felhő, lemez és  |
| EntityState |Szöveg |A feladat objektum aktív, a törölt például aktuális állapota |
| JobFailureCode |Szöveg |Hiba a kód karakterlánc miatt, amelyet feladat hiba történt |
| JobOperation |Szöveg |A művelet, amelynek feladat futtatásakor például biztonsági mentése, visszaállítása, Backup konfigurálása |
| JobStartDate |Dátum |Feladat elindításának dátuma |
| JobStartTime |Time |Amikor a feladat elindítása fut |
| Feladat állapota |Szöveg |Például, befejezett, sikertelen végzett feladat állapota |
| JobUniqueId |Szöveg |A feladat azonosításához egyedi azonosítója |

### <a name="policy"></a>Szabályzat
Ez a táblázat alapvető mezők és az aggregációhoz biztosítanak-házirendekkel kapcsolatos mezőket.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| #Policies |Egész szám |A rendszer a meglévő biztonsági mentési házirendek száma |
| #PoliciesInUse |Egész szám |A biztonsági mentések beállítása jelenleg használt házirendek száma |
| AsOnDateTime |Dátum/idő |A kijelölt sor a legutóbbi frissítési időpont |
| BackupDaysOfTheWeek |Szöveg |Ha biztonsági mentések ütemezett napjai |
| BackupFrequency |Szöveg |A gyakoriság, amellyel biztonsági mentések futnak, például, naponta, hetente |
| BackupTimes |Szöveg |Dátum és idő, amikor a biztonsági mentés van ütemezve |
| DailyRetentionDuration |Egész szám |Teljes megőrzés időtartama napokban megadva beállított biztonsági mentés |
| DailyRetentionTimes |Szöveg |Dátum és idő, amikor napi megőrzési konfigurálása |
| EntityState |Szöveg |Aktív, a törölt például a csoportházirend-objektum aktuális állapota |
| MonthlyRetentionDaysOfTheMonth |Szöveg |A hónap havi megőrzési a kijelölt dátumok |
| MonthlyRetentionDaysOfTheWeek |Szöveg |Havi megőrzési napokat a hét kijelölt |
| MonthlyRetentionDuration |Egész szám |A konfigurált biztonsági mentésekhez hónapban teljes megőrzési időtartama |
| MonthlyRetentionFormat |Szöveg |Írja be a havi megőrzési konfiguráció például naponta meghatározásával, hetente alapján hét nap |
| MonthlyRetentionTimes |Szöveg |Dátum és idő, amikor a havi megőrzési van konfigurálva |
| MonthlyRetentionWeeksOfTheMonth |Szöveg |Adott hónap havi megőrzési esetén például konfigurált First, Last stb. |
| Házirendnév |Szöveg |A megadott házirend neve |
| PolicyUniqueId |Szöveg |A házirend-egyedi azonosítója |
| RetentionType |Szöveg |Írja be a megőrzési házirend például, naponta, hetente, havonta, éves ütemezéshez |
| WeeklyRetentionDaysOfTheWeek |Szöveg |Heti megőrzési napokat a hét kijelölt |
| WeeklyRetentionDuration |Egész szám |A konfigurált biztonsági mentésekhez hét teljes heti megőrzési időtartama |
| WeeklyRetentionTimes |Szöveg |Dátum és idő, ha a heti megőrzési van konfigurálva |
| YearlyRetentionDaysOfTheMonth |Szöveg |A hónap éves megőrzési a kijelölt dátumok |
| YearlyRetentionDaysOfTheWeek |Szöveg |Éves megőrzési napokat a hét kijelölt |
| YearlyRetentionDuration |Egész szám |A konfigurált biztonsági mentésekhez évben teljes megőrzési időtartama |
| YearlyRetentionFormat |Szöveg |Írja be az éves megőrzési konfiguráció például naponta meghatározásával, hetente alapján hét nap |
| YearlyRetentionMonthsOfTheYear |Szöveg |Az év hónapja kiválasztott éves megőrzési |
| YearlyRetentionTimes |Szöveg |Dátum és idő, amikor éves megőrzési van konfigurálva |
| YearlyRetentionWeeksOfTheMonth |Szöveg |Hét a hónapon belül éves megőrzési esetén például konfigurált First, Last stb. |

### <a name="protected-server"></a>Védett kiszolgáló
Ez a táblázat különböző védett kiszolgálóval kapcsolatos mezők alapvető mezők és az aggregációhoz biztosítanak.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| #ProtectedServers |Egész szám |Védett kiszolgálók száma |
| AsOnDateTime |Dátum/idő |A kijelölt sor a legutóbbi frissítési időpont |
| AzureBackupAgentOSType |Szöveg |Az Azure Backup szolgáltatás ügynöke operációsrendszer-típus |
| AzureBackupAgentOSVersion |Szöveg |Az Azure Backup szolgáltatás ügynöke az operációs rendszer verziója |
| AzureBackupAgentUpdateDate |Szöveg |Ügynök Backup szolgáltatás ügynökének frissítésének dátuma |
| AzureBackupAgentVersion |Szöveg |Biztonsági másolat verzióját ügynök verziószáma |
| BackupManagementType |Szöveg |A biztonsági mentés például IaaSVM FileFolder szolgáltató típusa |
| EntityState |Szöveg |Aktív, a törölt például a védett kiszolgáló objektum aktuális állapota |
| ProtectedServerFriendlyName |Szöveg |Védett kiszolgáló rövid neve |
| ProtectedServerName |Szöveg |Védett kiszolgáló neve |
| ProtectedServerType |Szöveg |A védett kiszolgáló típusa biztonsági mentése például IaaSVMContainer |
| ProtectedServerName |Szöveg |Mely biztonsági mentési elemet a védett kiszolgáló neve tartozik |
| RegisteredContainerId |Szöveg |A biztonsági mentéshez regisztrált tároló azonosító |

### <a name="storage"></a>Storage
Ez a táblázat alapvető mezők és az aggregációhoz biztosítanak tárolással kapcsolatos mezőket.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| #ProtectedInstances |Egész szám |A kijelölt időszak előtér tárolási számlázási, számított alapján legújabb értékének kiszámítására használt védett példányok száma |
| AsOnDateTime |Dátum/idő |A kijelölt sor a legutóbbi frissítési időpont |
| CloudStorageInMB |Egész szám |A kijelölt időszak legújabb érték alapján felhő biztonsági mentések, számított használt biztonsági mentési tároló |
| EntityState |Szöveg |Aktív, a törölt például az objektum aktuális állapota |
| LastUpdatedDate |Dátum |Ha a kijelölt sor utolsó módosításának dátuma |

### <a name="time"></a>Time
Ez a táblázat idővel kapcsolatos mezők ismerteti.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| Óra |Time |Például 1:00:00 PM óra |
| HourNumber |Egész szám |A nap például 13,00 óra száma |
| Perc |Egész szám |Az óra perc |
| PeriodOfTheDay |Szöveg |Period időszelet például a 12-3 de nap |
| Time |Time |Például 12:00:01 de időpontja |
| TimeKey |Szöveg |Azt az időt jelentik a kulcs értéke |

### <a name="vault"></a>Tároló
Ez a táblázat különböző tárolóval kapcsolatos mezők alapvető mezők és az aggregációhoz biztosítanak.

| Mező | Adattípus | Leírás |
| --- | --- | --- |
| #Vaults |Egész szám |Tárolók száma |
| AsOnDateTime |Dátum/idő |A kijelölt sor a legutóbbi frissítési időpont |
| AzureDataCenter |Szöveg |Az Adatközpont, ahol a tárolóban |
| EntityState |Szöveg |Aktív, a törölt például a tároló objektum aktuális állapota |
| StorageReplicationType |Szöveg |A tároló például GeoRedundant tárolóreplikálást típusa |
| SubscriptionId |Szöveg |Az ügyfél-jelentések létrehozása a kijelölt előfizetés-azonosító |
| VaultName |Szöveg |A tároló neve |
| VaultTags |Szöveg |A tárolóhoz társított címkék |

## <a name="next-steps"></a>További lépések
Után tekintse át az adatokat az adatmodellbe az Azure Backup-jelentések készítéséhez, létrehozásával és jelentések megtekintése a Power BI kapcsolatos további részletekért tekintse meg a következő cikkekben talál.

* [Jelentések létrehozása a Power bi-ban](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
* [Szűrés a jelentéseket a Power bi-ban](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
